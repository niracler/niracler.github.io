---
title: 面试题|Python基础(三)
tags:
  - python
  - 面试题
---

### 1 评价一下自己

评价一下自己，最大的优点以及最大的缺点，另外你的人生规划是什么？

我最大的优点是能够好好地做到时间规划

### 2 如果上司交给你一份不属于你份内的工作

你会拒绝or接受它？

当然，这个是要看情况了。我想了解一下你所说的“份内的工作”是什么含义。这是需要具体情况具体分析的，假如是“我做前端的人员，然后需要我去做后端”，这种情况属于是我的相关类型工作虽说是不属于我份内的工作，只要有合理的原因我还是能接受的，但这种情况要是一直持续下去，而我又真的对这种“不属于我份内的工作”

是指“我们做IT的，而上司指派我们去做流水线手工工作做一周”这种程度，不过即使是这种程度假如时间不长而且有足够的补贴的话，还能接受吧。这种情况我是需要向

### 3 当你确信你自己是正确的

但是其他人却不赞同你，你会怎样做？

这种情况，只能说是求同存异了。首先，一般这种问题最经常出现的一种情况就是我们对讨论的问题的理解程度不一样。

### 4 当你成为主要领导

当你成为了主要领导，发现工作压力和强度远远超过了你当初的想象，你会怎么做？

这是很自然的，主要领导是责任的核心。承担责任自然是要承担无法想象的工作压力和强度，是的，无论如何你都是无法想象那种压力和强度的。所以说，只能是不

### 5 你对加班有什么看法

你对加班有什么看法？假如是符合我想要做的事情的目标的话，我除了睡觉吃饭时间，以及每周八个小时的固定休息时间外，我都是可以工作的。假如是那种重复的流水线工作的话，我无法忍受那种加班时间。假如是没有加班费的话。我想我是需要问清楚你们公司的加班情况主要是怎么样的，大概多久加一次班？加班是否有补贴？补贴能有什么程度？这些问题都是要具体实际情况具体分析具体解决的。

### 6 os 和 sys 模块的作用

- os模块 负责程序与操作系统的交互，提供了访问操作系统底层的接口
- sys模块 负责程序与 Python 解析器的交互，提供了一系列的函数和变量，用于操控python的运行时环境。

os常用方法

```python
os.remove() # 删除文件
os.rename() # 文件重命名
os.walk() # 生成目录树下的所有文件名
os.chdir() # 改变目录
os.mkdir/makedirs # 创建目录、创建多级目录
os.listdir() # 列出指定目录的文件
os.getcwd() # 获取当前工作目录
os.path.basename() # 去掉目录路径，返回文件名
os.path.dirname() # 去掉文件名，返回目录路径
os.path.join() # 将分离的各部分组成一个路径名
os.path.split() # 返回 (dirname, basename) 元组
os.path.splitext() # 返回 (filename， extension) 元组
os.path.getatime/ctime/mtime # 分别返回最近访问、创建、修改时间
os.path.getsize() # 返回文件大小
os.path.exists() # 返回文件是否存在
os.path.isabs() # 是否为绝对路径
os.path.isdir() # 是否为目录
os.path.isfile() # 是否为文件
```

<!-- more -->

sys常用方法

```python
sys.argv # 命令行参数列表，第一个元素是程序本身路径
sys.moudules.keys() # 返回所有已导入的模块列表
sys.exc_info() # 获取当前正在处理的异常类
sys.exit() # 退出程序，正常退出时exit(0)
sys.hexversion # 获取python解析程序的十六进制版本值
sys.version # 获取python解析程序的版本信息
sys.maxint # 最大的int值
sys.maxunicode # 最大的unicode值
sys.modules # 返回系统导入的模块字段，key是模块名，value是模块
sys.path # 返回模块的搜索路径
sys.platform # 返回操作系统的平台名称
sys.stdout # 标准输出
sys.stdin # 标准输入
sys.stderr # 错误输出
sys.exc_clear # 清除当前线程所出现的当前的或最近的错误信息
sys.exec_prefix # 返回平台独立的python文件安装位置
sys.byteorder # 本地字节规则的指示器
sys.copyright # 记录python版权相关的东西
sys.api_version # 解析器的C的API版本
```

### 7 如何生成一个随机数

```python
>>> import random
>>> random.random()
0.10174308638634244
>>> random.randint(1, 10000)
8545
```

### 8 如何使用python删除一个文件

使用os.remove()方法

### 9 谈谈你对面向对象的理解

面向对象是一种编程思想，以类的眼光来看待事物的一种方法。将共同的属性和方法的事物封装到同一个类下面。

继承：将多个类的共同属性和方法封装到同一个父类中，然后用这些类类继承这个类的属性和方法

封装：将有共同的属性和方法封装到同一个类值嗯

- 创建类和对象会分别创建二者的名称空间，我们只能用类名```.```或者```obj.```的方式去访问里面的名字，这本身就是一种封装
- 类中把某些属性和方法隐藏起来，只在类的内部使用、外部无法访问，或者留下少量接口供外部访问

多态:Python天生是支持多态的。指的是基类的同一个方法在不同的派生类中有着不同的功能

### 10 面向对象中的继承有什么特点

继承概念的实现方式主要有两类：实现继承、接口继承

- 实现继承是指使用基类的属性和方法而无需额外编码的能力
- 接口继承是指仅使用属性和方法的名称、但是子类必须提供实现的能力(子类重构父类的方法)

### 11 面向对象深度优先和广度优先是什么

Python的类可以继承多个类，Python的类如果继承了多个类，那么其寻找方法的方式有两种：

1. 当类是经典类时，多继承情况下，会按照深度优先的方法查找 PY2
1. 当类是新式类时，多继承情况下，会按照广度优先的方法查找 PY3

总的来说：经典类是纵向查找，新式类是横向查找  
区别：在声明类的时候新式类需要加上 ```object``` 关键字。在Python3中默认全是新式类

### 12 面向对象中super的作用

super是指向父类的指针，在子类实体化的时候，也同时会实体化一个父类对象， 使用super().XXX()就能够调用父类的方法

- 可以用来解决多重继承的问题，直接用类名调用父类方法在使用单继承的时候没问题，但是如果使用多继承，会涉及到查询顺序(MRO)、重复调用(砖石继承)等种种问题
- MRO就是类的方法解析顺序表，其实也就是继承父类方法时的顺序表

### 13 请分析一下下面的输出结果

```python
class A:  
  def __init__(self):  
    print("Enter A")  
    print("Leave A")  
class B(A):  
  def __init__(self):  
    print("Enter B")  
    A.__init__(self)  
    print("Leave B")  
class C(A):  
  def __init__(self):  
    print("Enter C")  
    A.__init__(self)  
    print("Leave C")  
class D(A):  
  def __init__(self):  
    print("Enter D")  
    A.__init__(self)  
    print("Leave D")  
class E(B, C, D):  
  def __init__(self):  
    print("Enter E")  
    B.__init__(self)  
    C.__init__(self)  
    D.__init__(self)  
    print("Leave E")  
E()
```

结果

```python
"Enter E"
"Enter B"
"Enter A"
"Leave A"
"Leave B"
"Enter C"
"Enter A"
"Leave A"
"Leave C"
"Enter D"
"Enter A"
"Leave A"
"Leave D"
"Leave E"
```

### 14 是否使用过 functool 中的函数,其作用是什么

用于修复装饰器

```python

```

### 15 列举面向对象中带双下划线的特殊方法，如__init__, __new__

### 16 如何判断是函数还是方法
