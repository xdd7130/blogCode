---
title: 面试JS
date: 2019-01-03 21:42:31
categories: "Web前端"
tags: [JS]
---
# 介绍一下JS的数据类型？

>简单类型：String、Number、Boolean、Null、Undefined
>复杂类型：ObjectES6新增类型：Symbol
>扩展：null与undefined有什么区别 

答：null 表示一个对象被定义了，值为“空值”，而undefined 表示不存在这个值。 正因为这个原因，所以使用typeof判断时，null返回的是object，而undefined返回的是undefined。(判断两者时需要使用===严格判断)
Boolean类型在进行判断的时候设置为 0、-0、null、""、false、undefined 或 NaN，则该对象设置为 false。否则设置为 true（即使 value 参数是字符串 "false"）

# 如何通过JS判断一个数组？

> 1.instanceof方法

instanceof 运算符是用来测试一个对象是否在其原型链原型构造函数的属性。

```
var arr = []; 
arr instanceof Array; // true
```
>2.constructor方法

constructor属性返回对创建此对象的数组函数的引用，就是返回对象相对应的构造函数。
```
var arr = []; 
arr.constructor == Array; //true
```
>3.特性判断法

利用判断数组独有的length和splice方法，但是这是不靠谱的，因为对象也能添加方法和属性。那怎么办了，有一个办法，可以利用数组的length属性没法枚举来判断。
```
function isArray(object){
      return  object && typeof object==='object' &&    
      typeof object.length==='number' &&  
      typeof object.splice==='function' &&    
       //判断length属性是否是可枚举的 对于数组 将得到false  
      !(object.propertyIsEnumerable('length'));
}
```
>4.最简单的方法

这种写法，是 jQuery 正在使用的，淘宝的 kissy 也是使用这种方式。
```
Object.prototype.toString.call(value) == '[object Array]'
// 利用这个方法，可以写一个返回数据类型的方法
var isType = function (obj) {
     return Object.prototype.toString.call(obj).slice(8,-1); 
}
```
>5.ES5新增方法isArray()

```
var a = new Array(123);
var b = new Date();
console.log(Array.isArray(a)); //true
console.log(Array.isArray(b)); //false
```
&扩展：
使用instanceof和construcor，被判断的array必须是在当前页面声明的。比如，一个页面（父页面）有一个框架，框架中引用了一个页面（子页面），在子页面中声明了一个array，并将其赋值给父页面的一个变量，这时判断该变量，Array == object.constructor;会返回false。
最简单的方法，在IE6下判断null和undefined，有一些bug，判断undefined和null均为Object，(并不是bug，是在ES3的标准下返回的就为Object)

# 谈一谈let、const与var的区别？

## let命令基本用法
在ES6中，新增了let命令，用于声明变量，用来取代ES5中var命令，消除var声明的变量的不合理，不严谨之处。const用于声明常量。 
让我们来看看let和var的区别
>1.let不存在变量提升

使用let声明的变量，不会像使用var那样存在“变量提升“”的现象。所以使用let声明变量，必须遵循“先声明，后使用”的原则。否则会报错
```
console.log(a); //ReferenceError
let a = 10;
```
如果是var声明的变量，则不会报错。
```            
console.log(b); //undefined
var b = 10;
```
>2.let声明的变量，存在块级作用域

let声明的变量只在所声明的代码块内有效。块级作用域由 { } 包括，if语句和for语句里面的{ }也属于块作用域。
```
{
    var a = 10;
    let b = 20;  
}
    console.log(a); //10
    console.log(b); // ReferenceError: b is not defined
```

```
var a =[];
for( var i=0; i<10; i++ ){
    a[i] = function(){
        console.log(i);
    }
}

    for( var j = 0; j<a.length; j++ ){
        a[j](); //输出10个10。 因为i是var声明的，在全局范围内都用，每次新的i都会覆盖原来的。
    }
```

```
var a =[];
    for( let i=0; i<10; i++ ){
        a[i] = function(){
            console.log(i);
        }
    }

    for( let j = 0; j<a.length; j++ ){
        a[j](); //输出 0，1，2，...，9
    }
```


>3.let不允许在同一作用域内重复声明同一个变量在同一作用域内，如果使用var声明同一个变量，则后面的覆盖前面的

