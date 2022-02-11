## Redis

### redis键值对数据库



#### **概述**

redis是一种nosql数据库，它的数据是保存在内存中，同时redis可以定时把内存数据同步到磁盘，即可以将数据持久化，并且它比memcached支持更多的数据结构(string，list列表[队列和栈],set(集合)，sorted set[有序集合]，hash(hash表))。

相关参考文档:http://redisdoc.com/index.html

#### **redis使用场景**

1. 登录会话存储，存储在redis中，与memcached相比，数据不会丢失
2. 排行版/计数器:比如一些秀场类的项目，经常会有一些前多少名的主播排名。还有一些文章阅读量的技术，或者新浪微博的点赞数等。
3. 作为消息队列:比如celery就是使用redis作为中间人
4. 当前在线人数:还是之前的秀场例子，会显示当前系统有多少在线人数
5. 一些常用的数据缓存:比如我们的BBS论坛，模块不会经常变化的，但是每次访问首页都要从mysql中获取，可以在redis中缓存起来，不用每次都请求数据库。
6. 把前200篇文章缓存或者评论缓存:一般用户浏览网站，只会浏览前面一部分文章或者评论，那么可以把前面200篇文章和对应的评论缓存起来。用户访问超过的，就访问数据库，并且以后文章超过200篇，则把之前的文章删除
7. 好友关系:微博的好友关系使用redis实现
8. 发布和订阅功能:可以用来做聊天软件

#### **redis和memcached的比较**

|              | memcached                     | redis                          |
| ------------ | ----------------------------- | ------------------------------ |
| 类型         | 纯内存数据库                  | 内存磁盘同步数据库             |
| 数据类型     | 在定义value时就要固定数据类型 | 不需要                         |
| 虚拟内存     | 不支持                        | 支持                           |
| 过期策略     | 支持                          | 支持                           |
| 存储数据安全 | 不支持                        | 可以将数据同步到dump.db中      |
| 灾难恢复     | 不支持                        | 可以将磁盘中的数据恢复到内存中 |
| 分布式       | 支持                          | 主从同步                       |
| 订阅与发布   | 不支持                        | 支持                           |

### 安装以及客户端连接



#### **redis在Ubuntu系统中的安装与启动**

1.安装



```
sudo apt-get install redis-server
```

2.卸载



```
sudo apt-get purge --auto-remove redis-server
```

3.启动:



```
查看是否启动
ps aux|grep redis

手动启动
sudo service redis-server start
```

4.停止



```
sudo service redis-server stop
```

#### **Windows下安装redis**

redis官网:https://redis.io/

GitHub:https://github.com/MicrosoftArchive/redis/releases

解压后bin目录下的文件



```
redis-benchmark.exe         #基准测试
redis-check-aof.exe         # aof
redis-check-dump.exe        # dump
redis-cli.exe               # 客户端
redis-server.exe            # 服务器
redis.windows.conf          # 配置文件
```

用cd命令切换redis的目录并运行**redis-server.exe redis.windows.conf**

#### **启动redis:**



```
ubuntu:sudo service redis-server
windows:net start redis
```

#### **连接redis**



```
redis-cli -h [ip] -p[端口](默认端口为6379)
```

#### **Redis中文问题**

```
如果要在redis-cli中使用中文时，必须打开--raw选项，才能正常地显示中文
redis-cli --raw
```

### 字符串以及过期时间操作



#### **连接redis**



```
redis-cli -h 127.0.0.1 -p 6379 或者redis-cli
```

#### **set语法**



```
set key value [EX seconds] [PX milliseconds]
```

#### **设置key-velue**



```
语法:
set key value

示例:
set name angle
```

#### **获取value**



```
语法:
get key

示例:
get name
```

注意:如果设置值为多个单词，需要添加双引号



```
127.0.0.1:6379> set username "hello world"
OK
127.0.0.1:6379> get username
"hello world"
```

#### **设置过期时间**

如果不设置过期时间，则永久不会过期

##### **第一种:**



```
set key value EX seconds
或者
setex key timeout value


# 第一:设置10秒后过期
set name angle EX 10

# 第二:设置3秒后过期
setex name 3 angle
```

##### **第二种:**



```
expire key timeout(seconds)
```

#### **查看过期时间**



```
语法:
ttl key

示例:
ttl name
```

#### **删除数据**



```
语法:
del key

示例:
del name
```

#### **删除所有数据**

```
语法:
flushall
```

### 列表操作



**在列表左边添加元素**



```
语法:lpush key values

示例:lpush name angle
lpush name angle miku
```

