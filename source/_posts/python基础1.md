---
title: python基础（一）
date: 2019-08-07 10:27:58
categories:
- python
tags: [python基础]
---
### Python 中文编码
Python中默认的编码格式是 ASCII 格式，在没修改编码格式时无法正确打印汉字，所以在读取中文时会报错。

解决方法为只要在文件开头加入 # -*- coding: UTF-8 -*- 或者 # coding=utf-8 就行了
### Python 基础语法
#### Python 标识符
* 在 Python 里，标识符由字母、数字、下划线组成。
* 在 Python 中，所有标识符可以包括英文、数字以及下划线(_)，但不能以数字开头。
* Python 中的标识符是区分大小写的。
* 以下划线开头的标识符是有特殊意义的。以单下划线开头 _foo 的代表不能直接访问的类属性，需通过类提供的接口进行访问，不能用 from xxx import * 而导入。
* 以双下划线开头的 __foo 代表类的私有成员，以双下划线开头和结尾的 __foo__ 代表 Python 里特殊方法专用的标识，如 __init__() 代表类的构造函数。

* Python 可以同一行显示多条语句，方法是用分号 ; 分开，如：
```
>>> print 'hello';print 'runoob';
hello
runoob
```
#### Python 保留字符
| 1        | 2    | 3  |
| :----:  | :----:  | :----: |
|and |exec |   not|
|assert | finally |or|
|break |  for pass|
|class  | from  |  print|
|continue   | global | raise|
|def |if | return|
|del |import | try|
|elif |   in | while|
|else  |  is | with|
|except | lambda | yield|

#### 行和缩进
Python 与其他语言最大的区别就是，Python的代码块不使用大括号{}来控制类，函数以及其他逻辑判断。python 最具特色的就是用缩进来写模块。
<font color='red'>缩进的空白数量是可变的，但是所有代码块语句必须包含相同的缩进空白数量，这个必须严格执行。</font>如下所示：
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
# 文件名：test.py

if True:
    print "Answer"
    print "True"
else:
    print "Answer"
    # 没有严格缩进，在执行时会报错
  print "False"
```
执行以上代码，会出现如下错误提醒：
```
$ python test.py  
  File "test.py", line 10
    print "False"
                ^
IndentationError: unindent does not match any outer indentation level
```
IndentationError: unindent does not match any outer indentation level错误表明，你使用的缩进方式不一致，有的是 tab 键缩进，有的是空格缩进，改为一致即可。

如果是 IndentationError: unexpected indent 错误, 则 python 编译器是在告诉你"Hi，老兄，你的文件里格式不对了，可能是tab和空格没对齐的问题"，所有 python 对格式要求非常严格。

因此，在 Python 的代码块中必须使用相同数目的行首缩进空格数。

建议你在每个缩进层次使用 单个制表符 或 两个空格 或 四个空格 , 切记不能混用
#### 多行语句
Python语句中一般以新行作为语句的结束符。

但是我们可以使用斜杠（ \）将一行的语句分为多行显示，如下所示：
```
total = item_one + \
        item_two + \
        item_three
```
语句中包含 [], {} 或 () 括号就不需要使用多行连接符。如下实例：
```
days = ['Monday', 'Tuesday', 'Wednesday',
        'Thursday', 'Friday']
```
#### Python 引号
Python 可以使用引号( ' )、双引号( " )、三引号( ''' 或 """ ) 来表示字符串，引号的开始与结束必须的相同类型的。

其中三引号可以由多行组成，编写多行文本的快捷语法，常用于文档字符串，在文件的特定地点，被当做注释。
#### Python注释
python中单行注释采用 # 开头。
```#!/usr/bin/python
# -*- coding: UTF-8 -*-
# 文件名：test.py

# 第一个注释
print "Hello, Python!"  # 第二个注释
```
python 中多行注释使用三个单引号(''')或三个双引号(""")。
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
# 文件名：test.py


'''
这是多行注释，使用单引号。
这是多行注释，使用单引号。
这是多行注释，使用单引号。
'''

"""
这是多行注释，使用双引号。
这是多行注释，使用双引号。
这是多行注释，使用双引号。
"""
```
####Python空行
函数之间或类的方法之间用空行分隔，表示一段新的代码的开始。类和函数入口之间也用一行空行分隔，以突出函数入口的开始。

空行与代码缩进不同，空行并不是Python语法的一部分。书写时不插入空行，Python解释器运行也不会出错。但是空行的作用在于分隔两段不同功能或含义的代码，便于日后代码的维护或重构。

记住：空行也是程序代码的一部分
#### 等待用户输入
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
# \n 实现换行。一旦用户按下 enter(回车) 键退出，其它键显示。
input("按下 enter 键退出，其他任意键显示...\n")   # python2 raw_input()
```
#### 同一行显示多条语句
Python可以在同一行中使用多条语句，语句之间使用分号(;)分割，以下是一个简单的实例：
```
#!/usr/bin/python
import sys; x = 'runoob'; sys.stdout.write(x + '\n')
```
#### Print 输出
print 默认输出是换行的，如果要实现不换行需要在变量末尾加上逗号 ,
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-

x="a"
y="b"
# 换行输出
print x
print y

print '---------'
# 不换行输出
print x,
print y,

# 不换行输出
print x,y
```
以上实例执行结果为：
```
a
b
---------
a b a b
```
#### 多个语句构成代码组
缩进相同的一组语句构成一个代码块，我们称之代码组。

