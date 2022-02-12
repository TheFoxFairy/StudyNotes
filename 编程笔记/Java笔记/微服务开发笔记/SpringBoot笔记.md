# SpringBoot笔记

## 文档

* [spring boot](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems)

## 前言

### 回顾Spring

Spring是一个开源框架，2003 年兴起的一个轻量级的Java 开发框架，作者：Rod Johnson  。**Spring是为了解决企业级应用开发的复杂性而创建的，简化开发。**

为了降低Java开发的复杂性，Spring采用了以下4种关键策略：

1、基于POJO的轻量级和最小侵入性编程，所有东西都是bean；

2、通过IOC，依赖注入（DI）和面向接口实现松耦合；

3、基于切面（AOP）和惯例进行声明式编程；

4、通过切面和模版减少样式代码，RedisTemplate，xxxTemplate；

### 什么是SpringBoot

SpringBoot就是一个javaweb的开发框架，和SpringMVC类似，对比其他javaweb框架的好处，官方说是简化开发，**约定大于配置**，  you can "just run"，能迅速的开发web应用，几行代码开发一个http接口。

Spring Boot 基于 Spring 开发，Spirng Boot 本身并不提供 Spring 框架的核心特性以及扩展功能，只是用于快速、敏捷地开发新一代基于 Spring 框架的应用程序。也就是说，它并不是用来替代 Spring 的解决方案，而是和 Spring 框架紧密结合用于提升 Spring 开发者体验的工具。Spring Boot 以**约定大于配置的核心思想**，默认帮我们进行了很多设置，多数 Spring Boot 应用只需要很少的 Spring 配置。同时它集成了大量常用的第三方库配置（例如 Redis、MongoDB、Jpa、RabbitMQ、Quartz 等等），Spring Boot 应用中这些第三方库几乎可以零配置的开箱即用。

简单来说就是SpringBoot其实不是什么新的框架，它默认配置了很多框架的使用方式，就像maven整合了所有的jar包，spring boot整合了所有的框架 。

### SpringBoot的主要优点

- 为所有Spring开发者更快的入门
- **开箱即用**，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求

## 什么是微服务架构

### 什么是微服务

微服务是一种架构风格，它要求我们在开发一个应用的时候，这个应用必须构建成―系列小服务的组合；可以通过http的方式进行互通。

### 单体应用架构

所谓单体应用架构（all in one)是指，我们将一个应用的中的所有应用服务都封装在一个应用中。

无论是ERP、CRM或是其他什么系统，你都把数据库访问，web访问，等等各个功能放到一个war包内。

- 这样做的好处是，易于开发和测试;也十分方便部署;当需要扩展时，只需要将war复制多份，然后放到多个服务器上，再做个负载均衡就可以了。
- 单体应用架构的缺点是，哪怕我要修改一个非常小的地方，我都需要停掉整个服务，重新打包、部署这个应用war包。特别是对于一个大型应用，我们不可能吧所有内容都放在一个应用里面，我们如何维护、如何分工合作都是问题。

### 微服务架构

all in one的架构方式，我们把所有的功能单元放在一个应用里面。然后我们把整个应用部署到服务器上。如果负载能力不行，我们将整个应用进行水平复制，进行扩展，然后在负载均衡。

所谓微服务架构，就是打破之前all in one的架构方式，把每个功能元素独立出来。把独立出来的功能元素的动态组合，需要的功能元素才去拿来组合，需要多一些时可以整合多个功能元素。所以微服务架构是对功能元素进行复制，而没有对整个应用进行复制。

这样做的好处是:

1. 节省了调用资源。
2. 每个功能元素的服务都是一个可替换的、可独立升级的软件代码。

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201172250012.png" alt="image-20220117225033466" style="zoom:50%;" />

### 如何构建微服务

​	一个大型系统的微服务架构，就像一个复杂交织的神经网络，每一个神经元就是一个功能元素，它们各自完成自己的功能，然后通过http相互请求调用。比如一个电商系统，查缓存、连数据库、浏览页面、结账、支付等服务都是一个个独立的功能服务，都被微化了，它们作为一个个微服务共同构建了一个庞大的系统。如果修改其中的一个功能，只需要更新升级其中一个功能服务单元即可。

​	但是这种庞大的系统架构给部署和运维带来很大的难度。于是,spring为我们带来了构建大型分布式微服务的全套、全程产品:

- 构建一个个功能独立的微服务应用单元，可以使用springboot，可以帮我们快速构建一个应用;

- 大型分布式网络服务的调用，这部分由spring cloud来完成，实现分布式;
- 在分布式中间，进行流式数据计算、批处理，我们有spring cloud data flow。
-  spring为我们想清楚了整个从开始构建应用到大型分布式应用全流程方案。

## 第一个SpringBoot程序

### 创建项目

Spring官方提供了非常方便的工具让我们快速构建应用

Spring Initializr：https://start.spring.io/

#### 第一种创建方式：使用Spring Initializr 的 Web页面创建项目

* 打开https://start.spring.io/
* 填写项目信息，并添加依赖

![image-20220117234333458](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821519.png)

![image-20220117234427770](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821520.png)

* 点击”Generate Project“按钮生成项目；下载此项目

* 解压项目包，并用IDEA以Maven项目导入，一路下一步即可，直到项目导入完毕

![image-20220117234907092](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821521.png)

* 如果是第一次使用，可能速度会比较慢，包比较多、需要耐心等待一切就绪。

#### 第一种创建方式：使用 IDEA 直接创建项目

- 创建一个新项目
- 选择spring initalizr ， 可以看到默认就是去官网的快速构建工具那里实现
- 填写项目信息
- 选择初始化的组件（初学勾选 Web 即可）
- 填写项目路径
- 等待项目构建成功

### 项目分析

* 打开`pom.xml`，看看Spring Boot项目的依赖：

````xml
<!-- 父依赖 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.6.2</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<!--java版本-->
<properties>
    <java.version>17</java.version>
</properties>

<dependencies>
    <!-- web场景启动器 -->
    <!--web依赖：tomcat，dispatcherServlet，xml-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--单元测试-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>

        <!-- 剔除依赖 -->
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>

<!--打包插件-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
````



* 主程序入口

![image-20220117235552330](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821522.png)

* 在主程序的同级目录下，新建一个controller包，一定要在同级目录下，否则识别不到
* 在包中新建一个HelloController类

```java
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello(){
        return "Hello SpringBoot";
    }
}
```

* 编写完毕后，从主程序启动项目，浏览器发起请求，看页面返回；控制台输出了 Tomcat 访问的端口号

http://localhost:8080/hello

![image-20220118000231394](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821523.png)

### 更改项目启动名称

如何更改启动时显示的字符拼成的字母，SpringBoot呢？也就是 banner 图案；

只需一步：到项目下的 resources 目录下新建一个banner.txt 即可。

图案可以到：https://www.bootschool.net/ascii 这个网站生成，然后拷贝到文件中即可！

![image-20220118000745390](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821524.png)

## 初探：自动配置原理

之前写的HelloSpringBoot，到底是怎么运行的呢，Maven项目，我们一般从pom.xml文件探究起；

### `pom.xml`

#### 父依赖

其中它主要是依赖一个父项目，主要是管理项目的资源过滤及插件！

 ```xml
<!-- 父依赖 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.6.2</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
 ```

点进去，发现还有一个父依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.6.2</version>
</parent>
```

这里才是真正管理SpringBoot应用里面所有依赖版本的地方，SpringBoot的版本控制中心；

#### 启动器 spring-boot-starter

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**springboot-boot-starter-xxx**：就是spring-boot的场景启动器

**spring-boot-starter-web**：帮我们导入了web模块正常运行所依赖的组件；

SpringBoot将所有的功能场景都抽取出来，做成一个个的starter （启动器），只需要在项目中引入这些starter即可，所有相关的依赖都会导入进来 ， 我们要用什么功能就导入什么样的场景启动器即可 ；我们未来也可以自己自定义 starter；

* [更多启动器](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.ant)

### 主启动类

分析完了 pom.xml 来看看这个启动类

#### 默认的主启动类

```java
// 程序主入口
@SpringBootApplication // 标注这个类是一个springboot应用
public class HelloworldApplication {

	public static void main(String[] args) {
		SpringApplication.run(HelloworldApplication.class, args);
	}
}
```

但是**一个简单的启动类并不简单！**来分析一下这些注解都干了什么

#### @SpringBootApplication

作用：标注在某个类上说明这个类是SpringBoot的主配置类 ， SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

进入这个注解：可以看到上面还有很多其他注解！

```java
@SpringBootConfiguration // springboot的配置
@EnableAutoConfiguration // 自动配置
@ComponentScan( // 自动扫描并加载符合条件的组件或者bean
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    // ......
}
```

##### @ComponentScan

这个注解在Spring中很重要 ,它对应XML配置中的元素。

**作用：自动扫描并加载符合条件的组件或者bean ， 将这个bean定义加载到IOC容器中**

##### @SpringBootConfiguration

**作用：SpringBoot的配置类 ，标注在某个类上 ， 表示这是一个SpringBoot的配置类；**

继续进去这个注解查看

```java
// 点进去得到下面的 @Component
@Configuration
public @interface SpringBootConfiguration {}

@Component
public @interface Configuration {}
```

这里的 @Configuration，说明这是一个配置类 ，配置类就是对应Spring的xml 配置文件；

里面的 @Component 这就说明，启动类本身也是Spring中的一个组件而已，负责启动应用！

回到 SpringBootApplication 注解中继续看。

##### @EnableAutoConfiguration

**@EnableAutoConfiguration ：开启自动配置功能**

以前我们需要自己配置的东西，而现在SpringBoot可以自动帮我们配置 ；@EnableAutoConfiguration告诉SpringBoot开启自动配置功能，这样自动配置才能生效；

点进注解接续查看：

**@AutoConfigurationPackage ：自动配置包**

```java
@Import({Registrar.class})
public @interface AutoConfigurationPackage {
}
```

**@import** **：Spring底层注解@import ， 给容器中导入一个组件**

**Registrar.class 作用：将主启动类的所在包及包下面所有子包里面的所有组件扫描到Spring容器 ；**

这个分析完了，退到上一步，继续看

**@Import({AutoConfigurationImportSelector.class}) ：给容器导入组件 ；**

AutoConfigurationImportSelector ：自动配置导入选择器，那么它会导入哪些组件的选择器呢？我们点击去这个类看源码：

* 这个类中有一个这样的方法

```java

// 获得候选的配置
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    //这里的getSpringFactoriesLoaderFactoryClass（）方法
    //返回的就是我们最开始看的启动自动导入配置文件的注解类；EnableAutoConfiguration
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

* 这个方法又调用了  SpringFactoriesLoader 类的静态方法！我们进入SpringFactoriesLoader类loadFactoryNames() 方法

```java

public static List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
    String factoryClassName = factoryClass.getName();
    //这里它又调用了 loadSpringFactories 方法
    return (List)loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
}
```

* 继续点击查看 loadSpringFactories 方法

```java
private static Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
    //获得classLoader ， 我们返回可以看到这里得到的就是EnableAutoConfiguration标注的类本身
    MultiValueMap<String, String> result = (MultiValueMap)cache.get(classLoader);
    if (result != null) {
        return result;
    } else {
        try {
            //去获取一个资源 "META-INF/spring.factories"
            Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
            LinkedMultiValueMap result = new LinkedMultiValueMap();

            //将读取到的资源遍历，封装成为一个Properties
            while(urls.hasMoreElements()) {
                URL url = (URL)urls.nextElement();
                UrlResource resource = new UrlResource(url);
                Properties properties = PropertiesLoaderUtils.loadProperties(resource);
                Iterator var6 = properties.entrySet().iterator();

                while(var6.hasNext()) {
                    Entry<?, ?> entry = (Entry)var6.next();
                    String factoryClassName = ((String)entry.getKey()).trim();
                    String[] var9 = StringUtils.commaDelimitedListToStringArray((String)entry.getValue());
                    int var10 = var9.length;

                    for(int var11 = 0; var11 < var10; ++var11) {
                        String factoryName = var9[var11];
                        result.add(factoryClassName, factoryName.trim());
                    }
                }
            }
            cache.put(classLoader, result);
            return result;
        } catch (IOException var13) {
            throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var13);
        }
    }
}
```

* 发现一个多次出现的文件：spring.factories，全局搜索它

##### spring.factories

们根据源头打开spring.factories ， 看到了很多自动配置的文件；这就是自动配置根源所在！

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181731737)

**WebMvcAutoConfiguration**

我们在上面的自动配置类随便找一个打开看看，比如 ：WebMvcAutoConfiguration

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181732800)

可以看到这些一个个的都是JavaConfig配置类，而且都注入了一些Bean，可以找一些自己认识的类，看着熟悉一下！

所以，自动配置真正实现是从classpath中搜寻所有的META-INF/spring.factories配置文件 ，并将其中对应的 org.springframework.boot.autoconfigure. 包下的配置项，通过反射实例化为对应标注了 @Configuration的JavaConfig形式的IOC容器配置类 ， 然后将这些都汇总成为一个实例并加载到IOC容器中。

**结论：**

1. SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值
2. 将这些值作为自动配置类导入容器 ， 自动配置类就生效 ， 帮我们进行自动配置工作；
3. 整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中；
4. 它会给容器中导入非常多的自动配置类 （xxxAutoConfiguration）, 就是给容器中导入这个场景需要的所有组件 ， 并配置好这些组件 ；
5. 有了自动配置类 ， 免去了我们手动编写配置注入功能组件等的工作；

#### **SpringApplication**

```java

@SpringBootApplication
public class SpringbootApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }
}
```

**SpringApplication.run分析**

分析该方法主要分两部分，一部分是SpringApplication的实例化，二是run方法的执行；



**这个类主要做了以下四件事情：**

1、推断应用的类型是普通的项目还是Web项目

2、查找并加载所有可用初始化器 ， 设置到initializers属性中

3、找出所有的应用程序监听器，设置到listeners属性中

4、推断并设置main方法的定义类，找到运行的主类

查看构造器：

```java
public SpringApplication(ResourceLoader resourceLoader, Class... primarySources) {
    // ......
    this.webApplicationType = WebApplicationType.deduceFromClasspath();
    this.setInitializers(this.getSpringFactoriesInstances();
    this.setListeners(this.getSpringFactoriesInstances(ApplicationListener.class));
    this.mainApplicationClass = this.deduceMainApplicationClass();
}
```

#### run方法流程分析

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181732502)

### 谈谈对SpringBoot的理解

* 自动装配
* run方法

## yaml配置注入

### yaml语法学习

#### 配置文件

SpringBoot使用一个全局的配置文件 ， 配置文件名称是固定的

- application.properties

- - 语法结构 ：key=value

- application.yml

- - 语法结构 ：key：空格 value

**配置文件的作用 ：**修改SpringBoot自动配置的默认值，因为SpringBoot在底层都给我们自动配置好了；

比如可以在配置文件中修改Tomcat 默认启动的端口号！测试一下！

 ```properties
server.port=8081
 ```

#### yaml概述

YAML是 "YAML Ain't a Markup Language" （YAML不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）

> **这种语言以数据作为中心，而不是以标记语言为重点！**

以前的配置文件，大多数都是使用xml来配置；比如一个简单的端口配置，我们来对比下yaml和xml

传统xml配置：

```xml
<server>
    <port>8081<port>
</server>
```

yaml配置：

```yaml
server：
  prot: 8080
```

> application.properties与application.yaml一样，都能生效。

#### yaml基础语法

说明：语法要求严格！

1、空格不能省略

2、以缩进来控制层级关系，只要是左边对齐的一列数据都是同一个层级的。

3、属性和值的大小写都是十分敏感的。

**字面量：普通的值  [ 数字，布尔值，字符串  ]**

字面量直接写在后面就可以 ， 字符串默认不用加上双引号或者单引号；

```yaml
k: v
```

注意：

- “ ” 双引号，不会转义字符串里面的特殊字符 ， 特殊字符会作为本身想表示的意思；

  比如 ：name: "kuang \n shen"  输出 ：kuang  换行  shen

- '' 单引号，会转义特殊字符 ， 特殊字符最终会变成和普通字符一样输出

  比如 ：name: ‘kuang \n shen’  输出 ：kuang  \n  shen

**对象、Map（键值对）**

```yaml
#对象、Map格式
k: 
    v1:
    v2:
```

在下一行来写对象的属性和值得关系，注意缩进；比如：

 ```yaml
student:
    name: aaaa
    age: 3
 ```

行内写法

```yaml
dog:
   {name: 小明,age: 18}
```

**数组（ List、set ）**

用 - 值表示数组中的一个元素,比如：

```yaml
pets:
 - cat
 - dog
 - pig
```

行内写法

```yaml
pets: [cat,dog,pig]
```

**修改SpringBoot的默认端口号**

配置文件中添加，端口号的参数，就可以切换端口；

```yaml
server:
  port: 8082
```

### 注入配置文件

yaml文件更强大的地方在于，他可以给我们的实体类直接注入匹配值！

#### yaml注入配置文件

1、在springboot项目中的resources目录下新建一个文件 application.yml

2、编写一个实体类 Dog；

```yaml
@Component  //注册bean到容器中
public class Dog {
    @Value("小明")
    private String name;
    @Value("18")
    private Integer age;

    //有参无参构造、get、set方法、toString()方法

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

* 思考，原来是如何给bean注入属性值的！@Value，给狗狗类测试一下：

```java
@Component //注册bean
public class Dog {
    @Value("小明")
    private String name;
    @Value("18")
    private Integer age;
}
```

* 在SpringBoot的测试类下注入Dog输出一下；

```java
@SpringBootTest
class DemoApplicationTests {

    @Autowired //将狗狗自动注入进来
    Dog dog;

    @Test
    public void contextLoads() {
        System.out.println(dog); //打印看下狗狗对象
    }
}
```

结果成功输出，@Value注入成功。

![image-20220118180706658](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821525.png)

* 来使用yaml配置的方式进行注入，写的时候注意区别和优势，编写一个yaml配置！

```yaml
dog:
   {name: 小明,age: 18}
```

或者

```yaml
dog:
  name: 旺财
  age: 1
