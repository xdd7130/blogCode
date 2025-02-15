---
title: SVG
date: 2020-07-01 14:16:35
categories: 
- SVG
tags: [SVG]
---
### 1. 可缩放矢量图形 Scalable Vector Graphics SVG
#### 什么是SVG
SVG 指可伸缩矢量图形 (Scalable Vector Graphics)
SVG 用来定义用于网络的基于矢量的图形
SVG 使用 XML 格式定义图形
SVG 图像在放大或改变尺寸的情况下其图形质量不会有所损失
SVG 是万维网联盟的标准
SVG 与诸如 DOM 和 XSL 之类的 W3C 标准是一个整体
#### 特点及优势
SVG 可被非常多的工具读取和修改（比如记事本）
SVG 与 JPEG 和 GIF 图像比起来，尺寸更小，且可压缩性更强。
SVG 图像可在任何的分辨率下被高质量地打印
SVG 可在图像质量不下降的情况下被放大
SVG 图像中的文本是可选的，同时也是可搜索的（很适合制作地图）


### 使用方式
#### 在浏览器直接打开
xml 是浏览器能读取的格式，但如果希望 svg 能在浏览器中渲染出来，需要使用 xmlns 声明渲染规则。所以必须使用 xmlns="http://www.w3.org/2000/svg"。

#### 内嵌到HTML中：
，<svg> 标签并没有使用 xmlns="http://www.w3.org/2000/svg" 声明命名空间，这是因为 HTML 5 文档使用 <!DOCTYPE html> 标记，它允许跳过 SVG 命名空间声明，HTML 解析器会自动识别 SVG 元素和它的子元素
在css背景图中使用

#### 使用img标签引入

### 坐标定位
和canvas一样，Svg以页面的左上角为 (0,0) 坐标点，它的默认坐标以像素为单位，x 轴正方向是向右，y 轴正方向是向下；如<svg width="200" height="200" viewBox="0 0 100 100">，这里定义的画布尺寸是 200*200px。但是，viewBox 属性定义了画布上可以显示的区域：从 (0,0) 点开始，100 宽*100 高的区域。这个 100*100 的区域，会放到 200*200 的画布上显示。于是就形成了放大两倍的效果。

### 元素
见链接 : [元素](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Element)

#### defs, use, g, symbol
g 是group（分组）的缩写。g元素通常用来对相关图形元素进行分组，以便统一操作，比如旋转，缩放或者添加相关样式等。
defs 重用已存储元素, 分组定义的内容直接会显示，所以defs在使用的时候会有更大的灵活性; use 用的是这些 tag 的 id，而非 defs;defs 是个声明块，声明块本身没啥重用意义，其内定义可被重用的内容集合，重用的是该集合中的一项或多项，原样的拿过来在不同位置下呈现
symbol 兼具g的分组功能和defs初始不可见的特性,能够创建自己的视窗,symbol 可以设定 width height
use 复用; 实现SVG现有图形的复用，可以复用单个SVG图形元素，也可以复用g定义的组元素

### 属性
所有 SVG 显示属性都可以作为 CSS 属性来使用

### 动画
#### SMIL 驱动
SMIL是Synchronized Multimedia Integration Language（同步多媒体集成语言）的首字母缩写简称，是一种标准。SVG借鉴了这种标准
用: Set, animate, animateColor, animateTransform, animateMotion元素实现动画

#### JavaScript 驱动
一般应用js操作属性实现
#### CSS 驱动
使用 css @keyframes animationName 定义动画

选择哪个?

建议将CSS动画用于无变形的过渡或简单动画。尤其是在硬件加速时。CSS不需要加载其他资源（一般指三方库），并且悬停时的小变换可以为交互带来更好的效果。特别是当你不需要3d、物理体感、或进行大量堆叠动画效果时建议选用CSS。另外，CSS方便调试也是很大的一个优势。
对于较长的动画，开发时会变得非常复杂且需要花精力去调试，而CSS调整时间尺度很困难，尤其是当你需要操纵一些细微帧时，个人觉得SMIL更合适做有序的，复杂的堆叠动画群的场景。
对于变形的动画，建议使用SMIL或者第三方库: GSAP, anime.js, D3, Snap.js, SVG.js, Velocity.js。


本文代码示例: [链接](https://github.com/xdd7130/vue3_vite_cesium_ts/tree/master/src/svg)