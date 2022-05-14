---
layout: post
title: "浏览器访问服务器流程解析"
subtitle: 'Browser access server process analysis'
author: "pz"
header-style: text
tags:
  - Java后端

---

# 浏览器访问服务器流程简略图

当在浏览器中输入域名，敲下回车后，不一会儿浏览器就会显示我们想要的界面。本文将简单介绍这其中经历了什么过程。

注意：以下分析基于**HTTP**请求，并且Web容器使用**Tomcat**，后端框架使用**SSM**

![image-20211130170837411](/img/image/image-20211130170837411.png)

> 从图中可以知道，当用户发起http请求后，客户端与服务端会通过三次握手建立连接，并发送http请求数据包到tomcat，进过一系列流程处理之后，tomcat返回响应数据包到浏览器，浏览器渲染并呈现给用户。

Web 应用处理完请求并将结果返回给 Web 容器后，容器会将响应结果返回给客户端，这是上面流程的逆过程。浏览器收到响应结果后，会对结果进行解析和渲染。这样我们就能看到浏览器给我们显示的网页了。20201210113745.png

![](/img/image/20201210113745.png)

# URL解析

## 地址解析

浏览器会根据你的输入来判断该输入是一条合法的URL，还是需要被搜索的关键词。并且根据你输入的内容进行自动完成、字符编码等操作。

## 其他操作

目前大部分浏览器都会**强制客户端使用HTTPS协议（HTTP+SSL/TLS）**以保证信息传输的安全性。同时还会进行一些额外的操作，比如安全检查、访问限制等。

## 缓存检查

有时候博客在gitee上进行了更新，但是通过谷歌浏览器查看博客时，仍是更新前的博客，这是因为浏览器中缓存了之前的博客界面。

浏览器会先检测是否缓存了目标URL的页面，如果有且缓存未过期，则直接展示缓存页面，无需再向服务器进行请求。

<img src="/img/image/image-20211130174310851.png" alt="image-20211130174310851" style="zoom:67%;" />

## DNS解析

**DNS解析是寻找所需要的资源的IP地址的过程**。因为互联网中每一台连网的机器都有**唯一IP作为标识**，但是它是一串数字，记忆太过困难。所以就需要将网址和IP地址进行转换，也就是DNS解析。其具体步骤如下。

<img src="/img/image/image-20211130174523666.png" alt="image-20211130174523666" style="zoom:80%;" />

### 1、查询缓存

我们的浏览器、操作系统、路由器都会缓存一些URL对应的IP地址，统称为**DNS高速缓存**。这是为了加快DNS解析速度，使得不必每次都到根域名服务器中去查询。

### 2、递归解析

输入`www.baidu.com`网址后，首先在高速缓存中查找，没找到去根域名服务器查找，没有再去`com`顶级域名服务器查找，依次类推，直到找到IP地址，然后把它记录在本地告诉缓存中，供下次使用。

大致过程就是`.`-> `.com` ->`baidu.com.` -> `www.baidu.com.`

其中`.`代表根域名服务器。

### 3、DNS负载均衡

访问`baidu.com`的时候，每次响应的可能并非是同一个服务器（IP地址不同），一般大公司都有成百上千台服务器来支撑访问，DNS可以返回一个**合适的机器的IP**给用户，例如可以**根据每台机器的负载量，该机器离用户地理位置的距离**等等，这种过程就是DNS负载均衡。

# 建立TCP连接

TCP/IP 分为四层，在发送数据时，**每层都要对数据进行封装**

<img src="/img/image/image-20211130174800466.png" alt="image-20211130174800466" style="zoom:67%;" />

TCP提供一种**面向连接的，可靠的字节流**服务，是一种可靠传输。接下来将会讲解TCP的**首部、三次握手与四次挥手**。

## TCP的首部

![image-20211205152822139](/img/image/image-20211205152822139.png)

- **源端口**（2个字节）：源端口和IP地址的作用是标识**报文的发送地址和返回地址**
- **目的端口**：端口指明**接收方**计算机上的应用程序接口
  - ==TCP报头中的源端口号和目的端口号同IP数据报中的源IP与目的IP**唯一确定**一条TCP连接==

- **序号**：是TCP可靠传输的**关键部分**
  - 序号是该报文段发送的数据组的第一个字节的序号。==在TCP传送的流中，每一个字节都有一个序号==
    - 比如一个报文段的序号为300，报文段数据部分共有100字节，则**下一个报文段**的序号为400。所以序号确保了TCP传输的有序性

- **确认号：ack，用于指明下一个期待收到的字节序号**，表明该序号之前的所有数据已经正确无误的收到
  - **确认号只有当ACK标志为1时才有效**。比如建立连接时，SYN报文的ACK标志位为0

- **首部长度/数据偏移**：占4个bit位，它指出**TCP报文的数据距离TCP报文段的起始处有多远**
- **保留**：占6位，保留今后使用，但**目前应都位0**

- **控制位：URG ACK PSH RST SYN FIN**，共6个，每一个标志位表示一个控制功能
  - URG：紧急。当URG=1时，表明紧急指针字段有效。告诉系统**此报文段中有紧急数据**
  - ==ACK==：确认。**当ACK=1时，确认号字段才有效**。TCP规定，在连接建立后所有报文的传输都必须把ACK置1
  - PSH：推送。当两个应用进程进行交互式通信时，有时在一端的应用进程希望在键入一个命令后立即就能收到对方的响应，这时候就将PSH=1
  - RST：复位。当RST=1，表明TCP连接中出现严重差错，**必须释放连接，然后再重新建立连接**
  - ==SYN==：同步，在连接建立时用来同步序号。当SYN=1，ACK=0，表明是连接请求报文，若同意连接，则响应报文中应该使SYN=1，ACK=1
  - FIN：终止，用来释放连接。当FIN=1，表明此报文的发送方的数据已经发送完毕，并且要求释放

- **窗口**：**滑动窗口**大小，用来告知发送端**接受端的缓存大小**，以此控制发送端发送数据的速率，从而达到流量控制。窗口大小时一个16bits字段，因而窗口大小最大为65535

- **校验和**：奇偶校验，此校验和是对整个的 TCP 报文段，包括 TCP 头部和 TCP 数据，以 16 位字进行计算所得。由发送端计算和存储，并由接收端进行验证

- **紧急指针**：只有当 **URG 标志置 1 时紧急指针才有效**。紧急指针是一个正的偏移量，和顺序号字段中的值相加表示紧急数据最后一个字节的序号。 TCP 的紧急方式是发送端向另一端发送紧急数据的一种方式
- **选项和填充**：最常见的可选字段是最长报文大小，又称为MSS（Maximum Segment Size），每个连接方通常都在通信的第一个报文段（为建立连接而设置SYN标志为1的那个段）中指明这个选项，它表示本端所能接受的最大报文段的长度。选项长度不一定是32位的整数倍，所以要加填充位，即在这个字段中加入额外的零，以保证TCP头是32的整数倍
- **数据部分**： TCP 报文段中的数据部分是可选的。**在一个连接建立和一个连接终止时，双方交换的报文段仅有 TCP 首部**。如果一方没有数据要发送，也使用没有任何数据的首部来确认收到的数据。在处理超时的许多情况中，也会发送不带任何数据的报文段。

## 三次握手

