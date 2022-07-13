# Spring源码剖析笔记

## Spring概念

### Spring编程思想

| Spring思想 | 应用场景（特点）                                             | 一句话归纳                 |
| ---------- | ------------------------------------------------------------ | -------------------------- |
| OOP        | Object Oriented Programming（面向对象编 程）用程序归纳总结生活中一切事物。 | 封装、继承、多态。         |
| BOP        | Bean Oriented Programming(面向Bean编 程）面向Bean（普通的Java类）设计程序， 解放程序员。 | 一切从Bean开始。           |
| AOP        | Aspect Oriented Programming(面向切面编 程)找出多个类中有一定规律的代码，开发时拆 开，运行时再合并。 面向切面编程，即面向规则编程。 | 解耦，专人做专事。         |
| lOC        | lnversion of Control（控制反转） 将new对象的动作交给Spring管理，并由\| Spring 保存已创建的对象(IOC容器）。 | 转交控制权（即控制权反转） |
| DI/DL      | Dependency lnjection ( 依赖注入)或者Dependency Lookup（依赖查找)依赖注入、依赖查找，Spring不仅保存自己创建的对象，而且保存对象与对象之间的关系。注入即赋值，**主要三种方式构造方法、set方法、通过属性直接赋值。** | 赋值                       |

### Spring的设计模式

* 工厂模式
* 单例模式
* 代理模式
* 模板方法
* 观察者模式
* 装饰器模式
* 适配器模式

## Spring5注解编程基础组件

### 配置组件Configure Components

| 注解名称       | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| @Configuration | 把一个类作为一个loC容器，它的某个方法头上如果注册了@Bean，就会作为这个Spring容器中 的Bean. |
| @ComponentScan | 在配置类上添加@ComponentScan注解。该注解默认会扫描该类所在的包下所有的配置类， 相当于之前的``<context:component-scan>``。 |
| @Scope         | 用于指定scope作用域的（用在类上)。                           |
| @Lazy          | 表示延迟初始化，即容器启动时不创建对象，调用对象的功能的时候才创建。 |
| @Conditional   | Spring4开始提供，它的作用是按照一定的条件进行判断，满足条件给容器注册Bean. |
| @lmport        | 导入外部资源                                                 |
| 生命周期控制   | PostConstruct用于指定初始化方法（用在方法上) PreDestory用于指定销毁方法（用在方法上) Dependson:定义Bean初始化及销毁时的顺序 |

#### ComponentScan扫描指定类型组件

```java
@Configuration
@ComponentScan(value = "project",
        includeFilters = {@Filter(type = FilterType.ANNOTATION,value = {Component.class})},
        useDefaultFilters = false)
public class MyConfig {
}
```

#### Scope

prototype：原型，多例

singleton：单例

#### Conditional

```java
public class WinConditional implements Condition {

    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {

        ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
        Environment environment = context.getEnvironment();
        String property = environment.getProperty("os.name");
        System.out.println(property);
        if(property.contains("Windows"))
            return true;
        return false;
    }
}
```

#### import

##### @Bean直接导入单个类

```java
@Import(value = {Cat.class})
```

##### @ComponentScan

@ComponentScan默认扫描**(@Controller、@Service、@Repostory、@Component)**

```java
@ComponentScan(value = "project",
        includeFilters = {@Filter(type = FilterType.CUSTOM,value = {MyTypeFilter.class})},
        useDefaultFilters = false)
```

筛选出指定组件

```java
public class MyTypeFilter implements TypeFilter {

    /**
     *
     * @param metadataReader：获取当前正在操作的类的信息
     * @param metadataReaderFactory：获取上下文中所有的信息
     * @return
     * @throws IOException
     */
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        // 获取当前类的所有注解信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        // 获取当前扫描到的类的信息
        ClassMetadata classMetadata = metadataReader.getClassMetadata();
        // 获取到当前类的所有的资源信息
        Resource resource = metadataReader.getResource();

        String className = classMetadata.getClassName();
        System.out.println("********************"+className+"********************");
        // 匹配当前类中是否含有er
        if(className.contains("er")){
            return true;
        }
        return false;
    }
}
```

##### Import快速给容器导入Bean的方式

```java
@Import(value = {Cat.class,MyImportSelector.class,MyImportBeanDefinitionRegistrar.class})
```

###### @Import

@Import直接参数导入

###### ImportSelector

实现ImportSelector自定义规则实现

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{"project.entity.CompanyA", "project.entity.Member", "project.entity.User"};
    }
}
```

###### ImportBeanDefinitionRegistrar

实现ImportBeanDefinitionRegistrar，获得BeanDefinitionRegistry可以于动直接往Toc容器中注入值。

```java
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        // 包里面如果声明了Company和Member这两个类，才把User对象注册到IOC容器中
        boolean company = registry.containsBeanDefinition("project.entity.Company");
        boolean member = registry.containsBeanDefinition("project.entity.Member");

        if(company && member){
            BeanDefinition beanDefinition = new RootBeanDefinition(User.class);
            registry.registerBeanDefinition("user",beanDefinition);
        }
    }

}
```

##### FactoryBean

FactoryBean把需要注朋的对象封装为FactoryBean

###### FactoryBean负责将Bean注册到IOC的Bean

```java
public class MyFactoryBean implements FactoryBean<Monkey> {
    @Override
    public Monkey getObject() throws Exception {
        return new Monkey();
    }

    @Override
    public Class<?> getObjectType() {
        return Monkey.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}
```

###### BeanFactory 从IOC容器中获得Bean对象

```java
@Bean
public MyFactoryBean monkey(){
    return new MyFactoryBean();
}
```

#### 生命周期

###### 配置@Bean的参数

```java
@Configuration
public class MyConfig {
    @Bean(initMethod = "addOil",destroyMethod = "close")
    public Car car(){
        return new Car();
    }
}
```

定义Car类

```java
public class Car {

    public Car(){
        System.out.println("调用Car的构造方法");
    }

    public void addOil(){
        System.out.println("加油");
    }

    public void close(){
        System.out.println("停车熄火");
    }

    public void run(){
        System.out.println("正在消耗汽油，运行中");
    }

}
```

###### 实现InitializingBean和DisposableBean接口

```java
@Component
public class Train implements InitializingBean, DisposableBean {
    @Override
    public void destroy() throws Exception {
        System.out.println("销毁火车对象");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("创建火车对象");
    }
}
```

##### 使用@PostConstruct和@PreDestroy注解

```java
@Component
public class AirPlane {
    public AirPlane(){
        System.out.println("调用AirPlane的构造方法");
    }

    @PostConstruct
    public void addOil(){
        System.out.println("加油");
    }

    public void run(){
        System.out.println("正在飞行");
    }

    @PreDestroy
    public void close(){
        System.out.println("降落熄火");
    }
}
```

##### 自定义类实现BeanPostProcessor接口

```java
@Component
public class MyBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessBeforeInitialization" + beanName + "," + bean);
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("postProcessAfterInitialization" + beanName + "," + bean);
        return bean;
    }
}
```

然后在配置类中，进行配置

```java
@ComponentScans({
    @ComponentScan("project.entity"),
    @ComponentScan("demo.annotation.lifecycle")
})
```

### 赋值（自动装配）组件

| 注解名称        | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| @Component      | 泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。 |
| @service        | 用于标注业务层组件                                           |
| @Controller     | 用于标注控制层组件                                           |
| @Repository     | 用于标注数据访问组件，即DAO组件。                            |
| @Value          | 普通数据类型赋值                                             |
| @Autowired      | **默认按类型装配，如果我们想使用按名称装配，可以结合@Qualifier注解一起使用** |
| @PropertySource | 读取配置文件赋值。`@PropertySource("classpath:application.properties")` |
| @Qualifier      | **如存在多个实例配合使用**                                   |
| @Primary        | **自动装配时当出现多个Bean候选者时，被注解为@Primary的Bean将作为首选者，否则将抛出异常** |
| @Resource       | **默认按名称装配，当找不到与名称匹配的bean才会按类型装配。** |

### 织入组件

| 注解名称                            | 说明                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| ApplicationContextAware             | 可以通过这个上下文环境对象得到Spring容器中的Bean             |
| BeanDefinitionRegistryPostProcessor | BeanDefinitionRegistryPostProcessor实现了 BeanFactoryPostProcessor接口，是Spring框架的 BeanDefinitionRegistry的后处理器，用来注册额外的 BeanDefinition |

### 切面组件

| 注解名称                     | 说明                 |
| ---------------------------- | -------------------- |
| @EnableTransactionManagement | 添加对事务管理的支持 |
| @Transactional               | 配置声明式事务信息   |

## 环境配置

### 安装lombok

* lombok依赖

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.22</version>
</dependency>
```

