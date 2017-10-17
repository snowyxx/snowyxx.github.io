---
layout: post
title:  "如何写个Sublime Text 2 插件"
date:   2016-04-27 15:05:05 +0800
categories: 笔记
tags:   [python]
---

Sublime text使用越来越多，但是我有个功能需求一直以来没有找到相应的插件。那么就自己写了个。因为我要的功能很简单，所以这个插件也不复杂。主要工作是：实现功能的python代码；添加菜单；添加快捷键。简单记录下过程：

> **注**：这里的插件是Sublime text 2的。如果是Sublime text 3，python代码略有不同，但是整个过程是一样的。

1. [Hellow world](#hellowworld)
2. [基本概念](#basicconcept)
3. [功能代码](#mycode)
4. [添加菜单](#menu)
5. [添加快捷键](#shortcut)

<a name="hellowworld"></a>

### Hellow world

打开Sublime Text，在Tools菜单点击New plugin...，将显示一个示例python代码：

``` python
import sublime, sublime_plugin

class ExampleCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        self.view.insert(edit, 0, "Hello, World!")

```

保存为py文件，文件名随意，放到包文件夹中。包文件夹：点击Preferences > Browse Packages...就找到了。可以自己新建个文件夹或者放到User文件夹中。

保存后，在Sublime text 2中使用快捷键**ctrl+`**打开Sublime Text控制台。输入命令：

    view.run_command('example')

然后就可以在Sublime text 2编辑窗口中国看到插入的`Hello, World!`。

<a name="basicconcept"></a>

### 基本概念

1. 插件存储位置：
    - Packages
    - Packages/\<pkg_name\>

2. 命令名称：命令的类名称应该是“驼峰式”的`CamelCasedPhrases`，Sublime Text把这样的类名转成“蛇形”`snake\_cased\_phrases`。要去掉类名称中的`Command`后缀。最终就是命令名称了。

3. Sublime Text有4种命令类型：
    - sublime_plugin.ApplicationCommand  很少用到
    - sublime_plugin.WindowCommand  窗口相关，window对象
    - sublime_plugin.TextCommand  文字相关，view对象，我们下面就会用到
    - sublime_plugin.EventListener   事件处理
    
    无论命令类扩展上面哪种命令，中必须实现run()方法。

4. Region选择范围，格式为：（a,b）

<a name="mycode"></a>

### 功能代码

[SOURCE](https://github.com/snowyxx/uni2str/blob/master/u2s.py)

> 很简单，不用解释太多。实现的功能是在字符串和unicode间转换。如果你了解java I18N，就回知道其作用。              
> reversed(dataRegion)： 一个小技巧，当你替换原来的文本的时候，从后向前，就是从大号的region开始，否则就乱套了。

<a name="menu"></a>

### 添加菜单

添加文件Main.sublime-menu。内容：

``` json
[
{
    "id": "edit",
    "children":
    [
        {
            "id" : "unicodetostring",
            "caption" : "Unicode To String",
            "command" : "unicode_to_string"
        },
        {
            "id" : "stringtounicode",
            "caption" : "String To Unicode",
            "command" : "string_to_unicode"
        }
    ]
}
]

```

<a name="shortcut"></a>

### 添加快捷键

添加文件Default (Linux).sublime-keymap、Default (OSX).sublime-keymap和Default (Windows).sublime-keymap。内容：

``` json
[
    { "keys": ["ctrl+shift+-"], "command": "unicode_to_string" },
    { "keys": ["ctrl+shift+="], "command": "string_to_unicode" }
]

```
---

演示：

![demo](/images/sublimePluginDemo.gif)

---

参考：                   
[官方API](https://www.sublimetext.com/docs/2/api_reference.html)                  
[官方例子](http://www.sublimetext.com/docs/plugin-examples)                  
[官方给的非官方文档](http://docs.sublimetext.info/en/sublime-text-2/reference/plugins.html)。这个很好。                               
[一个教程](http://code.tutsplus.com/tutorials/how-to-create-a-sublime-text-2-plugin--net-22685)：可以参考线程使用、发布插件等内容。


