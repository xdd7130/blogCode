---
title: Node.js部署到IIS服务器
date: 2020-09-25 15:00:31
categories:
- 其他
tags: [Node.js,IIS服务器]
---
使用iisnode模块在IIS中托管node.js应用程序来取代自托管node.exe进程：
### 起因：
服务器的ip只有80端口对外开放。但是我们node app.js后，外网访问不到，为了能够解决此问题，将nodejs部署到iis服务器上，这样既可以解决自动退出问题，也可共享80端口。
### 依赖项
>Node.js

><a href="urlrewrit2.exe">URL rewrite</a>

><a href="iisnode-full-v0.2.21-x64.msi">IISNode</a>

### 创建web.config文件（express）
```bash
<configuration>
 <system.webServer>
   <!-- indicates that the hello.js file is a node.js application 
   to be handled by the iisnode module -->
   <handlers>
     <add name="iisnode" path="app.js" verb="*" modules="iisnode" />
   </handlers>
    <!-- use URL rewriting to redirect the entire branch of the URL namespace
    to app.js node.js application; for example, the following URLs will 
    all be handled by app.js:
        http://localhost/foo
        http://localhost/bar
    -->
    <rewrite>
      <rules>
        <rule name="api">
          <match url="api/*" />
          <action type="Rewrite" url="app.js" />
        </rule>
      </rules>
    </rewrite>
    <!-- exclude node_modules directory and subdirectories from serving
    by IIS since these are implementation details of node.js applications -->
    <security>
      <requestFiltering>
        <hiddenSegments>
          <add segment="node_modules" />
        </hiddenSegments>
      </requestFiltering>
    </security>    
  </system.webServer>
</configuration>
```

### Node服务的修改
```bash
index.js中端口监听由 server.listen(3000) 改成 server.listen(process.env.PORT)
```

完整app.js
```bash
const userApi = require('./api/userApi');
const fs = require('fs');
const path = require('path');
const bodyParser = require('body-parser');
const express = require('express');
const app = express();
app.all('*', function(req, res, next) {
    res.header("Access-Control-Allow-Credentials", "true");
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Content-Type, Content-Length, Authorization, Accept, X-Requested-With,loginedtoken');
    res.header('Access-Control-Allow-Methods', 'PUT, POST, GET, DELETE, OPTIONS');
    if (req.method == 'OPTIONS') {
        res.send(200);
    }
    else {
        next();
    }
});
var history = require('connect-history-api-fallback');
const staticFileMiddleware = express.static('assets');
app.use(staticFileMiddleware);
app.use(history({
  disableDotRule: true,
  verbose: true
}));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }))
app.use('/node_yrd/api', userApi);
app.listen(process.env.PORT);
```

### Vue请求地址的修改

```bash
var isPro = process.env.NODE_ENV === 'production' //process.env.NODE_ENV用于区分是生产环境还是开发环境
//根据环境不同导出不同的baseURL
module.exports = {
    baseURL: isPro ? 'http://***.***.com/' : ''
}
```

### 创建添加应用程序
如图
![应用程序](1.png)