```

* 注入到类中！

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component  //注册bean到容器中
@ConfigurationProperties(prefix = "dog")
public class Dog {
    private String name;
    private Integer age;
    
     //有参无参构造、get、set方法、toString()方法  

    @Override
    public String toString() {
        return "Dog{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

* IDEA 提示，springboot配置注解处理器没有找到，让我们看文档，我们可以查看文档，找到一个依赖！

![image-20220118181129726](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821526.png)

```xml
<!-- 导入配置文件处理器，配置文件进行绑定就会有提示，需要重启 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

* 确认以上配置都OK之后，去测试类中测试一下

![image-20220118182129167](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181821527.png)

#### 加载指定的配置文件

**@PropertySource ：**加载指定的配置文件；

**@configurationProperties**：默认从全局配置文件中获取值；

* 在resources目录下新建一个**application.properties**文件

```properties
name=小明
```

* 然后在代码中指定加载application.properties文件

```java
@PropertySource(value = "classpath:application.properties")
@Component
public class Person {
    @Value("${name}") // 引用配置文件中name属性，进行赋值
    private String name;
}
```

* 再次输出测试一下：指定配置文件绑定成功！

![image-20220118183316879](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181836587.png)

#### properties配置

properties配置文件在写中文的时候，会有乱码 ， 我们需要去IDEA中设置编码格式为UTF-8；

settings-->FileEncodings 中配置；

![image-20220118183247785](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181836589.png)

#### 对比小结

@Value这个使用起来并不友好！我们需要为每个属性单独注解赋值，比较麻烦；我们来看个功能对比图

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201181835277)

1. @ConfigurationProperties只需要写一次即可 ， @Value则需要每个字段都添加
2. 松散绑定：这个什么意思呢? 比如我的yml中写的last-name，这个和lastName是一样的， - 后面跟着的字母默认是大写的。这就是松散绑定。可以测试一下

```yaml
person:
  last-name: 小明
```

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    @Value("${name}")
    private String lastName;

    @Override
    public String toString() {
        return "Person{" +
                "lastName='" + lastName + '\'' +
                '}';
    }
}
```

3. JSR303数据校验 ， 这个就是我们可以在字段是增加一层过滤器验证 ， 可以保证数据的合法性

4. 复杂类型封装，yml中可以封装对象 ， 使用value就不支持

**结论：**

配置yml和配置properties都可以获取到值 ， 强烈推荐 yml；

如果我们在某个业务中，只需要获取配置文件中的某个值，可以使用一下 @value；

如果说，专门编写了一个JavaBean来和配置文件进行一一映射，就直接@configurationProperties，不要犹豫！

## JSR303

* https://doc.stack.seezoon.com/guide/feature/jsr-303.html

### 数据校验

`person.java`

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
@ConfigurationProperties(prefix = "person")
@Validated // 数据校验
public class Person {
    private String email;

    @Override
    public String toString() {
        return "Person{" +
                "email='" + email + '\'' +
                '}';
    }
}
```

`application.yaml`

```yaml
person:
  email: 1111@qq.com
```

添加相关依赖：

```xml
<dependency>
    <groupId>commons-validator</groupId>
    <artifactId>commons-validator</artifactId>
    <version>1.3.1</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Springboot中可以用@validated来校验数据，如果数据异常则会统一抛出异常，方便异常中心统一处理。这里来写个注解让我们的email只能支持Email格式；

```java
@Validated // 数据校验
public class Person {

    @Email(message = "邮箱格式错误")
    private String email;
    
}
```



如果emial格式错误，输出会怎么样：

```yaml
email: 1111
```

![image-20220119163900009](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201191654137.png)

如果数据不符合规定，则会打印错误。

常见jsr303验证注解：

| 常用验证注解 | 描述                                                        |
| ------------ | ----------------------------------------------------------- |
| @NotNull     | 不能为null。                                                |
| @NotEmpty    | 不能为null 不能为空，适用字符、集合、Map、数组的验证。      |
| @NotBlank    | 不能为null或者空白符。                                      |
| @Null        | 必须为null，用的较少。                                      |
| @Size        | 为null的时候不生效，适用字符长度，集合、Map、数组容量校验。 |
| @Pattern     | 为null的时候不生效，适用字符正则验证。                      |
| @Min和@Max   | 为null的时候不生效，适合数值类验证。                        |
| @Email       | 为null的时候不生效，验证字符是否符合邮箱格式。              |
| @Digits      | 为null的时候不生效，小数验证。                              |
| @AssertTrue  | 验证 Boolean 对象是否为 true                                |
| @AssertFalse | 验证 Boolean 对象是否为 false                               |
| @Past@Past   | 验证 Date 和 Calendar 对象是否在当前时间之前                |
| @Future      | 验证 Date 和 Calendar 对象是否在当前时间之后                |
| @Pattern     | 验证 String 对象是否符合正则表达式的规则                    |

### 多环境切换

profile是Spring对不同环境提供不同配置功能的支持，可以通过激活不同的环境版本，实现快速切换环境；

#### 多配置文件

在主配置文件编写的时候，文件名可以是 application-{profile}.properties/yml , 用来指定多个环境版本；

**例如：**

application-test.properties 代表测试环境配置

application-dev.properties 代表开发环境配置

但是Springboot并不会直接启动这些配置文件，它**默认使用application.properties主配置文件**；

需要通过一个配置来选择需要激活的环境：

```properties
#比如在配置文件中指定使用dev环境，我们可以通过设置不同的端口号进行测试；
#我们启动SpringBoot，就可以看到已经切换到dev下的配置了；
spring.profiles.active=dev
```

#### yaml的多文档块

和properties配置文件中一样，但是使用yml去实现不需要创建多个配置文件，更加方便了 !

 ```yaml

server:
  port: 8081
#选择要激活那个环境块
spring:
  profiles:
    active: prod

---
server:
  port: 8083
spring:
  profiles: dev #配置环境的名称


---

server:
  port: 8084
spring:
  profiles: prod  #配置环境的名称
 ```

**注意：如果yml和properties同时都配置了端口，并且没有激活其他环境 ， 默认会使用properties配置文件的**

#### 配置文件加载位置

**外部加载配置文件的方式十分多，选择最常用的即可，在开发的资源文件中进行配置！**

官方外部配置文件说明参考文档

![image-20220119170635531](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921225.png)



springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件：

```properties
优先级1：项目路径下的config文件夹配置文件
优先级2：项目路径下配置文件
优先级3：资源路径下的config文件夹配置文件
优先级4：资源路径下配置文
```

优先级由高到底，高优先级的配置会覆盖低优先级的配置；

**SpringBoot会从这四个位置全部加载主配置文件；互补配置；**

我们在最低级的配置文件中设置一个项目访问路径的配置来测试互补问题；

```properties
#配置项目的访问路径
server.servlet.context-path=/easy
```

#### 拓展，运维小技巧

指定位置加载配置文件

我们还可以通过spring.config.location来改变默认的配置文件位置

项目打包好以后，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置；这种情况，一般是后期运维做的多，相同配置，外部指定的配置文件优先级最高

```sh
java -jar spring-boot-config.jar --spring.config.location=C:/application.properties
```

## 进阶：自动配置原理

配置文件到底能写什么？怎么写？

SpringBoot官方文档中有大量的配置，我们无法全部记住

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201191800592)



### 分析自动配置原理

以**HttpEncodingAutoConfiguration（Http编码自动配置）**为例解释自动配置原理；

```java
//表示这是一个配置类，和以前编写的配置文件一样，也可以给容器中添加组件；
@Configuration(proxyBeanMethods = false)

//启动指定类的ConfigurationProperties功能；
  //进入这个ServerProperties查看，将配置文件中对应的值和ServerProperties绑定起来；
  //并把ServerProperties加入到ioc容器中
@EnableConfigurationProperties(ServerProperties.class)

//Spring底层@Conditional注解
  //根据不同的条件判断，如果满足指定的条件，整个配置类里面的配置就会生效；
  //这里的意思就是判断当前应用是否是web应用，如果是，当前配置类生效
@ConditionalOnWebApplication(type = ConditionalOnWebApplication.Type.SERVLET)

//判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
@ConditionalOnClass(CharacterEncodingFilter.class)

//判断配置文件中是否存在某个配置：server.servlet.encoding.enabled；
  //如果不存在，判断也是成立的
  //即使我们配置文件中不配置server.servlet.encoding.enabled=true，也是默认生效的；
@ConditionalOnProperty(
    prefix = "server.servlet.encoding", 
    value = "enabled", 
    matchIfMissing = true)


public class HttpEncodingAutoConfiguration {

	private final Encoding properties;

	public HttpEncodingAutoConfiguration(ServerProperties properties) {
		this.properties = properties.getServlet().getEncoding();
	}

	@Bean
	@ConditionalOnMissingBean
	public CharacterEncodingFilter characterEncodingFilter() {
		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
		filter.setEncoding(this.properties.getCharset().name());
		filter.setForceRequestEncoding(this.properties.shouldForce(Encoding.Type.REQUEST));
		filter.setForceResponseEncoding(this.properties.shouldForce(Encoding.Type.RESPONSE));
		return filter;
	}

	@Bean
	public LocaleCharsetMappingsCustomizer localeCharsetMappingsCustomizer() {
		return new LocaleCharsetMappingsCustomizer(this.properties);
	}

	static class LocaleCharsetMappingsCustomizer
			implements WebServerFactoryCustomizer<ConfigurableServletWebServerFactory>, Ordered {
        
		private final Encoding properties;
        
		LocaleCharsetMappingsCustomizer(Encoding properties) {
			this.properties = properties;
		}

		@Override
		public void customize(ConfigurableServletWebServerFactory factory) {
			if (this.properties.getMapping() != null) {
				factory.setLocaleCharsetMappings(this.properties.getMapping());
			}
		}
        
		@Override
		public int getOrder() {
			return 0;
		}
	}
}
```

**一句话总结 ：根据当前不同的条件判断，决定这个配置类是否生效！**

- 一但这个配置类生效；这个配置类就会给容器中添加各种组件；
- 这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；
- 所有在配置文件中能配置的属性都是在xxxxProperties类中封装着；
- 配置文件能配置什么就可以参照某个功能对应的这个属性类

```java
//从配置文件中获取指定的值和bean的属性进行绑定
@ConfigurationProperties(prefix = "server", ignoreUnknownFields = true)
public class ServerProperties {
    // .....
}
```

去配置文件里面试试前缀，看提示！

![image-20220119204157228](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201192042930.png)

> **这就是自动装配的原理！**

### 精髓

1、SpringBoot启动会加载大量的自动配置类

2、我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；

3、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）

4、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；

**xxxxAutoConfigurartion：自动配置类；**给容器中添加组件

**xxxxProperties:封装配置文件中相关属性；**

### 了解：@Conditional

了解完自动装配的原理后，我们来关注一个细节问题，**自动配置类必须在一定的条件下才能生效；**

**@Conditional派生注解（Spring注解版原生的@Conditional作用）**

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201192048749" alt="Image" style="zoom:67%;" />

**那么多的自动配置类，必须在一定的条件下才能生效；也就是说，我们加载了这么多的配置类，但不是所有的都生效了。**

我们怎么知道哪些自动配置类生效？

**我们可以通过启用 debug=true属性；来让控制台打印自动配置报告，这样我们就可以很方便的知道哪些自动配置类生效；**

```properties
#开启springboot的调试类
debug=true
```

**Positive matches:（自动配置类启用的：正匹配）**

**Negative matches:（没有启动，没有匹配成功的自动配置类：负匹配）**

**Unconditional classes: （没有条件的类）**

## SpringBoot Web开发

### 回顾知识点

SpringBoot最大的特点就是自动装配。说白了就是别人写好的了，自动进行配置。

**使用SpringBoot的步骤：**

1、创建一个SpringBoot应用，选择我们需要的模块，SpringBoot就会默认将我们的需要的模块自动配置好

2、手动在配置文件中配置部分配置项目就可以运行起来了

3、专注编写业务代码，不需要考虑以前那样一大堆的配置了。

要熟悉掌握开发，之前学习的自动配置的原理一定要搞明白！

比如SpringBoot到底帮我们配置了什么？我们能不能修改？我们能修改哪些配置？我们能不能扩展？

- 向容器中自动配置组件 ：*** Autoconfiguration
- 自动配置类，封装配置文件的内容：***Properties

没事就找找类，看看自动装配原理！

### Web开发静态资源处理

#### 准备工作

* **搭建一个普通的SpringBoot项目**，回顾一下HelloWorld程序！

项目中有许多的静态资源，比如css，js等文件，这个SpringBoot怎么处理呢？

#### 静态资源处理

##### 第一种静态资源映射规则：通过webjars导入

SpringBoot中，SpringMVC的web配置都在 WebMvcAutoConfiguration 这个配置类里面；

我们可以去看看 WebMvcAutoConfigurationAdapter 中有很多配置方法；

有一个方法：addResourceHandlers 添加资源处理

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
        return;
    }
    addResourceHandler(registry, "/webjars/**", "classpath:/META-INF/resources/webjars/");
    addResourceHandler(registry, this.mvcProperties.getStaticPathPattern(), (registration) -> {
        registration.addResourceLocations(this.resourceProperties.getStaticLocations());
        if (this.servletContext != null) {
            ServletContextResource resource = new ServletContextResource(this.servletContext, SERVLET_LOCATION);
            registration.addResourceLocations(resource);
        }
    });
}
```

读一下源代码：比如所有的 `/webjars/**` ， 都需要去 `classpath:/META-INF/resources/webjars/` 找对应的资源；

##### 什么是webjars？

Webjars本质就是以jar包的方式引入的静态资源 ， 以前要导入一个静态资源文件，直接导入即可。

* 网站：https://www.webjars.org 

要使用jQuery，我们只要要引入jQuery对应版本的pom依赖即可！

 ```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>jquery</artifactId>
    <version>3.6.0</version>
</dependency>
 ```

导入完毕，查看webjars目录结构，并访问Jquery.js文件！

![image-20220119225325802](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921227.png)

访问：只要是静态资源，SpringBoot就会去对应的路径寻找资源，这里访问：

* http://localhost:8080/webjars/jquery/3.6.0/jquery.js

![image-20220119225826398](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921228.png)

##### 第二种静态资源映射规则：通过static导入

在项目中要是使用自己的静态资源该怎么导入呢？

去找staticPathPattern发现第二种映射规则 ：`/**` , 访问当前的项目任意资源，它会去找 resourceProperties 这个类，可以点进去看一下分析：

![image-20220119230453899](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921229.png)

ResourceProperties 可以设置静态资源有关的参数；这里面指向了它会去寻找资源的文件夹，即上面数组的内容。

所以得出结论，以下四个目录存放的静态资源可以被我们识别：

```properties
"classpath:/META-INF/resources/"
"classpath:/resources/"
"classpath:/static/"
"classpath:/public/"
```

![image-20220119230904152](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921230.png)

可以在resources根目录下新建对应的文件夹，都可以存放静态文件；

*  http://localhost:8080/test.js , 就会去这些文件夹中寻找对应的静态资源文件；

![image-20220119231006789](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921231.png)

##### 第三种静态资源映射规则：自定义

也可以自己通过配置文件来指定一下，哪些文件夹是需要我们放静态资源文件的，在application.properties中配置；

```properties
spring.web.resources.static-locations=classpath:/coding/,classpath:/documents/
```

#### 优先级

自定义规则 > `resouces` > `static`(默认) > `public`

### 自定义首页和图标

#### 首页

继续向下看`WebMvcAutoConfiguration.java`源码！可以看到一个欢迎页的映射，就是首页！

![image-20220119232011491](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921232.png)

点击`WelcomePageHandlerMapping.java`

![image-20220119232207751](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921233.png)

通过大致阅读，可知，这就是首页显示的源码，默认首页为`index.html`

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>首页</title>
    </head>
    <body>
        Hello SpringBoot
    </body>
</html>
```

> 静态资源可以放到上面所描述的任何地方。

#### 图标

与其他静态资源一样，Spring Boot在配置的静态内容位置中查找 favicon.ico。如果存在这样的文件，它将自动用作应用程序的favicon。

* 关闭SpringBoot默认图标

```properties
#关闭默认图标
spring.mvc.favicon.enabled=false
```

* 放一个图标在静态资源目录下，放在 public 目录或者static下，都可以4
* 清除浏览器缓存！刷新网页，发现图标已经变成自己的了！

![image-20220119234019441](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921234.png)

但是由于favicon在最新的springboot已经被禁止使用了，禁止原因详情请百度。

通过css进行导入，如下：

```css
<link rel="shortcut icon" href="favicon.ico" >
```

### Thymeleaf模板引擎

> 现在已经前后端分离啦，但是该学的还是要学

#### 模板引擎

前端交给我们的页面，是html页面。如果是我们以前开发，我们需要把他们转成jsp页面，jsp好处就是当我们查出一些数据转发到JSP页面以后，我们可以用jsp轻松实现数据的显示，及交互等。

现在springboot**现在默认是不支持jsp的**。**SpringBoot可以来使用模板引擎**来代替jsp。

![image-20220205005646557](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050104857.png)



> SpringBoot给推荐的Thymeleaf，模板引擎有非常多

模板引擎的作用就是来写一个页面模板，可以进行动态传参以及写表达式。

#### 引入Thymeleaf

* Thymeleaf 官网：https://www.thymeleaf.org/

* Spring官方文档：找到对应的版本

  https://docs.spring.io/spring-boot/docs/2.6.2/reference/htmlsingle/#using.build-systems.starters

* 导入依赖

```xml
<!--thymeleaf-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

* 编写测试页面`test.html`，在`resources/templates/test.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    test
</body>
</html>
```

#### Thymeleaf分析

按照SpringBoot的自动配置原理看一下Thymeleaf的自动配置规则，在按照那个规则，进行使用。

找一下Thymeleaf的自动配置类：ThymeleafProperties

