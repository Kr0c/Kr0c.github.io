---
layout: post
title: Vim Keyboard Shortcuts
tags: Codepen
---

[![Vim Keyboard Shortcuts](/upload/2013/codepen-3.jpg)](http://codepen.io/P233/pen/jrxzE)

开始学习 Vim，准确的说是学习使用 Sublime Text 的 Vim 模式，做了一个页面辅助记忆快捷键。

遇到两个比较费神的问题，一个是 `column-count` 属性在 Firefox 下不可将 `table` 元素拆分成多列，Stackoverflow 提问后得知 Gecko 不支持对 table 元素动态分页，只好用 hack 将宽度限制在 `1024px`。

第二个问题还是与 Firefox 元有关，table 相关元素不支持 `position: relative`  `position: absolute` 等定位，解决办法是在 `td` 内创建一个 `position: relative` 的 div 包裹内容，详细解释请看 [http://stackoverflow.com/questions/5148041/does-firefox-support-position-relative-on-table-elements](http://stackoverflow.com/questions/5148041/does-firefox-support-position-relative-on-table-elements)。

第三点不太重要，其他浏览器虽然可以将 table 元素拆分成多列，如果表格 `tr` 的数量为奇数，中间的一项会被分裂，解决办法是在表格最后添加空白 `tr`，凑成偶数。
