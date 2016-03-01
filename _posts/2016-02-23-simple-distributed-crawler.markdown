---
layout: post
title: 自己造轮子：一个简单的分布式网络爬虫
tags: Python
---

去年看过一个QQ空间爬虫的帖子，觉得挺有意思，一直想自己动手写一个，于是寒假期间花了点时间，用Python写了这个抓取豆瓣网用户信息的爬虫。虽说现在用Scrapy之类的爬虫库可以很方便地写出爬虫，但是自己造轮子更容易理解和掌握编写爬虫的技术。目前爬虫已实现基本功能，后期会做一些改进以提升其性能，以及对采集到的数据做一些简单的分析。代码基于Python 2.7.10实现，托管在Github: [Kr0c/DoubanRobot][1]。

### 思路

给定一个豆瓣用户ID: 130949863，打开该用户个人主页（需登录）: [http://www.douban.com/people/130949863/][2]，这个页面包含用户昵称、常居地、注册时间、读书、电影、关注、被关注等相关的信息。通过检索TA关注和被关注的用户列表，又可以获得更多的用户ID，由此可以构建一条流水线。

### 1. 模拟登录

上面提到打开用户个人主页需要登录，因此我们写一个登录模块，模仿浏览器的登录行为。经测试，前面几次可以直接登录，后面开始需要输入验证码。虽然Google开源的`pytesseract`可以用于识别验证码，但我们这个爬虫不用经常进行登录操作，因为登录时勾选`下次自动登录`可以将cookies保留很长一段时间，所以我们采用手动输入验证码的方式进行登录。

通过浏览器抓包，发现验证码登录时需发送以下表单数据：

    'form_email': 'example@email.com',
    'form_password': 'password',
    'captcha-solution': 'touch',
    'captcha-id': 'U19u798jWJYBcon6lSrYI8Lp:en',
    'remember': 'on'

这里使用requests处理网络请求，requests是一个非常强大的HTTP库，个人认为比urllib2好用。具体使用参见官方文档：[Requests: HTTP for Humans][3]

代码片段（已考虑需要验证码和不需验证码两种情况）：

	#!/usr/bin/env python
	# -*- coding: utf-8 -*-
	__author__ = 'Kr0c'
	
	payload = {
	    'form_email': 'example@email.com',
        'form_password': 'password',
        'remember': 'on'
    }
	
	def login(self):
        session = requests.session()
        session.headers.update(self.headers)

        # if needs captcha
        try:
            # get url_captcha, captcha-solution should be POST together with captcha_id
            login_page = BeautifulSoup(session.get(self.url_login).content, 'lxml')
            url_captcha = login_page.find('img', id='captcha_image')['src']
            captcha_id = url_captcha[38:65]

            # show captcha image
            img_buf = requests.get(url_captcha, stream=True, headers=self.headers).content
            Image.open(cStringIO.StringIO(img_buf)).show()
            print '[+] 验证码显示成功！'
            captcha_solution = raw_input('[+] 请输入验证码：\n>>> ')
            
            # add captcha-solution & captcha-id to Form Data
            self.payload['captcha-solution'] = captcha_solution
            self.payload['captcha-id'] = captcha_id
            
        # if no captcha
        except:
            pass

        # login and return session
        login = session.post(self.url_login, data=self.payload)
        login_code = BeautifulSoup(login.content, 'lxml').find('html')['lang']

        if login_code == 'zh-cmn-Hans':
            print '[+] 登录成功！'
            return session
            
登录成功后，可以从返回的session中获取登录后的浏览器头headers和cookies，建议将它们写入`session.txt`文件，下次登录时直接从文件读取。

### 2. 获取用户ID

- 用户关注列表URL：[http://www.douban.com/people/130949863/contacts][4]
- 用户被关注列表URL：[http://www.douban.com/people/130949863/rev_contacts][5]

从这两个页面获取与该用户相关的用户ID，由于被关注列表每页仅显示70个用户，为避免翻页，只获取第一页的ID。调用`get_user_id`模块，传入一个用户ID，返回相关用户的ID集合（Python的集合操作可以保证没有重复元素）。

    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    __author__ = 'Kr0c'

    import re
    import requests

    # ought to login or set cookies first
    # given a user_id, return it's relative user_id(type: set)
    def get_id(id_, headers, cookies, proxies=None):
        # get whole follows, but only 1st page of followers(to avoid page turning)
        url_contacts = 'http://www.douban.com/people/' + id_ + '/contacts'          # follow
        url_rev_contacts = 'http://www.douban.com/people/' + id_ + '/rev_contacts'  # follower
        pattern = re.compile(r'<dd>.*?people/(.*?)/', re.S)
        id_contacts = re.findall(pattern, requests.get(url_contacts, headers=headers, cookies=cookies, proxies=proxies).content)
        id_rev_contacts = re.findall(pattern, requests.get(url_rev_contacts, headers=headers, cookies=cookies, proxies=proxies).content)

        # combine id from contacts and rev_contacts, then return a set(user_id)
        follow = set(id_contacts)
        follower = set(id_rev_contacts)
        user_id = (follow | follower)
        print '[+] 正在获取用户社交关系...'
        print '[+] 关注:%d    粉丝: %d    朋友: %d' % (len(follow), len(follower), len(follow)+len(follower)-len(user_id))
        return user_id

### 3. 获取用户信息

有了用户ID，就可以从其个人主页获取用户信息了。获取的信息如下：

- 昵称
- 常居地
- 加入时间
- 关注数
- 粉丝数
- 读过的书（数量，下同）
- 听过的音乐
- 看过的电影
- 评论数
- 加入的小组
- 最近活跃时间


使用[BeautifulSoup][6]过滤网页中各类标签并且配合正则，便可以从网页中取出所需信息。例如：

    # 获取网页正文中class='user-info'的div标签
    soup = BeautifulSoup(webpage)
    print soup.html.body.find('div', 'user-info')
    
    # 从网页中类似'http://www.douban.com/people/130949863/'的链接获取ID
    pattern = re.compile(r'/people/(*?)/', re.S)    # *?匹配任意内容，re.S表示多行匹配
    ID = re.findall(pattern, webpage)

调用`get_user_info`模块，传入一个用户ID，返回包含该用户的信息的tuple，并且可以将信息以追加方式写入`info.txt`文件。考虑到不是每个用户都填写了以上11项信息，将未填写的项目设置为`Unknown`或`0`；某些用户账号已主动注销或者被禁，将其昵称设置为`Quit|Ban`。

代码片段：

    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    __author__ = 'Kr0c'

    # given a user_id, return user's information(type: tuple)
    def get_info(user_id, headers, cookies, proxies=None):
        url = 'http://www.douban.com/people/' + user_id
        soup = BeautifulSoup(requests.get(url, headers=headers, cookies=cookies, proxies=proxies).content, 'lxml')
        
        # nickname, city, register, book, music, movie, review, group, active, follow, follower
        try:
            city = soup.body.find(class_='user-info').a.string.encode('utf-8')
        except:
            city = 'Unknown'
        try:
            register = re.findall('<br/> (.*?)加入', soup.body.find('div', 'user-info').div.encode('utf-8'))[0]
        except:
            register = '0000-00-00'
            nickname = 'Quit|Ban'
        try:
            follow = soup.body.find(id='friend').a.string.encode('utf-8')[6:]
        except:
            follow = 0
        ...

        user_info = (user_id, nickname, city, register, follow, follower, book, music, movie, review, group, active)
        return user_info

    # write user info to file
    def write2file(path, data):
        with open(path, 'ab') as f:
            for item in data:
                f.write('%s   ' % item)
            f.write('\n')

### 4. 任务队列持久化

管理爬虫任务队列是一个关键性的问题，既要考虑去重复，又要保证未完成的任务不丢失。创建`todo`集合存放待完成的任务ID、`done`集合存放已完成的任务ID。每次从`todo`集合中`pop()`  一个ID，调用`get_user_id`和`get_user_info`模块，完成后将该ID加入`done`集合中，同时将新获取到的ID并入`todo`。及时将`todo`, `done`序列化到文件`todo.txt`, `done.txt`，下次程序启动后从文件加载，这样就可以保证程序中断后任务队列不会丢失。

### 5. 实现分布式

分布式进程可以将爬虫任务分散到多台机器上，利用多台机器的网络出口和系统资源，可以大大提升爬虫的性能。分布式网络爬虫的整体设计重点应该在于爬虫如何进行通信。目前分布式网络爬虫按通信方式不同分布式网路爬虫可以分为主从模式、自治模式与混合模式三种。这里采用的是相对简单的主从模式，即由一台主机作为控制节点负责任务管理，爬虫节点只需要从控制节点接收任务，并把新生成的任务提交给控制节点，在这个过程中不必与其他爬虫节点通信，这种方式实现简单利于管理。(*References: [分布式网络爬虫体系结构设计－小军人][7]*)

Python的`multiprocessing`模块不但支持多进程，其中`managers`子模块还支持把多进程分布到多台机器上。一个服务进程可以作为调度者，将任务分布到其他多个进程中，依靠网络通信。由于`managers`模块封装很好，不必了解网络通信的细节，就可以很容易地编写分布式多进程程序。(*References: [分布式进程－廖雪峰的官方网站][8]*)

`manager`模块负责管理`todo`, `done`和不断获取新的ID，并将`todo`中的任务分发给多个`worker`，`worker`抓取用户信息并返回`manager`保存（亦可改为不回传用户信息，直接由`worker`保存）。

部分代码：
    
    #!/usr/bin/env python
    
    ##### manager.py    
    # create task queue and result queue
    task_queue = Queue.Queue()
    result_queue = Queue.Queue()
    
    # register
    BaseManager.register('get_task_queue', callable=lambda: task_queue)
    BaseManager.register('get_result_queue', callable=lambda: result_queue)
    # bound port 5000, set authkey
    manager = BaseManager(address=('', 5000), authkey='douban')
    manager.start()
    
    task = manager.get_task_queue()
    result = manager.get_result_queue()
    id_ = todo.pop()
    print '[~] Send to task queue...'
    task.put(id_)
    print '[~] Receiving User Information...'
    data = result.get()
    
    ##### worker.py   
    # connect to manager
    BaseManager.register('get_task_queue')
    BaseManager.register('get_result_queue')
    server_addr = '127.0.0.1'
    worker = BaseManager(address=(server_addr, 5000), authkey='douban')
    worker.connect()
    
    task = worker.get_task_queue()
    result = worker.get_result_queue()
    id_ = task.get()
    print '[~] Running task...'
    result.put(info)
    
测试结果：

`manager`模块运行在一台海外VPS，`worker1`也运行在该VPS，`worker2`运行在本地主机，三个进程正常配合运行。下图中左、中、右三个窗口分别对应`manager`, `worker1`, `worker2`。

[![测试截图](/images/doubancrawler.png)](http://jiapeng.win/images/doubancrawler.png)

### 6. 数据分析

获取数据中，待续。。。

### 7. TODO

- 目前尚未完全了解豆瓣反爬虫机制，爬虫爬取速度受到限制。官方说明单IP请求不超过40次/分钟，经测试发现除限制IP外，与cookies也有一定关联。
- 后期可将manager和worker模块中I/O操作改为异步执行或采用多线程。
- 使用代理服务器进行网络请求。
- 使用数据库进行数据存储。
- ...

*文中代码为部分片段，不保证能直接执行，查看完整代码：[Kr0c/DoubanRobot][1]*


[1]: https://github.com/Kr0c/DoubanRobot/
[2]: http://www.douban.com/people/130949863/
[3]: http://www.python-requests.org/en/master/
[4]: http://www.douban.com/people/130949863/contacts
[5]: http://www.douban.com/people/130949863/rev_contacts
[6]: http://www.crummy.com/software/BeautifulSoup/bs4/doc/
[7]: http://www.cnblogs.com/yueyue_jwfm/archive/2010/04/11/1709821.html
[8]: http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386832973658c780d8bfa4c6406f83b2b3097aed5df6000