像if、while、def和class这样的复合语句，首行以关键字开始，以冒号( : )结束，该行之后的一行或多行代码构成代码组。

我们将首行及后面的代码组称为一个子句(clause)。
```
if expression : 
   suite 
elif expression :  
   suite  
else :  
   suite 
```
### 变量类型
变量存储在内存中的值。这就意味着在创建变量时会在内存中开辟一个空间。

基于变量的数据类型，解释器会分配指定内存，并决定什么数据可以被存储在内存中。

因此，变量可以指定不同的数据类型，这些变量可以存储整数，小数或字符。
#### 变量赋值
* Python 中的变量赋值不需要类型声明。
* 每个变量在内存中创建，都包括变量的标识，名称和数据这些信息。
* 每个变量在使用前都必须赋值，变量赋值以后该变量才会被创建。
* 等号（=）用来给变量赋值。
* 等号（=）运算符左边是一个变量名,等号（=）运算符右边是存储在变量中的值。

```
counter = 100 # 赋值整型变量
miles = 1000.0 # 浮点型
name = "John" # 字符串
 
print counter
print miles
print name
```
#### 多个变量赋值
Python允许你同时为多个变量赋值。例如：
a = b = c = 1
以上实例，创建一个整型对象，值为1，三个变量被分配到相同的内存空间上。
您也可以为多个对象指定多个变量。例如：
a, b, c = 1, 2, "john"
以上实例，两个整型对象 1 和 2 分别分配给变量 a 和 b，字符串对象 "john" 分配给变量 c。
#### 标准数据类型
在内存中存储的数据可以有多种类型。
例如，一个人的年龄可以用数字来存储，他的名字可以用字符来存储。
Python 定义了一些标准类型，用于存储各种类型的数据。
Python有五个标准的数据类型：
* Numbers（数字）
* String（字符串）
* List（列表）
* Tuple（元组）
* Dictionary（字典）

#### Python数字
数字数据类型用于存储数值。

他们是不可改变的数据类型，这意味着改变数字数据类型会分配一个新的对象。

当你指定一个值时，Number对象就会被创建：

var1 = 1
var2 = 10
您也可以使用del语句删除一些对象的引用。
del语句的语法是：
del var1[,var2[,var3[....,varN]]]]
您可以通过使用del语句删除单个或多个对象的引用。例如：
del var
del var_a, var_b
Python支持四种不同的数字类型：
>int（有符号整型）
>long（长整型[也可以代表八进制和十六进制]）
>float（浮点型）
>complex（复数）

#### Python字符串
字符串或串(String)是由数字、字母、下划线组成的一串字符。
一般记为 :
s="a1a2···an"(n>=0)
python的字串列表有2种取值顺序:

* 从左到右索引默认0开始的，最大范围是字符串长度少1
* 从右到左索引默认-1开始的，最大范围是字符串开头

![01.png](01.png)
如果你要实现从字符串中获取一段子字符串的话，可以使用 [头下标:尾下标] 来截取相应的字符串，其中下标是从 0 开始算起，可以是正数或负数，下标可以为空表示取到头或尾。
[头下标:尾下标] 获取的子字符串包含头下标的字符，但不包含尾下标的字符。
比如:
```
s = 'abcdef'
s[1:5]
'bcde'
```
当使用以冒号分隔的字符串，python 返回一个新的对象，结果包含了以这对偏移标识的连续的内容，左边的开始是包含了下边界。
上面的结果包含了 s[1] 的值 b，而取到的最大范围不包括尾下标，就是 s[5] 的值 f。
![02.png](02.png)
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
str = 'Hello World!'
print str           # 输出完整字符串
print str[0]        # 输出字符串中的第一个字符
print str[2:5]      # 输出字符串中第三个至第六个之间的字符串
print str[2:]       # 输出从第三个字符开始的字符串
print str * 2       # 输出字符串两次
print str + "TEST"  # 输出连接的字符串
```
以上实例输出结果：
```
Hello World!
H
llo
llo World!
Hello World!Hello World!
Hello World!TEST
```
Python 列表截取可以接收第三个参数，参数作用是截取的步长，以下实例在索引 1 到索引 4 的位置并设置为步长为 2（间隔一个位置）来截取字符串：
![03.png](03.png)
#### Python列表
List（列表） 是 Python 中使用最频繁的数据类型。
列表可以完成大多数集合类的数据结构实现。它支持字符，数字，字符串甚至可以包含列表（即嵌套）。
列表用 [ ] 标识，是 python 最通用的复合数据类型。
列表中值的切割也可以用到变量 [头下标:尾下标] ，就可以截取相应的列表，从左到右索引默认 0 开始，从右到左索引默认 -1 开始，下标可以为空表示取到头或尾。
![04.png](04.png)
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
list = [ 'runoob', 786 , 2.23, 'john', 70.2 ]
tinylist = [123, 'john']
print list               # 输出完整列表
print list[0]            # 输出列表的第一个元素
print list[1:3]          # 输出第二个至第三个元素 
print list[2:]           # 输出从第三个开始至列表末尾的所有元素
print tinylist * 2       # 输出列表两次
print list + tinylist    # 打印组合的列表
```
以上实例输出结果：
```
['runoob', 786, 2.23, 'john', 70.2]
runoob
[786, 2.23]
[2.23, 'john', 70.2]
[123, 'john', 123, 'john']
['runoob', 786, 2.23, 'john', 70.2, 123, 'john']
```
#### Python 元组
元组是另一个数据类型，类似于 List（列表）。
元组用 () 标识。内部元素用逗号隔开。但是元组不能二次赋值，相当于只读列表。
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
tuple = ( 'runoob', 786 , 2.23, 'john', 70.2 )
tinytuple = (123, 'john')
 
