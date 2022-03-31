# WebFront

## 1. HTML

<font color="orange">HTML基础概念</font>

| 名词     | 解释                                                         |
| -------- | ------------------------------------------------------------ |
| 网页     | 网页是基于浏览器的应用程序，是数据展示的载体                 |
| 网页组成 | 网页 = 浏览器 + 服务器 + 协议                                |
| 浏览器   | 代替用户向服务器发送请求、解析展示服务器响应数据             |
| 服务器   | 存储数据、处理并响应请求                                     |
| 协议     | 规范数据在传输过程中的打包方式                               |
| HTML     | 超文本标记语言简写（Typer Text Markdown Language）           |
| 标签     | 也成为标记或元素，用于在网页中标记内容，分为单标签和双标签，不区分大小写，通常小写 |
| 单标签   | <font color="red">所有单标签建议使用如&lt;br /&gt;</font>格式进行闭合 |
| 空白折叠 | HTML文档中所有连续的空白字符都换转换成一个空格               |

<font color="orange">开发环境准备（ArchLinux）</font>

* 安装vscode（sudo pacman -S code）
* 安装汉化插件、Live Server插件（模拟服务器，能实时显示保存好的页面）
* 设置自动保存，模式为 after delay

<font color="orange">HTML结构</font>

![](https://www.runoob.com/wp-content/uploads/2013/06/02A7DD95-22B4-4FB9-B994-DDB5393F7F03.jpg)

### 1.1 通用属性

| 通用属性 | 说明                               |
| -------- | ---------------------------------- |
| class    | 定义一个或多个类                   |
| id       | 定义元素唯一的id                   |
| style    | 定义元素的行内样式                 |
| title    | 描述元素额外信息（作为工具条使用） |

### 1.2 常用标签

<table class="reference"><tbody><tr><th>
  标签</th>
    <th>
   英文全称</th>
    <th>
   中文说明</th>
    </tr><tr><td>
   a</td>
    <td>
   Anchor</td>
    <td>
   锚</td>
    </tr><tr><td>
   abbr</td>
    <td>
   Abbreviation</td>
    <td>
   缩写词</td>
    </tr><tr><td>
   acronym</td>
    <td>
   Acronym</td>
    <td>
   取首字母的缩写词</td>
    </tr><tr><td>
   address </td>
    <td>
   Address</td>
    <td>
   地址</td>
    </tr><tr><td>
   alt</td>
    <td>
   alter</td>
    <td>
   替用(一般是图片显示不出的提示)</td>
    </tr><tr><td>
   b</td>
    <td>
   Bold</td>
    <td>
   粗体（文本）</td>
    </tr><tr><td>
   bdo</td>
    <td>
   Direction of Text Display</td>
    <td>
   文本显示方向</td>
    </tr><tr><td>
   big</td>
    <td>
   Big</td>
    <td>
   变大（文本）</td>
    </tr><tr><td>
   blockquote</td>
    <td>
   Block Quotation</td>
    <td>
   区块引用语</td>
    </tr><tr><td>
   br</td>
    <td>
   Break</td>
    <td>
   换行</td>
    </tr><tr><td>
   cell</td>
    <td>
   cell</td>
    <td>
   巢</td>
    </tr><tr><td>
   cellpadding</td>
    <td>
   cellpadding</td>
    <td>
   巢补白</td>
    </tr><tr><td>
   cellspacing</td>
    <td>
   cellspacing</td>
    <td>
   巢空间</td>
    </tr><tr><td>
   center</td>
    <td>
   Centered</td>
    <td>
   居中（文本）</td>
    </tr><tr><td>
   cite</td>
    <td>
   Citation</td>
    <td>
   引用</td>
    </tr><tr><td>
   code</td>
    <td>
   Code</td>
    <td>
   源代码（文本）</td>
    </tr><tr><td>
   dd</td>
    <td>
   Definition Description</td>
    <td>
   定义描述</td>
    </tr><tr><td>
   del</td>
    <td>
   Deleted</td>
    <td>
   删除（的文本）</td>
    </tr><tr><td>
   dfn</td>
    <td>
   Defines a Definition Term</td>
    <td>
   定义定义条目</td>
    </tr><tr><td>
   div</td>
    <td>
   Division</td>
    <td>
   分隔</td>
    </tr><tr><td>
   dl</td>
    <td>
   Definition List</td>
    <td>
   定义列表</td>
    </tr><tr><td>
   dt</td>
    <td>
   Definition Term</td>
    <td>
   定义术语</td>
    </tr><tr><td>
   em</td>
    <td>
   Emphasized</td>
    <td>
   加重（文本）</td>
    </tr><tr><td>
   font</td>
    <td>
   Font</td>
    <td>
   字体</td>
    </tr><tr><td>
   h1~h6</td>
    <td>
   Header 1 to Header 6</td>
    <td>
   标题1到标题6</td>
    </tr><tr><td>
   hr</td>
    <td>
   Horizontal Rule</td>
    <td>
   水平尺</td>
    </tr><tr><td>
   href</td>
    <td>
   hypertext reference</td>
    <td>
   超文本引用</td>
    </tr><tr><td>
   i</td>
    <td>
   Italic</td>
    <td>
   斜体（文本）</td>
    </tr><tr><td>
   iframe</td>
    <td>
   Inline frame</td>
    <td>
   定义内联框架</td>
    </tr><tr><td>
   ins</td>
    <td>
   Inserted</td>
    <td>
   插入（的文本）</td>
    </tr><tr><td>
   kbd</td>
    <td>
   Keyboard</td>
    <td>
   键盘（文本）</td>
    </tr><tr><td>
   li</td>
    <td>
   List Item</td>
    <td>
   列表项目</td>
    </tr><tr><td>
   nl</td>
    <td>
   navigation lists</td>
    <td>
   导航列表</td>
    </tr><tr><td>
   ol</td>
    <td>
   Ordered List</td>
    <td>
   排序列表</td>
    </tr><tr><td>
   optgroup</td>
    <td>
   Option group</td>
    <td>
   定义选项组</td>
    </tr><tr><td>
   p</td>
    <td>
   Paragraph</td>
    <td>
   段落</td>
    </tr><tr><td>
   pre</td>
    <td>
   Preformatted</td>
    <td>
   预定义格式（文本 ）</td>
    </tr><tr><td>
   q</td>
    <td>
   Quotation</td>
    <td>
   引用语</td>
    </tr><tr><td>
   rel</td>
    <td>
   Reload</td>
    <td>
   加载</td>
    </tr><tr><td>
   s/ strike</td>
    <td>
   Strikethrough</td>
    <td>
   删除线</td>
    </tr><tr><td>
   samp</td>
    <td>
   Sample</td>
    <td>
   示例（文本</td>
    </tr><tr><td>
   small</td>
    <td>
   Small</td>
    <td>
   变小（文本）</td>
    </tr><tr><td>
   span</td>
    <td>
   Span</td>
    <td>
   范围</td>
    </tr><tr><td>
   src</td>
    <td>
   Source</td>
    <td>
   源文件链接</td>
    </tr><tr><td>
   strong</td>
    <td>
   Strong</td>
    <td>
   加重（文本）</td>
    </tr><tr><td>
   sub</td>
    <td>
   Subscripted</td>
    <td>
   下标（文本）</td>
    </tr><tr><td>
   sup</td>
    <td>
   Superscripted</td>
    <td>
   上标（文本）</td>
    </tr><tr><td>
   td</td>
    <td>
   table data cell</td>
    <td>
   表格中的一个单元格</td>
    </tr><tr><td>
   th</td>
    <td>
   table header cell</td>
    <td>
   表格中的表头</td>
    </tr><tr><td>
   tr</td>
    <td>
   table row</td>
    <td>
   表格中的一行</td>
    </tr><tr><td>
   tt</td>
    <td>
   Teletype</td>
    <td>
   打印机（文本）</td>
    </tr><tr><td>
   u</td>
    <td>
   Underlined</td>
    <td>
   下划线（文本）</td>
    </tr><tr><td>
   ul</td>
    <td>
   Unordered List</td>
    <td>
   不排序列表</td>
    </tr><tr><td>
   var</td>
    <td>
   Variable</td>
    <td>
   变量（文本）<br></td>
    </tr></tbody></table>

#### 1.2.1 标题标签

```html
<h1>标题标签</h1>
<h2>标题标签</h2>
<h3>标题标签</h3>
<h4>标题标签</h4>
<h5>标题标签</h5>
<h6>标题标签</h6>
```

#### 1.2.2 水平线标签

```html
<hr />
```

#### 1.2.3 换行标签

```html
<br />
```

#### 1.2.4 段落标签

```html
<p>段落标签</p>
```

#### 1.2.5 预格式标签

```html
<pre>预格式标签</pre> <!-- 预格式标签原模原样显示空格和换行 -->
```

#### 1.2.6 文本格式化标签

```html
<b>粗体文字blod</b>
<i>斜体文字</i> 
<strong>着重文字，粗体效果</strong>
<em>着重文字，斜体效果</em>
<small>小号字体</small>
<sub>下标字</sub>
<sup>上标字</sup>
<del>删除字，字体中间被横线划过效果</del> <!-- 网页中删除原来内容时使用 -->
<ins>插入字，下划线效果</ins> <!-- 网页中删除原来内容时使用 -->
<code>显示代码标签，也可以用css自定义效果</code> <!-- 网页中显示代码时使用 -->
```

#### 1.2.7 超链接标签

* 格式

```html
<a href="网址链接">链接提示文本</a>
```

* `target"`默认为`_self`，表示在本页加载链接

```html
<a href="网址链接" target="_self">链接提示文本</a>
```

* `target="_blank"`，表示在新起一页加载链接

```html
<a href="网址链接" target="_blank">链接提示文本</a>
```

* `href=#`可用作锚点，默认指向当前页面顶部

```html
<a href="#">链接提示文本</a>
```

* `href=#id`指向本页特定位置

```html
<a href="#id">链接提示文本</a>
```
* `href=url#id`指向其它页特定位置

```html
<a href="url#id">链接提示文本</a>
```

* 超链接发送邮件

```html
<a href="mailto:邮件地址?subject=邮件主题&body=邮件内容">点击发送邮件</a>
```

* 超链接下载文件

```html
<a href="文件名">点击下载文件</a>
```

#### 1.2.8 head标签

```html
<!-- head标签 -->
<head>
    <!-- meta标签定义文档属性 -->
    <meta charset="UTF-8">

    <!-- title标签定义文档标题 -->
    <title>常用标签</title>

    <!-- style标签定义页面CSS样式  -->
    <style></style>

    <!-- script标签中引入js脚本 -->
    <script></script>

    <!-- link标签定义网页引用资源，如果是图片可以作为网页图标 -->
    <link rel="stylesheet" href="图片url">

    <!-- base标签定义网页链接默认指向地址 -->
    <base href="">
</head>
```

#### 1.2.9 图片标签

```html
<!-- 图片标签
1、src可以是本地资源 也可以是网页资源
2、width和height不填时 默认原大小 只填一个 另一个等比例缩放
3、alt表示图片加载失败时显示内容
4、title表示鼠标悬停时显示内容 -->

<img src="https://www.runoob.com/images/pulpit.jpg" alt="图片加载失败时显示文字" width="100px" height="50px" title="鼠标悬停时提示内容">
```

* 图片映射

```html
<!-- 图片映射
shape表示分区形状 
shape="rect" 表示矩形 coords="x1,y1,x2,y2" 分别表示左上角坐标和右下角坐标
shape="circle" 表示圆圈 coords="x,y,r" 分别表示圆心坐标和半径
shape="ploy" 表示多边形 coords="x1,y1,x2,y2,x3,y3..." 分别表示各个顶点坐标 -->

<img src="https://www.runoob.com/try/demo_source/planets.gif" usemap="#planetmap">
<map name="planetmap">
    <area shape="rect" coords="0,0,82,126" href="https://www.runoob.com/images/sun.gif">
    <area shape="circle" coords="90,58,3" href="https://www.runoob.com/try/demo_source/merglobe.gif">
    <area shape="circle" coords="124,58,8"  href="https://www.runoob.com/images/venglobe.gif">
</map>
```

### 1.3 列表结构

* 列表前缀

```css
list-style-type: 风格;
```
| 风格                 | 形状     |
| -------------------- | -------- |
| none                 | 取消前缀 |
| circle               | 空心圆圈 |
| decimal              | 数字     |
| decimal-leading-zero | 0+数字   |
| square               | 正方形   |
| upper-alpha          | 大写字母 |
| lower-alpha          | 小写字母 |


```html
<body>
    <!-- 网站所有内容只要相似的都可以用列表处理=>进入网站->检查->删除css链接即可看到直观的列表 -->
    <!-- 列表也可以减少多个div的嵌套，降低网页复杂度 -->

    <!-- ol>li*4=>生成四个元素的有序列表 -->
    <h2>有序列表</h2>
    <ol>
        <li>item1</li>
        <li>itme2</li>
        <li>itme3</li>
        <li>imte4</li>
    </ol>

    <!-- 无序列表使用场景更多 -->
    <h2>无序列表</h2>
    <ul>
        <li>item1</li>
        <li>item2</li>
        <li>item3</li>
        <li>item4</li>
    </ul>

    <!-- 列表嵌套 -->
    <h2>列表嵌套</h2>
    <ul>
        <li>西游记
            <ol>
                <li>大师兄</li>
                <li>水浒传</li>
                <li>白龙马</li>
            </ol>
        </li>
        <!-- 无序套无序=>内层无序的圆点变空心 -->
        <li>水浒传
            <ul>
                <li>武松</li>
                <li>松江</li>
                <li>镇关西</li>
            </ul>
        </li>
        <li>红楼梦</li>
        <li>三国演义</li>
    </ul>

</body>
```

### 1.4 表格结构

* 适用场景：表格table只用作展示数据，页面结构使用div更合适
* 设置边框折叠：

```css
table{
    border-collapse: collapse;
}
```

* 设置统一边框样式：

```css
table,tr,td{
    border: 1px solid black; 
}
```

* 设置表格标题

```html
<table>
    <caption>表格标题</caption>
</table>
```

* 设置表格标题位置

```css
caption{
    caption-side: bottom;	/* 默认top */
}
```

* 设置表格文字内边距

```css
td,th{
    padding:3px 7px 2px 7px;
}
```

* 效果图

![](https://tva3.sinaimg.cn/large/006bmdycgy1gznnuyhivwj30rq07641v.jpg)

* 代码

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        table{
            width: 100%;
            font-family: 微软雅黑;
            /* 设置边框折叠 */
            border-collapse: collapse;
        }
        table tr td{
            border: 1px solid black;
        }
        tr{
            text-align: center;
        }
        td{
            height: 20px;
        }
        #title{
            text-align: center;
            font-size: 3em;
            font-weight: bold;
            color: dodgerblue;
            border-bottom: 1px dashed darkgrey;
        }
        #date{
            text-align: right;
        }
        .blue_tr>td{
            background-color:lightskyblue;
        }
    </style>
