---
layout: post
title: "JS每日一题"
subtitle: "JS DAILY QUESTION"
author: "pz"
header-img: "img/post-bg-web.jpg"
header-mask: 0.4
tags:
  - 前端
  - JavaScipt
  - 笔记
---

# Javascript数组的常用方法

> 数组基本操作可以归纳为 增、删、改、查，需要留意的是哪些方法会对原数组产生影响，哪些方法不会

## 增

下面前三种是对原数组产生影响的增添方法，第四种则不会对原数组产生影响

* push()
* unshift()
* splice()
* concat()

### push

push() 方法接收任意数量的参数，并将它们添加到数组末尾，返回数组的最新长度

``` js
let colors = [];
let count = colors.push("red","green"); // 推入两顶
console.log(count) // 2
```

### unshift

unshift() 在数组开头添加任意多个值，然后返回新的数组长度

``` js
let colors = new Array();
let count = colors.unshift("red","green"); // 从数组开头推入两顶
alert(count) // 2
```

### splice

splice() 传入三个参数，分别是开始位置、0（要删除的元素数量）、插入的元素，返回空数组

```js
let colors = ["red","green","blue"];
let removed = colors.splice(1,0,"yellow","orange");
console.log(colors) //red,yellow,orange,green,blue
console.log(removed) // []
```

### concat

concat() 首先会创建一个当前数组的副本，然后再把它的参数添加到副本末尾，最后返回这个新结构的数组，不会影响原始数组

```js
let colors = ["red", "green","blue"];
let colors2 = colors.concat("yellow",["black","brown"]);
console.log(colors); // ["red", "green","blue"]
console.log(colors2); // ["red", "green","blue","yellow","black","brown"]
```

## 删

下面三种都会影响原数组，最后一项不影响原数组：

* pop()
* shift()
* shlice()
* slice()

### pop

pop() 方法用于删除数据的最后一项，同时减少数组的length值，返回被删除的项

```js
let colors = ["res","green"];
let item = colors.pop(); // 取得最后一项
console.log(item) // green
console.log(colors.length) // 1
```

### shift

shift() 方法用于删除数组的第一项,同时减少数组的length值，返回被删除的项

```js
let colors = ["red","green"];
let item = colors.shift() // 取得第一项
console.log(item) // red
console.log(colors.length) // 1
```

### splice

splice() 传入两个参数，分别是开始位置、删除的元素数量，返回包含删除元素的数组

```js
let colors = ["red","green","blue"];
let removed = colors.splice(0,1);
console.log(colors); // green,blue
console.log(removed)； // red,只有一个元素的数组
```

### slice

slice() 传入一个或两个参数，分别是开始位置、结束位置（不包含在内），用于创建一个包含原有数组中一个或多个元素的新数组，不会影响原始数组

```js
let colors = ["red","green","blue","yellow","purple"];
let colors2 = colors.slice(1);
let colors3 = colors.slice(1,4);
console.log(colors); // ["red","green","blue","yellow","purple"]
console.log(colors2); // ["green","blue","yellow","purple"]
console.log(colors3); // ["green","blue","yellow"]
```

## 改

即修改原来数组的内容，常用splice

### splice

splice() 传入三个参数，分别是开始位置、要删除的元素数量、插入的任意多个元素，返回删除元素的数组，对原数组产生影响

```js
let colors = ["red","green","blue"];
let removed = colors.splice(1,1,"red","purple");
console.log(colors) //red,red,purple,blue
console.log(removed) // green
```

## 查

即查找元素，返回元素坐标或者元素值

* indexOf()
* includes()
* find()

### indexOf

返回要查找的元素在数组中的位置，如果没找到则返回-1

```js
let numbers = [1,2,3,4,5,3,4,3,1];
numbers.indexOf(4) // 3
```

### includes

返回要查找的元素在数组中的位置，找到返回 `true`,否则`false`

```js
let numbers = [1,2,3,4,5,4,3,2,1];
numbers.includes(4) // true
```

### find

返回第一个匹配的元素

```js
const people = [
    {
        name:"Matt",
        age: 27
    },{
        name: "Nicholas",
        age: 29
    }
];
people.find((element,index,array) => element.age<28) // {name: "Matt",age: 27}
```

## 排序方法

数组有两个方法可以用来对元素重新排序

* reverse()
* sort()

### reverse

reverse() 将数组元素方向排列

```js
let values = [1,2,3,4,5];
values.reverse();
alert(values) // 5,4,3,2,1
```

### sort

sort() 方法接受一个比较函数，用于判断哪个值应该排在前面

```js
function compare(value1,value2){
    if(value1 < value2){
        return -1;
    }else if(value1 > value2){
        return 1;
    }else {
        return 0;
    }
}
let values = [0,1,5,10,15];
values.sort(compare);
alert(values); // 0,1,5,10,15
```

## 转换方法

常用的转换方法有

* join()

### join

join() 方法接收一个参数，即字符串分隔符，返回包含所有项的字符串

```js
let colors = ["red","green","blue"];
alert(colors.join(",")); // red,green,blue
alert(colors.join("||")) // red||green||blue
```

## 迭代方法

常用来迭代数组的方法（都不改变原数组）有如下：

* some()
* every()
* forEach()
* filter()
* map()

### some

对数组每一项都运行传入的函数，如果有一项函数返回`true`，则这个方法返回`true`

```js
let numbers =[1,2,3,4,5,4,3,2,1];
let someResult = numbers.some((item,index,array) => item > 2);
console.log(someResult) // true
```

### every

对数组每一项都运行传入的函数，如果对每一项函数都返回`true`，则这个方法返回`true`

```js
let numbers = [1,2,3,4,5,4,3,2,1];
let everyResult = numbers.every((item,index,array) => item > 2);
console.log(someResult) // false
```

### forEach

