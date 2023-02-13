---
layout: post
title: "分布式搜索和分析引擎 - ElasticSearch"
subtitle: 'Distributed search and analysis engine--ElasticSearch'
author: "pzw"
header-style: text
tags:
  - Java后端
  - 分布式

---

# ElasticSearch简介

Elasticsearch是一个开源的搜索引擎，建立在全文搜索引擎库 Apache Lucene 基础上；内部使用Lucence（Lucene是一套信息检索工具包jar包，被认为是迄今为止最先进、性能最好的、功能最全的搜索引擎库，ElasticSearch对Lucene 进行了封装和增强）做索引与搜索，但是它的目的是使全文检索变得简单，通过隐藏Lucence的复杂性，取而代之的提供一套简单一致的`RESTFUL API`

Elasticsearch不仅仅只是一个全文搜索引擎是一个分布式的实时文档存储，每个字段可以被索引与搜索可以做数据库用；还是一个分布式实时分析搜索引擎，能胜任上百个服务节点的扩展，并支持PB级别（大数据级别）的结构化或者非结构化数据（只需要直到其属性即可）；

## 衍生的开源软件

> * Elasticsearch：分布式搜索引擎
> * Logstash：日志的采集与解析工具
> * Kibana：可视化分析平台
> * Beats：数据采集工具家族（替换Logstash）
> * X-Pack：功能包

## ES特性

1. 速度快、易扩展、弹性、灵活、操作简单、多语言客户端
2. 分布式：横向扩展非常灵活
3. 全文检索：基于lucene的强大的全文检索能力
4. 近实时搜索和分析：数据进入ES，可达到近实时搜索，还可以进行聚合分析
5. 高可用：容错机制，自动发现新的或失败的节点，重组和重新平衡数据
6. 模式自由：ES的动态mapping机制可以自动检测数据的结构和类型，创建索引并使数据可搜索
7. RESTful API：JSON + HTTP

## ES核心概念

* **Near Realtime（NRT）**近实时：数据提交索引后，立马就可以搜索到；
* **Cluster集群**：一个集群由一个唯一的名字标识，默认为“elasticsearch”。集群名称非常重要，具有相同集群名的节点才会组成一个集群。集群名称可以在配置文件中指定；
* **Node节点**：存储集群的数据，参与集群的索引和搜索功能。想集群有名字，节点也有自己的名称，默认在启动时为一个随机的UUID的前七个字符作为结点的名字，你可以为其指定任意的名字。通过集群名在网络中发现同伴组成集群。一个节点也可是集群；
* **Index索引**：一个索引是一个文档的集合（等同于solr中的集合）。每个索引有唯一的名字，通过这个名字来操作它。一个集群中可以有任意多个索引。Type 类型：指在一个索引中，可以索引不同类型的文档，如用户数据、博客数据。从6.0.0 版本起已废弃，一个索引中只存放一类数据。
* **Document 文档**：被索引的一条数据，索引的基本信息单元，以JSON格式来表示。
* **Shard 分片**：在创建一个索引时可以指定分成多少个分片来存储。每个分片本身也是一个功能完善且独立的“索引”，可以被放置在集群的任意节点上。分片的好处：允许我们水平切分/扩展容量、可在多个分片上进行分布式的、并行的操作，提高系统的性能和吞吐量；
* **Replication 备份**: 一个分片可以有多个备份（副本）。备份的好处：高可用。一个主分片挂了，副本分片就顶上去。

> **物理设计**
>
> elasticsearch在后台把每个索引划分成多个分片。每个分片可以在集群中的不同服务器间迁移
>
> 一个人就是一个集群! ，即启动的ElasticSearch服务，默认就是一个集群，且默认集群名为elasticsearch
>
> **逻辑设计**
>
> 一个索引类型中，包含多个文档，比如说文档1，文档2。当我们索引一篇文档时，可以通过这样的一个顺序找到它：索引->类型->文档id，通过这个组合我们就能索引到某个具体的文档。注意：ID不必是整数，实际上它是一个字符串。

