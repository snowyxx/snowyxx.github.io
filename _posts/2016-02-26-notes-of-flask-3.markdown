---
layout: post
title:  "Flask学习笔记 3 - 表单"
date:   2016-02-26 16:53:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-iii-web-forms>

[SOURCE](https://github.com/snowyxx/myMicroblog)

使用的表单模块[Flask-WTF](http://pythonhosted.org/Flask-WTF/)是对[WTForms](http://wtforms.readthedocs.org/en/latest/)的封装。

Flask-WTF配置写在根目录的config.py中：

    {% highlight python %}
    {% raw %}
    #!/usr/bin/env python

    WTF_CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'

    OPENID_PROVIDERS = [
        {'name': 'Google', 'url': 'https://www.google.com/accounts/o8/id'},
        {'name': 'Yahoo', 'url': 'https://me.yahoo.com'},
        {'name': 'AOL', 'url': 'http://openid.aol.com/<username>'},
        {'name': 'Flickr', 'url': 'http://www.flickr.com/<username>'},
        {'name': 'MyOpenID', 'url': 'https://www.myopenid.com'}]
    {% endraw %}
    {% endhighlight %}

>  OPENID_PROVIDERS是用于后面的openid

在app/\_\_init\_\_.py中导入上面的设置

    {% highlight python %}
    {% raw %}
    from flask import Flask
    
    app = Flask(__name__)
    app.config.from_object('config')
    from app import views
    {% endraw %}
    {% endhighlight %}


定义表单类app/forms.py

    {% highlight python %}
    {% raw %}
    #!/usr/bin/env python
    
    from flask.ext.wtf import Form
    from wtforms import StringField, BooleanField
    from wtforms.validators import DataRequired
    
    class LoginForm(Form):
        openid = StringField('openid', validators=[DataRequired()])
        remember_me = BooleanField('remember_me', default=False)
    {% endraw %}
    {% endhighlight %}

登录页面的模版app/templates/login.html

    {% highlight html %}
    {% raw %}
    {% extends "base.html" %}
    {% block content %}
        <script type="text/javascript" charset="utf-8">
            function set_openid(openid, pr){
                u = openid.search('<username>')
                if (u != -1){
                    user = prompt('Enter your '+pr+' username:' )
                    openid = openid.substr(0,u)+user
                }
                form = document.forms['login'];
                form.elements['openid'].value = openid
            }
        </script>
        <h1>Sign In</h1>
        <form action="" method="post" accept-charset="utf-8" name="login">
            {{ form.hidden_tag() }}
            <p>
                Please enter your OpenID: <br>
                {{ form.openid(size=80) }} <br>
                {% for error in form.openid.errors %}
                    <span style="color: red;">[{{ error }}]</span>
                {% endfor %}<br>
                |{% for pr in providers %}
                    <a href="javascript:set_openid('{{ pr.url }}','{{ pr.name }}');" >{{ pr.name }}</a> |
                {% endfor %}
            </p>
            <p>{{ form.remember_me }} Remember Me</p>
            <p><input type="submit" value="Sign In"></p>
        </form>
    {% endblock %}
    {% endraw %}
    {% endhighlight %}

> javascript用于处理openid 

> form.hidden_tag() 用于添加隐藏的表单字段，例如这里会有csrf相关字段

设置登录页面路由app/views.py

    {% highlight python %}
    {% raw %}
    # coding:utf-8
    from app import app
    from flask import render_template, flash, redirect
    from .forms import LoginForm
    
    
    @app.route('/')
    @app.route('/index')
    def index():
        user = {'nickname': 'Snow'}  # fake user
        posts = [  # fake blog
            {
                'author': {'nickname': u'律香川'},
                'body': u'蛋炒饭要么？'
            },
            {
                'author': {'nickname': u'郭大路'},
                'body': u'你太像个女孩了！'
            }
        ]
        return render_template('index.html', title='Home', user=user, posts=posts)
    
    
    @app.route('/login', methods=['GET', 'POST'])
    def login():
        form = LoginForm()
        if form.validate_on_submit():
            flash('Lgin requested for OpenID="%s", remember_me=%s' % (form.openid.data, str(form.remember_me.data)))
            return redirect('/index')
        return render_template('login.html', title='Sign In', form=form, providers=app.config['OPENID_PROVIDERS'])
    
    {% endraw %}
    {% endhighlight %}

> flash发送通知，在后面的base.html中定义显示这种通知的div

> redirect 重定向

基本模版显示通知app/templates/base.html

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
        <div> Microblog: <a href="/index">Home</a></div>
        <hr>
        {% with messages = get_flashed_messages() %}
            {% if messages %}
                <ul>
                    {% for message in messages %}
                        <li>{{ message }}</li>
                    {% endfor %}
                </ul>
            {% endif %}
        {% endwith %}    
        {% block content %}{% endblock %}
    </body>
    </html>
    {% endraw %}
    {% endhighlight %}
