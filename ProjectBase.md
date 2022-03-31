 

<h1 align="center">项目基础</h1><hr>

# 一、基础技术

## 1.1 base64

### 1.1.1 原理

* 定义：为防止二进制数据在传输过程中不可见字符的丢失，而采用的编码技术（二进制转二进制）
* 编码方式：
  1. 分割：自左向右将目标字节串每次取24bit，以6bit为一单位重新分割
  2. 映射：将6bit二进制转换成十进制，根据<i>base64码表</i>，映射成ASCII码
  3. 尾数：末尾不足6bit的，补充0，若凑不到24bit，每6bit空缺补充一个b"="（最多补充2个b"="）
* 常用方法：

```python
# 字节串转base64格式
base64.b64encode(字节串, altchars=b"")		# altchars=b"-_"表示将b"+"换"成b"-"，将b"/"换成b"_"
base64.urlsafe_b64encode(字节串)			# 等价于base64.b64encode(字节串, altchars=b"-_")
```

```python
# base64转原始字节串
base64.b64decode(字节串, alterchars=b"")	# alterchars定义替换策略							  
base64.urlsafe_b64decode(字节串)			# 等价于base64.b64decode(字节串, alterchars=b"-_")
```

### 1.1.2 示例

```python
# 模拟网络上传输图片
import base64
import json


def png2json_bytes(form_url):
    """图片转可见字符字节串"""
    # 图片文件>>转成字节串>>b64编码>>转字符串>>json序列化>>转成字节串
    return json.dumps(base64.b64encode(open(form_url, "rb").read()).decode()).encode()


def json_bytes2png(data: bytes, to_url):
    """可见字符字节串转图片"""
    # 字节串>>转成字符串>>json反序列化>>b64解码
    open(to_url, "wb").write(base64.b64decode(json.loads(data.decode())))


if __name__ == '__main__':
    png_data = png2json_bytes("/home/gris/Desktop/month04/day01/pic01.png")
    json_bytes2png(png_data, "/home/gris/Desktop/month04/day01/pic01_copy.png")
```

## 1.2 hashlib

### 1.2.1 原理

哈希算法 (Hash Algorithm) 是将任意长度的数据映射为固定长度数据的算法，也称为消息摘要，常用md5或sha256加密操作

* 哈希特点：不可逆、定长、雪崩
* md5加密：存储长度固定32字符、计算速度优于sha256、安全性低于sha256，返回值是字符串

```python
# 存储用户密码
import hashlib

password_md5 = hashlib.md5(b'password').hexdigest()	 # '5f4dcc3b5aa765d61d8327deb882cf99'
```

```python
# 存储用户密码 + 加盐
import hashlib

SALT = b'%^&*(UYBIUSHADSAD7812hudhG&^TA^GDSGD*FSH*&G@HUIFH*(SDSF))'
m = hashlib.md5(SALT)
m.update(b'密码')
password_md5 = m.hexdigest()

# 等价于
password_md5 = hashlib.md5(SALT + b'password').hexdigest()
```

```python
# 校验文件md5
import hashlib

m = hashlib.md5()
with open('源文件', 'rb')  as for_read:
    while v := for_read.read(1024):
        m.update(v)
r = ("文件出错", "文件正确")[m.hexdigest()=="原始md5值"]
```

* sha256加密：储存长度固定64字符，计算速度慢于md5，安全性高于md5，返回值是字符串（方法于md5一致）
* hmac加密：将多种加密方法写成一个接口，提供使用

```python
# 生成hamc对象并获取加密结果
form hmac import HMAC
h = HMAC(b"盐值", msg=b"被加密字节串", digestmod="加密方法")	# digestmod为"md5"、"sha256"等
```

* 验证哈希结果是否一致：不可直接比较，可能被定时攻击破解

```python
import hmac

hmac.compare_digest(a,b,/)
```

* 共享初始结果hash对象：避免重复计算，比如token盐值可以提前只计算一次，以后每次调用使用之前对象的副本

```python
import hmac
from hmac import HMAC

INIT_HMAC = HMAC(b"盐值", digestmod="sha256")			# 只初始化一次以减少计算量

h1 = INIT_HMAC.copy()								  
h1.update(b"小王的token")
r1 = h1.hexdigest()					

h2 = INIT_HMAC.copy()
h2.update(b"小张的token")
r2 = h1.hexdigest()
```

## 1.3 jwt

### 1.3.1 原理

* session原理：

```
1. 用户向服务器发送用户名和密码
2. 服务器验证通过后，在当前对话（session）里面保存相关数据，比如用户角色、登录时间等等
3. 服务器向用户返回一个 session_id，写入用户的 Cookie
4. 用户随后的每一次请求，都会通过 Cookie，将 session_id 传回服务器
5. 服务器收到 session_id，找到前期保存的数据，由此得知用户的身份
```

* session的缺点：开销大、扩展差、CSRF
```
1. 每次用户认证后都要存储下来以记录连接状态，cookie中通常保存session的id，session通常放在内存中，两边都是开销
2. 用户session存放在服务器中，当使用的是分布式服务器时，还要解决session共享问题
3. 用户cookie被截获，容易造成CSRF跨站请求伪造攻击
```
* token流程：
  1. 用户完成认证后，服务器签发token
  2. 服务端将token保存到cookie或者localStorage
  3. 下一次访问时，在请求头中携带token
  4. 服务器通过密钥解密token，查看有效性

