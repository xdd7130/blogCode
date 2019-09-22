---
title: node基础(三)
date: 2019-08-10 17:41:11
categories:
- Node
tags: [Node.js]
---
### 什么是node.js
编写高性能网络服务器的JS工具包（用js开发服务端程序）
单线程、异步、事件驱动
特点：快、耗内存
#### 框架
express、koa、Hapi、以及express基础上的sails

### node的安装
http://nodejs.cn/
是否成功：
```
node -v  
npm -v
```


#### 第一个hello程序
```bash
var http = require('http');
http.createServer(function(request,response){
    response.writeHead(200,{'Content-Type': 'text/html; charset=utf-8'});
    if (request.url!=="/favicon.ico") {   //清除第二次访问
        console.log('访问');
        response.write('hello,world');
        response.end('你好，世界');
    }
}).listen(8000);
console.log('Server running at http://localhost:8000/');
```

### 调用函数
* 调用本地函数
* 调用其他js文件中的函数

```bash
var http = require('http');
 var otherfun = require("./models/other_func.js");
 http.createServer(function(request,response){
    response.writeHead(200,{'Content-Type': 'text/html; charset=utf-8'});
    if (request.url!=="/favicon.ico") {
        //fun1(response);
        otherfun.fun2(response);
        otherfun.fun3(response);

          //用字符串调用对应函数
        // otherfun.fun2(response);
        // otherfun.fun3(response);

        funname = 'fun3';
        otherfun[funname](response);

        response.end('');
    }
}).listen(8000);
console.log('Server running at http://10.75.68.123:8000/');

function fun1(res){       // 本文件函数
    console.log("fun1");
    res.write("hello,我是fun1");
}
```


关于被调用的函数：
```bash
//只支持一个函数
function fun2(res){
    console.log("fun2");
    res.write("hello,我是fun2");
}
module.exports = fun2; 
```

```bash
    //支持多个函数
module.exports={
    fun2:function(res){
        console.log("fun2");
        res.write("hello,我是fun2");
    },
    fun3:function(res){
        console.log("fun3");
        res.write("hello,我是fun3");
    }

} 
```
### 调用模块
```bash
 var http = require('http');
 // var User = require("./models/User");
 var Teacher = require("./models/Teacher");

 http.createServer(function(request,response){
    response.writeHead(200,{'Content-Type': 'text/html; charset=utf-8'});
    if (request.url!=="/favicon.ico") {
        teacher = new Teacher(1,"张三",20);
        teacher.enter();
        teacher.teach(response);
        // user.id=1;
        // user.name="张三";
        // user.age=20;
        // user.enter();
        response.end('');
    }
}).listen(8000);
console.log('Server running at http://localhost:8000/');
```
### 路由初步
```bash
 var http = require('http');
 var url = require("url");
 var router = require("./router");

http.createServer(function(request,response){
    response.writeHead(200,{'Content-Type': 'text/html; charset=utf-8'});
    if (request.url!=="/favicon.ico") {
        var pathname=url.parse(request.url).pathname;
        pathname = pathname.replace(/\//,'');   //替换掉前面的"/"
        router[pathname](request,response);
        console.log(pathname);
        response.end('');
    }
}).listen(8000);
console.log('Server running at http://10.75.68.123:8000/');
```
### 读文件
```bash
var http = require('http');
 var url = require('url');
 var router = require('./router');

 // var optfile = require("./models/optfile");

 http.createServer(function(request,response){
    response.writeHead(200,{'Content-Type': 'text/html; charset=utf-8'});
    if (request.url!=="/favicon.ico") {

        var pathname=url.parse(request.url).pathname;
        pathname = pathname.replace(/\//,'');   //替换掉前面的"/"
        router[pathname](request,response);
        
        /*
        // optfile.readfileSync('./views/login.html');
        // optfile.readFile('./views/login.html');
        function recall(data){
            response.write(data);
            response.end('ok');   //不写则没有http协议尾
        }
        optfile.readFile('./views/login.html',recall);
        // optfile.readFile('C:\inetpub\wwwroot\HN_NTZJS01/index.html',recall);


    
        console.log("主程序执行完毕");
        */

    }
}).listen(8000);
console.log('Server running at http://localhost:8000/')
```

待续。。。