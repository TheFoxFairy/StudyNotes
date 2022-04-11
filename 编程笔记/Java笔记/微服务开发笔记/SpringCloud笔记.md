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

![image-20220408144852932](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345187.png)

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

![image-20220408161059292](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345189.png)

###### 修改父pom

将pom文件的modules放在packing下面。

![image-20220408161338584](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345190.png)

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
    url: jdbc:mysql://localhost:3306/db2020?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities # 所有Entity别名类所在包
```

* 主启动

创建`com.atguigu.springcloud.PaymentMain8001`

![image-20220408163103024](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345191.png)

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

![image-20220408224346418](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345192.png)

插入数据：http://localhost:8001/payment/create

![image-20220408224740851](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345193.png)

如果报错，就如下输入

![image-20220411111140777](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117509.png)

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

![image-20220408230234350](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345194.png)

* 重启IDEA

**注：开发阶段可用热部署，生产环境中必须关闭**

##### 消费者订单模块

###### 新建module

![image-20220409110744924](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345195.png)

###### 修改pom

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
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
</dependencies>
```

###### 修改yaml

浏览器**默认为80端口**，所以客户端使用80端口可以方便用户。
如百度`baidu.com:80`，我们直接输入`baidu.com`就可以了

###### 业务类

* entites

与支付模块一样：`Payment`与`CommonResult`

* RestTemplate

RestTemplate提供了多种便捷访问远程Http服务的方法，是一种简单便捷的访问restful服务模板类，是Spring提供的用于访问Rest服务的**客户端模板工具集**

官网文档地址：https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/web/client/RestTemplate.html

使用：`(url,requestMap,ResponseBean.class)`这三个参数分别代表`REST请求地址`、`请求参数`、`HTTP响应转换被转换成的对象类型`。

* config配置类：RestTemplate的依赖注入配置

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }

}
```

* controller

```java
@RestController
@Slf4j
public class OrderController {