<img src="/img/image/image-20211205153959927.png" alt="image-20211205153959927" style="zoom:67%;" />

==握手过程中传送的包里不包含数据==，三次握手完毕后，客户端与服务器才正式开始传送数据。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP 连接都将被一直保持下去。

**为什么是三次握手？两次不行吗？四次不行吗？**

>  **为什么不是两次握手？**
>
> 这是为了**避免服务器建立无用连接**（客户端服务器建立连接后，却不传输数据）
>
> 如果只进行两次握手，如果客户端向服务器第一次发送的建立连接的请求因为某原因，**兜兜转转绕了一大圈才到达服务器**。这期间客户端因为未收到服务器的响应，就会再次发送连接请求，这时服务器收到了，向客户端发送连接请求后，连接便建立了。然后数据传输完毕后，释放连接。**这时刚刚兜兜转转一大圈的建立连接的请求到了服务器**，服务器收到后再次向客户端发送请求，发送后又建立了连接，但是建立连接后客户端没有再理会服务器，客户端与服务器之间没有传输数据，此时服务器的资源就会被浪费
>
> <img src="/img/image/image-20211205154532854.png" alt="image-20211205154532854" style="zoom:50%;" />
>
> **为什么不是四次握手？**
>
> **因为通信不可能100%可靠**（红军蓝军约定）， 而上面的三次握手已经做好了通信的准备工作， **再增加握手， 并不能显著提高可靠性**，所以只需要三次握手就足够了
>
> 这里简单介绍一下**红军蓝军约定**
>
> 红军和蓝军都想消灭一波敌人，但是单凭他们一个军队的力量都不足以消灭这波敌人，因此他们想到了一起合作，于是红军向蓝军发了一封电报，内容是约定好早上8点一起向敌军进攻，由于他们不确定蓝军是否一定能收到电报, 所以只有收到蓝军的回复之后才会进行进攻，而蓝军也是同样的想法，因为他们不确定红军一定能收到自己的回复而在约定好的时间发动进攻，所以他们只有收到红军的回复后才发动进攻….
>
> 问怎样才能保证这次战役一定胜利呢？答案是不可能的，因为**双方都对于自己发出的消息对方是否一定接收得到存在质疑**，所以，这样的通信将一直进行下去，结果将是使胜利的几率一直接近100%，但是却永远达不到100%。

## 四次挥手

<img src="/img/image/image-20211205154907983.png" alt="image-20211205154907983" style="zoom:67%;" />

- 第一次挥手
  - 客户端发送一个**FIN=1**，用来关闭客户端到服务器的数据传送，此后客户端不会再向服务器发送数据(当然，在fin包之前发送出去的数据，如果没有收到对应的ack确认报文，客户端依然会重发这些数据)，但是，此时客户端还可以接受数据。 FIN=1，其序列号为seq=u（等于前面已经传送过来的数据的最后一个字节的序号加1），此时，**客户端进入FIN-WAIT-1**（终止等待1）状态。 TCP规定，FIN报文段即使不携带数据，也要消耗一个序号
- 第二次挥手
  - 服务器收到FIN包后，发送一个ACK给对方并且带上自己的序列号seq，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号）。此时，**服务端就进入了CLOSE-WAIT**（关闭等待）状态。TCP服务器通知高层的应用进程，客户端向服务器的方向就释放了，这时候处于半关闭状态，即客户端已经没有数据要发送了，**但是服务器若发送数据，客户端依然要接受**。这个状态还要持续一段时间，也就是整个CLOSE-WAIT状态持续的时间
  - 此时，**客户端就进入FIN-WAIT-2**（终止等待2）状态，等待服务器发送连接释放报文（在这之前还需要接受服务器发送的最后的数据）
- 第三次挥手
  - 服务器发送一个FIN=1，用来关闭服务器到客户端的数据传送，也就是通知客户端，可以真正地释放连接了。由于在半关闭状态，服务器很可能又发送了一些数据，假定此时的序列号为seq=w，此时，**服务器就进入了LAST-ACK**（最后确认）状态，等待客户端的确认
- **第四次挥手**
  - 客户端收到FIN后，发送一个ACK=1给服务器，确认序号为收到序号+1，此时，**客户端就进入了TIME-WAIT**（时间等待）状态。注意此时TCP连接还没有释放，**必须经过2MSL（最长报文段寿命）的时间后**，当客户端撤销相应的TCB后，才进入CLOSED状态
  - **服务器只要收到了客户端发出的确认，立即进入CLOSED状态**。同样，撤销TCB后，就结束了这次的TCP连接。可以看到，服务器结束TCP连接的时间要比客户端早一些

> **为什么客户端最后还要等待2MSL?**
>
> **确保第四次挥手服务器能够收到，同时使失效的连接请求从网络中消失**\
>
> 
>
> **MSL**是Maximum Segment Lifetime英文的缩写，中文可以译为**报文最大生存时间**，他是任何报文在网络上存在的最长时间，超过这个时间报文将被丢弃。
>
> - **保证客户端发送的最后一个ACK报文能够到达服务器**，因为这个ACK报文可能丢失。站在服务器的角度看来，我已经发送了FIN+ACK报文请求断开了，客户端还没有给我回应，应该是我发送的请求断开报文它没有收到，**于是服务器又会重新发送一次**，而客户端就能在这个2MSL时间段内收到这个重传的报文，接着给出回应报文，并且会**重启2MSL计时器**
> - 防止类似与三次握手中提到了的**已经失效的连接请求报文段**出现在本连接中。客户端发送完最后一个确认报文后，在这个2MSL时间中，就可以**使本连接持续的时间内所产生的所有报文段都从网络中消失**（最长生存MSL）。**这样新的连接中不会出现旧连接的请求报文**

## 为什么建立连接是三次握手，关闭连接确是四次挥手

- 建立连接的时候， 服务器在LISTEN状态下，收到建立连接请求的SYN报文后，**把ACK和SYN放在一个报文里发送给客户端**
- 关闭连接时，服务器收到对方的FIN报文时，仅仅表示对方不再发送数据了但是还能接收数据，而自己也未必全部数据都发送给对方了，所以己方可以立即关闭，也可以发送一些数据给对方后，再发送FIN报文给对方来表示同意现在关闭连接，因此，**己方ACK和FIN一般都会分开发送，从而导致多了一次**

# 发送HTTPS请求

## HTTP简介

HTTP(HyperText Transfer Protocol)，超文本传输协议,**HTTP协议是用于客户端和服务器端之间的通信**，用于客户端和服务器端之间的通信有HTTP协议和TCP/IP协议族在内的其他众多的协议。

* **请求访问文本或图片等资源的一方**，我们叫做客户端；

* 负责接收，**提供响应的一方**称为服务器端。

> 超文本指的是HTML，css，JavaScript和图片等，HTTP的出现是为了接收和发布HTML页面，经过不断的发展也可以用于接收一些音频，视频，文件等内容。

**Client客户端请求Server服务端，Server服务端响应给Client客户端。**HTTP是基于客户端/服务端的架构模型，浏览器或其他任何客户端都可以用HTTP协议的，通过URL地址向HTTP的服务器即Web服务器发送所有请求，Web服务器端在接收到请求后会做出反应，响应给对方，就是向客户端回传响应的信息。

<img src="/img/image/image-20211205160332113.png" alt="image-20211205160332113" style="zoom:67%;" />

