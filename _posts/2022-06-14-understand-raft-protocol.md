---
layout:     post
title:      "理解分布式一致性:Raft协议"
subtitle:   "Understanding Distributed Consistency: Raft Protocol"
date:       2022-06-14 00:00:00
author:     "pz"
catalog: false
header-style: text
tags:
  - Java后端
  - 分布式
---

# 理解分布式一致性:Raft协议

> 在分布式系统中，分布式一致性是一个非常重要的概念，它是指分布式系统的各个服务器都保持一个统一的状态（数据）。但是在分布式系统中，通常由于网络，系统状态等原因会导致某些服务不可用或者不可靠。==这就需要一种分布式一致性的协议来确保系统在某些服务失败的情况下任然整体可用。==

>Raft协议是受到Paxos的影响而产生的，相对于Paxos而言，Raft协议更加简单易懂。

## 分布式一致性

> 假如我们有一个单节点的服务节点A，这个单节点的服务只是用来存储一个字母。同时我们还有一个客户端向这个服务发起更新数据的请求。
>
> 对于单节点的分布式一致性来说，服务响应客户端的更新请求即可。但是当我们有多个服务节点的情况下会怎么样呢？

<img src="../../../../../Download/Typora/image/image-20220614221017691.png" alt="image-20220614221017691" style="zoom:80%;" />

Raft协议就是保证多个服务器节点数据一致性的协议。

### Raft工作流程

Raft协议中，一个服务器的节点可以是以下三种状态中的任意一个：

* Follower 状态：跟随者，被动接收数据。我们用实心圆表示。

  <img src="../../../../../Download/Typora/image/image-20220614165451453.png" alt="image-20220614165451453" style="zoom:50%;" />

* Candidate 状态：候选人，可以被选做Leader。我们用实心圆+虚线边框表示。

  <img src="../../../../../Download/Typora/image/image-20220614165718706.png" alt="image-20220614165718706" style="zoom:50%;" />

* Leader 状态：领导者，处理所有客户端交互，日志复制等，一般一次只有一个Leader. 我们用实心圆+实线边框表示。

  <img src="../../../../../Download/Typora/image/image-20220614165701121.png" alt="image-20220614165701121" style="zoom:50%;" />

## Leader选举

所有的节点都是从Follower状态开始的。

<img src="../../../../../Download/Typora/image/image-20220614170042271.png" alt="image-20220614170042271" style="zoom: 67%;" />

如果Follower在一定的时间里面没有收到选举请求或者Leader节点的回复，Follower则会转变为Candidate。

<img src="../../../../../Download/Typora/image/image-20220614170310884.png" alt="image-20220614170310884" style="zoom:67%;" />

Candidate会发送选举请求给所有的其他节点，收到选举请求的其他节点会反馈回Candidate，当Candidate收到的所有响应数目大于n/2 时，Candidate会认为绝大多数节点已经选我作为Leader了，这时候Candidate就会转变为Leader。接下来所有的数据变化都会经由Leader发起。

<img src="../../../../../Download/Typora/image/image-20220614171107673.png" alt="image-20220614171107673" style="zoom:67%;" />

## 日志复制流程

在Raft系统中，所有的数据变化都是以日志记录的形式添加到服务节点中的。服务节点会不断的读取日志记录，并将日志记录更新到服务节点的数据中。日志记录最开始的状态是uncommited，更新之后状态则变为commited。

为了实现所有服务节点的一致性更新，步骤如下

* client 发送数据更改请求到Leader

  <img src="../../../../../Download/Typora/image/image-20220614172113713.png" alt="image-20220614172113713" style="zoom:80%;" />

* Leader复制日志记录到Follower节点

  <img src="../../../../../Download/Typora/image/image-20220614172640098.png" alt="image-20220614172640098" style="zoom:80%;" />

* Leader等待大多数节点完成复制日志记录

* Leader节点commit 当前日志记录，并更新Leader节点的数据

  <img src="../../../../../Download/Typora/image/image-20220614214955315.png" alt="image-20220614214955315" style="zoom:80%;" />

