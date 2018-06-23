# flask
```
    Flask是一个基于Python开发并且依赖jinja2模板和Werkzeug WSGI服务的一个微型框架，对于Werkzeug本质是Socket服务端，其用于接收http请求并对请求进行预处理，然后触发Flask框架，开发人员基于Flask框架提供的功能对请求进行相应的处理，并返回给用户，如果要返回给用户复杂的内容时，需要借助jinja2模板来实现对模板的处理，即：将模板和数据进行渲染，将渲染后的字符串返回给用户浏览器。

“微”(micro) 并不表示你需要把整个 Web 应用塞进单个 Python 文件（虽然确实可以 ），也不意味着 Flask 在功能上有所欠缺。微框架中的“微”意味着 Flask 旨在保持核心简单而易于扩展。Flask 不会替你做出太多决策——比如使用何种数据库。而那些 Flask 所选择的——比如使用何种模板引擎——则很容易替换。除此之外的一切都由可由你掌握。如此，Flask 可以与您珠联璧合。

默认情况下，Flask 不包含数据库抽象层、表单验证，或是其它任何已有多种库可以胜任的功能。然而，Flask 支持用扩展来给应用添加这些功能，如同是 Flask 本身实现的一样。众多的扩展提供了数据库集成、表单验证、上传处理、各种各样的开放认证技术等功能。Flask 也许是“微小”的，但它已准备好在需求繁杂的生产环境中投入使用。
```
## 基本实例
```python
from flask import Flask

# 实例化Flask对象
app = Flask(__name__) # 参数是应用模块或者包的名称

# 将url'/' 与函数 index() 函数建立对应关系并提那就到路由中
@app.route('/')
def index():
    return 'hello world'


if __name__ == '__main__':
    # 监听用户请求
    # 如果樱花请求到来则执行app对象的__call__方法
    app.run(host='127.0.0.1', port=8080, debug=True) # 启用调试支持,修改代码后服务器自动重启
```

## flask-路由系统
    flask的路由系统是通过'flask.Flask'的实列的'route()'方法进行装饰的
- route(url, method, endpoint)
>- url 是匹配的url，
>- method是运行请求的方法，
>- endpoint 是url别名，用于方向解析
>>- @app.route('/', method=['GET'], endpoint='index')
### route方法源码剖析
    route装饰器实际就是调用 app.add_url_rule注册路由信息的
```
    def route(self, rule, **options):
        def decorator(f):
            endpoint = options.pop('endpoint', None)
            self.add_url_rule(rule, endpoint, f, **options)
            return f
        return decorator
```

- add_url_rule参数详解
>- add_url_rule(rule, endpoint=None, view_func=None, provide_automatic_options=None, **options)
>>- rule: url
>>- endpoint: url别名， 默认不写为view_func函数名称
>>- view_func: 视图函数
>>- defaults=None,              默认值,当URL中无参数，函数需要参数时，使用defaults={'k':'v'}为函数提供参数
>>- **options

>>>- methods=None # 允许的请求方式['GET','POST' ...]

>>>- strict_slashes=None,        对URL最后的 / 符号是否严格要求
    
    ```
    @app.route('/index',strict_slashes=False)，
        访问 http://www.xx.com/index/ 或 http://www.xx.com/index均可
    @app.route('/index',strict_slashes=True)
        仅访问 http://www.xx.com/index 
    ```
>>>- redirect_to=None #重定向到指定地址
    
    ```
     @app.route('/index/<int:nid>', redirect_to='/home/<nid>')
    或
    def func(adapter, nid):
        return "/home/888"
    @app.route('/index/<int:nid>', redirect_to=func)
    ```

>>>- subdomain=None,   子域名访问
    
    ```
    from flask import Flask, views, url_for

    app = Flask(import_name=__name__)
    app.config['SERVER_NAME'] = 'xiaojie.com:5000'

    @app.route("/", subdomain="admin")
    def static_index():
        """Flask supports static subdomains
        This is available at static.your-domain.tld"""
        return "static.your-domain.tld"

    @app.route("/dynamic", subdomain="<username>")
    def username_index(username):
        """Dynamic subdomains are also supported
        Try going to user1.your-domain.tld/dynamic"""
        return username + ".your-domain.tld"

    if __name__ == '__main__':
        app.run()
    ```
    
    

