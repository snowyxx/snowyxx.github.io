---
layout: post
title:  "Flask学习笔记 14 - ajax"
date:   2016-04-25 14:34:05 +0800
categories: 笔记
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xv-ajax>

[SOURCE](https://github.com/snowyxx/microblog)

这章通过实现翻译微博内容来讲解ajax的使用。

主要包括3部分的内容：

1. 选择和使用翻译服务，微软免费翻译服务。
2. 使用[guess-language][]来获取一个微博的原始语言
3. 使用ajax获取翻译结果

### 微软免费翻译服务

微软翻译服务叫Microsoft Translator。在使用之前要先注册获取client id和密钥。步骤如下：

- 首先在[Microsoft Translator app][]注册。选择免费的即可。
- 开发者要[注册一个应用](https://datamarket.azure.com/developer/applications/)，以获得client id和密钥。这是发生翻译请求时的2个参数。

具体实行的代码：
把client id和密钥写到config.py中：

    # microsoft translation service
    MS_TRANSLATOR_CLIENT_ID = '' # enter your MS translator app id here
    MS_TRANSLATOR_CLIENT_SECRET = '' # enter your MS translator app secret here


获取翻译： app/translate.py

    {% highlight python %}
    {% raw %}
    try:
        import httplib  # Python 2
    except ImportError:
        import http.client as httplib  # Python 3
    try:
        from urllib import urlencode  # Python 2
    except ImportError:
        from urllib.parse import urlencode  # Python 3
    import json
    from flask.ext.babel import gettext
    from config import MS_TRANSLATOR_CLIENT_ID, MS_TRANSLATOR_CLIENT_SECRET

    def microsoft_translate(text, sourceLang, destLang):
        if MS_TRANSLATOR_CLIENT_ID == "" or MS_TRANSLATOR_CLIENT_SECRET == "":
            return gettext('Error: translation service not configured.')
        try:
            # get access token
            params = urlencode({
                'client_id': MS_TRANSLATOR_CLIENT_ID,
                'client_secret': MS_TRANSLATOR_CLIENT_SECRET,
                'scope': 'http://api.microsofttranslator.com', 
                'grant_type': 'client_credentials'})
            conn = httplib.HTTPSConnection("datamarket.accesscontrol.windows.net")
            conn.request("POST", "/v2/OAuth2-13", params)
            response = json.loads (conn.getresponse().read())
            token = response[u'access_token']
    
            # translate
            conn = httplib.HTTPConnection('api.microsofttranslator.com')
            params = {'appId': 'Bearer ' + token,
                      'from': sourceLang,
                      'to': destLang,
                      'text': text.encode("utf-8")}
            conn.request("GET", '/V2/Ajax.svc/Translate?' + urlencode(params))
            response = json.loads("{\"response\":" + conn.getresponse().read().decode('utf-8-sig') + "}")
            return response["response"]
        except:
            return gettext('Error: Unexpected error.')
    {% endraw %}
    {% endhighlight %}

> __conn.getresponse().read().decode('utf-8-sig')__ : 使用utf-8 decode的时候，回出现如下错误           
> "Error: Unexpected error:ValueError('No JSON object could be decoded',)"          
> 原因：response中的utf-8是呆BOM（Byte order mark）的，就是`u'\ufeff'`。所以采用[utf-8-sig][]。

### 获取一个微博的原始语言

使用[guess-language][]来获取一个微博的原始语言，然后记录到数据库的post表中。具体实现过程是：

/app/models.py

    {% highlight python %}
    {% raw %}
    class Post(db.Model):
        __searchable__ = ['body']

        id = db.Column(db.Integer, primary_key=True)
        body = db.Column(db.String(140))
        timestamp = db.Column(db.DateTime)
        user_id = db.Column(db.Integer, db.ForeignKey('user.id'))
        language = db.Column(db.String(5))
    {% endraw %}
    {% endhighlight %}
    

运行`flask/bin/python db_migrate.py`来更新数据库。

当用户发新的微博的时候获取其语言并保存到数据库。/app/views.py

    {% highlight python %}
    {% raw %}
    from guess_language import guessLanguage
    
    @app.route('/', methods=['GET', 'POST'])
    @app.route('/index', methods=['GET', 'POST'])
    @app.route('/index/<int:page>', methods=['GET', 'POST'])
    @login_required
    def index(page=1):
        form = PostForm()
        if form.validate_on_submit():
            language = guessLanguage(form.post.data)
            if language == 'UNKNOWN' or len(language) > 5:
                language = ''
            post = Post(body=form.post.data, 
                        timestamp=datetime.utcnow(), 
                        author=g.user, 
                        language=language)
            db.session.add(post)
            db.session.commit()
            flash(gettext('Your post is now live!'))
            return redirect(url_for('index'))
        posts = g.user.followed_posts().paginate(page, POSTS_PER_PAGE, False)
        return render_template('index.html',
                               title='Home',
                               form=form,
                               posts=posts)
    {% endraw %}
    {% endhighlight %}

### 使用ajax发送翻译的post请求并显示结果

1. 服务器端，处理/translate的post请求，把数据传给上面的translate.py，然后返回一个格式为`{ "text": "<translated text goes here>" }`的json结果。
    app/views.py
    
            {% highlight python %}
            {% raw %}
            from flask import jsonify
            from .translate import microsoft_translate

            @app.route('/translate', methods=['POST'])
            @login_required
            def translate():
                return jsonify({ 
                    'text': microsoft_translate(
                        request.form['text'], 
                        request.form['sourceLang'], 
                        request.form['destLang']) })
            {% endraw %}
            {% endhighlight %}

2. 客户端，在微博后面添加个翻译链接，点击该链接执行一个javascript函数`translate(sourceLang, destLang, sourceID, destID, loadingID)`
    post.html
        {% highlight html %}
        {% raw %}
        <table>
            <tr valign="top">
                <td><img src="{{ post.author.avatar(64)}}" alt="{{ post.author.nickname }}"></td>
                <td>
                    <p><a href="{{ url_for('user', nickname=post.author.nickname)}}">{{ post.author.nickname }}</a> said {{ momentjs(post.timestamp).fromNow() }}:</p>
                    <p><strong><span id="post{{ post.id }}">{{ post.body }}</span></strong></p>
                    {% if post.language != None and post.language !='' and post.language != g.locale %}
                    <img src="/static/images/loading.gif" id="loading{{ post.id }}" style="display:none">
                    <div><span id="translation{{ post.id }}"><a href="javascript:translate('{{ post.language }}', '{{ g.locale }}', '#post{{ post.id }}', '#translation{{ post.id }}', '#loading{{ post.id }}');">Translate</a></span></div>
                    {% endif %}
                </td>
            </tr>
        </table>
        {% endraw %}
        {% endhighlight %}

    
3. `translate(sourceLang, destLang, sourceID, destID, loadingID)`代码：base.html

    {% highlight js %}
    {% raw %}
    <script src="/static/js/jquery-2.2.3.min.js" type="text/javascript" charset="utf-8"></script>    
    <script>
        function translate(sourceLang, destLang, sourceID, destID, loadingID){
            $(destID).hide();
            $(loadingID).show();
            $.post('/translate',{
                text:$(sourceID).text(),
                sourceLang:sourceLang,
                destLang:destLang
            }).done(function(translated){
                $(destID).text(translated['text']);
                $(loadingID).hide();
                $(destID).show();
            }).fail(function(){
                $(destID).text("Translate failed!");
                $(loadingID).hide();
                $(destID).show();
            });
        }
    </script>
    {% endraw %}
    {% endhighlight %}

---

### 结束

这就是我的Flask学习笔记的最后一篇了。当然原作者还有2篇，讲了些Linux部署、使用mysql／postgresql作为数据库、使用web服务器（apache、gunicorn）等。

- [Deployment on Linux (even on the Raspberry Pi!)](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xvii-deployment-on-linux-even-on-the-raspberry-pi)
- [Deployment on the Heroku Cloud](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xviii-deployment-on-the-heroku-cloud)


[guess-language]:https://github.com/kent37/guess-language
[Microsoft Translator app]:https://datamarket.azure.com/dataset/1899a118-d202-492c-aa16-ba21c33c06cb
[utf-8-sig]:http://stackoverflow.com/questions/17912307/u-ufeff-in-python-string