print tuple               # 输出完整元组
print tuple[0]            # 输出元组的第一个元素
print tuple[1:3]          # 输出第二个至第四个（不包含）的元素 
print tuple[2:]           # 输出从第三个开始至列表末尾的所有元素
print tinytuple * 2       # 输出元组两次
print tuple + tinytuple   # 打印组合的元组
```
以上实例输出结果：
```
('runoob', 786, 2.23, 'john', 70.2)
runoob
(786, 2.23)
(2.23, 'john', 70.2)
(123, 'john', 123, 'john')
('runoob', 786, 2.23, 'john', 70.2, 123, 'john')
```
以下是元组无效的，因为元组是不允许更新的。而列表是允许更新的：
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
tuple = ( 'runoob', 786 , 2.23, 'john', 70.2 )
list = [ 'runoob', 786 , 2.23, 'john', 70.2 ]
tuple[2] = 1000    # 元组中是非法应用
list[2] = 1000     # 列表中是合法应用
```
#### Python 字典
字典(dictionary)是除列表以外python之中最灵活的内置数据结构类型。列表是有序的对象集合，字典是无序的对象集合。
两者之间的区别在于：字典当中的元素是通过键来存取的，而不是通过偏移存取。
字典用"{ }"标识。字典由索引(key)和它对应的值value组成。
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
dict = {}
dict['one'] = "This is one"
dict[2] = "This is two"
tinydict = {'name': 'john','code':6734, 'dept': 'sales'}
print dict['one']          # 输出键为'one' 的值
print dict[2]              # 输出键为 2 的值
print tinydict             # 输出完整的字典
print tinydict.keys()      # 输出所有键
print tinydict.values()    # 输出所有值
```
输出结果为：
```
This is one
This is two
{'dept': 'sales', 'code': 6734, 'name': 'john'}
['dept', 'code', 'name']
['sales', 6734, 'john']
```
#### Python数据类型转换
|函数 | 描述|
|----|----|
|int(x [,base])|将x转换为一个整数|
|long(x [,base] )|将x转换为一个长整数|
|float(x)|将x转换到一个浮点数|
|complex(real [,imag])|创建一个复数|
|str(x)|将对象 x 转换为字符串|
|repr(x)|将对象 x 转换为表达式字符串|
|eval(str)|用来计算在字符串中的有效Python表达式,并返回一个对象|
|tuple(s)|将序列 s 转换为一个元组|
|list(s)|将序列 s 转换为一个列表|
|set(s)|转换为可变集合|
|dict(d)|创建一个字典。d 必须是一个序列 (key,value)元组。|
|frozenset(s)|转换为不可变集合|
|chr(x)|将一个整数转换为一个字符|
|unichr(x)|将一个整数转换为Unicode字符|
|ord(x)|将一个字符转换为它的整数值|
|hex(x)|将一个整数转换为一个十六进制字符串|
|oct(x)|将一个整数转换为一个八进制字符串|
### Python 运算符
* 算术运算符
* 比较（关系）运算符
* 赋值运算符
* 逻辑运算符
* 位运算符
* 成员运算符
* 身份运算符
* 运算符优先级

#### Python算术运算符
|运算符| 描述  |实例|
|----|----|----|
|+ |  加 - 两个对象相加 | a + b 输出结果 30|
|- |  减 - 得到负数或是一个数减去另一个数 |a - b 输出结果 -10|
|* |  乘 - 两个数相乘或是返回一个被重复若干次的字符串  | a * b 输出结果 200|
|/ |  除 - x除以y   | b / a 输出结果 2|
|% |  取模 - 返回除法的余数  |  b % a 输出结果 0|
|**|  幂 - 返回x的y次幂 a**b 为10的20次方，| 输出结果 100000000000000000000|
|//|  取整除 - 返回商的整数部分（向下取整） |    9//2    结果：4|
#### Python比较运算符
![05.png](05.png)
#### Python赋值运算符
![06.png](06.png)
#### Python位运算符
![07.png](07.png)
#### Python逻辑运算符
![08.png](08.png)
#### Python成员运算符
![09.png](09.png)
#### Python身份运算符
![10.png](10.png)
#### Python运算符优先级
![11.png](11.png)