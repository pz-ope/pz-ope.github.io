JVM是Java Virtual Machine（Java虚拟机）的缩写，JVM是一种用于计算设备的规范，它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。

引入Java语言虚拟机后，Java语言在不同平台上运行时不需要重新编译。Java语言使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需生成在Java虚拟机上运行的目标代码（字节码），就可以在多种平台上不加修改地运行。

# JVM

## 概述

- **JVM是Java Virtual Machine（Java虚拟机）的缩写**，JVM是一种用于计算设备的规范，它是一个虚构出来的计算机，是**通过在实际的计算机上仿真模拟各种计算机功能来实现的**。
- **引入Java语言虚拟机后，Java语言在不同平台上运行时不需要重新编译**。Java语言使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需生成在Java虚拟机上运行的目标代码（字节码），就可以在多种平台上不加修改地运行。
- **通过JVM，Java实现了平台无关性**，Java语言在不同平台运行时不需要重新编译，只需要在该平台上部署JVM就可以了。因而能实现一次编译多处运行。

## JRE和JDK

JRE：Java Runtime Environment，也就是JVM的运行平台，联系平时用的虚拟机，大概可以理解成**JRE=虚拟机平台+虚拟机本体**(JVM)。类似于你电脑上的VMWare+适用于VMWare的Ubuntu虚拟机。

JDK：Java Develop Kit，Java的开发工具包，JDK本体也是Java程序，因此运行依赖于JRE,由于需要保持JDK的独立性与完整性，JDK的安装目录下通常也附有JRE。目前Oracle提供的Windows下的JDK安装工具会同时安装一个正常的JRE和隶属于JDK目录下的JRE。

## JVM位置

<img src="/img/image/image-20230308175752669.png" alt="image-20230308175752669" style="zoom:67%;" />

## JVM体系结构

<img src="/img/image/image-20230308182114744.png" alt="image-20230308182114744" style="zoom:87%;" />

## 类加载子系统

<img src="/img/image/image-20230308182220033.png" alt="image-20230308182220033" style="zoom:67%;" />

类加载过程：

1. 每个编写的".java"拓展名类文件都存储着需要执行的程序逻辑，这些".java"文件经过Java编译器编译成拓展名为".class"的文件；
2. ".class"文件中保存着Java代码经转换后的虚拟机指令；
3. 当需要使用某个类时，虚拟机将会加载它的".class"文件，并创建对应的class对象，将class文件加载到虚拟机的内存；

<img src="/img/image/image-20230308182653583.png" alt="image-20230308182653583" style="zoom:77%;" />

在虚拟机提供了3种类加载器，启动（Bootstrap）类加载器、扩展（Extension）类加载器、系统（System）类加载器（也称应用类加载器）：

* 启动（Bootstrap）类加载器

  启动类加载器主要加载的是JVM自身需要的类，这个类加载使用C++语言实现的，是虚拟机自身的一部分，它负责将 <JAVA_HOME>/lib路径下的核心类库或-Xbootclasspath参数指定的路径下的jar包加载到内存中，注意必由于虚拟机是按照文件名识别加载jar包的，如rt.jar，如果文件名不被虚拟机识别，即使把jar包丢到lib目录下也是没有作用的(出于安全考虑，Bootstrap启动类加载器只加载包名为java、javax、sun等开头的类)。

* 扩展（Extension）类加载器

  扩展类加载器是指Sun公司(已被Oracle收购)实现的sun.misc.Launcher$ExtClassLoader类，由Java语言实现的，是Launcher的静态内部类，它负责加载<JAVA_HOME>/lib/ext目录下或者由系统变量-Djava.ext.dir指定位路径中的类库，开发者可以直接使用标准扩展类加载器。

* 系统（System）类加载器

  也称应用程序加载器是指 Sun公司实现的sun.misc.Launcher$AppClassLoader。它负责加载系统类路径java -classpath或-D java.class.path 指定路径下的类库，也就是我们经常用到的classpath路径，开发者可以直接使用系统类加载器，一般情况下该类加载是程序中默认的类加载器，通过ClassLoader#getSystemClassLoader()方法可以获取到该类加载器。

流程简图：

<img src="/img/image/image-20230308183033961.png" alt="image-20230308183033961" style="zoom:67%;" />

```java
//代码详解
public class TestClassLoader {
    public static void main(String[] args) {

        Person person_01 = new Person();
        Person person_02 = new Person();
        Person person_03 = new Person();

        //发现person_01，person_02，person_03的hashCode不一致，代表这三个实例化对象虽隶属于一个Class，即Person，但它们的hashCode值不一样，即代表内存空间的首地址不同，即这仨对象开辟在不同的内存空间。
        System.out.println(person_01.hashCode());
        System.out.println(person_02.hashCode());
        System.out.println(person_03.hashCode());

        //Person实例化对象person_01通过getClass()方法得到Class对象Person
        Class Person = person_01.getClass();
        //Person通过getClassLoader()方法得到系统类加载器
        ClassLoader myClassLoader = Person.getClassLoader();
        System.out.println(myClassLoader.hashCode());
        //加载器对象myClassLoader通过getParent()方法得到拓展类加载器
        ClassLoader myParentClassLoader = myClassLoader.getParent();
        System.out.println(myParentClassLoader.hashCode());
        //加载器对象myGPClassLoader通过getParent()方法得到引导类加载器
        ClassLoader myGPClassLoader = myParentClassLoader.getParent();
        System.out.println(myGPClassLoader.hashCode()); //发现报错，无法通过方法获取引导类加载器
    }

}
class Person{}
```

