# Spring概述

> 广义的Spring是指整个Spring家族，包括Spring Boot，Spring Cloud等等项目。
>
> Spring Framework被分成了很多个模块，我们在构建应用程序的时候可以自由选择使用哪个模块。最核心的模块是核心容器:包括配置模块和依赖注入机制。除此之外，Spring Framework提供了对不同应用架构的基础支持，包括消息，交易数据，存储和网站。他还包括了Servlet-based Spring MVC web framework和最新的响应式Spring WebFlux reactive web framework。
>
> 这里探讨的是狭义上的Spring Framework。它可以分为下面几个模块：

1. **Core ** :Spring得以运行的核心，主要包括IoC Container, Events, Resources, i18n, Validation, Data Binding, Type Conversion, SpEL, AOP等内容。
2. **Testing** :测试是Spring中非常重要的一个模块，主要包括Mock Objects, TestContext Framework, Spring MVC Test, WebTestClient等内容。
3. **Data Access**:这一部分是对数据库的访问相关，包括Transactions, DAO Support, JDBC, O/R Mapping, XML Marshalling等内容。
4. **Web Servlet** :传统的对Web Servlet的支持，包括Spring MVC, WebSocket, SockJS, STOMP Messaging等内容。
5. **Web Reactive**:这是Spring5新增的对于响应式系统的支持，包括Spring WebFlux, WebClient, WebSocket等。
6. **Integration** :是对第三方系统的支持，包括Remoting, JMS, JCA, JMX, Email, Tasks, Scheduling, Caching等常用的第三方系统。
7. **Languages** – 最后是对其他JVM语言的支持，包括Kotlin, Groovy等动态语言。

## Core核心技术

Spring的核心技术是Spring中最为重要的内容，而Core中最重要的就是Spring框架的反转控制（IOC）容器。

通过在Spring中使用`面向方面编程（AOP）`技术，IOC在Spring中得到了全面的应用。并且Spring搭建了自己的AOP框架，同时Spring还提供了对第三方AOP框架AspectJ的集成，从而满足不同需求的用户的使用。

IOC也称为依赖注入（DI）。它是指对象仅通过构造函数参数、工厂方法的参数或从工厂方法构造或返回对象实例后，通过在其上设置的属性来定义其依赖项（即与之一起工作的其他对象）的过程。当容器在创建bean时将会注入这些依赖项。

这个过程跟bean自己控制实例化，这与通过直接构造类或服务定位器模式来定位其依赖项的方式相反（因此称为控制反转）。

# Spring5:IOC容器

Spring经过这么多年的发展，已经成为既定的企业级J2EE标准，其最大的优点即是轻量级和其最核心的IOC容器。Spring5提供了很多新的特性，个人认为最主要的有3点：

* 使用JDK8的新特性：最引人注意的是Spring5使用了JDK8中的lambda表达式，让代码变得更加简洁。
* 响应式编程支持：响应式编程是Spring5中最主要的特性之一，响应式编程提供了另一种编程风格，专注于构建对事件做出响应的应用程序。 Spring5 包含响应流和 Reactor。
* 响应式web框架：Spring5提供了一个最新的响应式Web框架，Spring WebFlux，在编程理念和使用习惯方面和之前的传统Web框架都有了很大的区别。

## 什么是IOC

