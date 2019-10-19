---
title: Python面试题|Python基础(二)
tags:
    - python
    - 面试题
---

### 36 什么是断言(assert)，有什么应用场景

我没有用过断言(assert)，应该是用来测试或者是抓取错误的

<!-- more -->

### 37 如下 按每个字符串的第一个值,合并 a 和 b 到 c

```python
a = [
    'a,1',
    'b,3,22',
    'c,3,4',
]

b = [
    'a,2',
    'b,1',
    'd,2',
]

# 按每个字符串的第一个值,合并 a 和 b 到 c
c = [
    'a,1,2',
    'b,3,22,1',
    'c,3,4',
    'd,2'
]

```

我的结果

```python

```

### 38 有一个多层嵌套的列表 A=[1,2,[3,4,["434",...]]], 请写一段代码遍历 A 中的每一个元素并打印出来

### 39 a = range(10),a[::-3] 的结果

range(9, -1, -3),为什么是这个？简直莫名奇妙。。

### 40 下面那个命令可以从虚拟环境中退出

A. deactivate  
B. exit  
C. quit  
D. 以上均可  

那肯定是 A 啊，后面那两个是 Python Shell 的退出

### 41 def func(a,b=[]) 这种写法有什什么坑



### 42 如何实现 “1,2,3” 变成 [‘1’,’2’,’3’]

```python

```

### 43 如何实现[‘1’,’2’,’3’]变成[1,2,3]

```python
[int(x) for x in ['1','2','3']]
```

### 44 a = [1,2,3] 和 b = [(1),(2),(3) ] 以及 b = [(1,),(2,),(3,) ] 的区别

```python

```

### 45 如何用一行代码生成[1,4,9,16,25,36,49,64,81,100]

```python
[i*i for i in range(1,11)]
```

### 46 通过代码实现如下进制转换

二进制转换为十进制

```python
v1 = '0b10010'
res = int(v1, 2)
print(res) # 123
```

十进制转换为二进制

```python
v2 = 18
res = bin(v2)
print(res) # '0b10010'
```

八进制转换为十进制

```python
v3 = '0o22'
res = int(v3, 8)
print(res) # 18
```

十进制转换为八进制

```python
v4 = 18
res = oct(v4)
print(res) # '0o22'
```

十六进制转换为十进制

```python
v5 = '0x12'
res = int(v5, 16)
print(res) # 18
```

十进制转换为十六进制

```python
v6 = 18
res = hex(v6)
print(res) # '0x12'
```

### 47 请写一个函数将IP地址转换为一个整数

如 10.3.9.12 转换规则为:

|||  
|---|---|  
|10| 00001010|  
| 3 | 00000011 |  
| 9 | 00001001 |  
| 12 | 00001100 |  

再将以上二进制拼接起来计算十进制结果:00001010 00000011 00001001 00001100 = ?

```python
def get_ip_sum(addr):
    res = [int(x) for x in addr.split(".")]
    return sum(res[i] << [24, 16, 8, 0][i] for i in range(4))

# get_ip_sum("127.0.0.1")
# 2130706433

```

位运算这个神器，我真是到最后都用不好

### 48 Python递归的最大层数

998 究竟是为什么，为什么不是九九六

### 49 按位与，按位或

求2^5

```python
res = 2 ^ 5 # 10 ^ 101 = 111
print(res) # 7
```

求2&5

```python
res = 2 & 5 # 10 & 101 = 000
print(res) # 0
```

### 50 lambda表达式格式以及应用场景

lambda函数也叫匿名函数：为了解决那些功能很简单的需求而设计的一句话函数

lambda的格式为： ```lambda 输入： 结果```

1. 参数可以有多个，用逗号隔开
2. 匿名函数不管逻辑有多复杂，只能写一行，且逻辑执行结束后的内容就是返回值
3. 返回值和正常函数一样，可以是任意数据类型

### 51 pass的作用

pass是空语句，是为了保持程序结构的完整性。pass不做任何事情，一般用作占位符。

### 52 *arg和**kwarg作用