### 双亲委派机制

双亲委派模式要求除了顶层的启动类加载器外，其余的类加载器都应当有自己的父类加载器，请注意双亲委派模式中的父子关系并非通常所说的类继承关系，而是采用组合关系来复用父类加载器的相关代码，类加载器间的关系如下：

<img src="/img/image/image-20230308183253707.png" alt="image-20230308183253707" style="zoom:67%;" />

* 双亲委派机制工作原理

  如果一个类加载器收到了类加载请求，它并不会自己先去加载，而是把这个请求委托给父类的加载器去执行，如果父类加载器还存在其父类加载器，则进一步向上委托，依次递归，请求最终将到达顶层的启动类加载器，如果父类加载器可以完成类加载任务，就成功返回，倘若父类加载器无法完成此加载任务，子加载器才会尝试自己去加载。

* 双亲委派机制优点

  双亲委派是向上委托加载的，所以它可以确保类只被加载一次， **避免重复加载**;

  java的核心API都是通过引导类加载器进行加载的，如果别人通过定义同样路径的类比如java.lang.Integer，类加载器通过向上委托，两个Integer，那么最终被加载的应该是jdk的Integer类，而并非我们自定义的，这样就 **避免了我们恶意篡改核心包的风险** 。

### 沙箱安全机制

沙箱机制就是将 Java 代码限定在虚拟机(JVM)特定的运行范围中，并且严格限制代码对本地系统资源访问，通过这样的措施来保证对代码的有效隔离，防止对本地系统造成破坏。沙箱主要**限制系统资源访问**，那系统资源包括什么？——CPU、内存、文件系统、网络。

组成沙箱的基本组件：

* 字节码校验器(bytecode verifier) :确保Java类文件遵循Java语言规范。这样可以帮助Java程序实现内存保护。但并不是所有的类文件都会经过字节码校验，比如核心类。
* 类装载器(class loader) :其中类装载器在3个方面对Java沙箱起作用
  * **防止恶意代码去干涉善意的代码;**
  * **守护了被信任的类库边界;**
  * **将代码归入保护域,确定了代码可以进行哪些操作。**

## 运行时数据区

<img src="/img/image/image-20230308182249055.png" alt="image-20230308182249055" style="zoom:77%;" />

### 本地方法栈（Native）

内存区域中专门开辟了一块标记区域: **Native Method Stack**，负责登记native方法，在**执行引擎( Execution Engine )执行的时候通过本地方法接口（JNI）加载本地方法库中的方法。**

* 程序中使用：`private native void start0();`

  * **凡是带了native关键字的，说明java的作用范围达不到了，回去调用底层c语言的库**；
  * **会进入本地方法栈，然后去调用本地方法接口将native方法引入执行**；

* 本地方法接口（JNI）

  本地接口的作用是融合不同的编程语言为Java所用，它的初衷是融合C/C++程序, Java在诞生的时候是C/C++横行的时候，想要立足，必须有调用C、C++的程序，然后在内存区域中专门开辟了一块标记区域: Native Method Stack，负责登记native方法，在执行引擎( Execution Engine )执行的时候通过本地方法接口（JNI）加载本地方法库中的方法。

### PC程序计数器

程序计数器: Program Counter Register

**记录要执行的代码位置，防止线程切换重新执行字节码执行引擎修改程序计数器的值**

* 每个线程都有一个程序计数器，是**线程私有**的
* 就是一个指针, **指向方法区中的方法字节码**(用来存储指向像一条指令的地址， 也即将要执行的指令代码)
* 在执行引擎读取下一条指令, 是一个非常小的内存空间，几乎可以忽略不计

### 方法区（Method Area）

- 方法区是**被所有线程共享**，所有字段和方法字节码，以及一些特殊方法，如构造函数，接口代码也在此定义，简单说，所有定义的方法的信息都保存在该区域，此区域属于共享区间。
- **静态变量（static）、常量（final）、类信息(构造方法、接口定义)（Class）、运行时的常量池存在方法区中，但是实例变量存在堆内存中，和方法区无关**

### 栈

* **栈:后进先出,每个线程都有自己的栈，线程私有，栈内存主管程序的运行,生命周期和线程同步,线程结束,栈内存也就是释放**。
* **对于栈来说,不存在垃圾回收问题,一旦线程结束,栈就结束.**
* **栈内存中运行:8大基本类型+对象引用+实例的方法.**
* 栈运行原理:栈桢
* 栈运行原理:栈桢

<img src="/img/image/image-20230308184547276.png" alt="image-20230308184547276" style="zoom:70%;" />

栈细分4部分：

```java
例：int a=7；
```

* 局部变量表：存放局部变量（a）；
* 操作数栈：存放操作数（7）；
* 动态链接：将符号引用转成直接引用（符号引用就是你知道调用了谁，直接引用就是你拿到可要调用的方法的地址）
* 方法出口：方法结束

### 堆

- **一个JVM只有一个堆内存,堆内存的大小是可以调节的,类加载器读取类文件后,一般会把类,方法,常量,变量,我们所有引用类型的真实对象,放入堆中。**
- 堆内存细分为三个区域:
  * 新生区(伊甸园区):Young/New
  * 养老区old
  * 永久区Perm

