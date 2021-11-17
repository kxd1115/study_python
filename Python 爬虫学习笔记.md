## 什么是 http 和 https 协议

#### HTTP 协议：
全称是`HyperText Transfer Protocol`，中文意思是超文本传输协议，是一种发布和接收HTML页面的方法。服务器端口号是`80`端口。 

#### HTTPS 协议：
是`HTTP`协议的加密版本，在`HTTP`下加入了`SSL`层。服务器端口号是`443`端口。

---

## URL 详解
URL是`Uniform Resource Locator`的简写，统一资源定位符。

一个`URL`由以下几部分组成：

`scheme://host:port/path/?query-string=xxx#anchor`

* `scheme`：代表的是访问的协议，一般为`http`或者`https`以及`ftp`等。
* `host`：主机名，域名，比如`www.baidu.com`。
* `port`：端口号。当你访问一个网站的时候，浏览器默认使用80端口。
* `path`：查找路径。比如：`www.jianshu.com/trending/now`，后面的`trending/now`就是`path`。
* `query-string`：查询字符串，比如：`www.baidu.com/s?wd=python`，后面的`wd=python`就是查询字符串。
* `anchor`：锚点，后台一般不用管，前端用来做页面定位的。
- 在浏览器中请求一个url，浏览器会对这个url进行一个编码。除英文字母，数字和部分符号外，其他的全部使用百分号+十六进制码值进行编码。

---

## 请求头常见参数
在http协议中，向服务器发送一个请求，数据分为三部分:

第一个是把数据放在url中，第二个是把数据放在body中（在post请求中），第三个就是把数据放在head中。

这里介绍在网络爬虫中经常会用到的一些请求头参数：

**User-Agent：**
浏览器名称。这个在网络爬虫中经常会被使用到。请求一个网页的时候，服务器通过这个参数就可以知道这个请求是由哪种浏览器发送的。如果我们是通过爬虫发送请求，那么我们的User-Agent就是Python，这对于那些有反爬虫机制的网站来说，可以轻易的判断你这个请求是爬虫。因此我们要经常设置这个值为一些浏览器的值，来伪装我们的爬虫。

**Referer：**
表明当前这个请求是从哪个url过来的。这个一般也可以用来做反爬虫技术。如果不是从指定页面过来的，那么就不做相关的响应。

**Cookie：**
http协议是无状态的。也就是同一个人发送了两次请求，服务器没有能力知道这两个请求是否来自同一个人。因此这时候就用cookie来做标识。一般如果想要做登录后才能访问的网站，那么就需要发送cookie信息了。

**常见响应状态码：**
1. 200：请求正常，服务器正常的返回数据。
2. 301：永久重定向。比如在访问www.jingdong.com的时候会重定向到www.jd.com。
3. 302：临时重定向。比如在访问一个需要登录的页面的时候，而此时没有登录，那么就会重定向到登录页面。
4. 400：请求的url在服务器上找不到。换句话说就是请求url错误。
5. 403：服务器拒绝访问，权限不够。
6. 500：服务器内部错误。可能是服务器出现bug了。

---

## urllib 库
* 在 python 中，所有的网络请求方法，都被集中到 urllib.request 模块中去了。
1. urlopen 函数
    `request.urlopen()`

2. urlretrieve 函数
    `request.urlretrieve()`
    * 该函数可以将网页上的一个文件保存到本地。

3. urlencode 函数
    `parse.urlencode()`
    * 该函数可以将字典文件转码为 URL 编码的数据。

4. parse_qs 函数
    `parse.parse_qs()`
    
    将编码后的 URL 参数进行解码。

5. urlparse 函数 和 urlsplit 函数

    `parse.urlparse()`
    `parse.urlsplit()`
    * 可以利用`urlparse`函数对拿到的`url`中各个部分进行切割，当然`urlsplit`函数也可以达到相同效果，但是`urlsplit`函数会忽略`params`属性。

6. request.Request 类
    如果想要在请求时增加一些请求头，则需要使用`request.Request`类来实现。
    包含了`headers，data，method`等参数

