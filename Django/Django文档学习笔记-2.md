# Django文档学习笔记-2

[TOC]

## 1. 初识API

```
py manage.py shell
```

使用该命令编辑，`manage.py`会设置`DJANGO_SETTINGS_MODULE`环境变量，该变量会让Django跟进`mysite/settings.py`文件来设置Python包的导入路径。



* 部分Django中检索对象，查找对象的方法

`all()`：查询所有，并返回查询集

`get()`：能且只能返回一条数据

`filter()`：条件查询，可以通过逗号连接多个条件

`exclude()`：与`filter()`函数相反，查询所有不符合条件的数据

`order by()`：排序（-id表示降序）



运行`Py manage.py shell`命令，输入以下代码

```python
from polls.models import Choice, Queston
from django.utils import timezone

# 查看对象数量
Question.objects.all()

# 创建对象q
q = Question(question_text="What's new?", pub_date=timezone.now())
q.save() # 保存

# 查看是否创建成功
q.id
q.question_text
q.pub_date

# 修改对象部分变量内容
q.question_text = "What's up?"
q.save()

# 查看对象数量
Question.objects.all()
```



* 给模型增加`__str__()`方法，将返回的内容以字符串的形式输出

```python
import datetime
from django.db import models

from django.utils import timezone # 当启用了时区支持时，用来处理时间

# Create your models here.

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField("date published")

    def __str__(self):
        return self.question_text

    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

    def __str__(self):
        return  self.choice_text
```



运行`Py manage.py shell`命令，输入以下代码

```python
from polls.models import Question, Choice
from django.utils import timezone

# 确定__str__()方法是否生效
Queston.objects.all()

# 查询id为1的对象
Queston.objects.filter(id=1)

# 设置年份，并通过get查找到当前年份下创建的问题
curret_year = timezone.now().year
Question.objects.get(pub_date__year=current_year)

# 常见的方法是通过主键查找，Django提供了一个用于主键精确查到的快捷方式
# 该方法等同于Question.objects.get(id=1)
Question.objects.get(pk=1)


# 确定我们设置的自定义方法能够正常工作
q = Question.objects.get(id=1)
q.was_published_recently()

# 查看相关对象中的全部内容（目前为空）
q.choice_set.all()

# 创建三个选项
q.choice_set.create(choice_text='No much', votes=0)
q.choice_set.create(choice_text='The sky', votes=0)
c = q.choice_set.create(choice_text='Just hacking again', votes=0)

# 查看选项对应的问题对象
c.question

# 查看问题对应的全部选项对象，对象数量
q.choice_set.all()
q.choice_set.count()

# 重用current_year来查找pub_date是今年的所有选项对象
# 在Django中使用双下划线 __ 来分隔关系
Choice.objects.filter(question__pub_date__year=current_year)
```



## 2. Django管理界面



创建一个管理页面用户

```
py manage.py createsuperuesr
```

设置账户，邮箱，密码

登录界面会根据你设置的`language_code`展示对应的语言



使用`admin.site.register(Question)`向管理页面中加入投票应用

```python
#polls/admin.py
from .models import Question

# 导入模型类
admin.site.register(Question)
```



* 不同的字段类型会生成对应的HTML输入控件
  * 日期时间字段：`DateTimeField`
  * 字符字段：`CharField`