IoC（Inverse of Control:控制反转）是⼀种设计思想，就是 将原本在程序中⼿动创建对象的控制权，交由**Spring**框架来管理。 IoC 在其他语⾔中也有应⽤，并⾮ Spring 特有。 **IoC** 容器是 **Spring**⽤来实现 **IoC** 的载体， **IoC** 容器实际上就是个**Map**（**key**，**value**）**,Map** 中存放的是各种对象。

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注⼊。这样可以很⼤程度上简化应⽤的开发，把应⽤从复杂的依赖关系中解放出来。 **IoC** 容器就像是⼀个⼯⼚⼀样，当我们需要创建⼀个对象的时候，只需要配置好配置⽂件**/**注解即可，完全不⽤考虑对象是如何被创建出来的。

在实际项⽬中⼀个 Service 类可能有⼏百甚⾄上千个类作为它的底层，假如我们需要实例化这个Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把⼈逼疯。如果利⽤IoC 的话，你只需要配置好，然后在需要的地⽅引⽤就⾏了，这⼤⼤增加了项⽬的可维护性且降低了开发难度。

Spring 时代我们⼀般通过 XML ⽂件来配置 Bean，后来开发⼈员觉得 XML ⽂件来配置不太好，于是SpringBoot 注解配置就慢慢开始流⾏起来。

**Spring IoC**的初始化过程：

![image-20220704131149468](/img/image/image-20220704131149468.png)

> 简单点说就是通过配置的参数来构造对象，然后通过配置的属性来实例化其依赖对象。一切都是通过配置来完成的，而不是使用通常的Java new方法来创建对象，也不需要手动去查找或者实例化其依赖对象，一切的一切都是通过Spring IOC容器来实现的。
>
> Bean在Spring中就是一个业务组件，我们通过创建各种Bean来完成最终的业务逻辑功能。在容器内部，每个bean的定义可以被表示为BeanDefinition，通过BeanDefinition可以获得bean的很多信息包括：包名，bean的作用域，生命周期，bean的引用和依赖等。

IOC容器的两个主要包是：org.spring framework.beans和org.springframework.context包。

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.1.8.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.8.RELEASE</version>
</dependency>
```

IOC容器中有两个非常重要的类：BeanFactory和ApplicationContext。

ApplicationContext是BeanFactory的子类，BeanFactory提供了对Bean的操作接口，ApplicationContext则是表示容器本身，提供了bean操作之外的如AOP接入，事件处理，消息资源接入和应用程序上下文等非常有用的特性。

> org.springframework.context.ApplicationContext接口代表着SpringIOC容器，它负责实例化、配置和组装bean。容器通过读取配置元数据获取关于要实例化、配置和组装的对象的指令。配置元数据以XML、Java注释或Java代码来表示。它定义了组成应用程序的对象以及这些对象之间的丰富依赖关系。

> 如果你是创建一个单体应用，那么Spring提供了两个非常有用的ApplicationContext实现，`ClassPathXMLApplicationContext`或`FileSystemXMLApplicationContext`。
>
> ClassPathXMLApplicationContext是从类路径去加载要装载的配置;
>
> FileSystemXMLApplicationContext是从文件路径去装载。

## 配置元数据

Spring的本质就是通过配置来展示和构建业务对象，通常来说，我们可以使用XML文件来配置，当然现在我们也可以使用Java注解和Java代码来实现。

Spring配置由容器必须管理的至少一个或多个bean定义组成。基于XML的配置元数据通常使用来表示。Java配置通常在@Configuration中使用@bean注解的方法。

下面是一个基本的基于XML的定义 daos.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao" class="com.flydean.daos.JpaAccountDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <bean id="itemDao" class="com.flydean.daos.JpaItemDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for data access objects go here -->

</beans>
```

其中id是bean的唯一标记，class是bean的类路径。

## 实例化容器

Spring容器有很多种实例化方法，比如上面讲的单体应用的两个类：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

上面已经列出了daos.xml , 这里我们再列出services.xml :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="com.flydean.services.PetStoreService">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
        <constructor-arg ref="accountDao"/>
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```

service.xml 里面主要定义了对在定义在daos.xml中的bean的引用。这里的引用方式是通过ref. ref引用了daos.xml里面bean的id。

## XML嵌套

除了上面例子中在创建ApplicationContext的时候，加载多个xml文件，其实我们也可以在xml中通过import来引入其他的xml文件。

```xml
 <import resource="daos.xml"/>
```

> resource配置的是要引入的xml的路径，可以使用相对路径和绝对路径。不建议使用相对路径“..”来引用父目录中的文件。这样做会创建对当前应用程序外部文件的依赖关系。直接使用绝对路径又会影响不同部署环境下文件路径的位置。所以比较好的方法是在运行时根据JVM系统属性解析的“$…”占位符。

## groovy bean定义DSL

除了xml定义，Spring也可以使用groovy bean来配置。

下面是daos.groovy的定义：

```java
import com.flydean.daos.JpaAccountDao;
import com.flydean.daos.JpaItemDao;
beans{
    accountDao(JpaAccountDao){

    }
    itemDao(JpaItemDao){

    }
}
```

很简单，就是定义了2个bean。下面是services.groovy的定义：

```java
import com.flydean.services.PetStoreService

beans {
    petStore(PetStoreService, accountDao){
        accountDao=accountDao
        itemDao=itemDao
    }
}
```

## 使用容器

ApplicationContext是高级工厂的接口，它能够维护不同bean的注册及其依赖。

通过使用方法T getBean(String name, Class requiredType)，获取到bean的实例。 ApplicationContext允许您读取bean定义并访问它们，如下例所示：

```java
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);
// use configured instance
List<String> userList = service.getUsernameList();
```

上面讲到了groovy bean配置， 下面是怎么使用groovy bean：

```java
// create and configure beans with groovy
//daos.groovy 必须写在services.groovy前面，否则会报bean找不到的错误
ApplicationContext context = new GenericGroovyApplicationContext("daos.groovy","services.groovy");

// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// use configured instance
List<String> userList = service.getUsernameList();
```

还可以使用XmlBeanDefinitionReader和GenericApplicationContext结合的方式：

```java
GenericApplicationContext context = new GenericApplicationContext();
//reader with xml
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
```

你也可以使用GroovyBeanDefinitionReader来加载Groovy文件，如下所示：

```java
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
```

# Spring5:依赖注入

依赖注入就是在Spring创建Bean的时候，去实例化该Bean构造函数所需的参数，或者通过Setter方法去设置该Bean的属性。

Spring的依赖注入有两种基于构造函数的依赖注入和基于setter的依赖注入。

## 基于构造函数的依赖注入

构造函数的注入是通过构造函数的参数来实现的。如下所示：

```java
public class ExampleBean {

    // Number of years to calculate the Ultimate Answer
    private int years;

    // The Answer to Life, the Universe, and Everything
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

该Bean有一个两个参数的构造函数，那么怎么注入这些参数呢？
有三种方法。

* 方法1：按构造函数的类型匹配：

```xml
 <bean id="exampleBeanA" class="com.flydean.beans.ExampleBean">
        <constructor-arg type="int" value="7500000"/>
        <constructor-arg type="java.lang.String" value="42"/>
 </bean>
```

这里通过指定参数的类型，即可以指定哪个参数是years，哪个参数是ultimateAnswer。

* 构造函数索引：

```xml
    <bean id="exampleBeanB" class="com.flydean.beans.ExampleBean">
        <constructor-arg index="0" value="7500000"/>
        <constructor-arg index="1" value="42"/>
    </bean>
```

Spring中可以通过构造函数的索引来指定特定的参数。要注意Spring的索引是从0开始的。

* 构造函数名字匹配：

```xml
    <bean id="exampleBeanC" class="com.flydean.beans.ExampleBeanWithConstructorProperties">
        <constructor-arg name="years" value="7500000"/>
        <constructor-arg name="ultimateAnswer" value="42"/>
    </bean>
```

如果通过构造函数的名字来匹配，需要注意必须在编译的时候开启调试标志，要不然Spring不能在构造函数中找到参数名。

如果不想启用调试标志，则必须使用@ConstructorProperties JDK注解显式命名构造函数参数。

```java
public class ExampleBeanWithConstructorProperties {

    // Number of years to calculate the Ultimate Answer
    private int years;

    // The Answer to Life, the Universe, and Everything
    private String ultimateAnswer;

    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBeanWithConstructorProperties(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

## 基于Setter的注入

Setter注入主要用来无参构造器或者获得对象实例之后才设置对象的属性。下面是Setter的例子：

```java
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on the MovieFinder
    private MovieFinder movieFinder;

    // a setter method so that the Spring container can inject a MovieFinder
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```

对于的XML文件如下：

```xml
 <!--Setter DI -->
    <bean id="movieFinder" class="com.flydean.beans.MovieFinder"/>

    <bean id="simpleMovieLister" class="com.flydean.beans.SimpleMovieLister">
        <property name="movieFinder" ref="movieFinder"/>
    </bean>
```

除了XML配置，也可以使用注解：@Component、@Controller。或者使用@Configuration注解中的@Bean方法。

> **如何选择？**
>
> 既然有这样两种注入方式，我们怎么选择呢？
>
> 通常来说，对于必须的属性，我们通过构造函数来注入。对于可选属性，我们通过Setter注入。当然你也可以在Setter方法中使用@Required注解。
>
> 当然如果第三方类不公开任何setter方法，那么构造函数注入可能是DI的唯一可用形式。
>
> **循环依赖**
>
> 循环依赖主要出现在构造函数注入的情况。
>
> 类A通过构造函数注入需要类B的实例，类B通过构造函数注入需要类A的实例。如果为要相互注入的类A和类B配置bean，那么SpringIOC容器在运行时检测到这个循环引用，会抛出BeanCurrentlyInCreationException。
>
> 解决方式就是使用Setter注入。

## 依赖注入的配置详解

### 基本类型，字符串或者其他

如果< property/>元素的value属性是基本类型，Spring会将其转换为类需要的类型，配置如下

```xml
 <!--Setter DI -->
    <bean id="movieFinder" class="com.flydean.beans.MovieFinder">
        <property name="name" value="movie"/>
        <property name="number" value="123456"/>
    </bean>
```

这是一个常见的Setter注入。为了简洁，也可以使用p-namespace，如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="movieFinder" class="com.flydean.beans.MovieFinder"
          p:name="movie"
          p:number="123456"/>
</beans>
```

Spring容器使用JavaBeans属性编辑器机制将< value/>元素中的文本转换为java.util.properties实例。这是一个很好的快捷方式，如下所示：

```xml
    <bean id="mappings"
          class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">

        <!-- typed as a java.util.Properties -->
        <property name="properties">
            <value>
                jdbc.driver.className=com.mysql.jdbc.Driver
                jdbc.url=jdbc:mysql://localhost:3306/mydb
            </value>
        </property>
    </bean>
```

### **ref**

通过< ref/>标记的bean属性允许在同一容器或父容器中创建对任何bean的引用，而不管它是否在同一XML文件中。bean属性的值可以与目标bean的id属性相同，也可以与目标bean的name属性中的一个值相同。以下示例显示如何使用ref元素：

```xml
<ref bean="someBean"/>
```

### **内部bean**

在< property/> 或者 < constructor-arg/>元素内部的< bean/>元素可以定义一个内部bean,下面是个例子：

```xml
    <bean id="simpleMovieLister" class="com.flydean.beans.SimpleMovieLister">
        <property name="movieFinder">
            <bean class="com.flydean.beans.MovieFinder">
                <property name="name" value="movie"/>
                <property name="number" value="123456"/>
            </bean>
        </property>
    </bean>
```

内部bean定义不需要ID或名称。如果指定，容器也不会使用这个值作为标识符。容器在创建时也忽略作用域标志，因为内部bean总是匿名的，并且总是用外部bean创建的。不可能单独访问内部bean，也不可能将它们注入到除封闭bean之外的协作bean中。

### **集合**

< list/>, < set/>, < map/>,和 < props/> 分别被用来设置Java Collection类型List, Set, Map，和 Properties 类型的属性和参数。 下面是个例子：

```xml
<bean id="movieFinderE" class="com.flydean.beans.MovieFinder"></bean>

<bean class="com.flydean.beans.CollectionBean">
        <property name="properties">
            <props>
                <prop key="administrator">administrator@example.org</prop>
                <prop key="support">support@example.org</prop>
                <prop key="development">development@example.org</prop>
            </props>
        </property>

        <property name="arrayList">
            <list>
                <value>"a list element followed by a reference"</value>
            </list>
        </property>

        <property name="hashMap">
            <map>
                <entry key="an entry" value="just some string"/>
                <entry key ="a ref" value-ref="movieFinderE"/>
            </map>
        </property>

        <property name="hashSet">
            <set>
                <value>just some string</value>
                <ref bean="movieFinderE" />
            </set>
        </property>
    </bean>

```

### **强类型集合**

通过在Java 5中引入泛型类型，可以使用强类型集合。也就是说，可以声明集合类型，使其只能包含（例如）字符串元素。如果使用Spring将强类型集合注入bean，则可以利用Spring的类型转换支持，以便在将强类型集合实例的元素添加到集合之前将其转换为适当的类型。下面的Java类和bean定义的例子：

```xml
public class SomeClass {
    private Map<String, Float> accounts;

    public void setAccounts(Map<String, Float> accounts) {
        this.accounts = accounts;
    }
}
        <bean id="something" class="com.flydean.beans.SomeClass">
            <property name="accounts">
                <map>
                    <entry key="one" value="9.99"/>
                    <entry key="two" value="2.75"/>
                    <entry key="six" value="3.99"/>
                </map>
            </property>
        </bean>
```

### **Null和Empty字符串值**

Null和空字符串是不一样的，如下：

```xml
<bean class="ExampleBean">
    <property name="email" value=""/>
</bean>
```

上面的例子相当于：

```java
exampleBean.setEmail("");
```

下面是怎么设置null：

```xml
<bean class="ExampleBean">
    <property name="email">
        <null/>
    </property>
</bean>
```

### **c-namespace**

上面讲到了`p-namespace`专门是设置bean的属性用的，同样的`c-namespace`是用来设置构造函数参数的，如下所示：

```xml
    <bean id="exampleBeanA" class="com.flydean.beans.ExampleBean">
        <constructor-arg type="int" value="7500000"/>
        <constructor-arg type="java.lang.String" value="42"/>
    </bean>

    <bean id="exampleBeanB" class="com.flydean.beans.ExampleBean"
    c:years ="10" c:ultimateAnswer="answer"
    />
```

### depends-on

通常一个bean依赖另一个bean，我们会在XML中使用< ref/>来引用，但是这种依赖关系并不直接。我们可以使用depends-on属性来显式强制一个或多个bean在使用此元素的bean初始化之前进行初始化,如下所示：

```xml
   <bean id="beanA" class="com.flydean.beans.SomeClass" depends-on="beanB"></bean>

    <bean id="beanB" class="com.flydean.beans.MovieFinder"></bean>
```

### lazy-init

正常来说ApplicationContext中配置成单例模式的bean都会随Spring启动而初始化，如果有特殊的需要，想延长初始化该bean，则可以使用 lazy-init 。一个lazy-initialized bean告诉IOC容器在第一次请求bean实例时创建它，而不是在启动时。

```xml
<bean id="beanD" class="com.flydean.beans.MovieFinder" lazy-init="true"></bean>
```

但是，当一个惰性初始化bean是一个非惰性初始化的singleton bean的依赖项时，ApplicationContext会在启动时创建惰性初始化bean，因为它必须满足singleton的依赖项。

您还可以通过在< beans/>元素上使用默认的lazy init属性在容器级别控制lazy初始化，下面的示例显示：

```xml
<beans default-lazy-init="true">
    <!-- no beans will be pre-instantiated... -->
</beans>
```

## 自动装载

如果想让Spring自动帮你注入bean的依赖bean时候，可以使用Spring的autowiring功能。autowiring 有4种模式：

| 模式        |                             说明                             |
| ----------- | :----------------------------------------------------------: |
| no          | （默认）无自动装载。bean必须引用由ref定义的元素。对于较大的部署，不建议更改默认设置，因为显式指定合作者可以提供更大的控制度和清晰性。在某种程度上，它记录了系统的结构。 |
| byName      | 按属性名称自动装载。Spring寻找与需要自动装载的属性同名的bean。例如，如果bean定义被设置为按名称自动装载，并且它包含一个master属性（即，它有一个setMaster（..）方法），那么spring将查找名为master的bean定义并使用它来设置该属性。 |
| byType      | 如果容器中只有一个属性类型的bean，则允许自动装载属性。如果存在多个，则会引发一个致命的异常，这表示您不能为该bean使用byType自动装载。如果没有匹配的bean，则不会发生任何事情（未设置属性）。 |
| constructor | 类似于byType，但适用于构造函数参数。如果容器中不只有一个构造函数参数类型的bean，则会引发致命错误。 |

### **自动注入的限制和缺陷**

虽然自动注入用起来很爽，但是它也有如下的缺陷：

* property和constructor-arg的显示设置会覆盖自动注入，==并且自动注入不能注入简单类型==。
* 自动注入不如显示配置精确。
* 自动注入可能和容器中的很多bean相匹配。可能会出现问题。

### **从自动装载中排除Bean**

使用autowire-candidate属性设置为false，可以防止bean被自动注入。该属性只会影响按类型注入的方式。如果按name注入，则不受影响。

```xml
下面是自动注入的例子：
  <bean id="simpleMovieLister" class="com.flydean.beans.SimpleMovieLister" autowire="byType">
    </bean>

    <!--Setter DI -->
    <bean id="movieFinder" class="com.flydean.beans.MovieFinder">
        <property name="name" value="movie"/>
        <property name="number" value="123456"/>
    </bean>
```

SimpleMovieLister如下：

```java
package com.flydean.beans;

import lombok.Data;

@Data
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on the MovieFinder
    private MovieFinder movieFinder;

    // a setter method so that the Spring container can inject a MovieFinder
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
```

## 方法注入

在bean的生命周期不同的时候，如果一个bean要依赖于另外一个bean则可能出现问题。 比如一个单例模式的beanA 依赖于多例模式的beanB。 因为beanA是单例模式的，所以在创建beanA的时候就已经将其依赖的beanB创建了，不可能在每次beanA需要beanB的时候都创建一个新的beanB的实例。

解决方法有很多种，我们一一进行讲解。

### 实现ApplicationContextAware

如果实现了ApplicationContextAware，则可以通过getBean方法在每次需要beanB的时候，请求他的新的实例，如下：

```java
public class CommandManager implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Object process(Map commandState) {
        // grab a new instance of the appropriate Command
        Command command = createCommand();
        // set the state on the (hopefully brand new) Command instance
        command.setState(commandState);
        return command.execute();
    }

    protected Command createCommand() {
        // notice the Spring API dependency!
        return this.applicationContext.getBean("command", Command.class);
    }

    public void setApplicationContext(
            ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```

### **查找方法注入**

查找方法注入是指容器重写container-managed bean上的方法，并返回容器中另一个命名bean。查找通常涉及一个原型bean，如前一节中描述的场景中所示。Spring框架通过使用cglib库中的字节码动态生成覆盖该方法的子类来实现该方法注入。

因为使用了cglib，所以bean不能是final类，方法也不能是final类型。

查找方法不适用于工厂方法，尤其不适用于配置类中的@Bean方法，因为在这种情况下，容器不负责创建实例，因此无法动态创建运行时生成的子类。

下面是一个查找方法的例子：

```java
public abstract class CommandManagerB {

    public Object process(Map commandState) {
        // grab a new instance of the appropriate Command interface
        AsyncCommand command = createCommand();
        return null;
    }

    // okay... but where is the implementation of this method?
    public abstract AsyncCommand createCommand();
}
```

这里我们定义了一个抽象类，要查找的方法就是createCommand。返回的对象类，如下：

```java
public class AsyncCommand {
}
```

下面是XML配置文件：

```xml
    <!-- a stateful bean deployed as a prototype (non-singleton) -->
    <bean id="myCommand" class="com.flydean.beans.AsyncCommand" scope="prototype">
        <!-- inject dependencies here as required -->
    </bean>

    <!-- commandProcessor uses statefulCommandHelper -->
    <bean id="commandManager" class="com.flydean.beans.CommandManagerB">
        <lookup-method name="createCommand" bean="myCommand"/>
    </bean>
```

CommandMangerB每次调用createCommand，都会返回一个新的AsyncCommand实例。

在基于注解的情况下，也可以这样使用：

```java
public abstract class CommandManagerC {

    public Object process(Object commandState) {
        Command command = createCommand();
        return command.execute();
    }

    @Lookup("myCommand")
    protected abstract Command createCommand();
}
```

其中@Lookup(“myCommand”) 中的名字也可以省略，则按照默认的类型来解析。

### **任意方法替换**

我们甚至可以使用replaced-method替换bean的方法实现。我们有个MyValueCalculator类，有一个我们想重写的方法computeValue。

```java
public class MyValueCalculator {

    public String computeValue(String input) {
        // some real code...
        return "abc";
    }

    // some other methods...
}
```

一个实现了org.springframework.beans.factory.support.MethodReplacer接口的类提供了新的方法，如下所示：

```java
public class ReplacementComputeValue implements MethodReplacer {

    public Object reimplement(Object o, Method m, Object[] args) throws Throwable {
        // get the input value, work with it, and return a computed result
        String input = (String) args[0];
        return "def";
    }
}
```

bean的定义如下：

```xml
    <bean id="myValueCalculator" class="com.flydean.beans.MyValueCalculator">
        <!-- arbitrary method replacement -->
        <replaced-method name="computeValue" replacer="replacementComputeValue">
            <arg-type>String</arg-type>
        </replaced-method>
    </bean>

    <bean id="replacementComputeValue" class="com.flydean.beans.ReplacementComputeValue"/>
```

# Spring5:Bean的创建

## 容器中的Bean

Bean在Spring中就是一个业务组件，我们通过创建各种Bean来完成最终的业务逻辑功能。

在容器内部，每个bean的定义可以被表示为BeanDefinition，通过BeanDefinition可以获得bean的很多信息包括：包名，bean的作用域，生命周期，bean的引用和依赖等。

通过ApplicationContext的`getBeanFactory（）`方法，能够获得`DefaultListableBeanFactory`的实现。实现类有两个方法可以将用户自定义的bean注册到Spring容器中。两个方法是：

- `registerSingleton(String beanName, Object singletonObject)`
- `registerBeanDefinition(String beanName, BeanDefinition beanDefinition)`

其中，registerSingleton通过bean名字，和bean实例在注册。registerBeanDefinition则通过bean名字和beanDefinition来注册。

如果想自己构建beanDefinition比较麻烦，他需要实现的方法比较多。

```java
// create and configure beans
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");

//从ApplicationContext 中获取 DefaultListableBeanFactory
DefaultListableBeanFactory beanFactory=(DefaultListableBeanFactory)context.getBeanFactory();

BeanDefinition beanDefinition=beanFactory.getBeanDefinition("daoA");

System.out.println(beanDefinition.getBeanClassName());

    DaoC daoC=new DaoC();
 //将daoC注册到spring容器中
   beanFactory.registerSingleton("daoC", daoC);

 //也可以注册beanDefinition
// beanFactory.registerBeanDefinition();

//从Spring容器中获取到刚刚手动注册的bean
System.out.println(context.getBean("daoC"))
```

## Bean的命名

Bean可以通过id和name属性来命名，id是全局唯一的，name可以有多个，可以通过逗号，分号或者空格来区分多个name。

当然id和name不是必须的，如果你不指定bean的标志符，SPring容器会为你自动分配一个。这种没有名字的Bean一般用在内部bean或者自动装载的情况。

==bean命名一般采用开头小写的驼峰模式。如：accountManager, accountService, userDao, loginController等。==

使用也可以为bean定义别名，一般用在大型系统中，将引入的外部bean和自身的系统命名保持一致。

```xml
<alias name="myApp-dataSource" alias="subsystemA-dataSource"/>
<alias name="myApp-dataSource" alias="subsystemB-dataSource"/>
```

如果你使用Java配置，那么可以使用@Bean来为Bean命名。

## Bean的实例化

实例化bean一般有3种方式，通过构造函数实例化，通过静态工程方法实例化，通过实例的工厂方法实例化。

一般来说我们使用构造函数在Spring容器中创建bean。这个和用new创建bean并将其注入到Spring容器中在本质上是一样的。

工厂方法用的比较少，如果我们需要每次生成一个新的对象时候，就可以考虑使用工厂方法了。工厂方法在后面的作用域范围一文中，我们还会详细的讲解怎么使用。

### **构造函数实例化**

在Spring中，bean的构造函数跟普通构造函数没有什么区别，最常见的就是无参构造函数：

```xml
<bean id="exampleBean" class="examples.ExampleBean"/>
```

当然也可以创建带参数的构造函数：

```xml
    <bean id="petStore" class="com.flydean.services.PetStoreService">
        <constructor-arg ref="accountDao"/>
    </bean>
```

### **静态工厂方法**

静态工厂方法通过class属性指定包含静态工厂方法的类，使用名为factory-method的属性指定工厂方法本身的名称。

这个指定的方法用来返回需要的实例。

```xml
    <!-- factory method -->
    <bean id="clientService"
          class="com.flydean.services.ClientService"
          factory-method="createInstance"/>
```

ClientService的代码如下：

```java
public class ClientService {
    private static ClientService clientService = new ClientService();
    private ClientService() {}

    /**
     * 工厂方法，必须是静态方法
     * @return
     */
    public static ClientService createInstance() {
        return clientService;
    }
}
```

大家注意，这里的createInstance方法必须是静态的。

**实例工厂方法**

和静态工厂方法一样，实例工厂方法只不过是使用实例中的方法来创建相应的bean对象。

这样在factory-bean定义工厂bean实例，在factory-method中定义需要创建bean的方法：

```xml
    <!--factory bean-->
    <bean id="serviceLocator" class="com.flydean.services.DefaultServiceLocator">
    </bean>

    <bean id="serviceA"
          factory-bean="serviceLocator"
          factory-method="createServiceAInstance"/>

    <bean id="serviceB"
          factory-bean="serviceLocator"
          factory-method="createServiceBInstance"/>
