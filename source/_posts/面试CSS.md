---
title: CSS
date: 2018-12-26 17:28:15
categories: 
- Web前端
tags: [CSS]
---


# 响应式布局的常用解决方案对比(媒体查询、百分比、rem和vw/vh）
简要介绍：前端开发中，静态网页通常需要适应不同分辨率的设备，常用的自适应解决方案包括媒体查询、百分比、rem和vw/vh等。本文从px单位出发，分析了px在移动端布局中的不足，接着介绍了几种不同的自适应解决方案。

- px和视口
- 媒体查询
- 百分比
- 自适应场景下的rem解决方案
- 通过vw/vh来实现自适应

## px和视口
在静态网页中，我们经常用像素（px）作为单位，来描述一个元素的宽高以及定位信息。在pc端，通常认为css中,1px所表示的真实长度是固定的。
那么，px真的是一个设备无关，跟长度单位米和分米一样是固定大小的吗？

答案是否定的，下面图1.1和图1.2分别表示pc端下和移动端下的显示结果，在网页中我们设置的font-size统一为16px。
![](css01.png)
从上面两幅图的对比可以看出，字体都是16px，显然在pc端中文字正常显示，而在移动端文字很小，几乎看不到，说明在css中1px并不是固定大小，直观从我们发现在移动端1px所表示的长度较小，所以导致文字显示不清楚。

那么css中的1px的真实长度到底由什么决定呢？

为了理清楚这个概念我们首先介绍像素和视口的概念

### 像素
像素是网页布局的基础，一个像素表示了计算机屏幕所能显示的最小区域，像素分为两种类型：css像素和物理像素。

我们在js或者css代码中使用的px单位就是指的是css像素，物理像素也称设备像素，只与设备或者说硬件有关，同样尺寸的屏幕，设备的密度越高，物理像素也就越多。下表表示css像素和物理像素的具体区别：
>css像素:  为web开发者提供，在css中使用的一个抽象单位 
>物理像素 : 只与设备的硬件密度有关，任何设备的物理像素都是固定的 
那么css像素与物理像素的转换关系是怎么样的呢？为了明确css像素和物理像素的转换关系，必须先了解视口是什么。

### 视口
广义的视口，是指浏览器显示内容的屏幕区域，狭义的视口包括了布局视口、视觉视口和理想视口

(1) 布局视口（layout viewport）
布局视口定义了pc网页在移动端的默认布局行为，因为通常pc的分辨率较大，布局视口默认为980px。也就是说在不设置网页的viewport的情况下，pc端的网页默认会以布局视口为基准，在移动端进行展示。因此我们可以明显看出来，默认为布局视口时，根植于pc端的网页在移动端展示很模糊。

(2) 视觉视口（visual viewport）
视觉视口表示浏览器内看到的网站的显示区域，用户可以通过缩放来查看网页的显示内容，从而改变视觉视口。视觉视口的定义，就像拿着一个放大镜分别从不同距离观察同一个物体，视觉视口仅仅类似于放大镜中显示的内容，因此视觉视口不会影响布局视口的宽度和高度。

(3) 理想视口（ideal viewport）
理想视口或者应该全称为“理想的布局视口”，在移动设备中就是指设备的分辨率。换句话说，理想视口或者说分辨率就是给定设备物理像素的情况下，最佳的“布局视口”。

上述视口中，最重要的是要明确理想视口的概念，在移动端中，理想视口或者说分辨率跟物理像素之间有什么关系呢？

为了理清分辨率和物理像素之间的联系，我们介绍一个用DPR（Device pixel ratio）设备像素比来表示，则可以写成：

1 DPR = 物理像素／分辨率
在不缩放的情况下，一个css像素就对应一个dpr，也就是说，在不缩放

1 CSS像素 = 物理像素／分辨率
此外，在移动端的布局中，我们可以通过viewport元标签来控制布局，比如一般情况下，我们可以通过下述标签使得移动端在理想视口下布局：
```
<meta id="viewport" name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1; user-scalable=no;">
```
| 属性名 |取值 | 描述|
|-------|--------|--------|
| width |  正整数| 定义布局视口的宽度，单位为像素|
| height | 正整数 |定义布局视口的高度，单位为像素，很少使用|
| initial-scale |  [0,10]|  初始缩放比例，1表示不缩放|
| minimum-scale|  [0,10] | 最小缩放比例|
| maximum-scale|  [0,10] | 最大缩放比例|
| user-scalable|  yes／no | 是否允许手动缩放页面，默认值为yes|
其中我们来看width属性，在移动端布局时，在meta标签中我们会将width设置称为device-width，device-width一般是表示分辨率的宽，通过width=device-width的设置我们就将布局视口设置成了理想的视口。
### px与自适应
上述我们了解到了当通过viewport元标签，设置布局视口为理想视口时，1个css像素可以表示成：