注意：在使用HTTP协议的时候，我们一端必定是客户端，另一端必定是服务器端。

==有时候服务端和客户端是可以互换的，如果要决定谁是服务器端和谁是客户端，单就一条通信路线来讲，是可以确定的，因为由HTTP协议就可以区分谁是客户端，和谁是服务器端了。==

### HTTP的特点

支持客户端、服务器端模式，简单快速，客户端向服务器端请求服务时，只需传送请求方法和路径，灵活，HTTP允许传输任意类型的数据对象，无连接，限制每次连接只处理一个请求，无状态，HTTP协议是无状态协议，指明协议对于事务处理没有记忆能力。

* HTTP都是由客户端发起请求的，并且由服务器端回应响应消息的。
* 灵活，我们知道允许可以任何类型的数据对象，包括音频，视频，图片，文件等等。
* 无状态，HTTP就是说，每次HTTP请求都是独立的，任何两个请求之间没有必然的联系。
* 无连接的，每次服务器在处理完客户端的请求后，并收到客户的应答后，就断开了通信，当客户端再次发送请求时就是一个新的连接，采用这种方式可以节省传输时间。

> **这是HTTP/1.0版的主要缺点，**每个TCP连接只能发送一个请求，发送数据完毕后，连接就关闭了，如果还要请求就必须要新建一个请求连接。
>
> HTTP是一种不保存状态，无状态协议，协议对于发送过来的请求或是响应都不做持久化处理。
>
> HTTP1.1虽然是无状态协议，但是为了实现期望的保持状态功能，于是引入了Cookie技术，有了Cookie，和HTTP协议通信，就可以管理状态了。
>
> 
>
> **TCP连接的新建成本很高，因为需要客户端和服务器端三次握手。**
>
> **交流的简单流程：**客户端发起连接，客户端发起请求，服务器端响应请求，服务器端关闭连接。
>
> HTTP、1.1版本是最流行的版本，可以持久连接，TCP连接默认不关闭，可以被多个请求复用，只有在一段时间内，没有请求，就可以自动关闭。
>
> ```js
> // 不用声明：
> Connection: keep-alive
> 
> // 发送关闭
> Connection: close
> // 要求服务器关闭TCP连接
> ```

<img src="/img/image/image-20211205160736832.png" alt="image-20211205160736832" style="zoom: 67%;" />

<img src="/img/image/image-20211205160801893.png" alt="image-20211205160801893" style="zoom:67%;" />

### HTTP的消息结构

我们可以把HTTP协议当做是一种客户端和服务器端相互沟通交流的语言，一个HTTP客户端在向服务器发送请求时会携带请求的消息，服务器端会根据客户端发送过来的请求消息了解客户端的需求，从而服务器端做出响应，发送响应消息给客户端。

**请求消息的结构：**一个请求消息是由**请求行，请求头字段，一个空行和消息主体(响应消息的承载数据)**构成。

**客户端：发送请求**

客户端发送给某个HTTP服务器端的请求报文中的内容

```json
GET/HTTP/1.1
Host: hackr.jp
```

```json

HTTP/1.1 200 OK
Date: Tue, 10 Jul ...
Content.Length: 362
Content.Type: text/html
<html>
...
```

<img src="/img/image/image-20211205161325436.png" alt="image-20211205161325436" style="zoom:80%;" />

GET，Request Method（请求方法），Request URL（为请求的url的地址），Status Code（状态码），Remote Address（地址）。

> HTTP是基于TCP/IP协议的应用层协议，不涉及数据包传输，规定了客户端和服务器端之间的通信方式，默认使用80端口，就如同他俩交流的语言。
>
> HTTP1.0的发布，任何格式的内容都可以发送了，不仅可以发送文件，图片，视频，二进制文件等。

**HTTP发送请求的例子：**

<img src="/img/image/image-20211205161623416.png" alt="image-20211205161623416" style="zoom:80%;" />

**服务器回应消息格式：**

<img src="/img/image/image-20211205161637944.png" alt="image-20211205161637944" style="zoom:80%;" />

**响应头：**

Server为服务器的名称，Location为通知客户端新的资源位置，Content-Type响应数据的类型，Content-Encoding为响应数据的编码格式。

<img src="/img/image/image-20211205161808294.png" alt="image-20211205161808294" style="zoom:67%;" />

头部信息回应的是ASCII码，后面的数据可以是任何形式，服务器返回信息的时候，告诉客户端数据是什么格式的，由Content-Type字段体现。

**Content-Type的字段值：**

```js
text/plain
text/html
text/css
image/jpeg
// 上面的图片返回的是
image/png
image/svg+xml
audio/mp4
video/mp4
application/javascript
application/pdf
application/zip
application/atom+xml
```

<img src="/img/image/image-20211205161937052.png" alt="image-20211205161937052" style="zoom:67%;" />



**分析一下就是，协议版本，状态码，状态描述。**

在尾部使用分号，添加参数，类型表示，发送的是网页，编码为utf-8

```js
Content-Type: text/html; charset=utf-8
```

Accept字段声明自己可以接受哪些数据格式：

```json
Accept: */*
```

### 请求消息

<img src="/img/image/image-20211205162108906.png" alt="image-20211205162108906" style="zoom:67%;" />

```json
GET /index.htm HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Host: example.com
Accept-Language: en-us
Accept-Encoding: gzip, deflate
```

Accept为客户端接受哪些Mine类型，Accept-Encoding支持的编码类型，Accept-Language为可接受的语言，User-Agent为一个标识客户端的字符串。

字符串index.htm指明了请求访问的资源对象，也叫做请求URL，后面的是HTTP/1.1，**为HTTP版本号，表示客户端使用的HTTP版本协议。**

URL为请求的URL地址，协议版本为代表在向服务器发送请求时，使用的协议以及其版本，请求头部为HTTP请求头部包含若干个属性，格式：

“属性名”：“属性值”，服务器端可以据此获取客户端请求的相关信息。

客户端请求用Accept-Encoding字段说明自己可以接受哪些压缩方式

请求行为请求消息的第一行，它说明了请求方法，**资源标示****符**，HTTP版本，如下：

请求URI定位资源：HTTP协议使用URI定位互联网上的资源。

<img src="/img/image/image-20211205162432054.png" alt="image-20211205162432054" style="zoom:80%;" />

URI,URL,URN是用来识别，定位和命名互联网上的资源。

<img src="/img/image/image-20211205162459613.png" alt="image-20211205162459613" style="zoom: 67%;" />

<img src="/img/image/image-20211205162524807.png" alt="image-20211205162524807" style="zoom:67%;" />

<img src="/img/image/image-20211205162630887.png" alt="image-20211205162630887" style="zoom:67%;" />

```json

URI：

Uniform Resource Identifier，统一资源标识符

URL：

Uniform Resource Locator，统一资源定位符

URN：

Uniform Resource Name，统一资源名称
```

<img src="/img/image/image-20211205162724230.png" alt="image-20211205162724230" style="zoom:67%;" />

<img src="/img/image/image-20211205162755689.png" alt="image-20211205162755689" style="zoom:67%;" />

由HTTP版本，状态码，状态描述文字构成：

```json
由HTTP版本，状态码，状态描述文字构成：
```

```json
GET /hello.htm HTTP/1.1
请求访问某台HTTP服务器上的index.htm页面资源
```

