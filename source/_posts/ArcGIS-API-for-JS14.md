---
title: ArcGIS API For Javascript之查询功能
date: 2019-08-06 15:26:24
categories:
- ArcGIS
tags: [ArcGIS API for JS]
---
在ArcGIS API中查询功能是非常常用的，Esri给我们提供了三个类用于实现矢量数据查询功能。FindTask,QueryTask,IdentifyTask，他们之间的区别为：

* FindTask只能进行属性查询，QueryTask,IdentifyTask两个类既可以进行属性查询也可以进行空间查询。
* 对于QueryTask,IdentifyTask两个类，QueryTask只可应用于一个单独的图层，IdentifyTask可应用于地图服务和多个图层
* QueryTask可以进行简单的统计功能。

### 需求
#### 利用FindTask实现简单的属性查询
![01.png](01.png)
我们将name属性为J4的教学楼查询出来，在页面上显示J4是哪一个学院？（机电学院），并且将J4的楼高亮显示
代码实现

在页面加入一个地图（略）
在页面添加一个button和一个div（button用于属性查询，div用于显示楼层的名字）
```
    <input type="button" value="属性查询" id="Btn"/>
    <div id="divShowResult"></div>
```
创建属性查询对象并给button绑定点击事件
```
//地图服务的URL
                var MapServer = "http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer";
                //创建属性查询对象
                var findTask = new FindTask(MapServer);
                //创建属性查询参数
                var findParams = new FindParameters();
                on(dom.byId("Btn"),"click",function(){
                    //是否返回给我们几何信息
                    findParams.returnGeometry = true;
                    //对哪一个图层进行属性查询
                    findParams.layerIds = [1];
                    //查询的字段
                    findParams.searchFields = ["name"];
                    //searchText和searchFields结合使用，即查询name=J4
                    findParams.searchText = "J4";
                    //执行查询对象
                    findTask.execute(findParams, ShowFindResult);
                })
```
处理属性查询返回给我们的数据
```
function ShowFindResult(queryResult) {
                    //创建线符号
                    var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                    //创建面符号
                    var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol);
                    if (queryResult.length == 0) {
                        dom.byId("divShowResult").innerHTML = "";
                        return;
                    }
                    var htmls = "";
                    if (queryResult.length >= 1) {
                        htmls = htmls + "<table style=\"width: 100%\">";
                        htmls = htmls + "<tr><td>名称</td></tr>";
                        for (var i = 0; i < queryResult.length; i++) {
                            //获得图形graphic
                            var graphic = queryResult[i].feature;
                            //赋予相应的符号
                            graphic.setSymbol(fill);
                            //将graphic添加到地图中，从而实现高亮效果
                            map.graphics.add(graphic);
                            //获得教学楼名称（此处是和shp属性表对应的）
                            var ptName = graphic.attributes["alias"];
                            if (i % 2 == 0)
                                htmls = htmls + "<tr>";
                            else
                                htmls = htmls + "<tr bgcolor=\"#F0F0F0\">";
                            htmls = htmls + "<td><a href=\"#\" \">" + ptName + "</a></td>";
                            htmls = htmls + "</tr>";
                        }
                        htmls = htmls + "</table>";
                        //将属性绑定在divShowResult上面
                        dom.byId("divShowResult").innerHTML = htmls;
                    }
                }
```
##### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>FindTask</title>
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
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "dojo/on",
            "dojo/dom",
            "esri/tasks/FindTask",
            "esri/tasks/FindParameters",
            "esri/symbols/SimpleLineSymbol",
            "esri/symbols/SimpleFillSymbol", 
            "esri/graphic",
            "dojo/domReady!"],
            function (Map, ArcGISDynamicMapServiceLayer,on,dom,
            FindTask, FindParameters, 
            SimpleLineSymbol,SimpleFillSymbol,Graphic) {   
                var map = new esri.Map("mapDiv");
                //地图服务的URL
                var MapServer = "http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer";
                var layer = new esri.layers.ArcGISDynamicMapServiceLayer(MapServer);
                map.addLayer(layer)
                //创建属性查询对象
                var findTask = new FindTask(MapServer);
                //创建属性查询参数
                var findParams = new FindParameters();
                on(dom.byId("Btn"),"click",function(){
                    //是否返回给我们几何信息
                    findParams.returnGeometry = true;
                    //对哪一个图层进行属性查询
                    findParams.layerIds = [1];
                    //查询的字段
                    findParams.searchFields = ["name"];
                    //searchText和searchFields结合使用，即查询name=J4
                    findParams.searchText = "J4";
                    //执行查询对象
                    findTask.execute(findParams, ShowFindResult);
                })
                function ShowFindResult(queryResult) {
                    //创建线符号
                    var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                    //创建面符号
                    var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol);
                    if (queryResult.length == 0) {
                        dom.byId("divShowResult").innerHTML = "";
                        return;
                    }
                    var htmls = "";
                    if (queryResult.length >= 1) {
                        htmls = htmls + "<table style=\"width: 100%\">";
                        htmls = htmls + "<tr><td>名称</td></tr>";
                        for (var i = 0; i < queryResult.length; i++) {
                            //获得图形graphic
                            var graphic = queryResult[i].feature;
                            //赋予相应的符号
                            graphic.setSymbol(fill);
                            //将graphic添加到地图中，从而实现高亮效果
                            map.graphics.add(graphic);
                            //获得教学楼名称（此处是和shp属性表对应的）
                            var ptName = graphic.attributes["alias"];
                            if (i % 2 == 0)
                                htmls = htmls + "<tr>";
                            else
                                htmls = htmls + "<tr bgcolor=\"#F0F0F0\">";
                            htmls = htmls + "<td><a href=\"#\" \">" + ptName + "</a></td>";
                            htmls = htmls + "</tr>";
                        }
                        htmls = htmls + "</table>";
                        //将属性绑定在divShowResult上面
                        dom.byId("divShowResult").innerHTML = htmls;
                    }
                }
            
        });


    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div> 
    <input type="button" value="属性查询" id="Btn"/>
    <div id="divShowResult"></div>
