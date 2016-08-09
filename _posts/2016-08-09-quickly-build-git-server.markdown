---
layout: post
title: 快速搭建Git服务器
tags: Toolbox
---

#### 1. 安装Git：

	$ sudo apt-get install git

#### 2. 创建一个git用户，用于运行git服务：

	$ sudo adduser git

#### 3. 创建`~/.ssh/`和`~/.ssh/authorized_keys`：

切换到**git用户**，在根目录下创建`.ssh/`文件夹，在该文件夹中创建`authorized_keys`文件，并修改它们的权限：

> 需要使用非`root`权限

	su - git
	mkdir .ssh
	chmod 700 .ssh
	touch ~/.ssh/authorized_keys 
	chmod 644 ~/.ssh/authorized_keys

#### 4. 导入用户公钥`~/.ssh/id_rsa.pub`，一行一个：

	vim ~/.ssh/authorized_keys

#### 5. 初始化Git仓库：

	git init —bare test.git
 
#### 6. 然后就可以免密码git clone啦：

	$ git clone git@127.0.0.1:/home/git/test.git
	Cloning into 'test'...
	warning: You appear to have cloned an empty repository.
	Checking connectivity… done

#### *Tips:*
> - 每当需要创建新的仓库时，需shell登录初始化Git裸仓库。为确保安全，可以考虑禁用git用户使用shell登录。
- 为便于使用，可在`/etc/hosts`文件中，将git服务器的ip地址命名为`gitserver`。