- 实例：使用add_url_rule方法注册路由表
```
def logout():
    session.pop('user', None)
    return redirect(url_for('login'))
app.add_url_rule(rule='/logout', endpoint='logout', view_func=logout, methods=['GET'])
```
### 静态路由
    ```
    #顾名思义，就是匹配完成的url
    @app.route('/hello')
    def hello():
        return 'Hello World'
    ```
### 动态路由
    flask 不支持正则表达式路由

>- 动态路由就是url携带参数的路由
```
@app.route('/show/<username>')
def show_info(username):
    return username
```

>- flask内置了转换器，可以把url转换成指定的数据类型
```
@app.route('/post/<int:post_id>')
def show_post(post_id):
    # post_id参数传递过来直接是 int 类型
    return 'Post %d' % post_id
```

 >- flask支持的转换器
```
int	接受整数
float	同 int ，但是接受浮点数
path	和默认的相似，但也接受斜线
```

>- flask指定url可以传递的字符串
```
@app.route('/any/<any("xiaojie", "hello"):page>')
def any(page):
    return page
```

### 请求方法路由

`HTTP （与 Web 应用会话的协议）有许多不同的访问 URL 方法。默认情况下，路由只回应 GET 请求，但是通过 route() 装饰器传递 methods 参数可以改变这个行为。`


```
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        do_the_login()
    else:
        show_the_login_form()
```
- 请求方法
>- GET
>>- 浏览器告知服务器：只 获取 页面上的信息并发给我。这是最常用的方法。
>- HEAD
>>- 浏览器告诉服务器：欲获取信息，但是只关心 消息头 。应用应像处理 GET 请求一样来处理它，但是不分发实际内容。在 Flask 中你完全无需 人工 干预，底层的 Werkzeug 库已经替你打点好了。
>- POST
>>- 浏览器告诉服务器：想在 URL 上 发布 新信息。并且，服务器必须确保 数据已存储且仅存储一次。这是 HTML 表单通常发送数据到服务器的方法。
>- PUT
>>- 类似 POST 但是服务器可能触发了存储过程多次，多次覆盖掉旧值。你可 能会问这有什么用，当然这是有原因的。考虑到传输中连接可能会丢失，在 这种 情况下浏览器和服务器之间的系统可能安全地第二次接收请求，而 不破坏其它东西。因为 POST 它只触发一次，所以用 POST 是不可能的。
>- DELETE
>>- 删除给定位置的信息。
>- OPTIONS
>>- 给客户端提供一个敏捷的途径来弄清这个 URL 支持哪些 HTTP 方法。 从 Flask 0.6 开始，实现了自动处理。
### flask路由-CBV
    django中指出CBV方式，在Flask中当然可以
- CBV实例
```
# 判断是否登陆装饰器
def auth_login(func):
    def werrp(*args, **kwargs):
        if session.get('user', None):
            return func(*args, **kwargs)
        else:
            return redirect(url_for('login'))
    return werrp

#################CBV##########################################
class Index(views.MethodView):
    methods = ['GET']
    decorators = [auth_login, ]  # 相当于函数装饰器,因为内部as_view类方法会把此类封装成函数

    def get(self):
        return 'Index.GET'

    def post(self):
        return 'Index.POST'

app.add_url_rule('/', view_func=Index.as_view(name='index'))


################FBV#########################################
@app.route('/', endpoint='index')
@auth_login
def index():
    return 'Hello 小杰!'
```
>- as_view类方法剖析
>>- as_view方法就是把类封装成函数
```
@classmethod
    def as_view(cls, name, *class_args, **class_kwargs):

        def view(*args, **kwargs):
            self = view.view_class(*class_args, **class_kwargs)
            return self.dispatch_request(*args, **kwargs)

        if cls.decorators:
            view.__name__ = name
            view.__module__ = cls.__module__
            for decorator in cls.decorators:
                view = decorator(view)

        view.view_class = cls
        view.__name__ = name
        view.__doc__ = cls.__doc__
        view.__module__ = cls.__module__
        view.methods = cls.methods
        view.provide_automatic_options = cls.provide_automatic_options
        return view


```
>- CBV 执行流程分析
1. if cls.decorators: 先把CBV decorators 的函数装饰到CBV上
2. view.view_class = cls 给view函数赋值属性view_class 等于 CBV的类
3. view.__name__ = name  # 函数名重新改名为name参数传递的str类型名称
4.  view.__doc__ = cls.__doc__  # 函数的文档等于CBV类中写的文档
5. view.__module__ = cls.__module__  # 函数的模块名称等于CBV所在的模块名称
6. return view # 返回封装好的函数返回并注册到路由系统中
-------------------------------------------------------------------------
7. 当有用户请求访问到来的时候会执行as_view里封装的view函数
```

    def view(*args, **kwargs):
            self = view.view_class(*class_args, **class_kwargs)
            return self.dispatch_request(*args, **kwargs)
```

