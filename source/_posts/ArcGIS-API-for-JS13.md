---
title: ArcGIS API For Javascript之GraphicsLayer
date: 2019-08-06 15:10:35
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
在ArcGIS API中有一种图层叫做GraphicsLayer，GraphicsLayer是一种客户端图层，GraphicsLayer中的图形我们称作Graphic，它对应着 esri/graphic，一个Graphic对象由四部分组成：geometry,symbol,attribute,infoTemplate组成，Graphic对象在我们开发中用到的非常多。例如：利用Graphic实现图形的高亮显示等等。
### 需求
#### 根据具体坐标生成Graphic
在我们的实际开发当中可能会存在这样的需求：根据坐标以及形状生成相应的图形然后在地图显示出来
在上面说过一个Graphic由四部分组成，在需求中我们已经知道坐标形状，即已经知道了geometry,因此我们可以直接根据坐标生成相应的geometry，从而创建Graphic，然后添加到地图中来。
##### 代码实现
我们首先创建一个html页面，引入相应的文件将地图加载进来
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Graphic</title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <style>
        .MapClass{
            width:100%;
            height:500px;
            border:1px solid #000;
        }
    </style>
    <script>
        require(["esri/map","esri/layers/ArcGISDynamicMapServiceLayer",
            "dojo/domReady!"],function(Map,ArcGISDynamicMapServiceLayer){
            var map = new Map("mapDiv");
            var layer = new ArcGISDynamicMapServiceLayer
            ("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer");
            map.addLayer(layer);
        })
    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div>
</body>
</html>
```
创建四个按钮，将四类不同的Graphic添加到GraphicsLayer中
```
<button>添加点</button>
<button>添加线</button>
<button>添加面</button>
<button>添加圆</button>
```
引入相应的模块，其中包括：
dojo/on（用于绑定事件的模块）
esri/symbols/SimpleMarkerSymbol（点符号类）
esri/symbols/SimpleLineSymbol（线符号类）
esri/symbols/SimpleFillSymbol（面符号类）
esri/geometry/Point（点类）
esri/geometry/Polyline（折线类）
esri/geometry/Polygon（面类）
esri/geometry/Circle（圆类）
dojo/query（dojo的选择器，用于选择DOM元素）
esri/graphic（图形类）
esri/layers/GraphicsLayer（客户端图层类）
为相应的按钮添加绑定事件
```
            //创建客户端图层
            var graphicsLayer=new GraphicsLayer();
            //将客户端图层添加到地图中
            map.addLayer(graphicsLayer);
            //通过query查询到button对象
            var btns=query("button");

            on(btns,"click",function(event){
                //获得按钮的文本
                var text=this.innerHTML;
                //定义线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //定义点符号l
                var pSymbol=new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CIRCLE,10, lineSymbol, new Color([255, 0, 0]));
                //定义面符号
                var fill=SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,  new Color([255, 0, 0]));
                //声明一个类型和图形
                var geometry;
                var graphic;
                //根据文本定义相应的geometry
                switch(text)
                {
                    case "添加点":
                        geometry=new Point({
                            "x":510706,
                            "y":3986100,
                            "spatialReference":map.spatialReference
                        });
                        graphic=new Graphic(geometry,pSymbol);
                        break;
                    case "添加线":
                        //点的坐标对
                         var  paths=[];
                         paths[0]=[
                            [510326,3985702],
                            [510994, 3985676],
                            [511078, 3985903],
                            [510433, 3985928]
                        ];
                        geometry=new Polyline({
                            "paths":paths,
                            "spatialReference":map.spatialReference
                        })
                        graphic=new Graphic(geometry,lineSymbol);
                        break;
                    case "添加面":
                        //点的坐标对
                        var ring=[];
                        ring[0]=[
                            [510275, 3986100],
                            [510508, 3986162],
                            [510596, 3986032],
                            [510271, 3985847],
                            [510275, 3986100]
                        ];
                        geometry=new Polygon({
                            "rings":ring,
                            "spatialReference":map.spatialReference
                        });
                        graphic=new Graphic(geometry,fill);
                        break;
                    case "添加圆":
                        //圆心
                        var p=new Point({
                            "x":510706,
                            "y":3986100,
                            "spatialReference":map.spatialReference
                        });
                        //半径
                        var r=20;
                        geometry=new Circle(p,{
                            "radius":r
                        });
                        graphic=new Graphic(geometry,fill);
                        break;
                }
                //将图形添加到图层中
                graphicsLayer.add(graphic);
