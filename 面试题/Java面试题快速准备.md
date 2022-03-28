# Java面试题

## Java基础

### Java概述

#### Java语言有哪些特点？

* 面向对象（**封装、继承、多态**）
* 平台无关性（一次编译，到处运行），具有很好的可移植性，而保证这一点的是Java虚拟机机制。
* 可靠性、安全性
* 支持多线程
* 支持网络编程
* 编译与解释并存

#### Java和C++有什么关系，并且有什么区别？

* 都是面向对象的语言，都支持**封装、继承、多态**
* C++支持指针，而Java没有指针的概念。
* C++支持多继承，而Java**不支持多重继承，但运行一个类实现多个接口**
* Java自动进行无用内存回收操作，不再需要程序员进行手动删除好；而C++必须有程序释放内存资源，这就增加了程序员的负担。
* Jav**a不支持操作符重载**，操作符重载则被认为C++的突出特征。
* Java是完全面向对象的语言，并且**取消了C/C++中的结构和联合**，使编译程序更加简洁。
* goto是C/C++的遗物，Java不提供goto语句，但是指定goto作为关键字，但不支持它的使用，这使得程序更简洁易读。

#### JVM、JRE、JDK三者的关系

JDK（Java开发工具）：它拥有 JRE 所拥有的一切，还有 编译器（javac）和工具（如 javadoc 和 jdb）。它能够创建和编译程序。

JRE（Java运行环境）：它是运行已编译 Java 程序所需的所有内容的集合，包括 Java 虚拟机（JVM）以及Java 类库，java 命令和其他的一些基础构件。但是，它不能用于创建新程序。

JVM（Java虚拟机）：运行通过编译器（javac）编译的字节码文件。

#### 什么是字节码？采用字节码的好处是什么？

>  这个问题，面试官可以扩展提问，Java 是编译执行的语言，还是解释执行的语言？

Java之所以可以“一次运行，到处编译”，一是因为JVM针对各种操作系统、平台都进行了**定制**；二是因为无论在什么平台，都可以**编译生成固定格式的字节码**（.class文件）供JVM使用。因此，可以看出字节码对于Java生态的重要性。

之所以被称为字节码，是因为**字节码文件是由十六进制值组成**，而JVM以**两个十六进制值为一组**，即**以字节为单位**进行读取。在Java中使用javac命令编译源代码为字节码文件，一个.java文件从编译到运行的示例如图所示。

![image-20220228172653031](../../../../../Pictures/assets/Java面试题快速准备/202203011731866.png)

Java语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以Java程序运行时比较高效，而且，由于字节码并不专对一种特定的机器，因此，Java程序无须重新编译便可在多种不同的计算机上运行。

> Java需要编译才能有字节码文件，编译后无须再编译，但是不能直接运行的，需要解释运行在JVM上。

### Java基础语法

#### Java有哪些数据类型？

Java 语言的数据类型分为两种：基本数据类型和引用数据类型。

![image-20220303153106830](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202203031807666.png)

![image-20220303153217030](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202203031807667.png)

==引用数据类型建立在基本数据类型的基础上，包括数组、类和接口。引用数据类型是由用户自定义，用来限制其他数据的类型。另外，Java 语言中不支持 C++ 中的指针类型、结构类型、联合类型和枚举类型。Java引入了枚举类型，但是它是一个特殊的类==

#### switch是否能作用到byte上，是否能作用在long上，是否能在String上？

Java5 以前 switch(expr)中，expr 只能是 byte、short、char、int。 从 Java 5 开始，Java 中引入了枚举类型， expr 也可以是 enum 类型。
从 Java 7 开始，expr还可以是字符串(String)，但是长整型(long)在目前所有的版本中都是不可以的。

> **byte、short、int、char、enum、String**

#### break、continue、return的区别及作用？

- **break**： 跳出总上一层循环，不再执行循环(结束当前的循环体) 
- **continue**： 跳出本次循环，继续执行下次循环(结束正在执行的循环 进入下一个循环条件)
- **return**： 程序返回，不再执行下面的代码(结束当前的方法 直接返回)

#### 值传递和引用传递的区别是什么？为什么说java只有值传递？

**值传递**：指的是在方法调用时，传递的参数是按值的拷贝传递，**传递的是值的拷贝**，也就是说传递后就互不相关了。

**引用传递**：指的是在方法调用时，传递的参数是按引用进行传递，其实**传递的是引用的地址**，也就是变量所对应的内存空间的地址。传递的是值的引用，也就是说**传递前和传递后都指向同一个引用**（也就是同一个内存空间）。

**基本类型作为参数被传递时肯定是值传递；引用类型作为参数被传递时也是值传递，只不过“值”为对应的引用**。

#### ==和equals的区别？

**`==`对比的是栈中的值，基本数据类型是变量值，引用类型是堆中内存对象的地址。**

**`equals`：object中默认是采用`==`，但是通常会重写。**

注意：String类中的`equals`被重写了，用于比较两个字符串的内容。

```java
public class Test01 {
    public static void main(String[] args) {
        String str1 = "Hello";
        String str2 = new String("Hello");
        String str3 = str2;

        System.out.println(str1 == str2); // false
        System.out.println(str1 == str3); // false
        System.out.println(str2 == str3); // true


        System.out.println(str1.equals(str2)); // true
        System.out.println(str1.equals(str3)); // true
        System.out.println(str2.equals(str3)); // true
    }
}
```

#### hashCode()与equals的关系？

![](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202203012046493.png)

##### hashCode介绍

**hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。**hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode()函数。

散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到 了散列码！（可以快速找到所需要的对象）

##### 为什么 要有hashCode？

**以“HashSet 如何检查重复”为例子来说明为什么要有 hashCode：**

当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashcode 值作比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现。

但是如果发现有相同 hashcode 值的对象，这时会调用 equals()方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。

##### hashCode(),equals()两种方法是什么关系？

- **如果两个对象相等，调用equals方法则返回true，则hashcode一定是也是相同的。**
- **如果两个对象的hashcode值相同，它们也不一定相等。**
- **因此equals方被重写，则hashcode也必须重写。**
- hashcode()的默认行为是对堆上的对象产生独特值。如果没有重写hashcode()，则该class的两个对象无论如何都不会相等。（即使这两个对象指向相同对的数据）

#### 为什么重写equals方法必须重写hashcode方法？(前面两个的总结)

**判断的时候先根据hashcode进行的判断，相同的情况下再根据equals()方法进行判断。如果只重写了equals方法，而不重写hashcode的方法，会造成hashcode的值不同，而equals()方法判断出来的结果为true。**

在Java中的一些容器中，不允许有两个完全相同的对象，插入的时候，如果判断相同则会进行覆盖。这 时候如果只重写了equals()的方法，而不重写hashcode的方法，Object中hashcode是根据对象的存储地址转换而形成的一个哈希值。这时候就有可能因为没有重写hashcode方法，造成相同的对象散列到不同的位置而造成对象的不能覆盖的问题。**(比如hashset存储String类型，而String类型重写equals了，但是没有重写hashcode方法的话，会导致hashcode不等，从而不去调用equals进行判断，导致set里面有多个重复对象)**

####  `short s=2;s=s+1;` 会报错吗？`short s=2;s+=1;` 会报错吗？

答：s=s+1 会报错，s+=1 不会报错，因为 s=s+1 会导致 short 类型升级为 int 类型，所以会报错，而 s+=1 还是原来的 short 类型，所以不会报错。

####  `float f=3.4;` 会报错吗？为什么？

答：会报错，因为值 3.4 是 double 类型，float 类型级别小于 double 类型，所以会报错。

#### 3*0.1==0.3 返回值是多少？

答：返回值为：false。

题目解析：因为有些浮点数不能完全精确的表示出来，如下代码：

> System.out.println(3 * 0.1);

返回的结果是：0.30000000000000004。

#### 以下程序执行的结果是？

```java
final int iMax = Integer.MAX_VALUE;
System.out.println(iMax + 1);
```

这是因为整数在内存中使用的是补码的形式表示，最高位是符号位 0 表示正数，1 表示负数，当执行 +1 时，最高位就变成了 1，结果就成了 -2147483648。

```java
Set<Short> set = new HashSet<>();
for (short i = 0; i < 5; i++) {
    set.add(i);
    set.remove(i - 1);
}
System.out.println(set.size());
```

Short 类型 -1 之后转换成了 Int 类型，remove() 的时候在集合中找不到 Int 类型的数据，所以就没有删除任何元素，执行的结果就是 5。

### Java字符串

#### 以下可以正确获取字符串长度的是？

 `str.length()` 方法。

#### String、StringBuffer、StringBuilder的区别

* String是final修饰的，不可变，每次操作都会产生新的String对象
* StringBuffer和StringBuilder都是在原对象上操作
* **StringBuffer是线程安全的，StringBuilder线程是不安全的，因为StringBuffer方法是synchronized修饰的。**
* 性能：**StringBuilder > StringBuffer > String**

**使用场景：经常需要改变字符串内容时使用后两个（StringBuffer、StringBuilder），优先使用StringBuilder，如果是多线程使用共享变量时使用StringBuffer。**

#### String 对象的 intern() 有什么作用？

intern() 方法用于查找常量池中是否存在该字符值，如果常量池中不存在则现在常量池中创建，如果已经存在则直接返回。

```java
String s = "laowang";
String s2 = s.intern();
System.out.println(s == s2); // 返回 true
```

#### String为什么要设计成不可变得？

1. **便于实现字符串池（String Pool）**

在Java中，由于会大量的使用String常量，如果每一次声明一个String都创建一个String对象，那将会**造成极大的空间资源的浪费**。Java提出了String pool的概念，**在堆中开辟一块存储空间String pool**，当初始化一个String变量时，如果该字符串已经存在了，就不会去创建一个新的字符串变量，而是会返回已经存在了的字符串的**引用**。**（节省空间资源）**

如果字符串是可变的，某一个字符串变量改变了其值，那么其指向的变量的值也会改变，String pool将不能够实现！

2. **使多线程安全**

在并发场景下，多个线程同时读一个资源，是安全的，不会引发竞争，**但对资源进行写操作时是不安全的，不可变对象不能被写，所以保证了多线程的安全。**、

3. **避免安全问题**

在网络连接和数据库连接中字符串常常作为参数，例如，网络连接地址URL，文件路径path，反射机制所需要的String参数。其不可变性可以保证连接的安全性。**如果字符串是可变的，黑客就有可能改变字符串指向对象的值，那么会引起很严重的安全问题。**

4. **加快字符串处理速度**

由于String是不可变的，保证了hashcode的唯一性，于是**在创建对象时其hashcode就可以放心的缓存了，不需要重新计算**。这也就是Map喜欢将String作为Key的原因，处理速度要快过其它的键对象。所以HashMap中的键往往都使用String。

==总体来说，String不可变的原因要包括 设计考虑，效率优化，以及安全性这三大方面。==

#### 字符型常量和字符串常量的区别？