7. `ProxyHandler`处理器（代理设置）
    * 代理的原理：在请求目的网站之前，现请求代理服务器，然后让代理服务器去请求目的网站，代理服务器拿到目的网站的数据后，再转发给我们的代码。
    * `http://httpbin.org/ip`这个网站可以方便的查看`http`请求的一些参数。
    * 在代码中使用代理：
        * 使用`urllib.request.ProxyHandler`传入一个代理，这个代理是一个字典，字典的`key`赖于代理服务器能够接收的类型，一般是`http`或者`https`，值是`ip:port`
        * 使用上一步创建的`Handler`以及`repuest.build_opener`创建一个`opener`对象
        * 使用上一步创建的`opener`调用`open`函数发起请求。

**示例代码如下：**
```
python
from urllib import request

url = 'http://httpbin.org/ip'
# 1. 使用 ProxyHandler 传入代理构建一个 Handler
handler = request.ProxyHandler({'http': '180.118.242.87:61234'})

# 2. 使用已经创建的 Handler 创建一个 opener
opener = request.build_opener(handler)

# 3. 使用 opener 去发送一个请求
resp = opener.open(url)
print(resp.read()
```

#### cookie的格式：
* Set-Cookie: NAME=VALUE；
* Expires/Max-age=DATE；
* Path=PATH；
* Domain=DOMAIN_NAME；
* SECURE

**参数意义：**
* NAME：cookie的名字。
* VALUE：cookie的值。
* Expires：cookie的过期时间。
* Path：cookie作用的路径。
* Domain：cookie作用的域名。
* SECURE：是否只在https协议下起作用。

---

## requests 库
通过`request.get`来直接发送`get`请求，`post`请求同理。

#### 基本操作
```
kw = {
    'wd': '中国'
}
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.117 Safari/537.36'
}

# 发送 get 请求，并添加查询参数
# params 接收一个字典类型的查询参数，并自动转换为 url 编码，不需要 urlencode()
response = requests.get('http://www.baidu.com/s', params=kw, headers=headers)

# 存储为本地html文件
with open('baidu_china.html', 'w', encoding='utf-8') as fp:
    fp.write(response.content.decode('utf-8'))

# 查看完整 url 地址
print(response.url)

# 查看响应内容，返回的是 Unicode 格式数据
print(response.text) 

# 查看响应内容，返回的是 bytes 格式数据
print(response.content) 

# 查看响应头部字符编码
print(response.encoding)

# 查看响应码
print(response.status_code)

```
**content 和 text 的区别：**
1. 首先`content`返回的是一个未经过解码的直接从网页上抓取的数据，因此，它是一个`bytes`类型的数据
2. `text`返回的是一个经过`resquests`猜测后并解码的数据，有的时候可能会出现乱码情况，因此就需要使用 `response.content.decode('utf-8')` 来进行解码

#### 使用代理：
在请求方法中，传递 proxies 参数即可。

#### 处理cookie：
如果想要在多次请求中共享 cookie ，那么应该使用 session 。

**示例代码如下**
```
import requests

url = 'http://www.renren.com/SysHome.do'

data = {
    'email': '18086507240',
    'password': 'qq941115'
}

headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/66.0.3359.117 Safari/537.36'
}

# 登录
session = requests.Session()
session.get(url=url, data=data, headers=headers)

# 访问大鹏个人主页
response = session.get('http://www.renren.com/880151247/profile')
with open('dapeng.html', 'w', encoding='utf-8') as fp:
    fp.write(response.text)
    
```

#### 处理不信任的 SSL 证书：

## lxml 库

#### 基本使用
**使用lxml解析HTML代码**

1. 解析`HTML`字符串，使用`lxml.etree.HTML`进行解析，示例代码如下：
    ```
    html = etree.HTML(text)
    print(etree.tostring(html, encoding='utf-8').decode())
    ```
2. 解析`HTML`文件，使用`lxml.etree.parse`进行解析，示例代码如下：
    ```
    parser = etree.HTMLParser(encoding='utf-8')  # 定义解释器
    html = etree.parse('tencent.html', parser=parser)
    print(etree.tostring(html, encoding='utf-8').decode())
    ```
    - 这个函数默认使用的是`XML`解析器，当碰到`HTML`代码不规范的情况，就需要利用`parse`参数自行定义`HTML`解析器。
    
**在lxml中使用XPath语法**

1. 使用`XPath`语法应该使用`Element.path`方法，来执行`XPath`代码，进行选择。示例代码如下：
    ```
    tr_5s = html.xpath("//tr[position() > 1]")
    ```
    - `xpath`函数返回的是一个列表
    
