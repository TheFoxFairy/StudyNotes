# JVM与GC调优

## 字节码篇

### JVM概述

#### 作为Java工程师的你，曾被JVM伤害过吗？

- 运行着的线上系统突然卡死，系统无法访问，甚至直接OOM！
- 想解决线上JVM GC问题，但却无从下手。
- 新项目上线，对各种JVM参数设置一脸茫然，直接默认吧，然后就JJ了
- 每次面试之前都要重新背一遍JVM的一些原理概念性的东西，然而面试官却经常问你在实际项目中如何调优JVM参数，如何解决GC、OOM等问题，一脸懵逼。

#### 开发人工如何看待上层框架

![image-20220606104425550](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606104425550.png)

大部分Java开发人员，除会在项目中使用到与Java平台相关的各种高精尖技术，对于Java技术的核心Java虚拟机了解甚少。

- 一些有一定工作经验的开发人员，打心眼儿里觉得SSM、微服务等上层技术才是重点，基础技术并不重要，这其实是一种本末倒置的“病态”。

- 如果我们把核心类库的 API 比做数学公式的话，那么 Java 虚拟机的知识就好比公式的推导过程。

![image-20220606104454292](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606104454292.png)


计算机系统体系对我们来说越来越远，在不了解底层实现方式的前提下，通过高级语言很容易编写程序代码。但事实上计算机并不认识高级语言。

#### 架构师每天都在思考什么

* 应该如何让我的系统更快？
* 如何避免系统出现瓶颈？

#### Java语言及Java生态圈

世界上没有最好的编程语言，只有最适用于具体应用场景的编程语言。

> 面向薪资编程

##### Oracle JDK与Open JDK有什么关系

![image-20220606104917990](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606104917990.png)

##### JDK与JVM是什么关系？

![image-20220606105250413](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606105250413.png)

- Oracle 有两个 Java 平台标准的产品，Java SE 开发工具包(JDK) 和 Java SE 运行时环境(JRE)。
- **JDK(Java Development Kit Java开发工具包)**，JDK是提供给Java开发人员使用的，其中包含了java的开发工具，也包括了JRE。所以安装了JDK，就不用在单独安装JRE了。其中的开发工具包括编译工具(javac.exe) 打包工具(jar.exe)等。
- **JRE(Java Runtime Environment Java运行环境)** 是 JDK 的子集，也就是包括 JRE 所有内容，以及开发应用程序所需的编译器和调试器等工具。JRE 提供了库、Java 虚拟机（JVM）和其他组件，用于运行 Java 编程语言、小程序、应用程序。
- **JVM(Java Virtual Machine Java虚拟机)**，JVM可以理解为是一个虚拟出来的计算机，具备着计算机的基本运算方式，它主要负责把 Java 程序生成的字节码文件，解释成具体系统平台上的机器指令，让其在各个平台运行。

###### 如何理解Java是跨平台语言？

![image-20220606105550870](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606105550870.png)

**“write once， run anywhere.”**

###### 如何理解JVM跨语言的平台

![image-20220606105603104](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606105603104.png)

Java虚拟机根本不关心运行在其内部的程序到底是使用何种编程语言编写的，它只关心“字节码”文件。

**Java不是最强大的语言，但是JVM是最强大的虚拟机。**

##### Java发展的几个重大事件

###### 概述

- 1990年，在 Sun 计算机公司中，由 Patrick Naughton、MikeSheridan 及 James Gosling 领导的小组Green Team，开发出的新的程序语言，命名为Oak，后期命名为Java
- 1995年，Sun正式发布Java和HotJava产品，Java首次公开亮相。
- 1996年1月23日Sun Microsystems发布了JDK 1.0。
- 1998年，JDK 1.2版本发布。同时，Sun发布了 JSP/Servlet、EJB规范，以及将Java分成了 J2EE、J2SE和J2ME。 这表明了 Java开始向企业、桌面应用和移动设备应用3大领域挺进。
- 2000年，JDK 1.3发布，**Java HotSpot Virtual Machine正式发布，成为Java的默认虚拟机。**
- 2002年，JDK 1.4发布，古老的Classic虚拟机退出历史舞台。
- 2003年年底，**Java平台的Scala正式发布，同年Groovy也加入了 Java阵营。**
- 2004年，JDK 1.5发布。同时JDK 1.5改名为JavaSE 5.0。
- 2006年，JDK 6发布。同年，**Java开源并建立了 OpenJDK。**顺理成章，**Hotspot虚拟机也成为了 OpenJDK中的默认虚拟机。**
- 2007年，**Java平台迎来了新伙伴Clojure。**
- 2008 年，Oracle 收购了 BEA，**得到了 JRockit 虚拟机。**
- 2009年，Twitter宣布把后台大部分程序从Ruby迁移到Scala，这是Java平台的又一次大规模应用。
- 2010年，Oracle收购了Sun，**获得Java商标和最具价值的HotSpot虚拟机。**此时，Oracle拥有市场占用率最高的两款虚拟机HotSpot和JRockit，并计划在未来对它们进行整合：HotRockit.  JCP组织管理：Java语言
- 2011年，JDK7发布。在JDK 1.7u4中，**正式启用了新的垃圾回收器G1**。
- 2017年，JDK9发布。**将G1设置为默认GC，替代CMS**
- 同年，**IBM的J9开源**，形成了现在的Open J9社区
- 2018年，Android的Java侵权案判决，Google赔偿Oracle计88亿美元
- 同年，Oracle宣告JavaEE成为历史名词，JDBC、JMS、Servlet赠予Eclipse基金会
- 同年，JDK11发布，LTS版本的JDK，**发布革命性的ZGC，调整JDK授权许可**
- 2019年，JDK12发布，加入RedHat领导开发的**Shenandoah GC**

###### 说说你认识的JVM

- Sun Classic VM  -->解释型
- Exact VM   --> Solaris
- **SUN公司的 HotSpot VM**
- **BEA 的 JRockit  --> 不包含解释器，服务器端，JMC**
- **IBM 的 J9**
- KVM和CDC/CLDC Hotspot
- Azul VM
- Liquid VM
- Apache Harmony
- Microsoft JVM
- TaobaoJVM

- **Graal VM --> 2018年，“Run Programs Faster Anywhere”**

![image-20220606103143885](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606103143885.png)

- Dalvik VM

- 其他JVM：
   Java Card VM、Squawk VM、JavaInJava、Maxine VM、Jikes RVM、IKVM.NET、Jam VM、      Cacao VM、Sable VM、Kaffe、Jelatine JVM、Nano VM、MRP、Moxie JVM

###### JVM的生命周期

**虚拟机的启动**
Java虚拟机的启动是通过引导类加载器(bootstrap class loader)创建一个初始类(initial class)来完成的，这个类是由虚拟机的具体实现指定的。

**虚拟机的退出有如下的几种情况：**

- 某线程调用Runtime类或System类的exit方法，或 Runtime类的halt方法，并且Java安全管理器也允许这次exit或halt操作。
- 程序正常执行结束
- 程序在执行过程中遇到了异常或错误而异常终止
- 由于操作系统出现错误而导致Java虚拟机进程终止

> 对于JVM面试，面试官可以循序渐进从理论、实践各种角度深入，也未必是要求面试者什么都懂。但如果你懂得原理，一定会成为面试中的加分项。
>
>
> 说说Java虚拟机的生命周期（阿里）

###### 重点说下HotSpot

- SUN的JDK版本从1.3.1开始运用HotSpot虚拟机， 2006年底开源，主要使用C++实现，JNI接口部分用C实现。
- HotSpot是较新的Java虚拟机，使用JIT(Just in Time)编译器，可以大大提高Java运行的性能。 
- Java原先是把源代码编译为字节码在虚拟机执行，这样执行速度较慢。而HotSpot将常用的部分代码编译为本地(原生，native)代码，这样显着提高了性能。 
- HotSpot JVM 参数可以分为规则参数(standard options)和非规则参数(non-standard options)。 规则参数相对稳定，在JDK未来的版本里不会有太大的改动。 非规则参数则有因升级JDK而改动的可能。

#### Java与C++

![image-20220606120208233](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606120208233.png)

垃圾收集机制为我们打理了很多繁琐的工作，大大提高了开发的效率，但是，垃圾收集也不是万能的，懂得JVM内部的内存结构、工作机制，是设计高扩展性应用和诊断运行时问题的基础，也是Java工程师进阶的必备能力。

#### JVM的整体架构

##### 简图

![image-20220606104033875](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606104033875.png)

##### 详细图

![image-20220606104047998](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606104047998.png)

##### Java脉络知识图

![image-20220606120637918](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606120637918.png)

#### 大厂面试题

##### 什么是Java虚拟机(JVM)，为什么要使用？（JVM = Japanese Vedio's Man ）

> 虚拟机：指以软件的方式模拟具有完整硬件系统功能、运行在一个完全隔离环境中的完整计算机系统 ，是物理机的软件实现。常用的虚拟机有VMWare，Visual Box，Java Virtual Machine（Java虚拟机，简称JVM）

##### 说说Java虚拟机的生命周期及体系结构

- 虚拟机的启动：Java虚拟机的启动是通过引导类加载器(bootstrap class loader)创建一个初始类(initial class)来完成的，这个类是由虚拟机的具体实现指定的。

- 虚拟机的退出：

  ​	有如下的几种情况：

  - 某线程调用Runtime类或System类的exit方法，或 Runtime类的halt方法，并且Java安全管理器也允许这次exit或halt操作。
  - 程序正常执行结束
  - 程序在执行过程中遇到了异常或错误而异常终止
  - 由于操作系统出现错误而导致Java虚拟机进程终止

##### JVM的组成

### 字节码文件概述

#### 字节码文件是跨平台的吗？

##### class文件里是什么

源代码经过编译器编译之后便会生成一个字节码文件，字节码是一种二进制的类文件，它的内容是JVM的指令，而不像C、C++经由编译器直接生成机器码。

随着Java平台的不断发展，在将来，Class文件的内容也一定会做进一步的扩充，但是其基本的格式和结构不会做重大调整。

##### 生成class文件的编译器

###### 从位置上理解

![image-20220606121138259](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606121138259.png)

前端编译器 vs 后端编译器

###### 前端编译器的种类

Java源代码的编译结果是字节码，那么肯定需要有一种编译器能够将Java源码编译为字节码，承担这个重要责任的就是配置在path环境变量中的javac编译器。javac是一种能够将Java源码编译为字节码的前端编译器。

HotSpot VM并没有强制要求前端编译器只能使用javac来编译字节码，其实只要编译结果符合JVM规范都可以被JVM所识别即可。
在Java的前端编译器领域，除了javac之外，还有一种被大家经常用到的前端编译器，那就是内置在Eclipse中的**ECJ (Eclipse Compiler for Java)编译器**。和Javac的全量式编译不同，ECJ是一种增量式编译器。

- 默认情况下，IntelliJ IDEA 使用 javac 编译器。(还可以自己设置为AspectJ编译器 ajc)

###### 前端编译器的任务

**前端编译器**的主要任务就是负责将符合Java语法规范的Java代码转换为符合JVM规范的字节码文件。        

##### javac编译器的编译步骤

![image-20220606121801472](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606121801472.png)

##### 目前前端编译器的局限性

前端编译器并不会直接涉及编译优化等方面的技术，而是将这些具体优化细节移交给HotSpot的JIT编译器负责。

复习：AOT(静态提前编译器，Ahead Of Time Compiler)

- jdk9引入了AOT编译器(静态提前编译器，Ahead Of Time Compiler)


- Java 9 引入了实验性 AOT 编译工具jaotc。它借助了 Graal 编译器，将所输入的 Java 类文件转换为机器码，并存放至生成的动态共享库之中。


- 所谓 AOT 编译，是与即时编译相对立的一个概念。我们知道，即时编译指的是在程序的运行过程中，将字节码转换为可在硬件上直接运行的机器码，并部署至托管环境中的过程。而 AOT 编译指的则是，在程序运行之前，便将字节码转换为机器码的过程。
  - .java -> .class -> .so
- 最大好处：Java虚拟机加载已经预编译成二进制库，可以直接执行。不必等待即时编译器的预热，减少Java应用给人带来“第一次运行慢”的不良体验。

- 缺点：

  - 破坏了java“一次编译，到处运行”，必须为每个不同硬件、OS编译对应的发行包。
  - 降低了Java链接过程的动态性，加载的代码在编译期就必须全部已知。

还需要继续优化中，最初只支持Linux x64 java base。

#### 哪些类型对应有Class的对象

1. class：
2. 外部类，成员(成员内部类，静态内部类)，局部内部类，匿名内部类
3. interface：接口
4. []：数组
5. enum：枚举
6. annotation：注解@interface
7. primitive type：基本数据类型
8. void

```java
@Test
public void test(){
    Class c1 = Object.class;
    Class c2 = Comparable.class;
    Class c3 = String[].class;
    Class c4 = int[][].class;
    Class c5 = ElementType.class;
    Class c6 = Override.class;
    Class c7 = int.class;
    Class c8 = void.class;
    Class c9 = Class.class;
 
    int[] a = new int[10];
    int[] b = new int[100];
    Class c10 = a.getClass();
    Class c11 = b.getClass();
    // 只要元素类型与维度一样，就是同一个Class
    System.out.println(c10 == c11);
}
```

#### 字节码指令

##### 什么是字节码指令

Java虚拟机的指令由一个字节长度的、代表着某种特定操作含义的**操作码**（opcode）以及跟随其后的零至多个代表此操作所需参数的**操作数**（operand）所构成。虚拟机中许多指令并不包含操作数，只有一个操作码。
![image-20220606122311099](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606122311099.png)

##### 为什么要懂字节码指令

看字节码指令，可以发现`i++`是直接在局部变量表中修改值的。

