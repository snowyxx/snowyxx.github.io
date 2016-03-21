---
layout: post
title:  "Flask学习笔记 9 - 分页"
date:   2016-03-21 14:19:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-ix-pagination>

[SOURCE](https://github.com/snowyxx/microblog)

分页使用的是[Flask-SQLAlchemy](http://packages.python.org/Flask-SQLAlchemy)的功能，很简单。在进行分页之前，首先实现提交新微博的功能。

### 提交新post

定义新的表单类 app/forms.py

    {% highlight python %}
    {% raw %}
    class PostForm(Form):
        post = StringField('post', validators=[DataRequired()])
    {% endraw %}
    {% endhighlight %}

在主页中添加该表单 app/templates/index.html

    {% highlight html %}
    {% raw %}
    <form action="" method="post" accept-charset="utf-8" name="post">
        {{ form.hidden_tag() }}        
        <table>
            <tr>
                <td>Say something:</td>
                <td>{{ form.post(size=30,maxlength=140) }}</td>
                <td>
                    {% for error in form.post.errors %}
                        <span style="color:red;">[{{ error }}]</span>
                    {% endfor %}
                </td>
            </tr>
            <tr>
                <td></td>
                <td><input type="submit" value="Post!"></td>
                <td></td>
            </tr>
        </table>
    </form>
    {% endraw %}
    {% endhighlight %}

在视图中接收表单数据并保存到数据库 app/views.py

    {% highlight python %}
    {% raw %}
    from forms import LoginForm, EditForm, PostForm
    from models import User, Post
    
    @app.route('/', methods=['GET', 'POST'])
    @app.route('/index', methods=['GET', 'POST'])
    @login_required
    def index():
        form = PostForm()
        if form.validate_on_submit():
            post = Post(body=form.post.data, timestamp=datetime.utcnow(), author=g.user)
            db.session.add(post)
            db.session.commit()
            flash('Your post is now live!')
            return redirect(url_for('index'))
        #...
    {% endraw %}
    {% endhighlight %}

在主页中显示所有微博`posts = g.user.followed_posts().all()`。使用其替换之前到假数据。`all()`方法是把`g.user.followed_posts()`返回的sqlalchemy查询对象转成一个列表。

### 分页实现

分页要使用`posts = g.user.followed_posts().paginate(page, POSTS_PER_PAGE, False)`

> paginate()的参数： __page__ 是第几页；__POSTS\_PER\_PAGE__ 每页显示的数量；__False__ 是错误标志。当发生错误时，如果是False返回空列表，如果是True，转到404页面。
>
> POSTS_PER_PAGE写入到config.py中，便于以后更改。 page通过使用该句到方法参数传入。
>
> paginate()返回个Pagination对象。通过__items__获取到成员列表。
>
> Pagination用于导航的几个属性：
>
>   - has_next: True if there is at least one more page after the current one
>
>   - has_prev: True if there is at least one more page before the current one
>
>   - next_num: page number for the next page
>
>   - prev_num: page number for the previous page

视图中路由的设置app/views.py

    {% highlight python %}
    {% raw %}
    from config import POSTS_PER_PAGE
    
    @app.route('/', methods=['GET', 'POST'])
    @app.route('/index', methods=['GET', 'POST'])
    @app.route('/index/<int:page>', methods=['GET', 'POST'])
    @login_required
    def index(page=1):
        form = PostForm()
        if form.validate_on_submit():
            post = Post(body=form.post.data, timestamp=datetime.utcnow(), author=g.user)
            db.session.add(post)
            db.session.commit()
            flash('Your post is now live!')
            return redirect(url_for('index'))
        posts = g.user.followed_posts().paginate(page, POSTS_PER_PAGE, False).items
        return render_template('index.html',
                               title='Home',
                               form=form,
                               posts=posts)
    {% endraw %}
    {% endhighlight %}

导航链接 app/templates/index.html

    {% highlight html %}
    {% raw %}
    {% if posts.has_prev %}
        <a href="{{ url_for('index', page=posts.prev_num) }}">&lt;&lt; Newer posts</a>
    {% else %}
        &lt;&lt; Newer posts
    {% endif %}
    |
    {% if posts.has_next %}
        <a href="{{ url_for('index', page=posts.next_num) }}">&gt;&gt; Newer posts</a>
    {% else %}
        &gt;&gt; Older posts
    {% endif %}
    {% endraw %}
    {% endhighlight %}

然后就是还有一些具体编码工作。比如在用户界面，user.html，中分页现实其所有微博；添加导航链接。这都和index页面类似。