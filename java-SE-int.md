# java基础面试题

## 基础

### java创建对象的方式

在Java中，创建对象主要有以下几种方式：

1. **使用`new`关键字**：这是最常见的创建对象的方式。例如：`Student s = new Student();`

2. **使用`Class`类的`newInstance`方法**：这种方式通常用于反射。例如：

```java
Class<Student> cls = Student.class;
Student s = cls.newInstance();
```

3. **使用`Constructor`类的`newInstance`方法**：这种方式也是反射的一种，可以用于创建带参数的对象。例如：

```java
Constructor<Student> constructor = Student.class.getConstructor();
Student s = constructor.newInstance();
```

4. **使用`clone`方法**：如果**一个类实现了`Cloneable`接口，那么可以通过调用`clone`方法来创建对象。**例如：

```java
Student s1 = new Student();
Student s2 = (Student) s1.clone();
```

5. **使用反序列化**：如果一个对象已经被序列化，那么可以通过反序列化来创建新的对象。例如：

```java
ObjectInputStream in = new ObjectInputStream(new FileInputStream("data.obj"));
Student s = (Student) in.readObject();
```

****

### class 文件的结构

```shell
javap -verbose xxx.class
```

Java的`.class`文件是一种包含了Java程序编译结果的二进制文件。它的结构非常复杂，包含了许多不同的部分。以下是`.class`文件的主要结构：

1. **魔数（Magic Number）**：这是一个用于标识文件类型的标记，对于Java `.class`文件，这个值是`0xCAFEBABE`。

2. **版本号**：这部分包含了`.class`文件的主版本号和次版本号，用于标识`.class`文件的版本。

3. **常量池（Constant Pool）**：这是`.class`文件中的一个重要部分，它包含了`.class`文件中所有的常量信息，包括字符串常量、类和接口名、字段名等。

4. **访问标志（Access Flags）**：这部分标识了类或接口的访问权限，如`public`、`private`、`protected`、`static`、`final`等。

5. **类信息**：这部分包含了类或接口的名称、父类的名称以及实现的接口。

6. **字段表（Fields）**：这部分包含了类或接口中声明的所有字段的信息。

7. **方法表（Methods）**：这部分包含了类或接口中声明的所有方法的信息。

8. **属性表（Attributes）**：这部分包含了类、字段、方法等的附加信息，如异常表、源文件名、行号表等。

****

### java临界资源

在Java中，临界资源通常指的是在多线程环境下，同时被多个线程访问而可能引发问题的资源。以下是一些常见的临界资源：

1. **共享数据**：这是最常见的临界资源。如果多个线程同时读写同一份数据，而没有进行适当的同步控制，就可能导致数据的不一致。
2. **文件**：如果多个线程同时读写同一个文件，也可能导致数据的不一致或其他问题。
3. **数据库连接**：如果多个线程共享同一个数据库连接，而没有进行适当的同步控制，可能会导致数据库操作的错误。
4. **网络连接**：如果多个线程共享同一个网络连接，也可能会导致数据的不一致或其他问题。
5. **其他系统资源**：如打印机、图形设备等，如果多个线程同时访问，也可能导致问题。

在处理临界资源时，通常需要使用同步机制（如`synchronized`关键字、`Lock`接口等）来确保数据的一致性和线程的安全。

****

### java抽象类和接口的区别

Java中的抽象类和接口都是用于抽象和封装设计的工具，但它们有一些关键的区别：

1. **默认方法实现**：抽象类可以有默认的方法实现，接口完全是抽象的，不能有任何方法实现（Java 8开始，接口可以有默认方法和静态方法）。

2. **成员变量**：**抽象类中可以有成员变量，接口中只能有静态不可变的常量。**

3. **继承限制**：一个类可以实现多个接口，但只能继承一个抽象类。

4. **构造器**：**抽象类可以有构造器，接口不能有构造器。**

5. **访问修饰符**：接口中所有的方法默认都是public的，而抽象类可以有public、protected和default方法。

6. **使用**：如果你需要创建一个对象的“模板”，并且预计会有很多不同的版本，那么使用抽象类。如果你需要定义一个对象的接口，那么使用接口。

总的来说，抽象类和接口都是OOP设计的重要工具，它们有各自的用途和优点。在设计你的类时，应根据具体的需求来选择使用抽象类还是接口。

****

### java三大特性

Java的三大特性，也被称为面向对象编程（OOP）的三大特性，包括：

1. **封装（Encapsulation）**：封装是指将对象的状态（属性）和行为（方法）包装在一起，隐藏对象的内部细节，只提供有限的访问接口。封装可以增强安全性和简化编程，使用者只需要知道对象提供了哪些方法，而不需要知道这些方法的具体实现。

2. **继承（Inheritance）**：继承是一种可以让我们创建新类并复用现有类的代码的机制。子类可以继承父类的属性和方法，同时还可以添加自己特有的属性和方法。

3. **多态（Polymorphism）**：多态是指允许一个接口使用多种实际类型的能力。一个接口的引用可以指向（引用）其任何实现类的对象。程序在运行时会根据对象的实际类型来调用其相应的方法。

*****

### 深拷贝和浅拷贝

深拷贝和浅拷贝是编程中常见的两种复制对象的方式，主要区别在于是否复制对象的引用类型字段。