对数组每一项都运行传入的函数，没有返回值

```js
let numbers = [1,2,3,4,5,4,3,2,1];
numbers.forEach((item,index,array) => {
    // 执行操作
})
```

### filter

对数组每一项都允许传入的函数，函数返回`true`的项会组成数组之后返回

```js
let numbers = [1,2,3,4,5,4,3,2,1];
let filterResult = numbers.filter((item,index,array) => item>2);
console.log(filterResult); // 3，4，5，4，3
```

### map

对数组每一项都运行传入的函数，返回由每次函数调用的结果构成的数组

```js
let numbers = [1,2,3,4,5,4,3,2,1];
let mapResult = numbers.map((item,index,array) => item*2);
console.log(mapResult) // 2,4,6,8,10,8,6,4,2
```

# Javascript字符串的常用方法

字符串的常用的操作方法归纳为增、删、改、查’

## 增

这里的增的意思并不是说直接添加内容，而是创建字符串的一个副本，再进行操作；

除了常用`+` 以及`${}`进行字符串拼接之外，还可以通过`concat`

### concat

用于将一个或多个字符串拼成一个新字符串

```js
let stringValue = "hello";
let result = stringValue.concat("world");
console.log(result); // "hello world"
console.log(stringValue); // "hello"
```

## 删

这里的删的意思并不是说删除字符串的内容，而是创建字符串的一个副本，再进行操作

常见的有：

* slice()
* substr()
* substring()

这三个方法都返回调用它们的字符串的一个子字符串，而且都接收一个或两个参数

``` js
let stringValue = "hello world";
console.log(stringValue.slice(3)) // "lo world"
console.log(stringValue.substring(3)) // "lo world"
console.log(stringValue.substr(3)) // "lo world"
console.log(stringValue.slice(3,7)) // "lo w"
console.log(stringValue.subtring(3,7)) // "lo w"
console.log(stringValue.substr(3,7)) // "lo worl"
```

## 改

这里的改的意思也不是改变原字符串，而是创建字符串的一个副本，再进行操作

常见的有：

* trim()、trimLeft()、trimRight()
* repeat()
* padStart()、padEnd()
* toLowerCase()、toUpperCase()

### trim()、trimLeft()、trimRight()

删除前、后或前后所有空格符，再返回新的字符串

```js
let stringValue = " hello world ";
let trimmedStringValue = stringValue.trim();
console.log(stringValue); // " hello world "
console.log(trimmedStringValue); // "hello world"
```

### repeat

接受一个整数参数，表示要将字符串复制多少次，然后返回拼接所有副本后的结果

```js
let stringValue = "na"
let copyResult = stringValue.repeat(2) // na na
```

### padEnd

复制字符串，如果小于指定长度，则在相应一边填充字符，直到满足长度条件

```js
let stringValue = "foo"
console.log(stringValue.padStart(6)); // "foo"
console.log(stringValue.padStart(9,".")) // "......foo"
```

### toLowerCase()、toUpperCase()

大小写转化

```js
let stringValue = "hello world"
console.log(stringValue.toUpperCase()) // "HELLO WORLD"
console.log(stringValue.toLowerCase()) // "hello world"
```

## 查

除了通过索引的方式获取字符串的值，还可以通过：

* charAt()
* indexOf()
* startWith()
* includes()

### charAt

返回给定索引位置的字符，由传给方法的整数参数指定

```js
let message = "abcd"
console.log(message.charAt(2)) // "c"
```

### indexOf

从字符串开头去搜索传入的字符串，并返回位置（如果没有找到返回-1）

```js
let stringValue = "hello world"
console.log(stringValue.indexOf("0")) // 4
```

### startWith、includes

从字符串中搜索传入的字符串，并返回一个表示是否包含的布尔值

```js
let message = "foobarbaz"
console.log(message.startsWith("foo")); // true
console.log(message.startsWith("bar")); // false
console.log(message.includes("bar")); // true
console.log(message.includes("qux")); // false
```

## 转换方法

### split

把字符串按照指定的分割符，拆分成数组的每一项

```js
let str = "12+23+34";
let arr = str.split("+") // [12,23,34]
```

## 模板匹配方法

针对正则表达式，字符串设计了几种方法

* match()
* search()
* replace()

### match

接收一个参数，可以是一个正则表达式字符串，也可以是一个`RegExp`对象，返回数组

```js
let text = "cat,bat,sat,fat";
let pattern = /.at/;
let matches = text.match(pattern);
console.log(matches[0]); // "cat"
```

### search

接收一个参数，可以是一个正则表达式字符串，也可以是一个`RegExp`对象，找到则返回匹配索引，否则返回-1

```js
let text = "cat,bat,sat,fat";
let pos = text.search(/at/);
console.log(pos); // 1
```

### replace

接受两个参数，第一个参数为匹配的内容，第二个参数为替换的元素（可用函数）

```js
let text = "cat,bat,sat,fat";
let result = text.replace("at","ond");
console.log(result); // "cond,bat,sat,fat"
```

# JavaScript 中的类型转换机制

## 概述

Js中有六种简单数据类型：`undefined`、`null`、`boolean`、`string`、`number`、`symbol(标识)`，以及引用类型：`object`

但是我们在声明的时候只有一种数据类型，只有到运行期间才会确定当前类型

```js
let x=y?1:a;
```

上面代码中，`x`的值在编译阶段是无法获取到，只有等到程序运行时才能知道

虽然变量的数据类型是不确定的，但是各种运算符对数据类型是有要求的，如有运算子的类型与预期不符合，就会触发类型转换机制

常见的类型转换有：

* 强制转换（显示转换）
* 自动转换（隐式转换）

## 显示转换

显示转换，即我们很清楚可以看到这里发生了类型的转变，常见的方法有：

* Number()
* parseInt()
* String()
* Boolean()

