3在现代计算机中，为了提升执行效率，引入了多线程的概念，多线程是怎么工作的？在多线程中的编程中需要注意哪些点？

如何在多线程中处理并发的问题？如何写出一个计划中中的多线程程序？本文将会带给大家不一样的感受。

# java.util.concurrent简介

java.util.concurrent包提供了很多有用的类，方便我们进行并发程序的开发。

## 主要的组件

java.util.concurrent包含了很多内容， 本文将会挑选其中常用的一些类来进行大概的说明：

- Executor
- ExecutorService
- ScheduledExecutorService
- Future
- CountDownLatch
- CyclicBarrier
- Semaphore
- ThreadFactory

## Executor

Executor是一个接口，它定义了一个execute方法，这个方法接收一个Runnable，并在其中调用Runnable的run方法。

```java
public class Invoker implements Executor {
    @Override
    public void execute(Runnable r) {
        r.run();
    }
}
```

现在我们可以直接调用该类中的方法：

```java
    public void execute() {
        Executor executor = new Invoker();
        executor.execute( () -> {
            log.info("{}", Thread.currentThread().toString());
        });
    }
```

> *Executor并不一定要求执行的任务是异步的。*

## ExecutorService

如果我们真正的需要使用多线程的话，那么就需要用到ExecutorService了。

ExecutorService管理了一个内存的队列，并定时提交可用的线程。

我们首先定义一个Runnable类：

```java
public class Task implements Runnable {
    @Override
    public void run() {
        // task details
    }
}
```

