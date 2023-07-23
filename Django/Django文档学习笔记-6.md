# Django文档学习笔记-6



## 自定义应用的界面和风格

在`polls`文目录下创建一个`static`目录

```css
<!--polls/static/polls/style.css-->
li a {
    color: green
}
```

在`polls/templates/polls/index.html`头文件中添加以下内容

```html
{% load static %}
    <link rel="stylesheet" href="{% static 'polls/style.css' %}">
```

### 添加一个背景图

在`static`中创建一个`images`目录，将背景图存储在其中

```css
body {
    background: white url("images/background.png");
}
```



以上为界面和风格的简单介绍

