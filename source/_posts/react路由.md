---
title: 路由
date: 2021-01-14 09:21:19
categories:
- React
tags: 
    - React
---

[demo代码](https://github.com/xdd7130/REACT/tree/main/react-04-router)
### 基本概念
- react-ruter: 核心库
- react-router-dom: 基于核心库在 web端 使用的库
- react-router-native: 基于核心库在 React Native 使用的库
- react-router-config:  用于配置静态路由的库,随着 React Router v4 的引入，不再有集中式路由配置
- react-router-redux: 集成 Redux 的库,已经不推荐使用，推荐使用 connected-react-router 和 Redux 进行配合使用

如果只在浏览器端使用，直接安装 react-router-dom.

### 基本组件
 React Router有三大组件：
- router components 路由器，`<BrowserRouter>`、`<HashRouter>`
- route matching components 路线匹配器，`<Route>`、`<Switch>`
- navigation components 导航，`<Link>`、`<NavLink>`、`<Redirect>`

其他还有 `<MemoryRouter>`  内存路由组件、`<NativeRouter>`  Native的路由组件、`<StaticRouter>` 静态路由组件这些路由组件，其中 MemoryRouter 主要用在 ReactNative 这种非浏览器的环境中，因此直接将 URL 的 history 保存在了内容中。StaticRouter 主要用于服务器端渲染
#### 路由器
react-router-dom 提供了BrowserRouter 和 HashRouter 两个路由，这两个路由都会创建一个专门的 history 对象。两者之间的主要区别是它们存储 URL 和与 Web 服务器通信的方式
> BrowserRouter http://localhost:3000/byd
> HashRouter http://localhost:3000/#/byd 两者生成的 URL 区别在于有没有 

```bash
import { BrowserRouter, Route, Link } from "react-router-dom";
```

BrowserRouter 容器，用来存放 Router、Link
BrowserRouter 提供了如下属性:

- basename (string) 当前位置的基准 URL
- forceRefresh (boolean)，在导航的过程中整个页面是否刷新
- getUserConfirmation (func)，当导航需要确认时执行的函数。默认是：window.confirm
- keyLength (number)  location.key 的长度。默认是 6
- children (node) 要渲染的子节点

> HashRouter 不支持 location.key 和 location.state ，所以在浏览器中建议使用 BrowserRouter

#### 路线匹配器
> Route： location 与 Route 的 path 匹配时，渲染 Route 中的 Component
* Route 接受三种渲染方式：`<Route component>`、`<Route render>`、`<Route children>`。
(1)render: function 类型，Route 会渲染这个 function 的返回值，可以在函数中附加一些额外的逻辑，所以你可以在render中添加一些逻辑判断，再返回一个要渲染的 component。
(2)children: function 类型，比 render 多了 match参数，可以根据 match参数来决定匹配的时候渲染什么，不匹配的时候渲染什么
Route 经常用的是 exact、path 以及 component 属性。

* exact 是否进行精确匹配，路由 /a 可以和 /a/、/a 匹配。
* strict 是否进行严格匹配，指明路径只匹配以斜线结尾的路径，路由/a可以和/a匹配，不能和/a/匹配，相比 exact 会更严格些。
* path (string) 标识路由的路径，没有 path 属性的 Route 总是会匹配。
* component 表示路径对应显示的组件。
* location (object) 除了通过 path 传递路由路径，也可以通过传递 location 对象可以匹配。
* sensitive (boolean) 匹配路径时，是否区分大小写。
* Route 组件都接收 location、history、match三个 props：三个 props 比较常用的是 match，通过 match.params 可以取到动态参数的值。

![01.png](01.png)
![02.png](02.png)

> Swtich:就近匹配路由，仅渲染一个路由，路由的默认行为是匹配了就直接渲染，大部分场景下这个逻辑是没有问题的，但考虑下面的场景

#### 导航
Link 声明路由要跳转的地方 
* to（string | object | function） 需要跳转到的路径(pathname) 或地址（location）
(1)为 string 时 就是一个明确的路径地址
(2)为 object 时有如下属性（就是一个location对象）
pathname：URL路径。
search：URl中查询字符串。
hash：URL的hash分段，例如#a-hash。
state：表示location中的状态
(3)为 function 时，就是一个函数接收当前 location 为参数，然后以字符串或对象的形式返回位置形式
* replace (boolean) 为 true 是替换历史记录，false 是新增历史记录

NavLink 功能与 Link 类似不过参数更多，并且可以设置被选中时的样式或者类
* exact (boolean) 是否进行精确匹配
* strict (boolean) 是否进行严格匹配
* to（string | object） 需要跳转到的路径(pathname)或地址（location）
* activeClassName (string) 是选中状态的类名，我们可以为其添加样式
* activeStyle (Object) 元素处于选中状态时，应用于元素的样式
* isActive(function) 添加额外逻辑以确定链接是否处于活动状态

Redirect 重定向组件
* from (string) 需要重定向的路径，可以包括动态参数
* push (boolean) 为 true 时，重定向会将新条目推入历史记录，而不是替换当前条目
* to (string | object) 重定向到的路径
* exact (boolean) 是否要对 from 进行精确匹配
* strict (boolean) 是否要对 from 进行严格匹配
* sensitive (boolean) 匹配 from 时是否区分大小写





### react-router与react-router-dom
#### API
React-router：提供了router的核心api。如Router、Route、Switch等，但没有提供有关dom操作进行路由跳转的ap；

React-router-dom：提供了BrowserRouter、HashRouter、Link等api，可以通过dom操作触发事件控制路由。
#### 功能
React-router：实现了路由的核心功能

React-router-dom：基于React-router，加入了一些在浏览器运行下的一些功能.

例如：Link组件会渲染一个a标签，

　　　BrowserRouter使用 HTML5 提供的 history API可以保证你的 UI 界面和 URL 保持同步，

　　　HashRouter使用 URL 的 hash 部分保证你的 UI 界面和 URL 保持同步
#### 写法

React-router不能通过操作dom控制路由，此时还需引入React-router-dom
```bash
mport {Switch, Route, Router} from 'react-router';
import {HashHistory, Link} from 'react-router-dom';
```
React-router-dom在React-router的基础上扩展了可操作dom的api

```bash
import {Swtich, Route, Router, HashHistory, Link} from 'react-router-dom';
```

#### 路由跳转
React-router：router4.0以上版本用this.props.history.push('/path')实现跳转；

　　　　　　　router3.0以上版本用this.props.router.push('/path')实现跳转

React-router-dom：直接用this.props.history.push('/path')就可以实现跳转

#### 