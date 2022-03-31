<h1 align="center">django框架</h1>
<hr>

## 一、基础概念

### 1.1 起源 & 现状

* 官网：<http://www.djangoproject.com>

* 起源：2005年发布的web框架，使用python编写，早期用于新闻和内容管理

* 特点：重量级、开源

* 组件：

  `基本配置` `路由系统` `原生HTML模板信息`  `视图View` `Model模型，数据库连接和ORM数据库管理`

  `中间件` `Cookie & Seesion` `分页` `分页数据库后台管理系统admin`

* 用途：

  `网站后端开发` `微信公众号/微信小程序后台开发` 

  `基于HTTP/HTTPS协议的后台服务开发(在线语音/图像识别服务器、在线第三方身份验证服务器)`

* 版本：目前最新版是django 4.0，对应python的版本是v3.8及以上

### 1.2 安装 django

* 使用shell安装

```shell
# 若没有pip先安装pip
$ sudo pacman -S python-pip
$ sudo pip install django[==2.2.12] -i源	# 默认安装最新版，也可以指定版本安装
```

- 使用pycharm安装 

```shell
# 1. 将pip更新至最新版本
# 2. 添加国内镜像源`https://pypi.tuna.tsinghua.edu.cn/simple/ `
# 3. 搜索软件包django，下载清华源下的django 
```

* 查看已安装的版本

```shell
$ python -m django --version				# win下命令相同
```

### 1.3 创建项目

#### 1.3.1 创建服务

1. 切换到项目上级目录，如```$ cd /home/mycode```，不可以是`document root`目录，否则存在安全风险

2. 创建一个名为如`mysite`项目，项目名不能为`django `&`test`

```shell
$ django-admin startproject mysite
```

#### 1.3.2 启动服务

`runserver`仅适用于开发测试阶段，正是上线后需要使用`uwsgi`启动

* 使用终端启动

```shell
# mysite
$ python manage.py runserver [端口号=8000]
```

* 使用pycharm直接启动
```shell
# 方式一，设置运行参数：找到manage.py的修改运行配置项 -- 修改形参为 runserver -- 右击manage.py 运行启动服务器
# 方式二，pycharm调用终端：快捷键alt+f12，启动终端，在终端中输入启动指令（推荐）
```
* 解决端口被占用问题

```shell
$ sudo lsof -i :被占用的端口
$ kill -9 被占用端口的PID
```

### 1.4 项目目录

#### 1.4.1 结构概览

```shell
mysite/
	manage.py
	mysite/
		 __init__.py
         settings.py
         urls.py
         asgi.py
         wsgi.py
```

#### 1.4.2 内容解析

* 外层的**`mysite/`**文件夹是项目文件夹，命名和框架无关，用户可以自定义名称
* 里层的**`mysite/`**文件夹是实际的Python项目模块，方便导包，如`(from . import views)`
* **`manage.py`**：一个命令行应用程序，允许使用辅助命令运行/调试/配置django项目
* **`mysite/__init__.py`**：一个空的py文件,一个模块必须要有一个`__init__.py`文件才能算作一个模块
* **`mysite/settings.py`**：django项目的配置文件,也可以添加自定义的变量作为项目的全局变量使用
* **`mysite/urls.py`**：项目的主路由配置文件，负责URL解析，所有的动态路径必须先走该文件进行匹配
* **`mysite/wsgi.py`**：Web Server Gateway Interface，WEB服务网关接口的配置文件，部署项目时使用
* **`mysite/asgi.py`**：运行在 ASGI 兼容的 Web 服务器上的入口,为了支持异步网络服务器和应用而新出现的 Python 标准
```shell
$ python manage.py								  # 查看所有辅助命令
```

```python
# 返回结果:
[django]
    check
    compilemessages
    createcachetable							   # 创建缓存数据库表 
    dbshell
    diffsettings								   # 查看与默认配置项(django/conf/global_settings.py)不同之处
    dumpdata
    flush
    inspectdb
    loaddata
    makemessages
    makemigrations									# 提交数据库改动至migrations文件夹
    migrate											# 提交migrations改动至数据库
    sendtestemail
    shell											# 提供交互式执行项目工程代码调试功能（不能远程）
    showmigrations
    sqlflush
    sqlmigrate
    sqlsequencereset
    squashmigrations
    startapp										# 创建应用
    startproject									# 创建项目
    test
    testserver

[sessions]
    clearsessions									# 清除已过期的session数据，可以每晚执行一次

[staticfiles]
    collectstatic									# 部署时，将静态文件转移到项目文件夹外使用
    findstatic
    runserver										# 启动服务，开发或测试环境使用
```

#### 1.4.3 配置文件

* Django 的配置文件包含 Django 应用的所有配置项，是一个使用模块级变量的一个 Python 模块
* 配置文件可以有动态语法（如`if socket.gethostname == 'my-latop:...else:...'`），但严禁语法错误，会引发崩溃
* 所有配置文件在`django/conf/global_settings`中，项目创建同时会有一个覆盖第一个的配置文件`mysite/mysite/settings.py`，综合结果在`django/conf/settings.py中`，一般只对它读操作
* 多种配置文件切换：如开发和测试两份配置文件（同时只有一个生效）

```python
# 方法一：新建一个my_settings.py，显式指定配置文件
# mysite/mysite
python manage.py runserver --settings=mysite.my_settings	
```

```python
# 方法二：修改manage.py
def main():
    """Run administrative tasks."""
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mysite.my_settings')
    ...
```

```python
# 方法三：在具体应用中使用settings，注意应从django.conf中导入settings，且结果是一个可读对象，因为整合了用户配置和系统配置
# mysite/my_app/views.py
from django.conf import settings
if settings.DEBUG:
    ...
else:
    ...
```

* 某个应用或脚本单独使用一套配置：https://docs.djangoproject.com/zh-hans/4.0/topics/settings/

* 更多信息：https://docs.djangoproject.com/zh-hans/4.0/ref/settings/

| 默认配置参数                                        | 说明                         |
| --------------------------------------------------- | ---------------------------- |
| `DEBUG = True | False`                              | 测试或开发模式               |
| `BASE_DIR = Path(__file__).resolve().parent.parent` | 项目路径                     |
| `ALLOWED_HOSTS = []`                                | 允许访问本项目host请求头的值 |
| `INSTALLED_APPS = [...]`                            | 项目应用列表                 |
| `MIDDLEWARE = [...]`                                | 中间件列表                   |
| `ROOT_URLCONF = 'mysite.urls'`                      | 根级urls目录                 |
| `TEMPLATES = [...]`                                 | 模板配置                     |
| `WSGI_APPLICATION ='mysite.wsgi.application' `      | 配置wsgi应用程序             |
| `DATABASES = [...]`                                 | 数据库配置                   |
| `LANGUAGE_CODE` = 'en-us'                           | 系统语言，汉语：'zh-Hans'    |
| `TIME_ZONE = 'UTC'`                                 | 时区，中国：'Asia/Shanghai'  |
| `STATIC_URL = '/static/'`                           | 配置静态文件路由             |

### 1.5 URL

#### 1.5.1 格式

* URL 全写为 Uniform Resource Locator，即统一资源定位符，用来表示资源的地址

* 一般语法格式

```   shell
protocol://hostname[:port]/path[?query][#fragment]
#   协议       主机    端口  路由  查询参数      锚点
```

#### 1.5.2 常见protocol

| protocol | 访问类型           | 特点                                      |
| -------- | ------------------ | ----------------------------------------- |
| http     | 超文本传输协议     | 不加密                                    |
| https    | 安全超文本传输协议 | 安全网页，加密所有信息交换（非对称+对称） |
| ftp      | 文件传输协议       | 下载、上传文件至网站                      |
| file     | 本地访问本地       | 标记本地资源                              |

### 1.6 业务流程

#### 1.6.1 总体流程

1. `django`从配置文件中根据`ROOT_URLCONF`找到主路由文件，默认是同名目录下的`urls.py`文件
2. 加载主路由文件下的`urlpatterns`参数
3. 依次匹配`urlpatterns`参数的URL，若命中则停止匹配后续URL
4. 命中后，调用对应视图函数处理请求或应用，返回响应。若未命中则返回404响应 -- 客户端错误

#### 1.6.2 路由解析

路由解析列表即`urlpatterns`，是通过`ROOT_URLCONF`配置而引导的路由文件中的路由解析列表，列表的每一项是一个`path`函数，用于描述路由和视图函数的对应关系，默认`urlpatterns`列表如下：

```python
from django.contrib import admin

urlpatterns = [
    path('admin/', admin.site.urls, name=别名),	# 别名必须要关键字传参
]
```

#### 1.6.2 路由正则

路由正则是`urlpatterns`列表中元素`path`函数的第一项参数，本质上是类似正则表达式的存在（下面用路由正则简述），用来匹配浏览器请求中的路由`path`，命中后交给第二项参数即视图函数进行处理业务逻辑，返回结果，匹配规则如下

* 当`DEBUG=True`并且未配置任何自定义`url`时，访问主页`http://127.0.0.1:8000/`返回的是django默认主页(小火箭)
* 视图函数自上而下匹配，若命中后则终止匹配，同时调用对应的视图处理函数
* 遍历完所有视图函数还未匹配到则返回404错误，设计时要注意匹配先后顺序
* 路由正则从 `http://127.0.0.1:8000/`处开始匹配，根路由使用空字符串表示`""`
* 视图处理函数不需要在`settings.py`中额外配置，通常将它建立在`urls.py`同级目录下，如新建一个`views.py`文件
* 常用匹配手段有`全路径匹配`、`path转换器`、`正则表达式`

```python
urlpatterns = [
    # 匹配: http://127.0.0.1:8000/ 如果在最後面 匹配所有未匹配到的内容
    path("", views.indexView),
    
    # 匹配: http://127.0.0.1:8000/admin/
    path('admin/', admin.site.urls),
    
    path('username/<username>/check'),	# 匹配username/任意内容/check
    
    # 正则表达式：htttp://127.0.0.1:8000/birthday/四位数数字/俩位数数字/俩位数数字
    re_path(r"^birthday/(?P<Y>\d{4})/(?P<m>\d{1,2})/(?P<d>\d{1,2})$", views.returnBirthday),
]
```

* path 转换器匹配规则

| 转换器 | 匹配规则                    | 案例                                                         |
| :----: | --------------------------- | ------------------------------------------------------------ |
|  str   | 非空字符串，除连字符`/`以外 | `login/<str:uname>`匹配`.../login/qtx`                       |
|  int   | 自然数，返回值也是int       | `calculate/<int:x>/<str:op>/<int:y>`匹配`../calculate/100/add/200` |
|  slug  | 字母、数字、连字符、下划线  | `address/<slug:address>`匹配`address/anhui-anqing-246000`    |
|  path  | 非空字段，包括路径分隔符`/` | `bookdir/<path:path>`匹配`bookdir/INTRODUCTION_TO_LINEAR_ALGEBRA/Apply` |

```python
urlpatterns = [
    # path转换器：http://127.0.0.1:8000/整数/字符串/整数
 	path("<int:x>/<str:op>/<int:y>", views.calculate), 
]
# 对应视图函数(在视图文件中)
def calculate(request: HttpRequest, x, y, op):
    match op:
        case "add":
            r = x + y
        case "sub":
            r = x - y
        case "mul":
            r = x * y
        case "div":
            r = x / y
        case _:
            r = "页面不存在"
    return HttpResponse(r)
```

* re_path匹配规则（正则规则）

