---
layout: post
title: 如何优雅地翻墙？
tags: Toolbox
---

![GFW](/images/GFW.jpg)

## GFW

防火长城（英文名称Great Firewall of China，简写为Great Firewall，缩写GFW），也称中国防火墙或中国国家防火墙，指中华人民共和国政府在其管辖因特网内部建立的多套网络审查系统的总称，包括相关行政审查系统。

我们对GFW的功过暂且不做讨论，如果你厌倦了虚假医疗广告和过度商业化却苦于无法使用Google，或者你仅仅想在Instagram分享精彩瞬间，亦或是你想和你的国际友人在Facebook, Twitter互动，那么这篇文章就是为你而写的。

通常，我们翻越GFW的途径有一下几种：

- 免费VPN：天下没有免费的午餐，僧多粥少，这类免费账号通常很快就会失效，安全性也难以保证。
- 付费VPN：可以保证长期有效，但商家往往将一个账号卖给多个用户，因此使用体验可能较差。
- 自己搭建代理服务器：自己动手，丰衣足食。需要一定的动手能力和英文基础。不过这是一个一劳永逸的过程。当然，你必须保证VPS账号有足够的Money :)

## Shadowsocks（影梭）

Shadowsocks（中文名称：影梭）是使用Python等语言开发的、基于Apache许可证开源的代理软件。Shadowsocks使用socks5代理，用于保护网络流量。Shadowsocks分为服务器端和客户端。在使用之前，需要先将服务器端部署在支持Python的服务器上面，然后通过客户端连接并创建本地代理。

### 服务端

部署Shadowsocks的过程非常简单。首先，我们需要购买一台海外VPS，服务商有很多家，我目前使用的是`Vultr`，使用体验较为满意。同类的还有`Linode`和`DigitalOcean`等，不过
`Linode`价格相对较高，`DigitalOcean`的速度不及`Vultr`。这里推荐`Vultr`，选择最便宜的5美元/月就可以了。

> 如果不介意，通过我的推广链接注册：[http://www.vultr.com/?ref=6867518](http://www.vultr.com/?ref=6867518)，这样我们可以各获得$10奖励～

`Vultr`甚至还提供了通过网页显示可视化控制台的功能，即使你没有运行`MacOS X`或`Linux`的机器，你也可以很方便的登录到你的VPS。

![vultr](/images/vultr.png)

创建VPS时，建议选择洛杉矶节点，Ubuntu系统，$5/mo，然后给服务器起个名字，其他不熟悉的选项可以不管。

登录VPS，安装服务端Shadowsocks：

**Debian/Ubuntu:**

    apt-get install python-pip
    pip install shadowsocks

**CentOS:**

    yum install python-setuptools && easy_install pip
    pip install shadowsocks

新建配置文件：

    vi /etc/shadowsocks.json
    
然后填入以下内容，需要修改的部分：服务器地址`server`，改成你刚刚创建的VPS的IP地址；端口`server_port`，随便设置一个`1024~65535`之间的数值；密码`password` 。

    {
        "server": "your_server_ip",
        "server_port": 8388,
        "local_address": "127.0.0.1",
        "local_port": 1080,
        "password": "yourpassword",
        "timeout": 300,
        "method": "aes-256-cfb",
        "fast_open": false
    }
    
端口号、密码就构成了一个账号。如果你需要设置多个账号，将上面的内容改成：
    
    {
        "server": "your_server_ip",
        "local_address": "127.0.0.1",
        "local_port": 1080,
        "port_password": {
            "9001": "password1",
            "9002": "password2",
            "9003": "password3"
        },
        "timeout": 300,
        "method": "aes-256-cfb",
        "fast_open": false
    }


启动和停止Shadowsocks服务器：

    ssserver -c /etc/shadowsocks.json -d start
    ssserver -c /etc/shadowsocks.json -d stop


### 客户端

不同平台下都有相应的客户端程序，安装好之后输入之前设置好的服务器地址、端口和密码，就可以愉快地开始科学上网了！如果你有兴趣，还可以将客户端部署到路由器。

- Windows：点击下载：[Shadowsocks-2.5.8.zip](/images/Shadowsocks-2.5.8.zip)
- MacOS X：点击下载：[ShadowsocksX-2.6.3.dmg](/images/ShadowsocksX-2.6.3.dmg)
- iOS: 在`App Store`中国区貌似已经下架，建议切换到美国区下载，越狱用户可以在`Cydia`中搜索安装。
- Android: 各大应用商店搜索安装“影梭”。
- ...

