---
layout: post
title:  "Flask学习笔记 5 - 登录"
date:   2016-03-04 09:08:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-v-user-logins>

[SOURCE](https://github.com/snowyxx/microblog)

> openid似乎没有推广起来。只有几个国外网站支持。而且大部分需要搭梯子。 

这次使用登录模块`Flask-Login`和`Flask-OpenID`。配置如下app/\_\_init\_\_.py

    {% highlight python %}
    {% raw %}
    import os
    from flask.ext.login import LoginManager
    from flask.ext.openid import OpenID
    from config import basedir
    
    lm = LoginManager()
    lm.init_app(app)
    oid = OpenID(app, os.path.join(basedir, 'tmp'))   
    {% endraw %}
    {% endhighlight %}

User类需要实现一些Flask-Login需要的属性和方法app/models.py。

    {% highlight python %}
    {% raw %}
    class User(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        nickname = db.Column(db.String(64), index=True, unique=True)
        email = db.Column(db.String(120), index=True, unique=True)
        posts = db.relationship('Post', backref='author', lazy='dynamic')
        
        @property
        def is_authenticated(self):
            return True
        
        @property
        def is_active(self):
            return True
        
        @property
        def is_anonymous(self):
            return False
        
        def get_id(self):
            try:
                return unicode(self.id)  # python 2
            except NameError:
                return str(self.id)  # python 3
        
        def __repr__(self):
            return '<User %r>' % (self.nickname
    {% endraw %}
    {% endhighlight %}

Flask-Login需要一个从数据库获取用户的函数app/views.py：

    {% highlight python %}
    {% raw %}
    @lm.user_loader
    def load_user(id):
        return User.query.get(int(id))
    {% endraw %}
    {% endhighlight %}

执行登录的地方app/views.py：

    {% highlight python %}
    {% raw %}
    from flask import render_template, flash, redirect, session, url_for, request, g
    from flask.ext.login import login_user, logout_user, current_user, login_required
    from app import app, db, lm, oid
    from .forms import LoginForm
    from .models import User
    
    @app.route('/login', methods=['GET', 'POST'])
    @oid.loginhandler
    def login():
        if g.user is not None and g.user.is_authenticated:
            return redirect(url_for('index'))
        form = LoginForm()
        if form.validate_on_submit():
            session['remember_me'] = form.remember_me.data
            return oid.try_login(form.openid.data, ask_for=['nickname', 'email'])
        return render_template('login.html', 
                               title='Sign In',
                               form=form,
                               providers=app.config['OPENID_PROVIDERS'])
    {% endraw %}
    {% endhighlight %}

> The oid.loginhandler tells Flask-OpenID that this is our login view function.

> __g__是Flask在request范围内的全局变量。那么，登录后再有请求的时候，新的reqeust怎么获取登录的用户信息呢？方法是通过flask的before_request事件把Flask-Login的current_user全局变量赋值给g.user

        @app.before_request
        def before_request():
        g.user = current_user        

> __url_for__是flask的函数，用来获取指定view️函数的url

> session[]是flask的会话。注意区别后面的db.session

> try_login是实现openid登录的地方。

Flask-OpenID的回调函数实现after_login。app/views.py：

    {% highlight python %}
    {% raw %}
    @oid.after_login
    def after_login(resp):
        if resp.email is None or resp.email == "":
            flash('Invalid login. Please try again.')
            return redirect(url_for('login'))
        user = User.query.filter_by(email=resp.email).first()
        if user is None:
            nickname = resp.nickname
            if nickname is None or nickname == "":
                nickname = resp.email.split('@')[0]
            user = User(nickname=nickname, email=resp.email)
            db.session.add(user)
            db.session.commit()
        remember_me = False
        if 'remember_me' in session:
            remember_me = session['remember_me']
            session.pop('remember_me', None)
        login_user(user, remember = remember_me)
        return redirect(request.args.get('next') or url_for('index'))
    {% endraw %}
    {% endhighlight %}

> 这里检查登录用户是不是已经在数据库中，如果不在就添加新用户。Flask-Login的login_user函数用来注册该次登录为有效登录。

通过login_required装饰器来定义一个view是否需要登录才能访问。如果一个view需要登录，访问的时候该view的url就是__next__

那么，就需要设置当一个view需要登录的时候，跳转到哪里去登录app/\_\_init\_\_.py

    {% highlight python %}
    {% raw %}
    lm = LoginManager()
    lm.init_app(app)
    lm.login_view = 'login'
    {% endraw %}
    {% endhighlight %}

在index view中使用登录用户

    {% highlight python %}
    {% raw %}
    app.route('/')
    @app.route('/index')
    @login_required
    def index():
        user = g.user
        posts = [
            { 
                'author': {'nickname': 'John'}, 
                'body': 'Beautiful day in Portland!' 
            },
            { 
                'author': {'nickname': 'Susan'}, 
                'body': 'The Avengers movie was so cool!' 
            }
        ]
        return render_template('index.html',
                               title='Home',
                               user=user,
                               posts=posts)
    {% endraw %}
    {% endhighlight %}

最后，做用户退出。

退出试图app/views.py

    {% highlight sh %}
    {% raw %}
    @app.route('/logout')
    def logout():
        logout_user()
        return redirect(url_for('index'))
    {% endraw %}
    {% endhighlight %}

修改模板app/templates/base.html

    {% highlight html %}
    {% raw %}
    <div>Microblog:
            <a href="{{ url_for('index') }}">Home</a>
            {% if g.user.is_authenticated %}
            | <a href="{{ url_for('logout') }}">Logout</a>
            {% endif %}
        </div>
    {% endraw %}
    {% endhighlight %}


下次课是用户信息页面。