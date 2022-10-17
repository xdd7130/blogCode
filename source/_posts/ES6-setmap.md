---
title: ES6(二) Set和Map
date: 2020-11-23 19:18:34
categories:
- Web前端
tags: 
    - ES6
---

### 🌴 Set 和 WeakSet数据结构
#### 👉 Set：
1、ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值。

```bash
    //add方法
    const s = new Set();

    [2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

    for (let i of s) {
    console.log(i);
    }
    // 2 3 5 4


    // 一、 数组去重
    [...new Set(array)]
    // 二、Array.from方法可以将 Set 结构转为数组。
    const items = new Set([1, 2, 3, 4, 5]);
    const array = Array.from(items);
 
```

2、Set 实例的属性和方法
* 实例属性：
Set.prototype.constructor：构造函数，默认就是Set函数。
Set.prototype.size：返回Set实例的成员总数。
* 实例方法:
Set.prototype.add(value)：添加某个值，返回 Set 结构本身。
Set.prototype.delete(value)：删除某个值，返回一个布尔值，表示删除是否成功。
Set.prototype.has(value)：返回一个布尔值，表示该值是否为Set的成员。
Set.prototype.clear()：清除所有成员，没有返回值。

例：
```bash
s.add(1).add(2).add(2);
// 注意2被加入了两次

s.size // 2

s.has(1) // true
s.has(2) // true
s.has(3) // false

s.delete(2);
s.has(2) // false
```

3、遍历操作

四个遍历方法：

Set.prototype.keys()：返回键名的遍历器
Set.prototype.values()：返回键值的遍历器
Set.prototype.entries()：返回键值对的遍历器
Set.prototype.forEach()：使用回调函数遍历每个成员

#### WeakSet
WeakSet 结构与 Set 类似，也是不重复的值的集合。但是，它与 Set 有两个区别:
1、WeakSet 的成员只能是对象，而不能是其他类型的值。
2、WeakSet 中的对象都是弱引用，即垃圾回收机制不考虑 WeakSet 对该对象的引用

WeakSet 结构有以下三个方法：

WeakSet.prototype.add(value)：向 WeakSet 实例添加一个新成员。
WeakSet.prototype.delete(value)：清除 WeakSet 实例的指定成员。
WeakSet.prototype.has(value)：返回一个布尔值，表示某个值是否在 WeakSet 实例之中

### 🌴 Map 和 WeakMap数据结构
#### Map
JavaScript 的对象（Object），本质上是键值对的集合（Hash 结构），但是传统上只能用字符串当作键。这给它的使用带来了很大的限制。
ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键
```bash
const m = new Map();
const o = {p: 'Hello World'};

m.set(o, 'content')
m.get(o) // "content"

m.has(o) // true
m.delete(o) // true
m.has(o) // false
```

如果对同一个键多次赋值，后面的值将覆盖前面的值：
```bash
const map = new Map();

map
.set(1, 'aaa')
.set(1, 'bbb');

map.get(1) // "bbb"
```

如果读取一个未知的键，则返回undefined。
```bash
new Map().get('asfddfsasadf')
// undefined
```


注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。
```bash
const map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined
```
上面代码的set和get方法，表面是针对同一个键，但实际上这是两个不同的数组实例，内存地址是不一样的，因此get方法无法读取该键，返回undefined。
同样的值的两个实例，在 Map 结构中被视为两个键。
```bash
const map = new Map();

const k1 = ['a'];
const k2 = ['a'];

map
.set(k1, 111)
.set(k2, 222);

map.get(k1) // 111
map.get(k2) // 222
```
另外：
```bash
let map = new Map();

map.set(-0, 123);
map.get(+0) // 123

map.set(true, 1);
map.set('true', 2);
map.get(true) // 1

map.set(undefined, 3);
map.set(null, 4);
map.get(undefined) // 3

map.set(NaN, 123);
map.get(NaN) // 123
```

