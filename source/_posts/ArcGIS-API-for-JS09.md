---
title: ArcGIS API For JavaScript本地部署（IIS）
date: 2019-08-05 18:23:24
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
在windows系统，我们总是将ArcGIS API For JavaScript部署在IIS中，而不是部署在Tomcat中，其实在IIS中的配置和Tomcat中的配置ArcGIS API几乎没有什么差别，在本篇文章中，主要讲解在IIS中的配置ArcGIS API。
部署本地环境在IIS主要分为以下几个步骤： 
1. 下载ArcGIS API For JavaScript函数库 
2. 安装IIS软件并进行配置 
3. 修改相应的js文件（2个文件） 
4. 将修改好的函数库复制到指定位置 
5. 测试ArcGIS API For JavaScript函数库是否安装成功。
###　下载ArcGIS API For JavaScript函数库
ArcGIS API For JavaScript
下载的函数库如图所示:
![01.png](01.png)
###　安装IIS软件并进行配置（略）
###　修改相应的js文件（2个文件）
修改 文件一（init.js） 
位置：arcgis_js_v317_api\arcgis_js_api\library\3.17\3.17\init.js 
将[HOSTNAME_AND_PATH_TO_JSAPI]替换成localhost/arcgis_js_api/library/3.17/3.17/
修改文件二（dojo.js） 
位置：arcgis_js_v317_api\arcgis_js_api\library\3.17\3.17\dojo\dojo.js 
将[HOSTNAME_AND_PATH_TO_JSAPI]替换成localhost/arcgis_js_api/library/3.17/3.17/
