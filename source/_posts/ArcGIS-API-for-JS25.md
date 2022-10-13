---
title: ArcGIS API For Javascript之影像服务分析
date: 2019-08-06 18:08:42
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
  在前面我们大多数的程序是对矢量数据的操作，在这篇博客中，主要介绍影响服务的操作。在影像服务的操作中主要用到的类有：

* esri/tasks/ImageServiceIdentifyTask用于对影像数据的查询，它主要对应的参数为esri/tasks/ImageServiceIdentifyParameters,查询返回的结果用esri/tasks/ImageServiceIdentifyResult接受
* 对影像数据的测量主要用到的类为esri/tasks/ImageServiceMeasureTask,它所对应的的参数为esri/tasks/ImageServiceMeasureParameters

### ArcGIS API影像测量
#### 代码实现
首先加载地图
```
require(["esri/map","esri/layers/ArcGISImageServiceLayer","dojo/domReady!"],function(Map,ArcGISImageServiceLayer){
        var map=new Map("mapDiv")
        //注意这里是影响服务
        var layer=new ArcGISImageServiceLayer("http://localhost:6080/arcgis/rest/services/Test/Dem/ImageServer")
        map.addLayer(layer);
    });
```
添加两个按钮，一个用于绘制折线，一个用于测量分析
```
    <button id="drawp">绘制点</button>
    <button id="btn">查询</button>
```
创建必要对象
```
//用于绘制点
            var toolbar =new Draw(map);
            
            //创建影像分析对象
            var task=new ImageServiceIdentifyTask("http://192.168.0.112:6080/arcgis/rest/services/Test/Dem1/ImageServer");
            //创建影像分析参数对象
            var params=new ImageServiceIdentifyParameters();
            var lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
            var marker= new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CIRCLE,10, lineSymbol, new dojo.Color([255, 0, 0]));
            var fill= new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,  new dojo.Color([255, 0, 0]));
```
给绘制折现按钮添加绑定事件
```
//给绘制点按钮绑定事件
            on(dom.byId("drawp"),"click",function(){
                toolbar.activate(Draw.POLYLINE, {
                        showTooltips:true
                })
            })
            //给draw工具的绘制完成事件绑定函数
            on(toolbar,"draw-complete", function (result) {
                var geometry=result.geometry;
                //给参数对象的几何属性赋值
                params.geometry=geometry;
                toolbar.deactivate();
            
            })
```
给分析按钮绑定事件
```
on(dom.byId("btn"),"click",function(){
                //设置掩膜规则
                var mosaicRule=new MosaicRule();
                mosaicRule.ascending=false;
                mosaicRule.method=MosaicRule.METHOD_CENTER
                params.mosaicRule=mosaicRule
                params.pixelSizeX=layer.pixelSizeX;
                params.pixelSizeY=layer.pixelSizeY;
                task.execute(params,function(result){
                    //弹出改点的高程值
                    alert(result.value)
                })
            })
```
#### 代码结果
![04.png](04.png)
#### 全部代码
```
<!DOCTYPE html>
<html>
  <head>
    <title>index.html</title>
    
    <meta http-equiv="keywords" content="keyword1,keyword2,keyword3">
    <meta http-equiv="description" content="this is my page">
    <meta http-equiv="content-type" content="text/html; charset=utf-8">
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <!--<link rel="stylesheet" type="text/css" href="./styles.css">-->
    <style>
        .MapClass{
            width:100%;
            height:500px;
            border:1px solid #000;
        }
    </style>
    <script>
        require(["esri/map","esri/layers/ArcGISImageServiceLayer","dojo/on",
        "dojo/dom","esri/toolbars/draw",
        "esri/tasks/ImageServiceIdentifyTask","esri/tasks/ImageServiceIdentifyParameters",
        "esri/symbols/SimpleLineSymbol","esri/graphic","esri/symbols/SimpleMarkerSymbol", "esri/symbols/SimpleFillSymbol",
        "esri/layers/MosaicRule",
        "dojo/domReady!"],function(Map,ArcGISImageServiceLayer,on,dom,Draw,ImageServiceIdentifyTask,
        ImageServiceIdentifyParameters,SimpleLineSymbol,
        Graphic,SimpleMarkerSymbol,SimpleFillSymbol,MosaicRule){
            var map=new Map("mapDiv")

            //注意这里是影响服务
            var layer=new ArcGISImageServiceLayer("http://192.168.0.112:6080/arcgis/rest/services/Test/Dem1/ImageServer")
            map.addLayer(layer);
            //用于绘制点
            var toolbar =new Draw(map);
            
            //创建影像分析对象
            var task=new ImageServiceIdentifyTask("http://192.168.0.112:6080/arcgis/rest/services/Test/Dem1/ImageServer");
            //创建影像分析参数对象
            var params=new ImageServiceIdentifyParameters();
            var lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
            var marker= new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CIRCLE,10, lineSymbol, new dojo.Color([255, 0, 0]));
            var fill= new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,  new dojo.Color([255, 0, 0]));
            //给绘制折线按钮绑定事件
            on(dom.byId("drawp"),"click",function(){
                toolbar.activate(Draw.POINT, {
                        showTooltips:true
                })
            })
            //给draw工具的绘制完成事件绑定函数
            on(toolbar,"draw-complete", function (result) {
                map.graphics.clear();
                var geometry=result.geometry;
                //给参数对象的几何属性赋值
                params.geometry =geometry;
                graphic= new Graphic(geometry, marker);
                map.graphics.add(graphic);
                toolbar.deactivate();
            
            })
            
            on(dom.byId("btn"),"click",function(){
                //设置掩膜规则
                var mosaicRule=new MosaicRule();
                mosaicRule.ascending=false;
                mosaicRule.method=MosaicRule.METHOD_CENTER
                params.mosaicRule=mosaicRule
                params.pixelSizeX=layer.pixelSizeX;
                params.pixelSizeY=layer.pixelSizeY;
                task.execute(params,function(result){
                    //弹出改点的高程值
                    alert(result.value)
                })
            })
            
        });
    </script>
  </head>
  
  <body>
    <div id="mapDiv" class="MapClass"></div>
    <button id="drawp">绘制点</button>
    <button id="btn">查询</button>
  </body>
</html>
```