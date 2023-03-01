---
layout:     post
title:      "Linux"
subtitle:   ""
date:       2022-09-20 09:23:11
author:     "pz"
catalog: false
header-style: text
tags:
  - 笔记
  - 操作系统

---

# Linux概述

linux诞生了这么多年，以前还喊着如何能取代windows系统，现在这个口号已经小多了，任何事物发展都有其局限性都有其天花板。就如同在国内再搞一个社交软件取代腾讯下·一样，想想而已基本不可能，因为用户已经习惯于使用微信交流，不是说技术上实现不了解而是老百姓已经习惯了，想让他们不用，即使他们自己不用亲戚朋友还是要用，没有办法的事情。

用习惯了windows操作系统，再让大家切换到别的操作系统基本上是不可能的事情，改变一个人已经养成的习惯太难。没有办法深入到普通老百姓的生活中，并不意味着linux就没有用武之地了。在服务器端，在开发领域linux倒是越来越受欢迎，很多程序员都觉得不懂点linux都觉得不好意思，linux在开源社区的地位依然岿然不动。

尤其是作为一个后端程序员，是必须要掌握Linux的，因为这都成为了你找工作的基础门槛了，所以不得不学习！

## Linux 简介

Linux 内核最初只是由芬兰人林纳斯·托瓦兹（Linus Torvalds）在赫尔辛基大学上学时出于个人爱好而编写的。

Linux 是一套免费使用和自由传播的类 Unix 操作系统，是一个基于 POSIX（可移植操作系统接口） 和 UNIX 的多用户、多任务、支持多线程和多 CPU 的操作系统。（最高权限:`root`）

Linux 能运行主要的 UNIX 工具软件、应用程序和网络协议。它支持 32 位和 64 位硬件。Linux 继承了 Unix 以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。

## Linux 发行版

Linux 的发行版说简单点就是将 Linux 内核与应用软件做一个打包。

<img src="/img/image/image-20220802111157751.png" alt="image-20220802111157751" style="zoom:80%;" />

目前市面上较知名的发行版有：Ubuntu、RedHat、CentOS、Debian、Fedora、SuSE、OpenSUSE、Arch Linux、SolusOS 等。(可以自己编写Lunix系统，搜索kali linux做安全渗透测试=>白帽，补天，红帽认证工程师)

## Linux 应用领域

今天各种场合都有使用各种 Linux 发行版，从嵌入式设备到超级计算机，并且在服务器领域确定了地位，通常服务器使用 LAMP（Linux + Apache （Web服务器软件）+ MySQL + PHP（超文本预处理器））或 LNMP（Linux + Nginx（高性能的HTTP和反向代理web服务器）+ MySQL + PHP）组合。

## Linux vs Windows

目前国内 Linux 更多的是应用于服务器上，而桌面操作系统更多使用的是 Windows。主要区别如下

| 比较     | Windows                                                      | Linux                                                        |
| :------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 界面     | 界面统一，外壳程序固定所有 Windows 程序菜单几乎一致，快捷键也几乎相同 | 图形界面风格依发布版不同而不同，可能互不兼容。GNU/Linux 的终端机是从 UNIX 传承下来，基本命令和操作方法也几乎一致。 |
| 驱动程序 | 驱动程序丰富，版本更新频繁。默认安装程序里面一般包含有该版本发布时流行的硬件驱动程序，之后所出的新硬件驱动依赖于硬件厂商提供。对于一些老硬件，如果没有了原配的驱动有时很难支持。另外，有时硬件厂商未提供所需版本的 Windows 下的驱动，也会比较头痛。 | 由志愿者开发，由 Linux 核心开发小组发布，很多硬件厂商基于版权考虑并未提供驱动程序，尽管多数无需手动安装，但是涉及安装则相对复杂，使得新用户面对驱动程序问题（是否存在和安装方法）会一筹莫展。但是在开源开发模式下，许多老硬件尽管在Windows下很难支持的也容易找到驱动。HP、Intel、AMD 等硬件厂商逐步不同程度支持开源驱动，问题正在得到缓解。 |
| 使用     | 使用比较简单，容易入门。图形化界面对没有计算机背景知识的用户使用十分有利。 | 图形界面使用简单，容易入门。文字界面，需要学习才能掌握。     |
| 学习     | 系统构造复杂、变化频繁，且知识、技能淘汰快，深入学习困难。   | 系统构造简单、稳定，且知识、技能传承性好，深入学习相对容易。 |
| 软件     | 每一种特定功能可能都需要商业软件的支持，需要购买相应的授权。 | 大部分软件都可以自由获取，同样功能的软件选择较少。           |

# 环境搭建

Linux 的安装，安装步骤比较繁琐（操作系统本身也是一个软件），现在其实云服务器挺普遍的，价格也便宜，如果直接不想搭建，也可以直接买一台学习用用！

## 安装CentOS

Linux是一个操作系统,你也可以把自己电脑安装成双系统!

**下载VMware**

![image-20220802111453019](/img/image/image-20220802111453019.png)

**安装centOS**

1. 通过镜像进行安装

   CentOS7网盘地址：链接：https://pan.baidu.com/s/1EPhK9nqK9AiBmuitscVy9Q提取码：76x5

2. 打开VMware 虚拟机软件，然后打开我们的镜像选中解压后文件中的`CentOS 64 位.vmx`文件

3. 点击开启此虚拟机

   <img src="/img/image/image-20220802111933224.png" alt="image-20220802111933224" style="zoom:80%;" />

4. 密码都是123456（Linux磁盘分区的时候需要注意分区名称：/boot /home）

   <img src="/img/image/image-20220802112058543.png" alt="image-20220802112058543" style="zoom:50%;" />

## 购买云服务器（推荐）

云服务就是一个远程电脑，服务器一般不会关机

虚拟机安装后占用空间，也会有些卡顿，我们作为程序员其实可以选择购买一台自己的服务器，这样的话更加接近真实线上工作；

1、阿里云购买服务器：https://www.aliyun.com/minisite/goods?userCode=0phtycgr

2、购买完毕后，获取服务器的ip地址，重置服务器密码，就可以远程登录了

关于安全组说明（即自己开放的一系列端口号）

<img src="/img/image/image-20220802112254411.png" alt="image-20220802112254411" style="zoom:80%;" />

![image-20220802112321673](/img/image/image-20220802112321673.png)

<img src="/img/image/image-20220802112354320.png" alt="image-20220802112354320" style="zoom:80%;" />

3、下载 xShell 工具（远程连接工具，建议使用360下载，此外还可以下载xFtp文件上传工具），进行远程连接使用！连接成功效果如下：（当然也可以连接本地虚拟机）

要打开端口，需要在阿里云的安全组面板中开启对应的出入规则，不然的话会被阿里拦截!

<img src="/img/image/image-20220802112430692.png" alt="image-20220802112430692" style="zoom:80%;" />

<img src="/img/image/image-20220802112552457.png" alt="image-20220802112552457" style="zoom:80%;" />

<img src="/img/image/image-20220802112605017.png" alt="image-20220802112605017" style="zoom:80%;" />

## 宝塔面板

如果前期不好操作，可以推荐安装宝塔面板，傻瓜式管理服务器

安装教程：https://www.bt.cn/bbs/thread-19376-1-1.html

1、  开启对应的端口

<img src="/img/image/image-20220802112828351.png" alt="image-20220802112828351" style="zoom:80%;" />

2、  一键安装

```shell
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

<img src="/img/image/image-20220802112914658.png" alt="image-20220802112914658" style="zoom:80%;" />

3、  安装完毕后会得到远程面板的地址，账号，密码，就可以登录了

<img src="/img/image/image-20220802112958726.png" alt="image-20220802112958726" style="zoom:80%;" />

4、登录之后就可以可视化的安装环境和部署网站！

<img src="/img/image/image-20220802113041013.png" alt="image-20220802113041013" style="zoom:80%;" />

<img src="/img/image/image-20220802113101045.png" alt="image-20220802113101045" style="zoom:80%;" />

<img src="/img/image/image-20220802113118463.png" alt="image-20220802113118463" style="zoom:80%;" />

<img src="/img/image/image-20220802113133290.png" alt="image-20220802113133290" style="zoom:80%;" />

<img src="/img/image/image-20220802113148336.png" alt="image-20220802113148336" style="zoom:80%;" />

<img src="/img/image/image-20220802113203827.png" alt="image-20220802113203827" style="zoom:80%;" />

<img src="/img/image/image-20220802113216469.png" alt="image-20220802113216469" style="zoom:80%;" />

<img src="/img/image/image-20220802113241274.png" alt="image-20220802113241274" style="zoom:80%;" />

<img src="/img/image/image-20220802113316699.png" alt="image-20220802113316699" style="zoom:80%;" />

<img src="/img/image/image-20220802113405438.png" alt="image-20220802113405438" style="zoom:80%;" /><img src="/img/image/image-20220802113430855.png" alt="image-20220802113430855" style="zoom:80%;" />

<img src="/img/image/image-20220802113456014.png" alt="image-20220802113456014" style="zoom:80%;" />

<img src="/img/image/image-20220802113509976.png" alt="image-20220802113509976" style="zoom:80%;" />

<img src="/img/image/image-20220802113538841.png" alt="image-20220802113538841" style="zoom:80%;" />

<img src="/img/image/image-20220802113549180.png" alt="image-20220802113549180" style="zoom:80%;" />

复制里面这么一串配置信息，与前面的server配置同级括号下 就可以访问到了

<img src="/img/image/image-20220802113617570.png" alt="image-20220802113617570" style="zoom:80%;" />

关于域名

如果自己的网站想要上线，就一定要购买一个域名然后进行备案；

<img src="/img/image/image-20220802113653438.png" alt="image-20220802113653438" style="zoom:80%;" />

# Linux系统基础

## 开机登录

开机会启动许多程序。它们在Windows叫做"服务"（service），在Linux就叫做"守护进程"（daemon）。

开机成功后，它会显示一个文本登录界面，这个界面就是我们经常看到的登录界面，在这个登录界面中会提示用户输入用户名，而用户输入的用户将作为参数传给login程序来验证用户的身份，密码是不显示的，输完回车即可！

一般来说，用户的登录方式有三种：

* 命令行登录
* ssh登录
* 图形界面登录

最高权限账户为 root，可以操作一切！

## 关机

在linux领域内大多用在服务器上，很少遇到关机的操作。毕竟服务器上跑一个服务是永无止境的，除非特殊情况下，不得已才会关机。

不管是重启系统还是关闭系统，首先要运行 **sync** 命令，把内存中的数据写到磁盘中

```shell
sync # 将数据由内存同步到硬盘中。
shutdown # 关机指令，你可以man shutdown 来看一下帮助文档。例如你可以运行如下命令关机：

