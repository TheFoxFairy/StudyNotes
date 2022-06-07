# JUC进阶编程

## ConcurrentHashMap与HashMap

hashmap：

- 在jdk1.7之前，数组+链表；在jdk1.8时，数组+链表+红黑树（平衡二叉树）。

- 链表与红黑树之间的转换有一个阈值。
- 需要设计一个良好的hash算法，尽量避免hash冲突问题，利用位运算**(n-1)&hash <==> hash % n**。n的值必须为2的n次幂
  - 这是为了利用位运算从而快速计算数组下标
  - 这是一个概率统计的值，默认值是16。
  - 可以自己设置n的值，但是程序里面会转换为2的n次幂
- $n*负载因子=16*0.75=12$，默认为0.75，也是统计得来的。
- 扩容要做的事情： 
  - 创建新数组
  - 转移数据到新数组中（怎么计算位置），位运算hash & ((n << 1) - 1)，也就是说当前位置为原来位置+n或者原来位置。
  - 原来的数组分段规定每个线程最少负责16个桶的迁移工作，如果小于16个桶，可以使用单线程运行即可。

- Hash冲突解决办法： 
  - 开放定址法
  - 再哈希法
  - 链地址法（拉链法）
  - 建立公共溢出区

## 线程池的设计及原理

### 线程使用上的问题

通过`new Thread().start()`创建线程

* 线程的频繁创建和销毁，会消耗CPU资源
* 线程的数量过多，会造成CPU资源的开销
  * 上下文切换（消耗CPU资源）

思考：如何实现线程的复用

### 池化技术

连接池、对象池、内存池、线程池

> 池化技术的核心：复用

### 线程池

提前创建一系列的线程，保存在这个线程池中。

有任务要执行的时候，从线程池中取出线程来执行。

没有任务的时候，再把线程池放回去。

### Java中提供的线程池

> Executors

* newFixedThreadPool：创建固定线程池
* newSingleThreadExecutor：创建单个线程的线程池
* newCachedThreadPool：创建可缓存的线程池
* newScheduledThreadPool：提供了按照周期执行的线程池

### ThreadPoolExecutor

Java中提供的线程池都是基于ThreadPoolExecutor实现的。

```java
public ThreadPoolExecutor(int corePoolSize, //核心线程数
                          int maximumPoolSize, //最大线程数
                          long keepAliveTime, //存活时间
                          TimeUnit unit, //存活单位
                          BlockingQueue<Runnable> workQueue, //阻塞队列
                          ThreadFactory threadFactory, //线程工厂，用来创建工作线程的。 默认实现（自定义线程池中线程的名字）
                          RejectedExecutionHandler handler) { //拒绝执行策略 。默认实现
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.acc = System.getSecurityManager() == null ?
        null :
    AccessController.getContext();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

### 线程池的设计思考

需求：实现线程的重复使用

分解：如何将线程复用

线程复用：让线程实现复用的唯一的方法，就是让线程不结束

* 那如何让线程执行新的任务呢？也就是说，任务怎么给他执行
  * 共享内存（List.add()）
* 线程一直处于运行状态，合理吗？
  * 有任务来的时候，执行
  * 没有任务的时候，阻塞
* 线程数量的设置：
  * $N_{cpu}$：cpu的设置数量
  * $U_{cpu}$：cpu的使用率目标
  * $W/C$：等待时间/计算时间的比率
  * 设置线程数量$N_{threads} = N_{cpu} * U_{cpu}*(1+W/C) $
* IO密集型：2 * core + 1
* CPU密集型：CPU+1
