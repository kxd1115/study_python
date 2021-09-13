### 打开文件的方式
##### open
    * r 读取文件；rb 读取二进制文件
    * w 写入内容，会覆盖之前文件中写入的内容；wb 写入二进制文件
    * a 写入内容，且不会覆盖文件内之前写入的内容
    * r+ 基于读的读写方式
    * w+ 基于写的读写方式

> read()函数 

    * 读取文件，可以指定n个字符进行读取。
> readline()函数

    * 读取文件，展示文件中的一行内容，使用一次之后再次使用时，会展示下一行数据

> readlines()函数

    * 可以以行的形式把文件中的所有内容读取出来，并保存到一个列表中

> 遍历文件指针对象

```python
fp = open('big_file.txt', 'r')
for line in fp:
    print(line)
```

> 获取当前文件指针所在的位置

    * tell()函数
> 改变文件指针的位置

    * seek(a, b)函数
    a：需要移动到的位置
    b：需要移动的位置
    说明：当b<>0时，a必须等于0

##### with open
```
with open('xxx.txt', 'a', encoding='utf-8') as fp:
    fp.write('oh my god!')

```
    * `with open`会自动帮助你在操作完成后关闭文件

```
# 实现文件拷贝
with open('xxx.txt', 'r') as fp1:
    with open('xxx[copy].txt', 'w') as fp2:
        for line in fp1.read():
            fp2.write(line)
```

---


### 1. 基本知识补充
##### 数据结构
###### list 列表 []
###### dict 字典 {key:value……}
> get(a, b)方法

     返回key(a)在字典中对应的value值，如果该key没有对应的value，则返回参数b中的数值

###### set 集合 ()
    > 不能包含重复的数值，没有特定顺序


### 2. 面向对象


##### 类和对象
###### 类：是对某一种物种的抽象表达。比如人类，动物，植物等
1. 定义一个类的语法，必须使用`class`关键字，`object`是所有类的基类。类中的所有方法第一个参数必须是`self`，代表当前的对象。

###### 对象：是对类的一种具体化。比如某一个人，某一种动物，某一种植物

```
# coding='utf-8'

class Person(object):
    '''类：创建一个类'''
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def eat(self):
        print("my name is %s, I'm %s years old, I like eat banana." % (self.name, self.age))

# 创建一个对象p1
p1 = Person("kangxiaodong", 25)  
p1.eat()
```

##### 访问限制
1. 受保护的属性和方法。一般用一个下划线开头；代表该属性不希望其他人进行更改，但其实可以更改。
2. 私有属性和方法。一般用两个下划线开头；代表该属性不可以更改。

```
# coding="utf-8"

class Women(object):

    def __init__(self, age, name):

        # 定义一个受保护的属性
        self._age = age

        # 定义私有属性
        self.__name = name


```
```
class Women(object):

    def __init__(self, age, name):
        # 定义一个受保护的属性
        self._age = age
        # 定义私有属性
        self.__name = name

    def __get_name(self):
        print("这是我的小秘密，答对我的名字我就告诉你！")
        print("你真聪明，我喜欢你！")

    def get_name(self, name1):
        '''利用新的方法在类中调用私有方法'''
        if name1 == self.__name:
            return self.__get_name()
        else:
            return None
```
3. 说明
    - 私有属性和方法并不是100%的不能访问，可以使用类似`w1._Women__name`代码来去访问私有属性和方法。（私有属性和方法不建议去强制更改）
    - `__init__`一般是Python的内置方法，不建议用这种模式来对自己创建的方法命名。

##### 析构函数
1. `__del__`方法，在该对象在内存中即将被清除的时候，会调用该方法。
```
class Name(object):

    def __init__(self, peploe):
        self.peploe = peploe
        self.fp = open("xxx.txt", 'w')

    def writer(self, message):
        self.fp.write(message)

    def __del__(self):
        print(u"这是析构函数")
        self.fp.close()
```
2. 引用计数。在对象内存被关闭之前，可以查询到有多少变量指向该对象，一般使用`sys.getrefcount(p)`来查询

##### 继承

- 当我们定义一个`class`的时候，可以从现有的`class`中继承，这种类我们一般称为`子类`，而被继承的类称为`父类`或者`基类`。

 ```
1. 重写父类中的方法
2. 如果需要使用父类中的部分方法，可以在子类中调用`supper()`方法。
# coding = "utf-8"

class Person(object):
    def __init__(self, age, name):
        self.age = age
        self.name = name

    def eat(self):
        print("人类在吃饭")


class Student(Person):
    def __init__(self, name, age):
        # super()函数可以用来调用父类中的初始化方法
        super(Student, self).__init__(name, age)
        print("初始化学生信息")

    def eat(self):
        print("学生在吃饭")

    def get(self):
        print("My name is %s, my age is %s" %(self.name, self.age))

s1 = Student(24, 'XIAODONG')
s1.eat()
s1.get()
# 会优先使用子类中的eat()方法，否则会执行父类中的eat()方法
 ```
- 子类无法继承父类的私有属性和方法
- 子类可以继承父类中受保护的属性和方法

##### 多继承

```
# coding = "utf-8"

class Ma(object):
    def __init__(self):
        print("我是马")

    def run(self):
        print("我擅长奔跑")

class Lv(object):
    def __init__(self):
        print("我是驴")

    def lamo(self):
        print("我会拉磨")

class Luozi(Ma, Lv):
    def __init__(self):
        print("我是骡子")
        
    def sleep(self):
        Lv.sleep() # 指定先执行驴子的sleep

luozi1 = Luozi()
luozi1
luozi1.run()
luozi1.lamo()

luozi1 = Luozi()
luozi1
luozi1.run()
luozi1.lamo()
luozi1.sleep() # 如果没有指定执行顺序，会根据多继承的父类顺序执行
```

##### 多态
- 在调用的时候不知道要调用哪个方法，在运行的时候才知道要调用哪一种（类似在游戏中选择某个角色，选择前不知道是哪个英雄）
```
# coding = "utf-8"

class Game():
    def stroke(self):
        pass

class Chengyaojin(Game):
    def stroke(self):
        print("程咬金要放大招了！")
        print("旋转，回血，我闭上眼~")

class Xiangyu(Game):
    def stroke(self):
        print("项羽要放大招了！")
        print("我推，我推，精油开背！")

hero = input("请输入英雄名字1/2：")
stroke = None
if hero == '1':
    stroke = Chengyaojin()
else:
    stroke = Xiangyu()
stroke.stroke()
# 不需要指定类名字，直接调用方法
```

##### 类属性和实例属性
- 绑定到对象上的属性就是实例属性。只在当前对象上有作用。
- 绑定在类上的，就是类属性。在当前类和子类中都可以有作用。
```
# coding = "utf-8"

class Person(object):

    country = "china"

    def __init__(self, name):
        self.name = name

p1 = Person("kang")
print(p1.country)
```
##### 类方法和静态方法
> 类方法
- 类方法需要使用装饰器`@classmethod`，且第一个参数必须是`cls`，可以访问类属性。类方法可以被实例调用。不随实例属性的变化而变化。
- 实例方法只能被实例调用，且随着实例的属性改变而改变。
```
# coding="utf-8"

class Person(object):

    country = "中国"

    def __init__(self):
        print("我是人类")

    @classmethod
    def greet(cls):
        # cls.country = "美国" 
        print("我是%s人" % cls.country)
        
    # 静态方法，不需要传递必要参数和类
    @staticmethod
    def static_method():
        print("hello")
            
p1 = Person()
p1.greet()
```
> 静态方法
- 无法访问类属性，相当于一个普通函数。
- 可以被类调用，也可以被实例调用。

