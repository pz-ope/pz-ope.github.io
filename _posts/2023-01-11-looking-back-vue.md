---
layout:     post
title:      "vue知识回顾"
subtitle:   "Vue knowledge review"
date:       2023-01-11 10:00:21
author:     "pz"
catalog: false
header-style: text
tags:
  - 笔记
  - 前端


---

# vue生命周期

Vue实例从创建到销毁的过程，就是生命周期。详细来说也就是从开始创建、初始化数据、编译模板、挂载Dom、渲染→更新→渲染、卸载等一系列过程。

1. beforeCreate（Vue中的data和方法都是去不到的，并且是在wath之前执行）
2. created（可以获取Vue的data，调用Vue方法，`获取原本HTML上的直接加载出来的DOM`，但是无法获取到通过挂载模板生成的DOM（例如：v-for循环遍历Vue.list生成li））
3. beforeMount（在挂载开始之前被调用：相关的` render 函数（模板）首次被调用`。）
4. mounted（这里为止，挂载到实例上了，我们可以获取到li的个数了）
5. beforeUpdate（数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前。 你可以在这个钩子中进一步地更改状态，这`不会触发附加的重渲染过程`。更改Vue的任何数据，都会触发该函数）
6. updated（数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。数据更新就会触发。vue所有的数据只有有更新就会触发）,`如果想数据一遍就做统一的处理，可以用这个，如果想对不同数据的更新做不同的处理可以用nextTick，或者是watch进行监听`）
7. beforeDestroy（实例销毁之前调用）
8. destroyed（Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。）

**注意：**

1. 用定时器来做异步函数（ setTimeout）
2. Vue.nextTick对异步函数的结果进行操作（ this.$nextTick），改变数据时,各自触发各自的方法；

## 简单HelloWorld vue项目启动流程

![image-20230208204253217](/img/image/image-20230208204253217.png)

1. 在执行npm run dev的时候，会在当前目录中寻找 package.json 文件，导入依赖；
2. 启动 npm run dev 命令后，会加载 build/webpack.dev.conf.js 配置并启动 webpack-dev-server；
3. webpack.dev.conf.js 中引入了很多模块的内容，其中就包括 config 目录下服务器环境的配置文件；
4. 在 index.js 文件中包含服务器 host 和 port 以及入口文件的相关配置，默认启动端口是8080，这里可以进行修改；
5. index.html 的内容很简单，主要是提供一个 div和 vue 挂载；
6. main.js 中， 引入了 vue、App 和 router 模块， 创建了一个 Vue 对象，并把 App.vue 模板的内容挂载到 index.html 的 id 为 app 的 div 标签下， 并绑定了一个路由配置；
7. main.js 把 App.vue 模板的内容，放置在了 index.html 的 div 标签下面。查看 App.vue 的内容我们看到，这个页面的内容由一个 logo 和一个待放置内容的 router-view，router-view 的内容将由 router 配置决定；
8. 查看 route 目录下的 index.js，我们发现这里配置了一个路由， 在访问路径 / 的时候， 会把 HelloWorld 模板的内容放置到上面的 router-view中；
9. HelloWorld 中主要是一些 Vue 介绍显示内容，页面关系组成是 index.html 包含 App.vue，App.vue 包含 HelloWorld.vue；

## vue项目结构

![image-20230208204749061](/img/image/image-20230208204749061.png)

# vue路由跳转的方式

## router-link

* 不带参数

  ```vue
  <router-link :to="{name:'home'}">
  <router-link :to="{name:'/home'}"> //name,path都行，建议用name
  //注意:router-link中链接如果是'/'开始就是从根路由开始，如果开始不带/'，则从当前路由开始。
  ```

* params传参

  ```vue
  <router-link : to="{name:'home',params:{id:1}}">
  // params传参数 (类似post)
  // 路由配置 path:"/home/:id"或者path:"/home:id"
  // 不配置path,第一次可请求,刷新页面id会消失
  // 配置path, 刷新页面id会保留
  ```

* query传参

  ```vue
  <router-link : to="{name:'home',query:{id:1}}">
  // query传参数(类似get,urL后面会显示参数)
  ```

## this.$router.push()

`函数里面调用`