</body>
</html>
```
运行之后的结果为：
![01.png](01.png)
#### 利用QueryTask实现空间查询
此时的的需求是：我们在地图上画一个图形，然后和图形相交的教学楼选择出来。
```
1.使用draw交互绘制几何形状
2.根据geometry形状构建空间查询参数
3.执行空间分析对象
4.处理空间分析的结果
```
创建一个button和一个div（button用于激活绘图工具，div用户显示用户选择的教学楼的名称）
```
<input type="button" value="空间查询" id="Btn"/>
<div id="divShowResult"></div>
```
给button绑定事件（激活绘图工具）
```
            //定义一个绘图工具
            var toolBar = new Draw(map);
            //给button绑定事件
            on(dom.byId("Btn"),"click",function(){  
                //激活绘图工具，我要绘制一个面图形
                toolBar.activate(Draw.POLYGON);
            })
```
给绘图工具绑定绘图完成事件，绘图完成执行queryGraphic函数,并将绘制的geometry传入函数
```
        on(toolBar, "draw-complete", function (result) {
                //获得绘图得到的面
                var geometry=result.geometry;
                //关闭绘图工具
                toolBar.deactivate();
                queryGraphic(geometry);
            });
```
编写queryGraphic函数
```
function queryGraphic(geometry) {
                //创建查询对象，注意：服务的后面有一个编号，代表对那一个图层进行查询
                var queryTask = new QueryTask("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer/1");
                //创建查询参数对象
                var query = new Query();
                //空间查询的几何对象
                query.geometry = geometry;
                //服务器给我们返回的字段信息，*代表返回所有字段
                query.outFields = ["*"];
                //空间参考信息
                query.outSpatialReference = map.spatialReference;
                //查询的标准，此处代表和geometry相交的图形都要返回
                query.spatialRelationship = Query.SPATIAL_REL_INTERSECTS;
                //是否返回几何信息
                query.returnGeometry = true;
                //执行空间查询
                queryTask.execute(query, showQueryResult);
            }
