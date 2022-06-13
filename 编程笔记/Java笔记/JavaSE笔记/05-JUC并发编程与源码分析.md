# JUC并发编程与源码分析

## 线程基础知识

### 什么是JUC

java.util.concurrent在并发编程中使用的工具包

- `java.util.concurrent`
- `java.util.concurrent.atomic`
- `java.util.concurrent.locks`

<img src="../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/51083f7652764efd9b58758bf684f2f8.png" style="zoom:67%;" />

### 为什么要学好多线程

* 提高程序性能，高并发系统
* 提高程序吞吐量，异步+回调等生成需求

### start线程解读

```java
public class ThreadBaseDemo {
    public static void main(String[] args) {
        Thread t1 = new Thread(()->{

        },"t1");
        t1.start();
    }
}
```

查看start方法：

```java
public synchronized void start() {
    /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
    if (threadStatus != 0)
        throw new IllegalThreadStateException();

    /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
    group.add(this);

    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
            /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
        }
    }
}
// 调用第三方模块实现的
private native void start0();
```

native调用了本地方法，我们可以通过下载官网OpenJDK查看其源码

- thread.c

java线程是通过start的方法启动执行的，主要内容在native方法start0中Openjdk的写JNI一般是一一对应的，Thread.java对应的就是``Thread.c``。start0其实就是``JVM_StartThread``。此时查看源代码可以看到在jvm.h中找到了声明，jvm.cpp中有实现。

![](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/de16a11f0fb44c45b72fa7b26040e597.png)

- jvm.cpp

![在这里插入图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/2b1d3dd9dde4414b85cd42c71377ae16.png)

- thread.cpp：终于在这里调用了**操作系统的线程启动**，`os::start_thread（thread);`

![在这里插入图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/2b5576a3b91a428e87bb3bdc00472320.png)

### Java多线程相关概念

#### 锁

这里的锁指的是synchronized，后面会学习到。

#### 并发与并行

##### 并发

- 是在同一实体上的多个事件
- 是在同一台处理器上“同时”处理多个任务
- **同一时刻，其实是只有一个事件在发生**。

##### 并行

- 是在不同实体上的多个事件
- 是在多台处理器上同时处理多个任务
- **同一时刻，大家都真的在做事情，你做你的，我做我的**（需要多核）

<img src="../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/2f14deefe3854cb0b86c71c41a12306f.png" alt="在这里插入图片描述" style="zoom:50%;" />

#### 进程

系统中运行的一个应用程序就是一个进程，每一个进程都有它自己的内存空间和系统资源。

#### 线程

也被称为轻量级进程，在同一个进程内基本会有一个或多个线程，是大多数操作系统进行调度的基本单元。

#### 管程

- Monitor（监视器），也就是我们平时说的锁。

- Monitor其实是一种同步机制，他的义务是保证（同一时间）只有一个线程可以访问被保护的数据和代码。

- JVM中同步是基于进入和退出监视器对象(Monitor,管程对象)来实现的，每个对象实例都会有一个Monitor对象，

- Monitor对象会和Java对象一同创建并销毁，它底层是由C++语言来实现的。

### 用户线程和守护线程

Java线程分为用户线程和守护线程

* 用户线程：是系统的工作线程，它会完成这个程序需要完成的业务操作
* 守护线程：是一种特殊的线程，为其他线程服务的，在后台默默地完成一些系统性的服务，比如垃圾回收线程。

- 线程的daemon属性为 
  - true表示是守护线程
  - false表示是用户线程。

```java
public class DaemonDemo
{
    public static void main(String[] args)
    {
        Thread t1 = new Thread(() -> {
            System.out.println(Thread.currentThread().getName()+"\t 开始运行，"+(Thread.currentThread().isDaemon() ? "守护线程":"用户线程"));
            while (true) {

            }
        }, "t1");
        //线程的daemon属性为true表示是守护线程，false表示是用户线程
        //---------------------------------------------
        t1.setDaemon(true); 
        //-----------------------------------------------
        t1.start();
        //3秒钟后主线程再运行
        try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }

        System.out.println("----------main线程运行完毕");
    }
}
```

- 守护线程作为一个服务线程，没有服务对象就没有必要继续运行了，如果用户线程全部结束了，意味着程序需要完成的业务操作已经结束了，系统可退出了。假如当系统只剩下守护线程的时候，java虚拟机会自动退出。

- setDaemon(true)方法必须在start()之前设置，否则报IIIegalThreadStateException异常。

## CompletableFuture

### Future接口

#### 什么是Future？

Future接口(**FutureTask实现类**)定义了操作**异步任务执行一些方法**，如获取异步任务的执行结果、取消任务的执行、判断任务是否被取消、判断任务执行是否完毕等。（异步：可以被叫停，可以被取消）

一句话：**Future接口可以为主线程开一个分支任务，专门为主线程处理耗时和费力的复杂业务。**

#### Future能干什么

比如主线程让一个子线程去执行任务，子线程可能比较耗时，启动子线程开始执行任务后，主线程就去做其他事情了，过了一会才去获取子任务的执行结果。老师在上课，但是口渴，于是让班长这个线程去买水，自己可以继续上课，实现了异步任务。

**目的：异步多线程任务执行且有返回结果，三个特点：多线程/有返回/异步任务（班长作为老师去买水作为新启动的异步多线程任务且买到水有结果返回）**

#### FutureTask实现类

- FutureTask(实现了x接口，x接口又继承了a和v接口)
  - 在源码可以看到，他既继承了`RunnableFuture`接口，也在构造方法中实现了`Callable`接口（有返回值、可抛出异常）和`Runnable`接口

![image-20220609211814836](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220609211814836.png)

![image-20220609211843251](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220609211843251.png)

```java
public class CompletableFutureDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<String> task1 = new FutureTask<>(new MyThread2());
        FutureTask task2 = new FutureTask(new MyThread(),null);

        Thread t1 = new Thread(task1,"t1");
        Thread t2 = new Thread(task2,"t1");
        t1.start();

        // 使用get获取返回值
        String value = task1.get();
        System.out.println(value);
    }
}

class MyThread implements Runnable{

    @Override
    public void run() {
    }
}

class MyThread2 implements Callable<String> {

    @Override
    public String call() throws Exception {
        System.out.println("-----come in call() ----异步执行");
        return "hello Callable 返回值";
    }
}
```

#### Future优点

- **future**+**线程池**异步多线程任务配合，能显著提高程序的执行效率。
- 方案一，3个任务1个main线程处理，大概**1541ms**

```java
public class FutureThreadPoolDemo {
    public static void main(String[] args) {
        // 3个任务1个main线程处理，大概1541ms

        long startTime = System.currentTimeMillis();

        // 暂停毫秒
        try {TimeUnit.MILLISECONDS.sleep(500);} catch (InterruptedException e) {e.printStackTrace();}
        try {TimeUnit.MILLISECONDS.sleep(500);} catch (InterruptedException e) {e.printStackTrace();}
        try {TimeUnit.MILLISECONDS.sleep(500);} catch (InterruptedException e) {e.printStackTrace();}

        long endTime = System.currentTimeMillis();

        System.out.println("costTime："+(endTime - startTime) + "ms");

        System.out.println(Thread.currentThread().getName());
    }
}
```

* 方案二，3个任务3个线程，利用线程池（假如每次new一个Thread，太浪费资源，会有GC这些工作），大概**41毫秒**。

```java
public class FutureThreadPoolDemo {
    public static void main(String[] args) {

        long startTime = System.currentTimeMillis();

        ExecutorService threadPool = Executors.newFixedThreadPool(3);

        // 3个任务3个线程，利用线程池
        FutureTask<String> task1 = new FutureTask<>(()->{
            try {TimeUnit.MILLISECONDS.sleep(500);} catch (InterruptedException e) {e.printStackTrace();}
            return "task1 over";
        });

        threadPool.submit(task1);

        FutureTask<String> task2 = new FutureTask<>(()->{
            try {TimeUnit.MILLISECONDS.sleep(500);} catch (InterruptedException e) {e.printStackTrace();}
            return "task2 over";
        });

        threadPool.submit(task2);

        FutureTask<String> task3 = new FutureTask<>(()->{
            try {TimeUnit.MILLISECONDS.sleep(500);} catch (InterruptedException e) {e.printStackTrace();}
            return "task3 over";
        });

        threadPool.submit(task3);

        threadPool.shutdown();

        long endTime = System.currentTimeMillis();

        System.out.println("costTime："+(endTime - startTime) + "ms");

        System.out.println(Thread.currentThread().getName());
    }
}
```

#### Future缺点

##### get()阻塞

一旦调用get()方法，不管是否计算完成，都会导致阻塞（所以一般get方法放到最后）

```java
public class FutureAPIDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<String> task = new FutureTask<>(()->{
            System.out.println(Thread.currentThread().getName() + "\n");
            try {TimeUnit.SECONDS.sleep(5);} catch (InterruptedException e) {e.printStackTrace();}
            return "task";
        });

        Thread thread = new Thread(task);
        thread.start();

        String value = task.get();
        // String value = task.get(3,TimeUnit.SECONDS); // 设置超过等待时间
        System.out.println(value);
    }
}
```

##### isDone()轮询

利用`if(futureTask.isDone())`的方式使得FutureTask在结束之后才get()，但是也会消耗cpu。

```java
public class FutureAPIDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<String> task = new FutureTask<>(()->{
            System.out.println(Thread.currentThread().getName() + "\n");
            try {TimeUnit.SECONDS.sleep(5);} catch (InterruptedException e) {e.printStackTrace();}
            return "task";
        });

        Thread thread = new Thread(task);
        thread.start();

//        String value = task.get();
//        System.out.println(value);

        while (true){
            if(task.isDone()){
                System.out.println(task.get());
                break;
            }else{
                // 暂停毫秒
                try {TimeUnit.MILLISECONDS.sleep(500);} catch (InterruptedException e) {e.printStackTrace();}
                System.out.println("正在处理中");
            }
        }
    }
}
```

#### future应用现状