请求报文是由**请求方法，请求URL，协议版本，可选的请求首部字段和内容实体**构成的。

### 请求报文的解构

**消息报头**

<img src="/img/image/image-20211205162929687.png" alt="image-20211205162929687" style="zoom:50%;" />

<img src="/img/image/image-20211205162939119.png" alt="image-20211205162939119" style="zoom: 67%;" />

响应报文由协议版本，状态码，响应的首部字段，以及实体主体构成。

### HTTP/1.1中请求方法

HTTP的请求方法有很多，但是最常见的两种请求方法是GET和POST

提交HTML表单或上传文件

<img src="/img/image/image-20211205163034953.png" alt="image-20211205163034953" style="zoom:67%;" />

POST：传输实体主体 

POST 方法用来传输实体的主体。 

<img src="/img/image/image-20211205163106751.png" alt="image-20211205163106751" style="zoom:67%;" />

PUT：传输文件 

PUT 方法用来传输文件。

<img src="/img/image/image-20211205163123195.png" alt="image-20211205163123195" style="zoom:67%;" />

HEAD：获得报文首部 

DELETE 方法用来删除文件，是与 PUT 相反的方法。

OPTIONS：询问支持的方法 

OPTIONS 方法用来查询针对请求 URI 指定的资源支持的方法

```json
1.GET为获取资源数据
get方法用于请求指定的页面信息，并返回请求消息的主体

2.POST为提交资源数据
post方法用于向指定的资源提交数据

3.PUT为更新资源数据
4.DELETE为删除资源数据
5.HEAD为读取资源的元数据
6.OPTIONS为读取资源多支持的所有请求方法
7.TRACE为回显服务器收到额请求
8.CONNECT为保留将来使用
```

**HTTP超文本传输协议是一个基于请求与响应模式的，无状态的，应用层的协议，常基于TCP的连接方式。**HTTP表示通过HTTP协议定位网络资源，host表示合法的Internet主机域名或者ip地址，port指定为端口号。

第一行：方法，URL，协议版本

第二行：请求首部字段

第三行：内容实体

**客户端的请求消息：**

**请求方法，URL，协议版本为请求行，请求头部，头部字段名，值，请求数据，**一个HTTP请求的请求消息包括请求行，请求头部，空行和请求数据。

**服务器端响应消息：**

状态行，消息报头，空行，响应正文，这是一个HTTP响应的响应消息。

状态行，HTTP/1.1为表示使用的协议是HTTP的版本，我们可以看到状态码常见返回“200 0k”代表是服务器成功响应了此次请求，消息报头，与HTTP请求头部一样，格式为“属性名:属性值"，客户端可以根据此获取服务器端响应的信息，响应正文是服务器返回响应的东西给到客户端，也是客户端想要的东西。

**超文本传输协议**（英文：HyperText Transfer Protocol，缩写：HTTP）是互联网上应用最为广泛的一种网络协 议。设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法。通过HTTP或者HTTPS协议请求的资源由 统一资源标识符（Uniform Resource Identifiers，URI）来标识。

### HTTP状态码

HTTP状态码是用来干什么的呢？它的重要作用是Web服务器用来告诉客户端，当前的网页请求发生什么事情了，或者是当前web服务器端的响应状态，**HTTP状态码是服务器端返回给客户端的。**

你比较常见的状态码有哪些呢？

**有200,301,302,304,404,500,403。**

我们最常见的状态码为200，状态码200表示服务器响应成功，服务器找到了客户端请求的内容，并将内容发送给了客户端。

我们程序员有时候也常见的500，状态码500表示程序错误，就是说请求的网页程序本身就报错了。

现在的浏览器会对状态码500做出一定的处理，所以在一般情况下会返回一个定制的错误页面。

状态码404表示服务器上没有该资源，或者说是服务器上没有找到客户端请求的资源，是最常见的请求错误码。

状态码302表示临时跳转。

url地址a可以向url地址b上跳转，但这并不意味着是永久性的，有可能过短时间就从url地址a跳转到地址c。

而状态码301代表的是永久性的重定向。

<img src="/img/image/image-20211205163421903.png" alt="image-20211205163421903" style="zoom: 80%;" />

2xx:

200为请求已经成功，202为服务器已经接受请求，但尚未处理，204为服务器成功处理了请求，但不需要返回如何实体内容。

304状态码，被请求的资源内容没有发生更改。

400为包含语法错误，无法被服务器解析，403为服务器已经接收请求，但是被拒绝执行，404请求失败。

500为服务器内部错误，无法处理请求，502为作为网关或者代理工作的服务器尝试执行请求时，从上游服务器接收到无效响应，504为作为网关或者代理工作的服务器尝试执行请求时，未能及时从上游服务器（URI标识出的服务器，例如HTTP、FTP、LDAP）或者辅助服务器（例如DNS）收到响应

200 OK 

请求被正确处理 

302 Found 

所请求的资源已暂时更改.通常会重定向到另一个 URL 

404 Not Found 

所请求的资源无法找到 

500 Internal Server Error 

服务器出现一般性错误 

### HTTP通信过程

本地服务器编写一个demo.html文档：

```html

<!DOCTYPE html>
<html>
 <head>
  <meta charset="UTF-8">
  <title>Document</title>
  <script type="text/javsscript" src="/js/jquery.js">
  </script>
 </head>
 <body>
 <img src="/images/01.jpg"><br>
 </body>
</html>
```

**消息头是在客户端发送请求时，向服务器传输的信息。**

Accept为告诉服务器接收哪些格式的文件，Accept-Encoding为接收的编码方式，Accept-Language为接收的语言，Cache-Control为是否使用缓存，Connection为连接方式，Cookie为向服务器发送的Cookie信息，Host为服务器域名。

Content-Length为服务器返回给客户端的内容的长度，Content-Type为服务器返回给客户端的内容的类型，Keep-Alive为缓存的时间，Server为服务器的相关信息。

HTTP过程中包含3个重要部分，**消息头，响应头和状态码。**

消息头中存放的信息是客户端告诉服务器能回传什么，就是客户端能接收的信息是什么类型的，如果客户端需要传值，则传值信息也会放在消息头中。

响应头中存放的信息是服务器告诉客户端返回的内容相关信息，包括回传内容的长度，内容和缓存的时间等。

状态码为服务器告诉客户端它的响应结果是什么。

### HTTP工作原理

<img src="/img/image/image-20211205163653797.png" alt="image-20211205163653797" style="zoom:67%;" />

## HTTPS简介

在HTTP的基础上再加一层TLS（传输层安全性协议）或者SSL（安全套接层），就构成了HTTPS协议。

为了应付HTTP是明文传输的缺点，容易被中间人窃听或者篡改，导致隐私和信息安全出现问题的解决方案。

<img src="/img/image/image-20211205190831423.png" alt="image-20211205190831423" style="zoom:48%;" />

从上图看出，SSL和TLS在应用层中“垫”了一层SSL/TLS ，安全协议。

### 逐步说明中间人攻击

### 明文传输被窃听

如果在通信链路上出现Hacker，由于通信内容都是明文可见，所以Hacker可以嗅探看这些，也可以篡改内容。

<img src="/img/image/image-20211205191004522.png" alt="image-20211205191004522" style="zoom:80%;" />

比如我们以前在访问网页时会发现某些网页中间底部或者顶部出现横栏广告。这就是被劫持之后的效果。

