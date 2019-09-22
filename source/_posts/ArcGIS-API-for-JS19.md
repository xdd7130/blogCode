---
title: ArcGIS API For Javacsript编辑客户端图形
date: 2019-08-06 17:07:56
categories:
- ArcGIS
tags: [ArcGIS API for JS]
---
在ArcGIS API当中，提供了一个工具叫做Edit，我们利用Edit可以交互的编辑客户端图形Graphic，这个功能在我们实际开发过程中非常有用，可以交互编辑已经存在的Graphic以达到我们的目的，Edit工具可以从以下方面编辑客户端图形：

* 移动客户端图形
* 编辑结点（增加结点，删除结点）
* 拉伸客户端图形（是否保持长宽比例）
* 旋转客户端图形

### Edit工具的使用
使用Edit工具很简单，只需要以下几步即可：

* 给地图添加Graphic，为了下面Edit交互操作做准备
* 创建Edit对象
* 激活Edit工具，此时需要制定Edit的一些参数
* 操作完Graphic将Edit工具关闭

#### 代码实现
添加地图（省略）
定义Edit对象，和graphic对象
```
            //声明一个客户端图形（为了以后的编辑）
            var graphic
            //定义一个Edit对象
            var edit = new Edit(map);
```
首先创建几个按钮（用户实现相应的功能）
```
    <input type="button"  id="add" value="添加客户端图形">
    <input type="button" class="btn" value="移动客户端图形">
    <input type="button" class="btn" value="拉伸客户端图形">
    <input type="button" class="btn" value="旋转客户端图形">
    <input type="button" class="btn" value="编辑结点">
```
给添加客户端图形按钮添加绑定事件
```
on(dom.byId("add"),"click",function(){
                //定义线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //定义面符号
                var fill=SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,  new Color([255, 0, 0]));
                //点的坐标对
                var ring=[];
                ring[0]=[
                    [510275, 3986100],
                    [510508, 3986162],
                    [510596, 3986032],
                    [510271, 3985847],
                    [510275, 3986100]
                ];
                var geometry=new Polygon({
                    "rings":ring,
                    "spatialReference":map.spatialReference
                });
                graphic=new Graphic(geometry,fill);
                map.graphics.add(graphic);
            })
```
给剩余四个按钮添加绑定事件
```
//给剩余四个按钮绑定事件
            query(".btn").on("click",function(event){
                var value=event.currentTarget.defaultValue;
                switch(value){
                    case "移动客户端图形":
                        //激活Edit工具（移动）
                        edit.activate(Edit.MOVE,graphic);
                        break;
                    case "拉伸客户端图形":
                        //构建Edit参数 注：拉伸的时候，可以指定是否保持长宽比例
                        var options = {
                            uniformScaling:true
                        };
                        //激活Edit工具（拉伸客户端图形）
                        edit.activate(Edit.SCALE,graphic,options)
                        break;
                    case "旋转客户端图形":
                        //激活Edit工具（旋转客户端图形）
                        edit.activate(Edit.ROTATE,graphic)
                        break;
                    case "编辑结点":
                        //构建Edit参数 注：编辑折点的时候，要指定是否能添加或者删除结点。
                        var options = {
                            allowAddVertices:true,//能添加结点
                            allowDeleteVertices: true,//能删除结点
                        };
                        edit.activate(Edit.EDIT_VERTICES, graphic, options);
                        break;
                }
            })
```
关闭Edit工具
```
//用完Edit工具，将工具关闭，点击地图（没有点击到Graphic时结束）
            map.on("click", function () {
                edit.deactivate();
            })
```
#### 结果
![1.png](1.png)
![2.png](2.png)
![3.png](3.png)
![4.png](4.png)
![5.png](5.png)
![6.png](6.png)
#### 完整代码
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
        require(["esri/map","dojo/dom","dojo/on","esri/toolbars/edit","esri/geometry/Polygon",
            "esri/layers/ArcGISDynamicMapServiceLayer", "esri/graphic",
            "esri/symbols/SimpleLineSymbol", "esri/symbols/SimpleFillSymbol","dojo/colors","dojo/query",
             "dojo/domReady!"
        ], function (
                Map,dom,on, Edit,Polygon, ArcGISDynamicMapServiceLayer, Graphic,
                SimpleLineSymbol, SimpleFillSymbol,Color,query
        ) {
            var map = new Map("mapDiv");
            var url = "http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer";
            var layer = new ArcGISDynamicMapServiceLayer(url);
            map.addLayer(layer);
            //定义一个Edit对象
            var edit = new Edit(map);
            //定义一个graphic，为了后来的交互编辑
            var graphic;
            //给`添加客户端图形`按钮添加绑定事件
            on(dom.byId("add"),"click",function(){
                //定义线符号
                var lineSymbol=new SimpleLineSymbol(SimpleLineSymbol.STYLE_DASH, new Color([255, 0, 0]), 3);
                //定义面符号
                var fill=SimpleFillSymbol(SimpleFillSymbol.STYLE_SOLID, lineSymbol,  new Color([255, 0, 0]));
                //点的坐标对
                var ring=[];
                ring[0]=[
                    [510275, 3986100],
                    [510508, 3986162],
                    [510596, 3986032],
                    [510271, 3985847],
                    [510275, 3986100]
                ];
                var geometry=new Polygon({
                    "rings":ring,
                    "spatialReference":map.spatialReference
                });
                graphic=new Graphic(geometry,fill);
                map.graphics.add(graphic);
            })
            //给按钮绑定事件
            query(".btn").on("click",function(event){
                var value=event.currentTarget.defaultValue;
                switch(value){
                    case "移动客户端图形":
                        //激活Edit工具（移动）
                        edit.activate(Edit.MOVE,graphic);
                        break;
                    case "拉伸客户端图形":
                        //构建Edit参数 注：拉伸的时候，可以指定是否保持长宽比例
                        var options = {
                            uniformScaling:true
                        };
                        //激活Edit工具（拉伸客户端图形）
                        edit.activate(Edit.SCALE,graphic,options)
                        break;
                    case "旋转客户端图形":
                        //激活Edit工具（旋转客户端图形）
                        edit.activate(Edit.ROTATE,graphic)
                        break;
                    case "编辑结点":
                        //构建Edit参数 注：编辑折点的时候，要指定是否能添加或者删除结点。
                        var options = {
                            allowAddVertices:true,//能添加结点
                            allowDeleteVertices: true,//能删除结点
                        };
                        edit.activate(Edit.EDIT_VERTICES, graphic, options);
                        break;
                }
            })
            //用完Edit工具，将工具关闭，点击地图（没有点击到Graphic时结束）
            map.on("click", function () {
                edit.deactivate();
            })

        });
    </script>
</head>
<body>
    <div id="mapDiv" class="MapClass"></div>
    <input type="button"  id="add" value="添加客户端图形">
    <input type="button" class="btn" value="移动客户端图形">
    <input type="button" class="btn" value="拉伸客户端图形">
    <input type="button" class="btn" value="旋转客户端图形">
    <input type="button" class="btn" value="编辑结点">
</body>
</html>
```