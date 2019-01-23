# Celery
    Celery 是一个 基于python开发的分布式异步消息任务队列，通过它可以轻松的实现任务的异步处理， 如果你的业务场景中需要用到异步任务，就可以考虑使用celery， 举几个实例场景中可用的例子:
    1. 你想对100台机器执行一条批量命令，可能会花很长时间 ，但你不想让你的程序等着结果返回，而是给你返回 一个任务ID,你过一段时间只需要拿着这个任务id就可以拿到任务执行结果， 在任务执行ing进行时，你可以继续做其它的事情。 
    2. 你想做一个定时任务，比如每天检测一下你们所有客户的资料，如果发现今天 是客户的生日，就给他发个短信祝福

# Celery中间人URL格式
>- rabbitMQ
>>- amqp://username:password@ip:port//
>- redis
>>- redis://:password@hostname:port/db_number
>- mongodb
>>- mongodb://userid:password@hostname:port/database_name
>- Amazon SQS
>>- sqs://aws_access_key_id:aws_secret_access_key@
>- CouchDB
>>- couchdb://userid:password@hostname:port/database_name
>- Beanstalk
>>- beanstalk://hostname:port
>- IronMQ
>>- ironmq://project_id:token@
>- SQLAlchemy
```
# sqlite (filename)
BROKER_URL = 'sqla+sqlite:///celerydb.sqlite'

# mysql
BROKER_URL = 'sqla+mysql://scott:tiger@localhost/foo'

# postgresql
BROKER_URL = 'sqla+postgresql://scott:tiger@localhost/mydatabase'

# oracle
BROKER_URL = 'sqla+oracle://scott:tiger@127.0.0.1:1521/sidname'
```
## 初步使用
    使用redis做结果存储，使用rabbitmq做任务队列

>- demo/tasks.py
```python
from celery import Celery

import time
# 创建celery实例
app = Celery('hello',
             broker='amqp://xiaojie:xiaojiexiaojie@10.0.0.101:5672/',  # 任务队列中间人
             backend='redis://:xiaojiexiaojie@10.0.0.102:6379/0'  # 任务执行结果的存储
             )


# 创建任务, app.task装饰后将task函数变成呢给一个异步任务，task.delay函数将任务序列化后发送到rabbitmq
# 该过程创建一个名字为celery的exchange，类型为direct（直连交换机）;创建一个名为celery的queue，队列和交换机使用路由键celery绑定；
@app.task
def task(sleep):
    start_time = time.time()
    time.sleep(sleep)
    end_time = time.time()
    return [start_time, end_time]
```
>- 启动worker
>>- celery -A demo.tasks worker -l info -P eventlet
```
A参数指定celery对象的位置，该app.celery_tasks.celery指的是app包下面的celery_tasks.py模块的celery实例，注意一定是初始化后的实例，
l参数指定worker的日志级别；
```
>- 发送任务到队列中
```python
from demo.tasks import *
a = task.delay(10)
```
## celery的装饰方法Celery.task
    task方法将任务装饰成异步