</head>
<body>
    <table align="center">

        <!-- 顶部表格 展示标题和日期 分开展示 低耦合 -->
        <table>
            <tr>
                <td id="title">受理业务员统计表</td>
            </tr>
            <tr>
                <td id="date">2017-01-02---2017-05-02</td>
            </tr>
        </table>

        <table id="content_table" >
            <tr class="blue_tr">
                <td colspan="2" rowspan="2">受理员</td>
                <td rowspan="2">受理数</td>
                <td rowspan="2">自办数</td>
                <td rowspan="2">直接解答</td>
                <td colspan="2">拟办意见</td>
                <td colspan="2">返回修改</td>
                <td colspan="3">工单类型</td>
            </tr>
            <tr class="blue_tr">
                <td>同意</td>
                <td>比例</td>
                <td>数量</td>
                <td>比例</td>
                <td>建议件</td>
                <td>诉求件</td>
                <td>咨询件</td>
            </tr>
            <tr>
                <td rowspan="3">受理处</td>
                <td>王艳</td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
            </tr>
            <tr>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
            </tr>
            <tr>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
                <td></td>
            </tr>
        </table>
    </table>
</body>
</html>
```

### 1.5 容器结构

* 效果图

![](https://tva4.sinaimg.cn/large/006bmdycgy1gzno70bt4yj31e40ie78z.jpg)

* 代码

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>容器标签-重点</title>
    <style>
        /* 先统一修改标签默认属性 */
        body,ul,h1{
            margin: 0;
        }
        /* 再针对选择器进行特定渲染 */
        #header{
            background-color: green;
            color: white;
        }
        #menu{
            width: 25%;
            height: 200px;
            background-color: darkgreen;
            float: left;
        }
        #content{
            background-color: black;
            width: 75%;
            height: 200px;
            color: white;
        }
        #footer{
            text-align: center;
            background-color: darkcyan;
            clear: both;
        }
    </style>
</head>
<body>
    <!-- 最外层容器 -->
    <div id="container">

        <!-- 网页页眉 -->
        <div id="header">
            <h1>主要的网页标题</h1>
        </div>

        <!-- 左侧导航栏 -->
        <div id="menu">
            <ul>
                <li>菜单</li>
                <li>HTML</li>
                <li>CSS</li>
                <li>JS</li>
            </ul>
        </div>

        <!-- 正文栏 -->
        <div id="content">
            这里显示内容
        </div>

        <!-- 底栏 -->
        <div id="footer">
            版权&copy;runoob.com
        </div>
    </div>
</body>
</html>
```

### 1.6 表单结构

* form属性

| 属性    | 说明                            |
| ------- | ------------------------------- |
| action  | 数据提交url地址                 |
| method  | 数据提交方式，默认get，可选post |
| enctype | 数据编码类型，可选              |

* input属性

| 属性        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| type        | 可选text、password、radio、checkbox、file、submit、reset、button、email、number等，表示是不同组件 |
| name        | 作为提交给服务器数据的键名                                   |
| value       | 组件值                                                       |
| placeholder | 提示信息                                                     |
| checked     | 默认选中                                                     |
| required    | 是否可为空，默认可以，若写上required表示不允许为空           |
| min         | 为数字时最小值                                               |
| max         | 为数字时最大值                                               |

* 注意事项

  > 1. method默认为get，此时enctype默认为text/plain，请求携带数据拼接在url中
  > 2. 若指定method=post，enctype默认为application/x-www-form-urlencode，请求携带数据在body中
  > 3. 若提交内容包含二进制文件，enctype需要手动指定为multipart/form-data2
  > 4. 表单提交时，仅携带带有name属性的表单控件value
  > 4. https://www.cnblogs.com/larrywang/p/10504421.html

* 常用控件

  文本输入框、密码框、单选框、多选框、下拉菜单、文本域、上传文件、表单控制按钮

* 代码

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>表单控件-重点</title>
</head>
<body>
    
    <form action="" method="GET" enctype="multipart/form-data">
        
        <!-- 输入框 -->
        <p>姓名:<input type="text" name="uname" placeholder="用户名"></p>
        <p>密码:<input type="password" name="upwd" placeholder="密码"></p>

        <!-- 单选框 -->
        <p>
            性别:<input type="radio" name="sex" value="1" checked>男
            <input type="radio" name="sex" value="0">女
        </p>

		<!-- 多选框 -->
        <p>
            爱好:<input type="checkbox" name="uhobby" value="0" checked>喝酒
            <input type="checkbox" name="uhobby" value="1">抽烟
            <input type="checkbox" name="uhobby" value="2">烫头
        </p>

        <!-- 下拉菜单 -->
        <p>地址:
            <select name="uaddr" size="4" multiple>
                <option value="1001">上海</option>
                <option value="1002" selected>重庆</option>
                <option value="1003">湖南</option>
                <option value="1004">江苏</option>
                <option value="1005">安徽</option>
                <option value="1006">蒙古</option>
                <option value="1007">河北</option>
            </select>
        </p>

		<!-- 文本域 -->
        <p>留言:<textarea style="resize: none;" name="usug" cols="30" rows="10"></textarea></p>

        <!-- 上传文件 -->
        <p>头像:<input type="file" name="uhead"></p>

        <!-- 表单控制按钮 -->
        <p>
            <input type="submit" name="submit" value="保存">
            <!-- reset=>重置 -->
            <input type="reset" name="reset">
            <!-- button=>普通按钮 -->
            <input type="button" name="button" value="一个普通的按钮">
        </p>

    </form>
</body>
</html>
```

### 1.7 框架结构

* 作用：同一个网页中显示多个页面
* 配置：在响应标头中配置是否可被用作`iframe`

```yacas
x-frame-options:DENY # 不允许嵌套，包括自己域名
				SAMEORIGIN  # 仅允许在相同域名中嵌套
				ALLOW-FROM # 仅在指定来源页面中嵌套