**浅拷贝**：创建一个新对象，然后将当前对象的非静态字段复制到该新对象，无论这个字段是值类型还是引用类型。**如果字段是值类型，那么对应的值就被复制过去；如果字段是引用类型，则复制的是引用，也就是说新对象和原对象的引用类型字段指向的是同一个对象。**

**深拷贝**：创建一个新对象，然后将当前对象的非静态字段复制到该新对象，对于引用类型的字段，深拷贝会创建一个新的对象，并复制其内容，这样新对象和原对象的引用类型字段指向的是两个不同的对象。

举个例子，假设有一个`Person`类，包含`name`（String类型）和`address`（Address类型）两个字段。浅拷贝会创建一个新的`Person`对象，但`address`字段仍然指向原来的`Address`对象。而深拷贝会创建一个新的`Person`对象，并且还会创建一个新的`Address`对象，新的`Person`对象的`address`字段指向新的`Address`对象。

**在Java中，可以通过实现`Cloneable`接口并重写`clone()`方法来实现浅拷贝。**要实现深拷贝，可以通过序列化（Serialization）或者手动复制所有字段来实现。

****

### 反射为什么效率低

反射在Java中是一种强大的工具，它允许在运行时动态地访问和修改类的信息。然而，使用反射通常会比直接的Java代码运行得慢，主要有以下几个原因：

1. **类型检查**：在正常的Java代码中，类型检查是在编译时进行的。但是在使用反射时，类型检查必须在运行时进行，这会增加额外的开销。
2. **方法调用**：使用反射调用方法时，需要通过`Method.invoke`方法，这个方法需要处理很多事情，比如访问权限检查，参数包装和解包装等，这些都会增加额外的开销。
3. **字段访问**：使用反射访问字段时，也需要通过`Field.get`或`Field.set`方法，这些方法同样需要处理访问权限检查和类型转换等问题，增加了额外的开销。
4. **动态分派**：反射方法调用需要进行动态分派，即在运行时确定要调用的具体方法。而直接的Java方法调用可以在编译时就确定下来，或者通过虚方法表进行快速查找。
5. **优化难度**：JVM的即时编译器（JIT）可以对常规的Java代码进行各种优化，如内联，循环展开等。但是对于反射代码，由于其动态性，很多优化都无法进行。

****

### final关键字

`final`关键字在Java中有三种用途：

1. 创建常量：当`final`关键字用在变量前面时，该变量的值就不能被改变，它就成为了一个常量。
2. 防止方法被重写：当`final`关键字用在方法前面时，子类不能重写（override）这个方法。
3. 防止类被继承：当`final`关键字用在类前面时，这个类不能被继承。

****

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

> HashSet如何去重

首先根据hashCode方法比较两者的哈希值，然后使用equals方法比较两个对象是否真的相同

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

```
(h = key.hashCode()) ^ (h >>> 16)
(n - 1) & hash
```

![image-20240426194740560](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240426194740560.png)

### hashmap为什么要用红黑树

在Java 8中，HashMap的实现进行了一些优化，当链表的长度超过一定阈值（默认为8）时，链表会被转换为红黑树。这是为了提高HashMap在处理哈希冲突时的效率。

链表的查找操作的时间复杂度是O(n)，其中n是链表的长度。当链表长度很长时，查找效率会很低。而红黑树是一种自平衡的二叉查找树，它的查找操作的时间复杂度是O(log n)，所以当链表长度超过一定阈值时，使用红黑树代替链表**可以大大提高查找效率。**

同时，**红黑树相比于其他的平衡二叉树（如AVL树，左右子树高度必须相差不超过1）来说，其插入和删除操作的效率更高**，更适合用于HashMap这种需要频繁插入和删除的场景。

###  Arrays.sort底层是什么排序算法

在 Java 中，`Arrays.sort()` 方法的底层排序算法取决于输入数据类型的不同。

1. **基本数据类型**（如 `int`、`double` 等）：
   - 对于基本数据类型，`Arrays.sort()` 使用的是一种名为 **Dual-Pivot Quicksort** 的排序算法。这是一种**改进的快速排序算法**，它在大多数情况下表现得很好，并且在平均情况下具有 O(n log n) 的时间复杂度。
2. **对象类型**（如 `String`、自定义对象等）：
   - 对于对象类型，`Arrays.sort()` 使用的是 **TimSort** 算法(改编自python)。**TimSort 是结合了归并排序（Merge Sort）和插入排序（Insertion Sort）的排序算法**，并且针对部分有序的数组有很好的性能。它在大多数情况下表现优异，并且具有 O(n log n) 的时间复杂度。

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

```java
//CountDownLatch
import java.util.concurrent.CountDownLatch;

CountDownLatch latch1 = new CountDownLatch(1);
CountDownLatch latch2 = new CountDownLatch(1);

Thread T1 = new Thread(() -> {
    // T1的代码
    latch1.countDown();
});

Thread T2 = new Thread(() -> {
    try {
        latch1.await();
        // T2的代码
        latch2.countDown();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

Thread T3 = new Thread(() -> {
    try {
        latch2.await();
        // T3的代码
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

T1.start();
T2.start();
T3.start();
```

