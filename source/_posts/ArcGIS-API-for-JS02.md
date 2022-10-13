---
---
title: ArcGIS Server之发布切片地图服务
date: 2019-08-05 10:17:48
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---

> 参考：[https://github.com/CarpenterGISer/ArcGIS-API-For-Javacsript](https://github.com/CarpenterGISer/ArcGIS-API-For-Javacsript)

### 什么是切片地图服务？
切片地图服务又叫缓存第服务区，地图缓存是使地图和图像服务更快运行的一种非常有效的方法。创建地图缓存时，服务器会在若干个不同的比例级别上绘制整个地图并存储地图图像的副本。然后，服务器可在某人请求使用地图时分发这些图像。对于服务器来说，每次请求使用地图时，返回缓存的图像要比绘制地图快得多。
![01.png](01.png)
>注：此图片的意思是，将同一幅地图按照不同的比例进行切片，得到相应的地图切片。

#### 切片地图服务优点
1、由于切片地图服务中的图片不需要服务器实时生成，本身存在服务器的硬盘上，所以大大提高了服务器的性能。
2、图像的详细程度不会对服务器分发副本的速度造成显著影响。
#### 切片地图服务缺点
1、切片地图服务由于图片事先存在，所以该服务实现的功能有限，例如想隐藏服务中的某一个图层不可以实现。
2、当缓存级别较高时，占用的硬盘存储量较大

### 利用ArcMap发布切片地图服务
利用ArcMap发布切片地图服务与发布动态地图服务类似，主要分为以下几步：

1、用ArcMap打开我们想要发布为服务的数据
2、符号化我们的数据（可选）
3、利用ArcMap连接ArcGIS Server
4、修改地图服务属性（修改为切片地图服务）
5、发布服务到ArcGIS Server
#### （步骤1-3略）
#### 步骤4:修改地图服务属性

>点击缓存->使用缓存中的切片->切片方案->设置缓存级别

![02.png](02.png)
>注： 这个地方设置的是5个级别，可以看到缓存的大小是小于5M，当设置的级别越多时，缓存的大小基本成几何倍数增长
### 查看硬盘缓存
可以去ArcGIS Server站点目录查看缓存（我的站点是在：C:\arcgisserver）
进入C:\arcgisserver\directories\arcgiscache可以看到我们刚刚发布的服务myTile
![03.png](03.png)
>注：如果发布的不是切片地图服务。此目录下面是不会有相应的文件夹的

去ArcGIS REST Services Directory查看服务

在网址上输入http://localhost:6080/arcgis/rest/services进入服务目录，进入 Test文件夹可以看到我们发布的两个服务
![04.png](04.png)
>注：可以看到myTile后面也加了一个（MapServer），可以得知切片地图服务也是属于地图服务的一种。