shutdown –h 10 # 这个命令告诉大家，计算机将在10分钟后关机
shutdown –h now # 立马关机
shutdown –h 20:25 # 系统会在今天20:25关机
shutdown –h +10 # 十分钟后关机
shutdown –r now # 系统立马重启
shutdown –r +10 # 系统十分钟后重启
reboot # 就是重启，等同于 shutdown –r now
halt # 关闭系统，等同于shutdown –h now 和 poweroff
```

## 系统目录结构

* linux中是一切皆文件；
* 根目录` /` ,所有文件都挂载在这个节点下
* 当前目录` ./`

登录系统后，在当前命令窗口下输入命令：

```shell
 ls / 
```

你会看到如下图所示:

<img src="/img/image/image-20220802114937785.png" alt="image-20220802114937785" style="zoom:80%;" />

树状目录结构：（Linux的一切资源都挂载在这个 / 根节点下）

<img src="/img/image/image-20220802115041646.png" alt="image-20220802115041646" style="zoom:80%;" />

* **/bin**：bin 是 Binaries (二进制文件) 的缩写, 这个目录存放着最经常使用的命令。
* **/boot**： 这里存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件。
* **/dev** **：** dev是Device(设备)的缩写, 存放的是Linux的外部设备，在Linux中访问设备的方式和访问文件的方式是相同的。
* ==**/etc**： etc 是 Etcetera(等等) 的缩写,这个目录用来存放所有的系统管理所需要的配置文件和子目录==
* ==**/home**：用户的主目录，在Linux中，每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。==
* **/lib**：lib 是 Library(库) 的缩写这个目录里存放着系统最基本的动态连接共享库，其作用类似于 Windows 里的 DLL 文件。几乎所有的应用程序都需要用到这些共享库。
* **/lost+found**：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件

- **/media**：linux 系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux 会把识别的设备挂载到这个目录下。

- **/mnt**：系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在 /mnt/ 上，然后进入该目录就可以查看光驱里的内容了。

- ==**/opt**：opt 是 optional(可选) 的缩写，这是给主机额外安装软件所摆放的目录。比如你安装一个ORACLE数据库则就可以放到这个目录下。默认是空的。==

* **/proc**：proc 是 Processes(进程) 的缩写，/proc 是一种伪文件系统（也即虚拟文件系统），存储的是当前内核运行状态的一系列特殊文件，这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。

  这个目录的内容不在硬盘上而是在内存里，我们也可以直接修改里面的某些文件，比如可以通过下面的命令来屏蔽主机的ping命令，使别人无法ping你的机器：

  ```bash
  echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
  ```

- **/root**：该目录为系统管理员，也称作超级权限者的用户主目录。

- **/sbin**：s 就是 Super User 的意思，是 Superuser Binaries (超级用户的二进制文件) 的缩写，这里存放的是系统管理员使用的系统管理程序。

- **/selinux**：
   这个目录是 Redhat/CentOS 所特有的目录，Selinux 是一个安全机制，类似于 windows 的防火墙，但是这套机制比较复杂，这个目录就是存放selinux相关的文件的。

- **/srv**：
   该目录存放一些服务启动之后需要提取的数据。

- **/sys**：

  这是 Linux2.6 内核的一个很大的变化。该目录下安装了 2.6 内核中新出现的一个文件系统 sysfs 。

  sysfs 文件系统集成了下面3种文件系统的信息：针对进程信息的 proc 文件系统、针对设备的 devfs 文件系统以及针对伪终端的 devpts 文件系统。

  该文件系统是内核设备树的一个直观反映。

  当一个内核对象被创建的时候，对应的文件和目录也在内核对象子系统中被创建。

- ==**/tmp**：
  tmp 是 temporary(临时) 的缩写这个目录是用来存放一些临时文件的。==
- ==**/usr**：
   usr 是 unix shared resources(共享资源) 的缩写，这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于 windows 下的 program files 目录。==

- **/usr/bin：**
  系统用户使用的应用程序。

- **/usr/sbin：**
  超级用户使用的比较高级的管理程序和系统守护程序。

- **/usr/src：**
  内核源代码默认的放置目录。

- ==**/var**：
  var 是 variable(变量) 的缩写，这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件。==

- **/run**：
  是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。如果你的系统上有 /var/run 目录，应该让它指向 run。

- **/www**：使用远程服务器时有的，存放服务器网站相关的资源，环境，网站的项目**

  <img src="/img/image/image-20220802121600916.png" alt="image-20220802121600916" style="zoom:80%;" />

在 Linux 系统中，有几个目录是比较重要的，平时需要注意不要误删除或者随意更改内部文件。

**/etc**： 上边也提到了，这个是系统中的配置文件，如果你更改了该目录下的某个文件可能会导致系统不能启动。

**/bin, /sbin, /usr/bin, /usr/sbin**: 这是系统预设的执行文件的放置目录，比如 **ls** 就是在 **/bin/ls** 目录下的。

值得提出的是 **/bin**、**/usr/bin** 是给系统用户使用的指令（除 root 外的通用用户），而/sbin, /usr/sbin 则是给 root 使用的指令。

**/var**： 这是一个非常重要的目录，系统上跑了很多程序，那么每个程序都会有相应的日志产生，而这些日志就被记录到这个目录下，具体在 /var/log 目录下，另外 mail 的预设放置也是在这里。

## 目录管理

==**使用tab建自动补全当前目录存在的文件名**==

### 绝对路径和相对路径

* **绝对路径：路径的全称**（`/`）(当前用户：`~`)

  路径的写法，由根目录 / 写起，例如：/usr/share/doc 这个目录。

* **相对路径：对于当前目录**（`..`相当于返回上级目录）

  路径的写法，不是由 / 写起，例如由 /usr/share/doc 要到 /usr/share/man 底下时，可以写成：cd ../man 这就是相对路径的写法

<img src="/img/image/image-20220802122834286.png" alt="image-20220802122834286" style="zoom:67%;" />

我们知道Linux的目录结构为树状结构，最顶级的目录为根目录 /。

其他目录通过挂载可以将它们添加到树中，通过解除挂载可以移除它们。

```shell
cd：切换目录命令；
./ :当前目录；
..：上一级目录；
clear:清除
```

<img src="/img/image/image-20220802122422011.png" alt="image-20220802122422011" style="zoom: 67%;" />

<img src="/img/image/image-20220802122435662.png" alt="image-20220802122435662" style="zoom: 67%;" />

### 处理目录的常用命令

接下来我们就来看几个常见的处理目录的命令吧：

```shell
•	ls: 列出目录
•	cd：切换目录
•	pwd：显示目前的目录
•	mkdir：创建一个新的目录
•	rmdir：删除一个空的目录
•	cp: 复制文件或目录
•	rm: 移除文件或目录
•	mv: 移动文件与目录，或修改文件与目录的名称
```

你可以使用 *man [**命令**]* 来查看各个命令的使用文档，如 ：man cp。

### ls （列出目录）

ls（英文全拼：list files）命令用于显示指定工作目录下之内容（列出目前工作目录所含之文件及子目录)。

语法：

[root@www ~]# ls [-aAdfFhilnrRSt] 目录名称

选项与参数：

* -a ：全部的文件，连同隐藏文件( 开头为 . 的文件) 一起列出来(常用)
* -l ：长数据串列出，包含文件的属性与权限等等数据；(常用)

将目录下的所有文件列出来(含属性与隐藏档)

```java 
[root@www ~]# ls -al ~
```

### cd （切换目录）

cd是Change Directory的缩写，这是用来变换工作目录的命令。

语法：

```shell
cd [相对路径或绝对路径]
```

### pwd ( 显示目前所在的目录 )

pwd 是 **Print Working Directory** 的缩写，也就是显示目前所在目录的命令。

```bash
[root@kuangshen kuangstudy]#pwd [-P]
```

选项与参数：**-P** ：显示出确实的路径，而非使用连接(link) 路径。

```shell
# 单纯显示出目前的工作目录
[root@kuangshen ~]# pwd
/root

# 如果是链接，要显示真实地址，可以使用 -P参数
[root@kuangshen /]# cd bin
[root@kuangshen bin]# pwd -P
/usr/bin
```

### mkdir （创建新目录）

如果想要创建新的目录的话，那么就使用mkdir (make directory)

```shell
mkdir [-mp] 目录名称
```

选项与参数：

* -m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色~
* -p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来

```shell
# 进入我们用户目录下
[root@kuangshen /]# cd /home
# 创建一个 test 文件夹
[root@kuangshen home]# mkdir test
# 创建多层级目录
[root@kuangshen home]# mkdir test1/test2/test3/test4
mkdir: cannot create directory ‘test1/test2/test3/test4’:
No such file or directory  # <== 没办法直接创建此目录啊！

# 加了这个 -p 的选项，可以自行帮你创建多层目录！
[root@kuangshen home]# mkdir -p test1/test2/test3/test4

