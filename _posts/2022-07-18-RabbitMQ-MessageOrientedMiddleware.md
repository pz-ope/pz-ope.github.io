---
layout: post
title: "高级消息队列协议（AMQP）的开源消息代理软件"
subtitle: 'Incremental Data Collection - Oracle CDC'
author: "pzw"
header-style: text
lang: en
tags:
  - Java后端
  - 中间件

---

# 消息队列

消息（Message）是指在应用间传送的数据。消息可以非常简单，比如只包含文本字符串，也可以更复杂，可能包含嵌入对象。

消息队列（Message Queue）是一种应用间的通信方式，消息发送后可以立即返回，由消息系统来确保消息的可靠传递。消息发布者只管把消息发布到 MQ 中而不用管谁来取，消息使用者只管从 MQ 中取消息而不管是谁发布的。这样发布者和使用者都不用知道对方的存在。

# 为何用消息队列

从上面的描述中可以看出消息队列是一种`应用间的异步协作机制`，那什么时候需要使用 MQ 呢？

以常见的订单系统为例，用户点击【下单】按钮之后的业务逻辑可能包括：扣减库存、生成相应单据、发红包、发短信通知。在业务发展初期这些逻辑可能放在一起同步执行，随着业务的发展订单量增长，需要提升系统服务的性能，这时可以将一些不需要立即生效的操作拆分出来异步执行，比如发放红包、发短信通知等。这种场景下就可以用 MQ ，在下单的主流程（比如扣减库存、生成相应单据）完成之后发送一条消息到 MQ 让主流程快速完结，而由另外的单独线程拉取MQ的消息（或者由 MQ 推送消息），当发现 MQ 中有发红包或发短信之类的消息时，执行相应的业务逻辑。

以上是用于业务解耦的情况，其它常见场景包括`最终一致性`、`广播`、`错峰流控`等等。

# RabbitMQ 特点

RabbitMQ 是一个由 Erlang 语言开发的 AMQP 的开源实现。

AMQP ：Advanced Message Queue，高级消息队列协议。它是应用层协议的一个开放标准，为面向消息的中间件设计，基于此协议的客户端与消息中间件可传递消息，并不受产品、开发语言等条件的限制。

RabbitMQ 最初起源于金融系统，用于在分布式系统中存储转发消息，在易用性、扩展性、高可用性等方面表现不俗。具体特点包括：

1. 可靠性（Reliability） 

   RabbitMQ 使用一些机制来保证可靠性，如持久化、传输确认、发布确认。

2. 灵活的路由（Flexible Routing）

   在消息进入队列之前，通过 Exchange 来路由消息的。对于典型的路由功能，RabbitMQ 已经提供了一些内置的 Exchange 来实现。针对更复杂的路由功能，可以将多个 Exchange 绑定在一起，也通过插件机制实现自己的 Exchange 。

3. 消息集群（Clustering）

   多个 RabbitMQ 服务器可以组成一个集群，形成一个逻辑 Broker 。

4. 高可用（Highly Available Queues）

   队列可以在集群中的机器上进行镜像，使得在部分节点出问题的情况下队列仍然可用。

5. 多种协议（Multi-protocol）

   RabbitMQ 支持多种消息队列协议，比如 STOMP、MQTT 等等。

6. 多语言客户端（Many Clients）

   RabbitMQ 几乎支持所有常用语言，比如 Java、.NET、Ruby 等等。

7. 管理界面（Management UI）

   RabbitMQ 提供了一个易用的用户界面，使得用户可以监控和管理消息 Broker 的许多方面。

8. 跟踪机制（Tracing）

   如果消息异常，RabbitMQ 提供了消息跟踪机制，使用者可以找出发生了什么。

9. 插件机制（Plugin System）

   RabbitMQ 提供了许多插件，来从多方面进行扩展，也可以编写自己的插件。

# RabbitMQ 中的概念模型

## 消息模型

所有 MQ 产品从模型抽象上来说都是一样的过程：

消费者（consumer）订阅某个队列。生产者（producer）创建消息，然后发布到队列（queue）中，最后将消息发送到监听的消费者。