- ```*args``` 代表位置参数，他会接收任意多个参数并把这些参数作为元组传递给函数。
- ```**kwarg``` 代表的关键字参数,返回的是字典,位置参数一定要放在关键字前面

### 53 列举常见的内置函数及其功能

abs()

返回数字的绝对值

map()

根据函数对指定序列做映射，map()函数接收两个参数,一个是函数，一个是可迭代对象,map将传入的函数依次作用到序列的每个元素，并把结果作为新的list返回。Py2是返回list，而Py3是返回迭代器

filter()

该函数hi接收一个函数和一个list(可迭代对象)，这个函数的作用是对每个元素进行判断，返回True 或者 False。能根据判断结果过滤掉不符合条件的元素，返回由符合条件的元素组成的新list

```python
res = list(filter(lambda x : x % 2 == 0 , [6,84,15,34,3,8,4,36,84,8]))
print(res)
# [6, 84, 34, 8, 4, 36, 84, 8]
```

map()与filter()

参数以及返回值的类型都一样，filter是做筛选的，而map是对可迭代对象做操作的

isinstance()与type()

isinstance函数判断一个对象是否是一个已知的类型，类似与type，而区别是type不会认为子类是一个父类类型，不考虑继承关系，而isinstacne考虑继承关系，所以推荐使用isintance??

zip() 拉链函数

将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的迭代器，如果各个迭代器的元素个数不一致，则返回列表长度与最短对象相同

```python
print(list(zip([0,1,3], [5,6,7], ['a','b'])))

# [(0, 5, 'a'), (1, 6, 'b')]
```

reduce()

```python
>>> from functools import reduce
>>> print(reduce(lambda x,y: x+y, [1,2,3,4,5,6]))
21
```

### 54 如何安装第三方模块，以及用过哪些第三方模块

1. pip安装
2. 源码安装

用过的第三方模块，太多了，我不想列举

### 55 常用模块都有哪些

re, os, json, time, 爬虫里面的requests/beautifulsoup4

### 56 re的match和search的区别

- match 尝试从字符串的起始位置匹配一个模式，如果不是起始位置匹配成功的话，match就返回None
- search 是扫描整个字符串并返回第一个成功的匹配

### 57 什么是正则的贪婪匹配

匹配一个字符串没有节制，能匹配多少就去匹配多少，知道没有匹配的为止。

### 58 def func(a,b=[])这种写法有什么坑

例子：

```python
def func(a, b = []):
    b.append(1)
    print(a, b)

func(2)
func(2)
func(2)

# 2[1]
# 2[1,1]
# 2[1,1,1]

```

那个默认参数经过多次执行还是第一次实例化的那个地址

### 59 下面这两种操作有什么区别

```python
a = [1, 2, 3, 4, 5]
b = a
c = a[:]
```

一个是指针指向原来数组的那片内存空间，一个是拷贝，重新申请一片内存空间再拷贝进去

### 60 如何在函数中设置一个全局变量

使用 global 语句啊

### 61 logging 模块的作用，以及应用场景

logging模块定义的函数和类为应用程序和库的开发实现了一个灵活的事件日志系统

作用 可以了解程序运行情况是否正常，在程序出现故障时快速定位出错地方以及故障分析

### 62 请用代码解答实现stack

- Stack()创建一个新的空栈
- push()添加一个新元素item到栈顶
- pop()弹出栈顶元素
- is_empty()判断栈顶是否为空
- size()返回栈顶元素个数

```python
class Stack():
    def __init__(self):
        self.items = []
    def push(self, item):
        self.items.append(item)
    def pop(self):
        return self.items.pop()
    def peek(self):
        return self.items[len(self.items)-1]
    def is_empty(self):
        return not self.items
    def size(self):
        return len(self.items)

>>> stack = Stack()
>>> stack.push('H')
>>> stack.push('E')
>>> stack.push('L')
>>> stack.pop()
'L'
>>> stack.peek()
'E'
>>> stack.is_empty()
False
>>> stack.size()
2
```

### 63 简述生成器，迭代器，可迭代对象，以及应用场景

迭代器

生成器

可迭代对象

装饰器