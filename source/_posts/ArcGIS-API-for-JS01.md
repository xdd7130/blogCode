---
---
title: ArcGIS Server之发布动态地图服务
date: 2019-08-05 10:17:48
categories:
- ArcGIS
tags: [ArcGIS API for JS]
---

> 参考：[https://github.com/CarpenterGISer/ArcGIS-API-For-Javacsript](https://github.com/CarpenterGISer/ArcGIS-API-For-Javacsript)

在WebGIS开发过程中，我们所需要的数据不仅仅来自于ArcGIS online，有时候我们需要发布自己的数据服务,在这里主要说明一下如何利用ArcGIS Server发布自己的动态地图服务。

#### 什么是地图服务?
地图服务是一种利用 ArcGIS 使地图可通过 Web 进行访问的方法。我们首先在 ArcMap 中制作地图，然后将地图作为服务发布到 ArcGIS Server 站点上。这样，Internet 用户便可在 Web应用程序、ArcGIS for Desktop、ArcGIS Online 以及其他客户端应用程序中使用此地图服务。
当地图服务发布成功后，我们可以通过网址（http://localhost:6080/arcgis/rest/services）来查看地图服务所支持的操作，地图服务所包含的数据，以及我们还可以通过网址来测试地图服务的功能
#### 地图服务之动态地图服务
我们常用的地图服务主要分为两种：动态地图服务，切片地图服务（也叫瓦片地图服务），地图服务其实可以理解为图层的集合，在本篇中主要说的是动态地图服务，
动态地图服务的特点：

1、动态地图会在用户发出请求时进行绘制。
2、地图服务具有允许客户端（例如 ArcGIS web API）动态更改每个图层的行为和外观。
3、可以执行属性查询，空间查询等相关功能。
#### 利用ArcMap发布动态地图服务
利用ArcMap发布自己的服务，主要分为以下几步：

1、用ArcMap打开我们想要发布为服务的数据
2、符号化我们的数据（可选）
3、利用ArcMap连接ArcGIS Server发布服务
#### 查看地图服务
若发布到本地，打开并登录服务管理器（http://localhost:6080/arcgis/manager/）
![01.png](01.png)
![02.png](02.png)
![03.png](03.png)
![03.png](03.png)
接下来我们可以使用ArcGIS API For js,来调用我们的服务做一些事情啦