---
layout: post
title: 关注 CSS 性能 (一)
tags: CSS
---

虽然自己只是入门水平会写简单的页面，但本着“取法乎上，得乎其中”的原则，一直关注 CSS 性能，读过几篇相关文章，准备逐步翻译出来当做总结与积累。第一篇 [Why you should care about CSS page performance](http://boagworld.com/dev/why-you-should-care-about-css-page-performance/)
由 Doug Stewart 发表于 2012 年 5 月。以下只摘录几点重要的信息，没有直接翻译，理解不当的地方还请大家指正。

Doug 介绍了自己测试 CSS 性能的两个方法：缩放浏览器窗口与上下滚动页面。浏览器重绘页面可以反映很多关于性能的问题，未添加 CSS 的页面反应速度肯定是最快的。一般来说，*如果页面缩放时有明显延时，很可能是布局问题，比如浮动、流体宽度等等，而如果页面滚动时有明显延时，则可能是特效问题，比如说阴影与渐变。*

优化 CSS 性能，自然要先找到问题出在哪里。作者介绍了三款衡量页面性能的工具：[CSS Stress Test](https://github.com/andyedinborough/stress-css)，Chrome Developer Tools 的 Profiles 与 Timeline。就不一一介绍这些工具了，原则上是找出最耗时间的地方，然后想办法优化。

作者给出的 CSS 性能小提示：

1. 大家都知道 id 选择器的表现效果要优于 class，事实虽然也如此，但仅仅是非常微小的差别，几乎可以不予考虑；
2. CSS3 属性往往是影响页面性能的罪魁祸首，尤其是 `animation` 与 `@font-face`，谨慎地使用；
3. 现代浏览器支持 Javascript 与 Flash 硬件加速，却不支持 CSS， 虽然 webkit 有优化功能，但还是受限的；
4. 移动平台的情况是，即便现在最先进的手机也不及 10 年前最先进的电脑;
5. 人脑有大约 80 毫秒的反应时间，优化页面性能时不要在一些细枝末节的地方花太多精力，专注于拖慢严重的地方。

**PS:** 有读者回复 Javascript 其实没有硬件加速，Safari mobile 使用 GPU 运算 animations 与 3D 特效。
