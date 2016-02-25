---
layout: post
title:  "Flask学习笔记 2 - 模板"
date:   2016-02-25 14:02:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-ii-templates>

### Python模板引擎<http://jinja.pocoo.org/>

### 相关文件

base.html

    {% highlight html %}
    {% raw %}
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        {% if title %}
            <title>{{ title }} microblog</title>
        {% else %}
            <title>Welcome to microblog</title>
        {% endif %}
    </head>
    <body>
        {% block content %}
        {% endblock %}
    </body>
    </html>
    {% endraw %}
    {% endhighlight %}

index.html

    {% highlight html %}
    {% raw %}
    {% extends "base.html" %}
    {% block content %}
        <h1>Hello, {{ user.nickname }}!!</h1>
        {% for post in posts %}
            <div><p>{{ post.author.nickname }} says: <b>{{ post.body }}</b></p></div>
        {% endfor %}
    {% endblock %}
    {% endraw %}
    {% endhighlight %}


views.py

    {% highlight python %}
    #coding:utf-8
    from app import app
    from flask import render_template
    
    
    @app.route('/')
    @app.route('/index')
    def index():
        user = {'nickname': 'Snow'} #fake user
        posts = [ #fake blog
            {
                'author' : {'nickname' : u'律香川'},
                'body' : u'蛋炒饭要么？'
            },
            {
                'author' : {'nickname' : u'郭大路'},
                'body' : u'你太像个女孩了！'
            }
        ]
        return render_template('index.html', title='Home', user=user, posts = posts)
    {% endhighlight %}


### 模板应用

1. 模板if语句

        {% highlight html %}
        {% raw %}
        <head>
        {% if title %}
        <title>{{ title }} - microblog</title>
        {% else %}
        <title>Welcome to microblog</title>
        {% endif %}
        </head>
        {% endraw %}
        {% endhighlight %}

2. 模板循环语句

        {% highlight html %}
        {% raw %}
        {% for post in posts %}
            <div><p>{{ post.author.nickname }} says: <b>{{ post.body }}</b></p></div>
        {% endfor %}
        {% endraw %}
        {% endhighlight %}

3. 模板继承
        
        {% highlight html %}
        {% raw %}
        {% extends "base.html" %}
        {% block content %}
            <h1>Hello, {{ user.nickname }}!!</h1>
            {% for post in posts %}
                <div><p>{{ post.author.nickname }} says: <b>{{ post.body }}</b></p></div>
            {% endfor %}
        {% endblock %}
        {% endraw %}
        {% endhighlight %}
        
### [模板设计](http://jinja.pocoo.org/docs/dev/templates/)

1. 几种符号定义

    + {% raw %}{% ... %} {% endraw %}语句
    + {% raw %}{{ ... }} {% endraw %}输出表达式
    + {% raw %}{# ... #} {% endraw %}注释
    + \#  ... \#\#  行语句

2. 变量

    {% raw %}
    {{ foo.bar }}
    {{ foo['bar'] }}
    {% endraw %}

3. 过滤器 `{% raw %}{{ name|striptags|title }}{% endraw %}`

    内置过滤器<http://jinja.pocoo.org/docs/dev/templates/#builtin-filters>

4. “测试器” `{% raw %}{% if name is defined %}{% endraw %}`

    内置测试器<http://jinja.pocoo.org/docs/dev/templates/#builtin-tests>

5. 转义 raw

6. 继承

    * {% raw %}{% block title %}{% endblock %}{% endraw %}
    * {% raw %}{% extends "base.html" %}{% endraw %}
    * {% raw %} {{ super() }}{% endraw %}

7. 控制语句

    * for： {% raw %}{% for key, value in my_dict.iteritems() %}{% endraw %}
    * if： {% raw %}{% for user in users %}{% endraw %}
    * 宏：

            {% raw %}
            {% macro input(name, value='', type='text', size=20) -%}
            <input type="{{ type }}" name="{{ name }}" value="{{
            value|e }}" size="{{ size }}">
            {%- endmacro %}
            {% endraw %}

        像函数一样调用：

            {% raw %}
            <p>{{ input('username') }}</p>
            <p>{{ input('password', type='password') }}</p>
            {% endraw %}

8. 包含

        {% raw %}
        {% include 'header.html' %}
        Body
        {% include 'footer.html' %}
        {% endraw %}

9. 计算 +,-,*,/...