<img src="/img/image/image-20230308185355032.png" alt="image-20230308185355032" style="zoom:80%;" />

新生区:类的诞生,成长和死亡的地方。分为:

* 伊甸园区:所有对象都在伊甸园区new出来
* **幸存0区和幸存1区:轻GC之后存下来的**

老年区(养老区)：多次轻GC存活下来的对象放在老年区

* 真理:经过研究，99%的对象都是临时对象!

永久区

* 这个区域常驻内存的。用来存放JDK自身携带的Class对象。 Interface元数据，存储的是java运行时的一些环境或
  类信息，这个区域不存在垃圾回收!关闭JVM虚拟就会释放这个区域的内存
* 一个启动类，加载了大量的第三方jar包。Tomcat部署了太多的应用，大量动态生成的反射类。不断的被加载。直
  到内存满，就会出现00M;

jdk1.6之前：永久代，常量池是在方法区;
jdk1.7：永久代，但是慢慢的退化了，去永久代，常量池在堆中
jdk1.8之后：无永久代，常量池在元空间

> **元空间：逻辑上存在，物理上不存在 ，因为：存储在本地磁盘内,不占用虚拟机内存**
>
> <img src="/img/image/image-20230308185919204.png" alt="image-20230308185919204" style="zoom:80%;" />

**默认情况下,JVM使用的最大内存为电脑总内存的四分之一,JVM使用的初始化内存为电脑总内存的六十四分之一.**

### 总结

- **栈：基本类型的变量，对象的引用变量，实例对象的方法**
- **堆：存放由new创建的对象和数组**
- **方法区：Class对象，static变量，常量池（常量）**

## 执行引擎

<img src="/img/image/image-20230308182401207.png" alt="image-20230308182401207" style="zoom:77%;" />





## JVM调优

使用JPofiler工具分析OOM原因

**下载地址：https://www.ej-technologies.com/download/jprofiler/version_92**

常见JVM调优参数

| 配置参数                        | 功能                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| -Xms                            | 初始堆大小。如: -Xms256m                                     |
| -Xmx                            | 最大堆大小。如: -Xmx512m                                     |
| -Xmn                            | 新生代大小。通常为Xmx的1/3或1/4。新生代= Eden + 2个Survivor空间。实际可用空间为= Eden+ 1个Survivor,即90% |
| -XX:NewRatio                    | 新生代与老年代的比例，如-XX:NewRatio=2,则新生代占整个堆空间的1/3,老年代占2/3 |
| -XX:SurvivorRatio               | 新生代中Eden与Surivor的比值。默认值为8。即Eden占新生代空间的8/10，另外两个Surivor各占1/10 |
| -XX:+PrintGCDetails             | 打印GC信息                                                   |
| -XX:+HeapDumpOnOutOfMemoryError | 让虚拟机在发生内存溢出时Dump出当前的内存堆转储快照，以便分析用 |

## 垃圾回收（GC）

- 无法手动垃圾回收，只能手动提醒，等待JVM自动回收；
- GC的作用区在堆（Heap）和方法区中；
- JVM进行GC时，并不是统一对这三区域（新生区，幸存区，老年区）统一回收，回收都是新生代
  * 轻GC（普通GC）只针对于新生区，偶尔作用幸存区（在新生区满的情况下）；
  * 重GC（全局GC）全局释放内存；

### 常见垃圾回收算法

> 引用计数算法

原理是此对象有一个引用，即增加一个计数，删除一个引用则减少一个计数。垃圾回收时，**只用收集计数为 0** 的对象。此算法最致命的是无法处理循环引用的问题。

> 复制算法

此算法把内存空间划为两个相等的区域，每次只使用其中一个区域。**垃圾回收时，遍历当前使用区域，把正在使用中的对象复制到另外一个区域中，同时回收未使用的对象**。此算法每次只处理正在使用中的对象，因此复制成本比较小，同时复制过去以后还能进行相应的内存整理。

优点：不会出现碎片化问题
缺点：需要两倍内存空间，浪费

<img src="/img/image/image-20230308193147862.png" alt="image-20230308193147862" style="zoom:70%;" />

> 标记-清除算法

此算法执行分两阶段。**第一阶段从引用根节点开始标记所用存活的对象，第二阶段遍历整个堆，把未标记的对象清除。**

优点：不会浪费内存空间
缺点：此算法需要暂停整个应用，同时，会产生内存碎片

<img src="/img/image/image-20230308193337966.png" alt="image-20230308193337966" style="zoom:67%;" />

> 标记-压缩算法

此算法结合了“标记-清除”和“复制”两个算法的优点。也是分两阶段，**第一阶段从根节点开始标记所有存活的对象，第二阶段遍历整个堆，清除未标记对象并且把存活对象“压缩”到堆的其中一块，按顺序排放。**
此算法避免了“标记-清除”的碎片问题，同时也避免了“复制”算法的空间问题。

<img src="/img/image/image-20230308193442307.png" alt="image-20230308193442307" style="zoom:67%;" />

- 总结：
  - 内存效率（时间复杂度）：复制算法>标记清除算法>标记压缩算法
  - 内存效率整齐度：复制算法=标记压缩算法>标记清除算法
  - 内存利用率：标记清除算法=标记压缩算法>复制算法

