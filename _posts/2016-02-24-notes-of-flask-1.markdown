---
layout: post
title:  "Flask学习笔记 1"
date:   2016-02-24 11:25:05 +0800
categories: blog
tags:   [python,Flask]
---
Flask教程：<http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world>

### 初始化：

    $ mkdir microblog
    $ cd microblog/

> download virtualenv whl file from <https://pypi.python.org/pypi/virtualenv>

    $ pip install ~/Downloads/virtualenv-14.0.6-py2.py3-none-any.whl
    $ virtualenv flask

安装需要的模块

> Virtual environments can be activated and deactivated, if desired. An activated environment adds the location of its bin folder to the system path, so that for example, when you type python you get the environment's version and not the system's one. But activating a virtual environment is not necessary, it is equally effective to invoke the interpreter by specifying its pathname.

    $ flask/bin/pip install flask
    $ flask/bin/pip install flask-login
    $ flask/bin/pip install flask-openid
    $ flask/bin/pip install flask-mail
    $ flask/bin/pip install flask-sqlalchemy
    $ flask/bin/pip install sqlalchemy-migrate
    $ flask/bin/pip install flask-whooshalchemy
    $ flask/bin/pip install flask-wtf
    $ flask/bin/pip install flask-babel
    $ flask/bin/pip install guess_language
    $ flask/bin/pip install flipflop
    $ flask/bin/pip install coverage

