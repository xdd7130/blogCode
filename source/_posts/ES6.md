---
title: ES6(一)
date: 2020-10-25 19:18:34
categories:
- 基础前端技术
tags: 
    - ES6
---

### 🌴 let 与 const
#### 👉 总结：
* let :声明的变量只在 let 命令所在的代码块内有效，且只能声明一次，多次声明，报错：“Identifier XX has already been declared”,for循环很适合let。
* const:声明一个只读的常量，一旦声明，常量的值就不能改变。

#### 👉 比较：
1、不存在变量提升：let没有预解析，不存在变量提升，var 会变量提升。

```bash
console.log(a); // undefined  ===>  a已声明还没赋值，默认得到undefined值
var a = 100;

console.log(b); // 报错：b is not defined  ===> 找不到b这个变量
let b = 10;

console.log(c); // 报错：c is not defined  ===> 找不到c这个变量
const c = 10;
```

2、const 声明一个只读变量，声明之后不允许改变。意味着，一旦声明必须初始化，否则会报错。
3、暂时性死区：代码块内如果存在 let 或者 const，代码块会对这些命令声明的变量从块的开始就形成一个封闭作用域。
4、var声明的变量会挂载在window上，而let和const声明的变量不会。

```bash
var a = 100;
console.log(a,window.a);    // 100 100

let b = 10;
console.log(b,window.b);    // 10 undefined

const c = 1;
console.log(c,window.c);    // 1 undefined
```

#### 👉 注意:
对于简单类型（数值 number、字符串 string 、布尔值 boolean）,值就保存在变量指向的那个内存地址，因此 const 声明的简单类型变量等同于常量。
复杂类型（对象 object，数组 array，函数 function），变量指向的内存地址其实是保存了一个指向实际数据的指针，所以 const 只能保证指针是固定的，至于指针指向的数据结构变不变就无法控制了，所以使用 const 声明复杂类型对象时要慎重。



### 🌴 解构赋值

解构赋值是对赋值运算符的扩展。

他是一种针对数组或者对象进行模式匹配，然后对其中的变量进行赋值。

在代码书写上简洁且易读，语义更加清晰明了；也方便了复杂对象中数据字段获取。
在解构中，有下面两部分参与：

解构的源，解构赋值表达式的右边部分。
解构的目标，解构赋值表达式的左边部分。

<font color="green">一一对应的关系</font>

```bash
let [a,b,c] = [1,2,3];
let [a,[b,c]] = [1,[2,3]];

let json = {
    name:'DANDAN',
    age:18,
    job:"打工人"
}

let {name,age,job} = {
    nama:"DANDAN",
    age:18,
    job:"打工人"
}

//别名
let {name:n,age:g,job:b} = json
console.log(n,g,b)
//解构可以给默认值
let [a,b,c="暂无数据"] = [1,2,undefined];
//
let = a;
({a} = {a:"apple",b:"banana"});
console.log(a)
```


### 🌴 字符串模板
代替字符串拼接
```bash
let name = "DANDAN";
let age = 18;

let str = `我是${name},年龄永远${age}岁`;

```

### 🌴 函数
#### 默认参数
函数参数默认已经定义，不能再用let,const声明
```bash
function(a=0,b=0){
    console.log(a,b)
}
```

#### 箭头函数

()=>{
    语句
    return
}
注:
* this问题，定义函数所在的对象，不是运行时所在对象。
* 箭头函数里没有arguments；用“...”。
* 箭头函数不能作构造函数

#### 剩余参数
扩展运算符，reset运算符(...)：
剩余参数必须放到最后

```bash

let arr = [a,b,c]
console.log(...arr);

function show(...a){
    console.log(a)
}
show(1,2,3,4,5)
```

### 🌴 热闹的数组循环
* forEach() 代替for,两个参数
* map()
* filter()
* some()
* every()

1、arr.forEach(function(){},指向)、arr.map(function(){},指向),接受两个参数，第一个：循环回调函数，第二个：this指向。