# 创建权限为 rwx--x--x 的目录。
[root@kuangshen home]# mkdir -m 711 test2
[root@kuangshen home]# ls -l
drwxr-xr-x 2 root root  4096 Mar 12 21:55 test
drwxr-xr-x 3 root root  4096 Mar 12 21:56 test1
drwx--x--x 2 root root  4096 Mar 12 21:58 test2
```

### rmdir ( 删除空的目录 )

语法：

```shell
rmdir [-p] 目录名称
```

选项与参数：**-p** **：**连同上一级『空的』目录也一起删除

```java
# 看看有多少目录存在？
[root@kuangshen home]# ls -l
drwxr-xr-x 2 root root  4096 Mar 12 21:55 test
drwxr-xr-x 3 root root  4096 Mar 12 21:56 test1
drwx--x--x 2 root root  4096 Mar 12 21:58 test2
# 可直接删除掉，没问题
[root@kuangshen home]# rmdir test
# 因为尚有内容，所以无法删除！
[root@kuangshen home]# rmdir test1
rmdir: failed to remove ‘test1’: Directory not empty
# 利用 -p 这个选项，立刻就可以将 test1/test2/test3/test4 依次删除。
[root@kuangshen home]# rmdir -p test1/test2/test3/test4
```

注意：这个 rmdir 仅能删除空的目录，可以使用 rm 命令来删除非空目录

### cp ( 复制文件或目录 )

语法：

```shell
cp [-adfilprsu] 来源档(source) 目标档(destination)
cp [options] source1 source2 source3 .... directory
```

选项与参数：

- **-a**：此选项通常在复制目录时使用，它保留链接、文件属性，并复制目录下的所有内容。其作用等于dpR参数组合。
- **-d**：复制时保留链接，复制链接属性而非文件本身。这里所说的链接相当于 Windows 系统中的快捷方式。
- **-f**：为强制(force)的意思，覆盖已经存在的目标文件而不给出提示。
- **-i**：与 **-f** 选项相反，在覆盖目标文件之前给出提示，要求用户确认是否覆盖，回答 **y** 时目标文件将被覆盖。
- **-p**：除复制文件的内容外，还把修改时间和访问权限也复制到新文件中
- **-r**：若给出的源文件是一个目录文件，此时将复制该目录下所有的子目录和文件。
- **-l**：不复制文件，只是生成链接文件。

```shell
# 找一个有文件的目录，我这里找到 root目录
[root@kuangshen home]# cd /root
[root@kuangshen ~]# ls
install.sh
[root@kuangshen ~]# cd /home
# 复制 root目录下的install.sh 到 home目录下
[root@kuangshen home]# cp /root/install.sh /home
[root@kuangshen home]# ls
install.sh
# 再次复制，加上-i参数，增加覆盖询问？
[root@kuangshen home]# cp -i /root/install.sh /home
cp: overwrite ‘/home/install.sh’? y # n不覆盖，y为覆盖
```

### rm ( 移除文件或目录 )

语法：

```shell
rm [-fir] 文件或目录
```

选项与参数:

* -f ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；
* -i ：互动模式，在删除前会询问使用者是否动作
* -r ：递归删除！最常用在目录的删除了！这是非常危险的选项！！！

```shell
# 将刚刚在 cp 的实例中创建的 install.sh删除掉！
[root@kuangshen home]# rm -i install.sh
rm: remove regular file ‘install.sh’? y
# 如果加上 -i 的选项就会主动询问喔，避免你删除到错误的档名！

# 尽量不要在服务器上使用 rm -rf /
# 系统中所有的文件就被删除了，删库跑路就是这么操作的！！！
```

### mv  ( 移动文件与目录，或修改名称 )

语法：

```shell
mv [-fiu] source destination
mv [options] source1 source2 source3 .... directory
```

选项与参数：

* -f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；

* -i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！

* -u ：若目标文件已经存在，且source 比较新，才会更新 (update)

```java
# 复制一个文件到当前目录
[root@kuangshen home]# cp /root/install.sh /home

# 创建一个文件夹 test
[root@kuangshen home]# mkdir test
# 将复制过来的文件移动到我们创建的目录，并查看
[root@kuangshen home]# mv install.sh test
[root@kuangshen home]# ls
test
[root@kuangshen home]# cd test
[root@kuangshen test]# ls
install.sh

