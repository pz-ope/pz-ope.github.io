# Nosql概述

## 为什么使用Nosql

大数据时代：大数据一般的数据库无法进行分析处理；

### 单机MySQL的时代

在90年代，一个网站的访问量一般都不大，用单个数据库完全可以轻松应付。 

在那个时候，更多的都是静态网页Html ~服务器根本没有太大的压力!，动态交互类型的网站不多。

![image-20220721102641793](../../../../../Download/Typora/image/image-20220721102641793.png)

上述架构下，我们来看看数据存储的瓶颈是什么？

DAL : Data Access Layer（数据访问层 – Hibernate，MyBatis）

* 数据量如果太大，一个机器放不下时。

* 数据的索引（B+ Tree）一个机器的内存放不下时。

* 访问量(读写混合)一个实例或服务器不能承受。

如果满足了上述1 or 3个时，只能对数据库的整体架构进行重构。

### Memcached(缓存)+MySQL+垂直拆分（读写分离）

> 后来，随着访问量的上升，几乎大部分使用MySQL架构的网站在数据库上都开始出现了性能问题，web程序不再仅仅专注在功能上，同时也在追求性能。程序员们开始大量的使用缓存技术来缓解数据库的压力，优化数据库的结构和索引。开始比较流行的是通过文件缓存来缓解数据库压力，但是当访问量继续增大的时候，多台web机器通过文件缓存不能共享，大量的小文件缓存也带了了比较高的IO压力。在这个时候，Memcached就自然的成为一个非常时尚的技术产品。

<img src="../../../../../Download/Typora/image/image-20220721103004994.png" alt="image-20220721103004994" style="zoom:80%;" />

网站80%的情况都是在读，每次都去查询数据库的话就十分麻烦，(第一次访问数据库中的数据后存入缓存中，第二次如果这个数据库中的数据没有变化就直接从缓存中获取)，为了减轻数据的压力，可以使用缓存来保证效率。

发展过程：优化数据结构和索引—>文件缓存(IO)—> Memcached(缓存)

<img src="../../../../../Download/Typora/image/image-20220721103043069.png" alt="image-20220721103043069" style="zoom:80%;" />

### 分库分表+水平拆分+mysql集群

> 在Memcached的高速缓存，MySQL的主从复制，读写分离的基础之上，这时MySQL主库的写压力开始出现瓶颈，而数据量的持续猛增，由于MyISAM在写数据的时候会使用表锁，在高并发写数据的情况下会出现严重的锁问题，大量的高并发MySQL应用开始使用InnoDB引擎代替MyISAM。
>
> ps：这就是为什么 MySQL 在 5.6 版本之后使用 InnoDB 做为默认存储引擎的原因 – MyISAM 写会锁表，InnoDB 有行锁，发生冲突的几率低，并发性能高。

<img src="../../../../../Download/Typora/image/image-20220721111751261.png" alt="image-20220721111751261" style="zoom:80%;" />

> 扩展：行锁和表锁的含义及区别是什么
>
> 对于行锁和表锁的含义区别，在面试中应该是高频出现的，我们应该对MySQL中的锁有一个系统的认识，更详细的需要自行查阅资料，本篇为概括性的总结回答。MySQL常用<span style="color:#a2a">引擎</span>有MyISAM和InnoDB，而InnoDB是mysql默认的引擎。MyISAM不支持行锁，而InnoDB支持行锁和表锁。
>
> MyISAM在执行查询语句（SELECT）前，会自动给涉及的所有表加读锁，在执行更新操作（UPDATE、DELETE、INSERT等）前，会自动给涉及的表加写锁，这个过程并不需要用户干预，因此用户一般不需要直接用LOCK TABLE命令给MyISAM表显式加锁。
>
> **表锁不会出现死锁，发生锁冲突几率高，并发低。**
>
> MyISAM引擎
>
> MyISAM在执行查询语句（select）前，会自动给涉及的所有表加读锁，在执行增删改操作前，会自动给涉及的表加写锁。
>
> MySQL的表级锁有两种模式：
>
> 表共享读锁
>
> 表独占写锁
>
> 读锁会阻塞写，写锁会阻塞读和写
>
> 对MyISAM表的读操作，不会阻塞其它进程对同一表的读请求，但会阻塞对同一表的写请求。只有当读锁释放后，才会执行其它进程的写操作。
>
> 对MyISAM表的写操作，会阻塞其它进程对同一表的读和写操作，只有当写锁释放后，才会执行其它进程的读写操作。
>
> MyISAM不适合做写为主表的引擎，因为写锁后，其它线程不能做任何操作，大量的更新会使查询很难得到锁，从而造成永远阻塞
>
> **行锁**
>
> 会出现死锁，发生锁冲突几率低，并发高。
>
> 在MySQL的InnoDB引擎支持行锁，与Oracle不同，MySQL的行锁是通过索引加载的，也就是说，行锁是加在索引响应的行上的，要是对应的SQL语句没有走索引，则会全表扫描，行锁则无法实现，取而代之的是表锁，此时其它事务无法对当前表进行更新或插入操作。
>
> ```mysql
> CREATE TABLE 'USER' {
>  'name' VARCHAR(32) DEFAULT NULL,
>  'count' INT(11) DEFAULT NULL,
>  'id' INT(11) NOT NULL AUTO_INCREMENT
>  PRIMARY KEY ('id')
> } ENGINE=INNODB AUTO_INCREMENT=7 DEFAULT CHARSET=UTF8
> ```
>
> * A通过主键执行插入操作，但事务未提交
>
>   ```sql
>   update user set count=10 where id=1;
>   ```
>
>   B在此时也执行更新操作
>
>   ```sql
>   update user set count=10 where id=2;
>   ```
>
>   由于是通过主键选中的，为行级锁，A和B操作的不是同一行，B执行的操作是可以执行的
>
> * A通过name执行插入操作，但事务未提交
>
>   ```sql
>   update user set count=10 where name='xxx';
>   ```
>
>   B在此时也执行更新操作
>
>   ```sql
>   update user set count=10 where id=2;
>   ```
>
>   由于是通过非主键或索引选中的，升级为为表级锁，
>   B则无法对该表进行更新或插入操作，只有当A提交事务后，B财会成功执行

<span style="color:red">数据库本质：读，写</span>

* 早些年MyISAM：表锁（假如有1000万条数据，查张三的密码，会把真个用户表锁住，其他进程只能等待其解锁），十分影响效率，在高并发中会出现严重的锁问题。
* 转InnoDB: 行锁（查询数据只锁这一行）
* 慢慢的就开始使用分库分表来解决写的压力。MySQL在早些年代推出了表分区。逐渐的出现的集群尽管性能也不能很好满足互联网的要求，只是在高可靠性上提供了非常大的保证解决的那个时代的大量需求。

### Mysql的扩展瓶颈

> MySQL数据库也经常存储一些大文本字段或者图片（非常麻烦），导致数据库表非常的大，在做数据库恢复的时候就导致非常的慢，不容易快速恢复数据库。比如1000万4KB大小的文本就接近40GB的大小，如果能把这些数据从MySQL省去，MySQL将变得非常的小。关系数据库很强大，但是它并不能很好的应付所有的应用场景。MySQL的扩展性差（需要复杂的技术来实现），大数据下IO压力大，表结构更改困难，正是当前使用MySQL的开发人员面临的问题。

![image-20210713134654824](../../../../../Download/Typora/image/image-20210713134654824.png)

最前面的是企业级防火墙，后面通过负载均衡主机（软负载：Nginx，硬负载：F5）在 web 服务器集群之间进行调度，再由具体的 web 服务器（Tomcat）去访问缓存，访问数据库。

### 总结

今天我们可以通过第三方平台（如：Google,Facebook等）可以很容易的访问和抓取数据。

用户的个人信息，社交网络，地理位置，用户生成的数据和用户操作日志已经成倍的增加。我们如果要对这些用户数据进行挖掘，那SQL数据库已经不适合这些应用了, NoSQL数据库的发展也却能很好的处理这些大的数据。

## 什么是NOSQL

NoSQL(NoSQL = Not Only SQL )，意即“不仅仅是SQL”；

关系型数据库：由表格，行，列基本结构

随着互联网web2.0互联网的兴起，传统的关系数据库在应付web2.0时代，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题，包括超大规模数据的存储。

很多的数据类型用户的个人信息，社交网络，地理位置。这些数据类型的存储不需要一个固定的格式！不需要过多的操作就可以横向扩展！`Map<String,Object>`使用键值对来控制。

（例如谷歌或Facebook每天为他们的用户收集万亿比特的数据）。这些类型的数据存储不需要固定的模式，无需多余操作就可以横向扩展。