```java
@ConfigurationProperties(prefix = "spring.thymeleaf")
public class ThymeleafProperties {

	private static final Charset DEFAULT_ENCODING = StandardCharsets.UTF_8;

	public static final String DEFAULT_PREFIX = "classpath:/templates/";

	public static final String DEFAULT_SUFFIX = ".html";

	/**
	 * Whether to check that the template exists before rendering it.
	 */
	private boolean checkTemplate = true;

	/**
	 * Whether to check that the templates location exists.
	 */
	private boolean checkTemplateLocation = true;

	/**
	 * Prefix that gets prepended to view names when building a URL.
	 */
	private String prefix = DEFAULT_PREFIX;

	/**
	 * Suffix that gets appended to view names when building a URL.
	 */
	private String suffix = DEFAULT_SUFFIX;

	/**
	 * Template mode to be applied to templates. See also Thymeleaf's TemplateMode enum.
	 */
	private String mode = "HTML";

	/**
	 * Template files encoding.
	 */
	private Charset encoding = DEFAULT_ENCODING;
  	...
}
```

可以在其中看到默认的前缀和后缀！**只需要把html页面放在类路径下的templates下，thymeleaf就可以自动渲染了。**

>  使用thymeleaf什么都不需要配置，只需要将他放在指定的文件夹下即可！

首先编写一个TestController

```java
@Controller
public class TestController {
    @GetMapping("/test")
    public String test(){
        return "test";
    }
}
```

然后启动项目请求测试

* http://localhost:8080/test

![image-20220205012447063](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050124537.png)

#### Thymeleaf语法

* Thymeleaf 官网：https://www.thymeleaf.org/ 

##### 简单测试

* 在`resources/templates/test.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    !--th:text就是将div中的内容设置为它指定的值，和之前学习的Vue一样-->
	<div th:text="${msg}"></div>
</body>
</html>
```

* 编写TestController

```java
@Controller
public class TestController {
    @GetMapping("/test")
    public String test(Model model){
        model.addAttribute("msg", "Hello SpringBoot");
        return "test";
    }
}
```

##### 使用语法

* **可以使用任意的 th:attr 来替换Html中原生属性的值！**

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050130357)

* **表达式**

```properties

Simple expressions:（表达式语法）
Variable Expressions: ${...}：获取变量值；OGNL；
    1）、获取对象的属性、调用方法
    2）、使用内置的基本对象：#18
         #ctx : the context object.
         #vars: the context variables.
         #locale : the context locale.
         #request : (only in Web Contexts) the HttpServletRequest object.
         #response : (only in Web Contexts) the HttpServletResponse object.
         #session : (only in Web Contexts) the HttpSession object.
         #servletContext : (only in Web Contexts) the ServletContext object.

    3）、内置的一些工具对象：
　　　　　　#execInfo : information about the template being processed.
　　　　　　#uris : methods for escaping parts of URLs/URIs
　　　　　　#conversions : methods for executing the configured conversion service (if any).
　　　　　　#dates : methods for java.util.Date objects: formatting, component extraction, etc.
　　　　　　#calendars : analogous to #dates , but for java.util.Calendar objects.
　　　　　　#numbers : methods for formatting numeric objects.
　　　　　　#strings : methods for String objects: contains, startsWith, prepending/appending, etc.
　　　　　　#objects : methods for objects in general.
　　　　　　#bools : methods for boolean evaluation.
　　　　　　#arrays : methods for arrays.
　　　　　　#lists : methods for lists.
　　　　　　#sets : methods for sets.
　　　　　　#maps : methods for maps.
　　　　　　#aggregates : methods for creating aggregates on arrays or collections.
==================================================================================

  Selection Variable Expressions: *{...}：选择表达式：和${}在功能上是一样；
  Message Expressions: #{...}：获取国际化内容
  Link URL Expressions: @{...}：定义URL；
  Fragment Expressions: ~{...}：片段引用表达式

Literals（字面量）
      Text literals: 'one text' , 'Another one!' ,…
      Number literals: 0 , 34 , 3.0 , 12.3 ,…
      Boolean literals: true , false
      Null literal: null
      Literal tokens: one , sometext , main ,…
      
Text operations:（文本操作）
    String concatenation: +
    Literal substitutions: |The name is ${name}|
    
Arithmetic operations:（数学运算）
    Binary operators: + , - , * , / , %
    Minus sign (unary operator): -
    
Boolean operations:（布尔运算）
    Binary operators: and , or
    Boolean negation (unary operator): ! , not
    
Comparisons and equality:（比较运算）
    Comparators: > , < , >= , <= ( gt , lt , ge , le )
    Equality operators: == , != ( eq , ne )
    
Conditional operators:条件运算（三元运算符）
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
    
Special tokens:
    No-Operation: _
```

* 抽取公共模板

`common.html`

```html
<html xmlns:th="http://www.thymeleaf.org">
    <div th:fragment="xxx">
        xxx
    </div>
</html>
```

`index.html`

插入公共部分

```html
<div th:replace="~{commons/commno::xxx}">
</div>
```

其中`xxx`类似于id，便于进行插入，`~{commons/common::xxx}`是该公共部分被放在`commons/common.html`中的`xxx`部分处。

* 日期格式化

```html
th:text="${#dates.format(date,'yyyy-MM-dd HH:mm:ss')}"
```

**练习测试：**

* 编写一个Controller，放一些数据

````java

@RequestMapping("/t2")
public String test2(Map<String,Object> map){
    //存入数据
    map.put("msg","<h1>Hello</h1>");
    map.put("users", Arrays.asList("aaa","asda"));
    //classpath:/templates/test.html
    return "test";
}
````

* 测试页面取出数据

````html

<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>狂神说</title>
    </head>
    <body>
        <h1>测试页面</h1>

        <div th:text="${msg}"></div>
        <!--不转义-->
        <div th:utext="${msg}"></div>

        <!--遍历数据-->
        <!--th:each每次遍历都会生成当前这个标签：官网#9-->
        <h4 th:each="user :${users}" th:text="${user}"></h4>

        <h4>
            <!--行内写法：官网#12-->
            <span th:each="user:${users}">[[${user}]]</span>
        </h4>

    </body>
</html>
````

> web中的session可以通过`${session.key}`进行取出来使用

### MVC自动配置原理

* 官方文档：https://docs.spring.io/spring-boot/docs/2.6.2/reference/htmlsingle/#web.servlet.spring-mvc.auto-configuration

如何扩展SpringMVC：编写一个@Configuration注解类，并且类型要为WebMvcConfigurer，还不能标注@EnableWebMvc注解；我们去自己写一个；我们新建一个包叫config，写一个类MyMvcConfig；

```java
/应为类型要求为WebMvcConfigurer，所以我们实现其接口
//可以使用自定义类扩展MVC的功能
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        // 浏览器发送/test ， 就会跳转到test页面；
        registry.addViewController("/test").setViewName("test");
    }
}
```

**要扩展SpringMVC，官方就推荐这么去使用，既保SpringBoot留所有的自动配置，也能用我们扩展的配置**

> **开发中，不推荐使用全面接管SpringMVC**

全面接管即：SpringBoot对SpringMVC的自动配置不需要了，所有都是我们自己去配置！

只需在我们的配置类中要加一个`@EnableWebMvc`。

### 错误页面

只需要在templates文件夹下创建error文件夹，放进错误页面，比如`404.html`，SpringBoot会自动寻找。

### 拦截器

> 对于登录进行拦截，进行权限判断

* 编写`config/LoginHandlerInterceptor.java`

```java
public class LoginHandlerInterceptor implements HandlerInterceptor {

    // 处理前
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        // 登录成功之后，应该有用户会话session
        Object loginUser = request.getSession().getAttribute("loginUser");

        if(loginUser == null){ //没有登录
            request.setAttribute("msg","不能登录");
            request.getRequestDispatcher("/index.html").forward(request,response);
            return false;
        }else {
            return true;
        }
    }

    //处理后
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    // 清理后
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```

* 在`config/WebMvcConfig.java`配置拦截器，进行生效

```java
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**").excludePathPatterns("/login","/index","/css/*","/img/*","/js/*");
    }
}
```

* addPathPatterns：进行设置地址进行拦截，`/**`表示所有地址。
  * excludePathPatterns：对不需要的地址进行放行，不需要拦截，比如首页，`"/css/*","/img/*","/js/*"`必须放行。

### 国际化

#### 编码问题

* 先在IDEA中统一设置properties的编码问题！

![image-20220205043849604](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050438360.png)

![img](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050229373.png)

编写国际化配置文件，抽取页面需要显示的国际化页面消息。我们可以去登录页面查看一下，哪些内容我们需要编写国际化的配置！

#### 配置文件编写

* 在resources资源文件下新建一个i18n目录，存放国际化配置文件

* 建立一个login.properties文件，还有一个login_zh_CN.properties；发现IDEA自动识别了我们要做国际化操作；文件夹变了！

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050230111)

* 可以在这上面去新建一个文件；

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050231564)

弹出如下页面：再添加一个英文的；

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050231298)

这样就快捷多了！

* **接下来，我们就来编写配置，我们可以看到idea下面有另外一个视图；**

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050231737)

这个视图我们点击 + 号就可以直接添加属性了；我们新建一个login.tip，可以看到边上有三个文件框可以输入

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050233897)

添加一下首页的内容

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050233444)



然后依次添加其他页面内容即可！

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050233035)

然后去查看我们的配置文件；

login.properties ：默认

```properties
login.btn=登录
login.password=密码
login.remember=记住我
login.tip=请登录
login.username=用户名
```

英文：

```properties
login.btn=Sign in
login.password=Password
login.remember=Remember me
login.tip=Please sign in
login.username=Username
```

中文：

```properties
login.btn=登录
login.password=密码
login.remember=记住我
login.tip=请登录
login.username=用户名
```

OK，配置文件步骤搞定！

#### 配置文件生效

```properties
spring.messages.basename=i18n.login
```

真实的情况是放在了i18n目录下，所以我们要去配置这个messages的路径；

#### 配置页面国际化值

去页面获取国际化的值，查看Thymeleaf的文档，找到message取值操作为：`#{...}`。我们去页面测试下：

IDEA还有提示，非常智能的！

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050238943)

可以去启动项目，访问一下，发现已经自动识别为中文的了！

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050238957)



**但是我们想要更好！可以根据按钮自动切换中文英文！**

#### 配置国际化解析

在Spring中有一个国际化的Locale （区域信息对象）；里面有一个叫做LocaleResolver （获取区域信息对象）的解析器！

如果我们现在想点击链接让我们的国际化资源生效，就需要让我们自己的Locale生效！

需要写一个自己的LocaleResolver，可以在链接上携带区域信息！

修改一下前端页面的跳转连接：

```html
<!-- 这里传入参数不需要使用 ？使用 （key=value）-->
<a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
<a class="btn btn-sm" th:href="@{/index.html(l='en_US')}">English</a>
```

去写一个处理的组件类！

```java

package com.easy.component;

import org.springframework.util.StringUtils;
import org.springframework.web.servlet.LocaleResolver;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Locale;

//可以在链接上携带区域信息
public class MyLocaleResolver implements LocaleResolver {

    //解析请求
    @Override
    public Locale resolveLocale(HttpServletRequest request) {

        String language = request.getParameter("l");
        Locale locale = Locale.getDefault(); // 如果没有获取到就使用系统默认的
        //如果请求链接不为空
        if (!StringUtils.isEmpty(language)){
            //分割请求参数
            String[] split = language.split("_");
            //国家，地区
            locale = new Locale(split[0],split[1]);
        }
        return locale;
    }

    @Override
    public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {

    }
}
```

为了让我们的区域化信息能够生效，我们需要再配置一下这个组件！在我们自己的MvcConofig下添加bean；

```java
@Bean
public LocaleResolver localeResolver(){
    return new MyLocaleResolver();
}
```

### Json乱码

```properties
#设置响应为utf-8
spring.http.encoding.force-response=true
```

### 整合JDBC

对于数据访问层，无论是 SQL(关系型数据库) 还是 NOSQL(非关系型数据库)，Spring Boot 底层都是采用 Spring Data 的方式进行统一处理。

Spring Boot 底层都是采用 Spring Data 的方式进行统一处理各种数据库，Spring Data 也是 Spring 中与 Spring Boot、Spring Cloud 等齐名的知名项目。

- Sping Data 官网：https://spring.io/projects/spring-data

- 数据库相关的启动器 ：可以参考官方文档：https://docs.spring.io/spring-boot/docs/2.2.5.RELEASE/reference/htmlsingle/#using-boot-starter

#### 创建测试项目测试数据源

* 创建新项目：springboot-data-jdbc，引入相应的模块

![image-20220205041058674](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050411542.png)

* 项目建好之后，发现自动帮我们导入了如下的启动器：

```xml
<!-- JDBC -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<!-- Mysql -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

* 编写yaml配置文件连接数据库

```yaml
spring:
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/review01?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
```

* 配置完这一些东西后，我们就可以直接去使用了，因为SpringBoot已经默认帮我们进行了自动配置；去测试类测试一下

```java
@SpringBootTest
class SpringbootDataJdbcApplicationTests {

    // DI注入数据源
    @Autowired
    DataSource dataSource;

    @Test
    void contextLoads() throws SQLException {
        //看一下数据源
        System.out.println(dataSource.getClass());

        // 获得连接
        Connection connection = dataSource.getConnection();
        System.out.println(connection);

        // 关闭连接
        connection.close();
    }
}
```

可以看到他默认给我们配置的数据源为 : class

`com.zaxxer.hikari.HikariDataSource `

> 默认使用HikariDataSource 数据源，**HikariDataSource 号称 Java WEB 当前速度最快的数据源，相比于传统的 C3P0 、DBCP、Tomcat jdbc 等连接池更加优秀；**

**可以使用 spring.datasource.type 指定自定义的数据源类型，值为 要使用的连接池实现的完全限定名。**

#### JDBCTemplate

1、有了数据源(com.zaxxer.hikari.HikariDataSource)，然后可以拿到数据库连接(java.sql.Connection)，有了连接，就可以使用原生的 JDBC 语句来操作数据库；

2、即使不使用第三方第数据库操作框架，如 MyBatis等，Spring 本身也对原生的JDBC 做了轻量级的封装，即JdbcTemplate。

3、数据库操作的所有 CRUD 方法都在 JdbcTemplate 中。

4、Spring Boot 不仅提供了默认的数据源，同时默认已经配置好了 JdbcTemplate 放在了容器中，程序员只需自己注入即可使用

5、JdbcTemplate 的自动配置是依赖 org.springframework.boot.autoconfigure.jdbc 包下的 JdbcTemplateConfiguration 类

**JdbcTemplate主要提供以下几类方法：**

- execute方法：可以用于执行任何SQL语句，一般用于执行DDL语句；
- update方法及batchUpdate方法：update方法用于执行新增、修改、删除等语句；batchUpdate方法用于执行批处理相关语句；
- query方法及queryForXXX方法：用于执行查询相关语句；
- call方法：用于执行存储过程、函数相关语句。

**测试**

* 编写一个Controller，注入 jdbcTemplate，编写测试方法进行访问测试；

```java
@RestController
public class JdbcController {

    /**
     * Spring Boot 默认提供了数据源，默认提供了 org.springframework.jdbc.core.JdbcTemplate
     * JdbcTemplate 中会自己注入数据源，用于简化 JDBC操作
     * 还能避免一些常见的错误,使用起来也不用再自己来关闭数据库连接
     */
    @Autowired
    JdbcTemplate jdbcTemplate;

    @RequestMapping("/test")
    public List<Map<String,Object>> test(){
        String sql = "select * from student";
        List<Map<String,Object>> students = jdbcTemplate.queryForList(sql);
        return students;
    }
}
```

* 启动程序

![image-20220205042928599](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921235.png)

### 整合Druid

#### 简介

Java程序很大一部分要操作数据库，为了提高性能操作数据库的时候，又不得不使用数据库连接池。

Druid 是阿里巴巴开源平台上一个数据库连接池实现，结合了 C3P0、DBCP 等 DB 池的优点，同时加入了日志监控。

Druid 可以很好的监控 DB 池连接和 SQL 的执行情况，天生就是针对监控而生的 DB 连接池。

Druid已经在阿里巴巴部署了超过600个应用，经过一年多生产环境大规模部署的严苛考验。

Spring Boot 2.0 以上默认使用 Hikari 数据源，可以说 Hikari 与 Driud 都是当前 Java Web 上最优秀的数据源，我们来重点介绍 Spring Boot 如何集成 Druid 数据源，如何实现数据库监控。

Github地址：https://github.com/alibaba/druid/

**com.alibaba.druid.pool.DruidDataSource 基本配置参数如下：**



![image-20220205043712530](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921236.png)

![image-20220205043935815](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050439645.png)

![image-20220205044001835](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050440169.png)

#### 配置数据源

* 添加上 Druid 数据源依赖。

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.8</version>
</dependency>
```

* 切换数据源；之前已经说过 Spring Boot 2.0 以上默认使用 com.zaxxer.hikari.HikariDataSource 数据源，但可以 通过 spring.datasource.type 指定数据源。

```yaml
spring:
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/review01?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    type: com.alibaba.druid.pool.DruidDataSource # 自定义数据源
```

* 数据源切换之后，在测试类中注入 DataSource，然后获取到它，输出一看便知是否成功切换；

![image-20220205044408232](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921237.png)

* 切换成功！既然切换成功，就可以设置数据源连接初始化大小、最大连接数、等待时间、最小连接数 等设置项；可以查看源码

```yaml
spring:
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/review01?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    type: com.alibaba.druid.pool.DruidDataSource # 自定义数据源

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

* 导入Log4j 的依赖

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

* 现在需要程序员自己为 DruidDataSource 绑定全局配置文件中的参数，再添加到容器中，而不再使用 Spring Boot 的自动生成了；我们需要 自己添加 DruidDataSource 组件到容器中，并绑定属性；

```java
@Configuration
public class DruidConfig {
    /*
       将自定义的 Druid数据源添加到容器中，不再让 Spring Boot 自动创建
       绑定全局配置文件中的 druid 数据源属性到 com.alibaba.druid.pool.DruidDataSource从而让它们生效
       @ConfigurationProperties(prefix = "spring.datasource")：作用就是将 全局配置文件中
       前缀为 spring.datasource的属性值注入到 com.alibaba.druid.pool.DruidDataSource 的同名参数中
     */
    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }
}
```

* 去测试类中测试一下；看是否成功！

```java
@SpringBootTest
class SpringbootDataJdbcApplicationTests {