    public static final String PAYMENT_URL = "http://localhost:8001";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment){

        return restTemplate.postForObject(PAYMENT_URL+"/payment/create",payment,CommonResult.class);

    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id){
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);
    }
    
}
```

###### 测试

同时启动`cloud-consumer-order80`与`cloud-provider-payment8001`两个子工程项目。

查询地址：http://127.0.0.1/consumer/payment/get/31

![image-20220409133208238](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345196.png)

插入地址：http://127.0.0.1/consumer/payment/create?serial=111

不要忘记@RequestBody，**`@RequestBody`主要用来接收前端传递给后端的json字符串中的数据的(请求体中的数据)；** GET方式无请求体，所以使用@RequestBody接收数据时，前端不能使用GET方式提交数据，而是用POST方式进行提交。

`cloud-provider-payment8001`下的`create`方法进行修改：

```java
@PostMapping("/payment/create")
public CommonResult create(@RequestBody Payment payment){

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
```

如下：

![image-20220409134753424](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345197.png)

##### 工程重构

###### 观察问题

系统中有重复问题，重构

###### 新建module

![image-20220409135943101](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345198.png)

###### 重写pom

```xml
<dependencies>
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
    <!--工具包，如时间日期格式-->
    <dependency>
        <groupId>cn.hutool</groupId>
        <artifactId>hutool-all</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
```

###### entities

将两个子工程的entities复制到`cloud-api-commons`中。

![image-20220409142056280](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345199.png)

###### maven命令

清除和重新安装依赖库。

###### 改造两个子工程项目

* 删除原来的entities
* 然后引入相关依赖

```xml
<dependency>
    <groupId>com.atguigu.springcloud</groupId>
    <artifactId>cloud-api-commons</artifactId>
    <version>${project.version}</version>
</dependency>
```

* 重新启动测试

## 服务注册与发现

### 什么是服务治理？

在传统的rpc远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。

### 什么是服务注册与发现

**服务注册**：服务进程在注册中心注册自己的元数据信息。 通常包括主机和端口号，有时还有身份验证信息，协议，版本号，以及运行环境的信息。

**服务发现**：客户端服务进程向注册中心发起查询，来获取服务的信息。 服务发现的一个重要作用就是提供给客户端一个可用的服务列表。

在服务注册与发现中，有一个注册中心。当服务器启动的时候，会把当前自己服务器的信息比如服务地址通讯地址等以别名方式注册到注册中心上。另一方(消费者|服务提供者)，以该别名的方式去注册中心上获取到实际的服务通讯地址，然后再实现本地NRPC调用RPC远程调用框架核心设计思想：在于注册中心，因为使用注册中心管理每个服务与服务之间的一个依赖关系(服务治理概念)。在任何rpc远程框架中，都会有一个注册中心(存放服务地址相关信息(接口地址))

### Eureka

#### Eureka简述

##### 什么是Eureka？

[Eureka](https://github.com/Netflix/Eureka) 是 [Netflix](https://github.com/Netflix) 开发的，一个基于 REST 服务的，服务注册与发现的组件，以实现中间层服务器的负载平衡和故障转移。

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345200)

Eureka采用了CS的设计架构，Eureka Server作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用Eureka的值客户端连接到Eurek a sever并推持心跳连接。这样系统的维护人员就可以通过Eureka Server 来监控系统中各个微服务是否正常运行。 

##### Eureka两大组件

它主要包括两个组件：Eureka Server 和 Eureka Client

- **Eureka Server**：提供服务注册与发现（通常就是微服务中的注册中心）

各个微服务节点通过配置启动后，会在EurekaServer中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到。

- **Eureka Client**：通过注册中心进行访问

它是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳(默认周期为30秒)。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除(默认90秒)。

#### 单机Eureka构建步骤

##### eurekaServer端服务注册中心（类似物业公司）

###### 新建module

![image-20220409150556845](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345201.png)

###### 改写pom

```xml
<dependencies>
    <!--eureka-server-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
    <!--自定义api通用包-->
    <dependency>
        <groupId>com.atguigu.springcloud</groupId>
        <artifactId>cloud-api-commons</artifactId>
        <version>${project.version}</version>
    </dependency>
    <!--boot web acctuator-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
</dependencies>
```

###### 写yaml

```yaml
server:
  port: 7001

eureka:
  instance:
    hostname: localhost # eureka服务端的实例名称
  client:
    register-with-eureka: false # false表示不能向注册中心注册自己
    fetch-registry: false # false表示自己就是注册中心，其职责就是维护服务实例，并不需要去检索服务
    service-url:
      # 设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

###### 主启动

```java
@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class,args);
    }
}
```

###### 测试   

Eureka地址：http://127.0.0.1:7001/

![image-20220409152353793](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345202.png)

##### 服务提供者

找到`cloud-provider-payment8001`进行改写，注册成为服务提供者。

###### 改pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

###### 写yaml

```yaml
server:
  port: 8001 # 端口号

spring:
  application:
    name: cloud-payment-service # 应用名称
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2020?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    type: com.alibaba.druid.pool.DruidDataSource # 自定义数据源

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities # 所有Entity别名类所在包


eureka:
  client:
    register-with-eureka: true # 表示是否将自己注册进EurekaServer，默认为true
    fetch-registry: true # 是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用均衡负载
    service-url:
    # 这里地址一定是注册中心的地址
      defaultZone: http://localhost:7001/eureka/
```

###### 主启动

```java
@SpringBootApplication
@EnableEurekaClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class,args);
    }
}
```

###### 测试

启动服务提供者和注册中心。访问地址：http://127.0.0.1:7001/

![image-20220409162409956](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345203.png)

##### 服务消费者

找到`cloud-consumer-order80`进行改写，注册成为服务提供者。

###### 改pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

###### 写yaml

```yaml
server:
  port: 80

spring:
  application:
    name: cloud-consumer-order # 应用名称

eureka:
  client:
    register-with-eureka: true # 表示是否将自己注册进EurekaServer，默认为true
    fetch-registry: true # 是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用均衡负载
    service-url:
      # 这里地址一定是注册中心的地址
      defaultZone: http://localhost:7001/eureka/
```

###### 主启动

```java
@SpringBootApplication
@EnableEurekaClient
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class,args);
    }
}
```

###### 测试

启动服务消费者，然后刷新地址。访问地址：http://127.0.0.1:7001/

![image-20220409163134328](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345204.png)

#### 集群Eureka构建步骤

##### Eureka集群原理

**服务注册**：将服务信息注册到注册中心

**服务发现**：从注册中心获取服务信息

**实质**：存key服务名，取value调用地址

**步骤**：

1. 先启动eureka注册中心

2. 启动服务提供者payment支付服务

3. 支付服务启动后，会把自身信息注册到eureka

4. 消费者order服务在需要调用接口时，使用服务别名去注册中心获取实际的远程调用地址

5. 消费者获得调用地址后，底层实际是调用httpclient技术实现远程调用

6. 消费者获得服务地址后会缓存在本地jvm中，默认每30秒更新异常服务调用地址

**问题：**微服务RPC远程调用最核心的是说明？

高可用，如果注册中心只有一个，出现故障就麻烦了o(*￣︶￣*)o。会导致整个服务环境不可用。

**解决办法**：搭建eureka注册中心集群，实现**负载均衡+故障容错**

> 互相注册，相互守望

##### EurekaServer集群环境构建步骤

###### 新建module

参考``cloud-eureka-server7001``新建一个``clourd-eureka-server7002``。

###### 修改映射配置

找到当前系统的hosts文件，进行修改。windows中hosts在`C:\Windows\System32\drivers\etc`，添加如下：

```tex
127.0.0.1 eureka7001.com
127.0.0.1 eureka7002.com
```

###### 修改yml

`cloud-eureka-server7001\...\application.yml`

```yaml
server:
  port: 7001

eureka:
  instance:
    hostname: eureka7001.com # eureka服务端的实例名称
  client:
    register-with-eureka: false # false表示不能向注册中心注册自己
    fetch-registry: false # false表示自己就是注册中心，其职责就是维护服务实例，并不需要去检索服务
    service-url:
      # 设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7002.com:7002/eureka/
     
spring:
  freemarker:
    prefer-file-system-access: false
```

`cloud-eureka-server7002\...\application.yml`

```yaml
server:
  port: 7002

eureka:
  instance:
    hostname: eureka7001.com
  client:
    fetch-registry: false
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
      
spring:
  freemarker:
    prefer-file-system-access: false
```

注意，hostname不能为同一个名字，因此配合上面的hosts，进行改写。然后两个注册中心之间进行相互注册，在`defaultZone`处相互写注册的地址。

如果想继续添加多个集群，在`defaultZone=http://node1:10001/eureka/,http://node2:10002/eureka/`处，类似的注册其他的地址即可。

![image-20220409202702559](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345205.png)

##### 将支付8001微服务发布到上面2台Eureka集群配置中

注册中心集群搭建好后，只需要修改yml即可。对`cloud-provider-payment8001`下的application.yml进行修改。

```yaml
eureka:
  client:
    register-with-eureka: true # 表示是否将自己注册进EurekaServer，默认为true
    fetch-registry: true # 是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用均衡负载
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
```

##### 将订单服务80微服务发布到上面2台Eureka集群配置中

同理，对`cloud-consumer-order80`下的application.yml进行修改。

```yaml
defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
```

##### 测试

* 先启动EurekaServer，7001/7002服务
* 再启动服务提供者provider，8001
* 最后启动消费者consumer，80

![ ](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345206.png)

![image-20220409203540774](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345207.png)

访问地址：http://localhost/consumer/payment/get/31

![image-20220409203406527](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345208.png)

##### 支付服务提供者8001集群环境构建

###### 新建module

参考`cloud-provider-payment8001`，新建一个`cloud-provider-payment8002`。

###### 写yml

注意修改端口号

````yaml
server:
  port: 8002 # 端口号

spring:
  application:
    name: cloud-payment-service # 应用名称
  datasource: # 数据库
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2020?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities # 所有Entity别名类所在包

eureka:
  client:
    register-with-eureka: true # 表示是否将自己注册进EurekaServer，默认为true
    fetch-registry: true # 是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用均衡负载
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7001.com:7002/eureka/
````

###### 修改8001/8002的Controller

```java
@RestController
@Slf4j // 打印日志
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @Value("${server.port}")
    private String serverPort;

    @PostMapping("/payment/create")
    public CommonResult create(Payment payment){

        int result = paymentService.create(payment);
        log.info("插入结果:"+result);

        int code = 404;
        String message = "当前端口号:"+serverPort;

        if(result > 0){
            code = 200;
            message += "插入成功";
        }else {
            code = 444;
            message += "插入数据库失败";
        }

        return new CommonResult(code,message,result);
    }

    @GetMapping("/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id){

        Payment payment = paymentService.getPaymentById(id);
        log.info("查询结果:"+payment);

        int code = 404;
        String message = "当前端口号:"+serverPort;

        if(payment != null ){
            code = 200;
            message += "查询成功";
        }else {
            code = 444;
            message += "查询数据库失败";
        }

        return new CommonResult(code,message,payment);
    }
}
```

![image-20220409205407356](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345209.png)

###### 修改Order80的Controller

由于Order80的端口是写死的，会一直只访问一个提供者。

单机版写成这样没问题：

```java
public static final String PAYMENT_URL = "http://localhost:8001";
```

但是在多集群上，不能这样。通过在eureka上注册过的微服务名称调用。

```java
public static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE";
```

重新访问，http://localhost/consumer/payment/get/31，出现如下错误：

![image-20220410112829556](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345210.png)

因为不知道是那一台机器，导致无法找到。可以通过负载均衡解决。

###### 负载均衡

修改Order80的config类，使用``@LoadBalanced``注解赋予RestTemplate负载均衡的能力。

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced // 使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }

}
```

重新访问：http://localhost/consumer/payment/get/31

![image-20220410113224897](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345211.png)

![image-20220410113232253](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345212.png)

#### actuator微服务信息完善

##### 主机名称与服务名称的修改

修改`cloud-provider-payment8001`以及`cloud-provider-payment8002`的yaml文件，在eureka下添加instance-id。

```yaml
eureka:
  client:
    register-with-eureka: true # 表示是否将自己注册进EurekaServer，默认为true
    fetch-registry: true # 是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用均衡负载
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7001.com:7002/eureka/
  instance:
    instance-id: payment8001
