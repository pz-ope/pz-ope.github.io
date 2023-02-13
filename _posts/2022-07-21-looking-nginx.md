# Nginx介绍

## 为什么使用Nginx

> 网站上线初期，并发量小，用户使用少，一个jar包启动应用就够了，内部使用tomatf返回内容给用户。

<img src="../../../../../Download/Typora/image/image-20220721214403331.png" alt="image-20220721214403331" style="zoom:80%;" />

> 随着用户量增加，并发量增多，一台服务器不能满足需求

<img src="../../../../../Download/Typora/image/image-20220721214430540.png" alt="image-20220721214430540" style="zoom:80%;" />

> 使用横向扩张的方式增加服务器，这时候几个项目在不同的服务器上（由于服务器是独立的，tomcat之间不共享，例如一个用户在服务器1上登录了，在服务器2由于不知道用户已经登录的，就会导致错误，用户在服务器2中也会登录），用户要访问就需要增加一个代理服务器。通过代理服务器来帮我们转发和处理请求。

<img src="../../../../../Download/Typora/image/image-20220721214506579.png" alt="image-20220721214506579" style="zoom:80%;" />

我们希望这个代理服务器可以帮助我们接收用户的请求,然后将用户的请求按照规则帮我们转发到不同的服务器节点之上。这个过程用户是无感知的，用户并不知道是哪个服务器返回的结果,我们还希望他可以按照服务器的性能提供不同的权重选择。保证最佳体验!所以我们使用了Nginx.

## 什么是Nginx

* Nginx (engine x)是一个高性能的HTTP和反向代理web服务器, 同时也提供了IMAP/POP3/SMTP服务。Nginx是 由伊戈尔.赛索耶夫为俄罗斯访问量第二的Rambler.ru站点(俄文: Pam6nep)开发的,第一个公开版本0.1.0发布于2004年10月4日。2011年6月18, nginx1.0.4发布。
* 其特点是占有内存少，并发能力强，实上nginx的并发能力在同类型的网页服务器中表现较好,中国大陆使用nginx网站用户有:百度、东新浪、网易、腾讯、淘宝等。在全球活跃的网站中有12.18%的使用比率,约为2220万个网站。
* Nginx是一个安装非常的简单、 配置文件非常简洁(还能够支持peri语法)、Bug非常少的服务。Nginx 启动特别容易，并且几乎可以做到7*24不间断运行,即使运行数个月也不需要重新启动。你还能够不间断服务的情况下进行软件版本的升级。
* Nginx代码完全用C语言从头写成。官方数据测试表明能够支持高达50,000个并发连接数的响应

## Nginx作用

Http代理，反向代理:作为web服务器最常用的功能之一，尤其反向代理。

负载均衡

* **正向代理即是客户端代理, 代理客户端, 服务端不知道实际发起请求的客户端.**

* **反向代理即是服务端代理, 代理服务端, 客户端不知道实际提供服务的服务端**

## 正向代理

> 正向代理类似一个跳板机，代理访问外部资源
>
> 比如我们国内访问谷歌，直接访问访问不到，我们可以通过一个正向代理服务器，请求发到代理器，代理服务器能够访问谷歌，这样由代理去谷歌取到返回数据，再返回给我们，这样我们就能访问谷歌了
>
> **正向代理的用途：**
>
> 　　（1）访问原来无法访问的资源，如google
>
> ​        （2） 可以做缓存，加速访问资源
>
> 　　（3）对客户端访问授权，上网进行认证
>
> 　　（4）代理可以记录用户访问记录（上网行为管理），对外隐藏用户信息

<img src="../../../../../Download/Typora/image/image-20220721214849079.png" alt="image-20220721214849079" style="zoom:80%;" />

## 反向代理

> 反向代理（Reverse Proxy）实际运行方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个服务器
>
> **反向代理作用**
>
> （1）保证内网的安全，阻止web攻击，大型网站，通常将反向代理作为公网访问地址，Web服务器是内网
>
> （2）负载均衡，通过反向代理服务器来优化网站的负载

<img src="../../../../../Download/Typora/image/image-20220721215017342.png" alt="image-20220721215017342" style="zoom:80%;" />

> Nginx提供的负载均衡策略有2种:内置策略和扩展策略。内置策略为轮殉，加权轮询，Ip hash.扩展策略，就天马行空，只有你想不到的没有他做不到的。

### 轮询

<img src="../../../../../Download/Typora/image/image-20220721215100275.png" alt="image-20220721215100275" style="zoom:80%;" />

### 加权轮询

更多的请求发送到权重大的服务器上

<img src="../../../../../Download/Typora/image/image-20220721215118650.png" alt="image-20220721215118650" style="zoom:80%;" />

### iphash

iphash对客户端请求的ip进行hash操作，然后根据hash结果将同一个客户端ip的请求分发给同一 台服务器进行处理，可以解决session不共享的问题。

> 一个会话（session）保存在一个tomcat中，当有多个tomcat（n个）则session就有n个，但这时候就不可能做到session共享的。在nginx中提供了一个默认算法iphash及根据IP进行计算，固定的IP只能访问固定的服务器上这样session就不会丢失
>
> 但iphash的性能不好，大部分是使用redis做到session共享。

