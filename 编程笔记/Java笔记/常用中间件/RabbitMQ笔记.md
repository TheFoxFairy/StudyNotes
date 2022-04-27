# RabbitMQ笔记

## 初识RabbitMQ

### 中间件的相关概念

#### 什么是中间件

我国企业从20世纪80年代开始就逐渐进行信息化建设，由于方法和体系的不成熟，以及企业业务的市场需求的不断变化，一个企业可能同时运行着多个不同的业务系统，这些系统可能基于不同的操作系统、不同的数据库、异构的网络环境。现在的问题是，如何把这些信息系统结合成一个有机地协同工作的整体，真正实现企业跨平台、分布式应用。中间件便是解决之道，它用自己的复杂换取了企业应用的简单。

**中间件（Middleware）是处于操作系统和应用程序之间的软件**，也有人认为它应该属于操作系统中的一部分。人们在使用中间件时，往往是一组中间件集成在一起，构成一个平台（包括开发平台和运行平台），但在这组中间件中必须要有一个通信中间件，即中间件+平台+通信，这个定义也限定了只有用于分布式系统中才能称为中间件，同时还可以把它与支撑软件和使用软件区分开来。

#### 为什么需要使用消息中间件

具体地说，中间件屏蔽了底层操作系统的复杂性，使程序开发人员面对一个简单而统一的开发环境，减少程序设计的复杂性，将注意力集中在自己的业务上，不必再为程序在不同系统软件上的移植而重复工作，从而大大减少了技术上的负担，中间件带给应用系统的，不只是开发的简便、开发周期的缩短，也减少了系统的维护、运行和管理的工作量，还减少了计算机总体费用的投入。

#### 中间件特点

为解决分布异构问题，人们提出了中间件（middleware)的概念。中间件时位于平台（硬件和操作系统）和应用之间的通用服务，这些服务具有标准的程序接口和协议。针对不同的操作系统和硬件平台，它们可以有符合接口的协议规范的多种实现。

也很难给中间件一个严格的定义，但中间件应具有如下的一些特点：

1. 满足大量应用的需要
2. 运行于多种硬件和 OS平台
3. 支持分布计算，提供跨网络、硬件和 OS平台的透明性的应用或服务的交互
4. 支持标准的协议
5. 支持标准的接口

由于标准接口对于可移植性和标准协议对于互操作性的重要性，中间件已成为许多标准化工作的主要部分。对于应用软件开发，中间件远比操作系统和网络服务更为重要，中间件提供的程序接口定义了一个相对稳定的高层应用环境，不管底层的计算机硬件和系统软件怎样更新换代，只要将中间件升级更新，并保持中间件对外的接口定义不变，应用软件几乎不需任何修改，从而保护了企业在应用软件开发和维护中的重大投资。

简单说：中间件有个很大的特点，是脱离于具体设计目标，而具备提供普遍独立功能需求的模块。这使得中间件一定是可替换的。如果一个系统设计中，中间件是不可替代的，不是架构、框架设计有问题，那么就是这个中间件，在别处可能是个中间件，在这个系统内是引擎。

#### 在项目中什么时候使用中间件技术

在项目的架构和重构中，使用任何技术和架构的改变我们都需要谨慎斟酌和思考，因为任何技术的融入和变化都可能人员，技术，和成本的增加，中间件的技术一般现在一些互联网公司或者项目中使用比较多，如果你仅仅还只是一个初创公司建议还是使用单体架构，最多加个缓存中间件即可，不要盲目追求新或者所谓的高性能，而追求的背后一定是业务的驱动和项目的驱动，因为一旦追求就意味着你的学习成本，公司的人员结构以及服务器成本，维护和运维的成本都会增加，所以需要谨慎选择和考虑。

但是作为一个开放人员，一定要有学习中间件技术的能力和思维，否则很容易当项目发展到一个阶段在去掌握估计或者在面试中提及，就会给自己带来不小的困扰，在当今这个时代这些技术也并不是什么新鲜的东西，如果去掌握和挖掘最关键的还是自己花时间和经历去探讨和研究。

### MQ的相关概念

#### 什么是MQ

MQ(message queue)，从字面意思上看，本质是个队列，FIFO 先入先出，只不过队列中存放的内容是 message 而已，还是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，MQ 是一种非常常见的上下游“逻辑解耦+物理解耦”的消息通信服务。使用了 MQ 之后，消息发送上游只需要依赖MQ，不用依赖其他服务。

#### 为什么要用MQ?

- **流量消峰**

举个例子，如果订单系统最多能处理一万次订单，这个处理能力应付正常时段的下单时绰绰有余，正常时段我们下单一秒后就能返回结果。但是在高峰期，如果有两万次下单操作系统是处理不了的，只能限制订单超过一万后不允许用户下单。使用消息队列做缓冲，我们可以取消这个限制，把一秒内下的订单分散成一段时间来处理，这时有些用户可能在下单十几秒后才能收到下单成功的操作，但是比不能下单的体验要好。

- **应用解耦**

以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合调用库存系统、物流系统、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在这几分钟的时间里，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成。当物流系统恢复后，继续处理订单信息即可，中单用户感受不到物流系统的故障，提升系统的可用性。

![RabbitMQ-00000004](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349997.png)

- **异步处理**

有些服务间调用是异步的，例如 A 调用 B，B 需要花费很长时间执行，但是 A 需要知道 B 什么时候可以执行完。