#### 实例的属性和操作方法
1、size 属性
size属性返回 Map 结构的成员总数
2、Map.prototype.set(key, value)
set方法设置键名key对应的键值为value，然后返回整个 Map 结构。如果key已经有值，则键值会被更新，否则就新生成该键。set方法返回的是当前的Map对象，因此可以采用链式写法。
3、Map.prototype.get(key)
get方法读取key对应的键值，如果找不到key，返回undefined。
4、Map.prototype.has(key)
has方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。
5、Map.prototype.delete(key)
delete方法删除某个键，返回true。如果删除失败，返回false。
6、Map.prototype.clear()
clear方法清除所有成员，没有返回值。

#### 遍历方法

Map.prototype.keys()：返回键名的遍历器。
Map.prototype.values()：返回键值的遍历器。
Map.prototype.entries()：返回所有成员的遍历器。
Map.prototype.forEach()：遍历 Map 的所有成员。

Map 结构转为数组结构，比较快速的方法是使用扩展运算符（...）。
```bash
const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
// [1, 2, 3]

[...map.values()]
// ['one', 'two', 'three']

[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]

[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]
```

#### 与其他数据结构的互相转换
1、Map 转为数组
```bash
const myMap = new Map()
  .set(true, 7)
  .set({foo: 3}, ['abc']);
[...myMap]
// [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
```
2、数组 转为 Map
将数组传入 Map 构造函数，就可以转为 Map。
```bash
new Map([
  [true, 7],
  [{foo: 3}, ['abc']]
])
// Map {
//   true => 7,
//   Object {foo: 3} => ['abc']
// }
```

3、Map 转为对象
如果所有 Map 的键都是字符串，它可以无损地转为对象
```bash
function strMapToObj(strMap) {
  let obj = Object.create(null);
  for (let [k,v] of strMap) {
    obj[k] = v;
  }
  return obj;
}

const myMap = new Map()
  .set('yes', true)
  .set('no', false);
strMapToObj(myMap)
// { yes: true, no: false }
```
如果有非字符串的键名，那么这个键名会被转成字符串，再作为对象的键名。

4、对象转为 Map
对象转为 Map 可以通过Object.entries()。
```bash
let obj = {"a":1, "b":2};
let map = new Map(Object.entries(obj));


//此外，也可以自己实现一个转换函数。

function objToStrMap(obj) {
  let strMap = new Map();
  for (let k of Object.keys(obj)) {
    strMap.set(k, obj[k]);
  }
  return strMap;
}

objToStrMap({yes: true, no: false})
// Map {"yes" => true, "no" => false}
```

5、Map 转为 JSON
Map 转为 JSON 要区分两种情况:
一种情况是，Map 的键名都是字符串，这时可以选择转为对象 JSON。
```bash
function strMapToJson(strMap) {
  return JSON.stringify(strMapToObj(strMap));
}

let myMap = new Map().set('yes', true).set('no', false);
strMapToJson(myMap)
// '{"yes":true,"no":false}'
```
另一种情况是，Map 的键名有非字符串，这时可以选择转为数组 JSON。
```bash
function mapToArrayJson(map) {
  return JSON.stringify([...map]);
}

let myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
mapToArrayJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]'
```

6、JSON 转为 Map

JSON 转为 Map，正常情况下，所有键名都是字符串。
```bash
function jsonToStrMap(jsonStr) {
  return objToStrMap(JSON.parse(jsonStr));
}

jsonToStrMap('{"yes": true, "no": false}')
// Map {'yes' => true, 'no' => false}
```

但是，有一种特殊情况，整个 JSON 就是一个数组，且每个数组成员本身，又是一个有两个成员的数组。这时，它可以一一对应地转为 Map。这往往是 Map 转为数组 JSON 的逆操作。
```bash
function jsonToMap(jsonStr) {
  return new Map(JSON.parse(jsonStr));
}

jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
// Map {true => 7, Object {foo: 3} => ['abc']}
```