### 分代回收策略

<img src="/img/image/image-20230308193536241.png" alt="image-20230308193536241" style="zoom:80%;" />

- 绝大多数刚刚被创建的对象会存放在Eden区
- 当Eden区第一次满的时候，会触发MinorGC（轻GC）。首先将Eden区的垃圾对象回收清除，并将存活的对象复制到S0，此时S1是空的。
- 下一次Eden区满时，再执行一次垃圾回收，此次会将Eden和S0区中所有垃圾对象清除，并将存活对象复制到S1，此时S0变为空。
- 如此反复在S0和S1之间切换几次（默认15次）之后，还存活的对象将他们转移到老年代中。
- 当老年代满了时会触发FullGC（全GC）

### MinorGC

- 使用的算法是复制算法
- 年轻代堆空间紧张时会被触发
- 相对于全收集而言，收集间隔较短

#### FullGC

- 使用的算法一般是标记压缩算法
- 当老年代堆空间满了，会触发全收集操作
- 可以使用 System.gc()方法来显式的启动全收集
- 全收集非常耗时

## JMM（java内存模型）

**JMM（java内存模型）Java Memory Model，本身是一个抽象的概念，不是真实存在的，它描述的是一组规则或规范，通过这组规范定义了程序中各个变量（包括实例字段、静态字段和构成数组对象的元素）的访问方式。**

<img src="/img/image/image-20230308194048490.png" alt="image-20230308194048490" style="zoom:67%;" />

**JMM定义了线程和主内存之间的抽象关系：线程之间的共享变量存储在主内存（Main Memory）中，每个线程都有一个私有的本地内存（Local Memory），本地内存中存储了该线程读/写共享变量的副本。**

Java内存模型规定了所有的变量都存储在主内存中，每条线程还有自己的工作内存，线程的工作内存中 保存了该线程中是用到的变量的主内存副本拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存。不同的线程之间也无法直接访问对方工作内存中的变量，线程间变量的传递均需要自己的工作内存和主存之间进行数据同步进行。

JMM内存模型三大特性

* 原子性
  使用 synchronized 互斥锁来保证操作的原子性
* 可见性：
  volatile，会强制将该变量自己和当时其他变量的状态都刷出缓存。
  synchronized，对一个变量执行 unlock 操作之前，必须把变量值同步回主内存。
  final，被 final 关键字修饰的字段在构造器中一旦初始化完成，并且没有发生 this 逃逸（其它线程通过 this 引用访问到初始化了一半的对象），那么其它线程就能看见 final 字段的值。
* 有序性
  源代码 -> 编译器优化的重排 -> 指令并行的重排 -> 内存系统的重排 ->最终执行的命令。
  重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。
  处理器在进行重排时必须考虑数据的依赖性，多线程环境线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的。

# jcmd:JDK14中的调试神器

## 简介

jcmd是JDK自带的调试工具，具有非常强大的功能。jcmd是JDK7中正式引入的，有了jcmd，完全可以替换很多常用的其他工具，比如jstak和jmap。

jcmd可以将具体的诊断命令发送给JVM。为了安全起见，使用jcmd的用户必须跟运行的java程序具有同样的用户和用户组。

jcmd的调试命令有很多种，每一种调试命令又有自己的参数。

## jcmd的语法

jcmd的语法比较简单：

```java
jcmd [pid | main-class] command... | PerfCounter.print | -f filename

jcmd [-l]

jcmd -h
```

pid和main-class是二选一：

其中pid表示要发送诊断命令的java进程id。

也可以指定main-class，表示要发送诊断命令给运行该main-class的java进程。

command表示可以在jcmd中运行的命令，我们看下jcmd支持哪些命令：

```java
./jcmd 93989 help
93989:
The following commands are available:
Compiler.CodeHeap_Analytics
Compiler.codecache
Compiler.codelist
Compiler.directives_add
Compiler.directives_clear
Compiler.directives_print
Compiler.directives_remove
Compiler.queue
GC.class_histogram
GC.class_stats
GC.finalizer_info
GC.heap_dump
GC.heap_info
GC.run
GC.run_finalization
JFR.check
JFR.configure
JFR.dump
JFR.start
JFR.stop
JVMTI.agent_load
JVMTI.data_dump
ManagementAgent.start
ManagementAgent.start_local
ManagementAgent.status
ManagementAgent.stop
Thread.print
VM.class_hierarchy
VM.classloader_stats
VM.classloaders
VM.command_line
VM.dynlibs
VM.events
VM.flags
VM.info
VM.log
VM.metaspace
VM.native_memory
VM.print_touched_methods
VM.set_flag
VM.stringtable
VM.symboltable
VM.system_properties
VM.systemdictionary
VM.uptime
VM.version
help
```

Perfcounter.print表示要打印java进程暴露的performance counters。

-f filename表示从文本文件中读取要运行的命令。

-l 列出不是运行在docker中JVM。

-h 表示帮助。

下面我们举几个常用的例子

## 列出运行的JVM

```java
./jcmd -l
98109 jdk.jcmd/sun.tools.jcmd.JCmd -l
```

通过使用jcmd -l可以列出所有正在运行的JVM进程。跟jps是一样的。

## 打印stack信息

使用jcmd pid Thread.print -l可以打印出java程序的stack信息。其中-l表示输出java.util.concurrent的lock信息。

