> Java中最让人激动的部分便是IO和NIO。IO的全称是input output，是java程序跟外部世界交流的桥梁，IO指的是java.io包中的所有类，他们是从java1.0开始就存在的。NIO叫做new IO，是在java1.4中引入的新一代IO。
>
> IO的本质是什么呢？它和NIO有什么区别呢？

# Java中IO和NIO的本质和区别

## IO的本质

IO的作用就是从外部系统读取数据到java程序中，或者把java程序中输出的数据写回到外部系统。这里的外部系统可能是磁盘，网络流等等。

因为对所有的外部数据的处理都是由操作系统内核来实现的，对于java应用程序来说，只是调用操作系统中相应的接口方法，从而和外部数据进行交互。

所有IO的本质就是对Buffer的处理，我们把数据放入Buffer供系统写入外部数据，或者从系统Buffer中读取从外部系统中读取的数据。如下图所示：

<img src="/img/image/image-20220724160535963.png" alt="image-20220724160535963" style="zoom:80%;" />

用户空间也就是我们自己的java程序有一个Buffer，系统空间也有一个buffer。所以会出现系统空间缓存数据的情况，这种情况下系统空间将会直接返回Buffer中的数据，提升读取速度。

`Buffer 对象是固定数量的数据的容器，其作用是一个存储器，或者分段运输区，在这里，数据可被存储并在之后用于检索。`

##  IO 流分类

* 按照流的流向分，可以分为输⼊流和输出流；
  * 输入流：从文件，网络，压缩包或其它数据源写入内存中目的地（InputStream，Reader）
  * 输出流：源地写出到文件，网络，压缩包或其它数据输出　（OutputStream，Writer）
* 按照操作单元划分，可以划分为字节流和字符流；
* 按照流的⻆⾊划分为节点流和处理流。

Java Io 流共涉及 40 多个类，这些类看上去很杂乱，但实际上很有规则，⽽且彼此之间存在⾮常紧密的联系， Java I0 流的 40 多个类都是从如下 4 个抽象类基类中派⽣出来的。

* InputStream/Reader: 所有的输⼊流的基类，前者是字节输⼊流，后者是字符输⼊流。
* OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

> 不管是⽂件读写还是⽹络发送接收，信息的最⼩存储单元都是字节，那为什么 **I/O** 流操作要分为字节流操作和字符流操作呢？
>
> 字符流是由 Java 虚拟机将字节转换得到的，问题就出在这个过程还算是⾮常耗时，并且，如果我们不知道编码类型就很容易出现乱码问题。所以， I/O 流就⼲脆提供了⼀个直接操作字符的接⼝，⽅便我们平时对字符进⾏流操作。如果⾳频⽂件、图⽚等媒体⽂件⽤字节流⽐᫾好，如果涉及到字符的话使⽤字符流比较好。

IO可以分为File/Block IO和Stream I/O两类。

对于File/Block IO来说，数据是存储在disk中，而disk是由filesystem来进行管理的。我们可以通过filesystem来定义file的名字，路径，文件属性等内容。

filesystem通过把数据划分成为一个个的data blocks来进行管理。有些blocks存储着文件的元数据，有些block存储着真正的数据。

最后filesystem在处理数据的过程中，也进行了分页。filesystem的分页大小可以跟内存分页的大小一致，或者是它的倍数，比如 2,048 或者 8,192 bytes等。

并不是所有的数据都是以block的形式存在的，我们还有一类IO叫做stream IO。

stream IO就像是管道流，里面的数据是序列被消费的。

## DMA和虚拟地址空间

在继续讲解之前，我们先讲解两个操作系统中的基本概念，方便后面我们对IO的理解。

现代操作系统都有一个叫做DMA（Direct memory access,`直接存储器访问`）的组件。这个组件是做什么的呢？

一般来说对内存的读写都是要交给CPU来完成的，在没有DMA的情况下，如果程序进行IO操作，那么所有的CPU时间都会被占用，CPU没法去响应其他的任务，只能等待IO执行完成。这在现代应用程序中是无法想象的。

如果使用DMA，则CPU可以把IO操作转交给其他的操作系统组件，比如数据管理器来操作，只有当数据管理器操作完毕之后，才会通知CPU该IO操作完成。现代操作系统基本上都实现了DMA。

`虚拟地址空间`也叫做（Virtual address space），为了不同程序的互相隔离和保证程序中地址的确定性，现代计算机系统引入了虚拟地址空间的概念。简单点讲可以看做是跟实际物理地址的映射，通过使用分段或者分页的技术，将实际的物理地址映射到虚拟地址空间。

<img src="/img/image/image-20220724161734809.png" alt="image-20220724161734809" style="zoom:80%;" />

