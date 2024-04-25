# Redis-interview

![image-20240417194510685](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240417194510685.png)

![image-20240417194524955](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240417194524955.png)

![image-20240417195821923](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240417195821923.png)

## 缓存

### 布隆过滤器

**bitmap**（位图）：**相当于是一个以（bit）位为单位的数组**，数组中每个单元只能存储二进制数0或1

**布隆过滤器作用：**布隆过滤器可以用于检索一个元素是否在一个集合中

其实就是使用hash函数多次加密，使得数组某些位为1，如果查询传来的数据经hash后也在相同位为1，说明该元素在集合中。

但这天然的会有一个问题，也就是hash碰撞，但不同数据散列成相同值，**这就导致了布隆过滤器的误判**

很多布隆过滤器工具都提供了**预期添加数量**和**误判概率**配置参数，它们会根据配置的参数计算出**最佳的位图长度**和**哈希函数数量**。

****

### 缓存穿透

![image-20240417200059774](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240417200059774.png)

恶意多次查询不存在数据，导致数据库宕机

**解决方案：**

* 缓存空数据，查询返回的数据为空，仍将这个结果进行缓存 `key:1 value:null`
  * 优点：简单
  * 缺点：消耗内存，可能会发生不一致的问题（即即使后来数据库更新了该数据，缓存中仍旧为空）
* 使用布隆过滤器
  * 优点：内存占用少，没有多余key，缓存预热(提前将热点数据放入缓存)时，预热布隆过滤器
  * 缺点：实现会复杂一些，存在误判

****

### 缓存击穿

**缓存击穿**：**给某一个key设置了过期时间，当key过期的时候**，恰好这时间点对这个key有大量的并发请求过来，这些并发的请求可能会瞬间把DB压垮

![image-20240417202451046](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240417202451046.png)

当key过期时会重建缓存，但这需要时间！！！！

**解决方案：**

* 互斥锁 强一致但性能差
* 逻辑过期 弱一致，但高可用，性能优

![image-20240417203409447](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240417203409447.png)

****

### 缓存雪崩

**缓存雪崩**是指在同一时段**大量的缓存key同时失效**或者**Redis服务宕机**，导致大量请求到达数据库，带来巨大压力。

**解决方案**：

* 给**不同的Key的相同过期时间添加随机值（1-5min）**

* **利用Redis集群**提高服务的可用性   **哨兵模式和集群模式**

* 给缓存业务添加**降级(使用备用数据，提供部分功能)限流(限制请求数量)**策略   **nginx**

* 给业务添加多级缓存 **Guava或Caffeine 再加一层缓存**

****

### 双写一致性

> redis做为缓存，mysql的数据如何与redis进行同步呢？（双写一致性）

分业务场景回答

* 一致性要求高 如涉及钱的
* 允许延迟一致

**双写一致性：**当修改了数据库的数据也要同时更新缓存的数据，缓存和数据库的数据要保持一致

读操作：缓存命中，直接返回；缓存未命中查询数据库，写入缓存，设定超时时间

写操作：延迟双删   删除缓存 修改数据库 删除缓存

**1.先删除缓存，还是先修改数据库** 都会有问题

![image-20240423201222672](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240423201222672.png)

![image-20240423201411798](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240423201411798.png)

**2.为什么要删除两次缓存？** 多线程导致的问题

**3.为什么要延时删除？** 数据库是主从分离的，所以需要延时一会，让主数据库同步给从数据库

**解决方案：**

**允许延迟一致的业务，采用异步通知**

①使用MQ中间中间件，更新数据之后，通知缓存删除

②利用canal中间件，不需要修改业务代码，伪装为mysql的一个从节点，canal通过读取binlog数据更新缓存

**强一致性的，采用Redisson提供的读写锁**

①共享锁：读锁readLock，加锁之后，其他线程可以共享读操作   

②排他锁：独占锁writeLock也叫，加锁之后，阻塞其他线程读写操作

****

### 持久化

Redis有两种持久化的方式

* RDB 
  * 全称Redis Database Backup file（Redis数据备份文件），也被叫做Redis数据快照。简单来说就是把内存中的所有数据都记录到磁盘中。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据
* AOF
  * AOF全称为Append Only File（追加文件）。Redis处理的**每一个写命令都会记录**在AOF文件，可以看做是命令日志文件。

因为是记录命令，AOF文件会比RDB文件大的多。而且AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义。通过执行`bgrewriteaof`命令，可以让AOF文件执行重写功能，用最少的命令达到相同效果。

![image-20240423203204034](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240423203204034.png)

****

### 数据过期策略

> 假如redis的key过期之后，会立即删除吗？

Redis对数据设置数据的有效时间，数据过期以后，就需要将数据从内存中删除掉。可以按照不同的规则进行删除。