- 对于简单的业务场景使用Future完全OK

  - 回调通知

    - 前面的isDone()方法耗费cpu资源，一般应该还是利用回调函数，在Future结束时自动调用该回调函数。应对Future的完成时间，完成了可以告诉我，也就是我们的回调通知
  - 创建异步任务
    - Future+线程池配合
  - 多个任务前后依赖可以组合处理（水煮鱼）
    - 想将多个异步任务的计算结果组合起来，后一个异步任务的计算结果需要前一个异步任务的值，将两个或多个异步计算合成一个异步计算，这几个异步计算相互独立，同时后面这个又依赖前一个处理的结果

    - 比如买鱼-加料-烹饪

  - 对计算速度选最快完成的（并返回结果）

    - 当Future集合中某个任务最快结束时，返回结果，返回第一名处理结果。

### CompletableFuture

#### CompletableFuture基本介绍

**阻塞的方式和异步编程的设计理念相违背，而轮询的方式会消耗无畏的CPU资源。**因此，JDK8设计出CompletableFuture

```java
public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
    ...
}
```

![image-20220610122149352](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220610122149352.png)

- 在Java 8中， CompletableFuture提供了非常强大的Future的扩展功能， 可以帮助我们简化异步编程的复杂性， 并且提供了函数式编程的能力， 可以通过回调的方式处理计算结果， 也提供了转换和组合CompletableFuture的方法。
- 它可能代表一个明确完成的Future， 也有可能代表一个完成阶段(Completion Stage) ， 它支持在计算完成以后触发一些函数或执行某些动作。
- 它实现了`Future`和`CompletionStage`接口

#### CompletionStage基本介绍

- `CompletionStage`代表异步计算过程中的**某一个阶段**， 一个阶段完成以后可能会触发另外一个阶段
- 一个阶段的计算执行可以是一个Function， Consumer或者Runnable。比如：``stage.thenApply(x->square(x)).then Accept(x->System.out.print(x)).thenRun()->System.out.println() )``，一个阶段的执行可能是被单个阶段的完成触发，也可能是由多个阶段一起触发。

#### 四个核心静态API方法

- 利用核心的四个静态方法创建一个异步操作 | 不建议用new

- 关键就是 |有没有返回值|是否用了线程池|

- 参数说明：

  - 没有指定Executor的方法，直接使用默认的ForkJoinPool.commPool()作为它的线程池执行异步代码。

  - 如果指定线程池，则使用我们定义的或者特别指定的线程池执行异步代码

##### runAsync无返回值

###### runAsync

```java
public static CompletableFuture<Void> runAsync(Runnable runnable)
```

实例：

```java
public class CompletableFutureBuildDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName());
            // 暂停几秒钟线程
            try {TimeUnit.SECONDS.sleep(1);} catch (InterruptedException e) {e.printStackTrace();}
        });

        System.out.println(completableFuture.get());
    }
}
```

###### runAsync+线程池

没有指定线程池，会使用默认线程池。

```java
public static CompletableFuture<Void> runAsync(Runnable runnable,
                                               Executor executor)
```

实例：

```java
public class CompletableFutureBuildDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {

        ExecutorService threadPool = Executors.newFixedThreadPool(3);

        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName());
            // 暂停几秒钟线程
            try {TimeUnit.SECONDS.sleep(1);} catch (InterruptedException e) {e.printStackTrace();}
        },threadPool);

        System.out.println(completableFuture.get());
        threadPool.shutdown();
    }
}
```

##### supplyAsync有返回值

###### supplyAsync

```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier)
```

实例：

````java
public class CompletableFutureBuildDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> {
            // 暂停几秒钟线程
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "completableFuture";
        });

        System.out.println(completableFuture.get());
    }
}
````

###### supplyAsync+线程池

```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier,Executor executor)
```

实例：

```java
public class CompletableFutureBuildDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService threadPool = Executors.newFixedThreadPool(3);

        CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> {
            // 暂停几秒钟线程
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "completableFuture";
        },threadPool);

        System.out.println(completableFuture.get());
        threadPool.shutdown();
    }
}
```

#### 通用异步编程

##### 基本功能

`CompletableFuture`可以完成`Future`的功能

```java
public class CompletableFutureUseDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName());
            int result = ThreadLocalRandom.current().nextInt(10);

            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("1秒钟后出结果：" + result);
            return result;
        });

        System.out.println(Thread.currentThread().getName() + "线程先去忙其他任务");

        System.out.println(completableFuture.get());
    }
}
```

##### 减少阻塞和轮询whenComplete

`CompletableFuture`通过`whenComplete`来**减少阻塞和轮询**（自动回调）。

注意不要让主线程死亡，不然用户线程也会跟随死亡。

```java
public class CompletableFutureUseDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {

        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName());
            int result = ThreadLocalRandom.current().nextInt(10);

            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("1秒钟后出结果：" + result);
            return result;
        }).whenComplete((v,e)->{
            if(e == null){
                System.out.println("计算完成:"+v);
            }
        }).exceptionally(e -> {
            e.printStackTrace();
            System.out.println("异常情况");
            return null;
        });

        //线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭：暂停3秒钟线程
        System.out.println(Thread.currentThread().getName()+"线程先去忙其他任务");
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

#### CompletableFuture优点总结

- 异步任务结束时，会**自动回调**某个对象的方法；
- 主线程设置好毁掉后，不再关心异步任务的执行，异步任务之间可以顺序执行
- 异步任务出错时，会自动回调某个对象的方法。

### Completable案例精讲-电商网站的比价需求

#### 面试题

![image-20220610143342290](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220610143342290.png)

#### 编程必备技能准备

##### 函数式接口

###### 函数式接口定义

任何**接口**，如果只包含**唯一一个**抽象方法，那么它就是一个**函数式接口**。对于函数式接口，我们可以通过**lambda表达式**来创建该接口的对象。

```java
public interface Runnable{
  public abstract void run();
}
```

###### 常见函数式接口

###### Runnable

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

###### Function-功能性函数式接口

```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```

###### Consumer-消费性函数式接口

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}
```

###### Supplier-供给性函数式接口

```java
@FunctionalInterface
public interface Supplier<T> {

    /**
     * Gets a result.
     *
     * @return a result
     */
    T get();
}
```

###### Biconsumer-消费性函数式接口

(Bi代表两个的意思，我们要传入两个参数，在上面的案例中是v和e)

```java
@FunctionalInterface
public interface BiConsumer<T, U> {
    void accept(T t, U u);

}
```

| 函数式接口名称 | 方法名称 | 参数     | 返回值   |
| -------------- | -------- | -------- | -------- |
| Runnable       | run      | 无参数   | 无返回值 |
| Function       | apply    | 1个参数  | 有返回值 |
| Consume        | accept   | 1个参数  | 无返回值 |
| Supplier       | get      | 没有参数 | 有返回值 |
| Biconsumer     | accept   | 2个参数  | 无返回值 |

##### 链式编程

```java
public class Chain {
    public static void main(String[] args) {
        //-------------------老式写法------------
        //        Student student = new Student();
        //        student.setId(1);
        //        student.setMajor("cs");
        //        student.setName("小卡");
        new Student().setId(1).setName("大卡").setMajor("cs");
    }

}

@NoArgsConstructor
@AllArgsConstructor
@Data
@Accessors(chain = true)//开启链式编程
class Student{
    private int id;
    private String name;
    private String major;
}
```

##### join和get对比

- 功能几乎一样，区别在于编码时是否需要抛出异常
  - get()方法需要抛出异常
  - join()方法不需要抛出异常

````java
public class Chain {
    public static void main(String[] args) throws ExecutionException, InterruptedException {//抛出异常
        CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> {
            return "hello 12345";
        });
        System.out.println(completableFuture.get());
    }

}

public class Chain {
    public static void main(String[] args)  {//不需要抛出异常
        CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> {
            return "hello 12345";
        });
        System.out.println(completableFuture.join());
    }
}
````

#### 实战精讲-比价网站case

##### 需求

```
1需求说明
1.1同一款产品，同时搜索出同款产品在各大电商平台的售价；
1.2同一款产品，同时搜索出本产品在同一个电商平台下，各个入驻卖家售价是多少

2输出返回：
出来结果希望是同款产品的在不同地方的价格清单列表， 返回一个List<String>
《mysql》in jd price is 88.05
《mysql》in dang dang price is 86.11
《mysql》in tao bao price is 90.43

3解决方案，比对同一个商品在各个平台上的价格，要求获得一个清单列表
1   stepbystep   ， 按部就班， 查完京东查淘宝， 查完淘宝查天猫......
2   all in       ，万箭齐发，一口气多线程异步任务同时查询。。。
```

##### 基本框架

相当于是一个一个按部就班

```java
public class Case {
    static List<NetMall> list = Arrays.asList(
            new NetMall("jd"),
            new NetMall("dangdang"),
            new NetMall("taobao")
    );

    public static List<String> getPrice(List<NetMall> list, String productName){
        return list
                .stream() //----流式计算做了映射（利用map），希望出来的是有格式的字符串（利用String.format）,%是占位符
                .map(netMall -> String.format(productName + " in %s price is %.2f",
                        netMall.getNetMallName(),//第一个%
                        netMall.calcPrice(productName))).collect(Collectors.toList());//第二个%
    }

    public static void main(String[] args) {
        long startTime = System.currentTimeMillis();
        List<String> list1 = getPrice(list, "mysql");
        for(String element:list1){
            System.out.println(element);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("---当前操作花费时间----costTime:"+(endTime-startTime)+"毫秒");
    }
}

class NetMall{
    @Getter
    private String netMallName;

    public NetMall(String netMallName){
        this.netMallName = netMallName;
    }

    public double calcPrice(String productName){
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return ThreadLocalRandom.current().nextDouble() * 2 + productName.charAt(0);//用这句话来模拟价格
    }
}

// mysql in jd price is 109.80
// mysql in dangdang price is 110.86
// mysql in taobao price is 110.63
// ---当前操作花费时间----costTime:3069毫秒
```

##### 从功能到性能：利用CompletableFuture

- 这里是利用异步线程，万箭齐发
- 此处用了两步**流式编程**。
- 性能差距巨大

