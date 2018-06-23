1. 解析出来微信登陆的图片
```
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        # 获取微信扫描登陆的二维码, 响应信息 window.QRLogin.code = 200; window.QRLogin.uuid = "AeMwX5ZZ1A==";
        wx_response = requests.get(url="https://login.wx.qq.com/jslogin?appid=wx782c26e4c19acffb&redirect_uri=https%3A%2F%2Fwx.qq.com%2Fcgi-bin%2Fmmwebwx-bin%2Fwebwxnewloginpage&fun=new&lang=zh_CN&_={0}".format(int(time.time() * 1000)))
        
        # 解析出来图片的UUID
        uuid = re.search(r'uuid = "(?P<uuid>.+)";', wx_response.text).group('uuid')

        # 把二维码UUID拼接成图片连接
        img_url = 'https://login.weixin.qq.com/qrcode/' + uuid

        return render_template('login.html', img=img_url)
```
2. 向此地址发送GET长轮询， 查询是否扫码
https://login.wx.qq.com/cgi-bin/mmwebwx-bin/login?loginicon=true&uuid=IdyFt8gccA==&tip=0&r=-639143436&_=1529647449986

没有扫描返回 ： window.code=408;
二维码超时 ： window.code=400;
扫码后返回头像： window.code=201;window.userAvatar = 头像图片

点击确认登陆后返回
    window.code=200;
    window.redirect_uri="https://wx.qq.com/cgi-bin/mmwebwx-bin/webwxnewloginpage?ticket=ATDDEdc0UnFIWJkrs-g-bcTb@qrticket_0&uuid=4ZMREKwYcg==&lang=zh_CN&scan=1529651591";
    需要在重定向url后拼接字符串  &fun=new&version=v2
 
 3. 访呢重定向的url获得授权信息
    <error>
        <ret>0</ret>
        <message></message>
        <skey>@crypt_9d36bb3d_ceaffb92f25f1651bc738a77f67f9cff</skey>
        <wxsid>DLaWPbhtjE4i9hzJ</wxsid>
        <wxuin>2365688641</wxuin>
        <pass_ticket>iB2Y9QCiDp%2BZ6m9VlZxSnfIvPv3n6oTy3xAk5OXakjk5gsaHaEDEDF0tYZZQtfEY</pass_ticket>
        <isgrayscale>1</isgrayscale>
    </error>
4. 获取初始化信息
https://wx.qq.com/cgi-bin/mmwebwx-bin/webwxinit?r=-639092734&pass_ticket=<pass_ticket>
data = 
{
    BaseRequest:{
        DeviceID:"e135634317552700"
        Sid:"DLaWPbhtjE4i9hzJ" # wxsid
        Skey:"@crypt_9d36bb3d_ceaffb92f25f1651bc738a77f67f9cff" # vskey
        Uin:"2365688641" # wxuin
    }
}

5. 请求头像需要后端处理完返回给前端
    
def img_base64(url):
    url = 'https://wx.qq.com' + url
    cookies = session.get('cookies')
    response = requests.get(url=url, cookies=cookies)
    img = base64.b64encode(response.content)
    img = img.decode('utf-8')
    img = 'data:image/jpg;base64,' + img
    print(img)
    return img
    
5. 获取所有联系人