```java
public class ByteCodeInterview {
    //面试题： i++和++i有什么区别？
    @Test
    public void test1(){
        int i = 10;
        i++; //11
        //++i;

        System.out.println(i);
    }

    @Test
    public void test2(){
        int i = 10; 
        i = i++;
        System.out.println(i);//10
    }

    @Test
    public void test3(){
        int i = 2;
        i *= i++; // i = i * (i++)
        System.out.println(i);//4
    }

    @Test
    public void test4(){
        int k = 10;
        k = k + (k++) + (++k); // k = 10 + 10 + 12 = 32
        // 第一个k从局部变量表中获取值10放入操作数栈中
        // 第二个k从局部变量表中获取值10放入操作数栈中，由于i++操作，局部变量表中的k值变为11
        // 第三个k，由于++i操作，k值变为12，然后从局部变量表中获取值12放入操作数栈中
        // 最后进行add操作，10+10+12=32
        System.out.println(k);
    }

    //包装类对象的缓存问题
    @Test
    public void test5(){
        //        Integer x = 5;
        //        int y = 5;

        Integer i1 = 10;
        Integer i2 = 10;
        System.out.println(i1 == i2); // true

        Integer i3 = 128;
        Integer i4 = 128;
        System.out.println(i3 == i4); // false

        Boolean b1 = true;
        Boolean b2 = true;
        System.out.println(b1 == b2); // true
    }
    
    @Test
    public void test6(){
        Integer i1 = new Integer(1); // 没有从缓存中拿数据，直接创建的新对象
        Integer i2 = 1;
        System.out.println(i1 == i2);// false
    }

    @Test
    public void test7(){
        // 1. 创建StringBuilder对象
        // 2. 创建两个对象 hello ，一个在堆中，一个在常量池中
        // 3. 创建两个对象 world ，一个在堆中，一个在常量池中
        // 4. 通过append方法，添加进去
        // 5. 通过toString方法，创建堆中对象 => 一共6个对象
        String str = new String("hello") + new String("world");
        
        // str.intern(); // 在常量池中，存储堆中的地址
        // String声明的字面量数据都放在字符串常量池中
        // jdk6中字符串常量池存放在方法区（永久代中），intern方法创建一个新的字符串常量地址
        // jdk7及以后字符串常量池存放在堆空间，intern方法返回堆中的字符串常量地址
        
        // 在字符串常量池中创建对象，如果当前对象存在，则返回地址
        String str1 = "helloworld";
        System.out.println(str == str1);
    }
}
```

![image-20220606122623787](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606122623787.png)

再举例：

```java
class Father {
    int x = 10;
    public Father() {
        this.print();
        x = 20;
    }
    public void print() {
        System.out.println("Father.x = " + x);
    }
}

class Son extends Father {
    int x = 30;
    public Son() {
        this.print();
        x = 40;
    }
    public void print() {
        System.out.println("Son.x = " + x);

    }
}

public class SonTest {
    public static void main(String[] args) {
        Father f = new Son();
        System.out.println(f.x);
    }
} 
```

#### 如何解读class文件

方式一：一个一个二进制的看。这里用到的是Notepad++，需要安装一个HEX-Editor插件，或者使用Binary Viewer

![image-20220606151257654](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606151257654.png)

方式二：使用javap指令：jdk自带的反解析工具
方式三：使用IDEA插件：jclasslib 或jclasslib bytecode viewer客户端工具。（可视化更好）

![image-20220606151313456](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606151313456.png)

 或

![image-20220606151328319](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606151328319.png)

### Class文件结构细节

#### class文件结构细节概述

Class文件的结构并不是一成不变的，随着Java虚拟机的不断发展，总是不可避免地会对Class文件结构做出一些调整，但是其基本结构和框架是非常稳定的。

**Class文件的总体结构如下：**

- **魔数**
- **Class文件版本**
- **常量池**
- **访问标识(或标志)**
- **类索引，父类索引，接口索引集合**
- **字段表集合**
- **方法表集合**
- **属性表集合**

> 这里一般常问：文件结构有几个部分？

![image-20220606151515061](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606151515061.png)

![image-20220606151546114](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606151546114.png)

这是一张Java字节码总的结构表，我们按照上面的顺序逐一进行解读就可以了。

```java
public class Demo{
    private int num = 1;
    public int add(){
        num = num + 2;
        return num;
    }
}
```

![image-20220606152319619](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606152319619.png)

#### class文件的魔数是什么

**Magic Number（魔数）：class文件的标志**

- 每个 Class 文件开头的4个字节的无符号整数称为魔数（Magic Number）

- 它的唯一作用是确定这个文件是否为一个能被虚拟机接受的有效合法的Class文件。即：魔数是Class文件的标识符。


- 魔数值固定为0xCAFEBABE。不会改变。


- 如果一个Class文件不以0xCAFEBABE开头，虚拟机在进行文件校验的时候就会直接抛出以下错误：

  ```
  Error: A JNI error has occurred， please check your installation and try again
  Exception in thread "main" java.lang.ClassFormatError: Incompatible magic value 1885430635 in class file StringTest
  ```

- 使用魔数而不是扩展名来进行识别主要是基于安全方面的考虑，因为文件扩展名可以随意地改动。


#### 如何确保高本版的JVM可执行低版本的class文件

- 不同版本的Java编译器编译的Class文件对应的版本是不一样的。目前，高版本的Java虚拟机可以执行由低版本编译器生成的Class文件，但是低版本的Java虚拟机不能执行由高版本编译器生成的Class文件。否则JVM会抛出java.lang.UnsupportedClassVersionError异常。 （向下兼容）
- 在实际应用中，由于开发环境和生产环境的不同，可能会导致该问题的发生。因此，需要我们在开发时，特别注意开发编译的JDK版本和生产环境中的JDK版本是否一致。

**class文件版本号**

- 紧接着魔数的 4 个字节存储的是 Class 文件的版本号。同样也是4个字节。第5个和第6个字节所代表的含义就是编译的副版本号minor_version，而第7个和第8个字节就是编译的主版本号major_version。


- 它们共同构成了class文件的格式版本号。譬如某个 Class 文件的主版本号为 M，副版本号为 m，那么这个Class 文件的格式版本号就确定为 M.m。
- 版本号和Java编译器的对应关系如下表：

![image-20220606152722169](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606152722169.png)

- Java 的版本号是从45开始的，JDK 1.1之后的每个JDK大版本发布主版本号向上加1。
- 虚拟机JDK版本为1.k （k >= 2）时，对应的class文件格式版本号的范围为45.0 - 44+k.0 （含两端）。

#### 常量池：class文件的基石

##### 概述

**常量池：存放所有常量**

- 常量池是Class文件中内容最为丰富的区域之一。常量池对于Class文件中的字段和方法解析也有着至关重要的作用。

- **常量池：可以理解为Class文件之中的资源仓库，它是Class文件结构中与其他项目关联最多的数据类型（后面的很多数据类型都会指向此处），也是占用Class文件空间最大的数据项目之一。**

- **常量池表项中，用于存放编译时期生成的各种==字面量==和==符号引用==，这部分内容将在类加载后进入方法区的运行时常量池中存放。**

![image-20220606153014174](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606153014174.png)

- 在版本号之后，紧跟着的是常量池的数量，以及若干个常量池表项。

##### 为什么需要常量池计数器

==constant_pool_count （常量池计数器）==

- 由于常量池的数量不固定，时长时短，所以需要放置两个字节来表示常量池容量计数值。
- 常量池容量计数值（u2类型）：从1开始，表示常量池中有多少项常量。即constant_pool_count=1表示常量池中有0个常量项。
- Demo的值为：

![image-20220606153156554](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220606153156554.png)

其值为0x0016，掐指一算，也就是22。

需要注意的是，这实际上只有21项常量。索引为范围是1-21。为什么呢？

**通常我们写代码时都是从0开始的，但是这里的常量池却是从1开始，因为它把第0项常量空出来了。这是为了满足后面某些指向常量池的索引值的数据在特定情况下需要表达“不引用任何一个常量池项目”的含义，这种情况可用索引值0来表示。**

```
int[] arr = new int[10];
arr[0];
arr[1];
 
arr[10 - 1];
```

##### 常量池表

###### 概述

**constant_pool []（常量池）**

- constant_pool是一种表结构，以 1 ~ constant_pool_count - 1为索引。表明了后面有多少个常量项。
- 常量池主要存放两大类常量：**字面量（Literal）和符号引用（Symbolic References）**
- 它包含了class文件结构及其子结构中引用的所有字符串常量、类或接口名、字段名和其他常量。常量池中的每一项都具备相同的特征。第1个字节作为类型标记，用于确定该项的格式，这个字节称为tag byte （标记字节、标签字节）。

 ![image-20220607103715353](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220607103715353.png)

###### 字面量和符号引用

- **字面量和符号引用**

在对这些常量解读前，我们需要搞清楚几个概念。
常量池主要存放两大类常量：字面量（Literal）和符号引用（Symbolic References）。如下表：

![image-20220607104007525](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220607104007525.png)

```java
String str = "atguigu";
final int NUM = 10;
```

- **全限定名**

com/atguigu/test/Demo这个就是类的全限定名，仅仅是把包名的"."替换成"/"，为了使连续的多个全限定名之间不产生混淆，在使用时最后一般会加入一个“;”表示全限定名结束。

- **简单名称**

简单名称是指没有类型和参数修饰的方法或者字段名称，上面例子中的类的add()方法和num字段的简单名称分别是add和num。

- **描述符**

**描述符的作用是用来描述字段的数据类型、方法的参数列表（包括数量、类型以及顺序）和返回值。**根据描述符规则，基本数据类型（byte、char、double、float、int、long、short、boolean）以及代表无返回值的void类型都用一个大写字符来表示，而对象类型则用字符L加对象的全限定名来表示，详见下表:   （数据类型：基本数据类型 、 引用数据类型）

![image-20220607104220146](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220607104220146.png)

用描述符来描述方法时，按照先参数列表，后返回值的顺序描述，参数列表按照参数的严格顺序放在一组小括号“()”之内。如：

- 方法java.lang.String toString()的描述符为() Ljava/lang/String;

