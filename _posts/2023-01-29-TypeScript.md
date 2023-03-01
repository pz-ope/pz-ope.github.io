# 概述

在浏览器中ES语法可以直接运行，但==目前浏览器却不支持==TS，这是就需要使用typescript（es的超级）编译器将其转为js在提交给浏览器运行。

## 变量

* **数据类型：**String、number、boolean、Array、null、undefined、Symbol、object、`tuple元组`、`enum枚举`、`any任意类型`、`never`、`void`

* **变量赋值**

  ```kotlin
  // js中变量声明并赋值~let 变量名 = 值
  let age = 18
  
  // ts中变量声明并赋值~let 变量名:变量类型 = 值
  let age:number = 18
  ```

* **数组**

  *需要声明时指定数组中元素的类型*

  ```kotlin
  //let 数组名:类型[] = [值1,值2】
  let arr:string[] = ['上海','杭州'];
  //let 数组名:Array<类型> = [值1,值2】
  let arr:Array<string> = ['上海','杭州'];
  ```

* **元组**

  规定了元素数量和每个元素类型的集合，而每一个元素的类型，可以不相同。

  ```kotlin
  // let 元组名:[类型1，类型2,类型3]=[值1,值2,值3];
  let tup:[string,number,boolean] = ['上海',18,true];
  ```

* **枚举**

  *枚举类型用于定义数值集合，使用枚举我们可以定义一些带名字的常量。 使用枚举可以清晰地表达意图或创建一组有区别的用例。*

  ```kotlin
  // 声明1
  enum 枚举名{
  枚举项1 =枚举值1,枚举项2 =枚举值2,...
  }
  enum GunType {
  M416 = 1,AK47 = 2,Goza = 3
  }
  
  // 声明2,默认值,枚举值将自动生成从0开始的数值
  enum 枚举名{
  枚举项1,枚举项2,...
  }
  enum GunType {
  M416,AK47,Goza // 0,1,2
  }
  ```

  ```kotlin
  //声明性别枚举
  enum Gender{
  Boy = 1,Girl = 2,Unknown = 3
  }
  
  // 创建用户性别变量
  Let usrSex:Gender = Gender.Boy;
  //判断变量中的性别是否为Boy
  if(usrSex == Gender.Boy){
  console.log(usrSex); // 1
  }else{
  console.log(usrSex); // 2 or 3
  }
  ```

* **any任意类型**

  any代表任意类型，一般在获取dom时使用。

  在接收用户输入或第三方代码库时，还不能确定会返回什么类型的值，此时也可以使用any类型

  ```kotlin
  let txtName:any = document.getElementById("txtN");
  ```

* **void**

  void代表没有类型，一般用在无返回值的函数

  ```kotlin
  // 在ts中函数必须指定返回值类型
  function say():string {
  	return "hello!!!";
  }
  // 使用变量保存返回值
  let re1=say();
  // void类型函数不需要return
  function clap():void{
  	console.log("hi!!!");
  }
  clap();
  ```

* **nerver**

  nerver代表不存在的值的类型，常用作为抛出异常或者无限循环的函数返回类型。

  *never类型是ts中的底部类型，所有类型都是never类型的父类，所以never只能给never赋值。*

  ```kotlin
  function test():never{
  while(true)
  }
  function test2():never{
  throw new Error('讨厌， 死鬼~');
  }
  ```

## 类型推断

如果变量的声明和初始化实在同一行，可以省略掉变量类型的声明。

```kotlin
let 变量名 = 值;
相当于
let 变量名:变量类型 = 值;

```

## 联合类型

表示取值可以为多种类型中的一种。

```kotlin
let 变量名:变量类型1|变量类型2 = 值;
```

```kotlin
// prompt()（确定：返回用户输入的字符串；取消：返回null）
let dName: string | null = prompt('请输入小狗狗名字:');
console.log('hello' + dName );
```

## 类型别名

类型别名用来给一个类型起个新名字。它只是起了一个新名字，并没有创建新类型。类型别名常用于联合类型。

*类型别名type 也可以为属性其新名字*

```TypeScript
type count = number | number[];
function hello(value: count) {}
```

## 类型断言

某些情况下，我们可能比typescript更加清楚的知道某个变量的类型，所以我们可能希望手动指定一个值的类型

类型断言有两种方式：

* **尖括号写法**

  ```tsx
  let str: any = "to be or not to be";
  let strLength: number = (<string>str).length;
  ```

* **as 写法**

  ```ts
  let str: any = "to be or not to be";
  let strLength: number = (str as string).length;
  ```

## 非空断言