```python
from celery import Celery

import time
# 创建celery实例
app = Celery('hello',
             broker='amqp://xiaojie:xiaojiexiaojie@10.0.0.101:5672/',  # 任务队列中间人
             backend='redis://:xiaojiexiaojie@10.0.0.102:6379/0'  # 任务执行结果的存储
             )


# 创建任务, app.task装饰后将task函数变成呢给一个异步任务，task.delay函数将任务序列化后发送到rabbitmq
# 该过程创建一个名字为celery的exchange，类型为direct（直连交换机）;创建一个名为celery的queue，队列和交换机使用路由键celery绑定；
@app.task
def task(sleep):
    pass
```
>- Celery.task(name, serializer, bind, base, exchange)
>>- name: 课可以显示指定任务的名字
>>- serializer: 执行序列化的方法
>>- bind: 一个bool值，设置是否绑定一个task的实例，如果把绑定，task实例会作为参数传递到任务方法中，可以访问task实例的所有的属性，即前面反序列化中那些属性
```
@app.task(bind=True)  # 第一个参数是self，使用self.request访问相关的属性
def add(self, x, y):
    logger.info(self.request.id)
```
>>- base: 定义任务的基类，可以以此来定义回调函数
```python
from celery import Celery, Task
import time

filepath = r'E:\code\python\markdown笔记\demo\1.txt'
app = Celery('hello',
             broker='amqp://xiaojie:xiaojiexiaojie@10.0.0.101:5672/',
             backend='amqp://xiaojie:xiaojiexiaojie@10.0.0.101:5672/'
             )


class MyTask(Task):
    # 任务失败时执行
    def on_failure(self, exc, task_id, args, kwargs, einfo):
        print('{0!r} failed: {1!r}'.format(task_id, exc))

    # 任务成功时执行
    def on_success(self, retval, task_id, args, kwargs):
        pass

    # 任务重试时执行
    def on_retry(self, exc, task_id, args, kwargs, einfo):
        pass


@app.task(base=MyTask)
def task(sleep):
    start_time = time.time()
    time.sleep(sleep)
    end_time = time.time()
    return [start_time, end_time]

# exc:失败时的错误的类型；
# task_id:任务的id；
# args:任务函数的参数；
# kwargs:参数；
# einfo:失败时的异常详细信息；
# retval:任务成功执行的返回值；
```
### 一些方法
```
r.ready()     # 查看任务状态，返回布尔值,  任务执行完成, 返回 True, 否则返回 False.
r.wait()      # 等待任务完成, 返回任务执行结果，很少使用；
r.get(timeout=1)       # 获取任务执行结果，可以设置等待时间
r.result      # 任务执行结果.
r.state       # PENDING, START, SUCCESS，任务当前的状态
r.status      # PENDING, START, SUCCESS，任务当前的状态
r.successful  # 任务成功返回true
r.traceback  # 如果任务抛出了一个异常，你也可以获取原始的回溯信息
```
## 调用异步任务的方法
    delay, apply_async, send_task
>- task.delay(*args)
>>- 这是apply_async方法的别名,但接受的参数较为简单;
```
a = task.delay(10)

# 返回一个AsyncRsult对象
a # <AsyncResult: 7b95ae07-71b8-4ea0-90a6-d13e33aac84e>

a.__dict__
{'app': <Celery hello at 0x2b41b60c518>,
 'id': '7b95ae07-71b8-4ea0-90a6-d13e33aac84e',
 'backend': <celery.backends.amqp.AMQPBackend at 0x2b41b7cd780>,
 'parent': None,
 'on_ready': <promise@0x2b41b80c558 --> <bound method AsyncResult._on_fulfilled of <AsyncResult: 7b95ae07-71b8-4ea0-90a6-d13e33aac84e>>>,
 '_cache': None,
 '_ignored': False}

# 可以根据task的id来查询任务进度
r = app.AsyncResult(a)

r.ready()     # 查看任务状态，返回布尔值,  任务执行完成, 返回 True, 否则返回 False.
r.wait()      # 等待任务完成, 返回任务执行结果，很少使用；
r.get(timeout=1)       # 获取任务执行结果，可以设置等待时间
r.result      # 任务执行结果.
r.state       # PENDING, START, SUCCESS，任务当前的状态
r.status      # PENDING, START, SUCCESS，任务当前的状态
r.successful  # 任务成功返回true
r.traceback  # 如果任务抛出了一个异常，你也可以获取原始的回溯信息
```
>- task.apply_async(*args, **kwargs)
>>- countdown: 设置该问问无等待一段时间再执行，单位S
>>- eta: 定义任务开始时间; eta=time.time() + 10
>>- expires : 设置任务时间，任务在过期时间后还没有执行则被丢弃；
>>- retry : 如果任务失败后, 是否重试;使用true或false，默认为true
>>- shadow：重新指定任务的名字str，覆盖其在日志中使用的任务名称；
>>- retry_policy : 重试策略.