```
var a = 10;
let a = 10;

console.log(a);//Uncaught SyntaxError:
                 Identifier 'a' has already been declared

或

let a = 10;
let a = 15;

console.log(a);//Uncaught SyntaxError:
                 Identifier 'a' has already been declared
```
>4.暂时性死区：在代码块内，使用let声明变量之前，该变量都是不可以使用用
只要在同一作用域内存在let命令，他所声明的变量就“绑定”在这个作用域内，不管外部有没有声明

例如
```
    let a =10;
    function fn1(){
        console.log(a);
        let a = 5;
    }
    fn1();//Uncaught ReferenceError: a is not defined
```

ES6规定，如果在区块中存在let和const声明的变量，则这个区块对这些声明的变量从一开始就形成一个封闭的作用域。不管在外部有没有声明这个变量。必须遵守“先声明，后使用”的原则，否则报错

ES6规定暂时性死区和不存在变量提升，主要是为了减少运行程序的错误，防止出现“先使用（变量），后声明（变量）”的情况，从而导致意料之外的行为。这种错误在ES5中很常见，现在有了这种规定，就可以避免出现此类错误了

<font color="red">总之，暂时性死区的本质就是，只要一进入当前作用域，所使用的变量就已存在，但是不可获取，只有等到声明变量的哪一行代码的出现，在可以获取和使用该变量</font>

## const命令的基本使用
const用来声明常量，一旦声明，其值就不能更改
```
const a = 10;
    a = 20;

 console.log(a);//Uncaught TypeError: Assignment to constant variable.
```
- 1.const声明的常量不能改变，意味着const一旦声明常量，就必须同时初始化。不能先声明，后初始化，这样会报错

- 2.与let一样。const声明的常量也只在块级作用域内有效 
- 3.与let一样，必须先声明，后使用 
- 4.与let一样，在同一作用域，const不能重复声明同一常量

那么如果使用const声明对象呢？
```
const b = {
        name:'zs',
        age:22
    }
b.name = "lzx";

console.log(b.name+"---"+b.age);//输出lzx---22
```
<font color="red">我们可以看到，使用const声明的对象的属性是可以修改。 </font>   
因为Object类型是引用类型。用const声明常量保存的是对象的地址，不可变的是地址，在上面的代码中就是，不能把 b 指向另一个对象。而修改对象的属性，并不会改变对象的地址，因此用const声明对象的属性是可以修改的 

# map与forEach的区别？


1、forEach方法，是最基本的方法，就是遍历与循环，默认有3个传参：分别是遍历的数组内容item、数组索引index、和当前遍历数组Array。另外，除去第一个必须的回调函数参数，还可以接受一个上下文参数(改变回调函数的this指向)；并且forEach不会遍历空元素。
2、map方法，基本用法与forEach一致，但是不同的，它会返回一个新的数组，所以在callback需要有return值，如果没有，会返回undefined。(从字面理解，map就是映射的意思)
3、filter方法，用法和map很相似，从字面理解，就是过滤、筛选的意思。但是函数的callback需要返回布尔值true或false，并且返回值只需要为弱等==即可。
4、some 方法，对数组中每一项运行指定函数，如果该函数对任一项返回true，则返回true。(一旦遇到true，就会中断循环，返回true，类似于||判断)
5、every方法，对数组中的每一项运行给定函数，如果该函数对每一项返回true，则返回true。(一旦遇到false，就会中断循环，返回false，类似于&&判断)
6、indexOf方法，与字符串中的indexOf类似，返回数组索引值，如果没有匹配，则会返回-1，第二个参数为可选，表示从当前位置开始搜索。
7、lastIndexOf方法，与indexOf相似，只是是从数组的末尾开始查找，而第二个参数的默认值是array.length - 1。
8、reduce方法，字面意思应该是‘减少’，但是实际是‘递归’的意思。实际就是应用一个函数针对数组的两个值(从左到右)，以减至一个值。它的callback接收4个参数：之前值(上一次循环返回的值)、当前值、索引值以及数组本身。initialValue参数可选，表示初始值。
9、reduceRight方法，与reduce方法类似，只是从数组的末尾开始实现。

# 谈一谈你理解的函数式编程？

# 谈一谈箭头函数与普通函数的区别？


- 箭头函数使得表达更加简洁。(这个是废话)
- 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
- 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
- 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用Rest参数代替。
- 不可以使用yield命令，因此箭头函数不能用作Generator函数。


# 谈一谈函数中this的指向吧？

## 全局环境
>无论是否在严格模式下，在全局执行环境中（在任何函数体外部）this 都指代全局对象。