将值value插入到列表key的表头。如果key不存在，一个空列表会被创建并执行lpush操作。当key存在但不是列表类型时，将返回一个错误。

**在列表右边添加元素**



```
语法: rpush key values

示例: rpush websites www.qq.com 0 -1
```

将值value插入到列表可以的表尾。如果可以不存在，一个空列表会被创建并执行RPUSH操作。当key存在但不是列表类型时，返回一个错误。

**查看列表中的元素**



```
语法:lrange key start stop

示例:lrange name 0 1
```

返回列表key中指定区间内的元素，区间以偏移量start和stop指定如果要左边的第一个到最后的一个lrange key 0 -1

**移除列表中的元素**

- 移除并返回列表key的头元素



```
lpop key

示例:
127.0.0.1:6379> lpop websites
"www.163.com"
127.0.0.1:6379> lrange websites 0 -1
1) "www.baidu.com"
2) "www.qq.com"
3) "0"
4) "-1"
```

- 移除并返回列表的尾元素



```
rpop key

示例:
127.0.0.1:6379> rpop websites
"-1"
127.0.0.1:6379> lrange websites 0 -1
1) "www.baidu.com"
2) "www.qq.com"
3) "0"
```

- 移除并返回列表key的中间元素



```
lrem key couont value

示例:
127.0.0.1:6379> lrem websites 1 www.qq.com
(integer) 1
127.0.0.1:6379> lrange websites 0 -1
1) "www.baidu.com"
2) "0"
```

将删除可以key这个列表中，count个值为value的元素

**指定返回第几个元素**



```
lindex key index

示例:
127.0.0.1:6379> lindex websites 0
"www.baidu.com"
127.0.0.1:6379> lindex websites 1
"0"
```

将返回key这个列表中，索引为index的这个元素

**获取列表中的元素个数**



```
llen key

示例:
127.0.0.1:6379> llen websites
(integer) 2
```

**删除指定的元素**



```
lrem key count value

示例:
# 从表头开始搜索，删除数量为1，删除值为1
lrem websites 1 1
# 从表尾开始搜索，删除数量为1，删除值为1
lrem websites -1 1
# 删除所有1
lrem websites 0 1
```

1. count>0:从表头开始向表尾搜索，移除与value相等的元素，数量为count
2. count<0:从表尾开始向表头搜索，移除与value相等的元素，数量为count的绝对值
3. count=0:移除表中所有与value相等的值

### 集合操作



- **添加元素**



```
语法:
sadd key member[member...]

示例:
# 向a集合中添加1、2、3、4、5、6
sadd a 1 2 3 4 5 6
```

- **查看元素**



```
语法:
smembers key

示例:
# 查看a集合中元素
smembers a
```

- **移除元素**



```
语法:
srem key members[members...]

示例:
# 从a集合中移除1、2元素
srem a 1 2
```

- **查看集合中的元素个数**



```
语法:
scard a

示例:
# 查看a集合有几个元素
scard a
```

- **获取多个集合的交集**



```
语法:
sinter key[key...]

示例:
# 返回a、b集合的交集
sinter a b
```

- **获取多个集合的差集**



```
语法:
sdiff key[key...]

示例:
# 返回得到集合b在集合a中没有的元素
sdiff b a
```

- **获取多个集合的并集**

```
语法:
sunion key[key...]

示例:
# 返回得到a和b集合的并集
sunion a b
```

### 哈希操作



- **添加**



```
语法:
hset key field value

示例:
# 设置person键并定义person下的字段为field，字段对应的值为angle
hset person name angle
```

将哈希表key中的域field的值设为value

如果key不存在，一个新的哈希表被创建并进行HSET操作。如果域field已经存在于哈希表中，旧值将被覆盖

- **获取哈希中的field对应的值**



```
语法:
hget key field

示例:
# 获取person下的字段的值
127.0.0.1:6379> hget person name
"angle"
```

- **删除key中的某个field**



```
语法:
hdel key field[field...]

示例:
# 删除person下的name字段
hdel person name
```



```
# 定义数据
127.0.0.1:6379> hset person name angle
(integer) 1
127.0.0.1:6379> hset person age 18
(integer) 1
127.0.0.1:6379> hset person sex boy
(integer) 1
```

- **获取某个哈希中所有的field和value**



```
语法:
hgetall key

示例:
# 获取person下的所有的键值对
127.0.0.1:6379> hgetall person
1) "name"
2) "angle"
3) "age"
4) "18"
5) "sex"
6) "boy"
```

