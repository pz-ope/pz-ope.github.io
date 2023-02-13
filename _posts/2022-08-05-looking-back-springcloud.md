# 微服务概述

## 微服务概述

微服务（Microservice Architecture）是近几年流行的一种架构思想，关于它的概念很难一言以蔽之。

究竟什么是微服务呢？我们在此应用 ThoughtWorks 公司的首席科学家 Martin Fowler 于2014年提出的一段话：

* 就目前而言，对于微服务，业界应没有一个统一的，标准的定义
* 但通常而言，微服务架构是一种架构模式，或者说是一种架构风格，**它提倡将单一的应用程序划分成一组小的服务，每个服务器运行在其独立的自己的进程内，服务之间相互协调，互相配置，为用户提供最终价值。**
* 服务之间采用轻量级的通信机制互相沟通，每个服务都围绕着具体的业务进行构建，并且能够被独立的部署到生产环境中，另外，应尽量避免统一的，集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言，工具对其进行构建，可以一个非常轻量级的集中式管理来协调这些服务，可以使用不同的语言来编写服务，也可以使用不同的数据存储；

可能有的人觉得官方的话太过生涩，我们从技术维度来理解下：

微服务化的核心就是将传统的一站式应用，根据业务拆分成一个一个的服务，彻底地去耦合，每一个微服务提供单个业务功能的服务，一个服务做一件事情，从技术角度看就是一种小而独立的处理过程，类似进程的概念，能够自行单独启动或销毁，拥有自己独立的数据库。

## CAP 定理

CAP 定理指的是在一个分布式系统中，Consistency（一致性）、 Availability（可用性）、

Partition tolerance（分区容错性），三者不可兼得。

* 一致性（C）：分布式系统中多个主机之间是否能够保持数据一致的特性。即，当系统数据发生更新操作后，各个主机中的数据仍然处于一致的状态。
* 可用性（A）：系统提供的服务必须一直处于可用的状态，即对于用户的每一个请求，系统总是可以在有限的时间内对用户做出响应。
* 分区容错性（P）：分布式系统在遇到任何网络分区故障时，仍能够保证对外提供满足一致性和可用性的服务。

CAP 定理的内容是：对于分布式系统，网络环境相对是不可控的，出现网络分区是不可避免的，因此系统必须具备分区容错性。但系统不能同时保证一致性与可用性。即要么 CP，要么 AP。

例如：在数据同步时间内集群中的该台机器，将不会提供服务，所有保持了CP将会去掉A

**回顾**

RDBMS (MySQL\Oracle\sqlServer) ===> ACID

NoSQL (Redis\MongoDB) ===> CAP

ACID是什么？

* A (Atomicity) 原子性
* C (Consistency) 一致性
* I (Isolation) 隔离性
* D (Durability) 持久性

CAP是什么?

* C (Consistency) 强一致性
* A (Availability) 可用性
* P (Partition tolerance) 分区容错性
* CAP的三进二：CA、AP、CP

## 微服务和微服务架构

**微服务**

强调的是服务的大小，他关注的某一个点，是具体解决某一个问题/提供落地对应服务的一个服务应用，狭义的看，可以看做是IDEA中的一个个微服务工程，或者Moudel

IDEA 工具里面使用Maven开发的一个个独立的小Moudle，它具体是使用springboot开发的一个小模块，专业的事情交给专业的模块来做，一个模块就做着一件事情，强调的是一个个的个体，每个个体完成一个具体的任务或者功能!

**微服务架构**

一种新的架构形式，Martin Fowler， 2014提出

微服务架构是一种架构模式，它提倡将单一应用程序划分成一组小的服务，服务之间互相协调，互相配合，为用户提供最终价值。每个服务运行在其独立的进程中，服务与服务间采用轻量级的通信机制互相协作，每个服务都围绕着具体的业务进行构建，并且能够被独立的部署到生产环境中，另外，应尽量避免统一的，集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言，工具对其进行构建。

## 微服务优缺点

**优点**

- 单一职责原则
- 每个服务足够内聚，组够小，代码容易理解，这样能聚集一个指定的业务功能或业务需求；
- 开发简单，开发效率提供，一个服务可能就是专一的只敢一件事；
- 微服务能够被小团队单独开发，这个小团队是2~5人的开发人员组成；
- 微服务是松耦合的，是有功能意义的服务，无论是在开发阶段或者部署阶段都是独立的。
- 微服务能使用不同的语言开发。
- 易于和第三方继承，微服务允许容易且灵活的方式集成自动部署，通过持续继承工具，如 Jenkins，Hudson，Bamboo
- 微服务易于被一个开发人员理解，修改和维护，这样小团队能够功能关注自己的工作成果。无需通过合作才能体现价值。
- 微服务允许你利用融合最新技术
- 微服务知识业务逻辑的代码，不会和 HTML，CSS 或其他界面混合
- 每个微服务都有自己的存储能力，可以有自己的数据库，也可以有同意数据库

**缺点：**

- 开发人员要处理分布式系统的复杂性
- 多服务运维难度，随着服务的增加，运维的压力也在增大
- 系统部署依赖
- 服务间通信成本
- 数据一致性
- 系统集成测试
- 性能监控。

## 微服务技术栈

|             **微服务条目**              |                         **落地技术**                         |
| :-------------------------------------: | :----------------------------------------------------------: |
|                服务开发                 |                 SpringBoot,Spring,SpringMVC                  |
|             服务配置与管理              |            Netflix公司的Archaius、阿里的Diamond等            |
|             服务注册与发现              |                 Eureka、Consul、Zookeeper等                  |
|                服务调用                 |                       Rest、RPC、gRPC                        |
|               服务熔断器                |                       Hystrix、Envoy等                       |
|                负载均衡                 |                       Ribbon、Nginx等                        |
| 服务接口调用（客户端调用服务的简化工具) |                           Feign等                            |
|                消息队列                 |                 Kafka、RabbitMQ、ActiveMQ等                  |
|            服务配置中心管理             |                  SpringCloudConfig、chef等                   |
|            服务路由(API网关)            |                            Zuul等                            |
|                服务监控                 |            Zabbix、Nagios、Metrics、Specatator等             |
|               全链路追踪                |                   Zipkin、Brave、Dapper等                    |
|                服务部署                 |               Docker、OpenStack、Kubernetes等                |
|            数据流操作开发包             | SpringCloud Stream(封装与Redis，Rabbit，Kafka等发送接收消息) |
|              事件消息总线               |                       SpringCloud Bus                        |

# SpringCloud 入门概述

Spring Cloud 是一系列框架的有序集合。基于SpringBoot提供了一套微服务解决方案，包括服务注册于发现，配置中心，全链路监控， 服务网关，负载均衡， 熔断器等组件，除了基于NetFlix的开源组件做高度抽象封装之外，还有一些选型中立的开源组件。

SpringCloud 利用SpringBoot的开发便利性，巧妙地简化了分布式系统基础设施的开发，SpringCloud为开发人员提供了快速构建分布式系统的写工具，包括配置管理，服务发现，断路器，路由，微代理，事件总线，全局锁，决策竞选，分布式会话等等，他们都可以用SpringBoot开发风格做的一键启动和部署。

Spring Boot 为 Spring Cloud 提供了代码实现环境，使用 Spring Boot将其它组件有机融合到了 Spring Cloud 的体系架构中了。所以说，Spring Cloud 是基于 Spring Boot 的、微服务系统架构的一站式解决方案。

SpringCloud 是 分布式微服务架构下的一站式解决方案，是各个微服务架构落地技术的集合体，俗称微服务全家桶。

## SpringCloud 和 SpringBoot关系
SpringBoot 专注于快速方便的开发单个个体微服务。
SpringCloud 是关注全局的微服务协调整理治理框架，他将SpringBoot开发的一个个单体微服务整合并管理起来，为各个微服务之间提供：配置管理，服务发现，断路器，路由，微代理，事件总线，全局锁，决策竞选，分布式会话等等集成服务。
SpringBoot可以离开SpringCloud独立使用，开发项目，但是SpringCloud离不开SpringBoot，属于依赖关系
SpringBoot专注于快速开发、方便的开发单个个体微服务，SpringCloud关注全局的服务治理框架

## Dubbo 和 SpringCloud 对比

最大区别：SpringCloud 抛弃了 Dubbo 的 RPC 通信，采用的是基于HTTP的REST方式。

严格来说，这两种方式各有优劣。虽然从一定程度上来说，后者牺牲了服务调用的性能，但也避免了上面提到的原来RPC带来的问题。而且REST相比RPC更为灵活，服务提供方和调用方的依赖只依靠一纸锲约，不存在代码级别的强依赖，这在强调快速演化的微服务环境下，显得更加合适。

**品牌机与组装机的区别**

很明显，Spring Cloud的功能比DUBBO更加强大，涵盖面更广，而且作为Spring的拳头项目，它也能够与SpringFramework、Spring Boot、Spring Data、Spring Batch等其他Spring项目完美融合，这些对于微服务而言是至关重要的。使用Dubbo构建的微服务架构就像组装电脑，各环节我们的选择自由度很高，但是最终结果很有可能因为一条内存质量不行就点不亮了，总是让人不怎么放心，但是如果你是一名高手，那这些都不是问题；而SpringCloud就像品牌机，在Spring Source的整合下，做了大量的兼容性测试，保证了机器拥有更高的稳定性，但是如果要在使用非原装组件外的东西，就需要对其基础有足够的了解。

**社区支持与更新力度**

最为重要的是，DUBBO停止了5年左右的更新，虽然2017.7重启了。对于技术发展的新需求，需要由开发者自行拓展升级（比如当当网弄出了DubboX)，这对于很多想要采用微服务架构的中小软件组织，显然是不太合适的，中小公司没有这么强大的技术能力去修改Dubbo源码+周边的一整套解决方案，并不是每一个公司都有阿里的大牛+真实的线上生产环境测试过。

总结：

曾风靡国内的开源 RPC 服务框架 Dubbo 在重启维护后，令许多用户位置雀跃，但同时，也迎来了一些质疑的声音。互联网技术发展迅速，Dubbo 是否还能跟上时代？Dubbo 与 Spring Cloud 相比又有何优势和差异？是否会有相关举措保证 Dubbo 的后续更新频率？

人物：Dubbo重启维护开发的刘军，主要负责人之一

刘军，阿里巴巴中间件高级研发工程师，主导了 Dubbo 重启维护以后的几个发版计划，专注于高性能 RPC 框架和微服务相关领域。曾负责网易考拉 RPC 框架的研发及指导在内部使用，参与了服务治理平台、分布式跟踪系统、分布式一致性框架等从无到有的设计与开发过程。