> ==**CPU 寻址了解吗?为什么需要虚拟地址空间?**==
>
> 现代处理器使⽤的是⼀种称为 虚拟寻址**(Virtual Addressing)** 的寻址⽅式。使⽤虚拟寻址，**CPU** 需要将虚拟地址翻译成物理地址，这样才能访问到真实的物理内存。 实际上完成虚拟地址转换为物理地址转换的硬件是 CPU 中含有⼀个被称为 内存管理单元（**Memory Management Unit, MMU**） 的硬件。
>
> 先从没有虚拟地址空间的时候说起吧！没有虚拟地址空间的时候，程序都是直接访问和操作的都是物理内存 。但是这样有什么问题呢?
>
> * ⽤户程序可以访问任意内存，寻址内存的每个字节，这样就很容易（有意或者⽆意）破坏操作系统，造成操作系统崩溃。
> * 想要同时运⾏多个程序特别困难，⽐如你想同时运⾏⼀个微信和⼀个 QQ ⾳乐都不⾏。为什么呢？举个简单的例⼦：微信在运⾏的时候给内存地址 1xxx 赋值后，QQ ⾳乐也同样给内存地址1xxx 赋值，那么 QQ ⾳乐对内存的赋值就会覆盖微信之前所赋的值，这就造成了微信这个程序就会崩溃。
>
> 总结来说：如果直接把物理地址暴露出来的话会带来严重问题，⽐如可能对操作系统造成伤害以及给同时运⾏多个程序造成困难。
>
> **通过虚拟地址访问内存有以下优势：**
>
> * 程序可以使⽤⼀系列相邻的虚拟地址来访问物理内存中不相邻的⼤内存缓冲区
> * 程序可以使⽤⼀系列虚拟地址来访问⼤于可⽤物理内存的内存缓冲区。当物理内存的供应量变⼩时，内存管理器会将物理内存⻚（通常⼤⼩为 4 KB）保存到磁盘⽂件。数据或代码⻚会根据需要在物理内存与磁盘之间移动。
> * 不同进程使⽤的虚拟地址彼此隔离。⼀个进程中的代码⽆法更改正在由另⼀进程或操作系统使⽤的物理内存。
>
> ==**虚拟内存意义**==
>
> 这个在我们平时使⽤电脑特别是 Windows 系统的时候太常⻅了。很多时候我们使⽤点开了很多占内存的软件，这些软件占⽤的内存可能已经远远超出了我们电脑本身具有的物理内存。为什么可以这样呢？ 正是因为 虚拟内存 的存在，通过虚拟内存 可以让程序可以拥有超过系统物理内存⼤⼩的可⽤内存空间。另外，虚拟内存为每个进程提供了⼀个⼀致的、私有的地址空间，它让每个进程产⽣了⼀种⾃⼰在独享主存的错觉（每个进程拥有⼀⽚连续完整的内存空间）。这样会更加有效地管理内存并减少出错。
>
> 虚拟内存是计算机系统内存管理的⼀种技术，我们可以⼿动设置⾃⼰电脑的虚拟内存。不要单纯认为虚拟内存只是“使⽤硬盘空间来扩展内存“的技术。虚拟内存的重要意义是它定义了⼀个连续的虚拟地址空间，并且 把内存扩展到硬盘空间。

对于上面的IO的基本流程图中，我们可以将系统空间的buffer和用户空间的buffer同时映射到虚拟地址空间的同一个地方。这样就省略了从系统空间拷贝到用户空间的步骤。速度会更快。

同时为了解决虚拟空间比物理内存空间大的问题，现代计算机技术一般都是用了分页技术。

分页技术就是将虚拟空间分为很多个page，只有在需要用到的时候才为该page分配到物理内存的映射，这样物理内存实际上可以看做虚拟空间地址的缓存。

虚拟空间地址分页对IO的影响就在于，IO的操作也是基于page来的。

比较常用的page大小有：1,024, 2,048, 和 4,096 bytes。

## IO和NIO的区别

java1.0中的IO是流式IO，它只能一个字节一个字节的处理数据，所以IO也叫做Stream IO。

而NIO是为了提升IO的效率而生的，它是以Block的方式来读取数据的。

Stream IO中，input输入一个字节，output就输出一个字节，因为是Stream，所以可以加上过滤器或者过滤器链，可以想想一下web框架中的filter chain。在Stream IO中，数据只能处理一次，你不能在Stream中回退数据。

在Block IO中，数据是以block的形式来被处理的，因此其处理速度要比Stream IO快，同时可以回退处理数据。但是你需要自己处理buffer，所以复杂程度要比Stream IO高。

一般来说Stream IO是阻塞型IO，当线程进行读或者写操作的时候，线程会被阻塞。

而NIO一般来说是非阻塞的，也就是说在进行读或者写的过程中可以去做其他的操作，而读或者写操作执行完毕之后会通知NIO操作的完成。

在IO中，主要分为DataOutPut和DataInput，分别对应IO的out和in。

DataOutPut有三大类，分别是Writer，OutputStream和ObjectOutput。

看下他们中的继承关系：

<img src="/img/image/image-20220724164340318.png" alt="image-20220724164340318" style="zoom:80%;" />

<img src="/img/image/image-20220724164406512.png" alt="image-20220724164406512" style="zoom:80%;" />

DataInput也有三大类，分别是ObjectInput，InputStream和Reader。

看看他们的继承关系：

<img src="/img/image/image-20220724164441347.png" alt="image-20220724164441347" style="zoom:80%;" />

