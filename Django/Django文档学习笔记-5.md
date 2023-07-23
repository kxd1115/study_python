# Django文档学习笔记-5

[TOC]

## 自动化测试简介

### 什么是自动化测试

测试代码，是用来检查你的代码能够正常运行的程序。



### 为什么需要写测试

1. 节约时间
2. 发现错误
3. 预防错误
4. 让代码更具有吸引力
5. 更有利于团队协作

一些开发者会遵循`测试驱动`原则，在写代码之前先写测试



## 开始写第一个测试

在本例中，我们的要求是：如果`Question`是在一天之类发布的，`Question.was_published_recently()`方法会返回`TRUE`，但目前这个方法在`Question`的`pub_date`字段比当前时间还晚时也会返回`TRUE`（这是个`BUG`）

```python
import datetme
from django.utils import timezone
from polls.models import Question

future_question = Question(pub_date=timezone.now()+datetime.timedelta(days=30))
future_question.was_published_recently()
```

> 该代码依然会返回`TRUE`



## 创建测试暴露这个BUG

```python
# polls/test.py
import datetime
from django.test import TestCase
from django.utils import timezone

from .models import Question

class QuestionModelTests(TestCase):
    def test_was_published_recently_with_future_qestion(self):
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
    	self.assertIs(future_question.was_published_recently(), False)
```

* `python manage.py test polls`将寻找`polls`应用中的测试代码 
* 使用`test_was_published_recently_with_future_qestion`方法创建一个`pub_date`值为30天后的`Question`的实例
* 使用`assertls()`方法，发现`was_published_recently()`返回了`TRUE`，我们期望的返回是`False`



## 修复BUG

```python
# polls/models.py

def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

再次运行`py manage.py test polls`

```
Creating test database for alias 'default'...
System check identified no issues (0 silenced).
.
----------------------------------------------------------------------
Ran 1 test in 0.016s

OK
Destroying test database for alias 'default'...
```



## 更全面的测试

在测试文件中新增两个测试方法

```python
# polls/tests.py
def test_was_published_recently_with_old_question(self):
    time = timezone.now() - datetime.timedelta(days=1, seconds=1)
    old_question = Question(pub_date=time)
    self.assertIs(old_question.was_published_recently(), False)
    
def test_was_published_recently_with_recent_question(self):
    time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
    recent_question = Question(pub_date=time)
    self.assertIs(recent_question.was_published_recently(), True)
```

通过三种测试来确保`Queston.was_published_recently()`方法对于过去，最近和将来的三种情况都返回正确的值



## 测试视图

### Django测试工具Client

```
py manage.py shell
```

```python
from django.test.utils import setup_test_environment
setup_test_environment() # 安装一个模版渲染器，方便我们检查响应上的一些额外属性

from django.test import Client
client = Client()

response = client.get("/")
response.status_code

from django.urls import reverse
response = client.get(reverse("polls:index"))
response.status_code

response.content
response.context["latest_question_list"] 
```

### 改善视图代码

修改`get_queryset()`方法

```python
# polls/view.py
def get_queryset(self):
    return Question.objects.filter(pub_date__lte=timezone.now()).order_by("-pub_date")[:5]
	# 返回一个早于或等于当前日期的问题中的最新的5个
```

### 测试新视图

```python
# polls/test.py
from django.urls import reverse

def create_question(question_text, days):
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)

class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        # 如果没有问题，则显示
        response = self.clientget(reverse("poll:index"))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerySetEqual(
            response.context["latest_question_list"], 
            []
        )
	
    def test_past_question(self):
        question = create_question(question_text="Past question.", days=30)
        response = self.client.get(reverse("polls:index"))
        self.assertQuerySetEqual(
            response.context["latest_question_list"], 
            [question], 
        )
    
    def test_future_question(self):
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse("polls:index"))
        self.assertContains(response, "No polls are available.")
        self.assertQuerySetEqual(
            response.context["latest_question_list"], 
            []
        )
    
    def test_future_question_and_past_question(self):
        question = create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse("polls:ndex"))
    	self.assertQuerySetEqual(
            response.context["latest_question_list"], 
            [question], 
        )
    
    def test_two_past_questions(self):
        question1 = create_question(question_text="Past question.", days=-30)
        question2 = create_question(question_text="Past question.", days=-5)
        response = self.client.get(reverse("polls:ndex"))
        self.assertQuerySetEqual(
            response.context["latest_question_list"], 
            [question2, question1], 
        )
```

* 创建一个快捷函数`created_question`
* `Django.test.TestCase`中提供了一些额外的断言方法
  * `assertContains()`
  * `assertQuerySetEqual()`



### 测试`DetailView`

在`DetailView`中增加一些约束，防止用户猜到正确的URL并访问他们

```python
# polls/views.py

class DetailView(generic.DetailView):
    ...
    def get_queryset(self):
        return Question.objects.filter(pub_date__lte=timezone.now())
```



增加测试检验`pub_date`是否能够在过去的`Question`中能够被显示出来，且未来的`pub_date`中不显示

```python
# polls/tests.py

class QuestionDetailViewTests(TestCase):
    def test_future_question(self):
        future_question = create_question(question_text="Future question.", days=5)
        url = reverse("polls:detail", args=(future_question.id,))
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)
    
    def test_past_question(self):
        future_question = create_question(question_text="Past question.", days=-5)
        url = reverse("polls:detail", args=(past_question.id,))
        response = self.client.get(url)
        self.assertContains(response, past_question.question_text)
```



关于测试的整体规划建议：

1. 对于每个模型和视图都建立单独的`TestClass`
2. 每个测试方法只测试一个功能
3. 给每个测试方法起一个能描述其功能的名字



## 深入代码测试

可以使用`selenium`来检查最终渲染出的HTML是否符合预期

`Django`中提供了`LiveServerTestCase`来和`Selenium`工具进行交互