```bash
arr.forEach(function(val,index,arr){
    console.log(this,val,index,arr);
},windows)
```
2、arr.map(function(){},指向),和forEach()一样，接受两个参数，第一个：循环回调函数，第二个：this指向。
   <font color= red>一般用来做数据交互映射，正常情况下，需要配合return,返回一个新的数组 </font> 若没有return相当于forEach,
   日常用return返回目标数据。
3、arr.filter(),过滤掉“不合格”数据.

```bash
let arr = [
    {type:true},
    {type:false}
]
let newArr = arr.filter(function(item,index,arr){
    return item.type === true;
})
console.log(newArr);
```

4、arr.some(),类似查找，数组里面有一个元素符合条件，返回true.

```bash
let arr = [1,2,3,4,5,6];
let r = arr.some((val,index,arr) =>{
    return val%2 = 1;
})
```

5、arr.every(),数组里面所有元素符合条件，返回true。
6、arr.reduce(),与上面的不同，有四个参数,从左往右。
```bash
let r = arr.reduce((pre,cur,index,arr)=>{
    return pre+cur;  //求和
})
```

7、arr.reduceRight(),有四个参数,从右往左。
8、for...of

```bash
for(let val of arr){
    console.log(val);
}
for(let index of arr.keys()){
    console.log(val);
}
for(let item of arr.entries()){
    console.log(val);
}
for(let [key,val] of arr.entries()){
    console.log(key,val);
}
```

### 🌴 数组新增
1、Array.from():把类数组对象转为数组。
2、Array.of():把一组值转为数组。

```bash
Array.of('1','2','3') //['1','2','3']
```

3、Array.find():数组查找，找出第一个符合条件，若没找到，返回undefined。
4、Array.findIndex():返回找到的位置，没找到返回-1.
5、arr.fill()
6、arr.includes()
6、arr.indexOf()

### 🌴 Promise
1、解决异步回调问题

```bash
new Promise(function(resolve,reject){
    //resolve成功调用
    //reject 失败调用
})

let a=10;
let promise = new Promise(function(resolve,reject){
    if(a==10){
        resolve('成功！');
    }else{
        resolve('失败！');
    }
})
promise.then(res=>{
    console.log(res);
},err=>{
    console.log(err);
})
```

2、Promise.resolve('a'):将现有的东西转成一个promise对象，resolve状态，成功状态。等价于：new Promise(resolve=>{resolve("a")})。
Promise.reject('a'):将现有的东西转成一个promise对象，reject状态，失败状态。
3、Promise.all([p1,p2,p3]):把promise打包，扔到数组里，打包完还是一个promise对象。必须确保所有的promise对象都是resolve状态，都是成功状态。
4、Promise.race([p1,p2,p3]):只要有一个成功，就返回。

### 🌴 模块化
在ES6之前，社区定制一套模块规范
* Commonjs----主要服务端nodejs  require('http')
* AMD---------requirejs,curljs
* CMD---------seajs

ES6 ， import/export/export default  .
* 可以是相对路径，也可绝对路径。
* import只会导入一次，无论引入多少次。
* import "./modules/1.js";如果这么用，相当于引入文件。
* import会自动提升到顶部，先执行
* 导出去模块内容，如果里面有定时器更改，外面也会改动，不像CommonJS规范缓存。
* 动态引入：类似node里的require，可以动态引入，默认import语法不能写入if之类的里面，返回值是promise对象
    优点：1、按需加载；2、可以写if中；3、路径也可以动态。


```bash
//1、import、export
    //模块
        const a=1;
        const b=2;
        let c=3
        export {
            a as first
            b as second
            c as third
        }
    //引入
        import {first,second,third} from ./modules.js

//2、import、export default
    export default引入时不用加花括号.
    //模块
        export default a=3;
    //引入
        import a from "./modules.js"
//3、动态引入
    import('./modules.js).then(res=>{
        console.log(res.a+res.b);
    })

    Promise.all([import('./1.js'),[import('./2.js')]).then(([res1,res2])=>{
        console.log(res1,res2);
    })
```