# 将文件夹重命名，然后再次查看！
[root@kuangshen test]# cd ..
[root@kuangshen home]# mv test mvtest
[root@kuangshen home]# ls
mvtest
```

# Linux**基本属性**

## 文件或目录基本属性

Linux系统是一种典型的多用户系统，不同的用户处于不同的地位，拥有不同的权限。为了保护系统的安全性，Linux系统对不同的用户访问同一文件（包括目录文件）的权限做了不同的规定。

在Linux中我们可以使用`ll`或者`ls –l`命令来显示一个文件的属性以及文件所属的用户和组，如：

<img src="/img/image/image-20220802130049014.png" alt="image-20220802130049014" style="zoom: 67%;" />

## 权限属性

在Linux中第一个字符代表这个文件是目录、文件或链接文件等等：

- 当为[ **d** ]则是目录
- 当为[ **-** ]则是文件；
- 若是[ **l** ]则表示为链接文档 ( link file )；
- 若是[ **b** ]则表示为装置文件里面的可供储存的接口设备 ( 可随机存取装置 )；
- 若是[ **c** ]则表示为装置文件里面的串行端口设备，例如键盘、鼠标 ( 一次性读取装置 )。

实例中，boot文件的第一个属性用"d"表示。"d"在Linux中代表该文件是一个目录文件。

接下来的字符中，以三个为一组，且均为『rwx』 的三个参数的组合。

其中，[ r ]代表可读(read)、[ w ]代表可写(write)、[ x ]代表可执行(execute)。

要注意的是，这三个权限的位置不会改变，如果没有权限，就会出现减号[ - ]而已。

每个文件的属性由左边第一部分的10个字符来确定（如下图）：

<img src="/img/image/image-20220802130453834.png" alt="image-20220802130453834" style="zoom: 67%;" />

* 属主权限：第1-3位确定属主（该文件的所有者）拥有该文件的权限
* 属组权限：第4-6位确定属组（所有者的同组用户）拥有该文件的权限
* 其他用户权限：第7-9位确定其他用户拥有该文件的权限。

第1、4、7位表示读权限，如果用"r"字符表示，则有读权限，如果用"-"字符表示，则没有读权限；

第2、5、8位表示写权限，如果用"w"字符表示，则有写权限，如果用"-"字符表示没有写权限；

第3、6、9位表示可执行权限，如果用"x"字符表示，则有执行权限，如果用"-"字符表示，则没有执行权限。

对于文件来说，它都有一个特定的所有者，也就是对该文件具有所有权的用户。

同时，在Linux系统中，用户是按组分类的，一个用户属于一个或多个组。

文件所有者以外的用户又可以分为文件所有者的同组用户和其他用户。

因此，Linux系统按文件所有者、文件所有者同组用户和其他用户来规定了不同的文件访问权限。

在以上实例中，boot 文件是一个目录文件，属主和属组都为 root。

## 修改文件属性

**chgrp：更改文件属组**

```shell
chgrp [-R] 属组名 文件名
```

-R：递归更改文件属组，就是在更改某个目录文件的属组时，如果加上-R的参数，那么该目录下的所有文件的属组都会更改。

**chown：更改文件属主，也可以同时更改文件属组**

```shell
chown [–R] 属主名 文件名
chown [-R] 属主名：属组名 文件名
```

**chmod：更改文件9个属性**

```shell
chmod [-R] xyz 文件或目录
```

Linux文件属性有两种设置方法，一种是数字（常用），一种是符号。

Linux文件的基本权限就有九个，分别是`owner/group/others`三种身份各有自己的`read/write/execute`权限

文件的权限字符为：『-rwxrwxrwx』， 这九个权限是三个三个一组的！其中，我们可以使用数字来代表各个权限，各权限的分数对照表如下：

| r:4  | w:2  | x:1      | 等式   |
| ---- | ---- | -------- | ------ |
| 可读 | 可写 | 不可执行 | rw- =6 |
| 可读 | 可写 | 可执行   | rwx=7  |

每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的，例如当权限为：[-rwxrwx---] 分数则是：

- owner = rwx = 4+2+1 = 7
- group = rwx = 4+2+1 = 7
- others= --- = 0+0+0 = 0

<img src="/img/image/image-20220802131804511.png" alt="image-20220802131804511" style="zoom:67%;" />

## 文件内容查看

Linux系统中使用以下命令来查看文件的内容：

```shell
•	cat 由第一行开始显示文件内容\
•	tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！
•	nl  显示的时候，顺道输出行号！
•	more 一页一页的显示文件内容
•	less 与 more 类似，但是比 more 更好的是，他可以往前翻页！
•	head 只看头几行
•	tail 只看尾巴几行
```

==你可以使用 *man [**命令**]*来查看各个命令的使用文档，如 ：man cp。==

### cat

语法：

```shell
cat [-AbEnTv]
```

选项与参数：

选项与参数：

* -A ：相当於 -vET 的整合选项，可列出一些特殊字符而不是空白而已；
* -b ：列出行号，仅针对非空白行做行号显示，空白行不标行号！
* -E ：将结尾的断行字节 $ 显示出来；
* -n ：列印出行号，连同空白行也会有行号，与 -b 的选项不同；
* -T ：将 [tab] 按键以 ^I 显示出来；
* -v ：列出一些看不出来的特殊字符

```java
# 查看网络配置: 文件地址 /etc/sysconfig/network-scripts/
[root@kuangshen ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=dhcp
ONBOOT=yes
```

### tac

tac与cat命令刚好相反，文件内容从最后一行开始显示，可以看出 tac 是 cat 的倒着写

```java
[root@kuangshen ~]# tac /etc/sysconfig/network-scripts/ifcfg-eth0
ONBOOT=yes
BOOTPROTO=dhcp
DEVICE=eth0
```

### nl 

nl  显示行号

语法：

nl [-bnw] 文件

选项与参数：

* -b ：指定行号指定的方式，主要有两种：-b a ：表示不论是否为空行，也同样列出行号(类似 cat -n)；-b t ：如果有空行，空的那一行不要列出行号(默认值)；
* -n ：列出行号表示的方法，主要有三种：-n ln     ：行号在荧幕的最左方显示；-n rn     ：行号在自己栏位的最右方显示，且不加 0 ；-n rz ：行号在自己栏位的最右方显示，且加 0 ；
* -w ：行号栏位的占用的位数。

测试：

```shell
[root@kuangshen ~]# nl /etc/sysconfig/network-scripts/ifcfg-eth0
1 DEVICE=eth0
2 BOOTPROTO=dhcp
3 ONBOOT=yes
```

### more  

more  一页一页翻动

在 more 这个程序的运行过程中，你有几个按键可以按的：

* 空白键 (space)：代表向下翻一页；
* Enter   ：代表向下翻『一行』；
* /字串   ：代表在这个显示的内容当中，向下搜寻『字串』这个关键字；
* :f    ：立刻显示出档名以及目前显示的行数；
* q    ：代表立刻离开 more ，不再显示该文件内容。
* b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。

```shell
[root@kuangshen etc]# more /etc/csh.login
....(中间省略)....
--More--(28%) # 重点在这一行喔！你的光标也会在这里等待你的命令

```

<img src="/img/image/image-20220803205117299.png" alt="image-20220803205117299" style="zoom:80%;" />

### less  

less  一页一页翻动，以下实例输出/etc/man.config文件的内容：

* 空白键  ：向下翻动一页；
* [pagedown]：向下翻动一页；
* /字串  ：向下搜寻『字串』的功能；
* ?字串  ：向上搜寻『字串』的功能；
* n   ：重复前一个搜寻 (与 / 或 ? 有关！)
* N   ：反向的重复前一个搜寻 (与 / 或 ? 有关！)
* q   ：离开 less 这个程序；

```shell
[root@kuangshen etc]# more /etc/csh.login
....(中间省略)....
:   # 这里可以等待你输入命令！
```

### head  

head  取出文件前面几行

语法：

```shell
head [-n number] 文件
```

选项与参数：**-n** 后面接数字，代表显示几行的意思

默认的情况中，显示前面 10 行！若要显示前 20 行，就得要这样：

```shell
[root@kuangshen etc]# head -n 20 /etc/csh.login
```

### tail  

tail  取出文件后面几行

语法：

```shell
tail [-n number] 文件
```

选项与参数：

- -n ：后面接数字，代表显示几行的意思

默认的情况中，显示最后 10 行！若要显示最后 20 行，就得要这样：

```shell
[root@kuangshen etc]# tail -n 20 /etc/csh.login
```

## Linux 链接概念

Linux 链接分两种，一种被称为硬链接（Hard Link），另一种被称为符号链接（Symbolic Link）。

情况下，`ln `命令产生硬链接。

### **硬连接**

硬连接指通过索引节点来进行连接。

在 Linux 的文件系统中，保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。

在 Linux 中，多个文件名指向同一索引节点是存在的。比如：A 是 B 的硬链接（A 和 B 都是文件名），则 A 的目录项中的 inode 节点号与 B 的目录项中的 inode 节点号相同，即一个 inode 节点对应两个不同的文件名，两个文件名指向同一个文件，A 和 B 对文件系统来说是完全平等的。删除其中任何一个都不会影响另外一个的访问。

硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。

其原因如上所述，因为对应该目录的索引节点有一个以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，==只有当最后一个硬连接被删除后，文件的数据块及目录的连接才会被释放==。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均被删除。

### **软连接**

另外一种连接称之为符号连接（Symbolic Link），也叫软连接。软链接文件有类似于 Windows 的快捷方式。它实际上是一个特殊的文件。在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。比如：A 是 B 的软链接（A 和 B 都是文件名），A 的目录项中的 inode 节点号与 B 的目录项中的 inode 节点号不相同，A 和 B 指向的是两个不同的 inode，继而指向两块不同的数据块。但是 A 的数据块中存放的只是 B 的路径名（可以根据这个找到 B 的目录项）。A 和 B 之间是“主从”关系，如果 B 被删除了，A 仍然存在（因为两个是不同的文件），但指向的是一个无效的链接。

# Linux文件管理

## 创建文件

touch:创建文件

**测试：**

```bash
[root@kuangshen /]# cd /home
[root@kuangshen home]# touch f1 # 创建一个测试文件f1
[root@kuangshen home]# ls
f1
[root@kuangshen home]# ln f1 f2     # 创建f1的一个硬连接文件f2
[root@kuangshen home]# ln -s f1 f3   # 创建f1的一个符号(软)连接文件f3
[root@kuangshen home]# ls -li       # -i参数显示文件的inode节点信息
397247 -rw-r--r-- 2 root root     0 Mar 13 00:50 f1
397247 -rw-r--r-- 2 root root     0 Mar 13 00:50 f2
397248 lrwxrwxrwx 1 root root     2 Mar 13 00:50 f3 -> f1
```

从上面的结果中可以看出，硬连接文件 f2 与原文件 f1 的 inode 节点相同，均为 397247，然而符号连接文件的 inode 节点不同。

## 命令输入字符

`echo 输入字符串`

```shell
# echo 字符串输出 >> f1 输出到 f1文件
[root@kuangshen home]# echo "I am f1 file" >>f1
[root@kuangshen home]# cat f1
I am f1 file
[root@kuangshen home]# cat f2
I am f1 file
[root@kuangshen home]# cat f3
I am f1 file
[root@kuangshen home]# rm -f f1
[root@kuangshen home]# cat f2
I am f1 file
[root@kuangshen home]# cat f3
cat: f3: No such file or directory
```

通过上面的测试可以看出：当删除原始文件 f1 后，硬连接 f2 不受影响，但是符号连接 f1 文件无效；

依此您可以做一些相关的测试，可以得到以下全部结论：

- 删除符号连接f3,对f1,f2无影响；
- 删除硬连接f2，对f1,f3也无影响；
- 删除原文件f1，对硬连接f2没有影响，导致符号连接f3失效；
- 同时删除原文件f1,硬连接f2，整个文件会真正的被删除。

#  Linux系统网络

## ping

 ping：基本使用详解（windows,Linux）

在网络中ping是一个十分强大的TCP/IP工具。它的作用主要为：

（1）用来检测网络的连通情况和分析网络速度；

（2）根据域名得到服务器IP；

（3）根据ping返回的TTL值来判断对方所使用的操作系统及数据包经过路由器数量。

<img src="/img/image/image-20220803202233690.png" alt="image-20220803202233690" style="zoom:80%;" />

## ifconfig

`Crt+C退出执行`

ipconfig：查看网络配置信息（windows）

ifconfig: 查看网络配置信息（Linux）

<img src="/img/image/image-20220803202344798.png" alt="image-20220803202344798" style="zoom:80%;" />

linux中etc/sysconfig/network-scripts包含有网络配置文件

<img src="/img/image/image-20220803203224964.png" alt="image-20220803203224964" style="zoom:80%;" />

# Vim

Vim是从 vi 发展出来的一个文本编辑器。代码补完、编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用。（vim通过一些插件可以实现和IDE一样的功能）

简单的来说， vi 是老式的字处理器，不过功能已经很齐全了，但是还是有可以进步的地方。

vim 则可以说是程序开发者的一项很好用的工具。

所有的 Unix Like 系统都会内建 vi 文书编辑器，其他的文书编辑器则不一定会存在。

连 vim 的官方网站 (http://www.vim.org) 自己也说 vim 是一个程序开发工具而不是文字处理软件。

vim 键盘图：

<img src="/img/image/image-20220803211354085.png" alt="image-20220803211354085" style="zoom:80%;" />

基本上 vi/vim 共分为三种模式，分别是**命令模式（Command mode），输入模式（Insert mode）和底线命令模式（Last line mode）**。这三种模式的作用分别是：

## 命令模式

用户刚刚启动 vi/vim，便进入了命令模式。

此状态下敲击键盘动作会被Vim识别为命令，而非输入字符。比如我们此时按下i，并不会输入一个字符，i被当作了一个命令。

以下是常用的几个命令：

* **i** 切换到输入模式，以输入字符。
* **x** 删除当前光标所在处的字符。
* **:**切换到底线命令模式，以在最底一行输入命令。如果是编辑模式，要ESC退出该模式后进入

若想要编辑文本：启动Vim，进入了命令模式，按下i，切换到输入模式。

命令模式只有一些最基本的命令，因此仍要依靠底线命令模式输入更多命令。

## 输入模式

在命令模式下按下i就进入了输入模式。

在输入模式中，可以使用以下按键：

* **字符按键以及Shift组合**，输入字符
*  **ENTER**，回车键，换行
* **BACK SPACE**，退格键，删除光标前一个字符
* **DEL**，删除键，删除光标后一个字符
* **方向键**，在文本中移动光标
*  **HOME**/**END**，移动光标到行首/行尾
* **Page Up**/**Page Down**，上/下翻页
* **Insert**，切换光标为输入/替换模式，光标将变成竖线/下划线
* **ESC**，退出输入模式，切换到命令模式

## 底线命令模式

在命令模式下按下:（英文冒号）就进入了底线命令模式。

底线命令模式可以输入单个或多个字符的命令，可用的命令非常多。

在底线命令模式中，基本的命令有（已经省略了冒号）：

* q 退出程序
* w 保存文件

按ESC键可随时退出底线命令模式。

简单的说，我们可以将这三个模式想成底下的图标来表示：

<img src="/img/image/image-20220803212830421.png" alt="image-20220803212830421" style="zoom:80%;" />

<img src="/img/image/image-20220803212916378.png" alt="image-20220803212916378" style="zoom:80%;" />

# 账号管理

## 简介

Linux系统是一个多用户多任务的分时操作系统，任何一个要使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。

用户的账号一方面可以帮助系统管理员对使用系统的用户进行跟踪，并控制他们对系统资源的访问；另一方面也可以帮助用户组织文件，并为用户提供安全性保护。

每个用户账号都拥有一个唯一的用户名和各自的口令。

用户在登录时键入正确的用户名和口令后，就能够进入系统和自己的主目录。

实现用户账号的管理，要完成的工作主要有如下几个方面：

* 用户账号的添加、删除与修改。
* 用户口令的管理。
* 用户组的管理。

## 用户账号的管理

用户账号的管理工作主要涉及到用户账号的添加、修改和删除。

添加用户账号就是在系统中创建一个新账号，然后为新账号分配用户号、用户组、主目录和登录Shell等资源。

属主，属组

### 添加账号

```shell
useradd 选项 用户名(登录名)
```

参数说明：

选项 :

* **-c:**comment 指定一段注释性描述。
* **-d:** 目录 指定用户主目录，如果此目录不存在，则同时使用-m选项，可以创建主目录
* **-g:** 用户组, 指定用户所属的用户组。
* **-G:** 用户组，指定用户所属的附加组。
* **-m:**使用者目录如不存在则自动建立。自动在home目录下创建当前新增用户的目录
* **-s:** Shell文件 指定用户的登录Shell。
* **-u:** 用户号 指定用户的用户号，如果同时有-o选项，则可以重复使用其他用户的标识号。

测试：

```shell
# 此命令创建了一个用户kuangshen，其中-m选项用来为登录名kuangshen产生一个主目录 /home/kuangshen
[root@kuangshen home]# useradd -m kuangshen
```

增加用户账号就是在/etc/passwd文件中为新用户增加一条记录，同时更新其他系统文件如/etc/shadow, /etc/group等。

Linux中一切皆文件，新增用户本质就是往某一文件中写入用户信息！

```shell 
cat /etc/passwd #查看用户信息
```

### 切换用户

1. 切换用户的命令为：su username 【username是你的用户名】
2. 从普通用户切换到root用户，还可以使用命令：`sudo su`
3. 在终端输入exit或logout或使用快捷方式ctrl+d，可以退回到原来用户，其实ctrl+d也是执行的exit命令
4. 在切换用户时，如果想在切换用户之后使用新用户的工作环境，可以在su和username之间加-，例如：【su - root】

`$`表示普通用户

`#`表示超级用户，也就是root用户

<img src="/img/image/image-20220803215421399.png" alt="image-20220803215421399" style="zoom:80%;" />

<img src="/img/image/image-20220803215439959.png" alt="image-20220803215439959" style="zoom:80%;" />

![image-20220803215501570](/img/image/image-20220803215501570.png)

### 删除帐号

如果一个用户的账号不再使用，可以从系统中删除。

删除用户账号就是要将/etc/passwd等系统文件中的该用户记录删除，必要时还删除用户的主目录。

删除一个已有的用户账号使用userdel命令，其格式如下：

```shell
userdel 选项 用户名
```

常用的选项是 **-r**，它的作用是把用户的主目录一起删除。

```java
此命令删除用户kuangshen在系统文件中（主要是/etc/passwd, /etc/shadow, /etc/group等）的记录，同时删除用户的主目录。
[root@kuangshen home]# userdel -r kuangshen
```

### 修改帐号

修改用户账号就是根据实际情况更改用户的有关属性，如用户号、主目录、用户组、登录Shell等。

修改已有用户的信息使用`usermod`命令，其格式如下：

```shell
usermod 选项 用户名
```

常用的选项包括-c, -d, -m, -g, -G, -s, -u以及-o等，这些选项的意义与useradd命令中的选项一样，可以为用户指定新的资源值。

例如：

```shell
此命令将用户kuangshen的登录Shell修改为ksh，主目录改为/home/z，用户组改为developer。
usermod -s /bin/ksh -d /home/z –g developer kuangshen
```

### 用户口令的管理

用户口令的管理即用户的密码设置，Linux上输入密码是不会显示的

用户管理的一项重要内容是用户口令的管理。用户账号刚创建时没有口令，但是被系统锁定，无法使用，必须为其指定口令后才可以使用，即使是指定空口令。

指定和修改用户口令的Shell命令是passwd。超级用户可以为自己和其他用户指定口令，普通用户只能用它修改自己的口令。

注意在root权限下切换普通用户是不用输入密码的，若要测试的话可以新建连接输入普通用户的用户名

<img src="/img/image/image-20220803220020616.png" alt="image-20220803220020616" style="zoom:80%;" />

命令的格式为：

```shell
passwd 选项 用户名
```

可使用的选项：

*  -l 锁定口令，即禁用账号。
*  -u 口令解锁。
*  -d 使账号无口令。
*  -f 强迫用户下次登录时修改口令。

如果默认用户名，则修改当前用户的口令。

例如，假设当前用户是kuangshen，则下面的命令修改该用户自己的口令：

```shell
$ passwd
Old password:******
New password:*******
Re-enter new password:*******
```

如果是超级用户，可以用下列形式指定任何用户的口令：

```shell
# passwd kuangshen
New password:*******
Re-enter new password:*******
```

普通用户修改自己的口令时，passwd命令会先询问原口令，验证后再要求用户输入两遍新口令，如果两次输入的口令一致，则将这个口令指定给用户；而超级用户为用户指定口令时，就不需要知道原口令。

为了系统安全起见，用户应该选择比较复杂的口令，例如最好使用8位长的口令，口令中包含有大写、小写字母和数字，并且应该与姓名、生日等不相同。

为用户指定空口令时，执行下列形式的命令：

```shell
passwd -d kuangshen  #清空密码并且冻结账户
```

此命令将用户 kuangshen的口令删除，这样用户 kuangshen下一次登录时，系统就不再允许该用户登录了。

passwd 命令还可以用 -l(lock) 选项锁定某一用户，使其不能登录，例如：

```shell
passwd -l kuangshen
```

<img src="/img/image/image-20220803220341003.png" alt="image-20220803220341003" style="zoom:80%;" />

## 用户组管理

每个用户都有一个用户组，系统可以对一个用户组中的所有用户进行集中管理。不同Linux 系统对用户组的规定有所不同，如Linux下的用户属于与它同名的用户组，这个用户组在创建用户时同时创建。

用户组的管理涉及用户组的添加、删除和修改。组的增加、删除和修改实际上就是对`/etc/group`文件的更新。

### 新增用户组

```shell
groupadd 选项 用户组
```

测试

```shell
[root@kuangshen home] groupadd pzw
[root@kuangshen home] cat /etc/group
```

<img src="/img/image/image-20220804205126668.png" alt="image-20220804205126668" style="zoom:50%;" />

创建完用户组后可以得到一个组的id，这个id是可以指定的， -g 520 如果不指定则自增

可以使用的选项有：

* **-g GID** 指定新用户组的组标识号（GID）。
* **-o** 一般与-g选项同时使用，表示新用户组的GID可以与系统已有用户组的GID相同。

实例1：

```shell
groupadd group1
```

此命令向系统中增加了一个新组group1，新组的组标识号是在当前已有的最大组标识号的基础上加1。

实例2：

```shell
groupadd -g 101 group2
```

此命令向系统中增加了一个新组group2，同时指定新组的组标识号是101。

### 删除用户组

```shell
groupdel 用户组
```

例如：

```shell
groupdel group1
```

此命令从系统中删除组group1。

### 修改用户组

修改用户组的属性使用groupmod命令

```shell
groupmod 选项 用户组
```

常用的选项有：

* **-g GID** 为用户组指定新的组标识号。
* **-o** 与-g选项同时使用，用户组的新GID可以与系统已有用户组的GID相同。
* **-n**新用户组 将用户组的名字改为新名字

```shell
# 此命令将组group2的组标识号修改为102。
groupmod -g 102 group2

# 将组group2的标识号改为10000，组名修改为group3。
groupmod –g 10000 -n group3 group2
```

## 切换用户组

如果一个用户同时属于多个用户组，那么用户可以在用户组之间切换，以便具有其他用户组的权限。

用户可以在登录后，使用命令newgrp切换到其他用户组，这个命令的参数就是目的用户组。例如：

```shell
$ newgrp root
```

这条命令将当前用户切换到root用户组，前提条件是root用户组确实是该用户的主组或附加组。

## /etc/passwd

完成用户管理的工作有许多种方法，但是每一种方法实际上都是对有关的系统文件进行修改。

与用户和用户组相关的信息都存放在一些系统文件中，这些文件包括/etc/passwd, /etc/shadow, /etc/group等。

下面分别介绍这些文件的内容。

`/etc/passwd文件是用户管理工作涉及的最重要的一个文件。`

Linux系统中的每个用户都在/etc/passwd文件中有一个对应的记录行，它记录了这个用户的一些基本属性。

这个文件对所有用户都是可读的。它的内容类似下面的例子：

```shell
cat /etc/passwd

root:x:0:0:Superuser:/:
daemon:x:1:1:System daemons:/etc:
bin:x:2:2:Owner of system commands:/bin:
sys:x:3:3:Owner of system files:/usr/sys:
adm:x:4:4:System accounting:/usr/adm:
uucp:x:5:5:UUCP administrator:/usr/lib/uucp:
auth:x:7:21:Authentication administrator:/tcb/files/auth:
cron:x:9:16:Cron daemon:/usr/spool/cron:
listen:x:37:4:Network daemon:/usr/net/nls:
lp:x:71:18:Printer administrator:/usr/spool/lp:
```

从上面的例子我们可以看到，/etc/passwd中一行记录对应着一个用户，每行记录又被冒号(:)分隔为7个字段，其格式和具体含义如下：

```shell
用户名:口令(登录密码，不可见，显示X):用户标识号:组标识号:注释性描述:主目录:登录Shell
```

* **用户名**：是代表用户账号的字符串。

  通常长度不超过8个字符，并且由大小写字母和/或数字组成。登录名中不能有冒号(:)，因为冒号在这里是分隔符。为了兼容起见，登录名中最好不要包含点字符(.)，并且不使用连字符(-)和加号(+)打头。

* **口令**：一些系统中，存放着加密后的用户口令字。

  虽然这个字段存放的只是用户口令的加密串，不是明文，但是由于/etc/passwd文件对所有用户都可读，所以这仍是一个安全隐患。因此，现在许多Linux 系统（如SVR4）都使用了shadow技术，把真正的加密后的用户口令字存放到/etc/shadow文件中，而在/etc/passwd文件的口令字段中只存放一个特殊的字符，例如“x”或者“*”。

* **用户标识号**：是一个整数，系统内部用它来标识用户。

  一般情况下它与用户名是一一对应的。如果几个用户名对应的用户标识号是一样的，系统内部将把它们视为同一个用户，但是它们可以有不同的口令、不同的主目录以及不同的登录Shell等。

  通常用户标识号的取值范围是0～65 535。0是超级用户root的标识号，1～99由系统保留，作为管理账号，普通用户的标识号从100开始。在Linux系统中，这个界限是500。

* **组标识号**：字段记录的是用户所属的用户组。

  它对应着/etc/group文件中的一条记录。

* **注释性描述**：字段记录着用户的一些个人情况。

  例如用户的真实姓名、电话、地址等，这个字段并没有什么实际的用途。在不同的Linux 系统中，这个字段的格式并没有统一。在许多Linux系统中，这个字段存放的是一段任意的注释性描述文字，用作finger命令的输出。

* **主目录**:也就是用户的起始工作目录。

  它是用户在登录到系统之后所处的目录。在大多数系统中，各用户的主目录都被组织在同一个特定的目录下，而用户主目录的名称就是该用户的登录名。各用户对自己的主目录有读、写、执行（搜索）权限，其他用户对此目录的访问权限则根据具体情况设置。

* **Shell**：用户登录后，要启动一个进程，负责将用户的操作传给内核，这个进程是用户登录到系统后运行的命令解释器或某个特定的程序，即Shell。

> Shell是用户与Linux系统之间的接口。Linux的Shell有许多种，每种都有不同的特点。常用的有sh(Bourne Shell), csh(C Shell), ksh(Korn Shell), tcsh(TENEX/TOPS-20 type C Shell), bash(Bourne Again Shell)等。
>
> 系统管理员可以根据系统情况和用户习惯为用户指定某个Shell。如果不指定Shell，那么系统使用sh为默认的登录Shell，即这个字段的值为/bin/sh。
>
> 用户的登录Shell也可以指定为某个特定的程序（此程序不是一个命令解释器）。
>
> 利用这一特点，我们可以限制用户只能运行指定的应用程序，在该应用程序运行结束后，用户就自动退出了系统。有些Linux 系统要求只有那些在系统中登记了的程序才能出现在这个字段中。
>
> 8)系统中有一类用户称为伪用户（pseudo users）。
>
> 这些用户在/etc/passwd文件中也占有一条记录，但是不能登录，因为它们的登录Shell为空。它们的存在主要是方便系统管理，满足相应的系统进程对文件属主的要求。
>
> 常见的伪用户如下所示：
>
> * bin 拥有可执行的用户命令文件
> * sys 拥有系统文件
> * adm 拥有帐户文件
> * uucp UUCP使用
> * lp lp或lpd子系统使用
> * nobody NFS使用