```

* 格式

```html
<iframe src="对应页面url" width="" height="" frameborder="">
```

* 超链接的`url`在`ifrme`中显示

```html
<iframe src="https://www.runoob.com" frameborder="0" width="500px", height="500px" name="noob"></iframe>
<a href="https://www.tmooc.cn/" target="noob">点击在iframe中展示tmooc</a>
```

## 2. CSS

<font color="orange">分类</font>

* 外部（简洁高效，如：```<link rel="stylesheet" href="demo01.css" type="text/css">```）
* 内嵌（写在style中，便于展示代码）
* 行内（写在对应标签的style属性中）

<font color="orange">性质</font>

* 层叠性：多组CSS样式可以同时作用于一个元素
* 继承性：后代继承绝大部分样式（主要是文字相关格式）
* 优先性：选择器范围越广，优先性越低

<font color="orange">元素分类</font>

| 类别       | 说明                                             | 举例                                                |
| ---------- | ------------------------------------------------ | --------------------------------------------------- |
| 块元素     | 独占一行，可以自定义宽高，宽度默认等于父元素宽度 | div、h1、p、table、ul、ol、li、form、table、body... |
| 行内元素   | 共行显示，不能自定义宽高，宽高由内容决定         | span、a、i、b、sub、strong、sup、code...            |
| 行内块元素 | 既能共行显示，也能自定义宽高                     | img、input、button...                               |

<font color="orange">嵌套规则</font>

* 除了p标签，块元素可以嵌套任何元素（大套小）
* 行内元素不要嵌套块元素

<font color="orange">尺寸单位</font>

* px 像素单位
* % 百分比，参照父元素对应属性的值进行计算
* em 字体尺寸单位，参照父元素的字体大小计算，1em=16px
* rem字体尺寸单位，参照根元素的字体大小计算，1rem=16px

<font color="orange">颜色</font>

```html
<p style="background-color:#FFFF00">通过十六进制设置背景颜色</p>

<p style="background-color:rgb(255,255,0)">通过 rgb 值设置背景颜色</p>

<p style="background-color:rgba(255,255,0, 0.5)">通过 rgba 值设置背景颜色</p>
	
<p style="background-color:yellow">通过颜色名设置背景颜色</p>
<p style="background-color:transparent">设置透明色</p>
```

<font color="orange">设计思路</font>

```html
<style>
    /* 当准备开始写样式之前 通常使用群组选择器
    找到页面中带有默认样式的元素 清除默认样式 */
    body,p,ul{
        margin: 0;
        padding: 0;
    }
    /* 当设置完页面样式后 可使用群组选择器将大段重复的代码放在一个
    群组中 减少样式代码的重复度 */
    div,p,.red,#header{
        width: 200px;   
        height: 200px;
    }
    div{ 
        /* width: 200px; */
        /* height: 200px; */
        background-color: red;
    }
    p{
        /* width: 200px; */
        /* height: 200px; */
        background-color: rosybrown;
    }
    /* 子代选择器 查找.nav中直接包含的li */
    /* 当页面元素的结构相同时 为外层元素设置Id或Class
    然后通过后代或自带选择器查找内部的元素
    为不同的元素设置不同的元素 */
    .nav>li{
        background-color: blue;
        color: white;
    }
    /* 后代选择器 查找.bot中的所有li */
    .bot li{
        background-color: black;
        color: grey;
    }
</style>
```

### 2.1 选择器分类

#### 2.1.1 标签选择器

* 格式：

```html
标签名{
	属性: 值;
}
```

* 举例：

```html
<style>
    p{
        backgroud-color: red;
    }
</style>
```

#### 2.1.2 类选择器

* 格式：

```html
[标签名].类名{
	属性: 值;
}
```

* 举例：

```html
<style>
    .center{
        text-align: center;
    }
    p.center{
        text-align: center;
    }
</style>
```

#### 2.1.3 伪类选择器

* 格式：

```html
基础选择器:伪类标记{
	属性: 值;
}
```

* 分类：

```yacas
:link 	 超链接访问前的状态
:visited 超链接访问后的状态
:hover	 鼠标滑过时的状态
:active  鼠标点按不抬起时的状态(激活)
:focus	 焦点状态(文本框被编辑时就称为获取焦点)
:first-child 匹配第一个基础选择器
```

* 使用链式调用伪类：

```css
li a:hover:not(.active){
    定义鼠标悬停在非active类下面时的动作;
}
```

* 使用丰富的其它伪类：

```css
p: first-child;			/* 匹配文档第一个p标签 */
p>i :first-child;		/* 匹配每个p标签下第一个i标签 */
p: first-child i; 		/* 匹配文档第一个p标签下面所有的i标签 */
```

* 更多伪类：

<table width="100%" cellspacing="0" cellpadding="0" border="1" id="table13" class="reference">
    <tbody><tr>
      <th width="20%" align="left">选择器</th>
      <th width="17%" align="left">示例</th>
      <th width="63%" align="left">示例说明</th>
    </tr>
  <tr>
      <td>:checked</td>
      <td>input:checked</td>
      <td>选择所有选中的表单元素</td>
    </tr>
  <tr>
      <td>:disabled</td>
      <td>input:disabled</td>
      <td>选择所有禁用的表单元素</td>
    </tr>
  <tr>
      <td>:empty</td>
      <td>p:empty</td>
      <td>选择所有没有子元素的p元素</td>
    </tr>
  <tr>
      <td>:enabled</td>
      <td>input:enabled</td>
      <td>选择所有启用的表单元素</td>
    </tr>
  <tr>
      <td>:first-of-type</td>
      <td>p:first-of-type</td>
      <td>选择的每个 p 元素是其父元素的第一个 p 元素</td>
    </tr>
  <tr>
      <td>:in-range</td>
      <td>input:in-range</td>
      <td>选择元素指定范围内的值</td>
    </tr>
  <tr>
      <td>:invalid</td>
      <td>input:invalid</td>
      <td>选择所有无效的元素</td>
    </tr>
  <tr>
      <td>:last-child</td>
      <td>p:last-child</td>
      <td>选择所有p元素的最后一个子元素</td>
    </tr>
  <tr>
      <td>:last-of-type</td>
      <td>p:last-of-type</td>
      <td>选择每个p元素是其母元素的最后一个p元素</td>
    </tr>
  <tr>
      <td>:not(selector)</td>
      <td>:not(p)</td>
      <td>选择所有p以外的元素</td>
    </tr>
  <tr>
      <td>:nth-child(n)</td>
      <td>p:nth-child(2)</td>
      <td>选择所有 p 元素的父元素的第二个子元素</td>
    </tr>
  <tr>
      <td>:nth-last-child(n)</td>
      <td>p:nth-last-child(2)</td>
      <td>选择所有p元素倒数的第二个子元素</td>
    </tr>
  <tr>
      <td>:nth-last-of-type(n)</td>
      <td>p:nth-last-of-type(2)</td>
      <td>选择所有p元素倒数的第二个为p的子元素</td>
    </tr>
  <tr>
      <td>:nth-of-type(n)</td>
      <td>p:nth-of-type(2)</td>
      <td>选择所有p元素第二个为p的子元素</td>
    </tr>
  <tr>
      <td>:only-of-type</td>
      <td>p:only-of-type</td>
      <td>选择所有仅有一个子元素为p的元素</td>
    </tr>
  <tr>
      <td>:only-child</td>
      <td>p:only-child</td>
      <td>选择所有仅有一个子元素的p元素</td>
    </tr>
  <tr>
      <td>:optional</td>
      <td>input:optional</td>
      <td>选择没有"required"的元素属性</td>
    </tr>
  <tr>
      <td>:out-of-range</td>
      <td>input:out-of-range</td>
      <td>选择指定范围以外的值的元素属性</td>
    </tr>
  <tr>
      <td>:read-only</td>
      <td>input:read-only</td>
      <td>选择只读属性的元素属性</td>
    </tr>
  <tr>
      <td>:read-write</td>
      <td>input:read-write</td>
      <td>选择没有只读属性的元素属性</td>
    </tr>
  <tr>
      <td>:required</td>
      <td>input:required</td>
      <td>选择有"required"属性指定的元素属性</td>
    </tr>
  <tr>
      <td>:root</td>
      <td>root</td>
      <td>选择文档的根元素</td>
    </tr>
  <tr>
      <td>:target</td>
      <td>#news:target</td>
      <td>选择当前活动#news元素(点击URL包含锚的名字)</td>
    </tr>
  <tr>
      <td>:valid</td>
      <td>input:valid</td>
      <td>选择所有有效值的属性</td>
    </tr>
    <tr>
      <td>:link</td>
      <td>a:link</td>
      <td>选择所有未访问链接</td>
    </tr>
      <tr>
      <td>:visited</td>
      <td>a:visited</td>
      <td>选择所有访问过的链接</td>
    </tr>
      <tr>
      <td>:active</td>
      <td>a:active</td>
      <td>选择正在活动链接</td>
    </tr>
      <tr>
      <td>:hover</td>
      <td>a:hover</td>
      <td>把鼠标放在链接上的状态</td>
    </tr>
      <tr>
      <td>:focus</td>
      <td>input:focus</td>
      <td>选择元素输入后具有焦点</td>
    </tr>
      <tr>
      <td>:first-letter</td>
      <td>p:first-letter</td>
      <td>选择每个&lt;p&gt; 元素的第一个字母</td>
    </tr>
      <tr>
      <td>:first-line</td>
      <td>p:first-line</td>
      <td>选择每个&lt;p&gt; 元素的第一行</td>
    </tr>
    <tr>
      <td>:first-child</td>
      <td>p:first-child</td>
      <td>选择器匹配属于任意元素的第一个子元素的 &lt;p&gt; 元素</td>
    </tr>
      <tr>
      <td>:before</td>
      <td>p:before</td>
      <td>在每个&lt;p&gt;元素之前插入内容</td>
    </tr>
      <tr>
      <td>:after</td>
      <td>p:after</td>
      <td>在每个&lt;p&gt;元素之后插入内容</td>
    </tr>
      <tr>
      <td>:lang(<i>language</i>)</td>
      <td>p:lang(it)</td>
      <td>为&lt;p&gt;元素的lang属性选择一个开始值</td>
    </tr>
      </tbody></table>
#### 2.1.4 后代选择器

* 格式：

```html
选择器1 选择器2{
	属性: 值;
}
```

* 举例：

```html
<style>
    p span{
        color: red;
    }
</style>
```

#### 2.1.5 子代选择器

* 格式：

```html
选择器1>选择器2{
	属性: 值;
}
```

* 举例：

```html
<style>
    p>span{
        color: red;
    }
</style>
```

#### 2.1.6 id选择器

* 格式：

```html
#id值{
	属性: 值;
}
```

* 举例：

```html
<style>
	#username{
		color: red;
	}
</style>
```

#### 2.1.7 群组选择器

* 格式：

```html
选择器1,选择器2{
	属性: 值；
}
```

#### 2.1.8 属性选择器

* 格式：

```html
选择器[标签属性="标签属性值"]{
	属性: 值;
}
```

* 举例：

```html
<style>
	p[title]{				/* 所有p标签中 含有title属性的所有元素 */
		color: red;
	}
    p[title="xixi"]{		/* 所有p标签中 含有title属性且值为"xixi"的所有元素 */
		color: red;
	}