以上命令输出：

    $ mkdir microblog
    $ cd microblog/
    $ pip install ~/Downloads/virtualenv-14.0.6-py2.py3-none-any.whl
    Processing /Users/yan/Downloads/virtualenv-14.0.6-py2.py3-none-any.whl
    Installing collected packages: virtualenv
    Successfully installed virtualenv-14.0.6
    $ virtualenv flask
    New python executable in /Users/yan/Documents/git/microblog/flask/bin/python
    Installing setuptools, pip, wheel...done.
    $ flask/bin/pip install flask
    Collecting flask
      Downloading Flask-0.10.1.tar.gz (544kB)
        100% |████████████████████████████████| 544kB 204kB/s
    Collecting Werkzeug>=0.7 (from flask)
      Downloading Werkzeug-0.11.4-py2.py3-none-any.whl (305kB)
        100% |████████████████████████████████| 307kB 898kB/s
    Collecting Jinja2>=2.4 (from flask)
      Downloading Jinja2-2.8-py2.py3-none-any.whl (263kB)
        100% |████████████████████████████████| 266kB 219kB/s
    Collecting itsdangerous>=0.21 (from flask)
      Downloading itsdangerous-0.24.tar.gz (46kB)
        100% |████████████████████████████████| 49kB 231kB/s
    Collecting MarkupSafe (from Jinja2>=2.4->flask)
      Downloading MarkupSafe-0.23.tar.gz
    Building wheels for collected packages: flask, itsdangerous, MarkupSafe
      Running setup.py bdist_wheel for flask ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/d2/db/61/cb9b80526b8f3ba89248ec0a29d6da1bb6013681c930fca987
      Running setup.py bdist_wheel for itsdangerous ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/97/c0/b8/b37c320ff57e15f993ba0ac98013eee778920b4a7b3ebae3cf
      Running setup.py bdist_wheel for MarkupSafe ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/94/a7/79/f79a998b64c1281cb99fa9bbd33cfc9b8b5775f438218d17a7
    Successfully built flask itsdangerous MarkupSafe
    Installing collected packages: Werkzeug, MarkupSafe, Jinja2, itsdangerous, flask
    Successfully installed Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.4 flask-0.10.1 itsdangerous-0.24
    $ flask/bin/pip install flask-login
    Collecting flask-login
      Downloading Flask-Login-0.3.2.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): Flask in ./flask/lib/python2.7/site-packages (from flask-login)
    Requirement already satisfied (use --upgrade to upgrade): itsdangerous>=0.21 in ./flask/lib/python2.7/site-packages (from Flask->flask-login)
    Requirement already satisfied (use --upgrade to upgrade): Werkzeug>=0.7 in ./flask/lib/python2.7/site-packages (from Flask->flask-login)
    Requirement already satisfied (use --upgrade to upgrade): Jinja2>=2.4 in ./flask/lib/python2.7/site-packages (from Flask->flask-login)
    Requirement already satisfied (use --upgrade to upgrade): MarkupSafe in ./flask/lib/python2.7/site-packages (from Jinja2>=2.4->Flask->flask-login)
    Building wheels for collected packages: flask-login
      Running setup.py bdist_wheel for flask-login ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/33/98/db/c54c7ae0bcf80e660b49ee5b1019fceb725898f6102c671462
    Successfully built flask-login
    Installing collected packages: flask-login
    Successfully installed flask-login-0.3.2
    $ flask/bin/pip install flask-openid
    Collecting flask-openid
      Downloading Flask-OpenID-1.2.5.tar.gz (43kB)
        100% |████████████████████████████████| 45kB 183kB/s
    Requirement already satisfied (use --upgrade to upgrade): Flask>=0.3 in ./flask/lib/python2.7/site-packages (from flask-openid)
    Collecting python-openid>=2.0 (from flask-openid)
      Downloading python-openid-2.2.5.tar.gz (301kB)
        100% |████████████████████████████████| 303kB 171kB/s
    Requirement already satisfied (use --upgrade to upgrade): itsdangerous>=0.21 in ./flask/lib/python2.7/site-packages (from Flask>=0.3->flask-openid)
    Requirement already satisfied (use --upgrade to upgrade): Werkzeug>=0.7 in ./flask/lib/python2.7/site-packages (from Flask>=0.3->flask-openid)
    Requirement already satisfied (use --upgrade to upgrade): Jinja2>=2.4 in ./flask/lib/python2.7/site-packages (from Flask>=0.3->flask-openid)
    Requirement already satisfied (use --upgrade to upgrade): MarkupSafe in ./flask/lib/python2.7/site-packages (from Jinja2>=2.4->Flask>=0.3->flask-openid)
    Building wheels for collected packages: flask-openid, python-openid
      Running setup.py bdist_wheel for flask-openid ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/3f/b3/e7/a41e337ee6d5bad62f30bbad73a9f0f831c0dd5096f1dae0f9
      Running setup.py bdist_wheel for python-openid ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/a6/bc/dd/01dd5a43edd00c8f8660e3d51a3ef4eb685c741a7a6032e27e
    Successfully built flask-openid python-openid
    Installing collected packages: python-openid, flask-openid
    Successfully installed flask-openid-1.2.5 python-openid-2.2.5
    $ flask/bin/pip install flask-mail
    Collecting flask-mail
      Downloading Flask-Mail-0.9.1.tar.gz (45kB)
        100% |████████████████████████████████| 49kB 269kB/s
    Requirement already satisfied (use --upgrade to upgrade): Flask in ./flask/lib/python2.7/site-packages (from flask-mail)
    Collecting blinker (from flask-mail)
      Downloading blinker-1.4.tar.gz (111kB)
        100% |████████████████████████████████| 114kB 301kB/s
    Requirement already satisfied (use --upgrade to upgrade): itsdangerous>=0.21 in ./flask/lib/python2.7/site-packages (from Flask->flask-mail)
    Requirement already satisfied (use --upgrade to upgrade): Werkzeug>=0.7 in ./flask/lib/python2.7/site-packages (from Flask->flask-mail)
    Requirement already satisfied (use --upgrade to upgrade): Jinja2>=2.4 in ./flask/lib/python2.7/site-packages (from Flask->flask-mail)
    Requirement already satisfied (use --upgrade to upgrade): MarkupSafe in ./flask/lib/python2.7/site-packages (from Jinja2>=2.4->Flask->flask-mail)
    Building wheels for collected packages: flask-mail, blinker
      Running setup.py bdist_wheel for flask-mail ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/60/fd/d1/085467d24b655541d08e54b18d667a19b5e6e2bcbfbcf40cc9
      Running setup.py bdist_wheel for blinker ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/f2/c3/e5/ee0b56ce61cac874efcee8d98a9a9e9415cb8e229cde993c7f
    Successfully built flask-mail blinker
    Installing collected packages: blinker, flask-mail
    Successfully installed blinker-1.4 flask-mail-0.9.1
    $ flask/bin/pip install flask-sqlalchemy
    Collecting flask-sqlalchemy
      Using cached Flask-SQLAlchemy-2.1.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): Flask>=0.10 in ./flask/lib/python2.7/site-packages (from flask-sqlalchemy)
    Collecting SQLAlchemy>=0.7 (from flask-sqlalchemy)
      Downloading SQLAlchemy-1.0.12.tar.gz (4.7MB)
        100% |████████████████████████████████| 4.8MB 106kB/s
    Requirement already satisfied (use --upgrade to upgrade): itsdangerous>=0.21 in ./flask/lib/python2.7/site-packages (from Flask>=0.10->flask-sqlalchemy)
    Requirement already satisfied (use --upgrade to upgrade): Werkzeug>=0.7 in ./flask/lib/python2.7/site-packages (from Flask>=0.10->flask-sqlalchemy)
    Requirement already satisfied (use --upgrade to upgrade): Jinja2>=2.4 in ./flask/lib/python2.7/site-packages (from Flask>=0.10->flask-sqlalchemy)
    Requirement already satisfied (use --upgrade to upgrade): MarkupSafe in ./flask/lib/python2.7/site-packages (from Jinja2>=2.4->Flask>=0.10->flask-sqlalchemy)
    Building wheels for collected packages: flask-sqlalchemy, SQLAlchemy
      Running setup.py bdist_wheel for flask-sqlalchemy ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/79/20/fe/49ca207b5445eacf8f34ac4c7f2365dd344e17668cd63b8fdd
      Running setup.py bdist_wheel for SQLAlchemy ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/ac/7f/71/77897285b1a0d19e2251d87cbc8f6f215b2d0be05ef36a3a5f
    Successfully built flask-sqlalchemy SQLAlchemy
    Installing collected packages: SQLAlchemy, flask-sqlalchemy
    Successfully installed SQLAlchemy-1.0.12 flask-sqlalchemy-2.1
    $ flask/bin/pip install sqlalchemy-migrate
    Collecting sqlalchemy-migrate
      Downloading sqlalchemy_migrate-0.10.0-py2-none-any.whl (108kB)
        100% |████████████████████████████████| 110kB 29kB/s
    Collecting pbr<2.0,>=1.3 (from sqlalchemy-migrate)
      Downloading pbr-1.8.1-py2.py3-none-any.whl (89kB)
        100% |████████████████████████████████| 90kB 201kB/s
    Collecting six>=1.7.0 (from sqlalchemy-migrate)
      Downloading six-1.10.0-py2.py3-none-any.whl
    Requirement already satisfied (use --upgrade to upgrade): SQLAlchemy!=0.9.5,>=0.7.8 in ./flask/lib/python2.7/site-packages (from sqlalchemy-migrate)
    Collecting sqlparse (from sqlalchemy-migrate)
      Downloading sqlparse-0.1.18.tar.gz (58kB)
        100% |████████████████████████████████| 61kB 2.2MB/s
    Collecting decorator (from sqlalchemy-migrate)
      Downloading decorator-4.0.9-py2.py3-none-any.whl
    Collecting Tempita>=0.4 (from sqlalchemy-migrate)
      Downloading Tempita-0.5.2.tar.gz
    Building wheels for collected packages: sqlparse, Tempita
      Running setup.py bdist_wheel for sqlparse ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/f0/78/f3/329eab6612e4c39684d45feeab48be7b992b1131a2b504339b
      Running setup.py bdist_wheel for Tempita ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/2f/70/ff/ffaf15f2ae7e8ccee9a6024295c3100a72a497fbaa0ae7890f
    Successfully built sqlparse Tempita
    Installing collected packages: pbr, six, sqlparse, decorator, Tempita, sqlalchemy-migrate
    Successfully installed Tempita-0.5.2 decorator-4.0.9 pbr-1.8.1 six-1.10.0 sqlalchemy-migrate-0.10.0 sqlparse-0.1.18
    $ flask/bin/pip install flask-whooshalchemy
    Collecting flask-whooshalchemy
      Downloading Flask-WhooshAlchemy-0.56.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): Flask-SQLAlchemy in ./flask/lib/python2.7/site-packages (from flask-whooshalchemy)
    Requirement already satisfied (use --upgrade to upgrade): SQLAlchemy in ./flask/lib/python2.7/site-packages (from flask-whooshalchemy)
    Collecting Whoosh (from flask-whooshalchemy)
      Downloading Whoosh-2.7.2-py2.py3-none-any.whl (468kB)
        100% |████████████████████████████████| 471kB 757kB/s
    Requirement already satisfied (use --upgrade to upgrade): blinker in ./flask/lib/python2.7/site-packages (from flask-whooshalchemy)
    Requirement already satisfied (use --upgrade to upgrade): Flask>=0.10 in ./flask/lib/python2.7/site-packages (from Flask-SQLAlchemy->flask-whooshalchemy)
    Requirement already satisfied (use --upgrade to upgrade): itsdangerous>=0.21 in ./flask/lib/python2.7/site-packages (from Flask>=0.10->Flask-SQLAlchemy->flask-whooshalchemy)
    Requirement already satisfied (use --upgrade to upgrade): Werkzeug>=0.7 in ./flask/lib/python2.7/site-packages (from Flask>=0.10->Flask-SQLAlchemy->flask-whooshalchemy)
    Requirement already satisfied (use --upgrade to upgrade): Jinja2>=2.4 in ./flask/lib/python2.7/site-packages (from Flask>=0.10->Flask-SQLAlchemy->flask-whooshalchemy)
    Requirement already satisfied (use --upgrade to upgrade): MarkupSafe in ./flask/lib/python2.7/site-packages (from Jinja2>=2.4->Flask>=0.10->Flask-SQLAlchemy->flask-whooshalchemy)
    Building wheels for collected packages: flask-whooshalchemy
      Running setup.py bdist_wheel for flask-whooshalchemy ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/38/e6/87/f8baffb253d3cc9532893b7bb0559a91ab8e30786ac910ebfb
    Successfully built flask-whooshalchemy
    Installing collected packages: Whoosh, flask-whooshalchemy
    Successfully installed Whoosh-2.7.2 flask-whooshalchemy-0.56
    $ flask/bin/pip install flask-wtf
    Collecting flask-wtf
      Downloading Flask_WTF-0.12-py2-none-any.whl
    Requirement already satisfied (use --upgrade to upgrade): Werkzeug in ./flask/lib/python2.7/site-packages (from flask-wtf)
    Requirement already satisfied (use --upgrade to upgrade): Flask in ./flask/lib/python2.7/site-packages (from flask-wtf)
    Collecting WTForms (from flask-wtf)
      Downloading WTForms-2.1.zip (553kB)
        100% |████████████████████████████████| 557kB 598kB/s
    Requirement already satisfied (use --upgrade to upgrade): itsdangerous>=0.21 in ./flask/lib/python2.7/site-packages (from Flask->flask-wtf)
    Requirement already satisfied (use --upgrade to upgrade): Jinja2>=2.4 in ./flask/lib/python2.7/site-packages (from Flask->flask-wtf)
    Requirement already satisfied (use --upgrade to upgrade): MarkupSafe in ./flask/lib/python2.7/site-packages (from Jinja2>=2.4->Flask->flask-wtf)
    Building wheels for collected packages: WTForms
      Running setup.py bdist_wheel for WTForms ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/06/b6/4a/6dae35583a348364dbdcc36ac0b742e05d698acee1010bcf5c
    Successfully built WTForms
    Installing collected packages: WTForms, flask-wtf
    Successfully installed WTForms-2.1 flask-wtf-0.12
    $ flask/bin/pip install flask-babel
    Collecting flask-babel
      Using cached Flask-Babel-0.9.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): Flask in ./flask/lib/python2.7/site-packages (from flask-babel)
    Collecting Babel>=1.0 (from flask-babel)
      Downloading Babel-2.2.0-py2.py3-none-any.whl (6.5MB)
        100% |████████████████████████████████| 6.5MB 58kB/s
    Collecting speaklater>=1.2 (from flask-babel)
      Downloading speaklater-1.3.tar.gz
    Requirement already satisfied (use --upgrade to upgrade): Jinja2>=2.5 in ./flask/lib/python2.7/site-packages (from flask-babel)
    Requirement already satisfied (use --upgrade to upgrade): itsdangerous>=0.21 in ./flask/lib/python2.7/site-packages (from Flask->flask-babel)
    Requirement already satisfied (use --upgrade to upgrade): Werkzeug>=0.7 in ./flask/lib/python2.7/site-packages (from Flask->flask-babel)
    Collecting pytz>=0a (from Babel>=1.0->flask-babel)
      Downloading pytz-2015.7-py2.py3-none-any.whl (476kB)
        100% |████████████████████████████████| 479kB 632kB/s
    Requirement already satisfied (use --upgrade to upgrade): MarkupSafe in ./flask/lib/python2.7/site-packages (from Jinja2>=2.5->flask-babel)
    Building wheels for collected packages: flask-babel, speaklater
      Running setup.py bdist_wheel for flask-babel ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/e7/b9/24/e618a5e9a381574e119d4e42252e1729c97a252920451313f9
      Running setup.py bdist_wheel for speaklater ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/33/fc/24/80d4d188859886015425b29007da5248f8d90ab2a7bcd06a72
    Successfully built flask-babel speaklater
    Installing collected packages: pytz, Babel, speaklater, flask-babel
    Successfully installed Babel-2.2.0 flask-babel-0.9 pytz-2015.7 speaklater-1.3
    $ flask/bin/pip install guess_language
    Collecting guess-language
      Downloading guess-language-0.2.tar.gz (98kB)
        100% |████████████████████████████████| 102kB 173kB/s
    Building wheels for collected packages: guess-language
      Running setup.py bdist_wheel for guess-language ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/6e/71/91/a5244b78ce34b62f74f026013d4bbb6c088390af4bdbd0e358
    Successfully built guess-language
    Installing collected packages: guess-language
    Successfully installed guess-language-0.2
    $ flask/bin/pip install flipflop
    Collecting flipflop
      Downloading flipflop-1.0.tar.gz
    Building wheels for collected packages: flipflop
      Running setup.py bdist_wheel for flipflop ... done
      Stored in directory: /Users/yan/Library/Caches/pip/wheels/66/c1/b8/753a091d7edab54053605ae4a4fe004bcf11666f5c59cedddf
    Successfully built flipflop
    Installing collected packages: flipflop
    Successfully installed flipflop-1.0
    $ flask/bin/pip install coverage
    Collecting coverage
      Downloading coverage-4.0.3-cp27-none-macosx_10_10_x86_64.whl (158kB)
        100% |████████████████████████████████| 159kB 134kB/s
    Installing collected packages: coverage
    Successfully installed coverage-4.0.3

