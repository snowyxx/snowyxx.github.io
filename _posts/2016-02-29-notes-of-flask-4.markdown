---
layout: post
title:  "Flask学习笔记 4 - 数据库"
date:   2016-02-29 14:21:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-iv-database>

[SOURCE](https://github.com/snowyxx/microblog)

使用的数据库模块[Flask-SQLAlchemy](http://packages.python.org/Flask-SQLAlchemy)是对[SQLAlchemy](http://www.sqlalchemy.org/)的封装。

使用的数据库迁移模块[SQLAlchemy-migrate](http://code.google.com/p/sqlalchemy-migrate)

数据库配置写在根目录的config.py中：

    {% highlight python %}
    {% raw %}
    import os
    basedir = os.path.abspath(os.path.dirname(__file__))
    
    SQLALCHEMY_DATABASE_URI = 'sqlite:///' + os.path.join(basedir, 'app.db')
    SQLALCHEMY_MIGRATE_REPO = os.path.join(basedir, 'db_repository')    
    {% endraw %}
    {% endhighlight %}

>  使用sqlite，不用启动数据库服务器

在app/\_\_init\_\_.py中导入上面的设置

    {% highlight python %}
    {% raw %}
    from flask import Flask
    from flask.ext.sqlalchemy import SQLAlchemy
    
    app = Flask(__name__)
    app.config.from_object('config')
    db = SQLAlchemy(app)
    
    from app import views, models
    {% endraw %}
    {% endhighlight %}

> 不要搞混的地方， `from app import views, models`中的app是包名称（文件夹名称）；`app = Flask(__name__)`是个对象，一个Flask实例。

定义表：app/models.py

    {% highlight python %}
    {% raw %}
    from app import db
    
    class User(db.Model):
        id = db.Column(db.Integer, primary_key=True)
        nickname = db.Column(db.String(64), index=True, unique=True)
        email = db.Column(db.String(120), index=True, unique=True)
        posts = db.relationship('Post', backref='author', lazy='dynamic')
    
        def __repr__(self):
            return '<User %r>' % (self.nickname)
    
    class Post(db.Model):
        id = db.Column(db.Integer, primary_key = True)
        body = db.Column(db.String(140))
        timestamp = db.Column(db.DateTime)
        user_id = db.Column(db.Integer, db.ForeignKey('user.id'))
    
        def __repr__(self):
            return '<Post %r>' % (self.body)
    {% endraw %}
    {% endhighlight %}

> 注意这里外键的定义方法

以下是一些数据库操作脚本：

db_create.py

    {% highlight python %}
    {% raw %}
    #!flask/bin/python
    from migrate.versioning import api
    from config import SQLALCHEMY_DATABASE_URI
    from config import SQLALCHEMY_MIGRATE_REPO
    from app import db
    import os.path
    db.create_all()
    if not os.path.exists(SQLALCHEMY_MIGRATE_REPO):
        api.create(SQLALCHEMY_MIGRATE_REPO, 'database repository')
        api.version_control(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    else:
        api.version_control(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO, api.version(SQLALCHEMY_MIGRATE_REPO))
    {% endraw %}
    {% endhighlight %}

db_migrate.py

    {% highlight python %}
    {% raw %}
    #!flask/bin/python
    import imp
    from migrate.versioning import api
    from app import db
    from config import SQLALCHEMY_DATABASE_URI
    from config import SQLALCHEMY_MIGRATE_REPO
    v = api.db_version(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    migration = SQLALCHEMY_MIGRATE_REPO + ('/versions/%03d_migration.py' % (v+1))
    tmp_module = imp.new_module('old_model')
    old_model = api.create_model(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    exec(old_model, tmp_module.__dict__)
    script = api.make_update_script_for_model(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO, tmp_module.meta, db.metadata)
    open(migration, "wt").write(script)
    api.upgrade(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    v = api.db_version(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    print('New migration saved as ' + migration)
    print('Current database version: ' + str(v))
    {% endraw %}
    {% endhighlight %}

db_upgrade.py

    {% highlight python %}
    {% raw %}
    #!flask/bin/python
    from migrate.versioning import api
    from config import SQLALCHEMY_DATABASE_URI
    from config import SQLALCHEMY_MIGRATE_REPO
    api.upgrade(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    v = api.db_version(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    print('Current database version: ' + str(v))
    {% endraw %}
    {% endhighlight %}

db_downgrade.py

    {% highlight python %}
    {% raw %}
    #!flask/bin/python
    from migrate.versioning import api
    from config import SQLALCHEMY_DATABASE_URI
    from config import SQLALCHEMY_MIGRATE_REPO
    v = api.db_version(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    api.downgrade(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO, v - 1)
    v = api.db_version(SQLALCHEMY_DATABASE_URI, SQLALCHEMY_MIGRATE_REPO)
    print('Current database version: ' + str(v))
    {% endraw %}
    {% endhighlight %}

执行db_create.py生成数据库后，看一下怎么使用数据库：

    {% highlight sh %}
    {% raw %}
    # 启动pyton交换方法
    flask\Scripts\python
    >>> from app import db, models
    # 创建2个用户
    >>> u = models.User(nickname='john', email='john@email.com')
    >>> db.session.add(u)
    >>> db.session.commit()
    >>> u = models.User(nickname='susan', email='susan@email.com')
    >>> db.session.add(u)
    >>> db.session.commit()
    >>>
    # 查询所有用户
    >>> users = models.User.query.all()
    >>> users
    [<User u'john'>, <User u'susan'>]
    >>> for u in users:
    ...     print(u.id,u.nickname)
    ...
    1 john
    2 susan
    >>>
    # 查询具体某个用户
    >>> u = models.User.query.get(1)
    >>> u
    <User u'john'>
    >>>
    # 添加个post
    >>> import datetime
    >>> u = models.User.query.get(1)
    >>> p = models.Post(body='my first post!', timestamp=datetime.datetime.utcnow(), author=u)
    >>> db.session.add(p)
    >>> db.session.commit()
    # get all posts from a user
    >>> u = models.User.query.get(1)
    >>> u
    <User u'john'>
    >>> posts = u.posts.all()
    >>> posts
    [<Post u'my first post!'>]
    
    # obtain author of each post
    >>> for p in posts:
    ...     print(p.id,p.author.nickname,p.body)
    ...
    1 john my first post!
    
    # a user that has no posts
    >>> u = models.User.query.get(2)
    >>> u
    <User u'susan'>
    >>> u.posts.all()
    []
    
    # get all users in reverse alphabetical order
    >>> models.User.query.order_by('nickname desc').all()
    [<User u'susan'>, <User u'john'>]
    >>>
    # 清空上面的测试数据
    >>> users = models.User.query.all()
    >>> for u in users:
    ...     db.session.delete(u)
    ...
    >>> posts = models.Post.query.all()
    >>> for p in posts:
    ...     db.session.delete(p)
    ...
    >>> db.session.commit()
    >>>
    {% endraw %}
    {% endhighlight %}

[Flask-SQLAlchemy一个简介](https://segmentfault.com/a/1190000004618621)

---

### 补充: 查看Flask-SQLAlchemy具体执行的语句

启用慢查询监视及监视阈值： config.py

    SQLALCHEMY_RECORD_QUERIES = True
    # slow database query threshold (in seconds)
    DATABASE_QUERY_TIMEOUT = 0.5

Flash的after_request方法可用帮助我们输出每次请求的慢语句。

``` python
app/views.py

from flask.ext.sqlalchemy import get_debug_queries
from config import DATABASE_QUERY_TIMEOUT

@app.after_request
def after_request(response):
    for query in get_debug_queries():
        if query.duration >= DATABASE_QUERY_TIMEOUT:
            app.logger.warning("SLOW QUERY: %s\nParameters: %s\nDuration: %fs\nContext: %s\n" % (query.statement, query.parameters, query.duration, query.context))
    return response
```

下次课是如何真正地使用数据库