````java
public static List<String> getPriceByCompletableFuture(List<NetMall> list,String productName){

    return list.stream().map(netMall -> CompletableFuture.supplyAsync(()-> String.format(productName + " in %s price is %.2f",
                                                                                         netMall.getNetMallName(),
                                                                                         netMall.calcPrice(productName))))
        .collect(Collectors.toList())
        .stream()
        .map(CompletableFuture::join)
        .collect(Collectors.toList());
}

// mysql in jd price is 110.35
// mysql in dangdang price is 109.25
// mysql in taobao price is 110.63
// ---当前操作花费时间----costTime:1063毫秒
````

### Completable常用API

- getNow调用的时候如果计算完了，就拿取这个计算完的值；否则就拿**默认值**

#### 获得结果和触发计算

##### 获取结果

- `public T get()` 不见不散，容易阻塞
- `public T get(long timeout,TimeUnit unit) `过时不候，超过时间会爆异常
- `public T join()` 类似于get()，区别在于是否需要抛出异常
- `public T getNow(T valueIfAbsent)`
  - 没有计算完成的情况下，给一个替代结果

  - 立即获取结果不阻塞

    - 计算完，返回计算完成后的结果

    - 没算完，返回设定的valueAbsent(直接返回了默认值xxx)

##### 主动触发计算

public boolean complete(T value) 是否立即打断get()方法返回括号值

(执行要2s，等待只有1s，所以还没执行完就被打断了。返回true表示打断了获取这个过程，直接返回了备胎值complete；如果没打断，返回false 和原来的abc)

```java
public class CompletableFutureAPIDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> completableFuture = CompletableFuture.supplyAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(1);//执行需要1秒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "abc";
        });

        //        try {
        //            TimeUnit.SECONDS.sleep(2);//等待需要1秒
        //        } catch (InterruptedException e) {
        //            e.printStackTrace();
        //        }
        //         System.out.println(completableFuture.getNow("xxx"));//执2-等1 返回xxx
        System.out.println(completableFuture.complete("completeValue")+"\t"+completableFuture.get());//执2-等1 返回true+默认值completeValue
    }
}
```

#### 对计算结果进行处理

##### thenApply

- `thenApply `计算结果存在在依赖关系，使得线程串行化。因为依赖关系，所以一旦有异常，直接叫停。

```java
public class CompletableFutureDemo2 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //当一个线程依赖另一个线程时用 thenApply 方法来把这两个线程串行化,
        CompletableFuture.supplyAsync(() -> {
            //暂停几秒钟线程
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("111");
            return 1024;
        }).thenApply(f -> {
            System.out.println("222");
            return f + 1;
        }).thenApply(f -> {
            //int age = 10/0; // 异常情况：那步出错就停在那步。
            System.out.println("333");
            return f + 1;
        }).whenCompleteAsync((v, e) -> {
            System.out.println("*****v: " + v);
        }).exceptionally(e -> {
            e.printStackTrace();
            return null;
        });

        System.out.println("-----主线程结束，END");

        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

##### handle

`handle `类似于thenApply，但是有异常的话**仍然**可以往下走一步。

```java
public class CompletableFutureDemo2 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //当一个线程依赖另一个线程时用 handle 方法来把这两个线程串行化,
        // 异常情况：有异常也可以往下一步走，根据带的异常参数可以进一步处理
        CompletableFuture.supplyAsync(() -> {
            //暂停几秒钟线程
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("111");
            return 1024;
        }).handle((f, e) -> {
            int age = 10 / 0;//异常语句
            System.out.println("222");
            return f + 1;
        }).handle((f, e) -> {
            System.out.println("333");
            return f + 1;
        }).whenCompleteAsync((v, e) -> {
            System.out.println("*****v: " + v);
        }).exceptionally(e -> {
            e.printStackTrace();
            return null;
        });

        System.out.println("-----主线程结束，END");

        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

> 一般用thenApply

#### 对计算结果进行消费

接收任务的处理结果，并**消费处理，无返回结果**|**消费型函数式接口**，之前的是Function

##### thenAccept

```java
public static void main(String[] args) throws ExecutionException, InterruptedException{
    CompletableFuture.supplyAsync(() -> {
        return 1;
    }).thenApply(f -> {
        return f + 2;
    }).thenApply(f -> {
        return f + 3;
    }).thenApply(f -> {
        return f + 4;
    }).thenAccept(r -> System.out.println(r));
}
//10
//消费一下，直接得到10
```

##### code补充：Code之任务之间的顺序执行

- thenRun

  - thenRun(Runnable runnable)

  - 任务A执行完执行B，并且B不需要A的结果

- thenAccept

  - thenAccept(Consumer action)

  - 任务A执行完执行B，B需要A的结果，但是任务B无返回值

- thenApply

  - thenApply(Function fn)

  - 任务A执行完执行B，B需要A的结果，同时任务B有返回值

```java
 
System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenRun(() -> {}).join());
//null 

System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenAccept(resultA -> {}).join());
//resultA打印出来的 null因为没有返回值

System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenApply(resultA -> resultA + " resultB").join());
//resultAresultB 返回值
```

#### **CompleteFuture和线程池**

- 上面的几个方法都有普通版本和**后面加Async**的版本

- 以`thenRun`和`thenRunAsync`为例，有什么区别？

- 先看结论

  - **没有传入自定义线程池，都用默认线程池ForkJoinPool**

  - 传入了一个自定义线程池如果你执行第一个任务的时候，传入了一个自定义线程池

    - **调用thenRun方法执行第二个任务的时候，则第二个任务和第一个任务是用同一个线程池**(thenRun方法使用的是前面使用的线程池)

    - **调用thenRunAsync执行第二个任务的时候，则第一个任务使用的是你自己传入的线程池，第二个任务使用的是ForkJoin线程池**

  - 也有可能处理太快，**系统优化切换原则**，直接使用main线程处理（把sleep去掉）

```java
public class CompletableFutureWithThreadPoolDemo {
    public static void main(String[] args) {

        ExecutorService threadPool = Executors.newFixedThreadPool(5);

        try {
            CompletableFuture<Void> completableFuture = CompletableFuture.supplyAsync(() -> {
                try {
                    TimeUnit.MILLISECONDS.sleep(20);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("1号任务" + "\t" + Thread.currentThread().getName());
                return "abcd";
            },threadPool).thenRunAsync(() -> {
                try {
                    TimeUnit.MILLISECONDS.sleep(20);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("2号任务" + "\t" + Thread.currentThread().getName());
            }).thenRunAsync(() -> {
                try {
                    TimeUnit.MILLISECONDS.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("3号任务" + "\t" + Thread.currentThread().getName());
            }).thenRunAsync(() -> {
                try {
                    TimeUnit.MILLISECONDS.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("4号任务" + "\t" + Thread.currentThread().getName());
            });
            System.out.println(completableFuture.get(2L,TimeUnit.SECONDS));
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            threadPool.shutdown();
        }
    }
}
```

#### 对计算速度进行选用

`applyToEither`方法，谁快谁优先

```java
public class CompletableFutureFastDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException
    {
        CompletableFuture<String> play1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            //暂停几秒钟线程
            try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }
            return "play1 ";
        });

        CompletableFuture<String> play2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            return "play2";
        });

        CompletableFuture<String> thenCombineResult = play1.applyToEither(play2, f -> {//对计算速度进行选用
            return f + " is winner";
        });

        System.out.println(Thread.currentThread().getName() + "\t" + thenCombineResult.get());
    }
}
```

#### 对计算结果进行合并

`thenCombine` 合并：两个CompletionStage任务都完成后，最终能把两个任务的结果一起交给thenCOmbine来处理

> 先完成的先等着，等待其它分支任务

```java
public class CompletableFutureCombineDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> completableFuture1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            return 10;
        });

        CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            return 20;
        });

        // 合并
        CompletableFuture<Integer> thenCombineResult = completableFuture1.thenCombine(completableFuture2, (x, y) -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            return x + y;
        });

        System.out.println(thenCombineResult.get());
    }
}
```

* 合并版本

```java
public class CompletableFutureCombineDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Integer> thenCombineResult = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 1");
            return 10;
        }).thenCombine(CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 2");
            return 20;
        }), (x, y) -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 3");
            return x + y;
        }).thenCombine(CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 4");
            return 30;
        }), (a, b) -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in 5");
            return a + b;
        });
        System.out.println("-----主线程结束，END");
        System.out.println(thenCombineResult.get());


        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        try {
            TimeUnit.SECONDS.sleep(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

## 说说Java“锁”事

### 大厂面试题

#### 一、Synchronized相关问题

1.Synchronized用过吗， 其原理是什么?
2.你刚才提到获取对象的锁，这个锁到底是什么?如何确定对象的锁?
3.什么是可重入性，为什么说Synchronized是可重入锁?
4.JVM对Java的原生锁做了哪些优化?
5.为什么说Synchronized是非公平锁?
6.什么是锁消除和锁粗化?
7.为什么说Synchronized是个悲观锁?乐观锁的实现原理又是什么?什么是CAS， 它有
8.乐观锁一定就是好的吗?

#### 二、可重入锁Reentrant Lock及其他显式锁相关问题

1.跟Synchronized相比，可重入锁Reentrant Lock其实现原理有什么不同?
2.那么请谈谈AQS框架是怎么回事儿?
3.请尽可能详尽地对比下Synchronized和Reentrant Lock的异同。
4.Reentrant Lock是如何实现可重入性的?

#### 其他问题

1， 你怎么理解iava多线程的?怎么处理并发?线程池有那几个核心参数?
2， Java加锁有哪几种锁?
3， 简单说说lock?
4， hashmap的实现原理?hash冲突怎么解决?为什么使用红黑树?
5， spring里面都使用了那些设计模式?循环依赖怎么解决?
6，项目中那个地方用了countdown lanch， 怎么使用的?

### 乐观锁和悲观锁

#### 悲观锁

悲观锁认为自己在使用数据的时候一定有别的线程来修改数据，因此在获取数据的时候会先加锁，确保数据不会被别的线程修改。

悲观锁的实现方式

- `synchronized`关键字
- `Lock`的实现类都是悲观锁


适合写操作多的场景，先加锁可以保证写操作时数据正确。显示的锁定之后再操作同步资源。

```java
public synchronized void m1(){
    //加锁后的业务逻辑......
}

