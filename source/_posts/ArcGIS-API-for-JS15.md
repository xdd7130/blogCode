---
title: ArcGIS API For Javascript之渲染实现
date: 2019-08-06 15:43:25
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
在 GIS当中，总要时不时的制作几张专题图，制作专题图的过程中，不可避免的就要涉及到图层的渲染问题（即改变图层的颜色信息），渲染分为客户端渲染，服务器渲染，客户端渲染要求我们的服务必须是要素服务，或者是可以使用FeatureLayer接收的服务图层
### 需求
首先我们查看一下数据（宿舍楼信息）
![01.png](01.png)
#### 需求一（唯一值渲染）
根据alias属性进行唯一值渲染
##### 代码实现
首先我们先将地图加载进来
```
var map = new esri.Map("MyMapDiv");
var layer = new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/my/fea/MapServer");
map.addLayer(layer);
```
定义一个渲染按钮
```
<button id="btn">渲染</button>
```
给按钮绑定相应的事件
```
on(btn,"click",function(){
                        //定义一个要素图层
                        var featureLayer = new FeatureLayer(
                        "http://localhost:6080/arcgis/rest/services/Test/ft/MapServer/1",
                         {
                            mode:FeatureLayer.MODE_SNAPSHOT,
                            outFields: ["*"]
                         });
                        //定义线符号
                        var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                        //定义面符号
                        var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color("#FFFFCC"));
                        //定义唯一值渲染器，对字段alias进行渲染，fill是默认的渲染符号
                        var renderer = new UniqueValueRenderer(fill, "alias");
                        //设置渲染的方式
                        renderer.addValue("C区宿舍", new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([0, 255, 0, 0.5])));
                        renderer.addValue("B区宿舍", new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([0, 0, 255, 0.5])));
                        renderer.addValue("A区宿舍",new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([255, 0, 255, 0.5])));
                        renderer.addValue("公共出口", new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([255, 255, 255, 0.75])));
                        featureLayer.setRenderer(renderer);
                        map.addLayer(featureLayer);              
                    })  
```
##### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>UniqueValueRenderer</title>
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
                    "dojo/colors",
                    "esri/symbols/SimpleFillSymbol",
                    "esri/layers/FeatureLayer",
                    "esri/symbols/SimpleLineSymbol",
                    "esri/renderers/UniqueValueRenderer",
                    "dojo/domReady!"],
                function (Map, ArcGISDynamicMapServiceLayer,on,dom,Color,SimpleFillSymbol, FeatureLayer, SimpleLineSymbol,UniqueValueRenderer) {
                    var map = new esri.Map("MyMapDiv");
                    var layer = new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer");
                    map.addLayer(layer);
                    //获得id为btn的DOM元素
                    var btn=dom.byId("btn");
                    //绑定相应的事件
                    on(btn,"click",function(){
                        //定义一个要素图层
                        var featureLayer = new FeatureLayer(
                        "http://localhost:6080/arcgis/rest/services/Test/ft/MapServer/1",
                         {
                            mode:FeatureLayer.MODE_SNAPSHOT,
                            outFields: ["*"]
                         });
                        //定义线符号
                        var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                        //定义面符号
                        var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color("#FFFFCC"));
                        //定义唯一值渲染器，对字段alias进行渲染，fill是默认的渲染符号
                        var renderer = new UniqueValueRenderer(fill, "alias");
                        //设置渲染的方式
                        renderer.addValue("C区宿舍", new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([0, 255, 0, 0.5])));
                        renderer.addValue("B区宿舍", new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([0, 0, 255, 0.5])));
                        renderer.addValue("A区宿舍",new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([255, 0, 255, 0.5])));
                        renderer.addValue("公共出口", new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([255, 255, 255, 0.75])));
                        featureLayer.setRenderer(renderer);
                        map.addLayer(featureLayer);              
                    })  
                });


    </script>