> 索引（“库”）
>
> 就是数据库!
>
> 索引是映射类型的容器， elasticsearch中的索引是一个非常大的文档集合。索引存储了映射类型的字段和其他设置。然后它们被存储到了各个分片上了。我们来研究下分片是如何工作的。

> 类型（“表”）
>
> 类型是文档的逻辑容器，就像关系型数据库一样，表格是行的容器。类型中对于字段的定 义称为映射，比如name映射为字符串类型。我们说文档是无模式的 ，它们不需要拥有映射中所定义的所有字段，比如新增一个字段，那么elasticsearch是怎么做的呢?elasticsearch会自动的将新字段加入映射，但是这个字段的不确定它是什么类型， elasticsearch就开始猜，如果这个值是18 ，那么elasticsearch会认为它是整形。但是elasticsearch也可能猜不对 ，所以最安全的方式就是提前定义好所需要的映射，这点跟关系型数据库殊途同归了，先定义好字段，然后再使用，别整什么幺蛾子。

> 扩展搜索的并发(同一处理器，同一时间段内处理多个任务)能力、吞吐量。搜索可以在所有的副本上并行（多个处理器同时处理多个任务）运行。高并发下副本也可搜索

|         数据库（database）         |          索引（index）          |
| :--------------------------------: | :-----------------------------: |
|          **表（table）**           | **类型（type）（6.0.0后废弃）** |
|           **行（row）**            |      **文档（document）**       |
|          **列（column）**          |        **字段（field）**        |
|        **表结构（schema）**        |       **映射（mapping）**       |
|              **索引**              |          **反向索引**           |
|              **SQL**               |           **查询DSL**           |
|      **select * from table**       |       **GET http:// ...**       |
| **insert into table,update table** |       **PUT http:// ...**       |
|             **delete**             |     **DELETE http:// ...**      |

## 结构化数据与非结构化数据

平时的数据可以分为结构化数据和非结构化数据：计算机中的储存单位，1DB=1024的八次方GB

* 结构化数据是指格式和长度固定的数据，如一个人的年龄，姓名等；
* 非结构化数据是指格式和长度不固定的数据，如一个文章的内容等；

> DB就是一个存储数据的仓库。为了方便数据的存储和管理。它将这个数据按照特定的规律来存储在磁盘上上。通过数据库管理系统，可以有效的组织和管理存储在数据库里面的数据。

对于结构化数据，可以存储在DB中通过精确匹配找到。但是对于非结构化数据，一般查询时只能提供查询的局部信息或模糊信息，传统数据库无法根据这些信息进行查询，效率差；

## 倒排索引

<img src="../../../../../Download/Typora/image/image-20220523154038795.png" alt="image-20220523154038795" style="zoom: 67%;" />

> 倒排索引是相对于正排索引而言的，可以有效的解决该问题；
>
> * 正排索引可以通过id查找到对应的文章，但是无法通过给的部分内容如elasticsearch，找出含有该关键字的文档。
> * 倒排索引会先对文档进行分析将其拆分成单个Term, 并存储包含该Term的文档id，这样便可以实现通过内容查找对应文档，如包含elasticsearch的文档为文档1；

倒排索引的过程：

1. 通过倒排索引获得“搜索引擎”对应的文档id列表，有1和3；
2. 通过正排索引查询1和3的完整内容；
3. 返回最终结果；

# ES与Solr的区别

## Solr简介

Solr是Apache下的一个顶级开源项目，采用Java开发，它是基于Lucene的全文搜索服务器。Sorl提供了比Lucene更为丰富的查询语言，同时实现了可配置、可扩展、并对索引、搜索性能进行了优化

Solr可以独立运行，运行在Jetty、Tomcat等这些Servlet容器中，Solr索引的实现方法很简单，`用POST方法向SOlr服务器放送一个描述Field及其内容的XML文档，Solr根据XML文登添加、删除、更新索引。`Solr搜索只需要发送Http Get请求，然后对Solr返回xml、json等格式的查询结果进行解析，组织页面布局。Solr不提供构建UI的功能，Solr提供了一个管理界面，通过管理界面可以查询Solr配置和运行情况。

