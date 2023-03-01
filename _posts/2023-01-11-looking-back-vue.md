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

# render函数

vue中视图的渲染过程如下图所示。

<img src="../../../../../Download/Typora/image/image-20230216113337079.png" alt="image-20230216113337079" style="zoom:80%;" />

优先使用render函数，如render函数不存在会去查找template。最终将template编译生成render函数，使用render函数完成视图渲染。（vue的视图模板实际上是通过render函数渲染出来的）

render函数返回virtual DOM，vue框架基于virtual DOM生成真实的DOM。
（virtual DOM -> actual DOM）
**render函数使得我们拥有使用js构建DOM的能力。我们可以自定义Render函数实现更灵活的功能。**

render函数与props、data、computed同级，它接受一个createElement函数作为参数, 通常简写为h。如下图所示。

```js
render(h){
    return h("div",{},[...])
}
```

## createElement 

createElement (简写为h) 是 render 函数的参数，它本身也是个函数，有三个参数。

1. 第一个参数是必填的，可以是String | Object | Function
   String，表示的是HTML 标签名
   Object ，一个含有数据的组件对象
   Function ，返回了一个含有标签名或者组件选项对象的async 函数
2. 第二个参数是选填的，一个与模板中属性对应的数据对象
   常用的有class | style | attrs | domProps | on
3. 第三个参数是选填的，代表子级虚拟节点 (VNodes)，由 `createElement()` 构建而成, 也可以放置字符串。如果放置字符串，字符串会被创建为第一个参数创建的dom节点下的文本。

注：任何时候都需要一个文本放置在dom元素中。即第三个参数的位置始终需要放置一个东西，如果不是[...]，就是"str"。

```js
h("div","some text");
h("div",{class:'foo'},"some text");
h("div",{...},["some text",h('span','bar')])

// 实现一个example组件，接收数组tags作为参数，创建与tags内容相同的html标签，标签的内容是对应tag的index。
// html
<example :tags="['h1', 'h2', 'h3 ' ]"></ example>
// js
props:["tags" ],
render(h) {
return h("div", this.tags.map((tag,i) => h(tag,i)));
}
which renders the expected output:
// html
<div>
	<h1>0</h1>
	<h2>1</h2>
	<h3>2</h3>
</div>
```

# Vue 的父组件和子组件生命周期

## Vue 的父组件和子组件生命周期钩子函数执行顺序

Vue 的父组件和子组件生命周期钩子函数执行顺序可以归类为以下 4 部分：

* 加载渲染过程 :
  父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted
* 子组件更新过程 :
  父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated
* 父组件更新过程 :
  父 beforeUpdate -> 父 updated
* 销毁过程 :
  父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

## 父组件可以监听到子组件的生命周期

* 自定义事件

  比如有父组件 Parent 和子组件 Child，如果父组件监听到子组件挂载 mounted 就做一些逻辑处理，可以通过以下写法实现：

  ```kotlin
  // Parent.vue
  <Child @mounted="doSomething"/>
  
  // Child.vue
  mounted() {
    this.$emit("mounted");
  }
  ```

* @hook 来监听

  简单的方式可以在父组件引用子组件时通过 @hook 来监听即可

  ```kotlin
  //  Parent.vue
  <Child @hook:mounted="doSomething" ></Child>
  
  doSomething() {
     console.log('父组件监听到 mounted 钩子函数 ...');
  },
  
  //  Child.vue
  mounted(){
     console.log('子组件触发 mounted 钩子函数 ...');
  },    
  // 以上输出顺序为：
  // 子组件触发 mounted 钩子函数 ...
  // 父组件监听到 mounted 钩子函数 ...
  ```

# computed 和 watch

## computed 

计算属性，依赖其它属性值，并且 computed 的值有缓存，只有它依赖的属性值发生改变，下一次获取 computed 的值时才会重新计算 computed 的值；