<img src="../../../../../Download/Typora/image/image-20220718171837326.png" alt="image-20220718171837326" style="zoom:40%;" />

## RabbitMQ 基本概念

上面只是最简单抽象的描述，具体到 RabbitMQ 则有更详细的概念需要解释。上面介绍过 RabbitMQ 是 AMQP 协议的一个开源实现，所以其内部实际上也是 AMQP 中的基本概念：

<img src="../../../../../Download/Typora/image/image-20220718171927383.png" alt="image-20220718171927383" style="zoom:60%;" />

* **Message**

  消息，消息是不具名的，它由消息头和消息体组成。消息体是不透明的，而消息头则由一系列的可选属性组成，这些属性包括routing-key（路由键）、priority（相对于其他消息的优先权）、delivery-mode（指出该消息可能需要持久性存储）等。

* **Publisher**

  消息的生产者，也是一个向交换器发布消息的客户端应用程序。

* **Exchange**

  交换器，用来接收生产者发送的消息并将这些消息路由给服务器中的队列。

* **Binding**

  绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表。

* **Queue**

  消息队列，用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走。

* **Connection**

  网络连接，比如一个TCP连接。

* **Channel**

  信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP连接内地虚拟连接，AMQP 命令都是通过信道发出去的，不管是发布消息、订阅队列还是接收消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁 TCP 都是非常昂贵的开销，所以引入了信道的概念，以复用一条 TCP 连接。

* Consumer

  消息的消费者，表示一个从消息队列中取得消息的客户端应用程序。

* Virtual Host

  虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个 vhost 本质上就是一个 mini 版的 RabbitMQ 服务器，拥有自己的队列、交换器、绑定和权限机制。vhost 是 AMQP 概念的基础，必须在连接时指定，RabbitMQ 默认的 vhost 是 / 。

* Broker

  表示消息队列服务器实体。

## AMQP 中的消息路由

AMQP 中消息的路由过程和 Java 开发者熟悉的 JMS 存在一些差别，AMQP 中增加了 Exchange 和 Binding 的角色。生产者把消息发布到 Exchange 上，消息最终到达队列并被消费者接收，而 Binding 决定交换器的消息应该发送到那个队列。

<img src="../../../../../Download/Typora/image/image-20220718205755752.png" alt="image-20220718205755752" style="zoom:80%;" />

## Exchange 类型

Exchange分发消息时根据类型的不同分发策略有区别，目前共四种类型：direct、fanout、topic、headers 。

headers 匹配 AMQP 消息的 header 而不是路由键，此外 headers 交换器和 direct 交换器完全一致，但性能差很多，目前几乎用不到了，所以直接看另外三种类型：

* **direct**

  <img src="../../../../../Download/Typora/image/image-20220718205939866.png" alt="image-20220718205939866" style="zoom:80%;" />

  消息中的路由键（routing key）如果和 Binding 中的 binding key 一致， 交换器就将消息发到对应的队列中。路由键与队列名完全匹配，如果一个队列绑定到交换机要求路由键为“dog”，则只转发 routing key 标记为“dog”的消息，不会转发“dog.puppy”，也不会转发“dog.guard”等等。它是完全匹配、单播的模式。

* **fanout**

  <img src="../../../../../Download/Typora/image/image-20220718210131481.png" alt="image-20220718210131481" style="zoom:80%;" />

  每个发到 fanout 类型交换器的消息都会分到所有绑定的队列上去。fanout 交换器不处理路由键，只是简单的将队列绑定到交换器上，每个发送到交换器的消息都会被转发到与该交换器绑定的所有队列上。很像子网广播，每台子网内的主机都获得了一份复制的消息。fanout 类型转发消息是最快的。

* **topic**

  <img src="../../../../../Download/Typora/image/image-20220718211732002.png" alt="image-20220718211732002" style="zoom:80%;" />

  topic 交换器通过模式匹配分配消息的路由键属性，将路由键和某个模式进行匹配，此时队列需要绑定到一个模式上。它将路由键和绑定键的字符串切分成单词，这些单词之间用点隔开。它同样也会识别两个通配符：符号“#”和符号“\*”。#匹配0个或多个单词，\*匹配不多不少一个单词。

