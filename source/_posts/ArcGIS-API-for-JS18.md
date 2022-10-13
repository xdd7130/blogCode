---
title: ArcGIS API For Javascript之动态图层
date: 2019-08-06 16:42:34
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
动态图层主要特点：

* 改变地图服务的现有图层
* 动态图层适用于海量图层的情况
* 不需要将shp文件发布为服务，服务器可以直接读取shp文件在前端显示

### 调用动态图层
在前面已经说过动态图层并不是一种新的服务类型，而是地图服务的一种扩展，因此我们不需要发布服务，只需要对地图服务添加动态图层功能即可。启用地图服务的步骤为：

* 给相应的地图服务启用动态工作空间
* 选择服务器相应的硬盘地址并赋予其id
* 将相应位置注册到ArcGIS Server服务器（保证ArcGIS Server可以访问数据）
* 在Web端进行调用

#### 启用动态图层
我们打开Manager->登录，找到该服务，点击功能，选择允许每次请求修改图层顺序和符号->添加
![01.png](01.png)
硬盘上的数据
![02.png](02.png)
注:D:\myshp文件夹下面有一个shp文件：lunkuoxian.shp
因此我们选择工作空间类型为：shpfile文件夹，工作空间id在此处取名为my，硬盘路径：D：\myshp
![03.png](03.png)
点击确定，重新启动服务
![04.png](04.png)
![05.png](05.png)
注册文件夹到服务器
点击站点->数据存储->注册文件夹（因为我们是文件夹，所以是注册文件夹，如果是数据库，那么就要注册数据库）
![06.png](06.png)
![07.png](07.png)
![08.png](08.png)
在Services Directory 查看动态图层是否启用
![09.png](09.png)
#### Web端调用
#### 代码实现
首先我们加载地图
```
require(["esri/map",
            "dojo/dom",
            "dojo/on",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/layers/FeatureLayer",
            "esri/layers/TableDataSource",
            "esri/layers/LayerDataSource",
            "esri/renderers/SimpleRenderer",
            "esri/symbols/SimpleMarkerSymbol",
            "esri/symbols/SimpleLineSymbol",
            "dojo/domReady!"], function (
                Map,dom,on, ArcGISDynamicMapServiceLayer, FeatureLayer, TableDataSource, LayerDataSource, SimpleRenderer, SimpleMarkerSymbol,SimpleLineSymbol) {
            var map = new Map("mapDiv");
            var layer1 = new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services//Test/MyService/MapServer");
            map.addLayer(layer1);
 });
```
添加一个按钮（用于添加动态图层）
```
<button id="btn">添加动态图层</button>
```
给按钮添加事件
```
on(dom.byId("btn"),"click",function(e){
                //定义一个数据源
                var dataSource = new TableDataSource();
                //此处为我们设置的命名空间
                dataSource.workspaceId = "my";
                //命名空间下面的shp
                dataSource.dataSourceName = "lunkuoxian.shp";
                //定义一个图层数据源
                var layerSource = new LayerDataSource();
                //给图层数据源赋值
                layerSource.dataSource = dataSource;
                //定义一个要素图层:注意链接为动态图层的地址
                var layer2 = new FeatureLayer("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer/dynamicLayer", {
                    mode: FeatureLayer.MODE_ONDEMAND,
                    outFields: ["*"],
                    source: layerSource
                });
                //定义线符号
                var lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                //定义渲染器
                var renderer = new esri.renderer.SimpleRenderer(lineSymbol);
                //定义渲染器
                layer2.setRenderer(renderer);
                layer2.refresh();
                map.addLayer(layer2);
    })
```
##### 完整代码
```
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <script type="text/javascript">
        require(["esri/map",
            "dojo/dom",
            "dojo/on",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/layers/FeatureLayer",
            "esri/layers/TableDataSource",
            "esri/layers/LayerDataSource",
            "esri/renderers/SimpleRenderer",
            "esri/symbols/SimpleLineSymbol",
            "dojo/domReady!"], function (
                Map,dom,on, ArcGISDynamicMapServiceLayer, FeatureLayer, TableDataSource, LayerDataSource, SimpleRenderer,SimpleLineSymbol) {
            var map = new Map("mapDiv");
            var layer1 = new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services//Test/MyService/MapServer");
            map.addLayer(layer1);

            on(dom.byId("btn"),"click",function(e){
                //定义一个数据源
                var dataSource = new TableDataSource();
                //此处为我们设置的命名空间
                dataSource.workspaceId = "my";
                //命名空间下面的shp
                dataSource.dataSourceName = "lunkuoxian.shp";
                //定义一个图层数据源
                var layerSource = new LayerDataSource();
                //给图层数据源赋值
                layerSource.dataSource = dataSource;
                //定义一个要素图层:注意链接为动态图层的地址
                var layer2 = new FeatureLayer("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer/dynamicLayer", {
                    mode: FeatureLayer.MODE_ONDEMAND,
                    outFields: ["*"],
                    source: layerSource
                });
                //定义线符号
                var lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                //定义渲染器
                var renderer = new esri.renderer.SimpleRenderer(lineSymbol);
                //定义渲染器
                layer2.setRenderer(renderer);
                layer2.refresh();
                map.addLayer(layer2);
            })
            
        });
    </script>
</head>

<body>
<div id="mapDiv" style="height:600px;"></div>
<button id="btn">添加动态图层</button>
</body>
</html>
```
运行结果之后：
![10.png](10.png)
### 动态图层的属性查询
在真实需求中,我们不仅仅是将shp在地图中显示那么简单，我们往往要查询该图层的属性信息,我们在前面代码的基础上添加上属性查询。
#### 方法1（通过click直接获取）
##### 代码实现
给要素图层添加点击事件
```
layer2.on("click", function(evt) {
                    //得到点击的graphics
                    var graphic=evt.graphic;
                    //得到该属性信息
                    var attributes=graphic.attributes;
                    //得到该属性信息转换成字符串
                    var result=jsonUtil.stringify(attributes)
                    //将结果弹出一下
                    alert(result);
                    //获得要素图层的主键名称
                    
})
```
##### 全部代码
```
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <script type="text/javascript">
        require(["esri/map",
            "dojo/dom",
            "dojo/on",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/layers/FeatureLayer",
            "esri/layers/TableDataSource",
            "esri/layers/LayerDataSource",
            "esri/renderers/SimpleRenderer",
            "esri/symbols/SimpleLineSymbol","dojo/json",
            "dojo/domReady!"], function (
                Map,dom,on, ArcGISDynamicMapServiceLayer, FeatureLayer, TableDataSource, LayerDataSource, SimpleRenderer,SimpleLineSymbol,jsonUtil) {
            var map = new Map("mapDiv");
            var layer1 = new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services//Test/MyService/MapServer");
            map.addLayer(layer1);

            on(dom.byId("btn"),"click",function(e){
                //定义一个数据源
                var dataSource = new TableDataSource();
                //此处为我们设置的命名空间
                dataSource.workspaceId = "my";
                //命名空间下面的shp
                dataSource.dataSourceName = "lunkuoxian.shp";
                //定义一个图层数据源
                var layerSource = new LayerDataSource();
                //给图层数据源赋值
                layerSource.dataSource = dataSource;
                //定义一个要素图层:注意链接为动态图层的地址
                var layer2 = new FeatureLayer("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer/dynamicLayer", {
                    mode: FeatureLayer.MODE_ONDEMAND,
                    outFields: ["*"],
                    source: layerSource
                });
                //定义线符号
                var lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                //定义渲染器
                var renderer = new esri.renderer.SimpleRenderer(lineSymbol);
                //定义渲染器
                layer2.setRenderer(renderer);
                layer2.refresh();
                map.addLayer(layer2);
                layer2.on("click", function(evt) {
                    //得到点击的graphics
                    var graphic=evt.graphic;
                    //得到该属性信息
                    var attributes=graphic.attributes;
                    //得到该属性信息转换成字符串
                    var result=jsonUtil.stringify(attributes)
                    //将结果弹出一下
                    alert(result);
                    //获得要素图层的主键名称

                })
            })
            
        });
    </script>
</head>

<body>
<div id="mapDiv" style="height:600px;"></div>
<button id="btn">添加动态图层</button>
</body>
</html>
```
运行结果:
![11.png](11.png)
#### 通过查询获得属性
##### 代码实现
定义一个按钮（用户获得属性）
```
<button id="attr">用户弹出属性</button>
```
给相应的按钮绑定事件
```
on(dom.byId("attr"),"click",function(){
                    //得到要素图层的主键属性
                    var idProperty = layer2.objectIdField;
                    //定义查询参数
                    var query = new Query();
                    //是否返回几何形状
                    query.returnGeometry = false;
                    //图层的主键名称（根据自己要求修改）
                    query.objectIds = [247];
                    //查询条件1=1意思是：只根据主键查询，忽略where子句
                    query.where = "1=1";
                    //进行查询
                    layer2.selectFeatures(query, FeatureLayer.SELECTION_NEW,function(result){
                        //因为我们根据主键查询，一定只有一个元素
                        var graphic=result[0];
                        //获得属性
                        var attributes=graphic.attributes;
                        //得到该属性信息转换成字符串
                        var result=jsonUtil.stringify(attributes)
                        //将结果弹出一下
                        alert(result);
                    });

                })
```
##### 全部代码
```
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link  rel="stylesheet"  type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script  type="text/Javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <script type="text/javascript">
        require(["esri/map",
            "dojo/dom",
            "dojo/on",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/layers/FeatureLayer",
            "esri/layers/TableDataSource",
            "esri/layers/LayerDataSource",
            "esri/renderers/SimpleRenderer",
            "esri/symbols/SimpleLineSymbol","dojo/json","esri/tasks/query",
            "dojo/domReady!"], function (
                Map,dom,on, ArcGISDynamicMapServiceLayer, FeatureLayer, TableDataSource, LayerDataSource, SimpleRenderer,SimpleLineSymbol,jsonUtil,Query) {
            var map = new Map("mapDiv");
            var layer1 = new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services//Test/MyService/MapServer");
            map.addLayer(layer1);

            on(dom.byId("btn"),"click",function(e){
                //定义一个数据源
                var dataSource = new TableDataSource();
                //此处为我们设置的命名空间
                dataSource.workspaceId = "my";
                //命名空间下面的shp
                dataSource.dataSourceName = "lunkuoxian.shp";
                //定义一个图层数据源
                var layerSource = new LayerDataSource();
                //给图层数据源赋值
                layerSource.dataSource = dataSource;
                //定义一个要素图层:注意链接为动态图层的地址
                var layer2 = new FeatureLayer("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer/dynamicLayer", {
                    mode: FeatureLayer.MODE_ONDEMAND,
                    outFields: ["*"],
                    source: layerSource
                });
                //定义线符号
                var lineSymbol = new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new dojo.Color([255, 0, 0]), 3);
                //定义渲染器
                var renderer = new esri.renderer.SimpleRenderer(lineSymbol);
                //定义渲染器
                layer2.setRenderer(renderer);
                layer2.refresh();
                map.addLayer(layer2);
                on(dom.byId("attr"),"click",function(){
                    //得到要素图层的主键属性
                    var idProperty = layer2.objectIdField;
                    //定义查询参数
                    var query = new Query();
                    //是否返回几何形状
                    query.returnGeometry = false;
                    //图层的主键名称（根据自己要求修改）
                    query.objectIds = [247];
                    //查询条件1=1意思是：只根据主键查询，忽略where子句
                    query.where = "1=1";
                    //进行查询
                    layer2.selectFeatures(query, FeatureLayer.SELECTION_NEW,function(result){
                        //因为我们根据主键查询，一定只有一个元素
                        var graphic=result[0];
                        //获得属性
                        var attributes=graphic.attributes;
                        //得到该属性信息转换成字符串
                        var result=jsonUtil.stringify(attributes)
                        //将结果弹出一下
                        alert(result);
                    });

                })

            })
            
        });
    </script>
</head>

<body>
<div id="mapDiv" style="height:600px;"></div>
<button id="btn">添加动态图层</button>
<button id="attr">用户弹出属性</button>
</body>
</html>
```
### 补充
#### 启用文件地理数据库三点不同
文件的目录
![12.png](12.png)
命名空间配置
![13.png](13.png)
注册文件夹配置
![14.png](14.png)
代码调用差别
![15.png](15.png)
注意：忽略了要素集的存在，即调用是lunkuoxian，而不是vector.lunkuoxian，调用的时候后面没有*.shp