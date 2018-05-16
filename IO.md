# IO

## 事件驱动模型
>- 事件驱动编程是一种编程范式，这里程序的执行流由外部事件来决定。它的特点是包含一个事件循环，当外部事件发生时使用回调机制来触发相应的处理。

>- 注意，事件驱动的监听事件是由操作系统调用的cpu来完成的
### 驱动模型大体思路如下：

>1. 有一个事件（消息）队列；
>2. 鼠标按下时，往这个队列中增加一个点击事件（消息）；
>3. 有个循环，不断从队列取出事件，根据不同的事件，调用不同的函数，如onClick()、onKeyDown()等；
>4. 事件（消息）一般都各自保存各自的处理函数指针，这样，每个消息都有独立的处理函数；
### 服务器处理模型的程序时，有以下几种模型：
>1. 每收到一个请求，创建一个新的进程，来处理该请求；
>2. 每收到一个请求，创建一个新的线程，来处理该请求；
>3. 每收到一个请求，放入一个事件列表，让主进程通过非阻塞I/O方式来处理请求
## 阻塞IO
>- 在linux中，默认情况下所有的socket都是blocking，一个典型的读操作流程大概是这样：

> ![image](http://img.my.csdn.net/uploads/201007/31/0_1280550787I2K8.gif)

>> 当用户进程调用了recvfrom这个系统调用，kernel就开始了IO的第一个阶段：准备数据。对于network io来说，很多时候数据在一开始还没有到达（比如，还没有收到一个完整的UDP包），这个时候kernel就要等待足够的数据到来。而在用户进程这边，整个进程会被阻塞。当kernel一直等到数据准备好了，它就会将数据从kernel中拷贝到用户内存，然后kernel返回结果，用户进程才解除block的状态，重新运行起来。
所以，blocking IO的特点就是在IO执行的两个阶段都被block了。
```
import socket
sk = socket.socket()
sk.bind(("127.0.0.1", 8080))
sk.listen(5)
while True:
    conn, addr = sk.accept() # 发送系统调用，等待有客户端连接
    while True:
        sk.send('hello client'.encode('utf8'))
        data = conn.recvf(1024)
        print(data)
```

## 非阻塞IO
>- 非阻塞IO原理：
>>- 主进程执行轮训，不断的发送系统调用查看是否有客户端连接，没有则先执行其他操作，执行完毕再次轮训，直至有客户端连接
```
from socket import socket
import time
sk = socket(socket.AF_INET, socket.SOCK_STREAM)
sk.bind(("127.0.0.1", 8080))
sk.listen(5)
sk.setblocking(False) # 设置非堵塞IO
while True:
    try:
        client_socket, addtess = sk.accept()
        client_socket.send("Hello client")
        data = client_socket.recv(1024)
        print(data)
        client_socket.close()
    except Exception as e:
        print(e)
        time.sleep(0.5)
```
## IO多路复用
>- IO多路复用，就是利用select监听所有的socket（内部原理是，不断地轮训，查看是否有客户端连接）一旦有客户端连接，则返回客户端socket继续执行
>- 优势：可以监听多个socket
### select
```
from socket import socket
import select

sk = socket()
sk.bind(("127.0.0.1", 8080))
sk.listen(5)
sk.setblocking(False) # 设置非堵塞IO
socket_list = [sk,]
while True:
    r, w, e = select.select(socket_list, [], [], 5)  # input,output, erroput
    for i in r:
        if i == sk:
            conn, addr = i.accept()
            socket_list.append(conn)
        else:
            data = i.recv(1024)
            print(str(data, 'utf8'))
```
### selectors IO多路复用模块


## 异步IO
### 自定义异步IO
```

```
### asycio
```
import asyncio


@asyncio.coroutine
def task():
    yield from asyncio.sleep(5)


task = [task(), task()] # 执行两个任务

loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.gather(*task))
loop.close()
```