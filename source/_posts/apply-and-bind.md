---
title: apply and bind
date: 2019-08-11 10:36:27
categories: 
- 基础前端技术
tags: [js]
---
本篇主要解决一下几个问题：
* 1.apply和call的区别在哪里
* 2.什么情况下用apply,什么情况下用call
* 3.apply的其他巧妙用法（一般在什么情况下可以使用apply）*

<font color="red"> apply:方法能劫持另外一个对象的方法，继承另外一个对象的属性.</font>
>Function.apply(obj,args)方法能接收两个参数
>obj：这个对象将代替Function类里this对象
>args：这个是数组，它将作为参数传给Function（args-->arguments）

call:和apply的意思一样,只不过是参数列表不一样.

>Function.call(obj,[param1[,param2[,…[,paramN]]]])
>obj：这个对象将代替Function类里this对象
>params：这个是一个参数列表

#### apply示例:
```bash
<script type="text/javascript">
    /*定义一个人类*/
    function Person(name,age)
    {
        this.name=name;
        this.age=age;
    }
    /*定义一个学生类*/
    functionStudent(name,age,grade)
    {
        Person.apply(this,arguments);
        this.grade=grade;
    }
    //创建一个学生类
    var student=new Student("qian",21,"一年级");
    //测试
    alert("name:"+student.name+"\n"+"age:"+student.age+"\n"+"grade:"+student.grade);
    //大家可以看到测试结果name:qian  age:21  grade:一年级
    //学生类里面我没有给name和age属性赋值啊,为什么又存在这两个属性的值呢,这个就是apply的神奇之处.
</script>
```
分析: Person.apply(this,arguments);

* this:在创建对象在这个时候代表的是student
* arguments:是一个数组,也就是[“qian”,”21”,”一年级”];
也就是通俗一点讲就是:用student去执行Person这个类里面的内容,在Person这个类里面存在this.name等之类的语句,这样就将属性创建到了student对象里面

#### call示例

在Studen函数里面可以将apply中修改成如下:
```bash
Person.call(this,name,age);
```
#### 什么情况下用apply,什么情况下用call
在给对象参数的情况下,如果参数的形式是数组的时候,比如apply示例里面传递了参数arguments,这个参数是数组类型,并且在调用Person的时候参数的列表是对应一致的(也就是Person和Student的参数列表前两位是一致的) 就可以采用 apply , 如果我的Person的参数列表是这样的(age,name),而Student的参数列表是(name,age,grade),这样就可以用call来实现了,也就是直接指定参数列表对应值的位置(Person.call(this,age,name,grade));
#### apply的一些其他巧妙用法
细心的人可能已经察觉到,在我调用apply方法的时候,第一个参数是对象(this), 第二个参数是一个数组集合, 在调用Person的时候,他需要的不是一个数组,但是为什么他给我一个数组我仍然可以将数组解析为一个一个的参数,这个就是apply的一个巧妙的用处,可以将一个数组默认的转换为一个参数列表([param1,param2,param3] 转换为 param1,param2,param3) 这个如果让我们用程序来实现将数组的每一个项,来装换为参数的列表,可能都得费一会功夫,借助apply的这点特性,所以就有了以下高效率的方法:

a) Math.max 可以实现得到数组中最大的一项

因为Math.max参数里面不支持Math.max([param1,param2])也就是数组但是它支持Math.max(param1,param2,param3…),所以可以根据刚才apply的那个特点来解决 var max=Math.max.apply(null,array),这样轻易的可以得到一个数组中最大的一项(apply会将一个数组装换为一个参数接一个参数的传递给方法)
这块在调用的时候第一个参数给了一个null,这个是因为没有对象去调用这个方法,我只需要用这个方法帮我运算,得到返回的结果就行,.所以直接传递了一个null过去

b) Math.min  可以实现得到数组中最小的一项同样和 max是一个思想 var min=Math.min.apply(null,array);

c) Array.prototype.push 可以实现两个数组合并

同样push方法没有提供push一个数组,但是它提供了push(param1,param,…paramN) 所以同样也可以通过apply来装换一下这个数组,即:
```bash
vararr1=new Array("1","2","3");
vararr2=new Array("4","5","6");
Array.prototype.push.apply(arr1,arr2);
```

