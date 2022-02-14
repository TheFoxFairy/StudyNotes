# Redis笔记

## NoSQL概述

### Mysql演进过程

#### 单机MySQL

![image-20220209112704197](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251959.png)

90年代，一个基本的网站访问量一般不会太大，单个数据库完全足够！

那个时候，更多的去使用静态网页 Html ~ 服务器根本没有太大的压力！

思考一下，这种情况下：整个网站的瓶颈是什么？ 

1. 数据量如果太大、一个机器放不下了！ 

2. 数据的索引 （B+ Tree），一个机器内存也放不下 

3. 访问量（读写混合），一个服务器承受不了~ 

只要你开始出现以上的三种情况之一，那么你就必须要晋级！

#### Memcached（缓存） + MySQL + 垂直拆分 （读写分离）

网站80%的情况都是在读，每次都要去查询数据库的话就十分的麻烦！所以说我们希望减轻数据的压 力，我们可以使用缓存来保证效率！

发展过程： 优化数据结构和索引--> 文件缓存（IO）---> Memcached（当时最热门的技术！）

![image-20220209113045009](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251961.png)

#### 分库分表 + 水平拆分 + MySQL集群

技术和业务在发展的同时，对人的要求也越来越高！

> 本质：数据库（读，写）

早些年MyISAM： 表锁，十分影响效率！高并发下就会出现严重的锁问题

转战Innodb：行锁

慢慢的就开始使用分库分表来解决写的压力！ MySQL 在那个年代推出了表分区！这个并没有多少公司使用！ 

MySQL 的 集群，很好满足哪个年代的所有需求！

![image-20220209113226073](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251962.png)

#### 当今的企业架构分析

2010--2020 十年之间，世界已经发生了翻天覆地的变化；（定位，也是一种数据，音乐，热榜！）

MySQL 等关系型数据库就不够用了！数据量很多，变化很快~！

MySQL 有的使用它来村粗一些比较大的文件，博客，图片！

数据库表很大，效率就低了！如果有一种数据库来专门处理这种数据, MySQL压力就变得十分小（研究如何处理这些问题！）大数据的IO压力下，表几乎没法更大！

##### 目前一个基本的互联网项目！

![image-20220209121317474](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251963.png)

##### 为什么要用NoSQL！

用户的个人信息，社交网络，地理位置。用户自己产生的数据，用户日志等等爆发式增长！

这时候我们就需要使用NoSQL数据库的，Nosql 可以很好的处理以上的情况！

### 什么是NoSQL

#### NoSQL

NoSQL = Not Only SQL （不仅仅是SQL）

泛指非关系型数据库的，随着web2.0互联网的诞生！传统的关系型数据库很难对付web2.0时代！尤其 是超大规模的高并发的社区！ 暴露出来很多难以克服的问题，NoSQL在当今大数据环境下发展的十分迅 速，Redis是发展最快的，而且是我们当下必须要掌握的一个技术！

很多的数据类型用户的个人信息，社交网络，地理位置。这些数据类型的存储不需要一个固定的格式！ 不需要多月的操作就可以横向扩展的 ！ `Map<String,Object>`使用键值对来控制！

#### NoSQL 特点

解耦！

- 1、方便扩展（数据之间没有关系，很好扩展！）
- 2、大数据量高性能（Redis 一秒写8万次，读取11万，NoSQL的缓存记录级，是一种细粒度的缓存，性 能会比较高！） 
- 3、数据类型是多样型的！（不需要事先设计数据库！随取随用！如果是数据量十分大的表，很多人就无 法设计了！） 
- 4、传统 RDBMS 和 NoSQL

```
传统的 RDBMS
- 结构化组织
- SQL
- 数据和关系都存在单独的表中 row col
- 操作操作，数据定义语言
- 严格的一致性
- 基础的事务
- .....


Nosql
- 不仅仅是数据
- 没有固定的查询语言
- 键值对存储，列存储，文档存储，图形数据库（社交关系）
- 最终一致性，
- CAP定理和BASE （异地多活） 初级架构师！（狂神理念：只要学不死，就往死里学！）
- 高性能，高可用，高可扩
- ....
```

### 了解：3V+3高

大数据时代的3V：主要是描述问题的 

1. 海量Volume 
2. 多样Variety
3. 实时Velocity 

大数据时代的3高：主要是对程序的要求 

1. 高并发 
2. 高可扩 
3. 高性能

真正在公司中的实践：NoSQL + RDBMS 一起使用才是最强的，阿里巴巴的架构演进！

技术没有高低之分，就看你如何去使用！（提升内功，思维的提高！）

### 阿里巴巴实践分析理解数据架构演进

![image-20220209124320994](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251964.png)

技术急不得，越是慢慢学，才能越扎实！ 

开源才是技术的王道！ 任何一家互联网的公司，都不可能只是简简单单让用户能用就好了！ 

大量公司做的都是相同的业务；（竞品协议） 

随着这样的竞争，业务是越来越完善，然后对于开发者的要求也是越来越高！

![image-20220209124411403](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251965.png)如果你未来相当一个架构师： 没有什么是加一层解决不了的！

```properties
# 1、商品的基本信息
    bilibili：狂神说Java
    要知道，一个简单地网页背后的技术一定不是大家所想的那么简单！
    大型互联网应用问题：
    数据类型太多了！
    数据源繁多，经常重构！
    数据要改造，大面积改造？
    解决问题：
    名称、价格、商家信息；
    关系型数据库就可以解决了！ MySQL / Oracle （淘宝早年就去IOE了！- 王坚：推荐文章：阿里云
    的这群疯子：40分钟重要！）
    淘宝内部的 MySQL 不是大家用的 MySQL
# 2、商品的描述、评论（文字比较多）
	文档型数据库中，MongoDB
# 3、图片
    分布式文件系统 FastDFS
    - 淘宝自己的 TFS
    - Gooale的 GFS
    - Hadoop HDFS
    - 阿里云的 oss
# 4、商品的关键字 （搜索）
    - 搜索引擎 solr elasticsearch
    - ISerach：多隆（多去了解一下这些技术大佬！）
    所有牛逼的人都有一段苦逼的岁月！但是你只要像SB一样的去坚持，终将牛逼！
# 5、商品热门的波段信息
    - 内存数据库
    - Redis Tair、Memache...
# 6、商品的交易，外部的支付接口
	- 三方应用
```

要知道，一个简单地网页背后的技术一定不是大家所想的那么简单！

大型互联网应用问题： 

- 数据类型太多了！ 
- 数据源繁多，经常重构！ 
- 数据要改造，大面积改造？ 

解决问题：

![image-20220209124742634](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251966.png)

![image-20220209124752602](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251967.png)

这里以上都是NoSQL入门概述，不仅能够提高大家的知识，还可以帮助大家了解大厂的工作内容！

### NoSQL的四大分类

**KV键值对：** 

- 新浪：Redis 
- 美团：Redis + Tair 

阿里、百度：Redis + memecache 

**文档型数据库（bson格式 和json一样）：** 

- MongoDB （一般必须要掌握） 
  - MongoDB 是一个基于分布式文件存储的数据库，C++ 编写，主要用来处理大量的文档！
  - MongoDB 是一个介于关系型数据库和非关系型数据中中间的产品！MongoDB 是非关系型数 据库中功能最丰富，最像关系型数据库的！ 
- **ConthDB**  

**列存储数据库**

- HBase
- 分布式文件系统

**图关系数据库**

![image-20220209131138098](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251968.png)

- 他不是存图形，放的是关系，比如：朋友圈社交网络，广告推荐！ 
- **Neo4j**，InfoGrid；

**对比**

![image-20220209131239107](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251969.png)

## Redis入门

### 概述

