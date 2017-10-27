---
layout: post
title:  "Flask学习笔记 7 - 日志和单元测试"
date:   2016-03-10 16:07:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-vii-unit-testing>

[SOURCE](https://github.com/snowyxx/microblog)

这次课首先添加了404.html和500.html错误页面；然后配置通过邮件和文件记录日志；用重复nickename为例讲了问题解决办法；最后写了个单元测试框架。

**补充内容** 最后添加了[测试覆盖范围检查工具](#coverage)； [Flash应用性能分析](#profiling)。

### 404和500错误页面

首先设置视图app/views.py

    {% highlight python %}
    {% raw %}
    @app.errorhandler(404)
    def not_found_error(error):
        return render_template('404.html'), 404
    
    @app.errorhandler(500)
    def internal_error(error):
        db.session.rollback()
        return render_template('500.html'), 500
    {% endraw %}
    {% endhighlight %}

页面模板

- app/templates/404.html

    {% highlight html %}
    {% raw %}
    {% extends "base.html" %}
    
    {% block content %}
      <h1>File Not Found</h1>
      <p><a href="{{ url_for('index') }}">Back</a></p>
    {% endblock %}
    {% endraw %}
    {% endhighlight %}

- app/templates/500.html

    {% highlight html %}
    {% raw %}
    {% extends "base.html" %}
    {% block content %}
        <h1>An unexpected error has occurrred</h1>
        <p>The administrator has been notified. Sorry for the inconvenience!</p>
        <p><a href="{{ url_for('index') }}">Home</a></p>
    {% endblock %}
    {% endraw %}
    {% endhighlight %}
    
### 日志邮件和日志文件

添加打开用户页面的链接\_\_init\_\_.py：

    {% highlight python %}
    {% raw %}
    from config import  basedir, ADMINS, MAIL_SERVER, MAIL_PORT, MAIL_USERNAME, MAIL_PASSWORD
    if not app.debug:
        import logging
        from logging.handlers import SMTPHandler
        from logging.handlers import RotatingFileHandler
        credentials = None
        if MAIL_USERNAME or MAIL_PASSWORD:
            credentials = (MAIL_USERNAME, MAIL_PASSWORD)
        mail_handler =  SMTPHandler((MAIL_SERVER, MAIL_PORT), 'no-reply@' + MAIL_SERVER, ADMINS, 'microblog failure', credentials)
        file_handler = RotatingFileHandler('tmp/microblog.log', 'a', 1*1024*1024, 10)
        file_handler.setFormatter(logging.Formatter('%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]'))
        app.logger.setLevel(logging.INFO)
        mail_handler.setLevel(logging.ERROR)
        file_handler.setLevel(logging.INFO)
        app.logger.addHandler(mail_handler)
        app.logger.addHandler(file_handler)
        app.logger.info('microblog startup')
    {% endraw %}
    {% endhighlight %}

> 当在run.py中设置`app.run(debug=False)`，出现错误不会输出给客户（浏览器）。

__[NOTE]__ 使用python的smtp模块把发送邮件在控制台中输出：

    python -m smtpd -n -c DebuggingServer localhost:25

### 解决nickname重复的问题

在app/views.py的after_login中添加`nickname = User.make_unique_nickname(nickname)`：

    {% highlight python %}
    {% raw %}
    if user is None:
        nickname = resp.nickname
        if nickname is None or nickname == "":
            nickname = resp.email.split('@')[0]
        nickname = User.make_unique_nickname(nickname)
        user = User(nickname = nickname, email = resp.email)
        db.session.add(user)
        db.session.commit()
    {% endraw %}
    {% endhighlight %}

为用户类中添加make_unique_nickname(nickname)。 app/models.py

    {% highlight python %}
    {% raw %}
    class User(db.Model):
    # ...
    @staticmethod
    def make_unique_nickname(nickname):
        if User.query.filter_by(nickname=nickname).first() is None:
            return nickname
        version = 2
        while True:
            new_nickname = nickname + str(version)
            if User.query.filter_by(nickname=new_nickname).first() is None:
                break
            version += 1
        return new_nickname
    # ...
    {% endraw %}
    {% endhighlight %}

对于用户配置文件编辑页面中，如果用户自己输入的用户已经存在，那么给出重新输入提示。主要是覆写表单验证方法 app/forms.py

    {% highlight python %}
    {% raw %}
    from app.models import User
    
    class EditForm(Form):
        nickname = StringField('nickname', validators=[DataRequired()])
        about_me = TextAreaField('about_me', validators=[Length(min=0, max=140)])
    
        def __init__(self, original_nickname, *args, **kwargs):
            Form.__init__(self, *args, **kwargs)
            self.original_nickname = original_nickname
    
        def validate(self):
            if not Form.validate(self):
                return False
            if self.nickname.data == self.original_nickname:
                return True
            user = User.query.filter_by(nickname=self.nickname.data).first()
            if user != None:
                self.nickname.errors.append('This nickname is already in use. Please choose another one.')
                return False
            return True
    {% endraw %}
    {% endhighlight %}

记得更新编辑页面的视图设置app/views.py

    {% highlight python %}
    {% raw %}
    @app.route('/edit', methods=['GET', 'POST'])
    @login_required
    def edit():
        form = EditForm(g.user.nickname)
        # ...
    {% endraw %}
    {% endhighlight %}

更新编辑页面模板，显示错误信息。app/templates/edit.html

    {% highlight html %}
    {% raw %}
    <td>Your nickname:</td>
    <td>
        {{ form.nickname(size=24) }}
        {% for error in form.errors.nickname %}
        <br><span style="color: red;">[{{ error }}]</span>
        {% endfor %}
    </td>
    {% endraw %}
    {% endhighlight %}

### 单元测试

    {% highlight python %}
    {% raw %}
    #!flask/bin/python
    import os
    import unittest
    
    from config import basedir
    from app import app, db
    from app.models import User
    
    class TestCase(unittest.TestCase):
        def setUp(self):
            app.config['TESTING'] = True
            app.config['WTF_CSRF_ENABLED'] = False
            app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///' + os.path.join(basedir, 'test.db')
            self.app = app.test_client()
            db.create_all()
    
        def tearDown(self):
            db.session.remove()
            db.drop_all()
    
        def test_avatar(self):
            u = User(nickname='john', email='john@example.com')
            avatar = u.avatar(128)
            expected = 'http://www.gravatar.com/avatar/d4c74594d841139328695756648b6bd6'
            assert avatar[0:len(expected)] == expected
    
        def test_make_unique_nickname(self):
            u = User(nickname='john', email='john@example.com')
            db.session.add(u)
            db.session.commit()
            nickname = User.make_unique_nickname('john')
            assert nickname != 'john'
            u = User(nickname=nickname, email='susan@example.com')
            db.session.add(u)
            db.session.commit()
            nickname2 = User.make_unique_nickname('john')
            assert nickname2 != 'john'
            assert nickname2 != nickname
    
    if __name__ == '__main__':
        unittest.main()
    {% endraw %}
    {% endhighlight %}

运行单元测试flask/Scripts/python tests.py，如果测试用例都通过，显示类似这样：

    $ flask/bin/python test.py
    ..
    ----------------------------------------------------------------------
    Ran 2 tests in 0.057s
    
    OK

----

### 测试范围覆盖<a name="coverage"></a>

[查看这里](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xvi-debugging-testing-and-profiling)的**Test Coverage**部分。

### 应用性能分析<a name="profiling"></a>

[查看这里](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xvi-debugging-testing-and-profiling)的**Profiling for performance**部分。

Python自带了性能分析工具[cProfile](http://docs.python.org/2/library/profile.html)
Flask有一个扩展为其提供应用性能分析**Werkzeug**
使用下面的profile.py替换run.py，启动应用就可以获得应用分析报表了。

    {% highlight python %}
    {% raw %}
    #!flask/bin/python
    from werkzeug.contrib.profiler import ProfilerMiddleware
    from app import app

    app.config['PROFILE'] = True
    app.wsgi_app = ProfilerMiddleware(app.wsgi_app, restrictions=[30])
    app.run(debug = True)
    {% endraw %}
    {% endhighlight %}





