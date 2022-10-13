---
title: ArcGIS API For Javascript之调用动态地图服务
date: 2019-08-06 13:40:02
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
主要记录一下在ArcGIS API如何调用自己发布的动态地图服务,利用动态地图服务我们可以完成哪一些需求等等。
### 调用动态地图服务
  在ArcGIS API 中给我们提供了一个类叫做ArcGISDynamicMapServiceLayer利用这个类，我们可以获得发布的地图服务。调用动态地图服务一般只需要两步：

通过地图服务的URL创建一个ArcGISDynamicMapServiceLayer对象将动态地图服务的对象添加到地图容器中
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
        require(["esri/map","esri/layers/ArcGISDynamicMapServiceLayer",
                    "dojo/domReady!"],
                function(Map,ArcGISDynamicMapServiceLayer){
                    var map = new Map("mapDiv");
                    //利用url创建一个动态地图服务对象
                    var layer=new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/Test/MyServer/MapServer");
                    //将地图服务对象添加到地图容器中
                    map.addLayer(layer);
                })
    </script>
</head>
<body class="tundra">
<div id="mapDiv" style="width:900px; height:600px; border:1px solid #000;"></div>
</body>
</html>
```
在ArcGIS API中与ArcMap不同的是：一个服务图层相当于一组图层的集合（即多个图层）
### 需求
在真实开发过程中，动态地图服务可以帮助我们完成很多的功能需求，例如：

* 根据需求隐藏服务中的某一个图层（动态地图服务可以实现，但是切片地图服务就不能实现）
* 通过属性查询地图服务中的信息
* 通过空间查询地图服务中的信息（包括点查询，线查询，面查询等等）

#### 根据需求隐藏服务中的某一个图层
我们发布的地图服务中有四个图层
![01.png](01.png)
在本需求中,我们主要是给页面添加一个按钮，然后将地图服务中的road2隐藏,代码如下
```
 require(["esri/map","dojo/dom","dojo/on","esri/layers/ArcGISDynamicMapServiceLayer",
                    "dojo/domReady!"],
                function(Map,dom,on,ArcGISDynamicMapServiceLayer){
                    var map = new Map("mapDiv");
                    var layer=new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/Test/MyServer/MapServer");
                    map.addLayer(layer);
                    //给id为btn的按钮绑定click事件
                    on(dom.byId("btn"),"click",function()
                    {
                        layer.setVisibleLayers([1,2,3]);
                    })
                })
```
代码解释：
* 此代码功能的实现非常简单，只利用了setVisibleLayers方法，告诉服务，我要显示图层编号为1，2，3的图层。
* 在require中加载了一个新的模块dojo/dom模块，此模块给我们提供了一个方法dom.byId(id),我们可以通过id获得dom对象，类似于document.getElementById()方法。
* 在require中加载了一个新的模块dojo/on模块，在dojo中on是用来绑定事件的,on(target,type,listener)的第一个参数是给哪一个对象绑定事件，第二个参数是事件的类型，第三个是参数是回调函数。
#### 通过属性查询地图服务中的信息
根据属性查询出我们想要的要素图形，然后将该图形高亮（在本事例中查询教学楼的信息，例如根据教学楼的名称将该楼层成高亮）
![02.png](02.png)
在实现这个功能的时候我们分几步开始考虑：

* 在网页上新建一个文本框
* 将文本框中的教学楼名称获取，并创建属性查询对象
* 将查询到的楼层信息（几何信息）获取，利用graphics高亮显示

##### 创建一个文本框
```
Name:<input class="nm" type="text">
<input type="button" value="查询">
```
##### 将文本框中的教学楼名称获取，并创建属性查询对象
```
query("#btn").on("click",function(){
                        //获得教学楼的名称
                        var name=query(".nm")[0].value;
                        //实例化查询参数
                        var findParams = new esri.tasks.FindParameters();
                        findParams.returnGeometry = true;
                        findParams.layerIds = [3];
                        findParams.searchFields = ["name"];
                        findParams.searchText = name;
                        //实例化查询对象
                        var FindTask = new esri.tasks.FindTask("http://localhost:6080/arcgis/rest/services/Test/MyServer/MapServer");
                        //进行查询
                        FindTask.execute(findParams,ShowFindResult)
                    })
