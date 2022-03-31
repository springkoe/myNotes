## 1. 网络编程

### 1.1 基础知识

#### 1.1.1 七层模型

* 全称是Open System Interconnection
* 建立了统一的通信标准
* 每层功能明确，降低了开发难度

| OSI |功能|协议|
|:-----|:------|------|
| 应用层 | 网络服务与最终用户的一个接口 | HTTP、FTP、TFTP、SMTP、SNMP、DNS、TELNET、HTTPS、POP3、DHCP |
| 表示层 | 数据格式化、安全、压缩、加密 | JPEG、ASCII、加密格式等 |
| 会话层 | 建立、管理、终止会话 | 对应主机进程，指本地主机与远程主机正在进行的会话 |
| 传输层 | 协议端口号、流控制、差错校验 | TCP、UDP，数据包离开网卡后即进入网络传输层 |
| 网络层 | 逻辑寻址、路径选择 | ICMP、IGMP、IP(IPV4、IPV6) |
| 数据链路 | 将数据封装为帧 | 用MAC地址访问介质，错误发现但不能纠正 |
| 物理层 | 建立、维护、断开物理连接 | 底层原理... |

#### 1.1.2 四层模型

* 以最重要的两个协议TCP和IP协议为名
* 是OSI七层的简化，因为七层模型实施起来过于复杂
* 常以TCP/IP模型作为工作流程

| TCP/IP | 包含OSI层数              | 协议集                       |
| ------ | ------------------------ | ---------------------------- |
| 应用层 | 应用层 + 表示层 + 会话层 | Telent FTP SMTP DNS HTTP...  |
| 传输层 | 传输层                   | TCP UDP                      |
| 网络层 | 网络层                   | IP ARP RARP ICMP             |
| 链路层 | 数据链路 + 物理层        | 各种通信网络接口（以太网等） |

#### 1.1.3 通信地址

* 组成：ip地址 + : + 端口号
* IP地址：网络中一台计算机的唯一编号
* 地址分类：IPv4 IPv6
* 查看地址：

```shell
$ ifconfig			# Ubantu
$ ip address		# ArchLinux
```

* 连接性检测

```shell
$ ping 对方地址
```

* 公网ip：互联网上所有人都可以访问的地址
* 内网ip：一个局域网内由网络设备分配的ip地址
* 域名：与端口号一一对应，便于访问与记忆
* 端口号：
  * 网络地址的一部分，计算机的一个网络程序对应一个端口号
  * 端口号范围为0～65535，占用2字节
  * 防火墙可以设置开启/关闭某些端口号来提高安全性
  * 常见端口号

| 协议  | 端口号 |
| ----- | ------ |
| HTTP  | 80     |
| HTTPS | 443    |
| FTP   | 21     |
| MySQL | 3306   |
| Redis | 6379   |
| DNS   | 53     |
| SMTP  | 25     |
| POP3  | 110    |

### 1.2 套接字

套接字：实现网络编程进行数据传输的一种技术手段

更多：https://voycn.com/article/jisuanjiwangluoudphetcpshoubuziduanxiangjie

#### 1.2.1 UDP

* UDP：User Datagram Protocol 用户数据报协议
* 传输形式：数据包
* 优点：传输简单、效率高、开销小
* 缺点：可能会造成数据丢失
* 首部信息：2字节源端口号 + 2字节目的端口号 + 2字节UDP长度信息 + 2字节UPD校验和 = 8字节