# Java 客户端访问RabbitMQ

RabbitMQ 支持多种语言访问，以 Java 为例看下一般使用 RabbitMQ 的步骤。

1. maven工程的pom文件中添加依赖

   ```xml
   <dependency>
       <groupId>com.rabbitmq</groupId>
       <artifactId>amqp-client</artifactId>
       <version>4.1.0</version>
   </dependency>
   ```

2. 消息生产者

   ```java
   import com.rabbitmq.client.Channel;
   import com.rabbitmq.client.Connection;
   import com.rabbitmq.client.ConnectionFactory;
   import java.io.IOException;
   import java.util.concurrent.TimeoutException;
   public class Producer {
        public static void main(String[] args) throws IOException, TimeoutException {
            //创建连接工厂
           ConnectionFactory factory = new ConnectionFactory();
           factory.setUsername("guest");
           factory.setPassword("guest");
            //设置 RabbitMQ 地址
           factory.setHost("localhost");
            //建立到代理服务器到连接
           Connection conn = factory.newConnection();
            //获得信道
           Channel channel = conn.createChannel();
            //声明交换器
           String exchangeName = "hello-exchange";
           channel.exchangeDeclare(exchangeName, "direct", true);
            String routingKey = "hola";
            //发布消息
           byte[] messageBodyBytes = "quit".getBytes();
           channel.basicPublish(exchangeName, routingKey, null, messageBodyBytes);
            channel.close();
           conn.close();
        }
   }
   ```

3. 消息消费者

   ```java
   import com.rabbitmq.client.*;
   import java.io.IOException;
   import java.util.concurrent.TimeoutException;
   public class Consumer {
       public static void main(String[] args) throws IOException, TimeoutException {
           ConnectionFactory factory = new ConnectionFactory();
           factory.setUsername("guest");
           factory.setPassword("guest");
           factory.setHost("localhost");
           //建立到代理服务器到连接
           Connection conn = factory.newConnection();
           //获得信道
           final Channel channel = conn.createChannel();
           //声明交换器
           String exchangeName = "hello-exchange";
           channel.exchangeDeclare(exchangeName, "direct", true);
           //声明队列
           String queueName = channel.queueDeclare().getQueue();
           String routingKey = "hola";
           //绑定队列，通过键 hola 将队列和交换器绑定起来
           channel.queueBind(queueName, exchangeName, routingKey);
           while(true) {
               //消费消息
               boolean autoAck = false;
               String consumerTag = "";
               channel.basicConsume(queueName, autoAck, consumerTag, new DefaultConsumer(channel) {
                   @Override
                   public void handleDelivery(String consumerTag,
                                              Envelope envelope,
                                              AMQP.BasicProperties properties,
                                              byte[] body) throws IOException {
                       String routingKey = envelope.getRoutingKey();
                       String contentType = properties.getContentType();
                       System.out.println("消费的路由键：" + routingKey);
                       System.out.println("消费的内容类型：" + contentType);
                       long deliveryTag = envelope.getDeliveryTag();
                        //确认消息
                       channel.basicAck(deliveryTag, false);
                       System.out.println("消费的消息体内容：");
                       String bodyStr = new String(body, "UTF-8");
                       System.out.println(bodyStr);
                   }
               }
           }
       }
   }
   ```

4. 启动 RabbitMQ 服务器

   ```shell
   ./sbin/rabbitmq-server
   ```

5. 运行 Consumer
   先运行 Consumer ，这样当生产者发送消息的时候能在消费者后端看到消息记录。

6. 运行 Producer
   接着运行 Producer ,发布一条消息，在 Consumer 的控制台能看到接收的消息：

   <img src="../../../../../Download/Typora/image/image-20220719173807115.png" alt="image-20220719173807115" style="zoom:80%;" />



# RabbitMQ 集群

RabbitMQ 最优秀的功能之一就是内建集群，这个功能设计的目的是允许消费者和生产者在节点崩溃的情况下继续运行，以及通过添加更多的节点来线性扩展消息通信吞吐量。RabbitMQ 内部利用 Erlang 提供的分布式通信框架 OTP 来满足上述需求，使客户端在失去一个 RabbitMQ 节点连接的情况下，还是能够重新连接到集群中的任何其他节点继续生产、消费消息。

