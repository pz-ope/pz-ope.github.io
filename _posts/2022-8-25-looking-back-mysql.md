# Mysql概述

mysql是一种关系型数据库，在java企业级开发中起着非常重要作用，因为Mysql是免费开源的，==并且⽅便扩展==。MySQL是开放源代码的，因此任何⼈都可以在 GPL(General Public License) 的许可下下载并根据个性化的需要对其进⾏修改。MySQL的默认端⼝号是**3306**。

# 字符集及校对规则

字符集指的是⼀种从⼆进制编码到某类字符符号的映射。校对规则则是指某种字符集下的排序规则。

MySQL中每⼀种字符集都会对应⼀系列的校对规则。

MySQL采⽤的是类似继承的⽅式指定字符集的默认值，每个数据库以及每张数据表都有⾃⼰的默认值，他们逐层继承。⽐如：某个库中所有表的默认字符集将是该数据库所指定的字符集（这些表在没有指定字符集的情况下，才会采⽤默认字符集） 。

# 存储引擎

## MySQL存储引擎

查看**MySQL**提供的所有存储引擎

```sql
mysql> show engines;
```

<img src="/img/image/image-20220825175830566.png" alt="image-20220825175830566" style="zoom:80%;" />

们可以查看出 MySQL 当前默认的存储引擎是InnoDB,并且在5.7版本所有的存储引擎中只有InnoDB 是事务性存储引擎，也就是说只有 InnoDB ⽀持事务。

查看**MySQL**当前默认的存储引擎

```sql
# 可以通过下⾯的命令查看默认的存储引擎。
mysql> show variables like '%storage_engine%';
```

查看表的存储引擎

```sql
show table status like "table_name" ;
```

<img src="/img/image/image-20220825180007950.png" alt="image-20220825180007950" style="zoom:80%;" />

MyISAM是MySQL的默认数据库引擎（5.5版之前）。虽然性能极佳，⽽且提供了⼤量的特性，包括全⽂索引、压缩、空间函数等，但MyISAM不⽀持事务和⾏级锁，⽽且最⼤的缺陷就是崩溃后⽆法安全恢复。不过，5.5版本之后，MySQL引⼊了InnoDB（事务性数据库引擎），MySQL 5.5版本后默认的存储引擎为InnoDB。

⼤多数时候我们使⽤的都是 InnoDB 存储引擎，但是在某些情况下使⽤ MyISAM 也是合适的⽐如读密集的情况下。（如果你不介意 MyISAM 崩溃恢复问题的话）。

## 表锁和行锁

**表锁**

 MySQL中锁定 粒度最⼤ 的⼀种锁，对当前操作的整张表加锁，实现简单，资源消耗也比较少，加锁快，不会出现死锁。其锁定粒度最⼤，触发锁冲突的概率最⾼，并发度最低，MyISAM和 InnoDB引擎都⽀持表级锁。

表锁不会出现死锁，发生锁冲突几率高，并发低。

* 在执行查询语句（select）前，会自动给涉及的所有表加读锁

* 在执行增删改操作前，会自动给涉及的表加写锁。

**行锁**

 MySQL中锁定 粒度最⼩ 的⼀种锁，只针对当前操作的⾏进⾏加锁。 ⾏级锁能⼤⼤减少数据库操作的冲突。其加锁粒度最⼩，并发度⾼，但加锁的开销也最⼤，加锁慢，会出现死锁。

会出现死锁，发生锁冲突几率低，并发高。	

* 在执行查询语句（select）前，会自动给涉及的所有一行数据加读锁

* 在执行增删改操作前，会自动给涉及的一行数据加写锁。

==MySQL的行锁是通过索引加载的，也就是说，行锁是加在索引响应的行上的，要是对应的SQL语句没有走索引，则会全表扫描，行锁则无法实现，取而代之的是表锁，此时其它事务无法对当前表进行更新或插入操作。==

```sql
# 由于是通过非主键或索引选中的，升级为为表级锁，只有当该事务提交并执行玩后，其他修改或查询sql才能获得锁
update user set count=10 where name='xxx';
```

* 表共享读锁

* 表独占写锁

读锁会阻塞写，写锁会阻塞读和写

对表的读操作，不会阻塞其它进程对同一表的读请求，但会阻塞对同一表的写请求。只有当读锁释放后，才会执行其它进程的写操作。

对表的写操作，会阻塞其它进程对同一表的读和写操作，只有当写锁释放后，才会执行其它进程的读写操作。

## MyISAM和InnoDB区别