```bash
// 在浏览器中, window 对象同时也是全局对象：
console.log(this === window); // true

a = 37;
console.log(window.a); // 37

this.b = "MDN";
console.log(window.b)  // "MDN"
console.log(b)         // "MDN"
```

## 函数（运行内）环境
在函数内部，this的值取决于函数被调用的方式。
### 简单调用

因为下面的代码不在严格模式下，且 this 的值不是由该调用设置的，所以 this 的值默认指向全局对象
```bash
function f1(){
  return this;
}
//在浏览器中：
f1() === window;   //在浏览器中，全局对象是window

//在Node中：
f1() === global;
```
然而，在严格模式下，this将保持他进入执行环境时的值，所以下面的this将会默认为undefined。
```bash
function f2(){
  "use strict"; // 这里是严格模式
  return this;
}

f2() === undefined; // true
```
所以，在严格模式下，如果 this 没有被执行环境（execution context）定义，那它将保持为 undefined。

>在第二个例子中，this的确应该是undefined，因为f2是被直接调用的，而不是作为对象的属性或方法调用的（如 window.f2()）。有一些浏览器最初在支持严格模式时没有正确实现这个功能，于是它们错误地返回了window对象。

如果要想把 this 的值从一个环境传到另一个，就要用 call 或者apply 方法
// 将一个对象作为call和apply的第一个参数，this会被绑定到这个对象。
```bash
var obj = {a: 'Custom'};

// 这个属性是在global对象定义的。
var a = 'Global';

function whatsThis(arg) {
  return this.a;  // this的值取决于函数的调用方式
}

whatsThis();          // 'Global'
whatsThis.call(obj);  // 'Custom'
whatsThis.apply(obj); // 'Custom'
```
当一个函数在其主体中使用 this 关键字时，可以通过使用函数继承自Function.prototype 的 call 或 apply 方法将 this 值绑定到调用中的特定对象。
```bash
function add(c, d) {
  return this.a + this.b + c + d;
}

var o = {a: 1, b: 3};

// 第一个参数是作为‘this’使用的对象
// 后续参数作为参数传递给函数调用
add.call(o, 5, 7); // 1 + 3 + 5 + 7 = 16

// 第一个参数也是作为‘this’使用的对象
// 第二个参数是一个数组，数组里的元素用作函数调用中的参数
add.apply(o, [10, 20]); // 1 + 3 + 10 + 20 = 34
```
使用 call 和 apply 函数的时候要注意，如果传递给 this 的值不是一个对象，JavaScript 会尝试使用内部 ToObject 操作将其转换为对象。因此，如果传递的值是一个原始值比如 7 或 'foo'，那么就会使用相关构造函数将它转换为对象，所以原始值 7 会被转换为对象，像 new Number(7) 这样，而字符串 'foo' 转化成 new String('foo') 这样，例如：
```bash
function bar() {
  console.log(Object.prototype.toString.call(this));
}

//原始值 7 被隐式转换为对象
bar.call(7); // [object Number]
```
### bind方法
ECMAScript 5 引入了 Function.prototype.bind。调用f.bind(someObject)会创建一个与f具有相同函数体和作用域的函数，但是在这个新函数中，this将永久地被绑定到了bind的第一个参数，无论这个函数是如何被调用的。
```bash
function f(){
  return this.a;
}

var g = f.bind({a:"azerty"});
console.log(g()); // azerty

var h = g.bind({a:'yoo'}); // bind只生效一次！
console.log(h()); // azerty

var o = {a:37, f:f, g:g, h:h};
console.log(o.f(), o.g(), o.h()); // 37, azerty, azerty
```
### 箭头函数
  [箭头函数详解1](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
  [箭头函数详解2](https://www.cnblogs.com/freelyflying/p/6978126.html)

在箭头函数中，this与封闭词法环境的this保持一致。在全局代码中，它将被设置为全局对象
```bash
var globalObject = this;
var foo = (() => this);
console.log(foo() === globalObject); // true
```
>注意：如果将this传递给call、bind、或者apply，它将被忽略。不过你仍然可以为调用添加参数，不过第一个参数（thisArg）应该设置为null。

```bash
// 接着上面的代码
// 作为对象的一个方法调用
var obj = {foo: foo};
console.log(obj.foo() === globalObject); // true

// 尝试使用call来设定this
console.log(foo.call(obj) === globalObject); // true

// 尝试使用bind来设定this
foo = foo.bind(obj);
console.log(foo() === globalObject); // true
```
无论如何，foo 的 this 被设置为他被创建时的环境（在上面的例子中，就是全局对象）。这同样适用于在其他函数内创建的箭头函数：这些箭头函数的this被设置为封闭的词法环境的。
```bash
// 创建一个含有bar方法的obj对象，
// bar返回一个函数，
// 这个函数返回this，
// 这个返回的函数是以箭头函数创建的，
// 所以它的this被永久绑定到了它外层函数的this。
// bar的值可以在调用中设置，这反过来又设置了返回函数的值。
var obj = {
  bar: function() {
    var x = (() => this);
    return x;
  }
};

// 作为obj对象的一个方法来调用bar，把它的this绑定到obj。
// 将返回的函数的引用赋值给fn。
var fn = obj.bar();

// 直接调用fn而不设置this，
// 通常(即不使用箭头函数的情况)默认为全局对象
// 若在严格模式则为undefined
console.log(fn() === obj); // true

// 但是注意，如果你只是引用obj的方法，
// 而没有调用它
var fn2 = obj.bar;
// 那么调用箭头函数后，this指向window，因为它从 bar 继承了this。
console.log(fn2()() == window); // true
```
在上面的例子中，一个赋值给了 obj.bar的函数（称为匿名函数 A），返回了另一个箭头函数（称为匿名函数 B）。因此，在 A 调用时，函数B的this被永久设置为obj.bar（函数A）的this。当返回的函数（函数B）被调用时，它this始终是最初设置的。在上面的代码示例中，函数B的this被设置为函数A的this，即obj，所以即使被调用的方式通常将其设置为 undefined 或全局对象（或者如前面示例中的其他全局执行环境中的方法），它的 this 也仍然是 obj .
### 作为对象的方法
当函数作为对象里的方法被调用时，它们的 this 是调用该函数的对象。
下面的例子中，当 o.f()被调用时，函数内的this将绑定到o对象。
```bash
var o = {
  prop: 37,
  f: function() {
    return this.prop;
  }
};

console.log(o.f()); // logs 37
```
请注意，这样的行为，根本不受函数定义方式或位置的影响。在前面的例子中，我们在定义对象o的同时，将函数内联定义为成员f。但是，我们也可以先定义函数，然后再将其附属到o.f。这样做会导致相同的行为：
```bash
var o = {prop: 37};

function independent() {
  return this.prop;
}

o.f = independent;

console.log(o.f()); // logs 37
```
这表明函数是从o的f成员调用的才是重点。

同样，this 的绑定只受最靠近的成员引用的影响。在下面的这个例子中，我们把一个方法g当作对象o.b的函数调用。在这次执行期间，函数中的this将指向o.b。事实证明，这与他是对象 o 的成员没有多大关系，最靠近的引用才是最重要的。
```bash
o.b = {g: independent, prop: 42};
console.log(o.b.g()); // 42
```
### 原型链中的 this
对于在对象原型链上某处定义的方法，同样的概念也适用。如果该方法存在于一个对象的原型链上，那么this指向的是调用这个方法的对象，就像该方法在对象上一样。
```bash
var o = {
  f: function() { 
    return this.a + this.b; 
  }
};
var p = Object.create(o);
p.a = 1;
p.b = 4;

console.log(p.f()); // 5
```
在这个例子中，对象p没有属于它自己的f属性，它的f属性继承自它的原型。虽然在对 f 的查找过程中，最终是在 o 中找到 f 属性的，这并没有关系；查找过程首先从 p.f 的引用开始，所以函数中的 this 指向p。也就是说，因为f是作为p的方法调用的，所以它的this指向了p。这是 JavaScript 的原型继承中的一个有趣的特性。
### getter 与 setter 中的 this
再次，相同的概念也适用于当函数在一个 getter 或者 setter 中被调用。用作 getter 或 setter 的函数都会把 this 绑定到设置或获取属性的对象。
```bash
function sum() {
  return this.a + this.b + this.c;
}

var o = {
  a: 1,
  b: 2,
  c: 3,
  get average() {
    return (this.a + this.b + this.c) / 3;
  }
};

Object.defineProperty(o, 'sum', {
    get: sum, enumerable: true, configurable: true});

console.log(o.average, o.sum); // logs 2, 6
```
### 作为构造函数
当一个函数用作构造函数时（使用new关键字），它的this被绑定到正在构造的新对象
>虽然构造器返回的默认值是this所指的那个对象，但它仍可以手动返回其他的对象（如果返回值不是一个对象，则返回this对象）。

```bash
/*
 * 构造函数这样工作:
 *
 * function MyConstructor(){
 *   // 函数实体写在这里
 *   // 根据需要在this上创建属性，然后赋值给它们，比如：
 *   this.fum = "nom";
 *   // 等等...
 *
 *   // 如果函数具有返回对象的return语句，
 *   // 则该对象将是 new 表达式的结果。 
 *   // 否则，表达式的结果是当前绑定到 this 的对象。
 *   //（即通常看到的常见情况）。
 * }
 */

function C(){
  this.a = 37;
}

var o = new C();
console.log(o.a); // logs 37


function C2(){
  this.a = 37;
  return {a:38};
}

o = new C2();
console.log(o.a); // logs 38
```
### 作为一个DOM事件处理函数节
当函数被用作事件处理函数时，它的this指向触发事件的元素（一些浏览器在使用非addEventListener的函数动态添加监听函数时不遵守这个约定）。
```bash
// 被调用时，将关联的元素变成蓝色
function bluify(e){
  console.log(this === e.currentTarget); // 总是 true

  // 当 currentTarget 和 target 是同一个对象时为 true
  console.log(this === e.target);        
  this.style.backgroundColor = '#A5D9F3';
}

// 获取文档中的所有元素的列表
var elements = document.getElementsByTagName('*');

// 将bluify作为元素的点击监听函数，当元素被点击时，就会变成蓝色
for(var i=0 ; i<elements.length ; i++){
  elements[i].addEventListener('click', bluify, false);
}
```
### 作为一个内联事件处理函数
当代码被内联on-event 处理函数调用时，它的this指向监听器所在的DOM元素：
```bash
<button onclick="alert(this.tagName.toLowerCase());">
  Show this
</button>
```
上面的 alert 会显示button。注意只有外层代码中的this是这样设置的：
```bash
<button onclick="alert((function(){return this})());">
  Show inner this
</button>
```
在这种情况下，没有设置内部函数的this，所以它指向 global/window 对象（即非严格模式下调用的函数未设置this时指向的默认对象）




# 异步编程的实现方式？


回调函数：
优点：简单、容易理解
缺点：不利于维护，代码耦合高


>1.事件监听(采用时间驱动模式，取决于某个事件是否发生)：

优点：容易理解，可以绑定多个事件，每个事件可以指定多个回调函数
缺点：事件驱动型，流程不够清晰


>2.发布/订阅(观察者模式)：

类似于事件监听，但是可以通过‘消息中心’，了解现在有多少发布者，多少订阅者。


>3.Promise对象

优点：可以利用then方法，进行链式写法；可以书写错误时的回调函数；
缺点：编写和理解，相对比较难


>4.Generator函数

优点：函数体内外的数据交换、错误处理机制
缺点：流程管理不方便

>5.async函数

优点：内置执行器、更好的语义、更广的适用性、返回的是Promise、结构清晰。
缺点：错误处理机制

......js基础

类和继承（es5实现方法 + es6实现方法）；

promise；

写一个方法遍历所有文档树所有节点(考察递归)；


jsonp和跨域；

sort排序相关(注意ascll这个坑)

数组和对象的深浅拷贝；

String + Array的一些基本操作；
写一个数组去重的方法；
冒泡和捕获；
事件代理；

call、apply、bind；
变量提升；
高阶函数；
动画；
setTimeout、setInterval和requestAnimationFrame；
模块化开发；
引起内存泄漏的原因；
ajax；
map、filter、reduce相关；
Map和Set；
移动端开发相关；


......技术拓展

设计模式相关(单例、发布订阅等)；
浏览器渲染过程；
性能优化相关(这个点很大，设计很多方面，不要自以为优化就是网上经常看到的那一套)；
设计一个模版引擎；
设计一个打包工具；
设计一个mvvm框架需要注意的case；
源码阅读相关；

作者：玩弄心里的鬼
链接：https://juejin.im/post/59ec3d50f265da431c6f7339
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


# json的JSON.parse()和JSON.stringfy()
## JSON.parse()
>从字符串解析出对象

```
//定义一个字符串
var data='{"name":"goatling"}'
//解析对象​
​JSON.parse(data)
结果是：
​name:"goatling"
```
## JSON.stringfy()
>从一个对象解析出字符串

```
var data={name:'goatling'}
JSON.stringify(data)
结果是：
'{"name":"goatling"}'
```  