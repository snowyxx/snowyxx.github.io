---
layout: post
title:  "Flask学习笔记 12 - 日期和时间"
date:   2016-04-19 17:08:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xiii-dates-and-times>

[SOURCE](https://github.com/snowyxx/myMicroblog)

这章讲解格式化时间。

存储的日期时间都是UTC的，对于不同的用户，使用javascipt格式UTC时间显示给客户。

这里比较有意思的是：

1. 使用了[momentjs][]这个API。
2. 使用python包了下momentjs函数，给模版引擎Jinja2调用。


### 使用momentjs

从[momentjs][]下载moment.min.js，放到ap/static/js/文件夹中。

在app/templates/base.html引用

    {% highlight javascript %}
    {% raw %}
    <script src="/static/js/moment.min.js"></script>
    {% endraw %}
    {% endhighlight %}

### 用户pyhton包momentjs

新建文件：app/momentjs.py

    {% highlight python %}
    from jinja2 import Markup
    
    class momentjs(object):
        def __init__(self, timestamp):
            self.timestamp = timestamp
    
        def render(self, format):
            return Markup("<script>\ndocument.write(moment(\"%s\").%s);\n</script>" % (self.timestamp.strftime("%Y-%m-%dT%H:%M:%S Z"), format))
    
        def format(self, fmt):
            return self.render("format(\"%s\")" % fmt)
    
        def calendar(self):
            return self.render("calendar()")
    
        def fromNow(self):
            return self.render("fromNow()")
    {% endraw %}
    {% endhighlight %}

### 把把的函数设置给模版引擎Jinja2

app/\_\_init\_\_.py

    {% highlight python %}
    {% raw %}
    from .momentjs import momentjs
    app.jinja_env.globals['momentjs'] = momentjs
    {% endraw %}
    {% endhighlight %}

### 在显示日期时间的地方使用上面的函数

app/templates/user.html

{% highlight html %}
{% raw %}
{% if user.last_seen %}
<p><em>Last seen: {{ momentjs(user.last_seen).calendar() }}</em></p>
{% endif %}
{% endraw %}
{% endhighlight %}

app/templates/post.html

{% highlight html %}
{% raw %}
<p><a href="{{ url_for('user', nickname=post.author.nickname)}}">{{ post.author.nickname }}</a> said {{ momentjs(post.timestamp).fromNow() }}:</p>
<p><strong>{{ post.body }}</strong></p>
{% endif %}
{% endraw %}
{% endhighlight %}

[momentjs]:http://momentjs.com/