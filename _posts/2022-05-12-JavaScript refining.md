---
layout: post
title: "「前端」JavaScript精炼"
subtitle: 'Front end JavaScript refining'
author: "pz"
header-style: text
tags:
  - 前端


---

# 数据类型

```js
Number   Boolean   undefined     Object   Function    String  Null

基本类型：Number Boolean  String  undefined null

引用类型：Object  Function

```

# 页面渲染

## 浏览器记载页面流程

**渲染时，大致的流程：**

* 解析html以构建dom树->解析CSS，得到CSSOM树->构建render树->布局render树->绘制render树;

**具体的流程：**

1. 浏览器会将HTML解析成一个DOM树，DOM树的构建过程是一个深度遍历过程,当前节点的所有子节点都构建好后才会去构建当前节点的下一个兄弟节点，
2. 将CSS解析成CSS规则树；
3. 根据DOM树和CSS来构造render树，渲染树不等于DOM树，像header和display：none；
   这种没有具体内容的东西就不在渲染树中；
4. 根据render树，浏览器可以计算出网页中有哪些节点，各节点的CSS以及从属关系，
   然后可以计算出每个节点在屏幕中的位置；
5. 遍历render树进行绘制页面中的各元素。

## 回流

**页面发生重排（回流）的话，会重新加载DOM树，影响页面加载速度。会导致页面重排的原因：**

* 页面初始化；
* 操作DOM时；
* 某些元素的尺寸变了；
* CSS的属性发生改变；

> **script需要放在body的最后位置**
>
> 因为script若在其他位置会推迟或者意外的进行预渲染。
>
> 具体原因：解析到body中的第一脚本前，浏览器就会认为已经解析得差不多了，可以进行一次预渲染。所以script如果放在head里，会推迟预渲染。如果放在body的一开头，后面的一大堆标签还没解析，等于欺骗浏览器说已经“差不多了”，也就等于违背了设计预渲染的初衷，会影响页面的效果。放在body中间也是一样的道理，所以还是放在最尾巴上比较好。

# JS继承方法

## 属性拷贝(混入式继承)

如果属性的值是引用类型的数据, 子对象和父对象共享同一份数据, 修改其中一个会影响另一个

```js
 var obj = {name : 'zs',age : 20,friends:['小明','小红']};
 var  obj1 = {}; // 需求:obj1获取obj的属性
 for(var key in obj){
      obj1[key] = obj[key];
  }
  obj1.friends.push('老王');// 修改了obj1的friends对obj的friends会有影响,因为他们存储的是同一个数组的地址
  console.log(obj1);
  console.log(obj);

```

## 原型式继承

Son.prototype = Father.prototype（父构造函数的原型对象赋值给子构造函数的原型对象）

* 创建出来的对象, 构造器属性, 默认指向父构造函数
* 无法通过构造器属性进行修改
* 不能获取实例属性/方法, 只能获取父构造函数原型对象的属性和方法

```js
// 创建父构造函数
function SuperClass(name){
  this.name = name;
  this.showName = function(){
    alert(this.name);
  }
}
// 设置父构造器的原型对象
SuperClass.prototype.showAge = function(){
  console.log(this.age);
}
// 创建子构造函数
function SubClass(){
}
// 设置子构造函数的原型对象实现继承
SubClass.prototype = SuperClass.prototype;（关键在这里！！！！！）
var child = new SubClass()
```

## 原型链继承

子构造函数.prototype = new 父构造函数()

* 无法传递参数给父构造函数-13
* 继承过来的实例属性会成为原型属性, 
* 对创建出来的对象存在数据共享的问题

```js
// 创建父构造函数
function SuperClass(){
    this.name = 'liyajie';
    this.age = 25;
    this.showName = function(){
        console.log(this.name);
    }
}
// 设置父构造函数的原型
SuperClass.prototype.friends = ['小名', '小强'];
SuperClass.prototype.showAge = function(){
    console.log(this.age);
}
// 创建子构造函数
function SubClass(){

}
// 实现继承
SubClass.prototype = new SuperClass();
// 修改子构造函数的原型的构造器属性
SubClass.prototype.constructor = SubClass;

var child = new SubClass();
console.log(child.name); // liyajie
console.log(child.age);// 25
child.showName();// liyajie
child.showAge();// 25
console.log(child.friends); // ['小名','小强']

// 当我们改变friends的时候, 父构造函数的原型对象的也会变化
child.friends.push('小王八');
console.log(child.friends);["小名", "小强", "小王八"]
var father = new SuperClass();
console.log(father.friends);["小名", "小强", "小王八"]
```

## 借用构造函数继承

使用call和apply借用其他构造函数的成员

