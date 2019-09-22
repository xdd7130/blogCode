---
title: MySQL
date: 2019-09-11 15:11:01
categories:
- Node
tags: [Node.js]
---
### 准备
#### mysql安装
参考：https://www.cnblogs.com/laumians-notes/p/9069498.html


Navicat数据库管理工具:https://blog.csdn.net/a599174211/article/details/82795658


### nodeJS操作MySQL
nodeJS默认不支持MySQL,需要第三方工具来驱动，这里用mysql驱动。
* npm install mysql

```bash
const mysql = require('mysql');

1.连接
//createConnection(哪台服务器，用户名，密码，库)
var db = mysql.createConnection(host:'localhost',port:'3306',user:'root',password:'123456',database:'my_db',)；

2.查询
//query(干啥，回调函数)
db.query("SELECT * FROM `user_table`;",(err,data)=>{
    if(err)
        console.log('出错了'，err)
    else
        console.log('成功了'，data)
        console.log(JSON.stringify(data))
});

```
### SQL标准写法：
1.关键字大写
2.库、表、字段需加上饭单引号``
SQL四大语句：增删改查
增：INSERT
INSERT INTO 表（字段列表） VALUES (值列表)
INSERT INTO `user_table`（`ID`,`username`,`password`）VALUES (`0`,`blue2`,`987654`) VALUES (值列表)
删：DELETE
改：UPDATE
查：SELECT