```
##### 将查询到的楼层信息（几何信息）获取，利用graphics高亮显示
```
function showFindResult(queryResult)
                    {
                        if (queryResult.length == 0) {
                            alert("没有该元素");
                            return;
                        }
                        for (var i = 0; i < queryResult.length; i++) {
                                //获得该图形的形状
                                var graphic = queryResult[i].feature;
                                var geometry = graphic.geometry;
                                //定义高亮图形的符号
                                //1.定义面的边界线符号
                                var outline= new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASHDOT,new Color([255, 0, 0]), 1);
                                //2.定义面符号
                                var PolygonSymbol = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, outline,new Color([0, 255, 0, 1]));
                                //创建客户端图形
                                var graphic = new Graphic(geometry, PolygonSymbol);
                                //将客户端图形添加到map中
                                map.graphics.add(graphic);
                        }
                    }       
```
![03.png](03.png)

##### 完整代码

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


        require(["esri/map","dojo/query","dojo/on",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/tasks/FindTask",
            "esri/tasks/FindParameters",
            "esri/symbols/SimpleLineSymbol",
            "esri/symbols/SimpleFillSymbol",
            "esri/Color",
            "esri/graphic",
            "dojo/domReady!"],
                function(Map,query,on,ArcGISDynamicMapServiceLayer,FindTask,FindParameters,SimpleLineSymbol,SimpleFillSymbol,Color,Graphic){
                    var map = new Map("mapDiv");
                    var layer=new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/Test/MyServer/MapServer");
                    map.addLayer(layer);
                    query("#btn").on("click",function(){
                        //获得教学楼的名称
                        var name=query(".nm")[0].value;
                        //实例化查询参数
                        var findParams = new FindParameters();
                        findParams.returnGeometry = true;
                        findParams.layerIds = [3];
                        findParams.searchFields = ["name"];
                        findParams.searchText = name;
                        //实例化查询对象
                        var findTask = new FindTask("http://localhost:6080/arcgis/rest/services/Test/MyServer/MapServer");
                        //进行查询
                        findTask.execute(findParams,showFindResult)
                    });
                    function showFindResult(queryResult)
                    {
                        if (queryResult.length == 0) {
                            alert("没有该元素");
                            return;
                        }
                        for (var i = 0; i < queryResult.length; i++) {
                                //获得该图形的形状
                                var feature= queryResult[i].feature;
                                var geometry = feature.geometry;
                                //定义高亮图形的符号
                                //1.定义面的边界线符号
                                var outline= new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASHDOT,new Color([255, 0, 0]), 1);
                                //2.定义面符号
                                var PolygonSymbol = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, outline,new Color([0, 255, 0, 1]));
                                //创建客户端图形
                                var graphic = new Graphic(geometry, PolygonSymbol);
                                //将客户端图形添加到map中
                                map.graphics.add(graphic);
                        }
                    }



                })
    </script>
</head>
<body class="tundra">
<div id="mapDiv" style="width:900px; height:580px; border:1px solid #000;"></div>
    Name:<input class="nm" type="text">
    <input id="btn" type="button" value="查询">
</body>
</html>
```

##### 代码解释
* FindTask 是Esri提供的一个属性查询的类，他所对应的参数为：FindParameters，FindTask只能用于属性查询，不能用于空间查询
* FindTask类中有一个方法叫做execute,execute的第一个参数是属性查询的参数，第二个参数是一个回调函数（即是一个异步函数），当服务器返回数据时，此函数才会被触发。
* dojo/query是dojo提供的一个DOM选择器，他的功能非常强大，与JQuery中的$符一样强大，可以根据id选择query（"#id"）,也可以根据类名选择query（".classname"），query方法还有很多强大的地方，具体可以参考官方文档
* query("#btn").on()是dojo提供给我们第二种绑定事件的方式。
* graphicesri提供给我们使用的客户端图层，利用graphic，我们可以完成很多操作，graphic具体的使用，将在Draw工具时说明。

#### 通过空间查询地图服务中的信息
相信大家都遇到过这种问题，当我点击地图时，将我点击的图形进行高亮显示，此时就用到了空间查询。为了实现该功能我们可以分为以下几步：

* 因为要点击地图，所以首先我们给地图绑定点击事件
* 获得点击的地图坐标(点坐标)，并创建空间查询参数对象
* 将教学楼与点相交的楼层查询出来，然后利用graphic进行高亮显示

