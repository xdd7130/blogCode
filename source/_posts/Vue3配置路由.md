---
title: Vue3+Router4.X
date: 2022-10-20 20:22:12
categories:
- Cesium
tags: 
    - [Vue Router4.X,Vue , Vue3]
---
## 安装 Vue Router
```
npm install vue-router@4
```
## 创建router文件
```
// router.ts

import { createRouter, createWebHistory } from "vue-router";
import HelloWorld from "./components/HelloWorld.vue";


const routes = [
  { path: "/", component: HelloWorld },
  { path: "/cesium", component: () => import("./components/Cesium.vue") }
];

const router = createRouter({
  history: createWebHistory(),
  routes
});

export default router;
```
## App.vue配置菜单
```
<router-link to="/cesium">
  ...
</router-link>
```
## 配置 main.ts

```
import Router from './router'
createApp(App)
.use(Router)
.mount('#app');

```