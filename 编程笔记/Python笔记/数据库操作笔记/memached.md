## Memcached

### memcached 介绍

1. memcached之前是danga的一个项目，最早是为LiveJournal服务的，当初设计师为了加速LiveJournal访问速度而开发的，后来被很多大型项目采用。官网是` www.danga.com` 或者是 `memcached.org`
2. Memcached是一个高性能的分布式的内存对象缓存系统，全世界有不少公司采用这个缓存项目来构建大负载的网站，来分担数据库的压力。Memcached是通过在内存里维护一个巨大的hash表，memcached能存储各种各样的数据，包括图像、视频、文件、以及数据库检索的结果等。简单的说就是将数据调用到内存中，然后从内存中读取，从而大大提高读取速度
3. 哪些情况下适合使用Memcached:存储验证码(图形验证码，短信验证码)、登录session等所有不是至关重要的数据。

### memcached 安装和参数详解

1. windows:

- 安装:memcached -d install

  

  ```
  32位系统 1.4.4版本：http://static.runoob.com/download/memcached-win32-1.4.4-14.zip
  64位系统 1.4.4版本：http://static.runoob.com/download/memcached-win64-1.4.4-14.zip
  32位系统 1.4.5版本：http://static.runoob.com/download/memcached-1.4.5-x86.zip
  64位系统 1.4.5版本：http://static.runoob.com/download/memcached-1.4.5-amd64.zip
  ```

- 启动:memcached -d start

- 停止:memcached.exe -d stop

- 卸载:memcached.exe -d uninstall

- memcached版本>=1.45

  - 安装

  

  ```
  schtasks /create /sc onstart /tn memcached /tr "'F:\software\memcached>memcached.exe' -m 512"
  
  注意：-m 512 意思是设置 memcached 最大的缓存配置为512M。我们可以通过使用
   "memcached.exe -h" 命令查看更多的参数配置。
  ```

2. linux(ubuntu):

- 安装:sudo apt-get install memcached

- 启动:sudo service memcached start

- 查看是否启动成功

  ```
  查看当前所有端口
  ps aux|grep memcached
  ```

- 建议使用windows自带的linux子系统

- 注意:ufw allow 11211

3. 可能出现的问题:

- 提示没有权限:使用管理员权限
- 不要放在含有中文的路径下面
- 提示缺少pthreadGC2.dll文件；将缺少文件拷贝到windows/System32中

```
/usr/bin/memcached -u memcache start

# 后台运行
/usr/bin/memcached -u memcache -d start

# 杀掉所有memcached进程
sudo killall memcached
```

4. 启动memcached:

```
注意:指定用户
```

- -d:这个参数是让memcached在后台运行
- -m:指定占用多少内存，以n为单位，默认为64M
- -p:指定占用的端口。默认的端口是11211
- -l:别的机器可以通过哪个ip地址链接到这台服务器。想要别的机器链接，就必须配置'-l 0.0.0.0'

```
 /usr/bin/memcached -u memcache -l 0.0.0.0 -d start
```

### telnet 操作memcached



windows下可能没有启动telnet，需要手动启动telnet服务

#### **启动Telnet**

首先win+R，在弹出框中输入control并点击打开，打开后，点击程序，可以看见如下图所示

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922230847.png)

然后点击启用或关闭Windows功能

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/notebook-picgo@master/img/20200922230848.png)

打开后，勾选telnet客户端选项，然后点击确定，便可以安装telent了，安装完成后，重启后，便可以正式使用了。

> 接下来开始学习telnet操作memcached的方法

#### **telnet 登录memcached**

命令格式:



```
telnet ip 地址 端口号
```

示例:



```
telnet 127.0.0.1 11211
```

> 温馨提示:memcached 存储数据是以键值对的方式存储的

#### **添加数据**

- set 命令（设置）

  - 语法格式

    

    ```
    set key 0(否)(是否压缩) timeout(过期时间) value_length(字符串的长度)
    ```

  - 示例

    

    ```
    set name 0 60 5
    angle
    STORED
    ```

