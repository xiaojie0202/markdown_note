# python内置函数详解
    python提供了很多内置函数
## 数学运算类
- abs(x)求数值的绝对值
    ```
    >>> abs(-4)
    4
    ```
- divmod(x, y):返回元祖(x//y, x%y) 商河余数
    ```
    >>> divmod(5,2)
    (2, 1)
    >>> divmod(5.2,2)
    (2.0, 1.2000000000000002)
    ```
- max(iterable):返回可迭代对象中元素的最大值
    ```
    >>> a = (i for i in range(200))
    >>> max(a)
    199
    ```
- max(arg1, arg2, *args, *[, key=func]): 传入多个参数，求最大值，可以指定key为一个函数处理所有的参数后再近些比较
    ```
    >>> a = [i for i in range(-100,50)]
    >>> max(*a,key=abs)
    -100
    ```
- min：同max函数
- pow(x, y, z=None):返回x**y或者x**y%z
    ```
    >>> pow(5,2)
    25  # 5**2
    >>> pow(5,2,2)
    1   # 5**2%2
    ```
- sum(iterable, start=0): 对元素是数值类型的可迭代对象中的每个元素求和,可以指定start起始数值
    ```
    >>> sum([1,2,3,4])
    10
    >>> sum([1,2,3,4], 10)
    20
    ```
## 类型转换
- bool(x):返回传入参数的逻辑值
    ```
    # 只有`None`,`0`,`空对象` 才会返回False
    >>> bool(None)
    False
    >>> bool(0)
    False
    >>> bool([])
    False
    >>> bool(1)
    True
    ```
- int(x, base=10)：返回一个数值类型的十进制，如果指定进制（base）参数则x需要str类型
    ```
    >>> int(5)
    5
    >>> int(5.2)
    5
    ```
- float(x): 返回x的浮点数
    ```
    >>>float(5)
    5.0
    >>>float(5.556)
    5.556
    ```
- complex(x)：返回传入参数的复数形式
    ```
    >>> complex(10000)
    (10000+0j)
    >>> complex(10000.55)
    (10000.55+0j)
    ```
- str(x)：返回对象的字符串形式，也可以传入一个bytes类型指定编码
    ```
    >>> a = bytes('123', encoding='utf-8')
    >>> str(a)
    "b'123'"
    >>> str(a, encoding='utf-8')
    '123'
    >>> str(False)
    'False'
    ```
- bytearray()返回一个新字节数组。数组里的元素是可变的，每个元素的值范围: 0 <= x < 256。
    ```
    >>> bytearray([1,2,3])
    bytearray(b'\x01\x02\x03')
    >>> for i in bytearray([1,2,3]):
    ...     print(i)
    ...
    1
    2
    3
    >>> bytearray('xiaojie',encoding='utf-8')
    bytearray(b'xiaojie')

    ```
- bytes() 返回参数的不可变的字节数组
    ```
    >>> bytes(1)
    b'\x00'
    >>> bytes('小杰', encoding='utf-8')
    b'\xe5\xb0\x8f\xe6\x9d\xb0'
    ```
- memoryview() 函数返回给定参数的内存查看对象
    ```
    >>> v = memoryview(b'abcefg')
    >>> v[1]
    98
    >>> v[-1]
    103
    ```
- ord()：返回Unicode字符对应的整数
    ```
    >>> ord('a')
    97
    ```
- chr():返回整数所对应的Unicode字符
    ```
    >>> chr(97) #参数类型为整数
    'a'
    ```
- bin(int)：将整数转换成二进制字符串
    ```
    >>> bin(192)
    '0b11000000'
    ```
- oct(int):整数转八进制字符串
    ```
    >>> oct(192)
    '0o300'
    ```
- hex(int)：整数转十六进制字符串
    ```
    >>> hex(192)
    '0xc0'
    ```
- tuple(iterable) 将可迭代对象的元素转换为元组。
    ```
    >>>tuple([1,2,3,4])
    (1, 2, 3, 4)
    >>> tuple({1:2,3:4})    #针对字典 会返回字典的key组成的tuple
    (1, 3)
    ```
- list(iterable) 将可迭代对象的元素转换成列表
    ```
    >>>list() # 不传入参数，创建空列表
    []
    >>> list('abcd') # 传入可迭代对象，使用其元素创建新的列表
    ['a', 'b', 'c', 'd']
    ```
- dict：根据传入的参数创建一个新的字典
    ```
    >>> dict() # 不传入任何参数时，返回空字典。
    {}
    >>> dict(a = 1,b = 2) #  可以传入键值对创建字典。
    {'b': 2, 'a': 1}
    >>> dict(zip(['a','b'],[1,2])) # 可以传入映射函数创建字典。
    {'b': 2, 'a': 1}
    >>> dict((('a',1),('b',2))) # 可以传入可迭代对象创建字典。
    {'b': 2, 'a': 1}
    ```
- set：根据传入的参数创建一个新的集合
    ```
    >>> a = set([1,2,3,1])
    >>> b = set([3,4,5,3])
    >>> a
    {1, 2, 3}   # 集合中数据不可以重复
    >>> b
    {3, 4, 5}
    >>> a - b   # 集合可以参与运算
    {1, 2}
    ```
- frozenset(iterable)：把可迭代对象的元素创建一个新的不可变集合
    ```
    >>> a = frozenset(range(10))
    >>> a
    frozenset({0, 1, 2, 3, 4, 5, 6, 7, 8, 9})
    ```
- iter：根据传入的参数创建一个新的可迭代对象
    ```
    >>> a = iter('abcd') #字符串序列
    >>> a
    <str_iterator object at 0x03FB4FB0>
    >>> next(a)
    'a'
    >>> next(a)
    'b'
    >>> next(a)
    'c'
    >>> next(a)
    'd'
    >>> next(a)
    Traceback (most recent call last):
      File "<pyshell#29>", line 1, in <module>
        next(a)
    StopIteration
    ```
- slice：根据传入的参数创建一个新的切片对象
    ```
    >>> c1 = slice(5) # 定义c1
    >>> c1
    slice(None, 5, None)
    >>> c2 = slice(2,5) # 定义c2
    >>> c2
    slice(2, 5, None)
    >>> c3 = slice(1,10,3) # 定义c3
    >>> c3
    slice(1, 10, 3)
    ```
- super：根据传入的参数创建一个新的子类和父类关系的代理对象
    ```
    #定义父类A
    >>> class A(object):
        def __init__(self):
            print('A.__init__')

    #定义子类B，继承A
    >>> class B(A):
        def __init__(self):
            print('B.__init__')
            super().__init__()

    #super调用父类方法
    >>> b = B()
    B.__init__
    A.__init__
    ```
## 序列操作
- all(iterable)判断可迭代对象的每个元素是否都为True值
    ```
    # all函数等价于以下代码
    def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
    >>>all(['a', 'b', 'c', 'd'])  # 列表list，元素都不为空或0
    True
    >>> all(['a', 'b', '', 'd'])   # 列表list，存在一个为空的元素
    False
    >>> all([0, 1，2, 3])          # 列表list，存在一个为0的元素
    False

    >>> all(('a', 'b', 'c', 'd'))  # 元组tuple，元素都不为空或0
    True
    >>> all(('a', 'b', '', 'd'))   # 元组tuple，存在一个为空的元素
    False
    >>> all((0, 1，2, 3))          # 元组tuple，存在一个为0的元素
    False

    >>> all([])             # 空列表
    True
    >>> all(())             # 空元组
    True

    ```
- any(iterable)判断可迭代对象的元素是否有为True值的元素
    ```
    def any(iterable):
    for element in iterable:
        if element:
            return True
    return False

    >>> any([0,1,2]) #列表元素有一个为True，则返回True
    True
    >>> any([0,0]) #列表元素全部为False，则返回False
    False
    >>> any([]) #空列表
    False
    >>> any({}) #空字典
    False
    ```
- zip()：拉链，聚合传入的每个迭代器中相同位置的元素，返回一个新的元组类型迭代器
    ```
    >>> zip(('a','b','b'),(1,2,3,4))
    <zip object at 0x000001E1F5818A88>
    >>> list(zip(('a','b','b'),(1,2,3,4)))
    [('a', 1), ('b', 2), ('b', 3)]

    >>> x = [1,2,3] #长度3
    >>> y = [4,5,6,7,8] #长度5
    >>> list(zip(x,y)) # 取最小长度3
    [(1, 4), (2, 5), (3, 6)]
    # 跟进字典中的value取最大的元素
    >>> dict_age = {'xiaojie':20, 'baba':30, 'mama':31}
    >>> max(zip(dict.values(), dict_age.keys()))
    (31, 'mama')
    ```
- reversed：反转序列生成新的可迭代对象
    ```
    >>> a = reversed(range(10)) # 传入range对象
    >>> a # 类型变成迭代器
    <range_iterator object at 0x035634E8>
    >>> list(a)
    [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]

    ```
- sorted：对可迭代对象进行排序，返回一个新的列表
    ```
    >>> a = ['a','b','d','c','B','A']
    >>> a
    ['a', 'b', 'd', 'c', 'B', 'A']

    >>> sorted(a) # 默认按字符ascii码排序
    ['A', 'B', 'a', 'b', 'c', 'd']

    >>> sorted(a,key = str.lower) # 转换成小写后再排序，'a'和'A'值一样，'b'和'B'值一样
    ['a', 'A', 'b', 'B', 'c', 'd']
    ```
- next：返回可迭代对象中的下一个元素值
    ```
    >>> a = iter('abcd')
    >>> next(a)
    'a'
    >>> next(a)
    'b'
    >>> next(a)
    'c'
    >>> next(a)
    'd'
    >>> next(a)
    Traceback (most recent call last):
      File "<pyshell#18>", line 1, in <module>
        next(a)
    StopIteration
    ```
- map(func, *iterables):把可迭代对象交给函数func去处理，返回处理完数据的可迭代对象
    ```
    # map函数原理
    def map_test(fun,array):
		ret = []
		for i in arry:
			res = fun(i)
			ret.append(res)
	# 实列转换字符串大写
	>>> msg = 'xiaojie'
    >>> map(lambda x:x.upper(), msg)
    <map object at 0x000001E1F5C34F60>
    >>> list(map(lambda x:x.upper(), msg))
    ['X', 'I', 'A', 'O', 'J', 'I', 'E']
    ```
- filter(fun， arry)过滤序列中元素,fun是一个函数要求有boolean的返回值，arry是可迭代想
    ```
    # filter 原理
    def filter_test(fun,arry):
	ret = []
	for i in arry:
		if fun(i):
			ret.append(i)
	# 过滤序列中的1
    >>> a = filter(lambda x:x!=1,[1,1,1,5,1,5,1,6,2,1])
    >>> list(a)
    [5, 5, 6, 2]
    ```
- reduce(function, iterable[, initializer])对参数序列中元素进行累积
    ```
    # 原理
    def reduce(fun, arry, init=None):
    ret = init if init else arry.pop(0)
    for i in arry:
        ret = fun(ret, i)
    return ret

    reduce(lamdba x,y:x*y,  [1,2,3,4,100], 1)
    ```
## 对象操作
- help()：返回对象的帮助信息
    ```
    >>> help(dir)

    Help on built-in function dir in module builtins:
    dir(...)
        dir([object]) -> list of strings

        If called without an argument, return the names in the current scope.
        Else, return an alphabetized list of names comprising (some of) the attributes
        of the given object, and of attributes reachable from it.
        If the object supplies a method named __dir__, it will be used; otherwise
        the default dir() logic is used and returns:
          for a module object: the module's attributes.
          for a class object:  its attributes, and recursively the attributes
            of its bases.
          for any other object: its attributes, its class's attributes, and
            recursively the attributes of its class's base classes.
    ```
 - dir:返回对象或者当前作用域内的属性列表
    ```
    >>> name = 'xiaojie'
    >>> dir()
    ['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'name']
    >>> dir(str)
    ['__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
    >>> dir()
    ```
- id:返回对象的唯一标识符
    ```
    >>> a = 'some text'
    >>> id(a)
    69228568
    ```
- hash:获取对象的哈希值
    ```
    >>> hash('good good study')
    1032709256
    ```
- type():返回对象的类型
    ```
    >>> type(1) # 返回对象的类型
    <class 'int'>

    #使用type函数创建类型D，含有属性InfoD
    >>> D = type('D',(A,B),dict(InfoD='some thing defined in D'))
    >>> d = D()
    >>> d.InfoD
     'some thing defined in D'
    ```

- len：返回对象的长度
    ```
    >>> len('abcd') # 字符串
    >>> len(bytes('abcd','utf-8')) # 字节数组
    >>> len((1,2,3,4)) # 元组
    >>> len([1,2,3,4]) # 列表
    >>> len(range(1,5)) # range对象
    >>> len({'a':1,'b':2,'c':3,'d':4}) # 字典
    >>> len({'a','b','c','d'}) # 集合
    >>> len(frozenset('abcd')) #不可变集合
    ```
- ascii：返回对象的可打印表字符串表现方式
    ```
    >>> ascii(1)
    '1'
    >>> ascii('&')
    "'&'"
    >>> ascii(9000000)
    '9000000'
    >>> ascii('中文') #非ascii字符
    "'\\u4e2d\\u6587'"
    ```
- format：格式化显示值
    ```
    #字符串可以提供的参数 's' None
    >>> format('some string','s')
    'some string'
    >>> format('some string')
    'some string'

    #整形数值可以提供的参数有 'b' 'c' 'd' 'o' 'x' 'X' 'n' None
    >>> format(3,'b') #转换成二进制
    '11'
    >>> format(97,'c') #转换unicode成字符
    'a'
    >>> format(11,'d') #转换成10进制
    '11'
    >>> format(11,'o') #转换成8进制
    '13'
    >>> format(11,'x') #转换成16进制 小写字母表示
    'b'
    >>> format(11,'X') #转换成16进制 大写字母表示
    'B'
    >>> format(11,'n') #和d一样
    '11'
    >>> format(11) #默认和d一样
    '11'

    #浮点数可以提供的参数有 'e' 'E' 'f' 'F' 'g' 'G' 'n' '%' None
    >>> format(314159267,'e') #科学计数法，默认保留6位小数
    '3.141593e+08'
    >>> format(314159267,'0.2e') #科学计数法，指定保留2位小数
    '3.14e+08'
    >>> format(314159267,'0.2E') #科学计数法，指定保留2位小数，采用大写E表示
    '3.14E+08'
    >>> format(314159267,'f') #小数点计数法，默认保留6位小数
    '314159267.000000'
    >>> format(3.14159267000,'f') #小数点计数法，默认保留6位小数
    '3.141593'
    >>> format(3.14159267000,'0.8f') #小数点计数法，指定保留8位小数
    '3.14159267'
    >>> format(3.14159267000,'0.10f') #小数点计数法，指定保留10位小数
    '3.1415926700'
    >>> format(3.14e+1000000,'F')  #小数点计数法，无穷大转换成大小字母
    'INF'

    #g的格式化比较特殊，假设p为格式中指定的保留小数位数，先尝试采用科学计数法格式化，得到幂指数exp，如果-4<=exp<p，则采用小数计数法，并保留p-1-exp位小数，否则按小数计数法计数，并按p-1保留小数位数
    >>> format(0.00003141566,'.1g') #p=1,exp=-5 ==》 -4<=exp<p不成立，按科学计数法计数，保留0位小数点
    '3e-05'
    >>> format(0.00003141566,'.2g') #p=1,exp=-5 ==》 -4<=exp<p不成立，按科学计数法计数，保留1位小数点
    '3.1e-05'
    >>> format(0.00003141566,'.3g') #p=1,exp=-5 ==》 -4<=exp<p不成立，按科学计数法计数，保留2位小数点
    '3.14e-05'
    >>> format(0.00003141566,'.3G') #p=1,exp=-5 ==》 -4<=exp<p不成立，按科学计数法计数，保留0位小数点，E使用大写
    '3.14E-05'
    >>> format(3.1415926777,'.1g') #p=1,exp=0 ==》 -4<=exp<p成立，按小数计数法计数，保留0位小数点
    '3'
    >>> format(3.1415926777,'.2g') #p=1,exp=0 ==》 -4<=exp<p成立，按小数计数法计数，保留1位小数点
    '3.1'
    >>> format(3.1415926777,'.3g') #p=1,exp=0 ==》 -4<=exp<p成立，按小数计数法计数，保留2位小数点
    '3.14'
    >>> format(0.00003141566,'.1n') #和g相同
    '3e-05'
    >>> format(0.00003141566,'.3n') #和g相同
    '3.14e-05'
    >>> format(0.00003141566) #和g相同
    '3.141566e-05'
    ```
- vars：返回当前作用域内的局部变量和其值组成的字典，或者返回对象的属性列表
    ```
    #作用于类实例
    >>> class A(object):
        pass

    >>> a.__dict__
    {}
    >>> vars(a)
    {}
    >>> a.name = 'Kim'
    >>> a.__dict__
    {'name': 'Kim'}
    >>> vars(a)
    {'name': 'Kim'}
    ```
## 反射

- hasattr（object， name，default=None）判断objecct中有没有字符串name对应的方法或者属性
    ```
    #定义类A
    >>> class Student:
        def __init__(self,name):
            self.name = name


    >>> s = Student('Aim')
    >>> hasattr(s,'name') #a含有name属性
    True
    >>> hasattr(s,'age') #a不含有age属性
    False
    复制代码
    ```

- getattr（object， name， defualt=None）获取object中name的属性或者方法，没有则报错，defual指定的时候则不报错
    ```
    getattr（x, 'y'） == x.y
    #定义类Student
    >>> class Student:
        def __init__(self,name):
            self.name = name

    >>> getattr(s,'name') #存在属性name
    'Aim'

    >>> getattr(s,'age',6) #不存在属性age，但提供了默认值，返回默认值

    >>> getattr(s,'age') #不存在属性age，未提供默认值，调用报错
    Traceback (most recent call last):
      File "<pyshell#17>", line 1, in <module>
        getattr(s,'age')
    AttributeError: 'Stduent' object has no attribute 'age'
    ```
- setattr(objects,'key','value')  ==  object.key = value
    ```
    给object设置属性值和方法
    setattr(object, 'fun', lanbda x:x+1)
    >>> class Student:
    def __init__(self,name):
        self.name = name


    >>> a = Student('Kim')
    >>> a.name
    'Kim'
    >>> setattr(a,'name','Bob')
    >>> a.name
    'Bob'
    ```
- delattr（object， name）  == del object.name
    ```
    删除object的属性或者方法
    #定义类A
    >>> class A:
        def __init__(self,name):
            self.name = name
        def sayHello(self):
            print('hello',self.name)

    #测试属性和方法
    >>> a.name
    '小麦'
    >>> a.sayHello()
    hello 小麦

    #删除属性
    >>> delattr(a,'name')
    >>> a.name
    Traceback (most recent call last):
      File "<pyshell#47>", line 1, in <module>
        a.name
    AttributeError: 'A' object has no attribute 'na

    ```

- callable：检测对象是否可被调用
    ```
    >>>callable(0)
    False
    >>> callable("runoob")
    False
    >>> def add(a, b):
         return a + b
    >>> callable(add)             # 函数返回 True
    True
    >>> class A:                  # 类
    ...     def method(self):
    ...             return 0
    ...
    >>> callable(A)               # 类返回 True
    True
    >>> a = A()
    >>> callable(a)               # 没有实现 __call__, 返回 False
    False
    >>> class B:
    ...     def __call__(self):
    ...             return 0
    ...
    >>> callable(B)
    True
    >>> b = B()
    >>> callable(b)               # 实现 __call__, 返回 True
    True
    ```

## 变量操作
- globals：返回当前作用域内的全局变量和其值组成的字典
    ```
    >>> globals()
    {'__spec__': None, '__package__': None, '__builtins__': <module 'builtins' (built-in)>, '__name__': '__main__', '__doc__': None, '__loader__': <class '_frozen_importlib.BuiltinImporter'>}
    >>> a = 1
    >>> globals() #多了一个a
    {'__spec__': None, '__package__': None, '__builtins__': <module 'builtins' (built-in)>, 'a': 1, '__name__': '__main__', '__doc__': None, '__loader__': <class '_frozen_importlib.BuiltinImporter'>}
    ```
- locals：返回当前作用域内的局部变量和其值组成的字典
    ```
    >>> def f():
    print('before define a ')
    print(locals()) #作用域内无变量
    a = 1
    print('after define a')
    print(locals()) #作用域内有一个a变量，值为1


    >>> f
    <function f at 0x03D40588>
    >>> f()
    before define a
    {}
    after define a
    {'a': 1}
    ```
## 编译执行
- compile：将字符串编译为代码或者AST对象，使之能够通过exec语句来执行或者eval进行求值
    ```
    >>> #流程语句使用exec
    >>> code1 = 'for i in range(0,10): print (i)'
    >>> compile1 = compile(code1,'','exec')
    >>> exec (compile1)
    0
    1
    2
    3
    4
    5
    6
    7
    8
    9
    >>> #简单求值表达式用eval
    >>> a = compile('1+2+3','','eval')
    >>> eval(a)
    6
    ```
- eval：执行动态表达式求值
    ```
    >>> eval('1+2+3+4')
    10
    ```
- exec：执行动态语句块
    ```
    >>> exec('a=1+2') #执行语句
    >>> a
    3
    ```
- repr：返回一个对象的字符串表现形式(给解释器)
    ```
    >>> a = 'some text'
    >>> str(a)
    'some text'
    >>> repr(a)
    "'some text'"
    ```