- 方法int abc(int[] x， int y)的描述符为([II) I。

###### 谈谈你对符号引用、直接引用的理解

这里说明下符号引用和直接引用的区别与关联：

- 符号引用：符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能无歧义地定位到目标即可。**符号引用与虚拟机实现的内存布局无关**，引用的目标并不一定已经加载到了内存中。 

- 直接引用：直接引用可以是直接**指向目标的指针、相对偏移量或是一个能间接定位到目标的句柄。直接引用是与虚拟机实现的内存布局相关的**，同一个符号引用在不同虚拟机实例上翻译出来的直接引用一般不会相同。如果有了直接引用，那说明引用的目标必定已经存在于内存之中了。

###### 常量类型和结构

常量池中每一项常量都是一个表，JDK1.7之后共有14种不同的表结构数据。如下表格所示：

![image-20220607105110104](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220607105110104.png)

![image-20220607105115004](../../../../assets/07-JVM%E4%B8%8EGC%E8%B0%83%E4%BC%98/image-20220607105115004.png)

总结1：

- 这14种表（或者常量项结构）的共同点是：表开始的第一位是一个u1类型的标志位（tag），代表当前这个常量项使用的是哪种表结构，即哪种常量类型。

- 在常量池列表中，CONSTANT_Utf8_info常量项是一种使用改进过的UTF-8编码格式来存储诸如文字字符串、类或者接口的全限定名、字段或者方法的简单名称以及描述符等常量字符串信息。


- 这14种常量项结构还有一个特点是，其中13个常量项占用的字节固定，只有CONSTANT_Utf8_info占用字节不固定，其大小由length决定。为什么呢？**因为从常量池存放的内容可知，其存放的是字面量和符号引用，最终这些内容都会是一个字符串，这些字符串的大小是在编写程序时才确定**，比如你定义一个类，类名可以取长取短，所以在没编译前，大小不固定，编译后，通过utf-8编码，就可以知道其长度。

#### 访问标识(access_flag、访问标志、访问标记)

在常量池后，紧跟着访问标记。该标记使用**两个字节**表示，用于识别一些类或者接口层次的访问信息，包括：这个 Class 是类还是接口；是否定义为 public 类型；是否定义为 abstract 类型；如果是类的话，是否被声明为 final 等。各种访问标记如下所示：

![image-20220607105742885](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607105742885.png)

- 类的访问权限通常为 ACC_ 开头的常量。
- 每一种类型的表示都是通过设置访问标记的32位中的特定位来实现的。比如，若是public final的类，则该标记为ACC_PUBLIC | ACC_FINAL。
- 使用ACC_SUPER可以让类更准确地定位到父类的方法super.method()，现代编译器都会设置并且使用这个标记。

#### 类索引、父类索引、接口索引集合

##### 概述

在访问标记后，会指定该类的类别、父类类别以及实现的接口，格式如下：

![image-20220607105858590](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607105858590.png)

这三项数据来确定这个类的继承关系。

- 类索引用于确定这个类的全限定名
- 父类索引用于确定这个类的父类的全限定名。由于 Java语言不允许多重继承，所以父类索引只有一个，除了java.lang.Object 之外，所有的Java类都有父类，因此除了java.lang.Object 外，所有Java类的父类索引都不为 0。
- 接口索引集合就用来描述这个类实现了哪些接口，这些被实现的接口将按 implements 语句（如果这个类本身是一个接口，则应当是 extends 语句）后的接口顺序从左到右排列在接口索引集合中。

##### this_class（类索引）

2字节无符号整数，指向常量池的索引。它提供了类的全限定名，如com/atguigu/java1/Demo。this_class的值必须是对常量池表中某项的一个有效索引值。常量池在这个索引处的成员必须为CONSTANT_Class_info类型结构体，该结构体表示这个class文件所定义的类或接口。

##### super_class （父类索引）

2字节无符号整数，指向常量池的索引。它提供了当前类的父类的全限定名。如果我们没有继承任何类，其默认继承的是java/lang/Object类。同时，由于Java不支持多继承，所以其父类只有一个。

superclass指向的父类不能是final。

##### interfaces

###### 概述

指向常量池索引集合，它提供了一个符号引用到所有已实现的接口
由于一个类可以实现多个接口，因此需要以数组形式保存多个接口的索引，表示接口的每个索引也是一个指向常量池的CONSTANT_Class (当然这里就必须是接口，而不是类)。

###### interfaces_count (接口计数器)

interfaces_count项的值表示当前类或接口的直接超接口数量。

###### interfaces [](接口索引集合)

interfaces []中每个成员的值必须是对常量池表中某项的有效索引值，它的长度为 interfaces_count。 每个成员 interfaces[i]必须为 CONSTANT_Class_info结构，其中 0 <= i < interfaces_count。在 interfaces[]中，各成员所表示的接口顺序和对应的源代码中给定的接口顺序（从左至右）一样，即 **interfaces[0]对应的是源代码中最左边的接口。**

#### 字段表集合

##### 概述

###### fields

- **用于描述接口或类中声明的变量**。字段（field）包括**类级变量以及实例级变量**，但是不包括方法内部、代码块内部声明的局部变量。(local variables)
- 字段叫什么名字、字段被定义为什么数据类型，这些都是无法固定的，只能引用常量池中的常量来描述。
- 它指向常量池索引集合，它描述了每个字段的完整信息。比如**字段的标识符、访问修饰符（public、private或protected）、是类变量还是实例变量（static修饰符）、是否是常量（final修饰符）等。**

######  注意事项

字段表集合中不会列出从父类或者实现的接口中继承而来的字段，但有可能列出原本Java代码之中不存在的字段。譬如在内部类中为了保持对外部类的访问性，会自动添加指向外部类实例的字段。

在Java语言中字段是无法重载的，两个字段的数据类型、修饰符不管是否相同，都必须使用不一样的名称，但是对于字节码来讲，如果两个字段的描述符不一致，那字段重名就是合法的。

##### 字段计数器

fields_count的值表示当前class文件fields表的成员个数。使用2个字节来表示。

fields表中每个成员都是一个field_info结构，用于表示该类或接口所声明的所有类字段或者实例字段，不包括方法内部声明的变量，也不包括从父类或父接口继承的那些字段。

##### 字段表

fields表中的每个成员都必须是一个fields_info结构的数据项，用于表示当前类或接口中某个字段的完整描述。

一个字段的信息包括如下这些信息。这些信息中，**各个修饰符都是布尔值，要么有，要么没有。**

- 作用域（public、private、protected修饰符）
- 是实例变量还是类变量（static修饰符）
- 可变性（final）
- 并发可见性（volatile修饰符，是否强制从主内存读写）
- 可否序列化（transient修饰符）
- 字段数据类型（基本数据类型、对象、数组）
- 字段名称

字段表结构

- 字段表作为一个表，同样有他自己的结构：

![image-20220607110630067](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607110630067.png)

#### 方法表集合

##### 概述

###### methods

methods 指向常量池索引集合，它完整描述了每个方法的签名。

在字节码文件中，**每一个method_info项都对应着一个类或者接口中的方法信息。**比如方法的访问修饰符(public、private或protected)，方法的返回值类型以及方法的参数信息等。

如果这个方法不是抽象的或者不是native的，那么字节码中会体现出来。

一方面，methods表只描述当前类或接口中声明的方法，**不包括从父类或父接口继承的方法。**另一方面，methods表有可能会出现由编译器自动添加的方法，最典型的便是编译器产生的方法信息(比如：**类(接口)初始化方法<clinit>()和实例初始化方法<init>()**)。

###### 使用注意事项

在Java语言中，要重载(Overload)一个方法，除了要与原方法具有相同的简单名称之外，还要求必须拥有一个与原方法不同的特征签名，特征签名就是一个方法中各个参数在常量池中的字段符号引用的集合，也就是因为返回值不会包含在特征签名之中，因此Java语言里无法仅仅依靠返回值的不同来对一个已有方法进行重载。但在Class文件格式中，特征签名的范围更大一些，只要描述符不是完全一致的两个方法就可以共存。也就是说，如果两个方法有相同的名称和特征签名，但返回值不同，那么也是可以合法共存于同一个class文件中。

也就是说，尽管Java语法规范并不允许在一个类或者接口中声明多个方法签名相同的方法，但是和Java语法规范相反，字节码文件中却恰恰允许存放多个方法签名相同的方法，唯一的条件就是这些方法之间的返回值不能相同。

##### 方法计数器

methods_count的值表示当前class文件methods表的成员个数。使用两个字节来表示。
methods 表中每个成员都是一个method_info结构。

##### 方法表

- methods表中的每个成员都必须是一个method_info结构，用于表示当前类或接口中某个方法的完整描述。如果某个method_info结构的access_flags项既没有设置 ACC_NATIVE 标志也没有设置ACC_ABSTRACT标志，那么该结构中也应包含实现这个方法所用的Java虚拟机指令。
- method_info结构可以表示类和接口中定义的所有方法，包括实例方法、类方法、实例初始化方法和类或接口初始化方法
- 方法表的结构实际跟字段表是一样的，方法表结构如下：

![image-20220607111141889](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607111141889.png)

#### 属性表集合

##### 概述

方法表集合之后的属性表集合，**指的是class文件所携带的辅助信息**，比如该 class 文件的源文件的名称。以及任何带有RetentionPolicy.CLASS 或者RetentionPolicy.RUNTIME的注解。这类信息通常被用于Java虚拟机的验证和运行，以及Java程序的调试，**一般无须深入了解。**

此外，字段表、方法表都可以有自己的属性表。用于描述某些场景专有的信息。

属性表集合的限制没有那么严格，不再要求各个属性表具有严格的顺序，并且只要不与已有的属性名重复，任何人实现的编译器都可以向属性表中写入自己定义的属性信息，但Java虚拟机运行时会忽略掉它不认识的属性。

##### 属性计数器

attributes_count的值表示当前class文件属性表的成员个数。属性表中每一项都是一个attribute_info结构。

##### 属性表

###### 概述

属性表的每个项的值必须是attribute_info结构。属性表的结构比较灵活，各种不同的属性只要满足以下结构即可。

###### ConstantValue属性

ConstantValue 属性表示一个常量字段的值。位于 field_info结构的属性表中。

```java
ConstantValue_attribute {
    u2 attribute_name_index;
    u4 attribute_length;
    u2 constantvalue_index;//字段值在常量池中的索引，常量池在该索引处的项给出该属性表示的常量值。（例如，值是long型的，在常量池中便是CONSTANT_Long）
}
```

###### Deprecated属性

Deprecated 属性是在 JDK 1.1 为了支持注释中的关键词@deprecated 而引入的。

```java
Deprecated_attribute {
    u2 attribute_name_index;
     u4 attribute_length;
}
```

###### Code属性

Code属性就是存放方法体里面的代码。但是，并非所有方法表都有Code属性。像接口或者抽象方法，他们没有具体的方法体，因此也就不会有Code属性了。

Code属性表的结构，如下图：

![image-20220607111515834](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607111515834.png)

可以看到：Code属性表的前两项跟属性表是一致的，即Code属性表遵循属性表的结构，后面那些则是他自定义的结构。

###### InnerClass属性

为了方便说明特别定义一个表示类或接口的 Class 格式为 C。如果 C 的常量池中包含某个CONSTANT_Class_info 成员，且这个成员所表示的类或接口不属于任何一个包，那么 C 的ClassFile 结构的属性表中就必须含有对应的 InnerClasses 属性。InnerClasses 属性是在 JDK 1.1 中为了支持内部类和内部接口而引入的，位于 ClassFile结构的属性表。

###### LineNumberTable属性

LineNumberTable 属性是可选变长属性，位于 Code结构的属性表。

LineNumberTable属性是**用来描述Java源码行号与字节码行号之间的对应关系。**这个属性可以用来在调试的时候定位代码执行的行数。

- **start_pc，即字节码行号;line_number，即Java源代码行号。**

在 Code 属性的属性表中，LineNumberTable 属性可以按照任意顺序出现，此外，多个 LineNumberTable属性可以共同表示一个行号在源文件中表示的内容，即 LineNumberTable 属性不需要与源文件的行一一对应。

LineNumberTable属性表结构：

![image-20220607111732115](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607111732115.png)

###### LocalVariableTable属性

LocalVariableTable 是可选变长属性，位于 Code属性的属性表中。它被调试器**用于确定方法在执行过程中局部变量的信息**。在 Code 属性的属性表中，LocalVariableTable 属性可以按照任意顺序出现。 Code 属性中的每个局部变量最多只能有一个 LocalVariableTable 属性。

- **start pc + length表示这个变量在字节码中的生命周期起始和结束的偏移位置（this生命周期从头0到结尾）**
- **index就是这个变量在局部变量表中的槽位（槽位可复用）**
- **name就是变量名称**
- **Descriptor表示局部变量类型描述**

 LocalVariableTable 属性表结构：

![image-20220607111853874](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607111853874.png)



###### Signature属性

Signature 属性是可选的定长属性，位于 ClassFile， field_info
或 method_info结构的属性表中。在 Java 语言中，任何类、 接口、 初始化方法或成员的泛型签名如果包含了类型变量（ Type Variables） 或参数化类型（ Parameterized Types），则 Signature 属性会为它记录泛型签名信息。

###### SourceFile属性

SourceFile属性结构

![image-20220607111954672](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607111954672.png)

可以看到，其长度总是固定的8个字节。

###### 其他属性

Java虚拟机中预定义的属性有20多个，这里就不一一介绍了，通过上面几个属性的介绍，只要领会其精髓，其他属性的解读也是易如反掌。

### Oracle官方的反解析工具：javap

#### 解析字节码的作用

通过反编译生成的字节码文件，我们可以深入的了解java代码的工作机制。但是，自己分析类文件结构太麻烦了！除了使用第三方的jclasslib工具之外，oracle官方也提供了工具：javap。

javap是jdk自带的反解析工具。它的作用就是根据class字节码文件，反解析出当前类对应的code区（字节码指令）、局部变量表、异常表和代码行偏移量映射表、常量池等信息。

通过局部变量表，我们可以查看局部变量的作用域范围、所在槽位等信息，甚至可以看到槽位复用等信息。

#### javac -g操作

解析字节码文件得到的信息中，有些信息（如局部变量表、指令和代码行偏移量映射表、常量池中方法的参数名称等等）需要在使用javac编译成class文件时，指定参数才能输出。

比如，你直接javac xx.java，就不会在生成对应的局部变量表等信息，如果你使用javac -g xx.java就可以生成所有相关信息了。如果你使用的eclipse或IDEA，则默认情况下，eclipse、IDEA在编译时会帮你生成局部变量表、指令和代码行偏移量映射表等信息的。

#### javap的用法

**javap的用法格式：**`javap <options> <classes>`，其中，classes就是你要反编译的class文件。

在命令行中直接输入javap或javap -help可以看到javap的options有如下选项：

![image-20220607112856652](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607112856652.png)

这里重组一下：

```shell
-help  --help  -?      输出此用法消息
    -version               版本信息，其实是当前javap所在jdk的版本信息，不是class在哪个jdk下生成的。
    -public                仅显示公共类和成员
    -protected             显示受保护的/公共类和成员
    -p  -private           显示所有类和成员
    -package               显示程序包/受保护的/公共类 和成员 (默认)
    -sysinfo               显示正在处理的类的系统信息 (路径， 大小， 日期， MD5 散列，源文件名)
    -constants             显示静态最终常量

    -s                     输出内部类型签名
    -l                     输出行号和本地变量表
    -c                     对代码进行反汇编
    -v  -verbose           输出附加信息（包括行号、本地变量表，反汇编等详细信息）


    -classpath <path>      指定查找用户类文件的位置
    -cp <path>             指定查找用户类文件的位置
    -bootclasspath <path>  覆盖引导类文件的位置
```

一般常用的是-v -l -c三个选项。

- javap -l 会输出行号和本地变量表信息。
- javap -c 会对当前class字节码进行反编译生成汇编代码。
- javap -v classxx 除了包含-c内容外，还会输出行号、局部变量表信息、常量池等信息。

#### 总结

1、通过javap命令可以查看一个java类反汇编得到的Class文件版本号、常量池、访问标识、变量表、指令代码行号表等等信息。不显示类索引、父类索引、接口索引集合、``<clinit>()``、``<init>()``等结构

2、通过对前面例子代码反汇编文件的简单分析，可以发现，一个方法的执行通常会涉及下面几块内存的操作：
（1）java栈中：局部变量表、操作数栈。
（2）java堆。通过对象的地址引用去操作。
（3）常量池。
（4）其他如帧数据区、方法区的剩余部分等情况，测试中没有显示出来，这里说明一下。

3、平常，我们比较关注的是java类中每个方法的反汇编中的指令操作过程，这些指令都是顺序执行的，可以参考官方文档查看每个指令的含义，很简单：https://docs.oracle.com/javase/specs/jvms/se7/html/jvms-6.html

### 字节码指令集与解析概述

#### 概述

Java字节码对于虚拟机，就好像汇编语言对于计算机，属于基本执行指令。

Java 虚拟机的指令由一个字节长度的、代表着某种特定操作含义的数字（称为**操作码，Opcode**）以及跟随其后的零至多个代表此操作所需参数（称为**操作数，Operands**）而构成。由于 Java 虚拟机采用面向操作数栈而不是寄存器的结构，所以大多数的指令都不包含操作数，只有一个操作码。

由于限制了 Java 虚拟机操作码的长度为一个字节（即 0～255），这意味着指令集的操作码总数不可能超过 256 条。

官方文档：https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html

熟悉虚拟机的指令对于动态字节码生成、反编译Class文件、Class文件修补都有着非常重要的价值。因此，阅读字节码作为了解 Java 虚拟机的基础技能，需要熟练掌握常见指令。

#### 字节码与数据类型

在Java虚拟机的指令集中，大多数的指令都包含了其操作所对应的数据类型信息。例如，iload指令用于从局部变量表中加载int型的数据到操作数栈中，而fload指令加载的则是float类型的数据。

对于大部分与数据类型相关的字节码指令，**它们的操作码助记符中都有特殊的字符来表明专门为哪种数据类型服务**：

- i代表对int类型的数据操作
- l代表long类型的数据操作
- s代表short类型的数据操作
- b代表byte类型的数据操作
- c代表char类型的数据操作
- f代表float类型的数据操作
- d代表double类型的数据操作

也有一些指令的助记符中**没有明确地指明操作类型**的字母，如arraylength指令，它没有代表数据类型的特殊字符，但操作数永远只能是一个数组类型的对象。

还有另外一些指令，如无条件跳转指令goto则是与**数据类型无关的**。

**大部分的指令都没有支持整数类型byte、char和short，甚至没有任何指令支持boolean类型。**编译器会在编译期或运行期将byte和short类型的数据带符号扩展（Sign-Extend）为相应的int类型数据，将boolean和char类型数据零位扩展（Zero-Extend）为相应的int类型数据。与之类似，在处理boolean、byte、short和char类型的数组时，也会转换为使用对应的int类型的字节码指令来处理。因此，**大多数对于boolean、byte、short和char类型数据的操作，实际上都是使用相应的int类型作为运算类型。**

```java
byte b1 = 12;
short s1 = 10;
int i = b1 + s1;
```

#### 指令分类

由于完全介绍和学习这些指令需要花费大量时间。为了让大家能够更快地熟悉和了解这些基本指令，这里将JVM中的字节码指令集按用途大致分成 9 类。

- 加载与存储指令
- 算术指令
- 类型转换指令
- 对象的创建与访问指令
- 方法调用与返回指令
- 操作数栈管理指令
- 控制转移指令
- 异常处理指令
- 同步控制指令

（说在前面）在做值相关操作时：

- 一个指令，可以从局部变量表、常量池、堆中对象、方法调用、系统调用中等取得数据，这些数据（可能是值，可能是对象的引用）被压入操作数栈。
- 一个指令，也可以从操作数栈中取出一到多个值（pop多次），完成赋值、加减乘除、方法传参、系统调用等等操作。

### 字节码指令

#### 面试题1

**知道字节码吗？字节码指令都有哪些？**

#### 加载与存储指令

##### 概述

###### 作用

加载和存储指令用于将数据从栈帧的局部变量表和操作数栈之间来回传递。

###### 常用指令

```
1、【局部变量压栈指令】将一个局部变量加载到操作数栈：xload、xload_<n>（其中x为i、l、f、d、a，n 为 0 到 3）

2、【常量入栈指令】将一个常量加载到操作数栈：bipush、sipush、ldc、ldc_w、ldc2_w、aconst_null、iconst_m1、iconst_<i>、lconst_<l>、fconst_<f>、dconst_<d>

3、【出栈装入局部变量表指令】将一个数值从操作数栈存储到局部变量表：xstore、xstore_<n>（其中x为i、l、f、d、a，n 为 0 到 3）

4、扩充局部变量表的访问索引的指令：wide。
```

上面所列举的指令助记符中，有一部分是以尖括号结尾的（例如``iload_<n>``）。这些指令助记符实际上代表了一组指令（例如 ``iload_<n>``代表了``iload_0``、``iload_1``、``iload_2``和``iload_3``这几个指令）。这几组指令都是某个带有一个操作数的通用指令（例如 iload）的特殊形式，**对于这若干组特殊指令来说，它们表面上没有操作数，不需要进行取操作数的动作，但操作数都隐含在指令中。** 

比如：

- iload_0：将局部变量表中索引为0位置上的数据压入操作数栈中。
- iload 0：将局部变量表中索引为0位置上的数据压入操作数栈中。
- iload 4：将局部变量表中索引为4位置上的数据压入操作数栈中。

除此之外，它们的语义与原生的通用指令完全一致（例如 iload_0的语义与操作数为0时的 iload 指令语义完全一致）。在尖括号之间的字母指定了指令隐含操作数的数据类型，``<n>``代表非负的整数， `<i>` 代表是int类型数据，``<l>`` 代表long类型，``<f>``代表float类型，``<d>`` 代表double类型。

操作byte、char、short和boolean类型数据时，经常用int类型的指令来表示。

##### 操作数栈与局部变量表

###### 操作数栈(Operand Stacks)

我们知道，Java字节码是Java虚拟机所使用的指令集。因此，它与Java虚拟机基于栈的计算模型是密不可分的。

在解释执行过程中，每当为Java方法分配栈桢时，Java虚拟机往往需要开辟一块额外的空间作为**操作数栈，来存放计算的操作数以及返回结果。**

具体来说便是：**执行每一条指令之前，Java 虚拟机要求该指令的操作数已被压入操作数栈中。在执行指令时，Java 虚拟机会将该指令所需的操作数弹出，并且将指令的结果重新压入栈中。**

![image-20220607115025180](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607115025180.png)

以加法指令 iadd 为例。假设在执行该指令前，栈顶的两个元素分别为 int 值 1 和 int 值 2，那么 iadd 指令将弹出这两个 int，并将求得的和 int 值 3 压入栈中。

![image-20220607115043497](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607115043497.png)

由于 iadd 指令只消耗栈顶的两个元素，因此，对于离栈顶距离为 2 的元素，即图中的问号，iadd 指令并不关心它是否存在，更加不会对其进行修改。

###### 局部变量表(Local Variables)

Java 方法栈桢的另外一个重要组成部分则是局部变量区，**字节码程序可以将计算的结果缓存在局部变量区之中。**
实际上，Java 虚拟机将局部变量区当成一个数组，依次存放 this 指针（仅非静态方法），所传入的参数，以及字节码中的局部变量。

和操作数栈一样，long 类型以及 double 类型的值将占据两个单元，其余类型仅占据一个单元。

![image-20220607115339339](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607115339339.png)

举例：

```java
public void foo(long l， float f) {
    {
        int i = 0;
    }
    {
        String s = "Hello， World";
    }
}
```

对应的图示：

![image-20220607115412498](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607115412498.png)

> 其中i和s是同一个槽，是因为i的作用域已经过了，因此当前槽被复用了。

在栈帧中，与性能调优关系最为密切的部分就是局部变量表。局部变量表中的变量也是重要的垃圾回收根节点，只要被局部变量表中直接或间接引用的对象都不会被回收。

在方法执行时，虚拟机使用局部变量表完成方法的传递。

##### 局部变量压栈指令

**局部变量压栈指令将给定的局部变量表中的数据压入操作数栈。**

* `xload_<n>` (x为i、l、f、d、a，n为 0 到 3)
* `xload` (x为i、l、f、d、a) 

说明：在这里，x的取值表示数据类型。

指令xload_n表示将第n个局部变量压入操作数栈，比如iload_1、fload_0、aload_0等指令。其中aload_n表示将一个对象引用压栈。

指令xload通过指定参数的形式，把局部变量压入操作数栈，当使用这个命令时，表示局部变量的数量可能超过了4个，比如指令iload、fload等。

##### 常量入栈指令

常量入栈指令的功能是将常数压入操作数栈，根据数据类型和入栈内容的不同，又可以分为const系列、push系列和ldc指令。

**指令const系列**：用于对特定的常量入栈，入栈的常量隐含在指令本身里。指令有：``iconst_<i> (i从-1到5)、lconst_<l> (l从0到1)、fconst_<f> (f从0到2)、dconst_<d> (d从0到1)、aconst_null``。

比如，

- iconst_m1将-1压入操作数栈；

- iconst_x（x为0到5）将x压入栈：
- lconst_0、lconst_1分别将长整数0和1压入栈； 
- fconst_0、fconst_1、fconst_2分别将浮点数0、1、2压入栈；
- dconst_0和dconst_1分别将double型0和1压入栈。
- aconst_null将null压入操作数栈；

从指令的命名上不难找出规律，指令助记符的第一个字符总是喜欢表示数据类型，i表示整数，l表示长整数，f表示浮点数，d表示双精度浮点，习惯上用a表示对象引用。如果指令隐含操作的参数，会以下划线形式给出。

```java
int i = 3;   iconst_3
int j = 6;   iconst 6? bipush 6?
int k = 32768 ldc ?
```

**指令push系列**：主要包括bipush和sipush。它们的区别在于接收数据类型的不同，bipush接收8位整数作为参数，sipush接收16位整数，它们都将参数压入栈。

**指令ldc系列**：如果以上指令都不能满足需求，那么可以使用万能的ldc指令，它可以接收一个8位的参数，该参数指向常量池中的int、float或者String的索引，将指定的内容压入堆栈。

类似的还有ldc_w，它接收两个8位参数，能支持的索引范围大于ldc。

如果要压入的元素是long或者double类型的，则使用ldc2_w指令，使用方式都是类似的。

总结如下：

![image-20220607115919005](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607115919005.png)

##### 出栈装入局部变量表指令

出栈装入局部变量表指令用于将操作数栈中栈顶元素弹岀后，装入局部变量表的指定位置，用于给局部变量赋值。

这类指令主要以store的形式存在，比如xstore （x为i、l、f、d、a）、 xstore_n （x 为 i、l、f、d、a， n 为 0 至 3）。
其中，指令istore_n将从操作数栈中弹出一个整数，并把它赋值给局部变量索引n位置。
指令xstore由于没有隐含参数信息，故需要提供一个byte类型的参数类指定目标局部变量表的位置。

**说明：** 

**一般说来，类似像store这样的命令需要带一个参数，用来指明将弹出的元素放在局部变量表的第几个位置。**但是，为了尽可能压缩指令大小，使用专门的istore_1指令表示将弹出的元素放置在局部变量表第1个位置。类似的还有istore_0、istore_2、istore_3，它们分别表示从操作数栈顶弹出一个元素，存放在局部变量表第0、2、3个位置。

由于局部变量表前几个位置总是非常常用，因此**这种做法虽然增加了指令数量，但是可以大大压缩生成的字节码的体积。**如果局部变量表很大，需要存储的槽位大于3，那么可以使用istore指令，外加一个参数，用来表示需要存放的槽位位置。

#### 算术指令

##### 概述

###### 作用

算术指令用于对两个操作数栈上的值进行某种特定运算，并把结果重新压入操作数栈。 

###### 分类

大体上算术指令可以分为两种：对整型数据进行运算的指令与对浮点类型数据进行运算的指令。

###### byte、short、char和boolean类型说明

在每一大类中，都有针对Java虚拟机具体数据类型的专用算术指令。但没有直接支持byte、short、char和boolean类型的算术指令，对于这些数据的运算，都使用int类型的指令来处理。此外，在处理boolean、byte、short和char类型的数组时，也会转换为使用对应的int类型的字节码指令来处理。

###### 运算时的溢出

数据运算可能会导致溢出，例如两个很大的正整数相加，结果可能是一个负数。其实Java虚拟机规范并无明确规定过整型数据溢出的具体结果，仅规定了在处理整型数据时，只有除法指令以及求余指令中当出现除数为0时会导致虚拟机抛出异常ArithmeticException。

###### 运算模式

向最接近数舍入模式：JVM要求在进行浮点数计算时，所有的运算结果都必须舍入到适当的精度，非精确结果必须舍入为可被表示的最接近的精确值，如果有两种可表示的形式与该值一样接近，将优先选择最低有效位为零的；

向零舍入模式：将浮点数转换为整数时，采用该模式，该模式将在目标数值类型中选择一个最接近但是不大于原值的数字作为最精确的舍入结果；

###### NaN值使用

当一个操作产生溢出时，将会使用有符号的无穷大表示，如果某个操作结果没有明确的数学定义的话，将会使用 NaN值来表示。而且所有使用NaN值作为操作数的算术操作，结果都会返回 NaN；

##### 所有算术指令

###### 概述

所有的算术指令包括：

```shell
加法指令：iadd、ladd、fadd、dadd
减法指令：isub、lsub、fsub、dsub
乘法指令:imul、lmul、 fmul、dmul
除法指令：idiv、ldiv、fdiv、ddiv
求余指令：irem、lrem、frem、drem    //remainder:余数
取反指令：ineg、lneg、fneg、dneg    //negation:取反
自增指令：iinc
位运算指令，又可分为：
位移指令：ishl、ishr、iushr、lshl、lshr、lushr
按位或指令：ior、lor
按位与指令：iand、land
按位异或指令：ixor、lxor

比较指令：dcmpg、dcmpl、fcmpg、fcmpl、lcmp
```

###### 举例

```java
public static int bar(int i) {
    return ((i + 1) - 2) * 3 / 4;
}
```

字节码指令对应的图示：

![image-20220607121006179](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121006179.png)

###### 一个曾经的案例1

```java
public void add(){
    byte i = 15;
    int j = 8;
    int k = i + j;
}
```

字节码对应的内存解析：

![image-20220607121133513](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121133513.png)

![image-20220607121204156](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121204156.png)

![image-20220607121217844](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121217844.png)

![image-20220607121223612](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121223612.png)

![image-20220607121241924](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121241924.png)

![image-20220607121253010](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121253010.png)

![image-20220607121314225](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121314225.png)

![image-20220607121319170](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121319170.png)

###### 一个曾经的案例2

```java
public static void main(String[] args) {
    int x = 500;
    int y = 100;
    int a = x / y;
    int b = 50;
    System.out.println(a + b);
}
```

字节码对应的内存解析：

![image-20220607121419387](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607121419387.png)

##### 比较指令的说明

- 比较指令的作用是比较栈顶两个元素的大小，并将比较结果入栈。

- **比较指令有：dcmpg， dcmpl、fcmpg、fcmpl、lcmp。**
  - 与前面讲解的指令类似，首字符d表示double类型，f表示float，l表示long。

- 对于double和float类型的数字，由于NaN的存在，各有两个版本的比较指令。以float为例，**有fcmpg和fcmpl两个指令，它们的区别在于在数字比较时，若遇到NaN值，处理结果不同。**
- 指令dcmpl和dcmpg也是类似的，根据其命名可以推测其含义，在此不再赘述。
- 指令lcmp针对long型整数，由于long型整数没有NaN值，故无需准备两套指令。

举例：

指令fcmpg和fcmpl都从栈中弹出两个操作数，并将它们做比较，设栈顶的元素为v2，栈顶顺位第2位的元素为v1，若v1=v2，则压入0；若v1>v2则压入1；若v1<v2则压入-1。

两个指令的不同之处在于，如果遇到NaN值，fcmpg会压入1，而fcmpl会压入-1。

- 数值类型的数据，才可以谈大小！ （byte\short\char\int；long\float\double)