* **是否⽀持⾏级锁：** MyISAM 只有表级锁(table-level locking)，⽽InnoDB ⽀持⾏级锁(row level locking)和表级锁,默认为⾏级锁。
* **是否⽀持事务和崩溃后的安全恢复：**  **MyISAM** 强调的是性能，每次查询具有原⼦性,其执⾏速度⽐InnoDB类型更快，但是不提供事务⽀持。但是**InnoDB** 提供事务⽀持事务，外部键等⾼级数据库功能。 具有事务(commit)、回滚(rollback)和崩溃修复能⼒(crash recovery capabilities)的事务安全(transaction-safe (ACID compliant))型表。
* **是否⽀持外键：** MyISAM不⽀持，⽽InnoDB⽀持。

⼀般情况下我们选择 InnoDB 都是没有问题的，但是某些情况下你并不在乎可扩展能⼒和并发能⼒，也不需要事务⽀持，也不在乎崩溃后的安全恢复问题的话，选择MyISAM也是⼀个不错的选择。但是⼀般情况下，我们都是需要考虑到这些问题的。

# MySQL基础操作

## 创建数据库和表

### 创建数据库

```sql
create database name;
```

显示所有数据库

```sql
show database;
```

使用指定数据库

```sql
use name;
```

### 创建表

```sql
create table student(
    s_no VARCHAR(20) PRIMARY KEY COMMENT'学生学号',
    s_name VARCHAR(20) NOT NULL COMMENT'学生姓名 不能为空',
    s_sex VARCHAR(10) NOT NULL COMMENT'学生性别',
    s_birthday DATETIME COMMENT'学生生日',
    s_class VARCHAR(20) COMMENT'学生所在的班级'
);
```

展示表

```sql
describe table;
```

<img src="/img/image/image-20211206145411910.png" alt="image-20211206145411910" style="zoom:67%;" />

## 数据的操作

### 基本操作

查看操作

```sql
select * from table;
```

插入操作

```sql
insert into table values('',''...);
```

删除操作

```sql
delete from table where cow = '';
```

修改操作

```sql
update table set cow1 = '' where cow2 = '';
```

### 连接查询

* 内连接

```sql
inner join ... on ...
```

举例：

```sql
select * from person as p inner join card as c on p.card_id = c.id
```

<img src="/img/image/image-20211206152229010.png" alt="image-20211206152229010" style="zoom:80%;" />

**内联查询：两张表通过某个字段合并起来，查询出相关数据**

* 外连接

  * 左外连接

    会把左表里的所有数据都取出来,对于右表，如果有数据就取出，如果没有就为空

    ```sql
    left join ... on ...
    或
    left outer join ... on ... 
    ```

  * 右外连接

    会把右表中的所有数据都取出来,对于左表，如果有数据就取出，如果没有就为空

    ```sql
    RIGHT JOIN ... ON ...
    或
    RIGHT OUTER JOIN ... ON ...
    ```

## 数据约束

### 主键

能够唯一确定表中的一条记录。我们通过给某个字段添加该约束，就可以使得该字段不重复且不为空。

```sql
create table user(
    id int primary key,
    name varchar(20)
);

# 建表后添加主键
alert table user2 add primary key (id);
ALERT TABLE user2 MODIFY id INT PRIMARY KEY;

# 建表后删除主键
ALERT TABLE user2 DROP PRIMARY KEY (id);
```

### 联合主键

此处字段id和name一同作为主键，联合主键要求每个字段**加起来不同即可**（无需每个字段都不同）

此处字段id和name一同作为主键，联合主键要求每个字段**加起来不同即可**（无需每个字段都不同）

```sql
CREATE TABLE user2 (
	id INT,
	name VARCHAR(20),
    PRIMARY KEY(id, name)
);
```

### 自增约束

自增约束一般**与主键搭配使用**

```sql
create table user(
    id int primary key 唯一约束,
    name varchar(20)
);

INSERT INTO user3 (name) VALUES ('name'); //插入成功，自动生成了id
```

### 唯一约束

约束修饰的字段不可以重复

```sql
CREATE TABLE user4 (
	id INT PRIMARY KEY AUTO_INCREAMENT UNIQUE,
	name VARCHAR(20)
);

CREATE TABLE user4 (
	id INT PRIMARY KEY AUTO_INCREAMENT,
	name VARCHAR(20),
	UNIQUE(id, name)
);
```

### 非空约束

```sql
CREATE TABLE user4 (
	id INT NOT NULL,
	name VARCHAR(20),
);
```

### 默认约束

如果我们插入字段时没有传值，就会**使用默认值**

```sql
CREATE TABLE user4 (
	id INT DEFAULT 10,
	name VARCHAR(20),
);
```

### 外键约束

- 主表中没有的数据，在副表中是不可以使用的
- 主表中的数据被副表引用时，是不可以删除的

```sql
CREATE TABLE master(
	id INT PRIMARY KEY,
	name VARCHAR(20)
);

CREATE TABLE pet(
	id INT PRIMARY KEY,
	name VARCHAR(20),
	master_id int,
	foreign key(master_id) references master(id)
);
```

## 三大范式

### 第一范式

