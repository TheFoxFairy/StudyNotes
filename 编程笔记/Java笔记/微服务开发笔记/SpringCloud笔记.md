# SpringCloud笔记

## 前言

### 内容

SpringCloud + SpringCloud alibaba

### 前提技术要求

java8+maven+git+github+nginx+RabbitMQ+SpringBoot2.0

## 微服务架构零基础理论入门

### 微服务架构概述

#### 什么是微服务

微服务是将完整系统的各个模块拆分成一个个独立的服务模块，服务之间可以相互调用。

#### 微服务的优缺点

优点：

- 单个服务代码量少，易于维护；
- 单个微服务可独立部署和运行；
- 进程独立，可以动态升级；
- 多个相同的微服务可以做负载均衡，提高性能和可靠性；

缺点：

- 虽然代码量少，但系统复杂度的总量是不变的；
- 每一个微服务需要一个团队维护，小公司玩不起…

#### 什么是分布式微服务架构

分布式微服务架构：服务注册与发现、服务调用、服务熔断、负载均衡、服务降级、服务消息队列、配置中心管理、服务网关、服务监控、全链路追踪、自动化构建部署、服务定时任务调度操作。

![image-20220408112828193](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551152.png)

#### SpringCloud与微服务的关系

SpringCloud是分布式微服务架构的**一站式解决方案**，是多种微服务架构落地技术的**集合体**，俗称微服务全家桶。

SpringCloud官网：https://spring.io/

#### SpringCloud集成相关优质项目推荐

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551154.png)

#### 大厂案例

##### 京东

![京东.png](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551155.png)

##### 阿里

![阿里.png](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551156.png)

##### 京东物流

![s76Kw8.png](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551157.png)

#### 基础服务

![image-20220408135827288](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551158.png)

#### SpringCloud技术栈

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551159.png)

## 从2.2.x和H版开始说起

### 环境配置

| 工具         | 版本          |
| ------------ | ------------- |
| Cloud        | Hoxton.SR1    |
| Boot         | 2.2.2.RELEASE |
| CloudAlibaba | 2.1.0.RELEASE |
| Java         | Java8         |
| Maven        | 3.5及以上     |
| Mysql        | 5.7及以上     |