##### 给地图绑定点击事件
```
map.on("click",mapClick);
```
##### 获得点击的地图坐标(点坐标)，并创建空间查询参数对象
```
function mapClick(e){
                        //获得用户点击的地图坐标
                        var point=e.mapPoint;
                        //实例化查询参数
                        query=new Query();
                        query.geometry = point;
                        query.outFields = ["*"];
                        query.outSpatialReference = map.spatialReference;
                        query.spatialRelationship = Query.SPATIAL_REL_INTERSECTS;
                        query.returnGeometry = true;
                        //实例化查询对象
                        var queryTask = new QueryTask("http://localhost:6080/arcgis/rest/services/Test/MyServer/MapServer/3");
                        //进行查询
                        queryTask.execute(query,showFindResult)
                    }
```
##### 将教学楼与点相交的楼层查询出来，然后利用graphic进行高亮显示
```
function showFindResult(queryResult)
                    {
                        if (queryResult.features == 0) {
                            alert("没有该元素");
                            return;
                        }
                        for (var i = 0; i < queryResult.features.length; i++) {
                            //获得该图形的形状
                            var feature = queryResult.features[i];
                            var geometry = feature.geometry;
                            //定义高亮图形的符号
                            //1.定义面的边界线符号
                            var outline= new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASHDOT,new Color([255, 0, 0]), 1);
                            //2.定义面符号
                            var PolygonSymbol = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, outline,new Color([0, 255, 0, 1]));
                            //创建客户端图形
                            var graphic = new Graphic(geometry, PolygonSymbol);
                            //将客户端图形添加到map中
                            map.graphics.add(graphic);
                        }
                    }
```
##### 完整代码
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
        require(["esri/map","dojo/query","dojo/on",
                    "esri/layers/ArcGISDynamicMapServiceLayer",
                    "esri/tasks/QueryTask",
                    "esri/tasks/query",
                    "esri/symbols/SimpleLineSymbol",
                    "esri/symbols/SimpleFillSymbol",
                    "esri/Color",
                    "esri/graphic",
                    "dojo/domReady!"],
                function(Map,query,on,ArcGISDynamicMapServiceLayer,QueryTask,Query,SimpleLineSymbol,SimpleFillSymbol,Color,Graphic){
                    var map = new Map("mapDiv");
                    var layer=new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/Test/MyServer/MapServer");
                    map.addLayer(layer);
                    map.on("click",mapClick);
                    function mapClick(e){
                        //获得用户点击的地图坐标
                        var point=e.mapPoint;
                        //实例化查询参数
                        query=new Query();
                        query.geometry = point;
                        query.outFields = ["*"];
                        query.outSpatialReference = map.spatialReference;
                        query.spatialRelationship = Query.SPATIAL_REL_INTERSECTS;
                        query.returnGeometry = true;
                        //实例化查询对象
                        var queryTask = new QueryTask("http://localhost:6080/arcgis/rest/services/Test/MyServer/MapServer/3");
                        //进行查询
                        queryTask.execute(query,showFindResult)

                    }
                    function showFindResult(queryResult)
                    {
                        if (queryResult.features == 0) {
                            alert("没有该元素");
                            return;
                        }
                        for (var i = 0; i < queryResult.features.length; i++) {
                            //获得该图形的形状
                            var feature = queryResult.features[i];
                            var geometry = feature.geometry;
                            //定义高亮图形的符号
                            //1.定义面的边界线符号
                            var outline= new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASHDOT,new Color([255, 0, 0]), 1);
                            //2.定义面符号
                            var PolygonSymbol = new SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, outline,new Color([0, 255, 0, 1]));
                            //创建客户端图形
                            var graphic = new Graphic(geometry, PolygonSymbol);
                            //将客户端图形添加到map中
                            map.graphics.add(graphic);
                        }
                    }
                })
    </script>
</head>
<body class="tundra">
<div id="mapDiv" style="width:900px; height:580px; border:1px solid #000;"></div>
</body>
</html>
```
##### 代码解释
* QueryTask是esri提供给我们的一个工具类，可用于属性查询，也可用于空间查询，QueryTask只能作用于地图服务的某一个图层，而不能作用于一整个地图服务（IdentifyTask类可作用于一整个地图服务）
* Query类是QueryTask参数类，用于设定空间查询的参数。
* QueryTask类中有一个方法叫做execute,execute的第一个参数是查询的参数，第二个参数是一个回调函数（即是一个异步函数），当服务器返回数据时，此函数才会被触发

##### 补充
绑定事件之后，在某些情况下也解除事件的绑定，下面提供几种方法解除事件绑定
直接通过事件句柄解除（dojo新版本）
```
//绑定事件
var handle=map.on("click",mapClick);
//解除事件
handle.remove();
```

通过老版本的方法解除
```
//绑定事件
var handle=map.on("click",mapClick);
//解除事件
dojo.disconnect(handle)
```