````
max_retries : 最大重试次数, 默认为 3 次.
interval_start : 重试等待的时间间隔秒数, 默认为 0 , 表示直接重试不等待.
interval_step : 每次重试让重试间隔增加的秒数, 可以是数字或浮点数, 默认为 0.2
interval_max : 重试间隔最大的秒数, 即 通过 interval_step 增大到多少秒之后, 就不在增加了, 可以是数字或者浮点数, 默认为 0.2 .

add.apply_async((2, 2), retry=True, retry_policy={
    'max_retries': 3,
    'interval_start': 0,
    'interval_step': 0.2,
    'interval_max': 0.2,
})
````
>>- routing_key:自定义路由键；
>>- queue：指定发送到哪个队列；
>>- exchange：指定发送到哪个交换机；
>>- priority：任务队列的优先级，0-9之间；
>>- serializer：任务序列化方法；通常不设置；
>>- compression：压缩方案，通常有zlib, bzip2
>>- headers：为任务添加额外的消息；
>>- link：任务成功执行后的回调方法；是一个signature对象；可以用作关联任务；
>>- link_error: 任务失败后的回调方法，是一个signature对象；
>>- 自定义发布者,交换机,路由键, 队列, 优先级,序列方案和压缩方法:
```
task.apply_async((2,2), 
    compression='zlib',
    serialize='json',
    queue='priority.high',
    routing_key='web.add',
    priority=0,
    exchange='web_exchange')
```
>- send_task
>>- 可以发送未被注册的异步任务，即没有被celery.task装饰的任务；
```python
# tasks.py
from celery import Celery
app = Celery()
def add(x,y):
    return x+y

app.send_task('tasks.add',args=[3,4])  # 参数基本和apply_async函数一样
# 但是send_task在发送的时候是不会检查tasks.add函数是否存在的，即使为空也会发送成功
```
## Celery从配置文件中加载配置
>- settings.py
```python
# 注意，celery4版本后，CELERY_BROKER_URL改为BROKER_URL
BROKER_URL = 'amqp://username:passwd@host:port/虚拟主机名'
# 指定结果的接受地址
CELERY_RESULT_BACKEND = 'redis://username:passwd@host:port/db'
# 指定任务序列化方式
CELERY_TASK_SERIALIZER = 'msgpack' 
# 指定结果序列化方式
CELERY_RESULT_SERIALIZER = 'msgpack'
# 任务过期时间,celery任务执行结果的超时时间
CELERY_TASK_RESULT_EXPIRES = 60 * 20   
# 指定任务接受的序列化类型.
CELERY_ACCEPT_CONTENT = ["msgpack"]   
# 任务发送完成是否需要确认，这一项对性能有一点影响     
CELERY_ACKS_LATE = True  
# 压缩方案选择，可以是zlib, bzip2，默认是发送没有压缩的数据
CELERY_MESSAGE_COMPRESSION = 'zlib' 
# 规定完成任务的时间
CELERYD_TASK_TIME_LIMIT = 5  # 在5s内完成任务，否则执行该任务的worker将被杀死，任务移交给父进程
# celery worker的并发数，默认是服务器的内核数目,也是命令行-c参数指定的数目
CELERYD_CONCURRENCY = 4 
# celery worker 每次去rabbitmq预取任务的数量
CELERYD_PREFETCH_MULTIPLIER = 4 
# 每个worker执行了多少任务就会死掉，默认是无限的
CELERYD_MAX_TASKS_PER_CHILD = 40 
# 设置默认的队列名称，如果一个消息不符合其他的队列就会放在默认队列里面，如果什么都不设置的话，数据都会发送到默认的队列中
CELERY_DEFAULT_QUEUE = "default" 
# 设置详细的队列
CELERY_QUEUES = {
    "default": { # 这是上面指定的默认队列
        "exchange": "default",
        "exchange_type": "direct",
        "routing_key": "default"
    },
    "topicqueue": { # 这是一个topic队列 凡是topictest开头的routing key都会被放到这个队列
        "routing_key": "topic.#",
        "exchange": "topic_exchange",
        "exchange_type": "topic",
    },
    "task_eeg": { # 设置扇形交换机
        "exchange": "tasks",
        "exchange_type": "fanout",
        "binding_key": "tasks",
    },
    
}
```
>- task.py
```python
from celery import Celery
from. import settings
import time

app = Celery()
# 从settings.py 文件中加载配置
app.config_from_object(settings)

@app.task
def task(sleep):
    start_time = time.time()
    time.sleep(sleep)
    end_time = time.time()
    return [start_time, end_time]

```
## Celery与定时任务
    在celery中执行定时任务非常简单，只需要设置celery对象的CELERYBEAT_SCHEDULE属性即可。
