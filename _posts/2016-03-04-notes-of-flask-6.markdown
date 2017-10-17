---
layout: post
title:  "Flask学习笔记 6 - 用户页面"
date:   2016-03-04 15:58:05 +0800
categories: 笔记
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-vi-profile-page-and-avatars>

[SOURCE](https://github.com/snowyxx/microblog)

没有什么新概念，主要是编码。实现在用户页面中显示名称、自我介绍、最后活动时间、编辑信息页面、用户头像和该用户的微博列表。

用户视图定义app/views.py

    {% highlight python %}
    {% raw %}
    @app.route('/user/<nickname>')
    @login_required
    def user(nickname):
        user = User.query.filter_by(nickname=nickname).first()
        if user == None:
            flash('User %s not found.' % nickname)
            return redirect(url_for('index'))
        posts = [
            {'author': user, 'body': 'Test post #1'},
            {'author': user, 'body': 'Test post #2'}
        ]
        return render_template('user.html',
                               user=user,
                               posts=posts)  
    {% endraw %}
    {% endhighlight %}

用户页面模板app/templates/user.html

    {% highlight html %}
    {% raw %}
    {% extends "base.html" %}
    
    {% block content %}
        <table>
            <tr valign="top">
                <td><img src="{{ user.avatar(128) }}" alt="{{ user.nickname }}"></td>
                <td>
                    <h1>User: {{ user.nickname }}</h1>
                    {% if user.about_me %}<p>{{ user.about_me }}</p>{% endif %}
                    {% if user.last_seen %}<p>Last seen on:{{ user.last_seen }}</p>{% endif %}
                    {% if user.id == g.user.id %}<p><a href="{{ url_for('edit') }}">Edit</a></p>{% endif %}
                </td>
            </tr>
        </table>
        <hr>
        {% for post in posts %}
            {% include 'post.html' %}
        {% endfor %}
    {% endblock %}
    {% endraw %}
    {% endhighlight %}

上面的模板中嵌入了另外一个用来显示post的模版/app/templates/post.html

    {% highlight html %}
    {% raw %}
    <table>
        <tr valign="top">
            <td><img src="{{ post.author.avatar(64)}}" alt="{{ post.author.nickname }}"></td>
            <td><i>{{ post.author.nickname }} says:</i><br>{{ post.body }}</td>
        </tr>
    </table>
    {% endraw %}
    {% endhighlight %}

添加打开用户页面的链接app/templates/base.html：

    {% highlight html %}
    {% raw %}
    <div> Microblog: 
        <a href="/index">Home</a>
        {% if g.user.is_authenticated %}
            | <a href="{{ url_for('user', nickname=g.user.nickname) }}">My profile</a>
            | <a href="{{ url_for('logout') }}">Logout</a>
        {% endif %}
    </div>
    {% endraw %}
    {% endhighlight %}

在User类中添加头像方法app/models.py：

    {% highlight python %}
    {% raw %}
    from hashlib import md5
    # ...
    class User(db.Model):
        # ...
        def avatar(self, size):
            return 'http://www.gravatar.com/avatar/%s?d=mm&s=%d' % (md5(self.email.encode('utf-8')).hexdigest(), size)
    {% endraw %}
    {% endhighlight %}

为用户添加更多信息app/models.py

    {% highlight python %}
    {% raw %}
    class User(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        nickname = db.Column(db.String(64), index=True, unique=True)
        email = db.Column(db.String(120), index=True, unique=True)
        posts = db.relationship('Post', backref='author', lazy='dynamic')
        about_me = db.Column(db.String(140))
        last_seen = db.Column(db.DateTime)
    {% endraw %}
    {% endhighlight %}

> 运行db_migrate.py更新数据库结构

记录最后活动时间到数据库中app/views.py

    {% highlight python %}
    {% raw %}
    from datetime import datetime
    # ...
    @app.before_request
    def before_request():
        g.user = current_user
        if g.user.is_authenticated:
            g.user.last_seen = datetime.utcnow()
            db.session.add(g.user)
            db.session.commit()
    {% endraw %}
    {% endhighlight %}

设置编辑用户信息的表单app/forms.py

    {% highlight python %}
    {% raw %}
    from flask.ext.wtf import Form
    from wtforms import StringField, BooleanField, TextAreaField
    from wtforms.validators import DataRequired, Length
    
    class EditForm(Form):
        nickname = StringField('nickname', validators=[DataRequired()])
        about_me = TextAreaField('about_me', validators=[Length(min=0, max=140)])
    {% endraw %}
    {% endhighlight %}

编辑页面模板app/templates/edit.html

    {% highlight html %}
    {% raw %}
    {% extends "base.html" %}
    {% block content %}
        <h1>Edit Your Profile</h1>
        <form action="" method="post" name="edit">
            {{ form.hidden_tag() }}
            <table>
                <tr>
                    <td>Your nickname:</td>
                    <td>{{ form.nickname(size=24) }}</td>
                </tr>
                <tr>
                    <td>About yourself:</td>
                    <td>{{ form.about_me(cols=32, rows=4) }}</td>
                </tr>
                <tr>
                    <td></td>
                    <td><input type="submit" value="Save"></td>
                </tr>
            </table>
        </form>
    {% endblock %}
    {% endraw %}
    {% endhighlight %}

设置编辑页面的视图app/views.py

    {% highlight python %}
    {% raw %}
    from forms import LoginForm, EditForm
    
    @app.route('/edit', methods=['GET', 'POST'])
    @login_required
    def edit():
        form = EditForm()
        if form.validate_on_submit():
            g.user.nickname = form.nickname.data
            g.user.about_me = form.about_me.data
            db.session.add(g.user)
            db.session.commit()
            flash('Your changes have been saved.')
            return redirect(url_for('edit'))
        else:
            form.nickname.data = g.user.nickname
            form.about_me.data = g.user.about_me
        return render_template('edit.html', form=form)
    {% endraw %}
    {% endhighlight %}