1 CSS像素 = 物理像素／分辨率
我们直到，在pc端的布局视口通常情况下为980px，移动端以iphone6为例，分辨率为375 * 667，也就是说布局视口在理想的情况下为375px。比如现在我们有一个750px * 1134px的视觉稿，那么在pc端，一个css像素可以如下计算：

PC端： 1 CSS像素 = 物理像素／分辨率 = 750 ／ 980 =0.76 px
而在iphone6下：

iphone6：1 CSS像素 = 物理像素 ／分辨率 = 750 ／ 375 = 2 px
也就是说在PC端，一个CSS像素可以用0.76个物理像素来表示，而iphone6中 一个CSS像素表示了2个物理像素。此外不同的移动设备分辨率不同，也就是1个CSS像素可以表示的物理像素是不同的，因此如果在css中仅仅通过px作为长度和宽度的单位，造成的结果就是无法通过一套样式，实现各端的自适应。
## 媒体查询
在前面我们说到，不同端的设备下，在css文件中，1px所表示的物理像素的大小是不同的，因此通过一套样式，是无法实现各端的自适应。由此我们联想：

如果一套样式不行，那么能否给每一种设备各一套不同的样式来实现自适应的效果？

答案是肯定的。

使用@media媒体查询可以针对不同的媒体类型定义不同的样式，特别是响应式页面，可以针对不同屏幕的大小，编写多套样式，从而达到自适应的效果。举例来说：
```
@media screen and (max-width: 960px){
    body{
      background-color:#FF6699
    }
}

@media screen and (max-width: 768px){
    body{
      background-color:#00FF66;
    }
}

@media screen and (max-width: 550px){
    body{
      background-color:#6633FF;
    }
}

@media screen and (max-width: 320px){
    body{
      background-color:#FFFF00;
    }
}
```
上述的代码通过媒体查询定义了几套样式，通过max-width设置样式生效时的最大分辨率，上述的代码分别对分辨率在0～320px，320px～550px，550px～768px以及768px～960px的屏幕设置了不同的背景颜色。

通过媒体查询，可以通过给不同分辨率的设备编写不同的样式来实现响应式的布局，比如我们为不同分辨率的屏幕，设置不同的背景图片。比如给小屏幕手机设置@2x图，为大屏幕手机设置@3X图，通过媒体查询就能很方便的实现。

但是媒体查询的缺点也很明显，如果在浏览器大小改变时，需要改变的样式太多，那么多套样式代码会很繁琐。
## 百分比
除了用px结合媒体查询实现响应式布局外，我们也可以通过百分比单位 " % " 来实现响应式的效果。

比如当浏览器的宽度或者高度发生变化时，通过百分比单位，通过百分比单位可以使得浏览器中的组件的宽和高随着浏览器的变化而变化，从而实现响应式的效果。

为了了解百分比布局，首先要了解的问题是：

css中的子元素中的百分比（%）到底是谁的百分比？

直观的理解，我们可能会认为子元素的百分比完全相对于直接父元素，height百分比相对于height，width百分比相对于width。当然这种理解是正确的，但是根据css的盒式模型，除了height、width属性外，还具有padding、border、margin等等属性。那么这些属性设置成百分比，是根据父元素的那些属性呢？此外还有border-radius和translate等属性中的百分比，又是相对于什么呢？下面来具体分析。

1. 百分比的具体分析
（1）子元素height和width的百分比

子元素的height或width中使用百分比，是相对于子元素的直接父元素，width相对于父元素的width，height相对于父元素的height。比如：
```
<div class="parent">
  <div class="child"></div>
</div>
如果设置：
.father{
width:200px;
height:100px;
}
.child{
width:50%;
height:50%;
}
```
展示的效果为：
![](css02.png)
(2) top和bottom 、left和right

子元素的top和bottom如果设置百分比，则相对于直接非static定位(默认定位)的父元素的高度，同样