## /etc/shadow

除了上面列出的伪用户外，还有许多标准的伪用户，例如：audit, cron, mail, usenet等，它们也都各自为相关的进程和文件所需要。

由于/etc/passwd文件是所有用户都可读的，如果用户的密码太简单或规律比较明显的话，一台普通的计算机就能够很容易地将它破解，因此对安全性要求较高的Linux系统都把加密后的口令字分离出来，单独存放在一个文件中，这个文件是/etc/shadow文件。有超级用户才拥有该文件读权限，这就保证了用户密码的安全性。

/etc/shadow中的记录行与/etc/passwd中的一一对应，它由pwconv命令根据/etc/passwd中的数据自动产生

它的文件格式与/etc/passwd类似，由若干个字段组成，字段之间用":"隔开。这些字段是：

```shell
登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志
```

1. "口令"字段存放的是加密后的用户口令字，长度为13个字符。如果为空，则对应用户没有口令，登录时不需要口令；如果含有不属于集合 { ./0-9A-Za-z }中的字符，则对应的用户不能登录。

2. "最后一次修改时间"表示的是从某个时刻起，到用户最后一次修改口令时的天数。时间起点对不同的系统可能不一样。例如在SCO Linux 中，这个时间起点是1970年1月1日。

3. "最小时间间隔"指的是两次修改口令之间所需的最小天数。

