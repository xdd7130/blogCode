---
title: Cesium+Vue3 搭建第一个项目(一)
date: 2022-10-20 20:22:12
categories:
- Cesium
tags: 
    - [Cesium, Vue, Vite]
---
## 搭建 Vite 项目
```
# yarn
yarn create vite my-vue-app --template vue-ts
# npm 
npm init vite my-vue-app --template vue-ts
```
## 安装 cesium
```
yarn add cesium # 这个是给开发时 vite 的 esbuild 找模块用的
yarn add @types/cesium -D # 这个是给智能提示用的，例如 import { Viewer } from 'cesium'
 yarn add -D vite-plugin-cesium # 安装vite-plugin-cesium插件
```
## 配置 vite.config.ts
```
import cesium from 'vite-plugin-cesium' // 引入插件
 // 在plugin 里增加
plugins: [
    cesium()
],
```
## 完整代码实例
```
<template>
  <div id="mainMap"></div>
</template>
<script lang="ts" setup>
import { onMounted } from 'vue'
import * as Cesium from 'cesium'
onMounted(() => {
  Cesium.Ion.defaultAccessToken =
    'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiIxNTFlNWVmMC1jOWRhLTQyMDktOTY4Ny02YTU5YWFlMGYwY2IiLCJpZCI6OTQzNDQsImlhdCI6MTY1Mjg4ODEyNn0.g2vq1qq_rdfZeRTy73nBkEDzhMIM4upkYcbIdFYnCiQ'

  const viewer = new Cesium.Viewer('mainMap', {
    infoBox: false,
    homeButton: false,
    timeline: false,
    navigationHelpButton: false,
    geocoder: false,
    sceneModePicker: false,
    animation: false,
    fullscreenButton: false
  })
  const buildingTileset = viewer.scene.primitives.add(Cesium.createOsmBuildings())
  viewer.camera.flyTo({
    destination: Cesium.Cartesian3.fromDegrees(116.405419, 39.926666, 3000),
    orientation: {
      heading: Cesium.Math.toRadians(0.0),
      pitch: Cesium.Math.toRadians(-25.0)
    }
  })
  // viewer.cesiumWidget.creditContainer.style.display = 'none'
})
</script>
<style scoped>
#mainMap {
  width: 800px;
  height: 800px;
  background: #aabbcc;
}
</style>
```