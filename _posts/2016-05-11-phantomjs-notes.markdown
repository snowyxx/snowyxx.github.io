---
layout: post
title:  "Phantomjs笔记 - 基础知识"
date:   2016-05-11 13:45:05 +0800
categories: blog
tags:   [phantomjs]
---

* 目录
{:toc}

### phantomjs

[phantomjs]是一个基于WebKit的服务器端JavaScript API，它无需浏览器的支持即可实现对Web的支持，且原生支持各种Web标准。主要用于：

1. 无需浏览器的Web测试。

2. 页面自动化操作：使用标准的DOM API或一些JavaScript框架（如jQuery）访问和操作Web页面。

3. 屏幕捕获。

4. 网络监控：自动进行网络性能监控、跟踪页面加载情况以及将相关监控的信息以标准的HAR格式导出。

### 安装

从[下载页面](http://phantomjs.org/download.html)下载安装。支持Windows，Linux和Mac。

下载包中的example文件夹中的[例子](http://phantomjs.org/examples/index.html)很适合学习和参考。
    
Mac可以直接使用homebrew安装：

    brew install phantomjs

### API

<http://phantomjs.org/api/>

### 入门

把phantomjs代码保存到扩展名为js的文件中，然后运行如下命令执行：

    phantomjs xxx.js

下面是一些例子来认识和学习phantomjs：

#### 打开页面和截图

```js
var page = require('webpage').create();

//viewportSize being the actual size of the headless browser
page.viewportSize = { width: 1024, height: 768 };
//the clipRect is the portion of the page you are taking a screenshot of
page.clipRect = { top: 0, left: 0, width: 1024, height: 768 };

page.open('http://xiaoxuenotes.com',function(status){
    console.log("Status: " + status);
    if(status === "success"){
        page.render('xiaoxuenotes.png');
    }
    phantom.exit();
});
```

#### 页面加载速度

```js
var page = require('webpage').create(),
    system = require('system'),
    t, address;

if (system.args.length === 1){
    console.log('Usage: loadspeed.js <some URL>');
    phantom.exit();
}

t = Date.now();
address = system.args[1];

page.open(address, function(status){
    if(status !== 'success'){
        console.log('FAIL to load the address');
    }else{
        t = Date.now() - t;
        console.log('Loading ' + system.args[1]);
        console.log('Loading time ' + t + ' msec');
    }
    phantom.exit();
});

```

#### 执行页面中的javascript

```js
var page =  require('webpage').create();
page.open('http://xiaoxuenotes.com', function(status){
    var title = page.evaluate(function(){
        return document.title;
    });
    console.log('Page title is  ' + title);
    phantom.exit();
});
```

#### 输出页面中的console信息。可以用类似的方法输出alert等信息。`onConsoleMessage` `onAlert`等。

[webpageapi]

```js
var page = require('webpage').create();
page.onConsoleMessage = function(msg){
    console.log('Page title is ' + msg);
};
page.open('http://xiaoxuenotes.com',function(status){
    page.evaluate(function(){
        console.log(document.title);
    });
    phantom.exit();
});
```

#### 网络资源等请求和响应

[webpageapi]

```js
var page = require('webpage').create();
page.onResourceRequested =  function(request){
    console.log('Request '+ JSON.stringify(request, undefined,4));
};
page.onResourceReceived = function(response){
    console.log('Received ' + JSON.stringify(response, undefined, 4));
};
page.open('http://xiaoxuenotes.com');
```

example中的[netsniff.js]可以输出[HAR]格式的结果。然后使用[HAR viewer]生成瀑布图表。


#### DOM

```js
var page = require('webpage').create();
console.log('The default user agent is ' + page.settings.userAgent);
page.settings.userAgent = 'SpecialAgent';
page.open('http://www.httpuseragent.org', function(status){
    if (status  !== 'success'){
        console.log('Unable to access network');
    }else{
        var ua = page.evaluate(function(){
            return document.getElementById('myagent').textContent;
        });
        console.log(ua);
    }
    phantom.exit();
});
```

#### 使用jquery

```js
var page = require('webpage').create();
page.onConsoleMessage = function(msg){
    console.log(msg);
};
page.open('http://xiaoxuenotes.com', function() {
  page.includeJs("http://ajax.googleapis.com/ajax/libs/jquery/1.6.1/jquery.min.js", function() {
    page.evaluate(function() {
      console.log($("li").text());
    });
    phantom.exit();
  });
});
```

#### 文件IO

[fsapi]

```js
var fs = require('fs');
var stream = fs.open('test.js','r');
while(!stream.atEnd()){
    console.log(stream.readLine());
}
```

#### 向页面中注入js

    page.injectJs("injectme.js")

example中的[injectme.js]
[fsapi]

```js
var fs = require('fs');
var stream = fs.open('test.js','r');
while(!stream.atEnd()){
    console.log(stream.readLine());
}
```

#### phantomjs的自带web服务器

[example](http://phantomjs.org/examples/index.html#network)

```js
var page = require('webpage').create(),
    server = require('webserver').create(),
    system = require('system'),
    data = 'universe=expanding&answer=42';

if (system.args.length !== 2) {
    console.log('Usage: postserver.js <portnumber>');
    phantom.exit(1);
}

var port = system.args[1];

var service = server.listen(port, function (request, response) {
    console.log('Request received at ' + new Date());

    response.statusCode = 200;
    response.headers = {
        'Cache': 'no-cache',
        'Content-Type': 'text/plain;charset=utf-8'
    };
    response.write(JSON.stringify(request, null, 4));
    response.close();
});

page.open('http://localhost:' + port + '/', 'post', data, function (status) {
    if (status !== 'success') {
        console.log('Unable to post!');
    } else {
        console.log(page.plainText);
    }
    phantom.exit();
});
```

[phantomjs文档页面](http://phantomjs.org/documentation/)

### Selenium和phantomjs

Selenium和phantomjs和shell命令，如wget和curl，不同的地方是他们回真的“渲染”页面。phantomjs和Selenium不同地方是“headless”。在不需要图形界面的情况下可以使用。而且没有运行真的浏览器消耗的时间。

Selenium的好处是支持各种“真的浏览器”；支持编程语言众多。

#### 使用Selenium运行phantomjs

```python
from selenium import webdriver
driver = webdriver.PhantomJS(executable_path='/Users/yan/Downloads/phantomjs-2.1.1-macosx/bin/phantomjs')
driver.get('http://cn.bing.com')
print  driver.find_element_by_id('scpl0').text
```

[netsniff.js]:https://github.com/ariya/phantomjs/blob/master/examples/netsniff.js
[injectme.js]:https://github.com/ariya/phantomjs/blob/master/examples/injectme.js
[webpageapi]:http://phantomjs.org/api/webpage/
[HAR]:http://www.softwareishard.com/blog/har-12-spec
[HAR viewer]:http://www.softwareishard.com/blog/har-viewer
[fsapi]:http://phantomjs.org/api/fs/