我们可以通过Executors来方便的创建ExecutorService：

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
```

上面创建了一个ThreadPool(线程池)， 我们也可以创建单线程的ExecutorService：

```java
ExecutorService executor =Executors.newSingleThreadExecutor();
```

我们这样提交task：

```java
public void execute() { 
    executor.submit(new Task()); 
}
```

因为ExecutorService维持了一个队列，所以它不会自动关闭， 我们需要调用executor.shutdown() 或者executor.shutdownNow()来关闭它。

如果想要判断ExecutorService中的线程在收到shutdown请求后是否全部执行完毕，可以调用如下的方法：

```java
try {
            executor.awaitTermination( 5l, TimeUnit.SECONDS );
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
```

## ScheduledExecutorService

ScheduledExecutorService和ExecutorService很类似，但是它可以周期性的执行任务。

我们这样创建ScheduledExecutorService：

```java
ScheduledExecutorService executorService = Executors.newSingleThreadScheduledExecutor();
```

executorService的schedule方法，可以传入`Runnable`也可以传入`Callable`：

```java
Future<String> future = executorService.schedule(() -> {
        // ...
        return "Hello world";
    }, 1, TimeUnit.SECONDS);

    ScheduledFuture<?> scheduledFuture = executorService.schedule(() -> {
        // ...
    }, 1, TimeUnit.SECONDS);
```

还有两个比较相近的方法：

```java
scheduleAtFixedRate( Runnable command, long initialDelay, long period, TimeUnit unit )

scheduleWithFixedDelay( Runnable command, long initialDelay, long delay, TimeUnit unit ) 
```

两者的区别是前者的period是以任务开始时间来计算的，后者是以任务结束时间来计算。

## Future

Future用来获取异步执行的结果。可以调用cancel(boolean mayInterruptIfRunning) 方法来取消线程的执行。

得到一个Future对象：

```java
public void invoke() {
    ExecutorService executorService = Executors.newFixedThreadPool(10);

    Future<String> future = executorService.submit(() -> {
        // ...
        Thread.sleep(10000l);
        return "Hello world";
    });
}
```

获取Future的结果：

```java
if (future.isDone() && !future.isCancelled()) {
    try {
        str = future.get();
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
}
```

future还可以接受一个时间参数，超过指定的时间，将会报TimeoutException。

```java
try {
    future.get(10, TimeUnit.SECONDS);
} catch (InterruptedException | ExecutionException | TimeoutException e) {
    e.printStackTrace();
}
```

## CountDownLatch

CountDownLatch是一个并发中很有用的类，CountDownLatch会初始化一个counter，通过这个counter变量，来控制资源的访问。

## CyclicBarrier

CyclicBarrier和CountDownLatch很类似。CyclicBarrier主要用于多个线程互相等待的情况，可以通过调用await() 方法等待，知道达到要等的数量。

```java
public class Task implements Runnable {

    private CyclicBarrier barrier;

    public Task(CyclicBarrier barrier) {
        this.barrier = barrier;
    }

    @Override
    public void run() {
        try {
            LOG.info(Thread.currentThread().getName() + 
              " is waiting");
            barrier.await();
            LOG.info(Thread.currentThread().getName() + 
              " is released");
        } catch (InterruptedException | BrokenBarrierException e) {
            e.printStackTrace();
        }
    }

}
```

```java
public void start() {

    CyclicBarrier cyclicBarrier = new CyclicBarrier(3, () -> {
        // ...
        LOG.info("All previous tasks are completed");
    });

    Thread t1 = new Thread(new Task(cyclicBarrier), "T1"); 
    Thread t2 = new Thread(new Task(cyclicBarrier), "T2"); 
    Thread t3 = new Thread(new Task(cyclicBarrier), "T3"); 

    if (!cyclicBarrier.isBroken()) { 
        t1.start(); 
        t2.start(); 
        t3.start(); 
    }
}
```

## Semaphore

Semaphore包含了一定数量的许可证，通过获取许可证，从而获得对资源的访问权限。通过 tryAcquire()来获取许可，如果获取成功，许可证的数量将会减少。一旦线程release()许可，许可的数量将会增加。

```java
static Semaphore semaphore = new Semaphore(10);

public void execute() throws InterruptedException {

    LOG.info("Available permit : " + semaphore.availablePermits());
    LOG.info("Number of threads waiting to acquire: " + 
      semaphore.getQueueLength());

    if (semaphore.tryAcquire()) {
        try {
            // ...
        }
        finally {
            semaphore.release();
        }
    }

}
```

## ThreadFactory

ThreadFactory可以很方便的用来创建线程：

```java
public class ThreadFactoryUsage implements ThreadFactory {
    private int threadId;
    private String name;

    public ThreadFactoryUsage(String name) {
        threadId = 1;
        this.name = name;
    }

    @Override
    public Thread newThread(Runnable r) {
        Thread t = new Thread(r, name + "-Thread_" + threadId);
        log.info("created new thread with id : " + threadId +
                " and name : " + t.getName());
        threadId++;
        return t;
    }
}
```

# java并发中的Synchronized关键词

如果在多线程的环境中，我们经常会遇到资源竞争的情况，比如多个线程要去同时修改同一个共享变量，这时候，就需要对资源的访问方法进行一定的处理，保证同一时间只有一个线程访问。

java提供了synchronized关键字，方便我们实现上述操作。

## 为什么要同步

举个例子，我们创建一个类，提供了一个setSum的方法：

```java
public class SynchronizedMethods {
    private int sum = 0;
    public void calculate() {
        setSum(getSum() + 1);
    }
}
```

如果我们在多线程的环境中调用这个calculate方法：

```java
    @Test
    public void givenMultiThread_whenNonSyncMethod() throws InterruptedException {
        ExecutorService service = Executors.newFixedThreadPool(3);
        SynchronizedMethods summation = new SynchronizedMethods();

        IntStream.range(0, 1000)
                .forEach(count -> service.submit(summation::calculate));
        service.shutdown();
        service.awaitTermination(1000, TimeUnit.MILLISECONDS);

        assertEquals(1000, summation.getSum());
    }
```

按照上面的方法，我们预计要返回1000， 但是实际上基本不可能得到1000这个值，因为在多线程环境中，对同一个资源进行同时操作带来的不利影响。

## Synchronized关键词

java提供了多种线程安全的方法，本文主要讲解Synchronized关键词，Synchronized关键词可以有很多种形式：

- Instance methods
- Static methods
- Code blocks

当我们使用synchronized时，java会在相应的对象上加锁，从而在同一个对象等待锁的方法都必须顺序执行，从而保证了线程的安全。

### Synchronized Instance Methods

Synchronized关键词可以放在实例方法的前面：

```java
    public synchronized void synchronisedCalculate() {
        setSum(getSum() + 1);
    }
```

看下调用结果：

```java
@Test
public void givenMultiThread_whenMethodSync() {
    ExecutorService service = Executors.newFixedThreadPool(3);
    SynchronizedMethods method = new SynchronizedMethods();

    IntStream.range(0, 1000)
      .forEach(count -> service.submit(method::synchronisedCalculate));
    service.awaitTermination(1000, TimeUnit.MILLISECONDS);

    assertEquals(1000, method.getSum());
}
```

这里synchronized将会锁住该方法的实例对象，多个线程中只有获得该实例对象锁的线程才能够执行。

### Synchronized Static Methods

Synchronized关键词也可以用在static方法前面：

```java
    public static synchronized void syncStaticCalculate() {
        staticSum = staticSum + 1;
    }
```

Synchronized放在static方法前面和实例方法前面锁住的对象不同。放在static方法前面锁住的对象是这个Class本身，因为一个Class在JVM中只会存在一个，所以不管有多少该Class的实例，在同一时刻只会有一个线程可以执行该放方法。

```java
    @Test
    public void givenMultiThread_whenStaticSyncMethod() throws InterruptedException {
        ExecutorService service = Executors.newCachedThreadPool();

        IntStream.range(0, 1000)
                .forEach(count ->
                        service.submit(SynchronizedMethods::syncStaticCalculate));
        service.shutdown();
        service.awaitTermination(100, TimeUnit.MILLISECONDS);

        assertEquals(1000, SynchronizedMethods.staticSum);
    }
```

### Synchronized Blocks

有时候，我们可能不需要Synchronize整个方法，而是同步其中的一部分，这时候，我们可以使用Synchronized Blocks：

```java
    public void performSynchronizedTask() {
        synchronized (this) {
            setSum(getSum() + 1);
        }
    }
```

```java
    @Test
    public void givenMultiThread_whenBlockSync() throws InterruptedException {
        ExecutorService service = Executors.newFixedThreadPool(3);
        SynchronizedMethods synchronizedBlocks = new SynchronizedMethods();

        IntStream.range(0, 1000)
                .forEach(count ->
                        service.submit(synchronizedBlocks::performSynchronizedTask));
        service.shutdown();
        service.awaitTermination(100, TimeUnit.MILLISECONDS);

        assertEquals(1000, synchronizedBlocks.getSum());
    }
```

上面我们同步的是实例，如果在静态方法中，我们也可以同步class：

```java
    public static void performStaticSyncTask(){
        synchronized (SynchronizedMethods.class) {
            staticSum = staticSum + 1;
        }
    }
```

```java
    @Test
    public void givenMultiThread_whenStaticSyncBlock() throws InterruptedException {
        ExecutorService service = Executors.newCachedThreadPool();

        IntStream.range(0, 1000)
                .forEach(count ->
                        service.submit(SynchronizedMethods::performStaticSyncTask));
        service.shutdown();
        service.awaitTermination(100, TimeUnit.MILLISECONDS);

        assertEquals(1000, SynchronizedMethods.staticSum);
    }
```

# java中的Volatile关键字使用

在本文中，我们会介绍java中的一个关键字volatile。 volatile的中文意思是易挥发的，不稳定的。那么在java中使用是什么意思呢？

我们知道，在java中，每个线程都会有个自己的内存空间，我们称之为working memory。这个空间会缓存一些变量的信息，从而提升程序的性能。当执行完某个操作之后，thread会将更新后的变量更新到主缓存中，以供其他线程读写。

因为变量存在working memory和main memory两个地方，那么就有可能出现不一致的情况。 那么我们就可以使用Volatile关键字来强制将变量直接写到main memory，从而保证了不同线程读写到的是同一个变量。

> JMM(java内存模型,java memory model，简称JMM)，它本身是一种抽象的概念==并不真实存在==，它描述的是一组规则或规范，通过这个规范定义了程序中各个变量（包括实例字段，静态字段和构成数组对象的元素）的访问方式。

JMM关于同步的规定：

1. 线程解锁前，必须把共享变量的值刷新回**主内存**
2. 线程加锁前，必须读取主内存的最新值到自己的**工作内存**
3. 解锁加锁是同一把锁

由于JVM运行程序的实体是**线程**，而每个线程创建时JVM都会为其创建一个**工作内存**（有些地方称为栈空间），工作内存是每个线程的私有数据区域，而**Java内存模型**中规定所有变量都存储在主内存，**主内存是共享内存区域**，所有线程都可以访问，==但线程对变量的操作（读取赋值等）必须在工作内存中进行，首先要将变量从主内存拷贝到自己的工作内存空间，然后对变量进行操作，操作完成后再将变量写回主内存==，不能直接操作主内存中的变量，各个线程中的工作内存中存储着主内存中的变量副本拷贝，因此不同的线程间无法访问对方的工作内存，线程间的通信（传值）必须通过主内存来完成，其简要访问过程：

<img src="../../../../../Download/Typora/image/image-20220725204303427.png" alt="image-20220725204303427" style="zoom:50%;" />









## 什么时候使用volatile

那么我们什么时候使用volatile呢？当一个线程需要立刻读取到另外一个线程修改的变量值的时候，我们就可以使用volatile。

```java
public class VolatileWithoutUsage {
    private  int count = 0;

    public void incrementCount() {
        count++;
    }
    public int getCount() {
        return count;
    }
}
```

这个类定义了一个incrementCount()方法，会去更新count值，我们接下来在多线程环境中去测试这个方法：

```java
    @Test
    public void testWithoutVolatile() throws InterruptedException {
        ExecutorService service= Executors.newFixedThreadPool(3);
        VolatileWithoutUsage volatileWithoutUsage=new VolatileWithoutUsage();

        IntStream.range(0,1000).forEach(count ->service.submit(volatileWithoutUsage::incrementCount) );
        service.shutdown();
        service.awaitTermination(1000, TimeUnit.MILLISECONDS);
        assertEquals(1000,volatileWithoutUsage.getCount() );
    }
```

运行一下，我们会发现结果是不等于1000的。

```txt
java.lang.AssertionError: 
Expected :1000
Actual   :999
```

这是因为多线程去更新同一个变量，我们在上篇文章也提到了，这种情况可以通过加Synchronized关键字来解决。

那么是不是我们加上Volatile关键字后就可以解决这个问题了呢？

```java
public class VolatileFalseUsage {
    private volatile int count = 0;

    public void incrementCount() {
        count++;
    }
    public int getCount() {
        return count;
    }

}
```

上面的类中，我们加上了关键字Volatile，我们再测试一下：

```java
    @Test
    public void testWithVolatileFalseUsage() throws InterruptedException {
        ExecutorService service= Executors.newFixedThreadPool(3);
        VolatileFalseUsage volatileFalseUsage=new VolatileFalseUsage();

        IntStream.range(0,1000).forEach(count ->service.submit(volatileFalseUsage::incrementCount) );
        service.shutdown();
        service.awaitTermination(5000, TimeUnit.MILLISECONDS);
        assertEquals(1000,volatileFalseUsage.getCount() );
    }
```

运行一下，我们会发现结果还是错误的：

```txt
java.lang.AssertionError: 
Expected :1000
Actual   :992
~~

为什么呢？ 我们先来看下count++的操作，count++可以分解为三步操作，1. 读取count的值，2.给count加1， 3.将count写回内存。添加Volatile关键词只能够保证count的变化立马可见，而不能保证1，2，3这三个步骤的总体原子性。 要实现总体的原子性还是需要用到类似Synchronized的关键字。

原子性:不可分割，完整性，也就是说某个线程正在做某个具体业务时，中间不可以被加塞或者被分割，需要具体完成，要么同时成功，要么同时失败。（数据库也经常提到事务具备原子性）

1. 方法synchronized加锁。(引入重量级的同步机制，并不划算，一般使用JUC下面的原子包装类)
2. 使用`java.util.concurrent.AtomicInteger`类。

下面看下正确的用法：

~~~java
public class VolatileTrueUsage {
    private volatile int count = 0;

    public void setCount(int number) {
        count=number;
    }
    public int getCount() {
        return count;
    }
}
```

```java
    @Test
    public void testWithVolatileTrueUsage() throws InterruptedException {
        VolatileTrueUsage volatileTrueUsage=new VolatileTrueUsage();
        Thread threadA = new Thread(()->volatileTrueUsage.setCount(10));
        threadA.start();
        Thread.sleep(100);

        Thread reader = new Thread(() -> {
            int valueReadByThread = volatileTrueUsage.getCount();
            assertEquals(10, valueReadByThread);
        });
        reader.start();
```

<img src="../../../../../Download/Typora/image/image-20220725205036172.png" alt="image-20220725205036172" style="zoom: 80%;" />

## Happens-Before

从java5之后，volatile提供了一个Happens-Before的功能。Happens-Before 是指当volatile进行写回主内存的操作时，会将之前的非volatile的操作一并写回主内存。

```java
public class VolatileHappenBeforeUsage {

    int a = 0;
    volatile boolean flag = false;

    public void writer() {
        a = 1;              // 1 线程A修改共享变量
        flag = true;        // 2 线程A写volatile变量
    }
}
```

上面的例子中，a是一个非volatile变量，flag是一个volatile变量，但是由于happens-before的特性，a 将会表现的和volatile一样。

## Volatile禁止指令重排

计算机在执行程序时，为了提高性能，编译器和处理器常常会对指令重排，一般分为以下三种：

```
源代码 -> 编译器优化的重排 -> 指令并行的重排 -> 内存系统的重排 -> 最终执行指令
```

* 单线程环境里面确保最终执行结果和代码顺序的结果一致

* 处理器在进行重排序时，必须要考虑指令之间的`数据依赖性`

* 多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的，结果无法预测。

> volatile可以保证**有序性**，也就是防止**指令重排序**。所谓指令重排序，就是出于优化考虑，CPU执行指令的顺序跟程序员自己编写的顺序不一致。就好比一份试卷，题号是老师规定的，是程序员规定的，但是考生（CPU）可以先做选择，也可以先做填空。

volatile底层是用CPU的**内存屏障**（Memory Barrier）指令来实现的，有两个作用，一个是保证特定操作的顺序性，二是保证变量的可见性。在指令之间插入一条Memory Barrier指令，告诉编译器和CPU，在Memory Barrier指令之间的指令不能被重排序。

**指令重排 - example 1**

```java
public void mySort() {
	int x = 11;
	int y = 12;
	x = x + 5;
	y = x * x;
}
```

按照正常单线程环境，执行顺序是 1 2 3 4

但是在多线程环境下，可能出现以下的顺序：

- 2 1 3 4
- 1 3 2 4 

上述的过程就可以当做是指令的重排，即内部执行顺序，和我们的代码顺序不一样

但是指令重排也是有限制的，即不会出现下面的顺序

- 4 3 2 1

因为处理器在进行重排时候，必须考虑到指令之间的数据依赖性

因为步骤 4：需要依赖于 y的申明，以及x的申明，故因为存在数据依赖，无法首先执行

**例子**

int a,b,x,y = 0

| 线程1         | 线程2  |
| ------------- | ------ |
| x = a;        | y = b; |
| b = 1;        | a = 2; |
|               |        |
| x = 0;  y = 0 |        |

因为上面的代码，不存在数据的依赖性，因此编译器可能对数据进行重排

| 线程1         | 线程2  |
| ------------- | ------ |
| b = 1;        | a = 2; |
| x = a;        | y = b; |
|               |        |
| x = 2;  y = 1 |        |

这样造成的结果，和最开始的就不一致了，这就是导致重排后，结果和最开始的不一样，因此为了防止这种结果出现，volatile就规定禁止指令重排，为了保证数据的一致性

**指令重排 - example 2**

比如下面这段代码

```java
public class ResortSeqDemo {
    int a= 0;
    boolean flag = false;

    public void method01() {
        a = 1;
        flag = true;
    }

    public void method02() {
        if(flag) {
            a = a + 5;
            System.out.println("reValue:" + a);
        }
    }
}
```

我们按照正常的顺序，分别调用method01()  和 method02() 那么，最终输出就是 a = 6

但是如果在多线程环境下，因为方法1 和 方法2，他们之间不能存在数据依赖的问题，因此原先的顺序可能是

```java
a = 1;
flag = true;

a = a + 5;
System.out.println("reValue:" + a);
```

但是在经过编译器，指令，或者内存的重排后，可能会出现这样的情况

```java
flag = true;

a = a + 5;
System.out.println("reValue:" + a);

a = 1;
```

也就是先执行 flag = true后，另外一个线程马上调用方法2，满足 flag的判断，最终让a + 5，结果为5，这样同样出现了数据不一致的问题

为什么会出现这个结果：多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的，结果无法预测。

这样就需要通过volatile来修饰，来保证线程安全性

> Volatile实现禁止指令重排优化，从而避免了多线程环境下程序出现乱序执行的现象
>
> 首先了解一个概念，内存屏障（Memory Barrier）又称内存栅栏，是一个CPU指令，它的作用有两个：
>
> - 保证特定操作的顺序
> - 保证某些变量的内存可见性（利用该特性实现volatile的内存可见性）
>
> 由于编译器和处理器都能执行指令重排的优化，如果在指令间插入一条Memory Barrier则会告诉编译器和CPU，不管什么指令都不能和这条Memory Barrier指令重排序，也就是说 `通过插入内存屏障禁止在内存屏障前后的指令执行重排序优化`。 内存屏障另外一个作用是刷新出各种CPU的缓存数，因此任何CPU上的线程都能读取到这些数据的最新版本。
>
> ![image-20211025170021073](../../../../../Download/Typora/image/image-20211025170021073.png)
>
> 也就是过在Volatile的写 和 读的时候，加入屏障，防止出现指令重排的

**线程安全获得保证**

工作内存与主内存同步延迟现象导致的可见性问题

- 可通过synchronized或volatile关键字解决，他们都可以使一个线程修改后的变量立即对其它线程可见

对于指令重排导致的可见性问题和有序性问题

- 可以使用volatile关键字解决，因为volatile关键字的另一个作用就是禁止重排序优化

### 多线程下确保单例模式

首先回顾一下，单线程下的单例模式代码

```java
public class SingletonDemo {

    private static SingletonDemo instance = null;

    private SingletonDemo () {
        System.out.println(Thread.currentThread().getName() + "\t 我是构造方法SingletonDemo");
    }

    public static SingletonDemo getInstance() {
        if(instance == null) {
            instance = new SingletonDemo();
        }
        return instance;
    }

    public static void main(String[] args) {
        // 这里的 == 是比较内存地址
        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
    }
}
```

最后输出的结果

<img src="../../../../../Download/Typora/image/image-20211026110249438.png" alt="image-20211026110249438" style="zoom:50%;" />

但是在多线程的环境下，我们的单例模式是否还是同一个对象了

```java
public class SingletonDemo {

    private static SingletonDemo instance = null;

    private SingletonDemo () {
        System.out.println(Thread.currentThread().getName() + "\t 我是构造方法SingletonDemo");
    }

    public static SingletonDemo getInstance() {
        if(instance == null) {
            instance = new SingletonDemo();
        }
        return instance;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                SingletonDemo.getInstance();
            }, String.valueOf(i)).start();
        }
    }
}
```

从下面的结果我们可以看出，我们通过SingletonDemo.getInstance() 获取到的对象，并不是同一个，而是被下面几个线程都进行了创建，那么在多线程环境下，单例模式如何保证呢？

<img src="../../../../../Download/Typora/image/image-20211026110325150.png" alt="image-20211026110325150" style="zoom:67%;" />

### 解决方法1

引入synchronized关键字

```java
    public synchronized static SingletonDemo getInstance() {
        if(instance == null) {
            instance = new SingletonDemo();
        }
        return instance;
    }
