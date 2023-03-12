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

基本类型：Number Boolean  String  undefined  null

引用类型：Object（包括 Object 、Array 、Function) 
```

## null 和 undefined 

**undefined** 表示未定义，就是应该有值但是还没有赋值，连null的值都没有赋予

**null** 代表空值，空引用。

**undefined**

* 变量被声明了，但没有赋值时，就等于undefined。
* 调用函数时，应该提供的参数没有提供，该参数等于undefined。
* 对象没有赋值的属性，该属性的值为undefined。
* 函数没有返回值时，默认返回undefined。

**null**

* 作为对象原型链的终点出现；

* 当我们访问一个不存在的dom节点的时候。

null 和 undefined 虽然值的结果是相等的，但是其所代表的语义是完全不一样的（==是相等的）。

undefined 代表了某个变量完全不存在，在内存中完全能不存在这个标识符所指向的地址；

null 代表了内存中是存在这个变量的，只是在某些情况下需要把这个变量原本的值给覆盖了，将它设置为一个空。

null 转为数值是 0 ; undefined 转为数值是 NAN（not a number）。

null 通过 typeof 判断类型的时候结果的输出是 object ; 而 undefined 的类型是 undefined 。

> null 和 undefined 都是js语言的基础数据类型, 都是原始值类型，但是 typeof null 是 object ，是因为不同的对象在底层都表现为二进制，在  JavaScript  中二进制前三位都为 0 的话会被判断为 object  类型，null 的二进制全部都为 0 ，前三位自然也是 0 ，所以执行 typeof 值会返回 object 。
>
> `ES6`之后引入两种新数据类型`Symbol`与`bigInt`。

## 判断类型

* **typeof**：检测变量数据类型的操作符，主要用来检测基本数据类型

  它可以判断一个变量是字符串、数值、布尔值还是undefined，有个例外null返回object，但是如果检测的变量是引用数据类型，返回object或者function，但不能细致地分出是array还是RegExp。

  ```js
  var str="nihao",num=15,arr= [1,2,3],fun = function(){alert(2)},n = null, ;
  
  console.log(typeof num);   //number  
  console.log(typeof str);   //string  
  console.log(typeof arr1);  //object  
  console.log(typeof n);     //object  
  ```

* **instanceof**：主要的目的就是检测引用类型,它可以判断出对象是Array还是RegExp。

  instanceof运算符只能用于对象（纯对象和数组），不适用原始类型（Undefined、Null、Boolean、Number 和 String）的值。

  instanceof的作用：会检查右边构造函数的原型对象（prototype），是否在左边对象的原型链上。

  ```js
  var a = {};
  var b = [];
  var c = function () {};
  console.log(a instanceof Object) //true
  console.log(b instanceof Object) //true
  console.log(c instanceof Object) //true
  
  console.log(b instanceof Array) //true
  console.log(c instanceof Function) //true
  ```

  ```js
  //所以可用于检测实例是否是由某个构造函数而来的
  function Ninja(){}
  const ninja = new Ninja();
  
  ninja instanceof Ninja;   //true，其中原理就是instanceof会检查右边构造函数的原型对象（prototype），是否在左边对象的原型链上。
  // 等同于
  Ninja.prototype.isPrototypeOf(ninja)
  ```

  Object.prototype.isPrototypeOf的含义如下：

  ```js
  var p = {x:1};//定义一个原型对象
  var o = Object.create(p);//使用这个原型创建一个对象
  p.isPrototypeOf(o);//=>true：o继承p
  Object.prototype.isPrototypeOf(p);//=> true p继承自Object.prototype
  ```

  由于instanceof检查整个原型链，因此同一个实例对象，可能会对多个构造函数都返回true。

* Object.prototype.toString.call()

  由于 JavaScript 中一切都是对象，任何都不例外，对所有值类型应用 Object.prototype.toString.call() 方法结果

  ```js
  console.log(Object.prototype.toString.call(123)) //[object Number]
  console.log(Object.prototype.toString.call('123')) //[object String]
  console.log(Object.prototype.toString.call(undefined)) //[object Undefined]
  console.log(Object.prototype.toString.call(true)) //[object Boolean]
  console.log(Object.prototype.toString.call({})) //[object Object]
  console.log(Object.prototype.toString.call([])) //[object Array]
  console.log(Object.prototype.toString.call(function(){})) //[object Function]
  ```

> instanceof的原理是检查右边构造函数的prototype属性，是否在左边对象的原型链上。有一种特殊情况，就是左边对象的原型链上，只有null对象。这时，instanceof判断会**失真**。
>
> ```js
> var obj = Object.create(null);
> typeof obj // "object"
> Object.create(null) instanceof Object // false
> ```
>
> 上面代码中，Object.create(null)返回一个新对象obj，它的原型是null（Object.create的详细介绍见后文）。右边的构造函数Object的prototype属性，不在左边的原型链上，因此instanceof就认为obj不是Object的实例。但是，只要一个对象的原型不是null，instanceof运算符的判断就不会失真

## “ ===”、“ ==”

1、 "\==“叫做相等运算符，”\=\=="叫做严格运算符。
2、 \==，等同的意思，两边值类型不同的时候，要先进行**类型转换**为同一类型后，再比较值是否相等。
      \=\==，恒等的意思，不做类型转换，类型不同的结果一定不等。
3、 "\==“表示只要值相等即可为真，而”==="则要求不仅值相等，而且也要求类型相同。

```js
// JS尝试转换字符串’chuichui’，但是该字符串并不能转换为数字，这时候转换的结果就为NaN
99 == “chuichui” 
↓↓↓↓↓↓
99 == NaN // false
// JS在布尔值和其他类型比较时，都是把布尔值转换为数字
1 == true
↓↓↓↓↓↓
1 == 1
// null和undefined比较 只不过一个是没有值得变量，一个是没有值的对象，因此他们相似
null == undefined //True

// 空字符串""转换为0
false == ""
↓↓↓↓↓↓
0==0// ture false转化为0,空字符串””转换为0
```

```js
三等号===:两个都是数值，并且是同一个值，那么相等；如果其中至少一个是NaN，那么不相等。（判断一个值是否是NaN，只能使用isNaN( ) 来判断）
```

| 表达式               | 结果   | 原因                                                         |
| -------------------- | ------ | ------------------------------------------------------------ |
| undefined == null    | 真     | （发生转换）0==0                                             |
| NaN == NaN           | 假     | 两个NaN永远不会相等                                          |
| isNaN("100")         | 假     | （发生转换）"100"=>100=>假                                   |
| isNaN(null)          | 假     | （发生转换）null=>0=>假                                      |
| parseInt("1a") === 1 | 真     | （发生转换）"1a"=>1=>真                                      |
| [] instanceof Array  | 真     |                                                              |
| typeot []或 unll     | Object | 基本数据类型我行，引用数据类型摆烂，都是obj                  |
| +new Array(017)      | NaN    | +是一元运算符无运算效果，但是可以将字符串等转为number类型，017其实是八进制 |
| alert（2<1<3）       | true   | 2<1会显示false，而false在js中会被转换为0，后面等于0<3,于是true |

# 正则表达式

正则表达式（英语：Regular Expression，在代码中常简写为regex、regexp或RE）使用单个字符串来描述、匹配一系列符合某个句法规则的字符串搜索模式。

* 正则表达式是由一个字符序列形成的搜索模式。
* 当你在文本中搜索数据时，你可以用搜索模式来描述你要查询的内容。
* 正则表达式可以是一个简单的字符，或一个更复杂的模式。
* 正则表达式可用于所有文本搜索和文本替换的操作。

```kotlin
/正则表达式主体/修饰符(可选)