7.1. self = view.view_class(*class_args, **class_kwargs) # 相当于实例化CBV类

7.2. self.dispatch_request(*args, **kwargs) # 执行CBV类的dispatch_request方法
```    
        def dispatch_request(self, *args, **kwargs):
            meth = getattr(self, request.method.lower(), None)  # 反射获取该类的对应 请求方法的的函数
    
            if meth is None and request.method == 'HEAD': 
                meth = getattr(self, 'get', None)
    
            assert meth is not None, 'Unimplemented method %r' % request.method
            return meth(*args, **kwargs) # 执行CBV类的请求方法函数返回结果给客户端
```
### flask路由扩展-支持正则的路由
 ```
 from werkzeug.routing import BaseConverter
 class RegexConverter(BaseConverter):
    """
    自定义URL匹配正则表达式
    """
    def __init__(self, map, regex):
        super(RegexConverter, self).__init__(map)
        self.regex = regex

    def to_python(self, value):
        """
        路由匹配时，匹配成功后传递给视图函数中参数的值
        :param value: 
        :return: 
        """
        return int(value)

    def to_url(self, value):
        """
        使用url_for反向生成URL时，传递的参数经过该方法处理，返回的值用于生成URL中的参数
        :param value: 
        :return: 
        """
        val = super(RegexConverter, self).to_url(value)
        return val

# 添加到flask中
app.url_map.converters['regex'] = RegexConverter


@app.route('/index/<regex("\d+"):nid>')
def index(nid):
    print(url_for('index', nid='888'))
    return 'Index'
 ```
## flask-模板
    Flask 配备了 Jinja2 模板引擎。
### 模板渲染
    在flask中渲染模板使用`flask.render_template`函数, 存放模板的路径默认是‘templates’，如果想修改可在Flask初始化的时候修改'template_folder'参数
>- render_template(template_name_or_list, **context)
>>- template_name_or_list: 模板的名字，或者一个模板的迭代器
>>- **context： 上下文 name=xiaojie，在模板中即可实现{{name}}
### 模板语法
    跟django模板语法类似
>- 模板继承
```
{% extends 'base.html' %}
{% block title %}{% endblock %}
```
>- 模板导入
```
{% include 'tp.html' %}
```
>- 宏定义
```
{% macro set_input(name, type='text', value='') %}
    <input type="{{ type }}" name="'{{ name }}" value="{{ value }}">
{% endmacro %}

{{ set_input('username') }}
```
>- 单值渲染
```
    {{变量名}}
```
>- 循环字典
```
<ul>
    {% for user in users %}
      <li><a href="{{ user.url }}">{{ user.username }}</a></li>
    {% endfor %}
</ul>
```
>- 判断
```
{% if request.method == 'POST' %}
        
    {% endif %}
```
>- 控制html是否转义（渲染）
```
{% autoescape false %}
    <p>autoescaping is disabled here
    <p>{{ will_not_be_escaped }}
{% endautoescape %}
```
>- 字符串标签进行html渲染
```
    {{ul | safe}}
```
>- 前端执行视图传递的函数
```
# views
def show_func():
    return '<div>小杰</div>'

@app.route('/')
def index():
    return render_template('index.html', func=show_func, name='小杰')

# 模板
{{func() | safe}}
```
>- 函数进行模板渲染
```python
from flask import Markup

def show_func():
    return Markup('<div>小杰</div>')

# index.html
{{show_func()}}

```
## 请求对象-request
    flask.request 封装了用户请求的所有数据
### request详解
>- form (post请求数据)
>>- 一个包含解析过的从 POST 或 PUT 请求发送的表单对象的 MultiDict 。请注意上传的文件不会在这里，而是在 files 属性中。
```
username = request.form.get('username')
password = request.form.get('password')
```
>- args (get请求数据)
>>- 一个包含解析过的查询字符串（ URL 中问号后的部分）内容的 MultiDict 。
```
user_id = request.form.get('id')
```
>- values
>>- 一个包含 form 和 args 全部内容的 CombinedMultiDict 。

>- cookies
>>- 一个包含请求中传送的所有 cookie 内容的 dict 。