</style>
```

#### 2.1.9 相邻兄弟选择器

匹配选择器1和选择器2是兄弟元素关系，此时在选择器2上面做事

* 格式：

```css
选择器1+选择器2{
    属性: 值;
}
```

#### 2.1.10 普通兄弟选择器

* 格式：

```css
选择器1~选择器2{
    属性: 值;
}
```

### 2.2 选择器优先级

* 复杂选择器(后代,子代,伪类)最终的权重为各个选择器权重值之和
* 群组选择器权重以每个选择器单独的权重为准，不进行相加计算
* 权重越大，优先级越高
* 权重相同，代码靠后的生效

| 选择器                           | 权重 |
| -------------------------------- | ---- |
| 标签选择器                       | 1    |
| 类选择器、伪类选择器、属性选器器 | 10   |
| id选择器                         | 100  |
| 行内样式                         | 1000 |

### 2.3 常用CSS属性

| 属性名                        | 功能         | 取值                                                         |
| ----------------------------- | ------------ | ------------------------------------------------------------ |
| width                         | 宽度         | 尺寸单位，行内元素无效                                       |
| height                        | 高度         | 尺寸单位，行内元素无效                                       |
| background-color              | 背景颜色     | 颜色单位                                                     |
| background-image              | 背景图片     | url("路径")                                                  |
| background-repeat             | 平铺方式     | 默认全屏平铺，repeat-x表示仅水平平铺，no-repeat表示不平铺    |
| background-position           | 图片位置     | 图片起始位置                                                 |
| background-attachment         | 定位方式     | 默认相对定位，fixed表示固定定位，不随页面滚动而滚动          |
| background-size               | 背景大小     | cover显示更多，默认contain                                   |
| backdrop-filter: blur(像素);  | 模糊背景     | 11px等                                                       |
| font-size                     | 字体大小     | 尺寸单位                                                     |
| font-weight                   | 字体粗细     | normal/bold(加粗)/lighter(细)                                |
| font-style                    | 字体样式     | normal/italic(倾斜)                                          |
| font-family                   | 字体名称     | 字体名称，如Arial,"黑体"                                     |
| color                         | 文本颜色     | 颜色单位                                                     |
| text-decoration               | 文本装饰线   | none/underline(下划线)/line-throught(删除线)/overline(上划线) |
| text-align                    | 文本水平排列 | left(默认值)/center/right/justify(两端对齐)                  |
| vertical-align                | 文本垂直排列 | 同上                                                         |
| line-height                   | 文本行高     | line-height = height文本垂直居中，> height 文本下移， < height 文本靠上显示 |
| text-transform                | 文本转换     | uppercase(转大写)/lowercase(转小写)/capitalize(手写字母大写) |
| text-indect                   | 首行缩进     | 尺寸单位                                                     |
| letter-spacing                | 字符间隔     | 尺寸单位                                                     |
| white-space                   | 禁用文字环绕 | nowrap                                                       |
| text-shadow                   | 文字阴影效果 | 2px 2px 颜色，尺寸表示水平垂直偏移量                         |
| list-style-type               | 列表表项标记 | none/circle/decimal/square/upper-alpha/lower/alpha           |
| list-style-image              | 列表表项图片 | url("路径")                                                  |
| border                        | 边框         | 宽度 样式 颜色，用空格隔开，如 1px solid red(1像素实线边框)  |
| border- top/right/bottom/left | 单边框       | 同上                                                         |
| border-radius                 | 圆角边框     | 像素值或百分比，50%为圆形，最多取4个值，顺时针，取两个值代表对角线 |
| border-style                  | 边框风格     | none/dotted/dashed/solid/double/hidden/混合边框（同时写多个） |
| padding                       | 内边距       | 常用像素值，调整元素内容与边框之间的距离，取值逻辑同border-radius |
| padding-top/right/bottom/left | 单方向内边距 | 取一个值                                                     |
| margin                        | 外边距       | 调整元素与元素之间的距离，取值逻辑同border-radius            |
| margin-top/right/bottom/left  | 单方向外边距 | 取一个值                                                     |
| cursor                        | 鼠标指针样式 | pointer光标呈现为指示链接的指针                              |
| transition                    | 过渡效果     | 取多个值，如width 2s linear，如all 0.5s                      |

### 2.4 盒模型

#### 2.4.1 布局

* 可见内容：最里层内容框 + padding + border

![](https://tva4.sinaimg.cn/large/006bmdycgy1gzol6u9nnkj30h40iigp8.jpg)

#### 2.4.2 内容框

* 般情况下，为元素设置width/height，指定的是内容框的大小（除非指定box-sizing=border-box，此时如果指定width=100%表示自己的内容框+内边距+外边框的大小）
* 当宽高设置成百分比时，父元素必须被设置大小
* 设定尺寸：

```css
width: 宽度;						/* 像素或百分比 */
height: 高度;
line-height: 行高;
max/min-width/height 尺寸;		/* 最大或最小尺寸 */
```

* 内容溢出：内容超出元素的尺寸范围，称为溢出。默认情况下溢出部分仍然可见，可以使用overflow调整溢出部分的显示,取值如下：

```css
voerflow: visible;					/* 1 */
voerflow: hidden;					/* 2 */
voerflow: scroll;					/* 3 */
voerflow: auto;						/* 4 */
```

| 序号 | 作用                           |
| ---- | ------------------------------ |
| 1    | 默认值，溢出部分可见           |
| 2    | 溢出部分隐藏                   |
| 3    | 强制在水平和垂直方向添加滚动条 |
| 4    | 自动在溢出方向添加可用滚动条   |

#### 2.4.3 内边距

* 属性：padding
* 作用：设定内容框到边框的距离
* 取值：

```yacas
20px;					一个值表示统一设置上右下左
20px 30px;				两个值表示分别设置(上下) (左右)
20px 30px 40px;			三个值表示分别设置上右下，左右保持一致
20px 30px 40px 50px;	表示分别设置上右下左
```

#### 2.4.4 边框

* 统一设定边框：

```css
border: 宽度 风格 颜色;												/* 定义四周边框 */
```

* 仅设定单边框：

```css
border-top/buttom/left/right[-style/color/width]: 宽度 风格 颜色;		/* 定义某条边框 */
```

* 仅设定边框风格：

```css
border-style: 风格 [风格 [风格 [风格]]];
```

* 风格：

| 风格   | 含义     |
| ------ | -------- |
| solid  | 实线边框 |
| dotted | 点线边框 |
| dashed | 虚线边框 |
| double | 双线边框 |
| hidden | 应藏边框 |

* 仅设定边框宽度：

```css
border-width: 宽度;
```

* 仅设定边框颜色：

```css
border-color: 颜色;
```

* 特殊用法：制作三角图标、设置圆角边框等

#### 2.4.5 外边距

* 属性：margin
* 作用：调整元素与元素之间距离，是完全透明的
* 取值：

```yacas
margin: 0;							/* 取消所有外边距 */
margin: 0 auto;						/* 左右自动调整外边距，结果是在父元素里水平居中 */
margin: 上边距 右边距 下边距 左边距
margin: 上下外边距 左右外边距
margin-left/right/bottom/top: 值		/* 定义某边外边距 */
margin-left: -10px;					 /* margin取值为负数时 表示元素之间堆叠 文字的优先级最高 总是会显示 */
margin-bottom: 20%;					 /* 根据比例定义外边距 */
```

#### 2.4.6 轮廓

* 属性：outline
* 作用：outline（轮廓）是绘制于元素周围的一条线，位于边框边缘的外围，可起到突出元素的作用，不占空间
* 设置轮廓：

```css
outline: 颜色 风格 尺寸;	/* 风格同边框风格 */
```

#### 2.4.7 显示与可见性

```css
display: none; 						/* 1 */
display: block; 					/* 2 */
display: inline; 					/* 3 */
display: inline-block; 				/* 4 */
visibility: hidden;					/* 5 */
```

| 序号 | 含义               |
| ---- | ------------------ |
| 1    | 隐藏且不占据空间   |
| 2    | 设置成块元素       |
| 3    | 设置成行内元素     |
| 4    | 设置成行内块元素   |
| 5    | 隐藏但还会占据空间 |

### 2.5 布局方式

#### 2.5.1 默认布局方式

默认布局方式,按照代码书写顺序及标签类型从上到下，从左到右依次显示

#### 2.5.2 浮动布局

* 格式：

```css
float: left/right
```

* 特点：

  > 1. 元素设置成浮动布局，就具有块元素特征，可以手动设置宽高
  > 2. 浮动布局的元素会从原始位置脱流，向左或向右停靠在父元素边缘，且不再占位
  > 3. 浮动元素遮挡正常元素的位置，但无法遮挡文字内容的显示，呈现的效果是“文字环绕”
  > 4. 多个浮动元素停靠在同一位置时，根据浮动顺序展开排列，不会重叠，排列不下则会换行显示，类似于行内块性质

* 父元素高度为0：

  子元素全部设置成浮动后，下一个非浮动元素会显示在浮动元素下方，被遮挡无法正常显示，因为此时父元素高度为0

* 解决：

```
方法1：父元素末尾添加空的块元素（使用伪元素），并设置clear:both以清除浮动(推荐，因为整个css文件只用设置一次就好了)
方法2：对于内容固定不变的元素，如果子元素都浮动，可以给父元素设置高度（如网页顶部导航栏）
方法3：为父元素设置overflow:hidden
```

* 方法1代码举例

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>浮动布局</title>
    <style>
        .red{
            width: 200px;
            height: 200px;
            background-color: red;
            float: right;
        }
        .blue{
            width: 200px;
            height: 200px;
            background-color: blue;
            float: right;
        }
        span{
            width: 200px;
            height: 200px;
            background-color: chartreuse;
            /* 行内元素设置了浮动布后局 可以手动调整宽高 */
            float: right;
        }
        p{
            height: 400px;
            background-color: green;
        }
        
        /* 伪元素 */
        .wrapper::after{
            content: "";
            display: block;
            clear: both;
        }
    </style>
</head>
<body>
    
    <div class="wrapper">
        <div class="red"></div>
        <div class="blue"></div>
        <span></span>
    </div>
    <p></p>

</body>
</html>
```

* 方法2举例

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        body,ul{
            /* 取消body自带的8px外边距 */
            /* 取消ul自带的上下外边距 */
            margin: 0;
            /* 取消ul自带的左内边距 */
            padding: 0px;
        }
        ul{
            /* 这里设置了浮动元素的父元素的高度 解决了浮动布局父元素高度为0的问题 */
            height: 64px;
            /* line-height=height保证文字在ul内居中 */
            line-height: 64px;
            font-size: 32px;
            background-color: rgb(204, 192, 161);
            /* 取消自带的列表前缀 */
            list-style-type: none;
        }
        li{
            /* 将无序列表所有li标签全部置为浮动布局 */
            float: left;
        }
        .first{
            color: rgb(124, 42, 42);
            padding-left: 20px;
        }
        .item{
            margin-left: 50px;
            padding: 0px 20px;
        }
        .active, .item:hover{
            background-color: red;
            cursor: pointer;
        }

    </style>