下面看个简单的例子：

```java
./jcmd 93989 Thread.print -l

Full thread dump Java HotSpot(TM) 64-Bit Server VM (14.0.1+7 mixed mode, sharing):

Threads class SMR info:
_java_thread_list=0x00007fbeb1c4cb10, length=12, elements={
0x00007fbeb282a800, 0x00007fbeb282d800, 0x00007fbeb282e800, 0x00007fbeb2830800,
0x00007fbeb2831800, 0x00007fbeb2832000, 0x00007fbeb2833000, 0x00007fbeb3831000,
0x00007fbeb3822000, 0x00007fbeb3174000, 0x00007fbeb3815000, 0x00007fbeb226f800
}

"Reference Handler" #2 daemon prio=10 os_prio=31 cpu=0.64ms elapsed=8996.59s tid=0x00007fbeb282a800 nid=0x4703 waiting on condition  [0x000070000440d000]
   java.lang.Thread.State: RUNNABLE
    at java.lang.ref.Reference.waitForReferencePendingList(java.base@14.0.1/Native Method)
    at java.lang.ref.Reference.processPendingReferences(java.base@14.0.1/Reference.java:241)
    at java.lang.ref.Reference$ReferenceHandler.run(java.base@14.0.1/Reference.java:213)

   Locked ownable synchronizers:
    - None
```

## 打印heap info

使用jcmd pid GC.heap_info可以获得heap info。

```java
./jcmd 93989 GC.heap_info
93989:
 garbage-first heap   total 71680K, used 34410K [0x00000007d4400000, 0x0000000800000000)
  region size 1024K, 20 young (20480K), 4 survivors (4096K)
 Metaspace       used 23810K, capacity 24246K, committed 24752K, reserved 1071104K
  class space    used 2850K, capacity 3015K, committed 3072K, reserved 1048576K
```

## 打印heap dump

如果想知道heap里面到底有什么，则可以通过下面的命令将heap dump出来：

```java
./jcmd 93989 GC.heap_dump heap_dump.out
93989:
Dumping heap to heap_dump.out ...
Heap dump file created [27727979 bytes in 0.643 secs]
```

heap dump需要传入一个文件名，存放dump出来的信息。

## 统计heap使用情况

有时候我们需要统计一下heap中各个对象的使用情况，则可以下面方法：

```java
./jcmd 93989 GC.class_histogram

93989:
 num     #instances         #bytes  class name (module)
-------------------------------------------------------
   1:         25826       11748304  [B (java.base@14.0.1)
   2:          2233        1971800  [I (java.base@14.0.1)
   3:          5154         614928  java.lang.Class (java.base@14.0.1)
   4:         24757         594168  java.lang.String (java.base@14.0.1)
   5:          4491         439432  [Ljava.lang.Object; (java.base@14.0.1)
   6:         13177         421664  java.util.concurrent.ConcurrentHashMapNode (java.base@14.0.1)
   7:          5025         160800  java.util.HashMapNode (java.base@14.0.1)
   8:          8793         140688  java.lang.Object (java.base@14.0.1)
   9:           212         103584  [Ljava.util.concurrent.ConcurrentHashMap$Node; (java.base@14.0.1)
```

上面的结果非常有用，在一些性能调试方法可以起到意想不到的作用。

## JFR功能

jcmd还支持jfr功能。JFR的全称叫做Java Flight Recorder。你可以将其看做是JVM中一些事件的记录器。

# JDK 14的新特性:JFR,JMC和JFR事件流

## 简介

Java Flight Recorder（JFR）是JVM的诊断和性能分析工具。它可以收集有关JVM以及在其上运行的Java应用程序的数据。JFR是集成到JVM中的，所以JFR对JVM的性能影响非常小，我们可以放心的使用它。

一般来说，在使用默认配置的时候，性能影响要小于1%。

JFR的历史很久远了。早在Oracle2008年收购BEA的时候就有了。JFR一般和JMC（Java Mission Control）协同工作。

JFR是一个基于事件的低开销的分析引擎，具有高性能的后端，可以以二进制格式编写事件，而JMC是一个GUI工具，用于检查JFR创建的数据文件。

这些工具最早是在BEA的JRockit JVM中出现的，最后被移植到了Oracle JDK。最开始JFR是商用版本，但是在JDK11的时候，JFR和JMC完全开源了，这意味着我们在非商用的情况下也可以使用了。

而在今天的JDK 14中，引入了一个新的JFR特性叫做JFR Event Streaming，我们将在本文中详细讲解。

## JFR

上面我们简单的介绍了一下JFR。JFR是JVM的调优工具，通过不停的收集JVM和java应用程序中的各种事件，从而为后续的JMC分析提供数据。

Event是由三部分组成的：时间戳，事件名和数据。同时JFR也会处理三种类型的Event：持续一段时间的Event，立刻触发的Event和抽样的Event。

为了保证性能的最新影响，在使用JFR的时候，请选择你需要的事件类型。

JFR从JVM中搜集到Event之后，会将其写入一个小的thread-local缓存中，然后刷新到一个全局的内存缓存中，最后将缓存中的数据写到磁盘中去。

或者你可以配置JFR不写到磁盘中去，但是这样缓存中只会保存部分events的信息。这也是为什么会有JDK14 JEP 349的原因。

开启JFR有很多种方式，这里我们关注下面两种：

