# mysql数据库
     数据库（database，DB）是指长期存储在计算机内的，有组织，可共享的数据的集合。数据库中的数据按一定的数学模型组织、描述和存储，具有较小的冗余，较高的数据独立性和易扩展性，并可为各种用户共享。
# 数据库管理软件
```
    数据库管理系统(Database Management System)是一种操纵和管理数据库的大型软件，用于建立、使用和维护数据库，简称DBMS。它对数据库进行统一的管理和控制，以保证数据库的安全性和完整性。用户通过DBMS访问数据库中的数据，数据库管理员也通过dbms进行数据库的维护工作。它可使多个应用程序和用户用不同的方法在同时或不同时刻去建立，修改和询问数据库。大部分DBMS提供数据定义语言DDL（Data Definition Language）和数据操作语言DML（Data Manipulation Language），供用户定义数据库的模式结构与权限约束，实现对数据的追加、删除等操作。
数据库管理系统是数据库系统的核心，是管理数据库的软件。数据库管理系统就是实现把用户意义下抽象的逻辑数据处理，转换成为计算机中具体的物理数据处理的软件。有了数据库管理系统，用户就可以在抽象意义下处理数据，而不必顾及这些数据在计算机中的布局和物理位置。
常见的数据库管理软件：甲骨文的oracle,IBM的db2，sql server, Access,Mysql(开源，免费，跨平台).
```
# 数据库系统
    数据库系统DBS（Data Base System，简称DBS）通常由软件、数据库和数据管理员组成。其软件主要包括操作系统、各种宿主语言、实用程序以及数据库管理系统。数据库由数据库管理系统统一管理，数据的插入、修改和检索均要通过数据库管理系统进行。数据管理员负责创建、监控和维护整个数据库，使数据能被任何有权使用的人有效使用。