```java
//CyclicBarrier
import java.util.concurrent.CyclicBarrier;

CyclicBarrier barrier1 = new CyclicBarrier(2);
CyclicBarrier barrier2 = new CyclicBarrier(2);

Thread T1 = new Thread(() -> {
    // T1的代码
    try {
        barrier1.await();
    } catch (Exception e) {
        e.printStackTrace();
    }
});

Thread T2 = new Thread(() -> {
    try {
        barrier1.await();
        // T2的代码
        barrier2.await();
    } catch (Exception e) {
        e.printStackTrace();
    }
});

Thread T3 = new Thread(() -> {
    try {
        barrier2.await();
        // T3的代码
    } catch (Exception e) {
        e.printStackTrace();
    }
});

T1.start();
T2.start();
T3.start();
```

```java
//Semphore
import java.util.concurrent.Semaphore;

Semaphore sem1 = new Semaphore(0);
Semaphore sem2 = new Semaphore(0);

Thread T1 = new Thread(() -> {
    // T1的代码
    sem1.release();
});

Thread T2 = new Thread(() -> {
    try {
        sem1.acquire();
        // T2的代码
        sem2.release();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

Thread T3 = new Thread(() -> {
    try {
        sem2.acquire();
        // T3的代码
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

T1.start();
T2.start();
T3.start();
```

> notify和 notifyAll有什么区别？

- **notifyAll**:唤醒所有wait的线程
- **notify**：根据调度唤醒一个wait线程

> 在java中wait和sleep方法的不同?

**共同点**

wait() , wait(long) 和 sleep(long) 的效果都是**让当前线程暂时放弃CPU的使用权**，进入阻塞状态

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
  - 打断正常的线程，可以根据打断状态来标记是否退出线程(其实也就是方式1).

> 多线程暂停线程的方式

1. **使用`sleep`方法**：`Thread.sleep(millis)`方法可以使当前线程暂停指定的毫秒数。这是一个静态方法，会使调用它的线程暂停执行。需要注意的是，`sleep`方法可能会抛出`InterruptedException`。
2. **使用`wait`方法**：`wait`方法是`Object`类的一个方法，它会使当前线程进入等待状态，直到其他线程调用同一个对象的`notify`或`notifyAll`方法。`wait`方法也可能会抛出`InterruptedException`。
3. **使用`join`方法**：`join`方法会使当前线程等待，直到调用`join`方法的线程结束。`join`方法也可能会抛出`InterruptedException`。
4. **使用`LockSupport.park`方法**：`LockSupport.park()`方法可以暂停当前线程，直到其他线程调用`LockSupport.unpark(线程变量)`方法。

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
  * **考虑两个线程A和B，线程A负责初始化一个对象并将其引用赋值给一个共享变量，线程B负责检查这个共享变量是否已经被初始化。如果线程A的指令被重排，那么可能先将引用赋值给共享变量，然后再初始化对象。这样，线程B可能会看到一个还没有完全初始化的对象。**
* 不保证原子性
  * 举例来说，如果一个变量的更新操作涉及读取、修改和写入三个步骤，而这些步骤之间没有被同步控制，那么即使这个变量被声明为 `volatile`，其他线程仍然有可能在这些步骤之间插入自己的操作，导致最终结果出现异常。

****

#### AQS

全称是AbstractQueuedSynchronizer，即**抽象队列同步器。它是构建锁或者其他同步组件的基础框架**

![image-20240427145324512](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427145324512.png)

AQS**常见的实现类**

- ReentrantLock 阻塞式锁
- Semaphore 信号量
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

Java Virtual Machine Java程序的运行环境（java二进制字节码（class）的运行环境）

好处：

* 一次编写，到处运行
* **自动内存管理，垃圾回收机制**

![image-20240428131327870](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428131327870.png)

> Java虚拟机（JVM）主要由以下几个部分组成：
>
> 1. **类加载器（Class Loader）**：负责加载.class文件，每个类只会被加载一次。
> 2. **运行时数据区（Runtime Data Area）**：包括方法区（Method Area）、堆（Heap）、虚拟机栈（Java Stacks）、本地方法栈（Native Method Stacks）和程序计数器（Program Counter Register）。
> 3. **执行引擎（Execution Engine）**：负责执行字节码指令。
> 4. **本地方法接口（Java Native Interface，JNI）**：提供了一种方式，使得Java代码可以调用或被本地应用（如C、C++）调用。
> 5. **本地方法库（Native Method Libraries）**：这是一组用于实现本地方法的库。
>
> interpreter：
>
> - 在Java虚拟机（JVM）的执行引擎中，解释器（Interpreter）的主要任务是读取字节码，然后逐条解释执行。这种方式的优点是实现简单，启动速度快，但是执行效率较低。
> - 当Java程序开始运行时，解释器会先开始工作，逐条解释执行字节码。这种方式可以快速启动程序，但是对于一些被频繁执行的代码（例如循环、热点方法等），解释执行的效率较低。
> - 为了提高执行效率，JVM引入了即时编译器（JIT Compiler）。当一段代码被频繁执行时，即时编译器会将这段代码编译成本地机器代码，以便直接执行，从而提高执行效率。
>
> JIT：
>
> - JIT（Just-In-Time）编译器是Java虚拟机（JVM）的一个重要组成部分，它的主要任务是将字节码（Bytecode）编译成本地机器代码，以提高程序的执行效率。
> - **在Java程序运行的初期，字节码通常会被解释执行**，但是对于一些被频繁调用的代码（例如循环、热点方法等），解释执行的效率较低。这时，JIT编译器就会将这些代码编译成本地机器代码，以便直接执行，从而提高执行效率。
> - JIT编译器还可以进行一些优化，例如方法内联（Method Inlining）、死代码消除（Dead Code Elimination）、循环优化（Loop Optimization）等，以进一步提高代码的执行效率。