解决的问题域不一样：Dubbo 的定位是一款RPC框架，Spring Cloud 的目标是微服务架构下的一站式解决方案

## SpringCloud 作用
Distributed/versioned configuration（分布式/版本控制配置）
Service registration and discovery（服务注册与发现）
Routing（路由）
Service-to-service calls（服务到服务的调用）
Load balancing（负载均衡配置）
Circuit Breakers（断路器）
Distributed messaging（分布式消息管理）
...

# SpringCloud使用

## 版本号来源

<img src="/img/image/image-20220805165717739.png" alt="image-20220805165717739" style="zoom:60%;" />

spring cloud 是一个由众多独立子项目组成的大型综合项目，每个子项目有不同的发行节奏，都维护着自己的发布版本号。
spring cloud 通过一个资源清单 BOM (Bi11 of Materials)来管理每个版本的子项目清单。为避免与子项目的发布号混
淆，所以没有采用版本号的方式，而是通过命名的方式。
这些版本名称的命名方式采用了伦敦地铁站的名称，同时根据字母表的顺序来对应版本时间顺序，比如: 最早的 Release版本: Ange1，第二个Release版本: Brixton，然后是camden、Dalston、Edgware，目前最新的是Finchley版本。

* SNAPSHOP：快照版，可以使用，但其仍处理连续不断的开发改进中，不建议使用。
* M：里程碑版。其也会标注上 PRE，preview，预览版，内测版，不建议使用。
* RC：Release Candidate，发行候选版，主要是用于修复 BUG，一般该版本中不会再添加大的功能修改了。正式发行前的版本。
* SR：Service Release，服务发行版，正式发行版。一般还会被标注上 GA，General Available

## SpringCloud版本选择
大版本说明

| **SpringBoot** | **SpringCloud**       | **关系**                                       |
| -------------- | --------------------- | ---------------------------------------------- |
| 1.5.x          | Dalston版本(多尔斯顿) | 兼容Spring Boot 1.5.x，不兼容Spring Boot 2.0.x |
| 1.5.x          | Edgware版本(埃奇韦尔) | 兼容Spring Boot 1.5.x，不兼容Spring Boot 2.0.x |
| 2.0.x          | Finchley版本(芬奇利)  | 兼容Spring Boot 2.0.x，不兼容Spring Boot 1.5.x |

实际开发版本关系

| **spring-boot-starter-parent**             |           | **spring-cloud-dependencies** |              |
| ------------------------------------------ | --------- | ----------------------------- | ------------ |
| 版本号                                     | 发布日期  | 版本号                        | 发布日期     |
| 2.0.2.RELEASE                              | May, 2018 | Finchley.BUILD-SNAPSHOT       | 2018年未知月 |
| 2.0.6.RELEASE                              | Oct, 2018 | Finchley.SR2                  | Oct, 2018    |
| 2.1.4.RELEASEApr,2019Greenwich.SR1Mar,2019 |           |                               |              |

## 服务提供者/消费者项目

本例实现了消费者对提供者的调用，但并未使用到Spring Cloud，但其为后续Spring Cloud的运行测试环境。使用 MySQL 数据库，使用 Spring Data JPA 作为持久层技术。

### 创建提供者工程 01-provider-8081

1. 创建一个 Spring Initializr 工程，并命名为 01-provider-8081。导入 Lombok、Web、JPA 及MySQL 驱动依赖。

   <img src="/img/image/image-20220805170718861.png" alt="image-20220805170718861" style="zoom:67%;" />

2.  **导入 Druid 依赖**

   ```xml
   <!--导入阿里连接池依赖-->
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid</artifactId>
   </dependency>
   
   <!--修改 MySQL 驱动版本-->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>5.1.47</version>
       <scope>runtime</scope>
   </dependency>
   ```

3. **定义实体类**

   springboot使用jackson自动将返回的类转换为json，但有时候我们需要自定义返回的json，因此需要使用JsonIgnoreProperties、JsonIgnore、JsonFormat。

   因为jsonplugin用的是java的内审机制被管理的pojo会加入["hibernateLazyInitializer","handler","fieldHandler"]等属性,json plugin会对这些属性拿出来操作,并读取里面一个不能被反射操作的属性就产生了异常。

   实体类中有的字段值为null，所以在json化的时候，fasterxml.jackson将对象转换为json报错。

   在MappingJackson2HttpMessageConverter通过aop转化为json的时候不处理相关属性

   **@JsonIgnoreProperties**

   此注解用于类类，作用是json序列化时将java bean中的一些属性忽略掉，序列化和反序列化都受影响。

   ```java
   @Data
   @Entity
   // 告诉json plugin组件，在将代理对象转换为json对象时，忽略value对应的数组中的属性.
   @JsonIgnoreProperties ({ "hibernateLazyInitializer", "handler", "fieldHandler"}) 
   public class Depart{
       @ID
       @GeneratedValue(strategy = Generation.IDENTITY)
       private Integer id;
       private String name;
   }
   ```

4. **定义 Repository 接口**

   ```java
   public interface DepartRepository extends JpaRepository<Depart, Integer>{}
   ```

5. **定义** **Service** **接口**

   ```java
   public interface DepartService{
       boolean saveDepart(Depart depart);
       boolean removeDepartById(int id);
       boolean modifyDepart(Depart depart);
       Depart getDepartById(int id);
       List<Depart> listAllDeparts();
   }
   ```

6. **定义 Service 实现类**

   ```java
   @Service
   public class DepartServiceImpl implements DepartService{
       @Autowired
       private DepartRepository repository;
       
       @Override
       public boolean saveDepart(Depart depart){
           Depart obj = repository.save(depary);
           if(obj!=null){
               return true;
           }
           return false;
       }
       
       @Override
       public boolean removeDepartById(int id){
           if(repository.existsById(id)){
               repository.deleteById(id);
               return true;
           }
           return false;
       }
       
       @Override
       public boolean modifyDepart(Depart depart){
           Depart obj = repository.save(depary);
           if(obj!=null){
               return true;
           }
           return false;
       }
       
       @Override
       public Depart getDepartById(int id){
           if(repository.existsById(id)){
               return repository.getOne(id);
           }
           Depart depart = new Depart();
           depart.setName("xxx")
           return depart;
       }
       @Override
       public List<Depart> listAllDeparts(){
           return repository.findAll();
       }
   }
   ```

7. **定义处理器**

   ```java
   @RequestMapping("/provider/depart" )
   @RestController
   public class DepartController{
       @Autowired
       private DepartService service;
       
       @PostMapping("/save")
       public boolean saveHandle(@RequestBody Depart depart){
           return service.saveDepart(depart);
       }
   
       @DeleteMapping("/del/{id}")
       public boolean deleteHandle(@PathVariable("id") int id){
           return service.removeDepartById(id);
       }
       
       @PutMapping("/update")
       public boolean updateHandle(@RequestBody Depart depart){
           return service.modifyDepart(depart);
       }
       
       @GetMapping("/get/{id}")
       public Depart getHandle(@PathVariable("id") int id){
           return service.getDepartById(id);
       }
       
       @GetMapping("/list")
       public List<Depart> listHandle(){
           return service.listAllDeparts();
       }
   }
   ```

8. 修改配置文件

   ```properties
   server:
   	port:8081
   
   spring:
   	# 配置spring-data-jpa
   	jpa:
   		#指定是否在Spring容器启动时创建表，默认为false
   		generate-ddl: true
   		#指定是否在控制台显示SQL语句，默认为false 
   		show-sql: true
   		#指定应用重启时不重新更新表
   		hibernate :
   			ddl-auto: none
   # 配置数据源
   datasource:
   	type:com.alibaba.druid.pool.DruidDataSource
   	driver-class-name:com.mysql.jdbc.Driver
   	ur1:jdbc:mysql:///test?useUnicode=true&amp;characterEncoding=utf8
   	username:root
   	password:111
   #配置日志
   logging:
   	#控制日志在控制台的输出格式
   	pattern :console :level-%level %msg%n
   	#控制日志的显示级别
   	level:
   		#控制Spring Boot启动时显示的日志级别
   		root: info
   		#控制Hibernate运行时的日志級别
   		org.hibernate: info
   		#在show-sql为true时显示SQL中的动态参数值
   		org.hibernate.type.descriptor.sq1.BasicBinder: trace
   		#在show-sql为true时显示查询结果
   		org.hibernate.type.descriptor.sq1.BasicExtractor: trace
   		#控制自己代码运行时显示的日志级别
   		com.abc.msc: debug
   ```

### **创建消费者工程** **01-consumer-8080**

1. **创建工程**

   创建一个 Spring Initializr 工程，并命名为 01-consumer-8080，导入 Lombok 与 Web 依赖。

   <img src="/img/image/image-20220805181154471.png" alt="image-20220805181154471" style="zoom:67%;" />

2. **定义实体类**

   ```java
   public class Depart{
       private Integer id;
       private String name;
   }
   ```

3.  **定义 JavaConfig 容器类**

   ```java
   @Configuration
   public class DepartCodeConfig{
       // 使用RestTemplete先需要放入Spring容器中
       @Bean
       private RestTemplate restTemplate(){
           return new RestTemplate();
       }
   }
   ```

4. **定义处理器类**

   ```java
   @RequestMapping("/consumer/depart" )
   @RestController
   public class DepartController{
       @Autowired
       private RestTemplate restTemplate;
       private static final String SERVICE_PROVIDER = "http://localhost:8081" ;
   
       
       @PostMapping("/save")
       public boolean saveHandle(Depart depart){
           String url = SERVICE_PROVIDER+"/provider/depart/save";
           return restTemplate.postForObject(url,depart,Boolean.class);
       }
   
       @DeleteMapping("/del/{id}")
       public void deleteHandle(@PathVariable("id") int id){
           String url = SERVICE_PROVIDER+"/provider/depart/del"+id;
           restTemplate.delete(url);
       }
       
       @PutMapping("/update")
       public void updateHandle(Depart depart){
           String url = SERVICE_PROVIDER+"/provider/depart/update";
           restTemplate.put(url,depart);
       }
       
       @GetMapping("/get/{id}")
       public Depart getHandle(@PathVariable("id") int id){
           String url = SERVICE_PROVIDER+"/provider/depart/get"+id;
           return restTemplate.getForObject(url,Depart.class);
       }
       
       @GetMapping("/list")
       public List<Depart> listHandle(){
           String url = SERVICE_PROVIDER+"/provider/depart/list";
           return restTemplate.getForObject(url,List.class);
       }
   }
   ```

   

# Eureka服务注册与发现

## 什么是Euraka

