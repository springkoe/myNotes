<h1 align='center'>Redis</h1>

## 一、简介

### 1.1 属性特点

* 开源，支持多语言，使用单进程单线程，基于内存、可选持久化
* 高性能的`key-value`的`NoSQL`数据库（单机dps10w+）
* 数据类型丰富：`string`、`hashes`、`list`、`sets`、`sorted sets`
* 与`MySQL`对比

|            |      `Redis`       | `MySQL`          |
| ---------- | :----------------: | ---------------- |
| 数据库类型 |      非关系型      | 关系型           |
| 存储机制   | 基于内存，可选磁盘 | 磁盘             |
| 是否开源   |         是         | 否               |
| 效率       |         高         | 不如内存型数据库 |
| 应用场景   |     缓存数据库     | 持久数据库       |

### 1.2 应用场景

​	缓存、并发计数、排行榜、生产者消费者模型

### 1.3 附加功能

* 持久化：可选RDB/AOF两种持久化数据方式
* 过期键：可设置指定时间后过期或指定永不过期
* 事务：支持事务，但不支持回滚
* 主从复制（将master中的数据即时、有效的复制到slave中）
* 哨兵模式（主从复制的管理组件）

## 二、安装

### 2.1 下载安装

* 下载`redis`客户端

```shell
# 下载
$ sudo pacman -S redis
# 服务端开机自启
$ sudo systemctl enable redis
# 服务端启动服务 
$ sudo systemctl start redis
# 客户端登陆
$ redis-cli -h 地址 -p 端口
# 修改配置文件
$ sudo vim /etc/redis/redis.conf
```

* `python`中使用`redis`接口：`pip`中搜索并安装`redis`

* `django`中使用的`redis`接口：	`pip`中搜索并安装`django-redis`

### 2.2 基本配置

* 配置文件路径：`/etc/redis/redis.conf`
* 设置密码

```shell
# /etc/redis/redis.conf
requirepass 密码
```

* 允许远程连接

  注释绑定本地地址 >> 关闭保护模式 >> 重启服务

```shell
# /etc/redis/redis.conf
# bind 127.0.0.1 ::1
protected-mode no
sudo systemctl restart redis
```

### 2.3 检查连接

```shell
$ redis-cli [-h 主机地址 -p 端口 -a 密码]
redis 127.0.0.1:6379>ping
PONG									# 出现PONG表示通信正常
```

## 三、通用命令

| 命令                 | 作用                                                         |
| -------------------- | ------------------------------------------------------------ |
| `SELECT 库`          | 切换库，范围0～15                                            |
| `TYPE 键`            | 查看数据类型，一共五种（string 、list、hash、set、sortedlist） |
| `MOVE 键 库`         | 将当前键移动到指定库中                                       |
| `RANDOMKEY`          | 从当前数据库中随即返回一个键                                 |
| `KEYS *`             | 查看当前库所有键                                             |
| `EXISTS 键 [键 ...]` | 键是否存在                                                   |
| `DEL 键 [键 ...]`    | 删除键                                                       |
| `RENAME 键 键`       | 重命名键                                                     |
| `EXPIRE 键 存活秒数` | 为已存在的键设置存活时间秒                                   |
| `EXPIREAT 键 时间戳` | 设置过期时间戳                                               |
| `TTL 键`             | 查看生于存活时间秒                                           |
| `PERSIST 键`         | 设置键永不过期                                               |
| `FLUSHDB`            | 清除当前数据库内容                                           |
| `FLUSHALL`           | 清楚所有数据库内容                                           |

## 四、存储数据类型

### 4.1 string

*  特点
  * 字符串、数字都会转成字符串二进制格式储存
  * 字符串可以包含任何数据，如图片或<font color="red">序列化对象</font>
  * 键可以用冒号如`user:0001`，但不宜过长
  * 值最大存储大小为`512M`
* 应用
  * 缓存：`mysql + redis`
  * 并发计数：将并转串行
  * 有效期服务：短信验证码
  * 作为普通数据库使用
*  命令