在上下文中当类型检查器无法断定类型时，可以使用缀表达式操作符 `!` 进行断言操作对象是非 null 和非 undefined 的类型，**即x!的值不会为 null 或 undefined**

```ts
  let user: string | null | undefined;
  console.log(user!.toUpperCase()); // 编译正确
  console.log(user.toUpperCase()); // 错误
```

## 确定赋值断言

```ts
let value:number
console.log(value); // Variable 'value' is used before being assigned.
```

定义了变量, 没有赋值就使用，则会报错

通过 let x!: number; 确定赋值断言，TypeScript 编译器就会知道该属性会被明确地赋值。

```ts
let value!:number
console.log(value); // undefined 编译正确
```

## 交叉类型

交叉类型就是跟联合类型相反，用`&`操作符表示，交叉类型就是两个类型必须存在

```TypeScript
interface IpersonA{
  name: string,
  age: number
}
interface IpersonB {
  name: string,
  gender: string
}
 
let person: IpersonA & IpersonB = { 
    name: "师爷",
    age: 18,
    gender: "男"
};
```

person 即是 IpersonA 类型，又是 IpersonB 类型

注意：交叉类型取的多个类型的并集，但是如果key相同但是类型不同，则该key为never类型

```ts
interface IpersonA {
    name: string
}
interface IpersonB {
    name: number
}
function testAndFn(params: IpersonA & IpersonB) {
    console.log(params)
}
testAndFn({name: "黄老爷"}) // error TS2322: Type 'string' is not assignable to type 'never'.
```

## 函数

```ts
// 函数返回类型 无则定义为void
function函数名():返回值类型{}
let变量名:变量类型 = 函数名();

// 函数形参类型
function函数名(形参1:类型,形参2:类型):返回值类型{}
let变量名:变量类型 = 函数名(实参1,实参2);

// 可选参数
function函数名(形参1?:类型):返回值类型{}

// 带默认值，自动为可选参数
function函数名(形参1:类型=默认值1,形参2:类型=默认值2):返回值类型{} // 调用时参数1可以传undefined

// 剩余参数
function add(形参1:类型，形参2:类型, ...形参3:类型[] ):void {}

function add(x:number, y:number, ...restOfNum:number[]): void {
	//创建一个求和变量，保存求和结果，将x和y的值求和后存入
	let resNum: number = x + y;
    // 使用for of语法遍历剩余参数数组中每个元素，并累加到求和变量中
    for(let ele of restofNum){
        resNum += ele;
    }
    //将结果打印出来
    console.log( '打印结果: ' + resNum);
}
add(1, 2);
add(1, 2, 3, 4, 5, 6, 7);
```

## 抽象

以abstract开头的类是抽象类， 抽象类和其他类区别不大，只是不能用来创建对象，抽象类就是专门用来被继承的类， 抽象类中可以添加抽象方法.

```typescript
(function () {
    /*
*   以abstract开头的类是抽象类，
*       抽象类和其他类区别不大，只是不能用来创建对象
*       抽象类就是专门用来被继承的类
*
*       抽象类中可以添加抽象方法
* */
    abstract class Animal {
        name: string;
        constructor(name: string) {
            this.name = name;
        }
        // 定义一个抽象方法
        // 抽象方法使用 abstract开头，没有方法体
        // 抽象方法只能定义在抽象类中，子类必须对抽象方法进行重写
        abstract sayHello(): void
        eat() {
            console.log("吃小孩～～～～")
        }
    }
    class Dog extends Animal {
        sayHello(): void {
            console.log('汪汪汪汪！');
        }
    }
    class Cat extends Animal {
        sayHello(): void {
            console.log('喵喵喵喵！');
        }
    }
    const dog = new Dog('旺财')
    const cat = new Cat('布丁')
    dog.sayHello();
 
})()
```

# 类

使用类来批量创建对象， Class 关键字来定义一个类

* **创建对象(构造函数+ new)**

  ```tsx
  //类
  // class 类名 {
  //     属性名: 类型；
  //     constructor(参数：类型){
  //       this.  属性名 =  参数
  //      }
  //     方法名(){
   
  //      }
  // }
  class Person {
    name: string;
    age: number;
    constructor(name: string, age: number) {
      this.name = name;
      this.age = age;
    }
    sayHi(): void {
      console.log(`Hi, ${this.name}`);
    }
  }
  ```

# 接口

## 定义

使用接口来定义对象的类型。接口是对象的状态(属性)和行为(方法)的抽象(描述)

简单理解就是：**为我们的代码提供一种约定**

使用关键字`interface`来声明接口