var patt = /runoob/i
runoob  是一个正则表达式主体 (用于检索)。
i  是一个修饰符 (搜索不区分大小写)。
```

* **search()** 方法用于检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串，并返回子串的起始位置。

  ```kotlin
  // 使用正则表达式搜索 "Runoob" 字符串，且不区分大小写
  var str = "Visit Runoob!"; 
  var n = str.search(/Runoob/i); // 6
  // search 方法可使用字符串作为参数。字符串参数会转换为正则表达式
  var str = "Visit Runoob!"; 
  var n = str.search("Runoob");// 6
  ```

* **replace()** 方法用于在字符串中用一些字符串替换另一些字符串，或替换一个与正则表达式匹配的子串。

  ```kotlin
  //使用正则表达式且不区分大小写将字符串中的 Microsoft 替换为 Runoob 
  var str = document.getElementById("demo").innerHTML; 
  var txt = str.replace(/microsoft/i,"Runoob");
  // replace() 方法也接收字符串作为参数：
  ```

## 正则表达式修饰符

**修饰符** 可以在全局搜索中不区分大小写:

| i    | 执行对大小写不敏感的匹配。                                 |
| ---- | ---------------------------------------------------------- |
| g    | 执行全局匹配（查找所有匹配，而非在找到第一个匹配后停止）。 |
| m    | 执行多行匹配。                                             |

## 正则表达式模式

* 方括号用于查找某个范围内的字符：

  ```kotlin
  [abc]	查找方括号之间的任何字符;
  [^abc]	查找任何不在方括号之间的字符；
  [a-z]	查找任何从小写 a 到小写 z 的字符；
  [A-Z]	查找任何从大写 A 到大写 Z 的字符；
  [A-z]	查找任何从大写 A 到小写 z 的字符；
  [0-9]	查找任何从 0 至 9 的数字；
  [adgk]	查找给定集合内的任何字符。
  [^adgk]	查找给定集合外的任何字符。
  (red|blue|green)	查找任何指定的选项；
  ```

* 元字符是拥有特殊含义的字符

  ```kotlin
  .	查找单个字符，除了换行和行结束符；
  \w	查找数字、字母及下划线
  \W	查找非单词字符；
  \d	查找数字;
  \D	查找非数字字符；
  \s	查找空白字符
  \S	查找非空白字符;
  \b	匹配单词边界;
  \B	匹配非单词边界；
  \0	查找 NULL 字符；
  \n	查找换行符；
  \f	查找换页符；
  \r	查找回车符；
  \t	查找制表符；
  \v	查找垂直制表符；
  \xxx	查找以八进制数 xxx 规定的字符
  \xdd	查找以十六进制数 dd 规定的字符
  \uxxxx	查找以十六进制数 xxxx 规定的 Unicode 字符;
  ```

* 量词

  ```kotlin
  n+	匹配任何包含至少一个 n 的字符串;
  n*	匹配任何包含零个或多个 n 的字符串;
  n?	匹配任何包含零个或一个 n 的字符串;
  n{X}	匹配包含 X 个 n 的序列的字符串；
  n{X,}	X 是一个正整数。前面的模式 n 连续出现至少 X 次时匹配；
  n{X,Y}	X 和 Y 为正整数。前面的模式 n 连续出现至少 X 次，至多 Y 次时匹配；
  n$	匹配任何结尾为 n 的字符串。
  ^n	匹配任何开头为 n 的字符串。
  ?=n	匹配任何其后紧接指定字符串 n 的字符串。
  ?!n	匹配任何其后没有紧接指定字符串 n 的字符串
  ```

## 使用 RegExp 对象

在 JavaScript 中，RegExp 对象是一个预定义了属性和方法的正则表达式对象。

正则表达式用于对字符串模式匹配及检索替换，是对字符串执行模式匹配的强大工具。

```kotlin
var patt=new RegExp(pattern,modifiers);
或者更简单的方式:
var patt=/pattern/modifiers;
// pattern（模式） 描述了表达式的模式
// modifiers(修饰符) 用于指定全局匹配、区分大小写的匹配和多行匹配
```

> **注意：**当使用构造函数创造正则对象时，需要常规的字符转义规则（在前面加反斜杠 \）。比如，以下是等价的：
>
> ```kotlin
> var re = new RegExp("\\w+");
> var re = /\w+/;
> ```

test() ,exec() 方法是一个正则表达式方法。

test() 方法用于检测一个字符串是否匹配某个模式，如果字符串中含有匹配的文本，则返回 true，否则返回 false。

```kotlin
// 字符串中含有 "e"
var patt = /e/;
patt.test("The best things in life are free!");// true
/e/.test("The best things in life are free!");// true 两行代码可以合并
```

exec() 方法用于检索字符串中的正则表达式的匹配

该函数返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null

```kotlin
// 字符串中含有 "e"
/e/.exec("The best things in life are free!");// e
```

案例

```kotlin
//用户名正则，4到16位（字母，数字，下划线，减号）
var uPattern = /^[a-zA-Z0-9_-]{4,16}$/;
//密码强度正则，最少6位，包括至少1个大写字母，1个小写字母，1个数字，1个特殊字符
var pPattern = /^.*(?=.{6,})(?=.*\d)(?=.*[A-Z])(?=.*[a-z])(?=.*[!@#$%^&*? ]).*$/;
//正整数正则
var posPattern = /^\d+$/;
//负整数正则
var negPattern = /^-\d+$/;
//整数正则
var intPattern = /^-?\d+$/;
//正数正则
var posPattern = /^\d*\.?\d+$/;
//负数正则
var negPattern = /^-\d*\.?\d+$/;
//数字正则
var numPattern = /^-?\d*\.?\d+$/;
//Email正则
var ePattern = /^([A-Za-z0-9_\-\.])+\@([A-Za-z0-9_\-\.])+\.([A-Za-z]{2,4})$/;
//手机号正则
var mPattern = /^1[34578]\d{9}$/;
//身份证号（18位）正则
var cP = /^[1-9]\d{5}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/;
//包含中文正则
var cnPattern = /[\u4E00-\u9FA5]/;
```

# eval()

eval是JS中最强大的方法之一，它就像一个完整的ECMAScript解析器，它会根据ECMAScript语句对字符串进行解析和计算。

eval() 函数把对应的字符串解析成 JS 代码并运行；

**如果参数是一个表达式，eval() 函数将执行表达式。如果参数是Javascript语句，eval()将执行 Javascript 语句（代码）**

该方法只接受原始字符串作为参数，如果 string 参数不是原始字符串，那么该方法将不作任何改变地返回。因此请不要为 eval() 函数传递 String 对象来作为参数。

如果试图覆盖 eval 属性或把 eval() 方法赋予另一个属性，并通过该属性调用它，则 [ECMAScript](https://so.csdn.net/so/search?q=ECMAScript&spm=1001.2101.3001.7020) 实现允许抛出一个 EvalError 异常。

虽然 eval() 的功能非常强大，但在实际使用中用到它的情况并不多:

* 如果参数中没有合法的表达式和语句，则抛出 SyntaxError 异常。
* 如果非法调用 eval()，则抛出 EvalError 异常。
* 如果传递给 eval() 的 Javascript 代码生成了一个异常，eval() 将把该异常传递给调用者。
* 不安全，非常耗性能（2次，一次解析成 js 语句，一次执行）。

```js
eval("x=10;y=20;console.log(x*y)"); // 200
console.log(eval("2+2")); // 4
console.log(eval(x+17)); // 27
console.log(eval(18)); // 18
// 相当于直接调用console.log
let msg = "hello world";
eval("console.log(msg)"); // hello world
// 相当于函数声明
eval("function sayHi() { console.log('hi'); }");

const obj = {
  a: {
    b: {
      c: 1,
    }
  }
}
function get(str) {
  console.log(eval(str)) // 1，相当于console.log(obj.a.b.c)
}
get('obj.a.b.c')
```

## eval作用域

eval()函数并不会创建一个新的作用域，并且它的作用域就是它所在的作用域。这在所有主流浏览器都是如此，但是有时候需要将eval()函数的作用域设置为全局，当然可以将eval()在全局作用域中使用。

```js
function a(){
  eval( "var x=1" )
  console.log(x) // 1
}
a()
console.log(x) // 报错，x is not defined
```

需要在局部作用域使用具有全局作用域的此函数，这个时候可以用window.eval()的方式实现：

```js
function a(){
  window.eval( "var x=1" )
  console.log(x) // 1
}
a()
console.log(x) // 1
```



# 页面渲染

## JavaScript 是单线程的，浏览器是多进程的

* 每打开一个新网页就会创建一个渲染进程
* 渲染进程是多线程的
* 负责页面渲染的 GUI 渲染线程
* 负责JavaScript的执行的 JavaScript 引擎线程，
* 负责浏览器事件循环的事件触发线程，注意这不归 JavaScript 引擎线程管
* 负责定时器的定时触发器线程，setTimeout 中低于 4ms 的时间间隔算为4ms
* 负责XMLHttpRequest的异步 http 请求线程
* GUI 渲染线程与 JavaScript 引擎线程是互斥的
* 单线程JavaScript是因为避免 DOM 渲染的冲突，web worker 支持多线程，但是 web worker 不能访问 window 对象，document 对象等。

## 常见的浏览器内核

主要分成两部分：渲染引擎(layout engineer或Rendering Engine)和JS引擎

* 渲染引擎：负责取得网页的内容（HTML、XML、图像等等）、整理讯息（例如加入CSS等），以及计算网页的显示方式，然后会输出至显示器或打印机。浏览器的内核的不同对于网页的语法解释会有不同，所以渲染的效果也不相同。所有网页浏览器、电子邮件客户端以及其它需要编辑、显示网络内容的应用程序都需要内核。
* JS引擎则：解析和执行javascript来实现网页的动态效果。

最开始渲染引擎和JS引擎并没有区分的很明确，后来JS引擎越来越独立，内核就倾向于只指渲染引擎。

* 常见内核
  * Trident 内核：IE, MaxThon, TT, The World, 360, 搜狗浏览器等。[又称 MSHTML]、
  * Gecko 内核：Netscape6 及以上版本，FF, MozillaSuite / SeaMonkey 等
  * Presto 内核：Opera7 及以上。 [Opera内核原为：Presto，现为：Blink;]
  * Webkit 内核：Safari, Chrome等。 [ Chrome的：Blink（WebKit 的分支）]

##  网页从输入网址到渲染完成经历

大致可以分为如下7步：

* 输入网址；
* 发送到DNS服务器，并获取域名对应的web服务器对应的ip地址；
* 与web服务器建立TCP连接；
* 浏览器向web服务器发送http请求；
* web服务器响应请求，并返回指定url的数据（或错误信息，或重定向的新的url地址）；
* 浏览器下载web服务器返回的数据及解析html源文件；
* 生成DOM树，解析css和js，渲染页面，直至显示完成；

## 浏览器渲染页面流程

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

##  网页前端性能优化

1. 压缩 css, js, 图片

2. 减少 http 请求次数， 合并 css、js 、合并图片（雪碧图）

3. 使用 CDN

4. 减少 dom 元素数量

5. 图片懒加载

6. 静态资源另外用无 cookie 的域名

7. 减少 dom 的访问（缓存 dom）

8. 巧用事件委托

9. 样式表置顶、脚本置低

## 移动端性能优化

* 尽量使用css3动画，开启硬件加速
* 适当使用touch时间代替click时间
* 避免使用css3渐变阴影效果
* 可以用transform: translateZ(0) 来开启硬件加速
* 不滥用float。float在渲染时计算量比较大，尽量减少使用
* 不滥用web字体。web字体需要下载，解析，重绘当前页面
* 合理使用requestAnimationFrame动画代替setTimeout
* css中的属性（css3 transitions、css3 3D transforms、opacity、webGL、video）会触发GUP渲染，耗电

# TCP 传输的三次握手、四次挥手策略

## 三次握手

为了准确无误地吧数据送达目标处，TCP协议采用了三次握手策略。用TCP协议把数据包送出去后，TCP不会对传送后的情况置之不理，一定会向对方确认是否送达，握手过程中使用TCP的标志：SYN（同步序列编号）和ACK（确认字符）

1. 发送端首先发送一个带SYN的标志的数据包给对方
2. 接收端收到后，回传一个带有SYN/ACK标志的数据包以示传达确认信息
3. 最后，发送端再回传一个带ACK的标志的数据包，代表“握手”结束

> 如在握手过程中某个阶段莫明中断，TCP协议会再次以相同的顺序发送相同的数据包

## 四次挥手

断开一个TCP连接需要“四次挥手”

由于TCP连接是全双工的，因此每个方向都必须单独进行关闭。这原则是当一方完成它的数据发送任务后就能发送一个FIN来终止这个方向的连接。收到一个 FIN只意味着这一方向上没有数据流动，一个TCP连接在收到一个FIN后仍能发送数据。首先进行关闭的一方将执行主动关闭，而另一方执行被动关闭。

挥手过程中使用TCP的标志：FIN(报文)

* 第一次挥手：主动关闭方发送一个FIN，用来关注主动方到被动关闭方的数据传送，也即是主动关闭方告诫被动关闭方：我已经不会再给你发数据了（在FIN包之前发送的数据，如果没有收到对应的ACK确认报文，主动关闭方依然会重发这些数据）。但是，`此时主动关闭方还可以接受数据`
* 第二次挥手：被动关闭方收到FIN包后，发送一个ACK给对方，确认序号收到序号 +1（与SYN相同，一个 FIN占用一个序号）
* 第三次挥手：被动关闭方发送一个 FIN。用来关闭被动关闭方到主动关闭方的数据传送，也就是告诉主动关闭方，我的数据也发送完了及数据处理完毕，不会给你发送数据了
* 第四次挥手：主动关闭方收到FIN后，发送一个ACK给被动关闭方，确认序号为收到序号+1，至此，完成四次挥手

# let、const、var

let和const是ES6新增的声明变量的关键词，之前声明变量的关键词是var。

## let

* var定义的变量，可以预解析提前调用的结果是undefined，let定义的变量不能预解析，提前调用的结果是 报错。

  ```kotlin
  // 提前调用 预解析
  console.log( int1 );
  // 提前调用 结果是报错
  console.log( int2 );
  // var 定义的变量 
  var int1 = 100 ;
  let int2 = 200 ;
  ```

* var定义的变量，变量名称可以重复，效果是重复赋值，let定义的变量不能重复，否则执行报错。

  ```kotlin
  // var 定义的变量 
  var int1 = 100 ;
  let int2 = 200 ;
  // 变量名称重复 重复赋值效果
  var int1 = '北京' ;
  console.log( int1 );
  // 变量名称重复 结果是报错
  let int2 = '上海' ;
  ```

* var定义的变量作用域是全局/局部作用域可以进行变量提升。let定义的变量如果在{}块级作用域内中只能在{}中调用。

  ```kotlin
  // 在 {} 中 使用 let 定义变量 只能在 { } 中 使用
  // 如果需要在 { } 中 对 let 定义的变量 进行操作 
  // 提前定义 变量 在 {} 中 进行赋值操作
  if( true ){
     var a = 300 ;
     let b = 400 ;
     // let 声明的变量 在 { } 中可以调用
     // 对 {} 外定义的变量 进行赋值操作
     console.log( b );
  }
  console.log( a ) ; // 变量提升
  // let 声明的变量 在 { } 外 不能调用 
  console.log( b );
  ```

* 在循环语句中var定义的循环变量和使用let定义的循环变量。执行原理和执行效果不同。

  * **var声明的循环变量**

    在整个循环变量过程中只定义了一个循环变量i，每次循环都对这一个循环变量i进行重复赋值，也就是之后的循环变量数值会覆盖之前的循环变量数值，**当循环结束后只有一个循环变量i，存储的是最终的循环变量数值**。

  * **let声明的循环变量**

    在整个循环过程中每次循环都相当于触发执行了一个{   }，每一个{   }对于let定义的变量就是一个独立的作用域，也就是每次循环let声明的循环变量都是一个人独立作用域中的循环变量，每一次循环中循环变量都会存储不同的数据数值，互相之间不会影响，不会覆盖，**也就是每次循环let声明的循环变量都相当于是一个独立的变量，不会覆盖之前的数据数值。**

## const

* var定义的变量，可以预解析提前调用的结果是undefined，const定义的变量不能预解析，提前调用的结果是报错。
* var定义的变量，变量名称可以重复，效果是重复赋值，const定义的变量不能重复，否则执行报错。
* var定义的变量作用域是全局/局部作用域。const定义的变量如果在{}中只能在{}中调用。
* const 定义的变量存储的数据数值不能改变，也就是const定义的变量，不能重复赋值。

## 事件的绑定

通过for循环给标签绑定事件，也就是一打开执行界面，事件绑定就结束了，也就是 循环已经结束了，也就是触发事件时循环已经结束了。

```kotlin
 <ul>
        <li>我是第一个li</li>
        <li>我是第二个li</li>
        <li>我是第三个li</li>
        <li>我是第四个li</li>
        <li>我是第五个li</li>
    </ul>
 
    <script>
        // 给 li 绑定事件 点击 li标签 弹出 索引下标
 
        // 获取标签对象
        const oLis = document.querySelectorAll('ul>li');
 
        // 通过 for循环 给 li标签 绑定事件
        for( var i = 0 ; i <= oLis.length -1 ; i++ ){
            // i 是 索引下标 oLis[i] 是 li标签对象
            oLis[i].addEventListener( 'click' , function(){
                
                // 点击时输出索引下标
                console.log( `我是var循环的i ${i}` );
            })
        }
        for( let j = 0 ; j <= oLis.length -1 ; j++ ){
            // i 是 索引下标 oLis[i] 是 li标签对象
            oLis[j].addEventListener( 'click' , function(){
                
                // 点击时输出索引下标
                console.log( `我是let循环的i ${j}` );
            })
        }
 
    </script>
```

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

* 所有函数拥有prototype属性（显式原型）（仅限函数）， 等于原型对象(就是一个普通对象包含公共属性);

  对象具有 constructor 属性，指向构造函数（Person.prototype.constructor == Person）

* 所有引用类型的\__proto\__属性指向它构造函数的prototype;

  ```js
  const obj = {}
  obj.__proto__ === Object.prototype // true
  ```

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

<img src="/img/image/image-20230209201628780.png" alt="image-20230209201628780" style="zoom:80%;" />

![image-20230208214004851](/img/image/image-20230208214004851.png)

JavaScript对象是通过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本。当我们修改原型时，与之相关的对象也会继承这一改变。

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

**函数和对其周围状态的引用捆绑在一起构成闭包**。也就是说，闭包可以让你从内部函数访问外部函数作用域。在 JavaScript 中，每当函数被创建，就会在函数生成时生成闭包。

- 闭包是指有权访问另一个函数作用域中的变量的函数，创建闭包常见方式，就是在一个函数的内部创建另一个函数
- 使用闭包主要为了设计私有的方法和变量，闭包的优点是可以避免变量的污染，缺点是闭包会常驻内存，会增大内存使用量，使用不当很容易造成内存泄露。在js中，函数即闭包，只有函数才会产生作用域的概念。
- 闭包有三个特性：
  - 函数嵌套函数
  - 函数内部可以引用外部的参数和变量
  - 参数和变量不会被垃圾回收机制回收
- 闭包的缺点就是常驻内存，会增大内存使用量，使用不当会造成内存泄漏

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

# javascript的内存(垃圾)回收机制

- 垃圾回收器会每隔一段时间找出那些不再使用的内存，然后为其释放内存

- 一般使用**标记清除方法(mark and sweep)**, 当变量进入环境标记为进入环境，离开环境标记为离开环境

  垃圾回收器会在运行的时候给存储在内存中的所有变量加上标记，然后去掉环境中的变量以及被环境中变量所引用的变量（闭包），在这些完成之后仍存在标记的就是要删除的变量了

- 还有**引用计数方法(reference counting)**, 在低版本IE中经常会出现内存泄露，很多时候就是因为其采用引用计数方式进行垃圾回收。

  策略是跟踪记录每个值被使用的次数，当声明了一个 变量并将一个引用类型赋值给该变量的时候这个值的引用次数就加1，如果该变量的值变成了另外一个，则这个值得引用次数减1，当这个值的引用次数变为0的时 候，说明没有变量在使用，这个值没法被访问了，因此可以将其占用的空间回收，这样垃圾回收器会在运行的时候清理掉引用次数为0的值占用的空间。

- 在IE中虽然JavaScript对象通过标记清除的方式进行垃圾回收，但BOM与DOM对象却是通过引用计数回收垃圾的， 也就是说只要涉及BOM及DOM就会出现循环引用问题。

# Cookie、session和localStorage、sessionStorage

在实际开发中前端展示的数据大都是通过`http`网络请求后后台服务器返回的数据，所以这就引出了`Cookies、Session、SessionStore、LocalStore`这几个浏览器的缓存机制，来保存必要的数据，这样就大大的减少了请求次数！

# js事件队列

事件队列可以分为微任务（micro task）队列和宏任务（macro task）队列。

**微任务一般比宏任务先执行，并且微任务队列只有一个，宏任务队列可能有多个**。另外我们常见的点击和键盘等事件也属于宏任务。

常见宏任务：

* setTimeout()
* setInterval()
* setImmediate()

常见微任务：

* promise.then()、promise.catch()
* new MutaionObserver()
* process.nextTick()

## 区别

- **宏任务特征**：有明确的异步任务需要执行和回调；需要其他异步线程支持。
- **微任务特征**：没有明确的异步任务需要执行，只有回调；不需要其他异步线程支持。

```js
setTimeout(function () {
    console.log("1");
}, 0);
async function async1() {
    console.log("2");
    const data = await async2();
    console.log("3");
    return data;
}
async function async2() {
    return new Promise((resolve) => {
        console.log("4");
        resolve("async2的结果");
    }).then((data) => {
        console.log("5");
        return data;
    });
}
async1().then((data) => {
    console.log("6");
    console.log(data);
});
new Promise(function (resolve) {
    console.log("7");
  resolve()
}).then(function () {
    console.log("8");
});
// 2 4 7 5 8 3 6 async2的结果 1
```

## async/await

async 是一个通过异步执行并隐式返回 Promise 作为结果的函数。是Generator函数的语法糖，并对Generator函数进行了改进。

* 内置执行器，无需手动执行 next() 方法。
* 更好的语义
* 更广的适用性：co模块约定，yield命令后面只能是 Thunk 函数或 Promise 对象，而async函数的await命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）。
* 返回值是 Promise，比 Generator 函数返回的 Iterator 对象方便，可以直接使用 then() 方法进行调用。
* async 隐式返回 Promise 作为结果的函数，那么可以简单理解为，await后面的函数执行完毕时，await会产生一个微任务(Promise.then是微任务)。

# http协议

## 概述

* http（超文本传输协议）是一个基于请求与响应模式的、无状态的、应用层的协议，常基于TCP的连接方式
* http请求由三部分组成，分别是：请求行、消息报头、请求正文
* HTTP消息报头包括普通报头、请求报头、响应报头、实体报头。

HTTP分为 **请求Request** 和 **响应Response**，如图：

<img src="../../../../../Download/Typora/image/image-20230312123507240.png" alt="image-20230312123507240" style="zoom:60%;" />

## Request 

| Request Header      | 规定                                                         |
| ------------------- | ------------------------------------------------------------ |
| Accept              | 浏览器端接受的格式                                           |
| Accept-Encoding     | 浏览器端接收的编码方式                                       |
| Accept-Language     | 浏览器端接受的语言，用于服务端判断多语言                     |
| Cache-Control       | 控制缓存的时效性                                             |
| Connection          | 连接方式，如果是keep-alive， 且服务端支持，则会复用连接      |
| Host                | HTTP访问使用的域名                                           |
| If- Modified- Since | 上次访问时的更改时间，如果服务端认为此时间后自己没有更新，则会给出304响应 |
| If-None -Match      | 上次访问时使用的E-Tag，通常是页面的信息摘要，这个比更改时间更准确一些。 |
| User-Agent          | 客户端标识，因为一些历史原因，这是一笔糊涂账，多数浏览器的这个字段都十分复杂，区别十分微妙 |
| Cookie              | 客户端存储的cookie字符串                                     |

**Body**

一般请求体就是以下4种格式

- application/json
- application/x-www-form-urlencoded
- mutipart/form-data
- text/xml

```js
GET / HTTP/1.1
Host: time.geekbang.org
```

## Response

| Response Header   | 规定                                                         |
| ----------------- | ------------------------------------------------------------ |
| Cache-Control     | 缓存控制，用于通知各级缓存保存的时间，例如max-age=0， 表示不要缓存。 |
| Connection        | 连接类型，Keep-Alive表示复用连接                             |
| Content- Encoding | 内容编码方式， 通常是gzip                                    |
| Content-Length    | 内容的长度，有利于浏览器判断内容是否已经结束                 |
| Content-Type      | 内容类型，所有请求网页的都是text/html                        |
| Date              | 当前的服务器日期                                             |
| ETag              | 页面的信息摘要，用于判断是否需要重新到服务端取回页面         |
| Expires           | 过期时间，用于判断下次请求是否需要到服务端取回页面           |
| Keep-Alive        | 保持连接不断时需要的一些信息，如timeout=5, max=100           |
| Last-Modified     | 页面上次修改的时间                                           |
| Server            | 服务端软件的类型                                             |
| Set-Cookie        | 设置cookie，可以存在多个                                     |
| Via               | 服务端的请求链路，对一些调试场景至关重要的一个头             |

**Body:**

一般响应体就是HTML、JSON 或者 二进制多媒体数据

```js
HTTP/1.1 301 Moved Permanently
Date: Fri, 25 Jan 2019 13:28:12 GMT
Content-Type: text/html
Content-Length: 182
Connection: keep-alive
Location: https://time.geekbang.org/
Strict-Transport-Security: max-age=15768000

<html>
<head><title>301 Moved Permanently</title></head>
<body bgcolor="white">
<center><h1>301 Moved Permanently</h1></center>
<hr><center>openresty</center>
</body>
</html>
```

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

> GET与POST请求方式的区别:
>
> GET/POST都是TCP链接
>
> `GET`比`POST`不安全，因为`GET`提交的数据放在`URL`中，`POST`则不是(`POST`也不安全，因为`HTTP`是明文传输抓包就能获取数据内容，要想安全还得加密)。GET参数通过URL传递，POST放在Request body中。
>
> GET在浏览器回退时是无害的，而POST会再次提交请求(`GET`有缓存中拿结果，`POST`没缓存);
>
> GET请求会被浏览器主动缓存，而POST不会，除非手动设置;
>
> GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
>
> GET请求只能进行url编码，而POST支持多种编码方式;GET请求在URL中传送的参数是有长度限制的，而POST没有；
>
> GET和POST还有一个重大区别，简单的说：GET产生一个TCP数据包，POST产生两个TCP数据包。换句话说：对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200(返回数据)，而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok(返回数据)。也就是网络请求的两次握手，我记得在哪见过解释
>
> 因为POST需要两步，时间上消耗的要多一点，看起来GET比POST更有效。因此Yahoo团队有推荐用GET替换POST来优化网站性能。但这是一个坑!跳入需谨慎。为什么?
>
> 1. GET与POST都有自己的语义，不能随便混用。
>
> 2. 据研究，在网络环境好的情况下，发一次包的时间和发两次包的时间差别基本可以无视。而在网络环境差的情况下，两次包的TCP在验证数据包完整性上，有非常大的优点。
>
> 3. 并不是所有浏览器都会在POST中发送两次包，Firefox就只发送一次。

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

## HTTPS

- HTTP协议通常承载与 TCP协议之上，在HTTP和TCP之间添加一个安全协议层（SSL或TSL），这个时候，就成了我们常说的HTTPS
- 默认HTTP的端口号为80，HTTPS的端口号为443
- 因为网络请求需要中间有很多的服务器路由的转发，中间的节点都可能篡改信息，而如果使用HTTPS，密钥在你和终点站才有，https之所有说比http安全，是因为他利用ssl/tls协议传输。包含证书，流量转发，负载均衡，页面适配，浏览器适配，refer传递等，保障了传输过程的安全性

# js常用函数

## js中数组操作方法

* join()：将数组中所有元素都转化为字符串并连接在一起；

* reverse()：将数组中的元素颠倒顺序；

* concat()：数组拼接的功能 ,返回新数组，**原数组不受影响**；

* slice()：截取数组生成新数组，**原数组不受影响**；

  返回的数组包含第一个参数指定的位置和所有到但不含第二个参数指定位置之间的所有元素。如果为负数，表示相对于数组中最后一个元素的位置。如果只有一个参数，表示到数组末尾。

  ```jsx
  var aa = [1,2,3,4,5,6];
  console.log(aa.slice(2)); //[3,4,5,6]
  console.log(aa.slice(2,8)); //[3,4,5,6] 超过最大长度，只显示到最后结果
  console.log(aa.slice(2,-1)); //[3,4,5] 相对于倒数第一个之前
  console.log(aa.slice(2,-2)); //[3,4] 相对于倒数第二个之前
  console.log(aa.slice(3)); //[4,5,6] 一个参数从第三个到最后
  console.log(aa.slice(-2));//[5,6] 一个参数负值从倒数第二个到最后
  ```

* splice()：从数组中删除元素、插入元素到数组中或者同时完成这两种操作。

  输入：第一个参数为指定插入或删除的起始位置，第二个参数为要删除的个数。之后的参数表示需要插入到数组中的元素 。如果只有一个参数，默认删除参数后边的所有元素。
   输出：返回一个由删除元素组成的数组。
   **新建了一个数组，并修改了原数组**

  ```jsx
  var aa = [1,2,3,4,5,6];
  console.log(aa.splice(4)); //[5,6]  返回删除后的数组
  aa; // [1,2,3,4]
  console.log(aa.splice(2,2)); //[3,4] 从第二位起删除两个元素
  aa; //[1,2]
  console.log(aa.splice(1,0,7,8)); //[]从第一位起删除0个元素，添加7,8到原数组
  aa;//[1,7,8,2]
  ```

* push()：在数组末尾添加一个或多个元素，并返回新数组长度；

* pop()：从数组末尾删除1个元素(删且只删除1个), 并返回 被删除的元素；

* shift()：在数组开始添加一个或多个元素，并返回新数组长度；

* unshift()：在数组开始删除一个元素(删且只删除1个),并返回 被删除的元素；

* toString()和toLocaleString()：将数组的每个元素转化为字符串，并且输入用逗号分隔的字符串列表。功能类似join();

*  indexOf()和lastIndexOf()：

  indexOf() 两个参数：要查找的项和（可选的）表示查找起点位置的索引。其中， 从数组的开头（位置 0）开始向后查找。没找到返回-1. 返回查找项的索引值
   lastIndexOf() 从数组的末尾开始向前查找。返回查找项的索引值(索引值永远是正序的索引值),没找到返回-1；

* sort()：默认情况下sort()方法没有传比较函数的话，默认按字母升序，如果不是元素不是字符串的话，会调用toString()方法将元素转化为字符串的Unicode(万国码)位点，然后再比较字符。所以用默认方法排序数据是有问题的。

* forEach()：从头至尾遍历数组，为每个元素调用指定函数，输入为一个待遍历函数，函数的参数依次为：数组元素、元素的索引、数组本身

* map()：调用的数组的每一个元素传递给指定的函数，并返回一个新数组 ,不修改原数组。

* filter()：过滤功能，数组中的每一项运行给定函数，返回满足过滤条件组成的数组。

* every()和some()：

  every() 判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true。
  some() 判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true。

* reduce()和reduceRight()

  reduce() 两个参数：函数和递归的初始值。从数组的第一项开始，逐个遍历到最后
  reduceRight() 从数组的最后一项开始，向前遍历到第一项

  ```jsx
  //可以用reduce快速求数组之和
  var arr=[1,2,3,4];
  arr.reduce(function(a,b){
    return a+b;
  }); //10
  ```

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

## valueOf和toString

JavaScript 中的 valueOf() 方法用于返回指定对象的原始值，若对象没有原始值，则将返回对象本身。

```js
console.log([].valueOf() , [].toString()) // [] ,  ''
console.lgo({}.valueOf(). {}.toString()) // {}, [object Object] 
```

# Js 函数内部的this指向

## 函数内部的this指向

 *this 的指向，是当我们调用函数的时候确定的。调用方式的不同决定了this 的指向不同一般指向我们的调用者.*

**函数的不同使用场合，`this`有不同的值。总的来说，`this`就是函数运行时所在的环境对象。**

| 调用方式     | this指向                                 |
| ------------ | ---------------------------------------- |
| 普通函数调用 | window                                   |
| 构造函数调用 | 实例对象原型对象里面的方法也指向实例对象 |
| 对象方法调用 | 该方法所属对象                           |
| 事件绑定方法 | 绑定事件对象                             |
| 定时器函数   | window                                   |
| 立即执行函数 | window                                   |

```kotlin
// 普通函数调用
function f1(){
	console.log(this);
}
f1(); //window

// 构造函数调用
function fn(name,age,sex){
this.name=username,
this.age=userage,
this.sex=sex
console.log(this)
}
var fn1 = new fn('张三',20,'男')//输出结果为：fn {name: "张三", age: 20, sex: "男"}构造函数中的this指向的是new出来的实例化对象

// 对象方法调用
var obj = {
            name: '张三',
            age: 20,
            sex: '男',
            say: function () {
                console.log(this);
            }
        }
        obj.say()//输出结果为：{name: "张三", age: 20, sex: "男", say: ƒ},对象方法调用中的this指向的是该方法所属的对象

// 事件绑定方法
<button id="but">按钮</button>
<script>
$('#but').on('click', function () {
    console.log(this);
})
</script>//输出结果为：<button id=​"but">​按钮​</button>​故：绑定事件方法中的this指向的是绑定事件的对象

// 定时器函数
setInterval(function () {
            console.log(this);
        }, 3000)//输出的结果为：Window,在定时器函数中this的指向是window

// 立即执行函数
(function fn(){
console.log(this)
})()
//输出的结果为：Window ,在立即执行函数中this的指向是window
```

## 改变函数内部 this 指向

### call 方法

call()方法调用一个对象。简单理解为调用函数的方式，但是它可以改变函数的 this 指向

**经常做继承**

```kotlin
var o = {
	name: 'andy'
}
function fn(a, b) {
    console.log(this);
    console.log(a+b)
};
fn(1,2)// 此时的this指向的是window 运行结果为3
fn.call(o,1,2)//此时的this指向的是对象o,参数使用逗号隔开,运行结果为3
```

### apply方法

apply() 方法调用一个函数。简单理解为调用函数的方式，但是它可以改变函数的 this 指向。

**应用场景：经常跟数组有关系**

```kotlin
var o = {
	name: 'andy'
}
 function fn(a, b) {
      console.log(this);
      console.log(a+b)
};
fn(1，2)// 此时的this指向的是window 运行结果为3
fn.apply(o,[1,2])//此时的this指向的是对象o,参数使用数组传递 运行结果为3
```

###  bind方法

bind() 方法不会调用函数,但是能改变函数内部this 指向,返回的是原函数改变this之后产生的新函数。

如果只是想改变 this 指向，并且不想调用这个函数的时候，可以使用 bind() 。

```kotlin
 var o = {
 name: 'andy'
 };
 
function fn(a, b) {
	console.log(this);
	console.log(a + b);
};
var f = fn.bind(o, 1, 2); //此处的f是bind返回的新函数
f();//调用新函数  this指向的是对象o 参数使用逗号隔开
```

### call、apply、bind 三者的异**同**

共同点：都可以改变this指向

不同点：

* call 和 apply 会调用函数, 并且改变函数内部this指向
* call 和 apply传递的参数不一样,call传递参数使用逗号隔开,apply使用数组传递
* bind 不会调用函数, 可以改变函数内部this指向

应用场景：

* call 经常做继承
* apply经常跟数组有关系. 比如借助于数学对象实现数组最大值最小值
* bind 不调用函数,但是还想改变this指向. 比如改变定时器内部的this指向

# 函数

## 立即执行函数

### 声明

声明一个函数，并马上调用这个匿名函数就叫做立即执行函数；也可以说立即执行函数是一种语法，让你的函数在定义以后立即执行；

```kotlin
(function(){alert('我是一个匿名函数')})()
先声明一个匿名函数并用小括号包起来，最后的括号是表示调用此函数
```

我们定义函数之后，立即调用该函数，这时不能在函数的定义后面直接加小括号，这会产生语法错误。产生语法错误的原因是，function 这个关键字，既可以当做语句，也可以当做表达式，比如下边：

```kotlin
//语句
function fn() {};
//表达式
var fn = function (){};
```

为了避免解析上的歧义，JS引擎规定，如果function出现在行首，一律解析成语句。因此JS引擎看到行首是function关键字以后，认为这一段都是函数定义，不应该以小括号结尾，所以就报错了。

解决方法就是不要让function出现在行首，让JS引擎将其理解为一个表达式，最简单的处理就是将其放在一个圆括号里，比如下边：

```kotlin
(function(){
//code
}())

(function (){
//code
})()
```

上边的两种写法，都是以圆括号开头，引擎会意味后面跟的是表达式，而不是一个函数定义语句，所以就避免了错误，这就叫做"立即调用的函数表达式"。

立即执行函数，还有一些其他的写法（加一些小东西，不让解析成语句就可以）

### 作用

不必为函数命名，避免了污染全局变量
立即执行函数内部形成了一个单独的作用域，可以封装一些外部无法读取的私有变量
封装变量
总而言之：立即执行函数会形成一个单独的作用域，我们可以封装一些临时变量或者局部变量，避免污染全局变量

```kotlin
<body>
    <ul id="list">
        <li>公司简介</li>
        <li>联系我们</li>
        <li>营销网络</li>
    </ul>
    <script>
       var list = document.getElementById("list");
      var li = list.children;
      for(var i = 0 ;i<li.length;i++){
        li[i].onclick=function(){
          alert(i);  // 结果总是3.而不是0，1，2
        }
      }
     </script>  
</body>
```

 因为i是贯穿整个作用域的，而不是给每一个li分配一个i,点击事件使异步，用户一定是在for运行完了以后，才点击，此时i已经变成3了。
那么怎么解决这个问题呢，可以用立即执行函数，给每个li创建一个独立的作用域,在立即执行函数执行的时候，i的值从0到2，对应三个立即执行函数，这3个立即执行函数里边的j分别是0，1，2所以就能正常输出了，看下边例子：

```kotlin
<body>
    <ul id="list">
        <li>公司简介</li>
        <li>联系我们</li>
        <li>营销网络</li>
    </ul>
    <script>
       var list = document.getElementById("list");
      var li = list.children;
      for(var i = 0 ;i<li.length;i++){
       ( function(j){
            li[j].onclick = function(){
              alert(j);
          })(i); 把实参i赋值给形参j
        }
      }
     </script>  
</body>
```

也可以使用ES6的块级作用域解决整个问题

```kotlin
<body>
    <ul id="list">
        <li>公司简介</li>
        <li>联系我们</li>
        <li>营销网络</li>
    </ul>
    <script>
       var list = document.getElementById("list");
      var li = list.children;
      for(let i = 0 ;i<li.length;i++){
        li[i].onclick=function(){
          alert(i);  // 结果是0，1，2
        }
      }
     </script>  
</body>
```

立即执行函数的返回值被赋值给了一个变量result，这个函数简单的返回了res的值，这个值事先被计算并被存储在立即执行行数的闭包中。

```js
var result=(function( ){
    var res=2+2;// 私有，临时的变量
    return function(){
        return res ;
    }
})()
console.log(result());//4
```

## 箭头函数定义

箭头函数提供了一种更加简洁的函数书写方式。

基本语法是：

参数 => 函数体
(参数) => {函数体}

> 当箭头函数没有参数或者有多个参数，要用 **()** 括起来。
>
> 当箭头函数函数体有多行语句，用 **{}** 包裹起来，表示代码块，当只有一行语句，并且需要返回结果时，可以省略 **{}** , 结果会自动返回。
>
> 当箭头函数要返回对象的时候，为了区分于代码块，要用 **()** 将对象包裹起来
>
> ```kotlin
> var f = (id,name) => ({id: id, name: name});
> f(6,2);  // {id: 6, name: 2}
> ```

箭头函数没有自己的this，在箭头函数的函数体中使用this时，会取得其上下文中的this。箭头函数调用时并不会生成自身作用域下的this，它只会从自己的作用域链的上一层继承this。由于箭头函数没有自己的this指针，使用apply、call、bind仅能传递参数而不能动态改变箭头函数的this指向。

* **不能作为构造器**

  ```kotlin
  let fn = () => {};
  new fn()    // Uncaught TypeError: fn is not a constructor
  ```

* **不绑定arguments**

  ```kotlin
      let fn1 = (x, y) => {
        console.log(x, y);
        console.log("箭头函数中的arguments：",arguments);// 箭头函数中的arguments长度为0，即未绑定arguments对象
      };
   
      let fn2 = function (x, y) {
        console.log(x, y);
        console.log("匿名函数function()中的arguments：",arguments);
      };
   
      fn1(1, 2);
      fn2(1, 2);
  ```

* **对象的方法使用箭头函数**

  ```kotlin
  //对象方法中使用箭头函数
      let obj1 = {
        name: "leo",
        showName: () => {
          console.log("this：", this);
          console.log("this.name：", this.name);
        },
      };
  //对象方法中使用匿名函数function()
      let obj2 = {
        name: "leo",
        showName: function () {
          console.log("this：", this);
          console.log("this.name：", this.name);
        },
      };
  ```

  箭头函数中的this是当前组件实例（当前上下文对象），不是obj1，由于组件实例name未定义，因此为undefined；而匿名函数function()中的this指向调用该方法的对象，即obj2，因此name为leo。

* **数组方法中使用箭头函数**

  ```kotlin
      let arr = ["leo"]
      arr.forEach(()=>{
        console.log(this)    //在vue中，则this表示当前组件实例
      })
  ```

* **定时器中使用箭头函数**

  ```kotlin
      setTimeout(()=>{
        console.log(this)    //在vue中，则this为当前组件实例
      },100)
   
      setTimeout(function(){
        console.log(this)      //this为Window
      },100)
  ```

* **addEventListener使用箭头函数**

  ```kotlin
  let el = document.getElementById("pId")
  el.addEventListener("click",() => {
    console.log(this)    //在vue中，则this为当前组件实例，而不是el元素
  })
  
  
  el.addEventListener("click", function () {
      console.log(this);      //this为当前el元素
  });
  ```

### **apply、call、bind中使用箭头函数** 

``` kotlin
    let obj1 = {
      name: "leo",
      showName: function () {      //使用匿名函数function()，当前this.name为"leo"
        console.log(this.name);
      },
    };
    let obj2 = {
      name: "lion",
    };
    obj1.showName.apply(obj2);     //可以改变this指向，此时this.name为"lion"，call、bind同理
```

```kotlin
    let obj1 = {
      name: "leo",
      showName: () => {           //使用箭头函数，当前this.name为"undefined"
        console.log(this.name);
      },
    };
    let obj2 = {
      name: "lion",
    };
    obj1.showName.apply(obj2);   //不能改变this指向，this.name仍为"undefined"，call、bind同理
```

**注意**：如果this依赖于当前作用域，使用匿名函数function()形式；如果this取上下文对象（没有自身作用域的this），则使用箭头函数形式。

# js new

## new 的基本用法

1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。
2、属性和方法被加入到 this 引用的对象中。
3、新创建的对象由 this 所引用，并且最后隐式的返回 this 。

```js
var 对象 = new 函数([参数])
```

这里的函数可以是内置构造器，也可以是用户自己定义的函数。

例如：

``` js
var arr = new Array ();
```

此时，arr将可以使用Array.prototype上的全部方法。关于原型链你可以看看之前的文章去介绍原型链。

使用自己定义的构造器:

``` js
function F(name,age){
    this.name = name;
    this.age = age
}
F.prototype.hello = function(){
    console.log(this.name,this.age)
}
var f1 = new F('curry', 30);
console.log(f1)
f1.hello()// curry 30
函数F在被调用的过程中,在前面加new ，所以这个函数是被当作构造器来使用了。f1之所以可以调用 hello方法，也是因为原型链的缘故
```

##  构造器的返回值

一般来讲，如果要把一个函数当做构造函数来使用，在这个函数的内部是不应该去设置返回值的。但是，如果它设置了返回值呢？

* return后面跟着不是对象，就会不管return语句，返回this对象；

  ```kotlin
  var Vehicle = function () {
    this.price = 1000;
    return 1000;
  };
  (new Vehicle()) === 1000// false
  ```

* return后面跟着一个对象，new会返回return语句指定的对象；

  ```kotlin
  var Vehicle = function (){
    this.price = 1000;
    return { price: 2000 };
  };
  (new Vehicle()).price
  // 2000
  ```

## 不加new也能创建对象

```kotlin
function F(name,age){
    this.name = name;
    this.age = age
}
var f1 = F('curry', 30); // 不加new 
console.log(f1)
```

此时，我们把F当作一个普通的函数来调用，由于在函数F内部并没有明确写出return语句，所以f1的值是undefined。同时上面的代码还会有另一个隐藏的后果：在执行F时，由于this的值是指向window，所以上面的代码还会给window对象添加两个属性。如下：

<img src="../../../../../Download/Typora/image/image-20230301114128362.png" alt="image-20230301114128362" style="zoom:50%;" />

为了保证构造函数必须与new命令一起使用，一个解决办法是，构造函数内部使用严格模式，即第一行加上use strict。这样的话，一旦忘了使用new命令，直接调用构造函数就会报错。

```js
function F(name,age){ 
    'use strict'; // 这句新加的
    this.name = name;
    this.age = age
}

var f1 = F('curry', 30); // 不加new
console.log(f1) // Uncaught TypeError: Cannot set property 'name' of undefined 
// 因为在函数内部开启了严格模式之后，函数内部的this将不会默认指向window,它的值会是undefined。
```

可以在构造函数内部判断，当前调用是否使用new命令，如果发现没有使用new，则直接返回一个实例对象。

```js
function F(name,age){ 
  // 如果没有用new，this就不会是F的实例
  if (!(this instanceof F)) {
    return new F(name,age);
  }
  this.name = name;
  this.age = age
}
var f1 = new F('a','30');
var f2 = F('b','30');
console.log(f2)
```

## new 原理

使用new命令时，在构造函数内部依次执行下面的步骤:

1. 创建一个空对象，作为将要返回的对象;
2. 将这个空对象的原型指向构造函数的prototype属性,作用是让这个对象能沿着原型链去使用构造函数中prototype上的方法;
3. 将这个空对象赋值给构造函数内部的this关键字，执行构造函数,让构造器中设置在this上的属性最终设置在这个对象上
4. 返回这个对象

```js
function F(name,age){ 
  this.name = name;
  this.age = age
}
F.prototype.hello = function(){
    console.log(this.name,this.age)
}
var f = new F('a','30');
```

第一步：var obj = {}

第二步：obj.__proto__ = F.prototype

第三步：F.apply(obj,参数)

第四步：return obj

# 线程与进程的区别

* 一个程序至少有一个进程,一个进程至少有一个线程.
* 线程的划分尺度小于进程，使得多线程程序的并发性高。
* 另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。
* 线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。
* 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。
  