![](https://img-blog.csdnimg.cn/d6654b0b409848fa91959b01a4cbc450.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZGL5ZaD5ZCW,size_20,color_FFFFFF,t_70,g_se,x_16)

* 代码实现

```python
import threading
from socket import *

"""UPD服务端和客户端交互实例"""

# 定义服务器地址和端口 也可选"127.0.0.1"或本机ip如"76.4.10.25" 当前设定为8888端口
SERVER_ADDRESS = ("0.0.0.0", 8888)


def upd_server():
    """ upd服务端：一创二绑三收发"""
    # 1.创建 ipv4 upd 协议的套接字对象
    udp = socket(AF_INET, SOCK_DGRAM)
    # 2.绑定地址 
    udp.bind(SERVER_ADDRESS)
    # 3.收发消息
    while True:
        # 1024表示接受缓冲区大小为1024字节，超过会会自动抛弃，注意如果发送消息时可能最后字节串因被抛弃部分而decode()错误
        recv_bytes, recv_address = udp.recvfrom(1024)	# recvfrom是一个阻塞函数
        udp.sendto(f"已收到{recv_address}的消息：{recv_bytes.decode()}".encode(), recv_address)
    # 4.关闭套接字
    # upd.close()


def udp_client():
    """udp客户端：一创二发收"""
    udp = socket(AF_INET, SOCK_DGRAM)
    while True:
        msg = input("客户端: ")
        udp.sendto(msg.encode(), SERVER_ADDRESS)
        recv_bytes, recv_address = udp.recvfrom(1024)
        print(f"服务端：{recv_bytes.decode()}")


if __name__ == '__main__':
    threading.Thread(target=upd_server).start()
    threading.Thread(target=udp_client).start()
```

#### 1.2.2 TCP

* TCP：Transmission Control Protocol 传输控制协议
* 传输形式：字节流
* 特点：面向连接的传输服务、三次握手四次挥手
* 优点：传输可靠 无丢失、无失序、无差错、无重复
* 注意事项：
  * python中客户端不允许发送空字节串，若服务端接收到空字节串，则说明客户端意外断开连接
  * TCP是长连接 若要同时处理多个客户端请求 需要IO并发或者多线程
  * TCP是流式传输，没有消息边界，容易产生粘包，需要使人为添加消息边界或控制发送速度
  * TCP如果一端已经不存在，另一端继续发送数据则会引发BrokenPipeError
* 建立连接：

| 三次握手                     | 动作                      |
| ---------------------------- | ------------------------- |
| 客户端发送连接请求           | SYN=1 seq=x               |
| 服务端接受请求，发送可以连接 | SYN=1 ACK=1 ack=x+1 seq=y |
| 客户端发送确认连接           | ACK=1 ack=y+1             |

* 释放连接：

| 四次挥手           | 动作                      |
| ------------------ | ------------------------- |
| 客户端发送断开请求 | FIN=1 seq=x               |
| 服务端发送准备断开 | ACK=1 ack=x+1 seq=y       |
| 服务端发送可以断开 | FIN=1 ACK=1 ack=x+1 seq=z |
| 客户端发送确认断开 | ACK=1 ack=z+1 seq=h       |

* 首部信息：2字节源端口+2字节目的端口+4字节序号+4字节确认序号+...=20字节

| 首部信息 | 含义作用                                                     |
| -------- | ------------------------------------------------------------ |
| 数据偏移 | 等于首部字节数，因为消息包第一个字节偏移到数据部分正好是首部大小 |
| 窗口     | 告知对发下一次允许发送数据大小（字节）                       |
| 序号     | 表示此次发送数据的字节编号                                   |
| 确认号   | 期待下一次接收到TCP数据的字节编号                            |
| ACK      | 为1时表示确认号有效，TCP规定，建立连接后所有报文必须将ACK置1 |
| RST      | 为1时表示连接出错，需要重新建立连接                          |
| SYN      | SYN=1且ACK=0时，表示这是一个建立连接的请求                   |
| FIN      | 为1时表示数据已经发送完毕，要求释放连接                      |

![](https://img-blog.csdnimg.cn/f47d45584565491ea206d706ac76fe1e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZGL5ZaD5ZCW,size_20,color_FFFFFF,t_70,g_se,x_16) 

* 代码实现：

```python
import time
from socket import *
import threading

"""TCP服务端和客户端交互实例"""

SERVER_ADDRESS = ("0.0.0.0", 8888)


def tcp_server():
    # 1、创建套接字 参数默认 AF_INET SOCKET_STREAM
    tcp = socket()
    # 2、绑定地址
    tcp.bind(SERVER_ADDRESS)
    # 3、监听队列大小 5指的是允许5个客户端同时等待3次握手
    tcp.listen(5)
    while True:
        # 4、建立连接
        conn_socket, conn_address = tcp.accept()
        print(f"服务端：已连接上客户端{conn_address}")
        while True:
            # 如果建立连接在内循环中，表示为短连接，每一个会话都要建立连接，早期的网络原理，开销太大
            # conn_socket, conn_address = tcp.accept() 
            # 5、接收消息 缓冲区小于消息长度时，会分次接受，只要资源没有关闭释放，会全部接收,类似于一个生成器
            recv_bytes = conn_socket.recv(1024)
            # 如果客户端断开连接 会自动发送一个空字节串 同时设置断开连接口令
            if not recv_bytes or recv_bytes == b"#exit":
                conn_socket.close()
                print(f"服务端：客户端{conn_address}已退出连接")
                break
            else:
                conn_socket.send(f'服务端："已收到客户端{conn_address}消息"'.encode())


def tcp_client(n: str, t: int):
    tcp = socket(AF_INET, SOCK_STREAM)
    tcp.connect(SERVER_ADDRESS)
    for i in range(t):
        time.sleep(2)
        msg = f"客户端{n}：hello"
        tcp.send(msg.encode())
        print(msg)
        recv_bytes = tcp.recv(1024)
        print(recv_bytes.decode())


if __name__ == "__main__":
    threading.Thread(target=tcp_server).start()
    threading.Thread(target=tcp_client, args=("A", 2)).start()
    threading.Thread(target=tcp_client, args=("B", 4)).start()
```

#### 1.2.3 总结

| 比较点     | UDP        | TCP                     |
| ---------- | ---------- | ----------------------- |
| 连接性     | 无连接     | 面向连接                |
| 可靠性     | 不可靠     | 可靠                    |
| 首部空间   | 小         | 大                      |
| 资源消耗   | 小         | 大                      |
| 应用层协议 | DNS        | HTTP HTTPS FTP SMTP DNS |
| 应用场景   | 视频、游戏 | 下载文件、访问网页      |

### 1.3 传输流程

* 发送短由应用程序发送消息 逐层添加首部信息 最终在物理层发送消息包
* 发送的消息需要经过多个节点（交换机、路由）传输 最终达到目标主机
* 目标主机由物理层逐层解析消息包 最终到应用程序呈现消息

![](https://www.freesion.com/images/695/9109a75fdd596402c33191a186e6ed6f.png)

## 2. 多任务编程

| 名词        | 解释                                                         |
| ----------- | ------------------------------------------------------------ |
| cpu轮询机制 | cpu每次只能执行一个任务，在不同任务间快速切换就是轮询        |
| 多核cpu     | 相当于多个单核cpu的集合                                      |
| 并发        | 多个任务由同一个cpu核心处理，这些任务间的关系就是并发        |
| 并行        | 多个任务由同不同cpu核心处理，这些任务间的关系就是并行        |
| 阻塞        | 任务在无阻塞状态下只有并行才能提高效率；任务在阻塞状态下并行、并发都能提高效率 |

### 2.1 进程

#### 2.1.1 基础概念

> 1. 进程是资源分配的最小单位(线程是CPU调度的最小单位)
> 2. 子进程和父进程是并行还是并发是由系统决定
> 3. 新的进程是原有进程的子进程，子进程复制父进程全部内存空间代码段，一个进程可以创建多个子进程
> 4. 各个进程在执行上互不影响，也没有先后顺序关系
> 5. 不同系统创建进程略微不同，Windows 和 MacOS 中创建进程语句不允许放在全局作用域

| 名词     | 解释                                                         |
| -------- | ------------------------------------------------------------ |
| 进程     | 进程是cpu分配资源的最小单位，是程序在计算机中的一次执行过程，具有一定的生命周期 |
| 三态     | 就绪态、执行态、阻塞态                                       |
| 五态     | 新建态、就绪态、执行态、阻塞态、终止态                       |
| 进程状态 | ps -aux                                                      |
| 进程树   | pstree                                                       |

![](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg-blog.csdnimg.cn%2F20210215121528306.png%3Fx-oss-process%3Dimage%2Fwatermark%2Ctype_ZmFuZ3poZW5naGVpdGk%2Cshadow_10%2Ctext_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTI0NTQ1NQ%3D%3D%2Csize_16%2Ccolor_FFFFFF%2Ct_70&refer=http%3A%2F%2Fimg-blog.csdnimg.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1647872395&t=10d23294ce6ed0e49495239c6d07ac50)

| Linux进程状态（ps -aux） | 含义                                                         |
| ------------------------ | ------------------------------------------------------------ |
| R                        | 运行或就绪态                                                 |
| S                        | 阻塞态（可被中断）                                           |
| D                        | 阻塞态（不可被中断，通常是IO操作，不被中断目的是防止难以挽回性破坏，kill -9杀不死） |
| Z                        | 退出状态（僵尸进程、孤儿进程）                               |
| <                        | 高优先级                                                     |
| N                        | 低优先级                                                     |
| s                        | 标记为session leader                                         |
| l                        | 是一个多进程                                                 |
| +                        | 前台进程                                                     |

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAq4AAAHuCAYAAABeT473AAAgAElEQVR4XuxdCbxN1RdeInPmIVOm6CkyT5XIkDkVFZEkRSUNkqIMhRBF8UcKyZCxwfSeDEmJTJnKPGXIPPNQ+e9vv7fvO3ce3jn3nmGt3+/87hvO2Wfvb597znfWXutbaW4IIzZGgBFgBBgBRoARYAQYAUbA5AikYeJq8hni7jECFkfg6NGjtGHDBlq3bh0dPnxYbocOHZKjKly4MBUqVEhuVapUoUqVKlGBAgUsPmLuPiPACDACjIBRCDBxNQpZbpcRcDgC69evp3HjxtGaNWvCQqJ69erUuXNnqly5cljH8c6MACPACDAC9keAiav955hHyAhEFQF4WPv27esirNnSpaNqt9xCVbNnp1KZMlHBjBmpQIYMsk9Hr16lI4mJtOvKFVp77hz9duECnf/nH/k/ENj+/fuzBzaqs8cnYwQYAUbA3AgwcTX3/HDvGAFLITB//nwaMmQInT9/ngqlT08P589PbcR2iyCvodgFQVqnHztG34rt8LVrlC1bNurZsyc1a9YslMN5H0aAEWAEGAGbI8DE1eYTzMNjBKKFAEhr79695ekeyZuXuhctGjJh9ewjCGy/PXtoyZkz8l8DBw5k8hqtieTzMAKMACNgYgSYuJp4crhrjIBVENCS1oElSlCzfPl06fr848ep9969TF51QZMbYQQYAUbA+ggwcbX+HPIIGIGYIoAkrI4dOyaRSx1JqxqUlrxOmDCBk7ZiOtt8ckaAEWAEYosAE9fY4s9nZwQsj8Bzzz1Hv/32mwwP6FeypCHjQdjANydOyIStzz77zJBzcKOMACPACDAC5keAiav554h7yAiYFgEVIgDlgIUVKkQc0xpsgIh5bfL771JxYOTIkVSnTp1gh/D/GQFGgBFgBGyIABNXG04qD4kRiBYCTzzxBG3fvt2QEAHPMaiQgTJlytDXX38drSHyeRgBRoARYARMhAATVxNNBneFEbASAtBrbdSoEcHbulJUvYqG1RLVt+B1jY+PZ33XaADO52AEGAFGwGQIMHE12YRwdxgBqyAAr+cHH3wQemzr9RPUdf0eWulrgDlK0rK4vJQ7yOC779ghJbJSI4+1c+dOWYIW5WVLly5tFbi5n4wAI8AIMAICASaufBkwAoxARAh0796dlixZElmYwMX9VP5QlpDIqrZzKlygfv36NHz48JD6fUFU4wJR/fHHH2US2aFDh+Rxs2bNYuIaEoK8EyPACDAC5kGAiat55oJ7wghYCgGlJjBBxJxWFuVcw7IIiet6URa2459/UrVq1Wj8+PF+T6m8qsuWLXOVnvXcedOmTWF1mXdmBBgBRoARiD0CTFxjPwfcA0bAkgg0adKEDh8+TPEVK1KBDBnCG0OExPXo1avUaONGKly4MC1YsMB1Tn9eVbXDjRs3KE2aNKQ+8XcmruFNGe/NCDACjIAZEGDiaoZZ4D4wAlFC4JzwWJ49e9a1nT9/ns6ImFF8nj59mvB/kMCrgiBev37d9Xnt2jX5u/q8cuWKq8ebatQIv/cRElecqPzq1fJ8WOpHCIAvryoIqtZAWj1typQplCNHDsouvMXZsmULfwx8BCPACDACjEDUEWDiGnXI+YSMgHEIgHweOXJEekLVJ2I6sUEF4B+Rka+3xYq4Qs8VcavffPON25AUafVFVgONPXfu3JQzZ07CZ6FChbw2/J2NEWAEGAFGILYIMHGNLf58dkYgIgRAShHHuWvXLvpTxHweOHBAktPExMSA7WXJkoVy5colCRq8jZ6f8D5iu/nmmyl9+vSUTkhd4VP7O37OnDkzxTJUAMRy4cKFcqzwEE+cOJFWrlwpMVHmKzxAC065cuWktxlk//Lly0HnIVOmTDJEoUiRInIrVaoU3XXXXVRClLllYwQYAUaAEYgOAkxco4Mzn4URiAgBLM3v3r1bEtQdQgoKxAyfWNr3ZSCmIFUgWCB3+CxYsKD8uUCBApQxY8aI+uHrIDMmZ8GrvGLFCpo8ebL0OmtJrPpZeWI9Y1yPHz8uQyhAZHEstr/++osOHjwoN3/kFoS2gqgaVr58eUlk8Qnyz8YIMAKMACOgPwJMXPXHlFtkBCJGALGlmzdvprVr10rppo0iEcmXISbzjjvuoDvvvFN6/uD1AzmFFzVaZnY5LJD8adOm0dKlS72IvtZjGypeILUq7AKfWm+3ZxtoHwQWG0htXFxcqKfh/RgBRoARYAQCIMDElS8PRiCGCCDm9I8//nAjqiCvWoPXFGVOIZaPDYQV3tNYmypAUF+EHQwXfQrLIkzOirQAAWJh582bJ3VnYcHktMIZy6VLl1wecZS/RegGvORIZlN2yy23SAJbuXJlWfgALxwIuWBjBBgBRoARCA8BJq7h4cV7MwKpRgDL/L/88gv99NNPMi4TMZpaK1mypCRWVUQZVWzR9KKGMzhXyde0aWll1arhHBrxvqkt+QqsEUqAcAt4jI00kFioHsB7vmbNGgLB1VpVgRmIbEUhJ3b33XfLuGE2RoARYAQYgcAIMHHlK4QRiAICSJ4CSYXnD2Tm33//dZ21aNGiVL16dRdRtVL2+hNPPEEgaANFqEKzfPkMRVJVzcKy+4wZMww9l96NY77hiUX4B0gsrgHELytDElzNmjWpQYMGVKdOHY6R1XsCuD1GgBGwDQJMXG0zlTwQMyGAEIDff/9delVBVkFclWGJGN622rVrS5Jy6623mqnrYfVl/vz51Lt3b8omvK4LhefwFkHAjLALAs8mIt73vCCAkMECblY2FcsMEott27ZtrpeZtAJLeNwbNmxIdevWZRJr5YnmvjMCjIDuCDBx1R1SbtDJCGzdulXKNGGD1JIySFDdf//9coNnzU7Lwkpd4NG8eamvCHMwwvrt2UPfnDiha2yqEf2MtE0UfkAIw+LFi2m1KLCg4mNBYhEu8uCDD1K9evWkfBkbI8AIMAJORoCJq5Nnn8euCwLHjh0jeB6R/LNv3z5Xm8j2h2cQZLVs2bJ000036XI+szWyfv166tixo+yWESEDKkQA7aNaFhLU7GwXL16Unnokkv3888+yehkM18+9995Ljz76qLymEF7AxggwAoyA0xBg4uq0Gefx6oIAND1/+OEHSVaRfKMM2f7NmzenFi1aSA1Vp5gKGdCbvGpJ68CBA6lZs2ZOgVSOU11n3333HeEFQRk8+LjGHnnkEUKMNBsjwAgwAk5BgImrU2aax5lqBJBg8+uvv8owAHjDlCcMAvSIRwSpwrJuuKVGU90xkzSgJa8NBLHqKxK2Io15RUxr/7176QdRDECSYQeSVs9pRbU0EFi8LGmLK0CVoGXLljKxS88CEya5rLgbjAAjwAi4IcDElS8IRiAIAli6/fbbb+mrr76iv//+W+6NZVsoATz00EMygYYJQxKIIK9DhgyRgv9I2Govqna1zp8/ZAILwvq1CL2YLEgaErFQaOH999+3fDKWnl8ylLKF9xUENiEhga5cuSKbh1Zsq1at6Mknn6R8Bis86DkebosRYAQYgXAQYOIaDlq8r6MQOHnypCSrc+bMcWmtgkjBu9W6dWtLqwEYOZHQd+3Tp4+UfoKBwFYXJVCrCGJVWmiVFhBlZwtkyCD/d1TEbx5NTKSdIvRindBYXSOSlEBYYcisf++990xRbMFIvFLTNkgrKoPBE6vwRuxro0aNqH379rJYBRsjwAgwAnZCgImrnWaTx6ILAqh69OWXX8qQAMhawZAQBE9W48aN2buqQRke6BMi2x8kHxuUFOChBqE6ePCg1HhFqdRwDAUXgHeRIkUk1lBgwN+gb5tXKBdgw89Zs2YNp1nb74sytJMnT5YkNlG8DMAgu/b000/Tfffd59gQFttPPA+QEXAYAkxcHTbhPFz/CCB+FYQVn8pAVLH8ithVJxqIKMTyd+7cScePH3dtIKtaua9A2GBp29NUHHCg/wXDG7HFWBJXW548eah48eJSwQGKDk41SGvNnDmTpk+fTqdOnZIwFCtWjJ555hl6+OGHnQoLj5sRYARsggATV5tMJA8jcgSgCjBu3DiXOgC8ewgHQFWo/CI+00mGoglbhRj+5k2bZAEFSH0FsjyimEL+9Okpn9jyip/ziM8sIjQgMzYRB5xRfGYSn/hdfeLnjGnSyP9dEGEBV7D99x9dFp+Xxaf8HT8n//2S+NsZUWXqhNiOX79Ox0R4wWFN1Slf/QOpRRlVbOXKlZOfTtNAhRZsfHy8fBnbtWuXhAnXc5cuXWRsNstpOembzWNlBOyDABNX+8wljyRMBOBZHT9+vEtmCEvQL7zwglQHyJAcgxlmk5bbfdWqVYQNJHXLli1e/c8gSGc5sSR/d5YsVEgs24OYgqyCpOaPMUbnRBjHCUHOTggie1J8gtT+KcIUNopYWfzuaZAnK1++vPSeQ8w/u4i7dYpBD3bChAmuax2ybS+++KIksGyMACPACFgJASauVpot7qsuCKAyETysWAKH4SHeqVMnuYxqdy8UYnZRYhQJPUuEDu05kf2vtUKCmFYQSVTlxVYOyVTCc5nOgoUTkPS1WRDYTWLbLMjsnyLm9h/huVXm1LKqSOD63//+RxtF+VwYNGDxsgY5N7sWyNDlpsGNMAKMgGkQYOJqmqngjhiNAAjr2LFjXQ/tQoUKScLqhGVTeNyWL19OCYsW0YVLl1xQ5xIZ6M1EbGhFoZZQXnhWcwviakdLFGEH24RyAcjsUhH3uUWDAcZbo0YNqYOKzQme2F9++UUSWJQohpUUpXpfeuklKe3mVB1iO173PCZGwI4IMHG146zymNwQQBnWQYMGueSCQFife+45WeHKzh5WJFNNmjRJatBe0hC17IKsNhVZ+fUEYYVElRPtiPDIJggCGy+UELYLQqs1ZOC3a9eOatasaWtokBi3YsUKGj16tEy+gyEe+J133qG4uDhbj50HxwgwAtZFgImrdeeOex4EAWRX46E8e/ZsQtUraLB2FokprUXSlZ0JK2SRPv/8c5ovBOqvJ8t55RBktX7OnPQgyKrAIa1IjmJLQuCwILHxgsQuEkoJu5LF/PF3JHQ9++yzVLt2bVt7IUFgUQkOHti9oloZDGEzr776quMS2vg7wQgwAuZHgImr+eeIexgmAojjhBzQmDFjZAUnkFQoBCCbGuTVrgbCCvKxYMEC1xCrivG2u/VWqiNKsLIFR2CvIK4zhTbtXEFirybHxEJaq2vXrrav3oWXu7lz59Knn35KeOmDfi6+M23btrX1i17wq4L3YAQYATMhwMTVTLPBfUk1AojlHDZsGCE8AIaYvTfeeIMQHmBXg1YnYndBOkDa04mBNhae1adE0tkdQg2ALXwEzgscZ4lQi2miCphSKMAyevfu3alixYrhN2ihI9RKxaxZs+g/Qd6RwNWjRw+qVauWhUbBXWUEGAG7IsDE1a4z67BxgbyhPOiPP/4oR45kk379+snlXjsb4lcHDx4sK1WlFQNtLgjri7fdJrVV2VKPwDVB3GYLAjteeLNPJ4ddtGjRgt58803bV+5CBbn3339fSqXB6tSpI0v5omoZGyPACDACsUKAiWuskOfz6oIA4vPmzJlDI0aMoAsiYxxarN26dZNarHaW90EZ1b59+7qIegMRCvCyKJFaVMhXsemPQKIgsDNECMGYw4dlcQR48JHwV6FCBf1PZrIWFwklio8++khWTUOoDVYwQN7ZGAFGgBGIBQJMXGOBOp9TFwQQ04kMaKVJ2aZNG3r55Zcpi82Xxw8L8tSxY0f6WxCpTKL61DuinGczQdjZjEfgr8REemXHDtojPNx4MRo4cCA1adLE+BPH+AxQpRg5ciTNmDFD9qRq1apyhaNgwYIx7hmfnhFgBJyGABNXp824DcaLUpYTJ06kzz77jPAz6rCDQKBGvd1t+/bt1LlzZ4LHtYSoZDVCyBYVFZ9s0UMAmrAf7t9Ps0UCFwwefqgPOMFQtOPdd98lvDRmFNfdy2LsT4oXRjuvbjhhXnmMjICVEGDiaqXZ4r5KwfTevXvTfkEcbhZlR0EYUEQAP9vd1q5dK0Xirwr5pspCf3W0IK3wuLLFBoGvhcf7A3Edwh5//HF5XTrBEoXXGeoVX331lUzeKlOmDA0ZMkQmcbExAowAI2A0AkxcjUaY29cFAWTL42EJTyselsjwhpfVKQ9LJMq0bt1aepjvy5GDPi5dmtJbsBSrLheDiRqZJ7yufYSCBa5JlE6FfJRTbNu2bTJUB9qveHGE4gLCddgYAUaAETASASauRqLLbeuCwJEjR2RCCB6UmUTyEYTRocvqlNKU8HC1bNlSLs+WF/G7X9x1F93MpFWXa0uPRiCZNeTAAdnU5MmTqXz58no0a4k28CKFYhfY8HKJqmNIWnNC2VxLTBB3khGwIQJMXG04qXYaklbuCV5WLEnaWZPV19xBMQGe5nxC4mqWwCCHA8IirHYN9xAlUxefPk1FhLIDrlk7V2bzNTeIvYbHFS9XeYQk29ChQ6ly5cpWm0buLyPACFgAASauFpgkJ3bx4sWLMglk2bJlMvEDWfRYinUaITh58iQ1atRIhgh8IsIDanMFLFN+HS6KhK2mQu/0rJgnLJ8/9thjpuynkZ2CljC+sz/88IM8DWLPnfidNRJjbpsRYASImLjyVWA6BCB4jtCAEyJ+MF++fFJg36neG0gOQae2gkjG+lKECLCZF4G5x45RfxHvmkPEICckJMiseycaPM4ffPABIcQFqySoZHerKDvMxggwAoyAHggwcdUDRW5DNwQmTJgga6Uj2QWVegYMGEC3CNLmRMOD/5577iHUkJ8pKoDdIWrHs5kXARTDaLV5M+0WnkfEeTZt2tS8nTW4ZwcPHqRXXnlFJm5lFtctKnDVr1/f4LNy84wAI+AEBJi4OmGWLTBGCJxjiRWhARkyZJAlNVu1amWBnhvXRZSvxcMf0lcTou5tvUjDV2+lycnDq1WyMo3Kay3Jsa37V9PYLNHt92whkfW+kMhCeAfisZ1skG2Dt3XmzJkShrZt28o42LQs4ebky4LHzgikGgEmrqmGkBtILQLwznTt2pUOiMxsLCmOHj2abr/99tQ2a/nj4aWaPXu2LOXaSZQYjZ4lkVYqW4O6Z00666kT+2l1jmLUFNz1+gnquv4UNa4cl/S7SS0WxPWwIGtNNm6U6herVq1iYX5xbcTHx8uXUsRpV6tWTZaPdeoqikm/KtwtRsBSCDBxtdR02a+zP//8s4xnRWIHykgOHz6cpXSSp7ldu3a0ZcsW+t8dd9C9OXNGb/Iv7qfyh7LQsri8lNvXWZm4BpyLGqJQxBUR3vH99987Rmc42MX5559/ygpjx48fp8KFC0tNZqdoMAfDhv/PCDAC4SHAxDU8vHhvnRBADCseXuPHj5ctdujQQT7YeBkxBeAmTZrQ4cOH6RsR31oimvGtIK5bE2mQL4+q/N/fKZ3MUTKZ4F6nBdvXU6+z6l853I4/dWI71b1UmJZlOUR19yTv5DpWHKPIcuFLVNfV/q00tUYxSinkGzh8QZ5D0/agjHtoUZRDBTD6x0Wc647Llx2n6Rrs1oAyxS+//DJtFvgg7hUvqYjhZmMEGAFGIBwEmLiGgxbvqwsCkLqCl/XXX3+l9EKbFIksDRo00KVtOzVSpUoVuby6RiyvZoxywQEss7cV/LS9JlzAha2XxzWZtGYsS5uKJccWeJBfRSpTYmU9jlGE+NaUNmQfEhUxTiKt+1yxtu7hDEntZ0whusntxSI297UdO2jZmTP0ySefUO3ate10SaZ6LChSgO87lDJgr732mnxpZWMEGAFGIFQEmLiGihTvpwsCx4Rk0HPPPcfxrCGgWaNGDRlCsVqEUGSKQUJLigfT3XvqFeMqSSJ5eEeJtDGmsq1Tud3DD7TH+WpDQ5BrnPU+PqV9kt7e7YVTYnIBbyxiXHHeN0Qxgh9EMYKRI0dKZQw2bwRmzZolZe5AZBs3bkyQfcNLLBsjwAgwAsEQYOIaDCH+v24IoLoOBMlPi4c6x7MGh7VZs2b0119/0cKKFamQUFqIlSkC6/K+enpc/cTESo+pWOiHF1aFCrg8shiMbOcSdUE4gM824FU9RHEiZEESVxUGoAVCemjJtZ82WSxWxPVpUZr49wsXaMqUKVLHlM03AhtFEhtUM86dOyfL5CJ0KGvWZI89g8YIMAKMgB8EmLjypREVBBAWgIcUJHIeeugh6t+/P2dcB0EeS6h4uE8WUljlY6xl60YCfRHXUDyuIsbVjbhqyaov4qohtgV8eWxd+HmrIAhWLL2wi3JHVw4LXYKqANQFFi1aRAULFozK98uqJ0EM9/PPPy9LxZYsWVLGvOfO7TMl0KpD5H4zAoyAzggwcdUZUG7OG4H58+fLUpBIyOrSpYv0urIFRwDVh77++mt6q2hRalOgQPAD9NpDkMiuVwppdFs9iGHIMa4p4QNeXlvyiFn1ikn1iIGV59xDxTUxt1v3b6cDhZIkudzjYSHfleShjXaM62kRk1xPEFd4DleuXKnXjNi6HSRtgbzuELHBBcR1/sUXX1ChqMq/2RpeHhwjYDsEmLjabkrNNSAs/40bN056V6FLiuVvttAQAPGBvm0l4W2dGOUCBG4Z+qK7ngRQJW+RX1UBd0UAFSowlbbKpC+YW5vJHtepuU9RWxUSoEnUkgckk1dFB90TxzxUDcSxOFe0CxDMEgUIBogCBLjOBw4cGNpE8150Wagw4Fpfv3495RTSb5999hmVLl2akWEEGAFGwAsBJq58URiCALyrffv2lVqWqISFRJWaNWsaci67NoqSr7Vq1ZIJLF+XLWvpkq8+Y1y1ExdMO9YCk/xfcsnXPSKhDolHSDpiCx0BKGigYh6q56GAAwqRVK5cOfQGeE9GgBFwBAJMXB0xzdEdJIgWSjuiZGm2bNlk3FpcXFx0O2GTs8Frh5KZdYUX6mNRiMCq5gTiGn/iBPXcs4fy5Mkj41s5Sz78q/WGIP+Qy8I1f/PNN8uSsazMED6OfAQjYGcEmLjaeXZjMDZ4TZCE9csvv8jyrSCtt912Wwx6Yo9TQoEBde+R1DZVeF3LWjTr2u7EFd7WFps20UHhJUd508cee8weF2CMRoHwIoQZIcQIsd74DrAxAowAIwAEmLjydaAbAiBXqIyzZs0aSVYnTpwovU9sqUNgxIgREsviGTPSNCGvlDkGmq6pG4H9j/7kwAH64uhR+bK2YMECSpcunf0HbfAIkZgI0srk1WCguXlGwGIIMHG12ISZtbsgrVAM2LBhgyStkydPlkkWbKlHAEUI2rZtS3vEMvR9OXLQpyJk4KY0aVLfMLegCwIJJ0/Sm7t3y3LFeMGAJimbPggwedUHR26FEbATAkxc7TSbMRoLiNWLL74oSWuJEiVowoQJTFp1ngvoXbZu3ZrOnz9PD+bKRR+UKkXpmLzqjHL4zS0XoRxvCNL6j0hGhORbq1atwm+EjwiIgJa8Dh8+nOrWrcuIMQKMgIMRYOLq4MnXY+iXLonKR8LTunnzZklaJ02aRNmzZ9ejaW7DA4E///xTlsu9IKoywfM6XMgFZRQxgGyxQeC748epn5C+goIGQmQ6deoUm4444KyKvMKrjYQtJq8OmHQeIiPgBwEmrnxpRIwASOuzzz5LIFSlhAcQwuFMWiOGM6QDd+3aJcXakbRVSkgGDRdhA0VF7Ctb9BC4Kojqh4KwzhLEFQbZt0cffTR6HXDomZi8OnTiediMgAcCTFz5kmAELIbAwYMH6ZlnnqGTIrYSHteexYrRo/nyWWwU1uzuLiGU/+bOnbRXqAfAIFfGRTWiN5da8jpmzBiqXr169E7OZ2IEGAFTIMDE1RTTwJ1gBMJD4MyZM9SnTx/66aef5IEVRHWttwSBLZMlS3gN8d4hIXBOyLyNO3SIZgqt1uvC44rSpNAbrVSpUkjH806MACPACDAC+iDAxFUfHLkVRiAmCCxcuFBKBiFpC9Y4d256Rag6FBDVythSjwDCAqaJMq5fHDlCF0RhDdjjjz9Or7/+uqzuxMYIMAKMACMQXQSYuEYXbz4bENg6nMonNKRN3csm47GVhpdvSzR1E7n+5IZU0v8nB0Kv1iBaNqop5XYgwufOnaOxY8fKakOoWgYF0cZCP7dDwYJ0e+bMDkQk9UM+L3CccewYTRek9ZTwtsIqVKggS5LeddddqT8Bt8AIMAKMACMQEQJMXCOCzYCDQObaKmpWiwYuG0XNBAvbOrw8qT/XGrSMRjVV1OwULehal3qtTOlLe1/E79R86lq3N7l280HwtOdIaq294JDdSdFK0Ytk4pjSL9dZZfvx1Aj9JY9zaWFynTep39uLt5darwGt/dRkcovzJ1BDtz5pjkQf+qah/g4lrgoJxL5+/PHHsta7shpC4aGtEMW/nzV1Q/rSIoZ1liCs34qQAHhbYUWLFqXXXnuNHnjggZDa4J2sjwDuiQkNNS/S8v6Md2vtfVEzTrf7t+/xu9+/rY8Rj4ARiBUCTFxjhbz2vF43RUHuhq+mGt3hQUwieosaa0hrMhklNyKbRHK1N9tTC7pS3V4igSSZBMtTinN1PdAhmQAnk19y91Z6H6fxeHoSXy1x1bo7Maaxcd5eUH9/DzgP7HEN5zLdLXRFofCQkJBA//77rzw0m5ARqi/0X+uLUILqgsyyBmwKorsFWV0iVBoWi2S3PclJV/hvXFwcdejQgR588EFZXIBNi4DHi7N6yXR7UfZ4AfYkd14v0aG06TELbm34uE+4Xn6Tj/NFMD33kWPYQcXbTxYv14Fn3eUs8FpFcj8O99S+1F/jeOCriRFgBCJFgIlrpMjpeBwI59g4rTfV+wGRQlx9EFlffZE36H3upNVjP0lQFzX2ucTu/r/kpfz2SV5SN89BWMQ1+cFUPMmT6u3p1XTQ64HEHtdwL7kjIi4Turrz588nSJcpyyJIWD3hgW0oSOx9DvXE7hB4LBVkFYR1jyigobV7772XnnrqKapZs2a4kDtkfx/3IHG/GU7dkxDI6tMAACAASURBVEJ9fHgnk77rHkTWbaUkSJsKWX8vvj5f5n28mHv1Lenetk/jBAh8P/Yzxexxdci1z8M0AwJMXE0wC/Kmvs9fjKbHDT3YkpXLsSDaJLXU7muQyUv2XfzElboR0pQY1IYJHg+gMIhrkidXBC14ejhMMAd27gLiXtesWUNLliyhpUuXEmJilWUScloVhSJBFeGFrSQ+7xKqBOltWNRgv/CkbhQJbOvFtlZsf1+75sIA3tSqVatKz2r9+vVZizjYlyHZI9kl4LK5Zlk9lHtWsDYDEtdAL/PB759uL+nJBLi4DLsKvNLj9gLPHtdgVw3/nxHQDQEmrrpBmZqG1A3SM7YUbbrfeOVNdnsXTWJTBKRUHoJzjqU7tGEEbk1pE6a8f57stjSYHOMaMFQgOfyh4Xaqm5yYFdzjWpQmBUvK8hy+8NRuErGubL4RAIn97bffJInFpiWxOAKktXzWrFRZkNhK2bLJnzNabJn8hhgHYlUVUV0nKo2pBCuFCshqtWrVJFmtV68ek9VwvjBu5M7HgW5ENcQVomBtBiKuQUivGzH1QaJT/l+DVifnDfjMFwgHI96XEWAEDEOAiath0IbbsCa+y22ZPBhxdfcKJHkBKCkByp83NWTiqoitR9Z/8rKYvLkX0CRnhRrj6qYoECJOnIAVIlDh7bZq1SrphUU8LErJ+rK7hRe2iiCxhUSFrjzp01Oem2+mvGLLH2PJrXOChJ8QGf8nrl6lk+LzmPCibr14kdaLcUAVwJdh+b9BgwbsWQ3vMvHaW62e+Ew4ciOHwV6QU5oO2GYg4hosbl7bH0/iqiXM4l42fHVNari9bnKuQAge16KTNEm1oYHKSVqh4cR7MQL+EGDiarprQxFY5X31sdTlK+lJjEMbmxU8TiuyUAElV+WKWVt2B41VqgJhEldX6ICvOfAIJ8C+k4qO8iOXZbpJtGSH9u7dS9u3b6cdO3bQtm3baOvWrXTFI/7Tc2C5BYG9VZDZfGIDmQWxRQxtZmzCe4vKXlnF3/GZMU2apL+LDT/Dk3tBJI9dwSYy+C9rP8XP6vdL4n9nBCk9jk0Q1OOCqB7WLPX7AxvJVdjuvPNOukOUxsXPGbk8rn7Xpiau081DGYS4un/v/Sdw+fR6+iKpoRBXdc/0EYvqeR4vRYEQEeMErBCB4t0YgVQiwMQ1lQAac7jWS+G51OZf89SNrIYQVxY0OcsVkuDrnMneiFq1qJYIW5VyWBEQV5+ZtvCCTCpGozSirgHDCuQk+JDqMmZyHNUqJLZAZqFUcEhUjkLCFz5PCLmoUO3GDSzeu1saQVxhgf4XSvtZhDe4cOHCVKhQISoodGuLFy8uCWqpUqUoQ4w9wqH03xb7JJNBlyfRR6iAz9WfQEv8nm0qoHyR1FSGCnjOQQpx9ZYc1O7rTnix7yQqOsqPXJYtJpoHwQiYAwEmriaYh63Du9L+DhriFyxGTLtUnyK26uZxVbGxvVZ6ezQilcPyKhDg8l74II3+vCCaJAa/HgofxDXwNAXReTXBHNuxCwcOHKCjR4/K7fDhw5LUnhSSUleFR/S68Iwifha/J2okpkLBAYQzjyigkE2EJ9wsvLXYsovkMRBTEFSUW1Ub/s4WewTcYu89Xpr9viCHQjg94/l93leCJ2e5iHMIL/TuxLUv3ejv8VIu4PZeAWLJvthfhdwDpyDAxNUUM+35Zh9CAQLPwgJyHN7JXV7L8T4y+r28mV76ioG9vG0nR05cpcqALwtHeSDYUqEp5tg5nQCR7du3r1QygGVLl46qiWSvqoJklhJlUguK5XpVkvaoILlHBLHdJUIS1gqi+5smPrV69erUv39/SVLZTISA14tlsMx933rRpCWuwdpUw9ddDssb18iIa5D5CaI6YKLZ5a4wAqZHgImr6afIZh1MtcfVl2eDwwTMcpVAM3bIkCF0XkhOFRLxrg/nz09txHaLIK+h2AWRVDUdlavEhjhWeF179uxJzZo1C+Vw3idaCHjGioZQgMBXuI+XpJSreiDew33I+QV8SfW+N3glQoXtcXWvTqiFNxzlgeA5B9GaOD4PI2B9BJi4Wn8OeQSMgCkQAGnt3bu37MsjefNSd1EqNVTC6jkAENh+e/bQkjNn5L8GDhzI5NUUs2z/TqTa4+ojAUxUbfFZ6MX+aPIIGQH9EWDiqj+m3CIj4DgEtKR1YIkS1CxfPl0wmH/8OPUWagdMXnWBkxthBBgBRsDyCDBxtfwU8gAYgdgisH79eurYsWMSudSRtKpRacnrhAkTqHLlyrEdMJ+dEWAEGAFGIGYIMHGNGfSxPzHiEFu2bEnHhVfr2WefpW7dusW+U9wDyyHw3HPPyWpcCA/oV7KkIf1H2MA3QoILCVufffaZIefgRhkBRoARYATMjwATV/PPkSE9/E+IuoNwrFu3jlBNaMyYMaS0NQ05ITdqSwRUiACUAxZWqBBxTGswcBDz2uT332VFrJEjR1KdOnWCHcL/ZwS8EPj222+l4kXmzJlp9uzZUl6NjRFgBKyFABNXa82Xbr0dPnw4TZ48WUoN4QaeVdSkZ2MEwkXgiSeekAUKjAgR8OyLChkoU6YMff311+F2lfdnBCQCffr0oe+++45KitWBadOmcTU1vi4YAYshwMTVYhOmR3fhZUVoAMpfTp8+nUqIuEQ2RiBcBKDX2qhRI6nTurJKlXAPj2j/WuLahdc1Pj6e9V0jQpAPQnGMdu3ayRcuXL+Qb2NjBBgB6yDAxNU6c6VLT1HFqHnz5jKuFUtmjz76qC7tciPOQwBezw8++MDQ2FZPVLvv2CElsiKVxwLZXrFihQyRGTZsmPMmjUcsETgmdIJbtWol9YaHDh1KDRs2ZGQYAUbAIggwcbXIROnVTTzwZ86cKTOzkaHNxghEikD37t1pyZIlEYUJbN2/mtpSWdpULLwQFRUuUL9+fUK4Syi2c+dOQiwuEsj+/PNP1yGbNm0K5XDex6YIJCQk0Jtvvkk5c+aU1weHS9l0onlYtkOAiavtptT/gDZv3kxPPfUUoRb8vHnzKL+oaMTGCESKgFITmCBiTiuLcq7hWKTEdb0oC9tRkM9q1arR+PHjfZ7ygigbu2HDBvrxxx8lsYZXzZcxcQ1nxuy5b9euXWnlypX0yCOPUL9+/ew5SB4VI2AzBJi42mxC/Q3nmiif+fDDD9Phw4fp7bffptatWztk5DxMoxBo0qSJvJ7iK1akAuJlKDS7Tgu2r6deZ1P2bl+2BnUP0fF69OpVarRxIxUuXJgWLFjgakQbAvDDDz+4deXGjRuu37XKGUxcQ5sxO+91QkisoZwwQqgmTpxIlSpVsvNweWyMgC0QYOJqi2kMPgjEcU2dOpXuvvtu+uqrr4IfwHvYAoFzwkN59uxZwqfazoqfL1+6JB/WVwURRLKK9hM/40VHbep37efly5cJhBBEcFONGmFjFanHFScqv3q1PN+sWbN8hgDgf6pv6tNXBx977DFKnz69XIHIlCmTlEjKkSMHZRfeY3xiy5Ytm/ydzb4IqFjtIkWK0Jw5c+T1wMYIMALmRYCJq3nnRreeIa4PHtabb75ZysCwdqFu0Ea9oSNHjtDff/9Np0+flkvgIKWKmOITf8P/zogEJhDVaFisiCv0XBEOsHTpUrdwAH8e1tRgkStXLhkLiU8tscXfFNnNkycPFSxYkIluaoCO0bFQGdiyZQsXYokR/nxaRiAcBJi4hoOWBff9R0gHQTngwIEDhGSa9u3bW3AUzukyli6x/A6CqrZDhw4RNvw9XIPHEMQKBAuES33i78rbiE9seLFRP/v7hDcqnZC/QiJLZKECSSOI1OOqQgXw8rVw4UIXHIrAfv/9924QBSKxiGmEF1l5khEbC/IP4g/SD/KPny8J73Q4Bmxvu+02Gc4ALx4+QWjVZzht8b7RQWDv3r1SZQAGXWuWCIwO7nwWRiASBJi4RoKahY6BVwrqAXFxcVK0natjxXbyQIxASBU5xSc2ENP9+/cH7VxeUVYVZEh5+rRL28oTqD6xj5FmxuQskE/IXS1fvlwmZmlNkVj1HQgnxvXkyZMuz7byZqtPzCl+hicc83jlypWAsIPUKkKLn0FqseFnhCuwxQaB0aNHy3LC5cqVk8VZbrrppth0hM/KCDACARFg4mrjC2T37t2EOD7cgOfOnUtFixa18WjNNTR4uvft20eQYtohtEexQfAcJCeQ5c6dW3rmsKGqmfrEz9jgCTWLmV0OS5FYhMdACsvTwiGu4WAOL616GVGfILQHDx6U+qGBDJ7ksmXL0p133unaWKYpHPQj3xex3lAX+Ouvv6iHkMlq17Zt5I3xkYwAI2AYAkxcDYM2tg3/+++/krTu2bOHXn75ZerUqVNsO2Tjs1+8eFGS0l27dkmCiphi4I4HoafBQwpvm/KygZjeeuutMu4YxBTVzKxiKqmlvvDsDr/jjvC6ff0EdV2/h1aKo8JRFYi0AIFSHfjmm2/kXMGMIq6BgEDCG/oCIgvPO0gSfsYnNl/eWnhiQWbvuusuuXKCkrdZsmQJD2/eOyQEIKP2zDPPyO/ht99+y9XZQkKNd2IEoosAE9fo4h21s40ZM4bGjh0r63Ej+zpt2rRRO7edT4QlYRCeP/74QxJVfGKJ2JcBexCNOwSpwyc2O2Wou0q+imtrZdWqUZl2PUq+ot/QMX7++eej0udwTgISu23bNrfNF5ktVqyYi8yCyOLagjICW+oReO+996S6QPXq1WXoABsjwAiYCwEmruaaD116g0QsJGT9999/krTefvvturTrxEZAVFcL+SV4YrDc7CsOFcv3WnKKn0uVKmUp72mkc/vEE09ID+bAEiWoWb58kTYT0nGqahZI2owZM0I6xuo74TuMxCF48bdu3So3ePU9vfkIB4JHFoUZqoqXiAoVKjCRjXDysYICbVd89wcPHkyNGzeOsCU+jBFgBIxAgImrEajGsE0koED6CmSic+fO9OKLL8awN9Y79alTp2j9+vW0Zs0a2iiE7rHk72kgBsrLVbp0aUlSnWooldm7d2/KJryuC0UhgluE4oARdkHEDDcR83FehMAg4bBOnTpGnMYSbYK0InYa3n4QWcg4IZ4aJFcZFCKg2ayILBKOzBQfbXagUcTijTfekEmQiJG200qJ2bHn/jECwRBg4hoMIYv9HwoCeLBziEBoEweiCpK6bt06ucFbrTXIP8F7BbKKqjpMALxxVeoCjwrFg74iPMII6ydeIL4RUmGBSr0acV6rtAkvIV64sCqA6xlhLJ7XMa5f4IcNL14cPhR4dlU52BYtWhDCB9gYAUbAHAgwcTXHPOjSCw4RCA4jZI3wcAdJXbt2rcz01hqSMioKz2GVKlWocuXKMo4Q3is2/wiAMHXs2FHuYETIgAoRQPsIfYGXmy0wAlCvwHWOaxxE1vOFDMlduMariTjOquITmLJUnjum0FR+6KGHCFXixo0bRzUiqBDH1ykjwAjojwATV/0xjVmLL7zwAq1atYqrv3jMALxPqK6E5T9IhGkNCS0gqsqjCqIKgX228BBQIQN6k1ctaR04cKCMPWQLH4Hjx49LIosNMdueslzQB27QoAHVr19ffh9YwzQJ45kzZxKuOyh/oLgFl4MN/9rjIxgBvRFg4qo3ojFqD5nuqIoFuSUQNKfHsyEzG2QVIvRabxOIKjypyqMKvUwmqvpctFry2kCURu0rErYijXlFTGt/kZT0g9BElWSYSas+k5TcClYasOqgQgugPasMWsIgsdhAYp0eUqDKwXbr1k06BdgYAUYgtggwcY0t/rqdXd1c33nnHanf6jRDUhrI+7JlyyRZ1ZZHBZmvV6+e3CBxw0TVuKsD5HXIkCF0/vx5mbDVXlSEap0/f8gEFoT1ayHSP1lonCIRC+VT33//fUcnYxk3W0ktq+8OXvQSEhLcvLG5xAuI8sTihc+JJBYJcG1FMQJci4sWLZLljtkYAUYgdggwcY0d9rqdGXXaX3nlFbmctWDBAscQMxRZgEyV8qwiJk3rNcKyJ8gqvKtOfODqdoGF2RB0Uvv06eOqVgUCWz17dqpyyy1UWpQ0LSDiiAuIpDfY0atX6WhiIu0UcYTrRLnWNefOScIKQxIRkmJQmIEtOgiAxEKlAC9/ixcvlsUSlCHDHt8pbAitcdJ3CvdX3Ge7dOlCCMliYwQYgdghwMQ1dtjrcmY8aKDZCq3HQYMGUdOmTXVp16yNoJQqkk1AVuFdhdaisnxCRxTeIZBVjtOL/QwiaQtFMHyVWw3UOxBWEAR4+NhiiwC8jQg9wuZrFQPfN5BYu69iQH4MK1mZxYsXsGCva2yvSz67sxFg4mrx+V+4cCG9/fbbVELEE86dO9e2mcF4aCKjHGUYtWQVpVKVZxW6lZwZbb4LGl47kFjEVGIe1YaeYv7UpuKO2cNqvjlEj0BilSdWS2IRTtCqVSu55RdhIXa1Hj16SC804lwR78rGCDACsUGAiWtscNflrBAcR1UXlBwdMWIEPfDAA7q0a5ZGEArw008/ScL6yy+/uLqF2u3Ks4pqQWyMACMQXQRQ/AAkFjGxKFMLgxIB7kGPP/64jCW320skquZB0xWSeSCwXJQgutccn40RUAgwcbXwtTB79myZuIISo5BtsYtBaxXeYxBWyPjAoKX64IMPyuU6hAGwMQKMgDkQgCcd9x+E7yCUB1a0aFFq06aNlC+7RcQ228V69eol8wiefvppev311+0yLB4HI2ApBJi4Wmq6Ujp77do16W0Fyfvss8+kh8PKhlhdxK6CjCN2Fd5WWOHChSVZfeSRR9jDYeUJ5r7bHgFUocP3F5t64YR3EuT1iSeesEXhCIRIYDxITIO3GdJhbIwAIxBdBJi4Rhdv3c42adIk+vjjj2XG/BdffKFbu9Fu6JzIIp83b5702Ci9VSw5ohY9Yubuuece2y05RhtjPh8jEE0E4HVdvnw5zZgxQ1buUoaVEhBYxKRbuRodFDO+++476VF+6623ogktn4sRYAQEAkxcLXgZoC45vK3QysTDIS4uznKjgObqnDlzpC4ivMcwVO+BQkLLli1tneRhucniDjMCESKwb98+eY8C0UPpVBiSufAdx0qKFZO54HVFKVjE8CJswIpjiHA6+TBGwBQIMHE1xTSE14nRo0fL8IC6detKrysbI8AIMAKMQHQRKF++vFwVevfdd6N7Yj4bI+BwBJi4WuwCgBRUw4YN6aoQbscSOzLs2RgBRoARYASiiwDCtBCb//3330tJNzZGgBGIDgJMXKODs25nGTp0KE2dOpWaN29OAwYM0K1dbogRYAQYAUYgdARQ2njatGlSIgsV3tgYAUYgOggwcY0OzrqcBSVNEdsK/VbEVoUu1L6VhpdvS5NdvWhPU5fdQWPr9qaVnj1rP5U2dS8r/upxTK1BtGxUU5I5tKfmU9e6O6jLpu6EPd3tFC3oWpe2d9lEshmXob0EaujzGM82wtlXF2i5EUaAEQgVAXz/+6ah/up+gOO2DqeuBzrQqKa4Q+Ae0Jdu9B9FzTyT7uW9Q9x3xH1mWdxYqtvL6w5E5LoHodmutL+Dj3ZwlgVdaVLRUe73GdW+GgvuW122U922KXe/lH8tS+qv5zGa82Nc5cfGpdz7NBhBRQGrX1BAmT9/PntdQ71+eL9UIYCCLih1ri3oorSUocKjLehSqVKlMHhCqroV1YOZuEYV7tSdrG/fvrJyVOvWrWW1rNBNSwSTfwZx9fHwKZ/QUENcFdEUx3Q9QB00D6qtw8tTW1IkV8tPxY3e1UZw4irb8X6m+B+a9qESOgC8JyPACOiIAEhjX+qfTFSDE9eU77l4aU5+efVLPCcVo1HqrRekUvu7ljj6I67qvqYINoirxz3Jrf9aIu51vqQX8UWNk0muB4YfffQRffnll7LUNkpuszECRiGA6oPjxo2TspHhGKQyO3fubKsS2kxcw7kCYrgvdBHxdg8ZGegH5syZM4ze+CGuQT2u7sS1YeNF1NaXhwQ9kR7ZGrRa42nBw8GnR0X13C8JZY9rGJPLuzICMUAAhG4SFR3VkBLcVnM8u5JCVJNWcVJWXUIirtJ7i/NgdSeJRPq7BbWfKlZ5Cmi8wVriGszjqiW7gih3Ea/lfl+oNfctyPmhMEpiYqJUTihWrFgM5oJPaWcE4GGF00oR1ixZslCFChUIJc6LFy8uVS2UssWxY8cIG9Q8Nm/eTL///jtdunRJwgMC279/f1t4YJm4WuSKHzVqFI0fP17qIKJ6S3jmJ1QgqMdVE14giOlUQVzHar0sqhPJD4hXxP9HJv8fHpaxcVovRQAyiuW4ENyutQb59nqEhwXvzQgwArojEDBUwPP+k/Sii/uJzxdhSQzJI7ypFg1c1p/SiAe4CkFIIb6ae4u/UIFgHlftS7w4/1RBXBMaeoY7iX7jXuXR1ieffCK1tFGGetiwYbpDyw06FwGEoCCWGtKXIKdwXkGKLWvWrCGBAulMJA/C2QVCmy1bNurZs6csomFlY+Jqgdm7fv26FO0+e/ZshG/1yTf2qURt1U3XT5yaV6iAOGasK3bNxwMoGb/2ImGM2mqIrpc3NTBxTYmPQ4Pe+3otTVpg3riLjIAdEVjQtXyS19MjFjRojGvA2PgASLnIYgG32Fm/HlvxQt5F+5Ltg2z6ChXAMQkqZtaTALu6BwLtHnN7+vRpqlevnsw9ANEoUqSIHaedxxRlBHAt9e7dW54VhPW5554LmbB6dhUEFtKZv/zyi/zXwIEDLU1embhG+WKM5HQLFy6UMa2prpKlvYH7uzF7Ek43T0oIvXdLZvBPdNGSXNqTeWDaxA4mriGgzLswArFFwJMMhpCclRQ6JJyt7RtT/+5FaZLfEAMNOVQv2P1vUN9Jaajxvl6+QwU87ltuxNTPio7nCo5PIhwiyvBixcfH0zPPPEOvvvpqiEfxboyAbwS0pLVHjx7yxUgPW7p0KX344YeWJ69MXPW4Ggxu48knn6Rt27bJCw7xVGGbj1iv9iKRYN8iov7axAVPYotQAldGbi2qVWslrfSRBCz7gxhXPFz8qg0E6DWHCoQ9pXwAIxBTBJLvFX6VATSdSyKIRAuG96VFSMQU9xFyS3bysxqjvS9oYui3FyfaF5eSGOZGOL36VUskTRWnXtsbShUDlVDmRWzFSpRrLLXEvU7c6Pzd6tw8zcnjRGnbTp06yaVYlLtNly5dTKeHT25dBJCE1bFjRzkAPUmrQkRLXidMmGDJpC0mria/vkFYQVxz5MhBy5Yto7Rp04bfY1/Zta5QAXg+NElYwgsi0n5TkhwEce3rChXQnNrH8punQkD7qcsobqy/ZApt0oZ2SBovrVaCK/xR8xGMACNgFAI+PK4pYUY+5LCkRzaOivfaLiTxOtABV8IVOuiLuCYnZfVPUj9JWvrvQo0XJVDR5L8lyXF5HKshri7VA9XXDvtTZLzkilM8NcKy/9GkuFUQVy95LdG7UMOUEHt44MABGjx4sJQtZGMEIkEAIQG//fabDA947bXXImki6DEIG0DcKxK2UIXTasbE1eQzpiSwXnjhBerSpUtkvQ1IXOEKUdn/GjKpOSaFuGo0WinpZo8kBvckLG0X/ek5BvKwgDcjU1mQaak1m/xw8dSDjAwJPooRYAT0QMCNuOL7PJbucMV+en7v1e9daEdd9ZIcQCHAUzPalUSaIst3NDn5s8v2uu73n0DEVcQluV6utefQHJNCXFPGVHM1pL9Amtv60KdOAXOKiPP/UBSISXVIlx7zw21YEgEVInDLLbfQxIkTI45pDTZ4xLwirOXChQs0cuRIqlOnTrBDTPV/Jq6mmg73ziCTELEt//zzj/S2hieBpWkrUKjAKHhcx9I+z+U7T7LboQCdOjopRQ/RT8KEVsy71qCp4mY/1ocQuQdx1YiSpxQ/8HzA+fPQmngCuWuMgE0RkPGq27u4sv/lKo2r4IgP4rrgqNA6hVKAryIkARI3tUmkbmQ5mfgW99CSDhAqsEl6XOMJ8U7Ftf31ILv9a4hJW93XR1jB0YBFVCA7BAJw7do1TtKy6XVv9LCgGrR9+3ZDQgQ8+65CBsqUKUNff/210UPTtX0mrrrCqW9jeOMaMWKEjGtVAdWRnMGnl8GVnKUSIbRC23DCJi/xI+mhYYLwropkKhGf5qp85RUPe4huXTmGZrkt7wfSXUwiouJJQG33aapyyQH6epAlhxBwAYJILoGkYwKIuUvMPYpMqBPh+nGXBvJfGcn3vkqHE1Prr0AF/oViFLge4ZnTVnpTPXHP6PY+V/IYQ/bS+x9z5CDb/0hJWhcVp/aiFt8+P8L8vlHwR1BDIa5JL9hJXl11LxhEg5CsRer+obnfeFa/knJ76uU36fgksp0SmoRY3P6CrvZNCqJ1VQbzFQ+b9ILtbX369JHKL22Fwsqbb75p/4uBR6gbAtBrbdSoEUGndc6cObq1G6ihli1bSp1XJBaGXokzKl0LeBImrrGfA589uHHjhoyTwsUMjUAsP+lqfgiE34pYySfXFhVwqQL47ViYoQKudrgAga5zrRqLiLiquUgO35BVjwKU9PR66dAKyPsgrhp1i5Qsb9+E0rP8p2+SLKocUS2RWNPYZ5lOd1yZuEZynS3oOjykggCu1w2X/nLK97pAqMVJPEKWQCzr9iruqr4lz5F8DZEfnWffMaqBK2Kh2ZSYfW8JLH+4bdq0idq3by+TtLBKhoIxbIxAKAjA6/nBBx+EGNt6hpb2aUMfUg+a/l49cpUjOrOE+rQRWsI9ptN79YIXKXr//felRJbV5LGYuIZyRcVgnx9//JFeeeUVKlq0qBQQZmMEIkbAryalnxY1XvOUh752mTQQcU0ip9D0TCIZfnOzhRBFSkEJd3IRGXFNWb4W1Nq1lO3bM5Y08gBybezZj/hyc/qBrVq1ol27dtGAAQOoefPmToeDxx8iAt27d6clS5aEHiaQTFKLjIin5+KSTrJ9fCN6lUZQvPpD9QhBHgAAIABJREFUkHOrcAHoxA8fPjzEnsZ+NyausZ8Dnz1AItavv/5K77zzDj322GMm7SV3y1IIhO1x1SbdJHvLZMJcby+pIP/e98AeV19lgb2KWbhATvZ8IQtcW2lNEO1BxcWSsUfISVLbHt45twljj6ulrl+LdHb27NkETxZKcn711VcW6TV3M7UI1BIyaogXLVSoEN1xxx1UunRpqlSpUsjNKjWBoSLBD9dOKHZmaR9q82ERGhH/HMVtH0+NXv2L3pj+HtUP7myVzaMsLEJaqlWrJitzWsWYuJpwpg4fPkxNmjShTJkyETyvGTNmNGEvuUuWQyCY59VDfkwJxg+c2pji2/ZKJquIE4ScUUrpTdeSPZQmJKHULq0GCRVIBtG7RHBwdNV5OxxQiUI+vKtSCxThjAhx8DQmrsFR5j3CRSAxMVEmaV25coXmzp1LJUuWDLcJ3t+CCCji6dn1woULS0KryCwIra94Ujzz8ez/8ssvZXnX0Gw7jW/0Kv3VYwTVXvEqragdWoiAahtlYJ9++mlCHxcsWBDaKU2wFxNXE0yCZxfwxjVVSKu0adOG3nrrLUN6iJsq2gYxhj377LP00ksvRaYTa0gPuVHdEQjH45qscyk4KDXqrxJitEoPPoirZIa+pJCCJWclLdnvEx6L4l1GkSSi/kIMNEv4bjGufki5e3WkwJXcFN6eFZV0nwduMOoIgEy+++67tHjxYhl3+sYbb1Dr1q0N6wdiBmfOnEnIEu/VS5QLY7M9Av369aNvvvmGkJ+iLE2aND7HDWdUvnz56Pbbb5fl3EFkIU8FQ6JUWCY9rSKZq5pHvGuIjSAhDIb4bKsYE1eTzZT2bR3ZqcWKFdO9h3irgy4sxLKzZ88uFQsgRMxmfQSuXr1KO3fupL/++osOHTpEzz//fMqgwvG4Ck/lcOogCkgogqpNmHMnp+5JUlrNTl/KAFqMk7O8ERM7dp/06NZaWZy6aLyjgcTf3dQyZEnQYjRKk+3tXcIzeNJfqGLz1r9SnDkCaK1+JGL5/v33Xyk1+N577xmilblv3z56+OGHedXMQZcZhPxHjx7tNWJFZEFi8bMvMvvII49I7zz+FzFxpZZJIQNhYs7ENUzAeHdvBFR8VNWqVenzzz/XHaKNGzfSyy+/LIWHK1asKAOyc+dmdX/dgY5CgydPnpQkdceOHTIZ5M8//6T9+/fTf//95zq721t0OB5X2YKWoHoTV6gGTZ4MuqnV2PUnPg+vrdjVS80iKZRAFKEXagCijGfRSTKxa1TTpGvSF5H0qWzhY2yeKgS+ZdbcJ8rzfMgQxzIftri4OLnsy5niUbi4DTzF77//LisSnT59mm677TYaN24cFSxYUPczPvXUUzKGEBJZkB1isy8Cq1atkgUDUPHKHznVEliFRM2aNQlFhuBxTU2ogNDNJHoVIQORhQogLnfhwoWWmSD2uJpsqlTZwGHDhlGDBg107R3kNhCGAG8DbqRI/Lrpppt0PQc3ZgwCu3fvliQVn3/88Yckq3jwehrI1V133UUlBMG6VcRJYRnKVwJU4F5qtX2De1w9qJ8goilhBF7Z/X5KhWrryEtyq02+0pxAmwTmFSoAjys0h9WxiNmFJ1ZbecmnAH7KCTyJa/ny5X1ifOeddxI2xKshdo3j0I257o1q9dSpU1K1ZcuWLXLVacyYMfJ7o6fNmzdP3mNxfSBsgM1eCJw7d056SaG5ihUuRVg9iasvwgoPa+fOnd1iXSNJzpIqAn8lhwhwcpa9LjCrjGbt2rXUqVMnypMnj4zFSps2rS5dB1FFzBW+YOnSpaOePXvS448/rkvb3Ij+CGC+4D3F9YBtw4YNMtFDa7g2EB+Fhy02EFaQqPTp0wfvkHhonxJeduVnx0Pct9c9sMf1hox9xek890sSikepzLqLPPRUvYjrAppfoKnc11VbXjOCYKECrsII/rzJbn8PP1Rg9erVspINXhS2bdsmw2s8DXOBOcAqCbYKFSrIJWI2cyOAioRvv/22K+4VzgI9S19ev36dHnjgAbm6NWXKFCpXrpy5AeHehYQAvKqIZdV6KPG9f/TRR+X1pCWwaFCFBkDbF1545K6gpKunhS2H5UVU/Wi7BhgVy2GFNOW8UyAE+vfvL9/gXnzxRfk2pofhptmtWzdJfvBl+fTTT2WIAJt5EMDSPryp69atk0tN+EQ1E2XwipcoUcJFUrFsDZIatpdP48lM8VyeEg7OvqJiVZLeqruslT/i6oGdqywnqhuJFH6xvD5ZiAv4zuT3jbs/ghoWcXV5VlPO4aUPK/oH3YNAFig56/Lly67wDBBaeL/37NlDICnK8HIIkgKJGRQOgbRN2HNlnsvT9j0ZO3as9LjCXhXLrSpJRo+BI38ApBUeNiTvsFkXAeQMwIOOcDsYQoaaNWsms/KLFxdxU8LUCo3W6xqMsCpEVAGCe++9VyYSBjR/hQa4AIF1LzAr9hzk5b777pOERS8JFXjScBOGlwiFDHBzRiwLW2wRwE0NZGf9+vWSqMKrimUnrcGDCuKDN/nKlSvLMoARmyKsIYjqJyU8qZhV93jVQCEHkvCKdC5XfGqA5f6UcaTExroXOghELkUYw2whz9UK8lyesbXJZYrdgNLuE77HNRTMkVC5detWOY+YTyw/a4ksHnDwwmIuQWRBakPyjIdyct5HFwR++OEH6S3DvCGpCnGHeoRRIea8RYsWso+oUJQ1a1Zd+suNRA8BrIBNFm/i//vf/+jatWtydQpqEVClQJiJ1vB3vNDC8KzFteTPw+o5Ai75Gvqccoxr6FgZuie8bJCk0itI+u+//6YOHTrIkrH33HMPYRksVeTH0NHbv3Hc/EBUly9fLqujHD9+3G3QeGOHsoNabva8IdofIfuMEGEdSIpToR4gtZh/ZRkyZJCrHorIli1bVobwsMUWAcxZ165d6fz58zJkAImreswLnAdY8TIibyG2iNn/7HAw4IUGoUKwJ598UsZG+1tBQZwqVHuwagpvbLimiG+PHj2k6oWRpsIE4CSZMWOGkafSvW0mrrpDGlmDSrsV5BVL+6kxeFhxs4THtXHjxrL+sT89udSch48NjADezhEjCbKKuuVnz551HYAXFEVU4VlFXDObPRHAKgoy2RWRRXiBVvkBL5RIonvwwQepRo0aupAleyJp/KiQZIMwLZAPLNmOHDky1SoS8NaBBIPIINeAzRoIaL3wyPofNGhQ0EpYcBT5Ki4Q6ojnz59PvXv3lmF9UCkwykN/8eJFyREQSohrXM/Y7lDHmpr9mLimBj0dj8WD68SJE7LwADwwkRpiJUF+4TWAcgCkWNiihwBiIH/++WdJVFesWEH4XRnebDHPeJNGzCqbMxHAwwIeOBXTrJYWgQbi4XCNNGzYUHpk9UrQdCbSkY36zJkz1LFjR9q7d698uUReALzkkZpaAsbc4p6gRwhCpH3h40JDQKvJCi8o5NOilXCp1AWgr4qYayPs448/poSEBMuVelVYMHE14qoIs01kK2MJIm/evHIZOVLDUjSqX2Gp0siqW5H2z67HIT4VDyQsvUDPD55WZVgSVmQ1NW/idsWOx0UEPV587xctWiQ9s8py5swpJfHgiUXNcyax0bta8J1GqBXIK7BHAldqyCuSs9AW6sFjhYXNnAhAaQLOHpQ/RWz6kCFDDF+y90QCz3G8OMGMCBlQIQJof9asWTLR12rGxNUEMzZq1Ch5QwN5hVRVJIaEELj+kVzQpUsXWRmLzTgEsNQLkvrtt9/KUADc8GAgF/CUgaxCCodDAIybAzu2jNh0eEJAYiGJpgwJISCw2PAyxKE/xs8+yCtWr1DcA+QVyTmRet0++eQT+uKLL6ht27b05ptvGt95PkPYCGD5HPGrWAlB+A6qYMVKgUeFDOhNXrWkFWErkcThhg2sAQcwcTUA1HCbVBUzIn0bhyg9Kvwglg6B5EbW4A53bHbbH+Ec0PCD8gOWAGF4M0cCHMhq7dq1vTJN7YYBjyc6CBw8eNBFYpEkogzx0Y899pjMWIZXls04BBDWAYcAyCsUIVBlK5KELVTQgoYn6tMjdpLNXAgcO3ZMxjajVC9WPvEsVhJXseqplrxCcQhhA5HGvIKUjxDVtRDGBrMyaUX/mbjG6qpMPq+SS8EbHi6qcOOfkEyAGyLisl5//XWpKcemLwLICF+5cqUs4IA5Uok1KAAAAtG0aVOfgtL69oJbczICeDmFJxZ1zEFo1QsTPLC4BmPlGXLCnIC8Iu4QHnDEpyPRKhKPN1ZgUO0OVbRQTYvNHAhgfvEdgiMCuQeIbwV5NYOBvCJcATkrSNhCkYPmzZuHTGBBWFHBDY4WjBNx1u+//77lkrE854KJa4yvzgkTJsisPnhPUIAgHIOkEpae8AmPKypvsOmHAJZtZ8+eLcMB4GmFQQYFQfO4gfgqB6rf2bklRsA3AoiBQ2waPHcqRKVUqVJypQUqIix7p/+VAwKAUC4otrRq1Sq4QLyPLoAw4H6CPITnn39e/05yi2EjACcEqlXiO3XrrbfKlwqzSRGCUCPuFhrRMHy/8aIKPWgQbXjx84vy3jB4jsEHEE+N8EEUS1DFbBBb/d5776VK9SBsgA06gImrQcCG2my7du3kBRauJAWkleBphfcFJAqC2WypRwBEAIoAeEP99ddfXQ3CQ4IHFsI6Il2uSX3vuAVGIAUByN3hOgWJxQMLhhjMhx56SJZ0xooAm34IAGOQVyTTRZJHgFUb6MSi8h2qJLHFHgElQ4nvDbRMUajHrAZyjSRBRWBD7ScIK65XFLKxizFxjeFMYtkIy0fw4qGqSqixU0jAAmnF0lXdunXpo48+imjpKoZDN92pocQAAgDNReVdxc0MRBUvBqmRKDPdYLlDtkIAL1sIYQEZ0r5sQcoJ2cnQhmXTBwF4suBsgBdrwIABctk2VMM8IVYR9xq8HCPhji12CCAB8q233pLheYhdtoraAzywILFIIoPesNqAJOLf1YaYbJBVO6rZMHGN3fdGvuFB1BhxaqhpHarhy4YvHR5MyHQNlfCG2r6T9kPm8PTp02natGmusqtQAsDDCXFP7F110tVg/bEi5h0vYEggRFwcDPrBILBIHmRJrdTPMSpsAU8sMyMeEioiodobb7whQzwgMg+vOFtsEIDTB84fOIGsntCsqm7WrFlTemSdYExcYzjLiHNas2YNDR48WMamhWJffvml9LAitgVkK1J5llDOZed9sMwKLBHTBA8IDFmk0G5EshWUAtgYAasikJiYKGOzJ02a5FK/KFKkiLy+EUqQPn16qw7NFP2GrBCSYZHsgrhVFWMYrHMqU9xJJCMYJtH+P+73LVq0kOE1dijSg9ABJA9C2WbMmDHRhjMm52PiGhPYSXpDatWqJT0gWObLnDlz0J6gfCgkO3CzhLe2YMGCQY/hHdwROHLkCCEhDg91vG3DEOiOBzqkrCLJFmaMGQGzIoDl6cWLF8trHpJOsFy5csmkTiRz8YpC5DMHwoqEK8S/I8TIX/167RmwwnP//ffL+/5PP/3E+EcOf8RHIksfTh/EgMNxYfVVCMULEIYC7VknGBPXGM0yJCreeecdWQ8by/3BDEuAWFqCJwUac4hfYQsdAcSmff7557IiijJI20A+jNUBQseR97QuAnhBhgg+ys3CQLTgccJLGzKT2cJHQCkFhBPuhSz2tWvXSpkjKJSwRQ8BVaUSZBXOH6hxWN1QCAcFh/BChPLETjAmrjGaZdQ+RoA+1ACQ/BPIQFZRLxmarzgODxq20BBAAhwqk0GDVRnwBoZmziANbXS8FyMQPgJbt26VL78//vij62AsnaJKFH8nwsMT5Z0RK7l9+3bqISpitROe7GA2ZepU+nDoUJn4+cEHHwTbnf+vEwLQ48a9H89RxCijSpYdDC+kkFirU6eOVCdygjFxjcEsX716VXpasVSNcqFYugtk0F4D8ULd8mHDhsWgx9Y7JTD+6quvpJdVxbDiZgUPa44cOaw3IO4xI6AzAtAkRZy39qUOIQQIRzKblqXOQ9e1Oeg9gxDhPgNlh2DFBZAVDk8r8hNAOji5Vtfp8NsYEumwlI6seyQvZsiQITonNvgsCDl5+eWXZXEM5L84wZi4xmCWlyxZIosFILYSyROBDF4RvBmWLl2apkyZYpsvm5GwIwECb54QYobBm4Q30lATKIzsG7fNCJgNAeiSTpw4Ud5fYKjQg+9LmzZtzNZV0/ZHkYfbbrtNqjoEi3dFiAaqoUGGieXKjJ/WQ4cOyecAYr4Rj2yn8DDFEcIJVzEecWPPwMTVWHx9to46wQgKf/HFF6V3w5+pN3l82fCGiDdFNv8IoB445MUgdQJDZRHEEUMOiI0RYAQCI4A4eqzoqBCCkiVLUo8ePQgZ8GzBEfj444+lIyKUKojw/MEDiJcDyBuyGYsACj+gAARC7nr16mXsyaLculK4gBcfcdNOMCauMZhlJFnt2LFDaq75eyggHgfLdiBhIF/QFGXzjQAEmPHQgD4iDLI/8FIjtIKNEWAEwkMAupB4AdyzZ488EPco3IMKFy4cXkMO21t7z8aSLZZu/RnijHF/L1asGH333XcOQyq6w/3jjz/kCwJCM7DaaTclDTz3oA8MGUd8b51gTFyjPMsI5kfhAIhXo8qNPxksJE8gqYj1/vxPECTF4LVAXBnihSETBg823qpZhzXKFzafzlYIgIShnCzuQSgvjThMlDvF98tuD349Jw6JPwgDAEkCfoHUGvAcQOItqiYypnrOgntbWNkExsi8R+lTu5mqAAZ9ZqhcOMGYuEZ5llWVCyzD4cbmyw4ePEiPPPKIvPnhbZxLA3qjhLdMvF1CNQAPVZBV3JRAXtkYAUZAHwQuXrwoV4ZQXQ4hS0hsRCII7k9W17/UByHvViA59sknn8iKWkgO9We4X8F5AdF4iMez6Y+A8rbiuQCCZ8cXBEg8IvwB38l+/frpD6IJW2TiGuVJgRA4EocCVeyAVNPGjRulVAokU9hSEICXFV9OxPXA6tatK5dJOP6XrxJGwDgEoECA+xGIFgxx4yhTjWQkNncEtCED/fv3lzGvvgz63UjOCpbrwPhGjgBWCCDQjxhXVJeyo8G51adPH2rVqhW9++67dhyi15iYuEZ5mpV+KySukOXoaVwS0P+EILgeX1B4WSEhBo8rJ45E+QLm0zkaAYidI4EL8a8oGwtCAB3Tm266ydG4eA5ehQwgFMyfp08pEaCCIkIy2PRF4Pfff5fyh/C2YoUumNKDvmePXmtI3IYzx0mJfkxco3d9yTNBJPjMmTNSJaBEiRJuZ8eyXOPGjaUe4Pfff88lXZPRQZnEwYMH08KFC+Vf4IVGJi5rTUb54uXTMQICAYQMILYcy+DwLkK9A9nMvOrhfnlAYmzEiBF+M9kRO4wy0yBWeCln0xeB9u3b06ZNm+jVV1+lZ555Rt/GTdSaKj2MGPSePXuaqGfGdYWJq3HYerWM7HeQrixZshA8F56GZSXEvaKCTbdu3aLYM/OeSutlRXzdgAEDCB4KNkaAEYgtAlA8wQskvIvwZr3++uuyLHWaNGli2zGTnB0JuAgTQJgFEkjLlCnj1TM8D/BcgKOCq5bpN3HK24qVOTg8kC9iV4O0JiQ227VrJ+XrnGBMXKM4yyr7D1WzEN+kNSy9ofoKslDnzZtn22WNUOGGlxVeHASew1CHGaSVvayhIsj7MQLGI4AKdVjmhqg7rHLlynJ1JFA2vfG9Ms8ZkM2OGFbEBM+YMcOrY2+//bYkVgh7gpwRmz4IINYTLwNwAMERZGfDSxHizxEWgZdHJxgT1yjO8lBRn3qqqFPtKxhfSXZgn4YNG0axV+Y7ldbLCqKKmztCKNgYAUbAnAjAw4XMZngPEdeJJUt/SUnmHIFxvUJS0G+//SbjEJH5rbVp06bJF/TWrVvL+xxb6hG4dOmSdHTA440k3mAl1VN/xti2MEVwig8Fb0A4BMIinGBMXKM4y3Dlb9myxavwAOJwEI8DbwVUB5xqiJ2DcDfIPQwSMfBE5MyZ06mQ8LgZAcsgcPnyZRo+fDgh5k59f7FK4nQ5P4RSYDUNL+Hx8fFuZbvVvR9hBPCcsaUeAeWBhOIMCtPY3bDage+dk0IMmbhG6aoOVHhAEVrUuC5dunSUemSu0yA0ANWuIAOGWunw2DRv3txcneTeMAKMQFAEID+ESlsnTpyQiUfwxDp9xQRLuSBU3bt3l04KZXhZh94rDLhlyJAhKL68Q2AEIMSPuGKEsDghH0IlAT7//PP00ksvOeLyYOIapWlWweKehQdWrFgh43CcLImye/du+YX7+++/pdICBM/z588fpZnh0zACjIDeCEAhBcmmixcvlk2DTGCp3KlFCy5cuCDJOyr6eSYLofQrSsBOmjSJKlasqPdUOKq9zZs3S3m2vHnzSgksJyQKqoIXKGiB6mBOMCauUZrlKVOmSMFubeGBGzduyCWkvXv3yuXxsmXLRqk35jkNbi7wyMAjjaUdeCbsqrdnHtS5J4xAdBCYM2eO/E6jJHOlSpWkPJRTEywhITZ69GgveSYks6Eymac3NjozZK+z4OUIUpNOInGqPDycP/C6OsGYuEZplt98801KSEggbeEBvHkjIN+XykCUuhWz00D/EfFHX331lewDV4+J2VTwiRkBQxFA+A8Kr0C/ukCBArJalBOln+B1rV+/vizcAE+0kmhSJTsffPBB6dxgiwwBhF3gWZqYmCjxdcqqHb5PUClCKeZOnTpFBp7FjmLiGqUJU4UHoNOKcAEYYjgPHjzoOG8r4lnhXVi7dq30riKw/L777ovSTPBpGAFGINoIHD9+XIYD7dy5U6oO4DuP5EunGZJPv/zyS7cSpIjHRCgFktiWLVvmNEh0Gy/UaFDJzWlJzmPGjJHhdU6Q/lIXCxNX3b42/hs6duwY4W1aW3hgzZo10q2PkqW46Jxi2nhWVNrB0lnx4sWdMnweJyPgWATgCevduzctWbJEloiFdA+0J51kp06dks8Cz1KwyHE4f/68ozyFes87VjMRmgIRfiQ8O8XAH0Be2ePqlBmP0jhxo4aHURsSgKUzvF3jgnOK50Ebz4pMWsS7Zc2aNUqzwKdhBBgBMyCgljbRF3ga+/btS+nSpTND16LSB0iEQUFGS7CUjjc80QgnYAsPAWi2PvDAA4Qyuk4KEwBKTFzDu1Z47xARQCwnMkZVHCdkYnBzgscRca5OMCWSjLHCywLpK6dmGDthvnmMjEAgBH766SdC3P+VK1fo7rvvpk8//ZRQ0tkJBvUUKAwg3lfd/9Vyr5NE5PWc63Xr1kkd01KlSrl0hPVs38xtqRdBhEmg2IUTjEMFojDL6m0a8U316tWTnkZor2GpDDcqu5vKesQ44V2BkgIbI8AIOBsBqKng3nj06FH5Eg8h9Tx58jgCFLXiBtJRo0YN+vnnn2UMsNPiM/WabCS1Qbmnc+fO8ppykjFxddJsR3GsLVq0IFRPQa3qYsWKSdkn1PhGqIDdpWFGjhzpqgY2cOBAatasWRSR51MxAoyAmRFAoiZe4Dds2CDJK17onZANrvS7sfKG8AAscdeuXVvqvCJp1Qn6o3pel8ARK5ko8oAqZE4yJbPGHlcnzXoUxgr9Qsg/YXls+fLl0uvYpEkTqW9oZ9OSVtTjbtSokZ2Hy2NjBBiBCBCAjBHIK7LCQVoRVlWwYMEIWrLOIdDwhgMDhHXp0qWUK1cuuRp38uRJ+v777x0pFxbp7O3Zs0eu4qFKG64hpxkTV6fNeBTGCxmYBg0aSO0+vEl36NBBljVFtYsqVapEoQexOcXQoUOlzBeyh4cNGyZvymyMACPACPhCAOQVMa8gcQgXgOf1tttuszVYUFQB6VBi+Yj9R4VFJNtAbYYtNASQ6IaENyc4g3whwgUIQrtOeK8wEFClXrF8gQB86LlCfBtv1XY1hATMnDlTZgojMe3++++361B5XIwAI6ATAliVQqa98kBC79TO5FXJJKI8KYrTvPvuu4RiBJwHEN4FpYr7oGrWI488Et7BNtibS77aYBLNNgRVHQvafQi8R3iAnWNRcNP99ttvpYf5k08+Yc+B2S5I7g8jYGIEQF5RAjo+Pl4un0+YMMHWOs/wtv7666+y8hGcHPDAovoRNDnZQkMAscEIuZg/fz4VKVIktINstBeSGhEnDVUFFCFwgrGqgMGzrNz4CBHYvHmzTEKYPXu2lO2wmynSimpYyHSsUKGC3YbI42EEGAGDEYAmJ0phg7wieRUeJTveLwEjPK3wGLZs2VLKguEe2rRpUxo0aJDBKNujeRXf6uSqY0hIg0MMoSavv/66PSY2yCiYuBo8zaqaB5IPIIOVL18+ghC/3QxvfHjzQ/3tzz//nMqWLWu3IfJ4GAFGIEoIaMnrLbfcQnAA2DFbHLG9kMNCIRaQD3hg8cKPMAm24AjACfT+++/Tww8/TP379w9+gA33UBi0adOG3nrrLRuO0HtITFwNnmaUdUV518cff1zGfT711FP0xhtvGHzW6DaPJRqUckRBAXhHKlasGN0O8NkYAUbAdgiAvMIDiXwAlEhF2IAdyavSdAVxhafZyd7DcC/inj17Ss88PNTwVDvRvvvuO+rTpw+1atVKxkk7wZi4GjzLyHQ8fPgwQRILYQJ2UxOAQgJiaxCbxjqtBl9M3Dwj4DAEIBuFhzLIKzyv06dPt10cI3ICQNBbt24tdUhhv/32G2XIkMFhsx3+cJE7giQ3p8a3AjGVRwO9eKzwOsGYuBo4y/AYwPuImy9uQtig5QqJKDvYgQMHqG3btnThwgVHBYbbYe54DIyAVRDA/RPJStDoRPINCrlkyZLFKt0P2k8UYYDyCsLIsGqFSmLwoqFYDZt/BFTRBuRUYFXTqYbQQ6ziOik2momrgVf7kSNHZE1qJBjg5tSwYUOCvqkdDONB+APqbkNIG7JXbIwAI8AIGIHAlStXqF27drR7924ZEwppQbv+4HrVAAAgAElEQVQ4AIAXQsiQvBsXF0fbt2+X47vnnnuMgNI2ba5atYpeeOEFqlatmoyBdqrBGYYXOyc9h5m4Gni1r1u3Tnoib731VknwEL+E5SCr2/Xr1+mZZ56hLVu2ULly5WT4Ay9rWX1Wuf+MgLkRQDEXxPHhpfnJJ58kxDfaxZDQ+umnn1Lp0qVp586dMjwCSgNs/hFAzDOqMzopm94XGuvXr6eOHTtS9erVpZyaE4yJq4GzjLgsBEsj2P7UqVNyiQtv1FY3JfgMQo6EM3iU2RgBRoARMBoBeCXxkMbLM7LJH3roIaNPGZX2t27dKsOuChUqJHMinKTJGSnAKFaxePFiGjx4sFzZdKrBQ//EE09IJ9KUKVMcAQMTVwOnGcs92BC3hDgcLG2kSZPGwDMa37TSpYXsFYg4qoCxMQKMACMQLQQWLVokZX9wX8X9CIVdrG6Qxbr33nslIcfPSOoFIWPzj4BKfHZ6PPDBgwepefPmslAHEv2cYExcDZxleFvxpQJZrVWrFo0aNcrAsxnf9PLlywl6tIgtQ4EBxBaxMQKMACMQbQRQlQ8hSlAaQK36AgUKRLsLup/vueeek2oCSEYrX748ffXVV7qfwy4NXrx4URJ9pydmYT6xmov4VrtqxPu6Zpm4GvhNRhwoJLBgr7zyilzisqph+Qp1oK9evcq1tK06idxvRsBGCKB0NpQG4GmaOnWq5ZUGUPYVDgEQV4SXwVHA5hsBEHwQfXjbEevqZEtMTJTxrU4i8UxcDbziGzRoQEgogKESilVLoOJGiqxXJGNhSWLAgAEGosZNMwKMACMQHAFPpYGxY8daOhRLZcmrkbOWq/9rQJU5RWxnr169gl8sNt8DShv4PkBhwAk5J0xcDbqglYarah6Zf+nSpTPobMY2O3HiRFe5WsTQ2ElD0VjkuHVGgBEwEgE4BkBeTp8+Td26dZNJTVa1S5cu0X333Ud4dsBZgDAzeJPZvBGArCS87D3efJPaiaQ2pxtWQ/fu3Utz5syh22+/3fZwMHE1aIpxQ61fv770ACBTFNUtrGgoMvDoo4/KhAEQWFQAY2MEGAFGwCwIKNlBOAaQMGrlBzdIOLLEYaNHj5ZEls0bgZdeeol+/vlnKSGG4g1Oty5dutCvv/5KWHWoWbOm7eFg4mrQFCPTr1mzZpZOzEIZV+gl4kZqN91Eg6adm2UEGIEYIPDBBx/IcqklS5aUyVpQHLCi9e/fn+bOnSs9rqr+vBXHYXSfEbKGZ6zTFQUUzrhWgIWdJOICXUNMXA36hu3YsYMee+wxSVzbt29P3bt3N+hMxjWrkgUgeTV79mxKnz69cSfjlhkBRoARiBABJKhgZQhJpJ07d6YXX3wxwpZie9jkyZNp+PDhkrgi7AFJvWzuCGjD8DZu3GirCmqRzjU8zyhigQpanTp1irQZyxzHxNWgqdq0aZNMaAJx7devn8zIt5LBy9qmTRvZ5enTp9uicIKV8Oe+MgKMQHgIoDgB7rmQ65s2bRqVKVMmvAZMsDdUEqCWAOIKIo5nB5s7Akq3tHDhwrRgwQKGRyCAQkADBw6U8d5OSFZj4mrQZb969Wp6/vnnJXGdNGkSVaxY0aAz6d/stWvXZGlFxLdiDIgnYmMEGAFGwOwIjBgxQsbiY5UIS+5WS4g9dOgQNW3aVBJXLkLg+2pDbCueSdBxxaogG0npNGisQ8/1448/tj0kTFwNmuIff/xRZrmCuK5YsYJy5Mhh0Jn0bxZLVViyQnlaeC6sGi+mPzLcIiPACJgZASSRPv7447Rnzx6CjjYe5lYzaJNiHEg6whIwmzsCSgoLK4KooMZGpEoGI8YbL2x2NyauOs3wL7/8QkuWLJHC0Xny5KFt27bJ8msgrogPLV26tE5nMraZ/fv3U4sWLejmm2+W0hpc0tVYvLl1RoAR0BeB3bt3y/wCxEKi+tTdd9+t7wkMbg1JvX/99Zejas+HA+lHH30kddFff/11evrpp8M51Lb7nj9/XlbnRJjM2rVrLbfSEO7EMHENFzE/+6us1lCaw1IHShWa0VTZQQR4I9CbjRFgBBgBqyEAWaAxY8ZIKUI4EKyUWKqkjYoVKyYzxdncEUAMJ2Jb8cxFOAVbEgKq4BEcZaVKlbI1LExcdZpeFBjA0hQ8rIhP0hr+pgyl2T777DOdzqpvM4jLRUYuKm8sWrSICw3oCy+3xggwAlFCQCvlByL4wgsvROnMqT8NErK++eYbR9WeDwc15F2sWbOGxo8fT9WqVQvnUFvvC0cTKmcNHjyYGjdubOuxMnHVcXrxJbp69aokrlqyilOov40cOZLq1Kmj41n1a6ply5aEZba3336bWrdurV/D3BIjwAgwAlFGAMooyLLOkCEDJSQkUM6cOaPcg8hOh8IDcG5ky5aNoDLA5o6AqhIFcl+iRAmGJxkBJYmFVVMoU9jZmLjqOLvwuG7YsMGLuCrSauYb0fz586l3795yae3777+3fYyMjtPOTTECjIBJEejZsyfFx8fLmNd33nnHpL107xaWeiEknzFjRulZZHNHANXELly4IL2LWB1kS0IAL2dvihK4cIzBQWZnY+Kq4+wq8ocmtV5X9TM8mqhwYTa7fv26jBVCmdphw4bJWBk2RoARYASsjsDRo0dd8lKIF73ttttMPySl5Qo1FzhC2FIQQAgIyo5D5gzheWwpCOzdu1fqxVu5xHyo88nENVSkQtgPb4HQllNxrtp4V/yMN/8CBQqE0FJ0d4HuIfQP77rrLil/xcYIMAKMgF0QGDp0KE2dOtUyGpcI14KTA4ZCNmwpCKAyGpwsTiBn4c47HGRVq1YlOKKg65orV65wm7DM/kxcdZ4qldmnvKzqE7E4iMmJtSHwHxc3RK5hINsI5MYnbu5ly5aNdRf5/IwAI8AI6IYApIIefPBBunLlitSnLl++vG5tG9HQpUuX6J577pGrdihpyjraKSir6mgo6IPCPmzuCKBM8Lp162yfoMXEVecrf9CgQTRjxgxXqIAiru+++66sRhVrU3JXiLdt3769rO0NQl2/fn1ZI5uNEWAEGAG7ITBhwgQZ91euXDmaMmWK6YenyPWyZcukNjhbEgKIa0X2PF5EPvzwQ4bFAwEoLYwaNUqWC+7bt69t8WHiqvPU7ty5UyYCwBRphZg/3oLMYFhmAVn1tCFDhlCjRo3M0EXuAyPACDACuiKAMtZYWTp58qR8QceLupkNcZyI50SVqDJlypi5q1Ht28KFC6XqDaqjIZmYzR0B5ZHOmzevLIhkV2PiasDMQqs1MTHRRVzNVFNZvckrrVlPjVlUzVJhBAZAw00yAowAIxATBJCcheTYIkWKSOUUVBkyq1WpUkXGKrJWqfsMzZo1iwYMGEAdOnSg1157zazTF7N+4WUHfANhMfPmzbNEMmIkYDFxjQS1IMc89dRThDcfZfiymaXkq2d8ly/NWQ4bMOCi4CYZAUYgpgjgXoclVGRfQxpLrYzFtFN+Tq6cHyhvWq9ePTN2MSZ9UonE0ClF2BubNwIohbt06VL5kqaS/OyGExNXA2YUmflYeodlypSJUJHKDAb5kI4dOwYskIBsTcTomrUkrRlw5D4wAoyANRFAue2XXnqJ8uTJI5dSPQvFmGVUNWvWpMuXL0s914ceesgs3Yp5Pz755BP64osvCPq8Tz75ZMz7Y8YOzJw5kwYOHEjQu0UxCzsaE1cDZhUZ+sgKxVIU4kYViTXgVGE16Y+4agsk4KZgFu9wWIPjnRkBRoARCAEBJMnu2rXL1JrV999/P507d47eeustatOmTQijcsYuKvn5vffeI4S1sXkjcOrUKSn9BluxYgXlyJHDdjAxcTVoSkFYjx07JrMgzeK9VAUSfIUHAAYzhTQYNC3cLCPACDgcgW+//VZmXCOOFC/qZrQHHniATp8+LTPoO3XqZMYuxqRPvXr1ogULFpj6pSMmwHicFKsKWF3oISpptWvb1gxd0rUPTFx1hTOlMWQ8olb2nDlzDDpD+M2i/rV26UBLYLG00KxZs/Ab5SMYAUaAEbAQAkh6AjHEyhgStooVK2a63is9cIR2vfLKK6brX6w61K1bN+lFHDNmjFzVZPONwA8//EBvvPGGVKSAMoXdjImrQTP6+++/yxKq0Jszi2mJK5NWs8wK94MRYASijQCSnr788kuZvGLGMtxQdjl06BC1bt1ayj+xJSEAIo+QNysUkojlnP3zzz/y5QzFN8z6cpYafJi4BkHv77//phMnTkj9P2xnzpyhixcvSrkJBM+rDb+j4on6PHv2rGwZYQJI0MqSJQtlzpxZbtrfM2bMKP+GOBQITUN/DRt+zpo1a2rm1uvY7t27u7TdFHFFbWNU02JjBBgBRsApCKjSoenTp5cZ2CjIYoTZ6flhBD7htomErG3btsmVzNtvvz3cwx21vyp1jGpa8FTbyRxPXEFEN2zYQCgcAA+p2kBW8b9YGghuvnz5XBsyYYsXLy7LspYqVSrsrqlycOpAJq1hQ8gHMAKMgE0QQPwochAgH/T0009HNConPT8iAkjngyBhhmc18jWgx8vmH4Hdu3fLFQXwiPj4eFslaTmOuGIJf6t4Y9u8aRPhZyRQBbLsebJTzvw5KWfenJQ9X3bKkScHZcqSiTJkzkAZM2Wk9JnSU4ZM4ufM4m/4WfxdbhkzUPqM6enyhct09crVpO3yNUq8nCh+Ftvlq5QoPq9dEX+7lEgXzlygMyfO0JnjYjt2hk4ePhmwX7gY7777brmhjCE+c+bMGfAYhC2o8cbFxUnZKzZGgBFgBJyIgJLGKlCgAC1atCgkaaxwnx+URyCbX2x5xZZPbPg9i9gyE6XJJAogZLpBN8SWJnMa+Sn/jp8zir9lTEN0QVRgvCJ+viJ+vix+vpz0Mz7pitj3yk1049J/RPCxnBDbcbHPMbHPYbF/AIvk+WGGawTOFujwYr4KFixohi6Zug9QpQBWKO+OFVe7mO2J66pVqwgbbjhbtmzxmrebM9xMJcqVoJJ3l6S8hfISiGp2QU5z5M1OufLniuk8Xzx3kc6dOEdnsZ08Q2ePn6X9f+6nXRt30bmT57z6VrhwYUKBAWTLQrQ6e/bsbvuo4gOs1RrTaeWTMwKMgEkQgEbqgQMHaOTIkVSnTh2vXgV7flAGcUg5sd0tSGQhQUTzCCIqNhDVNPkDk0fDIcAjAmRWbDdOij4JUkt/im2j2Hz4RYI9PwzvbwgnUPO1ePFiyp8fbwRsgRA4cuSITLqGNCfK5WIF1w5mO+KKoOQ1a9bIuCUITEMLT2t5CuWh2yvcTreXv51KlrudipQuTGnTpbXcXJ46eop2b9pNezbvkduBPw/Qv//86xpH2rRpqVq1atSwYUOp6QYSC+KKN21kHJpFostywHOHGQFGwDYITJk6lT4cOpRq1KhB48aNI+3z44clP9D5c+fdxnqjkPBmVhCEtHwyYS0tCGq6GBPUSGbjqCCzmwSZRYFHbCC0/6Q05Ov5Eclp9D6mSZMmhPjkZcuWyTwQtuAIoEQupC7tFBpoG+KKZZ/ly5dTQkKClDlRli1XNqrZrCaVrliaSpYvSdlzu3shg0+7Nfa4lniN9m3bT3sFiV23dB3t3bLXreMIJ4Do9v/+9z+qXLmyNQbFvWQEGAFGwEAEkFALT+u1a9fkS/7Pq36mSxcuuc54I5cgqs0EMa0o/iTIaprcFiSpIeB3I1GQ2G3JJHap+PRYnASxh0QXNs+VvBCa120XJRNmV2F93YDSNAQ9YIQJ/vvvvwQN46JFixpxmqi2aWniikSqSZMmycnADUhZluxZqGbTmlS5XhWKq3JHVAE1y8lOHjlJaxPW0ur41XRw+0FZ5hWGEocoBdeuXTtCWUE2RoARYASciIB6fiDWH9qu6v54I7sgq03TUJp6gqRWcSIywht7RGCQcBP9F/8vpdnuTtZj+fxQhRlWrlxpmBKEHWd8xIgRNHHiRLnqik941K1sliSu0Lf7/PPPad78efTP9aT1jSw5slCV+lWo2oPVBFmNo5vSingjNonAicMn6Lf432j1otV0aNchFypI6ILSQO3atUNKTGA4GQFGgBGwOgK+nh+Eqpj1xQbZbUFW06S1p2c1ork7LIhsvHB8LBJH70ppIRbPj1q1akltUsQeQ2KSLTQEINsJRQZc+3aQx7IUcQXoWOpGyTdld1S9gxq2a0gV62Athy0YAkf2HqXlM5fRirkr6PrVJC8DpLW6du3qMzkhWHv8f0aAEWAErICAr+fHjarCs9hOeFfrMFENZQ5v7BUEdqbYc67YriYdEc3nB0IWoJWOPBZooLOFjgBCBdu0aSNXF1DqGEncVjVLENdTp07R2LFjae7cuTJ4HslU1RtXp0ZPNfo/e1cC30TVfS/KviqLgIAFRSiL7CCLdWFf3RcERETAqlXQKmpBsKhVPv8V9KtaRaGiIAJ+gshShIKi7KhglSIgsgnIJiC70v87L3npJJkkk2SSTNL7fr9IbWbevDlvOnPmvnPPpVr12cstkIvv5PGTtHzWcvpq+lcOhwLoYGGZ0bw5vwQEginvwwgwAtZDwPX5QUXFGHuIz30islqfCWtAMyZy1vJnCQeF6YL4H7JhGI7nB8gWiBeqZxUtionk5g8CKP/6yiuvUMWKFWURB/wbjc0v4rpv3z5p1r9+/XqZ2YcP3mLRYKUBmyV8cHG1aNGC4I8XbIN+9dVXX5VvWVj+b9+nPd32yG0Rt6oK9ryssv/5c+dp+ezlNG/SPDpxxJbUdsstt9DIkSNNr9xllXPmcTACjEDhQED7/CDI+voIsvqIIFrspGTOBXBOENjZgsBOEgT2iI3AmvX8iATfMAcUa/eCKlpIbgNPg+QSeS/R1gwRV7zdwCoE4Xl/2rXXXksPPfRQQFnsKJk6duxYWr58uTxkqy6t6I7H7qBqcdX8GQJvaxABuBIsnbGU5mbOlcUS8AKSlpZGzZo1M9gDb8YIMAKMgDUQcH1+UBcxrscEaY2Lvoe0NRD1MYozgsDOEDKCTLGdKIwQzPMjEnwjKjA2aZAoWY+KWihH3KNHD3r55ZejLlnLK3HFGw/IoyKsJcqXoBptalDN1jWp0tWVqNzl5ah8dVuN5+P7jtOJP07Q4a2Hac+6PbR37V46e9wmggGBTU1NNRyBRSR38ODBElhUpRo4eiC1793epGnjbrwhcGD3Afrv8P/S3u17pWkxLmp453FjBBgBRiAaENA+P6iUIKujRaJub5urCrfQIpC/W+A8XBxjO/n9/IgU3wgtItbsfdeuXfTAAw/QoUOH6Prrr6fXX3+dihUrZs3B6ozKI3FFLeDx48fLDL7yNcpTg1sbUJN7m1CJcigV4rudPXGWNn2yiTbP2UzH94o+ypenZ555RlZx8Nby8vJklBZvzNWvrE6PT3w8zFHWHfRp03G0yGmQ3en5jffQlb5P2/8tDq+kiR13082h6t//ERGir9Nfm05fz/5a7o2lBWQicmMEGAFGwMoIaJ8f8oY9kaOs4Z4v6Qn7mjjqbNuRjTw/IsU3wo2NlY6HqlqDBg2SZeBbt25Nb731FpUoYYzfRfo8dIkrLqJRo0bJsTW4rQFdl3ydYcLqekIgsEtfWEq/LbEZ4iOC54m8rlu3jh599FE6e/Ys1WtZj55860kZcQ1vsxFXmjaF7mlsO/KO9Ado3I6hNDGjPZlevsCCxFXhvWTGEpr2yjT5v3fffbfjmgjvfPDRGAFGgBHwjYD2+UGosfKWIK2lWBrgG7nQbCGlA6/Y+vb2/IgU3wjNWUdXryCtiLxilQJJ2SCv0WAz5kZctRdR55c7U3zveFNmIu/LPFoyaolH8rpt2zbq27evzBhscl0TSpqQRMWKRyJ07U5cKZTkMpR9mzBz3837jiaPmUwXLlyghx9+mBITE03olbtgBBgBRsA8BLTPD7pO9DtBkNbiTFrNQziwnvLnCfI6Rux7gXSfH5HiG4GdTWzuBbkAyCvkA0iy/48ogdyoUSNLn6wTcYUoGtpSNDNJq0JAS14nT57sSNo6c+aMFAvDoQBlWZ/94FkqWixSVhdGiOsxWpk0giatsJ/ZwDE0JbmOY6KPzZ9II1I22v/fVWbgLEXonjaU9qVAKtCd9qPPOs59yWiv+MvX9h/uKwqWWdPHT5eHnTp1qqy+wY0RYAQYASsgoH1+oCwrfSBIazEmrVaYG4wBllk03jYa7fMjUnzDKrhYaRxHjx6loUOHyrLwyG25//775YuGVaUDTsQVA1+7dq2UB3R6oVNIcIVsYPPnm2XC1nvvvSePocqRXXrZpZQ6K5XKXVIuJMc21qkrcbWTVAeh1P//tT0EWe0lhAQigvrp6kZ0D34m121tfe9Ls2+rvl9hJ7e5n9ID/Umjp8X2c6lWzghqX8nY6EO11dtPv03rFq+jWrVqyRK77KEXKqS5X0aAEfAHAfX8uOgyUaJ01gUqcgmTVn/wC8e2+U8L8rqYnJ4fkeIb4TjfaDwGLEffeOMN+uSTT+Tw4+LiZPQ1Pt6cVXczMXEQVxWyh3PAwAUDA9a0+hocNK9Te06VjgMAqXHjxtS9e3cpERj+5nBqdkOk7Zfck7O6a/Su5EYuBT1FhDXvFt2oqNN32DezlrNW1kkq4EKa9bb3BXCIvj/992l6ptczdOKvEzR69GhZPo4bI8AIMAKRRADLnOr5QW+KSOsNTFojOR+ejp3/dz5d1Eu8WPx1QT4/SpUqJXMmws03brzxRivCY6kx5ebm0nPPPSelAxdffDENGTKEhg0bZqlglYO43nPPPYSMzFBIBFxnRUkGGjRoQA0bNpQVHOo2q0ujPrQlhEW2OZNH27J/9YIoqCSuzp4DcrwJ9uQtSUQnkRIKaL8jPYLronEtILoVpRxhd2JBklhkcSFZJjYrNYsuueQSys7O5pJ7kZ4QPj4jUMgRGDdunHx+kIh3FPmQSauVL4f8/4moayrJ50fVqlVpy5YtYecbqBzFzTcC586dk9VKs7Ky6N9//6Vq1arJHKTbb7+dKlQILkX9119/pXr16vkehJctJHGFfxreWvH2M3TF0KA6NLrzpIRJMuoKPQUSf8bNHGeR8q0+pAI6EdeCc7ZJAxyyAfGFJKIL28goqySu9p8dU+/anyKyObXoC4vZZIlLhcbcOYb2bNsjixP06tXL6HTzdowAI8AImIoAtK3t27eXD1aayeVbTQU3BJ3h+UF3Cs3rVlFlS1RrigTfWLRokWE/+RBAEHVdIu9oypQpNHfuXLkqDs0rXKH69etHdevWDeh8oJ/FSkmnTp3o3nvvDWg+JHFV9WuNaltPzZ9Hk1N2Og06Lm0w9elV2vCJLEheIC2ycDHXb1Wfnpv8nOF9Q7uhp+SstdRGak1ddao2crooboSwz3IlrnbZgUs0trpDeqBkCc4JXEjImrtDZBn0eMCmm7VQWzZ7GU19cap80YHPLzdGoACBwzQ/qSPlJW6kZGEll5velPpP9YRPAr2ck0G9PWi35b40jTaiI24xiQCSc1DF54Ybbgjo/FBVcfjw4ZTfUixDTxZFBsLWRInTJHEwlZyrjptThIqEKRchf74ggQvFgTPEMcN23sEf6MJsoUF+0TbiSPANb3acwZ9d7PZw+PBh+uijj+jTTz+lU6dOyRNF1BTk86abbqL69esbPnl4xiKiqxrynVAmGIUQypUzlt8kiWtycjItWbLEcNheEteF9WhwRn2SVPXwFprX8SuqOC2JOhh8zii5AIjrnY/fSb2HeC9MYBiVoDfUIa6iT5ndP1URTGcdbFNHshWw0EoFutPQtH00SRtldZIaNKWh09rQ2v4uBQjkNvtoqAWSslzhPLj3II3sOVJqlFauXCkj5twYgQIECshrt+ymlN3NRmKdG7YZS/mpNuJ6eH4SdUxxZQEeMB3IZDZWrjYk58I3Eq1Lly7yAejPw+vFF1+k2bOFyz1KuQ4JJ32zE9ce4ri9bMeVRDJF/LAxPEQyWokr7RXOWD0EeRURV6OyRDP5RufOnSk9PT1W/oTCfh4nTpyQ5HXatGl05MgRx/FR4hfY4m+4SZMmXkvIKlcicD9cB6qBU6AELaKwvqQEkriq7L7bJt9GNVrW8AmG24Uk9jiQnkFr441HXfdu2EufD/5cRlyT30mmazpc4/O4hWUDrbzAWvFW2wwktk2ks6fP0hdffCEzD7kxAnoIIGrqIK6Hv6SksUUoNaMXVSJ34jpWiN8yegkWm5tOTbO7aSKtztsy0rGDgJa4as/KKIkdMGAA/fTTT0RvCwLZIbLEVVBXyocVVxijrtF6JVxoLojrhSIUCb7Rpk0bmjRpUrRCZ6lxw4Hq22+/pW+++YZ27NjhGBs0sC1btpTcAL6w+MCNCOQWTWunKeUj9qYlsZUqVZIJYZAj6kVhJXFFLXpUThi4aCCVr17eJzhmENfj+47T1O5TJXF9eU4a1bjycp/HLRwb6Ed8rXTuY+8eS7u27GJPVytNigXGMj+pKSFwmpCWI0mojbiKymv9+5NWMZCQNo16LMx0iriOFcueK1b4jrqqvi1wujyEIBFQxFVFXlwjMOjeG4lVzy36XBDXK61FXN0joi5R2lzx/5mIAoiPsECUbaA4j2T7efj43rl/e9/oC32qPyMnEm0n1mrOUBARx40A0b7QUhDXf4pEhG+ARM2fPz/IK5d3d0UA/BH3b3xAZj01kNmdO51lpmpbTyQWc/bYY49JeaKD5IK4KgactBHCHd/NjbjmfkcZ/Y9Ql5w+VN8PjU9G0wx5sHfXvEvFSxb3feAY38ImRxBvJFrpgQXP+b9P/Je+z/negiPjIUUKgY0b7T4aImKatHOQhrjapQImR1yVB3SkzpePGzwCKNGKqI22eXp4YZvKlSvLCIxK6GjVqpVMGKE1gvCVjCxxzU8XkSMEneyaU0PEFcRRkdXDglh2FP8/TUgNIK0BcfXyvS5xBWG1E1Hn8diJraiRo4ix/B5vkxEgrvlNbRj7wLEAACAASURBVFG2SPGN4K9c7sEXAtqXUWyLaKrei6lrP97+/kuWLClfZJ955hmSEdeAiKs2OSuhS4He1dcZab5XxDVzdSaVKFXCjz1500gi8NZTb9H6r9ZHcgh8bIsh4JG4iiczquU4tYSB4rc7qL5G4+okFXDL6HJP5OLqbRa7AEIwHPUQ0y4hqsNgyRfX3NmzZ4lWiwdjqQgQV+0CQVqB3hVjNExcNZpYSSaF17vUzSri6uF7XeKq0dySIsLYX/uzAlD9LlqIq4l8IwSXKndpEAG91RVvKy7aewD+5pHEhWROE6QCB+i7prPoiJ+uAlqpwGsLX6MqNaoYPHXeLNIIvHz/y7Ttx2308ccf0zXXsDY50vNhqeN7irg6DdKLxtXgyXDE1SBQFt5ML+IqSZ89acM1QlO8eHFq166d9JKEkTxseXbv3k1FFgii5zs1w0QkdJb9XZbdDRFXLOtrXAHciKuX7/WlAvZoLc5US1aV7MDJgSBymtwLrYRU4HygUoHg+AZ0lgsWLDDxWuCu/EFAlfn1FllVf/dI1oI8AH/r0Mxqta7mJGcFIBXQJmeN+mg0Xd00ME8wf0Djbc1B4OmeT9OhvYdo4cKFdPnlrE02B9UY6cVOXBPzsigba5HCVQDuAgVBVERPU6nI2AJXAU9aWGdEvNtnxQh6heo0tMlZnshq+fLlpeUOHl6uVY8GDRpEP/zwg1zyLtI0AhFXratAIFKBcBJXEGut40EEI64XWgviei6I5Kwg+AYnZ0X2FqOIq3YUWhKLv3e4E+j9vWv3MccOS/QIV4FZdBclJVc1hIzWDqv/c/2py71dDO1nZCO3aldGdorQNt7KxcohBVz21b0YghmnePzIcXqi0xNUtmxZQ8k0ZhyT+4geBJS1lTYBq3aWchdQkVYtcXWOvjo5EcjTttlrLexhS/jiFjsIeHIVQFRMkVVEWjy1V155hWQlpGcFcb03ssTV4SrgqlFVS/Fq6V9JCnSioCGLuCrHA4ecQeNDG2apQP4RcewbbTZIAdthBcE32A4rsvcPENfEROFKBImPvcGFwNPLqafROhUguLLzldQzvafPM9NzFVBermRQMuBUgKClKEAwxaQCBAETPZ+nHZINQkdcMVw4FMylWib6webMyqGPXvpILtPBzJkbI6BFwEE8qxfYX+1zKkTgGnHNpfSm2SIwm0zK7tVRfGDQ75TUcRTVmabnBcu4RzsCWuKqyGqfPn18ejiq80YGc1JSEuW3EAUIpoTTT9rdxxVjcni5ahOklLxbJGHJ5C0VpQ0rccX7nz35S4GXI35AMli4iessoeN9yTaISPANLkAQ+bsG7FfhQgCyihdT15UUIyN0KvlavHxxGrZimJH9gt5GlXwtVqyYLNn3wowXTCj5aj5RC/pEfXQQWuJqLzmbdwtNSRYppUE2lOZFyde92/fSq6++Ks2CuTECeghoI6f6UVSbVKDdalF8IC/RqUKWc8WtgTRNQ2oZ7dhB4Msvv5T16v0hq9qzR8nXhIQEOvePqMIjAq9F6ocz6hrl86CXsBXiU8q/YC/5us0WcY0E3+CSryGeZAPd79u3L6Ayr9quJXHFL+655x7Ky8szHL43MD6PmyiZQHx8vKyyMHPmTGrRsQU9NkGUQAmmIdqa3cYLSbMtn+9OHCM87MbRIkIlrDa0ruk4YUMyRZRstR9cVq4i8d09dCWpfSZSrcwRNElmkoqKV44opq/vsb3nSluKuE6Mn0sjUuyWQqpELHZ1iyDbjmcbh2gDx3gnpbKSl0tlrgAxXr1wNb377LvSlgb6ViRLcGME3BCA9VVWbcpwLZmF34sIqu3SBSEdRDtlBS1IBzpKD1j5jVOEFRFZjQ8sV87iC06DACJoeH4gelhkAhNX/YvDHiF2JGe522OF46K6sFBoW58tIp8fMJjHS4tRuUAw49PyDVR94hb9CDiIK95+R40aRSXKl6CBCwZSiXKhsac6e+IsTe05lc4eP0tvvPGGJK7IHIPm4flpY+jKxoFHBuGDOjd+Io3o5anelCJ9qnRrAan0RVwnrSggq9JvdcdQmpjRnirYia3n71Eu9lPKTwYJthNRTTlXmx53o8a71T7GOnZC6kRcXb6zH3ttD2/nbE4UGtHWlFtS6MCuAzR69Gi66667ov/q5zNgBBiBqEYAZSfV84OEqX6RxkxedSfUVSqgLXYQhisA0daLbrmILuy6IJ8fyBiPBN8IZFk6DPDwIfxEwEFcsZ8q/drg9gbUaWwnP7sytvnSF5bS5s83kza7b+LEiTRlyhSqVqcajZ0+lkqWLmmsM5etQCjXdtNETt160UtY0qlUpRNxdSKHTlFMnT69RjmdiaRueVft8bXE1WlctpPzKTWQ5HYKUeoIah9EbsvsN2fT/A/mU7Vq1WTlkaJFiwY0R7wTI8AIMAJmIqCeH4SYx3RBXkszeTUTXzP6uvCmiLZ+UMTp+REpvmHG+XAfkUXAibhqrQpCEcJXIXuc8qxZsxwi/NOnT4uqkP1p+/bt1OS6JjT8v8Ppoov8F9sbJa67E7Xk1hhxddpHh7h6/h4RV1tFLG3rbpcm6BJPbf9uxHWR+xWjlRbokvXgiOuaRWso85lMuvjii+ULBpu/R/aPlo/OCDACBQhonx90nfj9fwV5vYjJq1WukfxFQo34DLk9PyLFN6yCC48jcASciCu6UZIB/GwmedWSVr3MPmSZ9e3bl44fP06tu7amh155iC4uerFfZ2ZUKhBW4uoWJXUmyrrE1ZWsZtayyRJ0Iq6+AQpOKvD9su/pnafeoX/++Yeef/55uvPOO30fkrdgBBgBRiCMCGifH9RVHPgVQV6LMnkN4xToHip/mSCtT4mv/hE5IzrPj0jxjUjjwscPDgE34upKXq/qchV1FIkTgWpeoWnNSc2h7V9tlyP1ZkexefNmKVc4ceKEjLw+mv4oFS/pRwKQ4eQsbcRVXzc6aYXSwarkK80+/kRcXcim0rQ6RVyFxlX9v0rk2pdm1606aVxtpNfxncAT/S2KG1GQWOZ6PQSRnLVi7grKeiGLoG997LHHaMiQIcFdbbw3I8AIMAIhQkD7/JCR13RBXksyeQ0R3D67zZ8rSOsLYrML5PX5oSWv4eQbPk+AN7AsArrEVZHX8ePHywgoEraaD2xO1/S9xjCBBWH9acZP9MPUH2QiFioivPjiiz49u7Zu3UrDhg0jiO5rXl1TktdqcdUMAghit5ZaSzcAvaZDQrGZJHeTyJbTL5Kw0qrTpBRXV4EAiavoUSsVaJo2lKqnTHK4GKiI6xgaVyAn0DoFuLkKeHYo0D1jJH/5aYd1/ux5mv7adFo+a7nscqyocoQyi9wYAUaAEbAyAtrnB11tJ69xTF7DOWf5ZwVhfU0ccZbtqEaeHyCvkeAb4cSFj2UeAh6JKw4Bv60xY8bQ2rVr5RHhu1br2lp0eavLqUq9KlS2elkqX728/O74vuP0976/6eCvB+mP9X/Q7jW76dxx4a8nGhKxxo0bZ9i7a9euXfTAAw/QoUOHZMS13zP96IbbbzB01r6TlQx1EyMb+S8T2L11D70z8m3a99s+iQEbNsfIpcCnwQgUEgS0zw9Cnq/QVxa5nclrOKY/f6sgrSPFkX6zHc2f50ek+EY4cOFjmIuAV+KqDgURdWZmpoPAGh0CCCvKe3kr2+epr6NHj0rS/M0338hN6jarS/2fHUC1G8T5PLyMcJIPf1OfvUT7Bv6VfP372N/0xbtf0LKZy+if8//Il4y0tDRq0aJFtAPB42cEGIFChoDr84OaCQBQGrYBE9hQXAr5xwRhfVf0LCx16TwF9fyIBN8IBSbcZ+gQMERc1eHxRoSLav369bJkl/rge5TsU59WrVpJsgryE2xbsGABoSY1JAto1/a4lu4afhdVqh6Et1Owg4qh/SELWDJ9Cc37YB6dPnFantndd99NTz75pPTa48YIMAKMQLQi4Pr8kCVXhwvyGvyjKVohMXXckAUUmX4R5X8ghKwnbF2b9fyIBN8wFRzuLGQI+EVcQzYKHx0fO3ZMRnxRIQXZ7XAbAIHtOagn1ahbI1LDiurjnjx+kpZ9uoy++uQrOn7Y9lLQrFkzGjlyJDVq1Ciqz40HzwgwAoyAQsD1+UGwoEa16kGCxNblCGxAV4p4ZOR/KipwfSKI62Ebhvz8CAhJ3ikABKKCuKrzgnZpwoQJlJOT4zjVRm0bUZf+Xajp9U0DOP3Ct8verXspZ1YOrZizghBtRYuLi6MnnniCbrrppsIHCJ8xI8AIFAoE9J4f1FacuijvXeR6JrCGLoKtgrDOErKAOWLrs7Y9+PlhCDneyEQEooq4qvPetm0bffDBB5SdnU3//vuv/HXp8qWpVedW8tPw2oZ+e8CaiKnlutq7bS+tX7Ke1i1eR3u27ZHjK1KkCMXHx9OgQYOoa9eu0hyaGyPACDACsY6A3vMjv7yIHHYW5LWzuDdeK/7lwoCOyyB/myCqSwRhXSww2l5A8Pn5Eet/KdY9v6gkrgrOP/74g7KysmTRhJMnTzpQLlmmJLXs1JLadGsj/WALY9u9ZTetX7qeNizZQHu37y24CeXnU9WqVaXLQ7t27QojNHzOjAAjwAiQen7MmzdPPj/wMi9bGfFBxfNugsReVzgjsflbBFldKjAQhJVsFuyO1qFDB7rvvvv4+cF/QxFDIKqJq0INutc1a9bQkiVLaOnSpQRNk2olSpWgq5tfTfGt4ql+i/oU1yiOihUvFjHAQ3Xgfb/vp60//EpbNmyhvHV5dGT/EcehEE1t3bq1jKy2bduWevbsKX1Z4a/HjRFgBBiBwozA3LlzpYPN1VdfTX/++afT8yO/lIgyNhfktZUgsS3Ev5D/+1ETJ2pw/V1EVH8QZHWDGPE68dlfMHLt86Nz585UoUKFqDktHmhsIhATxFU7NSCx8J0FicVHS2KxHUjrVU2vovot61O9FvWobtO6/lXnssB1kC+ipnuEVvXXH7ZIovrr+l/p2OECso4h4mYDOzKQ1U6dOjndbJo2temBH3roIXrkkUcscEY8BEaAEWAEIoMASo2j6ta7775LcMTx9vzILy6IbFNBYFuKscIpUNxKo646l+Cn0m/1BzF+QVTz1xckWKkZ8Pb8iMws8VEZgQIEYo64uk7uypUrZRQWeliUktVrVza5UkZkq9SoQhUqVxCfS+iSKhWoYtWKEb1W4K167OAx+gufQ0fp6IGj9NtPv9GW77fQqeOndMeG5f8uXbqQtzdjWJWB4KP5YxAdUTD44IwAI8AImIzApk2b5LI3Eoy++OILt96NPD8IajREZGtcRFRZZNqLD1UR/181wjIDxDIO2j75h8SYDoiffxKf78XHZiTj1ow8P0yeAu6OEfAbgZgnrlpEfvvtN8rLy6MtW7bQzz//TLm5uXT6tM271FOrUKkCXVrtUrq0yqVU4bIKdIkgtaXKlKISpUtQyVIlqXip4lSqbGkZtS2Bj/i9/JQsIX936sQpOnv6rO1z6pz898yp0+Jn8e/pM3Tu9Dk6c/IMnTh6go78eYSO/nmU/vrzLzq095DPyYQ4Hp+GDRtS/fr15c8lS6JUjPfWr18/ef6qzZo1i+rVq+drN/6eEWAEGIGYQmDUqFEyR+K5554jRF69tUCeH/mVRDSzmiCwgsjSZeJTWXygoS0tiG0p4X9a6gIVKSu+F7ft/JKCXOL3pYvIn2UkV8Ra8k+Ln0+Ln0WsQv58Snx/SmwrHl1FTos+TgoP1aOizz/F93+K34t/i+z1TZoDfX7E1AXAJxOVCBQq4qo3Q7BIAZlFpumePXukYB//HjyIV9XItzJlylDNmjVlcYfLL7+c6tSpIwkq9FglSpQIaIBDhw6Vy2GQHCAhoXz58tKlgclrQHDyTowAIxCFCKC6FlamihUrJlflcK/1txXG54e/GPH2jIDZCBR64uoN0J07dxKqd+CDKmEgtYcOHaKzZ8/S+fPnnT7nzp2Ty+/4/ZkzZ+Q2IIX4lC4tIrLFi8sbJP4tWrSo/Fn7geAdxBQEFRXH1CcUQvj09HSaOnWqPHUteUWVmXLlypl9jXF/jAAjwAhYDoFJkyZRRkYG3XPPPZSSkmL6+AJ9fuBZgueHalZ7fpgOFHfICPiJABNXPwEzsvl7771HK1asoMsuu4xAEq3WML633nrLMSxFXhs0aEC4mTN5tdqM8XgYAUbATATg/43EVQQi4CpQu3ZtM7sPqq/U1FT67LPPZNADybNIouXGCDACBQgwcQ3B1YA3eEgPEIFdtGiRjJ5aqSniqggr/kXDjRLkdcaMGVYaLo+FEWAEGAFTEfjqq6/oqaeekjaB77//vql9B9sZ9Lbz58+X9+MhQ4bQY489FmyXvD8jEFMIMHE1eTohK+jevbtjCf7++++nJ5980uSjBNfdhg0baPDgwY4xojdFYvEze7wGhy/vzQgwAtZGAIRw3bp1ckUMOlcrteHDh9OyZcskcYXjAQg2N0aAEShAgImryVcDMlShl1JVWCpXriyF/1ZqesSVyauVZojHwggwAqFC4Pfff6dbbrmFcG+G17ejYlaoDuhnv5AGrFq1So7rzjvvpOeff1708JmfvVh782PHTlLZsr2F33hkLSetjRKPzhMCTFxNvjaSk5PlzVDbJk+eTPBOtUpzjQqrcWklA/gde7xaZcZ4HIwAI2AWAggsYCkeS/CIvFqtDRw4kH788UdJXFHl8JVXXok54rp//x/011/HhTvOw0xerXYBRsF4mLiaPEkJCQmyWhduOmr5/bbbbqMXXnjB5CMF152qnoVeXAkrfgd3AyQG9O7dO7gD8d6MACPACFgEAXix4n6MBFQUpQnEAivUp4Io66+//iqfITfccAO9+eabMUlcEUApVaoMk9dQX1Ax2D8TVxMnVS3BayOYahnq22+/tVS2viKuWm0rxt2xY0caMGCApSLEJk4Rd8UIMAKFGAEsw69evVrmHSD/wIoNwQL4w+LZgRK08NiONakAIq4grmhMXq14FVp7TExcTZwfCP0//PBDp2irIoZGKrOYOBSfXakiBNgQBQhgD/P3338TEgMefPBBn/vzBowAI8AIRBMCIKwgrlWrVpXVsuCpbcWG4AEK4Fx00UWyIuLMmTNjmrgyebXiVWjtMTFxNXF+oEdC1S2tTEDrkWolmykQVxRVgJ6qV69etHHjRnr00UfF228pWrx4sSSz3BgBRoARiBUE7rrrLrkEb3Xtfrt27ejkyZPyOQJ/WfjMxnLEVV1fHHmNlb+00J8HE1eTMHZNeHJdgsdhZs2aZZmyqriBu5Z4RaR1/fr1bMFi0jXB3TACjIA1EFi4cCE9++yzsmT2559/bjknAS1KTZo0kf8L4ooiNvCcLQzElSOv1vhbiYZRMHE1aZYQTU1LS9O9IVo5SUt7+nl5ebL8IUrRzps3z3KFE0yaKu6GEWAEChECKASD1bADBw7Q22+/TR06dLDs2aPUK4oiKOKKFTBIHAoLcWXyatlL01IDY+Jq0nTABgtvxlqZgOpaEVcsvy9YsMBSSVqup//0009LqUCfPn3opZdeMgkd7oYRYAQYgcgggKACLKVatGhBU6ZMicwgDB4VjjRwptF6y9qssf5nsIfo2EybnKU3YpYNRMc8RmqUTFxNQP7EiRN03XXXOVWf0nartZuyur4KkgdoXpGs9b///Y+uuuoqExDiLhgBRoARCD8CZ86coW7dugnP0L9kkhOSnazcEBXu0qWLJK5Y+Tp//jzZHGkWW3nYfo/NF3HlyKvfkBaqHZi4mjDdy5cvl9n4erpW1+6vvfZaeu+990w4aui6QHQCUQosqWFpjRsjwAgwAtGIwDvvvEOZmZkaI39rn8XOnTvlaheIKzxmkaSFFbCqVb+19sD9HJ0R4srk1U9QC9HmTFxNmGwUF0B00lvpQFivVK9eXVqxvP7665aWCxw9elRGKaC3gocgvAS5MQKMACMQTQjANQWlXS9cuCAlWtWqVbP88JFncPfdd8tnCUrSHjp0SLoK1K69wfJj92eARokrk1d/UC082zJxNWGukaEPUop/0bZv3y4tV+DDh5smrKairSFKgWhFXFwczZ4927Keh9GGK4+XEWAEQo8AVr/uu+8++umnn2QFQPi3RkODnhUWhSCutWrVot27d9Onn35K8fE/mz78w/OnU8eUbd77HTiYNibXJDq8iZLGEqVmNKFK9j1y08dRdrcxlNxY/CJ3MSXtbEcZvco594f9Os4hShtMPRZOppQVroerQ2OzGlIdH2fHmlfTpz+qO2TiGoLp++abb2Qd7JIlSxI0VralnqohOFLoujx37hyh9CCWrlBhBpVmuDECjAAjEA0IIAlr4sSJgvDF0/Tp0+niiy+OhmHTqlWrJMkGccXYEYH96KOPqEmTrf6NXxDJpv1X08BpdmLp194naH7SBEqhWylHQ1RBdMdSHwc59UZcC0hxW5q2sSs1JvS5iuIy8DORLeL6C80YtI+uNUBcMXwmr35NYkxvzMQ1BNO7bt06GjJkCFWoUIGQJfruu+9S27ZtQ3Ck0Ha5efNm6tu3rzyI7eZp8xfkxggwAoyAVRHAy/btt98uyd9nn30mV42ipS1btkzmS2DsyIdYs2aNXa610+ApKNLZlgaSsNFK9I+42ggn0cs5/ai3Cq06jmwnn6nVKFNEUd2Cp/btCsjyHkpv+gt1M4m4Mnk1eAkUgs2YuIZgkrE8NWDAAKpSpYos3We1cq/+nPKbb74pb5w1atSQOl5EkbkxAowAI2BFBOCG0q9fPxmpxCoRVouiqX08bRq99p//yMQs+Lki8dfmPbvfz9OwEdg8f4irp+V+D0f2HHG1k2cNs00IUiqgHQJHXv28FGJwcyauIZjUbdu20R133CHlAbA3gal/Sop4jY3CBvNuJAtAt4uSiaNHj47Cs+AhMwKMQGFAQLkIXHPNNXKVyFvCrBXxGD9+vJQ2QN/aqFEjWrRoEU2YMIE6djzq53DNJa4gqf2nEiWkPWFIKiAHa5crkNLJ+nkG3jdvJL6ON7VH7ix6EGDiGoK52rNnj/RCRbm+P//8U745v//++yE4Uni6BBEH+QaJRdIWamlzYwQYAUbASgggynrvvfdK/1OUdcUqUbS1Rx99VPq2tmnTRib8wlHg1VdfpR49Tvl5Kt6Iq3tE1GvnCXatqz0im0rzvCd12YlqrpAdZKZUph5ptt5TUlABzL1pybDxk2Tiahyr2NuSiWsI5vTw4cPiDbmjw86kYsWKBO1SNLdJkyZRRkYGVapUSZaDxVIWN0aAEWAErIAAXqqha4W+ddSoUXKVKBob7Lt+//13ScAhe0DRhBdffJFuvvm8n6djbsRVHtxFSgA9bFZcPw+uAnto/vwjlJeyX2hcG1J2OlEy3AlEc03y8vPE7JszcQ0Mt9jYi4lrCOYRNxyUFyxatKh8a4alCepNo+50tDbYyyBRC1ENRJPT0uyv0dF6QjxuRoARiBkE4CAAJ4FoKOvqDXSsZp06dUqWqP3555/p448/pueff144vBTxc65CTFylzdV+SpSJV3qkdjGtatuQtnRUyVlI1JpMQm3g0up6SATzdbpMXH0hFMvfM3EN0ex2795d2H3so/bt29PKlStltSxkiUZzQzQD2l2UIUTS1g033BDNp8NjZwQYgRhAQCXDIjCApfVosx5UUwDCqtxnoG1FtBVk/NlnnxUR2OJ+zpQOcZVkM5e66zoGuJNPtwM6Iq7HBAn9hupr+3GJxubO30TVe1WkLIerQEFvHHH1cyp5czcEmLiG6KKAHRZsseCFCgP/aDLB9gYJIgCvvfaarPz1ySefyCQCbowAI8AIRAKBI0eOSFkA3FtsS+o3R2IYphwTq3KqUMIPP/xAb731lsyNePrpp4VLjdHVOn39qrSoqh4EcbWTXkog6p6qZ5WlB4HWDouJqykXCXciEWDiGqILAWVgkSAwdOhQgj4US0BIbIqFNnjwYNqwYYMkrSCvILHcGAFGgBEIJwIoSY0qU5Av9enTh1566aVwHt70Y6Wnp9PUqVOl/zeK2MAGCx7gI0aMoAceKG/68WwdOhNdTwULctOn0++DQFgNJHY5XAQUcW1HO1HQwJPxq0r+8usMWSrgF1wxtjET1xBNqEpmGjZsGH344YfSlgVv1NFmz6IHjzbK0bJlSymDgJ6XGyPACDAC4ULgiSeeoJycHGrevLmMTEb7Pah///6Um5tLjRs3pmnCzxWkFeT18ccfpwcfvCRcsEbJcZi4RslEhWSYTFxDAivRggULZOEBLF1hGQul/GbMmEENGjQI0RHD2y0ssnCjRUlbZMKOGzcuvAPgozECjEChRQAOJwgO1KxZU95Xo33VB8mvsE1EqW24I2DFDmT8v//9L8Eia9gwtzJWhXbubSfOxLUwXwBMXEM0+z/++KOs2oIsVyRo4UYbzRW09GCC3+Bjjz1GFy5csOuwBoQITe6WEWAEGAEbAtnZ2TRy5EhJVkFaQV6jvUHuAK9sEFjcUyExQ2IW3BKge33kkarRfoomj5+Jq8mARlV3TFxDNF0oPNClSxdZhADVUB544AGC0wB+jqUGjSsMsi+66CJC1RqVFRtL58jnwggwAtZAAEvpgwYNki/LKEUNmUAstKysLFkhC8QVVliwHITeFbpXkNikpOqxcJomngMTVxPBjLqumLiGcMqg/4QxNmQCCQkJ0scVonuQvFhqkAl89tlnVLp0aZmsVbt27Vg6PT4XRoARsAAC+/fvlw4Cx44do5dffpl69+5tgVGZMwSszmGVDsQVJBYrdSj9ikAHkmGHDzcYVXaxvIL1VMeUbW6D9JSEhQ2dCguYc3qOXpyTvOZRvmGHAteBMHE1eWqiqjsmriGcLlUFBd6CuNGuXbtWRglatWoVwqOGv2tEP5KSkui7776TBRc+/fRTmRnLjRFgBBgBMxA4efIk9evXT1aVwuoVMu1jpYGIX3/9YtjjNgAAIABJREFU9XTxxRfLalmQQlSrVk3KIBB9Bal98sk4P04X2fw2n9V2qzXVrew9uBJTI/8/lvpQRq/g3GMkic67njbaK2ihElfT/oe4AIEfM8ub2hBg4hrCK0HVnUZm6I4dO6T/6YABA6QeNNYakrTwYNm+fTs1adKEJk+eLGuGc2MEGAFGIBgEQFoTExNp06ZN1KFDB+lvGgvuLAoT2CYiGQvRVjgjfP/99/Ir+H/Dm9b2zLgyIAiNRFzDQlxBUjOrUU5GE3JKMwuYvHLENaALIkZ2YuIawolEWVREH0ePHi3fqLt27So1r1999VUIjxq5ruGegKU82GXhAfPGG28weY3cdPCRGYGoRwCk9cEHH6TNmzdTfHy8tBYsWbJk1J+X9gSwWrVihc3kFN7YX375pfwZ8ivIsODeMnLkVQGds96yvxGimhXXj5JlLVebdCCoiKudnCYMbEypyc7ENTd9HGVTW6Hn9TfyysQ1oAsiRnZi4hrCicRN9vXXX5fJBPAcxL+oiAIyi5twLDZElqHJAnlFiVvYuZQoUSIWT5XPiRFgBEKIwIkTJ2RiEkjrlVdeKbWfsSZBQhEFvOTjvA4dOiSTW+HfGihxnZ80Thr9J6Q9IZf2jUZc9XSw2qlV/fk/3aoIQUPKdi0TS5rKWiC32Q0LZAQ+D8TE1SdEMbwBE9cQTu6SJUsoOTlZRlohE1CapYcfflgufcVq27Vrl9RlgbwiyQBSCSSmcWMEGAFGwAgCIK3Qsm7dupWuvvpqmRsQa6QVOCC6OmrUKJm8i6jrHXfcQWPGjAmYuModBQlM2tnOQVwRPe2WrSpfYQNUv/KcGKUXkQ0q4mqfcFeNa3CRXCauRv6OYnUbJq4hnFlECvr27SuLDoC0gsjddNNNVK9ePZo1a1YIjxz5rkFe8eBBFAGaV1TXYvIa+XnhETACVkdAS1px70ShgWgvMOAJ8yFDhtC6deskYYU0ALIBRJnRApYK6BDX5Mb2hK1pjWlR/zlUZ5pYAaxejirJEq6rKC6jK9mVATquAmLfpCM0yFWf6veFhDFMJpo2hpKrb6KkjvspcWPBcf3rjomrf3jF1tZMXEM4nypbFJqsNWvWyCOpG9X//vc/uuqqwHRLIRyyqV3/8ccfdN999znIa2ZmJpUpU8bUY3BnjAAjEDsI4J4JTSsirXjhjeV7xoEDB+RqXFxcHDVr1ozgPgMXgZ49e5pPXEEUs4h67JhDC3vYZAS2Fk7iKg4n9a6r5ZG9WXL5vqKZuPrGKHa3YOIa4rmFzhMZ9/BvxVKXWhpCBv4zzzwT4qNHvnuQV2h7cZOO9ehJ5NHmETAC0YsASCvuFb/99luhkBipsrV4DuTk5MjIK3S8qqiCGRFXJD/1nyquiYRb7Rn9IKoTNOQ1eOKKY2TGa8mwl2tQ+szOIaSiMXGN3r/VSI+ciWuIZwBl/FDOTyVknT9/XlbUApldvnx5zGXI6sEJ0orIK/6NZb1aiC8l7p4RiFkEtKS1MCR1wvqqY8eO9Pfff8vnAKQC+/bto8WLF1PVqrbyrgERV5eI5qCd7j6u6Nu5EMAEmdDltTmIr+tW3vWyjq3VuFwItDaRzL+LmyOu/uEVW1szcQ3xfCI5C0laKN3XuXNnebQ333xTJhvAo+/mm28O8Qis0T1IKzSve/fulRnC8Hm99NJLrTE4HgUjwAhEDIGjR49KJxJEWguLjd63335L8Pm+7bbbpK93+/btpY4Xv1ctEOLqIKRUENn0OLEDBxvM4vemcfWuf3VEfL0cSzkf+OdcwMQ1Yn+wFjgwE9cQTwIy6mFv8sgjj9BDDz0kj6a0TdBwffTRRyEegXW6R6LWwIEDJXlFhS1gAxLLjRFgBAonArDPg8MKyrneeOON8gUfJvyx3kBWEV2dNm2ajLri2QDy+s477wRFXGMdt4LzY+JaeOba/UyZuIZ49tWb9XXXXScrvqg2fPhwuURUGJK0tBCDvCLyCtcBuAygFG6nTp1CPAvcPSPACFgNAdz/oO+EbAom+1idQtnTWG8IXCABq3bt2lIO8P7770u/a5BXBDhUCyTiGuvYMXEtPDPs7UyZuIb4Ovjrr7/ohhtucFsGWrlyJcHP9ZZbbpHVUQpTg54NDykkI6ANGzZM3rBjqYxjYZpPPldGwB8ELly4QEhMglwK0dWXXnqJevTo4U8XUb3tq6++Sp988gmlpqbSrbfeSo8//jh9/fXXEhP4uaqmSr4GUzkrqoHyOniOuMbu3Po+MyauvjEKegvoWHfu3Elz5gj/vDp1HP3hrRuC/EWLFjkE+UEfLEo6+Pfff2VJWFQXQwO5hxUM22VFyQTyMBmBABDAsvhTTz1Fq1atoooVK8pIY+PGykE0gA6jbBe8tGOFqWzZslIqULx4ccJqHLxrlfOMOiUk9KJs+IABA4QOliVVzlPNxDXKLn1Th8vE1VQ49Tt7/vnn6YsvvpCRhT59+jg2+vzzz+mFF15wqpYShuFY6hBfffUVpaSk0Llz56SfITReNWrUsNQYeTCMACMQPAKQB0HPCo07Sl5D416pUqXgO46iHvCyjsTUZ599lu69917as2cP9erVi2rWrEnz5893OhNEZRGdRRXCJ5+MM3aWsJsaS5SqKRaABKnsbsL0H+8HmuIETh3abaoobTD1WDhZx2WgLU3zUCzAWAUsZ9utAlcDY6flvhUT10CRi4X9mLiGYRZnzpwptZx33323LO+nGqKOILLQPC1YsKDQRV0VDtu2bZMZtkjQQGbtf/7zH5mowI0RYARiAwFo/RFpPX36tCRqWCYvVqxYbJycwbNAtBnRVmj7VbR14cKFksQCE0RXte3jjz+WpcKREzBiRC2DRyFZ+UpbotUbcVUZ/USKmLr7uhKh4tUv1M0s4qpDrg2fnGNDJq7+YxY7ezBxDcNc/vLLL/Ltun79+gQSq22qIAH8XhF5LKxNq3u96KKLCMlrMCPnxggwAtGLAPxKUf0KHyRePfnkk3LpuzA2lL1Ggu5zzz0nS4Gj4SUdzgJIUkNRGm1DMYIJEybISmKPP+7PKpSdfKZWo0y72b8e3gUFALTE1BdxtZVtRU0D781OhEFSsypSRnIFR2lZUkURXDswbM+FHZm4+pqBWP6eiWsYZveff/6htm3bEiKsq1evphIlSjiOihs7ErRQYSo7O7vQLZ1p4XfVvSJhA1IKlMzlxggwAtGFwJ9//imjiRs2bJArKRMnTqRWrVpF10mYNFpoWLt160alS5eWOQ3K8gsk/qeffqKpU6dS06ZNnY4GSQGkBUheffTRagGPxHPE1VZFS1t8IMFsqYArcU0k4SCx2qlqVmCyASauAV8QMbAjE9cwTSKWe77//nuZSet688aNDG/cqJ4yZsyYMI3Iuodx1b0iaatRI9youDECjEA0IDBv3jwaP368TDqCVzMy5guzdh33sBkzZshVNayuoWkDGnBYcfWvnTRpksQN7jOJiVUMTbsy/Nea+fvUuKqqVn5FPAuG41Xj6kJc8+ocEvrmyrSQ2lFGr3KiE4OVt9zOnomroQsiRjdi4hqmicWSD5Z+RowYITVL2oao65133knQesICBWVRC3tDIgdMulEuF9IBFC6AZZY2Wl3YMeLzZwSshgAkP6NHj5YZ8miQSOGeV5hXTX7//Xe5qlalShWnaOumTZtkKWy4KkAu4Nogr0CyalJSEg0dWtH4VNsTsFJpHnVM2eZ5PztRzRWa2MyUytTDLrFNSVmtuw/IcGLeBOrvWydg2x/9DzriJhVoDM1sOglLxJpiI++VtzwPnomr8Qsi9rZk4hqmOUXZV3iXoj41SKxry8nJoSeeeIKaNWvmsIgK09AsexhEJGCXhZv3+fPnpesAR18tO108sEKOwIoVKyRphXc1KuMh2ahFixaFHBWiIUOGSM9qJOj27t3bgcf06dNlVBrkHpIK16aqLsLn9cEHLzGOo4tzACKiWXH9PLgK7BFuBkcoL2W/SL5qSNkOQume5KU7ABWtpbr0ck4/6u1qEqGjcZXmBunT6fdBYvt9iylpp4q+Gj9F1rj6g1XsbcvENUxzqsq8wrtw2bJlukdVcgKQM3i8crMhgLKQiL5u3bpVRl8RpYALAUdf+QphBCKPAOQAsG1CoinaXXfdJV/SkT1f2BvIPCKm11xzDcElQNuQpAU3GRB8uAq4tjfffFNKy2yrdOWNQ6klrtLmaj8lKkcAN1K7mFa1bUhbOirXAE/JV+7EVDoS5F1POfHfCBeD66WNVl6i3XZLjVYS1/1UZ+pqkdClsdRy/P4Q1dcjvD7PliOuPiGK4Q2YuIZxchFtPXz4sLxZ6em9sDyOSirwNoRGrDAvr7lOC6KvyMpFeUQkcXH0NYwXLh+KEfCAACKJiBailPNll10mSVjr1q0ZL4EAKoThfo7iM3qlvRGcgKctPL5xP3NtSGabMmWKfGkfMMCPlwAHOT0mHAC+cSaGLsQ1d/4mqt6rImXp2F151K46PF+fkDpV7XY2jW0BQbXZbVUWHrDtaGfSKorL6Eq2chN2ghygrpYjroX7T4yJaxjnHzcg+PchOuGpxKF6y7Zlkj4axtFFx6GgeR05cqR8GHD0NTrmjEcZewjAj/X111932PvddtttkmBx5buCucZLNiqD6UkBoAW+/vrr3UqBa68U4AuplM0qq8CJxuvVJEllLpGoHNs9VWfpXndnfZ9WPeIqiemOWylHU+DAfTs9Ulpgs1VdklmySQuEVKBp/0P6MgOvJ8oR19i7qxg/IyauxrEKektYnqQLVbqt9vRI3f7Onj0rhfwHDx4klPyrW7du0MeNtQ6AEfRfwBNRDUSvoQ/u0qVLrJ0qnw8jYCkEkEiKFSPYNEH+VLlyZVkRsF27dpYaZ6QHgxfr22+/XdpfocgASrxq2/Lly6VXdYcOHeS9TK8pj1ebE0FRQ6dUYC3lbnXl1oEj2qmIK6KizvZYTvskOBNW9Z0/lbPy6kAyIJK2ZGKWavax1nH9vbdTZuJq6IKI0Y2YuIZxYn/44Qdpqq+nd9K7qV111VUyouFqkxLGIVv6UMjKhU4MZRPRkNiG/0c5SW6MACNgLgL4e3vxxRfp119/lR0j0QgyAXi0citAAOQeBQawOjRu3DgZiHBtiMQiIosSuLC70mtI5sL9f+zYsYIE5zPETggwcS3MFwQT1zDOPiKFKESACjIoROCNkD722GPSUsZWNeXxMI4yug4FTGElg4fAyZMn5eDxoEA0o7DVQY+umePRRgsC0GHCCQX+ymiw6wNhLazFBHzNG3Sp0KdC64v7kl6DFGzNmjWyktZ1112nuw3K4kIbi4h2nz7nfB22kH3PxLWQTbjT6TJxDfPs40188+bNupVStENBEhd0Y9BCIRsVUVpunhGABQ+W3OCDi+QtZDSD9MOBgJPc+MphBPxH4Pjx4wQT/E8++UTa0eFFEC/UeDGEvpybOwJKIgDHkzlz5siENdeGiCwCGGfOnJHBiQoVKuhCCWsxJOnaciJOMdxOCDBxLcwXBBPXMM++qqDytNC4DhBaV2/t22+/lQla0HDizZsJmO/JgjPD//3f/9HXX38tN65ataq0k0EyXJEiRXx3wFswAoUcATh44AUQL4J4cS5evLh8AcSLICdfeb44tBIBbwm4qvBAvXr1aNasWR47RFIWqiriftaly/FCflW6nj4T18J8QTBxDfPsq0IDCQkJspyfr6Z0Tl27dqXXXnvN1+b8vR0B6IlhzaP0eIhYIyGuSZMmjBEjwAh4QABJQ8hmR+QQrVu3bvTkk09StWrVGDMfCCjdKjBDYpWnppxjHnroIVkN0FMD7kuXLpWJcDfeeDhC+AdakjXUw2XiGmqErdw/E9cwzw40mcgkRTY8zKl9RTDOnTsna1v/9ttvTnWuwzzsqD0cTNFx4//zzz/lOcA7EYbghbluetROJg88ZAjgRQ96S/iyojVq1Ei+6CHhkZtvBL777jtJQlHWFb6scBPw1CABw/0clbOAs6em8hwQ+e7QYb/vQagtHNWsvOyiHAJct3XzVWXiahx43jJcCDBxDRfSmuPAugmRV0RQEUn11bZv3y7JK8guLKBQ25qbcQSgJVMJXKdOnZLJcZAOoBRjnTp1jHfEWzICMYbAqlWrZALR+vXr5ZlBk4nERlRyYmmNsclG8tqdd95JuLfAd9Ub2d+/X5RWFRHZSy+9lBDd9tbgOID5gc64TZvdxgaDrVyKDLjvKOyvko7QIHixarcFic0mGiirXOk0D3ZYxgdm5pYccTUTzWjri4lrBGYMgnsI7/FwwHK2kfaxyJx/TSw/IUECetdLLvGjdrWRAxSCbY4cOSIf0iCxqqGaGbR7/DJQCC4APkUHAnAIQPb7zz//LH+HUtT333+/tHFiLb3xCwUvxf369SMEF7CSM3ToUK87Ky/vu+++m0aNGuV1W7xYIwKelZVFzZv/ZnxQQRHXhjoeq/Mo33AxA+PDDG5LJq7B4RfdezNxjcD8qaopeEBgicmoT6vSPDVv3pwmT57Mmb0Bzh0ILJwaZsyY4bDQuvbaayWBxb/cGIFYRABuG9nZ2fLlDUQLrXr16vTAAw/I0qTIhOfmHwKqGiKqYEHj6qsB6++//96rDZbqA57fkHDgRbtx4y2+ui743k/i2rT/6oJ9IRXo9gsl7Wwny7kS+ZYKyGpabkUFjA83sC2ZuAaGW2zsxcQ1QvOo3qbfeecdat++vaFRQB+LqAjstLxV3zLUGW9Ef//9tySvILFHjx6ViCCJCwT2xhtv5KVSvkZiAgHo5LHKg5ddVayjdu3aUioDyYzRF+eYAMPEkwCe0M9feeWVUq8KCz5vzd+ABe7xubm5soJifLwtMm6o+alxdZBUKRWwRVydK3B5j7gycTU0K7yRiQgwcTURTH+6wo1u/PjxUruKkn5GG/xdsZyHZKPnn39eaqu4BYcAlvvguYgHEcpYoqFqGR7s0CDzgz04fHnvyCBw+vRp+kzIij4US80qORFV5XBdd+rUiVdsgpgWZPtjBax8+fKSWF5++eU+e0PSFu7ZnTt3lqW/fbW77rpLuqJAGnbVVT/62tzxve8SrN40rnapwOFNlJRVkTKSK9D8JJYKGAafNwwLAkxcwwKz+0H8Eem77r1161apq0IkBdY1eAhxCx4B+FfOnz+fPvjgA4cdEJJVUHMcH3jCcmMErI7Ajh07pD8ooqwoIoAGeREIq6cqTVY/JyuND4lssLJCsiwSp4xWEFNJubA4RLlcXw3yDcwlCG9cnC15zkgDcc2K60fJRnJ43ZKzWONqBGPeJrIIMHGNIP6ItqKedSCVsdQbP6KB7777ruGbZwRPN2oOjQfSkiVLJIHF/KChUhBszBDhhgcvnAm4MQJWQQCrBki4+uyzz6QuUjXIkCB9MUqurHI+Vh0HbKwQNEA0G4m1SLA10pQNIiqQeauWpe0LfUPagSIE1auvNHIYsY1vTapTRybYYUEqkBn/hF0Ta3CYQW9WeDWu+/btkzppvEDB0QIfJQGqWbOmtHrEB3/zLVq0kDr2WGtMXCM4oyCc8OjDg+Xxxx/3eyRYosLNE9oqZKuiEgs3cxHADeLzzz+XDw9EuNEQhYUX4x133MFRWHPh5t78RAARuZkzZxL8ilV0FSVE+/TpI1+y2O7NT0C9bA6ZFtwADh06JCsaDhs2zHDnqOSHezyIBNwcjDRICg4ePEjLli0Trg/LjOwirbCaZlajHFhdGdkj6Iirn0TZyJgMbVP4iOuGDRtkkGrNmjWGEFIbIeEYKwQtW7b0az8rb8zENYKzs2XLFnkjxFsSlqgDaSC+uJjhC4jsUzbWDwRF3/ucOCFu0GKOsAS7bds2xw5YelVRWNbC+saRtwgeAURXFy9eLKOrP/5YoH1s3bq1lLSA8KBMKzfzEEAiJ8reIuIKjMeOHetX5+PGjZPzBV0sEmyNNKzs4GUEzjNlywqDVQOtIKnKwMbeSLqQG3RMsd/nvPq3avSywR3Sz70LD3FFhBXXmyKsJUqXpxoN2lDN+NZU6YqrqVzFy6l8ZVtU9fihfXTiyB90eNdW2pO3jvZuXktnT9nkQiCwqampMRGBZeLq55+L2Zt36dJFJk4gOSjQ6Mgrr7wis+OhwYTVzRVXXGH2MLk/DQKoNY6EiYULFxJIBBoq5qgoLJfH5MvFbAQQ7QeBgRwAETiY3aPhhfWWW24hJPLgBZibNRGASwmcS+bOnUtwdDDS2rRpQ5AYwMu1ePF5RnYpRNsUDuKKlRQkceMFpnzlGtTg+lupSad7qURpWJX5bmdPnaBNSz+hzd/MEaR2r0wmfOaZZwxprH33HrktmLhGDnt5ZGSXYpl/xIgR0k8x0AYza1zkMBJHdnygJDjQ4xfG/U6ePCnJK6KwSgsLHFA5By8kN910E0fAC+OFYdI5Qw+pyCoq7Smyiu7btm0rpSoooMGRfpMAD1E3iIojyurvylrTpk3liDZu3Cj++1mIRhet3cY+ccXzXBWpaJBwG13XN9kwYXWdVRDYpZNfoN82LJFfGU0QtOrVwcQ1wjMD3crgwYOpSZMm9NFHHwU8GiQUJScny1Ky0Lghsejqq68OuD/e0T8EYOiOhC4YvCtzd/RQv359B4mtW7euf53y1oUOAZDVlStXysgq/pbxcqSafCES9mxdxUsRdNbcogMBeL0imIDABAIURhoi7JB+oCjE2rVrmbi6gRbbxFVLWjsPeZniO/h2oTByXeV99yUted9WsS2aySsTVyOzHcJtQDhRdQUaSjyoUNI10AY7JywDgECVK1dOWrU0aNAg0O54vwARgA5OkVitHjYuLk7qDxEla9SoERc4CBDfWNsNf7cgq7hmcA/AvcDyZDU3XZjVdxNm9VrPpcPC87MjLeyRIzLMPdzHdPY7PD9J2DdleLRvyk1vStndNhZ8jz76E03bmEy6jk/y+6leL5OENJcxHv6SkjqOohVe9ho4TTMGPy5C5Q5gK93a3NCe0NTCxQRLuytWYFQccXUGLnaJqwpm4XzNJK0KPy15xQtVNCZtMXE1dBsJ7UYvvPCCzFw3o6BAfn4+jRkzRnr/lS5dmt577z1ZDYpbZBBA1rcisfDfVQ0vKFjuxQeiefaIjcz8ROqoeKGBdnHVqlXyX60MwPqR1VxKF8zRmRomCIeTOqKYiithTKCXczKot+SxILZjRd179f829L0SV0kot1CdgVOFpMr7bDmIpS6pLtgXxxtLqc7kGscZW4RSM3rpZuP7IteeRvb7779LDTICCd9++63hyw16WOhiEVlH9D3UxNVnpSwUJBD5aKkatwLYYGV3G2N7ofBUZhb7dZxDlDaYeiycTClubwZtxQtIV/0XEK9oxS5xHTp0qIyyQx7QafALhq8ZfzaEbGDzis/lswccIdoaE1cLzNjy5ctp+PDhsvQrSsCa0VQWK5aaUKSAjcfNQDW4Pnbv3i1ttZARjoo42oZoLEgsEjKwRAi5B7fYQQAWSqtXr5YfkFX8v7ZZn6yq0RZEVRPzOhZEQu0EM1FEQSk9iX4f5ExOsTcip5nxiHSSjMy6kxj7MQZOc0RyC/bxYyXKQhFXRFknTJggySvuyUYbKvihal+tWrVk7oJfxNVOFhVHHDjNTi49HBwFCzrmXS9LvcomvV0PiReOfvYXDtuvXStyeSOusk/pSqCIKWyzVlFchpakCkeCpr9QNyaujplREgE4Bwx8bUHAmlZf1xk0r1Of7ikdByBlwUtSNDUmrhaYLWSOQi4AfRuWhcqUKWPKqEBYP/zwQ9nX008/TQMGDDClX+4keATwYAKJgcUJiMyRI0ecOm3cuLF8GwaRBakpWbJk8AflHsKGALSpiKQicoL5hXxE22Bbp15UMM9wB4iG5hqtzLWT1NpZ2uV8EZFN2kmDNNFL7NdxYQ/hL+oe0fQYzbQv39eRS/R6Ud4CxJyW/i0UcR00aJAsCAHyComQ0bZr1y7pxYvS03AwMU5cXeypJInNpe4uJNQxDk++r7rk1U4+U6tRpoiiepJVFBBlLTFl4mpk7lVRolBIBFyPryQDkBPClSiaGhNXi8wWiCUicbC26tmzp2mjgtn1xIkTZX8oIQg/OFSB4mYdBCDvcF06RmUe1eDJCW2cIjq40XDlLuvMH1444cm8efNm+vnnnyk3N1cm6EG/rtoll1wiX0Qwh4ioI5IWrQ1RUB8SUsep2ZbvEaXNEtG2ZKoOApuX6KSN1SeutsguorKBaksN4WtA2+raj9HxoJqRqqyFlxdIt4w23A/gGgEt/PTp0/0grq5H8FIgwE5OEwY2ptRk54IFMpoqoqVTp7pHXtURPEdcccwJThH1BJYK+Jx6+LV2795dRFnL09C3vKmtfXZleINJjybIqKutOlv0VNhi4mp4ikO7oZIL+FNZxeiIcFHCVgNJIHhwwoKrbNmyRnfn7cKMAOYJ5AfRWERl4RuL36mGSmlwK1AfuEegahpHZUM/Uf/++698yVAkFUQVsg+QV23DXKDkooqaY35i+YXRLYFKbyoQCc2Mp5zEPOrolfkKXezsHrTl17bUzSFHMBBxjcvymZTlOixHpNYlSusaWdbVxfq43NSKF/ydkcfgT8N1hdKyeGGF3MB4xNX1KJ6W49XvG1J202+ovlNEVrMPyG12Q5pGk+XLSkJaQWlXnxpXVU524OACGYI/IHjdNvY0roh6InBlVNt6amUSTZ6kT3DjhuZQn/a+5TULxMskLLKizWGAiatpf0jBdYSoG7w/UeIvmGIEnkbx008/0WOPPSZNsOHximpbnBAU3JyFa28UOQA5+uWXX+QHpBZJX9qIHsYCnWx8fLwksfiA2Po1xzoJKnKJV0XIvGoHB+pkeWuTcUA8soWezUMmuBi/Pvnxth++yxQPXRc9pY9EG6d5M7AtKqaBSOADwgppj7bBRxVWZ0iCRIQMEXH8fyz6q8rrwaM4Vf8vAhHKbtm2KK1bNr9mF70EGRULAAAgAElEQVT5N0SIdQ7rN9EMRBfr5QaAFxn4OMMhAhUNIf3xp0FeAJkBAg24VwdKXEEuM+MLyKbeGFw1rq5aVsc+9gSsVJpXUFVLr0M7Uc0VOtfMlMrUI822UUrKal0ItGTYOEaxR1xhZ4lE3mBkAgc+bUqz9qXR4BG9yEh8X8kF4HaDgFa0NCauFpopeK+++eabBJ1LSkqK6SNDha7ExES5jImly1dffZXatWtn+nG4w9AjgCx0FD34RRCpX+zL0zt37nQ7MJYnsUzpRjgS0tz1hh5InBN51T01fXLpup+vfvwlrh7Jie55FCw9G50dRbKUETz2Q9QUL34gIviApOIFgUusFqDqNo/2+UjssZAykc1vIDKqluML+vI+f87L9wXSBMN00eSIK152cA/HCwxKvfrboI1GdjnKvmZkZAREXEFa+5ORaCcirJOFv5hI4qoOTex+StRLmHJxDgDBzYrr58FVYI8okX2E8lL2i5dVEdUVnCjZnvzlkRj7BVLsEVflJnDbc5OpRr2WfqEhN96RThnjdlCXiRlU32Bu795fN9DnrwyWuRSwz4yWxsTVQjN17NgxKeAvVqwYQToQiqVfaCdRLxu+kWj4Y3n44YdZM2mh6yDQocD7ERFZRAbVv3v37pWVd5xInqcoo+vvfSS5aKiKezRVk2VeQB5sy70iNKvr2ekfcXUmy750lx4jfQYirs8995yMpDZs2FASVUg1Cm/zvmTvhovmBcn5GjxMh4UlnHYx03vEVd9KC8dz18gaGKPri5vJxBWVslAxCzkFt99+u9+XC5J0k5KSqFOnTtIVxt+Iq3HSah+aWtYX/+vRhUBLXGXSl4bgupHaxbSqbUPa0lG5BtjIsbujWV039wJjYMUecUVuC+7XA19bJMq7+qs3PUxbJnakX9sYkwgojI8f2ifcBbr7XdXN2ByFbismrqHDNqCe8ZaOt/XRo0fL+uOhaFhiRnT37bfflsvN0NW+9tprVLly5VAcjvuMIAKIzCLqqkdce9RJITfbTTVWWBJ1yy4wmdddSlUena4RVw9L+JJlwJdzkchy1rdLcjKal2PRj+a6RuLyEjVk2AsZNbrUbTQBJ4JTG/FDy5cFoX50LkLgSfJhI5gO/1Sd68A84uoDGh2Salj+oLHq8nYUROnxghNoAALerU899ZRM7kpLw1q70aitLTFqYQ8deYA3hwGNhZZv4npMkFAXXawLcc2dv4mq96pIWTp2Vxxx1b9y1MpO0hSU+PWvSb3r2h6GJQLa3jMe0JYW9u+4kdqaiWukkPdwXKVtgl4RRQRC2XAsRF9hxQTpwP/93//JjGdusYdA0BFXt+irB/2qk4WRBxzt25BL9SJfUVPVW0LKc8LQ/BWHHY9uFSQPZvJu0TkdkhuorjL2rhoDZ6QTlfeEn7u0Q7yUyCVkW0zeN3H17P3qz4uGEW9YvzWyLlCBhAQj+VqwYAEh0o9oLaK2homrJnJaMCR7VJN0rLHU9gm3CukQnAUKHAGctKd20ksJRN1Tnf1dPV8l+olhTFxNJq7H5tO8EQupnh8SASauBu5tvIl/CNx8880EvWI4yrGBtKJMrK0eNkkN7EMPPRTTGdD+zUZsbK2rcU3NFw9FTbUgb1IBQxHXQbRTWBjl1RkobHS8lTlCpDaViowVRKSOs9m8e8TVM/5OmlmvtkYF1ZuYuIbmenZ+6dBL1HOJuKphOF1X7vs5R9bdq26hG69VrfSuW61MwEBSlitiRkgyiOvcuXOpdu3aAQGOSopwIujbt68ksIaJq8GjSSkB/kS9ZPyrIgIgsIl580RRCRBWd6srt0M6+lTEtZ24LzjbYznt4yDNBgcvN2OpgA2twCQCCmklFYCvNF6WoqVxxNWCMzV79mx68cUXpZ8r7DFC3SAXQNk3VbULhvc4/hVXXBHqQ3P/YULAU8RVrCs6L99qiaw2mmY04up0PnrL/J5lBH5FOpW1kocSnZ5gZalAmC64Qn4YOALYbKwCa8oaCVpZrIqZTVwDG5WV9oo94hpIcpbNEqsO3TUlmaoGMD2cnBUAaLyLPgKwP0IJNliqQOtUsWJFhooRCAoBz1IBzZI/udRrD5a46mpNvRNXT/aeznIA1wxzRFQTRSJIf53kDwGbnoOCDFZAbzvKJjkwqF0MahJ450KDAPIUVPGBQE5alYodNmwYPfroo0xc3UCMPeLqtx2WlAikUMUxG6lDnUCuMiK2wwoMN97LAwKItOKtG96rQ4YMYZwYgaAQMEXj6sYqPSVn2YaqrxP05MvqPWvckdTjEQUP/Xqz+EIJUiWXkKb4MDzw7DMb1ATwzoUKAQQd4A4TaIN3K5JnC+7/RpOzAj1itO0Xe8RVRdmvbNmZeib58lS1SQS+8pDHxQUIou16jpHxwmAeJVqrVKkio65FihSJkTPj04gEAgH5uHqNuGrPQoc0enIP0LXJEn1JraE+cfSYKKPRJw4UJu/UX6fAgZ5uVxBwRwTX6Xu7jZKnCG0kJo6PWSgRgJ83nF9QCnzAgAEccS0EEVdV8rW4KPk6jEu+ev27Z42rhW+LgwcPpg0bNtAbb7whpQMFzYvhO6JIHnV/HghGVm3KsGf2eoXDgOel3F/X/9NoRE2MMWknDZLnYCMSO1yyz/XH6Lsyk4WnOuRDCzri6nWELth7cRaQBNrtGrUt/S/skUMZvdzLFLoRV7XE77S878W7UxLRtrRaHEObDCZPSe+a9uB6EPJJsvwB1DzbkvA8zZeVTyM3PUkkGblasfm+d+jv5/meFiwGsCf8+OOPNbaIBiOuLpZXKsHKdTweLa/wJ6EtLBDsidj765iyzXtPfpeFjb2IKwCCEwUKywRTPcvolCmZAKotfvrpp0Z3s8R2TFwtMQ36g1i0aJHM+O/QoYNcNlJNzzYoQXj91REesK653FptoG7kCg9pN+Lqf5UhrUZQm2RTcEwS5EQvK9jl5u+p7KgeIXdK0PH98LHwVEdwaM6Ez0lL6qMAgdN1aCeRtshuHacld9fr1S0r28dxjFkT+ScVcABu9GUsgjNknUM7Y6xrbyVeNNU9yFt5V+PnZLsXOfn0Gt/ZfUu9+52BQhv6xNUmh3GURA5mXC77onb8zJkzady4cXTLLbeIbw0SV9kPsvltPqvtVmuqW9mP4UpMjfz/WOojXirLmXiG6MruUEDKisuf7mOTuH755Zc0atQoKiGirgNfWyD+NRtzG8ZnT50QhQd6in+P6wTG/JmHyGzLxDUyuBs66r///isrpxw9elRaVcCywhGxgiYvu5vd/Ftzc6d0Sto5yD1ypSF5yCT3bLitb2UjB2zoIa99uGlJqYfohM5Sru743RBzfaAxcTV0UfFGjIAPBIz66apubAR1n1tVtMPz00X1pGRhoRQM5EEQVw9yFRsJtduxycw8D00jGSkgrj6qcol9NorVomAbvFvnzJkjXWXgLuMfcS04upGIaySIq21cFGDVLJxfbBJXnJlyF2hw/e3U6QF4+Jrflk5+gTav+DzqSr0qJJi4mn9NmNrjW2+9Ja2qYK/yxBNPiDKA8ylVSQEEGZUkD7W/HSQWK/Uuy2FeqhVJMupJKuDVG1OdpjPRdVoK9rG/jLwJot3UUyq5HpJqedio/yLrFU29HrmzwowASGQWxWXoJLAFaE/mG03ziSukTAUvxz5eeF3vMw7ZicvqkcnnryoooihMly5dgiKuWXH9nEosGyGq2n3MKRigmWmXKlu+rwG9LWKXuEIeCJkgWigkA0oigP5nzZpF9erVC2wKIrgXE9cIgm/k0AcOHKDu3btTmTJl6Ntvv7UnsXgzd9f0qlm+zYrLoOTqsP/ZQonazGlfxFVLat0irnqlPsVSoZ0s7hMlITPjlW7RiB7My4NRC5auhpIjrkauJ96GEfCNgI5USI+wuSbv9d8hImjupXzV8VwTBHUrnil7MlIvxC7E1U3f7DJWTy+2Hl5gtXIHbz7C7lIBjRbf0EqUb9S1W6DcK5JykaR1ww03GCau85PGUYqIIquqV0Yjrr40qE5VtPw7Ffetmbj6RFBJBswmr1rSCjlK7969fY7FihswcbXirLiMCZHWnJwc2rjRg/eFAY2WreZ7ptA9efG7tB9Xu/RnhCIrzaIspSgeOLQjXkSF4yjLkWSFjg0QV4PVj/QfMExco+BS5iFGBQKedef7HKs57n/PSmKgV1nKXfvsknhpj2wW7JtL8+dXF16o0MbbNa7yxXsU1cFKjawSayetjuprLkl+ritNjuipIsXO9wwjxLXdam8yK4wpQdynM4Ke5ccff5y+/vpryszMpHbt2hkmrvLAGmKooqvdsqfbK19hA2hL51G+h9KtehHZwDSuBqpsaZHyq4JW7EZcFSRa8npVqy7UUcgGAtW8QtOaMyWVtq//SnYfzaQV42fiGvQtJvQdrFy5kh5++GFJXJNEKUFvsiy30SAC0S3bthzvbdncCPnViSw4lWRMX01tB7mUEXUMyDdxRVRjZ3wdyotLdlraki4FmfFe3BLk3VoQcx07pNBPDx+BEYgxBDwTV+jj5eoNJD4aeZICoCCqWlBm1/a3Kb3OPP5dO6/OaOFUEVfYnbk4jOhYqLmXAV5E3Z2iwJr7hC+5kca1Qo+Uy99RQcliMy8ClN5etWqVtMRq1apV0MQ1ubE9YWtaY1rUf44g/0/QoOrlqJIs4bpKyD+6knwXEM3dVUDsm3REOL00EU4vJjSOuBoGEeR1/PjxdPz4cZmw1bz7QLqmU1/DBBaE9aelM+iHRVNlIlb58uVlVUxnlyLDw7HMhkxcLTMV3gcCmwxDlhWeSnMm9qBFmWSLhGoyf3WPOvBtStvxiFxyMtzUTV6RWxi7O5b9PPSitTNykGJhWwQCnAw7LHkb1c8q9vXQUYfkikiGp5A3ZATs1EX+zTn9/auXXnl/EYXGpu4Qqze+ZQG2CKqHamkO4mmz19J3DtBIAVxfvD3dA9R2utp+zy+4ehFXVyJeO0uQVa/LUANFgCE56AvpwQcfpPXr19NHH31ETZo0CZ64Vt8kchmIeuyYI2zMntA4BDBxDXqyQtwB/F3HjBlDa9eulUcqXqo81Wp0LV1evxVVuaIela1YncpXri6/O35oH/19ZB8d3PUr/bFlPe3+eQ2dO31cftemTRvpUlG9um3baG5MXKNk9r777jtpi+Vfxq8m6uEULT1Mhw9XEm/bmpMPOuJq78uj3st7xNXpoaHpA5EYY1ENjrhGyaXMw7Q8At4s6lyW+L3oO6V0SGrc3R0HJASOlRSbx66+L6x6cc2h+EwXH14vRStk/16JazfK9vUC79DZuia8ut9rvMkMApnu++67jzZt2iSDFfDZ9MtVQBPRzE0fZyPajmV42/J9AXkNnrjiGJnxWjLs44w54uoRoP3799PBgwfp0KFD8gNHob///ptOnz5Nu3btkh6vf/31l1+X1CWXXCITsGrVqkUlS5ak0qVLE35XSRAAFDjCBz+XLVvWr34juTET10iiH8yxPd20PRFQF+LqZvjutp8P2xfN2J00bYEQV70x65rMewOMiWswlxPvywh4RqBg1aNbti3i6EisUn+7Qo7kbGPnLA/wpHF1yAfk/Uyb3KWjcZWRW5H86VhFcSHR4KpKyoB1bxMirgoTbXKWNskMOCTmdTT4cm38GlNG9J999hnVrVvXOHEVpLBp/9XyQCgyMGinu4+r7Z1BaV4N6lA96k+962V1z7gQE1cQ0e+//55+/fVX+vPPPx0fkFV8Z6Tl5+e7baYqa3r7zlffpUqVossuu8zxqVy5MtWpU4caN25MV199ta/dw/o9E9ewwm32wXTImiHiahtHbrqoh5xst7aJVMRVPlw0TgcuCRTiSSUelF68ZR2QMnE1++ri/gonAulCR++8Gm5buZHL5DtQhcwmNwLpHLTTrnkVRNGXa4Dr97qFKBzr8B5cBex6WeVcoqrreSp64Fih0lRPy6szkKaKBFLXCoNGkrOcPGmdpApG7lHGr6fbb7+dtm/fTl988QXFxcUZJq4OQkpCGtBxjvd8CMPVqrxpXI3qX50JsrfKXcZQio7krB9//JFyf/6ZNon8FPwMlyBvrXSFylTm0qpU9tIqVKbCZVT6kspCGlCGipcoTcVKlqJixUtRUfxbsjQVFT8Xw+/lp6T4/5KysMD5s6fpH/E5f+4UnT9ziv45c5rOncXP+J34nDlJp0+ISO7Rg3Ty6J/ic0BIDPZ6HRdILSQr+FxzzTXy30svvdTYVIVgKyauIQA17F26aL30snr1iwc4R1V19xMn4/TA8aUZ9SviWrAk6dCO6fZfMM6EtNnUY+Gdgelvwz4xfEBGIDYQcKsQ5XAAMZewhRQtrX+rIY28ODeZEwZajPO0aXEd2l+9ksMmFSDo06ePXBpeuHAhXX755YaJa0jxs1Tn1iSuSKTGByT1p59+ckOsaLESVPXKa6jqVU2ofJUaVFYQ1VKCnJapUEVoVatGFOEzfx+jU8cEmcXn6CE6+ZeICP++mfZt/UH8/pDb2GrWrElNxUsukgdRKKlChQphGz8T17BBzQdiBBgBRoARYAR8I9CtWzeC3nHJkiVSg+iXxtV39zGwhTWI6z///ENr1qyhpUuXyrk6duyYE7blK9eganWbiU9T8bmGKtesRxddXDTq8D9xeB/t37aR9m/fRPvE59DOzXTh338c53HxxRfL5C9ctx07dgw5iWXiGnWXEA+YEWAEGAFGIJYRwMP/8OHD0ssViTRMXF1nO7LEFcWAli1bRguzs+nkiROOwZUqX5Hqt+tNl9drTtWuakqlK5hiIGa5S/2fc2dENPZnSWS3rV9Kf/7mHF1u27atrPiGTygisUxcLXdJ8IAYAUaAEWAECjMCCQkJ0rsTbjK2bO/PCjMcOucefuKKZKqsrCyaM2cOnTx50jGmEmUqCLLai+q26iQtqgpjO37oD9q2Lpt+Xb2IDu3Kc4LguuuuowEDBtgLaZiDDhNXc3AMey/wY0PG6f33309PPvlk2I8fqgPu3r1bntfnn3/usP0oV64c3XzzzXTXXXfJLEdujAAjwAjEMgKIWMECCd6dJUqUME5cD4ukrLHw6y4oFgC7quxuY2zFHzxl9GM/kcxFaYNFDsFknRyCtkLjW1CkQIs9ChZ4q6x1eP5iWtW2KzkltskOjCZ26c10+Ijrnj176P3336d5876kf/45LwdTsswldFWrznT1tV0lWb3oootj+XL069yOH9xLW9cuEiR2IR3es9WxLxK64E+MEsbKBcGvjjUbM3ENFLkI74e3v549e8pRLF68mCpWrBjhEZl7+HPnzknN0MyZM+mHH35wdN66dWu6++676aabbqJixYqZe1DujRFgBKyFgK7biUtpV2uN2JTRtGzZUpCkfzRlvo1HXF2JpDfiim07pmwTY1bE1N3XVRLMpr9QtwCJKwlSnL66DiX3KueCjbWJKwjr22+/LUoPz3eMu0b91tSs2wCq0/xGU+Y51js5su83ys2ZSb98/T/65/xZebqw1kpKSgqqehcT1yi+ctKFndXUqVMJnn8pKSlRfCbeh75t2zaaNWuWtIY5deqU3Bi6GejAkM147bXXUvHixWP2/PnEGIHCiYCel3QCpaXVEfc71/JV2hKz0Y8WsrXRUObb1owTVyI7+UytRpleLLEKLKm0xNQXccW2k13s0vTw1kZoRZ/pO0Q1RBEFtkd29YoyJqT5UcSAQhdxhbY4MzOT/vc/QbbEywOSqa6+tocot3ofVa5VP/ovrgicwdmTxyl3+Sza+NV0h0MBbLWShR1n8+bN/R4RE1e/IbPODshgRBYfopOoaWyzTYndBl3RokWLZBQWFURUK1OmjHx7A4lFdTFUB+FmHQRQshCm2yhhuXfvXvlBNAMNlio1atSQH9iqtGjRIiZKEloH/WgdSUFUFQb/2d1QOlaci8b3mdKT6PdBnsvORuuZgywh4ooVJfzN+E9cnc/cc8TVvfhAgqlSAdf+BZnNEWTaIWUoiLju08oZDE1caIgr9KuvvvqqlGlg+b9e+z7U9rZHIm5VZQiSKNjo3/PnBIGdTevmTaIzJ47IEd9yyy00cuRIvyp3MXGNgsn2NsR33nlHvh3C9++ll16K8rMxPnxEYXNycuirr76SVUhUA2m9/vrrZTQWCQ7RVMbO+NlHx5YbNmygd999V9rF+NMQQX/ooYfkw5tb4UQAvrFjKVWUi7VlZavKVfB7dpBYFCNI2kmDMnpRLOVunz17VloLQduq6tMbjbiqEq/a6KVPjauqtmW4IIHzNelL42qbQFHRK7shbRx0RKPBtY5UAGVUx44dS8uXL5fDvapVF2p3x2N0STUUf+BmNgJwJdi0dAatm5spCyYgcJGWlkbNmjUzdCgmroZgsu5GqGPco0cPmYFaUGXFuuMNxcgQwQOBhZce6nurhohF+/btpSUHyGwobDlCcT7R3icirHgIKMJavnQJatOgBrWOr0lXX1GJLq9YjqpXLi9Pc9+h4/THkRO0dddhWpe3h9Zu3kvHT9m0UCCwqampHIGN9gsiwPE7ql4Z2N9T8RQDu1puE0T7kJyFmvKrVq2yj88PqYA9ASuV5tn1qx5O0U5Uc4XONTOlMvVIs22XkmIrGevaQIYT8ybIkr+GmiLCkAdkVaSMbr9QUrbYc+pq94peHkvKejqSeRFXPD8GDx4sfXOLlShFNw4cTfXb9zZ0irxRcAgcO7Cb5v93OB3Zu11EuC+il19+2ZG7461nJq7B4W6JvT/88EN6/fXX5VI5/i3MDUlriMQisQsRvwsXLkg4lEEySCyisZEsVxfL8wPJyvjx4+WLVA1BTm+9vgHd26kJlRPk1Ug7IUjrJ0s30ZxvNtNeQWrLly9PzzzzDPXuzQ8SI/gFus1vv/0m/2aGDRsWaBdh2c9bWdawDCAMB0EwApInXPsrVig1qP/ENcOeDIWIaFZcPw+uAntE8tERykvZL5KvGlK2rAJeU56l4UhqfxDduqIscD935wCtphVEVkZc91MdOkT1U+3bK2JrP64xiM0hrpCcYXUHEddLq19JvR6fyFFWYxNg2laIvq6Y/hr9/PVs2efjjz8u3QeYuJoGsTU7gsYVDgMHDx6kTz75hBo2bGjNgYZ5VEePHpVLP3ggI/p3/rzNygQtPj5eRvTgUgBdJXSy3IJDAKR11KhRspPbEhpQct/rDBNW1yODwL4wWVSj2fCb/Apv4kxeg5sf7d6HDh2Sy9DQUOJvQ2mOC5KBzDtWID05lZk22EGsRF2Ru4AVIhQeQAECWwuQuEriuJ8SlSOAix2WzaqqIW3pqFwDPCVfuRNT6UiQdz3lxH8jZB3XSxutvES77ZZ91Lnpi4kGCV2riLgm0je0M74ypeQJyYCIvkrpQHIFmp80j/IViTU412RCcta6devo0UcfJUgzqtdrSTc/+ZaMuHKLDAIbl8ygFdNekQeHc5B6luiNhiOukZkj04+KpCVEpho1akQff/yxDLtzK0AAUYxvvvlGklhUPcHNSjVEY4EbSCy0ZdDZcIKXf1ePlrS+PKQz9e4Q718HHrb+8rs8GvX+EiavQaKJlzgtUd25c6dTj5DRYMUGEo9oabEafT1y5Ii0+6tUqZJcPQqcuB4TDgDfUH1tJNSFuObO30TVe1WkLB27K48RV4fnq80FQLudTWPr4vmqiai6b0vkn5uAujqDi7giR6Jv374ymBHX5DrqmTSBLi7GzjSR/tvP+24e5UweI1dKH374YUpMTNQdEhPXSM+Uicd/5JFHZKWVp59+Wlaq4KaPwJkzZ6TNDN648TDPzc2lf//917ExtLEwSwaJRaY7fma7Lc9XEyQZ0IihmUla1RG15HXy5MmctGXgDxtRO0RTVUR1+/btTnuVKlVK2tDgGscLW4MGDaScxjpNzwrLy+gS0ignRpK0EA3HS0SVKlXki7ZfxFWSylzBBom6G45i6vu06hFXSUx33CqwLihw4L6dPWrrqnGFTECMrbsk0prIrt/6ViASOHHF/f+OO+6QqwxVRVnW25/9gC4uyp7gVvnb/1FYZn07fbwcDuw+lTWcdnxMXK0yWyaMAzc8uAvgLRIRsGrVqpnQa+x3AZst2DWByGLZVGu1hbNHdq96yIPIIjpbtGjR2AfG4BkOHTpUvgBAHvDC4E4G9/JvM8gGPl+xWco73nvvPf92LgRbnxD10vECAaKKudiyZYvTWePFCysJ6mWscePGUVPAQyZp0TSxrAw/rIIWqxFX6PShxb/ssstk0qk/xDU3fbqwCAMxdLe6cvszcLgIKOLajnaKyGOKnskqdvZAMH1qYe0R11QhKciK60PxmTiGJiqrdLB+uRoETlwnTpxIU6ZMoTKXXkZ9U2dRqXKXFII7RHSd4sK3n6bt6xZTrVq1ZIld1+ctE9fomk+fo509eza9+OKLMoqCMnXc/EcA0SqQWEVkd+zY4dQJiGz9+vWlThYf/Fy3bt1CKS9QEgE4Byx4bWDAmlZfswTNa8+np0rHgTfeeCOoqiu+jmX173F94uUK5BT//vLLL4Slf5WIiPHjRg+Db+2qQVTLX3QqaMUqcT1w4AB17dpVBh6ys5GGj+aHxtXqF7Ap4wuMuCK40717dxnc6TX8TarT7AZTRsOdmIvAudN/09Rnegmv179o9OjRsty7tjFxNRdvS/R2//33048//khjxoyRSyLcgkNAJbIoIgv7FNcGTXGdOnXkkis+9erVk4Q21i24ULUN5CkUEgFXjJVkAPjOmDEjuEmNkr0RfVMEFThv3izcFjxcf0qnrQo5QA4Qa83ZImsgTduYTM5x2Og/Y9jJgVzB23LBggVMXHWnNDDiOm7cOPrss8+oWt1mdOeoD6P/YonhM8gVZWKXZ6XKJEW8wGlfvJm4xuDEQ7tz6623yqXAefPmUeXKlWPwLCN3SigJCDKhCAXIhGuyixodoiZweVCRWZDZ6tWrR27wJq2ZZw4AACAASURBVB5ZPWARbV3x1lATe/bcVcKjk2TUFcmIgeKIcSNSDImDVVp+fr7U3KlIKq4pRFKRqOPaEE1FhF+9JOGawosSfD+5RT8CeDGBSwyqys2fP5+Jq0nEFdpW+Hojn6HvuJlcvtXifyq4J34y5k46smebLE7Qq1cvx4iZuFp88gIdHpJYsKQKP8C333470G54P4MIQCe7detWWcVLLd8ic1VrwaW6QiTsiiuukPodPJzwL8r14meQMbxwREND1POVV17xW9t6eKUwRp+kstqb07QpHQxHzZIzFkiLLH/tsUBWYS0EvRRIIVq4rZ9QyhPjAEEFOdm1a5f8Wf0L43nXBlmKkqTgBQgEFaSVkwWj4S8ksDEq4op7BAIPtsZSAWc0/Y+4whpx+PDh0vrqjucmBzY5fux1amUSTZ6kLxiOG5pDfdqbX+9NHnN3IiXdo78OIb9f24MGj+hFgbzm+urfD3gMbfrTstn09dQX5QoE/MFVY+JqCL7o2whvlVjGBZkCucAbPLfwIgCiAmN3RWZBaPFBIo2nBslB1apVJZlVxBZLhvjg/60kPUhOTpZZz/7IBCRpXVuPckbUt5fpPEDpnwrT83uqGpocJRfo3LkzpacLt3QvDTjDAm3u3Lm6ZWdDQVzxAgMyqv3s3r1bklNU5tG6V7gOvVy5cpKkIpKqIvSQn1gr29/QNPFGQSCA6wWexQETVw+G/nAEyIy3WVg5NVnylYTsoqvmBdKW3OXqy+r7tLBfIL6svnsOlrgi9wM5IG1FKddWvYf4e0BTtj/waVOatS8tYOLoaxC+iGW0EdfjB/fS1JE9CcGelStXOmw+mbj6uhKi+HsQJnjVwVwf5WC5WpQ1JhNSAxV1UwQHDyt8UETCW0MErmLFitLjEfOJn6EBwr/qZ/weH/y+bNmyITtp5SYw+bnbqOX/t3cd8FFU2/sgEkLA0IUQMC/0IoQOIk2KEARREOQBRsDGe0ZBsSAoIfgQ0ccTIUh4KiASBFGwQahBRQUpIhIevQmh1wCBgPr/3+/Ozu5sy5bM7s7unvv7Dbvsztzy3cndb8495zu1Y91oJ4+WTRUi5b2TaVS8G6c7OGXrXpGecdJSGXT0/vvv252hktV169ZpIrKV07D1hFKkSBH56g5xxTUIhsKWvXpAExVziFd8hvc44Audl5dX4MAwR3FxcdK6rlrb8VCC9+zS4909ocdV2IpctGiRVAxZsGCBHlV6XYeexFXRVXXcFUU/FVqvs8nulHY1qd36/dapWaEqMPwkdZKZskzFTgnAuMQVEpE7duygXs+/R3EN7/Z6fry+8NAUSptwiLpOTaM6pb2upcALXRHXwrbq6/od9W/W8NZ0M/+aVUp7Jq6FnUmDXw93AbgNdOvWjd566y2D95a7Bz8sldQeP35ckln1sFU3cBctyOqoxBavILWw3GK7GUQYrzjgoqC+t33Fefger/CxBCGGFR/bmiuEmkCMSO/quijEdUxMP9rupoXVts4TIg1sd6EuYO3/RzJDGsTaYV3VFpWsagmr+v348eNlIgocyD6nJagqEXXkY+pqnKqlHBYzHHADUS3m7IfqCj3/f79z504aOHCgtGwjcAdW7kAW/P3Dnw/3EXyxleKeq4A1UVWyXd21UZPyVdRklwSARCpWZLJKryz1WQkZscZUEBZYkQJWkFrK0GTD0iYwgKVWiB4kzdtoT3zRZa/0Wd1F3nNXAXW9GjhxKZWrUt3dhnQ6T8RFTO1Ee1vaughk049DB9E2cytJ1G/OKFL2n5RrzvbOIJqgnqN8T7DcrlAu0rodqMRyWLV0i5tCgsXCa29xVdpYvd3Uge4Z1m4GkmyrjzVJ1PWJQ7S6AFcEncCyqmZhSn86+/seK01XJq6+QNpAdeJHuU+fPpIMzZgxg9q2bWug3nFXPEUgNzdX5tXGAYsfXkGu1P+r71XroCO/SU/bdHY+SCGsl9vnJLtf5aU9lDxyNa1P6KpxF3D/cpyZMDRNXoCHMvitwl0BuKilILLqWUvK2cgZr1q5VUs3yL/2vWr9xgMBZ63zBuXAXAN3HiivHD58WOZHR570QJdCB2fZuQrACrqB4tKEK4Akm0i1WlUZpnQT0FhQCxh8EggsraLkI3cp7ga2dclrjWtxhdoGYg6Gz/qZbo2I9Os0O96iV0jreY2/q+IXG28iryqpVMmshWQ2Gbed7sbzlY0VV/WrtZBZ0zUxCiG17of1dypRNpNrWwvxpWX09cgxdMSW3PoYyeXTn6ODv2TRtGnTqEMHRb6MiauPQTdC9ZDGgkQWtiJhkfLl9rERxst9sCAA4qoluZLoCsKbJ3wx8R0sjVjM8YCjvqoWSHyHQ/2/9hVb4l4RV9k14dc6dLFipenuufVVJa6LFy+WwSsQ3LdNGuEOeYU2oGp1hg9VlHCpKWfjfgFrNZfQRWDWrFkyeBUWfKyNRkgsgp2WxMRE71UFVOIqM1V9Yb3dr51K09Z/ikpEC5hmWGnnxg2UxNWK6MJVQFhrzWTWDeIqrcKw8qrk2avbyzOLK5JzIIUo1rCn0jdSseJ+lIqThC+Tatu4CDgms6qVFcTU3kprfw3IbzpVMNXtsE5JQEmS4du0wVmaz9UIA60rAPxxN1WzthAHwlUgc8YLdGDLaiv9biauXv3RBN9FqmM6ZLJSU1ODbwDcY8Mh4LmrgO0QTATWA+ur6ipgrXFJMlof1ldo7VrSZCrtqQRbfVV74Y6Pq+FA5w7phgB2oXr37k2wun700Ucys5gRCoL44NrltY6rg0xUIJ6ddrd3QhY16VcdACAtrWqQup2rgGK9tc7YVXBwlj+JKwgrHk7wt481Am5XSW8tp+iK7vjk63E3OHMRIMX6abftrj2fTK4CJuuq6I5j4ioi60wWWId1SuK8m1o6JK4OHKCla0FrOirdFCxtA41AENfPJj5KJ/f/SvPnz5dJVVCYuOpxbwZBHVeuXJGLNAJIENSC4BYujEBhEPA8OMtBa9J14CwNd1MSy1VwFlpQ05/C73Xt2rV2bgSeBGcVBh++1tgIqIlajPYwr6Z89SZz1rLkCUrKVpMltEA3AG3K1+TzNET4t9oKNIFkruzmmriS2T2htCFUBUBSYaDZsGGDvAkR5zF9+nTatm0b9R07j2JqJvjl5rTe+rdu0l2Lq5Y8ekVcYVn9sq5UMiAXFldLDx0TbqmKINIvO5Pb8gWo817sQblncygzM1PGCzBx9QXKXCcjECYIeC6HheCsbRQ30qLbai+PVTB4nshhqTVBXcORSwFbXMPkRg2yYcI//Z577iG4qaxevdrUe/eCs+TJVj6u9j6nFuuoCkzBFlciJcirJ1itE4urBeLA+riCsP73v/+lJUuWmLsEbWwkLIEsJLSn2w0cTQld/+77u8LkE1pO9Ue1a9GJj6tZZ1XrNqBc7BZxFdqxZh9Ysm7D+nrH7W+LSZP+s3akWw3U8qOPa17ueZr7XGfp3rh+vUUTly2uvr99uQVGICQRUBMQdGlWnaYku6sTrPFvlaj4JwGBOgFalwJXOrAhOWk8KMMjAHWL9u3bS/UP7BooxUviek6Q0hQhdyV+8yF/NXz3Ow60XMU5TiyuaNmK6NoGc3khh+VUT9ajmbH2ccUuC9YjuHzY6mTD2tqsWTNJfJKTk0UCgqYiAcEcj1rz/GSbaH2bCizBUzaqAhoFAIuqgOeuAv2EF7GqOkAaoumM+KqqBraJEaSF1aReQKJv/Vpm0mI/qgrsyFpM3338L6lrjKQzamHi6vkdyVcwAoyAQMCS8jVCpHx90i+Y6JHy1S8d5UYYAS8RgFsXMh4iIcUPP/xQOOKq9sGKcLa2STbgGXEtnKqAXhZZC3GFhfXjjz82uwRpfdlbtGhBH3zwgUQBUoPt2rWTPs39xy/klK9e3p/+uuz//vpLSfmac4DefPNNGbDIxNVf6PurnewpQuakm3CUhxf9OeFn1EnxdbIq7cSWT5qy5WMu2UKAeiV12z5KZE3BdSn0f6m252jPV+rePXy7xWFffq2tx9WgPTnXVV38fSARQHY2RPR7kj3L2/6qbgLIKgWxeC7hjoD1OnJuWbLQH7VPsdnujSwh32TtvZk9JUH4biprGK5LoVS7cwKFLghWq1atZLagjRtVqSr3LK6qjiusq6kkstSN2a8Mw8oyqrgGHJIJCJBFywNXASegyOAvta0C9VsLJsnuY95AaNzup5kzZ0qpRxRtghGVvEIyr2PHjuZqYbX79NNPqXrTTtTjmXfcb47P9DsCezdm0qpZo6UaEvxbtWmu2eLq9+lw9pf/jZAuGWuSLkkST8QgkkrBImud/cT6e9NJNsTVloA6IaWC8CYfGaIs2udEH+b+jdLMIaQO+mpFkLXfOyaj9n13AXhShol8G2ViuB8FIQCB9LFjx1J0VHFaLhIR3CZefVEu5+VTD5F4IFe82v4Y+aI9rlMHBLCemNc0TX3t3hBC9/fZBQKpZ9gTUEcP3HJl1Dx0OyagjkmpuC75iAhGQh+wLs4VGqeW9VaHkReqCqQFbtq0qazD4oftHnEtVMNBdPH8+Vvp7beXyR7bZsRTSaut8gjOhf8w8t5DFqvfuAyqFK/+ygbR4MOgq7C2ZozpTRdP/U6vvvoqQbpQW5i4GukmACmUOaPVRdRkOSXrhV5Z2MlsPbUmh1jkU6lIinsW1+wpyXR4iGJhLZBkyh+b1rRRY5F1ZuEwQ+qUhLLF1Ui3XWH7oqoL9Glfj1KGdi5sdQ6vHz97LS1dv8tpqlefNMqV6oSAso5lJtpbPq0bUHeKbB7MTQSYtJZTuVZapHxgVU0VdlO3LK7ah3Wbeqz744ww6wRLAdWoYvmQc1K0ZZm4auF67711QuZqvVnqTvudSlxhXYVvpG2ZOnUqzZkzh8pWjqf+KQuoWGSU7yeUW/AIgQ2fTaOtyz4kKGssW7bMTl+ZiatHcPr4ZBviKolhZqJD64Tdd04toQX1WUMg8eOQUoRSbcip9mqt9QIkN72u9oeoADJa4I+DpQVHW3o+Rpyr1wEB/LgOGzZM1uQLlwHVRQD1I+lA7dq1deg1V+E/BNwjrgWtd3I3qNMK6m7l6uTdA7DFTcCyC3XXRmO5CyDDIYKMfvzxR1PCGCau7hDXgqyt6vVIvDJo0CA6cOAAxTVqSz1HTKcit9zivz8HbqlABPb9vIJWpr8sUzDjASMhwV66jImrkW4iK+LqzJfU1GHbhdxMXGOs/Vth9ey2UmOdsFgRFItpvLDwDqEjZr9VZ36u+JEYZMlJbWdNLZi4mt0RZPftzzWan5mRbotg6IvqMqA3edWSVmcWlGDAJ7z76A5xdXWOo+8t60iMlX8rLLbdaKVmvTI/FJust/EZ22nIEbH+7R4uXZOMtv507txZam6vW7dOphvmYo2Adr3RBmO5sraqtSCt7oABA2RAV40W91K3pybRLUVh2eYSSAQO/bKOVsx8gf4UAXSvvfYaPfTQQw67w8Q1kLNk27YVccWinE517IKp1IsUIilWaCVISrVqtkuiJPHfbvDZUn1WBXFViaPFNUAlomKRz6pD6WbfVseBXUnadtLrmqzANmTWZjzaa5i4GulG801ftD8mXZvXEG4Dnbz2eYVPa+qcLFq95YDsLJNW38yZf2p1RUrVh9mC1juTK5MQP1cCUNVr8DDdjpKURU+sharPqiCuqh+rxjVAdYdKysiiuukW31aHbk8B9Lfv1asX/f7771ai6/6Zq+BpZfLkybRgwQLZYa2fqyPfVkej2rVrF8HNCZZtWF4Tn55Ct0ZEBg8AIdbTXeu/pHVzx9Nfwr/1mWeeoccff9zpCJm4GmnyPSauykL/t7mm4C250LpncVW2xhIpfsxuk6KACkQBygLSWrGHhmsCx9yCj10F3IIpFE4CecUPCiwZCNhK6t6EBnRu6DaBBWFduHYHzVuxTQZiRUdHE9IVayODQwGn8BqDD4ireU1R/GHdsri23iDdoRLjx9Buk6KAedUzmLIAtrKzs7NlBHydOnXC63ZxY7RI7Tx8+HC6efMmqZnw3LW2aqvft28fPfnkkzJoq3zVWpK8lqkc50YP+BS9EPjjZj79sOBtyv52sawyJSWF+vTpU2D1TFz1Ql+PenRxFbCJknTi+5o9ZQrRKGynqVJYromrbfCWYrVwFASGuhwoH8gmNFZaF9HFekDKdfgfAei7jhs3jjZt2iQbjy4RQa0aVKPmdapQ7TsqUky5UhRTIVp+d+JsLp04f4X2/n6Gtuw5Tj/vPEq5127I75CWeMKECYTMN1yCGQF3iKsL1yiTxJ91gJcz9yQnn4u1cAqNom4rLVJY5lXPYMQVpAzpSj/88ENCoBYXCwK//vqrJJtQBhg5ciRVrFhRKpvItUY86GozLLmDGyzbQ4cOla4ZsLi2H/gy1e9QMHFyp14+xzUC547toxXvvUQXThyUJ7u7s8bE1TW2/jvD0+Ask3+W7KBKUK38WZ103bwF5miBd0fL1bzcO9F9df7DoXg3mAgzXBTsAi78Bze3VHgETp48SWfOnJGLPo4LFy4QBNQRAIEfBGi8Xrx40aOGypQpIwOwqlWrRpGRkRQVFUX4rHz58vJHCgfeIw0gl2BAwB3iqshZOQtGVVyhDtnoUKvrjLU/q2NELA/SWg1XoxLXF198kVatWsXSbzaTifVkyJAhcn1BQOiIESPkGaqb0tNPPy1JracF6xYetr///nt5aeWajanD4NFUMa6ep1Xx+W4gcP3KJdr81SzKXvep8Ge9KY0Tb7zxhlkGzlUVTFxdIeTP772Uw7IirloNVrG1PyVlhQyoSkpMpVE2ItyOkwY49nG1DW6AzHe7NzIoMTPdQcICG+Kq6jk6JMxOJHD8iTu35RQBLOi//PIL7d27l06fPm0+QFbxnTtF9T/Tnqvd3rOtQ/3OVd0QaEc+d/WAUHV8fDzdeeedVKtWLVeX8/d+Q8A94mpOnGIj/6f679urjjh58J6SQpnKokepo+z1Yp0RVzsprQDuCGGn4fPPP3fbAuW3qQxgQ0eOHKHBgwdLN6T+/fubraxql6BuggdeZBzztixfvpwmTZpkzsJVq1Uitek3gm4rz7s+3mKqvQ5uAb+tWUBbvv6Qbly7LL/CXD7//PMy4Ya7hYmru0j5+jwrsW4XCQgcLaiOMmfhB2D4buokMmpl1U03KQhohbY9sbiatvit2naWoQtgKWMQEWQ06JCt4Lijdk31S3KLy2xdGHw9AVw/EMA2XPbOnfTb9u3y/alTpwoEpkLpKKpUtiRVLFuKbi9dkiqUiaKSwjUgqngElYgsRiUixBF5K0XJ9+K1eDF5ROIQ/4dP67X8m+L4g/Ju3KS86+L9dfE+/4Z4Fe/FZ1fF64XL1+jMhSt0+sJVOiWOHOFiUFDBItioUSN5NGzYUL4i9zsXPyIQgAQEir71cNrTSawfckdnLEFBQPs876nF1dH5vkbxnXfeoblz59Lo0aPp73//u6+bM3z9cD8aOHCg9EW97777pHXOV+XSpUuUnp4u/YuRHhZqAyCwTXsMofKxNX3VbEjXm381l3asW0TbV39C13LPybE2btyYXnrpJWrQAOl7PStMXD3Dy4BnW8gjLBLDd3cSWbY0wtlWhNaWfHpCXB0NvSDprIKIpxv6iw63Bg0If5B36aeffiIcIKk7duywG03xYrdSw+qVqFGNShRbMZoqlC4lyGkJqihIaiXhqxrIcunKdTpzKU8cV+msILOnL16lXYdP07Z9J+is+Ny2VK1aVWoCwmcQckOlS5cOZPe57UIgYFYBwIM0Hs5FMgKLRdZ6fVF88y1roqfEVfXLP+QgdWwhhlDgpR988AFNnz6dvN369lW/AlEvdnqShGwEyGuHDh0ICQRu8YPuKlyd8ACRlZVlHna1Bq0poesg+ltC+0BAEXRtnsvZR9lZi2nX+i8I1laUuLg4eu655+iee+7xejxMXL2Gji/0OQIgr2bpLZ+3FhYNwILw888/09q1a2nNmjUE64K2xIqgqcY1K1OCOBqKo3bVCnRr0eAT5z5x7jJt33+SfjuA4wTtOnKW/vjzL/NQIW6N4K9u3bpRp06dmMSGxd1fmEG6kicsTN321y5ZsoRSU1OltRVW13AtsLA+8sgjdOzYMWrTpo0k80omMf+V/fv3yyC5lStXEtLxohSPiqbqzbtQLXFUrd+KNWA103EuZz8d2LKG9m9eRedzFDlDlLp160r/5HvvvVcmFyhMYeJaGPT4WkYgSBD44YcfpJj5ypWZQrfwqrnX5aJLUM+76lCT2lUooUZlKi+2/kOxXL/xB+0U1lgQ2bVb9tOOg6ethtm6dWvq2rWrPNgSG4p3QHCNCUFC0LLs0qULTYECTBgWkFYQHfi23nXXXTRt2jSKiIgIGBLHjx+X7hsIBLt61bKGRkSWpPhmnal2y25SDzYcy9mjewRZXUsHtq6xIqvA4u6775YPH5hDvQoTV72Q5HoYAYMhgC02LLRffPGF1UJbumRxuk+Q1c7Na0qJqnAsx4WP7MrN+2nFxr20+/ezVhAg3SaCQPRcaMMRYx6z9whAHB+ZneCfPX/+fO8rCtIrtaQVrj0zZ84MKGnVwqjuWmHHCjtX2l2rYsVLUEytJhRbtzlVqdOUbo9rQEWLBY5s+2r6L5w8TCf2bqOcPVvp+O7NdPn8SXNTsKa2aNFCWlbx4OULQwATV1/NLNfLCAQIAWyrwUfum6+/ppvCNQClTMlI6iKyWd3bqpYkq0X94CMWoOF73GzOmVxasWkfZQoSu++YEjiAgoCuxx57TPrVuat04HHjfAEj4AABSMvBDxuKGatXrw4rjGxJ64wZM6QsnhELSCz0qkFiHblegbRWrpEgSGwzihVEFu+DLTsXVGHgq3p8zzZxbKWcvVvo2iXLOol5UV2vQFb9ET/AxNWIfw0e9amgyH4HFWlUASxBCjZBVtqALptsWXY5vZ1EDivpXgtOCWvbO3u5G4+ACPuTQVjfe+89WrZsmRmLFnViaXC3xtSxSXzY4+MOAAdPnKdPs7JpyXf/o/ybCumHtFZycjJn73IHQK/OCfAaJvtsk0Lbq3HoexECCVG2C4WPcCmwXmJbGe4BsLQambQ6mhMEusIKC39YpJJ1VCpVbyQtstEVY6lU6QpUokwFKlm6IpUqVymg0wxt1bxLZ+gqjgtn6cqFU3Tq4A5BVn+h/DzHKi7YlYJ7la8sq84AYeIa0FtF38btSKXMNpPiQGdVtAvCKdIfpqapOoeazDUiv0yCkNDajmQGVsFRWiKqUS6Qi75FRcChfIxde476oC8e4VLbuXPnpHwLAjpgAUAwVaKwrD4i0q3WqVYhXGDQdZy5V/Np8bfZtGD1drNCAbZtR40aRU2aNNG1La7MgoDP1zCHD9pJQl7pEI0ZA3VqbXGW/c8/M9ajRw/KycmRJKhy5cr+aTSArYC0IoPVgQMHgpK02kJ38OBBmYBlz549tFNIDCKFLxInFFRKlBaJVcpWFkdFQWZvpyhBaiNKlKSI4lFULLKEsNaWEIFhpaTVtqg4iuFzeUTKz/LzLtPN/Gv0hzhu3shT3l/Loxv54v11fCaO61fp2mWRKOb8aUFOT1PexdOUezbH5cwjuApH/fr1ZRpivA+UJZyJq8vpCpYTHJFU58TVLCcjh6cloeK/DtPEauoiQXrn/o3SVHFEG1LKxNV/9wz8V9988025IGL7v1eb2vTPB1sHXKrKfwj4tqUbN/+kzwSBff/rzXT+8nXZWO/evaX+IGfu0ht7X69hqlUVqao1D/Rm9ZLWtDF5LsWlabWu9R6j+/WpaV/h9gOfwVAuoUZanc2Vmk0QSgXYIUPAF16R0MUIpWTJkgTZwNjYWKpSpYpM6AKCil2n4sWLG6GLsg9MXA0zFYXsiJ0lAWQ0lYqkaBZonCMJpxD4Tz5CQ8zWVlO6RTuLg6lPSArgJJVsUgZ+BDrR7uHKq6zCUYIEtrgWcoKtL0ca1RQxt99++638oqvwX32m710UV7mMru1wZQoCUCVYuPY3Sv9ys0yYgIUdIugQ0eaiEwI+XsMyaBCt7KYmIwBJBkkVUevaXSnzGnmnToPyvhpkcFq4cCGNHz+eHnzwQe8rMviV2DGCpTVY3QP0ghfjh1YtDljaQWrh65yfn083b960Om7cuCF31/A53uMctSBFNtQXihUrJl8hH4b32gMBUyCmWMeQblU9fBFIpRc+2nqYuPoC1QDUCQvq3Lg0U4YYB4syrKSdVlD3jFS6i+ZSSvohWr/etDUmiGZGYialUyql2aaF1SzkahvdVibIHwC8Ct1vjfB3AQNn4qrbXYFFDXm6T548SSVEBqpXkzpSzzZ1dKufK3KOwNFTl2jE9GV0IOe8FEGfOHEiYUuXS+ER8P0aFiPIqunh2mV3bXahXJ6v/wnzMzLo7bfekn/rI0aM0L8BA9SINQySVyBr8Jd89913DWXZMwBE3AUHCDBxDYnbwiYTlZkkYusLFlekQEynOllp1LO8ZcDaLX0r3zKxdZZ8ZIhCYm2Iq20+bwRhDTmSTCmC9KaKf/Gqkl9rdwR3gA78j4U7vQzkOfCZeuqppwgW1+oxZWnqs/exldXPEwLr69sL1tNn3+2ULT/77LNSfYBLYRDwzxpm38MC4gAKMxwdrlW1XBH88u9//1uHGo1VBSyMsLTC4tq+fXuZpcrfyQWMhQj3xl0EmLi6i5SBzwMBTa/7BsWP2U3dto+iGGF9VQgkmSwMjoMMvCGusOpqLa4ru2lcBLQY2boLsMW10HfQ5s2bZQpIbAs1qx1DM56/X1pcuQQGgYVrttOkDGXXon///jR27NjAdCQEWvXHGjZcuApgh8j9EtgH6UOHDtEDDzwg/Qzhyx5KZe/evfJhLzc3lxITE+XORWGzKYUSPjyWghFg4hr0d4iwVKj+qmYfMZWoOrEmIBjBagVXomozTdZSK+urC1eBlZRE8w7VpSyFT7mCRgAAHstJREFUJVtZXK2gdeQ75ojMBv18+GYAcOaHIDl8mto2iqN3kntQRLHCpc3zTU/Dq9avf9xN42Zn0V9//UX/+Mc/CAE1XDxFwH9rWME9M5b1FfqZCMrC3zzSNAcqgtvT2XR1/m+//UZPPvmkDCjt27cvjRs3ztUl/D0jYIUAE9eQuiE0klYytsB2Ibb+vzOLq5UqgAtXgXbt2ll8Za2wtLZW2MvcoHu2klwhNRm6Deb69etygUf0aUKNSvTh6D5U7FYmrboBXMiKFqz+lSYv+EHWMm/ePFL1NwtZbZhe7sM1bMhh4ec/lmxFr5wCjaBUVTklQLMBTVMQvY8//lgmxAj2AgIOTWQEFD366KP0/PPPB/uQuP8BQICJawBA91WTIJyDSLvYOiKuFrkXh7JVWmuszcKtnm/tKqBG6SrKBFofV8s44b9m72PLxNW9O2Hq1Kk0Z84cur1sSVqcOoDK3FbCvQv5LL8h8OJ7mbRq8wGqVq2a3NZlXz3voPf1Gqb0SiHHmYlZNsGoxrK4oqeQuvvkk0/otddeo4ceesg7UA1y1fLly+nVV1+lP//8k/3CDTInwdoNJq7BOnNW/TZlnom3tRDYLMSO9FaFb+wh4Rs7XPjGCnYpfMAs/rBKcFU8ZYjv7rRJMoDmbYmvY+KqaCceesP2RwK/H2xxdXX7QQ6le/fucrtw2oj7qENjzoDlCrNAfH/l2g267+V5dFFoveLHuV+/foHoRhC36Y81zBoej5IdBAjZr0XaZtxPIK0gr8FakNFv1qxZUonj9ddfp549ewbrULjfBkCAiasBJqFwXXBmPTDZFiT5tGyOWdKqmn4oSJHCGiTOUdK02vRGTfmaQZSuVRqQW24u3AHktUKCy0bNwNwCE1eXUz9hwgT6/PPPqXHNyvTR2OC2uLgcbJCfsOS7bEqd+y2VKVNGZjsKFZ9E30+Ln9Yw+QBuW2zSzTrSoPY9AE5bgGB9r169qEaNGjIzXrAVaI3ChxVpqEuUKCGVAyB7xYURKAwCTFwLgx5fywj4EAH4trZp00ZurX06YQCnb/Uh1npUjWCah8Z9QvuPnZfJCe67TwQscmEECokA8sAjs9J3330nH4qCpVy5ckXqz27ZsoXKlStH//3vf2UGJi6MQGERYOJaWAT5ekbARwggKxYWfkhfzX6lr49acVztuZ++pk7vHzF92YQy5txtsladoilDN1Gdqb2oZ2nfdil7UZrw2e5H2x+u5NuGdKz9s3U76PV530n3jsmTJ+tYM1cVrgggQx78poPpYQguTo8//jhB0qt69eqUnp5OlSoFz99xuN5rwTJuJq7BMlPcz7BDAL5gn332mUjl2poe79ncb+OXpHVTbcoaWYeUfBWCrC4iGiUJJBPXgiYi50wu9XhpntwW/emnn6RPHxdGoDAIYJt9zJgxdP/990v/UKOXX3/9lZ577jk6f/48NWnShNLS0qhUqVJG7zb3L4gQYOIaRJMVrF3FNhd0+w4ePCifvrFlVLFixWAdjt/6PXjwYNqxYwe993wvurthnJ/azaNlU2fT7t7JNMphHJj/iKufBqx7M62Hz6Jr+Tfpq6++org4f82b7sPweYXYSoY00rZt2+R6MHv2bLrjjjt83m6wNQCR/o4dO0ryl5WVZWjFioULF9Lbb79N8G3t3bu39G9lhY1gu+OM318mrsafo5DoofZHCv5O06dPpzvvtA+VCInB6jSIHj16UE5ODi2dOJCqVymnU62uqlGI65gYZ1v09sTVuVuBaOvQj5QwYZup0TiaaHYxUAnyMKr7pWhvu3JKuyeGUVqbKPne2lVAbbcl7Rm5mJQESFoXBvHfS3soeeRqk05nHL0xrjZlTjhLw81uDq7Grs/3/VMW0p7fz7KmawFw4r5Gwgak/QS5//DDD/lhtgC8kNhiw4YN0noJ7WyjFaiewKUB1mEQ1dGjR7OyhtEmKYT6w8Q1hCbT6EPB4gbBaeTgLlasmNz+6tOnj9G7HbD+NW/eXMmaM2s4RUbc6r9+qAQwoavGXUBt3pq4KqS1nNkH1srNQJLW8xayKv9PpnNNBHm7hsya2o0fp1h77YkrCKtKVm0JNvq1mA6Zia9avw259QOKz01fTlm/HKRp06ZRhw4d/NBicDWBYJ2RI0fS5cuXqV69enIHJjo6OrgG4efewscVxBABf/B1NVLBjhos57t376ayZcsSdKcbN25spC5yX0IMASauITahRh8OUmPOnDlT/lih8HaS8xlr3bq1TIu4Mf0pKlG8mJ+nViGC0rLZXWt91RJX5RwyEU3ZQUk+YeVsQkeE5TazpcWCau0fqxBL6++tyaoj4mrVFojwlxUUcq19ryJlRZT9B98LMzJp9ZYD9O6778otXi4WBOZnZNB/pkyRShmdOnWSAvvFixdniFwgoLoLFC1alFatWiUJohHKDz/8QK+88gqhf3Xr1pUWYXYDM8LMhHYfmLiG9vwadnSImMd2EohZw4YN5Y98+fJKKBAXBQGIdB89epSWv5VEsRUDZZEyEViz9dWeuCrb9toCK2pnKjLH4gKg/TZJEl3HvrTSYnu0pVQScOwqoFEz0JBV0lxnbstMolVFBP/cWY9O/Ix+3X+S5s+fL+9tLiR9HlU9T+DxzDPPyKhzLu4jgB0qbMX/85//pKeeesr9C31wZn5+Pv373/+mTz/9VNYOS3BqaqrcSePCCPgaASauvkaY63eKAMS1scUEPzf4vcISi6d2LgoCQ4YMkYEr88b2pYSaMYGDxYoAurC4mnvp2KJqGYRj4gqyml5NsdJ6TFytlBBESwGyuPZ4cR7lnM2lzMxMqlKlSuDmzSAtX7p0iZ5++mkZaAi1BWwlYzeBi2cI7N+/n/r27Su1XNesWRMwknjgwAHp8nX48GGKioqSBgjsnHFhBPyFABNXfyHN7ThE4OrVq/Tyyy/T+vXr5UIMuZfExERGSyAwadIkQpTu6IHt6O9dE/yECQjlNoobabFSWstjWfu4SnJ5QuMLC5K7ogylCYuprf+rdBWYepGGSJktBz6oNj6xnhDX8jb+sYpbgtYn1j/wnc/No87PzZUR4Linw72AbCEI6/Tp0xQbGyv1PFk5wPu7Qg3SwgP/E0884X1FXl65YMEC+s9//iN97xs1aiS1ivnhzEsw+TKvEWDi6jV0fKFeCCDjEHxekc8aBU/veIrH03w4FxAf/EA1FQkI5vg1AYHGv1VOQMEJCCTBXGGaKZuALueKA6rFtR/RBFUlQKs64ExVwLGrgHQysVIwEH2eWoHSpb+t/1wFFmftoH99/J1085g4cWI4375Sg/itt94ibCsjzSe2llnPs3C3xK5du2jAgAEycv/LL7+kqlWrFq5CN69G4BUepKHRCj9buCrA1QPvuTAC/kaAiau/Eef2nCLw888/S8IK4WosyFNEEEc4uw4g5Sukb+AfuHB8/xBL+epKL1aHPxRHAVs6VOusir/+UlK+Hsg5L4OOwnXnANJ3Y8eOJfixg9jA4vrYY49xMgad7j11Jwb+0/Cj9mWBpXzGjBkycxcKrOZ4GGEpQ1+iznW7QoCJqyuE+Hu/InDhwgV69dVXCdGqsCrA4vjoo4+G7Y8erHYIgOjUtDq980wPv86FbxvTm7hq3RDQc1t5LN+OBrVnbtxLo2etogoVKkj/1oiICN83arAWsrOz6YUXXqATJ07Q7bffLq2sCQn+cnMxGBg+6g5kxB544AFCWtV7771XCv7rXRA0+/HHH0vSqhb4KSclJVFkZKTezXF9jIBHCDBx9QguPtlfCGh9qaBnCisDfgjDrcD6jLz32G7NGNeP7owPlXzfehNXcWdYuQpYJzPw9X0Da2vvMRn0+6mL8sGrXz/hAhFGBTJ3SCKAAEtIXbVt21ZanW+77bYwQsF/Q927d68kkSCYDz30EL322mu6NA5rOfzqYcmFEQEFbi8jRowIy/VXF1C5Et0RYOKqO6RcoV4IILAD0atQHYBA+b/+9a+wFHRHFPacOXMovnJZWpDSn6IiWXJGr3tMr3qmfbaBPly2lSpXrmzOHqRX3Uav59y5c/TSSy8REgsgwHKkyFM/eNAgo3c76PsH1yqk0kaBJi7IK9RZvCnHjx+nzz//nGAwyMvLk1UgicCoUaNkEBYXRsBICDBxNdJscF/sEICfJyJXlyxZYn76x49k6dKlwwYtWFUGCSIAGZq2jeJo+oiewnWiSNiM3+gDXfHzPno5faX058QDRjhtjX/zzTfS5xGSV+yX7v87devWrdI1AzszeLjHdj58q91ZH/HAgQBQ+K9Cdk8tDRo0kFqxsJpzYQSMiAATVyPOCvfJDgHoFsKiAGsArAogr+EU/ILc7ogmRoaae1vUoElPdaNbi97Cd0qAEVj3yyF6YeYKEUD3p7w/sW0bDgWkB4L4GzdulMPt2rWrlLKDTisX/yKAuYBlVEs+YYGFVm6NGjUoPj5e+lvv27dPHv/73/8IhBfJTbTl7rvvlvcvruXCCBgZASauRp4d7psVAsiJPX78eBm4hQKLAP4fLikGIYUD7UYEZ8DyOuXpRIqMuJXvkgAh8OX6XTR+7jqCf2c4ZYJaunSpVPzAfRiOD5EBut1cNgtJQVjA4VrlTkGqXbgDIMALDx7uWGndqZfPYQR8jQATV18jzPXrjsDq1atl4AeiaqH1CmsDMsoUKRL62+ewmMCvDVuDtaqWl+Q1rnIZ3THmCp0jkH/zD3p7wQ+0+NtseVJKSgr16dMn5CE7deqUlLnavHmzHCvSfCJ5CBMeY009YgPgAgDXIlhV8QrfY9X6WqdOHSlnFc5Sg8aaMe6NpwgwcfUUMT7fEAgg+hUZXBBQgNKkSRMZvOUvQe5AgoBUuUOHDpXEHRbXlwe2pz4d6geyS2HT9r5jIhDpvRV08IQScQ25MkRdh3JBgpDFixfLvzf4W0PdA39rrVq1CuVh89gYAUbAoAgwcTXoxHC33EMAmVzgWwgyB6vCI488IjO6lCxZ0r0KgvQsSNWMGzeOvv/+ezmCxjUr0+jBHaheXMUgHZGxu33pynWa9dVm+nRdNt0U/qwxMTH0xhtvUNOmTY3d8UL2DhmTQFJ37Ngha4LM13NCNSDU/74KCRtfzggwAj5EgImrD8Hlqv2DwI0bN2TKWER0I8sU/O6effZZuv/++0M+JeHy5culxi2CtlASW9WiEf3aUEx51s/U4+6DW8CCNb/Rh19vocvXbsgq+/fvL2XaQjkQCQ9G77zzjkwrioKMSSDq8InkwggwAoxAIBFg4hpI9LltXRFAUAKyyMC/CwU+XYh8RgKDUC6QIkpPT5cZtkDcoTYAAjukR1OqGVs+lIfus7HlXs2nRet20Cert9O53GuyHZA2qFlALihUC+4fZEzCgyAUPEDOka4VYvcI5uHCCDACjECgEWDiGugZ4PZ1RwBC6LAOISgBBfIusJBVq1ZN97aMVCHcJWAly8rKMnerdYNqNKhrArVP+JuRumrYvuzLOUeLs7LpC6EYAGsrSlxcnNwev+eeewzbbz069t1330m1ADUqHcFXI0eO5IxJeoDLdTACjIBuCDBx1Q1KrshICCDtJIS109LSZAT+rbfeSr1795b+r1WqVDFSV3XvC6KKkX5z5cqVMv0mSnRUcerSvLo4alGr+lVZA1aD+n5BVtdsOUCrNu+nAznnzd8g6nrIkCFSLgjJBUK1HDt2TPqxbtiwQQ4R44b/dChblkN1LnlcjEA4IMDENRxmOYzHePXqVfrggw/k9ufNmzclgX3ggQekpFSlSpVCGhmkcZw7d67UdgQOaikZGUGdm8VTt5a1pR5sOJY9R8/SWkFW12w9YEVWgQWE2BHkd9ddd4U0NLCs4gEH9wcecCpUqCBz0vfq1SsspOVCenJ5cIxACCPAxDWEJ5eHZkEAGpQzZ86UwSYQjIcCwYMPPigtsKFOYOG3iLzmyD62du1amZ5TLSWKF6MmtWKoed1YalqnCjWIu50iioWedfHwyQu0be8J2ronhzbvPk4nz182YwBraosWLaRltUuXLiGvSwqLPHxYYZFHQTAjLMsIOgvlgDNeDxkBRiA0EGDiGhrzyKNwEwFYmaZPn05IYoACAgvxeASghDqBxXhBYjdt2iRJLA4ticX3IK0JNSpTM0Fim9aJle+DLTsXdEfhq7ptz3FBVI/Tlr05dO6SEmClFpDVli1bSrLauXPnkCerGHd2drYkrPBlRSlfvjwNGzZMpvmMjIx08y+IT2MEGAFGILAIMHENLP7ceoAQ2LNnjwxkUv360A1YnB599NGwSGKgwv7TTz9JKyysb0jh6ag0ql5JWmRjK0ZThdKlqEKZElSxdEmqVK5UgGZPaRbaqmcu5YnjKp29cJVOXbhCOw6eol8EWc3Ny3fYN2z/I71lOFhWVQCQIvmjjz6SDywosLA+JnYa+gnCykoBAb2FuXFGgBHwAgEmrl6AxpeEDgJIXzlt2jT67bffzIPq0KEDDRo0KOwyAx08eJAgOA9Sv3PnTmmhQ6akgkr50iWoctlSVFEctwsyW6FMFJUsEUFRxSOoRGQxKiEye5USgWGw2kZGFBWfF5NHJA7x2WVBMK/l3xTHH5R3A683Ke+aeJ9/g65dF/8Xn10VrxcuX6PT56/QaUFOT1/Mo5yzim5tQQVBRjjq169PSHOJ9+FiWYS28YoVK2jevHmENMEoyHiFnQW4yDBhdXX38PeMACNgVASYuBp1ZrhffkUArgOwSqkZgtB4fHy8tMAmJiaGDeGxBR0SWyCz8ItE9DkCvvB65swZt+cHW/e2pUiRIvKjgr5zpwFkcEKaXwjkQy0CcwaCWqtWrbAkZxcvXqSFCxfSokWLpJoGCuS8cB/37dvXHUj5HEaAEWAEDI0AE1dDTw93zt8IwMqYkZFBq1atkv6gKGXLlpWpLh9++GEZec1FQQD+widOnJBHTk6OJLVnz56l/Px8qeAA/1n8//r16x5BBmsgcI6OjpY+yDhKly4tiSkIKtKtqgc+50J06NAhmj9/Pn311VcEaytK69atafDgwdS2bVtWCeCbhBFgBEIGASauITOVPBA9ETh9+rS0Wi1evNgcwAQprfbt28tUsu3atZPSWlzsEQCRTUlJkUoGKNCQbVkvllrUrUq17ihPVcrdRjEVouV3J8SW/3ER4b/v93Mi2v8YbdqVY/ZPbdWqFaWmpkqSysUeATwQIMBu6dKlhKQbKCD5PXr0kJmuatasybAxAowAIxByCDBxDbkp5QHpiQDIQWZmptSBVTNxoX4EuPTs2VNqXtauXVvPJoO6LmiCTp48mXJzcylWkNMH2tejv3duRLcJ8upOgc/rJ2t/oy++3yX9WGF1ffnllyXWXBQE4I8NWTfcl6o+b5kyZWRw4YABA6RaABdGgBFgBEIVASauoTqzPC7dEfj111/lViwIA/K4q6VevXoyqQF8YcN56xqkdezYsRKWB9vVo1ED2rpNWG0nCwR2/Oy1IkHAQfnVxIkTw5q8XrhwgZYtW0ZLliyxeoCCSgLuPaQ1joiI0P2e5woZAUaAETAaAkxcjTYj3B/DIwArLCSkkFJWlRhCp7FNi3z2cCVo06ZNSKcJtZ0kLWmd+HgX6nl3XV3m8Zsfd9PYD9aEJXlFNqsff/xRWle//fZbs881gtGQvhhHOGgP63IjcSWMACMQMggwcQ2ZqeSBBAIBBCWBtIFc4L1asHULWS34xILERkVFBaJ7fmlz69atUsheWkZ1JK1q57Xkdfbs2dSsWTO/jCsQjeChCNrCIKpIFABLKwoC1pAsAWS1efPmHGwViMnhNhkBRsAQCDBxNcQ0cCeCHQHIOoHAwZUAYv7aSHpYYpFSFNZYkNlQs5I98cQT0vIM94Dxwzr7ZCrhNrB0/S6prYvsT6FUIGG1bt06eYC0qqoASL+K+wVuAAgGDOWHn1CaTx4LI8AI+BYBJq6+xZdrD0MEQFo3btxotpqpepoqFNAaBQEDmcURzH6xqosAlAOWv53ktU+rq9sEPq89XpwnFQfeffdd6tixo6tLDPs9/KN/+eUXSfahvACdXLUgGA0POCCr8F/lRAGGnUbuGCPACAQIASauAQKemw0PBP766y8ZBY5tX1jUoLdpW5DVCUS2ZcuW1LRpU4KofrAUaNuCePnCRcAWA9VlAMFwENkPlgJNW9wDKlFFkgtVIxhjgEIFiGrnzp3lPcAya8Eys9xPRoARCAQCTFwDgTq3GbYIQLR//fr1lJWVRdu2bSMQW20pWrQoNWjQQBIYWGMbN25s2Kxd0Gvt3r271GldP+MJv8xpu6ffl1ZXpDP1VN/18uXL9P3330uiPWrUKJ/1F3OKtLkqUYULidZ1BGln4aerPqzgweWWW27xWX+4YkaAEWAEQgkBJq6hNJs8lqBC4MqVKwSJLRBYkBtk7YJ1TltgfUPKTpAbCMqrB7JIqWlTAzVoWD0nTZrktm/ruZ++pk7vH7HubkJXyhpZh9xVHh2VtlxKZHkij4VAJ7g0IK0vCh4K3n//fV1gQ5awffv20d69eyVZxQFifO3aNXP9mMNGjRrJdnHgPfyeuTACjAAjwAh4jgATV88x4ysYAZ8hgAxIKpEFqdUSIG2jCNypVauWPKpXr25Oh4qUqKVKlfJZ/7QVw2qJzE3uuglI4rqpthVRVcls0rhkGhXvutuqu0CXLl1oypQpTi8Akfzkk09k/5AMQVvgRwqrtycFAVTHjh2TB9QjUD8I6uHDhx1WgwcN+KjCag73Dw6s8gRtPpcRYAQYAecIMHHlu4MRMDAC+/fvlxY9kCRY83bu3GlHxGy7j2CvO+64g0BicUD3ExbasmXLEmS69FI1UNUEZr/yIDWrHesSRUfEVV506EdKmECUMeduutNFLVv35tCwSUsdWk3hugBfYmQ5A8FUCxQfYJ1WX/H59u3brVpCil+QUwTSaQnq0aNHJTl19gCBSkBS69atS/C9xXtkUvPXw4NL0PkERoARYARCDAEmriE2oTyc0EcABE27LQ1idfCgkmHK3QKLLUgsAoPwClILwotXBIfBQqge+D/O136O//fo0UNaH1cINYEYkd7VVXFKXCmPlk2dTZkth1Fam4L1bk+INLDdhboAyDgyScFvde7cudKCCky0ZFV978ilomHDhlIjFURVmwXN2RgwXrRZrVo1ecDKDYJav359V8Pm7xkBRoARYAR0RICJq45gclWMQKAQgDURJAxEEsfx48flq2o9PHnyJCETk14Fvq2vvPKKrG77nGS3qnVOXImyF6VRejXXxBUNJQxNk+316dNHpkDVFuCA4qn/b/ny5SVpxyus1LBQg6iq1mp8zoURYAQYAUYg8AgwcQ38HHAPGAG/IICt8EuXLsktcdv36mewYEIAH0FHCBTDgf9rX7FtbgTiCuKM1LvatLsAUiWvKqiOSOz8+fOlpRlWZvi8cmEEGAFGgBEIDgSYuAbHPHEvGQHDIaC3q8Du3q4DtFRXAVhFly9fLjEB2YYqA9QDQGK1qXdtiaxKYm19XA0HLneIEWAEGAFGwCECTFz5xmAEGAGvEDBacJY6CET8IzOVI2useg4TV6+mnC9iBBgBRiDgCDBxDfgUcAcYgeBEQD85LKKJU3tRz9KucXBXDkutyZk1dvbs2TIJABdGgBFgBBiB4EKAiWtwzRf3lhEwDAJqAoIuzarTlOQeLvsVqAQE2o6p1lhoq0IVgAsjwAgwAoxAcCHAxDW45ot7ywgYBgFLytcIkfL1Sb/0qzApX/3SQW6EEWAEGAFGwKcIMHH1KbxcOSMQ2gg8/PDDMjmCu9mzCoOG6iYAsf9FixYVpiq+lhFgBBgBRiBIEWDiGqQTx91mBIyAwDfffENjx46l6KjitFwkIrhNvPqiXM7Lpx4i8UCueH333XepY8eOvmiG62QEGAFGgBEwOAJMXA0+Qdw9RsDoCKjqAn3a16OUoZ190t3xs9fS0vW7HKZ69UmDXCkjwAgwAoyAIRFg4mrIaeFOMQLBgwA0VIcNGyY77AuXAdVFAPUvXryYg6qC59bgnjICjAAjoDsCTFx1h5QrZATCDwHVZUBv8qolrRMnTqSePXuGH7g8YkaAEWAEGAEzAkxc+WZgBBgBXRDQkteuzWsIt4FOXvu8wqc1dU4Wrd5yQLHkMmnVZY64EkaAEWAEgh0BJq7BPoPcf0bAQAiAvE6ePJlyc3NlwFZS9yY0oHNDtwksCOvCtTto3optMhArOjqaXn/9dQ7GMtAcc1cYAUaAEQgkAkxcA4k+t80IhCAC0HcdN24cbdq0SY4uukQEtWpQjZrXqUK176hIMeVKUUyFaPndibO5dOL8Fdr7+xnasuc4/bzzKOVeuyG/a9myJU2YMIFiYmJCECUeEiPACDACjIA3CDBx9QY1voYRYARcIoCgrfT0dDOBdXmB6QQQ1uHDh3NKVncB4/MYAUaAEQgjBJi4htFk81AZgUAgAAssSOyWLVsoJyfHfKAvsbGx5qN58+aSrLKFNRCzxG0yAowAIxAcCDBxDY554l4yAowAI8AIMAKMACMQ9gj8P7P+rv6Z1fu3AAAAAElFTkSuQmCC)

#### 2.1.2 僵尸进程

* 定义

  > 子进程先于父进程退出，父进程又没有处理子进程的退出状态，此时子进程就会成为僵尸进程。

* 危害

  >  僵尸进程虽然结束，但是会存留部分进程资源在内存中，大量的僵尸进程会浪费系统资源。

* 处理方法

  > 1. 父进程帮儿子收尸`p.join()`
  > 2. 操作系统自动帮忙收尸
  > 3. 生二胎时候再收尸，父进程再次调用`p2.start()`

#### 2.1.3 孤儿进程

* 定义

  >  父进程先于子进程退出时，子进程会成为孤儿进程

* 特点

  >  孤儿进程会被系统自动收养，成为孤儿进程新的父进程，并在孤儿进程退出时释放其资源

#### 2.1.3 进程通信

* 必要性：

> 进程间空间互相独立、资源不共享，因此必须使用通信完成资源共享

* 方法：

> 1. 消息队列
> 2. 套接字（如创建UDP套接字 socket自己向自己发送消息）

#### 2.1.4 关键方法

* 创建进程对象

```python
import from multiprocessing import Process

p = Process(target=目标函数名, 
            args=位置参数元祖, 
            kwargs=关键字参数字典, 
            daemon=False)	# daemon表示子进程是否随父进程退出 默认False
```

* *设置父进程等待子进程退出

```python
p.join(timeout=-1)			# timeout表示最长等待秒数
```

* 启动进程

```python
p.start()					# 此时进程才真正被创建并启动
```

* *获取进程PID

```python
os.getpid()					# 获取当前进程pid
os.getppid()				# 获取父进程pid
```

* *退出进程

```python
sys.exit(info)
```

* *使用消息队列

```python
from multiprocessing import Queue

# 注意：消息队列q也要作为参数传给Process()中
q = Queue(maxsize=0)		# 创建消息队列对象
q.put(数据)				   # 数据入队
q.get()						# 数据出队
q.full()					# 是否满队
q.empty()					# 是否空队
q.qsize()					# 队列长度
q.close()					# 关闭队列
```

* *使用套接字

```python
# 略
```

#### 2.1.5 代码示例

* 思路：

  > 1. 将需要多进程事件封装成函数或类
  > 2. 创建进程对象并启动进程

* 注意事项：
  * 新的进程是原来进程的子进程
  * 子进程完全复制父进程全部内存空间，并相互独立，且只执行自
  * 子进程无法使用标准输入（input函数）

* 类式启动

```python
from multiprocess import Process

MyProcess(Process):
    
    def __init__(self)
    	supper().__init__()
    
    def run(self):
        # ...进程执行语句
        
if __name__ == "__main__":
    p = Process()
    p.start()								# 创建并启动进程
```

* 函数式启动：

```python
# 略
```

### 2.2 线程

#### 2.2.1 基础概念

* 线程也被成为轻量级进程，属于多任务变成方式
* 线程同样可以利用计算机的多核资源，但是不能同时在多个核心上工作
* 但凡创建了一个线程，就成为了多线程，对应主线程和分支线程
* 一个进程中可以包含多个线程，多个线程资源共享，一般需要同步锁和互斥锁
* 线程的开销远小于进程开销
* 一个进程下多个线程处于资源竞争的局面（如果一个线程死循环，其他线程就抢不到资源而严重影响效率）

#### 2.2.2 同步互斥

* 原因：

> 1. 线程由于共享进程资源，所以直接使用全局变量通信，于是乎产生线程的同步和互斥两大问题
> 2. 对共享资源的无序操作会引起数据混乱，对同步互斥错误操作可能会引起死锁
> 3. <font color="orange">某些场合下，使用生成器对象可以代替互斥锁，大大提高效率</font>

* 同步：

> 各个线程操作共享资源时按一定顺序执行（先打开浏览器才能访问到网页）

* 互斥：

> 同一时刻各个线程只能有一个线程拥有共享资源访问权限（小王在使用打印机时小李无法使用，直至小王打印好了）

#### 2.2.3 关键方法 

* Thead对象

```python
# ...同Process
```

* Event对象

```python
from threading import Event

e = Event()
```

* 开启阻塞

```python
e.wait(timout=-1)		# 阻塞直到另一个线程调用 e.set()
```

* 结束阻塞

```python
e.set()					# 结束一个线程的阻塞状态
```

* 恢复成可阻塞状态

```python
e.clear()				# e.wiat()为阻塞的前提是e.clear()
```

* 查看是否可阻塞

```python
e.is_set()		
```

* Lock对象

```python
from threading import Lock

lock = Lock()
```

* 上锁

```python
lock.acquire()			# 一个线程上了锁，另一个再次上锁便会处于阻塞状态
```

* 解锁

```python
lock.release()			# 一个线程上锁并执行完操作后调用解锁，另一个线程的lock.acquire()便不再阻塞
```

#### 2.2.4 线程死锁

多个线程由于竞争资源或由于彼此通信而造成的阻塞现象，且此时若无外力作用，将持续阻塞

* 产生条件（必须全部同时成立）

> 1. 互斥（线程使用了互斥方法，使用一个资源时，其它线程无法使用）
> 2. 请求和保持条件（线程至少保持了一个资源，但是又提出新的资源请求，获取到新的资源前不会释放保持了的资源）
> 3. 不剥夺条件（不会受到线程外部干扰，如系统强制终止线程等）
> 4. 环路等待（产生等待资源的环形链）

* 如何避免

  > 1. 逻辑清晰，避免同时出现四大死锁条件
  > 2. 测试工程师检测

#### 2.2.5 GIL

* 基本概念

> 1. 一个进程（解释器）对应一把GIL大锁
> 2. 一个进程同以时刻只有一个线程能被执行
> 3. 若线程处于阻塞状态，便会释放GIL，让解释器转而执行其它线程
> 4. 只要有一个线程处于计算密集型，多线程便不能提高效率（从多线程资源竞争角度思考）

* 启示

> 1. 使用多进程完成计算密集型任务
> 2. 特殊情况考虑出用JPython等非CPython解释器

#### 2.2.6 代码示例

* 互斥锁

```python
from threading import Thread, Lock

""" 创建2个分支线程，一个打印[1, 52]这52个数字，另一个打印[A, Z]这26个字母
    要求：打印出来的顺序是12A34B...5152Z
"""


def display_num():
    # global lock01, lock02
    for i in range(1, 53):
        if i > 2 and i % 2 == 1: lock02.acquire()   # 3, 5, 7...
        print(i, end="")
        if i % 2 == 0: lock01.release()     # 2, 4, 6...


def display_letter():
    # global lock01, lock02
    for i in range(ord("A"), ord("Z")+1):
        lock01.acquire()
        print(chr(i), end="")
        lock02.release()


def main():
    thread01, thread02 = Thread(target=display_num), Thread(target=display_letter)
    lock01.acquire()
    lock02.acquire()
    thread01.start()
    thread02.start()
    thread01.join()
    thread02.join()
    ...


# --------------------------------test---------------------------------------
if __name__ == "__main__":
    lock01, lock02 = Lock(), Lock() 
    main()
    ...

```

## 3. 网络并发

### 3.1 网络循环模型

* 最古老的网络服务模型，一台服务器同一时刻只能处理一个客户端请求
* 优点是结构简单，消耗资源少
* 适用于低频请求场合

### 3.2 多线程/进程并发模型

* 优点：能同时满足多个客户端请求
* 缺点：消耗资源较大
* 应用：客户端请求复杂，需要长时间占有服务器
* 思路：

```yacas
1. 创建网络套接字
2. 阻塞等待客户端连接
3. 为每一个连接进来的客户端创建进程/线程，来处理请求
4. 主进程继续等待新的客户端连接
5. 当客户端退出连接时，销毁对应进程/线程
```

### 3.3 代码示例

* 多线程网络并发（服务端）

```python
from threading import *
from socket import *

""" 面向对象完成多线程网络并发服务端
"""


# ------------------------------------------------------------------------------------
# TCP网络服务类
# ------------------------------------------------------------------------------------


class TcpServer:
    client_dict = {}  # 存储用户套接字和地址信息

    def __init__(self, host, port):
        self.__host = host
        self.__port = port
        self.__socket = self.__create_socket()

    def __create_socket(self):
        socket_ = socket()
        socket_.bind((self.__host, self.__port))
        return socket_

    def start_server(self):
        self.__socket.listen(5)
        while 1:
            client_socket, client_address = self.__socket.accept()  # 阻塞等待客户端连接
            TcpServer.client_dict[client_socket] = client_address  # 添加客户到字典中去
            print(f"{TcpServer.client_dict=}")
            print(f"客户端{client_address}已连接")
            thread_ = MyThread(client_socket, client_address)  # 创建MyThread对象
            thread_.start()  # 调用MyThread的run方法


# ------------------------------------------------------------------------------------
# 自定义线程类
# ------------------------------------------------------------------------------------
class MyThread(Thread):
    client_dict = TcpServer.client_dict

    def __init__(self, client_socket, client_address):
        self.__client_socket = client_socket
        self.__client_address = client_address
        super().__init__()

    def run(self, ):
        while msg := self.__client_socket.recv(1024):  # 客户端执行socket.close()时，会返回一个空字符串
            print(f"客户端{self.__client_address}:{msg}")
            self.__client_socket.send(b"test")
        print(f"客户端{self.__client_address}已退出")
        self.__client_socket.close()
        del MyThread.client_dict[self.__client_socket]


# --------------------------------test---------------------------------------
if __name__ == "__main__":
    server_socket = TcpServer("0.0.0.0", 9999)
    server_socket.start_server()
```

### 3.3 IO并发模型

* IO操作：执行读或写的操作均为IO操作
* 程序分类：IO密集型、计算密集型
* IO分类：<font color="orange">阻塞IO、非阻塞IO、IO多路复用</font>

#### 3.3.1 阻塞IO

* 定义：不满足条件时保持阻塞（默认阻塞行为）
* 例如：input()、accpt()、recv()

#### 3.3.2 非阻塞IO

* 定义：手动修改默认阻塞为非阻塞IO
* 方法：`sock.setblocking(False)`或`sock.setimeout(3)`
* 注意：当设置超时且达到设定时间，此时还没有客户端连接进来，便会引发BlockingIOError（记得try）

#### 3.3.3 IO多路复用

* 定义：同时监控多个IO状态，哪一个IO就绪就执行哪个IO事件，避免了一个IO一直阻塞
* 方法：select、epoll
* select:

```yacas
函数：rs, ws, xs = select(rlist, wlist, xlist)
功能：监控IO事件，阻塞等待IO发生
参数：
	rlist: 可读IO列表，添加等待发生或者可读的IO事件
	wlist: 可写IO列表，添加主动处理或者可写的IO事件
	xlist: 异常IO列表，添加若出现异常要处理的IO事件
返回值：
	rs: rlist中就绪的IO
	ws: wlist中就绪的IO
	xs: xlist中就绪的IO
```

* epoll

  > 1. epoll比select效率高
  > 2. epoll同时监控数量更多
  > 3. epool支持出边缘处罚（EPOLLET）

```python
# 创建epoll对象
ep = select.epoll()
```

```python
# 注册IO事件
ep.register(IO对象, event)  # event: 事件类型，EPOLLIN（读IO）、EPOLLOUT（写IO）、EPOLLERR（异常IO）
# 举例
ep.register(sockfd, EPOLLIN|EPOLLEET)
ep.register(sockfd, EPOLLIN|EPOLLERR)
```

```python
# 取消注册IO事件
ep.unregister(fd)			# fd指的是文件描述符号,为整数 IO对象.filno()可获取到fd
```

```python
# 阻塞等待IO事件发生
events = ep.poll()			# events: [(fd, event), ...]
# 每个元祖为一个就绪的IO，元祖第一项为IO的fd文件描述符，第二项为该IO就绪的事件类型
```

#### 3.3.4 代码示例

* 基于select的IO网络并发（服务端）

```python
from socket import *
from select import *

""" 基于select(多路复用)的网络并发"""


class TcpUdpServer:

    def __init__(self, address, family, type_):
        self.__socket = socket(family, type_)
        self.__socket.bind(address)

    def start_tcp_server(self):
        self.__socket.listen(5)
        # 设置成非阻塞,select仍会阻塞,但是到达accept或recv时必然不会阻塞,除非网络拥塞
        self.__socket.setblocking(False)
        # 初始化监听可读、监听可写、监听异常列表
        rs, ws, xs = [self.__socket], [], []
        # 基于IO网络并发的主循环中 不可以有除select以外阻塞
        while 1:
            # 阻塞等待 直到IO列表中非阻塞IO出现
            ready_rs, ready_ws, ready_xs = select(rs, ws, xs)   # 阻塞等待 -->多路复用
            for item in ready_rs:                               # 遍历可读列表
                # 如果有客户端连接进来，self.__socket变成等待发生状态
                if item is self.__socket:
                    connect_socket, connect_address = self.__socket.accept()
                    # 将连接套接字也添加进等待发生列表中
                    rs.append(connect_socket)
                    print(f"用户{connect_socket}已连接")
                # 如果客户端有消息进来(分2种情形)
                else:
                    data = item.recv(1024)
                    # 如果发来空字符串，说明用户退出连接了
                    if not data:
                        print(f"用户{item}已退出连接")
                        rs.remove(item)
                        item.close()
                    # 否则是用户正常发消息
                    else:
                        print(f"用户{item}发来消息:{data.decode()}")
                        # 添加进主动处理监听列表 下一个循环处理消息
                        ws.append(item)

            # ！某时刻某一用户端只能在rs/ws中一个,且出现在ws中后马上会去处理,处理完再从ws中移除
            for item in ready_ws:                               # 遍历可写列表
                # 回应用户进行交互操作
                item.send(b"Thanks")
                ...
                # 处理完用户请求后将用户从监听可写队列中移除，直到用户再次发来消息
                ws.remove(item)


# --------------------------------test---------------------------------------
if __name__ == "__main__":
    tcp01 = TcpUdpServer(("0.0.0.0", 8888), AF_INET, SOCK_STREAM)
    tcp01.start_tcp_server()
```

* 基于epoll的IO网络并发

```python
from socket import *
from select import *


""" INTRODUCTION"""


class TCPServer:

    def __init__(self, host_address, ):
        self.__host_address = host_address
        self.__socket = socket()
        self.__socket.bind(self.__host_address)
        self.__socket.setblocking(False)
        self.__ep = epoll()
        self.__map = {}     # {fd: sock}

    def __register(self, target, event):
        # 每次注册监听事件要同时放到字典中去，保持数据一致
        self.__map[target.fileno()] = target
        self.__ep.register(target, event)

    def __unregister(self, fd, ):
        # 每次解除注册事件也要同时将时间从字典中移除，保持数据一致
        del self.__map[fd]
        self.__ep.unregister(fd)

    def start_tcp_server(self):
        self.__socket.listen(5)
        # 设置成边缘出发(|EPOLLET) -->IO事件未处理时，等待下一次IO事件再一起提醒 而不是一只提醒
        self.__register(target=self.__socket, event=EPOLLIN | EPOLLET)
        while 1:
            # 开始关注IO事件,下面是一个阻塞函数
            events = self.__ep.poll()                                   # [(fd, event), ]
            # 遍历就绪的IO文件描述符 和事件类型
            for fd, event in events:
                # 根据fd中实现建立的字典中找到fd对应的套接字对象
                sock = self.__map[fd]
                # 如果是用户连接进来
                if sock == self.__socket:
                    conn_sock, conn_addr = sock.accept()
                    conn_sock.setblocking(False)
                    print(f"用户{conn_sock}已连接")
                    # 将连接套接字注册到进去 事件类型为EPOLLIN
                    self.__register(conn_sock, EPOLLIN)
                # 如果用户端发来消息(有2种情形)
                elif event == EPOLLIN:
                    data = sock.recv(1024)
                    if not data:                                        # 如果用户退出连接
                        print(f"客户{sock}已退出连接")
                        self.__unregister(fd, )
                        sock.close()
                    else:                                                # 如果用户真的发来消息
                        print(f"用户{sock}发来消息: {data.decode()}")
                        # 这里可以直接处理回复用户，也可以先注册，然后一起处理
                        # 修改监听类型必须先取消注册，否则引发重复发注册错误
                        # epoll基于系统托管,系统帮忙保存,select基于重复提交,系统不保存
                        self.__unregister(fd)
                        self.__register(sock, event=EPOLLOUT)
                # 逮到要回复用户套接字
                else:   # event == EPOLLOUT
                    # 其它交互操作，也可以创建子进程、分支线程等
                    sock.send(b"Thanks")
                    self.__unregister(fd)
                    self.__register(sock, event=EPOLLIN)


if __name__ == "__main__":
    tcp01 = TCPServer(("0.0.0.0", 8888))
    tcp01.start_tcp_server()
```

## 4. Web服务

### 4.1 HTTP

属于应用层的协议，用来网页获取、数据传输，使用TCP传输服务

* 网页访问流程

> 1. 浏览器（客户端）使用TCP发送HTTP请求给服务端
> 2. 服务端收到HTTP请求后进行解析，组织响应内容，以HTTP响应格式将数据回复给浏览器
> 3. 浏览器接收到响应内容，解析展示给用户

* HTTP请求

```yacas
GET		/		HTTP/1.1 	# 请求行
							# 空行
Accept-Encoding: gzip		# 请求头
...

							# 空行
name=qtx&age=999			# 请求体
```

* HTTP响应

```yacas
HTTP/1.1	200		OK		# 状态行
DATE:...					# 报头
Content-Type:...			
							# 空行
...							# 响应正文	
```

### 4.2 代码示例

```python
from socket import *
from multiprocessing import *


""" HTTP

    超文本传输协议（英语：HyperText Transfer Protocol，缩写：HTTP）
    是一种用于分布式、协作式和超媒体信息系统的应用层协议，是因特网上应用最为广泛的一种网络传输协议，
    所有的 WWW 文件都必须遵守这个标准。
    HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件, 图片文件, 查询结果等）。
"""


class TCPServer:

    def __init__(self, family, type_, host_address):
        self.__socket = socket(family, type_)
        self.__socket.bind(host_address)

    def start_tcp_server(self):
        self.__socket.listen(5)
        while 1:
            # 一旦被访问就发送一张图片回去,不必要先接收,因为是TCP
            conn_socket, conn_address = self.__socket.accept()
            t = Process(target=TCPServer.send_image, args=(conn_socket, conn_address), daemon=True)
            t.start()

    @staticmethod
    def send_image(sock, address):
        head = """HTTP/1.1 200 OK\r\nContent-Type:image/jpeg\r\n\r\n"""
        # HTTP响应
        sock.send(head.encode())
        with open("./1.jpeg", "rb") as f:
            while data := f.read(1024):
                sock.send(data)
        # [可选]发送完毕主动断开连接
        sock.close()
        ...


if __name__ == "__main__":
    tcp01 = TCPServer(AF_INET, SOCK_STREAM, ("0.0.0.0", 8888))
    tcp01.start_tcp_server()
```

## 5 高并发技术

* QPS：每秒查询率，是一台服务器每秒能够相应的查询次数
* TPS：每秒事务数，一个事务是指一个客户机向服务器发送请求然后服务器做出反应的过程
* 区别：客户端请求一个网页，就是一个T，这个请求网页中包含了10次请求，就是10个Q
* 并发数：系统同时处理的请求/事务数
* 并发用户数：现实系统中操作业务的用户，在性能测试工具中称为虚拟用户数
* RT：响应时间，一般取平均响应时间
* 关系：QPS（TPS）= 并发数/平均响应时间
* PV：日均页面浏览量
* 关系：QPS = PV * 0.8 / 86400 * 0.2（考虑峰值并采用二八原则）

多进程+多线程

多进程+IO并发

...