## RabbitMQ 集群中的一些概念

RabbitMQ 会始终记录以下四种类型的内部元数据：

1. 队列元数据
   包括队列名称和它们的属性，比如是否可持久化，是否自动删除
2. 交换器元数据
   交换器名称、类型、属性
3. 绑定元数据
   内部是一张表格记录如何将消息路由到队列
4. vhost 元数据
   为 vhost 内部的队列、交换器、绑定提供命名空间和安全属性

在单一节点中，RabbitMQ 会将所有这些信息存储在内存中，同时将标记为可持久化的队列、交换器、绑定存储到硬盘上。存到硬盘上可以确保队列和交换器在节点重启后能够重建。而在集群模式下同样也提供两种选择：存到硬盘上（独立节点的默认设置），存在内存中。

如果在集群中创建队列，集群只会在单个节点而不是所有节点上创建完整的队列信息（元数据、状态、内容）。结果是只有队列的所有者节点知道有关队列的所有信息，因此当集群节点崩溃时，该节点的队列和绑定就消失了，并且任何匹配该队列的绑定的新消息也丢失了。还好RabbitMQ 2.6.0之后提供了镜像队列以避免集群节点故障导致的队列内容不可用。

RabbitMQ 集群中可以共享 user、vhost、exchange等，所有的数据和状态都是必须在所有节点上复制的，例外就是上面所说的消息队列。RabbitMQ 节点可以动态的加入到集群中。

当在集群中声明队列、交换器、绑定的时候，这些操作会直到所有集群节点都成功提交元数据变更后才返回。集群中有内存节点和磁盘节点两种类型，内存节点虽然不写入磁盘，但是它的执行比磁盘节点要好。内存节点可以提供出色的性能，磁盘节点能保障配置信息在节点重启后仍然可用，那集群中如何平衡这两者呢？

RabbitMQ 只要求集群中至少有一个磁盘节点，所有其他节点可以是内存节点，当节点加入或离开集群时，它们必须要将该变更通知到至少一个磁盘节点。如果只有一个磁盘节点，刚好又是该节点崩溃了，那么集群可以继续路由消息，但不能创建队列、创建交换器、创建绑定、添加用户、更改权限、添加或删除集群节点。换句话说集群中的唯一磁盘节点崩溃的话，集群仍然可以运行，但直到该节点恢复，否则无法更改任何东西。

## RabbitMQ 集群配置和启动

如果是在一台机器上同时启动多个 RabbitMQ 节点来组建集群的话，只用使用默认的方式启动第二、第三个节点将会因为节点名称和端口冲突导致启动失败。

所以在每次调用 rabbitmq-server 命令前，设置环境变量 RABBITMQ_NODENAME 和 RABBITMQ_NODE_PORT 来明确指定唯一的节点名称和端口。下面的例子端口号从5672开始，每个新启动的节点都加1，节点也分别命名为test_rabbit_1、test_rabbit_2、test_rabbit_3。

启动第1个节点：

```undefined
RABBITMQ_NODENAME=test_rabbit_1 RABBITMQ_NODE_PORT=5672 ./sbin/rabbitmq-server -detached
```

启动第2个节点：

```undefined
RABBITMQ_NODENAME=test_rabbit_2 RABBITMQ_NODE_PORT=5673 ./sbin/rabbitmq-server -detached
```

启动第2个节点前建议将 RabbitMQ 默认激活的插件关掉，否则会存在使用了某个插件的端口号冲突，导致节点启动不成功。

现在第2个节点和第1个节点都是独立节点，它们并不知道其他节点的存在。集群中除第一个节点外后加入的节点需要获取集群中的元数据，所以要先停止 Erlang 节点上运行的 RabbitMQ 应用程序，并重置该节点元数据，再加入并且获取集群的元数据，最后重新启动 RabbitMQ 应用程序。

停止第2个节点的应用程序：

```undefined
./sbin/rabbitmqctl -n test_rabbit_2 stop_app
```

重置第2个节点元数据：

```undefined
./sbin/rabbitmqctl -n test_rabbit_2 reset
```

