JVM是Java Virtual Machine（Java虚拟机）的缩写，JVM是一种用于计算设备的规范，它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。

引入Java语言虚拟机后，Java语言在不同平台上运行时不需要重新编译。Java语言使用Java虚拟机屏蔽了与具体平台相关的信息，使得Java语言编译程序只需生成在Java虚拟机上运行的目标代码（字节码），就可以在多种平台上不加修改地运行。

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

<img src="../../../../../Download/Typora/image/image-20220730211455700.png" alt="image-20220730211455700" style="zoom:80%;" />

我们可以看到在界面的左边已经可以看到运行在本机的ThreadTest程序了。

点击MBean服务器，可以看到该java程序的面板信息，里面包含CPU，堆栈信息。

在下面有7个tab分别是概览，MBean浏览器，触发器，系统，内存，线程，和诊断命令。

JMC非常强大，也有很多功能，具体的细节大家可以自己运行去体会。

因为本文主要是将JFR，下面我们将讲解如何在JMC中创建JFR和分析JFR。

## 创建JFR

上面右侧的MBean服务器下就是飞行记录器了，也就是我们的目标。

点击飞行记录器：

<img src="../../../../../Download/Typora/image/image-20220730211622790.png" alt="image-20220730211622790" style="zoom:80%;" />

我们就可以开始创建一个JFR了。

目标文件就是JFR的生成地址，名称可以自己随便起一个，记录时间表示需要记录多长时间范围之内的JFR。

点下一步：

<img src="../../../../../Download/Typora/image/image-20220730211719920.png" alt="image-20220730211719920" style="zoom:80%;" />

这一步可以选择更加详细的JVM参数。

点下一步：

<img src="../../../../../Download/Typora/image/image-20220730211940380.png" alt="image-20220730211940380" style="zoom:80%;" />

这里，我们可以选择需要监控的Profile事件选项。可以按照你的需要进行选择。

最后点完成创建JFR。

## 分析JFR

上面我们的JFR记录了1分钟的Profile，在1分钟之后，我们可以看到目标JFR文件生成了。

<img src="../../../../../Download/Typora/image/image-20220730212809867.png" alt="image-20220730212809867" style="zoom:80%;" />

生成完JFR之后，JMC会自动打开生成的JFR文件，我们得到一个大纲视图。

里面包含java应用程序，JVM内部，环境和事件浏览器。

事件浏览器中列出了我们在1分钟之内监控的事件。

<img src="../../../../../Download/Typora/image/image-20220730212828563.png" alt="image-20220730212828563" style="zoom:80%;" />

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

<img src="../../../../../Download/Typora/image/image-20220730213902673.png" alt="image-20220730213902673" style="zoom:80%;" />

有听过Oracle和Google之间的API十年诉讼案吗？API都不能顺便用，更何况是JVM。各大厂商为了各自的利益，最终搞出了这么多个JVM的版本。

在这些JVM中，最常用的就是HotSpot JVM了，毕竟它是Oracle的亲儿子，或者可以说HotSpot JVM就是JVM的标准。

接下来就是Eclipse OpenJ9,这个是由IBM主导的JVM，一般只能跟IBM的产品一起使用的，因为有许可证限制。

## java程序的执行顺序

为了说明JVM的作用，我们先来回顾一下java程序的执行顺序。

<img src="../../../../../Download/Typora/image/image-20220730213952869.png" alt="image-20220730213952869" style="zoom:80%;" />

1. 编写java代码文件比如Example.java
2. 使用java编译器javac将源文件编译成为Example.class文件
3. JVM加载生成的字节码文件，将其转换成为机器可以识别的native machine code执行

## JVM整体架构

其实吧，JVM可以分为三大部分，五大空间和三大引擎，要讲起来也不是特别复杂，先看下面的总体的JVM架构图。

<img src="../../../../../Download/Typora/image/image-20220730214210722.png" alt="image-20220730214210722" style="zoom:80%;" />

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

<img src="../../../../../Download/Typora/image/image-20220730215349915.png" alt="image-20220730215349915" style="zoom:80%;" />

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









