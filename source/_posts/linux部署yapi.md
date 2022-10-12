---
title: linux部署yapi
date: 2022-09-14 20:22:12
categories:
- 笔记
tags: 
    - [mock, linux]
---
# yapi解压
```
unzip yapi20220224.zip
```
# 安装node
### 官网下载node(略)
### 解压压缩包
```
tar -xvf node-v16.17.0-linux-x64.tar.xz
```
### 将解压的 node 文件进行重命名，方便后续的操作
```
mv node-v16.17.0-linux-x64 nodejs
```
### 全局引用
```
引入npm：ln -s /opt/yapiMock/nodejs/bin/npm /usr/local/bin
引入node：ln -s /opt/yapiMock/nodejs/bin/node /usr/local/bin
```
### 查看全局是否生效
```
npm –v
node -v
```
### 安装pm2
```
npm install pm2 -g
```
# 安装mongodb
### 下载安装包(略)
### 下载完成后解压缩压缩包
```
tar zxf mongodb-linux-x86_64-rhel70-5.0.3.tgz
```
### 安装准备
将mongodb移动到/usr/local/server/mongdb文件夹
```
mv  mongodb-linux-i686-2.2.3  /usr/local/mongodb
```
### 创建数据库文件夹与日志文件
```
mkdir /usr/local/mongodb/data
touch /usr/local/mongodb/logs
```
### 设置开机自启动
```
echo "/usr/local/mongodb/bin/mongod --dbpath=/usr/local/mongodb/data –logpath=/usr/local/mongodb/logs –logappend --auth –port=27017" >> /etc/rc.local
```
### 启动mongodb
```
/usr/local/mongodb/bin/mongod --dbpath=/usr/local/mongodb/data --logpath=/usr/local/mongodb/logs/mongodb.log --logappend --port=27017 --bind_ip=10.40.128.3 --fork
```