### Number

将任意类型的值转化为数值

类型转换规则如下：

|  原始值   |             转换结果             |
| :-------: | :------------------------------: |
| Undefined |               NaN                |
|   Null    |                0                 |
|   true    |                1                 |
|   false   |                0                 |
|  String   |     根据语法和转换规则来转换     |
|  Symbol   |   Throw a TypeError exception    |
|  Object   | 先调用toPrimitive,再调用toNumber |

```js
Number(324) // 324

// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回 NaN
Number('324abc') // NaN

// 空字符串转为 0
Number(' ') // 0

// 布尔值：true 转成 1，false 转成 0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // Nan

// null：转成0
Number(null)

// 对象：通常转换成NaN(除了只包含单个数值的数组)
Number({a:1}) // NaN
Number([1,2,3]) // NaN
Number([5]) // 5
```

可以看出，`Number`转换的时候是很严格的，只要有一个字符无法转换为数值，整个字符串就会转为`NaN`

### parseInt

`parseInt`相比`Number`，就没那么严格了。`parseInt`函数逐个解析字符，遇到不能转换的字符就停下来

```js
parseInt("32a3") //32
```

### String

可以将任意类型的值转化成字符串

转换规则如下:

|  原始值   |             转换结果             |
| :-------: | :------------------------------: |
| Undefined |           'Undefined'            |
|  Boolean  |        'true' or 'false'         |
|  Number   |         对应的字符串类型         |
|  String   |              String              |
|  Symbol   |   Throw a TypeError exception    |
|  Object   | 先调用toPrimitive,再调用toNumber |

```js
// 数值：转为相应的字符串
String(1) // "1"

// 字符串：转换后还是原来的值
String("a") // "a"

// 布尔值：true转为字符串“true”，false转为字符串“false”
String(true) // "true"

// undefined:转为字符串"undefined"
String(undefined) //"undefined"

// null：转为字符串"null"
String(null) // "null"

// 对象
String({a:1}) // "[object Object]"
String([1,2,3]) // "1,2,3"
```

### Boolean

将任意类型的值转换为布尔值，转换规则如下：

| 数据类型  |     转换为true的值     | 转化为false的值 |
| :-------: | :--------------------: | :-------------: |
|  Boolean  |          true          |      false      |
|  String   |       非空字符串       |  " "(空字符串)  |
|  Number   | 非零数值（包括无穷值） |     0、NaN      |
|  Object   |        任意对象        |      null       |
| Undefined |     N/A（不存在）      |    undefined    |

```js
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean(NaN) // false
Boolean('') // false
Boolean({}) // true
Boolean([]) // true
Boolean(new Boolean(false)) // true
```

## 隐式转换

在隐式转换中，可以归纳为两种情况下发生：

* 比较运算（`==`、`!=`、`>`、`<`）、`if`、`while`需要布尔值地方

* 算术运算（`+`、`-`、`*`、`/`、`%`）

  除了以上场景，还要求运算符两边的操作数不是同一类型

### 自动转换为布尔值

在需要布尔值的地方，就会将非布尔值的参数自动转换为布尔值，系统内部会调用`Boolean`函数

* undefined
* null
* false
* +0
* -0
* NaN
* " "

除了上面几种会转化为`false`，其他都转化为`true`

### 自动转换为字符串

遇到预期为字符串的地方，就会将非字符串的值自动转为字符串

具体规则：先将复合类型的值转为原始类型的值，再将原始类型的值转为字符串

常发生在`+`运算中，一旦存在字符串，则会进行字符串拼接操作

```js
'5' + 1 // '51'
'5' + true // "5true"
'5' + false // "5false"
'5' + {} // "5[object Object]"
'5' + [] // "5"
'5' + function (){} // "5function (){}"
'5' + undefined // "5undefined"
'5' + null // "5null"
```

### 自动转换为数值

除了`+`有可能把运算子转为字符串，其他运算符都会把运算子自动转为数值

```js
'5' - '2' // 3
'5' * '2' // 10
true - 1  // 0
false - 1 // -1
'1' - 1   // 0
'5' * []    // 0
false / '5' // 0
'abc' - 1   // NaN
null + 1 // 1
undefined + 1 // NaN
```

`null`转为数值时，值为`0` 。`undefined`转为数值时，值为`NaN`

# == 和 === 区别，分别在什么情况使用

## 等于操作符

等于操作符用两个等于号（ == ）表示，如果操作数相等，则会返回 `true`

前面文章，我们提到在`JavaScript`中存在隐式转换。等于操作符（==）在比较中会先进行类型转换，再确定操作数是否相等

遵循以下规则：

如果任一操作数是布尔值，则将其转换为数值再比较是否相等

```js
let result1 = (true == 1); // true
```

如果一个操作符是字符串，另一个操作数是数值，则尝试将字符串转换为数值，再比较是否相等

```js
let result1 = ("55" == 55); // true
```

如果一个操作数是对象，另一个操作数不是，则调用对象的valueOf()方法取得其原始值，再根据前面的规则进行比较

```js
let obj = {valueOf:function(){return 1}}
let result1 = (obj == 1) // true
```

`null`和`undefined`相等

```js
let result1 = (null == undefined) // true 
```

如果有任一操作数是 `NaN` ，则相等操作符返回 `false`

```js
let result1 = (NaN == NaN ); // false
```

如果两个操作数都是对象，则比较它们是不是同一个对象。如果两个操作数都指向同一个对象，则相等操作符返回`true`

```js
let obj1 = {name:"xxx"}
let obj2 = {name:"xxx"}
let result1 = (obj1 == obj2 ); // false
```

下面进一步做个小结：

- 两个都为简单类型，字符串和布尔值都会转换成数值，再比较
- 简单类型与引用类型比较，对象转化成其原始类型的值，再比较
- 两个都为引用类型，则比较它们是否指向同一个对象
- null 和 undefined 相等
- 存在 NaN 则返回 false

