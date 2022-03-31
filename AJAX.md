<h1 align="center">AJAX</h1>

# 1. 简介

## 1.1 什么是`AJAX`

异步的`JS`和`XML` 的缩写，即通过异步`JS`向服务器发送请求并接受响应数据，一系列技术的集合

`Asynchronous` `Javascript` `And` `Xml`

`XML`：`Extensible Markup Language`

* 优点：无需刷新页面即可获取数据、允许根据用户事件更新页面部分内容
* 缺点：没有浏览历史，不能回退、存在跨域问题（手动解决）、`SEO`不友好（Search Engine Optimization）

## 1.2 同步和异步

* 同步：当客户端向浏览器发送请求，服务在处理过程中，浏览器只能等待（网站图标转圈圈），效率低，体验差
* 异步：当客户端向浏览器发送请求，服务在处理过程中，浏览器可以做其它事情，不影响交互
* 异步优点：异步访问和局部刷新（无刷更新）
* 使用场合：无刷更新的场合（95%），场景如所搜建议、表单验证、前后端分离

# 2. `XML`

* `Extensible Markup Language`的缩写，叫做可扩展标记语言，**XML被设计用来传输和存储数据**，`HTML`设计用来显示数据

* `XML`没有预定义标签，用途广泛，出现很早

* 现在被JSON替代

```
<student>
	<name>wd</name>
	<age>20</age>
</student>
```

# 3. `JSON`

## 3.1 `JSON`介绍

`JavaScript Object Notation`的缩写，一种轻量级的数据交换格式，易于交换与书写

## 3.2 `JSON`使用

### 3.2.1 `JSON`对象

```js
{"键"：值...}
```

* 键必须用双引号
* 值如果是字符串，也必须用双引号

### 3.2.2 `JSON`数组

```
[对象...]
```

* 对象可以是`JSON`对象或字符串

### 3.2.3 后台处理

> 1. 从数据库或其它地方获取数据，允许数据类型为元祖、列表或字典
> 2. 将数据转换成`JSON`样式的字符串
> 3. 响应`JOSN`样式的字符串

* 内置环境

```python
import json
json_str = json.dumps(元祖|列表|字典)
```

* `django`环境

```python
# 使用serializers
from django.core import serializers
json_str = serializers.serialize('json', 结果集)
return HttpResponse(json_str)

# 或者 使用JsonResponse
return JsonResponse(列表|元祖|字典)
```

### 3.2.5 前端处理

* 手动处理（如果内容为非`JSON`样式则会报错）

```js
Json对象 = JSON.parse(JSON样式字符串)
```

* 或 指定期望类型后再获取结果

```
xhr.responseType = 'json';
json_obj = xhr.response
```

#  4. 异步对象

`XMLHttpResponse`是异步`JS`的核心，由`JS`提供的类，被称为**异步对象**，**代替向服务器发送异步的请求并处理响应**

## 4.1 对象创建

根据不同浏览器，使用不同的创建方法

* `IE7+,Chrome,Firefox,Safari,Opera)  -> 调用 XMLHttpRequest 生成 xhr对象`

* `IE低版本浏览器中(IE6以及以下) -> 调用 ActiveXObject() 生成xhr`

```js
<script>
    if(windows.XMLHttpResponse){
        var xhr = new XMLHttpResponse();
    }else{
        var xhr = new ActiveXObject("Microsoft.XMLHTTP");
    }
</script>
```

## 4.2 对象成员

| 方法/属性                                         | 作用                                                    |
| ------------------------------------------------- | ------------------------------------------------------- |
| `xhr.open( "请求方法", "url地址", 是否异步 )`     | 创建请求                                                |
| `xhr.setRequestHeader( "请求头名"， "请求头值" )` | 设置请求头，如post请求必须添加对应请求头`Content-Type`  |
| `xhr.send( [请求体] )`                            | 发送请求，为`get`不用写内容，为`post`写请求体`body`内容 |
| `xhr.readyState`                                  | 异步状态，结果是0～4                                    |
| `xhr.respons e`                                   | 响应结果                                                |
| `xhr.responseText`                                | 响应数据                                                |
| `xhr.responseType`                                | 期望返回类型                                            |
| `xhr.status`                                      | 响应状态码`2xx 4xx 5xx`                                 |
| `xhr.onreadystatechange(){}`                      | 监听异步状态值的改变，一个回调函数                      |
| `xhr.statusText`                                  | 状态字符串，如`OK`                                      |
| `xhr.getAllResponseHeaders()`                     | 所有响应头                                              |

## 4.3 状态标记

异步状态标记即`xhr.readyState`的值，用来判断当前异步`JS`的过程状态