### Hello world!

    $ mkdir app
    $ mkdir app/static
    $ mkdir app/templates
    $ mkdir tmp

>The app folder will be where we will put our application package. The static sub-folder is where we will store static files like images, javascripts, and cascading style sheets. The templates sub-folder is obviously where our templates will go.

- app/\_\_init\_\_.py

        from flask import Flask

        app = Flask(__name__)
        from app import views

The script above simply creates the application object (of class Flask) and then imports the views module, which we haven't written yet. Do not confuse app the variable (which gets assigned the Flask instance) with app the package (from which we import the views module).            

The views are the handlers that respond to requests from web browsers or other clients. In Flask handlers are written as Python functions. Each view function is mapped to one or more request URLs.

- app/views.py

        from app import app

        @app.route('/')
        @app.route('/index')
        def index():
            return 'Hello, World!'

The two route decorators above the function create the mappings from URLs / and /index to this function.

- run.py

        #!flask/bin/python

        from app import app
        app.run(debug=True)

启动

    $ flask/bin/python run.py
     * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
     * Restarting with stat
     * Debugger is active!
     * Debugger pin code: 714-895-622
    127.0.0.1 - - [24/Feb/2016 14:55:38] "GET / HTTP/1.1" 200 -
    127.0.0.1 - - [24/Feb/2016 14:55:38] "GET /favicon.ico HTTP/1.1" 404 -
    127.0.0.1 - - [24/Feb/2016 14:57:34] "GET /index HTTP/1.1" 200 -


