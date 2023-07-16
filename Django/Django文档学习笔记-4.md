# Django文档学习笔记-4

[TOC]

## 1. 编写一个简单的表单

```html
<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
    <fieldset>
        <legend><h1>{{ question.question_text }}</h1></legend>
            {% if error_message %}
                <p><strong>{{ error_message }}</strong></p>
            {% endif %}
            {% for choice in question.choice_set.all %}
                <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
                <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
            {% endfor %}
    </fieldset>
    <input type="submit" value="Vote">
</form>
```

* 使用`method="post"`非常重要，因为提交这个表单的行为将改变服务器端的数据。
  * 当你创建一个改变服务器端数据的表单时，使用`post`

* `forloop.counter`表示`for`标签的循环次数
* 所有针对内部URL的POST表单都应该使用`{% csrf_token %}`模版标签



修改`polls/views.py`中的`vote`视图

```python
# polls/views.py
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST["choice"])
    except (KeyError, Choice.DoesNotExist):
        # 重新显示表单内容
        context = {
            "question": question,
            "error_message": "You didn't select a choice.",
        }
        return render(request, "polls/detail.html", context)
    else:
        selected_choice.votes += 1
        selected_choice.save()
        return HttpResponseRedirect(reverse("polls:results", args=(question.id, )))
```

* `request.POST`是一个类字典对象，返回的值永远是字符串格式
  * 该例子以字符串的形式返回`Choice`的`ID`

​		`Django`中同样存在`request.GET`方法

* 通过`Keterror`检查`request.POST`是否提供`choice`
* `HttpResponseRedirect`只接受一个参数：用户将要被重新定向的URL
  * 在成功处理POST数据后，应该总是返回一个`HttpResponseRedirect`
* `reverse()`函数避免了我们在视图函数中硬编码URL。它提供了我们想要跳转的视图名字和对应的URL模式中需要给该视图提供的参数。
  * 在本例中，`reverse()`调用的返回是`/polls/3/results/`



修改`/polls/views.py`中的`results`视图

```python
def results(request, question_id):
	question = get_object_or_404(Question, pk=question_id)
    return render(request, "polls/results.html", {"question": question})
```



创建`/polls/templates/polls/results.html/`模版

```html
<h1>{{ question.question_text }}</h1>
<ul>
    {% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
    {% endfor %}
</ul>
<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```



## 使用通用视图：减少代码量

在本例中，部分视图存在高度相似的情况，有代码冗余。

`Django`提供一种`通用视图`系统，将常见的模式抽象化，可以使你在编写应用时用

> 本例中提到了一个`F()表达式`，能够避免竞争条件



改良`URLconf`

```python
# polls.urls.py
app_name = "polls"

urlpatterns = [
    # 举例：/polls/
    path("", views.IndexView.as_view(), name="index"),

    # 举例: /polls/1/
    path("<int:pk>/", views.DetailView.as_view(), name="detail"),
    # 举例：/polls/1/results/
    path("<int:pk>/results/", views.ResultsView.as_view(), name="results"),
    # 举例: /polls/5/vote/
    path("<int:question_id>/vote/", views.vote, name="vote"),
]
```



改良视图`views.py`

```python
# polls/views.py
from django.views import generic

class IndexView(generic.ListView):
    template_name = "polls/index.html"
    context_object_name = "lastest_question_list"
    
    def get_queryset(self):
        # 返回最新提交的5个问题
        return Question.objects.order_by("-pub_date")[:5]
    
class DetailView(generic.DetailView):
    model = Question
    template_name = "polls/detail.html"

class ResultsView(generic.DetailView):
    model = Question
    template_name = "polls/results.html"
```

* `ListView`显示一个对象列表

* `DetailView`显示一个特定类型对象列表的详细信息页面

  * 模型类型由`model`属性提供
  * `DetailView`从URL中捕获主键值`pk`

  * `template_name`属性是用来告诉`Django`待使用的模版的名字
  * 在`ListView`中使用`context_object_name `属性，改变context的指向，表示我们想使用`lastest_question_list`
    * 这是便捷方法，直接告诉`Django`使用你想使用的变量名。



