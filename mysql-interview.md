# Mysql 面试题

![image-20240424102149535](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424102149535.png)

## 优化

### 如何定位慢查询

* 聚合查询 有聚合函数的查询比如 Sum、Avg、Count等
* 多表查询
* 表数据量过大查询
* 深度分页查询

**现象：页面加载过慢、接口压测响应时间过长(超过1s)**

#### 解决方案一：

* 调试工具：Arthas
* 运维工具：Prometheus、Skywalking

#### 解决方案二：Mysql自带慢日志

> 调试阶段开启，避免损耗性能

慢查询日志记录了**所有执行时间超过指定参数**（long_query_time，单位：秒，默认10秒）的所有SQL语句的日志如果要开启慢查询日志，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：

```shell
# 开启MySQL慢日志查询开关
slow_query_log=1
# 设置慢日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
long_query_time=2
```

****

### 慢查询如何分析

![image-20240424103717316](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424103717316.png)

* possible_key 当前sql可能会使用到的索

* **key 当前sql实际命中的索引**

* **key_len 索引占用的大小**

* Extra 额外的优化建议
  * Using where; Using Index
    * 查找使用了索引，需要的数据都在索引列中能找到，不需要回表查询数据
  * Using index condition
    * 查找使用了索引，但是需要回表查询数据
* type 这条sql的连接的类型，性能由好到差为NULL、system、const、eq_ref、ref、range、index、all
  * system:查询系统中的表
  * const:根据主键查询
  * eq_ref:主键索引查询或唯一索引查询
  * ref：索引查询
  * range:范围查询
  * **index:索引树扫描**
  * **all:全盘扫描**

>如果一条sql执行很慢的话，我们通常会使用mysql自动的执行计划explain来去查看这条sql的执行情况。
>
>比如在这里面可以通过**key和key_len检查是否命中了索引**，如果本身已经添加了索引，也可以判断索引是否有失效的情况。
>
>第二个，可以通过type字段查看sql是否有进一步的优化空间，是否存在全索引扫描或全盘扫描，第三个可以通过extra建议来判断，是否出现了回表的情况，如果出现了，可以尝试添加索引或修改返回字段来修复

****

### 索引概念、索引底层数据结构

