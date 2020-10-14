---
title: Cesium
date: 2020-07-01 14:16:35
categories: 
- Cesium
tags: [Cesium]
---
### 3dtiles文件放在iis服务器
1、目录下，添加应用程序
2、配置iis的mime头，防止b3dm请求跨域：.b3dm    application/octet-stream

* 问题
1、vue跨域：config目录下的index.js:proxyTable.
2、“请求筛选模块被配置为拒绝包含双重转义序列的请求。”，在些URL中若包含+号等符号，然后IIS7以上的版本会默认拒绝请求此URL，需要进行如下的修改.

```bash
<system.webServer>
<security>
<requestFiltering allowDoubleEscaping="true"/>
</security>
</system.webServer>
```

>>> viewer.scene.globe.undergroundColor 不同版本，有优化，在1.68,globe无undergroundColor属性，1.71版本有，可自定义设置。

三维遇到的问题（20200709）：
1.arcScence:导出的是wrl文件，cesium不支持可视化，还没找到可以文件转换的方法。
2.evs:导出的也是wrl文件，呵呵
3.arcgis Pro:可以解决这个问题，可以发布服务，但是，需要注册安装，试用期21天，需购买。
4.openGeoSys:开源，文件输出的vtk格式，cesium实现可视化，但专业，需进一步学习。