    //DI注入数据源
    @Autowired
    DataSource dataSource;

    @Test
    public void contextLoads() throws SQLException {
        //看一下默认数据源
        System.out.println(dataSource.getClass());
        //获得连接
        Connection connection =   dataSource.getConnection();
        System.out.println(connection);

        DruidDataSource druidDataSource = (DruidDataSource) dataSource;
        System.out.println("druidDataSource 数据源最大连接数：" + druidDataSource.getMaxActive());
        System.out.println("druidDataSource 数据源初始化连接数：" + druidDataSource.getInitialSize());

        //关闭连接
        connection.close();
    }
}
```

![image-20220205045033087](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921238.png)

> springboot整合Druid数据(出现log4j:WARN No appenders could be found for logger (druid.sql.Connection))的解决方案：配置log4j.properties文件，详细见Mybatis日志。

#### 配置Druid数据源监控

Druid 数据源具有监控的功能，并提供了一个 web 界面方便用户查看，类似安装 路由器 时，人家也提供了一个默认的 web 页面。

所以第一步需要设置 Druid 的后台管理页面，比如 登录账号、密码 等；配置后台管理；

```java
@Configuration
public class DruidConfig {

    ...

    // 后台监控
    @Bean
    public ServletRegistrationBean statViewServlet(){
        ServletRegistrationBean<StatViewServlet> bean = new ServletRegistrationBean<>(new StatViewServlet(),"/druid/*");
        //后台，账号密码设置

        HashMap<String,String> initParameters = new HashMap<>();
        // 账号
        initParameters.put("loginUsername","admin");
        // 密码
        initParameters.put("loginPassword","123456");
        // 允许谁可以访问
        /*
            initParams.put("allow", "localhost")：表示只有本机可以访问
            initParams.put("allow", "")：为空或者为null时，表示允许所有访问
         */
        initParameters.put("allow", "");

        bean.setInitParameters(initParameters); // 初始化参数

        return bean;
    }
}
```

![image-20220205051559978](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202050516689.png)

### 整合Mybatis

- 官方文档：http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/
- Maven仓库地址：https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter/2.2.2

1. 导入 MyBatis 所需要的依赖

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.2.2</version>
</dependency>
```

2. 配置数据库连接信息（不变），对mybatis进行配置

```yaml
spring:
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/review01?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    type: com.alibaba.druid.pool.DruidDataSource # 自定义数据源

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
    

#配置mybatis
mybatis:
  #开启驼峰写法
  configuration:
    map-underscore-to-camel-case: true
  #如果配置文件和类名对应包名可以省略，否则 需要声明位置
  mapper-locations: classpath:mapper/*.xml
  #配置别名
  type-aliases-package: com.easy.pojo
```

3. **测试数据库是否连接成功！**

4. **创建实体类`Student.java`，导入 Lombok！**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    private int id;
    private String name;
}
```

* **创建mapper目录以及对应的 Mapper 接口**`StudentMapper.java`

```java
//@Mapper : 表示本类是一个 MyBatis 的 Mapper
@Mapper
@Repository
public interface StudentMapper {
    public List<Student> getAllStudent();
}
```

* **对应的Mapper映射文件**`StudentMapper.xml`

```xml
<mapper namespace="com.easy.dao.StudentMapper">
    <select id="getAllStudent" resultType="Student">
        select * from student;
    </select>
</mapper>
```

> SpringBoot会自动默认生成bean名称，生成bean规则：
>
> 1、如果首字母和第二个字母都是大写，这直接使用类名作为bean名称
>
> 2、默认是首字母转换成小写，作为bean名称
>
> 直接使用类名最方便。

* maven配置资源过滤问题

```xml
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
    </resource>
</resources>
```

* **编写StudentController测试**

```java
@RestController
public class StudentController {
    @Autowired
    StudentMapper studentMapper;

    @GetMapping("/query")
    public List<Student> query(){
        return studentMapper.getAllStudent();
    }
}
```

## SpringSecurity

### 扩展阅读

* https://www.cnblogs.com/zongmin/p/13783285.html

### 安全简介

在 Web 开发中，安全一直是非常重要的一个方面。安全虽然属于应用的非功能性需求，但是应该在应用开发的初期就考虑进来。如果在应用开发的后期才考虑安全的问题，就可能陷入一个两难的境地：**一方面，应用存在严重的安全漏洞，无法满足用户的要求，并可能造成用户的隐私数据被攻击者窃取；另一方面，应用的基本架构已经确定，要修复安全漏洞，可能需要对系统的架构做出比较重大的调整，因而需要更多的开发时间，影响应用的发布进程。**因此，从应用开发的第一天就应该把安全相关的因素考虑进来，并在整个应用的开发过程中。

> 市面上存在比较有名的：Shiro，Spring Security ！

### 什么是Spring Security？

- Spring Security是一个功能强大且高度可定制的身份验证和访问控制框架。它实际上是保护基于spring的应用程序的标准。
- Spring Security是一个框架，侧重于为Java应用程序提供身份验证和授权。与所有Spring项目一样，Spring安全性的真正强大之处在于它可以轻松地扩展以满足定制需求

从上面的介绍中可以知道这是一个权限框架。想我们之前做项目是没有使用框架是怎么控制权限的？对于权限 一般会细分为功能权限，访问权限，和菜单权限。代码会写的非常的繁琐，冗余。

怎么解决之前写权限代码繁琐，冗余的问题，一些主流框架就应运而生而Spring Scecurity就是其中的一种。

Spring 是一个非常流行和成功的 Java 应用开发框架。**Spring Security 基于 Spring 框架，提供了一套 Web 应用安全性的完整解决方案。**一般来说，Web 应用的安全性包括用户认证（Authentication）和用户授权（Authorization）两个部分。用户认证指的是验证某个用户是否为系统中的合法主体，也就是说用户能否访问该系统。用户认证一般要求用户提供用户名和密码。系统通过校验用户名和密码来完成认证过程。用户授权指的是验证某个用户是否有权限执行某个操作。在一个系统中，不同用户所具有的权限是不同的。比如对一个文件来说，有的用户只能进行读取，而有的用户可以进行修改。一般来说，系统会为不同的用户分配不同的角色，而每个角色则对应一系列的权限。

对于上面提到的两种应用情景，Spring Security 框架都有很好的支持。**在用户认证方面，Spring Security 框架支持主流的认证方式，包括 HTTP 基本认证、HTTP 表单验证、HTTP 摘要认证、OpenID 和 LDAP 等。在用户授权方面，Spring Security 提供了基于角色的访问控制和访问控制列表（Access Control List，ACL），可以对应用中的领域对象进行细粒度的控制。**

> 工作：认证、授权

### 认识SpringSecurity

Spring Security 是针对Spring项目的安全框架，也是Spring Boot底层安全模块默认的技术选型，他可以实现强大的Web安全控制，对于安全控制，我们仅需要引入 spring-boot-starter-security 模块，进行少量的配置，即可实现强大的安全管理！

记住几个类：

- **WebSecurityConfigurerAdapter：自定义Security策略**
- **AuthenticationManagerBuilder：自定义认证策略**
- **@EnableWebSecurity：开启WebSecurity模式**

> 开启某个功能，@Enablexxx

Spring Security的两个主要目标是 “认证” 和 “授权”（访问控制）。

**“认证”（Authentication）**

身份验证是关于验证您的凭据，如用户名/用户ID和密码，以验证您的身份。

身份验证通常通过用户名和密码完成，有时与身份验证因素结合使用。

 **“授权” （Authorization）**

授权发生在系统成功验证您的身份后，最终会授予您访问资源（如信息，文件，数据库，资金，位置，几乎任何内容）的完全权限。

这个概念是通用的，而不是只在Spring Security 中存在。

### 前提工作

* 创建新项目`springboot-06-security`，添加基础模块

![image-20220205212058304](../../../../../../../Pictures/assets/SpringBoot笔记/202202052301617.png)

* 创建在`templates`文件夹下创建

```properties
index.html
|views
    |level1
        1.html
        2.html
        3.html
    |level2
        1.html
        2.html
        3.html
    |level3
        1.html
        2.html
        3.html
    Login.html
```

> 链接：https://pan.baidu.com/s/1K2CzctswRKit5bce5_CpVw 
>
> 提取码：ps61 

* 在`application.properties`中关闭模板引擎缓存，方便调试

```properties
spring.thymeleaf.cache=false
```

* 创建Controller类`RouterController.java`

```java
@Controller
public class RouterController {

    @RequestMapping({"/","/index"})
    public String index(){
        return "index";
    }

    @RequestMapping("/toLogin")
    public String toLogin(){
        return "views/login";
    }

    @RequestMapping("level1/{id}")
    public String level1(@PathVariable("id") int id){
        return String.format("views/level1/%d", id);
    }

    @RequestMapping("level2/{id}")
    public String level2(@PathVariable("id") int id){
        return String.format("views/level2/%d", id);
    }

    @RequestMapping("level3/{id}")
    public String level3(@PathVariable("id") int id){
        return String.format("views/level3/%d", id);
    }
}
```

* 测试环境，是否成功

![image-20220205214616269](../../../../../../../Pictures/assets/SpringBoot笔记/202202052301618.png)

### 认证和授权

目前，测试环境，是谁都可以访问的，使用 Spring Security 增加上认证和授权的功能

* 引入 Spring Security 模块，已经在前面引入了

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

#### 授权

* 编写 Spring Security 配置类

参考官网：https://spring.io/projects/spring-security 

对应的帮助文档：https://www.baeldung.com/spring-security-jdbc-authentication

![image-20220205220126296](../../../../../../../Pictures/assets/SpringBoot笔记/202202052301619.png)

* 编写基础配置类

```java
@EnableWebSecurity
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
    }
}
```

* 定制请求的授权规则，对不同角色进行设置权限

```java
@EnableWebSecurity
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 设置权限
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("vip1")
                .antMatchers("/level2/**").hasRole("vip2")
                .antMatchers("/level3/**").hasRole("vip3");
    }
}
```

* 在configure()方法中加入以下配置，开启自动配置的登录功能！对没有权限的用户跳转到登录页面

```
// 开启自动配置的登录功能
// /login 请求来到登录页
// /login?error 重定向到这里表示登录失败
http.formLogin();
```

* 测试一下：发现，没有权限的时候，会跳转到登录的页面！

![image-20220205221611449](../../../../../../../Pictures/assets/SpringBoot笔记/202202052301444.png)

#### 认证

##### 内存认证

定义认证规则，重写configure(AuthenticationManagerBuilder auth)方法

```java
@EnableWebSecurity
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
	...
        
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("customer").password("123456").roles("vip1","vip2")
                .and()
                .withUser("admin").password("123456").roles("vip1","vip2","vip3");
    }
}
```

`inMemoryAuthentication`是从内存中读取，正常情况下应该从数据库中读取。

直接运行后，出现`java.lang.IllegalArgumentException: There is no PasswordEncoder mapped for the id "null"`错误，解决如下：

在Spring Security5.0+中，新增了很多加密方式。

```java
@EnableWebSecurity
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	...

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // 加密
        BCryptPasswordEncoder bpe = new BCryptPasswordEncoder();
        auth.inMemoryAuthentication().passwordEncoder(bpe)
                .withUser("customer").password(bpe.encode("123456")).roles("vip1","vip2")
                .and()
                .withUser("admin").password(bpe.encode("123456")).roles("vip1","vip2","vip3");
    }
}

```

> spring security 官方推荐的是使用bcrypt加密方式。

* 测试，发现，登录成功，并且每个角色只能访问自己认证下的规则！搞定

![image-20220205225920871](../../../../../../../Pictures/assets/SpringBoot笔记/202202052259831.png)

##### JDBC身份验证

* 创建数据库

```mysql
create table users(
    username varchar(50) not null primary key,
    password varchar(200) not null,
    enabled boolean not null
);
create table authorities (
    username varchar(50) not null,
    authority varchar(200) not null,
    constraint fk_authorities_users foreign key(username) references users(username)
);
create unique index ix_auth_username on authorities (username,authority);

-- User user/pass
INSERT INTO users (username, password, enabled) values 
('customer',
    '$2a$10$SBrJ5ixGwqZaE5jPnDSCZOLagXpju4lLk9XrvAYvlyE8QASC7vova',
    1),
('admin',
    '$2a$10$SBrJ5ixGwqZaE5jPnDSCZOLagXpju4lLk9XrvAYvlyE8QASC7vova',
    1);

INSERT INTO authorities (username, authority) values
('customer', 'ROLE_vip1'),
('customer', 'ROLE_vip2'),
('admin', 'ROLE_vip1'),
('admin', 'ROLE_vip2'),
('admin', 'ROLE_vip3');
```

* 导入依赖

```xml
<!-- JDBC -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<!-- Mysql -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

* 在application.yml中配置数据库

```yaml
spring:
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/review01?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
```

* 必须禁用ddl-auto属性，不然JDBC无法使用

```pro
#spring.sql.init.mode=always
spring.datasource.initialization-mode=always
spring.jpa.hibernate.ddl-auto=none
```

> 另外，请注意spring.sql.init.mode属性是在 Spring Boot 2.5.0 中引入的；对于早期版本，我们需要使用spring.datasource.initialization-mode。

* 配置JDBC身份验证

```java
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.jdbcAuthentication().dataSource(dataSource)
                .usersByUsernameQuery("select username, password, enabled from users where username=?")
                .authoritiesByUsernameQuery(
                        "select username, authority from authorities where username = ?");
    }
```



### 权限控制和注销

#### 注销

* 开启自动配置的注销的功能

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
		
        ...
        // 没有权限的跳转到登录页面
        http.formLogin();

        //开启自动配置的注销的功能
        // /logout 注销请求
        http.logout();
    }
```

* 在前端，增加一个注销的按钮，index.html 导航栏中

```html
<a class="item" th:href="@{/logout}">
   <i class="address card icon"></i> 注销
</a>
```

![image-20220206001315880](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206001315880.png)

* 运行测试

![image-20220206001346592](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206001346592.png)

* 注销后，可以重定向页面

```java
// 开启注销登录功能
http.logout()
    // 用户注销登录时访问的 url，默认为 /logout
    .logoutUrl("/logout")
    // 用户成功注销登录后重定向的地址，默认为 loginPage() + ?logout
    .logoutSuccessUrl("/login/page?logout");    
```

#### 权限控制

根据角色的权限显示出当前角色拥有的页面功能，无权限页面则不显示，并且如果登录了则隐藏登录按钮，只显示注销按钮，而未登录同理。

* ==我们需要结合thymeleaf中的一些功能==

`sec：authorize="isAuthenticated()"`:是否认证登录！来显示不同的页面

* 导入依赖

```xml
<!-- https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity4 -->
<dependency>
   <groupId>org.thymeleaf.extras</groupId>
   <artifactId>thymeleaf-extras-springsecurity5</artifactId>
   <version>3.0.4.RELEASE</version>
</dependency>
```

* 修改`index.html`前端页面，导入命名空间

```html
xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5"
```

* 修改导航栏，增加认证判断

```html
<!--登录注销-->
<div class="right menu">
    <!--未登录-->
    <div sec:authorize="!isAuthenticated()">
        <a class="item" th:href="@{/toLogin}">
            <i class="address card icon"></i> 登录
        </a>
    </div>

    <!--如果已登录-->
    <div sec:authorize="isAuthenticated()">
        <a class="item" th:href="@{/logout}">
            <i class="address card icon"></i> 注销
        </a>
    </div>
</div>
```

修改完后，进行测试

![image-20220206002200942](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206002200942.png)

* 如果注销404了，就是因为它默认防止csrf跨站请求伪造，因为会产生安全问题，我们可以将请求改为post表单提交，或者在spring security中关闭csrf功能；我们试试：在 配置中增加 `http.csrf().disable();`

```properties
http.csrf().disable();//关闭csrf功能:跨站请求伪造,默认只能通过post方式提交logout请求
http.logout().logoutSuccessUrl("/");
```

* 修改角色功能块认证完成

```html
<div class="ui three column stackable grid">
    <div class="column"  sec:authorize="hasRole('vip1')">
        <div class="ui raised segment">
            <div class="ui">
                <div class="content">
                    <h5 class="content">Level 1</h5>
                    <hr>
                    <div><a th:href="@{/level1/1}"><i class="bullhorn icon"></i> Level-1-1</a></div>
                    <div><a th:href="@{/level1/2}"><i class="bullhorn icon"></i> Level-1-2</a></div>
                    <div><a th:href="@{/level1/3}"><i class="bullhorn icon"></i> Level-1-3</a></div>
                </div>
            </div>
        </div>
    </div>

    <div class="column" sec:authorize="hasRole('vip2')">
        <div class="ui raised segment">
            <div class="ui">
                <div class="content">
                    <h5 class="content">Level 2</h5>
                    <hr>
                    <div><a th:href="@{/level2/1}"><i class="bullhorn icon"></i> Level-2-1</a><a
                                                                                                 th:href="@{/level2/2}"><i class="bullhorn icon"></i> Level-2-2</a></div>
                    <div><a th:href="@{/level2/3}"><i class="bullhorn icon"></i> Level-2-3</a></div>
                </div>
            </div>
        </div>
    </div>

    <div class="column" sec:authorize="hasRole('vip3')">
        <div class="ui raised segment">
            <div class="ui">
                <div class="content">
                    <h5 class="content">Level 3</h5>
                    <hr>
                    <div><a th:href="@{/level3/1}"><i class="bullhorn icon"></i> Level-3-1</a></div>
                    <div><a th:href="@{/level3/2}"><i class="bullhorn icon"></i> Level-3-2</a></div>
                    <div><a th:href="@{/level3/3}"><i class="bullhorn icon"></i> Level-3-3</a></div>
                </div>
            </div>
        </div>
    </div>