* 可以解决给父构造函数传递参数的问题, 但是获取不到父构造函数原型上的成员.
* 也不存在共享问题
* 只能获取实例属性和方法, 不能获取原型属性和方法

```js
// 创建父构造函数
function Person(name){
  this.name = name;
  this.freinds = ['小王', '小强'];
  this.showName = function(){
     console.log(this.name);
  }
}
// 创建子构造函数
 function Student(name){
  // 使用call借用Person的构造函数
  Person.call(this, name);
 }
 // 测试是否有了 Person 的成员
 var stu = new Student('Li');
 stu.showName(); // Li
 console.log(stu.friends); // ['小王','小强']
```

## 组合继承

借用构造函数 + 原型式继承

* 解决了 父构造函数的属性继承到了子构造函数的实例对象上了,
* 继承了父构造函数原型对象上的成员
* 解决了给父构造函数传递参数问题

```js
// 创建父构造函数
function Person(name,age){
    this.name = name;
    this.age = age;
    this.showName = function(){
        console.log(this.name);
    }
}
// 设置父构造函数的原型对象
Person.prototype.showAge = function(){
    console.log(this.age);
}
// 创建子构造函数
function Student(name){
    Person.call(this,name);
}
// 设置继承
Student.prototype = Person.prototype;
Student.prototype.constructor = Student;

```

## 借用构造函数 + 深拷贝

这样就将Person的原型对象上的成员拷贝到了Student的原型上了, 这种方式没有属性共享的问题

```js
function Person(name,age){
    this.name = name;
    this.age = age;
    this.showName = function(){
        console.log(this.name);
    }
}
Person.prototype.friends = ['小王','小强','小王八'];
function Student(name,25){	   
    Person.call(this,name,25); // 借用构造函数(Person)
}
deepCopy(Student.prototype,Person.prototype);// 使用深拷贝实现继承
Student.prototype.constructor = Student;
```

# js垃圾回收机制

> * JS不像C/C++，它有自己的一套垃圾回收机制（Garbage Collection）。
> * 由于字符串、对象和数组没有固定大小，所有当它们的大小已知时，才能对他们进行动态的存储分配。
> * JavaScript程序每次创建字符串、数组或对象时，解释器都必须分配内存来存储那个实体。只要像这样动态地分配了内存，最终都要释放这些内存以便他们能够被再用，否则，JavaScript的解释器将会消耗完系统中所有可用的内存，造成系统崩溃。
> * JavaScript的解释器可以检测到何时程序不再使用一个对象了，当他确定了一个对象是无用的时候，它就知道不再需要这个对象，可以把它所占用的内存释放掉了。

## 垃圾回收方式

1. **标记清除**

   这是javascript中最常用的垃圾回收方式。
   垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记。然后，它会去掉环境中的变量以及被环境中的变量引用的标记。

2. **引用计数**

   这一种不太常见的垃圾回收策略是引用计数。
   引用计数的含义是跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型赋值给该变量时，则这个值的引用次数就是1。相反，减1。它就会释放那些引用次数为0的值所占的内存。

# 原型、原型链

## 简介

**原型：**

* 所有引用类型（函数，数组，对象）都拥有\__proto\__属性（隐式原型），属性值是一个普通的对象;
* 所有函数拥有prototype属性（显式原型）（仅限函数），属性值是一个普通的对象;
* 所有引用类型的\__proto\__属性指向它构造函数的prototype;

**原型链：**

* 当访问一个对象的某个属性时，会先在这个对象本身属性上查找;
* 如果没有找到，则会去它的\__proto\__隐式原型上查找，即它的构造函数的prototype;
* 如果还没有找到就会再在构造函数的prototype的\__proto\__中查找;
* 这样一层一层向上查找就会形成一个链式结构，我们称为原型链;

==查找属性，如果本身没有，则会去\__proto\__中查找，也就是构造函数的显式原型中查找，如果构造函数中也没有该属性，因为构造函数也是对象，也有\__proto\__，那么会去它的显式原型中查找，一直到null，如果没有则返回undefined==

```js
p.__proto__.constructor  == function Person(){}
p.___proto__.__proto__== Object.prototype
p.___proto__.__proto__.__proto__== Object.prototype.__proto__ == null         
通过__proto__形成原型链而非protrotype
```

<img src="../../../../../Download/Typora/image/image-20230209201628780.png" alt="image-20230209201628780" style="zoom:80%;" />

![image-20230208214004851](../../../../../Download/Typora/image/image-20230208214004851.png)

## isPrototypeOf()

判断一个prototype对象是否存在于另一个对象的原型链中。返回值true：是；返回值false：否
如：`a.isPrototypeOf(b)方法  判断a是否是b的父级 `

## hasOwnProperty()

 判断是自有属性还是继承的
 console.log(s1.hasOwnProperty('sum'))