子元素的left和right如果设置百分比，则相对于直接非static定位(默认定位的)父元素的宽度。

展示的效果为：
![](css03.png)
（3）padding

子元素的padding如果设置百分比，不论是垂直方向或者是水平方向，都相对于直接父亲元素的width，而与父元素的height无关。

举例来说：
```
.parent{
  width:200px;
  height:100px;
  background:green;
}
.child{
  width:0px;
  height:0px;
  background:blue;
  color:white;
  padding-top:50%;
  padding-left:50%;
}
```
展示的效果为：
![](css04.png)
子元素的初始宽高为0，通过padding可以将父元素撑大，上图的蓝色部分是一个正方形，且边长为100px,说明padding不论宽高，如果设置成百分比都相对于父元素的width。

（4）margin

跟padding一样，margin也是如此，子元素的margin如果设置成百分比，不论是垂直方向还是水平方向，都相对于直接父元素的width。这里就不具体举例。

（5）border-radius

border-radius不一样，如果设置border-radius为百分比，则是相对于自身的宽度，举例来说：
```
  <div class="trangle"></div>
```
设置border-radius为百分比：
```
.trangle{
  width:100px;
  height:100px;
  border-radius:50%;
  background:blue;
  margin-top:10px;
}
```
展示效果为：

![](css06.gif)
2. 百分比单位布局应用
百分比单位在布局上应用还是很广泛的，这里介绍一种应用。
比如我们要实现一个固定长宽比的长方形，比如要实现一个长宽比为4:3的长方形,我们可以根据padding属性来实现，因为padding不管是垂直方向还是水平方向，百分比单位都相对于父元素的宽度，因此我们可以设置padding-top为百分比来实现，长宽自适应的长方形：
```
<div class="trangle"></div>
```
设置样式让其自适应：
```
.trangle{
  height:0;
  width:100%;
  padding-top:75%;
}
```
通过设置padding-top：75%,相对比宽度的75%，因此这样就设置了一个长宽高恒定比例的长方形，具体效果
![](css05.png)
3. 百分比单位缺点
从上述对于百分比单位的介绍我们很容易看出如果全部使用百分比单位来实现响应式的布局，有明显的以下两个缺点：
（1）计算困难，如果我们要定义一个元素的宽度和高度，按照设计稿，必须换算成百分比单位。
（2）从小节1可以看出，各个属性中如果使用百分比，相对父元素的属性并不是唯一的。比如width和height相对于父元素的width和height，而margin、padding不管垂直还是水平方向都相对比父元素的宽度、border-radius则是相对于元素自身等等，造成我们使用百分比单位容易使布局问题变得复杂。

## 自适应场景下的rem解决方案
1. rem单位

首先来看，什么是rem单位。rem是一个灵活的、可扩展的单位，由浏览器转化像素并显示。与em单位不同，rem单位无论嵌套层级如何，都只相对于浏览器的根元素（HTML元素）的font-size。默认情况下，html元素的font-size为16px，所以：
```
    1 rem = 12px
```
为了计算方便，通常可以将html的font-size设置成：
```
    html{ font-size: 62.5% }
```
这种情况下：
```
    1 rem = 10px
```

2.通过rem来实现响应式布局

rem单位都是相对于根元素html的font-size来决定大小的,根元素的font-size相当于提供了一个基准，当页面的size发生变化时，只需要改变font-size的值，那么以rem为固定单位的元素的大小也会发生响应的变化。
因此，如果通过rem来实现响应式的布局，只需要根据视图容器的大小，动态的改变font-size即可。
```
function refreshRem() {
    var docEl = doc.documentElement;
    var width = docEl.getBoundingClientRect().width;
    var rem = width / 10;
    docEl.style.fontSize = rem + 'px';
    flexible.rem = win.rem = rem;
}
win.addEventListener('resize', refreshRem);
```
上述代码中将视图容器分为10份，font-size用十分之一的宽度来表示，最后在header标签中执行这段代码，就可以动态定义font-size的大小，从而1rem在不同的视觉容器中表示不同的大小，用rem固定单位可以实现不同容器内布局的自适应。

3. rem2px和px2rem

如果在响应式布局中使用rem单位，那么存在一个单位换算的问题，rem2px表示从rem换算成px，这个就不说了，只要rem乘以相应的font-size中的大小，就能换算成px。更多的应用是px2rem，表示的是从px转化为rem。