```

输出结果

<img src="../../../../../Download/Typora/image/image-20211026110355611.png" alt="image-20211026110355611" style="zoom:67%;" />

我们能够发现，通过引入Synchronized关键字，能够解决高并发环境下的单例模式问题

但是synchronized属于重量级的同步机制，它只允许一个线程同时访问获取实例的方法，但是==为了保证数据一致性，而减低了并发性==，因此采用的比较少

### 解决方法2

通过引入DCL   Double Check Lock   `双端检锁机制`

就是在进来和出去的时候，进行检测

```java
    public static SingletonDemo getInstance() {
        if(instance == null) {
            // 同步代码段的时候，进行检测
            synchronized (SingletonDemo.class) {
                if(instance == null) {
                    instance = new SingletonDemo();
                }
            }
        }
        return instance;
    }
```

最后输出的结果为：

![image-20211026110437647](../../../../../Download/Typora/image/image-20211026110437647.png)

DCL（双端检锁）机制不一定是线程安全的，原因是有指令重排的存在，这个漏洞比较tricky，很难捕捉，但是是存在的。`instance=new SingletonDemo();`可以大致分为三步

```java
memory = allocate();     //1.分配内存
instance(memory);	 //2.初始化对象
instance = memory;	 //3.设置引用地址
```

其中2、3没有数据依赖关系，**可能发生重排**。如果发生，此时内存已经分配，那么`instance=memory`不为null。如果此时线程挂起，`instance(memory)`还未执行，对象还未初始化。由于`instance!=null`，所以两次判断都跳过，最后返回的`instance`没有任何内容，还没初始化，这就造成了线程安全的问题

解决的方法就是对`singletondemo`对象添加上`volatile`关键字，禁止指令重排。

`指令重排只会保证串行语义的执行一致性（单线程），但并不会关系多线程间的语义一致性`

引入volatile，来保证出现指令重排的问题，从而保证单例模式的线程安全性

```java
private static volatile SingletonDemo instance = null;
```

# java中wait和sleep的区别

* 两者最主要的区别在于：**sleep** ⽅法没有释放锁，⽽ **wait** ⽅法释放了锁 。
* 两者都可以暂停线程的执⾏。
* Wait 通常被⽤于线程间交互/通信，sleep 通常被⽤于暂停执⾏。
* wait() ⽅法被调⽤后，线程不会⾃动苏醒，需要别的线程调⽤同⼀个对象上的 notify() 或者notifyAll() ⽅法。sleep() ⽅法执⾏完成后，线程会⾃动苏醒。或者可以使⽤ wait(longtimeout)超时后线程会⾃动苏醒。

## Wait和sleep的区别

`wait()` 是Object中定义的native方法：

```java
public final native void wait(long timeout) throws InterruptedException;
```

所以每一个类的实例都可以调用这个方法。wait()只能在synchronized block中调用。它会释放synchronized时加在object上的锁。

sleep()是定义Thread中的native静态类方法：

```java
public static native void sleep(long millis) throws InterruptedException;
```

所以Thread.sleep()可以在任何情况下调用。Thread.sleep()将会暂停当前线程，并且不会释放任何锁资源。

```java
@Slf4j
public class WaitUsage {

    private static Object LOCK = new Object();

    public static void WaitExample() throws InterruptedException {
        synchronized (LOCK) {
            LOCK.wait(1000);
            log.info("Object '" + LOCK + "' is woken after" +
                    " waiting for 1 second");
        }
    }
}
```

```java
@Slf4j
public class SleepUsage {

    public static void sleepExample() throws InterruptedException {
        Thread.sleep(1000);
        log.info(
                "Thread '" + Thread.currentThread().getName() +
                        "' is woken after sleeping for 1 second");
    }
}
```

## 唤醒wait和sleep

sleep()方法自带sleep时间，时间过后，Thread会自动被唤醒。
或者可以通过调用interrupt()方法来中断。

相比而言wait的唤醒会比较复杂，我们需要调用notify() 和 notifyAll()方法来唤醒等待在特定wait object上的线程。

notify()会根据线程调度的机制选择一个线程来唤醒，而notifyAll()会唤醒所有等待的线程，由这些线程重新争夺资源锁。

wait,notity通常用在生产者和消费者情形，我们看下怎么使用：

```java
@Slf4j
public class WaitNotifyUsage {

    private int count =0;

    public void produceMessage() throws InterruptedException {

        while(true) {
            synchronized (this) {
                while (count == 5) {
                    log.info("count == 5 , wait ....");
                    wait();
                }
                count++;
                log.info("produce count {}", count);
                notify();
            }
        }
    }