```

DefaultServiceLocator代码如下：

```java
public class DefaultServiceLocator {

    private static ServiceA serviceA = new ServiceA();

    private static ServiceB serviceB = new ServiceB();

    public ServiceA createServiceAInstance() {
        return serviceA;
    }

    public ServiceB createServiceBInstance() {
        return serviceB;
    }
}
```

## 类声明为Spring的 bean 的注解

⼀般使⽤` @Autowired `注解⾃动装配 bean，要想把类标识成可⽤于 @Autowired 注解⾃动装配的 bean 的类,采⽤以下注解可实现：

* `@Component `：通⽤的注解，可标注任意类为 Spring 组件。如果⼀个Bean不知道属于哪个层，可以使⽤ @Component 注解标注。

* `@Repository` : 对应持久层即 Dao 层，主要⽤于数据库相关操作。

* `@Service` : 对应服务层，主要涉及⼀些复杂的逻辑，需要⽤到 Dao层。

* `@Controller `: 对应 Spring MVC 控制层，主要⽤户接受⽤户请求并调⽤ Service 层返回数据给前端⻚⾯。

## @Component 和 @Bean 的区别是什么？

1. 作⽤对象不同: @Component 注解作⽤于类，⽽ @Bean 注解作⽤于⽅法。

2. `@Component `通常是通过类路径扫描来⾃动侦测以及⾃动装配到Spring容器中（我们可以使⽤`@ComponentScan `注解定义要扫描的路径从中找出标识了需要装配的类⾃动装配到 Spring 的bean 容器中）。` @Bean` 注解通常是我们在标有该注解的⽅法中定义产⽣这个 bean, @Bean 告诉了Spring这是某个类的示例，当我需要⽤它的时候还给我。

3. `@Bean `注解⽐ Component 注解的⾃定义性更强，⽽且很多地⽅我们只能通过 @Bean 注解来注册bean。⽐如当我们引⽤第三⽅库中的类需要装配到 Spring 容器时，则只能通过@Bean 来实现。

`@Bean` 注解使⽤示例：

```java
@Configuration
public class AppConfig {
 @Bean
 public TransferService transferService() {
 return new TransferServiceImpl();
 }
}

代码相当于下⾯的 xml 配置
<beans>
 <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

下⾯这个例⼦是通过 @Component ⽆法实现的。

```java
@Bean
public OneService getService(status) {
 case (status) {
 when 1:
 return new serviceImpl1();
 when 2:
 return new serviceImpl2();
 when 3:
 return new serviceImpl3();
 }
}
```

# Spring5:Bean作用域

Bean是Spring的根本，Spring本身就是一个一个的bean组成的，bean托管在Spring容器中，那么这些bean的作用域范围是怎么样的呢？

在Spring中，有六个作用域。分别是`singleton`，`prototype`，`request`，`session`，`application`，`websocket`。

除了这六个比较通用的作用域外，Spring3.0开始，添加了`thread`作用域，pring4.2开始，添加了`transaction`作用域。

* singleton : 唯⼀ bean 实例，Spring 中的 bean 默认都是单例的。

* prototype : 每次getBean()请求都会创建⼀个新的 bean 实例。

* request : 每⼀次HTTP请求都会产⽣⼀个新的bean，该bean仅在当前HTTP request内有效。

* session : 每⼀次HTTP请求都会产⽣⼀个新的 bean，该bean仅在当前 HTTP session 内有效。

## Singleton作用域

设计模式大家应该学过，设计模式里面有一个很经典的模式就是Singleton模式。Spring里面的Singleton作用域表明这个定义的bean在整个Spring容器中只有一个实例。任何对这个bean的请求都会返回这个唯一的实例。

下图显示了Singleton作用域的工作方式：

<img src="/img/image/image-20220705203213500.png" alt="image-20220705203213500" style="zoom:80%;" />

Singleton作用域是Spring中bean的默认作用域，所以，Singleton的bean可以如下定义：

```xml
两个bean都是singleton作用域。
<bean id="beanA" class="com.flydean.beans.BeanA" scope="singleton"/>
<bean id="beanB" class="com.flydean.beans.BeanB" />
```

## Prototype作用域

Prototype也是设计模式中一个很经典的模式。Prototype也被很多人也叫他多例模式，就是说可以创建出很多个类的实例。

在Spring容器中，如果一个bean被定义为Prototype，那么，每次通过getBean（）方法来获取这个bean都会返回一个新的bean实例。

下图说明了prototype作用域：

<img src="/img/image/image-20220705203429157.png" alt="image-20220705203429157" style="zoom:80%;" />

一般来说，对于有状态的bean可以使用Prototype。

```xml
<bean id="BeanC" class="com.flydean.beans.BeanB" scope="prototype"/>
```

因为prototype是多例的模式，所以Spring不负责该bean的整个生命周期，一旦bean被创建，交给client使用，Spring就不会再负责维护该bean实例。

==如果在Prototype bean上面配置了生命周期回调方法，那么该方法是不会起作用的。客户端需要自己释放该bean中的资源。==

要让Spring容器释放原型作用域bean所拥有的资源，可以使用自定义`bean post-processor`，用来处理bean的资源清理。

某种意义上Spring的Prototype相当于java中的new方法。

## Singleton Beans 中依赖 Prototype-bean

既然singleon和prototype的作用域范围不一样，如果发生singleton Bean需要依赖于Prototype的时候，Prototype bean只会被实例化一次，然后注入到singleton bean中。

那么怎么解决这个问题呢？可以参照上一篇文章我们讲过的`spring5依赖注入-方法注入`。

## web 作用域

Request, Session, Application, 和WebSocket作用域仅在使用web的Spring ApplicationContext实现中，如果将这些作用域同Spring正常的IOC容器一起使用，则会报错：`IllegalstateException`。

配置web作用域的方式和普通的应用程序稍有不同。Web程序需要运行在相应的Web容器中，通常我们需要将程序入口配置在`web.xml`中。

==如果你使用了Spring MVC的DispatcherServlet，那么不需要做额外的配置，因为DispatcherServlet已经包含了相关的状态。==

