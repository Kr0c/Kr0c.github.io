---
layout: post
title: Pure CSS Happy Hacking Keyboard
tags: Codepen
---

[![Pure CSS Happy Hacking Keyboard](/upload/2013/codepen-4.jpg)](http://codepen.io/P233/pen/qEagi)

纯 CSS 写的 Happy Hacking Keyboard。

首先，键帽周围的效果是通过 `border` 属性实现的，一般情况下会给 div 的四边相同的 `border` 宽度与颜色，往往忽视了这个属性的工作方式，给四边不同的宽度与颜色就会发现一些玄机（理解 CSS 画三角形也就不复杂了）。为了让键盘看起来更自然一些，键帽中间的 div 使用了阴影，让 `border` 看上去不再是纯色的而是能感受到深浅变化。

键帽侧面的字体通过 `transfrom: rotateX()` 属性实现，此外，尝试给键盘使用 `transform: rotateX(15deg)` 意外发现了浏览器在渲染时增加了一点点模糊效果，颜色的区分不再界限分明，看上去更舒服。不过 Firefox 渲染效果不太好，所以只保留了 `-webkit-transform`， `-ms-transform` 与 `-o-transform` 三个属性，也算是一种 hack 方法吧。

为了制造键帽顶部凹陷下去的效果，使用了线性渐变 `linear-gradient`，调颜色是关键，真的花了不少时间。

按键效果通过 `transform: scale()` 缩小实现，每个按键都有独立的 class, 也就是是首字母 `k` + 按键的 keycode，这样就可以通过 javascript 添加／删除 class 表现按下去的效果了。

因为多个特效重叠使用，这个页面的性能比较糟糕。

最后，感谢网友 [dehash](http://codepen.io/dehash) 在这个键盘的基础上添加了打字特效，欢迎大家查看 [http://codepen.io/dehash/pen/nEJyf](http://codepen.io/dehash/pen/nEJyf)。
