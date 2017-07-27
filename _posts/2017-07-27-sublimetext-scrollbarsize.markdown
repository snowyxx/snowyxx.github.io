---
layout: post
title:  "更改Sublime Text滚动条大小"
date:   2017-07-27 15:50:05 +0800
categories: blog
tags:   [other]
---

年龄大了，眼神也不好使了。

再加上公司的显示器--呵呵--

Sublime Text打开个几百行的文件后，要频繁地使用垂直和水平滚动条来编辑文件。问题是默认的滚动条太窄（小）了，颜色也不清楚，明显欺负老年人啊。

变大变大————

__步骤__ ：

1. 菜单`Preferences -> Browse Packages...` , 在打开的文件夹中，打开`User`新建一个名称为`Theme - Default`文件夹。
2. 创建一个新的文件，命名为`Default.sublime-theme`，打开，输入如下内容：

```js
[
    // More visible scrollbar
    {
        "class": "puck_control",
        "layer0.texture": "User/Theme - Default/vertical_white_scrollbar.png",
        "content_margin": [16, 4],
        // Adjust RGB color. Optional: comment the following line (or set 255,255,255) to not modify image color
        "layer0.tint": [10, 210, 80]
    },
    {
        "class": "puck_control",
        "attributes": ["horizontal"],
        "layer0.texture": "User/Theme - Default/h_white_scrollbar.png",
        "content_margin": [4, 16],
        "layer0.tint": [10, 210, 80]
    },
    {
    "class": "tab_label",
    "parents": [{"class": "tab_control", "attributes": ["selected"]}],
    //"fg": [30,30,30],
    "fg": [255,131,0] //change highlighted tab color
    }
]
```

3. 把![demo](/images/vertical_white_scrollbar.png)和![demo](/images/h_white_scrollbar.png)复制到`Theme - Default`文件夹。就可以了。 

也可以直接把<a href="/images/Theme - Default.zip">Theme - Default.zip</a>解压缩到你的`User`文件夹，里面已经有编辑好的`Default.sublime-theme`文件和这2个图片文件。

效果如下：

![demo](/images/sublime_scroll.png)

---

参考：                                   

- <https://stackoverflow.com/questions/31093497/how-to-make-scroll-bar-indicator-more-clear-and-easy-to-see>