* servlet 2.5 web容器中，如果是DispatcherServlet之外的的请求，那么需要注册org.springframework.web.context.request.RequestContextListener。
* servlet 3.0+web容器中，可以使用WebApplicationInitializer接口以编程的方式来添加。

下面是注册RequestContextListener的例子：

```xml
<web-app>
    ...
    <listener>
        <listener-class>
            org.springframework.web.context.request.RequestContextListener
        </listener-class>
    </listener>
    ...
</web-app>
```

如果Listener不能注册，那么可以注册RequestContextFilter，如下所示：

```xml
<web-app>
    ...
    <filter>
        <filter-name>requestContextFilter</filter-name>
        <filter-class>org.springframework.web.filter.RequestContextFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>requestContextFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    ...
</web-app>
```

通过配置DispatcherServlet, RequestContextListener, 和 RequestContextFilter ，我们就可以在相应的请求服务中调用相应范围的bean。

## Request scope

下面是一个LoginAction的定义：

```xml
<bean id="loginActionA" class="com.flydean.actions.LoginAction" scope="request"/>
```

Spring容器通过为每个HTTP请求使用LoginAction定义来创建一个新的LoginAction bean实例。也就是说，LoginAction bean的作用域是在HTTP request级别。可以根据需要更改所创建实例的内部状态，因为从相同的LoginAction bean定义创建的其他实例在状态中看不到这些更改。它们是针对单个请求的。当请求完成处理时，将丢弃该请求的作用域bean。

下面是使用注解@RequestScope的例子：

```java
@RequestScope
@Component
public class LoginAction {
}
```

## Session Scope

下面是Session的例子：

```xml
<bean id="userPerferenceA" class="com.flydean.service.UserPreferences" scope="session"/>
```

Spring容器通过在单个HTTP Session的生命周期中使用UserPreferences bean定义创建一个新的UserPreferences bean实例。换句话说，UserPreferences bean在HTTP Session级别有效。

与request scope的bean一样，您可以根据需要更改创建的实例的内部状态，因为其他也使用从相同的用户首选项bean定义创建的实例的HTTP session实例在状态中看不到这些更改，因为它们是特定于单个HTTP session的。当最终丢弃HTTP session时，也会丢弃作用于该特定HTTP session的bean。

下面是注解驱动的例子：

```java
@SessionScope
@Component
public class UserPreferences {
}
```

## Application Scope

参考下面的例子：

```xml
<bean id="appPerferences" class="com.flydean.service.AppPreferences" scope="application"/>
```

所谓的Application scope就是对于整个web容器来说，bean的作用域是ServletContext级别的，这个和singleton有点类似，但是区别在于，Application scope是ServletContext的单例，singleton是一个ApplicationContext的单例。在一个web容器中ApplicationContext可以有多个。

当然，也可以采用注解的方式来配置：

```java
@ApplicationScope
@Component
public class AppPreferences {
}
```

## 作用域Bean的依赖

之前的文章我们讲到了，如果Singleton bean需要依赖Prototype bean那么可以采用方法注入。

但是如果将短作用域的bean注入到长作用域的bean时，该怎么处理呢？比如将request scope的bean 注入到 session scope的bean中，这个时候可以考虑使用AOP代理。

也就是说，你需要插入一个代理对象，该对象与被代理的对象公开相同的公共接口，但该对象可以从相关作用域（如HTTP请求）中获取到实际的目标对象，并将方法调用委托给实际对象。

### AOP代理

在Spring中，你可以使用 来实现对目标bean的自动代理。

当然，singleton 和 prototype bean都可以使用， 对于prototype bean， 每次代理都会生成一个新的对象。

参考下面的xml配置：

```xml
    <!-- an HTTP Session-scoped bean exposed as a proxy -->
    <bean id="userPreferences" class="com.flydean.service.UserPreferences" scope="session">
        <!-- instructs the container to proxy the surrounding bean -->
        <aop:scoped-proxy/>
    </bean>

    <!-- a singleton-scoped bean injected with a proxy to the above bean -->
    <bean id="userService" class="com.flydean.service.SimpleUserService">
        <!-- a reference to the proxied userPreferences bean -->
        <property name="userPreferences" ref="userPreferences"/>
    </bean>
```

上面的例子中，SimpleUserService 需要一个session scope 的UserPreferences 属性。但是双方作用域范围不同，这时候使用AOP对session scope对象进行代理，每次真正调用的时候，再有代理对象去session scope中查找真正的对象，然后将该对象返回。

一般来说Spring的会使用CGLIB的代理。但是要注意，CGLIB只会代理`非final类`的公共方法。

如果将的proxy-target-class属性的值指定false，则将使用基于JDK接口的代理。使用JDK接口代理，意味着该bean 必须实现特定的接口。并且所有被注入的bean的必须通过其接口之一引用bean。 如下所示：

```xml
    <!-- DefaultUserPreferences implements the UserPreferencesInterface interface -->
    <bean id="userPreferencesC" class="com.flydean.service.DefaultUserPreferences" scope="session">
        <aop:scoped-proxy proxy-target-class="false"/>
    </bean>

    <bean id="userManager" class="com.flydean.service.UserManager">
        <property name="userPreferences" ref="userPreferencesC"/>
    </bean>
```

其中DefaultUserPreferences是UserPreferencesInterface接口的实现，而UserManager的一个属性就是UserPreferencesInterface接口。

### **其他方法**

当然AOP代理并不是唯一的方法，您还可以将注入点（即构造函数、setter参数或autowired字段）声明为ObjectFactory，允许每次需要时getObject（）调用根据需要获取当前实例-而不保留该实例或将其单独存储。

或者使用它的JSR-330变种：Provider，和Provider一起使用，每次查询时都会调用get()方法。

## 自定义作用域

Spring提供了一个`org.springframework.beans.factory.config.Scope`接口来实现自定义作用域的功能。

第一节我们提到了Spring3.0开始，提供了thread的作用域，但是这个作用域需要自己来注册。 我们来看Spring自己的SimpleThreadScope是怎么定义和使用的。

首先 SimpleThreadScope 实现了 Scope接口， Scope接口提供了5个方法：

- Object get(String name, ObjectFactory<?> objectFactory); 从所在作用域返回对象。
- Object remove(String name); 从作用域删除对象
- void registerDestructionCallback(String name, Runnable callback); 注册销户回调方法
- Object resolveContextualObject(String key); 根据key获得上下文对象
- String getConversationId(); 获得当前scope的会话ID

自定义好了Scope类之后，需要将其注册到Spring容器中，可以通过大多数Spring ApplicationContext 的ConfigurableBeanFactory接口来注册：

```java
void registerScope(String scopeName, Scope scope);
```

下面是编程方式的注册：

```java
Scope threadScope = new SimpleThreadScope();
beanFactory.registerScope("thread", threadScope);
```

下面是配置方式的注册：

```xml
    <bean class="org.springframework.beans.factory.config.CustomScopeConfigurer">
        <property name="scopes">
            <map>
                <entry key="thread">
                    <bean class="org.springframework.context.support.SimpleThreadScope"/>
                </entry>
            </map>
        </property>
    </bean>
```

# Spring5:Bean的生命周期管理

## Spring Bean 的生命周期回调

Spring中的Bean是随着Spring容器产生的，当Spring容器关闭的时候，相应的Bean也会消失。当然这个和Bean自身的作用域范围也有关系，但是通常都逃不过`初始化`，`运行`，`关闭`这三个状态。

在Spring中，通常需要在Bean刚刚初始化的时候，或者Bean被销毁的时候做一些额外的资源处理的事情。Spring提供了InitializingBean和DisposableBean接口，前者调用afterPropertiesSet（），为后者调用destroy（），以便bean在初始化和销毁bean时执行某些操作。

```java
public class SampleInitializingBean implements InitializingBean {
    // 在spring的bean的生命周期中,实例化->生成对象->属性填充后会进行
    @Override
    public void afterPropertiesSet() throws Exception {
    log.info("inside afterPropertiesSet");
    }
}
```

```java
public class SampleDisposableBean implements DisposableBean {
    // 容器销毁该bean的时候获得一次回调
    @Override
    public void destroy() throws Exception {
        log.info("destroy");
    }
}
```

当然这样做就和Spring的代码耦合了。我们可以使用JSR-250 的`@PostConstruct`和`@PreDestroy`注解，这样就和Spring框架解耦了。

第三种方法就是使用配置的init-method和destroy-method，或者使用@Bean的initMethod属性和@Bean的destroyMethod属性。

```java
<bean id="exampleInitBean" class="com.flydean.beans.ExampleBean" init-method="init" destroy-method="destroy"/>
```

```java
  @Bean(initMethod = "init",destroyMethod = "destroy")
    public ExampleBean getExampleBean(){
        return new ExampleBean();
    }
```

也可以为所有的bean都添加一个默认的初始化方法,和销毁方法：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd"
    default-init-method="init" default-destroy-method="destroy">
```

## 总结生命周期机制

从Spring2.5之后，你有3种方式来控制bean的生命周期：

* InitializingBean和DisposableBean回调接口
* 自定义init() 和destroy() 方法。
* @PostConstruct 和 @PreDestroy注解.

如果为initialization配置了多种生命周期的多个名字，那么执行顺序如下：

1. @PostConstruct 的方法注解
2. InitializingBean接口里面的afterPropertiesSet() 方法
3. 自定义的init() 方法

同样的Destroy也是一样的顺序：

1. @PreDestroy 的方法注解
2. DisposableBean接口里面的destroy() 方法
3. 自定义的destroy() 方法

## startup和Shutdown回调

上面我们讲了在bean初始化和销毁的时候的回调。一般来说就已经够用了。如果需要自己管理生命周期对象，比如启动和停止一些后台进程的时候，Spring提供了Lifecycle接口。

```java
public interface Lifecycle {

    void start();

    void stop();

    boolean isRunning();
}
```

任何Spring管理的对象都可以实现Lifecycle接口。当ApplicationContext收到一个start或者stop信号时，他会将该信号传递给所有的Lifecycle接口的实现。

Spring提供了LifecycleProcessor接口，该接口的默认实现是DefaultLifecycleProcessor ：

```java
public interface LifecycleProcessor extends Lifecycle {

    /**
     * Notification of context refresh, e.g. for auto-starting components.
     */
    void onRefresh();

    /**
     * Notification of context close phase, e.g. for auto-stopping components.
     */
    void onClose();

}
```

onRefresh()和onClose()会去调用实现了Lifecycle接口的start（）和close（）方法。

如果需要实现启动和关闭回调的顺序，则可以实现SmartLifecycle接口，该接口提供了getPhase（）方法:

```java
 public interface SmartLifecycle extends Lifecycle, Phased {

    boolean isAutoStartup();

    void stop(Runnable callback);
}
```

启动时，具有最低phase的对象首先启动。停止时，按相反顺序执行。

## 优雅的关闭Spring IoC容器

如果是Spring WEB应用程序，Spring的web基础的ApplicationContext实现，已经有代码优雅的关闭 Spring IoC 容器。

这里我们考虑非web情况，我们需要注册一个shutdown hook到JVM中。这样将保证优雅的关闭，并且在单例bean中调用相关的销毁方法，让所有的资源得到释放。

调用ConfigurableApplicationContext接口中的registerShutdownHook()来注册一个shutdown hook， 如下所示：

```java
    public static void main(final String[] args) throws Exception {
        ConfigurableApplicationContext ctx = new ClassPathXmlApplicationContext("bean-lifecycle.xml");

        // add a shutdown hook for the above context...
        ctx.registerShutdownHook();

        // app runs here...

        // main method exits, hook is called prior to the app shutting down...
    }