###### `__new__`方法
```
# encoding = "utf-8"

# 创建对象__new__ → 初始化对象__init__
class Car(object):
    def __new__(cls, *args, **kwargs):
        print("new_method")
        # new方法后必须要返回当前这个类的对象
        return super(Car, cls).__new__(cls, *args, **kwargs)

    def __init__(self):
        print("car init method")

car = Car()
print(car)
```
###### 单例设计模式
```
# coding = "utf-8"

class Person(object):

    __instance = None

    def __new__(cls, *args, **kwargs):
        if not cls.__instance:
            cls.__instance = super(Person, cls).__new__(cls, *args, **kwargs)
        return cls.__instance

    def __init__(self, name):
        self.name = name

    def shar_user(cls):
        return Person()
```

---

### 3. 异常处理
- try
- except
    - 可以处理多个异常，可以写入多个except也可以在有一个except中用元祖的形式接受多个异常
- esle
- finally
> 抛出异常
- 利用`raise`抛出异常
```
a = "aaa"

class ArgumentError(Exception):
    '''自定义异常'''
    def __init__(self, *args, **kwargs):
        super(ArgumentError, self).__init__(*args, **kwargs)
        print("参数错误")


    def get_error(name, age):
        if not isinstance(name, str):
            raise ArgumentError("name is must be a string")
        if not isinstance(age, int):
            raise ArgumentError("age is must be a int")

get_error(11,"kang")
```
###### 自定义异常
- 所有异常都在类`exception`中
- 自定义异常时需要引用`exception`为父类
- 自定义异常的代码尽量简洁

---

#### 4. 模块和包
###### 模块
- 在`python`中，一个模块就是一个`py`文件，在需要使用该模块时，通过在代码开头利用`import`函数导入该模块，或者s使用`from...import...`导入其中一个函数
###### 包
- 包本质上是一个文件夹，但是文件夹中必须要有一个名称叫做`__init__.py`的文件；才会被`python`识别为一个包。

- 从子模块中导入父模块中的函数/或者子模块之间相互调入。
    - 绝对路径(`from fathaer import son`)
    - 相对路径(`from .fathaer import son`)
    
###### `__all__`
- 可以指定被调用的模块中可以使用的函数是哪些，并放在一个列表内
- 调用时只能使用列表内的函数
    ```
    # codin = "utf-8"
    # study_12.py
    __all__ = ['a']
    
    a = "aaa"
    
    class ArgumentError(Exception):
        def __init__(self, *args, **kwargs):
            super(ArgumentError, self).__init__(*args, **kwargs)
            print("参数错误")
    
    
    def get_error(name, age):
        if not isinstance(name, str):
            raise ArgumentError("name is must be a string")
        if not isinstance(age, int):
            raise ArgumentError("age is must be a int")
    
    # get_error(11,"kang")
    ```
    
    ```
    from study_12 import *

    print(a) 
    get_error(12,13)
    ```
    
    - 如果`__all__`变量在`__init__.py`文件中有控制导入的行为，那么会控制从包中导入的模块数量
    
###### 循环引用
- 循环引用不是好的解决办法，有循环引用说明程序设计存在一定问题。
- 建议避免使用循环引用。

###### 特殊变量`__name__`

---

### 5. 知识补充
###### 列表生成式
```
value = [i for i in range(0,100)]
print(value)
```

###### 模仿三目运算符
```
a = 5
b = 2
# 如果满足a>b，返回1，否则返回0，但是这种模式python不支持
# c = a > b ? 1 : 0

# 替代三目运算符的一种用法
d = 1 if a > b else 0
print(d)
```

###### 装饰器`@property`
```
class Student(object):

    @property # 将函数变为一个可读属性
    def score(self):
        return self._score

    @score.setter # 将可读属性变为一个可以改变的属性
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError("score must be an integer!")
        if value < 0 or value > 100:
            raise ValueError("score must between 0~100!")
        self._score = value
s1 = Student()
s1.score = 1000 # set方法
s_sc = s1.score # get方法
```

---

### 6. 进阶知识补充
##### 虚拟环境
- 虚拟环境相当于一个独立的新的系统，和现存的系统互不影响
- 安装`virtualenv`
    - 是用来创建虚拟环境的工具包
```
pip install virtualenv
pip3 install virtualenv

# 创建虚拟环境
virtualenv[虚拟环境的名字]
# 会创建在当前工作环境所在的文件夹

# 进入虚拟环境
activate

# 退出虚拟环境
deactivate

```

---

#### `virtualenvwrapper`
- 让虚拟环境管理起来更加简单
- 创建虚拟环境
```
mkvirtualenv name
```
- 激活虚拟环境
```
workon name
```
- 退出虚拟环境
```
deactivate
```
- 删除虚拟环境
```
rmvirtualenv name
```
- 进入虚拟环境所在目录
```
cdvirtualenv
```
- 更改默认环境
```
在高级设置→环境变量→系统变量中添加一个WORKON_HOME，用来指定需要调整的位置
```
- 指定python版本
```
--python==python位置 虚拟环境名字
```

---

#### 迭代器
###### 可迭代的对象（Iterable）
- 可以用for循环遍历的对象就是可迭代的对象（`list`，`tuple`，`dict`，`str`, `set`）
- 这些对象都有一个`__iter__`方法，用来返回一个迭代对象。
- 可以使用`isinstance()`来判断一个对象是否是一个可迭代对象
```
# coding="utf-8"
from collections import Iterable

l1 = [1,2,3]
print(isinstance(l1,Iterable))
```
###### 迭代器（Iterator）
```
# coding="utf-8"
from collections import Iterable, Iterator

l1 = [1,2,3]
print(isinstance(l1,Iterable))
print(isinstance(l1,Iterator))
# 可迭代的对象不一定是迭代器

# 包含`__iter__`方法和`__next__`方法的就是一个迭代器 
class Myrange(object):
    def __iter__(self):
        pass

    def __next__(self):
        pass


m1 = Myrange()
print(isinstance(m1,Iterable))
print(isinstance(m1, Iterator))
# 迭代器也可以是可迭代的对象
```
- 使用`iter()`可以获取可迭代对象的迭代器
- 自定义迭代器
- 可迭代对象，利用迭代器方法`iter()`进行迭代；程序运行期间该对象可以被多次遍历，不受次数限制。
- 可以把迭代器和可迭代对象方法封装到一起；但是这种方法下，在程序运行期间，该对象只能够被遍历一次，无法遍历第二次。
```
# coding = "utf-8"

class Myiterator(object):

    def __init__(self, start, end):
        self.index = start
        self.end = end

    def __iter__(self):
        return self

    def __next__(self):
        if self.index < self.end:
            temp = self.index
            self.index += 1
            return temp
        else:
            raise StopIteration()

class Myiterable(object):
    '''
    可迭代对象，利用迭代器进行迭代；程序运行期间该对象可以被多次遍历，不受次数限制。
    '''
    def __init__(self, start, end):
        self.start = start
        self.end = end

    def __iter__(self):
        return Myiterator(self.start, self.end)

r1 = Myiterable(0, 12)
print(r1)
for r in r1:
    print(r)
    
    
class Myiterable1(object):
    '''
    把迭代器和可迭代对象方法封装到一起；但是这种方法下，该程序运行期间，只能够被遍历一次，无法遍历第二次。
    '''
    def __init__(self, start, end):
        self.start = start
        self.end = end

    def __iter__(self):
        return self

    def __next__(self):
        if self.start < self.end:
            temp = self.start
            self.start += 1
            return temp
        else:
            raise StopIteration()

r2 = Myiterable1(0,15)
for r in r2:
    print(r)
```

