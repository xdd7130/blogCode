---
title: ArcGIS API For Javascript之网络分析
date: 2019-08-06 17:27:59
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
在WebGIS中网络分析一直是比较常用的功能，比如：路径分析，服务点分析等等,在WebGIS中想要实现相应的功能需要将我们的服务发布为网络服务。
### 路径分析
在路径分析中，主要有两个比较关键的参数：

* 停靠点
* 障碍点
* 
总体来说，路径分析的步骤主要有以下几步：

* 发布网络分析服务
* 在Web端创建路径分析对象
* 构建路径分析参数。
（1）停靠点
（2）障碍点
* 获得结果，进行处理。

####　代码实现
创建三个按钮（停靠点，障碍点，分析）
```
    <input id="Stop" type="button" value="停靠点" />
    <input id="barriers" type="button" value="障碍点" />
    <input id="Analyse" type="button" value="分析" />
```
添加地图（步骤省略）
创建路径分析对象，创建路径分析参数对象（注意：还缺少两个重要的参数，停靠点，障碍点）
```
//创建路径分析对象
                var shortestAnalyst = new RouteTask("http://localhost:6080/arcgis/rest/services/Test/mynet/NAServer/Route");
                //创建路径参数对象
                var routeParas = new RouteParameters();
                //障碍点，但是此时障碍点为空
                routeParas.barriers = new FeatureSet();
                //停靠点，但是此时停靠点为空
                routeParas.stops = new FeatureSet();
                //路径是否有方向
                routeParas.returnDirections = false;
                //是否返回路径，此处必须返回
                routeParas.returnRoutes = true;
                //空间参考
                routeParas.outSpatialReference = map.SpatialReference;
```
给停靠点按钮和障碍点按钮绑定点击事件
```
//定义一个标志
                //selectPointID=0什么都不做
                //selectPointID=1说明是添加停靠点
                //selectPointID=2说明是添加障碍点
                var selectPointID;
                //给停靠点按钮添加点击事件
                on(dom.byId("stop"),"click",function(){
                    selectPointID = 1;
                })
                //给障碍点按钮添加点击事件
                on(dom.byId("barriers"),"click",function(){
                    selectPointID = 2;
                })
```
给地图绑定点击事件（为了获得点的坐标–>构建停靠点或者障碍点）
```
//定义停靠点的符号
                var stopSymbol = new SimpleMarkerSymbol();
                stopSymbol.style = SimpleMarkerSymbol.STYLE_CIRCLE;
                stopSymbol.setSize(8);
                stopSymbol.setColor(new Color("#FFFFCC"));
                //定义障碍点的符号
                var barrierSymbol = new SimpleMarkerSymbol();
                barrierSymbol.style = SimpleMarkerSymbol.STYLE_CIRCLE;
                barrierSymbol.setSize(8);
                barrierSymbol.setColor(new Color("#f1a340"));
                on(map, "mouse-down", function(evt){
                    //通过selectPointID判断是添加是停靠点还是障碍点
                    switch (selectPointID) {
                        case 0:
                            break;
                        case 1:
                            //获得停靠点的坐标
                            var pointStop=evt.mapPoint;
                            var gr=new Graphic(pointStop,stopSymbol);
                            //构建停靠点的参数
                            routeParas.stops.features.push(gr);
                            break;
                        case 2:
                            //获得障碍点的坐标
                            var pointBarrier=evt.mapPoint;
                            var gr=new Graphic(pointBarrier,barrierSymbol);
                            //构建障碍点的参数
                            routeParas.barriers.features.push(gr);
                            break;
                    }
                    //如果selectPointID不等于0，将点的坐标在地图上显示出来
                    if (selectPointID != 0) {
                        addTextPoint("停靠点", pointStop, stopSymbol);
                        addTextPoint("障碍点", pointBarrier, barrierSymbol);
                        selectPointID = 0;
                    }
                });
                //文本符号：文本信息，点坐标，符号
                function addTextPoint(text,point,symbol) {
                    var textSymbol = new TextSymbol(text);
                    textSymbol.setColor(new Color([128, 0, 0]));              
                    var graphicText = Graphic(point, textSymbol);
                    var graphicpoint = new Graphic(point, symbol);
                    //用默认的图层添加
                    map.graphics.add(graphicpoint);
                    //用默认的图层添加
                    map.graphics.add(graphicText);
                }
```
给分析按钮绑定点击事件
```
//给分析按钮添加点击事件
                on(dom.byId("analyse"),"click",function(){
                    //如果障碍点或者停靠点的个数有一个为0，提示用户参数输入不对
                    if  (routeParas.stops.features.length  ==  0  || routeParas.barriers.features.length==0)
                    {
                        alert("输入参数不全，无法分析");
                        return;
                    }
                    //执行路径分析函数
                    shortestAnalyst.solve(routeParas, showRoute)
                })
```
获得结果，处理路径分析返回的结果。
```
//处理路径分析返回的结果。
                function showRoute(solveResult) {
                    //路径分析的结果
                    var routeResults = solveResult.routeResults;
                    //路径分析的长度
                    var res = routeResults.length;
                    //路径的符号
                    routeSymbol  = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                    if (res > 0) {
                        for (var i = 0; i < res; i++) {
                            var graphicroute = routeResults[i];
                            var graphic = graphicroute.route;
                            graphic.setSymbol(routeSymbol);
                            map.graphics.add(graphic);
                        }
                    }
                    else {
                        alert("没有返回结果");
                    }
                }
```
#### 运行结果
添加停靠点
![01.png](01.png)
添加障碍点
![02.png](02.png)
分析获得结果
![03.png](03.png)
#### 完整代码
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
            "esri/tasks/RouteTask",
            "esri/tasks/FeatureSet",
            "esri/symbols/SimpleMarkerSymbol",
            "esri/symbols/TextSymbol",
            "esri/toolbars/draw",
            "esri/symbols/SimpleLineSymbol",
            "esri/tasks/RouteParameters",
            "dojo/colors",
            "esri/graphic",
            "dojo/domReady!"],
            function (Map,on,dom,
                ArcGISDynamicMapServiceLayer,
                RouteTask,
                FeatureSet,
                SimpleMarkerSymbol,
                TextSymbol,
                Draw,
                SimpleLineSymbol,
                RouteParameters,
                Color,
                Graphic
                ) {
                var map = new Map("MyMapDiv");
                var layer = new ArcGISDynamicMapServiceLayer
                ("http://localhost:6080/arcgis/rest/services/Test/mynet/MapServer");
                map.addLayer(layer)
                //创建路径分析对象
                var shortestAnalyst = new RouteTask("http://localhost:6080/arcgis/rest/services/Test/mynet/NAServer/Route");
                //创建路径参数对象
                var routeParas = new RouteParameters();
                //障碍点，但是此时障碍点为空
                routeParas.barriers = new FeatureSet();
                //停靠点，但是此时停靠点为空
                routeParas.stops = new FeatureSet();
                //路径是否有方向
                routeParas.returnDirections = false;
                //是否返回路径，此处必须返回
                routeParas.returnRoutes = true;
                //空间参考
                routeParas.outSpatialReference = map.SpatialReference;
                //定义一个标志
                //selectPointID=0什么都不做
                //selectPointID=1说明是添加停靠点
                //selectPointID=2说明是添加障碍点
                var selectPointID;
                //给停靠点按钮添加点击事件
                on(dom.byId("stop"),"click",function(){
                    selectPointID = 1;
                })
                //给障碍点按钮添加点击事件
                on(dom.byId("barriers"),"click",function(){
                    selectPointID = 2;
                })
                //定义停靠点的符号
                var stopSymbol = new SimpleMarkerSymbol();
                stopSymbol.style = SimpleMarkerSymbol.STYLE_CIRCLE;
                stopSymbol.setSize(8);
                stopSymbol.setColor(new Color("#FFFFCC"));
                //定义障碍点的符号
                var barrierSymbol = new SimpleMarkerSymbol();
                barrierSymbol.style = SimpleMarkerSymbol.STYLE_CIRCLE;
                barrierSymbol.setSize(8);
                barrierSymbol.setColor(new Color("#f1a340"));
                on(map, "mouse-down", function(evt){
                    //通过selectPointID判断是添加是停靠点还是障碍点
                    switch (selectPointID) {
                        case 0:
                            break;
                        case 1:
                            //获得停靠点的坐标
                            var pointStop=evt.mapPoint;
                            var gr=new Graphic(pointStop,stopSymbol);
                            //构建停靠点的参数
                            routeParas.stops.features.push(gr);
                            break;
                        case 2:
                            //获得障碍点的坐标
                            var pointBarrier=evt.mapPoint;
                            var gr=new Graphic(pointBarrier,barrierSymbol);
                            //构建障碍点的参数
                            routeParas.barriers.features.push(gr);
                            break;
                    }
                    //如果selectPointID不等于0，将点的坐标在地图上显示出来
                    if (selectPointID != 0) {
                        addTextPoint("停靠点", pointStop, stopSymbol);
                        addTextPoint("障碍点", pointBarrier, barrierSymbol);
                        selectPointID = 0;
                    }
                });
                //文本符号：文本信息，点坐标，符号
                function addTextPoint(text,point,symbol) {
                    var textSymbol = new TextSymbol(text);
                    textSymbol.setColor(new Color([128, 0, 0]));              
                    var graphicText = Graphic(point, textSymbol);
                    var graphicpoint = new Graphic(point, symbol);
                    //用默认的图层添加
                    map.graphics.add(graphicpoint);
                    map.graphics.add(graphicText);
                }
                //给分析按钮添加点击事件
                on(dom.byId("analyse"),"click",function(){
                    //如果障碍点或者停靠点的个数有一个为0，提示用户参数输入不对
                    if  (routeParas.stops.features.length  ==  0  || routeParas.barriers.features.length==0)
                    {
                        alert("输入参数不全，无法分析");
                        return;
                    }
                    //执行路径分析函数
                    shortestAnalyst.solve(routeParas, showRoute)
                })
                //处理路径分析返回的结果。
                function showRoute(solveResult) {
                    //路径分析的结果
                    var routeResults = solveResult.routeResults;
                    //路径分析的长度
                    var res = routeResults.length;
                    //路径的符号
                    routeSymbol  = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                    if (res > 0) {
                        for (var i = 0; i < res; i++) {
                            var graphicroute = routeResults[i];
                            var graphic = graphicroute.route;
                            graphic.setSymbol(routeSymbol);
                            map.graphics.add(graphic);
                        }
                    }
                    else {
                        alert("没有返回结果");
                    }
                }               
            });


    </script>
</head>
<body>
    <div id="MyMapDiv" class="MapClass"></div>
    <input id="stop" type="button" value="停靠点" />
    <input id="barriers" type="button" value="障碍点" />
    <input id="analyse" type="button" value="分析" />
    
</body>
</html>
```