### JVM组成

#### 什么是程序计数器

**程序计数器：**线程私有(无线程安全问题)的，**内部保存的字节码的行号。用于记录正在执行的字节码指令的地址**。

```shell
javap -v xxx.class # 用于反编译 .class 文件并输出其详细的字节码信息
```

![image-20240428132554355](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428132554355.png)

****

#### java 堆

**线程共享的区域（有线程安全问题）：**主要用来**保存对象实例，数组等**，当堆中没有内存空间可分配给实例，也无法再扩展时，则抛出Out Of Memory Error异常。

![image-20240428133121796](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428133121796.png)

**元空间主要用于存储类的元数据，这包括类的名称、字段的信息、方法的信息等。此外，元空间还存储了常量池、静态变量、即时编译器优化后的代码等**

java7 是将方法区或者称之为 永久代 放于堆中，但随着程序的运行，方法区会越来越大，变得不可控，故移至本地内存

这种改变本质来说就是为了避免OOM

****

#### 什么是虚拟机栈

> **变量**：局部变量、方法参数和返回值存储在栈（Stack）中。每当一个方法被调用时，JVM都会在栈中创建一个新的栈帧（Stack Frame），这个栈帧用于存储这个方法的局部变量、参数和返回值。

![image-20240428134620116](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428134620116.png)

> 垃圾回收是否涉及栈内存？

垃圾回收主要指就是堆内存。当栈帧弹栈以后，内存就会释放。**不涉及！**

> 栈内存分配越大越好吗？

未必，默认的栈内存通常为1024k，一般不需要我们修改。

栈帧过大会导致线程数变少，例如，机器总内存为512m，目前能活动的线程数则为512个，如果把栈内存改为2048k，那么**活动线程**数就会减半。

> 方法内的局部变量是否线程安全？

* 如果方法内**局部变量没有逃离方法的作用范围**(比如return 局部变量)，它是线程安全的
* 如果是**局部变量引用了对象，并逃离方法的作用范围**，需要考虑线程安全

> 栈内存溢出情况

* 栈帧过多导致栈内存溢出，**典型问题：递归调用**

* 栈帧过大导致栈内存溢出, 一般不会出现

> Java虚拟机（JVM）的本地方法栈（Native Method Stack）是用来支持Java的Native方法执行的。Native方法是用Java以外的语言（如C、C++等）编写的方法，它们通常用来执行一些和系统相关的、需要高 效率的、或者和硬件交互的任务。
>
> 当一个线程调用一个Native方法时，JVM会进入一个全新的工作区域，这个工作区域就是本地方法栈。在本地方法栈中，JVM不再有执行控制权，它将控制权交给了Native方法。当Native方法执行完毕后，它会返回给Java线程，然后JVM会恢复执行控制权。

#### 能不能解释一下方法区（元空间）？

![image-20240428140105643](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428140105643.png)



> 常量池

![image-20240428141445567](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428141445567.png)

![image-20240428141526420](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428141526420.png)

> 常量池（Constant Pool）和运行时常量池（Runtime Constant Pool）是Java虚拟机（JVM）中的两个重要概念，它们的主要区别在于生命周期和存储内容。
>
> 1. **常量池**：常量池是Java类文件（.class文件）的一部分，它包含了一个Java类或接口中的常量信息，包括直接常量（如字符串、整数、浮点数等）和符号引用（如类和接口的全限定名、字段的名称和描述符、方法的名称和描述符等）。常量池是静态的，它的内容在类文件创建时就已经确定，不会在运行时改变。
> 2. **运行时常量池**：运行时常量池是每个类或接口在JVM内存中的表示形式的一部分，它在类或接口被加载到JVM时创建。运行时常量池包含了常量池的所有信息，以及可能会在运行时新生成的常量（如String的intern()方法可能会在运行时将新的字符串添加到运行时常量池）。运行时常量池是动态的，它的内容可以在运行时改变。
>
> 总的来说，常量池是类文件的一部分，它的内容在编译时就已经确定；而运行时常量池是类或接口在JVM内存中的表示形式的一部分，它的内容可以在运行时改变。

****

#### 你听过直接内存吗？

直接内存（Direct Memory）是Java中的一种特殊类型的内存，**它并不是Java虚拟机堆内存的一部分，而是直接分配在本地内存中的。**

**直接内存主要被用于NIO**（New Input/Output）这种基于通道（Channel）与缓冲区（Buffer）的I/O方式。当我们读取或写入一个文件时，如果使用**传统的I/O流，数据会被读取到JVM堆内存中，然后再从堆内存写入到文件中。**这就涉及到在堆内存和本地内存之间进行数据拷贝，这个过程的开销是相当大的。