这种删除规则就被称之为数据的删除策略（数据过期策略）

* **惰性删除**：设置该key过期时间后，我们不去管它，当**需要该key时，我们在检查其是否过期，如果过期，我们就删掉它**，反之返回该key
  * 对**CPU友好**，只会在使用该key时才会进行过期检查，对于很多用不到的key不用浪费时间进行过期检查
  * 对**内存不友好**，如果一个key已经过期，但是一直没有使用，那么该key就会一直存在内存中，内存永远不会释放
* **定期删除**：每隔一段时间，我们就对一些key进行检查，删除里面过期的key(从一定数量的数据库中取出一定数量的随机key进行检查，并删除其中的过期key)。
  * 优点：可以通过限制删除操作执行的时长和频率来减少删除操作对CPU的影响。另外定期删除，也能有效释放过期键占用的内存。
  * 缺点：难以确定删除操作执行的时长和频率。

定期清理有两种模式：

* SLOW模式是定时任务，执行频率默认为10hz，每次不超过25ms(为了不影响主进程数据访问)，以通过修改配置文件redis.conf的hz选项来调整这个次数

* FAST模式执行频率不固定，但两次间隔不低于2ms，每次耗时不超过1ms

***Redis的过期删除策略：情性删除+定期删除两种策略进行配合使用***

****

### 数据淘汰策略

> 假如缓存过多，内存是有限的，内存被占满了怎么办？

**数据的淘汰策略：**当Redis中的内存不够用时，此时在向Redis中添加新的key，那么Redis就会按照某一种规则将内存中的数据删除掉，这种数据的删除规则被称之为内存的淘汰策略。

Redis支持**8种不同策略**来选择要删除的key:

* noeviction：不淘汰任何key，但是内存满时不允许写入新数据**，默认就是这种策略**。

* volatile-ttl：对设置了TTL的key，比较key的剩余TTL值，TTL越小越先被淘汰
* **allkeys-random：对全体key，随机进行淘汰。**
* volatile-random：对设置了TTL的key，随机进行淘汰。
* **allkeys-lru:对全体key，基于LRU算法进行淘汰** 
* **volatile-Iru：对设置了TTL的key，基于LRU算法进行淘汰**
  * 如果业务中有置顶的需求，可以使用volatile-Iru策略，同时置顶数据不设置过期时间，这些数据就一直不被删除会淘汰其他设置过期时间的数据。
* allkeys-lfu:对全体key，基于LFU算法进行淘汰
* volatile-lfu:对设置了TTL的key，基于LFU算法进行淘汰

![image-20240423204056444](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240423204056444.png)



****

## 分布式锁

> 需要结合项目中的业务进行回答
>
> 通常情况下，分布式锁使用的场景：集群情况下的定时任务、抢单、幂等性场景

不同端口的服务，有各自的本地锁，但这些服务都启动后，本地锁无法锁其它服务，从而导致问题，这里就需要分布式锁。

比如说买票，当多线程时会出现并发问题导致超卖，这时我们知道可以使用锁机制来解决这一问题，单体项目自然没有问题，但当我们部署同一份代码到多台服务器时，也就是所谓的集群，这时就会出现本地锁无法锁住的状况，此时需要如redis提供的分布式锁来保证线程安全性。

**Redis实现分布式锁主要利用Redis的setnx命令**。setnx是SET if not exists(如果不存在，则 SET)的简写。

![image-20240423210207435](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240423210207435.png)

### **redisson实现的分布式锁**

> 更好的分布式锁实现

![image-20240423210810037](C:\Users\18356\AppData\Roaming\Typora\typora-user-images\image-20240423210810037.png)



**注意，加锁、设置过期时间等操作都是基于lua脚本完成**

> lua脚本：保证多条redis指令执行的原子性
>
> Lua脚本能够实现Redis指令的原子性主要是因为Redis的`EVAL`命令允许在服务器端执行Lua脚本。当客户端发送Lua脚本到Redis服务器执行时，**Redis会将脚本作为一个整体进行执行，确保了脚本内部的操作是原子性的。**
>
> **具体来说，当客户端发送一个Lua脚本到Redis服务器时，服务器会将脚本加载到内存中，并在执行脚本期间暂停其他客户端的操作。**这意味着在执行Lua脚本期间，Redis服务器会单独处理Lua脚本中的各个指令，而不会被其他客户端的操作所中断。这种机制确保了Lua脚本内部的多个指令在执行过程中是原子性的，即它们要么全部执行成功，要么全部执行失败，不会出现部分执行成功或者执行失败的情况。

#### 可重入性

无论是java中的可重入锁还是redis中的可重入锁都是基于线程id来判断是否是同一线程重复获取同一把锁

![image-20240423211400323](C:\Users\18356\AppData\Roaming\Typora\typora-user-images\image-20240423211400323.png)

