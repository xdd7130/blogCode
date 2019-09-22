---
title: ArcGIS API For Javascript之几何服务
date: 2019-08-06 17:43:13
categories:
- ArcGIS
tags: [ArcGIS API for JS]
---
什么是几何服务（GeometryService）？从字面意义上来说是和几何（Geometry）相关的服务，具体一点讲我们可以用这个类实现一些简单的图形操作。例如：

* 测量直线的距离
* 形状的缓冲区分析
* 判断两个形状之间的关系（相交，相离等等）
* 两个形状求交
* 对形状的裁剪等等

在此以缓冲区分析和图形求交来介绍几何服务的使用.
### 启用几何服务
当我们安装好ArcGIS Server的时候，几何服务就已经存在了，因此几何服务不需要我们发布，但是在默认情况下几何服务是关闭的，因此我们首先启动几何服务，步骤如下：

登录我们的ArcGIS Server Manager
![01.png](01.png)
启动成功
![02.png](02.png)
几何服务的URL
![03.png](03.png)
### 利用几何服务进行缓冲区分析
几何服务的使用一般分为四个步骤：

* 创建几何服务对象
* 创建参数
* 构建参数对象
* 执行相应的方法，并处理返回的结果

#### 代码实现
加载地图（省略）
创建两个按钮–一个按钮用于画点，另外一个按钮用于缓冲区分析
```
<input id="point" type="button" value="点"/>
<input id="Btn" type="button" />
```
创建相关对象（例如符号对象，Draw对象等等）
```
//使用toolbar上的绘图工具
                var toolBar = new Draw(map);
                //创建点符号
                var pointSymbol = new SimpleMarkerSymbol();
                pointSymbol.style = SimpleMarkerSymbol.STYLE_CIRCLE;
                pointSymbol.setSize(12);
                pointSymbol.setColor(new Color("#FFFFCC"));
                //线符号
                lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //面符号
                polygonSymbol= new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new Color([255, 255, 0, 0.25]));
```
创建几何服务对象，创建相应的缓冲区参数对象
```
var geometryServices = new GeometryService("http://localhost:6080/arcgis/rest/services/Utilities/Geometry/GeometryServer");
var params = new BufferParameters();
```
给画点按钮绑定事件
```
//给画点按钮绑定事件
                on(dom.byId("point"),"click",function(){
                    //激活画点工具
                    toolBar.activate(Draw.POINT, {
                        showTooltips:true
                    })
                    
                })

                //绘图结束绑定事件
                on(toolBar,"draw-end",function(result){
                    //获得点形状
                    var geometry=result.geometry;
                    //创建Graphic
                    var graphicpoint = new Graphic(geometry, pointSymbol);
                    map.graphics.add(graphicpoint); 
                    //关闭绘图工具
                    toolBar.deactivate();
                    //很重要！！！在这里构建缓冲区分析的形状属性
                    //注意这里可以放一个数组，此处为了方便我们只对一个形状进行缓冲区分析
                    params.geometries  = [ geometry ];

                    
                })
```
给缓冲区分析按钮绑定事件
```
//给缓冲区分析按钮绑定事件
                on(dom.byId("Btn"),"click",function(){
                    //缓冲距离
                    params.distances = [ "80" ];
                    //空间参考
                    params.bufferSpatialReference = map.spatialReference;
                    params.outSpatialReference = map.spatialReference;
                    //执行分析处理结果
                    geometryServices.buffer(params,function(result){
                        //处理缓冲区分析得到的结果
                        for(var idx in result)
                        {   
                            //创建Graphic
                            var graphic=new Graphic(result[idx],polygonSymbol)
                            //将每一个结果都放在map中
                            map.graphics.add(graphic)
                        }
                    });
                })
```
#### 运行结果
![04.png](04.png)
#### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>缓冲区分析</title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <style type="text/css">
        .MapClass{
            width:100%;
            height:500px;
            border:1px solid #000;
        }
    </style>
    <script type="text/javascript">

        require(["esri/map",
            "dojo/dom","dojo/on",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/symbols/SimpleMarkerSymbol",
            "esri/symbols/SimpleLineSymbol",
            "esri/symbols/SimpleFillSymbol",
            "esri/toolbars/draw",
            "esri/graphic",
            "esri/tasks/GeometryService",
            "esri/tasks/BufferParameters",
            "dojo/colors",
            "dojo/domReady!"],
            function (Map,dom,on,
                ArcGISDynamicMapServiceLayer,
                SimpleMarkerSymbol,
                SimpleLineSymbol,
                SimpleFillSymbol,
                Draw,
                Graphic,
                GeometryService,
                BufferParameters,Color) {
                var map = new esri.Map("mapDiv");
                var layer = new esri.layers.ArcGISDynamicMapServiceLayer
                ("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer");
                map.addLayer(layer);
                //创建几何服务对象
                var geometryServices = new GeometryService("http://localhost:6080/arcgis/rest/services/Utilities/Geometry/GeometryServer");
                //创建缓存参数对象
                var params = new BufferParameters();
                
                //使用toolbar上的绘图工具
                var toolBar = new Draw(map);
                //创建点符号
                var pointSymbol = new SimpleMarkerSymbol();
                pointSymbol.style = SimpleMarkerSymbol.STYLE_CIRCLE;
                pointSymbol.setSize(12);
                pointSymbol.setColor(new Color("#FFFFCC"));
                //线符号
                lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //面符号
                polygonSymbol = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new Color([255, 255, 0, 0.25]));
                //给画点按钮绑定事件
                on(dom.byId("point"),"click",function(){
                    //激活画点工具
                    toolBar.activate(Draw.POINT, {
                        showTooltips:true
                    })
                    
                })
                //绘图结束绑定事件
                on(toolBar,"draw-end",function(result){
                    //获得点形状
                    var geometry=result.geometry;
                    //创建Graphic
                    var graphicpoint = new Graphic(geometry, pointSymbol);
                    map.graphics.add(graphicpoint); 
                    //关闭绘图工具
                    toolBar.deactivate();
                    //很重要！！！在这里构建缓冲区分析的形状属性
                    //注意这里可以放一个数组，此处为了方便我们只对一个形状进行缓冲区分析
                    params.geometries  = [ geometry ];

                    
                })
                //给缓冲区分析按钮绑定事件
                on(dom.byId("Btn"),"click",function(){
                    //缓冲距离
                    params.distances = [ "80" ];
                    //空间参考
                    params.bufferSpatialReference = map.spatialReference;
                    params.outSpatialReference = map.spatialReference;
                    //执行分析处理结果
                    geometryServices.buffer(params,function(result){
                        //处理缓冲区分析得到的结果
                        for(var idx in result)
                        {   
                            //创建Graphic
                            var graphic=new Graphic(result[idx],polygonSymbol)
                            //将每一个结果都放在map中
                            map.graphics.add(graphic)
                        }
                    });


                })
               
            });
    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div>
    <input id="point" type="button" value="点"/>
    <input id="Btn" type="button" value="缓冲区分析" />
