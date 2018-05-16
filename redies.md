# 缓存数据库
    NoSQL(NoSQL = Not Only SQL )，意即“不仅仅是SQL”，泛指非关系型的数据库,随着互联网web2.0网站的兴起，传统的关系数据库在应付web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题。
# NoSQL数据库的四大分类
## 键值(Key-Value)存储数据库
    这一类数据库主要会使用到一个哈希表，这个表中有一个特定的键和一个指针指向特定的数据。Key/value模型对于IT系统来说的优势在于简单、易部署。但是如果DBA只对部分值进行查询或更新的时候，Key/value就显得效率低下了。[3]  举例如：Tokyo Cabinet/Tyrant, Redis, Voldemort, Oracle BDB.
## 列存储数据库。
    这部分数据库通常是用来应对分布式存储的海量数据。键仍然存在，但是它们的特点是指向了多个列。这些列是由列家族来安排的。如：Cassandra, HBase, Riak.
## 文档型数据库
    文档型数据库的灵感是来自于Lotus Notes办公软件的，而且它同第一种键值存储相类似。该类型的数据模型是版本化的文档，半结构化的文档以特定的格式存储，比如JSON。文档型数据库可 以看作是键值数据库的升级版，允许之间嵌套键值。而且文档型数据库比键值数据库的查询效率更高。如：CouchDB, MongoDb. 国内也有文档型数据库SequoiaDB，已经开源。
## 图形(Graph)数据库
    图形结构的数据库同其他行列以及刚性结构的SQL数据库不同，它是使用灵活的图形模型，并且能够扩展到多个服务器上。NoSQL数据库没有标准的查询语言(SQL)，因此进行数据库查询需要制定数据模型。许多NoSQL数据库都有REST式的数据接口或者查询API。[2]  如：Neo4J, InfoGrid, Infinite Graph.
## 总结
    因此，我们总结NoSQL数据库在以下的这几种情况下比较适用：
1. 数据模型比较简单；
2. 需要灵活性更强的IT系统；
3. 对数据库性能要求较高；
4. 不需要高度的数据一致性；
5. 对于给定key，比较容易映射复杂值的环境。

# Redis
## 介绍
    redis是业界主流的key-value nosql 数据库之一。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。
## 优点
- 异常快速 : Redis是非常快的，每秒可以执行大约110000设置操作，81000个/每秒的读取操作。
- 支持丰富的数据类型 : Redis支持最大多数开发人员已经知道如列表，集合，可排序集合，哈希等数据类型。
- 这使得在应用中很容易解决的各种问题，因为我们知道哪些问题处理使用哪种数据类型更好解决。
- 操作都是原子的 : 所有 Redis 的操作都是原子，从而确保当两个客户同时访问 Redis 服务器得到的是更新后的值（最新值）。
- MultiUtility工具：Redis是一个多功能实用工具，可以在很多如：缓存，消息传递队列中使用（Redis原生支持发布/订阅），在
- 应用程序中，如：Web应用程序会话，网站页面点击数等任何短暂的数据；

## Redis使用
### 安装
    ```
    $sudo apt-get update
    $sudo apt-get install redis-server
    ```
### 启动
    ```$redis-server```
### 客户端连接
    $ redis-cli -h 127.0.0.1 -p 6379
### 切换数据库
    redis最多支持16个数据库
    '''
    127.0.0.1:6379> select 0
    OK
    127.0.0.1:6379> select 1
    OK
    127.0.0.1:6379[1]>
    '''
## redis中数据类型
### 字符串
    redis中的String在在内存中按照一个key对应一个value来存储。
#### SET key value [EX seconds] [PX milliseconds] [NX|XX]
>>- key:存储到数据库中的KEY
>>- value: key对应的值
>>- EX ： 过期时间（秒） 到期则自动删除
>>- PX：过期时间（毫秒）到期则自动删除
>>- NX：当前key不存在才会操作
>>- XX：如果key存在才会操作