- Netflix 在设计Eureka时，遵循的就是AP原则
- Eureka 是 Netflix 的一个子模块，也是核心模块之一。Eureka 是一个基于 REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移，就可以访问到服务，而不需要修改调用的配置文件了，功能类似于Dubbo 的注册中心，比如 Zookeeper；

<img src="/img/image/image-20220812155810941.png" alt="image-20220812155810941" style="zoom:80%;" />

## 原理理解

Eureka 的基本架构

* SpringCloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务注册和发现（对比Zookeeper）

* Eureka 采用了 C-S （客户/服务器模式）的架构设计， EurekaServer 作为服务注册功能的服务器，是`服务注册中心`

* 而系统中的其他微服务。使用Eureka的客户端连接到 EurekaServer 并维持心跳连接。这样系统的维护人员就可以通过EurekaServer来监控系统中各个微服务是否正常运行，SpringCloud的一些其他模块（比如Zuul)就可以通过EurekaServer来发现系统中的其他微服务，并执行相关的逻辑;

* 和 Dubbo 架构对比

  <img src="/img/image/image-20220812161250091.png" alt="image-20220812161250091" style="zoom:60%;" />

  <img src="/img/image/image-20220812161444308.png" alt="image-20220812161444308" style="zoom:60%;" />

* Eureka包含两个组件: **Eureka Serve**r和 **Eureka Client** 。

* Eureka Server提供服务注册服务，各个节点启动后，会在EurekaServer中进行注册，这样Eureka Server中的服务注册表中将会显示所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。

* Eureka Client是一个java客户端，用于简化EurekaServer的交互，客户端同时也具备一个内置的，使用轮询负载算法的负载均衡器。在应用启动后，将会向EurekaServer发送心跳（默认周期为30秒)。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除掉（默认周期为90秒)

**三大角色**

* Eureka Server：提供服务的注册与发现。
* Service Provider：将自身服务注册到Eureka中，从而使消费方能够找到。
* Service Consumer：服务消费方从Eureka中获取注册服务列表，从而找到消费服务。

## 创建Eureka服务中心00-eurekaserver-8000

1. 添加 Eureka Server 依赖
2. 在配置文件中配置 Eureka Server
3. 在启动类上添加@EnableEurekaServer 注解，启动 Eureka Server 功能

### **创建工程**

创建一个 Spring Initializr 工程，命名为 00-eurekaserver-8000，仅导入 Eureka Server 依赖即可

<img src="/img/image/image-20220812164951436.png" alt="image-20220812164951436" style="zoom:70%;" />

工程创建完毕后，在 pom 文件中可以看到如下的版本信息。

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.3.RELEASE</version>
    <relativePath/>
</parent>

<properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version }</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

若使用的是 JDK6、7、8，那么这些依赖无需导入。而 JDK9 及其以上版本需要导入

```xml
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.2.11</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-core</artifactId>
    <version>2.2.11</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</ groupId>
    <artifactId>jaxb-imp1</artifactId>
    <version>2.2.11</version>
</dependency>
<dependency>
    <groupId>javax.activation</groupId>
    <artifactId>activation</artifactId>
    <version>1.1.1</version>
</dependency>

```

### **创建并配置** **yml** **文件**

```yaml
server:
  port: 8000

#Eureka 配置
eureka:
  instance:
    hostname: localhost #Eureka 服务端的实例名称
  client:
    register-with-eureka: false # 表示是否向 Eureka注册中心注册自己
    fetch-registry: false #如果为false，则表示自己为注册中心，我的职责就是维护服务实例并不需要去检索服务不能获取eureka注册信息
    service-url:  # 监控页面~即暴露服务中心地址，
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      # 设置与Eureka Server 交互的地址查询服务和注册服务都需要依赖这个defaultZone 地址
```

### **定义** **spring boot** **启动类**

```java
@SpringBootApplication
@EnableEurekaServer // 服务端的启动类，可以接受别人注册进来~
public class EurekaServer_7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServer_7001.class, args);
    }
}
```

## 创建提供者工程02-provider-8081

1. 添加 Eureka Client 依赖
2. 在配置文件中指定要注册的 Eureka Server 地址，指定自己微服务名称

### **创建工程**

复制 上一章项目01-provider-8081，并重命名为 02-provider-8081

### **添加依赖管理及依赖**

```xml
<properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
</properties>

<!--eureka 客户端依赖--> 
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

<!-- spring-cloud-starter-eureka已经停止维护，建议使用 spring-cloud-starter-netflix-eureka-client -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### **修改** **yml** **文件**

```yaml
server:
	port:8081

spring:
	# 指定当前微服务对外暴露的名称
	application:
		name: abcmsc-provider-depart
	# 配置spring-data-jpa
	jpa:
		#指定是否在Spring容器启动时创建表，默认为false
		generate-ddl: true
		#指定是否在控制台显示SQL语句，默认为false 
		show-sql: true
		#指定应用重启时不重新更新表
		hibernate :
			ddl-auto: none
# 配置数据源
datasource:
	type:com.alibaba.druid.pool.DruidDataSource
	driver-class-name:com.mysql.jdbc.Driver
	ur1:jdbc:mysql:///test?useUnicode=true&amp;characterEncoding=utf8
	username:root
	password:111
#配置日志
logging:
	#控制日志在控制台的输出格式
	pattern :console :level-%level %msg%n
	#控制日志的显示级别
	level:
		#控制Spring Boot启动时显示的日志级别
		root: info
		#控制Hibernate运行时的日志級别
		org.hibernate: info
		#在show-sql为true时显示SQL中的动态参数值
		org.hibernate.type.descriptor.sq1.BasicBinder: trace
		#在show-sql为true时显示查询结果
		org.hibernate.type.descriptor.sq1.BasicExtractor: trace
		#控制自己代码运行时显示的日志级别
		com.abc.msc: debug
		
# Eureka 的配置, 服务注册到哪里
eurake:
	# 指定eureka服务中心
	client:
		service-url:
			defaultZone: http://localhost:8000/eureka/
	# 指定当前eureka的客户端在注册中心的名称
	instance:
		instance-id: abc-msc-provider-8081
		prefer-ip-address: true # 访问路径可以显示ip地址
```

<img src="/img/image/image-20220812175631977.png" alt="image-20220812175631977" style="zoom:80%;" />

info内容构建

现在点击info，出现ERROR页面

<img src="/img/image/image-20220812175706073.png" alt="image-20220812175706073" style="zoom:80%;" />

### **actuator** **完善微服务** **info**

```xml
<!-- actuator完善监控信息 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

```yaml
# 设置info监控终端显示信息
info:
  auth: Tom
  app.name: abcmsc
  app.desc: This is a msc 	of ABC Company
  company.name: www.abb.com
  company.addr: Beijing China
```

<img src="/img/image/image-20220812180040984.png" alt="image-20220812180040984" style="zoom:80%;" />

### **服务发现** **Discovery**

```java
@RequestMapping("/provider/depart" )
@RestController
public class DepartController{
    @Autowired
    private DepartService service;
    
    // 获取一些配置的信息，得到具体的微服务！
    @Autowired
    private DiscoveryClient client;
    
    @PostMapping("/save")
    public boolean saveHandle(@RequestBody Depart depart){
        return service.saveDepart(depart);
    }

    @DeleteMapping("/del/{id}")
    public boolean deleteHandle(@PathVariable("id") int id){
        return service.removeDepartById(id);
    }
    
    @PutMapping("/update")
    public boolean updateHandle(@RequestBody Depart depart){
        return service.modifyDepart(depart);
    }
    
    @GetMapping("/get/{id}")
    public Depart getHandle(@PathVariable("id") int id){
        return service.getDepartById(id);
    }
    
    @GetMapping("/list")
    public List<Depart> listHandle(){
        return service.listAllDeparts();
    }
    
    @GetMapping("/discovery")
    public Object discoveryHandle(){
        //获取服务列表中所有服务名称， 即spring . appl ication. name的值
        List<String> services = client.getServices();
        for(String name : services) {
            //获取指定名称的服务提供者
            List<ServiceInstance> instances = client.getInstances(name);
            for(ServiceInstance instance: instances) {
                //获取服务id,即eureka. instance. instance-id
                //获取服务提供者的URI,及主机名、端口号
                System.out.println(instance.getHost() + "\t"+instance.getPort()+"\t"+instance.getUri()+"\t"+instance.getServiceId()+"\t");
            }
            return services ; 
        }
    }
}
```

### 主启动类中加入@EnableDiscoveryClient 注解

```java
@SpringBootApplication
@EnableEurekaClient // 自动在服务启动后，自动注册到Eureka中！
@EnableDiscoveryClient // 服务发现~ 可以用来获取一些配置的信息，得到具体的微服务
public class DeptProvider_8001 {
    public static void main(String[] args) {
        SpringApplication.run(DeptProvider_8001.class, args);
    }
}
```

<img src="/img/image/image-20220812182428266.png" alt="image-20220812182428266" style="zoom:80%;" />

## 创建消费工程02-consumer-8080

1. 添加 Eureka Client 依赖
2. 在配置文件中指定要注册的 Eureka Server 地址，指定自己微服务名称
3. 在 JavaConfig 类中为 RestTemplate 添加@LoadBalance 注解，实例负载均衡
4. 修改处理器，将“主机名:端口” -> “提供者微服务名称”

### **创建工程**

复制上一章项目 01-consumer-8080，并重命名为 02-consumer-8080。

### **添加依赖管理及依赖**

```xml
<properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Greenwich.SR1</spring-cloud.version>
</properties>

<!--eureka 客户端依赖--> 
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

<!-- actuator完善监控信息 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<!-- spring-cloud-starter-eureka已经停止维护，建议使用 spring-cloud-starter-netflix-eureka-client -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### **修改** **yml** **文件**

```yaml
spring:
	# 指定当前微服务对外暴露的名称
	application:
		name: abcmsc-consumer-depart
		
# Eureka 的配置, 服务注册到哪里
eurake:
	# 指定eureka服务中心
	client:
		service-url:
			defaultZone: http://localhost:8000/eureka/
```

### **修改处理器**

```java
@RequestMapping("/consumer/depart" )
@RestController
public class DepartController{
    @Autowired
    private RestTemplate restTemplate;
    // 将原来的“主机名+端口号”修改为提供者的微服务名称
    private static final String SERVICE_PROVIDER = "http://abcmsc-provider-depart" ;

    
    @PostMapping("/save")
    public boolean saveHandle(Depart depart){
        String url = SERVICE_PROVIDER+"/provider/depart/save";
        return restTemplate.postForObject(url,depart,Boolean.class);
    }

