# 序列化
    序列化是为了把models对象进行序列化成json数据
>- models.py
```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=32)
    price = models.IntegerField()
    pub_date = models.DateField()
    publish = models.ForeignKey('Publish', on_delete=models.CASCADE)
    authors = models.ManyToManyField("Author")

    def __str__(self):
        return self.title


class Publish(models.Model):
    name = models.CharField(max_length=32)
    email = models.EmailField()

    def __str__(self):
        return self.name


class Author(models.Model):
    name = models.CharField(max_length=32)
    age = models.IntegerField()

    def __str__(self):
        return self.name
```
## django.core.serializers
>- django原生序列化对象
```
class Book(View):
    def get(self, request, *args, **kwargs):
        # 序列化1， 使用django.core.serializers 进行序列化
        book_obj = models.Book.objects.all()
        ret = serializers.serialize('json', book_obj)
        return HttpResponse(ret)
```
>- 序列化后的结果
```
[
    {
        "model":"markdowndemo.book",
        "pk":1,
        "fields":{
            "title":"射雕英雄传",
            "price":22,
            "pub_date":"2018-11-08",
            "publish":1,
            "authors":[
                2
            ]
        }
    },
    {
        "model":"markdowndemo.book",
        "pk":2,
        "fields":{
            "title":"盗墓笔记",
            "price":22,
            "pub_date":"2018-11-08",
            "publish":2,
            "authors":[
                3,
                4
            ]
        }
    }
]
```

## rest_framework.serializers.serializers
    专门为API接口提供的序列化模型的类，比django原生的序列化组件更强大
    
### 序列化单表
>- 创建序列化类
```python
from rest_framework import serializers
from . import models

class PublishSerializers(serializers.Serializer):
    name = serializers.CharField()
    email = serializers.EmailField()
```
>- 序列化对象简单使用
```python
from markdowndemo.views import PublishSerializers
from markdowndemo import models
publish_list = models.Publish.objects.all()
bs = PublishSerializers(publish_list, many=True)

bs.data  # 序列化叔家
# [OrderedDict([('name', '张三'), ('email', 'wang1418075087@qq.com')]), OrderedDict([('name', '李四'), ('email', 'lsi@qwe.com')])]

bs.instance # 取回序列化之前的QuerySet
# <QuerySet [<Publish: 张三>, <Publish: 李四>]>
```
>- 视图中使用
```python
from rest_framework.response import Response
from rest_framework.views import APIView

class PublishView(APIView):

    def get(self, request, *args, **kwargs):
        publish_list = models.Publish.objects.all()
        bs = PublishSerializers(publish_list, many=True)
        return Response(bs.data)
```
>- 序列化结果
```json
[
    {
        "name": "张三",
        "email": "wang1418075087@qq.com"
    },
    {
        "name": "李四",
        "email": "lsi@qwe.com"
    }
]
```
>- 单表序列化扩展-取跨表的Book的所有title
```
book_title = serializers.SerializerMethodField()
def get_book_title(self, obj):
    temp = []
    for book in obj.book_set.all():
        temp.append(book.title)
    return temp
```