    public void consumeMessage() throws InterruptedException {

        while (true) {
            synchronized (this) {
                while (count == 0) {
                    log.info("count == 0, wait ...");
                    wait();
                }
                log.info("consume count {}", count);
                count--;
                notify();
            }
        }
    }
}
```

```java
   @Test
    public void testWaitNotifyUsage() throws InterruptedException{
        WaitNotifyUsage waitNotifyUsage=new WaitNotifyUsage();

        ExecutorService executorService=Executors.newFixedThreadPool(4);
        executorService.submit(()-> {
            try {
                waitNotifyUsage.produceMessage();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        executorService.submit(()-> {
            try {
                waitNotifyUsage.consumeMessage();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });

        Thread.sleep(50000);
    }
```

# java中Future的使用

Future是java 1.5引入的一个interface，可以方便的用于异步结果的获取。 本文将会通过具体的例子讲解如何使用Future。

## 创建Future

正如上面所说，Future代表的是异步执行的结果，意思是当异步执行结束之后，返回的结果将会保存在Future中。

那么我们什么时候会用到Future呢？ 一般来说，当我们执行一个长时间运行的任务时，使用Future就可以让我们暂时去处理其他的任务，等长任务执行完毕再返回其结果。

经常会使用到Future的场景有：1. 计算密集场景。2. 处理大数据量。3. 远程方法调用等。

接下来我们将会使用ExecutorService来创建一个Future。

```java
<T> Future<T> submit(Callable<T> task);
```

上面是ExecutorService中定义的一个submit方法，它接收一个Callable参数，并返回一个Future。

我们用一个线程来计算一个平方运算：

```java
    private ExecutorService executor
            = Executors.newSingleThreadExecutor();

    public Future<Integer> calculate(Integer input) {
        return executor.submit(() -> {
            System.out.println("Calculating..."+ input);
            Thread.sleep(1000);
            return input * input;
        });
    }
```

submit需要接受一个Callable参数，Callable需要实现一个call方法(run是没有返回值的，call有返回值所以使用Callable)，并返回结果。这里我们使用lamaba表达式来简化这一个流程。

## 从Future获取结果

上面我们创建好了Future，接下来我们看一下怎么获取到Future的值。

```java
       FutureUsage futureUsage=new FutureUsage();
        Future<Integer> futureOne = futureUsage.calculate(20);
        while(!futureOne.isDone()) {
            System.out.println("Calculating...");
            Thread.sleep(300);
        }
        Integer result = futureOne.get();
```

首先我们通过Future.isDone() 来判断这个异步操作是否执行完毕，如果完毕我们就可以直接调用futureOne.get()来获得Futre的结果。

这里futureOne.get()是一个阻塞操作，会一直等待异步执行完毕才返回结果。

如果我们不想等待，future提供了一个带时间的方法：

```java
Integer result = futureOne.get(500, TimeUnit.MILLISECONDS);
```

如果在等待时间结束的时候，Future还有返回，则会抛出一个TimeoutException。

## 取消Future

如果我们提交了一个异步程序，但是想取消它， 则可以这样：

```java
Future<Integer> futureTwo = futureUsage.calculate(4);

        boolean canceled = futureTwo.cancel(true);
```

Future.cancel(boolean) 传入一个boolean参数，来选择是否中断正在运行的task。

如果我们cancel之后，再次调用get()方法，则会抛出CancellationException。

## 多线程环境中运行

如果有两个计算任务，先看下在单线程下运行的结果。

```java
        Future<Integer> future1 = futureUsage.calculate(10);
        Future<Integer> future2 = futureUsage.calculate(100);

        while (!(future1.isDone() && future2.isDone())) {
            System.out.println(
                    String.format(
                            "future1 is %s and future2 is %s",
                            future1.isDone() ? "done" : "not done",
                            future2.isDone() ? "done" : "not done"
                    )
            );
            Thread.sleep(300);
        }

        Integer result1 = future1.get();
        Integer result2 = future2.get();

        System.out.println(result1 + " and " + result2);
```

因为我们通过Executors.newSingleThreadExecutor（）来创建的单线程池。所以运行结果如下：

```txt
Calculating...10
future1 is not done and future2 is not done
future1 is not done and future2 is not done
future1 is not done and future2 is not done
future1 is not done and future2 is not done
Calculating...100
future1 is done and future2 is not done
future1 is done and future2 is not done
future1 is done and future2 is not done
100 and 10000
```

如果我们使用Executors.newFixedThreadPool(2)来创建一个多线程池，则可以得到如下的结果：

```txt
calculating...10
calculating...100
future1 is not done and future2 is not done
future1 is not done and future2 is not done
future1 is not done and future2 is not done
future1 is not done and future2 is not done
100 and 10000
```

# java并发中ExecutorService的使用

ExecutorService是java中的一个异步执行的框架，通过使用ExecutorService可以方便的创建多线程执行环境。之前简单记录了下excutorServive的介绍，本文将会详细的讲解ExecutorService的具体使用。

## 创建ExecutorService

通常来说有两种方法来创建ExecutorService。

第一种方式是使用Executors中的工厂类方法，例如：

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
```

除了newFixedThreadPool方法之外，Executors还包含了很多创建ExecutorService的方法。

第二种方法是直接创建一个ExecutorService， 因为ExecutorService是一个interface，我们需要实例化ExecutorService的一个实现。

这里我们使用ThreadPoolExecutor来举例：

```java
ExecutorService executorService =
            new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS,
                    new LinkedBlockingQueue<Runnable>());
```

## 为ExecutorService分配Tasks

ExecutorService可以执行Runnable和Callable的task。其中Runnable是没有返回值的，而Callable是有返回值的。我们分别看一下两种情况的使用：

```java
Runnable runnableTask = () -> {
    try {
        TimeUnit.MILLISECONDS.sleep(300);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
};

Callable<String> callableTask = () -> {
    TimeUnit.MILLISECONDS.sleep(300);
    return "Task's execution";
};
```

将task分配给ExecutorService，可以通过调用execute(), submit(), invokeAny(), invokeAll()这几个方法来实现。

execute() 返回值是void，他用来提交一个Runnable task。

```java
executorService.execute(runnableTask);
```

submit() 返回值是Future，它可以提交Runnable task, 也可以提交Callable task。 提交Runnable的有两个方法：

```java
<T> Future<T> submit(Runnable task, T result);

Future<?> submit(Runnable task);
```

第一个方法在返回传入的result。第二个方法返回null。

再看一下callable的使用：

```java
Future<String> future = 
  executorService.submit(callableTask);
```

invokeAny() 将一个task列表传递给executorService，并返回其中的一个成功返回的结果。

```java
String result = executorService.invokeAny(callableTasks);
```

invokeAll() 将一个task列表传递给executorService，并返回所有成功执行的结果：

```java
List<Future<String>> futures = executorService.invokeAll(callableTasks);
```

## 关闭ExecutorService

如果ExecutorService中的任务运行完毕之后，ExecutorService不会自动关闭。它会等待接收新的任务。如果需要关闭ExecutorService， 我们需要调用shutdown() 或者 shutdownNow() 方法。

shutdown() 会立即销毁ExecutorService，它会让ExecutorServic停止接收新的任务，并等待现有任务全部执行完毕再销毁。

```java
executorService.shutdown();
```

shutdownNow()并不保证所有的任务都被执行完毕，它会返回一个未执行任务的列表：

```java
List<Runnable> notExecutedTasks = executorService.shutdownNow();
```

oracle推荐的最佳关闭方法是和awaitTermination一起使用：

```java
executorService.shutdown();
try {
if (!executorService.awaitTermination(800, TimeUnit.MILLISECONDS)) {
executorService.shutdownNow();
}
} catch (InterruptedException e) {
executorService.shutdownNow();
}
```

先停止接收任务，然后再等待一定的时间让所有的任务都执行完毕，如果超过了给定的时间，则立刻结束任务。

## ExecutorService 中Future

submit() 和 invokeAll() 都会返回Future对象。之前的文章我们已经详细讲过了Future。 这里就只列举一下怎么使用：

~~~java
Future<String> future = executorService.submit(callableTask);
String result = null;
try {
result = future.get();
} catch (InterruptedException | ExecutionException e) {
e.printStackTrace();
}
~~~

## ScheduledExecutorService

ScheduledExecutorService为我们提供了定时执行任务的机制。

我们这样创建ScheduledExecutorService：

```java
ScheduledExecutorService executorService
                = Executors.newSingleThreadScheduledExecutor();
```

executorService的schedule方法，可以传入Runnable也可以传入Callable：

```java
Future<String> future = executorService.schedule(() -> {
        // ...
        return "Hello world";
    }, 1, TimeUnit.SECONDS);

    ScheduledFuture<?> scheduledFuture = executorService.schedule(() -> {
        // ...
    }, 1, TimeUnit.SECONDS);
```

还有两个比较相近的方法：

```java
scheduleAtFixedRate( Runnable command, long initialDelay, long period, TimeUnit unit )

scheduleWithFixedDelay( Runnable command, long initialDelay, long delay, TimeUnit unit ) 
```

两者的区别是前者的period是以任务开始时间来计算的，后者是以任务结束时间来计算。

## ExecutorService和 Fork/Join

java 7 引入了Fork/Join框架。 那么两者的区别是什么呢？

ExecutorService可以由用户来自己控制生成的线程，提供了对线程更加细粒度的控制。而Fork/Join则是为了让任务更加快速的执行完毕。

# java中Runnable和Callable的区别

在java的多线程开发中Runnable一直以来都是多线程的核心，而Callable是java1.5添加进来的一个增强版本。

## 运行机制

首先看下Runnable和Callable的接口定义：

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
@FunctionalInterface
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```

Runnable需要实现run（）方法，Callable需要实现call（）方法。

我们都知道要自定义一个Thread有两种方法，一是继承Thread，而是实现Runnable接口，这是因为Thread本身就是一个Runnable的实现：

```java
class Thread implements Runnable {
    /* Make sure registerNatives is the first thing <clinit> does. */
    private static native void registerNatives();
    static {
        registerNatives();
    }
    ...
```

所以Runnable可以通过Runnable和之前我们介绍的ExecutorService 来执行，而Callable则只能通过ExecutorService 来执行。

## 返回值的不同

根据上面两个接口的定义，Runnable是不返还值的，而Callable可以返回值。

如果我们都通过ExecutorService来提交，看看有什么不同：

- 使用runnable

```java
    public void executeTask() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        Future future = executorService.submit(()->log.info("in runnable!!!!"));
        executorService.shutdown();
    }
```

- 使用callable

```java
    public void executeTask() {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        Future future = executorService.submit(()->{
            log.info("in callable!!!!");
            return "callable";
        });
        executorService.shutdown();
    }
```

虽然我们都返回了Future，但是runnable的情况下Future将不包含任何值。

## Exception处理

Runnable的run（）方法定义没有抛出任何异常，所以任何的Checked Exception都需要在run（）实现方法中自行处理。

Callable的Call（）方法抛出了throws Exception，所以可以在call（）方法的外部，捕捉到Checked Exception。我们看下Callable中异常的处理。

```java
 public void executeTaskWithException(){
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        Future future = executorService.submit(()->{
            log.info("in callable!!!!");
            throw new CustomerException("a customer Exception");
        });
        try {
            Object object= future.get();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
            e.getCause();
        }
        executorService.shutdown();
    }
```

上面的例子中，我们在Callable中抛出了一个自定义的CustomerException。

这个异常会被包含在返回的Future中。当我们调用future.get()方法时，就会抛出ExecutionException，通过e.getCause()，就可以获取到包含在里面的具体异常信息。

# java中ThreadLocal的使用

ThreadLocal主要用来为当前线程存储数据，这个数据只有当前线程可以访问。

在定义ThreadLocal的时候，我们可以同时定义存储在ThreadLocal中的特定类型的对象。

```java
ThreadLocal<Integer> threadLocalValue = new ThreadLocal<>();
```

上面我们定义了一个存储Integer的ThreadLocal对象。

要存储和获取ThreadLocal中的对象也非常简单，使用get（）和set（）即可：

```java
threadLocalValue.set(1);
Integer result = threadLocalValue.get();
```

我可以将ThreadLocal看成是一个map，而当前的线程就是map中的key。

除了new一个ThreadLocal对象，我们还可以通过：

```java
    public static <S> ThreadLocal<S> withInitial(Supplier<? extends S> supplier) {
        return new SuppliedThreadLocal<>(supplier);
    }
```

ThreadLocal提供的静态方法withInitial来初始化一个ThreadLocal。

```java
ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 1);
```

withInitial需要一个Supplier对象，通过调用Supplier的get()方法获取到初始值。

要想删除ThreadLocal中的存储数据，可以调用：

```java
threadLocal.remove();
```

下面我通过两个例子的对比，来看一下使用ThreadLocal的好处。

在实际的应用中，我们通常会需要为不同的用户请求存储不同的用户信息，一般来说我们需要构建一个全局的Map，来根据不同的用户ID，来存储不同的用户信息，方便在后面获取。

## 在Map中存储用户数据

我们先看下如果使用全局的Map该怎么用：

```java
public class SharedMapWithUserContext implements Runnable {

    public static Map<Integer, Context> userContextPerUserId
            = new ConcurrentHashMap<>();
    private Integer userId;
    private UserRepository userRepository = new UserRepository();

    public SharedMapWithUserContext(int i) {
        this.userId=i;
    }

    @Override
    public void run() {
        String userName = userRepository.getUserNameForUserId(userId);
        userContextPerUserId.put(userId, new Context(userName));
    }
}
```

这里我们定义了一个static的Map来存取用户信息。

再看一下怎么使用：

```java
    @Test
    public void testWithMap(){
        SharedMapWithUserContext firstUser = new SharedMapWithUserContext(1);
        SharedMapWithUserContext secondUser = new SharedMapWithUserContext(2);
        new Thread(firstUser).start();
        new Thread(secondUser).start();
        assertEquals(SharedMapWithUserContext.userContextPerUserId.size(), 2);
    }
```

## 在ThreadLocal中存储用户数据

如果我们要在ThreadLocal中使用可以这样：

```java
public class ThreadLocalWithUserContext implements Runnable {

    private static ThreadLocal<Context> userContext
            = new ThreadLocal<>();
    private Integer userId;
    private UserRepository userRepository = new UserRepository();

    public ThreadLocalWithUserContext(int i) {
        this.userId=i;
    }

    @Override
    public void run() {
        String userName = userRepository.getUserNameForUserId(userId);
        userContext.set(new Context(userName));
        System.out.println("thread context for given userId: "
                + userId + " is: " + userContext.get());
    }

}
```

测试代码如下：

```java
public class ThreadLocalWithUserContextTest {

    @Test
    public void testWithThreadLocal(){
        ThreadLocalWithUserContext firstUser
                = new ThreadLocalWithUserContext(1);
        ThreadLocalWithUserContext secondUser
                = new ThreadLocalWithUserContext(2);
        new Thread(firstUser).start();
        new Thread(secondUser).start();
    }
}
```

运行之后，我们可以得到下面的结果：

运行之后，我们可以得到下面的结果：

```java
thread context for given userId: 1 is: com.flydean.Context@411734d4
thread context for given userId: 2 is: com.flydean.Context@1e9b6cc
```

不同的用户信息被存储在不同的线程环境中。

*注意，我们使用ThreadLocal的时候，一定是我们可以自由的控制所创建的线程。如果在ExecutorService环境下，就最好不要使用ThreadLocal，因为在ExecutorService中，线程是不可控的。*

# java中线程的生命周期

线程是java中绕不过去的一个话题， 今天本文将会详细讲解java中线程的生命周期，希望可以给大家一些启发。

## java中Thread的状态

java中Thread有6种状态，分别是：

1. NEW – 新创建的Thread，还没有开始执行
2. RUNNABLE – 可运行状态的Thread，包括准备运行和正在运行的。
3. BLOCKED – 正在等待资源锁的线程
4. WAITING – 正在无限期等待其他线程来执行某个特定操作
5. TIMED_WAITING – 在一定的时间内等待其他线程来执行某个特定操作
6. TERMINATED – 线程执行完毕

我们可以用一个图来直观的表示：

<img src="../../../../../Download/Typora/image/image-20220727184458913.png" alt="image-20220727184458913" style="zoom:80%;" />

JDK代码中的定义如下：

```java
public enum State {
        /**
         * Thread state for a thread which has not yet started.
         */
        NEW,

        /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,

        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,

        /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */
        WAITING,

        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,

        /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */
        TERMINATED;
    }
```

## NEW

NEW 表示线程创建了，但是还没有开始执行。我们看一个NEW的例子：

```java
public class NewThread implements Runnable{
    public static void main(String[] args) {
        Runnable runnable = new NewThread();
        Thread t = new Thread(runnable);
        log.info(t.getState().toString());
    }

    @Override
    public void run() {

    }
}
```

上面的代码将会输出：

```txt
NEW
```

## Runnable

Runnable表示线程正在可执行状态。包括正在运行和准备运行两种。

为什么这两种都叫做Runnable呢？我们知道在多任务环境中，CPU的个数是有限的，所以任务都是轮循占有CPU来处理的，JVM中的线程调度器会为每个线程分配特定的执行时间，当执行时间结束后，线程调度器将会释放CPU，以供其他的Runnable线程执行。

我们看一个Runnable的例子：

```java
public class RunnableThread implements Runnable {
    @Override
    public void run() {

    }

    public static void main(String[] args) {
        Runnable runnable = new RunnableThread();
        Thread t = new Thread(runnable);
        t.start();
        log.info(t.getState().toString());
    }
}
```

上面的代码将会输出：

```txt
RUNNABLE
```

## BLOCKED

BLOCKED表示线程正在等待资源锁，而目前该资源正在被其他线程占有。

我们举个例子：

```java
public class BlockThread implements Runnable {
    @Override
    public void run() {
        loopResource();
    }

    public static synchronized void loopResource() {
        while(true) {
            //无限循环
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new BlockThread());
        Thread t2 = new Thread(new BlockThread());

        t1.start();
        t2.start();

        Thread.sleep(1000);
        log.info(t1.getState().toString());
        log.info(t2.getState().toString());
        System.exit(0);
    }
}
```

上面的例子中，由于t1是无限循环，将会一直占有资源锁，导致t2无法获取资源锁，

上面的例子中，由于t1是无限循环，将会一直占有资源锁，导致t2无法获取资源锁，从而位于BLOCKED状态。

我们会得到如下结果：

```txt
12:40:11.710 [main] INFO com.flydean.BlockThread - RUNNABLE
12:40:11.713 [main] INFO com.flydean.BlockThread - BLOCKED
```

## WAITING

WAITING 状态表示线程正在等待其他的线程执行特定的操作。有三种方法可以导致线程处于WAITTING状态：

1. object.wait()
2. thread.join()
3. LockSupport.park()

其中1，2方法不需要传入时间参数。

我们看下使用的例子：

```java
public class WaitThread implements  Runnable{

    public static Thread t1;
    @Override
    public void run() {
        Thread t2 = new Thread(()->{
            try {
                Thread.sleep(10000);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
                log.error("Thread interrupted", e);
            }
            log.info("t1"+t1.getState().toString());
        });
        t2.start();

        try {
            t2.join();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            log.error("Thread interrupted", e);
        }
        log.info("t2"+t2.getState().toString());
    }

    public static void main(String[] args) {
        t1 = new Thread(new WaitThread());
        t1.start();

    }
}
```

在这个例子中，我们调用的t2.join()，这会使调用它的t1线程处于WAITTING状态。

我们看下输出结果：

```java
12:44:12.958 [Thread-1] INFO com.flydean.WaitThread - t1 WAITING
12:44:12.964 [Thread-0] INFO com.flydean.WaitThread - t2 TERMINATED
```

## TIMED_WAITING

TIMED_WAITING状态表示在一个有限的时间内等待其他线程执行特定的某些操作。

java中有5中方式来达到这种状态：

1. thread.sleep(long millis)
2. wait(int timeout) 或者 wait(int timeout, int nanos)
3. thread.join(long millis)
4. LockSupport.parkNanos
5. LockSupport.parkUntil

我们举个例子：

```java
public class TimedWaitThread implements  Runnable{
    @Override
    public void run() {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            log.error("Thread interrupted", e);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        TimedWaitThread obj1 = new TimedWaitThread();
        Thread t1 = new Thread(obj1);
        t1.start();

        // The following sleep will give enough time for ThreadScheduler
        // to start processing of thread t1
        Thread.sleep(1000);
        log.info(t1.getState().toString());
    }
}
```

上面的例子中我们调用了Thread.sleep(5000)来让线程处于TIMED_WAITING状态。

看下输出：

```txt
12:58:02.706 [main] INFO com.flydean.TimedWaitThread - TIMED_WAITING
```

那么问题来了，TIMED_WAITING和WAITTING有什么区别呢？

TIMED_WAITING如果在给定的时间内没有等到其他线程的特定操作，则会被唤醒，从而进入争夺资源锁的队列，如果能够获取到锁，则会变成Runnable状态，如果获取不到锁，则会变成BLOCKED状态。

## TERMINATED

TERMINATED表示线程已经执行完毕。我们看下例子：

```java
public class TerminatedThread implements Runnable{
    @Override
    public void run() {

    }

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new TerminatedThread());
        t1.start();
        // The following sleep method will give enough time for
        // thread t1 to complete
        Thread.sleep(1000);
        log.info(t1.getState().toString());
    }
}
```

输出结果：

```txt
13:02:38.868 [main] INFO com.flydean.TerminatedThread - TERMINATED
```

# java中join的使用

join()应该是我们在java中经常会用到的一个方法，它主要是将当前线程置为WAITTING状态，然后等待调用的线程执行完毕或被interrupted。

join()是Thread中定义的方法，我们看下他的定义：

```java
   /**
     * Waits for this thread to die.
     *
     * <p> An invocation of this method behaves in exactly the same
     * way as the invocation
     *
     * <blockquote>
     * {@linkplain #join(long) join}{@code (0)}
     * </blockquote>
     *
     * @throws  InterruptedException
     *          if any thread has interrupted the current thread. The
     *          <i>interrupted status</i> of the current thread is
     *          cleared when this exception is thrown.
     */
    public final void join() throws InterruptedException {
        join(0);
    }
```

我们看下join是怎么使用的，通常我们需要在线程A中调用线程B.join():

```java
public class JoinThread implements Runnable{
    public int processingCount = 0;

    JoinThread(int processingCount) {
        this.processingCount = processingCount;
        log.info("Thread Created");
    }

    @Override
    public void run() {
        log.info("Thread " + Thread.currentThread().getName() + " started");
        while (processingCount > 0) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                log.info("Thread " + Thread.currentThread().getName() + " interrupted");
            }
            processingCount--;
        }
        log.info("Thread " + Thread.currentThread().getName() + " exiting");
    }

    @Test
    public void joinTest()
            throws InterruptedException {
        Thread t2 = new Thread(new JoinThread(1));
            t2.start();
        log.info("Invoking join");
        t2.join();
        log.info("Returned from join");
        log.info("t2 status {}",t2.isAlive());
    }
}
```

我们在主线程中调用了t2.join(),则主线程将会等待t2执行完毕，我们看下输出结果：

```txt
06:17:14.775 [main] INFO com.flydean.JoinThread - Thread Created
06:17:14.779 [main] INFO com.flydean.JoinThread - Invoking join
06:17:14.779 [Thread-0] INFO com.flydean.JoinThread - Thread Thread-0 started
06:17:15.783 [Thread-0] INFO com.flydean.JoinThread - Thread Thread-0 exiting
06:17:15.783 [main] INFO com.flydean.JoinThread - Returned from join
06:17:15.783 [main] INFO com.flydean.JoinThread - t2 status false
```

当线程已经执行完毕或者还没开始执行的时候，join（）将会立即返回：

```java
Thread t1 = new SampleThread(0);
t1.join();  //returns immediately
```

join还有两个带时间参数的方法：

```java
public final void join(long millis) throws InterruptedException
public final void join(long millis,int nanos) throws InterruptedException
```

如果在给定的时间内调用的线程没有返回，则主线程将会继续执行：

```java
    @Test
    public void testJoinTimeout()
            throws InterruptedException {
        Thread t3 =  new Thread(new JoinThread(10));
        t3.start();
        t3.join(1000);
        log.info("t3 status {}", t3.isAlive());
    }
