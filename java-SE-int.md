# java基础面试题

## 集合

![image-20240426161801822](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240426161801822.png)

### Arraylist

> ArrayList底层的实现原理是什么

* ArrayList底层是用动态的数组实现的

* ArrayList初始容量为0，当第一次添加数据的时候才会初始化容量为10

* ArrayList在进行扩容的时候是原来容量的1.5倍，每次扩容都需要拷贝数组

> ArrayListlist=newArrayList(10)中的list扩容几次

没有扩容

> 如何实现数组和List之间的转换

```java
ArrayList<Object> list = new ArrayList<>();
list.add(1);

Object[] array = list.toArray();// 集合转数组

List<Object> list1 = Arrays.asList(array); //数组转集合
```

**数组转List**，使用JDK中java.util.Arrays工具类的asList方法

**List转数组**，使用List的toArray方法。**无参toArray方法返回Object数组，传入初始化长度的数组对象，返回该对象数组**

> 用Arrays.asList转List后，如果修改了数组内容，list受影响吗
>
> List用toArray转数组后，如果修改了List内容，数组受影响吗

Arrays.asList转换list之后，如果修改了数组的内容，list会受影响，**因为它的底层使用的Arrays类中的一个内部类ArrayList来构造的集合**，在这个集合的构造器中，把我们传入的这个集合进行了包装而已，**最终指向的都是同一个内存地址**

list用了toArray转数组后，如果修改了list内容，数组不会影响，**当调用了toArray以后，在底层是它是进行了数组的拷贝，跟原来的元素就没关系了**，所以即使list修改了以后，数组也不受影响

****

### LinkedList

> ArrayList和LinkedList的区别是什么？

![image-20240426170240869](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240426170240869.png)

![image-20240426170319267](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240426170319267.png)

### 散列表

散列表(HashTable)又名哈希表/Hash表，是根据键（Key）直接访问在内存存储位置值（Value）的数据结构，它是由数组演化而来的，**利用了数组支持按照下标进行随机访问数据的特性**

**将键(key)映射为数组下标的函数叫做散列函数**。可以表示为：hashValue=hash(key)

散列函数的基本要求：

* 散列函数计算得到的散列值必须是大于等于o的正整数，因为hashValue需要作为数组的下标。

* 如果key1==key2，那么经过hash后得到的哈希值也必相同即：hash(key1) == hash(key2)