```
处理返回的结果信息
```
function showQueryResult(queryResult) {
                //创建线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                //创建面符号
                var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol);
                if (queryResult.features.length == 0) {
                    dom.byId("divShowResult").innerHTML = "";
                    return;
                }
                var htmls = "";
                if (queryResult.features.length >= 1) {
                    htmls = htmls + "<table style=\"width: 100%\">";
                    htmls = htmls + "<tr><td>名称</td></tr>";
                    for (var i = 0; i < queryResult.features.length; i++) {
                        //得到graphic
                        var graphic = queryResult.features[i];
                        //给图形赋予符号
                        graphic.setSymbol(fill);
                        //添加到地图从而实现高亮效果
                        map.graphics.add(graphic);
                        //获得教学楼的名称信息，此处应和shp的属性表对应
                        var ptName = graphic.attributes["alias"];
                        if (i % 2 == 0)
                            htmls = htmls + "<tr>";
                        else
                            htmls = htmls + "<tr bgcolor=\"#F0F0F0\">";
                        htmls = htmls + "<td><a href=\"#\"\">" + ptName + "</a></td>";
                        htmls = htmls + "</tr>";
                    }
                    htmls = htmls + "</table>";
                    //将教学楼的名称信息和divShowResult绑定
                    dom.byId("divShowResult").innerHTML = htmls;
                }
            }
