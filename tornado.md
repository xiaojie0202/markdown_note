# tornade
    Tornado是使用Python编写的一个强大的、可扩展的Web服务器。它在处理严峻的网络流量时表现得足够强健，但却在创建和编写时有着足够的轻量级，并能够被用在大量的应用和工具中。

# Hello Tornado
```
import tornado.web
import tornado.ioloop


class MainHandler(tornado.web.RequestHandler):

    def get(self):
        self.write('Hello,Tornado')


# 1. 生成路由规则
application = tornado.web.Application([
    (r'/', MainHandler)
])

if __name__ == '__main__':
    # 2.. 创建socket对象监听8888端口
    #  将socket添加到select或者epool中
    application.listen(8888)
    # 3. 将select或者epool开始死循环
    tornado.ioloop.IOLoop.instance().start()
```
# Tornado-流程解析
## 1. 编写请求处理函数
    tornado.web.RequestHandler 是专门处理用户请求的函数，需要继承此函数

```
class MainHandler(tornado.web.RequestHandler):

    def get(self):
    def post()
```