- **获取某个哈希中所有的字段**



```
语法:
hkeys key

示例:
# 获取person下的所有的字段名
127.0.0.1:6379> hkeys person
1) "name"
2) "age"
3) "sex"
```

- **获取某个哈希中所有的值**



```
语法:
hvals key

示例:
# 获取person下的所有字段的值
127.0.0.1:6379> hvals person
1) "angle"
2) "18"
3) "boy"
```

- **判断哈希中是否存在某个field**



```
语法:
hexists key field

示例:
# 判断person中是否存在这个字段，存在返回1，否则返回0
127.0.0.1:6379> hexists person age
(integer) 1
127.0.0.1:6379> hexists person username
(integer) 0
```

- **获取哈希中总共的键值对**



```
语法:
hlen key

示例:
# 判断person中有几个键值对
127.0.0.1:6379> hlen person
(integer) 3
```

### 事务操作



redis事务可以一次执行多个命令，事务具有以下特征:

- 隔离性:事务中的所有命令都会序列化、按顺序地执行，不会被其他命令打扰
- 原子性:事务中的命令要么全部被执行，要么全部都不执行
- 开启一个事务



```
语法:
multi
```

以后执行的所有命令。都在这个事务中执行的。

- **执行事务**



```
语法:
exec
```

会将在multi和exec中的操作一并提交

- **取消事务**



```
语法:
discard
```

会将multi后的所有命令取消

- **监视一个或者多个key**



```
语法:
watch key[key...]

示例:
# 如果监听的可以发生改变，事务则不会执行,被取消掉
127.0.0.1:6379> watch username
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set username angle
QUEUED

重新打开一个cmd
127.0.0.1:6379> set username miku
OK

127.0.0.1:6379> exec


127.0.0.1:6379> get username
miku
-------------------------------------------------
# 如果监听的key没有发生改变，则事务会正常执行
127.0.0.1:6379> watch name
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name angle
QUEUED
127.0.0.1:6379> exec
OK
127.0.0.1:6379> get name
angle
```

监听一个(或多个)key，如果在事务执行之前这个(或这些)key被其他命令所改动，那么事务将被打断

- **取消所有可以的监视**



```
语法:
unwatch
```

### 发布和订阅操作



**给某个频道发布消息**



```
语法:
publish channel message

示例:
# 向channe2拼单发送消息hello
127.0.0.1:6379> publish channe2 hello
(integer) 1
127.0.0.1:6379>
```

- **订阅某个频道的消息**



```
语法:
subscribe channel[channel...]

示例:
# 订阅channe2频道，如果channe2频道发布消息，则会将消息显示出来
127.0.0.1:6379> subscribe channe2
subscribe
channe2
1
message
channe2
hello
```

### RDB和AOF的两种数据持久化



持久化:redis提供了两种数据备份方式，一种是RDB，另外一种是AOF

|                | RDB                                                          | AOF                                                          |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 开启关闭       | 开启:默认开启。关闭:把配置文件中所有的save都注释，就是关闭了 | 开启:在配置文件中appendonly yes即开启了aof，为no关闭         |
| 同步机制       | 可以指定某个时间内发生多少个命令进行同步。比如"save 900 1",如果在900s内发生了1此数据更改操作，那么就会做一次同步操作 | （1）appendfsync always:每次有数据更新操作，都会同步到文件中。（2）appendfsyc everysec:每秒进行一次更新。（3）appendfsync no:使用操作系统的方式进行更新，普遍是30s更新一次 |
| 存储内容       | 存储的是redis里面的具体的值，并且使经过压缩后存储进去的      | 存储的是执行的写操作命令                                     |
| 优点           | （1）存储数据到文件中会进行注释，文件体积比aof小。（2）因为存储的是redis具体的值，并且会经过压缩，因此在恢复的时候速度比AOF快。（3）非常适用于备份 | （1）AOF的策略是每秒钟或者每次发生写操作的时候都会同步，因此即使服务器故障，最多只会丢失1秒的数据。（2）AOF存储的redis命令，并且使直接追加到aof文件后面，因此每次备份的时候只要添加新的数据进去就可以了。（3）如果AOF文件比较大了，那么Redis会进行重写，只保留最小的命令集合 |
| 缺点           | （1）RDB在多少时间内发生了多少写操作的时候就会触发同步机制，因为采用压缩机制，RDB在同步的时候都重新保存整个Redis中的数据，因此在一般会设置在最少5分钟才保存一次数据。在这种情况下，一旦服务器故障，会造成5分钟的数据丢失。（2）在数据保存进RDB的时候，Redis会fork出一个子进程来同步，在数据量比较大的时候，可能会非常耗时。 | （1）AOF文件因为没有压缩，因此体积比RDB大。（2）AOF是在每秒或者每次写操作都进行备份，因此如果并发了比较大，效率可能有点慢。（3）AOF文件因为存储的是命令，因此在灾难恢复的时候Redis会重新运行AOF中的命令，速度不及RDB |
| 存储文件的路径 | 根据dir以及rdbfilename来指定路径和具体的文件名               | 根据dir以及appendfilename来指定具体的路径和文件名            |

