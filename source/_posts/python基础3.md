---
title: python基础(三)--数据类型
date: 2019-08-07 18:59:42
categories:
- python
tags: [python基础]
---
### Python Number(数字)
Python Number 数据类型用于存储数值。

数据类型是不允许改变的,这就意味着如果改变 Number 数据类型的值，将重新分配内存空间。

以下实例在变量赋值时 Number 对象将被创建：
```
var1 = 1
var2 = 10
```
Python 支持四种不同的数值类型：

* 整型(Int) - 通常被称为是整型或整数，是正或负整数，不带小数点。
* 长整型(long integers) - 无限大小的整数，整数最后是一个大写或小写的L。
* 浮点型(floating point real values) - 浮点型由整数部分与小数部分组成，浮点型也可以使用科学计数法表示（2.5e2 = 2.5 x 102 = 250）
* 复数(complex numbers) - 复数由实数部分和虚数部分构成，可以用a + bj,或者complex(a,b)表示， 复数的实部a和虚部b都是浮点型。

#### Python Number 类型转换
![01.png](01.png)
#### Python math 模块、cmath 模块
Python 中数学运算常用的函数基本都在 math 模块、cmath 模块中。

Python math 模块提供了许多对浮点数的数学运算函数。

Python cmath 模块包含了一些用于复数运算的函数。

cmath 模块的函数跟 math 模块函数基本一致，区别是 cmath 模块运算的是复数，math 模块运算的是数学运算。