---

#### 生成器（generator）
- 生成器中有`yield`函数，每次使用`next()`函数，都只会反馈一个值；下次调用，才会继续显示下一个值。
- 生成器既是一个可迭代的对象，也是一个迭代器。
    - 所以生成器在程序运行期间只能够被遍历一次，无法多次循环。
```
# 创建一个简单的生成器
s1 = (i for i in range(1,100))
for j in s1:
    print(j)
print(type(s1))

# 自定义生成器
def my_gen():
    yield 1
    yield 2
    yield 3

r1 = my_gen()
print(next(r1))
```

##### send方法
```
def my_gen(start):
    while start < 10:
        temp = yield start
        print(temp)
        start += 1

r1 = my_gen(1)

print(next(r1))
# 返回1，函数会停在yield start这一行

print(next(r1)) # 返回None，2
# 第二次运行时，函数会从print(temp)开始，但此时还未调用yield，因此会返回None，
# 接下来函数会开始继续运行，start += 1 → while循环 → yield start(yield函数会直接将生成器停下来，并将这一轮的值打印出来)

print(r1.send("kang")) # 返回kang，3
# send()函数可以把None替换会自己想要的值，并让生成器开始下一轮，而且会将值打印出来。
```
> 注意：如果在生成器刚开始第一轮的时候就使用send()方法，必须给send()传递一个参数`None`；不传或者传其他参数都会报错。

> 生成器中的`return`语句会触发`StopIteration`错误
```
# coding = "utf-8"
# 斐波那契数列
def fb(x):
    index = 0
    a, b = 0, 1
    while index < x:
        yield b
        c = b
        b = a + b
        a = c
        index += 1
f1 = fb(10)
for f in f1:
    print(f)
```

---

#### 装饰器

##### 闭包
- 在一个函数中再定义一个函数，并引用最外层函数的变量，且最外层的函数的返回值是里层函数
- `nonlocal`关键字允许在闭包的里层函数中修改外层函数的变量。
```
# coding="utf-8"

def great(name):
    def say_hello():
        nonlocal name
        name += "xiaodong"
        print("hello! My name is %s" %name)
    return say_hello

great("kang")()
```
##### 装饰器
- 装饰器利用了函数可以作为对象被返回和闭包的特点；可以在函数执行之前和执行之后方便的添加一些代码。
> 装饰器的基本使用
```
#  coding = "utf-8"
user = {
    'is_login': True
}

def login_func(func):
    def wrapper():
        if user['is_login'] == True:
            func()
        else:
            print("跳转到登陆页面!")
            name = input("请输入账户名：")
            password = input("请输入密码：")
            print("登陆成功!")
    return wrapper

# 调用函数xg()的时候，会把xg调用到login_func中，先判断用户是否登录
@login_func
def xg():
    input("请输入新密码：")
    print("修改密码成功")
    
xg()

```

- 当被装饰的函数中有参数时
```
# coding = "utf-8"

user = {
    "is_login": True
}

def login_u(func):
    def wrapper(*args, **kwargs):
        if user["is_login"] == True:
            func(*args, **kwargs)
        else:
            print("跳转到登陆界面")
    return wrapper

@login_u
def ext_u(user_name):
    print("修改名称%s" %user_name)

@login_u
def ext_article(title, author):
    print("添加文章：%s，作者：%s" %(title, author))

ext_u("kang")
ext_article("life", "Dennis")
```
- 给装饰器传递参数
    - 使用三层嵌套
```
import functools
def login_required(site="front"):
    def outer_wrapper(func):
        @functools.wraps(func)
        def inner_wrapper(*args, **kwargs):
            if user["is_login"] == True:
                print("call is %s" % func.__name__)
                func(*args, **kwargs)
            else:
                if site == "front":
                    print("跳转的前端登陆页面")
                else:
                    print("跳转到登陆界面")
        return inner_wrapper
    return outer_wrapper

@login_required("front")
def ext_article_2(title, author):
    print("添加文章：%s，作者：%s" %(title, author))
ext_article_2("No!!!", "MaYue")
```
###### `functools.wraps`
- 将装饰器`functools.wraps`放在`wrapper`函数上方，能够保证`wrapper`执行的函数名字不会被更改。
    - 每个函数都有一个`__name__属性`，调用装饰器时，会将该函数指向装饰器内部的`wrapper`函数（`示例：add = wrapper`），如果不使用`functools.wraps`，使用装饰器的函数名字会被更改，`add.__name__ = wrapper`。
```
# coding="utf-8"
import functools

def greet(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print("func is start")
        func(*args, **kwargs)
        print("func is end")
    return wrapper

@greet
def add(x, y):
    print("%d+%d=%d" %(x, y, x+y))
print(add.__name__)
```

---

#### 面向对象[进阶]
##### 动态添加属性和方法
> 直接指定
> `setattr`函数
```
# coding="utf-8"

class Person(object):

    def __init__(self, name):
        self.name = name

    def name_s(self):
        print("my name is %s" % self.name)

p1 = Person("kang")
p1.name_s()
setattr(p1, "age", 12)
print(p1.age)
```
> `hasattr`
- 判断一个对象是否拥有某个属性
```
if not hasattr(p1, "age"):
    print(True)
else:
    print(False)
```
##### 动态添加实例方法
> 使用`types.MethonType`方法
```
# coding="utf-8"
import types

class Person(object):

    def __init__(self, name):
        self.name = name

    def name_s(self):
        print("my name is %s" % self.name)

p1 = Person("kang")
p1.name_s()
setattr(p1, "age", 12)
print(p1.age)

def run(self):
    print("%s is running" % self.name)

# 将run指定为实例p1的run方法
p1.run = types.MethodType(run, p1)
p1.run()
```
> 动态添加类方法
> 动态添加静态方法
```
class Person(object):
    na = "xiaodong"
    def __init__(self, name):
        self.name = name

    def name_s(self):
        print("my name is %s" % self.name)
        
@classmethod
def run(cls):
    print("%s is running" % cls.na)
    
@staticmethod
def eat():
    print("开始吃饭")

# 直接指定一个待添加的类方法为类方法
Person.run = run
# 直接指定一个待添加的静态方法为静态方法
Person.eat = eat

p3 = Person("ma")
p3.run()

p4 = Person("yue")
p4.eat()
```
> 动态删除属性
```
del p1.name
delattr(p1, name)
```
> `__slots__`魔术变量

     限制类能够绑定的属性
```
# Person类只能绑定name和age属性
class Person(object):
    __slots__ = ("name", "age")
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def name_s(self):
        print("my name is %s" % self.name)
```
##### 装饰器模拟Flask的url映射


---

#### 元类
###### 类也是对象
```
# coding="utf-8"

class Objectcreator(object):
    pass

print(Objectcreator)

def echo(o):
    print(o)

# 可以将类作为一个对象传入到函数中
echo(Objectcreator)
```
###### 动态的添加类
> 使用`tpye`函数动态的创建类
> `type(类名，用元祖包含的继承的父类名，用字典包含的参数)`
```
# 使用tpye动态的创建一个类
Person = type("Person", (object,), {"name":"kang", "age":19})
p = Person()
print(p.name)
print(p.age)
```
- 所有创建类对象的对象就称为元类，可以把元类称为`生产类的工厂`，而`type`就是Python的内建元类。同时，用户也可以根据需求创建自己需要的元类。
- 自定义元类
    - 日常工作中99.9%场景可能用不到元类，只有在明确自己要做什么的情况（技术过硬，有使用需求），才会使用元类。