1. 添加命令行参数

```java
-XX:StartFlightRecording:<options>
```

启动命令行参数的格式如上所述。

JFR可以获取超过一百种不同类型的元数据。如果要我们一个个来指定这些元数据，将会是一个非常大的功能。所以JDK已经为我们提供了两个默认的profile：default.jfc and profile.jfc。

其中 default.jfc 是默认的记录等级，对JVM性能影响不大，适合普通的，大部分应用程序。而profile.jfc包含了更多的细节，对性能影响会更多一些。

如果你不想使用默认的两个jfc文件，也可以按照你自己的需要来创建。

下面看一个更加完整的命令行参数：

```java
-XX:StartFlightRecording:disk=true,filename=/tmp/customer.jfr,maxage=5h,settings=profile
```

上面的命令会创建一个最大age是5h的profile信息文件。

1. 使用jcmd

命令行添加参数还是太麻烦了，如果我们想动态添加JFR，则可以使用jcmd命令。

```java
jcmd <pid> JFR.start name=custProfile settings=default
jcmd <pid> JFR.dump filename=custProfile.jfr
jcmd <pid> JFR.stop
```

上面的命令在一个运行中的JVM中启动了JFR，并将统计结果dump到了文件中。

上面的custProfile.jfr是一个二进制文件，为了对其进行分析，我们需要和JFR配套的工具JMC。

## JMC

JDK Mission Control 是一个用于对 Java 应用程序进行管理、监视、概要分析和故障排除的工具套件。

在JDK14中，JMC是独立于JDK单独发行的。我们可以下载之后进行安装。

我们先启动一个程序，用于做JFR的测试。

```java
@Slf4j
public class ThreadTest {

    public static void main(String[] args) {
        ExecutorService executorService= Executors.newFixedThreadPool(10);
        Runnable runnable= ()->{
            while(true){
                log.info(Thread.currentThread().getName());
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    log.error(e.getMessage(),e);
                }
            }
        };

        for(int i=0; i<10; i++){
            executorService.submit(runnable);
        }
    }
}
```

很简单的一个程序，启动了10个线程，我们启动这个程序。

然后再去看看JMC的界面：

<img src="/img/image/image-20220730211455700.png" alt="image-20220730211455700" style="zoom:80%;" />

我们可以看到在界面的左边已经可以看到运行在本机的ThreadTest程序了。

点击MBean服务器，可以看到该java程序的面板信息，里面包含CPU，堆栈信息。

在下面有7个tab分别是概览，MBean浏览器，触发器，系统，内存，线程，和诊断命令。

JMC非常强大，也有很多功能，具体的细节大家可以自己运行去体会。

因为本文主要是将JFR，下面我们将讲解如何在JMC中创建JFR和分析JFR。

## 创建JFR

上面右侧的MBean服务器下就是飞行记录器了，也就是我们的目标。

点击飞行记录器：

<img src="/img/image/image-20220730211622790.png" alt="image-20220730211622790" style="zoom:80%;" />

我们就可以开始创建一个JFR了。

目标文件就是JFR的生成地址，名称可以自己随便起一个，记录时间表示需要记录多长时间范围之内的JFR。

点下一步：

<img src="/img/image/image-20220730211719920.png" alt="image-20220730211719920" style="zoom:80%;" />

这一步可以选择更加详细的JVM参数。

点下一步：

<img src="/img/image/image-20220730211940380.png" alt="image-20220730211940380" style="zoom:80%;" />

这里，我们可以选择需要监控的Profile事件选项。可以按照你的需要进行选择。

最后点完成创建JFR。

## 分析JFR

上面我们的JFR记录了1分钟的Profile，在1分钟之后，我们可以看到目标JFR文件生成了。

<img src="/img/image/image-20220730212809867.png" alt="image-20220730212809867" style="zoom:80%;" />

生成完JFR之后，JMC会自动打开生成的JFR文件，我们得到一个大纲视图。

里面包含java应用程序，JVM内部，环境和事件浏览器。

事件浏览器中列出了我们在1分钟之内监控的事件。

<img src="/img/image/image-20220730212828563.png" alt="image-20220730212828563" style="zoom:80%;" />

> *JMC浏览器不仅可以监控本机的应用程序，也可以监控远程的应用程序。由于JMC的连接是通过JMX协议，所以远程java程序需要开启JMX协议的支持。*

## JFR事件

JMC好用是好用，但是要一个一个的去监听JFR文件会很繁琐。接下来我们来介绍一下怎么采用写代码的方式来监听JFR事件。

还是上面的图，如果我们想通过程序来获取“Class Loading Statistics”的信息，可以这样做。

上图的右侧是具体的信息，我们可以看到主要包含三个字段：开始时间，Loaded Class Count和 Unloaded Class Count。

我们的思路就是使用jdk.jfr.consumer.RecordingFile去读取生成的JFR文件，然后对文件中的数据进行解析。

相应代码如下：