- boolean、引用数据类型不能比较大小。

#### 类型转换指令

##### 概述

① 类型转换指令可以将两种不同的数值类型进行相互转换。

② 这些转换操作一般用于实现用户代码中的显式类型转换操作，或者**用来处理字节码指令集中数据类型相关指令无法与数据类型一一对应的问题。**

##### 宽化类型转换

###### 转换规则

Java虚拟机直接支持以下数值的宽化类型转换（widening numeric conversion，小范围类型向大范围类型的安全转换）。也就是说，并不需要指令执行，包括：

- 从int类型到long、float或者double类型。对应的指令为：i2l、i2f、i2d
- 从long类型到float、double类型。对应的指令为：l2f、l2d
- 从float类型到double类型。对应的指令为：f2d

**简化为：int --> long --> float --> double**（由小到大，x2x）

###### 精度损失问题

1. 宽化类型转换是不会因为超过目标类型最大值而丢失信息的，例如，从int转换到 long，或者从int转换到double，都不会丢失任何信息，转换前后的值是精确相等的。 
2. 从int、long类型数值转换到float，或者long类型数值转换到double时，将可能发生精度丢失——可能丢失掉几个最低有效位上的值，转换后的浮点数值是根据IEEE754最接近舍入模式所得到的正确整数值。 


尽管宽化类型转换实际上是可能发生精度丢失的，但是这种转换永远不会导致Java虚拟机抛出运行时异常。 

###### 补充说明

**从byte、char和short类型到int类型的宽化类型转换实际上是不存在的。**对于byte类型转为int，虚拟机并没有做实质性的转化处理，只是简单地通过操作数栈交换了两个数据。而将byte转为long时，使用的是i2l，可以看到在内部byte在这里已经等同于int类型处理，类似的还有short类型，这种处理方式有两个特点：

- 一方面可以减少实际的数据类型，如果为short和byte都准备一套指令，那么指令的数量就会大增，**而虚拟机目前的设计上，只愿意使用一个字节表示指令，因此指令总数不能超过256个，为了节省指令资源，将short和byte当做int处理也在情理之中。**

- 另一方面，由于局部变量表中的槽位固定为32位，无论是byte或者short存入局部变量表，都会占用32位空间。从这个角度说，也没有必要特意区分这几种数据类型。

##### 窄化类型转换