| `Redis`命令                       | 作用                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| `SET 键 值 [NX] [EX 存活秒数]`    | 条件设置一个字符串键值对                                     |
| `SETNX 键 值`                     | 不存在时设置字符串键值对                                     |
| `SETEX 键 存活秒数 值`            | 创建字符串键值对并设过期时间                                 |
| `GET 键`                          | 获取键对应的值                                               |
| `MSET 键 值 [键 值...]`           | 同时设置多个字符串键值对                                     |
| `MGET 键 [键...]`                 | 同时获取多个字符串键值对                                     |
| `STRLEN 键`                       | 获取键对应字符串值的长度                                     |
| `GETRANGE 键 起始索引 结束索引`   | 获取键对应<font color="red">闭区间</font>切片的值            |
| `SETRANGE 键 起始索引 值`         | 指定索引处替换值                                             |
| `GETSET 键 值`                    | 设置新值，返回旧值                                           |
| `APPEND 键 值`                    | 如果键已存在且为字符串类型，则在原来值末尾追加新字符串，不存在则创建新键 |
| `SETBIT 键 偏移量 值`             | 设置位图偏移量上的值，值只能是0或1                           |
| `GETBIT 键 偏移量`                | 获取位图偏移量上的值                                         |
| `BITCOUNT 键 [开始索引 结束索引]` | 统计位图1的个数                                              |

| 命令（数字操作）     | 作用                                              |
| -------------------- | ------------------------------------------------- |
| `INCR 键`            | 键对应值+1，值必须是数字                          |
| `DECR 键`            | 键对应值-1，值必须是数字                          |
| `INCRBY 键 变化量`   | 键对应值+变化量，值必须是数字，变化量必须为整数   |
| `DECRBY 键 变化量`   | 键对应值-变化量，值必须是数字，变化量必须为整数   |
| `INCRBYFLOAT 变化量` | 键对应值+变化量，值必须是数字，变化量可以是浮点数 |

### 4.2 list

* 特点
  * 元素只能是字符串结构，可以存放<font color="red">序列化对象</font>，用法如string
  * 数据结构类似于双端队列
  * 列表最大长度2^32^-1
* 应用
  * 消息队列
* 命令

| redis命令                     | 作用                                                   |
| ----------------------------- | ------------------------------------------------------ |
| `LPUSH 键 元素 [元素...]`     | 列表头部添加元素，若列表不存在则创建列表，有先后顺序   |
| `LPUSHX 键 元素 [元素...]`    | 列表存在时才会执行                                     |
| `RPUSH 键 元素 [元素...]`     | 列表尾部添加元素，若列表不存在则创建列表，有先后顺序   |
| `RPUSHX 键 元素 [元素...]`    | 列表存在时才会执行                                     |
| `RPOPLPUSH 键 键`             | 移除列表尾部元素并添加到指定列表头部                   |
| `LSET 键 索引 元素 `          | 根据索引设置元素，键必须存在，索引不可越界             |
| `INSERT 键 BEFORE|AFTER 元素` | 根据指定元素，在其前或后插入新元素                     |
| `LINDEX 键 索引`              | 根据索引获取元素                                       |
| `LLEN 键`                     | 获取列表长度                                           |
| `LRANGE 键 起始索引 结束索引` | 获取列表指定索引范围内元素，LRANGE 键 0 -1表示列表全部 |
| `LPOP 键`                     | 移除并获取列表头元素                                   |
| `RPOP 键`                     | 移除并获取列表尾元素                                   |
| `BLPOP 键 [最长等待秒]`       | 阻塞LPOP                                               |
| `BRPOP 键 [最长等待秒]`       | 阻塞RPOP                                               |
| `BRPOPLPUSH 键 [最长等待秒]`  | 阻塞RPOPLPUSH                                          |
| `LREM 键 标记 元素`           | 标记是索引格式，是整数格式，分3中情况                  |
| `LTRIM 键 起始索引 结束索引`  | 保留索引范围内的列表元素                               |

### 4.3 hash

* 特点

  * 哈希k-v型数据结构
  * k、v均是字符串，适合存放<font color="red">序列化对象</font>
  * 一个hash最多有2^32^-1个键值对
  * 当字段小于512个且v不超过64字节时，自动压缩成ziplist
  * 过期键只能针对整个hash使用
* 应用
  * 用户维度统计（一个用户对应一个hash）


| redis命令                                | 作用                     |
| ---------------------------------------- | ------------------------ |
| `HSET 键 field value [field value...]`   | 设置一个hash             |
| `HSETNX 键 field value [field value...]` | 键不存在时设置           |
| `HLEN 键`                                | 获取字段数               |
| `HEXISTS 键 field`                       | 判断field是否存在        |
| `HGET 键 field`                          | 获取filed对应value       |
| `HMGET 键 field [field...]`              | 获取多个field对应的value |
| `HGETALL 键`                             | 获取所有的field-value    |
| `HKEYS 键`                               | 获取所有filed            |
| `HVALS 键`                               | 获取所有value            |
| `HDEL 键 field [field]`                  | 删除指定field            |
| `HINCRBY 键 field 增量`                  | 对field对应value增量运算 |
| `HINCRBYFLOAT 键 field 增量`             | 浮点增量运算             |