```python
urlpatterns = [
    # 匹配：htttp://127.0.0.1:8000/birthday/四位数数字/一到俩位数数字/一到俩位数数字
    re_path(r"^birthday/(?P<Y>\d{4})/(?P<m>\d{1,2})/(?P<d>\d{1,2})$", views.returnBirthday),
    
    # 匹配：htttp://127.0.0.1:8000/birthday/一到俩位数数字/一到俩位数数字/四位数数字
    re_path(r"^birthday/(?P<m>\d{1,2})/(?P<d>\d{1,2})/(?P<Y>\d{4})$", views.returnBirthday),
]
# 对应视图函数(在视图文件中)
def returnBirthday(request: HttpRequest, Y, m, d):
    r = f"你输入的生日是{Y}年-{m}月{d}日"
    return HttpResponse(r)
```

#### 1.6.3 视图函数

* 关联路由和视图函数

```python
# mysite/mysite/urls.py
urlpatterns = [
    path("匹配规则", view.视图函数名),
]
```

* 定义视图函数

```python
# mysite/mysite/views.py
def 视图函数名(request, [参数列表]):
    # 逻辑处理
    return HttpResponse
```

* 注意事项
  * 每一个视图函数的首参数必须是`HttpRequest`类的对象，通常写作`request`，因为视图函数本质是`HttpRequest`类的实例方法
  * 每一个视图函数的返回值必须是`HttpResponse`类的对象
  * 视图函数通过关键字传参，对应路由正则设计的变量名、组名
  * 视图函数不可重名，但可以重复使用，可以添加装饰器

#### 1.6.4 自定义转换器

```python
# mysite/utils/converters.py

class Myconverter:
    regex = "正则规则" # 正则里不可使用^$开始结束
    
    def to_python(self, value):
        return value如果符合正则条件时对value进行的处理（处理完会直接交给视图函数）
    
    def to_url(self, value):
        return 将视图函数内容反转成url
```

```python
# mysite/mysite/urls.py

register_converter(Myconverter, 别名)

urlpatterns = [
    path('<别名:传给视图函数参数名>', 视图处理函数),
]
```

### 1.7 `HTTP`请求

请求：浏览器通过HTTP协议发送给服务端的数据

响应：服务端接收到请求后做相应的处理再回复给浏览器端的数据，每一个请求都有响应

#### 1.7.1 请求范文

```python
POST /calculate/ HTTP/1.1					# 请求行

Host: 127.0.0.1:8888						# 请求头
Connection: keep-alive
Content-Length: 34
Cache-Control: max-age=0
sec-ch-ua: " Not A;Brand";v="99", "Chromium";v="96", "Microsoft Edge";v="96"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
Origin: http://127.0.0.1:8888
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36 Edg/96.0.1054.62
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: http://127.0.0.1:8888/calculate/
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6
Cookie: csrftoken=iOhLaNWAkucXlE1dIn2WP4UX9SV0hlY26tHRttuXF8gGP5OscKpQjAD45f69OynT
   
b'x=200&op=mul&y=2&uhobby=1&uhobby=2'		# 请求体
```

#### 1.7.2 请求类型

|   方法    | 描述                                                         |
| :-------: | :----------------------------------------------------------- |
|   `GET`   | 请求指定的页面信息，并返回实体主体。                         |
|  `HEAD`   | 类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头 |
|  `POST`   | 向指定资源提交数据进行处理请求，数据被包含在请求体中。POST请求可能会导致新资源的建立或已有资源的修改。 |
|   `PUT`   | 从客户端向服务器传送的数据取代指定的文档的内容。             |
| `DELETE`  | 请求服务器删除指定的页面。                                   |
| `CONNECT` | HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。     |
| `OPTIONS` | 允许客户端查看服务器的性能。                                 |
|  `TRACE`  | 回显服务器收到的请求，主要用于测试或诊断。                   |

#### 1.7.3 请求对象

请求对象指的是`HttpRequest`类的对象

| 属性        | 类型             | 表示                  | 举例                                                    |
| ----------- | ---------------- | --------------------- | ------------------------------------------------------- |
| `method`    | `str`            | 请求类型              | `GET`、`POST`...                                        |
| `GET`       | `QueryDict`      | 查询参数，在`url`中   | `<QueryDict: {'name': ['qtx', 'sbw'], 'age': ['18']}>`  |
| `POST`      | `QueryDict`      | 提交参数，在请求体中  | `<QueryDict: {'x': ['100'], 'op': ['mul'], 'y':['2']}>` |
| `path_info` | `str`            | URL                   | `/index/`                                               |
| `scheme`    | `str`            | 协议类型              | `http`                                                  |
| `FILES`     | `MultiValueDict` | 用户上传文件字典      | `my_file = request.FILE.get('my_file')`                 |
| `COOKIES`   | 字典             | 按域分的用户信息      | `{sessionid: 'xxxx'}`                                   |
| `session`   | `SessionStore`   | 按地址分的用户信息    | `{'uid':'xxx', 'uname':'xxx'}`                          |
| `body`      | `bytes`          | 请求体内容，post或put | `b'uhead=head.png'`                                     |
| `META`      | `dict`           | 请求头元数据          | `{'REMOTE_ADDR': '127.0.0.1',...`                       |

| 方法              | 类型  | 表示           | 举例                                 |
| ----------------- | ----- | -------------- | ------------------------------------ |
| `get_host()`      | `str` | 服务端主机     | `'176.4.10.25:8888'`                 |
| `get_full_path()` | `str` | 请求的完整路径 | `'/index/?name=qtx&name=sbw&age=18'` |

#### 1.7.4 查询字典

查询字典即`QueryDict`（查询字符串）类的对象，常用方法如下：
| 方法                      | 类型   | 表示                                           | 举例                                |
| ------------------------- | ------ | ---------------------------------------------- | ----------------------------------- |
| `get(查询字符串的键)`     | `str`  | 获取查询字符串变量名对应的值，若有多个，取最后 | `get("name")` -> `'sbw'`            |
| `getlist(查询字符串的键)` | `list` | 获取查询字符串变量名对应的值，取全部           | `getlist("name")`->`["qtx", "sbw"]` |

### 1.8 `HTTP`响应

#### 1.8.1 响应范文

```python
HTTP/1.1 200 OK		# 响应行
	
Date: Sat, 05 Mar 2022 04:21:59 GMT		# 响应头
Server: WSGIServer/0.2 CPython/3.10.2
Content-Type: text/html; charset=utf-8
X-Frame-Options: DENY
Vary: Cookie
Content-Length: 1358
X-Content-Type-Options: nosniff
Referrer-Policy: same-origin
Cross-Origin-Opener-Policy: same-origin
Set-Cookie: csrftoken=GGQf4VhaB0IbcKruwAWT4xyrJv4ivjez1qYtC8XwFKuQcZWODjeyaGlJ8WiDOfOI; expires=Sat, 04 Mar 2023 04:21:59 GMT; Max-Age=31449600; Path=/; SameSite=Lax

HTML页面  # 响应体（如果Conten-Type: json, 那么响应体是json数据）
```

#### 1.8.2 响应类型

| 响应码 | 含义                                      |
| ------ | ----------------------------------------- |
| 1xx    | 请求已接收,需要请求者继续执行操作         |
| 2xx    | 成功,操作被成功接受并处理                 |
| 200    | 请求成功                                  |
| 3xx    | 重定向,需要进一步操作以完成请求           |
| 301    | 永久重定向                                |
| 302    | 临时重定向                                |
| 4xx    | 客户端错误,请求包含语法错误或无法完成请求 |
| 404    | 请求的资源不存在                          |
| 5xx    | 服务器错误,服务器在处理请求中发生了错误   |
| 500    | 服务器内部错误                            |

#### 1.8.3 响应对象

* `HttpResponse`类常用参数

```python
HttpResponse(content=bytes|str, content_type="text/html|json", status=200, headers=)		# 伪代码
#			    响应体		     响应数据类型				 		状态码     	头部信息
```

* 常见`MIME(Multipurpose Internet Mail Extensions)`(即返回数据类型)

| `content_type`常用参数 | 用途            |
| ---------------------- | --------------- |
| `text/html`            | 默认的,html文件 |
| `text/plain`           | 纯文本          |
| `text/css`             | css文件         |
| `text/csv`             | csv文件         |
| `text/javascript`      | js文件          |
| `multipart/form-data`  | 文件提交        |
| `application/json`     | json传输        |
| `application/xml`      | xml文件         |
| ...                    | ...             |


* `HttpResponse`子类
| 类型 | 作用 | 状态码 |
|-|-|-|
| `HttpResponseRedirect` | 重定向 | 302 |
| HttpResponseNotModified | 未修改 | 304 |
| HttpResponseBadRequest | 错误请求 | 400 |
| HttpResponseNotFound | 没有对应的资源 | 404 |
| HttpResponseForbidden | 请求被禁止 | 403 |
| HttpResponseServerError | 服务器错误 | 500 |

### 1.9 请求处理

* 无论是`GET`还是`POST`请求,都可以交给同一个视图函数去处理

```python
# mysite/mysite/views.py
def suggest(request: HttpRequest, ):
    def get():
        return render(request, "suggest.html", )

    def post():
        return HttpResponse("<h1>感谢您的建议</h1>")

    def notFoundMethod():
        return HttpResponse("未找到method")

	return locals().get(request.method.lower(), notFoundMethod)()
```

* <font color="orange">无论是`GET`还是`POST`请求,都可以处理查询字符串并且要使用`request.GET.get()|reqeust.GET.getlist()`</font>

```python
# 获取查询字符串对象`Query Dict`
request_obj.GET							# <QueryDict: {'answer': ['A', 'B'], 'uname': 'Tom'}>
# 通过查询字符串对象获取数据
request_obj.GET.get('uname')			# 'Tom'
request_obj.GET.getlist('answer')		# ['A', 'B']
request_obj.GET.get('东方扥黄森囧的')	    # None
```

#### 1.9.1 GET的处理

* 产生`GET`请求的场景
```shell
# 1. 浏览器输入url地址并回车执行
# 2. 标签(<a>标签/<img>标签/<script>标签等)中的链接src=...被执行
# 3. form表单的method为GET请求时
```
* `GET`请求时携带的数据

```shell
# 1. url中的查询字符串：http://127.0.0.1:8000/test/?answer=A&answer=B&uname=Tom
# 2. cookie：自动提交
```

#### 1.9.2 POST的处理

* 产生`POST`请求的场景
```shell
# 1. 向服务器提交大量数据
# 2. 通过表单等`POST`请求传递数据
```
* `form`表单的`name`属性
```shell
# 1. 表单在提交(submit按钮)时会自动将表单内`name`属性的值和`value`属性的值拼接在一起,再以字典的形式提交给服务器
# 2. 组成字典形式的字符串存放在请求体中
```

* 服务器处理`POST`请求

```python
request_obj.POST								# 获取查询字符串对象QquerySet
request_obj.POST.get(name属性值)				  # 获取对象数据
request_obj.POST.getlist(name属性值)
```

#### 1.9.3 CSRF 验证

* `CSRF`即跨站请求伪造（Cross Site Request Forgey），默认情况下`django`对所有`post`请求进行`csrf验证`

* `CSRF`验证原理

  > 1. 用户第一次访问时，`django`生成一个`csrf_token`，并放到用户`cookie中`，会随请求提交到服务端
  > 2. 在用户的`post`表单中添加`{% csrf_token %}`，即隐藏的`input`项，随`post`请求而提交
  > 3. 在用户`post`提交中，验证`cookie`中的`csrf_token`与请求体中（或请求头）中的`csrfmiddlewaretoken`是否匹配

```html
# 携带POST请求的模板
<form action="/note/add/" method="post">
	{% csrf_token %}						{# 框架自动传参 + 设置cookie #}
{# ... #}       
</form>
```