* [Redis官网](https://redis.io/)
* [Redis中文网](http://www.redis.cn/)
* [Redis中文教程](https://www.redis.com.cn/)

> 注意：Wdinow在 Github上下载（停更很久了！） 
>
> Redis推荐都是在Linux服务器上搭建的，基于Linux学习！

#### 什么是Redis？

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件. 它支持多种类型的数据结构，如 [字符串（strings）](https://www.redis.com.cn/topics/data-types-intro.html#strings)， [散列（hashes）](https://www.redis.com.cn/topics/data-types-intro.html#hashes)， [列表（lists）](https://www.redis.com.cn/topics/data-types-intro.html#lists)， [集合（sets）](https://www.redis.com.cn/topics/data-types-intro.html#sets)， [有序集合（sorted sets）](https://www.redis.com.cn/topics/data-types-intro.html#sorted-sets) 与范围查询， [bitmaps](https://www.redis.com.cn/topics/data-types-intro.html#bitmaps)， [hyperloglogs](https://www.redis.com.cn/topics/data-types-intro.html#hyperloglogs) 和 [地理空间（geospatial）](https://www.redis.com.cn/commands/geoadd.html) 索引半径查询. Redis 内置了 [复制（replication）](https://www.redis.com.cn/topics/replication.html)， [LUA脚本（Lua scripting）](https://www.redis.com.cn/commands/eval.html)， [LRU驱动事件（LRU eviction）](https://www.redis.com.cn/topics/lru-cache.html)， [事务（transactions）](https://www.redis.com.cn/topics/transactions.html) 和不同级别的 [磁盘持久化（persistence）](https://www.redis.com.cn/topics/persistence.html)， 并通过 [Redis哨兵（Sentinel）](https://www.redis.com.cn/topics/sentinel.html) 和自动 [分区（Cluster）](https://www.redis.com.cn/topics/cluster-tutorial.html)提供高可用性（high availability）.

#### 用途

- 内存存储、持久化，内存中是断电即失、所以说持久化很重要（rdb、aof） 
- 效率高，可以用于高速缓存 
- 发布订阅系统 
- 地图信息分析 
- 计时器、计数器（浏览量！）
- …

#### 特性

- 多样的数据类型
- 持久化
- 集群
- 事务

用途

### 安装

#### windows下安装

* https://github.com/microsoftarchive/redis/releases
* 将redis注册成服务（一般都会默认注册）

```sh
# 注册服务 
redis-server.exe –service-install redis.windows.conf

# 删除服务 
redis-server –service-uninstall

# 开启服务 
redis-server –service-start

# 停止服务 
redis-server –service-stop
```

* 开启服务后，打开控制平台，输入`redis-cli`

#### Linux下安装

* http://www.redis.cn/download.html

* 下载redis

```sh
wget http://download.redis.io/releases/redis-6.0.6.tar.gz
```

* 解压redis

```sh
tar xzf redis-6.0.6.tar.gz
```

* 安装GCC

```sh
sudo apt update
sudo apt install build-essential
```

* 编译redis

```sh
sudo make
sudo make install
```

* redis的默认安装路径 `/usr/local/bin`

![image-20220209145324021](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251970.png)

* 将redis配置文件。复制到当前目录下

```sh
sudo mkdir redis-config

sudo cp ~/opt/redis-6.0.6/redis.conf redis-config

cd redis-config

ls
```

![image-20220209145838420](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251971.png)

* redis默认不是后台启动的，修改配置文件！

```sh
sudo vim redis.conf
```

> vim中的保存退出命令是wq。具体方法：首先按i键进入插入模式；然后编辑文件；接着按esc键进入命令行模式；最后输入【:wq】即可保存文件并退出。

![image-20220209150138044](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251972.png)

* 启动Redis服务！

```sh
redis-server redis-config/redis.conf
```

![image-20220209150447833](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251973.png)

* 使用redis-cli 进行连接测试！

```sh
redis -p 6379
```

![image-20220209150636611](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251974.png)

* 查看redis的进程是否开启！

```sh
ps -ef|grep redis
```

![image-20220209150808328](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251975.png)

* 如何关闭Redis服务呢？

```sh
sudo mkdir redis-log
sudo vim redis_log.log

cd redis-config/redis.conf
sudo vim redis.conf
```

> 在命令模式下敲斜杆( / )这时在状态栏（也就是屏幕左下脚）就出现了 “/” 然后输入你要查找的关键字敲回车就可以了。

设置日志`logfile`，`logfile /usr/local/bin/redis-log/redis_log.log`

![image-20220209152607028](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251976.png)

然后继续修改`dir`，`dir /usr/local/bin/redis-dbfile/`

```sh
sudo mkdir redis-dbfile
sudo vim redis.conf
```

![image-20220209152135216](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251977.png)

赋予读写权限使用指令： `sudo chmod 777 redis-dbfile`

```sh
sudo chmod 777 redis-config/redis.conf 
sudo chmod 777 redis-dbfile
sudo chmod 777 redis-log/redis_log.log
```

然后进行关闭服务

![image-20220209153619505](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251978.png)

* 再次查看进程是否存在

![image-20220209153706262](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251979.png)

### 可视化工具

* [redis-insight](https://redis.com/redis-enterprise/redis-insight/#insight-form)

![RedisInsight interface](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251980.jpg)

### 命令行美化工具

```sh
pip install iredis
```

### 性能测试

- redis-benchmark 是一个压力测试工具！ 

redis 性能测试工具可选参数如下所示：

| 序号 | 选项                   | 描述                                       | 默认值    |
| :--- | :--------------------- | :----------------------------------------- | :-------- |
| 1    | **-h**                 | 指定服务器主机名                           | 127.0.0.1 |
| 2    | **-p**                 | 指定服务器端口                             | 6379      |
| 3    | **-s**                 | 指定服务器 socket                          |           |
| 4    | **-c**                 | 指定并发连接数                             | 50        |
| 5    | **-n**                 | 指定请求数                                 | 10000     |
| 6    | **-d**                 | 以字节的形式指定 SET/GET 值的数据大小      | 2         |
| 7    | **-k**                 | 1=keep alive 0=reconnect                   | 1         |
| 8    | **-r**                 | SET/GET/INCR 使用随机 key, SADD 使用随机值 |           |
| 9    | **-P**                 | 通过管道传输 `<numreq>` 请求               | 1         |
| 10   | **-q**                 | 强制退出 redis。仅显示 query/sec 值        |           |
| 11   | **--csv**              | 以 CSV 格式输出                            |           |
| 12   | **-l（L 的小写字母）** | 生成循环，永久执行测试                     |           |
| 13   | **-t**                 | 仅运行以逗号分隔的测试命令列表。           |           |
| 14   | **-I（i 的大写字母）** | Idle 模式。仅打开 N 个 idle 连接并等待。   |           |
| 15   | --threads              | 指定线程数目`<num>`                        |           |

* 来简单测试下

```sh
redis-benchmark -h localhost -p 6379 -c 100 -n 10000
```

![image-20220209154206150](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251981.png)

* 如何查看这些分析呢？

![image-20220209154224082](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251982.png)

### 基础知识

#### 默认数据库

redis的默认数据库有16个，而且默认使用的是第0个

![image-20220210000546224](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251984.png)

#### 查看服务是否运行

通过`ping`查看服务是否运行，返回`PONG`表示正常运行

```sh
127.0.0.1:6379[1]> ping
PONG
```

#### 切换数据库sh

可以通过使用`select`进行切换数据库

```sh
127.0.0.1:6379> select 3 # 切换数据库
OK
127.0.0.1:6379[3]> dbsize # 查看DB大小
(integer) 0

```

![image-20220210000734632](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251985.png)

#### 查看数据库中的所有key

```sh
127.0.0.1:6379[3]> set name hutao
OK
127.0.0.1:6379[3]> keys *
1) "name"
127.0.0.1:6379[3]> 
```

#### 清除当前数据库

```sh
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> 
```

#### 清除全部数据库内容

```sh
flushall
```

#### Redis 管理 redis 服务相关命令

下表列出了管理 redis 服务相关的命令

| 命令                                                         | 描述                                             |
| ------------------------------------------------------------ | ------------------------------------------------ |
| [BGREWRITEAOF](https://www.redis.com.cn/commands/bgrewriteaof.html) | 异步执行一个 AOF（AppendOnly File） 文件重写操作 |
| [BGSAVE](https://www.redis.com.cn/commands/bgsave.html)      | 在后台异步保存当前数据库的数据到磁盘             |
| [CLIENT](https://www.redis.com.cn/commands/client-kill.html) | 关闭客户端连接                                   |
| [CLIENT LIST](https://www.redis.com.cn/commands/client-list.html) | 获取连接到服务器的客户端连接列表                 |
| [CLIENT GETNAME](https://www.redis.com.cn/commands/client-getname.html) | 获取连接的名称                                   |
| [CLIENT PAUSE](https://www.redis.com.cn/commands/client-pause.html) | 在指定时间内终止运行来自客户端的命令             |
| [CLIENT SETNAME](https://www.redis.com.cn/commands/client-setname.html) | 设置当前连接的名称                               |
| [CLUSTER SLOTS](https://www.redis.com.cn/commands/cluster-slots.html) | 获取集群节点的映射数组                           |
| [COMMAND](https://www.redis.com.cn/commands/command.html)    | 获取 Redis 命令详情数组                          |
| [COMMAND COUNT](https://www.redis.com.cn/commands/command-count.html) | 获取 Redis 命令总数                              |
| [COMMAND GETKEYS](https://www.redis.com.cn/commands/command-getkeys.html) | 获取给定命令的所有键                             |
| [TIME](https://www.redis.com.cn/commands/time.html)          | 返回当前服务器时间                               |
| [COMMAND INFO](https://www.redis.com.cn/commands/command-info.html) | 获取指定 Redis 命令描述的数组                    |
| [CONFIG GET](https://www.redis.com.cn/commands/config-get.html) | 获取指定配置参数的值                             |
| [CONFIG REWRITE](https://www.redis.com.cn/commands/config-rewrite.html) | 修改 redis.conf 配置文件                         |
| [CONFIG SET](https://www.redis.com.cn/commands/config-set.html) | 修改 redis 配置参数，无需重启                    |
| [CONFIG RESETSTAT](https://www.redis.com.cn/commands/config-resetstat.html) | 重置 INFO 命令中的某些统计数据                   |
| [DBSIZE](https://www.redis.com.cn/commands/dbsize.html)      | 返回当前数据库的 key 的数量                      |
| [DEBUG OBJECT](https://www.redis.com.cn/commands/debug-object.html) | 获取 key 的调试信息                              |
| [DEBUG SEGFAULT](https://www.redis.com.cn/commands/debug-segfault.html) | 让 Redis 服务崩溃                                |
| [FLUSHALL](https://www.redis.com.cn/commands/flushall.html)  | 删除所有数据库的所有 key                         |
| [FLUSHDB](https://www.redis.com.cn/commands/flushdb.html)    | 删除当前数据库的所有 key                         |
| [INFO](https://www.redis.com.cn/commands/info.html)          | 获取 Redis 服务器的各种信息和统计数值            |
| [LASTSAVE](https://www.redis.com.cn/commands/lastsave.html)  | 返回最近一次 Redis 成功将数据保存到磁盘上的时间  |
| [MONITOR](https://www.redis.com.cn/commands/monitor.html)    | 实时打印出 Redis 服务器接收到的命令，调试用      |
| [ROLE](https://www.redis.com.cn/commands/role.html)          | 返回主从实例所属的角色                           |
| [SAVE](https://www.redis.com.cn/commands/save.html)          | 异步保存数据到硬盘                               |
| [SHUTDOWN](https://www.redis.com.cn/commands/shutdown.html)  | 异步保存数据到硬盘，并关闭服务器                 |
| [SLAVEOF](https://www.redis.com.cn/commands/slaveof.html)    | 将当前服务器转变从属服务器(slave server)         |
| [SLOWLOG](https://www.redis.com.cn/commands/showlog.html)    | 管理 redis 的慢日志                              |
| [SYNC](https://www.redis.com.cn/commands/sync.html)          | 用于复制功能 ( replication ) 的内部命令          |

#### 单线程redis

##### Redis 是单线程的！ 

明白Redis是很快的，官方表示，Redis是基于内存操作，CPU不是Redis性能瓶颈，Redis的瓶颈是根据 机器的内存和网络带宽，既然可以使用单线程来实现，就使用单线程了！所有就使用了单线程了！

 Redis 是C 语言写的，官方提供的数据为 100000+ 的QPS，完全不比同样是使用 key-vale的 Memecache差！

##### Redis 为什么单线程还这么快？

- 误区1：高性能的服务器一定是多线程的？ 
- 误区2：多线程（CPU上下文会切换！）一定比单线程效率高！ 先去CPU>内存>硬盘的速度要有所了解！

核心：redis 是将所有的数据全部放在内存中的，所以说使用单线程去操作效率就是最高的，多线程 （CPU上下文会切换：耗时的操作！！！），对于内存系统来说，如果没有上下文切换效率就是最高 的！多次读写都是在一个CPU上的，在内存情况下，这个就是最佳的方案！

> 那些之前的版本，现在新版本已经有多线程了。如果Redis支持了多线程（目前多线程的Redis最对不建议超出8个线程）

## Redis语法

### Redis Key

#### 获取所有键值keys

* 通过`keys *`获取所有键值

```sh
127.0.0.1:6379> set name hutao
OK
127.0.0.1:6379> get name
"hutao"
127.0.0.1:6379> set age 18
OK
127.0.0.1:6379> keys *
1) "age"
2) "name"
```

* 通过`keys`进行模糊查询

在redis里有3个通配符用于模糊查询key：

- *: 通配任意多个字符
- ?: 通配单个字符
- []: 通配括号内的某1个字符

```sh
127.0.0.1:6379> keys *
1) "age"
2) "name"
127.0.0.1:6379> keys a*
1) "age"
127.0.0.1:6379> keys *[a]*
1) "age"
2) "name"
127.0.0.1:6379> keys ?[a]*
1) "name"
127.0.0.1:6379> 
```

#### 判断键值key是否存在

通过`exists key`判断键值`key`是否存在，存在该键值返回1，否则返回0

```sh
127.0.0.1:6379> exists name
(integer) 1
127.0.0.1:6379> exists username
(integer) 0
127.0.0.1:6379> keys *
1) "age"
2) "name"
```

#### 移动键值队key-value

通过`move key dbNumber`移动键值队key-value到指定数据库dbNumber

```sh
127.0.0.1:6379> set a 1
OK
127.0.0.1:6379> set b 2
OK
127.0.0.1:6379> set c 3
OK
127.0.0.1:6379> move a 1
(integer) 1
127.0.0.1:6379> keys *
1) "c"
2) "b"
127.0.0.1:6379> select  1
OK
127.0.0.1:6379[1]> keys *
1) "a"
```

#### 删除键值对key-value

```sh
127.0.0.1:6379[1]> del a
(integer) 1
127.0.0.1:6379[1]> keys *
(empty array)
127.0.0.1:6379[1]> 
```

#### 清理当前屏幕

```sh
clear
```

#### 设置过期时间

* 通过`expire key seconds`设置过期时间

```sh
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> set name hutao
OK
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> expire name 5 # 设置过期时间
(integer) 1
127.0.0.1:6379> keys *
1) "name"
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> 
```

* 通过`ttl key`查看键值`key`的过期时间还有多久

```sh
127.0.0.1:6379> set name hutao
OK
127.0.0.1:6379> expire name 5
(integer) 1
127.0.0.1:6379> ttl name
(integer) 2
127.0.0.1:6379> ttl name
(integer) -2
127.0.0.1:6379> keys *
(empty array)
127.0.0.1:6379> 
```

#### 查看键值对数据类型

通过`type key`查看`key`键值的存储的数据类型

```sh
127.0.0.1:6379> set name hutao
OK
127.0.0.1:6379> type name
string
127.0.0.1:6379> 
```

#### Redis键相关的命令

| 命令                                                         | 描述                                                  |
| ------------------------------------------------------------ | ----------------------------------------------------- |
| [DEL](https://www.redis.com.cn/commands/del.html)            | 用于删除 key                                          |
| [DUMP](https://www.redis.com.cn/commands/dump.html)          | 序列化给定 key ，并返回被序列化的值                   |
| [EXISTS](https://www.redis.com.cn/commands/exists.html)      | 检查给定 key 是否存在                                 |
| [EXPIRE](https://www.redis.com.cn/commands/expire.html)      | 为给定 key 设置过期时间                               |
| [EXPIREAT](https://www.redis.com.cn/commands/expireat.html)  | 用于为 key 设置过期时间，接受的时间参数是 UNIX 时间戳 |
| [PEXPIRE](https://www.redis.com.cn/commands/pexpire.html)    | 设置 key 的过期时间，以毫秒计                         |
| [PEXPIREAT](https://www.redis.com.cn/commands/pexpireat.html) | 设置 key 过期时间的时间戳(unix timestamp)，以毫秒计   |
| [KEYS](https://www.redis.com.cn/commands/keys.html)          | 查找所有符合给定模式的 key                            |
| [MOVE](https://www.redis.com.cn/commands/move.html)          | 将当前数据库的 key 移动到给定的数据库中               |
| [PERSIST](https://www.redis.com.cn/commands/persist.html)    | 移除 key 的过期时间，key 将持久保持                   |
| [PTTL](https://www.redis.com.cn/commands/pttl.html)          | 以毫秒为单位返回 key 的剩余的过期时间                 |
| [TTL](https://www.redis.com.cn/commands/ttl.html)            | 以秒为单位，返回给定 key 的剩余生存时间(              |
| [RANDOMKEY](https://www.redis.com.cn/commands/randomkey.html) | 从当前数据库中随机返回一个 key                        |
| [RENAME](https://www.redis.com.cn/commands/rename.html)      | 修改 key 的名称                                       |
| [RENAMENX](https://www.redis.com.cn/commands/renamenx.html)  | 仅当 newkey 不存在时，将 key 改名为 newkey            |
| [TYPE](https://www.redis.com.cn/commands/type.html)          | 返回 key 所储存的值的类型                             |

### 五大基本数据类型

#### String（字符串）

Redis 字符串命令用于管理 Redis 中的字符串值。

##### 设置键值对

* 通过`set name value`设置键值`key`的值

```sh
127.0.0.1:6379[1]> set name hutao
OK
```

* 通过`setnx key value`，如果key 不存在，创建key 

> 不存在在设置 （在分布式锁中会常常使用！）

```sh
127.0.0.1:6379[1]> set name hutao
OK
127.0.0.1:6379[1]> setnx name hutao1 # 存在key，不能被创建
(integer) 0
127.0.0.1:6379[1]> get name
"hutao"
127.0.0.1:6379[1]> setnx name1 hutao1 # 不存在，创建key
(integer) 1
127.0.0.1:6379[1]> get name1
"hutao1"
```

* 通过`getset key value`先获取key
  * 如果不存在，则返回(nil)，并设置value
  * 如果存在值，获取原来的值，并设置新的值value

```sh
127.0.0.1:6379[1]> getset db redis
(nil)
127.0.0.1:6379[1]> get db
"redis"
```

##### 获取键值对

通过`get key`获取键值`key`的值

```sh
127.0.0.1:6379[1]> get name
"hutao"
```

##### 获得所有的key

通过`keys *`获取所有键值

```sh
127.0.0.1:6379[1]> keys *
1) "name"
```

##### 判断key是否存在

通过`exists key`判断某一个key是否存在，存在返回1，否则返回0

```sh
127.0.0.1:6379[1]> exists name
(integer) 1
127.0.0.1:6379[1]> exists name1
(integer) 0
```

##### 追加字符串

通过`append key value`如果当前key不存在，就相当于setkey

```sh
127.0.0.1:6379[1]> append name 123
(integer) 8
127.0.0.1:6379[1]> get name
"hutao123"
```

##### 字符串长度

通过`strlen key`获取键值`key`的字符串的长度

```sh
127.0.0.1:6379[1]> strlen name
(integer) 8
```

##### 逻辑运算

> 值一定要为数字

###### 自增

* `incr`自增1

```sh
127.0.0.1:6379[1]> flushdb
OK
127.0.0.1:6379[1]> set num 1
OK
127.0.0.1:6379[1]> incr num
(integer) 2
127.0.0.1:6379[1]> get num
"2"
```

* `incrby key len`指定自增长度

```sh
127.0.0.1:6379[1]> incrby num 10
(integer) 12
127.0.0.1:6379[1]> get num
"12"
```

###### 自减

- `decr`自减1

```sh
127.0.0.1:6379[1]> decr num
(integer) 11
127.0.0.1:6379[1]> get num
"11"
```

- `decrby key len`指定自减长度

```sh
127.0.0.1:6379[1]> decrby num 10
(integer) 1
127.0.0.1:6379[1]> get num
"1"
```

##### 截取字符串

通过`getrange key startIndex endIndex`从第startIndex字符到第endIndex截取字符串，如果endIndex值为-1，则会获取从第startIndex之后的所有字符。

```sh
127.0.0.1:6379[1]> set say "hello,hutao"
OK
127.0.0.1:6379[1]> get say
"hello,hutao"
127.0.0.1:6379[1]> strlen say
(integer) 11
127.0.0.1:6379[1]> getrange say 0 4 # 截取字符串 [0,4]
"hello"
127.0.0.1:6379[1]> getrange say 6 -1 # 截取字符串 [6,11]
"hutao"
127.0.0.1:6379[1]> 
```

##### 替换字符串

通过`setrange key index newValue`替换第index开始的字符串

```sh
127.0.0.1:6379[1]> setrange say 0 112
(integer) 11
127.0.0.1:6379[1]> get say
"112lo,hutao"
```

##### 设置过期时间

通过`setex key seconds value`设置键值对`key-value`的过期时间`seconds`

```sh
127.0.0.1:6379[1]> setex name 5 hutao # 设置键值name的值hutao，在5秒后过期
OK
127.0.0.1:6379[1]> ttl name # 查看还有几秒过期
(integer) 2

```

##### 同时设置多个值

* 通过`mset key1 value1 key2 value2 ...`创建多个键值对

```sh
127.0.0.1:6379[1]> mset a 1 b 2 c 3
OK
127.0.0.1:6379[1]> keys *
1) "c"
2) "a"
3) "b"
127.0.0.1:6379[1]> 
```

*  `msetnx` 是一个原子性的操作，要么一起成功，要么一起 失败！

是`setnx`的多个操作

```sh
127.0.0.1:6379[1]> flushdb
OK
127.0.0.1:6379[1]> mset a 1 b 2 c 3
(integer) 1
127.0.0.1:6379[1]> msetnx a 1 b 2 d 3
(integer) 0
127.0.0.1:6379[1]> msetnx d 4 e 5
(integer) 1
127.0.0.1:6379[1]> keys *
1) "c"
2) "e"
3) "d"
4) "a"
5) "b"
```

##### 同时获取多个值

通过`mget key1 value1 key2 value2 ...`获取多个键值对

```sh
127.0.0.1:6379[1]> mget a b c
1) "1"
2) "2"
3) "3"
```

##### 设置对象

```
127.0.0.1:6379[1]> set user:1 {name:hutao,age:18}
OK
127.0.0.1:6379[1]> get user:1
"{name:hutao,age:18}"
127.0.0.1:6379[1]> mset user:1:name hutao user:1:age 2
OK
127.0.0.1:6379[1]> mget user:1:name user:1:age
1) "hutao"
2) "2"
```

> 这里的key是一个巧妙的设计： user:{id}:{filed} , 如此设计在Redis中是完全OK了！

##### Redis字符串命令

以下是一些用于在 Redis 中管理字符串的基本命令的列表：

| 命令                                                         | 描述                                                        |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| [SET](https://www.redis.com.cn/commands/set.html)            | 设置指定 key 的值                                           |
| [GET](https://www.redis.com.cn/commands/get.html)            | 获取指定 key 的值                                           |
| [GETRANGE](https://www.redis.com.cn/commands/getrange.html)  | 返回 key 中字符串值的子字符                                 |
| [GETSET](https://www.redis.com.cn/commands/getset.html)      | 将给定 key 的值设为 value ，并返回 key 的旧值 ( old value ) |
| [GETBIT](https://www.redis.com.cn/commands/getbit.html)      | 对 key 所储存的字符串值，获取指定偏移量上的位 ( bit )       |
| [MGET](https://www.redis.com.cn/commands/mget.html)          | 获取所有(一个或多个)给定 key 的值                           |
| [SETBIT](https://www.redis.com.cn/commands/setbit.html)      | 对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)    |
| [SETEX](https://www.redis.com.cn/commands/setex.html)        | 设置 key 的值为 value 同时将过期时间设为 seconds            |
| [SETNX](https://www.redis.com.cn/commands/setnx.html)        | 只有在 key 不存在时设置 key 的值                            |
| [SETRANGE](https://www.redis.com.cn/commands/setrange.html)  | 从偏移量 offset 开始用 value 覆写给定 key 所储存的字符串值  |
| [STRLEN](https://www.redis.com.cn/commands/strlen.html)      | 返回 key 所储存的字符串值的长度                             |
| [MSET](https://www.redis.com.cn/commands/mset.html)          | 同时设置一个或多个 key-value 对                             |
| [MSETNX](https://www.redis.com.cn/commands/msetnx.html)      | 同时设置一个或多个 key-value 对                             |
| [PSETEX](https://www.redis.com.cn/commands/psetex.html)      | 以毫秒为单位设置 key 的生存时间                             |
| [INCR](https://www.redis.com.cn/commands/incr.html)          | 将 key 中储存的数字值增一                                   |
| [INCRBY](https://www.redis.com.cn/commands/incrby.html)      | 将 key 所储存的值加上给定的增量值 ( increment )             |
| [INCRBYFLOAT](https://www.redis.com.cn/commands/incrbyfloat.html) | 将 key 所储存的值加上给定的浮点增量值 ( increment )         |
| [DECR](https://www.redis.com.cn/commands/decr.html)          | 将 key 中储存的数字值减一                                   |
| [DECRBY](https://www.redis.com.cn/commands/decrby.html)      | 将 key 所储存的值减去给定的减量值 ( decrement )             |
| [APPEND](https://www.redis.com.cn/commands/append.html)      | 将 value 追加到 key 原来的值的末尾                          |

String类似的使用场景：value除了是我们的字符串还可以是我们的数字！ 

- 计数器
- 统计多单位的数量
- 粉丝数
- 对象缓存存储

#### List（列表）

Redis 列表是按插入顺序排序的字符串列表。可以在列表的头部（左边）或尾部（右边）添加元素。

> **消息排队！消息队列 （Lpush Rpop）（左进右出）， 栈（ Lpush Lpop）（左进左出）！**

![image-20220210023610437](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251986.png)

##### 插入值

* 通过`lpush keyList value1 [value2 ...]`将一个或者多个value插入到列表左边

```sh
127.0.0.1:6379[1]> flushdb
OK
127.0.0.1:6379[1]> lpush keyList three
(integer) 1
127.0.0.1:6379[1]> lpush keyList fourth
(integer) 2
127.0.0.1:6379[1]> lpush keyList fifth sixth
(integer) 4
127.0.0.1:6379[1]> lrange keyList 0 -1 # 查看keyList列表所有值
1) "sixth"
2) "fifth"
3) "fourth"
4) "three"

```

* 通过`rpush keyList value1 [value2 ...]`将一个或者多个value插入到列表右边

```sh
127.0.0.1:6379[1]> rpush keyList second first
(integer) 6
127.0.0.1:6379[1]> lrange keyList 0 -1
1) "sixth"
2) "fifth"
3) "fourth"
4) "three"
5) "second"
6) "first"
```

* 通过`linsert keyList before[afrer] value1 value2 ` 将某个具体的value2插入到列把你中某个元素value1 的前面或者后面！

```sh
127.0.0.1:6379[1]> flushdb
OK
127.0.0.1:6379[1]> lpush keyList 3
(integer) 1

# 插入到前面
127.0.0.1:6379[1]> linsert keyList before 3 2
(integer) 2

127.0.0.1:6379[1]> lrange keyList 0 -1
1) "2"
2) "3"

# 插入到后面
127.0.0.1:6379[1]> linsert keyList after 3 1
(integer) 3


127.0.0.1:6379[1]> lrange keyList 0 -1
1) "2"
2) "3"
3) "1"
127.0.0.1:6379[1]> 

```

##### 获取值

通过`lrange keyList startIndex endIndex`从列表左边（开头处）第startIndex开始到第endIndex获取列表中的所有值，如果endIndex值为-1，则获取第startIndex开始到最后的所有字符。

```sh
127.0.0.1:6379[1]> lrange keyList 0 3
1) "sixth"
2) "fifth"
3) "fourth"
4) "three"
```

##### 移除元素

* 通过`Lpop keyList`移除列表左边（开头处）第一个元素

```sh
127.0.0.1:6379[1]> lpop keyList
"sixth"
```

* 通过`rpop keyList`移除列表右边（末尾处）第一个元素

```sh
127.0.0.1:6379[1]> rpop keyList
"first"
```

* 通过`lrem keyList count value`移除列表keyList中指定个数count的value

```sh
127.0.0.1:6379[1]> lrem keyList 1 second
(integer) 1
127.0.0.1:6379[1]> lrange keyList 0 -1
1) "fifth"
2) "fourth"
3) "three"
```

* 通过`rpoplpush keyList newKeyList`移除列表keyList的最后一个元素，将他移动到新的列表newKeyList中！

```sh
127.0.0.1:6379[1]> flushdb
OK
127.0.0.1:6379[1]> lpush keyList 1
(integer) 1
127.0.0.1:6379[1]> lpush keyList 2
(integer) 2
127.0.0.1:6379[1]> lpush keyList 3
(integer) 3

 # 移除列表的最后一个元素，将他移动到新的列表中！
127.0.0.1:6379[1]> rpoplpush keyList keyList2
"1"

127.0.0.1:6379[1]> lrange keyList 0 -1 # 查看原来的列表
1) "3"
2) "2"
127.0.0.1:6379[1]> lrange keyList2 0 -1 # 查看目标列表中，确实存在改值！
1) "1"
127.0.0.1:6379[1]> 
```

##### 通过下标获取值

通过`lindex keyList index` 获取列表keyList第index处的值

```sh
127.0.0.1:6379[1]> lrange keyList 0 -1
1) "fifth"
2) "fourth"
3) "three"
4) "second"
127.0.0.1:6379[1]> lindex keyList 2
"three"
```

##### 获取列表长度

通过`llen keyList`获取列表keyList的长度

```sh
127.0.0.1:6379[1]> lrange keyList 0 -1
1) "fifth"
2) "fourth"
3) "three"
4) "second"
127.0.0.1:6379[1]> llen keyList
(integer) 4
```

##### 截断列表数据

* 通过`ltrim keyList startIndex endIndex`从第startIndex到第endIndex对列表进行截断，并返回截取的部分

```sh
127.0.0.1:6379[1]> flushdb
OK
127.0.0.1:6379[1]> lpush keyList 1
(integer) 1
127.0.0.1:6379[1]> lpush keyList 2
(integer) 2
127.0.0.1:6379[1]> lpush keyList 3
(integer) 3
127.0.0.1:6379[1]> lpush keyList 4
(integer) 4
127.0.0.1:6379[1]> lpush keyList 5
(integer) 5
127.0.0.1:6379[1]> lrange keyList 0 -1
1) "5"
2) "4"
3) "3"
4) "2"
5) "1"


127.0.0.1:6379[1]> ltrim keyList 1 2 # 截断列表数据
OK
127.0.0.1:6379[1]> lrange keyList 0 -1
1) "4"
2) "3"
```

##### 替换值

通过`lset keyList index newValue`将列表中指定下标index的值替换为另外一个值newValue，更新操作

* 如果不存在列表去更新就会报错

```sh
127.0.0.1:6379[1]> lrange keyList 0 -1
1) "3"
2) "2"

127.0.0.1:6379[1]> lset keyList 0 1 # 如果存在，更新当前下标的值
OK

127.0.0.1:6379[1]> lrange keyList 0 -1
1) "1"
2) "2"
127.0.0.1:6379[1]> 
```



##### Redis 列表命令

下表列出了列表相关命令：

| 命令                                                         | 描述                                                     |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| [BLPOP](https://www.redis.com.cn/commands/blpop.html)        | 移出并获取列表的第一个元素                               |
| [BRPOP](https://www.redis.com.cn/commands/brpop.html)        | 移出并获取列表的最后一个元素                             |
| [BRPOPLPUSH](https://www.redis.com.cn/commands/brpoplpush.html) | 从列表中弹出一个值，并将该值插入到另外一个列表中并返回它 |
| [LINDEX](https://www.redis.com.cn/commands/lindex.html)      | 通过索引获取列表中的元素                                 |
| [LINSERT](https://www.redis.com.cn/commands/linsert.html)    | 在列表的元素前或者后插入元素                             |
| [LLEN](https://www.redis.com.cn/commands/llen.html)          | 获取列表长度                                             |
| [LPOP](https://www.redis.com.cn/commands/lpop.html)          | 移出并获取列表的第一个元素                               |
| [LPUSH](https://www.redis.com.cn/commands/lpush.html)        | 将一个或多个值插入到列表头部                             |
| [LPUSHX](https://www.redis.com.cn/commands/lpushx.html)      | 将一个值插入到已存在的列表头部                           |
| [LRANGE](https://www.redis.com.cn/commands/lrange.html)      | 获取列表指定范围内的元素                                 |
| [LREM](https://www.redis.com.cn/commands/lrem.html)          | 移除列表元素                                             |
| [LSET](https://www.redis.com.cn/commands/lset.html)          | 通过索引设置列表元素的值                                 |
| [LTRIM](https://www.redis.com.cn/commands/ltrim.html)        | 对一个列表进行修剪(trim)                                 |
| [RPOP](https://www.redis.com.cn/commands/rpop.html)          | 移除并获取列表最后一个元素                               |
| [RPOPLPUSH](https://www.redis.com.cn/commands/rpoplpush.html) | 移除列表的最后一个元素，并将该元素添加到另一个列表并返回 |
| [RPUSH](https://www.redis.com.cn/commands/rpush.html)        | 在列表中添加一个或多个值                                 |
| [RPUSHX](https://www.redis.com.cn/commands/rpushx.html)      | 为已存在的列表添加值                                     |

#### Hash（哈希）

Redis hash 是一个 string 类型的 key和value 的映射表，hash 特别适合用于**存储对象**。

> hash变更的数据 user name age,尤其是是用户信息之类的，经常变动的信息！ hash 更适合于对象的 存储，String更加适合字符串存储！

##### 设置值

* 通过`hset hashList key value`设置哈希表hashList的key-value

```sh
127.0.0.1:6379[1]> hset hashList name hutao
(integer) 1
```

* 通过`hmset hashList key1 value1 [key2 value2 ...]`设置哈希表hashList 的一个或者多个key-value

```sh
127.0.0.1:6379[1]> hmset hashList a 1 b 2
OK
```

##### 获取值

* 通过`hget hashList key`获取哈希表hashList给定key的值

```sh
127.0.0.1:6379[1]> hget hashList name
"hutao"
```

* 通过`hmget hashList key1 [key2 ...]`获取哈希表hashList 一个或者多个key的value

```sh
127.0.0.1:6379[1]> hmget hashList a b
1) "1"
2) "2"
```

* 通过`hgetall hashList`获取在哈希表hashList 中指定 key 的所有字段和值

```sh
127.0.0.1:6379[1]> hgetall hashList
1) "name"
2) "hutao"
3) "a"
4) "1"
5) "b"
6) "2"
```

* 通过`hkeys hashList`获取在哈希表hashList 中指定 key 的所有字段

* 通过`hvals hashList`获取在哈希表hashList 中指定 key 的所有字段

##### 删除值

通过`hdel hashList key`删除hash指定key字段！对应的value值也就消失了！

```sh
127.0.0.1:6379[1]> hdel hashList name
(integer) 1
127.0.0.1:6379[1]> hgetall hashList
1) "a"
2) "1"
3) "b"
4) "2"
127.0.0.1:6379[1]> 
```

##### 获取字段数量

通过`hlen hashList`获取hash表的字段数量

```sh
127.0.0.1:6379[1]> hlen hashList
(integer) 2
```

##### 逻辑运算

```sh
127.0.0.1:6379> hset myhash field3 5 #指定增量！
(integer) 1
127.0.0.1:6379> HINCRBY myhash field3 1
(integer) 6
127.0.0.1:6379> HINCRBY myhash field3 -1
(integer) 5
127.0.0.1:6379> hsetnx myhash field4 hello # 如果不存在则可以设置
(integer) 1
127.0.0.1:6379> hsetnx myhash field4 world # 如果存在则不能设置
(integer) 0
```

##### Redis哈希命令

| 命令                                                         | 描述                                                      |
| ------------------------------------------------------------ | --------------------------------------------------------- |
| [HDEL](https://www.redis.com.cn/commands/hdel.html)          | 删除一个或多个哈希表字段                                  |
| [HEXISTS](https://www.redis.com.cn/commands/hexists.html)    | 查看哈希表 key 中，指定的字段是否存在                     |
| [HGET](https://www.redis.com.cn/commands/hget.html)          | 获取存储在哈希表中指定字段的值                            |
| [HGETALL](https://www.redis.com.cn/commands/hgetall.html)    | 获取在哈希表中指定 key 的所有字段和值                     |
| [HINCRBY](https://www.redis.com.cn/commands/hincrby.html)    | 为哈希表 key 中的指定字段的整数值加上增量 increment       |
| [HINCRBYFLOAT](https://www.redis.com.cn/commands/hincrbyfloat.html) | 为哈希表 key 中的指定字段的浮点数值加上增量 increment     |
| [HKEYS](https://www.redis.com.cn/commands/hkeys.html)        | 获取所有哈希表中的字段                                    |
| [HLEN](https://www.redis.com.cn/commands/hlen.html)          | 获取哈希表中字段的数量                                    |
| [HMGET](https://www.redis.com.cn/commands/hmget.html)        | 获取所有给定字段的值                                      |
| [HMSET](https://www.redis.com.cn/commands/hmset.html)        | 同时将多个 field-value (域-值)对设置到哈希表 key 中       |
| [HSET](https://www.redis.com.cn/commands/hset.html)          | 将哈希表 key 中的字段 field 的值设为 value                |
| [HSETNX](https://www.redis.com.cn/commands/hsetnx.html)      | 只有在字段 field 不存在时，设置哈希表字段的值             |
| [HVALS](https://www.redis.com.cn/commands/hvals.html)        | 获取哈希表中所有值                                        |
| [HSCAN](https://www.redis.com.cn/commands/hscan.html)        | 迭代哈希表中的键值对                                      |
| [HSTRLEN](https://www.redis.com.cn/commands/hstrlen.html)    | 返回哈希表 key 中， 与给定域 field 相关联的值的字符串长度 |

#### Set（无序集合）

- Redis 的 Set 是 string 类型的无序集合。
- 集合成员是唯一的，这就意味着集合中没有重复的数据。
- 在 Redis 中，添加、删除和查找的时间复杂都是 O(1)（不管 Set 中包含多少元素）。

##### 添加值

通过`sadd setList value`向集合setList添加值

```sh
127.0.0.1:6379[1]> sadd setList hutao
(integer) 1
127.0.0.1:6379[1]> sadd setList yaodao
(integer) 1
127.0.0.1:6379[1]> sadd setList tianhai
(integer) 1
```

##### 获取值

通过`smembers setList`获取集合的所有值

```sh
127.0.0.1:6379[1]> smembers setList
1) "yaodao"
2) "tianhai"
3) "hutao"
```

##### 判断值是否存在

通过`sismember setList value`判断当前的value是否存在于setList集合中

```sh
127.0.0.1:6379[1]> sismember setList hutao
(integer) 1
```

##### 获取元素个数

通过`scard setList`获取set集合中的内容元素个数

```sh
127.0.0.1:6379[1]> scard setList
(integer) 3
```

##### 移除元素

* 通过`srem setList value` 移除setList集合中的指定元素value，如果指定元素不存在，则会返回0

```sh
127.0.0.1:6379[1]> srem setList hutao
(integer) 1
127.0.0.1:6379[1]> srem setList hutao
(integer) 0
```

* 通过`spop setList` 随机移除setList集合中的元素

```sh
127.0.0.1:6379[1]> smembers setList
1) "yaodao"
2) "tianhai"
127.0.0.1:6379[1]> spop setList
"tianhai"
127.0.0.1:6379[1]> smembers setList
1) "yaodao"
```

##### 随机抽选元素

* 通过`srandmember setList`随机抽选一个集合中的元素

```sh
127.0.0.1:6379[1]> srandmember setList
"yaodao"
```

* 过`srandmember setList count`随机抽选count个集合中的元素

```sh
127.0.0.1:6379[1]> srandmember setList 2
1) "yaodao"
2) "tianhai"
```

##### 移动元素

通过`smove setList setList2 value`将一个指定的值，从集合setList移动到另外一个set集合setList2 

```sh
127.0.0.1:6379[1]> flushdb
OK
127.0.0.1:6379[1]> sadd setList 1
(integer) 1
127.0.0.1:6379[1]> sadd setList 2
(integer) 1
127.0.0.1:6379[1]> sadd setList 3
(integer) 1

127.0.0.1:6379[1]> smove setList setList2 2  #将一个指定的值，移动到另外一个set集合！
(integer) 1

127.0.0.1:6379[1]> smembers setList
1) "1"
2) "3"
127.0.0.1:6379[1]> smembers setList2
1) "2"

```

##### 集合范围

```sh
127.0.0.1:6379> SDIFF key1 key2 # 差集
1) "b"
2) "a"

127.0.0.1:6379> SINTER key1 key2 # 交集 共同好友就可以这样实现
1) "c"


127.0.0.1:6379> SUNION key1 key2 # 并集
1) "b"
2) "c"
3) "e"
4) "a"
```

> 微博，A用户将所有关注的人放在一个set集合中！将它的粉丝也放在一个集合中！ 
>
> 共同关注，共同爱好，二度好友，推荐好友！（六度分割理论）

##### Redis 集合命令

下表列出了 Redis 集合相关命令：

| 命令                                                         | 描述                                                |
| ------------------------------------------------------------ | --------------------------------------------------- |
| [SADD](https://www.redis.com.cn/commands/sadd.html)          | 向集合添加一个或多个成员                            |
| [SCARD](https://www.redis.com.cn/commands/scard.html)        | 获取集合的成员数                                    |
| [SDIFF](https://www.redis.com.cn/commands/sdiff.html)        | 返回给定所有集合的差集                              |
| [SDIFFSTORE](https://www.redis.com.cn/commands/sdiffstore.html) | 返回给定所有集合的差集并存储在 destination 中       |
| [SINTER](https://www.redis.com.cn/commands/sinter.html)      | 返回给定所有集合的交集                              |
| [SINTERSTORE](https://www.redis.com.cn/commands/sinterstore.html) | 返回给定所有集合的交集并存储在 destination 中       |
| [SISMEMBER](https://www.redis.com.cn/commands/sismember.html) | 判断 member 元素是否是集合 key 的成员               |
| [SMEMBERS](https://www.redis.com.cn/commands/smembers.html)  | 返回集合中的所有成员                                |
| [SMOVE](https://www.redis.com.cn/commands/smove.html)        | 将 member 元素从 source 集合移动到 destination 集合 |
| [SPOP](https://www.redis.com.cn/commands/spop.html)          | 移除并返回集合中的一个随机元素                      |
| [SRANDMEMBER](https://www.redis.com.cn/commands/srandmember.html) | 返回集合中一个或多个随机数                          |
| [SREM](https://www.redis.com.cn/commands/srem.html)          | 移除集合中一个或多个成员                            |
| [SUNION](https://www.redis.com.cn/commands/sunion.html)      | 返回所有给定集合的并集                              |
| [SUNIONSTORE](https://www.redis.com.cn/commands/sunionstore.html) | 所有给定集合的并集存储在 destination 集合中         |
| [SSCAN](https://www.redis.com.cn/commands/sscan.html)        | 迭代集合中的元素                                    |

#### Zset（有序集合）

- Redis 有序集合和集合一样也是 string 类型元素的集合，且不允许重复的成员。
- 不同的是每个元素都会关联一个 double 类型的分数。Redis 正是通过分数来为集合中的成员进行从小到大的排序。
- 有序集合的成员是唯一的,但分数 ( score ) 却可以重复。
- 集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。

**在set的基础上，增加了一个值**

- **set k1 v1 ****

- **zset k1 score1 v1**

>案例思路：set 排序 存储班级成绩表，工资表排序！
>
>普通消息，1； 重要消息 2，带权重进行判断！
>
>排行榜应用实现，取Top N 测试！

##### 添加/获取值

```sh
127.0.0.1:6379[1]> flushdb
OK
127.0.0.1:6379[1]> zadd zsetList 1 one # 添加一个值
(integer) 1
127.0.0.1:6379[1]> zadd zsetList 2 two 3 three # 添加多个值
(integer) 2
127.0.0.1:6379[1]> zrange zsetList 0 -1 # 获取搜索值
1) "one"
2) "two"
3) "three"
```

##### 排序

```sh
127.0.0.1:6379[1]> zadd salary 2500 xiaohong # 添加三个用户
(integer) 1
127.0.0.1:6379[1]>  zadd salary 5000 zhangsan
(integer) 1
127.0.0.1:6379[1]> zadd salary 500 kaungshen
(integer) 1

127.0.0.1:6379[1]> zrangebyscore salary -inf +inf # 显示全部的用户 从小到大！
1) "kaungshen"
2) "xiaohong"
3) "zhangsan"

127.0.0.1:6379[1]> zrevrange salary 0 -1 # 从大到进行排序！
1) "zhangsan"
2) "xiaohong"
3) "kaungshen"

127.0.0.1:6379[1]> zrangebyscore salary -inf +inf withscores # 显示全部的用户并且附带成绩
1) "kaungshen"
2) "500"
3) "xiaohong"
4) "2500"
5) "zhangsan"
6) "5000"


127.0.0.1:6379[1]> zrangebyscore salary -inf 2500 withscores  # 显示工资小于2500员工的升序排序！
1) "kaungshen"
2) "500"
3) "xiaohong"
4) "2500"
```

##### 移除元素

```sh
127.0.0.1:6379> zrange salary 0 -1
1) "kaungshen"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> zrem salary xiaohong # 移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "kaungshen"
2) "zhangsan"
127.0.0.1:6379> zcard salary # 获取有序集合中的个数
(integer) 2
```

##### 计算在有序集合中指定区间分数的成员数

```sh
127.0.0.1:6379> zadd myset 1 hello
(integer) 1
127.0.0.1:6379> zadd myset 2 world 3 kuangshen
(integer) 2
127.0.0.1:6379> zcount myset 1 3 # 获取指定区间的成员数量！
(integer) 3
127.0.0.1:6379> zcount myset 1 2
(integer) 2
```

##### Redis 有序集合命令

下表列出了 Redis 有序集合的基本命令

| 命令                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [ZADD](https://www.redis.com.cn/commands/zadd.html)          | 向有序集合添加一个或多个成员，或者更新已存在成员的分数       |
| [ZCARD](https://www.redis.com.cn/commands/zcard.html)        | 获取有序集合的成员数                                         |
| [ZCOUNT](https://www.redis.com.cn/commands/zcount.html)      | 计算在有序集合中指定区间分数的成员数                         |
| [ZINCRBY](https://www.redis.com.cn/commands/zincrby.html)    | 有序集合中对指定成员的分数加上增量 increment                 |
| [ZINTERSTORE](https://www.redis.com.cn/commands/zinterstore.html) | 计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中 |
| [ZLEXCOUNT](https://www.redis.com.cn/commands/zlexcount.html) | 在有序集合中计算指定字典区间内成员数量                       |
| [ZRANGE](https://www.redis.com.cn/commands/zrange.html)      | 通过索引区间返回有序集合成指定区间内的成员                   |
| [ZRANGEBYLEX](https://www.redis.com.cn/commands/zrangebylex.html) | 通过字典区间返回有序集合的成员                               |
| [ZRANGEBYSCORE](https://www.redis.com.cn/commands/zrangebyscore.html) | 通过分数返回有序集合指定区间内的成员                         |
| [ZRANK](https://www.redis.com.cn/commands/zrank.html)        | 返回有序集合中指定成员的索引                                 |
| [ZREM](https://www.redis.com.cn/commands/zrem.html)          | 移除有序集合中的一个或多个成员                               |
| [ZREMRANGEBYLEX](https://www.redis.com.cn/commands/zremrangebylex.html) | 移除有序集合中给定的字典区间的所有成员                       |
| [ZREMRANGEBYRANK](https://www.redis.com.cn/commands/zremrangebyrank.html) | 移除有序集合中给定的排名区间的所有成员                       |
| [ZREMRANGEBYSCORE](https://www.redis.com.cn/commands/zremrangebyscore.html) | 移除有序集合中给定的分数区间的所有成员                       |
| [ZREVRANGE](https://www.redis.com.cn/commands/zrevrange.html) | 返回有序集中指定区间内的成员，通过索引，分数从高到底         |
| [ZREVRANGEBYSCORE](https://www.redis.com.cn/commands/zrevrangebyscore.html) | 返回有序集中指定分数区间内的成员，分数从高到低排序           |
| [ZREVRANK](https://www.redis.com.cn/commands/zrevrank.html)  | 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序 |
| [ZSCORE](https://www.redis.com.cn/commands/zscore.html)      | 返回有序集中，成员的分数值                                   |
| [ZUNIONSTORE](https://www.redis.com.cn/commands/zunionstore.html) | 计算一个或多个有序集的并集，并存储在新的 key 中              |
| [ZSCAN](https://www.redis.com.cn/commands/zscan.html)        | 迭代有序集合中的元素（包括元素成员和元素分值）               |

### 三种特殊数据类型

#### Geospatial 地理位置

Redis GEO 主要用于存储地理位置信息，并对存储的信息进行操作，该功能在 Redis 3.2 版本新增。

Redis GEO 操作方法有：

- geoadd：添加地理位置的坐标。
- geopos：获取地理位置的坐标。
- geodist：计算两个位置之间的距离。
- georadius：根据用户给定的经纬度坐标来获取指定范围内的地理位置集合。
- georadiusbymember：根据储存在位置集合里面的某个地点获取指定范围内的地理位置集合。
- geohash：返回一个或多个位置对象的 geohash 值。

测试数据：http://www.jsons.cn/lngcodeinfo/0706D99C19A781A3/

> **GEO 底层的实现原理其实就是 Zset！可以使用Zset命令来操作geo！**

##### geoadd添加地理位置

语法格式：

```sh
geoadd key longitude latitude member [longitude latitude member ...]
```

* key：存储的key名称，比如为(china:city)
* longitude：经度
* latitude：维度
* member：指定地址名称

规则：两级无法直接添加，我们一般会下载城市数据，直接通过java程序一次性导入！ 

- 有效的经度从-180度到180度。 
- 有效的纬度从-85.05112878度到85.05112878度。 

* 当坐标位置超出上述指定范围时，该命令将会返回一个错误

```sh
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> geoadd china:city 121.472644 31.231706 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 116.405285 39.904989 beijing
(integer) 1
```

##### geopos返回地址信息

语法格式：

```
geopos key member [member ...]
```

geopos 用于从给定的 key 里返回所有指定名称(member)的位置（经度和纬度），不存在的返回 nil。

```sh
127.0.0.1:6379> geopos china:city shanghai
1) 1) "121.47264629602432251"
   2) "31.23170490709807012"
```

##### geodist位置距离

语法格式：

```
geodist key member1 member2 [m|km|ft|mi]
```

member1和member2 为两个地理位置。最后一个距离单位参数说明：

- m ：米，默认单位。
- km ：千米。
- mi ：英里。
- ft ：英尺。

geodist 用于返回两个给定位置之间的距离。

```sh
127.0.0.1:6379> geodist china:city shanghai beijing km
"1067.5980"
127.0.0.1:6379> geodist china:city shanghai beijing ft
"3502618.0013"
```

##### georadius寻找附近所有位置元素

georadius 以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。

语法格式：

```
georadius key longitude latitude radius m|km|ft|mi [withcoord] [withdist] [withhash] [COUNT count] [asc|desc] [store key] [storedist key]
```

参数说明：

- m ：米，默认单位。
- km ：千米。
- mi ：英里。
- ft ：英尺。
- withdist: 在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回。
- withcoord: 将位置元素的经度和维度也一并返回。
- withhash: 以 52 位有符号整数的形式， 返回位置元素经过原始 geohash 编码的有序集合分值。 这个选项主要用于底层应用或者调试， 实际中的作用并不大。
- COUNT限定返回的记录数。
- asc: 查找结果根据距离从近到远排序。
- desc: 查找结果根据从远到近排序。

> 附近的人？ （获得所有附近的人的地址，定位！）通过半径来查询！ 获得指定数量的人，200

```sh
127.0.0.1:6379> georadius china:city 110 30 1000 km # 以110，30 这个经纬度为中心，寻找方圆1000km内的城市
1) "chongqi"
2) "xian"
3) "shengzhen"
4) "hangzhou"
127.0.0.1:6379> georadius china:city 110 30 500 km
1) "chongqi"
2) "xian"

127.0.0.1:6379> georadius china:city 110 30 500 km withdist # 显示到中间距离的位置
1) 1) "chongqi"
2) "341.9374"
2) 1) "xian"
2) "483.8340"

127.0.0.1:6379> georadius china:city 110 30 500 km withcoord # 显示他人的定位信息
1) 1) "chongqi"
2) 1) "106.49999767541885376"
2) "29.52999957900659211"
2) 1) "xian"
2) 1) "108.96000176668167114"
2) "34.25999964418929977"

127.0.0.1:6379> georadius china:city 110 30 500 km withdist withcoord count 1 #筛选出指定的结果！
1) 1) "chongqi"
2) "341.9374"
3) 1) "106.49999767541885376"
2) "29.52999957900659211"
127.0.0.1:6379> georadius china:city 110 30 500 km withdist withcoord count 2
1) 1) "chongqi"
2) "341.9374"
3) 1) "106.49999767541885376"
2) "29.52999957900659211"
2) 1) "xian"
2) "483.8340"
3) 1) "108.96000176668167114"
2) "34.25999964418929977"
```

##### georadiusbymember

georadiusbymember 和 georadius命令一样， 都可以找出位于指定范围内的元素， 但是 georadiusbymember 的中心点是由给定的位置元素决定的， 而不是使用经度和纬度来决定中心点。

语法格式：

```sh
georadiusbymember key member radius m|km|ft|mi [withcoord] [withdist] [withhash] [COUNT count] [asc|desc] [store key] [storedist key]
```

```sh
# 找出位于指定元素周围的其他元素！
127.0.0.1:6379> georadiusbymember china:city beijing 1000 km
1) "beijing"
2) "xian"
127.0.0.1:6379> georadiusbymember china:city shanghai 400 km
1) "hangzhou"
2) "shanghai"
```

##### geohash返回位置元素的 Geohash 表示

geohash命令 - 返回一个或多个位置元素的 Geohash 表示

```sh
# 将二维的经纬度转换为一维的字符串，如果两个字符串越接近，那么则距离越近！
127.0.0.1:6379> geohash china:city beijing chongqi
1) "wx4fbxxfke0"
2) "wm5xzrybty0"
```

#### Hyperloglog基数统计

##### 概念

Redis HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定 的、并且是很小的。

在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 264 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

##### 什么是基数

比如数据集 {1, 3, 5, 7, 5, 7, 8}， 那么这个数据集的基数集为 {1, 3, 5 ,7, 8}, **基数(不重复元素个数)**为5。 基数估计就是在误差可接受的范围内，快速计算基数。

##### 用途

**网页的 UV （一个人访问一个网站多次，但是还是算作一个人！）** 

传统的方式， set 保存用户的id，然后就可以统计 set 中的元素数量作为标准判断 ! 

这个方式如果保存大量的用户id，就会比较麻烦！我们的目的是为了计数，而不是保存用户id； 0.81% 错误率！ 统计UV任务，可以忽略不计的！

##### Redis HyperLogLog命令

下表列出了列表相关命令：

| 命令                                                      | 描述                                      |
| --------------------------------------------------------- | ----------------------------------------- |
| [PFADD](https://www.redis.com.cn/commands/pfadd.html)     | 添加指定元素到 HyperLogLog 中。           |
| [PFCOUNT](https://www.redis.com.cn/commands/pfcount.html) | 返回给定 HyperLogLog 的基数估算值。       |
| [PFMERGE](https://www.redis.com.cn/commands/pfmerge.html) | 将多个 HyperLogLog 合并为一个 HyperLogLog |

```sh
127.0.0.1:6379> pfadd mykey a b c d e f g h # 创建第一组元素 mykey
(integer) 1
127.0.0.1:6379> pfcount mykey 			    # 统计 mykey 元素的基数数量
(integer) 8
127.0.0.1:6379> pfadd mykey2 a i s p f z    # 创建第二组元素 mykey2
(integer) 1
127.0.0.1:6379> pfcount mykey2
(integer) 6
127.0.0.1:6379> pfmerge mykey3 mykey mykey2 # 合并两组 mykey mykey2 => mykey3 并集
OK
127.0.0.1:6379> pfcount mykey3              # 看并集的数量！
(integer) 12
127.0.0.1:6379> 
```

>- 如果允许容错，那么一定可以使用 Hyperloglog 
>- 如果不允许容错，就使用 set 或者自己的数据类型即可

#### Bitmap位图

统计用户信息，活跃，不活跃！ 登录 、 未登录！ 打卡，365打卡！ 两个状态的，都可以使用 Bitmaps！ 

Bitmap 位图，数据结构！ 都是操作二进制位来进行记录，就只有0 和 1 两个状态！ 

365 天 = 365 bit 1字节 = 8bit 46 个字节左右！

```
一共365个bit
0 0 0 0 0 1 0 1 ...
其中在第几天，就在第几位标记为1
```

* 使用bitmap 来记录 周一到周日的打卡！ 

周一：1 周二：0 周三：0 周四：1 ......

```sh
127.0.0.1:6379> setbit sign 0 1
(integer) 0
127.0.0.1:6379> setbit sign 1 0
(integer) 0
127.0.0.1:6379> setbit sign 2 0
(integer) 0
127.0.0.1:6379> setbit sign 3 1
(integer) 0
127.0.0.1:6379> setbit sign 4 1
(integer) 0
127.0.0.1:6379> setbit sign 5 0
(integer) 0
127.0.0.1:6379> setbit sign 6 0
(integer) 0
```

* 查看某一天是否有打卡！

```sh
127.0.0.1:6379> getbit sign 3
(integer) 1
127.0.0.1:6379> getbit sign 6
(integer) 0
```

* 统计操作，统计 打卡的天数！

```sh
127.0.0.1:6379> bitcount sign # 统计这周的打卡记录，就可以看到是否有全勤！
(integer) 3
```

## Redis事务操作

### 什么是事务

事务是指**一个完整的动作，要么全部执行，要么什么也没有做**。

Redis 事务不是严格意义上的事务，只是用于帮助用户在一个步骤中执行多个命令。

**单个 Redis 命令的执行是原子性的，但 Redis 没有在事务上增加任何维持原子性的机制，所以 Redis 事务的执行并不是原子性的。**

Redis 事务本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行过程的中，会按照顺序执行！

一次性、顺序性、排他性！执行一些列的命令！

**Redis事务没有没有隔离级别的概念！** 

所有的命令在事务中，并没有直接被执行！只有发起执行命令的时候才会执行！Exec

一个redis事务从开始到执行会经历以下三个阶段：

- 开始事务（multi）。
- 命令入队。
- 执行事务（exec）。

### 正常执行事务

* 开启事务

```sh
127.0.0.1:6379> multi
OK
```

* 命令入队

```sh
127.0.0.1:6379> set key1 val1                                                           
QUEUED
127.0.0.1:6379> set key2 val2                                                           
QUEUED
127.0.0.1:6379> get key2                                                                
QUEUED
```

* 执行事务

```sh
127.0.0.1:6379> exec                                                                    
1) "OK"
2) "OK"
3) "val2"
```

### 放弃事务

* 开启事务

```sh
127.0.0.1:6379> multi
OK
```

* 命令入队

```sh
127.0.0.1:6379> set key1 val1                                                          
QUEUED
127.0.0.1:6379> set key2 val2                                                          
QUEUED
127.0.0.1:6379> set key3 val3                                                          
QUEUED
```

* 取消事务

```sh
127.0.0.1:6379> discard                                                                
OK
```

* 测试，事务队列中命令都不会被执行

```sh
127.0.0.1:6379> get key3
(nil)
127.0.0.1:6379>
```

### 编译型异常

编译型异常（代码有问题！ 命令有错！） ，事务中所有的命令都不会被执行！

```sh
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set key1 val1                                                          
QUEUED

# 错误的命令    
127.0.0.1:6379> getset key1                                                            
(error) ERROR wrong number of arguments for 'getset' command

# 执行事务报错！
127.0.0.1:6379> exec                                                                   
(error) ERROR Transaction discarded because of previous errors.

# 所有的命令都不会被执行！
127.0.0.1:6379> get key1
(nil)
```

### 语法性错误

运行时异常（1/0）， 如果事务队列中存在语法性，那么执行命令的时候，其他命令是可以正常执行 的，错误命令抛出异常！

```sh
127.0.0.1:6379> set k1 "v1"
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr k1 # 会执行的时候失败！
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> get k3
QUEUED
127.0.0.1:6379> exec
1) (error) ERR value is not an integer or out of range # 虽然第一条命令报错了，但是依旧正常执行成功了！
2) OK
3) OK
4) "v3"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k3
"v3"
```

### 监控！ Watch （面试常问！）

#### 悲观锁

* 很悲观，认为什么时候都会出问题，无论做什么都会加锁！

##### 乐观锁

- 很乐观，认为什么时候都不会出问题，所以不会上锁！更新数据的时候去判断一下，在此期间是否有人修改过这个数据

- 获取version

- 更新的时候比较 version

> Redis的watch本身就是一个乐观锁

#### Redis监控

* 正常运行

```sh
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money  # 监视 money 对象
OK
127.0.0.1:6379> multi        # 事务正常结束，数据期间没有发生变动，这个时候就正常执行成功！
OK
127.0.0.1:6379> decrby money 20                                                         
QUEUED
127.0.0.1:6379> incrby out 20                                                           
QUEUED
127.0.0.1:6379> exec                                                                    
1) "80"
2) "20"
```

* 测试多线程修改值 , 使用watch 可以当做redis的乐观锁操作！

1. 线程1

```sh
127.0.0.1:6379> watch money
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10                                                           
QUEUED
127.0.0.1:6379> incrby out 10                                                             
QUEUED
127.0.0.1:6379>
```

2. 没有执行`exec`，现在有一个线程2突然插进来

```sh
127.0.0.1:6379> get money
"80"
127.0.0.1:6379> set money 1000
OK
```

3. 切换回线程1，继续执行

```sh
127.0.0.1:6379> watch money
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10                                                          
QUEUED
127.0.0.1:6379> incrby out 10                                                            
QUEUED

# 执行之前，另外一个线程，修改了我们的值，这个时候，就会导致事务执行失败！
127.0.0.1:6379> exec                                                                     
(nil)
127.0.0.1:6379>
```

* 如果修改失败，获取最新的值就好

```sh
127.0.0.1:6379> unwatch # 如果事务执行失败，就先进行解锁
OK
127.0.0.1:6379> watch money # 获取最新的值，然后再进行监视，select version
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> decrby money 10                                                          
QUEUED
127.0.0.1:6379> incrby out 10                                                            
QUEUED
127.0.0.1:6379> exec  # 比对监视的值是否发生了变化，如果没有变化，那么可以执行成功，如果变化了就执行失败     
1) "990"
2) "30"
127.0.0.1:6379>
```

## Redis配置

### 读取文件配置

![image-20220210113227109](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251987.png)

```sh
redis-server redis-config/redis.conf
```

![image-20220210113438987](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251988.png)

### 单位

![image-20220210113451313](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251989.png)

* 配置文件 unit单位 对大小写不敏感！

### 包含配置文件

![image-20220210113727541](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251990.png)

```
如果你想用include来覆盖配置  
options，最好使用include作为最后一行。  
```

### 网络

```properties
bind 127.0.0.1 # 绑定的ip
protected-mode yes # 开启保护模式
port 6379 # 端口设置
```

### 通用GENERAL

```
daemonize yes # 以守护进程的方式运行，默认是 no，我们需要自己开启为yes！

pidfile /var/run/redis_6379.pid # 如果以后台的方式运行，我们就需要指定一个 pid 文件！
```

![image-20220210114528540](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251991.png)

![image-20220210114613406](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251992.png)

```properties
# 日志
# Specify the server verbosity level.
# This can be one of:
# debug (a lot of information, useful for development/testing)
# verbose (many rarely useful info, but not a mess like the debug level)
# notice (moderately verbose, what you want in production probably) 生产环境
# warning (only very important / critical messages are logged)
loglevel notice
logfile /usr/local/bin/redis-log/redis_log.log # 日志的文件位置名
databases 16 # 数据库的数量，默认是 16 个数据库
always-show-logo yes # 是否总是显示LOGO
```

![image-20220210114817241](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251993.png)

### 快照

持久化， 在规定的时间内，执行了多少次操作，则会持久化到文件 .rdb. aof

redis 是内存数据库，如果没有持久化，那么数据断电及失！

```properties
# 如果900s内，如果至少有一个1 key进行了修改，我们及进行持久化操作
save 900 1
# 如果300s内，如果至少10 key进行了修改，我们及进行持久化操作
save 300 10
# 如果60s内，如果至少10000 key进行了修改，我们及进行持久化操作
save 60 10000
# 我们之后学习持久化，会自己定义这个测试！

stop-writes-on-bgsave-error yes # 持久化如果出错，是否还需要继续工作！

rdbcompression yes # 是否压缩 rdb 文件，需要消耗一些cpu资源！

rdbchecksum yes # 保存rdb文件的时候，进行错误的检查校验！

dir /usr/local/bin/redis-dbfile/ # rdb 文件保存的目录！
```

### REPLICATION 复制

> 后面

### SECURITY 安全

可以在这里设置redis的密码，默认是没有密码！

```sh
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> config get requirepass # 获取redis的密码
1) "requirepass"
2) ""

127.0.0.1:6379> config set requirepass "123456" # 设置redis的密码
OK

127.0.0.1:6379> config get requirepass # 发现所有的命令都没有权限了
(error) NOAUTH Authentication required.
127.0.0.1:6379> ping
(error) NOAUTH Authentication required.


127.0.0.1:6379> auth 123456 # 使用密码进行登录！
OK
127.0.0.1:6379> config get requirepass
1) "requirepass"
2) "123456"
```

### 限制 CLIENTS

```properties
maxclients 10000 # 设置能连接上redis的最大客户端的数量

maxmemory <bytes> # redis 配置最大的内存容量

maxmemory-policy noeviction # 内存到达上限之后的处理策略
    1、volatile-lru：只对设置了过期时间的key进行LRU（默认值）
    2、allkeys-lru ： 删除lru算法的key
    3、volatile-random：随机删除即将过期key
    4、allkeys-random：随机删除
    5、volatile-ttl ： 删除即将过期的
    6、noeviction ： 永不过期，返回错误
```

### APPEND ONLY 模式 aof配置

```properties
# 默认是不开启aof模式的，默认是使用rdb方式持久化的，在大部分所有的情况下，rdb完全够用！
appendonly no

# 持久化的文件的名字
appendfilename "appendonly.aof"

# 每次修改都会 sync。消耗性能
# appendfsync always

# 每秒执行一次 sync，可能会丢失这1s的数据！
appendfsync everysec

# 不执行 sync，这个时候操作系统自己同步数据，速度最快！
# appendfsync no 
```

## Redis持久化

Redis 是内存数据库，如果不将内存中的数据库状态保存到磁盘，那么一旦服务器进程退出，服务器中的数据库状态也会消失。所以 Redis 提供了持久化功能！

### 什么是持久化

持久化就是把内存的数据写到磁盘中去，防止服务宕机了内存数据丢失。

### RDB（Redis DataBase）

#### 什么是RDB（Redis DataBase）



![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251994.png)

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，它恢复时是将快 照文件直接读到内存里。

AOF（Append OnRedis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程 都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的。 这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那 RDB方式要比AOF方式更加的高效。

RDB的缺点是最后一次持久化后的数据可能丢失。我们默认的就是 RDB，一般情况下不需要修改这个配置！ly File）

**rdb保存的文件是dump.rdb 都是在我们的配置文件中快照中进行配置的！**

![image-20220210222050768](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251995.png)

#### 触发机制

1. save的规则满足的情况下，会自动触发rdb规则 

![image-20220210223240112](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251996.png)

2. 执行 flushall 命令，也会触发我们的rdb规则！ 

3. 退出redis，也会产生 rdb 文件！

备份就自动生成一个 dump.rdb

![image-20220210223031150](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251997.png)

#### 如何恢复rdb文件

1. 只需要将rdb文件放在我们redis启动目录或者自定义存放路径就可以，redis启动的时候会自动检查dump.rdb 恢复其中 的数据！

![image-20220210222050768](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251995.png)

2. 查看需要存在的位置

![image-20220210223417731](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251998.png)

#### 优缺点

**优点**： 

- 适合大规模的数据恢复！ 
- 对数据的完整性要不高！ 最后一次持久化后的数据可能丢失。

这种文件非常适合用于进行备份： 比如说，你可以在最近的 24 小时内，每小时备份一次 RDB 文件，并且在每个月的每一天，也备份一个 RDB 文件。 这样的话，即使遇上问题，也可以随时将数据集还原到不同的版本。**RDB 非常适用于灾难恢复**（disaster recovery）。

**缺点**： 

- 需要一定的时间间隔进程操作！如果redis意外宕机了，这个最后一次修改数据就没有的了！ 
- **fork进程的时候，会阻塞主进程！**RDB每次fork出子进程来执行RDB快照生成文件时，如果文件特别大，可能会导致客户端提供服务暂停数毫秒或者几秒 

**如果你需要尽量避免在服务器故障时丢失数据**，**那么 RDB 不适合你**。 虽然 Redis 允许你设置不同的保存点（save point）来控制保存 RDB 文件的频率， 但是， 因为RDB 文件需要保存整个数据集的状态， 所以它并不是一个轻松的操作。 因此你可能会至少 5 分钟才保存一次 RDB 文件。 在这种情况下， **一旦发生故障停机， 你就可能会丢失好几分钟的数据**。

### AOF（Append Only File）

#### 什么是AOF（Append Only File）？

AOF，英文是 Append Only File，即只允许追加不允许改写的文件。

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251999.png)

AOF 方式是将执行过的写指令记录下来，在数据恢复时按照从前到后的顺序再将指令都执行一遍，就这么简单。

> 以日志的形式来记录每个写操作，将Redis执行过的所有指令记录下来（读操作不记录），只许追加文件 但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件 的内容将写指令从前到后执行一次以完成数据的恢复工作

**Aof保存的是 appendonly.aof 文件**

#### append

![image-20220210224949951](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251000.png)



Redis订阅默认是不开启的，我们需要手动进行配置！我们只需要将 appendonly 改为yes就开启了 aof

一定要重启服务器，redis 就可以生效了！ 

```sh
ubuntu@VM-16-6-ubuntu:/usr/local/bin$ redis-cli
127.0.0.1:6379> shutdown
not connected> exit
ubuntu@VM-16-6-ubuntu:/usr/local/bin$ redis-server redis-config/redis.conf

ubuntu@VM-16-6-ubuntu:/usr/local/bin$ redis-cli
127.0.0.1:6379> set name hutao
OK
127.0.0.1:6379> get name
"hutao"
127.0.0.1:6379> exit
ubuntu@VM-16-6-ubuntu:/usr/local/bin$ cd redis-dbfile
ubuntu@VM-16-6-ubuntu:/usr/local/bin/redis-dbfile$ ls
appendonly.aof  dump.rdb
```

![image-20220210230125797](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251001.png)

**需要注意的是：即使已经在redis.conf文件中把appendonly 从no改为了yes,把服务器重启了的情况也没有appendonly.aof文件时，必须要执行**

```sh
redis-cli config set appendonly yes
redis-cli config set save “”
```

如果这个 aof 文件有错位，这时候 redis 是启动不起来的，我们需要修复这个aof文件

redis 给我们提供了一个工具 `redis-check-aof --fix`

```sh
redis-check-aof --fix redis-dbfile/appendonly.aof
```

如果文件正常，重启就可以直接恢复了！

#### 重写规则说明

aof默认就是文件的无限追加，文件会越来越大！

![image-20220210230426995](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251002.png)

如果 aof 文件大于 64m，太大了！ fork一个新的进程来将我们的文件进行重写！

> 会丢弃错误的数据

#### 优缺点

**优点**： 

1. 每一次修改都同步，文件的完整会更加好！
2. 每秒同步一次，可能会丢失一秒的数据
3. 从不同步，效率最高的！

**缺点**： 

1. 相对于数据文件来说，aof远远大于rdb，修复的速度也比rdb慢
2. Aof运行效率也要比rdb 慢，所以我们redis默认的配置就是rdb持久化

### 使用建议

1. RDB 持久化方式能够在指定的时间间隔内对你的数据进行快照存储
2. AOF 持久化方式记录每次对服务器写的操作，当服务器重启的时候会重新执行这些命令来恢复原始 的数据，AOF命令以Redis 协议追加保存每次写的操作到文件末尾，Redis还能对AOF文件进行后台重 写，使得AOF文件的体积不至于过大。
3. **只做缓存，如果你只希望你的数据在服务器运行的时候存在，你也可以不使用任何持久化**
4. 同时开启两种持久化方式
   - 在这种情况下，当redis重启的时候会优先载入AOF文件来恢复原始的数据，因为在通常情况下AOF 文件保存的数据集要比RDB文件保存的数据集要完整。
   - RDB 的数据不实时，同时使用两者时服务器重启也只会找AOF文件，那要不要只使用AOF呢？建议不要，因为RDB更适合用于备份数据库（AOF在不断变化不好备份），快速重启，而且不会有 AOF可能潜在的Bug，留着作为一个万一的手段。
5. 性能建议
   - 因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够 了，只保留`save 900 1`这条规则。
   - 如果Enable AOF ，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自 己的AOF文件就可以了，代价一是带来了持续的IO，二是AOF rewrite 的最后将 rewrite 过程中产 生的新数据写到新文件造成的阻塞几乎是不可避免的。只要硬盘许可，应该尽量减少AOF rewrite 的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上，默认超过原大小100%大小重 写可以改到适当的数值。
   - 如果不Enable AOF，仅靠 Master-Slave Repllcation 实现高可用性也可以，能省掉一大笔IO，也 减少了rewrite时带来的系统波动。代价是如果Master/Slave同时倒掉，会丢失十几分钟的数据， 启动脚本也要比较两个 Master/Slave 中的 RDB文件，载入较新的那个，微博就是这种架构。

## Redis发布订阅

### 概念

Redis 发布/订阅阅(pub/sub)是一种消息传模式，其中发送者(pub)发送消息，而接收者（订阅者）(sub)接收消息。传递消息的通道称为**channel**。比如微信、 微博、关注系统！

Redis 客户端可以订阅任意数量的频道。 

订阅/发布消息图：

第一个：消息发送者， 第二个：频道 第三个：消息订阅者！

![image-20220210234955941](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251003.png)

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的 关系：

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251004.png)

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户 端：

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251005.png)



### Redis发布订阅命令

下表列出了列表相关命令：

| 命令                                                         | 描述                               |
| ------------------------------------------------------------ | ---------------------------------- |
| [PSUBSCRIBE](https://www.redis.com.cn/commands/psubscribe.html) | 订阅一个或多个符合给定模式的频道。 |
| [PUBSUB](https://www.redis.com.cn/commands/pubsub.html)      | 查看订阅与发布系统状态。           |
| [PUBLISH](https://www.redis.com.cn/commands/publish.html)    | 将信息发送到指定的频道。           |
| [PUNSUBSCRIBE](https://www.redis.com.cn/commands/punsubscribe.html) | 退订所有给定模式的频道。           |
| [SUBSCRIBE](https://www.redis.com.cn/commands/subscribe.html) | 订阅给定的一个或多个频道的信息。   |
| [UNSUBSCRIBE](https://www.redis.com.cn/commands/unsubscribe.html) | 指退订给定的频道。                 |

这些命令被广泛用于构建即时通信应用，比如网络聊天室(chatroom)和实时广播、实时提醒等。

### 简单测试

打开两个线程便于测试

![image-20220211002618676](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251006.png)

##### 订阅

```sh
127.0.0.1:6379> subscribe testChannel # 订阅频道
subscribe from testChannel: 1  # 等待推送的信息
```

![image-20220211002804327](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251007.png)

在等待发送消息，等待发送端发送消息

![image-20220211003007921](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251008.png)

#### 发送

```sh
127.0.0.1:6379> publish testChannel hutao # 发布者发布信息到频道
(integer) 1
```

当发送端发送完消息后，订阅段会自动接收到信息。

### 原理

Redis是使用C实现的，通过分析 Redis 源码里的 pubsub.c 文件，了解发布和订阅机制的底层实现，籍 此加深对 Redis 的理解。

Redis 通过 PUBLISH 、SUBSCRIBE 和 PSUBSCRIBE 等命令实现发布和订阅功能。

#### 应用

**微信：** 通过 SUBSCRIBE 命令订阅某频道后，redis-server 里维护了一个字典，字典的键就是一个个 频道！， 而字典的值则是一个链表，链表中保存了所有订阅这个 channel 的客户端。SUBSCRIBE 命令的关键， 就是将客户端添加到给定 channel 的订阅链表中。

![image-20220211003440511](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251009.png)

通过 PUBLISH 命令向订阅者发送消息，redis-server 会使用给定的频道作为键，在它所维护的 channel 字典中查找记录了订阅这个频道的所有客户端的链表，遍历这个链表，将消息发布给所有订阅者。

Pub/Sub 从字面上理解就是发布（Publish）与订阅（Subscribe），在Redis中，你可以设定对某一个 key值进行消息发布及消息订阅，当一个key值上进行了消息发布后，所有订阅它的客户端都会收到相应 的消息。这一功能最明显的用法就是用作实时消息系统，比如普通的即时聊天，群聊等功能。

使用场景： 

1. 实时消息系统！ 
2. 事实聊天！（频道当做聊天室，将信息回显给所有人即可！） 
3. 订阅，关注系统都是可以的！ 

稍微复杂的场景我们就会使用 消息中间件MQ

## Redis集群

### 环境配置

* 查看当前库信息

```sh
127.0.0.1:6379> info replication # 查看当前库的信息
# Replication
role:master  # 角色 master
connected_slaves:0 # 没有从机
master_replid:35aa4fc57579e4a201d546229fbab6630af0b12f
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

* 复制3个配置文件，然后修改对应的信息

```sh
sudo cp redis.conf redis79.conf
sudo cp redis.conf redis80.conf
sudo cp redis.conf redis81.conf

sudo vim redis79.conf
sudo vim redis80.conf
sudo vim redis81.conf
```

**端口**

![image-20220211014735114](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251010.png)

**pid名字**

![image-20220211014636390](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251011.png)

**log文件名字**

```sh
logfile /usr/local/bin/redis-log/redis-log-6379.log
```

![image-20220211014558801](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251012.png)

**dump.rdb名字**

![image-20220211014812817](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251013.png)

* 启动服务器

```sh
# 先给配置日志文件给予权限
cd redis-log

sudo vim redis-log-6379.log
sudo vim redis-log-6380.log
sudo vim redis-log-6381.log

cd ..

sudo chmod 777 redis-config/redis79.conf 
sudo chmod 777 redis-config/redis80.conf 
sudo chmod 777 redis-config/redis81.conf 
sudo chmod 777 redis-log/redis-log-6379.log
sudo chmod 777 redis-log/redis-log-6380.log
sudo chmod 777 redis-log/redis-log-6381.log


redis-server redis-config/redis79.conf
redis-server redis-config/redis80.conf
redis-server redis-config/redis81.conf
```

* 修改完毕之后，启动我们的3个redis服务器，可以通过进程信息查看！

![image-20220211015920375](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251014.png)

### 主从模式

#### 概念

主从复制，是指将一台Redis服务器的数据，复制到其他的Redis服务器。前者称为主节点 (master/leader)，后者称为从节点(slave/follower)；**数据的复制是单向的，只能由主节点到从节点。** Master以写为主，Slave 以读为主。

> 主从复制，读写分离

**默认情况下，每台Redis服务器都是主节点；**且一个主节点可以有多个从节点(或没有从节点)，但一个从节点只能有一个主节点。

**主从复制的作用主要包括**： 

1. 数据冗余：主从复制实现了数据的热备份，是持久化之外的一种数据冗余方式。 
2. 故障恢复：当主节点出现问题时，可以由从节点提供服务，实现快速的故障恢复；实际上是一种服务 的冗余。 
3. 负载均衡：在主从复制的基础上，配合读写分离，可以由主节点提供写服务，由从节点提供读服务 （即写Redis数据时应用连接主节点，读Redis数据时应用连接从节点），分担服务器负载；尤其是在写 少读多的场景下，通过多个从节点分担读负载，可以大大提高Redis服务器的并发量。 
4. 高可用（集群）基石：除了上述作用以外，主从复制还是哨兵和集群能够实施的基础，因此说主从复 制是Redis高可用的基础。

一般来说，要将Redis运用于工程项目中，只使用一台Redis是万万不能的（宕机），原因如下： 

- 从结构上，单个Redis服务器会发生单点故障，并且一台服务器需要处理所有的请求负载，压力较 大；

- 从容量上，单个Redis服务器内存容量有限，就算一台Redis服务器内存容量为256G，也不能将所有内存用作Redis存储内存，一般来说，**单台Redis最大使用内存不应该超过20G**。

电商网站上的商品，一般都是一次上传，无数次浏览的，说专业点也就是"多读少写"。

对于这种场景，我们可以使如下这种架构：

![image-20220211010654809](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251015.png)

**主从复制，读写分离**！ 80%的情况下都是在进行读操作！减缓服务器的压力！架构中经常使用！ 一主二从！

只要在公司中，主从复制就是必须要使用的，因为在真实的项目中不可能单机使用Redis！

#### 主从模式搭建

> 只配置从库，不用配置主库！

默认情况下，每台Redis服务器都是主节点； 我们一般情况下只用配置从机就好了！ 认老大！

> 一主 （79）二从（80，81）

* 查看6381端口

```sh
redis-cli -p 6381
```

![image-20220211020528200](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251016.png)

* 查看6380端口

```sh
redis-cli -p 6380
```

![image-20220211020608151](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251017.png)

* 查看6379端口

```sh
redis-cli -p 6379
```

![image-20220211020616046](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251018.png)

* 配置一主二从

```sh
127.0.0.1:6380> slaveof 127.0.0.1 6379 # SLAVEOF host 6379 找谁当自己的老大！
OK
127.0.0.1:6380> info replication
# Replication
role:slave # 当前角色是从机
master_host:127.0.0.1 # 可以的看到主机的信息
master_port:6379
master_link_status:up
master_last_io_seconds_ago:5
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:ee5f9c93f4bffccd95bcbd457b706b709a410bb9
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14

======================================================================
# 在主机中查看！

127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:1 # 多了从机的配置
slave0:ip=127.0.0.1,port=6380,state=online,offset=168,lag=1 # 多了从机的配置
master_replid:ee5f9c93f4bffccd95bcbd457b706b709a410bb9
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:168
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:168

```

![image-20220211021051656](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251019.png)

![image-20220211021246135](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251020.png)

* 密码认证

如果master需要通过密码登陆，那就需要配置slave在进行所有同步操作也要使用到密码。 在一个运行的实例上尝试，使用 redis-cli :

```properties
config set masterauth <password>
```

你也可以设置永久的。在配置文件中增加：

```properties
masterauth <password>
```

**真实的从主配置应该在配置文件中配置，这样的话是永久的，我们这里使用的是命令，暂时的！**

* 永久修改主从配置文件

```properties
# 配置主机
## masterip：主机地址
## masterport：主机redis端口
replicaof <masterip> <masterport>

# 主机设置密码在这里设置
masterauth <master-password>
```

![image-20220211022429002](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251021.png)

#### 配置细节

主机可以写，从机不能写只能读！主机中的所有信息和数据，都会自动被从机保存！ **主机写**：

![image-20220211023136663](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251022.png)

**从机读**：

![image-20220211023206761](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251023.png)

测试：主机断开连接，从机依旧连接到主机的，但是没有写操作，这个时候，主机如果回来了，从机依旧可以直接获取到主机写的信息！ 

如果是使用命令行，来配置的主从，这个时候如果重启了，就会变回主机！只要变为从机，立马就会从主机中获取值！

> 重启了，命令配置的信息会消失

#### 复制原理

Slave 启动成功连接到 master 后会发送一个sync同步命令

Master 接到命令，启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行 完毕之后，**master将传送整个数据文件到slave，并完成一次完全同步。**

- **全量复制**：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。

- **增量复制**：Master 继续将新的所有收集到的修改命令依次传给slave，完成同步

但是只要是重新连接master，一次完全同步（全量复制）将被自动执行！ 我们的数据一定可以在从机中 看到！

#### 链路式主从复制

![image-20220211024507672](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202110251024.png)

这时候也可以完成我们的主从复制！

> 如果没有老大了，这个时候能不能选择一个老大出来呢？ 手动！

* 恢复主机地位

如果主机断开了连接，我们可以使用 `SLAVEOF no one` 让自己变成主机！其他的节点就可以手动连接到最新的这个主节点（手动）！如果这个时候老大修复了，那就重新连接！

### **哨兵模式**

> （自动选举老大的模式）

#### 概念

主从切换技术的方法是：当主服务器宕机后，需要手动把一台从服务器切换为主服务器，这就需要人工 干预，费事费力，还会造成一段时间内服务不可用。这不是一种推荐的方式，更多时候，我们优先考虑 哨兵模式。Redis从2.8开始正式提供了Sentinel（哨兵） 架构来解决这个问题。 

**哨兵模式能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库。**

哨兵模式是一种特殊的模式，首先Redis提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独 立运行。**其原理是哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个Redis实例。**

![image-20220211025451912](../../../../../../Pictures/assets/redis笔记/image-20220211025451912.png)

这里的哨兵有两个作用

* 通过发送命令，让Redis服务器返回监控其运行状态，包括主服务器和从服务器。 

* 当哨兵监测到master宕机，会自动将slave切换成master，然后通过发布订阅模式通知其他的从服 务器，修改配置文件，让它们切换主机。 

然而一个哨兵进程对Redis服务器进行监控，可能会出现问题，为此，我们可以使用多个哨兵进行监控。 各个哨兵之间还会进行监控，这样就形成了多哨兵模式。

![image-20220211025940177](../../../../../../Pictures/assets/redis笔记/image-20220211025940177.png)

假设主服务器宕机，哨兵1先检测到这个结果，系统并不会马上进行failover过程，仅仅是哨兵1主观的认 为主服务器不可用，这个现象成为**主观下线**。当后面的哨兵也检测到主服务器不可用，并且数量达到一 定值时，那么哨兵之间就会进行一次投票，投票的结果由一个哨兵发起，进行failover[故障转移]操作。 切换成功后，就会通过发布订阅模式，让各个哨兵把自己监控的从服务器实现切换主机，这个过程称为**客观下线**。

#### 哨兵模式搭建

* 配置哨兵配置文件 sentinel.conf

```sh
sudo vim sentinel.conf


# sentinel monitor 被监控的名称 host port 1
sentinel monitor myredis 127.0.0.1 6379 1
```

![image-20220211031620426](../../../../../../Pictures/assets/redis笔记/image-20220211031620426.png)

后面的这个数字1，代表主机挂了，slave投票看让谁接替成为主机，票数最多的，就会成为主机！

```sh
sudo chmod 777 redis-config/sentinel.conf
```

* 启动哨兵！

```sh
redis-sentinel redis-config/sentinel.conf
```

![image-20220211032108997](../../../../../../Pictures/assets/redis笔记/image-20220211032108997.png)

如果Master 节点断开了，这个时候就会从从机中随机选择一个服务器！ （这里面有一个投票算法！）

![image-20220211032327640](../../../../../../Pictures/assets/redis笔记/image-20220211032327640.png)

![image-20220211032505055](../../../../../../Pictures/assets/redis笔记/image-20220211032505055.png)

哨兵日志！

![image-20220211032619023](../../../../../../Pictures/assets/redis笔记/image-20220211032619023.png)

如果主机此时回来了，只能归并到新的主机下，当做从机，这就是哨兵模式的规则！

#### 优缺点

**优点**：

- 哨兵集群，基于主从复制模式，所有的主从配置优点，它全有

* 主从可以切换，故障可以转移，系统的可用性就会更好 3、哨兵模式就是主从模式的升级，手动到自动，更加健壮！

**缺点**

* Redis 不好啊在线扩容的，集群容量一旦到达上限，在线扩容就十分麻烦！

* 实现哨兵模式的配置其实是很麻烦的，里面有很多选择！

#### 哨兵完整配置

```properties
# Example sentinel.conf
 
# 哨兵sentinel实例运行的端口 默认26379
port 26379
 
# 哨兵sentinel的工作目录
dir /tmp
 
# 哨兵sentinel监控的redis主节点的 ip port 
# master-name  可以自己命名的主节点名字 只能由字母A-z、数字0-9 、这三个字符".-_"组成。
# quorum 当这些quorum个数sentinel哨兵认为master主节点失联 那么这时 客观上认为主节点失联了
# sentinel monitor <master-name> <ip> <redis-port> <quorum>
  sentinel monitor mymaster 127.0.0.1 6379 2
 
# 当在Redis实例中开启了requirepass foobared 授权密码 这样所有连接Redis实例的客户端都要提供密码
# 设置哨兵sentinel 连接主从的密码 注意必须为主从设置一样的验证密码
# sentinel auth-pass <master-name> <password>
sentinel auth-pass mymaster MySUPER--secret-0123passw0rd
 
 
# 指定多少毫秒之后 主节点没有应答哨兵sentinel 此时 哨兵主观上认为主节点下线 默认30秒
# sentinel down-after-milliseconds <master-name> <milliseconds>
sentinel down-after-milliseconds mymaster 30000
 
# 这个配置项指定了在发生failover主备切换时最多可以有多少个slave同时对新的master进行 同步，这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越 多的slave因为replication而不可用。可以通过将这个值设为 1 来保证每次只有一个slave 处于不能处理命令请求的状态。
# sentinel parallel-syncs <master-name> <numslaves>
sentinel parallel-syncs mymaster 1
 
 
 
# 故障转移的超时时间 failover-timeout 可以用在以下这些方面： 
#1. 同一个sentinel对同一个master两次failover之间的间隔时间。
#2. 当一个slave从一个错误的master那里同步数据开始计算时间。直到slave被纠正为向正确的master那里同步数据时。
#3.当想要取消一个正在进行的failover所需要的时间。  
#4.当进行failover时，配置所有slaves指向新的master所需的最大时间。不过，即使过了这个超时，slaves依然会被正确配置为指向master，但是就不按parallel-syncs所配置的规则来了
# 默认三分钟
# sentinel failover-timeout <master-name> <milliseconds>
sentinel failover-timeout mymaster 180000
 
# SCRIPTS EXECUTION
 
#配置当某一事件发生时所需要执行的脚本，可以通过脚本来通知管理员，例如当系统运行不正常时发邮件通知相关人员。
#对于脚本的运行结果有以下规则：
#若脚本执行后返回1，那么该脚本稍后将会被再次执行，重复次数目前默认为10
#若脚本执行后返回2，或者比2更高的一个返回值，脚本将不会重复执行。
#如果脚本在执行过程中由于收到系统中断信号被终止了，则同返回值为1时的行为相同。
#一个脚本的最大执行时间为60s，如果超过这个时间，脚本将会被一个SIGKILL信号终止，之后重新执行。
 
#通知型脚本:当sentinel有任何警告级别的事件发生时（比如说redis实例的主观失效和客观失效等等），将会去调用这个脚本，这时这个脚本应该通过邮件，SMS等方式去通知系统管理员关于系统不正常运行的信息。调用该脚本时，将传给脚本两个参数，一个是事件的类型，一个是事件的描述。如果sentinel.conf配置文件中配置了这个脚本路径，那么必须保证这个脚本存在于这个路径，并且是可执行的，否则sentinel无法正常启动成功。
#通知脚本
# sentinel notification-script <master-name> <script-path>
  sentinel notification-script mymaster /var/redis/notify.sh
 
# 客户端重新配置主节点参数脚本
# 当一个master由于failover而发生改变时，这个脚本将会被调用，通知相关的客户端关于master地址已经发生改变的信息。
# 以下参数将会在调用脚本时传给脚本:
# <master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
# 目前<state>总是“failover”,
# <role>是“leader”或者“observer”中的一个。 
# 参数 from-ip, from-port, to-ip, to-port是用来和旧的master和新的master(即旧的slave)通信的
# 这个脚本应该是通用的，能被多次调用，不是针对性的。
# sentinel client-reconfig-script <master-name> <script-path>
 sentinel client-reconfig-script mymaster /var/redis/reconfig.sh
 
 # 守护进程模式
daemonize yes
```

### **集群模式**

#### 概念

`Redis Cluster` 集群模式通常具有 **高可用**、**可扩展性**、**分布式**、**容错** 等特性。`Redis` 分布式方案一般有两种：

在redis的每一个节点上，都有这么两个东西，一个是插槽（slot），它的的取值范围是：0-16383。还有一个就是cluster，可以理解为是一个集群管理的插件。当我们的存取的key到达的时候，redis会根据crc16的算法得出一个结果，然后把结果对 16384 求余数，这样每个 key 都会对应一个编号在 0-16383 之间的哈希槽，通过这个值，去找到对应的插槽所对应的节点，然后直接自动跳转到这个对应的节点上进行存取操作。

为了保证高可用，`redis-cluster`集群引入了主从模式，一个主节点对应一个或者多个从节点，当主节点宕机的时候，就会启用从节点。当其它主节点ping一个主节点A时，如果半数以上的主节点与A通信超时，那么认为主节点A宕机了。如果主节点A和它的从节点A1都宕机了，那么该集群就无法再提供服务了。

![image-20220211080503278](../../../../../../Pictures/assets/redis笔记/image-20220211080503278.png)

##### 数据分区

Redis Cluster 采用虚拟槽进行分区，槽是集群内数据管理和迁移的基本单位。所有的键根据哈希函数映射到 16384 个整数槽内，每个节点负责维护一部分槽及槽上的数据，计算公式如下：

```properties
HASH_SLOT = CRC16(key) mod 16384
```

假设现在有一个6个节点的集群，分别有3个Master 点和3个 Slave 节点，槽会尽量均匀的分布在所有 Master 节点上。数据经过散列后存储在指定的 Master 节点上，之后 Slave 节点会进行对应的复制操作。这里再次说明一下槽只是一个虚拟的概念，并不是数据存放的实际载体。

![](../../../../../../Pictures/assets/redis笔记/redis-集群架构.png)

##### 节点通讯

在 Redis 分布式架构中，每个节点都存储有整个集群所有节点的元数据信息，这是通过 P2P 的 Gossip 协议来实现的。集群中的每个节点都会单独开辟一个 TCP 通道，用于节点之间彼此通信，通信端口号在基础端口上加 10000；每个节点定期通过特定的规则选择部分节点发送 ping 消息，接收到 ping 信息的节点用 pong 消息作为响应，通过一段时间的彼此通信，最终所有节点都会达到一致的状态，每个节点都会知道整个集群全部节点的状态信息，从而到达集群状态同步的目的。

![](../../../../../../Pictures/assets/redis笔记/redis节点通讯.png)

##### 请求路由

###### 请求重定向

在集群模式下，Redis 接收到命令时会先计算键对应的槽，然后根据槽找出对应的目标节点，如果目标节点就是此时所在的节点，则直接进行处理，否则返回 MOVED 重定向消息给客户端，通知客户端去正确的节点上执行操作。

###### Smart 客户端

Redis 的大多数客户端都是 Smart 客户端， Smart 客户端会在内部缓存槽与节点之间的映射关系，从而在本机就可以查找到正确的节点，这样可以保证 IO 效率的最大化。如果客户端还接收到 MOVED 重定向的消息，则代表客户端内部的缓存已经失效，此时客户端会去重新获取映射关系然后刷新本地缓存。

###### ASK 重定向

当集群处于扩容阶段时，此时槽上的数据可能正在从源节点迁移到目标节点，在这个期间可能出现一部分数据在源节点， 而另一部分在目标节点情况。此时如果源节点接收到命令并判断出键对象不存在， 说明其可能存在于目标节点上， 这时会返回给客户端 ASK 重定向异常。

ASK 重定向与 MOVED 重定向的区别在于：收到 ASK 重定向时说明集群正在进行数据迁移， 客户端无法知道什么时候迁移完成，因此只是临时性的重定向， 客户端不会更新映射缓存。 但是 MOVED 重定向说明键对应的槽已经明确迁移到新的节点， 因此需要更新映射缓存。

###### 故障发现

由于 Redis 集群的节点间都保持着定时通讯，某个节点向另外一个节点发送 ping 消息，如果正常接受到 pong 消息，此时会更新与该节点最后一次的通讯时间记录，如果之后无法正常接受到 pong 消息，并且判断当前时间与最后一次通讯的时间超过 `cluster-node-timeout` ，此时会对该节点做出主观下线的判断。

当做出主观下线判断后，节点会把这个判断在集群内传播，通过 Gossip 消息传播， 集群内节点不断收集到故障节点的下线报告。 当半数以上持有槽的主节点都标记某个节点是主观下线时， 触发客观下线流程。  这里需要注意的是只有持有槽主节点才有权利做出主观下线的判断，因为集群模式下只有处理槽的主节点才负责读写请求和维护槽等关键信息， 而从节点只进行主节点数据和状态信息的复制。

###### 故障恢复

1. **资格检查**

每个从节点都要检查最后与主节点断线时间， 判断是否有资格替换故障的主节点。 如果从节点与主节点断线时间超过 `cluster-node-time*cluster-slave-validity-factor`，则当前从节点不具备故障转移资格。 这两个参数可以在 `redis.conf` 中进行修改，默认值分别为 15000 和 10。

2. **准备选举**

当从节点符合故障转移资格后， 更新触发故障选举的时间， 只有到达该时间后才能执行后续流程。在这一过程中，Redis 会比较每个符合资格的从节点的复制偏移量，然后让复制偏移量大（即数据更加完整）的节点优先发起选举。

3. **选举投票**

从节点每次发起投票时都会自增集群的全局配置纪元，全局配置纪元是一个只增不减的整数。之后会在集群内广播选举消息，只有持有槽的主节点才会处理故障选举消息，并且每个持有槽的主节点在一个配置纪元内只有唯一的一张选票。假设集群内有 N 个持有槽的主节点，当某个从节点获得 N/2+1 张选票则代表选举成功。如果在开始投票之后的 `cluster-node-timeout*2` 时间内没有从节点获取足够数量的投票， 则本次选举作废，从节点会对配置纪元自增并发起下一轮投票， 直到选举成功为止。

4. **替换主节点**

当从节点收集到足够的选票之后，就会触发替换主节点操作：

- 当前从节点取消复制变为主节点。
- 执行 clusterDelSlot 操作撤销原主节点负责的槽， 并执行 clusterAddSlot 把这些槽委派给自己。
- 向集群广播自己的 pong 消息，通知集群内的其他节点自己已经成为新的主节点。

#### 集群模式搭建

##### redis配置文件管理

拷贝6份 `redis.conf`，分别命名为 `redis-6379.conf` ~ `redis-6384.conf`

```sh
sudo mkdir redis-cluster-config

sudo cp redis.conf redis-6379.conf
sudo cp redis.conf redis-6380.conf
sudo cp redis.conf redis-6381.conf
sudo cp redis.conf redis-6382.conf
sudo cp redis.conf redis-6383.conf
sudo cp redis.conf redis-6384.conf

# 编辑配置文件
sudo vim redis-6379.conf
sudo vim redis-6380.conf
sudo vim redis-6381.conf
sudo vim redis-6382.conf
sudo vim redis-6383.conf
sudo vim redis-6384.conf
```

需要修改的配置项如下：

```
# redis-6379.conf
port 6379
# 以守护进程的方式启动
daemonize yes  
# 当Redis以守护进程方式运行时，Redis会把pid写入该文件
pidfile /var/run/redis_6379.pid  
logfile /usr/local/bin/redis-log/redis-log-6379.log
dbfilename dump-6379.rdb
dir /usr/local/bin/redis-dbfile/
# 开启集群模式
cluster-enabled yes 
# 节点超时时间，单位毫秒
cluster-node-timeout 15000
# 集群内部配置文件
cluster-config-file /usr/local/bin/redis-cluster-config/nodes-6379.conf
# aof日志开启，有需要就开启，它会每次写操作都记录一条日志
appendonly yes


# redis-6380.conf
port 6380
daemonize yes  
pidfile /var/run/redis_6380.pid  
logfile 6380.log
dbfilename dump-6380.rdb
dir /usr/local/bin/redis-dbfile/
cluster-enabled yes 
cluster-node-timeout 15000
cluster-config-file /usr/local/bin/redis-cluster-config/nodes-6380.conf
appendonly yes
..... 其他配置类似，修改所有用到端口号的地方
```

* 赋予文件权限

```sh
# 赋予权限
sudo chmod 777 redis-config/redis-6379.conf
sudo chmod 777 redis-config/redis-6380.conf
sudo chmod 777 redis-config/redis-6381.conf
sudo chmod 777 redis-config/redis-6382.conf
sudo chmod 777 redis-config/redis-6383.conf
sudo chmod 777 redis-config/redis-6384.conf
```

`Redis` 集群的节点规划如下：

| 节点名称   | 端口号 | 是主是从 | 所属主节点 |
| ---------- | ------ | -------- | ---------- |
| redis-6379 | 6379   | 主节点   | ---        |
| redis-6380 | 6389   | 从节点   | redis-6379 |
| redis-6381 | 6380   | 主节点   | ---        |
| redis-6382 | 6390   | 从节点   | redis-6380 |
| redis-6383 | 6381   | 主节点   | ---        |
| redis-6384 | 6391   | 从节点   | redis-6381 |

##### 查看集群命令

`redis-cli --cluster` **命令行工具** 的具体功能如下：

| 命令        | 作用                             |
| ----------- | -------------------------------- |
| create      | 创建集群                         |
| check       | 检查集群                         |
| info        | 查看集群信息                     |
| fix         | 修复集群                         |
| reshard     | 在线迁移slot                     |
| rebalance   | 平衡集群节点slot数量             |
| add-node    | 将新节点加入集群                 |
| del-node    | 从集群中删除节点                 |
| set-timeout | 设置集群节点间心跳连接的超时时间 |
| call        | 在集群全部节点上执行命令         |
| import      | 将外部redis数据导入集群          |

```properties
redis-cli --cluster help
Cluster Manager Commands:
  create         host1:port1 ... hostN:portN   #创建集群
                 --cluster-replicas <arg>      #从节点个数
  check          host:port                     #检查集群
                 --cluster-search-multiple-owners #检查是否有槽同时被分配给了多个节点
  info           host:port                     #查看集群状态
  fix            host:port                     #修复集群
                 --cluster-search-multiple-owners #修复槽的重复分配问题
  reshard        host:port                     #指定集群的任意一节点进行迁移slot，重新分slots
                 --cluster-from <arg>          #需要从哪些源节点上迁移slot，可从多个源节点完成迁移，以逗号隔开，传递的是节点的node id，还可以直接传递--from all，这样源节点就是集群的所有节点，不传递该参数的话，则会在迁移过程中提示用户输入
                 --cluster-to <arg>            #slot需要迁移的目的节点的node id，目的节点只能填写一个，不传递该参数的话，则会在迁移过程中提示用户输入
                 --cluster-slots <arg>         #需要迁移的slot数量，不传递该参数的话，则会在迁移过程中提示用户输入。
                 --cluster-yes                 #指定迁移时的确认输入
                 --cluster-timeout <arg>       #设置migrate命令的超时时间
                 --cluster-pipeline <arg>      #定义cluster getkeysinslot命令一次取出的key数量，不传的话使用默认值为10
                 --cluster-replace             #是否直接replace到目标节点
  rebalance      host:port                                      #指定集群的任意一节点进行平衡集群节点slot数量 
                 --cluster-weight <node1=w1...nodeN=wN>         #指定集群节点的权重
                 --cluster-use-empty-masters                    #设置可以让没有分配slot的主节点参与，默认不允许
                 --cluster-timeout <arg>                        #设置migrate命令的超时时间
                 --cluster-simulate                             #模拟rebalance操作，不会真正执行迁移操作
                 --cluster-pipeline <arg>                       #定义cluster getkeysinslot命令一次取出的key数量，默认值为10
                 --cluster-threshold <arg>                      #迁移的slot阈值超过threshold，执行rebalance操作
                 --cluster-replace                              #是否直接replace到目标节点
  add-node       new_host:new_port existing_host:existing_port  #添加节点，把新节点加入到指定的集群，默认添加主节点
                 --cluster-slave                                #新节点作为从节点，默认随机一个主节点
                 --cluster-master-id <arg>                      #给新节点指定主节点
  del-node       host:port node_id                              #删除给定的一个节点，成功后关闭该节点服务
  call           host:port command arg arg .. arg               #在集群的所有节点执行相关命令
  set-timeout    host:port milliseconds                         #设置cluster-node-timeout
  import         host:port                                      #将外部redis数据导入集群
                 --cluster-from <arg>                           #将指定实例的数据导入到集群
                 --cluster-copy                                 #migrate时指定copy
                 --cluster-replace                              #migrate时指定replace
  help           

For check, fix, reshard, del-node, set-timeout you can specify the host and port of any working node in the cluster.
```

##### 启动集群

启动所有redis节点

```sh
sudo redis-server redis-config/redis-6379.conf
sudo redis-server redis-config/redis-6380.conf
sudo redis-server redis-config/redis-6381.conf
sudo redis-server redis-config/redis-6382.conf
sudo redis-server redis-config/redis-6383.conf
sudo redis-server redis-config/redis-6384.conf
```

启动所有 Redis 节点，启动后使用 `ps -ef | grep redis` 查看进程，输出应如下：

![image-20220211064057989](../../../../../../Pictures/assets/redis笔记/image-20220211064057989.png)

接着需要使用以下命令创建集群，集群节点之间会开始进行通讯，并完成槽的分配：

```sh
redis-cli --cluster create 127.0.0.1:6379 127.0.0.1:6380 127.0.0.1:6381 127.0.0.1:6382 127.0.0.1:6383 127.0.0.1:6384 --cluster-replicas 1
```

集群创建后，`redis-cli --cluster` 会先将 `16384` 个 **哈希槽** 分配到 `3` 个 **主节点**，即 `redis-6379`，`redis-6380` 和 `redis-6381`。然后将各个 **从节点** 指向 **主节点**，进行 **数据同步**。`--cluster-replicas 1`表示从节点数目为1。

![image-20220211065451689](../../../../../../Pictures/assets/redis笔记/image-20220211065451689.png)

然后输入 `yes`，开始执行 **节点握手** 和 **槽分配** 操作，输出如下

```properties
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join

>>> Performing Cluster Check (using node 127.0.0.1:6379)
M: 8b3d969e8b5b99c31914d6401fedf0ce9b47140b 127.0.0.1:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 82d28d811d271056551fd337ca1257d6b75475c1 127.0.0.1:6381
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: ab2dcf9ca6a854aeb80ce93df97d1f6e49ad27b5 127.0.0.1:6383
   slots: (0 slots) slave
   replicates 82d28d811d271056551fd337ca1257d6b75475c1
S: 63a2ada7c405fb10f5620ed809e9a3e0214ccd6d 127.0.0.1:6382
   slots: (0 slots) slave
   replicates 382b2bbd03b3d5a09c4f38998248537fb5bc9ab5
S: 8954504e382293023d98445ae4157a4e52237666 127.0.0.1:6384
   slots: (0 slots) slave
   replicates 8b3d969e8b5b99c31914d6401fedf0ce9b47140b
M: 382b2bbd03b3d5a09c4f38998248537fb5bc9ab5 127.0.0.1:6380
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

执行 **集群检查**，检查各个 `redis` 节点占用的 **哈希槽**（`slot`）的个数以及 `slot` **覆盖率**。`16384` 个槽位中，**主节点** `redis-6379`、`redis-6380` 和 `redis-6381` 分别占用了 `5461`、`5462` 和 `5461` 个槽位。

##### 集群完整性校验

集群完整性指所有的槽都分配到存活的主节点上， 只要16384个槽中有一个没有分配给节点则表示集群不完整。 可以使用以下命令进行检测， check 命令只需要给出集群中任意一个节点的地址就可以完成整个集群的检查工作：

```sh
redis-cli --cluster check 127.0.0.1:6379
```

#### 集群伸缩

Redis 集群提供了灵活的节点扩容和缩容方案，可以在不影响集群对外服务的情况下，进行动态伸缩。

#####  集群扩容

这里准备两个新的节点 6485 和 6486，配置和其他节点一致，配置完成后进行启动。

```sh
sudo cp redis-6384.conf redis-6385.conf
sudo cp redis-6384.conf redis-6386.conf

sudo vim redis-6385.conf
sudo vim redis-6386.conf

sudo chmod 777 redis-config/redis-6385.conf
sudo chmod 777 redis-config/redis-6386.conf

sudo redis-server redis-config/redis-6385.conf
sudo redis-server redis-config/redis-6386.conf

ps -ef|grep redis
```

![image-20220211070920433](../../../../../../Pictures/assets/redis笔记/image-20220211070920433.png)

集群扩容的命令为 `add-node`，第一个参数为需要加入的新节点，第二个参数为集群中任意节点，用于发现集群：

```sh
redis-cli --cluster add-node 127.0.0.1:6385 127.0.0.1:6379
```

![image-20220211071128676](../../../../../../Pictures/assets/redis笔记/image-20220211071128676.png)

成功加入集群后，可以使用 `cluster nodes` 命令查看集群情况。不做任何特殊指定，默认加入集群的节点都是主节点，但是集群并不会为分配任何槽。如下图所示，其他 master 节点后面都有对应的槽的位置信息，但新加入的 6385 节点则没有，由于没有负责的槽，所以该节点此时不能进行任何读写操作：

```sh
redis-cli -h 127.0.0.1 -p 6379 cluster nodes
```

![image-20220211071528633](../../../../../../Pictures/assets/redis笔记/image-20220211071528633.png)

想要让新加入的节点能够进行读写操作，可以使用 `reshard` 命令为其分配槽，这里我们将其他三个主节点上的槽迁移一部分到 6485 节点上，这里一共迁移 4096 个槽，即 16384 除以 4 。 `cluster-from` 用于指明槽的源节点（主节点），可以为多个，`cluster-to` 为槽的目标节点，`cluster-slots` 为需要迁移的槽的总数：

```sh
redis-cli --cluster reshard 127.0.0.1:6379 \
--cluster-from 8b3d969e8b5b99c31914d6401fedf0ce9b47140b,382b2bbd03b3d5a09c4f38998248537fb5bc9ab5,82d28d811d271056551fd337ca1257d6b75475c1 \
--cluster-to a8293c46e0d8ada05ebacdc4a5071fd705805ebf \
--cluster-slots 4096 --cluster-yes
```

迁移后，再次使用 `cluster nodes` 命令可以查看到此时 6485 上已经有其他三个主节点上迁移过来的槽：

```sh
redis-cli -h 127.0.0.1 -p 6379 cluster nodes
```

![image-20220211071848727](../../../../../../Pictures/assets/redis笔记/image-20220211071848727.png)

为保证高可用，可以为新加入的主节点添加从节点，命令如下。`add-node` 接收两个参数，第一个为需要添加的从节点，第二个参数为集群内任意节点，用于发现集群。`cluster-master-id` 参数用于指明作为哪个主节点的从节点，如果不加这个参数，则自动分配给从节点较少的主节点：

```sh
redis-cli --cluster add-node 127.0.0.1:6386 127.0.0.1:6379 --cluster-slave \
--cluster-master-id a8293c46e0d8ada05ebacdc4a5071fd705805ebf
```

#### 集群缩容

集群缩容的命令如下：第一个参数为集群内任意节点，用于发现集群；第二个参数为需要删除的节点：

```sh
redis-cli --cluster del-node 127.0.0.1:6379 `<node-id>`
```

需要注意的是待删除的主节点上必须为空，如果不为空则需要将它上面的槽和数据迁移到其他节点上，和扩容时一样，可以使用 `reshard` 命令来完成数据迁移。

## Redis缓存问题

### 缓存问题

Redis缓存的使用，极大的提升了应用程序的性能和效率，特别是数据查询方面。但同时，它也带来了一 些问题。其中，最要害的问题，就是数据的一致性问题，从严格意义上讲，这个问题无解。如果对数据 的一致性要求很高，那么就不能使用缓存。

另外的一些典型问题就是，缓存穿透、缓存雪崩和缓存击穿。目前，业界也都有比较流行的解决方案。

![image-20220211073005484](../../../../../../Pictures/assets/redis笔记/image-20220211073005484.png)

### 缓存穿透（查不到）

#### 概念

缓存穿透的概念很简单，用户想要查询一个数据，发现redis内存数据库没有，也就是缓存没有命中，于 是向持久层数据库查询。发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中（秒 杀！），于是都去请求了持久层数据库。这会给持久层数据库造成很大的压力，这时候就相当于出现了 缓存穿透。

#### 解决方案

##### 布隆过滤器

布隆过滤器是一种数据结构，对所有可能查询的参数以hash形式存储，在控制层先进行校验，不符合则 丢弃，从而避免了对底层存储系统的查询压力；

![image-20220211073410275](../../../../../../Pictures/assets/redis笔记/image-20220211073410275.png)

**布隆过滤器（Bloom Filter）本质上是由长度为 m 的位向量或位列表**（仅包含 0 或 1 位值的列表）组成，最初所有的值均设置为 0，如下图所示。

![img](../../../../../../Pictures/assets/redis笔记/16eba609849bd878~tplv-t2oaga2asx-watermark.awebp)

**为了将数据项添加到布隆过滤器中，会提供 K 个不同的哈希函数，并将结果位置上对应位的值置为 “1”。**

布隆过滤器有一个可预测的误判率（FPP）：



![img](../../../../../../Pictures/assets/redis笔记/16eba609871807ab~tplv-t2oaga2asx-watermark.awebp)



- n 是已经添加元素的数量；
- k 哈希的次数；
- m 布隆过滤器的长度（如比特数组的大小）；

实际情况中，布隆过滤器的长度 m 可以根据给定的误判率（FFP）的和期望添加的元素个数 n 的通过如下公式计算：

![img](../../../../../../Pictures/assets/redis笔记/16eba609a9158723~tplv-t2oaga2asx-watermark.awebp)

##### 缓存空对象

当存储层不命中后，即使返回的空对象也将其缓存起来，同时会设置一个过期时间，之后再访问这个数 据将会从缓存中获取，保护了后端数据源；

![image-20220211073429318](../../../../../../Pictures/assets/redis笔记/image-20220211073429318.png)

但是这种方法会存在两个问题： 

- 如果空值能够被缓存起来，这就意味着缓存需要更多的空间存储更多的键，因为这当中可能会有很多 的空值的键；

* 即使对空值设置了过期时间，还是会存在缓存层和存储层的数据会有一段时间窗口的不一致，这对于 需要保持一致性的业务会有影响。

### 缓存击穿（量太大，缓存过期！）

#### 概述

这里需要注意和缓存击穿的区别，缓存击穿，是指一个key非常热点，在不停的扛着大并发，大并发集中 对这一个点进行访问，当这个key在失效的瞬间，持续的大并发就穿破缓存，直接请求数据库，就像在一 个屏障上凿开了一个洞。

当某个key在过期的瞬间，有大量的请求并发访问，这类数据一般是热点数据，由于缓存过期，会同时访问数据库来查询最新数据，并且回写缓存，会导使数据库瞬间压力过大。

#### 解决方案

**设置热点数据永不过期**

从缓存层面来看，没有设置过期时间，所以不会出现热点 key 过期后产生的问题。

> 比如微博热搜，可能会导致微博服务器宕机

**加互斥锁**

分布式锁：使用分布式锁，保证对于每个key同时只有一个线程去查询后端服务，其他线程没有获得分布 式锁的权限，因此只需要等待即可。这种方式将高并发的压力转移到了分布式锁，因此对分布式锁的考 验很大。

![image-20220211074847534](../../../../../../Pictures/assets/redis笔记/image-20220211074847534.png)

### 缓存雪崩

#### 概念

缓存雪崩，是指在某一个时间段，缓存集中过期失效。Redis 宕机！

产生雪崩的原因之一，比如马上就要到双十二零点，很快就会迎来一波抢购，这波商品时间比较集中的放入了缓存，假设缓存一个小时。那么到了凌晨一点钟的时候，这批商品的缓存就都过期了。而对这批商品的访问查询，都落到了数据库上，对于数据库而言，就会产生周期性的压力波峰。于是所有的请求都会达到存储层，存储层的调用量会暴增，造成存储层也会挂掉的情况。

![image-20220211075403898](../../../../../../Pictures/assets/redis笔记/image-20220211075403898.png)

其实集中过期，倒不是非常致命，比较致命的缓存雪崩，是缓存服务器某个节点宕机或断网。因为自然 形成的缓存雪崩，一定是在某个时间段集中创建缓存，这个时候，数据库也是可以顶住压力的。无非就 是对数据库产生周期性的压力而已。而缓存服务节点的宕机，对数据库服务器造成的压力是不可预知 的，很有可能瞬间就把数据库压垮。

#### 解决方案

##### redis高可用

这个思想的含义是，既然redis有可能挂掉，那我多增设几台redis，这样一台挂掉之后其他的还可以继续工作，其实就是**搭建的集群**。（异地多活！） 

##### 限流降级

这个解决方案的思想是，在缓存失效后，通过加锁或者队列来控制读数据库写缓存的线程数量。比如**对某个key只允许一个线程查询数据和写缓存，其他线程等待**。

##### 数据预热

数据加热的含义就是在正式部署之前，我先把可能的数据先预先访问一遍，这样部分可能大量访问的数据就会加载到缓存中。**在即将发生大并发访问前手动触发加载缓存不同的key，设置不同的过期时间，让缓存失效的时间点尽量均匀**。