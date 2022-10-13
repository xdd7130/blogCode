---
title: ArcGIS API For JavaScript之添加地图
date: 2019-08-05 18:35:33
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
 在ArcGIS API For JS中，esri.Map类可以说是最重要的一个类，Map作为地图图层的容器，存放各种地图服务，同时Map具有一些常用的属性和事件可以帮助我们完成一些复杂的操作。接下来我们使用Map类开始我们的HelloWorld程序。
### 步骤
#### 首先新建一个Hello_World.html,同时引入我们的init.js,esri.css样式文件
```
<link rel="stylesheet" type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
<link rel="stylesheet" type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
<script type="text/javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
```

#### 创建一个div作为Map的容器(div容器必须设置宽高，不然地图不会显示)
```
<div id="mapDiv" style="width:900px; height:600px; border:1px solid #000;"></div>
```

#### 创建Map容器，并且加入地图
```
require(["esri/map","dojo/domReady!"],
    function(Map){
             var myMap = new Map("mapDiv",{
                  basemap:"topo"
             });

 })
```

#### 完整代码
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Hello World</title>
    <link rel="stylesheet" type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link rel="stylesheet" type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script type="text/javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <script>
        require(["esri/map","dojo/domReady!"],
                function(Map){
                    var myMap = new Map("mapDiv",{
                        basemap:"topo"
                    });

        })
    </script>
</head>
<body class="tundra">
    <div id="mapDiv" style="width:900px; height:600px; border:1px solid #000;"></div>
</body>
</html>
```

代码讲解：

* require函数是dojo提供的一个全局函数，只要引入init.js便可使用，require函数支持AMD（异步模块定义，Asynchronous Module Definition）规范。
* require函数有两个参数，第一个参数是一个数组（用于加载的模块），第二个参数是一个回调函数，注意前后加载的顺序一定要一致，例如require的第一个参数是esri.map,回调函数的第一个参数必须是Map
* require加载的第二个是dojo/domReady！,dojo/domReady！是一个插件（如果加载的模块以感叹号结尾，一般都为插件），此插件的作用是，当Html的dom加载完毕后在执行此函数，类似与window.onload事件。
* Map类中有一个比较特殊的属性叫做basemap,在arcgis api中，esri自己定义了一些底图可直接供我们使用，这些底层的调用，我们可以根据制定basemap属性直接调用已经定义好的底图。
esri给我们定义的底图有：streets ,satellite ,hybrid, topo, gray,dark-gray, oceans, national-geographic,terrain, osm, dark-gray-vector, gray-vector,streets-vector, streets-night-vector, streets-relief-vector, streets-navigation-vector,topo-vector.terrain, dark-gray, dark-gray-vector, gray-vector, streets-vector,streets-night-vector, streets-relief-vector, streets-navigation-vector , topo-vector,注意：要使用esri定义的底图属性basemap电脑必须联网。

### 需求
在实际开发过程中，大家不喜欢logo的存在可以设置Map的属性logo：false
在开发的过程中，对于地图的放大缩小，一般用代码来控制地图的放大，缩小，拉框放大，拉框缩小等等，所以一般隐藏地图自带的按钮，设置属性slider：false