### 4.4 set

* 特点

  * 无序、不重复
  * 元素是字符串类型
  * 最多包含2^32^-1个元素

* 应用

  * 集合运算场景（如求交集）

| 命令                                    | 作用                            |
| --------------------------------------- | --------------------------------- |
| ` SADD key member [member ...]`         | 向集合中添加元素，返回完成数      |
| `SMEMBERS key`                          | 查看集合中所有元素                |
| `SREM key member [member ...]`          | 删除集合元素，返回完成数          |
  | `SISMEMBER key member`                  | 查询集合中元素是否存在，存在返回1 |
  | `SPOP key [count]`                      | 弹出成员，遵从FIFO结构            |
  | `SCARD key`                             | 返回集合元素个数（不遍历集合）    |
  | `SMOVE source destination member `      | 将元素从一个集合移动到另一个集合  |
  | `SDIFF key [key ...] `                  | 求差集                            |
  | `SDIFFSTORE destination key [key ...]`  | 求差集并保存结果                  |
  | `SINTER key [key ...]`                  | 求交集                            |
  | `SINTERSTORE destination key [key ...]` | 求交集并保存结果                  |
  | `SUNION key [key ...]`                  | 求并集                            |
  | `SUNIONSTORE destination key [key ...]` | 求并集并保存结果                  |

### 4.5 sort set

* 特点：
  * 有序、不重复
  * 元素是字符串类型
  * 每个元素关联一个浮点分数值，并按照分数值从小到大==升序==排列集合中的元素
  * 最多包含2^32^-1个元素
* 应用
  * 排行榜

| 命令                                             | 作用                                       |
| ------------------------------------------------ | ------------------------------------------ |
| `ZADD key score member [score member ...]`       | 向有序集合中添加元素，返回成功数           |
| `ZRANGE key min max [WITHSCORE]`                 | 查看索引闭区间内的内容（升序）             |
| `ZREVRANGE key min max`                          | 查看索引闭区间内的内容（降序）             |
| `ZSCORE key member`                              | 查看指定元素分数                           |
| `ZRANGEBYSCORE key min max [LIMIT offset count]` | 查看分数闭区间内的元素，开区间用`(min表示` |
| `ZREM key member [member ...] `                  | 删除有序集合元素                           |
| `ZINCRBY key increment member`                   | 增加或减少分数                             |
| `ZRANK key member`                               | 查询元素索引（升序）                       |
| `ZREVRANK key member`                            | 查询元素索引（降序）                       |
| `ZREMRANGEBYSCORE key min max`                   | 根据分数值删除指定区间内容                 |
| `ZREMRANGEBYRANK key start stop `                | 根据排名删除指定的区间内容                 |
| `ZCARD key`                                      | 查询有序集合元素个数                       |
| `ZCOUNT key min max`                             | 查询分数区间内有序集合元素个数             |

* 并集

```yacas
ZUNIONSTORE destination numkeys key [key ...] [WEIGHTS weight] [AGGREGATE SUM|MIN|MAX]
```

```yacas
ZADD z1 100 a 200 b
ZADD z2 300 c 50 a
ZUNIONSTORE z3 2 z1 z2 WEIGHTS 1 0.5 AGGREGATE MAX
ZRANGE z3 0 -1 WITHSCORE

"a" 100
"c" 150
"b" 200
```

* 交集

```yacas
ZINTERSTORE destination numkeys key [key ...] [WEIGHTS weight] [AGGREGATE SUM|MIN|MAX]
```

* 使用有序集合实现用户浏览记录

```python
# 用户浏览记录
class UserHistory(LoginRequired, View):
    def post(self, request):
        """ AJAX 提交用户浏览记录 
        
        	采用 redis 的 sorted set 存储 库5
            shape = user.id : {sku.id: int(time.time()), ...}
        """
        # 获取数据、校验数据略
        # 使用get_redis_connection()比直接使用caches['browse_history']好
        browse_history_cache = get_redis_connection('browse_history')
        # sorted set 自带升序排序 并且如果新加入数据是之前有的 会覆盖 符合浏览记录特性
        browse_history_cache.zadd(name=request.user.id, mapping={sku_id: int(time.time())}) 
        # -6 -6 保证了仅保留5次记录  
        browse_history_cache.zremrangebyrank(name=request.user.id, min=-6, max=-6)  
        return HttpResponse()

    def get(self, request: HttpRequest):
        """ AJAX 获取用户浏览记录
        """
        browse_history_cache = get_redis_connection('browse_history')
        response = [
            {
                'id': sku.id,
                'name': sku.name,
                'default_image_url': sku.default_image.url,
                'price': sku.price
            }
            # desc=True保证了后浏览的在最顶上 当然，交给前端处理也可以
            for sku_id in browse_history_cache.zrange(request.user.id, 0, -1, desc=True) 
            for sku in (SKU.objects.filter(id=sku_id).first(),) if sku
        ]
        return JsonResponse(response, safe=False)
```

