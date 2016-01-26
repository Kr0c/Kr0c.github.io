---
layout: post
title: CSS3 Timeline
tags: Codepen
---

[![CSS3 Timeline](/upload/2013/codepen-2.jpg)](http://codepen.io/P233/pen/lGewF)

用 CSS 写的 Timeline 效果，很荣幸入选了 2013 年最流行的 100 个 Codepen 之一。与上一个[CSS3 Slider 模仿练习](http://cdpn.io/HGdir)使用的方法基本相同，额外记录一点经验心得。

{% highlight scss %}
a {
  transition: color 0.1s linear;
  &:hover {
    transition: color 0.3s linear;
  }
}
{% endhighlight %}

像上面这样设定 `transition` 属性，当鼠标悬停在元素上时，使用 `a:hover` 的变化样式，当鼠标离开时，使用 `a` 的变化样式。如果 `a:hover` 没有设定变化样式，鼠标悬停或离开都使用 `a` 的样式。

补充，后来加入了横版效果，为了控制条目的宽度和美观，标题超出部分使用 `…` 表示，代码片段为：

{% highlight scss %}
h1 {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
  display: block;
}
{% endhighlight %}