</head>
<body>
    
    <ul>
        <li class="first">难度:</li>
        <li class="item">全部</li>
        <li class="item">初级</li>
        <li class="item active">中级</li>
        <li class="item">高级</li>
    </ul>

    <script>
        var items = document.getElementsByClassName("item");
        // 为所有可点击的li添加点击事件
        for(var i=0;i<items.length;i++){
            items[i].onclick = function(){
                document.getElementsByClassName("item active")[0].className = "item";
                this.className = "item active"; 
            };
        };
    </script>
</body>
</html>
```

### 2.6 元素定位

元素定位检查方法是查看position属性

![](https://tvax1.sinaimg.cn/large/006bmdycgy1gzoof35ztqj306805igmi.jpg)

#### 2.6.1 相对定位

* 效果：参照元素在文档中的原始位置进行偏移，不脱离文档流（占了原来位置，又坐到别的位置上了）
* 应用：通常用作位置微调使用，因为大幅度移动会在原始位置留下大面积空白，影响后续所有元素布局
* 格式：

```css
position: relative;
top: 100px;		/* 向下移动100px 顶部留下100px的空白 */
left: -100px;	/* 向左移动100px 右侧留下100px的空白 */
```

* 不脱离文档流：

  > 1. 若不设置相对定位，红色的div和蓝色的p垂直不重叠排列
  > 2. 相对定位后，蓝色的div元素向下移动了100px，但是紧接着的红色p标签不会移动（因为相对定位不脱离文档流）
  > 3. 蓝色的div会覆盖红色的p（定位的元素覆盖未定位的元素）

* 代码：

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>相对定位</title>
    <style>

        div{
            position: relative;
            width: 200px;
            height: 200px;
            background: blue;
            top: 100px;
        }
        p{
            margin: 0;
            padding: 0;
            width: 400px;
            height: 400px;
            background: red;
        }
    </style>
</head>
<body>
    
    <div></div>
    <p></p>

</body>
</html>
```

* 效果图：

<img src="https://tva1.sinaimg.cn/large/006bmdycgy1gzooz8ewxoj307q0bi3yg.jpg" style="zoom:80%;" />

#### 2.6.2 绝对定位

* 效果：根据已定位的最近祖先元素进行偏移，脱离文档流（绝对将规矩，不乱占位置），能够交叠元素
* 格式：

```css
position: absolute;
right: 0px; 			/* 脱流并移动到已定位的父元素右侧边缘 */
bottom 0px;				/* 脱流并移动到已定位的父元素下侧边缘 */
```

* 特点：

  > 1. 若没有已定位的父元素，则参照窗口进行偏移
  > 2. 后定位覆盖先定位的（代码顺序）

* 代码示例：

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>绝对定位</title>
    <style>
        p{
            margin: 0;
        }
        .parent{
            position: relative;
            width: 300px;
            height: 300px;
            border: 1px solid red;
            /* 调整外边距使其居中页面 */
            margin: 0 auto;
            overflow: hidden;
        }
        .chidren{
            /* 绝对定位参照物 距离最近的 已经定位的 祖先元素 */
            /* 如果没有参照物 参照窗口进行偏移 */
            position: absolute;
            bottom: 0px;
            right: 0px;
            width: 100px;
            height: 100px;
            background: aqua;
            /* margin: 200px 0 0 200px; */
        }
        #title{
            position: absolute;
            background-color: rgba(99, 0, 0, 0.5);
            height: 25px;
            line-height: 25px;
            /* 内容框宽度为父元素100%，但是左内边距右增加了10px，导致自身元素总显示宽度超出父元素了 
            在父元素设置overflow: hidden来解决*/
            padding-left: 10px;
            width: 100%;
            bottom: 0px;
        }
    </style>
</head>
<body>
    <div class="parent">
        <div class="chidren"></div>
        <!-- hello world在parent的左上角显示 表示绝对定位是脱流的 -->
        hello world
        <p id="title">三星工厂再陷倒闭风波</p>
    </div>
</body>
</html>
```

* 效果图：

![](https://tva1.sinaimg.cn/large/006bmdycgy1gzoppggwzzj308q08qdg6.jpg)


#### 2.6.3 固定定位

* 效果：相对窗口进行定位，脱离文档流（跟着网页动的<del>小广告</del>～）
* 代码示例：

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>固定定位</title>
    <style>
        #d1{
            /* 参照物 窗口 */
            /* 定位了固定定位 就会从原来布局中脱流 不占体积 */
            position: fixed;
            right: 8px;
            /* 以下两行代码设置该元素垂直居中 */
            top: 50%;
            margin-top: -150px;
            width: 180px;
            height: 300px;
            background-color: red;
        }
        p{
            height: 1000px;
        }
        .p1{
            background-color: hotpink;
        }
        .p2{
            /* 如果设置了相对定位，则会覆盖固定定位的元素 因为同为定位元素 后来居上*/
            position: relative;
            background-color: indigo;
        }
    </style>
</head>
<body>
    <div id="d1"></div>
    <p class="p1"></p>
    <p class="p2"></p>
</body>
</html>
```

* 效果图：

![](https://tva4.sinaimg.cn/large/006bmdycgy1gzoq9yodwdj30cq0g0q36.jpg)

### 2.7 堆叠次序

元素发生堆叠时可以使用 z-index 属性调整已定位元素的显示位置，值越大元素越靠上：

+ 属性 : z-index
+ 取值 : 无单位的数值,数值越大,越靠上
+ 堆叠：定位>未定位，后来居上（定位顺序）

### 2.8 Font Awesome 图标

* 网址：https://www.runoob.com/font-awesome/fontawesome-tutorial.html
* 添加国内CDN：

```HTML
<link rel="stylesheet" href="https://cdn.staticfile.org/font-awesome/4.7.0/css/font-awesome.css">
```

* 使用图标：

```html
<i class="fa fa-图标名" ></i>
```

* 动态旋转图标：

```html
<i class="fa fa-图标名 fa-spin" ></i>
<i class="fa fa-spinner fa-pulse"></i>
```

* 放大图标：

```html
<i class="fa fa-图标名 fa-spin fa-数字x" ></i>
```

* 堆叠图标：

```html
<span class="fa-stack fa-2x">
  <i class="fa fa-camera fa-stack-1x"></i>
  <i class="fa fa-ban fa-stack-2x" style="color:red;"></i>
</span>
```

* 静态旋转图标：

```html
<i class="fa fa-shield fa-rotate-度数"></i>
```

* 翻转图标

```html
<i class="fa fa-shield fa-flip-horizontal/virtical"></i>
```

### 2.9 媒体查询

* 格式：

```css
@media screen and (条件){
    当条件成立时的css内容;
}
```

* 举例：

```css
@media screen and (max-width: 600px){
	.header{
		width: 100%;
	}
}
/* 表示当浏览器屏幕宽度小于600时 将header类的宽度置为已定位好的父元素宽度的100% */
```

### 2.10 计数器

* 创建计数器（<font color="orange">注意不能写在伪类中</font>）：

```css
counter-reset: 计数器名;
```

* 递增计数器：

```css
注意不能写在伪类中counter-increment: 计数器名;
```

* 插入关于计数器文本：

```css
元素::after|before{
    countent: "这是第" counter(计数器名) "次计数";
}
```

* 计数器嵌套

```html
<!DOCTYPE html>
<head>
  <meta charset="UTF-8">
  <title>嵌套计数器</title>
  <style>
    body{
      counter-reset: l1-counter;
    }
    h1{
      counter-reset: l2-counter;
    }
    h1::before{
      counter-increment: l1-counter;
      content: "Section" counter(l1-counter) ".";
    }
    h2::before{
      counter-increment: l2-counter;
      content: "Section" counter(l1-counter) "." counter(l2-counter);
    }
  </style>
</head>
<body>

    <h1>HTML</h1>
    <h2>item</h2>
    <h2>item</h2>

    <h1>CSS</h1>
    <h2>item</h2>
    <h2>item</h2>

    <h1>JS</h1>
    <h2>item</h2>
    <h2>item</h2>
</body>
</html>
```

### 2.11 CSS3新特性

#### 2.11.1 盒阴影

* 格式：

```css
box-shadow: 横坐标偏移量 纵坐标偏移量 模糊距离 颜色;
```

#### 2.11.2 图片边框

* 格式：

```css
border-image: url("图片链接") 图片宽度 图片高度 strenth|round;
```

#### 2.11.3 图片背景

* 格式：

```css
background: ulr("图片链接") 位置1 位置2 重复策略[,ulr("图片链接") 位置1 位置2 重复策略...];
```

* 位置参数：

```
top|right|bottom|left
```

* 重复参数：

```
repeat|no-repeat
```

* 设置大小：

```css
background-size: 宽度尺寸单位 长度尺寸单位|cover;
```

* 设置图片实际位置：

```css
backgound-origin: content-box|padding-box|border-box;
```

#### 2.11.4 文本效果

*  文本阴影

```css
text-shadow: 水平阴影 垂直阴影 模糊 [阴影尺寸] 颜色;
```

* 盒子阴影

```css
box-shadow: 水平阴影 垂直阴影 模糊 [阴影尺寸] 颜色;
```

* 文本溢出处理

```css
text-overflow: ellipsis|clip|"自定义字符串"; /* 以...结尾|直接截取|以自定义字符串结尾 */
```

前提是溢出了且设置overflow: hidden;

#### 2.11.5 自定义字体

```css
@font-face{							 /* 创建自定义字体 */
    font-family: 自定义字体变量名;
    src: url("字体链接");
}
p{
    font-family: 自定义的字体变量名;		/* 使用字体 */
}
```

#### 2.11.6 2D转换

* 定义参考点：

```css
transform-origin: x坐标 y坐标;				 /* 默认以自身为参考点 */
```

* 旋转（顺时针旋转数字度）：

```css
transform: rotate(数字deg);	
```

* 移动（相当于相对定位）：

```css
transform: translate(x轴移动距离,y轴移动距离);
```

* 缩放

```css
transform: scale(x轴移动距离,y轴移动距离);
transform: scaleX(x轴移动距离);
transform: scaleY(y轴移动距离);
```

* 倾斜

```css
transform: skew(x轴倾斜角度,y轴倾斜角度);
```

* 统一2D转换

```css
transform: matrix(旋转参数,缩放参数,移动参数,倾斜参数);
```

#### 2.11.7 3D转换

<table class="reference">
<tbody><tr>
<th style="width:25%">函数</th>
<th>描述</th>
</tr><tr>
<td>matrix3d(<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<br><i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>)</td>
<td>定义 3D 转换，使用 16 个值的 4x4 矩阵。</td>
</tr><tr>
<td>translate3d(<i>x</i>,<i>y</i>,<i>z</i>)</td>
<td>定义 3D 转化。</td>
</tr><tr>
<td>translateX(<i>x</i>)</td>
<td>定义 3D 转化，仅使用用于 X 轴的值。</td>
</tr><tr>
<td>translateY(<i>y</i>)</td>
<td>定义 3D 转化，仅使用用于 Y 轴的值。</td>
</tr><tr>
<td>translateZ(<i>z</i>)</td>
<td>定义 3D 转化，仅使用用于 Z 轴的值。</td>
</tr><tr>
<td>scale3d(<i>x</i>,<i>y</i>,<i>z</i>)</td>
<td>定义 3D 缩放转换。</td>
</tr><tr>
<td>scaleX(<i>x</i>)</td>
<td>定义 3D 缩放转换，通过给定一个 X 轴的值。</td>
</tr><tr>
<td>scaleY(<i>y</i>)</td>
<td>定义 3D 缩放转换，通过给定一个 Y 轴的值。</td>
</tr><tr>
<td>scaleZ(<i>z</i>)</td>
<td>定义 3D 缩放转换，通过给定一个 Z 轴的值。</td>
</tr><tr>
<td>rotate3d(<i>x</i>,<i>y</i>,<i>z</i>,<i>angle</i>)</td>
<td>定义 3D 旋转。</td>
</tr><tr>
<td>rotateX(<i>angle</i>)</td>
<td>定义沿 X 轴的 3D 旋转。</td>
</tr><tr>
<td>rotateY(<i>angle</i>)</td>
<td>定义沿 Y 轴的 3D 旋转。</td>
</tr><tr>
<td>rotateZ(<i>angle</i>)</td>
<td>定义沿 Z 轴的 3D 旋转。</td>
</tr><tr>
<td>perspective(<i>n</i>)</td>
<td>定义 3D 转换元素的透视视图。</td>
</tr>
</tbody></table>	
#### 2.11.8 过渡

第一步：在原始元素添加`transition属性`

第二步：在触发条件中添加`transform属性`

* `transition属性`

```css
div{
    transition: 指定要变化的属性=all 动画时长=0 过渡时间曲线=ease 延迟触发时长=0;
}
```

* `transform属性`

```css
div:hover{			/* 假如是鼠标移入时触发 */
    transform: 转换参数1[,转换参数2...];
}
```

* 举例

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>过渡</title>
    <style>
        *{
            box-sizing: border-box;
        }
        div#transition-demo{
            width: 100px;
            height: 50px;
            line-height: 50px;
            background-color: darkolivegreen;
            color: white;
            text-align: center;
            border-radius: 6px;
            transition: all 1s ease 0.5s;
        }
        div#transition-demo:hover{
            transform: translateX(100px) translateY(100px) rotate(360deg) scale(1.5);
        }
    </style>