这里是对照这视频的版本对应，可以自己去在[官网里面进行查找对应版本](https://docs.spring.io/spring-cloud/docs/Hoxton.SR12/reference/html/)。

### SpringCloud版本（升级至2.0及以上）

SpringBoot 官网：https://spring.io/projects/spring-boot

源码：https://github.com/spring-projects/spring-boot/releases/

- GA：当前最稳定版本
- Pre-release：预发布版本

### SpringCloud 版本选择

官网：[https://spring.io/projects/spring-cloud](https://spring.io/projects/spring-cloud#learn)

源码：https://github.com/spring-projects/spring-cloud

### Cloud与Boot的对应的依赖关系

推荐：Cloud官网LEARN选项中查看版本后的Reference Doc。

或者：overview选项下翻查看表格选择，如下。

| Release Train       | Boot Version                     |
| ------------------- | -------------------------------- |
| 2020.0.x aka Ilford | 2.4.x                            |
| Hoxton              | 2.2.x, 2.3.x (Starting with SR5) |
| Greenwich           | 2.1.x                            |
| Finchley            | 2.0.x                            |

更详细版本对应关系：使用JSON工具查看[JSON串结果](https://start.spring.io/actuator/info)。

## 关于Cloud各种组件的停更/升级/替换

| 服务注册中心                                | 服务调用                                                 | 服务调用2 | 服务降级                            | 服务网关            | 服务配置 | 服务总线 |
| ------------------------------------------- | -------------------------------------------------------- | --------- | ----------------------------------- | ------------------- | -------- | -------- |
| ✖ Eureka（停更，要学）                      | ✔ Ribbon（正在使用，但已停更，未来将被LoadBalancer替换） | ✖ Feign   | ✖ Hystrix（停更，国内大规模使用中） | ✖ Zuul              | ✖ Config | ✖ Bus    |
| ✔ Zookeeper                                 | ✔ LoadBalancer（还没成熟）                               | OpenFeign | ✔ resilience4j（国外使用）          | ？ Zuul2 （还没出） | ✔ Nacos  | ✔ Nacos  |
| ✔ Consul                                    | -                                                        | -         | ✔ rentienl（阿里的，国内使用）      | ✔ gateway           | apollo   | -        |
| ✔ Nacos（阿里的，重点推荐，完美替换Eureka） | -                                                        | -         | -                                   | -                   | -        |          |

* ✖为老技术，基本上已停更，但很多公司还在用。

- ✔为老技术停更后的替代方法。
- 新老技术都会讲解，因此课程量很大。

参考资料：
 [SpringCloud官网文档](https://cloud.spring.io/spring-cloud-static/Hoxton.SR1/reference/htmlsingle/)、[SpringCloud中文文档](https://www.bookstack.cn/read/spring-cloud-docs/docs-index.md)、 [SpringBoot官方文档](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/)

## 微服务架构编码构建

### 微服务的规矩

**约定>配置>编码**

### IDEA新建project工作空间

#### 微服务cloud整体聚合父工程Project

* 新建Project

![image-20220408144323185](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551160.png)

![image-20220408144852932](../../../../../../../Pictures/assets/Untitled/202204081551161.png)

**之后删除src文件夹。**

* 字符编码

![image-20220408145220072](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551162.png)

* 注解生效激活

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551163.png" alt="image-20220408145319343" style="zoom:67%;" />

* Java编译版本选8

![image-20220408145405444](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551164.png)

* File Type过滤

![image-20220408150455105](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551165.png)

#### 父工程pom文件

* 在pom.xml中添加`<packaging>pom</packaging>`标签，表示这个pom是个总的父工程，如下：

```xml
<version>1.0-SNAPSHOT</version>
<packaging>pom</packaging>
```

* 统一管理Jar包版本

```xml
<!-- 统一管理Jar包版本 -->
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <junit.version>4.12</junit.version>
    <log4j.version>1.2.17</log4j.version>
    <lombok.version>1.16.18</lombok.version>
    <mysql.version>8.0.15</mysql.version>
    <druid.version>1.1.16</druid.version>
    <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
</properties>
```

* 子模块继承之后，提供作用：``锁定版本+子module`不用写groupId和version

```xml
<dependencyManagement>
    <dependencies>
        <!--spring boot 2.2.2-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.2.2.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--spring cloud Hoxton.SR1-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Hoxton.SR1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--spring cloud 阿里巴巴-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.1.0.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
            <!--      <scope>runtime</scope>-->
        </dependency>
        <!-- druid-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${druid.version}</version>
        </dependency>
        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>${mybatis.spring.boot.version}</version>
        </dependency>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit.version}</version>
        </dependency>
        <!--log4j-->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <!--解决maven项目中 无法打包生成空文件夹的问题-->
        <dependency>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-project-info-reports-plugin</artifactId>
            <version>3.0.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.3.7.RELEASE</version>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### Maven工程落地细节

##### DependencyManagement与Dependencies

Maven使用dependencyManagement元素来提供了一种管理依赖版本号的方式。

**通常会在一个组织或者项目的最顶层的父POM中看到dependencyManagement元素。**

使用pom.xml中的dependencyManagement元素能让所有在子项目中引用一个依赖而不用显式的列出版本号。

Maven会沿着父子层次向上走，直到找到一个拥有dependencyManagement元素的项目，然后它就会使用这个dependencyManagement元素中指定的版本号。

比如：

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.15</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

然后在子项目里就可以添加mysql-connector时可以不指定版本号 **（如果指定了就优先用子项目的版本号）**，例如：

```xml
<dependencies>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
</dependencies>
```

这样做的好处就是：如果有多个子项目都引用同一样依赖，则可以避免在每个使用的子项目里都声明一个版本号，这样当想升级或切换到另一个版本时，只需要在顶层父容器里更新，而不需要一个一个子项目的修改；另外如果某个子项目需要另外的一个版本，只需要声明version就可。

- dependencyManagement里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。
- 如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom
- 如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。

##### Maven中如何跳过单元测试

为什么要跳过单元测试：节约时间

这上面有一个闪电的标记，点击后，test测试会变成不可用。

![image-20220408152505302](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551166.png)

#### 将父工程发布到仓库

父工程创建完成执行`mvn:install`将父工程发布到仓库方便子工程继承。

测试一下发布：

![image-20220408153649641](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551167.png)

然后清除：

![image-20220408153724328](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204081551168.png)

### Rest微服务工程构建

#### 构建步骤

##### 支付模块构建

###### 新建module

![image-20220408161059292](../../../../../../../Pictures/assets/Untitled/image-20220408161059292.png)

###### 修改pom

将pom文件的modules放在packing下面。

![image-20220408161338584](../../../../../../../Pictures/assets/Untitled/image-20220408161338584.png)

导入依赖，子模块中有需要的在父工程中有的话，直接引入，不需要写版本号。

```xml
<dependencies>
    <!--spring boot 2.2.2-->
    <!--图形化监控展现-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--mybatis-->
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <!-- druid-->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.10</version>
    </dependency>
    <!--mysql-connector-java-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <!--jdbc-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

* 写yml文件

在`src/main/resources`下创建`application.yml`文件。

```yaml
server:
  port: 8001 # 端口号

spring:
  application:
    name: cloud-payment-service # 应用名称
  datasource: # 数据库
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2020?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities # 所有Entity别名类所在包
```

* 主启动

创建`com.atguigu.springcloud.PaymentMain8001`

![image-20220408163103024](../../../../../../../Pictures/assets/Untitled/image-20220408163103024.png)

手动设置主启动

```java
@SpringBootApplication
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class,args);
    }
}
```

###### 业务类

1. **[创建数据库](./工具类/db2020.md)**

2. 实体类

主实体**Payment.java**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Payment implements Serializable {

    private Long id;
    private String serial;

}
```

