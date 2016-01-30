---
layout: post
title: Visited 选择器的使用限制
tags: CSS
---

尝试给访问过的链接添加样式，结果遇到了问题，代码如下：

{% highlight html %}
<a id="link" href="#">link</a>
{% endhighlight %}

{% highlight css %}
#link:before {
  content: "+";
}
#link:visited:before {
  content: "-" !important;
}
{% endhighlight %}

链接被点击后，只有 Opera 浏览器正常显示替换后的内容，其他浏览器全部失败。百思不得其解，提问后才知道，`:visited` 使用是有限制的：只能变化 `color`, `background-color`, `border-*-color`, `outline-color`, 以及 `column-rule-color` 等与颜色有关的属性，不可以改变其他属性。`content` 不在这个范围内，自然不能工作了（ Opera 是不是太超前了，呵呵）。

相关文章：

* [Privacy and the :visited selector](https://developer.mozilla.org/en-US/docs/CSS/Privacy_and_the_%3avisited_selector)
* [Preventing attacks on a user's history through CSS :visited selectors](http://dbaron.org/mozilla/visited-privacy#limits)
* [privacy-related changes coming to CSS :visited](http://hacks.mozilla.org/2010/03/privacy-related-changes-coming-to-css-vistited/)