#### 主从一致性

主节点负责写数据，从节点负责读数据，**当主节点写入了数据，就需要将数据同步给从节点**

![image-20240423211846611](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240423211846611.png)

## 其它问题

### 主从复制、主从同步

> Redis集群有哪些方案，知道嘛

![image-20240424092257177](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424092257177.png)

**主从复制：**单节点Redis的并发能力是有上限的，要进一步**提高Redis的并发能力**，就需要搭建主从集群，**实现读写分离。**（主节点写，从节点读），**这就需要主从同步**

![image-20240424093006353](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424093006353.png)

![image-20240424093149289](C:\Users\18356\AppData\Roaming\Typora\typora-user-images\image-20240424093149289.png)

### 哨兵模式、集群脑裂

> 主从复制保证不了redis的高可用
>
> 比如主节点宕机，就无法写数据了

Redis提供了哨兵（Sentinel）机制来实现主从集群的自动故障恢复。哨兵的结构和作用如下:

* 监控：Sentinel会不断检查您的master和slave是否按预期工作
* 自动故障恢复：如果master故障，Sentinel会将一个slave提升为master。当故障实例恢复后也以新的master为主
* 通知：Sentinel充当Redis客户端的服务发现来源，当集群发生故障转移时，会将最新信息推送给Redis的客户端

Sentinel**基于心跳机制监测服务状态**，每隔1秒向集群的每个实例发送ping命令：

* 主观下线：如果某sentinel节点发现某实例**未在规定时间响应**，则认为该实例**主观下线**。
* 客观下线：**若超过指定数量（quorum）的sentinel都认为该实例主观下线，则该实例客观下线**。quorum值最好超过Sentinel实例数量的一半

哨兵选主规则

* 首先判断主与从节点**断开时间长短**，如超过指定值就排该从节点
* 然后判断从节点的slave-priority值，越小优先级越高
* **如果slave-prority一样，则判断slave节点的offset值，越大优先级越高**
* 最后是判断slave节点的运行id大小，越小优先级越高



**哨兵模式导致集群脑裂**

想象一下这样的一个问题，当出现网络问题，哨兵无法监控主节点，只监控到从节点，此时就会在从节点中选举新的主节点，这样就产生两个主节点，从而导致脑裂，此时客户端依旧向原有主节点写入数据，就会导致数据无法同步。

**解决方案**：redis中有两个配置参数：

* min-replicas-to-write1表示最少的salve节点为1个   **当主节点至少有一个从节点才可以写入**
* min-replicas-max-lag5表示数据复制和同步的延迟不能超过5秒 若超过时间 **拒绝客户端的请求**

****

### 分片集群、数据读写规则

> 主从和哨兵可以解决高可用、高并发读的问题。但是依然有两个问题没有解决：
>
> 海量数据存储问题
>
> 高并发写的问题

使用分片集群可以解决上述问题，分片集群特征：

* 集群中有多个master，每个master保存不同数据
* 每个master都可以有多个slave节点
* master之间通过ping监测彼此健康状态客户端请求
* 可以访问集群任意节点，最终都会被转发到正确节点  

![image-20240424095057587](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424095057587.png)

**数据读写规则**

2的14次方=16384

![image-20240424095451297](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424095451297.png)

****

### redis为什么这么快？

>Redis是纯内存操作，执行速度非常快 最主要的原因！！
>
>采用单线程，避免不必要的上下文切换可竞争条件，多线程还要考虑线程安全问题
>
>使用I/O多路复用模型，非阻塞IO

**解释一下I/O多路复用模型**

Redis是纯内存操作，执行速度非常快，**它的性能瓶颈是网络延迟而不是执行速度，**I/O多路复用模型主要就是实现了高效的网络请求

![image-20240424101434526](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424101434526.png)

![image-20240424101512839](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424101512839.png)

![image-20240424101557270](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424101557270.png)

![image-20240424101845206](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424101845206.png)

> I/O多路复用是指利用单个线程来同时监听多个Socket，并在某个Socket可读、可写时得到通知，从而避免无效的等待，充分利用CPU资源。
>
> 目前的I/O多路复用都是采用的epoll模式实现，它会在通知用户进程Socket就绪的同时，把已就绪的Socket写入用户空间，不需要挨个遍历Socket来判断是否就绪，提升了性能。
>
> 其中Redis的网络模型就是使用l/O多路复用结合事件的处理器来应对多个Socket请求，比如，提供了连接应答处理器、命令回复处理器，命令请求处理器；
>
> 在Redis6.0之后，为了提升更好的性能，在命令回复处理器使用了多线程来处理回复事件，在命令请求处理器中，将命令的转换使用了多线程，增加命令转换速度，在命令执行的时候，依然是单线程