// 保证多个线程使用的是同一个lock对象的前提下
ReentrantLock lock = new ReentrantLock();
public void m2(){
    lock.lock();
    try {
        // 操作同步资源
    }finally {
        lock.unlock();
    }
}
```

#### 乐观锁

**乐观锁认为自己在使用数据时不会有别的线程修改数据，所以不会添加锁**，只是在更新数据的时候去判断之前有没有别的线程更新了这个数据。如果这个数据没有被更新，当前线程将自己修改的数据成功写入。如果数据已经被其他线程更新，则根据不同的实现方式执行不同的操作。

乐观锁的实现方式

- 版本号机制Version。（只要有人提交了就会修改版本号，可以解决ABA问题）

  - ABA问题：再CAS中想读取一个值A，想把值A变为C，不能保证读取时的A就是赋值时的A，中间可能有个线程将A变为B再变为A。

  - **解决方法：Juc包提供了一个AtomicStampedReference，原子更新带有版本号的引用类型，通过控制版本值的变化来解决ABA问题。**
- 最常采用的是CAS算法，Java原子类中的递增操作就通过CAS自旋实现的。

适合读操作多的场景，不加锁的性能特点能够使其操作的性能大幅提升。

```java
// 保证多个线程使用的是同一个AtomicInteger
private AtomicInteger atomicInteger = new AtomicInteger();
atomicInteger.incrementAndGet();
```

### 从8种情况演示锁的案例，看看锁到底是什么

#### 8锁案例

##### 概述

阿里巴巴代码规范

- 【强制】高并发 时，同步调用应该去考量锁的性能损耗。能用无锁数据结构，就不要用锁；能锁区块，就不要锁整个方法体；能用对象锁，就不要用类锁。
- 说明：尽可能使加锁的代码块工作量尽可能的小，避免在锁代码块中调用 RPC 方法。

##### 8锁演示

###### **1. 两个同步方法，一个对象调用**，标准情况下，两个线程先发邮件还是短信？发邮件

**synchronized 锁的是方法的调用者，也就是对象锁。两个方法持有的是同一把锁，因此谁先拿到锁谁先执行**  。

```java
public class Lock8Demo {
    public static void main(String[] args) {

        Phone phone = new Phone();

        new Thread(phone::sendEmail).start();

        try {TimeUnit.MILLISECONDS.sleep(200);} catch (InterruptedException e) {e.printStackTrace();}

        new Thread(phone::sendMail).start();
    }
}

class Phone{ // 资源类
    public synchronized void sendEmail(){
        System.out.println("发送邮件");
    }

    public synchronized void sendMail(){
        System.out.println("发送短信");
    }
}
```

###### 2. sendEmail延迟4秒，是先发邮件还是短信？发邮件

**synchronized 锁的是方法的调用者，也就是对象锁。两个方法持有的是同一把锁，因此谁先拿到锁谁先执行**  。

```java
class Phone{ // 资源类
    public synchronized void sendEmail(){
        try {TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}
        System.out.println("发送邮件");
    }

    public synchronized void sendMail(){
        System.out.println("发送短信");
    }
}
```

在1和2中：一个对象里面如果有多个synchronized方法，某一时刻内，只要一个线程去调用其中的一个synchronized方法了，其他的线程都只能是等待，换句话说，某一个时刻内，只能有唯一的一个线程去访问这些synchronized方法，**锁的是当前对象this**，被锁定后，其它的线程都不能进入到当前对象的其他synchronized方法。

###### 3. 增加一个普通方法后，先发邮件还是Hello？普通方法

普通方法，因为**普通方法没有锁，不需要竞争锁。**

```java
public class Lock8Demo {
    public static void main(String[] args) {

        Phone phone = new Phone();

        new Thread(()->{
            phone.sendEmail();
        },"a").start();

        try {TimeUnit.MILLISECONDS.sleep(200);} catch (InterruptedException e) {e.printStackTrace();}

        new Thread(()->{
            phone.hello();
        },"b").start();
    }
}

class Phone{ // 资源类
    public synchronized void sendEmail(){
        System.out.println("发送邮件");
    }

    public void hello(){
        System.out.println("hello");
    }
}
```

**hello并未和其他`synchronized`修饰的方法产生争抢。**

###### **4. 两个对象，两个同步方法**，发邮件(这里有个3秒延迟)还是发短信？发短信

**synchronized 锁的是方法的调用者，也就是对象锁。两个对象分别调用两个方法持有的是两把把锁，发短信不需要等待。如果不沉睡，锁的是对象，因为是不同的两个对象，所以并不受锁的影响。**

```java
public class Lock8Demo {
    public static void main(String[] args) {
        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(()->{
            phone1.sendEmail();
        },"a").start();

        try {TimeUnit.MILLISECONDS.sleep(200);} catch (InterruptedException e) {e.printStackTrace();}

        new Thread(()->{
            phone2.sendMail();
        },"b").start();
    }
}

class Phone{ // 资源类
    public synchronized void sendEmail(){
        try {TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}
        System.out.println("发送邮件");
    }

    public synchronized void sendMail(){
        System.out.println("发送短信");
    }
}
```

**锁在两个不同的对象/两个不同的资源上，不产生竞争条件**

###### **5. 两个静态同步方法（synchroized前加static,3秒延迟也在），一个对象调用**，先发邮件还是发短信？发邮件

**static方法类一加载就会执行，synchronized 锁的是Class对象，所以两个方法持有一把锁，谁先得到谁先执行**

```java
public class Lock8Demo {
    public static void main(String[] args) {

        Phone phone = new Phone();

        new Thread(()->{
            phone.sendEmail();
        },"a").start();

        try {TimeUnit.MILLISECONDS.sleep(200);} catch (InterruptedException e) {e.printStackTrace();}

        new Thread(()->{
            phone.sendMail();
        },"b").start();
    }
}

class Phone{ // 资源类
    public static synchronized void sendEmail(){
        try {TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}
        System.out.println("发送邮件");
    }

    public static synchronized void sendMail(){
        System.out.println("发送短信");
    }
}
```

###### 6. 两个对象，两个静态的同步方法（synchroized前加static,3秒延迟也在）， 先打印发邮件还是发短信？发邮件

**static方法类一加载就执行，synchronized 锁的是Class对象即类锁，两个方法持有两把把锁**，因此谁先执行谁就先拿到了锁。

```java
public class Lock8Demo {
    public static void main(String[] args) {

        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(()->{
            phone1.sendEmail();
        },"a").start();

        try {TimeUnit.MILLISECONDS.sleep(200);} catch (InterruptedException e) {e.printStackTrace();}

        new Thread(()->{
            phone2.sendMail();
        },"b").start();
    }
}

```

- 对于普通同步方法，锁的是当前实例对象，通常指this，具体的一部部手机，所有的普通同步方法用的都是同一把锁→实例对象本身。

- 对于静态同步方法，锁的是当前类的Class对象，如Phone，class唯一的一个模板。

- 对于同步方法块，锁的是synchronized括号内的对象。synchronized(o)

###### 7. 一个静态的同步方法，一个普通的同步方法 ，一个对象，先发邮件还是发短信？ 发短信

**原因：静态同步方法和普通同步方法分别是类锁和对象锁，相当于两把锁，由于普通同步方法不要等待，因此先被执行了**

```java
public class Lock8Demo {
    public static void main(String[] args) {

        Phone phone = new Phone();

        new Thread(()->{
            phone.sendEmail();
        },"a").start();

        try {TimeUnit.MILLISECONDS.sleep(200);} catch (InterruptedException e) {e.printStackTrace();}

        new Thread(()->{
            phone.sendMail();
        },"b").start();
    }
}

class Phone{ // 资源类
    public static synchronized void sendEmail(){
        try {TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}
        System.out.println("发送邮件");
    }

    public synchronized void sendMail(){
        System.out.println("发送短信");
    }
}
```

###### 8. 一个静态的同步方法，一个普通的同步方法 ，两个对象，先发邮件还是发短信？发短信

**原因：静态同步方法和普通同步方法分别是类锁和对象锁，相当于两把锁，由于普通同步方法不要等待，因此先被执行了**

```java
public class Lock8Demo {
    public static void main(String[] args) {

        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(()->{
            phone1.sendEmail();
        },"a").start();

        try {TimeUnit.MILLISECONDS.sleep(200);} catch (InterruptedException e) {e.printStackTrace();}

        new Thread(()->{
            phone2.sendMail();
        },"b").start();
    }
}
```

在7和8中一个加了**对象锁**，一个加了**类锁**，不产生竞争条件

##### 8锁原理

- 1和2中：一个对象里面如果有多个synchronized方法，某一时刻内，只要一个线程去调用其中的一个synchronized方法了，其他的线程都只能是等待，换句话说，某一个时刻内，只能有唯一的一个线程去访问这些synchronized方法，**锁的是当前对象this**，被锁定后，其它的线程都不能 进入到当前对象的其他synchronized方法

- 3中：hello并未和其他synchronized修饰的方法产生争抢

- 4 中：锁在两个不同的对象/两个不同的资源上，不产生竞争条件

- 5和6中：static+synchronized - 类锁 **** phone = new Phone();中加到了左边的Phone上

  - 对于普通同步方法，锁的是当前实例对象，通常指this，具体的一部部手机，所有的普通同步方法用的都是同一把锁→实例对象本身。


  - 对于静态同步方法，锁的是当前类的Class对象，如Phone，class唯一的一个模板。


  - 对于同步方法块，锁的是synchronized括号内的对象。synchronized(o)


- 7和8中一个加了对象锁，一个加了类锁，不产生竞争条件

##### **8锁的3个体现**

- 8种锁的案例实际体现在3个地方-相当于总结
  - 作用域**实例方法**，当前实例加锁，进入同步代码块前要获得当前实例的锁。
  -  作用于**代码块**，对括号里配置的对象加锁。
  - 作用于**静态方法**，当前类加锁，进去同步代码前要获得当前类对象的锁

#### 字节码角度分析synchronized实现

##### 文件反编译技巧

可以通过jclasslib查看，或者通过`javap -c xxx.class`进行反编译。

##### synchronized同步代码块

```java
/**
 * 锁同步代码块
 */