###### 转换规则

Java虚拟机也直接支持以下窄化类型转换：

- 从int类型至byte、short或者char类型。**对应的指令有：i2b、i2s、i2c**
- 从long类型到int类型。**对应的指令有：l2i**
- 从float类型到int或者long类型。**对应的指令有：f2i、f2l**
- 从double类型到int、long或者float类型。**对应的指令有：d2i、d2l、d2f**

###### 精度损失问题

窄化类型转换可能会导致转换结果具备不同的正负号、不同的数量级，因此，转换过程很可能会导致数值丢失精度。

尽管数据类型窄化转换可能会发生上限溢出、下限溢出和精度丢失等情况，但是Java虚拟机规范中明确规定数值类型的窄化转换指令永远不可能导致虚拟机抛出运行时异常

###### 补充说明

当将一个浮点值窄化转换为整数类型T（T限于int或long类型之一）的时候，将遵循以下转换规则：

- 如果浮点值是NaN，那转换结果就是int或long类型的0。
- 如果浮点值不是无穷大的话，浮点值使用IEEE 754的向零舍入模式取整，获得整数值v，如果v在目标类型T（int或long）的表示范围之内，那转换结果就是v。否则，将根据v的符号，转换为T所能表示的最大或者最小正数

当将一个 double 类型窄化转换为 float 类型时，将遵循以下转换规则：

- 通过向最接近数舍入模式舍入一个可以使用float类型表示的数字。最后结果根据下面这3条规则判断：
  - 如果转换结果的绝对值太小而无法使用 float来表示，将返回 float类型的正负零。
  - 如果转换结果的绝对值太大而无法使用 float来表示，将返回 float类型的正负无穷大。
  - 对于double 类型的 NaN值将按规定转换为 float类型的 NaN值。

#### 对象的创建与访问指令

##### 概述

Java是面向对象的程序设计语言，虚拟机平台从字节码层面就对面向对象做了深层次的支持。有一系列指令专门用于对象操作，可进一步细分为创建指令、字段访问指令、数组操作指令、类型检查指令。

##### 创建指令

###### 概述

虽然类实例和数组都是对象，但Java虚拟机对类实例和数组的创建与操作使用了不同的字节码指令：
###### 创建类实例的指令：

**创建类实例的指令：new**

它接收一个操作数，为指向常量池的索引，表示要创建的类型，执行完成后，将对象的引用压入栈。

###### 创建数组的指令

**创建数组的指令：newarray、anewarray、multianewarray。**

- newarray：创建基本类型数组
- anewarray：创建引用类型数组
- multianewarray：创建多维数组

上述创建指令可以用于创建对象或者数组，由于对象和数组在Java中的广泛使用，这些指令的使用频率也非常高。

##### 字段访问指令

对象创建后，就可以通过对象访问指令获取对象实例或数组实例中的字段或者数组元素。

- **访问类字段（static字段，或者称为类变量）的指令：getstatic、putstatic**
- **访问类实例字段（非static字段，或者称为实例变量）的指令：getfield、putfield**

举例：

以getstatic指令为例，它含有一个操作数，为指向常量池的Fieldref索引，它的作用就是获取Fieldref指定的对象或者值，并将其压入操作数栈。

```java
public void sayHello() {
    System.out.println("hello");
}
```

对应的字节码指令：

```shell
0 getstatic #8 <java/lang/System.out>
3 ldc #9 <hello>
5 invokevirtual #10 <java/io/PrintStream.println>
8 return
```

##### 数组操作指令

###### 概述

数组操作指令主要有：xastore和xaload指令。具体为：

- 把一个数组元素加载到操作数栈的指令：baload、caload、saload、iaload、laload、faload、daload、aaload

- 将一个操作数栈的值存储到数组元素中的指令：bastore、 castore、 sastore、iastore、 lastore、fastore、dastore、aastore

即：

![image-20220607123442950](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607123442950.png)

- 取数组长度的指令：arraylength
  - 该指令弹出栈顶的数组元素，获取数组的长度，将长度压入栈。

###### 说明

- 指令xaload表示将数组的元素压栈，比如saload、caload分别表示压入short数组和char数组。指令xaload在执行时，要求操作数中栈顶元素为数组索引i，栈顶顺位第2个元素为数组引用a，该指令会弹岀栈顶这两个元素，并将a[i]重新压入栈。

- xastore则专门针对数组操作，以iastore为例，它用于给一个int数组的给定索引赋值。在iastore执行前，操作数栈顶需要以此准备3个元素：值、索引、数组引用，iastore会弹出这3个值，并将值赋给数组中指定索引的位置。

##### 类型检查指令

检查类实例或数组类型的指令：instanceof、checkcast。

- 指令checkcast用于检查类型强制转换是否可以进行。如果可以进行，那么checkcast指令不会改变操作数栈，否则它会抛出ClassCastException异常。

- 指令instanceof用来判断给定对象是否是某一个类的实例，它会将判断结果压入操作数栈。

#### **方法调用与返回指令**

##### 方法调用指令

###### 概述

**方法调用指令：invokevirtual、invokeinterface、invokespecial、invokestatic 、invokedynamic**

以下5条指令用于方法调用：

- **invokevirtual指令用于调用对象的实例方法**，根据对象的实际类型进行分派（虚方法分派），支持**多态**。这也是Java语言中最常见的方法分派方式。
- **invokeinterface指令用于调用接口方法**，它会在运行时搜索由特定对象所实现的这个接口方法，并找出适合的方法进行调用。
- **invokespecial指令用于调用一些需要特殊处理的实例方法**，包括实例初始化方法（构造器）、私有方法和父类方法。这些方法都是静态类型绑定的，不会在调用时进行动态派发。
- **invokestatic指令用于调用命名类中的类方法（static方法**）。这是静态绑定的。
- **invokedynamic：调用动态绑定的方法**，这个是JDK 1.7后新加入的指令。用于在运行时动态解析出调用点限定符所引用的方法，并执行该方法。前面4条调用指令的分派逻辑都固化在 java 虚拟机内部，而 invokedynamic指令的分派逻辑是由用户所设定的引导方法决定的。

```java
public class MethodInvokeTest {
    public static void main(String[] args) {
        Father f = new Father();
        Son s = new Son();
        System.out.println(f.getInfo());
        System.out.println(s.getInfo());

        Comparator<Integer> comparator = new Comparator<Integer>() {
            @Override
            public int compare(Integer o1， Integer o2) {
                return 0;
            }
        };
        //comparator = Integer::compare;

        comparator.compare(12，32);
    }
}

class Father {
    private String info = "atguigu";

    public void setInfo(String info) {
        this.info = info;
    }

    public String getInfo() {
        return info;
    }
}

class Son extends Father {
    private String info = "尚硅谷";

    public void setInfo(String info) {
        this.info = info;
    }

    public String getInfo() {
        return info;
    }

}
```

##### 方法返回指令

方法调用结束前，需要进行返回。方法返回指令是**根据返回值的类型区分的。**

- 包括ireturn（当返回值是 boolean、byte、char、short和int 类型时使用）、lreturn、freturn、dreturn和areturn
- 另外还有一条return 指令供声明为 void的方法、实例初始化方法以及类和接口的类初始化方法使用。

![image-20220607132646437](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607132646437.png)

**举例：**

通过ireturn指令，将当前函数操作数栈的顶层元素弹出，并将这个元素压入调用者函数的操作数栈中（因为调用者非常关心函数的返回值），所有在当前函数操作数栈中的其他元素都会被丢弃。

如果当前返回的是synchronized方法，那么还会执行一个隐含的monitorexit指令，退出临界区。

最后，会丢弃当前方法的整个帧，恢复调用者的帧，并将控制权转交给调用者。

![image-20220607132728402](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607132728402.png)

对应的代码：

```java
public int methodReturn(){
    int i = 500;
    int j = 200;
    int k = 50;
    return (i + j) / k;
}
```

#### 操作数栈管理指令

##### 概述

操作数栈管理指令

如同操作一个普通数据结构中的堆栈那样，JVM提供的操作数栈管理指令，可以用于直接操作操作数栈的指令。

这类指令包括如下内容：

- 将一个或两个元素从栈顶弹出，并且直接废弃： pop，pop2；
- 复制栈顶一个或两个数值并将复制值或双份的复制值重新压入栈顶： dup， dup2， dup_x1， dup2_x1， dup_x2， dup2_x2； 
- 将栈最顶端的两个Slot数值位置交换： swap。Java虚拟机没有提供交换两个64位数据类型（long、double）数值的指令。
- 指令nop，是一个非常特殊的指令，它的字节码为0x00。和汇编语言中的nop一样，它表示什么都不做。这条指令一般可用于调试、占位等。

这些指令属于通用型，对栈的压入或者弹出无需指明数据类型。

##### 说明

- **不带``_x``的指令是复制栈顶数据并压入栈顶**。包括两个指令，dup和dup2。dup的系数代表要复制的Slot个数。
  - dup开头的指令用于复制1个Slot的数据。例如1个int或1个reference类型数据
  - dup2开头的指令用于复制2个Slot的数据。例如1个long，或2个int，或1个int+1个float类型数据
- **带``_x``的指令是复制栈顶数据并插入栈顶以下的某个位置。**共有4个指令，``dup_x1， dup2_x1， dup_x2， dup2_x2``。对于带_x的复制插入指令，只要将指令的dup和x的系数相加，结果即为需要插入的位置。因此
  - dup_x1插入位置：1+1=2，即栈顶2个Slot下面
  - dup_x2插入位置：1+2=3，即栈顶3个Slot下面
  - dup2_x1插入位置：2+1=3，即栈顶3个Slot下面
  - dup2_x2插入位置：2+2=4，即栈顶4个Slot下面
- pop：将栈顶的1个Slot数值出栈。例如1个short类型数值
- pop2：将栈顶的2个Slot数值出栈。例如1个double类型数值，或者2个int类型数值

#### 控制转移指令

##### 概述

程序流程离不开条件控制，为了支持条件跳转，虚拟机提供了大量字节码指令，大体上可以分为：**1)比较指令、2)条件跳转指令、3)比较条件跳转指令、4)多条件分支跳转指令、5)无条件跳转指令等。**

##### 条件跳转指令

条件跳转指令通常和比较指令结合使用。在条件跳转指令执行前，一般可以先用比较指令进行栈顶元素的准备，然后进行条件跳转。

条件跳转指令有： **ifeq， iflt， ifle， ifne， ifgt， ifge， ifnull， ifnonnull**。这些指令都接收两个字节的操作数，用于计算跳转的位置(16位符号整数作为当前位置的offset)。

它们的统一含义为：**弹出栈顶元素，测试它是否满足某一条件，如果满足条件，则跳转到给定位置。**

**具体说明：**

![image-20220607133302981](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607133302981.png)

注意：
1. 与前面运算规则一致：

  - 对于boolean、byte、char、short类型的条件分支比较操作，都是使用int类型的比较指令完成

  - 对于long、float、double类型的条件分支比较操作，则会先执行相应类型的比较运算指令，运算指令会返回一个整型值到操作数栈中，随后再执行int类型的条件分支比较操作来完成整个分支跳转

2. 由于各类型的比较最终都会转为 int 类型的比较操作，所以Java虚拟机提供的int类型的条件分支指令是最为丰富和强大的。

##### 比较条件跳转指令

比较条件跳转指令类似于比较指令和条件跳转指令的结合体，它将比较和跳转两个步骤合二为一。

这类指令有：**if_icmpeq、if_icmpne、if_icmplt、if_icmpgt、if_icmple、if_icmpge**、if_acmpeq和if_acmpne。其中指令助记符加上“if_”后，以字符“i”开头的指令针对int型整数操作(也包括short和byte类型)，以字符“a”开头的指令表示对象引用的比较。

**具体说明：**

![image-20220607133605722](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607133605722.png)


这些指令都接收两个字节的操作数作为参数，用于计算跳转的位置。同时在执行指令时，栈顶需要准备两个元素进行比较。指令执行完成后，栈顶的这两个元素被清空，且没有任何数据入栈。**如果预设条件成立，则执行跳转，否则，继续执行下一条语句。**

##### 多条件分支跳转

多条件分支跳转指令是专为switch-case语句设计的，**主要有tableswitch和lookupswitch。** 

![image-20220607134259397](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607134259397.png)

从助记符上看，两者都是switch语句的实现，它们的区别:

- tableswitch要求**多个条件分支值是连续的**，它内部只存放起始值和终止值，以及若干个跳转偏移量，通过给定的操作数index， 可以立即定位到跳转偏移量位置，**因此效率比较高**。

- 指令lookupswitch内部**存放着各个离散的case-offset对**，每次执行都要搜索全部的case-offset对，找到匹配的case值，并根据对应的offset计算跳转地址，**因此效率较低**。

指令tableswitch的示意图如下图所示。由于tableswitch的case值是连续的，因此只需要记录最低值和最高值，以及每一项对应的offset偏移量，根据给定的index值通过简单的计算即可直接定位到offset。

![image-20220607134306649](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607134306649.png)

指令lookupswitch处理的是离散的case值，但是出于效率考虑，**将case-offset对按照case 值大小排序**，给定index时，需要査找与index相等的case，获得其offset，如果找不到则跳转到default。指令lookupswitch 如下图所示。

![image-20220607134313716](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607134313716.png)

##### 无条件跳转

目前主要的无条件跳转指令为goto。指令goto接收两个字节的操作数，共同组成一个带符号的整数，**用于指定指令的偏移量，指令执行的目的就是跳转到偏移量给定的位置处。**

如果指令偏移量太大，超过双字节的带符号整数的范围，则可以使用指令goto_w，它和goto有相同的作用，但是它接收4个字节的操作数，可以表示更大的地址范围。

指令jsr、jsr_w、ret虽然也是无条件跳转的，但主要用于 try-finally语句，且已经被虚拟机逐渐废弃，故不在这里介绍这两个指令。

![image-20220607140545356](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607140545356.png)

#### 异常处理指令

##### 抛出异常指令

###### athrow指令

在Java程序中显示抛出异常的操作（throw语句）都是由athrow指令来实现。

除了使用throw语句显示抛出异常情况之外，**JVM规范还规定了许多运行时异常会在其他Java虚拟机指令检测到异常状况时自动抛出**。例如，在之前介绍的整数运算时，当除数为零时，虚拟机会在 idiv或 ldiv指令中抛出 ArithmeticException异常。

###### 注意

正常情况下，操作数栈的压入弹出都是一条条指令完成的。唯一的例外情况是**在抛异常时，Java 虚拟机会清除操作数栈上的所有内容，而后将异常实例压入调用者操作数栈上。**

###### 异常及异常的处理