solr是基于lucene开发企业级搜索服务器，实际上就是封装了lucence

solr是一个独立的企业级搜索应用服务器，它对外提供类似于Web-service的API接口。用户可以通过http请求，向搜索引擎服务器提交一定格式的文件，生成索引；也可以通过查找请求，并得到返回结果。

## Lucene简介

Lucene是apache软件基金会4 jakarta项目组的一个子项目，是一个开放源代码的全文检索引擎工具包，但它不是一个完整的全文检索引擎，而是一个全文检索引擎的架构，提供了完整的查询引擎和索引引擎，部分文本分析引擎(英文与德文两种西方语言)。
Lucene的目的是为软件开发人员提供一个简单易用的工具包，以方便的在目标系统中实现全文检索的功能，或者是以此为基础建立起完整的全文检索引擎。Lucene是一套用于全文检索和搜寻的开源程式库，由Apache软件基金会支持和提供。Lucene提供了一 个简单却强大的应用程式接口，能够做全文索引和搜寻。在Java开发环境里Lucene是一个成熟的免费开源工具。 就其本身而言，Lucene是当前以及最近几年最受欢迎的免费Java信息检索程序库。人们经常提到信息检索程序库，虽然与搜索引擎有关，但不应该将信息检索程序库与搜索引擎相混淆。

Lucene是一个全文检索弓|擎的架构。那什么是全文搜索引擎?

全文搜索引擎是名副其实的搜索引擎，国外具代表性的有Google、Fast/AITheWeb、AltaVista、Inktomi、 Teoma、 WiseNut等 ，国内著名的有百度( Baidu)。它们都是通过从互联网上提取的各个网站的信息(以网页文字为主)而建立的数据库中，检索与用户查询条件匹配的相关记录, 然后按一定的排列顺序将结果返回给用户，因此他们是真正的搜索引擎。

以搜索结果来源的角度，全文搜索引擎又可细分为两种，一种是拥有自己的检索程序(Indexer )，俗称"蜘蛛” ( Spider )程序或"机器人”( Robot )程序，并自建网页数据库，搜索结果直接从自身的数据库中调用，如上面提到的7家引擎;另一种则是租用其也引擎的数据库，并按自定的格式排列搜索结果，如Lycos引擎。

## ES和Solr区别

单纯对已有数据进行搜索，solr更快；当实时建立索引时，Solr会产生io阻塞，查询性能较差，ElasticSearch具有明显的优势；随着数据量的增加，Solr的搜索效率会变得更低，而ElasticSearch却没有明显的变化；转变我们的搜索基础设施后从Solr ElasticSearch，我们看见一个即时~ 50x提高搜索性能！

1. es基本是开箱即用(解压就可以用! ) ,非常简单。Solr安装略微复杂一丢丢!
2. Solr 利用Zookeeper进行分布式管理,而Elasticsearch自身带有分布式协调管理功能。
3. Solr 支持更多格式的数据,比如JSON、XML、 CSV ,而Elasticsearch仅支持json文件格式。
4. Solr 官方提供的功能更多,而Elasticsearch本身更注重于核心功能,高级功能多有第三方插件提供,例如图形化界面需要
   kibana友好支撑~!
5. Solr 查询快,但更新索引时慢(即插入删除慢) , 用于电商等查询多的应用;
   * ES建立索引快(即查询慢) ,即实时性查询快,用于facebook新浪等搜索。
   * Solr 是传统搜索应用的有力解决方案,但Elasticsearch更适用于新兴的实时搜索应用
6. Solr比较成熟,有一个更大,更成熟的用户、开发和贡献者社区,而Elasticsearch相对开发维护者较少,更新太快,学习使用
   成本较高。( 趋势! )