</head>
<body>
<div id="MyMapDiv" class="MapClass"></div>
<button id="btn">渲染</button>
</body>
</html>
```
运行结果之后：
![02.png](02.png)
#### 需求二（分类渲染）
根据面积字段实现分类渲染，由于代码只是修改了渲染器，所以直接将代码贴出来
##### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>UniqueValueRenderer</title>
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
                    "dojo/colors",
                    "esri/symbols/SimpleFillSymbol",
                    "esri/layers/FeatureLayer",
                    "esri/symbols/SimpleLineSymbol",
                    "esri/renderers/ClassBreaksRenderer",
                    "dojo/domReady!"],
                function (Map, ArcGISDynamicMapServiceLayer,on,dom,Color,SimpleFillSymbol, FeatureLayer, SimpleLineSymbol,ClassBreaksRenderer) {
                    var map = new esri.Map("MyMapDiv");
                    var layer = new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer");
                    map.addLayer(layer);
                    //获得id为btn的DOM元素
                    var btn=dom.byId("btn");
                    //绑定相应的事件
                    on(btn,"click",function(){
                        //定义一个要素图层
                        var featureLayer = new FeatureLayer(
                        "http://localhost:6080/arcgis/rest/services/Test/ft/MapServer/1",
                         {
                            mode:FeatureLayer.MODE_SNAPSHOT,
                            outFields: ["*"]
                         });
                        //定义线符号
                        var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                        //定义面符号
                        var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color("#FFFFCC"));
                        //定义唯一值渲染器，对字段alias进行渲染，fill是默认的渲染符号
                        var renderer = new ClassBreaksRenderer(fill, "OBJECTID");
                        //设置渲染间隔
                        renderer.addBreak(0,20, new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([0, 255, 0, 0.5])));
                        renderer.addBreak(20,30, new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([0, 0, 255, 0.5])));
                        renderer.addBreak(30,40,new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([255, 0, 255, 0.5])));
                        renderer.addBreak(40,60, new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new Color([255, 255, 255, 0.75])));
                        featureLayer.setRenderer(renderer);
                        map.addLayer(featureLayer);              
                    })  
                });


    </script>
</head>
<body>
<div id="MyMapDiv" class="MapClass"></div>
<button id="btn">渲染</button>
</body>
</html>
```
运行结果后：
![03.png](03.png)
#### 需求三（服务器渲染）
当数据量过大时，客户端渲染变显得有些慢了，此时可以使用服务器渲染，服务器渲染提供了两种渲染的方式：唯一值渲染，分类渲染，在此处我们用到的为分类渲染。

注意：服务器渲染不需要要素服务。

