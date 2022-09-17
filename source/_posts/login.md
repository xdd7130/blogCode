---
title: vuex+node+express+token登录
date: 2019-09-22 16:38:30
categories:
- Vue
tags: [Node.js,vue]
---

### 思路

1、第一次登录的时候，前端调后端的登陆接口，发送用户名和密码

2、后端收到请求，验证用户名和密码，验证成功，就给前端返回一个token

3、前端拿到token，将token存储到localStorage和vuex中，并跳转路由页面

4、前端每次跳转路由，就判断 localStroage 中有无 token ，没有就跳转到登录页面，有则跳转到对应路由页面

5、每次调后端接口，都要在请求头中加token

6、后端判断请求头中有无token，有token，就拿到token并验证token，验证成功就返回数据，验证失败（例如：token过期）就返回401，请求头中没有token也返回401

7、如果前端拿到状态码为401，就清除token信息并跳转到登录页面
### 知识点
1、路由守卫导航
2、localStorage及其他前端存储（见基础前端）
3、防抖和节流（token过期）
4、后端接口调用
5、node.js、mysql、git、express、element-UI、vuex、vue-router、jsonwebtoken
### 知识点解释
#### 路由守卫导航
vue-router 的导航钩子，主要用来作用是拦截导航，让他完成跳转或取消。
有三种方式可以植入路由导航过程中：

* 全局导航守卫
1、全局前置守卫 router.beforeEach(fn)
2、全局的解析守卫(不常用)
3、全局的后置守卫 router.afterEach(fn)
（可以做一些用户友好提示、与全局前置守卫参数相同）

* 路由独享守卫
1、路由独享守卫 beforeEnter(to,from,next)
    （a、写在路由表中的守卫钩子
    b、针对的是和当前路由相关的，那么其他与之不相关的路由，它是无法监听它的变化情况的
    c、可用于用户权限管理
    d、与全局前置守卫参数相同）
```bash
const routes = [
     {
         path: '/',
         redirect: '/home'
     },
     {
         path: '/home',
         component: Home
     },
     {
         path: '/category',
         component: Category,
         beforeEnter ( to,from,next ) {
         alert('您要进入吗')
         next()
         //业务： 用户权限管理
         }
     },
     {
         path: '**',
         redirect: '/error'
     }
  ]
```
* 组件内守卫
1、组件内的前置守卫beforeRouteEnter((to,from,next)=>{})
导航进入组件时，调用因为组件此时没有创建，所以没有this,this是访问不到的，如果非要访问this,必须通过 next(vm=>{})访问。
```bash
    next(vm => { //vm指的就是组件
       const result = JSON.parse(res.data.slice(7,-1)).rp_result.categorys
       vm.$set(vm.category,'categorys',result)
     })
```
2、组件内的后置守卫beforeRouteEnter((to,from,next)=>{})
(a)离开组件时，调用
(b)this是可以访问到
```bash
beforeRouteLeave (to, from, next) {
    //我们现在已经要离开这个组件，那么这个组件身上的数据和方法等内容我们肯定可以获取到，也就是说这里可以使用 this
    if( this.username || this.password){
    if( confirm('您已经填写结束了吗?')){
        next()
    }else{
        next(false)
    }
    }else{
    	next()
    }
```
3、组件内的更新守卫（ 路由传参和路由的接参 ）
（a）在当前路由改变，但是该组件被复用时调用
（b）举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
（c）由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
（d）可以访问组件实例 this
```bash
beforeRouteUpdate ( to,from,next ) { //动态路由用
     console.log('路由改变了')
     next()
 }
 ```
 ##### 总结
 1、3种类型 7个路由监听钩子
 2、业务：
监听整个项目的路由变化情况——全局的前置守卫
监听某个路由的变化情况——路由的独享守卫
监听的路由组件的路由变化情况——组件内的导航守卫
#### 
### 实例实现
待补充。。。
