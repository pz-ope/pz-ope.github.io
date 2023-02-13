---
layout:     post
title:      "HTML+CSS元素精炼"
subtitle:   "HTML+CSS element refining"
date:       2022-09-20 09:23:11
author:     "pz"
catalog: false
header-style: text
tags:
  - 笔记
  - 前端

---

# HTML元素显示模式转换

|  元素模式  |  display值   |         元素排列         |     设置样式     |    默认宽度    |                     包含                     |
| :--------: | :----------: | :----------------------: | :--------------: | :------------: | :------------------------------------------: |
|  块级元素  |    block     |  一行只能放一个块级元素  | 可以设置宽度高度 |   容器的100%   | 是一个容器及盒子，里面可以放行内或者块级元素 |
|  行内元素  |    inline    |  一行可以放多个行内元素  | 不可以设置宽、高 | 本身内容的宽度 |      行内元素只能容纳文本或其他行内元素      |
| 行内块元素 | block-inline | 一行可以放多个行内块元素 | 可以设置宽度高度 | 本身内容的宽度 |                                              |

注意：

* 文字类的元素内不能使用块级元素。<p>标签主要用于存放文字，因此<p>里面不能放块级元素，特别是不能放<div>，同理，<h1>~<h6>等都是文字类块级元素，里面不能放块级元素；
* 链接里面不能再放链接，<a>里面可以放块级元素，但是给<a>转换一下块级模式最安全

# CSS选择器

css中有很多选择器，比如
类选择器、标签选择器、ID选择器、后代选择器、子选择器、伪类选择器、通用选择器、群组选择器等，
常见的选择器有id选择器、类选择器、标签选择器三大类。

## 标签选择器

**标签名{css属性名：属性值}**

通过标签名，找到页面中所有的这类标签，设置样式

`p{color: red;}`

## 类选择器

 **.类名{css属性名：属性值；}**

* 通过类名，找到页面中所有的带有这个类名的标签，设置样式；
* 所有的标签上都有class属性，class属性的属性值成为类名，可以重复；
* 一个标签中可以同时有多个类名，类名之间用空格隔开；

```js
<style>
    .one{
        color: red;
    }
</style>
<body>
    <div class="one">
        类选择器
    </div>
</body>
```

## id选择器

**#id属性值{css属性名：属性值;}**

* 通过id属性值，找到页面中带有这个id属性值的标签，设置样式
* 所有的标签上都有id属性,在一个页面中是唯一的，不可重复的，一个id选择器只能选中一个标签;
* 一个标签里面只能有一个ID属性值，id一般配合js来使用；

```js
<style>
    #one{
        color: red;
    }
</style>
<body>
    <div id="one">
        id选择器
    </div>
</body>
```

## 通配符选择器

***{css属性名：属性值}**

* 找到页面中所有的标签，然后设置样式
* 在小页面中可能会用于去除页面中默认的margin和padding

```html
<style>
    *{
        color: red;
    }
</style>
<body>
    <div>
        通配符选择器
    </div>
</body>
```

## 伪类选择器

伪类是选择器的一种，它用于选择处于特定状态的元素;

* **元素选择伪类选择器**

  :前有空格代表从子类开始数，无空格代表从所在级数开始；

  **在所有元素中选择：**

  | 伪类选择器        | 含义              | 备注                                                         |
  | :---------------- | :---------------- | ------------------------------------------------------------ |
  | :first-child      | 第一个子元素      |                                                              |
  | :last-child       | 最后一个子元素    |                                                              |
  | :nth-child(n)     | 选中第n个元素     | n：第n个 n的范围0到正无穷（全选）；even或2n 选中偶数位的元素；odd或2n+1 选中奇数位得到元素；(-n + i)代表前i个子元素 |
  | nth-last-child(n) | 选中倒数第n个元素 | 跟:nth-child()类似                                           |

  **在相同元素中选择：**

  | 伪类选择器      | 含义           | 备注                                                         |
  | :-------------- | :------------- | ------------------------------------------------------------ |
  | :first-of-type  | 第一个子元素   | “:first-child”是选择父元素下的第1个子元素（不区分元素类型），而“:first-of-type”是选择父元素下某个元素类型的第1个子元素（区分元素类型）。 |
  | :last-of-type   | 最后一个子元素 |                                                              |
  | :nth-of-type(n) | 选中第n个元素  |                                                              |