| 状态 | 说明                                           |
| :--: | :--------------------------------------------- |
|  0   | 代理`(xhr)`被创建，但尚未调用 open() 方法。    |
|  1   | `open()` 方法已经被调用。                      |
|  2   | `send()` 方法已经被调用，响应头也已经被接收    |
|  3   | 下载中； `responseText` 属性已经包含部分数据。 |
|  4   | 下载操作已完成                                 |

## 4.3 异步对象原始操作

### 4.3.1 `GET`请求

```html
<!-- mysite/templates/test.html -->
<input type="text" name="username" id="username">
<button onclick="ajax_get()">提交ajax_get请求</button>
<span id="res"></span>

<script>
    function ajax_get(){
        var username = document.getElementById("username").value;
        var url = "/test/?username=" + username;
        var xhr = new XMLHttpRequest();
        xhr.open("get", url, true);
        xhr.send();
        xhr.onreadystatechange = function(){
            if(xhr.readyState==4){
                document.getElementById("res").innerText=xhr.responseText;
            }
        }
    }
</script>
```

```python
# mysite/mysite/views.py
def test_view(request):
    def get():
        if username := request.GET.get("username"):
            return HttpResponse(f"经过AJAX响应get的结果：你好{username}")
        return render(request, 'test.html')
    return locals().get(request.method.lower(), get)()
```

### 4.3.1 `POST`请求

```html
<!-- mysite/templates/test.html -->
<input type="text" name="username" id="username">
<button onclick="ajax_get()">提交ajax_get请求</button>
<button onclick="ajax_post()">提交ajax_post请求</button>
<span id="res"></span>

<script>
    function ajax_get(){
        var xhr = new XMLHttpRequest();
        var username = document.getElementById("username").value;
        var url = "/test/?username=" + username;
        xhr.open("get", url, true);
        xhr.send();
        xhr.onreadystatechange = function(){
            if(xhr.readyState==4){
                document.getElementById("res").innerText=xhr.responseText;
            }
        }
    }

    function ajax_post(){
        var xhr = new XMLHttpRequest();
        xhr.open("post", "/test/", true);
         "Content-Type", "application/x-www-form-urlencoded");
        var username = document.getElementById("username").value;
        var body = "username=" + username + "&csrfmiddlewaretoken={{csrf_token}}";
        xhr.send(body);
        xhr.onreadystatechange = function(){
            if(xhr.readyState==4){
                document.getElementById("res").innerText=xhr.responseText;
            }
        }
    }
</script>
```

```python
# mysite/mysite/views.py
def test_view(request):

    def get():
        if username := request.GET.get("username"):
            return HttpResponse(f"经过AJAX响应get的结果：你好{username}")
        return render(request, 'test.html')

    def post():
        username = request.POST.get("username")
        return HttpResponse(f"经过AJAX响应post的结果：你好{username}")

    return locals().get(request.method.lower(), get)()
```

## 5.4 `JQuery`异步对象操作

流程：设置监听（如按键） ->  添加按下监听函数 ->  在监听函数内使用`jqueryObject.load()`或`$.get()`或`$.post()`或`$.ajax()`

### 5.4.1 `$.load()`方法

* 主要应用场景
  * 载入`html`

* 方法介绍
  * `load`方法是`JQuery`最为简单常用的`AJAX`方法，能载入远程`HTML`代码并插入到`DOM`中，通常用于从服务器获取静态文件数据
  * `load`方法直接在已定位的`jquery`对象上加载异步资源，需要前生成`jquery`对象而并非是全局方法

* 方法结构：

```
$.load(url[, data][, callback])
```

| 参数名称           | 类型       | 说明                                                         |
| ------------------ | ---------- | :----------------------------------------------------------- |
| `url`              | `String`   | 发送异步请求的`url`地址                                      |
| `data`(可选)       | `Object`   | 发送异步请求携带的数据，若为空则发送`get`请求，否则发送`post`请求 |
| `callback()`(可选) | `Function` | 无论请求成功或失败，先执行回调函数，再加载异步资源           |

* 获取静态资源

```js
{% load static %}
$("#res").load("{% static 'html/load.html' %}");
```

* 获取同源下某个页面并携带查询字符串

```js
$("#res").load("/test/?username=laowang");
```

* 获取页面中部分内容

```js
$("#res").load("{% static 'html/load.html' %} .p1");
```

* 当`data`包含数据时(如`JSON格式`)，转向参数`url`地址采用`post`异步请求而不是`get`异步请求

```js
$("#res").load("/test/", {username: 'laowang', csrfmiddlewaretoken: "{{csrf_token}}"});
```

* 回调函数`callback`
  * 无论请求是否成功，回调函数都会被执行
  * 先执行回调函数，再加载异步资源
  * 对于传递参数，交给`$.get()、$.post()、$.ajax()`更为便捷丰富