2. 获取标签的属性
    ```
    href = html.xpath("//a/@href")
    # 获取到a标签下的所有href对应的值
    ```
3. 获取文本，是通过`xpath`中的`test()`函数，示例代码如下：
    ```
    title = tr_5.xpath(".//a/text()")[0]
    # 代表查询所有a标签下的子孙元素，并获取文本
    
    title = tr_5.xpath("./td[1]//text()")[0]
    # 代表查询所有td标签下面的子元素，并获取文本
    ```
    - 如果想要在某个标签下面再执行`xpath`，需要在最前面加上一个点 . ，代表是在当前元素下获取，否则，将会在全局进行查找。
    
    
## BeautifulSoup4库

#### 基本使用
**find_all的使用**

1. 提取所有满足条件的标签
2. 可以使用`limit`参数限制提取的标签数量

**`find`和`find_all`的区别**

1. find：提取第一个满足条件的标签返回
2. find_all：提取所有满足条件的标签

**`find`和`find_all`的过滤条件**
1. 关键字参数：将属性的名字作为关键字参数的名字，属性的值作为关键字参数的值。
2. `attrs`参数：将属性条件放到一个字典中，传输给`attrs`参数。

**获取标签的属性**
1. 通过下标方式获取：通过标签的下标方式获取
    ```
    href = a['href']
    ```
2. 通过`attrs`属性获取
    ```
    href = a.attrs['href']
    ```

**`string`和`stripped_strings`、`string`属性以及`get_text`方法**
1. `string`：获取某个标签下的非标签字符串，如果存在多行字符串，将无法获取到。
2. `strings`：获取某个标签下的子孙标签中非标签字符串。
3. `stripped_strings`：获取某个标签下的子孙标签中非标签字符串，并剔除空格。
4. `get_text`：获取某个标签下子孙标签中非标签字符串，不是以列表形式返回，是以普通字符串形式返回。


**CSS选择器**

```css
<!DOCTYPE html>
<html>
<head>
	<title></title>
	<style type="text/css">
		.box > p{background-color: pink;}
		#line4{background-color: pink;}
		input[name='username'] {background-color: pink;}
		input[name='password'] {background-color: pink;}
		div.line1 {background-color: pink;}

	</style>
</head>
<body>
	<div class="box">
		<div>
			<p>第零行数据</p>
		</div>
		<p class='line1'>第一行数据</p>
		<p class='line2'>第二行数据</p>
		<p id='line3'>第三行数据</p>
		<div class="line1">
			div中的数据
		</div>
	</div>
	<p id="line4">第四行数据</p>
	<form>
		<input type="test" name="username">
		<input type="test" name="password">
	</form>
</body>
</html>
```

1. 根据标签的名字选择
    ```css
    p{background-color: pink;}
    ```
2. 通过类名查找
    - 需要在类的前面加一个.
    ```css
    .line2{background-color: pink;}
    ```
3. 根据id查找。
    - 在html中同一个id只能出现一次。
    ```css
    #line4{background-color: pink;}
    ```
4. 组合查找
    - 查找子孙元素
    ```css 
    .box p{background-color: pink;}
    ```
    - 只查找子元素
    ```css 
    .box > p{background-color: pink;}
    ```
5. 通过属性文字进行查找
    ```css
    input[name='username'] {background-color: pink;}
    ```
6. 在根据类名或者id进行查找的时候，如果还需要根据标签名进行过滤。
    ```
    div.line1 {background-color: pink;}
    ```

**`BeautifulSoup`中使用css选择器**
- 在`BeautifulSoup`中要使用css选择器，那么应该使用`soup.select`方法。
- 向该方法传递一个css选择器参数即可，基本类同于find_all方法。
1. 通过标签名查找：
```
print(soup.select('a'))
```
2. 通过类名查找：
通过类名，则应该在类的前面加一个.。比如要查找`class=sister`的标签。示例代码如下：
```
print(soup.select('.sister'))
```
3. 通过id查找：
通过id查找，应该在id的名字前面加一个＃号。示例代码如下：
`print(soup.select("#link1"))`
4. 组合查找：
组合查找即和写 class 文件时，标签名与类名、id名进行的组合原理是一样的，例如查找 p 标签中，id 等于 link1的内容，二者需要用空格分开：
```
print(soup.select("p #link1"))

# 直接子标签查找，则使用 > 分隔：
print(soup.select("head > title"))
```
5. 通过属性查找：
查找时还可以加入属性元素，属性需要用中括号括起来，注意属性和标签属于同一节点，所以中间不能加空格，否则会无法匹配到。示例代码如下：
```
print(soup.select('a[href="http://example.com/elsie"]'))
```