### 🌴 Class
ES6 的class可以看作只是一个语法糖，它的绝大部分功能，ES5 都可以做到，新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。

```bash
function Point(x, y) {
  this.x = x;
  this.y = y;
}
Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};
var p = new Point(1, 2);
//ES6改写
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

#### constructor 方法
1、constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。
2、constructor方法默认返回实例对象（即this），完全可以指定返回另外一个对象。
3、类必须使用new调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用new也可以执行。
#### 类的实例
生成类的实例的写法，与 ES5 完全一样，也是使用new命令。前面说过，如果忘记加上new，像函数那样调用Class，将会报错。
#### 取值函数（getter）和存值函数（setter）
与 ES5 一样，在“类”的内部可以使用get和set关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为
#### 属性表达式
类的属性名，可以采用表达式。

```bash
//Square类的方法名getArea，是从表达式得到的
let methodName = 'getArea';

class Square {
  constructor(length) {
    // ...
  }

  [methodName]() {
    // ...
  }
}
```

#### Class表达式

```bash
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};
```

#### 静态方法
1、在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。

```bash
class Foo {
  static classMethod() {
    return 'hello';
  }
}
Foo.classMethod() // 'hello'
var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

2、父类的静态方法，可以被子类继承，下节讨论。
#### 实例属性的新写法
实例属性除了定义在constructor()方法里面的this上面，也可以定义在类的最顶层。
```bash
class IncreasingCounter {
  constructor() {
    this._count = 0;
  }
  get value() {
    console.log('Getting the current value!');
    return this._count;
  }
  increment() {
    this._count++;
  }
}

//也可以
class IncreasingCounter {
  _count = 0;
  get value() {
    console.log('Getting the current value!');
    return this._count;
  }
  increment() {
    this._count++;
  }
}
```

#### 静态属性
静态属性指的是 Class 本身的属性，即Class.propName，而不是定义在实例对象（this）上的属性。

```bash
//为Foo类定义了一个静态属性prop。
class Foo {
}

Foo.prop = 1;
Foo.prop // 1

//提案
class MyClass {
  static myStaticProp = 42;

  constructor() {
    console.log(MyClass.myStaticProp); // 42
  }
}
```

#### 私有方法和私有属性
私有方法和私有属性，是只能在类的内部访问的方法和属性，外部不能访问。这是常见需求，有利于代码的封装。
一种做法是在命名上加以区别。下面代码_bar()方法前面的下划线，表示这是一个只限于内部使用的私有方法。但是，这种命名是不保险的，在类的外部，还是可以调用到这个方法。
```bash
class Widget {
  // 公有方法
  foo (baz) {
    this._bar(baz);
  }
  // 私有方法
  _bar(baz) {
    return this.snaf = baz;
  }
}
```
另一种方法就是将私有方法移出类，因为类内部的所有方法都是对外可见的。
#### 私有属性的提案
目前，有一个提案，为class加了私有属性。方法是在属性名之前，使用#表示。#count就是私有属性，只能在类的内部使用（this.#count）。如果在类的外部使用，就会报错。

#### new.target 属性
new是从构造函数生成实例对象的命令。ES6 为new命令引入了一个new.target属性，该属性一般用在构造函数之中，返回new命令作用于的那个构造函数。如果构造函数不是通过new命令或Reflect.construct()调用的，new.target会返回undefined，因此这个属性可以用来确定构造函数是怎么调用的。

Class 内部调用new.target，返回当前 Class。
子类继承父类时，new.target会返回子类。可以写出不能独立使用、必须继承后才能使用的类。
下面代码中，Shape类不能被实例化，只能用于继承。

```bash
class Shape {
  constructor() {
    if (new.target === Shape) {
      throw new Error('本类不能实例化');
    }
  }
}

class Rectangle extends Shape {
  constructor(length, width) {
    super();
    // ...
  }
}

var x = new Shape();  // 报错
var y = new Rectangle(3, 4);  // 正确
```

```bash
class IncreasingCounter {
  #count = 0;
  get value() {
    console.log('Getting the current value!');
    return this.#count;
  }
  increment() {
    this.#count++;
  }
}
```