## 全等操作符

全等操作符由 3 个等于号（ === ）表示，只有两个操作数在不转换的前提下相等才返回 `true`。即类型相同，值也需相同

```js
let result1 = ("55" === 55); // false，不相等，因为数据类型不同
let result2 = (55 === 55); // true，相等，因为数据类型相同值也相同
```

`undefined` 和 `null` 与自身严格相等

```js
let result1 = (null === null)  //true
let result2 = (undefined === undefined)  //true
```

### 区别

相等操作符（==）会做类型转换，再进行值的比较，全等运算符不会做类型转换

```js
let result1 = ("55" === 55); // false，不相等，因为数据类型不同
let result2 = (55 === 55); // true，相等，因为数据类型相同值也相同
```

`null` 和 `undefined` 比较，相等操作符（==）为`true`，全等为`false`

```js
let result1 = (null == undefined ); // true
let result2 = (null  === undefined); // false
```

## 小结

相等运算符隐藏的类型转换，会带来一些违反直觉的结果

```js
'' == '0' // false
0 == '' // true
0 == '0' // true

false == 'false' // false
false == '0' // true

false == undefined // false
false == null // false
null == undefined // true

' \t\r\n' == 0 // true
```

但在比较`null`的情况的时候，我们一般使用相等操作符`==`

```js
const obj = {};

if(obj.x == null){
  console.log("1");  //执行
}
```

等同于下面写法

```js
if(obj.x === null || obj.x === undefined) {
    ...
}
```

使用相等操作符（==）的写法明显更加简洁了

所以，除了在比较对象属性为`null`或者`undefined`的情况下，我们可以使用相等操作符（\==），其他情况建议一律使用全等操作符（\===）

# 深拷贝和浅拷贝

## 数据类型存储

`javaScript`中存在两大数据类型：

* 基本类型
* 引用类型

基本类型数据保存在栈内存中

引用类型数据保存在堆内存中，引用数据类型的变量是一个指向推内存中实际对象的引用，存在栈中

## 浅拷贝

浅拷贝，指的是创建新的数据，这个数据有着原始数据属性值的一份精确拷贝

如果属性是即被类型，拷贝的就是基本类型的值。如果属性是引用类型，拷贝的就是内存地址

即浅拷贝是拷贝一层，深层次的引用类型则共享内存地址

下面简单实现一个浅拷贝

```js
function shallowClone(obj){
    const newObj = {};
    for(let prop in obj){
        if(obj.hasOwnProperty(prop)){
            newObj[prop] = obj[prop]
        }
    }
}
```

在`JavaScript`中，存在浅拷贝的现象有：

* Object.assign
* Array.prototype.slice()
* Array.prototype.concat()
* 使用拓展运算符实现的复制

### Object.assign

```js
var obj = {
    age:18,
    nature:['smart','good'],
    names:{
        name1:'fx',
        name2:'xka'
    }
    love:function(){
        console.log("fx is a great girl");
    }
}
var newObj = Object.assign({},fxObj);
```

### slice()

```js
const fxArr = ['One','Two','Three']
const fxArrs = fxArr.slice(0)
fxArrs[1] = 'love'
console.log(fxArr) //['One','Two','Three']
console.log(fxArrs) //['One','love','Three']
```

### concat

```js
const fxArr = ['One','Two','Three']
const fxArrs = fxArr.concat(0)
fxArrs[1] = 'love'
console.log(fxArr) //['One','Two','Three']
console.log(fxArrs) //['One','love','Three']
```

### 拓展运算符

```js
const fxArr = ['One','Two','Three']
const fxArrs = [...fxArr]
fxArrs[1] = 'love'
console.log(fxArr) //['One','Two','Three']
console.log(fxArrs) //['One','love','Three']
```

> 原因就是我将fxArrs[1]存储的指针变了，而fxArr[1]的指针没有变
>
> concat方法不会改变this或任何作为参数提供的数组，而是返回一个 浅拷贝，它包含与原始数组相结合的相同元素的副本
>
> 当数组内部属性值为：
>
> * **对象引用**（而不是实际对象）：concat将对象引用复制到新数组中。 原始数组和新数组都引用相同的对象。 也就是说，如果引用的对象被修改，则更改对于新数组和原始数组都是可见的。 这包括也是数组的数组参数的元素。
> * **数据类型如字符串，数字和布尔**（不是String，Number 和 Boolean 对象）：concat将字符串和数字的值复制到新数组中。

## 深拷贝

深拷贝开辟一个新的栈，两个对象属性完全相同，但是对应两个不同的地址，修改一个对象的属性，不会改变另外一个对象属性

常见的深拷贝方式有：

* _.cloneDeep()
* jQuery.extend()
* JSON.stringify()
* 手写循环递归

### _.cloneDeep()

```js
const _ = require('lodash');
const obj1 ={
    a:1,
    b:{f:{g:1}},
    c:[1,2,3]
}
const obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f) // false
```

### jQuery.extend()

```js
const $ = require('jquery');
const obj1 = {
    a:1,
    b:{f:{g:1}},
    c:[1,2,3]
}
const obj2 = $.extend(true,{},obj1);
console.log(obj1.b.f === obj2.b.f) // false
```

### JSON.stringify()

```js
const $ = JSON.parse(JSON.stringify(obj1));
```

但是这种方式存在弊端，会忽略`undefined`、`symbol`和函数

```js
const obj = {
    name: 'A',
    name1: undefined,
    name3: function(){}
    name4: Symbol('A')
}
const obj2 = JSON.parse(JSON.stringify(obj));
console.log(obj2);// {name: "A"}
```

### 循环递归