<img src="/img/image/image-20220724164504228.png" alt="image-20220724164504228" style="zoom:80%;" />

ObjectOutput和ObjectInput类比较少，这里就不列出来了。

统计一下大概20个类左右，搞清楚这20个类的用处，恭喜你java IO你就懂了！

对于NIO来说比较复杂一点，首先，为了处理block的信息，需要将数据读取到buffer中，所以在NIO中Buffer是一个非常中要的概念，我们看下NIO中的Buffer：

<img src="/img/image/image-20220724164554060.png" alt="image-20220724164554060" style="zoom:80%;" />

从上图我们可以看到NIO中为我们准备了各种各样的buffer类型使用。

另外一个非常重要的概念是channel，channel是NIO获取数据的通道：

NIO需要掌握的类的个数比IO要稍稍多一点，毕竟NIO要复杂一点。

就这么几十个类，我们就掌握了IO和NIO，想想都觉得兴奋。

## 总结

| IO                      | NIO                         |
| ----------------------- | --------------------------- |
| 面向流(Stream Oriented) | 面向缓冲区(Buffer Oriented) |
| 阻塞IO(Blocking IO)     | 非阻塞IO(NonBlocking IO)    |
|                         | 选择器(Selectors)           |

### 1、面向流和缓冲区

**IO**

传统IO在传输数据时，根据输入输出的不同需要分别建立不同的链接，而且传输的数据是以流的形式在链接上进行传输的

就像自来水要通过水管将自来水厂和家连接起来一样

<img src="/img/image/image-20220724164806317.png" alt="image-20220724164806317" style="zoom:50%;" />

**NIO**

NIO在传输数据时，会在输入输出端之间建立**通道**，然后将数据放入到**缓冲区**中。缓冲区通过通道来传输数据

<img src="/img/image/image-20220724164843912.png" alt="image-20220724164843912" style="zoom:50%;" />

# NIO基础

> non-blocking io 非阻塞io
>
> Java NIO（New IO）是从Java 1.4版本开始引入的一个新的IO API，**可以替代标准的Java IO API**。NIO与原来的IO有同样的作用和目的，但是使用的方式完全不同，NIO支持**面向缓冲区的、基于通道的IO操作**。NIO将以更加高效的方式进行文件的读写操作。
>
> Java NIO系统的核心在于：**通道(Channel)和缓冲区(Buffer)**。通道表示打开到 IO 设备(例如：文件、套接字)的连接。若需要使用 NIO 系统，需要获取用于**连接 IO 设备的通道**以及用于**容纳数据的缓冲区**。然后操作缓冲区，对数据进行处理
>
> ==简而言之，**通道负责传输，缓冲区负责存储**==

## java中线程的基本状态

* 初始状态（new），线程被构建，但是还没有调用start()方法
* 运行状态（runnable），Java 线程将操作系统中的就绪和运行两种状态笼统地称作“运行中”
* 阻塞状态（blocked），表示线程阻塞于锁
* 等待状态（waiting）， 表示线程进入等待状态，进人该状态表示当前线程需要等待其他线程做出一些特定动作(通知或中断)
* 超时等待状态（time-waiting），该状态不同于WAITING,它是可以在指定的时间自行返回的
* 终止状态（terminated），表示当前线程已经执行完毕

线程创建之后它将处于 **NEW**（新建） 状态，调⽤ start() ⽅法后开始运⾏，线程这时候处于**READY**（可运⾏） 状态。可运⾏状态的线程获得了 cpu 时间⽚（timeslice）后就处于 **RUNNING**（运⾏） 状态。

==操作系统隐藏 Java 虚拟机（JVM）中的 READY 和 RUNNING 状态，它只能看到 RUNNABLE 状态,所以 Java 系统⼀般将这两个状态统称为 **RUNNABLE**（运⾏中） 状态 。==

当线程执⾏ wait() ⽅法之后，线程进⼊ **WAITING**（等待）状态。进⼊等待状态的线程需要依靠其他线程的通知才能够返回到运⾏状态，⽽ **TIME_WAITING(**超时等待**)** 状态相当于在等待状态的基础上增加了超时限制，⽐如通过 sleep（long millis） ⽅法或 wait（long millis） ⽅法可以将 Java线程置于 TIMED WAITING 状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。当线程调⽤同步⽅法时，在没有获取到锁的情况下，线程将会进⼊到 **BLOCKED**（阻塞） 状态。线程在执⾏Runnable 的 run() ⽅法之后将会进⼊到 **TERMINATED**（终⽌） 状态。

## Selector选择器

在使用Selector之前，处理socket连接还有以下两种方法

### **使用多线程技术**

为每个连接分别开辟一个线程，分别去处理对应的socke连接（客户端通过socke访问服务端）

<img src="/img/image/image-20220724165958714.png" alt="image-20220724165958714" style="zoom:80%;" />

这种方法存在以下几个问题

- 内存占用高
  - 每个线程都需要占用一定的内存，当连接较多时，会开辟大量线程，导致占用大量内存
- 线程上下文切换成本高
- 只适合连接数少的场景
  - 连接数过多，会导致创建很多线程，从而出现问题

### **使用线程池技术**

