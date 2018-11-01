# Flask-闪现
    设置值，取一次就没有了,底层是利用session实现的，访问一次设置信息，让后pop出来
>- 作用
```
在一个页面发生错误了可以给用户重定向到错误信息页面，并显示错误信息，
```
>- 代码实现
```python
from flask import Flask, flash, get_flashed_messages

app = Flask(__name__)
app.secret_key = 'asdaskldjklash'


@app.route('/get', methods=['GET'])
def get():
    data = get_flashed_messages(category_filter=['erro_log'])
    # category_filter 参数表示只取指定分类的数据
    if data:
        return str(data)
    return '木有设置值'


@app.route('/set/<value>', methods=['GET'])
def set(value='别瞎设置值啊'):
    flash(value,category='erro_log')
    flash(value + 'info',category='info_log')
    # category 参数对数据进行分类，
    
    return '俺设置了个值：%s' % value


if __name__ == '__main__':
    app.run()
```
# Flask-请求扩展
    请求扩展，类似于django中间件，在请求到来之前（app.before.request），和之后(app.after.response)做操作
1. before.requests
```python

@app.before_request # 装饰器，在请求到来时候先执行此before_request装饰的函数
def process_request(*args, **kwargs):
    '''判断如果用户是否登陆，如果登陆则允许访问，如果没有登陆则跳转到用户登陆页面'''
    if request.path is not '/login':
        if not session.get('is_login', False):
            return redirect(url_for(endpoint='login'))
```
2. after_request
````python
@app.after_request # 在给客户端返回数据时候经过after_request装饰的函数
def process_response(response, *args, **kwargs):
    print('给客户端返回数据执行此函数')
    return response
````
3.  before_request, after_request 执行顺序

```
当在app中有多个before_request, after_request方法的时候，访问到来会先经过代码上边写的before_request依次往下执行所有的before_request方法
当给用户返回数据的时候会先执行代码底部的after_request方法依次向上执行所有
```

4. before_first_request,

```
# 当客户端第一次访问的时候执行的函数
@app.before_first_request
def fiest_request(*args, **kwargs):
    pass

```
# Flask-中间件
```python
class Middleware(object):

    def __init__(self, old_wsgi):
        self.old_wsgi = old_wsgi

    def __call__(self, environ, start_response):
        print('开始之前')
        ret = self.old_wsgi(environ, start_response)
        print('结束之后')
        return ret


if __name__ == '__main__':
    app.wsgi_app = Middleware(app.wsgi_app)
    app.run()
```
## Flask-蓝图
    蓝图的基本设想是它们记录注册到一个应用时的操作执行情况。 当从一个端点到另一端分发请求和生成 URL 时，Flask 关联视图函数和蓝图。
1. 创建一个蓝图
```python
from flask import Blueprint

simple_page = Blueprint('simple_page', __name__,
                        template_folder='templates', # 次蓝图使用的模板路径
                        static_folder='static',  # 此蓝图静态文件理解
                        url_prefix='/user') # 访问次蓝图的适合的url前缀

@simple_page.route('/', defaults={'page': 'index'})
@simple_page.route('/<page>')
def show(page):
    return 'Hello World'
```
2. 注册蓝图到APP
```python
from flask import Flask
from yourapplication.simple_page import simple_page

app = Flask(__name__)
app.register_blueprint(simple_page)
```
# Flask-信号

# http://www.pythondoc.com/
# Flask-SQLAlchmey（ORM）
# Flask-Dashed(相当于django-admin)
# Flask-WTF（WEB表单）
# Flask-Mail（邮件扩展）
# Flask-Login （用户登录）
# Flask 中使用 Celery
# flask-debugtoolbar（web页面显示调试信息的工具栏）
