---
layout: post
title:  "Flask学习笔记 13 - 国际化和本地化"
date:   2016-04-22 10:40:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-xiv-i18n-and-l10n>

[SOURCE](https://github.com/snowyxx/microblog)

这章讲解国际化和本地化。使用的是[Flask-Babel][]。

### 配置

app/\_\_init\_\_.py

    {% highlight python %}
    {% raw %}
    from flask.ext.babel import Babel
    babel = Babel(app)
    {% endraw %}
    {% endhighlight %}

config.py

    {% highlight python %}
    {% raw %}
    LANGUAGES = {
        'en':'English',
        'zh_CN':'简体中文'
    }
    {% endraw %}
    {% endhighlight %}

app/views.py 在这里获取每个请求头中的`Accept-Language` ，来确定语言。然后设置给g。

    {% highlight python %}
    {% raw %}
    from app import babel
    from config import LANGUAGES
    
    @babel.localeselector
    def get_locale():
        return request.accept_languages.best_match(LANGUAGES.keys())
        
    @app.before_request
    def before_request():
        ...
        g.locale = get_locale()
    {% endraw %}
    {% endhighlight %}

babel.cfg：babel的配置文件。设定什么文件要I18N。

    [python: **.py]
    [jinja2: **/templates/**.html]
    extensions=jinja2.ext.autoescape,jinja2.ext.with_

### 抽出字串和翻译

py文件：使用`gettext()`

    {% highlight python %}
    {% raw %}
    flash(gettext('Your changes have been saved.'))
    {% endraw %}
    {% endhighlight %}
    

模版文件：`_('string')`

    {% highlight html %}
    {% raw %}
    <a href="/index">{{ _('Home') }}</a>
    {% endraw %}
    {% endhighlight %}

把`gettext()`和`_('string')`中字串抽出到翻译模板文件中：

    flask/bin/pybabel extract -F babel.cfg -o messages.pot app

然后根据生成的模板pot文件，生成具体语言的po文件。

    flask/bin/pybabel init -i messages.pot -d app/translations -l zh

使用文本编辑工具编辑，翻译抽出的字串。保存后，要编译成mo文件。

    flask/bin/pybabel compile -d app/translations

> 这里有个问题：如果上面的命令提示：catalog 'app/translations/zh/LC_MESSAGES/messages.po' is marked as fuzzy, skipping
> 删除po文件中`#,fuzzy`行。或者使用命令：pybabel compile  __-f__ -d translations

更新字串。比如你添加了新的翻译字串

    flask/bin/pybabel extract -F babel.cfg -o messages.pot app
    flask/bin/pybabel update -i messages.pot -d app/translations

### moment.js的国际化

1. [下载moment-with-locales.min.js](http://momentjs.com/)
2. 在base.html模版中使用如下代码：

    {% highlight html %}
    <script src="/static/js/moment-with-locales.min.js"></script>
    <script>moment.locale("{{ g.locale }}")</script>
    {% endraw %}
    {% endhighlight %}

完整的国际化和本地化更改请看这里：<https://github.com/miguelgrinberg/microblog/commit/cac572cb0e427654edd3ac599f6197f1f6ee29f6>
Flask-Babel的更多说明请看官网：[Flask-Babel][]

[Flask-Babel]:https://pythonhosted.org/Flask-Babel/
