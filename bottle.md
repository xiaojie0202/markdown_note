# bottle框架
    Bottle是一个快速、简洁、轻量级的基于WSIG的微型Web框架，此框架只由一个 .py 文件，除了Python的标准库外，其不依赖任何其他模块。
## 基本实列
```
from bottle import template, route

@route('/')
def index():
    return template('index.html')

if __name__ == '__main__':
    root.run(host='127.0.0.1', port=8000)


```
# bottle-路由系统
    路由系统，将不同的请求交给指定函数处理
>- 静态路由

```
@root.route('/index/')
def index():
    return template('index.html')
```
>- 动态路由
>>- url的参数为函数的参数

    ```
    @root.route('/index/<name>')
    def callback(name):  # 参数name == url传递的name
        ...
    ```
>>- 限制url传递参数的类型

    ```
    类型： int， float， path， re

    @route('/index/<id:int>')
    def callback(id): # 参数id为int类型
        ...

    @route('/showid/<num:int>')
    def show_name_id(num):
        print(id, type(id))
        return template('<p>{{name}}<p>', name=num)


    @route('/static/<path:path>')
    def static_files(path):
        return static_file(path, root='static')
    ```

>- 请求方法路由
```
@route('/acc/login', method=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return template('login.html')
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        if username == 'xiaojie' and password == 'xiaojie':
            return '登陆成功'


@get('/acc_login')
def acc_login():
    return template('login.html')


@post('/acc_login')
def auth_login():
    username = request.POST.get('username')
    password = request.POST.get('password')
    if username == 'xiaojie' and password == 'xiaojie':
        return '登陆成功'

```
>- 二级路由

# bottle-模板系统
    模板系统，将模板中的特殊语法渲染成字符串，值得一说的是Bottle的模板引擎可以任意指定：Bottle内置模板、mako、jinja2、cheetah
>1. 取单值
    `{{name}}`
>2. 循环取值
```
% for item in user_list:
    <li>{{item['id']}}--{{item['name']}}</li>
# end
```
>3. 在模板中声明变量
```% sl = 'xiaojie'   ```
>4. 在模板中定义python代码块
```
<%
    # A block of python code
    name = name.title().strip()
    if name == "Alex":
        name="seven"
%>
```
>5. 导入其他模板
```% include('page.html', name='xiaojie')```
>6. 在模板中执行函数
```
def custom():
    return '123123'

@route('/hello/')
def index():
    return template('hello_template.html', name='alex', func=custom)

# 模板中执行
 {{ func() }}
```
>7. 模板中的继承
```
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>{{title}}</title>
</head>
<body>
    <h1>自定义函数</h1>
    {{ !base }} # 相当于django的block

</body>
</html>
------------------------------------------------------------------------
% rebase('base.tpl', title='Page Title')
<p>Page Content ...</p>
```
# bottle-公共组件
    公共组件，用于提供处理请求相关的信息，如：表单数据、cookies、请求头等
## 公共组件-接受用户请求
    当框架接收到用户请求之后，将请求信息封装在Bottle的request中，以供开发人员使用
```
request.headers
    请求头信息

request.query
    get请求信息

request.forms
    post请求信息

request.files
    上传文件信息

request.params
    get和post请求信息

request.GET
    get请求信息

request.POST
    post和上传信息

request.cookies
    cookie信息

request.environ
    环境相关相关
```
## 公共组件-响应相内容
```
response
response.status_line
    状态行

response.status_code
    状态码

response.headers
    响应头

response.charset
    编码

response.set_cookie
    在浏览器上设置cookie

response.delete_cookie
    在浏览器上删除cookie
```
# bottle-支持的WSGI服务
```
server_names = {
    'cgi': CGIServer,
    'flup': FlupFCGIServer,
    'wsgiref': WSGIRefServer,
    'waitress': WaitressServer,
    'cherrypy': CherryPyServer,
    'paste': PasteServer,
    'fapws3': FapwsServer,
    'tornado': TornadoServer,
    'gae': AppEngineServer,
    'twisted': TwistedServer,
    'diesel': DieselServer,
    'meinheld': MeinheldServer,
    'gunicorn': GunicornServer,
    'eventlet': EventletServer,
    'gevent': GeventServer,
    'geventSocketIO':GeventSocketIOServer,
    'rocket': RocketServer,
    'bjoern' : BjoernServer,
    'auto': AutoServer,
}
```

使用时，只需在主app执行run方法时指定参数即可：
```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
from bottle import Bottle
root = Bottle()

@root.route('/hello/')
def index():
    return "Hello World"
# 默认server ＝'wsgiref'
root.run(host='localhost', port=8080, server='wsgiref')
```