> 了解过索引吗？(什么是索引）

索引（index）是帮助MYSQL高效获取数据的的数据结构（有序）。提高数据检索效率，降低数据库IO成本（无需全表扫描）

在数据之外，数据库系统还维护看满足特定查找算法的数据结构**（B+树）**，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

全文索引用的不是B+树

#### 二叉树、红黑树

> 二叉树（Binary Tree）是一种树形数据结构，其中每个节点最多有两个子节点，通常称为左子节点和右子节点。二叉树的每个节点都包含一个数据元素（称为键值）以及指向其左子树和右子树的指针。
>
> 二叉树可以为空（即不包含任何节点），或者由一个根节点和零个或多个子树组成，每个子树也是二叉树。

二叉搜索树（Binary Search Tree，BST）是一种特殊的二叉树，具有以下性质：

1. 左子树上所有节点的键值小于根节点的键值。
2. 右子树上所有节点的键值大于根节点的键值。
3. 左子树和右子树都是二叉搜索树。

二叉搜索树的时间复杂度取决于树的高度，平均情况下，查找、插入和删除操作的时间复杂度为O(log n)，其中n是树中节点的数量。然而，在最坏情况下，当二叉搜索树退化成链表时，时间复杂度会退化为O(n)。

**为了保持二叉搜索树的平衡，通常会使用自平衡二叉搜索树，例如AVL树和红黑树。**

红黑树（Red-Black Tree）是一种自平衡二叉搜索树，它是在**普通二叉搜索树的基础上引入了额外的约束和操作**，以保持树的平衡性。

**红黑树的特点在于它的每个节点都带有一个额外的表示节点颜色的属性，该颜色可以是红色或黑色。**

红黑树具有以下性质：

1. 每个节点要么是红色，要么是黑色。
2. 根节点是黑色的。
3. 每个叶子节点（NIL节点，即空节点）是黑色的。
4. 如果一个节点是红色的，则其子节点必须是黑色的（反之不一定成立）。
5. **从任意节点到其每个叶子节点的所有路径都包含相同数量的黑色节点。**

![image-20240424105159217](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424105159217.png)

#### B树和B+树

> 当数据量过大，无疑前面的二叉树的层级会很高，这样也会导致搜索效率的降低

B树和B+树是一种常用的**多路搜索树**，被广泛应用于数据库和文件系统中，用于组织大量数据并支持高效的检索、插入和删除操作。它们具有以下特点：

1. **B树（B-Tree）**：
   - B树是一种自平衡的多路搜索树，它的每个节点可以包含多个键值对（通常称为阶），节点中的键值对按照键的大小顺序排列。
   - 所有叶子节点位于相同的深度，内部节点可以拥有多个子节点。通常情况下，B树的节点会尽可能填充，从而减少树的高度，提高检索效率。
   - B树的节点通常会**维护一个指向子节点的指针数组，以及一个对应的键值数组**，用于进行快速的查找操作。
   - B树的插入、删除和查找操作都具有O(log n)的时间复杂度，其中n是树中键值对的数量。

![image-20240424140409602](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424140409602.png)

1. **B+树（B+ Tree）**：
   - B+树是在B树的基础上进一步优化而成的树结构，它与B树的主要区别在于，**B+树的所有关键字都出现在叶子节点上。**
   - 叶子节点之间通过指针进行连接，形成一个**有序双向链表**，**从而使得范围查询更加高效。**
   - **内部节点不存储数据，只存储键值对的键**，并且内部节点的子节点指针与叶子节点指针不同。这样的设计使得B+树在**磁盘存储中更加高效**，因为内部节点的大小通常要小于叶子节点，可以减少I/O操作的次数。
   - B+树的插入、删除和查找操作同样具有O(log n)的时间复杂度。

![image-20240424140445398](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424140445398.png)

****

### 聚集(聚簇)索引和非聚簇(二级)索引、回表查询

**聚集索引：**将数据存储与索引放到了一块，索引结构的叶子节点保存了行数据。**必须有且只有一个** 

**二级索引：**将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键。 **可以存在多个**

**聚集索引选取规则：**

* 如果存在主键，主键索引就是聚集索引。

* 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引。

* 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引。

![image-20240424141342666](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424141342666.png)

#### 什么是回表查询呢？

通过二级索引找到对应主键值，此时拿不到行数据，又需要通过主键值的聚集索引查询行数据

### 覆盖索引、超大分页优化

覆盖索引是指**查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到**，

即不需要回表查询的索引

**超大分页的优化可以用覆盖索引解决**

当在进行分页查询时，如果执行limit9000000,10，此时需要MySQL排序前9000010记录，仅仅返回9000000－9000010的记录，其他记录丢弃，查询排序的代价非常大。

![image-20240424142617144](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424142617144.png)

****

### 索引创建的原则

1).针对于**数据量较大，且查询比较频繁的表建立索引**。如单表超过10万数据 (增加用户体验)

2).针对于**常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引l**。

3).尽量**选择区分度高的列作为索引**，尽量建立唯一索引，区分度越高，使用索引的效率越高。

4).如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。

5).**尽量使用联合索引**，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。

6).**要控制索引的数量，索引并不是多多益善**，索引越多，维护索引结构的代价也就越大，**会影响增删改的效率**。

****

### 索引失效

- 违反最左前缀法则   复合索引，必须按序，不按序则失效

  ![image-20240424163934073](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424163934073.png)

- 范围查询右边的列，不能使用索引 

  ![image-20240424164021087](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424164021087.png)

- 不要在索引列上进行运算操作，索引将失效

  ![image-20240424164117435](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424164117435.png)