- add 命令（添加）

  - 语法格式

    

    ```
    add key 0 timeout value_length
    ```

  - 示例

    

    ```
    add name 0 60 2
    ab
    STORED
    ```

> 温馨提示:
>
> set 和 add 的区别:add是只负责添加数据，不会去修改数据。如果添加的数据的key已经存在了，则添加失败，如果是添加的key不存在，则添加成功。而set不同，如果memcached中不存在相同的key，则进行添加，如果存在，则替换。

#### **获取数据**

- get 命令

  - 语法格式

    

    ```
    get key
    ```

  - 示例

    

    ```
    get name
    angle
    ```

#### **删除数据**

- delete 命令

  - 语法格式

    

    ```
    delete key
    ```

  - 示例

    

    ```
    delete name
    ```

- flush_all 命令

  - 语法格式

    

    ```
    flush_all
    ```

  - 删除memcached中的所有键值对

#### **查看memcached的当前状态**

- status 命令

  

  ```
  'get_hists':get命令命中了多少次
  'get_misses':get命令get空了几次
  'curr_items':当前'memcached'中的键值对的个数
  'total_connections':从'memcached'开启到现在总共的连接数
  'curr_connections':当前'memcached'的连接数
  ‘memcached’默认醉的连接数是1024
  ```

#### **增加**



```
set age 0 120 2  > 20
incr age 2 > 22
注意:必须都是数值类型，不然会报错
```

#### **减少**

```
decr age 2  > 20
```

### python 操作memcached



#### **1.安装**



```
pip install python-memcached
```

#### **2.建立连接**



```
mc = memcache.Client(["127.0.0.1:11211"],debug=True)
```

#### **3.设置数据**



```
mc.set(key="name",val="angle",time=60,min_compress_len=5)

# 设置多个值
mc.set_multi({'title':r'小红帽','content':r'没有内容'},time=100)
```

#### **4.获取数据**



```
mc.get('title')
```

#### **5.删除数据**



```
mc.delete('name')
```

#### **6.自增长**



```
# 默认自增加一,delta属性设置增加值
mc.incr('age',delta=10)
```

#### **7.自减少**



```
mc.decr('age',delta=10)
```

* 代码

```
import memcache

# 连接
# 设置debug为true可以显示错误信息
# 在连接之前，要启动memcached服务
mc = memcache.Client(["127.0.0.1:11211"],debug=True)

# 设置
# time=0，永远不会过期
# key:键
# value:值
# mc.set(key="name",val="angle",time=60,min_compress_len=5)

# 设置多个值
# mc.set_multi({'title':r'小红帽','content':r'没有内容'},time=100)

# # 获取
# # print(mc.get("title"))
# username = mc.get('username')
# print(mc.get('username'))
#
# # 删除
# mc.delete('username')
# print(mc.get('username'))

# 默认自增加一,delta属性设置增加值
mc.incr('age',delta=10)
age = mc.get('age')
print(age)

# 自减少
mc.decr('age',delta=10)
age = mc.get('age')
print(age)
```

### memcached 的安全机制



memccached 的操作不需要任何用户名和密码，只需要知道'memcached'服务器的ip地址和端口号即可。因此'memcached'使用的时候尤其注意它的安全性。这里听两种安全的解决方法。

1. 使用'-l'参数设置为只有本地可以连接:这种方式，就只能通过本机才能连接，别的机器都不能访问，可以达到最好的安全性
2. 使用防火墙，关闭'11211'端口，外面也不能访问

> linux

```
ufw enable # 开启防火墙
ufw disable # 关闭防火漆
ufw default deny:防火墙以禁止的方式打开，默认是关闭哪些没有开启的端口
ufw deny 端口号 # 关闭某个端口
ufw allow 端口号 # 开启某个端口
查看端口状态:ufw status
```

