---
title: ArcGIS API For Javascript调用GP服务
date: 2019-08-06 15:59:51
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
ArcGIS API中关于GP服务的调用是比较容易出错的，出错的大部分原因便是Web API调用GP服务的时候参数不对应导致的
### ArcGIS API调用GP服务
![01.png](01.png)
然后接下来我们将在Web中调用我们的GP服务
#### 代码实现
在页面加入地图（略）
在页面添加两个按钮（第一个按钮用于画点，第二个按钮用于缓冲区分析）
```
<input id="Btn" type="button" value="画点" />
<input id="buffer" type="button" value="缓冲区分析" />
```
定义一个点几何对象（与服务定义的类型对应）
```
//定义点几何对象
var pointSet = new FeatureSet();
```
定义点符号对象
```
var psymbol = new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CROSS, 12,
new SimpleLineSymbol(SimpleLineSymbol.STYLE_SOLID,
new dojo.Color([255, 0, 0]), 2),
new dojo.Color([0, 255, 0, 0.25]));
```
给画点按钮绑定点击事件
```
on(dom.byId("Btn"),"click",function(e){
                    //定义绘图对象
                    var toolBar= new Draw(MyMap, { showTooltips: true });
                    //激活绘图对象
                    toolBar.activate(Draw.POINT);
                    on(toolBar, "draw-complete", function(result){
                        //获得绘图结束的点对象
                        var geometry = result.geometry;
                        //根据点对象生成相应的图形
                        var graphic = new Graphic(geometry, psymbol);
                        //将点对象存储在点几何中
                        pointSet.features.push(graphic);
                        //将图形存放在地图中，然后得以显示
                        MyMap.graphics.add(graphic);

                    }); 
});
```
给缓冲区分析按钮添加绑定事件
```
on(dom.byId("buffer"),"click",function(e){
                    //定义GP服务对象
                    var buffer = new Geoprocessor("http://localhost:6080/arcgis/rest/services/Test/MyBuffer/GPServer/Mybuffer");
                    //构建GP服务参数
                    var gpParams={};
                    //GP服务的Input参数
                    gpParams.Input=pointSet;
                    //GP服务的dis参数
                    var dis=new LinearUnit({
                        "distance": 100,
                        "units": "esriMeters"
                    });
                    gpParams.dis=dis;
                    //执行GP服务
                    buffer.execute(gpParams, showResult);
})
```
处理GP服务返回的结果
```
function showResult(results, messages){
                    var features = results[0].value.features;
                    for (var i = 0; i < features.length; i++) {
                        var graphic = features[i];
                        //定义线符号
                        var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASHDOT, new dojo.Color([255, 0, 0]), 1);
                        //定义面符号
                        var PolygonSymbol = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new dojo.Color([255, 255, 0, 0.25]));
                        //设置面符号
                        graphic.setSymbol(PolygonSymbol);
                        MyMap.graphics.add(graphic);
                    }
 }
```
运行代码发现错误
![02.png](02.png)
此时我们观察前台传给后台的参数
```
Input:{
            "geometryType":"esriGeometryPoint",
            "features":[{"geometry":{"x":21264961.8441914,"y":3997922.462940743,"spatialReference":{"wkid":2334,"latestWkid":2334}}}],
            "sr":{"wkid":2334,"latestWkid":2334}
    }
dis: {"distance":100,"units":"esriMeters"}
```
传给后台的参数和GP服务的参数对比，发现Input参数，缺少fields字段：
![03.png](03.png)
修改错误
我们给运行GP服务之前给Input参数添加上Fields字段:
```
on(dom.byId("buffer"),"click",function(e){
                    //定义GP服务对象
                    var buffer = new Geoprocessor("http://localhost:6080/arcgis/rest/services/Test/MyBuffer/GPServer/Mybuffer");
                    //构建GP服务参数
                    var gpParams={};
                    //添加fields字段，为了和后台服务字段匹配
                    pointSet.fields=[];
                    //GP服务的Input参数
                    gpParams.Input=pointSet;
                    //GP服务的dis参数
                    var dis=new LinearUnit({
                        "distance": 100,
                        "units": "esriMeters"
                    });
                    gpParams.dis=dis;
                    //执行GP服务
                    buffer.execute(gpParams, showResult);
});
```
#### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>GP</title>
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
    <script type="text/javascript" charset="utf-8">

        require(["esri/map",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "dojo/dom",
            "esri/tasks/Geoprocessor",
            "esri/tasks/FeatureSet",
            "esri/symbols/SimpleMarkerSymbol",
            "esri/symbols/SimpleLineSymbol",
            "esri/toolbars/draw",
            "esri/symbols/SimpleFillSymbol",
            "dojo/on","esri/graphic",
            "esri/tasks/LinearUnit",
            "dojo/domReady!"],
            function (Map,
                ArcGISDynamicMapServiceLayer,
                dom,
                Geoprocessor,
                FeatureSet,
                SimpleMarkerSymbol,
                SimpleLineSymbol,
                Draw,
                SimpleFillSymbol,on,Graphic,LinearUnit) {
                var MyMap = new Map("MyMapDiv");
                
                var layer = new ArcGISDynamicMapServiceLayer
                ("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer");
                MyMap.addLayer(layer);
                //定义点几何对象
                var pointSet = new FeatureSet();
                var psymbol = new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CROSS, 12,
                new SimpleLineSymbol(SimpleLineSymbol.STYLE_SOLID,
                new dojo.Color([255, 0, 0]), 2),
                new dojo.Color([0, 255, 0, 0.25]));
                on(dom.byId("Btn"),"click",function(e){
                    //定义绘图对象
                    var toolBar= new Draw(MyMap, { showTooltips: true });
                    //激活绘图对象
                    toolBar.activate(Draw.POINT);
                    on(toolBar, "draw-complete", function(result){
                        //获得绘图结束的点对象
                        var geometry = result.geometry;
                        //根据点对象生成相应的图形
                        var graphic = new Graphic(geometry,psymbol);
                        //将点对象存储在点几何中
                        pointSet.features.push(graphic);
                        //将图形存放在地图中，然后得以显示
                        MyMap.graphics.add(graphic);

                    }); 
                });
                
                on(dom.byId("buffer"),"click",function(e){
                    //定义GP服务对象
                    var buffer = new Geoprocessor("http://localhost:6080/arcgis/rest/services/Test/MyBuffer/GPServer/Mybuffer");
                    //构建GP服务参数
                    var gpParams={};
                    //添加fields字段，为了和后台服务字段匹配
                    pointSet.fields=[];
                    //GP服务的Input参数
                    gpParams.Input=pointSet;
                    //GP服务的dis参数
                    var dis=new LinearUnit({
                        "distance": 100,
                        "units": "esriMeters"
                    });
                    gpParams.dis=dis;
                    //执行GP服务
                    buffer.execute(gpParams, showResult);
                });
                function showResult(results, messages)
                {
                    var features = results[0].value.features;
                    for (var i = 0; i < features.length; i++) {
                        var graphic = features[i];
                        //定义线符号
                        var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASHDOT, new dojo.Color([255, 0, 0]), 1);
                        //定义面符号
                        var PolygonSymbol = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol, new dojo.Color([255, 255, 0, 0.25]));
                        //设置面符号
                        graphic.setSymbol(PolygonSymbol);
                        MyMap.graphics.add(graphic);
                    }
                }
                
            });


    </script>
</head>
<body>
    <div id="MyMapDiv" class="MapClass"></div>
    <input id="Btn" type="button" value="画点" />
    <input id="buffer" type="button" value="缓冲区分析" />
</body>
</html>
```
![04.png](04.png)