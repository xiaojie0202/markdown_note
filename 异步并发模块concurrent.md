# python异步并发模块concurrent.futures
用于并发处理，提供了多线程和多进程的并发功能
Python标准库为我们提供了threading和multiprocessing模块编写相应的多线程/多进程代码。从Python3.2开始，标准库为我们提供了concurrent.futures模块，它提供了ThreadPoolExecutor和ProcessPoolExecutor两个类，实现了对threading和multiprocessing的更高级的抽象，对编写线程池/进程池提供了直接的支持。
concurrent.futures基础模块是executor和future。

## Executor对象
>Executor是一个抽象类，它不能被直接使用。它为具体的异步执行定义了一些基本的方法。
ThreadPoolExecutor和ProcessPoolExecutor继承了Executor，分别被用来创建线程池和进程池的代码。

### 使用：
>- from concurrent.futures import ThreadPoolExecutor, ProcessPoolExecutor
>- pool = ThreadPoolExecutor(10) or ProcessPoolExecutor(10)
>- pool.submit(func, *arg, *kwargs) # 提交一个可执行的回调task，并返回一个future实例
>- pool.shutdown(wait=True) # 等待线程执行完毕，关闭线程池所有线程


>- pool.map(func, *iterables, timeout=None)
>- 此map函数和python自带的map函数功能类似，只不过concurrent模块的map函数从迭代器获得参数后异步执行。并且，每一个异步操作，能用timeout参数来设置超时时间，timeout的值可以是int或float型，如果操作timeout的话，会raisesTimeoutError。如果timeout参数不指定的话，则不设置超时间。
>>- func：为需要异步执行的函数
>>- iterables：可以是一个能迭代的对象，例如列表等。每一次func执行，会从iterables中取参数。
>>- timeout：设置每次异步操作的超时时间

## Future对象
>- Future对象是Executor对象 的 submit（）方法获取的

### 使用
>- future = pool.submit(func, *arg, *kwargs) # 提交一个可执行的回调task，并返回一个future实例
>>- future.cancel() # 终止某个线程和进程的任务，返回状态为 True False
>>- future.cancelled() # 判断是否真的结束了任务。
>>- future.running() # 判断是否还在运行
>>- future.done() #判断是否正常执行完毕的。
>>- future.result(timeout=None)() #返回这个进程执行函数的返回值，
>>- future.add_done_callback(fn) # 当前线程执行完毕的时候执行fn这个函数，并把self传递给回掉函数


>- concurrent.futures.wait(fs, timeout=None, return_when=ALL_COMPLETED)
>- wait方法接会返回一个tuple(元组)，tuple中包含两个set(集合)，一个是completed(已完成的)另外一个是uncompleted(未完成的)。
>>- fs:Future对象组成的迭代器
>>- timeout: timeout控制wait()方法返回前等待的时间。
>>- return_when: 决定方法什么时间点返回 如果采用默认的ALL_COMPLETED，程序会阻塞直到线程池里面的所有任务都完成；如果采用FIRST_COMPLETED参数，程序并不会等到线程池里面所有的任务都完成。

## 实例
```

from concurrent.futures import ThreadPoolExecutor
import requests

URLS = ['http://www.baidu.com', 'http://qq.com', 'http://sina.com'] # 爬虫需要爬的网站列表

# 定义函数用于获取网站数据
def task(url, timeout=10):
    return requests.get(url, timeout=timeout)


pool = ThreadPoolExecutor(max_workers=3) # 创建一个线程池。最大线程3
results = pool.map(task, URLS) # [pool.submit(task,'http://www.baidu.com'),pool.submit(task,'http://qq.com'),pool.submit(task,'http://sina.com')]

for ret in results:
    print('%s, %s' % (ret.url, len(ret.content)))

```
## 实列，线程池自动抽屉全站点赞
```
import requests
from bs4 import BeautifulSoup
import queue
from concurrent.futures import ThreadPoolExecutor, wait


class Chouti(object):

    def __init__(self, phone, password):

        self._login_post_date = {
            'phone': phone,
            'password': password,
            'oneMonth': 1}
        self._headers = {
            'Origin': 'http://dig.chouti.com',
            'Referer': 'http://dig.chouti.com/',
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 UBrowser/6.2.3964.2 Safari/537.36'
        }
        self.session = requests.Session()
        self.links_id = queue.Queue()

    def login_chouti(self):
        self.session.get(url='http://dig.chouti.com/', headers=self._headers)
        respones = self.session.post(url='http://dig.chouti.com/login', data=self._login_post_date, headers=self._headers, )  # 请求登录cookie
        print(respones.text)

    def get_chouti_html(self, page):
        respones = self.session.get(url='http://dig.chouti.com/all/hot/recent/%s' % page, headers=self._headers)
        return respones

    def handel_chouti_html(self, future):
        respones = future.result()
        future.links_id = []
        soup = BeautifulSoup(respones.text, 'lxml')
        news_div = soup.find(id='content-list')
        news_list = news_div.find_all('div', attrs={'class': 'item'})
        for i in news_list:
            news_id = i.find('i')
            future.links_id.append(news_id.text)

    def recommend(self, future):
        for i in future.links_id:
            text = self.session.post(url='http://dig.chouti.com/link/vote?linksId=%s' % i, headers=self._headers)
            print(text.text)

    def cancel_recommend(self, futrre):
        for i in futrre.links_id:
            text = self.session.post(url='http://dig.chouti.com/vote/cancel/vote.do', data={'linksId': i}, headers=self._headers)
            print(text.text)

    # 终极版本，使用Session 管理cookie
    def start_v2(self):
        self.login_chouti()
        pool = ThreadPoolExecutor(60)
        result = [pool.submit(self.get_chouti_html, i) for i in range(1, 121)]
        for i in result:
            i.add_done_callback(self.handel_chouti_html)
            i.add_done_callback(self.cancel_recommend)

        wait(result)
        print('操作完毕')


if __name__ == '__main__':
    c = Chouti(phone='8617612226296', password='wangyaojie150300')
    c.start_v2()

```