```js
function deepClone(obj, hash = new WeakMap()) {
  if (obj === null) return obj; // 如果是null或者undefined我就不进行拷贝操作
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);
  // 可能是对象或者普通的值  如果是函数的话是不需要深拷贝
  if (typeof obj !== "object") return obj;
  // 是对象的话就要进行深拷贝
  if (hash.get(obj)) return hash.get(obj);
  let cloneObj = new obj.constructor();
  // 找到的是所属类原型上的constructor,而原型上的 constructor指向的是当前类本身
  hash.set(obj, cloneObj);
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      // 实现一个递归拷贝
      cloneObj[key] = deepClone(obj[key], hash);
    }
  }
  return cloneObj;
}
```

## 区别

下面首先借助两张图，可以更加清晰看到浅拷贝与深拷贝的区别

<img src="/img/image/image-20220519230555068.png" alt="image-20220519230555068" style="zoom:80%;" />

<img src="/img/image/image-20220519230626967.png" alt="image-20220519230626967" style="zoom:80%;" />

从上图发现，浅拷贝和深拷贝都创建出一个新的对象，但在复制对象属性的时候，行为就不一样

浅拷贝只复制属性指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存，修改对象属性会影响原对象

```js
// 浅拷贝
const obj1 = {
    name : 'init',
    arr : [1,[2,3],4],
};
const obj3=shallowClone(obj1) // 一个浅拷贝方法
obj3.name = "update";
obj3.arr[1] = [5,6,7] ; // 新旧对象还是共享同一块内存

console.log('obj1',obj1) // obj1 { name: 'init',  arr: [ 1, [ 5, 6, 7 ], 4 ] }
console.log('obj3',obj3) // obj3 { name: 'update', arr: [ 1, [ 5, 6, 7 ], 4 ] }
```

但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象

```js
// 深拷贝
const obj1 = {
    name : 'init',
    arr : [1,[2,3],4],
};
const obj4=deepClone(obj1) // 一个深拷贝方法
obj4.name = "update";
obj4.arr[1] = [5,6,7] ; // 新对象跟原对象不共享内存

console.log('obj1',obj1) // obj1 { name: 'init', arr: [ 1, [ 2, 3 ], 4 ] }
console.log('obj4',obj4) // obj4 { name: 'update', arr: [ 1, [ 5, 6, 7 ], 4 ] }
```

## 小结

前提为拷贝类型为引用类型的情况下：

- 浅拷贝是拷贝一层，属性为对象时，浅拷贝是复制，两个对象指向同一个地址
- 深拷贝是递归拷贝深层次，属性为对象时，深拷贝是新开栈，两个对象指向不同的地址

# 闭包的理解和使用场景

## 概念

一个函数对其周围状态（lexical environme，词法环境）的引用捆绑在一起（或者说函数被引用周围

），这样的组合就是闭包（closure）

也就是说，闭包让你可以在一个内层函数中访问到其外层函数的作用域

在`Javascript`中，每当创建一个函数，闭包就会在函数创建的同时被创建出来，作为函数内部与外部连接起来的一个桥梁

```js
function init(){
    var name = "Mozille"; // name 是一个被init创建的局部变量
    function displayName(){ // displayName() 是一个内部函数，一个闭包
        alert(name); // 使用了父函数中声明的变量
    }
    displayName();
}
init();
```

`displayName()`没有自己的局部变量。然而，由于闭包的特性，它可以访问到外部函数的变量

## 使用场景

任何闭包的使用场景都离不开这两点：

* 创建私有变量
* 延长变量的生命周期

> 一般函数的词法环境在函数返回后就会被销毁，但是闭包会保存对创建时所在词法环境的引用，即便创建时所在的执行上下文被销毁，但创建时所在词法环境依然存在，以达到延长变量的生命周期的目的

在页面上添加一些可以调整字号的按钮：

```js
function makeSizer(size){
    return function(){
        document.body.style.fontSize = size + 'px';
    }
}
var size12 = makeSizer(12);
var size14 = makeSizer(14);
var size16 = makeSizer(16);

document.getElementById('size-12').onclick = size12;
document.getElementById('size-14').onclick = size14;
document.getElementById('size-16').onclick = size16;
```

## 柯里化函数

柯里化的目的在于避免频繁调用具有相同参数函数的同时，又能够轻松的重用

```js
// 假设我们有一个求长方形面积的函数
function getArea(width,height){
    return width*height;
}

// 如果我们碰到的长方形的宽老是10
const area1 = getArea(10, 20)
const area2 = getArea(10, 30)
const area3 = getArea(10, 40)

// 我们可以使用闭包柯里化这个计算面积的函数
function getArea(width){
    return height => {
        return width*height
    }
}

const getTenWidthArea = getArea(10)
// 之后碰到宽度为10的长方形就可以这样计算面积
const area1 = getTenWidthArea(20)

// 而且如果遇到宽度偶尔变化也可以轻松复用
const getTwentyWidthArea = getArea(20)
```

## 使用闭包模拟私有方法

在`JavaScript`中，没有支持声明私有变量，但我们可以使用闭包来模拟私有方法

```js
var Counter = (function(){
    var privateCounter = 0;
    function changeBy(val){
        privateCounter += val;
    }
    return {
        increment: function(){
            changeBy(1);
        },
        decrement: function(){
            changeBy(-1);
        },
        value: function(){
            return privateCounter;
        }
    }
})();
var Counter1 = makeCounter();
var Counter2 = makeCounter();
console.log(Counter1.value()); /* logs 0 */
Counter1.increment();
Counter1.increment();
console.log(Counter1.value()); /* logs 2 */
Counter1.decrement();
console.log(Counter1.value()); /* logs 1 */
console.log(Counter2.value()); /* logs 0 */
```

上述通过使用闭包来定义公共函数，并令其可以访问私有函数和变量，这种方式也叫模块方式