```

![image-20220410114143788](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345213.png)

##### 显示访问者的IP名称

修改`cloud-provider-payment8001`以及`cloud-provider-payment8002`的yaml文件，在eureka下添加prefer-ip-adress用于显示访问者ip。

```yaml
eureka:
  client:
    register-with-eureka: true # 表示是否将自己注册进EurekaServer，默认为true
    fetch-registry: true # 是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用均衡负载
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7001.com:7002/eureka/
  instance:
    instance-id: payment8001
    prefer-ip-address: true
```

![image-20220410114559368](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345214.png)

#### 服务发现Discovery

对于注册进eureka里面的微服务，可以通过服务发现来获得该服务的信息。

需要修改`cloud-provider-payment8001`以及``cloud-provider-payment8002``的controller。

```java
import org.springframework.cloud.client.discovery.DiscoveryClient;

@Resource
private DiscoveryClient discoveryClient;

@GetMapping("/payment/discovery")
public Object discovery(){
    // 获取服务列表名单
    List<String> services = discoveryClient.getServices();
    for(String service:services)
        log.info("当前服务service："+service);

    // 获取具体服务名称下的所有实例以及其所有信息
    List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
    for(ServiceInstance serviceInstance:instances)
        log.info("当前实例instance ID："+serviceInstance.getInstanceId() +";端口号："+serviceInstance.getPort());

    return discoveryClient;
}
```

并在主启动类添加`@EnableDiscoveryClient`

```java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
public class PaymentMain8002 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8002.class,args);
    }
}
```

#### Eureka自我保护机制

##### 概述

保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护。一旦进入保护模式，Eureka Server将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据，也就是不会注销任何微服务。

如果在Eureka Server的首页看到以下这段提示，则说明eureka进入了保护模式：

![image-20220410121043943](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101345215.png)

也就是某时刻某一个微服务不可用了，Eureka不会立刻清理，依旧会对该微服务的信息进行保存。属于CAP里面的AP分支。

##### 为什么会产生Eureka自我保护机制？

为了防止EurekaClient可以正常运行，但是与EurekaServer网络不通情况下，EurekaServer不会立刻将EurekaClient服务剔除。

##### 什么是自我保护模式？

默认情况下，如果EurekaServer在一定时间内没有接收到某个微服务实例的心跳，EurekaServer将会注销该实例（默认90秒)。但是当网络分区故障发生(延时、卡顿、拥挤)时，微服务与EurekaServer之间无法正常通信，以上行为可能变得非常危险了——**因为微服务本身其实是健康的，此时本不应该注销这个微服务**。Eureka通过“自我保护模式”来解决这个问题—当EurekaServer节点在短时间内丢失过多客户端时（可能发生了网络分区故障)，那么这个节点就会进入自我保护模式。

##### 如何进行eureka的自我保护？

* Eureka 服务中心配置

在注册中心`cloud-eureka-server7001`以及`cloud-eureka-server7002`中配置

```yaml
eureka:
  server:
    enable-self-preservation: false # 关闭自我保护机制，保证不可用服务被及时踢除
    eviction-interval-timer-in-ms: 2000