使用线程池，控制线程最大数，让线程池中的线程去处理连接

<img src="/img/image/image-20220724170049256.png" alt="image-20220724170049256" style="zoom:80%;" />

这种方法存在以下几个问题

- 阻塞模式下，线程仅能处理一个连接
  - 线程池中的线程获取任务（task）后，**只有当其执行完任务之后（断开连接后），才会去获取并执行下一个任务**
  - 若socke连接一直未断开，则其对应的线程无法处理其他socke连接
- 仅适合**短连接**场景（连接后完成业务后立马断开连接）
  - 短连接即建立连接发送请求并响应后就立即断开，使得线程池中的线程可以快速处理其他连接，例如HTTP请求

### **使用选择器**

**selector 的作用就是配合一个线程来管理多个 channel（fileChannel因为是阻塞式的，所以无法使用selector）**，获取这些 channel 上发生的**事件**，这些 channel 工作在**非阻塞模式**下，当一个channel中没有执行任务时，可以去执行其他channel中的任务。**适合连接数多，但流量较少的场景**

<img src="/img/image/image-20220724170238706.png" alt="image-20220724170238706" style="zoom:80%;" />

若事件未就绪，调用 selector 的 select() 方法会阻塞线程，直到 channel 发生了就绪事件。这些事件就绪后，select 方法就会返回这些事件交给 thread 来处理

选择器（Selector）是**SelectableChannle** 对象的多路复用器，Selector 可以同时监控多个SelectableChannel 的IO 状况，也就是说，利用Selector 可使一个单独的线程管理多个Channel。**Selector 是非阻塞IO 的核心**

<img src="/img/image/image-20220724182711045.png" alt="image-20220724182711045" style="zoom:80%;" />

**选择器的创建**

```java
// 创建一个选择器
Selector selector = Selector.open();
```

**绑定选择器**

通过调用通道的register方法可以绑定选择器，register方法有两个参数

- Selector：即绑定哪个选择器
- ops：监听事件类型。ops有4个值可以选择，为**SelectionKey**的静态属性

<img src="/img/image/image-20220724182803930.png" alt="image-20220724182803930" style="zoom:67%;" />

```java
// 让选择器监听一种状态
myChannel.register(selector, SelectionKey.OP_READ);
// 让选择器监听多种状态
myChannel.register(selector, SelectionKey.OP_READ | SelectionKey.OP_ACCEPT);
```

**SelectionKey**

**表示SelectableChannel 和Selector 之间的注册关系**。每次向选择器注册通道时就会选择一个事件(选择键)。选择键包含两个表示为整数值的操作集。操作集的每一位都表示该键的通道所支持的一类可选择操作。

## 缓冲区(Buffer)

### 缓冲区类型

Buffer 就像一个数组，可以保存多个相同类型的数据。根据数据类型不同(**boolean 除外**) 

**Buffer有以下几种**，其中使用较多的是ByteBuffer

- ByteBuffer
  - MappedByteBuffer
  - DirectByteBuffer
  - HeapByteBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer
- CharBuffer

**各种类型的缓冲区中，都有一个对应类型的数组**，如

ByteBuffer

```java
final byte[] hb;                  // Non-null only for heap buffersCopy
```

IntBuffer

```java
final int[] hb;                  // Non-null only for heap buffers
```

**他们的继承关系如下**

<img src="/img/image/image-20220724171324927.png" alt="image-20220724171324927" style="zoom:80%;" />

### 获取缓冲区

通过**allocate方法**可以获取一个对应缓冲区的对象，它是缓冲区类的一个静态方法

```java
// 获取一个容量大小为1024字节的字节缓冲区
ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
```

### 核心属性

缓冲区的父类Buffer中有几个核心属性，如下

- ==capacity==：缓冲区的容量。通过构造函数赋予，一旦设置，无法更改
- ==limit==：缓冲区的界限。位于limit 后的数据不可读写。缓冲区的限制不能为负，并且**不能大于其容量**
- ==position==：**下一个**读写位置的索引（类似PC）。缓冲区的位置不能为负，并且**不能大于limit**
- ==mark==：记录当前position的值。**position被改变后，可以通过调用reset() 方法恢复到mark的位置。**

以上四个属性必须满足以下要求

**mark <= position <= limit <= capacity**

```java
// Invariants: mark <= position <= limit <= capacity
private int mark = -1;
private int position = 0;
private int limit;
private int capacity;
```

### 核心方法

* **put()方法**

  put()方法可以将一个数据放入到缓冲区中。

  进行该操作后，postition的值会+1，指向下一个可以放入的位置。capacity = limit ，为缓冲区容量的值。

  <img src="/img/image/image-20220724171632143.png" alt="image-20220724171632143" style="zoom: 50%;" />

* **flip()方法**

  flip()方法会**切换对缓冲区的操作模式**，由写->读 / 读->写

  如果是写模式->读模式，position = 0 ， limit 指向最后一个元素的下一个位置，capacity不变

  如果是读->写，则恢复为put()方法中的值

  <img src="/img/image/image-20220724171809208.png" alt="image-20220724171809208" style="zoom:50%;" />