1. 形式上：字符常量是单引号引起的一个字符，字符串常量是双引号引起的若干个字符； 
2. 含义上：字符常量相当于一个整型值( ASCII 值)，**可以参加表达式运算**；字符串常量代表一个地址值 (该字符串在内存中存放位置，相当于对象）；
3. 占内存大小：字符常量只占**2个字节**；字符串常量占若干个字节(**至少一个字符结束标志**) (注意: char在Java中占两个字节)。

#### 什么是字符串常量池？

java中常量池的概念主要有三个：[**全局字符串常量池，class文件常量池，运行时常量池**](http://tangxman.github.io/2015/07/27/the-difference-of-java-string-pool/)。我们现在所说的就是**全局字符串常量池**。

**字符串常量池是存储在 Java 堆内存中的字符串池，是为防止每次新建字符串带的时间和空间消耗的一种解决方案。**在创建字符串时 JVM 会首先检查字符串常量池，如果字符串已经存在池中，就返回池中的实例引用，如果字符串不在池中，就会实例化一个字符串放到池中并把当前引用指向该字符串

#### [String str=“aaa”与String str=new String(“aaa”)是一样的吗？new String(“aaa”)创建了几个字符串对象？](https://blog.csdn.net/Theshy08/article/details/108890029)

- 使用String a = “aaa” ; ，程序运行时会在常量池中查找”aaa”字符串，若没有，会将”aaa”字符 串放进常量池，再将其地址赋给a；若有，将找到的”aaa”字符串的地址赋给a。 
- 使用String b = new String("aaa");`，程序会在堆内存中开辟一片新空间存放新对象，同时会 将”aaa”字符串放入常量池，相当于创建了两个对象，无论常量池中有没有”aaa”字符串，程序都会在堆内存中开辟一片新空间存放新对象。

```java
String a = new String("aaa"); //创建了两个对象，一个在堆中的StringObject对象，一个是在堆中的“aaa”对象，并在常量池中保存“aaa”对象的引用地址。
a.intern();       // 在常量池中寻找与a变量内容相同的对象，发现已经存在内容相同对象“aaa”，返回对象“aaa”的引用地址。

String b = "aaa"; //使用字面量创建，在常量池寻找是否有相同内容的对象，发现有，返回对象“aaa”的引用地址

System.out.println(a == b);//上面可以分析出，s变量和s2变量地址指向的是不同的对象，所以返回false


String c = new String("aa") + new String("aa"); // 创建了两个对象，一个在堆中的StringObject 对象，一个是在堆中的“aa”对象，并在常量池中保存“aa”对象的引用地址。
c.intern(); // 在常量池中寻找与s3变量内容相同的对象，没有发现“aaaa”对象，将s3对应的StringObject对象的地址保存到常量池中，返回StringObject对象的地址。

String d = "aaaa"; //使用字面量创建，在常量池寻找是否有相同内容的对象，发现有，返回其地址， 也就是StringObject对象的引用地址。
System.out.println(c == d);//从上面可以分析出，s3变量和s4变量地址指向的是相同的对象，所 以返回true。

String str1 = new String("123");
String str2 = new String("123");
System.out.println(str1 == str2);//返回false，比较的是StringObject对象的引用地址
```

intern的处理是先判断字符串常量是否在字符串常量池中，如果存在直接返回该常量，如果没有找到，说明该字符串常量在堆中，则处理是把堆区该对象的引用加入到字符串常量池中，以后别人拿到的是该字符串常量的引用，实际存在堆中。

#### String是最基本的数据类型吗？

不是。Java 中的基本数据类型只有 8 个 ：byte、short、int、long、float、double、char、boolean； 除了基本类型（primitive type），剩下的都是引用类型（referencetype），Java 5 以后引入的枚举类型也算是一种比较特殊的引用类型。

#### String有哪些特性？

- **不变性**：String 是只读字符串，是一个典型的 immutable 对象，对它进行任何操作，其实都是创 建一个新的对象，再把引用指向该对象。不变模式的主要作用在于当一个对象需要被多线程共享并 频繁访问时，可以保证数据的一致性； 
- **常量池优化**：String 对象创建之后，会在字符串常量池中进行缓存，如果下次创建同样的对象时， 会直接返回缓存的引用；（节省空间）
- **final**：使用 final 来定义 String 类，表示 String 类不能被继承，提高了系统的安全性。（防止篡改内容）

#### 在使用HashMap的时候，用String做key有什么好处？

HashMap 内部实现是通过 key 的 hashcode来确定value的存储位置，**因为字符串是不可变的，所以当创建字符串时，它的 hashcode 被缓存下来，不需要再次计算，所以相比于其他对象更快。**

#### 以下字符串对比的结果是什么？

```java
String s1 = "hi," + "lao" + "wang";
String s2 = "hi,";
s2 += "lao";
s2 += "wang";
String s3 = "hi,laowang";
System.out.println(s1 == s2);
System.out.println(s1 == s3);
System.out.println(s2 == s3);
```

答：false true false

题目解析：String s1 = "hi," + "lao" + "wang" 代码会被 JVM 优化为：String s1 = "hi,laowang"，这样就和 s3 完全相同，s1 创建的时候会把字符"hi,laowang"放入常量池，s3 创建的时候，常量池中已经存在对应的缓存，会直接把引用返回给 s3，所以 `s1==s3` 就为 true，而 s2 使用了 `+=` 其引用地址就和其他两个不同。

#### 以下 String 传值修改后执行的结果是什么？以下 StringBuffer 传值修改后的执行结果是什么？

```java
public static void main(String[] args) {
  String str = new String("laowang");
  change(str);
  System.out.println(str); //laowang
}
public static void change(String str) {
    str = "xiaowang";
}


public static void main(String[] args) {
  StringBuffer sf = new StringBuffer("hi,");
  changeSf(sf);
  System.out.println(sf); //hi,laowang
}
public static void changeSf(StringBuffer sf){
    sf.append("laowang");
}
```

String 为不可变类型，在方法内对 String 修改的时候，相当修改传递过来的是一个 String 副本，所以 String 本身的值是不会被修改的，而 StringBuffer 为可变类型，参数传递过来的是对象的引用，对其修改它本身就会发生改变。

### **Java包装类型**

#### 为什么需要包装类？

答：需要包装类的原因有两个。

① Java 的设计思想是万物既对象，包装类体现了面向对象的设计理念；

② 包装类包含了很多属性和方法，比基础数据类型功能多，比如提供的获取哈希值（hashCode）或获取类（getClass）的方法等。

#### 包装类的值比较要使用什么方法？

答：包装类因为有高频区域数据缓存，所以推荐使用 equals() 方法进行值比较。

#### 包装类有哪些功能？

答：包装类提供的功能有以下几个。

- 功能丰富：包装类包含了有 hashCode、getClass 、max、min 等方法；
- 可定义泛型类型参数：例如 `List<Integer> list = new ArrayList<>();` ;
- 序列化：包装类实现了 Serializable 接口，所以包装类天然支持序列化和反序列化；
- 类型转换：包装类提供了方便的类型转换方法，比如 Integer 的 parseInt() 方法；
- 高频区域数据缓存：高频区域可使用已有的缓存对象。

#### 选择包装类还是基础类的原则有哪些？

答：我们知道正确的使用包装类，可以提供程序的执行效率，可以使用已有的缓存，一般情况下选择基本数据类型还是包装类原则有以下几个。

① 所有 POJO 类属性必须使用包装类； ② RPC 方法返回值和参数必须使用包装类； ③ 所有局部变量推荐使用基础数据类型。

#### 包装类型是什么？基本类型和包装类型有什么区别？

Java 为每一个基本数据类型都引入了对应的包装类型（wrapper class），int 的包装类就是 Integer， 从 Java 5 开始引入了自动装箱/拆箱机制，把基本类型转换成包装类型的过程叫做装箱（boxing）；反之，把包装类型转换成基本类型的过程叫做拆箱（unboxing），使得二者可以相互转换。

Java 为每个原始类型提供了包装类型： 

- **原始类型：boolean，char，       byte，short， int，      long， float，double**
- **包装类型：Boolean，Character，Byte，Short，Integer，Long，Float，Double**

基本类型和包装类型的区别主要有以下几点：

- **包装类型可以为 null， 而基本类型不可以。**它使得包装类型可以应用于 POJO 中，而基本类型则不行。那为什么 POJO 的属性必须要用包装类型呢？《阿里巴巴 Java 开发手册》上有详细的说明， 数据库的查询结果可能是 null，如果使用基本类型的话，因为要自动拆箱（将包装类型转为基 本类型，比如说把 Integer 对象转换成 int 值），就会抛出 NullPointerException 的异常。 
- **包装类型可用于泛型， 而基本类型不可以。**泛型不能使用基本类型，因为使用基本类型时会编译出错。

```java
List<int> list = new ArrayList<>();// 提示 Syntax error, insert "Dimensions" to complete ReferenceType
List<Integer> list = new ArrayList<>();
```

**因为泛型在编译时会进行类型擦除，最后只保留原始类型，而原始类型只能是Object类及其子类 ——基本类型是个特例。**

* **基本类型比包装类型更高效。**==基本类型在栈中直接存储的具体数值，而包装类型则存储的是堆中的引用。== 很显然，相比较于基本类型而言，包装类型需要占用更多的内存空间。

#### 包装类型的常量池技术了解么？

Java 基本类型的包装类的大部分都实现了常量池技术。

`Byte`,`Short`,`Integer`,`Long` 这 4 种包装类默认创建了数值 **[-128，127]** 的相应类型的缓存数据，`Character` 创建了数值在 **[0,127]** 范围的缓存数据，`Boolean` 直接返回 `True` or `False`。

==如果超出对应范围仍然会去创建新的对象，缓存的范围区间的大小只是在性能和资源之间的权衡。==

==两种浮点数类型的包装类 `Float`,`Double` 并没有实现常量池技术。==

记住：**所有整型包装类对象之间值的比较，全部使用 equals 方法比较**。

```java
Integer i1 = 33;
Integer i2 = 33;
System.out.println(i1 == i2);// 输出 true

Float i11 = 333f;
Float i22 = 333f;
System.out.println(i11 == i22);// 输出 false

Double i3 = 1.2; //由于没有常量池，就相当于创建了两个对象，只是值一样，因此采用equals判断
Double i4 = 1.2;
System.out.println(i3 == i4);// 输出 false


Integer i1 = 40; //采用了常量池，缓存了默认范围的数据，因此，当前数据时从常量池中获取的
Integer i2 = new Integer(40); // 是一个对象，从堆中获得新的地址
System.out.println(i1==i2); // false
```

#### 解释一下自动装箱和自动拆箱？

**自动装箱： 将基本数据类型重新转化为对象**

```java
public class Test {
    public static void main(String[] args) { 
        // 声明一个Integer对象，用到了自动的装箱：解析为:Integer num = Integer.valueOf(9); 
        Integer num = 9;
    }
}
```

从字节码中，我们发现装箱其实就是调用了包装类的`valueOf()`方法，拆箱其实就是调用了`xxxValue()`方法。

因此，

- `Integer i = 10` 等价于 `Integer i = Integer.valueOf(10)`
- `int n = i` 等价于 `int n = i.intValue()`;

**自动拆箱： 将对象重新转化为基本数据类型**

```java
public class Test { 
    public static void main(String[] args) { 
        //声明一个Integer对象
        Integer num = 9;
        // 进行计算时隐含的有自动拆箱 
        System.out.print(num--);
    }
}
```

因为对象时不能直接进行运算的， 而是要转化为基本数据类型后才能进行加减乘除。

> **如果频繁拆装箱的话，也会严重影响系统的性能。我们应该尽量避免不必要的拆装箱操作。**

#### Integer和int有什么区别？

- Integer是int的包装类；int是基本数据类型；（类型不同）
- Integer变量必须实例化后才能使用；int变量不需要；（类需要实例化）
- Integer实际是对象的引用，指向此new的Integer对象；int是直接存储数据值 ；（存储值不同）
- Integer的默认值是null；int的默认值是0。(默认值不同)

#### 两个new生成的Integer变量的对比

由于Integer变量实际上是对一个Integer对象的引用，所以两个通过new生成的Integer变量永远是不相等的（因为new生成的是两个对象，其内存地址不同）。（这是类，每次创建的对象，都是新的地址）

```java
Integer i = new Integer(10000); 
Integer j = new Integer(10000);
System.out.print(i == j); //false
```

#### Integer变量和int变量的对比

Integer变量和int变量比较时，只要两个变量的值是向等的，则结果为true**（因为包装类Integer和基本 数据类型int比较时，java会自动拆包装为int，然后进行比较，实际上就变为两个int变量的比较）**

```java
int a = 10000; 
Integer b = new Integer(10000);
Integer c=10000;

System.out.println(a == b); //true
System.out.println(a == c); //true
```

#### 非new生成的Integer变量和new Integer()生成变量的对比

非new生成的Integer变量和new Integer()生成的变量比较时，结果为false。**（因为非new生成的Integer变量指向的是java常量池中的对象，而new Integer()生成的变量指向堆中新建的对象，两者在内存中的地址不同）**

```java
Integer b = new Integer(10000);
Integer c = 10000;
System.out.println(b == c);//false
```

#### 两个非new生成的Integer对象对比

对于两个非new生成的Integer对象，进行比较时，如果两个变量的值在区间-128到127之间，则比较结果为true，如果两个变量的值不在此区间，则比较结果为false。

```java
Integer a = 40;
Integer b = 40; //<=>Integer.valueOf(40)
System.out.println(a == b);//true

Integer a = 10000;
Integer b = 10000;
System.out.println(a == b);//false
```

当值在 -128 ~ 127之间时，java会进行自动装箱，然后会对值进行缓存，如果下次再有相同的值，会直 接在缓存中取出使用。缓存是通过Integer的内部类**IntegerCache**来完成的。当值超出此范围，会在堆中new出一个对象来存储。

### Java面向对象

#### 什么是面向对象？

> 面向对象和面向过程的区别？

**对比面向过程，是两种不同的处理问题的角度。**

**面向过程：更注重事情的每一个步骤以及顺序**。比如洗衣服，会一步一步按照步骤做下去

**面向对象：更注重事情有哪些参与者（对象），及各自需要做什么**。比如会将洗衣机和人拆开分为两个对象。每个对象负责做自己的事情。

因此，可以知道，**面向过程比较直接高效，而面向对象更易于复用、扩展和维护。**

#### 三大特性

**封装：封装的意义，在于明确标识出允许外部使用的所有成员函数和数据项。内部细节对外部调用透明，外部调用无需修改或关心内部实现。**比如，orm框架进行操作数据库，无需知道内部如何实现，只需引入mybatis，进行调用方法即可。

**继承：继承基类的方法，并做出自己的改变或者扩展。子类共性的方法或者属性可以直接使用父类而不需要自己重新定义，只需扩展自己的需要的。**

**多态：**基于对象**所属类的不同**，外部对同一个方法的调用，实际执行的逻辑不同。多态有三个条件（继承、重写、父类引用指向子类对象（向上转型））

> `Father f1 = new Son()`向上转型：只会保存父子类共有方法。移除子类特有方法。
>
> `Son s1 = (Son)f1`向下转型：在你向上转型时丢失的子类扩展方法，通过向下转型能找回来

####  创建一个对象用什么运算符？对象实体与对象引用有何不同？

new 运算符，new创建对象实例（**对象实例在堆内存中**），对象引用**指向**对象实例（**对象引用存放在栈内存中**）。

一个对象引用可以指向 0 个或 1 个对象（一根绳子可以不系气球，也可以系一个气球）;一个对象可以有 n 个引用指向它（可以用 n 条绳子系住一个气球）。

#### 对象的相等与指向他们的引用相等，两者有什么不同？

- 对象的相等一般比较的是内存中存放的内容是否相等。
- 引用相等一般比较的是他们指向的内存地址是否相等。

#### 一个类的构造方法的作用是什么？

构造方法是一种特殊的方法，主要作用是完成对象的初始化工作。

#### **Java语言是如何实现多态的？**

本质上多态分为两种：

1. 编译时多态（静态多态）
2. 运行时多态（动态动态）

重载就是编译时多态的一个例子，编译时多态在编译时就已经确定，运行的时候调用的是确定的方法。

**我们通常所说的多态都是指的运行时多态，也就是编译时不确定究竟调用那个具体方法，一直延迟到运行时才能确定。**（这也是为什么有时候多态方法又被称为延迟方法的原因）

**Java实现多态有3个必要条件：继承、重写和向上转型。**只有满足这3个条件，开发人员才能够在同一个继承结构中使用统一的逻辑实现代码处理不同的对象，从而执行不同的行为。

* 继承：在多态中必须存在有继承关系的子类和父类。
* 重写：子类和父类中某些方法进行重新定义，在调用这些方法时就会调用子类的方法。
* 向上转型：在多态中需要将子类的引用赋给父类对象，只有这样该引用才能调用父类的方法，又能调用子类的方法。

#### 重载(overload)与重写(override)的区别是什么？

**重载**：发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同，发生在编译时。（不能根据返回类型进行区分，编译时的多态性）（多个方法）

```java
public int add(int a, int b){ // 编译时就会报错，因为不能根据返回值类型进行区分
    return  a+b;
}
public String add(int a, int b){
    return  a+b+"";
}

// 编译错误
```

**重写**：发生在父子类中，方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类；如果父类方法访问修饰符为private则子类就不能重写方法 。（运行时的多态性）（重新写方法）

#### 重载的方法能否根据返回值类型进行区分？

不能根据返回值类型来区分重载的方法。因为调用时不指定类型信息，编译器不知道你要调用哪个函数。

```java
public int add(int a, int b){ // 编译时就会报错，因为不能根据返回值类型进行区分
    return  a+b;
}
public String add(int a, int b){
    return  a+b+"";
}

// 编译错误
```

#### 构造器(constructor)是否可以被重写(override)？

**构造器不能被继承，因此不能被重写，但可以被重载**。每一个类必须有自己的构造函数，负责构造自己这部分的构造。子类不会覆盖父类的构造函数，相反必须一开始调用父类的构造函数。

```java
class Animal{
    private String name;
    private String action;

    //所谓重载就是多个方法名一样，其他的不同（除了返回类型以外）
    public Animal(String name){}
    public Animal(String name,String action){}
}
```

#### **接口和抽象类的区别？**

**共同点** ：

- 都不能被实例化。 
- 都可以包含抽象方法。
- 都可以有默认实现的方法（Java 8 可以用 `default` 关键在接口中定义默认方法）。

**区别**：

* **抽象类可以存在普通成员函数，而接口中只能存在public abstract方法。**
* **抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final（静态常量）类型的。**
* **抽象类只能继承一个，接口可以实现多个。**

**接口的设计目的，是对类的行为进行约束**(更准确的说是一种“有”约束，因为接口不能规定类不可以有什么行为)，也就是提供一种机制，可以强制要求不同的类具有相同的行为。**它只约束了行为的有无，但不对如何实现行为进行限制。|**

**而抽象类的设计目的，是代码复用。**当不同的类具有某些相同的行为(记为行为集合A)，且其中一部分行为的实现方式一致时(A的非真子集，记为B)，可以让这些类都派生于一个抽象类。在这个抽象类中实现了B，避免让所有的子类来实现B，这就达到了代码复用的目的。而A减B的部分，留给各个子类自己实现。正是因为A-B在这里没有实现，所以抽象类不允许实例化出来(否则当调用到A-B时，无法执行)。

**抽象类是对类本质的抽象，表达的是is a（是什么）的关系**，比如: BMw is a car。**抽象类包含并实现子类的通用特性，将子类存在差异化的特性进行抽象。交由子类去实现。**

**而接口是对行为的抽象，表达的是like a（像什么）的关系。**比如: Bird like a Aircraft(像飞行器一样可以飞)，但其本质上 is a Bird。**接口的核心是定义行为，即实现类可以做什么，至于实现类主体是谁、是如何实现的，接口并不关心。**

**使用场景：当你关注一个事物的本质的时候，用抽象类；当你关注一个操作的时候，用接口。**

==抽象类的功能要远超过接口，但是，定义抽象类的代价高。因为高级语言来说（从实际设计上来说也是)每个类只能继承一个类。在这个类中，你必须继承或编写出其所有子类的所有共性。虽然接口在功能上会弱化许多，但是它只是针对一个动作的描述。而且你可以在一个类中同时实现多个接口。在设计阶段会降低难度。==（理解）

#### 简述final的作用

final用于修饰变量、方法和类。 

- final 变量：表示变量一旦被赋值就不可以更改它的值。
- final 方法：表示方法不可被子类覆盖，但是可以重载。
- final 类：表示类不可被继承。

> 注意在使用局部变量的时候，可以不初始化，但是在使用的时候，一定要赋值，但是第二次赋值就不可以了。
>
> 修饰引用类型数据的时候，是引用值不能改变，但是内部的值是可以改变的。

#### 为什么局部内部类和匿内部类只能访问局部final变量？

**用final修饰实际上就是为了保护数据的一致性。**因此外部类和内部类是同级的，可能会出现外部类死亡，而内部类存活仍然在运行，直到没有被引用时才会消亡。此时就会出现一种情况，就是内部类可能会访问一个不存在的局部变量。

#### 抽象类能使用final修饰吗？

不能，定义抽象类就是让其他类继承的，如果定义为 final 该类就不能被继承，这样彼此就会产生矛盾， 所以 final 不能修饰抽象类

#### 访问修饰符public、private、protected，以及默认时的区别？

Java中，可以使用访问控制符来保护对类、变量、方法和构造方法的访问。

Java 支持 4 种不同的访问权限。

default (即默认，什么也不写）: 在同一包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法。

private : 在同一类内可见。使用对象：变量、方法。注意： 不能修饰类（外部类） 

public : 对所有类可见。使用对象：类、接口、变量、方法

protected : 对同一包内的类和所有子类可见。使用对象：变量、方法。注意： 不能修饰类（外
部类） 。

![image-20220303154157666](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202203031807668.png)

#### 为什么要用static关键字？

**通常来说，用new创建类的对象时，数据存储空间才被分配，方法才供外界调用。**但有时我们只想为特定域分配单一存储空间，不考虑要创建多少对象或者说根本就不创建任何对象，再就是我们**想在没有创建对象的情况下也想调用方法。**在这两种情况下，static关键字，满足了我们的需求。

#### **“static”关键字是什么意思？Java是否可以覆盖(override)一个private或者static方法？**

“static”关键字表明一个成员变量或者是成员方法可以在没有所属的类的实例变量的情况下被访问。(就是类没有被初始化，也可以被使用)

**Java中static方法不能被覆盖，因为方法覆盖是基于运行时动态绑定的，而static方法是编译时静态绑定的。static方法跟类的任何实例都不相关，所以概念上不适用。**

#### 是否可以在static环境访问非static变量？

不可以。static变量在Java中是属于类的，它在所有的实例中的值是一样的。**当类被Java虚拟机载入的时候，会对static变量进行初始化。如果你的代码尝试不用实例来访问非static的变量，编译器会报错，因为这些变量还没有被创建出来，还没有跟任何实例关联上。**

#### static静态方法能不能引用非静态资源？

不可以。只有new一个对象的时候才会产生的东西，对于初始化后就存在的静态资源来说，根本不认识它。

#### static静态方法里面能不能引用静态资源？

可以。因为都是类初始化的时候加载的，大家相互都认识。

#### 非静态方法里面能不能引用非静态资源？

可以。非静态方法就是实例方法，那是new之后才产生的，那么属于类的内容它都认识。

#### java静态变量、代码块和静态方法的执行顺序是什么？

基本上代码块分为三种：Static静态代码块、构造代码块、普通代码块。

代码块执行顺序：**Static静态代码块——>构造代码块——>普通代码块。**

继承中代码块执行顺序：**父类静态块——>子类静态块——>父类构造器—>子类构造器——>子类代码块/父类代码块**

#### java创建对象有哪几种方式？

java中提供了以下四种创建对象的方式：

- new创建新对象
- 通过反射机制
- 采用clone机制
- 通过序列化机制

前两者都需要显式地调用构造方法；对于clone机制，需要注意浅拷贝和深拷贝的区别；对于序列化机制需要明确其实现原理，在java中序列化可以通过实现Externalizable或者Serializable来实现。

#### 什么是不可变对象？好处是什么？

不可变对象指对象一旦被创建，状态就不能再改变，任何修改都会创建一个新的对象,如String、Integer及其它包装类。**不可变对象最大的好处是线程安全。**

#### 能否创建一个包含可变对象的不可变对象？

当然可以，比如``final Person[] persons = new Persion[]{} ``。persons是不可变对象的引用，但其数组中的Person实例却是可变的。这种情况下需要特别谨慎,不要共享可变对象的引用.这种情况下，如果数据需要变化时,就返回原对象的一个拷贝。

#### 深拷贝和浅拷贝区别了解吗？什么是引用拷贝？

关于深拷贝和浅拷贝区别，我这里先给结论：

- **浅拷贝**：浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点），不过，如果原对象内部的属性是引用类型的话，浅拷贝会直接复制内部对象的引用地址，也就是说**拷贝对象和原对象共用同一个内部对象。**
- **深拷贝** ：深拷贝会**完全复制**整个对象，包括这个对象所包含的内部对象。

### **Java反射**

#### 什么是反射

Reflection (反射)是Java被视为动态语言的关键，反射机制允许程序在执行期借助于Reflection API取得任何类的内部信息，并能**直接操作任意对象的内部属性及方法**。

#### 反射机制的优缺点有哪些？

- 优点：可以实现动态创建对象和编译，让代码更加灵活，为各种框架提供开箱即用的功能提供了便利

- 缺点：使用反射**性能较低**，需要解析字节码，将内存中的对象进行解析。其解决方案是：通过 setAccessible(true)关闭JDK的安全检查来提升反射速度。

  ​		让我们在运行时有了分析操作类的能力，这同样也**增加了安全问题**。比如**可以无视泛型参数的安全检查**（泛型参数的安全检查发生在编译时）。

#### 如果获取反射中的Class对象？

* 通过`Class.forName("类路径")`
* 通过``类名.class``
* 通过`对象名.class`
* 如果是包装类，可以通过TYPE获取`Integer.TYPE`

```java
People people = new People();
Class c1 = Class.forName("view.People");
Class c2 = People.class;
Class c3 = people.getClass();
Class c4 = Integer.TYPE;
```

#### Java反射API有几类？

| API                             | 描述             |
| ------------------------------- | ---------------- |
| `java.lang.Class`               | 代表一个类       |
| `java.lang.reflect.Method`      | 代表类的方法     |
| `java.lang.reflect.Field`       | 代表类的成员变量 |
| `java.lang.reflect.Constructor` | 代表类的构造器   |

#### 反射使用的步骤

1. 获取想要操作的类的Class对象，这是反射的核心，通过Class对象我们可以任意调用类的方法。 
2. 调用 Class 类中的方法，既就是反射的使用阶段。
3. 使用反射 API 来操作这些信息。

```java
public class Test7 {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        People people = new People();
        Class c1 = Class.forName("view.People");
//        Class c2 = People.class;
//        Class c3 = people.getClass();
//        Class c4 = Integer.TYPE;

        Constructor constructor = c1.getConstructor();
//        Object object = c1.getDeclaredConstructor().newInstance();
        Object object = constructor.newInstance();

        Field age = c1.getDeclaredField("age");
        age.setAccessible(true);
        age.set(object,18);

        Method getAge = c1.getMethod("getAge");

        // invoke调用方法
        System.out.println(getAge.invoke(object));

    }
}

class People{
    private int age;

    public People(){

    }

    public People(int age){
        this.age = age;
    }

    private void setAge(int age){
        this.age = age;
    }

    public int getAge(){
        return this.age;
    }
}
```

#### 为什么引入反射概念？反射机制的应用有哪些？

反射主要应用在以下几方面：

- 反射让开发人员可以通过外部类的全路径名创建对象，并使用这些类，实现一些扩展的功能。

- 反射让开发人员可以枚举出类的全部成员，包括构造函数、属性、方法。以帮助开发者写出正确的代码。

- 测试时可以利用反射 API 访问类的私有成员，以保证测试代码覆盖率。

Oracle 希望开发者将反射作为一个工具，用来帮助程序员实现本不可能实现的功能。

#### 反射机制的原理是什么？

1. 反射获取类实例 Class.forName()，并没有将实现留给了java,而是交给了jvm去加载！主要是先获 取 ClassLoader, 然后调用 native 方法，获取信息，加载类则是回调 java.lang.ClassLoader。最后，jvm又会回调 ClassLoader 进类加载！

2. newInstance() 主要做了三件事： 

  * 权限检测，如果不通过直接抛出异常；

  * 查找无参构造器，并将其缓存起来；

  * 调用具体方法的无参构造方法，生成实例并返回。

3. 获取Method对象，

![image-20220304161647019](../../../../../Pictures/assets/Java面试题快速准备/image-20220304161647019.png)

上面的Class对象是在加载类时由JVM构造的，JVM为每个类管理一个独一无二的Class对象，这份Class 对象里维护着该类的所有Method，Field，Constructor的cache，这份cache也可以被称作根对象。

每次getMethod获取到的Method对象都持有对根对象的引用，因为一些重量级的Method的成员变量 （主要是MethodAccessor），我们不希望每次创建Method对象都要重新初始化，于是所有代表同一个 方法的Method对象都共享着根对象的MethodAccessor，每一次创建都会调用根对象的copy方法复制一份。

4. 调用invoke()方法。调用invoke方法的流程如下：

![image-20220304161822242](../../../../../Pictures/assets/Java面试题快速准备/image-20220304161822242.png)

调用Method.invoke之后，会直接去调MethodAccessor.invoke。MethodAccessor就是上面提到的所 有同名method共享的一个实例，由ReflectionFactory创建。

创建机制采用了一种名为inflation的方式（JDK1.4之后）：如果该方法的累计调用次数<=15，会创建出 NativeMethodAccessorImpl，它的实现就是直接调用native方法实现反射；如果该方法的累计调用次 数>15，会由java代码创建出字节码组装而成的MethodAccessorImpl。（是否采用inflation和15这个数字都可以在jvm参数中调整）。

### Java注解

`Annontation` （注解） 是Java5 开始引入的新特性，可以看作是一种特殊的注释，主要用于修饰类、方法或者变量。

注解本质是一个继承了`Annotation` 的特殊接口：

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {

}

public interface Override extends Annotation{
    
}
```

注解只有被解析之后才会生效，常见的解析方法有两种：

- **编译期直接扫描** ：编译器在编译 Java 代码的时候扫描对应的注解并处理，比如某个方法使用`@Override` 注解，编译器在编译的时候就会检测当前的方法是否重写了父类对应的方法。
- **运行期通过反射处理** ：像框架中自带的注解(比如 Spring 框架的 `@Value` 、`@Component`)都是通过反射来进行处理的。

> **元注解的作用就是负责注解其他注解**，Java定义了4个标准的meta-annotation类型，他们被用来提供对其他annotation类型作说明.
>
> 这些类型和它们所支持的类在``java.lang.annotation``包中可以找到。``( @Target , @Retention，@Documented , @Inherited )``
>
> - `@Target` ：用于描述注解的使用范围(即:被描述的注解可以用在什么地方)
> - `@Retention`：表示需要在什么级别保存该注释信息，用于描述注解的生命周期
>   - (SOURCE < CLASS < **RUNTIME**)
> - `@Document`：说明该注解将被包含在javadoc中
> - `@Inherited`：说明子类可以继承父类中的该注解

JDK 提供了很多内置的注解（比如 `@Override` 、`@Deprecated`），同时，我们还可以自定义注解。

### Java泛型

#### Java 泛型了解么？什么是类型擦除？介绍一下常用的通配符？

**Java 泛型（generics）** 是 JDK 5 中引入的一个新特性, 泛型提供了**编译时类型安全检测机制**，该机制允许程序员在编译时检测到非法的类型。泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。

Java 的泛型是伪泛型，这是因为 Java 在运行期间，所有的泛型信息都会被擦掉，这也就是通常所说类型擦除 。

**泛型一般有三种使用方式: 泛型类、泛型接口、泛型方法。**（类、接口、方法）

####  常用的通配符有哪些？

**常用的通配符为： T，E，K，V，？**

- ？ 表示不确定的 Java 类型
- T (type) 表示具体的一个 Java 类型
- K V (key value) 分别代表 Java 键值中的 Key Value
- E (element) 代表 Element

#### `List<Object>` 和 `List<?>` 有什么区别？

答：`List<?>` 可以容纳任意类型，只不过 `List<?>` 被赋值之后，就不允许添加和修改操作了；而 `List<Object>` 和 `List<?>` 不同的是它在赋值之后，可以进行添加和修改操作。

#### 可以把 `List<String>` 赋值给 `List<Object>` 吗？

答：不可以，编译器会报错。因此泛型严格规定了，集合中该装什么类型的对象，那么就不能装另一个类型的对象，这里没有继承关系。

```java
public class Test8 {
    public static void main(String[] args) {
        List<Son> list1 = new ArrayList<>();
        list1.add(new Son("111"));

        List<Father> list2 = new ArrayList<>();
//        list2 = list1; //报错


        System.out.println(list1.size());
        System.out.println(list2.size());
    }
}

class Father{
    private String name;
    public Father(){}
    public Father(String name){this.name = name;}
}
class Son extends Father{
    private String name;
    public Son(String name) {
        this.name = name;
    }
}
```



#### `List` 和 `List<Object>` 的区别是什么？

答： `List` 和 `List<Object>` 都能存储任意类型的数据，但 `List` 和 `List<Object>` 的唯一区别就是，`List` **不会触发编译器的类型安全检查**，比如把 `List<String>` 赋值给 `List` 是没有任何问题的，但赋值给 `List<Object>` 就不行。

> 泛型的优点：安全性、避免类型转换、提高了代码的可读性。泛型的本质是类型参数化，但编译之后会执行类型擦除，这样就可以和 Java 5 之前的二进制类库进行兼容。
>
> 使用迭代器的好处是不用关注容器的内部细节，用同样的方式遍历不同类型的容器。

### Java IO

#### NIO 介绍

上面讲的内容都是 java.io 包下的知识点，但随着 Java 的不断发展，在 Java 1.4 时新的 IO 包出现了 java.nio，NIO（Non-Blocking IO）的出现解决了传统 IO，也就是我们经常说的 BIO（Blocking IO）同步阻塞的问题，NIO 提供了 Channel、Selector 和 Buffer 等概念，可以实现多路复用和同步非阻塞 IO 操作，从而大大提升了 IO 操作的性能。 前面提到同步和阻塞的问题，那下面来看看同步和阻塞结合都有哪些含义。

| 组合方式   | 性能分析                                                     |
| :--------- | :----------------------------------------------------------- |
| 同步阻塞   | 最常用的一种用法，使用也是最简单的，但是 I/O 性能一般很差，CPU 大部分在空闲状态 |
| 同步非阻塞 | 提升 I/O 性能的常用手段，就是将 I/O 的阻塞改成非阻塞方式，尤其在网络 I/O 是长连接，同时传输数据也不是很多的情况下，提升性能非常有效。 这种方式通常能提升 I/O 性能，但是会增加 CPU 消耗，要考虑增加的 I/O 性能能不能补偿 CPU 的消耗，也就是系统的瓶颈是在 I/O 还是在 CPU 上 |
| 异步阻塞   | 这种方式在分布式数据库中经常用到。例如，在往一个分布式数据库中写一条记录，通常会有一份是同步阻塞的记录，而还有两至三份是备份记录会写到其他机器上，这些备份记录通常都是采用异步阻塞的方式写 I/O；异步阻塞对网络 I/O 能够提升效率，尤其像上面这种同时写多份相同数据的情况 |
| 异步非阻塞 | 这种组合方式用起来比较复杂，只有在一些非常复杂的分布式情况下使用，像集群之间的消息同步机制一般用这种 I/O 组合方式。例如，Cassandra 的 Gossip 通信机制就是采用异步非阻塞的方式。它适合同时要传多份相同的数据到集群中不同的机器，同时数据的传输量虽然不大，但是却非常频繁。这种网络 I/O 用这个方式性能能达到最高 |

多路指的是多个通道（SocketChannel），而复用指的是一个服务器端连接重复被不同的客户端使用。

#### AIO 介绍

AIO（Asynchronous IO）是 NIO 的升级，也叫 NIO2，实现了异步非堵塞 IO ，异步 IO 的操作基于事件和回调机制。

#### BIO、NIO、AIO 的区别是什么？

答：它们三者的区别如下。

- BIO 就是传统的 java.io 包，它是基于流模型实现的，交互的方式是同步、阻塞方式，也就是说在读入输入流或者输出流时，在读写动作完成之前，线程会一直阻塞在那里，它们之间的调用是可靠的线性顺序。它的优点就是代码比较简单、直观；缺点就是 IO 的效率和扩展性很低，容易成为应用性能瓶颈。
- NIO 是 Java 1.4 引入的 java.nio 包，提供了 Channel、Selector、Buffer 等新的抽象，可以构建多路复用的、同步非阻塞 IO 程序，同时提供了更接近操作系统底层高性能的数据操作方式。
- AIO 是 Java 1.7 之后引入的包，是 NIO 的升级版本，提供了异步非堵塞的 IO 操作方式，因此人们叫它 AIO（Asynchronous IO），异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

简单来说 BIO 就是传统 IO 包，产生的最早；NIO 是对 BIO 的改进提供了多路复用的同步非阻塞 IO，而 AIO 是 NIO 的升级，提供了异步非阻塞 IO。

#### select,poll,epoll的区别：

|            |                       select                       |                       poll                       |                            epoll                             |
| :--------- | :------------------------------------------------: | :----------------------------------------------: | :----------------------------------------------------------: |
| 操作方式   |                        遍历                        |                       遍历                       |                             回调                             |
| 底层实现   |                        数组                        |                       链表                       |                            红黑树                            |
| IO效率     |    每次调用都进行线性遍历，时间复杂度为**O(n)**    |   每次调用都进行线性遍历，时间复杂度为**O(n)**   | 事件通知方式，每当fd就绪，系统注册的回调函数就会被调用，将就绪fd放到readyList里面，时间复杂度**O(1)** |
| 最大连接数 |              1024（x86）或2048（x64）              |                      无上限                      |                            无上限                            |
| fd拷贝     | 每次调用select，都需要把fd集合从用户态拷贝到内核态 | 每次调用poll，都需要把fd集合从用户态拷贝到内核态 |  调用epoll_ctl时拷贝进内核并保存，之后每次epoll_wait不拷贝   |

#### 什么是序列化?什么是反序列化?

如果我们需要持久化 Java 对象比如将 Java 对象保存在文件中，或者在网络传输 Java 对象，这些场景都需要用到序列化。

简单来说：

- **序列化**： 将数据结构或对象转换成二进制字节流的过程
- **反序列化**：将在序列化过程中所生成的二进制字节流转换成数据结构或者对象的过程

对于 Java 这种面向对象编程语言来说，我们序列化的都是对象（Object）也就是实例化后的类(Class)，但是在 C++这种半面向对象的语言中，struct(结构体)定义的是数据结构类型，而 class 对应的是对象类型。

维基百科是如是介绍序列化的：

> **序列化**（serialization）在计算机科学的数据处理中，是指将数据结构或对象状态转换成可取用格式（例如存成文件，存于缓冲，或经由网络中发送），以留待后续在相同或另一台计算机环境中，能恢复原先状态的过程。依照序列化格式重新获取字节的结果时，可以利用它来产生与原始对象相同语义的副本。对于许多对象，像是使用大量引用的复杂对象，这种序列化重建的过程并不容易。面向对象中的对象序列化，并不概括之前原始对象所关系的函数。这种过程也称为对象编组（marshalling）。从一系列字节提取数据结构的反向操作，是反序列化（也称为解编组、deserialization、unmarshalling）。

综上：**序列化的主要目的是通过网络传输对象或者说是将对象存储到文件系统、数据库、内存中。**

#### Java 序列化中如果有些字段不想进行序列化，怎么办？

对于不想进行序列化的变量，使用 `transient` 关键字修饰。

`transient` 关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被 `transient` 修饰的变量值不会被持久化和恢复。

关于 `transient` 还有几点注意：

- `transient` 只能修饰变量，不能修饰类和方法。
- `transient` 修饰的变量，在反序列化后变量值将会被置成类型的默认值。例如，如果是修饰 `int` 类型，那么反序列后结果就是 `0`。
- `static` 变量因为不属于任何对象(Object)，所以无论有没有 `transient` 关键字修饰，均不会被序列化。

#### 获取用键盘输入常用的两种方法

方法 1：通过 `Scanner`

```java
Scanner input = new Scanner(System.in);
String s  = input.nextLine();
input.close();
```

方法 2：通过 `BufferedReader`

```java
BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
String s = input.readLine();
```

#### 既然有了字节流,为什么还要有字符流?

问题本质想问：**不管是文件读写还是网络发送接收，信息的最小存储单元都是字节，那为什么 I/O 流操作要分为字节流操作和字符流操作呢？**

回答：字符流是由 Java 虚拟机将字节转换得到的，问题就出在这个过程还算是非常耗时，并且，如果我们不知道编码类型就很容易出现乱码问题。所以， I/O 流就干脆提供了一个直接操作字符的接口，方便我们平时对字符进行流操作。如果音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好。

#### IO详解

https://javaguide.cn/java/basis/io/#

### Java异常处理

#### Exception 和 Error 有什么区别？

在 Java 中，所有的异常都有一个共同的祖先 `java.lang` 包中的 `Throwable` 类。`Throwable` 类有两个重要的子类:

- **`Exception`** :程序本身可以处理的异常，可以通过 `catch` 来进行捕获。`Exception` 又可以分为 Checked Exception (受检查异常，必须处理) 和 Unchecked Exception (不受检查异常，可以不处理)。
- **`Error`** ：`Error` 属于程序无法处理的错误 ，我们没办法通过 `catch` 来进行捕获 。例如Java 虚拟机运行错误（`Virtual MachineError`）、虚拟机内存不够错误(`OutOfMemoryError`)、类定义错误（`NoClassDefFoundError`）等 。这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。

####  Checked Exception 和 Unchecked Exception 有什么区别？

**Checked Exception** 即受检查异常，Java 代码在编译过程中，如果受检查异常没有被 `catch`/`throw` 处理的话，就没办法通过编译 。

除了`RuntimeException`及其子类以外，其他的`Exception`类及其子类都属于受检查异常 。常见的受检查异常有： IO 相关的异常、`ClassNotFoundException` 、`SQLException`...。

**Unchecked Exception** 即 **不受检查异常** ，Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译。

`RuntimeException` 及其子类都统称为非受检查异常，例如：`NullPointerException`、`NumberFormatException`（字符串转换为数字）、`ArrayIndexOutOfBoundsException`（数组越界）、`ClassCastException`（类型转换错误）、`ArithmeticException`（算术错误）等。

#### Throwable 类常用方法有哪些？

- `String getMessage()`: 返回异常发生时的简要描述
- `String toString()`: 返回异常发生时的详细信息
- `String getLocalizedMessage()`: 返回异常对象的本地化信息。使用 `Throwable` 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage()`返回的结果相同
- `void printStackTrace()`: 在控制台上打印 `Throwable` 对象封装的异常信息

#### try-catch-finally 如何使用？

- **`try`块：** 用于捕获异常。其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块。
- **`catch`块：** 用于处理 try 捕获到的异常。
- **`finally` 块：** 无论是否捕获或处理异常，`finally` 块里的语句都会被执行。当在 `try` 块或 `catch` 块中遇到 `return` 语句时，`finally` 语句块将在方法返回之前被执行。

**注意：不要在 finally 语句块中使用 return!** 当 try 语句和 finally 语句中都有 return 语句时，try 语句块中的 return 语句不会被执行。

####  finally 中的代码一定会执行吗？

不一定的！在某些情况下，finally 中的代码不会被执行。

就比如说 `finally` 之前虚拟机被终止运行的话，finally 中的代码就不会被执行。

另外，在以下 2 种特殊情况下，`finally` 块的代码也不会被执行：

1. 程序所在的线程死亡。
2. 关闭 CPU

#### 如何使用 `try-with-resources` 代替`try-catch-finally`？

1. **适用范围（资源的定义）：** 任何实现 `java.lang.AutoCloseable`或者 `java.io.Closeable` 的对象
2. **关闭资源和 finally 块的执行顺序：** 在 `try-with-resources` 语句中，任何 catch 或 finally 块在声明的资源关闭后运行

> 面对必须要关闭的资源，我们总是应该优先使用 `try-with-resources` 而不是`try-finally`。随之产生的代码更简短，更清晰，产生的异常对我们也更有用。`try-with-resources`语句让我们更容易编写必须要关闭的资源的代码，若采用`try-finally`则几乎做不到这点。

### 扩展

#### 为什么迭代器的 next() 返回的是 Object 类型？

答：因为迭代器不需要关注容器的内部细节，所以 next() 返回 Object 类型就可以接收任何类型的对象。

#### HashMap 的遍历方式都有几种？

答：HashMap 的遍历分为以下四种方式。

- 方式一：entrySet 遍历
- 方式二：iterator 遍历
- 方式三：遍历所有的 key 和 value
- 方式四：通过 key 值遍历

## Java集合

### 集合概述

#### 常见的集合有哪些？

集合分为List、Queue、Set、Map四个分支。

<img src="https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202203012046494.png" alt="image-20220301182637883" style="zoom: 50%;" />

#### 线程安全的集合有哪些？线程不安全的呢？

线程安全的：

* Hashtable：比HashMap多了个线程安全
* ConcurrentHashMap：是一种高效但是线程安全的集合
* Vector：比ArrayList多了个同步化机制
  * 使用``Collections.synchronizedList(new ArrayList<>())``：
  * 使用``new CopyOnWriteArrayList<>()``：其每次写操作都会进行一次数组复制操作，然后对新复制的数组进行些操作，不可能存在在同时又读写操作在同一个数组上（ 不是同一个对象），而读操作并没有对数组修改，不会产生线程安全问题。**CopyOnWriteArrayList比Vector高效，因为Vector使用synchronized会速度慢，而CopyOnWriteArrayList使用了lock锁。**（用在缓存）
* Stack：栈也是线程安全的，继承与Vector

线程不安全的：HashMap、ArrayList、LinkedList、HashSet、TreeSet、TreeMap

#### Vector

Vector 是 Java 早期提供的线程安全的有序集合，如果不需要线程安全，不建议使用此集合，毕竟同步是有线程开销的。

####  ArrayList

ArrayList 是最常见的非线程安全的有序集合，因为内部是数组存储的，所以随机访问效率很高，但非尾部的插入和删除性能较低，如果在中间插入元素，之后的所有元素都要后移。ArrayList 的使用与 Vector 类似。

#### LinkedList

LinkedList是使用双向链表数据结构实现的，因此增加和删除效率比较高，而随机访问效率较差。

LinkedList除了包含以上两个类的操作方法之外，还新增了几个操作方法，如 offer() 、peek() 等。

#### HashSet

HashSet 是一个没有重复元素的集合。虽然它是 Set 集合的子类，实际却为 HashMap 的实例。因此 HashSet 是无序集合，没有办法保证元素的顺序性。

#### TreeSet

TreeSet 集合实现了自动排序，也就是说 TreeSet 会把你插入数据进行自动排序。

#### LinkedHashSet

LinkedHashSet 是按照元素的 hashCode 值来决定元素的存储位置，但同时又使用链表来维护元素的次序，这样使得它看起来像是按照插入顺序保存的。

LinkedHashSet 的使用与 Vector 类似。

#### 集合与数组

集合和数组的转换可使用 toArray() 和 Arrays.asList() 来实现。

```java
List<String> list = new ArrayList();
list.add("cat");
list.add("dog");
// 集合转数组
String[] arr = list.toArray(new String[list.size()]);
// 数组转集合
List<String> list2 = Arrays.asList(arr);
```

#### 集合排序

在 Java 语言中排序提供了两种方式：Comparable 和 Comparator，它们的区别也是常见的面试题之一。下面彻底地来了解一下 Comparable 和 Comparator 的使用与区别。

##### Comparable

Comparable 位于 java.lang 包下，是一个排序接口，也就是说如果一个类实现了 Comparable 接口，就意味着该类有了排序功能。

```java
package java.lang;
import java.util.*;
public interface Comparable {
  public int compareTo(T o);
}
```

代码实例：

```java
class ComparableTest {
    public static void main(String[] args) {
        Dog[] dogs = new Dog[]{
                new Dog("老旺财", 10),
                new Dog("小旺财", 3),
                new Dog("二旺财", 5),
        };
        // Comparable 排序
        Arrays.sort(dogs);
        for (Dog d : dogs) {
            System.out.println(d.getName() + "：" + d.getAge());
        }
    }
}
class Dog implements Comparable<Dog> {
    private String name;
    private int age;
    @Override
    public int compareTo(Dog o) {
        return age - o.age;
    }
    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public int getAge() {
        return age;
    }
}
```

##### Comparator

Comparator 是一个外部比较器，位于 java.util 包下，之所以说 Comparator 是一个外部比较器，是因为它无需在比较类中实现 Comparator 接口，而是要新创建一个比较器类来进行比较和排序。

Comparator 接口包含的主要方法为 compare()，定义如下：

```java
public interface Comparator<T> {
  int compare(T o1, T o2);
}
```

**Comparator 使用示例**，请参考以下代码：

````java
class ComparatorTest {
    public static void main(String[] args) {
        Dog[] dogs = new Dog[]{
            new Dog("老旺财", 10),
            new Dog("小旺财", 3),
            new Dog("二旺财", 5),
        };
        // Comparator 排序
        Arrays.sort(dogs,new DogComparator());
        for (Dog d : dogs) {
            System.out.println(d.getName() + "：" + d.getAge());
        }
    }
}
class DogComparator implements Comparator<Dog> {
    @Override
    public int compare(Dog o1, Dog o2) {
        return o1.getAge() - o2.getAge();
    }
}
class Dog {
    private String name;
    private int age;
    public Dog(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public int getAge() {
        return age;
    }
}

````



### Collection子接口

#### List和Set的区别

List：有序可重复的，运行多个Null元素对象，可以使用iterator取出所有元素，在逐一遍历，还可以使用get(int index)获取指定下标的元素。

Set：无序不可重复的，只允许一个null对象，取元素时只能用Iterator接口取得所有元素，在逐一遍历各个元素。

#### Arraylist与LinkedList的区别

**ArrayList：基于动态数组，连续内存存储，适合下标访问（随机访问），扩容机制**：因为数组长度固定，超出长度存数据时需要新建数组，然后将老数组的数据拷贝到新数组，如果不是尾部插入数据还会涉及到元素的移动（往后复制一份，插入新元素），**使用尾插法并指定初始容量可以极大提升性能，甚至超过linkedList（需要创建大量的node对象）。**（每次创建node对象都会消耗性能）

**LinkedList：基于链表，可以存储在分散的内存中，适合做数据插入及删除操作，不适合查询**：==因为需要逐一遍历LinkeList必须使用iterator不能使用for循环，因为每次for循环体内通过get(i)取得某一元素时需要对list重新遍历，性能消耗极大。==

**另外不要试图使用indexOf等返回元素索引，并利用其进行遍历，使用indexOf对list进行了遍历，当结果为空时会遍历整个列表。**

#### ArrayList与Vector的区别

- `ArrayList` 是 `List` 的主要实现类，底层使用 `Object[ ]`存储，适用于频繁的查找工作，线程不安全 ；
- `Vector` 是 `List` 的古老实现类，底层使用` Object[ ]` 存储，线程安全的。

#### ArrayList的扩容机制

扩容机制**：因为数组长度固定，超出长度存数据时需要新建数组，然后将老数组的数据拷贝到新数组，如果不是尾部插入数据还会涉及到元素的移动（往后复制一份，插入新元素），**使用尾插法并指定初始容量可以极大提升性能，甚至超过linkedList（需要创建大量的node对象）。（每次创建node对象都会消耗性能）

**扩容机制：本质是计算出新的扩容数字的大小后，创建一个新的数组，然后把原有的数组内容复制到新数组中取。默认情况下，新的容量会是原容量的1.5倍。**

#### Vector 和 ArrayList 初始化大小和容量扩充有什么区别？

Vector 和 ArrayList 的默认容量都为 10。Vector 容量扩充默认增加 1 倍，ArrayList 容量扩充默认增加 0.5 倍。

#### Array与ArrayList的区别

* Array可以包含基本类型和对象类型，ArrayList只能包含对象类型
* Array大小是固定的，ArrayList的大小是动态变化的
* ArrayList提供了更多的方法和特性，比如addAll()，iterator()等待

#### Vector、ArrayList、LinkedList 有什么区别？

答：这三者都是 List 的子类，因此功能比较相似，比如增加和删除操作、查找元素等，但在性能、线程安全等方面表现却又不相同，差异如下：

- Vector 是 Java 早期提供的动态数组，它使用 synchronized 来保证线程安全，如果非线程安全需要不建议使用，毕竟线程同步是有性能开销的；
- ArrayList 是最常用的动态数组，本身并不是线程安全的，因此性能要好很多，与 Vector 类似，它也是动态调整容量的，只不过 Vector 扩容时会增加 1 倍，而 ArrayList 会增加 50%；
- LinkedList 是双向链表集合，因此它不需要像上面两种那样调整容量，它也是非线程安全的集合。

#### Vector、ArrayList、LinkedList 使用场景有什么区别？

答：Vector 和 ArrayList 的内部结构是以数组形式存储的，因此**非常适合随机访问**，但非尾部的删除或新增性能较差，比如我们在中间插入一个元素，就需要把后续的所有元素都进行移动。

LinkedList **插入和删除元素效率比较高**，但随机访问性能会比以上两个动态数组慢。

#### Collection 和 Collections 有什么区别？

答：Collection 和 Collections 的区别如下：

- Collection 是集合类的上级接口，继承它的主要有 List 和 Set；
- Collections 是针对集合类的一个帮助类，它提供了一些列的静态方法实现，如 Collections.sort() 排序、Collections.reverse() 逆序等。

#### LinkedHashSet 如何保证有序和唯一性？

答：LinkedHashSet 底层数据结构由哈希表和链表组成，链表保证了元素的有序即存储和取出一致，哈希表保证了元素的唯一性。

#### 如何用程序实现后进先出的栈结构？

答：可以使用集合中的 Stack 实现，Stack 是标准的后进先出的栈结构，使用 Stack 中的 pop() 方法返回栈顶元素并删除该元素。

#### LinkedList 中的 peek() 和 poll() 有什么区别？

答：peek() 方法返回第一个元素，但不删除当前元素，当元素不存在时返回 null；poll() 方法返回第一个元素并删除此元素，当元素不存在时返回 null。

#### Comparable 和 Comparator 有哪些区别？

答：Comparable 和 Comparator 的主要区别如下：

- Comparable 位于 java.lang 包下，而 Comparator 位于 java.util 包下；
- Comparable 在排序类的内部实现，而 Comparator 在排序类的外部实现；
- Comparable 需要重写 CompareTo() 方法，而 Comparator 需要重写 Compare() 方法；
- Comparator 在类的外部实现，更加灵活和方便。

### Map

#### **Map 常用的实现类**

- **Hashtable**：Java 早期提供的一个哈希表实现，它是线程安全的，不支持 null 键和值，因为它的性能不如 ConcurrentHashMap，所以很少被推荐使用。
- **HashMap**：最常用的哈希表实现，如果程序中没有多线程的需求，HashMap 是一个很好的选择，支持 null 键和值，如果在多线程中可用 ConcurrentHashMap 替代。
- **TreeMap**：基于红黑树的一种提供顺序访问的 Map，自身实现了 key 的自然排序，也可以指定 Comparator 来自定义排序。
- **LinkedHashMap**：HashMap 的一个子类，保存了记录的插入顺序，可在遍历时保持与插入一样的顺序。

#### HahsMap和Hashtable的区别？

区别：

**线程是否安全**：HashMap方法**没有synchronized修饰**，线程非安全，Hashtable线程安全。

**对key以及value是否可以为null**：HashMap允许key和value为null，而HashTable不允许。

**底层数据结构**：HashMap和Hashtable的底层实现都是**数组+链表结构实现**。 JDK1.8 以后的 `HashMap` 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。

> 首先计算key的hash值，二次hash然后对数组长度取模，对应到数组下标
>
> 如果没有产生hash冲突（下标位置没有元素），则直接创建Node存入数组
>
> 如果产生hash冲突，先进行equal比较，相同则取代元素，不同，则判断链表高后，插入链表，链表高度达到8，并且数组长度达到64则转变为红黑树，长度低于6则将红黑树转回链表。
>
> key为null，存在下标0的位置。

#### HashMap的底层数据是什么？（上面有了）

在JDK1.7中，由“**数组+链表**”组成，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的。

在JDK1.8中，有“**数组+链表+红黑树**”组成。当链表过长，则会严重影响HashMap的性能，红黑树搜索时间复杂度$O(\log n)$，而链表是糟糕的$O(n)$。因此，JDK1.8对数据结构做了进一步的优化，引入了红黑树，链表和红黑树在达到一定条件会进行转换：

* 当链表超过8且数据总量超过64才会转红黑树。
* 将链表转换成红黑树前会判断，如果当前数组的长度小于64，那么会选择先进行数组扩容，而不是转换为红黑树，以减少搜索时间。

![image-20220302140050493](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202203022322755.png)

```java
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable {
    // 序列号
    private static final long serialVersionUID = 362498820763181265L;
    // 默认的初始容量是16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
    // 最大容量
    static final int MAXIMUM_CAPACITY = 1 << 30;
    // 默认的填充因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    // 当桶(bucket)上的结点数大于这个值时会转成红黑树
    static final int TREEIFY_THRESHOLD = 8;
    // 当桶(bucket)上的结点数小于这个值时树转链表
    static final int UNTREEIFY_THRESHOLD = 6;
    // 桶中结构转化为红黑树对应的table的最小大小
    static final int MIN_TREEIFY_CAPACITY = 64;
    // 存储元素的数组，总是2的幂次倍
    transient Node<k,v>[] table;
    // 存放具体元素的集
    transient Set<map.entry<k,v>> entrySet;
    // 存放元素的个数，注意这个不等于数组的长度。
    transient int size;
    // 每次扩容和更改map结构的计数器
    transient int modCount;
    // 临界值 当实际大小(容量*填充因子)超过临界值时，会进行扩容
    int threshold;
    // 加载因子
    final float loadFactor;
}
```

- **loadFactor 加载因子**：loadFactor 加载因子是控制数组存放数据的疏密程度，loadFactor 越趋近于 1，那么 数组中存放的数据(entry)也就越多，也就越密，也就是会让链表的长度增加，loadFactor 越小，也就是趋近于 0，数组中存放的数据(entry)也就越少，也就越稀疏。

  **loadFactor 太大导致查找元素效率低，太小导致数组的利用率低，存放的数据会很分散。loadFactor 的默认值为 0.75f 是官方给出的一个比较好的临界值**。

  给定的默认容量为 16，负载因子为 0.75。Map 在使用过程中不断的往里面存放数据，当数量达到了 16 * 0.75 = 12 就需要将当前 16 的容量进行扩容，而扩容这个过程涉及到 rehash、复制数据等操作，所以非常消耗性能。

- **threshold**：**threshold = capacity \* loadFactor**，**当 Size>=threshold**的时候，那么就要考虑对数组的扩增了，也就是说，这个的意思就是**衡量数组是否需要扩增的一个标准**。

#### 解决hash冲突的办法有哪些？HashMap用的哪种？

解决Hash冲突方法有：**开放定址法（p=hash(p)）、再哈希法（多个hash）、链地址法（拉链法)、建立公共溢出区**。HashMap中采用的是**链地址法**。

> HashMap 使用链表和红黑树来解决哈希冲突。

#### 为什么在解决hash冲突的时候，不直接使用红黑树，而是选择先用链表，再转红黑树？

因为红黑树需要进行左旋，右旋，变色这些操作来保持平衡，而单链表不需要。当元素小于8个的时候，此时做查询操作，链表结构已经能保证查询性能。当元素大于8个的时候，红黑树搜索时间复杂度是o(logn)，而链表是O(n)，此时需要红黑树来加快查询速度，但是新增节点的效率变慢了。

因此，如果一开始就用红黑树结构，元素太少，新增效率又比较慢，无疑这是浪费性能的。

#### HashMap 有哪些重要的参数？用途分别是什么？

答：HashMap 有两个重要的参数：容量（Capacity）和负载因子（LoadFactor）。

- 容量（Capacity）：是指 HashMap 中桶的数量，默认的初始值为 16。
- 负载因子（LoadFactor）：也被称为装载因子，LoadFactor 是用来判定 HashMap 是否扩容的依据，默认值为 0.75f，装载因子的计算公式 = HashMap 存放的 KV 总和（size）/ Capacity

阈值=容量*负载因子。扩容两倍`（hash& (length-1））`，这样做的目的是为了让散列更加均匀，从而减少哈希碰撞，以提供代码的执行效率。

#### HashMap默认加载因子是多少？为什么是0.75，不是0.6或者0.8？

默认的loadFactor是0.75，0.75是对空间和时间效率的一个平衡选择，一般不要修改，除非在时间和空间比较特殊的情况下：

- 如果内存空间很多而又对时间效率要求很高，可以降低负载因子Load factor的值。
- 相反，如果内存空间紧张而对时间效率要求不高，可以增加负载因子loadFactor的值，这个值可以大于1。 

#### HashMap中key的存储索引是怎么计算的？

首先根据key的值计算出hashcode的值，然后根据hashcode计算出hash值，最后通过hash& (length-1）计算得到存储的位置。

#### HashMap的put方法流程？

1.首先根据key的值计算hash值，找到该元素在数组中存储的下标;

2.如果数组是空的，则调用resize进行初始化，否者判断是否超过阀值，如果超过就要扩容。;

3.如果没有哈希冲突直接放在对应的数组下标里;

4.如果冲突了，且 key已经存在，就覆盖掉value;

5.如果冲突后，发现该节点是红黑树，就将这个节点挂在树上;

6.如果冲突后是链表，判断该链表是否大于8，如果大于将这个结构转换为红黑树;否则，链表插入键值对，若key存在，就覆盖掉value。

<img src="../../../../../Pictures/assets/Java面试题快速准备/727836f0-ccc7-11e9-a9bd-857608719494" alt="enter image description here" style="zoom:67%;" />

#### 获取方法：get(Object key)

执行流程如下：

- 首先比对首节点，如果首节点的 hash 值和 key 的 hash 值相同，并且首节点的键对象和 key 相同（地址相同或 equals 相等），则返回该节点；
- 如果首节点比对不相同、那么看看是否存在下一个节点，如果存在的话，可以继续比对，如果不存在就意味着 key 没有匹配的键值对。

#### 一般用什么作为HashMap的key？

一般用Integer、String这种不可变类当HashMap当key，而且String最为常用。

- 因为字符串是不可变的，所以在它创建的时候hashcode就被缓存了，不需要重新计算。这就是HashMap 中的键往往都使用字符串的原因。
- 因为获取对象的时候要用到equals()和 hashCode()方法，那么键对象正确的重写这两个方法是非常重要的,这些类已经很规范的重写了hashCode()以及 equals()方法。

#### **HashMap为什么线程不安全？以及遇到的问题？如何避免？**

![image-20220302152824165](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202203022322758.png)

- **多线程下扩容死循环。**JDK1.7中的HashMap**使用头插法插入元素**，在多线程的环境下，扩容的时候有可能导致环形链表的出现，形成死循环。因此，JDK1.8**使用尾插法插入元素**，在扩容时会保持链表元素原本的顺序，不会出现环形链表的问题。
- **多线程的put可能导致元素的丢失。**多线程同时执行put操作，如果计算出来的索引位置是相同的，那会造成前一个key被后一个key覆盖，从而导致元素的丢失。此问题在JDK1.7和 JDK 1.8中都存在。
- **put和get并发时，可能导致get为null。**线程1执行put时，因为元素个数超出threshold而导致rehash，线程2此时执行get，有可能导致这个问题。此问题在JDK1.7和 JDK 1.8中都存在。

**因为 HashMap 本来就不是用在多线程版本下的，如果是多线程可使用 ConcurrentHashMap 替代 HashMap。**

#### ConcurrentHashMap的实现原理是什么？

ConcurrentHashMap在JDK1.7和JDK1.8的实现方式是不同的。

> 不允许键值对为null。

**JDK1.7**：

在JDK1.7中，ConcurrentHashMap是由`ReentrantLock`+`Segment`数组结构和`HashEntry`数组结构组成，也就是一个`Segment`包含一个`HashEntry`数组，每个`HashEntry`又是一个链表结构。

其中`Segment`继承了`ReentrantLock`，所以`Segment`是一种可重入锁，扮演锁的角色；`HashEntry`用于存储键值对数据。

首先将数据分为一段一段的存储，然后给每一把数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问，能够实现真正的并发访问。其中并发度为segment的个数。

元素查询：两次hash，第一次hash定位到segment，第二次hash定位到元素所在的链表头部。

**JDK1.8**：

数据结构：**synchronized+CAS(乐观锁机制)+Node+红黑树**，Node的val和next都用volatile修饰，保证可见性

查找、替换、赋值操作都是使用CAS。

锁：锁链表的head节点，不影响其他元素的读写，锁粒度更细，效率更高，扩容时，阻塞所有的读写操作，并发扩容。

读操作无锁：node的val和next使用volatie修饰，读写线程对该变量互相可见

数组用volatile修饰，保证扩容时被该线程感知。

#### ConcurrentHashMap的put方法执行逻辑是什么？

**先来看JDK1.7**
首先，会尝试获取锁，如果获取失败，利用自旋获取锁;如果自旋重试的次数超过64次，则改为阻塞获取锁。
获取到锁后:

1. 将当前Segment中的 table通过 key 的 hashcode定位到HashEntry.

2. 遍历该HashEntry，如果不为空则判断传入的 key和当前遍历的 key是否相等，相等则覆盖旧的value。

3. 不为空则需要新建一个HashEntry并加入到Segment中，同时会先判断是否需要扩容。4.释放Segment 的锁。

**再来看JDK1.8**
大致可以分为以下步骤:

1. 根据 key计算出 hash值。
2. 判断是否需要进行初始化。
3. 定位到Node，拿到首节点f，判断首节点f：
   1. 如果为null ，则通过cas的方式尝试添加。
   2. 如果为f.hash = MOVED = -1，说明其他线程在扩容，参与一起扩容。
   3. 如果都不满足，synchronized锁住f节点，判断是链表还是红黑树，遍历插入。
4. 当在链表长度达到8的时候，数组扩容或者将链表转换为红黑树。

#### ConcurrentHashMap的get方法是否要加锁，为什么？

get方法不需要加锁。因为Node 的元素val和指针 next 是用volatile修饰的，在多线程环境下线程A修改结点的val或者新增节点的时候是对线程B可见的。

#### get方法不需要加锁与volatile修饰的哈希桶有关吗？

没有关系。哈希桶table用volatile修饰主要是保证在数组扩容的时候保证可见性。

#### ConcurrentHashMap不支持key或者value为null的原因？

我们先来说value为什么不能为null，因为ConcurrentHashMap是用于多线程的，如果map.get(key)得到了null，无法判断，是映射的value是 null，还是没有找到对应的key而为null ,这就有了二义性。

而用于单线程状态的HashMap却可以用containsKey(key)去判断到底是否包含了这个null 。我们用反证法来推理:

**假设ConcurrentHashMap 允许存放值为null的value，这时有A、B两个线程，线程A调用ConcurrentHashMap .get(key)方法，返回为null，我们不知道这个null 是没有映射的null，还是存的值就是null 。**

**假设此时，返回为null 的真实情况是没有找到对应的key。那么，我们可以用ConcurrentHashMap.containsKey(key)来验证我们的假设是否成立，我们期望的结果是返回false。**

**但是在我们调用ConcurrentHashMap .get(key)方法之后，containsKey方法之前，线程B执行了ConcurrentHashMap .put(key , null )的操作。那么我们调用containsKey方法返回的就是true了，这就与我们的假设的真实情况不符合了，这就有了二义性。**

至于ConcurrentHashMap 中的key为什么也不能为null 的问题，源码就是这样写的，哈哈。如果面试官不满意，就回答因为作者Doug不喜欢null，所以在设计之初就不允许了null 的key存在。

#### ConcurrentHashMap的并发度是多少？

在JDK1.7中，并发度默认是16，这个值可以在构造函数中设置。如果自己设置了并发度，ConcurrentHashMap 会使用大于等于该值的最小的2的幂指数作为实际并发度，也就是比如你设置的值是17，那么实际并发度是32。

#### ConcurrentuHashMap迭代器是强一致性还是弱一致性？

与HashMap迭代器是强一致性不同，ConcurrentHashMap 迭代器是弱一致性。

ConcurrentHashMap的迭代器创建后，就会按照哈希表结构遍历每个元素，但在遍历过程中，内部元素可能会发生变化，如果变化发生在已遍历过的部分，迭代器就不会反映出来，而如果变化发生在未遍历过的部分，迭代器就会发现并反映出来，这就是弱一致性。

这样迭代器线程可以使用原来老的数据，而写线程也可以并发的完成改变，更重要的，这保证了多个线程并发执行的连续性和扩展性，是性能提升的关键。

#### JDK1.7与JDK1.8中ConcurrentHashMap的区别？

- 数据结构:取消了Segment分段锁的数据结构，取而代之的是数组+链表+红黑树的结构。
- 保证线程安全机制:JDK1.7采用Segment的分段锁机制实现线程安全，其中segment继承自ReentrantLock。JDK1.8采用CAS+Synchronized保证线程安全。
- 锁的粒度:原来是对需要进行数据操作的Segment加锁，现调整为对每个数组元素加锁(Node) 。
- 链表转化为红黑树:定位结点的hash算法简化会带来弊端,Hash冲突加剧,因此在链表节点数量大于8时，会将链表转化为红黑树进行存储。
- 查询时间复杂度:从原来的遍历链表O(n)，变成遍历红黑树O(logN)。

#### ConcurrentHashMap和Hashtable的效率哪个更高？为什么？

ConcurrentHashMap的效率要高于Hashtable，因为Hashtable给整个哈希表加了一把大锁从而实现线程安全。而ConcurrentHashMap 的锁粒度更低，在JDK1.7中采用分段锁实现线程安全，在JDK1.8中采用CAS+synchronized实现线程安全。

#### 说一下Hashtable的锁机制？

Hashtable是使用Synchronized来实现线程安全的，给整个哈希表加了一把大锁，多线程访问时候，只要有一个线程访问或操作该对象，那其他线程只能阻塞等待需要的锁被释放，在竞争激烈的多线程场景中性能就会非常差!

#### 多线程下安全的操作map还有其他方法吗？

```java
// map是这样用的吗？不是，工作中不用HahsMap
// 默认等价于什么？  new HashMap(16,0.75)
// Map<String,String> map = new HashMap<>();

Map<String,String> map = new ConcurrentHashMap<>();
// 还可以使用collections.synchronizedMap方法，对方法进行加同步锁
```

#### HashSet与HashMap的区别？

如果你看过 `HashSet` 源码的话就应该知道：`HashSet` 底层就是基于 `HashMap` 实现的。（`HashSet` 的源码非常非常少，因为除了 `clone()`、`writeObject()`、`readObject()`是 `HashSet` 自己不得不实现之外，其他方法都是直接调用 `HashMap` 中的方法。

|               `HashMap`                |                          `HashSet`                           |
| :------------------------------------: | :----------------------------------------------------------: |
|           实现了 `Map` 接口            |                       实现 `Set` 接口                        |
|               存储键值对               |                          仅存储对象                          |
|     调用 `put()`向 map 中添加元素      |             调用 `add()`方法向 `Set` 中添加元素              |
| `HashMap` 使用键（Key）计算 `hashcode` | `HashSet` 使用成员对象来计算 `hashcode` 值，对于两个对象来说 `hashcode` 可能相同，所以`equals()`方法用来判断对象的相等性 |

#### HashMap 和 TreeMap 区别

`TreeMap` 和`HashMap` 都继承自`AbstractMap` ，但是需要注意的是`TreeMap`它还实现了`NavigableMap`接口和`SortedMap` 接口。**相比于`HashMap`来说 `TreeMap` 主要多了对集合中的元素根据键排序的能力以及对集合内元素的搜索的能力。**

##### Iterator与ListIterator有什么区别？

- 遍历。使用lterator，可以遍历所有集合，如Map，List，Set;但只能在向前方向上遍历集合中的元素。
  使用Listlterator，只能遍历List实现的对象，但可以向前和向后遍历集合中的元素。
- 添加元素。lterator无法向集合中添加元素;而，Listlteror可以向集合添加元素。
- 修改元素。lterator无法修改集合中的元素;而，Listlterator可以使用set()修改集合中的元素。
- 索引。lterator无法获取集合中元素的索引;而，使用Listlterator，可以获取集合中元素的索引。

##### 快速失败(fail-fast)与安全失败(fail-false)         

快速失败：

- 在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出Concurrent Modification Exception。
- 原理:迭代器在遍历时直接访问集合中的内容，并且在遍历过程中使用一个modCount变量。集合在被遍历期间如果内容发生变化，就会改变modCount的值。每当迭代器使用hashNext()/next()遍历下一个元素之前，都会检测modCount变量是否为expectedmodCount值，是的话就返回遍历;否则抛出异常，终止遍历。
- 注意:这里异常的抛出条件是检测到modCount! =expectedmodCount这个条件。如果集合发生变化时修改modCount值刚好又设置为了expectedmodCount值，则异常不会抛出。因此，不能依赖于这个异常是否抛出而进行并发操作的编程，这个异常只建议用于检测并发修改的bug。

- 场景: java.util包下的集合类都是快速失败的，不能在多线程下发生并发修改（迭代过程中被修改），比如HashMap、ArrayList这些集合类。          

安全失败：

- 采用安全失败机制的集合容器，在遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在拷贝的集合上进行遍历。
- 原理:由于迭代时是对原集合的拷贝进行遍历，所以在遍历过程中对原集合所作的修改并不能被迭代器检测到，所以不会触发Concurrent Modification Exception。
- 缺点:基于拷贝内容的优点是避免了Concurrent Modification Exception，但同样地，迭代器并不能访问到修改后的内容，即:迭代器遍历的是开始遍历那一刻拿到的集合拷贝，在遍历期间原集合发生的修改迭代器是不知道的。
- 场景: java.util.concurrent包下的容器都是安全失败，可以在多线程下并发使用，并发修改，比如: ConcurrentHashMap。（使用CopyOnWriteArraySet/Collections.synchronizedSet）

## Java虚拟机（JVM）

## Java并发与多线程

### 多线程

#### 什么是线程和进程？

进程：进程是程序的一次执行过程，是系统运行程序的基本单位，因此进程是动态的。系统运行一个程序即是一个进程从创建，运行到消亡的过程。

线程：线程与进程相似，但线程是一个比进程更小的执行单位。一个进程在其执行的过程中可以产生多个线程。与进程不同的是**同类的多个线程**共享进程的**堆**和**方法区**资源，但每个线程有自己的**程序计数器**、**虚拟机栈**和**本地方法栈**，所以系统在产生一个线程，或是在各个线程之间作切换工作时，负担要比进程小得多，也正因为如此，线程也被称为轻量级进程。 

#### 请简要描述线程与进程的关系,区别及优缺点？

一个进程中可以有多个线程，多个线程共享进程的**堆**和**方法区 (JDK1.8 之后的元空间)资源，但是每个线程有自己的程序计数器、虚拟机栈 和 本地方法栈**。

总结： **线程是进程划分成的更小的运行单位。线程和进程最大的不同在于基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。线程执行开销小，但不利于资源的管理和保护；而进程正相反。**

程序计数器私有主要是为了**线程切换后能恢复到正确的执行位置**。

为了**保证线程中的局部变量不被别的线程访问到**，虚拟机栈和本地方法栈是线程私有的。

堆和方法区是所有线程共享的资源，其中堆是进程中最大的一块内存，主要用于存放新创建的对象 (几乎所有对象都在这里分配内存)，方法区主要用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。 

#### 说说并发与并行的区别?

- **并发：** 同一时间段，多个任务都在执行 (单位时间内不一定同时执行)；
- **并行：** 单位时间内，多个任务同时执行。

####  为什么要使用多线程呢?

先从总体上来说：

- **从计算机底层来说：** 线程可以比作是轻量级的进程，是程序执行的最小单位,**线程间的切换和调度的成本远远小于进程**。另外，多核 CPU 时代意味着多个线程可以同时运行，这**减少了线程上下文切换的开销**。
- **从当代互联网发展趋势来说：** 现在的系统动不动就要求百万级甚至千万级的并发量，而多线程并发编程正是开发高并发系统的基础，**利用好多线程机制可以大大提高系统整体的并发能力以及性能**。

再深入到计算机底层来探讨：

- **单核时代**： 在单核时代多线程主要是**为了提高单进程利用 CPU 和 IO 系统的效率。** 假设只运行了一个 Java 进程的情况，当我们请求 IO 的时候，如果 Java 进程中只有一个线程，此线程被 IO 阻塞则整个进程被阻塞。CPU 和 IO 设备只有一个在运行，那么可以简单地说系统整体效率只有 50%。当使用多线程的时候，一个线程被 IO 阻塞，其他线程还可以继续使用 CPU。从而提高了 Java 进程利用系统资源的整体效率。
- **多核时代**: 多核时代多线程主要是**为了提高进程利用多核 CPU 的能力。**举个例子：假如我们要计算一个复杂的任务，我们只用一个线程的话，不论系统有几个 CPU 核心，都只会有一个 CPU 核心被利用到。而创建多个线程，这些线程可以被映射到底层多个 CPU 上执行，在任务中的多个线程没有资源竞争的情况下，任务执行的效率会有显著性的提高，约等于（单核时执行时间/CPU 核心数）。

#### 使用多线程可能带来什么问题?

并发编程的目的就是为了能提高程序的执行效率以及提高程序运行速度，但是并发编程并不总是能提高程序运行速度的，而且并发编程可能会遇到很多问题，比如：内存泄漏、死锁、线程不安全等等。

#### 线程的生命周期和状态?

Java 线程在运行的生命周期中的指定时刻只可能处于下面 6 种不同状态的其中一个状态

![img](../../../../../Pictures/assets/Java面试题快速准备/Java+线程状态变迁.png)

当线程执行 `wait()`方法之后，线程进入 **WAITING（等待）** 状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而 **TIMED_WAITING(超时等待)** 状态相当于在等待状态的基础上增加了超时限制。

#### 什么是上下文切换?

线程在执行过程中会有自己的运行条件和状态（也称上下文）。

线程切换意味着需要保存当前线程的上下文，留待线程下次占用 CPU 的时候恢复现场。并加载下一个将要占用 CPU 的线程上下文。这就是所谓的 **上下文切换**。

#### 什么是线程死锁?

多个线程同时被阻塞，它们中的一个或者全部都在等待某个资源被释放。由于线程被无限期地阻塞，因此程序不可能正常终止。比如，线程 A 持有资源 2，线程 B 持有资源 1，他们同时都想申请对方的资源，所以这两个线程就会互相等待而进入死锁状态。

产生死锁必须具备以下四个条件：

1. **互斥条件**：该资源任意一个时刻只由一个线程占用。
2. **请求与保持条件**：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
3. **不剥夺条件**:线程已获得的资源在未使用完之前不能被其他线程强行剥夺，只有自己使用完毕后才释放资源。
4. **循环等待条件**:若干进程之间形成一种头尾相接的循环等待资源关系。



#### 如何预防和避免线程死锁?

**如何预防死锁？** 破坏死锁的产生的必要条件即可：

1. **破坏请求与保持条件** ：一次性申请所有的资源。
2. **破坏不剥夺条件** ：占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。
3. **破坏循环等待条件** ：靠按序申请资源来预防。按某一顺序申请资源，释放资源则反序释放。破坏循环等待条件。

**如何避免死锁？**

避免死锁就是在资源分配时，借助于算法（比如银行家算法）对资源分配进行计算评估，使其进入安全状态。

**安全状态** 指的是系统能够按照某种进程推进顺序（P1、P2、P3.....Pn）来为每个进程分配所需资源，直到满足每个进程对资源的最大需求，使每个进程都可顺利完成。称<P1、P2、P3.....Pn>序列为安全序列。

**具体实例**：假定操作系统中的4个进程P1、P2、P3、P4和3类资源R1、R2、R3(资源数量分别为9、3、6)，在t0时刻的资源分配情况如表2-1：

![img](../../../../../Pictures/assets/Java面试题快速准备/1358881-20191125171934215-167814328.png)

![img](../../../../../Pictures/assets/Java面试题快速准备/1358881-20191125172027066-647382599.png)

![img](../../../../../Pictures/assets/Java面试题快速准备/1358881-20191125172232493-1843282166.png)

![img](../../../../../Pictures/assets/Java面试题快速准备/1358881-20191125172314635-1423969714.png)

![img](../../../../../Pictures/assets/Java面试题快速准备/1358881-20191125172354117-21808820.png)

![img](../../../../../Pictures/assets/Java面试题快速准备/1358881-20191125172427092-1250011632.png)

![img](../../../../../Pictures/assets/Java面试题快速准备/1358881-20191125172453218-639569059.png)

#### 说说 sleep() 方法和 wait() 方法区别和共同点?

- 两者最主要的区别在于：**`sleep()` 方法没有释放锁，而 `wait()` 方法释放了锁** 。
- 两者都可以暂停线程的执行。
- `wait()` 通常被用于线程间交互/通信，`sleep()`通常被用于暂停执行。
- `wait()` 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 `notify()`或者 `notifyAll()` 方法。`sleep()`方法执行完成后，线程会自动苏醒。或者可以使用 `wait(long timeout)` 超时后线程会自动苏醒。

#### 为什么我们调用 start() 方法时会执行 run() 方法，为什么我们不能直接调用 run() 方法？

new 一个 Thread，线程进入了新建状态。调用 `start()`方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。 `start()` 会执行线程的相应准备工作，然后自动执行 `run()` 方法的内容，这是真正的多线程工作。 

但是，直接执行 `run()` 方法，会把 `run()` 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。

**调用 `start()` 方法方可启动线程并使线程进入就绪状态，直接执行 `run()` 方法的话不会以多线程的方式执行。**

### synchronized 关键字

#### 说一说自己对于 synchronized 关键字的了解

**`synchronized` 关键字解决的是多个线程之间访问资源的同步性，`synchronized`关键字可以保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行。**

另外，在 Java 早期版本中，`synchronized` 属于**重量级锁**，效率低下。

**为什么呢？**

因为监视器锁（monitor）是依赖于底层的操作系统的 `Mutex Lock` 来实现的，Java 的线程是映射到操作系统的原生线程之上的。如果要挂起或者唤醒一个线程，都需要操作系统帮忙完成，而操作系统实现线程之间的切换时需要从用户态转换到内核态，这个状态之间的转换需要相对比较长的时间，时间成本相对较高。

庆幸的是在 Java 6 之后 Java 官方对从 JVM 层面对 `synchronized` 较大优化，所以现在的 `synchronized` 锁效率也优化得很不错了。JDK1.6 对锁的实现引入了大量的优化，如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销。

所以，你会发现目前的话，不论是各种开源框架还是 JDK 源码都大量使用了 `synchronized` 关键字。

#### 说说自己是怎么使用 synchronized 关键字

**synchronized 关键字最主要的三种使用方式：**

- **修饰实例方法:** 作用于当前对象实例加锁，进入同步代码前要获得 **当前对象实例的锁**

- **修饰静态方法:** 也就是给当前类加锁，会作用于类的所有对象实例 ，进入同步代码前要获得 **当前 class 的锁**。

>  **因为访问静态 `synchronized` 方法占用的锁是当前类的锁，而访问非静态 `synchronized` 方法占用的锁是当前实例对象锁**。

- **修饰代码块** ：指定加锁对象，对给定对象/类加锁。`synchronized(this|object)` 表示进入同步代码库前要获得**给定对象的锁**。`synchronized(类.class)` 表示进入同步代码前要获得 **当前 class 的锁**。

## 设计模式

### 静态代理+JDK

### CGLIB动态代理

## MySQL

### MySQL中有哪几种锁？

1、表级锁： 开销小， 加锁快； 不会出现死锁； 锁定粒度大， 发生锁冲突的概率最高， 并发度最低。

2、行级锁： 开销大， 加锁慢； 会出现死锁； 锁定粒度最小， 发生锁冲突的概率最低， 并发度也最高。

3、页面锁： 开销和加锁时间界于表锁和行锁之间； 会出现死锁； 锁定粒度界于表锁和行锁之间， 并发度一般。



### MySQL 中有哪些不同的表格？

共有 5 种类型的表格：MyISAM、Heap、Merge、INNODB、ISAM

### 简述在MySQL 数据库中 MyISAM 和InnoDB 的区别

MySQL 5.5 之前，MyISAM 引擎是 MySQL 的默认存储引擎，可谓是风光一时。

虽然，MyISAM 的性能还行，各种特性也还不错（比如全文索引、压缩、空间函数等）。但是，MyISAM 不支持事务和行级锁，而且最大的缺陷就是崩溃后无法安全恢复。

5.5 版本之后，MySQL 引入了 InnoDB（事务性数据库引擎），MySQL 5.5 版本后默认的存储引擎为 InnoDB。小伙子，一定要记好这个 InnoDB ，你每次使用 MySQL 数据库都是用的这个存储引擎吧？

简单对比一下两者：

**1.是否支持行级锁**

MyISAM 只有表级锁(table-level locking)，而 InnoDB 支持行级锁(row-level locking)和表级锁,默认为行级锁。

也就说，MyISAM 一锁就是锁住了整张表，这在并发写的情况下是多么滴憨憨啊！这也是为什么 InnoDB 在并发写的时候，性能更牛皮了！

**2.是否支持事务**

MyISAM 不提供事务支持。

InnoDB 支持 ACID 的事务， 支持事务的四种隔离级别，具有提交(commit)和回滚(rollback)事务的能力。

> 四种隔离级别：读未提交，读已提交，可重复读（MySQL默认，而且解决了幻读问题），串行化。

**3.是否支持外键**

MyISAM 不支持，而 InnoDB 支持。

🌈 拓展一下：

一般我们也是不建议在数据库层面使用外键的，应用层面可以解决。不过，这样会对数据的一致性造成威胁。具体要不要使用外键还是要根据你的项目来决定。

**4.是否支持数据库异常崩溃后的安全恢复**

MyISAM 不支持，而 InnoDB 支持。

使用 InnoDB 的数据库在异常崩溃后，数据库重新启动的时候会保证数据库恢复到崩溃前的状态。这个恢复的过程依赖于 `redo log` 。

🌈 拓展一下：

- MySQL InnoDB 引擎使用 **redo log(重做日志)** 保证事务的**持久性**，使用 **undo log(回滚日志)** 来保证事务的**原子性**。
- MySQL InnoDB 引擎通过 **锁机制**、**MVCC** 等手段来保证事务的隔离性（ 默认支持的隔离级别是 **`REPEATABLE-READ`** ）。
- 保证了事务的持久性、原子性、隔离性之后，一致性才能得到保障。

**5.是否支持 MVCC（多版本并发控制）**

MyISAM 不支持，而 InnoDB 支持。

讲真，这个对比有点废话，毕竟 MyISAM 连行级锁都不支持。

**MVCC 可以看作是行级锁的一个升级，可以有效减少加锁操作，提供性能。**

> InnoDB实现的MVCC是乐观锁，在可重复读隔离级别下，它通过乐观并发控制解决了该隔离级别所不能解决的幻读，但是前提是这些都得依托于事务的封装。

### 锁机制与 InnoDB 锁算法

**MyISAM 和 InnoDB 存储引擎使用的锁：**

- MyISAM 采用表级锁(table-level locking)。
- InnoDB 支持行级锁(row-level locking)和表级锁,默认为行级锁

**表级锁和行级锁对比：**

- **表级锁：** MySQL 中锁定 **粒度最大** 的一种锁，对当前操作的整张表加锁，实现简单，资源消耗也比较少，加锁快，不会出现死锁。其锁定粒度最大，触发锁冲突的概率最高，并发度最低，MyISAM 和 InnoDB 引擎都支持表级锁。
- **行级锁：** MySQL 中锁定 **粒度最小** 的一种锁，只针对当前操作的行进行加锁。 行级锁能大大减少数据库操作的冲突。其加锁粒度最小，并发度高，但加锁的开销也最大，加锁慢，会出现死锁。

**InnoDB 存储引擎的锁的算法有三种：**

- Record lock：记录锁，单个行记录上的锁
- Gap lock：间隙锁，锁定一个范围，不包括记录本身
- Next-key lock：record+gap临键锁，锁定一个范围，包含记录本身

### MySQL 中InnoDB 支持的四种事务隔离级别名称，以及逐级之间的区别？

SQL 标准定义的四个隔离级别为：

1、read uncommited ： 读到未提交数据

2、read committed ： 脏读， 不可重复读

3、repeatable read： 可重读

4、serializable ： 串行化

### 隔离所导致的问题

* 脏读：读取到另一个事务未提交的数据的现象
* 不可重复读：前后两次读取的数据不一致的现象
* 幻读：幻读和不可重复读有些类似，但是**幻读强调的是集合的增减，而不是单条数据的更新。**
* 第一类丢失更新（回滚丢失）：事务A和事务B都对数据进行更新，但是事务A由于某种原因事务回滚了，把已经提交的事务B的更新数据给覆盖了。这种现象就是第一类更新丢失。
* 第二类丢失更新（更新丢失）：其实跟第一类更新丢失有点类似，也是两个事务同时对数据进行更新，但是事务A的更新把已提交的事务B的更新数据给覆盖了。这种现象就是第二类更新丢失。



## Redis

## Mybatis

## Spring

## SpringMVC

## SpringBoot

## SpringCloud

## Dubbo

## Zookeeper

## 计算机操作系统

## 计算机网络

## 算法题：剑指offer

