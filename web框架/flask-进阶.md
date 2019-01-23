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
# Flask-蓝图
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
# Flask-Session
## 简介
flask-session是flask框架的session组件，由于原来flask内置session使用签名cookie保存，该组件则将支持session保存到多个地方，如：
- redis：保存数据的一种工具，五大类型。非关系型数据库
- memcached
- filesystem
- mongodb
- sqlalchmey：那数据存到数据库表里面

## 安装
>>- pip install flask-session
## 存储到redis
```python
import redis
from flask import Flask, session
from flask_session import Session

app = Flask(__name__)
app.debug = True
app.secret_key = 'xxxx'

app.config['SESSION_TYPE'] = 'redis'  # session类型为redis
app.config['SESSION_PERMANENT'] = False  # 如果设置为True，则关闭浏览器session就失效。
app.config['SESSION_USE_SIGNER'] = False  # 是否对发送到浏览器上session的cookie值进行加密
app.config['SESSION_KEY_PREFIX'] = 'session:'  # 保存到session中的值的前缀
app.config['SESSION_REDIS'] = redis.Redis(host='127.0.0.1', port='6379', password='123123')  # 用于连接redis的配置

Session(app)


@app.route('/index')
def index():
    session['k1'] = 'v1'
    return 'xx'


if __name__ == '__main__':
    app.run()
```
## 存储到memcached
```python
from flask import Flask, session
from flask_session import Session
import memcache

app = Flask(__name__)
app.debug = True
app.secret_key = 'xxxx'


app.config['SESSION_TYPE'] = 'memcached' # session类型为redis
app.config['SESSION_PERMANENT'] = True # 如果设置为True，则关闭浏览器session就失效。
app.config['SESSION_USE_SIGNER'] = False # 是否对发送到浏览器上session的cookie值进行加密
app.config['SESSION_KEY_PREFIX'] = 'session:' # 保存到session中的值的前缀
app.config['SESSION_MEMCACHED'] = memcache.Client(['10.211.55.4:12000'])


Session(app)


@app.route('/index')
def index():
    session['k1'] = 'v1'
    return 'xx'


if __name__ == '__main__':
    app.run()
```
## 存储到文件系统
```python
from flask import Flask, session
from flask_session import Session

app = Flask(__name__)
app.debug = True
app.secret_key = 'xxxx'

app.config['SESSION_TYPE'] = 'filesystem'  # session类型为文件系统
app.config['SESSION_FILE_DIR'] = '/Users/app/'  # 存储session的文件路径
app.config['SESSION_FILE_THRESHOLD'] = 500  # 存储session的个数如果大于这个值时，就要开始进行删除了
app.config['SESSION_FILE_MODE'] = 384  # 文件权限类型

app.config['SESSION_PERMANENT'] = True  # 如果设置为True，则关闭浏览器session就失效。
app.config['SESSION_USE_SIGNER'] = False  # 是否对发送到浏览器上session的cookie值进行加密
app.config['SESSION_KEY_PREFIX'] = 'session:'  # 保存到session中的值的前缀

Session(app)


@app.route('/index')
def index():
    session['k1'] = 'v1'
    session['k2'] = 'v1'
    return 'xx'


if __name__ == '__main__':
    app.run()
```
## 存储到mongodb
```python
from flask import Flask, session
from flask_session import Session
import pymongo

app = Flask(__name__)
app.debug = True
app.secret_key = 'xxxx'

app.config['SESSION_TYPE'] = 'mongodb'  # session类型为mongodb

app.config['SESSION_MONGODB'] = pymongo.MongoClient('47.93.4.198', 27017)
app.config['SESSION_MONGODB_DB'] = 'mongo的db名称（数据库名称）'
app.config['SESSION_MONGODB_COLLECT'] = 'mongo的collect名称（表名称）'

app.config['SESSION_PERMANENT'] = True  # 如果设置为True，则关闭浏览器session就失效。
app.config['SESSION_USE_SIGNER'] = False  # 是否对发送到浏览器上session的cookie值进行加密
app.config['SESSION_KEY_PREFIX'] = 'session:'  # 保存到session中的值的前缀

Session(app)


@app.route('/index')
def index():
    session['k1'] = 'v1'
    session['k2'] = 'v1'
    return 'xx'


if __name__ == '__main__':
    app.run()
```
## 存储到 sqlalchemy
```python
from flask import Flask, session
from flask_session import Session as FSession
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.debug = True
app.secret_key = 'xxxx'

# 设置数据库链接
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://root:123@127.0.0.1:3306/fssa?charset=utf8'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True

# 实例化SQLAlchemy
db = SQLAlchemy(app)



app.config['SESSION_TYPE'] = 'sqlalchemy'  # session类型为sqlalchemy
app.config['SESSION_SQLALCHEMY'] = db # SQLAlchemy对象
app.config['SESSION_SQLALCHEMY_TABLE'] = 'session' # session要保存的表名称
app.config['SESSION_PERMANENT'] = True  # 如果设置为True，则关闭浏览器session就失效。
app.config['SESSION_USE_SIGNER'] = False  # 是否对发送到浏览器上session的cookie值进行加密
app.config['SESSION_KEY_PREFIX'] = 'session:'  # 保存到session中的值的前缀
FSession(app)


@app.route('/index')
def index():

    session['k1'] = 'v1'
    session['k2'] = 'v1'

    return 'xx'


if __name__ == '__main__':
    app.run()
```
# WTForm
    https://www.cnblogs.com/wupeiqi/articles/8202357.html
