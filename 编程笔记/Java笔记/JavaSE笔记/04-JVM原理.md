# JVM原理

## JVM整理认知

### Java文档

* [https://docs.oracle.com/javase/specs/index.html](https://docs.oracle.com/javase/specs/index.html)

### 问题思考

- Java代码Why Write Once Run Anywhere ?

- JRuby，Groovy，Scala，kotlin等语言代码为什么能运行在JVM之上?

- Java语言到底是解释型的语言还是编译型的语言?
- Java代码到底是怎么执行的?

### 什么是JVM（Java 虚拟机）

**Java 虚拟机是 Java 平台的基石，是JAVA语言实现跨平台的根本**。它是技术的组成部分，负责其硬件和操作系统独立性、编译代码的小尺寸以及保护用户免受恶意程序侵害的能力。

**Java虚拟机是虚拟机架构，其定义了自己的指令集及不同运行时期的内存区域**。使用虚拟机实现编程语言是相当普遍的；最著名的虚拟机可能是 UCSD Pascal 的 P-Code 机器。

**Java 虚拟机**对 Java 编程语言一无所知，**只知道一种特定的二进制格式，即`class`文件格式。文件`class`包含 Java 虚拟机指令（或字节码）和符号表，以及其他辅助信息。说白了就是JVM只在乎class file(字节码文件)。**

为了安全起见，Java 虚拟机对`class`文件中的代码施加了强大的语法和结构约束。**但是，任何具有可以用有效`class`文件表示的功能的语言（比如JRuby，Groovy，Scala，kotlin等语言代码）都可以由 Java 虚拟机托管。**被一个普遍可用的、独立于机器的平台所吸引，其他语言的实现者可以将 Java 虚拟机作为他们语言的交付工具。**因此JVM是一个多语言的平台**。

**Java即是编译型的，也是解释型语言，总的来说Java更接近解释型语言。 可以说它是编译型的。 因为所有的Java代码都是要编译的，.java不经过编译就什么用都没有。**

### JVM、JRE、JDK三者关系

#### 概念

JDK: java development kit, java开发工具包，用来开发Java程序的，针对java开发者。

JRE: java runtime environment, java运行时环境，针对java用户

JVM: java virtual machine，java虚拟机 用来解释执行字节码文件(class文件)的。

![image-20220216202440092](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258773.png)

#### **JDK**

JDK 即为 Java 开发工具包，包含编写 Java 程序所必须的编译、运行等开发工具以及 JRE。开发工具如：

- 用于编译 Java 程序的 javac 命令。
- 用于启动 JVM 运行 Java 程序的 Java 命令。
- 用于生成文档的 Javadoc 命令。
- 用于打包的 jar 命令等等。

> 简单说，就是 JDK 包含 JRE 包含 JVM。

#### **JRE**

JRE 即为 Java 运行环境，提供了运行 Java 应用程序所必须的软件环境，包含有 Java 虚拟机（JVM）和丰富的系统类库。系统类库即为 Java 提前封装好的功能类，只需拿来直接使用即可，可以大大的提高开发效率。

> 简单说，就是 JRE 包含 JVM。

#### **JVM**

JVM 即为 Java 虚拟机，提供了字节码文件(`.class`)的运行环境支持。

### 类加载子系统及类加载器

#### 类加载流程

![image-20220216204905893](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258775.png)

当程序主动使用某个类时，如果该类还未被加载到内存中，则系统会通过如下三个步骤来对该类进行初始化。

![image-20220214170130567](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258776.png)

**加载**：将class文件字节码内容加载到内存中，并将这些静态数据转换成方法区的运行时数据结构，然后生成一个代表这个类的`java.lang.Class`对象。

**链接**：将Java类的二进制代码合并到JVM的运行状态之中的过程。

* 验证：确保加载的类信息符合JVM规范，没有安全方面的问题
* 准备：[**正式为类变量(static) 分配内存并设置类变量默认初始值的阶段,这些内存都将在方法区中进行分配。**](#什么时候会发生类初始化)
* 解析：虚拟机常量池内的符号引用（常量名）替换为直接引用（地址）的过程。

**初始化**：

* 执行类构造器``<clinit> ()``方法的过程。类构造器``<clinit> ()``方法是由编译期自动收集类中所有类变量的赋值动作和静态代码块中的语句合并产生的。(类构造器是构造类信息的，不是构造该类对象的构造器)。（class的初始化只会执行一次）
* 当初始化一个类的时候，如果发现其父类还没有进行初始化，则需要先触发其父类的初始化。
* 虚拟机会保证一个类的``<clinit>()``方法在多线程环境中被正确加锁和同步。

#### 什么时候会发生类初始化

类的主动引用（一定会发生类的初始化）：

* 当虚拟机启动，先初始化main方法所在的类
* 创建类的实例，及其四种方式：
  * 通过`new`对象
  * 通过克隆
  * 通过反射机制
  * 通过反序列化
* 调用类的静态成员(除了final常量)和静态方法
* 使用`java.lang.reflect`包的方法`Class.forName(包类名)`对类进行反射调用
* 当初始化一个类, 如果其父类没有被初始化，则先会初始化它的父类
* 该类是程序引导入口（main入口或者test入口）

类的被动引用（不会发生类的初始化）：

* 当访问一个静态域时，只有真正声明这个域的类才会被初始化。如：当通过子类引用父类的静态变量，不会导致子类初始化
* 通过数组定义类引用，不会触发此类的初始化（是因为只是申请了空间，而没有真正的去初始化这个类的实例）
* 引用**常量**不会触发此类的初始化（常量在链接阶段就存入调用类的常量池中了）（是因为常量和静态变量在类被加载的时候，就已经单独给其开辟了空间进行存储，因此可以通过class直接进行引用）

#### 类加载器

- 类加载器可理解为类加载流程的实现者
- 类加载器都是以单例形式存在的
- Class的加载分为显示加载（自定义加载器，继承父类加载器）和隐式加载（默认使用类加载的流程）

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202161921274.png" alt="image-20220214173133939" style="zoom: 67%;" />

**类加载的作用**：将class文件字节码内容加载到内存中，并将这些静态数据转换成方法区的运行时数据结构，然后在堆中生成一个代表这个类的java.lang（Class对象）作为方法区中类数据的访问入口。

**类缓存**：标准的JavaSE类加载器可以按要求查找类,但一旦某企类被加载到类加载器中，它将维持加载（缓存）一段时间。不过JVM垃圾回收机制可以回收这些Class对象

类加载器作用是用来把类(class)装载进内存的。JVM规范走义了如下类型的类的加载器。

![image-20220214173836204](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258777.png)

- **引导类加载器（Bootstrap Classloader），又称为根类加载器**：
  - 它负责加载 Java 的核心库（``JAVA_HOME/jre/lib/rt.jar`` 等或 `sun.boot.class.path `路径下的内容）或java程序运行指定的``-Xbootclasspath`目录下jar包。
  - 是用原生代码（C/C++）来实现的，并不继承自 `java.lang.ClassLoader`，所以通过 Java 代码获取引导类加载器对象将会得到 null。（只有核心类库如 String 才使用 引导类加载器）
  - java，javax，sun等开头的规定的包名类必须用Bootstrap进行加载。
- **扩展类加载器（Extension Classloader**）：
  - Java语言编写的类加载器`sun.mise.Launcher$ExtClassLoader` ，父类加载器为null
  - 指定``Bootstrap Classloader``实例为Parent加载器，可通过`getParent()`可以获取``Bootstrap Classloader`的单例实例
  - 负责加载java平台中扩展功能的一些jar包，包括​``$JAVA_HOME`中 `jre/lib/ext`或环境参数`-Djava.ext.dirs`指定目录下的jar包(如果我们自定义的class需要交给Ext来加载可以放置到ext的自录下)

- **应用程序（系统）类加载器（Application Classloader）**：
  - 它由 `sun.misc.Lanuncher$AppClassLoader` 实现，是 `java.lang.ClassLoader`的子类，父类加载器为``ExtClassLoader``
  - 该加载器是Java程序默认的类加载器，开发者可以直接使用系统类加载器。
  - 指定``Extension Classloader``实例为parent加载器,可通过``getParent()``可以获取 `Extension Classloader`的单例实例
  - 负责加载环境变量``classpath``指定的目录,或者``-Djava.class.path``指定的目录类库
- **自定义类加载器**（显示加载）：开发人员可以通过继承``java.lang.ClassLoader``类的方式实现自己的类加载器（父类加载器为AppClassLoader），以满足一些特殊的需求，例如**对字节码进行加密来避免class文件被反编译，或者加载特殊目录下的字节码数据。**

#### 双亲委派机制

![image-20220216214354529](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258778.png)

**加载机制**：如果自己定义和`jdk`同名的类，运行时虚拟机会在系统的类加载器中寻找，再去扩展类加载器中寻找，再去根加载器中寻找，如果存在同名的类，会使用根加载器中的类，而不使用自己定义的类。

![image-20220216214754859](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258779.png)

**作用：1. 避免类的重复加载；2. 保护程序安全,防止核心的JAVA语言环境遭受破坏。**

### 初始化时数据区（RuntimeDataArea）

在程序的执行过程中，Java虚拟机定义了各种运行时数据区。其中一些运行时数据区创建于Java虚拟机启动，并且仅当虚拟机退出时销毁。而其他则是每个线程独有的，线程独有的数据区在线程创建时创建。线程退出时销毁。

- **线程共享数据区：堆区Heap、方法区Method Area【堆、堆外内存（永久代或元空间、代码缓存）】**
- **线程独占数据区：Java虚拟机栈(JVM Stack)、本地方法栈(Native Method Stack)、程序计数器/PC寄存器(Program Counter Register)。**

**简单图示**

![image-20220216220023602](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258780.png)

**详细图示**

![image-20220216221856134](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258781.png)

- **方法区**：方法区是被所有Java虚拟机线程所共享的，它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码缓存等。
- **堆区**：存储的全部是对象，每个对象都包含一个与之对应的class的信息。 (class的目的是得到操作指令) jvm只有一个堆区(heap)被所有线程共享，堆中不存放基本类型和对象引用，只存放对象本身。
- **程序计数器**：程序计数器是一块较小的内存空间，它的作用可以看作是当前线程所执行的字节码的行号指示器。在虚拟机的概念模型里字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成。
- **虚拟机栈**：Java虚拟机栈和程序计数器一样也是线程私有的，生命周期和线程相同；它是Java方法执行的线程内存模型。
- **本地方法栈**：本地方法栈（Native Method Stack）用于管理本地方法的调用。 本地方法栈，也是线程私有的。 如果线程请求分配的栈容量超过本地方法栈允许的最大容量，Java虚拟机将会抛出一个stackoverflowError 异常。

### 深刻理解执行引擎及各组成

#### 执行引擎

- **解释器**：将字节码文件中的内容“翻译”为对应平台的本地机器指令执行。
- **即时编译器**：是一个把Java的字节码（包括需要被解释的指令的程序）转换成可以直接发送给处理器的指令的程序。
- **垃圾回收器**：由Java 虚拟机（JVM）垃圾回收器提供的一种对内存回收的一种机制，它一般会在内存空闲或者内存占用过高的时候对那些没有任何引用的对象不定时地进行回收。

#### 代码的执行

![image-20220216220549004](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258782.png)

#### 整体流程

![image-20220216220800969](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202202162258783.png)

## JVM内存模型

### 方法区（Method area）

### 堆（Heap）

### Java虚拟机（Stack）

#### 理解栈帧的含义

#### 理解操作数栈存在的意义

#### 递归调用的栈溢出

### 程序计数器（Register count）

### 本地方法栈（Native method stack）

## 垃圾回收与垃圾回收器

### 理解共享内存分带设计

### 如何确定一个对象为垃圾对象

### 常见的垃圾回收算法

#### 标记-清除

#### 标记-整理

#### 标记-复制

### 历代的垃圾回收器

#### 串行单线程收集器

#### 并行多线程收集器

#### 并发多线程收集器

### 垃圾回收器的评判标准