两个计数器 `Counter1` 和 `Counter2` 是维护它们各自的独立性的，每次调用其中一个计数器时，通过改变这个变量的值，会改变这个闭包的词法环境，不会影响另一个闭包中的变量

## 其他

例如计数器、延迟调用、回调等闭包的应用，其核心思想还是创建私有变量和延长变量的生命周期

## 注意事项

如果不是某些特定任务需要使用闭包，在其它函数中创建函数是不明智的，因为闭包在处理速度和内存消耗方面对脚本性能具有负面影响

例如，在创建新的对象或者类时，方法通常应该关联于对象的原型，而不是定义到对象的构造器中。

原因在于每个对象的创建，方法都会被重新赋值

```js
function MyObject(name, message) {
    this.name = name.toString();
    this.message = message.toString();
    this.getName = function(){
        return this.name;
    };
    
    this.getMessage = function(){
        return this.message;
    }
}

```

上面的代码中，我们并没有利用到闭包的好处，因此可以避免使用闭包。修改成如下：

```js
function MyObject(name, message) {
  this.name = name.toString();
  this.message = message.toString();
}
MyObject.prototype.getName = function() {
  return this.name;
};
MyObject.prototype.getMessage = function() {
  return this.message;
};
```

# JavaScript中作用域

## 概念

作用域：即变量（变量作用域又称上下文）和函数生效（能被访问）的区域或集合

换句话说，作用域决定了代码块中变量和其他资源的可见性

```js
function myFunction(){
    let inVariable = '函数内部变量'；
}
myFunction(); // 要先执行这个函数，否则根本不知道里面是啥
console.log(inVariable); // Uncaught ReferenceError: inVariable is not defined
```

函数`myFunction`内部创建一个`inVariable`变量，当我们在全局访问这个变量的时候，系统会报错

这就说明我们在全局是无法获取到（闭包除外）函数内部的变量

我们一般将作用域分成：

- 全局作用域
- 函数作用域
- 块级作用域

## 全局作用域

任何不在函数中或是大括号中声明的变量，都是在全局作用域下，全局作用域下声明的变量可以在程序的任意位置访问

```js
// 全局变量
var greeting = 'Hello World!';
function greet() {
  console.log(greeting);
}
// 打印 'Hello World!'
greet();
```

## 函数作用域

函数作用域也叫局部作用域，如果一个变量是在函数内部声明的它就在一个函数作用域下面。这些变量只能在函数内部访问，不能在函数以外去访问

```js
function greet() {
  var greeting = 'Hello World!';
  console.log(greeting);
}
// 打印 'Hello World!'
greet();
// 报错：Uncaught ReferenceError: greeting is not defined
console.log(greeting);
```

可见上述代码中在函数内部声明的变量或函数，在函数外部是无法访问的，这说明在函数内部定义的变量或者方法只是函数作用域

## 块级作用域

ES6引入了`let`和`const`关键字,和`var`关键字不同，在大括号中使用`let`和`const`声明的变量存在于块级作用域中。在大括号之外不能访问这些变量

```js
{
  // 块级作用域中的变量
  let greeting = 'Hello World!';
  var lang = 'English';
  console.log(greeting); // Prints 'Hello World!'
}
// 变量 'English'
console.log(lang);
// 报错：Uncaught ReferenceError: greeting is not defined
console.log(greeting);
```

## 词法作用域

词法作用域，又叫静态作用域，变量被创建时就确定好了，而非执行阶段确定的。也就是说我们写好代码时它的作用域就确定了，`JavaScript` 遵循的就是词法作用域

词法作用域的规则：

1. 函数允许访问函数外的数据 (也有就近原则）
2. 整个代码结构中只有函数可以限定作用域
3. 作用域内首先使用变量提升分析
4. 如果当前作用域找到所需变量，则停止查找

```js
var a = 2; // LSH(left-hand side)找到变量所在的容器本身，并且将对该变量进行赋值。
function foo(){
    console.log(a)
}
function bar(){
    var a = 3;
    foo();
}
bar()
```

通过RHS(查找某个变量的值)，就根据书写的位置，查找上面一层的代码，引用到了全局作用域中的a，所以输出2。但是如果JS是动态作用域，情况就不一样了，当`foo()`无法找到a的变量引用时，会在调用`foo()`的地方查找a，而不是在嵌套的词法作用域上查找，所以会输出3。

## 作用域链

当在`Javascript`中使用一个变量的时候，首先`Javascript`引擎会尝试在当前作用域下去寻找该变量，如果没找到，再到它的上层作用域寻找，以此类推直到找到该变量或是已经到了全局作用域

如果在全局作用域里仍然找不到该变量，它就会在全局范围内隐式声明该变量(非严格模式下)或是直接报错

把作用域比喻成一个建筑，这份建筑代表程序中的嵌套作用域链，第一层代表当前的执行作用域，顶层代表全局作用域

<img src="/img/image/image-20220522203107424.png" alt="image-20220522203107424" style="zoom:67%;" />

变量的引用会顺着当前楼层进行查找，如果找不到，则会往上一层找，一旦到达顶层，查找的过程都会停止

```js
var sex = '男';
function person(){
    var name = '张三';
    function student(){
        var age = 18;
        console.log(name); // 张三
        console.log(sex); // 男
    }
    student();
    console.log(age); // Uncaught ReferenceError: age is not defined
}
person();
```

上述代码主要做了一下工作：

* `student`函数内部属于最内层作用域，找不到`name`，向上一层作用域`person`函数内部找，找到了输出“张三”
* `student`内部输出cat时找不到，向上一层作用域`person`函数找，还找不到继续向上一层找，即全局作用域，找到了输出“男”
* 在`person`函数内部输出`age`时找不到，向上一层作用域找，即全局作用域，还是找不到则报错

# JavaScript原型，原型链

## 原型

`JavaScript`常被描述为一种基于原型的语言——每个对象拥有一个原型对象

当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾

函数可以有属性。每个函数都有一个特殊的属性叫作原型`prototype`

```js
function doSomething(){}
console.log(doSomething.prototype);
```

控制台输出

```js
{
    constructor: ƒ doSomething(),
    __proto__: {
        constructor: ƒ Object(),
        hasOwnProperty: ƒ hasOwnProperty(),
        isPrototypeOf: ƒ isPrototypeOf(),
        propertyIsEnumerable: ƒ propertyIsEnumerable(),
        toLocaleString: ƒ toLocaleString(),
        toString: ƒ toString(),
        valueOf: ƒ valueOf()
    }
}
```

上面这个对象，就是大家常说的原型对象

可以看到，原型对象有一个自有属性`constructor`，这个属性指向该函数，如下图关系展示

![image-20220522211442216](/img/image/image-20220522211442216.png)

## 原型链

原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为原型链（prototype chain)，它解释了为何一个对象会拥有定义在其他对象中的属性和方法

