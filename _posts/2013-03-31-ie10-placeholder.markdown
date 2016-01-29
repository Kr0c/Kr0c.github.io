---
layout: post
title: IE10 的 placeholder 伪类问题
tags: CSS
---

从 HTML5 开始 `input` `textarea` 支持 `placeholder` 属性，除了 IE 浏览器，兼容性还算不错，以为 IE10 也可以友好兼容的，然后发现其实我是图样图森破。

{% highlight scss %}
::-webkit-input-placeholder {
  color: lighten($grey, 10%);
  font-weight: normal;
}
:-moz-placeholder {
  color: lighten($grey, 10%);
  font-weight: normal;
}
{% endhighlight %}

给主流浏览器设定 placeholder 样式没有任何问题，同样的方法在 IE10 下却不起作用：

{% highlight scss %}
:-ms-input-placeholder {
  color: lighten($grey, 10%);
  font-weight: normal;
}
{% endhighlight %}

以为伪类在 IE10 下不能单独使用，添加了 `input` 标签同样无效：

{% highlight scss %}
input:-ms-input-placeholder {
  color: lighten($grey, 10%);
  font-weight: normal;
}
{% endhighlight %}

尝试 id 或 class，结果终于生效了，所以必须这样设定样式，很蛋疼：

{% highlight scss %}
#name:-ms-input-placeholder, #phone:-ms-input-placeholder, #email:-ms-input-placeholder, #subject:-ms-input-placeholder, #message:-ms-input-placeholder {
  color: lighten($grey, 10%);
  font-weight: normal;
}
{% endhighlight %}

还没结束呢，使用 class 也不行，不过官网却用的 class 选择器举例子，不深究了，保险起见使用 id 选择器与 `:ms-input-placeholder` 伪类。看来前端的 IE 魔咒还没有破除，期待 IE11。。。
