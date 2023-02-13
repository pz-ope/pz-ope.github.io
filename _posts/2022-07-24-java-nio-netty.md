# netty:netty初探

## 简介

我们常用浏览器来访问web页面得到相关的信息，通常来说使用的都是HTTP或者HTTPS协议，这些协议的本质上都是IO，客户端的请求就是In，服务器的返回就是Out。但是在目前的协议框架中，并不能完全满足我们所有的需求。比如使用HTTP下载大文件，可能需要长连接等待等。
我们也知道IO方式有多种多样的，包括同步IO，异步IO，阻塞IO和非阻塞IO等。不同的IO方式其性能也是不同的，而netty就是一个基于异步事件驱动的NIO框架。

## netty介绍

Netty 是一个异步的、基于事件驱动的网络应用框架，用于快速开发可维护、高性能的网络服务器和客户端

**注意**：`netty的异步还是基于多路复用的，并没有实现真正意义上的异步IO`

> 异步IO一种独特的网络编程模型，但这里的netty并没有采用异步IO，这里的异步是指netty采用多线程模式，来实现netty方法调用的线程和处理结果的线程相分离。

netty是一个优秀的NIO框架，大家对IO的第一映像应该是比较复杂，尤其是跟各种HTTP、TCP、UDP协议打交道，使用起来非常复杂。但是netty提供了对这些协议的友好封装，通过netty可以快速而且简洁的进行IO编程。netty易于开发、性能优秀同时兼具稳定性和灵活性。如果你希望开发高性能的服务，那么使用netty总是没错的。

如果要在项目中使用，则可以引入下面的代码：

```xml
        <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
            <version>4.1.66.Final</version>
        </dependency>
```

## Netty的优势

如果使用传统NIO，其工作量大，bug 多

- 需要自己构建协议
- 解决 TCP 传输问题，如粘包、半包
- 因为bug的存在，epoll 空轮询导致 CPU 100%
- Netty 对 API 进行增强，使之更易用，如
  * FastThreadLocal => ThreadLocal
  * ByteBuf => ByteBuffer

Netty在Java网络应用框架中的地位就好比: Spring 框架在JavaEE开发中的地位

以下的框架都使用了Netty,因为它们有网络通信需求!

* Cassandra - nosql数据库
* Spark -大数据分布式计算框架
* Hadoop -大数据分布式存储框架
* RocketMQ - ali开源的消息队列
* ElasticSearch -搜索引擎
* gRPC-rpc框架
* Dubbo-rpc框架
* Spring 5.x- flux api完全抛弃了tomcat，使用netty作为服务器端
* Zookeeper -分布式协调框架

## netty的第一个服务器

什么叫做服务器？能够对外提供服务的程序就可以被称为是服务器。建立服务器是所有对外服务的第一步，怎么使用netty建立一个服务器呢？服务器主要负责处理各种服务端的请求，netty提供了一个ChannelInboundHandlerAdapter的类来处理这类请求，我们只需要继承这个类即可。

在NIO中每个channel都是客户端和服务器端沟通的通道。ChannelInboundHandlerAdapter定义了在这个channel上可能出现一些事件和情况，如下图所示：

<img src="/img/image/image-20220724213402469.png" alt="image-20220724213402469" style="zoom:50%;" />

如上图所示，channel上可以出现很多事件，比如建立连接，关闭连接，读取数据，读取完成，注册，取消注册等。这些方法都是可以被重写的，我们只需要新建一个类，继承ChannelInboundHandlerAdapter即可。

这里我们新建一个FirstServerHandler类，并重写channelRead和exceptionCaught两个方法，第一个方法是从channel中读取消息，第二个方法是对异常进行处理。

```java
public class FirstServerHandler extends ChannelInboundHandlerAdapter {
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) {
        // 对消息进行处理
        ByteBuf in = (ByteBuf) msg;
        try {
            log.info("收到消息:{}",in.toString(io.netty.util.CharsetUtil.US_ASCII));
        }finally {
            ReferenceCountUtil.release(msg);
        }
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        // 异常处理
        log.error("出现异常",cause);
        ctx.close();
    }
}
```