一个集群至少有一 个节点，而一个节点就是一-个elasricsearch进程 ，节点可以有多个索引默认的，如果你创建索引，那么索引将会有个5个分片( primary shard ，又称主分片)构成的，每一个主分片会有-一个副本( replica shard ，又称复制分片）

![image-20220524150133637](../../../../../Download/Typora/image/image-20220524150133637.png)

上图是一个有3个节点的集群，可以看到主分片和对应的复制分片都不会在同-个节点内，这样有利于某个节点挂掉了，数据也不至于丢失。实际上， 一个分片是- -个Lucene索引， 一个包含倒排索引的文件目录，倒排索引的结构使得elasticsearch在不扫描全部文档的情况下，就能告诉你哪些文档包含特定的关键字。

# ES的安装使用

## 准备

>JDK版本： 1.8;ElasticSearch 客户端,界面工具!
>Java开发, ElasticSearch 的版本和之后对应的Java的核心jar包!版本对应! JDK环境是正常!
>
>官网下载地址： https://www.elastic.co/downloads/elasticsearch
>
>推荐到华为云镜像下载：https://mirrors.huaweicloud.com/
>
>[ElasticSearch](https://mirrors.huaweicloud.com/elasticsearch/?C=N&O=D%20logstash:%20https://mirrors.huaweicloud.com/logstash/?C=N&O=D%20kibana:%20https://mirrors.huaweicloud.com/kibana/?C=N&O=D) 

## 启动

> 解压安装包，进入bin目录中双击elasticsearch.bat等待启动完毕

<img src="../../../../../Download/Typora/image/image-20220524164601132.png" alt="image-20220524164601132" style="zoom:80%;" />

> 打开浏览器，输入 http://localhost:9200 出现以下画面，说明ES安装成功；

<img src="../../../../../Download/Typora/image/image-20220524164624759.png" alt="image-20220524164624759" style="zoom:80%;" />

## ES端口

> - 9300端口： ES节点之间通讯使用，9300是tcp通讯端口，集群间和TCPClient 都走它；
> - 9200端口： ES节点和外部通讯使用，9200是http协议的RESTful接口；

# 可视化工具Kibana

Kibana是一个针对Elasticsearch的开源分析及可视化平台，使用Kibana可以查询、查看并与存储在ES索引的数据进行交互操作，同时也能执行高级的数据分析，并能以图表、表格和地图的形式查看数据 ；

## 下载安装

> 下载安装包，需要和ES版本匹配，下载完毕后安装到相应的目录就行；

下载地址：https://www.elastic.co/downloads/kibana

## 配置

在目录config/kibana.yml中配置 elasticsearch.hosts: 的值为ES的访问地址；

<img src="../../../../../Download/Typora/image/image-20220524164845043.png" alt="image-20220524164845043" style="zoom:80%;" />

## 启动Kibana

> 双击bin/kibana.bat
>
> - 访问地址：http://localhost:5601

# IK分词器

> 集成Ikanalyzer是一个开源的，基于java语言开发的轻量级的中文分词工具包；从3.0版本开始，IK发展为面向Java的公用分词组件，独立于Lucene项目，同时提供了对Lucene的默认优化实现。在2012版本中，IK实现了简单的分词歧义排除算法，标志着IK分词器从单纯的词典分词向模拟语义分词衍化；

## 下载安装

> 获取 ES-IKAnalyzer插件，需要和ES版本一致；
>
> 将ik的压缩包解压到 ES安装目录的plugins/目录下（新建文件夹analysis-ik），重启ES；

下载地址： https://github.com/medcl/elasticsearch-analysis-ik/releases

在elasticssearch的plugin目录下安装ik分词器：

```bash
[root@05592eb427bc plugins]# elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.14.2/elasticsearch-analysis-ik-7.14.2.zip
```

不同分词效果：

**ik_smart：**最少切分(最粗粒度的拆分)

**ik_max_word：**最细粒度划分（穷尽词库的可能）

### 细粒度拆分

```json
{
    "text":"我爱学习，最爱学Java",
    "analyzer":"ik_max_word" 
}
```

<img src="../../../../../Download/Typora/image/image-20220524170039696.png" alt="image-20220524170039696" style="zoom: 80%;" />

### 粗粒度拆分

```json
{
    "text":"我爱学习，最爱学Java",
    "analyzer":"ik_smart" 
}
```

<img src="../../../../../Download/Typora/image/image-20220524170152212.png" alt="image-20220524170152212" style="zoom:80%;" />

## ik 分词器增加自己的配置

添加自定义的词到扩展字典中

elasticsearch目录/plugins/ik/config/IKAnalyzer.cfg.xml

创建 `kuang.dic` 字典文件，添加字典内容

<img src="../../../../../Download/Typora/image/image-20220524164405878.png" alt="image-20220524164405878" style="zoom:70%;" />

打开 IKAnalyzer.cfg.xml 文件，扩展字典

```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
	<comment>IK Analyzer 扩展配置</comment>
	<!--用户可以在这里配置自己的扩展字典 -->
	<entry key="ext_dict">kuang.dic</entry>
	 <!--用户可以在这里配置自己的扩展停止词字典-->
	<entry key="ext_stopwords"></entry>
	<!--用户可以在这里配置远程扩展字典 -->
	<!-- <entry key="remote_ext_dict">words_location</entry> -->
	<!--用户可以在这里配置远程扩展停止词字典-->
	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
</properties>
```

# Rest风格

**一种软件架构风格**,而不是标准,只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以**更简洁**，**更有层次**，**更易于实现缓存**等机制。

## 共享数据基本信息

|      method      |                     url地址                     |          描述          |
| :--------------: | :---------------------------------------------: | :--------------------: |
| PUT（创建,修改） |     localhost:9200/索引名称/类型名称/文档id     | 创建文档（指定文档id） |
|   POST（创建）   |        localhost:9200/索引名称/类型名称         | 创建文档（随机文档id） |
|   POST（修改）   | localhost:9200/索引名称/类型名称/文档id/_update |        修改文档        |
|  DELETE（删除）  |     localhost:9200/索引名称/类型名称/文档id     |        删除文档        |
|   GET（查询）    |     localhost:9200/索引名称/类型名称/文档id     |   查询文档通过文档ID   |
|   POST（查询）   | localhost:9200/索引名称/类型名称/文档id/_search |      查询所有数据      |

put->创建、修改

post: 创建，_update修改， _search查询

delete：删除

get: 查询 _search?q=字段：值

## 关于索引的基本操作

### 创建索引(PUT)

```json
PUT /索引名/~类型名~/文档id
{请求体}
PUT /test1/type1/1
{
"name" : "流柚",
"age" : 18
}
# 返回结果
# 警告信息： 不支持在文档索引请求中的指定类型
# 而是使用无类型的断点(/{index}/_doc/{id}, /{index}/_doc, or /{index}/_create/{id}).
{
  "_index" : "test1",    # 索引
  "_type" : "type1",    # 类型（已经废弃）
  "_id" : "1",            # id
  "_version" : 1,        # 版本
  "result" : "created",    # 操作类型
  "_shards" : {            # 分片信息
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

**字段类型**

* 字符串类型：text、keyword

  **text**：支持分词，全文检索,支持模糊、精确查询,不支持聚合,排序操作;text类型的最大支持的字符长度无限制,适合大字段存储；

  **keyword**：不进行分词，直接索引、支持模糊、支持精确匹配，支持聚合、排序操作。keyword类型的最大支持的长度为——32766个UTF-8类型的字符,可以通过设置ignore_above指定自持字符长度，超过给定长度后的数据将不被索引，无法通过term精确匹配检索返回结果。

* 数值型：long、Integer、short、byte、double、float、half float、scaled float
* 日期类型：date

* 布尔类型：boolean

* 二进制类型：binary

* `默认类型是：_doc`