* 不带参数

  ```vue
  this.$router.push('/home');
  this.$router.push(name:'home');
  this.$router.push(path:'/home');
  ```

* query传参

  ```vue
  this.$router.push({name:'home',query:{id:'1'}});
  this.$router.push({path:'/home',query:{id:'1'}});
  ```

* params传参

  ```vue
  this.$router.push({name:'home',params:{id:'1'}}); // 只能用 name
  ```

## this.$router.replace() 

(用法同上,push)

## this.$router.go(n)

(用法同上)

## 总结

* **this.$router.push()**

  跳转到指定url路径，并想history栈中添加一个记录，点击后退会返回到上一个页面；

* **this.$router.replace()**

  跳转到指定url路径，但是history栈中不会记录，点击返回会跳转到上上个页面（就是直接替换了当前页面）；

* **this.$router.go(n)**

  向前或者向后跳转n个页面，n可以为正整数和负整数；

# vue数据双向绑定的原理

==vue.js采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。==

例：

obj ：待修改的对象，prop ：带修改的属性名称，descriptor ：待修改属性的相关描述

```vue
<script>
var obj = {}
var returnVal = ""
// 利用defineProperty去劫持更新
// 第三个参数  数据描述  存取器的描述
Object.defineProperty(obj, "age", {
    get:function () {
        return returnVal
    },
    set:function (newValue) {
        returnVal = newValue
    }
})
</script>
```

随文本框输入文字的变化，txtspan 中会同步显示相同的文字内容；在js或控制台显式的修改 obj 中 name 的值。
v-model:实现双向数据绑定。

```vue
<div id="app">
	<input type="text" v-model="msg">
	{{msg}}
	<button v-on:click="add()">按钮</button>
</div>		
<script src="js/vue.js"></script>
<script>
	new Vue({
		el:'#app',
		data:{
			msg:0
		},
		methods:{
			add(){
				this.msg=this.msg+1
			}
		}
	})
</script>
```

# 组件间的传参

![image-20230210143408183](/img/image/image-20230210143408183.png)

## props/$emit父子组件传值

### 父传子 (自定义属性 props)

父组件向子组件传参，通过自定义属性的方式进行传参，在子组件中使用prop定义自定义的属性，然后在父组件中通过v-bind指令把需要传递的数据绑定在子组件上，那在子组件中props里面的自定义属性可以直接使用。

```html
//父组件代码   渲染子组件
<Son :name="name" />
```

```js
// 子组件代码，接受父参数
export default {
  props: {
    name:{
                type:String,
                default:"我是默认字符串"//定义参数默认值
                required:false//定义参数是否必须值
            }
  }
}
```

### 子传父 (自定义事件 this.$emit)

子组件向父组件传数据使用自定义事件， vue 组件提供了一个emit(‘自定义事件名’， 传递的数据) )，子组件传递数据时，触发响应的事件函数，就会自动触发通过$emit给父组件绑定的自定义事件，自定义事件接受一个参数，参数就是子组件传递过来的数据。

```js
// 父组件代码，渲染子组件
<Son @setValue="valueFn" />
export default{
  method:{
    valueFn(value) {}
  }
}
```

```js
// 子组件代码
this.$emit('setValue', this.say)
```

## ref与parent/children父子组件传值

### 父传子

```js
//父组件
<Home ref="home"></Home>
<button @click="toValue">点击</button>
methods:{
  toValue(){
          this.msg = "这是父组件的值";
          this.$refs.home.setMsg(this.msg);
   }
}
```

```js
//子组件
<div class="home">
      {{msg}}
 </div>
    methods:{
        setMsg(val){
            this.msg = val;
        }
    }
```

### 子传父（如果子组件是公共组件，需判断父组件是否具有该方法）

```js
//父组件
<Home ref="home"></Home>
methods:{
  toValue(val){
          this.msg = val;
   }
}
```

```js
//子组件
<div class="home">
<button @click="setMsg">点击</button>
 </div>
    methods:{
        setMsg(val){
             this.$parent.toValue(this.msg);
        }
    }
```

## 兄弟之间传参

兄弟组件之间的数据传递，通过eventBus(事件总线)来做中间的桥梁，传输方通过中间组件调用 emit 传数据，接收方通过on 接受数据，两者之间的自定义属性名保持一致;