```

上面的例子将会输出：

```txt
06:30:58.159 [main] INFO com.flydean.JoinThread - Thread Created
06:30:58.163 [Thread-0] INFO com.flydean.JoinThread - Thread Thread-0 started
06:30:59.172 [main] INFO com.flydean.JoinThread - t3 status true
```

Join()还有个happen-before的特性，这就是如果thread t1调用 t2.join(), 那么当t2返回时，所有t2的变动都会t1可见。

之前我们讲volatile关键词的时候也提到了这个happen-before规则。 我们看下例子：

```java
    @Test
    public void testHappenBefore() throws InterruptedException {
        JoinThread t4 =  new JoinThread(10);
        t4.start();
        // not guaranteed to stop even if t4 finishes.
        do {
            log.info("inside the loop");
            Thread.sleep(1000);
        } while ( t4.processingCount > 0);
    }
```

我们运行下，可以看到while循环一直在进行中，即使t4中的变量已经变成了0。

所以如果我们需要在这种情况下使用的话，我们需要用到join（），或者其他的同步机制。

# 怎么在java中关闭一个thread

我们经常需要在java中用到thread，我们知道thread有一个start()方法可以开启一个线程。那么怎么关闭这个线程呢？

有人会说可以用Thread.stop（）方法。但是这个方法已经被废弃了。

根据Oracle的官方文档，Thread.stop是不安全的。因为调用stop方法的时候，将会释放它获取的所有监视器锁（通过传递ThreadDeath异常实现）。如果有资源该监视器锁所保护的话，就可能会出现数据不一致的异常。并且这种异常很难被发现。 所以现在已经不推荐是用Thread.stop方法了。

那我们还有两种方式来关闭一个Thread。

## Flag变量

如果我们有一个无法自动停止的Thread，我们可以创建一个条件变量，通过不断判断该变量的值，来决定是否结束该线程的运行。

```java
public class KillThread implements Runnable {
    private Thread worker;
    private final AtomicBoolean running = new AtomicBoolean(false);
    private int interval;