```typescript
// 定义了一个接口 Person，接着定义了一个变量 tom，它的类型是 Person。这样，我们就约束了 tom 的形状必须和接口 Person 一致。
// 接口一般首字母大写。(当然挺多人也习惯 I 大写字母开头，用来表示这是一个接口)
interface Person {
    name: string;
    age: number;
}
let tom: Person = {
    name: 'Tom',
    age: 25
};
```

## 设置接口可选|只读

* 可选属性，我们最常见的使用情况是，不确定这个参数是否会传，或者存在。
* 只读属性用于限制只能在对象刚刚创建的时候修改其值。此外 TypeScript 还提供了 ReadonlyArray 类型，它与 Array 相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改。

```typescript
interface Person {
  readonly name: string;
  age?: number;
}
```

## 索引签名

有时候我们希望一个接口中除了包含必选和可选属性之外，还允许有其他的任意属性，这时我们可以使用 **索引签名** 的形式来满足上述要求。

需要注意的是，一旦定义了任意属性，那么确定属性和可选属性的类型都必须是它的类型的子集

```typescript
//  prop字段必须是 string类型 or number类型。 值是any类型，也就是任意的
interface Person {
  name: string;
  age?: number;
  [prop: string]: any; // 以 string 类型的值来索引，索引到的是一个 any 类型的值
}
 
const p1:Person = { name: "张麻子" };
const p2:Person = { name: "树哥", age: 28 };
const p3:Person = { name: "汤师爷", sex: 1 };
```

# 接口与类型别名的区别

实际上，在大多数的情况下使用接口类型和类型别名的效果等价，但是在某些特定的场景下这两者还是存在很大区别。

* TypeScript 的核心原则之一是对值所具有的结构进行类型检查。 而接口的作用就是为这些类型命名和为你的代码或第三方代码定义数据模型。
* type(类型别名)会给一个类型起个新名字。 type 有时和 interface 很像，但是可以作用于原始值（基本类型），联合类型，元组以及其它任何你需要手写的类型。起别名不会新建一个类型 - 它创建了一个新名字来引用那个类型。给基本类型起别名通常没什么用，尽管可以做为文档的一种形式使用。

> **接口和类型别名都可以用来描述对象或函数的类型，只是语法不同**

```typescript
type MyTYpe = {
  name: string;
  say(): void;
}
 
interface MyInterface {
  name: string;
  say(): void;
}
```

> **都允许扩展**

* interface 用 `extends` 来实现扩展

```typescript
interface MyInterface {
  name: string;
  say(): void;
}
 
interface MyInterface2 extends MyInterface {
  sex: string;
}
 
let person:MyInterface2 = {
  name:'树哥',
  sex:'男',
  say(): void {
    console.log("hello 啊，树哥！");
  }
}
```

* type 使用 `&` 实现扩展

```typescript
type MyType = {
  name:string;
  say(): void;
}
type MyType2 = MyType & {
  sex:string;
}
let value: MyType2 = {
  name:'树哥',
  sex:'男',
  say(): void {
    console.log("hello 啊，树哥！");
  }
}
```

>type可以声明基本数据类型别名/联合类型/元组等，而interface不行
>
>interface能够合并声明，而type不行

```typescript
// 基本类型别名
type UserName = string;
type UserName = string | number;
// 联合类型
type Animal = Pig | Dog | Cat;
type List = [string, boolean, number];

interface Person {
  name: string
}
interface Person {
  age: number
}
// 此时Person同时具有name和age属性
```

# 泛型

**泛型是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。**

举个例子，比如我们现在有个这样的需求，我们要实现一个这样的函数，函数的参数可以是任何值，返回值就是将参数原样返回，并且参数的类型是 string，函数返回类型就为 string？

很容易写下：

```typescript
function getValue(arg:string):string  {
  return arg;
}
```

现在需求有变，需要返回一个 number 类型的值，你会说，联合类型就完事了：

```typescript
function getValue(arg:string | number):string | number  {
  return arg;
}
```

但是这样又有一个问题，就是如果我们需要返回一个 boolean 类型，string 数组甚至任意类型呢，难道有多少个就写多少个联合类型？

是的，我们直接用 any 就行了！

```typescript
function getValue(arg:any):any  {
  return arg;
}
```

尽管 any 大法好，很多时候 any 也确实能够解决不少问题，但是这样也不符合我们的需求了，传入和返回都是 any 类型，**传入和返回并没有统一**

## 基本使用

泛型的语法是尖括号 `<>` 里面写类型参数，一般用 `T` 来表示第一个类型变量名称，其实它可以用任何有效名称来代替,比如我们用`NIUBI`也是编译正常的

泛型就像一个占位符一个变量，在使用的时候我们可以将定义好的类型像参数一样传入，原封不动的输出

