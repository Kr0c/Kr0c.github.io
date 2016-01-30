---
layout: post
title: 用 Dropbox 同步 Mac 软件设置
tags: 杂货箱
---

台式机与笔记本电脑之间虽然用 Dropbox 同步文件很方便，但偶尔会想如果可以同步软件设置就更完美了。某个周末花了些时间搜集、整理了一些方法，在此分享出来。不必非用 Dropbox，Google Drive 等类似服务也没有问题。

Mac 用户都知道软件的配置文件存储在 `.plist` 文件中，同步的原理就是将这个文件移动到 Dropbox 文件夹下，交给 Dropbox 管理，然后在原地址创建 symlink（被称为符号链接，相当于快捷方式，具体解释还请搜索 google）指向 Dropbox 内的配置文件。软件每次读取/保存设置时其实是修改 Dropbox 内的文件。Mac 系统创建 symlink 的命令是: `ln -s <source_file> <myfile>`，或者先使用 `cd` 命令进入目标地址，然后用 `ln -s <source_file>` 命令将目标文件连接到当前地址。Windows 创建 symlink 的命令是 `mklink [[/d] | [/h] | [/j]] <Link> <Target>`。

> 如果不想使用命令，也可以试试 [DropboxAppSync](http://joeworkman.net/blog/post-9591023714) 这款软件，原理是完全一样的

## 同步 Sublime Text 2
Sublime Text 2 所有的配置文件、插件都存放在 Packages 文件夹下，这是一项非常重要的设定，因为 Mac OS, Windows, Linux 三个平台之间因此可以相互同步，而且包括所有插件！以下介绍 Mac 系统下的同步方法：

1. 关闭 Sublime Text 2；
2. 在准备同步的主机上找到 Sublime Text 2 的 packages 文件夹（路径：`~/Library/Application Support/Sublime Text 2/`），将其移动到 Dropbox 文件夹内，例如 `~/Dropbox/Sync/Packages`；
3. 启动 Terminal，输入 `cd ~/Library/Application\ Support/Sublime\ Text\ 2/` 打开目标文件夹（空格前要加 `\`），然后输入 `ln -s ~/Dropbox/Sync/Packages` 创建一个 symlink 将 Dropbox 下的 Packages 文件夹连接到当前目录；
4. 在第二台机器上，关闭 Sublime Text 2，找到 packages 文件夹并将其删除，然后重复步骤 3 中的命令。

## 同步其他软件
配置文件 `.plist` 通常保存在 `~/Library/Preferences/` 文件夹中，只要给这些文件创建 symlink 就可以完成同步。不过，有些软件除了 `.plist` 文件外，还会在 `~/Libray/Application Support/` 下创建文件夹存储额外的设置，比如 Alfred 会创建一个文件夹存储 extensions，TextMate 会创建一个文件夹存储 Bundles 与 Themes，给这些文件夹创建 symlink 可以实现更彻底的同步。*但是，并不是所有的软件都允许同步这些文件夹*，有些软件会删除 symlink 重新写入文件，而不是基于 symlink 找到位于 Dropbox 内的文件。已经同步的软件如果更新，则不会影响到 symlink。

## 同步 Mac OS Keychain
除了同步软件设置，Mac OS 还有一样可以同步，就是 Keychain。如果你的两台 Mac 之间完全共用一套密码，同步 Keychain 会是一个很不错的选择，即便不是，把 Keychain 存储到 Dropbox 重装新系统也会保留这些密码。此外，FTP 软件普遍将登陆信息存储进 Keychain，只同步 `.plist` 文件是没有效果的，还需要同步 Keychain。

同步方法：将 `~/Library/Keychains/` 下的 login.keychain 文件移动到 Dropbox 文件夹内；启动 Keychain Access，选择 File > Add Keychain…，然后添加位于 Dropbox 下的 login.keychain 文件；在第二台电脑上删除本地的 login.keychain 文件，然后重复 File > Add Keychain…。同步 Keychain 后，重启电脑偶尔会被要求输入密码，验证 Keychain 的管理权限。

> 现在插播广告：我的 Dropbox 推广链接 [http://db.tt/dKXdR3gf](http://db.tt/dKXdR3gf)，通过这个链接注册 Dropbox 账户，我们每个人都可以获得额外的 500MB 空间 :)

**最后，不要在两台电脑上同时运行同步的软件，会因为文件冲而突丢失设置。**