第2节点加入第1个节点组成的集群：

```dart
./sbin/rabbitmqctl -n test_rabbit_2 join_cluster test_rabbit_1@localhost
```

启动第2个节点的应用程序

```undefined
./sbin/rabbitmqctl -n test_rabbit_2 start_app
```

第3个节点的配置过程和第2个节点类似：

```dart
RABBITMQ_NODENAME=test_rabbit_3 RABBITMQ_NODE_PORT=5674 ./sbin/rabbitmq-server -detached

./sbin/rabbitmqctl -n test_rabbit_3 stop_app

./sbin/rabbitmqctl -n test_rabbit_3 reset

./sbin/rabbitmqctl -n test_rabbit_3 join_cluster test_rabbit_1@localhost

./sbin/rabbitmqctl -n test_rabbit_3 start_app
```

## RabbitMQ 集群运维

停止某个指定的节点，比如停止第2个节点：

```undefined
RABBITMQ_NODENAME=test_rabbit_2 ./sbin/rabbitmqctl stop
```

查看节点3的集群状态：

```undefined
./sbin/rabbitmqctl -n test_rabbit_3 cluster_status
```

# RabbitMQ6种模式

**大体分为两类：**

* 点对点模式：P2P（point to point）模式包含三个角色：

消息队列（queue），发送者（sender），接收者（receiver）

每个消息发送到一个特定的队列中，接收者从中获得消息

队列中保留这些消息，直到他们被消费或超时

特点：

1. 每个消息只有一个消费者，一旦消费，消息就不在队列中了

2. 发送者和接收者之间没有依赖性，发送者发送完成，不管接收者是否运行，都不会影响

消息发送到队列中（我给你发微信，不管你看不看手机，反正我发完了）

3. 接收者成功接收消息之后需向对象应答成功（确认）

如果希望发送的每个消息都会被成功处理，那需要P2P

* 发布订阅模式：publish（Pub）/subscribe（Sub）

pub/sub模式包含三个角色：交换机（exchange），发布者（publisher），订阅者

（subcriber）

多个发布者将消息发送交换机，系统将这些消息传递给多个订阅者

特点：

1. 每个消息可以有多个订阅者

2. 发布者和订阅者之间在时间上有依赖，对于某个交换机的订阅者，必须创建一个订阅

后，才能消费发布者的消息

3. 为了消费消息，订阅者必须保持运行状态；类似于，看电视直播。

如果希望发送的消息被多个消费者处理，可采用本模式

## simple简单模式

<img src="../../../../../Download/Typora/image/image-20220719182351525.png" alt="image-20220719182351525" style="zoom:80%;" />

一对一的消息模式，生产者往同一个队列里生产，只供一个消费者。

消息的消费者(consumer) 监听(while) 消息队列,如果队列中有消息,就消费掉,消息被拿走后,自动从队列中删除(隐患 消息可能没有被消费者正确处理,已经从队列中消失了,造成消息的丢失)应用场景:聊天(中间有一个过度的服务器;p端,c端)

## work工作模式(资源的竞争)

<img src="../../../../../Download/Typora/image/image-20220719182840244.png" alt="image-20220719182840244" style="zoom:80%;" />

一对多的消息模式，生产者往同一个消息队列里生产，供多个消费者消费（不会重复）。

应用场景:红包;大项目中的资源调度(任务分配系统不需知道哪一个任务执行系统在空闲,直接将任务扔到消息队列中,空闲的系统自动争抢)

## publish/subscribe发布订阅(共享资源)

<img src="../../../../../Download/Typora/image/image-20220719183016607.png" alt="image-20220719183016607" style="zoom:80%;" />

生产者生产数据，供多个消费者消费相同数据。由**交换机**把消息发布至所有消息队列中(不存在差异性)

相关场景:邮件群发,群聊天,广播(广告)

## routing路由模式

路由会根据类型进行***定向分发***消息给不同的队列(存在差异性)，如图所示