以前一般有两种方式，A 过一段时间去调用 B 的查询 api 查询。或者 A 提供一个 callback api， B 执行完之后调用 api 通知 A 服务。这两种方式都不是很优雅。

使用消息总线，可以很方便解决这个问题， A 调用 B 服务后，只需要监听 B 处理完成的消息，当 B 处理完成后，会发送一条消息给 MQ，MQ 会将此消息转发给 A 服务。这样 A 服务既不用循环调用 B 的查询 api，也不用提供 callback api。同样B 服务也不用做这些操作。A 服务还能及时的得到异步处理成功的消息。

#### MQ的分类

##### ActiveMQ

优点：单机吞吐量万级，时效性 ms 级，可用性高，基于主从架构实现高可用性，消息可靠性较低的概率丢失数据。

缺点：官方社区现在对 ActiveMQ 5.x 维护越来越少，高吞吐量场景较少使用。

##### Kafka

大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开 Kafka，这款为**大数据而生**的消息中间件，以其百万级 TPS 的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥着举足轻重的作用。目前已经被LinkedIn，Uber, Twitter, Netflix 等大公司所采纳。

**优点**：性能卓越，单机写入 TPS 约在百万条/秒，最大的优点，就是**吞吐量高**。时效性 ms 级可用性非常高，kafka 是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用，消费者采用Pull方式获取消息，消息有序，通过控制能够保证所有消息被消费且仅被消费一次；有优秀的第三方Kafka Web 管理界面 Kafka-Manager；在日志领域比较成熟，被多家公司和多个开源项目使用；功能支持：功能较为简单，主要支持简单的 MQ 功能，在大数据领域的实时计算以及日志采集被大规模使用。

**缺点**：Kafka 单机超过64 个队列/分区，Load 会发生明显的飙高现象，队列越多，load 越高，发送消息响应时间变长，使用短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，**社区更新较慢**；

##### RocketMQ

RocketMQ 出自阿里巴巴的开源产品，用 Java 语言实现，在设计时参考了 Kafka，并做出了自己的一些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，binglog 分发等场景。

优点：**单机吞吐量十万级**，可用性非常高，分布式架构，**消息可以做到0 丢失**,MQ 功能较为完善，还是分布式的，扩展性好，支**持10亿级别的消息堆积**，不会因为堆积导致性能下降，源码是java我们可以自己阅读源码，定制自己公司的 MQ。

缺点：**支持的客户端语言不多**，目前是 java 及 c++，其中 c++不成熟；社区活跃度一般，没有在MQ 核心中去实现 JMS 等接口，有些系统要迁移需要修改大量代码

##### RabbitMQ

2007 年发布，是一个在AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，**是当前最主流的消息中间件之一。**

优点：由于 erlang 语言的高并发特性，性能较好；吞吐量到万级，MQ 功能比较完备，健壮、稳定、易用、跨平台、支持多种语言如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP 等，支持 AJAX 文档齐全；开源提供的管理界面非常棒，用起来很好用，社区活跃度高；更新频率相当高。