    @DeleteMapping("/del/{id}")
    public void deleteHandle(@PathVariable("id") int id){
        String url = SERVICE_PROVIDER+"/provider/depart/del"+id;
        restTemplate.delete(url);
    }
    
    @PutMapping("/update")
    public void updateHandle(Depart depart){
        String url = SERVICE_PROVIDER+"/provider/depart/update";
        restTemplate.put(url,depart);
    }
    
    @GetMapping("/get/{id}")
    public Depart getHandle(@PathVariable("id") int id){
        String url = SERVICE_PROVIDER+"/provider/depart/get"+id;
        return restTemplate.getForObject(url,Depart.class);
    }
    
    @GetMapping("/list")
    public List<Depart> listHandle(){
        String url = SERVICE_PROVIDER+"/provider/depart/list";
        return restTemplate.getForObject(url,List.class);
    }
}
```

### **修改** **JavaConfig** **类**

```java
@Configuration
public class DepartCodeConfig{
    @LoadBalanced // 开启消费者客户端的负载均衡功能
    @Bean // 使用RestTemplete先需要放入Spring容器中
    private RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

### **修改启动类**

```java
@SpringBootApplication
@EnableDiscoveryClient // 开启服务发现客户端
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

## 服务离线

服务离线，即某服务不能对外提供服务了。服务离线的原因有两种：服务下架与服务下线。这两种方案都是基于 Actuator 监控器实现的。

* **服务下架**：将注册到 Eureka Server 中的 Eureka Client 从 Server 的注册表中移除，这样其实 Client 就无法发现该 Client 了。
* **服务下线**：Client并没有从Eureka Server的注册表中移除（其它Client仍可发现该服务），而是通过修改服务的状态来到达其它 Client 无法调用的目的。

**服务下架操作**

为 Eureka Client 添加 actuator 依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

修改Eureka Client的yml文件

```yaml
management:
  #开启所有监控终端
  endpoints:
    web:
      exposure :
        include: "*"
   #开启shutdown监控终端
   endpoint:
     shutdown :
       enabled: true
```

在 Restlet 中提交如下 POST 请求即可关闭该应用。

<img src="/img/image/image-20220812184045619.png" alt="image-20220812184045619" style="zoom:67%;" />

**服务平滑上下线**

前面的“服务下架”方式存在一个不足是，若还需要再启用该服务，则必须再次启动该应用。我们也可以通过修改服务的状态为 UP 或 DOWN 来设置提供者是否可用，而无需重启应用。这种方式通常称为服务的平滑上下线。

```yaml
management:
  #开启所有监控终端
  endpoints:
    web:
      exposure :
        include: "*"
```

在 Restlet 中提交如下 POST 请求，然后再查看 Eureka 页面，发现服务状态已经变为了DOWN

<img src="/img/image/image-20220812184236399.png" alt="image-20220812184236399" style="zoom:67%;" />

## Eureka的自我保护机制

在 Eureka 服务页面中看到如下红色字体内容，表示当前 EurekaServer 启动了自我保护机制，进入了自我保护模式。

<img src="/img/image/image-20220812182536967.png" alt="image-20220812182536967" style="zoom:50%;" />

我们修改一个服务名，故意制造错误！

**自我保护机制：好死不如赖活着**

一句话总结就是：某时刻某一个微服务不可用，eureka不会立即清理，依旧会对该微服务的信息进行保存！

* 默认情况下，如果EurekaServer在一定时间内没有接收到某个微服务实例的心跳，EurekaServer将会注销该实例（默认90秒)。但是当网络分区故障发生时，微服务与Eureka之间无法正常通行，以上行为可能变得非常危险了——因为微服务本身其实是健康的，**此时本不应该注销这个服务**。Eureka通过**自我保护机制**来解决这个问题——当EurekaServer节点在短时间内丢失过多客户端时（可能发生了网络分区故障),EurekaServer 收到的心跳数量小于阈值，为了保证系统的可用性（AP），那么这个节点就会进入自我保护模式。一旦进入该模式，EurekaServer就会保护服务注册表中的信息，不再删除服务注册表中的数据（也就是不会注销任何微服务)。当网络故障恢复后，该EurekaServer节点会自动退出自我保护模式。
* 在自我保护模式中，EurekaServer会保护服务注册表中的信息，不再注销任何服务实例。当它收到的心跳数重新恢复到阈值以上时，该EurekaServer节点就会自动退出自我保护模式。它的设计哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例。一句话:好死不如赖活着
* 综上，自我保护模式是一种应对网络异常的安全保护措施。它的架构哲学是宁可同时保留所有微服务（健康的微服务和不健康的微服务都会保留)，也不盲目注销任何健康的微服务。使用自我保护模式，可以让Eureka集群更加的健壮和稳定
* 在SpringCloud中，可以使用`eureka.server.enable-self-preservation = false`禁用自我保护模式【不推荐关闭自我保护机制】

```yaml
eureka:
  server:
    #启动自我保护的阈值因子默认为 0.85，即 85%。即 EurekaServer 收到的心跳数量若小于应该收到数量(count/15)的 85%时，会启动自我保护机制。
    renewal-percent-threshold: 0.85
    #关闭自我保护机制，默认为true
    enable-self-preservation: false
```

## EurekaServer集群

搭建的EurekaServer 集群中包含三个 EurekaServer 节点，其端口号分别为 8100、8200 与 8300。

### 集群成员相互关联

在 C:\Windows\System32\drivers\etc 的 host 文件中添加如下域名映射信息。

在hosts文件最后加上，要访问的本机名称，默认是localhost

```txt
127.0.0.1 eureka8100.com
127.0.0.1 eureka8200.com
127.0.0.1 eureka8300.com
```

### **创建** **00-eurekaserver-8100**

复制 00-eurekaserver-8000 工程，并重命名为 00-eurekaserver-8100。 

* 修改pom.xml

  ```xml
  <groupId>com.abc.msc</groupId>
  <artifactId>00-eurekaserver-8100</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>
  ```

* application.yml配置

  ```yaml
  server:
    port: 8100
  eureka:
    instance:
      hostname: eureka8100.com #Eureka 服务端的实例名称
    client:
      register-with-eureka: false
      fetch-registry: false
      service-url:
        # 在集群中使springcloud-eureka-7001关联springcloud-eureka-7002、springcloud-eureka-7003
        defaultZone: http://eureka8100.com:8100/eureka,http://eureka8200.com:8200/eureka,http://eureka8300.com:8300/eureka 
  ```

### **创建** **00-eurekaserver-8200**

再以相同的方式再复制出 00-eurekaserver-8200。

### **创建** **00-eurekaserver-8300**

再以相同的方式再复制出 00-eurekaserver-8300。

### **修改客户端配置文件**

修改客户端工程的配置文件，指定 Eureka 集群的各个主机即可。这里以提供者工程02-provider-8081 为例。

```yaml
eurake:
	# 指定eureka服务中心
	client:
		service-url:
			defaultZone: http://eureka8100.com:8100/eureka,http://eureka8200.com:8200/eureka,http://eureka8300.com:8300/eureka 
	# 指定当前eureka的客户端在注册中心的名称
	instance:
		instance-id: abc-msc-provider-8081
		prefer-ip-address: true # 访问路径可以显示ip地址
```

这样模拟集群就搭建号了，就可以把一个项目挂载到三个服务器上了

<img src="/img/image/image-20220812190036736.png" alt="image-20220812190036736" style="zoom:80%;" />

# Ribbon

## 负载均衡以及Ribbon

* SpringCloud Rinbbon是基于Netflix Ribbon实现的一套**客户端负载均衡的工具**。
* 简单的说，Ribbon是Netflix发布的开源项目，主要功能是提供客户端的软件负载均衡算法，将NetFlix的中间层服务连接在一起。Ribbon的客户端组件提供一系列完成的配置项如：连接超时、重试等等。简单的说，就是配置文件中列出LoadBalancer（简称LB：负载均衡）后面所有的机器，Ribbon会自动的帮助你基于某种规则（如简单轮询，随机连接等等）去连接这些机器。我们也很容易使用Ribbon实现自定义的负载均衡算法！

<img src="/img/image/image-20220813141136336.png" alt="image-20220813141136336" style="zoom:70%;" />

Ribbon能干嘛？

* LB，即负载均衡 (LoadBalancer) ，在微服务或分布式集群中经常用的一种应用。
* 负载均衡简单的说就是将用户的请求平摊的分配到多个服务上，从而达到系统的HA (高用)。
* 常见的负载均衡软件有 Nginx、Lvs 等等。
* Dubbo、SpringCloud 中均给我们提供了负载均衡，SpringCloud 的负载均衡算法可以自定义。
* 负载均衡简单分类：
  * **集中式LB**：即在服务的提供方和消费方之间使用独立的LB设施，如Nginx(反向代理服务器)，由该设施负责把访问请求通过某种策略转发至服务的提供方！
  * **进程式 LB**：将LB逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用，然后自己再从这些地址中选出一个合适的服务器。Ribbon 就属于进程内LB，它只是一个类库，集成于消费方进程，消费方通过它来获取到服务提供方的地址。

## 内置负载均衡策略

*  **RoundRobinRule**

  轮询策略。Ribbon 默认采用的策略。若经过一轮轮询没有找到可用的 provider，其最多轮询 10 轮。若最终还没有找到，则返回 null;

* **RandomRule**

  随机策略，从所有可用的 provider 中随机选择一个;

* **RetryRule**

  重试策略。先按照 RoundRobinRule 策略获取 provider，若获取失败，则在指定的时限内重试。默认的时限为 500 毫秒。

* **BestAvailableRule**

  最可用策略。选择并发量最小的 provider，即连接的消费者数量最少的 provider。

*  **AvailabilityFilteringRule**

  可用过滤算法。该算法规则是：过滤掉处于熔断状态的 provider 与已经超过连接极限的provider，对剩余 provider 采用轮询策略。

*  **ZoneAvoidanceRule**

  zone 回避策略。根据 provider 所在 zone 及 provider 的可用性，对 provider 进行选择。

*  **WeightedResponseTimeRule**

  “权重响应时间”策略。根据每个 provider 的平均响应时间计算其权重，响应时间越快权重越大，被选中的机率就越高。在刚启动时采用轮询策略。后面就会根据权重进行选择了。

> Ribbon 默认采用的是 RoundRobinRule，即轮询策略。但通过修改消费者工程的配置文件，或修改消费者的启动类或 JavaConfig 类可以实现更换负载均衡策略的目的。

<img src="/img/image/image-20220813165517010.png" alt="image-20220813165517010" style="zoom:80%;" />

## 创建提供者03-provider-8082

### 创建工程

复制 02-provider-8081 工程，并重命名为 03-provider-8082。

### **修改配置文件**

```yaml
server:
	port: 8082

spring:
	# 指定当前微服务对外暴露的名称
	application:
		name: abcmsc-provider-depart # 微服务名称名称不需要修改
......
```

### 修改业务实现类

```java
@Service
public class DepartServiceImpl implements DepartService{
    @Autowired
    private DepartRepository repository;
    