<img src="/img/image/image-20211205191058566.png" alt="image-20211205191058566" style="zoom:80%;" />

那么问题来了，怎么制止被窃听和篡改呢。第一时间我们会想到给报文数据加密阿。

### 加密解密

对明文的加密，相当于对原来看得懂的内容变成一段看不懂的内容。而揭秘，本质上就是一种逆运算。

加入对2个二进制数A和B进行异或运算得到结果C，那C和B再异或一次就会得到A。

![image-20211206112824731](/img/image/image-20211206112824731.png)

所谓**对称加密**，就是这个意思，可以看到加密解密同时运用同一个密钥B。

> 问：这个密钥需要让对方知道，如果直接传输就会被截获那不就暴露了吗？

答： 这时候就需要非对称加密了。

**非对称加密**加密指的是，加密和解密用的并不是同一把钥匙，

<img src="/img/image/image-20211206113011516.png" alt="image-20211206113011516" style="zoom:50%;" />

这就是非对称加密，任何人都可以通过拿到Bob公开的公钥对内容进行加密，然后只有Bob自己私有的钥匙才能解密还原出原来内容。

<img src="/img/image/image-20211206113141681.png" style="zoom:50%;" />

而HTTPS的加密解密方式结合了**对称加密**和**非对称加密**，由于非对称加密的性能低，因此我们用这种方式来对密钥（解开门的钥匙）进行非对称加密，防止被中间人猜出保证密钥不泄露。然后针对报文数据的加解密用的是对称加密。

问：密钥配送问题解决了，那怎么确定一直是不是都是Alice和Bob呢？

<img src="/img/image/image-20211206113422119.png" alt="image-20211206113422119" style="zoom:50%;" />

因为中间人是有可能冒充身份的，又不需要给证明。这就会出现，

- Alice以为此时传过来的密钥是Bob的，没料到是Hacker的，因此Hacker能解开数据报文的私钥，因此顺利地解开数据内容。
- 而中间人会在篡改内容之后用Bob给的密钥加密数据报文，发送给Bob。此时Bob也能解开，但是他意识不到这个数据报文遭到了Hacker篡改。

那么，具体而言，问题到底出现在哪里？

**就是在进行SSL握手时出现了信任问题，Alice误把Hacker当成了Bob，用了Hacker的公钥加密，从而落入圈套**

所以，我们需要解决的是信任问题，确保和提供公钥的是Bob。

### 认证问题

联系我们的实际生活，我们对于个人身份认证就是身份证。背后是政府信用。在网络世界里也是一样的，**数字签名** 就相当于身份证，如果有个大家都信任的组织CA（Certificate Authority）给每个人出证明，那Alice只要拿到这个证明，检查一下是不是CA制作的Bob证书就可以证明Bob是Bob。所以这个证书里边需要有两个重要的东西：Bob的公钥+CA做的数字签名。

所以第一点，数字签名解决了信任问题。

问：可否解释一下数字签名具体是通过什么措施验证公钥来源的

答：