```

* Eureka 客户端配置

```yaml
eureka:
  instance:
    # Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
    #  Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2
```

![image-20220410142522753](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101554313.png)

关了自我保护机制，一旦发生故障，就去除。

* 停止8001或者8002其中一个，会立马删除
* 启动后，又会加回来

#### Eureka停止更新了怎么办？

作为Eureka的替换，还可以使用Zookeeper、Consul、Nacos进行替换。

### Zookeeper

#### 什么是Zookeeper?

Zookeeper是一个分布式协调工具，可以实现注册中心功能。[Zookeeper相关内容学习](./分布式开发笔记.md#Zookeeper)。

#### Zookeeper简单使用

配好集群过来使用….

```sh
systemctl stop firewalld # 关闭防火墙

ifconfig # 查看当前虚拟机中的IP地址，然后使用ping查看是否连通
```

#### 服务提供者

##### 新建module

![image-20220410154407500](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204101554314.png)

##### 改写pom

与之前类似，只是将eureka换成了zookeeper

```xml
    <dependencies>

        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
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
        
        <!--SpringBoot整合Zookeeper客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
            <exclusions>
                <!--先排除自带的zookeeper3.5.3-->
                <exclusion>
                    <groupId>org.apache.zookeeper</groupId>
                    <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--添加zookeeper3.4.6版本 -->
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.6</version>
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
    </dependencies>
