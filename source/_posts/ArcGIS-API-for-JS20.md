---
title: ArcGIS API For Javascript之编辑服务器端图层
date: 2019-08-06 17:19:00
categories:
- ArcGIS
tags: [ArcGIS API for JS, webgis]
---
在现实需求中，我们不仅仅需要编辑客户端图层，也有可能去编辑服务器端图层，要想编辑服务器端的图层，必须要将地图发布为：要素服务
### 需求（服务器端图层编辑）
关于服务器端图层的操作,基本就是增删改查操作，要素服务的查询前面已经介绍过，在此篇博客中主要记录一下关于要素服务的增删改操作,在ArcGIS API for JS中给我们提供了三个类用于要素的增Add,删Delete,改Update,接下来就用这三个类来实现我们的功能。

* 我们操作的是要素服务的sushelou图层
* 我们的服务：

![01.png](01.png)
注意：宿舍楼图层编号为1

图层的属性信息
![02.png](02.png)

### 实现
首先在页面添加三个按钮(用于实现用户的增删改操作)
<button class="btn">添加要素</button>
<button class="btn">删除要素</button>
<button class="btn">修改要素</button>
<button class="btn">查询要素</button>
添加地图（省略）
创建通用的对象，例如Draw
```
//用于操作的要素图层，注意我们是操作的宿舍楼图层
var featureLayer = new FeatureLayer("http://localhost:6080/arcgis/rest/services/Test/ft/FeatureServer/1",
                    {
                            mode:FeatureLayer.MODE_SNAPSHOT,
                            outFields: ["*"]
                    });
                    //在添加要素时，利用Draw工具获得geometry对象
                    var d = new Draw(map, { showTooltips: true });
                    //要操作的graphic
                    var g;
```
给相应的按钮添加绑定事件
```
query("button").on("click",function(event){
                        //获得按钮的文本
                        var value=this.innerHTML; 
                        //根据文本绑定不同的事件
                        switch(value){
                            case "添加要素":
                                d.activate(Draw.POLYGON);
                                break;
                            case "删除要素":
                                //创建Graphic对象，删除OBJECTID为34的元素，
                                //因为OBJECTID是主键，所以只需要指定主键即可
                                g=new Graphic("","",{
                                    "OBJECTID":34
                                });
                                //创建删除对象
                                var de=new Delete({
                                    "featureLayer":featureLayer,
                                    "deletedGraphics":[g]
                                });
                                //执行删除结果
                                de.performRedo();
                                //刷新图层
                                layer.refresh();
                                break;
                            case "修改要素":
                                //用于修改要素的函数,查询更新为36的要素
                                updateFeature(36);
                                break;                          
                            case "查询要素":
                                //用于查询要素的函数，查询主键为36的要素
                                searchFeather(36);
                                break;
                        }
                    });
```
如果是添加操作（利用draw添加geometry属性）
```
//当画图完毕时，添加要素
                    on(d, "draw-complete", function (result) {
                        //要素只赋予了geometry，属性信息为空
                        var graphic = new Graphic(result.geometry, null,{});
                        var add=new Add({
                            "featureLayer":featureLayer,//给哪一个要素图层添加要素
                            "addedGraphics":[graphic]//用于添加的要素
                        })
                        //执行添加函数
                        add.performRedo();
                        //刷新视图
                        layer.refresh();
                        //关闭绘图对象
                        d.deactivate();
                    });
```
如果是查询操作
```
//根据id查询要素
            function searchFeather(id,callback){
                        //得到要素图层的主键属性
                        var idProperty = featureLayer.objectIdField;
                        //定义查询参数
                        var query = new Query();
                        //是否返回几何形状
                        query.returnGeometry = false;
                        //图层的主键名称（根据自己要求修改）
                        query.objectIds = [id];
                        //查询条件1=1意思是：只根据主键查询，忽略where子句
                        query.where = "1=1";
                        //进行查询
                        featureLayer.selectFeatures(query, FeatureLayer.SELECTION_NEW,function(result){
                            //如果callback不存在，说明仅仅是查询操作
                            if(!callback){
                                //因为我们根据主键查询，一定只有一个元素
                                var graphic=result[0];
                                //获得属性
                                var attributes=graphic.attributes;
                                //得到该属性信息转换成字符串
                                var result=jsonUtil.stringify(attributes)
                                //将结果弹出一下
                                alert(result);
                            }else//如果callback存在，说明是更新操作
                            {
                                callback(result);
                            }
                            
                        });
                    }
```
如果是更新操作
```
//根据id修改要素
                    function updateFeature(id){
                        //注意:（第二个参数）这里传入一个回调函数，用于处理查询出来的数据
                        searchFeather(id,function(result){
                                //获得旧的要素
                                var oldgraphic=result[0];
                                //新的要素
                                var newgraphic=new Graphic(oldgraphic.toJson());
                                //将alias属性修改为:修改后的C区
                                newgraphic.attributes.alias="修改后的C区";
                                //创建更新对象
                                var update=new Update({
                                    "featureLayer":featureLayer,
                                    "postUpdatedGraphics":[newgraphic],//修改之后的要素
                                    "preUpdatedGraphics":[oldgraphic]//修改之前的要素
                                })
                                //执行刷新操作
                                update.performRedo();
                                //刷新视图
                                layer.refresh();
                                alert("修改成功");
                                
                        })
                        
                    }
```
#### 全部代码
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

        require(["esri/map",
        "dojo/on",
        "dojo/query",
        "esri/layers/ArcGISDynamicMapServiceLayer",
        "esri/toolbars/draw",
        "esri/layers/FeatureLayer",
        "esri/graphic", "esri/dijit/editing/Add",
        "esri/dijit/editing/Delete","esri/dijit/editing/Update","esri/tasks/query","dojo/json",
        "dojo/domReady!"],
                function (Map,on,query, ArcGISDynamicMapServiceLayer, Draw,FeatureLayer,Graphic,Add,Delete,Update,Query,jsonUtil) {

                    
                    var map = new esri.Map("MyMapDiv");
                    var layer = new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/Test/ft/MapServer");
                    map.addLayer(layer);
                    //用于操作的要素图层，注意我们是操作的宿舍楼图层
                    var featureLayer = new FeatureLayer("http://localhost:6080/arcgis/rest/services/Test/ft/FeatureServer/1",
                         {
                            mode:FeatureLayer.MODE_SNAPSHOT,
                            outFields: ["*"]
                         });
                    //在添加要素时，利用Draw工具获得geometry对象
                    var d = new Draw(map, { showTooltips: true });
                    //要操作的graphic
                    var g;
                    query("button").on("click",function(event){
                        var value=this.innerHTML;
                        switch(value){
                            case "添加要素":
                                d.activate(Draw.POLYGON);
                                break;
                            case "删除要素":
                                //创建Graphic对象，删除OBJECTID为34的元素，
                                //因为OBJECTID是主键，所以只需要指定主键即可
                                g=new Graphic("","",{
                                    "OBJECTID":34
                                });
                                //创建删除对象
                                var de=new Delete({
                                    "featureLayer":featureLayer,
                                    "deletedGraphics":[g]
                                });
                                //执行删除结果
                                de.performRedo();
                                //刷新图层
                                layer.refresh();
                                break;
                            case "修改要素":
                                //用于修改要素的函数,查询更新为36的要素
                                updateFeature(1);
                                break;                          
                            case "查询要素":
                                //用于查询要素的函数，查询主键为36的要素
                                searchFeather(1);
                                break;
                        }
                    });
                    //当画图完毕时，添加要素
                    on(d, "draw-complete", function (result) {
                        //要素只赋予了geometry，属性信息为空
                        var graphic = new Graphic(result.geometry, null,{});
                        var add=new Add({
                            "featureLayer":featureLayer,//给哪一个要素图层添加要素
                            "addedGraphics":[graphic]//用于添加的要素
                        })
                        //执行添加函数
                        add.performRedo();
                        //刷新视图
                        layer.refresh();
                        //关闭绘图对象
                        d.deactivate();
                    });
                    //根据id修改要素
                    function updateFeature(id){
                        //注意:（第二个参数）这里传入一个回调函数，用于处理查询出来的数据
                        searchFeather(id,function(result){
                                //获得旧的要素
                                var oldgraphic=result[0];
                                //新的要素
                                var newgraphic=new Graphic(oldgraphic.toJson());
                                //将alias属性修改为:修改后的A区宿舍
                                newgraphic.attributes.alias="修改后的A区宿舍";
                                //创建更新对象
                                var update=new Update({
                                    "featureLayer":featureLayer,
                                    "postUpdatedGraphics":[newgraphic],//修改之后的要素
                                    "preUpdatedGraphics":[oldgraphic]//修改之前的要素
                                })
                                //执行刷新操作
                                update.performRedo();
                                //刷新视图
                                layer.refresh();
                                alert("修改成功");
                                
                        })
                        
                    }
                    //根据id查询要素
                    function searchFeather(id,callback){
                        //得到要素图层的主键属性
                        var idProperty = featureLayer.objectIdField;
                        //定义查询参数
                        var query = new Query();
                        //是否返回几何形状
                        query.returnGeometry = false;
                        //图层的主键名称（根据自己要求修改）
                        query.objectIds = [id];
                        //查询条件1=1意思是：只根据主键查询，忽略where子句
                        query.where = "1=1";
                        //进行查询
                        featureLayer.selectFeatures(query, FeatureLayer.SELECTION_NEW,function(result){
                            //如果callback不存在，说明仅仅是查询操作
                            if(!callback){
                                //因为我们根据主键查询，一定只有一个元素
                                var graphic=result[0];
                                //获得属性
                                var attributes=graphic.attributes;
                                //得到该属性信息转换成字符串
                                var result=jsonUtil.stringify(attributes)
                                //将结果弹出一下
                                alert(result);
                            }else//如果callback存在，说明是更新操作
                            {
                                callback(result);
                            }
                            
                        });
                    }
                });


    </script>
</head>
<body>
<div id="MyMapDiv" class="MapClass"></div>
<button class="btn">添加要素</button>
<button class="btn">删除要素</button>
<button class="btn">修改要素</button>
<button class="btn">查询要素</button>
</body>
</html>
```