---
title: ArcGIS Server之发布网络分析服务
date: 2019-08-05 17:30:10
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
 ArcGIS中网络分析分为 : 最短路径分析，服务区信息等等,如果想要在Web应用程序中使用网络分析服务，需要将我们的数据发布为网络分析服务
### 发布网络分析服务
步骤如下：

1.利用ArcMap创建网络（如何创建网络，请查看ArcGIS帮助文档）
2.将创建的地图进行符号化
3.开启网络分析权限,添加分析图层
4.修改属性，将其发布为网络服务
#### 利用ArcMap创建网络
创建的网络为：
![01.png](01.png)
#### 将创建的地图进行符号化
![02.png](02.png)
#### 开启网络分析权限并添加分析图层
![03.png](03.png)
右击空白处Network Analyst
![04.png](04.png)
网络分析工具条
![05.png](05.png)
如果想要进行最短路径分析，进行新建路径
![06.png](06.png)
![07.png](07.png)
注：将路径图层名称修改为英文名（此处改为Route）
如果进行服务区分析，添加服务区图层
![08.png](08.png)
#### 修改属性并将其发布为网络服务
利用ArcMap链接ArcGIS Server ，并取名为mynet**（存放在Test目录下）**，到达服务编辑器
![09.png](09.png)
### 查看服务是否发布成功
从网站出输入网址http://localhost:6080/arcgis/rest/services,进入Test文件夹，可以看到我们刚刚发布的网络服务：
![10.png](10.png)