* **否定伪类选择器**

  not( ) 将符号条件的元素去除

  不支持组合使用 :not(div .one) 不支持

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>否定伪类</title>
      <style>
          :not(:first-of-type):not(:last-child){
              color: red;
          }
      </style>
  </head>
  <body>
      <p>文字</p><!-- 不变红 -->
      <p>文字</p><!-- 变红 -->
      <p>文字</p><!-- 变红 -->
      <p>文字</p><!-- 不变红 -->
  </body>
  </html>
  ```

* **特殊应用的伪类**

  | 伪类选择器 | 含义                   | 备注                            |
  | :--------- | :--------------------- | ------------------------------- |
  | :link      | 未访问过的a标签        | 超链接所独有                    |
  | :visited   | 访问过的a标签          | 超链接所独有,只能修改链接的颜色 |
  | :hover     | 鼠标移入后元素的状态   |                                 |
  | :active    | 鼠标点击后，元素的状态 |                                 |

* 常见的伪元素选择器

  | 伪类选择器     | 含义             | 备注                |
  | :------------- | :--------------- | ------------------- |
  | ::first-letter | 表示第一个字母   |                     |
  | ::first-line   | 表示第一行       |                     |
  | ::selection    | 表示选中的元素   |                     |
  | ::before       | 元素开始的位置前 | 配合contend一起使用 |
  | ::after        | 元素结束的位置后 | 配合contend一起使用 |

  ```html
  <html lang="en">
    <head>
      <meta charset="UTF-8" />
      <title>伪元素选择器</title>
      <style>
        /* 需求一：让文章的首字母一直为字体为24px */
        p::first-letter {
            font-size: 24px;
        }
        /* 需求二：让文章的第一行添加背景色灰色 */
        p::first-line {
            background-color: #ccc;
        }
        /* 需求三：让选中的内容，字体为红色 （此时我鼠标选中  ”我们“）*/
        ::selection{
          color: red;
        }
        /* 需求四：在元素开始的位置前+'你好' */
        p::before{
            content: '你好';
            color:red
        }
      </style>
    </head>
    <body>
      <p>hello</p>
      <p>world，我们要相信明天会更好。</p>
    </body>
  </html>
  ```

  

## 交集选择器

**选择器1选择器2{css样式}**

*之间没有任何东西隔开，紧挨着的*

* 找到标签中既能被选择器1又能被选择器2选中的标签，设置样式
* **交集选择器中如果有标签选择器，标签选择器必须放在前面**

```html
<style>
    p.one{
        color: red;
    }
</style>
<body>
    <p class="one">
        这个元素会被应用样式
    </p>
    <div>
        这个元素不会被应用样式
    </div>
</body>
```

## 并集选择器

**选择器1,选择器2{css样式}**

* 找到选择器1和选择器2选中的标签，设置样式

```html
<style>
    p,div{
        color: red;
    }
</style>
<body>
    <p>
        这个元素会被应用样式
    </p>
    <div>
        这个元素也会被应用样式
    </div>
</body>
```

## 属性选择器

|      语法      |                  用法                  | 示例                                     |
| :------------: | :------------------------------------: | ---------------------------------------- |
|     [属性]     |         选中含有指定属性的元素         | [title]{color:red; }                     |
| [属性=属性值]  |   选中含有指定属性和指定属性值的元素   | [title=ab]{color: green;}                |
| [属性^=属性值] | 选中含有指定属性和指定属性值开头的元素 | [title^=ab]{ background-color: yellow; } |
| [属性$=属性值] | 选中含有指定属性和指定属性值结尾的元素 | [title$=ab]{ font-size: 30px; }          |
| [属性*=属性值] |  选中指定属性名和含有指定属性值的元素  | [title*=c]{background-color: green; }    |

```html
<body>
    <h1 title="a">《出塞》</h1>
    <h2 title="ab">唐·王昌龄</h2>
    <p title="abc">秦时明月汉时关，</p>
    <p title="abab">万里长征人未还。</p>
    <p title="c">但使龙城飞将在，</p>
    <p>不教胡马度阴山。</p>
</body>
```

## 关系选择器

|   语法    |                用法                | 示例                                |
| :-------: | :--------------------------------: | ----------------------------------- |
| 父亲>儿子 |            儿子单个选择            | div > span{color: blue; }           |
| 祖先 后代 |            后代全部选择            | div span{ color: red;  }            |
|   兄+弟   | 相邻兄弟选择(必须是兄弟且严格相邻) | div + span{ color: greenyellow;   } |
|   兄~弟   |            全部兄弟选择            | p ~ span{color: black; }            |

```html
<body>
    <div>
        我是div
        <p>
            我是div中的p
            <span>我是p中的span</span>
        </p>
        <div>我是div中的div</div>
        <span>我是div中的span1</span>
        <span>我是div中的span2</span>
 
    </div>
    <span>我是div之外的span1</span>
    <span>我是div之外的span2</span>
 