```java
@Slf4j
public class JFREvent {

    private static Predicate<RecordedEvent> testMaker(String s) {
        return e -> e.getEventType().getName().startsWith(s);
    }

    private static final Map<Predicate<RecordedEvent>,
            Function<RecordedEvent, Map<String, String>>> mappers =
            Map.of(testMaker("jdk.ClassLoadingStatistics"),
                    ev -> Map.of("start", ""+ ev.getStartTime(),
                            "Loaded Class Count",""+ ev.getLong("loadedClassCount"),
                            "Unloaded Class Count", ""+ ev.getLong("unloadedClassCount")
                    ));

    @Test
    public void readJFRFile() throws IOException {
        RecordingFile recordingFile = new RecordingFile(Paths.get("/Users/flydean/flight_recording_1401comflydeaneventstreamThreadTest21710.jfr"));
        while (recordingFile.hasMoreEvents()) {
            var event = recordingFile.readEvent();
            if (event != null) {
                var details = convertEvent(event);
                if (details == null) {
                    // details为空
                } else {
                    // 打印目标
                    log.info("{}",details);
                }
            }
        }
    }

    public Map<String, String> convertEvent(final RecordedEvent e) {
        for (var ent : mappers.entrySet()) {
            if (ent.getKey().test(e)) {
                return ent.getValue().apply(e);
            }
        }
        return null;
    }
}
```

注意，在convertEvent方法中，我们将从文件中读取的Event转换成了map对象。

在构建map时，我们先判断Event的名字是不是我们所需要的jdk.ClassLoadingStatistics，然后将Event中其他的字段进行转换。最后输出。

运行结果：

```java
{start=2020-04-29T02:18:41.770618136Z, Loaded Class Count=2861, Unloaded Class Count=0}
...
```

可以看到输出结果和界面上面是一样的。

##  JFR事件流

讲了这么多，终于到我们今天要讲的内容了：JFR事件流。

上面的JFR事件中，我们需要去读取JFR文件，进行分析。但是文件是死的，人是活的，每次分析都需要先生成JFR文件简直是太复杂了。是个程序员都不能容忍。

在JFR事件流中，我们可以监听Event的变化，从而在程序中进行相应的处理。这样不需要生成JFR文件也可以监听事件变化。

```java
    public static void main(String[] args) throws IOException, ParseException {
        //default or profile 两个默认的profiling configuration files
        Configuration config = Configuration.getConfiguration("default");
        try (var es = new RecordingStream(config)) {
            es.onEvent("jdk.GarbageCollection", System.out::println);
            es.onEvent("jdk.CPULoad", System.out::println);
            es.onEvent("jdk.JVMInformation", System.out::println);
            es.setMaxAge(Duration.ofSeconds(10));
            es.start();
        }
    }
```

看看上面的例子。我们通过Configuration.getConfiguration(“default”)获取到了默认的default配置。

然后通过构建了default的RecordingStream。通过onEvent
方法，我们对相应的Event进行处理。

## 总结

本文讲解了JFR，JMC和JDK14的最新特性JFR event stream。希望能够对大家在工作中有所帮助。

# JVM之:JVM的架构和执行过程

## 简介

JVM也叫Java Virtual Machine，它是java程序运行的基础，负责将java bytecode转换成为适合在各个不同操作系统中运行的机器代码并运行。

## JVM是一种标准

其实吧，JVM只是一种标准，就像是一种协议，只要是实现和满足这种协议的都可以称为JVM。当然，java现在是Oracle公司的，所以这些所谓的JVM标准也是由Oracle来颁布的，如果你去查看Oracle的文档，就会发现有一个专门的Java SE Specifications栏目，这个栏目中列出了JVM的实现标准，最新的标准就是The Java Virtual Machine Specification, Java SE 14 Edition。

既然JVM是一个标准，就可能有很多种实现。各大公司在满足JVM标准的基础上，开发了很多个不同的版本。

下面是我在维基百科中截取到的目前各个JVM的比较：

<img src="/img/image/image-20220730213902673.png" alt="image-20220730213902673" style="zoom:80%;" />

有听过Oracle和Google之间的API十年诉讼案吗？API都不能顺便用，更何况是JVM。各大厂商为了各自的利益，最终搞出了这么多个JVM的版本。

在这些JVM中，最常用的就是HotSpot JVM了，毕竟它是Oracle的亲儿子，或者可以说HotSpot JVM就是JVM的标准。

接下来就是Eclipse OpenJ9,这个是由IBM主导的JVM，一般只能跟IBM的产品一起使用的，因为有许可证限制。

## java程序的执行顺序

为了说明JVM的作用，我们先来回顾一下java程序的执行顺序。

<img src="/img/image/image-20220730213952869.png" alt="image-20220730213952869" style="zoom:80%;" />

1. 编写java代码文件比如Example.java
2. 使用java编译器javac将源文件编译成为Example.class文件
3. JVM加载生成的字节码文件，将其转换成为机器可以识别的native machine code执行

## JVM整体架构

其实吧，JVM可以分为三大部分，五大空间和三大引擎，要讲起来也不是特别复杂，先看下面的总体的JVM架构图。

<img src="/img/image/image-20220730214210722.png" alt="image-20220730214210722" style="zoom:80%;" />

从上面的图中，我们可以看到JVM中有三大部分，分别是类加载系统，运行时数据区域和Execution Engine。

## 类加载系统

类加载系统分为三个阶段，分别是加载，链接和初始化。

加载大家都很清楚了，java中有个专门的`ClassLoader`来负责这个事情。除了加载Class之外，ClassLoader还可以用来加载resources。

在JDK9之前，系统默认有三个类加载器，分别是：

1. Bootstrap ClassLoader

