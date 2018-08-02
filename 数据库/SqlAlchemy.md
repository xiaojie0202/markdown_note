# ORM
    SORM 全称 Object Relational Mapping, 翻译过来叫对象关系映射。简单的说，ORM 将数据库中的表与面向对象语言中的类建立了一种对应关系。这样，我们要操作数据库，数据库中的表或者表中的一条记录就可以直接通过操作类或者类实例来完成。

![image](http://7xqdxb.com1.z0.glb.clouddn.com/orm1.png)

# SQLAlchemy
    SQLAlchemy 是Python 社区最知名的 ORM 工具之一，为高效和高性能的数据库访问设计，实现了完整的企业级持久模型。

## 安装
    pip install sqlalchemy

## 连接数据库
    在SQLAlchemy 中使用sqlalchemy.create_engine() 方法来简介数据库

>- 标准的调用形式是将URL作为第一个位置参数发送，通常是一个表示数据库链接信息和连接参数的字符串:
>- engine = create_engine('mysql+pymysql://root:linkidc@10.0.0.136:3306/school?charset=utf8')
>- 格式"dialect[+driver]://user:password@host/dbname[?key=value..]"

### 连接sqlite3
1. 驱动(driver)
    ```
    sqlite3是个文件数据库，不需要什么驱动，或者说python内置了驱动。
    ```
2. 标准连接参数
    ```
    sqlite://<nohostname>/<path>
    ```
3. 各种连接参数
    ```
    # 相对路径，就是这个python文件同目录下foo.db
    engine = create_engine('sqlite:///foo.db')

    #绝对路径
    #Unix/Mac下用四条////表示
    engine = create_engine('sqlite:////absolute/path/to/foo.db')

    #Windows下用三条///加盘符路径用两条\\
    engine = create_engine('sqlite:///C:\\path\\to\\foo.db')

    #Windows 也可以这么用三条///加盘符路径用一条\
    engine = create_engine(r'sqlite:///C:\path\to\foo.db')

    #数据库建在内存里。URI保持为空即可
    engine = create_engine('sqlite://')
    ```
### 连接MySQL
1. 驱动
    ```
    sqlalchemy默认使用mysql-python作为链接驱动，既default模式
    ```
2. 标准连接参数
    ```
    # 使用默认mysql-python作为链接驱动
    mysql://<user>:<password>@<host>[:<port>]/<dbname>'
    ```
3. 各种模块连接
    ```
    # MySQL-Python
        mysql+mysqldb://<user>:<password>@<host>[:<port>]/<dbname>
    # MySQL-pymysql
        mysql+pymysql://<username>:<password>@<host>/<dbname>[?<options>]
    # MySQL-Connector
        mysql+mysqlconnector://<user>:<password>@<host>[:<port>]/<dbname>
    cx_Oracle
        oracle+cx_oracle://user:pass@host:port/dbname[?key=value&key=value...]

    ```
### 连接Microsoft SQL Server
1. 驱动
    ```
    默认使用 pyodbc作为链接驱动
    ```
2. 标准参数
    ```
    # 使用默认的oyodbc作为连接驱动
    mssql+pyodbc://<user>:<password>@<host>[:<port>]/<dbname>
    ```
3. 其他连接驱动
    ```
    # pymssql
    mssql+pymssql://<user>:<password>@<host>[:<port>]/<dbname>
    ```
### 连接PostgreSQL
1. 驱动
    ```
    PostgreSQL默认使用 psycopg2作为链接驱动，既default模式
    ```
2. 标准连接参数
    ```
    postgresql://<user>:<password>@<host>[:<port>]/<dbname>
    ```
3. 各种模块驱动链接
    ```
    # psycopg2
    postgresql+psycopg2://<user>:<password>@<host>[:<port>]/<dbname>
    # pg8000
    postgresql+pg8000://<user>:<password>@<host>[:<port>]/<dbname>
    ```
### 连接Oracle
1. 驱动
    ```
    Oracle可能只有 cx_oracle一个驱动包，既default模式和声明模式一样。
    ```
2. 标准连接参数
    ```
    oracle://<user>:<password>@<host>[:<port>]/<dbname>
    or
    oracle+cx_oracle://<user>:<password>@<host>[:<port>]/<dbname>
    ```
### create_engine 参数
    create_engine的可选参数很多， 有几个比较常用的。
>- case_sensitive=True
>>- 如果False，结果列名将以不区分大小写的方式匹配,默认区分大小写

>- connect_args=None
>>- 连接数据的url后拼接的字符串 create_engine('sqlite3://?charset=utf8') 等于 create_engine('sqlite3://',connect, args = {'charset':"utf8"})

>- creator=None
>>- 返回DBAPI连接的可调用。这个创建函数将被传递到底层连接池，并将用于创建所有新的数据库连接。这个函数的使用会导致URL参数中指定的连接参数被忽略。

    ```
    def connect():
        return psycopg.connect(user='scott', host='localhost')
    db = create_engine('postgresql://', creator=connect)

    ```

>- echo=False
>>- 相当于Debug模式，会吧引擎记录的所有SQL 语句打印到控制台， 一般生产环境中设置为False

>- encoding='utf-8'
>>- 默认为utf - 8。这是SQLAlchemy用于字符串编码/解码操作的字符串编码

>- echo_pool=False
>>- 连接池的日志记录器

>- max_overflow=10
>>- 允许连接到数据库的最大连接数.

>- pool_size=5
>>- 在连接池中保持打开的连接数。这与QueuePool和SingletonThreadPool一起使用。对于QueuePool, pool_size设置为0表示没有限制;要禁用池，可以将poolclass设置为NullPool。

## 声明数据库映射的类
    在使用ORM时，配置过程首先描述我们将要处理的数据库表，然后定义我们自己的类，这些类将映射到这些表。在现代SQLAlchemy中，这两个任务通常是一起执行的，使用一个称为声明式的系统，它允许我们创建包含指令的类来描述它们将被映射到的实际数据库表。

1. 使用声明系统映射的类被定义为一个基类，它维护一个类和表相对于该基的目录——这被称为声明基类。我们的应用程序通常只在一个通常导入的模块中有这个基础的一个实例。我们使用declarative_base()函数创建基类
```
>>> from sqlalchemy.ext.declarative import declarative_base

>>> Base = declarative_base()
```
2. 有了一个“base”，我们可以用它来定义任意数量的映射类
```
from sqlalchemy import Column, Integer, String, Boolean, Date, create_engine, Float
# 2.1 使用Base基类来创建数据库映射表，每张映射表中必须存在__tablename__属性 和 至少一个主键
# 2.2 此映射表会有一个隐式的构造方法__init__() 会自动接收与我们映射的列相匹配的关键字名称
class Students(Base):
    __tablename__ = 'students' # 执行数据库中的表名
    id = Column(Integer, primary_key=True, autoincrement=True) # 数据库表中的主键i
    name = Column(String(20), nullable=False, unique=True)      #
    gender = Column(Boolean, default=1, nullable=False)
    birthday = Column(Date)

    def __repr__(self):
        return self.name
```
3. 通过使用声明系统构造的用户类，我们已经定义了关于表的信息，即表元数据。SQLAlchemy用来表示特定表的信息的对象称为表对象，这里声明为我们做了一个。通过检查__table__属性，我们可以看到这个对象:
```
>>> Students.__table__
Table('students', MetaData(bind=None), Column('id', Integer(), table=<students>, primary_key=True, nullable=False), Column('name', String(length=20), table=<students>, nullable=False), Column('gender', Boolean(), table=<students>, nullable=False, default=ColumnDefault(1)), Column('birthday', Date(), table=<students>), schema=None)

#  表对象是一个称为元数据( MetaData)的较大集合的成员。当使用声明性时，这个对象可以使用我们声明基类的.metadata属性。
```
4. 元数据( MetaData)是一个注册表，它包括向数据库发送执行命令。由于我们的SQLite数据库实际上没有一个sudents，所以我们可以使用元数据来为不存在的所有表向数据库发出CREATE TABLE语句。下面，我们调用MetaData.create_all()方法，将引擎作为数据库连接的源传递。我们将看到，首先发出特殊命令来检查students表的存在，并跟踪实际的CREATE table语句:
```
>>> Base.metadata.create_all(engin)
CREATE TABLE students (
	id INTEGER NOT NULL AUTO_INCREMENT,
	name VARCHAR(20) NOT NULL,
	gender BOOL NOT NULL,
	birthday DATE,
	PRIMARY KEY (id),
	UNIQUE (name),
	CHECK (gender IN (0, 1))
)
2018-04-15 17:23:01,611 INFO sqlalchemy.engine.base.Engine {}
2018-04-15 17:23:01,627 INFO sqlalchemy.engine.base.Engine COMMIT

```
5. 此外，Firebird和Oracle需要序列来生成新的主键标识符，而SQLAlchemy不会在没有指示的情况下生成或假设这些标识符。为此，您使用序列构造:
```
from sqlalchemy import Sequence
class Students(Base):
    __tablename__ = 'students'
    id = Column(Integer, primary_key=True, autoincrement=True, Sequence('students_id_seq'))
    name = Column(String(20), nullable=False, unique=True)
    gender = Column(Boolean, default=1, nullable=False)
    birthday = Column(Date)

    def __repr__(self):
        return self.name
```
## 数据库映射类的使用 - 增加
1. 先实例化声明的数据库表的类
```
>>> student = Students(name='小杰', gender=False, birthday=str(datetime.datetime.now().date()))
>>> student.name
'小杰'
>>> student.birthday
'2018-04-15'
即使我们没有在构造函数中指定ID，SQLAlchemy的工具通常在第一次访问时为列映射属性生成这个默认值。对于我们实际分配了一个值的那些属性，检测系统正在跟踪这些赋值，以便在最终的INSERT语句中被发送到数据库。
```
2. 现在我们可以开始与数据库对话了。ORM对数据库的“句柄”是Session。当我们第一次设置应用程序时，与create_engine()语句的级别相同，我们定义了一个Session 类，它将作为新Session object的工厂
```
>>> from sqlalchemy.orm import sessionmaker
>>> Session = sessionmaker(bind=engine)

# 在还没有定义引擎的时候可以先定义一个Session类，然后在绑定引擎
>>> Session = sessionmaker()
>>> Session.configure(bind=engine)  # once engine is available
# 这个定制的会话类将创建绑定到我们数据库的新会话对象。在调用sessionmaker时也可以定义其他事务特性
# 每次需要会话的时候都需要实例化一个Session
session = Session()
```
3. 需要把实例化的数据库映射表的实例添加到session中
```
# 此时并未向数据库中发送SQL语句，只是在缓冲区中等待
session.add(student)

# 也可以使用add_all()添加多个实例
students_list = []

for i in range(1,5):
    students_list.append(Students(name='张%s' % i, gender=i%2, birthday='2014-1-1'))

session.add_all(students_list)

# 查看session中的实列
session.now
```
4. 由于会话在事务中工作，所以我们也可以回滚更改。
```
>>> session.rollback()
```
5. 数据修改完，增加完后需要提交事务
```
session.commit()
2018-04-15 18:18:38,490 INFO sqlalchemy.engine.base.Engine BEGIN (implicit)
2018-04-15 18:18:38,492 INFO sqlalchemy.engine.base.Engine INSERT INTO students (name, gender, birthday) VALUES (%(name)s, %(gender)s, %(birthday)s)
2018-04-15 18:18:38,492 INFO sqlalchemy.engine.base.Engine {'name': '小杰', 'gender': 0, 'birthday': '2018-04-15'}
2018-04-15 18:18:38,494 INFO sqlalchemy.engine.base.Engine INSERT INTO students (name, gender, birthday) VALUES (%(name)s, %(gender)s, %(birthday)s)
2018-04-15 18:18:38,494 INFO sqlalchemy.engine.base.Engine {'name': '张1', 'gender': 1, 'birthday': '2014-1-1'}
2018-04-15 18:18:38,495 INFO sqlalchemy.engine.base.Engine INSERT INTO students (name, gender, birthday) VALUES (%(name)s, %(gender)s, %(birthday)s)
2018-04-15 18:18:38,495 INFO sqlalchemy.engine.base.Engine {'name': '张2', 'gender': 0, 'birthday': '2014-1-1'}
2018-04-15 18:18:38,497 INFO sqlalchemy.engine.base.Engine INSERT INTO students (name, gender, birthday) VALUES (%(name)s, %(gender)s, %(birthday)s)
2018-04-15 18:18:38,497 INFO sqlalchemy.engine.base.Engine {'name': '张3', 'gender': 1, 'birthday': '2014-1-1'}
2018-04-15 18:18:38,498 INFO sqlalchemy.engine.base.Engine INSERT INTO students (name, gender, birthday) VALUES (%(name)s, %(gender)s, %(birthday)s)
2018-04-15 18:18:38,498 INFO sqlalchemy.engine.base.Engine {'name': '张4', 'gender': 0, 'birthday': '2014-1-1'}
2018-04-15 18:18:38,499 INFO sqlalchemy.engine.base.Engine COMMIT
2018-04-15 18:18:38,516 INFO sqlalchemy.engine.base.Engine BEGIN (implicit)
2018-04-15 18:18:38,517 INFO sqlalchemy.engine.base.Engine SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
FROM students
WHERE students.id = %(param_1)s
2018-04-15 18:18:38,517 INFO sqlalchemy.engine.base.Engine {'param_1': 1}
2018-04-15 18:18:38,564 INFO sqlalchemy.engine.base.Engine SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
FROM students
WHERE students.id = %(param_1)s
2018-04-15 18:18:38,565 INFO sqlalchemy.engine.base.Engine {'param_1': 2}
2018-04-15 18:18:38,566 INFO sqlalchemy.engine.base.Engine SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
FROM students
WHERE students.id = %(param_1)s
2018-04-15 18:18:38,566 INFO sqlalchemy.engine.base.Engine {'param_1': 3}
2018-04-15 18:18:38,567 INFO sqlalchemy.engine.base.Engine SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
FROM students
WHERE students.id = %(param_1)s
2018-04-15 18:18:38,568 INFO sqlalchemy.engine.base.Engine {'param_1': 4}
2018-04-15 18:18:38,569 INFO sqlalchemy.engine.base.Engine SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
FROM students
WHERE students.id = %(param_1)s
2018-04-15 18:18:38,569 INFO sqlalchemy.engine.base.Engine {'param_1': 5}
```

## ORM-删除
1. 先查询踹需要删除的数据

    ```del_students = session.query(Students).filter_by(name='张3').first()```
2. 添加到session的删除队列中

    ```session.delete(del_students)```
3. 提交事务

    ```session.commit()```

## ORM-修改数据
1. 先查询到需要修改的数据

    ```a = session.query(Students).filter_by(name='张4').first()```
2. 修改value

    ```a.name = '王菲'```
3. 修改的数据存储在session.dirty
    ```
    >>> session.dirty
    IdentitySet([<Students(name=王菲,gender=False,birthday=2014-01-01)>])
    ```
4. 提交事务
    ```session.commit()```

## 数据库映射类的使用 - 查询
>- 查询某张表中所有的数据
>>- session.query(映射类名).all()

    ```
    >>> session.query(Students)  # 惰性查询，并未真的去数据库中获取数据

    <sqlalchemy.orm.query.Query object at 0x00000156C0591D30> # 可迭代对象，是映射类组成的

    >>> session.query(Students).all()

    SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
    FROM students

    [小杰, 张1, 张2, 张3, 张4] 返回一个list，是映射类组成的
    ```
>- 查询表中指定格式的数据
>>- session.query(映射类名.映射类的字段名).all()

    ```
    >>> session.query(Students.name).all() # 查询所有学生的姓名

    SELECT students.name AS students_name FROM students

    [('小杰',), ('张1',), ('张2',), ('张3',), ('张4',)]

    >>> for i in session.query(Students.name.label('stu_name')): # 指定查询每行数据的别名
            print(i.stu_name, i)

    SELECT students.name AS stu_name FROM students

    小杰 ('小杰',)
    张1 ('张1',)
    张2 ('张2',)
    张3 ('张3',)
    张4 ('张4',)

    # 给指定映射表类设置一个别名
    >>> from sqlalchemy.orm import aliased
        stu_alias = aliased(Students, name='stu') # 给Students设置一个别名

    >>> for i in session.query(stu_alias, stu_alias.name):
            print(i)

    SELECT stu.id AS stu_id, stu.name AS stu_name, stu.gender AS stu_gender, stu.birthday AS stu_birthday FROM students AS stu

    (<Students(name=小杰,gender=False,birthday=2018-04-15)>, '小杰')
    (<Students(name=张1,gender=True,birthday=2014-01-01)>, '张1')
    (<Students(name=张2,gender=False,birthday=2014-01-01)>, '张2')
    (<Students(name=张3,gender=True,birthday=2014-01-01)>, '张3')
    (<Students(name=张4,gender=False,birthday=2014-01-01)>, '张4')
    ```
>- 排序
>>- session.query(‘数据库映射类’).order_by('列名', '列名')

    ```
    >>> session.query(Students).order_by('name', 'id').all() # 跟进Students表中name和id字段进行排序

    SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
    FROM students ORDER BY students.name, students.id

    [<Students(name=小杰,gender=False,birthday=2018-04-15)>,
    <Students(name=张1,gender=True,birthday=2014-01-01)>,
    <Students(name=张2,gender=False,birthday=2014-01-01)>,
    <Students(name=张3,gender=True,birthday=2014-01-01)>,
    <Students(name=张4,gender=False,birthday=2014-01-01)>]
    ```

>- limit（分页）
>- session.query(Students).order_by('name', 'id')[1:3]

    ```
    >>> session.query(Students).order_by('name', 'id')[1:3]

    SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
    FROM students ORDER BY students.name, students.id
     LIMIT %(param_1)s, %(param_2)s

    [<Students(name=张1,gender=True,birthday=2014-01-01)>, <Students(name=张2,gender=False,birthday=2014-01-01)>]
    ```

>- 过滤
>>- session.query(Students).filter_by(name='小杰')

    ```
    >>> session.query(Students).filter_by(name='小杰').all()

    SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
    FROM students
    WHERE students.name = %(name_1)s

    [<Students(name=小杰,gender=False,birthday=2018-04-15)>]
    ```
>>- session.query(Students).filter(Students.name=='小杰').all()

    ```
    >>> session.query(Students).filter(Students.name=='小杰').all()

    SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
    FROM students
    WHERE students.name = %(name_1)s

    [<Students(name=小杰,gender=False,birthday=2018-04-15)>]
    ```

>>>- filter()过滤器的操作符

    ```
    相等:
        query(Students).filter(Students.name == '小杰')

    不等:
        query(Students).filter(Students.name != '小杰')

     大于，小于，大于等于，小于等于
        query(Students).filter(Students.id > 1)
        query(Students).filter(Students.id < 1)
        query(Students).filter(Students.id >= 1)
        query(Students).filter(Students.id <= 1)


    模糊匹配区分大小写 LIKE:
        query(Students).filter(Students.name.like('%张'))

    模糊匹配不许分大小写 ILIKE:
        query(Students).filter(Students.name.like('张%'))

    IN:
        query(Students).filter(Students.name.in_(['张1','张2']))
        session.query(Students).filter(Students.name.in_(session.query(Students.name).filter(Students.name.like('张%'))))

    NOT IN:
        query(Students).filter(~Students.name.in_(['张1','张2']))

    IS NULL:
        query.filter(User.name == None)
        query.filter(User.name.is_(None))

    IS NOT NULL:
        query.filter(User.name != None)
        query.filter(User.name.isnot(None))

    AND:
        from sqlalchemy import and_
        query.filter(and_(User.name == 'ed', User.fullname == 'Ed Jones'))

        # or send multiple expressions to .filter()
        query.filter(User.name == 'ed', User.fullname == 'Ed Jones')

        # or chain multiple filter()/filter_by() calls
        query.filter(User.name == 'ed').filter(User.fullname == 'Ed Jones')

    OR:

    from sqlalchemy import or_
    query.filter(or_(User.name == 'ed', User.name == 'wendy'))

    MATCH:
    query.filter(User.name.match('wendy'))
    ```
>- count()：计算查询到的条数
    ```
    >>> session.query(Students).count()

    SELECT count(*) AS count_1
    FROM (SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
    FROM students) AS anon_1

    4
    ```
>- 获取查询后数据格式
>>- query.all() # 获取所有查询的数据的列表
>>- query.first()    # 获取查询到的数据集中的第一个

## 字符串SQL语句
    文字字符串可以灵活地使用查询，通过指定它们与text()构造的使用，这是大多数适用的方法所接受的。例如，filter()和order_by()
1. 基本使用
    ```
    from sqlalchemy import text

    >>> session.query(Students).filter(text('id<2')).all()

    SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
    FROM students
    WHERE id<2

    [<Students(name=小杰,gender=False,birthday=2018-04-15)>]

    ```
2. 可以使用params() 来格式化 text() 中用“：”指定的值
    ```
    >>> session.query(Students).filter(text('id>=:value and name=:name')).params(value=1, name='小杰').all()

    SELECT students.id AS students_id, students.name AS students_name, students.gender AS students_gender, students.birthday AS students_birthday
    FROM students
    WHERE id>=%(value)s and name=%(name)s

    [<Students(name=小杰,gender=False,birthday=2018-04-15)>]

    >>> session.query(Students).from_statement(text('select * from students where name=:name')).params(name='小杰').all()
    ```

## SQLAlchemy高级- 数据库映射类实现外键

### 一对多
    在博客中，一个用户可以有多篇文章
1. 实现描述表结构

```
from sqlalchemy import Column, Integer, String, Boolean, Date, create_engine, ForeignKey, Float, Text
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm.session import sessionmaker
from sqlalchemy.orm import aliased, relationship

engin = create_engine('mysql+pymysql://root:linkidc@10.0.0.136:3306/school?charset=utf8', echo=True)

Base = declarative_base()  # 生成orm基类

# 用户表
class Users(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True, autoincrement=True, nullable=False)
    name = Column(String(20), nullable=False, unique=True)

# 文章表
class Articles(Base):
    __tablename__ = 'article'
    id = Column(Integer, primary_key=True, autoincrement=True, nullable=False)
    article = Column(Text)

    # user_id列 外键到 users表的id
    user_id = Column(Integer, ForeignKey('users.id'))

    # 持有文章所对应的用户
    user = relationship('Users', backref='article')

    # 使用backref参数时候相当于在Users类中创建这样一条语句
    # article = relationship('Articles', backref='users')

    # 如果文中使用back_populates 单项引用的话，则需要手动在Users类中添加 article = relationship('Articles', back_populates='users')，才能等于backref的属性
    # user = relationship('Users', back_populates='article')

```
2. 一对多  增加数据
```
>>> user = Users(name='小杰')
>>> user.article.append(Articles(article='我是文章1'))
>>> user.article.append(Articles(article='我是文章2'))
>>> user.article
    [<Articles object at 0x000001D4DB9E8CF8>, <Articles object at 0x000001D4DA996EB8>]

>>> session.add(user)
>>> session.new
    IdentitySet([<Users object at 0x000001D4DB9CFEF0>, <Articles object at 0x000001D4DB9E8CF8>, <Articles object at 0x000001D4DA996EB8>])
```
3. 一对多  删除数据
4. 一对多  修改数据
5. 一对多  查询数据


### 一对一
    在实际环境中，每个学生都有一个地址

从本质上说，一对一是双向关系，两边都是标量属性。为了实现这一点，uselist标志指示了一个标量属性的位置，而不是在关系的“many”方面的集合。将一对多转换为一对一:

```
    from sqlalchemy import Column, Integer, String, Boolean, Date, create_engine, ForeignKey, Float, Text
    from sqlalchemy.ext.declarative import declarative_base
    from sqlalchemy.orm.session import sessionmaker
    from sqlalchemy.orm import aliased, relationship

    engin = create_engine('mysql+pymysql://root:linkidc@10.0.0.136:3306/school?charset=utf8', echo=True)

    Base = declarative_base()  # 生成orm基类

    class Students(Base):
        __tablename__ = 'students'
        id = Column(Integer, primary_key=True, autoincrement=True)
        name = Column(String(20), nullable=False, unique=True)
        gender = Column(Boolean, default=1, nullable=False)
        birthday = Column(Date)

        # 引用至Addresses表中，实现一对一关系
        address = relationship('Addresses', back_populates='student', uselist=False)

        def __repr__(self):
            return '<Students name=%s, gender=%s, birthday=%s>' % (self.name, self.gender, self.birthday)


    class Addresses(Base):
        __tablename__ = 'address'
        id = Column(Integer, primary_key=True, autoincrement=True)
        emil = Column(String(64), nullable=False)

        stu_id = Column(Integer, ForeignKey('students.id'))
        student = relationship('Students', back_populates='address')

        def __repr__(self):
            return '<Address emil=%s, stu_id=%s>' % (self.emil, self.stu_id)
```
1. 一对一 增加数据
2. 一对一 删除数据
3. 一对一 修改数据
4. 一对一 查询数据

### 多对多
    多对多就是在数据库中新建一张表用来保存关系, 在SQLAlchemey中用secondary指定关系表

在实际环境中，一个用户有多钟角色，一个角色可以分配给多个用户

1. 表结构实现
```
from sqlalchemy import Column, Integer, String, Boolean, Date, create_engine, ForeignKey, Float, Text, Table
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm.session import sessionmaker
from sqlalchemy.orm import aliased, relationship

engin = create_engine('mysql+pymysql://root:linkidc@10.0.0.136:3306/school?charset=utf8', echo=True)

Base = declarative_base()  # 生成orm基类


class Users(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key=True, nullable=False, autoincrement=True)
    name = Column(String(20), nullable=True)
    roles = relationship('Roles', secondary='user_role', back_populates='users')


class Roles(Base):
    __tablename__ = 'roles'
    id = Column(Integer, primary_key=True, nullable=False, autoincrement=True)
    name = Column(String(20), nullable=False, unique=True)
    users = relationship('Users', secondary='user_role', back_populates='roles')


class UserToRoles(Base):
    __tablename__ = 'user_role'
    id = Column(Integer, primary_key=True, nullable=False, autoincrement=True)
    user_id = Column(Integer, ForeignKey('users.id', ondelete='CASCADE', onupdate='CASCADE'))
    role_id = Column(Integer, ForeignKey('roles.id', ondelete='CASCADE', onupdate='CASCADE'))

    users = relationship('Users', backref='f_roles')
    roles = relationship('Roles', backref='f_users')
```
2. 多对多增加
3. 多对多删除
4. 多对多修改
5. 多对多查询