上面例子中，我们收到消息后调用release()方法将其释放，并不进行实际的处理。调用release方法是在消息使用完成之后常用的做法。上面代码将msg进行了ByteBuf的强制转换，如果并不想进行转换的话，可以直接这样使用：

```java
        try {
            // 消息处理
        } finally {
            ReferenceCountUtil.release(msg);
        }
```

有了Handler,我们需要新建一个Server类用来使用Handler创建channel和接收消息。接下来我们看一下netty的消息处理流程。

在netty中，对IO进行处理是使用多线程的event loop来实现的。netty中的`EventLoopGroup`就是这些event loop的抽象类。

<img src="/img/image/image-20220724213833509.png" alt="image-20220724213833509" style="zoom:80%;" />

可以看出EventLoopGroup继承自EventExecutorGroup，而EventExecutorGroup继承自JDK自带的ScheduledExecutorService。

所以EventLoopGroup本质是是一个线程池服务，之所以叫做Group，是因为它里面包含了很多个EventLoop,可以通过调用next方法对EventLoop进行遍历。

EventLoop是用来处理注册到该EventLoop的channel中的IO信息，一个EventLoop就是一个Executor,通过不断的提交任务进行执行。当然，一个EventLoop可以注册多个channel，不过一般情况下并不这样处理。

EventLoopGroup将多个EventLoop组成了一个Group，通过其中的next方法，可以对Group中的EventLoop进行遍历。另外EventLoopGroup提供了一些register方法，将Channel注册到当前的EventLoop中。

从上图可以看到，register的返回结果是一个ChannelFuture，Future大家都很清楚，可以用来获得异步任务的执行结果，同样的ChannelFuture也是一个异步的结果承载器，可以通过调用sync方法来阻塞Future直到获得执行结果。

可以看到，register方法还可以传入一个ChannelPromise对象，ChannelPromise它同时是ChannelFuture和Promise的子类，Promise又是Future的子类，它是一个特殊的可以控制Future状态的Future。

EventLoopGroup有很多子类的实现，这里我们使用NioEventLoopGroup，Nio使用Selector对channel进行选择。还有一个特性是NioEventLoopGroup可以添加子EventLoopGroup。

对于NIO服务器程序来说，我们需要两个Group，一个group叫做bossGroup，主要用来监控连接，一个group叫做worker group，用来处理被boss accept的连接，这些连接需要被注册到worker group中才能进行处理。

将这两个group传给ServerBootstrap，就可以从ServerBootstrap启动服务了，相应的代码如下：

```java
//建立两个EventloopGroup用来处理连接和消息
EventLoopGroup bossGroup = new NioEventLoopGroup();
EventLoopGroup workerGroup = new NioEventLoopGroup();
try {
    ServerBootstrap b = new ServerBootstrap();// 启动器，负责装配netty组件，启动服务器
    b.group(bossGroup, workerGroup)
        .channel(NioServerSocketChannel.class)// 选择服务器的 ServerSocketChannel 实现
        //child 负责处理读写，该方法决定了 child 执行哪些操作
        // ChannelInitializer 处理器（仅执行一次）
        // 它的作用是待客户端SocketChannel建立连接后，执行initChannel以便添加更多的处理器
        .childHandler(new ChannelInitializer<SocketChannel>() {
            @Override
            public void initChannel(SocketChannel ch) throws Exception {
                ch.pipeline().addLast(new FirstServerHandler());
            }
        })
        .option(ChannelOption.SO_BACKLOG, 128)
        .childOption(ChannelOption.SO_KEEPALIVE, true);
    // 绑定端口并开始接收连接
    ChannelFuture f = b.bind(port).sync();
}
```

我们最开始创建的FirstServerHandler最作为childHandler的处理器在初始化Channel的时候就被添加进去了。

这样，当有新建立的channel时，FirstServerHandler就会被用来处理该channel的数据。

我们还指定了一些ChannelOption，用于对channel的一些属性进行设定。

最后，我们绑定了对应的端口，并启动服务器。

## netty的第一个客户端

上面我们已经写好了服务器，并将其启动，现在还需要一个客户端和其进行交互。

如果不想写代码的话，可以直接telnet localhost 8000和server端进行交互即可，但是这里我们希望使用netty的API来构建一个client和Server进行交互。

