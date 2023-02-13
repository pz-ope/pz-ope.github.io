---
layout:     post
title:      "常用的设计模式"
subtitle:   "Common design patterns"
date:       2022-06-04 00:00:00
author:     "pz"
catalog: false
header-style: text
tags:
  - Java后端

---

# 概述

* 设计模式 是前辈们对代码开发经验的总结, 是解决特定问题的一系列套路, 他不是语法规定, 而是一套用来提高代码**可复用性**、**可维护性**、**可读性**、**稳健性**以及**安全性**的解决方案
* 1995年,GoF合作出版了《设计模式:可复用面向对象软件的基础》一书,共收录了23种设计模式,从此树立了软件设计模式领域的里程碑,人称GoF设计模式

## 继承、多态、封装

> 多态是同一个行为具有多个不同表现形式或形态的能力。

- 继承
- 父类引用指向子类
- 重写

> 封装（Encapsulation）是面向对象方法的重要原则，就是把对象的属性和操作（或服务）结合为一个独立的整体，并尽可能隐藏对象的内部实现细节。

| 修饰符    | 当前类 | 当前包 | 当前类的子类 | 其他包 |
| --------- | ------ | ------ | ------------ | ------ |
| private   | √      | ×      | ×            | ×      |
| default   | √      | √      | ×            | ×      |
| protected | √      | √      | √            | ×      |
| public    | √      | √      | √            | √      |

> 继承就是子类继承父类的特征和行为，使得子类对象（实例）具有父类的实例域和方法，或子类从父类继承方法，使得子类具有父类相同的行为。

*  局部成员变量：直接使用,本类成员变量：this.成员变量, 父类成员变量：super.父类成员变量
* **重写(override)**:子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。**即外壳不变，核心重写！**
* **重载(overload)**:在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。

## 设计模式分类

设计模式的本质是面向对象设计原则的实际运用, 是对类的**封装性, 继承性和多态性以及类的关联关系和组合关系**的充分理解

正确使用设计模式具有以下优点:

- 可以提高程序员的思维能力, 编程能力和设计能力
- 使程序设计更加标准化, 代码编制更加工程化, 使软件开发效率大大提高, 从而缩短软件的开发周期
- 使设计的代码**可重用性高, 可读性强, 可靠性高, 灵活性好, 可维护性强**

- **创建型模式:**
  - 单例模式, 工厂模式, 抽象工厂模式, 建造者模式, 原型模式
- **结构型模式:**
  - 适配器模式, 桥接模式, 装饰模式, 组合模式, 外观模式, 享元模式, 代理模式
- **行为型模式:**
  - 模板方法模式, 命令模式, 迭代器模式, 观察者模式, 中介者模式, 备忘录模式, 解释器模式, 状态模式, 策略模式, 职责链模式, 访问者模式

# OOP 七大原则

* **开闭原则**：对扩展开放，对修改关闭
  * 一个类独立之后就不应该去修改它，而是以扩展的方式适应新需求。例如一开始做了普通计算器程序，突然添加新需求，要再做一个程序员计算器，这时不应该修改普通计算器内部，应该使用面向接口编程，组合实现扩展。
* **里氏替换原则**：继承必须确保超类所拥有的性质在子类中仍然成立
  * 所有基类出现的地方都可以用派生类替换而不会程序产生错误。子类可以扩展父类的功能，但不能改变父类原有的功能。例如机动车必须有轮胎和发动机，子类宝马和奔驰不应该改写没轮胎或者没发动机。
* **依赖倒置原则**：要面向接口编程，不要面向实现编程
  * 高级模块不应该依赖低级模块，而是依赖抽象。抽象不能依赖细节，细节要依赖抽象。比如类A内有类B对象，称为类A依赖类B，但是不应该这样做，而是选择类A去依赖抽象。例如垃圾收集器不管垃圾是什么类型，要是垃圾就行。
* **单一职责原则**：控制类的粒度大小，将对象解耦，提高其内聚性
  * 一个类或者一个方法只做一件事。如果一个类承担的职责过多，就等于把这些职责耦合在一起，一个职责的变化就可能抑制或者削弱这个类完成其他职责的能力。例如餐厅服务员负责把订单给厨师去做，而不是服务员又要订单又要炒菜。
* **接口隔离原则:** 要为各个类建立它们需要的专用接口
  * 类不应该依赖不需要的接口，知道越少越好。例如电话接口只约束接电话和挂电话，不需要让依赖者知道还有通讯录。
* **迪米特法则**：只与你的直接朋友交谈，不跟"陌生人"说话
* **合成复用原则: **尽量先使用组合或者聚合等关联关系实现,其次才考虑使用继承关系来实现

# 单例模式

这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

**注意：**

- 1、单例类只能有一个实例。
- 2、单例类必须自己创建自己的唯一实例。
- 3、单例类必须给所有其他对象提供这一实例。

> 饿汉式、DCL懒汉式、深究

## 饿汉式

饿汉式:**线程安全**

基于 classloader 机制避免了多线程的同步问题，不过，instance 在类装载时就实例化，虽然导致类装载的原因有很多种，在单例模式中大多数都是调用 getInstance 方法， 但是也不能确定有其他的方式（或者其他的静态方法）导致类装载，这时候初始化 instance 显然没有达到 lazy loading 的效果。

优点：没有加锁，执行效率会提高。
缺点：类加载时就初始化，浪费内存。	

```java
// 饿汉式单例
public class Hungry{
    //可能会很费空间
    private byte[] data1 = new byte[1024*1024];
    private byte[] data2 = new byte[1024*1024];
    private byte[] data3 = new byte[1024*1024];
    private byte[] data4 = new byte[1024*1024];
    
    private final static Hungry HUNGRY = new Hungry();
    
    public static Hungry getInstance(){
        return HUNGRY;
    }
}
```

