---
layout: post
title: 如何管理 Sass 文件结构
tags: Sass
---

[John W.Long](http://wiseheartdesign.com) 在 [The Sass Way](http://thesassway.com/beginner/how-to-structure-a-sass-project) 分享了他管理 Sass 项目的一些经验心得，简单翻译了一下以便深入学习理解。

## 文件结构

{% highlight raw %}
stylesheets/
|
|-- modules/              # Common modules
|   |-- _all.scss         # Include to get all modules
|   |-- _utility.scss     # Module name
|   |-- _colors.scss      # Etc...
|   ...
|
|-- partials/             # Partials
|   |-- _base.sass        # imports for all mixins + global project variables
|   |-- _buttons.scss     # buttons
|   |-- _figures.scss     # figures
|   |-- _grids.scss       # grids
|   |-- _typography.scss  # typography
|   |-- _reset.scss       # reset
|   ...
|
|-- vendor/               # CSS or Sass from other projects
|   |-- _colorpicker.scss
|   |-- _jquery.ui.core.scss
|   ...
|
`-- main.scss             # primary Sass file
{% endhighlight %}

## 主样式文件

{% highlight scss %}
// Modules and Variables
@import "partials/base";

// Partials
@import "partials/reset";
@import "partials/typography";
@import "partials/buttons";
@import "partials/figures";
@import "partials/grids";
// ...

// Third-party
@import "vendor/colorpicker";
@import "vendor/jquery.ui.core";
{% endhighlight %}

## Modules, partials, and vendor

模块 (modules) 包含不直接输出为 CSS 的 Sass 样式，比如 mixin, function, and variables 等。

Partials 包含主样式，很多人喜欢根据 header, content, sidebar, footer 等管理文件，不过作者更偏向于通过 categories 管理，比如 typography, buttons, textboxes, selectboxes 等等。

Vendor 包括第三方 CSS 样式，例如 jQuery UI，colorpicker，lightbox 等插件，不建议直接修改这部分，而是在主文件中 override 需要修改的样式，方便以后更新。

## 载入样式 (Using a base partial)

{% highlight scss %}
// Use Compass ('cause it rocks!)
@import "compass";

// Font weights
$light: 100;
$regular: 400;
$bold: 600;

// Base Font
$base-font-family: sans-serif;
$base-font-weight: $regular;
$base-font-size: 13px;
$base-line-height: 1.4;

// Fixed Font
$fixed-font-family: monospace;
$fixed-font-size: 85%;
$fixed-line-height: $base-line-height;

// Headings
$header-font-weight: $bold;

@import "modules/all";
{% endhighlight %}

刚刚提到 modules 应该包含不直接输出为 CSS 的 Sass 样式，创建一个 base 文件载入全局变量，modules 等，可以方便地在任何一个文件中使用全局样式，或者在一个大项目中整理不同的样式模板。

## 更进一步

假如一个 Rails app 中包含多个子项目，可以将每个子项目分别放入同级文件夹中，完整的项目结构看起来像下面这样：

{% highlight raw %}
stylesheets/
|
|-- admin/           # Admin sub-project
|   |-- modules/
|   |-- partials/
|   `-- _base.scss
|
|-- account/         # Account sub-project
|   |-- modules/
|   |-- partials/
|   `-- _base.scss
|
|-- site/            # Site sub-project
|   |-- modules/
|   |-- partials/
|   `-- _base.scss
|
|-- vendor/          # CSS or Sass from other projects
|   |-- _colorpicker-1.1.scss
|   |-- _jquery.ui.core-1.9.1.scss
|   ...
|
|-- admin.scss       # Primary stylesheets for each project
|-- account.scss
`-- site.scss
{% endhighlight %}

每个子项目都拥有独立的 主样式文件，modules，partials 等等，而第三方样式统一存入同一个目录。这对大型 Sass 项目来说，是一种非常有效的管理方式。
