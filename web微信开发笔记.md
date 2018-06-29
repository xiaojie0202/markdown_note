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

ser_list_url = "https://wx.qq.com/cgi-bin/mmwebwx-bin/webwxgetcontact?lang=zh_CN&r={0}&seq=0&skey={1}".format(int(time.time()*1000), ticket_dict.get('skey'))
    user_list_ret = requests.get(url=user_list_url, cookies=cookies)
    user_list_ret.encoding = 'utf-8'
    user_list_dict = user_list_ret.json()
 
 6. 发送消息
 def send():
    ticket_dict = session.get('ticket_dic')

    from_user = request.form.get('formuser')
    to = request.form.get('to')
    content = request.form.get('content')
    ctime = str(time.time()*1000)
    msg_url = "https://wx.qq.com/cgi-bin/mmwebwx-bin/webwxsendmsg?lang=zh_CN&pass_ticket={0}".format(ticket_dict['pass_ticket'])

    data_dict = {
        'BaseRequest': {
            "DeviceID": "e687896895614144",
            "Sid": ticket_dict.get('wxsid'),
            "Uin": ticket_dict.get('wxuin'),
            "Skey": ticket_dict.get('skey'),
        },
        'Msg': {
            'ClientMsgId':ctime,
            'LocalID':ctime,
            'FromUserName':from_user,
            'ToUserName':to,
            "Content":content,
            'Type':1
        },
        'Scene': 0
    }

    ret = requests.post(
        url=msg_url,
        data=bytes(json.dumps(data_dict,ensure_ascii=False),encoding='utf-8')
    )
    return jsonify(ret.json())

7. 接受消息
def get_msg():
    # 检查是否有新消息到来
    sync_url = "https://webpush.weixin.qq.com/cgi-bin/mmwebwx-bin/synccheck"
    ticket_dict = session.get('ticket_dic')
    sync_data_list = []
    for item in session.get('SyncKey')['List']:
        temp = "%s_%s" % (item['Key'], item['Val'])
        sync_data_list.append(temp)

    sync_data_str = "|".join(sync_data_list)
    nid = int(time.time())
    sync_dict = {
        "r": nid,
        "skey": ticket_dict['skey'],
        "sid": ticket_dict['wxsid'],
        "uin": ticket_dict['wxuin'],
        "deviceid": "e531777446530354",
        "synckey": sync_data_str
    }
    response_sync = requests.get(sync_url, params=sync_dict, cookies=session.get('cookies'))
    print(response_sync.text)
    # window.synccheck={retcode:"0",selector:"2"}
    if 'selector:"2"' in response_sync.text:
        fetch_msg_url = "https://wx.qq.com/cgi-bin/mmwebwx-bin/webwxsync?sid=%s&skey=%s&lang=zh_CN&pass_ticket=%s" % (ticket_dict['wxsid'], ticket_dict['skey'], ticket_dict['pass_ticket'])

        form_data = {
            'BaseRequest': {
                'DeviceID': 'e531777446530354',
                'Sid': ticket_dict['wxsid'],
                'Skey': ticket_dict['skey'],
                'Uin': ticket_dict['wxuin']
            },
            'SyncKey': session.get('SyncKey'),
            'rr': str(time.time())
        }
        response_fetch_msg = requests.post(fetch_msg_url, json=form_data)
        response_fetch_msg.encoding = 'utf-8'
        res_fetch_msg_dict = json.loads(response_fetch_msg.text)
        session['SyncKey'] = res_fetch_msg_dict['SyncKey']
        '''
        msgtype:1 :个人信息
        msgtypy:2: 群组消息
        msgtype:3: 公众号信息
        '''
        msg = {'msg': True, 'data':[]}
        print()
        if not res_fetch_msg_dict['AddMsgList']:
            return jsonify({'msg': False})
        for item in res_fetch_msg_dict['AddMsgList']:
            msg_dict = {'msgtype': 1, 'Content': item['Content'], 'FromUserName': item['FromUserName'], 'ctime': time.strftime('%H:%S', time.localtime(item['CreateTime']))}

            if item['Url'] != '':
                msg_dict['msgtype'] = 3
                msg_dict['title'] = item['FileName']
                msg_dict['url'] = item['Url']
            if item['FromUserName'].count('@') == 2:
                msg_dict['msgtype'] = 2
                send_user, info = re.search(r'(?P<senduser>.+):<br/>(?P<msg>.+)', msg_dict['Content']).group('senduser', 'msg')
                msg_dict['SendUser'] = send_user
                msg_dict['Content'] = info

            print(msg_dict)
            msg['data'].append(msg_dict)
            print(item['Content'], ":::::", item['FromUserName'], "---->", item['ToUserName'], )
        return jsonify(msg)
    return jsonify({'msg': False})