```
##### 全部代码
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Graphic</title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <style>
        .MapClass{
            width:100%;
            height:500px;
            border:1px solid #000;
        }
    </style>
    <script>
        require(["esri/map","esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/layers/GraphicsLayer",
            "dojo/on","dojo/query","dojo/colors",
            "esri/graphic","esri/symbols/SimpleMarkerSymbol",
            "esri/symbols/SimpleLineSymbol",
            "esri/symbols/SimpleFillSymbol",
            "esri/geometry/Point",
            "esri/geometry/Polyline","esri/geometry/Polygon",
            "esri/geometry/Circle",
            "dojo/domReady!"],
                function(Map,ArcGISDynamicMapServiceLayer,
                         GraphicsLayer,on,query,Color,Graphic,
                         SimpleMarkerSymbol,SimpleLineSymbol,
                         SimpleFillSymbol,Point,Polyline,Polygon,Circle){
            var map = new Map("mapDiv");
            var layer = new ArcGISDynamicMapServiceLayer
            ("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer");
            map.addLayer(layer);
            //创建客户端图层
            var graphicsLayer=new GraphicsLayer();
            //将客户端图层添加到地图中
            map.addLayer(graphicsLayer);
            //通过query查询到button对象
            var btns=query("button");

            on(btns,"click",function(event){
                //获得按钮的文本
                var text=this.innerHTML;
                //定义线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //定义点符号l
                var pSymbol=new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CIRCLE,10, lineSymbol, new Color([255, 0, 0]));
                //定义面符号
                var fill=SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,  new Color([255, 0, 0]));
                //声明一个类型和图形
                var geometry;
                var graphic;
                //根据文本定义相应的geometry
                switch(text)
                {
                    case "添加点":
                        geometry=new Point({
                            "x":510706,
                            "y":3986100,
                            "spatialReference":map.spatialReference
                        });
                        graphic=new Graphic(geometry,pSymbol);
                        break;
                    case "添加线":
                        //点的坐标对
                         var  paths=[];
                         paths[0]=[
                            [510326,3985702],
                            [510994, 3985676],
                            [511078, 3985903],
                            [510433, 3985928]
                        ];
                        geometry=new Polyline({
                            "paths":paths,
                            "spatialReference":map.spatialReference
                        })
                        graphic=new Graphic(geometry,lineSymbol);
                        break;
                    case "添加面":
                        //点的坐标对
                        var ring=[];
                        ring[0]=[
                            [510275, 3986100],
                            [510508, 3986162],
                            [510596, 3986032],
                            [510271, 3985847],
                            [510275, 3986100]
                        ];
                        geometry=new Polygon({
                            "rings":ring,
                            "spatialReference":map.spatialReference
                        });
                        graphic=new Graphic(geometry,fill);
                        break;
                    case "添加圆":
                        //圆心
                        var p=new Point({
                            "x":510706,
                            "y":3986100,
                            "spatialReference":map.spatialReference
                        });
                        //半径
                        var r=20;
                        geometry=new Circle(p,{
                            "radius":r
                        });
                        graphic=new Graphic(geometry,fill);
                        break;
                }
                //将图形添加到图层中
                graphicsLayer.add(graphic);
            })
        })
    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div>
    <button>添加点</button>
    <button>添加线</button>
    <button>添加面</button>
    <button>添加圆</button>
</body>
</html>
```
##### 代码解释
* 我们利用dojo/query查询到所有的button对象，利用dojo/on模块给所有的button对象绑定事件。
* 在此例子中，有一个缺点：便是将点的坐标写死了，因此我们可以将点的坐标存放在一个json文件中，从而读取文件生成相应的点坐标

