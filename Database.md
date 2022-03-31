

## 1. 文件管理

### 1.1 基本概念

| 名词         | 解释                                                         |
| ------------ | ------------------------------------------------------------ |
| 文件         | 文件是保存在持久化存储设备上（硬盘、U盘、磁带...）上的一段数据 |
| 文件分类     | 文本文件、二进制文件                                         |
| 文本文件     | 能够使用字符编码集解释的文件，如日至、邮件                   |
| 二进制文件   | 内部编码为二进制编码，如图片、音乐、压缩包等                 |
| 两者区别     | 本质相同，底层都是二进制，区别在于打开文件的软件对文件的解释 |
| 两者转化     | 字符串可以直接转化成功字节串，字节串需要base64编码后才能转成字符串 |
| 文件读写流程 | 打开文件 >> 读写操作 >> 关闭文件                             |

```python
# 字符串转化成字节串:方式一
my_bytes = b"hello world"					# 只适用于ASCII码范围

# 字符串转化成字节串:方式二
my_bytes = str.encode(self,					# 使用指定字符集将字符串转化成字节串，默认系统使用的编码集
    				  encodeing: str = ...,
    				  errors: str = ...,) ->bytes
```

```python
# 字节串转化成字符串
my_str = my_bytes.decode(self,						# 使用指定字符集将字节串转化成字符串，
    					 encoding: str = ...,		# 默认系统使用的编码集，如果无法匹配字符集则报错
                         errors: str = ...,) ->str	# 默认使用严格模式	
```

### 1.2 文件读写

#### 1.2.1 打开/关闭文件

- 方法一：直接打开

```python
file_obj = open(file, 							# 必选，相对路径(文件名)或绝对路径
                mode:str='rt', 			 		# 打开模式，默认'rt'
                buffering:int=-1,		  		# 缓冲模式
                encoding:str=None, 				# 打开文件编码方式，默认系统格式
                errors:str|None, 				# 报错级别
                newline: str|None=..., 			# 区分换行符
                closefd:bool=..., 				# 传入的file参数类型
                opener:(str,int)->int|None=...	# 自定义开启器
               	) ->IO							# 返回的对象是一个文件对象，可迭代

file_obj.close()								# 需要手动关闭文件，养成良好习惯
```

> 1. open函数通常只需要注意**file**、**mode**和**encoding**三个参数和**文件权限**即可，buffering一般不需要额外修改
> 2. 文件打开后须使用file_obj.close()手动关闭，目的是释放资源、防止误操作;
> 3. encoding参数默认值是根据计算机系统自动生成 -->当系统默认非utf-8，而打开utf-8编码的文件时会无法匹配码型便报错
> 4. mode参数默认是"r"也可以写成"rt"，也就是read text 读文本格式
> 5. buffering参数默认-1，系统自动提供的缓冲机制（自动设别字节串还是字符串），设置为0表示不缓冲（仅适用于字节串），设置成1表示行缓冲（仅适用于字符串）
> 6. newline参数是为了区分Window 和Unix下换行符的不同（Win下换行符是\r\n，Unix下是\n），设置newline=""表示不自动识别
> 6. 若项目标已记为源，则路径需要从源开始书写，可以用pycharm直接复制文件路径获取path

- 方法二：使用上下文管理器

```python
with open(...) as file_obj:
	...
```
> 1. 方法二参数同方法一
> 2. 优点是不用手动关闭（自动关闭）
> 3. 缺点是需要缩进

* 打开模式

| 打开模式 | 效果                                                         |
| :------- | :----------------------------------------------------------- |
| rt       | 为默认打开默认，读字符串模式，文件必须存在，否则报错         |
| wt       | 写字符串打开模式，文件不存在则创建，存在则清空原内容         |
| at       | 追加字符串打开模式，文件不存在则创建，存在则继续进行写操作   |
| xt       | 写字符串模式，新建一个文件，若文件已存则报错                 |
| rt+      | 读写打开模式，文件必须存在                                   |
| wt+      | 读写打开模式，文件不存在则创建，存在则清空原内容             |
| at+      | 追加并可读打开模式，文件不存在则创建，存在则继续进行写操作   |
| rb       | 二进制读打开模式，文件必须存在                               |
| wb       | 二进制写打开模式，文件不存在则创建，存在则清空原内容         |
| ab       | 二进制追加打开模式，文件必须存在                             |
| rb+      | 二进制读写打开模式，文件不存在则创建，存在则继续进行写操作   |
| wb+      | 二进制读写打开模式，文件不存在则创建，存在则清空原内容       |
| ab+      | 二进制追加并可读打开模式，文件不存在则创建，存在则继续进行写操作 |

> 1. 所有文件都可以用二进制方式打开，但二进制文件不能用文本格式打开（文本解释的是字符串不是字节串）
> 2. 文件路径可以用“/”或“\”来表示，使用“\”时注意使用"r"或"\\"进行转义，防止特殊空白字符串

- 打开关闭文件示例

```python
file_obj = open("./test.txt", 'rt')					# 读字符串打开模式打开 + 相对路径
file_obj = open(r".\test.txt", 'rt')				# 同上
file_obj = open(".\\test.txt", 'rt')				# 同上
file_obj = open(r"C:\Desktop\pysrc\Tare\t2\databases\test.txt", "rt")	   # 使用绝对路径
file_obj = open("../test.txt", 'rt')				# 打开上上一级文件夹中的text2.txt文件
file_obj.close()									# 关闭文件		
```

> 1. 读模式打开文件，文件不存在会报错，若后面对该对象进行写操作也会报错
> 2. 写模式打开文件，文件不存在则创建，存在则清空原内容
> 3. 读写操作时，要注意文件的权限

#### 1.2.2 读取文件

- `read([size])`

> 1. 用来读字节串或字符串都可以，读字节串居多
> 2. size指读取的字符/字节数，不写size默认读取到文件末尾；
> 3. 文件比较大时，需要设置**二次缓冲区**，如指定1024个字符或字节分批读取，以降低内存使用率；
> 4. 文件读完时，若继续读取，则返回空字符串或字节串，可以用该方法判断是否已经读完。

```python
# 二进制模式(只能是二进制)复制一部视频
for_read, for_write = open("movie.mp4", "rb"), open("copy_movie.mp4", "wb")
while part := for_read.read(1024): for_write.write(part)
for_write.close()
for_read.close()

# 文本模式读一个txt
with open("test.txt", "rt") as for_read:
    print("第一个字符：", for_read.read(1))
    print("剩下的字符：", for_read.read())
for_read.close()

# 二进制模式读一个txt
with open("test.txt", "rb") as for_read:
    print("第一个字节：", for_read.read(1))
    print("剩下的字节：", for_read.read())
```

- `readline([size])`

> 1. 用来读字节串或字符串都可以，读字符串居多
> 2. size值读取的字符/字节数，不写size默认读取一行

```python
for_read = open("./test", "rt") 
for_read.readline()										  # 读取"test.txt"的第一行
for_read.readline(1)									  # 读取"test.txt"的第二行的第一个字符串
for_read.close()
```

- `readlines([size])`

> 1. 用来读字节串或字符串都可以，读字符串居多
> 2. 给定size表示读取到size字符或字节的所在行后停下，不填参数默认读取文件每一行，并返回一个包含每一行内容的列表
> 3. 列表元素会体现每行的换行符，根据情况可通过str_.rstrip()消去换行符

```python
for_read = open("./test", "rt") 
list_lines = for_read.readlines()						  # ['第一行\n', '第二行\n', '第三行']						
for i, item in enumrate(list_lines):
    list_lines[i] = item.rstrip()						  # ['第一行', '第二行', '第三行']
for_read.close()
```

- `for` 循环每次读取一行

```python
# 文件对象是一个可迭代对象，每次迭代出的值是一行
for_read = open("./test", "rt")
for str_line in for_read:
	print(str_line)										  # 此时等价于for_read.readline()
for_read.close()
```

#### 1.2.2 写入文件

- `write(data)`

> 1. 将字符串或字节串写入到文件中，返回写入的字节数或字符串数；
>
> 2. 每次写入不会在末尾添加换行符，如要换行则手动添加。

```Python
for_write = open("test3.txt", "a")
for_write.write("你好")				# 2
for_write.close()
```

- `writelines(str_list)`

> 1. readlines()的逆操作

```Python
for_write = open("test3.txt", "a")
for_write.writelines(["hello","world"])
for_write.close()
```

#### 1.2.3 缓冲区

> 原理：程序<--->缓冲区<--->磁盘控制器<--->磁盘
> 定义：文件存在于磁盘中，每次对文件的读写前都会建立一个缓冲区，先将内容加载到缓冲区，再进行读写；
> 作用：减少与磁盘数据交换次数，保护此磁盘和提高读写效率

- 缓冲区设置（在open()函数中设置buffering参数）

| 类型           | 设置命令           | 注意事项                       |
| :------------- | :----------------- | :----------------------------- |
| 系统自定义     | buffering=-1或不写 | 一般使用系统自定义缓冲即可     |
| 行缓冲         | buffering=1        | 必须以字符串模式遇到\n自动刷新 |
| 指定缓冲区大小 | buffering>1        | 必须以字节串模式打开           |

- 缓冲区刷新条件

> 1. 缓冲区被写满
> 2. 程序执行结束或文件对象关闭
> 3. 使用了file_obj.flush()强制刷新

#### 1.2.4 文件偏移量

- 基本概念

> 1. 偏移量以Byte为单位(unicode一个汉字3个字节,英文数字1个字节)
> 2. 打开文件进行操作时会自动生成一个记录每次读写位置的记录，每次读写将在该纪录上开始进行
> 3. r或w方式打开时，文件偏移量在文件起始位置
> 4. a方式打开时，文件偏移量在文件结尾位置

- 获取当前文件偏移量：file_obj.tell()

```python
# 假定test.txt内容是：你好世界
with open("test.txt","rt") as file_obj:
    file_obj.read(1)							# 你
    file_obj.tell()								# 3
```

- 移动文件偏移量位置：seek(offset[,whence])