    public KillThread(int sleepInterval) {
        interval = sleepInterval;
    }

    public void start() {
        worker = new Thread(this);
        worker.start();
    }

    public void stop() {
        running.set(false);
    }

    public void run() {
        running.set(true);
        while (running.get()) {
            try {
                Thread.sleep(interval);
            } catch (InterruptedException e){
                Thread.currentThread().interrupt();
                log.info("Thread was interrupted, Failed to complete operation");
            }
            // do something here
        }
        log.info("finished");
    }

    public static void main(String[] args) {
        KillThread killThread= new KillThread(1000);
        killThread.start();
        killThread.stop();
    }


}
```

上面的例子中，我们通过定义一个AtomicBoolean 的原子变量来存储Flag标志。

我们将会在后面的文章中详细的讲解原子变量。

## 调用interrupt()方法

通过调用interrupt()方法，将会中断正在等待的线程，并抛出InterruptedException异常。

根据Oracle的说明，如果你想自己处理这个异常的话，需要reasserts出去，注意，这里是reasserts而不是rethrows，因为有些情况下，无法rethrow这个异常，我们需要这样做：

```java
Thread.currentThread().interrupt();
```

这将会reasserts InterruptedException异常。

看下我们第二种方法怎么调用：

```java
public class KillThread implements Runnable {
    private Thread worker;
    private final AtomicBoolean running = new AtomicBoolean(false);
    private int interval;

    public KillThread(int sleepInterval) {
        interval = sleepInterval;
    }

    public void start() {
        worker = new Thread(this);
        worker.start();
    }

    public void interrupt() {
        running.set(false);
        worker.interrupt();
    }

    public void stop() {
        running.set(false);
    }

    public void run() {
        running.set(true);
        while (running.get()) {
            try {
                Thread.sleep(interval);
            } catch (InterruptedException e){
                Thread.currentThread().interrupt();
                log.info("Thread was interrupted, Failed to complete operation");
            }
            // do something here
        }
        log.info("finished");
    }

    public static void main(String[] args) {
        KillThread killThread= new KillThread(1000);
        killThread.start();
        killThread.interrupt();
    }
}
```

# java中的Atomic类

## 背景

在多线程环境中，我们最常遇到的问题就是变量的值进行同步。因为变量需要在多线程中进行共享，所以我们必须需要采用一定的同步机制来进行控制。

通过之前的文章，我们知道可以采用Lock的机制，当然也包括今天我们讲的Atomic类。

下面我们从两种方式来分别介绍。

## Lock

在之前的文章中，我们也讲了同步的问题，我们再回顾一下。 如果定义了一个计数器如下：

```java
public class Counter {

    int counter;

    public void increment() {
        counter++;
    }

}
```

如果是在单线程环境中，上面的代码没有任何问题。但是如果在多线程环境中，counter++将会得到不同的结果。

因为虽然counter++看起来是一个原子操作，但是它实际上包含了三个操作：读数据，加一，写回数据。

我们之前的文章也讲了，如何解决这个问题：

```java
public class LockCounter {

    private volatile int counter;

    public synchronized void increment() {
        counter++;
    }
}
```

通过加synchronized，保证同一时间只会有一个线程去读写counter变量。

通过volatile，保证所有的数据直接操作的主缓存，而不使用线程缓存。

这样虽然解决了问题，但是性能可能会受影响，因为synchronized会锁住整个LockCounter实例。

## 使用Atomic

通过引入低级别的原子化语义命令（比如compare-and-swap (CAS)），从而能在保证效率的同时保证原子性。

一个标准的CAS包含三个操作：

1. 将要操作的内存地址M。
2. 现有的变量A。
3. 新的需要存储的变量B。

CAS将会先比较A和M中存储的值是否一致，一致则表示其他线程未对该变量进行修改，则将其替换为B。 否则不做任何操作。

使用CAS可以不用阻塞其他的线程，但是我们需要自己处理好当更新失败的情况下的业务逻辑处理情况。

Java提供了很多Atomic类，最常用的包括AtomicInteger, AtomicLong, AtomicBoolean, 和 AtomicReference.

其中的主要方法：

1. get() – 直接中主内存中读取变量的值，类似于volatile变量。
2. set() – 将变量写回主内存。类似于volatile变量。
3. lazySet() – 延迟写回主内存。一种常用的情景是将引用重置为null的情况。
4. compareAndSet() – 执行CAS操作，成功返回true，失败返回false。
5. weakCompareAndSet() – 比较弱的CAS操作，不同的是它不执行happens-before操作，从而不保证能够读取到其他变量最新的值。

我们看下怎么用：

```java
public class AtomicCounter {
    private final AtomicInteger counter = new AtomicInteger(0);

    public int getValue() {
        return counter.get();
    }
    public void increment() {
        while(true) {
            int existingValue = getValue();
            int newValue = existingValue + 1;
            if(counter.compareAndSet(existingValue, newValue)) {
                return;
            }
        }
    }
}
```

# java中interrupt,interrupted和isInterrupted的区别

前面的文章我们讲到了调用interrupt()来停止一个Thread，本文将会详细讲解java中三个非常相似的方法interrupt，interrupted和isInterrupted。

## isInterrupted

首先看下最简单的isInterrupted方法。isInterrupted是Thread类中的一个实例方法：

```java
    public boolean isInterrupted() {
        return isInterrupted(false);
    }
```

通过调用isInterrupted（）可以判断实例线程是否被中断。

它的内部调用了isInterrupted(false)方法：

```java
  /**
     * Tests if some Thread has been interrupted.  The interrupted state
     * is reset or not based on the value of ClearInterrupted that is
     * passed.
     */
    private native boolean isInterrupted(boolean ClearInterrupted);
```

这个方法是个native方法，接收一个是否清除Interrupted标志位的参数。

我们可以看到isInterrupted()传入的参数是false，这就表示isInterrupted()只会判断是否被中断，而不会清除中断状态。

## interrupted

interrupted是Thread中的一个类方法：

```java
 public static boolean interrupted() {
        return currentThread().isInterrupted(true);
    }
```

我们可以看到，interrupted（）也调用了isInterrupted(true)方法，不过它传递的参数是true，表示将会清除中断标志位。

*注意，因为interrupted()是一个类方法，调用isInterrupted(true)判断的是当前线程是否被中断。注意这里currentThread（）的使用。*

## interrupt

前面两个是判断是否中断的方法，而interrupt（）就是真正触发中断的方法。

我们先看下interrupt的定义：

```java
    public void interrupt() {
        if (this != Thread.currentThread())
            checkAccess();

        synchronized (blockerLock) {
            Interruptible b = blocker;
            if (b != null) {
                interrupt0();           // Just to set the interrupt flag
                b.interrupt(this);
                return;
            }
        }
        interrupt0();
    }
```

从定义我们可以看到interrupt（）是一个实例方法。

它的工作要点有下面4点：

1. 如果当前线程实例在调用Object类的wait（），wait（long）或wait（long，int）方法或join（），join（long），join（long，int）方法，或者在该实例中调用了Thread.sleep（long）或Thread.sleep（long，int）方法，并且正在阻塞状态中时，则其中断状态将被清除，并将收到InterruptedException。
2. 如果此线程在InterruptibleChannel上的I / O操作中处于被阻塞状态，则该channel将被关闭，该线程的中断状态将被设置为true，并且该线程将收到java.nio.channels.ClosedByInterruptException异常。
3. 如果此线程在java.nio.channels.Selector中处于被被阻塞状态，则将设置该线程的中断状态为true，并且它将立即从select操作中返回。
4. 如果上面的情况都不成立，则设置中断状态为true。

```java
@Slf4j
public class InterruptThread extends Thread {
    @Override
    public  void run() {
        for (int i = 0; i < 1000; i++) {
            log.info("i= {}", (i+1));
            log.info("call inside thread.interrupted()： {}", Thread.interrupted());
        }
    }

    @Test
    public void testInterrupt(){
        InterruptThread thread=new InterruptThread();
        thread.start();
        thread.interrupt();
        //test isInterrupted
        log.info("first call isInterrupted(): {}", thread.isInterrupted());
        log.info("second call isInterrupted(): {}", thread.isInterrupted());

        //test interrupted（)
        log.info("first call outside thread.interrupted()： {}", Thread.interrupted());
        log.info("second call outside thread.interrupted() {}：", Thread.interrupted());
        log.info("thread is alive : {}",thread.isAlive() );
    }
}
```

输出结果如下：

```txt
13:07:17.804 [main] INFO com.flydean.InterruptThread - first call isInterrupted(): true
13:07:17.808 [main] INFO com.flydean.InterruptThread - second call isInterrupted(): true

13:07:17.808 [Thread-1] INFO com.flydean.InterruptThread - call inside thread.interrupted()： true
13:07:17.808 [Thread-1] INFO com.flydean.InterruptThread - call inside thread.interrupted()： false