#### GraphicsLayer的属性查询
在前面我们提到过，Graphic是可以存在属性的，在实际开发当中，有时我们需要将相应的属性查询出来，在此时我提供一个简单的方法，便是根据 GraphicsLayer的点击事件来查询Graphic的属性。
##### 代码实现
首先我们添加一系列带有属性的Graphic（此处我们只用点图形便可以）
```
//创建客户端图层
            var graphicsLayer=new GraphicsLayer();
            //将客户端图层添加到地图中
            map.addLayer(graphicsLayer);
            //添加点图形的函数
            function addGraphic()
            {
                //定义线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //定义点符号l
                var pSymbol=new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CIRCLE,10, lineSymbol, new Color([255, 0, 0]));
                //声明形状
                var geometry;
                //声明和图形
                var graphic;
                //添加第一个点图形
                geometry=new Point({
                    "x":510706,
                    "y":3986100,
                    "spatialReference":map.spatialReference,
                    attributes:{
                        "h":100
                    }
                });
                graphic=new Graphic(geometry,pSymbol);
                graphicsLayer.add(graphic);
                //添加第二个点图形
                geometry=new Point({
                    "x":510326,
                    "y":3985702,
                    "spatialReference":map.spatialReference,
                    attributes:{
                        "h":200
                    }
                });
                graphic=new Graphic(geometry,pSymbol);
                graphicsLayer.add(graphic);
                //添加第三个点图形
                geometry=new Point({
                    "x":510275,
                    "y":3986100,
                    "spatialReference":map.spatialReference,
                    attributes:{
                        "h":300
                    }
                });
                graphic=new Graphic(geometry,pSymbol);
                graphicsLayer.add(graphic);
            }
            //调用添加点图形的函数
            addGraphic();
```
给GraphicsLayer绑定相应的事件
```
    //绑定事件
            on(graphicsLayer,"click",function(event){
                var graphic=event.graphic;
                alert(graphic.attributes["h"]);
            })
```
##### 全部代码
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Graphic</title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <style>
        .MapClass{
            width:100%;
            height:500px;
            border:1px solid #000;
        }
    </style>
    <script>
        require(["esri/map","esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/layers/GraphicsLayer",
            "dojo/on","dojo/query","dojo/colors",
            "esri/graphic","esri/symbols/SimpleMarkerSymbol",
            "esri/symbols/SimpleLineSymbol",
            "esri/geometry/Point",
                    "dojo/domReady!"],
                function(Map,ArcGISDynamicMapServiceLayer,
                         GraphicsLayer,on,query,Color,Graphic,
                         SimpleMarkerSymbol,SimpleLineSymbol,Point){
            var map = new Map("mapDiv");
            var layer = new ArcGISDynamicMapServiceLayer
            ("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer");
            map.addLayer(layer);
            //创建客户端图层
            var graphicsLayer=new GraphicsLayer();
            //将客户端图层添加到地图中
            map.addLayer(graphicsLayer);
            //添加点图形的函数
            function addGraphic()
            {
                //定义线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //定义点符号l
                var pSymbol=new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CIRCLE,10, lineSymbol, new Color([255, 0, 0]));
                //声明形状
                var geometry;
                //声明和图形
                var graphic;
                //添加第一个点图形
                geometry=new Point({
                    "x":510706,
                    "y":3986100,
                    "spatialReference":map.spatialReference,
                    attributes:{
                        "h":100
                    }
                });
                graphic=new Graphic(geometry,pSymbol);
                graphicsLayer.add(graphic);
                //添加第二个点图形
                geometry=new Point({
                    "x":510326,
                    "y":3985702,
                    "spatialReference":map.spatialReference,
                    attributes:{
                        "h":200
                    }
                });
                graphic=new Graphic(geometry,pSymbol);
                graphicsLayer.add(graphic);
                //添加第三个点图形
                geometry=new Point({
                    "x":510275,
                    "y":3986100,
                    "spatialReference":map.spatialReference,
                    attributes:{
                        "h":300
                    }
                });
                graphic=new Graphic(geometry,pSymbol);
                graphicsLayer.add(graphic);
            }
            //调用添加点图形的函数
            addGraphic();
            //绑定事件
            on(graphicsLayer,"click",function(event){
                var graphic=event.graphic;
                alert(graphic.attributes["h"]);
            })


        })
    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div>
</body>
</html>
```
##### 代码解释
* 需要注意的是graphicsLayer的事件机制是针对于graphic图形的,也就是当图形graphic被点击时被触发，此时回调函数也会将那个图形被点击传给我们，从而得到图形的属性
* 有的同学不太喜欢新建一个graphicsLayer,而是使用map.graphics.add()方法添加一个图形graphic，其实原理是一样的，因为map本身有一个默认的graphicsLayer即：map.graphics返回的便是一个graphicsLayer对象
* 在此代码当中我们只是将获得到的属性信息alert了一下，在真实需求当中我们可能会利用相应的属性信息做很多的事情。

#### 实现用户交互画图（Draw工具）
在部分项目中，有这么一个需求：让用户在地图上自己绘制一个图形，然后将图形添加到地图当中，要实现这个功能我们需要借助esri提供给我们的工具条实现：esri/toolbars/draw，利用该工具条我们可以得到用户交互画出来的geometry,从而根据geometry生成相应的graphic，使用该工具的一般步骤为：

1.创建绘图对象
2.激活绘图工具
3.得到图形Geometry
4.创建Graphic
5.添加到地图
##### 代码实现
创建5个button（和第一个例子一样）
创建绘图对象
```
var toolbar = new Draw(map, { showTooltips: true });
```
给每一个button绑定相应的事件（激活绘图工具）
```
query("button").on("click",function(event){
            var value=this.innerHTML;
            switch(value){
                case "绘制点":{
                    //激活绘图工具（画点）
                    toolbar.activate(Draw.POINT, {
                        showTooltips:true
                    })
                    break;

                }
                case "绘制折线":{
                     //激活绘图工具（画折线）
                    toolbar.activate(Draw.POLYLINE, {
                        showTooltips:true
                    })
                    break;
                }
                case "绘制面":{
                    //激活绘图工具（绘制面）
                    toolbar.activate(Draw.POLYGON, {
                        showTooltips:true
                    })
                    break;
                }
                case "徒手线":{
                    //激活绘图工具（徒手线）
                    toolbar.activate(Draw.FREEHAND_POLYLINE, {
                        showTooltips:true
                    })
                    break;
                }
                case "徒手面":{
                    //激活绘图工具（徒手面）
                    toolbar.activate(Draw.FREEHAND_POLYGON, {
                        showTooltips:true
                    })
                    break;
                }

            }
    });