public class LockSyncDemo {
    Object object = new Object();

    public void m1(){
        synchronized (object){
            System.out.println("-----hello synchronized code block");
        }
    }

    public static void main(String[] args) {

    }
}
```

* m1方法对应的字节码

```diff
 0 aload_0
 1 getfield #3 <chapter02/LockSyncDemo.object : Ljava/lang/Object;>
 4 dup
 5 astore_1
 6 monitorenter
 7 getstatic #4 <java/lang/System.out : Ljava/io/PrintStream;>
10 ldc #5 <-----hello synchronized code block>
12 invokevirtual #6 <java/io/PrintStream.println : (Ljava/lang/String;)V>
15 aload_1
16 monitorexit
17 goto 25 (+8)
20 astore_2
21 aload_1
22 monitorexit
23 aload_2
24 athrow
25 return
```

- 可以看到，synchronized同步代码块，实现使用的是moniterenter和moniterexit指令（moniterexit可能有两个）。

- 那一定是一个enter两个exit吗？（不一样，如果主动throw一个RuntimeException，发现一个enter，一个exit，还有两个athrow）

##### synchronized普通同步方法

```java
/**
 * 锁普通的同步方法
 */
public class LockSyncDemo {

    public synchronized void m2(){
        System.out.println("------hello synchronized m2");
    }

    public static void main(String[] args) {

    }
}
```

- m2方法对应的字节码

![image-20220612131233409](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220612131233409.png)

```java
0 getstatic #4 <java/lang/System.out : Ljava/io/PrintStream;>
3 ldc #7 <------hello synchronized m2>
5 invokevirtual #6 <java/io/PrintStream.println : (Ljava/lang/String;)V>
8 return
```

调用指令将会检查方法的**ACC_SYNCHRONIZED**访问标志是否被设置。如果设置了，执行线程会将先持有monitore然后再执行方法，最后在方法完成（无论是正常完成还是非正常完成）时释放monitor。

##### synchronized静态同步方法

```java
/**
 * 锁静态同步方法
 */
public class LockSyncDemo {
    public static synchronized void m3(){
        System.out.println("------hello synchronized m3---static");
    }
    public static void main(String[] args) {

    }
}
```

* 对应的字节码

**ACC_SYNCHRONIZED**、**ACC_STATIC**访问标志会区分该方法是否是静态同步方法。

![image-20220612131357908](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220612131357908.png)

```java
0 getstatic #4 <java/lang/System.out : Ljava/io/PrintStream;>
3 ldc #8 <------hello synchronized m3---static>
5 invokevirtual #6 <java/io/PrintStream.println : (Ljava/lang/String;)V>
8 return
```

#### 反编译的synchronized锁的是什么

##### 概念

管程概念：

- 管程：Monitor（监视器），也就是我们平时说的锁。监视器锁

- 信号量及其操作原语“封装”在一个对象内部）**管程实现了在一个时间点，最多只有一个线程在执行管程的某个子程序。** 管程提供了一种机制，管程可以看做一个软件模块，它是将共享的变量和对于这些共享变量的操作封装起来，形成一个具有一定接口的功能模块，进程可以调用管程来实现进程级别的并发控制。

- 执行线程就要求先成功持有管程，然后才能执行方法，最后当方法完成（无论是正常完成还是非正常完成）时释放管理。在方法执行期间，执行线程持有了管程，其他任何线程都无法再获取到同一个管程。

##### **面试题：为什么任何一个对象都可以成为一个锁？**

溯源

- Java Object 类是所有类的父类，也就是说 Java 的所有类都继承了 Object，子类可以使用 Object 的所有方法。

- ObjectMonitor.java→ObjectMonitor.cpp→objectMonitor.hpp


ObjectMonitor.cpp中引入了头文件（include）objectMonitor.hpp

```c++
//140行
ObjectMonitor() {
    _header       = NULL;
    _count        = 0; //用来记录该线程获取锁的次数
    _waiters      = 0,
    _recursions   = 0;//锁的重入次数
    _object       = NULL;
    _owner        = NULL; //------最重要的----指向持有ObjectMonitor对象的线程，记录哪个线程持有了我
    _WaitSet      = NULL; //存放处于wait状态的线程队列
    _WaitSetLock  = 0 ;
    _Responsible  = NULL ;
    _succ         = NULL ;
    _cxq          = NULL ;
    FreeNext      = NULL ;
    _EntryList    = NULL ;//存放处于等待锁block状态的线程队列
    _SpinFreq     = 0 ;
    _SpinClock    = 0 ;
    OwnerIsThread = 0 ;
    _previous_owner_tid = 0;
}
```

* 每一个对象天生都带有一个对象监视器
* 每一个被锁住的对象都会和Monitor关联起来

#### 提前熟悉锁升级

synchronized必须作用于某个对象中，所以Java在对象的头文件存储了锁的相关信息。锁升级功能主要依赖于 MarkWord 中的锁标志位和释放偏向锁标志位

![在这里插入图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/873d84c0d8bf41dfaec19fd1e0c58e18.png)

### 公平锁和非公平锁

#### 由ReentrantLock抢票案例演示公平和非公平现象

```java
class Ticket {
    private int number = 30;
//    ReentrantLock lock = new ReentrantLock(); // 非公平锁，默认的
    ReentrantLock lock = new ReentrantLock(true); // 公平锁