NoSQL代表:

* MongDB
*  Redis
* Memcache

### **NoSQL特点**

* 方便扩展（数据之间没有关系，扩展性好）
* 大数据量高性能（Redis一秒写8万次，读取11万，Nosql的缓存记录级是一种细粒度的缓存性能高）
* 数据类型多样（不需要事先设计数据库！随取随用）
* 键 - 值对存储，列存储，文档存储，图形数据库
* 最终一致性，而非ACID属性
* 非结构化和不可预知的数据
* **CAP定理和BASE**
* 高性能，高可用性和可伸缩性
* 分布式数据库中的CAP原理(了解)
* 代表着不仅仅是SQL，没有声明性查询语言，没有预定义的模式

### **ACID**和**CAP定理**

> **ACID**
>
> 关系型数据库遵循ACID规则
>
> 事务在英文中是transaction，和现实世界中的交易很类似，它有如下四个特性：

* **A (Atomicity) 原子性**

  原子性很容易理解，也就是说事务里的所有操作要么全部做完，要么都不做，事务成功的条件是事务里的所有操作都成功，只要有一个操作失败，整个事务就失败，需要回滚。比如银行转账，从A账户转100元至B账户，分为两个步骤：1）从A账户取100元；2）存入100元至B账户。这两步要么一起完成，要么一起不完成，如果只完成第一步，第二步失败，钱会莫名其妙少了100元。

* **C (Consistency) 一致性**

  一致性也比较容易理解，也就是说数据库要一直处于一致的状态，事务的运行不会改变数据库原本的一致性约束。

* **I (Isolation) 独立性**

  所谓的独立性是指并发的事务之间不会互相影响，如果一个事务要访问的数据正在被另外一个事务修改，只要另外一个事务未提交，它所访问的数据就不受未提交事务的影响。比如现有有个交易是从A账户转100元至B账户，在这个交易还未完成的情况下，如果此时B查询自己的账户，是看不到新增加的100元。

* **D (Durability) 持久性**

  持久性是指一旦事务提交后，它所做的修改将会永久的保存在数据库上，即使出现宕机也不会丢失。

> **CAP定理：**
>
> * Consistency(一致性), 数据一致更新，所有数据变动都是同步的
>
> * Availability(可用性), 好的响应性能
>
> * Partition tolerance(分区容错性) 可靠性

P: 系统中任意信息的丢失或失败不会影响系统的继续运作。

定理：任何分布式系统只可同时满足二点，没法三者兼顾。

CAP理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，

因此，根据 CAP 原理将 NoSQL 数据库分成了满足 CA 原则、满足 CP 原则和满足 AP 原则三大类：

CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。

CP - 满足一致性，分区容忍性的系统，通常性能不是特别高。

AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一些。

CAP理论就是说在分布式存储系统中，最多只能实现上面的两点。

而由于当前的网络硬件肯定会出现延迟丢包等问题，所以分区容忍性是我们必须需要实现的。所以我们只能在一致性和可用性之间进行权衡，没有NoSQL系统能同时保证这三点。

说明：C：强一致性 A：高可用性 P：分布式容忍性

举例：

CA：传统Oracle数据库

AP：大多数网站架构的选择

CP：Redis、Mongodb

注意：分布式架构的时候必须做出取舍。

一致性和可用性之间取一个平衡。多余大多数web应用，其实并不需要强一致性。

因此牺牲C换取P，这是目前分布式数据库产品的方向。

### 传统关系型数据库（RDBMS）和NoSQL

```xml
传统的RDBMS
- 高度组织化结构化数据
- 结构化查询语言（SQL）
- 数据和关系都存储在单独的表中。
- 数据操纵语言，数据定义语言
- 严格的一致性
- 基础事务
- ......
```

```xml
Nosql
- 不仅仅是数据
- 没有固定的查询语言
- 键值对存储，列存储，文档存储，图形数据库（社交关系）
- 最终一致性
- CAP定理和BASE(异地多活)
- 高性能，高可用，高可扩
- ......
当下的应用是 RDBMS 与 NoSQL 一起使用的。
```

## 大数据时代的3v和3高

> 大数据时代的3V：主要是描述问题的
>
> 1. 海量（Volume）
>
> 2. 多样（Variety）
>
> 3. 实时（Velocity）
>
> 大数据时代的3高：主要是对程序的要求
>
> 1. 高并发(Juc)
>
> 2. 高可扩(随时水平拆分，机器不够了，可以扩张机器来用)
>
> 3. 高性能（保证用户体验和性能）

# NOSQL的四大分类

**KV键值对**：

* 新浪：**Redis**
* 美团：Redis+Tair
* 阿里、百度：Redis+memecache

**文档型数据库（Bson格式）**：

* **MongoDB**
  * MongoDB是一个基于分布式文件存储系统的数据库，C++编写，主要用来处理大量的文档
  * MongoDb是一个介于关系型数据库和非关系型数据库中间的产品，。MongoDB是非关系型数据库中功能最丰富，最像关系型数据库的。
* ConthDB

**列存储数据库**：

* **HBase**
* 分布式数据库

**图关系数据库**：

# Redis介绍

## 概述

Redis（Remote Dictionary Server )，即远程字典服务