```

## Spring Bean的完整生命周期

* Bean 容器找到配置⽂件中 Spring Bean 的定义。
* Bean 容器利⽤ Java Reflection API 创建⼀个Bean的实例。
* 如果涉及到⼀些属性值 利⽤ set() ⽅法设置⼀些属性值
* 如果 Bean 实现了 BeanNameAware 接⼝，调⽤ setBeanName() ⽅法，传⼊Bean的名字。
* 如果 Bean 实现了 BeanClassLoaderAware 接⼝，调⽤ setBeanClassLoader() ⽅法，传⼊ ClassLoader 对象的实例
* 与上⾯的类似，如果实现了其他 *.Aware 接⼝，就调⽤相应的⽅法。
* 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执⾏` postProcessBeforeInitialization() `⽅法
* 如果Bean实现了 InitializingBean 接⼝，执⾏ afterPropertiesSet() ⽅法
* 如果 Bean 在配置⽂件中的定义包含 init-method 属性，执⾏指定的⽅法
* 如果有和加载这个 Bean的 Spring 容器相关的 BeanPostProcessor 对象，执⾏ `postProcessAfterInitialization() `⽅法
* 当要销毁 Bean 的时候，如果 Bean 实现了 DisposableBean 接⼝，执⾏ destroy() ⽅法。
* 当要销毁 Bean 的时候，如果 Bean 在配置⽂件中的定义包含 destroy-method 属性，执⾏指定的⽅法。

<img src="/img/image/image-20220711150941628.png" alt="image-20220711150941628" style="zoom:80%;" />

# Spring5:容器扩展

Spring提供了一系列的接口来提供对Spring容器的扩展功能

## BeanPostProcessor自定义bean

前面一篇文章我们在自定义bean中提到，可以实现Spring的InitializingBean和DisposableBean接口来实现自定义bean的生命周期。如果是容器级别的，Spring提供了更加强大的`BeanPostProcessor`，来实现在容器级对Bean的扩展。

BeanPostProcessor接口定义了两个方法：

```java
    default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }
```

该方法在调用容器初始化方法（如InitializingBean.afterPropertiesSet（）或任何声明的init方法）之前，以及在任何bean初始化之后，被调用。

```java
   default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }
```

该方法在容器初始化方法之后被调用。

BeanPostProcessor可以配置多个，如果想控制多个BeanPostProcessor的顺序，可以实现`Ordered`接口，来定义他们的顺序。

虽然BeanPostProcessor是通过ApplicationContext自动检测的，你也可通过ConfigurableBeanFactory的addBeanPostProcessor来手动注册。手动注册则其Ordered失效，以手动注册的先后为准。

还要注意，以编程方式注册的BeanPostProcessor实例总是在注册为自动检测的实例之前进行处理，而不接收任何显式排序。

所有BeanPostProcessor实例和这些实例直接引用的bean都在启动时实例化，因为AOP自动代理是作为BeanPostProcessor本身实现的，所以BeanPostProcessor实例和它们直接引用的bean都不符合自动代理的条件。

下面是一个调用的例子：

```xml
    <bean id="beanA" class="com.flydean.beans.BeanA"/>
    <bean id="beanB" class="com.flydean.beans.BeanB"/>

    <bean class="com.flydean.beans.InstantiationTracingBeanPostProcessor"/>
```

调用实现：

```java
    public static void main(final String[] args) throws Exception {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("bean-post-processor.xml");
        BeanA beanA = (BeanA) ctx.getBean("beanA");
        System.out.println(beanA);
    }
```

## BeanFactoryPostProcessor自定义配置元数据

BeanFactoryPostProcessor接口的语义与BeanPostProcessor的语义相似，但有一个主要区别：BeanFactoryPostProcessor对Bean配置元数据进行操作。也就是说，Spring IOC容器允许BeanFactoryPostProcessor读取配置元数据，并可能在容器实例化BeanFactoryPostProcessor实例以外的任何bean之前对其进行更改。

BeanFactoryPostProcessor也可以配置多个，并通过实现Ordered接口来确定执行顺序。BeanFactoryPostProcessor定义了一个方法：

```java
    void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;
```

通过该方法可以获取到可配置的beanFactory从而对bean定义进行修改。

Spring提供了很多预定义的bean工厂后处理器，例如PropertyOverrideConfigurer和PropertyPlaceholderConfigurer。下面我们通过例子来说明怎么使用。

**PropertyOverrideConfigurer类名替换**

PropertyPlaceholderConfigurer主要用于从外部的Property文件读取属性，用来替换定义好的配置，这样做可以使部署应用程序的人员自定义特定于环境的属性，如数据库URL和密码，而不必为容器修改一个或多个XML定义主文件从而增加复杂性或风险。

下面是配置的XML文件：

```xml
    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations" value="classpath:jdbc.properties"/>
    </bean>

    <bean id="dataSource" destroy-method="close"
          class="com.flydean.beans.BasicDataSource">
        <property name="driverClassName" value="{jdbc.driverClassName}"/>
        <property name="url" value="{jdbc.url}"/>
        <property name="username" value="{jdbc.username}"/>
        <property name="password" value="{jdbc.password}"/>
    </bean>
```

这个例子展示了属性被配置在外部的Properties文件中。在运行时，使用PropertyPlaceholderConfigurer将元数据替换成DataSource中的某些属性。要替换的值被指定为${property-name}格式的占位符，该格式遵循ant和log4j以及JSP EL样式。

真实的值取自外部的Java Properties格式的文件：

```java
jdbc.driverClassName=org.hsqldb.jdbcDriver
jdbc.url=jdbc:hsqldb:hsql://production:9002
jdbc.username=sa
jdbc.password=root
```

**PropertyOverrideConfigurer属性覆盖**

PropertyOverrideConfigurer可以用来覆盖Bean属性的默认值，或者设置新的值。我们看一个例子：

```xml
    <bean class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">
        <property name="locations" value="classpath:override.properties"/>
        <property name="properties">
            <value>beanOverride.url=com.something.DefaultStrategy</value>
        </property>
    </bean>
    <bean name="beanOverride" class="com.flydean.beans.BeanOverride"/>
```

对应的类是：

```java
@Data
public class BeanOverride {

    private String name="beanA";
    private String url="http://www.163.com";

}
```

它的默认属性会被覆盖。

## 使用FactoryBean自定义实例化逻辑

FactoryBean接口提供3个方法：

* Object getObject(): 返回工厂创建的实例，该实例可能是被共享的， 取决于该实例是单例还是多例模式。
* boolean isSingleton():判断FactoryBean返回的是单例还是多例。
* Class getObjectType():返回getObject() 方法返回的类型，如果提前不知道类型，那么返回null。

我们可以实现FactoryBean接口来自定义Bean的实现逻辑。

```java
public class BeanFactoryBean implements FactoryBean {

    @Resource
    private BeanA beanA;

    @Override
    public Object getObject() throws Exception {
        return beanA;
    }

    @Override
    public Class<?> getObjectType() {
        return BeanA.class;
    }
}
```

下面是其配置：

```xml
    <context:annotation-config/>
    <bean id="beanA" class="com.flydean.beans.BeanA"/>

    <bean id="beanFactoryBean" class="com.flydean.beans.BeanFactoryBean"/>
```

如何使用？

```java
    public static void main(final String[] args) throws Exception {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("bean-factory.xml");
        BeanFactoryBean beanFactoryBean = (BeanFactoryBean) ctx.getBean("&beanFactoryBean");
        System.out.println(beanFactoryBean.getObject());
        System.out.println(beanFactoryBean.getObjectType());

        BeanA beanA=(BeanA)ctx.getBean("beanFactoryBean");
        System.out.println(beanA);
    }
```

当需要向容器请求实际的FactoryBean实例本身而不是它生成的bean，在调用ApplicationContext的getbean（）方法时，在bean的ID前面加上符号（&）。因此，对于ID为beanFactoryBean的给定FactoryBean，在容器上调用getBean（“beanFactoryBean”）返回FactoryBean生成的bean，而调用getBean（“&beanFactoryBean”）则返回FactoryBean实例本身。

# Spring5:基于注解的容器配置

Spring的容器配置可以有两种方式，一种基于XML文件， 一种基于注解。注解注入在XML注入之前执行。因此，当两个同时使用时，XML配置会覆盖注解注入的属性。

本章会主要介绍 @Required，@Autowired， @PostConstruct, @PreDestroy 和 @Resource 这几个注解。

这几个注解都是由 来引入的。本质上引入这个配置会隐式的注册AutoWiredAnnotationBeanPostProcessor（提供@Autowired），CommonAnnotationBeanPostProcessor（提供@PostConstruct, @PreDestroy, @Resource），RequiredAnnotationBeanPostProcessor（提供 @Required),从而提供各个注解的功能。

## @Required

@Required 一般用在方法上面，表示该方法的参数必须能通过配置或者自动装载来填充。通常如果某个属性是必须的，我们会使用这个注解。	

不过从Spring Framework 5.1开始，@Required注解正式被弃用，取而代之的是使用构造函数注入用于所需的属性，或使用InitializingBean.afterPropertiesSet（）的自定义实现以及bean属性setter方法。

```java
public class RequiredBean {

    private BeanA  beanA;

    @Required
    public void setBeanA(BeanA beanA){
        this.beanA=beanA;

    }
}
```

## @Autowired

@Autowired 就是自动注入所需要的字段，参数等。JSR 330的@Inject注解可以代替spring的@Autowired注解。

你可以将@Autowired注解到构造器中，如下所示：

```java
public class AutowiredBean {

    private BeanA beanA;

    @Autowired
    public AutowiredBean(BeanA beanA){
        this.beanA=beanA;
    }
}
```

从SpringFramework4.3开始，如果目标bean只定义了一个构造函数，那么就不再需要在此类构造函数上使用@Autowired注解。但是，如果有多个构造函数可用，则必须至少对其中一个进行注解，以告诉容器使用哪一个。

@Autowired也可以注解到传统的setter方法，如下例子所示：

```java
public class AutowiredBean {

    private BeanB beanB;

    @Autowired
    public void setBeanB(BeanB beanB){
        this.beanB=beanB;
    }
}
```

也可以把注解应用到任何名字和多个参数，如下所示：

```java
    @Autowired
    public void configAB(BeanA beanA , BeanB beanB){
        this.beanA=beanA;
        this.beanB=beanB;
    }
```

@Autowired也可以用在字段上,如下所示：

```java
    @Autowired
    private BeanC beanC;
```

还可以通过将注解添加到需要该类型数组的字段或方法，那么可以从ApplicationContext中获取到该特定类型的所有bean，如下例所示：

```java
    @Autowired
    private BeanC[] beanCList;
```

如果希望数组或列表中的项按特定顺序排序,目标bean可以实现org.springframework.core.Ordered接口，或者可以使用@Order或标准的@Priority注解。
否则，它们的顺序遵循容器中相应目标bean定义的注册顺序。

Map实例也可以被注入，只要key是String类型。Map value包括了所有的类型匹配的Bean，keys是该bean的名字。如下所示：

```java
    @Autowired
    public void configMapA(Map<String,BeanA> mapA){
    this.mapA=mapA;
    }
```

@Autowired有个required属性，如果要注入的bean有可能不存在，则可以如下所示：

```java
    @Autowired(required = false)
    public void setBeanC(BeanC beanC){
    }
```

建议使用@Autowired的’required’属性而不是使用setter方法上的@Required注解。“required”属性表示自动装载需要该属性, 如果无法自动装载，则忽略该属性。而对于@Required来说，如果未定义任何值，则会报异常。

也可以通过Java 8的java.util.Optional表示特定依赖项的非必需性质，如下示例显示：

```java
    @Autowired
    public void setMovieFinder(Optional<BeanC> BeanC) {
    }
```

在Spring Framework 5.0中，你也可以使用@Nullable注解：

```java
    @Autowired
    public void setMovieFinderC(@Nullable BeanC beanC) {
    }
```

Spring可以使用@Autowired来自动解析一些默认存在的bean如：BeanFactory、ApplicationContext、Environment、ResourceLoader、ApplicationEventPublisher和MessageSource。这些接口及其扩展接口（如ConfigurableApplicationContext或ResourcePatternResolver）。
如下所示，自动注入ApplicationContext：

```java
@Autowired
private ApplicationContext context;
```

注意： @Autowired, @Inject, @Value, 和 @Resource 注解是在Spring的BeanPostProcessor中处理的，这意味着你不能将这些注解用在你自己的BeanPostProcessor，BeanFactoryPostProcessor类型。

## @primary

当按类型注入的时候，可能会有多个候选项，则可以通过@Primary注解表示优先的对象。如下所示：

```java
@Configuration
public class ConfigBean {

