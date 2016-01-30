---
layout: post
title: Sublime Text 设置
tags: 杂货箱
---

## 偏好设置

个人觉得几个必不可少的设置。

{% highlight json %}
{
  "caret_style": "solid",
  "close_windows_when_empty": true,
  "drag_text": false,
  "draw_white_space": "all",
  "folder_exclude_patterns":
  [
    ".svn",
    ".git",
    ".hg",
    "CVS",
    "node_modules",
    ".sass-cache"
  ],
  "gpu_window_buffer": true,
  "highlight_line": true,
  "overlay_scroll_bars": "enabled",
  "translate_tabs_to_spaces": true,
  "trim_trailing_white_space_on_save": true,
  "use_simple_full_screen": true,
  "wide_caret": true,
  "word_wrap": true
}
{% endhighlight %}

## 修改 Theme

以 Soda 为例，在 User 文件夹内创建名为 `Soda Light 3.sublime-theme` 或者 `Soda Dark 3.sublime-theme` 与 Theme 同名的文件，然后使用以下代码：

{% highlight json %}
[
  {
    "class": "sidebar_container",
    "content_margin": [0, 5, 1, 0]
  },
  {
    "class": "sidebar_tree",
    "indent": 10
  },
  {
    "class": "sidebar_label",
    "font.size": 13
  },
  {
    "class": "sidebar_heading",
    "font.size": 13
  },
  {
    "class": "minimap_control",
    "viewport_color": [0, 0, 0, 75]
  }
]
{% endhighlight %}

第一条调整侧边栏的内边距，第二条调整条目的缩进尺寸，第三条调整字体大小，第四条调整标题的字体尺寸，最后一条用于调整 Minimap 窗口的颜色，使用深色系的 Color Scheme 时窗口通常不容易分辨，修改这一条就可以了（rgba 格式）。其他设置可以在 Soda 文件夹内找到，选中需要修改的项目复制粘贴到这个文件里，即使 Soda 更新也不担心丢失设置。