是一个开源的使用ANSI [C语言](https://baike.baidu.com/item/C语言)编写、支持网络、可基于内存亦可持久化的日志型、Key-Value[数据库](https://baike.baidu.com/item/数据库/103728)，并提供多种语言的API。

redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

简单来说 redis 就是⼀个数据库，不过与传统数据库不同的是 redis 的数据是存在内存中的，所以读写速度⾮常快，因此 redis 被⼴泛应⽤于缓存⽅向。另外，redis 也经常⽤来做分布式锁。redis 提供了多种数据类型来⽀持不同的业务场景。除此之外，redis ⽀持事务 、持久化、LUA脚本、LRU驱动事件、多种集群⽅案。

## 作用

1、内存存储、持久化（内存中数据是断电及失的，所以持久化是很重要的-->rdb,aof）

2、效率高，可以用于高速缓存

3、发布订阅系统

4、地图信息分析

5、计时器、计数器（浏览量）

... 

> 特性

1、多样的数据类型

2、持久化

3、集群

4、事务

* **⾼性能：**

  假如⽤户第⼀次访问数据库中的某些数据。这个过程会⽐᫾慢，因为是从硬盘上读取的。将该⽤户访问的数据存在缓存中，这样下⼀次再访问这些数据的时候就可以直接从缓存中获取了。操作缓存就是直接操作内存，所以速度相当快。如果数据库中的对应数据改变的之后，同步改变缓存中相应的数据即可。

  <img src="../../../../../Download/Typora/image/image-20220721151828402.png" alt="image-20220721151828402" style="zoom: 67%;" />

* **⾼并发：**

  直接操作缓存能够承受的请求是远远⼤于直接访问数据库的，所以我们可以考虑把数据库中的部分数据转移到缓存中去，这样⽤户的⼀部分请求会直接到缓存这⾥⽽不⽤经过数据库。

  <img src="../../../../../Download/Typora/image/image-20220721151945796.png" alt="image-20220721151945796" style="zoom: 50%;" />

## 本地缓存相比

* 缓存分为本地缓存和分布式缓存。以 Java 为例，使⽤⾃带的 map 或者 guava 实现的是本地缓存，最主要的特点是轻量以及快速，⽣命周期随着 jvm 的销毁⽽结束，并且在多实例的情况下，每个实例都需要各⾃保存⼀份缓存，缓存不具有⼀致性。

* 使⽤ redis 或 memcached 之类的称为分布式缓存，在多实例的情况下，各实例共⽤⼀份缓存数据，缓存具有⼀致性。缺点是需要保持 redis 或 memcached服务的⾼可⽤，整个程序架构上᫾为复杂。

## Linux下安装Redis

1. 下载安装包`redis-6.2.4.tar.gz`

2. 上传到Linux服务器上并解压安装包；（程序-->opt目录下）

   ```bash
   #将安装包移动到opt目录下
   mv redis-6.2.4.tar.gz /opt
   #解压
   tar -zxvf redis-6.2.4.tar.gz
   ```

3. 进入解压后的配置文件

   <img src="../../../../../Download/Typora/image/image-20220721152843284.png" alt="image-20220721152843284" style="zoom:80%;" />

4. 基本的环境安装

   ```bash
   #安装gcc环境
   yum install gcc-c++
   #查看
   gcc -v
   #完成加载配置文件
   make
   make install
   ```

5. redis默认安装路径`/usr/local/bin`

   <img src="../../../../../Download/Typora/image/image-20220721152957529.png" alt="image-20220721152957529" style="zoom:80%;" />

6. 将redis配置文件，复制到当前目录下

   ```bash
   #创建目录
   mkdir kconfig
   #复制文件
   cp /opt/redis-6.2.4.tar.gz kconfig
   ```

   <img src="../../../../../Download/Typora/image/image-20220721153116088.png" alt="image-20220721153116088" style="zoom:80%;" />

7. redis默认不是后台启动的，修改配置文件

   <img src="../../../../../Download/Typora/image/image-20220721153140496.png" alt="image-20220721153140496" style="zoom:80%;" />

8. 启动redis服务

   ```bash
   #进入bin路径
   pwd
   --> /usr/local/bin
   #启动服务(redis服务+通过指定配置文件启动)
   redis-server kconfig/redis.conf
   #使用redis客户端建立连接(h指定主机默认是本地地址，p指定端口号)
   redis-cli -p 6379
   #测试连接
   127.0.0.1:6379>ping
   PONG
   #设置键
   127.0.0.1:6379>set name pz
   127.0.0.1:6379>get name
   127.0.0.1:6379>keys *
   ```

9. 查看redis进程是否开启

   ```bash
   # ps -ef 查看父进程的信息
   # |    在Linux这个叫做管道符    A|B
   #grep  查找文件中符合条件的字符串，过滤进程信息
   ps -ef|grep java  
   ```

   <img src="../../../../../Download/Typora/image/image-20220721153258513.png" alt="image-20220721153258513" style="zoom:80%;" />

10. 关闭redis服务

    ```bash
    #在连接状态下执行
    shutdown
    ```

    <img src="../../../../../Download/Typora/image/image-20220721153338444.png" alt="image-20220721153338444" style="zoom:80%;" />

11. 查看进程是否存在

    <img src="../../../../../Download/Typora/image/image-20220721153357274.png" alt="image-20220721153357274" style="zoom:80%;" />

## 性能测试

redis-benchmark是一个压力测试工具

```bach
redis-benchmark 命令参数
```

<img src="../../../../../Download/Typora/image/image-20220721153445743.png" alt="image-20220721153445743" style="zoom:80%;" />

```bash
#测试：100个并发连接，100000个请求
redis-benchmark -h localhost -p 6379 -c 100 -n 100000
```

<img src="../../../../../Download/Typora/image/image-20220721153514238.png" alt="image-20220721153514238" style="zoom:80%;" />

## 基础知识

> redis默认有16个数据库

```bash
vim redis.conf

databases 16
```

默认使用第0个数据库。可以使用select进行数据库的切换

```bash
#切换到下标三的数据库
select 3
#查看数据库大小
DBSIZE
#设置key
set name pz
#得到key
get name
#查看所有的key
keys *
#清除当前数据库
flushdb
#清除全部数据库的内容
flushall
```

<img src="../../../../../Download/Typora/image/image-20210714163344541.png" alt="image-20210714163344541" style="zoom:80%;" />

> Redis是单线程

明白Redis是很快的，官方表示，Redis是基于内存操作，cpu不是Redis性能瓶颈，Redis的瓶颈是根据机器的内存和网络带宽来决定，既然可以使用单线程实现，就使用单线程了。

Redis是C语言写的，官方提供的数据为100000+的QPS，完全不比同样是使用key-value的Memecache差

redis 内部使⽤⽂件事件处理器` file event handler `，这个⽂件事件处理器是单线程的，所以redis 才叫做单线程的模型。它采⽤ IO 多路复⽤机制同时监听多个 socket，根据 socket 上的事件来选择对应的事件处理器进⾏处理

⽂件事件处理器的结构包含 4 个部分：

* 多个 socket
* IO 多路复⽤程序
* ⽂件事件分派器
* 事件处理器（连接应答处理器、命令请求处理器、命令回复处理器）

多个 socket 可能会并发产⽣不同的操作，每个操作对应不同的⽂件事件，但是 IO 多路复⽤程序会监听多个 socket，会将 socket 产⽣的事件放⼊队列中排队，事件分派器每次从队列中取出⼀个事件，把该事件交给对应的事件处理器进⾏处理。

> **redis为什么单线程还这么快**
>
> 误区：
>
> 1. 高性能的服务器一定是多线程的？
> 2. 多线程一定比单线程效率高？（多线程中CPU会进行上下文的切换，速度:CPU>内存>硬盘）

**核心：redis是将所有的数据全部存放在内存中，所以说使用单线程去操作效率就是最高的，（多线程CPU进行上下文的切换这是一个耗时的操作），对于内存来说，如果没有上下文切换效率就是最高的！多次读写都是在一个CPU上的，在内存情况下，这就是最佳方案。**

# redis 常⻅数据结构

> Redis 是一种开源（BSD 许可）、内存中数据结构存储，用作数据库、缓存和消息代理。Redis 提供了诸如字符串、散列、列表、集合、带范围查询的排序集合、位图、超级日志、地理空间索引和流等数据结构。Redis 内置复制、Lua 脚本、LRU 驱逐、事务和不同级别的磁盘持久化，并通过 Redis Sentinel 和 Redis Cluster 自动分区提供高可用性。

## Redis-key

```bash
#判断指定key是否存在
exists name
#移除key：指定的name从数据库1中移除
move name 1
#设置key-value: name-qinjiang
set name qinjiang
get name 
#设置过期时间：设计10秒钟过期
expire name 10
#查看剩余时间
ttl name
#查看key的类型
type name
```

## String(字符串)

String数据结构是简单的key-value类型，value其实不仅可以是String，也可以是数字。 常规keyvalue缓存应⽤； 常规计数，微博数，粉丝数,对象缓存存储等。

```bash
#在name的value上追加字符串,如果name不存在这相当于set key
append name "hello"
#获取字符串长度
strlen name 


#设置value为数字
set view 0
#自增加1
incr view
#减1
decr view
#加法
incrby view 10
#减法
decrby view 5


#截取字符串(索引0到3)
getrange key 0 3
#获取全部字符串
getrange key 0 -1
#替换指定位置开始的字符串
setrange key 1 xx

#setex (set with expire) 设置过期时间
#setnx (set if not exist) 不存在在设置
```

<img src="../../../../../Download/Typora/image/image-20220721161650636.png" alt="image-20220721161650636" style="zoom:80%;" />

```bash
#批量设置m~
#批量设置key-value
mset k1 v1 k2 v2 k3 v3
#批量获取
mget k1 k2 k3
#批量设置不存在再设置(这是原子性操作，要么一起成功要么一起失败，只要一个设置失败后面的都失败了)
msetnx k1 v1 k4 v4

#对象
set user:1{name:zhangsan,age:3}     #设置一个user:1(user中id为1)的对象 值为 json字符来保存对象
#key的巧妙设计：user:{id}:{filed}
127.0.0.1:6379> mset user:1:name zhangsan user:1:age 2
OK
127.0.0.1:6379> mget user:1:name user:1:age
1) "zhangsan"
2) "2"

#先get再set
getset
127.0.0.1:6379> getset db redis # 如果不存在值，则返回ni1
(ni1)
127.0.0.1:6379> get db
"redis
127.0. 0.1:6379> getset db mongodb # 如果存在值，获取原来的值，并设置新的值
"redis"
127.0.0.1:6379> get db
"mongodb"
```

## List（列表）

list 就是链表，Redis list 的应⽤场景⾮常多，也是Redis最重要的数据结构之⼀，⽐如微博的关注列表，粉丝列表，消息列表等功能都可以⽤Redis的 list 结构来实现。

Redis list 的实现为⼀个双向链表，即可以⽀持反向查找和遍历，更⽅便操作，不过带来了部分额外的内存开销。

另外可以通过 lrange 命令，就是从某个元素开始读取多少个元素，可以基于 list 实现分⻚查询，这个很棒的⼀个功能，基于 redis 实现简单的⾼性能分⻚，可以做类似微博那种下拉不断分⻚的东⻄（⼀⻚⼀⻚的往下⾛），性能⾼。

> 在redis里面,我们可以把list玩成, 栈、队列、阻塞队列!
>
> 所有的list命令都是用 `l` 开头的

```bash
127.0.0.1:6379> LPUSH 1ist one # 将一个值或者多个值，插入到列表头部(左)
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1  #获取1ist中值!
1) "three"
2) " two"
3) "one"
127.0.0.1:6379> LRANGE list 0 1  #通过区间获取具体的值!
1) "three" 
2) "two"
127.0. 0.1:6379> Rpush list righr #将一个值或者多个值，插入到列表位部(右)
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "righr"

127.0.0.1:6379> Lpop list # 移除list的第-一个元素
"three"
127.0.0.1:6379> Rpop list # 移除list的最后一个元素
"righr" 
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> lindex list 1 # 通过下标获得1ist中的某一个值!
"one"
127.0.0.1:6379> Llen list #返回列表的长度
(integer) 3
127.0.0.1:6379> lrem list 1 one #移除list集合中指定个数的value,精确匹配
(integer) 1

#trim修购。; list 截断!
127.0.0.1:6379> 1trim mylist 1 2  #通过下标截取指定的长度，这个list已经被改变了，截断了只剩下截取的元素!
OK

rpop1push #移除列表的最后一个元素，将他移动到新的列表中!
127.0.0.1:6379> rpush my1ist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "he11ol"
(integer) 2
127.0.0.1:6379> rpush mylist "he11o2"
(integer) 3
127.0.0.1:6379> rpoplpush mylist myotherlist #移除列表的最后一个元素，将他移动到新的列表中!
"he1lo2"
127.0.0.1:6379> lrange mylist 0 -1 #查看原来的列表
1) "hello" 
2) "hel1o1"
127.0.0.1:6379> lrange myotherlist 0 -1
1) "he11o2" 

1set 将列表中指定下标的值替换为另外一个值，更新操作
127.0.0.1:6379> EXISTS list  # 判断这个列表是否存在
(integer) 0
127.0.0.1:6379> lset list 0 item 	#如果不存在列表我们去更新就会报错
(error) ERR no such key
127.0.0.1:6379> lpush list valuel
(integer) 1
127.0.0.1:6379> LRANGE list 0 0 
1) "value1"
127.0.0.1:6379> lset list 0 item	#如果存在，更新当前下标的值
OK
127.0.0.1:6379> LRANGE list 0 0
1) "item"
127.0.0.1:6379> lset list 1 other #如果不存在，则会报错!
(error) ERR index out of range

1insert #将某个具体的value插入到列把你中某个元素的前面或者后面!
127.0.0.1:6379> Rpush mylist "he11o"
(integer) 1
127.0.0.1:6379> Rpush mylist "world"
(integer) 2
127.0.0. 1:6379> LINSERT mylist before "world" "other"
(integer) 3
127.0.0.1:6379> LRANGE mylist 0 -1
1) "he1lo"
2) "other"
3) "world"
127.0.0.1:6379> LINSERT mylist after world new
(integer) 4
127.0.0.1:6379> LRANGE mylist 0 -1
1) "he1lo" 
2) "other"
3) "world"
4) "new"
```

> 小结
>
> * 如果key不存在,创建新的链表
> * 如果key存在,新增内容
> * 如果移除了所有值,空链表,也代表不存在!
> * 在两边插入或者改动值,效率最高!中间元素,相对来说效率会低一点~

## Set（集合）

set 对外提供的功能与list类似是⼀个列表的功能，特殊之处在于 set 是可以⾃动排重的。

当你需要存储⼀个列表数据，⼜不希望出现重复数据时，set是⼀个很好的选择，并且set提供了判断某个成员是否在⼀个set集合内的重要接⼝，这个也是list所不能提供的。可以基于 set 轻易实现交集、并集、差集的操作。

⽐如：在微博应⽤中，可以将⼀个⽤户所有的关注⼈存在⼀个集合中，将其所有粉丝存在⼀个集合。Redis可以⾮常⽅便的实现如共同关注、共同粉丝、共同喜好等功能。这个过程也就是求交集的过程，具体命令如下：

```bash
127.0.0.1:6379> sadd myset "hel1o"	# set集合中添加
(integer) 1
127.0.0.1:6379> sadd myset "kuangshen
(integer) 1
127.0.0.1:6379> sadd myset "lovekuangshen"
(integer) 1
127.0.0.1:6379> SMEMBERS myset	#查看指定set的所有值
1) "he11o"
2) "lovekuangshen"
3) "kuangshen"
127.0.0.1:6379> SISMEMBER myset he11o	#判断某一个值是不是在set集合中! 
(integer) 1
127.0.0.1:6379> SISMEMBER myset world
(integer) 0

127.0.0.1:6379> scard myset  #获取set集合中的内容元素个数! 
(integer) 4

127.0.0.1:6379> srem myset he11o	# 移除set集合中的指定元素
(integer) 1
127.0.0.1:6379> scard myset
(integer) 3
127.0.0.1:6379> SMEMBERS myset
1) "1ovekuangshen2"
2) "lovekuangshen
3) "kuangshen"

127.0.0.1:6379> SRANDMEMBER myset # 随机抽选出一个元素
"kuangshen'
127. 0.0.1:6379> SRANDMEMBER myset
"kuangshen"
127.0.0.1:6379> SRANDMEMBER myset 2 # 随机抽选出指定个数的元素
1) "lovekuangshen '
2) "lovekuangshen2"
127.0.0.1:6379> SRANDMEMBER myset 2
1) "lovekuangshen"
2) "1 ovekuangshen2"
127. 0.0.1:6379> SRANDMEMBER myset	#随机抽选出一个元素
"lovekuangshen2"

127.0. 0.1:6379> spop myset # 随机删除一些set集合中的元素!
"1ovekuangshen2"

127.0.0.1:6379> smove myset myset2 "kuangshen" #将一个指定的值，移动到另外一个set集合!
(integer) 1
127.0. 0.1:6379> SMEMBERS myset
1) "world"
2) "he11o"
127. 0.0.1:6379> SMEMBERS myset2
1) "kuangshen"
2) "set2"

微博，B站，共同关注! (并集)
数字集合类:
- 差集 SDIFF
- 交集 SINTER
- 并集 SUNION
127.0.0.1:6379> SDIFF key1 key2 #差集
1) "b"
2) "a"
127.0.0.1:6379> SINTER key1 key2 #交集	共同好友就可以这样实现
1) "c"
127.0.0.1:6379> SUNION key1 key2 #并集
1) "b"
2) "c"
3) "e"
4) "a"
5) "d"
127.0.0.1:6379> sinterstore key1 key2 key3 #将交集存在key1内
```

## Hash(哈希)

hash 是⼀个 string 类型的 field 和 value 的映射表，hash 特别适合⽤于存储对象，后续操作的时候，可以直接仅仅修改这个对象中的某个字段的值。 ⽐如我们可以 hash 数据结构来存储⽤户信息，商品信息等等。⽐如下⾯我就⽤ hash 类型存放了我本⼈的⼀些信息：

```bash
127.0.0.1:6379> hset myhash fie1d1 kuangshen # set一个具体 key-vlaue
(integer) 1
127.0.0.1:6379> hget myhash fie1d1 # 获取一个字段值
"kuangshen"
127.0.0.1:6379> hmset myhash field1 he1lo fie1d2 world # set多个key-vlaue
OK
127.0.0.1:6379> hmget myhash field1 fie1d2 	#获取多个字段值
1) "hello" 
2) "world"
127.0. 0.1:6379> hgeta11 myhash	#获取全部的数据，
1) "field1"
2) "he11o"
3) "field2"
4) "world"
127.0.0.1:6379> hde1 myhash fie1d1 	#删除hash指定key字段!对应的value值也就消失了!
(integer) 1
127.0.0.1:6379> hgetal1 myhash
1) "field2"
2) "world"

127.0.0.1:6379> hmset myhash field1 he11o fie1d2 world
OK
127.0.0.1:6379> HGETALL myhash
1) "field2"
2) "wor1d"
3) "fie1d1"
4) "hel1o"
127.0.0.1:6379> hlen myhash # 获取hash 表的字段数量!
(integer) 2

127.0.0.1:6379> HEXISTS myhash fie1d1 # 判断hash中指定字段是否存在!
(integer) 1
127.0.0.1:6379> HEXISTS myhash field3 
(integer) 0

#只获得所有field
#只获得所有value
127.0.0.1:6379> hkeys myhash #只获得所有field
1) "fie1d2"
2) "field1"
127.0.0.1:6379> hvals myhash # 只获得所有value
1) "world"
2) "he11o"

127.0.0.1:6379> hset myhash field3 5	#指定增量!
(integer) 1
127.0.0.1:6379> HINCRBY myhash fie1d3 1
(integer) 6
127.0.0.1:6379> HINCRBY myhash fie1d3 -1
(integer) 5
127.0.0.1:6379> hsetnx myhash field4 he11o # 如果不存在则可以设置
(integer) 1
127.0.0.1:6379> hsetnx myhash fie1d4 world # 如果存在则不能设置
(integer) 0
```

## Zset(有序集合)

和set相⽐，zset增加了⼀个权重参数score，使得集合中的元素能够按score进⾏有序排列。

举例： 在直播系统中，实时排⾏信息包含直播间在线⽤户列表，各种礼物排⾏榜，弹幕消息（可以理解为按消息维度的消息排⾏榜）等信息，适合使⽤ Redis 中的zset 结构进⾏存储

```bash
127.0.0.1:6379> zadd salary 2500 xiaohong # 添加三个用户
(integer) 1
127.0.0.1:6379> zadd salary 5000 zhangsan
(integer) 1
127.0.0.1:6379> zadd salary 500 kaungshen
I
(integer) 1
#zrangebyscore key min max
#zrevrange
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf # 显示全部的用户从小到大!
1) "kaungshen"
2) "xi aohong"
3) "zhangsan"
127.0.0.1:6379> ZREVRANGE salary 0 -1 #从大到进行排序!
1) "zhangsan"
2) "kaungshen"

127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores #显示全部的用户并且附带成绩
1) "kaungshen"
2) "500"
3) "xi aohong"
4) "2500"
5) " zhangsan"
6) "5000"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 2500 withscores #显示工资小于2500员工的升序排序!
1) "kaungshen"
2) "500"
3) "xiaohong"
4) "2500"
127.0.0.1:6379> zrange salary 0 -1  #查看有序集合中的元素
1) "kaungshen"
2) "xi aohong"
3) "zhangsan"
127.0.0.1:6379> zrem salary xi aohong	#移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "kaungshen"
2) "zhangsan"
127.0.0.1:6379> zcard salary	#获取有序集合中的个数
(integer) 2
127.0.0.1:6379> zadd myset 1 he11o
(integer) 1
127.0.0.1:6379> zadd mysetr 2 world 3 kuangshen
(integer) 2
127.0.0.1:6379> zcount myset 1 3 #获取指定区间的成员数量!
(integer) 3
127.0.0.1:6379> zcount myset 1 2
(integer) 2
```

## 扩展

### geospatial 地理位置

**自 3.2.0 起可用。**

**时间复杂度：** O(log(N)) 对于添加的每个项目，其中 N 是排序集中的元素数。

将指定的地理空间项（经度、纬度、名称）添加到指定的键。数据作为排序集存储在键中，这样就可以使用[GEOSEARCH](https://redis.io/commands/geosearch)命令查询项目。

该命令采用标准格式 x,y 的参数，因此必须在纬度之前指定经度。可编入索引的坐标有限制：非常靠近极点的区域不可编入索引。

EPSG:900913 / EPSG:3785 / OSGEO:41001 指定的确切限制如下：

- 有效经度为 -180 到 180 度。
- 有效纬度是从 -85.05112878 到 85.05112878 度。

当用户尝试索引指定范围之外的坐标时，该命令将报告错误。

**注意：**没有**GEODEL**命令，因为您可以使用[ZREM](https://redis.io/commands/zrem)删除元素。地理索引结构只是一个排序集。

> 这个功能可以推算出地理位置信息，两地之间的距离，方圆几里的人

<img src="../../../../../Download/Typora/image/image-20220721163842068.png" alt="image-20220721163842068" style="zoom:80%;" />

```bash
# getadd 添加地理位置
#规则:两级无法直接添加，我们一般会下载城市数据，直接通过java程序一次性导入!
#有效的经度从-180度到180度。
#有效的纬度从-85. 05112878度到85.05112878度。
#当坐标位置超出上述指定范围时，该命令将会返回一个错误。
# 127.0.0.1:6379> geoadd china:city 39.90 116.40 beijin
(error) ERR invalid longi tude, latitude pair 39 .900000，116.400000
#参数key值(经度、纬度、名称)
127.0.0.1:6379> geoadd china:city 116.40 39.90 beijing
(integer) 1
127.0.0.1:6379> geoadd china:city 121.47 31.23 shanghai
(integer) 1
127.0.0.1:6379> geoadd china:city 106.50 29. 53 chongqi 114.05 22.52 shengzhen
(integer) 2
127.0.0.1:6379> geoadd china:city 120.16 30. 24 hangzhou 108.96 34.26 xian
(integer) 2

#geopos获取指定的城市的经度和纬度!
#获得当前定位:一定是一个坐标值!
127.0.0.1:6379> GEOPOS china:city beijing
1)  1) "116.39999896287918091"
	2) "39.90000009167092543"
127.0.0.1:6379> GEOPOS china:city beijing chongqi 
1)  1) "116.39999896287918091"
	2) "39.90000009167092543"
2)  1) "106.49999767541885376"
	2) "29.52999957900659211"
```

> Redis GEODIST 命令 - 返回两个给定位置之间的距离

如果两个位置之间的其中一个不存在， 那么命令返回空值。

指定单位的参数 unit 必须是以下单位的其中一个：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

```bash
127.0.0.1:6379> GEODIST china:city beijing shanghai km 	#查看上海到北京的直线距离
"1067.3788"
127.0.0.1:6379> GEODIST china:city beijing chongqi km	#查看重庆到北京的直线距离
"1464.0708"
```

> Redis GEORADIUS 命令 - 以给定的经纬度为中心， 找出某一半径内的元素

以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。

范围可以使用以下其中一个单位：

- **m** 表示单位为米。
- **km** 表示单位为千米。
- **mi** 表示单位为英里。
- **ft** 表示单位为英尺。

在给定以下可选项时， 命令会返回额外的信息：

- `WITHDIST`: 在返回位置元素的同时， 将位置元素与中心之间的距离也一并返回。 距离的单位和用户给定的范围单位保持一致。
- `WITHCOORD`: 将位置元素的经度和维度也一并返回。
- `WITHHASH`: 以 52 位有符号整数的形式， 返回位置元素经过原始 geohash 编码的有序集合分值。 这个选项主要用于底层应用或者调试， 实际中的作用并不大。

命令默认返回未排序的位置元素。 通过以下两个参数， 用户可以指定被返回位置元素的排序方式：

- `ASC`: 根据中心的位置， 按照从近到远的方式返回位置元素。
- `DESC`: 根据中心的位置， 按照从远到近的方式返回位置元素。

> 我附近的人? ( 获得所有附近的人的地址,定位! )通过半径来查询!
> 获得指定数量的人, 200
> 所有数据应该都录入: china:city ,才会让结果更加请求!|

```bash
127.0.0.1:6379> GEORADIUS china:city 110 30 1000 km #以110， 30这个经纬度为中心，寻找方圆1000km内的城市
1) "chongqi "
2) "xian"
3) " shengzhen"
4) "hangzhou"
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km 
1) "chongqi"
2) "xian'
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km withdist # 显示到中间距离的位置
1)  1) "chongqi "
	2) "341. 9374 "
2)  1) "xi an"
	2) "483. 8340"
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km withcoord # 显示他人的定位信息
1)  1) "chongqi"
	2)  1) " 106. 49999767541885376"
		2) "29. 52999957900659211"
2)  1) "xi an"
	2)  1) " 108. 96000176668167114"
		2) "34. 25999964418929977" 
127.0.0.1:6379> GEORADIUS china:city 110 30 500 km withdist wi thcoord count 1 #筛选出指定的数量的结果!
1)  1) "chongqi"
	2) "341. 9374 "
	3)  1) ” 106. 49999767541885376"
		2) " 29.52999957900659211"

```

>  Redis GEORADIUSBYMEMBER 命令 - 找出位于指定范围内的元素，中心点是由给定的位置元素决定

这个命令和 [GEORADIUS](https://www.redis.net.cn/order/3689.html) 命令一样， 都可以找出位于指定范围内的元素， 但是 `GEORADIUSBYMEMBER` 的中心点是由给定的位置元素决定的， 而不是像 [GEORADIUS](https://www.redis.net.cn/order/3689.html) 那样， 使用输入的经度和纬度来决定中心点

指定成员的位置被用作查询的中心。

```bash
127.0.0.1:6379> GEORADIUSBYMEMBER china:city beijing 1000 km#找出位于指定元素周围的其他元素!
1) "beijing"I
2) "xi an"
127. 0.0.1:6379> GEORADIUSBYMEMBER china:city shanghai 400 km
1) "hangzhou"
2) "shanghai"
```

> Redis GEOHASH 命令 - 返回一个或多个位置元素的 Geohash 表示

返回一个或多个位置元素的 [Geohash](https://en.wikipedia.org/wiki/Geohash) 表示。

通常使用表示位置的元素使用不同的技术，使用Geohash位置52点整数编码。由于编码和解码过程中所使用的初始最小和最大坐标不同，编码的编码也不同于标准。此命令返回一个**标准的Geohash**，在[维基百科](https://en.wikipedia.org/wiki/Geohash)和[geohash.org](http://geohash.org/)网站都有相关描述

该命令将返回11个字符的Geohash字符串，所以没有精度Geohash，损失相比，使用内部52位表示。

```bash
#将二维的经纬度转换为一维的字符串，如果两个字符串越接近，那么则距离越近!
127.0.0.1:6379> geohash china:city beijing chongqi
1) "wx4fbxxfke0"
2) "wm5xzrybty0"
```

> GEO底层的实现原理其实就是Zset !我们可以使用Zset命令来操作geo !

```bash
127.0.0.1:6379> ZRANGE china:city 0 -1 #查看地图中全部的元素
1) " chongqi "
2)"xian"
3)" shengzhen'
4) "hangzhou'
5) " shanghai"
6) "beijing'
127.0.0.1:6379> zrem china:city beijing # 移除指定元素!
(integer) 1
127.0.0.1:6379> ZRANGE china:city 0 -1
1) "chongqi "
2) "xian"
3) " shengzhen"
4) "hangzhou"
5) "shanghai"
```

### Hyperloglog

> 什么是基数
>
> 例如：A={1,3,5,7,8,7},B={1,3,5,7,8}=>基数（不重复的元素个数）=5（可以接受误差）

> 简介

Redis 2.8.9版本就更新了Hyperloglog数据结构!
Redis Hyperloglog基数统计的算法!

优点:占用的内存是固定, 例如，统计2^64不同的元素的技术,只需要废12KB内存!如果从内存的角度来比较的话HyperLogLog首选。(尽管存在0.81%错误率，但在统计UV任务时可以忽略不计的)

**网页的UV（页面访问量，一个人访问一个网站多次,但是还是算作一 个人!）**

传统的方式，set （元素不允许重复）保存用户的id ,然后就可以统计set中的元素数量作为标准判断!
这个方式如果保存大量的用户id(复杂度高) ,就会比较麻烦!我们的目的是为了计数,而不是保存用户id ;

> 测试使用

```bash
127.0.0.1:6379> PFadd mykey a b c d e f g h i j	#创建第一组元素mykey 
(integer) 1
127.0.0.1:6379> PFCOUNT mykey 	# 统计mykey元素的基数数量
(integer) 10
127. 0.0.1:6379> PFadd mykey2 i j z x c v b n m 	#创建第二组元素mykey2
(integer) 1
127.0.0.1:6379> PFCOUNT mykey2
(integer) 9
127.0.0.1:6379> PFMERGE mykey3 mykey mykey2 	# 合并两组mykey mykey2 => mykey3 并集
0K
127.0.0.1:6379> PFCOUNT mykey3	 # 看并集的数量!
(integer) 15
```

如果允许容错,那么一定可以使用 Hyperloglog !
如果不允许容错,就使用set或者自己的数据类型即可!

### Bitmap

> 位存储
>
> 像统计用户登录和未登录，活跃与不活跃等两种状态的，都可以用bitmap操作

Bitmap位图，数据结构都是操作二进制位来进行记录，只有0和1两个状态！

使用bitmap来记录周一到周日的打卡!
周一: 1 周二:0 周三:0 周四: 1 ......

<img src="../../../../../Download/Typora/image/image-20220721164435517.png" alt="image-20220721164435517" style="zoom:80%;" />



查看某一天是否有打卡!

```bash
127.0.0.1:6379> getbit sign 3
(integer) 1
1270.0.1:6379> getbit sign 6
(integer) 0
```

统计操作,统计打卡的天数!

```bash
127.0.0.1:6379> bitcount sign #统计这周的打卡记录，就可以看到是否有全勤! 
(integer) 3
```

# 事务

## 概念

Redis 通过 MULTI（开启事务）、EXEC（执行事务）、WATCH 等命令来实现事务(transaction)功能。事务提供了⼀种将多个命令请求打包（:一组命令的集合），然后⼀次性、按顺序地执⾏多个命令的机制，并且在事务执⾏期间，服务器不会中断事务⽽改去执⾏其他客户端的命令请求，它会将事务中的所有命令都执⾏完毕，然后才去处理其他客户端的命令请求。

在传统的关系式数据库中，常常⽤ ACID 性质来检验事务功能的可靠性和安全性。在 Redis 中，事务总是具有原⼦性（Atomicity）、⼀致性（Consistency）和隔离性（Isolation），并且当 Redis 运⾏在某种特定的持久化模式下时，事务也具有持久性（Durability）。

==redis同⼀个事务中如果有⼀条命令执⾏失败，其后的命令仍然会被执⾏，没有回滚。==

> 正常执行

```bash
127.0.0.1:6379> multi	#开启事务
0K
#命令入队
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> exec # 执行事务
1) OK
2) OK
3) "v2"
4) OK
```

> 放弃事务

```bash
127.0.0.1:6379> mu1ti	#开启事务
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> DISCARD		#取消事务
OK
127.0.0.1:6379> get k4 		#事务队列中命令都不会被执行!
(ni1)
```

> 编译型异常(代码有问题!命令有错! )， 事务中所有的命令都不会被执行!

```bash
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 v1
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> getset k3	#错误的命令
(error) ERR wrong number of arguments for' getset ’command
127.0.0.1:6379> set k4 v4
QUEUED
127.0.0.1:6379> set k5 v5
QUEUED
127.0.0.1:6379> exec # 执行事务报错!
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379> get k5 # 所有的命令不能运行
(ni1)
```

> 运行时异常( 1/0)，如果事务队列中存在语法性,那么执行命令的时候,其他命令是可以正常执行的,错误命令抛出异常!

```bash
127.0.0.1:6379> set k1 "v1"
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr k1 #会执行的时候失败!
QUEUED
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> get k3
QUEUED
127.0.0.1:6379> exec
1) (error) ERR value is not^ an integer or out of range #虽然第一条命令报错了，但是依旧正常执行成功了!
2) oK
3) oK
4) "v3"
127.0.0.1:6379> get k2
"v2"
127.0.0.1:6379> get k
"v3"
```

## Redis监视测试

悲观锁:

* 很悲观，认为什么时候都会出问题,无论做什么都会加锁!

乐观锁:

* 很乐观，认为什么时候都不会出问题,所以不会上锁!更新数据的时候去判断一下,在此期间是否有人修改过这个数据
* 获取version
* 更新的时候比较version

> 正常执行成功

```bash
127.0.0.1:6379> set money 100
OK
127.0.0.1:6379> set out 0
OK
127.0.0.1:6379> watch money
#监视money对象
OK
127.0.0.1:6379> multi   #事务正常结束，数据期间没有发生其他线程对其的变动，这个时候就正常执行成功!
OK
127.0.0.1:6379> DECRBY money 20 
QUEUED
127.0.0.1:6379> INCRBY out 20
QUEUED
127.0.0.1:6379> exec
1) (integer) 80
2) (integer) 20
```

> 测试多线程修改值,使用watch可以当做redis的乐观锁操作!

```bash
127.0.0.1:6379> watch money		#监视money
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> DECRBY money 10
QUEUED
127.0.0.1:6379> INCRBY out 10
QUEUED
127.0.0.1:6379> exec # 执行之前，另外一个线程，修改了我们的值，这个时候，就会导致事务执行失败!
(ni1)
```

> 如果修改失败,获取最新的值就好

<img src="../../../../../Download/Typora/image/image-20220721165852689.png" alt="image-20220721165852689" style="zoom:80%;" />

# Jedis

> Jedis是Redis官方推荐的java连接开发工具!使用ava操作Redis的中间件!如果你要使用java操作redis ,那么一定要
> 对jedis十分的熟悉!

## 连接

导入对应的依赖

```xml
<!--导入jedis的包-->
<dependencies>
	<!-- https://mvnreposi tory. com/artifact/redis.clients/jedis -->
	<dependency>
		<groupId>redis. clients </groupId>
		<artifactId>jedis</artifactId>
		<version>3.2.0</version>
	</dependency>
	<!--fastjson存数据-->
	<dependency>
		<groupId>com. alibaba</groupId>
		<artifactId>fastjson</artifactId>
		<version>1.2.62</version>
		</ dependency>
</ dependencies>
```

编码测试

```java
// 1、 new Jedis对象即可
Jedis jedis = new Jedis( host: "127.0.0.1", port: 6379);
// jedis所有的命令就是我们之前学习的所有指令!所以之前的指令学习很重要!
System.out.print1n(jedis.ping());
//关闭连接
jedis.close();
```

## 常用API

<img src="../../../../../Download/Typora/image/image-20210720144849693.png" alt="image-20210720144849693" style="zoom:80%;" />

## 事务

`````java
// new Jedis对象即可
Jedis jedis = new Jedis( host: "127.0.0.1", port: 6379);
JSONObject jsonobject=new jsonobject();
jsonobject.put("name1","aaaa");
jsonobject.put("name2","bbbb");
String result=jsonobject.toJSONString();
//开启事务
Transaction multi=jedis.multi();
jedis.flushDB();
jedis.watch(result); 
try{
    multi.set("user1",result);
    multi.set("user2",result);
    multi.exec();//执行事务
}catch(Exception e){
    multi.discard();//放弃事务
    e.printStackTrace();
}finally{
    System.out.println(jedis.get("user1"));
    System.out.println(jedis.get("user2"));
    //关闭连接
	jedis.close();
}
`````

# Spring Boot整合

## 介绍

SpringBoot操作数据：封装在spring-data (jpa、jdbc、mongoDB、redis)中

SpringData和SpringBoot齐名的项目

说明：在SpringBoor2.x之后，原来使用的jedis被替换为lettuce

* jedis :采用的直连,多个线程操作的话,是不安全的,如果想要避免不安全的,使用jedis pool连接池!更像BIO 模式
* lettuce :采用netty ,实例可以再多个线程中进行共享,不存在线程不安全的情况!可以减少线程数据了,更像NIO模式

## 序列化与反序列化的概念
从广义上讲，数据序列化就是将数据结构或者是对象转换成我们可以存储或者传输的数据格式的一个过程，在序列化的过程中，数据结构或者对象将其状态信息写入到临时或者持久性的存储区中，而在对应的反序列化过程中，则可以说是生成的数据被还原成数据结构或对象的过程。

在对象序列化和反序列化角度来看，Java 提供了 Serializable 接口，而 Android 提供特有的 Parcelable 接口。从广义的角度来看，Json ，XML，SharedPreference，SQLite 等都是属于序列化的范畴。

序列化与反序列化的目的
序列化：得到的字节序列可以方便在网络上传输或者持久化——编码。

反序列化：主要从网络/磁盘读取读取字节序列然后转化为对象或者数据结构。——解码

## 程序代码

导入依赖：

```xml
<!-- 操作redis  -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-redis</artifactId>
		</dependency>
```

源码分析：

```java
# SpringBoot所有的配置类，都有一自动配置类   RedisAutoConfiguration
#自动配置类都会绑定一个properties配置文件    RedisProperties


@Bean
@ConditionalonMissingBean(name = "redisTemplate")//若不存在redisTemple实例才生效，自己重新编写redisTemple后回替换
pub1ic RedisTemplate<object， object> redisTemplate (Redi sConnecti onFactory redi sConnecti onFactory)
	throws UnknownHostException {
	//默认的RedisTemplate 没有过多的设置，redis 对象都是需要序列化!
    //两个泛型都是object， object 的类型，我们后使用需要强制转换<String， object>
	RedisTemplate<object，object> template = new RedisTemplate<>();
	template.setConnectionFactory(redisConnectionFactory);
	return template;
}

// 由于string 是redis中 最常使用的类型，所以说单独提出来了- -个bean!
@Bean
@ConditionalonMissingBean
pub1ic StringRedisTemplate stringRedisTemplate (RedisConnectionFactory redi sConnecti onFactory)
	throws UnknownHostException {
	StringRedisTemplate template = new stringRedisTemplate() ;
	template.setConnectionFactory(redisConnectionFactory) ;
	return template;
}
```

配置连接

```properties
#配置redis
spring.redis.host=127.0.0.1
spring.redis.port=6379
```

测试类：

```java
@SpringBootTest
class SpringDataDemoApplicationTests {

	@Autowired
	private RedisTemplate redisTemplate;

	@Test
	void contextLoads() {
		//redisTemplate 操作不同的数据类型，api和指令一样的
		//opsForValue 操作字符串，类似redis中String
		//opsForList 操作List，类似redis中List
		//opsForSet 操作Set，类似redis中Set
		//opsForHash、opsForGeo、opsForZSet...
		//除了基本的操作，我们常用的方法都可以直接通过redisTemplate操作，比如事务，和基本的CRUD

		//获取redis连接对象
		RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
		connection.flushDb();
		connection.flushAll();

		redisTemplate.opsForValue().set("mykey","kuangshen");
		System.out.println(redisTemplate.opsForValue().get( "mykey"));

	}
}
```

<img src="../../../../../Download/Typora/image/image-20210720170434758.png" alt="image-20210720170434758" style="zoom:80%;" />

<img src="../../../../../Download/Typora/image/image-20210720170526877.png" alt="image-20210720170526877" style="zoom:80%;" />

<img src="../../../../../Download/Typora/image/image-20210721092437840.png" alt="image-20210721092437840" style="zoom:80%;" />

```java
@Component
@AllArgsConstructor
@NoArgsConstructor
@Data
//在企业中，pojo类都会序列化,这里Serializable默认是jdk序列化
public class User implements Serializable {
    private String name;
    private Integer age;
}
```

```java
    //出来序列化外也可以转换为json对象
    @Test
	public void test() throws JsonProcessingException {
		//真实的开发一般都使用json来传递对象
		User user= new User("aaa",3);
		//String jsonUser = new ObjectMapper().writeValueAsString(user);
		redisTemplate.opsForValue().set("user",jsonUser);
		System.out.println(redisTemplate.opsForValue().get("user"));
	}
```

<img src="../../../../../Download/Typora/image/image-20210721093116853.png" alt="image-20210721093116853" style="zoom:80%;" />

> 自定义RedisTemplate

```java
@Configuration
public class RedisConfig {

    //编写自定义RedisTemplate
    @Bean
    @ConditionalOnSingleCandidate(RedisConnectionFactory.class)
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        //为了开发方便，一般使用<String, Object>类型
        RedisTemplate<String, Object> template = new RedisTemplate();
        //连接工厂
        template.setConnectionFactory(redisConnectionFactory);

        //序列化配置
        //采用jackson序列化方式
        Jackson2JsonRedisSerializer<Object> objectJackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om=new ObjectMapper();//ObjectMapper对对象objectJackson2JsonRedisSerializer进行转义,才能进行使用
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        objectJackson2JsonRedisSerializer.setObjectMapper(om);
        //采用String序列化方式
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        //key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        //hash的key也String的序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        //value序列化方式采用jackson
        template.setValueSerializer(objectJackson2JsonRedisSerializer);
        //hash-value序列化方式采用jackson
        template.setHashKeySerializer(objectJackson2JsonRedisSerializer);
        //afterPropertiesSet方法，初始化bean的时候执行，可以针对某个具体的bean进行配置,afterPropertiesSet 先执行，init-method 后执行
        template.afterPropertiesSet();

        return template;
    }
}
```

```java
@Autowired
//有时候以下无法进入指定的redisTemplate，使用Qualifier注解解决有歧义问题
@Qualifier("redisTemplate")
private RedisTemplate redisTemplate;
```

```java
// 在企业开发中，一般情况下都不会使用这个原生的方式去编写代发
// 自定义RedisUtils
@Component
public final class RedisUtil {

    //使用@Autowired注解RedisTemplate指向自定义RedisTemplate
    //使用@Resource注解RedisTemplate指向底层的RedisTemplate
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    /**
     * 指定缓存失效时间
     *
     * @param key  键
     * @param time 时间(秒)
     */
    public boolean expire(String key, long time, TimeUnit timeUnit) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, timeUnit);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     *
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }


    /**
     * 判断key是否存在
     *
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除缓存
     *
     * @param key 可以传一个值 或多个
     */
    @SuppressWarnings("unchecked")
    //... 表示多个参数key 
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                redisTemplate.delete((Collection<String>) CollectionUtils.arrayToList(key));
            }
        }
    }


    // ============================String=============================

    /**
     * 普通缓存获取
     *
     * @param key 键
     * @return 值
     */
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     *
     * @param key   键
     * @param value 值
     * @return true成功 false失败
     */

    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 普通缓存放入并设置时间
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */

    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 递增
     *
     * @param key   键
     * @param delta 要增加几(大于0)
     */
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }


    /**
     * 递减
     *
     * @param key   键
     * @param delta 要减少几(小于0)
     */
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }


    // ================================Map=================================

    /**
     * HashGet
     *
     * @param key  键 不能为null
     * @param item 项 不能为null
     */
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }

    /**
     * 获取hashKey对应的所有键值
     *
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * HashSet
     *
     * @param key 键
     * @param map 对应多个键值
     */
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * HashSet 并设置时间
     *
     * @param key  键
     * @param map  对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String, Object> map, long time, TimeUnit timeUnit) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if (time > 0) {
                expire(key, time, timeUnit);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @param time  时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value, long time, TimeUnit timeUnit) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if (time > 0) {
                expire(key, time, timeUnit);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除hash表中的值
     *
     * @param key  键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }


    /**
     * 判断hash表中是否有该项的值
     *
     * @param key  键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }


    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     *
     * @param key  键
     * @param item 项
     * @param by   要增加几(大于0)
     */
    public double hincr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, by);
    }


    /**
     * hash递减
     *
     * @param key  键
     * @param item 项
     * @param by   要减少记(小于0)
     */
    public double hdecr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, -by);
    }


    // ============================set=============================

    /**
     * 根据key获取Set中的所有值
     *
     * @param key 键
     */
    public Set<Object> sGet(String key) {
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 根据value从一个set中查询,是否存在
     *
     * @param key   键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key, Object value) {
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将数据放入set缓存
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 将set数据放入缓存
     *
     * @param key    键
     * @param time   时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key, long time, TimeUnit timeUnit, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if (time > 0) {
                expire(key, time, timeUnit);
            }
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 获取set缓存的长度
     *
     * @param key 键
     */
    public long sGetSetSize(String key) {
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 移除值为value的
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 移除的个数
     */

    public long setRemove(String key, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // ===============================list=================================

    /**
     * 获取list缓存的内容
     *
     * @param key   键
     * @param start 开始
     * @param end   结束 0 到 -1代表所有值
     */
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 获取list缓存的长度
     *
     * @param key 键
     */
    public long lGetListSize(String key) {
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 通过索引 获取list中的值
     *
     * @param key   键
     * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     */
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     */
    public boolean lSet(String key, Object value, long time,TimeUnit timeUnit) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0) {
                expire(key, time,timeUnit);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     * @return
     */
    public boolean lSet(String key, List<Object> value, long time,TimeUnit timeUnit) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0) {
                expire(key, time,timeUnit);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 根据索引修改list中的某条数据
     *
     * @param key   键
     * @param index 索引
     * @param value 值
     * @return
     */

    public boolean lUpdateIndex(String key, long index, Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 移除N个值为value
     *
     * @param key   键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */

    public long lRemove(String key, long count, Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }

    }

    // ===============================HyperLogLog=================================

    public long pfadd(String key, String value) {
        return redisTemplate.opsForHyperLogLog().add(key, value);
    }

    public long pfcount(String key) {
        return redisTemplate.opsForHyperLogLog().size(key);
    }

    public void pfremove(String key) {
        redisTemplate.opsForHyperLogLog().delete(key);
    }

    public void pfmerge(String key1, String key2) {
        redisTemplate.opsForHyperLogLog().union(key1, key2);
    }
}
```

# Redis持久化

Redis是内存数据库,如果不将内存中的数据库状态保存到磁盘,那么一旦服务器进程退出,服务器中的数据库状态也会消失。是为了之后重⽤数据（⽐如重启机器、机器故障之后恢复数据），或者是为了防⽌系统故障⽽将数据备份到⼀个远程位置。Redis提供了持久化功能。

Redis不同于Memcached的很重⼀点就是，Redis⽀持持久化，⽽且⽀持两种不同的持久化操作。**Redis**的⼀种持久化⽅式叫快照（**snapshotting**，**RDB**），另⼀种⽅式是只追加⽂件（**append-only****file,AOF**）。这两种⽅法各有千秋，

## RDB(Redis DataBase)

Redis可以通过创建快照来获得存储在内存⾥⾯的数据在指定的时间间隔的副本。Redis创建快照之后，可以对快照进⾏备份，可以将快照复制到其他服务器从⽽创建具有相同数据的服务器副本（Redis主从结构，主要⽤来提⾼Redis性能），还可以将快照留在原地（写入磁盘）以便重启服务器的时候使⽤。

快照持久化是Redis默认采⽤的持久化⽅式，在redis.conf配置⽂件中默认有此下配置：

<img src="../../../../../Download/Typora/image/image-20210721113356808.png" alt="image-20210721113356808" style="zoom:80%;" />

Redis会单独创建( fork ) 一个子进程来进行持久化,会先将数据写入到一个临时文件中,待持久化过程都结束了,再用这个临时文件替换上次持久化好的文件。整个过程中,主进程是不进行任何I0操作的。这就确保了极高的性能。如果需要进行大规模数据的恢复,且对于数据恢复的完整性不是非常敏感,那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。

**rdb保存的文件是dump.rdb**都是在我们的配置文件中快照中进行配置的! .

<img src="../../../../../Download/Typora/image/image-20210721111720316.png" alt="image-20210721111720316" style="zoom:60%;" />

<img src="../../../../../Download/Typora/image/image-20210721111730304.png" alt="image-20210721111730304" style="zoom:60%;" />

> 触发机制
> 1、save的规则满足的情况下,会自动触发rdb规则
> 2、执行flushall命令,也会触发我们的rdb规则!
> 3、退出redis ,也会产生rdb文件!
>
> 4、系统会设置默认的时间，用于自动生成二进制文件来进行数据持久化(一段时间，进行多少次操作进行保存)
>
> 备份就自动生成一个dump.rdb
>
> <img src="../../../../../Download/Typora/image/image-20210721112227962.png" alt="image-20210721112227962" style="zoom:67%;" />
>
> 如果恢复rdb文件!
>
> 1、只需要将rdb文件放在我们redis启动目录就可以, redis启动的时候会自动检查dump.rdb恢复其中的数据!
>
> 2、查看需要存在的位置
>
> ```bash
> 127.0.0.1:6379> config get dir
> 1) "dir" 
> 2) "/usr/1oca1/bin" #如果在这个目录下存在dump.rdb 文件，启动就会自动恢复其中的数据
> ```
>
> 几乎就他自己默认的配置就够用了，但是我们还是需要去学习!

**优点:**
	1、适合大规模的数据恢复!rdb可以用作备份
	2、对数据的完整性要不高! 

​	3、redis是多路复用的，主进程会fork一个子进程出来，子进程用来复制保存数据

**缺点:**

* 如果说数据需要每次操作每次数据都保存下来，也就是把数据每次都全部保存下来，rdb做不到(比如 300秒内有10次操作就会存盘，但是你如果操作了9次，然后redis崩了，这些数据就不会被保存下来，并且你也不会每次都save一下 )

* fork进行的时候，会占用一定内存空间
* 如果redis意外宕机了，那么这个最后一次修改的数据就没有了

## AOF（Append Only File）

与快照持久化相⽐，AOF持久化 的实时性更好，因此已成为主流的持久化⽅案。默认情况下Redis没有开启AOF（append only file）⽅式的持久化，可以通过appendonly参数开启：

```bash
appendonly yes
```

开启AOF持久化后每执⾏⼀条会更改Redis中的数据的命令，Redis就会将该命令写⼊硬盘中的AOF⽂件。AOF⽂件的保存位置和RDB⽂件的位置相同，都是通过dir参数设置的，默认的⽂件名是appendonly.aof。

在Redis的配置⽂件中存在三种不同的 AOF 持久化⽅式，它们分别是：

```bash
appendfsync always #每次有数据修改发⽣时都会写⼊AOF⽂件,这样会严重降低Redis的速度
appendfsync everysec #每秒钟同步⼀次，显示地将多个写命令同步到硬盘
appendfsync no #让操作系统决定何时进⾏同步
```

为了兼顾数据和写⼊性能，⽤户可以考虑 appendfsync everysec选项 ，让Redis每秒同步⼀次AOF⽂件，Redis性能⼏乎没受到任何影响。⽽且这样即使出现系统崩溃，⽤户最多只会丢失⼀秒之内产⽣的数据。当硬盘忙于执⾏写⼊操作的时候，Redis还会优雅的放慢⾃⼰的速度以便适应硬盘的最⼤写⼊速度。

优点：

- 每一次修改同步，文件的完整性会更好！
- 每秒同步一次，增加容错率，只可能会丢失一秒的数据

缺点：

- 相对于数据数据文件来说，aof远远大于rdb，修复的速度也比rdb慢！
- aof运行效率也要比rdb慢，所以redis默认的配置就是rdb持久化！并且不开启aof

## 同时开启两种持久化方式

* 在这种情况下，当redis重启的时候会优先载入AOF文件来恢复原始的数据， 因为在通常情况下AOF 文件保存的数据集要比RDB文件保存的数据集要完整。
* RDB 的数据不实时，同时使用两者时服务器重启也只会找AOF文件，那要不要 只使用AOF呢？作者建议不要，因为RDB更适合用于备份数据库（AOF在不断变 化不好备份），快速重启，而且不会有AOF可能潜在的Bug，留着作为一个万一 的手段。

# Redis 发布订阅

Redis发布订阅(pub/sub)是一种**消息通信模式**:发送者(pub)发送消息（发送到队列中）,订阅者(sub)接收消息（从队列中拿取）。微信、微博、关注系统!

Redis客户端可以订阅任意数量的频道。

订阅/发布消息图:

第一个:消息发送者，第二个:频道第三个:消息订阅者!

