* 根据业务功能定义路由字符串
* 从系统的代码逻辑中获取对应的功能字符串,将消息任务扔到对应的队列中业务场景:error 通知;EXCEPTION;错误通知的功能;传统意义的错误通知;客户通知;利用key路由,可以将程序中的错误封装成消息传入到消息队列中,开发者可以自定义消费者,实时接收错误;

<img src="../../../../../Download/Typora/image/image-20220719183350398.png" alt="image-20220719183350398" style="zoom:80%;" />

## topic 主题模式(路由模式的一种)

<img src="../../../../../Download/Typora/image/image-20220719183511127.png" alt="image-20220719183511127" style="zoom:80%;" />

routing路由模式的升级版，增加了通配符

1. 星号井号代表通配符
2. 星号代表多个单词,井号代表一个单词
3. 路由功能添加模糊匹配
4. 消息产生者产生消息,把消息交给交换机
5. 交换机根据key的规则模糊匹配到对应的队列,由队列的监听消费者接收消息消费

## RPC模式

生产者生产消息后至消息队列，进入阻塞状态，服务器消费完后返回给客户端notify

<img src="../../../../../Download/Typora/image/image-20220719191258642.png" alt="image-20220719191258642" style="zoom:80%;" />

客户端发送请求（消息）时，在消息的属性（Message Properties，在AMQP协议中定义了14种properties，这些属性会随着消息一起发送）中设置两个值replyTo（一个Queue名称，用于告诉服务器处理完成后将通知我的消息发送到这个Queue中）和correlationId（此次请求的标识号，服务器处理完成后需要将此属性返还，客户端将根据这个id了解哪条请求被成功执行了或执行失败）。服务器端收到消息处理完后，将生成一条应答消息到replyTo指定的Queue，同时带上correlationId属性。客户端之前已订阅replyTo指定的Queue，从中收到服务器的应答消息后，根据其中的correlationId属性分析哪条请求被执行了，根据执行结果进行后续业务处理。

# **Message acknowledgment**

在实际应用中，可能会发生消费者收到Queue中的消息，但没有处理完成就宕机（或出现其他意外）的情况，这种情况下就可能会导致消息丢失。为了避免这种情况发生，我们可以要求消费者在消费完消息后发送一个回执给RabbitMQ，RabbitMQ收到消息回执（Message acknowledgment 记ACK）后才将该消息从Queue中移除。

如果RabbitMQ没有收到回执并检测到消费者的RabbitMQ连接断开，则RabbitMQ会将该消息发送给其他消费者（如果存在多个消费者）进行处理。这里不存在timeout，一个消费者处理消息时间再长也不会导致该消息被发送给其他消费者，除非它的RabbitMQ连接断开。

这里会产生另外一个问题，如果我们的开发人员在处理完业务逻辑后，忘记发送回执给RabbitMQ，这将会导致严重的bug——Queue中堆积的消息会越来越多。消费者重启后会重复消费这些消息并重复执行业务逻辑。

另外publish message 是没有ACK的。

# **Message durability**

如果我们希望即使在RabbitMQ服务重启的情况下，也不会丢失消息，我们可以将Queue与Message都设置为可持久化的（durable），这样可以保证绝大部分情况下我们的RabbitMQ消息不会丢失。但依然解决不了小概率丢失事件的发生（比如RabbitMQ服务器已经接收到生产者的消息，但还没来得及持久化该消息时RabbitMQ服务器就断电了），如果我们需要对这种小概率事件也要管理起来，那么我们要用到事务。由于这里仅为RabbitMQ的简单介绍，所以这里将不讲解RabbitMQ相关的事务。

# **Prefetch count**
消费者在开启acknowledge的情况下，对接收到的消息可以根据业务的需要异步对消息进行确认。

然而在实际使用过程中，由于消费者自身处理能力有限，从rabbitmq获取一定数量的消息后，希望rabbitmq不再将队列中的消息推送过来，当对消息处理完后（即对消息进行了ack，并且有能力处理更多的消息）再接收来自队列的消息。在这种场景下，我们可以通过设置basic.qos信令中的prefetch_count来达到这种效果。
先直观的看看设置了prefetch_count的效果，：

1） 对比测试：两个消费者都订阅同一队列，no_ack均设置为false即开启acknowledge机制，且均未设置prefetch_count，向队列发布5条消息