在对象实例和它的构造器之间建立一个链接（它是`__proto__`属性，是从构造函数的`prototype`属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法

```js
function Person(name){
    this.name = name;
    this.age = 18;
    this.sayName = function(){
        console.log(this.name);
    }
}
// 常见实例
var person = new Person('person')
```

根据代码，我们可以得到下图

<img src="/img/image/image-20220522212918184.png" alt="image-20220522212918184" style="zoom:80%;" />

下面分析一下：

- 构造函数`Person`存在原型对象`Person.prototype`
- 构造函数生成实例对象`person`，`person`的`__proto__`指向构造函数`Person`原型对象
- `Person.prototype.__proto__` 指向内置对象，因为 `Person.prototype` 是个对象，默认是由 `Object`函数作为类创建的，而 `Object.prototype` 为内置对象
- `Person.__proto__` 指向内置匿名函数 `anonymous`，因为 Person 是个函数对象，默认由 Function 作为类创建
- `Function.prototype` 和 `Function.__proto__`同时指向内置匿名函数 `anonymous`，这样原型链的终点就是 `null`

## 总结

`__proto__`作为不同对象之间的桥梁，用来指向创建它的构造函数的原型对象的

<img src="/img/image/image-20220522213802727.png" alt="image-20220522213802727" style="zoom:80%;" />

每个对象的`__proto__`都是指向它的构造函数的原型对象`prototype`

```js
person1.__proto__ === Person.prototype
```

构造函数是一个函数对象，是通过 `Function`构造器产生的

```js
Person.__proto__ === Function.prototype
```

原型对象本身是一个普通对象，而普通对象的构造函数都是`Object`

```js
Person.prototype.__proto__ === Object.prototype
```

刚刚上面说了，所有的构造器都是函数对象，函数对象都是 `Function`构造产生的

```js
Object.__proto__ === Function.prototype
```

`Object`的原型对象也有`__proto__`属性指向`null`，`null`是原型链的顶端

```js
Object.prototype.__proto__ === null
```

下面作出总结：

- 一切对象都是继承自`Object`对象，`Object` 对象直接继承根源对象`null`
- 一切的函数对象（包括 `Object` 对象），都是继承自 `Function` 对象
- `Object` 对象直接继承自 `Function` 对象
- `Function`对象的`__proto__`会指向自己的原型对象，最终还是继承自`Object`对象

# JavaScript继承

## 概念

继承是面向对象软件技术当中的一个概念

如果一个类别B“继承自”另一个类别A，就把这个B称为“A的子类”，而把A称为“B的父类别”也可以称“A是B的超类”

继承的优点：

* 继承可以使得子类具有父类别的各种属性和方法，而不需要再次编写相同的代码
* 在子类别继承父类别的同时，可以重新定义某些属性，并重写某些方法，即覆盖父类别的原有属性和方法，使其获得与父类别不同的功能

虽然`JavaScript`并不是真正的面向对象语言，但它天生的灵活性，使应用场景更加丰富

关于继承，我们举个形象的例子：

定义一个类（Class）叫汽车，汽车的属性包括颜色、轮胎、品牌、速度、排气量等

```js
class Car{
    constructor(color,speed){
        this.color = color
        this.speed = speed
        // ...
    }
}
```

由汽车这个类可以派生出“轿车”和“货车”两个类，在汽车的基础属性上，为轿车添加一个后备厢、给货车添加一个大货箱

```js
// 货车
class Truck extends Car{
    constructor(color,speed){
        super(color,speed)
        this.Container = true // 货箱
    }
}
```

这样轿车和货车就是不一样的，但是二者都属于汽车这个类，汽车、轿车继承了汽车的属性，而不需要再次在“轿车”中定义汽车已经有的属性

在“轿车”继承“汽车”的同时，也可以重新定义汽车的某些属性，并重写或覆盖某些属性和方法，使其获得与“汽车”这个父类不同的属性和方法

```js
class Truck extends Car{
    constructor(color,speed){
        super(color,speed)
        this.color = "black" //覆盖
        this.Container = true // 货箱
    }
}
```

从这个例子中就能详细说明汽车、轿车以及卡车之间的继承关系

## 实现方式

下面给出`JavaScripy`常见的继承方式：

- 原型链继承
- 构造函数继承（借助 call）
- 组合继承
- 原型式继承
- 寄生式继承
- 寄生组合式继承

### 原型链继承

原型链继承是比较常见的继承方式之一，其中涉及的构造函数、原型和实例，三者之间存在着一定的关系，即每一个构造函数都有一个原型对象，原型对象又包含一个指向构造函数的指针，而实例则包含一个原型对象的指针

举个例子

