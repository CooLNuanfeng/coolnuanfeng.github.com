---
layout: post
title:  "Django笔记第一部分"
date:   2015-4-3
description:  整理Django的简单使用笔记
categories:
- blog
permalink: django-partOne
---

### **Django**
Django 是一个可以使Web开发工作愉快并且高效的Web开发框架。 使用Django，使你能够以最小的代价构建和维护高质量的Web应用。

学习中用到的版本是 django-1.6.10，Python版本为2.7.6。这里整理下学习过程中遇到的问题和解决方法，方便以后快速回忆。

####开始一个项目

转到相应的创建目录，并运行命令django-admin.py startproject +项目名，如：

    django-admin.py startproject mysite

运行后在当前目录中会生成如下内容文件：
    
    .
    └── mysite
        ├── manage.py
        └── mysite
            ├── __init__.py
            ├── settings.py
            ├── urls.py
            └── wsgi.py
    2 directories, 5 files

 1. \__init__.py ：让 Python 把该目录当成一个开发包 (即一组模块)所需的文件。 这是一个空文件，一般你不需要修改它。
 2. manage.py ：一种命令行工具，允许你以多种方式与该 Django 项目进行交互。 键入python manage.py help，看一下它能做什么。 你应当不需要编辑这个文件；在这个目录下生成它纯是为了方便。
 3. settings.py ：该 Django 项目的设置或配置。 查看并理解这个文件中可用的设置类型及其默认值。
 4. urls.py：Django项目的URL设置。 可视其为你的django网站的目录。 目前，它是空的。


####运行服务器

命令行执行下面的命令，即可启动服务。

    python manage.py runserver

默认情况下， runserver 命令在 8000 端口启动开发服务器，且仅监听本地连接。 要想要更改服务器端口的话，可将端口作为命令行参数传入

通过指定一个 IP 地址，你可以告诉服务器–允许非本地连接访问。 如果你想和其他开发人员共享同一开发站点的话，该功能特别有用。 `` 0.0.0.0`` 这个 IP 地址，告诉服务器去侦听任意的网络接口，命令如下：

    python manage.py runserver 0.0.0.0:8000


####如何创建一个简单的视图

在mysite文件夹中，创建一个叫做views.py的空文件，与urls.py、setting.py等在同级目录中。这个Python模块将包含这一章的视图。 请留意，Django对于view.py的文件命名没有特别的要求，它不在乎这个文件叫什么。但是根据约定，把它命名成view.py是个好主意，这样有利于其他开发者读懂你的代码，正如你很容易的往下读懂本文。

1、view.py 添加两个视图函数分别为hello 和home

    from django.http import HttpResponse
    def hello(request):
       return HttpResponse("Hello world")
    def home(request):
       return HttpResponse('This is Home Page.')
    
2、urls.py 配置正则路径对应的视图函数

    from django.conf.urls import patterns, include, url
    from mysite.view import hello,home
    urlpatterns = patterns('',
        url('^$', home),
        url('^hello/$', hello),
    )
    

PS : from mysite.view import hello,home  中的每一个视图函数要与下面的url对应

- view.py 文件为视图文件，通过在其中定义视图函数来完成我们的视图。
- urls.py 文件为路由文件，通过在其中设置路由正则来指定不同的url用不同的视图函数来渲染。


#### 引入外部的模板文件

1、修改view.py文件的内容如下：

    from django.shortcuts import render_to_response
    import datetime

    def current_datetime(request):
        now = datetime.datetime.now()
        return render_to_response('mysite-tmp.html',{'current_date': now,'content' : 'hello world'})

其中 render_to_response 模块包含了 import HttpResponse 和 import template 两个模块。import datetime 为django中的获取时间模块

2、修改urls.py文件内容如下：

    from django.conf.urls import patterns, include, url
    from mysite.view import current_datetime
    urlpatterns = patterns('',
        url('^time/$', current_datetime),
    )
    
3、创建templates文件夹，用于存放模板文件，在templates下创建mysite-tmp.html 模板文件，内容如下：

    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF-8">
    <title>Document</title>
    </head>
    <body>
    <h1> \{\{ current_date \}\} </h1>
    <p> \{\{ content \}\} </p>
    </body>
    </html>
    
4、配置setting.py文件，设置template的模板路径。
在setting.py中找到 import os 模块，在其下方加入如下内容：

    import os
    BASE_DIR = os.path.dirname(os.path.dirname(__file__))
    TEMPLATE_DIRS = (
        BASE_DIR+'/templates',
    )

此时整个文件的目录结构如下：

    .
    ├── manage.py
    ├── mysite
    │   ├── __init__.py
    │   ├── __init__.pyc
    │   ├── settings.py
    │   ├── settings.pyc
    │   ├── urls.py
    │   ├── urls.pyc
    │   ├── view.py
    │   ├── view.pyc
    │   ├── wsgi.py
    │   └── wsgi.pyc
    └── templates
        └── mysite-tmp.html
    
    2 directories, 12 files

####链接MySQL数据库

1、修改view.py 内容如下：
    
    from django.shortcuts import render_to_response
    import datetime
    import MySQLdb

    def current_datetime(request):
        now = datetime.datetime.now()
        return render_to_response('mysite-tmp.html', {'current_date': now,'content' : 'hello world'})

    def userInfo(request):
        db = MySQLdb.connect(user='root', db='mydata', passwd='123456', host='localhost')
        cursor = db.cursor()
        cursor.execute('SELECT username,age FROM users')
        name = [row[0] for row in cursor.fetchall()]
        age = [row[1] for row in cursor.fetchall()]
        db.close()
        return render_to_response('mysite-tmp.html', {'username': name,'age' : age})


2、urls.py修改为如下内容：

    from django.conf.urls import patterns, include, url
    from mysite.view import current_datetime,userInfo
    urlpatterns = patterns('',
        url('^time/$', current_datetime),
        url('^user/$',userInfo),
    )

3、配置setting.py中的DATABASES内容为：
    
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.mysql',
            'NAME': 'mydata',
        }
    }

其中设置django数据库为MySQL，要连接的数据库名字为 mydata。

以上为django学习的第一部分，简单介绍了如何简单使用django制作页面，并从数据库读取数据渲染页面显示。更多更详细的内容请参考[这里](http://djangobook.py3k.cn/2.0/)