</body>
```



# 盒子模型

## css的居中方式

### 水平居中

* **内联元素**：利用 `text-align: center` 可以实现在块级元素内部的内联元素水平居中：
* **块级元素**:通过把固定宽度块级元素的`margin-left`和`margin-right`设成`auto`。 `margin: 0 auto;`;
* **多块级元素**:
  1. 利用`inline-block`。设置块级元素的显示类型为inline-block和父容器的text-align属性；
  2. 利用`display: flex`。弹性布局，设置弹性盒子元素在主轴（横轴）方向上的对齐方式，子元素水平居中显示   `display: flex;justify-content: center;`

### 垂直居中：

* **单行内联元素**:设置内联元素的高度(`height`)和行高(`line-height`)相等，从而使元素垂直居中`.height: 120px;line-height: 120px;`
* **多行元素**:
  1. **利用表布局**:父元素：`display: table;`子元素：`display: table-cell;vertical-align: middle;`;
  2. **利用flex布局**:父元素：`display: flex;flex-direction: column;justify-content: center;`
  3. 利用“精灵元素”:父容器内放一个100%高度的伪元素，让`文本和伪元素垂直对齐`;
* **块级元素**:
  1. 固定高度的块级元素:绝对定位元素距离顶部50%，并设置margin-top向上偏移元素高度的一半;
  2. 未知高度的块级元素:CSS3中的transform属性向Y轴反向偏移50%。父级相对定位。子级绝对定位；top:50%;transform:translateY(-50%);

### 水平垂直居中

* **固定宽高元素**:通过margin平移元素整体宽度的一半;
* **未知宽高元素**:
  1. 利用2D变换，在水平和垂直两个方向都向反向平移宽高的一半。父级相对定位。子级绝对定位；`top:50%;left: 50%;transform:translate(-50%，-50%);）`;
  2. 利用flex布局（父元素：`display: flex;justify-content: center;align-items: center`;）

## px,em,rem,%

* **px像素(Pixel)**:相对长度单位。像素px是相对于显示器屏幕分辨率而言的(特点: IE无法调整那些使用px作为单位的字体大小);
* **em是相对长度单位**:相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。特点: em的值并不是固定的; em会继承父级元素的字体大小。
* **rem是CSS3新增的一个相对单位**。使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素。1rem等于html根元素设定font-size的px值；

**总结**:如果你的用户群都使用最新版的浏览器，那推荐使用rem,如果要考虑兼容性，那就使用px,或者两者同时使用。em是以自身父容器为参考对象的，而rem直接以HTML为参考对象的，在多层嵌套的情况下使用em很容易出现问题。所以rem更适合移动式开发。

## fixed ，relative ， absolute

* static（静态定位）：默认值。没有定位，元素出现在正常的流中（忽略 top, bottom, left, right 或者 z-index 声明）;
* relative（相对定位）：生成相对定位的元素，通过top,bottom,left,right的设置相对于其正常（原先本身）位置进行定位。可通过z-index进行层次分级。
* absolute（绝对定位）：生成绝对定位的元素，相对于 static 定位以外的第一个父元素进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。可通过z-index进行层次分级。
* fixed（固定定位）：生成绝对定位的元素，相对于浏览器窗口进行定位。元素的位置通过 "left", "top", "right" 以及 "bottom" 属性进行规定。可通过z-index进行层次分级。

# CSS3  转换

## transform

CSS3 转换可以对元素进行移动、缩放、转动、拉长或拉伸。

转换的效果是让某个元素改变形状，大小和位置。

CSS属性：`transform`

* transform:适用于2D或3D转换的元素;
* transform-origin:允许您更改转化元素位置

## translate()

CSS3 2D 转换, translate()方法，根据左(X轴)和顶部(Y轴)位置给定的参数，从当前元素位置移动。

`transform: translate(50px,100px);`

## rotate() 

CSS3 2D 转换, rotate()方法，在一个给定度数顺时针旋转的元素。负值是允许的，这样是元素逆时针旋转。

`transform: rotate(30deg);`

## scale()

CSS3 2D 转换, scale()方法，该元素增加或减少的大小，取决于宽度（X轴）和高度（Y轴）的参数：

`transform: scale(2,3);/* 转变宽度为原来的大小的2倍，高度为原来的大小的3倍*/`

## skew() 

CSS3 2D 转换, 包含两个参数值，分别表示X轴和Y轴倾斜的角度，如果第二个参数为空，则默认为0，参数为负表示向相反方向倾斜。

- skewX(<angle>);表示只在X轴(水平方向)倾斜。
- skewY(<angle>);表示只在Y轴(垂直方向)倾斜。

`transform: skew(30deg,20deg);/*元素在 X 轴和 Y 轴上倾斜20度 30度*/`

## matrix()

CSS3 2D 转换, matrix()方法和2D变换方法合并成一个。

matrix 方法有六个参数，包含旋转，缩放，移动（平移）和倾斜功能。

`transform:matrix(0.866,0.5,-0.5,0.866,0,0);`

## rotateX()

CSS 3D 转换,rotateX()方法，围绕其在一个给定度数X轴旋转的元素

`transform: rotateX(120deg);`

## rotateY() 

CSS 3D 转换 ,rotateY()方法，围绕其在一个给定度数Y轴旋转的元素。

`transform: rotateY(130deg);`

##  CSS 3D 其他方法

CSS 3D 转换在CSS 2D转换基础上添加了一个z轴的参数，方法基本类型

CSS 3D  其他**属性**

* transform-style：规定被嵌套元素如何在 3D 空间中显示。（例如属性值为preserve-3d时，以y轴旋转向前转的显示，向后转可以被其他元素隐藏）；

  * flat：表示所有子元素在2D平面呈现。
  * preserve-3d：表示所有子元素在3D空间中呈现

* perspective：规定 3D 元素的透视效果，多少像素的3D元素是从视图的perspective属性定义。

  不设置该属性时默认值为`0px`,元素3D旋转后的效果不明显，相当以水平视角观察。设置perspective后相当于有一个窗口来观察3D转换元素，其效果会随着值变。

  * 值以元素距离视图的距离，以像素计。

* perspective-origin：属性定义 3D 元素所基于的 X 轴和 Y 轴。该属性允许您改变 3D 元素的底部位置。相当于变化观察元素的角度

  * `perspective-origin: x-axis y-axis;/*默认50%，可取left、center、right、length、%*/`

* backface-visibility：定义元素在不面对屏幕时是否可见。

  * visible：背面是可见的；
  * hidden：背面是不可见的；

# CSS3 过渡

CSS3 过渡是元素从一种样式**逐渐**改变为另一种的效果，及样式改变的全过程可以被我们观察到；

通过`transition`属性来定义过渡过程中一种样式要怎样变为另一种；

要实现这一点，必须规定两项内容：

- 指定要添加效果的CSS属性；
- 指定效果的持续时间；

`transition: width 2s;/*宽度属性的过渡效果，时长为 2 秒。要添加多个样式的变换效果，添加的属性由逗号分隔*/`

过渡属性：

* transition-property：规定应用过渡的 CSS 属性的名称；
* transition-duration：定义过渡效果花费的时间。默认是 0；
* transition-timing-function：规定过渡效果的时间曲线。默认是 "ease/cubic-bezier(0.25,0.1,0.25,1)"；
* transition-delay：规定过渡效果何时开始。默认是 0；
* transition：简写属性，用于在一个属性中设置四个过渡属性；

# CSS3 动画

动画是使元素从一种样式逐渐变化为另一种样式的效果，可以改变任意多的样式任意多的次数。

CSS3 可以创建动画，它可以取代许多网页动画图像、Flash 动画和 JavaScript 实现的效果。

通过`@keyframes `规则是创建动画，用百分比来规定变化发生的时间，或用关键词 "from" 和 "to"，等同于 0% 和 100%。

0% 是动画的开始，100% 是动画的完成。

@keyframes 规则内指定一个 CSS 样式和动画将逐步从目前的样式更改为新的样式。

```css
@keyframes myfirst
{
    from {background: red;}
    to {background: yellow;}
}

@keyframes myfirst1
{
    0%   {background: red;}
    25%  {background: yellow;}
    50%  {background: blue;}
    100% {background: green;}
}
```

当在 **@keyframes** 创建动画，把它绑定到一个选择器，否则动画不会有任何效果。

指定至少这两个CSS3的动画属性绑定向一个选择器：

- 规定动画的名称
- 规定动画的时长

```css
div
{
    animation: myfirst 5s;
}
```









