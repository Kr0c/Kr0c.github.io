---
layout: post
title: 关注 CSS 性能 (二)
tags: CSS
---

如何写 CSS 令浏览器的执行效率最高呢？翻译一篇 Chris Coyier 的 [Efficiently Rendering CSS](http://css-tricks.com/efficiently-rendering-css/) 帮助理清思路。此外，Google 也有一篇更详细的讲解文章 [Optimize browser rendering](https://developers.google.com/speed/docs/best-practices/rendering)。

第一，浏览器读 CSS 的顺序是**从右往左**，比如  `ul > li a[title="home"]`，最先被读到的第一部分是 `a[title="home"]`。

第二，效率由高到低的选择器依次是 ID, class, tag, universal ：

{% highlight css %}
#main-navigation {   }      /* ID (Fastest) */
body.home #page-wrap {   }  /* ID */
.main-navigation {   }      /* Class */
ul li a.current {   }       /* Class *
ul {   }                    /* Tag */
ul li a {  }                /* Tag */
* {   }                     /* Universal (Slowest) */
#content [title='home']     /* Universal */
{% endhighlight %}

因为浏览器从右往左读，`#main-nav > li {}` 这一句即使用到了 id 选择器，实际效率却不会提升多少。

第三，不需要这样写 `ul#main-navigation {}`，因为 ID 已经是独一无二的了，同理，tag 也要尽可能地避免与 class 一同使用。

第四，后代选择器是性能最差的，尤其是后代中包含 tag 或者 universal 选择器，比如 `html body ul li a {}` 简直是一场灾难。

第五，浏览器如果发现某个选择器不能匹配任何元素，将会立即终止尝试，开始匹配下一个选择器，从而提高执行效率。

第六，重新思考一下为什么要这样写，是不是还有优化的可能呢？比如利用元素的继承性可以省下很多不必要的设定。

第七，如果很在意性能，尽量少用 CSS3 选择器。
