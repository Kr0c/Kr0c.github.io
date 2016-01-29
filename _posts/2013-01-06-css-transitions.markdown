---
layout: post
title: 关于 CSS Transition 的一切
tags: CSS
---

刚刚读到的文章，非常不错，翻译出来与大家分享。原文 [All you need to know about CSS Transitions](http://blog.alexmaccaw.com/css-transitions) 由 Alex Maccaw 发表于 2013 年 1 月 3 日。不想花太多时间纠结于字面翻译，只专注最核心的信息，自己的 Javascript 只有入门水平，因此后半部分理解不深入，深入理解还请阅读原文，翻译不当的地方请大家指正。

## 浏览器支持

目前，几乎所有版本的 Firefox, Safari, Chrome 包括最新的 IE10 都支持 transition 属性。动画与渐变特效在 Safari 和 Chrome 下仍然需要添加 `-webkit` 前缀，不过很快也会不需要了。

## 使用 transition 属性

Transition 属性使用最多的地方要数 CSS 伪类了，比如 `:hover`。使用时指定变化的属性名，变化时间，以及变化样式。例如

{% highlight css %}
.element {
  height: 100px;
  transition: height 2s linear;
}

.element:hover {
  height: 200px;
}
{% endhighlight %}

当 `:hover` 伪类被激活时，`.element` 的高度在 2 秒钟的时间内从 `100px` 线性（linear）变化为 `200px`。

浏览器默认变化属性为 `all` （全部属性），变化样式为 `ease`，因此如果没特殊要求，只需定义变化的时间。

使用 js 添加、移除 class 要比使用伪类更方便，因为后者必须用户激活后才能触发。下面的例子里用到了两个变化效果，都是通过 js 触发的。

{% highlight css %}
.element {
  opacity: 0.0;
  transform: scale(0.95) translate3d(0,100%,0);
  transition: transform 400ms ease, opacity 400ms ease;
}

.element.active {
  opacity: 1.0;
  transform: scale(1.0) translate3d(0,0,0);
}

.element.inactive {
  opacity: 0.0;
  transform: scale(1) translate3d(0,0,0);
}
{% endhighlight %}

{% highlight js %}
var active = function(){
  $('.element').removeClass('inactive').addClass('active');
};

var inactive = function(){
  $('.element').removeClass('active').addClass('inactive');
};
{% endhighlight %}

## 渐变背景的变化

不是所有的 CSS 属性都可以变化，而且只能从一个绝对值变化到另一个绝对值。比如，`height` 不能从 `0px` 变化到 `auto`（补充：不同单位之间也可以变化，比如从 `0px` 变化到 `10em`，但不可以从 `0px` 变化到 `10%`），可变化的属性列表请参考 [http://oli.jp/2010/css-animatable-properties/](http://oli.jp/2010/css-animatable-properties/)。

此外，只有纯色背景可以变化，带有渐变效果的背景却不可以，没有任何技术上的原因不支持，开发者需要更多时间支持这些特效。不过，还是有几种变通办法的：

第一种，渐变颜色采用有透明度的 rgba，然后变化 `background-color` 背景颜色，如下：

{% highlight css %}
.panel {
  background-color: #000;
  background-image: linear-gradient(rgba(255, 255, 0, 0.4), #FAFAFA);
  transition: background-color 400ms ease;
}

.panel:hover {
  background-color: #DDD;
}
{% endhighlight %}

如果渐变样式是连续的，还可以通过 `background-position` 属性移动它，[http://sapphion.com/2011/10/css3-gradient-transition-with-background-position/](http://sapphion.com/2011/10/css3-gradient-transition-with-background-position/)。或者，创建两个元素叠加，控制上面一层的透明度。如下：

{% highlight css %}
.element {
  width: 100px;
  height: 100px;
  position: relative;
  background: linear-gradient(#C7D3DC,#5B798E);
}

.element .inner {
  content: '';
  position: absolute;
  left: 0; top: 0; right: 0; bottom: 0;
  background: linear-gradient(#DDD, #FAFAFA);
  opacity: 0;
  transition: opacity 1s linear;
}

.element:hover .inner {
  opacity: 1;
}
{% endhighlight %}

但是，这样需要额外的标签，上一层的元素也会获取鼠标事件。所以，如果这里能用 `:before`，`:after` 伪类是最理想的，可惜目前只有 Firefox 支持伪类变化。Eliott Sprehn 正在努力让 webkit 也支持伪类变化，这一功能很快也会在 Safari Chrome 上实现。

> **更新：**这个问题现在已经解决，目前 Firefox，Chrome 支持伪类变化，Safari 会在下一个版本中更新，Opera 目前还不支持，IE10 也支持，但有点奇葩，要用下面的方式 (Source: [CSS-Tricks](http://css-tricks.com/pseudo-element-animationstransitions-bug-fixed-in-webkit/))

{% highlight css %}
.x:hover:before { /* By itself, doesn't work */ }

.x:hover {}
.x:hover:before { /* This works (needs :hover declared) */ }
{% endhighlight %}

## 硬件加速

变化某些属性，比如 `left` ，`margin`，*浏览器会重新计算每一帧的显示效果*，这严重影响速度，尤其是在移动设备上。解决办法就是让 GPU 来做这些运算，简单的说，就是将元素转化为图片再制作变化效果，而不是重新计算每一帧的 CSS 样式。最简单的一个开启硬件加速的办法就是用 `transform: translate3d(0,0,0);` 属性。

不过，这并不是个完美解决方法，同时会带来一些弊端，所以要有选择的使用硬件加速，不要用的太多。其中一项弊端是，字体在变化时会 lose weight （Safari 中比较明显，变化过程中字体变细，变化完毕后突然恢复），原因就是硬件加速过程中不会对字体启用抗锯齿特效，避免这个问题只能禁用 `font-smoothing: antialiased;`。

此外，不用的浏览器使用不同的硬件加速库，浏览器之间存在差异。如果遇到不同浏览器中显示的变化效果不同，不要使用通用的 `transform3d()` 设置，给不同浏览器不同的变化参数。

## Clipping

为了让 GPU 更好的运算变化效果，要避免使用像 `width` 这样需要重新计算每一帧样式的属性，而要用 clipping（术语，only drawing things that will be visible to the viewer）。下面的例子将一个搜索框拉宽，使用 `translate3d` 属性令元素在 X 轴上拉长，而不是用 `width`，这样每一帧都不会重新计算元素的宽度，速度更快，效果更流畅：

{% highlight css %}
.clipped {
  overflow: hidden;
  position: relative;
}

.clipped .clip {
  right: 0px;
  width: 45px;
  height: 45px;
  background: url(/images/clip.png) no-repeat
}

input:focus {
  -webkit-transform: translate3d(-50px, 0, 0);
}
{% endhighlight %}

## 时间函数

浏览器内置的时间函数包括 `linear, ease, ease-in, ease-out and ease-in-out`，复杂一点，可以用 cubic-bezier 曲线自定义时间函数，比如 `transition: -webkit-transform 1s cubic-bezier(.17,.67,.69,1.33);` 。两个帮你选择时间函数的工具 [Pre-defined Curves](http://easings.net) 和 [Graphing Tool](http://cubic-bezier.com/#.17,.67,.83,.67)。

## 用程序控制变化效果

借助 js 可以更有效的控制变化的效果，例如将连续的变化做成动画。变化属性为 `all` 时意味着元素的所有属性都可以同时发生变化 （慎用 `all`），请看下面的例子

{% highlight js %}
var defaults = {
  duration: 400,
  easing: ''
};

$.fn.transition = function (properties, options) {
  options = $.extend({}, defaults, options);
  properties['webkitTransition'] = 'all ' + options.duration + 'ms ' + options.easing;
  $(this).css(properties);
};
{% endhighlight %}

然后用 jQuery 函数 `$.fn.transition` 调用变化样式

{% highlight js %}
$('.element').transition({background: 'red'});
{% endhighlight %}

## 获取变化结束的 callback

组合多个 transitions 首先要知道变化在什么时间结束，webkit 浏览器中可以监视 `webkitTransitionEnd` 事件，其他浏览器需要先用 [https://gist.github.com/4414792](https://gist.github.com/4414792) 找出事件名称。

{% highlight js %}
var callback = function () {
  // ...
}

$(this).one('webkitTransitionEnd', callback)
$(this).css(properties);
{% endhighlight %}

有时也许不工作，比如属性没有变化，或者没有被激活，确保每次都能得到一个 callback，要设定 timeout 手动激活事件。

{% highlight js %}
$.fn.emulateTransitionEnd = function(duration) {
  var called = false, $el = this;
  $(this).one('webkitTransitionEnd', function() { called = true; });
  var callback = function() { if (!called) $($el).trigger('webkitTransitionEnd'); };
  setTimeout(callback, duration);
};
{% endhighlight %}

现在可以调用 `$.fn.emulateTransitionEnd()` 确保变化结束的 callback 被激活。

## 连续的变化

熟悉用 js 调用变化，与获取变化结束的 callback 后，可以进一步将变化进行排列，做成连续的动画。jQuery 提供了两个对列相关的函数 `$.fn.queue(callback)` 与 `$.fn.dequeue()`。先将 CSS 变化样式写入 `$.fn.queue` callback 中，变化结束后调用 `$.fn.dequeue`。

{% highlight js %}
var $el = $(this);
$el.queue(function(){
  $el.one('webkitTransitionEnd', function(){
    $el.dequeue();
  });
  $el.css(properties);
});
{% endhighlight %}

这个例子很简单，再复杂一点，让我们用 `delay()` 做成一个连续在一起的动画效果。

{% highlight js %}
$('.element').transition({left: '20px'})
             .delay(200)
             .transition({background: 'red'});
{% endhighlight %}

## 重绘

在使用 `transition` 属性时，要写两套 CSS 属性，初始样式与结束样式。

{% highlight js %}
$('.element').css({left: '10px'})
             .transition({left: '20px'});
{% endhighlight %}

然而，如果同时设定了两套样式，紧连在一起，浏览器试着完善属性之间的变化，却会忽略初始样式并放弃变化。在后台，浏览器其实是分批更新变化样式的，这样会加快速度，但有时也有不利的影响（注，这一段没有经验，不保证翻译的准确度）。

解决办法是利用 DOM 元素的 `offsetHeight` 属性让浏览器在两套样式之间重绘元素，例如：

{% highlight js %}
$.fn.redraw = function(){
  $(this).each(function(){
    var redraw = this.offsetHeight;
  });
};
{% endhighlight %}

这个方法在大多数浏览器下都工作正常，但在 Android 下还不够，使用 timeout 或者激活一个 class。

{% highlight js %}
$('.element').css({left: '10px'})
             .redraw()
             .transition({left: '20px'});
{% endhighlight %}

## Transition 的未来

W3C 的 [新草案](https://dvcs.w3.org/hg/FXTF/raw-file/tip/web-anim/index.html) 包含新的 Javascript API 应对现在的局限性，给开发者更灵活的空间。根据新的 API，可以同步动画，自定义时间函数，获取 callbacks，实在是令人期待的新功能。

{% highlight js %}
var anim = new Animation(elem, { left: '100px' }, 3);
anim.play();
{% endhighlight %}

另外，可以在 [https://github.com/web-animations/web-animations-js](https://github.com/web-animations/web-animations-js) 找到一些实现新 API 的变通法子。

## 结尾

目前为止，希望你已经深入的了解了 CSS 的 transition 属性，以及如何组合简单的 API 制作丰富的动画效果。文中大多数 js 例子来源于 [GFX](https://github.com/maccman/gfx)，这也是本文作者 Alex 写的一个 jQuery CSS transition 库。