4. "最大时间间隔"指的是口令保持有效的最大天数。

5. "警告时间"字段表示的是从系统开始警告用户到用户密码正式失效之间的天数。

6. "不活动时间"表示的是用户没有登录活动但账号仍能保持有效的最大天数。

7. "失效时间"字段给出的是一个绝对的天数，如果使用了这个字段，那么就给出相应账号的生存期。期满后，该账号就不再是一个合法的账号，也就不能再用来登录了。

## /etc/group

用户组的所有信息都存放在/etc/group文件中。

将用户分组是Linux 系统中对用户进行管理及控制访问权限的一种手段。

每个用户都属于某个用户组；一个组中可以有多个用户，一个用户也可以属于不同的组。

当一个用户同时是多个组中的成员时，在/etc/passwd文件中记录的是用户所属的主组，也就是登录时所属的默认组，而其他组称为附加组。

用户要访问属于附加组的文件时，必须首先使用newgrp命令使自己成为所要访问的组中的成员。

用户组的所有信息都存放在/etc/group文件中。此文件的格式也类似于/etc/passwd文件，由冒号(:)隔开若干个字段，这些字段有：

```shell
组名:口令:组标识号:组内用户列表
```

1. "组名"是用户组的名称，由字母或数字构成。与/etc/passwd中的登录名一样，组名不应重复。

2. "口令"字段存放的是用户组加密后的口令字。一般Linux 系统的用户组都没有口令，即这个字段一般为空，或者是*。

3. "组标识号"与用户标识号类似，也是一个整数，被系统内部用来标识组。

4. "组内用户列表"是属于这个组的所有用户的列表/b]，不同用户之间用逗号(,)分隔。这个用户组可能是用户的主组，也可能是附加组。

# 磁盘管理

## 概述

Linux磁盘管理好坏直接关系到整个系统的性能问题。

Linux磁盘管理常用命令为 df、du。

* ·   df ：列出文件系统的整体磁盘使用量
* ·   du：检查磁盘空间使用量

## df

df命令参数功能：检查文件系统的磁盘空间占用情况。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

语法：

```
df [-ahikHTm] [目录或文件名]
```

选项与参数：

* -a ：列出所有的文件系统，包括系统特有的 /proc 等文件系统；
* -k ：以 KBytes 的容量显示各文件系统；
* -m ：以 MBytes 的容量显示各文件系统；
* -h ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；
* -H ：以 M=1000K 取代 M=1024K 的进位方式；
* -T ：显示文件系统类型, 连同该 partition 的 filesystem 名称 (例如 ext3) 也列出；
*  -i ：不用硬盘容量，而以 inode 的数量来显示

测试：

```shell
# 将系统内所有的文件系统列出来！
# 在 Linux 底下如果 df 没有加任何选项
# 那么默认会将系统内所有的 (不含特殊内存内的文件系统与 swap) 都以 1 Kbytes 的容量来列出来！


# 将 /etc 底下的可用的磁盘容量以易读的容量格式显示
[root@kuangshen /]# df -h /etc
Filesystem     Size Used Avail Use% Mounted on
/dev/vda1       40G  6.3G   32G  17% /
```

<img src="/img/image/image-20220804213454310.png" alt="image-20220804213454310" style="zoom:80%;" />

## du

Linux du命令也是查看使用空间的，但是与df命令不同的是Linux du命令是对文件和目录磁盘使用的空间的查看，还是和df命令有一些区别的，这里介绍Linux du命令。

语法：

```
du [-ahskm] 文件或目录名称
```

选项与参数：

* -a ：列出所有的文件与目录容量，因为默认仅统计目录底下的文件量而已。
*  -h ：以人们较易读的容量格式 (G/M) 显示；
* -s ：列出总量而已，而不列出每个各别的目录占用容量；
*  -S ：不包括子目录下的总计，与 -s 有点差别。
*  -k ：以 KBytes 列出容量显示；
* -m ：以 MBytes 列出容量显示；

测试：

```shell
# 只列出当前目录下的所有文件夹容量（包括隐藏文件夹）:
# 直接输入 du 没有加任何选项时，则 du 会分析当前所在目录的文件与目录所占用的硬盘空间。
# 通配符 * 来代表每个目录。
# 与 df 不一样的是，du 这个命令其实会直接到文件系统内去搜寻所有的文件数据。
```

<img src="/img/image/image-20220804214033202.png" alt="image-20220804214033202" style="zoom:80%;" />

<img src="/img/image/image-20220804214049359.png" alt="image-20220804214049359" style="zoom:80%;" />

## 磁盘挂载与卸除

> Mac或者想使用Linux挂载我们的一些本地磁盘或者文件
>
> 例如，mac系统在使用U盘时（可以在Windows下使用），但不一定在mac系统下能使用，存在不兼容问题这时候就需要使用挂载到本机上

根文件系统之外的其他文件要想能够被访问，都必须通过“关联”至根文件系统上的某个目录来实现，此关联操作即为“挂载”，此目录即为“挂载点”,解除此关联关系的过程称之为“卸载”

Linux 的磁盘挂载使用`mount`命令，卸载使用`umount`命令。

<img src="/img/image/image-20220804214256488.png" alt="image-20220804214256488" style="zoom:80%;" />

磁盘挂载语法：