# Flask-SQLAlchmey（ORM）
    数据库交互ORM
>- 安装
>>- pip install flask-sqlalchemey
## 在flask中使用
```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

# 初始化app
app = Flask(__name__)



# 配置数据库连接
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///foo.db'
# 连接池数量
app.config['SQLALCHEMY_POOL_SIZE'] = 10
# 数据库连接超时时间
app.config['SQLALCHEMY_POOL_TIMEOUT'] = 30
# 配置为Ture 在请求结束后自动commit
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True
"""
# 其他配置
app.config.setdefault('SQLALCHEMY_DATABASE_URI', 'sqlite:///:memory:')
app.config.setdefault('SQLALCHEMY_BINDS', None)
app.config.setdefault('SQLALCHEMY_NATIVE_UNICODE', None)
app.config.setdefault('SQLALCHEMY_ECHO', False)
app.config.setdefault('SQLALCHEMY_RECORD_QUERIES', None)
app.config.setdefault('SQLALCHEMY_POOL_SIZE', None)
app.config.setdefault('SQLALCHEMY_POOL_TIMEOUT', None)
app.config.setdefault('SQLALCHEMY_POOL_RECYCLE', None)
app.config.setdefault('SQLALCHEMY_MAX_OVERFLOW', None)
app.config.setdefault('SQLALCHEMY_COMMIT_ON_TEARDOWN', False)
"""
# 初始化sqlalchemy
db = SQLAlchemy(app)

# 创建模型类
class Users(db.Model):
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String, unique=True, nullable=False)
    email = db.Column(db.String, unique=True, nullable=False)
    password = db.Column(db.String, nullable=False)

    def __repr__(self):
        return '<Users %s>' % self.username

# 视图函数
@app.route('/<user_id>', methods=['GET'])
def index(user_id):
    # 查询
    user = Users.query.filter(Users.id==user_id).first()
    # 查询
    user = db.session.query(Users).filter(Users.id==user_id).first()
    # 删除当前用户
    db.session.delete(user)
    db.session.commit()
    return 'ok'

if __name__ == '__main__':
    app.run()
```

# Flask-信号

# http://www.pythondoc.com/

# Flask-Dashed(相当于django-admin)
# Flask-Mail（邮件扩展）
# Flask-Login （用户登录）
# Flask 中使用 Celery
# flask-debugtoolbar（web页面显示调试信息的工具栏）
