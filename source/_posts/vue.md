---
title: vue
date: 2020-10-25 19:18:34
categories:
- Vue
tags: 
    - Vue
---

### 生命周期钩子
Vue实例从创建到销毁的过程，生命周期分为8个过程：创建前，创建后，挂载前，挂载后，更新前，更新后，销毁前，销毁后。
>beforeCreate:初始化vue实例，进行数据观测，发生在实例对象创建之前，一般来实现加载动画等。

>created:组件创建，属性被绑定，但DOM还没生成，一般在这个阶段进行请求、获取数据，赋给属性等。
* 完成数据观测，属性与方法的运算，watch、event事件回调的配置
* 可调用methods中的方法，访问和修改data数据触发响应式渲染dom，可通过computed和watch完成数据计算
* 此时vm.$el 并没有被创建
* 一般在created中进行ajax请求

>created -> beforeMount:
* 判断是否存在el选项，若不存在则停止编译，直到调用vm.$mount(el)才会继续编译
* 若存在el，则判断是否存在template，若存在，则将template编译成render函数；若不存在，则直接编译el所对应的外部html
* 如果存在render函数，则首先对其进行编译
* 此过程中vm.el被实例化为el选项对应DOM元素，所以在beforeMount中，用vm.el获取到的是挂载DOM的HTML
优先级： render > template > outerHTML

>beforeMount:
* 在此阶段可获取到vm.el
* 此阶段vm.el虽已完成DOM初始化，但并未挂载在el选项上

>beforeMount -> mounted:此阶段vm.el完成挂载，vm.$el生成的DOM替换了el选项所对应的DOM

>mounted:编译完成页面显示，有些东西需要在页面显示之后再操作的，应在mounted方法阶段。

>beforeUpdate:实例更新之前。

>updated:实例更新之后。

>beforeDestroy:实例销毁之前。

>destroyed:实例销毁之后。

### 路由导航守卫
 导航守卫就是路由跳转过程中的一些钩子函数，路由跳转的过程分为跳转前中后等等细小的过程，在每一个过程中都有一函数，这个函数能让你操作一些其他的事儿，这就是导航守卫，类似于上面的组件生命周期钩子函数。
 待补充。。。。