## 懒汉式

**存在多线程并发模式，后面的DCL懒汉式解决并发问题**

懒汉式，线程不安全

这种方式是最基本的实现方式，这种实现最大的问题就是不支持多线程。因为没有加锁 synchronized，所以严格意义上它并不算单例模式。
这种方式 lazy loading 很明显，不要求线程安全，在多线程不能正常工作。

```java
public class LazyMan{
    private LazyMan(){
        System.out.println(Thread.currentThread().getName()+"OK");
    }
    
    private static LazyMan lazyMan;
    
    //双重检测锁模式的  懒汉式单例    DCL懒汉式
    public static LazyMan getInstance(){
        if(lazyMan == null){
            lazyMan = new LazyMan();//不是一个原子性操作
        }
        return lazyMan;
    }
    
    /*
    * 1、分配内存空间
    * 2、执行构造方法，初始化对象
    * 3、把这个对象指向者个空间
    *
    * 123
    * 132 A
    *
    *     B //此时lazyMan还没有完成构造
    *
    * */
    
    //多线程并发
    public static void main(String[] args){
        for(int i = 0; i<10;i++){
            new Thread(()->{
                LazyMan.getInstance();
            }).start();
        }
    }
}
```

## DCL懒汉式

**注意**：synchronized 解决并发问题，但是因为`lazyMan = new LazyMan();`不是原子性操作(可以分割，见代码注释)，可能发生指令重排序的问题，通过volatil来解决。

```java
memory = allocate();     //1.分配内存
instance(memory);	 //2.初始化对象
instance = memory;	 //3.设置引用地址
其中2、3没有数据依赖关系，**可能发生重排**。如果发生，此时内存已经分配，那么`instance=memory`不为null。如果此时线程挂起，`instance(memory)`还未执行，对象还未初始化。由于`instance!=null`，所以双重检测锁模式两次判断都跳过，最后返回的`instance`没有任何内容，还没初始化，这就造成了线程安全的问题
```

* Java 语言提供了 volatile和 synchronized 两个关键字来保证线程之间操作的有序性，volatile 是因为其本身包含“禁止指令重排序”的语义，synchronized 是由“一个变量在同一个时刻只允许一条线程对其进行 lock 操作”这条规则获得的，此规则决定了持有同一个对象锁的两个同步块只能串行执行。
* 原子性就是指该操作是不可再分的。不论是多核还是单核，具有原子性的量，同一时刻只能有一个线程来对它进行操作。简而言之，在整个操作过程中不会被线程调度器中断的操作，都可认为是原子性。比如 a = 1；

```java
public class LazyMan {
    private LazyMan(){
        System.out.println(Thread.currentThread().getName()+"OK");
    }

    private volatile static LazyMan lazyMan;


    //双重检测锁模式的  懒汉式单例    DCL懒汉式
    public static LazyMan getInstance(){
        if(lazyMan==null){
            synchronized (LazyMan.class){//synchronized加锁解决多线程下的问题
                if(lazyMan == null){
                    lazyMan = new LazyMan();//不是一个原子性操作
                }
            }

        }
        return lazyMan;
    }
    /*
    * 1.分配内存空间
    * 2、执行构造方法，初始化对象
    * 3、把这个对象指向者个空间
    *
    * 123
    * 132 A
    *
    *     B //此时lazyMan还没有完成构造
    *
    * */


    //多线程并发
    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                LazyMan.getInstance();
            }).start();
        }
    }
}
```

## 静态内部类

```java
//静态内部类
public class Holder {

    //构造器私有
    private Holder(){

    }
    public static Holder getInstance(){
        return InnerClass.HOLDER;
    }

    public static class InnerClass{
        private static final Holder HOLDER = new Holder();

    }
}
```





# 工厂模式

* 作用
  * `实现了创建者和调用者的分离`
  * 详细分类
    * 简单工厂模式：用来生产同一等级结构中的任意产品(对于增加新的产品,需要覆盖已有代码)
    * 工厂方法模式：用来生产同一等级结构中的固定产品(支持增加任意产品)
    * 抽象工厂模式：围绕一个超级工厂创建其他工厂, 该超级工厂又称为其他工厂的工厂
* 遵循OOP原则
  * 开闭原则: 一个软件的实体应当对扩展开放, 对修改关闭
  * 依赖倒转原则: 要针对接口编程, 不要针对实现编程
  * 迪米特法则: 只与你直接的朋友通信, 而避免和陌生人通信
* **核心本质**
  * 实例化对象不使用new, 用工厂方法代替
  * 将选择实现类, 创建对象统一管理和控制。从而将调用者跟我们的实现类解耦

## 简单工厂模式

> Car

```java
public interface Car(){
    void name();
}
```

> Tesla

```java
public class Tesla implements Car {
    public void name() {
        System.out.println("特斯拉");
    }
}
```

> WuLing

```java
public class WuLing implements Car {
    public void name() {
        System.out.println("五菱宏光");
    }
}
```

>  CarFactory

```java
public class CarFactory {
    //静态工厂模式
    //增加一个新的产品如果不修改代码做不到

    //方法一
    public static Car getCar(String car){
        if (car.equals("五菱宏光")){
            return new WuLing();
        }else if(car.equals("特斯拉")){
            return new Tesla();
        }else return null;
    }

    //方法二
    public static Car getWuLing(){
        return new WuLing();
    }

    public static Car getTesla(){
        return new Tesla();
    }
}
```

> Consumer

```java
public class Consumer {

    public static void main(String[] args) {
        //接口，所有实现类
        Car wuLing = new WuLing();
        Car tesla = new Tesla();
        wuLing.name();
        tesla.name();

        wuLing=CarFactory.getCar("五菱宏光");
        tesla=CarFactory.getCar("特斯拉");
        wuLing.name();
        tesla.name();


    }
}
```