**字段还可以拆分的，就不满足第一范式**

比如地址如果写为

```
地址：四川省成都市高新区天府一街
```

就是可以被拆分的

如果字段写为

```
省份：四川省
城市：成都市
区域：高新区
街名：天府一街
```

就是不可拆分的

建表如下

```sql
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCRAEMENT,
	name VARCHAR(20),
	province VARCHAR(20),
	city VARCHAR(20),
	area VARCHAR(20),
	street: VARCHAR(20)
);
```

就是**符合第一范式**的，但**并不是拆分的越详细越好**

### 第二范式

- 满足第一范式的条件下，第二范式要求：**除主键外的每一列，都必须完全依赖于主键**
- 如果出现不完全依赖，则只可能发生在**联合主键**的情况下

**不满足第二范式的例子**

```sql
CREATE TABLE order(
	product_id INT,
	customer_id INT,
	product_name VARCHAR(20),
	customer_name VARCHAR(20),
    PRIMARY KEY(product_id, customer_id)
);
```

此处product_name只依赖于product_id，customer_name只依赖于customer_id，是完全依赖

**满足第二范式的例子**

```sql
CREATE TABLE order(
	id INT PRIMARY KEY,
	product_id INT,
	customer_id INT
);

CREATE TABLE product(
	id INT PRIMARY KEY,
	name VARCHAR(20)
);

CREATE TABLE customer(
	id INT PRIMARY KEY,
    name VARCHAR(20)
);
```

### 第三范式

- 满足第二范式，除主键外的其他列之间不能有传递依赖关系

**不满足第三范式的例子**

```sql
CREATE TABLE order(
	id INT PRIMARY KEY,
	product_id INT,
	customer_id INT,
	customer_phone INT
);
```

此处customer_phone又依赖于customer_id，存在传递依赖关系，不满足第三范式

**满足第三范式的例子**

```sql
CREATE TABLE order(
	id INT PRIMARY KEY,
	product_id INT,
	customer_id INT
);

CREATE TABLE customer(
	id INT PRIMARY KEY,
    name VARCHAR(20),
    phone INT
);
```

# MySQL事务

## 概述

事务是一个最小的不可分割的单元，事务能够保证一个业务的完整性

多条sql语句**要么同时成功，要么同时失败**，这时就要用到事务

事务最经典也经常被拿出来说例⼦就是转账了。假如⼩明要给⼩红转账1000元，这个转账会涉及到两个关键操作就是：将⼩明的余额减少1000元，将⼩红的余额增加1000元。万⼀在这两个操作之间突然出现错误⽐如银⾏系统崩溃，导致⼩明余额减少⽽⼩红的余额没有增加，这样就不对了。事务就是保证这两个关键操作要么都成功，要么都要失败。

## 事务的四⼤特性(ACID)

<img src="/img/image/image-20220825171218325.png" alt="image-20220825171218325" style="zoom:80%;" />

* 原⼦性（**Atomicity**）： 事务是最⼩的执⾏单位，不允许分割。事务的原⼦性确保动作要么全部完成，要么完全不起作⽤；
*  ⼀致性（**Consistency**）： 执⾏事务前后，数据保持⼀致，多个事务对同⼀个数据读取的结果是相同的；
* 隔离性（**Isolation**）： `并发访问数据库时`，⼀个⽤户的事务不被其他事务所⼲扰，各并发事务之间数据库是独⽴的；
* 持久性（**Durability**）： ⼀个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发⽣故障也不应该对其有任何影响。

## 事务的基本操作

* 自动提交、手动提交和回滚

  ```sql
  set autocommit = 0
  查看自动提交状态
  select @@autocommit;
  
  # 指令可以回滚，但需要关闭自动提交
  rollback
  
  # 如果在进行提交操作之后，执行则无法再进行回滚
  commit;
  ```

* 手动开启事务

  ```sql
  # 手动开启事务
  begin;
  或者
  start transaction;
  ```

  **插入数据前**

  <img src="/img/image/image-20211206153127183.png" alt="image-20211206153127183" style="zoom:80%;" />

  **插入数据后**

  ```sql
  BEGIN;
  INSERT INTO person VALUES (4, '老六', 7);
  INSERT INTO person VALUES (5, '老七', 9);
  或者
  START TRANSACTION;
  INSERT INTO person VALUES (4, '老六', 7);
  INSERT INTO person VALUES (5, '老七', 9);
  ```

  **插入数据后**

  ```sql
  BEGIN;
  INSERT INTO person VALUES (4, '老六', 7);
  INSERT INTO person VALUES (5, '老七', 9);
  或者
  START TRANSACTION;
  INSERT INTO person VALUES (4, '老六', 7);
  INSERT INTO person VALUES (5, '老七', 9);
  ```

  <img src="/img/image/image-20211206153203918.png" alt="image-20211206153203918" style="zoom:80%;" />

  **回滚后**

  ```sql
  ROLLBACK;
  ```

  <img src="/img/image/image-20211206153229921.png" alt="image-20211206153229921" style="zoom:80%;" />

 ## 并发事务问题