```
mount [-t 文件系统] [-L Label名] [-o 额外选项] [-n] 装置文件名 挂载点
```

测试：

```shell
# 将 /dev/hdc6 挂载到 /mnt/hdc6 上面！
[root@www ~]# mkdir /mnt/hdc6
[root@www ~]# mount /dev/hdc6 /mnt/hdc6
[root@www ~]# df
Filesystem           1K-blocks     Used Available Use% Mounted on
/dev/hdc6              1976312     42072   1833836   3% /mnt/hdc6
```

磁盘卸载命令 umount 语法：

```
umount [-fn] 装置文件名或挂载点
```

选项与参数：

* -f ：强制卸除！可用在类似网络文件系统 (NFS) 无法读取到的情况下；
* -n ：不升级 /etc/mtab 情况下卸除。

卸载/dev/hdc6

```shell
[root@www ~]# umount /dev/hdc6
```

> 除了这些外，安装jdk后，可以使用java中的一些命令查看信息！！！

# 进程管理

对于开发人员来说，其实Linux更多便向于使用即可

1.在Linux中，每一个程序都有自己的一个进程，每一个进程都有一个id号

2.每个进程，都会有一个父进程

3.进程可以有两种存在方式：前台，后台

4.一般的话服务都是后台运行的，基本的程序都是前台运行的

## ps

**ps** 查看当前系统中正在执行的各种进程的信息

ps -xx (ps -help/man ps 查看帮助文档)

* **-a** 显示当前终端运行的所有的进程信息(即当前的进程)
* **-u** 以用户的信息显示进程
* **-x** 显示后台运行进程的参数
* **-ef** 查看父进程的信息

`| ` 在Linux这个叫做管道符   A|B\

`grep`  查找文件中符合条件的字符串，过滤进程信息

```shell
ps -aux #查看所有的进程
ps -aux|grep mysql #查看存有mysql字符串所有的进程
ps -aux|grep redis #查看存有redis字符串所有的进程
ps -aux|grep java #查看存有java字符串所有的进程

# ps -ef 查看父进程的信息
ps -ef |grep java  #看父进程一般可以通过目录树结构查看

#进程树
pstree -pu
	-p 显示父id
	-u 显示用户组

```

 	 <img src="/img/image/image-20220804215033129.png" alt="image-20220804215033129" style="zoom:80%;" />

<img src="/img/image/image-20220804215046418.png" alt="image-20220804215046418" style="zoom:50%;" />

## 结束进程：

Kill -9 进程ID （-9 强行结束该进程）

# Linux软件管理工具

## rpm

RPM（RedHat Package Manager），Rethat软件包管理工具，类似windows里面的setup.exe

rpm（英文全拼：redhat package manager） 原本是 Red Hat Linux 发行版专门用来管理 Linux 各项套件的程序，由于它遵循 GPL 规则且功能强大方便，因而广受欢迎。逐渐受到其他发行版的采用。RPM 套件管理方式的出现，让 Linux 易于安装，升级，间接提升了 Linux 的适用度。

RPM是Linux这系列操作系统里面的打包安装工具，它虽然是RedHat的标志，但是其原始设计理念是开放式的，
包括OpenLinux、S.u.S.E.以及Turbo Linux等Linux的分发版本都有采用，可以算是公认的行业标准了。

RPM包的名称格式：

```shell
Apache-1.3.23-11.i386.rpm

“apache” 软件名称
“1.3.23-11”软件的版本号，主版本和此版本
“i386”是软件所运行的硬件平台
“rpm”文件扩展名，代表RPM包
```

```shell
常用命令  查询：
	rpm -qa | grep mysql 查询是否具有mysql的RPM包
  卸载：
	rpm -e --nodeps [包名] 强制卸载此包
  安装：
	rpm -ivh --nodeps [包名] 不检测依赖进度
```

使用RPM，用户可以自行安装和管理Linux上的应用程序和系统工具。RPM可以让用户直接以binary（二进制）方式安装软件包，并且可替用户查询是否已经安装了有关的库文件；在用RPM删除程序时，它又会聪明地询问用户是否要删除有关的程序。如果使用RPM来升级软件，RPM会保留原先的配置文件，这样用户就不用重新配置新的软件了。RPM保留一个数据库，这个数据库中包含了所有的软件包的资料，通过这个数据库，用户可以进行软件包的查询。RPM虽然是为Linux而设计的，但是它已经移值到SunOS、Solaris、AIX、Irix等其它UNIX系统上了。RPM遵循GPL版权协议，用户可以在符合GPL协议的条件下自由使用及传播RPM。

RPM共有10种基本的模式：它们是安装、查询、验证、删除等。

### 安装模式

命令格式：

```shell 
rpm -i (or --install)[install-options] file1.rpm ... fileN.rpm
```

常用选项：

* -v 详细信息
* -vv 详细显示指令执行过程，便于排错,即更详细信息
* -h 输出一个进度条
* --test 测试安装，但不安装
* --nodeps 忽略依赖关系  （不建议使用）
* --replacepkgs 重装
*  --nosignature不检查包签名信息的合法性
* ...

```shell
rpm -i example.rpm #安装 example.rpm 包；
rpm -iv example.rpm #安装 example.rpm 包并在安装过程中显示正在安装的文件信息；
rpm -ivh example.rpm #安装 example.rpm 包并在安装过程中显示正在安装的文件信息及安装进度
```

### 升级模式

命令格式

```shell
rpm -U(or --update)|-F(or --freshen) options file1.rpm ... fileN.rpm
```

* -U：升级或安装
* -F：升级

常用选项于安装模式类似

### 查询模式

命令格式

```shell
rpm -q ( or --query) [select-options] [query-iptions] options# 查询已安装的软件包
```

常用select-options：

* -q 查询指定的程序包是否已经安装，会显示详细信息
* -a --all 查询所有已经安装过的包
*  -f fiel 查询指定的文件是由那个程序包安装生成的
* -p --package package_fiel 用于查询未安装的包
*  --whatprovides 查询指定的capability由那个程序包提供
*  --whatrequires 查询指定的capability被那个包依赖
* ...

```shell
rpm -q --whatprobides /etc/passwd
```

常用query-iptions：

* -i 提供软件包的详细信息 版本 大小 所属包组
* -l 软件安装生成的所有文件列表
*  -c 配置文件
* -d --docfiles 提供的文档
* --provides 列出指定的程序包提供的所有功能
*  -R --requires 查询指定软件包的依赖关系

```shell
rpm -qi package_fiel 查询头信息
rpm -qf file 查询普通文件
rpm -qc package_file 查询配置文件
rpm -ql 列出所有文件列表
```

### 删除模式

命令格式

```shell
rpm -e(or --erase) options pkg1 ... pkgN
```

常用选项:

*  --alimatches 卸载匹配所有指定名称的软件包的所有版本
* --nodeps 忽略依赖关系
* --test 测试卸载

### 校验模式

命令格式

```shell
rpm -V(or --verify, or -y) options # 校验已安装的软件包
```

常用选项:

*  -f 校验所属的软件包
* -p Verify against package file
* -a Verify 校验所有的软件包
* -g 校验所有属于组 的软件包
* --noscripts 不运行校验脚本
* --nodeps 不校验依赖性
* --nofiles 不校验文件属性

## yum

yum（ Yellow dog Updater, Modified）是一个在 Fedora 和 RedHat 以及 SUSE 中的 Shell 前端软件包管理器。

rpm命令和yum命令都可以用来安装软件但与yum命令最大的区别为yum命令在安装软件时如果碰到了依赖性的问题，yum会去主动尝试解决依赖性，如果解决不了才会反馈给用户。而rpm命令一旦遇到了依赖性的问题不会去解决依赖性，而是直接反馈给用户，让用户自行解决。

yum基于 RPM 包管理，能够从指定的服务器自动下载 RPM 包并且安装，并且可以`自动处理依赖性关系`，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

yum 提供了查找、安装、删除某一个、一组甚至全部软件包的命令，而且命令简洁而又好记。

### yum 语法

```shell
yum [options] [command] [package ...]
```

- **options：**可选，选项包括-h（帮助），-y（当安装过程提示选择全部为 "yes"），-q（不显示安装的过程）等等。
- **command：**要进行的操作。
- **package：**安装的包名。

常用命令:

- 列出所有可更新的软件清单命令：**yum check-update**
- 更新所有软件命令：**yum update**
- 仅安装指定的软件命令：**yum install <package_name>**
- 仅更新指定的软件命令：**yum update <package_name>**
- 列出所有可安裝的软件清单命令：**yum list**
- 删除软件包命令：**yum remove <package_name>**
- 查找软件包命令：**yum search <keyword>**
- 清除缓存命令:
  - **yum clean packages**: 清除缓存目录下的软件包
  - **yum clean headers**: 清除缓存目录下的 headers
  - **yum clean oldheaders**: 清除缓存目录下旧的 headers
  - **yum clean, yum clean all (= yum clean packages; yum clean oldheaders)** :清除缓存目录下的软件包及旧的 headers

```shell
[root@www ~]# yum install pam-devel
Setting up Install Process
Parsing package install arguments
Resolving Dependencies  <==先检查软件的属性相依问题
...

[root@www ~]# yum remove pam-devel
Setting up Remove Process
Resolving Dependencies  <==同样的，先解决属性相依的问题
--> Running transaction check
---> Package pam-devel.i386 0:0.99.6.2-4.el5 set to be erased
--> Finished Dependency Resolution
...

[root@www ~]# yum list pam*
Installed Packages
pam.i386                  0.99.6.2-3.27.el5      installed
pam_ccreds.i386           3-5                    installed
pam_krb5.i386             2.2.14-1               installed
pam_passwdqc.i386         1.0.2-1.2.2            installed
pam_pkcs11.i386           0.5.3-23               installed
pam_smb.i386              1.1.7-7.2.1            installed
Available Packages <==底下则是『可升级』的或『未安装』的
pam.i386                  0.99.6.2-4.el5         base
pam-devel.i386            0.99.6.2-4.el5         base
```