* 支持缓存，只有依赖数据发生改变，才会重新进行计算；
* 不支持异步，当 computed 内有异步操作时无效，无法监听数据的变化；
* computed 属性值会默认走缓存，计算属性是基于它们的响应式依赖进行缓存的。也就是基于data中声明过或者父组件传递的 props 中的数据通过计算得到的值；
* 如果一个属性是由其他属性计算而来的，这个属性依赖其他属性 是一个多对一或者一对一，一般用computed；
* 如果 computed 属性值是函数，那么默认会走 get 方法，函数的返回值就是属性的属性值；在computed中的，属性都有一个get和一个 set 方法，当数据变化时，调用 set 方法；

```kotlin
computed:{
   计算的属性名：{
//当初次读取 计算的属性名 时，或者所依赖的数据发生变化时get调用。
  get（）{
    return  xxx
}，
//当计算的属性的数据被修改时set调用，是触发了setter也就会触发getter，他们两个是相互独立的，若set中当前所依赖的数据，才会触发get修改了执行顺序是setter -> getter -> updated
  set（）{
    }
  }
}
//简写：
computed：{
   计算的属性名（）{
    return
  }
}
```

## watch

监听属性, 更多的是「观察」的作用，类似于某些数据的监听回调 ，每当监听的数据变化时都会执行回调进行后续操作；

* 完全等同于vue2 中的watch
* 不支持缓存，数据变化，直接会触发相应的操作；
* watch 支持异步操作；
* 监听的函数接收两个参数，第一个参数是最新的值；第二个参数是输入之前的值；
   当一个属性发生变化时，需要执行对应的操作，一对多；
* 监听数据必须是 data 中声明过或者组合式api中声明的响应式值或者父组件传递过来的 props 中的数据。当数据变化时触发其他操作，函数有两个参数：

immediate属性：组件加载立即触发回调函数执行；

deep: 深度监听；为了发现对象内部值的变化，复杂类型的数据时使用，例如：数组中的对象内容的改变，注意：监听数组的变动不需要这么做。注意：deep无法监听到数组的变动和对象的新增，参考vue数组变异,只有以响应式的方式触发才会被监听到；

```kotlin
写法一：
const vm = new Vue({
watch:{
 （监视属性）：{
       immediate: true,   //立即执行
       deep:true,//深度监视
           //初始化时让handler调用一下
          //handler什么时候调用？当isHot发生改变时。
       handler(newValue, oldValue) {
      }
    }
  }
}）
写法二：
vm.$watch('监视属性',{
    immediate:true, //立即执行
    deep:true,//深度监视
   //初始化时让handler调用一下
   //handler什么时候调用？当isHot发生改变时。
    handler(newValue,oldValue){
  }
}）
简写：
1. watch：{
  监视属性(newValue,oldValue){
}
2. vm.$watch('监视属性',(newValue,oldValue)=>{
}) 
```

## 直接给一个数组项赋值，Vue 检测不到变化

由于 JavaScript 的限制，Vue 不能检测到以下数组的变动：

- 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`
- 当你修改数组的长度时，例如：`vm.items.length = newLength`

解决办法：

```kotlin
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
// vm.$set，Vue.set的一个别名
vm.$set(vm.items, indexOfItem, newValue)
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)

// Array.prototype.splice
vm.items.splice(newLength)
```

## 运用场景

* 当需要进行数值计算，并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算；
* 当需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许我们执行异步操作 ( 访问一个 API )，限制执行该操作的频率，并在得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

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

### Vue 的单向数据流

所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。
这样会防止从子组件意外改变父级组件的状态，从而导致应用的数据流向难以理解。
额外的，每次父级组件发生更新时，子组件中所有的 prop 都将会刷新为最新的值。
这意味着不应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。
子组件想修改时，只能通过 $emit 派发一个自定义事件，父组件接收到后，由父组件修改。

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

# Vuex

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。每一个 Vuex 应用的核心就是 store（仓库）。“store” 基本上就是一个容器，它包含着你的应用中大部分的状态 ( state )。

- Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
- 改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化。

```js
// 新建store文件->index.js，进行如下配置，在main.js中进行引入
import Vue from 'vue'
import Vuex from 'vuex'
 
