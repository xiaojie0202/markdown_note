# JSONP跨域请求
    当浏览器发送请求的时候， 会检测当前的协议，域名，端口是否一样（是否同域）, 如果一样，请求正常可以回来，如果不一样，则浏览器会拦截响应
# JSONP跨域原理
    jsonp是json用来跨域的一个东西。原理是通过script标签的跨域特性来绕过同源策略。
## 实现
>- 后端代码
```
def send_ajax(requests):
    callbacks = requests.GET.get('callbacks')
    data = {'name': 'xiaojie', 'age': 1, 'interest': [1, 2, 3, 4, 5]}
    return HttpResponse('%s(%s)' % (callbacks, json.dumps(data)))
```
>- 前端代码
```html
<!--当前网站http://127.0.0.1:8888/jsonp-->
<button id="submit" onclick="addScriptTag('http://127.0.0.1:7766/sendAjax?callbacks=getInfo')">JSON跨域</button>
<script>
    function addScriptTag(src){
         var script = document.createElement('script');
         script.setAttribute("type","text/javascript");
         script.src = src;
         document.body.appendChild(script);
         document.body.removeChild(script);
    }
    function getInfo(data){
        console.log(data);
        var f = document.getElementById('info');
        f.innerHTML = `<p>姓名:${data.name}</p><p>年龄:${data.age}</p>`
    }
</script>
```
>- 前端代码(jquery实现)
```html
   function addScriptTag(src){
          $.ajax({
                url: src //"http://127.0.0.1:7766/sendAjax",
                dataType:"jsonp",
                jsonp: 'callbacks',
                jsonpCallback:"getInfo"
           });

       }

    function getInfo(data){
        console.log(data);
        var f = document.getElementById('info');
        f.innerHTML = `<p>姓名:${data.name}</p><p>年龄:${data.age}</p>`
    }
<!--__________________________________________进阶版________________________________-->
 function f(){

            $.ajax({
               url:"http://127.0.0.1:7766/SendAjax/",
               dataType:"jsonp",            //必须有，告诉server，这次访问要的是一个jsonp的结果。
               jsonp: 'callbacks',          //jQuery帮助随机生成的：callbacks="wner"
               success:function(data){
                   var f = document.getElementById('info');
                    f.innerHTML = `<p>姓名:${data.name}</p><p>年龄:${data.age}</p>`
              }
         });

       }

```
# CORS
    CORS需要浏览器和服务器同时支持。目前，所有浏览器都支持该功能，IE浏览器不能低于IE10。
    整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。
    因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信。

浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。
只要同时满足以下两大条件，就属于简单请求。
```
（1) 请求方法是以下三种方法之一：
HEAD
GET
POST
（2）HTTP的头信息不超出以下几种字段：
Accept
Accept-Language
Content-Language
Last-Event-ID
Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain
```
凡是不同时满足上面两个条件，就属于非简单请求。
浏览器对这两种请求的处理，是不一样的。
```
* 简单请求和非简单请求的区别？

   简单请求：一次请求
   非简单请求：两次请求，在发送数据之前会先发一次请求用于做“预检”，只有“预检”通过后才再发送一次请求用于数据传输。
* 关于“预检”

- 请求方式：OPTIONS
- “预检”其实做检查，检查如果通过则允许传输数据，检查不通过则不再发送真正想要发送的消息
- 如何“预检”
     => 如果复杂请求是PUT等请求，则服务端需要设置允许某请求，否则“预检”不通过
        Access-Control-Request-Method
     => 如果复杂请求设置了请求头，则服务端需要设置允许某请求头，否则“预检”不通过
        Access-Control-Request-Headers
```
>- 支持跨域，简单请求
>>- 服务器设置响应头：Access-Control-Allow-Origin = '域名' 或 '*'
>- 支持跨域，复杂请求
>>- 由于复杂请求时，首先会发送“预检”请求，如果“预检”成功，则发送真实数据。
>>>- “预检”请求时，允许请求方式则需服务器设置响应头：Access-Control-Request-Method
>>>- “预检”请求时，允许请求头则需服务器设置响应头：Access-Control-Request-Headers
>>>- “预检”缓存时间，服务器设置响应头：Access-Control-Max-Age
```
# 后端代码实现
def send_ajax(requests):
    import json
    callbacks = requests.GET.get('callbacks')
    data = {'name': 'xiaojie', 'age': 1, 'interest': [1, 2, 3, 4, 5]}
    response = HttpResponse('%s(%s)' % (callbacks, json.dumps(data)))
    response['Access-Control-Allow-Origin'] = "*"
    response['Access-Control-Allow-Methods'] = "PUT,DELETE"
    response['Access-Control-Allow-Headers'] = "Content-Type,k1"
    response['Access-Control-Max-Age'] = "10"
    return response
```