而如果使用**直接内存，数据可以直接被读取到本地内存中，然后再从本地内存写入到文件中**，避免了在堆内存和本地内存之间进行数据拷贝，从而提高了I/O操作的性能。

***需要注意的是，虽然直接内存的性能更高，但是它的分配和回收成本都比堆内存要高，因为不受jvm内存回收管理。因此，只有当进行大量的I/O操作，且数据量较大时，使用直接内存才会带来性能上的优势。***

> Java的NIO（New I/O）和Linux的非阻塞I/O以及I/O多路复用都是为了提高I/O效率而设计的技术，但它们的工作方式有所不同。
>
> 1. **非阻塞I/O**：在Linux中，非阻塞I/O是指当一个I/O操作不能立即完成时，不会阻塞当前线程，而是立即返回。这样，线程可以继续执行其他任务，而不是等待I/O操作完成。但是，线程需要不断地轮询I/O操作是否完成，这会消耗一定的CPU资源。
> 2. **I/O多路复用**：I/O多路复用是指使用一个或少数几个线程来管理多个I/O操作。当一个I/O操作准备好（例如，数据已经准备好可以被读取，或者可以写入数据）时，线程会被通知，然后进行数据的读写。这样，线程不需要不断地轮询I/O操作是否完成，可以更有效地利用CPU资源。
> 3. **Java NIO**：Java的NIO更类似于I/O多路复用。在Java NIO中，可以使用一个Selector来管理多个Channel，当一个Channel准备好（例如，数据已经准备好可以被读取，或者可以写入数据）时，Selector会被通知，然后进行数据的读写。这样，一个线程就可以管理多个I/O操作，提高了I/O效率。

****

### 类加载器

> 类加载器（ClassLoader）和类加载子系统（Class Loading Subsystem）是Java虚拟机（JVM）中的两个重要概念，它们之间的关系是：类加载器是类加载子系统的一部分，负责实现类加载子系统的主要功能。
>
> 1. **类加载子系统**：类加载子系统是JVM的一个重要组成部分，它负责从文件系统或网络中加载Java类，然后将这些类转换成JVM可以执行的内部表示形式。类加载子系统主要包括以下几个步骤：加载（Loading）、链接（Linking）和初始化（Initialization）。
> 2. **类加载器**：类加载器是类加载子系统的一部分，它负责实现类加载子系统的加载步骤。类加载器从文件系统或网络中读取Java类的字节码，然后将这些字节码转换成JVM内部的数据结构（即类）。Java提供了一些内置的类加载器，如系统类加载器（System ClassLoader）、扩展类加载器（Extension ClassLoader）和引导类加载器（Bootstrap ClassLoader），同时也允许用户自定义类加载器。

#### 什么是类加载器，类加载器有哪些

*JVM只会运行二进制文件，类加载器的作用就是将字节码文件加载到JVM中，从而让Java程序能够启动起来*

![image-20240428142842903](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428142842903.png)

****

#### 双亲委派机制

***如果要加载某一个类，先委托上一级的加载器进行加载，如果上级加载器也有上级，则会继续向上委托，如果该类委托上级没有被加载，子加载器尝试加载该类***

为什么要使用这样的机制？

- 通过双亲委派机制可以避免某一个类被重复加载，当父类已经加载后则无需重复加载，保证唯一性
- 为了安全，保证类库API不会被修改

![image-20240428143825383](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428143825383.png)

> 如何把两个全限定名相同的类加载到jvm

通过自定义类加载器，当两个类加载器不同，这两个类就不同

****

#### 类加载的执行流程

类从加载到虚拟机中开始，直到卸载为止，它的整个生命周期包括了：加载、验证、准备、解析、初始化、使用和卸载这7个阶段。其中，验证、准备和解析这三个部分统称为连接(linking)

![image-20240428150412791](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428150412791.png)

**加载：**

- 通过类的全名，获取类的二进制数据流。
- 解析类的二进制数据流为方法区内的数据结构（Java类模型）
- 创建java.lang.Class类的实例，表示该类型。作为方法区这个类的各种数据的访问入口

其实就是把类的二进制数据流读入到运行时数据区，将类信息存在元空间，将类class对象存在堆中，方便后期创建对象使用。

![image-20240428150857010](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428150857010.png)

****

**验证：**验证类是否符合JVM规范，安全性检查

(1)文件格式验证

(2)元数据验证格式检查

(3)字节码验证

***前三项都是格式检查，如：文件格式是否错误、语语法是否错误、字节码是否合规***

(4)符号引用验证

- Class文件在其常量池会通过字符串记录自己将要使用的其他类或者方法，检查它们是否存在

****

**准备：**为类变量(static 修饰的变量)分配内存并设置类变量初始值

- static变量，分配空间在准备阶段完成（设置默认值），赋值在初始化阶段完成 ` static`
- static变量是final的基本类型，以及字符串常量，值已确定，赋值在准备阶段完成 `static final int`
- static变量是final的引用类型，那么赋值也会在初始化阶段完成 `static final Object`

****

**解析：**把类中的符号引用转换为直接引用

方法中调用了其他方法，方法名可以理解为符号引用，而直接引用就是使用指针直接指向方法。