<img src="/img/image/image-20220709141409510.png" alt="image-20220709141409510" style="zoom: 67%;" />

## 工厂方法模式

> Car

```java
public interface Car {
    void name();
}
```

> CarFactory

```java
public interface CarFactory {
    //工厂方法模式
    Car getCar();
}
```

> Tesla

```java
public class Tesla implements Car {
    public void name() {
        System.out.println("特斯拉");
    }
}
```

> TeslaFactory

```java
public class TeslaFactory implements CarFactory {
    public Car getCar() {
        return new Tesla();
    }
}
```

> WuLing

```java
public class WuLing implements Car {
    public void name() {
        System.out.println("五菱宏光");
    }
}
```

> WuLingFactory

```java
public class WuLingFactory implements CarFactory {
    public Car getCar() {
        return new WuLing();
    }
}
```

> Consumer

```java
public class Consumer {

    public static void main(String[] args) {
        //接口，所有实现类
        Car wuLing = new WuLingFactory().getCar();
        Car tesla = new TeslaFactory().getCar();
        wuLing.name();
        tesla.name();
    }
//结构复杂度: simple
//代码复杂度: simple
//编程复杂度: simple
//管理上的复杂度: simple
//舰据设计原则:工厂方法模式!
//根据实际业务:简单工厂模式!

}
```

<img src="/img/image/image-20220709142205940.png" alt="image-20220709142205940" style="zoom:67%;" />

## 抽象工厂模式

- 定义︰抽象工厂模式提供了一个创建一系列相关或者相互依赖对象的接口，无需指定它们具体的类

- 适用场景:
  - 客户端（应用层)不依赖于产品类实例如何被创建、实现等细节
  - 强调一系列相关的产品对象（属于同一产品族）一起使用创建对象需要大量的重复代码
  - 提供一个产品类的库，所有的产品以同样的接口出现，从而使得客户端不依赖于具体的实现
- 优点:
  - 具体产品在应用层的代码隔离，无需关心创建的细节
  - 将一个系列的产品统一到一起创建
- 缺点:
  - 规定了所有可能被创建的产品集合，产品簇中扩展新的产品困难;
  - 增加了系统的抽象性和理解难度

<img src="/img/image/image-20220709142344088.png" alt="image-20220709142344088" style="zoom:80%;" />

<img src="/img/image/image-20220709143012383.png" alt="image-20220709143012383" style="zoom:80%;" />

**代码测试**

> IPhoneProduct

```java
//手机产品接口
public interface IPhoneProduct {
    void start();
    void shutdown();
    void call();
    void sendMsg();
}
```

> IRouterProduct

```java
//路由器产品接口
public interface IRouterProduct {
    void start();
    void shutdown();
    void openWifi();
    void setting();
}
```

> IProductFactory

```java
//抽象产品工厂
public interface IProductFactory {
    //生产手机
    IPhoneProduct iPhoneProduct();
    //生产路由器
    IRouterProduct iRouterProduct();
}
```

> XiaomiPhone

```java
public class XiaomiPhone implements IPhoneProduct {
    @Override
    public void start() {
        System.out.println("小米开机");
    }

    @Override
    public void shutdown() {
        System.out.println("小米关机");
    }

    @Override
    public void call() {
        System.out.println("小米打电话");
    }

    @Override
    public void sendMsg() {
        System.out.println("小米发信息");
    }
}
```

> XiaomiRouter

```java
public class XiaomiRouter implements IRouterProduct {
    @Override
    public void start() {
        System.out.println("小米开启路由器");
    }

    @Override
    public void shutdown() {
        System.out.println("小米关闭路由器");
    }

    @Override
    public void openWifi() {
        System.out.println("小米开启Wifi");
    }

    @Override
    public void setting() {
        System.out.println("小米设置路由器");
    }
}
```

> XiaomiFactory

```java
public class XiaomiFactory implements IProductFactory {

    @Override
    public IPhoneProduct iPhoneProduct() {
        return new XiaomiPhone();
    }

    @Override
    public IRouterProduct iRouterProduct() {
        return new XiaomiRouter();
    }
}
```

> Client

```java
public class Client {
    public static void main(String[] args) {
        System.out.println("=================小米系列产品==============");
        XiaomiFactory xiaomiFactory = new XiaomiFactory();
        IPhoneProduct iPhoneProduct = xiaomiFactory.iPhoneProduct();
        iPhoneProduct.start();

        IRouterProduct iRouterProduct = xiaomiFactory.iRouterProduct();
        iRouterProduct.start();
    }
}
```

# 建造者模式

- 建造者模式也属于创建型模式，它提供了一种创建对象的最佳方式。
- 定义︰将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示
- 主要作用:在用户不知道对象的建造过程和细节的情况下就可以直接创建复杂的对象。
- 用户只需要给出指定复杂对象的类型和内容，建造者模式负责按顺序创建复杂对象（把内部的建造过程和细节隐藏起来)
- 例子:
  - 工厂(建造者模式)︰负责制造汽车（组装过>程和细节在工厂内)
  - 汽车购买者（用户)∶你只需要说出你需要的>型号(对象的类型和内容)，然后直接购买就可以使用了(不需要知道汽车是怎么组装的（车轮、车门、>发动机、方向盘等等)

<img src="/img/image/image-20220710101125194.png" alt="image-20220710101125194" style="zoom:80%;" />