    // 读取配置文件中的属性值
    @Value("${server.port}")
    private int port;
    
    @Override
    public boolean saveDepart(Depart depart){
        Depart obj = repository.save(depary);
        if(obj!=null){
            return true;
        }
        return false;
    }
    
    @Override
    public boolean removeDepartById(int id){
        if(repository.existsById(id)){
            repository.deleteById(id);
            return true;
        }
        return false;
    }
    
    @Override
    public boolean modifyDepart(Depart depart){
        Depart obj = repository.save(depary);
        if(obj!=null){
            return true;
        }
        return false;
    }
    
    @Override
    public Depart getDepartById(int id){
        // repository. getOne(id)指定的id对象不存在，则会抛出异常
        if(repository.existsById(id)){
            Depart depart=repository.getOne(id);
            //部门名称后加上端口号
            depart.setName(depart.getName()+port);
            return depart;
        }
        Depart depart = new Depart();
        depart.setName("no is depart"+port);
        return depart;
    }
    @Override
    public List<Depart> listAllDeparts(){
        List<Depart> departs = repository.findAll();
        for(Depart depart:departs){
            //部门名称后加上端口号
            depart.setName(depart.getName()+port);
        }
        return departs();
    }
}
```

## 创建提供者 03-provider-8083

以相同的方式创建提供者工程 03-provider-8083

## 创建提供者 03-provider-8084

以相同的方式创建提供者工程 03-provider-8084。

## 创建消费者03-consumer-loadbalance-8080

### **创建工程**

复制下一章 03-consumer-feign-8080 工程，并重命名为 03-consumer-loadbalance-8080。 

### 修改负载均衡策略

* **修改配置文件**

  ```yaml
  # 修改负载均衡策略
  abcmsc-provider-depart: # 要负载均衡的提供者微服务名称
    ribbon: # 指定要使用的负载均衡策略
      NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
  ```

* **修改** **JavaConfig** **类**

  ```java
  @Configuration
  public class DepartCodeConfig{
      //@LoadBalanced
      //@Bean
      //private RestTemplate restTemplate(){
      //    return new RestTemplate();
      //}
      
      // 指定Ribbon使用随机算法策略
      @Bean
      public IRule loadBalanceRule(){
          return new RandomRule();
          //return new RoundRobinRule();//使用轮询策略
          //return new AvailabilityFilteringRule();//使用轮询策略
          //return new RetryRule();//使用轮询策略
      }
  }
  ```

* **自定义负载均衡策略**

  Ribbon 支持自定义负载均衡策略。负载均衡算法类需要实现 IRule 接口。

  该负载均衡策略的思路是：从所有可用的 provider 中排除掉指定端口号的 provider，剩余 provider 进行随机选择。

  ```java
  public class CustomRule implements IRule {
      private ILoadBalancer lb;
      // 要排除的提供者服务器端口号
      private List<Interger> excludeports;
      
      public CustomRule(){}
      
      public CustomRule(List<Interger> excludePorts){
          this.excludeports = excludePorts;
      }
      @Override
      public void setLoadBalancer(ILoadBalancer lb) {
          this.lb = lb;
      }
      @Override
      public ILoadBalancer getLoadBalancer() {
          return lb;
      }
      @override
      public Server choose(object key) {
          //获取所有可用的Server
          List<Server> servers = lb.getReachableServers();
          //排除指定port的Server
          List<Server> availableServers = getAvailableServers(servers);
          //从剩余Server中随机选择Server
          return getAvailableRandomServers(availableServers);
      }
      
      //从所有Server中排除指定port的Server
      private List<Server> getAvailableServers(List<Server> servers) {
          //若没有指定要排除的Port，则返回所有Server
          if(excludePorts == nu11|| excludePorts.size() == 0) {
              return servers;
          }
          //用于存放可用Server
          List<Server> aservers = new ArrayList<>();
          for (Server server: servers) {
              boolean flag = true;
              for (Integer port:excludePorts) {
                  if(server.getPort() == port) {
                      flag = false;
                      break;
                  }
              }
              //只要flag为true时才说明该Server的port与所有排除的port不相同
              if(flag) {
                  aservers. add(server);
              }
          }
          return aservers;
      }
      
      //随机的从Server列表中获取一个Server
      private Server getAvailableRandomServers(List<Server> servers) {
          // 获取一个[0，servers.size())获取的随机整数
          int index = new Random().nextInt(servers.size());
          return servers.get( index);
      }
  
  }
  ```

  **修改 JavaConfig 类**

  ```java
  @Configuration
  public class DepartCodeConfig{
      //@LoadBalanced
      //@Bean
      //private RestTemplate restTemplate(){
      //    return new RestTemplate();
      //}
      
      // 指定Ribbon使用随机算法策略
      //@Bean
      //public IRule loadBalanceRule(){
      //    return new RandomRule();
      //}
      
      //指定Ribbon使用自定义负载均衡策略
      @Bean
      public IRule loadBalanceRule() {
          List<Integer> ports = new ArrayList<>( );
          ports.add(8083);
          return new CustomRule(ports);
      }
  
  }
  ```

### 主启动类开启负载均衡

```java
//Ribbon 和 Eureka 整合以后，客户端可以直接调用，不用关心IP地址和端口号
@SpringBootApplication
@EnableEurekaClient
//在微服务启动的时候就能去加载我们自定义的Ribbon类
@RibbonClient(name = "abcmsc-provider-depart", configuration = CustomRule.class)
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

# Feign：负载均衡(基于服务端)

## Feign简介

Feign是声明式Web Service客户端，它让微服务之间的调用变得更简单，类似controller调用service。SpringCloud集成了Ribbon和Eureka，可以使用Feigin提供负载均衡的http客户端。

只需要创建一个接口，然后添加注解即可~

Feign，主要是社区版，大家都习惯面向接口编程。这个是很多开发人员的规范。调用微服务访问两种方法

1. 微服务名字 【ribbon】
2. 接口和注解 【feign】

**Feign能干什么？**

* Feign旨在使编写Java Http客户端变得更容易
* 前面在使用Ribbon + RestTemplate时，利用RestTemplate对Http请求的封装处理，形成了一套模板化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一个客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步的封装，由他来帮助我们定义和实现依赖服务接口的定义，==在Feign的实现下，我们只需要创建一个接口并使用注解的方式来配置它 (类似以前Dao接口上标注Mapper注解，现在是一个微服务接口上面标注一个Feign注解)，==即可完成对服务提供方的接口绑定，简化了使用Spring Cloud Ribbon 时，自动封装服务调用客户端的开发量。

> 简单来说Feign，假装，伪装。OpenFeign可以将提供者提供的Restful服务伪装为接口进行消费，消费者只需使用“feign接口 + 注解”的方式即可直接调用提供者提供的 Restful 服务，而无需再使用 RestTemplate。
>
> * 该伪装的 Feign 接口是由消费者调用，与提供者没有任何关系
> * Feign 仅是一个伪客户端，其不会对请求做任何处理。
> * Feign 是通过注解的方式实现 RESTful 请求的。 

Spring Cloud D 版及之前的版本使用的是 Feign，而该项目现已更新为了 OpenFeign。

```xml
<!--openfeign依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</ dependency>

<!--feign依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```

**Feign默认集成了Ribbon**

说到 OpenFeign，不得不提的就是 Ribbon。Ribbon 是 Netflix 公司的一个开源的负载均衡项目，是一个客户端负载均衡器，运行在消费者端。

OpenFeign 也是运行在消费者端的，使用 Ribbon 进行负载均衡，所以 OpenFeign 直接内置了 Ribbon。即在导入 OpenFeign 依赖后，无需再专门导入 Ribbon 依赖了。

利用Ribbon维护了MicroServiceCloud-Dept的服务列表信息，并且通过轮询实现了客户端的负载均衡，而与Ribbon不同的是，通过Feign只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用。

## 创建消费者工程03-consumer-feign-8080

这里无需修改提供者工程，只需修改消费者工程即可。

1. 添加 OpenFeign 依赖
2. 定义 Feign 接口，指定要访问的微服务
3. 修改处理器，使用 Feign 接口来消费微服务
4. 将 JavaConfig 中的 RestTemplate 的创建方法删除
5. 在启动类上添加@EnableFeignClients 注解

### **创建工程**

复制 02-consumer-8080，并重命名为 03-consumer-feign-8080。 

###  **添加** **openfeign** **依赖**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</ dependency>
```

### **定义** **Feign** **接口**

```java
@FeignClient("abcmsc-provider-depart")
@RequestMapping( "/provider/depart")
public interface DepartService {
    @PostMapping("/save")
    boolean saveDepart(@RequestBody Depart depart);

    @DeleteMapping("/del/{id}")
    boolean removeDapartById(@PathVariable("id") int id);
    
    @PutMapping("/update")
    boolean modifyDepart(@RequestBody Depart depart);
    
    @GetMapping("/get/{id}")
    Depart getDepartById(@PathVariable("id") int id);
    