### 序列化多表
>- 创建序列化类
```python
from . import models
from rest_framework import serializers


class BookSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=32)
    price = serializers.IntegerField()
    pub_date = serializers.DateField()
    # publish = serializers.CharField(source='publish.name')  # 一对多的时候使用source指定要查询的跨表字段
    # 跨表的时候用SerializerMethodField 序列化跨表的多个字段
    publish = serializers.SerializerMethodField()  

    def get_publish(self, obj):
        temp = [{'name': obj.publish.name, 'email': obj.publish.email}]
        return temp

    authors = serializers.SerializerMethodField()

    def get_authors(self, obj):
        temp = []
        for auther in obj.authors.all():
            temp.append({'name': auther.name, 'age': auther.age})
        return temp
```
>- 序列化结果
```json
[
    {
        "title": "射雕英雄传",
        "price": 22,
        "pub_date": "2018-11-08",
        "publish": [
            {
                "name": "张三",
                "email": "wang1418075087@qq.com"
            }
        ],
        "authors": [
            {
                "name": "李四",
                "age": 2
            }
        ]
    },
    {
        "title": "盗墓笔记",
        "price": 22,
        "pub_date": "2018-11-08",
        "publish": [
            {
                "name": "李四",
                "email": "lsi@qwe.com"
            }
        ],
        "authors": [
            {
                "name": "d1",
                "age": 22
            },
            {
                "name": "d2",
                "age": 22
            }
        ]
    }
]
```
>- 视图函数中的使用
```python
from rest_framework.response import Response
from rest_framework.views import APIView
from . import models

class BookView(APIView):

    def get(self, request, *args, **kwargs):
        book_obj = models.Book.objects.all()
        bs = BookSerializer(book_obj, many=True)
        return Response(bs.data)
```
## rest_framework.serializers.ModelSerializer
    专门提供models的序列化
>- 创建序列化类
```python
from rest_framework import serializers

class BookModelSerializer(serializers.ModelSerializer):
    class Meta:
        # 指定序列化的表
        model = models.Book
        # 序列化的字段， author_list是自定义字段
        fields = ['title', 'price', 'pub_date', 'publish', 'authors', 'author_list']
        # fields = '__all__'
        # 自定义错误信息
        error_messages = {'fields': {'required': '由于某种原因，这是覆盖模型默认值的自定义错误消息' }} 
        # 对单个字段自定义错误信息
        # extra_kwargs = {“username”：{“error_messages”：{“required”：“给自己一个用户名”}}}
    publish = serializers.CharField(source='publish.name')
    
    # 自定义的字段， read_only 代表传递过来叔家的时候不需要验证
    author_list = serializers.SerializerMethodField(read_only=True)

    def get_author_list(self, obj):
        temp = []
        for auther in obj.authors.all():
            temp.append({'auther_id': auther.id, 'name': auther.name, 'age': auther.age})
        return temp

    # 因为自定义publish字段了，所以需要重写create方法
    def create(self, validated_data):
        publish = validated_data.pop('publish')['name']
        validated_data['publish_id'] = publish
        authors = validated_data.pop('authors')
        obj = models.Book.objects.create(**validated_data)
        obj.authors.add(*authors)
        return obj
```
>- 序列化结果
```json
[
    {
        "id": 1,
        "publish": [
            {
                "name": "张三",
                "email": "wang1418075087@qq.com"
            }
        ],
        "authors": [
            {
                "name": "李四",
                "age": 2
            }
        ],
        "title": "射雕英雄传",
        "price": 22,
        "pub_date": "2018-11-08"
    },
    {
        "id": 2,
        "publish": [
            {
                "name": "李四",
                "email": "lsi@qwe.com"
            }
        ],
        "authors": [
            {
                "name": "d1",
                "age": 22
            },
            {
                "name": "d2",
                "age": 22
            }
        ],
        "title": "盗墓笔记",
        "price": 22,
        "pub_date": "2018-11-08"
    }
]
```
# rest_framework视图
>-  URL 设计

| UEL        | 请求方式   |  描述  |
| --------:   | -----:  | :----:  |
| /books/     | GET |   获取全部书籍     |
| /books/        |   POST   |   增加一本书籍   |
|/books/(/d+)|GET|获取一本书籍|
|/books/(/d+)|PUT|更新一本书籍|
|/books/(/d+)|DELETE|删除一本书籍|