要使用 math 或 cmath 函数必须先导入：
```
import math
```
#### Python数学函数
![02.png](02.png)
#### Python随机数函数
![03.png](03.png)
#### Python三角函数
![04.png](04.png)
#### Python数学常量
![05.png](05.png)
### Python 字符串
可以使用引号('或")来创建字符串
Python访问字符串中的值
Python不支持单字符类型，单字符在 Python 中也是作为一个字符串使用。

Python访问子字符串，可以使用方括号来截取字符串，如下实例：
```
#!/usr/bin/python
 
var1 = 'Hello World!'
var2 = "Python Runoob"
 
print "var1[0]: ", var1[0]
print "var2[1:5]: ", var2[1:5]
```
Python字符串更新
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
var1 = 'Hello World!'
print "更新字符串 :- ", var1[:6] + 'Runoob!'
```
Python转义字符
![06.png](06.png)
![07.png](07.png)
Python字符串运算符
![08.png](08.png)
Python 字符串格式化 
![09.png](09.png)
```
#!/usr/bin/python

print "My name is %s and weight is %d kg!" % ('Zara', 21) 
```
以上实例输出结果：
```
My name is Zara and weight is 21 kg!
```
Python三引号（triple quotes）

python中三引号可以将复杂的字符串进行复制:
python三引号允许一个字符串跨多行，字符串中可以包含换行符、制表符以及其他特殊字符。
三引号的语法是一对连续的单引号或者双引号（通常都是成对的用）。

Unicode 字符串
引号前小写的"u"表示这里创建的是一个 Unicode 字符串。如果你想加入一个特殊字符，可以使用 Python 的 Unicode-Escape 编码。
### Python 列表(List)
序列是Python中最基本的数据结构。序列中的每个元素都分配一个数字 - 它的位置，或索引，第一个索引是0，第二个索引是1，依此类推。
Python有6个序列的内置类型，但最常见的是列表和元组。
序列都可以进行的操作包括索引，切片，加，乘，检查成员。
此外，Python已经内置确定序列的长度以及确定最大和最小的元素的方法。
列表是最常用的Python数据类型，它可以作为一个方括号内的逗号分隔值出现。
列表的数据项不需要具有相同的类型
创建一个列表，只要把逗号分隔的不同的数据项使用方括号括起来即可。如下所示：
```
list1 = ['physics', 'chemistry', 1997, 2000]
list2 = [1, 2, 3, 4, 5 ]
list3 = ["a", "b", "c", "d"]
```
访问列表中的值
使用下标索引来访问列表中的值，同样你也可以使用方括号的形式截取字符，如下所示：
```
#!/usr/bin/python
 
list1 = ['physics', 'chemistry', 1997, 2000]
list2 = [1, 2, 3, 4, 5, 6, 7 ]
 
print "list1[0]: ", list1[0]
print "list2[1:5]: ", list2[1:5]
```
更新列表
你可以对列表的数据项进行修改或更新，你也可以使用append()方法来添加列表项，如下所示：
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
list = []          ## 空列表
list.append('Google')   ## 使用 append() 添加元素
list.append('Runoob')
print list
```
删除列表元素
```
#!/usr/bin/python
 
list1 = ['physics', 'chemistry', 1997, 2000]
 
print list1
del list1[2]
print "After deleting value at index 2 : "
print list1
```
Python列表脚本操作符
列表对 + 和 * 的操作符与字符串相似。+ 号用于组合列表，* 号用于重复列表。
![10.png](10.png)
Python列表截取
```
>>>L = ['Google', 'Runoob', 'Taobao']
>>> L[2]
'Taobao'
>>> L[-2]
'Runoob'
>>> L[1:]
['Runoob', 'Taobao']
>>>
```
![11.png](11.png)
Python列表函数&方法
![12.png](12.png)
![13.png](13.png)
### Python 元组
Python的元组与列表类似，不同之处在于元组的元素不能修改。
元组使用小括号，列表使用方括号。
元组创建很简单，只需要在括号中添加元素，并使用逗号隔开即可。
```
tup1 = ('physics', 'chemistry', 1997, 2000)
tup2 = (1, 2, 3, 4, 5 )
tup3 = "a", "b", "c", "d"
```
创建空元组
```
tup1 = ()
```
元组中只包含一个元素时，需要在元素后面添加逗号
```
tup1 = (50,)
```
访问元组
元组可以使用下标索引来访问元组中的值
```
#!/usr/bin/python
 
tup1 = ('physics', 'chemistry', 1997, 2000)
tup2 = (1, 2, 3, 4, 5, 6, 7 )
 
print "tup1[0]: ", tup1[0]
print "tup2[1:5]: ", tup2[1:5]
```
修改元组
元组中的元素值是不允许修改的，但我们可以对元组进行连接组合
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
tup1 = (12, 34.56)
tup2 = ('abc', 'xyz')
 
# 以下修改元组元素操作是非法的。
# tup1[0] = 100
 
# 创建一个新的元组
tup3 = tup1 + tup2
print tup3
```
以上实例输出结果：
```
(12, 34.56, 'abc', 'xyz')
```
删除元组
元组中的元素值是不允许删除的，但我们可以使用del语句来删除整个元组
```
#!/usr/bin/python
 
tup = ('physics', 'chemistry', 1997, 2000)
 
print tup
del tup
print "After deleting tup : "
print tup
```
元组运算符
与字符串一样，元组之间可以使用 + 号和 * 号进行运算。这就意味着他们可以组合和复制，运算后会生成一个新的元组
![14.png](14.png)
元组索引，截取
![15.png](15.png)
无关闭分隔符
任意无符号的对象，以逗号隔开，默认为元组
元组内置函数
![16.png](16.png)
### Python 字典(Dictionary)
字典是另一种可变容器模型，且可存储任意类型对象。

字典的每个键值 key=>value 对用冒号 : 分割，每个键值对之间用逗号 , 分割，整个字典包括在花括号 {} 中 ,格式如下所示：
```
d = {key1 : value1, key2 : value2 }
```
键一般是唯一的，如果重复最后的一个键值对会替换前面的，值不需要唯一。
```
>>>dict = {'a': 1, 'b': 2, 'b': '3'}
>>> dict['b']
'3'
>>> dict
{'a': 1, 'b': '3'}
```
值可以取任何数据类型，但键必须是不可变的，如字符串，数字或元组。

一个简单的字典实例：
```
dict = {'Alice': '2341', 'Beth': '9102', 'Cecil': '3258'}
```
访问字典里的值
```
#!/usr/bin/python
 
dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'}
 
print "dict['Name']: ", dict['Name']
print "dict['Age']: ", dict['Age']
```
修改字典
向字典添加新内容的方法是增加新的键/值对，修改或删除已有键/值对
```
#!/usr/bin/python
 
dict = {'Name': 'Zara', 'Age': 7, 'Class': 'First'}
 
dict['Age'] = 8 # 更新
dict['School'] = "RUNOOB" # 添加
 
 
print "dict['Age']: ", dict['Age']
print "dict['School']: ", dict['School']
```
删除字典元素
能删单一的元素也能清空字典，清空只需一项操作。

显示删除一个字典用del命令.
### Python 日期和时间
Python 程序能用很多方式处理日期和时间，转换日期格式是一个常见的功能。

Python 提供了一个 time 和 calendar 模块可以用于格式化日期和时间。

时间间隔是以秒为单位的浮点小数。

每个时间戳都以自从1970年1月1日午夜（历元）经过了多长时间来表示。

Python 的 time 模块下有很多函数可以转换常见日期格式。如函数time.time()用于获取当前时间戳
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
import time;  # 引入time模块
 
ticks = time.time()
print "当前时间戳为:", ticks
```
以上实例输出结果：
```
当前时间戳为: 1459994552.51
```
获取当前时间
```
localtime = time.localtime(time.time())
print "本地时间为 :", localtime
```

获取格式化的时间
```
import time
 
localtime = time.asctime( time.localtime(time.time()) )
print "本地时间为 :", localtime
```
格式化日期
我们可以使用 time 模块的 strftime 方法来格式化日期，：
```
time.strftime(format[, t])
```
获取某月日历
```
import calendar
 
cal = calendar.month(2016, 1)
print "以下输出2016年1月份的日历:"
print cal
```
### Python 函数
函数是组织好的，可重复使用的，用来实现单一，或相关联功能的代码段。

函数能提高应用的模块性，和代码的重复利用率。你已经知道Python提供了许多内建函数，比如print()。但你也可以自己创建函数，这被叫做用户自定义函数。
#### 定义一个函数
你可以定义一个由自己想要功能的函数，以下是简单的规则：

* 函数代码块以 def 关键词开头，后接函数标识符名称和圆括号()。
* 任何传入参数和自变量必须放在圆括号中间。圆括号之间可以用于定义参数。
* 函数的第一行语句可以选择性地使用文档字符串—用于存放函数说明。
* 函数内容以冒号起始，并且缩进。
* return [表达式] 结束函数，选择性地返回一个值给调用方。不带表达式的return相当于返回 None。

#### 函数调用
定义一个函数只给了函数一个名称，指定了函数里包含的参数，和代码块结构。

这个函数的基本结构完成以后，你可以通过另一个函数调用执行，也可以直接从Python提示符执行。

如下实例调用了printme（）函数：
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
# 定义函数
def printme( str ):
   "打印任何传入的字符串"
   print str;
   return;
 
# 调用函数
printme("我要调用用户自定义函数!");
printme("再次调用同一函数");
```