#### SETNX key value
    相当于 'SET key value NX' 当前key不存在才会操作
#### SETEX key seconds value
    设置带有超时时间(秒)的key和value，相当于'SET key value EX seconds'
#### PSETEX key milliseconds value
    设置带有超时时间(毫秒)的key和value，相当于'SET key value PX millseconds'
#### MSET key value [key value ...]
    同时设置多个key和value
#### GET key
    获取key的 值
#### MGET key [key ...]
    批量获取
#### GETSET key value
    返回key的旧value，并设置新value
#### GETRANGE key start end
    对key对应的值进行切片操作， 包含stat和end
>- start :字符串开始
>- end : 字符串结束
#### SETRANGE key offset value
    修改指定key，从offset位置
>- offset： 偏移量，在字符串中需要修改的开始位置
####  SETBIT key offset value
    针对字符串转换的二进制格式进行修改, value 只能为二进制数据
#### GETBIT key offset
    吧字符串转换成二进制，并获取指定位置的值
#### BITCOUNT key [start end]
    统计字符串的二进制形式中有多少个1
#### STRLEN key
    返回指定key对应的value的字节长度
#### INCR key
    自增1，只针对数字类型，
####  INCRBYFLOAT key increment
    浮点自增
>>- incement ： 需要增加的浮点数的数值
#### DECR key
    自减去1
#### APPEND key value
    字符串后边增加内容
#### KEYS pattern
    查找所有能匹配pattren正则的key
>- pattern : 正则表达式
------------------------------------------------------------------------
### hash
    hash表现形式上想python中的dict，可以存储一组关联性较强的数据，如 {"info":{"name","xiaojie","age":18}}
#### HSET key field value
    key对应的hash中设置一个键值对，不存在则创建，存在则修改
>>- key:hash字典的键
>>- field: hash字典对应小字典的key
>>- value： field对应的值
#### HMSET key field value [field value ...]
    给hash自动设置多个字段
#### HGET key field
    获取hash字典中的字段的值
#### HMGET key field [field ...]
    获取字典中多个字段的值
#### HGETALL key
    获取字典中所有的信息
#### HLEN key
    查看hash字典中几对数据
#### HKEYS key
    获取hash字典中所有的key
#### HVALS key
    获取hash字典中所有的value
#### HEXISTS key field
    判断hash字典中是否存在field字段
#### HDEL key field [field ...]
    删除hash字典中指定的filed
#### HINCRBY key field increment
    对hash字典中字段进行自增操作
>>- filed 需要自增的字段
>>- increment 需要自增数值
#### HINCRBYFLOAT key field increment
    对hash字典中的字段进行float数值自增
#### HSCAN key cursor [MATCH pattern] [COUNT count]
    针对于大量时间的时候在hash中匹配 MATCHE  的field
>- cursor: 游标 字段开始的地方
>- MARCHE： 需要匹配的 如 '\*e\*'
>- COUNT: 匹配多少条数据
------------------------------------------------------------------------
### list
    List操作，redis中的list在内存中安装一个name对应一个list来存储 \*n1------[v1,v2]\*
#### LPUSH key value [value ...]
    向list的头部追加元素
#### RPUSH key value [value ...]
    向列表尾部追加元素
#### LRANGE key start stop
    获取list中的元素
>- start: 开始的角标位置
>- stop: 结束角标位置
#### LPUSHX key value
    只有list存在的时候才会添加key和value
#### LLEN key
    获取list中元素的个数
#### LINSERT key BEFORE|AFTER pivot value
   在列表中某个元素之前|之后插入一个元素
>- BFFORE: 在元素之前插入
>- AFTER: 在原始之后插入
>- picot: list中已经存在的一个value
#### LSET key index value
    修改list中指定位置的值
>- index: list中索引位置
#### LREM key count value
    删除list中指定的value
>- count: 表示删除几次
>- value: 需要删除的值
#### LPOP key
    删除list第一个元素，并返回