</head>
<body>
    <div id="transition-demo">
        CSS3过渡
    </div>
</body>
</html>
```

#### 2.11.9 动画

一、在原元素添加`animation属性`

二、添加动画`@keyframes`

* `animation`

```css
div{
    animation: 动画名 一周期持续时间=0 速度曲线=ease 延迟开始时间=0 播放次数=0 下周期是否逆播放=normal;
    /*                               ease|linear            infinite|1..    normal|alternate*/
}
```

* `keyframes`

```css
@keyframes 自定义动画名{
    [0%|from {初始效果}]
    [25% {中间过程1}]
    [50% {中间过程2}]
    [75% {中间过程3}]
    100%|to {最终效果}
}
```

* 举例

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>动画</title>
    <style>
        div{
            width: 100px;
            height: 100px;
            position: relative;
            background-color: rosybrown;
            animation: my-animation 5s ease 2s infinite alternate ;
        }
        @keyframes my-animation{
            0% {
                background-color: rosybrown; left: 0px; top: 0px ;
            }
            25% {
                background-color: steelblue; left: 200px; top: 0px ;
            }
            50% {
                background-color: rosybrown; left: 200px; top: 200px ;
            }
            75% {
                background-color: steelblue; left: 0px; top: 200px ;
            }
            100% {
                background-color: rosybrown; left: 0px; top: 0px ;
            }
        }
    </style>
</head>
<body>
    <div class="animation-demo"></div>
</body>
</html>
```

#### 2.11.10 多列

* 分成多列

```css
column-count: 列数;
```

* 列间隙宽度

```css
column-gap: 宽度;
```

* 列边框

```css
column-rule: 宽度 样式 颜色;
```

* 指定列宽

```css
column-width: 宽度;
```

* 跨列

```css
column-span: all;
```

#### 2.11.11 弹性盒子

效果：在容器定义了弹性属性后，其子元素会根据空白适配不同大小屏幕，来达到正确的显示

更多：http://c.biancheng.net/css3/flex.html

* 定义为弹性容器

```css
display: flex;
```

* 定义子元素展开方向

```css
flex-direction: row|row-reverse|column|column-reverse;
```

* 定义内容水平对齐方式

```css
justify-content: flex-start | flex-end | center | space-between | space-around
```