**常见的四种对象**
1. **Tag**：`BeautifulSoup`中的所有标签都是`Tag`类型，`BeautifulSoup`在本质上也属于`Tag`类型，所以我们在`BeautifulSoup`中使用的一些方法，例如`fing`及`find_all`等，其实就是在调用`Tag`的方法。
2. **Navigablestring**：继承自`python`中的基本数据类型`str`，基本用法一致
3. **BeautifulSoup**：继承自是`Tag`类，用来生成`BeautifulSoup`树，来方便使用查找方法，本质还是在调用`Tag`的方法。
4. **Comment**：继承自`Navigablestring`，是一个特殊的`Navigablestring`对象。

**`contents`和`children`**
- 返回某个标签下的直接子元素，包括所有字符串。区别在于：`contents`返回的是一个列表，而`children`返回的是一个迭代器。


## 正则表达式
#### 匹配单个字符
- `match`函数匹配到在匹配到一个符合条件的字符之后就会停止匹配，只要遇到第一个不符合条件的字符，就会停止。

**点（.）**
- 匹配任意的字符。
```
test = "hello"
ret = re.match('.', test)
print(ret.group())  # group() 返回match函数匹配到的结果
# 点不能匹配\n
```
**\d**
- 匹配`0-9`的数字。
```
test = "123"
ret = re.match('\d', test)
print(ret.group())
```
**\D**
- 与`\d`刚好相反，匹配`0-9`以外的任何字符。

**\s**
- 匹配空白字符（`\n`，`\t`，`\r`，空格）。
```
test = "\n\t"
ret = re.match('\s', test)
print(ret.group())
```
**\w**
- 匹配小写字母`a-z`，大写字母`A-Z`，数字`0-9`，下划线`_`。
```
test = "_"
ret = re.match('\s', test)
print(ret.group())
```
**\W**
- 与`\w`用法相反，匹配所有`\w`匹配不到的字符。

**组合用法[]**
- 只要满足中括号内的任意一个字符就算匹配成功。
```
test = "2"
ret = re.match('[0123]', test)
print(ret.group())
```
- 组合用法之前所讲述的匹配方法都可以使用中括号的方式进行匹配。
```
\d : [0-9]
\D : [^0-9]
\s : [\n\r\t ]
\w : [a-zA-Z0-9]
\W : [^a-zA-Z0-9]
```

#### 匹配多个字符
**`*`**
- 匹配0个或者多个满足条件的字符。
```python
text = '12345'
ret = re.match('\d*', text)
print(ret.group())
```
- 0个，其实也可以理解为在没有匹配到满足条件的字符时，不会报错，会直接执行，返回空值。

**+**
- 匹配1个或者多个满足条件的字符。
```
text = 'asdc+asdc'
ret = re.match('\w+', text)
print(ret.group())
```
**?**
- 匹配0个或者1个满足条件的字符
```
text = '12345'
ret = re.match('\d?', text)
print(ret.group())
```
**{m}**
- 匹配m个满足条件的字符。
```
text = 'asdc+asdc'
ret = re.match('\w{2}', text)
print(ret.group())
```
**{m,n}**
- 匹配m到n个满足条件的字符。
- 匹配满足条件的最大个数。
```
# 情况一
text = 'asdc+asdc'
ret = re.match('\w{2,4}', text)
print(ret.group())

# 情况二
# 即使匹配条件中的需匹配字符数量大于被匹配字符中符合条件的数量，仍然会匹配成功，但是只会返回被匹配字符中符合条件的数量
text = 'a'
ret = re.match('\w{2}', text)
print(ret.group())
```

#### 其他符号
**^(脱字号)**
- 表示以某个字符开始。

**$**
- 表示以某个字符结束。

**|**
- 匹配多个符合条件的表达式或者字符串。
```
text = 'http'
ret = re.match('http|https', text)
print(ret.group())
```