>-  序列化类
```python
class BookModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = models.Book
        fields = ['title', 'price', 'pub_date', 'publish', 'authors', 'author_list']

    # 序列化成publish为想要的格式
    # publish = PublishModelSerializer()

    publish = serializers.CharField(source='publish.name')
    author_list = serializers.SerializerMethodField(read_only=True)

    def get_author_list(self, obj):
        temp = []
        for auther in obj.authors.all():
            temp.append({'auther_id': auther.id, 'name': auther.name, 'age': auther.age})
        return temp

    # 因为自定义publish字段了，所以需要重写create方法
    def create(self, validated_data):
        publish = validated_data.pop('publish')['name']
        validated_data['publish_id'] = publish
        authors = validated_data.pop('authors')
        obj = models.Book.objects.create(**validated_data)
        obj.authors.add(*authors)
        return obj
    
    def update(self, instance, validated_data):
        print(validated_data)
        publish = validated_data.pop('publish')['name']
        validated_data['publish_id'] = publish
        b = super(BookModelSerializer, self).update(instance, validated_data)
        return b


```
## rest_framework.views.APIView
    rest_framework.views.APIView是对原生View的一次封装，基本功能同原生View
>-  视图函数
```python

class BookView(APIView):
    # 获取全部书籍
    def get(self, request, *args, **kwargs):
        book_obj = models.Book.objects.all()
        bs = BookModelSerializer(book_obj, many=True)
        return Response(bs.data)
    # 添加一本书籍
    def post(self, request, *args, **kwargs):
        bs = BookModelSerializer(data=request.data, many=False)
        if bs.is_valid():
            bs.save()
            return Response(bs.data)
        else:
            return Response(bs.errors)


class BookDetatailVie(APIView):
    # 获取一本书籍
    def get(self, request, pk, *args, **kwargs):
        context = {'code': 1000}
        try:
            book_obj = models.Book.objects.get(pk=pk)
        except models.Book.DoesNotExist:
            context['code'] = 4000
            context['erroMessage'] = '当前书籍不存在'
            return Response(context)
        bs = BookModelSerializer(book_obj)
        context['data'] = bs.data
        return Response(context)
    # 更新一本书籍
    def put(self, request, pk, *args, **kwargs):
        book_obj = models.Book.objects.get(pk=pk)
        bs = BookModelSerializer(book_obj, data=request.data)
        if bs.is_valid():
            bs.save()
            return Response(bs.data)
        else:
            return Response(bs.errors)
    # 删除一本书籍
    def delete(self, request, pk, *args, **kwargs):
        models.Book.objects.get(pk=pk).delete()
        return Response()
```
## rest_framework.mixins
    针对于 rest_framework.views.APIView的一次封装
