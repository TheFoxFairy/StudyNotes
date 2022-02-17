# Linux笔记

## 计算机概述

### 冯诺依曼体系结构

- 计算机处理的数据和指令一律用二进制数表示
- 顺序执行程序
- 计算机硬件由运算器、控制器、存储器、输入设备和输出设备五大部分组成

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122349.png" alt="image-20220215065945520"  />



### 计算机硬件组成

**输入设备**：输入设备用来将人们熟悉的信息形式转换为机器能够识别的信息形式

- 常见的有键盘，鼠标等

**输出设备**：输出设备可以将机器运算结果转换为人们熟悉的信息形式

- 打印机输出，显示器输出等

**存储器**：存储器用来存放数据和程序

- RAM(random access memory）即随机存储内存:
  - 速度快，容量小
  - 掉电易失
  - 逻辑IO
- ROM（Read-Only Memory）即只读内存硬盘:
  - 容量大，速度相对较慢
  - 长久保存
  - 物理IO

**CPU(中央处理器)**：

- 控制器
  - 控制器主要用来控制和指挥程序和数据的输入运行，以及处理运算结果
- 运算器
  - 运算器主要运行算数运算和逻辑运算，并将中间结果暂存到运算器中

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122350.png" alt="image-20220215070135910"  />

### 硬盘的分类

硬盘按照存储介质的不同可以分为如下两种：

- **机械硬盘（Hard Disk Driver, HDD）**
  - 机械硬盘采用磁性碟片来存储数据
  - 用显微镜把盘片放大，会看见盘片表面凹凸不平，凸起的地方被磁化，凹的地方是没有被磁化
  - 凸起的地方代表数字1（磁化为1），凹的地方代表数字0。
  - 硬盘可以以二进制来存储表示文字、图片等信息。
  - 硬盘可以根据转速来判断硬盘的好坏（转速7200转/分，读取速度100-200M/s）

![image-20220215070220344](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122351.png)

* **固态硬盘（Solid State Disk, SSD）**
  * 固态硬盘通过闪存颗粒（固态电子存储芯片阵列）来存储数据

![image-20220215070327724](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122352.png)

**读写速度的区别**：

- 固态硬盘的读取速度普遍可以达到400M/s，写入速度也可以达到130M/s以上，
- 其读写速度是普通机械硬盘的3-5倍。

**机械硬盘的数据读写**：

- 主流的硬盘半机械半电子硬盘(机械硬盘)
  - 硬盘的转速(转速越快读取越快)
  - 寻道时间
  - 数据传输时间

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122353.png" alt="image-20220215070541605" style="zoom:67%;" />

### 顺序读写与随机读写

![image-20220215070631735](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122354.png)

顺序读写比随机读写快很多，但是很不幸，随机读写比顺序读写在实际应用中更好。

### 网络连接概念

**IP地址IPADDR**：

- IP地址是一种逻辑地地址，用来标识网络中一个个主机
  - IP地址=网络地址+主机地址
  - IP地址是一个 4 * 8bit（1字节）由 0/1 组成的数字串（IP4协议）（0,1,255绝对不要使用）

**子网掩码NETMASK**：

- 网掩码只有一个功能，就是将IP地址划分为网络地址和主机地址两部分。

```
ipaddr：网络地址+主机地址，比如192.168.206.8
netmask子网掩码(255.255.255.0)--分割（通过与运算）

进行与运算，同时为1，结果才为1：
255.255.255.0
192.168.206.8
192.168.206.0 这就是网络地址，剩下就是主机地址
```

- 子网掩码可以用来判断任意两台计算机的IP地址是否在同一个子网中的根据
  - A 192.168.7.111 B 192.168.8.222
  - 255.255.0.0

**默认网关GATEWAY**：

- 连接两个不同的网络的设备都可以叫网关设备；网关的作用就是实现两个网络之间进行通讯与控制。
- 网关地址就是网关设备的IP地址

**域名服务器DNS**：

![image-20220215070738701](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122355.png)

- DNS是域名服务器，用来解析域名的（域名和IP之间的解析）。（一般借助阿里云等公司帮助注册域名）
- 如果没有这东西，登陆某个网站时就必须输入该网站的IP地址，有了DNS就可以直接输入网址。
- `C:\Windows\System32\drivers\etc\hosts`，修改该文件，通过指定字符指向的IP地址

```
127.0.0.1 baidu
```

![image-20220215083458023](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122356.png)

> 域名劫持就利用了该原理。

### 网络连接模式

- **host-only(主机模式)**
  - 在某些特殊的网络调试环境中，要求将真实环境和虚拟环境隔离开，这时你就可采用host-only模式。
  - 在host-only模式中，所有的虚拟系统是可以相互通信的，但虚拟系统和真实的网络是被隔离开的。
  - 在host-only模式下，虚拟系统的TCP/IP配置信息都是由VMnet1(host-only)虚拟网络的DHCP服务器来动态分配的
- **bridged(桥接模式)**
  - VMWare虚拟出来的操作系统就像是局域网中的一台独立的主机，它可以访问网内任何一台机器。
  - 使用桥接模式的虚拟系统和宿主机器的关系，就像连接在同一个Hub上的两台电脑。
  - 当前主机IP 为 192.168.8.100 虚拟机 192.168.8.xxx
    - 学习期间为了防止IP冲突，所以不选择这种模式
- **NAT(网络地址转换模式)**
  - 使用NAT模式，就是让虚拟系统借助NAT(网络地址转换)功能，通过宿主机器所在的网络来访问公网。
  - NAT模式下的虚拟系统的TCP/IP配置信息是由VMnet8(NAT)虚拟网络的DHCP服务器提供的
  - 虚拟系统也就无法和本局域网中的其他真实主机进行通讯

![image-20220215070821683](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122357.png)

### 软件分类

**应用软件**：

- 就是为了实现某些业务功能
- 应用软件要基于对应的系统软件
  - 不同的操作系统要安装不同的软件

**系统软件**：

- 就是为了和硬件打交道
- 屏蔽应用软件与硬件的差异

**系统软件的分类**：

- Window
  - 用户量全球最大
  - 收费，不开源，民用较多
  - 各种软件比较齐全
- Mac：只限定于某些苹果的品牌机（ios--自成一家）
- GNU/Linux：GNU是一个开源软件组织,世界上所有的软件都应该开源免费
  - GNU Is Not Unix
  - GCC++
  - 托瓦兹 林纳斯 Linus -- Linux(Linux is not unix)
  - Logo是企鹅

### Linux分支

- RedHat（收费）
  - CentOS
    - 完全开源免费
    - 不要使用最新版的CentOS
  - 主要用于服务器版本

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122358.png" alt="img" style="zoom:50%;" />

- Debain（免费）
  - Ubuntu
    - 视窗界面良好的Linux系统
    - 一些主流的软件都支持Ubuntu系统

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122359.png" alt="image-20220215071146535" style="zoom:50%;" />

### Linux下载

- 官网：[https://linux.org/](https://linux.org/)
- 下载页面：[https://linux.org/pages/download/](https://linux.org/pages/download/)
- CentOS下载地址：[https://www.centos.org/download/](https://www.centos.org/download/)
- - **everything**：This image contains the complete set of packages for CentOS Linux. It can be used for installing or populating a local mirror.
  - **minimal**：The aim of this image is to install a very basic CentOS  system with the minimum of packages needed to have a functional system.
  - **netinstall**：This is the network install and rescue image.
- 操作系统的位数：优先选择64位,将来安装软件有更好兼容性

## 虚拟机安装与配置

### 虚拟化技术

- 可以更好的利用计算机闲置的资源
- 我们可以在计算机中虚拟出多台虚拟机帮助我们执行程序或者业务
- 虚拟机的各种组成理论上和真实主机是一样的

如果要使用这种技术只需要安装对应的软件即可

- Virtualbox
- VirtualMachine

安装软件：[https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)

- VMware Workstation Pro 16 许可证密钥

```
ZF3R0-FHED2-M80TY-8QYGC-NPKYF

YF390-0HF8P-M81RQ-2DXQE-M2UT6

ZF71R-DMX85-08DQY-8YMNC-PPHV8 
```

### 创建虚拟主机

![image-20220215091644544](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122360.png)

![image-20220215091700183](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122361.png)

![image-20220215091715061](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122362.png)

![image-20220215091726255](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122363.png)

![image-20220215091733768](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122364.png)

![image-20220215091740361](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122365.png)

![image-20220215091809839](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122366.png)

![image-20220215091834053](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122367.png)

![image-20220215091849528](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122368.png)

![image-20220215091901065](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122369.png)

![image-20220215091917072](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122370.png)

通过自定义硬件修改硬件参数

![image-20220215092014780](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122371.png)

### 安装操作系统

![image-20220215092142316](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122372.png)

默认即可，选英文版本，不然容易出现bug

![image-20220215092326988](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122373.png)



![image-20220215092439465](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122374.png)

![image-20220215092557328](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122375.png)

修改主机名称，便于找到

![image-20220215094127650](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122376.png)

然后点击开始安装，密码改为123456

![image-20220215094223636](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122377.png)

等待安装即可，然后重启。

![image-20220215094824153](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122378.png)

### 配置常见参数

#### 网络

- 在编辑-虚拟网络编辑器里设置vmnet8的参数，同时取消勾选DHCP服务

![image-20220215152623502](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122379.png)

* 在NAT设置里面，记录下子网IP、子网掩码、网关这三个参数

![image-20220215152727916](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122380.png)

* 打开宿主机控制面板-网络和Internet-网络连接，配置VMnet8的IPV4协议，要保证该网络的IP地址和刚才NAT设置里面的子网IP地址在同一网段

**这里要和虚拟机的IP在同一网段，不然主机没法ping通虚拟机**

![image-20220215153509652](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122381.png)

* 进入虚拟机内CentOS系统，编辑配置文件

```sh
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

* 修改`ifcfg-ens33`信息

**注意红框里面的配置，BOOTPROTO（网络参数配置）要设置为“static”（静态IP），ONBOOT（开机启动）设置为“yes”，IPADDR一定要保证在你设置的网络范围内，NETMASK为子网掩码，GATEWAY为网关，上述参数一定要和虚拟机内参数相同（IP地址要相符合）**

```
删除------UUID

--修改
ONBOOT=yes 
BOOTPROTO=static  //静态网络IP   dhcp 动态获取网络IP

--添加
IPADDR=192.168.183.102
NETMASK=255.255.255.0
GATEWAY=192.168.183.2
DNS1=114.114.114.114
```

![image-20220215154138310](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122382.png)

* 增加DNS服务器配置

```sh
vi /etc/resolv.conf
```

![image-20220215154033858](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122383.png)

* 重启网卡重新加载配置文件

```sh
service network restart 
```

* 用`ip addr`命令查看一下更改是否生效

```
ip addr
```

![image-20220215154345430](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122384.png)

* 使用ping命令，查看是否可以访问网络,`ctrl+c`停止该命令

```sh
ping www.baidu.com
```

#### 防火墙

防火墙适用于保护本机的端口不被别人访问的，如果端口需要被别人访问到，需要添加端口的防火墙。

* 查看防火墙状态

```sh
systemctl status firewalld
```

* 关闭防火墙

  * 本次开机状态下防火墙关闭（本次服务内禁用防火墙）

  ```sh
  systemctl stop firewalld 
  ```

  - 服务器重启后防火墙禁用（下次重启时，才禁用防火墙服务）

  ```sh
  systemctl disable firewalld
  ```

#### 软件安装限制

操作系统对未知软件的安装有可能拒绝或者警告，需要禁用这个功能。

```sh
vi /etc/selinux/config
```

![image-20220215155722186](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122385.png)

#### 关机命令

* 直接拔掉电源

```sh
halt
```

* 直接关闭机器，但是有可能当前虚拟机其他人在使用

```sh
poweroff
```

* 马上关闭计算机 ，但是可以给其他用户发送消息

```sh
shutdown -h now
```

* 重启虚拟机

```sh
reboot
```

### 快照与克隆

- 拍摄快照
  - 记录当前虚拟机的状态
  - 拍摄快照的时候一定要关机

- 转到
  - 回到某一个历史快照的节点

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122386.png" alt="image-20220215160157684" style="zoom: 80%;" />

- 克隆
  - 复制某一个历史快照节点
  - 克隆的方式
    - 链接克隆
      - 当前节点文件夹只存储差异性数据，不会克隆基础配置
      - 相同数据存放在原始节点上
      - 优点：节省硬盘空间 缺点：耦合性大
    - 完整克隆
      - 就是基于原始节点完全拷贝到新节点的文件夹中
      - 优点：耦合性抵 缺点：硬盘空间使用大
      - 推荐使用完整克隆

### 修改主机名称

* 修改后，重启生效

```sh
vi /etc/hostname
```

* 本次启动中有效，重启虚拟机还是使用原来的名字

```sh
hostname hutao
```

### 连接Linux服务器的工具

下载地址：https://filedown.me/Windows/Soft/

#### 使用Xshell

负责向虚拟机输入命令

![image-20220215100029136](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122387.png)

#### 使用XFtp

复制则向服务器传输文件

![image-20220215164445370](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122388.png)

## Linux命令

### Linux相关资源

```
https://pan.baidu.com/s/174VpfzRcFVi6AjU5CwVTcg 
提取码：imnj 
```

### 命令学习法

- Linux命令与参数之间必须用空格隔开
- Linux命令是区分大小写的
- 如果输入了错误的命令
  - `-bash: abcd: command not found`
  - 命令敲错了
  - 命令未安装
- type 命令的类型

```sh
type cd # 查看cd命令类型
```

- 命令的帮助文档

  - help：内置命令的帮助文档

  ```sh
  help cd # 查看cd命令文档
  ```

  - man

    - 外部命令的帮助文档
    - 如果 没有man包，需要手动安装man

    ```sh
    yum install man man-pages -y
    ```

  - 将来工作中如果遇到生疏的命令，直接百度

    - 如果不是为了装C,完全没必要查看命令手册

### 常用Linux命令

- whereis：查询命令文件的位置

```sh
whereis ping
```

- file：查看文件的类型

```sh
file /usr/bin/cd
```

- who：查看当前在线的用户

```sh
who
```

- whoami：当前用户名称

```sh
whoami
```

- pwd：查看当前所在目录

```sh
pwd
```

- uname -a： 查看内核信息

```sh
uname -a
```

- echo：类似于 sout syso ，打印语句

```sh
# 打印数据
echo 1

# 将数据存储到文件中
echo 1 > 1.txt
```

- clear：清屏

```sh
clear
```

- history：历史

```sh
# 查看当前所有历史命令
history

# 清空所有历史命令记录
history -c
```

> 上下键可以进行查看历史记录的命令

### 特殊字符

| 特殊字符   | 用途                                                         |
| ---------- | ------------------------------------------------------------ |
| ``.``点    | 如果文件的开始是``.``说明当前文件是一个隐藏文件，比如`.xxx` ；**1. ``.`` 指向当前目录； 2. ``..``指向当前目录的上级目录** |
| `$`        | 说明这是一个变量，比如`$PATH Linux`的环境变量                |
| ``*``星号  | 通配符，比如`ll -a /etc/*d`，表示查看该目录下所有以d结尾的文件 |
| ``~``      | 1. 当前用户的家目录；2. 每个用户的家目录是不同的； 3. root用户家目录在系统根目录下； 4. 其他用户的家目录在/home/用户名为家目录 |
| 空格       | Linux的命令与参数用空格隔开                                  |
| ``/``      | 整个Linux的文件根目录                                        |
| 命令的参数 | 1. 如果是单词 一般加`-- `；2. 如果是字母或者缩写一般加 ``-`` |

## 文件系统命令

### 概念

##### 文件系统

操作系统如何管理文件，内部定义了一些规则或者定义。

在Linux中所有的东西都是以文件的方式进行操作，而且文件的访问不和Window的一样。window依靠的是通过盘符进行访问；而Linux维护着一个树状结构的文件模型，它只有一个根节点 ,其名字叫做 ``/``，而一个节点上可以有多个子节点。

##### 查找文件的方式

* 相对路径：以当前路径为基准点，查找其他资源，比如

```sh
vi ../etc/sysconfig/network
```

* 绝对路径：以根目录为基准点，查找其他资源，比如

```sh
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

**日常使用中，只要找到路径即可，但是如果是一些配置文件，尽量写绝对路径**

##### 可以随意的挂载磁盘

为了防止内容不够，自行进行扩容。linux通过`mount`进行将扩容的硬盘进行挂载。

> 不要随意动二级目录。

```sh
mount /dev/disk1 /usr/download
disk1 1T

mount /dev/disk2 /usr/upload
disk2 100T

mount /dev/disk3 /usr/upload/photo
disk3 1P
```

### Linux文件目录

![image-20220216111002612](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122389.png)

`/bin`：bin是Binary的缩写, 这个目录存放着最经常使用的命令。

`/boot`：这里存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。

``/dev``：dev是Device(设备)的缩写, 该目录下存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。

``/etc``：这个目录用来存放所有的系统管理所需要的配置文件和子目录。

``/home``：用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。

``/lib``：这个目录里存放着系统最基本的动态连接共享库，其作用类似于Windows里的DLL文件。几乎所有的应用程序都需要用到这些共享库。

``/lost+found``：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。

``/media``：linux 系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂载到这个目录下。

``/mnt``：系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在/mnt/上，然后进入该目录就可以查看光驱里的内容了。

``/opt``：这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。

``/proc``：这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。这个目录的内容不在硬盘上而是在内存里，我们也可以直接修改里面的某些文件，比如可以通过下面的命令来屏蔽主机的ping命令，使别人无法ping你的机器：`echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all`

``/root``：该目录为系统管理员，也称作超级权限者的用户主目录。

``/run``：是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。如果你的系统上有 /var/run 目录，应该让它指向 run。

``/sbin``：s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。

``/selinux``：这个目录是Redhat/CentOS所特有的目录，Selinux是一个安全机制，类似于windows的防火墙，但是这套机制比较复杂，这个目录就是存放selinux相关的文件的。

``/srv``： 该目录存放一些服务启动之后需要提取的数据。

``/sys``：这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统 sysfs 。sysfs文件系统集成了下面3种文件系统的信息：**针对进程信息的proc文件系统、针对设备的devfs文件系统以及针对伪终端的devpts文件系统。**该文件系统是内核设备树的一个直观反映。当一个内核对象被创建的时候，对应的文件和目录也在内核对象子系统中被创建。

``/tmp``：这个目录是用来存放一些临时文件的。

``/var``：这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。

![image-20220216111159414](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161122390.png)

``/usr``：这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于windows下的program files目录。

``/usr/bin``：系统用户使用的应用程序。

``/usr/sbin``：超级用户使用的比较高级的管理程序和系统守护程序。

``/usr/src``：内核源代码默认的放置目录。

### Linux文件操作

- `cd`：改变当前工作目录

```sh
cd ../root # 以相对方式切换路径，首先先返回上一层路径，在当前路径下切换到root文件夹中

cd /etc    # 以绝对方式切换路径
```

- `ls`：显示出指定目录下所有的文件/文件夹

```sh
ll  # 等价于ls -l --color=auto，查看当前路径的下文件/文件夹

ll -a # 查看所有文件/文件夹，包括隐藏文件/文件夹

ll 文件/文件夹 # 查看当前文件/文件夹下的所有文件/文件夹
```

文件的类型：``-``普通文件；``d``文件夹；``l``软连接，只看开头第一个字母。

```sh
lrwxrwxrwx.  1 root root     11 Feb 15 17:42 init.d -> rc.d/init.d # 这是软连接
-rw-r--r--.  1 root root    511 Oct 13  2020 inittab               # 这是普通文件
drwxr-xr-x.  2 root root    159 Feb 15 17:42 iproute2              # 这是文件夹
```

- `mkdir`：创建文件目录

创建文件夹

```sh
cd ~      # 切换到root目录
mkdir aaa         # 创建文件夹
```

自动创建文件父目录

```sh
mkdir -p a/b/c/d  # 会自动创建文件父目录

mkdir -p ddd/{1,2,3,4} # 创建目录ddd以及其子目录1,2,3,4
```

- `rmdir`：删除空文件夹，因此可以用来安全的删除文件目录

```sh
rmdir aaa # 移除空的文件夹
```

如果不为空文件夹（比如`rmdir a`，a下面还有其他文件夹/文件），会出现以下报错：

```sh
rmdir: failed to remove 'a1': Directory not empty
rmdir: failed to remove 'baidu': Not a directory
```

- `cp`：拷贝文件或者文件目录夹。**如果当前目标目录存在，则将源文件/文件夹复制到目标目录中，否则会将源文件/文件夹改名进行复制。**

拷贝文件夹：`cp 源文件 目标目录`

```sh
cp abcd /opt    # 拷贝源文件abcd到/opt目录下

cp abcd /opt/aaa # 拷贝源文件abcd到/opt目录下，并改名为aaa
```

拷贝文件夹：`cp -r 源文件夹 目标目录`

```sh
cp -r aaa /opt  # 拷贝源文件夹aaa到/opt目录下

cp -r aaa /opt/bbb # 拷贝源文件abcd到/opt目录下，并改名为bbb
```

- `mv`：

移动文件或者文件夹：`mv 源文件/文件夹 目标目录`，**如果当前目标目录存在，则将源文件/文件夹移动到目标目录中，否则会将源文件/文件夹改名进行移动。**

```sh
mv aaa /opt
```

修改文件名称：`mv 源文件/文件夹 新文件/文件夹`

```sh
mv aaa bbb
```

- `rm`：

删除文件

```sh
rm xxx # 删除文件，需要提示，如果想删除文件，需要输入`y`
rm -f xxx # 强制删除文件
```

删除文件夹

```sh
rm -r xxx # 删除文件夹，需要提示，如果想删除文件，需要输入`y`
rm -rf xxx # 强制删除文件夹
```

> 注意`rm -rf /*` 这是一个死亡命令，请勿使用。

- `touch`：如果没有就创建一个文件；如果该文件已经存在，修改文件的三个时间，将三个时间改为当前时间，经常和`stat`配合使用。

```sh
touch xxx # 创建文件
```

- `stat`：查看文件的状态。如果该文件已经存在，修改文件的三个时间，将三个时间改为当前时间。

````sh
stat xxx
````

1. `Inode`：当前文件在文件系统的唯一标识，类似于ID
2. `Links`：硬链接数量
3. `Size`：文件大小；
4. `Uid`：文件所有者
5. `Access:(0644/-rw-r--r--)`：文件权限
6. `Access`：访问时间
7. `Modify`：修改文件内容时间
8. `Change`：修改文件元数据信息时间

![image-20220217145939312](../../../../../../Pictures/assets/Linux笔记/image-20220217145939312.png)

- `ln`：创建文件的链接（快捷方式）

便于测试，先创建文件并写入数据

```sh
[root@centos7 ~]# vi 1.log
[root@centos7 ~]# cat 1.log # 查看文件里面的内容
wo hen kai xin
```

**软（符号）连接**：软连接和原始文件不是同一个文件（软硬链接在链接文件的时候，推荐使用文件的绝对路径,否则有可能会出现问题）（指向文件逻辑地址，删除了源文件，就找不到对应的物理地址了）

```sh
ln -s 1.log test.log
```

**硬链接**：硬链接和原始文件使用文件系统中的同一个文件（如果你害怕一个文件被别人误删，你可以使用硬链接保护这个文件）（直接指向物理地址）

```sh
ln 1.log test2.log
```

如果现在删除`1.log`，软连接方式连接的文件被删除了，会导致软连接的创建的文件连接不到原来的文件的逻辑地址。硬链接的方式指向的是物理地址，因此不存在影响。

### 读取文件信息

* `cat`：将整个文档加载到内存中，并进行一次性显示（除非后面使用管道，传递数据）

```sh
cat xxx # 查看文件信息
```

- `tac`：将整个文档加载到内存中，并进行一次性按行逆序显示

```sh
tac xxx # 按行倒着加载信息
```

上面两个方法都是一次性加载到内存中，然后进行显示的。这样有很多弊端，可以通过分页进行显示。

- `more less`：分页查看文档内容
  - 快捷键
    - 回车 下一行
    - 空格 下一页
    - b 回退
    - q 退出

```sh
more xxx # 分页查看信息
less xxx # 与上面差别不大
more less xxx # 分页查询
```

- `head`：从文章开始读取N行（默认如果超过10行读取10行,否则读取现在行数）

```sh
head -5 xxx # 查看文件前5行信息
```

- `tail`：

从文章末尾读取N行

```sh
tail -3 xxx # 查看文件后3行信息
```

利用管道只读取第N行，管道的作用就相当于把前面的结果以参数的方式传递给后面的命令

```sh
head -3 profile | tail -1 # 只查看文件第3行信息
```

读取新增数据（监听数据变化）

1. 如果``f``（监听的是指定inode的文件）
   - 它会监听指定inode的文件数据变化，但是当文件被删除后
   - 即使创新创建，inode也会发生变化，于是监听失败
2. **如果``F``（监听的是指定名字的文件）**
   - 他会监听指定名字的文件,如果文件被删除后，重新创建
   - 他会重新监听新文件的数据变化，监听不受影响

```sh
ping www.baidu.com >>baidu # 类似日志
tail -F baidu # 动态查看文件信息
```

- find：查找指定的文件（find 要查找的范围 -name 名字）

```sh
find /etc -name profile
```

### VI和Vim便捷器

#### 打开文件

#### 三种模式

#### 三种模式切换

#### 编辑模式

#### 输入模式

#### 末行模式

### 计算机间的数据传输

#### windows到linux

#### linux到linux

### 文件大小

### 文件压缩

#### tar

#### zip和unzip

## 网络信息

### 主机名称

### DNS解析

### 网络相关命令

### 加密算法

#### 不可逆加密算法

#### 对称加密算法

#### 非对称加密算法

### 主机见的相互免密钥

### 主机名与Host校验

## 日期与时间

### 时间命令

### 日期自动同步

### 命令执行时间统计

## 用户组权限

### 用户

### 组

### 权限

### 权限赋予

## 管道与重定向

### 管道

### 重定向

## 系统进程

## 软件安装

## Linux的三剑客

## Shell编程

### Linux启动流程

### 系统启动流程

### 系统服务

### 开机自动服务

### 定时任务

### 虚拟机初始化脚本