#### 贪婪模式和非贪婪模式
```
text = "0123456"
ret = re.match('\d+',text)
print(ret.group())
# 系统默认为贪婪模式，会匹配最大数量的符合条件的字符串
输出 0123456

text = "0123456"
ret = re.match('\d+?',text)
print(ret.group())
# 加上问号 ? 即是非贪婪模式
```
**案例**
```
###### 匹配0-100的数字
text = "0"
ret = re.match('[1-9]?\d$|100$',text)
print(ret.group())

```

### `re`模块中的常用函数
- `match`函数，只能从字符换的开始进行查找，如果开始不匹配则不会继续。
- `search`函数，可以在整个字符串中进行查找。

**分组**
```
text = "apple's price is $99,orange's price is $10,banana's price is $15"
ret = re.search(".*(\$\d+).*(\$\d+).*(\$\d+)", text)
# 一个小括号包含的部分就是一个分组
print(ret.group())  # 等价于 ret.group(0)
print(ret.group(1)) # 返回第一个分组
print(ret.group(2)) # 返回第二个分组
print(ret.group(1,2))  # 返回所有选中分组
print(ret.groups())  # 返回所有分组的查询结果
```
#### `findall`函数
- 查询所有符合条件的字符并返回。
- 返回的是一个列表。
```
text = "apple's price is $99,orange's price is $10,banana's price is $15"
ret = re.findall("\d+", text)
print(ret)
```
#### `sub`函数
- 将查询到的符合条件的字符替换成其他字符串。
```
text = "apple's price is $99,orange's price is $10,banana's price is $15"
ret = re.sub("\d+", "0",text)
print(ret)
```

```
html = '''
<div>
<p>工作职责：</p>
<p>&nbsp;1）对所需的网络数据源进行数据抓取；</p>
<p>&nbsp;2）负责信息提取、处理、优化、过滤、清洗等工作；&nbsp;</p>
<p>&nbsp;3）完成团队安排的其他工作。 &nbsp;</p>
<p>职位要求：</p>
<p>&nbsp;1）有python实习经验优先考虑</p>
<p>&nbsp;2）熟悉Python语言；了解scrapy爬虫框架；</p>
<p>&nbsp;3）有一定JS,CSS基础，熟悉XML,HTML语言和JavaScript等；&nbsp;&nbsp;</p>
<p>&nbsp;4）一定的SQL基础，熟悉主流Mysql, Redis等数据库；</p>
<p>&nbsp;5）熟悉正则表达式,Xpath,Css选择器等网页解析语言；</p>
</div>
'''

ret = re.sub("<.+?>", "", html)
print(ret)
```
#### `split`函数
- 通过正则表达式来进行分割字符串
```
text = "hello world!I'm Kang"
ret = re.split("[^a-zA-Z]", text)
print(ret)
```
#### `compile`函数
- 对于一些需要经常用到的正则表达式，可以使用`compile`进行编译，下次可以直接再使用即可。
```
# 使用compile
text = "the number is 13.14"
r = re.compile('''
    \d+  # 小数点前面的部分
    \.?  # 小数点本身
    \d*  # 小数点后面的部分
''', re.VERBOSE)
ret = re.findall(r, text)
print(ret)

# 不使用compile
text = "the number is 13.14"
ret = re.findall("\d\.?\d*", text)
print(ret)
```


## 文件处理
### `json`模块
`python`中的`json`模块能帮助我们读取和保存`json`文件。
1. 本质上，`json`文件是一个字符串。
2. 只有基础的数据类型才能够被转换成json文件(list,dict,tuple,str,int,float等)。

**将字典或列表转成`json`**
```
import json

persons = [
    {
        'username': "黄勇",
        'age': 18,
        'country': 'China'
    },
    {
        'username': "大鹏 ",
        'age': 28,
        'country': "American"
    }
]
json_str = json.dumps(persons, ensure_ascii=False)
```
- `json`包在进行`dumps`的时候，只能存放`ascii`的字符，因此会将中文转义，我们可以使用`ensure_ascii=False`来关闭这个特性。

**将`json`数据直接保存到文件中**
```
import json
persons = [
    {
        'username': "黄勇",
        'age': 18,
        'country': 'China'
    },
    {
        'username': "大鹏 ",
        'age': 28,
        'country': "American"
    }
]

with open('person.json', 'w', encoding='utf-8') as fp:
    json.dump(persons, fp, ensure_ascii=False)
```
- `dump`函数有一个`fp`参数，可以直接将数据`dump`到本地文件中。

