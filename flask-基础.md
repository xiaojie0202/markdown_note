# flask
```
    Flask是一个基于Python开发并且依赖jinja2模板和Werkzeug WSGI服务的一个微型框架，对于Werkzeug本质是Socket服务端，其用于接收http请求并对请求进行预处理，然后触发Flask框架，开发人员基于Flask框架提供的功能对请求进行相应的处理，并返回给用户，如果要返回给用户复杂的内容时，需要借助jinja2模板来实现对模板的处理，即：将模板和数据进行渲染，将渲染后的字符串返回给用户浏览器。

“微”(micro) 并不表示你需要把整个 Web 应用塞进单个 Python 文件（虽然确实可以 ），也不意味着 Flask 在功能上有所欠缺。微框架中的“微”意味着 Flask 旨在保持核心简单而易于扩展。Flask 不会替你做出太多决策——比如使用何种数据库。而那些 Flask 所选择的——比如使用何种模板引擎——则很容易替换。除此之外的一切都由可由你掌握。如此，Flask 可以与您珠联璧合。

默认情况下，Flask 不包含数据库抽象层、表单验证，或是其它任何已有多种库可以胜任的功能。然而，Flask 支持用扩展来给应用添加这些功能，如同是 Flask 本身实现的一样。众多的扩展提供了数据库集成、表单验证、上传处理、各种各样的开放认证技术等功能。Flask 也许是“微小”的，但它已准备好在需求繁杂的生产环境中投入使用。
```
## 基本实例
```
from flask import Flask

app = Flask(__name__) # 参数是应用模块或者包的名称


@app.route('/')
def index():
    return 'hello world'


if __name__ == '__main__':
    app.run(host='127.0.0.1', port=8080, debug=True) # 启用调试支持,修改代码后服务器自动重启
```
## flask-路由系统
    flask的路由系统是通过'flask.Flask'的实列的'route()'方法进行装饰的
>- route(url, method) # url 是匹配的url，method是运行请求的方法，例：@app.route('/', method=['GET'])
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
#### HTTP 方法
    （也经常被叫做“谓词”）告知服务器，客户端想对请求的页面 做 些什么。下面的都是非常常见的方法：
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

## flask-模板
    Flask 配备了 Jinja2 模板引擎。
### 模板渲染
    在flask中渲染模板使用`flask.render_template`函数, 存放模板的路径默认是‘templates’，如果想修改可在Flask初始化的时候修改'template_folder'参数
>- render_template(template_name_or_list, **context)
>>- template_name_or_list: 模板的名字，或者一个模板的迭代器
>>- **context： 上下文 name=xiaojie，在模板中即可实现{{name}}
### 模板语法
    跟django模板语法类似
>- 继承
```
{% extends 'base.html' %}
{% block title %}{% endblock %}
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
## 请求对象-request
    flask.request 封装了用户请求的所有数据
### request详解
>- form
>>- 一个包含解析过的从 POST 或 PUT 请求发送的表单对象的 MultiDict 。请注意上传的文件不会在这里，而是在 files 属性中。

>- args
>>- 一个包含解析过的查询字符串（ URL 中问号后的部分）内容的 MultiDict 。

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

>- environ
>>- 底层的 WSGI 环境。

>- method
>>- 当前请求的 HTTP 方法 (POST ， GET 等等)
```
如果用户请求下面的 URL:
http://www.example.com/myapplication/page.html?x=y
这个情况下，request.*属性的值会为如下:

path	/page.html
script_root	/myapplication
base_url	http://www.example.com/myapplication/page.html
url	http://www.example.com/myapplication/page.html?x=y
url_root	http://www.example.com/myapplication/
```
## 响应对象
    当服务器给用户响应数据的时候则需要用到响应对象 flask.response
    给用户返回数据的时候需要用flask.make_response函数对返回数据进行包装成Response对象
### response详解
>- headers
>>- Headers 对象表示响应的标头。

>- status
>>- 字符串表示的响应状态。

>- status_code¶
>>- 整数表示的响应状态。

>- mimetype
>>- The mimetype (content type without charset etc.)

>- set_cookie(key, value='', max_age=None, expires=None, path='/', domain=None, secure=None, httponly=False)
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
    在flask中url默认别名就是对应的views函数名，反向解析的时候使用url_for函数即可
>- flask.url_for(endpoint, **values)
>>- endpoint: url的别名，也就是url对应的函数名
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

@app.route('/any/<any("xiaojie", "hello"):page>')
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
```
from flask import Flask, render_template, url_for, redirect, session, request, escape

app = Flask(__name__)
app.secret_key = 'A0Zr98j/3yX R~XHH!jmN]LWX/,?RT' # 要使用会话，你需要设置一个密钥。


@app.route('/')
def index():
    if 'username' in session:
        return 'Logged in as %s' % escape(session['username']) # escape用来转意，解码
    return 'You are not logged in'


@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        session['username'] = request.form['username'] # 设置session
        return redirect(url_for('index'))
    return '''
        <form action="" method="post">
            <p><input type=text name=username>
            <p><input type=submit value=Login>
        </form>
    '''

@app.route('/logout')
def logout():
    # remove the username from the session if it's there
    session.pop('username', None)   # 删除session
    return redirect(url_for('index'))


if __name__ == '__main__':
    app.run(host='127.0.0.1', port=8080, debug=True)

```