> 1. 文件偏移量是根据文件二进制下的字节位置的度量，是对文件读写位置字节的标记；
> 2. offset 表示相对于该位置移动的字节数，负数表示向前移动（移动后接着写会覆盖原来内容），正数表示往后移动；
> 3. whence表示基准位置，0代表从文件头算起，1代表从当前位置算起，2代表文件末尾算起；
> 4. 必须在二进制打开方式下，基准位置才可以是1或者2，因为字符串不可以根据单字节偏移；
> 5. 若偏移量已经在文件起始位置，再设置往前移动，则会引发OSError。

```python
# 循环写入日志（my.log）每2秒写写入一行，程序结束后（强行结束），重写运行时继续往下写，序号衔接
# 格式：1.2021-12-03 08:35:40
def write_log():
    with open("my.log", "ab+") as for_wr:
        if for_wr.tell() == 0:  # 如果是第一次写入
            first_num = 1
        else:					# 如果需要衔接上次记录
            while True:         # 寻找上一次结束的序号
                # 偏移量每往上移2个单位，则往下读一个单位，直到读到上上一次的换行符
                for_wr.seek(-2, 1)
                byte_ = for_wr.read(1)
                if byte_!=b"\n":
                    continue
                first_num = int(for_wr.read().split(b".")[0]) + 1
                break
        while True:				# 开始写入
            log_line = str(first_num) + "." + time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())
            for_wr.write(log_line.encode() + b"\n")
            time.sleep(2)
            first_num += 1
```

### 1.3 OS模块

* 获取文件大小
```python
os.path.getsize(filename | str)
```
* 递归获取文件夹下及其子文件夹下所有文件路径和其大小
```python
total_size = 0												# 总大小
files_path = []												# 所有文件路径
for root, dirs, files in os.path.walk(dir)
	for file in files:
        path_ = os.path.join(root, file).replace("\\", "/")	# win下是以"\"拼接的，要手动转换成"/"
        files_path.append(path_)
        total_size += os.path.getsize(path_)
```


* 查看文件列表：只看到当前一层
```python
os.listdir(dir)	# 可以是"."
```

* 判断文件/文件夹是否存在
```python
os.path.exists(file | dir)	# 可以是文件路径或文件夹如"."
```

* 删除文件
```python
os.remove(file)
```

* 删除文件夹：前提是文件夹是空的，否则报错：目标文件夹非空
```python
os.rmdir(dir)	
```
### 1.4 综合练习

```python
import re, os.path
from functools import reduce
from multiprocessing import Process

""" 需求：将一个二进制文件根据字节大小拆成指定部分
    可选：使用多进程
"""

def teardown_process(path: str, serial: int, size: int, offset: int, buffer=1024):
    file_name = re.findall(r"(\w+)\.\w+", path)[0]
    file_kind = re.findall(r"\w+(\.\w+)", path)[0]
    new_path = re.sub(r"(\w+)\.\w+", file_name + "." + str(serial) + file_kind, path)	# 新文件在原文件夹
    for_read = open(path, "rb")
    for_read.seek(offset, 0)
    for_write = open(new_path, "wb")

    while size -buffer >= 0:
        size -= buffer
        for_write.write(for_read.read(buffer))
    for_write.write(for_read.read(size))

    for_write.close()
    for_read.close()

def teardown(path: str, copies: int, buffer=1024):
    """ 将二进制文件使用多进程分成指定份数
    """

    total_size = os.path.getsize(path)
    discuss, mod = divmod(total_size, copies)
    sizes = [([discuss] * copies)[i] + bool(mod -i > 0) for i in range(copies)]  # 二进制划分每个结果长度
    offsets = [0] + [reduce(lambda x, y:x+y, sizes[0:i]) for i in range(1, len(sizes))]

    for serial, size, offset in zip(list(range(1, copies+1)), sizes, offsets):
        Process(target=teardown_process, args=(path, serial, size, offset, buffer)).start()

# --------------------------------程序入口---------------------------------------
if __name__ == "__main__":
    teardown("img.png", 3)
    ...
```
## 2. 正则表达式

### 2.1 匹配函数 findall

* 格式
```python
def findall(pattern, string, flags=0) -> list:
```
> 1. **正则表达式里面不能写空格**，匹配空格用\s
> 2. 未匹配到结果时，返回空列表
> 3. 若有分组，则仅返回分组内的内容，分组外的内容起到辅助匹配作用
> 4. 若有多个分组，则以列表嵌套元组形式返回

* 匹配原则：**正确性、排他性、全面性**

### 2.2 元字符的使用