    @Bean
    @Primary
    public BeanA firstBeanA() { return new BeanA(); }

    @Bean
    public BeanA secondBeanA() {  return new BeanA();}

}
```

## @Qualifier

@Primary是一种在多个实例中按类型使用自动装载的有效方法，但是如果你希望对注入的Bean进行更细粒度的控制时候，可以使用@Qualifier。如下所示：

```java
    @Bean
    @Qualifier("main")
    public BeanC beanC() {  return new BeanC();}
    @Autowired
    @Qualifier("main")
    private BeanA beanA;

    @Autowired
    public void setBeanA(@Qualifier("main") BeanA beanA){

    }
```

限定符的值并不是唯一的，它只是一个过滤标准。

==@Autowired一般用来通过类型匹配，@Resource则是通过名称匹配。==

也可以创建自定义注解：

```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Genre {

        String value();
}
```

## 泛型

除了@Qualifier注解外，还可以使用Java泛型类型作为隐式的限定形式。例如，假设您具有以下配置：

```java
public class StringStore implements Store<String> {
}

public class IntegerStore implements Store<Integer> {
}
    @Bean
    public StringStore stringStore() {
        return new StringStore();
    }

    @Bean
    public IntegerStore integerStore() {
        return new IntegerStore();
    }
public class GenericBean {

    @Autowired
    private Store<String> s1; // <String> qualifier, injects the stringStore bean

    @Autowired
    private Store<Integer> s2; // <Integer> qualifier, injects the integerStore bean

    // Inject all Store beans as long as they have an <Integer> generic
    // Store<String> beans will not appear in this list
    @Autowired
    private List<Store<Integer>> s;

}
```

## @Resource

@Resource用在字段或者Setter方法上，默认情况下@Resource通过名字来注入。

```java
public class ResourceBean {

    @Resource(name = "beanA")
    private BeanA BeanA;
}
```

如果未显式指定名称，则从字段名或setter方法派生默认名称.

在@Resource用法中，如果没有指定显式名称，并且类似于@Autowired，@Resource会找到一个主类型匹配，而不是指定的bean，并解析已知的可解析依赖项：BeanFactory、ApplicationContext、ResourceLoader、ApplicationEventPublisher，和MessageSource接口。

## @PostConstruct和@PreDestroy

这两个注解主要用做生命周期回调。如下所示：

```java
public class ConstructBean   {

    @PostConstruct
    public void populateMovieCache() {
        // populates the movie cache upon initialization...
    }

    @PreDestroy
    public void clearMovieCache() {
        // clears the movie cache upon destruction...
    }
}
```

与@Resource一样，@PostConstruct和@PreDestroy注解类型是JDK 6到8标准Java库的一部分。然而，整个javax.annotation包与JDK 9中的核心Java模块分离，并最终在JDK 11中被删除。如果需要，javax.annotation-api工件现在需要通过maven central获得，只需像其他库一样添加到应用程序的类路径中即可。

# Spring5:组件扫描

上一篇文章我们讲到了`annotation-config`配置，它主要用于bean内部的属性注入。而bean本身则需要通过配置的方式来定义。如果想使用配置的方式来定义bean，则可以使用`component-scan`，如下：

```xml
<context:component-scan base-package="com.flydean"/>
```

component-scan会扫描类路径里面的注解，包括（@Component, @Repository, @Service,
@Controller, @RestController, @ControllerAdvice, 和@Configuration ）， 当然component-scan默认包含了annotation-config，我们可以直接在这些配置bean中使用上篇文章讲到的注解。

## @Component

@Component表示该bean是一个组件，@Component是任何Spring管理的组件的通用原型。@Repository、@Service和@Controller是@Component针对更具体的用例（分别在持久性、服务和表示层中）的特殊注解。因此，您可以用@Component注解组件类，但是，通过用@Repository、@Service或@Controller注解它们，您的类更具有语义性。通常更适合在AOP中做进一步的业务逻辑处理。

## 元注解和组合注解

所谓元注解就是可以用在其他注解中的注解。 像之前提到的@Component就是@Service的元注解。如下：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component 
public @interface Service {

    // ....
}
```

@Component会导致@Service和@Component一样被对待。
当然你也可以组合使用元注解，或者自定义元注解。例如，Spring的@SessionScope注解将作用域名称硬编码为session，但仍允许自定义proxyMode。以下列表显示了sessionScope注解的定义：

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Scope(WebApplicationContext.SCOPE_SESSION)
public @Interface SessionScope {

    /**
     * Alias for {@link Scope#proxyMode}.
     * <p>Defaults to {@link ScopedProxyMode#TARGET_CLASS}.
     */
    @AliasFor(annotation = Scope.class)
    ScopedProxyMode proxyMode() default ScopedProxyMode.TARGET_CLASS;

}
```

你可以不定义@SessionScope里面的proxyMode， 如下：

```java
@Service
@SessionScope
public class SessionScopedService {
    // ...
}
```

你也可以重写proxyMode，如下：

```java
@Service
@SessionScope(proxyMode = ScopedProxyMode.INTERFACES)
public class SessionScopedUserService implements UserService {
    // ...
}
```

\#@ComponentScan和filters

上面我们讲到，要是要使用组件扫描，需要在XML配置, 其实也可以使用注解的形式，如下所示：

```java
@Configuration
@ComponentScan(basePackages = "com.flydean.beans")
public class AppConfig {

}
```

@ComponentScan可以配置一些filters用来过滤不需要的组件。如下所示：

```java
@Configuration
@ComponentScan(basePackages = "com.flydean.beans",
        includeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = ".*Stub.*Repository"),
        excludeFilters = @ComponentScan.Filter(BeanA.class))
public class BeanAConfig {
}
```

下表是支持的filter类型和例子：

|    Filter type     |         表达式例子         |                         描述                          |
| :----------------: | :------------------------: | :---------------------------------------------------: |
| annotation（默认） | org.example.SomeAnnotation |                  type基本的目标组件                   |
|     assignable     |   org.example.SomeClass    |    目标组件可分配给（扩展或实现）的类（或接口）。     |
|      aspectj       |   org.example..*Service+   |               匹配目标组件的AspectJ类型               |
|       regex        |  org\.example\.Default.*   |             匹配目标主键内名的正则表达式              |
|       custom       |  org.example.MyTypeFilter  | org.springframework.core.type .TypeFilter的自定义实现 |

## 组件内部定义Bean元数据

Spring组件还可以为容器提供bean定义元数据。您可以使用用于在@Configuration annotated类中定义bean元数据的相同@Bean注解来实现这一点。以下示例显示了如何执行此操作：

```java
@Component
public class FactoryMethodComponent {

    @Bean
    @Qualifier("public")
    public BeanA publicInstance() {
        return new BeanA();
    }

    public void doWork() {
        // Component method implementation omitted
    }
}

```

**InjectionPoint**

从SpringFramework4.3开始，还可以声明InjectionPoint类型的工厂方法参数,来创建Bean。

注意，这只适用于bean实例的实际创建，而不适用于现有实例的注入。因此，这个特性对于原型范围的bean最有意义。

```java
@Component
public class InjectPointFactoryMethodComponent {

    @Bean
    @Scope("prototype")
    public BeanA prototypeInstance(InjectionPoint injectionPoint) {
        return new BeanA("prototypeInstance for " + injectionPoint.getMember());
    }
}
```

常规Spring组件中的@Bean方法的处理方式与Spring@Configuration类中的对应方法不同。不同的是，@Component类没有用cglib来增强以截获方法和字段的调用。cglib代理是调用@Configuration classes中通过@Bean methods内的方法或字段创建对协作对象的bean元数据引用的方法。

你可以将@Bean方法声明为静态方法，允许在不将其包含的配置类创建为实例的情况下调用它们。在定义post-processor bean（例如，BeanFactoryPostProcessor或BeanPostProcessor类型）时，这是特别有意义的，因为这样的bean在容器生命周期的早期就被初始化，应该避免在此时触发配置的其他部分。

由于技术限制，对static @Bean方法的调用永远不会被容器截获，即使是在@Configuration类（如本节前面所述）中也是如此：cglib子类只能重写非静态方法。因此，直接调用另一个@Bean方法相当于标准Java的new方法，导致从工厂方法本身直接返回一个独立的实例。

要注意： @Configuration类中的常规@Bean方法必须是可重写的，也就是说，它们不能声明为私有或最终的。

## 为自动检测组件命名

默认情况下，可以提供value属性给@Component、@Repository、@Service和@Controller，从而为Bean命名。

如果这样的注解不包含value，则默认bean名称生成器将返回小写的非限定类名。例如，如果检测到以下组件类，则名称为myMovieLister和movieFinderImpl：

```java
@Service("myMovieLister")
public class SimpleMovieLister {
    // ...
}
@Repository
public class MovieFinderImpl implements MovieFinder {
    // ...
}
```

如果您不想依赖默认的bean命名策略，可以提供一个自定义的bean命名策略。首先，实现BeanNameGenerator接口，并确保包含一个默认的无参数构造函数。然后，在配置扫描器时提供完全限定的类名，如下面的示例注解和bean定义所示：

```java
public class MyNameGenerator implements BeanNameGenerator {
    @Override
    public String generateBeanName(BeanDefinition definition, BeanDefinitionRegistry registry) {
        return null;
    }
}
@Configuration
@ComponentScan(basePackages = "com.flydean", nameGenerator = MyNameGenerator.class)
public class BeanNameConfig {
}
```

## 为自动检测的组件提供作用域

与一般的Spring管理组件一样，自动检测组件的默认和最常见的作用域是singleton。但是，有时您需要一个可以由@Scope注解指定的不同范围。可以在注解中提供作用域的名称，如下示例所示：

```java
@Scope("prototype")
@Component("beanA")
public class BeanA {

    public BeanA(){

    }

    public BeanA(String name){

    }
}
```

**自定义范围解析**

要为范围解析提供自定义策略，而不是依赖基于注解的方法，可以实现ScopeMetadataResolver接口。如下所示：

```java
public class MyScopeResolver implements ScopeMetadataResolver {
    @Override
    public ScopeMetadata resolveScopeMetadata(BeanDefinition definition) {
        return null;
    }
}
@Configuration
@ComponentScan(basePackages = "com.flydean", scopeResolver = MyScopeResolver.class)
public class BeanScopeResolverConfig {
}
```

**scoped-proxy**

当使用某些非单例作用域时，可能需要为作用域对象生成代理。为此，组件扫描元素上可以有一个scoped-proxy 属性。三个可能的值是：no、interfaces和targetClass。例如，以下配置将生成标准JDK动态代理：

```java
@Configuration
@ComponentScan(basePackages = "com.flydean", scopedProxy = ScopedProxyMode.INTERFACES)
public class ScopedProxyConfig {
}
```

## 生成候选组件的索引

虽然类路径扫描速度非常快，但是可以通过在编译时创建一个静态候选列表来提高大型应用程序的启动性能。

要生成索引，需要每个模块添加一个附加依赖项，该模块包含作为组件扫描指令目标的组件。下面的示例说明如何使用Maven：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context-indexer</artifactId>
        <version>5.1.8.RELEASE</version>
        <optional>true</optional>
    </dependency>
</dependencies>
```

这个过程生成一个包含在JAR文件中的META-INF/spring.components文件。

当在类路径上找到META-INF/Spring.components时，索引将自动启用。如果某个索引部分可用于某些库（或用例），但无法为整个应用程序生成，则可以通过将spring.index.ignore设置为true（作为系统属性或类路径根目录下的spring.properties文件）来回滚到常规类路径安排（就像根本没有索引一样）。