```

##### 写yaml

与之前类似

```yaml
server:
  port: 8004

# 服务名称--注册zookeeper到注册中心名称
spring:
  application:
    name: cloud-provider-payment
  cloud:
    zookeeper:
      connect-string: 192.168.183.102:2181
```

##### 主启动

```java
@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain8004 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8004.class,args);
    }
}
```

##### Controller

可以和之前一样，这里为了快速测试，就简单写一个函数，方便访问进行测试zookeeper是否可用。

```java
@RestController
@Slf4j
public class PaymentController {
    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/zk")
    public String paymentZk(){
        return "spring cloud with zookeeper:" + serverPort +":" + UUID.randomUUID().toString();
    }
}
```

访问地址：http://localhost:8004/payment/zk

![image-20220410161317527](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117512.png)

之后，查看zookeeper里面的当前数据。

```sh
ls / # 查看当前节点数据
ls /services # 这是当前服务的数据
```

![image-20220410161543026](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117513.png)

一直查下去，会出现一个流水号

![image-20220410162255923](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117514.png)

之后，使用get获取节点的内部数据信息

```sh
get /services/cloud-provider-payment/d1374223-68fd-4555-8f58-fe66628c9555
```

![image-20220410162345194](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117515.png)

![ ](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117516.png)

#### 思考：服务节点是临时还是永久？

在zookeeper中服务节点是临时的，如果服务挂掉了，过一段时间后，服务节点就会在zookeeper中去掉。

重新加入后，流水号就会变了，因为它会默认是加入了新的一个服务节点进来。

#### 服务消费者

##### 新建module

![image-20220410163605035](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117517.png)

##### 改写pom

```xml
<dependencies>

    <dependency>
        <groupId>com.atguigu.springcloud</groupId>
        <artifactId>cloud-api-commons</artifactId>
        <version>${project.version}</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
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

    <!--SpringBoot整合Zookeeper客户端-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        <exclusions>
            <!--先排除自带的zookeeper3.5.3-->
            <exclusion>
                <groupId>org.apache.zookeeper</groupId>
                <artifactId>zookeeper</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <!--添加zookeeper3.4.6版本 -->
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.4.6</version>
    </dependency>