    public void sale() {
        lock.lock();
        try {
            if (number > 0) {
                System.out.println(Thread.currentThread().getName() + "卖出第：\t" + (number--) + "\t 还剩下:" + number);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}

public class SaleTicketDemo {
    public static void main(String[] args) {
        Ticket ticket = new Ticket();

        new Thread(() -> {
            for (int i = 0; i < 35; i++) ticket.sale();
        }, "a").start();
        new Thread(() -> {
            for (int i = 0; i < 35; i++) ticket.sale();
        }, "b").start();
        new Thread(() -> {
            for (int i = 0; i < 35; i++) ticket.sale();
        }, "c").start();
    }
}
```

#### 非公平锁

- 默认是非公平锁
- 非公平锁可以**插队**，买卖票不均匀。
- 是指多个线程获取锁的顺序并不是按照申请锁的顺序，有可能后申请的线程比先申请的线程优先获取锁，在高并发环境下，有可能造成优先级翻转或**饥饿的状态**（某个线程一直得不到锁）

#### 公平锁

- `ReentrantLock lock = new ReentrantLock(true);`
- 买卖票一开始a占优，后面a b c a b c a b c均匀分布
- 是指多个线程按照**申请锁的顺序**来获取锁，这里类似排队买票，先来的人先买后来的人在队尾排着，这是公平的。

#### 为什么会有公平锁/非公平锁的设计？为什么默认是非公平？

- 恢复挂起的线程到真正锁的获取还是有时间差的，从开发人员来看这个时间微乎其微，但是从CPU的角度来看，这个时间差存在的还是很明显的。所以非公平锁能更充分的利用CPU 的时间片，尽量减少 CPU 空闲状态时间。（减少CPU空闲状态时间）

- 使用多线程很重要的考量点是线程切换的开销，当采用非公平锁时，当1个线程请求锁获取同步状态，然后释放同步状态，因为不需要考虑是否还有前驱节点，所以刚释放锁的线程在此刻再次获取同步状态的概率就变得非常大，所以就减少了线程的开销。（减少CPU开销）

#### 什么时候用公平？什么时候用非公平？

- 如果为了更高的**吞吐量**，很显然非公平锁是比较合适的，因为**节省很多线程切换时间**，吞吐量自然就上去了；
- 否则那就用公平锁，大家公平使用。

### 可重入锁（递归锁）

#### 可重入锁说明

可重入锁又名递归锁，是指在**同一个线程**在外层方法获取锁的时候，再进入该线程的内层方法会**自动获取锁**(前提，锁对象得是同一个对象)，不会因为之前已经获取过还没释放而阻塞。

如果是**1个有 synchronized 修饰的递归调用方法，程序第2次进入被自己阻塞了岂不是天大的笑话，出现了作茧自缚。**

所以**Java中ReentrantLock和synchronized都是可重入锁，可重入锁的一个优点是可一定程度避免死锁。**

> **一个线程中的多个流程可以获取同一把锁，持有这把锁可以再次进入，自己可以获取自己的内部锁。**

#### 可重入锁种类

##### 隐式锁Synchronized

`synchronized`是java中的关键字，**默认**是**可重入锁**，即隐式锁。

###### 同步块

```java
public class ReEntryLockDemo {
    public static void main(String[] args) {
        final Object objectLockA = new Object();

        new Thread(() -> {
            synchronized (objectLockA) {
                System.out.println("-----外层调用");
                synchronized (objectLockA) {
                    System.out.println("-----中层调用");
                    synchronized (objectLockA) {
                        System.out.println("-----内层调用");
                    }
                }
            }
        },"a").start();
    }
}
//-----外层调用
//-----中层调用
//-----内层调用
```

###### 同步方法

```java
public class ReEntryLockDemo {
    public synchronized void m1() {
        //指的是可重复可递归调用的锁，在外层使用之后，在内层仍然可以使用，并且不发生死锁，这样的锁就叫做可重入锁
        System.out.println(Thread.currentThread().getName() + "\t" + "-----come in m1");
        m2();
        System.out.println(Thread.currentThread().getName() + "\t-----end m1");
    }

    public synchronized void m2() {
        System.out.println("-----m2");
        m3();
    }

    public synchronized void m3() {
        System.out.println("-----m3");
    }

    public static void main(String[] args) {
        ReEntryLockDemo reEntryLockDemo = new ReEntryLockDemo();
        reEntryLockDemo.m1();
    }
}
/**
 * main  -----come in m1
 * -----m2
 * -----m3
 * main  -----end m1
 */
```

##### Synchronized的重入实现机理

- 回看上方的`ObjectMoitor.hpp`

```c++
//140行
ObjectMonitor() {
    _header       = NULL;
    _count        = 0; //用来记录该线程获取锁的次数
    _waiters      = 0,
    _recursions   = 0;//锁的重入次数
    _object       = NULL;
    _owner        = NULL; //------最重要的----指向持有ObjectMonitor对象的线程，记录哪个线程持有了我
    _WaitSet      = NULL; //存放处于wait状态的线程队列
    _WaitSetLock  = 0 ;
    _Responsible  = NULL ;
    _succ         = NULL ;
    _cxq          = NULL ;
    FreeNext      = NULL ;
    _EntryList    = NULL ;//存放处于等待锁block状态的线程队列
    _SpinFreq     = 0 ;
    _SpinClock    = 0 ;
    OwnerIsThread = 0 ;
    _previous_owner_tid = 0;
}
```

- ObjectMoitor.hpp底层：每个锁对象拥有一个锁计数器和一个指向持有该锁的线程的指针。_count _owner

- **首次加锁**：当执行monitorenter时，如果目标锁对象的计数器为零，那么说明它没有被其他线程所持有，Java虚拟机会将该锁对象的持有线程设置为当前线程，并且将其计数器加1。
- **重入**：在目标锁对象的计数器不为零的情况下，如果锁对象的持有线程是当前线程，那么 Java 虚拟机可以将其计数器加1，否则需要等待，直至持有线程释放该锁。
- **释放锁**：当执行monitorexit时，Java虚拟机则需将锁对象的计数器减1。计数器为零代表锁已被释放。

##### 显示锁Lock

- 显式锁（即Lock）也有ReentrantLock这样的可重入锁

> 所谓的显式隐式即是指显示/隐式的调用锁

- 注意：`lock` `unlock`要**成对**

```java
public class ReEntryLockDemo2 {
    static Lock lock = new ReentrantLock();

    public static void main(String[] args) {
        {
            new Thread(() -> {
                lock.lock();
                try {
                    System.out.println(Thread.currentThread().getName() + "\t----come in 外层调用");
                    lock.lock();
                    try {
                        System.out.println(Thread.currentThread().getName() + "\t------come in 内层调用");
                    } finally {
                        lock.unlock();
                    }
                } finally {
                    lock.unlock();
                }
            }, "t1").start();
        }
    }
}
//t1  ----come in 外层调用
//t1  ------come in 内层调用
```

* 假如`lock` `unlock`不成对，单线程情况下问题不大，但**多线程下出问题**

```java
public class ReEntryLockDemo3 {
    static Lock lock = new ReentrantLock();
    public static void main(String[] args) {

        new Thread(() -> {
            lock.lock();
            try {
                System.out.println(Thread.currentThread().getName() + "\t----come in 外层调用");
                lock.lock();
                try {
                    System.out.println(Thread.currentThread().getName() + "\t------come in 内层调用");
                } finally {
                    lock.unlock();
                }
            } finally {
                //lock.unlock();//-------------------------不成对|多线程情况
            }
        }, "t1").start();

        new Thread(() -> {
            lock.lock();
            try
            {
                System.out.println("t2 ----外层调用lock");
            }finally {
                lock.unlock();
            }
        },"t2").start();

    }
}
//t1  ----come in 外层调用
//t1  ------come in 内层调用
//(t2 ----外层调用lock 假如不成对，这句话就不显示了)
```

### 死锁及排查

#### 死锁是什么

死锁是指两个或两个以上的线程在执行过程中,因争夺资源而造成的一种互相等待的现象,若无外力干涉那它们都将无法推进下去，如果系统资源充足，进程的资源请求都能够得到满足，死锁出现的可能性就很低，否则就会因争夺有限的资源而陷入死锁。

实例：a跟b两个资源互相请求对方的资源

![在这里插入图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/39270a872ae348d995ae002e74788193.png)

#### 死锁产生的原因

- 系统资源不足
- 进程运行推进的顺序不合适
- 资源分配不当

#### 产生死锁的四个必要条件

- 互斥条件：一个资源每次只能被一个进程使用。
- 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放
- 不剥夺条件：进程已获得的资源，在未使用完之前不能强行剥夺。
- 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。

只要破坏其中一个。就可以避免死锁。

#### 死锁案例

```java
public class DeadLockDemo {
    public static void main(String[] args) {
        Object object1 = new Object();
        Object object2 = new Object();

        new Thread(()->{
            synchronized (object1){
                System.out.println(Thread.currentThread().getName()+"\t 持有a锁，想获得b锁");
                try {
                    TimeUnit.SECONDS.sleep(1);} catch (InterruptedException e) {e.printStackTrace();}//使得线程b也启动
                synchronized (object2){
                    System.out.println(Thread.currentThread().getName()+"\t 成功获得b锁");
                }
            }
        },"A").start();

        new Thread(()->{
            synchronized (object2){
                System.out.println(Thread.currentThread().getName()+"\t 持有b锁，想获得a锁");
                synchronized (object1){
                    System.out.println(Thread.currentThread().getName()+"\t 成功获得a锁");
                }
            }
        },"B").start();
    }

}
```

#### 如何排查死锁

##### JVM命令

* `jps -l` 查看当前进程运行状况
* `jstack pid` 查看进程信息

 ![image-20220612151209015](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220612151209015.png)

![image-20220612151144448](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220612151144448.png)

##### jconsole

![image-20220612151359194](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220612151359194.png)

##### jvisualvm

![image-20220612151443224](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220612151443224.png)

### 其他锁

- 写锁（独占锁）/读锁（共享锁）
- 自旋锁SpinLock
- 无锁/独占锁/读写锁/邮戳锁
- 无锁/偏向锁/轻量锁/重量锁 

### 总结

 指针指向monitor对象（也称为管程或监视器锁）的起始地址。每个对象都存在着一个monitor与之关联，当一个monitor被某个线程持有后，它便处于锁定状态。在Java虚拟机(HotSpot)中，monitor是由ObjectMonitor实现的，其主要数据结构如下（位于HotSpot虚拟机源码ObjectMonitor.hpp,C++实现的）。

![在这里插入图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/8f38594a73af4f95a2525adf87141d09.png)

## Lock Support与线程中断

### 线程中断机制

#### 面试题

#### 什么是中断机制

**首先**，一个线程不应该由其他线程来强制中断或停止，而是**应该由线程自己自行停止。**所以，Thread.stop, Thread.suspend, Thread.resume 都已经被废弃了。

**其次**，在Java中没有办法立即停止一条线程，然而停止线程却显得尤为重要，如取消一个耗时操作。因此，Java提供了一种用于停止线程的**协商机制**——**中断**。

**中断只是一种协作协商机制，Java没有给中断增加任何语法，中断的过程完全需要程序员自己实现。**若要中断一个线程，你需要手动调用该线程的interrupt方法，该方法也仅仅是将线程对象的中断标识设成true；接着你需要自己写代码不断地检测当前线程的标识位，如果为true，表示别的线程要求这条线程中断，此时究竟该做什么需要你自己写代码实现。

每个线程对象中都有一个标识，用于表示线程是否被中断；该标识位为true表示中断，为false表示未中断；通过调用线程对象的interrupt方法将该线程的标识位设为true；可以在别的线程中调用，也可以在自己的线程中调用。

eg.顾客在无烟餐厅中吸烟，服务员希望他别吸烟了，不是强行停止他吸烟，而是给他的标志位打为true，具体的停止吸烟还是要顾客自己停止。（体现了协商机制）

#### 中断相关的API方法之三大方法说明

![在这里插入图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/38f14981ccb54f58b7dfb21f3e9d4747.png)

| api方法                             | 含义                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| public void interrupt()             | 实例方法，实例方法interrupt()仅仅是设置线程的中断状态为true，发起一个协商而不会立刻停止线程 |
| public static boolean interrupted() | 静态方法，Thread.interrupted();判断线程是否被中断，并清除当前中断状态这个方法做了两件事：1. 返回当前线程的中断状态；2. 将当前线程的中断状态设为false（这个方法有点不好理解，因为连续调用两次的结果可能不一样。） |
| public boolean isInterrupted()      | 实例方法，判断当前线程是否被中断（通过检查中断标志位）       |

#### 面试题：如何使用中断标识停止线程

##### 如何停止中断运行的线程

###### 1. 通过一个volatile变量实现

- volatile保证了可见性，t2修改了标志位后能马上被t1看到

```java
public class InterruptDemo {
    public static volatile boolean isStop = false;

    public static void main(String[] args) {
        new Thread(()->{
            while (true){
                if(isStop){ // 如果这个标志位被其他线程改为true
                    System.out.println(Thread.currentThread().getName()+"\t isStop被修改为true，程序终止");
                    break;
                }
                System.out.println("t1 ------hello volatile");//----------------------如果没停止，那就一直打印
            }
        },"t1").start();


        try {
            TimeUnit.MILLISECONDS.sleep(20);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            isStop = true;
        },"t2").start();

    }
}
```

###### 2. 通过AtomicBoolean（原子布尔型）

```java
public class InterruptDemo {
    public static volatile boolean isStop = false;
    public static AtomicBoolean isStopAtomic = new AtomicBoolean(false);

    public static void main(String[] args) {
        new Thread(()->{
            while (true){
                if(isStopAtomic.get()){ // 如果这个标志位被其他线程改为true
                    System.out.println(Thread.currentThread().getName()+"\t isStopAtomic被修改为true，程序终止");
                    break;
                }
                System.out.println("t1 ------hello AtomicBoolean");//----------------------如果没停止，那就一直打印
            }
        },"t1").start();


        try {
            TimeUnit.MILLISECONDS.sleep(20);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            isStopAtomic.set(true);
        },"t2").start();

    }
}
```

###### 3. 通过Thread类自带的中断api方法实现

```java
public class InterruptDemo {
    public static void main(String[] args) {
        Thread t1 = new Thread(()->{
            while (true){
                if(Thread.currentThread().isInterrupted()){
                    System.out.println(Thread.currentThread().getName()+"\t isInterrupted被修改为true，程序终止");
                    break;
                }
                System.out.println("t1 ------hello isInterrupted api");//----------------------如果没停止，那就一直打印
            }
        },"t1");

        t1.start();


        try {
            TimeUnit.MILLISECONDS.sleep(20);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            t1.interrupt(); // 发送中断信号
        },"t2").start();

    }
}
```

###### Api源码分析

* **实例方法interrupt()，没有返回值**

```java
//Thread.java
public void interrupt() {
    if (this != Thread.currentThread())
        checkAccess();

    synchronized (blockerLock) {
        Interruptible b = blocker;
        if (b != null) {
            interrupt0(); // Just to set the interrupt flag----调用了interrupt0()方法
            b.interrupt(this);
            return;
        }
    }
    interrupt0();
}