## 五、持久化配置

* 使用前提：需要进行持久化配置redis时使用
* 注意事项：如不需要持久化配置，需要手动修改配置文件

参见：https://my.oschina.net/davehe/blog/174662

### 5.1 RDB

* 简介：RDB 是 Redis DataBase 的缩写，即内存块照
* 优点：性能好、占用空间较少、数据恢复速度快、健壮性好
* 缺点：停机时可能丢失数据较多（根据数据量和配置文件决定）、数据量大时备份进程消耗cpu资源较大
* 配置：

```shell
# /etc/redis/redis.conf
dbfilename dump.rdb					# RDB备份的文件名（默认）
dir /var/lib/redis/					# RDB备份的路径（默认）
save 3600 1							# RDB备份的策略:"N秒内数据集至少有M条改动"被满足时，备份一次数据集（默认系统自定义）
save 300 100
save 60 10000				
```

### 5.2 AOF

* 简介：AOF 是 Append Only File 的缩写，即追加保存
* 优点：耐久性好（停机时不丢失数据或丢失数据更少，根据配置文件决定）、数据易于解析、
* 缺点：占用空间较大、数据恢复速度较慢

* 配置：

```shell
# /etc/redis/redis.conf
appendonly yes						# AOF的开关（默认关闭）
appendfilename "appendonly.aof"		# AOF的文件名（默认）
# appendfsync always				# AOF备份策略，三选一，分别每执行一次命令更新一次、每秒更新一次、系统决定
appendfsync everysec				
# appendfsync no
```

## 六、事务

* 开启事务

```yacaS
MULTI
```

* 执行事务

```yacas
EXEC
```

* 取消事务

```yacas
DISCARD
```

### 6.1 特点

* 单独的隔离操作：事务中的所有命令会被序列化、按顺序执行，在执行的过程中不会被其他客户端发送来的命令打断
* 不保证原子性：redis中的一个事务中如果存在命令执行失败，那么其他命令依然会被执行，没有回滚机制

### 6.2 错误处理

* 语法错误：直接退出事务，所有入队的命令都不执行
* 语法正确，操作类型错误：正确的被执行，从出错处开始不执行

### 6.3 pipline

* 定义：流水线操作，批量执行redis命令以减少io操作（客户端技术）
* 举例：

```python
import redis
# 创建连接池并连接到redis
pool = redis.ConnectionPool(host = '127.0.0.1',db=0,port=6379)
r = redis.Redis(connection_pool=pool)

pipe = r.pipeline()
pipe.set('fans',50)
pipe.incr('fans')
pipe.incrby('fans',100)
pipe.execute()
```

* 连接池：

  避免客户端每次使用redis都创建一个连接带来的巨大开销的技术，让多个客户端共享连接池，减少io操作

```python
import redis
# 创建连接池
pool = redis.ConnectionPool(host = '127.0.0.1',db=0,port=6379)
# 客户端1访问时
r1 = redis.Redis(connection_pool=pool)
# 客户端2访问时
r2 = redis.Redis(connection_pool=pool)
```

### 6.4 乐观锁

* 定义：事件对对象进行监听，再进行修改，最后提交时检查监听后对象的值是否改变，若改变，则事务失败
* 举例：

```python
> watch books
OK
> multi
OK
> incr books
QUEUED
> exec  # 事务执行失败
(nil)


watch之后，再开一个终端进入redis
> incr books  # 修改book值
(integer) 1
```

## 七、Python交互

### 7.1 事务

```python
import redis
# 创建连接池并连接到redis
pool = redis.ConnectionPool(host = '127.0.0.1',db=0,port=6379)
r = redis.Redis(connection_pool=pool)
                
with r.pipeline(transaction=true) as pipe
    pipe.multi()
    pipe.incr("books")
    pipe.incr("books")
    values = pipe.execute()
```