</dependencies>

```

##### 写yaml

```yaml
server:
  port: 80

# 服务名称--注册zookeeper到注册中心名称
spring:
  application:
    name: cloud-consumer-order
  cloud:
    zookeeper:
      connect-string: 192.168.183.102:2181
```

##### 主启动

````java
@SpringBootApplication
@EnableDiscoveryClient
public class OrderZookeeperMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderZookeeperMain80.class,args);
    }
}
````

##### 配置类

```java
@Configuration
public class ApplicationContextConfig {
    @Bean
    @LoadBalanced
    public RestTemplate getRestRemplate(){
        return new RestTemplate();
    }
}
```

##### Controller

```java
@RestController
@Slf4j
public class PaymentController {

    public static final String INVOKE_URL = "http://cloud-provider-payment";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/zk")
    public String paymentInfo(){
        String result = restTemplate.getForObject(INVOKE_URL+"/payment/zk",String.class);
        return result;
    }
}
```

##### 测试

启动后，发现该项目也加入进来了。

![image-20220410164548097](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117518.png)

之后访问地址：http://localhost/consumer/payment/zk

![image-20220410164630311](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117519.png)

##### Eureka与Zookeeper的区别

###### Eureka保证AP

Eureka服务器节点之间是对等的，只要有一个节点在，就可以正常提供服务。

Eureka客户端的所有操作可能需要一段时间才能在Eureka服务器中反映出来，随后在其他Eureka客户端中反映出来。也就是说，客户端获取到的注册信息可能不是最新的，它并不保证强一致性

###### Zookeeper保证CP

Zookeeper集群中有一个Leader，多个Follower。Leader负责写，Follower负责读，ZK客户端连接到任何一个节点都是一样的，写操作完成以后要同步给所有Follower以后才会返回。如果Leader挂了，那么重新选出新的Leader，在此期间服务不可用。

###### 为什么用Eureka

分布式系统大都可以归结为两个问题：数据一致性和防止单点故障。而作为注册中心的话，即使在一段时间内不一致，也不会有太大影响，所以在A和C之间选择A是比较适合该场景的。
### Consul

#### 什么是Consul？

![image-20220410165515379](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117520.png)

Consul是一个服务网格（微服务间的 TCP/IP，负责服务之间的网络调用、限流、熔断和监控）解决方案，它是一个一个分布式的，高度可用的系统，而且开发使用都很简便。它提供了一个功能齐全的控制平面，主要特点是：**服务发现、健康检查、键值存储、安全服务通信、多数据中心**。

与其它分布式服务注册与发现的方案相比，Consul 的方案更“一站式”——内置了服务注册与发现框架、分布一致性协议实现、健康检查、Key/Value 存储、多数据中心方案，不再需要依赖其它工具。Consul 本身使用 go 语言开发，具有跨平台、运行高效等特点，也非常方便和 Docker 配合使用。

#### Consul的安装与使用

##### 安装

下载地址：https://www.consul.io/downloads

##### 运行

* 查看版本信息

````sh
consul --version
````

* 使用开发模式启动

```sh
consul agent -dev
```

* 访问Consul地址：http://localhost:8500/

![image-20220410170258065](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117521.png)

#### 服务提供者

##### 新建module

![image-20220410192418611](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117522.png)

![image-20220410195145268](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117523.png)

##### 改pom

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    </dependency>
    <dependency>
        <groupId>com.atguigu.springcloud</groupId>
        <artifactId>cloud-api-commons</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
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
</dependencies>
```

##### 写yml