</body>
</html>
```
### 利用几何服务进行多边形求交
首先我们先看看求交这一个方法
```
GeometryService.intersect(geometries, geometry, callback?, errback?)
```
解释： 
```
此方法有四个参数，
第一个参数是   形状的集合(多个形状)
第二个参数是   一个形状
第三个参数是   运行成功之后的结果
第四个参数是   运行失败之后的结果
```
方法的使用：用第二个参数的形状与第一个参数的多个形状进行求交，从而返回运行的结果
#### 代码实现
加载地图（略）
创建三个按钮
```
    <button class="pbtn" >第一个参数</button>
    <button class="pbtn" >第二个参数</button>
    <input id="Btn" type="button" value="求交分析" />
```
创建相关对象（符号对象，绘图对象）
```
//一个标志位，用于记录是绘制第几个参数
                //1代表第一个参数
                //2代表第二个参数
                var flag;
                //第一个参数  注意第一个参数是多个形状
                var geometries=[];
                //第二个参数  注意第二个参数是一个形状
                var paramGeometry;
                //使用toolbar上的绘图工具
                var toolBar = new Draw(map);
                
                //线符号
                lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //第一个参数的面符号
                polygonSymbol1 = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new Color([255, 0, 0, 0.25]));
                //第二个参数的面符号
                polygonSymbol2 = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new Color([0, 255, 0, 0.25]));
                //求交之后的面符号
                polygonSymbol3 = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new Color([0, 0, 255, 0.25]));
```
给画图形的按钮绑定事件
```
//给绘制polygon的按钮绑定事件
                query(".pbtn").on("click",function(event){
                    var value=this.innerHTML;
                    switch(value){
                        case "第一个参数":{
                            flag=1;
                            break;
                            
                        }
                        case "第二个参数":{
                            flag=2;
                            break;
                        }   
                    }
                    //激活绘制多边形
                    toolBar.activate(Draw.POLYGON, {
                        showTooltips:true
                    })
                    
                })
                //绘图结束绑定事件
                on(toolBar,"draw-end",function(result){
                    //获得面形状
                    var geometry=result.geometry;
                    //创建Graphic
                    var graphicpoint;

                    //很重要！！！在这里构建参数对象
                    if(flag==1)
                    {
                        //创建Graphic
                        graphicpoint = new Graphic(geometry, polygonSymbol1);
                        geometries.push(geometry)
                    }
                    else if(flag==2)
                    {
                        //创建Graphic
                        graphicpoint = new Graphic(geometry, polygonSymbol2);
                        paramGeometry=geometry;
                    }   
                    map.graphics.add(graphicpoint); 
                    //关闭绘图工具
                    toolBar.deactivate();                   
                })