```kotlin
// 传输方组件调用方式
import Bus from '@/EventBus.js'
Bus.$emit('pass-value', this.say);
```

```kotlin
// 接收方接受参数
import Bus from '@/EventBus.js'

created() {
  Bus.$on('pass-value', val => {
     this.sibilingValue = val;
  })
}
```

## $attrs/$listeners隔代组件传值（爷孙组件参数互传）

* $attrs
  1. 包含了父作用域中不被 prop 所识别 (且获取) 的特性绑定 (class 和 style 除外);
  2. 当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind=“$attrs” 传入内部组件。通常配合 interitAttrs 选项一起使用。
* $listeners
  1. 包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。
  2. 它可以通过 v-on=“$listeners” 传入内部组件。
* 简单来说：$attrs 与$listeners是两个对象，$attrs 里存放的是父组件中绑定的非 Props 属性，$listeners 里存放的是父组件中绑定的非原生事件。

### 爷传孙（$attrs）

```kotlin
    //爷组件
    <div id="app">
      <Home :msg="msg"></Home>
    </div>

    //父组件(父组件的操作最简单，但不做就会传不过去)
    <div class="home">
      <Sun v-bind="$attrs"></Sun>
    </div>
```

```kotlin
    //孙组件
    <div class="sun">
      {{ msg }}
    </div>
    //props直接接受 
    props:{ msg:String, }
```

### 孙传爷（$listeners）

```kotlin
    //爷组件
    <div id="app">
      <Home @setVal="setVal">></Home>
    </div>
    methods:{ setVal(val){ this.msg = val; } }
    //父组件(父组件的操作最简单，但不做就会传不过去)

    <div class="home">
      <Sun v-on="$listeners"></Sun>
    </div>

    //孙组件
    <div class="sun">
      <button @click="toVal">点我</button>
    </div>
    methods:{ toVal(){ this.$emit("setVal",this.msg) } }
```

## 通过Vuex数据共享

通过Vuex存储数据， Vuex是一个专为vue.js 应用程序开发的状态管理模式， 它采用集中式存储管理数据，以相应的规则保证状态以一种可预测的方式发生改变， 一变全变，同步更新数据。

```kotlin
// 注册代码
const store = new Vue.Store({
  state:{
    count: 100
  },
  mutations: {
    addCount(state, val = 1) {
      state.count += val;
    }，
    subCount(state, val = 1) {
      state.count -= val;
    }
  }
})

// 组件调用
this.$store.commit('addCount');  // 加 1
this.$store.commit('subCount');  // 减 1
```

# vue中常用算法

## 数组去重

* 双重for循环去重

  ```js
  function noRepeat1(arr){
      for(var i=0; i<arr.length; i++){ // 第一层for用来控制循环的次数
          for(var j=i+1; j<arr.length; j++){ //第二层for 用于控制与第一层比较的元素
              if(arr[i] == arr[j]){   
                  arr.splice(j,1);  //删除后面的 即第 j个位置上的元素  删除个数 1 个
                  j--;
              }
          }
      }
      return arr;
  }
  ```

* 单层for循环

  ```js
  function norepeat(arr){
      arr.sort();//先排序让大概相同的在一个位置，sort排序是把元素当字符串排序的 它和可能排成 1 1 10 11 2 20 3 ... 不是我们想要的从小到大
      for(var i = 0; i < arr.length-1;i++){ //还是两两比较 一样删除后面的
          if(arr[i]==arr[i+1]){
              arr.splice(i,1);
              //i-- 和j--同理
              i--;
          }
      }
      return arr;
  }
  ```

* 利用 indexOf 属性

  ```js
  // indexOf是返回某个指定的字符在字符串中出现的位置，如果没有就会返回-1
  function noRepeat2(arr){
      var newArr = [];//用一个空数组去存首次 出现的元素 
      for(var i = 0; i < arr.length; i++){
          if(newArr.indexOf(arr[i]) == -1){
                      newArr.push(arr[i]);
              }
      }
      return newArr;
  }
  ```