## instanceof

判断实例对象是否是某个构造函数的实例对象。
console.log(p1 instanceof Product);

# 作用域链

* java的作用域是是以块区分的，也就是{}。而JavaScript的作用域，是以方法来区分的。
* javascript并没有所谓的块级作用域，javascript的作用域是相对函数而言的，可以称为\*函数作用域\*

**变量的作用域：**

* 全局作用域和局部作用域（函数内部声明变量的时候，一定要使用var命令）
* 只要函数内定义了一个局部变量，函数在解析的时候都会将这个变量“提前声明”

```js
function a(){
	var n=0;
	function b(){
	console.log(n);
    }
	return b;
}
var fn=a();//fn获得b函数的引用
var n=1;
fn();//fn()相当于执行b()，结果是0
```

（作用域是在创建定义的时候就确定了的，函数创建在谁的作用域下，谁就是它的父作用域 ，该例子中b函数的父作用域是a函数，a函数的作用域是全局）因此js在寻找的时候，就会沿着这条线寻找下去，哪里找到就哪里停止寻找，直到全局还找不到，就表示不存在。而这条线，便叫做**作用域链**。

# 闭包

函数和对其周围状态的引用捆绑在一起构成闭包。也就是说，闭包可以让你从内部函数访问外部函数作用域。在 JavaScript 中，每当函数被创建，就会在函数生成时生成闭包。

闭包很有用，因为它允许将函数与其所操作的某些数据（环境）关联起来。这显然类似于面向对象编程。因此，通常你使用只有一个方法的对象的地方，都可以使用闭包。

```js
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}
var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
//add5 和 add10 都是闭包。它们共享相同的函数定义，但是保存了不同的词法环境。
在 add5 的环境中，x 为 5。而在 add10 中，x 则为 10。

```

# http协议

## 概述

* http（超文本传输协议）是一个基于请求与响应模式的、无状态的、应用层的协议，常基于TCP的连接方式

*　http请求由三部分组成，分别是：请求行、消息报头、请求正文

* HTTP消息报头包括普通报头、请求报头、响应报头、实体报头。

1.支持客户/服务器模式。
2.简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。
3.灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。
4.无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
5.无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

## HTTP协议的主要特点

* 支持客户/服务器模式。
* 简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、HEAD、POST。每种方法规定了客户与服务器联系的类型不同。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。
* 灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。
* 无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
* 无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

## 请求方法

* GET     请求获取Request-URI所标识的资源；
* POST    在Request-URI所标识的资源后附加新的数据；
* HEAD    请求获取由Request-URI所标识的资源的响应消息报头；
* PUT     请求服务器存储一个资源，并用Request-URI作为其标识；
* DELETE  请求服务器删除Request-URI所标识的资源;
* TRACE   请求服务器回送收到的请求信息，主要用于测试或诊断；
* CONNECT 保留将来使用；
* OPTIONS 请求查询服务器的性能，或者查询与资源相关的选项和需求；

## 状态代码

* 1xx：指示信息--表示请求已接收，继续处理
* 2xx：成功--表示请求已被成功接收、理解、接受
* 3xx：重定向--要完成请求必须进行更进一步的操作
* 4xx：客户端错误--请求有语法错误或请求无法实现
* 5xx：服务器端错误--服务器未能实现合法的请求

**常见状态代码、状态描述、说明：**

>200 OK      //客户端请求成功
>400 Bad Request  //客户端请求有语法错误，不能被服务器所理解
>401 Unauthorized //请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 
>403 Forbidden  //服务器收到请求，但是拒绝提供服务
>404 Not Found  //请求资源不存在，eg：输入了错误的URL
>500 Internal Server Error //服务器发生不可预期的错误
>503 Server Unavailable  //服务器当前不能处理客户端的请求，一段时间后可能恢复正常

# js常用函数

## 数组与字符串转换

* **toString()**	将数组转换成一个字符串（var s = a.toString();）
* **toLocalString()**	把数组转换成本地约定的字符串（var s = a.toLocalString();）
* **join()**	将数组元素连接起来以构建一个字符串（var s = a.join("==");）
* **split()** 方法把字符串转换为数组。
* **split()** 方法是 String 对象方法，与 join() 方法操作正好相反。（var a = s.split("==");）

## 下拉菜单

```js
 window.onload=function(){
  var aLi=document.getElementById("nav").getElementsByTagName("li"); 
    for(var i=0; i<aLi.length;i++){                
        aLi[i].onmouseover=function(){
            this.children[1].style.display='block';//选取当前li元素的第二个子元素
        }
        aLi[i].onmouseout=function(){
            this.children[1].style.display='none';
        }
    }
}
```