//Thread.java
/* Some private helper methods */
private native void setPriority0(int newPriority);
private native void stop0(Object o);
private native void suspend0();
private native void resume0();
private native void interrupt0();  //---------------------------调用了c底层
private native void setNativeName(String name);
```

###### 说明

具体来说，当对一个线程，调用 interrupt() 时：

- 如果线程处于正常活动状态，那么会将该线程的中断标志设置为 true，仅此而已。被设置中断标志的线程将继续正常运行，不受影响。所以， interrupt() 并不能真正的中断线程，需要被调用的线程自己进行配合才行。

- 第三种方法，如果线程处于被阻塞状态（例如处于sleep, wait, join 等状态），在别的线程中调用当前线程对象的interrupt方法，那么线程将立即退出被阻塞状态（中断状态将被清除），并抛出一个InterruptedException异常。

- （中断不活动的线程不会产生任何影响，看下面案例）

##### 当前线程的中断标识为true，是不是线程就立刻停止

不是。仅仅设置了一个中断状态

###### 案例1

看看中断是否会立即停止这个300的线程，否，虽然中断标志位变了。但是i一直在循环

```java
public class InterruptDemo2 {
    public static void main(String[] args) {

        // 仅仅是设置线程的中断状态位设置为true，不会停止线程
        Thread t1 = new Thread(()->{
            for (int i = 0; i < 300; i++) {
                System.out.println(i);
            }
            System.out.println("after t1.interrupt()---第2次----"+Thread.currentThread().isInterrupted());// true
        },"t1");

        t1.start();

        System.out.println("before t1.interrupt()----"+t1.isInterrupted()); // false

        try {TimeUnit.MILLISECONDS.sleep(2);} catch (InterruptedException e) {e.printStackTrace();}
        t1.interrupt();// true
        System.out.println("after t1.interrupt()---第1次---"+t1.isInterrupted()); // 第1次---true    ------此处中断标志位设置为了true,但是t1仍然在运行

        try {TimeUnit.MILLISECONDS.sleep(3000);} catch (InterruptedException e) {e.printStackTrace();}
        System.out.println("after t1.interrupt()---第3次---"+t1.isInterrupted()); // 第3次---false//中断不活动的线程不会产生任何影响，线程结束后应该是自动变为了false
    }
}
```

###### 案例2

如果线程处于被阻塞状态（例如处于sleep, wait, join 等状态），在别的线程中调用当前线程对象的`interrupt`方法，那么线程将**立即退出被阻塞状态**（中断状态将被清除），并抛出一个InterruptedException异常。

**在catch块中，需要再次给中断标志位设置为true，2次调用停止。**

```java
public class InterruptDemo3 {
    public static void main(String[] args) {
        Thread t1 =  new Thread(()->{
            while(true){
                if(Thread.currentThread().isInterrupted()){
                    System.out.println(Thread.currentThread().getName()+"\t"+
                                       "中断标志位："+Thread.currentThread().isInterrupted()+"程序终止");
                    break;
                }
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                    Thread.currentThread().interrupt();  //假如加了这个，程序可以终止，只会爆异常
                }
                System.out.println("-----hello InterruptDemo03");
            }
        },"t1");
        t1.start();
        try {TimeUnit.SECONDS.sleep(1);} catch (InterruptedException e) {e.printStackTrace();}
        new Thread(() -> t1.interrupt()).start();
    }
}
/**
 * 1 中断标志位 默认是false
 * 2 t2 ----->t1发出了中断协商，t2调用t1.interrupt()，中断标志位true
 * 3 中断标志位true，正常情况下，程序停止，^-^
 * 4 中断标志位true，异常情况下，InterruptedException，将会把中断状态清除，并且将收到InterruptedException。中断标志位false导致无限循环。
 * 5 在catch块中，需要再次给中断标志位设置为true，2次调用停止
 */
```

###### 小总结

中断只是一种协同机制，修改中断标识位仅此而已，而不是立刻stop打断。

##### 静态方法Thread.interrupted()，谈谈你的理解

public static boolean interrupted()静态方法，``Thread.interrupted()``;判断线程是否被中断，并清除当前中断状态这个方法做了两件事：

1. 返回当前线程的中断状态

2. 将当前线程的中断状态设为false（这个方法有点不好理解，因为连续调用两次的结果可能不一样。） 

```java
public class InterruptDemo4 {
    public static void main(String[] args) {
        // 中断标识被清空，返回一个boolean并清除中断状态
        // 第二次再调用时，中断状态已经被清除，将返回一个false 

        System.out.println(Thread.currentThread().getName() + "\t" + Thread.interrupted());// false
        System.out.println(Thread.currentThread().getName() + "\t" + Thread.interrupted());// false

        System.out.println("1");
        Thread.currentThread().interrupt(); // 中断标志位设置为true
        System.out.println("2");

        System.out.println(Thread.currentThread().getName() + "\t" + Thread.interrupted());// true
        System.out.println(Thread.currentThread().getName() + "\t" + Thread.interrupted());// false
    }
}
```

`interrupted()`对比`isInterrupted()`

```java
public static boolean interrupted() {
    return currentThread().isInterrupted(true);
}

public boolean isInterrupted() {
    return isInterrupted(false);
}
private native boolean isInterrupted(boolean ClearInterrupted);
```

- 他们在底层都调用了native方法isInterrupted。

- 只不过传入参数ClearInterrupted一个传参传了true，一个传了false。

  - 静态方法interrupted() 中true表示清空当前中断状态。

  - 实例方法isInterrupted 则不会。

### LockSupport是什么

#### 官方解释

用于创建锁和其他同步类的基本线程阻塞原语。

![image-20220612210833087](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/image-20220612210833087.png)

##### park()与unpark()

- `park()`方法是**阻塞线程**
- `unpark()`方法是**解除阻塞线程**

![请添加图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/1ca493a6ed07446a90dafaef90d25d23.png)

### 线程等待唤醒机制

#### 3种线程等待和唤醒的方法

1. 使用Object中的`wait()`方法让线程等待，使用Object中的`notify()`方法唤醒线程
2. 使用JUC包中`Condition`的`await()`方法让线程等待，使用`signal()`方法唤醒线程
3. `LockSupport`类可以阻塞当前线程以及唤醒指定被阻塞的线程

#### 1. Object类中的wait和notify方法实现线程等待和唤醒

* 一定是wait先执行然后才是notify再执行，不然会一直被阻塞
  * wait和notify方法必须要在**同步**块或者方法里面，且**成对**出现使用
* 使用`wait`和`notify`必须加`synchronized`

```java
public class LockSupportDemo {
    public static void main(String[] args) {
        Object object = new Object();

        new Thread(()->{
            synchronized (object){
                System.out.println(Thread.currentThread().getName() +"\t --------------- come in");
                try {
                    object.wait(); // 等待
                }catch (InterruptedException e){
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName() +"\t --------------- 被唤醒了");
        },"t1").start();

        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            synchronized (object){
                object.notify(); // 唤醒
                System.out.println(Thread.currentThread().getName() + "\t -------------发出通知");
            }
        },"t2").start();
    }
}
```

#### 2. Condition接口中的await后signal方法实现线程的等待和唤醒

- `await`和`notify`类似于上面`wait`和`notify`
  - Condition中的线程等待和唤醒方法，需要先获取锁
  - 一定要先await后signal，不能反了

```java
public class LockSupportDemo2 {

    Lock lock = new ReentrantLock();
    // condition1被阻塞唤醒condition2，condition2被阻塞唤醒condition3...
    Condition condition1 = lock.newCondition();
    Condition condition2 = lock.newCondition();
    Condition condition3 = lock.newCondition();

    public int version = 1; // condition1=1,condition2=2,condition3=3

