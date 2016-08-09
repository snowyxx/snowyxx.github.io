---
layout: post
title:  "web.py+apache的微信公众号服务器"
date:   2016-08-09 13:49:05 +0800
categories: blog
tags:   [python]
---

* 目录
{:toc}

很久之前就注册了个微信公众号，可是一直都没有用起来。所以就想弄个服务器端实现些功能。最终的选择是用web.py加Apache虚拟机的方式部署在一个现有的外网服务器上。

### 微信公众号

首先你得有个注册公众号。注册地址<https://mp.weixin.qq.com/>。微信公众号分为“服务号”和“订阅号”。服务号功能更多。而且二者都有认证和非认证之分。认证的功能更多。所以说，非认证的个人订阅号功能最少。

[公众号接口权限说明](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1433401084&token=&lang=zh_CN)

### 微信公众号开发信息

登录微信公众平台 > 开发 > 基本配置中有我们需要或配置的一些信息：

1. AppID(应用ID)：系统给你的。
2. AppSecret(应用密钥)：后面生成access_token用。
3. 服务器配置
    - URL(服务器地址)：服务器地址。http或https；域名或者外网地址；http必需是80端口，https必需是443端口。
    - Token(令牌)：Token可由开发者可以任意填写，用作生成签名。作用就是验证请求是不是来自微信。
    - EncodingAESKey(消息加解密密钥：EncodingAESKey由开发者手动填写或随机生成，将用作消息体加解密密钥。
    - 消息加解密方式：明文模式、兼容模式和安全模式。模式的选择与服务器配置在提交后都会立即生效，请开发者谨慎填写及选择。加解密方式的默认状态为明文模式，选择兼容模式和安全模式需要提前配置好相关加解密代码。

### 微信公众号开发接入

配置服务器的时候，微信会向你配置的URL(服务器地址)发送一个**GET**请求，主要目的是验证签名。

包括以下参数：

|参数|参数|
| ---- | ---- |
|signature|微信加密签名，signature结合了开发者填写的token参数和请求中的timestamp参数、nonce参数。|
|timestamp|时间戳|
|nonce|随机数|
|echostr|随机字符串|

收到该请求后需要以下步骤完成接入验证：

1. 将token、timestamp、nonce三个参数进行字典序排序
2. 将三个参数字符串拼接成一个字符串进行sha1加密
3. 开发者获得加密后的字符串可与signature对比，返回微信后台传过来的echostr

web.py处理该GET请求代码示例：

```python
def GET(self):
    data = web.input()
    signature = data.signature
    timestamp = data.timestamp
    nonce = data.nonce
    echostr = data.echostr
    token = "snowyxx"
    list = [token, timestamp, nonce]
    list.sort()
    sha1 = hashlib.sha1()
    map(sha1.update, list)
    hashcode = sha1.hexdigest()
    if hashcode == signature:
        return echostr
```

### 接收消息和被动回复

关注者发到公众号的信息将通过**POST**请求发给你配置的URL(服务器地址)

该请求带有signature、timestamp、nonce这3个参数。

请求的数据为xml格式，如下

```xml
<xml>
    <ToUserName><![CDATA[toUser]]></ToUserName>
    <FromUserName><![CDATA[fromUser]]></FromUserName> 
    <CreateTime>1348831860</CreateTime>
    <MsgType><![CDATA[text]]></MsgType>
    <Content><![CDATA[this is a test]]></Content>
    <MsgId>1234567890123456</MsgId>
</xml>
```

简单解析该xml如下：

```python
def POST(self):
    str_xml = web.data()
    domTree = xml.dom.minidom.parseString(str_xml)
    collection = domTree.documentElement
    ToUserName = collection.getElementsByTagName('ToUserName')[0].childNodes[0].data
    FromUserName = collection.getElementsByTagName('FromUserName')[0].childNodes[0].data
    MsgType = collection.getElementsByTagName('MsgType')[0].childNodes[0].data
    Content = collection.getElementsByTagName('Content')[0].childNodes[0].data
    print ToUserName, FromUserName, MsgType, Content
```

我们的服务器解析该xml，然后通过下面格式的xml反给微信。

```xml
<xml>
    <ToUserName><![CDATA[toUser]]></ToUserName>
    <FromUserName><![CDATA[fromUser]]></FromUserName>
    <CreateTime>12345678</CreateTime>
    <MsgType><![CDATA[text]]></MsgType>
    <Content><![CDATA[你好]]></Content>
</xml>
```

响应中的ToUserName和FromUserName就是请求中的二者调换位置；CreateTime是以秒为单位的UNIX时间戳。

在web.py中使用模板的方法如下：

```python
 return render.wx(ToUserName, FromUserName, int(time.time()), answer)
```

对应模板文件templates\wx.xml

```python
 $def with (toUser,fromUser,createTime,content,funcFlag=0)
<xml>
<ToUserName><![CDATA[$fromUser]]></ToUserName>
<FromUserName><![CDATA[$toUser]]></FromUserName>
<CreateTime>$createTime</CreateTime>
<MsgType><![CDATA[text]]></MsgType>
<Content><![CDATA[$content]]></Content>
<FuncFlag>$funcFlag</FuncFlag>
</xml>
```

### 调用公众号API

调用API，获取access token

接口为：

    http请求方式: GET
    https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=APPID&secret=APPSECRET

返回ACCESS_TOKEN及expires_in。expires_in为7200，表示2小时过期。因为每个接口调用有每天数限制。获取access token每天限制2000次。所以要保存获取的access token，在过期前再重新获取。

之后的所有API调用都要用到该ACCESS_TOKEN。

[详细的API列表](https://mp.weixin.qq.com/wiki)。

开发者工具:

1. 在线接口调试工具：在线测试API及其应答。
2. 公众平台测试帐号：测试用公众号。

### web.py

选择[web.py](http://webpy.org/cookbook/)是因为其轻。用到的功能：

1. 路由

    urls = (
        "/.*", "hello"
    )

    表示访问`/.*`有hello类处理。

2. 处理GET和POST请求。在类中实现`GET(self)`和`POST(self)`方法。代码如上。

3. 模板，以下代码用来使用templates\wx.xml模版

    render = web.template.render('templates/')
    return render.wx(ToUserName, FromUserName, int(time.time()), answer)

### web.py ＋ Apache

因为已有个外网服务器通过Apache虚拟主机来运行几个网站。下面的步骤就是添加一个新的虚拟主机作为我的微信服务器。

**虚拟主机配置**

apache主目录\conf\extra\httpd-vhosts.conf  ([apache 2.4](http://httpd.apache.org/docs/2.4/upgrading.html))

    <VirtualHost *:80>
        AddHandler cgi-script .cgi .pl .py
        ServerAdmin webmaster@dummy-host.example.com
        DocumentRoot "d:/wamp/www/weixin/"
        ServerName localhost
        ServerAlias localhost
        Alias /static "d:/wamp/www/weixin/static"
        ScriptAlias / "d:/wamp/www/weixin/test.py"
        <Directory />
        Options +ExecCGI +FollowSymLinks -Indexes
            Require all granted
        </Directory>

        # because Alias can be used to reference resources outside docroot, you
        # must reference the directory with an absolute path
        <Directory /static>
            # directives to effect the static directory
            Options +Indexes
        </Directory>
    </VirtualHost>

因为这个Apache来自wamp（一个Windows下的php＋apache＋mysql＋apache集成环境），已经使用CGI模块启用PHP。所以也用CGI来启动web.py。

[web.py文档中CGI部署配置说明](http://webpy.org/cookbook/cgi-apache)

遇到的2个坑：

1. `D:/wamp/www/weixin/test.py is not executable; ensure interpreted scripts have "#!" or "'!" first line`

    解决：必需在test.py第一行指定pyton地址。`#!C:\Python27\python.exe`

2. 例外

    File "D:/wamp/www/weixin/test.py", line 18, in <module>\r
     app.run()\r
    File "C:\\Python27\\lib\\site-packages\\web\\application.py", line 313, in run\r
     return wsgi.runwsgi(self.wsgifunc(*middleware))\r
    File "C:\\Python27\\lib\\site-packages\\web\\wsgi.py", line 35, in runwsgi\r
     return runfcgi(func, None)\r
    File "C:\\Python27\\lib\\site-packages\\web\\wsgi.py", line 17, in runfcgi\r
     return flups.WSGIServer(func, multiplexed=True, bindAddress=addr, debug=False).run()\r
    File "C:\\Python27\\lib\\site-packages\\flup\\server\\fcgi.py", line 112, in run\r
     sock = self._setupSocket()\r
    File "C:\\Python27\\lib\\site-packages\\flup\\server\\fcgi_base.py", line 978, in _setupSocket\r
     sock = socket.fromfd(FCGI\_LISTENSOCK\_FILENO, socket.AF_INET,\r
    AttributeError: 'module' object has no attribute 'fromfd'\r
    
    因为Windows中没有fromfd命令。[解决方法](https://groups.google.com/forum/#!msg/webpy/Z6jPpf2xiCs/fWXAkJzAGgMJ)：      

    注释Python27\\lib\\site-packages\\flup\\server\\fcgi_base.py 978行左右的代码
    
            sock = socket.fromfd(FCGI_LISTENSOCK_FILENO, socket.AF_INET,
                                 socket.SOCK_STREAM)
            try:
                sock.getpeername()
            except socket.error, e:
                if e[0] == errno.ENOTSOCK:
                    # Not a socket, assume CGI context.
                    isFCGI = False
                elif e[0] != errno.ENOTCONN:
                    raise
            在其下添加：
            isFCGI = False
    
---

参考：                                   
[廖雪峰的教程](http://www.liaoxuefeng.com/article/0013900476318564121d01facf844cba508396f95d9bb82000)                 
[知乎上的回答](https://zhuanlan.zhihu.com/p/21284127)                 
[wechat-python-sdk](https://github.com/doraemonext/wechat-python-sdk)                 
[微信文档](https://mp.weixin.qq.com/wiki)                 

