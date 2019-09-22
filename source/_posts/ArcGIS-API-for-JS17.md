---
title: ArcGIS API For Javascript调用结果地图服务
date: 2019-08-06 16:07:59
categories:
- ArcGIS
tags: [ArcGIS API for JS]
---
结果地图服务也是GP服务的一种，当GP服务的结果返回栅格数据时，我们往往使用结果地图服务来对生成的栅格数据进行渲染，本实例是：克里金差值
### ArcGIS API调用结果地图服务
首先我们查看一下服务的定义：
![01.png](01.png)
![02.png](02.png)
####代码实现
在页面添加地图（略）
创建两个按钮（一个是画点，一个是进行插值运算）
```
<input id="Btn" type="button" value="画点" />
<input id="king" type="button" value="插值分析" />
```
定义点集合对象
```
//定义点集合
var pointSet = new FeatureSet();
//定义点符号
var psymbol = new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CROSS, 12,
new SimpleLineSymbol(SimpleLineSymbol.STYLE_SOLID,
new dojo.Color([255, 0, 0]), 2),
new dojo.Color([0, 255, 0, 0.25]));
```
给画点的按钮绑定事件
```
        //绑定画点的事件
        on(dom.byId("Btn"),"click",function(e){
            //定义绘图工具
                    var toolBar = new Draw(MyMap, { showTooltips: true });
                    //激活绘图工具
                    toolBar.activate(Draw.POINT);
                    //定义一个i，为了给graphic赋予属性，为差值做准备。
                    var i=20;
                    //给绘图工具绑定绘图完成事件
                    on(toolBar, "draw-complete", function(result){
                        //保证每一次i的值都不一样，如果所有点的属性都一样，是不可以差值的。
                        i=i+40;
                        //获得几何形状
                        var geometry = result.geometry;
                        //给图形一个m属性，我们后面对m进行差值
                        var graphicpoint = new Graphic(geometry, psymbol,{"m":i});
                        pointSet.features.push(graphicpoint);   
                        MyMap.graphics.add(graphicpoint);
                    }); 
        });
```
给插值按钮绑定事件
```
//声明gp对象
var gp;
on(dom.byId("king"),"click",function(e){
                    //定义gp服务对象
                    gp= new Geoprocessor("http://localhost:6080/arcgis/rest/services/Test/myKing/GPServer/king");
                    //定义GP服务参数对象
                    var gpParams={};
                    //添加点集合的字段信息
                    pointSet.fields=[{
                    "name":"m",
                    "type":"esriFieldTypeSmallInteger",
                    "alias":"m"
                    }];
                    //添加Input参数
                    gpParams.Input=pointSet;
                    //添加Z属性：即差值的属性
                    gpParams.Z="m";
                    gp.submitJob(gpParams,completeCallback);
})
```
回掉函数处理结果
```
function completeCallback(jobInfo)
                {
                    gp.on("get-result-image-layer-complete",function(gpLayer){
                        MyMap.addLayer(gpLayer["layer"])
                    })
                    gp.getResultImageLayer(jobInfo.jobId,"out");
                    
                    
                    
}
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
            "esri/layers/ImageParameters",
            "dojo/domReady!"],
            function (Map,
                ArcGISDynamicMapServiceLayer,
                dom,
                Geoprocessor,
                FeatureSet,
                SimpleMarkerSymbol,
                SimpleLineSymbol,
                Draw,
                SimpleFillSymbol,on,Graphic,ImageParameters) {
                var MyMap = new Map("MyMapDiv");
                var layer = new ArcGISDynamicMapServiceLayer
                ("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer");
                MyMap.addLayer(layer)               
                
                //定义点集合对象
                var pointSet = new FeatureSet();
                //定义点符号
                var psymbol = new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CROSS, 12,
                new SimpleLineSymbol(SimpleLineSymbol.STYLE_SOLID,
                new dojo.Color([255, 0, 0]), 2),
                new dojo.Color([0, 255, 0, 0.25]));
                //绑定画点的事件
                on(dom.byId("Btn"),"click",function(e){
                    //定义绘图工具
                    var toolBar = new Draw(MyMap, { showTooltips: true });
                    //激活绘图工具
                    toolBar.activate(Draw.POINT);
                    //定义一个i，为了给graphic赋予属性，为差值做准备。
                    var i=20;
                    //给绘图工具绑定绘图完成事件
                    on(toolBar, "draw-complete", function(result){
                        //保证每一次i的值都不一样，如果所有点的属性都一样，是不可以差值的。
                        i=i+40;
                        //获得几何形状
                        var geometry = result.geometry;
                        //给图形一个m属性，我们后面对m进行差值
                        var graphicpoint = new Graphic(geometry, psymbol,{"m":i});
                        pointSet.features.push(graphicpoint);   
                        MyMap.graphics.add(graphicpoint);
                    }); 
                });
                var gp;
                on(dom.byId("king"),"click",function(e){
                    //定义gp服务对象
                    gp= new Geoprocessor("http://localhost:6080/arcgis/rest/services/Test/myKing/GPServer/king");
                    //定义GP服务参数对象
                    var gpParams={};
                    //添加点集合的字段信息
                    pointSet.fields=[{
                    "name":"m",
                    "type":"esriFieldTypeSmallInteger",
                    "alias":"m"
                    }];
                    //添加Input参数
                    gpParams.Input=pointSet;
                    //添加Z属性：即差值的属性
                    gpParams.Z="m";
                    //执行gp服务，注意这里是执行submitJob，因为是同步的gp服务
                    gp.submitJob(gpParams,completeCallback);
                })          
                function completeCallback(jobInfo)
                {
                    //给gp服务绑定获得图层事件
                    gp.on("get-result-image-layer-complete",function(gpLayer){
                        //将图层添加到地图中
                        MyMap.addLayer(gpLayer["layer"])
                    })
                    //注意out是服务定义的参数
                    gp.getResultImageLayer(jobInfo.jobId,"out");
                                                           
                }   

            });


    </script>
</head>
<body>
    <div id="MyMapDiv" class="MapClass"></div>
    <input id="Btn" type="button" value="画点" />
    <input id="king" type="button" value="插值分析" />

</body>
</html>
```
![03.png](03.png)