```
给求交按钮绑定事件
```
//给求交按钮绑定事件
                on(dom.byId("Btn"),"click",function(){
                    //在这里在说一边，第一个参数是图形集合，第二个参数是一个图形
                    geometryServices.intersect(geometries,paramGeometry,function(result){
                        for(var idx in result)
                        {
                            //创建Graphic
                            var graphic=new Graphic(result[idx],polygonSymbol3)
                            //将每一个结果都放在map中
                            map.graphics.add(graphic)
                        }
                    })
                })
```
#### 运行结果
![05.png](05.png)
#### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>多边形求交</title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <style type="text/css">
        .MapClass{
            width:100%;
            height:500px;
            border:1px solid #000;
        }
    </style>
    <script type="text/javascript">

        require(["esri/map",
            "dojo/dom","dojo/on","dojo/query",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/symbols/SimpleMarkerSymbol",
            "esri/symbols/SimpleLineSymbol",
            "esri/symbols/SimpleFillSymbol",
            "esri/toolbars/draw",
            "esri/graphic",
            "esri/tasks/GeometryService",
            "dojo/colors",
            "dojo/domReady!"],
            function (Map,dom,on,query,
                ArcGISDynamicMapServiceLayer,
                SimpleMarkerSymbol,
                SimpleLineSymbol,
                SimpleFillSymbol,
                Draw,
                Graphic,
                GeometryService,
                Color) {
                var map = new esri.Map("mapDiv");
                var layer = new esri.layers.ArcGISDynamicMapServiceLayer
                ("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer");
                map.addLayer(layer);
                //创建几何服务对象
                var geometryServices = new GeometryService("http://localhost:6080/arcgis/rest/services/Utilities/Geometry/GeometryServer");
                //一个标志位，用于记录是绘制第几个参数
                //1代表第一个参数
                //2代表第二个参数
                var flag;
                //第一个参数  注意第一个参数是多个形状
                var geometries=[];
                //第二个参数  注意第二个参数是一个形状
                var paramGeometry;
                //使用toolbar上的绘图工具
                var toolBar = new Draw(map);
                
                //线符号
                lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //第一个参数的面符号
                polygonSymbol1 = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new Color([255, 0, 0, 0.25]));
                //第二个参数的面符号
                polygonSymbol2 = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new Color([0, 255, 0, 0.25]));
                //求交之后的面符号
                polygonSymbol3 = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new Color([0, 0, 255, 0.25]));
                //给绘制polygon的按钮绑定事件
                query(".pbtn").on("click",function(event){
                    var value=this.innerHTML;
                    switch(value){
                        case "第一个参数":{
                            flag=1;
                            break;
                            
                        }
                        case "第二个参数":{
                            flag=2;
                            break;
                        }   
                    }
                    //激活绘制多边形
                    toolBar.activate(Draw.POLYGON, {
                        showTooltips:true
                    })
                    
                })
                //绘图结束绑定事件
                on(toolBar,"draw-end",function(result){
                    //获得面形状
                    var geometry=result.geometry;
                    //创建Graphic
                    var graphicpoint;

                    //很重要！！！在这里构建参数对象
                    if(flag==1)
                    {
                        //创建Graphic
                        graphicpoint = new Graphic(geometry, polygonSymbol1);
                        geometries.push(geometry)
                    }
                    else if(flag==2)
                    {
                        //创建Graphic
                        graphicpoint = new Graphic(geometry, polygonSymbol2);
                        paramGeometry=geometry;
                    }   
                    map.graphics.add(graphicpoint); 
                    //关闭绘图工具
                    toolBar.deactivate();                   
                })
                //给求交按钮绑定事件
                on(dom.byId("Btn"),"click",function(){
                    //在这里在说一边，第一个参数是图形集合，第二个参数是一个图形
                    geometryServices.intersect(geometries,paramGeometry,function(result){
                        for(var idx in result)
                        {
                            //创建Graphic
                            var graphic=new Graphic(result[idx],polygonSymbol3)
                            //将每一个结果都放在map中
                            map.graphics.add(graphic)
                        }
                    })
                })
               
            });
    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div>
    <button class="pbtn" >第一个参数</button>
    <button class="pbtn" >第二个参数</button>
    <input id="Btn" type="button" value="求交分析" />
</body>
</html>
```