[img](https://images2015.cnblogs.com/blog/877318/201702/877318-20170205222624323-946460904.png)

# Mysql数据存储引擎
>- InnoDB
>>- 支持事务处理，支持外键，支持崩溃修复能力和并发控制。如果需要对事务的完整性要求比较高（比如银行），要求实现并发控制（比如售票），那选择InnoDB有很大的优势。如果需要频繁的更新、删除操作的数据库，也可以选择InnoDB，因为支持事务的提交（commit）和回滚（rollback）。
>- MyISAM
>>- 插入数据快，空间和内存使用比较低。如果表主要是用于插入新记录和读出记录，那么选择MyISAM能实现处理高效率。如果应用的完整性、并发性要求比 较低，也可以使用。
>- MEMORY
>>- 所有的数据都在内存中，数据的处理速度快，但是安全性不高。如果需要很快的读写速度，对数据的安全性要求较低，可以选择MEMOEY。它对表的大小有要求，不能建立太大的表。所以，这类数据库只使用在相对较小的数据库表。
# Mysql 基础
    关系型数据库，存储在硬盘上，（非关系型数据库存储在内存中）
## 简单使用
>- 安装
    ```
    --yum -y install mariadb mariadb-server
    OR
    --yum -y install mysql mysql-server
    ```
>- 启动
    ```
    --service mysqld start  #开启
    --chkconfig mysqld on   #设置开机自启
    OR
    --systemctl start mariadb
    --systemctl enable mariadb
    ```
>- 查看
    ```
    -- ps aux |grep mysqld    #查看进程
    -- netstat -an |grep 3306 #查看端口
    ```
>- 设置密码
    ```
    -- mysqladmin -uroot password '123'   #设置初始密码，初始密码为空因此-p选项没有用
    -- mysqladmin -u root -p123 password '1234' #修改root用户密码
    ```

>- 登录
    ```
    -- mysql               #本地登录，默认用户root，空密码，用户为root@127.0.0.1
    -- mysql -uroot -p1234 #本地登录，指定用户名和密码，用户为root@127.0.0.1
    -- mysql -uroot -p1234 -h 192.168.31.95 #远程登录，用户为root@192.168.31.95
    ```
## E-R模型
- 当前物理的数据库都是按照E-R模型进行设计的
- E表示entry 实体
- R 表示relationship，关系
- 一个实体转换为数据库中的一个表
- 关系描述两个实体之间的对应规则
    * 一对一
    * 一对多
    * 多对多
- 关系转换为是数据库中的一个列*在关系型数据库中的一行就是一个对象
## 三范式
    经过研究和对使用中问题总结，对于设计数据库提出一些规范，被称为范式
- 第一范式（1NF）： 列不可拆分
- 第二范式（2NF）：唯一标示
- 第三范式范式 （3NF）：引用主键
## 数据库-mysql支持的数据类型
>- 字符串类型
- char	String(0~255)
- varchar	String(0~255)
- tinytext	String(0~255)
- text	String(0~65536)
- blob	String(0~65536)
- mediumtext	String(0~16777215)
- mediumblob	String(0~16777215)
- longblob	String(0~4294967295)
- longtext	String(0~4294967295)
>- 数值类型
- tinyint	Integer(-128~127)
- smallint	Integer(-32768~32767)
- mediumint	Integer(-8388608~8388607)
- int	Integer(-214847668~214847667)
- bigint	Integer(-9223372036854775808~9223372036854775807)
- float	decimal(精确到23位小数)
- double	decimal(24~54位小数)
- decimal	将double转储为字符串形式
>- 日期类型
- date	YYYY-MM-DD
- datetime	YYYY-MM-DD HH:MM:SS
- timestamp	YYYYMMDDHHMMSS
- time	HH:MM:SS
>- 其他
- enum	选项值之一
- set	选项值子集
- boolean	tinyint(1)
## 数据库-mysql约束
- 主键 primary key
- 非空 not null
- 唯一 unique
- 默认 default
- 外键 foreign key
## SQL
    sql语句使用来操作是操作数据库的
### 数据库操作
> - 创建数据库
>>- create database 数据库名称 charset=utf8;

    MariaDB [(none)]> create database python3 charset=utf8;
    Query OK, 1 row affected (0.00 sec)
>- 删除数据库
>>- drop database 数据库名;

    MariaDB [(none)]> drop database python3;
    Query OK, 0 rows affected (0.00 sec)
>- 切换数据库
>>- use 数据库名;

    MariaDB [(none)]> use students;
    Database changed
    MariaDB [students]>
>- 查看当前选择的数据库
>>- select database();

    MariaDB [students]> select database();
    +------------+
    | database() |
    +------------+
    | students   |
    +------------+
    1 row in set (0.00 sec)
>- 查看所有数据库
>>- show databases;

    MariaDB [students]> show databases;
    +--------------------+
    | Database           |
    +--------------------+
    | information_schema |
    | mysql              |
    | performance_schema |
    | students           |
    | test               |
    +--------------------+
    5 rows in set (0.01 sec)
### 数据库表操作
>- 创建表
>>- CREATE TABLE 表名称(列名 类型 约束)

    MariaDB [students]> CREATE TABLE studentss(
    -> id INT AUTO_INCREMENT PRIMARY KEY NOT NULL, # auto_increment 自增
    -> name VARCHAR(20） NOT NULL,
    -> gender BIT DEFAULT 1,
    -> birthday DATETIME);
    Query OK, 0 rows affected (0.00 sec)
>- 删除表
>- DROP TABLE 表名称;

    MariaDB [students]> DROP TABLE students;
    Query OK, 0 rows affected (0.01 sec)
>- 修改表名
>>- RENAME TABLE 原表名 TO 新表明;

    MariaDB [students]> RENAME TABLE studentss TO students;
    Query OK, 0 rows affected (0.00 sec)
>- 修改表结构
>- ALTER TABLE 表名 ADD|CHANGE|DROP 列名 类型；

    add:增加，change:修改(只能修改列的类型，名字不能改),drop:删除
    MariaDB [students]> ALTER TABLE students ADD isDelete BIT DEFAULT 0;
    Query OK, 0 rows affected (0.02 sec)
    Records: 0  Duplicates: 0  Warnings: 0
>- 查看当前数据库中所有表
>>- show tables;

    MariaDB [students]>  show tables;
    +--------------------+
    | Tables_in_students |
    +--------------------+
    | students           |
    +--------------------+
    1 row in set (0.00 sec)
>- 查看表结构
>>- DESC 表名;

    MariaDB [students]> DESC studentss;
    +----------+-------------+------+-----+---------+----------------+
    | Field    | Type        | Null | Key | Default | Extra          |
    +----------+-------------+------+-----+---------+----------------+
    | id       | int(11)     | NO   | PRI | NULL    | auto_increment |
    | name     | varchar(20) | NO   |     | NULL    |                |
    | gender   | bit(1)      | YES  |     | b'1'    |                |
    | birthday | datetime    | YES  |     | NULL    |                |
    +----------+-------------+------+-----+---------+----------------+
    4 rows in set (0.00 sec)
>- 查看表创建SQL语句
>>- SHOW CREATE TABLE '表名';

    MariaDB [students]> SHOW CREATE TABLE students;
    +----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | Table    | Create Table                                                                                                                                                                                                                   |
    +----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | students | CREATE TABLE `students` (
      `id` int(11) NOT NULL AUTO_INCREMENT,
      `name` varchar(20) NOT NULL,
      `gender` bit(1) DEFAULT b'1',
      `birthday` datetime DEFAULT NULL,
      PRIMARY KEY (`id`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
    +----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    1 row in set (0.00 sec)
### 数据库表数据操作

### 增加
>- 全列插入
>>- INSET INTO 表名 VALUES(值，值....)

    ```
    全列插入的时候要求值对应表结构 id,name,gender,birthday
    MariaDB [python3]> INSERT INTO students VALUES(0,'小杰',1,'2018-1-1');
    Query OK, 1 row affected (0.00 sec)
    ```
>- 缺省插入
>>- INSERT INTO 表名(列1，列2....) VALUES(值1，值2....)

    ```
    MariaDB [python3]> INSERT INTO students(name,birthday) VALUES('火狐','1998-2-2');
    Query OK, 1 row affected (0.01 sec)
    ```
>- 同时插入多条数据
>>- INSERT INTO 表名 VALUES(值...),(值....),(值...).....

    ```
    MariaDB [python3]> INSERT INTO students VALUES(0,'小杰1',1,'2018-2-5'),
                                                    (0,'小杰2',1,'2018-9-9');
    Query OK, 2 rows affected (0.00 sec)
    Records: 2  Duplicates: 0  Warnings: 0
    ```
>>- INSERT INTO 表名(列1,...) VALUES('值1'...),('值1'...)

    ```
    MariaDB [python3]> INSERT INTO students(name,birthday)
                    VALUES('火狐1','1998-8-8'),('火狐2','1998-7-7');
    Query OK, 2 rows affected (0.00 sec)
    Records: 2  Duplicates: 0  Warnings: 0
    ```
### 修改
>- 修改数据
>>- UPDATE 表名 SET 列=值 ... WHERE 条件；

    ```
    MariaDB [python3]> UPDATE students SET name='谷歌' WHERE id=2;
    Query OK, 1 row affected (0.01 sec)
    Rows matched: 1  Changed: 1  Warnings: 0
    ```
### 删除
>- 物理删除
>>- DELETE FROM 表名 WHERE 条件;

    ```
    MariaDB [python3]> DELETE FROM students WHERE id=2;
    Query OK, 1 row affected (0.01 sec)
    ```
### 查询
#### 基础查询
>- 查询表中所有字段
>- SELECT * FROM 表名;

    ```
    MariaDB [python3]> select * from students;
    +----+---------+--------+---------------------+
    | id | name    | gender | birthday            |
    +----+---------+--------+---------------------+
    |  1 | 小杰    |       | 2018-01-01 00:00:00 |
    |  3 | 小杰1   |       | 2018-02-05 00:00:00 |
    |  4 | 小杰2   |       | 2018-09-09 00:00:00 |
    |  5 | 火狐1   |       | 1998-08-08 00:00:00 |
    |  6 | 火狐2   |       | 1998-07-07 00:00:00 |
    +----+---------+--------+---------------------+
    5 rows in set (0.00 sec)
    ```
>- 查询表中指定列的数据
>>- SELECT 列名，列名... FROM 表

    ```
    MariaDB [python3]> select id,name from students;
    +----+---------+
    | id | name    |
    +----+---------+
    |  1 | 小杰    |
    |  3 | 小杰1   |
    |  4 | 小杰2   |
    |  5 | 火狐1   |
    |  6 | 火狐2   |
    +----+---------+
    5 rows in set (0.00 sec)
    ```
>- 查询表中指定列的数据，并消除重复行
>>- SELECT DISTINCT 列名 FROM 表名;

    ```
    MariaDB [python3]> select distinct birthday from students;
    +---------------------+
    | birthday            |
    +---------------------+
    | 2018-01-01 00:00:00 |
    | 2018-02-05 00:00:00 |
    | 1998-02-02 00:00:00 |
    +---------------------+
    3 rows in set (0.00 sec)
    ```
#### 条件查询
    where语句对查询结果进行筛选
>- WHERE 筛选支持的运算符

    ```
    比较运算符:=，<,>,<=,>=,!=,<>
    逻辑运算符:  and, or, not
    优先级: 小括号，not， 比较运算符，罗技运算符
    MariaDB [python3]> select * from students where name='小杰' and id =1;
    +----+--------+--------+---------------------+
    | id | name   | gender | birthday            |
    +----+--------+--------+---------------------+
    |  1 | 小杰   |       | 2018-01-01 00:00:00 |
    +----+--------+--------+---------------------+
    1 row in set (0.00 sec)
    ```
#### 模糊查询
    like 语句用来模糊查询，%表示任意多个组反映，_表示一个任意字符
>- 模糊查询
>>- SEKECT * FROM 表名 WHERE 列名 like '%字符_'

    ```
    MariaDB [python3]> SELECT * FROM students WHERE name like '小%';
    +----+---------+--------+---------------------+
    | id | name    | gender | birthday            |
    +----+---------+--------+---------------------+
    |  1 | 小杰    |       | 2018-01-01 00:00:00 |
    |  3 | 小杰1   |       | 2018-02-05 00:00:00 |
    |  4 | 小杰2   |       | 1998-02-02 00:00:00 |
    +----+---------+--------+---------------------+
    3 rows in set (0.00 sec)


    MariaDB [python3]> SELECT * FROM students WHERE name like '小_';
    +----+--------+--------+---------------------+
    | id | name   | gender | birthday            |
    +----+--------+--------+---------------------+
    |  1 | 小杰   |       | 2018-01-01 00:00:00 |
    +----+--------+--------+---------------------+
    1 row in set (0.00 sec)
    ```
#### 范围不查询
>- in 不连续范围查询
>>- SELECT * FROM 表名 WHERE 列 in(值1，值2...)

    ```
    MariaDB [python3]> select * from students where id in(1,3,8);
    +----+---------+--------+---------------------+
    | id | name    | gender | birthday            |
    +----+---------+--------+---------------------+
    |  1 | 小杰    |       | 2018-01-01 00:00:00 |
    |  3 | 小杰1   |       | 2018-02-05 00:00:00 |
    +----+---------+--------+---------------------+
    2 rows in set (0.00 sec)
    ```
>- between ... and ...表示在一个范围内查询
>- SELECT * FROM 表名 WHERE id BETWEEN 值 AND 值

    ```
    查询编号3-8的男生
    MariaDB [python3]> SELECT * FROM students WHERE id BETWEEN 1 AND 8 AND gender=1;
    +----+---------+--------+---------------------+
    | id | name    | gender | birthday            |
    +----+---------+--------+---------------------+
    |  1 | 小杰    |       | 2018-01-01 00:00:00 |
    |  3 | 小杰1   |       | 2018-02-05 00:00:00 |
    |  4 | 小杰2   |       | 1998-02-02 00:00:00 |
    |  5 | 火狐1   |       | 1998-02-02 00:00:00 |
    |  6 | 火狐2   |       | 1998-02-02 00:00:00 |
    +----+---------+--------+---------------------+
    5 rows in set (0.00 sec)
    ```
#### 空判断
>- IS NULL 为空的
>>- SELECT * FROM 表名 WHERE 值 IS NULL;

    ```
    MariaDB [python3]> SELECT * FROM students WHERE hometown IS NULL;
    +----+---------+--------+---------------------+----------+
    | id | name    | gender | birthday            | hometown |
    +----+---------+--------+---------------------+----------+
    |  4 | 小杰2   |       | 1998-02-02 00:00:00 | NULL     |
    |  5 | 火狐1   |       | 1998-02-02 00:00:00 | NULL     |
    |  6 | 火狐2   |       | 1998-02-02 00:00:00 | NULL     |
    +----+---------+--------+---------------------+----------+
    3 rows in set (0.00 sec)
    ```
>- IS NOT NULL 不为空的
>>- SELECT * FROM 表 WHERE 值 IS NOT NULL;

    ```
    查询填写了地址的女生
    MariaDB [python3]> SELECT * FROM students WHERE hometown IS NOT NULL AND gender=1;
    +----+---------+--------+---------------------+----------+
    | id | name    | gender | birthday            | hometown |
    +----+---------+--------+---------------------+----------+
    |  1 | 小杰    |       | 2018-01-01 00:00:00 | 家里     |
    |  3 | 小杰1   |       | 2018-02-05 00:00:00 | 家里     |
    +----+---------+--------+---------------------+----------+
    2 rows in set (0.00 sec)
    ```
## 聚合函数
### count(*) 计算总行数，括号中写列名称或者*
>>-SELECT COUNT(*) FROM 表 WHERE 条件

    ```
    查询没有填写地址的学生个数
    MariaDB [python3]> SELECT COUNT(*) FROM students WHERE hometown IS NULL;
    +----------+
    | COUNT(*) |
    +----------+
    |        3 |
    +----------+
    1 row in set (0.00 sec)
    ```
### MAX(列)表示此列的最大值
>- SELECT MAX(列) FROM 表;

    ```
    id最大的人
    MariaDB [python3]> SELECT MAX(id) FROM students;
    +---------+
    | MAX(id) |
    +---------+
    |       6 |
    +---------+
    1 row in set (0.00 sec)
    ```
### MIN(列) 表示求此列最小的
>- SELECT MIN(列) FROM 表;

    ```
    MariaDB [python3]> select * from students where id=(select max(id) from students);
    +----+---------+--------+---------------------+----------+
    | id | name    | gender | birthday            | hometown |
    +----+---------+--------+---------------------+----------+
    |  6 | 火狐2   |       | 1998-02-02 00:00:00 | NULL     |
    +----+---------+--------+---------------------+----------+
    1 row in set (0.00 sec)
    ```
### SUM(列) 求此列的和
>- SELECT SUM(列) FROM 表；

    ```
    MariaDB [python3]> SELECT SUM(id) FROM students;
    +---------+
    | SUM(id) |
    +---------+
    |      19 |
    +---------+
    ```
### AVG(列) 求此列平均值
>- SELECT AVG(列) FROM 表；

    ```
    MariaDB [python3]> SELECT AVG(id) FROM students;
    +---------+
    | AVG(id) |
    +---------+
    |  3.8000 |
    +---------+
    1 row in set (0.00 sec)
    ```
## 分组
    按照字段分组，表示此字段相同的数据会被放到一个组中
>- 分组 group by
>>- SELECT 列1，列2，聚合 FROM 表名 GROUP BY 列1, 列2

    ```
    MariaDB [python3]> SELECT gender as 性别,count(*) FROM students GROUP BY gender;
    +--------+----------+
    | 性别   | count(*) |
    +--------+----------+
    |        |        2 |
    |       |        3 |
    +--------+----------+
    2 rows in set (0.00 sec)
    ```
>- having 对分组数据筛选
>>- SELECT 列1，列2，聚合 FROM 表名 GROUP BY 列1, 列2 HAVING 条件;

    ```
        MariaDB [python3]> select gender,count(*) from students group by gender having count(*) > 2;
        +--------+----------+
        | gender | count(*) |
        +--------+----------+
        |       |        3 |
        +--------+----------+
        1 row in set (0.00 sec)
    ```
## 排序
     对查询的数据进行排序,升序asc 降序desc
>- ORDER BY 排序
>>- SELECT * FROM 表 ORDER BY 列 ASC|DESC, 列 ASC|DESC;

    ```
    MariaDB [python3]> select * from students order by birthday asc, gender desc;
    +----+---------+--------+---------------------+----------+
    | id | name    | gender | birthday            | hometown |
    +----+---------+--------+---------------------+----------+
    |  5 | 火狐1   |       | 1998-02-02 00:00:00 | NULL     |
    |  6 | 火狐2   |       | 1998-02-02 00:00:00 | NULL     |
    |  4 | 小杰2   |        | 1998-02-02 00:00:00 | NULL     |
    |  1 | 小杰    |       | 2018-01-01 00:00:00 | 家里     |
    |  7 | 谷歌    |        | 2018-01-01 00:00:00 | 美国     |
    +----+---------+--------+---------------------+----------+
    5 rows in set (0.00 sec)
    ```
## 分页
    对当前数据进行分页
>- limit 分页
>>- SELETE * FROM 表 LIMIT 开始数据，数据个数
>>>- SELECT * FROM 表 LIMIT (n-1)*m,m
>>>>- n代表第几次取数据，代表取几条数据

    ```
    MariaDB [python3]> select * from students;
    +----+---------+--------+---------------------+----------+
    | id | name    | gender | birthday            | hometown |
    +----+---------+--------+---------------------+----------+
    |  1 | 小杰    |       | 2018-01-01 00:00:00 | 家里     |
    |  4 | 小杰2   |        | 1998-02-02 00:00:00 | NULL     |
    |  5 | 火狐1   |       | 1998-02-02 00:00:00 | NULL     |
    |  6 | 火狐2   |       | 1998-02-02 00:00:00 | NULL     |
    |  7 | 谷歌    |        | 2018-01-01 00:00:00 | 美国     |
    +----+---------+--------+---------------------+----------+
    5 rows in set (0.00 sec)

    MariaDB [python3]> select * from students limit 2,2;
    +----+---------+--------+---------------------+----------+
    | id | name    | gender | birthday            | hometown |
    +----+---------+--------+---------------------+----------+
    |  5 | 火狐1   |       | 1998-02-02 00:00:00 | NULL     |
    |  6 | 火狐2   |       | 1998-02-02 00:00:00 | NULL     |
    +----+---------+--------
    ```
# Mysql 高级-关系
    关系才能体现mysql的关系数据库的强大，在实际环境中存在的关系有（一对一， 一对多， 多对多）
>- 一对一
>>- 关系可以存在任何表,一般用来扩展已有不可变并有数据的表

>- 一对多
>>- 关系存在多的那张表用来外键一的表

>- 多对多
>>- 需要新建一张表来保存这两张表中的关系
## 关系-外键
    ```如果一张表中有一个非主键的字段指向了别一张表中的主键，就将该字段叫做外键```
>- 外键的默认作用
>>1. 对子表(外键所在的表)的作用：子表在进行写操作的时候，如果外键字段在父表中找不到对应的匹配，操作就会失败。
>>2. 对父表的作用：对父表的主键字段进行删和改时，如果对应的主键在子表中被引用，操作就会失败。

>- 外键的约束模式
>>1. restrict：严格模式(默认), 父表不能删除或更新一个被子表引用的记录。
>>2. cascade：级联模式, 父表操作后，子表关联的数据也跟着一起操作。
>>3. set null：置空模式,前提外键字段允许为NLL,  父表操作后，子表对应的字段被置空。
>>4. no action：什么都不做

>- 使用外键的前提
>>1. 表储存引擎必须是innodb，否则创建的外键无约束效果。
>>2. 外键的列类型必须与父表的主键类型完全一致。
>>3. 外键的名字不能重复。
>>4. 已经存在数据的字段被设为外键时，必须保证字段中的数据与父表的主键数据对应起来。

### 外键 foreign key
>- 在创建表的时候添加外键约束
>>- constraint 外键名 foreign key(本表列名) references 外键表(外键表的列名));

    ```
    MariaDB [school]> create table scores(
    -> id int primary key auto_increment not null,
    -> score decimal(5,2) not null,
    -> stuid int,
    -> constraint scores_students foreign key(stuid) references students(id));
    Query OK, 0 rows affected (0.01 sec)
    ```

>- 在创建好的表中添加外键约束
>>- ALTER TABLE 表名
    ADD [CONSTRAINT 外键名] FOREIGN KEY [id] (index_col_name, ...)
    REFERENCES tbl_name (index_col_name, ...)
    [ON DELETE {CASCADE | SET NULL | NO ACTION | RESTRICT}]
    [ON UPDATE {CASCADE | SET NULL | NO ACTION | RESTRICT}]

    ```
    MariaDB [school]> alter table scores add constraint score_subject foreign key(subid) references subject(id) on update cascade on delete restrict;
    Query OK, 0 rows affected (0.02 sec)
    Records: 0  Duplicates: 0  Warnings: 0
    ```

>- 删除外键字段
>>1. 先找到外键字段的名字
>>> CONSTRAINT `scores_ibfk_2` FOREIGN KEY (`subid`) REFERENCES `subject` (`id`)

    '''
        MariaDB [school]> show create table scores;
        | scores | CREATE TABLE `scores` (
          `id` int(11) NOT NULL AUTO_INCREMENT,
          `stuid` int(11) DEFAULT NULL,
          `subid` int(11) DEFAULT NULL,
          `score` decimal(5,2) NOT NULL,
          PRIMARY KEY (`id`),
          KEY `stuid` (`stuid`),
          KEY `subid` (`subid`),
          CONSTRAINT `scores_ibfk_1` FOREIGN KEY (`stuid`) REFERENCES `students` (`id`),
          CONSTRAINT `scores_ibfk_2` FOREIGN KEY (`subid`) REFERENCES `subject` (`id`)
        ) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
    '''

>>2. 通过CONSTRAINT 删除外键

    '''
    MariaDB [school]> alter table scores drop foreign key scores_ibfk_2;
    Query OK, 0 rows affected (0.01 sec)
    Records: 0  Duplicates: 0  Warnings: 0
    '''
### 外键查询
>- 连接查询
>>- inner join 连接查询

    ```
    MariaDB [school]> select students.name,subject.stitle,scores.score from
        scores inner join students on scores.stuid=students.id inner join subject on scores.subid=subject.id;
    +--------+--------+-------+
    | name   | stitle | score |
    +--------+--------+-------+
    | 张一   | 数学   | 98.20 |
    | 张一   | 语文   | 97.20 |
    | 张一   | 英语   | 60.20 |
    +--------+--------+-------+
    3 rows in set (0.00 sec)

    MariaDB [school]> select students.name,subject.stitle,scores.score from students inner join scores on scores.stuid=students.id inner join subject on scores.subid=subject.id;
    +--------+--------+-------+
    | name   | stitle | score |
    +--------+--------+-------+
    | 张一   | 数学   | 98.20 |
    | 张一   | 语文   | 97.20 |
    | 张一   | 英语   | 60.20 |
    +--------+--------+-------+
    3 rows in set (0.00 sec)

    # 查询某个同学的某个科目的成绩
    MariaDB [school]> select students.name,subject.stitle,scores.score from students
    -> inner join scores on scores.stuid=students.id
    -> inner join subject on subject.id=scores.subid
    -> where students.name='张一' and subject.stitle='数学';
    +--------+--------+-------+
    | name   | stitle | score |
    +--------+--------+-------+
    | 张一   | 数学   | 98.20 |
    +--------+--------+-------+
    1 row in set (0.00 sec)

    # 查询张一同学的所有科目的平均分
    MariaDB [school]> select students.name,avg(scores.score) from scores inner join students on scores.stuid=students.id where students.name='张一' group by students.name;
    +--------+-------------------+
    | name   | avg(scores.score) |
    +--------+-------------------+
    | 张一   |         85.200000 |
    +--------+-------------------+
    1 row in set (0.00 sec)

    # 查询张一学生的最高分和最低分
    MariaDB [school]> select students.name,max(scores.score) as '最高分',min(scores.score) as '最低分'
    -> from students
    -> inner join scores on scores.stuid=students.id
    -> where students.name='张一';
    +--------+-----------+-----------+
    | name   | 最高分    | 最低分    |
    +--------+-----------+-----------+
    | 张一   |     98.20 |     60.20 |
    +--------+-----------+-----------+
    1 row in set (0.00 sec)
    ```
### 外键-自关联
    自己关联自己

>- 需求： 创建一张表，表中包含市，市下的所有省

1. 创建表
```
MariaDB [school]> create table areas(
    -> id int primary key auto_increment not null,
    -> atitle varchar(20) not null,
    -> pid int,
    -> foreign key(pid) references areas(id)
    -> );
    Query OK, 0 rows affected (0.01 sec)
```
2. 查询

```
查询市的名称为“广州市”的所有区县
select dis.*,dis2.* from areas as dis
inner join areas as city on city.id=dis.pid
left join areas as dis2 on dis.id=dis2.pid
where city.atitle='广州市';
```
## mysql-视图
    视图就是对一个查询语句进行封装

1. 定义视图
```
create view stuscore as
select students.*,scores.score from scores
inner join students on scores.stuid=students.id;
```
2. 使用创建的视图
```
select * from stuscore;
```
## mysql- 事务
    当一个业务逻辑需要多个sql完成时，如果其中某条sql语句出错，则希望整个操作都退回使用事务可以完成退回的功能，保证业务逻辑的正确性

### 事务的四大特性
- 原子性(Atomicity)：事务中的全部操作在数据库中是不可分割的，要么全部完成，要么均不执行
- 一致性(Consistency)：几个并行执行的事务，其执行结果必须与按某一顺序串行执行的结果相一致
- 隔离性(Isolation)：事务的执行不受其他事务的干扰，事务执行的中间结果对其他事务必须是透明的
- 持久性(Durability)：对于任意已提交事务，系统必须保证该事务对数据库的改变不被丢失，即使数据库出现故障

### 事务语句
- 开启： begin;
- 提交： commit;
- 回滚：rollback;


## mysql备份与恢复
>- 备份
```mysqldumo -uroot -p 数据库名 > name.sql```
>- 恢复
```mysql -uroot -p 数据库名称 < name.sql```


# Mysql 与 python 交互
    在python3中使用pymysql模块跟mysql进行交互
## 基本使用
```
# 导入pymysql模块
import pymysql

host = '10.0.0.136'
port = 3306
user = 'root'
passwd = 'linkidc'
db = 'school'

# 创建数据库连接
conn = pymysql.connect(host=host, port=port, user=user, passwd=passwd, db=db, charset='utf8')

# 创建执行数据库的游标
cursor = conn.cursor()

# 执行sql语句 返回受影响的行数
row = cursor.execute('insert into students VALUES(0,"小杰",1)')
print(row)

# 提交执行的语句
conn.commit()
cursor.close()
conn.close()

```

## pymysql.Connection 详解
>- mysql_conn = pymysql.connet() # 建立连接，参数详解

```
pymysql.connet(
    host=None,                  # 主机名
    user=None,                  # 数据库连接用户名

    password='',                # 数据库连接密码
    passwd=None,                # 数据库连接密码 为了兼容MySQLdb

    database=None,              # 数据库名称
    db=None,                    # 数据库名 为了兼容MySQLdb

    port=0,                     # 数据库的连接端口
    charset='',                 # 数据库的字符集

    bind_address=None,          # 当客户端有多个网络接口时，指定连接到主机的接口。参数可以是一个主机名或IP地址。
    connect_timeout=10,         # 设置数据库连接超时时间，超时会抛出异常 (默认值:10，最小:1，最大值:31536000)
    init_command=None,          # 建立连接时运行的初始SQL语句。

    autocommit=False,           # 自动提交模式，(默认值:False)

    # 以字节为单位发送给服务器的数据包的最大大小。(默认值:16MB)只用于限制比默认值小的“加载本地文件”数据包的大小(16KB)。
    max_allowed_packet=16777216,

    unix_socket=None,           # 可以选择使用UNIX套接字而不是TCP/IP。
    sql_mode=None,              # 使用默认的sql_mode
    read_default_file=None,     # 指定my.cnf文件读取这些参数从[客户]部分。
    conv=None,                  # 要使用转换字典而不是默认字典。这是用来提供自定义的编组和解组类型。看到转换器。
    use_unicode=None,           # 是否默认为unicode字符串。这个选项默认为Py3k。
    client_flag=0,              # 自定义标志发送到MySQL。在constants.CLIENT中找到潜在的标示
    read_default_group=None,    # 从配置文件中读取组信息
    local_infile=False,         # 布尔值，以启用LOAD DATA LOCAL命令的使用。(默认值:False)

    cursorclass=<class 'pymysql.cursors.Cursor'>, # 使用自定义游标类

```
>- mysql_conn = pymysql.connect() 返回的连接详解

>>- mysql_conn.open # 查看数据库连接状态 返回boolean值
>>- mysql_conn.ping() # 判断服务器是否存活
>>- mysql_conn.show_warnings() # Send the “SHOW WARNINGS” SQL command.
>>- mysql_conn.begin() # 开始事务
>>- mysql_conn.commit() # 提交事务
>>- mysql_conn.rollback() # 回滚事务
>>- myysql_conn.select_db(db)
>>- mysql_coun.set_charset('utf8') # 设置数据库字符编码
>>- mysql_coun.character_set_name() # 获取数据库字符编码
## pymysql Cuesor 详解
    cuesor 是用来和数据库交互的对象 通过 mysql_conn.cursor() 方法来创建游标

>- class pymysql.cursors.Cursor(connection)

    这是您用来与数据库交互的对象。
    mysql_cursors = mysql_conn.cursor()
---
>- mysql_cursors.connection.select_db('school') # 更换数据库
>- mysql_cursprs.execute(query, args)
>>- query: 执行的sql语句
>>- args ：是一个序列，如果query中语句有%s，则直接执行args就行
>>- 返回受影响的行数， 如果是查询则返回查询到的行数
>>- mysql_cursor.execute('select %s from students where id=%s', ('name',1))
---

>- mysql_cursors.excutemany(query, args)
>>- query: 执行的sql语句
>>- args ：是一个序列，如果query中语句有%s，则直接执行args就行
>>- 返回受影响的行数， 如果是查询则返回查询到的行数

>>- 相当于循环执行execute() 方法的时候 循环取args的值
>>- mysql_cursor.executemany('insert into students values(%s, %s, %s)', [(0,'张1',1),(0,'小2',1),])
>>- mysql_cursor.max_stmt_length # executemany()生成的最多语句个数默认1024000
---

>- mysql_cursor.fetchall() # 获取全部的查询结果
>- mysql_cursor.fetchone() # 获取一个查询结果
>- mysql_cursor.fetchmany(size) # 获取指定size个数的查询结果

>- cursor.scroll(1,mode='relative')  # 相对当前位置移动
>- cursor.scroll(2,mode='absolute') # 相对绝对位置移动

>- mysql_cursor.mogrify(query, args) # 方法并返回 execute() 执行的sql语句的字符串, 并不会对数据库进行操作


## pymysql操作流程详解
### pymysql 执行增删改操作流程
1. 创建数据库连接
```
    conn = pymysql.connect(host='10.0.0.136', port=3306, user='root', passwd='linkidc', db='school', charset='utf8')
```
2. 获取与数据库交互的游标
    ```
    cursor = conn.cursor()
    ```
3. 执行sql语句
>- 执行单条SQL语句,并返回影响的行数

    ```
    row = cursor.execute('insert into students VALUES(%s,%s,%s)', (1,'姓名啊',1))
    ```
>- 执行多条SQL语句

    ```
    data = [(0, '王0', 0), (0, '王1', 1), (0, '王2', 0), (0, '王3', 1), (0, '王4', 0), (0, '王5', 1), (0, '王6', 0), (0, '王7', 1), (0, '王8', 0), (0, '王9', 1)]
    try:
        row = cursor.executemany('insert into students values(%s,%s,%s)', data)
        print(row)
        conn.commit()
    except: # 当插入数据有误的时候进行回滚操作
        conn.rollback()
    ```
3. 释放数据库连接资源，释放游标资源

    ```
    cursor.close() # 关闭游标
    conn.close() # 关闭连接
    ```
### pymysql 执行查询流程
1. 创建数据库连接
```
    conn = pymysql.connect(host='10.0.0.136', port=3306, user='root', passwd='linkidc', db='school', charset='utf8')
```
2. 获取与数据库交互的游标
    ```
    cursor = conn.cursor()
    ```
3. 执行查询语句，并返回查询到的行数

    ```
    a = cursor.execute('select name from students where id>%s', (1,))
    date = cursor.fetchone()  # 获取一条查询到的数据
    date = cursor.fetchmany(2)  # 获取执行数量的数据
    date = cursor.fetchall()  # 获取全部查询到的数据
    ```
4. 释放数据库连接资源，释放游标资源

    ```
    cursor.close() # 关闭游标
    conn.close() # 关闭连接
    ```