```
# coding="utf-8"
### 将类中定义的类参数名改为大写

# 用函数的形式创建元类
def upper_class_attr(class_name, class_type, class_attrs):
    new_attrs = ((k, v) for k, v in class_attrs.items() if not k.startswith("__"))
    upper_attrs = dict((k.upper(),v) for k,v in new_attrs)
    return type(class_name, class_type, upper_attrs)

class Person(object, metaclass=upper_class_attr):
    country = "china"

print(Person.COUNTRY)

# 用类的形式创建元类
class Upperattrsmetaclass(type):
    def __new__(cls, class_name, class_type, class_attrs):
        new_attrs = ((k, v) for k, v in class_attrs.items() if not k.startswith("__"))
        upper_attrs = dict((k.upper(), v) for k, v in new_attrs)
        return super(Upperattrsmetaclass, cls).__new__(cls, class_name, class_type, upper_attrs)

class Person(object, metaclass=Upperattrsmetaclass):
    country = "china"

print(Person.COUNTRY)
```

---

#### 内存管理

##### 对象池
###### 小整数对象池
> [-5,256]之间的整数是比较常用的，因此在Python运行之前就会创建一个-5到256的小整数对象池供用户使用。
###### 对只包含英文字符的字符串，都指向一个内存，如果字符串中包含非英文字符的对象，则会使用不同的内存储存。
##### 垃圾回收机制
###### 引用计数
> 如果一个对象没有任何一个变量指向它，那么该对象所占用的内存空间会被清除
```
# coding="utf-8"

class Person():

    def __init__(self, name):
        self.name = name

    def __del__(self):
        print("%s执行了del函数" % self.name)

while True:
    p1 = Person("p1")
    p2 = Person("p2")
    del p1
    del p2
    a = input("test: ")
    if a == "exit":
        break
```
###### 循环引用
> 如果变量a指向变量b，变量b又指向变量a，那么引用计数的方式无法解决内存释放的问题。
###### 标记清除和分代回收
- 零代链表
    - Python会将创建的变量储存到`零代链表`当中，
    - 引用计数无法解决的循环引用问题：当你执行`del a `删除被循环引用的变量时，`python`会遍历`零代链表，将循环引用的变量的引用计数值减1，如果引用计数值为0了，就说明这个对象可以被释放了。
    - （python每次会遍历700次，如果700次之后还有循环引用，都会被移动到一代链表中）
    - 此时，剩余的变量会被储存到一个新的链表：`一代链表`。
- 一代链表
    - 如果一代链表中的对象在遍历10次(每遍历零代链表700次，一代链表就会+1次)后，还存在未被释放的变量，则会被移动到`二代链表`中
- 二代链表
    - 二代链表中依然会被遍历10次

###### gc模块
> 复习

- 导致引用计数+1的情况
    1. 对象被创建，并被另外一个对象引用 `a=23`
    2. 对象被另外一个变量引用 `b=a`
    3. 对象被作为参数传递给函数 `func(a)`
    4. 对象被添加到容器中 `列表、字典、集合、字符串中等`
- 导致引用计数-1的情况
    1. 引用对象的变量被删除 `del a`
    2. 引用该对象的变量指向了其他变量 `b='123'`
    3. 函数作用域执行完毕后，作为函数参数的临时变量在函数执行结束后就会消失
    4. 对象所在的容器被销毁，或者删除该对象。
    
```
# 查看一个对象的引用计数
# coding = "utf-8"

import sys

a = "123"

print(sys.getrefcount(a)) # 该函数也会计算一次引用计数，也就是说该函数获取的引用计数总是会比实际引用次数多1
```

> gc模块常用函数
- gc.set_debug(flags)
    - 设置gc的debug日志，一般设置为`gc.DEBUG_LEAK`，可以看到内存泄漏现象。
- gc.get_threshold()
    - 获取gc模块执行垃圾回收的阈值。返回一个元祖（700,10,10）分别代表零代链表，一代链表和二代链表的阈值
- gc.set_threshold()
    - 自定义设置阈值，需要传入三个参数来对应零、一和二代链表的调整值
- gc.get_count()
    - 查询目前程序中对象的阈值使用情况
```
# coding = "utf-8"

import gc

class Person(object):

    def __init__(self, name):
        self.name = name
        self.pointer = None

print(gc.get_threshold()) # 获取gc模块执行垃圾回收的阈值。返回一个元祖（700,10,10）分别代表零代链表，一代链表和二代链表的阈值
gc.set_threshold(1000,20,20) # 自定义设置阈值，需要传入三个参数来对应零、一和二代链表的调整值
print(gc.get_count()) # 查询目前程序中对象的阈值使用情况
print(gc.get_threshold())
gc.set_debug(gc.DEBUG_LEAK)
while True:

    print(gc.get_count())
    p1 = Person("p1")
    p2 = Person("p2")

    p1.pointer = p2
    p2.pointer = p1

    del p1
    del p2

    a = input("test: ")

    gc.collect() 
    # 执行垃圾回收。会将有循环引用的对象进行回收。
    # 参数=0，回收零代链表的阈值；
    # 参数=1，则会回收零代和一代链表中的阈值；
    # 参数=2，回收二代链表中的阈值；
    # 不传参数，则默认参数为2
```
> 说明：如果循环引用的对象有`__del__`方法，则`python`将永远无法回收该对象。

- 假设通过gc.get_threshold()返回的是(700,10,10)，那么意味着只要零代垃圾值到了700，就会执行gc.collect(0)，回收零代的垃圾值；只要1代垃圾值到了10，就会执行gc.collect(1)，回收零代和1代的垃圾值。只要2代垃圾值到了10，就会执行gc.collect(2)，回收零代和1代以及2代的垃圾值。

---

#### 常用魔术方法
##### 常用魔术方法
- `__init__`
- `__del__`
  
    > 对象即将被释放时，会使用这个方法
- `__new__`
  
    > 用来创建对象
    
- `__class__`
  
    > 用来查看对象所属的类。如果对象是类，那么将查看到对象的元类

- `__iter__`
  
    > 返回一个迭代器
    
- `__next__`
  
    > 迭代器在遍历对象时调用
    
- `__str__`
  
    > 在打印对象时可以返回用户想要看到的名字
```
# encoding="utf-8"

class Person(object):
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return "Person <%s>" % self.name
        
    def __repr__(self):
        return "Person --%s" % self.name

p1 = Person("kang")
p2 = Person("xiaodong")

print(p1)
print(p2)

res = str(p1)
print(res) # 如果对该对象使用函数`str()`，得到的对象在打印时也会显示你设置的名称。

print([p1,p2])

```
- `__repr__`

    > 如果对象在列表等容器中，打印列表时，会调用`__repr__方法`，显示对象在`__repr__`方法中定义的名字
    
- `__dict__`
  
    > 用来获取用户自定义的属性和定义的值，返回的是一个字典
    
- `dir()`函数会返回一个对象包含的所有属性名字（python自带的和用户自定义的），不会获取定义的值

##### 魔术方法之比较运算符

- `__cmp__`
  
    > 目前在python3中无法奏效

- `__eq__(self,other)`

    > 比较两个对象是否相等(`self == other`)
```
class Person(object):
def __init__(self,name,age,height):
    self.name = name
    self.age = age
    self.height = height