![](https://tvax2.sinaimg.cn/mw690/006bmdycgy1h02bvisgj5j311k0l8diq.jpg)

* token特点：
  1. JWT 默认是不加密，但也是可以加密的。生成原始 Token 以后，可以用密钥再加密一次
  2. JWT 不加密的情况下，不能将秘密数据写入 JWT
  3. JWT 不仅可以用于认证，也可以用于交换信息。有效使用 JWT，可以降低服务器查询数据库的次数
  4. JWT 的最大缺点是，由于服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限
  5. JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限，对于一些比较重要的权限，使用时应该再次对用户进行认证
  6. 为了减少盗用，JWT 不应该使用 HTTP 协议明码传输，要使用 HTTPS 协议传输

* token必要条件：

```
1. 客户端每次访问要将token放在请求头中
2. 服务端要设置正确的跨源策略，一般设置`Access-Control-Allow-Origin: *`
3. 服务端保存token密钥，不可以泄漏（因为是对称加密）
```

* jwt组成（json-web-token）：

```python
jwt = header + b"." + payload + b"." + signatures（结果是二进制字节串）
```

* header：

```python
header = {
    # 指定token类型和加密算法
    "typ": "JWT",
    "alg": "HS256", 
    
    
    # 自己的header
    "kid": "f139u8siknfd"
}

# 字典>>json字符串>>字节串>>base64编码
header = base64.b64encode(json.dumps(header).encode(), altchars=b"-_")	# 不能包含url不安全字节
```

* payload

```python
payload = {
    # 公有声明, 公共的声明可以添加任何的信息，一般添加用户的相关信息或其他业务需要的必要信息，但不能是敏感数据
    "exp": 过期时间戳(秒),
    "iss": token签发者,
    "aud": token受众(接受资源服务器),
    "iat": token签发时间,
    "nbf": 开始生效时间戳(秒),
    
    # 私有声明，私有声明是提供者和消费者所共同定义的声明，不放敏感数据
    "username": "laowang"
}

# 字典>>json字符串>>字节串>>base64编码
payload = base64.b64encode(json.dumps(payload).encode(), altchars=b"-_")	# 不能包含url不安全字节
```

* signature

```python
signature = header + b"." + payload
signature = HMAC(key=b"服务器统一密钥", msg=signature, digestmod="加密算法")
signature = base64.b64encode(signature, alterchars=b"-_)
```

* 浏览器携带token：

```js
fetch('api/user/1', {
  headers: {
    'Authorization': 'Bearer  ' + token	// 添加一个Authorization请求头并使用Bearer加上两个空格标注
  }
})
```

* 服务端获取token：

```python
token = request.META.get("HTTP_AUTHORIZATION")
```

### 1.3.2 pyjwt

使用第三方模块快速生成、校验token

* 生成token

```python
# 生成token
import jwt

def generate_token(username, exp=86400) -> bytes:
    payload = {
        'exp': int(time.time() + exp),
        'uname': username,
    }
    return jwt.encode(payload, "密钥", algorithm='HS256')
```

* 校验token

```python
# 校验token
import jwt

def check_token(f):
    def wrapper(self, request, *args, **kwargs):
        # 获取token
        token = request.META.get("HTTP_AUTHORIZATION")
        if not token: 
            # 错误提示 

        # 判断token是否在有效期内以及是否被篡改
        try:
            payload = jwt.decode(token, "密钥", 'HS256')
        except ExpiredSignatureError:
            # 提示已过期
        except InvalidSignatureError:
            # 提示非法token
		
        # 封装业务逻辑，为request共享对象对象添加属性、方法等
        # 为request添加request.my_user属性赋值
        return f(self, request, *args, **kwargs)
    
    return wrapper
```

## 1.4 CORS

### 1.4.1 原理

* CORS定义：Cross-Origin-Resource-Share，即跨域资源共享
* 跨域定义：不同域之间请求（域：协议+域名+端口号）时，新增一组header说明，来建立安全连接
* 详细参加：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS
* 跨源请求类型：简单请求 | 预检请求 + 真实请求
* 简单请求条件：

| 属性         | 要求                                                         |
| ------------ | ------------------------------------------------------------ |
| method       | GET \| POST \| HEAD 以内                                     |
| header       | Accept \| Accept-Language \| Content-Language 以内           |
| Content-Type | text/plain \| application/x-www-form-urlencoded \| multipart/form-data 以内 |

* 简单请求头：

| header             | 说明                     | 备注 |
| ------------------ | ------------------------ | ---- |
| Origin: 自己的域名 | 只要是跨域请求，必须携带 | 必选 |

* 简单响应头：

| header                                  | 说明                                                    | 备注 |
| --------------------------------------- | ------------------------------------------------------- | ---- |
| Access-Control- Allow-Origin: 域名 \| * | 若需要cookie操作，不能使用*，表示允许哪些域可以跨进来   | 必选 |
| Access-Control- Allow-Credentials: true | 当浏览器的 `credentials` 设置为 true 时，是否接受Cooike |      |
| Access-Control-Expose-Headers: 头       | 允许浏览器拿到非默认基本的响应头                        |      |

* 简单请求示例：

```js
const xhr = new XMLHttpRequest();
const url = 'https://bar.other/resources/public-data/';

xhr.open('GET', url);
xhr.onreadystatechange = someHandler;
xhr.send();
```

```http
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Origin: https://foo.example
```

```http
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml

[XML Data]
```

* 预检请求条件：非简单请求均要发送预检请求（js代码自动完成预检请求）

* 预检请求头：

| header                         | 作用                 | 备注 |
| ------------------------------ | -------------------- | ---- |
| Origin                         | 表明此请求来自哪个域 | 必选 |
| Access-Control-Request-Method  | 真实请求的请求方法   | 必选 |
| Access-Control-Request-Headers | 真实请求包含的头     | 必选 |

* 预检响应头：

| 响应头                           | 作用                                 | 备注 |
| -------------------------------- | ------------------------------------ | ---- |
| Access-Control-Allow-Origin      | 同简单请求                           | 必选 |
| Access-Control-Allow-Methods     | 告诉浏览器，服务器接受得跨域请求方法 | 必选 |
| Access-Control-Allow-Headers     | 返回所有支持的头部，该响应头必然回复 | 必选 |
| Access-Control-Allow-Credentials | 同简单请求                           |      |
| Access-Control-Max-Age           | OPTION请求缓存时间，单位s            |      |

* 真实请求：预检请求通过后再发送真实请求

* 真实请求头：

| 请求头 | 作用                 | 备注 |
| ------ | -------------------- | ---- |
| Origin | 表明此请求来自哪个域 | 必选 |

- 真实请求响应头：


| 响应头                      | 作用               | 备注 |
| --------------------------- | ------------------ | ---- |
| Access-Control-Allow-Origin | 当前服务器接受的域 | 必选 |

* 预检请求+真实请求示例：

```js
const xhr = new XMLHttpRequest();
xhr.open('POST', 'https://bar.other/resources/post-here/');
xhr.setRequestHeader('X-PINGOTHER', 'pingpong');	// 使用自定义header
xhr.setRequestHeader('Content-Type', 'application/xml');	// Content-Type包含非简单请求内容
xhr.onreadystatechange = handler;
xhr.send('<person><name>Arun</name></person>');
```

```http
OPTIONS /doc HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Origin: https://foo.example
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER, Content-Type
```

```http
HTTP/1.1 204 No Content
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
Vary: Accept-Encoding, Origin
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
```

```http
POST /doc HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
X-PINGOTHER: pingpong
Content-Type: text/xml; charset=UTF-8
Referer: https://foo.example/examples/preflightInvocation.html
Content-Length: 55
Origin: https://foo.example
Pragma: no-cache
Cache-Control: no-cache

<person><name>Arun</name></person>
```

```http
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:40 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 235
Keep-Alive: timeout=2, max=99
Connection: Keep-Alive
Content-Type: text/plain

[Some XML payload]
```

![](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/preflight_correct.png)

* 携带身份凭证的跨域请求（跨域同时发送cookie，默认不发送）：
  1. 携带身份凭证的跨域时，服务端header不得设置通配符
  2. 服务端响应携带`Access-Control-Allow-Credentials: true`才表明允许携带凭证

```js
const invocation = new XMLHttpRequest();
const url = 'https://bar.other/resources/credentialed-content/';

function callOtherDomain() {
  if (invocation) {
    invocation.open('GET', url, true);
    invocation.withCredentials = true;		// 设置withCredentials标志位
    invocation.onreadystatechange = handler;
    invocation.send();
  }
}
```

![](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/cred-req-updated.png)

```http
GET /resources/credentialed-content/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Referer: https://foo.example/examples/credential.html
Origin: https://foo.example
Cookie: pageAccess=2

```

```http
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:34:52 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Credentials: true
Cache-Control: no-cache
Pragma: no-cache
Set-Cookie: pageAccess=3; expires=Wed, 31-Dec-2008 01:34:53 GMT
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 106
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain

[text/plain payload]
```

### 1.4.2 django-cors-headers

1. 安装django-cores-headers（pip install django-cors-headers）
2. INSTALLED_APPS 中添加 corsheaders
3. MIDDLEWARE 中添加 corsheaders.middleware.CorsMiddleware（放在CommonMiddleware上方）
4. settings.py中配置跨源策略

```python
# settings.py
CORS_ORIGIN_ALLOW_ALL = True | False	# 为True时不启用白名单

CORS_ORIGIN_WHITELIST =[				# 白名单,CORS_ORIGIN_ALLOW_ALL为False时启用
	"https://example.com"
	] 

CORS_ALLOW_METHODS = (
    'DELETE',
    'GET',
    'OPTIONS',
    'PATCH',
    'POST',
    'PUT',
	)

CORS_ALLOW_HEADERS = (
    'accept-encoding',
    'authorization',
    'content-type',
    'dnt',
    'origin',
    'user-agent',
    'x-csrftoken',
    'x-requested-with',
	)

CORS_PREFLIGHT_MAX_AGE  默认 86400s 一天

# 有没有一些特殊的自定义响应头，需要ajax那边从响应头取值的，一般不用配置，如果你写了一个自定义的头前端要拿，那就配置
CORS_EXPOSE_HEADERS  []

# 告诉浏览器到底要不要把跨域的cookie送上来，默认不要，跨域的cookie不要
CORS_ALLOW_CREDENTIALS  布尔值， 默认False
```

## 1.5 RESTful

* 参见：https://restfulapi.cn/

* 总体要求：
  1. 请求方法与对应行为匹配
  2. 合适的url格式
  3. 返回Json数据
  4. 出错时，不使用200状态码而是对应4xx或5xx

* 请求url格式：

```
GET    /zoos：列出所有动物园
POST   /zoos：新建一个动物园
GET    /zoos/ID：获取某个指定动物园的信息
PUT    /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH  /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE /zoos/ID：删除某个动物园
GET    /zoos/ID/animals：列出某个指定动物园的所有动物
DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
```

* 过滤信息（当记录数量过多时提供查询参数）：

```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?animal_type_id=1：指定筛选条件
```

* 2xx响应状态码：

```
GET:    200 OK
POST:   201 Created
PUT:    200 OK
PATCH:  200 OK
DELETE: 204 No Content
```

* 3xx响应状态码（301永久重定向，302临时重定向）：

```http
HTTP/1.1 303 See Other
Location: /api/orders/12345
```

* 4xx响应状态码：

```
400 Bad Request：服务器不理解客户端的请求，未做任何处理。
401 Unauthorized：用户未提供身份验证凭据，或者没有通过身份验证。
403 Forbidden：用户通过了身份验证，但是不具有访问资源所需的权限。
404 Not Found：所请求的资源不存在，或不可用。
405 Method Not Allowed：用户已经通过身份验证，但是所用的 HTTP 方法不在他的权限之内。
410 Gone：所请求的资源已从这个地址转移，不再可用。
415 Unsupported Media Type：客户端要求的返回格式不支持。比如，API 只能返回 JSON 格式，但是客户端要求返回 XML 格式。
422 Unprocessable Entity ：客户端上传的附件无法处理，导致请求失败。
429 Too Many Requests：客户端的请求次数超过限额。
```

* 示例：

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json
{
  "error": "Invalid payoad.",
  "detail": {
    "surname": "This field is required."
  }
}
```

* 5xx响应状态码：

```
500 Internal Server Error：客户端请求有效，服务器处理时发生了意外。
503 Service Unavailable：服务器无法处理请求，一般用于网站维护状态。
```

## 1.6 nginx 

* 下载安装（Arch）

```shell
$ sudo pacman -S nginx-mainline
```

* 修改主配置文件

```shell
$ sudo vim /etc/nginx/ginx.conf

# 添加http 下面两行
http {
	include conf.d/*.conf;
	types_hash_max_size 4096;
	...
}
```

* 增加项目单独配置文件

```shell
sudo mkdir /etc/nginx/cof.d && vim /etc/nginx/cof.d/项目名.conf

# 加入项目配置文件
server {
        
        listen 7000 default_server;
        listen [::]:7000 default_server;
        server_name __;

        root /home/gris/Project;	# 自定义的项目路径
        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }
}
```

* nginx系统服务设置

```shell
$ sudo systemctl enable nginx		# 开机自启
$ sudo systemctl start nginx		# 开启服务
$ sudo systemctl status nginx		# 查看服务运行状态和日志
```

* 配置nginx文件权限

```shell
$ sudo gpasswd -a http gris			# nginx在这里默认以http用户运行..把他加入当前用户组
$ chmod g+x /home/gris && chmod g+x /home/gris/Project	# 添加文件读取权限
```

* 重启nginx

```shell
$ sudo nginx -s reload				# 平滑重启
$ sudo systemctl restart nginx		# 强制重启
```

* 拷贝项目前端文件到/home/gris/Project
* 测试

## 1.7 docker

* 中文文档：https://www.docker.org.cn/index.html
* 定位：开源软件部署解决方案，也是轻量级应用容器框架，输入`c/s`结构
* 结构：一个镜像可以有多个容器
* 命令：

![](https://tva2.sinaimg.cn/large/006bmdycgy1h0avjm9rilj31mw10wwnf.jpg)

* 运行容器

```shell
$ sudo docker run [选项] 镜像名:标签名 [指令]
```

* 常用选项

```
* -i 表示以《交互模式》运行容器。
* -t 表示容器启动后会进入其命令行。加入这两个参数后，容器创建就能登录进去。即分配一个伪终端。
* --name 为创建的容器命名。
* -v 表示目录映射关系，即宿主机目录:容器中目录。注意:最好做目录映射，在宿主机上做修改，然后共享到容器上。 
* -d 会创建一个守护式容器在后台运行(这样创建容器后不会自动登录容器)。 
* -p 表示端口映射，即宿主机端口:容器中端口。
* --network=host 表示将主机的网络环境映射到容器中，使容器的网络与主机相同。
```

* 进入ubuntu容器并退出

```shell
$ sudo docker run -it ubuntu:latest
$ exit
```

* 后台运行ubuntu容器（守护进程方式运行）

```shell
$ sudo docker run -dit ubuntu:latest	 # 以后台模式运行
$ sudo docker exec -it 容器id /bin/bash	# 进入终端
```

* 自定义容器名

```shell
$ sudo docker run -dit --name=myubuntu ubuntu:latest
```

## 1.8 fastDFS

* 介绍：C编写的开源、轻量级、分布式的文件系统
* 功能：文件上传、下载、同步
* 特点 ：冗余备份、伏在均衡、线性扩容
* 架构：client + tracker server + storage server

![](http://127.0.0.1:9999/assets/06FastDFS%E6%9E%B6%E6%9E%84.png)

* 文件上传

![](http://127.0.0.1:9999/assets/07FastDFS%E4%B8%8A%E4%BC%A0%E6%96%87%E4%BB%B6%E6%B5%81%E7%A8%8B.png)

* 文件下载

![](http://127.0.0.1:9999/assets/08FastDFS%E4%B8%8B%E8%BD%BD%E6%96%87%E4%BB%B6%E6%B5%81%E7%A8%8B.png)

![](http://127.0.0.1:9999/assets/10FDFS%E6%96%87%E4%BB%B6%E7%B4%A2%E5%BC%95%E7%9A%84%E4%BD%BF%E7%94%A8.jpg)

* FastDFS文件索引

![](https://tvax1.sinaimg.cn/large/006bmdycgy1h0aw4yucj0j313y0igtcp.jpg)

* fastDFS + docker

```shell
# 下载镜像
$ sudo docker image pull delron/fastdfs	

# 开启tracker
$ sudo docker run -dit --name tracker --network=host -v /var/fdfs/tracker:/var/fdfs delron/fastdfs tracker	

# 开启storage
$ sudo docker run -dti --name storage --network=host -e TRACKER_SERVER=本机ip:22122 -v /var/fdfs/storage:/var/fdfs delron/fastdfs storage	

# 查看运行状态
$ sduo docker contariner ls -a 

# 安装离线python模块（美多项目里有）
$ pip install fdfs_client-py-master.zip
$ pip install mutagen==1.40
$ pip install requests

# 修改本地client.conf文件（美多项目里有）
tracker_server=本机ip:22122

# 上传文件测试（python shell里运行）
from fdfs_client.client import Fdfs_client
client = Fdfs_client(conf_path='client.conf文件绝对路径')
client.upload_by_filename('/home/gris/Desktop/1.png')

# 下载文件测试
http://本机ip:8888/group1/M00/00/00/sAQKp2IxT8iAfFpLAALoF-3CSz8156.png
```

## 1.9 elasticsearch

* 定位：最常用的全文检索技术
* 底层依赖：

## 1.10 virtualenv

* 安装库文件

```shell
$ pip install virtualenv
```

* 修改终端环境配置文件（zsh为例）

```shell
$ sudo vim ~./zshrc
# 添加如下代码
export WORKON_HOME=~/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
export VIRTUALENVWARPPER_VIRTUALENV=~/.local/bin/virtualenv
source ~/.local/bin/virtualenvwrapper.sh
```

* 让终端配置文件生效

```shell
$ source ~/.zshrc
```

* 创建虚拟环境

```shell
mkvirtualenv 环境名 -p `which python`
```

* 进入环境

```shell
workon 环境名
```

* 退出环境

```shell
deactivate
```

## 1.11 DRF

即 Django REST Framework，在RESTful理念中简化API编写工作

### 1.11.1 安装

* 所有教程参考：https://www.django-rest-framework.org/

* 安装djangorestframework库

```shell
$ pip install djangorestframework
```

* 注册应用

```python
# settings.py
THIRD_APPS = [
    'rest_framework',
]
INSTALLED_APPS += THIRD_APPS
```

### 1.11.2 项目初始化

* 添加应用

```shell
$ mkdir apps && cd apps
$ python ../manage.py startproject book
```

* 注册应用

```python
# settings.py
MY_APPS = [
    'apps.book'
]
INSTALLED_APPS += MY_APPS
```

* 修改路径

```shell
# apps.book.apps.py
class BookConfig(AppConfig):
    # ..
    name = 'apps.book'					# 原本是 name = 'book' 会报错
```

* 配置主路由

```shell
# settings.py
urlpatterns = [
    path('admin/', admin.site.urls),
]

# 下行代码意思是 一次性添加所有自己创建的应用路由
urlpatterns += [path('', include(f'{app}'+'.urls')) for app in settings.MY_APPS]
```

* 配置应用路由、配置数据库（略）
* 建表示例

```python
# apps.book.models.py

class BookInfo(models.Model):
    # id
    name = models.CharField(max_length=20, verbose_name='名称')
    publish_date = models.DateField(verbose_name='发布日期', null=True)
    read_count = models.IntegerField(default=0, verbose_name='阅读量')
    comment_count = models.IntegerField(default=0, verbose_name='评论量')
    is_deleted = models.BooleanField(default=False, verbose_name='逻辑删除')
    image = models.ImageField(upload_to='book', null=True, verbose_name='图片')
    # people

    class Meta:
        db_table = 'book_book_info'  # 指明数据库表名
        verbose_name = '图书'  # 在admin站点中显示的名称

    def __str__(self):
        """定义每个数据对象的显示信息"""
        return self.name


# 准备人物列表信息的模型类
class PeopleInfo(models.Model):
	# id
    name = models.CharField(max_length=20, verbose_name='名称', )
    password = models.CharField(max_length=20, verbose_name='密码')
    description = models.CharField(max_length=200, null=True, verbose_name='描述信息')
    book = models.ForeignKey(BookInfo, related_name='people', on_delete=models.CASCADE)  # 外键
    is_deleted = models.BooleanField(default=False, verbose_name='逻辑删除')

    class Meta:
        db_table = 'book_people_info'
        verbose_name = '人物信息'

    def __str__(self):
        return self.name
```

* 添加序列化器类

```python
# apps.book.serializers.py
from rest_framework import serializers


# PeopleInfo序列化器
class PeopleInfoSerializer(serializers.Serializer):
    id = serializers.IntegerField(label='ID')
    book_id = serializers.IntegerField(label='图书id')
    name = serializers.CharField(label='姓名')
    password = serializers.CharField(label='密码')
    description = serializers.CharField(label='描述')


# BookInfo序列化器
class BookInfoSerializer(serializers.Serializer):
    """图书数据序列化器 与模型字段一一对应 is_delete、image可以不写"""
    id = serializers.IntegerField(label='ID')
    name = serializers.CharField(label='名称')
    publish_date = serializers.DateField(label='发布日期')
    read_count = serializers.IntegerField(label='阅读量')
    comment_count = serializers.IntegerField(label='评论量')
```

* 序列化字段类型

| 字段                    | 字段构造方式                                                 |
| :---------------------- | :----------------------------------------------------------- |
| **BooleanField**        | BooleanField()                                               |
| **NullBooleanField**    | NullBooleanField()                                           |
| **CharField**           | CharField(max_length=None, min_length=None, allow_blank=False, trim_whitespace=True) |
| **EmailField**          | EmailField(max_length=None, min_length=None, allow_blank=False) |
| **RegexField**          | RegexField(regex, max_length=None, min_length=None, allow_blank=False) |
| **SlugField**           | SlugField(max*length=50, min_length=None, allow_blank=False) 正则字段，验证正则模式 [a-zA-Z0-9*-]+ |
| **URLField**            | URLField(max_length=200, min_length=None, allow_blank=False) |
| **UUIDField**           | UUIDField(format='hex_verbose') format: 1)`'hex_verbose'`如`"5ce0e9a5-5ffa-654b-cee0-1238041fb31a"` 2）`'hex'`如`"5ce0e9a55ffa654bcee01238041fb31a"` 3）`'int'`- 如:`"123456789012312313134124512351145145114"` 4）`'urn'`如:`"urn:uuid:5ce0e9a5-5ffa-654b-cee0-1238041fb31a"` |
| **IPAddressField**      | IPAddressField(protocol='both', unpack_ipv4=False, **options) |
| **IntegerField**        | IntegerField(max_value=None, min_value=None)                 |
| **FloatField**          | FloatField(max_value=None, min_value=None)                   |
| **DecimalField**        | DecimalField(max_digits, decimal_places,  coerce_to_string=None, max_value=None, min_value=None) max_digits: 最多位数  decimal_palces: 小数点位置 |
| **DateTimeField**       | DateTimeField(format=api_settings.DATETIME_FORMAT, input_formats=None) |
| **DateField**           | DateField(format=api_settings.DATE_FORMAT, input_formats=None) |
| **TimeField**           | TimeField(format=api_settings.TIME_FORMAT, input_formats=None) |
| **DurationField**       | DurationField()                                              |
| **ChoiceField**         | ChoiceField(choices) choices与Django的用法相同               |
| **MultipleChoiceField** | MultipleChoiceField(choices)                                 |
| **FileField**           | FileField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL) |
| **ImageField**          | ImageField(max_length=None, allow_empty_file=False, use_url=UPLOADED_FILES_USE_URL) |
| **ListField**           | ListField(child=, min_length=None, max_length=None)          |
| **DictField**           | DictField(child=)                                            |

* 通用参数

| 参数名称       | 说明                                                         | 默认值 |
| :------------- | :----------------------------------------------------------- | ------ |
| **read_only**  | 表明该字段仅用于序列化输出，针对数据库而言是只读的，不可被反序列化输入，如id自增字段，有则忽略 | False  |
| **write_only** | 表明该字段仅用于反序列化输入，针对数据库而言是只写的，不可序列化输出，如password隐私字段 | False  |
| **required**   | 表明该字段在反序列化时必须输入，如果required=False，可以输入也可以不输入，如输入了则校验 | True   |
| **default**    | 反序列化时使用的默认值                                       |        |
| **label**      | 用于HTML展示API页面时，显示的字段名称                        |        |

* 选项参数

| 参数名称        | 作用           |
| :-------------- | :------------- |
| **max_length**  | 最大长度       |
| **min_lenght**  | 最小长度       |
| **allow_blank** | 是否允许为空   |
| **allow_null**  | 是否允许为null |
| **max_value**   | 最大值         |
| **min_value**   | 最小值         |

* read_only 和 required的区别
  1. read_only=True表示仅用作序列化输出，反序列化时，就算提供了rerad_only字段值也不会用上，也不做校验
  2. required=True表示反序列化时必须提供，required=False时，如果提供了required=False字段的值，将会做校验

### 1.11.3 序列化与反序列化

* 序列化模型实例

```python
book = BookInfo.objects.first()
serializer = BookInfoSerializer(instance=book)
serializer.data		# {"id":1, "name":"xx", "publish_date":"xx"...}
```

* 序列化QuerySet

```python
books = BookInfo.objects.all()
serializer = BookInfoSerializer(instance=books,many=True)
serializer.data		# [OrderedDict(..),...]
```

* 序列化外键
  * 使用数据库原始格式（book_id = 1）
  * 使用自定义格式（book = 1）（使用ModelSerializer默认该情形）
  * 使用模型str方法结果（book = '雪山飞狐'）

```python
# 使用数据库原始格式（book_id = 1）

# PeopleInfo序列化器
class PeopleInfoSerializer(serializers.Serializer):
    id = serializers.IntegerField(label='ID')
    book_id = serializers.IntegerField(label='图书id')		# IntergerField对应BookInfo的id主键类型
    # ...
```

```python
# 使用自定义格式（book = 1）

# PeopleInfo序列化器
class PeopleInfoSerializer(serializers.Serializer):
    id = serializers.IntegerField(label='ID')
    book = serializers.PrimaryKeyRelatedField(label='图书id', read_only=True)
    # ...
    
	# 或者 
	book = serializers.PrimaryKeyRelatedField(label='图书id', query_set=BookInfo.objects.all())
```

```python
# 使用模型str方法结果（book = '雪山飞狐'）

# PeopleInfo序列化器
class PeopleInfoSerializer(serializers.Serializer):
    id = serializers.IntegerField(label='ID')
    book = serializers.StringRelatedField(label='图书id')
    # ...
```

* 从表4种外键写法区别

```python
# 1. 
book = serializers.PrimaryKeyRelatedField(label='图书id', read_only=True)
# 2.
book = serializers.PrimaryKeyRelatedField(label='图书id', queryset=xxx)
# 3.
book_id = serializers.IntegerField(label='图书id')
# 4. 
book = serializers.IntegerField(label='图书id')

"""
PrimaryKeyRelatedField在反序列化时，外键在序列化器create或update中以模型对象形式存在
而IntegerField以数字格式存在
ModelSerializer默认是第四种格式
"""
```

* 嵌套序列化

```python
# BookInfo序列化器
class BookInfoSerializer(serializers.Serializer):
    """图书数据序列化器 与模型字段一一对应 is_delete、image可以不写"""
    id = serializers.IntegerField(label='ID')
    # ...
    # people对应PeopleInfo表中book_id字段related_name的值required=False表示反序列化时可以忽略,many=True表示一对多
	people = PeopleInfoSerializer(many=True, required=False)
```

```json
{
    "id": 1,
    "name": "射雕英雄传",
    "pub_date": "1980-05-01",
    "readcount": 12,
    "commentcount": 34,
    "people": [
        {
            "id": 1,
            "name": "郭靖",
            "password": "123456abc",
            "description": "降龙十八掌"
        },
        {
            "id": 2,
            "name": "黄蓉",
            "password": "123456abc",
            "description": "打狗棍法"
        }
    ]
}
```

* 反序列化验证数据合法性

```python
data = {
    # ...
}
serializer = BookInfoSerializer(data=data)
serializer.is_valid()							# False | True 
serializer.errors								# {错误信息} | {}		(使用.errors前必须先调用.is_valid())
serializer.validated_data						# {} | {合法数据}		(使用.validated_data前先.is_valid())
serializer.data									# {} | {合法数据包含id}
```

* 反序列化验证数据时，不合法自动返回400错误

```python
serializer.is_valid(raise_exception=True)
```

* 自定义字段验证函数（验证单个字段）

```python
class BookInfoSerializer(serializers.Serializer):
    def validate_comment_count(self, comment_count):	# validate_字段名 来定义校验函数名
        
        """自定义校验函数"""
        if comment_count < 0:
            raise serializers.ValidationError(
                detail=f"你传入了评论数量为{comment_count},你家评论量负数?", # 出错时以字典显示错误信息
                code=10001												# 如{"detail":"xx", "code":"xx"}
            )
            return comment_count
        
        comment_count = serializers.IntegerField(label='评论量')
        # ..
```

* 自定义字段验证函数（验证多个字段）

```python
class BookInfoSerializer(serializers.Serializer):
        
    def validate(self, attrs):
        """自定义校验函数 校验多个字段"""
        read_count = attrs.get("read_count")
        comment_count = attrs.get("comment_count")
        if read_count != comment_count:
            raise serializers.ValidationError(detail="产品说了，阅读量必须和评论量相等")
        return attrs
            
    read_count = serializers.IntegerField(label='阅读量')
    comment_count = serializers.IntegerField(label='评论量', required=False)
```

* 保存或修改通过验证的数据（保存到数据库）

```python
class PeopleInfoSerializer(serializers.Serializer):

    def update(self, instance, validated_data):
        for k, v in validated_data.items():
            setattr(instance, k, v)
        instance.save()
        return instance

    def create(self, validated_data):
        return PeopleInfo.objects.create(**validated_data)

    id = serializers.IntegerField(label='ID', read_only=True)
    book_id = serializers.IntegerField(label='图书', required=True)
    name = serializers.CharField(label='姓名', required=True)
    password = serializers.CharField(label='密码', required=True, write_only=True)
    description = serializers.CharField(label='描述', required=False, )
```

```python
data = {...}
serializer = PeopleInfoSerializer(data=data)	# 如果不传递instance参数，save()方法自动调用create方法
serializer.is_valid(raise_exception=True)
book = serializer.save()			
```

```python
data = {...}
people_info = PeopleInfo.objects.last()
serializer = PeopleInfoSerializer(instance=people_info, data=data)	# 如果传递instance参数，save()调用create
serializer.is_valid(raise_exception=True)
book = serializer.save()	
```

* 调用create或update时传递额外参数

```python
def create(self, validated_data):
    user = validated_data.pop("user")				# 可以通过validated_data中获取到
    return PeopleInfo.objects.create(**validated_data)
```

```python
serializer.save(user=request.user)
```

* 仅更新部分数据

```python
# 默认情况下，序列化程序必须为所有必填字段传递值，否则它们将引发验证错误
serializer = PeopleInfoSerializer(instance=people_info, data={"name":"老王"}, partial=True)
```

* 使用ModelSerializer自动填充显式字段

```python
# 原模型

class BookInfo(models.Model):
    # 创建字段，字段类型...
    name = models.CharField(max_length=20, verbose_name='名称')
    publish_date = models.DateField(verbose_name='发布日期', null=True)
    read_count = models.IntegerField(default=0, verbose_name='阅读量')
    comment_count = models.IntegerField(default=0, verbose_name='评论量')
    is_deleted = models.BooleanField(default=False, verbose_name='逻辑删除')
    image = models.ImageField(upload_to='book', null=True, verbose_name='图片')

    class Meta:
        db_table = 'book_book_info'  # 指明数据库表名
        verbose_name = '图书'  # 在admin站点中显示的名称

    def __str__(self):
        """定义每个数据对象的显示信息"""
        return self.name
```

```python
# 建立ModelSerializer

class BookInfoModelSerializer(ModelSerializer):
    class Meta:
        model = BookInfo
        fields = '__all__'			# 表示将模型类所有显式字段都列出
```

```python
# 查看结果
BookInfoModelSerializer():
    id = IntegerField(label='ID', read_only=True)
    name = CharField(label='名称', max_length=20)
    publish_date = DateField(allow_null=True, label='发布日期', required=False)
    read_count = IntegerField(label='阅读量', max_value=2147483647, min_value=-2147483648, required=False)
    comment_count = IntegerField(label='评论量', max_value=2147483647, min_value=-2147483648, required=False)
    is_deleted = BooleanField(label='逻辑删除', required=False)
    image = ImageField(allow_null=True, label='图片', max_length=100, required=False)

```

* ModelSerializer常用内部类字段

```python
class BookInfoModelSerializer(ModelSerializer):
    class Meta:
        model = BookInfo				# 必填
        fields = [...] or '__all__'		# 必填 '__all__' 或 ['字段名',...],为all时显式字段将全部拥有
        exclude = [..]					# 可选 表示除了字段之外 与fields互斥
        read_only_fields = [...]		# 为序列化器字段添加read_only(进序列化输出)
        extra_kwargs = {
            'name': {"min_length":6, ..}# 添加额外条件限制	
        }
```

* 验证数据，当提供数据时，外键格式是book时（默认情况，包含fields='\__all__'时）

```python
data = {
    "book": 1
}

class BookInfoModelSerializer(ModelSerializer):
    class Meta:		
        model = BookInfo
		fields = ['book']		# PrimaryKeyRelatedField(label='图书', queryset=BookInfo.objects.all())
```

* 验证数据，当外键格式是book_id时（需要额外修改）

```python
data = {
    "book_1": 1
}

class BookInfoModelSerializer(ModelSerializer):
    book_id = serializers.IntegerField()		# 重写类变量即可
    class Meta:		
        model = BookInfo
		fields = ['book_id']
```

* 反序列化时，若提供比序列化器fields中更多的字段，尽管提供了任何乱七八糟的内容，或者提供了属性read_only=True的字段时，自动忽略这些字段，也不会报错。但是如果少提供required=True字段的内容，则serializer.is_valid()必然会False

```python
反序列化忽略输入data中部分内容的情形：
	1. 提供了fields以外的内容，但包含在数据库内
    2. 提供了fields以外的内容，且不包含在数据库内
    3. 提供了fields以内的内容，但该字段标明了read_only=True
```

```python
序列化时忽略数据库部分内容的情形：
	1. fields字段以外的内容，这些内容在数据库中是存在的
    2. 标明write_only的字段，这些内容在数据库中是存在的
    3. 调用完is_valid()后，调用validated_data时会忽略掉id主键，使用data获得完整数据
```

```python
ModelSerializer反序列化时，其它几个重要特性
	1. 模型中的主键"id"在ModelSerializer序列化器中，默认read_only=True
    2. 模型中的外键如"book"在ModelSerializer序列化器中，默认格式是 book=PrimaryKeyRelatedField(...)
    3. 模型中的隐藏字段，如反向关系"author_set"默认不会在ModelSerializer序列化器中体现，若需要则添加该字段即可
    4. 如果需要验证或保存某一个字段，该字段必须要在序列化器中定义该字段
    5. 保存列表字典时，需要设置xxModelSerializer(data=xx, many=True)
```

* ModelSerializer嵌套序列化

```python
class BookInfoModelSerializer(ModelSerializer):
    people = PeopleInfoModelSerializer(many=True)	# 默认不包含模型类隐式字段，如需要则手动添加
    
    class Meta:
        model = BookInfo
        fields = '__all__'
```

* ModelSerializer嵌套反序列化

  默认反向关系是隐式的，不包含在ModelSerializer里，如果要启用嵌套反序列化，需要执行以下几个步骤

  1. 在ModelSerializer序列化器中添加反向关系字段
  2. 重写create方法

```js
# 需要保存的数据：

{
    'name':'离离原上草',
    'people':[
        {
            'name': '靖妹妹111',
            'password': '123456abc'
        },
        {
            'name': '靖表哥222',
            'password': '123456abc'
        }
    ]
}
```

```python
# 序列化器中添加反向关系字段，并重写create方法

class PeopleInfoModelSerializer(ModelSerializer):
    # book = serializers.RelatedField(many=True)

    class Meta:
        model = PeopleInfo
        fields = '__all__'
        # exclude = ['book']
        extra_kwargs = {
            "book": {"required": False},
            "id": {"read_only": False}
        }


class BookInfoModelSerializer(ModelSerializer):
    # 加上这一行才可以嵌套序列化
    people = PeopleInfoModelSerializer(many=True)

    # 加上这一坨才能嵌套反序列化 添加
    def create(self, validated_data):
        peoples = validated_data.pop('people')
        book = BookInfo.objects.create(**validated_data)
        for people in peoples:
            PeopleInfo.objects.create(book=book, **people)
        return book
    
	# 加上这一坨才能嵌套反序列化 修改
    def update(self, instance, validated_data):
        peoples = validated_data.pop('people')
        for k, v in validated_data.items():
            setattr(instance, k, v)
        instance.save()
        for people in peoples:
            people_id = people.pop("id")	# 前提是从表的id要从默认的read_only=True改为False
            PeopleInfo.objects.filter(book=instance, id=people_id).update(**people)
        return instance

    class Meta:
        model = BookInfo
        fields = '__all__'
```

* 使用 validator + 闭包 对字段设置通用正则校验函数

```python
def regex_check(regex, errmsg):
	def inner_func(*args):
    	value = args[0]
        if not re.fullmatch(regex, str(value)):
        	raise serializers.ValidationError(errmsg)
	return inner_func

class BookInfoModelSerializer(ModelSerializer):

    class Meta:		
        model=BookInfo
		fields = ['name']		# PrimaryKeyRelatedField(label='图书', queryset=BookInfo.objects.all())
        extrat_kwargs = {
            "name": {"validators": [regex_check('\S{1,30}', '书名应该是1-30位非空字符组成'),...]
        }
```

### 1.11.4 视图类

| 视图类         | 新增功能或需求                                               | 继承自                  |
| -------------- | ------------------------------------------------------------ | ----------------------- |
| APIView        | reqeust.query_params、request.data、Response、Request        | View                    |
| GenericAPIView | queryset、serializer_class、lookup_field、self.get_queryset()、self.get_serializer()、self.get_object()、self.kwargs | APIView                 |
| mixin扩展类    | self.create()、self.list()、self.retrieve()、self.update()、self.partial_update()、self.delete()、self.performxxx | /                       |
| 三级视图       | 只用定义好类变量如queryset、serializer_class、lookup_field即可 | mixin类、GenericAPIView |
| 视图集         | 一个模型类只用定义一个视图集                                 | 略                      |

* mixin扩展类：

```python
from rest_framework.mixins import \
CreateModelMixin, ListModelMixin, RetrieveModelMixin, UpdateModelMixin, DestroyModelMixin
```

* 三级视图：

```python
# 三级视图 方法都写好了 只用定义 queryset和serializer_class

from rest_framework.generics import \
CreateAPIView, ListAPIView, RetrieveAPIView, DestroyAPIView, UpdateAPIView, ListCreateAPIView, RetrieveUpdateAPIView, RetrieveDestroyAPIView, RetrieveUpdateDestroyAPIView
```

* 视图集 ：

```python
ViewSet 需要自己定义list、create、retrieve、update、partial_update、destroy方法
GenericViewSet 类似GenericAPIView，也需要自己定义list等具体方法
ModelViewSet 类似三级视图，封装了同一个模型不同路由，最少只用声明queryset、serializer_class两个类变量
ReadOnlyModelViewSet 只提供好了list和retrieve方法
# 视图集能自动识别路径最后的整数，在update、delete、retrieve方法中大有用处
```

```python
# 视图集 ViewSet 不提供任何action 所以需要自己定义各种方法

class BookViewSet(ViewSet):
    
	queryset = BookInfo.objects.all()
    serializer_class = BookInfoModelSerializer
    
    def list(self, request):
        pass

    def create(self, request):
        pass

    def retrieve(self, request, pk=None):
        pass

    def update(self, request, pk=None):
        pass

    def partial_update(self, request, pk=None):
        pass

    def destroy(self, request, pk=None):
        pass
```

* 视图集 ModelViewSet

```python
# 视图集 ModelViewSet封装好了mixin中的所有方法

class BookInfoModelViewSet(ModelViewSet):
    queryset = BookInfo.objects.all()
    serializer_class = BookInfoModelSerializer
```

* 定义视图集的url

```python
from rest_framework.routers import DefaultRouter, SimpleRouter
# 相同点: DefaultRouter和SimpleRouter都是生成路由
# 不同点: DefaultRouter可以访问根路由
router = DefaultRouter()
# router = SimpleRouter()
# router.register会生成两个路由’books/‘和‘books/pk/’两个路由
# basename规则: 列表视图为basename-list 详情视图为basename-detail（basename不能重复） 主要在前后端不分离中使用
router.register('books', views.BookInfoModelViewSet, basename='books')
router.register('peoples', views.PeopleInfoModelViewSet, basename='peoples')
urlpatterns += router.urls

"""
	/books/1/这样的参数视图集能自动识别,或使用self.get_object()能获取对象
"""
```

* DRF提供了APIView，继承自django.views.generic.View，提供更丰富、简单的属性和功能

* 新增Request请求对象

| 属性                 | 内容                                                         | 数据类型                  |
| -------------------- | ------------------------------------------------------------ | ------------------------- |
| request.query_params | 直接获取请求查询字符串内容                                   | QueryDict                 |
| request.data         | 如果是post表单获取表单内容，如果是json则是转换后的python内置类型 | QueryDict \| 字典 \| 列表 |

* 新增Response响应对象

```python
Response(data=None, status=None,template_name=None, headers=None,exception=False, content_type=None)
```

* Response的status属性可以使用枚举

```python
status.HTTP_200_OK				# == 200
status.HTTP_201_CREATED
status.HTTP_202_ACCEPTED
status.HTTP_203_NON_AUTHORITATIVE_INFORMATION
status.HTTP_204_NO_CONTENT
status.HTTP_205_RESET_CONTENT
status.HTTP_206_PARTIAL_CONTENT
status.HTTP_207_MULTI_STATUS
...
```

* Response的content_type属性自适应
  * 前端指定Accept=\*/\*或application/json时返回json类型数据
  * 前端指定Accept=text/html时返回html类型数据，使用框架自带的模型渲染

* 一个简单视图类示例

```python
from rest_framework import status
from rest_framework.request import Request
from rest_framework.response import Response
from rest_framework.views import APIView
from book.serializers import BookInfoModelSerializer
from book.models import BookInfo


class BookAPIView(APIView):

    def get(self, request: Request):
        books = BookInfo.objects.all()
        serializer = BookInfoModelSerializer(instance=books, many=True)
        data = serializer.data
        return Response(data=data, status=status.HTTP_200_OK)

    def post(self, request: Request):
        data = request.data
        serializer = BookInfoModelSerializer(data=data, many=True)
        if serializer.is_valid():
            books = serializer.save()
            # serializer.validated_data返回的结果不带主键id 因为id字段是read_only=True 改为False即可
            # return Response(serializer.validated_data, status=status.HTTP_201_CREATED)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response({"code": 10001, "errmsg": "错误"}, status=status.HTTP_400_BAD_REQUEST)
```

* 异常处理

```python
class BookAPIView(APIView):
    
	def post(self, request: Request):
        # ..
		raise serializers.ValidationError(detail={"username": "名字重复，再换一个试试"})
# 只用产生异常，不用捕获异常(除了非DRF异常外)，APIView自动返回错误的json数据或html数据
```

```html
HTTP 400 Bad Request
Allow: GET, POST, HEAD, OPTIONS
Content-Type: application/json
Vary: Accept

{
    "username": "名字重复，再换一个试试"
}
```

* 使用serializer.is_valid(raise_exception=True)自动回复异常响应

```python
# ...
	if serializer.is_valid(raise_exception=True):
        ...
# ...
```

```json
[
    {
        "name": [
            "This field is required."
        ]
    }
]
```

* APIView特性总结

```
1. APIView中的request是rest_framework.request中的Request，新增了一系列新的属性和方法
2. APIView可以返回rest_framework.response中的Response响应对象，新增了一系列新的属性和方法
3. APIView中不用再捕获异常，只用产生异常，由APIView自己捕获，自定义异常通过serializers.异常名称抛出
```

* 视图集和三级视图区别

```python
"""
1. 视图集能自动识别路由后缀整数（代表id），而三级视图默认是查询字符串pk=xx
2. 三级视图一般用在特定请求方法中，视图集用在通用处理逻辑上
"""
```

### 1.11.5 认证和权限

* 设置全局认证和权限（认证依赖于权限，没有权限设置认证是无效的）

```python
# ------------------------------------------ rest_framework settings -----------------------------------
# 对rest_framework框架下所有视图设置全局权限，注意：只针对rest_framework所包含的视图才起作用

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': (                             # 设置权限，若设置，将对DEFAULT_AUTHENTICATION_CLASSES
        'rest_framework.permissions.IsAuthenticated',           # 下面列出的所有权限进行一一检查，和路由一样，只要
    ) if not DEBUG else (),                                     # 通过第一个，就会将用户标记为已认证
    
    'DEFAULT_AUTHENTICATION_CLASSES': (                         # 也可以通过视图类的authentication_class和permission_class类变量单独设置权限
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ),
}
```

* 单独设置认证和权限

```python
class BookInfoModelViewSet(ModelViewSet):
    
    queryset = BookInfo.objects.all()
    serializer_class = BookInfoModelSerializer
    permission_classes = [AllowAny]                       # 单独设置权限
    authentication_classes = [TokenAuthentication]        # 单独设置认证
```

### 1.11.6 分页

* 设置全局分页

```python
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS':  'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 100  # 每页数目
}
```

* 单独设置分页

```python
class BookDetailView(RetrieveAPIView):
    
    queryset = BookInfo.objects.all()
    serializer_class = BookInfoSerializer
    pagination_class = PageNumberPagination
```

* 自定义分页效果（重写分页类）

```python
class LargeResultsSetPagination(PageNumberPagination):

    page_size = 1000
    page_size_query_param = 'page_size'
    max_page_size = 10000
    
    
class BookDetailView(RetrieveAPIView):
    
    queryset = BookInfo.objects.all()
    serializer_class = BookInfoSerializer
    pagination_class = LargeResultsSetPagination
```

* 自定义分页 + 自定义返回分页结果

```python
class MyPageNumberPagination(PageNumberPagination):
    page_size = 5                           # 默认每页数量            必填
    max_page_size = 20                      # 每页最大支持记录数      可选
    page_query_param = 'page'               # 页数查询关键字          第几页
    page_size_query_param = 'pagesize'      # 每页数量查询关键字      每页条数

    def get_paginated_response(self, data):
        return Response(OrderedDict([
            ('count', self.page.paginator.count),           # 用户总数
            ('lists', data),                                # 用户详细信息
            ('page', self.page.number),                     # 页码
            ('pages', self.page.paginator.num_pages),       # 总页数
            ('pagesize', self.page.paginator.per_page)      # 单页容量
        ]))
```