* 上述实质工作是

```html
<form action="/note/add/" method="post">
	<input type="hidden" name="csrfmiddlewaretoken" value="{{ csrf_token }}">
{# ... #}       
</form>
```

```python
response.set_cookie("csrf_token", 值)
```

* `AJAX`中`CSRF`请求方式

```js
<script>
    function ajax_post(){
            var xhr = new XMLHttpRequest();
            xhr.open("post", "/test/", true);
            xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
    		// 本页面通过后端渲染后 csrf_token 才会生成
            xhr.setRequestHeader("X-CSRFToken", "{{csrf_token}}");
            var username = document.getElementById("username").value;
            var body = "username=" + username;
            xhr.send(body);
            xhr.onreadystatechange = function(){
                if(xhr.readyState==4){
                    document.getElementById("res").innerText=xhr.responseText;
                }
            }
        }
</script>
```

* 启用`CSRF`
  * 全局启用：`django.middleware.csrf.CsrfViewMiddleware`
  * 局部启用 ：`@csrf_protect`
  * 局部弃用：`@csrf_exempt`

#### 1.9.4 FBV与CBV

* 分别指函数视图和类视图，类视图具有类的特性：可继承、可重写等
* 类视图通过View类的`dispatch()`方法进行分发请求，可进行通用预处理和请求方法自识别，不存在返回403

````python
# mysite/应用/urls.py
urlpatterns = [
    path('example/', views.MyView.as_view()),
]
````

```python
# mysite/utils/base_view.py
class MyAbstractView(View):
    def dispatch(self, request, *args, **kwargs):
        """ 通用逻辑、校验逻辑
        	如果不满足直接返回携带错误信息的响应
        """ 
        return super().dispatch(request, *args, **kwargs)
```

```python
# mysite/应用/views.py
class MyView(MyAbstractView):
    def get(): pass
    def post(): pass
    def put(): pass
    def delete(): pass
    ...
```

#### 1.9.5 celery

* Celery 是一款python编写，简单、灵活、可靠的分布式系统，用于处理任务队列和任务调度
* broker：消息传输的中间件，生产者一旦有任务发送，将发送至broder（RQ或Redis）
* backend：用于存储消息/任务结果，可选跟踪和查询任务状态
* worker：工作者，消费/执行broker中消息/任务的进程或协程
* 官网网址：https://www.celerycn.io/
* 通常工作流程：异步任务/定时任务发送至broker >> worker监控任务并执行任务 >> worker将结果存储到backend中
* 安装celery

```shell
$ pip install celery
```

* 可选使用协程gevent作为worker

```shell
$ pip install gevnet
```

* Django + celery 操作流程

```python
# 一、添加celery.py文件
# mysite/mysite/celery.py
from celery import Celery
from jango.conf import settings			# 项目最终生效的配置文件（默认+覆写结果）
import os

# 为celery设置环境变量
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")
# 创建应用
app = Celery("mysite", 	# 项目名称
             broker="redis://[密码]@127.0.0.1:6378/1",	# 存放任务
             backend="redis://[密码]@127.0.0.1:6378/2")	# 存放任务结果
# 设置自动加载任务
app.autodiscover_tasks(settings.INSTALLED_APPS)
```

```python
# 二、添加tasks.py文件
# mysite/应用/tasks.py
from mysite.celery import app
from django.core.mail import send_mail

@app.task
def async_send_email("收件人", "正文"):
    r = send_mail("标题", "正文", "发送人", ["接收人"])
    return r
```

```python
# 三、试图函数中调用任务，获取结果
# mysite/应用/views.py
from .tasks import async_send_email
from celery.result import AsyncResult

def test_celery(request):
    async_result = async_send_email.delay("收件人", "正文")
    r = AsyncResult(async_result.id).get()	# 获取存储结果
    if r:
        # 发送成功逻辑
    else：
    	# 发送失败逻辑
```

```shell
# 四、启动redis、启动worker
sudo systemctl start redis
# mysite/
celery -A mysite worker -P gevent -c 1000 -l info	# 使用gevent协程处理(推荐)
# 或
celry -A mysite worker -l info 	# 使用多进程
# 或后台启动
nohup celery -A proj worker -P gevent -c 1000 > celery.log 2>&1 &
```

### 1.10 静态文件

#### 1.10.1 静态文件定义

* 不能与服务端做动态交互的文件叫静态文件
* 区分静态还是动态看数据的类型
* 静态文件如图片、视频、css、js、html(部分)

#### 1.10.2 配置静态文件

1. 配置`url`

```shell
# mysite/mysite/settings.py
STATIC_URL = '/static/'							# 127.0.0.1:8000/static/...
```

2. 配置存储路径（手动添加）

```python
# mysite/mysite/settings.py
STATICFILES_DIRS = (
	os.path.join(BASE_DIR, 'static'),			# mysite/static/
)
```

#### 1.10.3 访问静态文件

1. 通过`{% static %}`标签访问

```shell
# mysite/templates/example.html
{% load static %}								# 加载静态资源,放在html首部
<a herf="{% static '/image/qtx.jpg' %}"></a>	# 使用静态资源文件,实际地址是 mysite/static/image/qtx.jpg
```

2. 通过静态文件访问路径访问（不推荐）

```python
# mysite/templates/example.html
<img src="/static/images/lena.jpg">
<img src="http://127.0.0.1:8000/static/images/lena.jpg">
```

### 1.11 测试请求

* 方法：并行开发环境中，需要自己手动创建请求，以检查代码缺陷，可使用第三方模块`requests`完成测试

* 官网：https://docs.python-requests.org/zh_CN/latest/index.html

* 安装第三方库

```shell
$ pip install requests
```

* 常用方法

```python
import requests
r = requests.get(...)
r = requests.post(...)
r = requests.put(...)
r = requests.delete(...)
...
```

* 常用请求参数

```python
def requests(
	url,			# String，url请求地址，必填，格式如"http://127.0.0.1:8000/v1/token"
    data,			# String，请求体数据，格式如"username=laownag&age=20"
    json,			# list|tuple|dict，请求体数据，格式如{"username": "laowang", "age": 20}
    headers,		# dict，请求头数据，格式如{"authorization": "xxxxx"}
    params,			# dict，查询字符串内容拼接在url中，格式如{"key": "value"}
) -> r:
```

* 常用响应参数

```python
r.url				# String，url请求地址
r.status_code		# int，响应状态码
r.json()			# list|tuple|dict，json响应数据
r.text				# String，字符串格式响应内容
r.content			# bytes，二进制格式响应内容
```

## 二、T模板

### 2.1 `MVC`和`MTV`设计模式

#### 2.1.1 `MVC`设计

* 概念：一种为降低模块间耦合度，将服务器分为模型、视图、控制器三大部分的设计模式
  1. `Model`层负责对数据库进行封装
  2. `View`层负责向用户展示结果
  3. `Controller`层是核心,负责处理请求、获取数据、返回结果