```
##### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>QueryTask</title>
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
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "dojo/dom",
            "dojo/on",
            "esri/tasks/QueryTask",
            "esri/toolbars/draw",
            "esri/tasks/query",
            "esri/symbols/SimpleLineSymbol",
            "esri/symbols/SimpleFillSymbol", 
            "esri/graphic",
            "dojo/domReady!"],
            function (Map, ArcGISDynamicMapServiceLayer,dom, on,
            QueryTask, Draw, Query,
            SimpleLineSymbol,SimpleFillSymbol,Graphic) {
            var map = new Map("mapdiv");
            var layer = new ArcGISDynamicMapServiceLayer
            ("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer");
            map.addLayer(layer)
            //定义一个绘图工具
            var toolBar = new Draw(map);
            //给button绑定事件
            on(dom.byId("Btn"),"click",function(){  
                //激活绘图工具，我要绘制一个面图形
                toolBar.activate(Draw.POLYGON);
            })
            on(toolBar, "draw-complete", function (result) {
                //获得绘图得到的面
                var geometry=result.geometry;
                //关闭绘图工具
                toolBar.deactivate();
                queryGraphic(geometry);
            });
            
            function queryGraphic(geometry) {
                //创建查询对象，注意：服务的后面有一个编号，代表对那一个图层进行查询
                var queryTask = new QueryTask("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer/1");
                //创建查询参数对象
                var query = new Query();
                //空间查询的几何对象
                query.geometry = geometry;
                //服务器给我们返回的字段信息，*代表返回所有字段
                query.outFields = ["*"];
                //空间参考信息
                query.outSpatialReference = map.spatialReference;
                //查询的标准，此处代表和geometry相交的图形都要返回
                query.spatialRelationship = Query.SPATIAL_REL_INTERSECTS;
                //是否返回几何信息
                query.returnGeometry = true;
                //执行空间查询
                queryTask.execute(query, showQueryResult);
            }

            function showQueryResult(queryResult) {
                //创建线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                //创建面符号
                var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol);
                if (queryResult.features.length == 0) {
                    dom.byId("divShowResult").innerHTML = "";
                    return;
                }
                var htmls = "";
                if (queryResult.features.length >= 1) {
                    htmls = htmls + "<table style=\"width: 100%\">";
                    htmls = htmls + "<tr><td>名称</td></tr>";
                    for (var i = 0; i < queryResult.features.length; i++) {
                        //得到graphic
                        var graphic = queryResult.features[i];
                        //给图形赋予符号
                        graphic.setSymbol(fill);
                        //添加到地图从而实现高亮效果
                        map.graphics.add(graphic);
                        //获得教学楼的名称信息，此处应和shp的属性表对应
                        var ptName = graphic.attributes["alias"];
                        if (i % 2 == 0)
                            htmls = htmls + "<tr>";
                        else
                            htmls = htmls + "<tr bgcolor=\"#F0F0F0\">";
                        htmls = htmls + "<td><a href=\"#\"\">" + ptName + "</a></td>";
                        htmls = htmls + "</tr>";
                    }
                    htmls = htmls + "</table>";
                    //将教学楼的名称信息和divShowResult绑定
                    dom.byId("divShowResult").innerHTML = htmls;
                }
            }

           

            
        });


    </script>
</head>
<body>
    <div id="mapdiv" class="MapClass"></div>
    <input type="button" value="空间查询" id="Btn"/>
    <div id="divShowResult"></div>
</body>
</html>
```
运行之后的结果为：
![03.png](03.png)
#### 利用QueryTask进行属性查询
利用QueryTask进行属性查询和空间查询代码差不多。
#####全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>QueryTask</title>
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
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "dojo/dom",
            "dojo/on",
            "esri/tasks/QueryTask",
            "esri/tasks/query",
            "esri/symbols/SimpleLineSymbol",
            "esri/symbols/SimpleFillSymbol", 
            "esri/graphic",
            "dojo/domReady!"],
            function (Map, ArcGISDynamicMapServiceLayer,dom, on,
            QueryTask, Query,
            SimpleLineSymbol,SimpleFillSymbol,Graphic) {
                //根据div的id属性创建地图
                var map = new Map("mapDiv");
                //定义一个动态地图服务
                var layer = new ArcGISDynamicMapServiceLayer
                ("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer");
                //将图层添加到地图
                map.addLayer(layer)
                //给属性查询按钮添加click事件
                on(dom.byId("Btn"),"click",function(e){
                    //定义查询对象
                    var queryTask = new QueryTask
                    ("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer/1");
                    //定义查询参数对象
                    var query = new Query();
                    //查询条件，类似于sql语句的where子句
                    query.where = "name = 'J4'";;
                    //返回的字段信息：*代表返回全部字段
                    query.outFields = ["*"];
                    //是否返回几何形状
                    query.returnGeometry = true;
                    //执行属性查询
                    queryTask.execute(query, showQueryResult);
                })
                //属性查询完成之后，用showQueryResult来处理返回的结果
                function showQueryResult(queryResult)
                {
                    //创建线符号
                    var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                    //创建面符号
                    var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol);
                    if (queryResult.features.length == 0) {
                        dom.byId("divShowResult").innerHTML = "";
                        return;
                    }
                    var htmls = "";
                    if (queryResult.features.length >= 1) {
                        htmls = htmls + "<table style=\"width: 100%\">";
                        htmls = htmls + "<tr><td>名称</td></tr>";
                        for (var i = 0; i < queryResult.features.length; i++) {
                            //获得图形graphic
                            var graphic = queryResult.features[i];
                            //赋予相应的符号
                            graphic.setSymbol(fill);
                            //将graphic添加到地图中，从而实现高亮效果
                            map.graphics.add(graphic);
                            //获得教学楼名称（此处是和shp属性表对应的）
                            var ptName = graphic.attributes["alias"];
                            if (i % 2 == 0)
                                htmls = htmls + "<tr>";
                            else
                                htmls = htmls + "<tr bgcolor=\"#F0F0F0\">";
                            htmls = htmls + "<td><a href=\"#\" \">" + ptName + "</a></td>";
                            htmls = htmls + "</tr>";
                        }
                        htmls = htmls + "</table>";
                        //将属性绑定在divShowResult上面
                        dom.byId("divShowResult").innerHTML = htmls;
                    }
                }
                
              
         

            });


    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div>
    <input type="button" value="属性查询" id="Btn"/>
    <div id="divShowResult"></div>