# __cmp__方法无法在python3中使用
def __cmp__(self,other):
    if self.age > other.age:
        if self.height == other.height:
            return 0
        else:
            return 1 if self.height > other.height else -1
    else:
        return 1 if self.age > other.age else -1

def __eq__(self,other):
    if self.age == other.age and self.height == other.height:
        return True
    else:
        return False


p1 = Person("xiaokang", 26, 155)
p2 = Person("xiaoma", 23, 115)
if p1 == p2:
    print(True)
else:
    print(False)
```

- `__ne__(self,other)`

    > 比较两个对象是否不相等(`self != other`)
    
- `__lt__(self,other)`

    > 比较`self`是否小于(`<`)`other`

- `__gt__(self,other)`

    > 比较`self`是否大于(`>`)`other`
    
- `<=`和`>=`的解决方法
  
    -`<=`
        
        - `__le__`
     -`>=`
        
    
        - `__ge__`

##### 魔术方法之一元运算符
```
class Coordinate(object):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __pos__(self):
        # positive 返回正数
        return self

    def __neg__(self):
        # negative 返回负数
        # 创建一个副本返回
        new_coordinate = Coordinate(-self.x, -self.y)
        return new_coordinate

        # 更改对象本身返回
        # self.x = -self.x
        # self.y = -self.y
        # return self

    def __abs__(self):
        # 返回绝对值
        new_coordinate = Coordinate(abs(self.x), abs(self.y))
        return new_coordinate

    def __invert__(self):
        # 在对象前面添加 ~
        new_coordinate = Coordinate(~self.x, ~self.y)
        return new_coordinate


    def __str__(self):
        return "(%s, %s)" % (self.x, self.y)

c1 = Coordinate(-1,-2)
print(c1)

c2 = -c1
print(c2)

c3 = abs(c1)
print(c3)

c4 = ~c1
print(c4)
```

##### 魔术方法之二元运算
```python

class Coordinate(object):
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self,other):
        # 对象相加
        x = self.x + other.x
        y = self.y + other.y
        return Coordinate(x, y)

    def __sub__(self,other):
        # 对象相减
        x = self.x - other.x
        y = self.y - other.y
        return Coordinate(x, y)

    def __mul__(self,other):
        # 对象相乘
        x = self.x * other.x
        y = self.y * other.y
        return Coordinate(x, y)

    def __truediv__(self,other):
        # 对象相除，保留小数点后所有内容
        x = self.x / other.x
        y = self.y / other.y
        return Coordinate(x, y)

    def __floordiv__(self,other):
        # 对象相除，保留整数部分
        x = self.x // other.x
        y = self.y // other.y
        return Coordinate(x, y)

    def __mod__(self,other):
        # 对象取模，保留相除后的余数部分
        x = self.x % other.x
        y = self.y % other.y
        return Coordinate(x, y)

    def __str__(self):
        return "(%s, %s)" % (self.x, self.y)

c1 = Coordinate(1,2)
c2 = Coordinate(2,3)

c3 = c1 + c2
print(c3)

c4 = c1 - c2
print(c4)

c5 = c1 * c2
print(c5)

c6 = c1 / c2
print(c6)

c7 = c1 // c2
print(c7)

c8 = c1 % c2
print(c8)

```
##### 魔术方法之增量赋值魔术方法
```
class Coordinate(object):
    def __init__(self,x,y):
        self.x = x
        self.y = y

    def __iadd__(self,other):
        # 实现对象 +=
        self.x += other
        self.y += other
        return self

    def __isub__(self, other):
        # 实现对象 -=
        new_coordinate = Coordinate(self.x-other, self.y-other)
        return new_coordinate

    def __imul__(self, other):
        # 实现对象 *=
        new_coordinate = Coordinate(self.x * other, self.y * other)
        return new_coordinate

    def __itruediv__(self, other):
        # 实现对象 /=
        new_coordinate = Coordinate(self.x / other, self.y / other)
        return new_coordinate

    def __str__(self):
        return "(%s, %s)" % (self.x, self.y)

c1 = Coordinate(1,2)
c1 += 1
print(c1)
c1 -= 1
print(c1)
c1 *= 1
print(c1)
c1 /= 1
print(c1)
```

##### 魔术方法之控制属性的访问和设置
- `__getattr__`
  
        > 当访问的对象的属性不存在的时候，才会调用该方法
    
- `__setattr__`

        > 给一个对象设置属性的时候，会使用该方法，需要避免产生递归，一般在该方法中使用`self.__dict__[key]=value`来创建新的属性

- `__getattribute__`
        
    
        > 只要你访问了一个对象的属性，不管属性是否被创建，都会调用该方法。需要使用`super(类名, self).__getattribute__(item)`来避免产生死循环的递归
```
# encoding="utf-8"

import logging

class Person(object):
    def __init__(self,name):
        self.name = name
        self.is_adult = False

    # def __getattr__(self,item):
    #     # 在属性不存在的时候会被调用
    #     # item 属性的名字
    #     if item == "pname":
    #         logging.warning("pname属性已被废弃，在下一个版本中不再使用。")
    #         return self.name
    #     else:
    #         raise AttributeError(item+" abnormal properties :)")


    def __setattr__(self,key,value):
        # key: 属性的名字
        # value: 属性的值
        # print("key: %s" % key)
        # print("value: %s" % value)
        self.__dict__[key] = value
        if key == "age":
            if value >= 18:
                self.__dict__["is_adult"] = True
                print("已成年")
            else:
                self.__dict__["is_adult"] = False
                print("未成年")

    def __getattribute__(self,item):
        print(item)
        print("=" * 30)
        return super(Person, self).__getattribute__(item)


p1 = Person("pname")
p1.age = 20
print(p1.name)
print(p1.age)


```

##### 魔术方法之创建属于自己的序列
- python内置的序列有列表、字典等

```
# encoding="utf-8"

class Mylist(object):
    def __init__(self, values=None):
        if values:
            self.values = values
        else:
            self.values = []

    def __len__(self):
        # 返回序列的长度
        return len(self.values)

    def __getitem__(self, item):
        # 返回序列中某个下标指向的值，或者是切片指向的某些值
        return self.values[item]

    def __setitem__(self, key, value):
        # 返回序列中某个下标指向的值，或者是切片指向的某些值
        self.values[key] = value

    def __delitem__(self, key):
        # 删除某个下标对应的值
        del self.values[key]

    def __iter__(self):
        # 将序列变成一个可迭代对象
        return iter(self.values)

    def __reversed__(self):
        # 将序列倒转顺序排列
        return reversed(self.values)

m1 = Mylist(values=[1,2,3,4])
print(len(m1))
print(m1[0:2])
m1[0] = ["a", "b"]
print(m1[0])
del m1[1]
print("="*20)
for m in m1:
    print(m)

m2 = reversed(m1)
print("="*20)
for m in m2:
    print(m)
```

##### 魔术方法之可调用的对象
- 在Python中，一个特殊的魔术方法可以让类的实例的行为表现的像函数一样，你可以调用他们，将一个函数当做一个参数传到另外一个函数中等等。这是一个非常强大的特性让Python编程更加舒适甜美。`__call__(self, [args...])`


- ++允许一个类的实例像函数一样被调用++。实质上说，这意味着x()与 x.__call__()是相同的。注意__call__参数可变。这意味着你可以定义__call__为其他你想要的函数，无论有多少个参数。
```
# encoding="utf-8"

