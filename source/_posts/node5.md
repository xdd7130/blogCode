---
title: Express
date: 2019-08-13 11:07:47
categories:
- Node
tags: [Node.js]
---
### Express框架
1.依赖中间件
2.接受请求
3.非破坏式的
4.static用法
#### 安装、配置、接受请求、响应
安装
```bash
npm install express
```
使用
* 创建服务
* 监听
* 处理请求
```bash
const express = require('express');
var server = express();
server.use('/a.html',function(req,res){
    res.send('abc');                          //send和write类似
    res.end();
});
server.use('/b.html',function(req,res){
    res.send('123');
    res.end();
});
server.listen(8080);
```
express保留了原生的功能，添加了一些方法，增强原有的功能。

#### 3种方法
get(处理get请求)\post(处理post请求)\use(get,post均可)。
```bash
const express = require('express');
var server = express();
server.get('/',function(req,res){
    console.log('get请求')
});
server.post('/',function(req,res){
    console.log('post请求')
});
server.use('/',function(req,res){
    console.log('use了')
});
server.listen(8080);
```
#### Express-static
读取静态文件
安装 npm install express-static -save

实例-登录
a.html:
![01.png](01.png)
server.js
```bash
    const express = require('express');
    const expressStatic = require('express-static');

    var server = express();
    server.listen(8080);

    //用户数据模拟
    var users={
        'blue':'123456',
        'zhangsan':'654321',
        'lisi':'987678'
    };
    server.get('/login',function(req,res){
        // urlLib.parse(req.url,true).query;
        console.log(req.query)
        var user= req.query['user'];
        var pass= req.query['pass'];

        if(users[user]==null){
            res.send({ok:false,mag:'此用户不存在'})
        }else{
            if(users[user]!=pass){
                res.send({ok:false,msg:'密码错了'})
            }else{
                res.send({ok:true,mag:'成功'})
            }
        }
    })
    server.use(expressStatic('./www'));
```
### 数据解析
get请求数据解析,无需中间件:req.query
post请求数据解析，需中间件：body-parser，首先解析数据
```base
const express = require('express');
const bodyParser = require('body-parser');

var server = express();
server.listen(8080);

server.use(bodyParser.urlencoded({
    extended:true,      //扩展模式
    limit: 2*1024       //限制2M，默认100k
}));
server.use('/',function(req,res){
    console.log(req.body)  //POST
})
```
#### 链式操作
```base
const express = require('express');

var server = express();
server.listen(8080);

//第三个参数
server.use('/',function(req,res,next){
    console.log('a')
    next();
});
server.use('/',function(req,res.next){
    console.log('b')  
})
```
中间件,类似'body-parser'
```base
const express = require('express');
const querystring = require('querystring');

var server = express();
server.listen(8080);

//第三个参数
server.use(function(req,res,next){
    var str='';
    req.on('data',function(data){
        str+=data;
    });
    req.on('end',function(data){
        req.body=querystring(str);
        next();
    });
});
server.use('/',function(req,res.next){
    console.log('b')  
})
```
### cookie、session
* a.发送cookie
res.cookie(名字,值,maxAge:毫秒,signed:true)
* b.读取cookie
cookie-parser
server.use(cookieParser('秘钥'))
server.use(function(){
    req.cookie   //未签名
    req.signedCookies   //签名版
})
* c.删除cookie
res.clearCookie(名字)


保存数据，cookie数据保存浏览器，session 数据保存在服务器。
npm install cookie-parser cookie-session 
```bash
const express = require('express');
const cookieParser = require('cookie-parser');

var server = express();
//发送cookie
server.use('/aaa/a.html',function(req,res){
    res.cookie('user','blue',{path:'/aaa','maxAge':3*24*3600*1000})
})
//读取cookie
server.use(cookieParser())；
server.use('/',function(req,res){
    console.log(req.cookie)
})
```
实现cookie加密：
```bash
const express = require('express');
const cookieParser = require('cookie-parser');

var server = express();
//cookieParser
server.use(cookieParser);
server.use('/',function(){
    req.secret='hdwifbonfjfosdjhfsj';
    res.cookie('user','blue',{signed:true});

    res.send('ok');
});
server.listen(8080);
```
session

```bash
const express = require('express');
const cookieSession = require('cookie-session');

var server = express();
//cookie
server.use(cookieParser('dnkfvbscsvius'));
server.use(cookieSession());

server.use('/',function(){
    req.secret='hdwifbonfjfosdjhfsj';
    res.cookie('user','blue',{signed:true});

    res.send('ok');
});
server.listen(8080);
```
### express框架整合
![02.png](02.png)
![03.png](03.png)

#### multer模块上传文件
npm install multer

* body-parser:解析post数据  (application/x-form-urlencoded)
server.use(bodyParse.urlencode());
* multer:解析post文件   (multipart/form-data)

```bash
//上传到磁盘  重命名

const multer = require('multer');
const pathLib = require('path');
const fs = require('fs');


//1、pathLib解析获取原始文件扩展名
    var str='c:\\wamp\www\a.html';
    var obj=path.parse(str);

    base :文件名"a.html"
    ext:扩展名"html"
    dir:路径'c:\\wamp\www'
    name:文件名"a"

//2.fs的rename重命名
    fs.rename('a.txt','b.txt',function(err){
        console.log(err)
    })
    fs.rename(老名，新名，function(err){
        ...
    })



var obj=multer({dest:'./www/upload/'})  //文件保存的位置

server.use(obj.any())       //接受任意类型文件
//只接收name=f1的文件 : server.use(obj.single('f1'))  


//req.files:指上传的文件信息，如下图：
server.use(function(req,res){
    var newName = req.file[0].path+pathLib.parse(req.file[0].originalname).ext
    fs.rename(req.file[0].path,newName,function()(err){
        if(err)
            res.send('上传失败');
        else
            res.send('上传成功')
   })
})
```

![05.png](05.png)
![06.png](06.png)


* html文件：

![04.png](04.png)
```bash
<form action='http://localhost:8080/' method='post' enctype='multipart/form-data'>
    <input type='file'/>
    <input type='submit' value='上传/>
</form>
```

### consolidate 模板引擎整合库

安装：npm install consolidate
引用：const consolidate = require('consolidate');
配置模板引擎：
* 我要输出什么东西
 server.set('view engine','html')
* 模板文件放在哪里
server.set('view','./views')
* 哪种模板引擎
server.engine('html',consolidate.ejs)

server.get('/',function(req,res){
    res.render('模板文件'，数据);
})
### route路由
根据目录的不同，调用不同的模块

const express = require('express');

var server=express();

var routeUser =express.Route();
routeUser.get('1.html',function(req,res){
    res.send('user1')
})
routeUser.get('2.html',function(req,res){
    res.send('user2')
})

server.use('/user',routeUser);
server.listen(8080);

