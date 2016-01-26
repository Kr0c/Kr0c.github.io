---
layout: post
title: Rebar Grid Framework
tags: Sass
---

最近半年断断续续一直在研究一套基于 Sass map 或 Stylus hash 的 grid framework。它的理念是通过设置一串 breakpoints 划分出若干个 responsive slices 然后一次性针对所有的 slices 设置 container 与 grid（也许将来能够支持更多的值），让 responsive development 变得更加高效。结果非常满意，我个人认为这是最好用的 grid framework，不知道大家会不会同意 ;-)

- 项目主页: [rebar.io](http://rebar.io)
- GitHub: [https://github.com/P233/Rebar](https://github.com/P233/Rebar)

以下是 Rebar 的介绍，如果有任何疑问或者建议欢迎联系我。

## Guide

Rebar 支持 Sass 与 Stylus，需要 Sass 3.4 以上版本，或者 Stylus，暂时不能用于 LibSass 3.1。

### Box-sizing

Rebar 与 Bootstrap 3 的 grid system 相同，都使用 `padding` 作为 gutter，因此需要设置全局 `box-sizing: border-box` 以便更好地控制 grid 的宽度。（参考 [* { Box-sizing: Border-box } FTW](http://www.paulirish.com/2012/box-sizing-border-box-ftw/)）

{% highlight css %}
html {
  box-sizing: border-box;
}
*, *:before, *:after {
  box-sizing: inherit;
}
{% endhighlight %}

### Mobile first 与 Desktop first

Rebar 支持 mobile first 与 desktop first 两种 `@media query` 方式，这会影响 container 与 grid 的设置，所以在使用之前请先选择使用哪一种。当 mobile first 时，media feature 使用 `min-width`。而 desktop first 时，madia feature 使用 `max-width`。默认使用 mobile first，创建变量 `$mobile-first: false` 则使用 desktop first。

### Responsive Slice

Rebar 最核心的部分是 responsive slice。在 `$breakpoints` 变量中设置 `n` 个 breakpoints，它将划分出 `n + 1` 个 slices。例如，当 mobile first 时，`$breakpoints: 481px 769px 993px` 代表了 4 个 slices：slice 0 代表任意宽度，slice 1 代表 `min-width: 481px`，slice 2 代表 `min-width: 769px`，slice 3 代表 `min-width: 993px`。这时如果设置宽度 `width: 1 1/2 null 1/3`，第一个值 `1` 给 slice 0，第二值 `1/2` 给 slice 1，第三个值 `null` 给 slice 2，最后一个值 `1/3` 给 slice 3。

基于 `$mobile-first` 的设置共有两种情况：

{% highlight text %}
$mobile-first:  true
---------------------------------------------------------------------------
 $breakpoints:                481px         769px         993px
      slice 0:  0 ------------------------------------------------------- ∞
      slice 1:                |-----------------------------------------> ∞
      slice 2:                              |---------------------------> ∞
      slice 3:                                            |-------------> ∞
---------------------------------------------------------------------------
     $gutters:  20px          20px          26px          30px
        width:  100%          50%           null          33.3%
{% endhighlight %}

{% highlight text %}
$mobile-first:  false
---------------------------------------------------------------------------
 $breakpoints:                992px         768px         480px
      slice 0:  ∞ ------------------------------------------------------- 0
      slice 1:                |-----------------------------------------> 0
      slice 2:                              |---------------------------> 0
      slice 3:                                            |-------------> 0
---------------------------------------------------------------------------
     $gutters:  30px          26px          20px          20px
        width:  33.3%         50%           null          100%
{% endhighlight %}

#### null

`null` 表示在这个 slice 不输出属性。上面的例子中 `width: 1 1/2 null 1/3` 在 slice 2 不输出宽度属性，所以此时的宽度还是上一次设定的值，也就是 `1/2`。可以认为 `null` 表示继承，但是在 grid 的 `gutter-left` 与 `gutter-right` 两项设置中，`null` 表示还原为默认值。此外，修改变量 `$gutters` 时，也不允许使用 `null` 省略。

结尾的 `null` 可以省略，例如 `width: 1/3 null null null` 可简写为 `width: 1/3`。

### 设置 container 与 grid

Container 与 grid 的设置分别在 `$container` 与 `$gird` 这两个 Sass map 或 Stylus hash 中管理。新建一个 container 或 grid，只需在相应 map/hash 中嵌套第二层 map/hash，将选择器引用并作为名称，然后在其中添加设置。例如：

**Sass map**

{% highlight scss %}
$container: (
  ".container": (
    width: 100px 300px 600px 900px
  )
);
$grid: (
  ".third": (
    width: 1 null 50% 1/3
  )
);
{% endhighlight %}

#### Stylus hash

{% highlight js %}
$container = {
  ".container": {
    width: 100px 300px 600px 900px
  }
};
$grid = {
  ".third": {
    width: 1 null 50% 1/3
  }
};
{% endhighlight %}


### Columns

Rebar 中没有固定列数的限制，可以灵活实现任意宽度的布局。例如，传统 12 列网格中的 6 列可以表示为 `1/2` `50%` `.5` 或者 `6/12`。再或者实现如宽度 `64.136%` 与宽度 `(1 - 64.136%)` 这样的组合。

设置 `width` `max-width` `min-width` `offset` `gutter` 以及 `$gutters` 等值时都可以使用分数、小数、百分数，或者带有 `px` `em` 等单位的固定值。


### @import rebar

Rebar 的主文件需要在 `$container` 与 `$grid` 之后载入才能获取其中的设置，所以请按照下面的顺序整理代码：

{% highlight scss %}
// Default Settings (optional)
// $mobile-first: true
// $max-width:    1170px
// $breakpoints:  481px 769px 993px
// $gutters:      20px 20px 26px 30px

// Containers
$container: ();

// Grids
$grid: ();

// Import Rebar
@import "rebar";
{% endhighlight %}

### 输出代码

编译时，Rebar 根据所有的设置生成 CSS 代码，也会尽可能地简化所输出的代码。比如，使用默认 gutter 值的 grid 与 container 会合并输出，宽度为 `100%` 的 grids 也会被合并。


---

## slicer() Mixin

`slicer()` mixin 辅助 Rebar 划分 responsive slice 并生成 `@media query` 语句，但也可以单独使用。它接收 1 到 2 个参数，当传入 slice index 时将得到对应的 `@media query` 语句，传入负的 slice index 时将得到与对应 slice 相反部分的 `@meida query` 语句，传入两个值时将得到一个范围内的 `@media query` 语句。例如:

{% highlight scss %}
// $mobile-first: true
// $breakpoints:  481px 769px 993px
@include slicer(2) {}
@include slicer(-2) {}
@include slicer(2, -3) {}
{% endhighlight %}

编译为：

{% highlight scss %}
@media screen and (min-width: 769px) {}
@media screen and (max-width: 768px) {}
@media screen and (min-width: 769px) and (max-width: 992px) {}
{% endhighlight %}

有效的 `slicer()` 的输出结果包括：

{% highlight text %}
$mobile-first:  true
---------------------------------------------------------------------------
 $breakpoints:                481px         769px         993px
---------------------------------------------------------------------------
   slicer(-1):  0 <----------|
    slicer(1):                |-----------------------------------------> ∞
   slicer(-2):  0 <------------------------|
    slicer(2):                              |---------------------------> ∞
   slicer(-3):  0 <--------------------------------------|
    slicer(3):                                            |-------------> ∞
---------------------------------------------------------------------------
slicer(1, -2):                |<---------->|
slicer(2, -3):                              |<---------->|
slicer(1, -3):                |<------------------------>|
{% endhighlight %}


{% highlight text %}
$mobile-first:  false
---------------------------------------------------------------------------
 $breakpoints:                992px         768px         480px
---------------------------------------------------------------------------
   slicer(-1):  ∞ <----------|
    slicer(1):                |-----------------------------------------> 0
   slicer(-2):  ∞ <------------------------|
    slicer(2):                              |---------------------------> 0
   slicer(-3):  ∞ <--------------------------------------|
    slicer(3):                                            |-------------> 0
---------------------------------------------------------------------------
slicer(1, -2):                |<---------->|
slicer(2, -3):                              |<---------->|
slicer(1, -3):                |<------------------------>|
{% endhighlight %}

请注意没有 `slicer(0)`。

`slicer()` 只能在 `@import "rebar"` 之后才能使用，所以建议在项目中比较靠前的位置设置 Rebar。

### query-breakpoint() Function

由于 `slicer()` 只能使用 `min-width` 与 `max-width` 这两个 media feature。需要设置其他 media feature 时，可借助 `query-breakpoint()` 函数获取 breakpoint 的值。传入负的 index，这个函数将根据 `$mobile-first` 对查询的 breakpoint 进行 `-1` 或 `+1` 计算，例如：

{% highlight scss %}
/* $mobile-first: true */
/* $breakpoints:  481px 769px 993px */
@media screen and (min-device-width: query-breakpoint(1))
              and (max-device-width: query-breakpoint(-3)) {}

/* $mobile-first: false */
/* $breakpoints:  992px 768px 480px */
@media screen and (max-device-width: query-breakpoint(1))
              and (min-device-width: query-breakpoint(-3)) {}
{% endhighlight %}

编译为：

{% highlight css %}
/* $mobile-first: true */
/* $breakpoints:  481px 769px 993px */
@media screen and (min-device-width: 481px) and (max-device-width: 992px) {}

/* $mobile-first: false */
/* $breakpoints:  992px 768px 480px */
@media screen and (max-device-width: 992px) and (min-device-width: 481px) {}
{% endhighlight %}


---

## Settings

### 默认设置

Rebar 共有 4 个默认设置：`$mobile-first`, `$max-width`, `$breakpoints` 与 `$gutters`。修改默认设置可在 `@import "rebar"` 之前创建相应变量并设置新的值。

#### $mobile-first

设置 `@media query` 的方式，`true` 使用 `min-width`，`false` 使用 `max-width`，默认 `true`。

#### $max-width

设置 container 的最大宽度，默认 `1170px`。

#### $breakpoints

设置所有使用的 breakpoints，支持设置任意数量个 breakpoint，默认 `481px 769px 993px` (mobile first) 或者 `992px 768px 480px` (desktop first)。

#### $gutters

设置 gird 在每个 responsive slice 下的 gutter 宽度。默认值 `20px 20px 26px 30px` (mobile first) 或者 `30px 26px 20px 20px` (desktop first)。`$gutters` 所包含的值的数量必须比 `$breakpoints` 多一个。


### Container

Container 可使用的选项共有 5 个，都是可选项，所以最基础的 container 可以设置为：

{% highlight scss %}
$container: (
  ".container": ()
);
{% endhighlight %}

#### gutter

Container 默认没有 gutter，设置为 `true` 这个 container 将使用 `$gutters` 中定义的 gutter 值，也就是变成 full width 的 gird。

{% highlight text %}
Type:     boolean
Default:  false
Length:   single value
{% endhighlight %}

#### nested

是否作为被嵌套的 container，相当于 Bootstrap 的 `.row`。设置为 `true` 会禁用其他设置，所以请独立使用这一条设置。

{% highlight text %}
Type:     boolean
Default:  false
Length:   single value
{% endhighlight %}

#### width

设置 container 在每个 responsive slice 下的宽度。

{% highlight text %}
Type:     number (1/2 | .5 | 50% | 600px | 40em) | null
Default:  null
Length:   space separated multiple values
{% endhighlight %}

#### max-width

调整 container 的最大宽度，覆盖默认的 `$max-width`。

{% highlight text %}
Type:     number (1/2 | .5 | 50% | 600px | 40em)
Default:  null
Length:   single value
{% endhighlight %}

#### min-width

设置 container 的最小宽度。

{% highlight text %}
Type:     number (1/2 | .5 | 50% | 600px | 40em)
Default:  null
Length:   single value
{% endhighlight %}


### Grid

Grid 可使用的选项共有 8 个，也都是可选项，但请至少包含一条设置。

#### float

调整 grid 在每个 responsive slice 下的浮动方向。

{% highlight text %}
Type:     string (left | right | none) | null
Default:  left
Length:   space separated multiple values
{% endhighlight %}

#### offset-left

设置 grid 在每个 responsive slice 下左侧方向的偏移量，即 `margin-left`。当 grid 只设置了 `offset-left` 与 `offset-right` 时，其他的默认设置不会输出（比如默认浮动与 gutter 宽度），也就是可以定义为单独使用的 class。

{% highlight text %}
Type:     number (1/2 | .5 | 50% | 600px | 40em) | null
Default:  null
Length:   space separated multiple values
{% endhighlight %}

#### offset-right

设置 grid 在每个 responsive slice 下右侧方向的偏移量，即 `margin-right`。当 grid 只设置了 `offset-left` 与 `offset-right` 时，其他的默认设置不会输出（比如默认浮动与 gutter 宽度），也就是可以定义为单独使用的 class。

{% highlight text %}
Type:     number (1/2 | .5 | 50% | 600px | 40em) | null
Default:  null
Length:   space separated multiple values
{% endhighlight %}

#### gutter-left

调整 grid 在每个 responsive slice 下左侧方向的 gutter 值，即 `padding-left`。每一项都不可以省略，例如将左侧的 gutter 清零需要 `gutter-left: 0 0 0 0`。使用 `null` 表示还原为默认值。

{% highlight text %}
Type:     number (1/2 | .5 | 50% | 600px | 40em) | null
Default:  $gutters/2
Length:   space separated multiple values
{% endhighlight %}

#### gutter-right

调整 grid 在每个 responsive slice 下右侧方向的 gutter 值，即 `padding-right`。每一项都不可以省略，例如将右侧的 gutter 清零需要 `gutter-right: 0 0 0 0`。使用 `null` 表示还原为默认值。

{% highlight text %}
Type:     number (1/2 | .5 | 50% | 600px | 40em) | null
Default:  $gutters/2
Length:   space separated multiple values
{% endhighlight %}

#### max-width

设置 grid 的最大宽度。

{% highlight text %}
Type:     number (1/2 | .5 | 50% | 600px | 40em)
Default:  null
Length:   single value
{% endhighlight %}

#### container

是否将 grid 作为 container，设置为 `true` 后不输出 gutter。

{% highlight text %}
Type:     boolean
Default:  false
Length:   single value
{% endhighlight %}

---

Rebar 是一套非常灵活高效的 grid framework，可以基于项目的需求或者个人习惯定制一套 grid system，而且非常轻松地在其他项目中复用，希望能对大家的开发工作有帮助。