实际的情况下想找一个散列函数能够做到对于不同的key计算得到的散列值都不同几乎是不可能的，即便像著名的MD5,SHA等哈希算法也无法避免这一情况，**这就是散列冲突(或者哈希冲突，哈希碰撞，就是指多个key映射到同一个数组下标位置）**

拉链法，其实就是用链表存储碰撞的值

### HashMap

> 说一下HashMap的实现原理？

HashMap的数据结构：底层使用hash表数据结构，即数组和链表或红黑树

* 当我们往HashMap中put元素时，利用key的hashCode重新hash计算出当前对象的元素在数组中的下标
* 存储时，如果出现hash值相同的key，此时有两种情况。
  * a.如果key相同，则覆盖原始值；
  * b.如果key不同（出现冲突），则将当前的key-value放入链表或红黑树中获取时，直接找到hash值对应的下标，在进一步判断key是否相同，从而找到对应值。

> HashMap的jdk1.7和jdk1.8有什么区别

**JDK1.8之前**采用的是拉链法。拉链法：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。

**jdk1.8**在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为8）时并且数组长度达到64时，将链表转化为红黑树，以减少搜索时间。**扩容resize（）时，红黑树拆分成的树的结点数小于等于临界值6个，则退化成链表**

> HashMap的put方法的具体流程

![image-20240426185717523](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240426185717523.png)

![image-20240426190122244](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240426190122244.png)

> 讲一讲HashMap的扩容机制

![image-20240426192220603](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240426192220603.png)

> hashMap的寻址算法

n-1去与是为了代替取模运算

**如果结果为 0，表示元素在旧数组中的位置与新数组中的位置相同，它们的索引值在新数组中是不变的**，不需要重新计算。这个操作可以保证原本放在同一链表（或红黑树）上的元素仍然保持在同一位置，不会被移动。

**如果结果不为 0，表示元素在新数组中的位置需要重新计算**。新位置的计算方法是将旧位置的索引值加上旧容量（即 oldCap），这是**因为在新数组中，元素的索引值需要扩展一倍才能保证均匀分布。**

![image-20240426194740560](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240426194740560.png)

## JUC

### 线程基础

#### 线程和进程的区别

当一个程序被运行，从磁盘加载这个程序的代码至内存这时就开启了一个进程

一个线程就是一个指令流，将指令流中的一条条指令以一定的顺序交给CPU执行一个进程之内可以分为一到多个线程。

**线程是最小调度单位，进程是资源分配的最小单位**

**二者对比**

* 进程是正在运行程序的实例，进程中包含了线程，每个线程执行不同的任务

* 不同的进程使用不同的内存空间，**在当前进程下的所有线程可以共享进程内存空间**

* 线程更轻量，线程上下文切换成本一般上要比进程上下文切换低(上下文切换指的是从一个线程切换到另一个线程)

#### 并行和并发的区别

**若为单核cpu**，线程实际还是串行执行的，只是切换快，感觉就是并行的。

**一般会将这种线程轮流使用CPU的做法称为并发(concurrent**

并发（concurrent）是同一时间应对（dealingwith）多件事情的能力，多个线程轮流使用一个或多个CPU

并行（parallel）是同一时间动手做（doing）多件事情的能力，4核CPU同时执行4个线程

#### 线程创建的方式

共有四种方式可以创建线程，分别是：

- 继承Thread类
- 实现runnable接口
- 实现Callable接口
- 线程池创建线程

#### 新建T1、T2、T3三个线程，如何保证它们按顺序执行？

可以使用线程中的join方法解决

![image-20240427131002277](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427131002277.png)

或者用lock的condition

> notify和 nnotifyAll有什么区别？

- **notifyAll**:唤醒所有wait的线程
- **notify**：只随机唤醒一个wait线程

> 在java中wait和sleep方法的不同?

**共同点**

wait(），wait(long）和sleep(long) 的效果都是**让当前线程暂时放弃CPU的使用权**，进入阻塞状态

**不同点**

1. 方法归属不同

   - sleep(long)是Thread的静态方法

   - 而waitO，wait(long）都是Object的成员方法，每个对象都有

2. 醒来时机不同
   * 执行sleep(long）和wait(long）的线程都会在等待相应毫秒后醒来
   * wait(long）和wait() 还可以被notify唤醒，wait()如果不唤醒就一直等下去
   * **它们都会被打断唤醒**
3. 锁特性不同 (重点)
   * wait方法的调用必须先获取wait对象的锁即需和syncronized锁配合使用，而sleep则无此限制
   * **wait方法执行后会释放对象锁**，允许其它线程获得该对象锁（我放弃cpu，但你们还可以用）
   * 而sleep如果在synchronized代码块中执行，**并不会释放对象锁**（我放弃cpu，你们也用不了）

> 如何停止一个正在运行的线程？

有三种方式可以停止线程

- 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止
- 使用stop方法强行终止（不推荐，方法已作废）
- 使用interrupt方法中断线程
  - 打断阻塞的线程（sleep，wait，join）的线程，线程会抛出InterruptedException异常
  - 打断正常的线程，可以根据打断状态来标记是否退出线程(其实也就是1)

****

### 线程安全

#### synchronized关键字的底层原理

> synchronized关键字的底层原理

Synchronized【对象锁】采用互斥的方式让**同一时刻至多只有一个线程能持有【对象锁】**，其它线程再想获取这个【对象锁】时就会阻塞住。

**底层原理是Monitor**

![image-20240427133652381](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427133652381.png)

![image-20240427133738734](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427133738734.png)

> 对象锁怎么关联上的Monitor

每个Java对象都可以关联一个Monitor对象，如果使用synchronized给对象上锁（重量级）之后，**该对象头的MarkWord中就被设置指向Monitor对象的指针**

![image-20240427134646633](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427134646633.png)

![image-20240427134724531](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427134724531.png)

****

> Monitor实现的锁属于重量级锁，你了解过锁升级吗？

**Monitor实现**的锁**属于重量级锁**，里面涉及到了用户态和内核态的切换、进程的上下文切换，成本较高，性能比较低。

在JDK1.6引入了两种新型锁机制：偏向锁和轻量级锁，它们的引入是为了**解决在没有多线程竞争或基本没有竞争的场景下因使用传统锁机制带来的性能开销问题。**

![image-20240427140724309](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427140724309.png)

**一旦锁发生了竞争，都会升级成重量级锁！**

****

#### JMM

> 你谈谈JMM(Java内存模型)

JMM(JavaMemoryModel) Java内存模型，**定义了共享内存中多线程程序读写操作的行为规范**，通过这些规则来规范对内存的读写操作从而保证指令的正确性。

JMM把内存分为两块，一块是**私有线程的工作区域（工作内存），一块是所有线程的共享区域（主内存）**

**线程跟线程之间是相互隔离，线程跟线程交互需要通过主内存**

****

#### CAS

> 你谈谈对CAS的理解

CAS的全称是：Compare And Swap(**比较再交换**)，它体现的一种乐观锁的思想，**在无锁情况下保证线程操作共享数据的原子性。**

在JUC（java.util.concurrent）包下实现的很多类都用到了CAS操作

* AbstractQueuedSynchronizer (AQS框架)
* AtomicXxX类

**CAS是基于乐观锁的思想：**

* 最乐观的估计，不怕别的线程来修改共享变量，就算改了也没关系，我吃亏点再重试呗。(自旋)

**synchronized是基于悲观锁的思想：**

* 最悲观的估计，得防着其它线程来修改共享变量，我上了锁你们都别想改，我改完了解开锁，你们才有机会。                                                        

****

#### volatile

> 请谈谈你对volatile的理解

一旦一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后，那么就具备了两层语义：

* 保证线程间的可见性
  * 用volatile修饰共享变量，能够防止编译器等优化发生，让一个线程对共享变量的修改对另一个线程可见
* 禁止进行指令重排序
  * 用volatile修饰共享变量会在读、写共享变量时加入不同的屏障，阻止其他读写操作越过屏障，从而达到阻止重排序的效果
* 不保证原子性
  * 举例来说，如果一个变量的更新操作涉及读取、修改和写入三个步骤，而这些步骤之间没有被同步控制，那么即使这个变量被声明为 `volatile`，其他线程仍然有可能在这些步骤之间插入自己的操作，导致最终结果出现异常。

****

#### AQS

全称是AbstractQueuedSynchronizer，即**抽象队列同步器。它是构建锁或者其他同步组件的基础框架**

![image-20240427145324512](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427145324512.png)

AQS**常见的实现类**

- ReentrantLock 阻塞式锁
- Semaph ore 信号量
- CountDownLatch 倒计时锁

> 虽然 AQS（AbstractQueuedSynchronizer）提供了一种灵活且强大的同步机制，但在 Java 中，synchronized 关键字仍然是最常用的同步工具之一，这是因为 synchronized 具有以下优点：
>
> 1. **简单易用：** synchronized 是 Java 语言内置的关键字，可以直接用于方法或代码块中，使用非常方便，无需额外导入或定义。
> 2. **自动释放锁：** synchronized 在代码执行完毕或抛出异常时会自动释放锁，避免了手动释放锁可能带来的问题。
> 3. **JVM 内置支持：** synchronized 是 JVM 内置的同步机制，底层由 JVM 实现，因此在某些情况下可能比 AQS 更高效。
> 4. **可读性好：** synchronized 通过关键字直观地表明了代码块的同步性质，使得代码更易于理解和维护。
> 5. **适用范围广：** synchronized 可以用于方法、静态方法、实例方法、代码块等多种场景，可以灵活适用于各种情况下的同步需求。

![image-20240427150115756](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427150115756.png)

****

#### ReentrantLock的实现原理

ReentrantLock翻译过来是可重入锁，相对于synchronized它具备以下特点：

* 可中断
* 可以设置超时时间
* 可以设置公平锁 synchronized只能非公平锁
* 支持多个条件(condition)变量
* 与synchronized一样，都支持重入

**ReentrantLock主要利用CAS+AQS队列来实现**。它支持公平锁和非公平锁，两者的实现类似。

构造方法接受一个可选的公平参数（默认非公平锁），当设置为true时，表示公平锁，否则为非公平锁。

**公平锁的效率往往没有非公平锁的效率高**，在许多线程访问的情况下，公平锁表现出较低的吞吐量。

****

#### synchronized和Lock有什么区别

* 语法层面
  * synchronized 是关键字，源码在jvm 中，用c++语言实现
  * Lock 是接口，源码由jdk提供，用java 语言实现使用 synchronized 时，退出同步代码块锁会自动释放，而使用 Lock 时，需要手动调用 unlock 方法释放锁
* 功能层面
  * 二者均属于悲观锁、都具备基本的互斥、同步、锁重入功能
  * Lock提供了许多synchronized 不具备的功能，例如公平锁、**可打断、可超时、多条件变量**
  * Lock有适合不同场景的实现，如ReentrantLock，ReentrantReadWriteLock(读写锁)
* 性能层面
  * 在没有竞争时，synchronized 做了很多优化，如偏向锁、轻量级锁，性能不赖
  * 在竞争激烈时，Lock的实现通常会提供更好的性能

****

#### 导致并发程序出现问题的根本原因是什么

> Java程序中怎么保证多线程的执行安全

根本原因就是违反了Java并发编程三大特性

* 原子性
* 可见性
* 有序性 (指令重排)

**原子性：一个线程在CPU中操作不可暂停，也不可中断，要不执行完成，要不不执行**

****

### 线程池

#### 说一下线程池的核心参数（线程池的执行原理知道吗）

详见 JUC 学习笔记

****

#### 线程池中有哪些常见的阻塞队列

* **ArrayBlockingQueue：基于数组结构的有界阻塞队列，FIFO。**
* **LinkedBlockingQueue：基于链表结构的有界阻塞队列，FIFO。**不给初始值，默认 MAX_INT
* DelayedWorkQueue：是一个优先级队列，它可以保证每次出队的任务都是当前队列中执行时间最靠前的
* SynchronousQueue：不存储元素的阻塞队列，每个插入操作都必须等待一个移出操作。

![image-20240427160910934](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427160910934.png)

****

#### 如何确定核心线程数

详见 JUC 学习笔记

![image-20240427161351885](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427161351885.png)

****

#### 线程池的种类有哪些

详见 JUC 学习笔记 5种

****

#### 为什么不建议用Executors创建线程池

详见 JUC 学习笔记

****

### 使用场景

#### 线程池使用场景(CountDownLatch,Future)（你们项目哪里用到了多线程）

？

#### 数据汇总

> 在一个电商网站中，用户下单之后，需要查询数据，数据包含了三部分：订单信息、包含的商品、物流信息；这三块信息都在不同的微服务中进行实现的，我们如何完成这个业务呢？

![image-20240427163954531](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427163954531.png)



#### 异步调用

注解 @Async("线程池bean名")

#### 如何控制某个方法充许并发访问线程的数量

Semaphore['sema,for]信号量，是JUC包下的一个工具类，底层是AQS，我们可以通过其限制执行的线程数量

****

#### 谈谈你对ThreadLocal的理解

ThreadLocal是多线程中对于解决线程安全的一个操作类，**它会为每个线程都分配一个独立的线程副本从而解决了变量并发访问冲突的问题**。ThreadLocal同时实现了线程内的资源共享

**案例：**使用JDBC操作数据库时，会将每一个线程的Connection放入各自的ThreadLocal中，从而保证每个线程都在各自的Connection上进行数据库的操作，避免A线程关闭了B线程的连接

**ThreadLocal本质来说就是一个线程内部存储类，从而让多个线程只操作自己内部的值，从而实现线程数据隔离**

![image-20240427170556660](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427170556660.png)

> ThreadLocal-内存泄露问题

Java对象中的四种引用类型：**强引用、软引用、弱引用、虚引用**

**强引用：**最为普通的引用方式，表示一个对象处于**有用且必须的状态**，如果一个对象具有强引用，则GC并不会回收它。即便堆中内存不足了**，宁可出现OOM，也不会对其进行回收**

**弱引用：**表示一个对象处于**可能有用且非必须的状态**。在GC线程扫描内存区域时，一旦发现弱引用，就会回收到弱引用相关联的对象。对于弱引用的回收，无关内存区域是否足够，**一旦发现则会被回收**

![image-20240427170439823](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427170439823.png)

## jvm