<img src="../../../../../Download/Typora/image/image-20220721215217523.png" alt="image-20220721215217523" style="zoom:80%;" />

### 动静分离

> 动静分离，在我们的软件开发中，有些请求是需要后台处理的，有些请求是不需要经过后台处理的(如: Css、html. jpg、 js等等文件)，这些不需要经过后台处理的文件称为静态文件。让动态网站里的动态网页根据一定规则把不变的资源和经常变的资源区分开来， 动静资源做好了拆分以后，我们就可以根据静态资源的特点将其做缓存操作。提高资源响应的速度。

<img src="../../../../../Download/Typora/image/image-20220721215417214.png" alt="image-20220721215417214" style="zoom:80%;" />

# Nginx安装

## Linux环境下

命令：

是否安装nginx：nginx/whereis nginx

解压文件：tar -zxvf nginx-1.18.0.tar.gz

自动配置运行解压目录下的configure文件：./configure

下一步执行make命令编译内核或者模块：make

```bash
[root@linux ~]# make   #编译

[root@linux ~]# make install  #安装

[root@linux ~]# make modules_install    #安装模块

[root@linux ~]# make clean    #清除编译痕迹
```

再来执行：make install 

<img src="../../../../../Download/Typora/image/image-20220721215556165.png" alt="image-20220721215556165" style="zoom:80%;" />

运行nginx：./nginx

<img src="../../../../../Download/Typora/image/image-20220721215637991.png" alt="image-20220721215637991" style="zoom:80%;" />

查看配置文件：cat nginx.conf

<img src="../../../../../Download/Typora/image/image-20220721215655860.png" alt="image-20220721215655860" style="zoom:80%;" />

访问端口ip+端口号：

```
8.210.51.92:80
```

## Nginx常用命令

1. `cd /usr/local/nginx/sbin/`
2. `./nginx  启动`
3. `./nginx -s stop  停止``#表示当前目录下实行`
4. `./nginx -s quit  安全退出`
5. `./nginx -s reload  重新加载配置文件`
6. `ps aux|grep nginx  查看nginx进程`

启动成功访问 服务器ip:80

注意：如何连接不上，检查阿里云安全组是否开放端口，或者服务器防火墙是否开放端口！

相关命令：

```bash
# 开启
service firewalld start
# 重启
service firewalld restart
# 关闭
service firewalld stop
# 查看防火墙规则
firewall-cmd --list-all
# 查询端口是否开放
firewall-cmd --query-port=8080/tcp
# 开放80端口
firewall-cmd --permanent --add-port=80/tcp
# 移除端口
firewall-cmd --permanent --remove-port=8080/tcp

#重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload

# 参数解释
1、firwall-cmd：是Linux提供的操作firewall的一个工具；
2、--permanent：表示设置为持久；
3、--add-port：标识添加的端口；
```

## 示例

```shell
upstream lb{
server 127.0.0.1:8080 weight=1;
server 127.0.0.1:8081 weight=1;
}


location / {
proxy_pass http://lb;
}
```

<img src="../../../../../Download/Typora/image/image-20220721220220217.png" alt="image-20220721220220217" style="zoom:80%;" />

![image-20220721220242636](../../../../../Download/Typora/image/image-20220721220242636.png)

<img src="../../../../../Download/Typora/image/image-20220721220338195.png" alt="image-20220721220338195" style="zoom:80%;" />

<img src="../../../../../Download/Typora/image/image-20220721220353620.png" alt="image-20220721220353620" style="zoom:80%;" />

# netty实现代理

http://www.flydean.com/35-netty-simple-proxy/

首选我们首先代理服务器是一个服务器，所以我们需要在netty中使用ServerBootstrap创建一个服务器：

```java
EventLoopGroup bossGroup = new NioEventLoopGroup(1);
EventLoopGroup workerGroup = new NioEventLoopGroup();

try {
    ServerBootstrap b = new ServerBootstrap();
     b.group(bossGroup, workerGroup)
         .channel(NioServerSocketChannel.class)
         .handler(new LoggingHandler(LogLevel.INFO))
         .childHandler(new SimpleDumpProxyInitializer(REMOTE_HOST, REMOTE_PORT))
         .childOption(ChannelOption.AUTO_READ, false)
         .bind(LOCAL_PORT).sync().channel().closeFuture().sync();
}
```

在这个local服务器中，我们传入ProxyInitializer。在这个handler初始化器中，我们传入自定义的handler：

```java
    public void initChannel(SocketChannel ch) {
        ch.pipeline().addLast(
                new LoggingHandler(LogLevel.INFO),
                new SimpleDumpProxyInboundHandler(remoteHost, remotePort));
    }
```

# 一致性哈希算法---负载均衡

https://blog.csdn.net/qq_41721746/article/details/124966916?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-124966916-blog-89420823.pc_relevant_multi_platform_whitelistv1&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-124966916-blog-89420823.pc_relevant_multi_platform_whitelistv1&utm_relevant_index=1