Vue.use(Vuex)
 
export default new Vuex.Store({
  //数据，相当于data
  state: {
    name:"张三",
    age:12,
    count:0
  },
  getters: {
    
  },
  //里面定义方法，操作state方发
  mutations: {
    addcount (state,num){
        state. count=+state.count+num
    },
    reduce (state){
         state.count--
    }
  },
  // 操作异步操作mutation
  actions: {
    asyncAdd( context){
        //异步
        setTimeout(()=>{
            context.commit("reduce")
        },1000);
  },
  modules: {
   userinfo: {
       state: {
           username: "张启楠"，
       },
   },
  },
})
// main.js中
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
Vue.config.productionTip = false
new Vue({
    router,
    store,
    render: h => h(App)
}).$mount('#app')
```

主要模块：

- State => 公共数据源，所有共享的数据统一放到store的state进行储存,定义了应用状态的数据结构，可以在这里设置默认的初始状态。

  ```jsx
  <p>{{ $store.state.name }}</p>  // 标签中直接使用
  this.$store.state.全局数据名称   // 标签中直接使用
  // 从vuex中按需导入mapstate函数
  import { mapState } from "vuex"; 
  computed: {
      ...mapState( ["name","age","sex"])
  }
  ```

- Getter => 从基本数据派生的数据，允许组件从 Store 中获取数据，mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性,类似于vue中的computed，进行缓存，对于Store中的数据进行加工处理形成新的数据。

- mutation => 是唯一更改 store 中状态的方法，且必须是同步函数。

  Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的**事件类型 (type)和一个回调函数 (handler)**。这个回调函数就是实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

  ```kotlin
  // 使用commit触发Mutation操作
  methods:{
      //加法
      btn(){
          this.$store.commit("addcount",10)     //每次加十
      }
      //减法
      btn1(){
          this.$store.commit("reduce") 
      }
  }
  
  // 使用辅助函数进行操作
  import { mapMutations } from "vuex"; 
  methods:{
      ...mapMutations(["addcount","reduce"]),
      btn(){
          this.addcount(10);
      },
      btn1(){
          this.reduce();
      }
  }
  ```

- action => 像一个装饰器，包裹mutations，使之可以异步。用于提交 mutation，而不是直接变更状态，可以包含任意异步操作。

  ```kotlin
  // 使用  dispatch触发Action函数
  this.$store.dispatch("asynAdd")
  // 使用辅助函数进行操作
  ...mapActions(["asyncAdd"]),
  btn2(){
      this.asyncAdd();
  }
  ```

- module => 模块化Vuex，允许将单一的 Store 拆分为多个 store **模块（module）**且同时保存在单一的状态树中。

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

M -Model 数据模型：数据和业务逻辑都在Model层中定义；
V - View UI视图：负责数据的展示；
VM - 视图模型：负责监听 Model 中数据的改变并且控制视图的更新，处理用户交互操作；

MVVM是将“数据模型数据双向绑定”的思想作为核心

Model 和 View 并无直接关联，而是通过 ViewModel 来进行联系的，Model 和 ViewModel 之间有着双向数据绑定的联系。因此当 Model 中的数据改变时会触发 View 层的刷新，View 中由于用户交互操作而改变的数据也会在 Model 中同步。
这种模式实现了 Model 和 View 的数据自动同步，因此开发者只需要专注对数据的维护操作即可，而不需要自己操作 dom。

# v-if 和 v-show

## 共同点

在 vue 中 v-show 与 v-if 的作用效果是相同的(不含v-else)，都能控制元素在页面是否显示 。

- 当表达式都为 false 时，都不会占据页面位置
- 当表达式结果为 true 时，都会占据页面的位置

## 区别

* **控制手段**：v-show隐藏则是为该元素添加css--display:none，dom元素依旧还在，不会占据页面位置。v-if显示隐藏是将dom元素整个添加或删除
* **编译过程**：v-if切换有一个局部编译/卸载的过程，切换过程中合适地销毁和重建内部的事件监听和子组件；v-show只是简单的基于css切换
* **编译条件**：v-if是真正的条件渲染，它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。只有渲染条件为假时，并不做操作，直到为真才渲染

v-show 由false变为true的时候不会触发组件的生命周期

v-if由false变为true的时候，触发组件的beforeCreate、create、beforeMount、mounted钩子，由true变为false的时候触发组件的beforeDestory、destoryed方法

**性能消耗**：v-if有更高的切换消耗；v-show有更高的初始渲染消耗；

# SPA 单页面

SPA（ single-page application ）仅在 Web 页面初始化时加载相应的 HTML、JavaScript 和 CSS。

一旦页面加载完成，SPA 不会因为用户的操作而进行页面的重新加载或跳转；取而代之的是利用路由机制实现HTML 内容的变换，UI 与用户的交互，避免页面的重新加载。

优点：

* 用户体验好、快，内容的改变不需要重新加载整个页面，避免了不必要的跳转和重复渲染；
* 基于上面一点，SPA 相对对服务器压力小；
* 前后端职责分离，架构清晰，前端进行交互逻辑，后端负责数据处理；

缺点：

* **初次加载耗时多**：为实现单页 Web 应用功能及显示效果，需要在加载页面的时候将 JavaScript、CSS 统一加载，部分页面按需加载；
* **前进后退路由管理**：由于单页应用在一个页面中显示所有的内容，所以不能使用浏览器的前进后退功能，所有的页面切换需要自己建立堆栈管理；
* **SEO 难度较大**：由于所有的内容都在一个页面中动态替换显示，所以在 SEO 上其有着天然的弱势。

# 动态绑定样式

## Class动态绑定

Class 可以通过对象语法和数组语法进行动态绑定

* 对象语法

  ```kotlin
  <div v-bind:class="{ active: isActive == item.nameId, 'user': isUser }">{{item.name}}</div>// active在对象里面可以不加单引号
  <div :class="classObject">{{name}}</div>
  <div :class="classObjectComputed">{{name}}</div>
  data() {
    return {
        isActive: true,
        user: false,
        classObject:{ active: true, user:false }// 放在data里面
    }
  }
  // 使用computed属性
  computed: {
    classObjectComputed: function () {
      return {
        active: this.isActive,
        user:this.isUser
      }
    }
  }
  ```

* 数组语法

  ```kotlin
  <div v-bind:class="[isActive,isUser]">{{name}}</div>
  <div :class="[isActive==index?'active':'otherActiveClass']">{{name}}</div>
  :class="[{ active: isActiveindex}, 'sort']"
  data() {
    return{
      isActive:'active',
      isUser:'user',
      isActiveindex:true,
   }
  }
  ```

## 动态绑定style

style属性名都要变成驼峰式，比如font-size要变成`fontSize`

除了绑定值，其他的属性名的值要用引号括起来，比如`backgroundColor:'#00a2ff'`而不是 backgroundColor:#00a2ff

* 对象语法：

  ```kotlin
  <div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
  <div :style="{color:(index == 1 ? conFontColor:'#000')}"></div>
  <div :style="styleObject"></div>
  
  <div :style="[{float: id === '12' ? 'left:'right}]"></div>
  <div :style="float: nameList.length === 20 ? 'height:64px' : 'height:32px' "></div>
  <div :style="{border:( nameId ===item.id ?'2px solid #4C78FF': 'red')}"></div>
  data() {
      return{
          activeColor: 'red',
          fontSize: 30
          styleObject: {
              color: 'red',
              fontSize: '14px'
          }  
      }
  }
  ```

* 数组语法：

  ```kotlin
  <div v-bind:style="[styleColor, styleSize]"></div>
  <div :style="[{color:(index == 1 ? conFontColor:'#000')},{fontSize:'18px'}]"></div>
  data: {
    styleColor: {
       color: 'red'
     },
    styleSize:{
       fontSize:'23px'
    }
  }
  ```

  