def index():
    return "index page"

class Indexview(object):
    def __call__(self):
        return "index view page"

i1 = Indexview()
print(i1())
print(index())
```
##### 魔术方法：会话语句
- 打开文件时一般使用with语句，该语句会在执行完成后自动关闭文件。
    - 是因为在该语句的底层定制了2个专属的魔术方法
    - `__enter__` 用来打开文件
    - `__exit__`  用来关闭文件，且在文件打开失败时处理异常。

```
# encoding="utf-8"

class Fileopen(object):
    def __init__(self, filename, mode):
        self.filename = filename
        self.mode = mode

    def __enter__(self):
        self.fp = open(self.filename, self.mode)
        return self.fp

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.fp.close()

with Fileopen("123.txt", "w") as fp:
    fp.write("hello world!")
```

##### 魔术方法：pickle
```
# encoding="utf-8"

import pickle

data = {'foo': [1, 2, 3],
        'bar': ('Hello', 'world!'),
        'baz': True}

with open("magic_22.pkl", "wb") as fp:
    pickle.dump(data, fp)

with open("magic_22.pkl", "rb") as fp:
    print(pickle.load(fp))

```

###### 自定义序列化对象的方法
- 需要实现两个魔术方法
    - `__getstate__`：对象被序列化时，使用该方法将序列化的对象保存到硬盘中
    - `__setstate__`：在读取序列化对象时会调用该魔术方法；将加载到的对象作为参数传递给该方法；之后就可以拿到想要的序列化数据。
```
# encoding="utf-8"

import pickle

data = {'foo': [1, 2, 3],
        'bar': ('Hello', 'world!'),
        'baz': True}

with open("magic_22.pkl", "wb") as fp:
    pickle.dump(data, fp)

with open("magic_22.pkl", "rb") as fp:
    print(pickle.load(fp))

class Person(object):
    def __init__(self,name,age):
        self.name = name
        self.age = age

    def __getstate__(self):
        # 只能返回pickle识别的数据类型（python的一些序列）
        # 识别需要存储的内容，并且按照自己想要的格式记录
        return {"name": self.name, "age": self.age}

    def __setstate__(self,state):
        # 将识别的内容储存
        self.name = state["name"]
        self.age = state["age"]

    def __str__(self):
        return "<name: %s, age: %s>" %(self.name, self.age)

p1 = Person("kamg", 18)

with open("person_m.pkl", "wb") as fp:
    pickle.dump(p1,fp)

with open("person_m.pkl", "rb") as fp:
    print(pickle.load(fp))
```

---
#### 多任务编程
> 操作系统可以同时运行多个任务。比如电脑可以一边玩游戏，一边听歌。

##### 多任务编程：多进程
> multiprocessing模块

    1. 专门用来创建多进程的模块，支持在多个平台上运行。
    2. 进程创建的代码需要放在 `if __name__ == "__main__"` 下面。

##### 获取进程号(进程id)
```
# encoding="utf-8"

from multiprocessing import Process
import time
import os

def kang():
    print("======子进程开始======")
    print("子进程id: %s" % os.getpid())
    print("父进程id: %s" % os.getppid())
    print("======子进程结束======")

if __name__ == "__main__":
    p =Process(target=kang)
    # 父进程会等待所有子进程执行完成后退出。
    p.start()
    print("我是主进程的代码")
    
    p.join()
    # 在所有子进程执行完成后执行
    print("所有子进程执行完毕")
```
##### `join()`方法
> 相当于主进程阻塞在每次子进程执行后，每次都会询问是否还有剩余进程，当所有子进程执行完成后，会执行join()方法之后的代码。

>可以添加一个参数用来指定join()方法之后的代码在等待多长时间后就执行。

##### 使用类的方式创建子进程
- 用类的方式创建子进程，不管执行的内容是什么，都必须重写run()方法
- 使用类创建子进程时，不需要传递`target`参数
```
# encoding="utf-8"

from multiprocessing import Process
import os
import time

# 用类的方式创建子进程，不管执行的内容是什么，都必须重写run()方法
class Myprocess(Process):
    def run(self):
        print("子进程id: %s" % os.getpid())
        for i in range(4):
            print("子进程：%s" % i)


if __name__ == "__main__":
    p1 = Myprocess()
    p1.start()
    print("我是父进程")
    print("父进程id: %s" % os.getppid())
    p1.join()
    print("所有子进程执行完毕")
```
##### 进程池详解
- multiprocessing模块中的`Pool`类可以实现一个容器，用来限制同时执行的子进程的最大数量
- `Pool`不会等待所有子进程执行完毕后退出，会在主进程程序执行后退出；因此需要在代码中添加`Pool.join()`来保证所有子进程都能执行。
```
# encoding ="utf-8"

import multiprocessing as mul
import os
import time

def my(num):
    for i in range(5):
        print("num: %s, id: %s" % (num, os.getpid()))
        time.sleep(2)

if __name__ == "__main__":
    m1 = mul.Pool(3)
    for x in range(10):               # 子进程执行10次，每次最多只能同时执行3给子进程
        m1.apply_async(my,args=(x, )) # 子进程
        # apply_async 异步执行，同时执行Pool限定的最大数量的子进程（并联）
        # apply 一次只能执行一个子进程，一个执行完成后执行下一个。（串联）
    print("="*30)
    m1.close()  # 关闭进程池，不在添加新的子进程了。

    m1.join()   # 在所有子进程执行完毕后执行，保证所有子进程都被执行。
    print("所有进程执行完毕!")

```
##### 父子进程数据共享问题
> 创建子进程时会复制主进程中的所有参数和资源，所以在子进程中对数据的修改不会影响到主进程
```
# encoding="utf-8"

from multiprocessing import Process
import os
import time

AGE = 1
def kang(names):
    global AGE

    AGE += 1
    names.append("xiaodong")
    
    print("=====子进程=====")
    print("子进程id: %s" % os.getpid())
    print("AGE:%d, AGE_id: %s" % (AGE, id(AGE)))
    print("names:%s, names_id: %s" % (names, id(names)))
    print("=====子进程=====\n")
    
    time.sleep(2)

if __name__ == "__main__":
    names = ["kang"]
    k1 = Process(target=kang,args=(names,))
    k1.start()

    print("=====父进程=====")
    print("父进程id: %s" % os.getppid())
    print("AGE:%d, AGE_id: %s" % (AGE, id(AGE)))
    print("names:%s, names_id: %s" % (names, id(names)))
    print("=====父进程=====\n")

    k1.join()
    print("执行完毕！")

```
##### Queue消息队列
- 进程之间的数据是不共享的。因此想要在2个不同的进程之间使用相同的数据，就需要使进程之间相互通信。一般有两种方式，一种是管道(Pipe)，一种是队列(Queue)。
```
# encoding="utf-8"

from multiprocessing import Queue

# 可以指定消息队列中可以容纳的消息maxsize，如果没有参数，则为-1：表示可以添加无数消息，知道你的电脑内存满了为止。
q = Queue(5)

q.put("m1")
q.put("m2")
q.put("m3")

# qsize() 获取消息队列中的合计消息数量
print("qsize: %s" % q.qsize())

# full() 判断消息队列是否满了，返回True/False
print(q.full())
# empty() 判断消息队列是否为空，返回True/False
print(q.empty())