```yaml
server:
  port: 8006 # 端口号

spring:
  application:
    name: cloud-payment-service # 应用名称
  datasource: # 数据库
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2020?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456
  # consul注册中心地址
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}

mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities # 所有Entity别名类所在包
```

##### 主启动

```java
@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain8006 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8006.class,args);
    }
}
```

##### Controller

参考其他的`provider8001`工程项目。

```java
@RestController
@Slf4j
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @Value("${server.port}")
    private String serverPort;

    @Resource
    private DiscoveryClient discoveryClient;

    @GetMapping("/payment/discovery")
    public Object discovery(){
        // 获取服务列表名单
        List<String> services = discoveryClient.getServices();
        for(String service:services)
            log.info("当前服务service："+service);

        // 获取具体服务名称下的所有实例以及其所有信息
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for(ServiceInstance serviceInstance:instances)
            log.info("当前实例instance ID："+serviceInstance.getInstanceId() +";端口号："+serviceInstance.getPort());

        return discoveryClient;
    }

    @PostMapping("/payment/create")
    public CommonResult create(Payment payment){

        int result = paymentService.create(payment);
        log.info("插入结果:"+result);

        int code = 404;
        String message = "当前端口号:"+serverPort;

        if(result > 0){
            code = 200;
            message += "插入成功";
        }else {
            code = 444;
            message += "插入数据库失败";
        }

        return new CommonResult(code,message,result);
    }

    @GetMapping("/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id){

        Payment payment = paymentService.getPaymentById(id);
        log.info("查询结果:"+payment);

        int code = 404;
        String message = "当前端口号:"+serverPort;

        if(payment != null ){
            code = 200;
            message += "查询成功";
        }else {
            code = 444;
            message += "查询数据库失败";
        }

        return new CommonResult(code,message,payment);
    }
}
```

![image-20220410205902619](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117524.png)

#### 服务消费者

##### 新建module

##### 改写pom

```xml
<dependencies>

    <dependency>
        <groupId>com.atguigu.springcloud</groupId>
        <artifactId>cloud-api-commons</artifactId>
        <version>${project.version}</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
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
        <artifactId>spring-cloud-starter-consul-discovery</artifactId>
    </dependency>
</dependencies>
```

##### 写yaml

```yaml
server:
  port: 80

spring:
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
  application:
    name: cloud-consumer-order
```

##### 主启动

```java
@SpringBootApplication
@EnableDiscoveryClient
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class,args);
    }
}
```

##### Config

```java
@Configuration
public class ApplicationContextConfig {
    
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
    
}
```

##### Controller

```java
@RestController
@Slf4j
public class OrderController {

    public static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment){
        return restTemplate.postForObject(PAYMENT_URL+"/payment/create",payment,CommonResult.class);

    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id){
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);
    }

}
```

![image-20220410210820752](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117525.png)

![image-20220410210832362](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117526.png)

### Eureka、Zookeeper与Consul注册中心的异同点

| 组件名    | 语言 | CAP  | 健康检查 | 对外暴露接口 | Spring Cloud 集成 |
| --------- | ---- | ---- | -------- | ------------ | ----------------- |
| Eureka    | Java | AP   | 可配支持 | HTTP         | 已集成            |
| Consul    | Go   | CP   | 支持     | HTTP/DNS     | 已集成            |
| ZooKeeper | Java | CP   | 支持     | 客户端       | 已集成            |

### CAP理论

- Consistency：强一致性

- Availability：可用性

- Partition tolerance：分区容错性

CAP关注的粒度是数据，而不是整个系统CAP理论的定义和解释上，用的都是system、node这类的系统级概念，容易给我们造成误解，认为系统只能选择AP或者CP。 但是在实际设计中，系统不可能只处理一种数据，有的数据需要使用AP，有的数据需要使用CP。

**最多只能同时较好的满足两个**。
CAP理论的核心是：**一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求**，因此，根据CAP原理将NoSQL数据库分成了满足CA原则、满足CР原则和满足AP原则三大类：

- CA-单点集群，满足—致性，可用性的系统，通常在可扩展性上不太强大。

- CP -满足—致性，分区容忍必的系统，通常性能不是特别高。

