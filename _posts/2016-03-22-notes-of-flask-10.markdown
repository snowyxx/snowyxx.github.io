---
layout: post
title:  "Flask学习笔记 10 - 搜索"
date:   2016-03-22 11:09:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-x-full-text-search>

[SOURCE](https://github.com/snowyxx/microblog)

全文搜索使用的是[Whoosh](https://whoosh.readthedocs.org/en/latest/)，通过[Flask-WhooshAlchemy](https://github.com/gyllstromk/Flask-WhooshAlchemy)和Flask-SQLAlchemy集成在一起。

### 索引

Whoosh设置 config.py

    WHOOSH_BASE = os.path.join(basedir, 'search.db')

在模块中设置索引

    {% highlight python %}
    {% raw %}
    import flask.ext.whooshalchemy as whooshalchemy
    class Post(db.Model):
        __searchable__ = ['body']
        
        id = db.Column(db.Integer, primary_key=True)
        body = db.Column(db.String(140))
        timestamp = db.Column(db.DateTime)
        user_id = db.Column(db.Integer, db.ForeignKey('user.id'))
    
        def __repr__(self):
            return '<Post %r>' % (self.body)
    
    whooshalchemy.whoosh_index(app, Post)
    {% endraw %}
    {% endhighlight %}

> 索引支只对新记录有效。而且不清楚旧记录会报错。

这样就可以搜索了： `Post.query.whoosh_search('second').all()`

### 应用中实现

最大搜索数设置config.py

    MAX_SEARCH_RESULTS = 50

搜索表单
表单类：app/forms.py 

    {% highlight python %}
    {% raw %}
    class SearchForm(Form):
        search = StringField('search', validators=[DataRequired()]
    {% endraw %}
    {% endhighlight %}

因为要在所有模版中使用搜索表单，所以把它设置到g中 app/views.py

    {% highlight python %}
    {% raw %}
    from forms import SearchForm
    
    @app.before_request
    def before_request():
        g.user = current_user
        if g.user.is_authenticated:
            g.user.last_seen = datetime.utcnow()
            db.session.add(g.user)
            db.session.commit()
            g.search_form = SearchForm()
    {% endraw %}
    {% endhighlight %}

在模版中添加表单 app/templates/base.html

{% highlight html %}
{% raw %}
<div> Microblog: 
    <a href="/index">Home</a>
    {% if g.user.is_authenticated %}
        | <a href="{{ url_for('user', nickname=g.user.nickname) }}">My profile</a>
        | <form action="{{ url_for('search') }}" method="post" accept-charset="utf-8" name="search" style="display: inline">
            {{ g.search_form.hidden_tag() }}
            {{ g.search_form.search(size=20) }}
            <input type="submit" value="Search">
        </form>
        | <a href="{{ url_for('logout') }}">Logout</a>
    {% endif %}
</div>
{% endraw %}
{% endhighlight %}

表单视图 app/views.py

{% highlight python %}
{% raw %}
@app.route('/search', methods=['POST'])
@login_required
def search():
    if not g.search_form.validate_on_submit():
        return redirect(url_for('index'))
    return redirect(url_for('search_results', query=g.search_form.search.data))
{% endraw %}
{% endhighlight %}

在新视图中处理搜索结果，防止刷新原页面重复提交搜索。并且在新模版中显示搜索结果。 

app/views.py

{% highlight python %}
{% raw %}
from config import MAX_SEARCH_RESULTS

@app.route('/search_results/<query>')
@login_required
def search_results(query):
    results = Post.query.whoosh_search(query, MAX_SEARCH_RESULTS).all()
    return render_template('search_results.html',
                           query=query,
                           results=results)
{% endraw %}
{% endhighlight %}

结果模版 app/templates/search_results.html

{% highlight html %}
{% raw %}
{% extends "base.html" %}

{% block content %}
  <h1>Search results for "{{ query }}":</h1>
  {% for post in results %}
      {% include 'post.html' %}
  {% endfor %}
{% endblock %}
{% endraw %}
{% endhighlight %}

### 中文搜索支持

上面这些已经实现全文搜索，但是不支持中文。要用到jieba

安装

    flask/bin/pip install jieba

Flask-WhooshAlchemy使用jieba分析器

{% highlight python %}
{% raw %}
class Post(db.Model):
    __searchable__ = ['body']
    __analyzer__ = ChineseAnalyzer()
    
    id = db.Column(db.Integer, primary_key=True)
    body = db.Column(db.String(140))
    timestamp = db.Column(db.DateTime)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'))

    def __repr__(self):
        return '<Post %r>' % (self.body)

whooshalchemy.whoosh_index(app, Post)
{% endraw %}
{% endhighlight %}

这样的实现效果还是不能完美搜索中文。