注意修改完配置，需重启服务:sudo service redis-server restart

配置文件:redis.windows-service.conf

更多细节:http://redisdoc.com/topic/persistence.html#redis

### Redis设置连接密码



#### **设置redis密码**

> 在windows中，配置文件为redis.windows-service.conf
>
> 在Ubuntu中，配置文件为redis.conf

将配置文件中的"requirepass password"取消注释，设置密码



```
requirepass 123456
```

临时设置密码，但是重启服务后失效



```
config set requirepass password
config get requirepass
auth overload
```

#### **连接redis**



```
有两种方式:

redis-cli -h 127.0.0.1 -p 6379 -a 123456

或者

redis-cli -h 127.0.0.1 -p 6379
> auth 123456
```

### 其他机器连接本机redis



打开配置文件,windows下的配置文件为"redis.windows-service.conf"，Ubuntu下的配置文件为"redis.conf"

- Ubuntu
  - cd /etc/redis,vim redis.conf
- windows
  - 找到配置文件，使用notepad记事本打开
- 找到bind,进行修改绑定本机地址



```
bind 127.0.0.1 本机地址
```

打开配置文件,windows下的配置文件为"redis.windows-service.conf"，Ubuntu下的配置文件为"redis.conf"

- Ubuntu
  - cd /etc/redis,vim redis.conf
- windows
  - 找到配置文件，使用notepad记事本打开
- 找到bind,进行修改绑定本机地址

```
bind 127.0.0.1 本机地址
```

![image-20200914181131209](../../assets/image-20200914181131209.png)

#### **redis问题**

-MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error.

出现如上错误，输入以下命令即可解决



```
config set stop-writes-on-bgsave-error no
```

#### **redis问题**

-MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error.

出现如上错误，输入以下命令即可解决



```
config set stop-writes-on-bgsave-error no
```

### python操作redis

**安装**

```
pip install redis
```

**新建一个文件redis_demo.py，然后初始化一个redis实例变量，并且在Ubuntu虚拟机找那个开启redis**

```
# 从redis包中导入Redis类
from redis import Redis
# 初始化redis实例变量
op_redis = Redis(host="192.168.5.128",port=6379)
```

**对字符串的操作:操作redis的方法名称，跟之前使用redis-cli一样**

```
# 1.字符串
# 设置
op_redis.set("name","angle")
# 获取数据
name = op_redis.get("name")
print(name)
# 删除
op_redis.delete(name)


# 2.列表的操作
# 添加
op_redis.lpush("languages","java")
op_redis.lpush("languages","python")
op_redis.lpush("languages","c/c++")
# 获取集合中的所有元素
print(op_redis.lrange("languages",0,-1))


# 3.集合的操作
# 添加
op_redis.sadd("like","a")
op_redis.sadd("like","b")
op_redis.sadd("like","c")
# 获取集合中的所有元素
like = op_redis.smembers('like')
print(like)


# 4.哈希的操作
# 添加web键，和几个字段以及对应的值
op_redis.hset('web','baidu','www.baidu.com')
op_redis.hset('web','python','www.python.com')
op_redis.hset('web','google','www.google.com')
# 获取所有的字段及其值
web = op_redis.hgetall('web')
print(web)


# 5.事务操作
# 在redis中执行事务，需要借助pipline()函数
pip = op_redis.pipeline()
# 绑定命令
pip.set('name','angle')
pip.set('name','miku')
# 执行事务
pip.execute()



# 6.发布与订阅功能

# 接收
# 异步发送邮件的功能
# 获取对象
ps = op_redis.pubsub()
# 频道
ps.subscribe('email')
while True:
    # 监听该频道
    for item in ps.listen():
        # print(item)
        if item['type'] == 'message':
            data = item['data']
            print(data)
-------------------------------------------------------
# 发送
from redis import Redis
op_redis = Redis(host="192.168.5.128",port=6379)
# 从email频道发布
op_redis.publish('email','python')
```