过程一：异常对象的生成过程 ---> throw (手动 / 自动)   ---> 指令：athrow

过程二：异常的处理：抓抛模型。 try-catch-finally     ---> 使用异常表

##### 异常处理与异常表

###### 处理异常

在Java虚拟机中，处理异常（catch语句）不是由字节码指令来实现的（早期使用jsr、ret指令），而是**采用异常表来完成的**。

###### 异常表

如果一个方法定义了一个try-catch 或者try-finally的异常处理，就会创建一个异常表。它包含了每个异常处理或者finally块的信息。异常表保存了每个异常处理信息。比如：

- 起始位置
- 结束位置
- 程序计数器记录的代码处理的偏移地址
- 被捕获的异常类在常量池中的索引

#### 同步控制指令

##### 概述

java虚拟机支持两种同步结构：**方法级的同步 和 方法内部一段指令序列的同步**，这两种同步都是使用**monitor**来支持的。

##### 方法级的同步

方法级的同步：**是隐式的**， 即无须通过字节码指令来控制，它实现在方法调用和返回操作之中。虚拟机可以从方法常量池的方法表结构中的 **ACC_SYNCHRONIZED** 访问标志得知一个方法是否声明为同步方法；

当调用方法时，调用指令将会检查方法的ACC_SYNCHRONIZED访问标志是否设置。

- 如果设置了，执行线程将先持有同步锁，然后执行方法。最后在方法完成（无论是正常完成还是非正常完成）时释放同步锁。
- 在方法执行期间，执行线程持有了同步锁，其他任何线程都无法再获得同一个锁。
- 如果一个同步方法执行期间抛出了异常，并且在方法内部无法处理此异常，那这个同步方法所持有的锁将在异常抛到同步方法之外时自动释放。

举例：

```java
private int i = 0;
public synchronized void add(){
    i++;
}
```

对应的字节码：

```shell
 0 aload_0
 1 dup
 2 getfield #2 <com/atguigu/java1/SynchronizedTest.i>
 5 iconst_1
 6 iadd
 7 putfield #2 <com/atguigu/java1/SynchronizedTest.i>
10 return
```

##### 方法内指定指令序列的同步

同步一段指令集序列：通常是由java中的**synchronized**语句块来表示的。jvm的指令集有 **monitorenter** 和 **monitorexit** 两条指令来支持 synchronized关键字的语义。

当一个线程进入同步代码块时，它使用monitorenter指令请求进入。如果当前对象的监视器计数器为0，则它会被准许进入，若为1，则判断持有当前监视器的线程是否为自己，如果是，则进入，否则进行等待，直到对象的监视器计数器为0，才会被允许进入同步块。

当线程退岀同步块时，需要使用monitorexit声明退出。在Java虚拟机中，任何对象都有一个监视器与之相关联，用来判断对象是否被锁定，当监视器被持有后，对象处于锁定状态。

指令monitorenter和monitorexit在执行时，都需要在操作数栈顶压入对象，之后monitorenter和monitorexit的锁定和释放都是针对这个对象的监视器进行的。

下图展示了监视器如何保护临界区代码不同时被多个线程访问，只有当线程4离开临界区后，线程1、2、3才有可能进入。

![image-20220607142125665](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607142125665.png)

#### 面试题2

- `int a = 1;`JVM如何取得a的值（圆通）

```
1. 通过加载指令，将值1装入到栈中
2. 然后将值1存储到局部变量表中
3. 

0 iconst_1
1 istore_1
2 return
```

- `Integer x = 5;int y = 5;`比较 `x == y` 都经过哪些步骤？（百度）

```
1. 通过加载指令iconst_5，将值5装入到栈中
2. 然后将值5存储到局部变量表中

3. 通过加载指令，将值5装入到栈中
4. 然后将值5存储到局部变量表中

5. 然后通过load，将局部变量表中的数据，装入栈中
6. 然后通过控制转义指令，进行比较

 0 iconst_5
 1 invokestatic #2 <java/lang/Integer.valueOf : (I)Ljava/lang/Integer;>
 4 astore_1
 5 iconst_5
 6 istore_2
 7 getstatic #3 <java/lang/System.out : Ljava/io/PrintStream;>
10 aload_1
11 invokevirtual #4 <java/lang/Integer.intValue : ()I>
14 iload_2
15 if_icmpne 22 (+7)
18 iconst_1
19 goto 23 (+4)
22 iconst_0
23 invokevirtual #5 <java/io/PrintStream.println : (Z)V>
26 return
```



#### 面试题3

##### Java虚拟机中，数据类型可以分为哪几类？ 

- Java虚拟机是通过某些数据类型来执行计算的，数据类型可以分为两种：**基本类型和引用类型**，基本类型的变量持有原始值，而引用类型的变量持有引用值。

![image-20220607143523470](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607143523470.png)

- Java语言中的所有基本类型同样也都是Java虚拟机中的基本类型。但是boolean有点特别，虽然Java虚拟机也把boolean看做基本类型，但是指令集对boolean只有很有限的支持，当编译器把Java源代码编译为字节码时，它会用int或者byte来表示boolean。在Java虚拟机中，false是由整数零来表示的，所有非零整数都表示true，**涉及boolean值的操作则会使用int**。另外，**boolean数组是当做byte数组来访问的**。
- Java虚拟机还有一个只在内部使用的基本类型：**returnAddress**，Java程序员不能使用这个类型，**这个基本类型被用来实现Java程序中的finally子句**。该类型是jsr, ret以及jsr_w指令需要使用到的，它的值是JVM指令的操作码的指针。returnAddress类型不是简单意义上的数值，不属于任何一种基本类型，并且它的值是不能被运行中的程序所修改的。
- Java虚拟机的引用类型被统称为“引用（reference）”，有三种引用类型：**类类型、接口类型、以及数组类型**，它们的值都是对动态创建对象的引用。类类型的值是对类实例的引用；数组类型的值是对数组对象的引用，在Java虚拟机中，数组是个真正的对象；而**接口类型的值，则是对实现了该接口的某个类实例的引用。还有一种特殊的引用值是null，它表示该引用变量没有引用任何对象。** 

##### 为什么不把基本类型放堆中呢？

首先是栈、堆的特点不同。（堆比栈要大，但是栈比堆的运算速度要快。）
将复杂数据类型放在堆中的目的是为了不影响栈的效率，而是通过引用的方式去堆中查找。（八大基本类型的大小创建时候已经确立大小。三大引用类型创建时候无法确定大小）

简单数据类型比较稳定，并且它只占据很小的内存，将它放在空间小、运算速度快的栈中，能够提高效率。

##### Java中的参数传递是传值呢？还是传引用？

传值，因为引用地址也是一个值。

##### Java中有没有指针的概念？

> 没有，Java叫做对象变量。

**指针具有您可能（或可能不会）通常使用的额外操作；引用缺少这些操作，因为这些操作可能不安全。**例如，如果您使用指针来索引数组的第一个元素，如下所示：

```java
int squares[] = {1, 4, 9, 16, 25, 36, 49};
int* intPointer = squares;
```

您可能想要取消引用指针并获取值“ 1”。还可以继续intPointer++获得值“4”，intPointer++获得值“9”。这是因为++操作将指针在内存中向前移动了一个“单位”。指针将一个地址存储在内存中，并且++操作将适当数量的字节添加到该地址。**在许多系统上，int会加四个字节，但是如果指针是char指针，则只能加一个字节。**由于指针的基础数据类型是内存中的地址，因此以下内容是合法的（但不建议这样做）：

```java
char* charPointer = squares;
charPointer++;

void* voidPointer = squares;
voidPointer++
```

## 类的加载篇

### 面试题1

- 简述 Java 类加载机制

- JVM中类加载机制，类加载过程，什么是双亲委派模型？

### 类的加载过程（声明周期）

#### 说说类加载分几步？

##### 哪些需要加载？

在Java中数据类型分为基本数据类型和引用数据类型。**基本数据类型由虚拟机预先定义，引用数据类型则需要进行类的加载。**

##### 面试题2

- Java 类加载过程?（苏宁）
- 描述一下 JVM 加载 Class 文件的原理机制?（国美）
- JVM底层怎么加载class文件的？（蚂蚁金服）
- 类加载过程 （蚂蚁金服）
- Java 类加载过程? （百度）
- 描述一下 JVM 加载 Class 文件的原理机制? （蚂蚁金服）
- Java类加载过程  （美团）
- 描述一下JVM加载class文件的原理机制  （美团）
- 什么是类的加载？ （京东）
- 讲一下JVM加载一个类的过程 （京东）

#### 过程一：Loding（装载）阶段

##### 概述

**所谓装载，简而言之就是将Java类的字节码文件加载到机器内存中，并在内存中构建出Java类的原型——类模板对象。**

##### 过程一都做了什么事？

**装载阶段，简言之，查找并加载类的二进制数据，生成Class的实例。**

在加载类时，Java虚拟机必须完成以下3件事情：

- 通过类的全名，获取类的二进制数据流。
- 解析类的二进制数据流为方法区内的数据结构（Java类模型）
- 创建java.lang.Class类的实例，表示该类型。作为方法区这个类的各种数据的访问入口

##### 过程一中什么是类模板对象

所谓类模板对象，其实就是Java类在JVM内存中的一个快照，JVM将从字节码文件中解析出的常量池、类字段、类方法等信息存储到类模板中，这样JVM在运行期便能通过类模板而获取Java类中的任意信息，能够对Java类的成员变量进行遍历，也能进行Java方法的调用。

反射的机制即基于这一基础。如果JVM没有将Java类的声明信息存储起来，则JVM在运行期也无法反射。

##### 二进制流有哪些获取方式

对于类的二进制数据流，虚拟机可以通过多种途径产生或获得。（**只要所读取的字节码符合JVM规范即可**）

- 虚拟机可能通过文件系统读入一个class后缀的文件（最常见）
- 读入jar、zip等归档数据包，提取类文件
- 事先存放在数据库中的类的二进制数据
- 使用类似于HTTP之类的协议通过网络进行加载
- 在运行时生成一段Class的二进制信息等

##### Class实例的位置在哪

###### Class实例的位置 

类将.class文件加载至元空间后，会在堆中创建一个Java.lang.Class对象，用来封装类位于方法区内的数据结构，该Class对象是在加载类的过程中创建的，每个类都对应有一个Class类型的对象。(instanceKlass -->mirror :Class的实例)

###### 图示

![image-20220607153127135](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220607153127135.png)

外部可以通过访问代表Order类的Class对象来获取Order的类数据结构。

###### 再说明

Class类的构造方法是私有的，只有JVM能够创建。

java.lang.Class实例是访问类型元数据的接口，也是实现反射的关键数据、入口。通过Class类提供的接口，可以获得目标类所关联的.class文件中具体的数据结构：方法、字段等信息。

##### 数组类的加载有什么不同？

创建数组类的情况稍微有些特殊，**因为数组类本身并不是由类加载器负责创建**，而是由JVM在运行时根据需要而直接创建的，但数组的元素类型仍然需要依靠类加载器去创建。创建数组类（下述简称A）的过程：

1. 如果数组的元素类型是引用类型，那么就遵循定义的加载过程递归加载和创建数组A的元素类型；
2. JVM使用指定的元素类型和数组维度来创建新的数组类。
3. 如果数组的元素类型是引用类型，数组类的可访问性就由元素类型的可访问性决定。否则数组类的可访问性将被缺省定义为public。

```java
int[] arr
String[] arr
Object[] arr
```

#### 过程二：Linking(链接)阶段

##### 链接阶段之Verification(验证)

当类加载到系统后，就开始链接操作，验证是链接操作的第一步。

**它的目的是保证加载的字节码是合法、合理并符合规范的。**

验证的步骤比较复杂，实际要验证的项目也很繁多，大体上Java虚拟机需要做以下检査，如图所示。

![image-20220609101920156](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609101920156.png)

整体说明：

验证的内容则涵盖了类数据信息的格式验证、语义检查、字节码验证，以及符号引用验证等。

- 其中**格式验证会和装载阶段一起执行**。验证通过之后，类加载器才会成功将类的二进制数据信息加载到方法区中。
- **格式验证之外的验证操作将会在方法区中进行。**

##### 链接阶段之Preparation(准备)

简言之，**为类的静态变量分配内存，并将其初始化为默认值。**

在这个阶段，虚拟机就会为这个类分配相应的内存空间，并设置默认初始值。Java虚拟机为各类型变量默认的初始值如表所示。

![image-20220609102448604](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609102448604.png)

注意：Java并不支持boolean类型，对于boolean类型，内部实现是int,由于int的默认值是0,故对应的，boolean的默认值就是false。

##### 链接阶段之Resolution(解析)

简言之，**将类、接口、字段和方法的符号引用转为直接引用。**

符号引用就是一些字面量的引用，和虚拟机的内部数据结构和和内存布局无关。比较容易理解的就是在Class类文件中，通过常量池进行了大量的符号引用。但是在程序实际运行时，只有符号引用是不够的，比如当如下println()方法被调用时，系统需要明确知道该方法的位置。

举例：输出操作System.out.println()对应的字节码：
```shell
invokevirtual #24 <java/io/PrintStream.println>
```

![image-20220609102556827](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609102556827.png)

以方法为例，Java虚拟机为每个类都准备了一张方法表，将其所有的方法都列在表中，当需要调用一个类的方法的时候，只要知道这个方法在方法表中的偏移量就可以直接调用该方法。**通过解析操作，符号引用就可以转变为目标方法在类中方法表中的位置，从而使得方法被成功调用。**

**所谓解析就是将符号引用转为直接引用，也就是得到类、字段、方法在内存中的指针或者偏移量。因此，可以说，如果直接引用存在，那么可以肯定系统中存在该类、方法或者字段。但只存在符号引用，不能确定系统中一定存在该结构。**

不过Java虚拟机规范并没有明确要求解析阶段一定要按照顺序执行。在HotSpot VM中，加载、验证、准备和初始化会按照顺序有条不紊地执行，但链接阶段中的解析操作往往会伴随着JVM在执行完初始化之后再执行。

#### 过程三：Initialization(初始化)阶段

##### 概述

**初始化阶段，简言之，为类的静态变量赋予正确的初始值。(显式初始化)**

类的初始化是类装载的最后一个阶段。如果前面的步骤都没有问题，那么表示类可以顺利装载到系统中。此时，类才会开始执行Java字节码。（即：**到了初始化阶段，才真正开始执行类中定义的 Java 程序代码。**）

**初始化阶段的重要工作是执行类的初始化方法：``<clinit>()``方法。**

- 该方法仅能由Java编译器生成并由JVM调用，程序开发者无法自定义一个同名的方法，更无法直接在Java程序中调用该方法，虽然该方法也是由字节码指令所组成。