### 国内 yum 源

网易（163）yum源是国内最好的yum源之一 ，无论是速度还是软件版本，都非常的不错。

将yum源设置为163 yum，可以提升软件包安装和更新的速度，同时避免一些常见软件版本无法找到。

**安装步骤**

首先备份/etc/yum.repos.d/CentOS-Base.repo

```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

下载对应版本 repo 文件, 放入 /etc/yum.repos.d/ (操作前请做好相应备份)

- [CentOS5](http://mirrors.163.com/.help/CentOS5-Base-163.repo) ：http://mirrors.163.com/.help/CentOS5-Base-163.repo
- [CentOS6](http://mirrors.163.com/.help/CentOS6-Base-163.repo) ：http://mirrors.163.com/.help/CentOS6-Base-163.repo
- [CentOS7](http://mirrors.163.com/.help/CentOS7-Base-163.repo) ：http://mirrors.163.com/.help/CentOS7-Base-163.repo

```
wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
mv CentOS6-Base-163.repo CentOS-Base.repo
```

运行以下命令生成缓存

```
yum clean all
yum makecache
```

# tar

最常用的打包命令是 tar，使用 tar 程序打出来的包我们常称为 tar 包，tar 包文件的命令通常都是以 .tar 结尾的。生成 tar 包后，就可以用其它的程序来进行压缩了，所以首先就来讲讲 tar 命令的基本用法。

tar 命令的选项有很多(用 man tar 可以查看到)，但常用的就那么几个选项，下面来举例说明一下：

```
# tar -cf all.tar *.jpg
```

这条命令是将所有 .jpg 的文件打成一个名为 all.tar 的包。-c 是表示产生新的包，-f 指定包的文件名。

```
# tar -rf all.tar *.gif
```

这条命令是将所有 .gif 的文件增加到 all.tar 的包里面去，-r 是表示增加文件的意思。

```
# tar -uf all.tar logo.gif
```

这条命令是更新原来 tar 包 all.tar 中 logo.gif 文件，-u 是表示更新文件的意思。

```
# tar -tf all.tar
```

这条命令是列出 all.tar 包中所有文件，-t 是列出文件的意思。

```
# tar -xf all.tar
```

这条命令是解出 all.tar 包中所有文件，-x 是解开的意思。

以上就是 tar 的最基本的用法。为了方便用户在打包解包的同时可以压缩或解压文件，tar 提供了一种特殊的功能。这就是 tar 可以在打包或解包的同时调用其它的压缩程序，比如调用 gzip、bzip2 等。

## tar调用

gzip 是 GNU 组织开发的一个压缩程序，.gz 结尾的文件就是 gzip 压缩的结果。与 gzip 相对的解压程序是 gunzip。tar 中使用 -z 这个参数来调用gzip。下面来举例说明一下：

```
# tar -czf all.tar.gz *.jpg
```

这条命令是将所有 .jpg 的文件打成一个 tar 包，并且将其用 gzip 压缩，生成一个 gzip 压缩过的包，包名为 all.tar.gz。

```
# tar -xzf all.tar.gz
```

这条命令是将上面产生的包解开。

## tar 调用 bzip2

bzip2 是一个压缩能力更强的压缩程序，.bz2 结尾的文件就是 bzip2 压缩的结果。

与 bzip2 相对的解压程序是 bunzip2。tar 中使用 -j 这个参数来调用 gzip。下面来举例说明一下：

```
# tar -cjf all.tar.bz2 *.jpg
```

这条命令是将所有 .jpg 的文件打成一个 tar 包，并且将其用 bzip2 压缩，生成一个 bzip2 压缩过的包，包名为 all.tar.bz2

```
# tar -xjf all.tar.bz2
```

这条命令是将上面产生的包解开。

## tar 调用 compress

compress 也是一个压缩程序，但是好象使用 compress 的人不如 gzip 和 bzip2 的人多。.Z 结尾的文件就是 bzip2 压缩的结果。与 compress 相对的解压程序是 uncompress。tar 中使用 -Z 这个参数来调用 compress。下面来举例说明一下：

```
# tar -cZf all.tar.Z *.jpg
```

这条命令是将所有 .jpg 的文件打成一个 tar 包，并且将其用 compress 压缩，生成一个 uncompress 压缩过的包，包名为 all.tar.Z。

```
# tar -xZf all.tar.Z
```

这条命令是将上面产生的包解开。

有了上面的知识，你应该可以解开多种压缩文件了，下面对于 tar 系列的压缩文件作一个小结：

**1) 对于.tar结尾的文件**

```
tar -xf all.tar
```

**2) 对于 .gz 结尾的文件**

```
gzip -d all.gz
gunzip all.gz
```

**3)对于 .tgz 或 .tar.gz 结尾的文件**

```
tar -xzf all.tar.gz
tar -xzf all.tgz
```

**4) 对于 .bz2 结尾的文件**

```
bzip2 -d all.bz2
bunzip2 all.bz2
```

**5) 对于 tar.bz2 结尾的文件**

```
tar -xjf all.tar.bz2
```

**6) 对于 .Z 结尾的文件**

```
uncompress all.Z
```

**7) 对于 .tar.Z 结尾的文件**

```
tar -xZf all.tar.z
```

另外对于 Windows 下的常见压缩文件 .zip 和 .rar，Linux 也有相应的方法来解压它们：

**1) 对于 .zip**

linux 下提供了 zip 和 unzip 程序，zip 是压缩程序，unzip 是解压程序。它们的参数选项很多，这里只做简单介绍，依旧举例说明一下其用法：

```
# zip all.zip *.jpg
```

这条命令是将所有 .jpg 的文件压缩成一个 zip 包:

```
# unzip all.zip
```

这条命令是将 all.zip 中的所有文件解压出来。

**2) 对于 .rar**

要在 linux 下处理 .rar 文件，需要安装 RAR for Linux。下载地址：http://www.rarsoft.com/download.htm，下载后安装即可。

```
# tar -xzpvf rarlinux-x64-5.6.b5.tar.gz
# cd rar 
# make
```

这样就安装好了，安装后就有了 rar 和 unrar 这两个程序，rar 是压缩程序，unrar 是解压程序。它们的参数选项很多，这里只做简单介绍，依旧举例说明一下其用法：

```
# rar a all *.jpg
```

这条命令是将所有 .jpg 的文件压缩成一个 rar 包，名为 all.rar，该程序会将 .rar 扩展名将自动附加到包名后。

```
# unrar e all.rar
```

这条命令是将 all.rar 中的所有文件解压出来：

------

## 扩展内容

**tar**

```
-c: 建立压缩档案 
-x：解压 
-t：查看内容 
-r：向压缩归档文件末尾追加文件 
-u：更新原压缩包中的文件
```

这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

```
-z：有gzip属性的 
-j：有bz2属性的 
-Z：有compress属性的 
-v：显示所有过程 
-O：将文件解开到标准输出 
```

下面的参数 -f 是必须的:

```
-f: 指定档案名字，切记，这个参数是最后一个参数，后面只能接档案名。 
```

\# tar -cf all.tar *.jpg

这条命令是将所有 .jpg 的文件打成一个名为 all.tar 的包。-c 是表示产生新的包，-f 指定包的文件名。

```
# tar -rf all.tar *.gif 
```

这条命令是将所有 .gif 的文件增加到 all.tar 的包里面去。-r 是表示增加文件的意思。

```
# tar -uf all.tar logo.gif 
```

这条命令是更新原来 tar 包 all.tar 中 logo.gif 文件，-u 是表示更新文件的意思。

```
# tar -tf all.tar 
```

这条命令是列出 all.tar 包中所有文件，-t 是列出文件的意思。

```
# tar -xf all.tar 
```

这条命令是解出 all.tar 包中所有文件，-x 是解开的意思。

**压缩**

```
tar –cvf jpg.tar *.jpg       // 将目录里所有jpg文件打包成 tar.jpg 
tar –czf jpg.tar.gz *.jpg    // 将目录里所有jpg文件打包成 jpg.tar 后，并且将其用 gzip 压缩，生成一个 gzip 压缩过的包，命名为 jpg.tar.gz 
tar –cjf jpg.tar.bz2 *.jpg   // 将目录里所有jpg文件打包成 jpg.tar 后，并且将其用 bzip2 压缩，生成一个 bzip2 压缩过的包，命名为jpg.tar.bz2 
tar –cZf jpg.tar.Z *.jpg     // 将目录里所有 jpg 文件打包成 jpg.tar 后，并且将其用 compress 压缩，生成一个 umcompress 压缩过的包，命名为jpg.tar.Z 
rar a jpg.rar *.jpg          // rar格式的压缩，需要先下载 rar for linux 
zip jpg.zip *.jpg            // zip格式的压缩，需要先下载 zip for linux
```

**解压**

```
tar –xvf file.tar         // 解压 tar 包 
tar -xzvf file.tar.gz     // 解压 tar.gz 
tar -xjvf file.tar.bz2    // 解压 tar.bz2 
tar –xZvf file.tar.Z      // 解压 tar.Z 
unrar e file.rar          // 解压 rar 
unzip file.zip            // 解压 zip 
```

**总结**

```
1、*.tar 用 tar –xvf 解压 
2、*.gz 用 gzip -d或者gunzip 解压 
3、*.tar.gz和*.tgz 用 tar –xzf 解压 
4、*.bz2 用 bzip2 -d或者用bunzip2 解压 
5、*.tar.bz2用tar –xjf 解压 
6、*.Z 用 uncompress 解压 
7、*.tar.Z 用tar –xZf 解压 
8、*.rar 用 unrar e解压 
9、*.zip 用 unzip 解
```

# Linux总结

**Linux中一切皆文件（文件 -----》 读写执行（查看，创建，删除，移动，复制，编辑），权限（用户用户组），系统（磁盘，进程））**