- 字符串不加单引号，造成索引失效。(类型转换）

  ![image-20240424164232599](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424164232599.png)

- 以%开头的Like模糊查询，索引失效

![image-20240424164310070](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424164310070.png)

****

### sql的优化经验

![image-20240424164425239](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240424164425239.png)

#### 表的设计优化

> 参考阿里开发手册《嵩山版》

比如设置合适的数值（tinyintintbigint），要根据实际情况选择

比如设置合适的字符串类型（char和varchar）char定长效率高，varchar可变长度，效率稍低

#### sql语句优化

SELECT语句务必指明字段名称（避免直接使用select＊）

SQL语句要避免造成索引失效的写法

尽量用union all代替union , **union会多一次过滤重复，效率低**

避免在where子句中对字段进行表达式操作

Join优化 能用innerjoin 就不用left join right join，如必须使用 一定要以小表为驱动，**内连接会对两个表进行优化，优先把小表放到外边，把大表放到里边**。left join 或 right join，不会重新调整顺序

#### 主从复制，读写分离

如果数据库的使用场景读的操作比较多的时候，为了避免写的操作所造成的性能影响可以采用读写分离的架构

**读写分离解决的是，数据库的写入，影响了查询的效率。**

****

## 事务

事务是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

**ACID原则**

### 并发事务带来哪些问题？MySQL的默认隔离级别是？

并发事务问题：脏读、不可重复读、幻读

隔离级别：读未提交、读已提交、**可重复读**、串行化

> MySQL通过使用多版本并发控制（MVCC，Multi-Version Concurrency Control）和事务隔离级别来解决脏读和不可重复读的问题。
>
> 1. **解决脏读问题**：脏读是指在一个事务内读取了另一个未提交事务的数据。MySQL通过设置事务的隔离级别来解决这个问题。在READ COMMITTED（读已提交）或更高的隔离级别下，一个事务只能读取到其他事务已经提交的数据，这样就避免了脏读的问题。
> 2. **解决不可重复读问题**：不可重复读是指在一个事务内，多次读取同一数据返回的结果有所不同，这是因为其他事务在这两次读取的过程中修改了数据。MySQL通过使用MVCC来解决这个问题。在REPEATABLE READ（可重复读）隔离级别下，MySQL会为每个读操作创建一个快照（snapshot），即在事务开始时的一致性视图（consistent read view）。在同一事务中，后续的读操作会读取这个快照中的数据，而不是最新的数据，这样就保证了在同一事务中多次读取同一数据返回的结果是一致的。
>
> 需要注意的是，虽然MVCC和事务隔离级别可以解决脏读和不可重复读的问题，但它们不能解决幻读（phantom read）的问题。幻读是指在一个事务内，执行相同的查询两次，但返回的结果集不同，这是因为其他事务在这两次查询之间插入或删除了数据。在MySQL中，只有在SERIALIZABLE（串行化）隔离级别下，才能避免幻读的问题。

### 间隙锁

间隙锁（Gap Lock）是MySQL中InnoDB存储引擎提供的一种锁定机制，主要用于防止幻读（Phantom Read）。

幻读是指在一个事务内读取某个范围内的记录时，另一个事务在该范围内插入了新的记录，导致第一个事务在再次读取该范围的记录时，会发现有些之前没有的记录，这就是幻读。

为了解决这个问题，InnoDB引入了间隙锁。间隙锁不是锁定记录，而是锁定记录之间的“间隙”。这样，即使没有记录的地方也会被锁定，其他事务就不能在这个范围内插入新的记录，从而防止了幻读。

例如，假设我们有一个按照ID排序的表，现在有ID为1和3的两条记录，那么间隙锁就会锁定ID为1和3之间的间隙，防止其他事务在这个间隙内插入新的记录。

需要注意的是，间隙锁只在使用READ COMMITTED和REPEATABLE READ这两种隔离级别，并且在明确指定了锁类型（如FOR UPDATE或LOCK IN SHARE MODE）的情况下才会生效。

****

### undo log和 redo log的区别

**缓冲池（bufferpool）**：主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据（若缓冲池没有数据，则从磁盘加载并缓存），以一定频率刷新到磁盘，从而减少磁盘IO，加快处理速度

**数据页（page）**：是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为16KB。页中存储的是行数据

当mysql宕机，缓冲池中数据还未同步到数据页中，此时就出现脏页问题

#### redo log 持久性

重做日志，记录的是事务提交时数据页的物理修改，是用来实现事务的持久性。

该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log file）,前者是在内存中，后者在磁盘中。

当事务提交之后会把所有修改信息都存到缓冲中再持久化到日志文件中，用于在刷新脏页到磁盘，发生错误时，进行数据恢复使用。

**好像和之前没什么区别？**不是，因为之前写入缓冲池的数据是随机写入数据页即磁盘中，随机读写性能差，而redlog因为是文件追加形式，所以是顺序读写，性能更好！

#### undo log 原子性和一致性

回滚日志，用于记录数据被修改前的信息，作用包含两个：**提供回滚和 MVCC(多版本并发控制）**。

**undolog 和 redolog记录物理日志不一样，它是逻辑日志。·**

可以认为当delete一条记录时，undolog中会记录一条对应的insert记录，反之亦然,当update一条记录时，它记录一条对应相反的update记录。

**当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。**

## 其它

### MVCC

> 事务中的隔离性是如何保证的呢？
>
> 锁：排他锁（如一个事务获取了一个数据行的排他锁，其他事务就不能再获取该行的其他锁）
>
> mvcc：多版本并发控制

全称Multi-VersionConcurrencyControl，多版本并发控制。指维护**一个数据的多个版本，使得读写操作没有冲突**

MVCC的具体实现，主要依赖于数据库记录中的**隐藏字段、undolog日志、readView**。

![image-20240425124624478](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425124624478.png)

**隐藏字段：**

* trx_id(事务id)，记录每一次操作的事务id，是自增的
* roll_pointer(回滚指针)，指向上一个版本的事务版本记录地址
* row_id(隐藏主键)，如果表结构没有指定主键，将会生成该隐藏字段

**undo log:**

* 回滚日志，存储老版本数据

* 版本链：多个事务并行操作某一行记录，记录不同事务修改数据的版本，通过roll_pointer指针形成一个链表

**readView 解决的是一个事务查询选择版本的问题**

* 根据readView的匹配规则和当前的一些事务id判断该访问那个版本的数据，不同的隔离级别快照读是不一样的，最终的访问的结果不一样
* RC：每一次执行快照读时生成ReadView
* RR：仅在事务中第一次执行快照读时生成ReadView，后续复用

****

### 主从同步

MySQL主从复制的核心就是二进制日志

>二进制日志（BINLOG）记录了所有的DDL（（数据定义语言）语句和DML数据操纵语言）语句，但不包括数据查询（SELECT、SHOW）语句

复制分成三步：

* Master 主库在事务提交时，会把数据变更记录在二进制日志文件 Binlog 中。

* 从库读取主库的二进制日志文件 Binlog，写入到从库的中继日志 RelayLog

* slave 从库重做中继日志中的事件，将改变反映它自己的数据。

****

### 分库分表

分库分表的时机：

* 前提，项目业务数据逐渐增多，或业务发展比较迅速单表的数据量达1000W或20G以后

* 优化已解决不了性能问题(主从读写分离、查询索引..)

* IO瓶颈（磁盘IO、网络IO）、CPU瓶颈（聚合查询、连接数太多）

#### 垂直分库

按业务拆分

![image-20240425140013839](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425140013839.png)

#### 垂直分表

将大数据和小数据分开，可以分在不同库也可以在同一个库中拆分

![image-20240425140052360](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425140052360.png)

#### 水平分库

就是一个库中数据太大，分到不同库存储

![image-20240425140341990](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425140341990.png)

#### 水平分表

就是一张表数据过大，水平拆分开

![image-20240425140530866](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425140530866.png)

#### 产生新问题

![image-20240425140839412](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240425140839412.png)

### 主键自增有什么问题

使用数据库的自增主键，虽然在很多情况下都很方便，但也可能遇到一些问题：

- **插入性能问题：**对于非常高并发的插入操作，MySQL 中如果有自增字段的话，MySQL 会维护一个自增锁，InnoDB 表引擎会在内存里保存一个计数器来记录 AUTO_INCREMENT 值。当插入一个新行数据时，就会用一个表锁来锁住这个计数器，直到插入结束。如果是一行一行的插入是没有问题的，但是在高并发情况下，表锁会引起 SQL 阻塞，极大的影响性能
- **主键耗尽：**如果表的记录非常多，可能会出现自增主键耗尽的情况。尤其是对于定义为整型的自增主键，如果插入的记录数超过了整型的最大值，就无法再插入新的记录。
- **分布式系统问题：**在分布式系统中，如果多个数据库节点都需要生成自增主键，就需要保证生成的主键在全局是唯一的。这通常需要引入额外的机制或工具，比如分布式ID生成器。

### mysql加锁

MySQL中的锁主要有两种类型：共享锁（Shared Locks）和排他锁（Exclusive Locks）。共享锁用于读操作，允许多个事务同时读取同一资源，但不允许其他事务对其进行写操作。排他锁用于写操作，只允许一个事务对资源进行写操作，同时阻止其他事务对其进行读和写操作。

在MySQL中，可以通过以下方式来加锁：

**1. 显式锁定**

可以使用`LOCK TABLES`和`UNLOCK TABLES`语句来显式地对表加锁。例如：

```sql
LOCK TABLES table_name READ;  -- 加共享锁
LOCK TABLES table_name WRITE; -- 加排他锁
UNLOCK TABLES; -- 解锁
```

**2. 事务中的隐式锁定**

在InnoDB存储引擎中，当你在一个事务中对数据进行操作时，会自动对涉及的数据加锁。例如：

```sql
START TRANSACTION;
SELECT * FROM table_name WHERE id = 1 FOR UPDATE; -- 加排他锁
COMMIT; -- 提交事务，自动解锁
```

在上述例子中，`SELECT ... FOR UPDATE`语句会对选中的数据加排他锁。

### mysql索引下推

[一文读懂什么是MySQL索引下推（ICP） - 简书 (jianshu.com)](https://www.jianshu.com/p/31ceadace535)

索引下推的目的是为了减少回表次数，也就是要减少IO操作。

对于`InnoDB`的**聚簇索引**来说，完整的行记录已经加载到缓存区了，索引下推也就没什么意义了。