>- stream
>>- 如果表单提交的数据没有以已知的 mimetype 编码，为性能考虑，数据会不经修改存储在这个流中。大多数情况下，使用可以把数据提供为字符串的 data 是更好的方法。流只返回一次数据。

>- headers
>>- 进入请求的标头存为一个类似字典的对象。

>- data
>>- 如果进入的请求数据是 Flask 不能处理的 mimetype ，数据将作为字符串存于此。

>- files
>>- 一个包含 POST 和 PUT 请求中上传的文件的 MultiDict 。每个文件存储为 FileStorage 对象。其基本的行为类似你在 Python 中见到的标准文件对象，差异在于这个对象有一个 save() 方法可以把文件存储到文件系统上。
```
obj = request.files['file_name']
obj.save('/vat/www/upload' + securs_filename(f.filename) # 重命名文件防止重复)
```
>- environ
>>- 底层的 WSGI 环境。

>- method
>>- 当前请求的 HTTP 方法 (POST ， GET 等等)
```
如果用户请求下面的 URL:
http://www.example.com/myapplication/page.html?x=y
这个情况下，request.*属性的值会为如下:

request.path	/page.html
request.script_root	/myapplication
request.base_url	http://www.example.com/myapplication/page.html
request.url	http://www.example.com/myapplication/page.html?x=y
request.url_root	http://www.example.com/myapplication/

```
## 响应对象
    当服务器给用户响应数据的时候则需要用到响应对象 flask.response
    给用户返回数据的时候需要用flask.make_response函数对返回数据进行包装成Response对象