# put() 如果消息队列还没有满，将一条消息添加到消息队列中，直到消息队列满为止
# block参数：默认为True，以阻塞的方式添加消息，如果队列满了，依然会阻塞在此。
# timeout参数，设定等待时间，超时后会在超时后抛出异常。
# 如果block参数设置为False，那么会在消息队列满之后立刻抛出异常。
q.put("xiaodong!", block=True, timeout=2)
print("finished!")

# 非阻塞式的推入一条消息进入队列，在队列满了之后，直接抛出异常
# 相当于q.put(block=False)
q.put_nowait("zhang")

# 运行一次获取一个消息，以先进先出的方式获取，但是如果超过了消息队列中的消息数量，会返回异常
# 同时会将获取的消息删除掉
# get(block,timeout) bolck默认为True，即以阻塞的方式获取值，如果队列中没有值，会一直阻塞
print(q.get())
print(q.get())
print(q.get())

```

##### Process进程间通信
```
# encoding="utf-8"

from multiprocessing import Process, Queue
import time
import os

def write(q):
    for m in ["m1", "m2", "m3"]:
        q.put(m)
        print("子进程%s已经存放了消息: %s" % (os.getpid(), m))

def read(q):
    while True:
        try:
            msg = q.get(block=False)
            print("子进程%s已经读取了消息: %s" % (os.getpid(), msg))
        except:
            print("消息已经读取完毕!")
            break

if __name__ == "__main__":
    q = Queue()

    pw = Process(target=write, args=(q,))
    pr = Process(target=read, args=(q,))

    pw.start()
    pr.start()
    pw.join()
```

##### Pool进程池中进程间通信
- Pool进程池中的进程间通信，需要使用Manger().Queue()，不能直接使用multiprocessing模块中的Queue类，内置的方法一模一样。
```
#encoding: utf-8

from multiprocessing import Process,Queue,Pool,Manager
import os
import time

def write(q):
    for m in ["m1", "m2", "m3"]:
        q.put(m)
        print("子进程%s存放了消息: %s" % (os.getpid(), m))
        time.sleep(2)

def read(q):
    while True:
        try:
            msg = q.get(block=False)
            print("子进程%s读取了消息: %s" % (os.getpid(), msg))
            time.sleep(2)
        except:
            print("所有消息都读取完毕~")
            break

if __name__ == "__main__":
    q = Manager().Queue(3)
    pool = Pool(2)
    pool.apply(write, args=(q,))
    pool.apply(read, args=(q,))

    pool.close()

    pool.join()
```

---

##### 多任务编程：多线程
- 多线程介绍
- 使用`threading`模块创建多线程
    - `threading.enumerate()` 打印当前程序中存在的线程总数量
    - `threading.current_thread()` 查看当前线程的名字
```
# encoding = "utf-8"

import threading
import time

def reading():
    # 线程1
    for r in range(4):
        print("正在读书%s, name: %s" % (r, threading.current_thread()))
        time.sleep(1)

def drawing():
    # 线程2
    for d in range(4):
        print("正在画画%s, name: %s" % (d, threading.current_thread()))
        time.sleep(1)

if __name__ == "__main__":

    # 创建线程
    t1 = threading.Thread(target=reading)
    t2 = threading.Thread(target=drawing)

    # 启动线程
    t1.start()
    t2.start()

    print(threading.enumerate()) # 打印当前程序中存在的线程总数量

```

###### 继承自`threading.Thread`类
- 使用继承的方法
- 需要重写一下run()方法
```
# encoding='utf-8'

# encoding = "utf-8"

import threading
import time

class Read_thread(threading.Thread):
    def run(self):
        # 线程1
        for r in range(4):
            print("正在读书%s, name: %s" % (r, threading.current_thread()))
            time.sleep(1)


class Draw_thread(threading.Thread):
    def run(self):
        # 线程2
        for d in range(4):
            print("正在画画%s, name: %s" % (d, threading.current_thread()))
            time.sleep(1)

if __name__ == "__main__":

    # 创建线程
    t1 = Read_thread()
    t2 = Draw_thread()

    # 启动线程
    t1.start()
    t2.start()

    print(threading.enumerate()) # 打印当前程序中存在的线程总数量

```

##### 多线程共享全局变量的问题
> 由于所有的线程都是在有一个进程内，所以所有的线程都会共享变量；由于运行顺序是无序的，可能会导致数据错误。
```
# encoding="utf-8"

import threading

VALUE = 0
gLock = threading.Lock() #加锁后，每个线程在调用变量时，另一个线程需要排队等待一个线程调用完成后再调用

def add_value():
    global VALUE
    gLock.acquire() # 上锁
    for i in range(1000000):
        VALUE += 1
    gLock.release() # 解锁
    print("VALUE: %s" % VALUE)

if __name__ == "__main__":
    for t in range(2):
        t = threading.Thread(target=add_value)
        t.start()
```
###### `Lock`版本生产者和消费者模式
```
# encoding="utf-8"

import threading
import time
import random

Money = 1000
Glock = threading.Lock()
GTimes = 0

class Producer(threading.Thread):
    '''生产者生产金钱'''
    def run(self):
        global Money, GTimes
        while True:
            p_money = random.randint(4500,7000)
            Glock.acquire() # 上锁
            if GTimes >= 10:
                Glock.release() # 达到最大生产次数后，需要先解锁，然后退出循环
                print("-----已达到最大生产次数 %d次，目前剩余%s元钱！！！-----" % (GTimes, Money))
                break
            Money += p_money
            print("%s生产了%s元钱，剩余%s元钱" % (threading.current_thread(), p_money, Money))
            GTimes += 1
            Glock.release() # 解锁
            time.sleep(1)

class Consumer(threading.Thread):
    '''消费者消费金钱'''
    def run(self):
        global Money
        while True:
            c_money = random.randint(3000, 10000)
            Glock.acquire() # 上锁
            if c_money <= Money:
                Money -= c_money
                print("%s消费了%s元钱，剩余%s元钱" % (threading.current_thread(), c_money, Money))
            else:
                if GTimes >= 10:
                    Glock.release() # 达到最大生产次数后，由于无法消费，需要先解锁，然后退出循环
                    print("-----已达到最大生产次数 %d次，无法再次消费！！！-----" % (GTimes))
                    break
                print("%s准备消费%s元钱，剩余%s元钱，无法消费！！！！！！" % (threading.current_thread(), c_money, Money))
            Glock.release() #解锁
            time.sleep(1)

if __name__ == "__main__":
    for p in range(2):
        t = Producer(name="生产者%d号" % p)
        t.start()

    for c in range(2):
        t = Consumer(name="消费者%d号" % c)
        t.start()
```
##### Condition版本生产者与消费者模式
- wait：让当前线程处于等待状态，并且会自动释放锁。
- notify：通知某个再等待的线程，默认是第一个线程
- notify_all：通知所有正在等待的线程；但是不会释放锁，需要手动释放。
```
# encoding="utf-8"

import threading
import time
import random

Gmoney = 1000
Gcondition = threading.Condition()
GTimes = 0

class Producer(threading.Thread):
    '''生产者生产金钱'''
    def run(self):
        global Gmoney, GTimes
        while True:
            p_money = random.randint(4500,7000)
            Gcondition.acquire() # 上锁
            if GTimes >= 10:
                Gcondition.release() # 达到最大生产次数后，需要先解锁，然后退出循环
                print("-----已达到最大生产次数 %d次，目前剩余%s元钱！！！-----" % (GTimes, Gmoney))
                break
            Gmoney += p_money
            print("%s生产了%s元钱，剩余%s元钱" % (threading.current_thread(), p_money, Gmoney))
            GTimes += 1
            Gcondition.notify_all() # 通知所有等待的线程，生产者已生产了多少元钱。
            Gcondition.release() # 解锁
            time.sleep(1)

