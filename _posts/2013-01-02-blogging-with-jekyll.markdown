---
layout: post
title: 使用 Jekyll 写博客
tags: 杂货箱
---

纪念刚刚过去的 2012 年，展望 2013，开始用时下流行的 Jekyll 在 Github 上写博客，积累经验，深度思考，期望在新的一年取得更好的成绩。

网络上关于 Jekyll 的文章已经非常详细了，在这里附上一点使用心得，从业余的角度看 Jekyll，也许能方便刚刚开始接触 Jekyll 的同学。

1. 门槛：需要理解 Github 的工作方式，熟悉 Github 客户端，熟悉 Html 与 CSS；
2. 不妨一边开始写静态模板，一边了解 Jekyll 布局所用到的 [Liquid Tag](https://github.com/mojombo/jekyll/wiki/Liquid-Extensions)，磨刀不误砍柴工；
3. Jekyll 博客，可以看作是一套文件结构，通过 Jekyll 程序编译成静态网站。如果不熟悉命令，完全可以不安装 Jekyll，只需按照[要求的格式](https://github.com/mojombo/jekyll/wiki/usage)创建文件后再上传到 Github，然后访问主页就可以了，因为 Github Pages 便是由 Jekyll 驱动的，或者直接使用 [Jekyll-Bootstrap](http://jekyllbootstrap.com) 的结构；
4. Jekyll 的文件结构大概可以这样分：配置文件 `_config.yml`，布局文件 `_layouts`，模块文件 `_includes`，插件 `_plugin`，文章 `_posts`，其他文件（不以*下划线*开头的文件及文件夹都会完整的拷贝到生成的静态网站中，比如 CSS 文件、图片等），以及将会生成的静态站 `_site`；
5. Jekyll 命令很简单，先使用 `cd` 命令进入目标文件夹，然后输入 `jekyll --server` 生成网站，浏览器中输入 `0.0.0.0:4000` 访问生成的静态网站，`jekyll --server --auto` 命令将开启实时更新，修改文件后在浏览器中刷新就可看到效果，对本地调试很有帮助；
6. Github Pages 禁用所有插件，需要使用自定义插件，只能上传生成的网站文件 `_site`，或者试试 [这个办法](http://edhedges.com/blog/2012/07/30/jekyll-with-plugins-hosted-on-github-pages/) ;
7. 想写草稿不想被编译？创建一个以下划线开头文件夹就会被忽略，例如在 `_posts` 下创建 `_drafts` 存储草稿；
8. 默认的 Markdown 引擎问题很多，建议替换成 [RDiscount](https://github.com/mojombo/jekyll/wiki/Install#rdiscount)。

## Jekyll Alfred 2 Workflows

三个 Alfred2 Workflows 让 Jekyll 用起来更方便：Create a Jekyll Post, Generate Jekyll site, 以及 Push Jekyll post to Github，下载地址：[https://github.com/P233/Jekyll-Alfred-Extensions](https://github.com/P233/Jekyll-Alfred-Extensions)
