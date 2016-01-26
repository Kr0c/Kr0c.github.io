---
layout: post
title: CSS 3D Walking Robot
tags: Codepen
---

[![CSS 3D Walking Robot](/upload/2013/codepen-5.jpg)](http://codepen.io/P233/pen/jrguI)

看到大家都在写 CSS 3D 特效，也跟着写了一个会走路的 3D 机器人。

一开始直接写 CSS 3D 效果结果越写越乱，只好推倒重来，先写完 2D 的机器人结构，主要使用的是 `position: absolute` 定位，把需要使用的 div 都准备好，机器人每个部分的每一个面都是一个 div，也就是所使用的 `.front` `.back` `.left` `.right` `.top` 与 `.bottom`。

第一步，给每个部位（包括最外层）添加 `transform-style: preserve-3d` 属性激活 3D 模式（注：webkit 中这个属性可以继承，但 Firefox 中不行，所以保险的做法是给每个部位都添加这个属性）。然后配合使用 `transform: rotate3D()` `transform: translate3D()` 以及 `transform-origin` 将刚刚提到的 6 个面旋转／偏移到指定位置，这样完整的 3D 机器人就成型了。

在使用 `transform-origin` 时遇到一个问题，到目前还没找到合适的解决办法。Chrome 在同时处理 `transform-origin` 与 `transform: translate3D()` 时不考虑偏移量，以 div 的实际长宽（应该包含 `margin`）计算，而 Safari 处理时将偏移量也增加到 div 的长宽中，所以这个机器人在两个浏览器中胳膊，大腿的甩动位置不一样。Firefox 的处理方式与 Chrome 是相同的，IE10 3D 模式无效，应该是我的问题，没有仔细研究。

然后，给机器人的每个部位都使用 `animation` 属性设定旋转角度，四肢 X 轴，躯干和身体 Y 轴，同样需要配合 `transform-origin`，时间函数使用 `ease-in-out` 让动作看起来更自然，会走路的机器人就完成了。

自己也是一名 CSS 3D 新手，如果大家发现错误的地方，欢迎指正 :)