class Consumer(threading.Thread):
    '''消费者消费金钱'''
    def run(self):
        global Gmoney
        while True:
            c_money = random.randint(3000, 10000)
            Gcondition.acquire() # 上锁
            while Gmoney < c_money:
                if GTimes >= 10:
                    Gcondition.release()
                    return
                Gcondition.wait() # 阻塞在此处
            Gmoney -= c_money
            print("%s消费了%s元钱，剩余%s元钱" % (threading.current_thread(), c_money, Gmoney))
            Gcondition.release() #解锁
            time.sleep(1)

if __name__ == "__main__":
    for p in range(2):
        t = Producer(name="生产者%d号" % p)
        t.start()

    for c in range(2):
        t = Consumer(name="消费者%d号" % c)
        t.start()
```

---
#### 正则表达式

##### 匹配单个字符

match函数匹配到在匹配到一个符合条件的字符之后就会停止匹配，只要遇到第一个不符合条件的字符，就会停止。

- 点（.）匹配任意的字符。
```
test = "hello"
ret = re.match('.', test)
print(ret.group())  # group() 返回match函数匹配到的结果
# 点不能匹配换行符(\n)
```

- \d：匹配任意数字。
- \D：匹配任意非数字。
- \s：匹配空白字符（\n，\t，\r，空格）
- \w：匹配大小写字母、数字和下划线
- \W：匹配的内容是`\w`匹配不到的内容
- 组合方式
     -用[]的方式包含自己想要匹配的内容
```
import re

text = "027-8888888"
ret = re.match("[\d-]+", text)
print(ret.group())
```
    + 加上加号，能够匹配至少一个或者多个满足条件的字符
- `[0-9]`可以代替`\d`
- `[^0-9]`可以代替`\D`
- `[a-zA-Z0-9_]`可以代替`\w`
- `[^a-zA-Z0-9_]`可以代替`\W`

##### 匹配多个字符
- *：匹配任意多个字符，0-无数个
- +：匹配至少一个字符
- ?：匹配0-1个字符
- {m,n}：匹配m-n个字符
```
text = "0271234"
ret = re.match("\d{1,3}", text)
print(ret.group())
```
##### 小案例
```
# 匹配手机号
tel = "13467283778"
rel = re.match("1[356789]\d{9}", tel)
print(rel.group())

# 匹配邮箱
email = "xiaodongkang@51talk.com"
eml = re.match("\w+@[a-z0-9]+\.[a-z]+", email)
print(eml.group())

# 验证URL
url = "https://www.baidu.com"
ur = re.match("(http|https|ftp)://[^\s]+", url)
print(ur.group())
```

```
# ^脱字号 以……开头
text = "hall0"
ret = re.match("^h", text)
print(ret.group())

# $ 以……为结尾
text = "hall0@163.com"
ret = re.match("\w+@163.com$", text)
print(ret.group())

# | 匹配多个字符串或者表达式
text = "hall0@163.com"
ret = re.match("ha|he|hk", text)
print(ret.group())

# 贪婪模式与非贪婪模式
text = "012345"
ret1 = re.match("\d+", text) # 贪婪模式：匹配所有符合条件的数值
ret2 = re.match("\d+?", text) # 非贪婪模式：只匹配一个符合条件的数值
print(ret1.group())
print(ret2.group())

# 匹配1-00的数字
num = "100"
ret = re.match("[1-9]\d?$|100$", num)
print(ret.group())
```

##### 转义字符和原生字符
- re.search() 在整个字符串中查找

> $在正则中有特殊含义，因此如果要匹配$，需要添加 \
```
text = "sad$sa"
ret = re.search("\$", text)
print(ret.group())

```

- 原生字符串
```
text2 = r"\n" #打印原生字符串中的内容
print(text2)

text = "\c"
ret1 = re.search("\\\\c", text)
ret2 = re.search(r"\\c", text)
print(ret1.group())
print(ret2.group())
```
##### 分组
```
text = "apple's price is $99, orange's price is $50"
ret = re.search(".*(\$[1-9]\d?).*(\$[1-9]\d?)", text)

print(ret.group())  # 获取匹配到的内容
print(ret.group(1)) # 获取第一个分组的内容：()中的内容
print(ret.group(2)) # 获取第二个分组的内容：()中的内容
print(ret.groups()) # 获取所有分组的内容
```

##### findall
- re.findall() 找出所有满足条件的内容，以列表的形式返回
```
text = "aasdafasdasd"
ret = re.findall("a", text)
print(ret)
```

##### sub
- re.sub() 使用正则表达式将需要替换的内容替换掉
```
html = '''
    <bb>吃饭了吗</bb>
'''

ret = re.sub("<.+?>", "", html)
print(ret)
```

##### split
- re.split() 使用正则表达式内容进行分割
```
text = "hello welcome to my world!"
ret1 = re.split("\W", text)
print(ret1)
```

##### compile
- re.compile() 对于经常使用到的正则表达式，可以使用该函数进行编译，后期可以直接拿过来使用
- 可以添加注释
```
text1 = "orange's price is $99.00"
r = re.compile(r'''
    \$?  # 匹配$字符
    \d+ # 匹配小数点前的数字
    \.?  # 匹配点
    \d+ # 匹配小数点后面的数字
''', re.VERBOSE)
ret2 = re.search(r, text1)
print(ret2.group())
```

---
#### 文件处理
##### json
- 将python对象转化为json对象
```
import json

books = [
    {
        "title": "maozedongxuanji",
        "price":99
    },
    {
        "title":"kangxiaodong",
        "price":11111111111111111111
    }
]

js1 = json.dumps(books) # 将python文件转化为json文件

with open("person.json", "a", encoding="utf-8") as fp:
    json.dump(js1, fp, ensure_ascii=False) 
    # 关掉ensure_ascii，可以保存中文
    # json.dump 可以直接保存json文件到本地
```
> 只有基本的数据类型可以转化为json文件

- 将json对象转化为python对象
> load  转化为python对象并保存到本地
> lodas 转化为python对象
```
with open("person.json", "r", encoding="utf-8") as fp:
    per = json.load(fp)
    print(per, type(per))
```
##### csv文件
```
import csv

with open("test.csv", "r", encoding="utf-8") as fp:
    reader1= csv.DictReader(fp)  # 以字典的方式读取csv文件
    reader2 = csv.reader(fp)  # 以普通的方式读取csv文件
```
> 可以通过key或者是下标的方式获取csv文件中的内容
```
import csv

headers = ["title", "price"]
books = [
    ("maozedongxuanji",99),
    ("kangxiaodong",11111111111111111111)
]

with open("test.csv", "w", encoding="utf-8", newline="") as fp:
    writer = csv.writer(fp)
    writer.writerow(headers)
    writer.writerows(books)
```

- 以字典的方式存储文件为csv
```
headers1 = ["title", "price"]
books1 = [
    {"title":"maozedongxuanji","price":99},
    {"title":"kangxiaodong","price":11111111111111111111}
]


with open("test1.csv", "w", encoding="utf-8", newline="") as fp:
    writer = csv.DictWriter(fp, headers1)
    writer.writeheader()
    writer.writerows(books1)
```
> newline=""参数可以让文件在存储为csv时，将换行符替换为你需要使用的字符