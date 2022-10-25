---
title: Cesium之鹰眼图
date: 2022-10-20 20:22:12
categories:
- Cesium
tags: 
    - [Cesium]
---
## 地图右下角放div容器
```
<div id="eye"></div>
#eye {
    position: absolute;
    width: 20%;
    height:20%;
    bottom: 0;
    right: 0;
    z-index: 999;
    background: red;
    border: solid blue 1px;
}
```
## 创建 两个地球
```
//1.创建双球
var viewer = new Cesium.Viewer('cesiumContainer', {});
var viewer1 = new Cesium.Viewer('eye', {});
```
## 设置鹰眼图中球属性
```
//2.设置鹰眼图中球属性
let control = viewer1.scene.screenSpaceCameraController;
control.enableRotate = false;
control.enableTranslate = false;
control.enableZoom = false;
control.enableTilt = false;
control.enableLook = false;
let syncViewer = function() {
    viewer1.camera.flyTo({
        destination: viewer.camera.position,
        orientation: {
            heading: viewer.camera.heading,
            pitch: viewer.camera.pitch,
            roll: viewer.camera.roll
        },
        duration: 0.0
    });
}
```
## 同步
通过添加了一个空字符串的label，利用它的CallbackProperty实现了每时每刻调用syncViewer方法来同步两个球的相机视角

```
//3. 同步
viewer.entities.add({
    position : Cesium.Cartesian3.fromDegrees(0, 0),
    label : {
        text : new Cesium.CallbackProperty(function(){
            syncViewer();
            return "";
        }, true)
    }
});
```
代码地址：[gitHub](https://github.com/xdd7130/vue3_vite_cesium_ts/blob/master/src/components/CesiumOverview.vue)