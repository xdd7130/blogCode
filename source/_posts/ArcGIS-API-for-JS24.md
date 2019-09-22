---
title: ArcGIS API For Javascript之调用地理编码服务
date: 2019-08-06 18:03:13
categories:
- ArcGIS
tags: [ArcGIS API for JS]
---
地理编码（Geocoding）指建立地理位置坐标与给定地址一致性的过程，简单的说就是将一个地址的描述信息映射为地图上该地址所对应的空间位置。
      根据输入的地址找到对应的空间信息。当输入一个地址的时候，可能会查到好多个空间位置，程序会给每一个位置一个打分，匹配的越完全的比分越高。（注意这里说的）
### 调用地理编码服务
#### 查看我们发布的服务
首先看一下发布的服务数据
![01.png](01.png)
我发布的地理编码服务是：name为主键

注意应该输入的参数
![02.png](02.png)
#### 代码实现
添加地图（略）
创建三个html要素
```
    Name:<input class="nm" type="text">
    <input id="btn" type="button" value="定位">
    <div id="divShowResult"></div>
```
给定位按钮添加绑定事件
```
//执行方法
                        locator.addressToLocations(options,function(candidates){
                            //获得运行之后的信息
                            if (candidates.length > 0){
                                    //拼接字符串
                                    var htmls = "<table style='width: 100%'>"; 
                                    htmls = htmls + "<tr bgcolor='#E0E0E0'><td>X 坐标</td><td>Y 坐标</td><td>得分</td></tr>"; 
                                    array.forEach(candidates, function (candidate, index) {  
                                        if (index % 2 == 1) { 
                                            htmls = htmls + "<tr  bgcolor='#E0E0E0'><td style='width: 60px'>" + candidate.location.x + "</td><td style='width: 60px'>" + candidate.location.y+ "</td><td>" + candidate.score + "</td></tr>"; 
                                        } else { 
                                            htmls = htmls + "<tr><td style='width: 60px'>" + candidate.location.x + "</td><td style='width: 60px'>" + candidate.location.y+ "</td><td>" + candidate.score + "</td></tr>";
                                        }                                       
                                    }); 
                                    htmls = htmls + "</table>"; 
                                    //将拼接的字符串显示在页面上
                                    dom.byId("divShowResult").innerHTML = htmls;
                            }
                        },function(error){alert(error)});   
```
#### 运行结果
![03.png](03.png)
#### 全部代码
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="gbk">
    <title>Hello World</title>
    <link rel="stylesheet" type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/dijit/themes/tundra/tundra.css"/>
    <link rel="stylesheet" type="text/css" href="http://localhost/arcgis_js_api/library/3.17/3.17/esri/css/esri.css" />
    <script type="text/javascript" src="http://localhost/arcgis_js_api/library/3.17/3.17/init.js"></script>
    <script>


        require(["esri/map","dojo/query","dojo/on","dojo/_base/array","dojo/dom",
            "esri/layers/ArcGISDynamicMapServiceLayer",
            "esri/tasks/locator",
            "esri/symbols/SimpleMarkerSymbol",
            "dojo/colors",
            "esri/InfoTemplate","esri/graphic",
            "dojo/domReady!"],
                function(Map,query,on,array,dom,ArcGISDynamicMapServiceLayer,Locator,SimpleMarkerSymbol,Color,InfoTemplate,Graphic){
                    var map = new Map("mapDiv");
                    var layer=new ArcGISDynamicMapServiceLayer("http://localhost:6080/arcgis/rest/services/Test/MyService/MapServer");
                    map.addLayer(layer);
                    query("#btn").on("click",function(){
                        //获得教学楼的名称
                        var name=query(".nm")[0].value;
                        //创建地理编码对象
                        var locator = new Locator("http://localhost:6080/arcgis/rest/services/Test/nameLoc/GeocodeServer");
                        //注意这里的参数要和服务的参数对应
                        var address = { "Single Line Input": name}; 
                        //输出的空间参考
                        locator.outSpatialReference = map.spatialReference; 
                        var options = { 
                            address: address, 
                            outFields: ["*"] 
                        } 
                        //执行方法
                        locator.addressToLocations(options,function(candidates){
                            //获得运行之后的信息
                            if (candidates.length > 0){
                                    //拼接字符串
                                    var htmls = "<table style='width: 100%'>"; 
                                    htmls = htmls + "<tr bgcolor='#E0E0E0'><td>X 坐标</td><td>Y 坐标</td><td>得分</td></tr>"; 
                                    array.forEach(candidates, function (candidate, index) {  
                                        if (index % 2 == 1) { 
                                            htmls = htmls + "<tr  bgcolor='#E0E0E0'><td style='width: 60px'>" + candidate.location.x + "</td><td style='width: 60px'>" + candidate.location.y+ "</td><td>" + candidate.score + "</td></tr>"; 
                                        } else { 
                                            htmls = htmls + "<tr><td style='width: 60px'>" + candidate.location.x + "</td><td style='width: 60px'>" + candidate.location.y+ "</td><td>" + candidate.score + "</td></tr>";
                                        }                                       
                                    }); 
                                    htmls = htmls + "</table>"; 
                                    //将拼接的字符串显示在页面上
                                    dom.byId("divShowResult").innerHTML = htmls;
                            }
                        },function(error){alert(error)});                               });                             
                });
                       
                              
    </script>
</head>
<body class="tundra">
<div id="mapDiv" style="width:900px; height:580px; border:1px solid #000;"></div>
    Name:<input class="nm" type="text">
    <input id="btn" type="button" value="定位">
    <div id="divShowResult"></div>
</body>
</html>
```