![image-20240428151826373](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428151826373.png)

****

**初始化：**对类的静态变量，静态代码块执行初始化操作

如果初始化一个类的时候，其父类尚未初始化，则优先初始化其父类

如果同时包含多个静态变量和静态代码块，则按照自上而下的顺序依次执行。

****

**使用：**

JVM开始从入口方法开始执行用户的程序代码

- 调用静态类成员信息(比如：静态字段、静态方法）
- 使用new关键字为其创建对象实例

****

**卸载：**用户程序结束之后，jvm就会销毁创建的class对象

****

### 垃圾回收

在学习Java GC 之前，我们需要记住一个单词：

* stop-the-world(STW) 。它会在任何一种GC 算法中发生。
* stop-the-world 意味着JVM因为需要执行GC而停止了应用程序的执行。**当stop-the-world 发生时，除GC所需的线程外，所有的线程都进入等待状态，直到GC任务完成。**GC 优化很多时候就是减少 stop-the-world 的发生或者说是减少 stop-the-world 的时间。

#### 对象什么时候可以被垃圾器回收

垃圾回收主要指堆中区域。

***简单一句就是：如果一个或多个对象没有任何的引用指向它了，那么这个对象现在就是垃圾，如果定位了垃圾，则有可能会被垃圾回收器回收。***

如果要定位什么是垃圾，有两种方式来确定**，第一个是引用计数法，第二个是可达性分析算法**

>引用计数法

一个对象被引用了一次，**在当前的对象头上递增一次引用次数**，如果这个对象的引用次数为0，代表这个对象可回收

当对象间出现了循环引用的话则引用计数法就会失效，从而导致内存泄漏

![image-20240428152823366](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428152823366.png)

> 可达性分析算法

现在的虚拟机采用的都是通过可达性分析算法来确定哪些内容是垃圾。

![image-20240428152934401](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428152934401.png)

> 哪些对象可以作为 GC Root 呢

![image-20240428153136375](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428153136375.png)

这里的虚拟机栈指的就是正在活动的线程使用的对象

****

#### JVM垃圾回收算法有哪些？

- 复制算法 年轻代垃圾回收器使用多
- 标记清除算法
- 标记整理算法 老年代垃圾回收器使用多

> 复制算法 年轻代

复制算法：将原有的内存空间一分为二，每次只用其中的一块，正在使用的对象复制到另一个内存空间中，然后将该内存空间清空，交换两个内存的角色，完成垃圾的回收

![image-20240428153826287](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428153826287.png)

> 标记清除算法 使用较少

标记清除算法，是将垃圾回收分为2个阶段，分别是**标记和清除**

- 根据**可达性分析算法得出的垃圾进行标记**
- 对这些标记为可回收的内容进行垃圾回收

![image-20240428153544603](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428153544603.png)

> 标记整理算法 老年代

标记整理算法，是将垃圾回收分为3个阶段，分别是**标记和清除和整理**

- 根据**可达性分析算法得出的垃圾进行标记**
- 对这些标记为可回收的内容进行垃圾回收
- 移动内存，使其连贯

![image-20240428153658636](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428153658636.png)

****

#### 说一下JVM中的分代回收

在java8时，堆被分为了两份：新生代和老年代【1：2】

![image-20240428154244624](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428154244624.png)

![image-20240428154354950](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428154354950.png)

> MinorGC.MixedGC、FullGC的区别是什么

> 1. **Minor GC**：Minor GC主要清理新生代（Young Generation）的内存空间。新生代通常分为一个Eden区和两个Survivor区（S0和S1）。大部分新创建的对象首先会被分配到Eden区，**当Eden区满时，就会触发Minor GC。**Minor GC会清理掉Eden区中不再使用的对象，并将还在使用的对象移动到Survivor区或者老年代（Old Generation）。Minor GC的执行频率较高，但每次执行的速度较快。
> 2. **Full GC**：Full GC会清理整个Java堆，包括新生代和老年代。Full GC通常在以下情况下被触发：老年代空间不足、持久代（PermGen，Java8之前）或元空间（Metaspace，Java8之后）空间不足、System.gc()被显式调用、上一次GC后Heap的增量超过了设定的比例。Full GC的执行频率较低，但每次执行的时间较长，因为需要清理整个Java堆。
> 3. **Mixed GC**：Mixed GC是在Java 8中引入的一种新的GC类型，主要用于**在新生代和部分老年代之间进行垃圾回收。**Mixed GC的主要目标是在不触发Full GC的情况下，尽可能地清理掉老年代中的垃圾对象，以此来避免执行时间较长的Full GC。Mixed GC通常在新生代的Minor GC之后，以及老年代的使用率达到一定阈值时被触发。

![image-20240428162931857](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428162931857.png)

****

#### 说一下JVM有哪些垃圾回收器？

在jvm中，实现了多种垃圾收集器，包括：

* 串行垃圾收集器
* 并行垃圾收集器 jdk默认垃圾回收器
* CMS(并发)垃圾收集器 老年代垃圾回收
* G1垃圾收集器

> 串行垃圾收集器

![image-20240428163244851](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428163244851.png)

> 并行垃圾收集器 jdk默认垃圾回收器

![image-20240428163314345](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428163314345.png)

> CMS(并发)垃圾收集器

![image-20240428163712580](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428163712580.png)

![image-20240428163952746](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240428163952746.png)

CMS收集器的运作过程主要分为四个步骤：

1. **初始标记（Initial Mark）**：这个阶段需要"Stop The World"，即暂停所有的其他线程，然后标记所有的根对象，包括根直接引用的对象，以及通过根对象可达的对象。
2. **并发标记（Concurrent Mark）**：在此阶段，CMS收集器会遍历堆中的对象，找出存活的对象。这个阶段是与用户线程并发执行的，不需要停止用户线程。
3. **重新标记（Remark）**：这个阶段需要"Stop The World"，主要是为了修正并发标记期间，因用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间通常比初始标记阶段的时间长，但是远比并发标记的时间短。
4. **并发清除（Concurrent Sweep）**：此阶段与用户线程并发执行，不需要停止用户线程，此阶段完成后，CMS收集器会进行一次碎片整理，整理出连续的内存空间。

CMS收集器的优点是并发收集、低停顿，这使得CMS收集器成为很多以响应速度为要求的Java应用的首选，比如网站或者B/S系统的服务器端等。但是CMS收集器对CPU资源非常敏感，同时，CMS收集器无法处理浮动垃圾，可能会出现"Concurrent Mode Failure"失败，需要进行Full GC的清理动作。**另外，CMS也有碎片问题，因为并发清理阶段用户线程还在运行着，不可能像其他收集器一样进行压缩整理，也就无法保证给大对象分配足够的连续空间，**所以在老年代使用CMS收集器时，有可能会出现多次Minor GC后，老年代还有大量空间，但是无法找到足够大的连续空间来分配当前对象，导致提前触发Full GC。

****

#### 详细聊一下G1(Garbage first)垃圾回收器

* **应用于新生代和老年代，在JDK9之后默认使用G1**
* 划分成多个区域，每个区域都可以充当eden，survivor，old，humongous，其中humongous专为大对象准备

* **采用复制算法!!!!!!!!!!!!!!!** 
* 响应时间与吞吐量兼顾
* 分成三个阶段：新生代回收、并发标记、混合收集
* **如果并发失败（即回收速度赶不上创建新对象速度），会触发Full GC**

![image-20240429151002812](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429151002812.png)

> 新生代回收 eden区占5%-6% 会触发 stw 时间短

![image-20240429151216078](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429151216078.png)

![image-20240429151329484](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429151329484.png)

> 年轻代垃圾回收+并发标记

![image-20240429151457239](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429151457239.png)

![image-20240429151601637](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429151601637.png)

![image-20240429151707868](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429151707868.png)

复制完成，内存得到释放。进入下一轮的新生代回收、并发标记、混合收集

如果对象过大，会存在连续的区域之中。

> G1（Garbage-First）垃圾回收器是一种面向服务器的垃圾回收器，它在Java 7中被引入，从Java 9开始成为默认的垃圾回收器。G1垃圾回收器的主要目标是提供高吞吐量以及可预测的停顿时间，即尽可能地减少垃圾回收过程中的暂停时间。
>
> G1垃圾回收器的工作原理如下：
>
> 1. **划分区域**：G1垃圾回收器将堆内存划分为多个大小相等的独立区域（Region），每个区域可能是Eden区、Survivor区或者Old区。
> 2. **并行与并发**：G1垃圾回收器在新生代的垃圾回收阶段（即Minor GC）使用并行（Parallel）方式，多个GC线程同时工作，但是用户线程会被暂停；在老年代的垃圾回收阶段，G1垃圾回收器使用并发（Concurrent）方式，GC线程和用户线程同时进行。
> 3. **回收过程**：G1垃圾回收器的回收过程包括初始标记（Initial Marking）、并发标记（Concurrent Marking）、最终标记（Final Marking）和筛选回收（Live Data Counting and Evacuation）四个阶段。
> 4. **Mixed GC**：G1垃圾回收器引入了Mixed GC，它在新生代进行Minor GC的同时，也清理掉老年代中的一部分内存，以避免老年代的内存使用过高导致的Full GC。
> 5. **可预测的停顿时间**：**G1垃圾回收器可以设置期望的停顿时间目标，它会根据这个目标来决定每次回收多少区域的内存**，根据时间优先回收存活对象少的老年区。

****

#### 强引用、弱引用、软引用、虚引用的区别

![image-20240429152239078](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429152239078.png)

![image-20240429152328717](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429152328717.png)

![image-20240429152603054](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429152603054.png)

> 在Java中，根据对象的生命周期，引用可以分为强引用、软引用、弱引用和虚引用四种：
>
> 1. **强引用（Strong Reference）**：这是最常见的引用类型。如果一个对象具有强引用，那么垃圾回收器绝不会回收它。当内存空间不足，Java虚拟机宁愿抛出OutOfMemoryError错误，使程序异常终止，也不会靠随意回收具有强引用的对象来解决内存不足问题。**代码中常见的直接引用就是强引用。**
> 2. **软引用（Soft Reference）**：如果一个对象只具有软引用，那么在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围进行第二次回收。如果这次回收还没有足够的内存，才会抛出内存溢出异常。**在Java中，使用SoftReference类来实现软引用。**
> 3. **弱引用（Weak Reference）**：如果一个对象只具有弱引用，那么在垃圾回收器线程扫描它所管辖的内存区域的过程中，只要发现弱引用关联的对象，不管当前内存空间足够与否，都会回收它的内存。**在Java中，使用WeakReference类来实现弱引用。**
> 4. **虚引用（Phantom Reference）**："虚引用"顾名思义，就是形同虚设，与其他几种引用不同，虚引用并不影响对象的生命周期。在Java中，使用PhantomReference类来实现虚引用。**如果一个对象只有虚引用，那么它就和没有任何引用一样，随时都可能被垃圾回收器回收，它不能单独使用，必须配合引用队列（ReferenceQueue）使用。**

### JVM实践

#### JVM调优的参数可以在哪里设置

- war包部署在tomcat中设置
- jar包部署在启动参数设置 docker在dockerfile的env处设置

****

#### 用的JVM调优的参数都有哪些？

对于JVM调优，**主要就是调整年轻代、老年代、元空间的内存空间大小及使用的垃圾回收器类型。**

<img src="https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429153400471.png" alt="image-20240429153400471" style="zoom:50%;" />



<img src="https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429153530713.png" alt="image-20240429153530713" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429153651683.png" alt="image-20240429153651683" style="zoom:50%;" />

<img src="https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429153731013.png" alt="image-20240429153731013" style="zoom: 67%;" />

****

#### 说一下JVM调优的工具？

命令工具

* jps 进程状态信息
* jstack pid **查看java进程内线程**的堆栈信息
* jmap 用于生成堆转内存快照、内存使用情况
* jhat 堆转储快照分析工具
* jstat JVM统计监测工具

可视化工具

* jconsole 用于对jvm的内存，线程，类的监控
* VisualVM 能够监控线程，内存情况

#### Java内存溢出的排查思路？

一共有三处可能导致虚拟机内存溢出的问题

* 虚拟机栈
  * Stack OverFlow Error 一般为递归
* 方法区
  * Out Of Memory Error:Metaspace,
* 堆
  * Out Of Memory Error:java heap space

但是如果是微服务项目或者上线的单体项目，内存溢出可能直接就导致无法启动，此时如何分析呢

* **通过jmap或设置jvm参数获取堆内存快照dump**
* 通过工具，VisualVM去分析dump文件，VisualVM可以加载离线的dump文件
* 通过查看堆信息的情况，可以大概定位内存溢出是哪行代码出了问题
* 找到对应的代码，通过阅读上下文的情况，进行修复即可

#### CPU飚高排查方案与思路？

![image-20240429165527253](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240429165527253.png)

`top`和`ps`都是Linux系统中常用的进程查看命令，但它们的功能和使用场景有所不同。

1. **top命令**：`top`命令可以**实时动态地查看系统的整体运行状态**，包括CPU使用率、内存使用率、运行进程数、负载、运行时间等信息。同时，它还可以显示所有进程的详细信息，包括进程ID、用户ID、CPU使用率、内存使用率、运行时间等。`top`命令的输出会不断刷新，以显示最新的系统状态。
2. **ps命令**：`ps`命令用于显示当前系统中的进程状态，包括进程ID、终端ID、CPU使用时间、进程状态等信息。与`top`命令不同，`ps`命令**只会显示一次进程信息，不会动态刷新**。`ps`命令通常用于查看某个特定进程的状态，或者配合其他命令（如`grep`）使用。

总的来说，`top`命令更适用于需要实时监控系统状态和进程状态的场景，而`ps`命令更适用于需要查看一次性进程信息的场景。

#### 对象一定在堆里面吗

在Java中，对象通常是在堆（Heap）中创建的。这是因为堆是所有线程共享的内存区域，可以容纳大量的数据，而且其生命周期由垃圾回收器管理，适合存放复杂的数据结构如对象和数组。

然而，JVM也提出了一种叫做逃逸分析的技术，通过这种技术，JVM可以判断出哪些对象不会被其他线程访问（即不会“逃逸”出方法或线程），这些对象可以被优化为在栈（Stack）上分配。

逃逸分析的主要步骤如下：

1. JVM分析程序的字节码，找出所有的对象创建操作。
2. 对于每一个对象创建操作，JVM分析这个对象的引用是否会被其他方法或线程访问。如果一个对象只在一个方法中使用，并且不会被其他线程访问，那么这个对象就不会“逃逸”。
3. 对于不会“逃逸”的对象，JVM可以选择在栈上分配这个对象，而不是在堆上分配。

栈上的对象和堆上的对象有以下主要区别：

1. **生命周期**：栈上的对象的生命周期与方法的执行周期相同，当方法执行结束，栈上的对象就会被立即释放。而堆上的对象的生命周期由垃圾回收器管理，当对象不再被引用时，垃圾回收器会在合适的时机回收这个对象。
2. **性能**：栈上的对象分配和回收的效率更高，因为只需要移动栈顶指针。而堆上的对象分配和回收需要更复杂的算法，且可能会引发垃圾回收。
3. **线程安全**：栈上的对象只能被创建它的线程访问，因此不需要考虑线程同步的问题。而堆上的对象可以被所有线程访问，如果多个线程同时访问一个对象，就需要考虑线程同步的问题。