- 它是由类静态成员的赋值语句以及static语句块合并产生的。

##### 子类加载前先加载父类？

在加载一个类之前，虚拟机总是会试图加载该类的父类，因此父类的``<clinit>``总是在子类``<clinit>``之前被调用。也就是说，父类的static块优先级高于子类。 

口诀：由父及子，静态先行。

##### 哪些类不会生成``<clinit>``方法？

Java编译器并不会为所有的类都产生``<clinit>()``初始化方法。哪些类在编译为字节码后，字节码文件中将不会包含``<clinit>()``方法？

- 一个类中并没有声明任何的类变量，也没有静态代码块时
- 一个类中声明类变量，但是没有明确使用类变量的初始化语句以及静态代码块来执行初始化操作时
- 一个类中包含static final修饰的基本数据类型的字段，这些类字段初始化语句采用编译时常量表达式

##### 代码举例：static与final的搭配问题

```java
/**
 * 哪些场景下，java编译器就不会生成<clinit>()方法
 */
public class InitializationTest1 {
    //场景1：对于非静态的字段，不管是否进行了显式赋值，都不会生成<clinit>()方法
    public int num = 1;
    //场景2：静态的字段，没有显式的赋值，不会生成<clinit>()方法
    public static int num1;
    //场景3：比如对于声明为static final的基本数据类型的字段，不管是否进行了显式赋值，都不会生成<clinit>()方法
    public static final int num2 = 1;
}



/**
 *
 * 一、说明：使用static + final修饰的字段的显式赋值的操作，到底是在哪个阶段进行的赋值？
 * 情况1：在链接阶段的准备环节赋值
 * 情况2：在初始化阶段<clinit>()中赋值
 *
 * 二、结论：
 * 1. 在链接阶段的准备环节赋值的情况：
 * 1）对于基本数据类型的字段来说，如果使用static final修饰，则显式赋值(直接赋值常量，而非调用方法）通常是在链接阶段的准备环节进行
 * 2）对于String来说，如果使用字面量的方式赋值，使用static final修饰的话，则显式赋值通常是在链接阶段的准备环节进行
 *
 * 2. 在初始化阶段<clinit>()中赋值的情况：
 * 排除上述的在准备环节赋值的情况之外的情况。
 *
 *
 *  总结：
 *  使用static + final 修饰的成员变量，称为：全局常量。
 *  什么时候在链接阶段的准备环节：给此全局常量附的值是字面量或常量。不涉及到方法或构造器的调用。
 *  除此之外，都是在初始化环节赋值的。
 *
 */
public class InitializationTest2 {
//    public static int a = 1;   //在初始化阶段赋值
//    public static final int INT_CONSTANT = 10;   //在链接阶段的准备环节赋值
//
//    public static Integer INTEGER_CONSTANT1 = Integer.valueOf(100); //在初始化阶段赋值
//    public static final Integer INTEGER_CONSTANT2 = Integer.valueOf(1000); //在初始化阶段赋值
//
    public static final String s0 = "helloworld0";   //在链接阶段的准备环节赋值
    public static final String s1 = new String("helloworld1"); //在初始化阶段赋值
//
    public static String s2 = "helloworld2";  //在初始化阶段赋值
//
    public static final int NUM1 = new Random().nextInt(10);  //在初始化阶段赋值

    static int a = 9;//在初始化阶段赋值
    static final int b = a; //在初始化阶段赋值

}

```

##### `<clinit>()`的调用会死锁吗？

对于``<clinit>()``方法的调用，也就是类的初始化，虚拟机会在内部确保其多线程环境中的安全性。

**虚拟机会保证一个类的``<clinit>()``方法在多线程环境中被正确地加锁、同步**，如果多个线程同时去初始化一个类，那么只会有一个线程去执行这个类的``<clinit>()``方法，其他线程都需要阻塞等待，直到活动线程执行``<clinit>()``方法完毕。

**正是因为函数``<clinit>()``带锁线程安全的**，因此，如果在一个类的``<clinit>()``方法中有耗时很长的操作，就可能造成多个线程阻塞，引发死锁。并且这种死锁是很难发现的，因为看起来它们并没有可用的锁信息。

如果之前的线程成功加载了类，则等在队列中的线程就没有机会再执行``<clinit>()``方法了。那么，当需要使用这个类时，虚拟机会直接返回给它已经准备好的信息。

```java
class StaticA {
    static {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
        }
        try {
            Class.forName("com.atguigu.java.StaticB");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        System.out.println("StaticA init OK");
    }
}

class StaticB {
    static {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
        }
        try {
            Class.forName("com.atguigu.java.StaticA");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        System.out.println("StaticB init OK");
    }
}

public class StaticDeadLockMain extends Thread {
    private char flag;

    public StaticDeadLockMain(char flag) {
        this.flag = flag;
        this.setName("Thread" + flag);
    }

    @Override
    public void run() {
        try {
            Class.forName("com.atguigu.java.Static" + flag);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        System.out.println(getName() + " over");
    }

    public static void main(String[] args) throws InterruptedException {
        StaticDeadLockMain loadA = new StaticDeadLockMain('A');
        loadA.start();
        StaticDeadLockMain loadB = new StaticDeadLockMain('B');
        loadB.start();
    }
}
```

##### 类的初始化情况：主动使用vs被动使用

###### 主动使用的情况

**Class只有在必须要首次使用的时候才会被装载，Java虚拟机不会无条件地装载Class类型。Java虚拟机规定，一个类或接口在初次使用前，必须要进行初始化。这里指的“使用”，是指主动使用。**

主动使用只有下列几种情况：（即：如果出现如下的情况，则会对类进行初始化操作。而初始化操作之前的加载、验证、准备已经完成。）

1. 当创建一个类的实例时，比如使用new关键字，或者通过反射、克隆、反序列化。
2. 当调用类的静态方法时，即当使用了字节码invokestatic指令。
3. 当使用类、接口的静态字段时(final修饰特殊考虑)，比如，使用getstatic或者putstatic指令。
4. 当使用java.lang.reflect包中的方法反射类的方法时。比如：Class.forName("com.atguigu.java.Test")
5. 当初始化子类时，如果发现其父类还没有进行过初始化，则需要先触发其父类的初始化。
6. **如果一个接口定义了default方法，那么直接实现或者间接实现该接口的类的初始化，该接口要在其之前被初始化。**
7. 当虚拟机启动时，用户需要指定一个要执行的主类（包含main()方法的那个类），虚拟机会先初始化这个主类。
8. 当初次调用 MethodHandle 实例时，初始化该 MethodHandle 指向的方法所在的类。（涉及解析REF_getStatic、REF_putStatic、REF_invokeStatic方法句柄对应的类）

###### 被动使用的情况

除了以上的情况属于主动使用，其他的情况均属于被动使用。被动使用不会引起类的初始化。

也就是说：并不是在代码中出现的类，就一定会被加载或者初始化。如果不符合主动使用的条件，类就不会初始化。

1. 当访问一个静态字段时，只有真正声明这个字段的类才会被初始化。
  - 当通过子类引用父类的静态变量，不会导致子类初始化

2. 通过数组定义类引用，不会触发此类的初始化
3. 引用常量不会触发此类或接口的初始化。因为常量在链接阶段就已经被显式赋值了。
4. 调用ClassLoader类的loadClass()方法加载一个类，并不是对类的主动使用，不会导致类的初始化。

> 被动的使用，意味着不需要执行初始化环节，意味着没有``<clinit>()``的调用。

###### 打印加载过程

如果针对代码，设置参数``-XX:+TraceClassLoading``，可以追踪类的加载信息并打印出来。

###### 面试题

- 类加载的时机 （百度）
- Class的forName("Java.lang.String")和Class的getClassLoader()的loadClass("Java.lang.String")有什么区别？  （百度）
- 哪些情况会触发类的加载？ （京东）

类的加载 = 装载+链接（①②③）+初始化

#### 面试题

##### 面试题1

```java
class Root{
    static{
        System.out.println("Root的静态初始化块");
    }
    {
        System.out.println("Root的普通初始化块");
    }
    public Root(){
        System.out.println("Root的无参数的构造器");
    }
}
class Mid extends Root{
    static{
        System.out.println("Mid的静态初始化块");
    }
    {
        System.out.println("Mid的普通初始化块");
    }
    public Mid(){
        System.out.println("Mid的无参数的构造器");
    }
    public Mid(String msg){
        //通过this调用同一类中重载的构造器
        this();
        System.out.println("Mid的带参数构造器，其参数值："
                           + msg);
    }
}
class Leaf extends Mid{
    static{
        System.out.println("Leaf的静态初始化块");
    }
    {
        System.out.println("Leaf的普通初始化块");
    }
    public Leaf(){
        //通过super调用父类中有一个字符串参数的构造器
        super("尚硅谷");
        System.out.println("Leaf的构造器");
    }
}
public class LeafTest{
    public static void main(String[] args){
        new Leaf(); 
        System.out.println();
        new Leaf();
    }
}
```

##### 面试题2

```java
public class Test {
    static int x, y, z;

    static {
        int x = 5;
        x--;
    }

    static {
        x--;
    }

    public static void main(String[] args) {
        System.out.println("x=" + x);
        z--;
        method();
        System.out.println("result:" + (z + y + ++z));
    }

    public static void method() {
        y = z++ + ++z;
    }
}
```

##### 面试题3

```java
public class Test03 {
    public static void main(String[] args) {
        Father f = new Son();
        System.out.println(f.x);
    }
}
class Father{
    int x = 10;
    public Father(){
        this.print();
        x = 20;
    }
    public void print(){
        System.out.println("Father.x = " + x);
    }
}
class Son extends Father{
    int x = 30;
    public Son(){
        this.print();
        x = 40;
    }
    public void print(){
        System.out.println("Son.x = " + x);
    }
}
```

##### 面试题4

```java
// 这个题含金量很高
public class T {
    public static int k = 0;
    public static T t1 = new T("t1");
    public static T t2 = new T("t2");
    public static int i = print("i");
    public static int n = 99;

    public int j = print("j");

    {
        print("构造块");
    }

    static {
        print("静态块");
    }

    public T(String str) {
        System.out.println((++k) + ":" + str + "  i=" + i + "  n=" + n);
        ++n;
        ++i;
    }

    public static int print(String str) {
        System.out.println((++k) + ":" + str + "  i=" + i + "  n=" + n);
        ++n;
        return ++i;
    }

    public static void main(String[] args) {

    }
}
```

#### 过程四：类的Using(使用)

任何一个类型在使用之前都必须经历过完整的加载、链接和初始化3个类加载步骤。一旦一个类型成功经历过这3个步骤之后，便“万事俱备，只欠东风”，就等着开发者使用了。

开发人员可以在程序中访问和调用它的静态类成员信息（比如：静态字段、静态方法），或者使用new关键字为其创建对象实例。

#### 过程五：类的Unloading(卸载)

##### 类、类的加载器、类的实例之间的引用关系

在类加载器的内部实现中，用一个Java集合来存放所加载类的引用。另一方面，一个Class对象总是会引用它的类加载器，调用Class对象的getClassLoader()方法，就能获得它的类加载器。由此可见，代表某个类的Class实例与其类的加载器之间为双向关联关系。

一个类的实例总是引用代表这个类的Class对象。在Object类中定义了getClass()方法，这个方法返回代表对象所属类的Class对象的引用。此外，所有的Java类都有一个静态属性class，它引用代表这个类的Class对象。

![image-20220609143239897](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609143239897.png)

##### 何种情况类会被卸载？

**一个类何时结束生命周期，取决于代表它的Class对象何时结束生命周期。**

当Sample类被加载、链接和初始化后，它的生命周期就开始了。当代表Sample类的Class对象不再被引用，即不可触及时，Class对象就会结束生命周期，Sample类在方法区内的数据也会被卸载，从而结束Sample类的生命周期。

举例：

![image-20220609143718843](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609143718843.png)

loader1变量和obj变量间接引用代表Sample类的Class对象，而objClass变量则直接引用它。

如果程序运行过程中，将上图左侧三个引用变量都置为null，此时Sample对象结束生命周期，MyClassLoader对象结束生命周期，代表Sample类的Class对象也结束生命周期，Sample类在方法区内的二进制数据**被卸载**。

当再次有需要时，会检查Sample类的Class对象是否存在，**如果存在会直接使用，不再重新加载**；如果不存在Sample类会被重新加载，在Java虚拟机的堆区会生成一个新的代表Sample类的Class实例(可以通过哈希码查看是否是同一个实例)。

##### 类卸载在实际生产中的情况如何？

(1) 启动类加载器加载的类型在整个运行期间是不可能被卸载的(jvm和jls规范)

(2) 被系统类加载器和扩展类加载器加载的类型在运行期间不太可能被卸载，因为系统类加载器实例或者扩展类的实例基本上在整个运行期间总能直接或者间接的访问的到，其达到unreachable的可能性极小。

(3) 被开发者自定义的类加载器实例加载的类型只有在很简单的上下文环境中才能被卸载，而且一般还要借助于强制调用虚拟机的垃圾收集功能才可以做到。可以预想，稍微复杂点的应用场景中(比如：很多时候用户在开发自定义类加载器实例的时候采用缓存的策略以提高系统性能)，被加载的类型在运行期间也是几乎不太可能被卸载的(至少卸载的时间是不确定的)。

综合以上三点，一个已经加载的类型被卸载的几率很小至少被卸载的时间是不确定的。同时我们可以看的出来，开发者在开发代码时候，不应该对虚拟机的类型卸载做任何假设的前提下，来实现系统中的特定功能。

##### 扩展：方法区的垃圾回收

方法区的垃圾收集主要回收两部分内容：**常量池中废弃的常量和不再使用的类型。**

HotSpot虚拟机对常量池的回收策略是很明确的，只要常量池中的常量没有被任何地方引用，就可以被回收。

判定一个常量是否“废弃”还是相对简单，而要判定一个类型是否属于“不再被使用的类”的条件就比较苛刻了。需要同时满足下面三个条件： 

- **该类所有的实例都已经被回收。也就是Java堆中不存在该类及其任何派生子类的实例。** 
- **加载该类的类加载器已经被回收。这个条件除非是经过精心设计的可替换类加载器的场景，如OSGi、JSP的重加载等，否则通常是很难达成的。** 
- **该类对应的java.lang.Class对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。**

Java虚拟机被允许对满足上述三个条件的无用类进行回收，这里说的仅仅是“被允许”，而并不是和对象一样，没有引用了就必然会回收。

### 类的加载器

#### 面试题

#### 作用

类加载器是 JVM 执行类加载机制的前提。

ClassLoader的作用：

