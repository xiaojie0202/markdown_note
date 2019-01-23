# rabbitMQ
    RabbitMQ采用Erlang语言开发。Erlang语言由Ericson设计，专门为开发concurrent和distribution系统的一种语言，在电信领域使用广泛。OTP（Open Telecom Platform）作为Erlang语言的一部分，包含了很多基于Erlang开发的中间件／库／工具，如mnesia／SASL，极大方便了Erlang应用的开发。OTP就类似于Python语言中众多的module，用户借助这些module可以很方便的开发应用。
# AMQP
![img](http://img.blog.csdn.net/20160310091724939)
>- Broker: 接收和分发消息的应用，RabbitMQ Server就是Message Broker。
>- Virtual host: 出于多租户和安全因素设计的，把AMQP的基本组件划分到一个虚拟的分组中，类似于网络中的namespace概念。当多个不同的用户使用同一个RabbitMQ server提供的服务时，可以划分出多个vhost，每个用户在自己的vhost创建exchange／queue等。
>- Connection: publisher／consumer和broker之间的TCP连接。断开连接的操作只会在client端进行，Broker不会断开连接，除非出现网络故障或broker服务出现问题。
>- Channel: 如果每一次访问RabbitMQ都建立一个Connection，在消息量大的时候建立TCP Connection的开销将是巨大的，效率也较低。Channel是在connection内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread创建单独的channel进行通讯，AMQP method包含了channel id帮助客户端和message broker识别channel，所以channel之间是完全隔离的。Channel作为轻量级的Connection极大减少了操作系统建立TCP connection的开销。
>- Exchange: message到达broker的第一站，根据分发规则，匹配查询表中的routing key，分发消息到queue中去。常用的类型有：direct (point-to-point), topic (publish-subscribe) and fanout (multicast)。
>- Queue: 消息最终被送到这里等待consumer取走。一个message可以被同时拷贝到多个queue中。
>- Binding: exchange和queue之间的虚拟连接，binding中可以包含routing key。Binding信息被保存到exchange中的查询表中，用于message的分发依据。
# rabbitMQ 安装
>- 添加源
>>- echo 'deb http://www.rabbitmq.com/debian/ testing main' | sudo tee /etc/apt/sources.list.d/rabbitmq.list
>- 新增公钥
>>- wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
>- 更新源 
>>- sudo apt-get update
>- 安装rabbitMQ
>>- sudo apt-get install rabbitmq-server
>- 启动rabbitMQ
>>- service rabbitmq-server start
>- 查看rabbitmq进程
>>- invoke-rc.d rabbitmq-server stop/start/etc
>- 打开管理页面 
>>- sudo rabbitmq-plugins enable rabbitmq_management
>- 查看安装的插件 
>>- sudo rabbitmqctl list_users
>- 查看用户 
>>- sudo rabbitmqctl list_users
>- 新增管理员用户 
>>- sudo rabbitmqctl add_user admin admin 
>>- sudo rabbitmqctl set_user_tags admin administrator
>- 授权用户远程连接
>>- rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*" 
>- 用刚设置的账户登录管理页面 
>>- http://127.0.0.1:15672
>- 查看当前所有的队列
>>- rabbitmqctl list_queues
>- 查看当前所有的交换机
>>- rabbitmqctl list_exchanges 
>- 查看当前所有的绑定
>>- rabbitmqctl list_bindings 
>- 查看所有的tcp链接
>>- rabbitmqctl list_connections
>- 查看所有的信道
>>- rabbitmqctl list_channels
>- 关闭应用
>>- rabbitmqctl stop_app 
>- 打开应用
>>- rabbitmqctl start_app
>- 清空队列
>>- rabbitmqctl reset
>- 查看状态信息
```
rabbitmqctl status

# 得到如下的信息

[{pid,3086},   # rabbitmq服务运行的进程ID
 {running_applications,
     [{rabbit,"RabbitMQ","3.6.10"},
      {ranch,"Socket acceptor pool for TCP protocols.","1.3.0"},
      {ssl,"Erlang/OTP SSL application","8.2"},
      {public_key,"Public key infrastructure","1.4.1"},
      {asn1,"The Erlang ASN1 compiler version 5.0","5.0"},
      {os_mon,"CPO  CXC 138 46","2.4.2"},
      {rabbit_common,
          "Modules shared by rabbitmq-server and rabbitmq-erlang-client",
          "3.6.10"},
      {syntax_tools,"Syntax tools","2.1.2"},
      {xmerl,"XML parser","1.3.15"},
      {crypto,"CRYPTO","4.0"},
      {mnesia,"MNESIA  CXC 138 12","4.15"},
      {compiler,"ERTS  CXC 138 10","7.1"},
      {sasl,"SASL  CXC 138 11","3.0.4"},
      {stdlib,"ERTS  CXC 138 10","3.4"},
      {kernel,"ERTS  CXC 138 10","5.3"}]},
 {os,{unix,linux}},
 {erlang_version,
     "Erlang/OTP 20 [erts-9.0] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:64] [hipe] [kernel-poll:true]\n"},
 {memory,  # 内存
     [{total,56444960},  # 总共消耗的内存，单位字节
      {connection_readers,0},  # tcp订阅连接消耗
      {connection_writers,0},  # tcp发布者连接消耗
      {connection_channels,0}, # 信道消耗
      {connection_other,0},    # 其他消耗
      {queue_procs,2840},      # 队列进程消耗
      {queue_slave_procs,0},   # 队列子进程消耗
      {plugins,0},             # 插件消耗
      {other_proc,22726336},   # 其他进程消耗
      {mnesia,60944},          
      {metrics,184544},
      {mgmt_db,0},
      {msg_index,45048},       # 消息标记
      {other_ets,1695784},
      {binary,94472},         # 一些被引用的数据
      {code,21374813},        # 代码
      {atom,891849},
      {other_system,9549962}]},
 {alarms,[]},
 {listeners,[{clustering,25672,"::"},{amqp,5672,"::"}]},  # mq监听端口5672，erlang端口25672
 {vm_memory_high_watermark,0.4},  # 开启流控的内存阀值
 {vm_memory_limit,6568098201},    # 消息持久化阀值
 {disk_free_limit,50000000},      # 磁盘开启流控阀值
 {disk_free,48187891712},         # 磁盘空闲量
 {file_descriptors,  # 文件句柄
     [{total_limit,924},  # 文件句柄上限
     {total_used,2},      # 已使用
     {sockets_limit,829}, # 允许的TCP连接上限
     {sockets_used,0}]},  # 已使用连接数
 {processes,       
    [{limit,1048576},     # 允许的最大进程数
    {used,156}]},         # 已使用进程数
 {run_queue,0},           # 运行的队列数
 {uptime,97},             
 {kernel,{net_ticktime,60}}]

```
# Python简单操作rabbitMQ
    python操作RabbitMQ需要安装pika模块   pip install pika
## 一对一发送消息(工作队列)
    每个任务 交付给一个工人（消息接收者，或者消费者）
![img](http://www.rabbitmq.com/img/tutorials/python-two.png)
### 发送消息
```python
import pika
#封装RabbitMQ的认证信息
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie', erase_on_connect=False)  
# 连接RabbitMQ
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', credentials=credentials))
# 建立RabbitMQ协议通道
channel = connection.channel()  
# 声明queue(队列)
channel.queue_declare(queue='hello')  
# 发送消息到指定队列(queue)
channel.basic_publish(exchange='', routing_key='hello', body='Hello World!')
# 关闭通道
connection.close()
```
### 接受消息
```python
import pika
#封装RabbitMQ的认证信息
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie', erase_on_connect=False)  
# 连接RabbitMQ
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', credentials=credentials))
# 建立RabbitMQ协议通道
channel = connection.channel()  
# 声明queue(队列)
channel.queue_declare(queue='hello')  

# 当消息队列中有消息的时候执行此函数
def callback(ch, method, properties, body):
    # ch：  通道（channel）的实例
    # method： 发送端发送消息附带的参数
    # proprties: 属性
    # body: 发送端发送过来的数据 
    print(" [x] Received %r" % ch, method, properties, body)

# 把队列跟回调函数绑定
channel.basic_consume(callback, queue='hello', no_ack=True)

# 死循环监听指定队列是否有消息
channel.start_consuming()
```
### 消息持久化
    当有生产者发送到RabbitMQ服务器，此时在消费者还未取出队列的时候，服务器Down机了，这是消息队列会丢失，此时为了队列的持久化可以在声明队列的时候指定durable=True,此时消费者也需要指定durable=True
```
# 声明持久化消息队列，当服务器down掉也会存在
channel.queue_declare(queue='hello', durable=True)
```
>- durable=True参数只能保证消息队列（queue）不丢失，但是消息会丢失，此时需要指定channel.basic_publish的properties参数
```
channel.basic_publish(exchange='', routing_key='task', body='Hello World!',
                      properties=pika.BasicProperties(
                          delivery_mode=2,  # make message persistent
                      ))
```
### 消息安全
    当生产者发送消息到RabbitMQ到服务器的时候，消费者在处理数据的过程中down掉了，此时消息就会丢失，为了保证消息的持久化，当消费者处理完后应向服务器发送处理完成确认。
```python
import pika
import time
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))  # 连接RabbitMQ
channel = connection.channel()  # 建立RabbitMQ协议通道
channel.queue_declare(queue='hello')  # 声明queue
def callback(ch, method, properties, body):
    print(body)
    # time.sleep(20)
    print(" [x] Received %r" % ch, method, properties, body)
    ch.basic_ack(delivery_tag=method.delivery_tag)  # 给服务器发送处理完消息的标识符
channel.basic_consume(callback, queue='hello', 
                    no_ack=False # 启用ack确认，当数据处理完毕，想服务器发送标识符
                    )
channel.start_consuming()

```
### 公平分发
    如果Rabbit只管按顺序把消息发到各个消费者身上，不考虑消费者负载的话，很可能出现，一个机器配置不高的消费者那里堆积了很多消息处理不完，同时配置高的消费者却一直很轻松。为解决此问题，可以在各个消费者端，配置perfetch=1,意思就是告诉RabbitMQ在我这个消费者当前消息还没处理完的时候就不要再给我发新消息了。
```
channel.basic_qos(prefetch_count=1)
```
### 消息持久化，消息安全，公平分发完整代码
#### 生产者（发送端）
```python
import pika
# 封装RabbitMQ的认证信息
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
# 连接RabbitMQ
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))
# 建立RabbitMQ协议通道
channel = connection.channel()
# 声明queue
channel.queue_declare(queue='task', durable=True)
# 发送消息到指定的队列，
channel.basic_publish(exchange='',
                      routing_key='task',  # 指定发送到那个队列
                      body='Hello World!',  # 发送到队列的内容
                      properties=pika.BasicProperties(  # 消息持久化
                          delivery_mode=2,  # make message persistent
                      ))
# 关闭队列
connection.close()
```
#### 消费者(接收端)
```python
import pika
# 封装RabbitMQ的认证信息
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
# 连接RabbitMQ
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))
# 建立RabbitMQ协议通道
channel = connection.channel()
# 声明queue
channel.queue_declare(queue='task', durable=True)


# 当有消息的时候执行的回调函数
def callback(ch, method, properties, body):
    # ch：  通道（channel）的实例
    # method： 发送端发送消息附带的参数
    # proprties: 属性
    # body: 发送端发送过来的数据
    ch.basic_ack(delivery_tag=method.delivery_tag)  # 给服务器发送处理完消息的标识符


# 公平消息队列，当有任务处理的时候通知RabbitMQ不再接收任务
channel.basic_qos(prefetch_count=1)
# 接受指定队列的消息
channel.basic_consume(callback, queue='hello', no_ack=False)  # no_ack：关系自动回应确认消息
# 循环接受
channel.start_consuming()
```
## 发布/订阅
    一个消息发送到多个 消费者。 这种模式被称为“发布/订阅”。
![](http://www.rabbitmq.com/img/tutorials/exchanges.png)
### 应用场景
1. 在app中给所有VIP推送消息
2. 所有订阅咨询的用户推送消息
### Exchange类型
    在声明队列的时候需要指定交换类型
>- fanout: 所有bind到此exchange的queue都可以接收消息
>- direct: 通过routingKey和exchange决定的那个唯一的queue可以接收消息
>- topic:所有符合routingKey(此时可以是一个表达式)的routingKey所bind的queue可以接收消息
>>- 表达式符号说明：#代表一个或多个字符，*代表任何字符
>>- 例：#.a会匹配a.a，aa.a，aaa.a等
>>- *.a会匹配a.a，b.a，c.a等
>>- 注：使用RoutingKey为#，Exchange Type为topic的时候相当于使用fanout　
### fanout,订阅/发布
>- 生产者
```python
import pika
# 封装RabbitMQ的认证信息
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
# 连接RabbitMQ
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))
# 建立RabbitMQ协议通道
channel = connection.channel()


##########################################################################################
# 声明queue
channel.exchange_declare(exchange='logs', exchange_type='fanout')
channel.basic_publish(exchange='logs', routing_key='', body='Hello, Woeld')
########################################################################################################


connection.close()
```
>- 消费者
```python
import pika
# 封装RabbitMQ的认证信息
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
# 连接RabbitMQ
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))
# 建立RabbitMQ协议通道
channel = connection.channel()


##########################################################################################################
# 声明queue
channel.exchange_declare(exchange='logs', exchange_type='fanout') # 指定交换名称个类型
 # 不指定queue名字,rabbit会随机分配一个名字,exclusive=True会在使用此queue的消费者断开后,自动将queue删除
result = channel.queue_declare(exclusive=True) 
queue_name = result.method.queue
channel.queue_bind(exchange='logs', queue=queue_name)
############################################################################################################3


def callback(ch, method, properties, body):
    print(" [x] %r" % body)

channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=True)
channel.start_consuming()
```
### 有选择的接收消息(exchange type=direct)
    RabbitMQ还支持根据关键字发送，即：队列绑定关键字，发送者将数据根据关键字发送到消息exchange，exchange根据 关键字 判定应该将数据发送至指定队列。
    比如有个接受日志的消息，当然日志分级别的，可以定义消费者接受制定的日志级别,
![img](http://www.rabbitmq.com/img/tutorials/python-four.png)
>- 发送端
```python
import pika
import re
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))
channel = connection.channel()

# 声明组播队列，指定类型为direct
channel.exchange_declare(exchange='rabbitmq_log', exchange_type='direct')

# RabbitMQ的日志
log = [
    "=INFO REPORT==== 17-Jun-2018::20:56:52 ===connection <0.1988.0> (10.0.0.1:7853 -> 10.0.0.101:5672): user 'xiaojie' authenticated and granted access to vhost '/'",
    "=WARNING REPORT==== 17-Jun-2018::20:36:01 ===closing AMQP connection <0.1748.0> (10.0.0.1:7305 -> 10.0.0.101:5672, vhost: '/', user: 'xiaojie'):client unexpectedly closed TCP connection",
    '=ERROE REPORT==== 17-Jun-2018::20:36:01 ===this is rabbit error log'
]

# 循环日志发送发到服务端
for i in log:
    # 解析日志等级
    lavel = re.search(r'^=(?P<lave>\w+)', i).group('lave')
    channel.basic_publish(exchange='rabbitmq_log',
                          routing_key=lavel,
                          body=i)
connection.close()
```
>- 接收端
```python
import pika
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))
channel = connection.channel()
channel.exchange_declare(exchange='rabbitmq_log', exchange_type='direct')
result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue

# 绑定到指定的消息队列，和接受的日志级别如 INFO, ERROR
channel.queue_bind(exchange='rabbitmq_log',
                   queue=queue_name,
                   routing_key='INFO')

def callback(ch, method, properties, body):
    print(" [x] %r:%r" % (method.routing_key, body))

# 开始接受日列
channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=True)

channel.start_consuming()
```
### 更细致的过来接受消息(exchange_type=topic)
    根据(* , . , #)去匹配routing_key
1. \#： 代表接受全部
2. .: 代表分割
3. *： 代表匹配一个单词
```
例如:
    mysql.erro : this is mysql
    mysql.info : test
    mysql.django.info :test
1. # :代表接受以上全部
2. mysql.* 代表接受 mysql.erro, mysql.info 
3. mysql.info : 接受mysql.info
5. *.django.* ： 接受包含django的， mysql.django.info
```
![](http://www.rabbitmq.com/img/tutorials/python-five.png)
>- 发送端
```python
import pika
import re
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))
channel = connection.channel()

channel.exchange_declare(exchange='app_logs', exchange_type='topic')
log = [
    'mysql.info this is mysql logs',
    'kern.erro this is kern logs',
    'mysql.django.info test'
]
for i in log:
    routing_key = re.match(r'(?P<app>\w+.\w+)', i).group('app')
    print(routing_key)
    channel.basic_publish(exchange='app_logs',
                          routing_key=routing_key,
                          body=i)
connection.close()

```
>- 接收端
```python
import pika
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))
channel = connection.channel()
channel.exchange_declare(exchange='app_logs', exchange_type='topic')
result = channel.queue_declare(exclusive=True)
queue_name = result.method.queue

'''
routing_key='#', 接受全部日志
routing_key='mysql.*' 接受mysql.erro, mysql.info,......
routing_key='*.django.*' 接受包含django的日志
'''
channel.queue_bind(exchange='app_logs',
                   queue=queue_name,
                   routing_key='mysql.*')

def callback(ch, method, properties, body):
    print(" [x] %r:%r" % (method.routing_key, body))


channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=True)

channel.start_consuming()

```
## RPC远程过程调用
    客户端发送请求到队列，服务端从队列取数据进行处理，处理完毕将结果加上一个唯一标识符返回到一个新的队列让客户端取结果
![](http://www.rabbitmq.com/img/tutorials/python-six.png)
>- 服务端
```python
import pika
credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=credentials))

channel = connection.channel()

channel.queue_declare(queue='rpc_queue')

def fib(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib(n-1) + fib(n-2)

def on_request(ch, method, props, body):
    n = int(body)

    print(" [.] fib(%s)" % n)
    response = fib(n)

    ch.basic_publish(exchange='',
                     routing_key=props.reply_to,
                     properties=pika.BasicProperties(correlation_id = \
                                                         props.correlation_id),
                     body=str(response))
    ch.basic_ack(delivery_tag = method.delivery_tag)

channel.basic_qos(prefetch_count=1)
channel.basic_consume(on_request, queue='rpc_queue')

print(" [x] Awaiting RPC requests")
channel.start_consuming()

```
>- 客户端
```python
import pika
import uuid

class FibonacciRpcClient(object):
    def __init__(self):
        self.credentials = pika.PlainCredentials(username='xiaojie', password='xiaojiexiaojie')  # RadditMQ的用户名密码认证
        self.connection = pika.BlockingConnection(pika.ConnectionParameters(host='10.0.0.101', port=5672, credentials=self.credentials))

        self.channel = self.connection.channel()

        result = self.channel.queue_declare(exclusive=True)
        self.callback_queue = result.method.queue

        self.channel.basic_consume(self.on_response, no_ack=True,
                                   queue=self.callback_queue)

    def on_response(self, ch, method, props, body):
        if self.corr_id == props.correlation_id:
            self.response = body

    def call(self, n):
        self.response = None
        self.corr_id = str(uuid.uuid4())
        self.channel.basic_publish(exchange='',
                                   routing_key='rpc_queue',
                                   properties=pika.BasicProperties(
                                         reply_to = self.callback_queue,
                                         correlation_id = self.corr_id,
                                         ),
                                   body=str(n))
        while self.response is None:
            self.connection.process_data_events()
        return int(self.response)

fibonacci_rpc = FibonacciRpcClient()

print(" [x] Requesting fib(30)")
response = fibonacci_rpc.call(30)
print(" [.] Got %r" % response)
```