创建一个按钮（用于触发服务器渲染）
```
<input type="button" id="Btn" value="服务器渲染" />
```
给按钮绑定点击事件
```
on(btn,"click",function(e){
                //定义线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                //定义面符号
                var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new dojo.Color("#FFFFCC"));
                //创建服务器渲染对象
                var RenderAnalyst = new GenerateRendererTask("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer/1");
                //创建服务器渲染参数对象
                var params = new GenerateRendererParameters();   
                //创建颜色坡道对象
                var colorRamp = new AlgorithmicColorRamp();
                //颜色的起始位置
                colorRamp.fromColor = dojo.colorFromHex("#998ec3");
                //颜色的终止位置
                colorRamp.toColor = dojo.colorFromHex("#f1a340");
                //颜色的类型
                colorRamp.algorithm = "hsv";
                //告诉服务器我们需要什么样子的渲染器
                var unidef = new ClassBreaksDefinition();
                //分类的字段
                unidef.classificationField = "Shape.STArea()";
                //如何分类
                unidef.classificationMethod="natural-breaks";
                //分为几类？
                unidef.breakCount = 3;
                //默认的符号
                unidef.baseSymbol=fill;
                //将自定义的颜色坡道告诉服务器
                unidef.colorRamp=colorRamp;
                //给渲染参数对象赋值
                params.classificationDefinition = unidef;
                //进行服务器渲染
                RenderAnalyst.execute(params, showRender);
                
                
            });
```
处理服务器返回的结果
```
//处理服务器返回来的结果
            function showRender(renderer) {
                
                if (renderer != null) {
                    //定义要素图层，注意：这个地方我们用到并不是要素服务，而是地图服务
                    var featureLayer = new FeatureLayer("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer/1",{
                        mode:  FeatureLayer.MODE_SNAPSHOT,
                        outFields: ["*"]
                    });     
                    //赋予渲染器
                    featureLayer.setRenderer(renderer);
                    featureLayer.refresh();
                    MyMap.addLayer(featureLayer);
                }
            }
```
#### 全部代码
```
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <title>GenerateRendererTask</title>
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
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "dojo/dom",
            "dojo/on",
            "esri/tasks/GenerateRendererTask",
            "esri/layers/FeatureLayer",
            "esri/symbols/SimpleFillSymbol",
            "esri/symbols/SimpleLineSymbol",
            "esri/tasks/GenerateRendererParameters",
            "esri/tasks/ClassBreaksDefinition",
            "esri/tasks/AlgorithmicColorRamp",
            "dojo/domReady!"], function
                (Map,
                 ArcGISDynamicMapServiceLayer,
                 dom,on,
                 GenerateRendererTask,
                 FeatureLayer,
                 SimpleFillSymbol,
                 SimpleLineSymbol,
                 GenerateRendererParameters,
                 ClassBreaksDefinition,
                 AlgorithmicColorRamp
                ) {
            //服务器端渲染
            var MyMap = new Map("MyMapDiv");
            var layer = new ArcGISDynamicMapServiceLayer
            ("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer/");
            MyMap.addLayer(layer)
            //获得id为Btn的DOM对象
            var btn=dom.byId("Btn");
            //给btn绑定点击事件
            on(btn,"click",function(e){
                //定义线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                //定义面符号
                var fill=new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,new dojo.Color("#FFFFCC"));
                //创建服务器渲染对象
                var RenderAnalyst = new GenerateRendererTask("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer/1");
                //创建服务器渲染参数对象
                var params = new GenerateRendererParameters();   
                //创建颜色坡道对象
                var colorRamp = new AlgorithmicColorRamp();
                //颜色的起始位置
                colorRamp.fromColor = dojo.colorFromHex("#998ec3");
                //颜色的终止位置
                colorRamp.toColor = dojo.colorFromHex("#f1a340");
                //颜色的类型
                colorRamp.algorithm = "hsv";
                //告诉服务器我们需要什么样子的渲染器
                var unidef = new ClassBreaksDefinition();
                //分类的字段
                unidef.classificationField = "Shape.STArea()";
                //如何分类
                unidef.classificationMethod="natural-breaks";
                //分为几类？
                unidef.breakCount = 3;
                //默认的符号
                unidef.baseSymbol=fill;
                //将自定义的颜色坡道告诉服务器
                unidef.colorRamp=colorRamp;
                //给渲染参数对象赋值
                params.classificationDefinition = unidef;
                //进行服务器渲染
                RenderAnalyst.execute(params, showRender);
                
                
            });
            //处理服务器返回来的结果
            function showRender(renderer) {
                
                if (renderer != null) {
                    //定义要素图层，注意：这个地方我们用到并不是要素服务，而是地图服务
                    var featureLayer = new FeatureLayer("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer/1",{
                        mode:  FeatureLayer.MODE_SNAPSHOT,
                        outFields: ["*"]
                    });     
                    //赋予渲染器
                    featureLayer.setRenderer(renderer);
                    featureLayer.refresh();
                    MyMap.addLayer(featureLayer);
                }
            }
        });
    </script>
</head>
<body>
<div id="MyMapDiv" class="MapClass"></div>
<input type="button" id="Btn" value="服务器渲染" />
</body>
</html>
```
![04.png](04.png)