![](https://img-blog.csdn.net/20180123160353398?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbF92aXA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
>- 视图函数
```python
from rest_framework import mixins
from rest_framework import generics


class BookView(mixins.ListModelMixin,
                  mixins.CreateModelMixin,
                  generics.GenericAPIView):
    queryset = models.Book.objects.all()
    serializer_class = BookModelSerializer

    # 获取全部书籍
    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    # 添加一条书籍
    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)


class BookDetailView(mixins.RetrieveModelMixin,
                        mixins.UpdateModelMixin,
                        mixins.DestroyModelMixin,
                        generics.GenericAPIView):
    queryset = models.Book.objects.all()
    serializer_class = BookModelSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def dalete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)

```
## rest_framework.generics
    针对于rest_framework.mixins的再次封装的通用视图类
    
>-  所有的视图类
```python
from rest_framework import generics

generics.ListAPIView
generics.CreateAPIView

generics.ListCreateAPIView


generics.RetrieveAPIView
generics.UpdateAPIView
generics.DestroyAPIView

generics.RetrieveUpdateAPIView
generics.RetrieveUpdateDestroyAPIView
```
>- 视图类的使用
```python
class BookView(generics.ListCreateAPIView):
    queryset = models.Book.objects.all()
    serializer_class = BookModelSerializer

class BookDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = models.Book.objects.all()
    serializer_class = BookModelSerializer
```
##  rest_framework.viewsets.ModelViewSet
    视图类的终极封装，
### 基本使用
>- url.py
```
url(r'^books/$', views.BookViewSet.as_view({"get":"list","post":"create"}),name="book_list"),
url(r'^books/(?P<pk>\d+)$', views.BookViewSet.as_view({
            'get': 'retrieve',
            'put': 'update',
            'patch': 'partial_update',
            'delete': 'destroy'
        }),name="book_detail"),
```
>- view.py
```python
class BookViewSet(ModelViewSet):
    queryset = models.Book.objects.all()
    serializer_class = BookModelSerializer
```
# rest_framework路由控制
    路由控制是为了针对一个序列化类生成对应的 增删改查的url
>- 未是哟弄个路由控制的url.py
```python
url(r'^books/$', views.BookViewSet.as_view({"get":"list","post":"create"}),name="book_list"),
url(r'^books/(?P<pk>\d+)$', views.BookViewSet.as_view({
            'get': 'retrieve',
            'put': 'update',
            'patch': 'partial_update',
            'delete': 'destroy'
        }),name="book_detail"),
```
>- 使用路由控制的url.py
```python
from rest_framework import routers
router = routers.DefaultRouter()
router.register(prefix='books', viewset=views.BookViewSet)

urlpatterns = [
    # path('books/', views.BookViewSet.as_view({'get': 'list', 'post': 'create'})),
    # path('books/<int:pk>/', views.BookViewSet.as_view({
    #     'get': 'retrieve',
    #     'put': 'update',
    #     'patch': 'partial_update',
    #     'delete': 'destroy'
    # })),s
    path('', include(router.urls)),
]
```
>>- 生成四条url
```
^books/$ [name='book-list']
^books\.(?P<format>[a-z0-9]+)/?$ [name='book-list']
^books/(?P<pk>[^/.]+)/$ [name='book-detail']
^books/(?P<pk>[^/.]+)\.(?P<format>[a-z0-9]+)/?$ [name='book-detail']
```
# rest_framework组件
## 认证组件
    rest_framework.authentication.BaseAuthentication
### 使用
1. 创建保存Tocken的models
```python
class Token(models.Model):
    user_type_choices = ((1, '普通用户'), (2, 'VIP用户'), (3, 'SVIP用户'))
    user_type = models.SmallIntegerField(verbose_name='用户类型', choices=user_type_choices, default=1)
    user = models.OneToOneField(to=settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    token = models.CharField(max_length=256)
    create_time = models.DateTimeField(verbose_name='Token创建时间', auto_now_add=True)
```
2. 创建登陆视图函数，采用Tocken方式认证
```python
class Login(APIView):

    def post(self, request, *args, **kwargs):
        res = {'code': 1000, 'msg': None}
        try:
            username = request._request.POST.get('username')
            password = request._request.POST.get('password')
            user = User.objects.filter(username=username).first()
            print(user)
            if not user:
                res['code'] = 4000
                res['msg'] = '当前用户不存在！'
                return Response(res)
            else:
                if not user.check_password(password):
                    res['code'] = 4001
                    res['msg'] = '密码错误！'
                    return Response(res)
                tockon = self.get_random_str(user.username)
                models.Token.objects.update_or_create(user=user, token=tockon)
                res['token'] = tockon
                return Response(res)
        except Exception as e:
            res['code'] = 2001
            res['msg'] = e
            return Response(res)

    def get_random_str(self, user):
        import hashlib, time
        ctime = str(time.time())

        md5 = hashlib.md5(bytes(user, encoding="utf8"))
        md5.update(bytes(ctime, encoding="utf8"))
```
3. 创建自定义认证类
```python
from rest_framework.authentication import BaseAuthentication
# 自定义认证类
class TockonAuthentication(BaseAuthentication):

    def authenticate(self, request):
        '''
        认证成功返回一个元祖， 第一个元素是用户models， 第二个是
        '''
        token = request._request.GET.get("token")
        token_obj = models.Token.objects.filter(token=token).first()
        if not token_obj:
            raise exceptions.AuthenticationFailed(detail={'erroMsg': '当前用户不存在'})
        else:
            time_out = timezone.now() - token_obj.create_time
            if time_out.seconds > 300:
                raise exceptions.AuthenticationFailed(detail={'erroMsg': 'Tocken超时！', 'status_code': 40001})
            return (token_obj.user, token_obj)
```
4. 局部认证配置
```python
# 试图函数3
class BookViewSet(ModelViewSet):
    authentication_classes = [TockonAuthentication, ] # 存放认证类
    queryset = models.Book.objects.all()
    serializer_class = BookModelSerializer
```
5. 全局使用认证类
```python
# settings.py
REST_FRAMEWORK={
    "DEFAULT_AUTHENTICATION_CLASSES":["app01.service.auth.TockonAuthentication",]
}
```
### django-rest-framework配置json web token进行接口的认证
    使用django-rest-framework开发api并使用json web token进行身份验证
    在这里使用django-rest-framework-jwt这个库来帮助我们简单的使用jwt进行身份验证
    并解决一些前后端分离而产生的跨域问题
#### 后端配置
1. 安装
>- pip install djangorestframework-jwt

2. settings.py，添加JSONWebTokenAuthentication到Django REST框架DEFAULT_AUTHENTICATION_CLASSES
```python

REST_FRAMEWORK = {

    # 设置所有接口都需要被验证
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.IsAuthenticated',
    ),
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
    ),
}
```
3. 在setting里设置token的过期时间
```python
import datetime
JWT_AUTH = {
    'JWT_EXPIRATION_DELTA': datetime.timedelta(seconds=300),
}
```
4. 配置URL
```python
from rest_framework_jwt.views import obtain_jwt_token
url(r'^api-token-auth/', obtain_jwt_token),
```
#### 前端配置
1. 使用post方法获取token并存入html的localStorage中
```javascript
function post_test() {
        $.post("http://10.127.48.204:8000/api-token-auth/",{
            'username':'earthchen',
            'password':'xxxxxxxx'
        },
        function(result){
            if(result){
                localStorage.token=result.token;  //存入数据
            }
        })
    }
```
2. 请求需要认证的视图的时候在请求头中携带token
```javascript
function test(){
    $.ajax({
        headers:{
            'Authorization':'JWT '+localStorage.token  //注意：jwt后面有个空格
        },
        type:"get",
        url:"http://10.127.48.204:8000/snippets/1/",
        success:function(result){
           document.write(result.style);
        }
    })
}
```
#### JWT_AUTH 的一些配置
```python
JWT_AUTH = {
    'JWT_ENCODE_HANDLER': 'rest_framework_jwt.utils.jwt_encode_handler',

    'JWT_DECODE_HANDLER': 'rest_framework_jwt.utils.jwt_decode_handler',

    'JWT_PAYLOAD_HANDLER': 'rest_framework_jwt.utils.jwt_payload_handler',

    'JWT_PAYLOAD_GET_USER_ID_HANDLER': 'rest_framework_jwt.utils.jwt_get_user_id_from_payload_handler',

    'JWT_RESPONSE_PAYLOAD_HANDLER': 'rest_framework_jwt.utils.jwt_response_payload_handler',

    'JWT_SECRET_KEY': settings.SECRET_KEY, # 用于加密JWT密钥， 默认是settings.SECRET_KEY
    'JWT_GET_USER_SECRET_KEY': None, 
    'JWT_PUBLIC_KEY': None,
    'JWT_PRIVATE_KEY': None,
    'JWT_ALGORITHM': 'HS256',
    'JWT_VERIFY': True,
    'JWT_VERIFY_EXPIRATION': True,
    'JWT_LEEWAY': 0,
    'JWT_EXPIRATION_DELTA': datetime.timedelta(seconds=300),
    'JWT_AUDIENCE': None,
    'JWT_ISSUER': None,

    'JWT_ALLOW_REFRESH': False,
    'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=7), # tockon 过期时间

    'JWT_AUTH_HEADER_PREFIX': 'JWT', # 请求头Authorization 需要写道的value的前缀
    'JWT_AUTH_COOKIE': None,
}
```
## 权限组件
    rest_framework.permissions.BasePermission
### 使用
1. 创建自定义权限类, 至运行用户类型为SVIP的访问
```python
from rest_framework.permissions import BasePermission

class SVIPPermission(BasePermission):
    def has_permission(self, request, view):

        if request.user.token.user_type == 3:
            return True
        else:
            return False
```
2. 在视图函数中采用局部认证
```python
class BookViewSet(ModelViewSet):
    authentication_classes = [TockonAuthentication, ]
    permission_classes = [SVIPPermission, ]
    queryset = models.Book.objects.all()
    serializer_class = BookModelSerializer
```
3. 全局认证
```python
# settings.py
REST_FRAMEWORK={
    "DEFAULT_AUTHENTICATION_CLASSES":["app01.service.auth.TockonAuthentication",],
    "DEFAULT_PERMISSION_CLASSES": ["app01.service.auth.SVIPPermission"]
}
```
## 频率组件
    rest_framework.throttling.BaseThrottle
### 使用
1. 创建自定义频率组件
```python
# 同一个IP在1分钟之内访问频率不允许超过3次
from rest_framework.throttling import BaseThrottle
import time

VISIT_RECORD={}
class VisitThrottle(BaseThrottle):

    def __init__(self):
        self.history=None

    def allow_request(self,request,view):
        remote_addr = request.META.get('REMOTE_ADDR')
        ctime=time.time()

        if remote_addr not in VISIT_RECORD:
            VISIT_RECORD[remote_addr]=[ctime,]
            return True

        history=VISIT_RECORD.get(remote_addr)
        self.history=history

        while history and history[-1]<ctime-60:
            history.pop()

        if len(history)<3:
            history.insert(0,ctime)
            return True
        else:
            return False

    def wait(self):
        import time
        ctime=time.time()
        return 60-(ctime-self.history[-1])

```
2. 局部使用
```python
class BookViewSet(generics.ListCreateAPIView):
    throttle_classes = [VisitThrottle,] # 注册频率组件
    queryset = Book.objects.all()
    serializer_class = BookSerializers
```
### 内置频率组件
AnonRateThrottle
#### UserRateThrottle
    UserRateThrottle将限制用户通过API的给定速率的请求。用户标识用于生成用于限制的唯一键。未经身份验证的请求将回退到使用传入请求的IP地址生成一个唯一的密钥来限制
>- view
```python
class BurstRateThrottle(UserRateThrottle):
    scope = 'burst'

class SustainedRateThrottle(UserRateThrottle):
    scope = 'sustained'
```
>- settings
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': (
        'example.throttles.BurstRateThrottle',
        'example.throttles.SustainedRateThrottle'
    ),
    'DEFAULT_THROTTLE_RATES': {
        'burst': '60/min',
        'sustained': '1000/day'
    }
}
```
#### ScopedRateThrottle
    ScopedRateThrottle类可用于限制访问API的特定部分。仅当正在访问的视图包含.throttle_scope属性时，才会应用此限制。然后，通过将请求的“范围”与唯一用户ID或IP地址连接，形成唯一的节流密钥。
>- view
```python
class ContactListView(APIView):
    throttle_scope = 'contacts'
    ...

class ContactDetailView(APIView):
    throttle_scope = 'contacts'
    ...

class UploadView(APIView):
    throttle_scope = 'uploads'
    ...
```
>- settings
```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': (
        'rest_framework.throttling.ScopedRateThrottle',
    ),
    'DEFAULT_THROTTLE_RATES': {
        'contacts': '1000/day',
        'uploads': '20/day'
    }
}
```
## 解析器
## 分页
## 过滤
## 响应器
##  渲染器
## 版本处理
    在API中应该加上API版本进行区分



    