    @GetMapping("/list")
    List<Depart> listAllDeparts();
}
```

### **修改** **JavaConfig** **类**

```java
// @Configuration
public class DepartCodeConfig{
    //开启消息者端的负载均衡功能，默认是轮询策略
    @LoadBalanced
    @Bean
    private RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

### **修改处理器**

```java
@RequestMapping("/consumer/depart" )
@RestController
public class DepartController{
    @Autowired
    private DepartService service;
    
    @PostMapping("/save")
    public boolean saveHandle(Depart depart){
        return service.saveDepart(depart);
    }

    @DeleteMapping("/del/{id}")
    public void deleteHandle(@PathVariable("id") int id){
        return service.removeDapartById(id);
    }
    
    @PutMapping("/update")
    public void updateHandle(Depart depart){
        return service.modifyDepart(depart);
    }
    
    @GetMapping("/get/{id}")
    public Depart getHandle(@PathVariable("id") int id){
        return service.getDepartById(id);
    }
    
    @GetMapping("/list")
    public List<Depart> listHandle(){
        return service.listAllDeparts();
    }
}
```

### **修改启动类**

```java
//Ribbon 和 Eureka 整合以后，客户端可以直接调用，不用关心IP地址和端口号
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients(basePackages = {"com.abc.msc.consumer.service"})
//@ComponentScan("com.su.springcloud")
public class FeignDeptConsumer_80 {
    public static void main(String[] args) {
        SpringApplication.run(FeignDeptConsumer_80.class, args);
    }
}
```

### 扩展

Feign 支持对请求（Feign 客户端向提供者的请求）和响应（Feign 客户端向客户端浏览器的响应）进行 Gzip 压缩以提高通信效率。并且Feign 连接提供者、对于提供者的调用均可设置超时时限

<img src="/img/image/image-20220813135910407.png" alt="image-20220813135910407" style="zoom:67%;" />

```yaml
feign:
  client:
    config:
      default:
        connectTimeout: 5000  #指定Feign连接提供者的超时时限
        readTimeout: 10000  #指定Feign从请求到获取提供者响应的超时时限
  
  compression:
    request:
      enabled: true   #开启对请求的压缩
      #指定对哪些MIME类型的文件进行压缩
      mime-types: ["text/xm1", "application/xml"，'application/json"]
      min-request-size: 2048  #指定启用压缩的最小文件大小，单位字节
    response:
      enabled: true  #开启对客户端响应的压缩
```

# Hystrix：服务熔断

## 分布式系统面临的问题

复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免失败！

## 服务雪崩

多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C，微服务B和微服务C又调用其他的微服务，这就是所谓的“扇出”，如果扇出的链路上某个微服务的调用响应时间过长，或者不可用，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的“雪崩效应”。

<img src="/img/image/image-20220813165442819.png" alt="image-20220813165442819" style="zoom: 67%;" />

对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几十秒内饱和。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障，这些都表示需要对故障和延迟进行隔离和管理，==以达到单个依赖关系的失败而不影响整个应用程序或系统运行==。
我们需要，弃车保帅！

## Hystrix介绍

Hystrix是一个应用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时，异常等，Hystrix 能够保证在一个依赖出问题的情况下，不会导致整个体系服务失败，避免级联故障，以提高分布式系统的弹性。

“断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控 (类似熔断保险丝) ，**向调用方返回一个服务预期的，可处理的备选响应 (FallBack) ，而不是长时间的等待或者抛出调用方法无法处理的异常，这样就可以保证了服务调用方的线程不会被长时间，不必要的占用**，从而避免了故障在分布式系统中的蔓延，乃至雪崩。
<img src="/img/image/image-20220813171606502.png" alt="image-20220813171606502" style="zoom:80%;" />

Hystrix能干嘛

* 服务降级
* 服务熔断
* 服务限流
* 接近实时的监控
* ...

当一切正常时，请求流可以如下所示：

<img src="/img/image/image-20220813174224669.png" alt="image-20220813174224669" style="zoom:67%;" />

当许多后端系统中有一个潜在阻塞服务时，它可以阻止整个用户请求：

<img src="/img/image/image-20220813174310036.png" alt="image-20220813174310036" style="zoom:67%;" />

随着大容量通信量的增加，单个后端依赖项的潜在性会导致所有服务器上的所有资源在几秒钟内饱和。

应用程序中通过网络或客户端库可能导致网络请求的每个点都是潜在故障的来源。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，从而备份队列、线程和其他系统资源，从而导致更多跨系统的级联故障。

<img src="/img/image/image-20220813174510944.png" alt="image-20220813174510944" style="zoom:67%;" />

当使用Hystrix包装每个基础依赖项时，上面的图表中所示的体系结构会发生类似于以下关系图的变化。每个依赖项是相互隔离的，限制在延迟发生时它可以填充的资源中，并包含在回退逻辑中，该逻辑决定在依赖项中发生任何类型的故障时要做出什么样的响应：
<img src="/img/image/image-20220813174554597.png" alt="image-20220813174554597" style="zoom:67%;" />

## 服务熔断

熔断机制是赌赢雪崩效应的一种微服务链路保护机制。

当扇出链路的某个微服务不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。当检测到该节点微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内20次调用失败就会启动熔断机制。熔断机制的注解是 `@HystriCommand`。

服务熔断解决如下问题：

* 当所依赖的对象不稳定时，能够起到快速失败的目的；
* 快速失败后，能够根据一定的算法动态试探所依赖对象是否恢复。

## 服务降级

服务降级是指 当服务器压力剧增的情况下，根据实际业务情况及流量，对一些服务和页面有策略的不处理，或换种简单的方式处理，从而释放服务器资源以保证核心业务正常运作或高效运作。说白了，就是尽可能的把系统资源让给优先级高的服务。

资源有限，而请求是无限的。如果在并发高峰期，不做服务降级处理，一方面肯定会影响整体服务的性能，严重的话可能会导致宕机某些重要的服务不可用。所以，一般在高峰期，为了保证核心功能服务的可用性，都要对某些服务降级处理。比如当双11活动时，把交易无关的服务统统降级，如查看蚂蚁深林，查看历史订单等等。

服务降级主要用于什么场景呢？当整个微服务架构整体的负载超出了预设的上限阈值或即将到来的流量预计将会超过预设的阈值时，为了保证重要或基本的服务能正常运行，可以将一些 不重要 或 不紧急 的服务或任务进行服务的 延迟使用 或 暂停使用。

降级的方式可以根据业务来，可以延迟服务，比如延迟给用户增加积分，只是放到一个缓存中，等服务平稳之后再执行 ；或者在粒度范围内关闭服务，比如关闭相关文章的推荐。

<img src="/img/image/image-20220813182446697.png" alt="image-20220813182446697" style="zoom:80%;" />

由上图可得，当某一时间内服务A的访问量暴增，而B和C的访问量较少，为了缓解A服务的压力，这时候需要B和C暂时关闭一些服务功能，去承担A的部分服务，从而为A分担压力，叫做服务降级。

**服务降级需要考虑的问题**

* 哪些服务是核心服务，哪些服务是非核心服务？
* 哪些服务可以支持降级，哪些服务不能支持降级，降级策略是什么？
* 除服务降级之外是否存在更复杂的业务放通场景，策略是什么？

**自动降级分类**

* 超时降级：主要配置好超时时间和超时重试次数和机制，并使用异步机制探测回复情况
* 失败次数降级：主要是一些不稳定的api，当失败调用次数达到一定阀值自动降级，同样要使用异步机制探测回复情况
* 故障降级：比如要调用的远程服务挂掉了（网络故障、DNS故障、http服务返回错误的状态码、rpc服务抛出异常），则可以直接降级。降级后的处理方案有：默认值（比如库存服务挂了，返回默认现货）、兜底数据（比如广告挂了，返回提前准备好的一些静态页面）、缓存（之前暂存的一些缓存数据）
* 限流降级：秒杀或者抢购一些限购商品时，此时可能会因为访问量太大而导致系统崩溃，此时会使用限流来进行限制访问量，当达到限流阀值，后续请求会被降级；降级后的处理方案可以是：排队页面（将用户导流到排队页面等一会重试）、无货（直接告知用户没货了）、错误页（如活动太火爆了，稍后重试）。

## 服务熔断和降级的区别

* 服务熔断—>服务端：某个服务超时或异常，引起熔断~，类似于保险丝(自我熔断)

  服务降级—>客户端：从整体网站请求负载考虑，当某个服务熔断或者关闭之后，服务将不再被调用，此时在客户端，我们可以准备一个 FallBackFactory ，返回一个默认的值(缺省值)。会导致整体的服务下降，但是好歹能用，比直接挂掉强。

* **触发原因不太一样**：服务熔断一般是某个服务（下游服务）故障引起，而服务降级一般是从整体负荷考虑；

* **管理目标的层次不太一样**，熔断其实是一个框架级的处理，每个微服务都需要（无层级之分），而降级一般需要对业务有层级之分（比如降级一般是从最外围服务开始）

* 实现方式不太一样，服务降级具有代码侵入性(由控制器完成/或自动降级)，熔断一般称为自我熔断。

**熔断，降级，限流：**：

* 限流：限制并发的请求访问量，超过阈值则拒绝；
* 降级：服务分优先级，牺牲非核心服务（不可用），保证核心服务稳定；==从整体负荷考虑==；
* 熔断：==依赖的下游服务故障触发熔断，避免引发本系统崩溃==；系统自动执行和恢复

服务降级是请求发生问题后的一种增强用户体验的方式。发生服务熔断，一定会发生服务降级。但发生服务降级，并不意味着一定是发生了服务熔断。

## fallbackMethod服务降级

Hystrix 对于服务降级的实现方式有两种：fallbackMethod 服务降级，与 fallbackFactory服务降级。首先来看 fallbackMethod 服务降级。

1. 添加 Hystrix 依赖
2. 修改处理器方法。在处理器方法上添加@HystrixCommond 注解
3. 在处理器中定义服务降级方法
4. 在启动类上添加@EnableCircuitBreaker 注解（或将@SpringBootApplication 注解替换为@SpringCloudApplication 注解）

### 创建消费者工程 04-consumer-fallbackmethod-8080

**创建工程**

复制 02-consumer-8080 工程，并重命名为 04-consumer-fallbackmethod-8080。

该工程的运行说明 hystrix 本身与 feign 是没有关系的。

**添加** **hystrix** **依赖**

```xml
<!--导入Hystrix依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

**修改处理器类**

```java
//指定该方法要使用服务降级，即若当前处理器方法在运行过程中发生异常，
//给客户端正常响应时，就会调用fallbackMethod指定的方法
@HystrixCommand(fallbackMethod ="getHystrixHandle")
@GetMapping("/get/{id}") 
public Depart getHandle(@PathVariable("id") int id) {
    String url = "http://localhost:8081/provider/depart/get/" + id;
    return restTemplate.getFor0bject(url, Depart.class);
}
//服务降级方法，即响应给客户端的备选方案
public Depart getHystrixHandle(@PathVariable("id") int id) {
    Depart depart = new Depart();
    depart.setId(id);
    depart.setName("no this depart" );
    return depart;
}
```

 **在启动类添加注解@EnableCircuitBreaker**

```java
@SpringBootApplication
//添加对熔断
@EnableCircuitBreaker
// SpringCloudApplication
public class ConsumerApplication{
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

## fallbackFactory服务降级

当一个服务同时存在类级别与方法级别的降级时，方法级别的降级优先级高。

1. 添加 Hystrix 依赖
2. 定义服务降级类
3. 在 Feign 接口中指定服务降级类
4. 修改配置文件，开启 Feign 对 Hystrix 的支持
5. 在启动类上添加@EnableCircuitBreaker 注解（或将@SpringBootApplication 注解替换为@SpringCloudApplication 注解）

**创建工程**

复制 04-consumer-hystrix-8080 工程，并重命名为 04-consumer-fallbackfactory-8080。

**定义降级处理类**

```java
/**
* 服务降级处理类，该类需要实现FallbackFactory接口,
* 其泛型为该服务降级类所对应的Feign接口
*/
@Component
public class DepartFallbackFactory implements FallbackFactory<DepartService> {
    @Override
    public DepartService create(Throwable throwable) {
        return new DepartService() {
            @override
            public boolean saveDepart(Depart depart) {
                System.out.println( "执行saveDepart()的服务降级处理方法");
                return false ;
            }
            
            @Override
            public boolean removeDepartById(int id) {
                System.out.print1n("执行removeDepartById()的服务降级处理方法");
                return false;
            }
            
            @Override
            public boolean modifyDepart(Depart depart) {
                System.out.print1n("执行modifyDepart()的服务降级处理方法");
                return false;
            }
            // fallbackFactory 的优先级高于fallbackMethod的
            @Override 
            public Depart getDepartById(int id) {
                System.out.println("执行getDepartById()的服务降级处理方法");
                Depart depart = new Depart();
                depart.setId(id);
                depart . setName("no this depart --类级别");
                return depart;
            }
            @Override
            public List<Depart> listAllDeparts() {
                System.out.println("执行listA1lDeparts()的服务降级处理方法");
                return null;
            }
        };
    }
}
```