</div>
```

其中，进行判断的语句是` <div class="column" sec:authorize="hasRole('vip3')">`。判断角色拥有的权限。

![image-20220206002623737](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206002623737.png)

### 记住我

现在的情况，只要登录之后，关闭浏览器，再登录，就会让重新登录，但是很多网站的情况，就是有一个记住密码的功能，这个该如何实现呢？很简单

* 开启记住我功能

```java
//定制请求的授权规则
@Override
protected void configure(HttpSecurity http) throws Exception {
    ....
    //记住我
    http.rememberMe();
}
```

* 再次启动项目测试一下，发现登录页多了一个记住我功能，登录之后关闭 浏览器，然后重新打开浏览器访问，发现用户依旧存在！

![image-20220206005845955](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206005845955.png)

思考：如何实现的呢？其实非常简单，可以查看浏览器的cookie

![image-20220206010046741](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206010046741.png)

> 默认保存14天

* 更改默认保存时间

```java
// 开启 Remember-Me 功能
http.rememberMe()
    // 指定在登录时“记住我”的 HTTP 参数，默认为 remember-me
    .rememberMeParameter("remember-me")
    // 设置 Token 有效期为 200s，默认时长为 2 星期
    .tokenValiditySeconds(200)
    // 指定 UserDetailsService 对象
    .userDetailsService(userDetailsService);
```

### 全局性的安全配置

```java
/**
     * 定制一些全局性的安全配置，例如：不拦截静态资源的访问
     */
@Override
public void configure(WebSecurity web) throws Exception {
    // 静态资源的访问不需要拦截，直接放行
    web.ignoring().antMatchers("/**/*.css", "/**/*.js", "/**/*.png", "/**/*.jpg", "/**/*.jpeg");
}
```

### 定制登录页

现在这个登录页面都是spring security 默认的，怎么样可以使用我们自己写的Login界面呢？

* 在刚才的登录页配置后面指定 loginpage

```java
http.formLogin().loginPage("/toLogin");
```

* 然后前端也需要指向我们自己定义的 login请求

```html
<a class="item" th:href="@{/toLogin}">
   <i class="address card icon"></i> 登录
</a>
```

* 我们登录，需要将这些信息发送到哪里，我们也需要配置，login.html 配置提交请求及方式，方式必须为post:

```html
<form th:action="@{/login}" method="post">
   <div class="field">
       <label>Username</label>
       <div class="ui left icon input">
           <input type="text" placeholder="Username" name="username">
           <i class="user icon"></i>
       </div>
   </div>
   <div class="field">
       <label>Password</label>
       <div class="ui left icon input">
           <input type="password" name="password">
           <i class="lock icon"></i>
       </div>
   </div>
   <input type="submit" class="ui blue submit button"/>
</form>
```

* 这个请求提交上来，我们还需要验证处理，怎么做呢？我们可以查看formLogin()方法的源码！我们配置接收登录的用户名和密码的参数！

```java
http.formLogin()
    .usernameParameter("username")
    .passwordParameter("password")
    .loginPage("/toLogin")
    .loginProcessingUrl("/login"); // 登陆表单提交请求
```

* 在登录页增加记住我的多选框

```html
<input type="checkbox" name="remember"> 记住我
```

* 后端验证处理！

```java
http.rememberMe().rememberMeParameter("remember");
```

![image-20220206011415325](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206011415325.png)

![image-20220206011425796](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206011425796.png)

### 完整配置代码

```java
@EnableWebSecurity
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private DataSource dataSource; //注入数据源

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // 设置权限
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("vip1")
                .antMatchers("/level2/**").hasRole("vip2")
                .antMatchers("/level3/**").hasRole("vip3");

        // 没有权限的跳转到登录页面
        http.formLogin()
                .usernameParameter("username")
                .passwordParameter("password")
                .loginPage("/toLogin")
                .loginProcessingUrl("/login"); // 登陆表单提交请求

        //开启自动配置的注销的功能
        // /logout 注销请求
        http.logout();

        http.csrf().disable();//关闭csrf功能:跨站请求伪造,默认只能通过post方式提交logout请求
        http.logout().logoutSuccessUrl("/");

        http.rememberMe().rememberMeParameter("remember");
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
//        BCryptPasswordEncoder bpe = new BCryptPasswordEncoder();
//        auth.inMemoryAuthentication().passwordEncoder(bpe)
//                .withUser("customer").password(bpe.encode("123456")).roles("vip1", "vip2")
//                .and()
//                .withUser("admin").password(bpe.encode("123456")).roles("vip1", "vip2", "vip3");

        auth.jdbcAuthentication().dataSource(dataSource)
                .usersByUsernameQuery("select username, password, enabled from users where username=?")
                .authoritiesByUsernameQuery(
                        "select username, authority from authorities where username = ?");
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
    
}
```

## Shiro

### 概述

#### 简介

[Apache](https://so.csdn.net/so/search?q=Apache&spm=1001.2101.3001.7020) Shiro是一个强大且易用的Java安全框架

[Shiro](https://so.csdn.net/so/search?q=Shiro&spm=1001.2101.3001.7020) 不仅可以用在 JavaSE 环境中，也可以用在 JavaEE 环境中，它可以完成身份验证、授权、密码和会话管理

官网： http://shiro.apache.org/

#### 功能

![](../../../../../../../Pictures/assets/SpringBoot笔记/202202060119401.png)

- Authentication：身份认证/登录，验证用户是不是拥有相应的身份；

- Authorization：授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能做事情，常见的如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限；

- Session Manager：会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通JavaSE环境的，也可以是如Web环境的；

- Cryptography：加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；

- Web Support：Web支持，可以非常容易的集成到Web环境；

- Caching：缓存，比如用户登录后，其用户信息、拥有的角色/权限不必每次去查，这样可以提高效率；

- Concurrency：shiro支持多线程应用的并发验证，即如在一个线程中开启另一个线程，能把权限自动传播过去；

- Testing：提供测试支持；

- Run As：允许一个用户假装为另一个用户（如果他们允许）的身份进行访问；

- Remember Me：记住我，这个是非常常见的功能，即一次登录后，下次再来的话不用登录了。

#### 从外部看

![](../../../../../../../Pictures/assets/SpringBoot笔记/202202060130682.png)

应用代码直接交互的对象是Subject，也就是说Shiro的对外API核心就是Subject；其每个API的含义：

​	**Subject**：主体，代表了当前“用户”，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是Subject，如网络爬虫，机器人等；即一个抽象概念；所有Subject都绑定到SecurityManager，与Subject的所有交互都会委托给SecurityManager；可以把Subject认为是一个门面；SecurityManager才是实际的执行者；

​	**SecurityManager**：安全管理器；即所有与安全有关的操作都会与SecurityManager交互；且它管理着所有Subject；可以看出它是Shiro的核心，它负责与后边介绍的其他组件进行交互，如果学习过SpringMVC，你可以把它看成DispatcherServlet前端控制器；

​	**Realm**：域，Shiro从从Realm获取安全数据（如用户、角色、权限），就是说SecurityManager要验证用户身份，那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法；也需要从Realm得到用户相应的角色/权限进行验证用户是否能进行操作；可以把Realm看成DataSource，即安全数据源。

也就是说对于我们而言，最简单的一个Shiro应用：

1. 应用代码通过Subject来进行认证和授权，而Subject又委托给SecurityManager；

2. 我们需要给Shiro的SecurityManager注入Realm，从而让SecurityManager能得到合法的用户及其权限进行判断。


从以上也可以看出，Shiro不提供维护用户/权限，而是通过Realm让开发人员自己注入

外部架构

![](../../../../../../../Pictures/assets/SpringBoot笔记/202202060131369.png)

- **Subject**：主体，可以看到主体可以是任何可以与应用交互的“用户”；
- **SecurityManager**：相当于SpringMVC中的DispatcherServlet或者Struts2中的FilterDispatcher；是Shiro的心脏；所有具体的交互都通过SecurityManager进行控制；它管理着所有Subject、且负责进行认证和授权、及会话、缓存的管理。
- **Authenticator**：认证器，负责主体认证的，这是一个扩展点，如果用户觉得Shiro默认的不好，可以自定义实现；其需要认证策略（Authentication Strategy），即什么情况下算用户认证通过了；
- **Authrizer**：授权器，或者访问控制器，用来决定主体是否有权限进行相应的操作；即控制着用户能访问应用中的哪些功能；
- **Realm**：可以有1个或多个Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是JDBC实现，也可以是LDAP实现，或者内存实现等等；由用户提供；注意：Shiro不知道你的用户/权限存储在哪及以何种格式存储；所以我们一般在应用中都需要实现自己的Realm；
- **SessionManager**：如果写过Servlet就应该知道Session的概念，Session呢需要有人去管理它的生命周期，这个组件就是SessionManager；而Shiro并不仅仅可以用在Web环境，也可以用在如普通的JavaSE环境、EJB等环境；所有呢，Shiro就抽象了一个自己的Session来管理主体与应用之间交互的数据；这样的话，比如我们在Web环境用，刚开始是一台Web服务器；接着又上了台EJB服务器；这时想把两台服务器的会话数据放到一个地方，这个时候就可以实现自己的分布式会话（如把数据放到Memcached服务器）；
- **SessionDAO**：DAO大家都用过，数据访问对象，用于会话的CRUD，比如我们想把Session保存到数据库，那么可以实现自己的SessionDAO，通过如JDBC写到数据库；比如想把Session放到Memcached中，可以实现自己的Memcached SessionDAO；另外SessionDAO中可以使用Cache进行缓存，以提高性能；
- **CacheManager**：缓存控制器，来管理如用户、角色、权限等的缓存的；因为这些数据基本上很少去改变，放到缓存中后可以提高访问的性能
- **Cryptography**：密码模块，Shiro提高了一些常见的加密组件用于如密码加密/解密的

####  认证流程

![](../../../../../../../Pictures/assets/SpringBoot笔记/202202060132088.png)

**用户** 提交 **身份信息、凭证信息** 封装成 **令牌** 交由 **安全管理器** 认证

### 快速入门

* 新建一个Maven工程`springboot-08-shiro`，删除其 src 目录，将其作为父工程

* 在父工程中新建一个 Maven 模块`hello-shiro`

![image-20220206013909463](../../../../../../../Pictures/assets/SpringBoot笔记/202202060139932.png)

* 复制快速入门案例 POM.xml 文件中的依赖 （版本号自选）

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.shiro</groupId>
        <artifactId>shiro-core</artifactId>
        <version>1.4.1</version>
    </dependency>

    <!-- configure logging -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>jcl-over-slf4j</artifactId>
        <version>1.7.29</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-slf4j-impl</artifactId>
        <version>2.17.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.17.1</version>
    </dependency>
</dependencies>
```

* 把快速入门案例中的 resource 下的`log4j.xml`复制下来
* 复制一下 `shiro.ini` 文件
* 复制一下 `Quickstart.java` 文件
  **如果有导包的错误，把那两个错误的包删掉，就会自动导对的包了，快速入门案例中用的方法过时了**

其中IniSecurityManager已经弃用

```java
Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
SecurityManager securityManager = factory.getInstance();
```

新写法如下：

```java
DefaultSecurityManager securityManager = new DefaultSecurityManager();
IniRealm iniRealm = new IniRealm("classpath:shiro.ini");
securityManager.setRealm(iniRealm);
```

* 运行 `Quickstart.java`，得到结果

![image-20220206020420241](../../../../../../../Pictures/assets/SpringBoot笔记/202202060204903.png)

### 分析案例

* 通过 SecurityUtils 获取当前执行的用户 Subject

```java
Subject currentUser = SecurityUtils.getSubject();
```

* 通过 当前用户拿到 Session

```java
Session session = currentUser.getSession();
```

* 用 Session 存值取值

```java
session.setAttribute("someKey", "aValue");
        String value = (String) session.getAttribute("someKey");
```

* 判断用户是否被认证

```java
currentUser.isAuthenticated()
```

* 权限判断

```java
currentUser.hasRole("vip1")
```

* 执行登录操作

```java
 currentUser.login(token);
```

* 打印其标识主体

```java
currentUser.getPrincipal()
```

* 注销

```java
currentUser.logout();
```

* 完整样例

```java
public class Quickstart {

    private static final transient Logger log = LoggerFactory.getLogger(Quickstart.class);


    public static void main(String[] args) {

        // 工厂模式，通过shiro.ini 配置文件中的信息，生成一个工厂实例
        DefaultSecurityManager securityManager = new DefaultSecurityManager();
        IniRealm iniRealm = new IniRealm("classpath:shiro.ini");
        securityManager.setRealm(iniRealm);
        SecurityUtils.setSecurityManager(securityManager);

        // 现在已经建立了一个简单的shiro环境，看看能做什么
        // 通过SecurityUtils获取当前执行的用户Subject
        Subject currentUser = SecurityUtils.getSubject();

        // 通过当前用户拿到session
        // 使用shiro的session（不需要web或者EJB容器）
        Session session = currentUser.getSession();

        // 通过session进行存值取值
        session.setAttribute("someKey", "aValue");
        String value = (String) session.getAttribute("someKey");
        if (value.equals("aValue")) {
            log.info("Retrieved the correct value! [" + value + "]");
        }

        // 登录当前用户，以便可以检查角色和权限
        // 这里和SpringSecurity使用了类似的代码，判断用户是否被认证
        if (!currentUser.isAuthenticated()) {

            // 如果被认证，就可以获得一个令牌（token）
            // 通过用户的账号密码生成一个令牌
            UsernamePasswordToken token = new UsernamePasswordToken("lonestarr", "vespa");
            token.setRememberMe(true);
            try {
                // 执行登录操作
                currentUser.login(token);
            } catch (UnknownAccountException uae) {
                // 如果用户不存在
                log.info("没有用户名为 " + token.getPrincipal());
            } catch (IncorrectCredentialsException ice) {
                //如果密码不正确
                log.info("账户密码 " + token.getPrincipal() + " 不正确");
            } catch (LockedAccountException lae) {
                // 用户被锁定，如密码输出过多，则被锁住
                log.info("用户名账户 " + token.getPrincipal() + " 被锁住了  " +
                        "请与管理员联系将其解锁");
            }
            // 在此处捕获更多异常
            catch (AuthenticationException ae) {
                //异常
            }
        }

        // currentUser一些用法
        // 打印其标识主题（在这种情况下，为用户名）
        log.info("User [" + currentUser.getPrincipal() + "] logged in successfully.");

        // 测试角色是否存在
        if (currentUser.hasRole("schwartz")) {
            log.info("schwartz 用户存在");
        } else {
            log.info("schwartz 用户不存在");
        }

        // 细粒度，权限范围小
        // 测试类型化的权限（不是实例级别）
        if (currentUser.isPermitted("lightsaber:wield")) {
            log.info("You may use a lightsaber ring.  Use it wisely.");
        } else {
            log.info("Sorry, lightsaber rings are for schwartz masters only.");
        }

        // x粒度，权限范围广
        // 实例级别权限
        if (currentUser.isPermitted("winnebago:drive:eagle5")) {
            log.info("You are permitted to 'drive' the winnebago with license plate (id) 'eagle5'.  " +
                    "Here are the keys - have fun!");
        } else {
            log.info("Sorry, you aren't allowed to drive the 'eagle5' winnebago!");
        }

        // 注销
        currentUser.logout();

        System.exit(0);
    }
}
```

### SpringBoot 集成 Shiro

#### 编写配置文件

* 在刚刚的父项目中新建一个 springboot 模块`shiro-springboot`

* 导入 SpringBoot 和 Shiro 整合包的依赖

```xml
<!--SpringBoot 和 Shiro 整合包-->
<!-- https://mvnrepository.com/artifact/org.apache.shiro/shiro-spring-boot-web-starter -->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring-boot-web-starter</artifactId>
    <version>1.6.0</version>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

* 在`resources`或者`src/main/resources/META-INF`文件夹下创建一个名为`shiro.ini`的文件.并且添加我们的用户验证信息.如下图所示

  ![image-20220206030730667](../../../../../../../Pictures/assets/SpringBoot笔记/202202060524518.png)

不然会出现`Description: No bean of type ‘org.apache.shiro.realm.Realm‘ found.`错误。

* 下面是编写配置文件

Shiro 三大要素

1. subject -> ShiroFilterFactoryBean（用户）
2. securityManager -> DefaultWebSecurityManager（管理所有用户）
3. realm（数据）

实际操作中对象创建的顺序 ： realm -> securityManager -> subject

* 编写自定义的ShiroConfig，需要继承 `AuthorizingRealm`

```java
@Configuration
public class ShiroConfig {

    // ShiroFilterFactoryBean
    @Bean(name = "shiroFilterFactoryBean") //这里必须这样写
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("defaultWebSecurityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();

        // 关联securityManager
        // 设置安全管理器
        bean.setSecurityManager(defaultWebSecurityManager);

        /*
          添加shiro的内置过滤器：
            anon:无需认证就可以访问
            authc:必须认证才可以访问
            user:必须拥有 记住我  功能才能用
            perms:拥有某个资源的权限才能访问
            role:拥有某个角色的权限才能访问
         */

        Map<String ,String> filterMap = new LinkedHashMap<>();
        filterMap.put("/index", "anon");
        filterMap.put("/", "anon");
        filterMap.put("/login", "anon");
        filterMap.put("/**", "authc");

        bean.setFilterChainDefinitionMap(filterMap);
        
        bean.setLoginUrl("/toLogin");

        return bean;
    }

    // DefaultWebSecurityManager
    // 通过@Qualifier("userRealm")创建realm对象数据
    @Bean(name = "defaultWebSecurityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();

        // 关联UserRealm
        securityManager.setRealm(userRealm);

        return securityManager;
    }

    // 创建realm对象，需要自定义类
    @Bean
    public UserRealm userRealm(){
        return new UserRealm();
    }
}
```

* 编写UserRealm类

```java
// 自定义的UserRealm
public class UserRealm extends AuthorizingRealm {
    // 授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
       System.out.println("执行了授权");
        return null;
    }

    // 认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了认证");
        return null;
    }
}
```

* 编写前端页面

`index.html`

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    首页
    <div th:text="${msg}"></div>
</body>
</html>
```

`add.html`

```html
<body>
    添加
</body>
```

`update.html`

```
<body>
   	修改
</body>
```

编写登录页面`login.html`

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>登录</h1>
    <hr/>
    <form th:action="@{/login}">
        <p>用户名：<input type="text" name="username"></p>
        <p>密码：<input type="password" name="password"></p>
        <p><input type="submit"></p>
        <div th:text="${msg}"></div>
    </form>