```js
 function Parent() {
    this.name = 'parent1';
    this.play = [1, 2, 3]
  }
  function Child() {
    this.type = 'child2';
  }
  Child.prototype = new Parent();
  console.log(new Child())
```

上面代码看似没问题，实际存在潜在问题

```js
var s1 = new Child();
var s2 = new Child();
s1.play.push(4);
console.log(s1.play, s2.play); // [1,2,3,4]
```

改变`s1`的`play`属性，会发现`s2`也跟着发生变化了，这是因为两个实例使用的是同一个原型对象，内存空间是共享的

### 构造函数继承

借助 `call`调用`Parent`函数

```js
function Parent(){
    this.name = 'parent1';
}

Parent.prototype.getName = function () {
    return this.name;
}

function Child(){
    Parent1.call(this);
    this.type = 'child'
}

let child = new Child();
console.log(child);  // 没问题
console.log(child.getName());  // 会报错
```

可以看到，父类原型对象中一旦存在父类之前自己定义的方法，那么子类将无法继承这些方法

相比第一种原型链继承方式，父类的引用属性不会被共享，优化了第一种继承方式的弊端，但是只能继承父类的实例属性和方法，不能继承原型属性或者方法

### 组合继承

前面我们讲到两种继承方式，各有优缺点。组合继承则将前两种方式继承起来

```js
function Parent3 () {
    this.name = 'parent3';
    this.play = [1, 2, 3];
}

Parent3.prototype.getName = function () {
    return this.name;
}
function Child3() {
    // 第二次调用 Parent3()
    Parent3.call(this);
    this.type = 'child3';
}

// 第一次调用 Parent3()
Child3.prototype = new Parent3();
// 手动挂上构造器，指向自己的构造函数
Child3.prototype.constructor = Child3;
var s3 = new Child3();
var s4 = new Child3();
s3.play.push(4);
console.log(s3.play, s4.play);  // 不互相影响
console.log(s3.getName()); // 正常输出'parent3'
console.log(s4.getName()); // 正常输出'parent3'
```

这种方式看起来就没什么问题，方式一和方式二的问题都解决了，但是从上面代码我们也可以看到`Parent3` 执行了两次，造成了多构造一次的性能开销

### 原型式继承

这里主要借助`Object.create`方法实现普通对象的继承

同样举个例子

```js
let parent4 = {
    name: "parent4",
    friends: ["p1", "p2", "p3"],
    getName: function() {
      return this.name;
    }
  };

  let person4 = Object.create(parent4);
  person4.name = "tom";
  person4.friends.push("jerry");

  let person5 = Object.create(parent4);
  person5.friends.push("lucy");

  console.log(person4.name); // tom
  console.log(person4.name === person4.getName()); // true
  console.log(person5.name); // parent4
  console.log(person4.friends); // ["p1", "p2", "p3","jerry","lucy"]
  console.log(person5.friends); // ["p1", "p2", "p3","jerry","lucy"]
```

这种继承方式的缺点也很明显，因为`Object.create`方法实现的是浅拷贝，多个实例的引用类型属性指向相同的内存，存在篡改的可能

### 寄生式继承

### 寄生式继承

寄生式继承在上面继承基础上进行优化，利用这个浅拷贝的能力再进行增强，添加一些方法

```js
let parent5 = {
    name: "parent5",
    friends: ["p1", "p2", "p3"],
    getName: function() {
        return this.name;
    }
};

function clone(original) {
    let clone = Object.create(original);
    clone.getFriends = function() {
        return this.friends;
    };
    return clone;
}

let person5 = clone(parent5);

console.log(person5.getName()); // parent5
console.log(person5.getFriends()); // ["p1", "p2", "p3"]
```

其优缺点也很明显，跟上面讲的原型式继承一样

### 寄生组合式继承

寄生组合式继承，借助解决普通对象的继承问题的`Object.create` 方法，在几种继承方式的优缺点基础上进行改造，这也是所有继承方式里面相对最优的继承方式

```js
function clone (parent, child) {
    // 这里改用 Object.create 就可以减少组合继承中多进行一次构造的过程
    child.prototype = Object.create(parent.prototype);
    child.prototype.constructor = child;
}

function Parent6() {
    this.name = 'parent6';
    this.play = [1, 2, 3];
}
Parent6.prototype.getName = function () {
    return this.name;
}
function Child6() {
    Parent6.call(this);
    this.friends = 'child5';
}

clone(Parent6, Child6);

Child6.prototype.getFriends = function () {
    return this.friends;
}

let person6 = new Child6(); 
console.log(person6); //{friends:"child5",name:"child5",play:[1,2,3],__proto__:Parent6}
console.log(person6.getName()); // parent6
console.log(person6.getFriends()); // child5
```

可以看到 person6 打印出来的结果，属性都得到了继承，方法也没问题

文章一开头，我们是使用`ES6` 中的`extends`关键字直接实现 `JavaScript`的继承

```js
class Person {
  constructor(name) {
    this.name = name
  }
  // 原型方法
  // 即 Person.prototype.getName = function() { }
  // 下面可以简写为 getName() {...}
  getName = function () {
    console.log('Person:', this.name)
  }
}
class Gamer extends Person {
  constructor(name, age) {
    // 子类中存在构造函数，则需要在使用“this”之前首先调用 super()。
    super(name)
    this.age = age
  }
}
const asuna = new Gamer('Asuna', 20)
asuna.getName() // 成功访问到父类的方法
```

利用`babel`工具进行转换，我们会发现`extends`实际采用的也是寄生组合继承方式，因此也证明了这种方式是较优的解决继承的方式

## 总结

下面以一张图作为总结：

![image-20220522224331767](/img/image/image-20220522224331767.png)

通过`Object.create` 来划分不同的继承方式，最后的寄生式组合继承方式是通过组合继承改造之后的最优继承方式，而 `extends` 的语法糖和寄生组合继承的方式基本类似