官网更新：[https://www.rabbitmq.com/news.html(opens new window)](https://www.rabbitmq.com/news.html)

缺点：商业版需要收费，学习成本较高

#### MQ 的选择

- **Kafka**

Kafka 主要特点是基于Pull 的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。大型公司建议可以选用，如果有日志采集功能，肯定是首选kafka 了。

- **RocketMQ**

天生为**金融互联网**领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。RoketMQ 在稳定性上可能更值得信赖，这些业务场景在阿里双11已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择 RocketMQ。

- RabbitMQ

结合 erlang 语言本身的并发优势，性能好时效性微秒级，社区活跃度也比较高，管理界面用起来十分方便，如果你的**数据量没有那么大**，中小型公司优先选择功能比较完备的RabbitMQ。

### RabbitMQ的相关概念

#### RabbitMQ概述

官网：[https://www.rabbitmq.com/#features](https://www.rabbitmq.com/#features)

RabbitMQ 是一个消息中间件：它接受并转发消息。

你可以把它当做一个快递站点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑 RabbitMQ 是一个快递站，一个快递员帮你传递快件。

RabbitMQ 与快递站的主要区别在于，它不处理快件而是**接收，存储和转发消息数据**。

> 消息队列就是一个使用队列来通信的组件。

![image-20210625230930992](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349999.png)

#### 四大核心概念

- **生产者**：产生数据发送消息的程序是生产者
- **交换机**：交换机是 RabbitMQ 非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定
- **队列**：队列是 RabbitMQ 内部使用的一种数据结构，尽管消息流经 RabbitMQ 和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的**消息缓冲区**。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式
- **消费者**：消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。

#### 各个名词介绍

![RabbitMQ-00000007](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349000.png)

- **Broker**：接收和分发消息的应用，RabbitMQ Server 就是 Message Broker
- **Virtual host**：出于多租户和安全因素设计的，把 AMQP 的基本组件划分到一个虚拟的分组中，类似于网络中的 namespace 概念。当多个不同的用户使用同一个 RabbitMQ server 提供的服务时，可以划分出多个 vhost，每个用户在自己的vhost 创建 exchange／queue 等
- **Connection**：publisher／consumer 和 broker 之间的 TCP 连接
- **Channel**：如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立TCP Connection的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread 创建单独的 channel 进行通讯，AMQP method 包含了 channel id 帮助客户端和 message broker 识别 channel，所以 channel 之间是完全隔离的。**Channel 作为轻量级的 Connection 极大减少了操作系统建立 TCP connection 的开销**。
- **Exchange**：message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，分发消息到 queue 中去。常用的类型有：direct (point-to-point)， topic (publish-subscribe) and fanout (multicast)
- **Queue**：消息最终被送到这里等待 consumer 取走
- **Binding**：exchange 和 queue 之间的虚拟连接，binding 中可以包含 routing key，Binding 信息被保存到 exchange 中的查询表中，用于 message 的分发依据

#### RabbitMQ特点

- 可靠性: RabbitMQ 使用一些机制来保证可靠性，如持久化、传输确认及发布确认等。

- 灵活的路由：在消息进入队列之前，通过交换器来路由消息。对于典型的路由功能， RabbitMQ 己经提供了一些内置的交换器来实现。针对更复杂的路由功能，可以将多个交换器绑定在一起，也可以通过插件机制来实现自己的交换器。

- 扩展性：多个 RabbitMQ 节点可以组成一个集群，也可以根据实际业务情况动态地扩展集群中节点。

- 高可用性：队列可以在集群中的机器上设置镜像，使得在部分节点出现问题的情况下队列仍然可用。

- 多种协议: RabbitMQ 除了原生支持 AMQP 协议，还支持 STOMP， MQTT 等多种消息中间件协议。

- 多语言客户端:RabbitMQ 几乎支持所有常用语言，比如 Java、 Python、 Ruby、 PHP、 C#、 JavaScript 等。

- 管理界面: RabbitMQ 提供了一个易用的用户界面，使得用户可以监控和管理消息、集群中的节点等。

- 插件机制: RabbitMQ 提供了许多插件，以实现从多方面进行扩展，当然也可以编写自己的插件。

### AMQP的相关概念

#### AMQP 是什么？

RabbitMQ 就是 AMQP 协议的 Erlang 的实现(当然 RabbitMQ 还支持 STOMP2、 MQTT3 等协议) AMQP 的模型架构和 RabbitMQ 的模型架构是一样的，生产者将消息发送给交换器，交换器和队列绑定。

RabbitMQ 中的交换器、交换器类型、队列、绑定、路由键等都是遵循的AMQP协议中相应的概念。目前RabbitMQ 最新版本默认支持的是AMQP 0-9-1。

#### AMQP 协议3 层？

- **Module Layer**：协议最高层，主要定义了一些客户端调用的命令，客户端可以用这些命令实现自己的业务逻辑。
- **Session Layer:**中间层，主要负责客户端命令发送给服务器，再将服务端应答返回客户端，提供可靠性同步机制和错误处理。
- **Transport Layer**：最底层，主要传输二进制数据流，提供帧的处理、信道服用、错误检测和数据表示等。

#### AMQP 模型的几大组件？

- 交换器(Exchange)：消息代理服务器中用于把消息路由到队列的组件。
- 队列(Queue)：用来存储消息的数据结构，位于硬盘或内存中。
- 绑定(Binding)：一套规则，告知交换器消息应该将消息投递给哪个队列。

### RabbitMQ安装

#### Linux安装

##### 下载

官网下载地址：[https://www.rabbitmq.com/download.html(opens new window)](https://www.rabbitmq.com/download.html)

这里我们选择的版本号（注意这两版本要求）

- rabbitmq-server-3.8.8-1.el7.noarch.rpm

  GitHub：[https://github.com/rabbitmq/rabbitmq-server/releases/tag/v3.8.8(opens new window)](https://github.com/rabbitmq/rabbitmq-server/releases/tag/v3.8.8)

  加载下载：[https://packagecloud.io/rabbitmq/rabbitmq-server/packages/el/7/rabbitmq-server-3.8.8-1.el7.noarch.rpm(opens new window)](https://packagecloud.io/rabbitmq/rabbitmq-server/packages/el/7/rabbitmq-server-3.8.8-1.el7.noarch.rpm)

- erlang-21.3.8.21-1.el7.x86_64.rpm

  官网：https://www.erlang-solutions.com/downloads/

  加速：[https://packagecloud.io/rabbitmq/erlang/packages/el/7/erlang-21.3.8.21-1.el7.x86_64.rpm(opens new window)](https://packagecloud.io/rabbitmq/erlang/packages/el/7/erlang-21.3.8.21-1.el7.x86_64.rpm)

Red Hat 8, CentOS 8 和 modern Fedora 版本，把“el7”替换成“el8”

![image-20220414221848167](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349001.png)

##### 安装

```sh
sudo rpm -ivh erlang-21.3.8.21-1.el7.x86_64.rpm
sudo yum install socat -y
sudo rpm -ivh rabbitmq-server-3.8.8-1.el7.noarch.rpm
```

##### 启动

```sh
#启动服务
systemctl start rabbitmq-server

#查看服务状态
systemctl status rabbitmq-server

#开机自启动
systemctl enable rabbitmq-server

#停止服务
systemctl stop rabbitmq-server

#重启服务
systemctl restart rabbitmq-server
```

##### 配置文件

配置文件地址：https://github.com/rabbitmq/rabbitmq-server/tree/v3.8.x/deps/rabbit/docs

在`/etc/rabbitmq`中配置advanced.config和rabbitmq.conf两个文件。

#### Web管理界面

##### 安装

默认情况下，是没有安装web端的客户端插件，需要安装才可以生效

```sh
sudo rabbitmq-plugins enable rabbitmq_management
```

安装完毕以后，重启服务即可

```sh
sudo systemctl restart rabbitmq-server
```

访问 http://192.168.183.102:15672 ，用默认账号密码(都是guest)登录，出现权限问题。

默认情况只能在 localhost 本机下访问，所以需要添加一个远程登录的用户

> 通过ip addr或者ifconfig查看当前服务器地址，注意还需要关闭防火墙`systemctl stop firewaalld`

![image-20220414222839776](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349002.png)

##### 添加用户

###### 创建账号

```sh
sudo rabbitmqctl add_user admin 123456
```

###### 设置用户角色

```sh
sudo rabbitmqctl set_user_tags admin administrator
```

用户级别：

1. **administrator**：可以登录控制台、查看所有信息、可以对 rabbitmq 进行管理
2. **monitoring**：监控者登录控制台，查看所有信息
3. **policymaker**：策略制定者登录控制台，指定策略
4. **managment**：普通管理员登录控制台

###### 设置用户权限

```sh
set_permissions [-p <vhostpath>]<user> <conf> <write> <read>
```

比如：用户admin具有/vhost1这个virtual host中所有资源的配置、写、读权限

```sh
sudo rabbitmqctl set_permissions -p "/" admin ".*"".*"".*"
```

###### 当前用户和角色

```sh
sudo rabbitmqctl list_users
```

![image-20220414223851935](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349003.png)

###### 再次登录，用 admin 用户

- 关闭应用的命令为：``rabbitmqctl stop_app``
- 清除的命令为：``rabbitmqctl reset``
- 重新启动命令为：``rabbitmqctl start_app``

访问地址：http://192.168.183.102:15672/#/

![image-20220414224443532](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349004.png)

#### Docker安装

官网：https://registry.hub.docker.com/_/rabbitmq/

```sh
docker run -id --name myrabbit -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=123456 -p 15672:15672 rabbitmq:3-management
```

## Hello World

我们将用 Java 编写两个程序。发送单个消息的生产者和接收消息并打印出来的消费者

在下图中，“ P”是我们的生产者，“ C”是我们的消费者。中间的框是一个队列 RabbitMQ 代表使用者保留的消息缓冲区。

![RabbitMQ-00000012](https://cdn.jsdelivr.net/gh/oddfar/static/img/RabbitMQ/RabbitMQ-00000012.png)

连接的时候，需要开启5672 端口

![image-20210626162052259](https://cdn.jsdelivr.net/gh/oddfar/static/img/RabbitMQ/image-20210626162052259.png)

接下来进行开始编写代码。

* 创建工程项目`rabbitmq-hello`
* 添加相关依赖

```xml
<!--指定 jdk 编译版本-->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>8</source>
                <target>8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
<dependencies>
    <!--rabbitmq 依赖客户端-->
    <dependency>
        <groupId>com.rabbitmq</groupId>
        <artifactId>amqp-client</artifactId>
        <version>5.8.0</version>
    </dependency>
    <!--操作文件流的一个依赖-->
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.6</version>
    </dependency>
</dependencies>

```

* 然后编写一个**消息生产者**进行发送信息

```java
public class Producer {

    //队列名称
    public static final String QUEUE_NAME = "hello";

    //发消息
    public static void main(String[] args) throws IOException, TimeoutException {

        //创建一个工厂连接
        ConnectionFactory factory = new ConnectionFactory();
        //然后工厂通过IP连接到RabbitMQ队列
        factory.setHost("192.168.183.102"); //服务host
        factory.setUsername("admin"); //RabbitMQ 管理用户名
        factory.setPassword("123456"); //用户密码

        //创建连接
        Connection connection = factory.newConnection();

        //获取信道
        Channel channel = connection.createChannel();

        //生成一个队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);

        //发消息
        String message = "hello world";

        //发送一个消息
        channel.basicPublish("",QUEUE_NAME,null,message.getBytes());

        System.out.println("消息发送完毕！！！");
    }

}
```

`channel.queueDeclare(QUEUE_NAME,false,false,false,null)`的参数含义：

1. 队列名称
2. 队列里面的消息是否持久化，也就是是否用完就删除
3. 该队列是否只供一个消费者进行消费是否进行共享 true 可以多个消费者消费
4. 是否自动删除最后一个消费者端开连接以后，该队列是否自动删除 true 自动删除
5. 其他参数

``channel.basicPublish("",QUEUE_NAME,null,message.getBytes())``的参数含义：

1. 发送到那个交换机
2. 路由的 key 是哪个
3. 其他的参数信息
4. 发送消息的消息体

之后运行**消息生产者**，观察RabbitMQ的管理界面，可以看到已经有了一个消息队列。

![image-20220414232633602](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349005.png)

* 编写一个**消息消费者**进行接收信息

```java
//与Producer类似，但是用来接收信息的
public class Consumer {
    //队列的名称
    public static final String QUEUE_NAME = "hello";

    //接收信息
    public static void main(String[] args) throws IOException, TimeoutException {
        //创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.183.102");
        factory.setUsername("admin");
        factory.setPassword("123456");

        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        //声明，lambda表达式，有个简单的方法，就是找到接口方法，然后粘贴复制过来....
        //接收消息时的回调
        DeliverCallback deliverCallback = (consumerTag, message)-> {
            System.out.println(new String(message.getBody()));
        };
        //取消消息时的回调
        CancelCallback cancelCallback = message -> {
            System.out.println("消费消息被中断");
        };

        //消费者消费消息
        channel.basicConsume(QUEUE_NAME, true, deliverCallback,cancelCallback);
    }

}
```

``channel.basicConsume(QUEUE_NAME, true, deliverCallback,cancelCallback)``其中参数的含义：

1. 消费哪个队列
2. 消费成功之后是否要自动应答 true 代表自动应答 false 手动应答
3. 消费者未成功消费的回调
4. 消息被取消时的回调

> 在写lambda表示时，有个简单的方法，就是找到接口方法，然后粘贴复制过来....

## Work Queues

### 什么是工作队列

工作队列：用来将耗时的任务分发给多个消费者（工作者）

主要解决问题：**处理资源密集型任务**，并且还要等他完成。有了工作队列，我们就可以将具体的工作放到后面去做，将工作封装为一个消息，发送到队列中，一个工作进程就可以取出消息并完成工作。如果启动了多个工作进程，那么工作就可以在多个进程间共享。

工作队列也称为**公平性队列模式**，怎么个说法呢？

循环分发，假如我们拥有两个消费者，默认情况下，RabbitMQ 将按顺序将每条消息发送给下一个消费者，平均而言，每个消费者将获得相同数量的消息，这种分发消息的方式称为轮询。

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349006.jpg)

### 轮训分发消息

#### 抽取工具类

```java
public class RabbitMqUtils {

    //获取channel
    public static Channel getChannel(String host,String username,String password) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost(host);
        factory.setUsername(username);
        factory.setPassword(password);

        Connection connection = factory.newConnection();

        return connection.createChannel();
    }

}
```

#### 启动两个工作线程

创建两个工作队列`Worker`，使用多线程开启来个两个工作队列。

```java
public class Worker implements Runnable{

    final static String QUEUE_NAME = "hello";
    final static String HOST = "192.168.183.102";
    final static String USERNAME = "admin";
    final static String PASSWORD = "123456";

    public void work() throws IOException, TimeoutException {

        System.out.println(Thread.currentThread().getName()+"已经启动");

        //获取信道
        Channel channel = RabbitMqUtils.getChannel(HOST, USERNAME, PASSWORD);

        DeliverCallback deliverCallback = (consumerTag, message)->{
            System.out.println(Thread.currentThread().getName()+"接收到的消息："+new String(message.getBody()));
        };
        CancelCallback cancelCallback = consumerTag -> {
            System.out.println(consumerTag +"消费者取消消费接口回调逻辑");
        };

        //消息接收
        channel.basicConsume(QUEUE_NAME,deliverCallback, cancelCallback);
    }

    @Override
    public void run(){
        try {
            this.work();
        } catch (IOException e){
            e.printStackTrace();
        } catch (TimeoutException e){
            e.printStackTrace();
        }
    }

    //使用多线程进行测试
    public static void main(String[] args) throws IOException, TimeoutException {
        Worker worker = new Worker();
        new Thread(worker).start();
        new Thread(worker).start();
    }

}
```

![image-20220415002319742](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349007.png)

#### 启动发送一个线程

```java
public class Task {
    final static String QUEUE_NAME = "hello";
    final static String HOST = "192.168.183.102";
    final static String USERNAME = "admin";
    final static String PASSWORD = "123456";

    public static void main(String[] args) throws IOException, TimeoutException {
        System.out.println(Thread.currentThread().getName()+"已经启动");

        //获取信道
        Channel channel = RabbitMqUtils.getChannel(HOST, USERNAME, PASSWORD);
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);

        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            String message = scanner.next();
            channel.basicPublish(""， QUEUE_NAME, null, message.getBytes());
            System.out.println("消息发送完成："+ message);
        }

    }
}
```

![image-20220415002328980](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349008.png)

#### 测试

然后开始在Task任务窗口输入数据

![image-20220415002353173](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349009.png)

然后观察Worker的窗口，发现两个工作队列在轮训接收队列的消息。

![image-20220415002431949](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349010.png)

### 消息应答

#### 概念

消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分突然它挂掉了，会发生什么情况。RabbitMQ 一旦向消费者传递了一条消息，便立即将该消息标记为删除。在这种情况下，突然有个消费者挂掉了，我们将丢失正在处理的消息。以及后续发送给该消费这的消息，因为它无法接收到。

为了保证消息在发送过程中不丢失，引入消息应答机制，消息应答就是：**消费者在接收到消息并且处理该消息之后，告诉rabbitmq它已经处理了，rabbitmq可以把该消息删除了。**

#### 自动应答

消息发送后立即被认为已经传送成功，这种模式需要在**高吞吐量和数据传输安全性方面做权衡**，因为这种模式如果消息在接收到之前，消费者那边出现连接或者 channel 关闭，那么消息就丢失了，当然另一方面这种模式消费者那边可以传递过载的消息，**没有对传递的消息数量进行限制**，当然这样有可能使得消费者这边由于接收太多还来不及处理的消息，导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死，**所以这种模式仅适用在消费者可以高效并以某种速率能够处理这些消息的情况下使用。**

#### 手动消息应答的方法

- Channel.basicAck(用于肯定确认)：RabbitMQ 已知道该消息并且成功的处理消息，可以将其丢弃了
- Channel.basicNack(用于否定确认)
- Channel.basicReject(用于否定确认)：与Channel.basicNack相比少一个参数，不处理该消息了直接拒绝，可以将其丢弃了

**Multiple（批量处理）的解释：**手动应答的好处是可以批量应答并且减少网络拥堵

![RabbitMQ-00000017](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349011.png)

- true 代表批量应答channel上未应答的消息：比如说 channel 上有传送 tag 的消息5,6,7,8当前tag是8 那么此时5-8 的这些还未应答的消息都会被确认收到消息应答
- false 同上面相比只会应答 tag=8 的消息5,6,7 这三个消息依然不会被确认收到消息应答

![RabbitMQ-00000018](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349012.png)

> 虽然批量处理快，但是可能会造成消息丢失的问题。因此一般最好设置为false

#### 消息自动重新入队

如果消费者由于某些原因失去连接(其通道已关闭，连接已关闭或 TCP 连接丢失)，导致消息未发送 ACK 确认，RabbitMQ 将了解到消息未完全处理，并将对其重新排队。如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。

![RabbitMQ-00000019](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349013.png)

#### 消息手动应答代码

默认消息采用的是自动应答，所以我们要想实现消息消费过程中不丢失，需要把自动应答改为手动应答

新建两个消费者`Worker1`以及`Worker2`在`Worker`代码的基础上增加了以下内容，然后两个队列的处理任务时间分别设置为1s/30s，表示一个处理速度非常快，一个非常慢：

```java
public class Worker1{

    final static String TASK_QUEUE_NAME = "ack_queue";
    final static String HOST = "192.168.183.102";
    final static String USERNAME = "admin";
    final static String PASSWORD = "123456";

    public void work(int seconds) throws IOException, TimeoutException {

        System.out.println("Worker1 已经启动");

        //获取信道
        Channel channel = RabbitMqUtils.getChannel(HOST, USERNAME, PASSWORD);

        DeliverCallback deliverCallback = (consumerTag, delivery)->{
            //模拟任务处理时间
            try {
                TimeUnit.SECONDS.sleep(seconds);
            } catch (InterruptedException e){
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()+"接收到的消息："+new String(delivery.getBody()));
            // ack
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        };
        CancelCallback cancelCallback = consumerTag -> {
            System.out.println(consumerTag +"消费者取消消费接口回调逻辑");
        };

        //手动应答
        boolean autoAck = false;

        //消息接收
        channel.basicConsume(TASK_QUEUE_NAME,autoAck,deliverCallback, cancelCallback);
    }

    public static void main(String[] args) throws IOException, TimeoutException {
        new Worker1().work(10);
    }

}
```

``channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false)``的参数含义：

1. 第一位参数表示消息标记tag
2. 第二位参数false代表只应答接收到的那个传递的消息；true为应答所有消息包括传递过来的消息。

`channel.basicConsume(QUEUE_NAME,autoAck,deliverCallback, cancelCallback)`：第二位参数表示是否自动应答。

之后新建一个``Task1``消息生产者：

```java
//消息生产者，消息在手动应答时是不丢失的，放回队列重新消费
public class Task1 {
    final static String TASK_QUEUE_NAME = "ack_queue";
    final static String HOST = "192.168.183.102";
    final static String USERNAME = "admin";
    final static String PASSWORD = "123456";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel(HOST,USERNAME,PASSWORD);

        channel.queueDeclare(TASK_QUEUE_NAME,false,false,false,null);

        Scanner scanner = new Scanner(System.in);
        System.out.println("请输入信息：");
        while (scanner.hasNext()){
            String message = scanner.nextLine();
            channel.basicPublish("",TASK_QUEUE_NAME,null,message.getBytes(StandardCharsets.UTF_8));
            System.out.println("生产者发出消息"+ message);
        }

    }
}
```

#### 手动应答效果演示

现在，启动生产者，再启动两个工作队列，然后进行发出消息，观察两个工作队列的情况。

正常情况下消息发送方发送两个消息 C1 和 C2 分别接收到消息并进行处理。

![RabbitMQ-00000021](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349014.png)

在生产者发出消息后，可以在web界面看到当前正在排队处理的任务，因为C2处理任务慢，任务正在排队处理中。

![RabbitMQ-00000023](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349015.png)

在发送者发送消息 dd，发出消息之后的把 C2 消费者停掉，按理说该 C2 来处理该消息，但是由于它处理时间较长，在还未处理完，也就是说 C2 还没有执行 ack 代码的时候，C2 被停掉了，此时会看到消息被 C1 接收到了，说明消息 dd 被重新入队，然后分配给能处理消息的 C1 处理了。

![RabbitMQ-00000024](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204151349016.png)

### RabbitMQ持久化

#### 概念

当 RabbitMQ 服务停掉以后，消息生产者发送过来的消息不丢失要如何保障？默认情况下 RabbitMQ 退出或由于某种原因崩溃时，它忽视队列和消息，除非告知它不要这样做。确保消息不会丢失需要做两件事：**我们需要将队列和消息都标记为持久化。**

#### 队列如何实现持久化

之前我们创建的队列都是非持久化的，rabbitmq 如果重启的化，该队列就会被删除掉，如果要队列实现持久化需要在声明队列的时候把 durable 参数设置为持久化。

```java
// 让队列持久化
boolean durable = true;
// 声明队列
channel.queueDeclare(QUEUE_NAME,durable,false,false,null);
```

注意：如果之前声明的队列不是持久化的，需要把原先队列先删除，或者重新创建一个持久化的队列，不然就会出现错误。

![RabbitMQ-00000026](../../../../../../../Pictures/assets/RabbitMQ笔记/RabbitMQ-00000026.png)

以下为控制台中持久化与非持久化队列的 UI 显示区：

![RabbitMQ-00000027](../../../../../../../Pictures/assets/RabbitMQ笔记/RabbitMQ-00000027.png)

其中D字母表示已经持久化了。

#### 消息实现持久化

需要在消息**生产者**修改代码，`MessageProperties.PERSISTENT_TEXT_PLAIN` 添加这个属性。

```java
// 设置生产者消息为持久化消息（要求保存在磁盘上）保存在内存中
channel.basicPublish("",TASK_QUEUE_NAME,MessageProperties.PERSISTENT_TEXT_PLAIN,message.getBytes(StandardCharsets.UTF_8));
```

将消息标记为持久化并不能完全保证不会丢失消息。尽管它告诉 RabbitMQ 将消息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候 但是还没有存储完，消息还在缓存的一个间隔点。此时并没 有真正写入磁盘。持久性保证并不强，但是对于我们的简单任务队列而言，这已经绰绰有余了。

> 在发布确认中，会学习更强有力的持久化策略。

#### 不公平分发

在最开始的时候我们学习到 RabbitMQ 分发消息采用的轮训分发，但是在某种场景下这种策略并不是很好，比方说有两个消费者在处理任务，其中有个**消费者 1** 处理任务的速度非常快，而另外一个**消费者 2** 处理速度却很慢，这个时候我们还是采用轮训分发的化就会到这处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活，这种分配方式在这种情况下其实就不太好，但是 RabbitMQ 并不知道这种情况它依然很公平的进行分发。

为了避免这种情况，**在消费者中消费之前**，我们可以设置参数 `channel.basicQos(1);`

```java
// 不公平分发
int prefetchCount = 1;
channel.basicQos(prefetchCount); // 这个表示队列中能够存放处理的数量。2就是提前存放两条数据，等着排队处理。
// 采用手动应答
boolean autoAck = false;
// 消息接收
channel.basicConsume(TASK_QUEUE_NAME, autoAck, deliverCallback, cancelCallback);
```

![RabbitMQ-00000030](../../../../../../../Pictures/assets/RabbitMQ笔记/RabbitMQ-00000030.png)

![RabbitMQ-00000031](../../../../../../../Pictures/assets/RabbitMQ笔记/RabbitMQ-00000031.png)



意思就是如果这个任务我还没有处理完或者我还没有应答你，你先别分配给我，我目前只能处理一个 任务，然后 rabbitmq 就会把该任务分配给没有那么忙的那个空闲消费者，当然如果所有的消费者都没有完 成手上任务，队列还在不停的添加新任务，队列有可能就会遇到队列被撑满的情况，这个时候就只能添加 新的 worker 或者改变其他存储任务的策略。

### 预取值分发(带权的消息分发)

本身消息的发送就是异步发送的，所以在任何时候，channel 上肯定不止只有一个消息另外来自消费 者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，因此希望开发人员能**限制此缓冲区的大小**，**以避免缓冲区里面无限制的未确认消息问题**。这个时候就可以通过使用 basic.qos 方法设 置“预取计数”值来完成的。

该值定义通道上允许的未确认消息的最大数量。一旦数量达到配置的数量， RabbitMQ 将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认，例如，假设在通道上有未确认的消息 5、6、7，8，并且通道的预取计数设置为 4，此时RabbitMQ 将不会在该通道上再传递任何消息，除非至少有一个未应答的消息被 ack。比方说 tag=6 这个消息刚刚被确认 ACK，RabbitMQ 将会感知 这个情况到并再发送一条消息。消息应答和 QoS 预取值对用户吞吐量有重大影响。

通常，增加预取将提高 向消费者传递消息的速度。**虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的 RAM 消耗**(随机存取存储器)应该小心使用具有无限预处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的 内存消耗变大，所以找到合适的预取值是一个反复试验的过程，不同的负载该值取值也不同 100 到 300 范 围内的值通常可提供最佳的吞吐量，并且不会给消费者带来太大的风险。

预取值为 1 是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境 中。对于大多数应用来说，稍微高一点的值将是最佳的。

![RabbitMQ-00000032](../../../../../../../Pictures/assets/RabbitMQ笔记/RabbitMQ-00000032.png)

## 发布确认                           

### 发布确认原理

生产者将信道设置成 confirm 模式，一旦信道进入 confirm 模式，所有在该信道上面发布的消息都将会被指派一个唯一的 ID(从 1 开始)，一旦消息被投递到所有匹配的队列之后，broker 就会发送一个确认给生产者(包含消息的唯一 ID)，这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，broker 回传给生产者的确认消息中 delivery-tag 域包含了确认消息的序列号，此外 broker 也可以设置basic.ack 的 multiple 域，表示到这个序列号之前的所有消息都已经得到了处理。

confirm 模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果RabbitMQ 因为自身内部错误导致消息丢失，就会发送一条 nack 消息， 生产者应用程序同样可以在回调方法中处理该 nack 消息。

### 发布确认的策略

#### 开启发布确认的方法

* 开启队列持久化

```java
// 1. 队列持久化
boolean durable = true;
channel.queueDeclare(TASK_QUEUE_NAME,durable,false,false,null);
```

- 开启消息持久化

```java
// 2. 设置生产者消息为持久化消息（要求保存在磁盘上）保存在内存中
channel.basicPublish("",TASK_QUEUE_NAME,MessageProperties.PERSISTENT_TEXT_PLAIN,message.getBytes(StandardCharsets.UTF_8));
```

- 开启发布确认

发布确认默认是没有开启的，如果要开启需要调用方法 confirmSelect，每当你要想使用发布确认，都需要在 channel 上调用该方法。

```java
// 3. 开启发布确
channel.confirmSelect();
```

#### 单个确认发布

这是一种简单的确认方式，它是一种**同步确认发布**的方式，也就是发布一个消息之后只有它被确认发布，后续的消息才能继续发布，`waitForConfirmsOrDie(long)` 这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常。

这种确认方式有一个最大的缺点就是：**发布速度特别的慢**，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能已经足够了。

```java
// 1. 单个确认
public static void publishMessageIndividually() throws Exception {
    String queueName = UUID.randomUUID().toString();
    // 队列的声明
    Channel channel = RabbitMqUtils.getChannel();
    channel.queueDeclare(queueName,true,false,false,null);

    // 开启发布确认
    channel.confirmSelect();

    // 开始时间
    long beginTime = System.currentTimeMillis();

    // 批量发送消息
    for(int i=0;i<MEESAGE_COUNT;i++){

        String message = String.valueOf(i);

        channel.basicPublish("",queueName,null,message.getBytes(StandardCharsets.UTF_8));

        // 单个消费就马上进行发布确认
        boolean flag = channel.waitForConfirms();
        if(flag){
            System.out.println("消息发送成功");
        }

    }

    // 结束时间
    long endTime = System.currentTimeMillis();

    System.out.println("发布"+MEESAGE_COUNT+"个单独确认消息，耗时"+(endTime - beginTime)+"ms");

}
```

#### 批量确认发布

上面那种方式非常慢，与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量，当然这种方式的缺点就是：当发生故障导致发布出现问题时，不知道是哪个消息出 问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布。

```java
// 2. 批量确认
public static void publishMessageBatch() throws Exception{
    String queueName = UUID.randomUUID().toString();

    Channel channel = RabbitMqUtils.getChannel();
    channel.queueDeclare(queueName,false,false,false,null);

    channel.confirmSelect();

    long beginTime = System.currentTimeMillis();

    // 批量确认消息大小
    int batchSize = 100;

    for(int i = 0;i<MEESAGE_COUNT;i++){
        String message = String.valueOf(i);
        channel.basicPublish("",queueName,null,message.getBytes(StandardCharsets.UTF_8));

        // 判断达到100条消息的时候，批量确认一次
        if((i+1) % batchSize == 0){
            // 发布确认
            channel.waitForConfirms();
        }

    }

    long endTime = System.currentTimeMillis();
    System.out.println("发布"+MEESAGE_COUNT+"个批量确认消息，耗时"+(endTime - beginTime)+"ms");
}
```

#### 异步确认发布

异步确认虽然编程逻辑比上两个要复杂，但是性价比最高，无论是可靠性还是效率都没得说， 他是利用回调函数来达到消息可靠性传递的，这个中间件也是通过函数回调来保证是否投递成功， 下面就让我们来详细讲解异步确认是怎么实现的。

![RabbitMQ-00000034](../../../../../../../Pictures/assets/RabbitMQ笔记/RabbitMQ-00000034.png)

如何处理异步未确认消息?

最好的解决的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列， 比如说用 ConcurrentLinkedQueue 这个队列在 confirm callbacks 与发布线程之间进行消息的传递。

```java
// 3. 异步批量确认
public static void publishMessageAsync() throws Exception{
    String queueName = UUID.randomUUID().toString();

    Channel channel = RabbitMqUtils.getChannel();
    channel.queueDeclare(queueName,false,false,false,null);

    channel.confirmSelect();

    long beginTime = System.currentTimeMillis();


    ConfirmCallback confirmCallback1 =(delivery, multiple)->{ // 消息确认成功
        System.out.println("确认的消息："+delivery);
    };
    ConfirmCallback confirmCallback2 =(delivery, multiple)->{ // 消息确认失败
        System.out.println("未确认的消息："+delivery);
    };
    // 开启监听 -- 准备消息的监听器，监听那些消息成功了，哪些消息失败了
    channel.addConfirmListener(confirmCallback1,confirmCallback2); //异步通知

    for(int i = 0;i<MEESAGE_COUNT;i++){
        String message = String.valueOf("消息"+i);
        channel.basicPublish("",queueName,null,message.getBytes(StandardCharsets.UTF_8));
    }

    long endTime = System.currentTimeMillis();
    System.out.println("发布"+MEESAGE_COUNT+"个异步确认消息，耗时"+(endTime - beginTime)+"ms");
}
```

如何处理异步未确认消息?

最好的解决的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列， 比如说用 ConcurrentLinkedQueue 这个队列在 confirm callbacks 与发布线程之间进行消息的传递。

####  3 种发布确认速度对比 

- 单独发布消息

  同步等待确认，简单，但吞吐量非常有限。

- 批量发布消息

  批量同步等待确认，简单，合理的吞吐量，一旦出现问题但很难推断出是那条消息出现了问题。

- 异步处理

  最佳性能和资源使用，在出现错误的情况下可以很好地控制，但是实现起来稍微难些

## 交换机

### Exchanges

#### Exchanges概念

#### Exchanges类型

#### 无名Exchanges

### 临时队列

### 绑定(bindings)

### Fanout

#### Fanout介绍

#### Fanout实战

### Direct Exchange

### Topics

## 死信队列

### 死信的概念

### 死信的来源

### 死信实战

## 延迟队列

## 发布确认高级

## 幂等性

## 优先级队列

## 惰性队列

## RabbitMQ集群