* 元字符匹配规则
|元字符|含义|举例|结果|
|:----|:---|:---|----|
|\||匹配"\|"字符两侧的任一个字符|re.findall("h\|w", "hello world")|["h","w"]|
|.|匹配任意一个字符，除了换行符|re.findall("张.丰", "张三丰")|["张三丰]|
|[aeiou]、[a-z]、[&%0-9]...|匹配区间内的一个字符|re.findall("[aeiou]", "hello world")|['e', 'o', 'o']|
|[^a-z]...|匹配非区间的一个字符|re.findall("[^0-9\]", "张三123")|["张", "三"]|
|*|贪婪匹配*前面的字符出现0次或多次|re.findall("wo*", "woooo~~w!")|["woooo", "w"]|
|+|贪婪匹配*前面的字符出现1次或多次|re.findall("wo+", "woooo~~w!")|["woooo"]|
|?|贪婪匹配*前面的字符出现0次或1次|re.findall("wo?", "woooo~~w!")|["wo", "w"]|
|{n}|匹配{n}前面的字符出现n次|re.findall("wo{3}", "woooo~~w!")|["wooo"]|
|{n,m}|贪婪匹配{n,n}前面的字符出现m-n次|re.findall("wo{3,4}", "woooo~~w!")|["wooo"]|
|^|仅匹配字符串开始位置|re.findall("^wo","woooo~~w!")|["wo"]|
|\$|仅匹配字符串结束位置或换行符前位置|re.findall("!$","woooo~~w!")|["!"]|
|\d|匹配数字字符|re.findall("\d+","age: 19")|["19"]|
|\D|匹配非数字字符|re.findall("\D+","age: 19")|["age: "]|
|\w|匹配python变量名能用的字符|re.findall("\w+","age: 19")|['age', '19']|
|\W|匹配python变量名不能用的字符|re.findall("\W+","age: 19")|[': ']|
|\s|匹配空字符(\r\n\t\v\f)|re.findall("\s+","age: 19")|[" "]|
|\S|匹配非空字符|re.findall("\S+","age: 19")|['age:', '19']|
|\b|匹配单词边界位置|re.findall(r"\bage\b","age: 19")|['age']|
|\B|匹配非单词边界位置|re.findall("\Bg\B","age: 19")|['g']|

* 匹配元字符：使用`\`进行转义

```python
# 包含：. * + ? ^ $ [] () {} | \
re.findall("\*","**之火，可以燎原")				# ['*', '*']
```

* 匹配`\`

```python
# 匹配"\section"
re.findall(r"\\section",r"\section")
```

* 贪婪模式和非贪婪模式

> 1. 默认使用贪婪模式 -->尽可能多的向后匹配
> 2. 强制非贪婪模式 -->在元字符后面添加?

```python
re.findall("\(.*\)","(my) name is (Tom)")		# ['(my) name is (Tom)'] -->默认贪婪
re.findall("\(.*?\)","(my) name is (Tom)")		# ['(my)', '(Tom)']	-->改为非贪婪
```

### 2.3 正则分组

* 在函数 findall()里面使用分组 -->结果里只包含括号内的内容（非括号内内容可以用来辅助匹配）

```python
# 给定路径，匹配文件名(不带文件格式)
path = r"D:\MyPrograms\Acrobat\ReadMe.htm"
path = "D:/MyPrograms/Acrobat/ReadMe.htm"
path = "D:\\MyPrograms\\Acrobat\\ReadMe.htm"
regex = r"(\w+)\.\w+"
print(re.findall(regex, path))				# ["ReadMe"]
```

* 在函数search()里面使用分组 -->结果会显示所有匹配到的内容且re.search()只返回匹配第一个符合的结果

```python
# 若使用re.findall() -->结果里只包含括号内的内容
re.findall("(bili)+","bilibili")			# ['bili']

# 使用re.search()单层分组：改变元字符的操作对象 -->结果会显示所有匹配到的内容
match_obj = re.search("(bili)+","bilibili")
res = match_obj.group()						# bilibili
position = match_obj.span()					# (0, 8)
```

* 捕获组：拥有自定义名字的子组

```python
match_obj = re.search("(?P<my_group>bili)+","bilibili")
res = match_obj.group("my_group")			# bili
```

> 1. 一个正则表达式可以有多个子组
> 2. 子组可以多重嵌套
> 3. 子组序号是由外到内，由左到右计数

* 组的序列号

```python
r"((ab)c)d(?P<my_group>ef)"
# 第一组：((ab)c)
# 第二组：(ab)
# 第三组：(?P<my_group>ef)
```

### 2.3 re模块

* 匹配并返回结果列表：

```python
def findall(pattern, string, flags=0) -> list:
```

* 匹配并返回第一个符合表达式的match对象

```python
def search(pattern, string, flags=0) -> Match | None:
```

* 分割：

```python
def split(pattern, string, maxsplit=0, flags=0 -> list:
```

* 替换：

```python
def sub(pattern, repl, string, count=0, flags=0) -> str:
```


* 匹配并返回结果的迭代器

```python
def finditer(pattern, string, flags=0) -> Iterator:
```

* 匹配目标字符串开始位置

```python
def match(pattern, string, flags=0) -> Match | None:
```

### 2.4 综合练习
```python
re.findall("[A-Z][a-z]*", "How are you? I am fine")		# 匹配手写字母大写的单词
re.findall("[0-9]+", "北京时间：2021-12-13")				 # 匹配年月日
re.findall("-?[0-9]+", "气温-20°,中午12点")			     # 匹配数字
re.findall(r"\b1[3578][0-9]{9}\b", "王总号码:13966669999,银行卡号:68239830123912231")	# 匹配手机号码
re.findall("[1-9][0-9]{5-10}", "王总qq号是4266666")		  # 匹配qq号码
re.findall("^[_0-9a-zA-Z]{6-12}$", input("用户名:"))	   # 匹配用户名是否仅由数字字母和下划线组成
re.findall("\$\d+", "日薪：$150")						   # 匹配工资
re.findall("《\S+?》", "《追风筝的人》《花千骨》")			# 匹配书名
re.findall("李蛋:(\d+)", "张三:87,李四:98,李蛋:66")		  	# 匹配李蛋分数
re.search("(\d{1,3}\.?){4}", "IP:192.168.4.6").group()	 # 匹配IP地址
```

## 	3. 数据库

### 3.1 基础概念

| 基础概念         | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| 数据库           | 数据库（Database）是按照一定数据结构来组织、存储和管理数据的仓库 |
| DBMS             | 数据库管理系统，管理数据库的软件，用于建立和维护数据         |
| 应用领域         | 几乎涉及到需要数据管理的方方面面                             |
| 关系型数据库     | 采用关系模型（二维表）来组织数据结构的数据库，如Oracle、SQL server、MySQL |
| 非关系型数据库   | 不采用关系模型的数据库，如MongoDB、Redis                     |
| SQL语言          | 结构化查询语言（Structured Query Language），是一种数据库查询和程序设计的语言 |
| SQL语言特点      | 几乎独立于数据库本身、语句使用`;`结尾，命令不区分大小写      |
| MySQL特点        | 开源 、跨平台、支持多种语言、结构优良、稳定运行、功能全面    |
| 数据存储发展阶段 | 人工管理、文件管理、数据库管                                 |
| 字段             | 数据属性、每一列                                             |
| 记录             | 每条数据、每一行                                             |
| 库名命名规范     | 只能包含数字、字母、下划线，不可为纯数字、库名区分大小写、不能使用关键字 |

掌握命令：

* MySQL安装和初始化
* 查看所有库
* 使用指定库
* 查看当前库
* 删库
* 整数类型
* 字符串类型
* 时间类型
* 获取当前时间日期
* 查看当前库下所有表
* 创建表
* 查看当前表结构
* 查看表创建过程
* 删表
* 重命名表
* 插入表记录
* 修改表记录
* 删除表记录
* 查询
* 添加表字段
* 删除表字段
* 修改表字段
* 模糊查询
* 临时重命名
* 排序
* 限制显示行数
* 联合查询
* 子查询
* 聚合查询
* 创建索引
* 添加索引
* 删除索引（主索引、非主键索引）
* 查看索引
* 添加外键
* 删除外键
* 多表联查
* 创建视图
* 查看视图
* 删除视图
* 创建函数
* 查看函数
* 调用函数
* 创建存储过程
* 查看存储过程
* 调用存储过程
* 事务控制语句
* 查看查询效能
* 数据库备份
* 数据库恢复
* 远程连接
* 添加用户（添加权限、删除权限、删除权限privileges）
* 删除用户

### 3.2 安装MySQL

- **安装和连接MySQL**（Archlinux）

```shell
sudo pacman -Syy	# 更新软件仓库
sudo pacman -S mysql	# 安装mysql
sudo mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql	# 初始化
sudo systemctl enable mysqld	# 开机自启动
sudo systemctl start mysqld	# 马上运行
mysql -h "主机名"-u root -p	# 登录
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码'	# 修改初始密码
mysql> EXIT	# 退出连接
```

### 3.3 数据库管理

#### 3.3.1 数据类型

- **数字类型**

|    类型     | 空间(字节) | 无符号范围                                         | 有符号范围      |
| :---------: | :--------- | :------------------------------------------------- | --------------- |
| **tinyint** | **1**      | (0, 2^8^-1)                                        | (-2^7^, 2^7^-1) |
|  smallint   | 2          | (0, 2^16^-1)                                       | 略              |
|  mediumint  | 3          | (0,2^24^-1)                                        | 略              |
|   **int**   | **4**      | (0,2^32^-1) # 约43亿                               | 略              |
|   bigint    | 8          | (0,2^54^-1)                                        | 略              |
|  **float**  | **4**      | 0,(1.175494351E-38,3.402823466E+38)                | 略              |
|   double    | 8          | 0,(2.2250738585072014E308,1.7976931348623157E+308) | 略              |
| **decimal** | max(M,N)+2 | 取决于M和D的值                                     | 略              |

> 1. decimal用于表示准确性要求很高的数据，如money，用decimal类型减少储存误差，语法是decimal(M,N)。M表示总位数，N表示小数位数，若录入小数位数>N，将做四舍五入处理，也就是强制N位小数，若录入整数位数>M-N，则报错
> 2. 浮点数支持如".14"写法
> 3. bit类型指0和1两种情况
> 3. 为什么是2^n^-1而不是2^n^：因为n个比特位全部都为1时必定是2^n^-1

- **字符串类型**

|    类型     | 字符个数/二进制长度 | 特点                                                      |
| :---------: | :------------------ | :-------------------------------------------------------- |
|  **char**   | 0-**255**           | 定长字符串，最大可存储255个字符，默认1字符                |
| **varchar** | 0-**65535**         | 变长字符串，最大可指定最大65535个字符，必须指定最大字符数 |
|  tinyblob   | 0-255               | 最大255字节二进制数据                                     |
|  tinytext   | 0-255               | 最大255个字符                                             |
|  **blob**   | 0-65535             | 最大约**64kB**二进制数据                                  |
|  **text**   | 0-65535             | 最大可存储**65535**个字符                                 |
| mediumblob  | 0-16777215          | 最大约1.6MB二进制数据                                     |
| mediumtext  | 0-16777215          | 最大可存储16777215个字符                                  |
|  longblob   | 0-4294967295        | 最大约4GB二进制数据                                       |
|  longtext   | 0-4294967295        | 最大可存储4294967295个字符                                |

> 1. char(n=1)和varchar(n)中的n指的是字符个数而不是字节数，如char(3)表示存入定长为3字符，不足3字符填充空白，所以实际所占空间大小由内容决定；
> 2. 若存入整型数据给字符串或字节串字段，MySQL不会报错，但是会转化成奇怪的数字；
> 3. char的n可以不写，默认1（字符）,varchar的n必须写，否则报错，而且存入字符串都不可以超过n个字符；
> 4. 汉字的占据字节长度和编码相关，UTF-8中是3字节或4字节，GBK中是2字节，UTF-8兼容ASCII编码，所以英文和数字字符是1字节。

- **时间类型**

| 符号      | 大小 | 范围                                                | 格式                |
| :-------- | :--- | :-------------------------------------------------- | :------------------ |
| date      | 3    | 1000-01-01/9999-12-31                               | yyyy-MM-dd          |
| time      | 3    | '-838:59:59'/'838:59:59' # 可表示约前后一个月时间段 | hh:mm:ss            |
| year      | 1    | 1901/2155                                           | yyyy                |
| datetime  | 8    | 1000-01-01 00:00:00/9999-12-31 23:59:59             | yyyy-MM-dd hh:mm:ss |
| timestamp | 4    | 1970-01-01 :00:00:00/2038                           | YYYYMMDD HHMMSS     |

> 1. 时间类型必须写成字符串形式录入，否则结果会变奇怪
> 2. 时间类型具有节省内存空间、自动检验排错、可比较的优点
> 3. 时间数据子元素可以不写完整，如录入time为"21:1:2"也是合法的
> 4. 可以将now()函数作为字段默认值

#### 3.3.2 基础操作


- **查看和创建库**

```MySQL
SHOW DATABASES;	 # 查看已有库
CREATE DATABASE students CHARACTER SET UTF8;	# 创建students数据库,指定字符后创建表继续使用该字符
CREATE DATABASE students DEFAULT CHARSET UTF8;	# 另一种写法
```

- 切换库

```MySQL
USE students;
```

- 查看当前所在库

```MySQL
SELECT DATABASE();
```

- 删除库

```MySQL
DROP DATABASE students;
```

* 注意事项：

```yacas
1. 数据库名区分大小写
2. UTF8字符必须手动指定，否则使用默认字符及将无法显示汉字
```

### 3.4 数据表管理

#### 3.4.1 建表流程

```yacas
确定存储内容 >> 确定字段内容和类型 >> 建立RE关系模型
```

#### 3.4.2 数据类型


- 获取当前datetime函数

```mysql
SELECT NOW();												# 直接显示当前datetime
alter table marathon modify r_time datetime default now();	# 将增加记录时的datetime作为一个字段默认值
```

#### 3.5.3 表的基本操作

> 格式：create table 表名(字段名1 数据类型 约束**, **字段名2 数据类型 约束**, ..., **字段名n 数据类型 约束);

- 创建一个学生基本信息表（姓名、班级、年龄、性别、体重、身高、入学时间）

```MySQL
create table student_msg(
    id int unsigned primary key auto_increment,
    name char(4) not null,
    class char(10) not null,
    age tinyint unsigned default 0,
    sex enum("man","woman"),
    weight tinyint unsigned not null,
    height tinyint unsigned not null,
    meet_time date not null
);
```

- 创建一个学生兴趣爱好表（姓名、兴趣、备注）

```MySQL
create table student_hobby(
    id int unsigned primary key auto_increment,
    name char(4) not null,
    hobby set("sing","dance","draw"),
    mark text comment "爱好的备注"
);
```

- **字段约束**

> 1. unsigned，可用在数字后面，表示无符号，数字默认为有符号
> 2. not null，可用在任意字段后面，表示强制不可缺省
> 3. default 数值，表示默认值是设定的数值，和not null 一般不在一起使用
> 4. comment 字符串，表示是改字段的注解，只起提醒作用，comment后面不用写()
> 5. auto_increment，定义列为自增属性，一般用于主键
> 6. primary key，表示是该表的主键，主键必须是唯一且不能为空，主键不保证每行记录连续
> 6. binary，表示区分大小写 -->用在内容是字符串并且是唯一索引时的场合，如假定用户名不能同时有a和A

- 查看当前库下数据表

```MySQL
show tables;
```

- 查看表结构

```MySQL
desc student_msg;								# 查看student_msg表结构
```

- 查看数据表创建信息

```MySQL
show create table student_msg;					# 查看student_msg表创建信息
```

- 删除表

```MySQL
drop table student_msg;							# 删除student_msg表
```

- 修改表名

```MySQL
alter table student_msg rename sut;				# 重命名表名
```

### 3.6  表数据操作

#### 3.6.1 插入(insert)

- 格式

```MySQL
# 方式1：
insert into 表名 values(值1,值2...),(值1,值2...),...;	# 全部字段值均需要添加！包括自增字段和有默认值字段
# 方式2：
insert into 表名(字段1,...) values(值1,值2...),...;	# 指定字段插入，若该字段存在not null 约束，则必须指定该字段
```

- 使用方式1分别向student_msg和student_hobby中插入5条和2条信息

```MySQL
insert into student_msg values
(1,"张三","1班",14,"man",50,150,"2019-9-1"),
(2,"李四","1班",15,"woman",40,140,"2019-9-1"),
(3,"王五","2班",15,"man",45,146,"2018-9-1"),
(4,"赵六","3班",13,"man",46,140,"2019-9-1"),
(5,"李七","3班",12,"woman",43,140,"2020-9-1");

insert into student_hobby values
(1,"王五","sing","他唱歌基本功不错"),
(2,"张三","dance",null);							# 方式一必须所有字段都带上，没有可以写null
```

- 使用方式2分别向student_msg和student_hobby中插入5条和2条信息

```MySQL
insert into student_msg(name,class,age,sex,weight,height,meet_time) values
("陈八","4班",15,"woman",46,160,"2019-9-1"),
("王九","4班",14,"man",51,162,"2019-9-1"),
("郑十","4班",15,"woman",46,150,"2019-9-1"),
("马十一","3班",13,"man",46,143,"2018-9-1"),
("胡十二","2班",16,"woman",46,140,"2019-9-1");

insert into student_hobby(name,hobby,mark) values
("郑十","draw",null),
("胡十二","sing,draw,dance","她参加了很多兴趣爱好");
```

#### 3.6.2 查询(select)

- 格式

```MySQL
select * from student_msg;						# 查询表所有列内容(全部内容)
select id,name from student_hobby;				# 查询指定字段内容
```

> **优先级**：from语句 > select语句

#### 3.6.3 数据运算

数据运算常用在where字句中，where 子句用来限制操作范围，**若不写where将操作的是表中所有记录，是一个很危险的行为**

- **算术运算符 + - * / % div**（加、减、乘、除、余和地板除）

```MySQL
select * from student_msg where age % 2 = 0;	# 查询表中年龄是偶数的记录
```

> **优先级：**from语句 > where语句>select语句
>
> - **比较运算符**

| 符号            | 举例用法                            |
| :-------------- | :---------------------------------- |
| =               | ...where name="Tom";                |
| !=              | ...where sex != "man";              |
| >               | ...where age > 30;                  |
| <               | ...where age <30;                   |
| <=              | ...where name <= "Tom";             |
| >=              | ...where name >="Tom";              |
| between  and    | ...where age between 20 and 30;     |
| not between and | ...where age not between 20 and 30; |
| in              | ...where age in (21,22,23);         |
| not in          | ...where age not in (21,22,23);     |
| is null         | ...where mark is null;              |
| is not null     | ...where mark is not null;          |

> 1. 比较运算符不能使用连比较，如...where 20 < age <30是非法的，若想使用连比较，则使用...where age between 20 and 30（包含20和30）或者用逻辑运算符"and"连接，另外比较运算符号可以作用在字符串上，原理同Python；
> 2. 字符串比较不区分大小写，若想区分大小写需要在字段前加上binary，如...where binary name = "Jack"，此时不会匹配到"jack"；
> 3. 一般情况下可以用in(值1,值2) 来代替 or，语法更简单

- 逻辑运算符

| 符号 | 举例用法                          |
| :--- | :-------------------------------- |
| not  | ...where not age > 20;            |
| and  | ...where age = 20 and score > 90; |
| or   | ...where age = 20 or age = 21;    |

- 运算符优先级

> 0. 括号（）
> 1. 算术运算符
> 2. 比较符
> 3. 逻辑（not > and > or）

* 其它运算：

> 1. 日期增加：`data_add(被修改字段, interval 数字 day)`

#### 3.6.4 更新表记录(update)

- 格式

```MySQL
update 表名 set 字段1=值1,字段2=值2,...[where 条件子句];
```

```MySQL
update student_msg set meet_time="2019-9-1" where meet_time = "2018-9-1";	# 将入学日期是2018-9-1调整为2019年
update student_msg set meet_time = date_add(meet_time,interval 2 day);		# 将所有入学日期+2天
```

> **若update语句后面不写where条件，将修改整个表格**
> **优先级：**where语句 > update...set语句

#### 3.6.5 删除表记录(delete)

- 格式

```MySQL
delete from 表名 where 条件;
```

```MySQL
delete from student_msg where sex is null;						# 删除sex是null的记录
```

> 1. **若delete语句后面不写where条件，将删除整个表格**
>
> 2. 删除记录后，该记录占用的主键后续仍然不可用，若要重新排列主键，可以使用下列方法

```mysql
# 若从一张表清空后开始再加数据
alter table 表名 auto_increment = 1;

# 若表里还有其它数据
alter table 表名 drop primary key;
alter table 表名 add id int primary key auto_icrement first;
```


#### 3.6.6 表字段操作(alter)

- 添加字段

```MySQL
alter table student_msg add tel char(11);						# 添加tel字段，默认添加到字段末尾
alter table student_msg add score float first;					# 添加score字段，添加到字段首
alter table student_msg add leave_time date after meet_time;	# 在meet_time字段后面添加leave_time字段
```

> 1. 添加字段后，所有记录在该字段上均为默认值，若添加字段时约束条件为not null，则默认值是空字符串
> 2. 一次性可以添加多个字段但是不能指定位置

- 删除字段

```MySQL
alter table student_msg drop tel;					
alter table student_msg drop score;							
alter table student_msg drop leave_time;							
```

- 修改字段类型（以及约束条件）

```MySQL
alter table student_msg modify name varchar(40) not null;
```

> **修改后的字段类型必须是修改前字段的父集或本身**

- 修改字段名和字段类型

```MySQL
alter table student_hobby change mark `comment` text;		# comment是关键字，必须带上``反引号
alter table student_msg change meet_time meet_date date not null;	# 修改后的名字是meet_date，后面数据类型和约束有必须照抄
```

> **修改字段名后面必须要跟上数据类型，且是旧字段类型的父集或本身，注意原来的约束需要保留**

### 3.7 高级查询语句

#### 3.7.1 like 模糊查询

like 用于where子句中进行模糊查询，like后面的匹配字符串中的 "%" 表示任意个字符，包括0个，下划线"_"表示一个任意一个字符

- 格式

```MySQL
select * from student_msg where name like "张%"# 查询姓张的记录
select * from student_msg where name like "___"# 查询名字是三个字符的记录
select * from student_msg where name like "%五%"# 查询名字中带有"五"的记录
```

> 模糊查询同样适用于update、delete语句

#### 3.7.2 as 语句

> as 语句用于给字段或表名重命名，仅适用于当前操作，并不是修改任何内容

```mysql
select name as "姓名",class as "班级" from student_msg;# 查询表中name和class字段
select stu.name,stu.class from student_msg as stu where stu.class in ("1班","2班");# 查询1班和2班的记录
```

#### 3.7.3 order 排序

> order by 子句进行升序（ASC）或降序（DESC）返回数据结果，默认ASC

```mysql
select * from student_msg order by height desc;# 根据身高降序显示
```

> 复合排序：第一项若相同，根据第二项比较

```mysql
select * from student_msg order by age,weight desc;# 根据年龄升序显示，年龄相同时根据体重降序排序
select * from student_msg where sex="man" order by age,weight,height;# 对男生年龄进行升序排序，年龄相同根据体重升序，体重相同根据身高升序
```

#### 3.7.4 limit 限制

> limit 子句用于限制select语句、update语句和delete语句操作的数量

```mysql
select * from student_msg order by height desc limit 1;# 查询身高最高的记录
select * from student_msg order by height desc limit 1 offset 1;# 查询第二高的记录
select * from student_msg order by height desc limit 2,8;# 查询身高第三名到第十名的记录
```

#### 3.7.5 union 联合查询

> 1. union 操作用于连接两个以上的select语句的结果合并到一个结果集中，可以连接同一个表或不同表
> 2. 结果集的字段以第一个为准，前提是保证多个select语句的字段数量相同，否则无法合并到一个结果集中而报错
> 3. 默认去重，使用union all 将不对结果集去重

```mysql
# 使用union查询身高大于150的女生和体重小于60的男生
select * from student_msg where sex = "woman" and height > 150
union
select * from student_msg where sex = "man" and weight < 60;

# 查询不同字段不同表格合并到一个结果集中
select name as "姓名",age as "年龄或爱好",meet_date as "入学时间或备注" from student_msg where id < 5
union
select name,hobby,`comment` from student_hobby;

# 对结果不进行去重
select * from student_msg where sex="man"
union all
select * from student_msg where height > 160;	# 此时结果集中会出现重复的记录
```

#### 3.7.6 子查询

- 将select语句用作另一个select语句的**表内容**或**单个条件值**或**多个值**

> 1. **子查询时禁止对一张表同时查询并修改**，若有需求，需要再次转换成子查询
> 2. **用作表内容时必须加上as别名**
> 3. **子查询时尽量用括号**，否则容易产生二义性

```mysql
# 使用子查询查询年龄大于14的男生
select * from (select * from student_msg where sex = "man") as man where age > 14;
```

```mysql
# 查询与张三同班的记录
select * from student_msg where age = (select age from student_msg where name = "张三");
# 查询报名了兴趣班的记录
select * from student_msg where name in (select name from student_hobby);
```

- <font color="red">子查询同样适用于updata、delete语句，前提不能是同一张表，若是，需要再次转换成子查询</font>

```mysql
# 将报名了兴趣班的学生入学日期提前5天
update student_msg set meet_date = date_add(meet_date,interval -5 day) where name in (select name from student_hobby);

# 将比张三晚入学的学生的入学日期退后3天
update student_msg set meet_date = date_add(meet_date,interval 3 day) where meet_date > (select meet_date from (select * from student_msg) as stu where name = "张三");
```

### 3.8 聚合查询

> 聚合操作指的是在数据查找基础上对数据的进一步整理筛选行

```mysql
# 示例数据表和数据内容如下
create table hero_msg(
	id int unsigned primary key auto_increment,
	name varchar(10) not null,
    sex enum("男","女"),
    country enum("魏","蜀","吴"),
    attack smallint unsigned default 0,
    defense smallint unsigned default 0
);

insert into hero_msg
values (1, '曹操', '男', '魏', 256, 63),
(2, '张辽', '男', '魏', 328, 69),
(3, '甄姬', '女', '魏', 168, 34),
(4, '夏侯渊', '男', '魏', 366, 83),
(5, '刘备', '男', '蜀', 220, 59),
(6, '诸葛亮', '男', '蜀', 170, 54),
(7, '赵云', '男', '蜀', 377, 66),
(8, '张飞', '男', '蜀', 370, 80),
(9, '孙尚香', '女', '蜀', 249, 62),
(10, '大乔', '女', '吴', 190, 44),
(11, '小乔', '女', '吴', 188, 39),
(12, '周瑜', '男', '吴', 303, 60),
(13, '吕蒙', '男', '吴', 330, 71);
```

| id   | name   | gender | country | attack | defense |
| ---- | ------ | ------ | ------- | ------ | ------- |
| 1    | 曹操   | 男     | 魏      | 256    | 63      |
| 2    | 张辽   | 男     | 魏      | 328    | 69      |
| 3    | 甄姬   | 女     | 魏      | 168    | 34      |
| 4    | 夏侯渊 | 男     | 魏      | 366    | 83      |
| 5    | 刘备   | 男     | 蜀      | 220    | 59      |
| 6    | 诸葛亮 | 男     | 蜀      | 170    | 54      |
| 7    | 赵云   | 男     | 蜀      | 377    | 66      |
| 8    | 张飞   | 男     | 蜀      | 370    | 80      |
| 9    | 孙尚香 | 女     | 蜀      | 249    | 62      |
| 10   | 大乔   | 女     | 吴      | 190    | 44      |
| 11   | 小乔   | 女     | 吴      | 188    | 39      |
| 12   | 周瑜   | 男     | 吴      | 303    | 60      |
| 13   | 吕蒙   | 男     | 吴      | 330    | 71      |

#### 3.8.1 5个聚合函数

> avg/max/min/sum/count

```mysql
# 查询最高攻击力的记录（有多个时全部显示）
select * from hero_msg where attack = (select max(attack) from hero_msg);

# 查询高于总体平均攻击力的男性英雄记录
select * from hero_msg where sex = "男" and attack > (select avg(attack) from hero_msg);

# 查询三个国家的总人数、总攻击力和平均攻击力
select count(*) as "总人数",sum(attack) as "总攻击力",avg(attack) as "平均攻击力" from hero_msg;
```

> 1. count(字段)无法统计null，可以用*来代替，但是在**多表联查时不能用count( * )**
> 2. 一个select语句中可以写多个聚合函数且可以重命名
> 3. 聚合函数和普通字段一起使用时，每个普通字段都要体现在group by 后面的字段中（8.0及以上新版不用受此约束）
>
> ```mysql
> # 查询女性角色的最大攻击力和最小攻击力
> select sex,max(attack),min(attack) from hero_msg where sex = "女";
> ```

#### 3.8.2 group by 聚合分组

> 1. 存在聚合函数时，根据group by分组，可分别计算聚合函数结果（理解成一次性操作多个分表）
> 2. 查询结果中的字段取分组后第一个字段

> ```mysql
> # 查询每个国家的英雄数量
> select country,count(*) from hero_msg group by country;
> # 上一句等价于
> select country,count(*) from hero_msg where country = "魏"
> union
> select country,count(*) from hero_msg where country = "蜀"
> union
> select country,count(*) from hero_msg where country = "吴";
> # 查询每个国家平均atk
> select country,avg(attack) from hero_msg group by country;	
> # 上一句等价于
> select country,avg(attack) from hero_msg where country = "魏"
> union
> select country,avg(attack) from hero_msg where country = "蜀"
> union
> select country,avg(attack) from hero_msg where country = "吴";
> ```

> 2. 对多个字段分组（二次分组）
>
> ```mysql
> # 查询每个国家男性、女性平均atk
> select country,sex,avg(attack) from hero_msg group by country,sex;
> # 上一句等价于
> select country,sex,avg(attack) from hero_msg where country = "魏" and sex = "男"
> union
> select country,sex,avg(attack) from hero_msg where country = "魏" and sex = "女"
> union
> select country,sex,avg(attack) from hero_msg where country = "蜀" and sex = "男"
> union
> select country,sex,avg(attack) from hero_msg where country = "蜀" and sex = "女"
> union
> select country,sex,avg(attack) from hero_msg where country = "吴" and sex = "男"
> union
> select country,sex,avg(attack) from hero_msg where country = "吴" and sex = "女";
> ```

> 3. group by 和 where 搭配使用 >>where是缩小查询范围，group by 是建立在where基础上的分组的操作
>
> ```mysql
> # 查询男性英雄最多的国家名称和男性英雄数量
> select country,count(*) from hero_msg where sex = "男" group by country order by count(*) desc limit 1;
> # 执行顺序： from语句 - where语句 - group by 语句 - select语句 - order by 语句 - limit语句
> ```

#### 3.8.3 having 聚合筛选

- **having 是记录集合的筛选**，很多情况下可以和 where 混用，**主要用途是在group by后面对整个表格进行计算筛选结果**

> ```mysql
> # 查询防御力大于60的记录
> select * from hero_msg where defense > 60;
> select * from hero_msg having defense > 60;
> 
> # 统计攻击力大于300且防御力大于60的记录
> select * from hero_msg where attack > 300 and defense > 60;
> select * from hero_msg having attack > 300 and defense > 60;
> select * from hero_msg where attack > 300 having defense > 60;
> # select * from hero_msg having attack > 300 where defense > 60; 错误示范
> # select * from hero_msg where attack > 300 where defense > 60; 错误示范
> select * from (select * from hero_msg where attack > 300) as great_hero where defense > 60;
> select * from (select * from hero_msg having attack > 300) as great_hero where defense > 60;
> select * from (select * from hero_msg having attack > 300) as greate_hero having defense > 60;
> select * from (select * from hero_msg where attack > 300) as great_hero having defense > 60;
> ```

- having 和 where 的区别

> 1. where 是取子表而 having 是对结果进行筛选
> 2. having 能直接用在 group by 后面表示对原表进行分组后的筛选而 where 不可以，非要用的话需要使用子查询
>
> ```mysql
> # 统计平均攻击力大于250的国家的英雄数量
> select country,count(*) from hero_msg group by country having avg(attack) > 250;
> # 上一句等价于
> select a as "国家",b as "人数" from (select country as a,count(*) as b,avg(attack) as c from hero_msg group by country) as country_group where country_group.c > 250;
> # 也等价于
> select a as "国家",b as "人数" from (
> select country as a,count(*) as b,avg(attack) as c from hero_msg where country = "魏"
> union
> select country,count(*),avg(attack) from hero_msg where country = "蜀"
> union
> select country,count(*),avg(attack) from hero_msg where country = "吴"
> ) as country_group where country_group.c > 250;
> ```

#### 3.8.4 distinct 去重语句

> 不显示字段重复值
>
> ```mysql
> # 统计hero_msg中有哪些国家
> select distinct country from hero_msg;
> # 等价于
> select country from hero_msg group by country;
> 
> # 统计有多少个国家
> select count(distinct country) from hero_msg;
> ```

#### 3.8.5 分区操作

```mysql
SELECT *, ROW_NUMBER() OVER(PARTITION BY id ORDER BY sign_date) AS cum FROM login_tab;
```

### 3.9 索引操作

#### 3.9.1 索引概述

- 定义

> 1. 索引是为了快速查找而对表的字段进行排序的一种结构，分为 Btree 和 Hash 结构
> 2. 优点：加快数据检索效率
> 3. 缺点：占据物理内存，降低写入效率

- 应用场景

> 1. 数据量很大且需要对该字段经常查询（from 后的多表连接、where 条件、order by 条件）
> 2. 若频繁修改建立索引的字段的内容，则需要权衡考虑
> 3. 建立主键或外键时将自动创建索引用于多表联查（主键对应主键索引，外键对应普通索引）

- 分类

> 1. 普通索引（MUL），字段值无约束
> 2. 唯一索引（UNI），字段值不能重复，可以有null
> 3. 主键索引（PRI），一个表中只能有一个主键，不允许重复，不能为null

#### 3.9.2 创建索引

- 格式

```mysql
# 1. 创建表时创建索引
create table 表名(
    id int primary key auto_increment,			# 主键索引
	字段1 字段类型,
    字段2 字段类型,
    index(字段1),								   # 字段1添加普通索引
    unique [index](字段2)						   # 字段2添加唯一索引
    # index 索引名(字段名),						# 添加索引同时添加索引名
    # unique 索引名(字段名)					
);

# 2. 在已有表中添加主键索引
alter table 表名 add primary key(字段名);	# 字段名要存在，否则要提前创建

# 3. 在已有表中添加非主键索引
alter table example add index(字段名);	# 添加普通索引
alter table example add unique [index](字段名);	# 添加唯一索引,index可写可不写，建议写方便记忆
```

- 举例

> ```mysql
> # 1 创建表时创建主索引、普通索引和唯一索引
> create table example(
> 	id int unsigned primary key auto_increment,
> 	name varchar(30),
> 	class varchar(10),
>   	index(name),
>   	unique index(class)
> );
> 
> # 在已有表中添加索引
> alter table example add primary key(id);	# 在已有表中添加主键索引
> alter table example add index(name);	# 为name添加普通索引
> alter table example add unique index(class);	# 为class添加唯一索引
> ```

#### 3.9.2 查看索引

```mysql
desc 表名;
show index from 表名;
```

#### 3.9.2 删除索引

- 删除主键索引

```mysql
alter table 表名 drop primary key;
```

- 删除非主键索引

```mysql
alter table 表名 drop index 字段名;	# 删除字段名索引，可以删普通索引和唯一索引
```


### 3.10 外键约束和表关联关系

#### 3.10.1 外键约束

- 外键约束：是表与表之间一种约束关系，来确保数据的完整性、关联性，如果不设置外键，也可以从应用层加以限制
- 以部门表和人员信息表示例

```mysql
# 建立部门表
create table department(
	id int primary key auto_increment,
    name varchar(20) not null
);
insert into department(name) values("技术部"),("销售部"),("市场部"),("行政部"),("财务部"),("总裁办公室");

# 建立员工表
create table employee(
	id int primary key auto_increment,
    name varchar(30) not null,
    age tinyint unsigned,
    salary decimal(8,2),
    department_id int
);
insert into employee values
(1,"Lily",29,20000,2),
(2,"Tom",27,16000,1),
(3,"Joy",30,28000,1),
(4,"Emma",24,8000,4),
(5,"Abby",28,17000,3),
(6,"Jame",32,22000,3);
```

> 上述两张表没有任何约束，但是每个员工的部门id指向部门表，为了产生关联性，因此可以建立关联关系来保证联系紧密，不会出现数据错乱

- 主表和从表

> **从表的外键与主表的主键相关联**

- foreign key 外键定义语法

```mysql
[constraint 外键名]
foreign key(外键字段)
references 主表名(主表主键)
[on delete {restrict|cascade|set null|no action}]
[on update {restrict|cascade|set null|no action}]
```

- foreign key 外键种类

> 根据delete有三种方式，update有三种方式，可知，**外键种类一共有9种**

* 外键名和索引名关系

  > 外键名称在所有数据库中的所有表中必须是唯一的，索引名称可以在不同的表中重复使用

- 创建表时添加外键

```mysql
create table employee(
	id int primary key auto_increment,
    name varchar(30) not null,
    age tinyint unsigned,
    salary decimal(8,2),
    department_id int
    foreign key(department_id) references department(id)	# 使用系统给定外键名且使用默认的严格关联关系
    # 默认是 on delete restrict on set restrict
    # constraint department_fk foreign key(department_id) references department(id)	# 给外键取名
);
```

- 创建表后添加外键

```mysql
alter table employee add foreign key(department_id) references department(id);	# 使用默认严格模式并使用系统给的外键名

# 或者
alter table employee add constraint department_fk foreign key(department_id) references department(id);	# 默认严格模式
```

- 查看外键名称

```mysql
show create table 表名;
```

- 解除外键

```mysql
alter table employee drop foreign key 外键名;	# 如果未自定义表名需要使用show create table 表名查看外键名
```

- 创建外键同时也会创建普通索引，需要解除普通索引请继续执行

```mysql
alter table employee drop index 字段名;
```

- 级联动作

> 1. restrict（默认）: on delete restrict on update restrict
>
>    > 1.1 主表记录删除时，如果从表有数据则不允许主表删除记录
>    >
>    > 1.2 当主表修改记录时，如果从表有数据则不允许主表更改记录
>    >
>    > 1.3 创建时添加：[constriant 外键名] foreign key(字段名) references 主表名(字段名) [on delete restrict on update restrict]
>    >
>    > 1.4 创建后添加：alter table 从表名 add [constriant 外键名] foreign key(字段名) references 主表名(字段名) [on delete restrict on update restrict]

> 2. cascade（数据级联更新）: on delete cascade on update cascade
>
>    > 2.1 当主表记录删除时，如果从表有数据则从表对应记录也会删除
>    >
>    > 2.2 当主表修改记录时，如果从表有数据则从表对应记录也会更改
>    >
>    > 2.3 创建时添加：[constriant 外键名] foreign key(字段名) references 主表名(字段名) [on delete cascade on update cascade]
>    >
>    > 2.4 创建后添加：alter table 从表名 add [constriant 外键名] foreign key(字段名) references 主表名(字段名) [on delete cascade on update cascade ]

> 3. set null: on delete set null on update set null
>
> 	> 2.1 当主表记录删除时，如果从表有数据则从表对应记录会置为null
> 	>
> 	> 2.2 当主表修改记录时，如果从表有数据则从表对应记录会置为null
> 	>
> 	> 2.3 创建时添加：[constriant 外键名] foreign key(字段名) references 主表名(字段名) [on delete set nullon update set null]
> 	>
> 	> 2.4 创建后添加：alter table 从表名 add [constriant 外键名] foreign key(字段名) references 主表名(字段名) [on delete set nullon update set null]

#### 3.10.2 表的关联关系

> 通常处理的表都是具有复杂关系，如一对多、多对多等

* 一对一关系

  > 操作同一对多类似，注意从表的外键设为唯一即可

* 一对多关系

> A表中一条记录可以对应B表中多条记录；反过来，B中的一条记录只能对应A中的一条记录，如上一个例子中A表是部门表，B表是员工信息表

* 多对多关系

> A表中一条记录可以对应B表中多条记录；反过来，B中的一条记录也可以对应A中的多条记录，如A是学生信息表，B是选课表

```mysql
# 学生信息表
create table student(
    id int unsigned primary key auto_increment,
    name varchar(5) not null
);

# 课程信息表
create table course(
	id int unsigned primary key auto_increment,
    name varchar(5) not null
);

# 选课表
create table student_course(
	id int unsigned primary key auto_increment,
    student_id int unsigned not null,
    course_id int unsigned not null,
    foreign key(student_id) references student(id),
    foreign key(course_id) references course(id)
);
```

#### 3.10.3 R-E模型图

> R-E模型图（Entry-Relationship）即实体-关系模型，用来反映自然界中存在的事物或数据以及他们间的关系

* 实体

> 1. 客观的事物
> 2. 表示方法：矩形框

* 属性

> 1. 客观事物具有的特征
> 2. 表示方法：椭圆框

* 关系

> 1. 实体与实体间的联系
> 2. 一对多关联（1：n）
> 3. 多对多关联（m：n）
> 4. 用棱形表示

#### 3.10.4 多表联查

* **基础概念**
> 1. 具有表间联系的表都可以使用多表联查（不一定非要外键）
> 2. 多表联查与外键约束之间没有必然联系，但基于外键约束的具有关联性的表往往更多使用多表联查
> 3. MySQL的版本不同，多表联查的语法略有差别，旧版本select后的字段必须体现在gourp by 后的字段中，而新版本不用，建议根据旧版本写

* **简单的多表联查**

```mysql
select 字段1,字段2... from 表1,表2... [where条件]
```

> 返回的结果是一个笛卡尔积形式的表的集合，记录数等于所有表记录数的累积，字段数等于所有表字段数的累和

* **内连接**

> 内连接相当于A表和B表的交集，只会查询到A表B表都符合条件的记录，结果等价于笛卡尔积表的where查询

```mysql
select 字段1,字段2... from 表1 inner join 表2 on 表1.字段 = 表2.字段 [where 条件] [group by 分组][having 条件][order by 排序][limit 限制]	# 推荐
# 等价于
select 字段1,字段2... from 表1,表2 where 表1.字段 = 表2.字段 [group by 分组][having 条件][order by 排序][limit 限制]
```

> 内连接也可以自连接，也可以连接2张以上的表，格式为

```mysql
select 字段1,字段2... from 表1 inner join 表2 on 表1.字段 = 表2.字段 inner join 表3 on 表3.字段 = 表1.字段|表2.字段 ...
# 等价于
select 字段1,字段2... from 表1,表2,表3 where 表1.字段 = 表2.字段 and 表3.字段 = 表2.字段|表1.字段 ...
```

* **外连接-左连接**

> 左表内容全部显示 + 与右表匹配的项（匹配不到的自动用null填充）（左表指join关键字左边的表，右表指join关键字右边的表）

```mysql
select 字段1,字段2,... from 表1 left join 表2 on 表1.字段 = 表2.字段 [where 子句][group by 子句][order by 子句][limit 子句]
```

```mysql
# 示例1：查询每个课程的选修人数
select course.name,count(student_course.student_id) from course left join student_course on course.id = student_course.course_id group by course.name;
```

* **外连接-右连接**

> 右表内容全部显示 + 与左表匹配的项（匹配不到的自动用null填充）

```mysql
select 字段1,字段2,... from 表1 right join 表2 on 表1.字段 = 表2.字段 [where 子句][group by 子句][order by 子句][limit 子句]

# 等价于
select 字段1,字段2,... from 表2 left join 表1 on 表1.字段 = 表2.字段 [where 子句][group by 子句][order by 子句][limit 子句]
```

* **适用场景**
> 1. 左连接和右连接可以互相换写，使用时以数据量大的放在左边以提高匹配效率
> 2. 何时使用内连接？何时使用外连接？ --内连接只会显示都能匹配到的项，而外连接可以指定哪一个表全部显示，匹配不到以null显示（注意此时这里不能使用count(字段名)）

### 3.11 视图

> 1. 视图是基于原表存储的查询语句，是一个**虚拟表**，不占据额外空间
>2. 视图的优点是方便用户操作（取所需要的子表），保障数据安全（自定义不同权限单位接触到的子表）,让数据更清晰
> 3. 视图的缺点是效率比原表要差
> 4. 视图基于原表，若原表删除或改名，则视图亦失效

* 创建语法

```mysql
create [or replace] view [视图名] as [select查询语句];
# or replace: 可选，意思是视图是重名的且覆盖原来同名的视图
# select查询语句: 可以是多表联查，多表联查时不要进行写操作
```

```mysql
create view student_class1 as (select * from student where student.class_id = 1);	# 创建学生中是1班的视图（虚拟子表）并命名为student_class1
```

* 视图的CRUD

> 1. 视图表的CRUD与一般操作表相同，但原表的约束条件作用仍在
> 2. 视图只能操作它被分配的字段
> 3. 查询时只能使用自己的字段，插入、删除数据时必须符合原表约束
> 4. 视图是多表联查的结果时，一般只作查询和修改，修改的数据不能同时涉及多张表格（insert、delete百分百涉及多张表，update也要小心）
* 查看现有视图

```mysql
show full tables;	# 查看当前库下所有表以及表的类型（BASE TYPE 或 VIEW）
show full tables in 库名;	# 查看某个库下所有表以及表的类型（BASE TYPE 或 VIEW）
```
* 删除视图

```mysql
drop view [if exists] 视图名;	# if exists防止没有视图名时删除而报错的情况，在任何语句都能适用
```

* 修改视图内容

```mysql
alter view student_class1 as select语句
# 或者重新建一个
```

### 3.12 函数和存储过程

* **基础概念**
> 1. 函数和储存过程是事先经过编译储存好在数据库中的一段sql语句集合
>
> 2. 作用是简化开发工作，提高数据处理效率

* **变量创建和赋值**
> 1. 定义局部变量（一般用在函数体当中）
```mysql
declare 变量名 变量类型	# 靠，与java相反可还行
declare name varchar(30);	
```
> 2. 定义用户变量
```mysql
set @变量名 = 变量值;
declare @name varchar(30);	
```
> 3. 变量赋值
```mysql
set 变量名 = 变量值;
select 语句 into 变量名;
```

> 4. 常见内建函数
```mysql
select now();	# 返回当前的datetime
select database();	# 返回当前的库名
```

#### 3.12.1 函数创建

* **函数注意事项**
> 1. 函数有且只有一个返回值
> 2. 函数的函数体不允许打印结果（如 select * from 表名）
> 3. 函数的函数体中若有修改操作（update、delete、alter、insert），则不能用于where子句，但可以单独调用
> 4. 函数一般用来作为查询中值的提供者

* **函数创建语法**
```mysql
[set global log_bin_trust_function_creators = 1;]	# 8.0版本中先设置全局变量
delimiter $$	# 更改断句符
create function 函数名(形参1 形参类型1,形参2 形参类型2...) returns 返回值类型	# 注意是returns
begin 
	函数体;	# 若干sql语句，但不要直接写查询，注意要以";"结尾
	return 返回值;	# 返回值类型要和定义函数时的返回值类型相同
end $$
delimiter ;	# 改回断局符

select func()	# 调用函数
```

* 示例1：获取id为1的员工的工资

```mysql
set global log_bin_trust_function_creators = 1;	# 8.0版本中先设置全局变量
delimiter $$
create function func() returns decimal
begin
	return (select salary from employee where id = 1);
end $$
delimiter ;

select func()	# 调用函数
select * from employee where salary > func();	# 调用函数
```

* 示例2：获取最大工资和最低工资只差

```mysql
set global log_bin_trust_function_creators = 1;	# 8.0版本中先设置全局变量
delimiter $$
create function func() returns decimal
begin
	declare num_1 decimal;	# 声明局部变量
	declare num_2 decimal;
	set num_1 = (select salary from employee order by salary desc limit 1);	# 赋值语句写法1
	select salary from employee order by salary limit 1 into num_2;	# 赋值语句写法2
	return num_1 - num_2;
end $$
delimiter ;

select func()	# 调用函数
```

* 示例3：创建根据员工id查询工资的函数

```mysql
set global log_bin_trust_function_creators = 1;	# 8.0版本中先设置全局变量
delimiter $$
create function querySalaryById(id_ int) returns decimal	# 参数不能和字段名重名
begin
return (select salary from employee where id = id_);
end	$$
delimiter ;

select querySalaryById(1);	# 调用函数		
```

#### 3.12.2 存储过程创建

* **存储过程注意事项**
> 1. 与函数不同，储存过程函数体可以是任意的，可以有CRUD
> 2. 储存过程没有返回值
> 3. 存储过程的形参分为IN、OUT和INOUT三种
> 4. 相比函数，存储过程用的更多，范围更广，而函数可以作为查询语句的一个部分，针对性很强

* **存储过程形参的分类**

> 1. IN：传入的参数可以在存储过程中使用，存储过程修改IN形参类型不改变外面的值，可以是变量也可以是常量
> 2. OUT：传入的参数不可以在存储过程中使用，若使用只能显示null，但是可以在存储过程中修改，修改后在函数体中可以使用，并且影响到外面的值，只能是变量
> 3. INOUT：兼顾IN类型和OUT类型

* 示例1：使用存储过程修改表
```mysql
delimiter $$
create procedure proc()
begin
	select * from employee;
	update employee set salary = 7777 where id = 2;
	select * from employee;
end $$
delimiter ;

call proc();
```

* 示例2：使用存储过程传入一个IN形参
```mysql
delimiter $$
create procedure proc_in(IN a int)
begin
	select a;
	set a = 666;
	select a;
end $$
delimiter ;

set @num = 1;
call proc_in(@num);	# 这里可以传变量或常量
select @num;	# 1 -->不改变实参变量的值
```

* 示例3：使用存储过程传入一个OUT形参

```mysql
delimiter $$
create procedure proc_out(OUT a int)
begin
	select a;	# 0
	set a = 666;
	select a;	# 666
end $$
delimiter ;

set @num = 0;
select a;	# 0
call proc_out(@num);	# 注意OUT类型只能传递变量不能是常量
select a;	# 666	# OUT类型可以改变传入的参数
```
#### 3.12.3 通用操作

* 调用函数
```mysql
select 函数名();
将函数名()用作一个值
```

* 调用存储过程
```mysql
call 存储过程名;
```

* 查看所有函数和存储过程

```mysql
show function status where db="库名";
show procedure status where db="库名";
```

* 查看创建过程

```mysql
show create {function|procedure} 函数名|存储过程名;
```

* 删除函数或存储过程

```mysql
drop {function|procedure} [if exists] 函数名|存储过程名
```

### 3.13 事务控制

* **定义**

> 1. 数据库做一件事情并进行一系列增删改时结果只能是成功或者所有数据都不影响，这一件事构成一个事务
> 2. 用在处理操作量大、复杂度高的数据
> 3. 作用是确保操作过程中数据完整和使用安全和事务操作过程中各客户端不互相影响
> 4. 若未开启事务，则增删改操作直接作用于数据库
> 5. 可以理解成你将时间停滞，然后做自己想做的事，自己不受影响，别人也看不见，commit是让时间接着流转，这时别人就能看见影响，rollback是将做的事再还回去

#### 3.13.1 事务四大特征

* **原子性（atomicity）**
> 一个事务是一个整体，不可再分割，因为只允许一个事务所有操作全部提交成功，要么失败全部回滚

* **一致性（consistency）**
> 事务完成时，数据必须处于一致状态，数据的完整性约束没有破坏，前后一致不出错

* **隔离性（isolation）**
> 数据库允许多个并发事务同时读写和修改，多个事务相互独立，并且不会由于交叉执行而导致数据不一致

* **持久性（durability）**
> 一旦事务提交，其所做的修改将会永久保存到数据库中

#### 3.13.2 事务语法

* **开启事务**
```mysql
begin;
# 开始执行事务中的若干条SQL语句（增删改）
```
* **结束事务（提交和回滚二选一）**

```mysql
commit;	# 提交事务
```

```mysql
rollback;	# 回滚到初始状态
```

* **事务逻辑**
> 1. 事务未提交或回滚，数据库数据不被影响
> 2. 事务操作只针对数据操作，回滚不恢复数据结构（如删表、删库、删字段等操作是无法恢复的），提交之后也无法回滚
> 3. 一个客户端在事务中操作某记录（该记录自动被上锁），其余客户端对相同记录的操作将被挂起，直到事务结束（解锁）

#### 3.13.3 事务隔离级别

> 1. 事务隔离级别是事务处理最重要的特征，隔离级别的不同带来的操作现象也不同
> 2. 最常使用的是rc级别和rr级别，MySQL使用的是rr级别

* 读未提交：read uncommitted

> 事务A和事务B，事务A未提交的数据，事物B可以读取到
>
> 可能导致"脏数据"

* 读已提交：read committed

> 事务A和事务B，事务A提交的数据，事务B才能读取到
>
> 可能导致"不可重复读"

* 可重复读：repeatable read

> 事务A和事务B，事务A提交之后的数据，事务B读取不到
>
> 可能导致"幻想读"

* 串行化：serializable

> 事务A和事务B，事务A在操作数据库时，事务B只能排队等待
>
> 可能导致效率极低


### 3.14 数据库优化
#### 3.14.1 设计范式

* 种类：第一范式、第二范式、第三范式（前三为常用范式）、巴斯-科德范式、第四范式和第五范式
* 作用：减少数据冗余，更好组织数据
* 特点：高范式兼容低范式，范式越高，表的细分约明显，冗余越小，效率越低（关联难度大）

| 范式 | 说明                                                         | 优化     | 举例                                                         |
| ---- | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| 1NF  | 字段原子性                                                   | 拆字段   | 地址："北京市朝阳区幸福大街33号"   =>   省："北京",  市："北京",  区："朝阳区",  地址："幸福大街33号" |
| 2NF  | 数据库表中所有字段依赖于全部主键，而非部分主键（主要针对符合主键） | 垂直拆分 | 商品id  分类id  分类名 价格  => 这里商品id和分类id是复合主键，但分类名仅仅依赖于分类id，不符合2NF |
| 3NF  | 属性间不互相依赖                                             | 垂直拆分 | 商品id  品名  仓库 负责人 => 仓库和负责人互相依赖，不符合3NF |

* 反范式：
  * 适当的冗余可以增加查询效率（销量表增加销售数量）
  * 还原数据真实性（订单表增加地址信息，防止地址修改导致脏数据）

#### 3.14.2 存储引擎

MySQL由于开源特性，采用插入式引擎，引擎是DBMS中用来处理表的底层控制器

* 基本语法

```mysql
# 1. 查看所有引擎
show engines;
```

```mysql
# 2. 查看已有表的引擎
show create table 表名;
```

```mysql
# 3. 创建表指定
create table 表名(...) engine = MySIAM;	# 默认使用InnoDB，现在指定使用MySIAM引擎
```

```MYSQL
# 4. 已有表指定
alter table 表名 engine = InnoDB;
```

* 常用引擎：InnoDB

> 1. 支持行级锁，仅对指定记录进行加锁，其它进程还是可以对同一个表中的记录进行操作
> 2. 支持外键、事务、事务回滚
> 3. 表字段（数据）和索引同存储在一个文件中（表名.frm 和 表名.ibd）
> 4. 上锁频繁，更适用于写操作

* 常用引擎：MyISAM

> 1. 支持表级锁
> 2. 不支持事务
> 3. 表字段和索引分开存储（表名.frm、表名.myi和表名.myd）
> 4. 上锁频率低，更实用于读操作

* 常用引擎：MEMORY

  > 1. 存储在内存中，读写速度快
  > 2. 不保存数据，断点丢失
  > 3. 适用于临时表或中间表

* 引擎的实际选择

> 1. 原则上根据读写频繁度和事务需求进行选择
> 2. 实际中会在上线前进行模拟仿真，根据测试结果进行选择

#### 3.14.3 表结构优化

* 数据类型的选择

> 1. 数据类型优先程度 数字类型 > 时间日期类型 > 字符串类型
> 2. 同一级别 占用空间小 > 占用空间大

* 键的选择

> 1. InnoDB会自动创建隐含的索引，故InooDB引擎下可以手动对每张表建一个显示主键
> 2. 设置占用空间小的字段作为主键，如int
> 3. 建立外键会自动建立索引，在表关联查询时建议使用外键字段作为关联条件
> 4. 外键可以保持数据完整性，但会降低数据导入和操作效率，增加维护成本和存储空间
> 5. 不使用外键就要从应用层加以限制，确保数据完整性

#### 3.14.4 explain 语句
* 作用
> 1. 使用explain语句可以模拟优化执行器执行SQL查询语句，了解执行过程
> 2. 根据执行过程和返回explairn结果得到查询问题点和性能瓶颈

* 语法
```mysql
explain select 语句;
```

* 结果

> 1. 表的读取顺序
> 2. 数据读取操作的操作类型
> 3. 哪些索引可以使用
> 4. 哪些索引被实际使用
> 5. 表之间的引用
> 6. 每张表有多少行被优化器查询

* explain主要字段解析

> 1. table: 显示这一行的数据是关于哪张表的
> 2. type: 显示使用了何种查询类型，从优到差依次为: system、const、eq_reg、ref、range、index、all，一般来说，查询至少达到range级别，最好能达到ref
> 3. possible_keys：显示可能应用在这张表中的索引，如果为空，表示没有可能应用的索引
> 4. key：实际使用的索引，如果为NULL，则没有使用索引
> 5. rows：MySQL认为必须检索的用来返回请求数据的行数

* type字段包含的值

> 1. system、const：可以将查询的变量转为常量，如id=1，id为主键或唯一键
> 2. eq_reg：索引访问，返回某一行的数据（通常在联接时出现，查询使用的索引为主键或唯一键）
> 3. ref：访问索引，返回某个值的数据，可以返回多行，通常使用"="时发生
> 4. range：这个连接类型使用索引返回一个范围中的行，比如使用">"或">"查找，并且改字段上建有索引
> 5. index：以索引的顺序进行全表扫面，优点是不用排序，缺点是还要全表扫描
> 6. all：全表扫描，应该尽量避免

#### 3.14.5 SQL优化

> 1. 尽量选择数据类型占空间少，在where、group by、order by中出现的频率高的字段建立索引
> 2. 尽量避免使用select * ，用具体字段代替*，不要返回用不到的字段
> 3. 尽量控制使用自定义函数
> 4. 查询最后填LIMIT会停止全表扫描
> 5. 尽量避免NULL值判断，否则否则会进行全表扫描，默认值为空时可以用默认0代替
> 6. 尽量避免or连接条件，否则会放弃索引进行全表扫描，可以用union代替
> 7. 尽量避免使用in和not in，否则会全表扫描，可以使用between代替

#### 3.14.6 表的拆分

* 垂直拆分：表中列太多，分为多个表，每个表是其中的几列，也可以将bolb和text类型的字段放到另一个中（一般在应用层管理）
* 水平拆分：表中记录数量太多，减少每个表的数据量，通过关键字进行划分然会拆分成多个表（一般在数据库运维层管理）

### 3.15  数据库安全

#### 3.15.1 表的复制

复制时仅复制数据，任何键都不保留

可以用复制来进行拆表

* 语法

```mysql
create table 表名 select 语句;	# select 语句能查询到什么就将什么复制给新表名
```

#### 3.15.2 数据库备份

备份前需要退出登陆

* 备份

```mysql
# Ubantu Linux环境下
mysqldump -u 用户名 -p 源库名 > stu.sql;

# 举例
mysqldump -u root -p stu > stu.sql;	# stu是库名，只要存在mysql会自动查找
```

* 恢复

```mysql
create database student character set utf8;
mysql - u root -p student < stu.sql;	# student是新建的一个空的库(注意设置字符集)
```

#### 3.15.3 远程连接

* 服务器端配置服务(Ubantu)
```mysql
# 1、开放允许远程访问权限
sudo vim /etc/mysql/mysql.conf.d
# 然后将bind-address = 127.0.0.1这一句加上注释，保存退出
# archliux需要此配置。
sudo service mysql restart	# 重启mysqk服务

# 2、root身份进入mysql将root用户的host
mysql -u -root -p
use mysql;
update user set host='%' where user='root';

# 3、刷新权限
flush privileges;
```

#### 3.15.4 添加用户

```mysql
# 1、使用root用户身份登陆mysql
mysql -u root -p

# 2、添加用户  本地地址使用"localhost" 如果是修改用户密码使用 alter user ...
create user "用户名"@"host" identified by "密码";

# 3、添加权限
grant {insert|update|select} on 库.表 to "用户名"@"地址" identified by "密码" with grant option;
# 对某个库添加所有权限
GRANT ALL ON 库名.* TO "用户名"@'%';

# 4、删除权限
revoke {insert|update|select} on 库.表 from "用户名"@"地址"

# 5、刷新权限
flush privileges;

# 6、删除用户
drop user "用户名"%"地址"
```

### 3.15  pymysql模块

> 1. pymysql模块是一个第三方模块，需要手动安装
> 2. 作用是使用一个py程序作为一个事务执行MySQL语句（若表的引擎不支持事务则直接操作数据库）

* Ubantu安装方法

```mysql
sudo pip3 install pymysql
```

* 操作流程

> 1. 设置连接参数，生成数据库对象
> 2. 根据数据库对象，生成游标对象
> 3. 编写特殊的sql语句，使用游标对象执行并捕获异常或提交事务，以便回滚（若支持事务）
> 4. 关闭游标
> 5. 关闭数据库对象，断开数据库连接

#### 3.15.1 初始化和退出操作

```python
import pymysql

db = pymysql.connect(填写参数)	# 生成数据库对象
cur = db.cursor()	# 生成游标对象
sql_ = "MySQL语句或带%s的pymysql语句"
try:
    cur.excute(sql_[,(参数)])	# 执行一次
    # cur.excutemany(sql_[, (参数)])	# 循环执行
cur.close()
db.close()
```

#### 3.15.2 pymysql 增删改操作

> 1. pymysql进行写操作如果数据表支持事务会自动开启事务，需要commit提交才能生效
> 2. 如果引擎不持支事务，则直接操作在数据库上

```python
import pymysql

# 生成数据库对象（本地连接为例）
db = pymysql.connect(										
    host = "localhost",
    port = 3306,
    user = "root",
    password = "157299",
    charset = "utf8",
    database = "exercise"
)
# 生成游标对象
cur = db.cursor()											
# 执行语句模板和执行参数
sql_ = "update class set caption = %s where cid = %s;"
# sql_ = "insert into class values(%s, %s);"
excute_args = ("三年3班", 3)
# excutemany_args = (
#	(4, "三年四班"),
#	(5, "三年五班"),
#)

# 执行过程
try															
	cur.excute(sql_, excute_args)
#	cur.excutemany(sql_, excutemany_args)
except Exception as e:
    print(e)
    db.roolback()
else:
    db.commit()
# 断开连接
cur.close()
db.close()
```

#### 3.15.3 pymysql 读操作

```python
cursor_obj.fetchone()	# 读一条记录
cursor_obj.fetchmany(n)	# 读n条记录
cursor_obj.fetchall()	# 读全部记录
for item in cursor_obj:	# 遍历读
    print(item)
```
```python
...(初始化操作同上)
sql_ = "select * from words limit 100;"

try:
    cur.execute(sql_)
except Exception as e:
    print(e, "捕获到mysql执行异常，已回滚数据(你要是删了列或表那就没办法了)", sep='\n')
    db.rollback()
else:
db.commit()

# 取出读到的数据
# 注意cur的读数据是对同一个迭代器进行操作，读完一次，下一次读会从上一次度的结尾开始读
one_res = cur.fetchone() # 读查到的一条记录，查不到结果返回None
many_res = cur.fetchmany(10) # 读查到的自定义条数记录，shape = ((...), (...),...)，查不到结果返回空元组()
all_res = cur.fetchall() # 都查到的全部记录，查不到结果返回空元组()

print(one_res)	# 读第一条
for row in cur: print(row)	# 读第二条
print(many_res)	# 读第3~12条
print(all_res)	# 读第12~100条

cur.close() # 关闭连接
db.close() # 关闭连接
```

#### 3.15.2 文件存储

* 存储文件路径

> 1. 优点：不占据数据库空间，提取方便
> 2. 缺点：文件迁移容易导致内容丢失

* 存储文件本身

> 1. 优点：文件安全
> 2. 缺点：占据空间，存入、读取、修改效率低下

### 4、常见问题

#### 4.1 数据库和数据仓库的区别

|          | 数据库                        | 数据仓库                            |
| -------- | ----------------------------- | ----------------------------------- |
| 数据体量 | 相比较小                      | 海量级，TB以上，分布式              |
| 数据来源 | 较单一                        | 来源多样，数据库、爬虫、网络日至    |
| 技术工具 | MySQL、Oracle、MongoDB、Redis | Java、Hadoop、Hive、HBase、Spark... |
| 应用场景 | 更注重于数据存储              | 更注重于 数据分析和数据挖掘         |

## 4. 练习题

### 4.1 统计连续登陆7天的用户

* 表结构

| id   | datetime            | sign |
| ---- | ------------------- | ---- |
| 1    | 2000-01-01 00:00:00 | 1    |
| 1    | 2000-01-02 00:00:00 | 1    |
| ...  | ...                 | ...  |

* 思路

> 1. 使用DISTINCT DATE(datetime)和sign=1进行数据过滤去重
> 2. 将每个用户的登陆日期生序排列并分区，使用SELECT *, ROW_NUMBER() OVER(PARTITION BY id ORDER BY 日期
> 3. 添加 日期-cum列 用于后期分组聚合统计（同一用户相等的 日期-cum 代表是连续登陆的）
> 4. 统计最终结果 SELECT id, COUNT(id) FROM ... GROUP BY id, 日期-cum HAVING COUNT(id) >= 7

### 4.2 统计每个产品下各个子产品的库存排名

* 数据集

```mysql
create database productdb default charset utf8;
use productdb;
create table product_stock(
id int primary key auto_increment,
product_id varchar(10),
channel_type int,
branch varchar(10),
stock int
)charset=utf8;
insert into product_stock values
(1,"产品id1",1,"子产品1",999),
(2,"产品id1",1,"子产品2",998),
(3,"产品id1",1,"子产品3",1000),
(4,"产品id2",1,"子产品1",800),
(5,"产品id2",1,"子产品2",798);
```

* 思路

> 1. 使用自连接，条件设为产品id相等并且左表库存<=右表库存
> 2. 上述结果每个id对应的count(id)即库存排名结果