    public void conditionMethod(Condition c1,Condition c2,int cur,int next){
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t --------------- come in");
            while (version != cur){
                c1.await();
            }
            version = next;
            c2.signal();
            System.out.println(Thread.currentThread().getName() + "\t --------------- 被唤醒");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void m1(){
        conditionMethod(condition1,condition2,1,2);
    }

    public void m2(){
        conditionMethod(condition2,condition3,2,3);
    }

    public void m3(){
        conditionMethod(condition3,condition1,3,1);
    }


    public static void main(String[] args) {
        LockSupportDemo2 lockSupportDemo2 = new LockSupportDemo2();
        new Thread(lockSupportDemo2::m1,"t1").start();
        new Thread(lockSupportDemo2::m2,"t2").start();
        new Thread(lockSupportDemo2::m3,"t3").start();
    }
}
```

#### Object和Condition使用的限制条件

- **线程先要获得并持有锁，必须在锁块（synchronized或lock）中**
- **必须要先等待后唤醒，线程才能够被唤醒**

#### 3. LockSupport类中的park等待和unpark唤醒

##### 什么是LockSupport

LockSupport是用来创建锁和其他同步类的基本线程阻塞原语。

- LockSupport类使用了一种名为Permit（许可） 的概念来做到阻塞和唤醒线程的功能， 每个线程都有一个许可(permit)，permit（许可）只有两个值1和0，默认是0。0 是阻塞，1是唤醒。

- 可以把许可看成是一种(0,1)信号量（Semaphore），但与 Semaphore 不同的是，许可的累加上限是1。

##### 主要方法

###### API

![在这里插入图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/a998863dc0604839a91a45cb139e2462.png)

###### 阻塞

- `park()/park(Object blocker)`
- 调用`LockSupport.park()`时，发现它调用了`unsafe类`，并且默认传了一个0

```java
public static void park() {
    UNSAFE.park(false, 0L);
}
```

![在这里插入图片描述](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/cdd87c9b436a435ab07a2fecec304229.png)

permit默认是零，所以一开始调用park()方法，当前线程就会阻塞，直到别的线程将当前线程的permit设置为1时，park方法会被唤醒，然后会将permit再次设置为零并返回。

###### 唤醒

调用`LockSupport.unpark();`时，也调用了`unsafe类`

```java
public static void unpark(Thread thread) {
    if (thread != null)
        UNSAFE.unpark(thread);
}
```

![img](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/d784a1d4a5e54c09a6a8258983506b29.png)

调用unpark(thread)方法后，就会将thread线程的许可permit设置成**1**(注意多次调用unpark方法，不会累加，permit值还是1)会自动唤醒thread线程，即之前阻塞中的LockSupport.park()方法会立即返回。

##### park与unpark编码实战

###### 正常+无锁块要求

```java
public class LockSupportDemo3 {
    public static void main(String[] args) {
        Thread t1 = new Thread(()->{
            System.out.println(Thread.currentThread().getName() + "\t --------------- come in");
            LockSupport.park();
            System.out.println(Thread.currentThread().getName() + "\t --------------- 被唤醒");
        },"t1");

        t1.start();

        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            LockSupport.unpark(t1);
            System.out.println(Thread.currentThread().getName() + "\t -------------发出通知");
        },"t2").start();
    }
}
```

###### 先唤醒后等待

之前错误的先唤醒后等待，LockSupport照样支持

```java
public class LockSupportDemo3 {
    public static void main(String[] args) {
        Thread t1 = new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "\t --------------- come in");
            LockSupport.park();
            System.out.println(Thread.currentThread().getName() + "\t --------------- 被唤醒");
        },"t1");

        t1.start();

        new Thread(()->{
            LockSupport.unpark(t1);
            System.out.println(Thread.currentThread().getName() + "\t -------------发出通知");
        },"t2").start();
    }
}
```

sleep方法3秒后醒来，执行park无效，没有阻塞效果，解释如下。先执行了unpark(t1)导致上面的park方法形同虚设无效，**时间是一样的**
\- 类似于高速公路的ETC，提前买好了通行证unpark，到闸机处直接抬起栏杆放行了，没有park拦截了。

> **成双成对要牢记**

###### 许可证是只有一个

因为凭证的数量最多为1， 连续调用两次unpark和调用一次unpark效果一样， 只会增加一个凭证；而调用两次park却需要消费两个凭证， 证不够， 不能放行。

```java
public class LockSupportDemo3 {
    public static void main(String[] args) {
        Thread t1 = new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "\t --------------- come in");
            LockSupport.park();
            LockSupport.park();
            System.out.println(Thread.currentThread().getName() + "\t --------------- 被唤醒");
        },"t1");

        t1.start();

        new Thread(()->{
            LockSupport.unpark(t1);
            LockSupport.unpark(t1);
            System.out.println(Thread.currentThread().getName() + "\t -------------发出通知");
        },"t2").start();
    }
}
```

##### 总结

- Lock Support是用来创建锁和其他同步类的基本线程阻塞原语。
- Lock Support是一个线程阻塞工具类， 所有的方法都是静态方法， 可以让线程在任意位置阻塞， 阻塞之后也有对应的唤醒方法。归根结
  底， Lock Support调用的Unsafe中的native代码。
- Lock Support提供park() 和unpark() 方法实现阻塞线程和解除线程阻塞的过程
- Lock Support和每个使用它的线程都有一个许可(permit) 关联。
- 每个线程都有一个相关的permit， permit最多只有一个， 重复调用un park也不会积累凭证。

- 形象的理解：线程阻塞需要消耗凭证(permit) ， 这个凭证最多只有1个。

#### 面试题

##### 为什么可以突破wait/notify的原有调用顺序?

因为unpark获得了一个凭证， 之后再调用park方法， 就可以名正言顺的凭证消费， 故不会阻塞。先发放了凭证后续可以畅通无阻。

##### 为什么唤醒两次后阻塞两次，但最终结果还会阻塞线程?

因为凭证的数量最多为1， 连续调用两次unpark和调用一次unpark效果一样， 只会增加一个凭证（因为最多能有一个凭证）；而调用两次park却需要消费两个凭证， 证不够， 不能放行。

## Java内存模型之JMM

### 面试题

1. 你知道什么是Java内存模型JMM吗？
2. JMM与volatile它们两个之间的关系？(下一章详细讲解)
3. JMM有哪些特性，它的三大特性是什么？
4. 为什么要有JMM，它为什么出现？作用和功能是什么？
5. **happens-before先行发生原则你有了解过吗？**

### 计算机硬件存储体系

#### 概述

计算机存储结构，从本地磁盘到主存到 CPU 缓存，也就是从硬盘到内存，到 CPU 。

一般对应的程序的操作就是从数据库查数据到内存然后到 CPU 进行计算。

![img](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/4a5f394543584298b802a3e79cdb0618.png)

#### 问题和推导出我们需要知道JMM

因为有这么多级的缓存(cpu和物理主内存的速度不一致的)，CPU的运行并不是直接操作内存而是先把内存里边的数据读到缓存，而内存的读和写操作的时候就会造成不一致的问题。

![img](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/6884e58f1234420d92bad82cc0b73cb1.png)

Java虚拟机规范中试图定义一种Java内存模型（java Memory Model，简称JMM) 来**屏蔽掉各种硬件和操作系统的内存访问差异**， 以实现让Java程序在各种平台下都能达到一致的内存访问效果。

### Java内存模型

#### 概述

JMM(Java内存模型Java Memory Model，简称JMM)本身是一种**抽象的**概念**并不真实存在**它**仅仅描述的是一组约定或规范** ，通过这组规范定义了程序中(尤其是多线程)各个变量的读写访问方式并决定一个线程对共享变量的写入何时以及如何变成对另一个线程可见，关键技术点都是围绕多线程的**原子性、可见性和有序性**展开的。 

#### 原则

JMM的关键技术点都是围绕多线程的**原子性、可见性和有序性展开的。**

#### 能干什么

- 通过JMM来实现**线程和主内存之间的抽象关系**。 
- **屏蔽各个硬件平台和操作系统的内存访问差异**以实现让Java程序在各种平台下都能达到一致的内存访问效果。 

### JMM规范

#### 三大特性

##### 可见性

可见性，**是指当一个线程修改了某一个共享变量的值，其他线程是否能够立即知道该变更** ，JMM 规定了所有的变量都存储在**主内存**中。

![img](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/801b91a32fb84693989997f049691655.png)

Java中**普通的共享变量不保证可见性** ，因为数据修改被写入内存的时机是不确定的，**多线程并发下很可能出现 " 脏读 "** ，所以每个线程都有自己的工作内存 ，线程自己的工作内存中保存了该线程使用到的变量的主内存副本拷贝 ，线程对变量的所有操作（读取，赋值等 ）都必需在线程自己的工作内存中进行，而不能够直接读写主内存中的变量。不同线程之间也无法直接访问对方工作内存中的变量，线程间变量值的传递均需要通过主内存来完成。

<img src="../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/f19a3ba039f74cce82e50b5f631bf38b.png" alt="img" style="zoom:67%;" />

线程脏读：如果没有可见性保证

| 主内存中有变量 x ，初始值为 0                                |
| ------------------------------------------------------------ |
| 线程 A 要将 x 加 1 ，先将 x=0 拷贝到自己的私有内存中，然后更新 x 的值 |
| 线程 A 将更新后的 x 值回刷到主内存的时间是不固定的           |
| 刚好在线程 A 没有回刷 x 到主内存时，线程 B 同样从主内存中读取 x ，此时为 0 ，和线程 A 一样的操作，最后期盼的 x=2 就会变成 x=1 |

![img](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/a72562e933b64398b217d84e8fa17efd.png)

##### 原子性

指一个操作是不可中断的，即多线程环境下，操作不能被其他线程干扰。

##### 有序性

###### 什么是有序性

对于一个线程的执行代码而言，我们总是习惯性认为代码的执行总是从上下，有序执行。但为了提供性能，编译器和处理器通常会对指令序列进行重新排序。  

###### 优缺点

指令重排**可以保证串行语义一致**，但没有义务保证 多线程间的语义也一致，即可能产生 " 脏读 " ，简单说，两行以上不相干的代码在执行的时候有可能先执行的不是第一条，**不见得是从上到下顺序执行，执行顺序**会被优化 。  

![img](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/5546d96fdd624df79d32be84b8cc678f.png)

- 单线程环境里面确保程序最终执行结果和代码顺序执行的结果一致。  
- 处理器在进行重排序时必须要考虑指令之间的数据依赖性 。
- 多线程环境中线程交替执行 , 由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的 , 结果无法预测 。

###### 简单案例

```java
public void mySort() {
    int x = 11;//语句1
    int y = 12;//语句2
    x = x + 5;//语句3
    y = x * x;//语句4
}
 
//1 2 3 4
//2 1 3 4
//1 3 2 4
//问题：请问语句4可以重排后变成第一条吗？
//不可以
```

#### 多线程对变量的读写过程

##### 读取过程

由于 JVM 运行程序的实体是线程，而每个线程创建时 JVM 都会为其创建一个工作内存 ( 有些地方称为栈空间 ) ，工作内存是每个线程的私有数据区域，而 Java 内存模型中规定所有变量都存储在 主内存 ，主内存是共享内存区域，所有线程都可以访问， 但线程对变量的操作 ( 读取赋值等 ) 必须在工作内存中进行，首先要将变量从主内存拷贝到的线程自己的工作内存空间，然后对变量进行操作，操作完成后再将变量写回主内存， 不能直接操作主内存中的变量，各个线程中的工作内存中存储着主内存中的 变量副本拷贝 ，因此不同的线程间无法访问对方的工作内存，线程间的通信 ( 传值 ) 必须通过主内存来完成，其简要访问过程如下图 ：

![img](../../../../assets/05-JUC%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B%E4%B8%8E%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/b97f37a8e74b453c93dff00b3aea2887.png)



##### 总结

#### 多线程先行发生原则之happens-before

##### 先行发生原则说明

##### happens-before总原则

##### happens-before之8条原则

##### 案例说明

## volatile与JMM

## CAS(compare and swap)

## 原子操作类

## 聊聊ThreadLocal

## Java对象内存布局与对象头

## Synchronized与锁升级

## AbstractQueuedSynchronizer之AQS

## ReetrantLock、ReentrantReadWriteLock、StampedLock讲解