* lombok插件

![image-20220508132713779](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032095.png)

### 配置注解处理器

在如下设置界面单击Build, Execution, Deployment→Compiler→Annotation Processors，然后在右侧勾选Enable annotation processing复选项即可。

![image-20220508132835201](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032096.png)

## 用300行代码概览Spring

### 绪论

初始Spring的源码世界，用300行代码最简洁的代码提炼Spring的基本设计思想，从而掌握Spring框架的基本脉络。

### 整体架构

- **Spring DI**：怎么给对象自动赋值，循环依赖注入（组合复用原则）
- **Spring IOC**：工厂怎么把对象创建出来，交给用户的（工厂模式、原型模式、单例模式）
- **Spring MVC**：用户输入URL怎样和Java代码关联，MVC九大组件（委派模式、策略模式、解释器模式）
- **Spring AOP**：面向切面设计，解耦（责任链模式、动态代理）
- **Spring JDBC ORM**：基于Spring JDBC，手写一个ORM框架（模板方法模式、建造者模式）

## 手写SpringV1.0版本

#### 实现思路

现在先看下Mini版的Spring实现的基本思路：

![image-20220508135708998](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032097.png)

#### 自定义配置

##### 初始化工作

* 创建`SpringCode`工程，然后添加web支持。

* 添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.22</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
</dependencies>
```

##### 配置web.xml

所有依赖于web容器的项目，都是从读取web.xml文件开始的。因此需要先配置好web.xml中的内容。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <display-name>Web Application</display-name>
    <servlet>
        <servlet-name>dispatchServlet</servlet-name>
        <servlet-class>com.study.myspring.mvcframework.v1.servlet.MyDispatchServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>application.properties</param-value>
        </init-param>

        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatchServlet</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>
</web-app>
```

其中，MyDispatchServlet是自己模拟Spring实现的核心功能类。

##### 配置application

```properties
scanPackage = com.study.myspring.demo
```

##### 自定义Annotation

* @Service

```java
@Target({ElementType.TYPE, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyService {
    String value() default "";
}
```

* @Autowired

```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyAutowired {
    String value() default "";
}
```