比如给定的视觉稿为750px（物理像素），如果我们要将所有的布局单位都用rem来表示，一种比较笨的办法就是对所有的height和width等元素，乘以相应的比例，现将视觉稿换算成rem单位，然后一个个的用rem来表示。另一种比较方便的解决方法就是，在css中我们还是用px来表示元素的大小，最后编写完css代码之后，将css文件中的所有px单位，转化成rem单位。

px2rem的原理也很简单，重点在于预处理以px为单位的css文件，处理后将所有的px变成rem单位。可以通过两种方式来实现：

1） webpack loader的形式：
```
npm install px2rem-loader
```
在webpack的配置文件中：
```
module.exports = {
  // ...
  module: {
    rules: [{
      test: /\.css$/,
      use: [{
        loader: 'style-loader'
      }, {
        loader: 'css-loader'
      }, {
        loader: 'px2rem-loader',
        // options here
        options: {
          remUni: 75,
          remPrecision: 8
        }
      }]
    }]
  }
}
```
2）webpack中使用postcss plugin
```
npm install postcss-loader
```
在webpack的plugin中:
```
var px2rem = require('postcss-px2rem');

module.exports = {
  module: {
    loaders: [
      {
        test: /\.css$/,
        loader: "style-loader!css-loader!postcss-loader"
      }
    ]
  },
  postcss: function() {
    return [px2rem({remUnit: 75})];
  }
}
```
4. rem 布局应用举例

网易新闻的移动端页面使用了rem布局，具体例子如下：

![](css07.gif)

5. rem 布局的缺点

通过rem单位，可以实现响应式的布局，特别是引入相应的postcss相关插件，免去了设计稿中的px到rem的计算。rem单位在国外的一些网站也有使用，这里所说的rem来实现布局的缺点，或者说是小缺陷是：

在响应式布局中，必须通过js来动态控制根元素font-size的大小。

也就是说css样式和js代码有一定的耦合性。且必须将改变font-size的代码放在css样式之前。

## 通过vw/vh来实现自适应
1. 什么是vw/vh ?

css3中引入了一个新的单位vw/vh，与视图窗口有关，vw表示相对于视图窗口的宽度，vh表示相对于视图窗口高度，除了vw和vh外，还有vmin和vmax两个相关的单位。各个单位具体的含义如下：

|单位  |含义|
|---|---|
|vw  |相对于视窗的宽度，视窗宽度是100vw|
|vh  |相对于视窗的高度，视窗高度是100vh|
|vmin|    vw和vh中的较小值|
|vmax|    vw和vh中的较大值|
这里我们发现视窗宽高都是100vw／100vh，那么vw或者vh，下简称vw，很类似百分比单位。vw和%的区别为：

|单位|  含义|
|---|---|
|% |  大部分相对于祖先元素，也有相对于自身的情况比如（border-radius、translate等)|
|vw/vh|   相对于视窗的尺寸|
从对比中我们可以发现，vw单位与百分比类似，单确有区别，前面我们介绍了百分比单位的换算困难，这里的vw更像"理想的百分比单位"。任意层级元素，在使用vw单位的情况下，1vw都等于视图宽度的百分之一。

2. vw单位换算

同样的，如果要将px换算成vw单位，很简单，只要确定视图的窗口大小（布局视口），如果我们将布局视口设置成分辨率大小，比如对于iphone6/7 375*667的分辨率，那么px可以通过如下方式换算成vw：
```
1px = （1/375）*100 vw
```
此外，也可以通过postcss的相应插件，预处理css做一个自动的转换，postcss-px-to-viewport可以自动将px转化成vw。
postcss-px-to-viewport的默认参数为：
```
var defaults = {
  viewportWidth: 320,
  viewportHeight: 568, 
  unitPrecision: 5,
  viewportUnit: 'vw',
  selectorBlackList: [],
  minPixelValue: 1,
  mediaQuery: false
};
```
通过指定视窗的宽度和高度，以及换算精度，就能将px转化成vw。