</body>
</html>
```

* 编写控制类`MyController.java`

```java
@Controller
public class MyController {

    @GetMapping({"/","/index"})
    public String index(Model model){
        model.addAttribute("msg", "hello shiro");
        return "index";
    }

    @GetMapping("/add")
    public String add(){
        return "add";
    }

    @GetMapping("/update")
    public String update(){
        return "update";
    }
    
    @GetMapping("/toLogin")
    public String toLogin(){
        return "login";
    }
}
```

* 测试，发现shiro默认在没有认证的情况下会自动跳往login.jsp，这是因为权限问题。
* 可以自行设置登录页面

```java
//设置登录页面
bean.setLoginUrl("/toLogin");
```

#### 登录拦截器

在上面的 `getShiroFilterFactoryBean` 方法中加上需要拦截的登录请求

```java
    // ShiroFilterFactoryBean
    @Bean(name = "shiroFilterFactoryBean") //这里必须这样写
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("securityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();

        // 关联securityManager
        // 设置安全管理器
        bean.setSecurityManager(defaultWebSecurityManager);

        
        // 拦截
        /*
          添加shiro的内置过滤器：
            anon:无需认证就可以访问
            authc:必须认证才可以访问
            user:必须拥有 记住我  功能才能用
            perms:拥有某个资源的权限才能访问
            role:拥有某个角色的权限才能访问
         */

        Map<String ,String> filterMap = new LinkedHashMap<>();
        // 这样就可以进行访问了
        filterMap.put("/index", "anon");
        filterMap.put("/", "anon");
        filterMap.put("/login", "anon");
        filterMap.put("/**", "authc");
        
        // 放开静态资源
        filterMap.put("/imgs/**","anon");
        filterMap.put("/css/**","anon");
        filterMap.put("/js/**","anon");

        bean.setFilterChainDefinitionMap(filterMap);
        
        //设置登录页面
        bean.setLoginUrl("/toLogin");

        return bean;
    }
```

* 测试，点击 add链接，不会跳到 add页面，而是跳到登录页，拦截成功

![image-20220206052423160](../../../../../../../Pictures/assets/SpringBoot笔记/202202060524619.png)

#### 用户认证

* 在 Controller 中写一个登录的控制器

```java
@GetMapping("/login")
public String login(String username,String password,Model model){

    // 获取当前的用户
    Subject subject = SecurityUtils.getSubject();
    // 封装用户的登录数据=>token
    UsernamePasswordToken token = new UsernamePasswordToken(username, password);
    try {
        // 执行登录的方法
        subject.login(token);
        return "index";
    }catch (UnknownAccountException e){
        model.addAttribute("msg", "用户名错误");
        return "login";
    }catch (IncorrectCredentialsException e){
        model.addAttribute("msg", "密码错误");
        return "login";
    }
    return "login";
}
```

![image-20220206053826694](../../../../../../../Pictures/assets/SpringBoot笔记/202202060538921.png)

* 下面去自定义的 `UserRealm` 中的 `AuthenticationInfo` 方法中去获取用户信息

```java
    // 认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了认证");

        // 用户名&密码，一般在用户库中取
        String name = "admin";
        String password = "123456";

        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;

        // 获取用户名并进行判断
        if (token.getUsername() !=null &&!token.getUsername().equals(name)){
            return null;// 抛出异常，UnknownAccountException
        }

        //密码认证， Shiro 自己做，为了避免和密码的接触
        //最后返回一个 AuthenticationInfo 接口的实现类，这里选择 SimpleAuthenticationInfo
        // 三个参数：获取当前用户的认证 ； 密码 ； 认证名
        return new SimpleAuthenticationInfo("",password,"");
    }
```

#### 整合Mybatis

* 导入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>

<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.2.2</version>
</dependency>

<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.22</version>
</dependency>
```

* 在`application.yaml`中配置数据源

```yaml
spring:
  datasource:
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/review01?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8

#配置mybatis
mybatis:
  #开启驼峰写法
  configuration:
    map-underscore-to-camel-case: true
  #如果配置文件和类名对应包名可以省略，否则 需要声明位置
  mapper-locations: classpath:mapper/*.xml,dao/*.xml
  #配置别名
  type-aliases-package: com.easy.pojo
```

* 必须禁用ddl-auto属性，不然JDBC无法使用

```pro
#spring.sql.init.mode=always
spring.datasource.initialization-mode=always
spring.jpa.hibernate.ddl-auto=none
```

> 另外，请注意spring.sql.init.mode属性是在 Spring Boot 2.5.0 中引入的；对于早期版本，我们需要使用spring.datasource.initialization-mode。

* 编写实体类`User`

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String username;
    private String password;

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

* 编写mapper层`UserMapper.java`

```java
@Repository // bean
@Mapper // 注册
public interface UserMapper {
    public User getUserByName(@Param("username") String name);
}

```

* 编写mapper层`UserMapper.xml`

```xml
<mapper namespace="com.easy.mapper.UserMapper">
    <select id="getUserByName" resultType="User" parameterType="String">
        select * from users where username = #{username};
    </select>
</mapper>
```

* 如果出现错误，可能是资源过滤问题，在`pom.xml`中配置

```xml
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
    </resource>
</resources>
```

* 测试

```java
@SpringBootTest
class ShiroSpringbootApplicationTests {

    @Autowired
    UserServiceImpl userService;

    @Test
    void contextLoads() {
        System.out.println(userService.getUserByName("admin"));
    }
}

```

![image-20220206071322868](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206071322868.png)

* 修改 `UserRealm` 中的 `AuthenticationInfo`，连接数据库

```java
// 自定义的UserRealm
public class UserRealm extends AuthorizingRealm {

    @Autowired
    UserService userService;

    // 授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了授权");
        return null;
    }

    // 认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了认证");

        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;

        // 连接真实数据库
        User user = userService.getUserByName(token.getUsername());

        if(user == null) {
            return null;
        }

        //根据用户的情况，来构建AuthenticationInfo对象,通常使用的实现类为SimpleAuthenticationInfo
        //以下信息是从数据库中获取的
        //1)principal：认证的实体信息，可以是username，也可以是数据库表对应的用户的实体对象
        //Object principal = token.getPrincipal();
        User principal = user; //这里一定要写为User对象，不然在授权时，会因为获取对象出错
        //2)credentials：数据库中的密码（经过加密的密码）
        Object credentials = user.getPassword();
        //3)credentials：盐值（使用用户名）
        ByteSource credentialsSalt = ByteSource.Util.bytes(principal.getUsername());
        //4)realmName：当前realm对象的name，调用父类的getName()方法即可
        String realmName = getName();
    }
}
```

* 在注册用户时修改加密方式,在 BCrypt 中我们不需要为每个用户分配不同的盐，只要使用 `BCrypt.gensalt()` 就可以生成盐。

```java
public String encodeByBCrypt(String password) {
    return BCrypt.hashpw(password, BCrypt.gensalt());
}
```

* 如果需要加密，Shiro 框架没有内置 BCrypt 。需要引入新的库 `jBCrypt`:

```xml
<dependency>
    <groupId>de.svenkubiak</groupId>
    <artifactId>jBCrypt</artifactId>
    <version>0.4.1</version>
</dependency>
```

* 在 Shiro 配置文件 `ShiroConfig` 中为我们自定义的 `UserRealm` Bean 添加凭证匹配器。

```java
// 创建realm对象，需要自定义类
@Bean
public UserRealm userRealm(){

    UserRealm realm = new UserRealm();
    //配置单项hash
    //UserRealm.setCredentialsMatcher(hashedCredentialsMatcher());

    //配置 BCrypt
    realm.setCredentialsMatcher(new CredentialsMatcher() {
        @Override
        public boolean doCredentialsMatch(AuthenticationToken token, AuthenticationInfo info) {
            UsernamePasswordToken userToken = (UsernamePasswordToken) token;
            //要验证的明文密码
            String plaintext = new String(userToken.getPassword());
            //数据库中的加密后的密文
            String hashed = info.getCredentials().toString();
            return BCrypt.checkpw(plaintext, hashed);
        }
    });
    return realm;
}
```

![image-20220206073628169](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206073628169.png)

#### 请求授权

**修改实体类，增加一个字段**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String username;
    private String password;
    private String perms;
}
```

对数据库进行修改，添加一个字段

![image-20220206085837366](../../../../../../../Pictures/assets/SpringBoot笔记/202202060858342.png)

在`public ShiroFilterFactoryBean getShiroFilterFactoryBean`中，添加授权样例

```java
// 授权
filterMap.put("/add", "perms[add]");
```

必须通过当前参数，比如perms[user:method]，表示`/user/method`类似链接。在未授权进行访问的时候，会跳转到未授权页面。如果想修改跳转到其他未授权页面，如下：

```java
bean.setUnauthorizedUrl("/unauthorized");
```

* 对被拦截的用户进行授权访问

```java
// 授权
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
    System.out.println("执行了授权");

    SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();

    Subject subject = SecurityUtils.getSubject(); //获得当前对象
    User currentUser = (User) subject.getPrincipal(); //拿到User对象
    info.addStringPermission(currentUser.getPerms()); //设置权限

    return info;
}
```

![image-20220206075736936](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206075736936.png)

#### 整合Thymeleaf

需求：**根据权限展示不同的前端页面**

* 添加maven依赖

```xml
<!-- https://mvnrepository.com/artifact/com.github.theborakompanioni/thymeleaf-extras-shiro -->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.1.0</version>
</dependency>
```

* **配置一个shiro的Dialect ，在shiro的配置中增加一个Bean**

```java
//配置ShiroDialect：用于 thymeleaf 和 shiro 标签配合使用
@Bean
public ShiroDialect getShiroDialect(){
    return new ShiroDialect();
}
```

* 修改`index.html`

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">

<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    首页
    <div th:text="${msg}"></div>

    <hr/>

    <div shiro:hasPermission="add">
        <a th:href="@{/add}">add</a>
    </div>

    <div shiro:hasPermission="update">
        <a th:href="@{/update}">update</a>
    </div>

</body>
</html>
```

![image-20220206091234671](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206091234671.png)

* 在用户登录后应该把信息放到Session中，完善下！在执行认证逻辑时候，加入session

**在controller的login方法中**

```java
// 执行登录的方法
subject.login(token);

//登陆成功存放用户信息
Subject currentSubject = SecurityUtils.getSubject();
Session session = currentSubject.getSession();
session.setAttribute("loginUser",username);
```

* 修改`index.html`页面，添加登录和注销

```html
<p th:if="${session.get('loginUser')}==null">
    <a th:href="@{/toLogin}">登录</a>
</p>
<p th:if="${session.get('loginUser')}!=null">
    <a th:href="@{/logOut}">注销</a>
</p>
```

![image-20220206092313534](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206092313534.png)

* 实现注销功能

```java
//注销登录功能
@RequestMapping("/logOut")
public String logOut(){

    Subject currentUser = SecurityUtils.getSubject();
    currentUser.logout();

    return "index";
}
```

## Swagger

![image-20220206131351334](../../../../../../../Pictures/assets/SpringBoot笔记/202202061313761.png)

### 学习目标

- 了解Swagger的概念及作用
- 掌握在项目中集成Swagger自动生成API文档

### Swagger简介

**前后端分离**

- 前端 -> 前端控制层、视图层
- 后端 -> 后端控制层、服务层、数据访问层
- 前后端通过API进行交互
- 前后端相对独立且松耦合

**产生的问题**

- 前后端集成，前端或者后端无法做到“及时协商，尽早解决”，最终导致问题集中爆发

**解决方案**

- 首先定义schema [ 计划的提纲 ]，并实时跟踪最新的API，降低集成风险
- 以前：指定word计划文档

**Swagger**

- 号称世界上最流行的API框架
- Restful Api 文档在线自动生成器 => **API 文档 与API 定义同步更新**
- 直接运行，在线测试API
- 支持多种语言 （如：Java，PHP等）
- 官网：https://swagger.io/

### SpringBoot集成Swagger

**SpringBoot集成Swagger** => **springfox**，两个jar包

1. **Springfox-swagger2**
2. swagger-springmvc

**使用Swagger**，要求：jdk 1.8 + 否则swagger2无法运行

* 新建一个SpringBoot-web项目
* 导入相应依赖

`swagger2.9.2`版本

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
</dependencies>
```

`swagger3.0.0`版本

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>3.0.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>3.0.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-boot-starter -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```



* 编写一个HelloController

```java
@RestController
public class HelloController {
    @RequestMapping("/")
    public String hello(){
        return "hello swagger";
    }
}
```