13:07:17.808 [main] INFO com.flydean.InterruptThread - first call outside thread.interrupted()： false
13:07:17.809 [main] INFO com.flydean.InterruptThread - second call outside thread.interrupted() false
```

上面的例子中，两次调用thread.isInterrupted()的值都是true。

在线程内部调用Thread.interrupted()， 只有第一次返回的是ture，后面返回的都是false，这表明第一次被重置了。

在线程外部，因为并没有中断外部线程，所以返回的值一直都是false。

# java中的daemon thread

java中有两种类型的thread，user threads 和 daemon threads。

User threads是高优先级的thread，JVM将会等待所有的User Threads运行完毕之后才会结束运行。

daemon threads是低优先级的thread，它的作用是为User Thread提供服务。 因为daemon threads的低优先级，并且仅为user thread提供服务，所以当所有的user thread都结束之后，JVM会自动退出，不管是否还有daemon threads在运行中。

因为这个特性，所以我们通常在daemon threads中处理无限循环的操作，因为这样不会影响user threads的运行。

daemon threads并不推荐使用在I/O操作中。

但是有些不当的操作也可能导致daemon threads阻塞JVM关闭，比如在daemon thread中调用join（）方法。

```java
public class DaemonThread extends Thread{

    public void  run(){
        while(true){
            log.info("Thread A run");
            try {
                log.info("Thread A is daemon {}" ,Thread.currentThread().isDaemon());
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }


    public static void main(String[] args) throws InterruptedException {
        DaemonThread daemonThread = new DaemonThread();
        daemonThread.setDaemon(true);
        daemonThread.start();
    }
}
```

创建 daemon thread很简单，只需要在创建之后，设置其daemon属性为true即可。

*注意setDaemon(true)必须在thread start（）之前执行，否则会抛出IllegalThreadStateException*

上面的例子将会立刻退出。

如果我们将daemonThread.setDaemon(true);去掉，则因为user thread一直执行，JVM将会一直运行下去，不会退出。

这是在main中运行的情况，如果我们在一个@Test中运行，会发生什么现象呢？

```java
public class DaemonThread extends Thread{

    public void  run(){
        while(true){
            log.info("Thread A run");
            try {
                log.info("Thread A is daemon {}" ,Thread.currentThread().isDaemon());
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    @Test
    public void testDaemon() throws InterruptedException {
        DaemonThread daemonThread = new DaemonThread();
        daemonThread.start();
    }
}
```

我们将main方法改成了@Test执行。执行之后我们会发现，不管是不是daemon thread， Test都会立刻结束。

再看一个daemon线程中启动一个user thread的情况：

```java
public class DaemonBThread extends Thread{

    Thread worker = new Thread(()->{
        while(true){
            log.info("Thread B run");
            log.info("Thread B is daemon {}",Thread.currentThread().isDaemon());
        }
    });
    public void  run(){
            log.info("Thread A run");
            worker.start();
    }

    public static void main(String[] args) {
        DaemonBThread daemonThread = new DaemonBThread();
        daemonThread.setDaemon(true);
        daemonThread.start();
    }
}
```

这个例子中，daemonThread启动了一个user thread，运行之后我们会发现，即使有user thread正在运行，JVM也会立刻结束执行。

# java中ThreadPool的介绍和使用

## Thread Pool简介

在Java中，threads是和系统的threads相对应的，用来处理一系列的系统资源。不管在windows和linux下面，能开启的线程个数都是有限的，如果你在java程序中无限制的创建thread，那么将会遇到无线程可创建的情况。

CPU的核数是有限的，如果同时有多个线程正在运行中，那么CPU将会根据线程的优先级进行轮循，给每个线程分配特定的CPU时间。所以线程也不是越多越好。

在java中，代表管理ThreadPool(线程池)的接口有两个：ExecutorService和Executor。

我们运行线程的步骤一般是这样的：1. 创建一个ExecutorService。 2.将任务提交给ExecutorService。3.ExecutorService调度线程来运行任务。

画个图来表示：

![image-20220730163007025](../../../../../Download/Typora/image/image-20220730163007025.png)



下面我讲一下，怎么在java中使用ThreadPool。

## Executors, Executor 和 ExecutorService

Executors 提供了一系列简便的方法，来帮助我们创建ThreadPool。

Executor接口定义了一个方法：

```java
public interface Executor {

    /**
     * Executes the given command at some time in the future.  The command
     * may execute in a new thread, in a pooled thread, or in the calling
     * thread, at the discretion of the {@code Executor} implementation.
     *
     * @param command the runnable task
     * @throws RejectedExecutionException if this task cannot be
     * accepted for execution
     * @throws NullPointerException if command is null
     */
    void execute(Runnable command);
}
```

ExecutorService继承了Executor，提供了更多的线程池的操作。是对Executor的补充。

根据接口实现分离的原则，我们通常在java代码中使用ExecutorService或者Executor，而不是具体的实现类。

我们看下怎么通过Executors来创建一个Executor和ExecutorService：

```java
        Executor executor = Executors.newSingleThreadExecutor();
        executor.execute(() -> log.info("in Executor"));


        ExecutorService executorService= Executors.newCachedThreadPool();
        executorService.submit(()->log.info("in ExecutorService"));
        executorService.shutdown();
```

关于ExecutorService的细节，我们这里就多讲了，感兴趣的朋友可以参考之前我写的ExecutorService的详细文章。

## ThreadPoolExecutor

ThreadPoolExecutor是ExecutorService接口的一个实现，它可以为线程池添加更加精细的配置，具体而言它可以控制这三个参数：corePoolSize, maximumPoolSize, 和 keepAliveTime。

PoolSize就是线程池里面的线程个数，corePoolSize表示的是线程池里面初始化和保持的最小的线程个数。

如果当前等待线程太多，可以设置maximumPoolSize来提供最大的线程池个数，从而线程池会创建更多的线程以供任务执行。

keepAliveTime是多余的线程未分配任务将会等待的时间。超出该时间，线程将会被线程池回收。

我们看下怎么创建一个ThreadPoolExecutor：

```java
        ThreadPoolExecutor threadPoolExecutor =
                new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS,
                        new LinkedBlockingQueue<Runnable>());
        threadPoolExecutor.submit(()->log.info("submit through threadPoolExecutor"));
        threadPoolExecutor.shutdown();
```

上面的例子中我们通过ThreadPoolExecutor的构造函数来创建ThreadPoolExecutor。

通常来说Executors已经内置了ThreadPoolExecutor的很多实现，我们来看下面的例子：

```java
ThreadPoolExecutor executor1 =
                (ThreadPoolExecutor) Executors.newFixedThreadPool(2);
        executor1.submit(() -> {
            Thread.sleep(1000);
            return null;
        });
        executor1.submit(() -> {
            Thread.sleep(1000);
            return null;
        });
        executor1.submit(() -> {
            Thread.sleep(1000);
            return null;
        });
        log.info("executor1 poolsize {}",executor1.getPoolSize());
        log.info("executor1 queuesize {}", executor1.getQueue().size());
        executor1.shutdown();
```

上的例子中我们Executors.newFixedThreadPool(2)来创建一个ThreadPoolExecutor。

上面的例子中我们提交了3个task。但是我们pool size只有2。所以还有一个1个不能立刻被执行，需要在queue中等待。

我们再看一个例子：

```java
ThreadPoolExecutor executor2 =
                (ThreadPoolExecutor) Executors.newCachedThreadPool();
        executor2.submit(() -> {
            Thread.sleep(1000);
            return null;
        });
        executor2.submit(() -> {
            Thread.sleep(1000);
            return null;
        });
        executor2.submit(() -> {
            Thread.sleep(1000);
            return null;
        });

        log.info("executor2 poolsize {}", executor2.getPoolSize());
        log.info("executor2 queue size {}", executor2.getQueue().size());
        executor2.shutdown();
```

上面的例子中我们使用Executors.newCachedThreadPool()来创建一个ThreadPoolExecutor。 运行之后我们可以看到poolsize是3，而queue size是0。这表明newCachedThreadPool会自动增加pool size。

如果thread在60秒钟之类没有被激活，则会被收回。

这里的Queue是一个SynchronousQueue，因为插入和取出基本上是同时进行的，所以这里的queue size基本都是0.

## ScheduledThreadPoolExecutor

还有个很常用的ScheduledThreadPoolExecutor，它继承自ThreadPoolExecutor, 并且实现了ScheduledExecutorService接口。

```java
public class ScheduledThreadPoolExecutor
        extends ThreadPoolExecutor
        implements ScheduledExecutorService
```

我们看下怎么使用：

```java
ScheduledExecutorService executor = Executors.newScheduledThreadPool(5);
        executor.schedule(() -> {
            log.info("Hello World");
        }, 500, TimeUnit.MILLISECONDS);
```

上面的例子中，我们定义了一个定时任务将会在500毫秒之后执行。

之前我们也讲到了ScheduledExecutorService还有两个非常常用的方法：

- scheduleAtFixedRate – 以开始时间为间隔。
- scheduleWithFixedDelay – 以结束时间为间隔。

```java
CountDownLatch lock = new CountDownLatch(3);

        ScheduledExecutorService executor2 = Executors.newScheduledThreadPool(5);
        ScheduledFuture<?> future = executor2.scheduleAtFixedRate(() -> {
            log.info("in ScheduledFuture");
            lock.countDown();
        }, 500, 100, TimeUnit.MILLISECONDS);

        lock.await(1000, TimeUnit.MILLISECONDS);
        future.cancel(true);
```

## ForkJoinPool

ForkJoinPool是在java 7 中引入的新框架，我们将会在后面的文章中详细讲解。 这里做个简单的介绍。

ForkJoinPool主要用来生成大量的任务来做算法运算。如果用线程来做的话，会消耗大量的线程。但是在fork/join框架中就不会出现这个问题。

在fork/join中，任何task都可以生成大量的子task，然后通过使用join（）等待子task结束。

这里我们举一个例子：

```java
static class TreeNode {

    int value;

    Set<TreeNode> children;

    TreeNode(int value, TreeNode... children) {
        this.value = value;
        this.children = Sets.newHashSet(children);
    }
}
```

定义一个TreeNode，然后遍历所有的value，将其加起来：

```java
public  class CountingTask extends RecursiveTask<Integer> {

    private final TreeNode node;

    public CountingTask(TreeNode node) {
        this.node = node;
    }

    @Override
    protected Integer compute() {
        return node.value + node.children.stream()
                .map(childNode -> new CountingTask(childNode).fork()).mapToInt(ForkJoinTask::join).sum();
    }
}
```

下面是调用的代码：

```java
    public static void main(String[] args) {
        TreeNode tree = new TreeNode(5,
                new TreeNode(3), new TreeNode(2,
                new TreeNode(2), new TreeNode(8)));

        ForkJoinPool forkJoinPool = ForkJoinPool.commonPool();
        int sum = forkJoinPool.invoke(new CountingTask(tree));
    }
```

# java中Locks的使用

之前文章中我们讲到，java中实现同步的方式是使用synchronized block。在java 5中，Locks被引入了，来提供更加灵活的同步控制。

## 公平锁/非公平锁

**概念**：所谓**公平锁**，就是多个线程按照**申请锁的顺序**来获取锁，类似排队，先到先得。而**非公平锁**，则是多个线程抢夺锁，会导致**优先级反转**或**饥饿现象**。

**区别**：公平锁在获取锁时先查看此锁维护的**等待队列**，**为空**或者当前线程是等待队列的**队首**，则直接占有锁，否则插入到等待队列，FIFO原则。非公平锁比较粗鲁，上来直接**先尝试占有锁**，失败则采用公平锁方式。非公平锁的优点是**吞吐量**比公平锁更大。

`synchronized`和`juc.ReentrantLock`默认都是**非公平锁**。`ReentrantLock`在构造的时候传入`true`则是**公平锁**。

## Lock和Synchronized Block的区别

我们在之前的Synchronized Block的文章中讲到了使用Synchronized来实现java的同步。既然Synchronized Block那么好用，为什么会引入新的Lock呢？

主要有下面几点区别：

1. synchronized block只能写在一个方法里面，而Lock的lock()和unlock()可以分别在不同的方法里面。
2. synchronized block 不支持公平锁，一旦锁被释放，任何线程都有机会获取被释放的锁。而使用 Lock APIs则可以支持公平锁。从而让等待时间最长的线程有限执行。
3. 使用synchronized block，如果线程拿不到锁，将会被Blocked。 Lock API 提供了一个tryLock() 的方法，可以判断是否可以获得lock，这样可以减少线程被阻塞的时间。
4. 当线程在等待synchronized block锁的时候，是不能被中断的,除非抛出异常或者正常运行完成。如果使用Lock API，则可以使用 lockInterruptibly()来中断线程。
5. synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；

<img src="../../../../../Download/Typora/image/image-20220730171336070.png" alt="image-20220730171336070" style="zoom:80%;" />

## Lock interface

我们来看下Lock interface的定义, Lock interface定义了下面几个主要使用的方法：

- void lock() – 尝试获取锁，如果获取不到锁，则会进入阻塞状态。
- void lockInterruptibly() – 和lock（）很类似，但是它可以将正在阻塞的线程中断，并抛出java.lang.InterruptedException。
- boolean tryLock() – 这是lock()的非阻塞版本，它回尝试获取锁，并立刻返回是否获取成功。
- boolean tryLock(long timeout, TimeUnit timeUnit) – 和tryLock()很像，只是多了一个尝试获取锁的时间。
- void unlock() – unlock实例。
- Condition newCondition() – 生成一个和当前Lock实例绑定的Condition。

在使用Lock的时候，一定要unlocked，以避免死锁。所以，通常我们我们要在try catch中使用：

catch中使用：

```java
Lock lock = ...; 
lock.lock();
try {
    // access to the shared resource
} finally {
    lock.unlock();
}
```

除了Lock接口，还有一个ReadWriteLock接口，在其中定义了两个方法，实现了读锁和写锁分离：

除了Lock接口，还有一个ReadWriteLock接口，在其中定义了两个方法，实现了读锁和写锁分离：

- Lock readLock() – 返回读锁
- Lock writeLock() – 返回写锁

其中读锁可以同时被很多线程获得，只要不进行写操作。写锁同时只能被一个线程获取。

接下来，我们几个Lock的常用是实现类。

## ReentrantLock

ReentrantLock是Lock的一个实现，什么是ReentrantLock（可重入锁）呢？

简单点说可重入锁就是当前线程已经获得了该锁，如果该线程的其他方法在调用的时候也需要获取该锁，那么该锁的lock数量+1，并且允许进入该方法。

> 不可重入锁：只判断这个锁有没有被锁上，只要被锁上申请锁的线程都会被要求等待。实现简单
>
> 可重入锁：不仅判断锁有没有被锁上，还会判断锁是谁锁上的，当就是自己锁上的时候，那么他依旧可以再次访问临界资源，并把加锁次数加一。

我们看下怎么使用ReentrantLock：

```java
    public void perform() {

        lock.lock();
        try {
            counter++;
        } finally {
            lock.unlock();
        }
    }
```

下面是使用tryLock（）的例子：

```java
    public void performTryLock() throws InterruptedException {
        boolean isLockAcquired = lock.tryLock(1, TimeUnit.SECONDS);

        if(isLockAcquired) {
            try {
                counter++;
            } finally {
                lock.unlock();
            }
        }
    }
```

## ReentrantReadWriteLock

ReentrantReadWriteLock是ReadWriteLock的一个实现。上面也讲到了ReadWriteLock主要有两个方法：

- Read Lock – 如果没有线程获得写锁，那么可以多个线程获得读锁。
- Write Lock – 如果没有其他的线程获得读锁和写锁，那么只有一个线程能够获得写锁。

我们看下怎么使用writeLock：

```java
    Map<String,String> syncHashMap = new HashMap<>();
    ReadWriteLock lock = new ReentrantReadWriteLock();

    Lock writeLock = lock.writeLock();

    public void put(String key, String value) {
        try {
            writeLock.lock();
            syncHashMap.put(key, value);
        } finally {
            writeLock.unlock();
        }
    }

    public String remove(String key){
        try {
            writeLock.lock();
            return syncHashMap.remove(key);
        } finally {
            writeLock.unlock();
        }
    }
```

再看下怎么使用readLock：

```java
    Lock readLock = lock.readLock();
    public String get(String key){
        try {
            readLock.lock();
            return syncHashMap.get(key);
        } finally {
            readLock.unlock();
        }
    }

    public boolean containsKey(String key) {
        try {
            readLock.lock();
            return syncHashMap.containsKey(key);
        } finally {
            readLock.unlock();
        }
    }
```

## 乐观锁的概念：

​    乐观锁：指的是在操作数据的时候非常乐观，乐观地认为别人不会同时修改数据，因此乐观锁默认是不会上锁的，只有在执行更新的时候才会去判断在此期间别人是否修改了数据，如果别人修改了数据则放弃操作，否则执行操作。

​    冲突比较少的时候, 使用乐观锁(没有[悲观锁](https://so.csdn.net/so/search?q=悲观锁&spm=1001.2101.3001.7020)那样耗时的开销) 由于乐观锁的不上锁特性，所以在性能方面要比悲观锁好，比较适合用在DB的读大于写的业务场景。

## 悲观锁的概念：

​    悲观锁：指的是在操作数据的时候比较悲观，悲观地认为别人一定会同时修改数据，因此悲观锁在操作数据时是直接把数据上锁，直到操作完成之后才会释放锁，在上锁期间其他人不能操作数据。

​    冲突比较多的时候, 使用悲观锁(没有乐观锁那么多次的尝试)对于每一次数据修改都要上锁，如果在DB读取需要比较大的情况下有线程在执行数据修改操作会导致读操作全部被挂载起来，等修改线程释放了锁才能读到数据，体验极差。所以比较适合用在DB写大于读的情况。

读取频繁使用乐观锁，写入频繁使用悲观锁。

## StampedLock

StampedLock也支持读写锁，获取锁的是会返回一个stamp，通过该stamp来进行释放锁操作。

上我们讲到了如果写锁存在的话，读锁是无法被获取的。但有时候我们读操作并不想进行加锁操作，这个时候我们就需要使用乐观读锁。

StampedLock中的stamped类似乐观锁中的版本的概念，当我们在
StampedLock中调用lock方法的时候，就会返回一个stamp，代表锁当时的状态，在乐观读锁的使用过程中，在读取数据之后，我们回去判断该stamp状态是否变化，如果变化了就说明该stamp被另外的write线程修改了，这说明我们之前的读是无效的，这个时候我们就需要将乐观读锁升级为读锁，来重新获取数据。

我们举个例子，先看下write排它锁的情况：

```java
    private double x, y;
    private final StampedLock sl = new StampedLock();

    void move(double deltaX, double deltaY) { // an exclusively locked method
        long stamp = sl.writeLock();
        try {
            x += deltaX;
            y += deltaY;
        } finally {
            sl.unlockWrite(stamp);
        }
    }
```

再看下乐观读锁的情况：

```java
    double distanceFromOrigin() { // A read-only method
        long stamp = sl.tryOptimisticRead();
        double currentX = x, currentY = y;
        if (!sl.validate(stamp)) {
            stamp = sl.readLock();
            try {
                currentX = x;
                currentY = y;
            } finally {
                sl.unlockRead(stamp);
            }
        }
        return Math.sqrt(currentX * currentX + currentY * currentY);
    }
```

上面使用tryOptimisticRead（）来尝试获取乐观读锁，然后通过sl.validate(stamp)来判断该stamp是否被改变，如果改变了，说明之前的read是无效的，那么需要重新来读取。

最后，StampedLock还提供了一个将read锁和乐观读锁升级为write锁的功能：

```java
   void moveIfAtOrigin(double newX, double newY) { // upgrade
        // Could instead start with optimistic, not read mode
        long stamp = sl.readLock();
        try {
            while (x == 0.0 && y == 0.0) {
                long ws = sl.tryConvertToWriteLock(stamp);
                if (ws != 0L) {
                    stamp = ws;
                    x = newX;
                    y = newY;
                    break;
                }
                else {
                    sl.unlockRead(stamp);
                    stamp = sl.writeLock();
                }
            }
        } finally {
            sl.unlock(stamp);
        }
    }
```

上面的例子是通过使用tryConvertToWriteLock(stamp)来实现升级的。

## Conditions

上面讲Lock接口的时候有提到其中的一个方法：

```java
Condition newCondition();
```

Condition提供了await和signal方法，类似于Object中的wait和notify。

不同的是Condition提供了更加细粒度的等待集划分。我们举个例子：

```java
public class ConditionUsage {
    final Lock lock = new ReentrantLock();
    final Condition notFull  = lock.newCondition();
    final Condition notEmpty = lock.newCondition();

    final Object[] items = new Object[100];
    int putptr, takeptr, count;

    public void put(Object x) throws InterruptedException {
        lock.lock();
        try {
            while (count == items.length)
                notFull.await();
            items[putptr] = x;
            if (++putptr == items.length) putptr = 0;
            ++count;
            notEmpty.signal();
        } finally {
            lock.unlock();
        }
    }

    public Object take() throws InterruptedException {
        lock.lock();
        try {
            while (count == 0)
                notEmpty.await();
            Object x = items[takeptr];
            if (++takeptr == items.length) takeptr = 0;
            --count;
            notFull.signal();
            return x;
        } finally {
            lock.unlock();
        }
    }
}
```

上面的例子实现了一个ArrayBlockingQueue，我们可以看到在同一个Lock实例中，创建了两个Condition，分别代表队列未满，队列未空。通过这种细粒度的划分，我们可以更好的控制业务逻辑。





