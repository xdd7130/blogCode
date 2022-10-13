---
title: ArcGIS Server之发布地理编码服务
date: 2019-08-05 17:48:35
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
地理编码服务是指建立地理位置坐标与给定地址的过程，简单的说就是将一个地址的描述信息映射为地图上所对应的空间位置，地理编码服务是GIS中比较重要的一个功能。
有的时候我们知道一个具体的地理坐标，却想知道这个地方的地址，就称作逆地理编码。

### 地理编码工具（Geocoding Tools）locator制作

打开arcCatalog，找到工具箱ArcToolbox中的Geocoding Tools---Create Address Locator，界面如下：
![01.png](01.png)
![02.png](02.png)
### locator已经制作完成，接下来就是发布地理编码服务
![03.png](03.png)
右键locator工具，选择Share as---Geocode Service，界面如下：
![04.png](04.png)
### 到这里，地理编码服务已经结束，最后是测试一下发布的服务是否成功
（1）测试Find Address Candidates
![05.png](05.png)
![06.png](06.png)
（2）测试Reverse Geocode
![07.png](07.png)