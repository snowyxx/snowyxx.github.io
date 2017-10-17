---
layout: post
title:  "Flask学习笔记 8 - 关注和取消关注"
date:   2016-03-21 10:15:05 +0800
categories: 笔记
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-viii-followers-contacts-and-friends>

[SOURCE](https://github.com/snowyxx/microblog)

这次课首先介绍了数据库关系，一对一，一对多，多对多。讲得非常明白。[查看原文](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-viii-followers-contacts-and-friends)。

### 模块设置

创建user引用自己的协表followers

![flask-mega-tutorial-part-viii-2](/images/flask-mega-tutorial-part-viii-2.png) 

模式编码： app/models.py

followers表

    {% highlight python %}
    {% raw %}
    followers = db.Table('followers',
        db.Column('follower_id', db.Integer, db.ForeignKey('user.id')),
        db.Column('followed_id', db.Integer, db.ForeignKey('user.id'))
    )
    {% endraw %}
    {% endhighlight %}

User表

    {% highlight python %}
    {% raw %}
    #...
    followed = db.relationship('User', 
                                   secondary=followers, 
                                   primaryjoin=(followers.c.follower_id == id), 
                                   secondaryjoin=(followers.c.followed_id == id), 
                                   backref=db.backref('followers', lazy='dynamic'), 
                                   lazy='dynamic')
    def follow(self, user):
        if not self.is_following(user):
            self.followed.append(user)
            return self
    
    def unfollow(self, user):
        if self.is_following(user):
            self.followed.remove(user)
            return self
    
    def is_following(self, user):
        return self.followed.filter(followers.c.followed_id == user.id).count() > 0
    
    def followed_posts(self):
        return Post.query.join(followers, (followers.c.followed_id == Post.user_id)).filter(followers.c.follower_id == self.id).order_by(Post.timestamp.desc())
    {% endraw %}
    {% endhighlight %}

运行`db_migrate.py`更新数据库

### 客户端实现

首先，新用户自己关注自己添加打开用户页面的链接app/views.py

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
            nickname = User.make_unique_nickname(nickname)
            user = User(nickname=nickname, email=resp.email)
            db.session.add(user)
            db.session.commit()
            # make the user follow him/herself
            db.session.add(user.follow(user))
            db.session.commit()
        #...
    {% endraw %}
    {% endhighlight %}

关注和取消关注的控制器app/views.py

    {% highlight python %}
    {% raw %}
    @app.route('/follow/<nickname>')
    @login_required
    def follow(nickname):
        user = User.query.filter_by(nickname=nickname).first()
        if user is None:
            flash('User %s not found.' % nickname)
            return redirect(url_for('index'))
        if user == g.user:
            flash('You can\'t follow yourself!')
            return redirect(url_for('user', nickname=nickname))
        u = g.user.follow(user)
        if u is None:
            flash('Cannot follow ' + nickname + '.')
            return redirect(url_for('user', nickname=nickname))
        db.session.add(u)
        db.session.commit()
        flash('You are now following ' + nickname + '!')
        return redirect(url_for('user', nickname=nickname))
    
    @app.route('/unfollow/<nickname>')
    @login_required
    def unfollow(nickname):
        user = User.query.filter_by(nickname=nickname).first()
        if user is None:
            flash('User %s not found.' % nickname)
            return redirect(url_for('index'))
        if user == g.user:
            flash('You can\'t unfollow yourself!')
            return redirect(url_for('user', nickname=nickname))
        u = g.user.unfollow(user)
        if u is None:
            flash('Cannot unfollow ' + nickname + '.')
            return redirect(url_for('user', nickname=nickname))
        db.session.add(u)
        db.session.commit()
        flash('You have stopped following ' + nickname + '.')
        return redirect(url_for('user', nickname=nickname))
    {% endraw %}
    {% endhighlight %}

最后在用户页面中添加关注和取消关注链接 app/templates/user.html

    {% highlight html %}
    {% raw %}
    <table>
          <tr valign="top">
              <td><img src="{{ user.avatar(128) }}"></td>
              <td>
                  <h1>User: {{ user.nickname }}</h1>
                  {% if user.about_me %}<p>{{ user.about_me }}</p>{% endif %}
                  {% if user.last_seen %}<p><i>Last seen on: {{ user.last_seen }}</i></p>{% endif %}
                  <p>{{ user.followers.count() }} followers | 
                  {% if user.id == g.user.id %}
                      <a href="{{ url_for('edit') }}">Edit your profile</a>
                  {% elif not g.user.is_following(user) %}
                      <a href="{{ url_for('follow', nickname=user.nickname) }}">Follow</a>
                  {% else %}
                      <a href="{{ url_for('unfollow', nickname=user.nickname) }}">Unfollow</a>
                  {% endif %}
                  </p>
              </td>
          </tr>
      </table>
    {% endraw %}
    {% endhighlight %}

