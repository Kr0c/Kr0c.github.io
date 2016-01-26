---
layout: post
title: CSS3 Slider 模仿练习
tags: Codepen
---

[![CSS Slider](/upload/2013/codepen-1.jpg)](http://codepen.io/P233/pen/HGdir)

最近模仿了一个动画网站练习用 CSS 写 Slider，记录一点经验心得。

{% highlight scss %}
a {
  opacity: 0;
  transform: translate(-10px, 5px);
  transition: transform 0.3s, opacity 0.3s;
  @for $i from 1 through 5 {
    &:nth-of-type(#{$i}) {
      transition-delay: 0.3s + $i*0.1s;
    }
  }
}
.parent:hover a {
  opacity: 1;
  transform: translate(0px, 0px);
}
{% endhighlight %}

一，使用 `transition` 属性控制变化效果的时候，变化项也需要添加浏览器前缀，开始没有注意到这点多次尝试还以为做错了；

二，`:nth-of-type()` 是个好东西，可玩性非常高，配合 `transition-delay` 不借助 js 就可以做出连续的动画效果；

{% highlight html %}
<label for="trigger1">Slide1</label>
<label for="trigger2">Slide2</label>
<label for="trigger3">Slide3</label>
<label for="trigger4">Slide4</label>

<input type="radio" id="trigger1" name="trigger" checked="">
<input type="radio" id="trigger2" name="trigger">
<input type="radio" id="trigger3" name="trigger">
<input type="radio" id="trigger4" name="trigger">

<div id="wrapper">
  <div id="slider">
  </div>
</div>
{% endhighlight %}

{% highlight scss %}
#slider {
  width: $width * 4;
}
@for $i from 1 through 4 {
  #trigger#{$i}:checked ~ #wrapper #slider {
    transform: translateX(($i - 1) * -$width);
  }
}
{% endhighlight %}

三，利用单选框与 `:checked` 伪类存储用户的输入信息是一件很巧妙的做法，乍看上去有点麻烦但相信还是可以继续深度发掘出更多用法，比如这个纯 CSS 写的小游戏：[CSS PANIC](http://jsdo.it/GeckoTang/4rXg)

四，同级选择器 `~` 与后代选择器连用，通过不同的单选框控制不同的偏移量。

这是 CSS3 Slider 的一种写法，印象中还有另一种写法利用到了 `:target` 伪类，下次有时间再尝试一下。