**将一个`json`字符串`loads`字典或列表**
```
import json

json_str = '[{"country": "China", "username": "黄勇", "age": 18}, {"country": "American", "username": "大鹏 ", "age": 28}]'
persons = json.loads(json_str)
print(persons, type(persons))
```
**直接从文件中读取`json`字符串**
```
import json

json_str = '[{"country": "China", "username": "黄勇", "age": 18}, {"country": "American", "username": "大鹏 ", "age": 28}]'

with open('person.json', 'r', encoding='utf-8') as fp:
    persons = json.load(fp)
    print(person)
```

### `csv`模块
**读取`csv`文件**
```
import csv

# 第一种方法 → 利用csv模块下的reader函数进行读取。
def read_csv_01():
    with open("stock.csv", 'r') as fp:
        reader = csv.reader(fp)  # 返回的是一个迭代器对象
        next(reader)
        # 利用for循环进行遍历，每一项都是一个列表
        for i in reader:
            value = {'name': i[3], 'volumn': i[-1]}
            print(value)

# 第二种方法 → 利用csv模块下的DictReader函数进行读取。
def read_csv_02():
    with open("stock.csv", 'r') as fp:
        reader = csv.DictReader(fp)
        # 每一项都是一个字典
        for i in reader:
            value = {'name': i['secShortName'], 'volumn': i['turnoverVol']}
            print(value)

if __name__ == '__main__':
    read_csv_01()
```
**写入`csv`文件**
```
import csv

# 方法1 → 用csv.writer方法进行写入
def write_csv_01():
    headers = ['username', 'age', 'height']
    value = [
        ('张三', 18, 180),
        ('李四', 19, 190),
        ('王五', 20, 160)
    ]
    with open("classroom1", 'w', encoding='utf-8', newline='') as fp:
        writer = csv.writer(fp)
        writer.writerow(headers) # 写入头部信息
        writer.writerows(value)  # writer.writerows 一次性写入

# 方法2 → 用csv.DictWriter方法进行写入
def write_csv_02():
    headers = ['username', 'age', 'height']
    value = [
        {'username': '张三', 'age': 18, 'height': 180},
        {'username': '李四', 'age': 19, 'height': 190},
        {'username': '王五', 'age': 20, 'height': 160}
    ]
    with open("classroom2", 'w', encoding='utf-8', newline='') as fp:
        writer = csv.DictWriter(fp, headers)  # 需要传入头部信息变量
        writer.writeheader()                  # 然后调用writer.writeheader()进行头部信息写入
        writer.writerows(value)               # 其他信息写入
        
if __name__ == '__main__':
    write_csv_02()
```

### MySQL模块
**用python连接数据库**
- `python`想要连接数据库，需要使用驱动程序，驱动程序有很多种：`mysqldb`, `mysqlclient`, `pymysql`等。我们一般使用`pymysql`。
```
import pymysql

conn = pymysql.connect(host='localhost', user='root', password='qq941115', 
                       database='python_mysql', port:3306)
                       # 设置连接数据库的参数设置（用户，密码，数据库，端口号……）
# 连接指定数据库
cousor = conn.cousor()

sql = '''
INSERT INTO user01 VALUES
(1,'A',18,123)
'''

cousor.execute(sql) # 向指定表格插入数据，删除和更新都是同理，需要使用字符串将SQL语句包含进去
cousor.commit()  # 在进行数据插入，删除和更新操作后都需要进行映射操作

cousor.close()  # 关闭连接
```

**查询数据**
- 查询数据时需要使用SQL语句。
```
import pymysql

conn = pymysql.connect(host='localhost', user='root', password='qq941115', 
                       database='python_mysql', port:3306)
                       # 设置连接数据库的参数设置（用户，密码，数据库，端口号……）
# 连接指定数据库
cousor = conn.cousor()

sql = """
SELECT * FROM user01
"""
```
**`fetchone()`**
- 查询一条数据，第一次使用显示第一条数据，第二次使用显示第二条，类似于生成器的next()。
```
corsur.execute(sql)
result = corsur.fetchone()  
print(result)

cousor.close()
```
**`fetchall()`**
- 查询表中所有数据。
```
corsur.execute(sql)
results = corsur.fetchall()  # 查找表中所有数据
print(results)

cousor.close()
```
**`fetchmany(size)`**
- 查询指定数量(`size`)的数据
```
corsur.execute(sql)
result_1 = corsur.fetchmany(2)  # 查找表中指定数量的数据
print(result_1)

cousor.close()
```