构建netty客户端的流程和构建netty server端的流程基本一致。首先也需要创建一个Handler用来处理具体的消息，同样，这里我们也继承ChannelInboundHandlerAdapter。

上一节讲到了ChannelInboundHandlerAdapter里面有很多方法，可以根据自己业务的需要进行重写，这里我们希望当Channel active的时候向server发送一个消息。那么就需要重写channelActive方法，同时也希望对异常进行一些处理，所以还需要重写exceptionCaught方法。如果你想在channel读取消息的时候进行处理，那么可以重写channelRead方法。

创建的FirstClientHandler代码如下：

```java
@Slf4j
public class FirstClientHandler extends ChannelInboundHandlerAdapter {
    private ByteBuf content;
    private ChannelHandlerContext ctx;
    
    @Override
    public void channelActive(ChannelHandlerContext ctx) {
        this.ctx = ctx;
        content = ctx.alloc().directBuffer(256).writeBytes("Hello flydean.com".getBytes(StandardCharsets.UTF_8));
        // 发送消息
        ctx.writeAndFlush(content.retain());
    }
    
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        // 异常处理
        log.error("出现异常",cause);
        ctx.close();
    }
}
```

上面的代码中，我们首先从ChannelHandlerContext申请了一个ByteBuff，然后调用它的writeBytes方法，写入要传输的数据。最后调用ctx的writeAndFlush方法，向服务器输出消息。

接下来就是启动客户端服务了，在服务端我们建了两个NioEventLoopGroup，是兼顾了channel的选择和channel中消息的读取两部分。对于客户端来说，并不存在这个问题，这里只需要一个NioEventLoopGroup即可。

服务器端使用`ServerBootstrap`来启动服务，客户端使用的是`Bootstrap`，其启动的业务逻辑基本和服务器启动一致：

```java
        EventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap b = new Bootstrap();
            b.group(group)
             .channel(NioSocketChannel.class)
             .handler(new ChannelInitializer<SocketChannel>() {
                 @Override
                 protected void initChannel(SocketChannel ch) throws Exception {
                     ChannelPipeline p = ch.pipeline();
                     p.addLast(new FirstClientHandler());
                 }
             });

            // 连接服务器
            ChannelFuture f = b.connect(HOST, PORT).sync();
```

## 运行服务器和客户端

有了上述的准备工作，我们就可以运行了。首先运行服务器，再运行客户端。

如果没有问题的话，应该会输出下面的内容：

```java
[nioEventLoopGroup-3-1] INFO com.flydean01.FirstServerHandler - 收到消息:Hello flydean.com
```

## 总结

一个完整的服务器，客户端的例子就完成了。我们总结一下netty的工作流程，对于服务器端，首先建立handler用于对消息的实际处理，然后使用ServerBootstrap对EventLoop进行分组，并绑定端口启动。对于客户端来说，同样需要建立handler对消息进行处理，然后调用Bootstrap对EventLoop进行分组，并绑定端口启动。

- channel 可以理解为数据的通道
- msg 理解为流动的数据，最开始输入是 ByteBuf，但经过 pipeline 中的各个 handler 加工，会变成其它类型对象，最后输出又变成 ByteBuf
- handler 可以理解为数据的处理工序
  - 工序有多道，合在一起就是 pipeline（传递途径），pipeline 负责发布事件（读、读取完成…）传播给每个 handler， handler 对自己感兴趣的事件进行处理（重写了相应事件处理方法）
    - pipeline 中有多个 handler，处理时会依次调用其中的 handler
  - handler 分 Inbound 和 Outbound 两类
    - Inbound 入站
    - Outbound 出站
- eventLoop 可以理解为处理数据的工人
  - eventLoop 可以管理多个 channel 的 io 操作，并且一旦 eventLoop 负责了某个 channel，就**会将其与channel进行绑定**，以后该 channel 中的 io 操作都由该 eventLoop 负责
  - eventLoop 既可以执行 io 操作，**也可以进行任务处理**，每个 eventLoop 有自己的任务队列，队列里可以堆放多个 channel 的待处理任务，任务分为普通任务、定时任务
  - eventLoop 按照 pipeline 顺序，依次按照 handler 的规划（代码）处理数据，可以为每个 handler 指定不同的 eventLoop