由于给前端不显示业务类，只需要传给前端是否成功的信息。

Json封装体**CommonResult.java**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CommonResult <T>{

    private Integer code;
    private String message;
    private T data;

    public CommonResult(Integer code,String message){
        this(code,message,null);
    }

}
```

###### 创建dao层

**PaymentDao.java**接口

```java
@Mapper // 注册Dao层
public interface PaymentDao {
    public int create(Payment payment);
    public Payment getPaymentById(@Param("id") Long id);
}
```

**mybatis的映射文件PaymentMapper.xml**

路径一般设置在`resources/mapper`下，以便于后续方便修改。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- 命名空间一般设置dao接口，声明该xml文件属于那个接口的 -->
<mapper namespace="com.atguigu.springcloud.dao.PaymentDao">

    <insert id="create" parameterType="Payment" useGeneratedKeys="true" keyProperty="id">
        insert into payment(serial) values (#{serial});
    </insert>


    <resultMap id="BaseResultMap" type="Payment">
        <id column="id" property="id" jdbcType="BIGINT" />
        <id column="serial" property="serial" jdbcType="VARCHAR" />
    </resultMap>
    <select id="getPaymentById" parameterType="Long" resultMap="BaseResultMap">
        select * from payment where id = #{id};
    </select>

</mapper>
```

最好使用resultMap进行封装数据后返回使用，因为可能类里面的属性名与数据库的列名名字不同。

###### 创建service层

**PaymentService.java**接口

```java
public interface PaymentService {
    public int create(Payment payment);
    public Payment getPaymentById(@Param("id") Long id);
}
```

**创建业务实体类PaymentServiceImpl.java**

```java
@Service
public class PaymentServiceImpl implements PaymentService{

    @Resource
    private PaymentDao paymentDao;

    @Override
    public int create(Payment payment) {
        return paymentDao.create(payment);
    }

    @Override
    public Payment getPaymentById(Long id) {
        return paymentDao.getPaymentById(id);
    }

}
```

**创建控制类PaymentController.java**

````java
@RestController
@Slf4j // 打印日志
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @PostMapping("/payment/create")
    public CommonResult create(Payment payment){

        int result = paymentService.create(payment);
        log.info("插入结果:"+result);

        int code = 404;
        String message = null;

        if(result > 0){
            code = 200;
            message = "插入成功";
        }else {
            code = 444;
            message = "插入数据库失败";
        }

        return new CommonResult(code,message,result);
    }

    @GetMapping("/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id){

        Payment payment = paymentService.getPaymentById(id);
        log.info("查询结果:"+payment);

        int code = 404;
        String message = null;

        if(payment != null ){
            code = 200;
            message = "查询成功";
        }else {
            code = 444;
            message = "查询数据库失败";
        }

        return new CommonResult(code,message,payment);
    }
}
````

###### 小结

1. 构造方法注解化的实现使用了插件lombok，要上线的项目还是全部敲出来较好
2. 前后端分离，返回给前端一个json形式的封装类
3. dao接口推荐使用@Mapper注解而不是@Repository，因为@Repository插入时可能会有问题
4. 返回结果集建议使用映射resultMap，可以避免大小写、下划线等错误。（如果开启Mybatis的驼峰匹配，其实也无所谓）

###### 测试

将没用过的模块（eureka）先注释了，然后重新加载依赖即可。然后点击主启动开启服务。

查询数据：http://localhost:8001/payment/get/31。

![image-20220408224346418](../../../../../../../Pictures/assets/Untitled/image-20220408224346418.png)

插入数据：http://localhost:8001/payment/create

![image-20220408224740851](../../../../../../../Pictures/assets/Untitled/image-20220408224740851.png)

##### 热部署Devtools

热部署：开启后，项目在已部署状态下，每次代码改动后无需手动重新部署，可以实时更新。付费插件`JRebel`也可实现热部署。

> 比较耗内存

* 添加DevTools到项目中

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```

* 添加maven插件到父工程的`pom.xml`中（如果报错就加上版本号）

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.3.7.RELEASE</version>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

* 开启IDEA的自动编译

![image-20220408230234350](../../../../../../../Pictures/assets/Untitled/image-20220408230234350.png)

* 重启IDEA

**注：开发阶段可用热部署，生产环境中必须关闭**

##### 消费者订单模块



##### 工程重构

#### 目前工程样图

## Eureka服务注册与发现

## Zookeeper服务注册与发现

## Consul服务注册与发现

## Ribbon负载均衡服务调用

## OpenFeign服务接口调用

## Hystrix断路器

## Zuul路由网关

## Gateway新一代网关

## SpringCloud Config分布式配置中心

## SpringCloud Bus消息总线

## SpringCloud Stream消息驱动

## SpringCloud Sleuth分布式请求链路追踪

## SpringCloud Alibaba入门简介

## SpringCloud Alibaba Nacos服务注册与配置中心

## SpringCloud Alibaba Sentinel实现熔断与限流

## SpringCloud Alibaba Seata处理分布式事务