这个类加载器主要是加载 /jre/lib下面的rt.jar，并且这个类加载器是用C/C++来编写的，并且它是后面Extension ClassLoader的父ClassLoader。
这个类应该在java代码中找不到的（correct me if I am wrong!）。

2. Extension ClassLoader

这个类加载器主要加载JDK的扩展类 /jre/lib/ext，它的实现类是 sun.misc.Launcher$ExtClassLoader ：

```java
static class ExtClassLoader extends URLClassLoader {
        private static volatile Launcher.ExtClassLoader instance;

        public static Launcher.ExtClassLoader getExtClassLoader() throws IOException {
            if (instance == null) {
                Class var0 = Launcher.ExtClassLoader.class;
                synchronized(Launcher.ExtClassLoader.class) {
                    if (instance == null) {
                        instance = createExtClassLoader();
                    }
                }
            }

            return instance;
        }
```

我们看下它的实现，实际上它创建了一个单例模式，使用的是双重检查加锁，小师妹可以考虑一下怎么使用延迟初始化占位类的方式来重新这个类。

3. System ClassLoader

这个加载器是加载定义在ClassLoader中的类。它的实现类是sun.misc.Launcher$AppClassLoader，这个类的实现很长，这里就不完整列出来了：

```java
static class AppClassLoader extends URLClassLoader 
```

在JDK9之后，因为引入了JPMS模块的概念，所以类加载器变得不一样了，在JDK9之后还是有三个内置的类加载器，分别是BootClassLoader，PlatformClassLoader和AppClassLoader：

```java
    private static class BootClassLoader extends BuiltinClassLoader {
        BootClassLoader(URLClassPath bcp) {
            super(null, null, bcp);
        }

        @Override
        protected Class<?> loadClassOrNull(String cn, boolean resolve) {
            return JLA.findBootstrapClassOrNull(this, cn);
        }
    };
private static class PlatformClassLoader extends BuiltinClassLoader 
private static class AppClassLoader extends BuiltinClassLoader
```

**Linking阶段主要做了三件事情：**

1. Verification – 主要验证字节码文件的结构的正确性，如果不正确则会报LinkageError。
2. Preparation – 负责创建static fields，并且初始化他们的值。
3. Resolution – 把类型的常量池中引用的类，接口，字段和方法替换为直接引用的过程。

**Initialization**阶段主要是调用class的父类和自身的初始化方法，来设置变量的初始值。

## 运行时数据区域

类加载好了，也初始化了，接下来就可以准备运行了。

运行的时候要为数据分配运行空间，这就是运行时数据区域的作用。

运行时数据区域又可以分为5个部分：

1. Method Area

方法区是非Heap(非推)的内存空间，主要用来存放class结构，static fields, method, method’s data 和 static fields等。方法区是在JVM启动的时候创建的，并且在所有的线程中共享。

> Run-Time Constant Pool运行时常量池是放在方法区中的，他是class文件中constant_pool的运行时表现。
>
> 注意在JDK8之前，HotSpot JVM中对方法区的实现叫做持久代Perm Gen。不过在JDK8之后，Perm Gen已经被取消了，现在叫做Metaspace。Metaspace并不在java虚拟机中，它使用的是本地内存。Metaspace可以通过-XX:MaxMetaspaceSize来控制。

2. Heap Area

Heap Area(推)主要存储类对象和数组。垃圾回收器（GC）主要就是用来回收Heap Area中的对象的。

3. Stack Area

因为是栈的结构，所以这个区域总是LIFO(Last in first out)。我们考虑一个方法的执行，当方法执行的时候，就会在Stack Area中创建一个block，这个block中持有对本地对象和其他对象的引用。一旦方法执行完毕，则这个block就会出栈，供其他方法访问。

4. PC Registers

PC Registers主要用来对程序的执行状态进行跟踪，比如保存当前的执行地址，和下一步的地址等。

5. Native Methods

最后一个就是本地方法区了，因为JVM的底层很多都是由C/C++来实现的，这些方法的实现就构成了本地方法区。

## 执行引擎

执行引擎主要负责将java的字节码翻译成机器码然后执行。

先看一个java字节码的内在结构,大家可以随便找一个编译好的类，使用javap来进行解析：

先看一个java字节码的内在结构,大家可以随便找一个编译好的类，使用javap来进行解析：

```java
javap -v BufferUsage
```

<img src="/img/image/image-20220730215349915.png" alt="image-20220730215349915" style="zoom:80%;" />

这里不过多介绍输出结果的含义，我们会在后面的文章中进行详解。

这我们可以看到方法中都有一个Code片段，这些Code被称为OpCode，是JVM可以理解的操作命令。

执行引擎中里面又有三个部分：

1. Interpreter

翻译器用来读取上面介绍的OpCode，并将其翻译成为机器语言。因为翻译器需要一个命令一个命令的翻译字节码，所以速度会比较慢。这就是很久很久以前Java被诟病的地方。

2. JIT (Just-In-Time) compiler

为了解决Interpreter翻译慢的问题，JDK引入了JIT，对于那些经常使用的代码，JIT会将这些字节码翻译成为机器代码，并直接复用这些机器代码，从而提高了执行效率。

3. Garbage Collector

GC用来回收Heap Area，他是一个Daemon thread。

# 总结

本文介绍了JVM的总体架构信息。各个部分的细节信息会在后面的系列文章中陆续讲解。