</body>
</html>
```
运行之后的结果：
![04.png](04.png)
#### 利用IdentifyTask实现空间查询
IdentifyTask的使用与QueryTask十分类似，唯一不同的是IdentifyTask可以作用于多个图层，而QueryTask是作用于一个图层的。
##### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>WebGIS</title>
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
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "dojo/on",
            "dojo/dom",
            "esri/tasks/IdentifyTask",
            "esri/tasks/IdentifyParameters",
            "esri/toolbars/draw",
            "esri/symbols/SimpleFillSymbol",
            "esri/symbols/SimpleLineSymbol",
            "esri/graphic",
            "dojo/domReady!"],
            function (Map,
                ArcGISDynamicMapServiceLayer,
                on,
                dom,
                IdentifyTask,
                IdentifyParameters,
                Draw,
                SimpleFillSymbol,
                SimpleLineSymbol,
                Graphic
                ) {
                    var map = new Map("mapDiv");
                    //地图服务的url
                    MapServer = "http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer";
                    //定义一个动态地图服务
                    var layer = new ArcGISDynamicMapServiceLayer(MapServer);    
                    map.addLayer(layer)
                    //定义绘图对象
                    var toolBar = new Draw(map);
                    //绑定点击事件
                    on(dom.byId("Btn"),"click",function(e){
                        //激活绘图工具：绘制面
                        toolBar.activate(esri.toolbars.Draw.POLYGON);
                        
                    })
                    //给绘图工具绑定绘图完成事件
                    on(toolBar, "draw-complete", function (result)
                    {
                        //获得绘图得到的面
                        var geometry=result.geometry;
                        //关闭绘图工具
                        toolBar.deactivate();
                        //执行空间查询
                        identifyQuery(geometry);
                    });                   
                    function identifyQuery(geometry) {
                        //定义空间查询对象，注意他的参数是整个地图服务，而不是单个图层
                        var identifyTask = new IdentifyTask(MapServer);
                        //定义空间查询参数对象
                        var params = new IdentifyParameters();
                        //容差
                        params.tolerance = 5;
                        //是否返回几何信息
                        params.returnGeometry = true;
                        //空间查询的图层，此时是两个图层
                        params.layerIds = [1,3];
                        //空间查询的条件
                        params.layerOption = IdentifyParameters.LAYER_OPTION_ALL;
                        params.width = map.width;
                        params.height = map.height;
                        //空间查询的几何对象
                        params.geometry = geometry;
                        params.mapExtent = map.extent;
                        //执行空间查询
                        identifyTask.execute(params,showQueryResult);
                    }
                    //通过此函数处理查询之后的信息
                    function showQueryResult(idResults) {
                        //创建线符号
                        var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                        //创建面符号
                        var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol);
                        if (idResults.length > 0) {
                                var htmls = "<table style=\"width: 100%\">";
                                htmls = htmls + "<tr bgcolor=\"#E0E0E0\"><td> 图层 </td><td> 名 称</td></tr>";
                                for (var i = 0; i < idResults.length; i++) {
                                    var result = idResults[i];
                                    //获得图形graphic
                                    var graphic = result.feature;
                                    //设置图形的符号
                                    graphic.setSymbol(fill);
                                    //获得教学楼的名称信息
                                    var namevalue = result.feature.attributes.alias;
                                    if (i % 2 == 1) {
                                        htmls = htmls + "<tr  bgcolor=\"#E0E0E0\"><td>" +
                                        result.layerName + "</td><td>" + namevalue + "</td></tr>";
                                    }
                                    else {
                                        htmls = htmls + "<tr><td>" + result.layerName + "</td><td>"
                                        + namevalue + "</td></tr>";
                                    }
                                    
                                    map.graphics.add(graphic);
                                }
                                htmls = htmls + "</table>";
                                document.getElementById("divShowResult").innerHTML = htmls;
                            }
                            else {
                                document.getElementById("divShowResult").innerHTML = "";
                            }
                        }
                        
        });


    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div>
    <input type="button" value="空间查询---多边形" id="Btn"/>
    <div id="divShowResult"></div>
</body>
</html>
```
运行之后的结果：
![05.png](05.png)