### 🌴 Class继承（extends）
1、Class 可以通过extends关键字实现继承。
2、子类必须在constructor方法中调用super方法，否则新建实例时会报错。
这是因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。
3、如果子类没有定义constructor方法，这个方法会被默认添加。
4、在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。
这是因为子类实例的构建，基于父类实例，只有super方法才能调用父类实例。
5、父类的静态方法，也会被子类继承。
```bash
class Parent {
}

class Child extends Parent {
    constructor(x, y, color) {
        super(x, y); // 调用父类的constructor(x, y)
        this.color = color;
    }

    toString() {
        return this.color + ' ' + super.toString(); // 调用父类的toString()
    }
}

//3、
class ColorPoint extends Point {
}

// 等同于
class ColorPoint extends Point {
  constructor(...args) {
    super(...args);
  }
}
```
#### Object.getPrototypeOf()
判断，一个类是否继承了另一个类

```bash
Object.getPrototypeOf(ColorPoint) === Point 
//true
```
#### super 关键字
当作函数使用，也可以当作对象使用，在这两种情况下，它的用法完全不同。
1、第一种情况，super作为函数调用时，super()只能用在子类的构造函数之中,代表父类的构造函数。ES6 要求，子类的构造函数必须执行一次super函数。
注意：super虽然代表了父类A的构造函数，但是返回的是子类B的实例，即super内部的this指的是B的实例，因此super()在这里相当于A.prototype.constructor.call(this)。
下面，new.target指向当前正在执行的函数。可以看到，在super()执行时，它指向的是子类B的构造函数，而不是父类A的构造函数。也就是说，super()内部的this指向的是B。
```bash
class A {
  constructor() {
    console.log(new.target.name);
  }
}
class B extends A {
  constructor() {
    super();
  }
}
new A() // A
new B() // B
```

2、第二种情况，super作为对象时，在普通方法中，指向父类的原型对象；在静态方法中，指向父类。

下面，子类B当中的super.p()，就是将super当作一个对象使用。这时，super在普通方法之中，指向A.prototype，所以super.p()就相当于A.prototype.p()。
注：super指向父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的。

```bash
//1、在普通方法中
class A {
  p() {
    return 2;
  }
}
class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2
  }
}
let b = new B();


class Parent {
  static myMethod(msg) {
    console.log('static', msg);
  }

  myMethod(msg) {
    console.log('instance', msg);
  }
}

class Child extends Parent {
  static myMethod(msg) {
    super.myMethod(msg);
  }

  myMethod(msg) {
    super.myMethod(msg);
  }
}

Child.myMethod(1); // static 1

var child = new Child();
child.myMethod(2); // instance 2
```

#### 类的 prototype 属性和__proto__属性
ES5 中，每一个对象都有__proto__属性，指向对应的构造函数的prototype属性。Class 作为构造函数的语法糖，同时有prototype属性和__proto__属性，因此同时存在两条继承链。
1、子类的__proto__属性，表示构造函数的继承，总是指向父类。
2、子类prototype属性的__proto__属性，表示方法的继承，总是指向父类的prototype属性。

```bash
class A {
}

class B extends A {
}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```

#### 实例的 __proto__ 属性 
1、子类实例的__proto__属性的__proto__属性，指向父类实例的__proto__属性。也就是说，子类的原型的原型，是父类的原型。
2、通过子类实例的__proto__.__proto__属性，可以修改父类实例的行为。

```'bash
p2.__proto__.__proto__.printName = function () {
  console.log('Ha');
};

p1.printName() // "Ha"
```



### 🌴 Symbol
ES6 数据类型除了 Number 、 String 、 Boolean 、 Object、 null 和 undefined ，还新增了 Symbol 。

#### 基本用法
Symbol 函数栈不能用 new 命令，因为 Symbol 是原始数据类型，不是对象。可以接受一个字符串作为参数，为新创建的 Symbol 提供描述，用来显示在控制台或者作为字符串的时候使用，便于区分。