3. vw/vh单位的兼容性
可以在[https://caniuse.com/](https://caniuse.com/) 查看各个版本的浏览器对vw单位的支持性。
从上图我们发现，绝大多数的浏览器支持vw单位，但是ie9-11不支持vmin和vmax，考虑到vmin和vmax单位不常用，vw单位在绝大部分高版本浏览器内的支持性很好，但是opera浏览器整体不支持vw单位，如果需要兼容opera浏览器的布局，不推荐使用vw。

## 小结
本文介绍在布局中常用的单位，比如px、%、rem和vw等等，以及不同的单位在响应式布局中的优缺点。

# 画一条0.5px的线
- 采用meta viewport的方式

- 采用 border-image的方式

- 采用transform: scale()的方式

# link标签和import标签的区别
- link属于html标签，而@import是css提供的
- 页面被加载时，link会同时被加载，而@import引用的css会等到页面加载结束后加载。
- link是html标签，因此没有兼容性，而@import只有IE5以上才能识别。
- link方式样式的权重高于@import的。

# transition和animation的区别
Animation和transition大部分属性是相同的，他们都是随时间改变元素的属性值，他们的主要区别是transition需要触发一个事件才能改变属性，而animation不需要触发任何事件的情况下才会随时间改变属性值，并且transition为2帧，从from .... to，而animation可以一帧一帧的。

# Flex布局
文章链接：
http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?utm_source=tuicool（语法篇）
http://www.ruanyifeng.com/blog/2015/07/flex-examples.html（实例篇）

Flex是Flexible Box的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。
布局的传统解决方案，基于盒状模型，依赖 display属性 + position属性 + float属性。它对于那些特殊布局非常不方便，比如，垂直居中就不容易实现。

简单的分为容器属性和元素属性
容器的属性：

flex-direction：决定主轴的方向（即子item的排列方法）
.box {
flex-direction: row | row-reverse | column | column-reverse;
}
flex-wrap：决定换行规则
.box{
flex-wrap: nowrap | wrap | wrap-reverse;
}
flex-flow：
.box {
flex-flow: || ;
}
justify-content：对其方式，水平主轴对齐方式
align-items：对齐方式，竖直轴线方向
项目的属性（元素的属性）：

order属性：定义项目的排列顺序，顺序越小，排列越靠前，默认为0
flex-grow属性：定义项目的放大比例，即使存在空间，也不会放大
flex-shrink属性：定义了项目的缩小比例，当空间不足的情况下会等比例的缩小，如果定义个item的flow-shrink为0，则为不缩小
flex-basis属性：定义了在分配多余的空间，项目占据的空间。
flex：是flex-grow和flex-shrink、flex-basis的简写，默认值为0 1 auto。
align-self：允许单个项目与其他项目不一样的对齐方式，可以覆盖align-items，默认属性为auto，表示继承父元素的align-items
比如说，用flex实现圣杯布局

# BFC（块级格式化上下文，用于清除浮动，防止margin重叠等）
直译成：块级格式化上下文，是一个独立的渲染区域，并且有一定的布局规则。

BFC区域不会与float box重叠
BFC是页面上的一个独立容器，子元素不会影响到外面
计算BFC的高度时，浮动元素也会参与计算
哪些元素会生成BFC：

根元素
- float不为none的元素
- position为fixed和absolute的元素
- display为inline-block、table-cell、table-caption，flex，inline-flex的元素
- overflow不为visible的元素


>BFC定义

1、 box
box是css布局的基本单位，元素的类型和display属性决定了box的类型。不同的类型的盒子会参与不同的formatting context。
block-level box :display属性为block、list-item(作为列表显示的元素)、table的元素会生成block-level box。
2、formatting context
它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用

BFC（block formatting context）为块级格式化上下文，他是一个独立渲染区域，并且只有block-level box参与，规定了block-level box内部的布局方式。

>布局规则

- 1、内部的box会在垂直方向上一个接一个的放置
```bash
<div class="div1"></div>
<div class="div2"></div>
```
- 2、同一个bfc内，两个相邻box元素的margin会产生重叠，重叠距离为数值大的margin值
给div1设置margin: 20px 0,给div2设置margin: 30px 0,此时div1和div2之间的间距为30px。

解决方式  让两个元素不属于同一个BFC
3、BFC区域不会与浮动元素重叠
```bash
<div class="left"></div>
<div class="main"></div>
```
此时main元素没有产生bfc,left元素浮动，效果如下：
给main元素设置overflow:hidden变为bfc,效果如下：
- 4、计算BFC的高度时，浮动元素也参与计算
- 5、每个元素的margin box的左边， 与 包含块 border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此
- 6、BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素
>3、产生BFC的方式
- 1、overflow属性不是visible
- 2、float属性不为none
- 3、display属性为inline-block、table-cell、flex、inline-flex、table-caption
- 4、position为absolute、fixed
- 5、body根元素


# 关于js动画和css3动画的差异性
渲染线程分为main thread和compositor thread，如果css动画只改变transform和opacity，这时整个CSS动画得以在compositor trhead完成（而js动画则会在main thread执行，然后出发compositor thread进行下一步操作），特别注意的是如果改变transform和opacity是不会layout或者paint的。
区别：

- 功能涵盖面，js比css大
- 实现/重构难度不一，CSS3比js更加简单，性能跳优方向固定
- 对帧速表现不好的低版本浏览器，css3可以做到自然降级
- css动画有天然事件支持
- css3有兼容性问题

# 块元素和行元素
HTML可以将元素分类方式分为行内元素、块状元素和行内块状元素三种。首先需要说明的是，这三者是可以互相转换的，使用display属性能够将三者任意转换：

　　(1)display:inline;转换为行内元素

　　(2)display:block;转换为块状元素

　　(3)display:inline-block;转换为行内块状元素
块元素：独占一行，并且有自动填满父元素，可以设置margin和pading以及高度和宽度
行元素：不会独占一行，width和height会失效，并且在垂直方向的padding和margin会失
效。
1.块状元素
　　块状元素代表性的就是div，其他如p、nav、aside、header、footer、section、article、ul-li、address等等，都可以用div来实现。不过为了可以方便程序员解读代码，一般都会使用特定的语义化标签，使得代码可读性强，且便于查错。

　　块状元素特征：(1)能够识别宽高

　　　　　　　　　(2)margin和padding的上下左右均对其有效

　　　　　　　　　(3)可以自动换行

　　　　　　　　　(4)多个块状元素标签写在一起，默认排列方式为从上至下
2.块状元素
　　块状元素代表性的就是div，其他如p、nav、aside、header、footer、section、article、ul-li、address等等，都可以用div来实现。不过为了可以方便程序员解读代码，一般都会使用特定的语义化标签，使得代码可读性强，且便于查错。

　　块状元素特征：(1)能够识别宽高

　　　　　　　　　(2)margin和padding的上下左右均对其有效

　　　　　　　　　(3)可以自动换行

　　　　　　　　　(4)多个块状元素标签写在一起，默认排列方式为从上至下
3.行内块状元素
　　行内块状元素综合了行内元素和块状元素的特性，但是各有取舍。因此行内块状元素在日常的使用中，由于其特性，使用的次数也比较多。

　　行内块状元素特征：(1)不自动换行

　　　　　　　　　　　(2)能够识别宽高

　　　　　　　　　　　(3)默认排列方式为从左到右

# 多行元素的文本省略号
```
display: -webkit-box
-webkit-box-orient:vertical
-webkit-line-clamp:3
overflow:hidden
```
# visibility=hidden, opacity=0，display:none
opacity=0，该元素隐藏起来了，但不会改变页面布局，并且，如果该元素已经绑定一些事件，如click事件，那么点击该区域，也能触发点击事件的visibility=hidden，该元素隐藏起来了，但不会改变页面布局，但是不会触发该元素已经绑定的事件display=none，把元素隐藏起来，并且会改变页面布局，可以理解成在页面中把该元素删除掉一样。
# 双边距重叠问题（外边距折叠）
多个相邻（兄弟或者父子关系）普通流的块元素垂直方向marigin会重叠

折叠的结果为：

两个相邻的外边距都是正数时，折叠结果是它们两者之间较大的值。
两个相邻的外边距都是负数时，折叠结果是两者绝对值的较大值。
两个外边距一正一负时，折叠结果是两者的相加的和。

# position

- static  默认。位置设置为 static 的元素，它始终会处于页面流给予的位置（static 元素会忽略任何 top、bottom、left 或 right 声明）。
- relative  位置被设置为 relative 的元素，可将其移至相对于其正常位置的地方，因此 "left:20" 会将元素移至元素正常位置左边 20 个像素的位置。
- absolute  位置设置为 absolute 的元素，可定位于相对于包含它的元素的指定坐标。此元素的位置可通过 "left"、"top"、"right" 以及 "bottom" 属性来规定。
- fixed 位置被设置为 fixed 的元素，可定位于相对于浏览器窗口的指定坐标。此元素的位置可通过 "left"、"top"、"right" 以及"bottom" 属性来规定。不论窗口滚动与否，元素都会留在那个位置。工作于 IE7（strict 模式）。
- sticky 基于用户的滚动位置来定位。粘性定位的元素是依赖于用户的滚动，在position:relative 与 position:fixed定位之间切换。它的行为就像position:relative;而当页面滚动超出目标区域时，它的表现就像 position:fixed;，它会固定在目标位置。元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。

# animation
语法
```bash
@keyframes animationname {keyframes-selector {css-styles;}}
```
下面的表格列出了 @keyframes 规则和所有动画属性：

|属性|  描述|  CSS|
|----|----|--|
|@keyframes  |规定动画。 |3|
|animation |所有动画属性的简写属性，除了 animation-play-state 属性。 |3|
|animation-name  |规定 @keyframes 动画的名称。|  3|
|animation-duration | 规定动画完成一个周期所花费的秒或毫秒。默认是 0。| 3| 
|animation-timing-function |规定动画的速度曲线。默认是 "ease"。 |3|
|animation-delay |规定动画何时开始。默认是 0。 |3|
|animation-iteration-count |规定动画被播放的次数。默认是 1。 |3|
|animation-direction| 规定动画是否在下一周期逆向地播放。默认是 "normal"。 | 3|
|animation-play-state  |规定动画是否正在运行或暂停。默认是 "running"。 | 3|
|animation-fill-mode |规定对象动画时间之外的状态。 | 3|

# 伪元素伪类
>css3为了区分伪类和伪元素，伪元素采用双冒号写法。

伪类 -- :hover, :link, :active, :visited, :first-child, :last-child, nth-child(n), :not(), :focus
伪元素 -- ::before, ::after, ::first-letter, ::first-line, ::selection
>定义:

伪类：用于向某些选择器添加特殊的效果
伪元素：用于将特殊的效果添加到某些选择器
>区别

例子来理解
```bash
<p>
    <em>This</em>
    <em>is a text</em>
</p>
```
![](weilei01.png)
如果我们想要第一个em标签字体颜色变红怎么做呢 
使用我们熟悉的伪类很简单
```bash
em:first-child {
    color: red;
}
```
![](weilei02.png)
但是如果不存在伪类我们怎么做呢 
这是我们就需要为第一个em标签添加类
```
<p>
    <em class="first-child">This</em>
    <em>is a text</em>
</p>

em.first-child {
    color: red;
}
```
可以实现同样的效果
```
<p>
    <em>This</em>
    <em>is a text</em>
</p>
```
还是这个例子 
现在我想让这个段落的第一个字母变红 

这回我们需要使用伪元素
```
p::first-letter {
    color: red;
}
```
![](weilei03.png)
同样假设伪元素不存在的情况 
这时我们只能嵌套span标签来实现
```
<p>
    <em><span>T</span>his</em>
    <em>is a text</em>
</p>

p span {
    color: red;
}
```
看到这里，相信大家已经清楚了为什么一个叫做伪类，一个叫做伪元素 
伪类的效果可以通过添加实际的类来实现 
伪元素的效果可以通过添加实际的元素来实现 
<font color="red">它们的本质区别就是是否抽象创造了新元素</font>
伪元素是可以设置content属性的，类似于标签，但是伪类只能设置样式不能设置内容。
**注意**
伪类就像真正的类一样，可以叠加使用 
没有数量上限，只要不是互斥的 
比如这样
```bash
em:first-child:hover {
    color: red;
}
```
但注意，这里是“与”的关系 
也就是说既要满足“first-child”第一个子元素 
又要满足“hover”光标悬浮
伪元素就要严格的多 
伪元素在一个选择器中只能出现一次，并且只能出现在末尾 
（这里有同学误会了，所以我作出了修改） 
像下面的样式是无法生效的
```bash
p::first-letter:hover {  /*错误的写法：伪元素不是末尾*/
    color: red;
}
p::first-letter::selection {  /*错误的写法：伪元素出现了多个*/
    color: red;
}
```
关于它们的优先级 
在计算权重的时候 
伪类与类优先级相同 
伪元素与标签优先级相同

**总结**
- 伪类与伪元素都是用于向选择器加特殊效果
- 伪类与伪元素的本质区别就是是否抽象创造了新元素
- 伪类只要不是互斥可以叠加使用
- 伪元素在一个选择器中只能出现一次，并且只能出现在末尾
- 伪类与伪元素优先级分别与类、标签优先级相同