 **修改 Feign 接口**

```java
//该注解仅仅就是使controller中的service在idea下不报错
@Service
//指定该Service绑定的微服务名称，及降级处理类
@FeignClient(value ="abcmsc-provider-depart",fallbackFactory = DepartFallbackFactory.class)
@RequestMapping( "/provider/depart")
public interface DepartService {
    @PostMapping("/save")
    boolean saveDepart(@RequestBody Depart depart);

    @DeleteMapping("/del/{id}")
    boolean removeDapartById(@PathVariable("id") int id);
    
    @PutMapping("/update")
    boolean modifyDepart(@RequestBody Depart depart);
    
    @GetMapping("/get/{id}")
    Depart getDepartById(@PathVariable("id") int id);
    
    @GetMapping("/list")
    List<Depart> listAllDeparts();
}
```

**方法二：**

```java
@Component
@RequestMapping("/fallback/consumer/depart" )
public class DepartFallback implements DepartService{
            @override
            public boolean saveDepart(Depart depart) {
                System.out.println( "执行saveDepart()的服务降级处理方法");
                return false ;
            }
            
            @Override
            public boolean removeDepartById(int id) {
                System.out.print1n("执行removeDepartById()的服务降级处理方法");
                return false;
            }
            
            @Override
            public boolean modifyDepart(Depart depart) {
                System.out.print1n("执行modifyDepart()的服务降级处理方法");
                return false;
            }
            // fallbackFactory 的优先级高于fallbackMethod的
            @Override 
            public Depart getDepartById(int id) {
                System.out.println("执行getDepartById()的服务降级处理方法");
                Depart depart = new Depart();
                depart.setId(id);
                depart.setName("no this depart --类级别");
                return depart;
            }
            @Override
            public List<Depart> listAllDeparts() {
                System.out.println("执行listA1lDeparts()的服务降级处理方法");
                return null;
            }
}
```

```java
//该注解仅仅就是使controller中的service在idea下不报错
@Service
//指定当前Service所绑定的提供者微服务名称
//fallback指定该接口所绑定的服务降级类
@FeignClient(value ="abcmsc-provider-depart",fallback = DepartFallback.class)
@RequestMapping( "/provider/depart")
public interface DepartService {
    @PostMapping("/save")
    boolean saveDepart(@RequestBody Depart depart);

    @DeleteMapping("/del/{id}")
    boolean removeDapartById(@PathVariable("id") int id);
    
    @PutMapping("/update")
    boolean modifyDepart(@RequestBody Depart depart);
    
    @GetMapping("/get/{id}")
    Depart getDepartById(@PathVariable("id") int id);
    
    @GetMapping("/list")
    List<Depart> listAllDeparts();
}
```

# Dashboard 流监控

Hystrix Dashboard 仪表盘用于以 GUI 的形式展示消费者的执行情况，包括其处理器方法与 Service 方法的调用执行情况，及熔断器 CircuitBreaker 的状态等。当然，这些显示出的数据都是在指定时间窗内的执行情况及状态信息。

**总步骤**

* 添加 hystrix-dashboard 与 actuator 依赖
* 配置文件中开启 actuator 的 hystrix.stream 监控终端
* 在启动类上添加@EnableHystrixDashboard 注解

## 定义消费者工程 04-consumer-dashboard-8080

Hystrix-dashboard 用于监控 Hystrix 服务降级情况，所以应添加在消费者工程中。

###  **创建工程**

本例完全可以直接在 04-consumer-fallbackfeign-8080 工程中进行修改，为了便于演示，这里又新建了一个工程。复制工程 04-consumer-fallbackfeign-8080，并重命名为04-consumer-dashboard-8080。

### **添加依赖**

```xml
<!-- hystrix-dashboard 依赖 --> 
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
<!--actuator 依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency
```

### **修改配置文件**

在配置文件中添加如下内容，用于开启 actuator 的相关监控终端，并调整 Hystrix 隔离线程执行的超时时限。

```yaml
# 开启 actuator 的相关监控终端
management:
  endpoints:
    web:
      exposure:
        include: hystrix.stream
        
# 设置服务熔断时限
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 3000
```

###  **修改启动类**

```java
//指定Service接口所在的包，开启openFeign客户端
@EnableFeignClients (basePackages = "com.abc.consumer.service")
@SpringCloudApplication
@EnableHystrixDashboard // 开启Hystrix仪表盘功能
pub1ic class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

## 定义提供者工程04-provider-8081

### **创建工程**

复制 03-provider-8081 工程，并重命名为 04-provider-8081。因为 03-provider-8081工程的 Service 接口实现类中的 getDepartById()方法中具有 sleep()休眠功能。

### **修改** **Service** **实现类**

这里仅修改 getDepartById()方法。

为了测试方便，这里将 id 值设置为 sleep()的时间，这样设置没有业务上的意义。

为了演示效果，这里测试时 id 的取值为 2、4。当取 2 时，不会超时，但当取 4 时，隔离线程执行会超时。

```java
@Override
pub1ic Depart getDepartById(int id) {
    int time = id;
    try {
        TimeUnit.SECONDS.sleep(time) ;
    } catch (InterruptedException e) {
        e. printStackTrace();
    }
    if(repository. existsById(id)) {
        return repository . getOne(id);
    }
    Depart depart = new Depart( );
    depart. setName("no this depart") ;
    return depart;
}
```

## Turbine聚合监控--监控多个组集群

为了更加方便对集群的运行状态的监控，Turbine 将集群进行了分组。前面我们监控了两个集群，这两个集群默认被分为了一个组，是默认组。我们也可以将集群划分到多个组中使用同一个 Turbine 进行分别监控。

<img src="/img/image/image-20220816143454844.png" alt="image-20220816143454844" style="zoom:67%;" />

Turbine 对于集群分组进行监控的原理是，在集群之上再添加一种分类：组。为每个集群中的 Sever 都添加一个 groupId，而 Turbine 是基于 groupId 进行监控的。这个 groupId 是基于自定义的 Eureka 元数据实现的。

Eureka 元数据是指，Eureka Client 向 Eureka Server 注册时的描述信息，注册数据。其有

两种类型：

* 标准元数据：Eureka 中已经定义好的客户端描述信息。
* 自定义元数据：在客户端配置文件中自己指定的 key-value 数据。

# 微服务网关Zuul

Zull包含了对请求的路由和过滤两个最主要功能：

其中路由功能负责将外部请求转发到具体的微服务实例上，是实现外部访问统一入口的基础，而过滤器功能则负责对请求的处理过程进行干预，是实现请求校验，服务聚合等功能的基础。Zuul和Eureka进行整合，将Zuul自身注册为Eureka服务治理下的应用，同时从Eureka中获得其他服务的消息，也即以后的访问微服务都是通过Zuul跳转后获得。

<img src="/img/image/image-20220816144645489.png" alt="image-20220816144645489" style="zoom:80%;" />

**注意**：Zuul 服务最终还是会注册进 Eureka

**提供**：代理 + 路由 + 过滤 三大功能！

Zuul 能干嘛：

- 路由
- 过滤

**基本环境搭建**

这里需要一个 EurekaServer，一个 zuul 网关，及两个消费者。

## 创建工程05-zuul-consumer--8080

### **创建工程**

复制工程 04-consumer-feign-fallbackmethod-8080，并重命名为 05-zuul-consumer--8080。

### **修改配置文件**

```yaml
spring:
  application:  #指定微服务对外暴露的名称
    name: abcmsc- consumer- depart- 8080
    
eureka :
  client :
    service-ur1: #指定Eureka服务注册中心
      defaultZone: http://localhost:8000/eureka
```

### **修改处理器**

```java
@HystrixCommand(fallbackMethod = "getHystrixHandle")
@GetMapping("/get/{id}")
public Depart getHandle(@PathVariable("id") int id) {
    return service.getDepartById(id);
}

public Depart getHystrixHandle(@PathVariable("id") int id) {
    Depart depart = new Depart() ;
    depart.setId(id);
    depart.setName("no this depart-8080");
    return depart;
}

@HystrixCommand(fallbackMethod = "listHandleHystrix")
@GetMapping("/list")
public List<Depart> listHandle(){
    return service.listAllDeparts();
}

public List<Depart> listHandleHystrix(){
    ArrayList<Depart> departs = new ArrayList<>();
    departs.add(new Depart("no this depart-8080"));
    return departs;
}
```

## 创建工程05-zuul-consumer--8090

### **创建工程**

复制工程 05-zuul-consumer--8080，并重命名为 05-zuul-consumer--8090。

### **修改配置文件**

```yaml
server:
  port: 8090
  
spring:
  application: #指定微服务对外暴露的名称
    name: abcmsc-consumer-depart-8090
    
eureka:
  client:
    service-ur1: #指定Eureka 服务注册中心
    defaultZone: http://localhost:8000/eureka
```

### **修改处理器**

```java
@HystrixCommand(fallbackMethod = "getHystrixHandle")
@GetMapping("/get/{id}")
public Depart getHandle(@PathVariable("id") int id) {
    return service.getDepartById(id);
}
public Depart getHystrixHandle(@PathVariable("id") int id) {
    Depart depart = new Depart();
    depart.setId(id);
    depart.setName=("no this depart - 8090");
    return depart;
}

@HystrixCommand(fallbackMethod = "listHandleHystrix")
@GetMapping("/list")
public List<Depart> listHandle() {
    return service.listAl lDeparts();
}

public List<Depart> listHandleHystrix() {
    ArrayList<Depart> departs = new ArrayList<>();
    departs.add( new Depart("no this depart-8090") );
    return departs;
}
```

## 服务路由00-zuul-9000

当用户提交某请求后，该请求应交给哪个微服务的哪个主机来处理，通过配置可以完成。

### **创建** **zuul** **网关工程**

复制 00-hystrix-turbine-8888 工程，并重命名为 00-zuul-9000。

### **导入依赖**

首先除了 eureka client 依赖后，将其它依赖全部删除，然后再导入 zuul 依赖

```xml
<!--zuul 依赖--> 
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
</dependency>
```

### **修改启动类**

```java
@EnableZuulProxy // 开启zuul代理模式
@SpringBootApplication
public class ZuulApplication9000 {
public static void main(String[] args) {
    SpringApplication.run(ZuulApplication9000.class, args);
}
```

### **修改配置文件**

```yaml
server :
  port: 9000
eureka :
  client:
    service-url:
      defaultZone: http://localhost:8000/eureka
spring:
  application:
    name: abcmsc-zuul-depart
```

## 路由策略配置

### **修改配置文件**

前面的访问方式，需要将微服务名称暴露给用户，会存在安全性问题。所以，可以自定义路径来替代微服务名称，即自定义路由策略。

在配置文件中添加如下配置。

```yaml
zuul :
  routes :
    #指定微服务的路由规则
    abcmsc-consumer-depart-8080: /abc8080/**
    abcmsc-consumer-depart-8090: /abc8090/**
```

### **路由前辍**

在配置路由策略时，可以为路由路径配置一个统一的前辍，以便为请求归类。在前面的配置文件中增加如下配置。

```yaml
zuul :
  #指定统一前辍|
  prefix: /abc
  
  #设置微服务路由规则
  routes:
```

### **服务名屏蔽**

前面的设置方式可以使用指定的路由路径访问到相应微服务，但使用微服务名称也可以访问到，为了防止服务侵入，可以将服务名称屏蔽。

在配置文件中添加如下内容：

```yaml
zuul :
  #指定统一前辍
  prefix: /abc
  
  #屏蔽所有微服务名称
  ignored-services :"*"
  #屏蔽指定微服务名称
  # ignored-services: abcmsc-consumer-depart-8080
```

### **路径屏蔽**

可以指定屏蔽掉的路径 URI，即只要用户请求中包含指定的 URI 路径，那么该请求将无法访问到指定的服务。通过该方式可以限制用户的权限。

```yaml
zuul:
  #指定统一前辍
  prefix: /abc
  # 屏蔽指定URI
  ignored-patterns: /**/list/**
  #屏蔽所有微服务名称
  ignored-services:"*"
  #屏蔽指定微服务名称
  #ignored-services: abcmsc-consumer-depart-8080
```

### **敏感请求头屏蔽**

默认情况下，像 Cookie、Set-Cookie 等敏感请求头信息会被 zuul 屏蔽掉，我们可以将这些默认屏蔽去掉，当然，也可以添加要屏蔽的请求头。

**修改 05-zuul-consumer-8080 工程**

修改该工程处理器中的方法。

```java
@HystrixCommand(fallbackMethod = "getHystrixHandle")
@GetMapping("/get/{id}" )
public Depart getHandle(@PathVariable("id") int id,HttpServletRequest request) {
    return service.getDepartById(id);
}
	
public Depart getHystrixHandle(@PathVariable("id") int id, HttpServletRequest request){
    System.out.println("token =" + request.getHeader("token"));
    System.out.println("Set-Cookie = "+ request.getHeader("Set-Cookie"));
    Depart depart = new Depart( );
    depart.setId(id);
    depart.setName( "no this depart-8080");
    return depart;
}
```

**修改** **00-zuul-9000** **配置文件**

```yaml
zuu1 :
  #指定token被屏蔽
  sensitive-headers: token
```

## 负载均衡

用户提交的请求被路由到一个指定的微服务中，若该微服务名称的主机有多个，则默认采用负载均衡策略是轮询。

### **创建三个消费者工程**

这里要创建三个工程。这三个工程均复制于 04-consumer-fallbackmethod-8080 工程。

* **创建** **05-consumer-8080**

  修改配置文件：

  ```yaml
  server :
    port: 8080
  spring:
    application:
      name: abcmsc-consumer-depart
  eureka :
    client:
      service-url:
        defaultZone: http://localhost:8000/eureka
  ```

  修改处理器：

  ```java
  // 服务降级方法， 即响应给客户端的备选方案
  public Depart getHystrixHandle(@PathVariable("id") int id){
      Depart depart =new Depart();
      depart.setId(id);
      depart.setName("no this depart8080");
      return depart;
  }
  ```

* **创建** **05-consumer-8081**

  修改配置文件：

  ```yaml
  server :
    port: 8081
  spring:
    application:
      name: abcmsc-consumer-depart
  eureka :
    client:
      service-url:
        defaultZone: http://localhost:8000/eureka
  ```

  修改处理器：

  ```java
  // 服务降级方法， 即响应给客户端的备选方案
  public Depart getHystrixHandle(@PathVariable("id") int id){
      Depart depart =new Depart();
      depart.setId(id);
      depart.setName("no this depart8081");
      return depart;
  }
  ```

* **创建** **05-consumer-8082**

  修改配置文件：

  ```yaml
  server :
    port: 8082
  spring:
    application:
      name: abcmsc-consumer-depart
  eureka :
    client:
      service-url:
        defaultZone: http://localhost:8000/eureka
  ```

  修改处理器：

  ```java
  // 服务降级方法， 即响应给客户端的备选方案
  public Depart getHystrixHandle(@PathVariable("id") int id){
      Depart depart =new Depart();
      depart.setId(id);
      depart.setName("no this depart8082");
      return depart;
  }
  ```

### **修改配置文件**

在配置文件中添加如下内容：

```yaml
zuul:
  routes :
    #指定微服务的路由规则
    abcmsc-consumer-depart-8080: /abc8080/**
    abcmsc-consumer-depart-8090: /abc8090/**
    abcmsc-consumer-depart: /abc012/**
```

### **修改负载均衡策略**

修改00-zuul-9000的启动类，在其中直接添加如下代码，更换负载均衡策略为随机算法。

```java
@EnableZuulProxy //开启zuul网关
@SpringBootApplication
public class Zuu1009000{
    public static void main(String[] args) {
        SpringApplication. run(Zuu1009000. class, args);
    }
    
    //设置负载均衡算法为“随机算法”
    @Bean
    public IRule loadBalanceRule() {
        return new RandomRule();
    }
}

```

## 服务降级

当消费者调用提供者时由于各种原因出现无法调用的情况时，消费者可以进行服务降级。那么，若客户端通过网关调用消费者无法调用时，是否可以进行服务降级呢？当然可以，zuul具有服务降级功能。

### **创建工程** **00-zuul-fallback-9000**

复制 00-zuul-9000 工程，并重命名为 00-zuul-fallback-9000。

### **定义** **fallback** **类**

这里仅仅是对名称为 abcmsc-consumer-depart-8080 的微服务进行降级处理

```java
@Component
public class ConsumerFallback implements FallbackProvider {
    //指定要降级的微服务名称
    @Override
    pub1ic String getRoute() {
        // 对所有微服务降级
        // return "*";
        //仅对指定的微服务进行降级
        return "abcmsc-consumer-depart-8080" ;
    }
    
    //定制降级响应
    @Override
    public ClientHttpResponse fallbackResponse(String route, Throwable cause) {
        return new ClientHttpResponse() {
            @Override
            public HttpStatus getStatusCode() throws IOException {
                //返回状态常量
                return HttpStatus.SERVICE_ UNAVAILABLE;
            }
            @Override
            public int getRawStatusCode() throws IOException {
                //返回状态码，这里为503
                return HttpStatus.SERVICE_UNAVAILABLE.value();
            }
            @Override
            public String getStatusText() throws IoException {
                //返回状态码对应的状态短语，这里为"Service Unavailable'
                return HttpStatus.SERVICE_ UNAVAILABLE.getReasonPhrase( );
            }
            @Override
            public void close(){};
            @Override
            public InputStream getBody() throws IoException {
                //设置降级信息
                String msg = "fal1back:"+ ConsumerFallback.this.getRoute();
                return new ByteArrayInputStream(msg.getBytes());
            }
            @Override
            public HttpHeaders getHeaders() {
                HttpHeaders headers = new HttpHeaders();
                headers.setContentType(MediaType.APPLICATION_JSON);
                return headers;
            }
        };
    }
}
```

### **修改配置文件**

这里其实并没有对配置文件进行设置，仅仅是将一些不必要的设置给删除了

```yaml
server:
  port: 9000
      
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8000/eureka
spring:
  application:
    name: abcmsc-zuul-depart
zuul:
  routes:
    #指定微服务的路由规则
    abcmsc-consumer-depart-8080: /abc8080/**
    abcmsc-consumer-depart-8090: /abc8090/**
```

## 请求过滤

在服务路由之前、中、后，可以对请求进行过滤，使其只能访问它应该访问到的资源，增强安全性。此时需要通过 ZuulFilter 过滤器来实现对外服务的安全控制。

<img src="/img/image/image-20220816184306211.png" alt="image-20220816184306211" style="zoom:67%;" />

### **路由过滤实现** **00-zuul-filter-9000**

该过滤的条件是，只有请求参数携带有 user 的请求才可访问/abc8080 工程，否则返回401，未授权。当然，对/abc8090 工程的访问没有限制。简单来说就是，只有当访问/abc8080且 user 为空时是通不过过滤的，其它请求都可以。

复现 00-zuul-9000 工程，并重命名为 00-zuul-filter-9000。

### **定义** **RouteFilter** **类**

```java
@Component
@slf4j
public class RouteFilter extends ZuulFilter {
    
    //指定路由之前执行过滤
    @Override
    public string filterType() {
        //返回"pre
        return FilterConstants.PRE_TYPE ;
    }
    @Override
    public int filterorder() {
        //在系统最小值-3的前面执行
        return -5;
    }
    //对请求通过过滤的后的逻辑
    @Override
    public object run() throws ZuulException {
        Log.info("通过过滤");
        return null;
    }
    
    @Override
    public boolean shouldFilter() {
        RequestContext context = RequestContext.getCurrentContext();
        HttpServletRequest request = context.getRequest();
        String user = request.getParameter("user");
        String uri = request.getRequestURI();
        if (uri.contains("/abc8080") && StringUtils.isEmpty(user)) { 
            context. setSendZuulResponse( false);
            context. setResponseStatusCode( HttpStatus . UNAUTHORIZED. value());
            return false;
        }
        return true;
    }
}
```

































