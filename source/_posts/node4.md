---
title: node笔记(一)
date: 2019-08-11 19:48:05
categories:
- Node
tags: [Node.js]
---
### node
优势：
1、性能好
2、跟前台JS配合方便
3、NodeJS便于前端学习
### http系统模块使用
```bash
const http=require('http');
var server = http.creatServer(function(req,res){  //创建服务器
    switch(res.url){
        case "/1.html":
            res.write("向前台写东西111")；
            break; 
        case "/2.html":
            res.write("向前台写东西222")；
            break;
        default
            res.write("404")；
            break;
    }
    // res.write("向前台写东西")；
    res.end();//结束请求
})
//监听 —— 等着
//端口 —— 数字
server.listen(8888);
```
### fs文件模块
fs (file system)
```bash
//导入引用:
    const fs = require('fs');
//1.readFile(文件名，回调函数)
    fs.readFile('aaa.txt',function(err,data){
        if(err){
            console.log('读取失败')；
        }else{
            console.log(data.toString());//二进制格式用toString转换
        }
        
    })
//2.writeFile(文件名，内容，回调函数)
fs.write("bbb.txt","asdfghjkl",function(err){
    console.log(err);
})
```
与服务器结合
建立www（可以是其他名字）文件夹，用于存放请求的文件。
```bash
const http=require('http');
var server = http.creatServer(function(req,res){  //创建服务器
    //req.url = 'index.html'
    //读取:'./www/index.html'
    var file_name = './www'+req.url
    
    fs.readFile(file_name,function(err,data){ //异步
        if(err){
            res.write('读取失败')
        }else{
            res.write(data)
        }
        res.end();//结束请求，因为异步，res.end()应放在此处
    })
    
})
server.listen(8888);
```
### http数据解析（get）
前台发送请求的方式：form/ajax/jsonp
后台都是一样。唯有一点不同，请求方式不同，接受方式不一样（get/post）.下面一个实例：
GET数据解析：
* 自己切
* querystring:
* url:urlLib.parse(...,true)

server.js 普通写法
```bash
const http = require('http');
http.createServer(function(req,res){
    //req获取前台请求的数据
    var get={};
    if(req.url.indexOf('?') != -1){
        console.log(req.url); //  /aaa?user=Tom&pass=123456
        var arr = req.url.split('？')；// arr[0]='/aaa'  arr[1]='user=Tom&pass=123456'
        var url=arr[0];
        var arr2 = arr[1].split('&'); //arr2
        for(var i=0;i<arr2.length;i++){
            var arr3=arr2[i].split('=')
            get[arr3[0]]=arr3[1];
        }
    }else{
        var url=req.url;
    }
    console.log(url,get)
    res.end();
}).listen(8080);
```
server.js querystring模块写法：
```bash
  //写法如下：
    //const querystring=require('querystring');
    //var json = querystring.parse('user=Tom&pass=123456')
    //console.log(json)
//server.js改动后：
    const http = require('http');
    const querystring=require('querystring');
    http.createServer(function(req,res){
        //req获取前台请求的数据
        var get={};
        if(req.url.indexOf('?') != -1){
            console.log(req.url); //  /aaa?user=Tom&pass=123456
            var arr = req.url.split('？')；// arr[0]='/aaa'  arr[1]='user=Tom&pass=123456'
            var url=arr[0];
            get=querystring.parse(arr[1]);
        }else{
            var url=req.url;
        }
        console.log(url,get)
        res.end();
    }).listen(8080);
```
server.js url模块写法：
```bash
//url.js写法
    const urlLib=require('url');
    var obj= urlLib.parse("http://www.aaa.bbb/index.html?a=12&b=55",true);  第二个参数为true时，会把pathname等解析为json.
    console.log(obj.pathname,obj.query);
//server.js改动后：
    const http = require('http');
    http.createServer(function(req,res){
        //req获取前台请求的数据
        var obj = urlLib.parse(req.url,true);
        var url=obj.pathname;
        var get=obj.query;

        console.log(url,get)
        res.end();
    }).listen(8080);
```
form.html
```bash
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
    <form action="http://localhost:8080/aaa" method="get">
        用户：
        <input type="text" name="user" value=''> <br>
        <input type="password" name="pass" value=''> <br>
        <input type="submit" value='提交'
    </form>
</body>
</html>
```
### http数据解析（post）
POST数据接收：POST数据比GET数据大的多,根据数据量大小分段完成
```bash
    const http=require('http');
    const querystring=require('querystring');
    http.creatServer(function(req,res){
            //POST--req
        var str='';  //接受数据

            //data--有一段数据到达（很多次）
        req.on('data',function(){
            str+=data
        });
            //end--数据全部到达（一次）
        req.on('end',function(){
            var post = querystring.parse(str);
            console.log(post)
        });
    }).listen(8080);
```
### 应用1:简易httpServer搭建,用户注册登录(略)
### nodeJS模块化
#### 系统模块介绍
除了以上用到的：http、querystring、url,还有：
* Crypto 加密
* Event 事件
* Net 网络操作
* OS 操作系统信息
* Path 处理文件路径
* Stream 流操作
* Timer 定时器
* ZLIB 压缩

#### 自定义模块
关于引入模块写法：若为当前目录自己的模块，加上“./”,系统模块要加。
若想要输出东西，必须exports。
若都输出多个东西，module可实现批量输出。
* require:请求引入其他模块。
* module:批量输出。
* exports:输出。
* module.exports == exports。
* const aaa = require('./bbb.js'); 其中“.js”可省略。

npm:nodejs package manager(nodejs包管理器)
1、统计下载途径
2、自动下载依赖

npm init
npm publish
npm update
npm unpublish
npm --force unpublish


![01.png](01.png)




node_modules:放模块，可以放自己定义的模块，此时若引用，不加“./”
>总结：require引入模块，若有“./”,从当前目录找，若没有“./”，从系统模块或node_modules找。
>模块优先级：系统>node_modules,一般将自定义模块放在node_modules中。

