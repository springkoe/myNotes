# 消息队列

消息队列是分布式系统中重要组件之一，主要解决应用==解耦==、==异步==、==削峰==问题，实现高性能、高可用

## 一、RabbitMQ

MQ全称为Message Queue 消息队列（MQ）是一种应用程序对应用程序的通信方法。MQ是消费-生产者模型的一个典型的代表，一端往消息队列中不断写入消息，而另一端则可以读取队列中的消息。这样发布者和使用者都不用知道对方的存在。

![](https://img2018.cnblogs.com/i-beta/1588750/202001/1588750-20200114204948765-1197950209.png)

### 1.1 安装

* Ubuntu: <a>https://www.jianshu.com/p/5c8c4495827f</a>

### 1.2 运行

#### 1.2.1 简单模式

简单模式下，生产者和消费者公用一个消息队列，当消息被某一个消费者成功消费时，该消息将不会被其它消费者接收到

##### 1.2.1.1 自动应答

自动应答数据不可靠 但是效率肯定更高

* 生产者

```python
import pika
import settings

# 连接RabbitMQ
connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))

# 获取控制对象
channel = connection.channel()

# 创建队列 quque表示队列的名字字符串 如 "hello"
channel.queue_declare(queue=settings.QUEUE_NAME)

# 插入数据
channel.basic_publish(
    exchange="",                    	# 交换机参数为空字符串，表示是简单模式
    routing_key=settings.QUEUE_NAME,    # 指定队列
        body=b"hello world twice"       # 插入的数据
)
```

* 消费者

```python
import pika
import settings

# 连接、获取控制对象
connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

# 创建队列（注意：生产者和消费都要创建，且名字相同，因为不知道哪一个先运行，当检测到已经有了默认不创建）
channel.queue_declare(queue=settings.QUEUE_NAME)

# 回调函数
def callback(ch, method, properties, body):
    print(f"[X] Received {body}")

# 确定监听队列
channel.basic_consume(
    queue=settings.QUEUE_NAME,
    # 默认应答 简单模式下 消费者取走数据后 队列里面的数据就被删除了 相当于剪切
    # 但是消费者程序报错了 获取服务崩掉了 数据便会丢失
    auto_ack=True,
    on_message_callback=callback
)

# 开始消费
channel.start_consuming()
```

##### 1.2.1.2 手动应答

手动应答可以防止回调函数异常时 还能让其余消费者处理

* 生产者（不变）

* 消费者

```python
import pika
import settings

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()
channel.queue_declare(queue=settings.QUEUE_NAME)

def callback(ch, method, properties, body):
    print(f"[X] Received {body}")
    # 发送成功执行信号
    ch.basic_ack(delivery_tag=method.delivery_tage)

channel.basic_consume(
    queue=settings.QUEUE_NAME,
    # 将默认应答该给手动应答 auto_ack=False 并且手动在回调函数里发送信号
    auto_ack=False,
    on_message_callback=callback
)

channel.start_consuming()
```

##### 1.2.1.3 持久配置

持久化配置后 生产者往消息队列推送数据时，同时写入磁盘，当服务宕机重启，数据还能正确被消费者拿到

* 生产者

```python
import pika
import settings

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

# durable=True表示创建的是可持久化队列 这里queue的名字不能和已经使用的非持久化队列名重名
channel.queue_declare(queue=settings.QUEUE_NAME10, durable=True)

channel.basic_publish(
    exchange="",
    routing_key=settings.QUEUE_NAME10,
    body=b"hello world",
    properties=pika.BasicProperties(            # 设置数据可持久化
        delivery_mode=2,
    )
)
```

* 消费者

```python
import pika
import settings

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

# 同样需要指明 durable=True 这里queue的名字不能和已经使用的非持久化队列名重名
channel.queue_declare(queue=settings.QUEUE_NAME10, durable=True)

def callback(ch, method, properties, body):
    print(f"[X] Received {body}")
    ch.basic_ack(delivery_tag=method.delivery_tag)

channel.basic_consume(
    queue=settings.QUEUE_NAME10,
    auto_ack=False,
    on_message_callback=callback
)

channel.start_consuming()
```

##### 1.2.1.4 公平分发

在简单模式下，默认使用轮询分发机制，消息依次分发，如有2个消费者，4个消息，必定是1、3，交给a消费者处理，2、4交给b处理，无论消费耗时多久（荷官发牌不等人理好，只管自己发），通常情况下是谁能力强谁多消费，下面是设置公平分发方法

* 消费者

```python
import time
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()
channel.queue_declare(queue="hello4")

def callback(ch, method, properties, body):
    time.sleep(2)
    print(f"[X] Received {body}")
    # 公平分发要设置手动应答 否则不生效
    ch.basic_ack(delivery_tag=method.delivery_tag)

# 设置公平分发
channel.basic_qos(prefetch_count=1)

channel.basic_consume(
    queue="hello4",
    # 公平分发要设置手动应答 否则不生效
    auto_ack=False,
    on_message_callback=callback
)

channel.start_consuming()
```

#### 1.2.2 交换机模式

交换机是一个容器，一般由生产者创建并向交换机插入数据，且生产者不再创建队列，队列由消费者创建，并将队列绑定到同一个交换机上，共有三种子模式：发布订阅、关键字匹配、模糊匹配

![](https://images2015.cnblogs.com/blog/425762/201607/425762-20160717140730998-2143093474.png)

##### 1.2.2.1 发布订阅模式

发布订阅模式中，生产者向交换机发送数据，每个消费者都创建独一无二的消息队列，并将队列绑定到交换机上，完成消费

* 生产者

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

# 声明一个名为logs的发布订阅模式的交换机
channel.exchange_declare(
    exchange="logs",                    # 交换机名字
    exchange_type="fanout"              # 声明发布订阅模式
)

# 向名为logs的交换机插入b"hello world"数据 并设置了可持久化
channel.basic_publish(
    exchange="logs",                    # 指定插入数据的交换机
    routing_key="",                     # 不指定消息队列
    body=b"hello world",
    properties=pika.BasicProperties(    # 设置数据可持久化
        delivery_mode=2,
    )
)
```

* 消费者

```python
import pika
import settings

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

# 同样要声明一个名为logs的发布订阅模式的交换机 因为启动顺序可能不一致 谁先启动谁创建
channel.exchange_declare(
    exchange="logs",                    # 交换机名字
    exchange_type="fanout"              # 声明发布订阅模式
)

# 创建队列 名字为空且exclusive=True表示让系统取一个随机且唯一的队列名字
result = channel.queue_declare("", exclusive=True, durable=True)
queue_name = result.method.queue

# 将队列绑定到名为logs交换机上
channel.queue_bind(exchange="logs", queue=queue_name)

def callback(ch, method, properties, body):
    print(f"[X] Received {body}")
    ch.basic_ack(delivery_tag=method.delivery_tag)

channel.basic_consume(
    queue=queue_name,
    auto_ack=False,
    on_message_callback=callback
)

channel.start_consuming()
```

##### 1.2.2.2 关键字模式

每个消费者只能拿到匹配到关键字的消息

![](https://images2015.cnblogs.com/blog/425762/201607/425762-20160717140748795-1181706200.png)

* 生产者

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

channel.exchange_declare(
    exchange="logs2",
    exchange_type="direct"              # 声明关键字模式
)

channel.basic_publish(
    exchange="logs2",
    routing_key="info",                 # 声明发布的消息属于哪一个关键字
    body=b"This message's key is info", # 消息内容
)
```

* 消费者（此消费者只接受关键字属于"error"或"warning"的消息）

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

channel.exchange_declare(
    exchange="logs2",
    exchange_type="direct"              # 声明关键字模式
)

result = channel.queue_declare("", exclusive=True)
queue_name = result.method.queue

channel.queue_bind(
    exchange="logs2",
    queue=queue_name,
    routing_key="error"                 # 设置接受关键字为error的消息
)

channel.queue_bind(
    exchange="logs2",
    queue=queue_name,
    routing_key="warning"               # 设置第二个关键waring字
)

def callback(ch, method, properties, body):
    print(f"[X] Received {body}")

channel.basic_consume(
    queue=queue_name,
    auto_ack=True,
    on_message_callback=callback
)

channel.start_consuming()
```

##### 1.2.2.3 通配符模式

关键字模式下，只有生产者标记的关键字和消费者绑定时设置的关键字完全匹配才能接收到消息，而通配符允许关键字中使用通配符完成匹配，"#"代表一个或多个词，"*"代表一个词

![](https://img-blog.csdn.net/20180318124005972)

* 消费者

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

channel.exchange_declare(
    exchange="news",
    exchange_type="topic"                           # 声明通配符模式
)

channel.basic_publish(
    exchange="news",
    routing_key="news.usa",                       	# 声明发布的消息属于哪一个关键字
    body=b"666666666",                              # 消息内容
)
```

* 消费者

```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters("localhost"))
channel = connection.channel()

channel.exchange_declare(
    exchange="news",
    exchange_type="topic"              # 声明通配符模式
)

result = channel.queue_declare("", exclusive=True)
queue_name = result.method.queue

channel.queue_bind(
    exchange="news",
    queue=queue_name,
    routing_key="#.usa"                 # 设置接受关键字为"#.usa"的消息 #代表一个或多个词
)

def callback(ch, method, properties, body):
    print(f"[X] Received {body}")

channel.basic_consume(
    queue=queue_name,
    auto_ack=True,
    on_message_callback=callback
)

channel.start_consuming()
```