* **get()方法**

  get()方法会读取缓冲区中的一个值

  进行该操作后，position会+1，如果超过了limit则会抛出异常

  <img src="/img/image/image-20220724171939678.png" alt="image-20220724171939678" style="zoom:50%;" />

* **rewind()方法**

  该方法**只能在读模式下使用**

  rewind()方法后，会恢复position、limit和capacity的值，变为进行get()前的值

  <img src="/img/image/image-20220724172033596.png" alt="image-20220724172033596" style="zoom:50%;" />

* **clean()方法**

  clean()方法会将缓冲区中的各个属性恢复为最初的状态，position = 0, capacity = limit

  **此时缓冲区的数据依然存在**，处于“被遗忘”状态，下次进行写操作时会覆盖这些数据

  <img src="/img/image/image-20220724172141855.png" alt="image-20220724172141855" style="zoom:50%;" />

* **mark()和reset()方法**

  mark()方法会将postion的值保存到mark属性中

  reset()方法会将position的值改为mark中保存的值

* **compact()方法**

  **此方法为ByteBuffer的方法，而不是Buffer的方法**

  compact会把未读完的数据向前压缩，然后切换到写模式

  数据前移后，原位置的值并未清零，写时会**覆盖**之前的值

  <img src="/img/image/image-20220724172404978.png" alt="image-20220724172404978" style="zoom:80%;" />

> clear()与 compact()
>
> clear只是对position、limit、mark进行重置，而compact在对position进行设置，以及limit、mark进行重置的同时，还涉及到数据在内存中拷贝（会调用arraycopy）。**所以compact比clear更耗性能。**但compact能保存你未读取的数据，将新数据追加到为读取的数据之后；而clear则不行，若你调用了clear，则未读取的数据就无法再读取到了

使用展示

```java
public class Demo1 {
	public static void main(String[] args) {
		ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

		System.out.println("放入前参数");
		System.out.println("position " + byteBuffer.position());
		System.out.println("limit " + byteBuffer.limit());
		System.out.println("capacity " + byteBuffer.capacity());
		System.out.println();

		System.out.println("------put()------");
		System.out.println("放入3个数据");
		byte bt = 1;
		byteBuffer.put(bt);
		byteBuffer.put(bt);
		byteBuffer.put(bt);

		System.out.println("放入后参数");
		System.out.println("position " + byteBuffer.position());
		System.out.println("limit " + byteBuffer.limit());
		System.out.println("capacity " + byteBuffer.capacity());
		System.out.println();

		System.out.println("------flip()-get()------");
		System.out.println("读取一个数据");
		// 切换模式
		byteBuffer.flip();
		byteBuffer.get();

		System.out.println("读取后参数");
		System.out.println("position " + byteBuffer.position());
		System.out.println("limit " + byteBuffer.limit());
		System.out.println("capacity " + byteBuffer.capacity());
		System.out.println();

		System.out.println("------rewind()------");
		byteBuffer.rewind();
		System.out.println("恢复后参数");
		System.out.println("position " + byteBuffer.position());
		System.out.println("limit " + byteBuffer.limit());
		System.out.println("capacity " + byteBuffer.capacity());
		System.out.println();

		System.out.println("------clear()------");
		// 清空缓冲区，这里只是恢复了各个属性的值，但是缓冲区里的数据依然存在
		// 但是下次写入的时候会覆盖缓冲区中之前的数据
		byteBuffer.clear();
		System.out.println("清空后参数");
		System.out.println("position " + byteBuffer.position());
		System.out.println("limit " + byteBuffer.limit());
		System.out.println("capacity " + byteBuffer.capacity());
		System.out.println();
		System.out.println("清空后获得数据");
		System.out.println(byteBuffer.get());

	}
}
```

打印结果

```json
放入前参数
position 0
limit 1024
capacity 1024

------put()------
放入3个数据
放入后参数
position 3
limit 1024
capacity 1024

------flip()-get()------
读取一个数据
读取后参数
position 1
limit 3
capacity 1024

------rewind()------
恢复后参数
position 0
limit 3
capacity 1024

------clear()------
清空后参数
position 0
limit 1024
capacity 1024

清空后获得数据
1
```

### 非直接缓冲区和直接缓冲区

* **非直接缓冲区**

  通过**allocate()**方法获取的缓冲区都是非直接缓冲区（受垃圾回收影响）。这些缓冲区是建立在JVM**堆内存**之中的。

  ```java
  public static ByteBuffer allocate(int capacity) {
      if (capacity < 0)
      throw new IllegalArgumentException();
  
      // 在堆内存中开辟空间
      return new HeapByteBuffer(capacity, capacity);
  }
  
  HeapByteBuffer(int cap, int lim) {        // package-private
      // new byte[cap] 创建数组，在堆内存中开辟空间
      super(-1, 0, lim, cap, new byte[cap], 0);
      /*
      hb = new byte[cap];
      offset = 0;
      */
  }
  ```

  <img src="/img/image/image-20220724172752114.png" alt="image-20220724172752114" style="zoom:80%;" />

  通过非直接缓冲区，想要将数据写入到物理磁盘中，或者是从物理磁盘读取数据。**都需要经过JVM和操作系统**，数据在两个地址空间中传输时，会**copy**一份保存在对方的空间中。所以非直接缓冲区的读取效率较低