```typescript
function getValue<T>(arg:T):T  {
  return arg;
}
```

* 调用时，定义要使用的类型

  ```typescript
  getValue<string>('树哥'); // 定义 T 为 string 类型
  ```

* 利用 typescript 的类型推断

  ```typescript
  getValue('树哥') // 自动推导类型为 string
  ```

其实并不是只能定义一个类型变量，我们可以引入希望定义的任何数量的类型变量。比如我们引入一个新的类型变量 U

```typescript
function getValue<T, U>(arg:[T,U]):[T,U] {
  return arg;
}
// 使用 typescript 给我们自动推断出输入、返回的类型
const str = getValue(['树哥', 18]);
```

## 泛型约束

在函数内部使用泛型变量的时候，由于事先不知道它是哪种类型，所以不能随意的操作它的属性或方法：

```typescript
function getLength<T>(arg:T):T  {
  console.log(arg.length); // 报错，不能调用 length 属性
}
```

因为泛型 T 不一定包含属性 length，那么我想 getLength 这个函数只允许传入包含 length 属性的变量，该怎么做呢

这时，我们可以使用`extends`关键字来对泛型进行约束

```typescript

interface Lengthwise {
  length: number;
}
 
function getLength<T extends Lengthwise>(arg:T):T  {
  console.log(arg.length); 
  return arg;
}

// 不管你是 str，arr 还是obj，只要具有 length 属性，都可以
const str = getLength('树哥')
const arr = getLength([1,2,3])
const obj = getLength({ length: 5 })
```

## 泛型接口

在定义接口的时候指定泛型

```typescript
interface KeyValue<T,U> {
  key: T;
  value: U;
}
 
const person1:KeyValue<string,number> = {
  key: '树哥',
  value: 18
}
const person2:KeyValue<number,string> = {
  key: 20,
  value: '张麻子'
}
```

## 泛型类

```typescript
class Test<T> {
  value: T;
  add: (x: T, y: T) => T;
}
 
let myTest = new Test<number>();
myTest.value = 0;
myTest.add = function (x, y) {
  return x + y;
};
```

## 泛型类型别名

```typescript
type Cart<T> = { list: T[] } | T[];
let c1: Cart<string> = { list: ["1"] };
let c2: Cart<number> = [1];
```

## 泛型参数的默认类型

可以为泛型中的类型参数指定默认类型。当使用泛型时没有在代码中直接指定类型参数，从实际值参数中也无法推测出时，这个默认类型就会起作用。有点 js 里函数默认参数的意思。

```typescript
function createArray<T = string>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
```

## 泛型工具类型

* typeof

  关键词除了做类型保护，还可以从实现推出类型

  ```typescript
  //先定义变量，再定义类型
  let p1 = {
    name: "树哥",
    age: 18,
    gender: "male",
  };
  type People = typeof p1;
  function getName(p: People): string {
    return p.name;
  }
  getName(p1);
  ```

* keyof

  可以用来获取一个对象接口中的所有 key 值

  ```typescript
  interface Person {
    name: string;
    age: number;
    gender: "male" | "female";
  }
   
  type PersonKey = keyof Person; //type PersonKey = 'name'|'age'|'gender';
   
  function getValueByKey(p: Person, key: PersonKey) {
    return p[key];
  }
  let val = getValueByKey({ name: "树哥", age: 18, gender: "male" }, "name");
  console.log(val); // 树哥
  ```

* in

  用来遍历枚举类型

  ```typescript
  type Keys = "a" | "b" | "c"
   
  type Obj =  {
    [p in Keys]: any
  } // -> { a: any, b: any, c: any }
  ```

* infer

  在条件类型语句中，可以用 infer 声明一个类型变量并且对它进行使用。

  ```typescript
  type ReturnType<T> = T extends (
    ...args: any[]
  ) => infer R ? R : any;
  infer R 就是声明一个变量来承载传入函数签名的返回值类型，简单说就是用它取到函数返回值的类型方便之后使用。
  ```

* extends

  有时候我们定义的泛型不想过于灵活或者说想继承某些类等，可以通过 extends 关键字添加泛型约束。

  ```typescript
  interface Lengthwise {
    length: number;
  }
   
  function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
  }
  
  // 泛型函数被定义了约束，因此它不再是适用于任意类型
  loggingIdentity(3);  // Error, number doesn't have a .length property
  loggingIdentity({length: 10, name: '张麻子'}); // 编译正确
  ```

* 索引访问操作符

  使用 `[]` 操作符可以进行索引访问：

  ```typescript
  interface Person {
    name: string;
    age: number;
  }
  type x = Person["name"]; // x is string
  ```







