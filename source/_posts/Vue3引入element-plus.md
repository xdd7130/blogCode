---
title: Vue3引入element-plus
date: 2022-10-20 20:33:12
categories:
- Vue3
tags: 
    - [Vue, Vue3, element-plus]
---
## 安装 element-plus
```
cnpm install element-plus --save
```
## 配置 main.ts
```
import ElementPlus from 'element-plus';
import 'element-plus/dist/index.css';
import App from './App.vue'
createApp(App)
.use(ElementPlus)
.mount('#app');
```
## 安装 @element-plus/icons-vue 使用 icon
```
cnpm install @element-plus/icons-vue --save
```
## 组件局部按需引用
```
import { Download } from '@element-plus/icons-vue';

<el-icon><download /></el-icon>
```