- AP–满足可用性，分区容忍性的系统，通常可能对—致性要求低一些。

> 先保证AP，再CP

## 服务调用

### Ribbon负载均衡服务调用

#### Ribbon概述

##### 什么是Ribbon

Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端负载均衡的工具。

简单的说，Ribbon是Netflix发布的开源项目，主要功能是提供客户端的软件负载均衡算法和服务调用。Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出Load Balancer(简称LB)后面所有的机器，Ribbon会自动的帮助你基于某种规则(如简单轮询，随机连接等）去连接这些机器。我们很容易使用Ribbon实现自定义的负载均衡算法。

##### Ribbon官网

官网：https://github.com/Netflix/ribbon/wiki/Getting-Started

目前Ribbon进入维护模式中。

##### Ribbon能做什么

###### LB负载均衡(Load Balance)是什么

简单的说就是将用户的请求平摊的分配到多个服务上，从而达到系统的HA（高可用)。常见的负载均衡有软件Nginx，LVS，硬件F5等。

###### Ribbon本地负载均衡客户端 VS Nginx服务端负载均衡区别

Nginx是服务器负载均衡，客户端所有请求都会交给nginx，然后由nginx实现转发请求。即负载均衡是由服务端实现的。（集中式LB）

Ribbon本地负载均衡，在调用微服务接口时候，会在注册中心上获取注册信息服务列表之后缓存到JVM本地，从而在本地实现RPC远程服务调用技术。（进程式LB）

#### Ribbon负载均衡演示

Ribbon其实就是一个软负载均衡的客户端组件, 他可以和其他所需请求的客户端结合使用，和eureka结合只是其中一个实例。

> 说白了就是通过负载均衡+RestTemplate调用

##### 架构说明

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117527.png)

##### pom

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

eureka包就已经整合了ribbon的包。

##### RestTemplate使用

语法文档：https://docs.spring.io/spring-framework/docs/5.2.2.RELEASE/javadoc-api/org/springframework/web/client/RestTemplate.html

前面已经写过配置类

```java
@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced // 使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }

}
```

![image-20220410235447730](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202204111117528.png)



`restTemplate.getForObject`：返回对象为响应体中数据转化成的对象，基本上可以理解为json

`restTemplate.getForEntity`：返回对象为ResponseEntity对象，包含了响应中的一些重要信息，比如响应头、响应状态码、响应体。

```java
@RestController
@Slf4j
public class OrderController {

    public static final String PAYMENT_URL = "http://CLOUD-PAYMENT-SERVICE";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment){
        return restTemplate.postForObject(PAYMENT_URL+"/payment/create",payment,CommonResult.class);

    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id){
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);
    }

    @GetMapping("/consumer/payment2/get/{id}")
    public CommonResult<Payment> getPayment2(@PathVariable("id") Long id){
        ResponseEntity<CommonResult> entity = restTemplate.getForEntity(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);

        if(entity.getStatusCode().is2xxSuccessful()){
            return entity.getBody();
        }else{
            return new CommonResult<>(444,"操作失败");
        }
    }

    @GetMapping("/consumer/payment2/create")
    public CommonResult<Payment> create2(Payment payment){

        ResponseEntity<CommonResult> entity = restTemplate.postForEntity(PAYMENT_URL+"/payment/create",payment,CommonResult.class);

        if (entity.getStatusCode().is2xxSuccessful()){
            return entity.getBody();
        }else{
            return new CommonResult<>(400,"操作失败");
        }

    }

}
```

#### Ribbon核心组件IRule



#### Ribbon负载均衡算法

### OpenFeign服务接口调用

## Hystrix断路器

## 服务网关

### Zuul路由网关

### Gateway新一代网关

## SpringCloud Config分布式配置中心

## SpringCloud Bus消息总线

## SpringCloud Stream消息驱动

## SpringCloud Sleuth分布式请求链路追踪

## SpringCloud Alibaba入门简介

## SpringCloud Alibaba Nacos服务注册与配置中心

## SpringCloud Alibaba Sentinel实现熔断与限流

## SpringCloud Alibaba Seata处理分布式事务