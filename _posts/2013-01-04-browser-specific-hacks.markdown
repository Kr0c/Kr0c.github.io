---
layout: post
title: CSS 判断浏览器方法整理
tags: CSS
---

[http://browserhacks.com/](http://browserhacks.com/)，最全的 hack 方法整理。

记录几个常用的判断浏览器的方法，在方便查阅。

## Conditional Comments

{% highlight html %}
<!--[if IE]>
  According to the conditional comment this is IE
<![endif]-->

<!--[if IE 6]>
  According to the conditional comment this is IE 6
<![endif]-->

<!--[if IE 7]>
  According to the conditional comment this is IE 7
<![endif]-->

<!--[if IE 8]>
  According to the conditional comment this is IE 8
<![endif]-->

<!--[if IE 9]>
  According to the conditional comment this is IE 9
<![endif]-->

<!--[if gte IE 8]>
  According to the conditional comment this is IE 8 or higher
<![endif]-->

<!--[if lt IE 9]>
  According to the conditional comment this is IE lower than 9
<![endif]-->

<!--[if lte IE 7]>
  According to the conditional comment this is IE lower or equal to 7
<![endif]-->

<!--[if gt IE 6]>
  According to the conditional comment this is IE greater than 6
<![endif]-->

<!--[if !IE]> -->
  According to the conditional comment this is not IE
<!-- <![endif]-->
{% endhighlight %}

Source: [http://www.quirksmode.org/css/condcom.html](http://www.quirksmode.org/css/condcom.html)

## Target IE

{% highlight css %}
p {
  color: red; /* All browsers */
  color: red\9; /* IE8 and below */
  *color: red; /* IE7 and below */
  _color: red; /* IE6 */
}

/* Target IE 10 */
@media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {
  p {
    color: red;
  }
}
{% endhighlight %}

## Target Safari and Chrome

{% highlight css %}
@media screen and (-webkit-min-device-pixel-ratio:0) {
  p {
    color: red;
  }
}
{% endhighlight %}

## Target Firefox

{% highlight css %}
@-moz-document url-prefix() {
  p {
    color: red;
  }
}
{% endhighlight %}

## Target Opera

{% highlight css %}
x:-o-prefocus, p {
  color: red;
}
{% endhighlight %}
