---
title: jade、ejs模板库
date: 2019-09-10 16:36:44
categories:
- Node
tags: [Node.js]
---
模板引擎：生成页面

主要的：
>jade-破坏式、侵入式、强依赖
>ejs-温和式、非侵入式、 弱依赖


### 安装jade插件
npm install jade


jade写法:
* 根据缩进，规定层级
* 属性放在()里面，逗号分隔
* a标签文本内容前有一个空格
* style两种写法：普通属性写法;用json。
* class两种写法:普通属性写法;用数组。
* &attribute

jade1.js
```bash
const jade = require('jade');
//字符串：var str = jade.render('njfnejrfbjreknve');
//文件：
var str = jade.renderfile('./views/1.jade',{pertty:true});
console.log(str);
```

1.jade
```bash
html
    head
        style
        script(src="a.js")
        link(href='a.css',rel='stylesheet')
    body
        div.box
        div#div1
        div(style="width:200px;height:200px;",class="aaa bbb")
            ul
                li
                    input(type='text',id="test1")
                li
                    a(href='http://baidu.com') 百度
                li
            div(style={width:'200px',height:'200px';},class=['aaa','bbb'])
```
### jade模板库高级语法
* 识别单双标签，自定义标签默认为双标签。
![01.png](01.png)
* "|"或"."用来原样输出东西。
![02.png](02.png)
![03.png](03.png)
* include引入文件
![04.png](04.png)
* 使用变量：#{}  或"="
```bash
1.js
    const jade= require('jade');
    console.log(jade.renderfile('./view/1.jade',{pretty:true,name:'blue'}))

1.jade
    div 我的名字#{name}
    div=name
```
* "-"代表：我是一段代码
![05.png](05.png)
* 循环：
```bash
1.jade
html 
    head
        body
            .for(var i=0;i<arr.length;i++)
            div=arr[i]

1.js
console.log(jade.renderfile('./view/1.jade',{pretty:true,
    arr:['aaa','bbb','ccc']
}))

```
* 非转义：标签原样输出：！
```bash
1.jade
html 
    head
        body
            div!=content

1.js
console.log(jade.renderfile('./view/1.jade',{pretty:true,
    content:'<h2>mwenfoirefwo</h2>'
}))

```
* 生成html文件
![06.png](06.png)


### 安装ejs插件
npm install ejs

ejs1.js
```bash
const ejs = require('ejs');
var str = ejs.renderfile('./views/1.ejs',{name:'blue'},function(err,data){
    if(err)
        console.log('编译失败')
    else
        console.log(data)
});
console.log(str);
```
1.ejs
```bash
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
    我的名字叫：<%= name %>
</body>
</html>

```
### ejs模板库高级语法
* 灵活，支持各类型数据
![07.png](07.png)
* 循环
![08.png](08.png)
* "="转义输出,"-"非转义输出
* <% include ./a.txt %>
![09.png](09.png)