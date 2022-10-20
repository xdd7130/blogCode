---
title: 理解cookie、session、localStorage、sessionStorage之不同
date: 2019-01-02 10:03:31
categories: 
- Web前端
tags: [cookie]
---

# 理解cookie、session、localStorage、sessionStorage之不同

![](http12.png)
- 1、相同点：数据都保存在浏览器，同源共享。即相同的域下可以修改读取。

- 2、不同点：

     <font color="red">大小数量： </font>IE6或更低版本最多20个cookie，IE7+可以有50个。 cookie大小4KB左右，超过部分会被截掉。sessionStoage，localStoage大小可达5M。

     <font color="red">传输： </font>每次请求cookie都会发送到服务器，然后回传给浏览器，sessionStoage,localStoage不会自动发送到服务器端。

   <font color="red">有效性：</font>cookie可以设置path路径，限制只属于某个路径。在过期时间之前都有效，即使窗口和浏览器关闭。sesesionStorage当前窗口有效，关闭窗口自动失效。localStorage始终有效，即使窗口和浏览器关闭。

   <font color="red">作用域：</font>cookie在同源页面中共享，sessionStorage只能在当前页有效，localStorage在所有同源页面中共享。

    <font color="red">操作方法：</font>Cookie的修改读取方法需自己实现，sessionStorage，localStorage提供了get，set方法。    

注：cookie是不可以或缺的，cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。


# web Storage支持的属性与方法
localStorage和sessionStorage都具有相同的操作方法，例如setItem、getItem和removeItem等

getItem(key):获取指定key所存储的value值
key(index)方法：返回列表中对应索引的key值
length属性：返回key/value队列的长度
removeItem(key)方法：从Storage中删除一个对应的键值对。
setItem(key,value)方法：将value存储到key指定的字段。
clear()方法：移除所有的内容