# Spring5:JSR 330标准注解

之前的文章我们有讲过，从Spring3.0之后，除了Spring自带的注解，我们也可以使用JSR330的标准注解。不过需要加入maven依赖如下：

```xml
    <dependencies>
    <dependency>
        <groupId>javax.inject</groupId>
        <artifactId>javax.inject</artifactId>
        <version>1</version>
    </dependency>
    </dependencies>
```

下面是标准注解和Spring注解的区别：

| Spring              | javax.inject.*        | javax.inject限制/描述                                        |
| :------------------ | :-------------------- | :----------------------------------------------------------- |
| @Autowired          | @Inject               | @Inject没有required属性，可以使用Java8的Optional代替         |
| @Component          | @Named / @ManagedBean | JSR-330没有提供组合模式，只有一种方式来标记命名组件          |
| @Scope(“singleton”) | @Singleton            | JSR-330默认范围类似Spring的prototype，但是为了和Spring的默认值保持一致，在Spring中定义的JSR-330 bean默认是singleton。如果要使用其他的作用范围，那么需要使用Spring的@Scope注解。javax.inject也提供了一个@Scope注解。但是这个注解仅用来创建你自己的注解。 |
| @Qualifier          | @Qualifier / @Named   | javax.inject.Qualifier只是一个用来构建自定义Qualifier的元注解。具体的字符串限定符（如带value的Spring的@Qualifier）可以通过javax.inject.Named关联。 |
| @Value              | –                     | 没有相同功能                                                 |
| @Required           | –                     | 没有相同功能                                                 |
| @Lazy               | –                     | 没有相同功能                                                 |
| ObjectFactory       | Provider              | javax.inject.Provider是Spring的ObjectFactory的直接替代品，它只使用了较短的get（）方法名。它还可以与Spring的@Autowired结合使用，或者与无注解的构造函数和setter方法结合使用。 |

下面我们分别来介绍。

## @Inject 和 @Named

@Inject可以用来替换@Autowired：

```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Inject
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    public void listMovies() {
        this.movieFinder.findMovies();
    }
}
```

与@Autowired一样，你可以在字段级、方法级和构造函数参数级使用@Inject。此外，可以将注入点声明为Provider，允许通过Provider.get() 调用按需访问较短作用域的bean或延迟访问其他bean。下面是Provider的例子：

```java
public class SimpleMovieProviderLister {
    private Provider<MovieFinder> movieFinder;

    @Inject
    public void setMovieFinder(Provider<MovieFinder> movieFinder) {
        this.movieFinder = movieFinder;
    }

    public void listMovies() {
        this.movieFinder.get().findMovies();
    }
}
```

可以使用@Named注解来为注入的参数限定名字：

```java
    @Inject
    public void setMovieFinderNamed(@Named("main") MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
```

与@Autowired一样，@Inject也可以与java.util.Optional或@Nullable一起使用。下面是例子：

```java
    @Inject
    public void setMovieFinder(Optional<MovieFinder> movieFinder) {
    }

    @Inject
    public void setMovieFinder(@Nullable MovieFinder movieFinder) {
    }
```

## @Named 和 @ManagedBean

除了使用@Component，你也可以使用@javax.inject.Named 或者 javax.annotation.ManagedBean，如下：

```java
@Named("movieListener")  // @ManagedBean("movieListener") could be used as well
public class SimpleMovieNamedLister {

    private MovieFinder movieFinder;

    @Inject
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```

# Spring5:SpringAOP简介

写过程序的都知道OOP即面向对象编程

从最开始的面向过程编程，到后面的面向对象编程，程序的编写方式发生了重大的变化，面向对象编程将业务逻辑封装成一个一个的对象，所有的操作都转换为对对象的操作。

面向对象编程现在使用的非常普遍，一般来说只要是高级语言都支持，但是它也有缺点，比如如果我们想做一些横跨对象的操作（如统计各个对象里面某个方法的执行时间），这时候面向对象可以能力有不逮。我们可能需要在每个对象里面都添加一些统计时间的代码，那么有没有更好的方法来处理这个问题呢？

面向方面编程（AOP）通过提供对程序结构的另一种思考方式来补充面向对象编程（OOP）。OOP中模块化的关键单元是类，而AOP中模块化的单元是方面。方面支持跨多个类型和对象的关注点（如事务管理）的模块化。（在AOP文献中，这种关注通常被称为“横切”关注。）

## AOP的概念

AOP(Aspect-Oriented Programming:⾯向切⾯编程)能够将那些与业务⽆关，却为业务模块所共同调⽤的逻辑或责任（例如事务处理、⽇志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

* **方面（Aspect）**：跨越多个类的模块化关注点。事务管理是企业Java应用程序中横切关注点的一个很好的例子。在SpringAOP中，方面是通过使用常规类（基于模式的方法）或使用@Aspect注解（@Aspectj样式）注解的常规类来实现的。

- **连接点（Join point）**：程序执行过程中的一点，如方法的执行或异常的处理。在SpringAOP中，连接点总是表示一个方法执行。

- **通知（Advice）**：一个方面在特定连接点采取的行动。不同类型的通知包括“环绕”、“前“和”后”通知。许多AOP框架（包括Spring）将通知建模为拦截器，并在连接点周围维护拦截器链。
- **切点（Pointcut）**：与连接点匹配的谓词。通知与切入点表达式关联，并在与切入点匹配的任何连接点上运行（例如，使用特定名称执行方法）。pointcut表达式匹配的连接点概念是AOP的核心，Spring默认使用AspectJ pointcut表达式语言。
- 说明（Introduction）：代表类型声明其他方法或字段。SpringAOP允许您向任何advised对象引入新的接口（和相应的实现）。例如，您可以使用一个Introduction使bean实现一个IsModified接口，以简化缓存。（introduction在AspectJ社区中称为类型间声明。）
- 目标对象（Target object）：由一个或多个方面advised的对象。也称为“advised 对象”。因为SpringAOP是通过使用运行时代理实现的，所以这个对象始终是一个代理对象。
- AOP代理：由AOP框架创建的用于实现aspect contracts（通知方法执行等）的对象。在Spring框架中，AOP代理是JDK动态代理或CGLIB代理。
- 编织（Weaving）：将aspects与其他应用程序类型或对象链接，以创建advised的对象。这可以在编译时（例如，使用AspectJ编译器）、加载时或运行时完成。Spring AOP和其他纯Java AOP框架一样，在运行时进行编织。

## Spring AOP简介

Spring AOP是纯Java实现的。不需要特殊的编译过程。

SpringAOP目前只支持方法上面的连接点，并没有实现字段连接。如果要实现这样的功能可以考虑使用AspectJ。

面向方面的集大成者是AspectJ, 它提供了面向方面编程的非常全面的功能，SpringAOP从未试图与AspectJ竞争，以提供全面的AOP解决方案。我们相信，基于代理的框架（如SpringAOP）和全面的框架（如AspectJ）都是有价值的，它们是互补的，而不是竞争中的。Spring无缝地将SpringAOP和IOC与AspectJ集成在一起。

SpringAOP的AOP方法不同于大多数其他AOP框架。目的并不是提供最完整的AOP实现。相反，其目的是在AOP实现和SpringIOC之间提供紧密的集成，以帮助解决企业应用程序中的常见问题。

**Spring AOP**就是基于动态代理的，如果要代理的对象，实现了某个接⼝，那么Spring AOP会使⽤**JDK Proxy**，去创建代理对象，⽽对于没有实现接⼝的对象，就⽆法使⽤ JDK Proxy 去进⾏代理了，这时候Spring AOP会使⽤**Cglib** ，这时候Spring AOP会使⽤ **Cglib** ⽣成⼀个被代理对象的⼦类来作为代理，

如下图所示：

<img src="/img/image/image-20220712155818498.png" alt="image-20220712155818498" style="zoom:80%;" />

当然你也可以使⽤ AspectJ ,Spring AOP 已经集成了AspectJ ，AspectJ 应该算的上是 Java ⽣态系统中最完整的 AOP 框架了

使⽤ AOP 之后我们可以把⼀些通⽤功能抽象出来，在需要⽤到的地⽅直接使⽤即可，这样⼤⼤简化了代码量。我们需要增加新功能时也⽅便，这样也提⾼了系统扩展性。⽇志功能、事务管理等等场景都⽤到了 AOP 。

## Spring AOP通知类型

- Before advice:在连接点之前运行但不能阻止执行到连接点的通知（除非它抛出异常）。
- After returning advice:在连接点正常完成后要运行的通知（例如，如果方法返回并且不引发异常）。
- After throwing advice: 如果方法通过引发异常而退出，则要执行的通知。
- After (finally) advice:无论连接点退出的方式如何（正常或异常返回），都要执行的通知。

- Around advice:环绕连接点（如方法调用）的通知。这是最有力的通知。around通知可以在方法调用前后执行自定义行为。它还负责通过返回自己的返回值或引发异常来选择是继续到连接点还是快捷地执行通知的方法。

最常使用的是Around advice,他的功能也最强大。他可以实现其他advice的功能，但是我们建议使用功能最小的通知类型，因为这样的模型更加简单，并减少你的编写程序出错几率。

# Spring5:AspectJ注解

想要在Spring中使用AOP，和通用的Spring Bean一样有两种方式，一种就是注解，一种就是XML配置，本文主要讲解如何通过注解开启Spring AOP。

@AspectJ是一种将aspects声明为用注解来注解Java类的样式。@Aspectj样式是作为Aspectj 5版本的一部分由Aspectj项目引入的。Spring使用和AspectJ相同的注解，它使用了AspectJ提供的用于切入点解析和匹配的库。但是，AOP运行时仍然是纯SpringAOP，并且不依赖于AspectJ编译器或weaver。

## 启用AOP

Spring中使用@Aspectj特性需要Spring的支持，一旦启用@AspectJ , Spring将会为目标Bean自动生成代理，从而来拦截方法调用，或者根据需要调用通知。

> 注意，如果要启用@AspectJ支持，必须在classpath包含aspectjweaver.jar。

启用@AspectJ支持很简单，只需要在@Configuration 中添加@EnableAspectJAutoProxy 即可，如下所示：

```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {
}
```

## 定义Aspect

如果想要定义一个Aspect， 只需要在Spring Bean上面加上@Aspect注解即可，如下所示：

```java
@Aspect
@Component
public class NotVeryUsefulAspect {
}
```

## 定义Pointcut

SpringAOP只支持方法层的切入点，也就是说你只能在方法上面定义Pointcut.

一个切入点声明有两部分：一个包含一个名称和任何参数的签名，一个能精确地确定我们感兴趣的执行方法的切入点表达式。在aop的@Aspectj注解样式中，通过常规方法定义提供切入点签名，并使用@Pointcut注解指示切入点表达式（作为切入点签名的方法必须具有void返回类型）。

下面的例子定义一个名为anyOldTransfer的切入点，该切入点与名为Transfer的任何方法的执行相匹配：

```java
@Aspect
@Component
public class NotVeryUsefulAspect {
    @Pointcut("execution(* transfer(..))")// the pointcut expression
    private void anyOldTransfer() {}// the pointcut signature

}
```

## 切入点指示符（PCD）

上面的例子我们看到在@Pointcut注解中可以使用execution， 表示执行方法的Pointcut，其实@Pointcut注解支持很多AspectJ切入点指示符（PCD），如下所示：

- execution：用于匹配方法执行连接点。这是使用SpringAOP时要使用的主要切入点指示符。
- within：特定类型中的连接点。
- this：bean引用（SpringAOP代理）是给定类型的实例。
- target：目标对象（要代理的应用程序对象）是给定类型的实例。
- args：参数是给定类型的实例。
- @target：执行对象的类具有给定类型的注解。
- @Args：传递的实际参数的运行时类型具有给定类型的注解。
- @within：与具有给定注解的类型中的联接点匹配。
- @Annotation：在SpringAOP中执行的方法具有给定注解的连接点。