* 利用对象的思想

  ```js
  // 如果对象里没有这个属性的话就会返回undefined 。利用这个原理当返回的是undefined时 让其放入数组 然后在给这个属性赋值
  function norepeat3(arr) {
       var obj = {};
       var newArr = [];
       for(var i = 0; i < arr.length; i++) {
           if(obj[arr[i]] == undefined) {
               newArr.push(arr[i]);
               obj[arr[i]] = 1;
           }
       }
       return newArr;
  }
  ```

## 统计字符串中出现最多的字符

```js
function countStr(str){  // 形参接受
	var myobj = {}; // 存储出现最多的字符
	for( var i=0, len=str.length,num; i<len; i++ ){
		num= str.charAt(i) 
		if( myobj[num] ){ // 保存次数
			myobj[num]++;
		}else{
			myobj[num]=1;
		};
	};
	console.log(myobj)
	var n= 0,item=null;
        for(var k in myobj){
            if( myobj[k] > n ){
                n = myobj[k];
                item = k;
            }
        }
	return item+':'+n
};
countStr('哈哈哈呵呵');  // 实参传参
 // {哈: 3, 呵: 2}
 // "哈:3"
```

# 图片预加载和懒加载

* 懒加载：在需要显示图片的时候才去加载图片。

* 预加载：在还没显示的时候就加载图片。

## 懒加载

> 原理：

比如我们首屏有二十张图片，但是打开页面的时候只需要显示轮播图和第一张图片，那么另外的图片是不是可以在需要显示的轮播图和显示的第一张图片加载完成后慢慢去加载。

> 方法

1、使用定时器或者计时器。在打开页面需要显示的图片我们优先加载，然后写一个定时器或者计时器去请求之后需要用到的图片。	
2、条件加载。一些图片是某些条件触发才显示的，也可以在显示页面的时候先不加载，直到需要显示的时候去加载图片。
3、可视区域加载。说白了就是监听滚动条，滚动条滚动到一定位置的时候就去加载马上要显示的图片，要稍微提前一点去加载。

## 预加载

> 原理：

虽然页面还不需要显示图片，但是我们已经把这些图片加载下来了， 只是不显示这些图片，众说周知浏览器是会缓存请求过的图片。

> 方法

1、使用css背景图片。我们写一些样式，然后把图片的地址放到背景图片里面，让图片隐藏，也可以设置背景图片位置偏移出这个页面。浏览器解析到这些样式的时候就会去加载这些图片，然后等你需要显示的时候浏览器会从缓存里面取，就不需要再去请求。但是这个会造成解析过程中增加了解析时间。	
2、使用JavaScript配合css背景图片。原理就是在DOM和CSS都加载完了，就是在ready方法里面给这些样式添加背景图片，这样就不会造成解析时间边长的问题。
3、用JavaScript创建图片。创建一个对象，就是new Image()，然后给这个对象赋值src，也可以使用数组去实现需要加载很多图片的时候。
4、使用Ajax。就是发起一个get请求，地址是这张图片，因为请求后浏览器会缓存，这张图片就预加载到了本地。

## 总结

比如需要几十张图片显示，但是有优先顺序，就使用懒加载，
如果是只有几张小图，页面渲染速度也够快，想要交互的时候响应快速，使用预加载。

> 1、设置了display: none的img标签和元素背景图片，不会渲染但是会加载。
> 2、父元素div设置了display: none，那么这个父元素里面的子元素img标签会加载,父元素里面子元素背景图片不会加载
> 3、重复或者加载过的图片只会加载一次
> 4、不存在元素的背景图片不会加载
> 5、伪类，比如hover，在触发后才会加载

# MVVM和MVC区别

## MVC （Model-View-Controller）

M - Model ：数据保存
V - View : 用户界面
C - Controller ： 业务逻辑
MVC ，用户操作> View (负责接受用户的输入操作)>Controller（业务逻辑处理）>Model（数据持久化）>View（将结果通过View反馈给用户）

## MVVM

M -Model
V - 
VM - 视图模型

MVVM是将“数据模型数据双向绑定”的思想作为核心，因此在View和Model之间没有联系，
通过ViewModel进行交互，而且Model和ViewModel之间的交互是双喜那个的，
因此试图的数据的变化会同时修改数据源，而数据源数据的变化也会立即反应到View上。

