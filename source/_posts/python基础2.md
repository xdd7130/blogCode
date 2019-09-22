---
title: python基础(二)--语句
date: 2019-08-07 18:24:58
categories:
- python
tags: [python基础]
---
### Python 条件语句
Python条件语句是通过一条或多条语句的执行结果（True或者False）来决定执行的代码块。
```
if 判断条件：
    执行语句……
else：
    执行语句……
```
![01.gif](01.gif)
if 语句的判断条件可以用>（大于）、<(小于)、==（等于）、>=（大于等于）、<=（小于等于）来表示其关系。

当判断条件为多个值时，可以使用以下形式：
```
if 判断条件1:
    执行语句1……
elif 判断条件2:
    执行语句2……
elif 判断条件3:
    执行语句3……
else:
    执行语句4……
```
简单的语句组
```
#!/usr/bin/python 
# -*- coding: UTF-8 -*-
 
var = 100 
if ( var  == 100 ) : print "变量 var 的值为100" 
print "Good bye!"
```
### Python 循环语句
![01.png](01.png)
循环控制语句
![02.png](02.png)
#### Python While 循环语句
Python 编程中 while 语句用于循环执行程序，即在某条件下，循环执行某段程序，以处理需要重复处理的相同任务。其基本形式为：
```
while 判断条件：
    执行语句……
```
![02.gif](02.gif)
while 语句时还有另外两个重要的命令 continue，break 来跳过循环，continue 用于跳过该次循环，break .

循环使用 else 语句
在 python 中，while … else 在循环条件为 false 时执行 else 语句块：
```
#!/usr/bin/python
 
count = 0
while count < 5:
   print count, " is  less than 5"
   count = count + 1
else:
   print count, " is not less than 5"
```
简单语句组
类似 if 语句的语法，如果你的 while循环体中只有一条语句，你可以将该语句与while写在同一行中， 如下所示：
```
#!/usr/bin/python
flag = 1
while (flag): print 'Given flag is really true!'
print "Good bye!"
```
#### Python for 循环语句
Python for循环可以遍历任何序列的项目，如一个列表或者一个字符串。

语法：
```
for iterating_var in sequence:
   statements(s)
```

```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
for letter in 'Python':     # 第一个实例
   print '当前字母 :', letter
 
fruits = ['banana', 'apple',  'mango']
for fruit in fruits:        # 第二个实例
   print '当前水果 :', fruit
 
print "Good bye!"
```
结果：
```
当前字母 : P
当前字母 : y
当前字母 : t
当前字母 : h
当前字母 : o
当前字母 : n
当前水果 : banana
当前水果 : apple
当前水果 : mango
Good bye!
```
通过序列索引迭代:
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
fruits = ['banana', 'apple',  'mango']
for index in range(len(fruits)):
   print '当前水果 :', fruits[index]
 
print "Good bye!"
```
循环使用 else 语句
在 python 中，for … else 表示这样的意思，for 中的语句和普通的没有区别，else 中的语句会在循环正常执行完（即 for 不是通过 break 跳出而中断的）的情况下执行，while … else 也是一样。
```
for num in range(10,20):  # 迭代 10 到 20 之间的数字
   for i in range(2,num): # 根据因子迭代
      if num%i == 0:      # 确定第一个因子
         j=num/i          # 计算第二个因子
         print '%d 等于 %d * %d' % (num,i,j)
         break            # 跳出当前循环
   else:                  # 循环的 else 部分
      print num, '是一个质数'
```
#### Python 循环嵌套
Python 语言允许在一个循环体里面嵌入另一个循环。

Python for 循环嵌套语法：
```
for iterating_var in sequence:
   for iterating_var in sequence:
      statements(s)
   statements(s)
Python while 循环嵌套语法：
```

```
while expression:
   while expression:
      statement(s)
   statement(s)
```
你可以在循环体内嵌入其他的循环体，如在while循环中可以嵌入for循环， 反之，你可以在for循环中嵌入while循环
#### Python break 语句
Python break语句，就像在C语言中，打破了最小封闭for或while循环。

break语句用来终止循环语句，即循环条件没有False条件或者序列还没被完全递归完，也会停止执行循环语句。

break语句用在while和for循环中。

如果您使用嵌套循环，break语句将停止执行最深层的循环，并开始执行下一行代码。
#### Python continue 语句
Python continue 语句跳出本次循环，而break跳出整个循环。

continue 语句用来告诉Python跳过当前循环的剩余语句，然后继续进行下一轮循环。

continue语句用在while和for循环中。
#### Python pass 语句
Python pass 是空语句，是为了保持程序结构的完整性。

pass 不做任何事情，一般用做占位语句。