#### RPOP key
    删除list最后一个元素，并返回
#### LINDEX key index
    根据索引值获取list中的元素
#### LTRIM key start stop
    切片操作，值取 start到stop中的元素，其他的删掉
#### RPOPLPUSH source destination
    从左边取的list中取最后一个value追加到destination这个list中
#### BLPOP key [key ...] timeout
    删除并获取列表中的第一个元素，或timeout秒阻塞，直到有一个元素可用。
#### BRPOPLPUSH source destination timeout
    从列表中弹出一个值，将其推到另一个列表并返回;或阻塞，直到有一个可用。
-------------------------------------
### SET操作
    set集合，就是不允许有重复的值
#### SADD key member [member ...]
    想set集合中添加值
#### SMEMBERS key
    获取集合中所有的值
#### SCARD key
    获取集合中元素的个数
#### SDIFF key [key ...]
    求两个集合的差集
#### SDIFFSTORE destination key [key ...]
    获取第一个key对应的集合中且不再其他key对应于的集合元素中，在将其加入到destination集合中
#### SINTER key [key ...]
    获取这些集合中的交集
#### SINTERSTORE destination key [key ...]
    获取这些key的交集，并存储到destionation集合中
#### SISMEMBER key member
    查看nember是否说话呀key对应集合的元素
#### SMOVE source destination member
    将元素member从source移动到destination
#### SPOP key [count]
    从key对应的set中移除尾部count个元素，并返回
#### SRANDMEMBER key [count]
    从集合中随机取count个y元素
#### SREM key member [member ...]
    从集合中删除元素
#### SUNION key [key ...]
    两个集合的并集
####  SUNIONSTORE destination key [key ...]
    获取几个的并集，并把并集存储到destinati集合中
---

---
### SortedSet（有序集合)
    Redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。有序集合的成员是唯一的,但分数(score)却可以重复。集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。 集合中最大的成员数为 232 - 1 (4294967295, 每个集合可存储40多亿个成员)。
#### ZADD key score1 member1 [score2 member2]
    向有序集合添加一个或多个成员，或者更新已存在成员的分数
#### ZCARD key
    获取有序集合的成员数
#### ZCOUNT key min max
    计算在有序集合中指定区间分数的成员数
#### ZINCRBY key increment member
    有序集合中对指定成员的分数加上增量 increment
#### ZINTERSTORE destination numkeys key [key ...]
    计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中
#### ZLEXCOUNT key min max
    在有序集合中计算指定字典区间内成员数量
#### ZRANGE key start stop [WITHSCORES]
    通过索引区间返回有序集合成指定区间内的成员
#### ZRANGEBYLEX key min max [LIMIT offset count]
    通过字典区间返回有序集合的成员
#### ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT]
    通过分数返回有序集合指定区间内的成员
#### ZRANK key member
    返回有序集合中指定成员的索引
#### ZREM key member [member ...]
    移除有序集合中的一个或多个成员
#### ZREMRANGEBYLEX key min max
    移除有序集合中给定的字典区间的所有成员
#### ZREMRANGEBYRANK key start stop
    移除有序集合中给定的排名区间的所有成员
#### ZREMRANGEBYSCORE key min max
    移除有序集合中给定的分数区间的所有成员
#### ZREVRANGE key start stop [WITHSCORES]
    返回有序集中指定区间内的成员，通过索引，分数从高到底
#### ZREVRANGEBYSCORE key max min [WITHSCORES]
    返回有序集中指定分数区间内的成员，分数从高到低排序
#### ZREVRANK key member
    返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序
#### ZSCORE key member
    返回有序集中，成员的分数值
#### ZUNIONSTORE destination numkeys key [key ...]
    计算给定的一个或多个有序集的并集，并存储在新的 key 中
#### ZSCAN key cursor [MATCH pattern] [COUNT count]
    迭代有序集合中的元素（包括元素成员和元素分值）

# Python操作Redis
## 安装
    pip install redis
## 链接redis

```

```