* @Controller

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyController {
    String value() default "";
}
```

* @RequestMapping

```java
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyRequestMapping {
    String value() default "";
}
```

* @RequestParam

```java
@Target({ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyRequestParam {
    String value() default "";
}
```

##### 配置Annotation

配置业务实现类DemoService

配置请求入口类DemoAction

##### 实现DispatchServlet类

实现DispatchServlet类，其继承HttpSevlet类，重写`doGet`、`doPost`、`init`三个方法。

为了更好的学习，以及实现代码，先打好框架，逐步实现。

```java
public class MyDispatchServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        // 6. 根据URL 委派调用具体的方法
        doDispatch();

    }

    @Override
    public void init() throws ServletException {
        // 1. 加载配置文件
        doLoadConfig();

        // 2. 扫描相关的类
        doScannerClass();

        // 3. 初始化IOC容器，将扫描到的类，进行实例化，然后缓存到IOC容器中
        doInstance();

        // 4. 完成依赖注入
        doAutowired();

        // 5. 初始化HandlerMapping == MVC功能
        doInitHandlerMapping();

        System.out.println("My Spring framework is init.");
    }
}
```

### 加载配置文件

```java
// 根据contextConfigLocation的路径去ClassPath下找到对应的配置文件
private void doLoadConfig(String contextConfigLocation) {
    InputStream resourceAsStream = this.getClass().getClassLoader().getResourceAsStream(contextConfigLocation);
    try {
        contextConfig.load(resourceAsStream);
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (resourceAsStream != null) {
            try {
                resourceAsStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

    }
}
```

### 扫描相关类

```java
// 扫描ClassPath下符合包路径规则所有的Class文件
private void doScanner(String scanPackage) {
    URL url = this.getClass().getClassLoader().getResource("/" + scanPackage.replaceAll("\\.", "/"));
    File classpath = new File(url.getFile());
    for (File file : classpath.listFiles()) {

        if (file.isDirectory()) {
            doScanner(scanPackage + "." + file.getName());
        } else {

            // 取反，减少代码的嵌套
            if (!file.getName().endsWith(".class")) {
                continue;
            }

            // 包名.类名 => 完整类名
            String className = (scanPackage + "." + file.getName().replace(".class", ""));

            classNames.add(className);
        }

    }
}
```

### IOC容器

```java
private void doInstance() {
    if (classNames.isEmpty()) return;


    try {
        for (String className : classNames) {
            Class<?> clazz = Class.forName(className);

            if(clazz.isAnnotationPresent(MyController.class)){

                Object instance = clazz.newInstance();

                String beanName = toLowerFirstCase(clazz.getSimpleName());

                if(ioc.containsKey(beanName)){
                    throw new RuntimeException("必须保证bean保证唯一");
                }

                ioc.put(beanName,instance);
            }else if(clazz.isAnnotationPresent(MyService.class)){
                // 1.默认类名首字母小写
                String beanName = toLowerFirstCase(clazz.getSimpleName());

                // 2.如果在多个包下出现了相同的类名，优先使用别名（自定义名）
                MyService myService = clazz.getAnnotation(MyService.class);
                if(!"".equals(myService.value())){
                    beanName = myService.value();
                }

                Object instance = clazz.newInstance();
                ioc.put(beanName,instance);

                // 3. 如果是接口，只能初始化其实现类
                for(Class<?>i:clazz.getInterfaces()){

                    if(ioc.containsKey(i.getName())){
                        throw new RuntimeException("每个接口只能实现一个实现类，再多就不行了，或者使用别名");
                    }

                    ioc.put(i.getName(),instance);
                }
            }
        }
    } catch (ClassNotFoundException | InstantiationException | IllegalAccessException e) {
        e.printStackTrace();
    }
}

private static String toLowerFirstCase(String simpleName) {

    char[] chars = simpleName.toCharArray();

    chars[0] = chars[0] < 'a'? (char) (chars[0] + 'a' - 'A') :chars[0];

    return String.valueOf(chars);
}
```

### DI依赖注入

```java
private void doAutowired() {
    if(ioc.isEmpty()) return;
    for(Map.Entry<String,Object> entry:ioc.entrySet()){
        for(Field field:entry.getValue().getClass().getDeclaredFields()){
            if(!field.isAnnotationPresent(MyAutowired.class)) continue;

            MyAutowired myAutowired = field.getAnnotation(MyAutowired.class);
            String beanName = myAutowired.value().trim();
            if("".equals(beanName)){

                beanName = field.getType().getName();

            }

            // 关闭检查
            field.setAccessible(true);

            try {
                field.set(entry.getValue(),ioc.get(beanName));
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }

        }
    }
}
```

### MVC：URL映射

```java
private void doInitHandlerMapping() {
    if(ioc.isEmpty()) return;

    for(Map.Entry<String,Object> entry:ioc.entrySet()){

        Class<?> clazz = entry.getValue().getClass();

        if(!clazz.isAnnotationPresent(MyController.class)) continue;

        String baseUrl = "";

        if(clazz.isAnnotationPresent(MyRequestMapping.class)){
            baseUrl = clazz.getAnnotation(MyRequestMapping.class).value();
        }

        // 只迭代public方法
        for(Method method:clazz.getMethods()){

            if(!method.isAnnotationPresent(MyRequestMapping.class)) continue;

            MyRequestMapping myRequestMapping = method.getAnnotation(MyRequestMapping.class);

            String url = ("/" + baseUrl + "/" + myRequestMapping.value()).replaceAll("/+","/");

            handlerMapping.put(url,method);

            System.out.println("Mapped:"+ url + "-->" + method);

        }

    }
}

private void doDispatch(HttpServletRequest request,HttpServletResponse response) throws Exception {

    String url = request.getRequestURI();

    String contextPath = request.getContextPath();

    url = url.replaceAll(contextPath,"").replaceAll("/+","/");

    if(!this.handlerMapping.containsKey(url)) {
        response.getWriter().write("404 Not Found");
        return;
    }

    Method method = (Method) this.handlerMapping.get(url);

    Map<String,String[]> params = request.getParameterMap();

    String beanName = toLowerFirstCase(method.getDeclaringClass().getSimpleName());
    method.invoke(ioc.get(beanName),new Object[]{request,response,params.get("name")[0]});
}
```

优化dispatch，动态匹配参数

```java
private void doDispatch(HttpServletRequest request,HttpServletResponse response) throws Exception {

    System.out.println("正在进行调用controller");

    String url = request.getRequestURI();

    String contextPath = request.getContextPath();

    url = url.replaceAll(contextPath,"").replaceAll("/+","/");

    System.out.println(url);

    if(!this.handlerMapping.containsKey(url)) {
        response.getWriter().write("404 Not Found");
        return;
    }

    Method method = (Method) this.handlerMapping.get(url);

    //        Map<String,String[]> params = request.getParameterMap();

    // 1. 先把形参的位置和参数名字建立映射关系，并且缓存下来
    Map<String,Integer> paramIndexMap = new HashMap<>();

    Annotation[][] annotations = method.getParameterAnnotations();
    for(int i=0;i<annotations.length;i++){
        for(Annotation a:annotations[i]){

            if(a instanceof MyRequestParam){
                String paramName = ((MyRequestParam) a).value();
                if(!paramName.trim().equals("")){
                    paramIndexMap.put(paramName,i);
                }
            }

        }
    }
    Class<?>[] parameterTypes = method.getParameterTypes();
    for(int i=0;i<parameterTypes.length;i++){
        Class<?> parameterType = parameterTypes[i];
        if(parameterType == HttpServletRequest.class || parameterType == HttpServletResponse.class){
            paramIndexMap.put(parameterType.getName(),i);
        }
    }

    // 2. 根据参数位置匹配参数名字，从url中取到参数名字对应的值
    Object[] paramValues = new Object[parameterTypes.length];
    Map<String,String[]> params = request.getParameterMap();

    for(Map.Entry<String, String[]> param:params.entrySet()){

        String value = Arrays.toString(param.getValue())
            .replaceAll("\\[\\]]","")
            .replaceAll("\\s","");

        if(!paramIndexMap.containsKey(param.getKey())) continue;

        int index = paramIndexMap.get(param.getKey());

        // 类型强制转换
        paramValues[index] = value;

    }

    // 如果使用了注解
    if(paramIndexMap.containsKey(HttpServletRequest.class.getName())){
        int index = paramIndexMap.get(HttpServletRequest.class.getName());
        paramValues[index] = request;
    }

    if(paramIndexMap.containsKey(HttpServletResponse.class.getName())){
        int index = paramIndexMap.get(HttpServletResponse.class.getName());
        paramValues[index] = response;
    }

    String beanName = toLowerFirstCase(method.getDeclaringClass().getSimpleName());

    // 3. 组成动态实际参数列表，传给反射调用
    method.invoke(ioc.get(beanName),paramValues);
}
```

其中，Annotation[][] annos = method.getParameterAnnotations()；得到的结果是一个二维数组，因为参数前可以添加多个注解，一个参数上不可以添加相同的注解,同一个注解可以加在不同的参数上。

### 运行测试

启动tomcat，进行测试：http://localhost:8080/demo/query?name=1

![image-20220508194726643](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032098.png)

## 手写SpringV2.0版本

### 顶层设计IOC与DI

#### IOC容器中有什么

* Map容器
* BeanFactory工厂
* ApplicationContext上下文：持有BeanFactory引用，门面模式。
* BeanDefinitionReader解析器：负载解析所有的配置文件
* BeanDefinition 元信息，配置：XML、YAML、Annotation、Properties
* Bean实例，反射实例化Object：原生的Bean、代理的Bean
* BeanWrapper包装器模式：缓存到了IOC容器，缓存（持有了Bean的引用）

![image-20220509135430564](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032099.png)

![image-20220509190216363](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032100.png)

#### 代码详解

##### DispatcherServlet

```java
public class MyDispatcherServlet extends HttpServlet {


    private final Map<String,Object> handlerMapping = new HashMap<>();

    // ioc的容器访问上下文
    private MyApplicationContext applicationContext = null;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//        super.doGet(req, resp);
        this.doPost(req,resp);

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        // 6. 根据URL 委派调用具体的方法
        try {
            doDispatch(req,resp);
        } catch (Exception e) {
            e.printStackTrace();
            resp.getWriter().write("500 Exception,Detail:"+Arrays.toString(e.getStackTrace()));
        }

    }

    private void doDispatch(HttpServletRequest request,HttpServletResponse response) throws Exception {

        System.out.println("正在进行调用controller");

        String url = request.getRequestURI();

        String contextPath = request.getContextPath();

        url = url.replaceAll(contextPath,"").replaceAll("/+","/");

        System.out.println(url);

        if(!this.handlerMapping.containsKey(url)) {
            response.getWriter().write("404 Not Found");
            return;
        }

        // url对应的方法
        Method method = (Method) this.handlerMapping.get(url);

//        Map<String,String[]> params = request.getParameterMap();

        // 1. 先把形参的位置和参数名字建立映射关系，并且缓存下来
        Map<String,Integer> paramIndexMap = new HashMap<>();

        // 获取方法中的参数的注解，一个参数可能有多个注解
        Annotation[][] annotations = method.getParameterAnnotations();

        for(int i=0;i<annotations.length;i++){

            for(Annotation a:annotations[i]){

                if(a instanceof MyRequestParam){
                    String paramName = ((MyRequestParam) a).value();
                    if(!paramName.trim().equals("")){
                        paramIndexMap.put(paramName,i);
                    }
                }

            }
        }

        Class<?>[] parameterTypes = method.getParameterTypes();
        for(int i=0;i<parameterTypes.length;i++){
            Class<?> parameterType = parameterTypes[i];
            if(parameterType == HttpServletRequest.class || parameterType == HttpServletResponse.class){
                paramIndexMap.put(parameterType.getName(),i);
            }
        }

        // 2. 根据参数位置匹配参数名字，从url中取到参数名字对应的值
        Object[] paramValues = new Object[parameterTypes.length];
        Map<String,String[]> params = request.getParameterMap(); // 获取url参数

        for(Map.Entry<String, String[]> param:params.entrySet()){

            String value = Arrays.toString(param.getValue())
                    .replaceAll("\\[\\]]","")
                    .replaceAll("\\s","");

            if(!paramIndexMap.containsKey(param.getKey())) continue;

            int index = paramIndexMap.get(param.getKey());

            // 类型强制转换
            paramValues[index] = value;

        }

        // 如果使用了注解
        if(paramIndexMap.containsKey(HttpServletRequest.class.getName())){
            int index = paramIndexMap.get(HttpServletRequest.class.getName());
            paramValues[index] = request;
        }

        if(paramIndexMap.containsKey(HttpServletResponse.class.getName())){
            int index = paramIndexMap.get(HttpServletResponse.class.getName());
            paramValues[index] = response;
        }

        String beanName = toLowerFirstCase(method.getDeclaringClass().getSimpleName());

        // 3. 组成动态实际参数列表，传给反射调用
        method.invoke(applicationContext.getBean(beanName),paramValues);
    }

    @Override
    public void init() throws ServletException {

        System.out.println("Spring V2版本");

        ServletConfig config = getServletConfig();

        applicationContext = new MyApplicationContext(config.getInitParameter("contextConfigLocation"));

        // 5. 初始化HandlerMapping == MVC功能
        doInitHandlerMapping();

        System.out.println("My Spring framework is init.");
    }

    private void doInitHandlerMapping() {

        System.out.println("正在初始化映射方法");

        if(this.applicationContext.getBeanDefinitionCount() == 0) return;

        for(String beanName: this.applicationContext.getBeanDefinitionNames()){

            Object instance = applicationContext.getBean(beanName);

            Class<?> clazz = instance.getClass();

            if(!clazz.isAnnotationPresent(MyController.class)) continue;

            String baseUrl = "";

            if(clazz.isAnnotationPresent(MyRequestMapping.class)){
                baseUrl = clazz.getAnnotation(MyRequestMapping.class).value();
            }

            // 只迭代public方法
            for(Method method:clazz.getMethods()){

                if(!method.isAnnotationPresent(MyRequestMapping.class)) continue;

                MyRequestMapping myRequestMapping = method.getAnnotation(MyRequestMapping.class);

                String url = ("/" + baseUrl + "/" + myRequestMapping.value()).replaceAll("/+","/");

                handlerMapping.put(url,method);

                System.out.println("Mapped:"+ url + "-->" + method);

            }
        }
    }

    private static String toLowerFirstCase(String simpleName) {

        char[] chars = simpleName.toCharArray();

        chars[0] = chars[0] < 'a'? (char) (chars[0] + 'a' - 'A') :chars[0];

        return String.valueOf(chars);
    }
}
```

##### BeanFactory

```java
// 创建对象工厂的最顶层的接口
public interface MyBeanFactory {

    Object getBean(Class<?> beanClass);

    Object getBean(String beanName);

}
```

##### BeanDefinition

```java
public class MyBeanDefinition {

    public boolean isLazyInit(){
        return false;
    }

    private String factoryBeanName; // beanName

    private String beanClassName;   // 类的全类名

    public String getFactoryBeanName() {
        return factoryBeanName;
    }

    public void setFactoryBeanName(String factoryBeanName) {
        this.factoryBeanName = factoryBeanName;
    }

    public String getBeanClassName() {
        return beanClassName;
    }

    public void setBeanClassName(String beanClassName) {
        this.beanClassName = beanClassName;
    }
}
```

##### BeanDefinitionReader

```java
public class MyBeanDefinitionReader {

    private final Properties contextConfig = new Properties();

    // 需要被注册的Bean
    private final List<String> registryBeanClasses = new ArrayList<>();

    public MyBeanDefinitionReader(String... locations) {
        // 1. 加载properties配置文件
        doLoadConfig(locations[0]);

        // 2. 扫描相关类
        doScanner(contextConfig.getProperty("scanPackage"));

        //        System.out.println(registryBeanClasses);
    }

    public List<MyBeanDefinition> loadBeanDefinitions() {

        List<MyBeanDefinition> result = new ArrayList<>();


        try {
            for (String className : registryBeanClasses) {
                Class<?> beanClass = Class.forName(className);

                if(beanClass.isInterface()) continue; // 如果是接口，就不进行处理

                // 1. 默认类名首字母小写的情况
                result.add(doCreateBeanDefinition(toLowerFirstCase(beanClass.getSimpleName()),beanClass.getName()));

                // 2. 如果是接口，就用其实现类
                for(Class<?> i:beanClass.getInterfaces()){

                    result.add(doCreateBeanDefinition(toLowerFirstCase(i.getName()),beanClass.getName()));

                }

            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }

        return result;
    }

    private MyBeanDefinition doCreateBeanDefinition(String factoryBeanName, String factoryClassName) {

        MyBeanDefinition beanDefinition = new MyBeanDefinition();
        beanDefinition.setFactoryBeanName(factoryBeanName);
        beanDefinition.setBeanClassName(factoryClassName);

        return beanDefinition;
    }

    private static String toLowerFirstCase(String simpleName) {

        char[] chars = simpleName.toCharArray();

        chars[0] = chars[0] < 'a'? (char) (chars[0] + 'a' - 'A') :chars[0];

        return String.valueOf(chars);
    }


    // 根据contextConfigLocation的路径去ClassPath下找到对应的配置文件
    private void doLoadConfig(String contextConfigLocation) {
        System.out.println("正在导入配置");
        InputStream resourceAsStream = this.getClass().getClassLoader().getResourceAsStream(contextConfigLocation.replaceAll("classpath:", ""));

        try {
            contextConfig.load(resourceAsStream);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (resourceAsStream != null) {
                try {
                    resourceAsStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

        }
    }

    // 扫描ClassPath下符合包路径规则所有的Class文件
    private void doScanner(String scanPackage) {
        System.out.println("正在扫描所有类");
        URL url = this.getClass().getClassLoader().getResource("/" + scanPackage.replaceAll("\\.", "/"));

        File classpath = new File(url.getFile());

        for (File file : classpath.listFiles()) {

            if (file.isDirectory()) {
                doScanner(scanPackage + "." + file.getName());
            } else {

                // 取反，减少代码的嵌套
                if (!file.getName().endsWith(".class")) {
                    continue;
                }

                // 包名.类名 => 完整类名
                String className = (scanPackage + "." + file.getName().replace(".class", ""));

                registryBeanClasses.add(className);
            }

        }
    }
}
```

##### DefaultListableBeanFactory

```java
public class MyDefaultListableBeanFactory implements MyBeanFactory {

    public final Map<String,MyBeanDefinition> beanDefinitionMap = new HashMap<>();

    @Override
    public Object getBean(Class<?> beanClass) {
        return null;
    }

    @Override
    public Object getBean(String beanName) {
        return null;
    }

    public void doRegistryBeanDefinition(List<MyBeanDefinition> beanDefinitions) throws Exception {

        for(MyBeanDefinition beanDefinition:beanDefinitions){

            if(this.beanDefinitionMap.containsKey(beanDefinition.getFactoryBeanName())){
                throw new RuntimeException("The" + beanDefinition.getFactoryBeanName() + "is exists!!!");
            }
            this.beanDefinitionMap.put(beanDefinition.getFactoryBeanName(),beanDefinition);
        }
    }
}
```

##### BeanWrapper

```java
public class MyBeanWrapper {

    private Object wrapperInstance;
    private Class<?> wrapperClass;

    public MyBeanWrapper(Object instance) {
        this.wrapperInstance = instance;
        this.wrapperClass = instance.getClass();
    }

    public Object getWrappedInstance() {
        return this.wrapperInstance;
    }

    public Class<?> getWrappedClass(){
        return this.wrapperClass;
    }
}
```

##### ApplicationContext

Spring入口，从这里获取bean信息。

```java
public class MyApplicationContext implements MyBeanFactory {

    private MyDefaultListableBeanFactory registry = new MyDefaultListableBeanFactory();

    // 三级缓存（终极缓存）
    private final Map<String, MyBeanWrapper> factoryBeanInstanceCache = new HashMap<>();

    //
    private final Map<String, Object> factoryBeanObjectCache = new HashMap<>();

    private MyBeanDefinitionReader reader;

    public MyApplicationContext(String... configLocations) {
        // 1. 加载配置文件
        reader = new MyBeanDefinitionReader(configLocations);

        try {
            // 2. 解析配置文件，将所有配置信息封装成BeanDefinition对象
            List<MyBeanDefinition> beanDefinitions = reader.loadBeanDefinitions();

            // 3. 所有的配置信息，缓存起来
            this.registry.doRegistryBeanDefinition(beanDefinitions);

            // 4. 加载非延时加载的所有的Bean
            doLoadInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        //        System.out.println("===========缓存==============");
        //        System.out.println(factoryBeanInstanceCache);
        //        System.out.println(factoryBeanObjectCache);
    }

    private void doLoadInstance() {
        // 循环调用getBean()方法
        for (Map.Entry<String, MyBeanDefinition> entry : this.registry.beanDefinitionMap.entrySet()) {

            String beanName = entry.getKey();

            if (!entry.getValue().isLazyInit()) {
                getBean(beanName);
            }
        }
    }


    @Override
    public Object getBean(Class<?> beanClass) {
        return getBean(beanClass.getName());
    }

    @Override
    public Object getBean(String beanName) {

        // 1. 先拿到BeanDefinition配置信息
        MyBeanDefinition beanDefinition = registry.beanDefinitionMap.get(beanName);

        // 2. 反射实例化对象
        Object instance = instantiateBean(beanName, beanDefinition);

        // 3. 将返回的Bean对象封装成BeanWrapper
        MyBeanWrapper beanWrapper = new MyBeanWrapper(instance);

        // 4. 执行依赖注入
        populateBean(beanName, beanDefinition, beanWrapper);

        // 5. 保存到IOC容器中
        this.factoryBeanInstanceCache.put(beanName, beanWrapper);

        // 拿到对应bean
        return this.factoryBeanInstanceCache.get(beanName).getWrappedInstance();
    }

    private void populateBean(String beanName, MyBeanDefinition beanDefinition, MyBeanWrapper beanWrapper) {
        // 先不考虑循环依赖

        Object instance = beanWrapper.getWrappedInstance();
        Class<?> clazz = beanWrapper.getWrappedClass();

        if(!(clazz.isAnnotationPresent(MyController.class) || clazz.isAnnotationPresent(MyService.class))) return;

        for (Field field : clazz.getDeclaredFields()) {

            if (!field.isAnnotationPresent(MyAutowired.class)) continue;

            MyAutowired autowired = field.getAnnotation(MyAutowired.class);
            String autowiredBeanName = autowired.value().trim();

            if ("".equals(beanName)) {

                beanName = field.getType().getName();

            }

            // 关闭检查
            field.setAccessible(true);

            try {

                if(this.factoryBeanObjectCache.get(autowiredBeanName) == null){
                    return;
                }

                field.set(instance, this.factoryBeanInstanceCache.get(beanName).getWrappedInstance());
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }

        }
    }

    private Object instantiateBean(String beanName, MyBeanDefinition beanDefinition) {
        String className = beanDefinition.getBeanClassName();
        Object instance = null;

        try {
            Class<?> clazz = Class.forName(className);

            instance = clazz.newInstance();

            // 如果是代理对象，触发AOP逻辑

            factoryBeanObjectCache.put(beanName, instance);

        } catch (ClassNotFoundException | InstantiationException | IllegalAccessException e) {
            e.printStackTrace();
        }
        return instance;
    }

    public int getBeanDefinitionCount(){
        return this.registry.beanDefinitionMap.size();
    }

    public String[] getBeanDefinitionNames(){
        return this.registry.beanDefinitionMap.keySet().toArray(new String[0]);
    }
}
```

### 三级缓存与循环依赖

#### 什么是循环依赖

![image-20220510161950943](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032101.png)

上图中，这种依赖关系形成了一个闭环，这种依赖关系就称之为循环依赖。

* BeanA类依赖BeanB类，同时BeanB又依赖BeanA类。
* BeanA类依赖了BeanB类，BeanB类依赖了BeanC类，BeanC依赖了BeanA类
* 自己依赖自己

#### 循环依赖问题复现

##### 定义依赖关系

* BeanA

```java
@MyService
public class BeanAImpl implements BeanA {

    @MyAutowired
    private BeanB beanB;

    @Override
    public String get(String name) {
        return "My name is A";
    }
}
```

* BeanB

```java
@MyService
public class BeanBImpl implements BeanB {

    @MyAutowired
    private BeanA beanA;

    @Override
    public String get(String name) {
        return "My name is B";
    }
}
```

相互依赖形成一个闭环。

* BeaC

```java
@MyService
public class BeanCImpl implements BeanC {
    @MyAutowired
    private BeanC beanC;
}
```

自己依赖自己。

##### 问题复现

在运行调试一下之前的代码，在MyApplicationContext初始化后打上断点，来跟踪一下IOC容器里面的情况，如下图：

![image-20220510163611567](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032102.png)

启动项目，我们发现只要是有循环依赖关系的属性并没有自动赋值，而没有循环依赖关系的属性均有自动赋值，如下图所示：

![image-20220510163916731](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032103.png)

这种情况是怎么造成的呢?我们分析原因之后发现，因为，IOC容器对Bean的初始化是根据BeanDefinition循环迭代，有一定的顺序。这样，在执行依赖注入时，需要自动赋值的属性对应的对象有可能还没初始化，没有初始化也就没有对应的实例可以注入。于是，就出现我们看到的情况。

##### 问题解决方案

1. 获取对象是从getBean()开始
2. 加一个容器，只要是相互依赖关系的打个标记，Mark一下
3. 把已经创建好的对象，放到一个新缓存里面（没有DI，没有自动赋值），一级缓存
4. 在先创建的Bean之前，加一个循环依赖的判断，首先去一级缓存里面，检查一下
5. 依赖注入的方法populateBean()，进行修改，不去从缓存中，而是通过调用getBean。

##### 三种缓存

- 一级缓存：已经完成依赖注入的Bean，成熟Bean

- 二级缓存：早起的纯净的Bean

- 三级缓存：代理的缓存。

![img](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032104.jpeg)

##### 不支持循环依赖的方式

* **通过构造器注入的不能支持循环依赖**
* **非单例的，不支持循环依赖**

#### 代码详解

##### 添加缓存

```java
public class MyApplicationContext implements MyBeanFactory {

    private final MyDefaultListableBeanFactory registry = new MyDefaultListableBeanFactory();

    // 循环依赖的标识，表示当前正在创建的BeanName，标记一下
    private final Set<String> singletonsCurrentlyInCreation = new HashSet<>();

    // 一级缓存：成熟的Bean
    private final Map<String,Object> singletonObjects = new HashMap<>();

    // 二级缓存：保存早起的纯净Bean
    private final Map<String,Object> earlySingletonObjects = new HashMap<>();

    // 三级缓存（终极缓存）：代理的Bean
    private final Map<String, MyBeanWrapper> factoryBeanInstanceCache = new HashMap<>();
    
    ....
}
```

##### 修改getBean

```java
@Override
public Object getBean(String beanName) {

    // 1. 先拿到BeanDefinition配置信息
    MyBeanDefinition beanDefinition = registry.beanDefinitionMap.get(beanName);

    // 入口
    Object singleton = getSingleton(beanName,beanDefinition);
    if(singleton != null) return singleton;

    // 标记bean正在创建
  if(!singletonsCurrentlyInCreation.contains(beanName)){
        singletonsCurrentlyInCreation.add(beanName);
    }

    // 2. 反射实例化对象
    Object instance = instantiateBean(beanName, beanDefinition);

    // 保存到一级缓存中
    this.singletonObjects.put(beanName,instance);

    // 3. 将返回的Bean对象封装成BeanWrapper
    MyBeanWrapper beanWrapper = new MyBeanWrapper(instance);

    // 4. 保存到IOC容器中
    this.factoryBeanInstanceCache.put(beanName, beanWrapper);

    // 5. 执行依赖注入
    populateBean(beanWrapper);

    // 拿到对应bean
    return this.factoryBeanInstanceCache.get(beanName).getWrappedInstance();
}
```

##### 修改依赖注入方式

通过getBean方法自动获取

```java
private void populateBean(MyBeanWrapper beanWrapper) {
    // 先不考虑循环依赖

    Object instance = beanWrapper.getWrappedInstance();
    Class<?> clazz = beanWrapper.getWrappedClass();

    if(!(clazz.isAnnotationPresent(MyController.class) || clazz.isAnnotationPresent(MyService.class))) return;

    for (Field field : clazz.getDeclaredFields()) {

        if (!field.isAnnotationPresent(MyAutowired.class)) continue;

        MyAutowired autowired = field.getAnnotation(MyAutowired.class);

        String autowiredBeanName = autowired.value().trim();

        if ("".equals(autowiredBeanName)) {
            autowiredBeanName = field.getType().getName();
        }

        // 关闭检查
        field.setAccessible(true);

        try {
            field.set(instance,getBean(autowiredBeanName));
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }

    }
}
```

##### 单例模式-getBean

```java
private Object instantiateBean(String beanName, MyBeanDefinition beanDefinition) {

    if(beanDefinition.isSingleton() && this.factoryBeanObjectCache.containsKey(beanName)){
        return this.factoryBeanObjectCache.get(beanName);
    }

    String className = beanDefinition.getBeanClassName();

    Object instance = null;

    try {
        Class<?> clazz = Class.forName(className);
        instance = clazz.newInstance();
        // 如果是代理对象，触发AOP逻辑
        factoryBeanObjectCache.put(beanName, instance);

        factoryBeanObjectCache.put(clazz.getName(), instance);

        for (Class<?> i : clazz.getInterfaces()) {
            this.factoryBeanObjectCache.put(i.getName(),instance);
        }


    } catch (ClassNotFoundException | InstantiationException | IllegalAccessException e) {
        e.printStackTrace();
    }
    return instance;
}
```

#####  测试访问

然后进行debug调试，发现一直相互依赖。

![image-20220510190341959](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032105.png)

用一级缓存可以解决，但是为什么有二级/三级缓存，是为了代码的健壮性。

### MVC顶层设计

#### MVC九大组件

| 序号 | 组件名                      | 解释                                |
| ---- | --------------------------- | ----------------------------------- |
| 1    | MultipartResolver           | 多文件上传的组件                    |
| 2    | LocaleResolver              | 本地语言环境                        |
| 3    | ThemeResolver               | 主题模板处理器                      |
| 4    | **HandlerMapping**          | **保存Url映射关系**                 |
| 5    | **HandlerAdapter**          | **动态参数适配器**                  |
| 6    | HandlerExceptionResolver    | 异常拦截器                          |
| 7    | RequestToViewNameTranslator | 视图提取器，从request中获取viewName |
| 8    | **ViewResolvers**           | **视图转换器，模板引擎**            |
| 9    | FlashMapManager             | 参数缓存器                          |

#### 文件配置

##### 模板文件

* `resources/layouts/404.html`

```html
<!DOCTYPE html>
<html lang="zh-cn">
    <head>
        <meta charset="utf-8">
        <title>页面去火星了</title>
    </head>
    <body>
        <font size='25' color='red'>404 Not Found</font><br/><font color='green'><i>Copyright@GupaoEDU</i></font>
    </body>
</html>
```

* `resources/layouts/500.html`

```html
<!DOCTYPE html>
<html lang="zh-cn">
    <head>
        <meta charset="utf-8">
        <title>服务器好像累了</title>
    </head>
    <body>
        <font size='25' color='blue'>500 服务器好像有点累了，需要休息一下</font><br/>
        <b>Message:￥{detail}</b><br/>
        <b>StackTrace:￥{stackTrace}</b><br/>
        <font color='green'><i>Copyright@GupaoEDU</i></font>
    </body>
</html>
```

* `resources/layouts/first.html`

```html
<!DOCTYPE html>
<html lang="zh-cn">
    <head>
        <meta charset="utf-8">
        <title>咕泡学院SpringMVC模板引擎演示</title>
    </head>
    <center>
        <h1>大家好，我是￥{teacher}老师<br/>欢迎大家一起来探索Spring的世界</h1>
        <h3>Hello,My name is ￥{teacher}</h3>
        <div>￥{data}</div>
        Token值：￥{token}
    </center>
</html>
```

##### properties文件

```properties
scanPackage = com.study.myspring.demo

templateRoot=layouts
```

##### 前提知识

* request.forward()：转发，自动携带上一次请求的所有参数
* response.redirect()：重定向，丢失上一次请求的所有参数

#### Spring MVC核心组件执行流程

![image-20220511173305660](https://raw.githubusercontent.com/TheFoxFairy/ImgStg/main/img202205182032106.png)

####  代码详解

##### 总结

- 找到对应的HandlerMapping

- 找到对应的HandlerAdapter

- handler.handle()找到对应的ModelAndView()

- ViewResolver找到对应的View对象

- view.render()

## 手写SpringV3.0版本

### AOP设计与实现

![image-20220526162943993](../../../../assets/Spring%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%AC%94%E8%AE%B0/image-20220526162943993.png)

#### 环境配置

##### application.properties

在application.properties中增加如下自定义配置：

````properties
# 多切面配置可以在key前面加前缀
# 例如：aspect.logAspect

# 切面表达式
pointCut=public .* com.study.myspring.demo.service..*Service..*(.*)
# 切面类
aspectClass=com.study.myspring.demo.aspect.LogAspect
# 切面前置通知
aspectBefore=before
# 切面后置通知
aspectAfter=after
# 切面异常通知
aspectAfterThrow=afterThrowing
# 切面异常类型
aspectAfterThrowingName=java.lang.Exception
````

##### Spring AOP的原生配置

下面是Spring AOP的原生配置，为了方便操作，用properties文件来替代xml，以简化操作：

```xml
<bean id="xmlAspect" class="com.study.myspring.demo.aspect.XmlAspect"/>

<!--AOP配置-->
<aop:config>
    <!--声明一个切面，并注入切面Bean，相等于@Aspect-->
    <aop:aspect ref="xmlAspect">
        <!--配置一个切入点，相等于@Poincut-->
        <aop:pointcut id="simplePointcut" expression="execution(* com.study.myspring.demo.service..*(..))"/>
        <!--配置通知，相当于@Before、@After、@AfterReturn，@Around、@AfterThrowing-->
        <aop:before method="before" pointcut-ref="simplePointcut"/>
        <aop:after method="after" pointcut-ref="simplePointcut"/>
        <aop:after-returning method="afterReturn" pointcut-ref="simplePointcut"/>
        <aop:after-throwing method="afterThrow" pointcut-ref="simplePointcut" throwing="ex"/>
    </aop:aspect>
</aop:config>
```

#### 完成AOP定层设计

##### MyAopProxy代理顶层接口定义

```java
public interface MyAopProxy {

    Object getProxy();

    Object getProxy(ClassLoader classLoader);

}
```

##### MyCglibAopProxy/MyJdkDynamicAopProxy

```java
public class MyJdkDynamicAopProxy implements MyAopProxy, InvocationHandler {

//    private MyAdvisedSupport config;
    private MyAdvisedSupport advised;

    public MyJdkDynamicAopProxy(MyAdvisedSupport config) {
        this.advised = config;
    }

    @Override
    public Object getProxy() {
        return getProxy(this.getClass().getClassLoader());
    }

    @Override
    public Object getProxy(ClassLoader classLoader) {
        return Proxy.newProxyInstance(
                this.getClass().getClassLoader(),
                this.advised.getTargetClass().getInterfaces(),
                this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        List<Object> chain = this.advised.getInterceptorsAndDynamicInterceptionAdvice(method,this.advised.getTargetClass());

        MyMethodInvocation invocation = new MyMethodInvocation(proxy,this.advised.getTarget(),method,args,this.advised.getTargetClass(),chain);

        return invocation.proceed();
    }
}
```

##### MyAdvisedSupport配置解析

```java
public class MyAdvisedSupport {
    private MyAopConfig config;

    private Pattern pointCutClassPattern;

    // chain
//    private Map<Method, Map<String, MyAdvice>> methodCache;
    private HashMap<Object, List<Object>> methodCache;

    private Class targetClass;
    private Object target;


    public MyAdvisedSupport(MyAopConfig config) {
        this.config = config;
    }

    public Class getTargetClass() {
        return targetClass;
    }

    public void setTargetClass(Class targetClass) {
        this.targetClass = targetClass;
        parse();
    }

    private void parse() {
        // public .* com.study.myspring.demo.service..*Service..*(.*)
        // 修饰符 方法返回值 包名.类名.方法名(形参列表)
        String pointCutRegex = this.config.getPointCut()
                .replaceAll("\\.", "\\\\.")
                .replaceAll("\\\\.\\*", ".*")
                .replaceAll("\\(", "\\\\(")
                .replaceAll("\\)", "\\\\)");
        // public .* com.study.myspring.demo.service..*Service
        String pointCutForClassRegex = pointCutRegex.substring(0, pointCutRegex.lastIndexOf("\\(") - 4);

        pointCutClassPattern = Pattern.compile("class " + pointCutForClassRegex.substring(pointCutForClassRegex.lastIndexOf(" ") + 1));


        // 保存回调通知和目标切点之间的关系
        // 一个方法 对应 多个目标切点
//        methodCache = new HashMap<>();
        methodCache = new HashMap<Object, List<Object>>();

        try {

            // 先把切面方法缓存起来，方便解析AOP配置文件的时候，可以根据方法名快速找到对应的回调方法
            Map<String, Method> aspectMethods = new HashMap<>();
            Class aspectClass = Class.forName(this.config.getAspectClass());
            for (Method method : aspectClass.getMethods()) {
                aspectMethods.put(method.getName(), method);
            }

            Pattern pointCutPattern = Pattern.compile(pointCutRegex);

            for (Method method : this.targetClass.getMethods()) {
                // public java.lang.String com.study.myspring.demo.service.impl.QueryService.query(java.lang.String)
                String methodString = method.toString();

                // public java.lang.String com.study.myspring.demo.service.impl.QueryService.query(java.lang.String) throws Exception
                if (methodString.contains("throws")) {
                    methodString = methodString.substring(0, methodString.lastIndexOf("throws")).trim();
                }

                Matcher matcher = pointCutPattern.matcher(methodString);
                if (matcher.matches()) {

                    List<Object> advices = new LinkedList<Object>();

                    if (!(null == this.config.getAspectBefore() || "".equals(this.config.getAspectBefore()))) {
                        advices.add(new MyMethodBeforeAdviceInterceptor(
                                aspectClass.newInstance(),
                                aspectMethods.get(this.config.getAspectBefore())
                        ));
                    }

                    if (!(null == this.config.getAspectAfter() || "".equals(this.config.getAspectAfter()))) {
                        advices.add(new MyAfterReturningAdviceInterceptor(
                                aspectClass.newInstance(),
                                aspectMethods.get(this.config.getAspectAfter())
                        ));
                    }

                    if (!(null == this.config.getAspectAfterThrow() || "".equals(this.config.getAspectAfterThrow()))) {
                        MyAspectJAfterThrowingAdvice advice = new MyAspectJAfterThrowingAdvice(
                                aspectClass.newInstance(),
                                aspectMethods.get(this.config.getAspectAfterThrow())
                        );
                        advice.setThrowName(this.config.getAspectAfterThrowingName());
                        advices.add(advice);
                    }

                    this.methodCache.put(method, advices);
                }
            }

        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }

    }

    public Object getTarget() {
        return target;
    }

    public void setTarget(Object target) {
        this.target = target;
    }

    public boolean pointCutMatch() {
        return this.pointCutClassPattern.matcher(this.targetClass.toString()).matches();
    }

    public List<Object> getInterceptorsAndDynamicInterceptionAdvice(Method method, Class targetClass) throws Exception {
        List<Object> cache = this.methodCache.get(method);

        if (cache == null) {
            Method m = null;
            m = targetClass.getMethod(method.getName(), method.getParameterTypes());
            cache = methodCache.get(m);
            this.methodCache.put(m, cache);
        }
        return cache;
    }
}
```

##### MyAdvice通知接口定义

```java
public interface MyAdvice {
//    private Object aspect;
//    private Method adviceMethod;
//    private String throwName;
//
//    public MyAdvice(Object aspect, Method adviceMethod) {
//        this.aspect = aspect;
//        this.adviceMethod = adviceMethod;
//    }
//
//    public Object getAspect() {
//        return aspect;
//    }
//
//    public void setAspect(Object aspect) {
//        this.aspect = aspect;
//    }
//
//    public Method getAdviceMethod() {
//        return adviceMethod;
//    }
//
//    public void setAdviceMethod(Method adviceMethod) {
//        this.adviceMethod = adviceMethod;
//    }
//
//    public String getThrowName() {
//        return throwName;
//    }
//
//    public void setThrowName(String throwName) {
//        this.throwName = throwName;
//    }
}
```

##### MyAbstractAspectJAdvice封装代理

```java
public class MyAbstractAspectJAdvice implements MyAdvice{

    private Object aspect;
    private Method adviceMethod;
    private String throwName;

    public MyAbstractAspectJAdvice(Object aspect, Method adviceMethod) {
        this.aspect = aspect;
        this.adviceMethod = adviceMethod;
    }

    protected Object invokeAdviceMethod(
            MyJoinPoint joinPoint, Object returnValue, Throwable ex)
            throws Throwable {
        Class<?> [] paramTypes = this.adviceMethod.getParameterTypes();
        if(null == paramTypes || paramTypes.length == 0){
            return this.adviceMethod.invoke(aspect);
        }else {
            Object[] args = new Object[paramTypes.length];
            for (int i = 0; i < paramTypes.length; i++) {
                if (paramTypes[i] == MyJoinPoint.class) {
                    args[i] = joinPoint;
                } else if (paramTypes[i] == Throwable.class) {
                    args[i] = ex;
                } else if (paramTypes[i] == Object.class) {
                    args[i] = returnValue;
                }
            }
            return this.adviceMethod.invoke(aspect, args);
        }
    }
}
```

##### MyAopCofig封装配置

```java
@Data
public class MyAopConfig {
    private String pointCut;
    private String aspectClass;
    private String aspectBefore;
    private String aspectAfter;
    private String aspectAfterThrow;
    private String aspectAfterThrowingName;
}
```

##### 接入getBean()方法与IOC容器衔接

找到MyApplicationContext的getBean()方法，可以知道getBean()负责Bean初始化的方法其实就是instantiateBean()，在初始化时就可以确定是否返回原生Bean还是Proxy Bean。代码实现如下：

```java
private Object instantiateBean(String beanName, MyBeanDefinition beanDefinition) {

    if(beanDefinition.isSingleton() && this.factoryBeanObjectCache.containsKey(beanName)){
        return this.factoryBeanObjectCache.get(beanName);
    }

    String className = beanDefinition.getBeanClassName();

    Object instance = null;

    try {
        Class<?> clazz = Class.forName(className);
        instance = clazz.newInstance();

        // 如果是代理对象，触发AOP逻辑
        MyAdvisedSupport config = instantionAopConfig(beanDefinition);
        config.setTargetClass(clazz);
        config.setTarget(instance);

        // 判断规则，是否要成代理，如果要调用代理工厂生成代理类，并且放入到三级缓存中
        // 如果不符合规则，则返回原生类
        if(config.pointCutMatch()){
            instance = proxyFactory.createAopProxy(config).getProxy();
        }


        factoryBeanObjectCache.put(beanName, instance);

        factoryBeanObjectCache.put(clazz.getName(), instance);

        for (Class<?> i : clazz.getInterfaces()) {
            this.factoryBeanObjectCache.put(i.getName(),instance);
        }


    } catch (ClassNotFoundException | InstantiationException | IllegalAccessException e) {
        e.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    }
    return instance;
}

private MyAdvisedSupport instantionAopConfig(MyBeanDefinition beanDefinition) {
    MyAopConfig config = new MyAopConfig();

    config.setPointCut(this.reader.getConfig().getProperty("pointCut"));
    config.setAspectClass(this.reader.getConfig().getProperty("aspectClass"));
    config.setAspectBefore(this.reader.getConfig().getProperty("aspectBefore"));
    config.setAspectAfter(this.reader.getConfig().getProperty("aspectAfter"));
    config.setAspectAfterThrow(this.reader.getConfig().getProperty("aspectAfterThrow"));
    config.setAspectAfterThrowingName(this.reader.getConfig().getProperty("aspectAfterThrowingName"));

    return new MyAdvisedSupport(config);
}
```

##### LogAspect自定义切面配置

```java
public class LogAspect {

    // 在调用一个方法之前，执行before方法·
    public void before(MyJoinPoint joinPoint){
        joinPoint.setUserAttribute("startTime_" + joinPoint.getMethod().getName(), System.currentTimeMillis());
        System.out.println("Invoker Before Method!!!");
    }

    // 在调用一个方法之后，执行after方法
    public void after(MyJoinPoint joinPoint){
        // 用于体现，暂停时间
        try {TimeUnit.MILLISECONDS.sleep(10);} catch (InterruptedException e) {e.printStackTrace();}
        long startTime = (Long)joinPoint.getUserAttribute("startTime_" + joinPoint.getMethod().getName());
        long endTime = System.currentTimeMillis();
        System.out.println("Invoker After Method!!! use time："+(endTime - startTime) + "ms");
    }

    public void afterThrowing(){
        System.out.println("出现异常");
    }
}
```

项目结构如下：

![image-20220626170929339](../../../../../assets/Spring%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%AC%94%E8%AE%B0/image-20220626170929339.png)

![image-20220626170942908](../../../../../assets/Spring%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%AC%94%E8%AE%B0/image-20220626170942908.png)

## 手绘Spring 运行时序图

### IOC运行时序图

#### Spring IOC容器

##### 什么是IOC/DI？

**IOC(Inversion of Control)控制反转**∶所谓控制反转，就是把原先我们代码里面需要实现的对象创建、依赖的代码，反转给容器来帮忙实现。

**DI(Dependency Injection)依赖注入**：就是指对象是被动接受依赖类而不是自己主动去找，换句话说就是指对象不是从容器中查找它依赖的类，而是在容器实例化对象的时候主动将它依赖的类注入给它。

##### 设计视角

###### 对象和对象的关系怎么表示？

可以用xml , properties文件等语义化配置文件表示。

###### 描述对象关系的文件存放在哪里？

可能是classpath , filesystem，或者是 URL网络资源，servletContext等。

###### 如何统一配置文件的标准？

在内部需要有一个统一的关于对象的定义，所有外部的描述都必须转化成统一的描述定义。

比如，BeanDefinition。

###### 如何对不同的配置文件进行解析？

需要对不同的配置文件语法，采用不同的解析器，采用策略模式。

##### Spring核心容器类图

- **BeanFactory**
- **BeanDefinition**
- **BeanDefinitionReader**

##### 基于XML的IOC容器的初始化

IOC容器的初始化包括BeanDefinition的Resource定位、加载和注册这三个基本的过程。

![image-20220703175643600](../../../../../assets/Spring%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%AC%94%E8%AE%B0/image-20220703175643600.png)

* 定位：定位配置文件和扫描相关注解
* 加载：将配置信息载入到内存中
* 注册：根据载入的信息，将对象初始化到IOC容器中

详细过程如下：

![image-20220703175818555](../../../../../assets/Spring%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%AC%94%E8%AE%B0/image-20220703175818555.png)

##### IOC容器初始化小结

现在通过上面的代码，总结一下IOC容器初始化的基本步骤：

- 初始化的入口在容器实现中的refresh()调用来完成。
- 对Bean定义载入IOC容器使用的方法是loadBeanDefinition()，其中的大致过程如下：

通过**ResourceLoader**来完成资源文件位置的定位，DefaultResourceLoader是默认的实现，同时上下文本身就给出了ResourceLoader的实现，可以从类路径，文件系统,URL等方式来定为资源位置。

如果是**XmlBeanFactory**作为IOC容器，那么需要为它**指定Bean定义的资源**，也就是说Bean定义文件时通过抽象成Resource来被IOC容器处理的，**容器通过BeanDefinitionReader来完成定义信息的解析和 Bean信息的注册，往往使用的是XmlBeanDefinitionReader 来解析Bean的XML定义文件-实际的处理过程是委托给BeanDefinitionParserDelegate来完成的**，从而得到 bean的定义信息，这些信息在Spring中使用BeanDefinition对象来表示-这个名字可以让我们想到loadBeanDefinition()，registerBeanDefinition()这些相关方法。它们都是为处理BeanDefinitin服务的，容器解析得到BeanDefinition 以后，需要把它在IOC容器中注册，这由IOC实现 BeanDefinitionRegistry 接口来实现。注册过程就是在IOC容器内部维护的一个HashMap来保存得到的BeanDefinition的过程。这个HashMap是IOC容器持有Bean信息的场所，以后对 Bean的操作都是围绕这个HashMap来实现的。

然后我们就可以通过BeanFactory和ApplicationContext来享受到Spring lOC的服务了,在使用IOC容器的时候，我们注意到除了少量粘合代码，绝大多数以正确IOC风格编写的应用程序代码完全不用关心如何到达工厂，因为容器将把这些对象与容器管理的其他对象钩在一起。基本的策略是把工厂放到已知的地方,最好是放在对预期使用的上下文有意义的地方,以及代码将实际需要访问工厂的地方。Spring本身提供了对声明式载入web应用程序用法的应用程序上下文,并将其存储在ServletContext中的框架实现。

![image-20220704212033670](../../../../../assets/Spring%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%AC%94%E8%AE%B0/image-20220704212033670.png)

![image-20220704212049424](../../../../../assets/Spring%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%AC%94%E8%AE%B0/image-20220704212049424.png)

![image-20220704212105728](../../../../../assets/Spring%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%AC%94%E8%AE%B0/image-20220704212105728.png)

### DI运行时序图

### AOP运行时序图

### MVC运行时序图

## Spring事务传播原理

## 数据库事务操作原理

## 基于Spring JDBC手写定制的ORM框架

## Spring5新特性

## 高频面试题