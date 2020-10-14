---
title: python基础(四)
date: 2019-08-07 20:04:14
categories:
- python
tags: [python基础]
---
## Python 模块
Python 模块(Module)，是一个 Python 文件，以 .py 结尾，包含了 Python 对象定义和Python语句。

模块让你能够有逻辑地组织你的 Python 代码段。

把相关的代码分配到一个模块里能让你的代码更好用，更易懂。

模块能定义函数，类和变量，模块里也能包含可执行的代码。
下例是个简单的模块 support.py：
```
def print_func( par ):
   print "Hello : ", par
   return
```
import 语句
```
import module1[, module2[,... moduleN]]
```
比如要引用模块 math，就可以在文件最开始的地方用 import math 来引入。在调用 math 模块中的函数时，必须这样引用：
```
模块名.函数名
```
当解释器遇到 import 语句，如果模块在当前的搜索路径就会被导入。

搜索路径是一个解释器会先进行搜索的所有目录的列表。如想要导入模块 support.py，需要把命令放在脚本的顶端：

test.py 文件代码：
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
# 导入模块
import support
 
# 现在可以调用模块里包含的函数了
support.print_func("Runoob")
```
from…import 语句
Python 的 from 语句让你从模块中导入一个指定的部分到当前命名空间中。语法如下：
```
from modname import name1[, name2[, ... nameN]]
```
例如，要导入模块 fib 的 fibonacci 函数，使用如下语句：
```
from fib import fibonacci
```
from…import* 语句
把一个模块的所有内容全都导入到当前的命名空间也是可行的，只需使用如下声明：
```
from modname import *
```
dir()函数
dir() 函数一个排好序的字符串列表，内容是一个模块里定义过的名字。

返回的列表容纳了在一个模块里定义的所有模块，变量和函数。如下一个简单的实例：
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
# 导入内置math模块
import math
 
content = dir(math)
 
print content;
```
## Python 文件I/O
### 打印到屏幕
最简单的输出方法是用print语句，你可以给它传递零个或多个用逗号隔开的表达式。此函数把你传递的表达式转换成一个字符串表达式，并将结果写到标准输出如下：
```
#!/usr/bin/python
# -*- coding: UTF-8 -*- 

print "Python 是一个非常棒的语言，不是吗？"
```
读取键盘输入
Python提供了两个内置函数从标准输入读入一行文本，默认的标准输入是键盘。如下：

* raw_input
* input

打开和关闭文件
open 函数
你必须先用Python内置的open()函数打开一个文件，创建一个file对象，相关的方法才可以调用它进行读写。
```
file object = open(file_name [, access_mode][, buffering])
```
* file_name：file_name变量是一个包含了你要访问的文件名称的字符串值。
* access_mode：access_mode决定了打开文件的模式：只读，写入，追加等。所有可取值见如下的完全列表。这个参数是非强制的，默认文件访问模式为只读(r)。
* buffering:如果buffering的值被设为0，就不会有寄存。如果buffering的值取1，访问文件时会寄存行。如果将buffering的值设为大于1的整数，表明了这就是的寄存区的缓冲大小。如果取负值，寄存区的缓冲大小则为系统默认。

![01.png](01.png)
![02.png](02.png)

write()方法
write()方法可将任何字符串写入一个打开的文件。需要重点注意的是，Python字符串可以是二进制数据，而不是仅仅是文字。

write()方法不会在字符串的结尾添加换行符('\n')

read()方法
read（）方法从一个打开的文件中读取一个字符串。需要重点注意的是，Python字符串可以是二进制数据，而不是仅仅是文字。

remove()方法
你可以用remove()方法删除文件，需要提供要删除的文件名作为参数

tell()方法,seek（offset [,from]）
文件定位

重命名和删除文件

Python的os模块提供了帮你执行文件处理操作的方法，比如重命名和删除文件。
要使用这个模块，你必须先导入它，然后才可以调用相关的各种功能。
rename()方法：
rename()方法需要两个参数，当前的文件名和新文件名。

```bash
os.rename(current_file_name, new_file_name)
```
下例将重命名一个已经存在的文件test1.txt。
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-

import os
 
# 重命名文件test1.txt到test2.txt。
os.rename( "test1.txt", "test2.txt" )
```

remove()方法
你可以用remove()方法删除文件，需要提供要删除的文件名作为参数。
语法：
```
os.remove(file_name)
```
例子：
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-

import os
 
# 删除一个已经存在的文件test2.txt
os.remove("test2.txt")
```