* **直接缓冲区**

  **只有ByteBuffer可以获得直接缓冲区**（受垃圾回收影响），通过allocateDirect()获取的缓冲区为直接缓冲区，这些缓冲区是建立在**物理内存**之中的。

  ```java
  public static ByteBuffer allocateDirect(int capacity) {
      return new DirectByteBuffer(capacity);
  }
  
  DirectByteBuffer(int cap) {                   // package-private
  	...
      // 申请物理内存
  	boolean pa = VM.isDirectMemoryPageAligned();
  	...
  }s
  ```

  <img src="/img/image/image-20220724172932914.png" alt="image-20220724172932914" style="zoom:80%;" />

  直接缓冲区通过在操作系统和JVM之间创建**物理内存映射文件**(虚拟地址空间)加快缓冲区数据读/写入物理磁盘的速度。放到物理内存映射文件中的数据就不归应用程序控制了，操作系统会自动将物理内存映射文件中的数据写入到物理内存中，但分配内存的效率效率低，而且使用不当，清理不干净会造成内存泄漏。

## 通道(Channel)

Channel由java.nio.channels 包定义的。Channel 表示**IO 源与目标打开的连接**。Channel 类似于传统的“流”。只不过**Channel 本身不能直接访问数据，Channel 只能与Buffer 进行交互**

* 起初

  应用程序进行读写操作调用函数时，**底层调用的操作系统提供给用户的读写API**，调用这些API时会生成对应的指令，CPU则会执行这些指令。在计算机刚出现的那段时间，**所有读写请求的指令都有CPU去执行**，过多的读写请求会导致CPU无法去执行其他命令，从而CPU的利用率降低

  <img src="/img/image/image-20220724173445865.png" alt="image-20220724173445865" style="zoom:50%;" />

* DMA出现

  后来，**DMA**(Direct Memory Access，直接存储器访问)出现了。当IO请求传到计算机底层时，**DMA会向CPU请求，让DMA去处理这些IO操作**，从而可以让CPU去执行其他指令。DMA处理IO操作时，会请求获取总线的使用权。**当IO请求过多时，会导致大量总线用于处理IO请求，从而降低效率**

  <img src="/img/image/image-20220724173458155.png" alt="image-20220724173458155" style="zoom:50%;" />

* 如今

  了**Channel(通道)**，Channel相当于一个**专门用于IO操作的独立处理器**，它具有独立处理IO请求的能力，当有IO请求时，它会自行处理这些IO请求

  <img src="/img/image/image-20220724173517345.png" alt="image-20220724173517345" style="zoom:50%;" />

### 通道类型

**常见的Channel有以下四种**，其中FileChannel主要用于文件传输，其余三种用于网络通信

- FileChannel
- DatagramChannel（用于UDP传输）
- SocketChannel（用于TCP传输）
- ServerSocketChannel（用于TCP传输）

### 通道API

* `getChannel() `获取通道
* 支持通道的类如下：
  - FileInputStream
  - FileOutputStream
  - RandomAccessFile
  - DatagramSocket
  - Socket
  - ServerSocket

``` java
   public static void main(String[] args) throws IOException {
      // 本地通道
      FileInputStream fileInputStream = new FileInputStream("");
      FileChannel channel1 = fileInputStream.getChannel();

      FileOutputStream fileOutputStream = new FileOutputStream("");
      FileChannel channel2 = fileOutputStream.getChannel();
   
      // 网络通道
      Socket socket = new Socket();
      SocketChannel channel3 = socket.getChannel();
      
      ServerSocket serverSocket = new ServerSocket();
      ServerSocketChannel channel4 = serverSocket.getChannel();

      DatagramSocket datagramSocket = new DatagramSocket();
      DatagramChannel channel5 = datagramSocket.getChannel();
       
      // 最后要关闭通道
       
   }
}
```

**也可以通过通道的静态方法`open()`来获取**

```java
public static void main(String[] args) throws IOException {
   FileChannel open = FileChannel.open(Paths.get(""));
   
   SocketChannel open1 = SocketChannel.open();
   
   ...
}
```

**getChannel()+非直接缓冲区**

- getChannel()获得通道
  - allocate()获得**非直接缓冲区**

通过非直接缓冲区读写数据，需要通过通道来传输缓冲区里的数据

