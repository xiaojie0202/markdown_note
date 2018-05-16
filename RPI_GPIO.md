# RPI.GPIO
    RPI.GPIO 是用于控制树莓派的GPIO针脚专用的python模块
# 安装
$ sudo apt-get update
$ sudo apt-get install python-rpi.gpio python3-rpi.gpio
To install the latest development version from the project source code library:
$ sudo apt-get install python-dev python3-dev
$ sudo apt-get install mercurial
$ sudo apt-get install python-pip python3-pip
$ sudo apt-get remove python-rpi.gpio python3-rpi.gpio
$ sudo pip install hg+http://hg.code.sf.net/p/raspberry-gpio-python/code#egg=RPi.GPIO
$ sudo pip-3.2 install hg+http://hg.code.sf.net/p/raspberry-gpio-python/code#egg=RPi.GPIO
To revert back to the default version in Raspbian:
$ sudo pip uninstall RPi.GPIO
$ sudo pip-3.2 uninstall RPi.GPIO
$ sudo apt-get install python-rpi.gpio python3-rpi.gpio

# 基本使用-初始化
    ```import RPI.GPIO as GPIO```

>- GPIO.sermodel(GPIO.BOARD or GPIO.BCM) #设置针脚通道采用编号模式
>>- 参数 : GPIO.BOARD 或者 GPIO.BCM

>- GPIO.getmode() # 获取当前针脚采用的编号

>- GPIO.setwarnings(False) # 当跑着多个控制pin的脚本的时候会有警告，通过此方法禁用警告

>- GPIO.setup(channel, direction, pull_up_down, initial)
>>- channel 针脚编号,int类型，也可以传一个list列表对列表中针脚编号统一初始化
>>- direction： 设置针脚编号的模式 GPIO.IN 或者 GPIO,OUT
>>- pull_up_down: 当连接按钮的时候，并且为GPIO.IN模式，针脚断开相当于没有连接任何东西，此时针脚将会受到干扰进行浮动,通过此参数使能使用输入通道与3.3V(拉升PUD_UP)或0V(下拉PUD_DOWN)之间的10K电阻，默认是关闭状态PUD_OFF
>>- initial: 为输出通道设置默认值，GPIO.LOW 或者 GPIO.HIGH

>- GPIO.cleanup(*args, **args)
>>- 通过重新设置所有GPIO通道来进行清理，可以传递一个list只清理list中的通道，默认清理所有！！！

# 输入
>- GPIO.setup(channel, GPIO.IN, pull_up_down=PUD_OFF)
>>- 初始化输入针脚

>- GPIO.input(channel)
>>- 返回当前针脚是否有输入 Returns HIGH=1=True or LOW=0=False

>- GPIO.wait_for_edge(channel, edge, bouncetime, timeout) # 边缘检测，阻塞方法，只要针脚输入没变动将阻塞
>>- channel: 针脚编号
>>- edge: 高电平触发(GPIO.RISING), 低电平触发(GPIO.FALLING)  只要有变动就触发(GPIO.BOTH)
>>- timeout: 超时时间(毫秒)
>>- bouncetime: 开关防抖，指定时间内只能触发一次（毫秒），回调函数

>- GPIO.add_event_detect(channel, edge, callback, bouncetime)
>>- 给输出通道的pin添加边缘检测,此方法不阻塞
>>- channel: 针脚编号
>>- edge: 高电平触发(GPIO.RISING), 低电平触发(GPIO.FALLING)  只要有变动就触发(GPIO.BOTH)
>>- callback: 回调函数,针脚通道触发的时候执行

>- GPIO.add_event_callback(channel, callback)
>>- 为已监听输入通道的pin添加回调函数

>- GPIO.event_detected(channel)
>>- 返回bool， add_event_detect的针脚是否触发

>- GPIO.remove_event_detect(channel)
>>- 移除chennel针脚的边缘检测

# 输出
>- GPIO.setup(channel, GPIO.OUT, initial)
>>- initial: 为输出通道设置默认值，GPIO.LOW 或者 GPIO.HIGH

>- GPIO.output(channel value)
>>- 设置针脚输出，为高电平(GPIO.HIGH)，或者低电平(GPIO.LOW)

# PWM
>- 创建一个 PWM 实例：p =GPIO.PWM(channel, frequency)

>- 启用 PWM：p.start(dc) # dc 代表占空比（范围：0.0 <= dc <= 100.0）

>- 更改频率：p.ChangeFrequency(freq) # freq 为设置的新频率，单位为 Hz (1秒钟重复的次数。60Hz代表每秒周期震动60次。)

>- 更改占空比：p.ChangeDutyCycle(dc) # 范围：0.0 <= dc <= 100.0
0.5ms /

>- 停止 PWM：p.stop()
# 其他
>- GPIO.gpio_function(pin)
>>- 显示GPIO通道的功能
>>- GPIO.IN, GPIO.OUT, GPIO.SPI, GPIO.I2C, GPIO.HARD_PWM, GPIO.SERIAL, GPIO.UNKNOWN
