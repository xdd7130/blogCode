---
title: linux部署nginx
date: 2022-09-14 20:22:12
categories:
- nginx
tags: 
    - [nginx, linux]
---
# 下载nginx linux版本
```
地址： http://nginx.org/en/download.html 下载nginx tar包
```
通过 mobaXterms/xftp上传到linux服务器
# 解压
```
tar -zxvf nginx-1.20.2.tar.gz
```
# 进入nginx-1.20.2目录，运行configure文件
```
cd nginx-1.20.2
./configure
```
# make -->  make install
```
make
make install
```
# 查看nginx所在目录
```
whereis nginx   // /usr/local/nginx
```
# 进入sbin目录启动
```
cd sbin
./nginx
```
# 进入conf查看端口
```
cat nginx.conf
```

# nginx 常用命令
```
./nginx // 启动nginx
./nginx -s stop // 停止nginx
./nginx -s reload // 重新加载配置
./nginx -v // 查看版本号
ps -ef | grep nginx 查看nginx进程
```

# 其他
### HTTP 全局跳转 HTTPS 的设置
代码 497 是 nginx 在遇到 HTTP 请求发到 HTTPS 时会产生的内部错误代码，因此我们将返回一个 307 的回复（307 Temporary Redirect是HTTP协议中的一个状态码（Status Code）。可以理解为一个临时的重定向
```
error_page 497 =307 https://$host:$server_port$request_uri;

```