# web框架
    web框架顾名思义，用于开发web应用重新的框架
# python中web框架对比

>- django 是一个庞大框架，内部内置了很多的组件，ORM,FORM,MODELFORM,缓存session，中间件,缓存，认证，Admin, 信号等.....
>- Flask: 是一个精简的框架，只提供了基础的web框架信息， 但是有超多的第三方扩展可以随意搭配
>- Tornado： 性能优异，异步非阻塞，
>- bottle: 轻量级一千多行代码


# django请求生命周期
1. 用户访问进来先经过wsgi
2. 让后经过django中间件的proses_requests
3. 当经过中间件后到路由系统进行路由匹配
4. 让后执行路由对应的视图函数,
5. 在视图函数中利用orm获取数据库数据
6. 让后从模板路径拿到对应的模板进行渲染，
7. 渲染完成后变成字符串，经过所有中间件的process_response方法后交给wsgi
8. wsgi交给客户端

# 什么是wsgi？
    是web服务网关接口，是一套协议规范。
- 实现此吸引的有：
    - wsgiref
    - werkzurg
    - uwsgi

- 以上模块本质就是实现了socket监听请求，获取请求后将数据封装交给web框架处理

# django中间件
- 作用： 对所有的http请求进行批量处理，在试图函数执行之前和执行后自定义一些操作
- 应用: cors跨域/用户登陆/权限校验/CSRF/session/缓存

- CSRF本质
     1. 用户先发送GET请求获取scrf torken, 放在前端Form表单中生成一个隐藏的标签 和cookies中
     2. 提交表单发送post请求时候，需要写道之前发给用户的scrf token
     3. scrf 是在CsrfViewMiddleware中间件的 process_view方法进行校验
    