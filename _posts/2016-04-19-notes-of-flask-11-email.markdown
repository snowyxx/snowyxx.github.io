---
layout: post
title:  "Flask学习笔记 11 - 邮件"
date:   2016-04-19 10:36:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xi-email-support>

[SOURCE](https://github.com/snowyxx/microblog)

这章讲解了怎么使用**Flask-Mail**发送邮件。

另外还涉及到[线程][1]和[装饰器][2]的使用。

### 配置

和前面单元配置中一样，在config.py配置邮件服务器和管理员邮件地址列表。

    {% highlight python %}
    {% raw %}
    # email server
    MAIL_SERVER = 'your.mailserver.com'
    MAIL_PORT = 25
    MAIL_USERNAME = None
    MAIL_PASSWORD = None
    
    # administrator list
    ADMINS = ['you@example.com']
    {% endraw %}
    {% endhighlight %}

### 初始化mail对象

表单类：app/\_\_init\_\_.py

    {% highlight python %}
    {% raw %}
    from flask.ext.mail import Mail
    mail = Mail(app)
    {% endraw %}
    {% endhighlight %}

### 定义新的模块用来包含各种发送邮件函数

app/emails.py

    {% highlight python %}
    {% raw %}
    from flask.ext.mail import Message
    from flask import render_template
    from app import mail, app
    from config import ADMINS
    from .decorators import async
    
    @async
    def send_async_email(app, msg):
        with app.app_context():
            mail.send(msg)
    
    def send_mail(subject, sender, recipients, text_body, html_body):
        msg = Message(subject, sender=sender, recipients=recipients)
        msg.body = text_body
        msg.html = html_body
        send_async_email(app, msg)
    
    
    def follower_notification(followed, follower):
        send_mail("[microblog] %s is now following you!" % follower.nickname,
                  ADMINS[0], [followed.email],
                  render_template(
                      'follower_email.txt', user=followed, follower=follower),
                  render_template(
                      'follower_email.html', user=followed, follower=follower),
                  )
    
    {% endraw %}
    {% endhighlight %}

>from .decorators import async： 在decorators.py中使用装饰器来调用新的线程来发送邮件：

       {% highlight python %}
       {% raw %}
       from threading import Thread
       
       def async(f):
           def wrapper(*args, **kwargs):
               thr = Thread(target=f, args=args, kwargs=kwargs)
               thr.start()
           return wrapper
       {% endraw %}
       {% endhighlight %}
    
> 发送邮件中的text\_body和html\_body使用模版：
> app/templates/follower_email.txt

       {% highlight text %}
       {% raw %}
       Dear {{ user.nickname }},
       
       {{ follower.nickname }} is now a follower. Click on the following link to visit {{ follower.nickname }}'s profile page:
       
       {{ url_for('user', nickname=follower.nickname, _external=True) }}
       
       Regards,
       
       The microblog admin
       {% endraw %}
       {% endhighlight %}

> app/templates/follower_email.html

       {% highlight html %}
       {% raw %}
       <p>Dear {{ user.nickname }},</p>
       <p><a href="{{ url_for('user', nickname=follower.nickname, _external=True) }}">{{ follower.nickname }}</a> is now a follower.</p>
       <table>
           <tr valign="top">
               <td><img src="{{ follower.avatar(50) }}"></td>
               <td>
                   <a href="{{ url_for('user', nickname=follower.nickname, _external=True) }}">{{ follower.nickname }}</a><br />
                   {{ follower.about_me }}
               </td>
           </tr>
       </table>
       <p>Regards,</p>
       <p>The <code>microblog</code> admin</p>
       {% endraw %}
       {% endhighlight %}

### 最后就是在要发送邮件地方添加相应的函数

app/views.py

{% highlight python %}
{% raw %}
from .emails import follower_notification

@app.route('/follow/<nickname>')
@login_required
def follow(nickname):
    user = User.query.filter_by(nickname=nickname).first()
    # ...
    follower_notification(user, g.user)
    return redirect(url_for('user', nickname=nickname))
{% endraw %}
{% endhighlight %}

这样，一个用户关注另一个用户的时候，就会有邮件发出。

[1]:http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386832360548a6491f20c62d427287739fcfa5d5be1f000
[2]:http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386819879946007bbf6ad052463ab18034f0254bf355000