![](https://tva3.sinaimg.cn/large/006bmdycgy1gzyy2nvathj30lf08swgy.jpg)

#### 2.1.2 `MTV`设计

* 概念：在`MVC`基础上，对控制层进行拆解，重新划分业务的设计模式
  1. `Model`层负责对数据库进行封装
  1. `Model`层负责对数据库进行封装
  1. `View`层是核心,负责接收请求、获取数据、返回结果

![](https://tvax1.sinaimg.cn/large/006bmdycgy1gzyy7048dij30lf08s0v7.jpg)

### 2.2 创建模板

#### 2.2.1 模板的定义

* 模板是特殊的`html`文件，遵从`django`自己的`html`标记语法
* 模板能根据字典内容动态生成`html`页面

#### 2.2.2 模板的配置

1. 创建模板文件夹`mysite/templates`
2. 在`settings.py`配置文件中修改`TEMPLATES`配置项，增加模板目录
3. 在视图函数`views`中使用`render()`或`loader.get_template()`方法调用模板,传递字典,响应请求
3. 每个应用应可以创建自己的templates目录，名字必须是`templates`

```python
# mysite/mysite/settings.py
TEMPLATES = [
    {
        # 模板引擎,默认DjangoTemplates
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        # 要修改的项,添加用户模板路径
        'DIRS': [BASE_DIR / "templates"],	
        # 是否索引各app里的templates目录
        'APP_DIRS': True,												
        ...
    },
]
```

#### 2.2.3 模板的加载

* 通过`render()`方法直接加载

```python
# mysite/mysite/views.py
from django.shortcuts import render
def 视图函数名(request, [其它参数]):
    [逻辑处理]
    return render(request, "模板文件全名", 字典)
```

* 通过`loader.get_template()`方法加载

```python
# mysite/mysite/views.py
from django.template import loader
def 视图函数名(request, [其它参数]):
    [逻辑处理]
    t = loader.get_template("模板文件名")
    html = t.render(字典数据)
    return HttpResponse(html)
```

### 2.3 模板变量

#### 2.3.1 传参方式

* 模板通过字典的方式进行传参,方法是`render()`,用两对花括号接收变量`{{ 变量 }}`
* 键对应模板文件中的`django模板`变量,值对应模板变量的值
* 传递函数时,模板的变量值是函数的返回值
* 传递对象时,模板的变量值是对象的`__str__`方法的返回值

#### 2.3.2 语法规则

* 空格严格定义
* 调用方法用属性方式调用，因为除标记符号外，内容不能包含`"()"/"[]"/"{}"`
* 当对象属性名和对象方法名同名时，使用对象调用变量名优先级：属性>方法
* `{% 语句 %}` 中不能含有运算符，加法或减法使用模板过滤器

| 语法               | 含义                   | 举例                |
| ------------------ | ---------------------- | ------------------- |
| `{{ 变量名 }}`     | 获取变量值             | `{{ uname }}`       |
| `{{ 序列.index }}` | 获取序列对应索引的元素 | `{{ cities.0 }}`    |
| `{{ 字典.key }}`   | 获取字典的值           | `{{ dicts.title }}` |
| `{{ 对象.属性 }}`  | 获取对象的属性值       | `{{ qtx.age }}`     |
| `{{ 函数名 }}`     | 获取函数的返回值       | `{{ func }}`        |
| `{{ 对象.方法 }}`  | 获取对象方法的返回值   | `{{ qtx.teach }}`   |

```python
# 样例 一个计算器 按下提交键获取结果 同时原来数据会被保存
# mysite/mysite/views.py
def calculate_view(request: HttpRequest, ):
    def get():
        return render(request, 
                      "calculator.html", 
                      context={"x": 1.0, "y": 1.0, "r": 2.0, "add": "selected"})
    def post():
        x, y, op = float(request.POST.get("x")), float(request.POST.get("y")), request.POST.get("op")
        if op == "add": r = x + y
        elif op == "sub": r = x - y
        elif op == "mul": r = x * y
        else: r = x / y
        return render(request, "calculator.html", context={"x": x, "y": y, op: "selected", "r": r})

    def notFoundMethod():
        return HttpResponse("method不存在")

    method = request.method
    methodFunctions = {"GET": get, "POST": post, }
    return methodFunctions.get(method, notFoundMethod)()
```

```python
# mysite/templates/calculator.html
...
<form action='/calculate/' method='POST'>	{# 表示POST到http://127.0.0.1:8000/calculate/ #}
    <input type='text' name="x" value="{{x}}">
    <select name='op' value="op">
        <option value="add" {{add}}> +加 </option>
        <option value="sub" {{sub}}> -减 </option>
        <option value="mul" {{mul}}> *乘 </option>
        <option value="div" {{div}}> /除 </option>
    </select>
    <input type='text' name="y" value="{{y}}"> = <span>{{r}}</span>
    <div>
        <input type="submit" value='开始计算'>
    </div>
...
```

### 2.4 模板标签

#### 2.4.1 注释标签

```python
# mysite/templates/index.html
...
{# 这是模板注释,浏览器无法获取该内容 #}
...
```

#### 2.4.2 选择标签

* 开始标签和结束标签成对写,防止忘记
* `if`标记里严格控制空格
* `if`标记里不能包含括号,应使用`if`嵌套曲线救国
* 语句可以写在任何地方,前提是逻辑OK

```python
{% if 条件表达式1 %}
...
{% elif 条件表达式2 %}
...
{% elif 条件表达式3 %}
...
{% else %}
...
{% endif %}
```

#### 2.4.3 循环标签

* 开始标签和结束标签成对写,防止忘记
* `forloop`全局变量
* `empty`表示当`for`循环没有值时的占位标签

```python
{% for 变量 in 可迭代对象 %}
    ... 循环语句
{% empty %}
    ... 可迭代对象无数据时填充的语句
{% endfor %}
```

* 内置变量 `forloop`（使用`{% forloop.属性 %}`调用）

| 变量 | 描述 |
|:-:|:--|
| `forloop.counter` | 循环的当前迭代（从1开始索引） |
| `forloop.counter0` | 循环的当前迭代（从0开始索引） |
| `forloop.revcounter` | counter值的倒序 |
| `forloop.revcounter0` | counter0值的倒序 |
| `forloop.first` | 如果这是第一次通过循环，则为真 |
| `forloop.last` | 如果这是最后一次循环，则为真 |
| `forloop.parentloop` | 当嵌套循环，parentloop 表示外层循环 |

### 2.5 模板过滤器

* 作用:  在模板变量输出时对变量的值进行处理
* 语法:  `{{ 变量名|过滤器[:参数] }}`
* 嵌套:  可嵌套`{{ 变量名|过滤器1|过滤器2... }}`
* 常用过滤器:

| 过滤器 | 说明 |
|:-:|:--|
| `lower` | 将字符串转换为全部小写。|
| `upper` | 将字符串转换为大写形式 |
| `safe` | 默认不对变量内的字符串进行html转义,safe是危险操作 |
| `add: "n"` | 将value的值增加 n，n可为正整数 负整数和 0 |
| `truncatechars:'n'` | 如果字符串字符多于指定的字符数量，那么会被截断。 |

### 2.6 模板的继承

* 父类中识别出哪些内容在子模块中允许修改，将允许子模块修改的内容打上`block`标记
* 子模块写上继承语句后拥有父模块全部内容，不想继承的部分,重写`block`标记内容
* 模板继承时，服务器端的动态内容无法继承
* 子模板特有的内容也必须写在`block`标记块内
*  父模块路径从`templates`后开始写

```python
# 父模块
{% block "块名" %}
...定义模板块,此模板块可以被子模板重新定义的同名块覆盖
{% endblock "块名" %}

# 子模块
{% extends "[应用名/]父模块名.html" %}	# 此处父模块路径从templates开始
{% block "父模块的块名" %}
...子模板块用来覆盖父模板中 父模块的块名 的内容
...子模块额外的内容 也必须写在 块里
{% endblock "父模块的块名" %}
```

## 三、URL反向解析

### 3.1 反向解析概念

* 定义:  url 反向解析是指在视图或模板中，用path定义的别名来查找或计算出相应的路由
* 作用:  使用反向路由解析，当路由地址修改时，只需要配置路由，不用配置已存在的超链接
* 手段:  模块里反向解析、视图函数里反向解析
* 应用:  网页会存在诸多超链接,超链接的路由可能会改变，如果配置了反向解析，不管路由怎么变，超链接仍生效
* 注意:  `urlpatterns`中反向解析必须使用关键字`name`传参

### 3.2 正向解析弊端

假设网页`/A/`页面包含有`/B/`页面的超链接,格式如下

```python
# mysite/mysite/ulrs.py
urlpatterns = [
    path('A/', views.a_view),
    path('B/', views.b_view),
]
# /A/页面的模板
<a href="/B/">不使用反向路由解析,点击跳转到/B/页面</a>
```

当`/B/`页面路由改变时,假设变成了`/BB/`

```python
# mysite/mysite/ulrs.py
urlpatterns = [
    path('A/', views.a_view),
    path('BB/', views.b_view),
]
# /A/页面的模板
<a href="/B/">不使用反向路由解析,点击可能跳转到/B/页面</a>
```

此时`/A/` 中原来`/B/`的超链接也要跟着修改,否则无法找到原来的`/B/`页面

通常超链接都很多,此时工程量太大

### 3.2 模板反向解析

* 为路由添加反向解析别名，反向别名一定要用`name`关键字传参!!
* 在模板或视图函数中改成反向解析方法
* 因为超链接使用反向解析，用户点击超链接后仍能跳转到`http://127.0.0.1:8000/BB/`

```python
# mysite/mysite/urls.py
urlpatterns = [
    path('A/', views.a_view),
    path("BB/", 					# 路由地址,路由修改时,指的就是修改这个
         views.calculate_view,
         name="B")					# 视图函数 反向解析name,一次定义,终生受用	  
]
# /A/页面的模板
<a href="{% url 'B' %}">使用反向路由解析,点击还能跳转到/B/页面</a>
```

### 3.3 视图反向解析

1. 获取解析地址

2. 使用方向解析地址重定向

```python
# mysite/mysite/views.py
...
fixed_url = reverse('反向解析name')
return HttpResponseRedirect(fixed_url)
...
```

## 四、常见web攻击

### 4.1 XSS攻击

* 定义:  XSS全称是Cross Site Scripting即跨站脚本
* 原理:  将恶意HTML/JavaScript代码注入到受害用户浏览的网页上，从而达到攻击目的
* 危害：盗取用户信息，破坏网站正常运行等

#### 4.1.1 反射型XSS攻击

* 定义：发出请求时，XSS代码出现在URL中，作为输入提交到服务器端，服务器端解析后响应，XSS代码随响应内容一起传回给浏览器，最后浏览器解析执行XSS代码。这个过程像一次反射，故叫反射型XSS

```python
# 浏览器端发送敏感url
http://127.0.0.1:8000/test_xss/?t=<script>alert('完成xss攻击')</script>
# 服务端返回用户提交内容
return HttpResponse(request.GET.get('t'))
# 浏览器自动执行js代码,成为被攻击对象
```

#### 4.1.2 存储型XSS攻击

* 定义：提交的XSS代码会存储在服务器端（数据库，内存，文件系统等），其他用户请求目标页面时即被攻击

```python
样例：
    博客发表文章时，提交XSS代码，服务器存储代码后，其他用户访问该文章时，被XSS攻击
```

### 4.2 DOM XSS攻击

* 定义：DOM XSS的代码无需跟服务器交互，在前端直接触发攻击

```python
样例：
地址栏提交#内容，例如-http://127.0.0.1:8000/test_html#javascript:alert(11)

页面中添加JS:
<script>
    var hash = location.hash;
    if(hash){
        var url = hash.substring(1);
        location.href = url;
    }
</script>
```

## 五、应用

* 应用是jango项目中一个独立的模块，拥有自己的路由、视图、模板和模型
* 多个项目可以公用一个应用，多个应用服务于一个项目
* `django`采用分布式路由管理，主路由不处理具体请求，转而交给应用处理

### 5.1 配置应用

#### 5.1.1 创建应用

1. 创建应用目录

```shell
# mysite/mysite
$ python manage.py startapp 应用名
```

2. 配置文件中注册应用

```python
# mysite/mysite/settings.py
INSTALLED_APPS = [
    应用名,									# 添加这一行
]
```

3. 主项目分配应用路由

```python
# mysite/mysite/ulrs.py
from django.urls import include
urlpatterns = [
    path('应用名/', include('应用名.urls')),		# 添加这一行
]
```

4. 创建应用路由解析（手动添加）

```python
# mysite/应用名/urls.py
urlpatterns = [
    path('', views.index),
    ...
]
```
5. 编写应用视图函数
6. 创建应用模板目录（可选，也可以放在在主项目`templates`文件夹）

```shell
# mysite/mysite/settings.py
TEMPLATE = [									# 检查'APP_DIRS'是否为True
    'APP_DIRS':True,							# true表示当在mysite/mysite下找不到模板时，到应用的模板里找
]

# mysite/应用名/templates/应用名/
...此处是应用模板文件
```

7. 配置静态文件

```python
# mysite/static/应用名/
...此处是应用静态文件
```

#### 5.1.2 应用目录

```shell
$ tree mysite/应用名

├── admin.py					# 应用的后台管理配置文件 
├── apps.py						# 应用的属性配置文件
├── __init__.py					# 应用子包的初始化文件
├── migrations					# 保存数据迁移的中间文件
│   └── __init__.py
├── models.py					# 与数据库相关的模型映射类文件
├── templates					# 手动创建的应用模板文件夹，必须命名为templates
│   └── 应用名					  # 里面嵌套应用同名的目录，便于查找模板
│       └── index.html			# 存放具体模板文件
├── tests.py					# 应用的单元测试文件
├── urls.py						# 手动创建的应用路由系统
└── views.py					# 定义视图处理函数的文件
```

### 5.2 admin应用

admin应用是django集成的后台数据库管理应用，提供图形化界面，可视化CRUD数据

* 创建后台超级管理员账户

```shell
$ python manage.py createsuperuser
```

* 后台管理页面网址

```yacas
http://127.0.0.1:8000/admin
```

* 重写修改、查询方法

```python
class BaseModel(admin.ModelAdmin)
    def save_model(self, request, obj, form, change):
        super().save_model(request, obj, form, change)
        # 删除缓存等操作

    def delete_model(self, request, obj):
        super().delete_model(request, obj)
        # 删除缓存等操作
```

* 注册自定义模型

```python
@admin.register(模型名)
class BrandAdmin(BaseModel):
    list_display = [要显示的字段]
    list_display_links = [哪些字段可以直接链接到修改界面]
    list_editable = [哪些字段可以直接在页面上编辑]
    更多：https://docs.djangoproject.com/en/2.2/ref/contrib/admin/
```

## 六、模型

### 6.1 基础概念

#### 6.1.1 配置模型

1. 安装`mysqlclient`、`pymysql`
2. 终端手动创建数据库`CREATE DATABASE 库名 CHARACTER SET Uub dsTF8;`
3. 修改配置文件`DATABASES`列表
4. 在应用的`models.py`中添加模型类
5. 完成模型类的构建后，执行数据库迁移命令（2条），生成数据库文件
5. 参见：https://docs.djangoproject.com/zh-hans/4.0/topics/db/models/

```python
# mysite/mysite/settings.py 
DATABASES = {	
    	'default': {	
        # 'ENGINE': 'django.db.backends.sqlite3',
        'ENGINE': 'django.db.backends.mysql',
        # 'NAME': BASE_DIR / 'db.sqlite3',
        'NAME': 'mywebdb',							# 库名
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': '157299',
    }
}
```

#### 6.1.2 `ORM`框架

`ORM`即`Object Relational Mapping`，对象关系映射，是将数据库映射到类和对象的技术，从而避免直接操作数据库，换库不换代码

* 作用：
  1. 建立模型类和和数据表之间的对应关系，将操作数据库转为操作类和对象
  2. 通过简单的配置实现数据库的快速切换，屏蔽了不同数据库操作上的差异

* `ORM`缺点
  1. 映射上性能的损失（需要很熟悉`ORM`操作减少性能损失）
  2. 业务复杂时的使用成本较高

* `Models`类
  1. 模型是一个Python类，写在应用的`models.py`文件中，一个模型类代表数据库中的一张数据表
  2. 每个自定义模型类继承`django.db.modles`中的`Model`类，默认添加id字段
  3. 也可以自定义抽象模型类并在内部类设置`abstract=True`，定义共有的字段，让别的模型类继承
  4. 模型类中每一个类属性都代表数据库中的一个字段

* 映射关系

| 数据库 | 模型类                               |
| ------ | ------------------------------------ |
| 数据表 | 类                                   |
| 字段   | 属性                                 |
| 记录   | 对象                                 |
| 表名   | 模型类内部类`Meta`的`db_table`属性值 |

* 字段类
  1. 每个字段均是字段类的对象
  2. 字段类都继承于`Field`抽象类

```python
class Field(RegisterLookupMixin):
    def __init__(self, 
                 verbose_name=None, 		# 在后台可视化管理界面中显示的字段名
                 name=None, 
                 primary_key=False,			# 是否是主键，若添加则不会自动创建id主键
                 max_length=None, 			# 最长字符长度或最大显示长度
                 unique=False, 				# 是否是唯一索引
                 blank=False, 				# blank=True仅仅是在表单验证的时候可以为空,必须填入值
                 null=False,				# 对应数据库是否可以为null，结果有两种：null和'',一般配合default=''
                 db_index=False,			# 是否创建普通索引
                 ...
```

| 字段类          | 数据库字段     | 备注                                                         |
| --------------- | -------------- | ------------------------------------------------------------ |
| `BooleanField`  | `tinyint(1)`   | 框架中使用`True`和`False`，数据库中使用1和0表示              |
| `CharField`     | `varchar`      | 必须指定`max_length`属性                                     |
| `DateField`     | `date`         | `auto_now`表示保存时间，`auto_now_add`表示创建时间，`default`设置当前时间，三选一 |
| `DateTimeField` | `date(6)`      | 表示日期和时间用法同`DateField`                              |
| `DecimalField`  | `decimal(m,n)` | `max_digits`表示`m`, `decimal_places`表示`n`                 |
| `FloatField`    | `double`       | /                                                            |
| `EmailField`    | `varchar(255)` | 必须是邮箱格式，否则不允许保存                               |
| `IntegerField`  | `int`          | /                                                            |
| `ImageField`    | `varchar(100)` | 作用是保存文件的路径，注意区分                               |
| `TextField`     | `longtext`     | /                                                            |
| ...             | ...            | ...                                                          |

* 表间关系映射

| 关系种类 | 关系类            | 数据库                       |
| -------- | ----------------- | ---------------------------- |
| 一对一   | `OneToOneField`   | 主键对主键添加外键           |
| 一对多   | `ForeignKey`      | 字段对主键添加外键           |
| 多对多   | `ManyToManyField` | 创建第三张表，存储多对多关系 |

*  `Meta`类
  1. `Meta`类是每个自定义模型类的内部类，写在模型类内部，负责修改表格特殊属性，如表名、抽象与否、排序、权限、约束等
  2. 参见：https://docs.djangoproject.com/zh-hans/4.0/ref/models/options/

```python
class Book(models.Model):
    ...
    class Meta:
        db_table = book		# 声明对应数据库的表名是book而不是默认名
        ordering = ['name', '-price']	# 根据名字升序，名字相同根据价格降序排列，很消耗成本，每次存储都会重新组织数据
        abstract = True		# 声明是抽象模型类，不会生成数据库实体，被其它模型类继承，作用是提供共有字段
        constrants = [CheckConstranstraint(check=Q(price__lte=999), name="price_lte_999")]	# 约束条件
```

*  注意事项
  1. 每个模型类框架自动分配一个`id`字段，无需手动添加，若某个字段指定`primary_kye=True`则不会创建`id`主键，因为主键唯一
  2. `null=True`常和`default=''`搭配，而`blank=True`仅在验证表单信息时使用，写完`null`的组合拳，此时`blank=False`恒成立
  3. `unique=True`表示为该字段创建唯一索引，`db_index=True`表示创建普通索引
  4. 使用框架创建的数据库，禁止使用数据库直接修改表结构，因为`ORM`框架依赖于正确的迁移文件
  5. 一般情况重要数据使用伪删除技术，即添加一个布尔值字段如`isActive`，代表是否删除
  6. 存在一对多关系时，先建立一对多关系，并将从表放在下面
  7. 多对多关系不用`on_delete=CASCADE`条件，因为关系复杂，迁一发而动全身

*  迁移异常
  1. 删除迁移文件
  2. 删库 再建库
  3. 重新迁移
* 抽象模型类
  1. 抽象模型类作用是提供共有的字段
  2. 抽象的模型类在不会应用到数据库中

```python
# mysite/mysite_utils/base_model.py
from django.db.models import Model, DateTimeField

class MyAbstractModel(Model):
    """
        自定义抽象模型类，不会被makemigrations和migrate操作
        作用：允许添加字段被其它自定义的模型继承（添加公共字段）
    """
    create_time = DateTimeField("创建日期", auto_now_add=True)
    update_time = DateTimeField("修改日期", auto_now=True)

    class Meta:
        abstract = True     # 该变量允许将该模型作为抽象模型 => 关键参数
```

```python
# mysite/应用/models.py
from mysite_utils.base_model import MyAbstractModel
class Book(MyAbstractModel):	# 自动拥有了create_time和update_time字段了
    ...
```

*  Django models中关于blank与null的补充说明

```python
class Person(models.Model):
    GENDER_CHOICES=(
        (1,'Male'),
        (2,'Female'),
        )
    name=models.CharField(max_length=30,unique=True,verbose_name='姓 名')   
    birthday=models.DateField(blank=True,null=True)
    gender=models.IntegerField(choices=GENDER_CHOICES)
    account=models.IntegerField(default=0)　
```

```
blank: 设置为True时，字段可以为空。设置为False时，字段是必须填写的。字
	   符型字段CharField和TextField是用空字符串来存储空值的
	   
null:  设置为True时，django用Null来存储空值。日期型、时间型和数字型字段不接受空字符串。所以设置IntegerField，			        DateTimeField型字段可以为空时，需要将blank，null均设为True

如果想设置BooleanField为空时可以选用NullBooleanField型字段
blank 是针对表单的，如果 blank=True，表示你的表单填写该字段的时候可以不填
```



### 6.2 建表实例

1. 创建`bookStore`应用，配置路由、`mysql`数据库
2. 配置`book`表、`publisher`表、`author`表以及`wife`表，并建立合适的字段与关联关系

```shell
# 1. 创建项目目录booksite
$ django-admin startproject booksite
```
```shell
# 2. 创建bookstore应用
$ python manage.py startapp bookstore
```
```shell
# 3. 手动创建数据库
$ mysql -u root -p
mysql> CREATE DATABASE book_database CHARACTER SET UTF8;
```
```python
# 3. 修改配置文件bookstore/booktore/settings.py
INSTALLED_APPS = [
	...,
	'bookstore',
	]
DATABASES = {
    'default': {
        # 'ENGINE': 'django.db.backends.sqlite3',
        # 'NAME': BASE_DIR / 'db.sqlite3',
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'book_database',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        'USER': 'root',
        'PASSWORD': '157299',
    }
}
LANGUAGE_CODE = 'zh-Hans'
TIME_ZONE = 'Asia/Shanghai'
```
```python
# 4. 配置路由 booksite/booksite/urls.py
urlpatterns = [
    path('admin/', admin.site.urls),
    path('bookstore/', include('bookstore.urls')),
]
# 在应用中创建urls.py文件并继续配置路由 booksite/bookstore/urls.py
urlpatterns = [
    path("", views.index),
]
# 创建视图解析函数检查基础连接功能 booksite/bookstore/views.py
def index(request: HttpRequest) -> HttpResponse:
    """ http://127.0.0.1:8000/bookstore/
    """
    return HttpResponse('OK')
```
```python
# 5. 确立关系和字段
"""
1. 确定关系
book        多对一        publisher-----OK
book        多对多        author--------OK
publisher   多对多        author--------OK
author      一对一        wife----------OK

2. 确定字段及约束
...
"""
```
```python
# 6. 构建模型类
from django.db.models import *
class Publisher(Model):
    """ 出版社表
    """
    name = CharField('出版社', max_length=50, unique=True, )
    create_time = DateTimeField('创建日期', auto_now_add=True)
    update_time = DateTimeField('修改日期', auto_now=True)

    class Meta:
        """ 内部类
        """
        db_table = 'publisher'

class Book(Model):
    """ 图书表
    """
    name = CharField('图书', max_length=100, db_index=True, default='')
    price = DecimalField('价格', max_digits=6, decimal_places=2, default=0.0)
    isActive = BooleanField('已上架', default=1)
    publisher = ForeignKey(Publisher, on_delete=CASCADE)

    class Meta:
        """ 内部类
        """
        db_table = 'book'

class Author(Model):
    """ 作者类
    """
    name = CharField('作者', max_length=30, default='')
    email = EmailField('邮箱', default='')
    age = PositiveSmallIntegerField('年龄', default=0)
    publisher = ManyToManyField(Publisher)
    book = ManyToManyField(Book)

    class Meta:
        """ 内部类
        """
        db_table = 'author'

class Wife(Model):
    """ 作者妻子类
    """
    name = CharField('老婆', max_length=30, default='')
    author = OneToOneField(Author, on_delete=CASCADE)

    class Meta:
        """ 内部类
        """
        db_table = 'wife'
```

```shell
# 7. 迁移数据库
$ python manage.py makemigrations
$ python manage.py migrate
```

### 6.3 模型`CRUD`

#### 6.3.1 事务

```python
# 开启事务
with transaction.atomic():
    # 创建存储点
    sid = transaction.savepoint()
    try:
    	# ...执行操作
    except Exceptions as e:
        print(f"xx事务执行失败，已回滚，e=")
        transaction.saveponint_rollback(sid)
        return JsonResponse({"code": 10001, "error": "执行xx事务失败"})
    transaction.savepoint_commit(sid)
    return JsonResponse({"code": 200, "data": "执行xx事务成功"})
```

#### 6.3.2 Create

* 正向创建实例

```python
# 1. 使用ModelManager.create(变量=值)
Book.objects.create(book_name='《三毛流浪记》', price=19.9)

# 2. 使用Model(变量=值).save()
Book(book_name='《三毛流浪记》', price=19.9).save()
```

* 反向创建实例

```python
# RelatedManager.create(变量=值) => 适用于一对多、多对多的反向关系，不用save()
p = Publisher.objects.get(id=1)
b = p.book_set.create(name="书名"...)
```

```python
# 多对多关系时 反向创建实例
# 方案1 先创建 author 再关联 book
    author1 = Author.objects.create(name='吕老师')
    author2 = Author.objects.create(name='王老师')
    # 吕老师和王老师同时写了一本Python
    book11 = author1.book_set.create(title="Python")
    author2.book_set.add(book11) 
    
# 方案2 先创建 book 再关联 author
    book = Book.objects.create(title='python1')
    #郭小闹和吕老师都参与了 python1 的 创作
    author3 = book.authors.create(name='guoxiaonao')
    book.authors.add(author1)
```

```python
# create(**kwargs)函数
创建一个新的对象，将其保存并放入关联对象集。返回新创建的对象
```

#### 6.3.3 Read

* 查询功能全部是建立在`模型.objects`的模型管理对象（下称为ModelManager）之上的操作
* 惰性性：如果不是获取结果，不会执行查询命令
* 灵活性：`QuerySet`本身可以被构造、过滤、切片（不支持负索引）、复制、迭代、复用
* 链式性：ModelManager对象和结果集（`QuerySet`）允许链式调用
* 参见：https://docs.djangoproject.com/zh-hans/4.0/topics/db/queries/

##### 6.3.3.1 简单查询

| ModelManager方法                 | 返回数据类型 | 说明                                                         |
| -------------------------------- | ------------ | ------------------------------------------------------------ |
| `all()`                          | `QuerySet`   | 获取所有数据集合，查询不到返回空的结果集，下同               |
| `get(条件)`                      | `模型实例`   | 如果正好查到一个结果，返回模型对象，否则抛出异常             |
| `filter(条件)`                   | `QuerySet`   | 获取符合条件的结果集，包括所有字段                           |
| `exclude(条件)`                  | `QuerySet`   | 获取不符合条件的结果集，包括所有字段                         |
| `values("字段",...)`             | `QuerySet`   | 获取指定列的结果集，数据被字典封装，字典被`QuerySet`封装     |
| `values_list("字段",...)`        | `QuerySet`   | 获取指定列的结果集，数据被元祖封装，元祖被`QuerySet`封装     |
| `order_by("字段",...)`           | `QuerySet`   | 获取字段升序排列的结果集，`ModelManager.order_by("-字段")`表示降序 |
| `aggregate([key], 函数("字段"))` | `dict`       | 聚合查询                                                     |
| `annotate([key], 函数("字段"))`  | `dict`       | 聚合分组                                                     |
| `count()`                        | `int`        | 结果集条数                                                   |
| `first()`                        | `模型实例`   | 第一条结果                                                   |
| `last()`                         | `模型实例`   | 最后一条结果                                                 |

| 查询条件表达式（写在查询条件中） | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| `字段__exact="值"`               | 等值匹配，等价于`字段="值"`，值可以是模型实例（为外键时使用） |
| `字段__iexact="值"`              | 不区分大小写匹配                                             |
| `字段__contains="值"`            | 包含指定值                                                   |
| `字段__startswith="值"`          | 以值开始                                                     |
| `字段__endswith="值"`            | 以值结束                                                     |
| `字段__gt="值"`                  | 大于值                                                       |
| `字段__gte="值"`                 | 大于等于值                                                   |
| `字段__lt="值"`                  | 小于值                                                       |
| `字段__lte="值"`                 | 小于等于值                                                   |
| `字段__in=["值", ...]`           | 在指定列表元素范围内                                         |
| `字段__range=(开始值，结束值)`   | 指定数学闭区间内                                             |
| `模型名__字段="值"`              | 等价于`字段="值"`                                            |

##### 6.3.3.2 反向查询

* 当表与表之间是一对多或多对多关系时，可选`RelatedManager`反向查询
* 参见：https://docs.djangoproject.com/zh-hans/4.0/topics/db/aggregation/#following-relationships-backwards

```python
# 查询id为1的出版社出版的所有图书
Pulisher.objects.get(id=1).book_set.all()
```

```python
# 查询每个出版社各出版多少本图书（查询结果里的每一个 Publisher 会有多余的属性： book__count）
Publisher.objects.annotate(Count('book'))
```

```python
# 通过出版社表反向找出最古老的一本
Publisher.objects.aggregate(oldest_pubdate=Min('book__pubdate'))
```

```python
# 通过作者表查询所有书的平均价格
Author.objects.aggregate(average_price=Avg('book__price'))
```

##### 6.3.3.3 聚合查询

* 聚合函数也来自于`from django.db.models import Avg, Sum, Max, Min, Count`
* 参见：https://docs.djangoproject.com/zh-hans/4.0/topics/db/aggregation/

```python
# Avg()
d = Book.objects.aggregate(my_avg=Avg("price"))		# 结果是dict，下同
print(f"图书的平均价格是{d.get('my_avg')}")
```

```python
# Sum()
d = Book.objects.aggregate(Sum("price"))
print(f"图书价格总数是{d.get('price__sum')}")
```

```python
# Max()
d = Book.objects.aggregate(my_max=Max("price"))
print(f"图书的最高价格是{d.get('my_max')}")
```

```python
# Min()
d = Book.objects.aggregate(Min("price"))
print(f"图书的最低价格是{d.get('price__min')}")
```

```python
# 联合运算
d = Book.objcts.aggregate(price_diff = Max("price") - Min("price"))
d.get("price_diff")		# xxx
```

```python
# 联合使用
d = Book.objcts.aggregate(Max("price"), Min("price"), Avg("price"))
d	# {"price__max": 99, "price__min": 10, "price__avg": 49}
```

```python
# Count
Q = Book.objects.annotate(book_count = Count("publisher"))	# 结果是QuerySet
Q # <QuerySet: publisehr: 出版社名, book_count: 总出版图书数量 >
Q[0].book_count	# 63
```

##### 6.3.3.4 F对象

* 定义：F对象表示直接操作数据库，并表示某条记录字段的信息
* 作用：不获取字段值情况下操作数据库数据、避免竞争条件
* 示例：

```python
# 将Book表中所有书涨价1元
Book.objects.update(price=F("price")+1)
```

```python
# 列出Book表中记录创建日期和修改日期相同的数据
Book.objects.filter(create_time__exact=F("update_time"))
```

##### 6.3.3.5 Q对象

* 定义：Q对象代表整个表
* 作用：在复杂查询中（与或非）查询结果
* 语法：

```python
from django.db.models import Q
Q(条件1)|Q(条件2)  # 条件1成立或条件2成立
Q(条件1)&Q(条件2)  # 条件1和条件2同时成立
Q(条件1)&~Q(条件2)  # 条件1成立且条件2不成立
```

```python
# 想找出定价低于20元 或 清华大学出版社的全部书
Book.objects.filter(Q(price__lt=20)|Q(pub="清华大学出版社"))
```

#### 6.3.4 Update

* 单例修改

```python
try:
    book = Book.objects.get(name="《三毛流浪记》")		# 获取模型实例
except:
    print("查无此书")	# 假设name字段设置了unique约束
else:
	book.price = "9.9"	# 修改属性
	book.save()		# 保存结果
```

* 多例修改

```python
books = Book.objcts.filter(price__lte=10)	# 获取结果集
books.update(price=10)	# 批量修改
```

* 反向修改

```python
# RelatedManager.add(模型实例) => 适用于一对多、多对多的反向关系，不用save() => 将指定的模型对象加入关联对象集
a = Author.objects.get(id=1)
b = Book.objects.get(id=666)
a.book_set.add(b)	# 如果b原来没有关联a,则现在关联上了
```

```python
# RelatedManager.set(模型实例列表, clear=False) => 替换一组关联对象
# 当clear=False，不解除原来的关系
# 当clear=True, 接触原来的关系
```

#### 6.3.5 Delete

* 单例删除

```Python
try:
    book = Book.objects.get(name="《三毛流浪记》")
except:
    print("查无此书")
else:
    book.delete()
```

* 多例删除

```python
books = Book.objcts.filter(price__lte=10)	# 获取结果集
books.delete()	# 批量删除
```

* 反向删除（解除关系，不删除数据）

```python
# RelatedManager.remove(模型实例)
a = Author.objects.get(id=1)	# 假定Book是Author的从表
b = Book.objects.get(id=666)	# 当b的外键是a时，且ForeignKey不为null(默认null=False)，remove无效
a.book_set.remove(b)			# 当b的外键是a时，且ForeignKey可为null，remove <=> b.author=null + b.save()

a = Publisher.objects.get(id=1)	# 假定Publisher和Book多对多
b = Book.objects.get(id=666)
a.book_set.remove(b)			# 解除a、b关联关系

# RelatedManager.clear()
# 同remove方法，不需要参数，从关联对象集中删除所有对象
```

### 6.4 原生sql语句

* 方法一：

```python
ModelManager.raw("mysql语句")
```

* 方法二：

```python
from django.db import connection

with connection.cursor() as cur: 
	cur.execute("mysql语句"')
```

## 七、常用功能

### 7.1 `cookie`&`session`

* 设置`cookie`

```python
response.set_cookie(key,value,max_age=None,expire=None)
```

	1. key、value只能是ASCII码
	1. max_age单位为秒，表示存活时间
	1. expire单位为时间戳，max_age与expire都不写表示关闭浏览器时自动失效

* 删除cookie

```python
response.delete_cookie(key)
```

```python
response.set_cookie(key,"",max_age=0)
```

* 获取cookie

```python
request.COOKIE.get(key,default=None)
```

* 启用session

```python
INSTALLED_APPS = [
    # 启用 sessions 应用
    'django.contrib.sessions',
]
```

```python
MIDDLEWARE = [
    # 启用 Session 中间件
    'django.contrib.sessions.middleware.SessionMiddleware',
]
```

* 保存session到服务器

```python
request.session[key] = value
```

* 获取session

```python
request.session.get(key.default=None)
```

* 删除session

```python
del request.session[key]
```

* 设置session保存时长

```python
# settings.py（默认两周）
SESSION_COOKIE_AGE = 60 * 60 * 24 * 7 * 2
```

* 设置关闭浏览器时清除session

```python
# settings.py（默认为False）
SESSION_EXPIRE_AT_BROWSER_CLOSE = True
```

* 删除已过期的session

```shell
$ python manage.py clearsessions
```

### 7.2 `cache`

#### 7.2.1 服务器缓存

默认使用文件系统缓存，一般使用redis居多

* 使用`redis`缓存
  1. 添加缓存配置设置

```python
# mysite/mysite/settings.py
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379',
    }
}
```

​		2. 或者使用用户名登陆方式

```python
# mysite/mysite/settings.py
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': 'redis://username:password@127.0.0.1:6379',
    }
}
```

​		3. 或者一台服务器多个`redis`服务

```python
# mysite/mysite/settings.py
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.redis.RedisCache',
        'LOCATION': [
            'redis://127.0.0.1:6379', # leader
            'redis://127.0.0.1:6378', # read-replica 1
            'redis://127.0.0.1:6377', # read-replica 2,
        ]
    }
}
```

* 举例

```python
CACHES = {
    # 邮件缓存随机数
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
        'TIMEOUT': 300,     # 默认300s
        # 'TIMEOUT': None,    # None表示常驻内存
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            # 'PASSWORD':
        }
    },
    # 网站首页缓存
    "goods_index": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/8",
        'TIMEOUT': 300,     # 默认300s
        # 'TIMEOUT': None,    # None表示常驻内存
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            # 设置缓存压缩，45%压缩率左右（见https://django-redis-chs.readthedocs.io/zh_CN/latest/
            "COMPRESSOR": "django_redis.compressors.zlib.ZlibCompressor",
            # 'PASSWORD':
        }
    },
    # 详情页缓存
    "goods_detail": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/9",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            "COMPRESSOR": "django_redis.compressors.zlib.ZlibCompressor",
        }
    },
    # 短信验证码
    "sms": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/2",
        'TIMEOUT': 300,     # 默认300s
        # 'TIMEOUT': None,    # None表示常驻内存
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            # 'PASSWORD':
        }
    },
    # 购物车数据 使用redis的AOF持久化存储
    "carts": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/3",
        "TIMEOUT": None,  # None表示永不过期，默认300s
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
            "COMPRESSOR": "django_redis.compressors.zlib.ZlibCompressor",
        }
    },
}
```

```python
# 视图函数中使用缓存
from django.views.decorators.cache import cache_page

@cache_page(30)  -> 单位s
def my_view(request):
    ...
```

```python
# 在路由中使用
from django.views.decorators.cache import cache_page

urlpatterns = [
    path('foo/', cache_page(60)(my_view)  ),
]
```

```python
# 在模板中使用
{% load cache %}        

{% cache 500 sidebar username %}
    .. sidebar for logged in user ..
{% endcache %}
```

```python
# 局部缓存

from django.core.cache import caches
cache1 = caches['default']
cache2 = caches['myalias_2']

#默认配置引入【指的配置中的default项】 等同于 caches['default']
from django.core.cache import cache

#常规命令 set
#key: 字符串类型
#value: Python对象
#timeout：缓存存储时间  默认值为settings.py CACHES对应配置的TIMEOUT
#返回值：None
cache.set('my_key', 'myvalue', 30)

#常规命令 get
#返回值：为key的具体值，如果没有数据，则返回None
cache.get('my_key')
#可添加默认值，如果没取到返回默认值
cache.get('my_key', 'default值')

#常规命令 add 只有在key不存在的时候 才能设置成功
#返回值 True or False
cache.add('my_key', 'value') #如果my_key已经存在，则此次赋值失效

#常规命令 get_or_set 如果未获取到数据 则执行set操作
#返回值 key的值
cache.get_or_set('my_key', 'value', 10)

#常规命令 get_many(key_list) set_many(dict,timeout)
#返回值  set_many:返回插入不成功的key数组 
#       get_many:取到的key和value的字典
>>> cache.set_many({'a': 1, 'b': 2, 'c': 3})
>>> cache.get_many(['a', 'b', 'c'])
{'a': 1, 'b': 2, 'c': 3}

#常规命令 delete
#返回值  None
cache.delete('my_key')

#常规命令 delete_many
#返回值  成功删除的数据条数
cache.delete_many(['a', 'b', 'c'])
```

* 自定义缓存装饰器

```python
from django.core.cache import caches
"""
    自定义缓存装饰器
"""


def cache_check(**cache_kwargs):
    """ 因为这个装饰器本身也需要参数，所以这个装饰器有3层
    :param cache_kwargs: 将装饰器参数打包成字典，key_prefix: str/key_param: str/cache: str/expire: int
    :return: _cache_check
    """
    def _cache_check(f):
        """

        :param f: 被装饰的方法，如get/post...
        :return: wrapper
        """
        def wrapper(self, request, *args, **kwargs):
            """
            :param self: 被装饰方法的参数，如get(self, request...)，下同
            :param request:
            :param args:
            :param kwargs: path转换器参数
            :return: f(self, request, *args, **kwargs)
            """
            # 通过装饰器传参字典cache_kwargs 获取自定义的缓存库 如不指定 默认为CACHES的default库
            cache = caches[cache_kwargs.get("cache", "default")]
            # 通过装饰器传参字典cache_kwargs 组织key字符串 格式 key_prefix + key_params（前缀+键名）
            key = cache_kwargs.get("key_prefix") + str(kwargs.get(cache_kwargs.get("key_param")))
            # 如果缓存中存在键 则跳过ORM查询 直接返回键对应的值
            if cached_response := cache.get(key):
                print(f"已从redis缓存中直接获取到数据")
                return cached_response
            # 如果缓存中不存在键 则根据自定义过期时间 保存键值对 缓存到redis 方便下一次查询
            else:
                # 获取到被装饰函数（视图函数）的返回值（JsonResponse）
                value = f(self, request, *args, **kwargs)
                # 通过装饰器传参字典cache_kwargs 获取自定义缓存时间 如不指定 默认300s
                expire = cache_kwargs.get("expire", 300)
                # 使用缓存对象cache 保存键值对和过期时间
                cache.set(key, value, expire)
                return value
        return wrapper
    return _cache_check
```

* 使用自定义缓存装饰器

```python
@cache_check(key_prefix="gd", key_param="sku_id", cache="goods_detail", expire=60)
    def get(self, request, sku_id):
```

#### 7.2.2 浏览器缓存

* 强缓存：浏览器向本地缓存查询是否有缓存数据
  1. `expire:Thu, 02 Apr 2030 05:14:08 GMT`
  2. `Cache-Control:max-age=120  `

* 协商缓存：强缓存失效后，浏览器向服务器发送协商缓存，若数据仍然可用，服务器返回304
  1. 服务器响应头信息：`Last-Modified`，下一次浏览器访问时携带`If-Modified-Since`，值等于`Last-Modified`，Date格式
  2. 服务器响应头信息：`ETag`，下一次浏览器访问时携带`If-None-Match`，值等于`ETag`，字符串格式，文件唯一哈希值

* 区别：
  1. `ETag`精度更高，但是更消耗计算资源
  2. 优先度：`ETag`>`Last-Modified`

### 7.3 中间件

* 中间件执行逻辑

![](https://tvax2.sinaimg.cn/large/006bmdycgy1h01ihc2pi7j30la0r30u5.jpg)

* 示例代码

```python
from typing import Optional
from django.http import HttpRequest, HttpResponse
from django.utils.deprecation import MiddlewareMixin


""" 演示使用中间件
    
    1. 新建一个py文件，一般在应用目录下
    2. 新建一个自定义类，继承MiddlewareMixin类
    3. 类中重写 process_request 
               process_response 
               process_view 
               process_exception 
               process_template_response 方法
"""


class MyMiddleware(MiddlewareMixin):
    """ 自定义中间件类
    """

    def process_request(self, request: HttpRequest):
        """ 请求进入django程序中时，会按照中间件注册顺序，依次执行所有中间件的process_request方法
            在wsgi之后，在路由解析之前执行

            所有中间件共用一个请求对象
            返回值为None表示当前中间件中的功能执行完成，按照正常的流程继续向下执行代码（中间件的process_request）
            返回值为HttpResponse对象表示当前流程执行完毕，直接进入响应流程（如身份验证不通过），
            不再执行后续中间件的process_request，并且直接执行当前中间件的process_response流程
        """
        print(f'这是第一个中间件的process_request方法被调用')
        """self=<MyMiddleware get_response=BaseHandler._get_response>
        request=<WSGIRequest: GET '/mycache/'>"""
        # return HttpResponse('不允许访问')

    def process_view(self, request: HttpRequest, callback, callback_args, callback_kwargs):
        """ 在django程序路由匹配之后，视图函数之前执行。也会按照中间件注册顺序，依次执行所有中间件的process_view方法
            此中间件函数可以过滤匹配后的路由

            request:                    所有中间件共用request对象
            callback:                   视图函数
            callback_args:              视图函数位置参数
            callback_kwargs:            视图函数关键字参数

            return None:                表示程序可以继续向下执行，执行后面中间件的process_view或执行视图处理函数
            return HttpResponse对象:     表示不再执行后续中间件的process_view和视图函数，立即执行最后中间件的的process_response方法
        """
        print(f'这是第一个中间件的process_view方法被调用')
        """
        self = < MyMiddleware get_response = BaseHandler._get_response >
        request = < WSGIRequest: GET '/mycache/' >
        callback = < funcx7ff203375f30 >, callback_args = (), callback_kwargs = {}
        """
        # return HttpResponse('拦截')

    def process_exception(self, request, exception):
        """
            当视图函数执行过程中发生异常时，会按照中间件的注册顺序倒序执行所有中间件的process_exception
            如果所有的中间件都没有对异常进行处理，会执行django默认的异常处理程序，然会将响应结果交给
            最后一个中间件的process_response倒序执行
        :param request:
        :param exception:
        :return:
        """
        print(f"这是第一个中间件的process_exception")

    def process_response(self, request: HttpRequest, response: HttpResponse):
        """ 当视图函数执行完成、或其它中间件返回HttpResponse对象，会按照中间件的注册顺序，倒序执行所有中间件的process_response

            返回值必须是HttpResponse
        :param request:
        :param response:
        :return: 必须是HttpResponse对象
                 如果直接返回response表示不对响应做任何处理
                 如果是重新创建的HttpResponse对象
        """
        """"
        self=<MyMiddleware get_response=BaseHandler._get_response>
        request=<WSGIRequest: GET '/mycache/'>, response=<atus_code=200, "text/html; charset=utf-8">
        """
        print(f'这是第一个中间件的process_response方法被调用')
        return response
        # return HttpResponse('这是第一个中间件的process_response重写的响应，覆盖掉了process_exception的重写响应')


class MyMiddleware2(MiddlewareMixin):
    def process_request(self, request):
        print("这是第二个中间件的process_request")
        # return HttpResponse('不允许访问')

    def process_view(self, request, callback, callback_args, callback_kwargs):
        print('这是第二个中间件的process_view')
        print(f'{callback=}')
        print(f'{callback_args=}')
        print(f'{callback_kwargs=}')

    def process_exception(self, request, exception):
        """

        :param request:
        :param exception:
        :return: None => 继续上抛给上层process_exception
                 HttpResponse => 跳转倒序执行process_response
        """
        print(f"这是第二个中间件的process_exception")
        # print(exception, f"{type(exception)=}")
        if isinstance(exception, ZeroDivisionError):
            return HttpResponse('0不能作为除数')  # 因为return了HttpResponse对象，后续不再执行剩下的process_exception

    def process_response(self, request, response):
        """

        :param request:
        :param response:
        :return: 必须是HttpResponse对象
                 如果直接返回response表示不对响应做任何处理
                 如果是重新创建的HttpResponse对象,若process_exception中也返回重新创建的HttpResponse对象，则覆盖响应
        """

        print("这是第二个中间件的process_response")
        return response  # 返回值必须是HttpResponse
        # return HttpResponse('这是第二')

```

* 举例2

```Python
from django.http import HttpRequest, HttpResponse
from django.utils.deprecation import MiddlewareMixin


class ExerciseMiddleware(MiddlewareMixin):
    """ 自定义中间件，强制某个ip地址只能向/test发送5次请求
    """
    def process_request(self, request: HttpRequest):
        if request.path_info.startswith('/test/'):
            times = request.session.get('times', 0)
            if not times:
                request.session['times'] = 1
            else:
                request.session['times'] += 1
            print(f"{request.session['times']=}")
            if request.session['times'] > 5:
                return HttpResponse('超过5次访问，已禁止访问')
```

### 7.4 分页

* 创建分页对象

```python
pagenator = Pagenator(object_list, per_page)
```

* 分页对象属性

```python
pagenator.count				# 值等于len(object_list)
pagenator.num_pages			# 总页数
pagenator.page_range		# 从1开始的对象，表示当前页码数
pagenator.per_page			# 每页数据个数
```

* 分页对象方法

```python
page = pagenator.page(数字)
```

* 遍历分页对象：返回值是page对象

* page对象属性

```python
page.object_list			# 当前页面的数据
page.number					# 当前页面页码
page.pagenator				# 返回关联的pagenator分页对象
```

* page对象方法

```python
page.has_next()				# 如果有下一页返回True
page.has_previous()			# 如果有上一页返回True
page.has_other_pages()		# 如果有上一页或下一页返回True
page.next_page_number()		# 返回下一页的页码，如果下一页不存在，抛出InvalidPage异常
page.previous_page_number() # 返回上一页的页码，如果上一页不存在，抛出InvalidPage异常
page.len()					# 返回当前页面对象的个数
```

* 示例代码

```python
from django.core.paginator import Paginator
def book(request):  
    bks = Book.objects.all()
    paginator = Paginator(bks, 10)
    cur_page = request.GET.get('page', 1)  # 得到默认的当前页
    page = paginator.page(cur_page)
    return render(request, 'bookstore/book.html', locals())
```

```html
<html>
<head>
    <title>分页显示</title>
</head>
<body>
{% for b in page %}
    <div>{{ b.title }}</div>
{% endfor %}

{% if page.has_previous %}
<a href="{% url 'book' %}?page={{ page.previous_page_number }}">上一页</a>
{% else %}
上一页
{% endif %}

{% for p in paginator.page_range %}
    {% if p == page.number %}
        {{ p }}
    {% else %}
        <a href="{% url 'book' %}?page={{ p }}">{{ p }}</a>
    {% endif %}
{% endfor %}

{% if page.has_next %}
<a href="{% url 'book' %}?page={{ page.next_page_number }}">下一页</a>
{% else %}
下一页
{% endif %}
</body>
</html>
```

### 7.5 CSV文件下载

```python
import csv
from django.http import HttpResponse
from .models import Book

def make_csv_view(request):
    response = HttpResponse(
        content_type='text/csv',
        # 这里filename后面的参数必须使用双引号
        headers={'Content-Disposition': 'attachment; filename="mybook.csv"'}
    )
	all_book = Book.objects.all()
     # 因为response其实也是类文件对象fileobj，csv.writer接受一个文件对象
    writer = csv.writer(response)
    writer.writerow(['id', 'title'])
    for b in all_book:    
    	writer.writerow([b.id, b.title])

    return response
```

### 7.6 文件上传

* 用户上传的文件一般放在`mysite/media文件夹下`

```python
# file : settings.py
MEDIA_URL = '/media/'
# MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
MEDIA_ROOT = BASE_DIR / 'media'
```

* 存入数据库（存储的是文件路径，文件在media下）

```python
#test_upload/models.py
from django.db import models
  
class Content(models.Model):

    desc = models.CharField(max_length=100)
    # 表示文件放在media/myfiles下面
    myfile = models.FileField(upload_to='myfiles')

    
#test_upload/views.py
from test_upload.models import *
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def upload_view_dj(request):
    if request.method == 'GET':
        return render(request, 'test_upload.html')
    elif request.method == 'POST':
        title = request.POST['title']
        a_file = request.FILES['myfile']
        Content.objects.create(desc=title,myfile=a_file)
        return HttpResponse('----upload is ok-----')
```

*  添加浏览器访问media功能

```python
# ulrs.py(http://127.0.0.1:8000/media/xxxx)
from django.conf import settings
from django.conf.urls.static import static

urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### 7.7 用户认证

* Django带有一个用户认证系统。 它处理用户账号、组、权限以及基于cookie的用户会话。
* 内置用户模型类：`from django.contrib.auth.models import User`
* 默认user的基本属性有：

| 属性名       | 类型                                                         | 是否必选 |
| ------------ | ------------------------------------------------------------ | -------- |
| username     | 用户名                                                       | 是       |
| password     | 密码                                                         | 是       |
| email        | 邮箱                                                         | 可选     |
| first_name   | 名                                                           |          |
| last_name    | 姓                                                           |          |
| is_superuser | 是否是管理员帐号(/admin)                                     |          |
| is_staff     | 是否可以访问admin管理界面                                    |          |
| is_active    | 是否是活跃用户,默认True。一般不删除用户，而是将用户的is_active设为False。 |          |
| last_login   | 上一次的登录时间                                             |          |
| date_joined  | 用户创建的时间                                               |          |

* auth字段扩展

```python
如果需要在默认auth表上扩展新的字段，如phone
1，添加新的应用
2，定义模型类 继承 AbstractUser
3，settings.py中 指明 AUTH_USER_MODEL = '应用名.类名'

#models.py案例
from django.db import models
from django.contrib.auth.models import AbstractUser

# Create your models here.
class UserInfo(AbstractUser):
    phone = models.CharField(max_length=11, default='')
    
#settings.py添加配置
AUTH_USER_MODEL = 'user.UserInfo'

#添加用户
from user.models import UserInfo
UserInfo.objects.create_user(username='guoxiao', password='123456', phone='13488871101')
```

* 创建普通用户

```python
from django.contrib.auth.models import User
user = User.objects.create_user(username='用户名', password='密码', email='邮箱',...)
```

* 创建超级用户

```python
from django.contrib.auth.models import User
user = User.objects.create_superuser(username='用户名', password='密码', email='邮箱',...)
```

* 删除用户

```python
from django.contrib.auth.models import User
try:
    user = User.objects.get(username='用户名')
    user.is_active = False  # 记当前用户无效
    user.save()
    print("删除普通用户成功！")
except:
    print("删除普通用户失败")
```

* 修改密码

```python
from django.contrib.auth.models import User
try:
    user = User.objects.get(username='xiaonao')
    user.set_password('654321')
    user.save()
    return HttpResponse("修改密码成功！")
except:
    return HttpResponse("修改密码失败！")
```

* 验证密码

```python
from django.contrib.auth.models import User
try:
    user = User.objects.get(username='xiaonao')
    if user.check_password('654321'):  # 成功返回True,失败返回False
        return HttpResponse("密码正确")
    else:
        return HttpResponse("密码错误")
except: 
    return HttpResponse("没有此用户！")
```

* 代码示例

```python
from django.contrib.auth.decorators import login_required
from django.shortcuts import render
from django.contrib.auth import authenticate, logout, login
from django.http import HttpRequest, HttpResponse


def login_(request: HttpRequest) -> HttpResponse:
    """ 登陆视图函数 http://127.0.0.1:8000/user_app/login/
    """

    def get():
        """ get 请求时发送登陆界面
        """
        return render(request, 'login.html')

    def post():
        """ post 请求时进行逻辑判断
        """
        username, password = request.POST.get('username'), request.POST.get('password')
        if not (username and password):
            return render(request, 'login.html', {'prompt': '用户名或密码不能为空'})

        user = authenticate(request, username=username, password=password)  # 检查用户名和密码是否一致
        if not user:
            return render(request, 'login.html', {'prompt': '用户名或密码错误'})

        login(request, user)
        # Persist a user id and a backend in the request. This way a user doesn't
        # have to reauthenticate on every request. Note that data set during
        # the anonymous session is retained when the user logs in.
        return HttpResponse('登陆成功，但是登陆界面还没写')

    def default():
        """ 意料之外的请求
        """
        return HttpResponse('OK')

    return locals().get(request.method.lower(), default)()


def logout_(request: HttpRequest) -> HttpResponse:
    """ 退出登陆 http://127.0.0.1:8000/user_app/logout/
    """

    logout(request)
    # Remove the authenticated user's ID from the request and flush their session data.
    return HttpResponse('退出登陆成功')

def test_login(request: HttpRequest) -> HttpResponse:
    """ 如果用户已经调用过login并没有调用logout，request.user.__str__为用户名
        如果用户调用过logout，request.user.__str__为AnonymousUser
    """
    return HttpResponse(str(request.user))

@login_required
def after_login_do(request: HttpRequest, ) -> HttpResponse:
    """ 被@login_required装饰过，里面的函数可以假设用户已经登陆过了
        若没有登陆，则用户会被重定向到登陆页面，在settings.py的LOGIN_URL='/user_app/login/'所配置，
        如： http://127.0.0.1:8000/user_app/login/?next=/user_app/test_login_decorator/
    """
    return HttpResponse('你已经登陆了，所以能看到此页面')
```

### 7.8 发送邮件

* 发送自定义内容邮件

```python
# settings.py
# 发送邮件设置
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend' # 固定写法
EMAIL_HOST = 'smtp.qq.com' # 腾讯QQ邮箱 SMTP 服务器地址
EMAIL_PORT = 25  # SMTP服务的端口号
EMAIL_HOST_USER = 'xxxx@qq.com'  # 发送邮件的QQ邮箱
EMAIL_HOST_PASSWORD = '******'  # 在QQ邮箱->设置->帐户->“POP3/IMAP......服务” 里得到的在第三方登录QQ邮箱授权码
EMAIL_USE_TLS = True  # 与SMTP服务器通信时，是否启动TLS链接(安全链接)默认false
```

```python
# views.py
from django.core import mail
mail.send_mail(
            subject,  #题目
            message,  # 消息内容
            from_email,  # 发送者[当前配置邮箱]
            recipient_list=['xxx@qq.com'],  # 接收者邮件列表
            )
```

* 自动发送服务错误报告

```python
# settings.py
DEBUG = False
ALLOWED_HOSTS = 非空列表

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.qq.com'
EMAIL_PORT = 25
EMAIL_HOST_USER = 'qq帐号@qq.com'
EMAIL_HOST_PASSWORD = 'qq帐号对应smtp授权码'
EMAIL_USE_TLS = True

SERVER_EMAIL = 'qq帐号@qq.com'
ADMINS = [('Tom', 'Tom的邮箱帐号'), ]
```

### 7.9 项目部署

* 总体流程

  1. 在服务器配置与开发相同的环境
  2. 迁移项目`sudo scp /home/tarena/django/mysite1 root@88.77.66.55:/home/root/xxx`
  3. 使用`uwsgi`代替`runserver`
  4. 配置`nginx`反向代理服务器

* `uWSGI`网关

  `WSGI`(Web Server Gateway Interface)的一种，即Web服务网关接口，是`Python`程序和`Web`服务器之间的桥梁

* 安装`uWSGI`

```shell
pip install uwsgi
```

* 添加配置文件

```ini
# mysite/mysite/uwsgi.ini
[uwsgi]
# 套接字方式的 IP地址:端口号
# socket=127.0.0.1:8000
# Http通信方式的 IP地址:端口号
http=127.0.0.1:8000
# 项目当前工作目录
chdir=/home/tarena/.../my_project 这里需要换为项目文件夹的绝对路径
# 项目中wsgi.py文件的目录，相对于当前工作目录
wsgi-file=my_project/wsgi.py
# 进程个数
process=4
# 每个进程的线程个数
threads=2
# 服务的pid记录文件
pidfile=uwsgi.pid
# 服务的目志文件位置
daemonize=uwsgi.log
# 开启主进程管理模式
master=true
```

* 修改项目配置文件

```python
# settings.py
DEBUG = False

ALLOW_HOSTS = ["网站域名或服务监听的ip地址"]
```

* 启动`uwsgi`

```shell
# mysite/mysite
$ sudo uwsgi --ini uwsgi.ini
```

* 停止`uwsgi`

```shell
$ sudo uwsgi --stop uwsgi.pid
```

* 安装`nginx`

```shell
sudo pacman -S nginx
```

* 修改`nginx`配置

```shell
# /etc/nginx/nginx.conf
# 在server节点下添加新的location项，指向uwsgi的ip与端口。
server {
    ...
    location / {
        uwsgi_pass 127.0.0.1:8000;  # 重定向到127.0.0.1的8000端口
        include /etc/nginx/uwsgi_params; # 将所有的参数转到uwsgi下
    }
    ...
}
```

* 开启或停止`nginx`服务

```shell
$ sudo systemctl start|enable|stop nginx
```

* 修改`uWSGI`配置

```ini
[uwsgi]
# 去掉如下
# http=127.0.0.1:8000
# 改为
socket=127.0.0.1:8000
# 以提高速度 最后重启uWSGI服务
```

* `nginx`配置静态文件

```python
# setting.py（注意 此配置路径为 存放所有正式环境中需要的静态文件）
STATIC_ROOT = '/home/tarena/项目名_static/static  
```

* 转移静态文件

```shell
$ python manage.py collectstatic
```

* 修改`niginx`配置

```shell
# 新添加location /static 路由配置，重定向到指定的 第一步创建的路径即可
server {
    ...
    location /static {
        # root 第一步创建文件夹的绝对路径,如:
         root /home/tarena/项目名_static;          
    }
    ...      
}
```

* 添加`404`页面

```python
from django.http import Http404
def xxx_view( ):
    raise Http404  # 直接返回404（需要在模板中提前创建404.html）
```
