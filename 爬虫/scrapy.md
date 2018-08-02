# Scrapy
>- Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 其可以应用在数据挖掘，信息处理或存储历史数据等一系列的程序中。
其最初是为了页面抓取 (更确切来说, 网络抓取 )所设计的， 也可以应用在获取API所返回的数据(例如 Amazon Associates Web Services ) 或者通用的网络爬虫。Scrapy用途广泛，可以用于数据挖掘、监测和自动化测试。

## Scrapy整体架构
>- Scrapy使用了 Twisted异步网络库来处理网络通讯。

![image](https://images2015.cnblogs.com/blog/425762/201605/425762-20160507220247421-1722096301.png)
### Scrapy主要包括了以下组件
>- 引擎(Scrapy)
>>- 用来处理整个系统的数据流处理, 触发事务(框架核心)
>- 调度器(Scheduler)
>>- 用来接受引擎发过来的请求, 压入队列中, 并在引擎再次请求的时候返回. 可以想像成一个URL（抓取网页的网址或者说是链接）的优先队列, 由它来决定下一个要抓取的网址是什么, 同时去除重复的网址
>- 下载器(Downloader)
>>- 用于下载网页内容, 并将网页内容返回给蜘蛛(Scrapy下载器是建立在twisted这个高效的异步模型上的)
>- 爬虫-线程-进程(Spiders)
>>- 爬虫是主要干活的, 用于从特定的网页中提取自己需要的信息, 即所谓的实体(Item)。用户也可以从中提取出链接,让Scrapy继续抓取下一个页面
>- 项目管道(Pipeline)
>>- 负责处理爬虫从网页中抽取的实体，主要的功能是持久化实体、验证实体的有效性、清除不需要的信息。当页面被爬虫解析后，将被发送到项目管道，并经过几个特定的次序处理数据。
>- 下载器中间件(Downloader Middlewares)
>>- 位于Scrapy引擎和下载器之间的框架，主要是处理Scrapy引擎与下载器之间的请求及响应。
>- 爬虫中间件(Spider Middlewares)
>>- 介于Scrapy引擎和爬虫之间的框架，主要工作是处理蜘蛛的响应输入和请求输出。
>- 调度中间件(Scheduler Middewares)
>>- 介于Scrapy引擎和调度之间的中间件，从Scrapy引擎发送到调度的请求和响应。
### Scrapy运行流程大概如下：
1. 引擎从调度器中取出一个链接(URL)用于接下来的抓取
2. 引擎把URL封装成一个请求(Request)传给下载器
3. 下载器把资源下载下来，并封装成应答包(Response)
4. 爬虫解析Response
5. 解析出实体（Item）,则交给实体管道进行进一步的处理
6. 解析出的是链接（URL）,则把URL交给调度器等待抓取
## 基本使用-创建项目
    创建项目: scrapy startproject your_project_name
>- 自动创建目录：
```
|- -    project_name/
|- - -      scrapy.cfg          #项目的配置信息，主要为Scrapy命令行工具提供一个基础的配置信息。（真正爬虫相关的配置信息在settings.py文件中）
|- - -      project_name/
|- - - -        __init__.py
|- - - -        items.py        # 设置数据存储模板，用于结构化数据，如：Django的Model
|- - - -        pipelines.py    # 数据处理行为，如：一般结构化的数据持久化
|- - - -        settings.py     # 配置文件，如：递归的层数、并发数，延迟下载等
|- - - -        spiders/        # 爬虫目录，如：创建文件，编写爬虫规则
|- - - - -          __init__.py
```
## Scrapy-创建爬虫
    创建爬虫: `cd your_project_name` `scrapy genspider carhome autohome.com.cn`

* 创建完爬虫会在spiders中生成爬虫的py文件

```
carhome.py

# -*- coding: utf-8 -*-
import scrapy
class CarhomeSpider(scrapy.Spider):
    name = 'carhome'                                        # 这个爬虫的名称，不能省略
    allowed_domains = ['autohome.com.cn','www.baidu.com]    # 限定爬虫爬取的域名
    start_urls = ['https://www.autohome.com.cn/news/']      # 开始爬取的URL

    def parse(self, response):
        # response 响应的数据
        # response.url 响应的url
        # response.text 整个页面的内容
        # response.meta = {'depth': '爬取的深度'}
        pass
```
* 特殊方法-爬虫的入口
```
    # 此方法会在 爬虫启动的时候执行，为父类的方法，重写此方法实现自定义操作
    def start_requests(self):
        for url in self.start_urls:
            yield Request(url,callback=self.parse)
```
## Scrapy-采集数据

>- from scrapy.selector import Selector
>- hex = Selector(respones).xpath("//div")
>>- xpath选择器语法：
>>>-        //                                                     #表示子孙中查找
>>>-        .//                                                    #表示当前标签的子孙中查找
>>>-         /                                                      #当前标签的的儿子中查找
>>>-         //a                                                    # 查找所有a标签
>>>-        //a[2]                                                 # 查找a标签中的第二个标签
>>>-        //a[@id]                                               # 查找有id属性的标签
>>>-        //div[id="i1"]                                         #查找id为i1的标签
>>>-        //a[re:test(@herf, '/all/hot/\d+')]                # 正则表达式查找a标签
>>>-        //a[starts-with(@href, "/news/")]                  # 跟进字符串开头进行查找
>>>-        //a[contains(@href, "link")]                       # 查找href属性包含link的标签
>>>-        .//img/@src                                            # img标签的sec属性的内容
>>>-        ./a/@href                                              # a标签的href属性
>>>-        .//h3/text()                                           # h3标签的文本内容
### 采集数据实例
```
def parse(self, response):
        page = Selector(response=response).xpath('//a[re:test(@href, "/news/\d+/#liststart")]/@href').extract()  # 查找当前页的a标签
        title = Selector(response=response).xpath('//div[@id="auto-channel-lazyload-article"]//li')
        for i in title:
            news_title = i.xpath('.//h3/text()').extract_first()  # 新闻标题
            news_herf = i.xpath('./a/@href').extract_first()  # 新闻链接
            news_png = i.xpath('.//img/@src').extract_first()  # 新闻图片
            news_herf = 'http:%s' % news_herf
            news_png = 'http:%s' % news_png
```
## Scrapy-调度器
>- 模块：  from scrapy.http import Request
>- 基本使用：在scrapy框架中，使用yield抛出请求则就是把请求交给调度器，等待引擎去执行
>>- yield Request(url='', callback='xx')
>- 参数详解:
>>- Request(url, callback=None, method='GET', headers=None, body=None,cookies=None, meta=None, encoding='utf-8', priority=0, dont_filter=False, errback=None, flags=None)
>>>- url: 需要请求的url
>>>- callback： 请求完成后需要执行的函数
>>>- method: 请求方式，get，post....
>>>- headers: 请求头
>>>- body: 请求体：boby='ke=vi&kw=v2'
>>>- cookies: 请求cookies
## Sceapy-响应对象response
    在请求完的时候，请求数据会封装成HtmlResponse对象返回给callback函数
>- 模块： from scrapy.http.response.html import HtmlResponse
```
print(response.headers)  # 响应头
print(response.status)  # 状态码
print(response.request)  # 请求对象
print(response.meta)  # 请求meta
print(response.body) # 响应体
```



### 请求指纹重复过滤器
    用来过滤重复的URL请求, 就是过滤重复url

>- 默认的过滤器: from scrapy.dupefilter import RFPDupeFilter

#### 自定义请求URL重复过滤器
> 1. 自定义重复请求url过滤器规则:
```
必须存在以下方法：
@classmethod
def from_settings(cls, settings):  # Scrapy内部会通过此方法创建对象
def request_seen(self, request):   # 每次请求都会经过此方法
def open(self):  # 爬虫开始的时候会调用openj方法
def close(self, reason):  # 爬虫关闭的时候会执行close方法
def log(self, request, spider):  # 每一次请求都会调用log
```
> 2. 去settings中注册

        DUPEFILTER_CLASS = 'news.duplicatioe.MyDupeFilter'
>3. 完整代码
```

class MyDupeFilter(object):

    def __init__(self):
        self.url_set = set()

    @classmethod
    def from_settings(cls, settings):  # Scrapy内部会通过此方法创建对象
        return cls()

    def request_seen(self, request):

        if request.url in self.url_set:
            return True
        self.url_set.add(request.url)
        return False

    def open(self):  # 爬虫开始的时候会调用openj方法
        print('open')
        pass

    def close(self, reason):  # 爬虫关闭的时候会执行close方法
        pass

    def log(self, request, spider):  # 每一次请求都会调用log
        pass
```
#### 请求指纹重复过滤器-总结
1. 爬虫执行过滤器的顺序
>1. 爬虫首先创建过滤器实例：                    from_settings
>2. 创建完毕实例之后执行:                     open
>3. 每次yield Request的时候都会执行            log 和 request_seen
>4. 爬虫结束后执行 close
## Scrapy-Items
    Items是用来格式化爬虫获取的数据的

```
/news/news/items.py
import scrapy


class NewsItem(scrapy.Item):
    news_title = scrapy.Field() # 存放新闻标题
    news_link = scrapy.Field() # 存放新闻练级
    news_png = scrapy.Field() # 存放新闻图片链接
```

```
yield Item.NewsItem(news_title='小人国与白雪公猪', news_link='http://www.nibuzhidao.com',news_png='http://a.png')
```


## Scrapy-pipline
    pipline 是用来处理爬虫yield Items（） 过来的数据，进行数据持久化的

>- 当执行 yield Item(name='xxx', title='xxx') 的时候 Scrapy会从settings配置文件中读取ITEM_PIPELINES的值，跟进权重把数据丢给pipline

### setting中注册pipline
```
ITEM_PIPELINES = {
   'news.pipelines.NewsPipeline1': 300, # 后边数字是权重，权重大，则先执行, 但是必须在process_item 方法中  return item才会交给下一个pipline
   # 如果不想returnm  需要 from scrapy.exceptions import DropItem      raise DropItem（）   抛出此异常
   'news.pipelines.NewsPipeline2': 400,
}
```
### pipline中的方法
>- def from_crawler(cls, crawler): 爬虫初始化通过次方法创建pipline对象
>- def open_spider(self,spider): 爬虫开始执行的时候调用，在重复指纹过滤器open之后
>- def close_spider(self,spider): 爬虫结束的时候调用,在重复指纹过滤器close方法之前调用
>- def process_item(self,item,spider): 每次yield Items的时候调用
### pipline实例:
```
from scrapy.exceptions import DropItem
import pymysql

class MySQLPipeline(object):

    def __init__(self, db_name, db_host, db_port, db_user, db_passwd):
        self.db_name = db_name
        self.db_host = db_host
        self.db_port = db_port
        self.db_user = db_user
        self.db_passwd = db_passwd
        self.db_conn = None
        self.db_cur = None

    @classmethod
    def from_crawler(cls, crawler):
        db_name = crawler.settings.get('MYSQL_DB_NAME', 'scrapy_db')
        host = crawler.settings.get('MYSQL_HOST', 'localhost')
        port = crawler.settings.get('MYSQL_PORT', 3306)
        user = crawler.settings.get('MYSQL_USER', 'root')
        passwd = crawler.settings.get('MYSQL_PASSWORD', '123456')
        return cls(db_name, host, port, user, passwd)

    # 打开数据库
    def open_spider(self, spider):
        self.db_conn = pymysql.connect(host=self.db_host, port=self.db_port, db=self.db_name, user=db_user, passwd=self.db_passwd, charset='utf8')
        self.db_cur = self.db_conn.cursor()

    # 关闭数据库
    def close_spider(self, spider):
        self.db_conn.commit()
        self.db_conn.close()

    # 对数据进行处理
    def process_item(self, item, spider):
        self.insert_db(item)
        # return item  # return 代表 交给下一个pipline去处理
        raise DropItem() # 丢弃Item  不交给下一个pipline

    #插入数据
    def insert_db(self, item):
        values = (
            item['news_title'],
            item['news_link'],
            item['news_png'],
        )

        sql = 'INSERT INTO books VALUES(%s,%s,%s,%s,%s,%s)'
        self.db_cur.execute(sql, values)
```
## Scrapy-cookie
    在scrapy中的cookie封装在CookieJar
1. 导入模块
    - from scrapy.http.cookies import CookieJar
2. 创建对象并传递响应数据
    - cookie_jar = CookieJar()  # 实例化对象
    - cookie_jar.extract_cookies(response, response.request) # 把响应数据传递给cookie_jar
3. 获取cookie
    - self.cookie_jar = cookie_jar._cookies
4. 请求的时候携带cookie
    - yield Request(url='http://dig.chouti.com/login', cookies=self.cookie_jar, callback=self.check_login )
5. 自动操作cookies（在Request参数中添加 meta={'cookiejar': True}）
    - yield Request(url='http://dig.chouti.com/login', callback=self.check_login, meta={'cookiejar': True} )
## Scrapy-信号
    Scrapy预留了很多钩子，用来让我们扩展
### Scrapy预留的钩子
>- engine_started = object()
>>- 引擎开始的时候执行
>- engine_stopped = object()
>>- 引擎关闭的时候执行
>- spider_opened = object()
>>- 爬虫开始的时候执行
>- spider_idle = object()
>>- 爬虫空闲的时候执行
>- spider_closed = object()
>>- 爬虫关闭的之后执行
>- spider_error = object()
>>- 爬虫错误的时候执行
>- request_scheduled = object()
>>- 请求交给调度器的时候执行
>- request_dropped = object()
>>-
>- response_received = object()
>>- 请求响应接收的时候执行
>- response_downloaded = object()
>>- 请求下载的时候执行
>- item_scraped = object()
>>- yield item的时候执行
>- item_dropped = object()
### 实现Scrapy钩子
1. 自定义类
```
from scrapy import signals

class MyExtensions:

    def __init__(self, crawler):
        self.crawler = crawler
        self.noisy = False
        self.crawler.signals.connect(self.start_engine, signals.engine_started)
        self.crawler.signals.connect(self.stop_engine, signals.engine_stopped)
        self.crawler.signals.connect(self.request_dropped, signals.request_dropped)

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler)

    def start_engine(self):
        print('引擎开始的执行执行此函数')

    def stop_engine(self):
        print('引擎结束的时候执行此函数')

    def request_dropped(self):
        print('request_dropped')
```
2. setting中注册此扩展
```
EXTENSIONS = {
   # 'scrapy.extensions.telnet.TelnetConsole': None,
   'news.extensions.MyExtensions':300
}
```
## Scrapy-settings配置文件详解
```
BOT_NAME = 'news'  # 爬虫的名字 会放在USER_AGENT中携带到请求里

SPIDER_MODULES = ['news.spiders'] # 爬虫的路径
NEWSPIDER_MODULE = 'news.spiders' # 爬虫的路径

# USER_AGENT = 'news (+http://www.yourdomain.com)' # 同http请求的请求头中User-Agent
USER_AGENT = 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 UBrowser/6.2.3964.2 Safari/537.36'

ROBOTSTXT_OBEY = True # 是否遵守爬虫的规则，如果遵守会检测网站中配置文件中是否允许爬虫

CONCURRENT_REQUESTS = 32 # 请求并发数，默认并发16个请求

DOWNLOAD_DELAY = 3  # 下载延迟，表示每个请求访问前的阻塞3秒
CONCURRENT_REQUESTS_PER_DOMAIN = 16 # 针对每个域名并发请求数
CONCURRENT_REQUESTS_PER_IP = 16     # 针对每个IP的并发请求数

COOKIES_ENABLED = True # 是否爬取cookie, 默认为True
COOKIES_DEBUG = True # 开启cookie的debug模式，每次访问都会打印cookie


TELNETCONSOLE_ENABLED = False   # 监听6023端口，可以远程查看爬虫的情况"telnet 127.0.0.1"

DEFAULT_REQUEST_HEADERS = {     # http请求的请求头
  'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
  'Accept-Language': 'zh-CN,zh;q=0.8',
}

SPIDER_MIDDLEWARES = {
   'news.middlewares.NewsSpiderMiddleware': 543,
}

EXTENSIONS = {  # 爬虫的信号注册到此选项
   # 'scrapy.extensions.telnet.TelnetConsole': None,
   'news.extensions.MyExtensions':300
}

ITEM_PIPELINES = { # 爬虫的pipline注册到此选项
   'news.pipelines.NewsPipeline': 300,
}

SCHEDULER = 'scrapy.core.scheduler.Scheduler'  # 调度器队列

DEPTH_LIMIT = 1     # 爬虫爬取深度

DEPTH_PRIORITY = 0  # 1：广度优先，0：深度优先

DUPEFILTER_CLASS = 'news.duplicatioe.MyDupeFilter' # url重复过滤器
```

### settings-智能限速请求

```
AUTOTHROTTLE_ENABLED = True # 开始自动限速
AUTOTHROTTLE_START_DELAY = 5    # 初始请求延迟
AUTOTHROTTLE_MAX_DELAY = 60     # 最大下载延迟
AUTOTHROTTLE_TARGET_CONCURRENCY = 1.0   # 平均值的波动延迟
AUTOTHROTTLE_DEBUG = False
```
>- 负责限速的类：from scrapy.contrib.throttle import AutoThrottle
>- 自动限速规则
```
1. 获取最小延迟 DOWNLOAD_DELAY
2. 获取最大延迟 AUTOTHROTTLE_MAX_DELAY
3. 设置出事下载延迟 AUTOTHROTTLE_START_DELAY
4. 当请求下载完成后，获取其连接时间latency，请求连接到获取到响应头的时间
5. 用于计算 AUTOTHROTTLE_TARGET_CONCURRENCY
targer_delay = latency / self.target_concurrency
new_delay = (slot.delay + target_delay) / 2.0 # 鄙视上一次的延迟时间
new_delay = max(target_delay, new_delay)
new_delay = min(max(self.maindelay, new)delay), self.maxdelay)
slot.delay = new_delay
```
### settings-缓存
```
HTTPCACHE_ENABLED = True    # 是否启用缓存
HTTPCACHE_POLICY = 'scrapy.extensions,httpcache,DummyPolicy'    # 缓存策略：所有请求均huancu7n，下次再请求直接访问原来缓存即可
HTTPCACHE_POLICY = 'scrapy.extensions,httpcache,Rfc2616Policy' # 缓存策略：跟进http响应头：Cache-Control， Last-Modified 等进行缓存策略
HTTPCACHE_EXPIRATION_SECS = 0   # 缓存超时时间
HTTPCACHE_DIR = 'httpcache'     # 缓存保存路径
HTTPCACHE_IGNORE_HTTP_CODES = []    # 缓存忽略的http状态码
HTTPCACHE_STORAGE = 'scrapy.extensions.httpcache.FilesystemCacheStorage' # 缓存存储的插件，默认保存文件
```
### settings- Https访问
>- Https 访问网站时的两种情况
>>1. 爬去网站使用的可信任证书（默认支持）
```
DOWNLOADER_HTTPCLIENTFACTORY = "scrapy.core.downloader.webclient.ScrapyHTTPClientFactory"
DOWNLOADER_CLIENTCONTEXTFACTORY = "scrapy.core.downloader.contextfactory.ScrapyClientContextFactory"
```
>>2. 要爬去网站使用的自定义证书
```
# 自定义证书 https.py
from scrapy.core.downloader.contextfactory import ScrapyClientContextFactory
from twisted.internet.ssl import (optionsForClientTLS, CertificateOptions, PrivateCertificate)
from OpenSSL import crypto

class MySSLFactory(ScrapyClientContextFactory):
    def getCertificateOptions(self):
        private_key = crypto.load_privatekey(crypto.FILETYPE_PEM, open('/ssl/client.key.unsecure', mode='r').read())
        cartificate = crypto.load_certificate(type=crypto.FILETYPE_PEM, buffer=open('/ssl/client.pem', mode='r').read(0))
        return CertificateOptions(
            privateKey=private_key, # privateKey对象
            certificate=cartificate, # X509对象
            method=getattr(self, 'method', getattr(self, '_ssl_method', None))
        )
# 去settings中注册
DOWNLOADER_HTTPCLIENTFACTORY = "scrapy.core.downloader.webclient.ScrapyHTTPClientFactory"
DOWNLOADER_CLIENTCONTEXTFACTORY = "news.https.MySSLFactory"

```
## Scrapy-中间件
### 下载中间件
    下载中间件是注册在 DOWNLOADER_MIDDLEWARES 中的
>- 执行顺序
```
# 请求下载的时候执行所有中间件的perocess_request方法

if self.perocess_request == None:
    # 丢给下一个下载中间件
if self.perocess_request ==  Response:
    self.process_response
if self.perocess_request ==  Request:
    # 丢给调度器重新请求
if self.perocess_request 抛出 raise IgonoreRequest异常：
    if self.process_exception == None:
        # 交给后续中间件的process_exception处理异常
    if self.process_exception == Request:
        # 交给调度器重新请求
     if self.process_exception == Response:
        # 停止后续的下载中间件的process_exception，交给后续中间件的process_response

# 如果有中间件下载完成后开始·逆序·执行所有中间件的process_response

if self.process_response == Response:
    # 转交给其他中间件的process_response
if self.process_response == request:
    # 丢给调度器重新请求
if self.process_response 抛出 raise IgonoreRequest异常：
    # 调用Request的 errback方法


```
1. 下载中间件的perocess_request
```
返回值为None：继续找后边的中间件去下载
返回值为Response对象：开始执行self.peocess_response方法
返回值为Request对象，将Request交给调度器，重新执行
raise IgonoreRequest：开始执行self.process_exception
```
2. 下载中间件的process_response
```
返回值Request对象： 将Request交给调度气重新执行
返回值Response对象： 转交给其他下载中间件的process_response
raise IgonoreRequest异常： 调用Request的 errback方法
```
3. 下载中间件的process_exception
```
返回值为None: 交给后续中间件的process_exception处理异常
返回值Request对象：# 交给调度器重新请求
返回值Response对象：# 停止后续的下载中间件的process_exception，交给后续中间件的process_response
```
4. 下载中间件实例

### spider中间件
    spider中间件是注册在SPIDER_MIDDLEWARES
>- 执行顺序
```
1.爬虫启动的时候会调用spider中间件的的 process_start_request方法
2.请求下载完后会交给spider中间件的 process_spider_input方法
    2.1 如果process_spider_input方法执行过程中出现异常会执行spider中间件的process_spider_exception
3.执行完毕下载中间件的process_spider_input方法 会交给spider的parse
4.当spider的parse执行完的时候 会吧yield的数据 交给 spider中间件的process_spider_output方法
```
1. spider中间件的process_start_request
```
def process_start_request(self,start_request,spider):
    '爬虫启用时调用
    return必须是包含Request对象的可迭代对象'
    return start_request
```
2. spider中间件的process_spider_input
```
def process_spider_input(self, response, spider):
    # 下载完成执行此函数
    pass
```
3. spider中间件的process_spider_input
```
def process_spider_output(self,response, result, spider):
    # spider处理完成返回 Request或者Item的时候调用
    return result # return 必须包含Request 或 Item 对象的可迭代对象
```
4. spider中间件的process_spider_excaption
```
def process_spider_exception(self, response, exception, spider):
    # 当process_spider_input方法执行过程中出现异常会执行
    # return None: 继续交给后续的spider中间件执行，
    # return 包含response 或者 item的客队带对象，将交给调度器或者pipline执行

```
## Scrapy-代理
    利用下载中间件process_request拦截添加请求头
>- 代理的原理就是在请求头中添加信息
>>- request.meta['proxy'] = to_bytes("http://111.11.228.75:80")
>- 如果代理需要密码验证则
>>- request.headers["Proxy-Authorization"] = to_bytes("Basic" + "basic64加密后的密码 root:xiaojie")
```
# 剖析Scrapy内置代理
from scrapy.contrib.downloadermiddleware.httpproxy import HttpProxyMiddleware
#'内置的代理其实就是利用当前python的环境变量来获取的'
os.environ
# 内置代理格式
{
    http_proxy:http://root:xiaojie@192.168.11.11:8080/,
    https_proky:http://192.168.11.11:8080/
}

# 使用自定义下载中间件实现自定义代理
import random
import base64
class ProxyMiddleware(object):
    def process_request(self, request, spider):
        PROXIEX = {
            {"ip_port": "192.168.11.11:80", 'user_password': 'xiaojie@root'},
            {"ip_port": "192.168.11.11:80", 'user_password': 'xiaojie@root'},
            {"ip_port": "192.168.11.11:80", 'user_password': 'xiaojie@root'},
        }
        proxy = random.choices(PROXIEX)

        if proxy['user_password'] is not None:
            request.meta['proxy'] = bytes('http://%s' % proxy['ip_port'], encoding='utf8')
            encoded_user_pass = base64.encodebytes(proxy['user_password'])
            request.headers['Proxy-Authorization'] = bytes('Basic') + encoded_user_pass
        else:
            request.meta['proxy'] = bytes('http://%s' % proxy['ip_port'], encoding='utf8')

# 自定义完成后在DOWNLOADER_MIDDLEWARES 中进行注册
DOWNLOADER_MIDDLEWARES = {
   'news.download.ProxyMiddleware': 543,
}
```
## Scrapy-自定义命令
1. 在spider统计目录创建任意目录，如commands
2. 在其中创建crawlall.py文件（此处文件名就是自定义的命令）
3. crawlall.py
```
from scrapy.commands import ScrapyCommand


class Command(ScrapyCommand):
    requires_project = True

    def syntax(self):
        return "[options]"

    def short_desc(self):
        return '运行全部的爬虫'

    def run(self, args, opts):
        spider_list = self.crawler_process.spiders.list()
        for name in spider_list:
            self.crawler_process.crawl(name, **opts.__dict__)
        self.crawler_process.start()
```
4. 在setting.py 中饭配置
```COMMANDS_MODULE = "项目名称.目录名称"```
5. 在项目目录执行命令
``` scrapy crawlall```
## 爬虫实例，爬取汽车之家所有的新闻
>- news/news/spoders/carhome
```
# -*- coding: utf-8 -*-
from scrapy.selector import Selector
from scrapy.http import Request
from .. import items
import scrapy


class CarhomeSpider(scrapy.Spider):
    name = 'carhome' # 爬虫的名字，这是必须的
    allowed_domains = ['autohome.com.cn'] # 爬取的域名，如果递归爬取得数据，此列表没有将不会爬取
    start_urls = ['https://www.autohome.com.cn/news/'] # 开始爬取的起始URL
    page_list = set()

    # 此方法是父类中的方法，爬虫执行的时候会先调用此方法
    def start_requests(self):
        for url in selg.start_urls:
            yield Request(url,callback=self.pares)

    # pares用来写爬虫逻辑， response是请求页面响应
    # repsonse.url
    # repsonse.text
    # repsonse.meta= {'depth':'爬取的深度'}，可以在settings配置文件中配置 DEPTH_LIMIT = 1
    def parse(self, response):
        page = Selector(response=response).xpath(
            '//a[re:test(@href, "/news/\d+/#liststart")]/@href').extract()  # 查找当前页的a标签
        for url in page:
            if url in self.page_list:
                pass
            else:
                self.page_list.add(url)
                url = 'https://www.autohome.com.cn%s' % url
                yield Request(url, callback=self.parse)  # 交给调度器,进行轮询发送请求

        title = Selector(response=response).xpath('//div[@id="auto-channel-lazyload-article"]//li')
        for i in title:
            news_title = i.xpath('.//h3/text()').extract_first()  # 新闻标题
            news_herf = i.xpath('./a/@href').extract_first()  # 新闻链接
            news_png = i.xpath('.//img/@src').extract_first()  # 新闻图片
            news_herf = 'http:%s' % news_herf
            news_png = 'http:%s' % news_png
            news_obj = items.NewsItem(
                news_title=news_title,
                news_link=news_herf,
                news_png=news_png)  # 创建item对象
            yield news_obj  # 把item对象交给pipelines
```
>- /news/news/items.py
>>- tiems 相当于djuango 的models 用于格式化数据
```
import scrapy

class NewsItem(scrapy.Item):
    news_title = scrapy.Field()
    news_link = scrapy.Field()
    news_png = scrapy.Field()

```
>- /news/news/pipelines.py
>- 需要去settings中进行注册
```
ITEM_PIPELINES = {
   'news.pipelines.NewsPipeline': 300, # 后边数字是权重，权重大，则先执行
}
```
```
class NewsPipeline(object):

    def process_item(self, item, spider):
        '''
        :param item: yeild 过来的item对象
        :param spider:  是那个爬虫和传递过来的
        '''
        print(spider, item['news_title'], item['news_link'],item['news_png'])
```
# Scrapy-redis
    让Scrapy实现分布式爬虫 pip install scrapy-redis
## 在settings.py文件中配置基本连接redis信息
```python
REDIS_HOST = '127.0.0.1'        # 主机名
REDIS_PORT = 6379               # 端口哈
REDIS_URL = 'redis//user:pass@hostname:9001' # 连接url（优先于以上配置）
REDIS_PARAMS = {'password' : 'xiaojiexiaojie'}
REDIS_PARAMS['redis_cls'] = 'myproject.RedisClient' # 指定连接Redis的python模块，默认redis.StrictRedis
REDIS_ENCODING = 'utf-8'
```
## 使用scrapy_redis的去重规则
```python
DUPEFILTER_CLASS = 'scrapy_redis.dupefilter.RFPDupeFilter'
```
## scrapy_redis组件之三种队列
1. FifoQueue
>- 队列数据结构，先进先出
>- 广度优先
2. LifoQueue
>- 栈数据结构后进先出
>- 深度优先
3. PriorityQueue
>- 优先级队列, 优先级存储在response.request.priority
>- 广度优先（默认）
## scrapy-redis调度器
    正常来说引擎从爬虫中获取url放在调度器中（内中中）, 可以使用scrapy-redis调度器(scrapy_redis.scheduler.Scheduler)放在redis中
1. 调度器配置
```python
# 启用scrapy_redis调度器
SCHEDULER = "scrapy_redis.scheduler.Scheduler"

SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.PriorityQueue'          # 默认使用优先级队列（默认），其他：PriorityQueue（有序集合），FifoQueue（列表）、LifoQueue（列表）
SCHEDULER_QUEUE_KEY = '%(spider)s:requests'                         # 调度器中请求存放在redis中的key
SCHEDULER_SERIALIZER = "scrapy_redis.picklecompat"                  # 对保存到redis中的数据进行序列化，默认使用pickle
SCHEDULER_PERSIST = True                                            # 是否在关闭时候保留原来的调度器和去重记录，True=保留，False=清空
SCHEDULER_FLUSH_ON_START = True                                     # 是否在开始之前清空 调度器和去重记录，rue=清空，False=不清空
SCHEDULER_IDLE_BEFORE_CLOSE = 10                                    # 去调度器中获取数据时，如果为空，最多等待时间（最后没数据，未获取到）。
SCHEDULER_DUPEFILTER_KEY = '%(spider)s:dupefilter'                  # 去重规则，在redis中保存时对应的key
SCHEDULER_DUPEFILTER_CLASS = 'scrapy_redis.dupefilter.RFPDupeFilter'# 去重规则对应处理的类
```
## scrapy_redis数据持久化
```python
# settings
ITEM_PIPELINES= ['crapy_redis.pipelines.RedisPipeline']
```
## scrapy-redis爬虫 RedisSpider
    基于redis的分布式爬虫， 可以把起始url放到redis中

```python
# spider/chout.py
from scrapy_redis.spiders import RedisSpider

class ChoutiSpider(RedisSpider):
    name = 'chouti'
    allowed_domains = ['chouti.com']
    
    def parse(self, response):
        print(response)

# settings.py
REDIS_START_URLS_BATCH_SIZE = 1 # 表示爬虫其实有几个url
REDIS_START_URLS_AS_SET = True # true：把起始url放在redis集合中，  false:把起始url放到redis的列表中
```    

# Scrapy下载大文件
    如果遇到大文件就需要一点一点下载楼
```pipline
from twisted.web.client import Agent, getPage, ResponseDone, PotentialDataLoss

from twisted.internet import defer, reactor, protocol
from twisted.web._newclient import Response
from io import BytesIO


class _ResponseReader(protocol.Protocol):

    def __init__(self, finished, txresponse, file_name):
        self._finished = finished
        self._txresponse = txresponse
        self._bytes_received = 0
        self.f = open(file_name, mode='wb')

    def dataReceived(self, bodyBytes):
        self._bytes_received += len(bodyBytes)

        # 一点一点的下载
        self.f.write(bodyBytes)

        self.f.flush()

    def connectionLost(self, reason=connectionDone):
        if self._finished.called:
            return
        if reason.check(ResponseDone):
            # 下载完成
            self._finished.callback((self._txresponse, 'success'))
        elif reason.check(PotentialDataLoss):
            # 下载部分
            self._finished.callback((self._txresponse, 'partial'))
        else:
            # 下载异常
            self._finished.errback(reason)

        self.f.close()


class BigfilePipeline(object):
    def process_item(self, item, spider):
        # 创建一个下载文件的任务

        if item['type'] == 'file':
            agent = Agent(reactor)
            d = agent.request(
                method=b'GET',
                uri=bytes(item['url'], encoding='ascii')
            )
            # 当文件开始下载之后，自动执行 self._cb_bodyready 方法
            d.addCallback(self._cb_bodyready, file_name=item['file_name'])

            return d
        else:
            return item

    def _cb_bodyready(self, txresponse, file_name):
        # 创建 Deferred 对象，控制直到下载完成后，再关闭链接
        d = defer.Deferred()
        d.addBoth(self.download_result)  # 下载完成/异常/错误之后执行的回调函数
        txresponse.deliverBody(_ResponseReader(d, txresponse, file_name))
        return d

    def download_result(self, response):
        pass


```
# Scrapy源码剖析
## Scrapy源码剖析-twisted
```
# 事件循环(就像select，epoll，终止条件：所有的socket都已经移除)
from twisted.internet import reactor
# socket对象(用来创建socket对象)(如果下载完成，自动从事件循环中移除)
from twisted.web.client import getPage
from twisted.internet import defer  # defer.Deferred 特殊的socket对象  （不发请求，手动移除）

# 1. 利用getPage创建socket
# 2. 将socket添加到事件循环中
# 3. 开始事件循环（内部发送请求，并接受相应，当所有的socket请求完成后，终止事件循环）


def response(response):
    print(response)


@defer.inlineCallbacks  # 2. 将socket添加到事件循环中
def task():
    url = 'http://www.baidu.com'

    d = getPage(url=url.encode('utf-8'))  # 1. 利用getPage创建socket
    d.addCallback(response)
    yield d


task()
reactor.run()  # 3. 开始事件循环
```