```java
public class Demo4 {
   public static void main(String[] args) {
      // 获取IO源
      FileInputStream is = null;
      FileOutputStream os = null;
      // 获得通道
      FileChannel inChannel = null;
      FileChannel outChannel = null;
       
      // 利用 try-catch-finally 保证关闭
      try {
         is = new FileInputStream("F:\\JDKLearning\\src\\main\\NIO\\day1\\1.jpg");
         os = new FileOutputStream("F:\\JDKLearning\\src\\main\\NIO\\day1\\2.jpg");

         // 获得通道
         inChannel = is.getChannel();
         outChannel = os.getChannel();

         // 获得缓冲区，用于在通道中传输数据
         ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

         // 循环将字节数据放入到buffer中，然后写入磁盘中
         while (inChannel.read(byteBuffer) != -1) {
            // 切换模式
            byteBuffer.flip();
            outChannel.write(byteBuffer);
            byteBuffer.clear();
         }
      } catch (IOException e) {
         e.printStackTrace();
      } finally {
         if (inChannel != null) {
            try {
               inChannel.close();
            } catch (IOException e) {
               e.printStackTrace();
            }
         }
         if (outChannel != null) {
            try {
               outChannel.close();
            } catch (IOException e) {
               e.printStackTrace();
            }
         }
         if (is != null) {
            try {
               is.close();
            } catch (IOException e) {
               e.printStackTrace();
            }
         }
         if (os != null) {
            try {
               os.close();
            } catch (IOException e) {
               e.printStackTrace();
            }
         }
      }
   }
}
```

**open()+直接缓冲区**

- 通过open获得通道
- 通过FileChannel.map()获取直接缓冲区

使用直接缓冲区时，无需通过通道来传输数据，直接将数据放在缓冲区内即可

```java
public class Demo5 {
   public static void main(String[] args) throws IOException {
      // 通过open()方法来获得通道
      FileChannel inChannel = FileChannel.open(Paths.get("F:\\JDKLearning\\src\\main\\NIO\\day1\\1.jpg"), StandardOpenOption.READ);

      // outChannel需要为 READ WRITE CREATE模式
      // READ WRITE是因为后面获取直接缓冲区时模式为READ_WRITE模式
      // CREATE是因为要创建新的文件
      FileChannel outChannel = FileChannel.open(Paths.get("F:\\JDKLearning\\src\\main\\NIO\\day1\\3.jpg"), StandardOpenOption.READ, StandardOpenOption.WRITE, StandardOpenOption.CREATE);

      // 获得直接缓冲区
      MappedByteBuffer inMapBuf = inChannel.map(FileChannel.MapMode.READ_ONLY, 0, inChannel.size());
      MappedByteBuffer outMapBuf = outChannel.map(FileChannel.MapMode.READ_WRITE, 0, inChannel.size());

      // 字节数组
      byte[] bytes = new byte[inMapBuf.limit()];

      // 因为是直接缓冲区，可以直接将数据放入到内存映射文件，无需通过通道传输
      inMapBuf.get(bytes);
      outMapBuf.put(bytes);

      // 关闭缓冲区，这里没有用try-catch-finally
      inChannel.close();
      outChannel.close();
   }
}
```

> 直接缓冲区的读写速度虽然很快，但是会占用很多很多内存空间。如果文件过大，会使得计算机运行速度变慢

### 分散和聚集

* **分散读取**

  分散读取（Scattering Reads）是指**从Channel 中读取的数据“分散”到多个Buffer 中**

  **注意**：按照缓冲区的**顺序**，从Channel 中读取的数据依次将 Buffer 填满

  <img src="/img/image/image-20220724175035514.png" alt="image-20220724175035514" style="zoom:50%;" />

* 聚集写入

  聚集写入（Gathering Writes）是指将**多个Buffer 中的数据“聚集”到Channel**

  按照缓冲区的**顺序**，写入position 和limit 之间的数据到Channel

  <img src="/img/image/image-20220724175718206.png" alt="image-20220724175718206" style="zoom:50%;" />

```java
public class Demo2 {
   public static void main(String[] args) throws IOException {
      FileInputStream is = new FileInputStream("F:\\JDKLearning\\src\\main\\nio\\day2\\计划.txt");
      FileOutputStream os = new FileOutputStream("F:\\JDKLearning\\src\\main\\nio\\day2\\计划2.txt");

      FileChannel inChannel = is.getChannel();
      FileChannel outChannel = os.getChannel();

      // 获得多个缓冲区，并且放入到缓冲区数组中
      ByteBuffer byteBuffer1 = ByteBuffer.allocate(50);
      ByteBuffer byteBuffer2 = ByteBuffer.allocate(1024);
      ByteBuffer[] byteBuffers = {byteBuffer1, byteBuffer2};

      // 分散读取
      inChannel.read(byteBuffers);
      
      // 切换为读模式
      byteBuffer1.flip();
      byteBuffer2.flip();
      
      // 聚集写入
      outChannel.write(byteBuffers);
   }
}
```

# 非阻塞式网络通信

### 1、概念

#### 举例

双11买的快递到了，快递小哥给你打电话说马上到小区门口，麻烦在门口等一下，方便签收快递

- 若为**阻塞式**的，你到了小区门口以后，快递小哥还没到的期间，你什么事儿也做不了
- 若为**非阻塞式**的，快递小哥将快递放到门卫处，门卫给你发消息说快递到了，麻烦去取一下。当快递还没到的时候你可以做你自己的事儿

#### 阻塞式网络通信

传统的IO 流都是阻塞式的。也就是说，当一个线程调用read() 或write() 时，**该线程被阻塞**，直到有一些数据被读取或写入，该线程在此期间不能执行其他任务

