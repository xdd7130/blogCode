---
title: ArcGIS API For Javascript之地图操作及小部件
date: 2019-08-06 14:22:30
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
开发一个GIS系统不可避免的就要涉及一些简单的地图操作，比如：拉框放大，拉框缩小，平移，全图等相关操作。除了这些基本的地图操作，Esri给我们提供的一些小部件也非常有用，其中几个重要的小部件有：鹰眼，书签，比例尺等等。在此篇博文中，主要介绍地图的基本操作和几个常用的小部件。
### 地图的基本操作
在ArcGIS API中给我们提供了一个类叫做Navigation，通过这个类我们可以实现对地图的基本操作，Navigation类的用法可以分为以下几步：

* 创建Navigation对象
* 激活Navigation的相应状态
* 地图操作
#### 代码实现
创建五个按钮
```
    <button>拉框放大</button>
    <button>拉框缩小</button>
    <button>平移</button>
    <button>全图</button>
    <button>漫游</button>
```
添加地图（省略）
给相应按钮添加绑定事
```
                //给按钮添加绑定事件
                query("button").on("click",function(event){
                    //获得按钮的文本信息
                    var value=this.innerHTML;
                    switch(value){
                        case "平移":
                            navToolbar.activate(Navigation.PAN);
                            break;                          
                        case "拉框缩小":
                            navToolbar.activate(Navigation.ZOOM_OUT);
                            break;
                        case "拉框放大":
                            navToolbar.activate(Navigation.ZOOM_IN);
                            break;
                        case "全图":
                            navToolbar.zoomToFullExtent();
                            break;
                        case "漫游":
                            //默认是漫游操作
                            navToolbar.deactivate();
                            break;
                    }
                })
```
#### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>Geometric _Network</title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <style type="text/css">
        .MapClass{
            width:100%;
            height:600px;
            border:1px solid #000;
        }
    </style>
    <script type="text/javascript">

        require(["esri/map",
            "dojo/on",
            "dojo/dom",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "dojo/query","esri/toolbars/navigation",
            "dojo/domReady!"],
            function (Map,on,dom,
                ArcGISDynamicMapServiceLayer,query,Navigation      
                ) {
                var map = new Map("mapDiv",{
                    logo: false
                });
                var layer = new ArcGISDynamicMapServiceLayer
                ("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer");
                map.addLayer(layer)
                //创建地图操作对象
                var navToolbar = new Navigation(map);
                //给按钮添加绑定事件
                query("button").on("click",function(event){
                    //获得按钮的文本信息
                    var value=this.innerHTML;
                    switch(value){
                        case "平移":
                            navToolbar.activate(Navigation.PAN);
                            break;                          
                        case "拉框缩小":
                            navToolbar.activate(Navigation.ZOOM_OUT);
                            break;
                        case "拉框放大":
                            navToolbar.activate(Navigation.ZOOM_IN);
                            break;
                        case "全图":
                            navToolbar.zoomToFullExtent();
                            break;
                        case "漫游":
                            //默认是漫游操作
                            navToolbar.deactivate();
                            break;
                    }
                })
                               
            });


    </script>
</head>
<body class="tundra">
    <div id="mapDiv" class="MapClass"></div>
    <button>拉框放大</button>
    <button>拉框缩小</button>
    <button>平移</button>
    <button>全图</button> 
    <button>漫游</button>
</body>
</html>
```
### API中的小部件
关于API中提供的小部件有两种使用方式：

* 将小部件绑定在一个DOM元素上面
* 将小部件绑定在地图的上面

#### 代码实现
首先我们将小部件绑定在DOM元素上面
我们创建一个DOM元素用于存放鹰眼控件
```
<div id="view"></div>
```
创建小部件（并指定小部件绑定在哪一个DOM元素上面）
```
//1.创建小部件对象
var overviewMapDijit = new OverviewMap({
                map: map,
                visible: true,
            },dom.byId("view"));//指定将小部件绑定在哪个DOM元素上面
```
启用小部件
```
//2.启用小部件
overviewMapDijit.startup();
```
#### 代码结果（绑定在DOM）
![04.png](04.png)
#### 全部代码（绑定在DOM）
```
<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <!--The viewport meta tag is used to improve the presentation and behavior of the samples 
      on iOS devices-->
    <meta name="viewport" content="initial-scale=1, maximum-scale=1,user-scalable=no">
    <title>Overview Map</title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <style>
        .MapClass{
            width:600px;
            height:400px;
            border:1px solid #000;
        }
        #view {
           height: 100px;
           width: 100px; 
           border:1px solid red;
           border-right-color:blue;
           position:relative;
           left:800px;
           top:-200px;


        }
    </style>
    <script>
       
        require(["esri/map","dojo/dom","esri/dijit/OverviewMap","dojo/domReady!"], function (
          Map,dom, OverviewMap){
            var map = new Map("mapDiv",
            {   
                basemap: "topo"
            });
            //1.创建小部件对象
            var overviewMapDijit = new OverviewMap({
                map: map,
                visible: true,
            },dom.byId("view"));//指定将小部件绑定在哪个DOM元素上面
            //2.启用小部件
            overviewMapDijit.startup();
        });
    </script>
  </head>
  
  <body>
      <div id="mapDiv" class="MapClass"></div>  
      <div id="view"></div> 
  </body>
</html>
```
#### 补充（将小部件绑定在地图上）
只需要在创建小部件的时候，修改一个属性即可
```
var overviewMapDijit = new OverviewMap({
                //指定将小部件绑定在地图的哪一个位置：可以填写top-right,bottom-right,bottom-left 和top-left. 
                attachTo: "top-right",
                map: map,
                visible: true,
            });
```
##### 运行结果（绑定在地图）
![05.png](05.png)
##### 全部代码（绑定在地图）
```
<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <!--The viewport meta tag is used to improve the presentation and behavior of the samples 
      on iOS devices-->
    <meta name="viewport" content="initial-scale=1, maximum-scale=1,user-scalable=no">
    <title>Overview Map</title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <style>
        .MapClass{
            width:100%;
            height:600;
        }
    </style>
    <script>
       
        require(["esri/map","dojo/dom","esri/dijit/OverviewMap","dojo/domReady!"], function (
          Map,dom, OverviewMap){
            var map = new Map("mapDiv",
            {   
                basemap: "topo"
            });
            //1.创建小部件对象
            var overviewMapDijit = new OverviewMap({
                //指定将小部件绑定在地图的哪一个位置：可以填写top-right,bottom-right,bottom-left 和top-left. 
                attachTo: "top-right",
                map: map,
                visible: true,
            });
            //2.启用小部件
            overviewMapDijit.startup();
        });
    </script>
  </head>
  
  <body>
      <div id="mapDiv" class="MapClass"></div>  
  </body>
</html>
```