**例子**

SpringAOP用户最经常使用执行切入点指示符。执行表达式的格式如下：

```java
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern?name-pattern(param-pattern)
            throws-pattern?)
```

除返回类型模式（前面代码段中的ret-type-pattern）、名称模式和参数模式之外的所有部分都是可选的。返回类型模式确定方法的返回类型，以便匹配连接点。*最常用作返回类型模式。它匹配任何返回类型。只有当方法返回给定类型时，完全限定的类型名才匹配。名称模式与方法名匹配。您可以使用*通配符作为名称模式的全部或部分。如果指定声明类型模式，请包含后缀.将其连接到名称模式组件。参数模式稍微复杂一点：（）匹配不带参数的方法，而（..）匹配任何数量（零个或多个）的参数。（*）模式与采用任何类型参数的方法匹配。（*，string）匹配接受两个参数的方法。第一个可以是任何类型，而第二个必须是字符串。

- 执行任何公共方法：

```java
execution(public * *(..))
```

- 执行任何以set开头的方法：

```java
execution(* set*(..))
```

- 执行任何定义在AccountService类的方法：

```java
execution(* com.xyz.service.AccountService.*(..))
```

- 执行任何定义在service包中的方法：

```java
execution(* com.xyz.service.*.*(..))
```

- 执行任何定义在service包或者他的一个子包中的方法：

```java
execution(* com.xyz.service..*.*(..))
```

- 任何在service包中的连接点（仅仅是Spring AOP中执行的方法）

```java
within(com.xyz.service.*)
```

- service包或其子包中的任何连接点（仅在SpringAOP中执行的方法）：

```java
within(com.xyz.service..*)
```

- 任何实现了AccountService接口的代理连接点（仅在SpringAOP中执行的方法）：

```java
this(com.xyz.service.AccountService)
```

> *this通常被用在绑定form中。*

- 任何实现了AccountService接口的目标对象连接点（仅在SpringAOP中执行的方法）：

```java
target(com.xyz.service.AccountService)
```

> *target更加通常被用在绑定form中。*

- 任何接收一个运行时是Serializable类型参数的连接点（仅在SpringAOP中执行的方法）：

```java
args(java.io.Serializable)
```

> *args通常被用在绑定form中。*

注意，在本例中给出的切入点与execution(* *(java.io.Serializable))不同。如果在运行时传递的参数是Serializable的，则args版本匹配；如果方法签名声明了Serializable的单个参数，则执行版本匹配。

- 任何目标对象有@Transactional 注解的连接点（仅在SpringAOP中执行的方法）：

```java
@target(org.springframework.transaction.annotation.Transactional)
```

> *@target也可以被用在绑定form中。*

- 目标对象的声明类型具有@transactional注解的任何连接点（仅在Spring AOP中执行的方法）：

```java
@within(org.springframework.transaction.annotation.Transactional)
```

> *@within也可以被用在绑定form中。*

- 执行方法具有@transactional注解的任何连接点（仅在Spring AOP中执行方法）：

```java
@Annotation(org.springframework.transaction.annotation.Transactional)
```

> *@Annotation也可以被用在绑定form中。*

- 接受单个参数的任何连接点（仅在Spring AOP中执行方法），并且传递参数的运行时类型具有@Classified annotation:

```java
@Args(com.xyz.security.Classified)
```

> *@Args也可以被用在绑定form中。*

- 名为tradeService的SpringBean上的任何连接点（仅在SpringAOP中执行方法）：

```java
bean(tradeService)
```

- SpringBean上的任何连接点（仅在SpringAOP中执行方法），其名称与通配符表达式*Service匹配：

```java
bean(*Service)
```

> *其他的AspectJ支持的pointcut指示符（call、get、set、preinitialization、staticinitialization、initialization、handler、adviceexecution、withincode、cflow、cflowbelow、if、@this和@withincode）在Spring中是不支持的，会引发IllegalArgumentException。*

除了标准的PCD之外，Spring还有一个基于特定名字的Spring Bean或一组Spring Bean（使用通配符时）PCD：

```java
bean(idOrNameOfBean)
```

其中idorNameOfBean可以是任何Spring Bean的名称，可以使用*通配符进行名称匹配。

bean PCD仅在SpringAOP中受支持，在native AspectJ中不支持。它是AspectJ定义的标准PCD的特定于Spring的扩展，因此不能用于native AspectJ中。

```java
    @Pointcut("bean(serviceA)")
    private void beanServiceA() {}
```

## 切入点组合

切入点表达式可以通过&&、|| 和！组合在一起，并可以按名称来引用，下面是例子：

```java
@Pointcut("execution(public * *(..))")
private void anyPublicOperation() {} 

@Pointcut("within(com.flydean.service.*)")
private void inTrading() {}

@Pointcut("anyPublicOperation() && inTrading()")
private void tradingOperation() {}
```

> 按名称应用Pointcut时，支持正常的java可见性规则。（可以在同一类型中看到私有切入点、在层次结构中看到protected切入点、在任何地方看到公共切入点等）。
>
> 注意，可见性并不影响Pointcut匹配。

## Advice

`Advice是与切入点相关连的`，Advice是在切入点匹配的方法上面执行before, after, 或 around Advice。

**下面是Before Advice的例子**

```java
    @Before("com.flydean.aspect.SystemArchitecture.businessService()")
    public void doAccessCheck() {
        // ...
    }


    @Before("execution(* com.flydean.service.*.*(..))")
    public void doServiceCheck() {
        // ...
    }
```

**After Returning Advice**

```java
    @AfterReturning("com.flydean.aspect.SystemArchitecture.businessService()")
    public void doBusinessCheck() {
        // ...
    }
```

有时，您需要访问通知主体中返回的实际值。您可以使用@AfterReturn的形式绑定返回值以获得该访问，如下示例所示：

```java
    @AfterReturning(
            pointcut="com.flydean.aspect.SystemArchitecture.businessService()",
            returning="retVal")
    public void doAccessCheck(Object retVal) {
        // ...
    }
```

returning属性中使用的名称必须与advice方法中参数的名称相对应。当方法执行返回时，返回值作为相应的参数值传递给通知方法。返回子句还限制只匹配那些返回指定类型值的方法执行（在本例中是Object，它匹配任何返回值）。

**After Throwing Advice**

当匹配的方法抛出异常退出时，After throwing advice执行，你可以使用@AfterThrowing来声明它：

```java
    @AfterThrowing("com.flydean.aspect.SystemArchitecture.businessService()")
    public void doRecoveryActions() {
        // ...
    }
```

如果你希望只有在抛出给定类型的异常时才运行通知，并且通常还需要访问通知正文中抛出的异常。可以使用throwing属性来限制匹配（如果需要-使用throwable作为异常类型），并将引发的异常绑定到advice参数。以下示例显示了如何执行此操作：

```java
   @AfterThrowing(
            pointcut="com.flydean.aspect.SystemArchitecture.businessService()",
            throwing="ex")
    public void doRecoveryActions(Exception ex) {
        // ...
    }
```

throwing属性中使用的名称必须与advice方法中参数的名称相对应。当通过引发异常退出方法执行时，异常作为相应的参数值传递给通知方法。

**After (Finally) Advice**

After (Finally) Advice当匹配的方法执行退出时执行，通过@After注解来定义。After advice 必须能够处理正常返回和异常返回的情况。它通常用于释放资源和类似用途。下面的示例演示如何在finally advice之后使用：

```java
    @After("com.flydean.aspect.SystemArchitecture.businessService()")
    public void doReleaseLock() {
        // ...
    }
```

**Around Advice**

around advice运行“around”匹配方法的执行。它有机会在方法执行之前和之后都进行工作，并确定何时、如何以及即使该方法真正开始执行。

使用@Around注解声明around通知。advice方法的第一个参数必须是ProceedingJoinPoint类型。在通知正文中，对ProceedingJoinPoint调用proceed（）会导致执行基础方法。proceed方法也可以传入Object[]。数组中的值在方法执行过程中用作参数。

```java
    @Around("com.flydean.aspect.SystemArchitecture.businessService()")
    public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable {
        // start stopwatch
        Object retVal = pjp.proceed();
        // stop stopwatch
        return retVal;
    }
```

## 访问JoinPoint

任何advice方法都可以将org.aspectj.lang.joinpoint类型的参数声明为其第一个参数（请注意，需要使用around advice从而声明ProceedingJoinPoint类型作为第一个参数，该参数是JoinPoint的子类）。JoinPoint接口提供了许多有用的方法：

- getArgs(): 返回方法参数
- getThis(): 返回代理对象
- getTarget(): 返回目标对象
- getSignature(): 返回被advice的方法描述
- toString():打印被advice方法的有用描述

## Advice参数

前面的例子我们看到了如何绑定返回值或异常值。如果想在通知主题中使用参数，可以使用参数绑定的形式。

在args表达式中使用参数名代替类型名，则在调用通知时，相应参数的值将作为参数值传递。

```java
    @Before("com.flydean.aspect.SystemArchitecture.businessService() && args(account,..)")
    public void validateAccount(Account account) {
        // ...
    }
```

pointcut中的args(account,..)部分有两个主要目的：首先，它将匹配限制为只执行那些方法，其中该方法至少接受一个参数，并且传递给该参数的参数是帐户的实例。其次，它通过account参数使实际的account对象可用于advice。

另一种编写方法是声明一个切入点，该切入点在与连接点匹配时“提供”account对象值，然后从通知中引用命名的切入点。如下所示：

```java
    @Pointcut("com.flydean.aspect.SystemArchitecture.businessService() && args(account,..)")
    private void accountDataAccessOperation(Account account) {}

    @Before("accountDataAccessOperation(account)")
    public void validateAccountA(Account account) {
        // ...
    }
```

## Advice参数和泛型

SpringAOP可以处理类声明和方法参数中使用的泛型。假设您有一个如下的泛型类型：

```java
public interface Sample<T> {
    void sampleGenericMethod(T param);
    void sampleGenericCollectionMethod(Collection<T> param);
}
```

通过将advice参数键入要拦截方法的参数类型，可以将方法类型的拦截限制为某些参数类型：

```java
    @Before("execution(* * ..Sample+.sampleGenericMethod(*)) && args(param)")
    public void beforeSampleMethod(MyType param) {
        // Advice implementation
    }
```

这种方法不适用于泛型集合.

## Advice Ordering

当在不同aspects定义的两条advice都需要在同一连接点上运行时，除非您另有指定，否则执行顺序是未定义的。您可以通过指定优先级来控制执行顺序。这可以通过在Aspect类中实现org.springframework.core.Ordered接口或使用order注解来以正常的Spring方式完成。给定两个aspects，从ordered.getValue（）返回较低值（或注解值）的方面具有较高的优先级。

给定两个before advice，最高优先级的advice首先运行。

给定两条after advice，最高优先级的通知将在第二运行。

## Introductions

Introductions能够为建议的对象提供指定的接口实现。

可以使用@DeclareParents注解来声明要实现的接口和默认的实现对象。

例如，给定一个名为UsageTracked的接口和一个名为DefaultUsageTracked的接口的实现，下面的方面声明com.flydean.service的所有方法也实现UsageTracked了接口：

```java
@Aspect
@Component
public class UsageTracking {

    @DeclareParents(value="com.flydean.service.*+", defaultImpl= DefaultUsageTracked.class)
    public static UsageTracked mixin;

    @Before("com.flydean.aspect.SystemArchitecture.businessService() && this(usageTracked)")
    public void recordUsage(UsageTracked usageTracked) {
        usageTracked.incrementUseCount();
    }
}
```

下面是调用的例子：

```java
        ServiceA serviceA=(ServiceA)classPathXmlApplicationContext.getBean("serviceA");
        UsageTracked usageTracked=(UsageTracked)serviceA;
        usageTracked.incrementUseCount();
```

其中serviceA是com.flydean.service包里面的方法。我们通过Introductions为其添加了一个incrementUseCount的方法。















