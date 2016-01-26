---
layout: post
title: Symphony CMS debug bookmarklet
---

最近一个星期把时间都花在 Symphony CMS 上了，经常需要查看 `?debug` 页面，所以写了个 bookmarklet 一键切换：

{% highlight js %}
javascript:(function() {
  var link = location.toString();
  if (link.slice(-6) != "?debug") {
    location.href = link + "?debug";
  } else {
    location.href = link.substring(0,link.length-6);
  }
})();
{% endhighlight %}

使用方法：将这个链接 <a href="javascript:(function(){var e=location.toString();if(e.slice(-6)!='?debug'){location.href=e+'?debug'}else{location.href=e.substring(0,e.length-6)}})()">?debug</a> 拖拽到浏览器收藏夹，或者新建 bookmark，拷贝下面的 code 作为地址。

{% highlight js %}
javascript:(function(){var%20e=location.toString();if(e.slice(-6)!="?debug"){location.href=e+"?debug"}else{location.href=e.substring(0,e.length-6)}})()
{% endhighlight %}

关于什么是 Bookmarklet？相信看完下面两篇文章就木有问题了：

* [Bookmarklet 编写指南](http://www.ruanyifeng.com/blog/2011/06/a_guide_for_writing_bookmarklet.html)
* [Create Bookmarklets – The Right Way](http://net.tutsplus.com/tutorials/javascript-ajax/create-bookmarklets-the-right-way/)