因此，在完成网络通信进行IO 操作时，由于线程会阻塞，所以**服务器端必须为每个客户端都提供一个独立的线程进行处理**，当服务器端需要处理大量客户端时，**性能急剧下降**

也就是说，服务器在等待IO准备就绪的期间，**线程处于阻塞状态**，若为单线程，等待期间CPU未执行任何任务，效率降低。所以需要开启多个线程，当某些线程因为等待IO准备就绪时，CPU可以去执行其他线程中的任务。但是线程的创建、切换与销毁的开销也是不小的。当大量的任务到来时，服务器性能也急剧下降。

<img src="/img/image/image-20220724181809286.png" alt="image-20220724181809286" style="zoom: 50%;" />

#### 非阻塞式网络通信

Java NIO 是非阻塞模式的。当线程从某通道进行读写数据时，**若没有数据可用时，该线程可以进行其他任务**。线程通常将非阻塞IO 的空闲时间用于在其他通道上执行IO 操作，所以**单独的线程可以管理多个输入和输出通道**

因此，NIO 可以让服务器端**使用一个或有限几个线程来同时处理连接到服务器端的所有客户端**

<img src="/img/image/image-20220724181906512.png" alt="image-20220724181906512" style="zoom:50%;" />

### 2、使用

#### 阻塞式网络通信演示

```java
public class Demo1 {
   public static void main(String[] args) throws IOException {
      Thread thread1 = new Thread(()->{
         try {
            server();
         } catch (IOException e) {
            e.printStackTrace();
         }
      });

      Thread thread2 = new Thread(()->{
         try {
            client();
         } catch (IOException e) {
            e.printStackTrace();
         }
      });

      thread1.start();
      thread2.start();
   }

   public static void client() throws IOException {
      // 创建客户端通道
      SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 2022));

      // 读取信息
      FileChannel fileChannel = FileChannel.open(Paths.get("F:\\JDKLearning\\src\\main\\nio\\day3\\1.jpg"), StandardOpenOption.READ);

      // 创建缓冲区
      ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

      // 写入数据
      while (fileChannel.read(byteBuffer) != -1) {
         byteBuffer.flip();
         socketChannel.write(byteBuffer);
         byteBuffer.clear();
      }

      fileChannel.close();
      socketChannel.close();
   }

   public static void server() throws IOException {
      // 创建服务端通道
      ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();

      FileChannel fileChannel = FileChannel.open(Paths.get("F:\\JDKLearning\\src\\main\\nio\\day3\\2.jpg"), StandardOpenOption.WRITE, StandardOpenOption.CREATE);

      // 绑定链接
      serverSocketChannel.bind(new InetSocketAddress(2022));

      // 获取客户端的通道
      SocketChannel socketChannel = serverSocketChannel.accept();

      // 创建缓冲区
      ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

      while (socketChannel.read(byteBuffer) != -1) {
         byteBuffer.flip();
         fileChannel.write(byteBuffer);
         byteBuffer.clear();
      }

      socketChannel.close();
      fileChannel.close();
      serverSocketChannel.close();
   }
}
```

#### 非阻塞式网络通信演示

```java
public class DemoNIO {
	public static void main(String[] args) {
		Thread thread1 = new Thread(()->{
			try {
				server();
			} catch (IOException e) {
				e.printStackTrace();
			}
		});

		Thread thread2 = new Thread(()->{
			try {
				client();
			} catch (IOException e) {
				e.printStackTrace();
			}
		});
		thread1.start();
		thread2.start();
	}

	public static void client() throws IOException {
		SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 2020));

		// 设置为非阻塞模式
		socketChannel.configureBlocking(false);

		ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

		Scanner scanner = new Scanner(System.in);
		while (scanner.hasNext()) {
			String str = scanner.next();
			byteBuffer.put(str.getBytes());
			byteBuffer.flip();
			socketChannel.write(byteBuffer);
			byteBuffer.clear();
		}

		byteBuffer.clear();

		socketChannel.close();
	}

	public static void server() throws IOException {
		ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
		serverSocketChannel.configureBlocking(false);
		serverSocketChannel.bind(new InetSocketAddress(2020));

		// 获得选择器
		Selector selector = Selector.open();

		// 将通道注册到选择器中，设定为接收操作
		serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

		// 轮训接受
		while (selector.select() > 0) {
			Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
			// 获得事件的key
			while (iterator.hasNext()) {
				SelectionKey key = iterator.next();
				if (key.isAcceptable()) {
					SocketChannel socketChannel = serverSocketChannel.accept();
					socketChannel.configureBlocking(false);
					socketChannel.register(selector, SelectionKey.OP_READ);
				} else if (key.isReadable()) {
					// 从选择器中获取通道
					SocketChannel socketChannel = (SocketChannel) key.channel();

					ByteBuffer byteBuffer = ByteBuffer.allocate(10);

					while (socketChannel.read(byteBuffer) != -1) {
						int len = byteBuffer.limit();
						byteBuffer.flip();
						System.out.println(new String(byteBuffer.array(), 0, len));
						byteBuffer.clear();
					}
					socketChannel.close();
				}
				iterator.remove();
			}
		}
		serverSocketChannel.close();
	}
}
```