* Leader通知Follower节点该日志记录已经commit

* follower节点commit该日志记录

  <img src="../../../../../Download/Typora/image/image-20220614215604230.png" alt="image-20220614215604230" style="zoom:80%;" />

* 整个分布式系统实现了数据一致性

## term选举周期

在Raft 协议中，有一个term的概念。term是一个选举周期，一个term周期只会产生一个Leader，term连续递增。

## timeout

在Raft协议中，为了保证选举和数据更新的顺利进行，规定了两种类型的timeout:`选举timeout`和`心跳timeout`。

## 选举和选举timeout

* 每个term开始时，会重置选举timeout。在一个term中，Follower会等待timeout的时间，如果超出这个时间还没有得到其他节点的选举请求，Follower会主动转变为Candidate，并且term+1，意味着开启了新的选举周期。

  选举timeout是150-300ms之间的一个随机数，之所以随机产生timeout，是为了避免同时产生多个Candidate的情况。当Follower转变为Candidate之后，term加1，然后开始新一轮的选举。

  Candidate首先会将自己的Vote Count加1，然会发送请求选举的消息给其他节点。

<img src="../../../../../Download/Typora/image/image-20220614221536161.png" alt="image-20220614221536161" style="zoom:80%;" />

<img src="../../../../../Download/Typora/image/image-20220614221859229.png" alt="image-20220614221859229" style="zoom:80%;" />

* 接收节点首先会比较term的大小，如果自己的term小于Candidate的term，则更新自己的term和Candidate的term保持一致，并重置timeout。如果接收节点在这个term中还没有做任何选举，则会返回选举响应消息给Candidate节点

<img src="../../../../../Download/Typora/image/image-20220614223008466.png" alt="image-20220614223008466" style="zoom:80%;" />

* Candidate 节点收到大部分节点的选举响应之后，会变成Leader 节点。

<img src="../../../../../Download/Typora/image/image-20220614223206140.png" alt="image-20220614223206140" style="zoom:80%;" />

* 一个选举周期完成，接下来Leader 发送更新日志给Follower节点，进入日志更新阶段。

## 选举分裂

值得注意的是Candidate只有得到超出n/2个节点的选举响应才能变为Leader节点。如果两个Follower节点同时变为Candidate节点，则会产生选举分裂的问题。

现在假设共有4个节点，其中两个节点同时变成Candidate节点，并向其余两个节点发送选举请求：

<img src="../../../../../Download/Typora/image/image-20220614224810156.png" alt="image-20220614224810156" style="zoom:80%;" />

节点B，C成为Candidate节点并行向节点A，D发送选举请求。

<img src="../../../../../Download/Typora/image/image-20220614224834782.png" alt="image-20220614224834782" style="zoom:80%;" />

节点A，D分别响应节点B，C的请求，这时候两个Candidate节点由于得到的Vote都是2，不满足大于n/2的条件，则其不能转变为Leader节点，继续等待timeout至新的term开始并开启新一轮的选举，只到符合条件为止。

<img src="../../../../../Download/Typora/image/image-20220614225028217.png" alt="image-20220614225028217" style="zoom:80%;" />

## 日志复制和心跳timeout

当系统进入到日志复制阶段，Leader节点会以心跳timeout的节奏向Follower节点发送日志记录，并且需要确保所有节点都能接受到完整的日志记录。

* 客户发送set 5 给Leader， 在下一个心跳timeout，Leader将set 5的日志记录发给Follower。

  <img src="../../../../../Download/Typora/image/image-20220614230050490.png" alt="image-20220614230050490" style="zoom:80%;" />

* Leader 收到大部分节点的ack 响应之后，commit 该日志记录

  <img src="../../../../../Download/Typora/image/image-20220614230215600.png" alt="image-20220614230215600" style="zoom:80%;" />

* Leader通知Client已经提交该日志记录，同时通知Follower 提交该日志记录

  <img src="../../../../../Download/Typora/image/image-20220614230521336.png" alt="image-20220614230521336" style="zoom:80%;" />