* 在WebMvcConfig中配置静态资源

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/**").addResourceLocations("classpath:/static/");
        registry.addResourceHandler("swagger-ui.html")
                .addResourceLocations("classpath:/META-INF/resources/");
        registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/");
    }
}
```

* 在SwaggerConfig中配置Swagger

```java
@Configuration
@EnableSwagger2 //开启Swagger2
// @EnableOpenApi //开启Swagger3
public class SwaggerConfig{

}
```

**注意：版本不同`注解`不同**

- **Swagger3.0**——>注解：`@EnableOpenApi` //开启Swagger3.0

```java
// 配置Swagger的Docket的Bean实例
@Bean
public Docket docket() {
    return new Docket(DocumentationType.OAS_30) // 这里和2.x有区别
        .apiInfo(apiInfo())
        .select()
        // RequestHandlerSelect，配置要扫描的包的接口
        .apis(RequestHandlerSelectors.basePackage("com.easy.controller"))
        .build();
}
```

- **Swagger2.0**——>注解：`@EnableSwagger2`

* 测试运行：http://localhost:8080/swagger-ui.html

**注意：版本不同`测试路径`不同**

- **Swagger3.0**——>测试地址：`http://localhost:8080/swagger-ui/index.html`
- **Swagger2.0**——>测试地址：`http://localhost:8080/swagger-ui.html`

![image-20220206140046487](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206140046487.png)

![image-20220207050959176](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220207050959176.png)

### 配置Swagger

Swagger实例Bean是Docket，所以通过配置Docket实例来配置Swaggger。其中可以通过apiInfo()属性配置文档信息

```java
@Configuration
@EnableSwagger2 //开启Swagger2
public class SwaggerConfig {

    // 配置Swagger的Docket的Bean实例
    @Bean
    public Docket docket() {
        return new Docket(DocumentationType.OAS_30)
                .apiInfo(apiInfo());
    }

    // 配置Swagger信息（apiInfo）
    private ApiInfo apiInfo() {

        Contact contact = new Contact("洛洛历险记", "https://xxx.com", "xxx@qq.com");

        ApiInfo info = new ApiInfo(
                "Api接口文档",
                "这是一个Api接口文档",
                "v1.0",
                "https://xxx.com",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList());
        return info;
    }
}

```

![image-20220206142732928](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220206142732928.png)

### 配置扫描端口

分析一下接口部分显示信息

![](../../../../../../../Pictures/assets/SpringBoot笔记/202202070518859.png)

#### 设置扫描包及过滤

* 构建Docket时通过select()方法配置怎么扫描接口。

```java
// 配置Swagger的Docket的Bean实例
@Bean
public Docket docket() {
    return new Docket(DocumentationType.OAS_30)
        .apiInfo(apiInfo())
        .select()
        // RequestHandlerSelect，配置要扫描的包的接口
        //basePackage指定要扫描的包
        .apis(RequestHandlerSelectors.basePackage("com.easy.controller"))
        .build();
}
```

![image-20220207052209185](../../../../../../../Pictures/assets/SpringBoot笔记/202202070552181.png)

RequestHandlerSelectors扫描接口的方式

**RequestHandlerSelectors**，扫描接口的方式。其拥有的配置方法：

- **basePackage**指定要扫描的包（常用）
- **any()**扫描全部
- **none**不扫描
- **withClassAnnotation** 扫描类上得注解
- **withMethodAnnotation** 扫描方法上得注解

#### PathSelectors过滤方式

方法：

- ant()：过滤路径
- any()：全部过滤
- none()：不过滤
- regex()：正则表达式

使用第一种过滤路径测试

![image-20220207055015769](../../../../../../../Pictures/assets/SpringBoot笔记/202202070553223.png)

过滤后什么都没有了

![image-20220207055028242](../../../../../../../Pictures/assets/SpringBoot笔记/202202070552183.png)

#### 配置是否启动

`enabled=true`属性是控制启动

在`.apiInfo(apiInfo())`后面进行添加`.enable(false)`

```java
// 配置Swagger的Docket的Bean实例
@Bean
public Docket docket() {
    return new Docket(DocumentationType.OAS_30)
        .apiInfo(apiInfo())
        .enable(false)//关闭Swagger
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.easy.controller"))
        .build();
}
```

测试地址：http://localhost:8080/swagger-ui/index.html#/

![image-20220207055720687](../../../../../../../Pictures/assets/SpringBoot笔记/202202070557411.png)

#### 设置Swagger在对应的项目环境启动

在项目中只在**开发环境**开启Swagger，**生产环境**就要关闭Swagger。下面进行设置。

##### 创建测试环境

- application-dev：开发环境
- application-pro：生产环境

![image-20220207061528258](../../../../../../../Pictures/assets/SpringBoot笔记/202202070627108.png)



`application.properties`内代码：``spring.profiles.active=dev`
`application-dev.properties`内代码：`server.port=8081`
`application-pro.properties`内代码：`server.port=8082`

##### 编写配置类获取项目环境

- `Profiles.of()`设置要显示的swagger的环境
- 通过`environment.acceptsProfiles`判断是否处在自己设定的环境中 获取`boolean`值`flag`
- 赋值给`enable（flag）`

```java
// 配置Swagger的Docket的Bean实例
@Bean
public Docket docket(Environment environment) {

    // 设置要显示的Swagger环境
    Profiles profiles = Profiles.of("dev","test");
    // 通过`Environment environment`获取项目环境
    // 通过environment.acceptsProfiles判断是否处于自己设置的环境中
    boolean flag = environment.acceptsProfiles(profiles);

    return new Docket(DocumentationType.OAS_30)
        .apiInfo(apiInfo())
        .enable(flag)
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.easy.controller"))
        .build();
}
```

##### 测试

application.properties中切换环境为dev环境： `spring.profiles.active=dev`

dev环境的测试地址：http://localhost:8081/swagger-ui/index.html#/

![image-20220207061958893](../../../../../../../Pictures/assets/SpringBoot笔记/202202070627141.png)

application.properties中切换环境为pro环境： `spring.profiles.active=pro`

![image-20220207062053031](../../../../../../../Pictures/assets/SpringBoot笔记/202202070627625.png)

### API分组

#### 设置分组

Docket的源码可以看到分组的默认值为default

![img](../../../../../../../Pictures/assets/SpringBoot笔记/202202070627594.png)

进行修改,只需要一行代码

```java
.groupName("花儿为什么这样红")
```

![image-20220207062858754](../../../../../../../Pictures/assets/SpringBoot笔记/202202070630386.png)

运行后，看到前端页面，也发什么了修改

![image-20220207062954903](../../../../../../../Pictures/assets/SpringBoot笔记/202202070629293.png)

#### 设置分组

**多个分组就是多人协作开发时每个人分组**，其实就是多个**Docket实例**。

```java
@Bean
public Docket docket1(){
    return new Docket(DocumentationType.OAS_30).groupName("A");
}

@Bean
public Docket docket2(){
    return new Docket(DocumentationType.OAS_30).groupName("B");
}

@Bean
public Docket docket3(){
    return new Docket(DocumentationType.OAS_30).groupName("C");
}
```

运行一下就可以看到多个分组了，选择一个分组信息就会发生变化，以及测试路径。

![image-20220207063512761](../../../../../../../Pictures/assets/SpringBoot笔记/202202070635169.png)

### 注释

#### 常用注解

Swagger的所有注解定义在io.swagger.annotations包下

下面列一些经常用到的，未列举出来的可以另行查阅说明：

| Swagger注解                                            | 简单说明                                             |
| ------------------------------------------------------ | ---------------------------------------------------- |
| @Api(tags = "xxx模块说明")                             | 作用在模块类上                                       |
| @ApiOperation("xxx接口说明")                           | 作用在接口方法上                                     |
| @ApiModel("xxxPOJO说明")                               | 作用在模型类上：如VO、BO                             |
| @ApiModelProperty(value = "xxx属性说明",hidden = true) | 作用在类方法和属性上，hidden设置为true可以隐藏该属性 |
| @ApiParam("xxx参数说明")                               | 作用在参数、方法和字段上，类似@ApiModelProperty      |

#### 实体类注释

先创建pojo编写一个User进行测试

```java
public class User {
    private String username;
    private String password;
}
```

直接启动项目是不能扫描到实体类的。

#### 方法一（不是用注解，也可以扫描到实体类）

实体类不使用注释也可以被扫描，只要请求返回类型是实体类即可。

* 在`HelloController`中添加方法

```java
@RequestMapping("/user")
public User getUser(){
    return new User();
}
```

* 运行测试

![image-20220207064413412](../../../../../../../Pictures/assets/SpringBoot笔记/202202070646180.png)

实体有了但是这样的没有属性，这是因为没有给属性添加get set方法。给是实体类添加get set 方法,再次测试

```java
import lombok.Data;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String username;
    private String password;
}
```

![image-20220207064600943](../../../../../../../Pictures/assets/SpringBoot笔记/202202070646391.png)



#### 方法二（注解）

- `@ApiModel("用户实体类")`：用于实体类上，描述实体类
- `@ApiModelProperty(value = "用户名",hidden = false)`：用于属性上描述实体类属性，hidden功能是，是否隐藏该属性

```java
@ApiModel("用户实体类")
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    @ApiModelProperty(value = "用户名",hidden = false)//hidden是否隐藏
    private String username;
    @ApiModelProperty(value = "密码")
    private String password;
}

```

![image-20220207065026736](../../../../../../../Pictures/assets/SpringBoot笔记/202202070650683.png)

#### 接口注释

接口常用的三个注解

- `@Api(tags = "helloController层")`：放在控制层类上，描述控制层

- `@ApiOperation(value = "hello2方法",notes = "方法描述")`：放在方法控制层上，描述控制层方法

- `@ApiParam("用户名")`：参数描述

```java
@Api(tags = "HelloController层")
@RestController
public class HelloController {

    @ApiOperation("hello方法")
    @RequestMapping("/")
    public String hello(){
        return "hello swagger";
    }

    @ApiOperation(value = "用户接口方法",notes = "方法描述")
    @RequestMapping("/user")
    public User getUser(@ApiParam("用户名") String username,@ApiParam("密码") String password){
        return new User(username,password);
    }
}
```

![image-20220207065736537](../../../../../../../Pictures/assets/SpringBoot笔记/202202070821709.png)

### 扩展功能

#### 默认的 

Swagger2：http://localhost:8080/swagger-ui.html

Swagger3： http://localhost:8080/swagger-ui/index.html

```xml
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger-ui</artifactId>
   <version>2.9.2</version>
</dependency>
```

#### Layui-ui

 http://localhost:8080/docs.html

```xml
<!-- 引入swagger-ui-layer包 /docs.html-->
<dependency>
   <groupId>com.github.caspar-chen</groupId>
   <artifactId>swagger-ui-layer</artifactId>
   <version>1.1.3</version>
</dependency>
```

#### mg-ui

http://localhost:8080/document.html

```xml
<!-- 引入swagger-mg-ui包 /document.html-->
<dependency>
   <groupId>com.zyplayer</groupId>
   <artifactId>swagger-mg-ui</artifactId>
   <version>1.0.6</version>
</dependency>
```

![image-20220207082116083](../../../../../../../Pictures/assets/SpringBoot笔记/202202070821453.png)

需要在`webMvcConfig.java`设置

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/**").addResourceLocations("classpath:/static/");
        registry.addResourceHandler("swagger-ui.html")
                .addResourceLocations("classpath:/META-INF/resources/");
        registry.addResourceHandler("document.html")
                .addResourceLocations("classpath:/META-INF/resources/");
        registry.addResourceHandler("docs.html")
                .addResourceLocations("classpath:/META-INF/resources/");
        registry.addResourceHandler("/webjars/**")
                .addResourceLocations("classpath:/META-INF/resources/webjars/");
    }
}
```

#### knife4j（推荐）

#### 配置

http://localhost:8080/doc.html

```xml
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-spring-boot-starter</artifactId>
    <version>3.0.3</version>
</dependency>
```

注意

- knife4j 已经引入了 springfox，所以在使用的时候无需再次引入
  springfox，否则有可能会导致版本冲突，如果你在网关聚合时，必须禁用 knife4j 的增强功能。
- 使用Knife4j2.0.6及以上的版本，Spring Boot的版本必须大于等于2.2.x

**SwaggerConfig 配置依赖**

```java
@Configuration
@EnableOpenApi // 开启Swagger3
public class SwaggerConfig{

    // 配置Swagger的Docket的Bean实例
    @Bean
    public Docket docket(Environment environment) {

        // 设置要显示的Swagger环境
        Profiles profiles = Profiles.of("dev","test");
        // 通过`Environment environment`获取项目环境
        // 通过environment.acceptsProfiles判断是否处于自己设置的环境中
        boolean flag = environment.acceptsProfiles(profiles);


        return new Docket(DocumentationType.OAS_30)
                .apiInfo(apiInfo())
                .enable(flag)
                .groupName("花儿为什么这样红")
                .select()
                // RequestHandlerSelect，配置要扫描的包的接口
                //basePackage指定要扫描的包
                .apis(RequestHandlerSelectors.basePackage("com.easy.controller"))
//                // paths：过滤路径
//                .paths(PathSelectors.ant("/easy/**"))
                .build();
    }

    @Bean
    public Docket docket1(){
        return new Docket(DocumentationType.OAS_30).groupName("A");
    }

    @Bean
    public Docket docket2(){
        return new Docket(DocumentationType.OAS_30).groupName("B");
    }

    @Bean
    public Docket docket3(){
        return new Docket(DocumentationType.OAS_30).groupName("C");
    }

    // 配置Swagger信息（apiInfo）
    private ApiInfo apiInfo() {

        Contact contact = new Contact("洛洛历险记", "https://xxx.com", "xxx@qq.com");

        return new ApiInfo(
                "Api接口文档",
                "这是一个Api接口文档",
                "v1.0",
                "https://xxx.com",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList());
    }

}
```

如果在启动项目的时候抛出：**Failed to start bean 'documentationPluginsBootstrapper'; nested exception is java.lang.NullPointerException**

那是因为springboot 版本太高，应该是 2.6.x，由于Springfox使用的路径匹配是基于AntPathMatcher，而Spring Boot 2.6.X使用的是PathPatternMatcher，所以将MVC的路径匹配规则改成 AntPathMatcher，在配置文件中加入如下参数即可（如果没有报错，可以跳过这个环节）

```yaml
spring:
  mvc:
    pathmatch:
      # Springfox使用的路径匹配是基于AntPathMatcher的，而Spring Boot 2.6.X使用的是PathPatternMatcher
      # 所以需要配置此参数
      matching-strategy: ant_path_matcher
```

启动成功之后，在浏览器中访问：http://localhost:8080/doc.html。

并且在`WebMvcConfig`中配置如下：

```java
registry.addResourceHandler("doc.html")
    .addResourceLocations("classpath:/META-INF/resources/");
```

![image-20220207083032117](../../../../../../../Pictures/assets/SpringBoot笔记/202202070830165.png)

#### knife4j增强功能

什么是 knife4j 的增强功能？我们在前面看到的只是 knife4j 最基础的使用方式，knife4j 还有很多强大的功能还没有展示出来，比如：i18n国际化、接口添加作责、自定义文档、访问权限控制、接口排序、到处离线文档、过滤请求参数等等，这些都是 knife4j 的增强功能，那如何开启 knife4j 的增强功能呢？

Knife4j自2.0.6版本开始,将目前在Ui界面中一些个性化配置剥离,开发者可以在后端进行配置，并且提供的knife4j-spring-boot-strater组件自动装载，开发者可以在配置文件中决定需要开启的功能。

springboot 中 knife4j的完整参数如下：

```yaml
knife4j:
  # 开启增强配置 
  enable: true
  # 开启生产环境屏蔽
  production: false
  documents:
    -
      group: 2.X版本
      name: 接口签名
      locations: classpath:sign/*
  setting:
    language: zh-CN
    enableSwaggerModels: true
    enableDocumentManage: true
    swaggerModelName: 实体类列表
    enableVersion: false
    enableReloadCacheParameter: false
    enableAfterScript: true
    enableFilterMultipartApiMethodType: POST
    enableFilterMultipartApis: false
    enableRequestCache: true
    enableHost: false
    enableHostText: 192.168.0.193:8000
    enableHomeCustom: true
    homeCustomLocation: classpath:markdown/home.md
    enableSearch: false
    enableFooter: false
    enableFooterCustom: true
    footerCustomContent: Apache License 2.0 | Copyright  2019-[浙江八一菜刀股份有限公司](https://gitee.com/xiaoym/knife4j)
    enableDynamicParameter: false
    enableDebug: true
    enableOpenApi: false
    enableGroup: true
  cors: false
  basic:
    enable: false
    username: test
    password: 12313
```

knife4j 的增强功能是需要开启的，默认关闭，开启也是十分的简单，**在以前的版本中,开发者需要在配置文件中手动使用@EnableKnife4j来使用增强，自2.0.6版本后,只需要在配置文件中配置knife4j.enable=true即可不在使用注解**
**注意：要使用Knife4j提供的增强，knife4j.enable=true必须开启。包括后面所讲解到的所有增强功能，都需要设置这个参数。**

下面来介绍以下上面的这些属性值所表达的是什么意思

| 属性                                               | 默认值         | 说明                                                         |
| -------------------------------------------------- | -------------- | ------------------------------------------------------------ |
| knife4j.enable                                     | false          | 是否开启Knife4j增强模式                                      |
| knife4j.cors                                       | false          | 是否开启一个默认的跨域配置,该功能配合自定义Host使用          |
| knife4j.production                                 | false          | 是否开启生产环境保护策略,详情参考文档                        |
| knife4j.basic                                      |                | 对Knife4j提供的资源提供BasicHttp校验,保护文档                |
| knife4j.basic.enable                               | false          | 关闭BasicHttp功能                                            |
| knife4j.basic.username                             |                | basic用户名                                                  |
| knife4j.basic.password                             |                | basic密码                                                    |
| knife4j.documents                                  |                | 自定义文档集合，该属性是数组                                 |
| knife4j.documents.group                            |                | 所属分组                                                     |
| knife4j.documents.name                             |                | 类似于接口中的tag,对于自定义文档的分组                       |
| knife4j.documents.locations                        |                | markdown文件路径,可以是一个文件夹(classpath:markdowns/*)，也可以是单个文件(classpath:md/sign.md) |
| knife4j.setting                                    |                | 前端Ui的个性化配置属性                                       |
| knife4j.setting.enableAfterScript                  | true           | 调试Tab是否显示AfterScript功能,默认开启                      |
| knife4j.setting.language                           | zh-CN          | Ui默认显示语言,目前主要有两种:中文(zh-CN)、英文(en-US)       |
| knife4j.setting.enableSwaggerModels                | true           | 是否显示界面中SwaggerModel功能                               |
| knife4j.setting.swaggerModelName                   | Swagger Models | 重命名SwaggerModel名称,默认                                  |
| knife4j.setting.enableDocumentManage               | true           | 是否显示界面中"文档管理"功能                                 |
| knife4j.setting.enableReloadCacheParameter         | false          | 是否在每个Debug调试栏后显示刷新变量按钮,默认不显示           |
| knife4j.setting.enableVersion                      | false          | 是否开启界面中对某接口的版本控制,如果开启，后端变化后Ui界面会存在小蓝点 |
| knife4j.setting.enableRequestCache                 | true           | 是否开启请求参数缓存                                         |
| knife4j.setting.enableFilterMultipartApis          | false          | 针对RequestMapping的接口请求类型,在不指定参数类型的情况下,如果不过滤,默认会显示7个类型的接口地址参数,如果开启此配置,默认展示一个Post类型的接口地址 |
| knife4j.setting.enableFilterMultipartApiMethodType | POST           | 具体接口的过滤类型                                           |
| knife4j.setting.enableHost                         | false          | 是否启用Host                                                 |
| knife4j.setting.enableHomeCustom                   | false          | 是否开启自定义主页内容                                       |
| knife4j.setting.homeCustomLocation                 |                | 主页内容Markdown文件路径                                     |
| knife4j.setting.enableSearch                       | false          | 是否禁用Ui界面中的搜索框                                     |
| knife4j.setting.enableFooter                       | true           | 是否显示Footer                                               |
| knife4j.setting.enableFooterCustom                 | false          | 是否开启自定义Footer                                         |
| knife4j.setting.footerCustomContent                | false          | 自定义Footer内容                                             |
| knife4j.setting.enableDynamicParameter             | false          | 是否开启动态参数调试功能                                     |
| knife4j.setting.enableDebug                        | true           | 启用调试                                                     |
| knife4j.setting.enableOpenApi                      | true           | 显示OpenAPI规范                                              |
| knife4j.setting.enableGroup                        | true           | 显示服务分组                                                 |

以下增强功能都需要

#### 接口添加作者

用于查看谁实现的接口

使用方式：添加注解 `@ApiOperationSupport(author = "胡桃最棒")`

```java
@ApiImplicitParam(name = "name",value = "姓名",required = true)
@ApiOperationSupport(author = "胡桃最棒")
@ApiOperation("hello方法")
@GetMapping("/hello")
public String hello(@RequestParam("name") String name){
    return "hello "+name;
}
```

![image-20220207084050177](../../../../../../../Pictures/assets/SpringBoot笔记/202202070840949.png)

#### 访问权限控制

虽然 knife4j给我们提供了很方便的在线接口文档，俗话说的好，凡事都具有两面性，有利自然也有弊，那就是在生茶环境上，也会显示出接口文档，这是非常危险的一件事情，问题如下：

- 系统部署生产环境时,我们想屏蔽Swagger的文档功能,不管是接口或者html文档
- 通常我们有时候需要生产环境部署后,又需要Swagger的文档调试功能,辅助开发者调试,但是存在安全隐患,没有对Swagger的资源接口过滤

Knife4j 基于 Servlet 体系提供了过滤 Filter 功能,如果开发者使用 Spring Boot 开发框架进行开发的话,只需在`application.properties`或者`application.yml`配置文件中配置相关属性即可方便的解决上面的问题,不用删除 Springfox-swagger 的 jar 包或者删除相关代码等复杂的操作,提升开发体验。

#### 资源屏蔽

目前`Springfox-Swagger`以及`Knife4j`提供的资源接口包括如下

| 资源                                      | 说明                                          |
| ----------------------------------------- | --------------------------------------------- |
| /doc.html                                 | Knife4j提供的文档访问地址                     |
| /v2/api-docs-ext                          | Knife4j提供的增强接口地址,自`2.0.6`版本后删除 |
| /swagger-resources                        | Springfox-Swagger提供的分组接口               |
| /v2/api-docs                              | Springfox-Swagger提供的分组实例详情接口       |
| /swagger-ui.html                          | Springfox-Swagger提供的文档访问地址           |
| /swagger-resources/configuration/ui       | Springfox-Swagger提供                         |
| /swagger-resources/configuration/security | Springfox-Swagger提供                         |

项目发布到生产环境之后，我们需要屏蔽 swagger 相关的资源，由于 Knife4j 基于 Servlet 体系提供了过滤 Filter 功能，所以就不需要我们再去造轮子了，直接使用即可。

springboot 只需要在配置文件中做如下修改即可

```yaml
knife4j:
  # 开启增强配置 
  enable: true
　# 开启生产环境屏蔽
  production: true
```

然后重启项目

![image-20220207084512721](../../../../../../../Pictures/assets/SpringBoot笔记/202202070845305.png)

如果看到如下信息，说明资源已经屏蔽成功，但是你又不想在生产环境中屏蔽 swagger 资源，只想给一部分人使用，也是可以的，加入权限校验即可。

#### 访问页面加权控制

针对Swagger的资源接口,`Knife4j`提供了简单的**Basic认证功能**

简单点说，指定一个用户名和密码，访问 Swagger 文档需要验证登录名和密码，验证通过之后才能正常访问。

knife4 允许开发者在配置文件（application.yml/properties）中增加一组用户名和密码。

```yaml
knife4j:
  # 开启增强配置 
  enable: true
　# 开启Swagger的Basic认证功能,默认是false
  basic:
      enable: true
      # Basic认证用户名
      username: test
      # Basic认证密码
      password: 123
```

如果用户开启了 basic （knife4j.basic.enable = true）认证功能，但是没有指定 username 和password，那么 knife4j 提供了一组默认的用户名密码

配置好application.yml 文件之后，我们再次重启项目（这个时候需要将之前设置的资源屏蔽需要去掉哦）

![image-20220207084656872](../../../../../../../Pictures/assets/SpringBoot笔记/202202070846052.png)

#### 接口排序

们在开发中，一个 controller 中往往会存在很多的接口，这样我们在文档查找的时候就会变得很苦恼，所以 knife4j 在 `@ApiOperationSupport`注解中增加了 order 字段,用于接口排序。

**在使用此注解之前需要开启增强功能。**

```java
@Api(tags = "HelloController层")
@RestController
public class HelloController {

    @ApiImplicitParam(name = "name",value = "姓名",required = true)
    @ApiOperationSupport(author = "胡桃最棒",order = 1)
    @ApiOperation("hello方法")
    @GetMapping("/hello")
    public String hello(@RequestParam("name") String name){
        return "hello "+name;
    }

    @ApiOperation(value = "用户接口方法",notes = "方法描述")
    @ApiOperationSupport(author = "胡桃最棒",order = 2)
    @RequestMapping("/user")
    public User getUser(@ApiParam("用户名") String username,@ApiParam("密码") String password){
        return new User(username,password);
    }

}
```

![image-20220207084938784](../../../../../../../Pictures/assets/SpringBoot笔记/image-20220207084938784.png)

[更多方法，查看该文档](https://doc.xiaominfo.com/knife4j/documentation/)

## 任务

### 前言

在工作中，常常会用到异步处理任务，比如在网站上发送邮件，后台会去发送邮件，此时前台会造成响应不动，直到邮件发送完毕，响应才会成功，所以一般会采用多线程的方式去处理这些任务。

还有一些定时任务，比如需要在每天凌晨的时候，分析一次前一天的日志信息。

还有就是邮件的发送，微信的前身也是邮件服务呢？这些东西都是怎么实现的呢？其实SpringBoot都给提供了对应的支持，上手使用十分的简单，只需要开启一些注解支持，配置一些配置文件即可！

### 异步任务

* 创建一个新项目`springboot-08-schedule`，添加web基础模块

* 创建一个service包
* 创建一个类AsyncService

异步处理还是非常常用的，比如在网站上发送邮件，后台会去发送邮件，此时前台会造成响应不动，直到邮件发送完毕，响应才会成功，所以一般会采用多线程的方式去处理这些任务。

编写方法，假装正在处理数据，使用线程设置一些延时，模拟同步等待的情况；

```java
@Service
public class AsyncService {

    public void test(){
        try {
            Thread.sleep(3000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("正在等待中~");
    }
}
```

* 编写controller包
* 编写AsyncController类

```java
@RestController
public class AsyncController {

    @Autowired
    AsyncService asyncService;

    @GetMapping("/test")
    public String test(){
        asyncService.test();
        return "success";
    }
}
```

* 访问http://localhost:8080/test进行测试，3秒后出现success，这是同步等待的情况。

问题：如果想让用户直接得到消息，就在后台使用多线程的方式进行处理即可，但是每次都需要自己手动去编写多线程的实现的话，太麻烦了，只需要用一个简单的办法，在方法上加一个简单的注解即可。

* 给test方法添加@Async注解；

```java
@Service
public class AsyncService {

    //告诉Spring这是一个异步方法
    @Async
    public void test(){
        try {
            Thread.sleep(3000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("正在等待中~");
    }
}
```

SpringBoot就会自己开一个线程池，进行调用！但是要让这个注解生效，还需要在主程序上添加一个注解@EnableAsync ，开启异步注解功能；

```java
@EnableAsync //开启异步注解功能
@SpringBootApplication
public class Springboot09ScheduleApplication {

    public static void main(String[] args) {
        SpringApplication.run(Springboot09ScheduleApplication.class, args);
    }

}
```

* 重启测试，网页瞬间响应，后台代码依旧执行！

### 定时任务

项目开发中经常需要执行一些定时任务，比如需要在每天凌晨的时候，分析一次前一天的日志信息，Spring为我们提供了异步执行任务调度的方式，提供了两个接口。

- TaskExecutor接口
- TaskScheduler接口

两个注解：

- @EnableScheduling
- @Scheduled

**cron表达式：**

![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070914986)



![Image](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921239)

**测试步骤：**

1、创建一个ScheduledService

里面存在一个hello方法，他需要定时执行，怎么处理呢？

```java
@Service
public class ScheduledService {

    //秒   分   时     日   月   周几
    //0 * * * * MON-FRI
    //注意cron表达式的用法；
    @Scheduled(cron = "0 * * * * 0-7")
    public void hello(){
        System.out.println("hello.....");
    }
}
```

* 这里写完定时任务之后，需要在主程序上增加@EnableScheduling 开启定时任务功能

```java
@EnableAsync //开启异步注解功能
@EnableScheduling //开启基于注解的定时任务
@SpringBootApplication
public class Springboot09ScheduleApplication {
    public static void main(String[] args) {
        SpringApplication.run(Springboot09ScheduleApplication.class, args);
    }
}
```

* 详细了解下cron表达式：http://www.bejson.com/othertools/cron/
* 常用的表达式

```
（1）0/2 * * * * ?   表示每2秒 执行任务
（1）0 0/2 * * * ?   表示每2分钟 执行任务
（1）0 0 2 1 * ?   表示在每月的1日的凌晨2点调整任务
（2）0 15 10 ? * MON-FRI   表示周一到周五每天上午10:15执行作业
（3）0 15 10 ? 6L 2002-2006   表示2002-2006年的每个月的最后一个星期五上午10:15执行作
（4）0 0 10,14,16 * * ?   每天上午10点，下午2点，4点
（5）0 0/30 9-17 * * ?   朝九晚五工作时间内每半小时
（6）0 0 12 ? * WED   表示每个星期三中午12点
（7）0 0 12 * * ?   每天中午12点触发
（8）0 15 10 ? * *   每天上午10:15触发
（9）0 15 10 * * ?     每天上午10:15触发
（10）0 15 10 * * ?   每天上午10:15触发
（11）0 15 10 * * ? 2005   2005年的每天上午10:15触发
（12）0 * 14 * * ?     在每天下午2点到下午2:59期间的每1分钟触发
（13）0 0/5 14 * * ?   在每天下午2点到下午2:55期间的每5分钟触发
（14）0 0/5 14,18 * * ?     在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发
（15）0 0-5 14 * * ?   在每天下午2点到下午2:05期间的每1分钟触发
（16）0 10,44 14 ? 3 WED   每年三月的星期三的下午2:10和2:44触发
（17）0 15 10 ? * MON-FRI   周一至周五的上午10:15触发
（18）0 15 10 15 * ?   每月15日上午10:15触发
（19）0 15 10 L * ?   每月最后一日的上午10:15触发
（20）0 15 10 ? * 6L   每月的最后一个星期五上午10:15触发
（21）0 15 10 ? * 6L 2002-2005   2002年至2005年的每月的最后一个星期五上午10:15触发
（22）0 15 10 ? * 6#3   每月的第三个星期五上午10:15触发
```

### 邮件任务

邮件发送，在日常开发中，也非常的多，Springboot也帮我们做了支持

- 邮件发送需要引入spring-boot-start-mail
- SpringBoot 自动配置MailSenderAutoConfiguration
- 定义MailProperties内容，配置在application.yml中
- 自动装配JavaMailSender
- 测试邮件发送

**测试：**

1. 引入pom依赖

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

看它引入的依赖，可以看到 jakarta.mail

```xml
<dependency>
   <groupId>com.sun.mail</groupId>
   <artifactId>jakarta.mail</artifactId>
   <version>1.6.4</version>
   <scope>compile</scope>
</dependency>
```

* 查看自动配置类：MailSenderAutoConfiguration

![image-20220207092111823](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070921240.png)

点击`MailSenderJndiConfiguration`，这个类中存在bean，`JavaMailSenderImpl`

![image-20220207092935036](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070929211.png)

然后去看下配置文件

![image-20220207093121954](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070931474.png)

* 配置文件

```properties
spring.mail.username=xxxxx@qq.com
spring.mail.password=你的qq授权码
spring.mail.host=smtp.qq.com
# qq需要配置ssl
spring.mail.properties.mail.smtp.ssl.enable=true
```

* 获取QQ授权码：在QQ邮箱中的设置->账户->开启pop3和smtp服务

![image-20220207094035800](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070940371.png)

* Spring单元测试

```java
@SpringBootTest
class Springboot09ScheduleApplicationTests {

    @Autowired
    JavaMailSenderImpl mailSender;

    @Test
    public void contextLoads() {
        //邮件设置1：一个简单的邮件
        SimpleMailMessage message = new SimpleMailMessage();
        message.setSubject("通知-明天来狂神这听课");
        message.setText("今晚7:30开会");

        message.setTo("24736743@qq.com");
        message.setFrom("24736743@qq.com");
        mailSender.send(message);
    }

    @Test
    public void contextLoads2() throws MessagingException {
        //邮件设置2：一个复杂的邮件
        MimeMessage mimeMessage = mailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage, true);

        helper.setSubject("通知-明天来狂神这听课");
        helper.setText("<b style='color:red'>今天 7:30来开会</b>",true);

        //发送附件
        helper.addAttachment("1.jpg",new File(""));
        helper.addAttachment("2.jpg",new File(""));

        helper.setTo("24736743@qq.com");
        helper.setFrom("24736743@qq.com");

        mailSender.send(mimeMessage);
    }

}
```

查看邮箱，邮件接收成功！

>  只需要使用Thymeleaf进行前后端结合即可开发自己网站邮件收发功能了！

## 集成Redis

### 准备工作

* 创建新项目`springboot-10-redis`

![image-20220207095638339](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202070956107.png)



* 导入依赖（前面勾选了，不需要导入了）

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

> 在 SpringBoot 2.X 之后，原来的 Jedis 被替换为了 lettuce
>
> **Jedis 和 lettuce 区别**
>
> Jedis ：采用的是直连的服务，如果有多个线程操作的话是不安全的，就需要使用 Jedis Pool 连接池取解决。问题就会比较多。
>
> lettuce ：底层采用 Netty ，实例可以在多个线程中共享，不存在线程不安全的情况。可以减少线程数据了，性能更高。

* 配置`application.yaml`

```yaml
# 配置 Redis
spring:
  redis:
    host: localhost
    port: 6379
```

* Spring单元测试

```java
@SpringBootTest
class Springboot10RedisApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        /** redisTemplate 操作不同的数据类型，API 和 Redis 中的是一样的
         * opsForValue 类似于 Redis 中的 String
         * opsForList 类似于 Redis 中的 List
         * opsForSet 类似于 Redis 中的 Set
         * opsForHash 类似于 Redis 中的 Hash
         * opsForZSet 类似于 Redis 中的 ZSet
         * opsForGeo 类似于 Redis 中的 Geospatial
         * opsForHyperLogLog 类似于 Redis 中的 HyperLogLog
         */

        // 除了基本的操作，常用的命令都可以直接通过redisTemplate操作，比如事务……

        // 和数据库相关的操作都需要通过连接操作
        //RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
        //connection.flushDb();

        redisTemplate.opsForValue().set("key", "呵呵");
        System.out.println(redisTemplate.opsForValue().get("key"));
    }

}
```

### 自定义配置类

* `RedisConfig.java`，从`RedisAutoConfiguration`中粘贴过来`RedisTemplate`，进行修改

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        return template;
    }

}
```

