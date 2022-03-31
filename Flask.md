旧的框架（同步为主）：django（同步）、flask（同步）、tornado（异步)、twisted（异步）

新的框架（异步为主）：sanic、FastAPI、django3.0（同步向异步过渡）、flask2.0（同步向异步过渡）

# Flask

诞生于2010年（django2005年），是Armin ronacher用Python编写的轻量级Web开发框架

Flask本身相当于一个内核，其它几乎所有的功能都要用到扩展（Flask-Mail、Flask-Login、Flask-SQLAlchemy）,比如用Flask扩展加入ORM、窗体验证工具、文件上传、身份验证等，也没有默认的数据。

其WSGIG工具箱采用Werkzeug（路由模块），模板引擎则使用Jinjia2，加密使用itsdangrous

官网：https://flask.palletsprojects.com/en/2.1.x/<br>中文文档：https://flask.net.cn/（1.x和2.x区别不是很大）

**Flask常用第三方扩展包**：

* Flask-SQLalchemy：操作数据库ORM
* Flask-scrip：终端脚本工具，脚手架（现在被内置全局命令Flask淘汰）
* Flask-migrate：管理迁移数据库
* Flask-Session：Session存储方式指定
* Flask-Mail：邮件
* Flask-Login：认证用户状态
* Flask-RESTful：开发REST API工具
* Flask JSON-RPC：开发rpc远程服务调用
* Flask-Bable：国际化和本地化支持、翻译
* Flask-Moment：本地化日期和时间
* Flask-Admin：简单可扩展的管理接口的框架
* Flask-Bootstrap：集成前端Bootstrap框架（前后端分离，基本不用）
* Flask-WTF：表单（前后端分离，基本不用）
* marshmallow：序列化（django restframework内置了）
* 查看所有模块：https://pypi.org/search/?q=flask&o=&c=Framework+%3A%3A+Flask



