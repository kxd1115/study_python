# Django文档学习笔记-3

[TOC]

## 1. 编辑更多视图

* `request`

1. 当一个页面被请求时，Django会创建一个包含本次请求原信息的HttpRequest对象。

2. Django会将这个对象自动传递给响应的视图函数，一般视图函数约定俗成的使用`request`参数承接这个对象。



向`polls/views.py`中添加更多视图

```python
# polls/views.py

from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")

# 添加新视图
def detail(request, question_id):
    return HttpResponse("You're looking at queston %s." % question_id)

def results(request, question_id):
    request = "You're looking at the results of queston %s."
    return HttpResponse(request % question_id)

def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```



把新的视图添加到`polls.urls`模块中

```python
# polls/urls.py
from django.urls import path
from . import views

urlpatterns = [
    # 举例：/polls/
    path("", views.index, name="index"),
    # 举例: /polls/1/
    path("<int: question_id>/", views.detail, name="detail"),
    # 举例：/polls/1/results/
    path("<int: question_id>/results/", views.results, name="results"),
    # 举例: /polls/5/vote/
    path("<int:question_id>/vote/", views.vote, name="vote"),
]
```

* 启动服务器查看是否生效



## 2. 写一个真正有用的视图

视图默认必须要做的两件事

1. 返回被请求页面内容的`HttpResponse`对象
2. 抛出一个异常，比如`Http404`

* 非必须事项：其他任何你想要做的事情



修改`polls/views.py`的`index()`

```python
# polls/views.py
from .models import Question

def index(request):
    # 查找最新的5个问题
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    # 将最新的5个问题添加到q.question_text
    output = ", ".join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
```



在`polls`目录中新建一个`templates`目录，用来存放`html`模板文件。

模版路径: `polls/templates/polls/index.html`

* 在templates中再创建一个`polls`，这样做的目的主要是为了帮助Django区分重名的模版

```html
<!--polls/templates/polls/index.html-->
{% if latest_question_id %}
	<ul>
		{% for question in latest_question_list %}
        	<li>
        		<a href="/polls/{{ question.id }}">{{ question.question_text }}</a>
        	</li>
        {% endfor %}
	</ul>
{% else %}
	<p>No polls are available.</p>
{% endif %}
```



然后更新一下`polls/views.py`中的`index()`函数方便使用新增的模版文件

```python
from django.http import HttpResponse
from .models import Question

from django.shortcuts import render

def index(request):
    # 查找最新的5个问题
    latest_question_list = Question.objects.order_by("-pub_date")[:5]
    
    # 解析模版
    context = {
        "latest_question_list": latest_question_list,
    }
    return render(request, "polls/index.html", context)
```



* `render(reuqest, template_name, context=None, content_type=None, status=None, using=None)`

* 通过`render()`函数，将给定的模版与上下文字典结合在一起，将渲染的文本以`HttpResponse`对象的形式返回
  * `request`：必填，用于生成此响应的对象
  * `template_name`：必填，模版的名称
  * `context`：要添加到模版上下文的值的字典，将模版内的变量映射为Python对象
  * `status`：响应状态码，默认200



## 抛出404错误

处理`detail`视图

```python
# polls/views.py
from django.http import Http404
from django.shortcuts import get_object_or_404, render
from .models import Queston

def detail(request, question_id):
    # 如果对应的问题ID不存在，则会抛出指定异常
    question = get_object_or_404(Question, pk=question_id)
    return render(request, "polls/detail.html", {"question": question})
```

在`detail.html`中添加以下代码方便测试

```html
<!--polls/templates/polls/detail.html-->
{{ question }}
```



* `get_object_or_404(klass, *args, **kwargs)`

  引发404异常

  * `klass`：获取对象的`Model`类，`Manger`，或者`Queryset`实例
  * `*args`：`Q objects`
  * `**kwargs`：查询参数，应采用`get()`和`filter()`接受的格式

与该函数相似的还有`get_list_or_404()`



## 使用模版系统

```html
<!--polls/templates/polls/detail.html-->
<h1>{{ queston.question_text }}</h1>
<ul>
	{% for choice in question.choice_set_all %}
    	<li>{{ choice.choice_text }}</li>
    {% endfor %}
</ul>
```



## 去除模版中的硬编码URL

调整在`polls/index.html`中关于投票链接的硬编码

* 原版本：硬编码URL对于一个包含很多应用的项目来说，修改起来十分困难

```html
<!--polls/index.html-->
<li><a href="/polls/{{ question.id }}">{{ question.question_text }}</a></li>
```

* 调整1：使用`{% url %}`代替，在`polls.urls`模块中寻找具有指定名字的条目

```html
<!--polls/index.html-->
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```



## 为URL名称添加命名空间

教学文档中只有一个应用`polls`，实际项目中的应用数量可能会很多。添加命名空间是为了方便Django区分重名的URL。



修改`polls/urls.py`

```python
from django.urls import path
from . import views

# 添加命名空间
app_name = "polls"

urlpatterns = [
    # 举例：/polls/
    path("", views.index, name="index"),

    # 举例: /polls/1/
    path("<int:question_id>/", views.detail, name="detail"),
    # 举例：/polls/1/results/
    path("<int:question_id>/results/", views.results, name="results"),
    # 举例: /polls/5/vote/
    path("<int:question_id>/vote/", views.vote, name="vote"),
]
```

调整`polls/index.html`文件：指向具有命名空间的详细视图

```html
<!--polls/index.html-->
<li><a href="{% url 'polls: detail' question.id %}">{{ question.question_text }}</a></li>
```

