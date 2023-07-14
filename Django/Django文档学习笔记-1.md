# Django文档学习笔记-1

[TOC]

* 基于Django4.2文档的学习笔记

## Django概述

1. 采用MTV框架模式（模型Model，视图View，模板Template）



* 创建项目(不一定要叫`mysite`)

```
django-admin startproject mysite
```



* 关于项目文件的解释

```python
py_mysite # 项目名称
  polls			  	# 应用名称
    migratons		# 用于存储应用的数据表结构的指令，通过这些指令可以修改和创建数据库，从而在models.py模型类和数据库表之间迁移
	__init__.py
	admin.py	    # 后台管理
	apps.py
	models.py		# 模型层，和数据库打交道，与数据库交互
	tests.py		# 测试文件
	urls.py			# 子路由（应用路由）
	views.py		# 视图层
  py_mysite
    __init__.py
    asgi.py			# 异步网关协议接口
    settings.py		# 配置文件
    urls.py			# 路由（总路由） 与视图函数建立对应关系
    wsgi.py			# wsgiref模块，web服务器网关接口
  templates			# 模板层，存储html文件
  manage.py			# 入口文件，启动文件
```

创建一个Django项目之后，会生成第一个文件夹`mystie`（一般是你创建的Django项目存放的文件夹名字），会包含几个Py文件

> `mysite/__init__.py`：空文件，告诉`Python`把`mysite`目录看成一个模块
>
>  `mysite/urls.py`：网址入口，关联到`views.py`中的一个函数（或者`generic`类），访问网址就对应一个函数。存放URL模式的地方，这里定义的每个URL都会被映射为一个`view`。
>
> `mysite/settings.py`：Django的配置文件，比如DEBUG开关，静态文件的位置等。
>
> `mysite/wsgi.py`：配置你的项目，让它作为一个WSGI程序来运行。
>
> `mysite/asgi.py`：相当于wsgi的升级版本，可以让Django支持异步视图，增大Django的并发量。



> `views.py`：处理用户发出的请求，从`urls.py`中对应过来，通过渲染`templates`中的网页可以将显示内容（比如登录后的用户名，用户请求的数据等）输出到网页。
>
> `models.py`：与数据库操作相关，存入或读取数据时用到。
>
> `admin.py`：后台，可以用很少量的代码就拥有一个强大的后台。



> `templates`文件夹：views.py中的函数渲染`templates`中的HTML模板，得到动态网页的内容，可以用缓存来提高速度。



* 启动服务器（用于开发的简易服务器）

```shell
py manage.py runserver
```



## 1. 创建投票应用（第一个项目）

```shell
py manage.py startapp polls
```



* **函数**

1. `path(route, view, [**kwargs, name=none])`：解析URL地址

> `route`：
>
> * 表述路径，从`urlpatterns`的第一项开始匹配，直到找到匹配的项
>
> `view`：
>
> * 表示route匹配成功后，需要调用的视图，view必须是一个函数
>
> `**kwargs` 表示一个字典，传递给view函数
>
> `name`表示别名



2. `include(module, namespace=None)`

> `module`：子url模块的字符串
>
> `namespace`：实例命名空间
>
> * 注意：在使用实例命名空间之前，必须先制定一个应用命名空间



## 2. 关于数据库配置

在`mysite/settings.py`中的`DATABASES 'default'`进行设置

* `ENGINE`
  * `'django.db.backends.sqlite3'`，`'django.db.backends.postgresql'`，`'django.db.backends.mysql'`，或 `'django.db.backends.oracle'`。其它 [可用后端](https://docs.djangoproject.com/zh-hans/4.2/ref/databases/#third-party-notes)。
* `NAME`：数据库的名称

如果不使用`SQLite`，则需要添加额外配置，比如`user`、`passward`、`host`等。想了解更多数据库设置方面的内容，请看文档：[`DATABASES`](https://docs.djangoproject.com/zh-hans/4.2/ref/settings/#std-setting-DATABASES) 。



注意：

* 设置`TIME_ZONE`调整时区 -- `Asia/Shanghai`
* 可以通过`LANGUAGE_CODE`调整语言 --`zh-hans`



在`mysite`文件夹中打开命令终端，并运行以下代码，根据`mysite/settings.py`中的数据库配置和随着应用提供的数据库迁移文件，创建必要数据库表。

```shell
py manage.py migrate
```



## 3. 创建模型

```python
# polls/models.py
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```



## 4. 激活模型

配置类`INSTALLED_APPS`中添加设置。`PollsConfig`类位于文件`polls/app.py`中

* `"Polls.apps.PollsConfig"`



在命令终端中运行

```
py manage.py makemigrations polls
```

通过`makemigrations`命令，Django会检测你对`models.py`文件的修改，并把修改的部分存储为一次`迁移`。



```
py manage.py sqlmigrate polls 0001
```

`sqlmigrate`命令接收一个迁移的名称，返回对应的SQL。



```
py manage.py check
```

检查项目中的问题，并且在检查过程中不会对数据库进行任何操作。



```
py manage.py migrate
```

运行`migrate`命令，在数据库里创建新定义的模型的数据表（选中所有还没有执行过的迁移，将对应的模型更改同步到数据库结构上）

**迁移**

能够让你在开发过程中持续的改变数据库结构，并且不需要重新删除和创建表

改变模型需要三步（目前只需要记住这些）：

* 编辑`models.py`文件，改变模型。
* 运行`python manage.py makemigrations`为模型的改变生成迁移文件。
* 运行`python manage.py migrate`来应用数据库迁移。