在典型的应⽤程序中，多个事务并发运⾏，经常会操作相同的数据来完成各⾃的任务（多个⽤户对同⼀数据进⾏操作）。并发虽然是必须的，但可能会导致以下的问题。

* **脏读**（**Dirty read**）**:** 当⼀个事务正在访问数据并且对数据进⾏了修改，⽽这种修改还没有提交到数据库中，这时另外⼀个事务也访问了这个数据，然后使⽤了这个数据。因为这个数据是还没有提交的数据即进行回滚操作了，那么另外⼀个事务读到的这个数据是“脏数据”，依据“脏数据”所做的操作可能是不正确的；
* **丢失修改**（**Lost to modify**）**:** 指在⼀个事务读取⼀个数据时，另外⼀个事务也访问了该数据，那么在第⼀个事务中修改了这个数据后，第⼆个事务也修改了这个数据。这样第⼀个事务内的修改结果就被丢失，因此称为丢失修改。 例如：事务1读取某表中的数据A=20，事务2也读取；
* 不可重复读（**Unrepeatableread**）**:** 指在⼀个事务内多次读同⼀数据。在这个事务还没有结束时，另⼀个事务也访问该数据。那么，在第⼀个事务中的两次读数据之间，由于第⼆个事务的修改导致第⼀个事务两次读取的数据可能不太⼀样。这就发⽣了在⼀个事务内两次读到的数据是不⼀样的情况，因此称为不可重复读；
* 幻读（**Phantom read**）**:** 幻读与不可重复读类似。它发⽣在⼀个事务（T1）读取了⼏⾏数据，接着另⼀个并发事务（T2）插⼊了⼀些数据时。在随后的查询中，第⼀个事务（T1）就会发现多了⼀些原本不存在的记录，就好像发⽣了幻觉⼀样，所以称为幻读。

> **不可重复读和幻读区别**：不可重复读的重点是修改⽐如多次读取⼀条记录发现其中某些列的值被修改，幻读的重点在于新增或者删除⽐如多次读取⼀条记录发现记录增多或减少了。

## 事务隔离级别

**SQL** 标准定义了四个隔离级别：

* **READ-UNCOMMITTED(**读取未提交**)**： 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
* **READ-COMMITTED(**读取已提交**)**： 允许读取并发事务已经提交的数据，可以阻⽌脏读，但是幻读或不可重复读仍有可能发⽣。
* **REPEATABLE-READ(**可重复读**)**： 对同⼀字段的多次读取结果都是⼀致的，除⾮数据是被本身事务⾃⼰所修改，可以阻⽌脏读和不可重复读，但幻读仍有可能发⽣。
* **SERIALIZABLE(**可串⾏化**)**： 最⾼的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执⾏，这样事务之间就完全不可能产⽣⼲扰，也就是说，该级别可以防⽌脏读、不可重复读以及幻读。

MySQL InnoDB 存储引擎的默认⽀持的隔离级别是 **REPEATABLE-READ**（可重读）。我们可以通过

`select @@tx_isolation`; 命令来查看

```sql
mysql> select  @@tx_isolation;
+-----------------+
| @@tx_isolation |
+-----------------+
| REPEATABLE-READ |
+-----------------+
```

> 这⾥需要注意的是：与 SQL 标准不同的地⽅在于 InnoDB 存储引擎在 **REPEATABLE-READ**（可重读） 事务隔离级别下使⽤的是Next-Key Lock 锁算法，因此可以避免幻读的产⽣，这与其他数据库系统(如SQL Server) 是不同的。所以说InnoDB 存储引擎的默认⽀持的隔离级别是 **REPEATABLE-READ**（可重读） 已经可以完全保证事务的隔离性要求，即达到了 SQL标准的 **SERIALIZABLE(**可串⾏化**)** 隔离级别。因为隔离级别越低，事务请求的锁越少，所以⼤部分数据库系统的隔离级别都是 **READCOMMITTED(读取提交内容)** ，但是你要知道的是InnoDB 存储引擎默认使⽤ **REPEAaTABLE-READ**（可重读） 并不会有任何性能损失。
>
> InnoDB 存储引擎在 分布式事务 的情况下⼀般会⽤到 **SERIALIZABLE(**可串⾏化**)** 隔离级别。

# MySQL索引

MySQL索引使⽤的数据结构主要有**BTree**索引 和 哈希索引 。对于哈希索引来说，底层的数据结构就是哈希表，因此在绝⼤多数需求为单条记录查询的时候，可以选择哈希索引，查询性能最快；其余⼤部分场景，建议选择BTree索引。