- 既然是建造者模式，那么我们还是继续造房吧，其实我也想不到更简单的例子。假设造房简化为如下步骤:(1）地基(2）钢筋工程(3）铺电线（4）粉刷;“如果”要盖一座房子，首先要找一个建筑公司或工程承包商(指挥者)。承包商指挥工人(具体建造者）过来造房子(产品)，最后验收。

## 代码实现

> Builder

```java
//抽象的建造者：方法(可以看作建房子的图纸)
public abstract class Builder {

    abstract void builderA();//地基
    abstract void builderB();//钢筋工程
    abstract void builderC();//铺电线
    abstract void builderD();//粉刷

    //完工：得到产品
    abstract Product getProduct();
}
```

> Product

```java
//产品，房子
public class Product {
    private String builderA;
    private String builderB;
    private String builderC;
    private String builderD;

    public String getBuilderA() {
        return builderA;
    }

    public void setBuilderA(String builderA) {
        this.builderA = builderA;
    }

    public String getBuilderB() {
        return builderB;
    }

    public void setBuilderB(String builderB) {
        this.builderB = builderB;
    }

    public String getBuilderC() {
        return builderC;
    }

    public void setBuilderC(String builderC) {
        this.builderC = builderC;
    }

    public String getBuilderD() {
        return builderD;
    }

    public void setBuilderD(String builderD) {
        this.builderD = builderD;
    }

    @Override
    public String toString() {
        return "Product{" +
                "builderA='" + builderA + '\'' +
                ", builderB='" + builderB + '\'' +
                ", builderC='" + builderC + '\'' +
                ", builderD='" + builderD + '\'' +
                '}';
    }
}
```

> Worker

```java
//具体的建造者：工人
public class Worker extends Builder {

    private Product product;

    public Worker() {
        product = new Product();
    }

    @Override
    void builderA() {
        product.setBuilderA("地基");
        System.out.println("地基");
    }

    @Override
    void builderB() {
        product.setBuilderB("钢筋工程");
        System.out.println("钢筋工程");
    }

    @Override
    void builderC() {
        product.setBuilderC("铺电线");
        System.out.println("铺电线");
    }

    @Override
    void builderD() {
        product.setBuilderD("粉刷");
        System.out.println("粉刷");
    }

    @Override
    Product getProduct() {
        return product;
    }
}
```

> Director

```java
//指挥：核心，负责指挥构建一个工程，工程如何构建，由它决定
public class Director {

    //指挥工人按照顺序建房子
    public Product build(Builder builder){
        builder.builderA();
        builder.builderB();
        builder.builderC();
        builder.builderD();
        return builder.getProduct();
    }
}
```

> Test

```java
public class Test {
    public static void main(String[] args) {
        //指挥
        Director director = new Director();
        //指挥具体的工人完成产品
        Product build = director.build(new Worker());
        System.out.println(build.toString());
    }
}
```

## 建造者模式的意图和适用场景（拓展）
* 模式意图

将一个复杂的构件与其表示相分离,使得同样的构件过程可以创建不同的表示

* 使用场景
  * 需要生成的产品对象有复杂的内部结构
  * 当一些基本部件不会变, 而其组合经常变化的时候
  * 需要生成的对象内部属性本身相互依赖

## 建造者的参与者(拓展)
**Builder**

抽象建造者,给出一个抽象接口, 以规范产品对象的各个组成成分的建造. 这个接口规定要实现复杂对象的哪些部分的创建,并不涉及具体的对象部件的创建

**ConcreteBuilder**

实现Builder接口, 针对不同的商业逻辑, 具体化复杂对象的各部分的创建, 即具体建造者

**Director**

指挥者, 调用具体建造者来创建复杂对象的各个部分, 在指导者不涉及具体产品的信息, 只负责保证对象各部分完整创建或者按某种顺序创建

**Product**

要创建的复杂对象, 即产品角色

## 总结 (拓展)

* 建造者模式的使用使得产品的内部表象可独立的变化. 使用建造者模式可以使客户端不必知道产品内部组成的细节
* 每一个Builder都相对独立, 而与其它的Builder无关
* 可使得对构造过程更加精细控制
* 将构建代码和表示代码分开
* 建造者模式的缺点在于难于应付"分步骤构建算法"的需求变动

> * 上面示例是Builder模式的常规用法，导演类Director在Builder模式中具有很重要的作用，它用于指导具体构建者如何构建产品，控制调用先后次序,并向调用者返回完整的产品类,但是有些情况下需要简化系统结构，可以把Director和抽象建造者进行结合。
> * 通过静态内部类方式实现零件无序装配构造,这种方式使用更加灵活，更符合定义。内部有复杂对象的默认实现，使用时可以根据用户需求自由定义更改内容,并且无需改变具体的构造方式。就可以生产出不同复杂产品
> * 比如:比如麦当劳的套餐,服务员(具体建造者)可以随意搭配任意几种产品(零件)组成-款套餐(产品) ,然后出售给客户。比第一种方式少了指挥者,主要是因为第二种方式把指挥者交给用户来操作，使得产品的创建更加简单灵活。

## 代码再实现

> Product

```java
//产品：套餐
public class Product {

    private String BuildA ="汉堡";
    private String BuildB ="可乐";
    private String BuildC ="薯条";
    private String BuildD ="甜点";

    public String getBuildA() {
        return BuildA;
    }

    public void setBuildA(String buildA) {
        BuildA = buildA;
    }

    public String getBuildB() {
        return BuildB;
    }

    public void setBuildB(String buildB) {
        BuildB = buildB;
    }

    public String getBuildC() {
        return BuildC;
    }

    public void setBuildC(String buildC) {
        BuildC = buildC;
    }

    public String getBuildD() {
        return BuildD;
    }

    public void setBuildD(String buildD) {
        BuildD = buildD;
    }

    @Override
    public String toString() {
        return "Product{" +
                "BuildA='" + BuildA + '\'' +
                ", BuildB='" + BuildB + '\'' +
                ", BuildC='" + BuildC + '\'' +
                ", BuildD='" + BuildD + '\'' +
                '}';
    }
}
```

> Builder

```java
//建造者
public abstract class Builder {
    abstract Builder BuildA(String msg);//"汉堡";
    abstract Builder BuildB(String msg);//"可乐";
    abstract Builder BuildC(String msg);//"薯条";
    abstract Builder BuildD(String msg);//"甜点";

    abstract Product getProduct();
}
```

> Worker

```java
public class Worker extends Builder {

    private Product product;

    public Worker() {
        product = new Product();
    }

    @Override
    Builder BuildA(String msg) {
        product.setBuildA(msg);
        return this;
    }

    @Override
    Builder BuildB(String msg) {
        product.setBuildB(msg);
        return this;
    }

    @Override
    Builder BuildC(String msg) {
        product.setBuildC(msg);
        return this;
    }

    @Override
    Builder BuildD(String msg) {
        product.setBuildD(msg);
        return this;
    }

    @Override
    Product getProduct() {
        return product;
    }
}
```

> Test

```java
public class Test {
    public static void main(String[] args) {
        //服务员
        Worker worker = new Worker();
        //可以按默认走，也可以自由组合
        Product product = worker.BuildA("全家桶").BuildB("雪碧").getProduct();

        System.out.println(product.toString());
    }
}
```

## 优缺点

* 优点:
  * 产品的建造和表示分离, 实现了解耦, 使用建造者模式可以使客户端不必知道产品内部组成的细节.
  * 将复杂产品的创建步骤分解在不同的方法中, 使得创建过程更加清晰
  * 具体的建造者类之间是相互独立的, 这有利于系统的扩展. 增加新的具体建造者无需修改原有的类库的代码, 符合"开闭原则"
* 缺点:
  * 建造者模式所创建的产品一般具有较多的共同点,其组成部分相似; 如果产品之间的差异性很大, 则不适合使用建造者模式, 因此其使用范围受到一定的限制
  * 如果产品的内部变化复杂,可能会导致需要定义很多具体建造者类实现这种变化, 导致系统变得很庞大

## 应用场景以及与抽象工厂模式对比

* 应用场景: .
  * 需要生成的产品对象有复杂的内部结构，这些产品对象具备共性;
  * 隔离复杂对象的创建和使用，并使得相同的创建过程可以创建不同的产品。
  * 适合于一个具有较多的零件(属性)的产品(对象)的创建过程。

* 建造者与抽象工厂模式的比较:
  * 与抽象工厂模式相比，建造者模式返回一个组装好的完整产品，而抽象工厂模式返回一系列相关的产
    品，这些产品位于不同的产品等级结构,构成了一个产品族。
  * 在抽象工厂模式中，客户端实例化工厂类,然后调用工厂方法获取所需产品对象,而在建造者模式中，
    客户端可以不直接调用建造者的相关方法，而是通过指挥者类来指导如何生成对象，包括对象的组装
    过程和建造步骤，它侧重于一步步构造一个复杂对象, 返回-个完整的对象。
  *  如果将抽象工厂模式看成汽车配件生产工厂，生产一个产品族的产品，那么建造者模式就是一个汽车
    组装工厂,通过对部件的组装可以返回一完整的汽车!

# 原型模式

* 克隆
  * Prototype
  * Cloneable接口
  * clone()方法

## 代码实现

> Video

```java
/*
1..实现一个接口Cloneable
2.重写一个方法clone()
*/
//Video
public class Video implements Cloneable { //无良up 主，克隆别人的视频! .
    private String name;
    private Date createTime;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }

    public Video() {

    }

    public Video(String name, Date createTime) {
        this.name = name;
        this.createTime = createTime;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    @Override
    public String toString() {
        return "Video{" +
                "name='" + name + '\'' +
                ", createTime=" + createTime +
                '}';
    }
}
```

> Bilibili

```java
/*
客户端:克隆
*/
public class Bilibili {
    public static void main(String[] args) throws CloneNotSupportedException {
        //原型对象v1
        Date date = new Date();
        Video v1 = new Video("狂神说Java", date);
        //v1克隆v2
        //Video v2 = new Video( "狂神说Java", date);
        Video v2 = (Video) v1.clone();
        System.out.println("v1=>" + v1);
        System.out.println("v2=>" + v2);

        date.setTime(22131231);

        System.out.println("++++++++++++++++++++++++++++++++++++++");

        System.out.println("v1=>" + v1);
        System.out.println("v2=>" + v2);
    }
}

```

> 运行结果

```java
v1=>Video{name='狂神说Java', createTime=Thu Dec 10 23:52:48 CST 2020}
v2=>Video{name='狂神说Java', createTime=Thu Dec 10 23:52:48 CST 2020}

++++++++++++++++++++++++++++++++++++++

v1=>Video{name='狂神说Java', createTime=Thu Jan 01 14:08:51 CST 1970}
v2=>Video{name='狂神说Java', createTime=Thu Jan 01 14:08:51 CST 1970}
```

## 原理理解

<img src="/img/image/image-20220710110450312.png" alt="image-20220710110450312" style="zoom:80%;" />

<img src="/img/image/image-20220710110505111.png" alt="image-20220710110505111" style="zoom:80%;" />

## 代码再实现

> Video

```java
/*
1..实现一个接口Cloneable
2.重写一个方法clone()
*/
//Video
public class Video implements Cloneable { //无良up 主，克隆别人的视频! .
    private String name;
    private Date createTime;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Object obj = super.clone();
        //实现深克隆~序列化， 反序列化
        Video v = (Video) obj;
        v.createTime = (Date) this.createTime.clone();//将这个对象的属性也进行克隆~
        return v;
    }

    public Video() {

    }

    public Video(String name, Date createTime) {
        this.name = name;
        this.createTime = createTime;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Date getCreateTime() {
        return createTime;
    }

    public void setCreateTime(Date createTime) {
        this.createTime = createTime;
    }

    @Override
    public String toString() {
        return "Video{" +
                "name='" + name + '\'' +
                ", createTime=" + createTime +
                '}';
    }
}
```

> Bilibili

```java
/*
客户端:克隆
*/
public class Bilibili {
    public static void main(String[] alrgs) throws CloneNotSupportedException {
        //原型对象v1
        Date date = new Date();
        Video v1 = new Video("狂神说Java", date);
        //v1克隆v2
        //Video v2 = new Video( "狂神说Java", date);
        Video v2 = (Video) v1.clone();
        System.out.println("v1=>" + v1);
        System.out.println("v2=>" + v2);
        date.setTime(22131231);
        System.out.println("+++++++++++++++++++++++++++++++++++");
        System.out.println("v1=>" + v1);
        System.out.println("v2=>" + v2);
    }
}
```

> 运行结果

```java
v1=>Video{name='狂神说Java', createTime=Thu Dec 10 23:56:38 CST 2020}
v2=>Video{name='狂神说Java', createTime=Thu Dec 10 23:56:38 CST 2020}
+++++++++++++++++++++++++++++++++++
v1=>Video{name='狂神说Java', createTime=Thu Jan 01 14:08:51 CST 1970}
v2=>Video{name='狂神说Java', createTime=Thu Dec 10 23:56:38 CST 2020}
```

# 适配器模式

**结构型模式:**

- 作用：从程序的结构上实现松耦合, 从而可以扩大整体的类结构,用来解决更大的问题

适配器模式就像**USB网线转换器**

## 代码实现

> Adaptee

```java
//要被适配的类:网线
public class Adaptee {
    public void request() {
        System.out.println("连接网线上网");
    }
}
```

> Adapter

```java
// 继承的方式（类适配器，单继承）
//真正的适配器，需要连接USB,连接网线~
public class Adapter extends Adaptee implements NetToUsb {
    @Override
    public void handleRequest() {
        //上网的具体实现，找一个转接头
        super.request();
    }
}
```

> NetToUsb

```java
//接口转换器的抽象实现~
public interface NetToUsb {
//作用:处理请求，网线=>usb
public void handleRequest();
}

```

>Adapter2

```java
// 组合(对象适配器:常用)
//真正的适配器，需要连接USB,连接网线~
public class Adapter2 implements NetToUsb {
	//组合模式
    private Adaptee adaptee;

    public Adapter2(Adaptee adaptee) {
        this.adaptee = adaptee;
    }
    @Override
    public void handleRequest() {
        //上网的具体实现，找一个转接头
        adaptee.request();//可以上网
    }
}
```

> Computer

```java
//客户端关: 想上:网，插不L:网线~
public class Computer {
    //我们的电脑需要连接:转换器才可以上网
    public void net(NetToUsb adapter) {
        //上网的具体实现， 找个转接器
        adapter.handleRequest();
    }

    public static void main(String[] args) {
/*        //电脑，适配器，网线~
        Computer computer = new Computer(); //电脑
        Adaptee adaptee = new Adaptee(); //网线
        Adapter adapter = new Adapter(); //转接器
        computer.net(adapter);*/

        System.out.println("++++++++++++++++++++++++++++++");

        //电脑，适配器，网线~
        Computer computer = new Computer(); //电脑
        Adaptee adaptee = new Adaptee(); //网线
        Adapter2 adapter = new Adapter2(adaptee); //转换器

        computer.net(adapter);
    }
}
```

## 角色分析

> 将一个类的接口转换成客户希望的另外一个接口. Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以在一起工作

- 目标接口: 客户所期待的接口, 目标可以是具体的或抽象的类, 可以是接口(例：USB)
- 需要适配的类: 需要适配的类或适配者类（例：网线）
- 适配器: 通过包装一个需要适配的对象, 把原接口转换成目标对象（例：USB转换器）

<img src="/img/image/image-20220710141636116.png" alt="image-20220710141636116" style="zoom:80%;" />

* **对象适配器优点**
  * 一个对象适配器可以把多个不同的适配者适配到同一个目标
  * 可以适配一个适配者的子类,由于适配器和适配者之间是关联关系,根据"里氏代换原则", 适配者的子类也可通过该适配器进行适配
* **类适配器缺点**
  * 对于Java, C#等不支持多重类继承的语言, 一次最多只能适配一个适配者类,不能同时适配多个适配者,
  * 在Java, C#等语言中, 类适配器模式中的目标抽象类只能为接口, 不能为类, 其使用有一定的局限性
* **适用场景**
  * 系统需要使用一些现有的类, 而这些类的接口(如方法名)不符合系统的需求,甚至没有这些类的源代码
  * 想创建一个可以重复使用的类, 用于与一些彼此之间没有太大关联的一些类,包括一些可能在将来引进的类一起工作

# 桥接模式

桥接模式是将抽象部分与它的实现部分分离, 使他们都可以独立地变化, 它是一种对象结构型模式, 又称为柄体(Handle and Body)模式或接口(Interface)模式

<img src="/img/image/image-20220710142434580.png" alt="image-20220710142434580" style="zoom:60%;" />

**分析:**

* 这个场景中有两个变化的维度: 品牌, 类型 

<img src="/img/image/image-20220710142631801.png" alt="image-20220710142631801" style="zoom:80%;" />

## 代码实现

> Brand

```java
//品牌
public interface Brand {
    void info();
}

```

> Lenovo

```java
//联想品牌
public class Lenovo implements Brand {
    @Override
    public void info() {
        System.out.print("联想");
    }
}
```

> Apple

```java
//苹果品牌
public class Apple implements Brand {
    @Override
    public void info() {
        System.out.print("苹果");
    }
}
```

> Computer

```java
//抽象的电脑类型类
public abstract class Computer {
    //组合，品牌~(看作是‘桥’)
    protected Brand brand;

    public Computer(Brand brand) {
        this.brand = brand;
    }

    public void info() {
        brand.info();//自带品牌
    }
}

class Desktop extends Computer {
    public Desktop(Brand brand) {
        super(brand);
    }

    @Override
    public void info() {
        super.info();
        System.out.print("台式机");
    }
}

class Laptop extends Computer {
    public Laptop(Brand brand) {
        super(brand);
    }

    @Override
    public void info() {
        super.info();
        System.out.print("笔记本");
    }
}
```

> Test

```java
public class Test {
    public static void main(String[] args) {
        //苹果管记本
        Computer computer = new Laptop(new Apple());
        computer.info();

        System.out.println();
        //联想台式机
        Computer computer2 = new Desktop(new Lenovo());
        computer2.info();
    }
}
```

<img src="/img/image/image-20220710143437282.png" alt="image-20220710143437282" style="zoom:80%;" />

## 优劣势分析

* **好处分析:**
  * 桥接模式偶尔类似于多继承方案，但是多继承方案违背了类的单一职责原则，复用性比较差，类的个数也非常多，桥接模式是比多继承方案更好的解决方法。极大的减少了子类的个数，从而降低管理和维护的成本
  * 桥接模式提高了系统的可扩充性，在两个变化维度中任意扩展一个维度，都不需要修改原有系统。符合开闭原则，就像一座桥，可以把两个变化的维度连接起来!
* **劣势分析:**
  - 桥接模式的引入会增加系统的理解与设计难度，由于聚合关联关系建立在抽象层，要求开发者针对抽象进行设计与编程。
  - 桥接模式要求正确识别出系统中两个独立变化的维度，因此其使用范围具有一定的局限性.
* **最佳实践:**
  * 如果一个系统需要在构建的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的继承联系，通过桥接模式可以使它们在抽象层建立一个关联关系。抽象化角色和实现化角色可以以继承的方式独立扩展而互不影响，在程序运行时可以动态将一个抽象化子类的对象和一个实现化子类的对象进行组合，即系统需要对抽象化角色和实现化角色进行动态耦合。
  * 一个类存在两个独立变化的维度，且这两个维度都需要进行扩展。
  * 虽然在系统中使用继承是没有问题的，但是由于抽象化角色和具体化角色需要独立变化，设计要求需要独立管理这两者。对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用。
* 场景:
  - Java语言通过Java虚拟机实现了平台的无关性。
  - AWT中的Peer架构
  - JDBC驱动程序也是桥接模式的应用之一。

例如：

<img src="/img/image/image-20220710144248066.png" alt="image-20220710144248066" style="zoom:80%;" />

# 代理模式

SpringAOP的底层 [SpringAOP和SpringMVC]

代理模式的分类

- 静态代理
- 动态代理

<img src="/img/image/image-20220710154613995.png" alt="image-20220710154613995" style="zoom:80%;" />

## 静态代理

角色分析:

- 抽象角色: 一般会使用接口或抽象类来解决（需要做的事情：租房）
- 真实角色: 被代理的角色（房东）
- 代理角色: 代理真实角色,代理真实角色后,我们一般会做一些附属操作（中介）
- 客户: 访问代理对象的人（你）

代码步骤:

1. 接口

```java
public interface Rent {
    public void rent();
}
```

2. 真实角色

```java
//房东
public class Host implements Rent{

    @Override
    public void rent() {
        System.out.println("房东要出租房子");
    }
}
```

3. 代理角色

```java
//代理
public class Proxy implements Rent{

    private Host host;

    public Proxy(){

    }

    public Proxy(Host host) {
        this.host = host;
    }

    @Override
    public void rent() {
        seeHouse();
        host.rent();
        hetong();
        fare();
    }

    //看房
    public void seeHouse(){
        System.out.println("中介带你看房");
    }

    //收中介费
    public void fare(){
        System.out.println("收中介费");
    }

    //签合同
    public void hetong(){
        System.out.println("签合同");
    }
}
```

4. 客户端访问代理角色

```java
public static void main(String[] args) {
        //房东要租房子
        Host host = new Host();

        //代理,中介帮房东租房子,但是代理一般会有一些附属操作
        Proxy proxy = new Proxy(host);

        //不用面对房东,直接找中介租房即可
        proxy.rent();
    }
```

代理模式的好处:

- 可以使真实角色的操作更加纯粹,不用去关注一些公共的业务
- 公共也就交给代理角色,实现了业务的分工
- 公共业务发生扩展的时候,方便集中管理

缺点:

- 一个真实角色就会产生一个代理角色,代码量会翻倍,开发效率会变低(使用动态代理解决)

## 加深理解

我们在不改变原来的代码的情况下，实现了对原有功能的增强，这是AOP中最核心的思想

聊聊AOP：纵向开发，横向开发。

<img src="/img/image/image-20220710160813373.png" alt="image-20220710160813373" style="zoom:80%;" />

* 创建一个抽象角色

```java
//抽象角色：增删改查业务
public interface UserService {
   void add();
   void delete();
   void update();
   void query();
}
```

* 一个真实对象来完成这些增删改查操作

```java
//真实对象，完成增删改查操作的人
public class UserServiceImpl implements UserService {

   public void add() {
       System.out.println("增加了一个用户");
  }

   public void delete() {
       System.out.println("删除了一个用户");
  }

   public void update() {
       System.out.println("更新了一个用户");
  }

   public void query() {
       System.out.println("查询了一个用户");
  }
}
```

* 需求来了，现在我们需要增加一个日志功能，怎么实现！
  * 思路1 ：在实现类的每个方法上增加代码 【麻烦！】
  * 思路2：使用代理来做，能够不改变原来的业务情况下，实现此功能就是最好的了！

* 设置一个代理类来处理日志, 代理角色

```java
//代理角色，在这里面增加日志的实现
public class UserServiceProxy implements UserService {
   private UserServiceImpl userService;

   public void setUserService(UserServiceImpl userService) {
       this.userService = userService;
  }

   public void add() {
       log("add");
       userService.add();
  }

   public void delete() {
       log("delete");
       userService.delete();
  }

   public void update() {
       log("update");
       userService.update();
  }

   public void query() {
       log("query");
       userService.query();
  }

   public void log(String msg){
       System.out.println("执行了"+msg+"方法");
  }

}
```

* 测试访问类：

```java
public class Client {
   public static void main(String[] args) {
       //真实业务
       UserServiceImpl userService = new UserServiceImpl();
       //代理类
       UserServiceProxy proxy = new UserServiceProxy();
       //使用代理类实现日志功能！
       proxy.setUserService(userService);

       proxy.add();
  }
}
```

## 动态代理

* 动态代理和静态代理角色一样
* 动态代理的代理类是动态生成的,不是我们直接写好的
* 动态代理分为两大类 : 基于接口的动态代理,基于类的动态代理
  * 基于接口-JDK动态代理
  * 基于类: cglib
  * java字节码实现 : javassist

需要了解的两个类: `Proxy:` 代理,` InvocationHandler: `调用处理程序

### InvocationHandler

接口：**由代理实例的调用处理程序实现的接口**

* 每个代理实例都有一个关联的调用处理程序（即代理类中的行为由InvocationHandler自动生成）。
* 当在代理实例上调用方法时，方法调用将被编码并分派到其调用处理程序的invoke方法（通过invoke的反射机制执行调用代理角色的对应方法）。

<img src="/img/image/image-20220710174152358.png" alt="image-20220710174152358"  />

### Proxy

是一个类在反射包`java.lang.reflect`下的

==Proxy提供了创建动态代理类和实例的静态方法。==

![image-20220710174810461](/img/image/image-20220710174810461.png)

![image-20220710175136322](/img/image/image-20220710175136322.png)

### 代码实现

* Rent . java 即抽象角色

```java
//抽象角色：租房
public interface Rent {
   public void rent();
}
```

* Host . java 即真实角色

```java
//真实角色: 房东，房东要出租房子
public class Host implements Rent{
   public void rent() {
       System.out.println("房屋出租");
  }
}
```

* ProxyInvocationHandler.（代理实例的调用处理程序）

```java
public class ProxyInvocationHandler implements InvocationHandler {
    // 被代理的接口
   private Rent rent;

   public void setRent(Rent rent) {
       this.rent = rent;
  }

   //生成代理类，重点是第二个参数，获取要代理的抽象角色！之前都是一个角色，现在可以代理一类角色
   public Object getProxy(){
       return Proxy.newProxyInstance(this.getClass().getClassLoader(),
               rent.getClass().getInterfaces(),this);
  }

   // proxy : 代理类 method : 代理类的调用处理程序的方法对象.
   // 处理代理实例上的方法调用并返回结果
   @Override
   public Object invoke(Object proxy, Method method, Object[] args) throwsThrowable {
       seeHouse();
       //核心：本质利用反射实现！
       Object result = method.invoke(rent, args);
       fare();
       return result;
  }

   //看房
   public void seeHouse(){
       System.out.println("带房客看房");
  }
   //收中介费
   public void fare(){
       System.out.println("收中介费");
  }

}
```

* Client . java

```java
//租客
public class Client {

   public static void main(String[] args) {
       //真实角色
       Host host = new Host();
       //代理实例的调用处理程序
       ProxyInvocationHandler pih = new ProxyInvocationHandler();
       pih.setRent(host); //将真实角色放置进去！
       Rent proxy = (Rent)pih.getProxy(); //动态生成对应的代理类！
       proxy.rent();
  }
}
```

核心：**一个动态代理类代理的是一个接口 , 一般就是对应的一类业务；一个动态代理可以代理多个类，只要是实现了同一个接口即可**

## 动态代理在理解

我们来使用动态代理实现代理我们后面写的UserService！

我们也可以编写一个通用的动态代理实现的类！所有的代理对象设置为Object即可！

> ProxyInvocationHandler

```java
public class ProxyInvocationHandler implements InvocationHandler {
   //被代理的接口
   private Object target;

   public void setTarget(Object target) {
       this.target = target;
  }

   // 生成得到代理对象
   public Object getProxy(){
       return Proxy.newProxyInstance(this.getClass().getClassLoader(),
               target.getClass().getInterfaces(),this);
  }

   // proxy : 代理类
   // method : 代理类的调用处理程序的方法对象.
   public Object invoke(Object proxy, Method method, Object[] args) throwsThrowable {
       log(method.getName()); // 添加日志功能
       //动态代理的本质,就是使用反射机制实现
       Object result = method.invoke(target, args);
       return result;
  }

   public void log(String methodName){
       System.out.println("执行了"+methodName+"方法");
  }

}
```

> Client 

```java
public class Client {
    public static void main(String[] args) {
        //真实角色
        UserServiceImpl userService = new UserServiceImpl();
        //代理角色,不存在
        ProxyInvocationHandler pih = new ProxyInvocationHandler();

        pih.setTarget(userService); //设置要代理的对象
        //动态生成代理类
        UserService proxy = (UserService) pih.getProxy();

        proxy.add();
    }
}
```

**动态代理的好处**

静态代理有的它都有，静态代理没有的，它也有！

- 可以使得我们的真实角色更加纯粹 . 不再去关注一些公共的事情 .
- 公共的业务由代理来完成 . 实现了业务的分工 ,
- 公共业务发生扩展时变得更加集中和方便 .
- 一个动态代理 , 一般代理某一类业务
- 一个动态代理可以代理多个类，代理的是接口！















