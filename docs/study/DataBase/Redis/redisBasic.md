---
title: redis 基础学习
katex: true
categories: 
- redis
tags:
- redis
- golang
---
## centos7 下载安装redis

[https://blog.csdn.net/wangzhilong1996/article/details/136901595](CentOS 7下载安装Redis)

安装、配置、开机自启、连接

## window安装和使用redis

[https://blog.csdn.net/qq_41941900/article/details/138712993?spm=1001.2101.3001.6650.4&amp;utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7Ebaidujs_baidulandingword%7ECtr-4-138712993-blog-122742423.235%5Ev43%5Epc_blog_bottom_relevance_base6&amp;depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7Ebaidujs_baidulandingword%7ECtr-4-138712993-blog-122742423.235%5Ev43%5Epc_blog_bottom_relevance_base6&amp;utm_relevant_index=9](超详细Redis下载安装图文教程（Win和Linux版）)

### 本地连接

```sh
redis-cli -h 127.0.0.1 -p 6379

ping
```

出现报错
(error) NOAUTH Authentication required.

(Redis (error) NOAUTH Authentication required.解决方法)[https://blog.csdn.net/basycia/article/details/52176025]

需要输入密码连接
```sh
auth "yourpassword"
```

### 远程连接

## 认识SQL与NoSQL

关系型数据库和非关系型数据库

|          | SQL                                                                                                                         | NoSQL                                                                                  |
| -------- | --------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| 数据结构 | 结构化，<br />有PrimaryKey Unique unsigned等字段的约束，<br />如bigint(20), varchar(32)，修改数据字段的结构时，所影响较大； | 键值型、JSON格式的文档型（字段约束无）、节点型，<br />修改数据字段的结构时，所影响较小 |
| 数据关联 | 可存在外键，实现多张表的联系                                                                                                | 如文档型，数据存在重复，<br />表和表之间关系需要程序员定义                             |
| 查询方式 | 语法固定，专门的SQL语句查询                                                                                                 | 语法不固定，如类似函数、请求等                                                         |
| 事务特性 | ACID                                                                                                                        | BASE，不能全部满足ACID                                                                 |
| 存储     | 磁盘                                                                                                                        | 内存                                                                                   |
| 扩展     | 垂直                                                                                                                        | 水平                                                                                   |
| 场景     | 数据结构固定，数据安全性、一致性要求较高                                                                                    | 数据结构不固定，对数据安全性、一致性要求不高，<br />性能要求高                         |

## 认识Redis

基于内存的键值型的NoSQL数据库，

### 特点

1. 键值型，value支持多种不同的数据结构
2. 单线程、具备原子性
3. 低延迟、速度快（基于内存、IO多路复用、良好编码）
4. 数据持久化
5. 支持主从集群、分片集群
6. 多语言客户端

## 不同数据类型的命令使用

```sh
help [command]
```

### 通用命令

1. KEYS （不建议在生产设备环境上使用）
   查询所有符合模板的
2. DEL
   删除一个指定的key
3. EXISTS
   判断key是否存在
4. EXPIRE
   给一个key设置有效期，有效期到期key会自动删除
5. TTL
   查看KEY的剩余有效期；-2为到期，-1为永久有效

### String类型

string 普通字符串；int 整数类型，可自增和自减； float 浮点类型，自增和自减

1. SET
   添加或者修改已经存在的一个String类型的键值对
2. GET
   根据key获取String类型的value
3. MSET
   根据多个key获取多个String类型的value
4. INCR
   让一个整型的key自增1
5. INCRBY
   让一个整型的key自增并指定步长，
6. INCRBYFLOAT
   让一个浮点类型的数字自增并指定步长
7. SETNX
   添加一个String类型的键值对，这个key不存在，则不执行
8. SETEX
   添加一个String类型的键值对，并指定有效期

### KEY的层级格式

多个单词之间":"隔开，形成层级结构

#### [项目名]:[业务名]:[类型]:[id]

如user相关的key  lucien:user:1

User对象的一个JSON字符串对象

```redis
set lucien:user:1 '{"id":1, "name":"Rose","age":18}'
set lucien:product:12 '{"id":1, "name":"小米","price":1888}'
```

### Hash类型

又称为散列， 其value是一个无序字典

String结构是将对象序列化为JSON字符串后存储，修改对象某个字段不方便

Hash结构可将对象中的每个字段独立存储，对单个字段做CRUD

#### 常见命令

1. HSET key field value
   添加或者修改hash类型key的field的值
2. HGET key field
   获取一个hash类型key的field值
3. HMSET
   批量添加多个hash类型key的field的值
4. HMGET
   批量获取多个hash类型key的field的值
5. HKEYS
   获取一个hash类型的key中的所有field
6. HVALS
   获取一个hash类型的key中的所有value
7. HINCRBY
   让一个hash类型key的字段值自增并指定步长
8. HSETNX
   添加一个hash类型的key的field值，前提是这个field不存在，否则不执行
9. HGETALL
   获取一个hash类型的key中的所有的field和value

### List类型

可看做是双向链表结构，支持正向检索和反向检索

#### 特征

1. 有序
2. 元素可重复
3. 插入和删除快
4. 查询速度一般

#### 常见命令

1. LPUSH key element
   向列表左侧插入一个或多个元素
2. LPOP key
   移除并返回列表左侧的第一个元素，没有则返回nil
3. RPUSH key element
   向列表右侧插入一个或多个元素
4. RPOP key
   移除并返回列表右侧的第一个元素
5. LRANGE key start end
   返回一段角标范围内的所有元素
6. BLPOP 和 BRPOP
   与LPOP和RPOP类似，在没有元素时等待指定时间，而不是直接返回nil

#### 如何利用List结构模拟一个栈

入口和出口在同一边

#### 如何利用List结构模拟一个队列

入口和出口在不同边

#### 如何利用List结构模拟一个阻塞队列

入口和出口在不同边，出队时采用BLPOP或BRPOP

### Set类型

Set结构可看作是一个value为null的HashMap，hash表，具备与HashSet类似的特征

1. 无序
2. 元素不可重复
3. 查找快
4. 支持交集、并集和差集等功能

#### Set常见命令

1. SADD key member
   向set中添加一个或多个元素
2. SREM key member
   移除set中指定元素
3. SCARD key
   返回set中元素的个数
4. SISMEMBER key member
   判断一个元素是否存在与set中
5. SMEMBERS
   获取set中的所有元素
6. SINTER key1 key2
   求key1与key2的交集
7. SDIFF key1 key2
   求key1和key2的差集
8. SUNION key1 key2
   求key1和key2的并集

### SortedSet类型

可排序的set集合，每一个元素都带有score属性，基于score属性对元素排序，底层是一个跳表 + hash表

1. 可排序
2. 元素不可重复
3. 查询速度快

#### 常见命令

1. ZADD key score member
   添加一个或多个元素到sorted set，如果已经存在则更新其score值
2. ZREM key member
   删除sorted set中的一个指定元素
3. ZSCORE key member
   获取sorted set中的指定元素的score值
4. ZRANK key member
   获取sorted set 中指定元素的排名
5. ZCARD key
   获取sorted set 中元素的个数
6. ZINCRBY key increment member
   让sorted set 中的指定元素自增，步长为指定的Increment值
7. ZRANGE key min max
   按照score排序后，获取指定排名范围内的元素
8. ZRANGEBYSCORE key min max
   按照score排序后，获取指定score范围内的元素
9. ZCOUNT key min max
   统计score值在指定范围的元素个数
10. ZDIFF ZINTER ZUNION

默认升序，降序则在命令的Z后面添REV