### 序列化

* 导入json相关依赖

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.13.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.13.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.13.1</version>
</dependency>
```

* 创建实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String username;
    private int age;
}
```

* 编写测试类，先不序列化

```java
@Test
public void test() throws JsonProcessingException {
    // 真实开发都是使用json来传递对象
    User user = new User("胡桃",18);
    String jsonUser = new ObjectMapper().writeValueAsString(user);
    redisTemplate.opsForValue().set("user", jsonUser);
    Object userData = redisTemplate.opsForValue().get("user");
    System.out.println(userData);
}
```

![image-20220207103928718](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202071039741.png)

> 有时候会报错，如果序列化就不会报错

虽然在 Java 端可以看到返回了中文，但是在 Redis 中查看是一串乱码，因此需要序列化。

![image-20220207105413428](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202071054271.png)

* 配置`RedisConfig.java`

```java
@Configuration
public class RedisConfig {
    /**
     *  编写自定义的 redisTemplate
     *  这是一个比较固定的模板
     */
    @Bean
    @SuppressWarnings("all")
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        // 为了开发方便，直接使用<String, Object>
        RedisTemplate<String, Object> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);

        // Json 配置序列化
        // 使用 jackson 解析任意的对象
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);
        // 使用 objectMapper 进行转义
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance, ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
        // String 的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        // key 采用 String 的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        // Hash 的 key 采用 String 的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        // value 采用 jackson 的序列化方式
        template.setValueSerializer(jackson2JsonRedisSerializer);
        // Hash 的 value 采用 jackson 的序列化方式
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        // 把所有的配置 set 进 template
        template.afterPropertiesSet();

        return template;
    }
}
```

* 单元测试

```java
@SpringBootTest
class Springboot10RedisApplicationTests {

    @Autowired
    @Qualifier("redisTemplate") // 如果多个重名的，可以通过Qualifier指定
    private RedisTemplate redisTemplate;

    @Test
    public void test() throws JsonProcessingException {
        // 真实开发都是使用json来传递对象
        User user = new User("胡桃",18);
        String jsonUser = new ObjectMapper().writeValueAsString(user);
        redisTemplate.opsForValue().set("user", jsonUser);
        Object userData = redisTemplate.opsForValue().get("user");
        System.out.println(userData);

    }
}
```

![image-20220207105505452](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202071055595.png)

### 工具类

[RedisUtil.java](./工具类/RedisUtil.md)

## 总结

````
一、三层架构 + MVC
	架构 ——>解耦

开发框架
	Spring
		IOC AOP
		IOC：控制反转
             约泡：
                泡温泉，泡茶…，泡友
                （传统方式）附近的人，打招呼。加微信，聊天，天天聊… ——>约泡
                浴场（容器）：温泉，茶庄，泡友
                直接进温泉，就有人和你一起了！
                原来我们都是自己一步步操作，现在交给容器了！我们需要什么就去拿就行了
		AOP：切面（本质，动态代理）
		

	为了解决什么？不影响业务本来的情况下，实现动态增加功能。
	大量应用在日志，事务…等方面

	Spring是一个轻量级的Java开源框架，容器
	目的：解决企业开发的复杂性问题
	Spring是春天，开始觉得是春天，也十分复杂，大量配置文件！



SpringBoot
	SpringBoot并不是新东西，就是Spring的升级版！
	新一代JavaEE的开发标准,开箱即用！	——>拿过来就可以用！
	它自动帮我们配置了非常多的东西，我们拿来即用！
	特点：约定大于配置!
		比如静态资源就要放到static下，不然就不让用【类似maven】

随着公司体系越来越大，用户越来越多！



二、微服务架构 ——>新的架构
模块化，功能化！
【这里所有功能都在一块，无论人多不多，我都可以横向扩展，发生假设1问题，就负载均衡一下】
用户，支付，签到，娱乐…【以前这些模块都写到一个项目里面】
但是人过于多的时候：一台服务器解决不了！就在增加一台服务器【横向】

假设1：A服务器占用98%资源，B服务器占用10%资源。 ——>负载均衡【解决方法,它里面的算法会将请求分配，让两个服务器变成50%】

【将原来的整体项目，分成模块化，用户就是一个单独的项目，签到也是一个单独的项目，项目与项目之间需要通信】

假设2：用户非常多！而签到非常少	——>解决：给用户多一点服务器，给签到少一点服务器
微服务架构问题？

分布式架构会遇到的四个核心问题？
    1. 这么多服务，客户端应该如何去访问？【一个共同的接口来处理它，类似网关】
    2. 这么多服务，服务之间如何进行通信？
    3. 这么多服务，如何治理呢？:如何进行统一的管理【解决：统一的服务管理平台——>Zookeeper（注册中心）】
    4. 服务挂了怎么办？

基于以上问题，有一些解决方案：
SpringCloud，是一套生态，就是来解决以上分布式架构的4个问题
想使用SpringCloud，必须要掌握SpringBoot【因为SpringCloud是基于SpringBoot的】

1. Spring Cloud NetFlix，出来了一套解决方案！一站式解决方案，我们都可以直接在这里拿【淘汰】
   问题1的解决：Api网关，zuul组件
    问题2的解决：Feign ——> 基于HttpClient ——> Http的通信方式,同步并阻塞
    问题3的解决：服务注册与发现，Eureka组件
    问题4的解决：熔断机制，Hystrix
    ****2018年年底，NetFlix宣布无限期停止维护，生态不在维护，产生脱节。

2. Apache Dubbo zookeeper，第二套解决系统
   问题1的解决：API没有！	——>要么找第三方组件，要不自己实现
   问题2的解决：Dubbo是一个高性能的基于Java实现的 RPC通信框架！【写一个Java类，调用一下】
   问题3的解决：服务注册与发现，zooKeeper[动物园管理者]——>比如（Hadoop，Hive）
   问题4的解决：熔断机制没有！	——>借助了Hystrix
   ****不完善，Dubbo3.0

3. SpringCloud Alibaba，一站式解决方案！

   三、设想：提出一个方案——>服务网格
   服务网格：下一代微服务标准，Service Mesh
   代表解决方案：istio

万变不离其宗，一通百通！

1. API网关问题，服务路由
2. HTTP或者RPC框架，异步调用
3. 服务注册与发现，高可用
4. 熔断机制，服务降级

为什么要解决这些问题？——>网络不可靠
````