## 多线程

### `threading`模块
```
import time
import threading

def coding():
    for x in range(3):
        print("正在写代码%i" %x)
        time.sleep(1)

def drawing():
    for i in range(3):
        print("正在画图%i" %i)
        print(threading.current_thread())  # 查看当前线程的名字
        time.sleep(1)

def main():
    t1 = threading.Thread(target=coding)  # 通过target参数指定需要运行的代码
    t2 = threading.Thread(target=drawing)

    t1.start()  # 让线程开始运行
    t2.start()

    print(threading.enumerate())  # 查看当前线程总数
    
if __name__ == '__main__':
    main()
```
**也可以使用类的方法进行实现**
- 继承来自threading.Thread下面的run方法来进行创建，基本和函数下进行创建一致。
```
import time
import threading

class CodingThread(threading.Thread):
    def run(self):
        for x in range(3):
            print("正在写代码%i" % x)
            time.sleep(1)


class DrawingThread(threading.Thread):

    def run(self):
        for i in range(3):
            print("正在画图%i" % i)
            time.sleep(1)


def main():
    t1 = CodingThread()
    t2 = DrawingThread()

    t1.start()
    t2.start()

    print(threading.enumerate())

if __name__ == '__main__':
    main()

```

### 多线程中共享全局变量的问题
- 因为多线程都是在同一个进程中进行运行的，而且由于多线程是无序运行，所以可能会造成数据错误。
- 因此在共享全局变量时，可以使用`threading`中 的`Lock`类，当某个进程在访问某个全局变量时，可以进行上锁操作，当该线程执行完毕后，进行解锁，让其他线程再继续访问。
```
import threading

VALUE = 0  # 设置全局变量
gLock = threading.Lock()  # 使用Lock类

def all_value():
    global VALUE  # 声明该函数使用了全局变量VALUE
    gLock.acquire()  # 进行上锁操作
    for v in range(100000):
        VALUE += 1
    gLock.release()  # 进行解锁操作
    print('value: %i' % VALUE)

def main():
    for i in range(2):
        t1 = threading.Thread(target=all_value)
        t1.start()

if __name__ == '__main__':
    main()

```
- 上锁解锁只需要在进行改变全局变量的时候，访问全局变量的时候无需加锁。

#### `Lock`版本生产者和消费者模式
- 生产者和消费者模式是编程开发的常见模式，生产者的线程负责进行生产一些数据存放到中间变量，然后由消费者取出这些变量进行消耗。需要用到的中间变量经常是一些全局变量，因此需要使用锁来保证数据的完整性。
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import threading
import time
import numpy as np

gMoney  = 1000
gTimes = 0
gLock = threading.Lock()

class Worker(threading.Thread):

    def run(self):
        global gMoney
        global gTimes
        money = np.random.randint(100, 1000)
        while True:
            gLock.acquire()  # 上锁
            if gTimes == 10:
                gLock.release()  # 解锁
                break
            gMoney += money
            gLock.release()  # 解锁
            gTimes += 1
            time.sleep(1)
            print("%s生产者生产了%d元钱，剩余%d元钱" %(threading.current_thread(),
                                          money, gMoney))

class Consumer(threading.Thread):

    def run(self):
        global gMoney
        global gTimes
        money = np.random.randint(100, 1000)
        while True:
            gLock.acquire()  # 上锁
            if gMoney >= money:
                gMoney -= money
                print("%s消费者消费了%d元钱，剩余%d元钱"
                      % (threading.current_thread(), money, gMoney))
            else:
                if gTimes == 10:
                    gLock.release()  # 解锁
                    break
                print("%s消费者准备消费%d元钱，剩余%d元钱，无法消费！！！"
                      % (threading.current_thread(), money, gMoney))
            gLock.release()  # 解锁
            time.sleep(1)

def main():
    for i in range(3):
        t = Worker()
        t.start()

    for j in range(4):
        t = Consumer()
        t.start()

if __name__ == '__main__':
    main()
```


2018年6月12日