### 响应
- return '字符串'
- return redirect('/index.html'')
- return render_template('模板'')
- return jsonify({k:v}) 
### response详解

>- 通过flask.mack_response 获取 response对象
>>- response = mack_response(render_template('index.html'))

>- response.headers 表示响应头
>>- response.headers['X-Something'] = 'xxxxx'

>- response.status
>>- 字符串表示的响应状态。

>- response.status_code¶
>>- 整数表示的响应状态。

>- response.mimetype
>>- The mimetype (content type without charset etc.)

>- response.set_cookie(key, value='', max_age=None, expires=None, path='/', domain=None, secure=None, httponly=False)
>>- Sets a cookie. The parameters are the same as in the cookie Morsel object in the Python standard library but it accepts unicode data, too.
>>- key – the key (name) of the cookie to be set.
>>- value – the value of the cookie.
>>- max_age – should be a number of seconds, or None (default) if the cookie should last only as long as the client’s browser session.
>>- expires – should be a datetime object or UNIX timestamp.
>>- domain – if you want to set a cross-domain cookie. For example, domain=".example.com" will set a cookie that is readable by the domain www.example.com, foo.example.com etc. Otherwise, a cookie will only be readable by the domain that set it.
>>- path – limits the cookie to a given path, per default it will span the whole domain.
### response实例-设置cookie
```
@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```
## flask重定向和错误
    在flask中重定向使用redirect函数跳转到执行url，
### flask重定向前戏-url反向解析
    在flask中url默认别名就是对应的views函数名，如果在注册路由的时候传递了endpoint参数，则别名为endpoint参数, 反向解析的时候使用url_for函数即可
>- flask.url_for(endpoint, **values)
>>- endpoint: url的别名
>>- values： url对应的函数名需要传递的的参数
### flask重定向-redirecv
>- redirect(location, code=302, Response=None)
>>- location: url
>>- code:状态码
>>- Response: Response对象
### flask重定向实例
```
@app.route('/post/<int:post_id>')
def show_post(post_id):
    # post_id参数传递过来直接是 int 类型
    url = url_for('any', page='xiaojie')
    return redirect(url)

@app.route('/any/<any("xiaojie", "hello"):page>', endpoint='any')
def any(page):
    return page
```
### flask错误页面指定：
    在flask中指定错误页面使用errorhandler装饰器
>- errorhandler(code_or_exception)
>>- code_or_exception： 参数可以是HTTP的错误状态码， 也可是是一个自定义异常
```
@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404
```
## Session
    除请求对象之外，还有一个 session 对象。它允许你在不同请求间存储特定用户的信息。它是在 Cookies 的基础上实现的，并且对 Cookies 进行密钥签名。这意味着用户可以查看你 Cookie 的内容，但却不能修改它，除非用户知道签名的密钥。
### Session的使用
>1. 设置一个密匙用来加密session
>>- app.secret_key = 'A0Zr98j/3yX R~XHH!jmN]LWX/,?RT'
>2. 给客户端设置session
>>- session['username'] = "xiaojie"
>3. 获取客户端携带的session信息
>>- session.get('username', None)
>4. 删除客户端session
>>- session.pop('username', None)
### Session实例
```python
from flask import Flask, render_template, request, redirect, url_for, session

app = Flask(__name__)
app.secret_key = 'asdaskldjklash'


# 判断是否登陆装饰器
def auth_login(func):
    def werrp(*args, **kwargs):
        if session.get('user', None):
            return func(*args, **kwargs)
        else:
            return redirect(url_for('login'))
    return werrp


@app.route('/', endpoint='index')
@auth_login
def index():
    return 'Hello 小杰!'


@app.route('/login', methods=['GET', 'POST'], endpoint='login')
def login():
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        if username == 'xiaojie' and password == 'xiaojie':
            session['user'] = username
            return redirect(url_for('index'))
        else:
            return  render_template('login.html', erro='用户名或密码错误')


@app.route('/logout', methods=['GET'], endpoint='logout')
def logout():
    session.pop('user', None)
    return redirect(url_for('login'))

if __name__ == '__main__':
    app.run()

```

## Flask配置文件
    flask中的配置文件是一个flask.config.Config对象（继承字典）,
    
- 默认配置为：
``` 
{
    'DEBUG':                                get_debug_flag(default=False),  是否开启Debug模式
    'TESTING':                              False,                          是否开启测试模式
    'PROPAGATE_EXCEPTIONS':                 None,                          
    'PRESERVE_CONTEXT_ON_EXCEPTION':        None,
    'SECRET_KEY':                           None,
    'PERMANENT_SESSION_LIFETIME':           timedelta(days=31),
    'USE_X_SENDFILE':                       False,
    'LOGGER_NAME':                          None,
    'LOGGER_HANDLER_POLICY':               'always',
    'SERVER_NAME':                          None,
    'APPLICATION_ROOT':                     None,
    'SESSION_COOKIE_NAME':                  'session',
    'SESSION_COOKIE_DOMAIN':                None,
    'SESSION_COOKIE_PATH':                  None,
    'SESSION_COOKIE_HTTPONLY':              True,
    'SESSION_COOKIE_SECURE':                False,
    'SESSION_REFRESH_EACH_REQUEST':         True,
    'MAX_CONTENT_LENGTH':                   None,
    'SEND_FILE_MAX_AGE_DEFAULT':            timedelta(hours=12),
    'TRAP_BAD_REQUEST_ERRORS':              False,
    'TRAP_HTTP_EXCEPTIONS':                 False,
    'EXPLAIN_TEMPLATE_LOADING':             False,
    'PREFERRED_URL_SCHEME':                 'http',
    'JSON_AS_ASCII':                        True,
    'JSON_SORT_KEYS':                       True,
    'JSONIFY_PRETTYPRINT_REGULAR':          True,
    'JSONIFY_MIMETYPE':                     'application/json',
    'TEMPLATES_AUTO_RELOAD':                None,
}
```
- 加载配置文件方式
```
方式一：
    app.config['DEBUG'] = True
 
    PS： 由于Config对象本质上是字典，所以还可以使用app.config.update(...)
 
方式二：
    app.config.from_pyfile("python文件名称")
        如：
            settings.py
                DEBUG = True
 
            app.config.from_pyfile("settings.py")
 
    app.config.from_envvar("环境变量名称")
        环境变量的值为python文件名称名称，内部调用from_pyfile方法
 
 
    app.config.from_json("json文件名称")
        JSON文件名称，必须是json格式，因为内部会执行json.loads
 
    app.config.from_mapping({'DEBUG':True})
        字典格式
 
    app.config.from_object("python类或类的路径")
 
        app.config.from_object('pro_flask.settings.TestingConfig')
 
        settings.py
 
            class Config(object):
                DEBUG = False
                TESTING = False
                DATABASE_URI = 'sqlite://:memory:'
 
            class ProductionConfig(Config):
                DATABASE_URI = 'mysql://user@localhost/foo'
 
            class DevelopmentConfig(Config):
                DEBUG = True
 
            class TestingConfig(Config):
                TESTING = True
 
        PS: 从sys.path中已经存在路径开始写
     
 
    PS: settings.py文件默认路径要放在程序root_path目录，如果instance_relative_config为True，则就是instance_path目录
    ```
    
```