结果：不管消息是否被ack，rabbitmq会轮流向两个消费者投递消息，第一个消费者收到"1"，“3”，“5"三条消息， 第二个消费者收到"2”，"4"两条消息。

2）prefetch_count设置测试：两个消费者都订阅同一队列，开启acknowledge机制，第一个消费者prefetch_count设置为1，另一个消费者未设置prefetch_count，同样向队列发布5条消息

结果：rabbitmq向第一个消费者投递了一条消息后，消费者未对该消息进行ack，rabbitmq不会再向该消费者投递消息，剩下的四条消息均投递给了第二个消费者


# **细节阐明**

## 使用ACK确认Message的正确传递

默认情况下，如果Message 已经被某个Consumer正确的接收到了，那么该Message就会被从Queue中移除。当然也可以让同一个Message发送到很多的Consumer。

如果一个Queue没被任何的Consumer Subscribe（订阅），当有数据到达时，这个数据会被cache，不会被丢弃。当有Consumer时，这个数据会被立即发送到这个Consumer。这个数据被Consumer正确收到时，这个数据就被从Queue中删除。

那么什么是正确收到呢？通过ACK。每个Message都要被acknowledged（确认，ACK）。我们可以显示的在程序中去ACK，也可以自动的ACK。如果有数据没有被ACK，那么RabbitMQ Server会把这个信息发送到下一个Consumer。

如果这个APP有bug，忘记了ACK，那么RabbitMQ Server不会再发送数据给它，因为Server认为这个Consumer处理能力有限。而且ACK的机制可以起到限流的作用（Benefitto throttling）：在Consumer处理完成数据后发送ACK，甚至在额外的延时后发送ACK，将有效的balance Consumer的load。

当然对于实际的例子，比如我们可能会对某些数据进行merge，比如merge 4s内的数据，然后sleep 4s后再获取数据。特别是在监听系统的state，我们不希望所有的state实时的传递上去，而是希望有一定的延时。这样可以减少某些IO，而且终端用户也不会感觉到。

## Reject a message

有两种方式，第一种的Reject可以让RabbitMQ Server将该Message 发送到下一个Consumer。第二种是从Queue中立即删除该Message。

## Creating a queue

Consumer和Procuder都可以通过 `queue.declare `创建queue。对于某个Channel来说，Consumer不能declare一个queue，却订阅其他的queue。当然也可以创建私有的queue。这样只有APP本身才可以使用这个queue。queue也可以自动删除，被标为auto-delete的queue在最后一个Consumer unsubscribe后就会被自动删除。那么如果是创建一个已经存在的queue呢？那么不会有任何的影响。需要注意的是没有任何的影响，也就是说第二次创建如果参数和第一次不一样，那么该操作虽然成功，但是queue的属性并不会被修改。

那么谁应该负责创建这个queue呢？是Consumer，还是Producer？

如果queue不存在，当然Consumer不会得到任何的Message。那么Producer Publish的Message会被丢弃。所以，还是为了数据不丢失，Consumer和Producer都try to create the queue！反正不管怎么样，这个接口都不会出问题。

queue对load balance的处理是完美的。对于多个Consumer来说，RabbitMQ 使用循环的方式（round-robin）的方式均衡的发送给不同的Consumer。

## Exchanges

从架构图可以看出，Procuder Publish的Message进入了Exchange。接着通过"routing keys”， RabbitMQ会找到应该把这个Message放到哪个queue里。queue也是通过这个routing keys来做的绑定。

有三种类型的Exchanges：direct, fanout,topic。 每个实现了不同的路由算法（routing algorithm）。

- **Direct exchange：**如果 routing key 匹配，那么Message就会被传递到相应的queue中。其实在queue创建时，它会自动的以queue的名字作为routing key来绑定那个exchange。
- **Fanout exchange：** 会向响应的queue广播。
- **Topic exchange：**对key进行模式匹配，比如ab可以传递到所有ab的queue。

## Virtual hosts

每个virtual host本质上都是一个RabbitMQ Server，拥有它自己的queue，exchagne，和bings rule等等。这保证了你可以在多个不同的Application中使用RabbitMQ。