![](https://www.runoob.com/wp-content/uploads/2016/04/2259AD60-BD56-4865-8E35-472CEABF88B2.jpg)

* 定义内容垂直对齐方式

```css
align-items: flex-start | flex-end | center | baseline | stretch
```

* 定义内容换行方式

```css
flex-wrap: nowrap|wrap|wrap-reverse|initial|inherit; /*默认共行*/
```

* 指定子元素如何分配空间（在子元素上）

```css
flex: auto | initial | none | inherit |  [ flex-grow ] || [ flex-shrink ] || [ flex-basis ]
/*                                           扩展比率（数字）   收缩比率（数字）    默认基准值（百分比或px）*/
```

```css
flex: 1 100%;	/* 表示子元素水平均匀布满 不留空隙 */
```

* 举例：通过弹性盒子设置元素水平垂直居中

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>弹性盒子实现居中</title>
    <style>
        div.flex-box{
            display: flex;
            align-items: center;
            justify-content: center
            background-color: lightgray;
            width: 400px;
            height: 400px;
        }
        div.flex-box>div{
            width: 100px;
            height: 100px;
            background-color: rosybrown;
        }
    </style>
</head>
<body>
    <div class="flex-box">
        <div>CSS</div>
    </div>
</body>
</html>
```



#### 2.11.12 网格布局

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>网格布局</title>
    <style>
        div.grid-container{
            display: grid;
            grid: 
            'a a a a a a'
            'b c c d d d'
            'b e e e e e';
            
        }
        div.grid-container>div{
            text-align: center;
            border: 1px solid black;
            /* 解决边框重叠变粗问题 */
            margin-bottom: -1px;
            margin-right: -1px;
        }
        .a-area{grid-area: a}
        .b-area{grid-area: b}
        .c-area{grid-area: c}
        .d-area{grid-area: d}
        .e-area{grid-area: e}
    </style>
</head>
<body>
    <div class="grid-container">
        <div class="a-area">A area</div>
        <div class="b-area">B area</div>
        <div class="c-area">C area</div>
        <div class="d-area">D area</div>
        <div class="e-area">E area</div>
    </div>
</body>
</html>
```

#### 2.11.13 渐变色

* 线性渐变：

```css
background: linear-gradient(角度|方向, color-stop1 [开始位置%], color-stop2[开始位置%]...);
```

* 重复的线性渐变：

```css
background: repeating-linear-gradient(角度|方向, color-stop1 开始位置（百分比）, color-stop2开始位置（百分比）);
```

* 中心渐变：

```css
background: radial-gradient(颜色 [开始位置%] [颜色[开始位置%]]...)
```

不写开始位置（百分比形式）表示均匀渐变

* 示例

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <title>渐变色</title>
    <style>
        div#gradient-demo{
            width: 100%;
            height: 200px;
            /* background: linear-gradient(#e66465, #9198e5); */
            /* background: linear-gradient(to right, #e66465, #9198e5); */
            /* background: linear-gradient(to bottom right, #e66465, #9198e5, yellow); */
            background: linear-gradient(to right, #e66465 0%, #9198e5 30%);
        }
    </style>
</head>
<body>
    
    <div id="gradient-demo"></div>

</body>
</html>
```

### 2.n 综合案例

#### 2.n.1 导航栏01

* 效果图（媒体查询+浮动布局+固定定位）

![](https://tvax2.sinaimg.cn/large/006bmdycgy1gzppybjpphj31800lujzs.jpg)

![](https://tva4.sinaimg.cn/large/006bmdycgy1gzszd12o13j30rq0oaaem.jpg)

* 代码

```html
<!DOCTYPE html>
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>浮动布局</title>
    <style>
       /* ================================================================== */
       /* 初始化 */
       /* ================================================================== */
       body{
           margin: 0px;
       }
       ul{
           margin: 0px;
           padding: 0px;
           list-style-type: none;
       }
       .clearfix::after{
            content: "";
            display: block;
            clear: both;
       }
       /* 因为有padding存在 并且设置了水平排列元素宽度分别为25%和75% 所以为了共行显示 将box-sizing重新设置 */
       * {
           box-sizing: border-box;
       }
       /* ================================================================== */
       /* 设置顶部导航栏 */
       /* ================================================================== */
       ul.top_menu{
           background-color: #777;
       }
       ul.top_menu>li{
           float: left;
       }
       ul.top_menu>li:last-child{
           float: right;
       }
        /* 当屏幕宽度小于600px时 执行如下代码（注意@media里面的过滤器必须在css中出现过 否则不起作用） */
       @media screen and (max-width: 600px) {
            ul.top_menu>li, ul.top_menu>li:last-child{
                float: none;
            }
       }
       ul.top_menu a{
           display: block;
           color: white;
           padding: 16px;
           text-decoration: none;
           text-align: center;
       }
       ul.top_menu a.active{
           background-color: green;
       }
       ul.top_menu a:not(.active):hover{
           background-color: #333;
       }
       /* ================================================================== */
       /* 设置左侧导航栏 */
       /* ================================================================== */
       div.column{
           float: left;
           padding: 15px;
       }
       div.sidemenu{
           width: 25%;
       }
       div.sidemenu>ul>li>a{
           display: block;
           margin-bottom: 4px;
           padding: 8px;
           background-color: #eee;
           text-decoration: none;
           color: #666;
       }
       div.sidemenu a:hover,div.sidemenu a.active{
           background-color: #008CBA;
           color: #fff;
       }
       /* ================================================================== */
       /* 设置内容框 */
       /* ================================================================== */
       div.content{
           width: 75%;
       }
       div.title{
           background-color: #2196F3;
           color: white;
           text-align: center;
           padding: 15px;
       }   
       /* ========================= 设置底部 ========================= */
       .foot{
            text-align: center;
            background-color: #bbb;
       }
    </style>
</head>
<body>
    
   <ul class="top_menu clearfix">
       <li><a href="#home" class="active">主页</a></li>
       <li><a href="#news">新闻</a></li>
       <li><a href="#contact">联系我们</a></li>
       <li><a href="#about">关于我们</a></li>
   </ul>

   <div class="clearfix">

       <div  class="column sidemenu">
           <ul>
               <li><a href="#flight">The Flight</a></li>
               <li><a href="#city" class="active">The City</a></li>
               <li><a href="#island">The Island</a></li>
               <li><a href="#food">The People</a></li>
               <li><a href="#history">The History</a></li>
               <li><a href="#oceans">The Oceans</a></li>
           </ul>
       </div>

       <div class="column content">
            <div class="title">
                <h1>The City</h1>
            </div>
            <h2>China</h2>
            <p>Chania is the capital of the Chania region on the island of Crete. 
                The city can be divided in two parts, the old town and the modern city.
            </p>
            <p>You will learn more about responsive web pages in a later chapter.</p>
       </div>

   </div>

    <div class="foot">
        <p>底部文本</p>
    </div>

</body>
</html>
```

#### 2.n.2 导航栏02

* 效果图（媒体查询+浮动布局+固定定位）

![](https://tvax2.sinaimg.cn/large/006bmdycgy1gzt4zvbk2ej315g0fkjzc.jpg)

![](https://tva3.sinaimg.cn/large/006bmdycgy1gzt50a1p4gj31ca0c4qat.jpg)

![](https://tva2.sinaimg.cn/large/006bmdycgy1gzt58bkdvnj30hg0ggq6l.jpg)

* 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
       /* ================================================================== */
       /* 初始化 统一风格设定 */
       /* ================================================================== */
        body,ul,li{
            margin: 0px;
            padding: 0px;
            list-style-type: none;
            color: black;
        }
        ul.clearfix::after{
            content: "";
            display: block;
            clear: both;
        }
       /* ================================================================== */
       /* 导航栏 */
       /* ================================================================== */
        ul.navigation{
            position: fixed;
            background-color: #ddd;
            width: 25%;
            height: 100%;
        }
        ul.navigation>li{
            float: none;
        }
        a.l1-menu{
            display: block;
            text-decoration: none;
            padding: 16px 10px;
            color: black;
        }
        ul.navigation>li>a.active{
            background-color: green;
            color: white;
        }
        /* 注意:hover伪类覆盖无效 所以要细分到具体元素 不能被后面代码重写 */
        ul.navigation>li>a:not(.active):hover{
            background-color: #bbb;
        }
        div.content{
            margin-left: 25%;
            padding: 5px;
        }
       /* ================================================================== */
       /* 下拉菜单 */
       /* ================================================================== */
       /* 设置相对定位后 隐藏的菜单栏才能更好使用绝对定位 且才能用width=100% 控制下拉菜单长度和下拉按钮一致 */
        li.pull-down-menu{
            position: relative;
        }
        .pull-down-menu>ul{
            display: none;
            position: absolute;
            background-color: rgb(162, 171, 180);
            font-size: 0.5em;
            box-shadow:0px 8px 16px 0px rgba(0,0,0,0.2);
            width: 100%;
        }
        a.l2-menu{
            display: block;
            text-decoration: none;
            color: black;
            border-bottom: 1px solid #999;
            padding: 10px 10px;
        }
        a.l2-menu:hover{
            background-color: rgb(66, 77, 77);
            color: white;
        }
        /* 当鼠标悬停在下来菜单时 显现下拉菜单ul */
        .pull-down-menu:hover>ul{
            display: block;
        }
        /* 当鼠标悬停在下来菜单内部时 该菜单栏颜色需要保持不变 */
        .pull-down-menu:hover>a.l1-menu{
            background-color: #bbb;
        }
        ul.navigation a{
            text-align: left;
        }
       /* ================================================================== */
       /* 适配不同屏宽 */
       /* ================================================================== */
        @media screen and (max-width: 900px) {
            ul.navigation{
                position: static;
                width: auto;
                height: auto;
            }
            ul.navigation>li{
                float: left;
            }
            ul.navigation>li:last-child{
                float: right;
            }
            div.content{
                margin-left: auto;
            }
            /* 控制下来菜单在不同布局下宽度 */
            .pull-down-menu>ul{
                right: 0px;
            }
            a.l1-menu{
                padding: 10px 16px;
                text-align: center;
            }
            a.l2-menu{
                padding: 10px 5px;
                text-align: center;
            }
        }
        @media screen and (max-width: 400px) {
            ul.navigation>li,ul.navigation>li:last-child{
                float: none;
            }
            ul.navigation a{
                text-align: center;
            }
        }
    </style>
</head>
<body>
    <ul class="navigation clearfix">
        <li><a href="#home" class="active l1-menu">主页</a></li>
        <li><a href="news" class="l1-menu">新闻</a></li>
        <li><a href="contact" class="l1-menu">联系</a></li>
        <li class="pull-down-menu">
            <a href="#" class="l1-menu">我的</a>
            <ul>
                <li><a href="" class="l2-menu">个人主页</a></li>
                <li><a href="" class="l2-menu">浏览记录</a></li>
                <li><a href="" class="l2-menu">异常反馈</a></li>
            </ul>
        </li>
    </ul>
    <div class="content">
        <h2>这是一个响应式导航栏</h2>
        <ul>
            <li>width&lt;400时 导航栏顶部居中布局(F12开发工具中选中手机端查看效果)</li>
            <li>400&le;width&lt;900时顶部水平布局</li>
            <li>900&le;width时局左固定布局</li>
            <li>导航栏"关于"是一个下拉菜单 并且对不同屏宽做了针对性优化</li>
        </ul>
    </div>
</body>
</html>
```

#### 2.n.3 导航栏03

* 效果图

![](https://tvax1.sinaimg.cn/large/006bmdycgy1gzt9b7dhp4j30t20cu0t7.jpg)

* 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>图标导航栏</title>
    <link rel="stylesheet" href="https://cdn.staticfile.org/font-awesome/4.7.0/css/font-awesome.css">
    <style>
        body,ul,a{
            margin: 0px;
            padding: 0px;
            list-style-type: none;
            text-decoration: none;
            color: black;
        }
        .fixclear::after{
            content: "";
            display: block;
            clear: both;
        }
        ul.navigation-bar{
            background-color: #777;
        }
        ul.navigation-bar>li{
            float: left;
            width: 20%;
        }
        ul.navigation-bar>li>a{
            display: block;
            text-align: center;
            font-size: 36px;
            padding: 10px;
            border-right: 1px solid #666;
        }
        ul.navigation-bar .active,ul.navigation-bar a:hover{
            color: white;
            background-color: darkseagreen;
        }

    </style>
</head>
<body>
    
    <ul class="navigation-bar fixclear">
        <li><a class="fa fa-home active" href=""></a></li>
        <li><a class="fa fa-search" href=""></a></li>
        <li><a class="fa fa-envelope" href=""></a></li>
        <li><a class="fa fa-pencil" href=""></a></li>
        <li><a class="fa fa-cog" href=""></a></li>
    </ul>

</body>
</html>
```

#### 2.n.4 提示框01

* 效果

![](https://tva1.sinaimg.cn/large/006bmdycgy1gztgc2y5njj30hq0aoq44.jpg)

* 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>提示框动画效果</title>
    <style>
        body{
            margin: 0px;
            padding: 0px;
        }
        div.tip-menu{
            position: relative;
            background-color: grey;
            color: white;
            text-align: center;
            width: 200px;
            padding: 10px;
            margin: 100px 40px 0px 40px;
        }
        div.tip-menu>i{
            position: absolute;
            display: block;
            opacity: 0;
            font-style: normal;
            font-weight: bold;
            color: white;
            background-color: cadetblue;
            padding: 10px;
            top: -55px;
            width: 250px;
            left: -25px;
            transform: translateY(15px);
            transition: all 0.3s ease-out;
        }
        div.tip-menu>i::after{
            position: absolute;
            content: "";
            display: block;
            width: 0px;
            height: 0px;
            border: 10px solid;
            border-color: cadetblue transparent transparent transparent;
            bottom: -20px;
            left: 115px;
        }
        div.tip-menu:hover i{
            opacity: 1;
            transform: translateY(0px);
        }
    </style>
</head>
<body>
    <div class="tip-menu">移动鼠标以弹出提示框
        <i>这是一个个动态提示框</i>
    </div>
</body>
</html>
```

#### 2.n.5 表单01

* 效果图

![](https://tva1.sinaimg.cn/large/006bmdycgy1gzti4ansjuj30zc0lwq4p.jpg)

* 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS渲染表单</title>
    <style>
        div.login-form{
            position: fixed;
            padding: 20px;
            width: 400px;
            border-radius: 5px;
            background-color: #f2f2f2;
            top: 50%;
            margin-top: -165.75px;
            left: 50%;
            margin-left: -200px;
            box-sizing: border-box;
        }
        label,input,select{
            display: block;
        }
        input[type=text],input[type=password],select{
            /* box-sizing: border-box指的是width=100%时这个width不仅仅是默认的内容框，还要加上内边距 */
            box-sizing: border-box;
            width: 100%;
            padding: 12px 20px;
            margin: 8px 0;
            border: 1px solid #ccc;
            border-radius: 4px;
            
        }
        input[type=text]:focus,input[type=password]:focus,select:focus{
            background-color: lightblue;
        }
        input[type=submit]{
            width: 100%;
            background-color: #4CAF50;
            color: white;
            padding: 14px 20px;
            margin: 8px 0;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        input[type=submit]:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    
    <div class="login-form">
        <form action="">
            <label for="username">用户名:</label>
            <input type="text" id="username" name="username" placeholder="用户名">
            <label for="password">密码:</label>
            <input type="password" id="password" name="password" placeholder="密码">
            <label for="address">地址:</label>
            <select name="address" id="address">
                <option value="anhui">安徽</option>
                <option value="jiangsu">江苏</option>
            </select>
            <input type="submit" value="Login" name="submit">
        </form>
    </div>

</body>
</html>
```

#### 2.n.6 弹性盒子

* 效果图

![](https://tva3.sinaimg.cn/large/006bmdycgy1gzug16za66j30r209aafp.jpg)

![](https://tvax3.sinaimg.cn/large/006bmdycgy1gzug1mqb1mj30xm07mdlo.jpg)

![](https://tva2.sinaimg.cn/large/006bmdycgy1gzug231ebej31e4078ag2.jpg)

* 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>弹性盒子实现响应式布局</title>
    <style>
        .flex-container{
            display: flex;
            flex-wrap: wrap;
        }
        .flex-container>*{
            flex: 1 1 100%;
            text-align: center;
        }
        article{
            text-align: left !important;
        }
        header{background-color: rgb(170, 255, 13);}
        article{background-color: cyan;}
        .aside1{background-color: palegoldenrod;}
        .aside2{background-color: rgb(209, 152, 111);}
        footer{background-color: lightgreen;}
        @media screen and (min-width: 600px) {
            .aside{
                flex: 1 auto;
            }
        }
        @media screen and (min-width: 900px) {
            .article{
                flex: 3 0px;
            }
            .aside1{
                order: 1;
            }
            .article{
                order: 2;
            }
            .aside2{
                order: 3;
            }
            .footer{
                order: 4;
            }
        }
    </style>
</head>
<body>
    <div class="flex-container">
        <header>顶栏</header>
        <article class="article">内容栏Lorem ipsum dolor sit amet consectetur, adipisicing elit. Dolorum dolorem illo ea necessitatibus illum! Expedita, temporibus. Pariatur quos cum vero vel ut. Iste nemo beatae pariatur aperiam error! Recusandae, dolorum?</article>
        <aside class="aside aside1">边栏1</aside>
        <aside class="aside aside2">边栏2</aside>
        <footer class="footer">底栏</footer>
    </div>
</body>
</html>
```

## 3. JS

### 3.1 js基础

#### 3.1.1 json

* 从本地获取json

```js
let settings;
$.ajaxSetup({async:false});
$.getJSON("../json/settings.json",(data)=>{
    settings = data;
    $.ajaxSetup({async:true});
})
```

#### 3.1.2 regexp

* json中存储正则

```js
const obj = {
  "abbreviation": "/([A - Z])\w+/"
};

const stringified = JSON.stringify(obj);
const regex = new RegExp(JSON.parse(stringified).abbreviation.slice(1, -1));
console.log(regex);
```

* 密码正则

```js
```



## 4. 综合案例

### 4.1 轮播图

* 技术点：弹性盒子 + 动画效果
* 附件：

```json
[
    "https://tvax2.sinaimg.cn/large/006bmdycgy1gzx3dmaa30j30xe0isdpo.jpg",
    "https://tva4.sinaimg.cn/large/006bmdycgy1gzx3ff1p9ej30xe0is795.jpg",
    "https://tvax2.sinaimg.cn/large/006bmdycgy1gzx3fokfoqj30xe0istbl.jpg",
    "https://tvax2.sinaimg.cn/large/006bmdycgy1gzx3dmaa30j30xe0isdpo.jpg",
    "https://tva4.sinaimg.cn/large/006bmdycgy1gzx3ff1p9ej30xe0is795.jpg",
    "https://tvax2.sinaimg.cn/large/006bmdycgy1gzx3fokfoqj30xe0istbl.jpg",
    "https://tvax2.sinaimg.cn/large/006bmdycgy1gzx3dmaa30j30xe0isdpo.jpg",
    "https://tva4.sinaimg.cn/large/006bmdycgy1gzx3ff1p9ej30xe0is795.jpg",
    "https://tvax2.sinaimg.cn/large/006bmdycgy1gzx3fokfoqj30xe0istbl.jpg",
    "https://tvax2.sinaimg.cn/large/006bmdycgy1gzx3dmaa30j30xe0isdpo.jpg"
]
```

* 代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>review slide</title>
    <script src="https://cdn.staticfile.org/jquery/1.8.3/jquery.min.js"></script>
    <style>
        *{
            margin: 0px;
            padding: 0px;
            list-style-type: none;
            box-sizing: border-box;
        }
        body{
            display: flex;
            height: 100vh;
            background-color: #71abe3;
        }
        div.slide{
            position: relative;
            display: flex;
            align-items: center;
            margin: auto;
            width: 50%;
            border: 5px solid white;
        }
        ul.slide-button{
            position: absolute;
            display: flex;
            width: 100%;
            height: 0px;
            align-items: center;
            justify-content: space-between;
        }
        ul.slide-button>li{
            font-size: 3em;
            font-weight: bold;
            background-color: rgba(0, 0, 0, 0.2);
            cursor: pointer;
            color: white;
        }
        ul.slide-button>li:hover{
            background-color: rgba(0, 0, 0, 0.4);
        }
        ul.images{
            position: absolute;
            display: flex;
            height: 100%;
            width: 100%;
        }
        ul.images>li{
            position: absolute;
            width: 100%;
            height: 100%;
            opacity: 0;
            transition: all 1s;
        }
        /* 回调css */
        .image{
            background-repeat: no-repeat;
            background-size: cover;
        }
        ul.min-images{
            position: absolute;
            display: flex;
            /* 定义子元素超出父元素宽度时自动折行 */
            flex-wrap: wrap;
            width: 100%;
            height: 100%;
            /* 垂直方向相邻布局 */
            align-content: flex-end;
            /* 这个是垂直方向均匀布局 勿混淆 */
            /* align-items: flex-end; */
            justify-content: center;
        }
        ul.min-images>li{
            width: 5%;
            /* 高度在js内修改 以保持正方形 */
            border: 2px solid white;
            border-radius: 50% 50%;
            margin: 5px;
        }
        ul.min-images>li:hover{
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="slide">
        <ul class="images">
            <!-- <li></li> -->
        </ul>
        <ul class="min-images">
            <!-- <li></li> -->
        </ul>
        <ul class="slide-button">
            <li>&lt;</li>
            <li>&gt;</li>
        </ul>
    </div>

    <script>
        // =======================================================
        // ======================底层函数=========================
        // =======================================================
        // 根据窗口大小、图片数量动态调整slide宽高函数
        function resizeSlide(imageCount){
            let slideWidth = parseInt($("div.slide").css("width"));
            $("div.slide").css("height", slideWidth*0.7);
            $("ul.min-images>li").css("height", $("ul.min-images>li").css("width"))
            // $("ul.images").css("width", imageCount + "00%")
        }
        // 载入图片
        function loadImage(imageJsonData){
            for(let i=0;i<imageJsonData.length;i++){
                let image = $("<li></li>");
                let j = "url(" + imageJsonData[i] + ")"
                image.css("background-image", j);
                image.addClass("image")
                $("ul.images").append(image);
            }
        }
        // 载入缩略图
        function loadMinImage(imageJsonData){
            for(let i=0;i<imageJsonData.length;i++){
                let image = $("<li></li>");
                let j = "url(" + imageJsonData[i] + ")"
                image.css("background-image", j);
                image.addClass("image")
                $("ul.min-images").append(image);
            }
        }
        // (loadImage)获取图片json数据
        function getImages(url){
            let imageJsonData;
            // getJSON是一个XHR请求 默认异步 但会导致外部不能获取到结果 所以改成同步
            $.ajaxSetup({async: false});    
            $.getJSON(url, (data)=>{
                imageJsonData = data;
                $.ajaxSetup({async: true});
            })
            return imageJsonData
        }
        // 轮播图移动逻辑
        function showSlideByIndex(i){
            let elements = $(".images>li");
            if(!elements[i]){
                return false;
            }else{
                for(let j=0;j<elements.length;j++){
                    if(i==j){
                        elements[j].style.opacity = 1;
                        $(".min-images>li")[j].style.borderColor = "#333";
                    }else{
                        elements[j].style.opacity = 0;
                        $(".min-images>li")[j].style.borderColor = "#fff";
                    }
                }
            }
        }
        // 轮播图右键
        function slideRight(slideIndex){
            slideIndex = slideIndex+1>=imagesCount?0:slideIndex+1
            showSlideByIndex(slideIndex);
            return slideIndex;
        }
        // 轮播图左键
        function slideLeft(slideIndex){
            slideIndex = slideIndex-1>=0?slideIndex-1:imagesCount-1;
            showSlideByIndex(slideIndex);
            return slideIndex;
        }
        // =======================================================
        // =======================全局变量========================
        // =======================================================
        let slideIndex = 0;
        let images = getImages("demo20.json");
        let imagesCount = images.length;
        // =======================================================
        // =======================初始化==========================
        // =======================================================
        loadImage(images);
        resizeSlide(imagesCount);
        loadMinImage(images);
        showSlideByIndex(0);
        // =======================================================
        // =======================绑定事件========================
        // =======================================================
        $(window).load(resizeSlide);
        $(window).resize(resizeSlide);
        $(".slide-button>li:first").click(()=>{slideIndex = slideLeft(slideIndex);});
        $(".slide-button>li:last").click(()=>{slideIndex = slideRight(slideIndex);});
        setInterval(()=>{slideIndex = slideRight(slideIndex);},5000);
        (()=>{
            let e = $(".min-images>li");
            for(let i=0;i<e.length;i++){
                e[i].onclick = function(){
                    showSlideByIndex(i);
                    slideIndex = i;
                }
            }
        })();
    </script>
</body>
</html>
```

### 4.2 毛玻璃

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>毛玻璃</title>
    <script src="https://cdn.staticfile.org/jquery/1.8.3/jquery.min.js"></script>
    <style>
        *{
            margin: 0px;
            padding: 0px;
            list-style-type: none;
            /* border: 1px solid black; */
        }
        body{
            position: relative;
            display: flex;
            height: 100vh;
            background: linear-gradient(to right, #a074c4, #71ABE3);
        }
        .demo{
            position: relative;
            margin: auto;
            width: 400px;
            height: 300px;
        }
        .ball>li{
            position: absolute;
            border-radius: 50%;
        }
        .ball>li:nth-child(1){
            width: 160px;
            height: 160px;
            background: powderblue;
            right: -60px;
            top: -50px;
        }
        .ball>li:nth-child(2){
            width: 150px;
            height: 150px;
            background: yellowgreen;
            left: -70px;
            bottom: -20px;
        }
        .grass{
            position: absolute;
            width: 100%;
            height: 100%;
            background: linear-gradient(to right bottom, rgba(255,255,255,.6), rgba(255,255,255,.4), rgba(255,255,255,.2));
            border-radius: 20px;
            border-top: 1px solid white;
            border-left: 1px solid white;
            /* 模糊背景核心属性 */
            backdrop-filter: blur(11px);
        }

    </style>
</head>
<body>
    <div class="demo">
        <ul class="ball">
            <li></li>
            <li></li>
        </ul>
        <div class="grass">
        </div>
    </div>

    <script>

    </script>
</body>
</html>
```

### 4.3 跟随鼠标

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>鼠标跟随</title>
    <style>
        *{
            margin: 0px;
            padding: 0px;
            box-sizing: border-box;
        }
        body{
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background: linear-gradient(to right, #a074c4, #a8c0fc);
        }
        .roboot{
            width: 100px;
            height: 100px;
            background: #000;
            border-radius: 10px;
            border-left: 30px solid #FEE082;
            box-shadow: 0 0 5px rgba(0, 0, 0, 0.4);
        }

    </style>
</head>
<body>
    
    <div class="roboot">

    </div>

</body>

    <script>
        // e 是回调函数的参数 表示事件
        document.querySelector("body").addEventListener("mousemove", e=>{
            // 方位角：[-180,180] = atan2(y,x)
            let X = e.clientX;
            let Y = e.clientY;
            
            let r = document.querySelector(".roboot");

            let eX = r.offsetLeft + r.clientWidth/2 + 15;
            let eY = r.offsetTop + r.clientHeight/2;
            
            let ra = Math.atan2(Y-eY, X-eX);
            let deg = ra*180/3.14;

            r.style.transform = `rotate(${deg}deg)`;
        })
    </script>
</html>
```

