---
title: linux部署yapi
date: 2022-09-14 20:22:12
categories:
- 笔记
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