```
给绘图工具绑定绘图完成事件
```
on(toolbar,"draw-complete", function (result) {
                //获得几何形状
                var geometry=result.geometry;
                //获得形状的类型
                var type=geometry.type;
                //声明图形对象
                var graphic;
                //通过几何形状的类型，创建不同的图形
                switch (type) {
                    case "point":
                        graphic= new Graphic(geometry, marker);
                        break;
                    case "polyline":
                        graphic= new Graphic(geometry, lineSymbol);
                        break;
                    case "polygon":
                        graphic= new Graphic(geometry, fill);
                        break;
                }

                map.graphics.add(graphic);
                //关掉绘图工具
                toolbar.deactivate();
});
```
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
            height:500px;
            border:1px solid #000;
        }
    </style>
    <script type="text/javascript">

        require(["esri/map","dojo/dom","dojo/on",
                    "esri/layers/ArcGISDynamicMapServiceLayer",
                    "dojo/query","esri/toolbars/draw",
                    "esri/symbols/SimpleLineSymbol","esri/graphic","esri/symbols/SimpleMarkerSymbol", "esri/symbols/SimpleFillSymbol","dojo/domReady!"],
            function (Map,dom,on, ArcGISDynamicMapServiceLayer,query, Draw, SimpleLineSymbol,Graphic,SimpleMarkerSymbol,SimpleFillSymbol) {
            var map = new esri.Map("MyMapDiv");
            var layer = new ArcGISDynamicMapServiceLayer
            ("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer");
            map.addLayer(layer)
            var toolbar = new Draw(map, { showTooltips: true });

            var lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
            var marker= new SimpleMarkerSymbol(SimpleMarkerSymbol.STYLE_CIRCLE,10, lineSymbol, new dojo.Color([255, 0, 0]));
            var fill= new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,  new dojo.Color([255, 0, 0]));
            on(toolbar,"draw-complete", function (result) {
                var geometry=result.geometry;
                var type=geometry.type;
                var graphic;
                switch (type) {
                    case "point":
                        graphic= new Graphic(geometry, marker);
                        break;
                    case "polyline":
                        graphic= new Graphic(geometry, lineSymbol);
                        break;
                    case "polygon":
                        graphic= new Graphic(geometry, fill);
                        break;
                }

                map.graphics.add(graphic);
                toolbar.deactivate();
            });
           query("button").on("click",function(event){
            var value=this.innerHTML;
            switch(value){
                case "绘制点":{
                    toolbar.activate(Draw.POINT, {
                        showTooltips:true
                    })
                    break;

                }
                case "绘制折线":{
                    toolbar.activate(Draw.POLYLINE, {
                        showTooltips:true
                    })
                    break;
                }
                case "绘制面":{
                    toolbar.activate(Draw.POLYGON, {
                        showTooltips:true
                    })
                    break;
                }
                case "徒手线":{
                    toolbar.activate(Draw.FREEHAND_POLYLINE, {
                        showTooltips:true
                    })
                    break;
                }
                case "徒手面":{
                    toolbar.activate(Draw.FREEHAND_POLYGON, {
                        showTooltips:true
                    })
                    break;
                }

            }
           });
        });


    </script>
</head>
<body>
    <div id="MyMapDiv" class="MapClass"></div>
    <button>绘制点</button>
    <button>绘制折线</button>
    <button>绘制面</button>
    <button>徒手线</button>
    <button>徒手面</button>
</body>
</html>
```
##### 代码解释
* 在使用绘图工具时，最主要的不同为：在激活绘图工具时的赋予的参数的不同。
* graphic只是客户端图形，并不改变服务本身，如果想要改变服务本身的数据，需要使用要素服务，用到esri/toolbars/Edit工具。

### 总结
通过上面几个需求得知，在生成graphic时，最主要是有用这个图形的几何信息，几何信息可以通过多方面获得，例如：

* 通过具体的坐标生成geometry（实现）
* 通过draw工具动态获得geometry（实现）
* 我们还可以通过map的点击事件获得点的geometry（可以尝试自己实现）。
* 一旦拥有了图形的几何信息，我们就可以直接创建graphic，从而添加到地图中来