```shell
function(responseText, textStatus, XMLHttpRequest){}
```

| 参数名称         | 类型     | 说明                             |
| ---------------- | -------- | -------------------------------- |
| `responseText`   | `String` | 异步响应数据                     |
| `textStatus`     | `String` | 异步响应结果，`success`或`error` |
| `XMLHttpRequest` | `Object` | 异步对象                         |

```js
$('#res').load('/test/', {username: 'laowang', csrfmiddlewaretoken: '{% csrf_token %}'}, 
    function(resposneText, textStatus, XMLHttpRequest){
    alert(resposneText);
    alert(textStatus);
	alert(XMLHttpRequest);
})
```

### 5.4.2 `$.get()`方法

通过`$.get()`向服务器发起`GET`异步请求，可指定返回数据样式`type`参数，当指定样式命中时才执行回调函数

`$.get()`方法属于全局方法，不需要提前生成`jquery`对象

* 结构

```js
$.get(url[, data][, callback][, type])
```

| 参数名称           | 类型              | 说明                                                         |
| ------------------ | ----------------- | ------------------------------------------------------------ |
| `url`              | `String`          | 发送异步请求的`url`地址                                      |
| `data`（可选）     | `Object`/`String` | 异步请求携带的数据，可以是字符串，可以是`js`对象             |
| `callback`（可选） | `Funcion`         | 只有异步请求成功时且`type`参数命中时才会调用回调函数         |
| `type`（可选）     | `String`          | 期待响应结果内容的样式，包括`xml`、`html`、`script`、`json`、`text`、`_default` |

* 回调函数`callback`（异步请求成功时才会调用）

```shell
function(responseTest, responseStatus){}
```

* 期待响应`html`，如果不是`html`样式数据则不执行回调函数

```js
$.get('/get/', {username: 'laowang'}, function(responseTest, responseStatus){
                alert(responseTest);
                alert(responseStatus);
            }, 'html')
```

### 5.4.3 `$.post()`方法

* 通过`$.post()`向服务器发起`POST`异步请求，可指定返回数据样式，当指定样式命中时才执行回调函数
* `$.post()`方法属于全局方法，不需要提前生成`jquery`对象
* 它与`$.get()`用法一致，区别是需要在`data`中增加`csrf`值，以及`POST`与`GET`上的区别

### 5.4.4 `$.ajax()`方法

`$.ajax()方法是jQuery最底层的Ajax实现，可以做到所有存在的异步方法`

`$.ajax()`方法不仅能实现与.load()、.get()和.post()方法同样的功能，可以给用户更多的Ajax提示信息

它的结构只有一个参数，参数以键值对形式存在，且所有参数都是可选的：

```
$.ajax(options)
```
| options参数  | 类型              | 说名                                                      |
| ------------ | ----------------- | --------------------------------------------------------- |
| `url`        | `String`          | 异步请求地址                                              |
| `type`       | `String`          | 异步请求方式，`get`、`post`...                            |
| `data`       | `String`|`Object` | 请求数据                                                  |
| `dataType`   | `String`          | 期望响应回来的数据样式，`html`、`xml`、`json`、`jsonp`... |
| `success`    | `Functioin`       | 请求成功时的回调函数，与`dataType`无关                    |
| `error`      | `Function`        | 请求失败时的回调函数                                      |
| `beforeSend` | `Function`        | 发送`ajax`请求前执行的操作，如果`return false`，终止请求  |
| `async`      | `boolean`         | 是否开启异步，默认`true`                                  |

# 5. 跨域

* 含义：非同源（相同协议、域名、端口号的网页）网页间的请求

## 5.1 jquery跨域

* 特点：
  1. 只支持`get`请求
  2. 后端需要指定响应`MIME`类型为`text/javascript`
* 原理：
  1. 前端`script`标签发送跨域的`get`请求不会被拦截
  2. 前端发送跨域请求时，携带回调函数名
  3. 后端响应`js`类型数据（格式为`callback(data)`，前端接收到后执行响应的`js`代码

```html
<!-- 定义回调函数 -->
<script>
    function myfunc(r){
        console.log(r);
    }
</script>
<!-- 浏览器加载下面这一行时 自动向src地址发送get请求 收到后端js响应后 自动执行js代码 -->
<s/cript src="http://127.0.0.1:8001?callback=myfunc"></script>
```

```python
# 视图函数
def ajax_view(request)
	callback_name = request.GET.get("callback")
    data = {"code":200}
    data = json.dumps(data)
    return HttpResponse(f'{callback_name}({data})', content_type="text/javascript")
```

* 使用`jquery`：

```js
// 前端
$(window).load(()=>{
    $.ajax({
        url:"http://127.0.0.1:8001",
        dataType:"jsonp",
        success:function(data){
            console.log(data);
        }
    })
})
```