1. 服务端把报文经过Hash处理后生成摘要信息Digest（至于为什么需要Hash处理我们等等再讲），摘要信息使用私钥private-key 加密之后就生成签名，服务器把签名连同报文一起发送给[客户端](https://www.zhihu.com/search?q=客户端&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966})。
2. 客户端接收后，把签名提取出来用public-key 解密，如果能正常解密出来Digest2，那么就能确认是对方发。

问：为啥如果用public-key解密就能证明是对方发的呢？

答：因为这是private-key是CA提供的，而CA是个权威组织，客户端是相信CA的，通过这种方式证明公钥来源。

<img src="/img/image/image-20211206113707983.png" alt="image-20211206113707983" style="zoom:50%;" />

### 数据完整性

现在已经解决了**密钥传输**、**认证问题**，但是在数据完整性上没有保障，虽然Hacker看不懂也解密不了内容，但是可以瞎改阿。那此时Bob看到的可能是很乱的内容，它不确定是Alice发的，还是被Hacker中途篡改的。

<img src="/img/image/image-20211206113749311.png" alt="image-20211206113749311" style="zoom:50%;" />

这就需要延续刚才说到数字签名认证问题的第二点了，自public-key成功解开之后，下一步就是检验数据完整性了。

客户端把报文Text提取出来做相同的Hash处理，得到摘要信息Digest1，再与之前用[public-key](https://www.zhihu.com/search?q=public-key&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966})成功解密之后的Digest2对比，如果两者相等，就表示内容没有被篡改，否则就是被人改过了。因为文本内容哪怕有一点点改动都会Hash出一个完全不一样的摘要信息出来。所以这个在第一步时，先对报文进行Hash处理是为了后续校验数据完整性问题设计的。

ps：注意，这里的Hash指的是单向Hash，只能内容生成Hash，而Hash是不能逆向推出内容的。并且不同的输入几乎不可能产生相同的输出，即便你要特意去找也非常难找到这样的输入（抗碰撞性）。因此Alice只要将明文内容做一个Hash运算得到一个Hash值，并一起加密传递过去给Bob。Hacker即便篡改了内容，Bob解密之后发现拿到的内容以及对应计算出来的Hash值与传递过来的不一致，说明这个包的完整性被破坏了。

<img src="/img/image/image-20211206113904228.png" alt="image-20211206113904228" style="zoom: 50%;" />

总结一下，安全可靠的保障：

1. 对称加密以及非对称加密来解决：保密性
2. 数字签名：认证、不可抵赖
3. [单向Hash算法](https://www.zhihu.com/search?q=单向Hash算法&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966})：完整性

数据的完整性问题？

![image-20211206114015942](/img/image/image-20211206114015942.png)

### HTTPS通信过程

上面指出了明文传输的问题及解决方案时，并解释完对称加密、非对称加密等概念之后。现在来完整地看下SSL协议握手的过程

![image-20211206114130409](/img/image/image-20211206114130409.png)

1. Client Hello

握手第一步是客户端向服务端发送 Client Hello 消息，这个消息里包含了一个客户端生成的[随机数](https://www.zhihu.com/search?q=随机数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966}) **Random1**、客户端支持的加密套件（Support Ciphers）和 SSL Version 等信息。

<img src="/img/image/image-20211206114207405.png" alt="image-20211206114207405" style="zoom: 67%;" />

2. Server Hello

第二步是服务端向客户端发送 Server Hello 消息，这个消息会从 Client Hello 传过来的 Support Ciphers 里确定一份加密套件，这个套件决定了后续加密和生成摘要时具体使用哪些算法，另外还会生成一份随机数 **Random2**。注意，至此客户端和服务端都拥有了两个随机数（Random1+ Random2），这两个随机数会在后续生成对称秘钥时用到。

<img src="/img/image/image-20211206114258565.png" alt="image-20211206114258565" style="zoom: 67%;" />

3. Certificate

这一步是服务端将自己的证书下发给客户端，让客户端验证自己的身份，客户端验证通过后取出证书中的公钥。

![image-20211206114352517](/img/image/image-20211206114352517.png)

4. Certificate Verify

客户端收到服务端传来的证书后，先从 CA 验证该证书的合法性，验证通过后取出证书中的服务端公钥，再生成一个随机数 **Random3**，再用服务端公钥非对称加密 **Random3** 生成 **PreMaster Key**。

5. Client Key Exchange

上面客户端根据服务器传来的公钥生成了 **PreMaster Key**，Client Key Exchange 就是将这个 key 传给服务端，服务端再用自己的私钥解出这个 **PreMaster Key** 得到客户端生成的 **Random3**。至此，客户端和服务端都拥有 **Random1** + **Random2** + **Random3**，两边再根据同样的算法就可以生成一份秘钥，握手结束后的应用层数据都是使用这个秘钥进行对称加密。为什么要使用三个随机数呢？这是因为 SSL/TLS 握手过程的数据都是明文传输的，并且多个[随机数种子](https://www.zhihu.com/search?q=随机数种子&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966})来生成秘钥不容易被暴力破解出来。客户端将 **PreMaster Key** 传给[服务端](https://www.zhihu.com/search?q=服务端&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966})的过程如下图所示：

![image-20211206114450997](/img/image/image-20211206114450997.png)

6. Encrypted Handshake Message(Client)

这一步对应的是 Client Finish 消息，客户端将前面的握手消息生成摘要再用协商好的秘钥加密，这是客户端发出的第一条加密消息。服务端接收后会用秘钥解密，能解出来说明前面协商出来的秘钥是一致的。

![image-20211206114511422](/img/image/image-20211206114511422.png)

7. Change Cipher Spec(Server)

这一步是服务端通知客户端后面再发送的消息都会使用加密，也是一条事件消息。

8. Encrypted Handshake Message(Server)

这一步对应的是 Server Finish 消息，服务端也会将握手过程的消息生成摘要再用秘钥加密，这是服务端发出的第一条加密消息。客户端接收后会用秘钥解密，能解出来说明协商的秘钥是一致的。

![image-20211206114534804](/img/image/image-20211206114534804.png)

9. Application Data

到这里，双方已安全地协商出了同一份秘钥，所有的[应用层](https://www.zhihu.com/search?q=应用层&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966})数据都会用这个秘钥加密后再通过 TCP 进行可靠传输。

### HTTPS的性能问题

### 协议交互增加网络RTT

简单介绍RTT，维基百科上：

> In [telecommunications](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Telecommunication), the **round-trip delay** (**RTD**) or **round-trip time** (**RTT**) is the length of time it takes for a signal to be sent plus the length of time it takes for an acknowledgement of that signal to be received. This time delay includes the [propagation times](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Propagation_time) for the paths between the two [communication endpoints](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Communication_endpoint).

指的是信息在两个端点之间从发送端开始，到接收端之间经历的时延。

下图是HTTP传输的过程，

![image-20211206114630876](/img/image/image-20211206114630876.png)

下面是HTTPS传输过程：

![image-20211206114655671](/img/image/image-20211206114655671.png)

**相比较两图，HTTPS传输过程最多会比HTTP多7个RTT。**

先一步步解释一下HTTPS传输的每一步：

step1：正常的TCP连接三次握手，这不必说

step2：然后链接会跳转到HTTPS的网站，毕竟协议都不同，考虑到不可能人会把网址打全，所以还需要跳转一步。

step3：又是TCP连接，这里需要又一步TCP连接是因为HTTPS的传输端口不同（这个是传输层的，http是80，https是443）。

step4：完成加密套件的协商和证书的身份确认，这次交互客户端和服务端会协商出相同的密钥交换算法、对称加密算法、[内容一致性校验算法](https://www.zhihu.com/search?q=内容一致性校验算法&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966})、证书签名算法等等。浏览器获取到证书之后，也要验证证书的有效性，是否过期是否撤销。

step5：浏览器获取CA域名，如果没有命中CA域名的缓存，还需要进行DNS解析，又需要多一次交互。

step6：解析成功解析ip之后，需要和CA网站进行tcp三次握手。

step7：这里OCSP请求，全称是Online Certificate Status Protocol，[在线证书状态协议](https://www.zhihu.com/search?q=在线证书状态协议&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A158593966})，顾名思义用来获取证书状态的请求，这里的状态包括有效、过期、未知。并且可以宽限一段客户端访问证书的时间。

step8：主要进行密钥协商。

**加解密计算耗时**

浏览器解析证书签名，各种密钥交换，应用层数据加解密，内容一致性交换。

**握手优化**

如果每次重连都要重新握手还是比较耗时的，所以可以对握手过程进行优化。从下图里我们看到 Client Hello 消息里还附带了上一次的 Session ID，服务端接收到这个 Session ID 后如果能复用就不再进行后续的握手过程。

![image-20211206114746213](/img/image/image-20211206114746213.png)

## 查询MAC地址

这一步主要负责为打包好的`数据+TCP首部+IP首部`寻找传输路线，**找到IP对应的物理机**，这里会用到ARP协议。

ARP（Address Resolution Protocol）即地址解析协议， 用于**实现从 IP 地址到 MAC 地址的映射**，即询问目标IP对应的MAC地址。

ARP协议通过**一问一答**实现交互，但是问和答都有讲究，**问是通过广播形式实现，答是通过单播形式。**

# 请求在Tomcat中的处理流程

Web 容器以**进程**的方式在计算机上运行，**它主要负责接收请求，并将其投送至特定的应用**，但Web容器并不属于计算机网络的组成部分。接下来将以Tomcat为例介绍Web容器的核心组件。

### Tomcat的核心组件

Tomcat的核心组件主要有：**Server、Service、Connector、Engine、Host和Context**。

**一个Server可以包含多个Service，一个Service可以包含多个Connector，但只能包含一个Engine，一个Engine可以包含多个Host，一个Host可以包含多个Context**。

![image-20211205193912963](/img/image/image-20211205193912963.png)

图中tomcat核心组件分别是连接器Connector和容器Container

- 连接器负责对外交流，处理socket请求，读取字节流解析成tomcat request和response对象。
- 容器负责内部业务逻辑，管理servlet的生命周期，处理具体业务请求逻辑。

### Coyote组件

Coyote是Tomcat中连接器组件的名称,从图中可以知道它内部由EndPoint,Processor,Adapter三个核心组件构成，下面来看下这三个组件分别是做什么的。

* **EndPoint**：通信监听接口，是具体的Socket接收发送处理器，是用来实现TCP/IP协议的，是对传输层的抽象。
* **Processor**：协议处理接口，用来接收EndPoint的Socket，读取字节流解析成Tomcat Request和Response对象，并通过Adapter提交到对应容器处理，是用来实现HTTP协议的，是对应用层的抽象。
* **ProtocolHandler**：协议接口，通过Endpoint和Processor组件，实现针对具体协议的处理能力。Tomcat 按照协议和I/O 提供了6个实现类 : AjpNioProtocol，AjpAprProtocol，AjpNio2Protocol，Http11NioProtocol，Http11Nio2Protocol，Http11AprProtocol
* **Adapter**：由于协议不同，客户端发过来的请求信息格式也不相同。ProtocolHandler接口解析请求并生成Tomcat Request类。由于不同协议导致的Request差异，Tomcat设计者的解决方案是引入CoyoteAdapter，这是适配器模式的经典运用，连接器调用CoyoteAdapter的Sevice方法，传入Tomcat Request对象， CoyoteAdapter负责将Tomcat Request转成ServletRequest，再调用容器。

**Connector是主要负责接收请求的组件**。

**Tomcat有以下两种工作模式**

- 作为Web服务器，直接接收客户端的请求
- 作为Java Web服务器，接收前置Web服务器的请求

<img src="/img/image/image-20211205195555845.png" alt="image-20211205195555845" style="zoom:67%;" />

每个 Service 可以有一个或多个 Connector，不同工作模式下，Tomcat 需要为各种类型的请求分别定义相应的 Connector，这样才能正确接收客户端对应协议的请求。定义 Connector 可以使用多种属性，某些属性只适用于某种特定的 Connector 类型。

一般说来，常见的 Connector 有 4 种类型

- **HTTP**
- **HTTPS**
- **AJP**
- **Proxy**

<img src="/img/image/image-20211205195725144.png" alt="image-20211205195725144" style="zoom:67%;" />

Connector作为通信接口，**它为其所属特定的 Service 接收外部客户端请求，以及回送应答至外部客户端**。具体职责包括创建 Request、Response 对象用于跟外部客户端交换数据，并**将 Request 交给配套的 Engine 来处理**。

### Catalina组件

 Catalina是Tomcat的servlet容器，Tomcat本质上就是一款Servlet 容器，因为Catalina才是Tomcat的核心，其他模块都是为Catalina提供支撑的。接下来来看下图中Catalina的组成部分分别都是干什么的。

* Engine：表示整个Catalina的Servlet引擎，用来管理多个虚拟站点，Service 组件中**负责请求处理的组件**，其内部可以包含多个 Host。Engine 从一个或多个 Connector 中接收请求并处理，并将处理结果封装成应答交给 Connector，最终回传给外部客户端。一个Service最多只能有一个Engine， 但是一个Engine可包含多个Host
* Host：代表一个**虚拟主机(java中jvm)，它对应计算机网络上的一个实体**即某个在 DNS 服务器上注册过的域名或者 IP 地址，例如：`www.baidu.com`或 201.187.10.21。Host 内部可以包含多个 Context，**每个 Context 表示一个 Web 应用**。Host 负责安装、展开、启动和结束每个 Web 应用。
* Context：表示一个Web应用程序，**负责处理某个特定 Web 应用的所有请求**， 一个Web应用可包含多个Wrapper
* Wrapper：表示一个Servlet，Wrapper 作为容器中的最底层，不能包含子容器

> 客户端在填写目标地址时会通过主机名来标识它希望访问的服务器，Tomcat 将从 HTTP 请求头的 Host 字段提取主机名，然后再匹配对应的虚拟主机。如果没有找到匹配的，HTTP 请求将被发送至默认主机 defaultHost。

### Jasper组件

提供JSP引擎，用于解析处理jsp格式文件。

### Juli组件

提供日志服务

### Naming组件

提供JNDI服务

### 配置文件的结构如下

```xml
<Server>                              
    <Service>
        <Engine>
            <Host>
                <Context />
            </Host>
            <Host>
                <Context />
            </Host>
        </Engine>  
        <Connector />
        <Connector />
    </Service>
</Server>
```

* Server 是整个配置文件的**唯一根元素**，代表整个 Tomcat 容器。Server 内部可以包含多个 Service，其主要职责就是管理多个 Service，对外提供给客户端访问，同时维护所有 Service 的生命周期，包括初始化服务、结束服务、定位客户端要访问的 Service 等等。

* Service 的主要职责就是将 Engine 与 Connector 装配在一起对外提供服务。一个 Service 可以包含多个 Connector，但只能包含一个 Engine，**其中 Connector 负责从客户端接收请求，Engine 负责处理 Connector 接收进来的请求。**



### Tomcat处理HTTP请求

当以 HTTP 请求到达Tomcat服务器（Server）以后，Tomcat会进行以下几个步骤，将请求交给对应的Web应用进行处理

*  **根据协议类型和端口号选定 Service 和 Engine**
  * Connector 主要负责接收请求。当 Connector 接收到特定协议和特定端口的请求后，**其所属的 Service 和 Service 下的 Engine 也就确定了**
* **根据域名或 IP 地址选定 Host**
  * Engine一旦确定了，就会根据 IP 来选择对应的虚拟主机Host来处理请求。如果匹配失败了，则会使用默认虚拟主机来处理请求
* **根据 URI 选定 Context**
  * URI 中的 context-path 指定了 HTTPS 请求将要访问的 Web 应用
  * 当请求抵达时，Tomcat 将根据 Context 的属性 path 取值与 URI 中的 context-path 的匹配程度来选择 Web 应用处理相应请求

### 怎么配置tomcat容器

Tomcat作为服务器的配置，主要在conf目录下的server.xml 文件，server.xml中包含了 Servlet容器的相关配置，即Catalina的配置。先来看下server.xml的具体内容。



```xml
<?xml version="1.0" encoding="UTF-8"?>

<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <GlobalNamingResources>

    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <Service name="Catalina">

    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

    <Engine name="Catalina" defaultHost="localhost">

      <Realm className="org.apache.catalina.realm.LockOutRealm">
    
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">

        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
    </Engine>
  </Service>
</Server>
```

##### server 标签

- port:关闭服务器的监听端口
- shutdown:关闭服务器的指令字符串

```xml
<Server port="8005" shutdown="SHUTDOWN">
  <!--以日志形式输出服务器，操作系统，jvm的版本信息-->
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <!-- 加载(服务器启动) 和 销毁 (服务器停止)APR。如果找不到APR库，则会输出日志，并不影响Tomcat启动 -->
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <!-- 避免JRE内存泄漏问题 -->
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <!-- 加载(服务器启动) 和 销毁(服务器停止) 全局命名服务 -->
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <!--在Context停止时重建 Executor 池中的线程， 以避免ThreadLocal 相关的内存泄漏 -->
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <GlobalNamingResources>

    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>
......
</Server>
```

##### service 标签

该标签用于创建 Service 实例，默认使用 org.apache.catalina.core.StandardService

- name:服务名称

```xml
<Service name="Catalina">
......
</Service>
```

##### Executor标签

- name:线程池名称
- namePrefix:线程名前缀
- maxThreads:最大线程数
- minSpareThreads:核心线程数(空闲时不会被回收)
- maxQueueSize:队列大小(大于最大线程数的请求会被放到队列排队)
- prestartminSpareThreads:启动线程池时是否启动minSpareThreads部分线程。默认值为false，即不启动.
- threadPriority:线程池中线程优先级，默认值为5，值从1到10
- className:线程池实现类，未指定情况下，默认实现类为org.apache.catalina.core.StandardThreadExecutor。如果想使用自定义线程池首先需要实现org.apache.catalina.Executor接口

```xml
<Executor name="xialuThreadPool"
    namePrefix="thread-exec-"
    maxThreads="200"
    minSpareThreads="100"
    maxIdleTime="60000"
    maxQueueSize="Integer.MAX_VALUE"
    prestartminSpareThreads="false"
    threadPriority="5"
   className="org.apache.catalina.core.StandardThreadExecutor"/>
```

##### Connector 标签

- prot:端口号，connector用于创建服务socket并进行监听，如果端口号设置为0，tomcat会随机选择一个可用端口号给当前connector
- protocol:当前connector支持的访问协议，默认为HTTP/1.1，并采用自动切换机制选择一个机遇java nio的链接7⃣️或者基于本地的APR链接器
- connectionTimeout: Connector收到连接后的等待超时时间，单位毫秒，-1表示不超时。
- redirectPort:当前connector不支持ssl请求，收到https请求之后，会重定向到redirectPort配置的端口
- executor:指定共享线程池的名称
- URIEncoding:指定编码uri的字符串编码，tomcat8.x版本默认编码为utf-8,tomcat7.x版本默认为iso-8859-1

```xml
<Connector port="8080"
          protocol="HTTP/1.1"
          executor="commonThreadPool"
          maxThreads="1000"
          minSpareThreads="100"
          acceptCount="1000"
          maxConnections="1000"
          connectionTimeout="20000"
          compression="on"
          compressionMinSize="2048"
          disableUploadTimeout="true"
          redirectPort="8443"
          URIEncoding="UTF-8" />
```

##### Engine 标签

- name: 用于指定Engine的名称，默认为Catalina
- defaultHost:默认使用的虚拟主机名称， 当客户端请求指向的主机无效时， 将交由默认的虚拟主机处理， 默认为localhost

```xml
<Engine name="Catalina" defaultHost="localhost">
.....
</Engine>
```

##### Host 标签

```xml
<Host name="localhost"  appBase="webapps" unpackWARs="true" autoDeploy="true">
    ......
</Host>
```

##### context 标签

- docBase:Web应用目录或者War包的部署路径。可以是绝对路径，也可以是相对于Host appBase的相对路径。
- path:Web应用的Context 路径。如果我们Host名为localhost，则该web应用访问的根路径为:[http://localhost:8080/web_demo](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8080%2Fweb_demo)。

```xml
<Host name="www.xialu.com"  appBase="webapps" unpackWARs="true"
autoDeploy="true">
<Context docBase="/Users/yingdian/web_demo" path="/web3"></Context> 
   <Valve className="org.apache.catalina.valves.AccessLogValve"
directory="logs"
   prefix="localhost_access_log" suffix=".txt"
   pattern="%h %l %u %t &quot;%r&quot; %s %b" />
</Host>
```

# 请求在Web应用中的处理流程

请求被 Web 容器中的 Connector 捕获，选取对应的 Server 中的 Engine ，Engine 再根据IP选择对应的虚拟主机，虚拟主机根据URI将请求交给对应的Web应用进行处理。接下来将介绍请求在Web请求中的处理过程。

## Web应用核心组件

> **Listener**

监听器 Listener 主要用于监听 Application、Session、Request 等对象的变化，每当这些对象发生变化就会回调用对应的监听方法。

> **Filter**

过滤器 Filter 负责对请求做**预处理**，接着将请求交给 Servlet 进行处理并生成响应，最后 Filter 再对响应进行后处理。

从请求的处理过程来看，Filter 主要参与以下几个环节

- 在 HttpServletRequest **到达 Servlet 之前，拦截客户的 HttpServletRequest**
- 根据需要检查 HttpServletRequest，也可以修改 HttpServletRequest 报文头和数据
- 在 Servlet 生成的 HttpServletResponse **抵达客户端之前，拦截 HttpServletResponse**
- 根据需要检查 HttpServletResponse，也可以修改 HttpServletResponse 报文头和数据

**简单来说就是在真正处理请求以及返回响应之前，通过过滤器对内容再进行一些修改**

> **Servlet**

**Servlet 负责处理客户端访问动态资源的 HTTP 请求**，接口 javax.servlet.Servlet 定义了所有 Servlet 必须要实现的方法

```java
public interface Servlet {
    // 由 Servlet 容器调用，完成 Servlet 初始化，启动对外服务
    void init(ServletConfig var1) throws ServletException;

    // 获取 Servlet 初始化和启动时参数的配置信息对象 ServletConfig
    ServletConfig getServletConfig();

    // 由 Servlet 容器调用，让 Servlet 处理某个 HTTP 请求
    void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;

    // 获取 Servlet 的说明信息，包括：作者、版本和版权等等
    String getServletInfo();

    // 由 Servlet 容器调用，用于关闭停止 Servlet 提供的服务
    void destroy();
}
```

从 HTTP 请求的处理过程来看，Servlet 主要参与以下几个环节

- 接收请求
  - 客户端请求会被封装成 HttpServletRequest 对象，包含报文头参数和报文体等信息
- 处理请求
  - 通常调用 Servlet 的方法 service、doPost 或 doGet 等方法处理请求，并**进一步调用业务层相应逻辑对其进行处理等**
- 反回响应
  - 处理完请求后，可以转发（forward）、重定向（redirect）到某个视图页面或者直接返回结果数据

## Web应用处理HTTP请求流程

Web 应用处理 HTTP 请求的流程主要是**穿越 Listener 和多个 Filters，最终抵达 Servlet 的过程**，Servlet再进行下一步的处理。

**具体流程如下图**

<img src="/img/image/image-20211205201638812.png" alt="image-20211205201638812" style="zoom: 67%;" />

# 请求在Spring Web应用中的处理流程

因为使用 SSM 框架，所以 Spring MVC 中的 DispatcherServlet 充当了 Web 应用中的 Serlvet，负责将任务分配给对应的Controller，并将最终视图返回给 Web 容器。

## Spring MVC的核心组件

* DispatcherServlet

  DispatcherServlet 是整个流程**控制的中心**，由它来**接收请求并调用其它组件处理用户的请求**，同时还负责响应结果。DispatcherServlet的存在降低了组件之间的耦合性。

* HandlerMapping

  HandlerMapping 负责**根据用户请求映射获得对应的 Handler和 HandlerInterceptor**。处理方法为从 URL 获得 URI，在通过 URI 从 HandlerMapping 中找到对应的 Handler 和 HandlerInterceptor，即处理器和拦截器。

* HandlerAdapter

  HandlerAdapter 负责按照特定规则去执行 Handler。如果 Handler 有对应的 HandlerAdapater，**HandlerAdapater 则会在调用 Handler 之前执行 HandlerInterceptor 的 preHandler() 方法对 Handler 进行拦截**。

* HandlerInterceptor

  HandlerInterceptor 主要负责在执行 Handler 前对其进行拦截。HandlerInterceptor 中的 preHandler() 方法将会提取 HTTP 请求中的数据填充到处理器 Handler 的中。

* Handler

  Handler **即Controller ，是处理业务代码的核心器件**。这部分由程序员自行编写，一般的SSM框架中，其下层还有Service和Dao。

## Spring MVC处理请求流程

当 Web 容器中的 Host 会选择对应的 Web应用来处理请求，这里将请求交给了 Spring MVC 中的 DispatcherServlet 来进一步处理请求

- DispatcherServlet 通过解析 HTTP 请求的 URL 获得 URI，再根据该 URI **从 HandlerMapping 当中获得该请求对应的 Handler 和 HandlerInterceptor**
- DispatcherServlet 根据获得的 Handler 选择合适的 HandlerAdapter。如果成功获得 HandlerAdapter，**HandlerAdapater 则会在调用 Handler 之前执行 HandlerInterceptor 的 preHandler() 方法对 Handler 进行拦截**
- Handler 即 Controller 会进行请求的处理，并向下调用 Service 和 Dao 来处理请求
- Hander 处理完成请求后会返回模型数据，模型数据由 DispatcherServlet 封装后返回给Web 容器

**处理的流程图如下**

<img src="/img/image/image-20211206112236358.png" alt="image-20211206112236358" style="zoom: 60%;" />





