ClassLoader是Java的核心组件，所有的Class都是由ClassLoader进行加载的，ClassLoader负责通过各种方式将Class信息的二进制数据流读入JVM内部，转换为一个与目标类对应的java.lang.Class对象实例。然后交给Java虚拟机进行链接、初始化等操作。

**因此，ClassLoader在整个装载阶段，只能影响到类的加载**，而无法通过ClassLoader去改变类的链接和初始化行为。至于它是否可以运行，则由Execution Engine决定。

![image-20220609145126761](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609145126761.png)

#### 类加载的显示加载与隐式加载

class文件的显式加载与隐式加载的方式是指JVM加载class文件到内存的方式。

**显式加载**：指的是在代码中通过调用ClassLoader加载class对象，如直接使用Class.forName(name)或this.getClass().getClassLoader().loadClass()加载class对象。

**隐式加载**：则是不直接在代码中调用ClassLoader的方法加载class对象，而是通过虚拟机自动加载到内存中，如在加载某个类的class文件时，该类的class文件中引用了另外一个类的对象，此时额外引用的类将通过JVM自动加载到内存中。

在日常开发以上两种方式一般会混合使用。

#### 类加载机制的必要性

一般情况下，Java开发人员并不需要在程序中显式地使用类加载器，但是了解类加载器的加载机制却显得至关重要。从以下几个方面说：

- 避免在开发中遇到 java.lang.ClassNotFoundException异常或java.lang.NoClassDefFoundError异常时，手足无措。只有了解类加载器的加载机制才能够在出现异常的时候快速地根据错误异常日志定位问题和解决问题

- 需要支持类的动态加载或需要对编译后的字节码文件进行**加解密操作**时，就需要与类加载器打交道了。

- 开发人员可以在程序中编写**自定义类加载器**来重新定义类的加载规则，以便实现一些自定义的处理逻辑。

#### 加载的类是唯一的吗？

##### 何为类的唯一性？

对于任意一个类，**都需要由加载它的类加载器和这个类本身一同确认其在Java虚拟机中的唯一性。**每一个类加载器，都拥有一个独立的类名称空间：**比较两个类是否相等，只有在这两个类是由同一个类加载器加载的前提下才有意义。**否则，即使这两个类源自同一个Class文件，被同一个虚拟机加载，只要加载他们的类加载器不同，那这两个类就必定不相等。

##### 命名空间

- 每个类加载器都有自己的命名空间，命名空间由该加载器及所有的父加载器所加载的类组成
- 在同一命名空间中，不会出现类的完整名字（包括类的包名）相同的两个类
- 在不同的命名空间中，有可能会出现类的完整名字（包括类的包名）相同的两个类

在大型应用中，我们往往借助这一特性，来运行同一个类的不同版本。

#### 类加载机制的基本特征

通常类加载机制有三个基本特征：

- **双亲委派模型**：但不是所有类加载都遵守这个模型，有的时候，启动类加载器所加载的类型，是可能要加载用户代码的，比如JDK内部的ServiceProvider/ServiceLoader机制，用户可以在标准API框架上，提供自己的实现，JDK也需要提供些默认的参考实现。例如，Java 中JNDI、JDBC、文件系统、Cipher等很多方面，都是利用的这种机制，这种情况就不会用双亲委派模型去加载，而是利用所谓的上下文加载器。
- **可见性：子类加载器可以访问父加载器加载的类型**，但是反过来是不允许的。不然，因为缺少必要的隔离，我们就没有办法利用类加载器去实现容器的逻辑。
- **单一性：**由于父加载器的类型对于子加载器是可见的，所以父加载器中加载过的类型，就不会在子加载器中重复加载。但是注意，**类加载器“邻居”间，同一类型仍然可以被加载多次**，因为互相并不可见。

### 类的加载器分类与测试

#### 类加载器的分类说明

JVM支持两种类型的类加载器，分别为引导类加载器（Bootstrap ClassLoader）和自定义类加载器（User-Defined ClassLoader）。

从概念上来讲，自定义类加载器一般指的是程序中由开发人员自定义的一类类加载器，但是Java虚拟机规范却没有这么定义，**而是将所有派生于抽象类ClassLoader的类加载器都划分为自定义类加载器。**

无论类加载器的类型如何划分，在程序中我们最常见的类加载器结构主要是如下情况：

![image-20220609153808144](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609153808144.png)

#### 子父类加载器的关系

- 除了顶层的启动类加载器外，其余的类加载器都应当有自己的“父类”加载器。 
- 不同类加载器看似是继承（Inheritance）关系，实际上是包含关系。在下层加载器中，包含着上层加载器的引用。

#### 具体类的加载器介绍

##### 引导类加载器

**启动类加载器（引导类加载器，Bootstrap ClassLoader）**

- 这个类加载使用C/C++语言实现的，嵌套在JVM内部。
- 它用来加载Java的核心库（JAVA_HOME/jre/lib/rt.jar或sun.boot.class.path路径下的内容）。用于提供JVM自身需要的类。
- 并不继承自java.lang.ClassLoader，没有父加载器。
- 出于安全考虑，Bootstrap启动类加载器只加载包名为java、javax、sun等开头的类
- 加载扩展类和应用程序类加载器，并指定为他们的父类加载器。

![image-20220609154334208](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609154334208.png)

![image-20220609154352398](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609154352398.png)

使用``-XX:+TraceClassLoading``参数得到。

##### 扩展类加载器

**扩展类加载器（Extension ClassLoader）**

- Java语言编写，由sun.misc.Launcher$ExtClassLoader实现。
- 继承于ClassLoader类
- 父类加载器为启动类加载器
- 从java.ext.dirs系统属性所指定的目录中加载类库，或从JDK的安装目录的jre/lib/ext子目录下加载类库。如果用户创建的JAR放在此目录下，也会自动由扩展类加载器加载。

![image-20220609154501388](../../../../assets/07-JVM%E5%8E%9F%E7%90%86%EF%BC%88%E8%BF%9B%E9%98%B6%E7%89%88%EF%BC%89/image-20220609154501388.png)

##### 系统类加载器

**应用程序类加载器（系统类加载器，AppClassLoader）**

- java语言编写，由sun.misc.Launcher$AppClassLoader实现
  继承于ClassLoader类
- 父类加载器为扩展类加载器
- 它负责加载环境变量classpath或系统属性 java.class.path 指定路径下的类库 
- **应用程序中的类加载器默认是系统类加载器。**
- 它是用户自定义类加载器的默认父加载器
- 通过ClassLoader的getSystemClassLoader()方法可以获取到该类加载器

#### 用户自定义类加载器

- 在Java的日常应用程序开发中，类的加载几乎是由上述3种类加载器相互配合执行的。在必要时，我们还可以自定义类加载器，来定制类的加载方式。
- 体现Java语言强大生命力和巨大魅力的关键因素之一便是,Java开发者可以自定义类加载器来实现类库的动态加载，加载源可以是本地的JAR包，也可以是网络上的远程资源。
- **通过类加载器可以实现非常绝妙的插件机制**，这方面的实际应用案例举不胜举。例如，著名的OSGI组件框架，再如Eclipse的插件机制。类加载器为应用程序提供了一种动态增加新功能的机制，这种机制无须重新打包发布应用程序就能实现。
- 同时，**自定义加载器能够实现应用隔离**，例如 Tomcat，Spring等中间件和组件框架都在内部实现了自定义的加载器，并通过自定义加载器隔离不同的组件模块。这种机制比C/C++程序要好太多，想不修改C/C++程序就能为其新增功能，几乎是不可能的，仅仅一个兼容性便能阻挡住所有美好的设想。
- 所有用户自定义类加载器通常需要**继承于抽象类java.lang.ClassLoader。**

#### 测试不同的类加载器

每个Class对象都会包含一个定义它的ClassLoader的一个引用。

获取ClassLoader的途径

```
获得当前类的ClassLoader
clazz.getClassLoader()

获得当前线程上下文的ClassLoader
Thread.currentThread().getContextClassLoader()
 
获得系统的ClassLoader
ClassLoader.getSystemClassLoader()
```

说明：站在程序的角度看，引导类加载器与另外两种类加载器（系统类加载器和扩展类加载器）并不是同一个层次意义上的加载器，引导类加载器是使用C++语言编写而成的，而另外两种类加载器则是使用Java语言编写而成的。由于引导类加载器压根儿就不是一个Java类，因此在Java程序中只能打印出空值。

### ClassLoader源码剖析

#### 面试题

- 深入分析ClassLoader（蚂蚁金服）

#### ClassLoader与现有类加载的关系



#### ClassLoader的主要方法

#### SecureClassLoader与URLClassLoader

#### ExtClassLoader与AppClassLoader

#### Class.forName与ClassLoader.loadClass()对比

### 自定义类的加载器

#### 为什么要自定义类的加载器

#### 应用场景有哪些？

#### 两种实现方式

- 重写loadClass()方法

- 重写findClass()方法

````java
public class UserDefineClassLoader extends ClassLoader {

    private String rootPath;

    public UserDefineClassLoader(String rootPath) {
        this.rootPath = rootPath;
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {

        //转换为以文件路径表示的文件
        String filePath = classToFilePath(name);

        //获取指定路径的class文件对应的二进制流数据
        byte[] data = getBytesFromPath(filePath);

        //自定义ClassLoader 内部调用defineClass()
        return defineClass(name,data,0,data.length);

    }

    private byte[] getBytesFromPath(String filePath) {

        FileInputStream fis = null;
        ByteArrayOutputStream baos = null;
        try {
            fis = new FileInputStream(filePath);

            baos = new ByteArrayOutputStream();

            byte[] buffer = new byte[1024];
            int len;

            while ((len = fis.read(buffer)) != -1) {
                baos.write(buffer, 0, len);
            }

            return baos.toByteArray();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (baos != null)
                    baos.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                if (fis != null)
                    fis.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return null;

    }

    private String classToFilePath(String name) {

        return rootPath + "\\" + name.replace(".", "\\") + ".class";

    }

    public static void main(String[] args) {  //加载com.atguigu.java3.User

        try {
            UserDefineClassLoader loader1 = new UserDefineClassLoader("D:\\code\\workspace_teach\\JVMdachang210416\\chapter02_classload\\src");
            Class userClass1 = loader1.findClass("com.atguigu.java3.User");
            System.out.println(userClass1);

            UserDefineClassLoader loader2 = new UserDefineClassLoader("D:\\code\\workspace_teach\\JVMdachang210416\\chapter02_classload\\src");
            Class userClass2 = loader2.findClass("com.atguigu.java3.User");

            System.out.println(userClass1 == userClass2);//实现了加载的类的隔离


            System.out.println(userClass1.getClassLoader());
            System.out.println(userClass1.getClassLoader().getParent());


        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } finally {
        }
    }
}
````

##### 对比

这两种方法本质上差不多，毕竟loadClass()也会调用findClass()，但是从逻辑上讲我们最好不要直接修改loadClass()的内部逻辑。建议的做法是只在findClass()里重写自定义类的加载方法，根据参数指定类的名字，返回对应的Class对象的引用。

##### 面试题

手写一个类加载器Demo （百度）

#### 双亲委派机制

##### 面试题

- 双亲委派好处有哪些？（亚信）
- 类加载器双亲委派模型机制？（苏宁）
- 双亲委派机制 （蚂蚁金服）
- 双亲委派机制及使用原因 （蚂蚁金服）
- 类加载器的双亲委派模型是什么？ （蚂蚁金服）
- 双亲委派模型介绍一下  （小米）
- 讲一下双亲委派模型，以及其优点  （滴滴）
- 类加载器的双亲委派模型是什么？ （京东）

##### 定义与本质

##### 如何证明？源码分析

##### 优势与劣势

###### 双亲委派机制优势

- **避免类的重复加载，确保一个类的全局唯一性**

**Java类随着它的类加载器一起具备了一种带有优先级的层次关系，通过这种层级关系可以避免类的重复加载**，当父亲已经加载了该类时，就没有必要子ClassLoader再加载一次。

- **保护程序安全，防止核心API被随意篡改**

###### 双亲委托模式的弊端

检查类是否加载的委托过程是单向的，这个方式虽然从结构上说比较清晰，使各个ClassLoader的职责非常明确，但是同时会带来一个问题，即顶层的ClassLoader无法访问底层的ClassLoader所加载的类。

通常情况下，启动类加载器中的类为系统核心类，包括一些重要的系统接口，而在应用类加载器中，为应用类。按照这种模式，**应用类访问系统类自然是没有问题，但是系统类访问应用类就会出现问题。**比如在系统类中提供了一个接口，该接口需要在应用类中得以实现，该接口还绑定一个工厂方法，用于创建该接口的实例，而接口和工厂方法都在启动类加载器中。这时，就会出现该工厂方法无法创建由应用类加载器加载的应用实例的问题。

###### 结论

**由于Java虚拟机规范并没有明确要求类加载器的加载机制一定要使用双亲委派模型，只是建议采用这种方式而已。**

**比如在Tomcat中，类加载器所采用的加载机制就和传统的双亲委派模型有一定区别，当缺省的类加载器接收到一个类的加载任务时，首先会由它自行加载，当它加载失败时，才会将类的加载任务委派给它的超类加载器去执行，这同时也是Servlet规范推荐的一种做法。**

##### 破坏双亲委派机制及举例

这里，我们使用了“被破坏”这个词来形容上述不符合双亲委派模型原则的行为，但这里“被破坏”并不一定是**带有贬义的。只要有明确的目的和充分的理由，突破旧有原则无疑是一种创新。**

正如：破坏双亲委派机制3的OSGi中的类加载器的设计不符合传统的双亲委派的类加载器架构，且业界对其为了实现热部署而带来的额外的高复杂度还存在不少争议，但对这方面有了解的技术人员基本还是能达成一个共识，认为**OSGi中对类加载器的运用是值得学习的，完全弄懂了OSGi的实现，就算是掌握了类加载器的精粹**。

###### 面试题

- 双亲委派机制可以打破吗？为什么 （京东）
- 可以打破双亲委派么，怎么打破。（拼多多）

###### 破坏双亲委派机制1-重写loadClass()方法

###### 破坏双亲委派机制2-线程上下文类加载器

###### 破坏双亲委派机制3-代码热替换

###### 热替换的实现

##### 面试题

###### 源码分析

###### 5个子问题

#### 沙箱安全机制

##### JDK1.0 时期

##### JDK1.1 时期

##### JDK1.2 时期

##### JDK1.6 时期

### JDK中类加载结构的新变化

## 运行时内存篇

## 对象内存布局

## 执行引擎篇

## 垃圾回收篇

## JVM性能监控篇

## JVM性能调优案例篇