```python
from celery.schedules import crontab

CELERY_TIMEZONE = 'UTC'
CELERYBEAT_SCHEDULE = {
    'add_schedule': {
            "task":"tasks.add",
            "schedule":10,
            "args":(1,2)
        },
    # 周期性任务
    'task-one': {
        'task': 'app.tasks.print_hello',
        'schedule': 5.0, # 每5秒执行一次
        # 'args': ()
    },
    # 定时任务
    'task-two': {
        'task': 'app.tasks.print_hello',
        'schedule': crontab(minute=0, hour='*/3,10-19'),
        # 'args': ()
    }
}

```
>- 启动定时任务
>>- celery -A { project_name } beat -l info
## Celery与分布式
https://www.cnblogs.com/rio2607/p/4421019.html
## Celery在Django中配置
    Django2.0 + Celerr4.2
1. 在Django的Setting.py配置文件中配置
```python
...
LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'
USE_I18N = True
USE_L10N = True
USE_TZ = False

CELERY_BROKER_URL = 'amqp://xiaojie:xiaojiexiaojie@10.0.0.101:5672/'
CELERY_RESULT_BACKEND = 'amqp://xiaojie:xiaojiexiaojie@10.0.0.101:5672/'
CELERYD_MAX_TASKS_PER_CHILD = 10 # 
```
2. 在settings.py同级目录创建celery.py
```python
# celery.py
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery

# 设置环境变量
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'project_name.settings')

# 注册Celery的APP
app = Celery('project_name')
# 绑定配置文件
app.config_from_object('django.conf:settings', namespace='CELERY')

# 自动发现各个app下的tasks.py文件
app.autodiscover_tasks()
```
3. 修改settings.py同级目录的init.py文件
```python
# __init__.py
from __future__ import absolute_import, unicode_literals
from .celery import app as celery_app

__all__ = ['celery_app']

```
4. 在某个APP中创建tasks.py文件
```python
# tasks.py
from celery.task import task

# 自定义要执行的task任务
@task
def print_hello():
    return 'hello celery and django...'
```
5. 配置周期性任务或定时任务
>- 在django的settings.py 中配置
>- 定时任务的配置格式参考：http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html
```python
from celery.schedules import crontab
CELERY_BEAT_SCHEDULE = {
    # 周期性任务
    'task-one': {
        'task': 'app.tasks.print_hello',
        'schedule': 5.0, # 每5秒执行一次
        # 'args': ()
    },
    # 定时任务
    'task-two': {
        'task': 'app.tasks.print_hello',
        'schedule': crontab(minute=0, hour='*/3,10-19'),
        # 'args': ()
    }
}
```
6. 启动worker和定时任务
>- 启动worker (切换到manage.py同级目录下执行)
>- 启动worker
>>- celery -A project_name worker -l info -P eventlet
>- 启动定时任务或周期性任务
>>- celery -A project_name beat -l info
