#  Python核心编程

## 一 高级语法

### 1. 模块的重新导入

```python
若模块在导入后修改了,可使用 reload重新导入.
from imp import *
reload(模块名称 reload重新导入.)
```

### 2. 模块循环导入问题

```json
a模块需要b模块中的内容,同时b模块需要a模块中的内容,a import b 且 b import a,此时陷入循环导入的问题.
```

### 3.  == 与 is

```python
== 用来判断值是否相等
is 用来判断两个引用是否指向同一个对象(引用比较)
```

### 4. 深拷贝与浅拷贝

```python
浅拷贝: 地址引用
深拷贝:  
import copy
c = copy.deepcopy(a) # c深拷贝a
e = copy.copy(a) # 
```

### 5. 私有化

```python
xx:公有变量
_x:私有化属性或方法,from somemodule import * 禁止导入,类对象和子类可以访问
__xx__:魔法方法
__xx:私有属性/方法   类外不可直接使用/子类不能直接继承与使用
xx_:用于避免与系统关键字冲突
```

### 6. property

```python
class Test(object):

    def __init__(self):
        self.__num = 100

    def setNum(self,new_num):
        print("---setter----")
        self.__num = new_num

    def getNum(self):
        print("-----getter----")
        return self.__num

    num = property(getNum,setNum)

t = Test()
#t.num = 200 #相当于调用 t.setNum(200)
t.num = 200  #相当于调用 t.getNum()
print(t.num)
#注意点:
# t.num 是调用getNum()还是setNum()要根据实际场景使用
# 如果给t.num赋值,name就一定调用setNum()
# 如果是获取t.num的值,name就一定调用getNum()
# property的作用: 相当于吧方法进行封装,开发者在对属性设置数据的时候更加方便

#另一种方式
class Test(object):

    def __init__(self):
        self.__num = 100
    @property
    def num(self):
        print("-----getter----")
        return self.__num
    
    @num.setter
    def num(self,new_num):
        print("---setter----")
        self.__num = new_num

t = Test()
#t.num = 200 #相当于调用 t.setNum(200)
t.num = 200  #相当于调用 t.getNum()
print(t.num)
#注意点:
# t.num 是调用getNum()还是setNum()要根据实际场景使用
# 如果给t.num赋值,name就一定调用setNum()
# 如果是获取t.num的值,name就一定调用getNum()
# property的作用: 相当于吧方法进行封装,开发者在对属性设置数据的时候更加方便
```

### 7. 迭代器

```python
1.可迭代对象
以可以直接使用for循环的数据类型有以下几种:
一类是集合数据类型:如list,tuple,dict,set,str等;
一类是generator,包括生成器和带yield的generator function
这些可以直接作用于for循环的对象统称为可迭代对象:iterable
  
2.判断是否可以迭代:
 1.使用for循环
 2.可以使用isinstance()判断一个对象是否是iterable对象
 from collections import Iterable
 isinstance([],Iterable) #返回true
  
3.迭代器
可以被next()函数调用并不断返回下一个值得对象称为迭代器:Iterator
可以使用isinstance()判断一个对象是否是iterable对象
from collections import Iterator
isinstance([],Iterator)  #False

4.iter()
生成器都是Iterable对象,但list,dict,str虽然是Iterable,去不是Iterator
把list.dict,str等Iterable变成Iterator可以使用iter()函数
isinstance(iter([]),Iterator) #True
```

### 8. 闭包

```python
定义: 在函数内部再定义一个函数,并且这个函数用到了外边函数的变量,name这个函数以及用到的一些变量称为闭包.
示例:
def test(number):

    def test_in(number_in):
        print("in test_in 函数,number_in is %d"%number_in)

    return test_in
# test(100)
# ret = test(100)
# ret()

闭包应用:
  
def line_conf(a,b):
    
    def line(x):
        return a*x + b
    return line

line1 = line_conf(1,1)
line2 = line_conf(4,5)

print(line1(5))
print(line2(5))
```

### 9. 装饰器 

```python
1. 原理:
def w1(func):
    def inner():
        print('正在验证权限...')
        func()
    return inner

def f1():
    print("------f1--------")

def f2():
    print("------f2--------")

f1 = w1(f1)
f1()

2.升级版

def w1(func):
    def inner():
        print('正在验证权限...')
        func()
    return inner
@w1  #语法糖
def f1():
    print("------f1--------")

@w1
def f2():
    print("------f2--------")

f1()

3. 多个装饰器
#定义函数：完成包裹数据
def makeBold(fn):
    def wrapped():
        return "<b>" + fn() + "</b>"
    return wrapped

#定义函数：完成包裹数据
def makeItalic(fn):
    def wrapped():
        return "<i>" + fn() + "</i>"
    return wrapped

@makeBold
def test1():
    return "hello world-1"

@makeItalic
def test2():
    return "hello world-2"

@makeBold   #后装饰
@makeItalic #先装饰
def test3():
    return "hello world-3"

print(test1())
print(test2())
print(test3())

# 运行结果:
# <b>hello world-1</b>
# <i>hello world-2</i>
# <b><i>hello world-3</i></b>

4. 多个装饰器的运行顺序
def w1(func):
    print("装饰1.........")
    def inner():
        print('正在验证权限1...')
        func()
    return inner
def w2(func):
    print("装饰2.........")
    def inner():
        print('正在验证权限2...')
        func()
    return inner
@w1
@w2
def f1():
    print("------f1--------")

f1()
# 运行结果:
# 装饰2.........
# 装饰1.........
# 正在验证权限1...
# 正在验证权限2...
# ------f1--------

5. 对有参数,无参数函数的装饰
有参数函数的装饰:
def deco_test(func):
    print("----func1----")
    def inner(*args,**kwargs):#如果x,y没有定义,那么会导致调用失败
        print("----inner1-----")
        func(*args,**kwargs) #如果没有把x,y当做实参传递,也会导致调用失败
        print("----inner2-----")

    print("----func2----")
    return inner


@deco_test
def test(a,b,c):
    print("----a = %d,b = %d,c= %d"%(a,b,c))
test(11,22,33)

6. 对有返回值函数的装饰
def deco_test(func):
    print("----func1----")
    def inner():
        print("----inner1-----")
        return func() #对有返回值的函数装饰时 return 
        print("----inner2-----")

    print("----func2----")
    return inner

@deco_test
def test():
    print("----a = %d,b = %d,c= %d")
    return "haha"
ret = test()
print("test return value is %s"%ret)

7.通用装饰器(有无参数,有无返回值)

def deco_test(func):

    def inner(*args,**kwargs):
        print("----记录日志----")
        return func(*args,**kwargs) #对有返回值的函数装饰时 return

    return inner

@deco_test
def test():
    print("----a = %d,b = %d,c= %d")
    return "haha"

@deco_test
def test2():
    print("-----test2-----")

@deco_test
def test3(a):
    print("----a = %d"%a)

ret = test()
print("test return value is %s"%ret)

a = test2()
print("test2 return value is %s"%a)

test3(10)

8. 带参数的装饰器
def func_arg(arg):
    def deco_test(func):

        def inner():
            print("----记录日志---arg = %s"%arg)
            if arg == 'heihei':
                func()
                func()
            else:
                func()
        return inner
    return deco_test
# 1.执行 func_arg("heihei") 函数 结果是deco_test函数的引用
# 2 @deco_test
# 3.使用 @deco_test对test进行装饰
@func_arg("heihei")
def test():
    print("----test---")

@func_arg("haha")#带参数的装饰器 运行时可具有不同功能
def test2():
    print("----test---")
test()
test2()

```

### 10. 作用域 

```python
Python访问变量的查找顺序:
LEGB规则:
Locals-> Enclosure function-> Globals-> Builtins
```

### 11. 动态添加属性与方法 

```python
import types
class Person(object):

    def __init__(self,new_name,new_age):
        self.name = new_name
        self.age = new_age

    def run(self):
        print("%s正在跑步..."%self.name)

def eat(self):
    print("---%s正在吃饭.."%self.name)

#给类添加静态方法
@staticmethod
def bark():
    print("static method")

Person.bark = bark
Person.bark()

#添加类方法
@classmethod
def flight(cls):
    print("--class method---")

Person.flight = flight
Person.flight()

laowang = Person('老王',40)
print(laowang.name)
print(laowang.age)
laowang.run()

laowang.address = '北京市朝阳区' #添加新对象属性
print(laowang.address)

#给具体的实例添加新方法
# 必须先 import types 模块
laowang.eat = types.MethodType(eat,laowang)
laowang.eat()

laozhao = Person('老赵',20)
#print(laozhao.address)

Person.num = 200 #添加类属性
print(laozhao.num)
print(laowang.num)
```

### 12.  slots的作用

```python
# python允许在定义类的时候,定义一个特殊的__slots__变量,来限制该class示例能添加的属性

class Person(object):
  __slots__ = ("name","age") #允许只能添加name与age属性
  
# 注意点:
#__slots__定义的属性仅对当前实例起作用,对继承的子类是不起作用的.
```

### 13. 生成器

```python
# python中一边循环一边计算的机制,称为生成器,generator.可以使用next()函数获取生成器的下一个返回值.
# 示例:
    b = (x*2 for x in range(10))
    next(b)
# 创建生成器方法2:
# 斐波那契数列示例:
    def  fib():
    print("----start----")
    a, b = 0, 1
    for i in range(5):
        #print(b)
        print("-----1----")
        yield b #创建了生成器对象
        print("------2-----")
        a,b = b,a+b
        print("------3------")
    print("-----stop----")

    for x in fib():
        print(x)
        
# 还可以使用以下两种方法获取
a = fib()
next(a)
ret = a.__next__() 
print(ret)

# send()示例
def fib2():
    i = 0
    while i < 5:
        temp = yield i #若yield前有变量
        print(temp)
        i += 1
t = fib2()
print(t.__next__()) #或 next(t)
print(t.__next__()) #或 next(t)
print(t.__next__()) #或 next(t)

t.send('haha') #不能创建完对象就send,先使用__next__()进行调用
# 或者
t.send(None) 

# 生成器完成多任务(协程):
def task1():
    while True:
        print("---1---")
        yield None

def task2():
    while True:
        print("---2---")
        yield None

t1 = task1()
t2 = task2()

while True:
    t1.__next__()
    t2.__next__()
```

### 14. 类装饰器   

```python
# 装饰器函数其实是这样一个接口的约束,它必须接受一个callable对象作为参数,然后返回一个callable对象.在python中一般callable对象都是函数,但也有例外.只要某个对象重写了__call__()方法,那么这个对象就是callable的.

class Test(object):
    def __call__(self):
        print("call me")
t = Test()
t() #call me

# 类装饰器demo
class Test(object):

    def __init__(self,func):
        print("初始化")
        print("function name is %s" %func.__name__)
        self.__func = func

    def __call__(self, *args, **kwargs):
        print("装饰器中的方法")
        self.__func()
@Test
def test():
    print("----test----")

test()
```

### 15. 元类(类的类)   

```python
# 使用type创建类
type(类名,由父类名称组成的元组(针对继承的情况,可以为空),包含属性的字典(名称和值))
# 示例:
def eat(self):
    print("正在吃饭")
    
Person = type("Person",(),{"name":"assasin","eat":eat})
laowang = Person()
laowang.name
laowang.eat()
# 验证是否元类
Person.__call__  # type

```

### 16. metaclass  

```python
def upper_attr(future_class_name,future_class_parents,future_class_attr):
    # 遍历属性字典,把不是__开头的属性名字变为大写
    newAttr = {}
    for name,value in future_class_attr.items():
        if not name.startswitch("__"):
            newAttr[name.upper()] = value
            
    return type(future_class_name,future_class_parents,newAttr)
# python2
class Foo(object):
    __metaclass__ = upper_attr #设置Foo类的元类是upper_attr
    bar = 'bip'
# python3
class Foo(object,metaclass=upper_attr):
    bar = 'bip'
    
print(hasattr(Foo,'bar'))
print(hasattr(Foo,'BAR'))

f = Foo()
print(f.BAR)
```

### 17. 小整数对象池  

```python
# python为了优化性能,使用小整数对象池,避免整数频繁申请和销毁内存空间.
# python对小整数的定义是[-5,257],这些证书对象是提前建立好的,不会被垃圾回收.所有位于这个范围内的整数使用的都是同一对象. 
# 即 a = 100 b = 100  id(a) == id(b)

# 每一个大整数,均创建一个新的对象.

# inter机制:使用引用计数去维护合适释放内存空间.
# 总结:
# 小整数[-5,257)公用对象,常驻内存;
# 单个字符串公用对象,常驻内存;
# 单个单词,不可修改,默认开启inter机制,公用对象,引用计数为0,则销毁.
```

### 18. 引用计数  

```python
# Garbage Collection(GC垃圾回收机制)
# python中每一个东西都是对象,它们的核心是一个结构体:PyObject
typedef struct_object{
    int ob_refcnt;
    struct_typeobject *ob_type;
} PyObject
# PyObject是每一个对象都有的内容,其中ob_refcnt就是作为引用计数.当每一对象都有新的引用时,它的ob_refcnt就会增加,当引用计数的对象被删除,它的ob_refcnt就会减少.

# 优点:
# 简答
# 实时性:一旦没有引用,内存就直接释放了.不用想其他机制等到特定时机.实时性还带来一个好处是:处理回收内存的时间分摊到了平时.

# 缺点:
# 维护引用计数消耗资源
# 循环引用

# 隔代回收 
# python以引用计数为主,标记-清除和隔代收集两种机制为辅的策略,完成垃圾回收.

```

### 19. Garbage Collection(GC)  

```python
import gc 并且 is_enbale() = True 才会启动自动垃圾回收
gc.set_debug(flags) # 设置gc的debug日志,,一般设置为 gc.DEBUG_LEAK
gc.get_threshold() # 获取的gc模块中自动执行垃圾回收的频率
gc.collection([generation]) # 显式进行垃圾回收,可以输入参数,0代表只检查第一代的对象,1代表检查一,二代的对象,2代表检查一,二,三代的对象,如果不传参数,执行一个full collection,也就是等于传2,返回不可达对象的数目.
gc.set_threshold(threshold0,threshold1,threshold2) #设置自动执行垃圾回收的频率
gc.get_count() # 获取当前自动执行垃圾回收的计数器,返回一个长度为3的列表
gc.garbage #保存被清理的对象 列表

1.导致引用计数 + 1 的情况
对象被创建,例如 a = 23
对象被引用,例如 b = a
对象被作为参数,传入到一个函数中,例如 func(a)
对象作为一个元素,存储在容器中,例如 list = [a,a]

2.导致引用计数 - 1 的情况
对象的别名被显式销毁,例如 del a
对象的别名被赋予新的对象,例如 a = 24
一个对象离开它的作用域,例如f函数执行完毕时,func函数中的局部变量(全局变量不会)
对象所在的容器被销毁,祸从容器中删除对象

3. 查看一个对象的引用计数
import sys
a = 'hello world'
sys.getrefcount(a)

4. 循环引用导致内存泄漏
import gc

class ClassA(object):
    def __init__(self):
        print("object born ,id:%s"%str(hex(id(self))))

def f2():
    while True:
        c1 = ClassA()
        c2 = ClassA()
        c1.t = c2
        c2.t = c1
        del c1
        del c2

gc.disable() #关闭gc功能 gc.enable() 开启
f2()
```

### 20. 内建属性  

```python
1. 常用专有属性
__init__ #构造初始化函数  创建实例后,赋值时使用,__new__后
__new__  #生成实例所需属性 创建实例时
__class__ #实例所在类 实例 __class__
__str__ #实例字符串表示,可读性 print(类实例)
__repr__ #实例字符串表示,准确性 类实例或者print(repr(类实例))
__del__ #析构 删除实例
__dict__ #实例自定义属性 vars(实例,__dict__)
__doc__ #类文档,子类不继承 help(类或实例)
__getattribute__ #属性访问拦截器 访问实例属性时
__bases__ #累的所有父类构成元素 类名.__bases__

__getattribute__示例:
    class Test(object):
        
        def __init__(self,subject1):
            self.subject1 = subject1
            self.subject2 = 'cpp'
        # 访问属性拦截器时,打log日志
        def __getattribute__(self,obj):
            if obj == 'subject1':
                    print('log subject1')
                    return 'redirect python'
            else: #注释掉这两行,将找不到subject2 
                return object.__getattribute__(self,obj)
            
         def show(self):
            print('this is test')
            
    s = Test('python')
    print(s.subject1)
    print(s.subject2)
```

### 21. 内建函数(方法)  

```python
range(start,stop,step) 
xrange()
map() # 根据提供的函数对指定序列做映射
map(lambda x:x*x,[1,2,3]) #1,4,9
filter() #对指定序列进行过滤操作
filter(lambda x: x%2,[1,2,3,4]) # 1,3
reduce() #对参数序列中元素进行累计
reduce(lambda x,y:x+y,[1,2,3,4]) #10
reduce(lambda x,y:x+y,[1,2,3,4],5) #15
reduce(lambda x,y:x+y,['cc','bb','cc'],'dd') #'ddaabbcc'
sorted() #排序
```

### 22. 集合  

```python
# 集合与列表,元组相似,可以存储多个数据,但这些数据是不重复的.
# 集合对象还支持union(联合),intersection(交),difference(差)和sysmetric_difference(对称差集)等数学运算.
#假设 a,b两个集合
a&b 交集
a|b 并集
a-b 差集
a^b 对称差集
```

### 23. functools   

```python
import functools
dir(functools)

partial(偏函数)
def showarg(*args,**kwargs):
     print(args)
     print(kwargs)
p1 = functools.partial(showarg,1,2,3)
p1()
p1(4,5,6)
p1(a='python',b='assasin')

functools.wraps()
```

### 24. 常见模块   

```python
# 常用标准库
import builtins # 内建函数默认加载
import os # 操作系统接口
import sys # python自身运行环境
import functools # 常用工具
import json # 编码与解码json对象
import logging # 记录日志 调试
import multiprocessing # 多进程
import threading # 多线程
import copy # 拷贝
import time # 时间
import datetime # 日期和时间
import calendar # 日历
import hashlib # 加密算法
import random # 随机数
import re # 字符串正则匹配
import socket # 标准的BSD Socket API
import shutil # 文件与目录管理
import glob # 基于文件通配符搜索

#扩展库
import requests
import urllib # 基于http的高级库
import scrapy # 爬虫
import beautifulsoup4 HTML/xml# 解析器
import celery # 分布式任务调度模块
import redis # 缓存
import Pillow(PIL) # 图像处理
import xlsxwriter # 仅写Excel功能,支持xlsx
import xlwt # 仅写Excel功能,支持xls,2013或更早版本
import xlrd # 仅读Excel功能
import elasticsearch # 全文搜索引擎
import pymysql # 数据库链接库
import mongoengine/pymongo mongodbpython# 接口
import matplotlib # 画图
import numpy/scipy # 科学计算
import django/tornado/flask # web框架
import xmltodict # xml转dict
import SimpleHtTPServer # 简单的Http server不适用web框架
import gevent # 基于协程的python网络库
import fabric # 系统管理
import pandas # 数据处理库
import scikit-learn # 机器学习裤
# hashlib
import hashlib
m = hashlib.md5()
m.update(b'assasin')
print(m.hexdigest())
# python -m http.server 8888 启动http 服务
```

## 二 网络编程(进程--线程)

### 25. pdb调试  

```python
1. python -m pdb some.py 
# 命令:
# l list显示当前代码
# n next向下执行一行代码
# c continue 继续执行代码
# b 7 break 执行至第7行断点 设置断点 
# clear 删除断点
# p print打印一个变量的值
# a args打印所有形参数据
# s step 进入到一个函数
# q quit 退出调试
# r return 快速执行到函数最后一行
2. import pdb
   pdb.set_trace() #当程序执行到pdb.set_trace()位置停下调试
```

### 26. pep8编码规则  

```python
# 编码风格
每级缩进使用4个空格;
最大行宽建议79个字符
顶层函数与顶层类之前两行空行
函数之间使用一行空行隔开
模块 标准->第三方->自定义
......
```

### 27. fork创建子进程  

```python
# 正在运行着的代码,就成为进程.
import os
import time
ret = os.fork() #主进程 ret > 0 |子进程 ret = 0
if ret == 0:
    while True:
        print("---1----")
        time.sleep(1)
else:
    while True:
        print("---2----")
        time.sleep(1)
        
# os.getpid() 获取当前进程值编号
# os.getppid() 获取当前进程的父级进程值编号

```

### 28. 多进程修改全局变量  

```python
import os
import time

g_num = 100

ret = os.fork()
if ret == 0:
    print("----process-1-----")
    g_num += 1
    print("---process-1 g_num=%d"%g_num) # 101
else:
    time.sleep(3)
    print("----process-2-----")
    print("---process-2 g_num=%d"%g_num) # 100
```

### 29.  多次fork   

```python
import os
import time

ret = os.fork()
if ret == 0:
    print("-----1-----")
else:
    print("-----2-----")
    
ret = os.fork()
if ret == 0:
    print("-----11-----")
else:
    print("-----22-----")
   
```

### 30. Process创建子进程  

```python
from multiprocessing import Process # Process对象
import time

def test():
    while True:
        print("----test-----")
        time.sleep(1)

p = Process(target=test)
p.start() # 让该进程开始执行test函数中的代码
p.join()  # 堵塞
while True:
    print("---main----")
    time.sleep(1)
    
# Process类常用方法:    
  p.join([timeout]) # 等待子进程停止后,join执行 堵塞
  p.is_alive() #判断进程实例是否还在执行
  p.run() #如果没有给定target参数,对这个对象调用start方法时,就将执行对象中run方法
  p.terminate() # 不管任务是否完成,立即终止.
# Process类常用属性:
  name #当前进程实例别名
  pid #当前进程实例的PID值
```

### 31. process创建子进程  

```python
from multiprocessing import Process
import time
import os
class Process_Class(Process):

    def __init__(self,interval):
        Process.__init__(self)
        self.interval = interval

    #重写Process类的run方法
    def run(self):
        print("子进程(%s),开始执行,父进程为(%s)"%(os.getpid(),os.getppid()))
        t_start = time.time()
        time.sleep(self.interval)
        t_stop = time.time()
        print("(%s)执行结束,耗时%0.2f秒"%(os.getpid(),t_stop - t_start))

if __name__=="__main__":
    t_start = time.time()
    print("当前程序进程(%s)"%os.getpid())
    p1 = Process_Class(2)
    #如果没有给定target参数,对这个对象调用start方法时,就将执行对象中run方法
    p1.start()
    p1.join()
    t_stop = time.time()
    print("(%s)执行结束,耗时%0.2f秒"%(os.getpid(),t_start - t_start))

```

### 32. 进程池  

```python
from multiprocessing import Pool
import os
import time


def worker(num):
    for i in range(5):
        print("pid = %d,num = %d"%(os.getpid(),num))
        time.sleep(1)

pool = Pool(3) #最大容纳3个进程一起执行

for i in range(10):
    print("----%d----"%i)
    #向进程池中添加任务,若超过,不会导致进不去,添到进程池中的任务
    # 若还没执行的话,那么会等待进程池中的进程完成一个任务后,会自动去用
    #刚刚进程完成当前新任务
    pool.apply_async(worker,(i,)) #以元组形式传参数
    pool.apply(worker,(i,)) #堵塞方式
print("----start----")
pool.close() #关闭进程池,不能再添加新任务
pool.join() #主进程创建/添加任务后,主进程默认不会等待进程池中的
            # 任务执行完毕后才结束,而是主进程任务结束后,立即结束
            # 若无join(),会导致进程池中的任务不会执行

print("----end-----")
```

### 33. 进程间通讯 Queue  

```python
# Queue 实例
from multiprocessing import Queue

q = Queue(3) #初始化队列,最大了一接收3条put消息 无参数表示不限量
q.put("消息1") # put(数据类型不限)
q.put("消息2")
print(q.full()) #False
q.put("消息3")
print(q.full()) # True 判断队列是否已满
print(q.empty()) # 判断队列是否为空
print(q.get()) #获取消息
print(q.get_nowait()) # 不等待get
print(q.put_nowait()) # 不等待put

try:
    q.put("消息4",True,2)
except:
    print("消息队列已满,现有消息数量:%s"%q.qsize())

# 进程间的通讯

from multiprocessing import Process,Queue
import os,random,time

def reader(q):
    for value in ['A','B','C']:
        print("put %s to queue "%value)
        q.put(value)
        time.sleep(random.random())

def write(q):
    while True:
        if not q.empty():
            value = q.get(True)
            print("Get %s from queue"%value)
            time.sleep(random.random())
        else:
            break

if __name__ == "__main__":
    q = Queue()
    pw = Process(target=write,args=(q,))
    pr = Process(target=reader,args=(q,))
    #启动子进程 写入
    pw.start()
    #等待pw结束
    pw.join()
    #启动子进程 读取
    pr.start()
    pr.join()
    # pr进入死循环 无法等待其结束,强行终止
    print(" ")
    print("所有数据写入并读取完毕")


```

### 34. 进程池中的queue 

```python
from multiprocessing import Manager,Pool #修改import中的Queue为Manager
import os,time,random

def reader(q):
    print("reader启动(%s),父进程为%s"%(os.getpid(),os.getppid()))
    for i in range(q.qsize()):
        print("reader 从queue中获取到消息:%s"%q.get(True))

def write(q):
    print("write启动(%s),父进程为%s" % (os.getpid(), os.getppid()))
    for i in "dongGe":
        q.put(i)

if __name__== "__main__":
    print("(%s)start"%os.getpid())
    q = Manager().Queue() #使用manager中的queue初始化
    po = Pool()
    #使用阻塞式模式创建进程,这样就不需要在reader中使用死循环了,
    # 可以让write完全执行完成后,在使用read
    po.apply(write,(q,))
    po.apply(reader,(q,))
    po.close()
    po.join()
    print("(%s)end "%os.getpid())
```

### 35. 多进程拷贝文件

```python
from multiprocessing import Pool
import os

# 完成copy一个文件的功能
def copy_file(filename,old_folder,new_folder):
    fr = open(old_folder + "/" + filename)
    fw = open(new_folder + "/" + new_folder,"w")

    content = fr.read()
    fw.write(content)

    fr.close()
    fw.close()

def main():

    #0. 获取用户要拷贝的文件夹名称
    old_folder_name = input("请输入要拷贝的文件夹名称:")
    # 1. 创建一个文件夹
    new_folder_name = old_folder_name + '-复件'
    #print(new_folder_name)
    os.mkdir(new_folder_name)
    # 2. 获取temp文件夹中所有文件名
    all_files = os.listdir(old_folder_name)
    # 3. 多进程拷贝temp文件夹所有文件到新文件夹中
    pool = Pool(5)
    for file in all_files:
        pool.apply_async(copy_file,args=(file,old_folder_name,new_folder_name))

    pool.close()
    pool.join()


if __name__=="__main__":
    main()

    
# 升级  增加拷贝进度 计数统计


from multiprocessing import Pool,Manager
import os

# 完成copy一个文件的功能
def copy_file(filename,old_folder,new_folder,queue):
    fr = open(old_folder + "/" + filename)
    fw = open(new_folder + "/" + new_folder,"w")

    content = fr.read()
    fw.write(content)

    fr.close()
    fw.close()

    queue.put(filename)

def main():

    #0. 获取用户要拷贝的文件夹名称
    old_folder_name = input("请输入要拷贝的文件夹名称:")
    # 1. 创建一个文件夹
    new_folder_name = old_folder_name + '-复件'
    #print(new_folder_name)
    os.mkdir(new_folder_name)
    # 2. 获取temp文件夹中所有文件名
    all_files = os.listdir(old_folder_name)
    # 3. 多进程拷贝temp文件夹所有文件到新文件夹中
    pool = Pool(5)
    queue = Manager().Queue()
    for file in all_files:
        pool.apply_async(copy_file,args=(file,old_folder_name,new_folder_name,queue))

    #pool.close()
    #pool.join()
    num = 0
    allNum = len(all_files)
    while True:
        queue.get()
        num += 1
        copy_rate = num/allNum
        print("\r拷贝的进度是:%.2f%%"%(copy_rate*100),end="")
        if num == allNum:
            break
    
    print("\n已完成文件拷贝")    


if __name__=="__main__":
    main()
```

### 36. 线程 threading 

```python
# 多线程示例
import threading #或 from threading import Thread
import time

def saySorry():
    print("我错了,请你原谅....")
    time.sleep(1)

if __name__=="__main__":
    for i in range(5):
        t = threading.Thread(target=saySorry)
        t.start()


```

### 37. thread子类创建多线程 

```python
import threading
import time

class MyThread(threading.Thread):

    def run(self):
        for i in range(5):
            time.sleep(1)
            msg = "i am "+self.name + " @ "+str(i)
            print(msg)

if __name__=="__main__":
    t = MyThread()
    t.start()

```

### 38. 线程的执行顺序

```python
import threading
import time

class MyThread(threading.Thread):

    def run(self):
        for i in range(5):
            time.sleep(1)
            msg = "i am "+self.name + " @ "+str(i)
            print(msg)
            
    def test():
        for i in range(5):
         t = MyThread()
    	 t.start()   
  
if __name__=="__main__":
    test()
    
# 由操作系统的调度指令决定
```

### 39.  线程共享全局变量

```python
from threading import Thread
import time

g_num = 100

def wrok1():
    global g_num
    for i in range(3):
        g_num += 1
    print("----in work1 g_num is %d"%g_num) # 103 

def wrok2():
    global g_num
    print("----in work2 g_num is %d"%g_num) # 103

print("----线程创建之前 g_num is %d"%g_num) # 100
t1 = Thread(target=wrok1)
t1.start()

time.sleep(1) #延迟 保证t1线程完成

t2 = Thread(target=wrok2)
t2.start()

# 线程之间共享全局变量
# 问题: 全局变量被修改
# 解决多线程对共享数据修改出错:
# 让某一线程完成任务后再换另一线程
# 互斥锁
```

### 40. 线程间互斥锁

```python
from threading import Thread,Lock
import time

g_num = 100

def wrok1():
    global g_num
    mutex.acquire() # 上锁
    for i in range(1000000):
        g_num += 1
    mutex.release() # 解锁

    print("----in work1 g_num is %d"%g_num)

def wrok2():
    global g_num
    mutex.acquire()
    for i in range(1000000):
        g_num += 1
    mutex.release()
    print("----in work2 g_num is %d"%g_num)

print("----线程创建之前 g_num is %d"%g_num)

#创建互斥锁 默认没有上锁
mutex = Lock()
t1 = Thread(target=wrok1)
t1.start()

t2 = Thread(target=wrok2)
t2.start()

#结果
----线程创建之前 g_num is 100
----in work1 g_num is 1000100
----in work2 g_num is 2000100
```

### 41. 升级加锁的位置

```python
# 在最小执行单元内加锁
from threading import Thread,Lock
  import time
  
  g_num = 0
  
  def wrok1():
      global g_num
      for i in range(1000000):
          mutex.acquire(blocking=True,timeout=-1) # 加锁 默认堵塞状态 可设置为False |timeout默认-1
                                                  # 超时时间 过后会取消上锁
          g_num += 1
          mutex.release() # 解锁
  
      print("----in work1 g_num is %d"%g_num)
  
  def wrok2():
      global g_num
      for i in range(1000000):
          mutex.acquire()
          g_num += 1
          mutex.release()
      print("----in work2 g_num is %d"%g_num)
  
  print("----线程创建之前 g_num is %d"%g_num)
  
  #创建互斥锁 默认没有上锁
  mutex = Lock()
  t1 = Thread(target=wrok1)
  t1.start()
  
  t2 = Thread(target=wrok2)
  t2.start()

```

### 42. 线程间局部变量

```python
from threading import Thread,Lock
import threading
import time

def wrok1():
    name = threading.current_thread().name #当前进程名称
    print("-----thread name is %s"%name)
    g_num = 100
    if name == 'Thread-1':
        g_num += 1
    else:
        time.sleep(2)
    print("----thread name is %s, g_num is %d"%(name,g_num))

#创建互斥锁 默认没有上锁
t1 = Thread(target=wrok1)
t1.start()

t2 = Thread(target=wrok1)
t2.start()

# 结果 局部变量互不影响
-----thread name is Thread-1
----thread name is Thread-1, g_num is 101
-----thread name is Thread-2
----thread name is Thread-2, g_num is 100
```

### 43. 同步应用

```python
# 同步就是协同步调,按照预定的先后次序进行运行
```

### 44. 生产者与消费者模式

```python
from queue import Queue
import threading
import time

class Producer(threading.Thread):

    def run(self):
        global queue
        count = 0
        while True:
            if queue.qsize() < 1000:
                for i in range(100):
                    count = count + 1
                    msg = "生成产品"+str(count)
                    queue.put(msg)
                    print(msg)
            time.sleep(0.5)


class Consumer(threading.Thread):

    def run(self):
        global queue
        while True:
            if queue.qsize() > 100:
                for i in range(3):
                    msg = self.name + '消费了' + queue.get()
                    print(msg)
            time.sleep(1)


if __name__ == "__main__":
    queue = Queue()
    for i in range(500):
        queue.put("初始化产品:" + str(i))

    for i in range(2):
        p = Producer()
        p.start()
    for i in range(5):
        c = Consumer()
        c.start()
```

### 45. 异步

```python
from multiprocessing import Pool
import time
import os

def test():
    print("---进程池中的进程---pid=%d,ppid=%d--"%(os.getpid(),os.getppid()))
    for i in range(3):
        print("----%d---"%i)
        time.sleep(1)
    return "hahah"

def test2(args):
    print("---callback func--pid=%d"%os.getpid())
    print("---callback func--args=%s"%args)

pool = Pool(3)
pool.apply_async(func=test,callback=test2)

#异步的理解：主进程正在做某件事情，突然　来了一件更需要立刻去做的事情，
#那么这种，在父进程去做某件事情的时候　并不知道是什么时候去做，的模式　就称为异步
while True:
    time.sleep(1)
    print("----主进程-pid=%d----"%os.getpid())
```

### 46. TCP/IP

```python
# 四层: 链路层(网络接口层)->网络层(国际层)->传输层->引用层
# 七层: 物理层->数据链路层->网络层->传输层->会话层->表示层->应用层
```

### 47. 端口  

```python
端口号: 0---65535
知名端口号(0-1023):
    HTTP -> 80
    FTP  -> 21
    SSH -> 22
    HTTPS -> 443
动态端口号:
    Redis -> 6379
    Mysql -> 3306
    SQLserver -> 1433
    Mongodb -> 27017
    Memcache -> 11211
```

### 48. Socket 

```python
# 创建一个TCP socket
import socket
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
# 创建一个UDP socket 
import socket
s = socket.socket(socket.AF_INET,socket.SOCK_DGRAM)

# 示例:
from socket import *
# 1. 创建套接字
udpSocket = socket(AF_INET,SOCK_DGRAM)
# 2. 设置接收方地址
sendAddr = (b'192.168.1.107',8080) #python3 + b udp每次都需要接收方的IP与port
# 绑定端口
udpSocket.bind(sendAddr) #绑定端口 元组
# 3. 获取要发送的数据
sendData = input("请输入要发送的数据")
# 4. 发送数据
udpSocket.sendto(sendData.encode("utf-8"),sendAddr)
# 接收数据
recvData = udpSocket.recvfrom(1024) # 1024表示本次能接收的最大字节数 元组形式
content,recvInfo = recvData # 元组解包
print(content.decode("gb2312")) # 解码
# 5. 关闭套接字
udpSocket.close()

# 统一操作系统中端口不允许相同,如果某一端口正在使用,那么在这个进程释放这个端口之前,其他进程不能使用改端口.原因是:端口用来区分一个进程,如果相同,那么就不能把数据发送给正确的进程,因此不允许相同.
```

### 49. 多线程聊天 

```python
from socket import *
from threading import Thread

# 接收数据 打印
def recvData():
    recvInfo = udpSocket.recvfrom(1024)
    print(">>%s:%s"%(str(recvInfo[1]),recvInfo[0]))

# 发送数据 获取用户输入
def sendData():
    while True:
        sendInfo = input("<<")
        udpSocket.send(sendInfo.encode("GB2312"),(destIp,destPort))

udpSocket = None # 初始化变量
destIp = ""
destPort = 0

def main():
    global udpSocket
    global destIp
    global destPort
    
    destIp = input("对方IP: ")
    destPort = input("对方端口号: ")
    
    udpSocket = socket(AF_INET,SOCK_DGRAM)
    udpSocket.bind(("",8888)) # 绑定端口号

    tr = Thread(target=recvData)
    ts = Thread(target=sendData)

    tr.start()
    ts.start()

    tr.join()
    ts.join()

if __name__ == "__main__":
    main()

```

### 50. TFTP下载器 

```python
# TFTP 协议:
# 1. TFTP客户端发送读写请求至TFTP服务器 默认端口:69  操作码:1:下载|2:上传
# 2. TFTP服务器寻找数据(文件),使用新端口并分批次(每次512子节)发送数据包给TFTP客户端,并携带块编码(序号)占2子节与操作码(3:数据包,5:error,4:确认码,回复确认-->随机端口(ACK确认包) 若文件不存在)占2子节
# 3. TFTP客户端发送确认 操作码4(2子节) + 块编码(2子节)
# 4. ......
# notice: 
# 为标记数据已经发送完毕,所以规定,当客户端收到的数据小于516(2子节操作码+2子节序号+512字节数据)时,就意味着服务器发送完毕了.
# 
```

### 51.  tftp文件下载

```python
# -*- coding:utf-8 -*-

import struct
from socket import *
import time
import os

def main():


	#0. 获取要下载的文件名字:
	downloadFileName = raw_input("请输入要下载的文件名:")	

	#1.创建socket
	udpSocket = socket(AF_INET, SOCK_DGRAM)

	requestFileData = struct.pack("!H%dsb5sb"%len(downloadFileName), 1, downloadFileName, 0, "octet", 0)

	#2. 发送下载文件的请求
	udpSocket.sendto(requestFileData, ("192.168.119.215", 69))

	flag = True #表示能够下载数据，即不擅长，如果是false那么就删除
	num = 0
	f = open(downloadFileName, "w")

	while True:
		#3. 接收服务发送回来的应答数据
		responseData = udpSocket.recvfrom(1024)

		# print(responseData)
		recvData, serverInfo = responseData

		opNum = struct.unpack("!H", recvData[:2])

		packetNum = struct.unpack("!H", recvData[2:4])

		print(packetNum[0])

		# print("opNum=%d"%opNum)
		# print(opNum)

		# if 如果服务器发送过来的是文件的内容的话:
		if opNum[0] == 3: #因为opNum此时是一个元组(3,)，所以需要使用下标来提取某个数据
			

			#计算出这次应该接收到的文件的序号值，应该是上一次接收到的值的基础上+1
			num = num + 1

			# 如果一个下载的文件特别大，即接收到的数据包编号超过了2个字节的大小
			# 那么会从0继续开始，所以这里需要判断，如果超过了65535 那么就改为0
			if num==65536:
				num = 0

			# 判断这次接收到的数据的包编号是否是 上一次的包编号的下一个
			# 如果是才会写入到文件中，否则不能写入（因为会重复）
			if num == packetNum[0]:
				# 把收到的数据写入到文件中
				f.write(recvData[4:])
				num = packetNum[0]

			#整理ACK的数据包
			ackData = struct.pack("!HH", 4, packetNum[0])
			udpSocket.sendto(ackData, serverInfo)

		elif opNum[0] == 5:
			print("sorry，没有这个文件....")
			flag = False

		# time.sleep(0.1)

		if len(recvData)<516:
			break

	if flag == True:
		f.close()
	else:
		os.unlink(downloadFileName)#如果没有要下载的文件，那么就需要把刚刚创建的文件进行删除

if __name__ == '__main__':
	main()
```

### 52. udp(用户数据报协议)广播

```python
import socket,sys

dest = ('<broadcast>',7788) #不确定IP地址

# 创建udp套接字
s = socket.socket(socket.AF_INET,socket.SOCK_DGRAM)
# 对这个需要发送广播数据的套接字进行修改设置,否则不能发送广播
s.setsockopt(socket.SOL_SOCKET,socket.SO_BROADCAST,1)
# 以广播形式发送数据到本网络的所有电脑
s.sendto("Hi",dest)

print("等待对方回复(按ctrl+c退出)")

while True:
    (buf,address) = s.recvfrom(2048)
    print("recieved from %s:%s"%(address,buf))
```

### 53. TCP 传输控制协议

```python
# TCP服务器创建过程:
# 1. Socket创建套接字
# 2. bind绑定IP与port
# 3. listen使套接字变为被动链接
# 4. accept等待客户端的链接
# 5. recv/send接收发送数据

# 服务端代码示例:
from socket import *

serverSocket = socket(AF_INET,SOCK_STREAM)

serverSocket.bind(("",8899))

serverSocket.listen(5)

clientSocket,clientAddr = serverSocket.accept() #返回元组 新套接字+对方IP与端口号

# clientSocket 新客户端
# clientAddr 新客户端IP与端口

recvData = clientSocket .recv(1024)

print("%s:%s"%(str(clientInfo),recvData))

clientSocket.close()

serverSocket.close()
```

### 54. 客户端  

```python
from socket import *

# 创建套接字
tcpClientSocket = socket(AF_INET,SOCK_STREAM)

# 链接服务器
serAddr = ('192.168.1.102',7788)
tcpClientSocket.connect(serAddr) # 元组

# 提示用户输入数据
sendData = input("请输入要发送的数据:")
tcpClientSocket.sendto(sendData)

# 接收对方发送过来的数据,最大接收1024子节
recvData = tcpClientSocket.recv(1024)
print("接收到的数据是:%s"%recvData)

# 关闭套接字
tcpClientSocket.close()
```

### 55. packet tracer  

```python
# mac地址在两个设备之间通信时变化 而 IP地址,在整个通信过程中都不会发生变化
# IP标记逻辑上的地址
# Mac标记实际转发数据时的设备地址
# netmask与IP地址一起来确定网络号
# 默认网关:发送的IP不在同一个网段内,那么会把这个数据转发给默认网关
```

### 56. www.baidu.com访问过程  

```python
# 1. 先解析出www.baidu.com对应的IP地址
# 	1-1 先知道默认网关的Mac
# 	 1-1-1 使用ARP获取默认网关的Mac地址
# 	1-2. 组织数据,发送给默认网关(是IP还是DNS服务器的IP,但是Mac地址是默认网关的Mac地址)
#	1-3. 默认网关拥有转发数据的能力,把数据转发给路由器
#	1-4. 路由器根据自己的路由协议,来选择一个合适的较快的路径,转发数据给目的网关
# 	1-5. 目的网关(DNS度武器所在网关),把数据转发给DNS服务器
#	1-6. DNS服务器查询并解析出www.baidu.com对应的IP地址,并把她原路返回给请求这个域名的client
# 2. 得到www.baidu.com对应的IP地址后,会发送tcp的3次握手进行连接
# 3. 使用http协议发送请求数据给web服务器
# 4. web服务器收到数据请求后,通过查询自己的服务器得到相应的结果,原路返回给浏览器
# 5. 浏览器接收到数据后,通过浏览器自己的渲染功能显示网页
# 6. 浏览器关闭tcp链接,即4次挥手
# 完成整个访问过程
```

### 57. tcp 3次握手4次挥手 

```python
# TCP中,如果有一方收到了对方的数据,一定会发送ACK确认包给发送方,而在UDP中,没有这个过程,因此,TCP比UDP稳定.
# 三次握手建立连接:
			   Client              Server
2SYN_SENT           | SYN------>     | 1LISTEN
	                |<------SYN + ACK| 3SYN_RECV
4ESTABLISHED        |ACK------>      | ESTABLISHED

# 四次挥手:

5FIN_WAIT_1   FIN seq=x+2 ACK=y+1
		    |--------------------->| 
                ACK x+3               6CLOSE_WAIT 
7FIN_WAIT_2	 |<---------------------|	
                FIN seq=y+1       
    	     |<---------------------|  8LAST_ACK(close())
 9TIME_WAIT       ACK=y+2
             |--------------------->| 10CLOSED 
  CLOSED 经过2MSL时长关闭

# TCP十种状态:
# TTL: 经过路由器的个数
# 2MSL问题:2MSL即两倍的MSL,MSL表示一个数据包在网络存活的最长时间.
# listen(10) 最大连接数 
```

### 58. listen(5)参数问题 

```python
# server端
from socket import *
from time import sleep

#创建Socket
tcpSerSocket = socket(AF_INET,SOCK_STREAM)

# 重复使用绑定的信息
# tcpSerSocket.setsockopt(SOL_SOCKET,SO_REUSEADDR,1)

#绑定本地信息
address = ("",7788)
tcpSerSocket.bind(address)

connNum = input("请输入最大连接数:")
# 使用Socket穿件的套接字默认的属性是自动的,使用listen将其变为被动,这样就可以接收别人的连接了

tcpSerSocket.listen(connNum)
# 如果有新的客户端来连接服务器,那么就会吵闹声一个新的套接字专门为这个客户端服务
for i in range(10):
    print(i)
    sleep(1)
# 在这个期间,如果有20个客户端帝爱用了connect链接服务器,
# 那么这个服务器的linux底层会自动维护2个队列(半连接和已连接),
# 其中半连接和已连接的总数是listen中的值,如果这个值为5,
# 那么意味着此时最多有5个客户端能够连接成功,而剩余的15则会堵塞在connect函数


print("延时结束....")

while True:
    newSocket,clientAddr = tcpSerSocket.accept()
    #如果服务器调用accept,那么Linux地底层中的那个半连接和已连接的客户端的个数就少了一个,
    # 因此此时那15个因为connect堵塞的客户端又会在进行连接,来争抢这一个空出来的位置
    print(clientAddr)
    sleep(1)

# 客户端
from socket import *

connNum = input("请输入要连接服务器的次数:")

for i in range(int(connNum)):
    s = socket(AF_INET,SOCK_STREAM)
    s.connect(("127.0.0.1",7788))
    print(i)
```

### 59. 常见网络攻击 

```python
# 1. tcp半连接攻击(SYN Flood(SYN洪水))一种典型的DoS(Denial of Server 拒绝访问)攻击,效果就是服务器TCP链接资源耗尽,停止正常响应的TCP链接请求.
# 2. DNS攻击
#  2-1 指向错误的IP
#  2-2 DNS欺骗 使用假的DNS应答来欺骗用户计算机,让其相信这个假的地址,并抛弃真正的DNS应答
# 3. ARP攻击
```

### 60.  多进程服务 

```python
from socket import *
from multiprocessing import *
from time import sleep

#处理客户端的请求并为其服务
def dealWithClient(newSocket,destAddr):
    while True:
        recvData = newSocket.recv(1024)
        if len(recvData) > 0:
            print("recv[%s]:%s"%(str(destAddr),recvData))
        else:
            print("[%s]客户端已经关闭"%str(destAddr))
            break

def main():
    serSocket = socket(AF_INET,SOCK_STREAM)
    serSocket.setsockopt(SOL_SOCKET,SO_REUSEADDR,1)
    localAddr = ("",7788)
    serSocket.bind(localAddr)
    serSocket.listen(5)
    try:
        while True:
            print("-----主进程---等待客户端的链接-----")
            newSocket,destAddr = serSocket.accept()

            print("------主进程,接下来创建一个新的进程负责数据处理[%s]"%str(destAddr))
            client = Process(target=dealWithClient,args=(newSocket,destAddr))
            client.start()
            #因为已经向子进程中copy了一份(引用),并且父进程这个套接字也没有用处了
            #所以关闭
            newSocket.close()
            # newSocket关闭,意味着这个套接字不在使用recv和send来接收数据了
    finally:
        #当所有的客户端服务完成之后进行关闭,表示不再接受新的客户端的链接
        serSocket.close()

if __name__ == "__main__":
    main()
```

### 61.  多线程服务

```python
from socket import *
from threading import Thread
from time import sleep

#处理客户端的请求并为其服务
def dealWithClient(newSocket,destAddr):
    while True:
        recvData = newSocket.recv(1024)
        if len(recvData) > 0:
            print("recv[%s]:%s"%(str(destAddr),recvData))
        else:
            print("[%s]客户端已经关闭"%str(destAddr))
            break
            
    newSocket.close()

def main():
    serSocket = socket(AF_INET,SOCK_STREAM)
    serSocket.setsockopt(SOL_SOCKET,SO_REUSEADDR,1)
    localAddr = ("",7788)
    serSocket.bind(localAddr)
    serSocket.listen(5)
    try:
        while True:
            print("-----主进程---等待客户端的链接-----")
            newSocket,destAddr = serSocket.accept()

            print("------主进程,接下来创建一个新的进程负责数据处理[%s]"%str(destAddr))
            client = Thread(target=dealWithClient,args=(newSocket,destAddr))
            client.start()
            #因为线程中共享这个套接字,如果关闭了就会导致这个套接字不可使用
            #但是此时在这个线程中这个套接字还在接收数据,因此不关闭
            # newSocket.close()
    finally:
        #当所有的客户端服务完成之后进行关闭,表示不再接受新的客户端的链接
        serSocket.close()

if __name__ == "__main__":
    main()
```

### 62. 单进程服务--堵塞模式

```python
from socket import *

serSocket = socket(AF_INET,SOCK_STREAM)

serSocket.setsockopt(SOL_SOCKET,SO_REUSEADDR,1)

localAddr = ("",7788)

serSocket.bind(localAddr)

serSocket.listen(5)

while True:
    print("-----主进程---等待客户端的链接-----")
    newSocket, destAddr = serSocket.accept()

    print("------主进程,接下来创建一个新的进程负责数据处理[%s]" % str(destAddr))

    try:
        while True:
            recvData = newSocket.recv(1024)
            if len(recvData) > 0:
                print("recv[%s]:%s" % (str(destAddr), recvData))
            else:
                print("[%s]客户端已经关闭" % str(destAddr))
                break
    finally:
        newSocket.close()

serSocket.close()
```

### 63. 单进程服务--非堵塞模式

```python
from socket import *
# 创建套接字
serSocket = socket(AF_INET, SOCK_STREAM)

serSocket.setsockopt(SOL_SOCKET, SO_REUSEADDR, 1)
#绑定本地IP与端口
localAddr = ("", 7788)
serSocket.bind(localAddr)

# 让套接字变为非堵塞
serSocket.setblocking(False)

#将Socket变为监听(被动)套接字
serSocket.listen(5)

# 保存所有已经链接的客户端信息
clientAddrList = []

while True:
    # 等待新的客户端到来,(即完成3次握手的客户端)
    try:
        clientSocket, clientAddr = serSocket.accept()
    except:
        pass
    else:
        print("一个新的客户端到来:%s"%str(clientAddr))
        clientSocket.setblocking(False)
        clientAddrList.append((clientSocket,clientAddr))

    for clientSocket,clientAddr in clientAddrList:
        try:
            recvData = clientSocket.recv(1024)
        except:
            pass
        else:
            if len(recvData) > 0:
                print("%s:%s"%(str(clientAddr),recvData))
            else:
                clientSocket.close()
                clientAddrList.remove((clientSocket,clientAddr))
                print("%s已经下线"%str(clientAddr))
```

### 64.  select版--tcp服务器

```python
# 可实现IO多路复用,没开辟多进程/线程的情况下,实现并发服务
# select能够对一些套接字检测
# select回显服务器
import select
import socket
import sys

server = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
server.bind(("",7788))
server.listen(5)

inputs = [server,sys.stdin]

running = True

while True:
    # 调用 select 函数 阻塞等待
    readable,writeable,exceptional = select.select(inputs,[],[]) #三个列表参数
    # 参数1 检测这个列表中的套接字是否可以接收数据
    # 参数2 检测这个列表中的套接字是否可以发送数据
    # 参数3 检测这个列表中的套接字是否产生异常

    # 数据抵达 循环
    for sock in readable:
        # 监听没有新的链接
        if sock == server:
            conn,addr = server.accept()
            # select 监听的 Socket
            inputs.append(conn)

        # 监听到键盘有输入
        elif sock == sys.stdin:
          cmd = sys.stdin.readline()
          running = False
          break

        #有数据抵达
        else:
            # 读取客户端链接发送的数据
            data = sock.recv(1024)
            if data:
                sock.send(data)
            else:
                inputs.remove(sock)
                sock.close()
# 优点:跨平台支持
# 缺点: 单个进程能够监听的文件描述符的数量linux一般为1024  cat /proc/sys/fs/file-max
#    32位默认1024,64位默认2048,对socket进行扫描时是依次扫描,即采用轮询的方法,效率低下.

#包含一个writeList的服务器

import socket  
import Queue
from select import select  

SERVER_IP = ('', 9999)  

# 保存客户端发送过来的消息,将消息放入队列中  
message_queue = {}  
input_list = []  
output_list = []  

if __name__ == "__main__":  
    server = socket.socket()  
    server.bind(SERVER_IP)  
    server.listen(10)  
    # 设置为非阻塞  
    server.setblocking(False)  

    # 初始化将服务端加入监听列表  
    input_list.append(server)  

    while True:  
        # 开始 select 监听,对input_list中的服务端server进行监听  
        stdinput, stdoutput, stderr = select(input_list, output_list, input_list)  

        # 循环判断是否有客户端连接进来,当有客户端连接进来时select将触发  
        for obj in stdinput:  
            # 判断当前触发的是不是服务端对象, 当触发的对象是服务端对象时,说明有新客户端连接进来了  
            if obj == server:  
                # 接收客户端的连接, 获取客户端对象和客户端地址信息  
                conn, addr = server.accept()  
                print("Client %s connected! "%str(addr))  
                # 将客户端对象也加入到监听的列表中, 当客户端发送消息时 select 将触发  
                input_list.append(conn)  
                # 为连接的客户端单独创建一个消息队列，用来保存客户端发送的消息  
                message_queue[conn] = Queue.Queue()  

            else:  
                # 由于客户端连接进来时服务端接收客户端连接请求，将客户端加入到了监听列表中 (input_list)，客户端发送消息将触发  
                # 所以判断是否是客户端对象触发  
                try:  
                    recv_data = obj.recv(1024)  
                    # 客户端未断开  
                    if recv_data:  
                        print("received %s from client %s"%(recv_data, str(addr)))  
                        # 将收到的消息放入到各客户端的消息队列中  
                        message_queue[obj].put(recv_data)  

                        # 将回复操作放到output列表中，让select监听  
                        if obj not in output_list:  
                            output_list.append(obj)  

                except ConnectionResetError:  
                    # 客户端断开连接了，将客户端的监听从input列表中移除  
                    input_list.remove(obj)  
                    # 移除客户端对象的消息队列  
                    del message_queue[obj]  
                    print("\n[input] Client %s disconnected"%str(addr))  

        # 如果现在没有客户端请求,也没有客户端发送消息时，开始对发送消息列表进行处理，是否需要发送消息  
        for sendobj in output_list:  
            try:  
                # 如果消息队列中有消息,从消息队列中获取要发送的消息  
                if not message_queue[sendobj].empty():  
                    # 从该客户端对象的消息队列中获取要发送的消息  
                    send_data = message_queue[sendobj].get()  
                    sendobj.send(send_data)  
                else:  
                    # 将监听移除等待下一次客户端发送消息  
                    output_list.remove(sendobj)  

            except ConnectionResetError:  
                # 客户端连接断开了  
                del message_queue[sendobj]  
                output_list.remove(sendobj)  
                print("\n[output] Client  %s disconnected"%str(addr))
```

### 65. epoll版--tcp服务器

```python
# 优点:
# 1. 没有最大并发连接的限制,能打开的FD(指的是文件描述符，通俗的理解就是套接字对应的数字编号)的上限远大于1024
# 2. 效率提升，不是轮询的方式，不会随着FD数目的增加效率下降。只有活跃可用的FD才会调用callback函数；即epoll最大的优点就在于它只管你“活跃”的连接，而跟连接总数无关，因此在实际的网络环境中，epoll的效率就会远远高于select和poll.

# 说明
# EPOLLIN （可读）
# EPOLLOUT （可写）
# EPOLLET （ET模式）
# epoll对文件描述符的操作有两种模式：LT（level trigger）和ET（edge trigger）。LT模式是默认模式，LT模式与ET模式的区别如下：

# LT模式：当epoll检测到描述符事件发生并将此事件通知应用程序，应用程序可以不立即处理该事件。下次调用epoll时，会再次响应应用程序并通知此事件。

# ET模式：当epoll检测到描述符事件发生并将此事件通知应用程序，应用程序必须立即处理该事件。如果不处理，下次调用epoll时，不会再次响应应用程序并通知此事件。

# 示例:
import socket
import select

# 创建套接字
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)

# 设置可以重复使用绑定的信息
s.setsockopt(socket.SOL_SOCKET,socket.SO_REUSEADDR,1)

# 绑定本机信息
s.bind(("",7788))

# 变为被动
s.listen(5)

# 创建一个epoll对象
epoll=select.epoll()

# 测试，用来打印套接字对应的文件描述符
# print s.fileno()
# print select.EPOLLIN|select.EPOLLET

# 注册事件到epoll中
# epoll.register(fd[, eventmask])
# 注意，如果fd已经注册过，则会发生异常
# 将创建的套接字添加到epoll的事件监听中
epoll.register(s.fileno(),select.EPOLLIN|select.EPOLLET)


connections = {}
addresses = {}

# 循环等待客户端的到来或者对方发送数据
while True:

    # epoll 进行 fd 扫描的地方 -- 未指定超时时间则为阻塞等待
    epoll_list=epoll.poll()

    # 对事件进行判断
    for fd,events in epoll_list:

        # print fd
        # print events

        # 如果是socket创建的套接字被激活
        if fd == s.fileno():
            conn,addr=s.accept()

            print('有新的客户端到来%s'%str(addr))

            # 将 conn 和 addr 信息分别保存起来
            connections[conn.fileno()] = conn
            addresses[conn.fileno()] = addr

            # 向 epoll 中注册 连接 socket 的 可读 事件
            epoll.register(conn.fileno(), select.EPOLLIN | select.EPOLLET)


        elif events == select.EPOLLIN: # 判断事件是否是接收数据的事件
            # 从激活 fd 上接收
            recvData = connections[fd].recv(1024) # 根据文件描述符找到它对应的套接字,因为文件描述符不能recv 只有套接字可以,所以需要找到这个fd的套接字

            if len(recvData)>0:
                print('recv:%s'%recvData)
            else:
                # 从 epoll 中移除该连接 fd
                epoll.unregister(fd)

                # server 侧主动关闭该 连接 fd
                connections[fd].close()

                print("%s---offline---"%str(addresses[fd]))
```

### 66. 协程

```python
# 协程，又称微线程，纤程。英文名Coroutine。协程其实可以认为是比线程更小的执行单元,它自带CPU上下文.
# 在一个线程中的某个函数，可以在任何地方保存当前函数的一些临时变量等信息，然后切换到另外一个函数中执行，注意不是通过调用函数的方式做到的，并且切换的次数以及什么时候再切换到原来的函数都由开发者自己确定.
# 示例:
import  time
def A():
    while True:
        print("------A------")
        yield
        time.sleep(0.5)

def B(c):
    while True:
        print("-----B-----")
        c.next()
        time.sleep(0.5)

if __name__ == '__main__':
    a = A()
    B(a)

```

### 67. 协程--greenlet版

```python
# pip install greenlet
from greenlet import greenlet
import time

def test1():
    while True:
        print("-----A-----")
        gr2.switch()
        time.sleep(0.5)

def test2():
    while True:
        print("-----B-----")
        gr1.switch()
        time.sleep(0.5)

gr1 = greenlet(test1)
gr2 = greenlet(test2)

# 切换到 gr1中执行
gr1.switch()
```

### 68. gevent

```python
# gevent原理是当一个greenlet遇到IO(指的是input output 输入输出，比如网络、文件操作等)操作时，比如访问网络，就自动切换到其他的greenlet，等到IO操作完成，再在适当的时候切换回来继续执行。
# 由于IO操作非常耗时，经常使程序处于等待状态，有了gevent为我们自动切换协程，就保证总有greenlet在运行，而不是等待IO
# 示例
import gevent

def f(n):
    for i in range(n):
        print(gevent.getcurrent(), i)
        gevent.sleep(1)

g1 = gevent.spawn(f, 5)
g2 = gevent.spawn(f, 5)
g3 = gevent.spawn(f, 5)
g1.join()
g2.join()
g3.join()
```

### 69. gevent并发下载器

```python
from gevent import monkey; 
import gevent
import urllib2

#有IO才做时需要这一句
monkey.patch_all()

def myDownLoad(url):
    print('GET: %s' % url)
    resp = urllib2.urlopen(url)
    data = resp.read()
    print('%d bytes received from %s.' % (len(data), url))

gevent.joinall([
        gevent.spawn(myDownLoad, 'http://www.baidu.com/'),
        gevent.spawn(myDownLoad, 'http://www.itcast.cn/'),
        gevent.spawn(myDownLoad, 'http://www.itheima.com/'),
])
```

### 70. gevent版tcp服务器

```python
import sys
import time
import gevent

from gevent import socket,monkey
monkey.patch_all()

def handle_request(conn):
    while True:
        data = conn.recv(1024)
        if not data:
            conn.close()
            break
        print("recv:", data)
        conn.send(data)


def server(port):
    s = socket.socket()
    s.bind(('', port))
    s.listen(5)
    while True:
        cli, addr = s.accept()
        gevent.spawn(handle_request, cli)

if __name__ == '__main__':
    server(7788)
```

### 71. web服务器(静态文件版) 

```python
# coding:utf-8
import socket
from multiprocessing import Process
import re # 正则表达式
# 常量 设置静态文件根目录
HTML_ROOT_DIR = "./static" #当前文件static文件夹下index.html


def handle_client(client_socket):
    """处理客户端请求"""
    # 获取客户端请求数据
    request_data = client_socket.recv(1024)
    print("request data :",request_data)
    # GET / HTTP/1.1\r\nHost: 127.0.0.1:8000\r\nConnection: keep-alive\r\n
    request_lines = request_data.splitlines()
    #  ['GET / HTTP/1.1', 'Host: 127.0.0.1:8000', 'Connection: keep-alive']
    for line in request_lines:
        print(line)
    # 解析请求报文
    request_start_line = request_lines[0]
    # 提取用户请求的文件名
    print("*"*10)
    print(request_start_line.decode("utf-8"))
    file_name = re.match(r"\w+ +(/[^ ]*) ",request_start_line.decode("utf-8")).group(1)
    print(file_name)
    if "/" == file_name:
        file_name = "/index.html"
    #打开文件 读取文件内容
    try:
        file = open(HTML_ROOT_DIR + file_name,"rb")
    except IOError:
        response_start_line = "HTTP/1.1 404  Not Found\r\n"
        response_headers = "Server:My server\r\n"
        response_body = " the file is not found"
    else:
        file_data = file.read()
        file.close()
        # 构造响应数据
        response_start_line = "HTTP/1.1 200 OK\r\n"
        response_headers = "Server:My server\r\n"
        response_body = file_data.decode("utf-8")
    # finally:
    #     response = response_start_line + response_headers + "\r\n" + response_body
    #     print("response data:", response)
        
    response = response_start_line + response_headers + "\r\n" + response_body
    print("response data:",response)

    # 向客户端返回响应数据
    client_socket.send(bytes(response,"utf-8"))

    # 关闭客户端链接
    client_socket.close()

if __name__ == '__main__':
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.setsockopt(socket.SOL_SOCKET,socket.SO_REUSEADDR,1)
    server_socket.bind(("", 8000))
    server_socket.listen(128)

    while True:
         client_socket,client_address = server_socket.accept()
         print("[%s:%s]用户链接上了"% client_address)
         handle_client_process = Process(target=handle_client,args=(client_socket,))
         handle_client_process.start()
         client_socket.close()
```

### 72. web服务器(面向对象版)

```python
# coding:utf-8
import socket
from multiprocessing import Process
import re

# 常量 设置静态文件根目录
HTML_ROOT_DIR = "./static"

class HTTPServer(object):
    """"""
    def __init__(self):
        self.server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)


    def bind(self,port):
        self.server_socket.bind(("", port))


    def start(self):
        self.server_socket.listen(128)
        while True:
            client_socket, client_address = self.server_socket.accept()
            print("[%s:%s]用户链接上了" % client_address)
            handle_client_process = Process(target=self.handle_client, args=(client_socket,))
            handle_client_process.start()
            client_socket.close()

    def handle_client(self,client_socket):
        """处理客户端请求"""
        # 获取客户端请求数据
        request_data = client_socket.recv(1024)
        print("request data :", request_data)
        # GET /favicon.ico HTTP/1.1\r\nHost: 127.0.0.1:8000\r\nConnection: keep-alive\r\n
        request_lines = request_data.splitlines()
        #  ['GET /favicon.ico HTTP/1.1', 'Host: 127.0.0.1:8000', 'Connection: keep-alive']
        for line in request_lines:
            print(line)
        # 解析请求报文
        request_start_line = request_lines[0]
        # 提取用户请求的文件名
        print("*" * 10)
        print(request_start_line.decode("utf-8"))
        file_name = re.match(r"\w+ +(/[^ ]*) ", request_start_line.decode("utf-8")).group(1)
        print(file_name)
        if "/" == file_name:
            file_name = "/index.html"
        # 打开文件 读取文件内容
        try:
            file = open(HTML_ROOT_DIR + file_name, "rb")
        except IOError:
            response_start_line = "HTTP/1.1 404  Not Found\r\n"
            response_headers = "Server:My server\r\n"
            response_body = " the file is not found"
        else:
            file_data = file.read()
            file.close()
            # 构造响应数据
            response_start_line = "HTTP/1.1 200 OK\r\n"
            response_headers = "Server:My server\r\n"
            response_body = file_data.decode("utf-8")
        # finally:
        #     response = response_start_line + response_headers + "\r\n" + response_body
        #     print("response data:", response)

        response = response_start_line + response_headers + "\r\n" + response_body
        print("response data:", response)

        # 向客户端返回响应数据
        client_socket.send(bytes(response, "utf-8"))

        # 关闭客户端链接
        client_socket.close()


def main():
    http_server = HTTPServer()
    http_server.bind(8000)
    http_server.start()

if __name__ == '__main__':
     main()
```

### 73. 动态服务器(WSGI协议)   

```python
# 1. server端
# coding:utf-8
import socket
from multiprocessing import Process
import re
import sys

# 常量 设置静态文件根目录
HTML_ROOT_DIR = "./static"
WSGI_PYTHON_DIR = "./wsgi_py"
class HTTPServer(object):
    def __init__(self):
        self.server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)


    def bind(self,port):
        self.server_socket.bind(("", port))


    def start(self):
        self.server_socket.listen(128)
        while True:
            client_socket, client_address = self.server_socket.accept()
            print("[%s:%s]用户链接上了" % client_address)
            handle_client_process = Process(target=self.handle_client, args=(client_socket,))
            handle_client_process.start()
            client_socket.close()

    def start_response(self,status,headers):
        response_headers = "HTTP/1.1" + status + "\r\n"
        for header in headers:
            response_headers += "%s:%s\r\n" % header
        self.response_headers = response_headers

    def handle_client(self,client_socket):
        """处理客户端请求"""
        # 获取客户端请求数据
        request_data = client_socket.recv(1024)
        print("request data :", request_data)
        # 解析请求报文
        # GET /favicon.ico HTTP/1.1\r\nHost: 127.0.0.1:8000\r\nConnection: keep-alive\r\n
        request_lines = request_data.splitlines()
        #  ['GET /favicon.ico HTTP/1.1', 'Host: 127.0.0.1:8000', 'Connection: keep-alive']
        for line in request_lines:
            print(line)
        # 解析请求报文
        request_start_line = request_lines[0]
        # 提取用户请求的文件名
        print("*" * 10)
        print(request_start_line.decode("utf-8"))
        file_name = re.match(r"\w+ +(/[^ ]*) ", request_start_line.decode("utf-8")).group(1)
        method = re.match(r"(\w+) +/[^ ]* ", request_start_line.decode("utf-8")).group(1)
        print(file_name)
        # 判断 file_name 是否是脚本
        if file_name.endswith(".py"):
            try:
                m = __import__(file_name[1:-3]) #导入模块
            except Exception:
                self.response_headers = "HTTP/1.1 404 Not Found\r\n"
                self.body = " not found"
            else:
                env = {
                    "PATH_INFO": file_name,
                    "METHOD": method
                }
                response_body = m.application(env,self.start_response)
            response = self.response_headers + "\r\n" + response_body
        else:
            if "/" == file_name:
                file_name = "/index.html"
            # 打开文件 读取文件内容
            try:
                file = open(HTML_ROOT_DIR + file_name, "rb")
            except IOError:
                response_start_line = "HTTP/1.1 404  Not Found\r\n"
                response_headers = "Server:My server\r\n"
                response_body = " the file is not found"
            else:
                file_data = file.read()
                file.close()
                # 构造响应数据
                response_start_line = "HTTP/1.1 200 OK\r\n"
                response_headers = "Server:My server\r\n"
                response_body = file_data.decode("utf-8")
                # finally:
                #     response = response_start_line + response_headers + "\r\n" + response_body
                #     print("response data:", response)

            response = response_start_line + response_headers + "\r\n" + response_body
            print("response data:", response)

        # 向客户端返回响应数据
        client_socket.send(bytes(response, "utf-8"))

        # 关闭客户端链接
        client_socket.close()


def main():
    sys.path.insert(1,WSGI_PYTHON_DIR)
    http_server = HTTPServer()
    http_server.bind(8000)
    http_server.start()

if __name__ == '__main__':
     main()
        
# 2. 文件 wsgi_py/ctime.py
# coding:utf-8
import time
def application(env,start_response):
    status = " 200 Ok"
    headers = {
        ("Content-Type","text/plain")
    }
    start_response(status,headers)
    return  time.ctime()
```

### 74. web框架 

```python
# 1.  MyWebFramework 框架(启动文件)
# coding:utf-8
import time

from  network.web.MyWebServer import HTTPServer

HTML_ROOT_DIR = "./static"

class Application(object):

    def __init__(self,urls):
        # 设置路由信息
        self.urls = urls

    def __call__(self, env, start_response):
        path = env.get("PATH_INFO","/")
        if path.startswith("/static"):
            # 访问静态文件
            file_name = path[7:]
            # 打开文件 读取文件内容
            try:
                file = open(HTML_ROOT_DIR + file_name, "rb")
            except IOError:
                # 未找到路由信息 404 错误
                status = "404 Not Found"
                headers = []
                start_response(status, headers)
                return " file not found "
            else:
                file_data = file.read()
                file.close()
                status = "200 OK"
                headers = []
                start_response(status, headers)
                return file_data.decode("utf-8")

        for url,handler in self.urls:
            if path == url:
                return handler(env,start_response)
        # 未找到路由信息 404 错误
        status = "404 Not Found"
        headers = []
        start_response(status,headers)
        return " file not found "


def show_ctime(env,start_response):
    status = " 200 OK"
    headers = [
        ("Content-type","text/html")
    ]
    start_response(status,headers)
    return time.ctime()

def say_hello(env,start_response):
    status = " 200 OK"
    headers = [
        ("Content-type", "text/html")
    ]
    start_response(status, headers)
    return "say hello"

if __name__ == '__main__':
    urls = [
        ("/",show_ctime),
        ("/ctime",show_ctime),
        ("/sayhello",say_hello)
    ]
    app = Application(urls)
    http_server = HTTPServer(app)
    http_server.bind(8000)
    http_server.start()

# 2. MyWebServer 服务器文件
# coding:utf-8
import socket
from multiprocessing import Process
import re
import sys

# 常量 设置静态文件根目录
HTML_ROOT_DIR = "./static"
WSGI_PYTHON_DIR = "./wsgi_py"
class HTTPServer(object):
    """"""
    def __init__(self,application):
        self.server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        self.app = application


    def bind(self,port):
        self.server_socket.bind(("", port))


    def start(self):
        self.server_socket.listen(128)
        while True:
            client_socket, client_address = self.server_socket.accept()
            print("[%s:%s]用户链接上了" % client_address)
            handle_client_process = Process(target=self.handle_client, args=(client_socket,))
            handle_client_process.start()
            client_socket.close()

    def start_response(self,status,headers):
        response_headers = "HTTP/1.1" + status + "\r\n"
        for header in headers:
            response_headers += "%s:%s\r\n" % header
        self.response_headers = response_headers

    def handle_client(self,client_socket):
        """处理客户端请求"""
        # 获取客户端请求数据
        request_data = client_socket.recv(1024)
        print("request data :", request_data)
        # 解析请求报文
        # GET /favicon.ico HTTP/1.1\r\nHost: 127.0.0.1:8000\r\nConnection: keep-alive\r\n
        request_lines = request_data.splitlines()
        #  ['GET /favicon.ico HTTP/1.1', 'Host: 127.0.0.1:8000', 'Connection: keep-alive']
        for line in request_lines:
            print(line)
        # 解析请求报文
        request_start_line = request_lines[0]
        # 提取用户请求的文件名
        print("*" * 10)
        print(request_start_line.decode("utf-8"))
        file_name = re.match(r"\w+ +(/[^ ]*) ", request_start_line.decode("utf-8")).group(1)
        method = re.match(r"(\w+) +/[^ ]* ", request_start_line.decode("utf-8")).group(1)
        print(file_name)

        env = {
            "PATH_INFO": file_name,
            "METHOD": method
        }
        response_body = self.app(env, self.start_response)
        response = self.response_headers + "\r\n" + response_body

        # 向客户端返回响应数据
        client_socket.send(bytes(response, "utf-8"))

        # 关闭客户端链接
        client_socket.close()


def main():
    sys.path.insert(1,WSGI_PYTHON_DIR)
    http_server = HTTPServer()
    http_server.bind(8000)
    http_server.start()

if __name__ == '__main__':
     main()
```

### 75. web框架扩展  

```python
# 1. MyWebServer 作为启动文件
# coding:utf-8
import socket
from multiprocessing import Process
import re
import sys

# 常量 设置静态文件根目录
HTML_ROOT_DIR = "./static"
WSGI_PYTHON_DIR = "./wsgi_py"
class HTTPServer(object):
    """"""
    def __init__(self,application):
        self.server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        self.app = application


    def bind(self,port):
        self.server_socket.bind(("", port))


    def start(self):
        self.server_socket.listen(128)
        while True:
            client_socket, client_address = self.server_socket.accept()
            print("[%s:%s]用户链接上了" % client_address)
            handle_client_process = Process(target=self.handle_client, args=(client_socket,))
            handle_client_process.start()
            client_socket.close()

    def start_response(self,status,headers):
        response_headers = "HTTP/1.1" + status + "\r\n"
        for header in headers:
            response_headers += "%s:%s\r\n" % header
        self.response_headers = response_headers

    def handle_client(self,client_socket):
        """处理客户端请求"""
        # 获取客户端请求数据
        request_data = client_socket.recv(1024)
        print("request data :", request_data)
        # 解析请求报文
        # GET /favicon.ico HTTP/1.1\r\nHost: 127.0.0.1:8000\r\nConnection: keep-alive\r\n
        request_lines = request_data.splitlines()
        #  ['GET /favicon.ico HTTP/1.1', 'Host: 127.0.0.1:8000', 'Connection: keep-alive']
        for line in request_lines:
            print(line)
        # 解析请求报文
        request_start_line = request_lines[0]
        # 提取用户请求的文件名
        print("*" * 10)
        print(request_start_line.decode("utf-8"))
        file_name = re.match(r"\w+ +(/[^ ]*) ", request_start_line.decode("utf-8")).group(1)
        method = re.match(r"(\w+) +/[^ ]* ", request_start_line.decode("utf-8")).group(1)
        print(file_name)

        env = {
            "PATH_INFO": file_name,
            "METHOD": method
        }
        response_body = self.app(env, self.start_response)
        response = self.response_headers + "\r\n" + response_body

        # 向客户端返回响应数据
        client_socket.send(bytes(response, "utf-8"))

        # 关闭客户端链接
        client_socket.close()


def main():
    sys.path.insert(1,WSGI_PYTHON_DIR)
    if len(sys.argv) < 2 :
        sys.exit("python MyWebServer.py mudole:app")
    module_name,app_name = sys.argv[1].split(":")
    m = __import__(module_name)
    app = getattr(m,app_name)
    http_server = HTTPServer(app)
    http_server.bind(8000)
    http_server.start()

if __name__ == '__main__':
     main()
        
# 2. MyWebFramework 
# coding:utf-8
import time

# from  network.web.MyWebServer import HTTPServer

HTML_ROOT_DIR = "./static"

class Application(object):

    def __init__(self,urls):
        # 设置路由信息
        self.urls = urls

    def __call__(self, env, start_response):
        path = env.get("PATH_INFO","/")
        if path.startswith("/static"):
            # 访问静态文件
            file_name = path[7:]
            # 打开文件 读取文件内容
            try:
                file = open(HTML_ROOT_DIR + file_name, "rb")
            except IOError:
                # 未找到路由信息 404 错误
                status = "404 Not Found"
                headers = []
                start_response(status, headers)
                return " file not found "
            else:
                file_data = file.read()
                file.close()
                status = "200 OK"
                headers = []
                start_response(status, headers)
                return file_data.decode("utf-8")

        for url,handler in self.urls:
            if path == url:
                return handler(env,start_response)
        # 未找到路由信息 404 错误
        status = "404 Not Found"
        headers = []
        start_response(status,headers)
        return " file not found "


def show_ctime(env,start_response):
    status = " 200 OK"
    headers = [
        ("Content-type","text/html")
    ]
    start_response(status,headers)
    return time.ctime()

def say_hello(env,start_response):
    status = " 200 OK"
    headers = [
        ("Content-type", "text/html")
    ]
    start_response(status, headers)
    return "say hello"

    urls = [
        ("/",show_ctime),
        ("/ctime",show_ctime),
        ("/sayhello",say_hello)
    ]
    app = Application(urls)
# if __name__ == '__main__':
#     urls = [
#         ("/",show_ctime),
#         ("/ctime",show_ctime),
#         ("/sayhello",say_hello)
#     ]
#     app = Application(urls)
#     http_server = HTTPServer(app)
#     http_server.bind(8000)
#     http_server.start()
```

### 76. 正则表达式 

```python
# re模块 需要导入 import re
# result = re.match(正则表达式,需要匹配的字符串)
# result.group() 如果上一步匹配到数据(匹配对象)的话(否则返回None,注意,不是空字符串 ""),可使用group方法来提取数据

# 正则表达式字符集描述
字符         功能
 .      匹配任意1个字符(除了\n)
 []     匹配[]中列举的字符
 \d     匹配数字,0-9 等价于 [0-9]
 \D     匹配非数字,即不是数字 等价于 [^0-9]
 \s     匹配空白,即空格,tab键 
 \S     匹配非空白
 \w     匹配单词字符,即a-z,A-Z,0-9,_ 等价于 [a-zA-Z0-9_]
 \W     匹配非单词字符 
# 表示数量
 *      匹配前一个字符出现0次或无限次,即可有可无
 +      匹配前一个字符出现1次或无限次,即至少有1次
 ?      匹配前一个字符出现1次或0次,即要么有1次,要么没有
{m}     匹配前一个字符出现m次
{m,}    匹配前一个字符至少出现m次
{m,n}   匹配前一个字符出现从m到n次
# 表示边界
 ^      匹配字符串开头
 $      匹配字符串结尾 如手机号 re.match('1[35678]\d{9}$','18311039502')
\b      匹配一个单词的边界
\B      匹配非单词边界
# 匹配分组/book
 |      匹配左右任意一个表达式
(ab)    将括号中字符作为一个分组
\num    引用分组num匹配到的字符串
(?P<name>) 分组起别名
(?P=name)  引用别名为name分组匹配到的字符串

# 匹配邮箱
re.match( r'(\w{5,20})@(163|126|sina|qq)\.(com|cn|net)$','assasin0308@sina.com')
# re的高级语法
re.search(r'\d+','阅读次数为 9999')
re.findall() # 返回列表
re.sub(r'php','python','itcast python cpp php python php')  #进行替换 将PHP替换为python 返回替换后的字符串

def replace(result):
    r =  int(result.group()) + 50
    return str(r)
re.sub(r'\d+',replace,'python=1000,php=0')
re.split(r':|,|-','itcast:php,python,cpp-java') #返回分割后的列表

# 贪婪模式与非贪婪模式
# python中数量词默认是贪婪的,总是尝试匹配尽可能多的字符,非贪婪则相反,总是尝试匹配尽可能少的字符.
# 在"*","?","+",".","{m,n}"后面加上?,使贪婪模式变为非贪婪模式.

```

## 三 数据结构与算法

### 77. 算法的特性 

```python
# 1. 输入:算法具有0个或多个输入
# 2. 输出 :算法至少有一个或多个输出
# 3. 有穷性: 算法在有限的步骤之后会自动结束而不会无限循环,并且每一个步骤都可以在可接受的时间内完成
# 4. 确定性:算法中的每一步都有确定的含义,不会出现二义性
# 5. 可行性:算法的每一步都是可行的,也就是说每一步都能够执行有限的次数完成
```

### 78. 时间复杂度 O

```python
# "大O记法":对于单调的整数函数f,如果存在一个整数g和一个实常数c>0,使得对于充分大的n总有f(n)<=c*g(n),就说函数g是f的一个渐进函数(忽略常数),记为f(n) = O(g(n)).也就是说,在趋向无穷的极限意义下,函数f的增长速度受到函数g的约束,亦即函数f与函数g的特征相似.

# 时间复杂度: 假设存在函数g,使得算法A处理规模为n的问题示例所用时间为T(n) = O(g(n)),则称O(g(n))为算法A的渐进时间复杂度,简称事件复杂度,记为T(n).

# 时间复杂度的基本计算规则:
# 1. 基本操作: 只有常数项,认为其时间复杂度是O(1)
# 2. 顺序结构:时间复杂度按假发进行计算
# 3. 循环结构:时间复杂度按乘法进行计算
# 4. 分支结构:时间复杂度取最大值
# 5. 判断一个算法的效率时,往往只需要关注操作数量的最高次项,其他次要项和常数项可以忽略
# 6. 在没有特殊说明时,时间复杂度都是指最坏事件复杂度

# 常见时间复杂度:

 执行次数函数举例     阶       非正式术语
    12		        O(1)       常数阶
    2n+3             O(n)       线性阶
    3n^2+2n+1        O(n^2)     平方阶
    5log2^n+19       O(logn)    对数阶
    6n^3+2n^2+4      O(n^3)     立方阶
    2^n              O(2^n)     指数阶
 # 所消耗的时间从小到大:
O(1) < O(logn) < O(n) < O(nlogn) < O(n^2) < O(n^3) < O(2^n) < O(n!) < O(n^n)
```

### 79.  内置类型性能分析

```python
# timeit 模块对列表操作测试
from timeit import Timer
def t1():
    li = []
    for i in range(1000):
        li.append(i)

def t2():
    li = []
    for i in range(1000):
        li = li + [i]

def t3():
    li = [i for i in range(1000)]

def t4():
    li = list(range(1000))

def t5():
    li = []
    for i in range(1000):
        li.extend([i])

def t6():
    li = []
    for i in range(10000):
        li.append(i)

def t7():
    li = []
    for i in range(10000):
        li.insert(0,i)

timer1 = Timer("t1()","from __main__ import t1")
print("append:",timer1.timeit(100))

timer2 = Timer("t2()","from __main__ import t2")
print("+:",timer2.timeit(100))

timer3 = Timer("t3()","from __main__ import t3")
print("[i for i in range]:",timer3.timeit(100))

timer4 = Timer("t4()","from __main__ import t4")
print("list(range):",timer4.timeit(100))

timer5 = Timer("t5()","from __main__ import t5")
print("extend:",timer5.timeit(100))

timer6 = Timer("t6()","from __main__ import t6")
print("append:",timer6.timeit(100))

timer7 = Timer("t7()","from __main__ import t7")
print("insert(0):",timer7.timeit(100))

# 结果:
append: 0.007166240705008858
+: 0.11363091409679203
[i for i in range]: 0.0026227672274691666
list(range): 0.001205245816548739
extend: 0.009428280468952777
append: 0.07506163749816638
insert(0): 3.2196555211401106 
```

### 80. list/dict操作时间复杂度  

```python
# list操作时间复杂度:
Operation            Big-O Efficiency
indexx[]                  O(1)
index assignment		 O(1)
append                    O(1)
pop()					O(1)
pop(i)					O(n)
insert(i,item)			 O(n)
del operator			 O(n)
iteration				 O(n)
contains(in)			 O(n)
get slice[x:y]			 O(k)
del slice				 O(n)
set slice 				 O(n+k)
reverse					 O(n)
concatenate				  O(k)
sort					 O(n log n)
multiply				 O(nk)

# dict操作时间复杂度:
Operation               Big-O Efficiency
 copy					    O(n)
 get item					O(1)
 set item					O(1)
 delete item				O(1)
 contains (in)				O(1)
 iteration  				O(n)
```

### 81. 顺序表 

```python
# 顺序表: 将元素顺序的存放在一块连续的存储区里,元素间的顺序关系由它们的存储顺序自然表示.

# 顺序表的一体式结构与分离式结构:
# 一体式: 整体性强,易于管理,但由于数据元素存储区是表对象的一部分,顺序表创建后,元素存储区就固定了
# 分离式: 表对象中只保存与整个表有关的信息(即容量和元素个数),实际数据元素存放在另一个独立的元素存储区中,通过链表与基本表对象关联 
# 采用分离式结构的顺序表,若将数据区更换为存储空间更大的区域,则可以在不改变表对象的前提下对其数据存储区进行了扩充,所有使用这个表的地方都不必修改.只要程序的运行环境(计算机系统)还有空闲存储,这种表结构就不会因为满了而导致操作无法进行.采用这种技术实现的顺序表称为动态顺序表,因为其容量可以在使用中动态变化.

# 扩充的联众策略:
# 1. 每次扩充增加固定数目的存储位置,如每次扩充增加10个数据元素的位置,这种策略可称为线性增长;
# 特点: 节省空间,但是扩充操作频繁,操作次数多.
# 2. 每次扩充容量加倍,如每次扩充增加一倍的存储空间;
# 特点: 减少了扩充操作的执行次数,但可能浪费空间资源.推荐的方式是:以空间换时间.

# list与tuple采用顺序表的实现技术,tuple是不可变类型,即不变的顺序表,因此不支持改变其内部状态的任何操作,其他方面,则与list的性质类似.
# list是可变的线性表,可以增加与删除元素,并在各种操作中维持已有元素的顺序(即保序),而且还具有以下行为特征:
# 基于下标(位置)的高效元素访问和更新,时间复杂度是O(1),为满足该特性,应该采用顺序表结构,表中元素保存在一块连续的存储区中;
# 允许任意加入元素,而且在不断加入元素的过程中,表对象的标识(函数id得到的值)不变,为满足该特性,就必须更换元素存储区,并且为保证更换存储区时list对象标识id不变,只能采用分离式实现;

# list就是采用分离式技术实现的动态顺序表,list的实现采用如下策略:
# 在建立空表(或很小的表)时,系统分配一块能容纳8个元素的存储区,在执行插入操作(insert或append)时,如果元素存储区满就换一块4倍大的存储区.但是如果此时的表已经很大(目前的阀值是50000),则改变策略,采用加一倍的方法,引入这种改变策略的方法,是为了避免出现过多空闲的存储位置.

```

### 82. 链表-- 单向链表  

```python
# 链表: 将元素存放在通过链接构造起来的一系列存储块中.是一种线性表,不像顺序表一样连续存储数据,而是在每一个节点(数据存储单元)中存放下一个节点的位置信息(即地址).

# 单向链表: 即单链表,是链表中最简单的一种形式,它的每一个节点包含两个域,一个信息域(元素域)和一个链接域.这个链接指向链表中的下一个节点,而最后一个节点的链接域则指向一个空值.
# 表元素域elem用来存放具体的数据;
# 链接域next用来存放下一个节点的位置(python中的标识);
# 变量p指向链表的头节点(首节点)的位置,从p出发能找到表中的任意节点.

# 节点的实现:
class SingleNode(object):
    def __init__(self,item):
        # _item存放数据元素
        self.item = item
        # next是下一个节点的标识
        self.next = None
        
# 单向链表的操作:
is_empty() 链表是否为空
length() 链表长度
travel() 遍历整个链表
add(item) 链表头部添加元素
append(item) 链表尾部添加元素
insert(pos, item) 指定位置添加元素
remove(item) 删除节点
search(item) 查找节点是否存在


# 单向链表的实现:
# coding:utf-8

class Node(object):
    """节点"""
    def __init__(self,elem):
        self.elem = elem
        self.next = None

class SingleLinkedList(object):
    """单向链表"""
    def __init__(self):
        self.__head = None #初始化链表

    # 判断链表是否为空
    def is_empty(self):
        return self.__head == None

    # 链表长度 如果链表为空 返回长度0
    def length(self):
        # cur游标 用来移动遍历节点
        cur = self.__head
        # count 记录数量
        count = 0
        while cur != None:
            count += 1
            cur = cur.next
            return count

    # 遍历整个链表
    def travel(self):
        cur = self.__head
        while cur != None:
            print(cur.elem,end = ' ')
            cur = cur.next
        print(" ")


    # 链表头部添加元素
    def add(self,item):
        node = Node(item)
        node.next = self.__head
        self.__head = node

    # 链表尾部添加元素
    def append(self,item):
        node = Node(item)
        if self.is_empty():
            self.__head = node
        else:
            cur = self.__head
            while cur.next != None:
                cur = cur.next
            cur.next = node

    # 在链表指定位置添加元素
    def insert(self,pos,item):
        if pos <= 0 :
            self.add(item) # 头部插入
        elif pos > (self.length() - 1) :
            self.append(item)
        else:
            node = Node(item)
            cur = self.__head
            count = 0
            # 移动到指定位置的前一个位置
            while count < (pos - 1):
                count += 1
                cur = cur.next
            node.next = cur.next
            cur.next = node

    # 删除节点
    def remove(self,item):
        cur  = self.__head
        pre = None
        while cur != None:
            if cur.elem == item:
                if cur == self.__head:
                    self.__head = cur.next
                else:
                    pre.next = cur.next
                break
            else:
                pre = cur
                cur = cur.next

    # 查找节点是否存在
    def search(self,item):
        cur = self.__head
        while cur != None:
            if cur.elem == item:
                return True
            else:
                cur = cur.next
        return False



if __name__ == '__main__':
    single_obj = SingleLinkedList()
    print(single_obj.is_empty())
    print(single_obj.length())
    single_obj.append(1)
    print(single_obj.is_empty())
    print(single_obj.length())
    single_obj.append(2)
    single_obj.add(8)
    single_obj.append(3)
    single_obj.append(4)
    single_obj.append(5)
    single_obj.append(6)
    single_obj.insert(-2,9)
    single_obj.travel()
    single_obj.insert(3,100)
    single_obj.travel()
    single_obj.insert(10,200)
    single_obj.travel()
    single_obj.remove(100)
    single_obj.travel()
    single_obj.remove(9)
    single_obj.travel()
    single_obj.remove(200)
    single_obj.travel()

```

### 83. 双向链表的实现 

```python

```

### 84. 栈(LIFO)与队列(FILO) 

```python
# 栈(堆栈),可存入数据元素,访问元素,删除元素,特点是只能允许在容器的一端(称为栈顶端指标)进行加入数据和输出数据的运算.没有位置概念,保证任何时候可以访问,删除的元素都是此前最后存入的那个元素,确定了一种默认的访问顺序. 由于数据结构只允许在一进行端操作,因而按照"后进先出"的原理运作.
# 队列是指允许在一段进行插入操作,而在另一端进行删除操作的线性表.遵循"先进先出"的原理.
```

### 85. 栈的实现  

```python
# Stack() 创建一个新的空栈
# push(item) 添加一个新的元素item到栈顶 
# pop() 弹出栈顶元素
# peek() 返回栈顶元素
# is_empty() 判断栈是否为空
# size() 返回栈的元素个数

# 栈的实现(list)
# coding:utf-8
class Stack(object):
    """栈 """
    def __init__(self):
        self.__list = [];

    def push(self,item):
        """添加一个新的元素item到栈顶"""
        return self.__list.append(item)

    def pop(self):
        """弹出栈顶元素"""
        return self.__list.pop()

    def peek(self):
        """返回栈顶元素"""
        if self.__list:
            return self.__list[-1]
        else:
            return None

    def is_empty(self):
        """判断栈是否为空"""
        return self.__list == []

    def size(self):
        """返回栈的元素个数"""
        return len(self.__list)


if __name__ == '__main__':
    stack =  Stack()
    print(stack.is_empty())
    stack.push(100)
    stack.push(200)
    stack.push(300)
    stack.push(400)
    print(stack.pop())
    print(stack.pop())
    print(stack.pop())
    print(stack.pop())
```

### 86. 队列的实现 

```python
# coding:utf-8

class Queue(object):
    """队列的实现"""
    def __init__(self):
        self.__list = []

    def enqueue(self,item):
        """往队列中添加一个元素"""
        self.__list.append(item)

    def dequeue(self):
        """从队列头部删除一个元素"""
        return self.__list.pop(0)

    def is_empty(self):
        """判断队列是否为空"""
        return self.__list == []

    def size(self):
        """返回队列的大小"""
        return len(self.__list)


if __name__ == '__main__':
    queue = Queue()
    queue.enqueue(100)
    queue.enqueue(200)
    queue.enqueue(300)
    queue.enqueue(400)
    print(queue.dequeue())
    print(queue.dequeue())
    print(queue.dequeue())
    print(queue.dequeue())
```

### 87. 双端队列  

```python
# 双端队列是一种具有队列和栈的性质的数据结构.双端端队列中的元素可以从两端弹出,其限定插入和删除操作在表的两端进行.双端队列可以再队列任意一端入队和出队.
# 实现
# coding:utf-8

class DoubleQueue(object):
    """双端队列 """
    def __init__(self):
        self.__list = []

    def add_front(self,item):
        """往队列头部添加一个元素"""
        self.__list.append(0,item)

    def add_rear(self,item):
        """往队列中尾部添加一个元素"""
        self.__list.append(item)

    def pop_front(self):
        """从队列头部删除一个元素"""
        return self.__list.pop(0)

    def pop_rear(self):
        """从队列尾部删除一个元素"""
        return self.__list.pop()

    def is_empty(self):
        """判断队列是否为空"""
        return self.__list == []

    def size(self):
        """返回队列的大小"""
        return len(self.__list)
```

### 88. 冒泡排序

```python
# 是一种简单的排序算法。它重复地遍历要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。遍历数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。
# 冒泡排序算法的运作如下：
# 比较相邻的元素。如果第一个比第二个大（升序），就交换他们两个。
# 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
# 针对所有的元素重复以上的步骤，除了最后一个。
# 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。
# 1. 实现
def bubble_sort(alist):
    count = len(alist)
    for j in range(count-1):
        number = 0
        for i in range(0,count-1-j):
            if alist[i] > alist[i+1]:
                alist[i],alist[i+1] = alist[i+1],alist[i]
                number += 1
        if 0 == number:
            return             

if __name__ == '__main__':
    li = [54, 26, 93, 17, 77, 31, 44, 55, 20]
    bubble_sort(li)
    print(li)
# 2. 时间复杂度
# 最优时间复杂度：O(n) （表示遍历一次发现没有任何可以交换的元素，排序结束。）
# 最坏时间复杂度：O(n^2)
# 稳定性：稳定
```

### 89. 选择排序

```python
# 首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕.
# 选择排序的主要优点与数据移动有关。如果某个元素位于正确的最终位置上，则它不会被移动。选择排序每次交换一对元素，它们当中至少有一个将被移到其最终位置上，因此对n个元素的表进行排序总共进行至多n-1次交换。在所有的完全依靠交换去移动元素的排序方法中，选择排序属于非常好的一种。
# 1. 实现
def select_sort(alist):
    count = len(alist)
    # 需要进行 n- 1 次选择操作
    for i in range(count-1):
        # 记录最小位置
        min_index = i
        # 从 i + 1 位置到末尾选择出最小数据
        for j in range(i+1,count):
            if alist[j] < alist[min_index]:
                min_index = j
        # 如果选择出的数据不再正确位置,进行交换
        if min_index != i:
            alist[i],alist[min_index] = alist[min_index],alist[i]

if __name__ == '__main__':
    li = [54, 226, 93, 17, 77, 31, 44, 55, 20]
    select_sort(li)
    print(li)
    
# 2. 时间复杂度:
# 最优时间复杂度：O(n^2)
# 最坏时间复杂度：O(n^2)
# 稳定性：不稳定（考虑升序每次选择最大的情况）
```

### 90. 插入排序

```python
# 工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。插入排序在实现上，在从后向前扫描过程中，需要反复把已排序元素逐步向后挪位，为最新元素提供插入空间。
# 1. 实现
def insert_sort(alist):
    n = len(alist)
    # 从右边的徐徐序列中取出多少个元素,执行以下过程
    for j in range(1,n):
        i = j  # i 内层循环起始值 执行从右边的无需序列中取出第一个元素,即i的位置,将其插入到前面的正确位置中
        while i > 0:
            if alist[i] < alist[i-1]:
                alist[i],alist[i-1] = alist[i-1],alist[i]
            i -= 1
            
if __name__ == '__main__':
    li = [54, 26, 93, 17, 77, 31, 44, 55, 20]
    insert_sort(li)
    print(li)
    
# 2. 时间复杂度
# 最优时间复杂度：O(n) （升序排列，序列已经处于升序状态）
# 最坏时间复杂度：O(n^2)
# 稳定性：稳定
```

### 91. 希尔排序

```python
# 是插入排序的一种。也称缩小增量排序，是直接插入排序算法的一种更高效的改进版本。希尔排序是非稳定排序算法。该方法因DL．Shell于1959年提出而得名。 希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1时，整个文件恰被分成一组，算法便终止。
# 基本思想是：将数组列在一个表中并对列分别进行插入排序，重复这过程，不过每次用更长的列（步长更长了，列数更少了）来进行。最后整个表就只有一列了。将数组转换至表是为了更好地理解这算法，算法本身还是使用数组进行排序。
# 1. 实现
def shell_sort(alist):
    n = len(alist)
    gap = n // 2
    # gap变化到0之前,插入算法执行的次数
    while gap > 0: # gap >= 1
        # 插入算法 与 插入算法的区别是gap步长
        for j in range(gap,n):
            i = j
            while i > 0:
                if alist[i] < alist[i-gap]:
                    alist[i],alist[i-gap] = alist[i-gap],alist[i]
                    i -= gap
                else:
                    break
        # 缩短gap步长
        gap  = gap // 2

if __name__ == '__main__':
    li = [54, 26, 93, 17, 77, 31, 44, 55, 20]
    shell_sort(li)
    print(li)
# 2. 时间复杂度
# 最优时间复杂度：根据步长序列的不同而不同
# 最坏时间复杂度：O(n^2)
# 稳定想：不稳定
```

### 92. 快速排序

```python
# 又称划分交换排序（partition-exchange sort），通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。
# 步骤为：
# 从数列中挑出一个元素，称为"基准"（pivot），
# 重新排序数列，所有元素比基准值小的摆放在基准前面，所有元素比基准值大的摆在基准的后面（相同的数可以到任一边）。在这个分区结束之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。
# 递归地（recursive）把小于基准值元素的子数列和大于基准值元素的子数列排序。
# 递归的最底部情形，是数列的大小是零或一，也就是永远都已经被排序好了。虽然一直递归下去，但是这个算法总会结束，因为在每次的迭代（iteration）中，它至少会把一个元素摆到它最后的位置去。
# 1. 实现
def quick_sort(alist,first,last):
    if first >= last:
        return
    mid_value = alist[first]
    low = first
    high = last
    while low < high:
        # high 左移
        while low < high and alist[high] >= mid_value:
            high -= 1
        alist[low] = alist[high]

        while low < high and alist[low] < mid_value:
            low += 1
        alist[high] = alist[low]
    # 从循环退出时,low = high
    alist[low] = mid_value

    # 对low左边的列表执行快速排序,
    quick_sort(alist,first,low-1)
    # 对low右边的列表排序
    quick_sort(alist,low+1,last)

if __name__ == '__main__':
    li = [54, 26, 93, 17, 77, 31, 44, 55, 20]
    quick_sort(li,0,len(li)-1)
    print(li)
# 2. 时间复杂度
# 最优时间复杂度：O(nlogn)
# 最坏时间复杂度：O(n^2)
# 稳定性：不稳定
```

### 93. 归并排序

```python
# 归并排序的思想就是先递归分解数组，再合并数组。
# 将数组分解最小之后，然后合并两个有序数组，基本思路是比较两个数组的最前面的数，谁小就先取谁，取了后相应的指针就往后移一位。然后再比较，直至一个数组为空，最后把另一个数组的剩余部分复制过来即可。
# 1. 实现
def merge_sort(alist):
    n = len(alist)
    if n <= 1:
        return alist
    # 二分分解
    mid = n // 2
    left = merge_sort(alist[:mid])
    right = merge_sort(alist[mid:])
    # 合并
    return merge(left,right)

def merge(left,right):
    # left right 的下标指针
    l,r = 0,0,
    result = []
    while l < len(left) and r < len(right):
        if left[l] < right[r]:
            result.append(left[l])
            l += 1
        else:
            result.append(right[r])
            r += 1
    result += left[l:]
    result += right[r:]
    return  result

if __name__ == '__main__':
    li = [54, 26, 93, 17, 77, 31, 44, 55, 20]
    sorted_alist = merge_sort(li)
    print(sorted_alist)
# 2. 时间复杂度
# 最优时间复杂度：O(nlogn)
# 最坏时间复杂度：O(nlogn)
# 稳定性：稳定
```

### 94. 常见排序算法效率

```python
 排序算法    平均情况                最好情况          最坏情况          辅助空间           稳定性
 冒泡排序     O(n^2)                   O(n)            O(n^2)            O(1)             稳定
 选择排序     O(n^2)				  O(n^2)          O(n^2)            O(1)            不稳定
 插入排序     O(n^2)                   O(n)            O(n^2)            O(1)             稳定
 希尔排序     O(n*logn) ~ O(n^2)       O(n^1.3)        O(n^2)            O(1)            不稳定
 堆排序       O(n*logn)                O(n*logn)       O(n*logn)         O(1)            不稳定
 归并排序     O(n*logn)                O(n*logn)       O(n*logn)         O(n)             稳定
*快速排序     O(n*logn)                O(n*logn)       O(n^2)            O(n*logn) ~ O(n)不稳定
```

### 95. 二分查找法

```python
# 搜索的几种常见方法：顺序查找、二分法查找、二叉树查找、哈希查找
# 二分查找又称折半查找，优点是比较次数少，查找速度快，平均性能好；其缺点是要求待查表为有序表，且插入删除困难。因此，折半查找方法适用于不经常变动而查找频繁的有序列表。首先，假设表中元素是按升序排列，将表中间位置记录的关键字与查找关键字比较，如果两者相等，则查找成功；否则利用中间位置记录将表分成前、后两个子表，如果中间位置记录的关键字大于查找关键字，则进一步查找前一子表，否则进一步查找后一子表。重复以上过程，直到找到满足条件的记录，使查找成功，或直到子表不存在为止，此时查找不成功。
# 实现1(非递归)
def binary_search(alist,item):
    first = 0
    last = len(alist) -1
    while first <= last:
        mid_value = (first + last) // 2
        if alist[mid_value] == item:
            print("找到了,下标是:%d"%mid_value)
            return True
        elif item < alist[mid_value]:
            last = mid_value -1
        else:
            first = mid_value + 1
    print("没有能找到...")
    return False

if __name__ == '__main__':
    testlist = [0, 1, 2, 8, 13, 17, 19, 32, 42, ]
    print(binary_search(testlist, 3))
    print(binary_search(testlist, 13))
# 实现2(递归)
def binary_search(alist,item):
    if len(alist) == 0:
        return False
    else:
        mid_value = len(alist) // 2
        if alist[mid_value] == item:
            print("找到了,下标是:%d"%mid_value)
            return True
        else:
            if item < alist[mid_value]:
                return binary_search(alist[:mid_value],item)
            else:
                return  binary_search(alist[mid_value + 1:],item)

if __name__ == '__main__':
    testlist = [0, 1, 2, 8, 13, 17, 19, 32, 42, ]
    print(binary_search(testlist, 3))
    print(binary_search(testlist, 13))
# 时间复杂度
# 最优时间复杂度：O(1)
# 最坏时间复杂度：O(logn)
```

### 96. 树与树算法

```python
# 树是一种抽象数据类型（ADT）或是实作这种抽象数据类型的数据结构，用来模拟具有树状结构性质的数据集合。它是由n（n>=1）个有限节点组成一个具有层次关系的集合。把它叫做“树”是因为它看起来像一棵倒挂的树，也就是说它是根朝上，而叶朝下的。它具有以下的特点：
# 每个节点有零个或多个子节点；
# 没有父节点的节点称为根节点；
# 每一个非根节点有且只有一个父节点；
# 除了根节点外，每个子节点可以分为多个不相交的子树

# 树的相关术语
# 节点的度：一个节点含有的子树的个数称为该节点的度；
# 树的度：一棵树中，最大的节点的度称为树的度；
# 叶节点或终端节点：度为零的节点；
# 父亲节点或父节点：若一个节点含有子节点，则这个节点称为其子节点的父节点；
# 孩子节点或子节点：一个节点含有的子树的根节点称为该节点的子节点；
# 兄弟节点：具有相同父节点的节点互称为兄弟节点；
# 节点的层次：从根开始定义起，根为第1层，根的子节点为第2层，以此类推；
# 树的高度或深度：树中节点的最大层次；
# 堂兄弟节点：父节点在同一层的节点互为堂兄弟；
# 节点的祖先：从根到该节点所经分支上的所有节点；
# 子孙：以某节点为根的子树中任一节点都称为该节点的子孙。
# 森林：由m（m>=0）棵互不相交的树的集合称为森林；

# 树的种类
# 无序树：树中任意节点的子节点之间没有顺序关系，这种树称为无序树，也称为自由树；
# 有序树：树中任意节点的子节点之间有顺序关系，这种树称为有序树；
# 	二叉树：每个节点最多含有两个子树的树称为二叉树；
# 		完全二叉树：对于一颗二叉树，假设其深度为d(d>1)。除了第d层外，其它各层的节点数目均已达最大值，且第d层所有节点从左向右连续地紧密排列，这样的二叉树被称为完全二叉树，其中满二叉树的定义是所有叶节点都在最底层的完全二叉树;(完全二叉树——若设二叉树的高度为h，除第 h 层外，其它各层 (1～h-1) 的结点数都达到最大个数，第h层有叶子结点，并且叶子结点都是从左到右依次排布，这就是完全二叉树。)(满二叉树---除了叶结点外每一个结点都有左右子叶且叶子结点都处在最底层的二叉树。)
# 		平衡二叉树（AVL树）：当且仅当任何节点的两棵子树的高度差不大于1的二叉树；
# 		排序二叉树（二叉查找树（英语：Binary Search Tree），也称二叉搜索树、有序二叉树）；
# 	霍夫曼树（用于信息编码）：带权路径最短的二叉树称为哈夫曼树或最优二叉树；
# 	B树：一种对读写操作进行优化的自平衡的二叉查找树，能够保持数据有序，拥有多余两个子树。

# 树的存储于表示
# 顺序存储:将数据结构存储在固定的数组中，然在遍历速度上有一定的优势，但因所占空间比较大，是非主流二叉树。二叉树通常以链式存储。
# 链式存储:由于对节点的个数无法掌握，常见树的存储表示都转换成二叉树进行处理，子节点个数最多为2
```

### 97. 二叉树

```python
# 二叉树是每个节点最多有两个子树的树结构。通常子树被称作“左子树”（left subtree）和“右子树”（right subtree）
# 性质
# 性质1: 在二叉树的第i层上至多有2^(i-1)个结点（i>0）
# 性质2: 深度为k的二叉树至多有2^k - 1个结点（k>0）
# 性质3: 对于任意一棵二叉树，如果其叶结点数为N0，而度数为2的结点总数为N2，则N0=N2+1;
# 性质4:具有n个结点的完全二叉树的深度必为 log2(n+1)
# 性质5:对完全二叉树，若从上至下、从左至右编号，则编号为i 的结点，其左孩子编号必为2i，其右孩子编号必为2i＋1；其双亲的编号必为i/2（i＝1 时为根,除外）

# 树的实现
class Node(object):

    def __init__(self,item):
        self.elem = item
        self.lchild = None
        self.rchild = None

class Tree(object):
    """二叉树"""
    def __init__(self):
        self.root = None

    def add(self,item):
        node = Node(item)
        # 如果树是空的,对根节点赋值
        if self.root is None:
            self.root = node
            return
        queue = [self.root]
        # 对已有节点进行层次遍历
        while queue:
            # 弹出队列第一个元素
            cur_node = queue.pop(0)
            if cur_node.lchild is None:
                cur_node.lchild = node
                return
            else:
                queue.append(cur_node.lchild)
            if cur_node.rchild is None:
                cur_node.rchild = node
                return
            else:
                queue.append(cur_node.rchild)


if __name__ == '__main__':
    tree = Tree()
```

### 98. 二叉树的遍历

```python
# 树的两种重要的遍历模式是深度优先遍历和广度优先遍历,深度优先一般用递归，广度优先一般用队列。一般情况下能用递归实现的算法大部分也能用堆栈来实现。
# 1. 深度优先遍历
# 1.1 先序遍历 在先序遍历中，我们先访问根节点，然后递归使用先序遍历访问左子树，再递归使用先序遍历访问右子树
# 根节点->左子树->右子树

# 1.2 中序遍历 在中序遍历中，我们递归使用中序遍历访问左子树，然后访问根节点，最后再递归使用中序遍历访问右子树
# 左子树->根节点->右子树

# 1.3 后序遍历 在后序遍历中，我们先递归使用后序遍历访问左子树和右子树，最后访问根节点
# 左子树->右子树->根节点

# 2. 广度优先遍历(层次遍历) 从树的root开始，从上到下从从左到右遍历整个树的节点
# 实现
class Node(object):

    def __init__(self,item):
        self.elem = item
        self.lchild = None
        self.rchild = None

class Tree(object):
    """二叉树"""
    def __init__(self):
        self.root = None

    def add(self,item):
        node = Node(item)
        # 如果树是空的,对根节点赋值
        if self.root is None:
            self.root = node
            return
        queue = [self.root]
        # 对已有节点进行层次遍历
        while queue:
            # 弹出队列第一个元素
            cur_node = queue.pop(0)
            if cur_node.lchild is None:
                cur_node.lchild = node
                return
            else:
                queue.append(cur_node.lchild)
            if cur_node.rchild is None:
                cur_node.rchild = node
                return
            else:
                queue.append(cur_node.rchild)

    # 广度遍历(层次遍历)
    def breadth_travel(self):
        if self.root is None:
            return
        queue = [self.root]
        while queue:
            cur_node = queue.pop(0)
            print(cur_node.elem,end= ' ')
            if cur_node.lchild is not None:
                queue.append(cur_node.lchild)
            if cur_node.rchild is not None:
                queue.append(cur_node.rchild)

    # 深度遍历
    def preorder(self,node):
        """先序遍历"""
        if node is None:
            return
        print(node.elem,end=' ')
        self.preorder(node.lchild)
        self.preorder(node.rchild)

    def inorder(self,node):
        """中序遍历"""
        if node is None:
            return
        self.inorder(node.lchild)
        print(node.elem,end=' ')
        self.inorder(node.rchild)


    def postorder(self,node):
        """后序遍历"""
        if node is None:
            return
        self.postorder(node.lchild)
        self.postorder(node.rchild)
        print(node.elem,end=' ')


if __name__ == '__main__':
    tree = Tree()
    tree.add(0)
    tree.add(1)
    tree.add(2)
    tree.add(3)
    tree.add(4)
    tree.add(5)
    tree.add(6)
    tree.add(7)
    tree.add(8)
    tree.add(9)
    tree.breadth_travel()
    print("\r\n")
    tree.preorder(tree.root)
    print("\r\n")
    tree.inorder(tree.root)
    print("\r\n")
    tree.postorder(tree.root)
```

## 四 数据库

### 99. Mysql 

```python
# 关系型数据库三范式
# 第一范式: 列不可拆分
# 第二范式: 唯一标识
# 第三范式: 引用主键
# 后一个范式,都是在前一个范式的基础上建立的.
```

### 100. 数据库操作

```python
from pymysql import *

class MysqlHelper(object):

    def __init__(self,host,port,dbname,user,passwd,charset='utf8'):
        self.host = host
        self.port = port
        self.db = dbname
        self.user = user
        self.passwd = passwd
        self.charset = charset

    def open(self):
        self.conn = connect(host=self.host,port=self.port,db=self.db,user=self.user,
                            passwd=self.passwd,charset=self.charset)
        self.cursor = self.conn.cursor()

     # 增 改 删
    def  curd(self,sql,params):
        self.open()

        self.cursor.execute(sql,params)
        self.conn.commit()

        self.close()

    def all(self,sql,params=[]):
        self.open()

        self.cursor.execute(sql,params)
        return  self.cursor.fetchall()

        self.close()

    def close(self):
        self.cursor.close()
        self.conn.close()
```

### 101. MongoDB

```python
# 特点:
# 模式自由 :可以把不同结构的文档存储在同一个数据库里
# 面向集合的存储：适合存储 JSON风格文件的形式
# 完整的索引支持：对任何属性可索引
# 复制和高可用性：支持服务器之间的数据复制，支持主-从模式及服务器之间的相互复制。复制的主要目的是提供冗余及自动故障转移
# 自动分片：支持云级别的伸缩性：自动分片功能支持水平的数据库集群，可动态添加额外的机器
# 丰富的查询：支持丰富的查询表达方式，查询指令使用JSON形式的标记，可轻易查询文档中的内嵌的对象及数组
# 快速就地更新：查询优化器会分析查询表达式，并生成一个高效的查询计划
# 高效的传统存储方式：支持二进制数据及大型对象（如照片或图片）
```

### 102. MongoDB 操作

```python
# 创建集合: db.createCollection('集合名称')
# 查看集合: show collections
# 删除集合: db.集合名称.drop()
# 高级聚合查询:......
# 查看执行计划: db.集合名称.find().explain('executionStats')
# 创建索引: db.集合名称.ensureIndex({"name":1}) # 1 升序| -1 降序
# 唯一索引: db.集合名称.ensureIndex({"name":1},{"unique":true})
# 联合索引: db.集合名称.ensureIndex({name:1,age:1})
# 查看索引: db.集合名称.getIndexes()
# 删除索引: db.集合名称.dropIndexes('索引名称')

# MongoDB采用角色--用户--数据库的安全管理模式
  # root 超级管理员
  # Read 允许用户读取指定数据
  # readWrite:允许用户读写指定数据库
# 创建一个用户
use admin
db.createUser({
    user:'admin',
    pwd:'123',
    roles:[{role:'root',db:'admin'}]
})

# 安全认证(用户+密码)

# 修改配置文件 
security:
    authorization: enabled(前有空格)
# 重启服务
service mongod restart
# mongo -u admin -p 123 --authenticationDatabase admin
```

### 103. MongoDB复制(副本集) 

```python
# 复制工作原理:
# 复制至少需要两个节点A,B...
# A是主节点,负责处理客户端请求
# 其余都是从节点,负责府之主节点的数据
# 节点常见的搭配方式是:一主一从,一主多从
# 主节点记录在其上的所有操作,从节点定期轮训主节点获取这些操作,然后对自己的数据副本执行这些操作,从而保证节点的数据与主节点的一致
# 主节点与从节点进行数据交互保障数据的一致性

# 复制的特点:
# N个节点的集群
# 任何节点可作为主节点
# 所有写入操作都在主节点上
# 自动故障转移

# 启动两个mongodb服务 replSet名称必须一致
mongod --bind_ip 192.168.1.128 --port 27017 --dbpath=/e/t1 --replSet rs0
mongod --bind_ip 192.168.1.128 --port 27018 --dbpath=/e/t2 --replSet rs0
# 启动连个客户端进行连接
mongo --host 192.168.1.128 --port 27017 
mongo --host 192.168.1.128 --port 27018
# 主节点上初始化 (自定义) 27017上
rs.initiate()
rs.status() # 查看当前状态
# 添加副本集
rs.add('192.168.1.128:27018')
# 在 主节点 添加数据
# 如果在从服务器上进行读操作,需要在从服务器设置
rs.slaveOk()
# 在主节点删除从节点,重启
rs.remove('192.168.1.128:27018')
```

### 104. 数据备份 

```python
# 备份
# mongodump -h host -d dbname -o 备份数据存放位置
# 恢复
# mongorestore -h host -d dbname --dir 备份数据所在位置
```

### 105. MongoDB操作 

```python
# pip install pymongo
# 无安全认证
# client = MongoClient('mongodb://localhost:27017')
# 有安全认证
# client = MongoClient('mongodb://用户名:密码@localhost:27017/数据库名称')

# 获得数据库
# db = client.数据库名称
# 操作方法
# db.insert_one
# db.insert_many
# db.update_one
# db.update_many
# db.delete_one
# db.delete_many#
# db.find
# db.find_one
# 获得集合
# stu = db.集合名称
# 添加文档,返回文档id
# s1 = {'name':'霍金','gender':True}
# s1_id = stu.insert(s1).inserted)id
# print(s1_id)
# 修改文档
# stu.update_one({'name':'霍金'},{'$set':{'name':'郭靖'}})
# 删除文档
# stu.delete({'name':'郭靖'})
# 查找一个文档,将文档转为元组返回
# s2 = stu.find_one({'name':'郭靖'})


from pymongo import *

# 获取客户端,建立连接
client = MongoClient('mongodb://127.0.0.1:27017')
# 切换数据库
db = client.py3
# 获取集合
stu = db.stu
# 添加一条数据
# stu.insert_one({'name':'张三丰'})
# 修改
# stu.update_one({'name':'张三丰'},{'$set':{'name':'史斌'}})
# 删除
# stu.delete_one({'name':'assasin'})
# 查询
# cursor = stu.find({'age':{'$gt':20}})
# cursor = stu.find({'age':{'$gt':20}}).sort('age',ASCENDING) # DESCENDING:降序|ASCENDING:升序
# for s in cursor:
#     print(s['name'])
```

### 106. Redis

```python
# string
# string最大存储512M,string类型是二进制安全的,可以为任何数据.
# 设置键值 setex name 10 assasin  

# hash hash用于存储对象，对象的格式为键值对
# 设置单个属性 HSET key field value  # hset info name assasin
# 设置多个属性 HMSET key field value[field value...]
# 获取单个属性 HGET info name
# 获取全部键及属性: HGETALL key
# 获取所有键: HKEYS key
# 获取所有值: HVALS key
# 获取属性个数: HLEN key
# 判断属性是否存在: HEXISTS key field
# 删除属性及值: HDEL key field

# list 列表的元素类型为string 按照插入顺序排序  在列表的头部或者尾部添加元素
# 在头部插入数据: LPUSH key value 
# 在尾部插入数据: RPUSH key value
# 在一个元素的前|后插入新元素: LINSERT key BEFORE|AFTER pivot value
	# 设置指定索引的元素值
	# 索引是基于0的下标
	# 索引可以是负数，表示偏移量是从list尾部开始计数，如-1表示列表的最后一个元素
# 移除并且返回 key 对应的 list 的第一个元素: LPOP key
# 移除并返回存于 key 的 list 的最后一个元素: RPOP key
# 返回存储在 key 的列表里指定范围内的元素 ;start 和 end 偏移量都是基于0的下标;偏移量也可以是负数，表示偏移量是从list尾部开始计数，如-1表示列表的最后一个元素: LRANGE key start stop


# set 无序集合 元素为string类型 元素具有唯一性，不重复
# 添加元素: SADD key member [member ...]
# 返回key集合所有的元素: SMEMBERS key
# 返回集合元素个数: SCARD key
# 求多个集合的交集: SINTER key [key ...]
# 求某集合与其它集合的差集: SDIFF key [key ...]
# 求多个集合的合集: SUNION key [key ...]
# 判断元素是否在集合中: SISMEMBER key member


# zset
# 特点:
# sorted set，有序集合
# 元素为string类型
# 元素具有唯一性，不重复
#每个元素都会关联一个double类型的score，表示权重，通过权重将元素从小到大排序,元素的score可以相同
# 添加: ZADD key score member [score member ...]
# 返回指定范围内的元素: ZRANGE key start stop
# 返回元素个数: ZCARD key
# 返回有序集key中，score值在min和max之间的成员: ZCOUNT key min max
# 返回有序集key中，成员member的score值: ZSCORE key member

# 发布/订阅
# 特点:
# 发布者不是计划发送消息给特定的接收者（订阅者），而是发布的消息分到不同的频道，不需要知道什么样的订阅者订阅
# 订阅者对一个或多个频道感兴趣，只需接收感兴趣的消息，不需要知道什么样的发布者发布的
# 发布者和订阅者的解耦合可以带来更大的扩展性和更加动态的网络拓扑
# 客户端发到频道的消息，将会被推送到所有订阅此频道的客户端
# 客户端不需要主动去获取消息，只需要订阅频道，这个频道的内容就会被推送过来

# 消息的格式:
# 推送消息的格式包含三部分:
# part1:消息类型，包含三种类型
	# subscribe，表示订阅成功
	# unsubscribe，表示取消订阅成功
	# message，表示其它终端发布消息
# 如果第一部分的值为subscribe，则第二部分是频道，第三部分是现在订阅的频道的数量
# 如果第一部分的值为unsubscribe，则第二部分是频道，第三部分是现在订阅的频道的数量，如果为0则表示当前没有订阅任何频道，当在Pub/Sub以外状态，客户端可以发出任何redis命令
# 如果第一部分的值为message，则第二部分是来源频道的名称，第三部分是消息的内容

# 订阅: SUBSCRIBE 频道名称 [频道名称 ...]
# 取消订阅,如果不写参数，表示取消所有订阅: UNSUBSCRIBE 频道名称 [频道名称 ...]
# 发布: PUBLISH 频道 消息


# 主从配置
# 一个master可以拥有多个slave，一个slave又可以拥有多个slave，如此下去，形成了强大的多级服务器集群架构.比如，将ip为192.168.1.10的机器作为主服务器，将ip为192.168.1.11的机器作为从服务器.
# 1. 设置主服务器的配置
bind 192.168.1.10
# 2. 设置从服务器的配置,注意：在slaveof后面写主机ip，再写端口，而且端口必须写 :
bind 192.168.1.11
slaveof 192.168.1.10 6379
# 注意: 分别重启主从服务
# 3. 在master和slave分别执行info命令，查看输出信息 在master上写数据
set hello world
# 4. 在slave上读数据
get hello
```

### 107. Redis操作

```python
# pip install redis
# 引入模块
import redis
# 连接
try:
    r=redis.StrictRedis(host='localhost',port=6379)
except Exception,e:
    print e.message
# 方式一：根据数据类型的不同，调用相应的方法，完成读写
r.set('name','hello')
r.get('name')
# 方式二：pipline 缓冲多条命令，然后一次性执行，减少服务器-客户端之间TCP数据库包，从而提高效率
pipe = r.pipeline()
pipe.set('name', 'world')
pipe.get('name')
pipe.execute()

# 封装
import redis
class RedisHelper():
    def __init__(self,host='localhost',port=6379):
        self.__redis = redis.StrictRedis(host, port)
    def get(self,key):
        if self.__redis.exists(key):
            return self.__redis.get(key)
        else:
            return ""
    def set(self,key,value):
        self.__redis.set(key,value)
```

## 五 Django

### 108. 项目应用环境创建 
pip install virtualenv virtualenvwrapper

vim ~/.bashrc 
export VIRTUALENVWRAPPER_PYTHON=/usr/local/python3/bin/python3
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/python3/bin/virtualenv
source /usr/local/python3/bin/virtualenvwrapper.sh
source  ~/.bashrc 

```python
# 创建虚拟环境
mkvirtualenv [虚拟环境名称]
mkvirtualenv -p /usr/local/python3/bin/python  py3
# 删除虚拟环境
rmvirtualenv [虚拟环境名称]
# 退出虚拟环境
deactivate
# 进入虚拟环境
workon [虚拟环境名称]
# 安装django
pip install Django (Django==1.8.2)
# 创建项目
django-admin startproject test1(项目名称)

├── manage.py
└── test1
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
    
# 项目目录说明

# manage.py:命令行工具,可使用多种方式对django项目进行交互
# 内层目录,真正的Python包
# __init__.py:空文件,它告诉python这个目录应该被看作一个python包
# settings.py:项目配置
# urls.py:项目的url声明
# wsgi.py:项目与WSGI兼容的web服务器入口


# 数据库设置
# 在settings.py文件中,通过DATABASES项进行数据库配置
# django支持的数据库包括:sqlite,mysql等主流数据库
# django默认使用SQLite数据库


# 创建应用
python manage.py startapp booktest

# 生成迁移(settings中INSTALLED_APPS注册)
python manage.py makemigrations

#执行迁移
python manage.py migrate

# 进入终端
python manage.py shell

# 运行项目
python manage.py runserver ip:port(默认端口为8000)

# 浏览器访问:http://0.0.0.0:8000/admin

# 创建模拟用户
python manage.py createsuperuser 

# admin.py(注册模型)
admin.site.register(模型类,admin类)
```

### 109. django 模型 

```python
# 1. 使用MySQL数据库(apt install libmysqlclient-dev)
  pip install mysql-python
  # 在settings.py中修改DATABASES项
  DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'test2',
        'USER': '用户名',
        'PASSWORD': '密码',
        'HOST': '数据库服务器ip，本地可以使用localhost',
        'PORT': '端口，默认为3306',
    }
}
    
# 2. 使用数据库生成模型类
python manage.py inspectdb > booktest/models.py

# 3. 定义模型
# 在模型中定义属性，会生成表中的字段
# django根据属性的类型确定以下信息：
#	当前选择的数据库支持字段的类型
#	渲染管理表单时使用的默认html控件
#	在管理站点最低限度的验证
# django会为表增加自动增长的主键列，每个模型只能有一个主键列，如果使用选项设置某属性为主键列后，则django不会再生成默认的主键列
# 属性命名限制:
#	不能是python的保留关键字
#	由于django的查询方式，不允许使用连续的下划线

# 4. 定义属性
# 定义属性时，需要字段类型
# 字段类型被定义在django.db.models.fields目录下，为了方便使用，被导入到django.db.models中
# 使用方式
#	导入from django.db import models
#	通过models.Field创建字段类型的对象，赋值给属性
# 对于重要数据都做逻辑删除，不做物理删除，实现方法是定义isDelete属性，类型为BooleanField，默认值为False

# 5.字段类型
# AutoField：一个根据实际ID自动增长的IntegerField，通常不指定
#	如果不指定，一个主键字段将自动添加到模型中
# BooleanField：true/false 字段，此字段的默认表单控制是CheckboxInput
# NullBooleanField：支持null、true、false三种值
# CharField(max_length=字符长度)：字符串，默认的表单样式是 TextInput
# TextField：大文本字段，一般超过4000使用，默认的表单控件是Textarea
# IntegerField：整数
# DecimalField(max_digits=None, decimal_places=None)：使用python的Decimal实例表示的十进制浮点数
#	DecimalField.max_digits：位数总数
#	DecimalField.decimal_places：小数点后的数字位数
#FloatField：用Python的float实例来表示的浮点数
# DateField[auto_now=False, auto_now_add=False])：使用Python的datetime.date实例表示的日期
#	参数DateField.auto_now：每次保存对象时，自动设置该字段为当前时间，用于"最后一次修改"的时间戳，它总是使用当前日期，默认为false
#	参数DateField.auto_now_add：当对象第一次被创建时自动设置当前时间，用于创建的时间戳，它总是使用当前日期，默认为false
#	该字段默认对应的表单控件是一个TextInput. 在管理员站点添加了一个JavaScript写的日历控件，和一个“Today"的快捷按钮，包含了一个额外的invalid_date错误消息键
#	auto_now_add, auto_now, and default 这些设置是相互排斥的，他们之间的任何组合将会发生错误的结果
# TimeField：使用Python的datetime.time实例表示的时间，参数同DateField
# DateTimeField：使用Python的datetime.datetime实例表示的日期和时间，参数同DateField
# FileField：一个上传文件的字段
# ImageField：继承了FileField的所有属性和方法，但对上传的对象进行校验，确保它是个有效的image

# 6. 字段选项
# 通过字段选项，可以实现对字段的约束
# 在字段对象时通过关键字参数指定
# null：如果为True，Django 将空值以NULL 存储到数据库中，默认值是 False
# blank：如果为True，则该字段允许为空白，默认值是 False
# 对比：null是数据库范畴的概念，blank是表单验证证范畴的
# db_column：字段的名称，如果未指定，则使用属性的名称
# db_index：若值为 True, 则在表中会为此字段创建索引
# default：默认值
# primary_key：若为 True, 则该字段会成为模型的主键字段
# unique：如果为 True, 这个字段在表中必须有唯一值

# 7. 关系
# 关系的类型包括
#	ForeignKey：一对多，将字段定义在多的端中
#	ManyToManyField：多对多，将字段定义在两端中
#	OneToOneField：一对一，将字段定义在任意一端中
# 可以维护递归的关联关系，使用'self'指定，详见“自关联”
# 用一访问多：对象.模型类小写_set
bookinfo.heroinfo_set
# 用一访问一：对象.模型类小写
heroinfo.bookinfo
# 访问id：对象.属性_id
heroinfo.book_id

# 8. 元选项
# 在模型类中定义类Meta，用于设置元信息
# 元信息db_table：定义数据表名称，推荐使用小写字母，数据表的默认名称
<app_name>_<model_name>
# ordering：对象的默认排序字段，获取对象的列表时使用，接收属性构成的列表
class BookInfo(models.Model):
    ...
    class Meta():
        ordering = ['id']
# 字符串前加-表示倒序，不加-表示正序
class BookInfo(models.Model):
    ...
    class Meta():
        ordering = ['-id']
# 排序会增加数据库的开销

# 9. 类的属性
# objects：是Manager类型的对象，用于与数据库进行交互
# 当定义模型类时没有指定管理器，则Django会为模型类提供一个名为objects的管理器
# 支持明确指定模型类的管理器
class BookInfo(models.Model):
    ...
    books = models.Manager()
# 当为模型类指定管理器后，django不再为模型类生成名为objects的默认管理器

# 10. 管理器Manager
# 管理器是Django的模型进行数据库的查询操作的接口，Django应用的每个模型都拥有至少一个管理器
# 自定义管理器类主要用于两种情况
#	情况一：向管理器类中添加额外的方法：见下面“创建对象”中的方式二
#	情况二：修改管理器返回的原始查询集：重写get_queryset()方法
class BookInfoManager(models.Manager):
    def get_queryset(self):
        return super(BookInfoManager, self).get_queryset().filter(isDelete=False)
class BookInfo(models.Model):
    ...
    books = BookInfoManager()

# 11. 创建对象
# 当创建对象时，django不会对数据库进行读写操作
# 调用save()方法才与数据库交互，将对象保存到数据库中
# 使用关键字参数构造模型对象很麻烦，推荐使用下面的两种之式
# 说明： _init _方法已经在基类models.Model中使用，在自定义模型中无法使用，
# 方式一：在模型类中增加一个类方法
class BookInfo(models.Model):
    ...
    @classmethod
    def create(cls, title, pub_date):
        book = cls(btitle=title, bpub_date=pub_date)
        book.bread=0
        book.bcommet=0
        book.isDelete = False
        return book
# 引入时间包：from datetime import *
# 调用：book=BookInfo.create("hello",datetime(1980,10,11));
# 保存：book.save()
# 方式二：在自定义管理器中添加一个方法
# 在管理器的方法中，可以通过self.model来得到它所属的模型类
class BookInfoManager(models.Manager):
    def create_book(self, title, pub_date):
        book = self.model()
        book.btitle = title
        book.bpub_date = pub_date
        book.bread=0
        book.bcommet=0
        book.isDelete = False
        return book

class BookInfo(models.Model):
    ...
    books = BookInfoManager()
# 调用：book=BookInfo.books.create_book("abc",datetime(1980,1,1))
# 保存：book.save()
# 在方式二中，可以调用self.create()创建并保存对象，不需要再手动save()
class BookInfoManager(models.Manager):
    def create_book(self, title, pub_date):
        book = self.create(btitle = title,bpub_date = pub_date,bread=0,bcommet=0,isDelete = False)
        return book

class BookInfo(models.Model):
    ...
    books = BookInfoManager()
# 调用：book=Book.books.create_book("abc",datetime(1980,1,1))
# 查看：book.pk

# 12. 实例属性
# DoesNotExist：在进行单个查询时，模型的对象不存在时会引发此异常，结合try/except使用

# 13. 实例方法
# str (self)：重写object方法，此方法在将对象转换成字符串时会被调用
# save()：将模型对象保存到数据表中
# delete()：将模型对象从数据表中删除

# 14 查询集
# 在管理器上调用过滤器方法会返回查询集
# 查询集经过过滤器筛选后返回新的查询集，因此可以写成链式过滤
# 惰性执行：创建查询集不会带来任何数据库的访问，直到调用数据时，才会访问数据库
# 何时对查询集求值：迭代，序列化，与if合用
# 返回查询集的方法，称为过滤器
#	all()
#	filter()
#	exclude()
#	order_by()
#	values()：一个对象构成一个字典，然后构成一个列表返回
# 写法：
filter(键1=值1,键2=值2)
等价于
filter(键1=值1).filter(键2=值2)
# 返回单个值的方法:
#	get()：返回单个满足条件的对象
# 如果未找到会引发"模型类.DoesNotExist"异常
# 如果多条被返回，会引发"模型类.MultipleObjectsReturned"异常
#	count()：返回当前查询的总条数
#	first()：返回第一个对象
#	last()：返回最后一个对象
#	exists()：判断查询集中是否有数据，如果有则返回True


# 15. 限制查询集
# 查询集返回列表，可以使用下标的方式进行限制，等同于sql中的limit和offset子句
# 注意：不支持负数索引
# 使用下标后返回一个新的查询集，不会立即执行查询
# 如果获取一个对象，直接使用[0]，等同于[0:1].get()，但是如果没有数据，[0]引发IndexError异常，[0:1].get()引发DoesNotExist异常

# 16.查询集的缓存
# 每个查询集都包含一个缓存来最小化对数据库的访问
# 在新建的查询集中，缓存为空，首次对查询集求值时，会发生数据库查询，django会将查询的结果存在查询集的缓存中，并返回请求的结果，接下来对查询集求值将重用缓存的结果
# 情况一：这构成了两个查询集，无法重用缓存，每次查询都会与数据库进行一次交互，增加了数据库的负载
print([e.title for e in Entry.objects.all()])
print([e.title for e in Entry.objects.all()])
# 情况二：两次循环使用同一个查询集，第二次使用缓存中的数据
querylist=Entry.objects.all()
print([e.title for e in querylist])
print([e.title for e in querylist])
# 何时查询集不会被缓存：当只对查询集的部分进行求值时会检查缓存，但是如果这部分不在缓存中，那么接下来查询返回的记录将不会被缓存，这意味着使用索引来限制查询集将不会填充缓存，如果这部分数据已经被缓存，则直接使用缓存中的数据

# 17.字段查询
# 实现where子名，作为方法filter()、exclude()、get()的参数
# 语法：属性名称__比较运算符=值
# 表示两个下划线，左侧是属性名称，右侧是比较类型
# 对于外键，使用“属性名_id”表示外键的原始值
# 转义：like语句中使用了%与，匹配数据中的%与，在过滤器中直接写，例如：filter(title__contains="%")=>where title like '%\%%'，表示查找标题中包含%的
# 比较运算符:
# exact：表示判等，大小写敏感；如果没有写“ 比较运算符”，表示判等
filter(isDelete=False)
# contains：是否包含，大小写敏感
exclude(btitle__contains='传')
# startswith、endswith：以value开头或结尾，大小写敏感
exclude(btitle__endswith='传')
# isnull、isnotnull：是否为null
filter(btitle__isnull=False)
# 在前面加个i表示不区分大小写，如iexact、icontains、istarswith、iendswith
# in：是否包含在范围内
filter(pk__in=[1, 2, 3, 4, 5])
# gt、gte、lt、lte：大于、大于等于、小于、小于等于
filter(id__gt=3)
# year、month、day、week_day、hour、minute、second：对日期间类型的属性进行运算
filter(bpub_date__year=1980)
filter(bpub_date__gt=date(1980, 12, 31))
# 跨关联关系的查询：处理join查询
#	语法：模型类名 <属性名> <比较>
#	注：可以没有__<比较>部分，表示等于，结果同inner join
#	可返向使用，即在关联的两个模型中都可以使用
filter(heroinfo_ _hcontent_ _contains='八')
# 查询的快捷方式：pk，pk表示primary key，默认的主键是id
filter(pk__lt=6)

# 18. 聚合函数
# 使用aggregate()函数返回聚合函数的值
# 函数：Avg，Count，Max，Min，Sum
from django.db.models import Max
maxDate = list.aggregate(Max('bpub_date')
# count的一般用法：
count = list.count()

# 19. F对象
# 可以使用模型的字段A与字段B进行比较，如果A写在了等号的左边，则B出现在等号的右边，需要通过F对象构造
list.filter(bread__gte=F('bcommet'))  
# django支持对F()对象使用算数运算
list.filter(bread__gte=F('bcommet') * 2) 
# F()对象中还可以写作“模型类__列名”进行关联查询
list.filter(isDelete=F('heroinfo__isDelete')) 
# 对于date/time字段，可与timedelta()进行运算
list.filter(bpub_date__lt=F('bpub_date') + timedelta(days=1))
                         
# 20. Q对象
# 过滤器的方法中关键字参数查询，会合并为And进行
# 需要进行or查询，使用Q()对象
# Q对象(django.db.models.Q)用于封装一组关键字参数，这些关键字参数与“比较运算符”中的相同               
from django.db.models import Q
list.filter(Q(pk_ _lt=6))
# Q对象可以使用&（and）、|（or）操作符组合起来
# 当操作符应用在两个Q对象时，会产生一个新的Q对象
list.filter(pk_ _lt=6).filter(bcommet_ _gt=10)
list.filter(Q(pk_ _lt=6) | Q(bcommet_ _gt=10))
# 使用~（not）操作符在Q对象前表示取反   
list.filter(~Q(pk__lt=6))
# 可以使用&|~结合括号进行分组，构造做生意复杂的Q对象
# 过滤器函数可以传递一个或多个Q对象作为位置参数，如果有多个Q对象，这些参数的逻辑为and
# 过滤器函数可以混合使用Q对象和关键字参数，所有参数都将and在一起，Q对象必须位于关键字参数的前面                         
```

### 110. django视图

```python
# 视图接受Web请求并且返回Web响应
# 视图就是一个python函数，被定义在views.py中
# 响应可以是一张网页的HTML内容，一个重定向，一个404错误等等：

# 1. URLconf
# 在settings.py文件中通过ROOT_URLCONF指定根级url的配置
# urlpatterns是一个url()实例的列表
# 一个url()对象包括：
#	正则表达式
#	视图函数
#	名称name
# 编写URLconf的注意：
#	若要从url中捕获一个值，需要在它周围设置一对圆括号
#	不需要添加一个前导的反斜杠，如应该写作'test/'，而不应该写作'/test/'
#	每个正则表达式前面的r表示字符串不转义
# 请求的url被看做是一个普通的python字符串，进行匹配时不包括get或post请求的参数及域名
# http://www.itcast.cn/python/1/?i=1&p=new，只匹配“/python/1/”部分
# 正则表达式非命名组，通过位置参数传递给视图
url(r'^([0-9]+)/$', views.detail, name='detail'),
# 正则表达式命名组，通过关键字参数传递给视图，本例中关键字参数为id
url(r'^(?P<id>[0-9]+)/$', views.detail, name='detail'),
# 参数匹配规则：优先使用命名参数，如果没有命名参数则使用位置参数
# 每个捕获的参数都作为一个普通的python字符串传递给视图
# 性能：urlpatterns中的每个正则表达式在第一次访问它们时被编译，这使得系统相当快

# 2.包含其它的URLconfs
# 在应用中创建urls.py文件，定义本应用中的urlconf，再在项目的settings中使用include()
from django.conf.urls import include, url
urlpatterns = [
    url(r'^', include('booktest.urls', namespace='booktest')),
]
# 匹配过程：先与主URLconf匹配，成功后再用剩余的部分与应用中的URLconf匹配
#	请求http://www.itcast.cn/booktest/1/
#	在sesstings.py中的配置：
#	url(r'^booktest/', include('booktest.urls', namespace='booktest')),
#	在booktest应用urls.py中的配置
#	url(r'^([0-9]+)/$', views.detail, name='detail'),
#	匹配部分是：/booktest/1/
#	匹配过程：在settings.py中与“booktest/”成功，再用“1/”与booktest应用的urls匹配
# 使用include可以去除urlconf的冗余
# 参数：视图会收到来自父URLconf、当前URLconf捕获的所有参数
# 在include中通过namespace定义命名空间，用于反解析

# 3. URL的反向解析
# 如果在视图、模板中使用硬编码的链接，在urlconf发生改变时，维护是一件非常麻烦的事情
# 解决：在做链接时，通过指向urlconf的名称，动态生成链接地址
# 视图：使用django.core.urlresolvers.reverse()函数
# 模板：使用url模板标签

# 4. 定义视图
# 本质就是一个函数
# 视图的参数
#	一个HttpRequest实例
#	通过正则表达式组获取的位置参数
#	通过正则表达式组获得的关键字参数
# 在应用目录下默认有views.py文件，一般视图都定义在这个文件中
# 如果处理功能过多，可以将函数定义到不同的py文件中

# 新建views1.py
#coding:utf-8
from django.http import HttpResponse
def index(request):
    return HttpResponse("你好")

在urls.py中修改配置
from . import views1
url(r'^$', views1.index, name='index'),

# 5. 错误视图
# 404 (page not found) 视图
#	defaults.page_not_found(request, template_name='404.html')
#	默认的404视图将传递一个变量给模板：request_path，它是导致错误的URL
#	如果Django在检测URLconf中的每个正则表达式后没有找到匹配的内容也将调用404视图
#	如果在settings中DEBUG设置为True，那么将永远不会调用404视图，而是显示URLconf 并带有一些调试信息
#	在templates中创建404.html
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
找不到了
<hr/>
{{request_path}}
</body>
</html>
# 在settings.py中修改调试
DEBUG = False
ALLOWED_HOSTS = ['*', ]
# 请求一个不存在的地址
http://127.0.0.1:8000/test/
# 500 (server error) 视图
#	defaults.server_error(request, template_name='500.html')
#	在视图代码中出现运行时错误
#	默认的500视图不会传递变量给500.html模板
#	如果在settings中DEBUG设置为True，那么将永远不会调用505视图，而是显示URLconf 并带有一些调试信息
# 400 (bad request) 视图
#	defaults.bad_request(request, template_name='400.html')
#	错误来自客户端的操作
#	当用户进行的操作在安全方面可疑的时候，例如篡改会话cookie

```

### 111. HttpReqeust对象

```python
# 服务器接收到http协议的请求后，会根据报文创建HttpRequest对象
# 视图函数的第一个参数是HttpRequest对象
# 在django.http模块中定义了HttpRequest对象的API

# 属性
# 下面除非特别说明，属性都是只读的
# path：一个字符串，表示请求的页面的完整路径，不包含域名
# method：一个字符串，表示请求使用的HTTP方法，常用值包括：'GET'、'POST'
# encoding：一个字符串，表示提交的数据的编码方式
# 如果为None则表示使用浏览器的默认设置，一般为utf-8
# 这个属性是可写的，可以通过修改它来修改访问表单数据使用的编码，接下来对属性的任何访问将使用新的encoding值
# GET：一个类似于字典的对象，包含get请求方式的所有参数
# POST：一个类似于字典的对象，包含post请求方式的所有参数
# FILES：一个类似于字典的对象，包含所有的上传文件
# COOKIES：一个标准的Python字典，包含所有的cookie，键和值都为字符串
# session：一个既可读又可写的类似于字典的对象，表示当前的会话，只有当Django 启用会话的支持时才可用

# 方法
# is_ajax()：如果请求是通过XMLHttpRequest发起的，则返回True

# QueryDict对象
# 定义在django.http.QueryDict
# request对象的属性GET、POST都是QueryDict类型的对象
# 与python字典不同，QueryDict类型的对象用来处理同一个键带有多个值的情况
#方法get()：根据键获取值
#	只能获取键的一个值
#	如果一个键同时拥有多个值，获取最后一个值
dict.get('键',default)
或简写为
dict['键']
# 方法getlist()：根据键获取值
#	将键的值以列表返回，可以获取一个键的多个值
dict.getlist('键',default)

# GET属性
# QueryDict类型的对象
# 包含get请求方式的所有参数
# 与url请求地址中的参数对应，位于?后面
# 参数的格式是键值对，如key1=value1
# 多个参数之间，使用&连接，如key1=value1&key2=value2
# 键是开发人员定下来的，值是可变的
# 示例如下
# 创建视图getTest1用于定义链接，getTest2用于接收一键一值，getTest3用于接收一键多值
def getTest1(request):
    return render(request,'booktest/getTest1.html')
def getTest2(request):
    return render(request,'booktest/getTest2.html')
def getTest3(request):
    return render(request,'booktest/getTest3.html')
# 配置url
url(r'^getTest1/$', views.getTest1),
url(r'^getTest2/$', views.getTest2),
url(r'^getTest3/$', views.getTest3),
# 创建getTest1.html，定义链接
<html>
<head>
    <title>Title</title>
</head>
<body>
链接1：一个键传递一个值
<a href="/getTest2/?a=1&b=2">gettest2</a><br>
链接2：一个键传递多个值
<a href="/getTest3/?a=1&a=2&b=3">gettest3</a>
</body>
</html>
# 完善视图getTest2的代码
def getTest2(request):
    a=request.GET['a']
    b=request.GET['b']
    context={'a':a,'b':b}
    return render(request,'booktest/getTest2.html',context)
# 创建getTest2.html，显示接收结果
<html>
<head>
    <title>Title</title>
</head>
<body>
a:{{ a }}<br>
b:{{ b }}
</body>
</html>
# 完善视图getTest3的代码
def getTest3(request):
    a=request.GET.getlist('a')
    b=request.GET['b']
    context={'a':a,'b':b}
    return render(request,'booktest/getTest3.html',context)
# 创建getTest3.html，显示接收结果
<html>
<head>
    <title>Title</title>
</head>
<body>
a:{% for item in a %}
{{ item }}
{% endfor %}
<br>
b:{{ b }}
</body>
</html>

# POST属性
# QueryDict类型的对象
# 包含post请求方式的所有参数
# 与form表单中的控件对应
# 问：表单中哪些控件会被提交？
# 答：控件要有name属性，则name属性的值为键，value属性的值为键，构成键值对提交
#	对于checkbox控件，name属性一样为一组，当控件被选中后会被提交，存在一键多值的情况
# 键是开发人员定下来的，值是可变的
# 示例如下
# 定义视图postTest1
def postTest1(request):
    return render(request,'booktest/postTest1.html')
# 配置url
url(r'^postTest1$',views.postTest1)
# 创建模板postTest1.html
<html>
<head>
    <title>Title</title>
</head>
<body>
<form method="post" action="/postTest2/">
    姓名：<input type="text" name="uname"/><br>
    密码：<input type="password" name="upwd"/><br>
    性别：<input type="radio" name="ugender" value="1"/>男
    <input type="radio" name="ugender" value="0"/>女<br>
    爱好：<input type="checkbox" name="uhobby" value="胸口碎大石"/>胸口碎大石
    <input type="checkbox" name="uhobby" value="跳楼"/>跳楼
    <input type="checkbox" name="uhobby" value="喝酒"/>喝酒
    <input type="checkbox" name="uhobby" value="爬山"/>爬山<br>
    <input type="submit" value="提交"/>
</form>
</body>
</html>
# 创建视图postTest2接收请求的数据
def postTest2(request):
    uname=request.POST['uname']
    upwd=request.POST['upwd']
    ugender=request.POST['ugender']
    uhobby=request.POST.getlist('uhobby')
    context={'uname':uname,'upwd':upwd,'ugender':ugender,'uhobby':uhobby}
    return render(request,'booktest/postTest2.html',context)
# 配置url
url(r'^postTest2$',views.postTest2)
# 创建模板postTest2.html
<html>
<head>
    <title>Title</title>
</head>
<body>
{{ uname }}<br>
{{ upwd }}<br>
{{ ugender }}<br>
{{ uhobby }}
</body>
</html>
# 注意：使用表单提交，注释掉settings.py中的中间件crsf
```

### 112. HttpResponse对象

```python
# 在django.http模块中定义了HttpResponse对象的API
# HttpRequest对象由Django自动创建，HttpResponse对象由程序员创建
# 不调用模板，直接返回数据
from django.http import HttpResponse

def index(request):
    return HttpResponse('你好')
# 调用模板
from django.http import HttpResponse
from django.template import RequestContext, loader

def index(request):
    t1 = loader.get_template('polls/index.html')
    context = RequestContext(request, {'h1': 'hello'})
    return HttpResponse(t1.render(context))
# 属性
# content：表示返回的内容，字符串类型
# charset：表示response采用的编码字符集，字符串类型
# status_code：响应的HTTP响应状态码
# content-type：指定输出的MIME类型
# 方法
# init ：使用页内容实例化HttpResponse对象
# write(content)：以文件的方式写
# flush()：以文件的方式输出缓存区
# set_cookie(key, value='', max_age=None, expires=None)：设置Cookie
#	key、value都是字符串类型
#	max_age是一个整数，表示在指定秒数后过期
#	expires是一个datetime或timedelta对象，会话将在这个指定的日期/时间过期，注意datetime和timedelta值只有在使用PickleSerializer时才可序列化
#	max_age与expires二选一
#	如果不指定过期时间，则两个星期后过期
from django.http import HttpResponse
from datetime import *

def index(request):
    response = HttpResponse()
    if request.COOKIES.has_key('h1'):
        response.write('<h1>' + request.COOKIES['h1'] + '</h1>')
    response.set_cookie('h1', '你好', 120)
    # response.set_cookie('h1', '你好', None, datetime(2016, 10, 31))
    return response
# delete_cookie(key)：删除指定的key的Cookie，如果key不存在则什么也不发生


# 子类HttpResponseRedirect
# 重定向，服务器端跳转
# 构造函数的第一个参数用来指定重定向的地址
在views1.py中
from django.http import HttpResponse,HttpResponseRedirect
def index(request):
    return HttpResponseRedirect('js/')
def index2(request,id):
    return HttpResponse(id)

在应用的urls.py中增加一个url对象
url(r'^([0-9]+)/$', views1.index2, name='index2'),
# 推荐使用反向解析
from django.core.urlresolvers import reverse

def index(request):
    return HttpResponseRedirect(reverse('booktest:index2', args=(1,)))

# 子类JsonResponse
# 返回json数据，一般用于异步请求
# _init _(data)
# 帮助用户创建JSON编码的响应
# 参数data是字典对象
# JsonResponse的默认Content-Type为application/json
from django.http import JsonResponse

def index2(requeset):
    return JsonResponse({'list': 'abc'})

# render
# render(request, template_name[, context])
# 结合一个给定的模板和一个给定的上下文字典，并返回一个渲染后的HttpResponse对象
# request：该request用于生成response
# template_name：要使用的模板的完整名称
# context：添加到模板上下文的一个字典，视图将在渲染模板之前调用它
from django.shortcuts import render

def index(request):
    return render(request, 'booktest/index.html', {'h1': 'hello'})

# 重定向
# redirect(to)
# 为传递进来的参数返回HttpResponseRedirect
# to推荐使用反向解析
from django.shortcuts import redirect
from django.core.urlresolvers import reverse

def index(request):
    return redirect(reverse('booktest:index2'))

# 得到对象或返回404
# get_object_or_404(klass, args, *kwargs)
# 通过模型管理器或查询集调用get()方法，如果没找到对象，不引发模型的DoesNotExist异常，而是引发Http404异常
# klass：获取对象的模型类、Manager对象或QuerySet对象
# **kwargs：查询的参数，格式应该可以被get()和filter()接受
# 如果找到多个对象将引发MultipleObjectsReturned异常
from django.shortcuts import *

def detail(request, id):
    try:
        book = get_object_or_404(BookInfo, pk=id)
    except BookInfo.MultipleObjectsReturned:
        book = None
    return render(request, 'booktest/detail.html', {'book': book})

# 将settings.py中的DEBUG改为False
# 将请求地址输入2和100查看效果

# 得到列表或返回404
# get_list_or_404(klass, args, *kwargs)
# klass：获取列表的一个Model、Manager或QuerySet实例
# **kwargs：查寻的参数，格式应该可以被get()和filter()接受
from django.shortcuts import *

def index(request):
    # list = get_list_or_404(BookInfo, pk__lt=1)
    list = get_list_or_404(BookInfo, pk__lt=6)
    return render(request, 'booktest/index.html', {'list': list})

# 将settings.py中的DEBUG改为False
```

### 113. Session

```python
# 状态保持
# http协议是无状态的：每次请求都是一次新的请求，不会记得之前通信的状态
# 客户端与服务器端的一次通信，就是一次会话
# 实现状态保持的方式：在客户端或服务器端存储与会话有关的数据
# 存储方式包括cookie、session，会话一般指session对象
# 使用cookie，所有数据存储在客户端，注意不要存储敏感信息
# 推荐使用sesison方式，所有数据存储在服务器端，在客户端cookie中存储session_id
# 状态保持的目的是在一段时间内跟踪请求者的状态，可以实现跨页面访问当前请求者的数据
# 注意：不同的请求者之间不会共享这个数据，与请求者一一对应

# 启用session
# 使用django-admin startproject创建的项目默认启用
# 在settings.py文件中
# INSTALLED_APPS列表中添加：
'django.contrib.sessions',

# MIDDLEWARE_CLASSES列表中添加：
'django.contrib.sessions.middleware.SessionMiddleware',
# 禁用会话：删除上面指定的两个值，禁用会话将节省一些性能消耗

# 使用session
# 启用会话后，每个HttpRequest对象将具有一个session属性，它是一个类字典对象
# get(key, default=None)：根据键获取会话的值
# clear()：清除所有会话
# flush()：删除当前的会话数据并删除会话的Cookie
# del request.session['member_id']：删除会话

# 在views.py文件中创建视图
from django.shortcuts import render, redirect
from django.core.urlresolvers import reverse

def index(request):
    uname = request.session.get('uname')
    return render(request, 'booktest/index.html', {'uname': uname})

def login(request):
    return render(request, 'booktest/login.html')

def login_handle(request):
    request.session['uname'] = request.POST['uname']
    return redirect(reverse('main:index'))

def logout(request):
    # request.session['uname'] = None
    # del request.session['uname']
    # request.session.clear()
    request.session.flush()
    return redirect(reverse('main:index'))
# 配置url
主url：
from django.conf.urls import include, url
urlpatterns = [
    url(r'^', include('booktest.urls', namespace='main'))
]

应用url：
from django.conf.urls import url
from . import views
urlpatterns = [
    url(r'^$', views.index, name='index'),
    url(r'login/$', views.login, name='login'),
    url(r'login_handle/$', views.login_handle, name='login_handle'),
    url(r'logout/$', views.logout, name='logout')
]
# 创建模板index.html
<!DOCTYPE html>
<html>
<head>
    <title>首页</title>
</head>
<body>
你好：{{uname}}
<hr/>
<a href="{%url 'main:login'%}">登录</a>
<hr/>
<a href="{%url 'main:logout'%}">退出</a>
</body>
</html>
# 创建模板login.html
<!DOCTYPE html>
<html>
<head>
    <title>登录</title>
</head>
<body>
<form method="post" action="/login_handle/">
    <input type="text" name="uname"/>
    <input type="submit" value="登录"/>
</form>
</body>
</html>
# 会话过期时间
# set_expiry(value)：设置会话的超时时间
# 如果没有指定，则两个星期后过期
# 如果value是一个整数，会话将在values秒没有活动后过期
# 若果value是一个imedelta对象，会话将在当前时间加上这个指定的日期/时间过期
# 如果value为0，那么用户会话的Cookie将在用户的浏览器关闭时过期
# 如果value为None，那么会话永不过期
# 修改视图中login_handle函数，查看效果
def login_handle(request):
    request.session['uname'] = request.POST['uname']
    # request.session.set_expiry(10)
    # request.session.set_expiry(timedelta(days=5))
    # request.session.set_expiry(0)
    # request.session.set_expiry(None)
    return redirect(reverse('main:index'))
# 存储session
# 使用存储会话的方式，可以使用settings.py的SESSION_ENGINE项指定
# 基于数据库的会话：这是django默认的会话存储方式，需要添加django.contrib.sessions到的INSTALLED_APPS设置中，运行manage.py migrate在数据库中安装会话表，可显示指定为
SESSION_ENGINE='django.contrib.sessions.backends.db'
# 基于缓存的会话：只存在本地内在中，如果丢失则不能找回，比数据库的方式读写更快
SESSION_ENGINE='django.contrib.sessions.backends.cache'
# 可以将缓存和数据库同时使用：优先从本地缓存中获取，如果没有则从数据库中获取
SESSION_ENGINE='django.contrib.sessions.backends.cached_db'


# 使用Redis缓存session
# 会话还支持文件、纯cookie、Memcached、Redis等方式存储，下面演示使用redis存储
# 安装包
pip install django-redis-sessions
# 修改settings中的配置，增加如下项
SESSION_ENGINE = 'redis_sessions.session'
SESSION_REDIS_HOST = 'localhost'
SESSION_REDIS_PORT = 6379
SESSION_REDIS_DB = 0
SESSION_REDIS_PASSWORD = ''
SESSION_REDIS_PREFIX = 'session'
# 管理redis的命令
启动：sudo redis-server /etc/redis/redis.conf
停止：sudo redis-server stop
重启：sudo redis-server restart
redis-cli：使用客户端连接服务器
keys *：查看所有的键
get name：获取指定键的值
del name：删除指定名称的键
```

### 114. django模板

```python
# 作为Web框架，Django提供了模板，可以很便利的动态生成HTML
# 模版系统致力于表达外观，而不是程序逻辑
# 模板的设计实现了业务逻辑(view)与显示内容（template）的分离，一个视图可以使用任意一个模板，一个模板可以供# 多个视图使用
# 模板包含
#	HTML的静态部分
#	动态插入内容部分
# Django模板语言，简写DTL，定义在django.template包中
# 由startproject命令生成的settings.py定义关于模板的值：
#	DIRS定义了一个目录列表，模板引擎按列表顺序搜索这些目录以查找模板源文件
#	APP_DIRS告诉模板引擎是否应该在每个已安装的应用中查找模板
# 常用方式：在项目的根目录下创建templates目录，设置DIRS值
DIRS=[os.path.join(BASE_DIR,"templates")]

# Django处理模板分为两个阶段
# Step1 加载：根据给定的标识找到模板然后预处理，通常会将它编译好放在内存中
loader.get_template(template_name)，返回一个Template对象
# Step2 渲染：使用Context数据对模板插值并返回生成的字符串
Template对象的render(RequestContext)方法，使用context渲染模板
# 加载渲染完整代码：
from django.template import loader, RequestContext
from django.http import HttpResponse

def index(request):
    tem = loader.get_template('temtest/index.html')
    context = RequestContext(request, {})
    return HttpResponse(tem.render(context))

# 为了减少加载模板、渲染模板的重复代码，django提供了快捷函数
# render_to_string("")
# render(request,'模板',context)
from django.shortcuts import render

def index(request):
    return render(request, 'temtest/index.html')


# 模板语言包括
#	变量
#	标签 { % 代码块 % }
#	过滤器
#	注释{# 代码或html #}
# 1. 变量
# 语法:
{{ variable }}
# 当模版引擎遇到一个变量，将计算这个变量，然后将结果输出
# 变量名必须由字母、数字、下划线（不能以下划线开头）和点组成
# 当模版引擎遇到点(".")，会按照下列顺序查询：
#	字典查询，例如：foo["bar"]
#	属性或方法查询，例如：foo.bar
#	数字索引查询，例如：foo[bar]
# 如果变量不存在， 模版系统将插入'' (空字符串)
# 在模板中调用方法时不能传递参数

# 2. 在模板中调用对象的方法
# 在models.py中定义类HeroInfo
from django.db import models

class HeroInfo(models.Model):
    ...
    def showName(self):
        return self.hname
# 在views.py中传递HeroInfo对象
from django.shortcuts import render
from models import *

def index(request):
    hero = HeroInfo(hname='abc')
    context = {'hero': hero}
    return render(request, 'temtest/detail.html', context)
# 在模板detail.html中调用
{{hero.showName}}

# 3. 标签
# 语法：
{ % tag % }
# 作 用:
#	在输出中创建文本
#	控制循环或逻辑
#	加载外部信息到模板中供以后的变量使用
# for标签:
{ %for ... in ...%}
循环逻辑
{{forloop.counter}}表示当前是第几次循环
{ %empty%}
给出的列表为或列表不存在时，执行此处
{ %endfor%}
# if标签:
{ %if ...%}
逻辑1
{ %elif ...%}
逻辑2
{ %else%}
逻辑3
{ %endif%}
# comment标签:
{ % comment % }
多行注释
{ % endcomment % }
# include：加载模板并以标签内的参数渲染:
{ %include "foo/bar.html" % }
# url：反向解析(动态生成url):
{ % url 'namespace:name' p1 p2 %}
# csrf_token：这个标签用于跨站请求伪造保护:
{ % csrf_token %}
# 布尔标签：and、or，and比or的优先级高


# 4.过滤器
# 语法：
{ { 变量|过滤器 }}，例如{ { name|lower }}，表示将变量name的值变为小写输出
# 使用管道符号 (|)来应用过滤器
# 通过使用过滤器来改变变量的计算结果
# 可以在if标签中使用过滤器结合运算符
if list1|length > 1
# 过滤器能够被“串联”，构成过滤器链:
name|lower|upper
# 过滤器可以传递参数，参数使用引号包起来:
list|join:", "
# default：如果一个变量没有被提供，或者值为false或空，则使用默认值，否则使用变量的值:
value|default:"什么也没有"
# date：根据给定格式对一个date变量格式化:
value|date:'Y-m-d'
    

# 5. 注释
# 单行注释:
{#...#}
# 注释可以包含任何模版代码，有效的或者无效的都可以:
{# { % if foo % }bar{ % else % } #}
# 使用comment标签注释模版中的多行内容 
    
    
    
#  模板继承
# 模板继承可以减少页面内容的重复定义，实现页面内容的重用
# 典型应用：网站的头部、尾部是一样的，这些内容可以定义在父模板中，子模板不需要重复定义
# block标签：在父模板中预留区域，在子模板中填充
# extends继承：继承，写在模板文件的第一行
# 定义父模板base.html
{ %block block_name%}
这里可以定义默认值
如果不定义默认值，则表示空字符串
{ %endblock%}
# 定义子模板index.html
{ % extends "base.html" %}
# 在子模板中使用block填充预留区域
{ %block block_name%}
实际填充内容
{ %endblock%} 

# 说明:
# 如果在模版中使用extends标签，它必须是模版中的第一个标签
# 不能在一个模版中定义多个相同名字的block标签
# 子模版不必定义全部父模版中的blocks，如果子模版没有定义block，则使用了父模版中的默认值
# 如果发现在模板中大量的复制内容，那就应该把内容移动到父模板中
# 使用可以获取父模板中block的内容
# 为了更好的可读性，可以给endblock标签一个名字
{ % block block_name %}
区域内容
{ % endblock block_name %} 


# HTML转义
# Django对字符串进行自动HTML转义，如在模板中输出如下值：
def index(request):
    return render(request, 'temtest/index2.html',
                  {
                      't1': '<h1>hello</h1>'
                  })
# 模板代码：
{{t1}}
# 显示效果如下图：
<h1>hello</h1>    
# 会被自动转义的字符
# html转义，就是将包含的html标签输出，而不被解释执行，原因是当显示用户提交字符串时，可能包含一些攻击性的代码，如js脚本
# Django会将如下字符自动转义：
< 会转换为&lt;
> 会转换为&gt;
' (单引号) 会转换为&#39;
" (双引号)会转换为 &quot;
& 会转换为 &amp; 
# 当显示不被信任的变量时使用escape过滤器，一般省略，因为Django自动转义    
{{ data|safe }}
# 对于代码块使用autoescape标签
{ % autoescape off %}
{{ body }}
{ % endautoescape %}
# 标签autoescape接受on或者off参数
# 自动转义标签在base模板中关闭，在child模板中也是关闭的
# 字符串字面值
# 手动转义:
{ { data|default:"<b>123</b>" }}
# 应写为:
{ { data|default:"&lt;b&gt;123&lt;/b&gt;" }} 
    
    
# csrf    
# 全称Cross Site Request Forgery，跨站请求伪造
# 某些恶意网站上包含链接、表单按钮或者JavaScript，它们会利用登录过的用户在浏览器中的认证信息试图在你的网站上完成某些操作，这就是跨站攻击
# 防csrf的使用
# 在django的模板中，提供了防止跨站攻击的方法，使用步骤如下：
# step1：在settings.py中启用'django.middleware.csrf.CsrfViewMiddleware'中间件，此项在创建项目时，默认被启用
# step2：在csrf1.html中添加标签
<form>
{% csrf_token %}
...
</form>
# step3：测试刚才的两个请求，发现跨站的请求被拒绝了，效果如下图 

# 取消保护
# 如果某些视图不需要保护，可以使用装饰器csrf_exempt，模板中也不需要写标签，修改csrf2的视图如下
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def csrf2(request):
    uname=request.POST['uname']
    return render(request,'booktest/csrf2.html',{'uname':uname})   

 # 保护原理
 # 加入标签后，可以查看源代码，发现多了如下代码
 <input type='hidden' name='csrfmiddlewaretoken' value='nGjAB3Md9ZSb4NmG1sXDolPmh3bR2g59' />
 
# 验证码
# 在用户注册、登录页面，为了防止暴力请求，可以加入验证码功能，如果验证码错误，则不需要继续处理，可以减轻一些服务器的压力
# 使用验证码也是一种有效的防止crsf的方法

# 验证码视图
# 新建viewsUtil.py，定义函数verifycode
pip install pillow    
# 此段代码用到了PIL中的Image、ImageDraw、ImageFont模块，需要先安装Pillow（3.4.1）包
# Image表示画布对象
# ImageDraw表示画笔对象
# ImageFont表示字体对象，ubuntu的字体路径为“/usr/share/fonts/truetype/freefont” 
from django.http import HttpResponse
def verifycode(request):
    #引入绘图模块
    from PIL import Image, ImageDraw, ImageFont
    #引入随机函数模块
    import random
    #定义变量，用于画面的背景色、宽、高
    bgcolor = (random.randrange(20, 100), random.randrange(
        20, 100), 255)
    width = 100
    height = 25
    #创建画面对象
    im = Image.new('RGB', (width, height), bgcolor)
    #创建画笔对象
    draw = ImageDraw.Draw(im)
    #调用画笔的point()函数绘制噪点
    for i in range(0, 100):
        xy = (random.randrange(0, width), random.randrange(0, height))
        fill = (random.randrange(0, 255), 255, random.randrange(0, 255))
        draw.point(xy, fill=fill)
    #定义验证码的备选值
    str1 = 'ABCD123EFGHIJK456LMNOPQRS789TUVWXYZ0'
    #随机选取4个值作为验证码
    rand_str = ''
    for i in range(0, 4):
        rand_str += str1[random.randrange(0, len(str1))]
    #构造字体对象
    font = ImageFont.truetype('FreeMono.ttf', 23)
    #构造字体颜色
    fontcolor = (255, random.randrange(0, 255), random.randrange(0, 255))
    #绘制4个字
    draw.text((5, 2), rand_str[0], font=font, fill=fontcolor)
    draw.text((25, 2), rand_str[1], font=font, fill=fontcolor)
    draw.text((50, 2), rand_str[2], font=font, fill=fontcolor)
    draw.text((75, 2), rand_str[3], font=font, fill=fontcolor)
    #释放画笔
    del draw
    #存入session，用于做进一步验证
    request.session['verifycode'] = rand_str
    #内存文件操作
    import cStringIO
    buf = cStringIO.StringIO()
    #将图片保存在内存中，文件类型为png
    im.save(buf, 'png')
    #将内存中的图片数据返回给客户端，MIME类型为图片png
    return HttpResponse(buf.getvalue(), 'image/png')
# 配置url
# 在urls.py中定义请求验证码视图的url
from . import viewsUtil

urlpatterns = [
    url(r'^verifycode/$', viewsUtil.verifycode),
]
# 显示验证码
# 在模板中使用img标签，src指向验证码视图
<img id='verifycode' src="/verifycode/" alt="CheckCode"/>
# 启动服务器，查看显示成功
# 扩展：点击“看不清，换一个”时，可以换一个新的验证码 
<script type="text/javascript" src="/static/jquery-1.12.4.min.js"></script>
<script type="text/javascript">
    $(function(){
        $('#verifycodeChange').css('cursor','pointer').click(function() {
            $('#verifycode').attr('src',$('#verifycode').attr('src')+1)
        });
    });
</script>
<img id='verifycode' src="/verifycode/?1" alt="CheckCode"/>
<span id='verifycodeChange'>看不清，换一个</span>
# 为了能够实现提交功能，需要增加form和input标签
<form method='post' action='/verifycodeValid/'>
    <input type="text" name="vc">
    <img id='verifycode' src="/verifycode/?1" alt="CheckCode"/>
<span id='verifycodeChange'>看不清，换一个</span>
<br>
<input type="submit" value="提交">
</form>
# 验证
# 接收请求的信息，与session中的内容对比
from django.http import HttpResponse

def verifycodeValid(request):
    vc = request.POST['vc']
    if vc.upper() == request.session['verifycode']:
        return HttpResponse('ok')
    else:
        return HttpResponse('no')
# 配置验证处理的url 
urlpatterns = [
    url(r'^verifycodeValid/$', views.verifycodeValid),
]    
```

### 115. django高级

```python
# 1. 管理静态文件
# 配置静态文件,在settings 文件中定义静态内容
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]
# 在项目根目录下创建static目录，再创建当前应用名称的目录
mysite/static/myapp/
# 在模板中可以使用硬编码
/static/my_app/myexample.jpg
# 在模板中可以使用static编码
{ % load static from staticfiles %}
<img src="{ % static "my_app/myexample.jpg" %}" alt="My image"/>

# 中间件
# 是一个轻量级、底层的插件系统，可以介入Django的请求和响应处理过程，修改Django的输入或输出
# 激活：添加到Django配置文件中的MIDDLEWARE_CLASSES元组中
# 每个中间件组件是一个独立的Python类，可以定义下面方法中的一个或多个
#	_init _：无需任何参数，服务器响应第一个请求的时候调用一次，用于确定是否启用当前中间件
#	process_request(request)：执行视图之前被调用，在每个请求上调用，返回None或HttpResponse对象
#	process_view(request, view_func, view_args, view_kwargs)：调用视图之前被调用，在每个请求上调用，返回None或HttpResponse对象
#	process_template_response(request, response)：在视图刚好执行完毕之后被调用，在每个请求上调用，返回实现了render方法的响应对象
#	process_response(request, response)：所有响应返回浏览器之前被调用，在每个请求上调用，返回HttpResponse对象
#	process_exception(request,response,exception)：当视图抛出异常时调用，在每个请求上调用，返回一个HttpResponse对象
# 使用中间件，可以干扰整个处理过程，每次请求中都会执行中间件的这个方法
# 示例：自定义异常处理
# 与settings.py同级目录下创建myexception.py文件，定义类MyException，实现process_exception方法
from django.http import HttpResponse
class MyException():
    def process_exception(request,response, exception):
        return HttpResponse(exception.message)
# 将类MyException注册到settings.py中间件中
MIDDLEWARE_CLASSES = (
    'test1.myexception.MyException',
    ...
)
# 定义视图，并发生一个异常信息，则会运行自定义的异常处理

# 3.上传图片
# 当Django在处理文件上传的时候，文件数据被保存在request.FILES
# FILES中的每个键为<input type="file" name="" />中的name
# 注意：FILES只有在请求的方法为POST 且提交的<form>带有enctype="multipart/form-data" 的情况下才会包含数据。否则，FILES 将为一个空的类似于字典的对象
# 使用模型处理上传文件：将属性定义成models.ImageField类型
pic=models.ImageField(upload_to='cars/')
# 注意：如果属性类型为ImageField需要安装包Pilow
pip install Pillow==3.4.1
# 图片存储路径
#	在项目根目录下创建media文件夹
#	图片上传后，会被保存到“/static/media/cars/图片文件”
#	打开settings.py文件，增加media_root项
 MEDIA_ROOT=os.path.join(BASE_DIR,"static/media")
# 使用django后台管理，遇到ImageField类型的属性会出现一个file框，完成文件上传
# 手动上传的模板代码
<html>
<head>
    <title>文件上传</title>
</head>
<body>
    <form method="post" action="upload/" enctype="multipart/form-data">
        <input type="text" name="title"><br>
        <input type="file" name="pic"/><br>
        <input type="submit" value="上传">
    </form>
</body>
</html>
# 手动上传的视图代码
from django.conf import settings

def upload(request):
    if request.method == "POST":
        f1 = request.FILES['pic']
        fname = '%s/cars/%s' % (settings.MEDIA_ROOT,f1.name)
        with open(fname, 'w') as pic:
            for c in f1.chunks():
                pic.write(c)
        return HttpResponse("ok")
    else:
        return HttpResponse("error")

# 4.Admin站点
# 通过使用startproject创建的项目模版中，默认Admin被启用
# 1.创建管理员的用户名和密码
python manage.py createsuperuser
# 2.在应用内admin.py文件完成注册，就可以在后台管理中维护模型的数据
from django.contrib import admin
from models import *

admin.site.register(HeroInfo)
# 查找admin文件：在INSTALLED_APPS项中加入django.contrib.admin，Django就会自动搜索每个应用的admin模块并将其导入
# ModelAdmin对象
# ModelAdmin类是模型在Admin界面中的表示形式
# 定义：定义一个类，继承于admin.ModelAdmin，注册模型时使用这个类
class HeroAdmin(admin.ModelAdmin):
    ...
# 通常定义在应用的admin.py文件里
# 使用方式一：注册参数  
admin.site.register(HeroInfo,HeroAdmin)
# 使用方式二：注册装饰器
@admin.register(HeroInfo)
class HeroAdmin(admin.ModelAdmin):
# 通过重写admin.ModelAdmin的属性规定显示效果，属性主要分为列表页、增加修改页两部分

# 列表页选项
# “操作选项”的位置
# actions_on_top、actions_on_bottom：默认显示在页面的顶部
class HeroAdmin(admin.ModelAdmin):
    actions_on_top = True
    actions_on_bottom = True
# list_display   
# 出现列表中显示的字段
# 列表类型
# 在列表中，可以是字段名称，也可以是方法名称，但是方法名称默认不能排序
# 在方法中可以使用format_html()输出html内容
在models.py文件中
from django.db import models
from tinymce.models import HTMLField
from django.utils.html import format_html

class HeroInfo(models.Model):
    hname = models.CharField(max_length=10)
    hcontent = HTMLField()
    isDelete = models.BooleanField()
    def hContent(self):
        return format_html(self.hcontent)

在admin.py文件中
class HeroAdmin(admin.ModelAdmin):
    list_display = ['hname', 'hContent']
# 让方法排序，为方法指定admin_order_field属性
在models.py中HeroInfo类的代码改为如下：
    def hContent(self):
        return format_html(self.hcontent)
    hContent.admin_order_field = 'hname'
# 标题栏名称：将字段封装成方法，为方法设置short_description属性
在models.py中为HeroInfo类增加方法hName：
    def hName(self):
        return self.hname
    hName.short_description = '姓名'
    hContent.short_description = '内容'

在admin.py页中注册
class HeroAdmin(admin.ModelAdmin):
    list_display = ['hName', 'hContent']
    
# list_filter 
# 右侧栏过滤器，对哪些属性的值进行过滤
# 列表类型
# 只能接收字段
class HeroAdmin(admin.ModelAdmin):
    ...
    list_filter = ['hname', 'hcontent']
    
# list_per_page
# 每页中显示多少项，默认设置为100
class HeroAdmin(admin.ModelAdmin):
    ...
    list_per_page = 10
 
# search_fields
# 搜索框
# 列表类型，表示在这些字段上进行搜索
# 只能接收字段
class HeroAdmin(admin.ModelAdmin):
    ...
    search_fields = ['hname']
    
# 增加与修改页选项
# fields：显示字段的顺序，如果使用元组表示显示到一行上
class HeroAdmin(admin.ModelAdmin):
    ...
    fields = [('hname', 'hcontent')]
# fieldsets：分组显示
class HeroAdmin(admin.ModelAdmin):
    ...
    fieldsets = (
        ('base', {'fields': ('hname')}),
        ('other', {'fields': ('hcontent')})
    )
# fields与fieldsets两者选一

# InlineModelAdmin对象
# 类型InlineModelAdmin：表示在模型的添加或修改页面嵌入关联模型的添加或修改
# 子类TabularInline：以表格的形式嵌入
# 子类StackedInline：以块的形式嵌入
class HeroInline(admin.TabularInline):
    model = HeroInfo

class BookAdmin(admin.ModelAdmin):
    inlines = [
        HeroInline,
    ]
# 重写admin模板
# 在项目所在目录中创建templates目录，再创建一个admin目录
# 设置模板查找目录：修改settings.py的TEMPLATES项，加载模板时会在DIRS列表指定的目录中搜索
'DIRS': [os.path.join(BASE_DIR, 'templates')],
# 从Django安装的目录下（django/contrib/admin/templates）将模板页面的源文件admin/base_site.html拷贝到第一步建好的目录里
# 编辑base_site.html文件
# 刷新页面，发现以刚才编辑的页面效果显示
# 其它管理后台的模板可以按照相同的方式进行修改

# 分页
# Django提供了一些类实现管理数据分页，这些类位于django/core/paginator.py中
# Paginator对象
# Paginator(列表,int)：返回分页对象，参数为列表数据，每面数据的条数

# 属性:
#	count：对象总数
#	num_pages：页面总数
#	page_range：页码列表，从1开始，例如[1, 2, 3, 4]
# 方法:
# page(num)：下标以1开始，如果提供的页码不存在，抛出InvalidPage异常
# 异常exception:
# InvalidPage：当向page()传入一个无效的页码时抛出
# PageNotAnInteger：当向page()传入一个不是整数的值时抛出
# EmptyPage：当向page()提供一个有效值，但是那个页面上没有任何对象时抛出

# Page对象
# 创建对象
# Paginator对象的page()方法返回Page对象，不需要手动构造
# 属性:
#	object_list：当前页上所有对象的列表
#	number：当前页的序号，从1开始
#	paginator：当前page对象相关的Paginator对象
# 方法:
#	has_next()：如果有下一页返回True
#	has_previous()：如果有上一页返回True
#	has_other_pages()：如果有上一页或下一页返回True
#	next_page_number()：返回下一页的页码，如果下一页不存在，抛出InvalidPage异常
#	previous_page_number()：返回上一页的页码，如果上一页不存在，抛出InvalidPage异常
#	len()：返回当前页面对象的个数
#	迭代页面对象：访问当前页面中的每个对象

# 示例:
# 创建视图pagTest
from django.core.paginator import Paginator

def pagTest(request, pIndex):
    list1 = AreaInfo.objects.filter(aParent__isnull=True)
    p = Paginator(list1, 10)
    if pIndex == '':
        pIndex = '1'
    pIndex = int(pIndex)
    list2 = p.page(pIndex)
    plist = p.page_range
    return render(request, 'booktest/pagTest.html', {'list': list2, 'plist': plist, 'pIndex': pIndex})
# 配置url
url(r'^pag(?P<pIndex>[0-9]*)/$', views.pagTest, name='pagTest'),
# 定义模板pagTest.html
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
<ul>
{%for area in list%}
<li>{{area.id}}--{{area.atitle}}</li>
{%endfor%}
</ul>

{%for pindex in plist%}
{%if pIndex == pindex%}
{{pindex}}&nbsp;&nbsp;
{%else%}
<a href="/pag{{pindex}}/">{{pindex}}</a>&nbsp;&nbsp;
{%endif%}
{%endfor%}
</body>
</html>

```

### 116. django扩展

```python
pip install django-tinymce
# 1. 富文本编辑器
# 在网站https://pypi.org进入解压后的目录，工作在虚拟环境，安装搜索并下载"django-tinymce-2.4.0" 解压
tar zxvf django-tinymce-2.4.0.tar.gz
# 进入解压后的目录，工作在虚拟环境，安装
python setup.py install

# 应用到项目中
# 在settings.py中为INSTALLED_APPS添加编辑器应用
INSTALLED_APPS = (
    ...
    'tinymce',
)
# 在settings.py中添加编辑配置项
TINYMCE_DEFAULT_CONFIG = {
    'theme': 'advanced',
    'width': 600,
    'height': 400,
}
# 在根urls.py中配置
urlpatterns = [
    ...
    url(r'^tinymce/', include('tinymce.urls')),
]
# 在应用中定义模型的属性
from django.db import models
from tinymce.models import HTMLField

class HeroInfo(models.Model):
    ...
    hcontent = HTMLField()
# 在后台管理界面中，就会显示为富文本编辑器，而不是多行文本框 

# 自定义使用
# 定义视图editor，用于显示编辑器并完成提交
def editor(request):
    return render(request, 'other/editor.html')
# 配置url
urlpatterns = [
    ...
    url(r'^editor/$', views.editor, name='editor'),
]
# 创建模板editor.html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <script type="text/javascript" src='/static/tiny_mce/tiny_mce.js'></script>
    <script type="text/javascript">
        tinyMCE.init({
            'mode':'textareas',
            'theme':'advanced',
            'width':400,
            'height':100
        });
    </script>
</head>
<body>
<form method="post" action="/content/">
    <input type="text" name="hname">
    <br>
    <textarea name='hcontent'>哈哈，这是啥呀</textarea>
    <br>
    <input type="submit" value="提交">
</form>
</body>
</html>
# 定义视图content，接收请求，并更新heroinfo对象
def content(request):
    hname = request.POST['hname']
    hcontent = request.POST['hcontent']

    heroinfo = HeroInfo.objects.get(pk=1)
    heroinfo.hname = hname
    heroinfo.hcontent = hcontent
    heroinfo.save()

    return render(request, 'other/content.html', {'hero': heroinfo})
# 添加url项
urlpatterns = [
    ...
    url(r'^content/$', views.content, name='content'),
]
# 定义模板content.html
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
姓名：{{hero.hname}}
<hr>
{%autoescape off%}
{{hero.hcontent}}
{%endautoescape%}
</body>
</html>

# 2. 缓存
# 设置缓存
# 通过设置决定把数据缓存在哪里，是数据库中、文件系统还是在内存中
# 通过setting文件的CACHES配置来实现
# 参数TIMEOUT：缓存的默认过期时间，以秒为单位，这个参数默认是300秒，即5分钟；设置TIMEOUT为None表示永远不会过期，值设置成0造成缓存立即失效
CACHES={
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'TIMEOUT': 60,
    }
}
# 可以将cache存到redis中，默认采用1数据库，需要安装包并配置如下：
CACHES = {
    "default": {
        "BACKEND": "redis_cache.cache.RedisCache",
        "LOCATION": "localhost:6379",
        'TIMEOUT': 60,
    },
}
# 可以连接redis查看存的数据
连接：redis-cli
切换数据库：select 1
查看键：keys *
查看值：get 键

# 单个view缓存
# django.views.decorators.cache定义了cache_page装饰器，用于对视图的输出进行缓存
# 示例代码如下：
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)
def index(request):
    return HttpResponse('hello1')
    #return HttpResponse('hello2')
# cache_page接受一个参数：timeout，秒为单位，上例中缓存了15分钟
# 视图缓存与URL无关，如果多个URL指向同一视图，每个URL将会分别缓存

# 模板片断缓存
# 使用cache模板标签来缓存模板的一个片段
# 需要两个参数：
#	缓存时间，以秒为单位
#	给缓存片段起的名称
# 示例代码如下：
{% load cache %}
{% cache 500 hello %}
hello1
<!--hello2-->
{% endcache %}

# 底层的缓存API
from django.core.cache import cache

设置：cache.set(键,值,有效时间)
获取：cache.get(键)
删除：cache.delete(键)
清空：cache.clear()

# 3. 全文检索
# 全文检索不同于特定字段的模糊查询，使用全文检索的效率更高，并且能够对于中文进行分词处理
# haystack：django的一个包，可以方便地对model里面的内容进行索引、搜索，设计为支持whoosh,solr,Xapian,Elasticsearc四种全文检索引擎后端，属于一种全文检索的框架
# whoosh：纯Python编写的全文搜索引擎，虽然性能比不上sphinx、xapian、Elasticsearc等，但是无二进制包，程序不会莫名其妙的崩溃，对于小型的站点，whoosh已经足够使用
# jieba：一款免费的中文分词包，如果觉得不好用可以使用一些收费产品

# 3-1.在虚拟环境中依次安装包
pip install django-haystack
pip install whoosh
pip install jieba
# 3-2.修改settings.py文件
# 添加应用
INSTALLED_APPS = (
    ...
    'haystack',
)
# 添加搜索引擎
HAYSTACK_CONNECTIONS = {
    'default': {
        'ENGINE': 'haystack.backends.whoosh_cn_backend.WhooshEngine',
        'PATH': os.path.join(BASE_DIR, 'whoosh_index'),
    }
}
#自动生成索引
HAYSTACK_SIGNAL_PROCESSOR = 'haystack.signals.RealtimeSignalProcessor'
# 3-3.在项目的urls.py中添加url
urlpatterns = [
    ...
    url(r'^search/', include('haystack.urls')),
]
# 3-4.在应用目录下建立search_indexes.py文件
# coding=utf-8
from haystack import indexes
from models import GoodsInfo


class GoodsInfoIndex(indexes.SearchIndex, indexes.Indexable):
    text = indexes.CharField(document=True, use_template=True)

    def get_model(self):
        return GoodsInfo

    def index_queryset(self, using=None):
        return self.get_model().objects.all()
# 3-5.在目录“templates/search/indexes/应用名称/”下创建“模型类名称_text.txt”文件 
#goodsinfo_text.txt，这里列出了要对哪些列的内容进行检索
{{ object.gName }}
{{ object.gSubName }}
{{ object.gDes }}
# 3-6.在目录“templates/search/”下建立search.html
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
{% if query %}
    <h3>搜索结果如下：</h3>
    {% for result in page.object_list %}
        <a href="/{{ result.object.id }}/">{{ result.object.gName }}</a><br/>
    {% empty %}
        <p>啥也没找到</p>
    {% endfor %}

    {% if page.has_previous or page.has_next %}
        <div>
            {% if page.has_previous %}<a href="?q={{ query }}&amp;page={{ page.previous_page_number }}">{% endif %}&laquo; 上一页{% if page.has_previous %}</a>{% endif %}
        |
            {% if page.has_next %}<a href="?q={{ query }}&amp;page={{ page.next_page_number }}">{% endif %}下一页 &raquo;{% if page.has_next %}</a>{% endif %}
        </div>
    {% endif %}
{% endif %}
</body>
</html>
# 3-7.建立ChineseAnalyzer.py文件
# 保存在haystack的安装文件夹下，路径如“/home/python/.virtualenvs/django_py2/lib/python2.7/site-packages/haystack/backends”
import jieba
from whoosh.analysis import Tokenizer, Token


class ChineseTokenizer(Tokenizer):
    def __call__(self, value, positions=False, chars=False,
                 keeporiginal=False, removestops=True,
                 start_pos=0, start_char=0, mode='', **kwargs):
        t = Token(positions, chars, removestops=removestops, mode=mode,
                  **kwargs)
        seglist = jieba.cut(value, cut_all=True)
        for w in seglist:
            t.original = t.text = w
            t.boost = 1.0
            if positions:
                t.pos = start_pos + value.find(w)
            if chars:
                t.startchar = start_char + value.find(w)
                t.endchar = start_char + value.find(w) + len(w)
            yield t


def ChineseAnalyzer():
    return ChineseTokenizer()
# 3-8.复制whoosh_backend.py文件，改名为whoosh_cn_backend.py
# 注意：复制出来的文件名，末尾会有一个空格，记得要删除这个空格
from .ChineseAnalyzer import ChineseAnalyzer 
查找
analyzer=StemmingAnalyzer()
改为
analyzer=ChineseAnalyzer()
# 3-9.生成索引 初始化索引数据
python manage.py rebuild_index
# 3-10.在模板中创建搜索栏
<form method='get' action="/search/" target="_blank">
    <input type="text" name="q">
    <input type="submit" value="查询">
</form>


# 4. Celery
# 官网:http://www.celeryproject.org/
# 中文文档:http://docs.jinkan.org/docs/celery/
# 示例一：用户发起request，并等待response返回。在本些views中，可能需要执行一段耗时的程序，那么用户就会等待很长时间，造成不好的用户体验
# 示例二：网站每小时需要同步一次天气预报信息，但是http是请求触发的，难道要一小时请求一次吗？
# 使用celery后，情况就不一样了
# 示例一的解决：将耗时的程序放到celery中执行
# 示例二的解决：使用celery定时执行

# 名称:
#	任务task：就是一个Python函数
#	队列queue：将需要执行的任务加入到队列中
#	工人worker：在一个新进程中，负责执行队列中的任务
#	代理人broker：负责调度，在布置环境中使用redis
# 使用:
# 安装包:
pip install celery==3.1.25
pip install celery-with-redis==3.0
pip install django-celery==3.1.17    
# 配置settings
INSTALLED_APPS = (
  ...
  'djcelery',
}

...

import djcelery
djcelery.setup_loader()
BROKER_URL = 'redis://127.0.0.1:6379/0'
CELERY_IMPORTS = ('应用名称.task')
# 在应用目录下创建task.py文件
import time
from celery import task

@task
def sayhello():
    print('hello ...')
    time.sleep(2)
    print('world ...')
# 迁移，生成celery需要的数据表
python manage.py migrate
# 启动worker
python manage.py celery worker --loglevel=info
# 调用语法
function.delay(parameters)
# 使用代码
#from task import *

def sayhello(request):
    print('hello ...')
    import time
    time.sleep(10)
    print('world ...')

    # sayhello.delay()

    return HttpResponse("hello world")    
```

### 117. 部署

```python
# 从uwsgi、nginx、静态文件三个方面处理
# 服务器环境配置
# 在本地的虚拟环境中，项目根目录下，执行命令收集所有包
pip freeze > plist.txt
# 通过ftp软件将开发好的项目上传到此服务器的某个目录
# 安装并创建虚拟环境，如果已有则跳过此步
sudo apt-get install python-virtualenv
sudo easy_install virtualenvwrapper
mkvirtualenv [虚拟环境名称]
# 在虚拟环境上工作，安装所有需要的包
workon [虚拟环境名称]
pip install -r plist.txt
# 更改settings.py文件
DEBUG = False
ALLOW_HOSTS=['*',]表示可以访问服务器的ip
# 启动服务器，运行正常，但是静态文件无法加载

# WSGI
# python manage.py runserver：这是一款适合开发阶段使用的服务器，不适合运行在真实的生产环境中
# 在生产环境中使用WSGI
# WSGI：Web服务器网关接口，英文为Python Web Server Gateway Interface，缩写为WSGI，是Python应用程序或框架和Web服务器之间的一种接口，被广泛接受
# WSGI没有官方的实现, 因为WSGI更像一个协议，只要遵照这些协议,WSGI应用(Application)都可以在任何服务器(Server)上运行
# 命令django-admin startproject会生成一个简单的wsgi.py文件，确定了settings、application对象
#	application对象：在Python模块中使用application对象与应用服务器交互
#	settings模块：Django需要导入settings模块，这里是应用定义的地方
# 此处的服务器是一个软件，可以监听网卡端口、遵从网络层传输协议，收发http协议级别的数据

# uWSGI
# uWSGI实现了WSGI的所有接口，是一个快速、自我修复、开发人员和系统管理员友好的服务器
# uWSGI代码完全用C编写
# 安装uWSGI:
pip install uwsgi
# 配置uWSGI，在项目中新建文件uwsgi.ini，编写如下配置
[uwsgi]
socket=外网ip:端口（使用nginx连接时，使用socket）
http=外网ip:端口（直接做web服务器，使用http）
chdir=项目根目录
wsgi-file=项目中wsgi.py文件的目录，相对于项目根目录
processes=4
threads=2
master=True
pidfile=uwsgi.pid
daemonize=uswgi.log

# 启动：uwsgi --ini uwsgi.ini
# 停止：uwsgi --stop uwsgi.pid
# 重启：uwsgi --reload uwsgi.pid
# 使用http协议查看网站运行情况，运行正常，但是静态文件无法加载

# nginx
# 使用nginx的作用
# 负载均衡：多台服务器轮流处理请求
#	反射代理：隐藏真实服务器
#	实现构架：客户端请求nginx，再由nginx请求uwsgi，运行django框架下的python代码
# nginx+uwsgi也可以用于其它框架的python web代码，不限于django
# 到官网下载nginx压缩文件或通过命令安装
sudo apt-get nginx
# 下载压缩
解压缩：
tar zxvf nginx-1.6.3.tar.gz

进入nginx-1.6.3目录依次执行如下命令进行安装：
./configure
make
sudo make install
# 默认安装到/usr/local/nginx目录，进入此目录执行命令
# 查看版本：sudo sbin/nginx -v
# 启动：sudo sbin/nginx
# 停止：sudo sbin/nginx -s stop
# 重启：sudo sbin/nginx -s reload
# 通过浏览器查看nginx运行结果
# 指向uwsgi项目：编辑conf/nginx.conf文件
sudo conf/nginx.conf

在server下添加新的location项，指向uwsgi的ip与端口
location / {
    include uwsgi_params;将所有的参数转到uwsgi下
    uwsgi_pass uwsgi的ip与端口;
}
# 修改uwsgi.ini文件，启动socket，禁用http
# 重启nginx、uwsgi
# 在浏览器中查看项目，发现静态文件加载不正常，接下来解决静态文件的问题

# 静态文件
# 静态文件一直都找不到，现在终于可以解决了
# 所有的静态文件都会由nginx处理，不会将请求转到uwsgi
# 配置nginx的静态项，打开conf/nginx.conf文件，找到server，添加新location
location /static {
    alias /var/www/test5/static/;
}
# 在服务器上创建目录结构“/var/www/test5/”
# 修改目录权限
sudo chmod 777 /var/www/test5
# 创建static目录，注意顺序是先分配权限，再创建目录
mkdir static
# 最终目录结构
static/
└── booktest
    └── jquery-1.12.4.min.js
└── admin
        
# 修改settings.py文件
STATIC_ROOT='/var/www/test5/static/'
STATIC_URL='/static/'
# 收集所有静态文件到static_root指定目录：python manage.py collectstatic
# 重启nginx、uwsgi
```

### 118. Git

```python
# 1.安装
apt-get install git
# 2. 远程仓库
# Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上。首先找一台电脑充当服务器的角色，每天24小时开机，其他每个人都从这个“服务器”仓库克隆一份到自己的电脑上，并且各自把各自的提交推送到服务器仓库里，也从服务器仓库中拉取别人的提交。可以自己搭建这台服务器，也可以使用GitHub网站

# 创建github账号
# 本地Git仓库和GitHub仓库之间的传输是通过SSH加密的
# step1：创建项目的SSH Key
ssh-keygen -t rsa -C "youremail@example.com"
# 创建完成后，在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人
# step2:* 登录github注册或登录账号，打开“settings”的“SSH Keys”页面，然后，点“New SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容，点“Add Key”，你就应该看到已经添加的Key

# 创建远程库
# 登陆GitHub
# 在右上角找到“new repository”按钮，创建一个新的仓库

修改个人信息:
git config --global user.name 'assasin'
git config --global user.email 'assasin0308@sina.com'

# 从远程库克隆
# 将github上的项目，克隆到本地一份
git clone git@github.com:账号名/项目名.git

#与远程库交互
# 从远程分支库获取到本地
git pull origin master(从指定分支拉取代码)
# 将本地提交远程库
git push origin 本地分支:master(远程分支)
# 提示：每次提交前，需要先获取，解决冲突后再次提交

# 3. 本地仓库
# 创建本地仓库
# 创建空目录
mkdir test7
cd test7
# 目录结构图如下：

# 在目录下创建本地仓库
git init
# 创建成功后，目录结构如下图：

#版本库就是一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”
git branch #查看分支
git branch 分支名 # 创建分支
git checkout 分支名 # 切换分支
git checkout -b 分支名 # 创建并切换分支
git branch -d 分支名 # 删除分支
git tag -a 版本号 -m '版本说明' # 打标签
git tag # 查看标签
git push origin 本地分支:远程分支 --tags  # 提交标签至远程
# 文件管理
# 本地仓库分为三部分：工作区，暂存区，仓库区，其中暂存区、仓库区是版本库部分

# 工作区与暂存区
#	使用IDE打开目录，创建项目，将文件添加到暂存区
git add 文件1 文件2 ...
git add 目录
#	使用暂时区的内容恢复工作区的内容
git checkout -- 文件名
#	查看暂存区的状态
git status

# 暂存区与仓库区
#	将暂存区的记录添加到仓库区
git commit -m '本次提交的说明信息'
#	查看仓库区的历史
当前版本的历史版本：git log
简版显示：git log --pretty=oneline
历史命令：git reflog
#	在Git中，用HEAD表示当前版本，也就是最新的提交3628164...882e1e0（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100
#	对比工作区和仓库区中某版本某文件的不同
git diff HEAD -- 文件名
#	回退历史版本到暂存区
git reset HEAD^或版本号

# 删除文件
# 依次执行如下命令
rm 文件名
git rm 文件名
git commit -m '说明信息'
```

### 119. 天天生鲜项目

```python

```

## 六  爬虫(scrapy)

### 120. "数据时代"数据获取方式

```python
# 1. 企业产生的用户数据：百度指数、阿里指数、TBI腾讯浏览指数、新浪微博指数
# 2. 数据平台购买数据：数据堂 https://www.datatang.com/index.html、国云数据市场  http://www.moojnn.com/ 、贵阳大数据交易所 http://trade.gbdex.com/trade.web/index.jsp
# 3. 政府/机构公开的数据：中华人民共和国国家统计局数据、世界银行公开数据、联合国数据、纳斯达克。
# 4. 数据管理咨询公司：麦肯锡 https://www.mckinsey.com.cn/ 、埃森哲 https://www.accenture.com/cn-zh、艾瑞咨询 https://www.iresearch.com.cn/
# 5. 爬取网络数据
```

### 121. 通用爬虫和聚焦爬虫

```python
# 根据使用场景，网络爬虫可分为 通用爬虫 和 聚焦爬虫 两种.
# 通用网络爬虫 是 捜索引擎抓取系统（Baidu、Google、Yahoo等）的重要组成部分。主要目的是将互联网上的网页下载到本地，形成一个互联网内容的镜像备份。
# 工作原理:
# 通用网络爬虫 从互联网中搜集网页，采集信息，这些网页信息用于为搜索引擎建立索引从而提供支持，它决定着整个引擎系统的内容是否丰富，信息是否即时，因此其性能的优劣直接影响着搜索引擎的效果。
# 第一步：抓取网页
# 搜索引擎网络爬虫的基本工作流程如下：
#	首先选取一部分的种子URL，将这些URL放入待抓取URL队列；
#	取出待抓取URL，解析DNS得到主机的IP，并将URL对应的网页下载下来，存储进已下载网页库中，并且将这些URL放进已抓取URL队列。
#	分析已抓取URL队列中的URL，分析其中的其他URL，并且将URL放入待抓取URL队列，从而进入下一个循环....
# 第二步：数据存储
#	搜索引擎通过爬虫爬取到的网页，将数据存入原始页面数据库。其中的页面数据与用户浏览器得到的HTML是完全一样的。
#	搜索引擎蜘蛛在抓取页面时，也做一定的重复内容检测，一旦遇到访问权重很低的网站上有大量抄袭、采集或者复制的内容，很可能就不再爬行。
# 第三步：预处理
# 搜索引擎将爬虫抓取回来的页面，进行各种步骤的预处理。
#	提取文字
#	中文分词
#	消除噪音（比如版权声明文字、导航条、广告等……）
#	索引处理
#	链接关系计算
#	特殊文件处理
#	......
# 除了HTML文件外，搜索引擎通常还能抓取和索引以文字为基础的多种文件类型，如 PDF、Word、WPS、XLS、PPT、TXT 文件等。我们在搜索结果中也经常会看到这些文件类型。
# 但搜索引擎还不能处理图片、视频、Flash 这类非文字内容，也不能执行脚本和程序。
# 第四步：提供检索服务，网站排名
# 搜索引擎在对信息进行组织和处理后，为用户提供关键字检索服务，将用户检索相关的信息展示给用户。
#同时会根据页面的PageRank值（链接的访问量排名）来进行网站排名，这样Rank值高的网站在搜索结果中会排名较前，当然也可以直接使用 Money 购买搜索引擎网站排名，简单粗暴。
# 局限性：
# 1. 通用搜索引擎所返回的结果都是网页，而大多情况下，网页里90%的内容对用户来说都是无用的。
# 2. 不同领域、不同背景的用户往往具有不同的检索目的和需求，搜索引擎无法提供针对具体某个用户的搜索结果。
# 3.万维网数据形式的丰富和网络技术的不断发展，图片、数据库、音频、视频多媒体等不同数据大量出现，通用搜索引擎对这些文件无能为力，不能很好地发现和获取。
# 4.通用搜索引擎大多提供基于关键字的检索，难以支持根据语义信息提出的查询，无法准确理解用户的具体需求。


# 聚焦爬虫在实施网页抓取时会对内容进行处理筛选，尽量保证只抓取与需求相关的网页信息。
```

### 122. HTTP和HTTPS

```python
# HTTP协议（HyperText Transfer Protocol，超文本传输协议）：是一种发布和接收 HTML页面的方法。端口:80
# HTTPS（Hypertext Transfer Protocol over Secure Socket Layer）简单讲是HTTP的安全版，在HTTP下加入SSL层。
# SSL（Secure Sockets Layer 安全套接层）主要用于Web的安全传输协议，在传输层对网络连接进行加密，保障在Internet上数据传输的安全。端口:443

# 浏览器发送HTTP请求的过程：
# 用户在浏览器的地址栏中输入一个URL并按回车键之后，浏览器会向HTTP服务器发送HTTP请求。HTTP请求主要分为“Get”和“Post”两种方法。
# 当我们在浏览器输入URL http://www.baidu.com 的时候，浏览器发送一个Request请求去获取 http://www.baidu.com 的html文件，服务器把Response文件对象发送回给浏览器。
# 浏览器分析Response中的 HTML，发现其中引用了很多其他文件，比如Images文件，CSS文件，JS文件。 浏览器会自动再次发送Request去获取图片，CSS文件，或者JS文件。
# 当所有的文件都下载成功后，网页会根据HTML语法结构，完整的显示出来了。

# URL（Uniform / Universal Resource Locator的缩写）：统一资源定位符，是用于完整地描述Internet上网页和其他资源的地址的一种标识方法。
# 基本格式：scheme://host[:port#]/path/…/[?query-string][#anchor]
#	scheme：协议(例如：http, https, ftp)
#	host：服务器的IP地址或者域名
#	port#：服务器的端口（如果是走协议默认端口，缺省端口80）
#	path：访问资源的路径
#	query-string：参数，发送给http服务器的数据
#	anchor：锚（跳转到网页的指定锚点位置）

# 客户端HTTP请求
# URL只是标识资源的位置，而HTTP是用来提交和获取资源。客户端发送一个HTTP请求到服务器的请求消息，包括以下格式：请求行、请求头部、空行、请求数据
# 请求方法
# GET: 请求指定的页面信息，并返回实体主体。
# HEAD: 类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头
# POST: 向指定资源提交数据进行处理请求（例如提交表单或者上传文件），数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。
# PUT: 从客户端向服务器传送的数据取代指定的文档的内容。
# DELETE: 请求服务器删除指定的页面。
# CONNECT: HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。
# OPTIONS: 允许客户端查看服务器的性能。
# TRACE: 回显服务器收到的请求，主要用于测试或诊断。

# HTTP请求主要分为Get和Post两种方法
# GET是从服务器上获取数据，POST是向服务器传送数据
# GET请求参数显示，都显示在浏览器网址上，HTTP服务器根据该请求所包含URL中的参数来产生响应内容，即“Get”请求的参数是URL的一部分。 例如： http://www.baidu.com/s?wd=Chinese
# POST请求参数在请求体当中，消息长度没有限制而且以隐式的方式进行发送，通常用来向HTTP服务器提交量比较大的数据（比如请求中包含许多参数或者文件上传操作等），请求的参数包含在“Content-Type”消息头里，指明该消息体的媒体类型和编码，
# 注意：避免使用Get方式提交表单，因为有可能会导致安全问题。 比如说在登陆表单中用Get方式，用户输入的用户名和密码将在地址栏中暴露无遗。

# 常用的请求报头
# 1. Host (主机和端口号)
# Host：对应网址URL中的Web名称和端口号，用于指定被请求资源的Internet主机和端口号，通常属于URL的一部分。
# 2. Connection (链接类型)
#Connection：表示客户端与服务连接类型
#	Client 发起一个包含 Connection:keep-alive 的请求，HTTP/1.1使用 keep-alive 为默认值。
#	Server收到请求后：
#		如果 Server 支持 keep-alive，回复一个包含 Connection:keep-alive 的响应，不关闭连接；
#		如果 Server 不支持 keep-alive，回复一个包含 Connection:close 的响应，关闭连接。
#	如果client收到包含 Connection:keep-alive 的响应，向同一个连接发送下一个请求，直到一方主动关闭连接。
# keep-alive在很多情况下能够重用连接，减少资源消耗，缩短响应时间，比如当浏览器需要多个文件时(比如一个HTML文件和相关的图形文件)，不需要每次都去请求建立连接。
# 3. Upgrade-Insecure-Requests (升级为HTTPS请求)
# Upgrade-Insecure-Requests：升级不安全的请求，意思是会在加载 http 资源时自动替换成 https 请求，让浏览器不再显示https页面中的http请求警报。
# HTTPS 是以安全为目标的 HTTP 通道，所以在 HTTPS 承载的页面上不允许出现 HTTP 请求，一旦出现就是提示或报错。
# 4. ser-Agent (浏览器名称)
# User-Agent：是客户浏览器的名称
# 5.Accept (传输文件类型)
# Accept：指浏览器或其他客户端可以接受的MIME（Multipurpose Internet Mail Extensions（多用途互联网邮件扩展））文件类型，服务器可以根据它判断并返回适当的文件格式。
# Accept: */*：表示什么都可以接收。
# Accept：image/gif：表明客户端希望接受GIF图像格式的资源；
# Accept：text/html：表明客户端希望接受html文本。
# Accept: text/html, application/xhtml+xml;q=0.9, image/*;q=0.8：表示浏览器支持的 MIME 类型分别是 html文本、xhtml和xml文档、所有的图像格式资源。
#q是权重系数，范围 0 =< q <= 1，q 值越大，请求越倾向于获得其“;”之前的类型表示的内容。若没有指定q值，则默认为1，按从左到右排序顺序；若被赋值为0，则用于表示浏览器不接受此内容类型。
# text：用于标准化地表示的文本信息，文本消息可以是多种字符集和或者多种格式的；Application：用于传输应用程序数据或者二进制数据。
# 6. Referer (页面跳转处)
# Referer：表明产生请求的网页来自于哪个URL，用户是从该 Referer页面访问到当前请求的页面。这个属性可以用来跟踪Web请求来自哪个页面，是从什么网站来的等。
# 有时候遇到下载某网站图片，需要对应的referer，否则无法下载图片，那是因为人家做了防盗链，原理就是根据referer去判断是否是本网站的地址，如果不是，则拒绝，如果是，就可以下载；
# 7. Accept-Encoding（文件编解码格式）
# Accept-Encoding：指出浏览器可以接受的编码方式。编码方式不同于文件格式，它是为了压缩文件并加速文件传递速度。浏览器在接收到Web响应之后先解码，然后再检查文件格式，许多情形下这可以减少大量的下载时间。
# 举例：Accept-Encoding:gzip;q=1.0, identity; q=0.5, *;q=0
# 如果有多个Encoding同时匹配, 按照q值顺序排列，本例中按顺序支持 gzip, identity压缩编码，支持gzip的浏览器会返回经过gzip编码的HTML页面。 如果请求消息中没有设置这个域服务器假定客户端对各种内容编码都可以接受。
# 8. Accept-Language（语言种类）
# Accept-Langeuage：指出浏览器可以接受的语言种类，如en或en-us指英语，zh或者zh-cn指中文，当服务器能够提供一种以上的语言版本时要用到。
# 9.  Accept-Charset（字符编码）
# Accept-Charset：指出浏览器可以接受的字符编码。
# 举例：Accept-Charset:iso-8859-1,gb2312,utf-8
# ISO8859-1：通常叫做Latin-1。Latin-1包括了书写所有西方欧洲语言不可缺少的附加字符，英文浏览器的默认值是ISO-8859-1.
# gb2312：标准简体中文字符集;
# utf-8：UNICODE 的一种变长字符编码，可以解决多种语言文本显示问题，从而实现应用国际化和本地化。
# 如果在请求消息中没有设置这个域，缺省是任何字符集都可以接受。
# 10. Cookie （Cookie）
# Cookie：浏览器用这个属性向服务器发送Cookie。Cookie是在浏览器中寄存的小型数据体，它可以记载和服务器相关的用户信息，也可以用来实现会话功能，以后会详细讲。
# 11. Content-Type (POST数据类型)
# Content-Type：POST请求里用来表示的内容类型。
# 举例：Content-Type = Text/XML; charset=gb2312：
# 指明该请求的消息体中包含的是纯文本的XML类型的数据，字符编码采用“gb2312”。


# 常用的响应报头
# 1. Cache-Control：must-revalidate, no-cache, private
# 这个值告诉客户端，服务端不希望客户端缓存资源，在下次请求资源时，必须要从新请求服务器，不能从缓存副本中获取资源。
#	Cache-Control是响应头中很重要的信息，当客户端请求头中包含Cache-Control:max-age=0请求，明确表示不会缓存服务器资源时,Cache-Control作为作为回应信息，通常会返回no-cache，意思就是说，"那就不缓存呗"。
#	当客户端在请求头中没有包含Cache-Control时，服务端往往会定,不同的资源不同的缓存策略，比如说oschina在缓存图片资源的策略就是Cache-Control：max-age=86400,这个意思是，从当前时间开始，在86400秒的时间内，客户端可以直接从缓存副本中读取资源，而不需要向服务器请求
# 2. Connection：keep-alive
# 这个字段作为回应客户端的Connection：keep-alive，告诉客户端服务器的tcp连接也是一个长连接，客户端可以继续使用这个tcp连接发送http请求。
# 3. Content-Encoding:gzip
# 告诉客户端，服务端发送的资源是采用gzip编码的，客户端看到这个信息后，应该采用gzip对资源进行解码。
# 4. Content-Type：text/html;charset=UTF-8
# 告诉客户端，资源文件的类型，还有字符编码，客户端通过utf-8对资源进行解码，然后对资源进行html解析。通常我们会看到有些网站是乱码的，往往就是服务器端没有返回正确的编码。
# 5. Date：Sun, 21 Sep 2016 06:18:21 GMT
# 这个是服务端发送资源时的服务器时间，GMT是格林尼治所在地的标准时间。http协议中发送的时间都是GMT的，这主要是解决在互联网上，不同时区在相互请求资源的时候，时间混乱问题。
# 6. Expires:Sun, 1 Jan 2000 01:00:00 GMT
# 这个响应头也是跟缓存有关的，告诉客户端在这个时间前，可以直接访问缓存副本，很显然这个值会存在问题，因为客户端和服务器的时间不一定会都是相同的，如果时间不同就会导致问题。所以这个响应头是没有Cache-Control：max-age=*这个响应头准确的，因为max-age=date中的date是个相对时间，不仅更好理解，也更准确。
# 7. Pragma:no-cache
# 这个含义与Cache-Control等同。
# 8. Server：Tengine/1.4.6
# 这个是服务器和相对应的版本，只是告诉客户端服务器的信息。
# 9. Transfer-Encoding：chunked
# 这个响应头告诉客户端，服务器发送的资源的方式是分块发送的。一般分块发送的资源都是服务器动态生成的，在发送时还不知道发送资源的大小，所以采用分块发送，每一块都是独立的，独立的块都能标示自己的长度，最后一块是0长度的，当客户端读到这个0长度的块时，就可以确定资源已经传输完了。
# 10.  Vary: Accept-Encoding
# 告诉缓存服务器，缓存压缩文件和非压缩文件两个版本，现在这个字段用处并不大，因为现在的浏览器都是支持压缩的。

# 响应状态码
# 常见状态码：
# 100~199：表示服务器成功接收部分请求，要求客户端继续提交其余请求才能完成整个处理过程。
# 200~299：表示服务器成功接收请求并已完成整个处理过程。常用200（OK 请求成功）。
# 300~399：为完成请求，客户需进一步细化请求。例如：请求的资源已经移动一个新地址、常用302（所请求的页面已经临时转移至新的url）、307和304（使用缓存资源）。
# 400~499：客户端的请求有错误，常用404（服务器无法找到被请求的页面）、403（服务器拒绝访问，权限不够）。
# 500~599：服务器端出现错误，常用500（请求未完成。服务器遇到不可预知的情况）。

# Cookie 和 Session：
# 服务器和客户端的交互仅限于请求/响应过程，结束之后便断开，在下一次请求时，服务器会认为新的客户端。
# 为了维护他们之间的链接，让服务器知道这是前一个用户发送的请求，必须在一个地方保存客户端的信息。
# Cookie：通过在 客户端 记录的信息确定用户的身份。
# Session：通过在 服务器端 记录的信息确定用户的身份。

```

### 123. urllib2基本使用

```python
import urllib2
# 向指定的url发送请求，并返回服务器响应的类文件对象
response = urllib2.urlopen("http://www.baidu.com")
# 类文件对象支持 文件对象的操作方法，如read()方法读取文件全部内容，返回字符串
html = response.read()
# 打印字符串
print html

```

### 124. 不同种类请求

```python
# urllib2默认只支持HTTP/HTTPS的GET和POST方法
# urllib 和 urllib2 都是接受URL请求的相关模块，但是提供了不同的功能。两个最显著的不同如下：
#	urllib 仅可以接受URL，不能创建 设置了headers 的Request 类实例；
#	但是 urllib 提供 urlencode 方法用来GET查询字符串的产生，而 urllib2 则没有。（这是 urllib 和 urllib2 经常一起使用的主要原因）
#	编码工作使用urllib的urlencode()函数，帮我们将key:value这样的键值对转换成"key=value"这样的字符串，解码工作可以使用urllib的unquote()函数。
# 重点 获取AJAX加载的内容:
import urllib
import urllib2

# demo1

url = "https://movie.douban.com/j/chart/top_list?type=11&interval_id=100%3A90&action"

headers={"User-Agent": "Mozilla...."}

# 变动的是这两个参数，从start开始往后显示limit个
formdata = {
    'start':'0',
    'limit':'10'
}
data = urllib.urlencode(formdata)

request = urllib2.Request(url, data = data, headers = headers)
response = urllib2.urlopen(request)

print response.read()


# demo2

url = "https://movie.douban.com/j/chart/top_list?"
headers={"User-Agent": "Mozilla...."}

# 处理所有参数
formdata = {
    'type':'11',
    'interval_id':'100:90',
    'action':'',
    'start':'0',
    'limit':'10'
}
data = urllib.urlencode(formdata)

request = urllib2.Request(url, data = data, headers = headers)
response = urllib2.urlopen(request)

print response.read()
```

### 125. urllib2 Handler处理器 和 自定义Opener

```python
# opener是 urllib2.OpenerDirector 的实例，我们之前一直都在使用的urlopen，它是一个特殊的opener（也就是模块帮我们构建好的）。
# 但是基本的urlopen()方法不支持代理、cookie等其他的HTTP/HTTPS高级功能。所以要支持这些功能：
#	使用相关的 Handler处理器 来创建特定功能的处理器对象；
#	然后通过 urllib2.build_opener()方法使用这些处理器对象，创建自定义opener对象；
#	使用自定义的opener对象，调用open()方法发送请求。
# 如果程序里所有的请求都使用自定义的opener，可以使用urllib2.install_opener() 将自定义的 opener 对象 定义为 全局opener，表示如果之后凡是调用urlopen，都将使用这个opener（根据自己的需求来选择）

# 简单的自定义opener()
import urllib2

# 构建一个HTTPHandler 处理器对象，支持处理HTTP请求
http_handler = urllib2.HTTPHandler()

# 构建一个HTTPHandler 处理器对象，支持处理HTTPS请求
# http_handler = urllib2.HTTPSHandler()

# 调用urllib2.build_opener()方法，创建支持处理HTTP请求的opener对象
opener = urllib2.build_opener(http_handler)

# 构建 Request请求
request = urllib2.Request("http://www.baidu.com/")

# 调用自定义opener对象的open()方法，发送request请求
response = opener.open(request)

# 获取服务器响应内容
print response.read()

# 在 HTTPHandler()增加 debuglevel=1参数，还会将 Debug Log 打开，这样程序在执行的时候，会把收包和发包的报头在屏幕上自动打印出来，方便调试，有时可以省去抓包的工作。
# 仅需要修改的代码部分：

# 构建一个HTTPHandler 处理器对象，支持处理HTTP请求，同时开启Debug Log，debuglevel 值默认 0
http_handler = urllib2.HTTPHandler(debuglevel=1)

# 构建一个HTTPHSandler 处理器对象，支持处理HTTPS请求，同时开启Debug Log，debuglevel 值默认 0
https_handler = urllib2.HTTPSHandler(debuglevel=1)

# ProxyHandler处理器（代理设置）
# urllib2中通过ProxyHandler来设置使用代理服务器
#urllib2_proxy1.py

import urllib2

# 构建了两个代理Handler，一个有代理IP，一个没有代理IP
httpproxy_handler = urllib2.ProxyHandler({"http" : "124.88.67.81:80"})
nullproxy_handler = urllib2.ProxyHandler({})

proxySwitch = True #定义一个代理开关

# 通过 urllib2.build_opener()方法使用这些代理Handler对象，创建自定义opener对象
# 根据代理开关是否打开，使用不同的代理模式
if proxySwitch:  
    opener = urllib2.build_opener(httpproxy_handler)
else:
    opener = urllib2.build_opener(nullproxy_handler)

request = urllib2.Request("http://www.baidu.com/")

# 1. 如果这么写，只有使用opener.open()方法发送请求才使用自定义的代理，而urlopen()则不使用自定义代理。
response = opener.open(request)

# 2. 如果这么写，就是将opener应用到全局，之后所有的，不管是opener.open()还是urlopen() 发送请求，都将使用自定义代理。
# urllib2.install_opener(opener)
# response = urlopen(request)

print response.read()

# 如果代理IP足够多，就可以像随机获取User-Agent一样，随机选择一个代理去访问网站。
import urllib2
import random

proxy_list = [
    {"http" : "124.88.67.81:80"},
    {"http" : "124.88.67.81:80"},
    {"http" : "124.88.67.81:80"},
    {"http" : "124.88.67.81:80"},
    {"http" : "124.88.67.81:80"}
]

# 随机选择一个代理
proxy = random.choice(proxy_list)
# 使用选择的代理构建代理处理器对象
httpproxy_handler = urllib2.ProxyHandler(proxy)

opener = urllib2.build_opener(httpproxy_handler)

request = urllib2.Request("http://www.baidu.com/")
response = opener.open(request)
print response.read()


# HTTPPasswordMgrWithDefaultRealm()
# HTTPPasswordMgrWithDefaultRealm()类将创建一个密码管理对象，用来保存 HTTP 请求相关的用户名和密码，主要应用两个场景：

#	验证代理授权的用户名和密码 (ProxyBasicAuthHandler())
#	验证Web客户端的的用户名和密码 (HTTPBasicAuthHandler())

# ProxyBasicAuthHandler(代理授权验证)
# 如果使用之前的代码来使用私密代理，会报 HTTP 407 错误，表示代理没有通过身份验证：urllib2.HTTPError: HTTP Error 407: Proxy Authentication Required

改写代码，通过：

HTTPPasswordMgrWithDefaultRealm()：来保存私密代理的用户密码
ProxyBasicAuthHandler()：来处理代理的身份验证。
#urllib2_proxy2.py

import urllib2
import urllib

# 私密代理授权的账户
user = "mr_mao_hacker"
# 私密代理授权的密码
passwd = "sffqry9r"
# 私密代理 IP
proxyserver = "61.158.163.130:16816"

# 1. 构建一个密码管理对象，用来保存需要处理的用户名和密码
passwdmgr = urllib2.HTTPPasswordMgrWithDefaultRealm()

# 2. 添加账户信息，第一个参数realm是与远程服务器相关的域信息，一般没人管它都是写None，后面三个参数分别是 代理服务器、用户名、密码
passwdmgr.add_password(None, proxyserver, user, passwd)

# 3. 构建一个代理基础用户名/密码验证的ProxyBasicAuthHandler处理器对象，参数是创建的密码管理对象
#   注意，这里不再使用普通ProxyHandler类了
proxyauth_handler = urllib2.ProxyBasicAuthHandler(passwdmgr)

# 4. 通过 build_opener()方法使用这些代理Handler对象，创建自定义opener对象，参数包括构建的 proxy_handler 和 proxyauth_handler
opener = urllib2.build_opener(proxyauth_handler)

# 5. 构造Request 请求
request = urllib2.Request("http://www.baidu.com/")

# 6. 使用自定义opener发送请求
response = opener.open(request)

# 7. 打印响应内容
print response.read()


# HTTPBasicAuthHandler处理器（Web客户端授权验证）
# 有些Web服务器（包括HTTP/FTP等）访问时，需要进行用户身份验证，爬虫直接访问会报HTTP 401 错误，表示访问身份未经授权：urllib2.HTTPError: HTTP Error 401: Unauthorized
# 如果有客户端的用户名和密码，我们可以通过下面的方法去访问爬取：
import urllib
import urllib2

# 用户名
user = "test"
# 密码
passwd = "123456"
# Web服务器 IP
webserver = "http://192.168.199.107"

# 1. 构建一个密码管理对象，用来保存需要处理的用户名和密码
passwdmgr = urllib2.HTTPPasswordMgrWithDefaultRealm()

# 2. 添加账户信息，第一个参数realm是与远程服务器相关的域信息，一般没人管它都是写None，后面三个参数分别是 Web服务器、用户名、密码
passwdmgr.add_password(None, webserver, user, passwd)

# 3. 构建一个HTTP基础用户名/密码验证的HTTPBasicAuthHandler处理器对象，参数是创建的密码管理对象
httpauth_handler = urllib2.HTTPBasicAuthHandler(passwdmgr)

# 4. 通过 build_opener()方法使用这些代理Handler对象，创建自定义opener对象，参数包括构建的 proxy_handler
opener = urllib2.build_opener(httpauth_handler)

# 5. 可以选择通过install_opener()方法定义opener为全局opener
urllib2.install_opener(opener)

# 6. 构建 Request对象
request = urllib2.Request("http://192.168.199.107")

# 7. 定义opener为全局opener后，可直接使用urlopen()发送请求
response = urllib2.urlopen(request)

# 8. 打印响应内容
print response.read()


# cookielib库 和 HTTPCookieProcessor处理器
# 在Python处理Cookie，一般是通过cookielib模块和 urllib2模块的HTTPCookieProcessor处理器类一起使用。
#	cookielib模块：主要作用是提供用于存储cookie的对象
#	HTTPCookieProcessor处理器：主要作用是处理这些cookie对象，并构建handler对象。
# cookielib 库 主要的对象有CookieJar、FileCookieJar、MozillaCookieJar、LWPCookieJar。
#	CookieJar(重点)：管理HTTP cookie值、存储HTTP请求生成的cookie、向传出的HTTP请求添加cookie的对象。整个cookie都存储在内存中，对CookieJar实例进行垃圾回收后cookie也将丢失。
#	FileCookieJar (filename,delayload=None,policy=None)：从CookieJar派生而来，用来创建FileCookieJar实例，检索cookie信息并将cookie存储到文件中。filename是存储cookie的文件名。delayload为True时支持延迟访问访问文件，即只有在需要时才读取文件或在文件中存储数据。
#	MozillaCookieJar (filename,delayload=None,policy=None)：从FileCookieJar派生而来，创建与Mozilla浏览器 cookies.txt兼容的FileCookieJar实例。
#	LWPCookieJar (filename,delayload=None,policy=None)：从FileCookieJar派生而来，创建与libwww-perl标准的 Set-Cookie3 文件格式兼容的FileCookieJar实例。

# 示例: 人人网登录
# -*- coding : utf-8 -*-
import cookielib
import urllib2
import urllib
# 构建一个CookieJar对象实例来保存cookie
cookie = cookielib.CookieJar()
# 使用HTTPCookieProcessor()来创建cookie处理器对象，参数为CookieJar()对象
cookie_handler = urllib2.HTTPCookieProcessor(cookie)
# 通过 build_opener() 来构建opener
opener = urllib2.build_opener(cookie_handler)
# addheaders 接受一个列表，里面每个元素都是一个headers信息的元祖, opener将附带headers信息
opener.addheaders = [
    ("User-Agent" , "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36"),
]

url = "http://www.renren.com/PLogin.do"
# 需要登录的账户和密码
data = {"email":"18311039502","password":"19920308shibin"}
# 通过urlencode()转码
data = urllib.urlencode(data)
# 构建Request请求对象，包含需要发送的用户名和密码
request = urllib2.Request(url,data=data)
# 通过opener发送这个请求，并获取登录后的Cookie值
response = opener.open(request)
# opener包含用户登录后的Cookie值，可以直接访问那些登录后才可以访问的页面
response = opener.open("http://www.renren.com/410043129/profile")
# 打印响应内容
print response.read()
```

### 126. 免费短期代理网站

```python
西刺免费代理IP : https://www.xicidaili.com
快代理免费代理:  https://www.kuaidaili.com/free/inha/
Proxy360代理:   http://www.proxy360.cn/default.aspx      
```

### 127. urllib2 的异常错误处理 

```python
# URLError 产生的原因主要有：
#	没有网络连接
#	服务器连接失败
#	找不到指定的服务器

# 示例:
# urllib2_urlerror.py

import urllib2

requset = urllib2.Request('http://www.ajkfhafwjqh.com')

try:
    urllib2.urlopen(request, timeout=5)
except urllib2.URLError, err:
    print err

# HTTPError
# HTTPError是URLError的子类，我们发出一个请求时，服务器上都会对应一个response应答对象，其中它包含一个数字"响应状态码"。
# 如果urlopen或opener.open不能处理的，会产生一个HTTPError，对应相应的状态码，HTTP状态码表示HTTP协议所返回的响应的状态。
# 注意，urllib2可以为我们处理重定向的页面（也就是3开头的响应码），100-299范围的号码表示成功，所以我们只能看到400-599的错误号码。
# 示例:
# urllib2_httperror.py

import urllib2

requset = urllib2.Request('http://blog.baidu.com/itcast')

try:
    urllib2.urlopen(requset)
except urllib2.HTTPError, err:
    print err.code
    print err
# 运行结果:
404
HTTP Error 404: Not Found
    
# 由于HTTPError的父类是URLError，所以父类的异常应当写到子类异常的后面，所以上述的代码可以这么改写：
# urllib2_botherror.py

import urllib2

requset = urllib2.Request('http://blog.baidu.com/itcast')

try:
    urllib2.urlopen(requset)

except urllib2.HTTPError, err:
    print err.code

except urllib2.URLError, err:
    print err

else:
    print "Good Job"
# 运行结果:
404
```

### 128. HTTP响应状态码参考

```python
# 1xx:信息

100 Continue
服务器仅接收到部分请求，但是一旦服务器并没有拒绝该请求，客户端应该继续发送其余的请求。
101 Switching Protocols
服务器转换协议：服务器将遵从客户的请求转换到另外一种协议。

# 2xx:成功

200 OK
请求成功（其后是对GET和POST请求的应答文档）
201 Created
请求被创建完成，同时新的资源被创建。
202 Accepted
供处理的请求已被接受，但是处理未完成。
203 Non-authoritative Information
文档已经正常地返回，但一些应答头可能不正确，因为使用的是文档的拷贝。
204 No Content
没有新文档。浏览器应该继续显示原来的文档。如果用户定期地刷新页面，而Servlet可以确定用户文档足够新，这个状态代码是很有用的。
205 Reset Content
没有新文档。但浏览器应该重置它所显示的内容。用来强制浏览器清除表单输入内容。
206 Partial Content
客户发送了一个带有Range头的GET请求，服务器完成了它。

# 3xx:重定向

300 Multiple Choices
多重选择。链接列表。用户可以选择某链接到达目的地。最多允许五个地址。
301 Moved Permanently
所请求的页面已经转移至新的url。
302 Moved Temporarily
所请求的页面已经临时转移至新的url。
303 See Other
所请求的页面可在别的url下被找到。
304 Not Modified
未按预期修改文档。客户端有缓冲的文档并发出了一个条件性的请求（一般是提供If-Modified-Since头表示客户只想比指定日期更新的文档）。服务器告诉客户，原来缓冲的文档还可以继续使用。
305 Use Proxy
客户请求的文档应该通过Location头所指明的代理服务器提取。
306 Unused
此代码被用于前一版本。目前已不再使用，但是代码依然被保留。
307 Temporary Redirect
被请求的页面已经临时移至新的url。

# 4xx:客户端错误

400 Bad Request
服务器未能理解请求。
401 Unauthorized
被请求的页面需要用户名和密码。
401.1
登录失败。
401.2
服务器配置导致登录失败。
401.3
由于 ACL 对资源的限制而未获得授权。
401.4
筛选器授权失败。
401.5
ISAPI/CGI 应用程序授权失败。
401.7
访问被 Web 服务器上的 URL 授权策略拒绝。这个错误代码为 IIS 6.0 所专用。
402 Payment Required
此代码尚无法使用。
403 Forbidden
对被请求页面的访问被禁止。
403.1
执行访问被禁止。
403.2
读访问被禁止。
403.3
写访问被禁止。
403.4
要求 SSL。
403.5
要求 SSL 128。
403.6
IP 地址被拒绝。
403.7
要求客户端证书。
403.8
站点访问被拒绝。
403.9
用户数过多。
403.10
配置无效。
403.11
密码更改。
403.12
拒绝访问映射表。
403.13
客户端证书被吊销。
403.14
拒绝目录列表。
403.15
超出客户端访问许可。
403.16
客户端证书不受信任或无效。
403.17
客户端证书已过期或尚未生效。
403.18
在当前的应用程序池中不能执行所请求的 URL。这个错误代码为 IIS 6.0 所专用。
403.19
不能为这个应用程序池中的客户端执行 CGI。这个错误代码为 IIS 6.0 所专用。
403.20
Passport 登录失败。这个错误代码为 IIS 6.0 所专用。
404 Not Found
服务器无法找到被请求的页面。
404.0
没有找到文件或目录。
404.1
无法在所请求的端口上访问 Web 站点。
404.2
Web 服务扩展锁定策略阻止本请求。
404.3
MIME 映射策略阻止本请求。
405 Method Not Allowed
请求中指定的方法不被允许。
406 Not Acceptable
服务器生成的响应无法被客户端所接受。
407 Proxy Authentication Required
用户必须首先使用代理服务器进行验证，这样请求才会被处理。
408 Request Timeout
请求超出了服务器的等待时间。
409 Conflict
由于冲突，请求无法被完成。
410 Gone
被请求的页面不可用。
411 Length Required
"Content-Length" 未被定义。如果无此内容，服务器不会接受请求。
412 Precondition Failed
请求中的前提条件被服务器评估为失败。
413 Request Entity Too Large
由于所请求的实体的太大，服务器不会接受请求。
414 Request-url Too Long
由于url太长，服务器不会接受请求。当post请求被转换为带有很长的查询信息的get请求时，就会发生这种情况。
415 Unsupported Media Type
由于媒介类型不被支持，服务器不会接受请求。
416 Requested Range Not Satisfiable
服务器不能满足客户在请求中指定的Range头。
417 Expectation Failed
执行失败。
423
锁定的错误。

# 5xx:服务器错误

500 Internal Server Error
请求未完成。服务器遇到不可预知的情况。
500.12
应用程序正忙于在 Web 服务器上重新启动。
500.13
Web 服务器太忙。
500.15
不允许直接请求 Global.asa。
500.16
UNC 授权凭据不正确。这个错误代码为 IIS 6.0 所专用。
500.18
URL 授权存储不能打开。这个错误代码为 IIS 6.0 所专用。
500.100
内部 ASP 错误。
501 Not Implemented
请求未完成。服务器不支持所请求的功能。
502 Bad Gateway
请求未完成。服务器从上游服务器收到一个无效的响应。
502.1
CGI 应用程序超时。　·
502.2
CGI 应用程序出错。
503 Service Unavailable
请求未完成。服务器临时过载或当机。
504 Gateway Timeout
网关超时。
505 HTTP Version Not Supported
服务器不支持请求中指明的HTTP协议版本
```

### 129. Requests

```python
# Requests 继承了urllib2的所有特性。Requests支持HTTP连接保持和连接池，支持使用cookie保持会话，支持文件上传，支持自动确定响应内容的编码，支持国际化的 URL 和 POST 数据自动编码。
# requests 的底层实现其实就是 urllib3
开源地址：https://github.com/kennethreitz/requests
中文文档 API： http://docs.python-requests.org/zh_CN/latest/index.html
# 安装方式:
pip install requests 或 easy_install requests

# 1. 最基本的GET请求可以直接用get方法
response = requests.get("http://www.baidu.com/")

# 也可以这么写
# response = requests.request("get", "http://www.baidu.com/")

# 2. 添加 headers 和 查询参数
# 如果想添加 headers，可以传入headers参数来增加请求头中的headers信息。如果要将参数放在url中传递，可以利用 params 参数。
import requests

kw = {'wd':'长城'}

headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}

# params 接收一个字典或者字符串的查询参数，字典类型自动转换为url编码，不需要urlencode()
response = requests.get("http://www.baidu.com/s?", params = kw, headers = headers)

# 查看响应内容，response.text 返回的是Unicode格式的数据
print response.text

# 查看响应内容，response.content返回的字节流数据
print respones.content

# 查看完整url地址
print response.url

# 查看响应头部字符编码
print response.encoding

# 查看响应码
print response.status_code
# 使用response.text 时，Requests 会基于 HTTP 响应的文本编码自动解码响应内容，大多数 Unicode 字符集都能被无缝地解码。
# 使用response.content 时，返回的是服务器响应数据的原始二进制字节流，可以用来保存图片等二进制文件。



# 1. 最基本的GET请求可以直接用post方法
response = requests.post("http://www.baidu.com/", data = data)
# 2. 传入data数据
# 对于 POST 请求来说，我们一般需要为它增加一些参数。那么最基本的传参方法可以利用 data 这个参数。
import requests

formdata = {
    "type":"AUTO",
    "i":"i love python",
    "doctype":"json",
    "xmlVersion":"1.8",
    "keyfrom":"fanyi.web",
    "ue":"UTF-8",
    "action":"FY_BY_ENTER",
    "typoResult":"true"
}

url = "http://fanyi.youdao.com/translate?smartresult=dict&smartresult=rule&smartresult=ugc&sessionFrom=null"

headers={ "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36"}

response = requests.post(url, data = formdata, headers = headers)

print response.text

# 如果是json文件可以直接显示
print response.json()

# 3. 代理（proxies参数）
import requests

# 根据协议类型，选择不同的代理
proxies = {
  "http": "http://12.34.56.79:9527",
  "https": "http://12.34.56.79:9527",
}

response = requests.get("http://www.baidu.com", proxies = proxies)
print response.text

# 4. 私密代理验证（特定格式） 和 Web客户端验证（auth 参数）
import requests

# 如果代理需要使用HTTP Basic Auth，可以使用下面这种格式：
proxy = { "http": "mr_mao_hacker:sffqry9r@61.158.163.130:16816" }

response = requests.get("http://www.baidu.com", proxies = proxy)

print response.text

# 5. web客户端验证
import requests

auth=('test', '123456')

response = requests.get('http://192.168.199.107', auth = auth)

print response.text

# 6. 如果一个响应中包含了cookie，那么我们可以利用 cookies参数拿到：
import requests

response = requests.get("http://www.baidu.com/")

# 7. 返回CookieJar对象:
cookiejar = response.cookies

# 8. 将CookieJar转为字典：
cookiedict = requests.utils.dict_from_cookiejar(cookiejar)

print cookiejar

print cookiedict

# 7. 在 requests 里，session对象是一个非常常用的对象，这个对象代表一次用户会话：从客户端浏览器连接服务器开始，到客户端浏览器与服务器断开。
#会话能让我们在跨请求时候保持某些参数，比如在同一个 Session 实例发出的所有请求之间保持 cookie 。
import requests

# 1. 创建session对象，可以保存Cookie值
ssion = requests.session()

# 2. 处理 headers
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}

# 3. 需要登录的用户名和密码
data = {"email":"mr_mao_hacker@163.com", "password":"alarmchime"}  

# 4. 发送附带用户名和密码的请求，并获取登录后的Cookie值，保存在ssion里
ssion.post("http://www.renren.com/PLogin.do", data = data)

# 5. ssion包含用户登录后的Cookie值，可以直接访问那些登录后才可以访问的页面
response = ssion.get("http://www.renren.com/410043129/profile")

# 6. 打印响应内容
print response.text


# 9. 处理HTTPS请求 SSL证书验证
# Requests也可以为HTTPS请求验证SSL证书：
# 要想检查某个主机的SSL证书，你可以使用 verify 参数（也可以不写）
import requests
response = requests.get("https://www.baidu.com/", verify=True)

# 也可以省略不写
# response = requests.get("https://www.baidu.com/")
print r.text
# 如果SSL证书验证不通过，或者不信任服务器的安全证书，则会报出SSLError，据说 12306 证书是自己做的：

# 如果我们想跳过 12306 的证书验证，把 verify 设置为 False 就可以正常请求了。
r = requests.get("https://www.12306.cn/mormhweb/", verify = False)
```

### 130. re 模块

```python
# re 模块的一般使用步骤如下：
#	使用 compile() 函数将正则表达式的字符串形式编译为一个 Pattern 对象
#	通过 Pattern 对象提供的一系列方法对文本进行匹配查找，获得匹配结果，一个 Match 对象。
#	最后使用 Match 对象提供的属性和方法获得信息，

re.I 表示忽略大小写
re.S 表示全文匹配
示例:
    pattern = re.compile('<div\sclass="f18 mb20">(.*?)</div>',re.S)
表示匹配所有 <div class="f18 mb20"> </div>中的内容

import re

# 将正则表达式编译成 Pattern 对象
pattern = re.compile(r'\d+')
# Pattern 对象的一些常用方法主要有：
#	match 方法：从起始位置开始查找，一次匹配
#	search 方法：从任何位置开始查找，一次匹配
#	findall 方法：全部匹配，返回列表
#	finditer 方法：全部匹配，返回迭代器
#	split 方法：分割字符串，返回列表
#	sub 方法：替换

# match 方法
# match 方法用于查找字符串的头部（也可以指定起始位置），它是一次匹配，只要找到了一个匹配的结果就返回，而不是查找所有匹配的结果。它的一般使用形式如下：
match(string, begin, end)
#其中，string 是待匹配的字符串，pos 和 endpos 是可选参数，指定字符串的起始和终点位置，默认值分别是 0 和 len (字符串长度)。因此，当你不指定 pos 和 endpos 时，match 方法默认匹配字符串的头部。
# 当匹配成功时，返回一个 Match 对象，如果没有匹配上，则返回 None。
# 当匹配成功时返回一个 Match 对象，其中：
#	group([group1, …]) 方法用于获得一个或多个分组匹配的字符串，当要获得整个匹配的子串时，可直接使用 group() 或 group(0)；
#	start([group]) 方法用于获取分组匹配的子串在整个字符串中的起始位置（子串第一个字符的索引），参数默认值为 0；
#	end([group]) 方法用于获取分组匹配的子串在整个字符串中的结束位置（子串最后一个字符的索引+1），参数默认值为 0；
#	span([group]) 方法返回 (start(group), end(group))。


# search 方法
# search 方法用于查找字符串的任何位置，它也是一次匹配，只要找到了一个匹配的结果就返回，而不是查找所有匹配的结果，它的一般使用形式如下：
search(string, begin, end)
# 其中，string 是待匹配的字符串，pos 和 endpos 是可选参数，指定字符串的起始和终点位置，默认值分别是 0 和 len (字符串长度)。
# 当匹配成功时，返回一个 Match 对象，如果没有匹配上，则返回 None。


# findall 方法
# 与match 和 search 方法都是一次匹配，只要找到了一个匹配的结果就返回。然而，在大多数时候，我们需要搜索整个字符串，获得所有匹配的结果。
# findall 方法的使用形式如下：
 findall(string[, pos[, endpos]])
# 其中，string 是待匹配的字符串，pos 和 endpos 是可选参数，指定字符串的起始和终点位置，默认值分别是 0 和 len (字符串长度)。
# findall 以列表形式返回全部能匹配的子串，如果没有匹配，则返回一个空列表。

# finditer 方法
# finditer 方法的行为跟 findall 的行为类似，也是搜索整个字符串，获得所有匹配的结果。但它返回一个顺序访问每一个匹配结果（Match 对象）的迭代器。

# split 方法
# split 方法按照能够匹配的子串将字符串分割后返回列表，它的使用形式如下：
split(string[, maxsplit])
# 其中，maxsplit 用于指定最大分割次数，不指定将全部分割。

# sub 方法
# sub 方法用于替换。它的使用形式如下：
sub(repl, string[, count])
# 其中，repl 可以是字符串也可以是一个函数：
#	如果 repl 是字符串，则会使用 repl 去替换字符串每一个匹配的子串，并返回替换后的字符串，另外，repl 还可以使用 id 的形式来引用分组，但不能使用编号 0；
#	如果 repl 是函数，这个方法应当只接受一个参数（Match 对象），并返回一个字符串用于替换（返回的字符串中不能再引用分组）。
#	count 用于指定最多替换次数，不指定时全部替换。


# 匹配中文
# 在某些情况下，我们想匹配文本中的汉字，有一点需要注意的是，中文的 unicode 编码范围 主要在 [u4e00-u9fa5]，这里说主要是因为这个范围并不完整，比如没有包括全角（中文）标点，不过，在大部分情况下，应该是够用的。
# 示例 : 匹配中文 "你好,世界"
import re

title = u'你好，hello，世界'
pattern = re.compile(ur'[\u4e00-\u9fa5]+')
result = pattern.findall(title)

print result

# 在正则表达式前面加上了两个前缀 ur，其中 r 表示使用原始字符串，u 表示是 unicode 字符串。


# 贪婪模式与非贪婪模式
# 贪婪模式：在整个表达式匹配成功的前提下，尽可能多的匹配 ( * )；
# 非贪婪模式：在整个表达式匹配成功的前提下，尽可能少的匹配 ( ? )；
# Python里数量词默认是贪婪的。


# 正则表达式在线测试
http://tool.oschina.net/regex/
```

### 131. Queue（队列对象）

```python
# Queue是python中的标准库，可以直接import Queue引用;队列是线程间最常用的交换数据的形式

# python下多线程的思考

# 对于资源，加锁是个重要的环节。因为python原生的list,dict等，都是not thread safe的。而Queue，是线程安全的，因此在满足使用条件下，建议使用队列

# 1. 初始化： class Queue.Queue(maxsize) FIFO 先进先出
# 2. 包中的常用方法:
#	Queue.qsize() 返回队列的大小
#	Queue.empty() 如果队列为空，返回True,反之False
#	Queue.full() 如果队列满了，返回True,反之False
#	Queue.full 与 maxsize 大小对应
#	Queue.get([block[, timeout]])获取队列，timeout等待时间
# 3. 创建一个“队列”对象
#	mport Queue
#	myqueue = Queue.Queue(maxsize = 10)
# 4. 将一个值放入队列中
#	myqueue.put(10)
# 5. 将一个值从队列中取出
#	myqueue.get()
```

### 132. Selenium与PhantomJS

```python
# Selenium是一个Web的自动化测试工具，最初是为网站自动化测试而开发的，类型像我们玩游戏用的按键精灵，可以按指定的命令自动操作，不同是Selenium 可以直接运行在浏览器上，它支持所有主流的浏览器（包括PhantomJS这些无界面的浏览器）。
# Selenium 可以根据我们的指令，让浏览器自动加载页面，获取需要的数据，甚至页面截屏，或者判断网站上某些动作是否发生。
# Selenium 自己不带浏览器，不支持浏览器的功能，它需要与第三方浏览器结合在一起才能使用。但是我们有时候需要让它内嵌在代码中运行，所以我们可以用一个叫 PhantomJS 的工具代替真实的浏览器。
# 可以从 PyPI 网站下载 Selenium库https://pypi.python.org/simple/selenium ，也可以用 第三方管理器 pip用命令安装：
pip install selenium
# Selenium 官方参考文档：http://selenium-python.readthedocs.io/index.html

# PhantomJS 是一个基于Webkit的“无界面”(headless)浏览器，它会把网站加载到内存并执行页面上的 JavaScript，因为不会展示图形界面，所以运行起来比完整的浏览器要高效。
# 如果我们把 Selenium 和 PhantomJS 结合在一起，就可以运行一个非常强大的网络爬虫了，这个爬虫可以处理 JavaScrip、Cookie、headers，以及任何我们真实用户需要做的事情。
# 注意：PhantomJS 只能从它的官方网站http://phantomjs.org/download.html) 下载。 因为 PhantomJS 是一个功能完善(虽然无界面)的浏览器而非一个 Python 库，所以它不需要像 Python 的其他库一样安装，但我们可以通过Selenium调用PhantomJS来直接使用。
# PhantomJS 官方参考文档：http://phantomjs.org/documentation
# 安装:
apt install phantomjs (#注意:这种安装方式不太能用)

    
 # Selenium 库里有个叫 WebDriver 的 API。WebDriver 有点儿像可以加载网站的浏览器，但是它也可以像 BeautifulSoup 或者其他 Selector 对象一样用来查找页面元素，与页面上的元素进行交互 (发送文本、点击等)，以及执行其他动作来运行网络爬虫。
# IPython2 测试代码

# 导入 webdriver
from selenium import webdriver

# 要想调用键盘按键操作需要引入keys包
from selenium.webdriver.common.keys import Keys

# 调用环境变量指定的PhantomJS浏览器创建浏览器对象
driver = webdriver.PhantomJS()

# 如果没有在环境变量指定PhantomJS位置
# driver = webdriver.PhantomJS(executable_path="./phantomjs"))

# get方法会一直等到页面被完全加载，然后才会继续程序，通常测试会在这里选择 time.sleep(2)
driver.get("http://www.baidu.com/")

# 获取页面名为 wrapper的id标签的文本内容
data = driver.find_element_by_id("wrapper").text

# 打印数据内容
print data

# 打印页面标题 "百度一下，你就知道"
print driver.title

# 生成当前页面快照并保存
driver.save_screenshot("baidu.png")

# id="kw"是百度搜索输入框，输入字符串"长城"
driver.find_element_by_id("kw").send_keys(u"长城")

# id="su"是百度搜索按钮，click() 是模拟点击
driver.find_element_by_id("su").click()

# 获取新的页面快照
driver.save_screenshot("长城.png")

# 打印网页渲染后的源代码
print driver.page_source

# 获取当前页面Cookie
print driver.get_cookies()

# ctrl+a 全选输入框内容
driver.find_element_by_id("kw").send_keys(Keys.CONTROL,'a')

# ctrl+x 剪切输入框内容
driver.find_element_by_id("kw").send_keys(Keys.CONTROL,'x')

# 输入框重新输入内容
driver.find_element_by_id("kw").send_keys("itcast")

# 模拟Enter回车键
driver.find_element_by_id("su").send_keys(Keys.RETURN)

# 清除输入框内容
driver.find_element_by_id("kw").clear()

# 生成新的页面快照
driver.save_screenshot("itcast.png")

# 获取当前url
print driver.current_url

# 关闭当前页面，如果只有一个页面，会关闭浏览器
# driver.close()

# 关闭浏览器
driver.quit()    
```

### 133. Selenium页面操作

```python
# Selenium 的 WebDriver提供了各种方法来寻找元素，假设下面有一个表单输入框：
<input type="text" name="user-name" id="passwd-id" />
# 获取id标签值
element = driver.find_element_by_id("passwd-id")
# 获取name标签值
element = driver.find_element_by_name("user-name")
# 获取标签名值
element = driver.find_elements_by_tag_name("input")
# 也可以通过XPath来匹配
element = driver.find_element_by_xpath("//input[@id='passwd-id']")
```

### 134. Selenium定位UI元素

```python
find_element_by_id
find_elements_by_name
find_elements_by_xpath
find_elements_by_link_text
find_elements_by_partial_link_text
find_elements_by_tag_name
find_elements_by_class_name
find_elements_by_css_selector

# 1. By ID
<div id="coolestWidgetEvah">...</div>
# 实现:
element = driver.find_element_by_id("coolestWidgetEvah")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
element = driver.find_element(by=By.ID, value="coolestWidgetEvah")

# 2. By Class Name
<div class="cheese"><span>Cheddar</span></div><div class="cheese"><span>Gouda</span></div>
# 实现:
cheeses = driver.find_elements_by_class_name("cheese")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheeses = driver.find_elements(By.CLASS_NAME, "cheese")

# 3. By Tag Name
<iframe src="..."></iframe>
# 实现:
frame = driver.find_element_by_tag_name("iframe")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
frame = driver.find_element(By.TAG_NAME, "iframe")

# 4. By Name
<input name="cheese" type="text"/>
# 实现:
cheese = driver.find_element_by_name("cheese")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheese = driver.find_element(By.NAME, "cheese")

# 5. By Link Text
<a href="http://www.google.com/search?q=cheese">cheese</a>
# 实现:
cheese = driver.find_element_by_link_text("cheese")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheese = driver.find_element(By.LINK_TEXT, "cheese")

# 6. By Partial Link Text
<a href="http://www.google.com/search?q=cheese">search for cheese</a>>
# 实现:
cheese = driver.find_element_by_partial_link_text("cheese")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheese = driver.find_element(By.PARTIAL_LINK_TEXT, "cheese")

# 7. By CSS
<div id="food"><span class="dairy">milk</span><span class="dairy aged">cheese</span></div>
# 实现:
cheese = driver.find_element_by_css_selector("#food span.dairy.aged")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheese = driver.find_element(By.CSS_SELECTOR, "#food span.dairy.aged")

# 8. By XPath
<input type="text" name="example" />
<INPUT type="text" name="other" />
# 实现:
inputs = driver.find_elements_by_xpath("//input")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
inputs = driver.find_elements(By.XPATH, "//input")
```

### 135. Selenium鼠标动作链

```python
# 模拟一些鼠标操作，比如双击、右击、拖拽甚至按住不动等，我们可以通过导入 ActionChains 类来做到：
#导入 ActionChains 类
from selenium.webdriver import ActionChains

# 鼠标移动到 ac 位置
ac = driver.find_element_by_xpath('element')
ActionChains(driver).move_to_element(ac).perform()


# 在 ac 位置单击
ac = driver.find_element_by_xpath("elementA")
ActionChains(driver).move_to_element(ac).click(ac).perform()

# 在 ac 位置双击
ac = driver.find_element_by_xpath("elementB")
ActionChains(driver).move_to_element(ac).double_click(ac).perform()

# 在 ac 位置右击
ac = driver.find_element_by_xpath("elementC")
ActionChains(driver).move_to_element(ac).context_click(ac).perform()

# 在 ac 位置左键单击hold住
ac = driver.find_element_by_xpath('elementF')
ActionChains(driver).move_to_element(ac).click_and_hold(ac).perform()

# 将 ac1 拖拽到 ac2 位置
ac1 = driver.find_element_by_xpath('elementD')
ac2 = driver.find_element_by_xpath('elementE')
ActionChains(driver).drag_and_drop(ac1, ac2).perform()
```

### 136. Selenium填充表单

```python
# 处理 <select> </select>标签的下拉框
<select id="status" class="form-control valid" onchange="" name="status">
    <option value=""></option>
    <option value="0">未审核</option>
    <option value="1">初审通过</option>
    <option value="2">复审通过</option>
    <option value="3">审核不通过</option>
</select>
# Selenium专门提供了Select类来处理下拉框。  WebDriver 中提供了一个叫 Select 的方法

# 导入 Select 类
from selenium.webdriver.support.ui import Select

# 找到 name 的选项卡
select = Select(driver.find_element_by_name('status'))

# 
select.select_by_index(1)
select.select_by_value("0")
select.select_by_visible_text(u"未审核")
# 以上是三种选择下拉框的方式，它可以根据索引来选择，可以根据值来选择，可以根据文字来选择。注意：
#	index 索引从 0 开始
#	value是option标签的一个属性值，并不是显示在下拉框中的值
#	visible_text是在option标签文本的值，是显示在下拉框的值

# 全部取消选择: 
select.deselect_all()
```

### 137. Selenium弹窗处理

```python
# 当触发了某个事件之后，页面出现了弹窗提示，处理这个提示或者获取提示信息方法如下：

alert = driver.switch_to_alert()
```

### 138. Selenium页面切换

```python
# 切换窗口的方法如下：

driver.switch_to.window("this is window name")
# 也可以使用 window_handles 方法来获取每个窗口的操作对象。例如：

for handle in driver.window_handles:
    driver.switch_to_window(handle)
```

### 139. Selenium页面前进和后退

```python
# 操作页面的前进和后退功能：

driver.forward()     #前进
driver.back()        # 后退
```

### 140. Selenium Cookies

```python
# 获取页面每个Cookies值，用法如下

for cookie in driver.get_cookies():
    print "%s -> %s" % (cookie['name'], cookie['value'])
    
# 删除Cookies，用法如下

# By name
driver.delete_cookie("CookieName")

# all
driver.delete_all_cookies()    
```

### 141. Selenium页面等待

```python
# 现在的网页越来越多采用了 Ajax 技术，这样程序便不能确定何时某个元素完全加载出来了。如果实际页面等待时间过长导致某个dom元素还没出来，但是你的代码直接使用了这个WebElement，那么就会抛出NullPointer的异常。
# 为了避免这种元素定位困难而且会提高产生 ElementNotVisibleException 的概率。所以 Selenium 提供了两种等待方式，一种是隐式等待，一种是显式等待。
# 隐式等待是等待特定的时间，显式等待是指定某一条件直到这个条件成立时继续执行。

# 显式等待
# 显式等待指定某个条件，然后设置最长等待时间。如果在这个时间还没有找到元素，那么便会抛出异常了。

from selenium import webdriver
from selenium.webdriver.common.by import By
# WebDriverWait 库，负责循环等待
from selenium.webdriver.support.ui import WebDriverWait
# expected_conditions 类，负责条件出发
from selenium.webdriver.support import expected_conditions as EC

driver = webdriver.Chrome()
driver.get("http://www.xxxxx.com/loading")
try:
    # 页面一直循环，直到 id="myDynamicElement" 出现
    element = WebDriverWait(driver, 10).until(
        EC.presence_of_element_located((By.ID, "myDynamicElement"))
    )
finally:
    driver.quit()
 
# 如果不写参数，程序默认会 0.5s 调用一次来查看元素是否已经生成，如果本来元素就是存在的，那么会立即返回。

# 下面是一些内置的等待条件，你可以直接调用这些条件，而不用自己写某些等待条件了。

title_is
title_contains
presence_of_element_located
visibility_of_element_located
visibility_of
presence_of_all_elements_located
text_to_be_present_in_element
text_to_be_present_in_element_value
frame_to_be_available_and_switch_to_it
invisibility_of_element_located
element_to_be_clickable – it is Displayed and Enabled.
staleness_of
element_to_be_selected
element_located_to_be_selected
element_selection_state_to_be
element_located_selection_state_to_be
alert_is_present


# 隐式等待
# 隐式等待比较简单，就是简单地设置一个等待时间，单位为秒。

from selenium import webdriver

driver = webdriver.Chrome()
driver.implicitly_wait(10) # seconds
driver.get("http://www.xxxxx.com/loading")
myDynamicElement = driver.find_element_by_id("myDynamicElement")

# 如果不设置，默认等待时间为0。
```

### 142. Selenium 模拟登陆

```python
# douban.py

from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time

driver = webdriver.PhantomJS()
driver.get("http://www.douban.com")

# 输入账号密码
driver.find_element_by_name("form_email").send_keys("xxxxx@xxxx.com")
driver.find_element_by_name("form_password").send_keys("xxxxxxxx")

# 模拟点击登录
driver.find_element_by_xpath("//input[@class='bn-submit']").click()

# 等待3秒
time.sleep(3)

# 生成登陆后快照
driver.save_screenshot("douban.png")

with open("douban.html", "w") as file:
    file.write(driver.page_source)

driver.quit()
```

### 143. Selenium 模拟动态点击

```python
# -*- coding:utf-8 -*-

# python的测试模块
import unittest
from selenium import webdriver
from bs4 import BeautifulSoup


class douyuSelenium(unittest.TestCase):
    # 初始化方法
    def setUp(self):
        self.driver = webdriver.PhantomJS()

    #具体的测试用例方法，一定要以test开头
    def testDouyu(self):
        self.driver.get('http://www.douyu.com/directory/all')
        while True:
            # 指定xml解析
            soup = BeautifulSoup(driver.page_source, 'xml')
            # 返回当前页面所有房间标题列表 和 观众人数列表
            titles = soup.find_all('h3', {'class': 'ellipsis'})
            nums = soup.find_all('span', {'class': 'dy-num fr'})

            # 使用zip()函数来可以把列表合并，并创建一个元组对的列表[(1,2), (3,4)]
            for title, num in zip(nums, titles):
                print u"观众人数:" + num.get_text().strip(), u"\t房间标题: " + title.get_text().strip()
            # page_source.find()未找到内容则返回-1
            if driver.page_source.find('shark-pager-disable-next') != -1:
                break
            # 模拟下一页点击
            self.driver.find_element_by_class_name('shark-pager-next').click()

    # 退出时的清理方法
    def tearDown(self):
        print '加载完成...'
        self.driver.quit()

if __name__ == "__main__":
    unittest.main()
```

### 144. Selenium 执行 JavaScript 语句

```python
# 隐藏百度图片
from selenium import webdriver

driver = webdriver.PhantomJS()
driver.get("https://www.baidu.com/")

# 给搜索输入框标红的javascript脚本
js = "var q=document.getElementById(\"kw\");q.style.border=\"2px solid red\";"

# 调用给搜索输入框标红js脚本
driver.execute_script(js)

#查看页面快照
driver.save_screenshot("redbaidu.png")

#js隐藏元素，将获取的图片元素隐藏
img = driver.find_element_by_xpath("//*[@id='lg']/img")
driver.execute_script('$(arguments[0]).fadeOut()',img)

# 向下滚动到页面底部
driver.execute_script("$('.scroll_top').click(function(){$('html,body').animate({scrollTop: '0px'}, 800);});")

#查看页面快照
driver.save_screenshot("nullbaidu.png")

driver.quit()


# 模拟滚动条滚动到底部
from selenium import webdriver
import time

driver = webdriver.PhantomJS()
driver.get("https://movie.douban.com/typerank?type_name=剧情&type=11&interval_id=100:90&action=")

# 向下滚动10000像素
js = "document.body.scrollTop=10000"
#js="var q=document.documentElement.scrollTop=10000"
time.sleep(3)

#查看页面快照
driver.save_screenshot("douban.png")

# 执行JS语句
driver.execute_script(js)
time.sleep(10)

#查看页面快照
driver.save_screenshot("newdouban.png")

driver.quit()
```

### 145. ORC库概述  

```python
# 图像处理 Tesseract   https://pypi.org/project/pytesseract/
# 安装:
# windows 下载可执行安装文件https://code.google.com/p/tesseract-ocr/downloads/list安装。
# linux    apt-get install tesseract-ocr
# 使用: tesseract test.png test.txt
# 安装pytesseract:
pip install pytessera
```

## 七 Scrapy 框架

### 146. Scrapy 框架介绍  

```python
# Scrapy框架官方网址：http://doc.scrapy.org/en/latest
# Scrapy中文维护站点：http://scrapy-chs.readthedocs.io/zh_CN/latest/index.html
# Scrapy 使用了 Twisted['twɪstɪd](其主要对手是Tornado)异步网络框架来处理网络通讯，可以加快我们的下载速度，不用自己去实现异步框架，并且包含了各种中间件接口，可以灵活的完成各种需求。

# 架构:
# Scrapy Engine(引擎): 负责Spider、ItemPipeline、Downloader、Scheduler中间的通讯，信号、数据传递等。
# Scheduler(调度器): 它负责接受引擎发送过来的Request请求，并按照一定的方式进行整理排列，入队，当引擎需要时，交还给引擎。
# Downloader（下载器）：负责下载Scrapy Engine(引擎)发送的所有Requests请求，并将其获取到的Responses交还给Scrapy Engine(引擎)，由引擎交给Spider来处理，
# Spider（爬虫）：它负责处理所有Responses,从中分析提取数据，获取Item字段需要的数据，并将需要跟进的URL提交给引擎，再次进入Scheduler(调度器)，
# Item Pipeline(管道)：它负责处理Spider中获取到的Item，并进行进行后期处理（详细分析、过滤、存储等）的地方.
# Downloader Middlewares（下载中间件）：你可以当作是一个可以自定义扩展下载功能的组件。
# Spider Middlewares（Spider中间件）：你可以理解为是一个可以自定扩展和操作引擎和Spider中间通信的功能组件（比如进入Spider的Responses;和从Spider出去的Requests）


# 安装:
# 安装非Python的依赖:
sudo apt-get install python-dev python-pip libxml2-dev libxslt1-dev zlib1g-dev libffi-dev libssl-dev
# 通过pip 安装 Scrapy 框架:
sudo pip install scrapy
```

### 147. 项目入门 

```python
# 一. 新建项目(scrapy startproject)
# 1. 新建项目
scrapy startproject mySpider
# 2. 目录结构
├── mySpider
│   ├── __init__.py
│   ├── items.py
│   ├── middlewares.py
│   ├── pipelines.py
│   ├── settings.py
│   └── spiders
│       └── __init__.py
└── scrapy.cfg

# scrapy.cfg ：项目的配置文件
# mySpider/ ：项目的Python模块，将会从这里引用代码
# mySpider/items.py ：项目的目标文件
# mySpider/pipelines.py ：项目的管道文件
# mySpider/settings.py ：项目的设置文件
# mySpider/spiders/ ：存储爬虫代码目录

#  二、明确目标(mySpider/items.py)
# 我们打算抓取：http://www.itcast.cn/channel/teacher.shtml 网站里的所有讲师的姓名、职称和个人信息。
# 1.打开mySpider目录下的items.py
# 2.Item 定义结构化数据字段，用来保存爬取到的数据，有点像Python中的dict，但是提供了一些额外的保护减少错误。
# 3. 可以通过创建一个 scrapy.Item 类， 并且定义类型为 scrapy.Field的类属性来定义一个Item（可以理解成类似于ORM的映射关系）。
# 4.接下来，创建一个ItcastItem 类，和构建item模型（model）。
import scrapy

class ItcastItem(scrapy.Item):
    name = scrapy.Field()
    level = scrapy.Field()
    info = scrapy.Field()
 
# 三、制作爬虫 （spiders/itcastSpider.py）
# 爬虫功能要分两步：
# 1. 爬数据
# 在当前目录下输入命令，将在mySpider/spider目录下创建一个名为itcast的爬虫，并指定爬取域的范围：
scrapy genspider itcast "itcast.cn"
# 打开 mySpider/spider目录里的 itcast.py，默认增加了下列代码:
import scrapy

class ItcastSpider(scrapy.Spider):
    name = "itcast"
    allowed_domains = ["itcast.cn"]
    start_urls = (
        'http://www.itcast.cn/',
    )

    def parse(self, response):
        pass
# 也可以自行创建itcast.py并编写上面的代码，只不过使用命令可以免去编写固定代码的麻烦
# 要建立一个Spider， 你必须用scrapy.Spider类创建一个子类，并确定了三个强制的属性 和 一个方法。
#	name = "" ：这个爬虫的识别名称，必须是唯一的，在不同的爬虫必须定义不同的名字。
#	allow_domains = [] 是搜索的域名范围，也就是爬虫的约束区域，规定爬虫只爬取这个域名下的网页，不存在的URL会被忽略。
#	start_urls = () ：爬取的URL元祖/列表。爬虫从这里开始抓取数据，所以，第一次下载的数据将会从这些urls开始。其他子URL将会从这些起始URL中继承性生成。
#	parse(self, response) ：解析的方法，每个初始URL完成下载后将被调用，调用的时候传入从每一个URL传回的Response对象来作为唯一参数，主要作用如下：
#		负责解析返回的网页数据(response.body)，提取结构化数据(生成item)
#		生成需要下一页的URL请求。

# 将start_urls的值修改为需要爬取的第一个url
start_urls = ("http://www.itcast.cn/channel/teacher.shtml",)
# 修改parse()方法:
def parse(self, response):
    filename = "teacher.html"
    open(filename, 'w').write(response.body)

# 在mySpider目录下执行： 
scrapy crawl itcast
# 运行之后，如果打印的日志出现 [scrapy] INFO: Spider closed (finished)，代表执行完成。 之后当前文件夹中就出现了一个 teacher.html 文件，里面就是我们刚刚要爬取的网页的全部源代码信息。

# 注意点:
# 注意，Python2.x默认编码环境是ASCII，当和取回的数据编码格式不一致时，可能会造成乱码；
# 我们可以指定保存内容的编码格式，一般情况下，我们可以在代码最上方添加：

    import sys
    reload(sys)
    sys.setdefaultencoding("utf-8")

# 2. 取数据
# mySpider/spiders/itcastspider.py
# 之前在mySpider/items.py 里定义了一个ItcastItem类。 这里引入进来
from mySpider.items import ItcastItem
# 然后将我们得到的数据封装到一个 ItcastItem 对象中，可以保存每个老师的属性：
from mySpider.items import ItcastItem

def parse(self, response):
    #open("teacher.html","wb").write(response.body).close()

    # 存放老师信息的集合
    items = []

    for each in response.xpath("//div[@class='li_txt']"):
        # 将我们得到的数据封装到一个 `ItcastItem` 对象
        item = ItcastItem()
        #extract()方法返回的都是unicode字符串
        name = each.xpath("h3/text()").extract()
        title = each.xpath("h4/text()").extract()
        info = each.xpath("p/text()").extract()

        #xpath返回的是包含一个元素的列表
        item['name'] = name[0]
        item['title'] = title[0]
        item['info'] = info[0]

        items.append(item)
        
        # 将获取的数据交给pipelines
        # yield item

    # 直接返回最后数据,不经过pipeline
    return items


# 保存数据
# scrapy保存信息的最简单的方法主要有四种，-o 输出指定格式的文件，，命令如下：
# json格式，默认为Unicode编码
scrapy crawl itcast -o teachers.json
# # json lines格式，默认为Unicode编码
scrapy crawl itcast -o teachers.jsonl
# # csv 逗号表达式，可用Excel打开
scrapy crawl itcast -o teachers.csv
# xml格式
scrapy crawl itcast -o teachers.xml

```

### 148. Scrapy Shell 

```python
# 官方文档：http://scrapy-chs.readthedocs.io/zh_CN/latest/topics/shell.html
# 启动Scrapy Shell
# 进入项目的根目录，执行下列命令来启动shell:
scrapy shell "http://www.itcast.cn/channel/teacher.shtml"
# Scrapy Shell根据下载的页面会自动创建一些方便使用的对象，例如 Response 对象，以及 Selector 对象 (对HTML及XML内容)。
#	当shell载入后，将得到一个包含response数据的本地 response 变量，输入 response.body将输出response的包体，输出 response.headers 可以看到response的包头。
#	输入 response.selector 时， 将获取到一个response 初始化的类 Selector 的对象，此时可以通过使用 response.selector.xpath()或response.selector.css() 来对 response 进行查询。
#	Scrapy也提供了一些快捷方式, 例如 response.xpath()或response.css()同样可以生效


# Selectors选择器
# Scrapy Selectors 内置 XPath 和 CSS Selector 表达式机制
# Selector有四个基本的方法，最常用的还是xpath:
#	xpath(): 传入xpath表达式，返回该表达式所对应的所有节点的selector list列表
#	extract(): 序列化该节点为Unicode字符串并返回list
#	css(): 传入CSS表达式，返回该表达式所对应的所有节点的selector list列表，语法同 BeautifulSoup4
#	re(): 根据传入的正则表达式对数据进行提取，返回Unicode字符串list列表

# XPath表达式的例子及对应的含义:
# /html/head/title: 选择<HTML>文档中 <head> 标签内的 <title> 元素
# /html/head/title/text(): 选择上面提到的 <title> 元素的文字
# //td: 选择所有的 <td> 元素
# //div[@class="mine"]: 选择所有具有 class="mine" 属性的 div 元素


# 尝试Selector
# 启动
scrapy shell "http://hr.tencent.com/position.php?&start=0#a"

# 返回 xpath选择器对象列表
response.xpath('//title')
[<Selector xpath='//title' data=u'<title>\u804c\u4f4d\u641c\u7d22 | \u793e\u4f1a\u62db\u8058 | Tencent \u817e\u8baf\u62db\u8058</title'>]

# 使用 extract()方法返回 Unicode字符串列表
response.xpath('//title').extract()
[u'<title>\u804c\u4f4d\u641c\u7d22 | \u793e\u4f1a\u62db\u8058 | Tencent \u817e\u8baf\u62db\u8058</title>']

# 打印列表第一个元素，终端编码格式显示
print response.xpath('//title').extract()[0]
<title>职位搜索 | 社会招聘 | Tencent 腾讯招聘</title>

# 返回 xpath选择器对象列表
response.xpath('//title/text()')
<Selector xpath='//title/text()' data=u'\u804c\u4f4d\u641c\u7d22 | \u793e\u4f1a\u62db\u8058 | Tencent \u817e\u8baf\u62db\u8058'>

# 返回列表第一个元素的Unicode字符串
response.xpath('//title/text()')[0].extract()
u'\u804c\u4f4d\u641c\u7d22 | \u793e\u4f1a\u62db\u8058 | Tencent \u817e\u8baf\u62db\u8058'

# 按终端编码格式显示
print response.xpath('//title/text()')[0].extract()
职位搜索 | 社会招聘 | Tencent 腾讯招聘

response.xpath('//*[@class="even"]')
职位名称:

print site[0].xpath('./td[1]/a/text()').extract()[0]
TEG15-运营开发工程师（深圳）
职位名称详情页:

print site[0].xpath('./td[1]/a/@href').extract()[0]
position_detail.php?id=20744&keywords=&tid=0&lid=0
职位类别:

print site[0].xpath('./td[2]/text()').extract()[0]
技术类
```

### 149. item pipeline

```python
# item pipiline组件是一个独立的Python类，其中process_item()方法必须实现:
import something

class SomethingPipeline(object):
    def __init__(self):    
        # 可选实现，做参数初始化等
        # doing something

    def process_item(self, item, spider):
        # item (Item 对象) – 被爬取的item
        # spider (Spider 对象) – 爬取该item的spider
        # 这个方法必须实现，每个item pipeline组件都需要调用该方法，
        # 这个方法必须返回一个 Item 对象，被丢弃的item将不会被之后的pipeline组件所处理。
        return item

    def open_spider(self, spider):
        # spider (Spider 对象) – 被开启的spider
        # 可选实现，当spider被开启时，这个方法被调用。

    def close_spider(self, spider):
        # spider (Spider 对象) – 被关闭的spider
        # 可选实现，当spider被关闭时，这个方法被调用
        
        
# 启用一个Item Pipeline组件
# 为了启用Item Pipeline组件，必须将它的类添加到 settings.py文件ITEM_PIPELINES 配置，
# Configure item pipelines
# See http://scrapy.readthedocs.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
    #'mySpider.pipelines.SomePipeline': 300,
    "mySpider.pipelines.ItcastJsonPipeline":300
}
# 分配给每个类的整型值，确定了他们运行的顺序，item按数字从低到高的顺序，通过pipeline，通常将这些数字定义在0-1000范围内（0-1000随意设置，数值越低，组件的优先级越高）

# 启动爬虫
scrapy crawl itcast
```

### 150. Spider

```python
# Spider类定义了如何爬取某个(或某些)网站。包括了爬取的动作(例如:是否跟进链接)以及如何从网页的内容中提取结构化数据(爬取item)。 换句话说，Spider就是您定义爬取的动作及分析某个网页(或者是有些网页)的地方。

# class scrapy.Spider是最基本的类，所有编写的爬虫必须继承这个类。

# 主要用到的函数及调用顺序为：

# __init__() : 初始化爬虫名字和start_urls列表

# start_requests() 调用make_requests_from url():生成Requests对象交给Scrapy下载并返回response

# parse() : 解析response，并返回Item或Requests（需指定回调函数）。Item传给Item pipline持久化 ， 而Requests交由Scrapy下载，并由指定的回调函数处理（默认parse())，一直进行循环，直到处理完所有的数据为止。


# 源码参考
#所有爬虫的基类，用户定义的爬虫必须从这个类继承
class Spider(object_ref):

    #定义spider名字的字符串(string)。spider的名字定义了Scrapy如何定位(并初始化)spider，所以其必须是唯一的。
    #name是spider最重要的属性，而且是必须的。
    #一般做法是以该网站(domain)(加或不加 后缀 )来命名spider。 例如，如果spider爬取 mywebsite.com ，该spider通常会被命名为 mywebsite
    name = None

    #初始化，提取爬虫名字，start_ruls
    def __init__(self, name=None, **kwargs):
        if name is not None:
            self.name = name
        # 如果爬虫没有名字，中断后续操作则报错
        elif not getattr(self, 'name', None):
            raise ValueError("%s must have a name" % type(self).__name__)

        # python 对象或类型通过内置成员__dict__来存储成员信息
        self.__dict__.update(kwargs)

        #URL列表。当没有指定的URL时，spider将从该列表中开始进行爬取。 因此，第一个被获取到的页面的URL将是该列表之一。 后续的URL将会从获取到的数据中提取。
        if not hasattr(self, 'start_urls'):
            self.start_urls = []

    # 打印Scrapy执行后的log信息
    def log(self, message, level=log.DEBUG, **kw):
        log.msg(message, spider=self, level=level, **kw)

    # 判断对象object的属性是否存在，不存在做断言处理
    def set_crawler(self, crawler):
        assert not hasattr(self, '_crawler'), "Spider already bounded to %s" % crawler
        self._crawler = crawler

    @property
    def crawler(self):
        assert hasattr(self, '_crawler'), "Spider not bounded to any crawler"
        return self._crawler

    @property
    def settings(self):
        return self.crawler.settings

    #该方法将读取start_urls内的地址，并为每一个地址生成一个Request对象，交给Scrapy下载并返回Response
    #该方法仅调用一次
    def start_requests(self):
        for url in self.start_urls:
            yield self.make_requests_from_url(url)

    #start_requests()中调用，实际生成Request的函数。
    #Request对象默认的回调函数为parse()，提交的方式为get
    def make_requests_from_url(self, url):
        return Request(url, dont_filter=True)

    #默认的Request对象回调函数，处理返回的response。
    #生成Item或者Request对象。用户必须实现这个类
    def parse(self, response):
        raise NotImplementedError

    @classmethod
    def handles_request(cls, request):
        return url_is_from_spider(request.url, cls)

    def __str__(self):
        return "<%s %r at 0x%0x>" % (type(self).__name__, self.name, id(self))

    __repr__ = __str__
    
# 主要属性和方法
# name

#	定义spider名字的字符串。

#	例如，如果spider爬取 mywebsite.com ，该spider通常会被命名为 mywebsite

# allowed_domains

#	包含了spider允许爬取的域名(domain)的列表，可选。

# start_urls

#	初始URL元祖/列表。当没有制定特定的URL时，spider将从该列表中开始进行爬取。

# start_requests(self)

#	该方法必须返回一个可迭代对象(iterable)。该对象包含了spider用于爬取（默认实现是使用 start_urls 的url）的第一个Request。

#	当spider启动爬取并且未指定start_urls时，该方法被调用。

# parse(self, response)

#	当请求url返回网页没有指定回调函数时，默认的Request对象回调函数。用来处理网页返回的response，以及生成Item或者Request对象。

# log(self, message[, level, component])

#	使用 scrapy.log.msg() 方法记录(log)message。 


# parse()方法的工作机制：
# 1. 因为使用的yield，而不是return。parse函数将会被当做一个生成器使用。scrapy会逐一获取parse方法中生成的结果，并判断该结果是一个什么样的类型；
# 2. 如果是request则加入爬取队列，如果是item类型则使用pipeline处理，其他类型则返回错误信息。
# 3. scrapy取到第一部分的request不会立马就去发送这个request，只是把这个request放到队列里，然后接着从生成器里获取；
# 4. 取尽第一部分的request，然后再获取第二部分的item，取到item了，就会放到对应的pipeline里处理；
# 5. parse()方法作为回调函数(callback)赋值给了Request，指定parse()方法来处理这些请求 scrapy.Request(url, callback=self.parse)
# 6. Request对象经过调度，执行生成 scrapy.http.response()的响应对象，并送回给parse()方法，直到调度器中没有Request（递归的思路）
# 7. 取尽之后，parse()工作结束，引擎再根据队列和pipelines中的内容去执行相应的操作；
# 8. 程序在取得各个页面的items前，会先处理完之前所有的request队列里的请求，然后再提取items。
# 9. 这一切的一切，Scrapy引擎和调度器将负责到底。
```

###151. CrawlSpiders

```python
# 通过下面的命令可以快速创建 CrawlSpider模板 的代码：
scrapy genspider -t crawl tencent tencent.com


class scrapy.spiders.CrawlSpider
# 它是Spider的派生类，Spider类的设计原则是只爬取start_url列表中的网页，而CrawlSpider类定义了一些规则(rule)来提供跟进link的方便的机制，从爬取的网页中获取link并继续爬取的工作更适合。

# 源码参考:
class CrawlSpider(Spider):
    rules = ()
    def __init__(self, *a, **kw):
        super(CrawlSpider, self).__init__(*a, **kw)
        self._compile_rules()

    #首先调用parse()来处理start_urls中返回的response对象
    #parse()则将这些response对象传递给了_parse_response()函数处理，并设置回调函数为parse_start_url()
    #设置了跟进标志位True
    #parse将返回item和跟进了的Request对象    
    def parse(self, response):
        return self._parse_response(response, self.parse_start_url, cb_kwargs={}, follow=True)

    #处理start_url中返回的response，需要重写
    def parse_start_url(self, response):
        return []

    def process_results(self, response, results):
        return results

    #从response中抽取符合任一用户定义'规则'的链接，并构造成Resquest对象返回
    def _requests_to_follow(self, response):
        if not isinstance(response, HtmlResponse):
            return
        seen = set()
        #抽取之内的所有链接，只要通过任意一个'规则'，即表示合法
        for n, rule in enumerate(self._rules):
            links = [l for l in rule.link_extractor.extract_links(response) if l not in seen]
            #使用用户指定的process_links处理每个连接
            if links and rule.process_links:
                links = rule.process_links(links)
            #将链接加入seen集合，为每个链接生成Request对象，并设置回调函数为_repsonse_downloaded()
            for link in links:
                seen.add(link)
                #构造Request对象，并将Rule规则中定义的回调函数作为这个Request对象的回调函数
                r = Request(url=link.url, callback=self._response_downloaded)
                r.meta.update(rule=n, link_text=link.text)
                #对每个Request调用process_request()函数。该函数默认为indentify，即不做任何处理，直接返回该Request.
                yield rule.process_request(r)

    #处理通过rule提取出的连接，并返回item以及request
    def _response_downloaded(self, response):
        rule = self._rules[response.meta['rule']]
        return self._parse_response(response, rule.callback, rule.cb_kwargs, rule.follow)

    #解析response对象，会用callback解析处理他，并返回request或Item对象
    def _parse_response(self, response, callback, cb_kwargs, follow=True):
        #首先判断是否设置了回调函数。（该回调函数可能是rule中的解析函数，也可能是 parse_start_url函数）
        #如果设置了回调函数（parse_start_url()），那么首先用parse_start_url()处理response对象，
        #然后再交给process_results处理。返回cb_res的一个列表
        if callback:
            #如果是parse调用的，则会解析成Request对象
            #如果是rule callback，则会解析成Item
            cb_res = callback(response, **cb_kwargs) or ()
            cb_res = self.process_results(response, cb_res)
            for requests_or_item in iterate_spider_output(cb_res):
                yield requests_or_item

        #如果需要跟进，那么使用定义的Rule规则提取并返回这些Request对象
        if follow and self._follow_links:
            #返回每个Request对象
            for request_or_item in self._requests_to_follow(response):
                yield request_or_item

    def _compile_rules(self):
        def get_method(method):
            if callable(method):
                return method
            elif isinstance(method, basestring):
                return getattr(self, method, None)

        self._rules = [copy.copy(r) for r in self.rules]
        for rule in self._rules:
            rule.callback = get_method(rule.callback)
            rule.process_links = get_method(rule.process_links)
            rule.process_request = get_method(rule.process_request)

    def set_crawler(self, crawler):
        super(CrawlSpider, self).set_crawler(crawler)
        self._follow_links = crawler.settings.getbool('CRAWLSPIDER_FOLLOW_LINKS',True)
        
        
# CrawlSpider继承于Spider类，除了继承过来的属性外（name、allow_domains），还提供了新的属性和方法:
# LinkExtractors
class scrapy.linkextractors.LinkExtractor
# Link Extractors 的目的很简单: 提取链接｡
# 每个LinkExtractor有唯一的公共方法是 extract_links()，它接收一个 Response 对象，并返回一个 scrapy.link.Link 对象。
# Link Extractors要实例化一次，并且 extract_links 方法会根据不同的 response 调用多次提取链接｡
class scrapy.linkextractors.LinkExtractor(
    allow = (),
    deny = (),
    allow_domains = (),
    deny_domains = (),
    deny_extensions = None,
    restrict_xpaths = (),
    tags = ('a','area'),
    attrs = ('href'),
    canonicalize = True,
    unique = True,
    process_value = None
)

# 主要参数：

#	allow：满足括号中“正则表达式”的值会被提取，如果为空，则全部匹配。

#	deny：与这个正则表达式(或正则表达式列表)不匹配的URL一定不提取。

#	allow_domains：会被提取的链接的domains。

#	deny_domains：一定不会被提取链接的domains。

#	restrict_xpaths：使用xpath表达式，和allow共同作用过滤链接。


# rules
# 在rules中包含一个或多个Rule对象，每个Rule对爬取网站的动作定义了特定操作。如果多个rule匹配了相同的链接，则根据规则在本集合中被定义的顺序，第一个会被使用。
class scrapy.spiders.Rule(
        link_extractor, 
        callback = None, 
        cb_kwargs = None, 
        follow = None, 
        process_links = None, 
        process_request = None
)

# link_extractor：是一个Link Extractor对象，用于定义需要提取的链接。

# callback： 从link_extractor中每获取到链接时，参数所指定的值作为回调函数，该回调函数接受一个response作为其第一个参数。

# 注意：当编写爬虫规则时，避免使用parse作为回调函数。由于CrawlSpider使用parse方法来实现其逻辑，如果覆盖了 parse方法，crawl spider将会运行失败。

# follow：是一个布尔(boolean)值，指定了根据该规则从response提取的链接是否需要跟进。 如果callback为None，follow 默认设置为True ，否则默认为False。

# process_links：指定该spider中哪个的函数将会被调用，从link_extractor中获取到链接列表时将会调用该函数。该方法主要用来过滤。

# process_request：指定该spider中哪个的函数将会被调用， 该规则提取到每个request时都会调用该函数。 (用来过滤request)


# 爬取规则(Crawling rules)
# 用腾讯招聘为例，给出配合rule使用CrawlSpider的例子:
# 1. 首先运行
scrapy shell "http://hr.tencent.com/position.php?&start=0#a"
# 2. 导入LinkExtractor，创建LinkExtractor实例对象。
from scrapy.linkextractors import LinkExtractor

 page_lx = LinkExtractor(allow=('position.php?&start=\d+'))
# allow : LinkExtractor对象最重要的参数之一，这是一个正则表达式，必须要匹配这个正则表达式(或正则表达式列表)的URL才会被提取，如果没有给出(或为空), 它会匹配所有的链接｡

# deny : 用法同allow，只不过与这个正则表达式匹配的URL不会被提取)｡它的优先级高于 allow 的参数，如果没有给出(或None), 将不排除任何链接｡
# 3. 调用LinkExtractor实例的extract_links()方法查询匹配结果：
page_lx.extract_links(response)
# 4.没有查到：
[]
# 注意转义字符的问题，继续重新匹配：
page_lx = LinkExtractor(allow=('position\.php\?&start=\d+'))
 # page_lx = LinkExtractor(allow = ('start=\d+'))

 page_lx.extract_links(response)

# CrawlSpider 版本
# 修改以下代码:
#提取匹配 'http://hr.tencent.com/position.php?&start=\d+'的链接
page_lx = LinkExtractor(allow = ('start=\d+'))

rules = [
    #提取匹配,并使用spider的parse方法进行分析;并跟进链接(没有callback意味着follow默认为True)
    Rule(page_lx, callback = 'parse', follow = True)
]

# 注意:callback 千万不能写 parse，再次强调：由于CrawlSpider使用parse方法来实现其逻辑，如果覆盖了 parse方法，crawl spider将会运行失败。

#tencent.py

import scrapy
from scrapy.spiders import CrawlSpider, Rule
from scrapy.linkextractors import LinkExtractor
from mySpider.items import TencentItem

class TencentSpider(CrawlSpider):
    name = "tencent"
    allowed_domains = ["hr.tencent.com"]
    start_urls = [
        "http://hr.tencent.com/position.php?&start=0#a"
    ]

    page_lx = LinkExtractor(allow=("start=\d+"))

    rules = [
        Rule(page_lx, callback = "parseContent", follow = True)
    ]

    def parseContent(self, response):
        for each in response.xpath('//*[@class="even"]'):
            name = each.xpath('./td[1]/a/text()').extract()[0]
            detailLink = each.xpath('./td[1]/a/@href').extract()[0]
            positionInfo = each.xpath('./td[2]/text()').extract()[0]

            peopleNumber = each.xpath('./td[3]/text()').extract()[0]
            workLocation = each.xpath('./td[4]/text()').extract()[0]
            publishTime = each.xpath('./td[5]/text()').extract()[0]
            #print name, detailLink, catalog,recruitNumber,workLocation,publishTime

            item = TencentItem()
            item['name']=name.encode('utf-8')
            item['detailLink']=detailLink.encode('utf-8')
            item['positionInfo']=positionInfo.encode('utf-8')
            item['peopleNumber']=peopleNumber.encode('utf-8')
            item['workLocation']=workLocation.encode('utf-8')
            item['publishTime']=publishTime.encode('utf-8')

            yield item

    # parse() 方法不需要写     
    # def parse(self, response):                                              
    #     pass
    
# 运行： scrapy crawl tencent


# Logging
# Scrapy提供了log功能，可以通过 logging 模块使用。
# 可以修改配置文件settings.py
LOG_FILE = "TencentSpider.log"
LOG_LEVEL = "INFO"

# Log levels
# Scrapy提供5层logging级别:
# CRITICAL - 严重错误(critical)
# ERROR - 一般错误(regular errors)
# WARNING - 警告信息(warning messages)
# INFO - 一般信息(informational messages)
# DEBUG - 调试信息(debugging messages)

# logging设置
# 通过在setting.py中进行以下设置可以被用来配置logging:

# 1. LOG_ENABLED 默认: True，启用logging
# 2. LOG_ENCODING 默认: 'utf-8'，logging使用的编码
# 3. LOG_FILE 默认: None，在当前目录里创建logging输出文件的文件名
# 4. LOG_LEVEL 默认: 'DEBUG'，log的最低级别
# 5. LOG_STDOUT 默认: False 如果为 True，进程所有的标准输出(及错误)将会被重定向到log中。例如，执行 print "hello" ，其将会在Scrapy log中显示。
```

### 152. Request/Response

```python
# 1.  Request

# Request 部分源码：
# 部分代码
class Request(object_ref):

    def __init__(self, url, callback=None, method='GET', headers=None, body=None, 
                 cookies=None, meta=None, encoding='utf-8', priority=0,
                 dont_filter=False, errback=None):

        self._encoding = encoding  # this one has to be set first
        self.method = str(method).upper()
        self._set_url(url)
        self._set_body(body)
        assert isinstance(priority, int), "Request priority not an integer: %r" % priority
        self.priority = priority

        assert callback or not errback, "Cannot use errback without a callback"
        self.callback = callback
        self.errback = errback

        self.cookies = cookies or {}
        self.headers = Headers(headers or {}, encoding=encoding)
        self.dont_filter = dont_filter

        self._meta = dict(meta) if meta else None

    @property
    def meta(self):
        if self._meta is None:
            self._meta = {}
        return self._meta
    
# 其中，比较常用的参数： 
# url: 就是需要请求，并进行下一步处理的url

# callback: 指定该请求返回的Response，由那个函数来处理。

# method: 请求一般不需要指定，默认GET方法，可设置为"GET", "POST", "PUT"等，且保证字符串大写

# headers: 请求时，包含的头文件。一般不需要。内容一般如下：
        # 自己写过爬虫的肯定知道
        Host: media.readthedocs.org
        User-Agent: Mozilla/5.0 (Windows NT 6.2; WOW64; rv:33.0) Gecko/20100101 Firefox/33.0
        Accept: text/css,*/*;q=0.1
        Accept-Language: zh-cn,zh;q=0.8,en-us;q=0.5,en;q=0.3
        Accept-Encoding: gzip, deflate
        Referer: http://scrapy-chs.readthedocs.org/zh_CN/0.24/
        Cookie: _ga=GA1.2.1612165614.1415584110;
        Connection: keep-alive
        If-Modified-Since: Mon, 25 Aug 2014 21:59:35 GMT
        Cache-Control: max-age=0

# meta: 比较常用，在不同的请求之间传递数据使用的。字典dict型

        request_with_cookies = Request(
            url="http://www.example.com",
            cookies={'currency': 'USD', 'country': 'UY'},
            meta={'dont_merge_cookies': True}
        )

# encoding: 使用默认的 'utf-8' 就行。

# dont_filter: 表明该请求不由调度器过滤。这是当你想使用多次执行相同的请求,忽略重复的过滤器。默认为False。

# errback: 指定错误处理函数


# 2. Response
# 部分代码
class Response(object_ref):
    def __init__(self, url, status=200, headers=None, body='', flags=None, request=None):
        self.headers = Headers(headers or {})
        self.status = int(status)
        self._set_body(body)
        self._set_url(url)
        self.request = request
        self.flags = [] if flags is None else list(flags)

    @property
    def meta(self):
        try:
            return self.request.meta
        except AttributeError:
            raise AttributeError("Response.meta not available, this response is not tied to any request")
            

# 大部分参数和上面的差不多：            
status: 响应码
_set_body(body)： 响应体
_set_url(url)：响应url
self.request = request


# 发送POST请求
# 可以使用 yield scrapy.FormRequest(url, formdata, callback)方法发送POST请求。

# 如果希望程序执行一开始就发送POST请求，可以重写Spider类的start_requests(self) 方法，并且不再调用start_urls里的url。
class mySpider(scrapy.Spider):
    # start_urls = ["http://www.example.com/"]

    def start_requests(self):
        url = 'http://www.renren.com/PLogin.do'

        # FormRequest 是Scrapy发送POST请求的方法
        yield scrapy.FormRequest(
            url = url,
            formdata = {"email" : "mr_mao_hacker@163.com", "password" : "axxxxxxxe"},
            callback = self.parse_page
        )
    def parse_page(self, response):
        # do something
        

# 模拟登陆
使用FormRequest.from_response()方法模拟用户登录 https://docs.pythontab.com/scrapy/scrapy0.24/topics/request-response.html#topics-request-response-ref-request-userlogin
# 通常网站通过 实现对某些表单字段（如数据或是登录界面中的认证令牌等）的预填充。

# 使用Scrapy抓取网页时，如果想要预填充或重写像用户名、用户密码这些表单字段， 可以使用 FormRequest.from_response() 方法实现。

# 下面是使用这种方法的爬虫例子:  
import scrapy

class LoginSpider(scrapy.Spider):
    name = 'example.com'
    start_urls = ['http://www.example.com/users/login.php']

    def parse(self, response):
        return scrapy.FormRequest.from_response(
            response,
            formdata={'username': 'john', 'password': 'secret'},
            callback=self.after_login
        )

    def after_login(self, response):
        # check login succeed before going on
        if "authentication failed" in response.body:
            self.log("Login failed", level=log.ERROR)
            return

        # continue scraping with authenticated session...
        
```

### 153. 知乎爬虫案例参考：

```python
# zhihuSpider.py爬虫代码
#!/usr/bin/env python
# -*- coding:utf-8 -*-
from scrapy.spiders import CrawlSpider, Rule
from scrapy.selector import Selector
from scrapy.linkextractors import LinkExtractor
from scrapy import Request, FormRequest
from zhihu.items import ZhihuItem

class ZhihuSipder(CrawlSpider) :
    name = "zhihu"
    allowed_domains = ["www.zhihu.com"]
    start_urls = [
        "http://www.zhihu.com"
    ]
    rules = (
        Rule(LinkExtractor(allow = ('/question/\d+#.*?', )), callback = 'parse_page', follow = True),
        Rule(LinkExtractor(allow = ('/question/\d+', )), callback = 'parse_page', follow = True),
    )

    headers = {
    "Accept": "*/*",
    "Accept-Encoding": "gzip,deflate",
    "Accept-Language": "en-US,en;q=0.8,zh-TW;q=0.6,zh;q=0.4",
    "Connection": "keep-alive",
    "Content-Type":" application/x-www-form-urlencoded; charset=UTF-8",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.111 Safari/537.36",
    "Referer": "http://www.zhihu.com/"
    }

    #重写了爬虫类的方法, 实现了自定义请求, 运行成功后会调用callback回调函数
    def start_requests(self):
        return [Request("https://www.zhihu.com/login", meta = {'cookiejar' : 1}, callback = self.post_login)]

    def post_login(self, response):
        print 'Preparing login'
        #下面这句话用于抓取请求网页后返回网页中的_xsrf字段的文字, 用于成功提交表单
        xsrf = Selector(response).xpath('//input[@name="_xsrf"]/@value').extract()[0]
        print xsrf
        #FormRequeset.from_response是Scrapy提供的一个函数, 用于post表单
        #登陆成功后, 会调用after_login回调函数
        return [FormRequest.from_response(response,   #"http://www.zhihu.com/login",
                            meta = {'cookiejar' : response.meta['cookiejar']},
                            headers = self.headers,  #注意此处的headers
                            formdata = {
                            '_xsrf': xsrf,
                            'email': '1095511864@qq.com',
                            'password': '123456'
                            },
                            callback = self.after_login,
                            dont_filter = True
                            )]

    def after_login(self, response) :
        for url in self.start_urls :
            yield self.make_requests_from_url(url)

    def parse_page(self, response):
        problem = Selector(response)
        item = ZhihuItem()
        item['url'] = response.url
        item['name'] = problem.xpath('//span[@class="name"]/text()').extract()
        print item['name']
        item['title'] = problem.xpath('//h2[@class="zm-item-title zm-editable-content"]/text()').extract()
        item['description'] = problem.xpath('//div[@class="zm-editable-content"]/text()').extract()
        item['answer']= problem.xpath('//div[@class=" zm-editable-content clearfix"]/text()').extract()
        return item
    
# Item类设置
from scrapy.item import Item, Field

class ZhihuItem(Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    url = Field()  #保存抓取问题的url
    title = Field()  #抓取问题的标题
    description = Field()  #抓取问题的描述
    answer = Field()  #抓取问题的答案
    name = Field()  #个人用户的名称

# setting.py 设置抓取间隔
BOT_NAME = 'zhihu'

SPIDER_MODULES = ['zhihu.spiders']
NEWSPIDER_MODULE = 'zhihu.spiders'
DOWNLOAD_DELAY = 0.25   #设置下载间隔为250ms
```

### 154. Downloader Middlewares

```python
# 反反爬虫相关机制
# 通常防止爬虫被反主要有以下几个策略：
# 1.动态设置User-Agent（随机切换User-Agent，模拟不同用户的浏览器信息）

# 2.禁用Cookies（也就是不启用cookies middleware，不向Server发送cookies，有些网站通过cookie的使用发现爬虫行为）

# 3.可以通过COOKIES_ENABLED 控制 CookiesMiddleware 开启或关闭
设置延迟下载（防止访问过于频繁，设置为 2秒 或更高）

# 4.Google Cache 和 Baidu Cache：如果可能的话，使用谷歌/百度等搜索引擎服务器页面缓存获取页面数据。

# 5.使用IP地址池：VPN和代理IP，现在大部分网站都是根据IP来ban的。

# 6.使用 Crawlera（专用于爬虫的代理组件），正确配置和设置下载中间件后，项目所有的request都是通过crawlera发出。

DOWNLOADER_MIDDLEWARES = {
      'scrapy_crawlera.CrawleraMiddleware': 600
  }

  CRAWLERA_ENABLED = True
  CRAWLERA_USER = '注册/购买的UserKey'
  CRAWLERA_PASS = '注册/购买的Password'

    
# 设置下载中间件（Downloader Middlewares）
# 下载中间件是处于引擎(crawler.engine)和下载器(crawler.engine.download())之间的一层组件，可以有多个下载中间件被加载运行。

# 1. 当引擎传递请求给下载器的过程中，下载中间件可以对请求进行处理 （例如增加http header信息，增加proxy信息等）；

# 2. 在下载器完成http请求，传递响应给引擎的过程中， 下载中间件可以对响应进行处理（例如进行gzip的解压等）

# 要激活下载器中间件组件，将其加入到 DOWNLOADER_MIDDLEWARES 设置中。 该设置是一个字典(dict)，键为中间件类的路径，值为其中间件的顺序(order)。

# 示例:
DOWNLOADER_MIDDLEWARES = {
    'mySpider.middlewares.MyDownloaderMiddleware': 543,
}
# 编写下载器中间件十分简单。每个中间件组件是一个定义了以下一个或多个方法的Python类:
class scrapy.contrib.downloadermiddleware.DownloaderMiddleware


# process_request(self, request, spider)
# 1. 当每个request通过下载中间件时，该方法被调用。

# 2. process_request() 必须返回以下其中之一：一个 None 、一个 Response 对象、一个 Request 对象或 raise IgnoreRequest:

#	2.1 如果其返回 None ，Scrapy将继续处理该request，执行其他的中间件的相应方法，直到合适的下载器处理函数(download handler)被调用， 该request被执行(其response被下载)。

#	2.2 如果其返回 Response 对象，Scrapy将不会调用 任何 其他的 process_request() 或 process_exception() 方法，或相应地下载函数； 其将返回该response。 已安装的中间件的 process_response() 方法则会在每个response返回时被调用。

#	2.3 如果其返回 Request 对象，Scrapy则停止调用 process_request方法并重新调度返回的request。当新返回的request被执行后， 相应地中间件链将会根据下载的response被调用。

#	2.4 如果其raise一个 IgnoreRequest 异常，则安装的下载中间件的 process_exception() 方法会被调用。如果没有任何一个方法处理该异常， 则request的errback(Request.errback)方法会被调用。如果没有代码处理抛出的异常， 则该异常被忽略且不记录(不同于其他异常那样)。

# 3. 参数:

#	3.1 request (Request 对象) – 处理的request
#	3.2 spider (Spider 对象) – 该request对应的spider


# process_response(self, request, response, spider)
# 当下载器完成http请求，传递响应给引擎的时候调用

# 1. process_request() 必须返回以下其中之一: 返回一个 Response 对象、 返回一个 Request 对象或raise一个 IgnoreRequest 异常。

#	1.1 如果其返回一个 Response (可以与传入的response相同，也可以是全新的对象)， 该response会被在链中的其他中间件的 process_response() 方法处理。

#	1.2 如果其返回一个 Request 对象，则中间件链停止， 返回的request会被重新调度下载。处理类似于 process_request() 返回request所做的那样。

#	1.3 如果其抛出一个 IgnoreRequest 异常，则调用request的errback(Request.errback)。 如果没有代码处理抛出的异常，则该异常被忽略且不记录(不同于其他异常那样)。

# 2. 参数:

#	2.1 request (Request 对象) – response所对应的request
#	2.2 response (Response 对象) – 被处理的response
#	2.3 spider (Spider 对象) – response所对应的spider


# 案例:
# 1. 创建middlewares.py文件。
# Scrapy代理IP、Uesr-Agent的切换都是通过DOWNLOADER_MIDDLEWARES进行控制，我们在settings.py同级目录下创建middlewares.py文件，包装所有请求。
# middlewares.py

#!/usr/bin/env python
# -*- coding:utf-8 -*-

import random
import base64

from settings import USER_AGENTS
from settings import PROXIES

# 随机的User-Agent
class RandomUserAgent(object):
    def process_request(self, request, spider):
        useragent = random.choice(USER_AGENTS)

        request.headers.setdefault("User-Agent", useragent)

class RandomProxy(object):
    def process_request(self, request, spider):
        proxy = random.choice(PROXIES)

        if proxy['user_passwd'] is None:
            # 没有代理账户验证的代理使用方式
            request.meta['proxy'] = "http://" + proxy['ip_port']
        else:
            # 对账户密码进行base64编码转换
            base64_userpasswd = base64.b64encode(proxy['user_passwd'])
            # 对应到代理服务器的信令格式里
            request.headers['Proxy-Authorization'] = 'Basic ' + base64_userpasswd
            request.meta['proxy'] = "http://" + proxy['ip_port']
            
--------------------------------------------------------------------------------------------
为什么HTTP代理要使用base64编码：

HTTP代理的原理很简单，就是通过HTTP协议与代理服务器建立连接，协议信令中包含要连接到的远程主机的IP和端口号，如果有需要身份验证的话还需要加上授权信息，服务器收到信令后首先进行身份验证，通过后便与远程主机建立连接，连接成功之后会返回给客户端200，表示验证通过，就这么简单，下面是具体的信令格式：

CONNECT 59.64.128.198:21 HTTP/1.1
Host: 59.64.128.198:21
Proxy-Authorization: Basic bGV2I1TU5OTIz
User-Agent: OpenFetion
其中Proxy-Authorization是身份验证信息，Basic后面的字符串是用户名和密码组合后进行base64编码的结果，也就是对username:password进行base64编码。

HTTP/1.0 200 Connection established
OK，客户端收到收面的信令后表示成功建立连接，接下来要发送给远程主机的数据就可以发送给代理服务器了，代理服务器建立连接后会在根据IP地址和端口号对应的连接放入缓存，收到信令后再根据IP地址和端口号从缓存中找到对应的连接，将数据通过该连接转发出去。
--------------------------------------------------------------------------------------------
# 2. 修改settings.py配置USER_AGENTS和PROXIES
# 2.1 添加USER_AGENTS：
USER_AGENTS = [
    "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 2.0.50727; Media Center PC 6.0)",
    "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 1.0.3705; .NET CLR 1.1.4322)",
    "Mozilla/4.0 (compatible; MSIE 7.0b; Windows NT 5.2; .NET CLR 1.1.4322; .NET CLR 2.0.50727; InfoPath.2; .NET CLR 3.0.04506.30)",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN) AppleWebKit/523.15 (KHTML, like Gecko, Safari/419.3) Arora/0.3 (Change: 287 c9dfb30)",
    "Mozilla/5.0 (X11; U; Linux; en-US) AppleWebKit/527+ (KHTML, like Gecko, Safari/419.3) Arora/0.6",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.2pre) Gecko/20070215 K-Ninja/2.1.1",
    "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN; rv:1.9) Gecko/20080705 Firefox/3.0 Kapiko/3.0",
    "Mozilla/5.0 (X11; Linux i686; U;) Gecko/20070322 Kazehakase/0.4.5"
    ]
# 2.2  添加代理IP设置PROXIES：
# 免费代理IP可以网上搜索，或者付费购买一批可用的私密代理IP：
PROXIES = [
    {'ip_port': '111.8.60.9:8123', 'user_passwd': 'user1:pass1'},
    {'ip_port': '101.71.27.120:80', 'user_passwd': 'user2:pass2'},
    {'ip_port': '122.96.59.104:80', 'user_passwd': 'user3:pass3'},
    {'ip_port': '122.224.249.122:8088', 'user_passwd': 'user4:pass4'},
]
# 2.3 除非特殊需要，禁用cookies，防止某些网站根据Cookie来封锁爬虫。
COOKIES_ENABLED = False
# 2.4 设置下载延迟
DOWNLOAD_DELAY = 3
# 2.5  最后设置setting.py里的DOWNLOADER_MIDDLEWARES，添加自己编写的下载中间件类。
DOWNLOADER_MIDDLEWARES = {
    #'mySpider.middlewares.MyCustomDownloaderMiddleware': 543,
    'mySpider.middlewares.RandomUserAgent': 1,
    'mySpider.middlewares.ProxyMiddleware': 100
}
```

### 155. Settings

```python
# Scrapy设置(settings)提供了定制Scrapy组件的方法。可以控制包括核心(core)，插件(extension)，pipeline及spider组件。比如 设置Json Pipeliine、LOG_LEVEL等。

# 参考文档：http://scrapy-chs.readthedocs.io/zh_CN/1.0/topics/settings.html#topics-settings-ref

# 内置设置参考手册
# 1. BOT_NAME

默认: 'scrapybot'

当您使用 startproject 命令创建项目时其也被自动赋值。

# 2. CONCURRENT_ITEMS

默认: 100

Item Processor(即 Item Pipeline) 同时处理(每个response的)item的最大值。

# 3. CONCURRENT_REQUESTS
默认: 16

Scrapy downloader 并发请求(concurrent requests)的最大值。

# 4. DEFAULT_REQUEST_HEADERS
默认: 如下

{
'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
'Accept-Language': 'en',
}
Scrapy HTTP Request使用的默认header。

# 5. DEPTH_LIMIT

默认: 0

爬取网站最大允许的深度(depth)值。如果为0，则没有限制。

# 6. DOWNLOAD_DELAY
默认: 0

下载器在下载同一个网站下一个页面前需要等待的时间。该选项可以用来限制爬取速度， 减轻服务器压力。同时也支持小数:

# 7. DOWNLOAD_DELAY = 0.25 # 250 ms of delay

默认情况下，Scrapy在两个请求间不等待一个固定的值， 而是使用0.5到1.5之间的一个随机值 * DOWNLOAD_DELAY 的结果作为等待间隔。
DOWNLOAD_TIMEOUT

默认: 180

下载器超时时间(单位: 秒)。

# 8. ITEM_PIPELINES
默认: {}

保存项目中启用的pipeline及其顺序的字典。该字典默认为空，值(value)任意，不过值(value)习惯设置在0-1000范围内，值越小优先级越高。

ITEM_PIPELINES = {
'mySpider.pipelines.SomethingPipeline': 300,
'mySpider.pipelines.ItcastJsonPipeline': 800,
}
# 9. LOG_ENABLED

默认: True

是否启用logging。

# 10. LOG_ENCODING

默认: 'utf-8'

logging使用的编码。

# 11. LOG_LEVEL

默认: 'DEBUG'

log的最低级别。可选的级别有: CRITICAL、 ERROR、WARNING、INFO、DEBUG 。

# 12. USER_AGENT
默认: "Scrapy/VERSION (+http://scrapy.org)"

爬取的默认User-Agent，除非被覆盖。

PROXIES： 代理设置
示例：

PROXIES = [
  {'ip_port': '111.11.228.75:80', 'password': ''},
  {'ip_port': '120.198.243.22:80', 'password': ''},
  {'ip_port': '111.8.60.9:8123', 'password': ''},
  {'ip_port': '101.71.27.120:80', 'password': ''},
  {'ip_port': '122.96.59.104:80', 'password': ''},
  {'ip_port': '122.224.249.122:8088', 'password':''},
]
# 13. COOKIES_ENABLED = False
禁用Cookies
```

## 八 Scrapy 案例

### 156. 斗鱼App

```python
#  1. items.py
class DouyuspiderItem(scrapy.Item):
    name = scrapy.Field()# 存储照片的名字
    imagesUrls = scrapy.Field()# 照片的url路径
    imagesPath = scrapy.Field()# 照片保存在本地的路径

# 2. spiders/douyu.py
import scrapy
import json
from douyuSpider.items import DouyuspiderItem

class DouyuSpider(scrapy.Spider):
    name = "douyu"
    allowd_domains = ["http://capi.douyucdn.cn"]

    offset = 0
    url = "http://capi.douyucdn.cn/api/v1/getVerticalRoom?limit=20&offset="
    start_urls = [url + str(offset)]

  def parse(self, response):
      # 返回从json里获取 data段数据集合
      data = json.loads(response.text)["data"]

      for each in data:
          item = DouyuspiderItem()
          item["name"] = each["nickname"]
          item["imagesUrls"] = each["vertical_src"]

          yield item

      self.offset += 20
      yield scrapy.Request(self.url + str(self.offset), callback = self.parse)

        
# 3. 设置setting.py
ITEM_PIPELINES = {'douyuSpider.pipelines.ImagesPipeline': 1}

# Images 的存放位置，之后会在pipelines.py里调用
IMAGES_STORE = "/Users/Power/lesson_python/douyuSpider/Images"

# user-agent
USER_AGENT = 'DYZB/2.290 (iPhone; iOS 9.3.4; Scale/2.00)'

# 4. pipelines.py
import scrapy
import os
from scrapy.pipelines.images import ImagesPipeline
from scrapy.utils.project import get_project_settings

class ImagesPipeline(ImagesPipeline):
    IMAGES_STORE = get_project_settings().get("IMAGES_STORE")

    def get_media_requests(self, item, info):
        image_url = item["imagesUrls"]
        yield scrapy.Request(image_url)

    def item_completed(self, results, item, info):
        # 固定写法，获取图片路径，同时判断这个路径是否正确，如果正确，就放到 image_path里，ImagesPipeline源码剖析可见
        image_path = [x["path"] for ok, x in results if ok]

        os.rename(self.IMAGES_STORE + "/" + image_path[0], self.IMAGES_STORE + "/" + item["name"] + ".jpg")
        item["imagesPath"] = self.IMAGES_STORE + "/" + item["name"]

        return item

#get_media_requests的作用就是为每一个图片链接生成一个Request对象，这个方法的输出将作为item_completed的输入中的results，results是一个元组，每个元组包括(success, imageinfoorfailure)。如果success=true，imageinfoor_failure是一个字典，包括url/path/checksum三个key。

# 在项目根目录下新建main.py文件,用于调试
from scrapy import cmdline
cmdline.execute('scrapy crawl douyu'.split())

# 执行程序
py2 main.py
```

### 157. 阳光热线问政平台

```python
# http://wz.sun0769.com/index.php/question/questionType?type=4
# 爬取投诉帖子的编号、帖子的url、帖子的标题，和帖子里的内容。

# items.py
import scrapy

class DongguanItem(scrapy.Item):
    # 每个帖子的标题
    title = scrapy.Field()
    # 每个帖子的编号
    number = scrapy.Field()
    # 每个帖子的文字内容
    content = scrapy.Field()
    # 每个帖子的url
    url = scrapy.Field()
    
# spiders/sunwz.py
# Spider 版本
# -*- coding: utf-8 -*-

import scrapy
from dongguan.items import DongguanItem

class SunSpider(CrawlSpider):
    name = 'sun'
    allowed_domains = ['wz.sun0769.com']
    url = 'http://wz.sun0769.com/index.php/question/questionType?type=4&page='
    offset = 0
    start_urls = [url + str(offset)]

    def parse(self, response):
        # 取出每个页面里帖子链接列表
        links = response.xpath("//div[@class='greyframe']/table//td/a[@class='news14']/@href").extract()
        # 迭代发送每个帖子的请求，调用parse_item方法处理
        for link in links:
            yield scrapy.Request(link, callback = self.parse_item)
        # 设置页码终止条件，并且每次发送新的页面请求调用parse方法处理
        if self.offset <= 71130:
            self.offset += 30
            yield scrapy.Request(self.url + str(self.offset), callback = self.parse)

    # 处理每个帖子里
    def parse_item(self, response):
        item = DongguanItem()
        # 标题
        item['title'] = response.xpath('//div[contains(@class, "pagecenter p3")]//strong/text()').extract()[0]

        # 编号
        item['number'] = item['title'].split(' ')[-1].split(":")[-1]

        # 文字内容，默认先取出有图片情况下的文字内容列表
        content = response.xpath('//div[@class="contentext"]/text()').extract()
        # 如果没有内容，则取出没有图片情况下的文字内容列表
        if len(content) == 0:
            content = response.xpath('//div[@class="c1 text14_2"]/text()').extract()
            # content为列表，通过join方法拼接为字符串，并去除首尾空格
            item['content'] = "".join(content).strip()
        else:
            item['content'] = "".join(content).strip()

        # 链接
        item['url'] = response.url

        yield item
        
# CrawlSpider 版本
# -*- coding: utf-8 -*-
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from dongguan.items import DongguanItem
import time


class SunSpider(CrawlSpider):
    name = 'sun'
    allowed_domains = ['wz.sun0769.com']
    start_urls = ['http://wz.sun0769.com/index.php/question/questionType?type=4&page=']

    # 每一页的匹配规则
    pagelink = LinkExtractor(allow=('type=4'))
    # 每个帖子的匹配规则
    contentlink = LinkExtractor(allow=r'/html/question/\d+/\d+.shtml')

    rules = [
        # 本案例为特殊情况，需要调用deal_links方法处理每个页面里的链接
        Rule(pagelink, process_links = "deal_links", follow = True),
        Rule(contentlink, callback = 'parse_item')
    ]

    # 需要重新处理每个页面里的链接，将链接里的‘Type&type=4?page=xxx’替换为‘Type?type=4&page=xxx’（或者是Type&page=xxx?type=4’替换为‘Type?page=xxx&type=4’），否则无法发送这个链接
    def deal_links(self, links):
        for link in links:
            link.url = link.url.replace("?","&").replace("Type&", "Type?")
            print link.url
        return links


    def parse_item(self, response):
        print response.url
        item = DongguanItem()
        # 标题
        item['title'] = response.xpath('//div[contains(@class, "pagecenter p3")]//strong/text()').extract()[0]

        # 编号
        item['number'] = item['title'].split(' ')[-1].split(":")[-1]

        # 文字内容，默认先取出有图片情况下的文字内容列表
        content = response.xpath('//div[@class="contentext"]/text()').extract()
        # 如果没有内容，则取出没有图片情况下的文字内容列表
        if len(content) == 0:
            content = response.xpath('//div[@class="c1 text14_2"]/text()').extract()
            # content为列表，通过join方法拼接为字符串，并去除首尾空格
            item['content'] = "".join(content).strip()
        else:
            item['content'] = "".join(content).strip()

        # 链接
        item['url'] = response.url

        yield item
        
# pipelines.py
# -*- coding: utf-8 -*-

# 文件处理类库，可以指定编码格式
import codecs
import json

class JsonWriterPipeline(object):

    def __init__(self):
        # 创建一个只写文件，指定文本编码格式为utf-8
        self.filename = codecs.open('sunwz.json', 'w', encoding='utf-8')

    def process_item(self, item, spider):
        content = json.dumps(dict(item), ensure_ascii=False) + "\n"
        self.filename.write(content)
        return item

    def spider_closed(self, spider):
        self.file.close()
        
# settings.py
ITEM_PIPELINES = {
    'dongguan.pipelines.DongguanPipeline': 300,
}

# 日志文件名和处理等级
LOG_FILE = "dg.log"
LOG_LEVEL = "DEBUG"

# 在项目根目录下新建main.py文件,用于调试
from scrapy import cmdline
cmdline.execute('scrapy crawl sunwz'.split())

# 执行程序
py2 main.py
```

### 158. 新浪网分类资讯

```python
# 爬取新浪网导航页所有下所有大类、小类、小类里的子链接，以及子链接页面的新闻内容。
# items.py
import scrapy
import sys
reload(sys)
sys.setdefaultencoding("utf-8")

class SinaItem(scrapy.Item):
    # 大类的标题 和 url
    parentTitle = scrapy.Field()
    parentUrls = scrapy.Field()

    # 小类的标题 和 子url
    subTitle = scrapy.Field()
    subUrls = scrapy.Field()

    # 小类目录存储路径
    subFilename = scrapy.Field()

    # 小类下的子链接
    sonUrls = scrapy.Field()

    # 文章标题和内容
    head = scrapy.Field()
    content = scrapy.Field()
    
# spiders/sina.py
# -*- coding: utf-8 -*-
# -*- coding: utf-8 -*-

from Sina.items import SinaItem
import scrapy
import os

import sys
reload(sys)
sys.setdefaultencoding("utf-8")


class SinaSpider(scrapy.Spider):
    name= "sina"
    allowed_domains= ["sina.com.cn"]
    start_urls= [
       "http://news.sina.com.cn/guide/"
    ]

    def parse(self, response):
        items= []
        # 所有大类的url 和 标题
        parentUrls = response.xpath('//div[@id=\"tab01\"]/div/h3/a/@href').extract()
        parentTitle = response.xpath("//div[@id=\"tab01\"]/div/h3/a/text()").extract()

        # 所有小类的ur 和 标题
        subUrls  = response.xpath('//div[@id=\"tab01\"]/div/ul/li/a/@href').extract()
        subTitle = response.xpath('//div[@id=\"tab01\"]/div/ul/li/a/text()').extract()

        #爬取所有大类
        for i in range(0, len(parentTitle)):
            # 指定大类目录的路径和目录名
            parentFilename = "./Data/" + parentTitle[i]

            #如果目录不存在，则创建目录
            if(not os.path.exists(parentFilename)):
                os.makedirs(parentFilename)

            # 爬取所有小类
            for j in range(0, len(subUrls)):
                item = SinaItem()

                # 保存大类的title和urls
                item['parentTitle'] = parentTitle[i]
                item['parentUrls'] = parentUrls[i]

                # 检查小类的url是否以同类别大类url开头，如果是返回True (sports.sina.com.cn 和 sports.sina.com.cn/nba)
                if_belong = subUrls[j].startswith(item['parentUrls'])

                # 如果属于本大类，将存储目录放在本大类目录下
                if(if_belong):
                    subFilename =parentFilename + '/'+ subTitle[j]
                    # 如果目录不存在，则创建目录
                    if(not os.path.exists(subFilename)):
                        os.makedirs(subFilename)

                    # 存储 小类url、title和filename字段数据
                    item['subUrls'] = subUrls[j]
                    item['subTitle'] =subTitle[j]
                    item['subFilename'] = subFilename

                    items.append(item)

        #发送每个小类url的Request请求，得到Response连同包含meta数据 一同交给回调函数 second_parse 方法处理
        for item in items:
            yield scrapy.Request( url = item['subUrls'], meta={'meta_1': item}, callback=self.second_parse)

    #对于返回的小类的url，再进行递归请求
    def second_parse(self, response):
        # 提取每次Response的meta数据
        meta_1= response.meta['meta_1']

        # 取出小类里所有子链接
        sonUrls = response.xpath('//a/@href').extract()

        items= []
        for i in range(0, len(sonUrls)):
            # 检查每个链接是否以大类url开头、以.shtml结尾，如果是返回True
            if_belong = sonUrls[i].endswith('.shtml') and sonUrls[i].startswith(meta_1['parentUrls'])

            # 如果属于本大类，获取字段值放在同一个item下便于传输
            if(if_belong):
                item = SinaItem()
                item['parentTitle'] =meta_1['parentTitle']
                item['parentUrls'] =meta_1['parentUrls']
                item['subUrls'] = meta_1['subUrls']
                item['subTitle'] = meta_1['subTitle']
                item['subFilename'] = meta_1['subFilename']
                item['sonUrls'] = sonUrls[i]
                items.append(item)

        #发送每个小类下子链接url的Request请求，得到Response后连同包含meta数据 一同交给回调函数 detail_parse 方法处理
        for item in items:
                yield scrapy.Request(url=item['sonUrls'], meta={'meta_2':item}, callback = self.detail_parse)

    # 数据解析方法，获取文章标题和内容
    def detail_parse(self, response):
        item = response.meta['meta_2']
        content = ""
        head = response.xpath('//h1[@id=\"main_title\"]/text()')
        content_list = response.xpath('//div[@id=\"artibody\"]/p/text()').extract()

        # 将p标签里的文本内容合并到一起
        for content_one in content_list:
            content += content_one

        item['head']= head
        item['content']= content

        yield item
        
# pipelines.py
from scrapy import signals
import sys
reload(sys)
sys.setdefaultencoding("utf-8")

class SinaPipeline(object):
    def process_item(self, item, spider):
        sonUrls = item['sonUrls']

        # 文件名为子链接url中间部分，并将 / 替换为 _，保存为 .txt格式
        filename = sonUrls[7:-6].replace('/','_')
        filename += ".txt"

        fp = open(item['subFilename']+'/'+filename, 'w')
        fp.write(item['content'])
        fp.close()

        return item
    
# settings.py
BOT_NAME = 'Sina'

SPIDER_MODULES = ['Sina.spiders']
NEWSPIDER_MODULE = 'Sina.spiders'

ITEM_PIPELINES = {
    'Sina.pipelines.SinaPipeline': 300,
}

LOG_LEVEL = 'DEBUG'

# 在项目根目录下新建main.py文件,用于调试
from scrapy import cmdline
cmdline.execute('scrapy crawl sina'.split())

# 执行程序
py2 main.py
```

### 159. 豆瓣电影 Pymongo

```python
# 爬取豆瓣电影top250 https://movie.douban.com/top250 的电影数据，并保存在MongoDB中。

# items.py
class DoubanspiderItem(scrapy.Item):
    # 电影标题
    title = scrapy.Field()
    # 电影评分
    score = scrapy.Field()
    # 电影信息
    content = scrapy.Field()
    # 简介
    info = scrapy.Field()

# spiders/douban.py
import scrapy
from doubanSpider.items import DoubanspiderItem


class DoubanSpider(scrapy.Spider):
    name = "douban"
    allowed_domains = ["movie.douban.com"]
    start = 0
    url = 'https://movie.douban.com/top250?start='
    end = '&filter='
    start_urls = [url + str(start) + end]

    def parse(self, response):

        item = DoubanspiderItem()

        movies = response.xpath("//div[@class=\'info\']")

        for each in movies:
            title = each.xpath('div[@class="hd"]/a/span[@class="title"]/text()').extract()
            content = each.xpath('div[@class="bd"]/p/text()').extract()
            score = each.xpath('div[@class="bd"]/div[@class="star"]/span[@class="rating_num"]/text()').extract()
            info = each.xpath('div[@class="bd"]/p[@class="quote"]/span/text()').extract()

            item['title'] = title[0]
            # 以;作为分隔，将content列表里所有元素合并成一个新的字符串
            item['content'] = ';'.join(content)
            item['score'] = score[0]
            item['info'] = info[0]
            # 提交item

            yield item

        if self.start <= 225:
            self.start += 25
            yield scrapy.Request(self.url + str(self.start) + self.end, callback=self.parse)
            
# pipelines.py
from scrapy.conf import settings
import pymongo

class DoubanspiderPipeline(object):
    def __init__(self):
        # 获取setting主机名、端口号和数据库名
        host = settings['MONGODB_HOST']
        port = settings['MONGODB_PORT']
        dbname = settings['MONGODB_DBNAME']

        # pymongo.MongoClient(host, port) 创建MongoDB链接
        client = pymongo.MongoClient(host=host,port=port)

        # 指向指定的数据库
        mdb = client[dbname]
        # 获取数据库里存放数据的表名
        self.post = mdb[settings['MONGODB_DOCNAME']]


    def process_item(self, item, spider):
        data = dict(item)
        # 向指定的表里添加数据
        self.post.insert(data)
        return item
    
# settings.py    
BOT_NAME = 'doubanSpider'

SPIDER_MODULES = ['doubanSpider.spiders']
NEWSPIDER_MODULE = 'doubanSpider.spiders'

ITEM_PIPELINES = {
        'doubanSpider.pipelines.DoubanspiderPipeline' : 300
        }

# Crawl responsibly by identifying yourself (and your website) on the user-agent
USER_AGENT = 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/48.0.2564.116 Safari/537.36'

# MONGODB 主机环回地址127.0.0.1
MONGODB_HOST = '127.0.0.1'
# 端口号，默认是27017
MONGODB_PORT = 27017
# 设置数据库名称
MONGODB_DBNAME = 'DouBan'
# 存放本次数据的表名称
MONGODB_DOCNAME = 'DouBanMovies'
```

### 160. 图片下载器

```python
# items.py
class CoserItem(scrapy.Item):
    url = scrapy.Field()
    name = scrapy.Field()
    info = scrapy.Field()
    image_urls = scrapy.Field()
    images = scrapy.Field()
    
# spiders/coser.py   
# -*- coding: utf-8 -*-
from scrapy.selector import Selector
import scrapy
from scrapy.contrib.loader import ItemLoader
from Cosplay.items import CoserItem


class CoserSpider(scrapy.Spider):
    name = "coser"
    allowed_domains = ["bcy.net"]
    start_urls = (
        'http://bcy.net/cn125101',
        'http://bcy.net/cn126487',
        'http://bcy.net/cn126173'
    )

    def parse(self, response):
        sel = Selector(response)

        for link in sel.xpath("//ul[@class='js-articles l-works']/li[@class='l-work--big']/article[@class='work work--second-created']/h2[@class='work__title']/a/@href").extract():
            link = 'http://bcy.net%s' % link
            request = scrapy.Request(link, callback=self.parse_item)
            yield request

    def parse_item(self, response):
        l = ItemLoader(item=CoserItem(), response=response)
        l.add_xpath('name', "//h1[@class='js-post-title']/text()")
        l.add_xpath('info', "//div[@class='post__info']/div[@class='post__type post__info-group']/span/text()")
        urls = l.get_xpath('//img[@class="detail_std detail_clickable"]/@src')
        urls = [url.replace('/w650', '') for url in urls]
        l.add_value('image_urls', urls)
        l.add_value('url', response.url)

        return l.load_item()
    
# pipelines.py
import requests
from Cosplay import settings
import os


class ImageDownloadPipeline(object):
    def process_item(self, item, spider):
        if 'image_urls' in item:
            images = []
            dir_path = '%s/%s' % (settings.IMAGES_STORE, spider.name)

            if not os.path.exists(dir_path):
                os.makedirs(dir_path)
            for image_url in item['image_urls']:
                us = image_url.split('/')[3:]
                image_file_name = '_'.join(us)
                file_path = '%s/%s' % (dir_path, image_file_name)
                images.append(file_path)
                if os.path.exists(file_path):
                    continue

                with open(file_path, 'wb') as handle:
                    response = requests.get(image_url, stream=True)
                    for block in response.iter_content(1024):
                        if not block:
                            break

                        handle.write(block)

            item['images'] = images
        return item
    
    
#  settings.py
ITEM_PIPELINES = {'Cosplay.pipelines.ImageDownloadPipeline': 1}

IMAGES_STORE = '../Images'

DOWNLOAD_DELAY = 0.25    # 250 ms of delay

# 在项目根目录下新建main.py文件,用于调试
from scrapy import cmdline
cmdline.execute('scrapy crawl coser'.split())

# 执行程序
py2 main.py
```

### 161. 模拟登陆策略

```python
# 注意：模拟登陆时，必须保证settings.py里的 COOKIES_ENABLED (Cookies中间件) 处于开启状态
COOKIES_ENABLED = True 或 # COOKIES_ENABLED = False

# 策略一：直接POST数据（比如需要登陆的账户信息)
# 只要是需要提供post数据的，就可以用这种方法。下面示例里post的数据是账户密码：
# -*- coding: utf-8 -*-
import scrapy


class Renren1Spider(scrapy.Spider):
    name = "renren1"
    allowed_domains = ["renren.com"]

    def start_requests(self):
        url = 'http://www.renren.com/PLogin.do'
        # FormRequest 是Scrapy发送POST请求的方法
        yield scrapy.FormRequest(
                url = url,
                formdata = {"email" : "mr_mao_hacker@163.com", "password" : "axxxxxxxe"},
                callback = self.parse_page)

    def parse_page(self, response):
        with open("mao2.html", "w") as filename:
            filename.write(response.body)
            
            
# 策略二：标准的模拟登陆步骤
# 正统模拟登录方法：
	# 1.首先发送登录页面的get请求，获取到页面里的登录必须的参数（比如说zhihu登陆界面的 _xsrf）
	# 2.然后和账户密码一起post到服务器，登录成功
# -*- coding: utf-8 -*-
import scrapy



class Renren2Spider(scrapy.Spider):
    name = "renren2"
    allowed_domains = ["renren.com"]
    start_urls = (
        "http://www.renren.com/PLogin.do",
    )

    # 处理start_urls里的登录url的响应内容，提取登陆需要的参数（如果需要的话)
    def parse(self, response):
        # 提取登陆需要的参数
        #_xsrf = response.xpath("//_xsrf").extract()[0]

        # 发送请求参数，并调用指定回调函数处理
        yield scrapy.FormRequest.from_response(
                response,
                formdata = {"email" : "mr_mao_hacker@163.com", "password" : "axxxxxxxe"},#, "_xsrf" = _xsrf},
                callback = self.parse_page
            )

    # 获取登录成功状态，访问需要登录后才能访问的页面
    def parse_page(self, response):
        url = "http://www.renren.com/422167102/profile"
        yield scrapy.Request(url, callback = self.parse_newpage)

    # 处理响应内容
    def parse_newpage(self, response):
        with open("xiao.html", "w") as filename:
            filename.write(response.body)
            
            
# 策略三：直接使用保存登陆状态的Cookie模拟登陆
# 如果实在没办法了，可以用这种方法模拟登录，虽然麻烦一点，但是成功率100%
# -*- coding: utf-8 -*-
import scrapy

class RenrenSpider(scrapy.Spider):
    name = "renren"
    allowed_domains = ["renren.com"]
    start_urls = (
        'http://www.renren.com/111111',
        'http://www.renren.com/222222',
        'http://www.renren.com/333333',
    )

    cookies = {
    "anonymid" : "ixrna3fysufnwv",
    "_r01_" : "1",
    "ap" : "327550029",
    "JSESSIONID" : "abciwg61A_RvtaRS3GjOv",
    "depovince" : "GW",
    "springskin" : "set",
    "jebe_key" : "f6fb270b-d06d-42e6-8b53-e67c3156aa7e%7Cc13c37f53bca9e1e7132d4b58ce00fa3%7C1484060607478%7C1%7C1486198628950",
    "t" : "691808127750a83d33704a565d8340ae9",
    "societyguester" : "691808127750a83d33704a565d8340ae9",
    "id" : "327550029",
    "xnsid" : "f42b25cf",
    "loginfrom" : "syshome"
    }

    # 可以重写Spider类的start_requests方法，附带Cookie值，发送POST请求
    def start_requests(self):
        for url in self.start_urls:
            yield scrapy.FormRequest(url, cookies = self.cookies, callback = self.parse_page)

    # 处理响应内容
    def parse_page(self, response):
        print "===========" + response.url
        with open("deng.html", "w") as filename:
            filename.write(response.body)
```

## 九 Scrapy-redis分布式组件

```python
# Scrapy 和 scrapy-redis的区别:
# Scrapy 是一个通用的爬虫框架，但是不支持分布式，Scrapy-redis是为了更方便地实现Scrapy分布式爬取，而提供了一些以redis为基础的组件(仅有组件)。
# 安装:
pip install scrapy-redis
# Scrapy-redis提供了下面四种组件（components）：(四种组件意味着这四个模块都要做相应的修改)
	# Scheduler
	# Duplication Filter
	# Item Pipeline
	# Base Spider

# scrapy-redis在scrapy的架构上增加了redis，基于redis的特性拓展了如下组件：

# 1. Scheduler
# Scrapy改造了python本来的collection.deque(双向队列)形成了自己的Scrapy queue(https://github.com/scrapy/queuelib/blob/master/queuelib/queue.py))，但是Scrapy多个spider不能共享待爬取队列Scrapy queue， 即Scrapy本身不支持爬虫分布式，scrapy-redis 的解决是把这个Scrapy queue换成redis数据库（也是指redis队列），从同一个redis-server存放要爬取的request，便能让多个spider去同一个数据库里读取。

# Scrapy中跟“待爬队列”直接相关的就是调度器Scheduler，它负责对新的request进行入列操作（加入Scrapy queue），取出下一个要爬取的request（从Scrapy queue中取出）等操作。它把待爬队列按照优先级建立了一个字典结构，比如：
{
        优先级0 : 队列0
        优先级1 : 队列1
        优先级2 : 队列2
    }
# 然后根据request中的优先级，来决定该入哪个队列，出列时则按优先级较小的优先出列。为了管理这个比较高级的队列字典，Scheduler需要提供一系列的方法。但是原来的Scheduler已经无法使用，所以使用Scrapy-redis的scheduler组件。

# 2. Duplication Filter
# Scrapy中用集合实现这个request去重功能，Scrapy中把已经发送的request指纹放入到一个集合中，把下一个request的指纹拿到集合中比对，如果该指纹存在于集合中，说明这个request发送过了，如果没有则继续操作。这个核心的判重功能是这样实现的：
def request_seen(self, request):
        # self.request_figerprints就是一个指纹集合  
        fp = self.request_fingerprint(request)

        # 这就是判重的核心操作  
        if fp in self.fingerprints:
            return True
        self.fingerprints.add(fp)
        if self.file:
            self.file.write(fp + os.linesep)
# 在scrapy-redis中去重是由Duplication Filter组件来实现的，它通过redis的set 不重复的特性，巧妙的实现了Duplication Filter去重。scrapy-redis调度器从引擎接受request，将request的指纹存⼊redis的set检查是否重复，并将不重复的request push写⼊redis的 request queue。
# 引擎请求request(Spider发出的）时，调度器从redis的request queue队列⾥里根据优先级pop 出⼀个request 返回给引擎，引擎将此request发给spider处理。

# 3. Item Pipeline
# 引擎将(Spider返回的)爬取到的Item给Item Pipeline，scrapy-redis 的Item Pipeline将爬取到的 Item 存⼊redis的 items queue。

# 修改过Item Pipeline可以很方便的根据 key 从 items queue 提取item，从⽽实现 items processes集群。

# 4. Base Spider
# 不在使用scrapy原有的Spider类，重写的RedisSpider继承了Spider和RedisMixin这两个类，RedisMixin是用来从redis读取url的类。

# 当我们生成一个Spider继承RedisSpider时，调用setup_redis函数，这个函数会去连接redis数据库，然后会设置signals(信号)：
	# 一个是当spider空闲时候的signal，会调用spider_idle函数，这个函数调用schedule_next_request函数，保证spider是一直活着的状态，并且抛出DontCloseSpider异常。
	# 一个是当抓到一个item时的signal，会调用item_scraped函数，这个函数会调用schedule_next_request函数，获取下一个request。
```

### 162. 源码分析之Connection

```python
# 官方站点：https://github.com/rolando/scrapy-redis

# scrapy-redis的官方文档写的比较简洁，没有提及其运行原理，所以如果想全面的理解分布式爬虫的运行原理，还是得看scrapy-redis的源代码才行。

# scrapy-redis工程的主体还是是redis和scrapy两个库，工程本身实现的东西不是很多，这个工程就像胶水一样，把这两个插件粘结了起来。下面我们来看看，scrapy-redis的每一个源代码文件都实现了什么功能，最后如何实现分布式的爬虫系统：

# connection.py https://github.com/rmax/scrapy-redis/blob/master/src/scrapy_redis/connection.py
# 负责根据setting中配置实例化redis连接。被dupefilter和scheduler调用，总之涉及到redis存取的都要使用到这个模块。
# 这里引入了redis模块，这个是redis-python库的接口，用于通过python访问redis数据库，
# 这个文件主要是实现连接redis数据库的功能，这些连接接口在其他文件中经常被用到

import redis
import six

from scrapy.utils.misc import load_object

DEFAULT_REDIS_CLS = redis.StrictRedis

# 可以在settings文件中配置套接字的超时时间、等待时间等
# Sane connection defaults.
DEFAULT_PARAMS = {
    'socket_timeout': 30,
    'socket_connect_timeout': 30,
    'retry_on_timeout': True,
}

# 要想连接到redis数据库，和其他数据库差不多，需要一个ip地址、端口号、用户名密码（可选）和一个整形的数据库编号
# Shortcut maps 'setting name' -> 'parmater name'.
SETTINGS_PARAMS_MAP = {
    'REDIS_URL': 'url',
    'REDIS_HOST': 'host',
    'REDIS_PORT': 'port',
}


def get_redis_from_settings(settings):
    """Returns a redis client instance from given Scrapy settings object.
    This function uses ``get_client`` to instantiate the client and uses
    ``DEFAULT_PARAMS`` global as defaults values for the parameters. You can
    override them using the ``REDIS_PARAMS`` setting.
    Parameters
    ----------
    settings : Settings
        A scrapy settings object. See the supported settings below.
    Returns
    -------
    server
        Redis client instance.
    Other Parameters
    ----------------
    REDIS_URL : str, optional
        Server connection URL.
    REDIS_HOST : str, optional
        Server host.
    REDIS_PORT : str, optional
        Server port.
    REDIS_PARAMS : dict, optional
        Additional client parameters.
    """
    params = DEFAULT_PARAMS.copy()
    params.update(settings.getdict('REDIS_PARAMS'))
    # XXX: Deprecate REDIS_* settings.
    for source, dest in SETTINGS_PARAMS_MAP.items():
        val = settings.get(source)
        if val:
            params[dest] = val

    # Allow ``redis_cls`` to be a path to a class.
    if isinstance(params.get('redis_cls'), six.string_types):
        params['redis_cls'] = load_object(params['redis_cls'])

    # 返回的是redis库的Redis对象，可以直接用来进行数据操作的对象
    return get_redis(**params)


# Backwards compatible alias.
from_settings = get_redis_from_settings


def get_redis(**kwargs):
    """Returns a redis client instance.
    Parameters
    ----------
    redis_cls : class, optional
        Defaults to ``redis.StrictRedis``.
    url : str, optional
        If given, ``redis_cls.from_url`` is used to instantiate the class.
    **kwargs
        Extra parameters to be passed to the ``redis_cls`` class.
    Returns
    -------
    server
        Redis client instance.
    """
    redis_cls = kwargs.pop('redis_cls', DEFAULT_REDIS_CLS)
    url = kwargs.pop('url', None)


    if url:
        return redis_cls.from_url(url, **kwargs)
    else:
        return redis_cls(**kwargs)
```

### 163. 源码分析之Dupefilter

```python
# 负责执行requst的去重，实现的很有技巧性，使用redis的set数据结构。但是注意scheduler并不使用其中用于在这个模块中实现的dupefilter键做request的调度，而是使用queue.py模块中实现的queue。

# 当request不重复时，将其存入到queue中，调度时将其弹出。
import logging
import time

from scrapy.dupefilters import BaseDupeFilter
from scrapy.utils.request import request_fingerprint

from .connection import get_redis_from_settings


DEFAULT_DUPEFILTER_KEY = "dupefilter:%(timestamp)s"

logger = logging.getLogger(__name__)


# TODO: Rename class to RedisDupeFilter.
class RFPDupeFilter(BaseDupeFilter):
    """Redis-based request duplicates filter.
    This class can also be used with default Scrapy's scheduler.
    """

    logger = logger

    def __init__(self, server, key, debug=False):
        """Initialize the duplicates filter.
        Parameters
        ----------
        server : redis.StrictRedis
            The redis server instance.
        key : str
            Redis key Where to store fingerprints.
        debug : bool, optional
            Whether to log filtered requests.
        """
        self.server = server
        self.key = key
        self.debug = debug
        self.logdupes = True

    @classmethod
    def from_settings(cls, settings):
        """Returns an instance from given settings.
        This uses by default the key ``dupefilter:<timestamp>``. When using the
        ``scrapy_redis.scheduler.Scheduler`` class, this method is not used as
        it needs to pass the spider name in the key.
        Parameters
        ----------
        settings : scrapy.settings.Settings
        Returns
        -------
        RFPDupeFilter
            A RFPDupeFilter instance.
        """
        server = get_redis_from_settings(settings)
        # XXX: This creates one-time key. needed to support to use this
        # class as standalone dupefilter with scrapy's default scheduler
        # if scrapy passes spider on open() method this wouldn't be needed
        # TODO: Use SCRAPY_JOB env as default and fallback to timestamp.
        key = DEFAULT_DUPEFILTER_KEY % {'timestamp': int(time.time())}
        debug = settings.getbool('DUPEFILTER_DEBUG')
        return cls(server, key=key, debug=debug)

    @classmethod
    def from_crawler(cls, crawler):
        """Returns instance from crawler.
        Parameters
        ----------
        crawler : scrapy.crawler.Crawler
        Returns
        -------
        RFPDupeFilter
            Instance of RFPDupeFilter.
        """
        return cls.from_settings(crawler.settings)

    def request_seen(self, request):
        """Returns True if request was already seen.
        Parameters
        ----------
        request : scrapy.http.Request
        Returns
        -------
        bool
        """
        fp = self.request_fingerprint(request)
        # This returns the number of values added, zero if already exists.
        added = self.server.sadd(self.key, fp)
        return added == 0

    def request_fingerprint(self, request):
        """Returns a fingerprint for a given request.
        Parameters
        ----------
        request : scrapy.http.Request
        Returns
        -------
        str
        """
        return request_fingerprint(request)

    def close(self, reason=''):
        """Delete data on close. Called by Scrapy's scheduler.
        Parameters
        ----------
        reason : str, optional
        """
        self.clear()

    def clear(self):
        """Clears fingerprints data."""
        self.server.delete(self.key)

    def log(self, request, spider):
        """Logs given request.
        Parameters
        ----------
        request : scrapy.http.Request
        spider : scrapy.spiders.Spider
        """
        if self.debug:
            msg = "Filtered duplicate request: %(request)s"
            self.logger.debug(msg, {'request': request}, extra={'spider': spider})
        elif self.logdupes:
            msg = ("Filtered duplicate request %(request)s"
                   " - no more duplicates will be shown"
                   " (see DUPEFILTER_DEBUG to show all duplicates)")
            msg = "Filtered duplicate request: %(request)s"
            self.logger.debug(msg, {'request': request}, extra={'spider': spider})
            self.logdupes = False
            
# 这个文件看起来比较复杂，重写了scrapy本身已经实现的request判重功能。因为本身scrapy单机跑的话，只需要读取内存中的request队列或者持久化的request队列（scrapy默认的持久化似乎是json格式的文件，不是数据库）就能判断这次要发出的request url是否已经请求过或者正在调度（本地读就行了）。而分布式跑的话，就需要各个主机上的scheduler都连接同一个数据库的同一个request池来判断这次的请求是否是重复的了。

# 在这个文件中，通过继承BaseDupeFilter重写他的方法，实现了基于redis的判重。根据源代码来看，scrapy-redis使用了scrapy本身的一个fingerprint接request_fingerprint，这个接口很有趣，根据scrapy文档所说，他通过hash来判断两个url是否相同（相同的url会生成相同的hash结果），但是当两个url的地址相同，get型参数相同但是顺序不同时，也会生成相同的hash结果（这个真的比较神奇。。。）所以scrapy-redis依旧使用url的fingerprint来判断request请求是否已经出现过。

# 这个类通过连接redis，使用一个key来向redis的一个set中插入fingerprint（这个key对于同一种spider是相同的，redis是一个key-value的数据库，如果key是相同的，访问到的值就是相同的，这里使用spider名字+DupeFilter的key就是为了在不同主机上的不同爬虫实例，只要属于同一种spider，就会访问到同一个set，而这个set就是他们的url判重池），如果返回值为0，说明该set中该fingerprint已经存在（因为集合是没有重复值的），则返回False，如果返回值为1，说明添加了一个fingerprint到set中，则说明这个request没有重复，于是返回True，还顺便把新fingerprint加入到数据库中了。 DupeFilter判重会在scheduler类中用到，每一个request在进入调度之前都要进行判重，如果重复就不需要参加调度，直接舍弃就好了，不然就是白白浪费资源。            
```

### 164. 源码分析之Picklecompat

```python
"""A pickle wrapper module with protocol=-1 by default."""

try:
    import cPickle as pickle  # PY2
except ImportError:
    import pickle


def loads(s):
    return pickle.loads(s)


def dumps(obj):
    return pickle.dumps(obj, protocol=-1)

# 这里实现了loads和dumps两个函数，其实就是实现了一个序列化器。

# 因为redis数据库不能存储复杂对象（key部分只能是字符串，value部分只能是字符串，字符串列表，字符串集合和hash），所以我们存啥都要先串行化成文本才行。

# 这里使用的就是python的pickle模块，一个兼容py2和py3的串行化工具。这个serializer主要用于一会的scheduler存reuqest对象。
```

### 165. 源码分析之Pipelines

```python
# 这是是用来实现分布式处理的作用。它将Item存储在redis中以实现分布式处理。由于在这里需要读取配置，所以就用到了from_crawler()函数。
from scrapy.utils.misc import load_object
from scrapy.utils.serialize import ScrapyJSONEncoder
from twisted.internet.threads import deferToThread

from . import connection


default_serialize = ScrapyJSONEncoder().encode


class RedisPipeline(object):
    """Pushes serialized item into a redis list/queue"""

    def __init__(self, server,
                 key='%(spider)s:items',
                 serialize_func=default_serialize):
        self.server = server
        self.key = key
        self.serialize = serialize_func

    @classmethod
    def from_settings(cls, settings):
        params = {
            'server': connection.from_settings(settings),
        }
        if settings.get('REDIS_ITEMS_KEY'):
            params['key'] = settings['REDIS_ITEMS_KEY']
        if settings.get('REDIS_ITEMS_SERIALIZER'):
            params['serialize_func'] = load_object(
                settings['REDIS_ITEMS_SERIALIZER']
            )

        return cls(**params)

    @classmethod
    def from_crawler(cls, crawler):
        return cls.from_settings(crawler.settings)

    def process_item(self, item, spider):
        return deferToThread(self._process_item, item, spider)

    def _process_item(self, item, spider):
        key = self.item_key(item, spider)
        data = self.serialize(item)
        self.server.rpush(key, data)
        return item

    def item_key(self, item, spider):
        """Returns redis key based on given spider.
        Override this function to use a different key depending on the item
        and/or spider.
        """
        return self.key % {'spider': spider.name}
    
# pipelines文件实现了一个item pipieline类，和scrapy的item pipeline是同一个对象，通过从settings中拿到我们配置的REDIS_ITEMS_KEY作为key，把item串行化之后存入redis数据库对应的value中（这个value可以看出出是个list，我们的每个item是这个list中的一个结点），这个pipeline把提取出的item存起来，主要是为了方便我们延后处理数据。

```

### 166. 源码分析之Queue

```python
# 该文件实现了几个容器类，可以看这些容器和redis交互频繁，同时使用了我们上边picklecompat中定义的序列化器。这个文件实现的几个容器大体相同，只不过一个是队列，一个是栈，一个是优先级队列，这三个容器到时候会被scheduler对象实例化，来实现request的调度。比如我们使用SpiderQueue最为调度队列的类型，到时候request的调度方法就是先进先出，而实用SpiderStack就是先进后出了。

# 从SpiderQueue的实现看出来，他的push函数就和其他容器的一样，只不过push进去的request请求先被scrapy的接口request_to_dict变成了一个dict对象（因为request对象实在是比较复杂，有方法有属性不好串行化），之后使用picklecompat中的serializer串行化为字符串，然后使用一个特定的key存入redis中（该key在同一种spider中是相同的）。而调用pop时，其实就是从redis用那个特定的key去读其值（一个list），从list中读取最早进去的那个，于是就先进先出了。 这些容器类都会作为scheduler调度request的容器，scheduler在每个主机上都会实例化一个，并且和spider一一对应，所以分布式运行时会有一个spider的多个实例和一个scheduler的多个实例存在于不同的主机上，但是，因为scheduler都是用相同的容器，而这些容器都连接同一个redis服务器，又都使用spider名加queue来作为key读写数据，所以不同主机上的不同爬虫实例公用一个request调度池，实现了分布式爬虫之间的统一调度。

from scrapy.utils.reqser import request_to_dict, request_from_dict

from . import picklecompat


class Base(object):
    """Per-spider queue/stack base class"""

    def __init__(self, server, spider, key, serializer=None):
        """Initialize per-spider redis queue.
        Parameters:
            server -- redis connection
            spider -- spider instance
            key -- key for this queue (e.g. "%(spider)s:queue")
        """
        if serializer is None:
            # Backward compatibility.
            # TODO: deprecate pickle.
            serializer = picklecompat
        if not hasattr(serializer, 'loads'):
            raise TypeError("serializer does not implement 'loads' function: %r"
                            % serializer)
        if not hasattr(serializer, 'dumps'):
            raise TypeError("serializer '%s' does not implement 'dumps' function: %r"
                            % serializer)

        self.server = server
        self.spider = spider
        self.key = key % {'spider': spider.name}
        self.serializer = serializer

    def _encode_request(self, request):
        """Encode a request object"""
        obj = request_to_dict(request, self.spider)
        return self.serializer.dumps(obj)

    def _decode_request(self, encoded_request):
        """Decode an request previously encoded"""
        obj = self.serializer.loads(encoded_request)
        return request_from_dict(obj, self.spider)

    def __len__(self):
        """Return the length of the queue"""
        raise NotImplementedError

    def push(self, request):
        """Push a request"""
        raise NotImplementedError

    def pop(self, timeout=0):
        """Pop a request"""
        raise NotImplementedError

    def clear(self):
        """Clear queue/stack"""
        self.server.delete(self.key)


class SpiderQueue(Base):
    """Per-spider FIFO queue"""

    def __len__(self):
        """Return the length of the queue"""
        return self.server.llen(self.key)

    def push(self, request):
        """Push a request"""
        self.server.lpush(self.key, self._encode_request(request))

    def pop(self, timeout=0):
        """Pop a request"""
        if timeout > 0:
            data = self.server.brpop(self.key, timeout)
            if isinstance(data, tuple):
                data = data[1]
        else:
            data = self.server.rpop(self.key)
        if data:
            return self._decode_request(data)


class SpiderPriorityQueue(Base):
    """Per-spider priority queue abstraction using redis' sorted set"""

    def __len__(self):
        """Return the length of the queue"""
        return self.server.zcard(self.key)

    def push(self, request):
        """Push a request"""
        data = self._encode_request(request)
        score = -request.priority
        # We don't use zadd method as the order of arguments change depending on
        # whether the class is Redis or StrictRedis, and the option of using
        # kwargs only accepts strings, not bytes.
        self.server.execute_command('ZADD', self.key, score, data)

    def pop(self, timeout=0):
        """
        Pop a request
        timeout not support in this queue class
        """
        # use atomic range/remove using multi/exec
        pipe = self.server.pipeline()
        pipe.multi()
        pipe.zrange(self.key, 0, 0).zremrangebyrank(self.key, 0, 0)
        results, count = pipe.execute()
        if results:
            return self._decode_request(results[0])


class SpiderStack(Base):
    """Per-spider stack"""

    def __len__(self):
        """Return the length of the stack"""
        return self.server.llen(self.key)

    def push(self, request):
        """Push a request"""
        self.server.lpush(self.key, self._encode_request(request))

    def pop(self, timeout=0):
        """Pop a request"""
        if timeout > 0:
            data = self.server.blpop(self.key, timeout)
            if isinstance(data, tuple):
                data = data[1]
        else:
            data = self.server.lpop(self.key)

        if data:
            return self._decode_request(data)


__all__ = ['SpiderQueue', 'SpiderPriorityQueue', 'SpiderStack']

```

### 167. 源码分析之Scheduler

```python
# 此扩展是对scrapy中自带的scheduler的替代（在settings的SCHEDULER变量中指出），正是利用此扩展实现crawler的分布式调度。其利用的数据结构来自于queue中实现的数据结构。

# scrapy-redis所实现的两种分布式：爬虫分布式以及item处理分布式就是由模块scheduler和模块pipelines实现。上述其它模块作为为二者辅助的功能模块

import importlib
import six

from scrapy.utils.misc import load_object

from . import connection


# TODO: add SCRAPY_JOB support.
class Scheduler(object):
    """Redis-based scheduler"""

    def __init__(self, server,
                 persist=False,
                 flush_on_start=False,
                 queue_key='%(spider)s:requests',
                 queue_cls='scrapy_redis.queue.SpiderPriorityQueue',
                 dupefilter_key='%(spider)s:dupefilter',
                 dupefilter_cls='scrapy_redis.dupefilter.RFPDupeFilter',
                 idle_before_close=0,
                 serializer=None):
        """Initialize scheduler.
        Parameters
        ----------
        server : Redis
            The redis server instance.
        persist : bool
            Whether to flush requests when closing. Default is False.
        flush_on_start : bool
            Whether to flush requests on start. Default is False.
        queue_key : str
            Requests queue key.
        queue_cls : str
            Importable path to the queue class.
        dupefilter_key : str
            Duplicates filter key.
        dupefilter_cls : str
            Importable path to the dupefilter class.
        idle_before_close : int
            Timeout before giving up.
        """
        if idle_before_close < 0:
            raise TypeError("idle_before_close cannot be negative")

        self.server = server
        self.persist = persist
        self.flush_on_start = flush_on_start
        self.queue_key = queue_key
        self.queue_cls = queue_cls
        self.dupefilter_cls = dupefilter_cls
        self.dupefilter_key = dupefilter_key
        self.idle_before_close = idle_before_close
        self.serializer = serializer
        self.stats = None

    def __len__(self):
        return len(self.queue)

    @classmethod
    def from_settings(cls, settings):
        kwargs = {
            'persist': settings.getbool('SCHEDULER_PERSIST'),
            'flush_on_start': settings.getbool('SCHEDULER_FLUSH_ON_START'),
            'idle_before_close': settings.getint('SCHEDULER_IDLE_BEFORE_CLOSE'),
        }

        # If these values are missing, it means we want to use the defaults.
        optional = {
            # TODO: Use custom prefixes for this settings to note that are
            # specific to scrapy-redis.
            'queue_key': 'SCHEDULER_QUEUE_KEY',
            'queue_cls': 'SCHEDULER_QUEUE_CLASS',
            'dupefilter_key': 'SCHEDULER_DUPEFILTER_KEY',
            # We use the default setting name to keep compatibility.
            'dupefilter_cls': 'DUPEFILTER_CLASS',
            'serializer': 'SCHEDULER_SERIALIZER',
        }
        for name, setting_name in optional.items():
            val = settings.get(setting_name)
            if val:
                kwargs[name] = val

        # Support serializer as a path to a module.
        if isinstance(kwargs.get('serializer'), six.string_types):
            kwargs['serializer'] = importlib.import_module(kwargs['serializer'])

        server = connection.from_settings(settings)
        # Ensure the connection is working.
        server.ping()

        return cls(server=server, **kwargs)

    @classmethod
    def from_crawler(cls, crawler):
        instance = cls.from_settings(crawler.settings)
        # FIXME: for now, stats are only supported from this constructor
        instance.stats = crawler.stats
        return instance

    def open(self, spider):
        self.spider = spider

        try:
            self.queue = load_object(self.queue_cls)(
                server=self.server,
                spider=spider,
                key=self.queue_key % {'spider': spider.name},
                serializer=self.serializer,
            )
        except TypeError as e:
            raise ValueError("Failed to instantiate queue class '%s': %s",
                             self.queue_cls, e)

        try:
            self.df = load_object(self.dupefilter_cls)(
                server=self.server,
                key=self.dupefilter_key % {'spider': spider.name},
                debug=spider.settings.getbool('DUPEFILTER_DEBUG'),
            )
        except TypeError as e:
            raise ValueError("Failed to instantiate dupefilter class '%s': %s",
                             self.dupefilter_cls, e)

        if self.flush_on_start:
            self.flush()
        # notice if there are requests already in the queue to resume the crawl
        if len(self.queue):
            spider.log("Resuming crawl (%d requests scheduled)" % len(self.queue))

    def close(self, reason):
        if not self.persist:
            self.flush()

    def flush(self):
        self.df.clear()
        self.queue.clear()

    def enqueue_request(self, request):
        if not request.dont_filter and self.df.request_seen(request):
            self.df.log(request, self.spider)
            return False
        if self.stats:
            self.stats.inc_value('scheduler/enqueued/redis', spider=self.spider)
        self.queue.push(request)
        return True

    def next_request(self):
        block_pop_timeout = self.idle_before_close
        request = self.queue.pop(block_pop_timeout)
        if request and self.stats:
            self.stats.inc_value('scheduler/dequeued/redis', spider=self.spider)
        return request

    def has_pending_requests(self):
        return len(self) > 0
    
 # 这个文件重写了scheduler类，用来代替scrapy.core.scheduler的原有调度器。其实对原有调度器的逻辑没有很大的改变，主要是使用了redis作为数据存储的媒介，以达到各个爬虫之间的统一调度。 scheduler负责调度各个spider的request请求，scheduler初始化时，通过settings文件读取queue和dupefilters的类型（一般就用上边默认的），配置queue和dupefilters使用的key（一般就是spider name加上queue或者dupefilters，这样对于同一种spider的不同实例，就会使用相同的数据块了）。每当一个request要被调度时，enqueue_request被调用，scheduler使用dupefilters来判断这个url是否重复，如果不重复，就添加到queue的容器中（先进先出，先进后出和优先级都可以，可以在settings中配置）。当调度完成时，next_request被调用，scheduler就通过queue容器的接口，取出一个request，把他发送给相应的spider，让spider进行爬取工作。   
```

### 168. 源码分析之Spider

```python
# 设计的这个spider从redis中读取要爬的url,然后执行爬取，若爬取过程中返回更多的url，那么继续进行直至所有的request完成。之后继续从redis中读取url，循环这个过程。

# 分析：在这个spider中通过connect signals.spider_idle信号实现对crawler状态的监视。当idle时，返回新的make_requests_from_url(url)给引擎，进而交给调度器调度。

from scrapy import signals
from scrapy.exceptions import DontCloseSpider
from scrapy.spiders import Spider, CrawlSpider

from . import connection


# Default batch size matches default concurrent requests setting.
DEFAULT_START_URLS_BATCH_SIZE = 16
DEFAULT_START_URLS_KEY = '%(name)s:start_urls'


class RedisMixin(object):
    """Mixin class to implement reading urls from a redis queue."""
    # Per spider redis key, default to DEFAULT_START_URLS_KEY.
    redis_key = None
    # Fetch this amount of start urls when idle. Default to DEFAULT_START_URLS_BATCH_SIZE.
    redis_batch_size = None
    # Redis client instance.
    server = None

    def start_requests(self):
        """Returns a batch of start requests from redis."""
        return self.next_requests()

    def setup_redis(self, crawler=None):
        """Setup redis connection and idle signal.
        This should be called after the spider has set its crawler object.
        """
        if self.server is not None:
            return

        if crawler is None:
            # We allow optional crawler argument to keep backwards
            # compatibility.
            # XXX: Raise a deprecation warning.
            crawler = getattr(self, 'crawler', None)

        if crawler is None:
            raise ValueError("crawler is required")

        settings = crawler.settings

        if self.redis_key is None:
            self.redis_key = settings.get(
                'REDIS_START_URLS_KEY', DEFAULT_START_URLS_KEY,
            )

        self.redis_key = self.redis_key % {'name': self.name}

        if not self.redis_key.strip():
            raise ValueError("redis_key must not be empty")

        if self.redis_batch_size is None:
            self.redis_batch_size = settings.getint(
                'REDIS_START_URLS_BATCH_SIZE', DEFAULT_START_URLS_BATCH_SIZE,
            )

        try:
            self.redis_batch_size = int(self.redis_batch_size)
        except (TypeError, ValueError):
            raise ValueError("redis_batch_size must be an integer")

        self.logger.info("Reading start URLs from redis key '%(redis_key)s' "
                         "(batch size: %(redis_batch_size)s)", self.__dict__)

        self.server = connection.from_settings(crawler.settings)
        # The idle signal is called when the spider has no requests left,
        # that's when we will schedule new requests from redis queue
        crawler.signals.connect(self.spider_idle, signal=signals.spider_idle)

    def next_requests(self):
        """Returns a request to be scheduled or none."""
        use_set = self.settings.getbool('REDIS_START_URLS_AS_SET')
        fetch_one = self.server.spop if use_set else self.server.lpop
        # XXX: Do we need to use a timeout here?
        found = 0
        while found < self.redis_batch_size:
            data = fetch_one(self.redis_key)
            if not data:
                # Queue empty.
                break
            req = self.make_request_from_data(data)
            if req:
                yield req
                found += 1
            else:
                self.logger.debug("Request not made from data: %r", data)

        if found:
            self.logger.debug("Read %s requests from '%s'", found, self.redis_key)

    def make_request_from_data(self, data):
        # By default, data is an URL.
        if '://' in data:
            return self.make_requests_from_url(data)
        else:
            self.logger.error("Unexpected URL from '%s': %r", self.redis_key, data)

    def schedule_next_requests(self):
        """Schedules a request if available"""
        for req in self.next_requests():
            self.crawler.engine.crawl(req, spider=self)

    def spider_idle(self):
        """Schedules a request if available, otherwise waits."""
        # XXX: Handle a sentinel to close the spider.
        self.schedule_next_requests()
        raise DontCloseSpider


class RedisSpider(RedisMixin, Spider):
    """Spider that reads urls from redis queue when idle."""

    @classmethod
    def from_crawler(self, crawler, *args, **kwargs):
        obj = super(RedisSpider, self).from_crawler(crawler, *args, **kwargs)
        obj.setup_redis(crawler)
        return obj


class RedisCrawlSpider(RedisMixin, CrawlSpider):
    """Spider that reads urls from redis queue when idle."""

    @classmethod
    def from_crawler(self, crawler, *args, **kwargs):
        obj = super(RedisCrawlSpider, self).from_crawler(crawler, *args, **kwargs)
        obj.setup_redis(crawler)
        return obj
    
# spider的改动也不是很大，主要是通过connect接口，给spider绑定了spider_idle信号，spider初始化时，通过setup_redis函数初始化好和redis的连接，之后通过next_requests函数从redis中取出strat url，使用的key是settings中REDIS_START_URLS_AS_SET定义的（注意了这里的初始化url池和我们上边的queue的url池不是一个东西，queue的池是用于调度的，初始化url池是存放入口url的，他们都存在redis中，但是使用不同的key来区分，就当成是不同的表吧），spider使用少量的start url，可以发展出很多新的url，这些url会进入scheduler进行判重和调度。直到spider跑到调度池内没有url的时候，会触发spider_idle信号，从而触发spider的next_requests函数，再次从redis的start url池中读取一些url。
```

### 169. 总结

```python
# scrapy-redis的总体思路：这个工程通过重写scheduler和spider类，实现了调度、spider启动和redis的交互。实现新的dupefilter和queue类，达到了判重和调度容器和redis的交互，因为每个主机上的爬虫进程都访问同一个redis数据库，所以调度和判重都统一进行统一管理，达到了分布式爬虫的目的。 当spider被初始化时，同时会初始化一个对应的scheduler对象，这个调度器对象通过读取settings，配置好自己的调度容器queue和判重工具dupefilter。每当一个spider产出一个request的时候，scrapy内核会把这个reuqest递交给这个spider对应的scheduler对象进行调度，scheduler对象通过访问redis对request进行判重，如果不重复就把他添加进redis中的调度池。当调度条件满足时，scheduler对象就从redis的调度池中取出一个request发送给spider，让他爬取。当spider爬取的所有暂时可用url之后，scheduler发现这个spider对应的redis的调度池空了，于是触发信号spider_idle，spider收到这个信号之后，直接连接redis读取strart url池，拿去新的一批url入口，然后再次重复上边的工作。
```

## 十  Scrapy-redis 案例

### 170. 源码说明 

```python
# 使用scrapy-redis的example修改
# 先从github上拿到scrapy-redis的示例，然后将里面的example-project目录移到指定的地址：

# clone github scrapy-redis源码文件
git clone https://github.com/rolando/scrapy-redis.git

# 直接拿官方的项目范例，改名为自己的项目用（针对懒癌患者)
mv scrapy-redis/example-project ~/scrapyredis-project

scrapy-redis 源码中有自带一个example-project项目，这个项目包含3个spider，分别是dmoz, myspider_redis，mycrawler_redis


# 一、dmoz (class DmozSpider(CrawlSpider))
# 这个爬虫继承的是CrawlSpider，它是用来说明Redis的持续性，当我们第一次运行dmoz爬虫，然后Ctrl + C停掉之后，再运行dmoz爬虫，之前的爬取记录是保留在Redis里的。

# 分析起来，其实这就是一个 scrapy-redis 版 CrawlSpider 类，需要设置Rule规则，以及callback不能写parse()方法。

# 执行方式：scrapy crawl dmoz
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule


class DmozSpider(CrawlSpider):
    """Follow categories and extract links."""
    name = 'dmoz'
    allowed_domains = ['dmoz.org']
    start_urls = ['http://www.dmoz.org/']

    rules = [
        Rule(LinkExtractor(
            restrict_css=('.top-cat', '.sub-cat', '.cat-item')
        ), callback='parse_directory', follow=True),
    ]

    def parse_directory(self, response):
        for div in response.css('.title-and-desc'):
            yield {
                'name': div.css('.site-title::text').extract_first(),
                'description': div.css('.site-descr::text').extract_first().strip(),
                'link': div.css('a::attr(href)').extract_first(),
            }
            
# 二、myspider_redis (class MySpider(RedisSpider))
# 这个爬虫继承了RedisSpider， 它能够支持分布式的抓取，采用的是basic spider，需要写parse函数。

# 其次就是不再有start_urls了，取而代之的是redis_key，scrapy-redis将key从Redis里pop出来，成为请求的url地址。
from scrapy_redis.spiders import RedisSpider


class MySpider(RedisSpider):
    """Spider that reads urls from redis queue (myspider:start_urls)."""
    name = 'myspider_redis'

    # 注意redis-key的格式：
    redis_key = 'myspider:start_urls'

    # 可选：等效于allowd_domains()，__init__方法按规定格式写，使用时只需要修改super()里的类名参数即可
    def __init__(self, *args, **kwargs):
        # Dynamically define the allowed domains list.
        domain = kwargs.pop('domain', '')
        self.allowed_domains = filter(None, domain.split(','))

        # 修改这里的类名为当前类名
        super(MySpider, self).__init__(*args, **kwargs)

    def parse(self, response):
        return {
            'name': response.css('title::text').extract_first(),
            'url': response.url,
        }
    
# 注意：RedisSpider类 不需要写allowd_domains和start_urls：

#	1.scrapy-redis将从在构造方法__init__()里动态定义爬虫爬取域范围，也可以选择直接写allowd_domains。

#	2.必须指定redis_key，即启动爬虫的命令，参考格式：redis_key = 'myspider:start_urls'

#	3.根据指定的格式，start_urls将在 Master端的 redis-cli 里 lpush 到 Redis数据库里，RedisSpider 将在数据库里获取start_urls。

# 执行方式：
#	1.通过runspider方法执行爬虫的py文件（也可以分次执行多条），爬虫（们）将处于等待准备状态：
scrapy runspider myspider_redis.py
#	2.在Master端的redis-cli输入push指令，参考格式：
$redis > lpush myspider:start_urls http://www.dmoz.org/       
#	3.Slaver端爬虫获取到请求，开始爬取。

# 三、mycrawler_redis (class MyCrawler(RedisCrawlSpider))
# 这个RedisCrawlSpider类爬虫继承了RedisCrawlSpider，能够支持分布式的抓取。因为采用的是crawlSpider，所以需要遵守Rule规则，以及callback不能写parse()方法。

# 同样也不再有start_urls了，取而代之的是redis_key，scrapy-redis将key从Redis里pop出来，成为请求的url地址。
from scrapy.spiders import Rule
from scrapy.linkextractors import LinkExtractor

from scrapy_redis.spiders import RedisCrawlSpider


class MyCrawler(RedisCrawlSpider):
    """Spider that reads urls from redis queue (myspider:start_urls)."""
    name = 'mycrawler_redis'
    redis_key = 'mycrawler:start_urls'

    rules = (
        # follow all links
        Rule(LinkExtractor(), callback='parse_page', follow=True),
    )

    # __init__方法必须按规定写，使用时只需要修改super()里的类名参数即可
    def __init__(self, *args, **kwargs):
        # Dynamically define the allowed domains list.
        domain = kwargs.pop('domain', '')
        self.allowed_domains = filter(None, domain.split(','))

        # 修改这里的类名为当前类名
        super(MyCrawler, self).__init__(*args, **kwargs)

    def parse_page(self, response):
        return {
            'name': response.css('title::text').extract_first(),
            'url': response.url,
        }
    
# 注意：同样的，RedisCrawlSpider类不需要写allowd_domains和start_urls：

#	1.scrapy-redis将从在构造方法__init__()里动态定义爬虫爬取域范围，也可以选择直接写allowd_domains。

#	2.必须指定redis_key，即启动爬虫的命令，参考格式：redis_key = 'myspider:start_urls'

#	3.根据指定的格式，start_urls将在 Master端的 redis-cli 里 lpush 到 Redis数据库里，RedisSpider 将在数据库里获取start_urls。 

# 执行方式：
#	1.通过runspider方法执行爬虫的py文件（也可以分次执行多条），爬虫（们）将处于等待准备状态：
scrapy runspider mycrawler_redis.py
#	2.在Master端的redis-cli输入push指令，参考格式：
$redis > lpush mycrawler:start_urls http://www.dmoz.org/
#	3.爬虫获取url，开始执行。

# 总结:
# 1.如果只是用到Redis的去重和保存功能，就选第一种；
# 2.如果要写分布式，则根据情况，选择第二种、第三种；
# 3.通常情况下，会选择用第三种方式编写深度聚焦爬虫。
```

### 171. 有缘网分布式(一)

```python
# clone github scrapy-redis源码文件
git clone https://github.com/rolando/scrapy-redis.git

# 直接拿官方的项目范例，改名为自己的项目用
mv scrapy-redis/example-project ~/scrapy-youyuan

# 1.修改settings.py
# 指定使用scrapy-redis的调度器
SCHEDULER = "scrapy_redis.scheduler.Scheduler"

# 指定使用scrapy-redis的去重
DUPEFILTER_CLASS = 'scrapy_redis.dupefilters.RFPDupeFilter'

# 指定排序爬取地址时使用的队列，
# 默认的 按优先级排序(Scrapy默认)，由sorted set实现的一种非FIFO、LIFO方式。
SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.SpiderPriorityQueue'
# 可选的 按先进先出排序（FIFO）
# SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.SpiderQueue'
# 可选的 按后进先出排序（LIFO）
# SCHEDULER_QUEUE_CLASS = 'scrapy_redis.queue.SpiderStack'

# 在redis中保持scrapy-redis用到的各个队列，从而允许暂停和暂停后恢复，也就是不清理redis queues
SCHEDULER_PERSIST = True

# 只在使用SpiderQueue或者SpiderStack是有效的参数，指定爬虫关闭的最大间隔时间
# SCHEDULER_IDLE_BEFORE_CLOSE = 10

# 通过配置RedisPipeline将item写入key为 spider.name : items 的redis的list中，供后面的分布式处理item
# 这个已经由 scrapy-redis 实现，不需要我们写代码
ITEM_PIPELINES = {
    'example.pipelines.ExamplePipeline': 300,
    'scrapy_redis.pipelines.RedisPipeline': 400
}

# 指定redis数据库的连接参数
# REDIS_PASS是我自己加上的redis连接密码（默认不做）
REDIS_HOST = '127.0.0.1'
REDIS_PORT = 6379
#REDIS_PASS = 'redisP@ssw0rd'

# LOG等级
LOG_LEVEL = 'DEBUG'

#默认情况下,RFPDupeFilter只记录第一个重复请求。将DUPEFILTER_DEBUG设置为True会记录所有重复的请求。
DUPEFILTER_DEBUG =True

# 覆盖默认请求头，可以自己编写Downloader Middlewares设置代理和UserAgent
DEFAULT_REQUEST_HEADERS = {
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
    'Accept-Language': 'zh-CN,zh;q=0.8',
    'Connection': 'keep-alive',
    'Accept-Encoding': 'gzip, deflate, sdch'
}

# 2. 查看pipeline.py
from datetime import datetime

class ExamplePipeline(object):
    def process_item(self, item, spider):
        #utcnow() 是获取UTC时间
        item["crawled"] = datetime.utcnow()
        # 爬虫名
        item["spider"] = spider.name
        return item
    
# 3.修改items.py
from scrapy.item import Item, Field

class youyuanItem(Item):
    # 个人头像链接
    header_url = Field()
    # 用户名
    username = Field()
    # 内心独白
    monologue = Field()
    # 相册图片链接
    pic_urls = Field()
    # 年龄
    age = Field()

    # 网站来源 youyuan
    source = Field()
    # 个人主页源url
    source_url = Field()

    # 获取UTC时间
    crawled = Field()
    # 爬虫名
    spider = Field()
    
# 4.编写 spiders/youyuan.py
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
# 使用redis去重
from scrapy.dupefilters import RFPDupeFilter

from example.items import youyuanItem
import re

#
class YouyuanSpider(CrawlSpider):
    name = 'youyuan'
    allowed_domains = ['youyuan.com']
    # 有缘网的列表页
    start_urls = ['http://www.youyuan.com/find/beijing/mm18-25/advance-0-0-0-0-0-0-0/p1/']

    # 搜索页面匹配规则，根据response提取链接
    list_page_lx = LinkExtractor(allow=(r'http://www.youyuan.com/find/.+'))

    # 北京、18~25岁、女性 的 搜索页面匹配规则，根据response提取链接
    page_lx = LinkExtractor(allow =(r'http://www.youyuan.com/find/beijing/mm18-25/advance-0-0-0-0-0-0-0/p\d+/'))

    # 个人主页 匹配规则，根据response提取链接
    profile_page_lx = LinkExtractor(allow=(r'http://www.youyuan.com/\d+-profile/'))

    rules = (
        # 匹配find页面，跟进链接，跳板
        Rule(list_page_lx, follow=True),

        # 匹配列表页成功，跟进链接，跳板
        Rule(page_lx, follow=True),

        # 匹配个人主页的链接，形成request保存到redis中等待调度，一旦有响应则调用parse_profile_page()回调函数处理，不做继续跟进
        Rule(profile_page_lx, callback='parse_profile_page', follow=False),
    )

    # 处理个人主页信息，得到我们要的数据
    def parse_profile_page(self, response):
        item = youyuanItem()
        item['header_url'] = self.get_header_url(response)
        item['username'] = self.get_username(response)
        item['monologue'] = self.get_monologue(response)
        item['pic_urls'] = self.get_pic_urls(response)
        item['age'] = self.get_age(response)
        item['source'] = 'youyuan'
        item['source_url'] = response.url

        #print "Processed profile %s" % response.url
        yield item


    # 提取头像地址
    def get_header_url(self, response):
        header = response.xpath('//dl[@class=\'personal_cen\']/dt/img/@src').extract()
        if len(header) > 0:
            header_url = header[0]
        else:
            header_url = ""
        return header_url.strip()

    # 提取用户名
    def get_username(self, response):
        usernames = response.xpath("//dl[@class=\'personal_cen\']/dd/div/strong/text()").extract()
        if len(usernames) > 0:
            username = usernames[0]
        else:
            username = "NULL"
        return username.strip()

    # 提取内心独白
    def get_monologue(self, response):
        monologues = response.xpath("//ul[@class=\'requre\']/li/p/text()").extract()
        if len(monologues) > 0:
            monologue = monologues[0]
        else:
            monologue = "NULL"
        return monologue.strip()

    # 提取相册图片地址
    def get_pic_urls(self, response):
        pic_urls = []
        data_url_full = response.xpath('//li[@class=\'smallPhoto\']/@data_url_full').extract()
        if len(data_url_full) <= 1:
            pic_urls.append("");
        else:
            for pic_url in data_url_full:
                pic_urls.append(pic_url)
        if len(pic_urls) <= 1:
            return "NULL"
        # 每个url用|分隔
        return '|'.join(pic_urls)

    # 提取年龄
    def get_age(self, response):
        age_urls = response.xpath("//dl[@class=\'personal_cen\']/dd/p[@class=\'local\']/text()").extract()
        if len(age_urls) > 0:
            age = age_urls[0]
        else:
            age = "0"
        age_words = re.split(' ', age)
        if len(age_words) <= 2:
            return "0"
        age = age_words[2][:-1]
        # 从age字符串开始匹配数字，失败返回None
        if re.compile(r'[0-9]').match(age):
            return age
        return "0"
    
# 5. 运行程序
# 1.Master端打开 Redis： redis-server
# 2.Slave端直接运行爬虫： scrapy crawl youyuan
# 3.多个Slave端运行爬虫顺序没有限制。
    
```

### 172. 有缘网分布式(二)

```python
# 修改 spiders/youyuan.py
from scrapy.linkextractors import LinkExtractor
#from scrapy.spiders import CrawlSpider, Rule

# 1. 导入RedisCrawlSpider类，不使用CrawlSpider
from scrapy_redis.spiders import RedisCrawlSpider
from scrapy.spiders import Rule


from scrapy.dupefilters import RFPDupeFilter
from example.items import youyuanItem
import re

# 2. 修改父类 RedisCrawlSpider
# class YouyuanSpider(CrawlSpider):
class YouyuanSpider(RedisCrawlSpider):
    name = 'youyuan'

# 3. 取消 allowed_domains() 和 start_urls
##### allowed_domains = ['youyuan.com']
##### start_urls = ['http://www.youyuan.com/find/beijing/mm18-25/advance-0-0-0-0-0-0-0/p1/']

# 4. 增加redis-key
    redis_key = 'youyuan:start_urls'

    list_page_lx = LinkExtractor(allow=(r'http://www.youyuan.com/find/.+'))
    page_lx = LinkExtractor(allow =(r'http://www.youyuan.com/find/beijing/mm18-25/advance-0-0-0-0-0-0-0/p\d+/'))
    profile_page_lx = LinkExtractor(allow=(r'http://www.youyuan.com/\d+-profile/'))

    rules = (
        Rule(list_page_lx, follow=True),
        Rule(page_lx, follow=True),
        Rule(profile_page_lx, callback='parse_profile_page', follow=False),
    )

# 5. 增加__init__()方法，动态获取allowed_domains()
    def __init__(self, *args, **kwargs):
        domain = kwargs.pop('domain', '')
        self.allowed_domains = filter(None, domain.split(','))
        super(youyuanSpider, self).__init__(*args, **kwargs)

    # 处理个人主页信息，得到我们要的数据
    def parse_profile_page(self, response):
        item = youyuanItem()
        item['header_url'] = self.get_header_url(response)
        item['username'] = self.get_username(response)
        item['monologue'] = self.get_monologue(response)
        item['pic_urls'] = self.get_pic_urls(response)
        item['age'] = self.get_age(response)
        item['source'] = 'youyuan'
        item['source_url'] = response.url

        yield item

    # 提取头像地址
    def get_header_url(self, response):
        header = response.xpath('//dl[@class=\'personal_cen\']/dt/img/@src').extract()
        if len(header) > 0:
            header_url = header[0]
        else:
            header_url = ""
        return header_url.strip()

    # 提取用户名
    def get_username(self, response):
        usernames = response.xpath("//dl[@class=\'personal_cen\']/dd/div/strong/text()").extract()
        if len(usernames) > 0:
            username = usernames[0]
        else:
            username = "NULL"
        return username.strip()

    # 提取内心独白
    def get_monologue(self, response):
        monologues = response.xpath("//ul[@class=\'requre\']/li/p/text()").extract()
        if len(monologues) > 0:
            monologue = monologues[0]
        else:
            monologue = "NULL"
        return monologue.strip()

    # 提取相册图片地址
    def get_pic_urls(self, response):
        pic_urls = []
        data_url_full = response.xpath('//li[@class=\'smallPhoto\']/@data_url_full').extract()
        if len(data_url_full) <= 1:
            pic_urls.append("");
        else:
            for pic_url in data_url_full:
                pic_urls.append(pic_url)
        if len(pic_urls) <= 1:
            return "NULL"
        return '|'.join(pic_urls)

    # 提取年龄
    def get_age(self, response):
        age_urls = response.xpath("//dl[@class=\'personal_cen\']/dd/p[@class=\'local\']/text()").extract()
        if len(age_urls) > 0:
            age = age_urls[0]
        else:
            age = "0"
        age_words = re.split(' ', age)
        if len(age_words) <= 2:
            return "0"
        age = age_words[2][:-1]
        if re.compile(r'[0-9]').match(age):
            return age
        return "0"
    
# 分布式爬虫执行方式：
# 6. 在Master端启动redis-server：
redis-server

# 7. 在Slave端分别启动爬虫，不分先后：
scrapy runspider youyuan.py

# 8. 在Master端的redis-cli里push一个start_urls  
redis-cli> lpush youyuan:start_urls http://www.youyuan.com/find/beijing/mm18-25/advance-0-0-0-0-0-0-0/p1/

# 9. 爬虫启动，查看redis数据库数据。        
```

### 173. 数据处理

```python
# 存入MongoDB数据库

# 1. 启动MongoDB数据库：sudo mongod

# 2.执行下面程序：python2 process_youyuan_mongodb.py

import json
import redis
import pymongo

def main():

    # 指定Redis数据库信息
    rediscli = redis.StrictRedis(host='192.168.199.108', port=6379, db=0)
    # 指定MongoDB数据库信息
    mongocli = pymongo.MongoClient(host='localhost', port=27017)

    # 创建数据库名
    db = mongocli['youyuan']
    # 创建表名
    sheet = db['beijing_18_25']

    while True:
        # FIFO模式为 blpop，LIFO模式为 brpop，获取键值
        source, data = rediscli.blpop(["youyuan:items"])

        item = json.loads(data)
        sheet.insert(item)

        try:
            print u"Processing: %(name)s <%(link)s>" % item
        except KeyError:
            print u"Error procesing: %r" % item

if __name__ == '__main__':
    main()
    

#  存入 MySQL数据库
# 启动mysql：mysql.server start（更平台不一样）
# 登录到root用户：mysql -uroot -p
# 创建数据库youyuan:create database youyuan;
# 切换到指定数据库：use youyuan
# 创建表beijing_18_25以及所有字段的列名和数据类型。
# 执行下面程序：python2 process_youyuan_mysql.py

import json
import redis
import MySQLdb

def main():
    # 指定redis数据库信息
    rediscli = redis.StrictRedis(host='192.168.199.108', port = 6379, db = 0)
    # 指定mysql数据库
    mysqlcli = MySQLdb.connect(host='127.0.0.1', user='power', passwd='xxxxxxx', db = 'youyuan', port=3306, use_unicode=True)

    while True:
        # FIFO模式为 blpop，LIFO模式为 brpop，获取键值
        source, data = rediscli.blpop(["youyuan:items"])
        item = json.loads(data)

        try:
            # 使用cursor()方法获取操作游标
            cur = mysqlcli.cursor()
            # 使用execute方法执行SQL INSERT语句
            cur.execute("INSERT INTO beijing_18_25 (username, crawled, age, spider, header_url, source, pic_urls, monologue, source_url) VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s )", [item['username'], item['crawled'], item['age'], item['spider'], item['header_url'], item['source'], item['pic_urls'], item['monologue'], item['source_url']])
            # 提交sql事务
            mysqlcli.commit()
            #关闭本次操作
            cur.close()
            print "inserted %s" % item['source_url']
        except MySQLdb.Error,e:
            print "Mysql Error %d: %s" % (e.args[0], e.args[1])

if __name__ == '__main__':
    main()
```

### 174. 新浪网分布式

```python
# items.py文件

import scrapy

import sys
reload(sys)
sys.setdefaultencoding("utf-8")

class SinaItem(scrapy.Item):
    # 大类的标题 和 url
    parentTitle = scrapy.Field()
    parentUrls = scrapy.Field()

    # 小类的标题 和 子url
    subTitle = scrapy.Field()
    subUrls = scrapy.Field()

    # 小类目录存储路径
    # subFilename = scrapy.Field()

    # 小类下的子链接
    sonUrls = scrapy.Field()

    # 文章标题和内容
    head = scrapy.Field()
    content = scrapy.Field()

# settings.py文件
SPIDER_MODULES = ['Sina.spiders']
NEWSPIDER_MODULE = 'Sina.spiders'

USER_AGENT = 'scrapy-redis (+https://github.com/rolando/scrapy-redis)'

DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
SCHEDULER = "scrapy_redis.scheduler.Scheduler"
SCHEDULER_PERSIST = True
SCHEDULER_QUEUE_CLASS = "scrapy_redis.queue.SpiderPriorityQueue"
#SCHEDULER_QUEUE_CLASS = "scrapy_redis.queue.SpiderQueue"
#SCHEDULER_QUEUE_CLASS = "scrapy_redis.queue.SpiderStack"

ITEM_PIPELINES = {
#    'Sina.pipelines.SinaPipeline': 300,
    'scrapy_redis.pipelines.RedisPipeline': 400,
}

LOG_LEVEL = 'DEBUG'

# Introduce an artifical delay to make use of parallelism. to speed up the
# crawl.
DOWNLOAD_DELAY = 1

REDIS_HOST = "192.168.13.26"
REDIS_PORT = 6379

# spiders/sina.py
from Sina.items import SinaItem
from scrapy_redis.spiders import RedisSpider
#from scrapy.spiders import Spider
import scrapy

import sys
reload(sys)
sys.setdefaultencoding("utf-8")

#class SinaSpider(Spider):
class SinaSpider(RedisSpider):
    name= "sina"
    redis_key = "sinaspider:start_urls"
    #allowed_domains= ["sina.com.cn"]
    #start_urls= [
    #   "http://news.sina.com.cn/guide/"
    #]#起始urls列表

    def __init__(self, *args, **kwargs):
        domain = kwargs.pop('domain', '')
        self.allowed_domains = filter(None, domain.split(','))
        super(SinaSpider, self).__init__(*args, **kwargs)


    def parse(self, response):
        items= []

        # 所有大类的url 和 标题
        parentUrls = response.xpath('//div[@id=\"tab01\"]/div/h3/a/@href').extract()
        parentTitle = response.xpath("//div[@id=\"tab01\"]/div/h3/a/text()").extract()

        # 所有小类的ur 和 标题
        subUrls  = response.xpath('//div[@id=\"tab01\"]/div/ul/li/a/@href').extract()
        subTitle = response.xpath('//div[@id=\"tab01\"]/div/ul/li/a/text()').extract()

        #爬取所有大类
        for i in range(0, len(parentTitle)):

            # 指定大类的路径和目录名
            #parentFilename = "./Data/" + parentTitle[i]

            #如果目录不存在，则创建目录
            #if(not os.path.exists(parentFilename)):
            #    os.makedirs(parentFilename)

            # 爬取所有小类
            for j in range(0, len(subUrls)):
                item = SinaItem()

                # 保存大类的title和urls
                item['parentTitle'] = parentTitle[i]
                item['parentUrls'] = parentUrls[i]

                # 检查小类的url是否以同类别大类url开头，如果是返回True (sports.sina.com.cn 和 sports.sina.com.cn/nba)
                if_belong = subUrls[j].startswith(item['parentUrls'])

                # 如果属于本大类，将存储目录放在本大类目录下
                if(if_belong):
                    #subFilename =parentFilename + '/'+ subTitle[j]

                    # 如果目录不存在，则创建目录
                    #if(not os.path.exists(subFilename)):
                    #    os.makedirs(subFilename)

                    # 存储 小类url、title和filename字段数据
                    item['subUrls'] = subUrls[j]
                    item['subTitle'] =subTitle[j]
                    #item['subFilename'] = subFilename

                    items.append(item)

        #发送每个小类url的Request请求，得到Response连同包含meta数据 一同交给回调函数 second_parse 方法处理
        for item in items:
            yield scrapy.Request( url = item['subUrls'], meta={'meta_1': item}, callback=self.second_parse)

    #对于返回的小类的url，再进行递归请求
    def second_parse(self, response):
        # 提取每次Response的meta数据
        meta_1= response.meta['meta_1']

        # 取出小类里所有子链接
        sonUrls = response.xpath('//a/@href').extract()

        items= []
        for i in range(0, len(sonUrls)):
            # 检查每个链接是否以大类url开头、以.shtml结尾，如果是返回True
            if_belong = sonUrls[i].endswith('.shtml') and sonUrls[i].startswith(meta_1['parentUrls'])

            # 如果属于本大类，获取字段值放在同一个item下便于传输
            if(if_belong):
                item = SinaItem()
                item['parentTitle'] =meta_1['parentTitle']
                item['parentUrls'] =meta_1['parentUrls']
                item['subUrls'] =meta_1['subUrls']
                item['subTitle'] =meta_1['subTitle']
                #item['subFilename'] = meta_1['subFilename']
                item['sonUrls'] = sonUrls[i]
                items.append(item)

        #发送每个小类下子链接url的Request请求，得到Response后连同包含meta数据 一同交给回调函数 detail_parse 方法处理
        for item in items:
                yield scrapy.Request(url=item['sonUrls'], meta={'meta_2':item}, callback = self.detail_parse)

    # 数据解析方法，获取文章标题和内容
    def detail_parse(self, response):
        item = response.meta['meta_2']
        content = ""
        head = response.xpath('//h1[@id=\"main_title\"]/text()').extract()
        content_list = response.xpath('//div[@id=\"artibody\"]/p/text()').extract()

        # 将p标签里的文本内容合并到一起
        for content_one in content_list:
            content += content_one

        item['head']= head[0] if len(head) > 0 else "NULL"

        item['content']= content

        yield item
  

# 执行：        
# slave端：
scrapy runspider sina.py

# Master端：
redis-cli> lpush sinaspider:start_urls http://news.sina.com.cn/guide/
```

### 175. IT桔子

```python
# 1. items.py
import scrapy

class CompanyItem(scrapy.Item):

    # 公司id (url数字部分)
    info_id = scrapy.Field()
    # 公司名称
    company_name = scrapy.Field()
    # 公司口号
    slogan = scrapy.Field()
    # 分类
    scope = scrapy.Field()
    # 子分类
    sub_scope = scrapy.Field()

    # 所在城市
    city = scrapy.Field()
    # 所在区域
    area = scrapy.Field()
    # 公司主页
    home_page = scrapy.Field()
    # 公司标签
    tags = scrapy.Field()

    # 公司简介
    company_intro = scrapy.Field()
    # 公司全称：
    company_full_name = scrapy.Field()
    # 成立时间：
    found_time = scrapy.Field()
    # 公司规模：
    company_size = scrapy.Field()
    # 运营状态
    company_status = scrapy.Field()

    # 投资情况列表：包含获投时间、融资阶段、融资金额、投资公司
    tz_info = scrapy.Field()
    # 团队信息列表：包含成员姓名、成员职称、成员介绍
    tm_info = scrapy.Field()
    # 产品信息列表：包含产品名称、产品类型、产品介绍
    pdt_info = scrapy.Field()
    
# settings.py
BOT_NAME = 'itjuzi'

SPIDER_MODULES = ['itjuzi.spiders']
NEWSPIDER_MODULE = 'itjuzi.spiders'

# Enables scheduling storing requests queue in redis.
SCHEDULER = "scrapy_redis.scheduler.Scheduler"

# Ensure all spiders share same duplicates filter through redis.
DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"

# REDIS_START_URLS_AS_SET = True

COOKIES_ENABLED = False

DOWNLOAD_DELAY = 1.5

# 支持随机下载延迟
RANDOMIZE_DOWNLOAD_DELAY = True

# Obey robots.txt rules
ROBOTSTXT_OBEY = False

ITEM_PIPELINES = {
    'scrapy_redis.pipelines.RedisPipeline': 300
}

DOWNLOADER_MIDDLEWARES = {
    # 该中间件将会收集失败的页面，并在爬虫完成后重新调度。（失败情况可能由于临时的问题，例如连接超时或者HTTP 500错误导致失败的页面）
   'scrapy.downloadermiddlewares.retry.RetryMiddleware': 80,

    # 该中间件提供了对request设置HTTP代理的支持。您可以通过在 Request 对象中设置 proxy 元数据来开启代理。
    'scrapy.downloadermiddlewares.httpproxy.HttpProxyMiddleware': 100,

    'itjuzi.middlewares.RotateUserAgentMiddleware': 200,
}

REDIS_HOST = "192.168.199.108"
REDIS_PORT = 6379


# middlewares.py
from scrapy.contrib.downloadermiddleware.useragent import UserAgentMiddleware
import random

# User-Agetn 下载中间件
class RotateUserAgentMiddleware(UserAgentMiddleware):
    def __init__(self, user_agent=''):
        self.user_agent = user_agent

    def process_request(self, request, spider):
        # 这句话用于随机选择user-agent
        ua = random.choice(self.user_agent_list)
        request.headers.setdefault('User-Agent', ua)

    user_agent_list = [
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.1 (KHTML, like Gecko) Chrome/22.0.1207.1 Safari/537.1",
        "Mozilla/5.0 (X11; CrOS i686 2268.111.0) AppleWebKit/536.11 (KHTML, like Gecko) Chrome/20.0.1132.57 Safari/536.11",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.6 (KHTML, like Gecko) Chrome/20.0.1092.0 Safari/536.6",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.6 (KHTML, like Gecko) Chrome/20.0.1090.0 Safari/536.6",
        "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.1 (KHTML, like Gecko) Chrome/19.77.34.5 Safari/537.1",
        "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/536.5 (KHTML, like Gecko) Chrome/19.0.1084.9 Safari/536.5",
        "Mozilla/5.0 (Windows NT 6.0) AppleWebKit/536.5 (KHTML, like Gecko) Chrome/19.0.1084.36 Safari/536.5",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_0) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1063.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1062.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1062.0 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1061.1 Safari/536.3",
        "Mozilla/5.0 (Windows NT 6.2) AppleWebKit/536.3 (KHTML, like Gecko) Chrome/19.0.1061.0 Safari/536.3",
        "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/535.24 (KHTML, like Gecko) Chrome/19.0.1055.1 Safari/535.24",
        "Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/535.24 (KHTML, like Gecko) Chrome/19.0.1055.1 Safari/535.24",
        "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US) AppleWebKit/531.21.8 (KHTML, like Gecko) Version/4.0.4 Safari/531.21.10",
        "Mozilla/5.0 (Windows; U; Windows NT 5.2; en-US) AppleWebKit/533.17.8 (KHTML, like Gecko) Version/5.0.1 Safari/533.17.8",
        "Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/533.19.4 (KHTML, like Gecko) Version/5.0.2 Safari/533.18.5",
        "Mozilla/5.0 (Windows; U; Windows NT 6.1; en-GB; rv:1.9.1.17) Gecko/20110123 (like Firefox/3.x) SeaMonkey/2.0.12",
        "Mozilla/5.0 (Windows NT 5.2; rv:10.0.1) Gecko/20100101 Firefox/10.0.1 SeaMonkey/2.7.1",
        "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_5_8; en-US) AppleWebKit/532.8 (KHTML, like Gecko) Chrome/4.0.302.2 Safari/532.8",
        "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_4; en-US) AppleWebKit/534.3 (KHTML, like Gecko) Chrome/6.0.464.0 Safari/534.3",
        "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_5; en-US) AppleWebKit/534.13 (KHTML, like Gecko) Chrome/9.0.597.15 Safari/534.13",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_2) AppleWebKit/535.1 (KHTML, like Gecko) Chrome/14.0.835.186 Safari/535.1",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_6_8) AppleWebKit/535.2 (KHTML, like Gecko) Chrome/15.0.874.54 Safari/535.2",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_6_8) AppleWebKit/535.7 (KHTML, like Gecko) Chrome/16.0.912.36 Safari/535.7",
        "Mozilla/5.0 (Macintosh; U; Mac OS X Mach-O; en-US; rv:2.0a) Gecko/20040614 Firefox/3.0.0 ",
        "Mozilla/5.0 (Macintosh; U; PPC Mac OS X 10.5; en-US; rv:1.9.0.3) Gecko/2008092414 Firefox/3.0.3",
        "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1) Gecko/20090624 Firefox/3.5",
        "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.6; en-US; rv:1.9.2.14) Gecko/20110218 AlexaToolbar/alxf-2.0 Firefox/3.6.14",
        "Mozilla/5.0 (Macintosh; U; PPC Mac OS X 10.5; en-US; rv:1.9.2.15) Gecko/20110303 Firefox/3.6.15",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10.6; rv:2.0.1) Gecko/20100101 Firefox/4.0.1"
    ]
    
# spiders/juzi.py 
from bs4 import BeautifulSoup
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule

from scrapy_redis.spiders import RedisCrawlSpider
from itjuzi.items import CompanyItem


class ITjuziSpider(RedisCrawlSpider):
    name = 'itjuzi'
    allowed_domains = ['www.itjuzi.com']
    # start_urls = ['http://www.itjuzi.com/company']
    redis_key = 'itjuzispider:start_urls'
    rules = [
        # 获取每一页的链接
        Rule(link_extractor=LinkExtractor(allow=('/company\?page=\d+'))),
        # 获取每一个公司的详情
        Rule(link_extractor=LinkExtractor(allow=('/company/\d+')), callback='parse_item')
    ]

    def parse_item(self, response):
        soup = BeautifulSoup(response.body, 'lxml')

        # 开头部分： //div[@class="infoheadrow-v2 ugc-block-item"]
        cpy1 = soup.find('div', class_='infoheadrow-v2')
        if cpy1:
            # 公司名称：//span[@class="title"]/b/text()[1]
            company_name = cpy1.find(class_='title').b.contents[0].strip().replace('\t', '').replace('\n', '')

            # 口号： //div[@class="info-line"]/p
            slogan = cpy1.find(class_='info-line').p.get_text()

            # 分类：子分类//span[@class="scope c-gray-aset"]/a[1]
            scope_a = cpy1.find(class_='scope c-gray-aset').find_all('a')
            # 分类：//span[@class="scope c-gray-aset"]/a[1]
            scope = scope_a[0].get_text().strip() if len(scope_a) > 0 else ''
            # 子分类：# //span[@class="scope c-gray-aset"]/a[2]
            sub_scope = scope_a[1].get_text().strip() if len(scope_a) > 1 else ''

            # 城市+区域：//span[@class="loca c-gray-aset"]/a
            city_a = cpy1.find(class_='loca c-gray-aset').find_all('a')
            # 城市：//span[@class="loca c-gray-aset"]/a[1]
            city = city_a[0].get_text().strip() if len(city_a) > 0 else ''
            # 区域：//span[@class="loca c-gray-aset"]/a[2]
            area = city_a[1].get_text().strip() if len(city_a) > 1 else ''

            # 主页：//a[@class="weblink marl10"]/@href
            home_page = cpy1.find(class_='weblink marl10')['href']
            # 标签：//div[@class="tagset dbi c-gray-aset"]/a
            tags = cpy1.find(class_='tagset dbi c-gray-aset').get_text().strip().strip().replace('\n', ',')

        #基本信息：//div[@class="block-inc-info on-edit-hide"]
        cpy2 = soup.find('div', class_='block-inc-info on-edit-hide')
        if cpy2:

            # 公司简介：//div[@class="block-inc-info on-edit-hide"]//div[@class="des"]
            company_intro = cpy2.find(class_='des').get_text().strip()

            # 公司全称：成立时间：公司规模：运行状态：//div[@class="des-more"]
            cpy2_content = cpy2.find(class_='des-more').contents

            # 公司全称：//div[@class="des-more"]/div[1]
            company_full_name = cpy2_content[1].get_text().strip()[len('公司全称：'):] if cpy2_content[1] else ''

            # 成立时间：//div[@class="des-more"]/div[2]/span[1]
            found_time = cpy2_content[3].contents[1].get_text().strip()[len('成立时间：'):] if cpy2_content[3] else ''

            # 公司规模：//div[@class="des-more"]/div[2]/span[2]
            company_size = cpy2_content[3].contents[3].get_text().strip()[len('公司规模：'):] if cpy2_content[3] else ''

            #运营状态：//div[@class="des-more"]/div[3]
            company_status = cpy2_content[5].get_text().strip() if cpy2_content[5] else ''

        # 主体信息：
        main = soup.find('div', class_='main')

        # 投资情况：//table[@class="list-round-v2 need2login"]
          # 投资情况，包含获投时间、融资阶段、融资金额、投资公司
        tz = main.find('table', 'list-round-v2')
        tz_list = []
        if tz:
            all_tr = tz.find_all('tr')
            for tr in all_tr:
                tz_dict = {}
                all_td = tr.find_all('td')
                tz_dict['tz_time'] = all_td[0].span.get_text().strip()
                tz_dict['tz_round'] = all_td[1].get_text().strip()
                tz_dict['tz_finades'] = all_td[2].get_text().strip()
                tz_dict['tz_capital'] = all_td[3].get_text().strip().replace('\n', ',')
                tz_list.append(tz_dict)

        # 团队信息：成员姓名、成员职称、成员介绍
        tm = main.find('ul', class_='list-prodcase limited-itemnum')
        tm_list = []
        if tm:
            for li in tm.find_all('li'):
                tm_dict = {}
                tm_dict['tm_m_name'] = li.find('span', class_='c').get_text().strip()
                tm_dict['tm_m_title'] = li.find('span', class_='c-gray').get_text().strip()
                tm_dict['tm_m_intro'] = li.find('p', class_='mart10 person-des').get_text().strip()
                tm_list.append(tm_dict)

        # 产品信息：产品名称、产品类型、产品介绍
        pdt = main.find('ul', class_='list-prod limited-itemnum')
        pdt_list = []
        if pdt:
            for li in pdt.find_all('li'):
                pdt_dict = {}
                pdt_dict['pdt_name'] = li.find('h4').b.get_text().strip()
                pdt_dict['pdt_type'] = li.find('span', class_='tag yellow').get_text().strip()
                pdt_dict['pdt_intro'] = li.find(class_='on-edit-hide').p.get_text().strip()
                pdt_list.append(pdt_dict)

        item = CompanyItem()
        item['info_id'] = response.url.split('/')[-1:][0]
        item['company_name'] = company_name
        item['slogan'] = slogan
        item['scope'] = scope
        item['sub_scope'] = sub_scope
        item['city'] = city
        item['area'] = area
        item['home_page'] = home_page
        item['tags'] = tags
        item['company_intro'] = company_intro
        item['company_full_name'] = company_full_name
        item['found_time'] = found_time
        item['company_size'] = company_size
        item['company_status'] = company_status
        item['tz_info'] = tz_list
        item['tm_info'] = tm_list
        item['pdt_info'] = pdt_list
        return item
    
# scrapy.cfg
# Automatically created by: scrapy startproject
#
# For more information about the [deploy] section see:
# https://scrapyd.readthedocs.org/en/latest/deploy.html

[settings]
default = itjuzi.settings

[deploy]
#url = http://localhost:6800/
project = itjuzi


# 运行:
# Slave端：
scrapy runspider juzi.py

# Master端：
redis-cli > lpush itjuzispider:start_urls http://www.itjuzi.com/company
    

```

## 十一  Tornado

### 176. 介绍

```python
# Tornado全称Tornado Web Server，是一个用Python语言写成的Web服务器兼Web应用框架，由FriendFeed公司在自己的网站FriendFeed中使用，被Facebook收购以后框架在2009年9月以开源软件形式开放给大众。

# 特点：

# 1. 作为Web框架，是一个轻量级的Web框架，类似于另一个Python web框架Web.py，其拥有异步非阻塞IO的处理方式。
# 2.作为Web服务器，Tornado有较为出色的抗负载能力，官方用nginx反向代理的方式部署Tornado和其它Python web应用框架进行对比，结果最大浏览量超过第二名近40%。
# 3.性能：Tornado有着优异的性能。它试图解决C10k问题，即处理大于或等于一万的并发

# Tornado框架和服务器一起组成一个WSGI的全栈替代品。单独在WSGI容器中使用tornado网络框架或者tornaod http服务器，有一定的局限性，为了最大化的利用tornado的性能，推荐同时使用tornaod的网络框架和HTTP服务器

# Tornado与Django
# Django是走大而全的方向，注重的是高效开发，它最出名的是其全自动化的管理后台：只需要使用起ORM，做简单的对象定义，它就能自动生成数据库结构、以及全功能的管理后台。

# Django提供的方便，也意味着Django内置的ORM跟框架内的其他模块耦合程度高，应用程序必须使用Django内置的ORM，否则就不能享受到框架内提供的种种基于其ORM的便利。

# session功能
# 后台管理
# ORM

# Tornado走的是少而精的方向，注重的是性能优越，它最出名的是异步非阻塞的设计方式。

# HTTP服务器
# 异步编程
# WebSockets


```

### 177. 安装

```python
#  pip install tornado
```

### 178. Hello Wrold

```python
import tornado.web
  import tornado.ioloop

  class IndexHandler(tornado.web.RequestHandler):
      """主路由处理类"""
      def post(self):  # 我们修改了这里
          """对应http的post请求方式"""
          self.write("Hello Wrold!")

  if __name__ == "__main__":
      app = tornado.web.Application([
          (r"/", IndexHandler),
      ])
      app.listen(8000)
      tornado.ioloop.IOLoop.current().start()
        
# 代码讲解:
# 1. tornado.web
# tornado的基础web框架模块
# RequestHandler 封装了对应一个请求的所有信息和方法，write(响应信息)就是写响应信息的一个方法；对应每一种http请求方式（get、post等），把对应的处理逻辑写进同名的成员方法中（如对应get请求方式，就将对应的处理逻辑写在get()方法中），当没有对应请求方式的成员方法时，会返回“405: Method Not Allowed”错误。
# Application Tornado Web框架的核心应用类，是与服务器对接的接口，里面保存了路由信息表，其初始化接收的第一个参数就是一个路由信息映射元组的列表；其listen(端口)方法用来创建一个http服务器实例，并绑定到给定端口（注意：此时服务器并未开启监听）。

# 2. tornado.ioloop
# tornado的核心io循环模块，封装了Linux的epoll和BSD的kqueue，tornado高性能的基石。
# IOLoop.current()  返回当前线程的IOLoop实例。
# IOLoop.start() 启动IOLoop实例的I/O循环,同时服务器监听被打开。

# 思路总结:
# 创建web应用实例对象，第一个初始化参数为路由映射列表。
# 定义实现路由映射列表中的handler类。
# 创建服务器实例，绑定服务器端口。
# 启动当前线程的IOLoop。


```

### 179. httpserver

```python
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver # 新引入httpserver模块

class IndexHandler(tornado.web.RequestHandler):
    """主路由处理类"""
    def get(self):
        """对应http的get请求方式"""
        self.write("Hello Itcast!")

if __name__ == "__main__":
    app = tornado.web.Application([
        (r"/", IndexHandler),
    ])
    # ------------------------------
    # 我们修改这个部分
    # app.listen(8000)
    http_server = tornado.httpserver.HTTPServer(app) 
    http_server.listen(8000)
    # ------------------------------
    tornado.ioloop.IOLoop.current().start()

# 引入了tornado.httpserver模块，顾名思义，它就是tornado的HTTP服务器实现。

# 我们创建了一个HTTP服务器实例http_server，因为服务器要服务于我们刚刚建立的web应用，将接收到的客户端请求通过web应用中的路由映射表引导到对应的handler中，所以在构建http_server对象的时候需要传出web应用对象app。http_server.listen(8000)将服务器绑定到8000端口。

# 实际上一版代码中app.listen(8000)正是对这一过程的简写    
    
```

### 180.  单进程与多进程

```python
# 一次启动多个进程，修改上面的代码如下：
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver 

class IndexHandler(tornado.web.RequestHandler):
    """主路由处理类"""
    def get(self):
        """对应http的get请求方式"""
        self.write("Hello Itcast!")

if __name__ == "__main__":
    app = tornado.web.Application([
        (r"/", IndexHandler),
    ])
    http_server = tornado.httpserver.HTTPServer(app) 
    # -----------修改----------------
    http_server.bind(8000)
    http_server.start(0)
    # ------------------------------
    tornado.ioloop.IOLoop.current().start()
    
# http_server.bind(port)方法是将服务器绑定到指定端口。

# http_server.start(num_processes=1)方法指定开启几个进程，参数num_processes默认值为1，即默认仅开启一个进程；如果num_processes为None或者<=0，则自动根据机器硬件的cpu核芯数创建同等数目的子进程；如果num_processes>0，则创建num_processes个子进程。 

# 说明:
# 1.关于app.listen()
# app.listen()这个方法只能在单进程模式中使用。

# 对于app.listen()与手动创建HTTPServer实例

http_server = tornado.httpserver.HTTPServer(app) 
http_server.listen(8000)

# 这两种方式，建议使用后者即创建HTTPServer实例的方式，因为其对于理解tornado web应用工作流程的完整性有帮助，便于大家记忆tornado开发的模块组成和程序结构；在熟练使用后，可以改为简写。

# 2.关于多进程
# 虽然tornado提供了一次开启多个进程的方法，但是由于：
# 每个子进程都会从父进程中复制一份IOLoop实例，如过在创建子进程前我们的代码动了IOLoop实例，那么会影响到每一个子进程，势必会干扰到子进程IOLoop的工作；
# 所有进程是由一个命令一次开启的，也就无法做到在不停服务的情况下更新代码；
# 所有进程共享同一个端口，想要分别单独监控每一个进程就很困难。
# 不建议使用这种多进程的方式，而是手动开启多个进程，并且绑定不同的端口。

```

### 181. options

```python
# tornado.options模块——全局参数定义、存储、转换。
tornado.options.define()
# 用来定义options选项变量的方法，定义的变量可以在全局的tornado.options.options中获取使用，传入参数：

#	name 选项变量名，须保证全局唯一性，否则会报“Option 'xxx' already defined in ...”的错误；
#	default　选项变量的默认值，如不传默认为None；
#	type 选项变量的类型，从命令行或配置文件导入参数的时候tornado会根据这个类型转换输入的值，转换不成功时会报错，可以是str、float、int、datetime、timedelta中的某个，若未设置则根据default的值自动推断，若default也未设置，那么不再进行转换。可以通过利用设置type类型字段来过滤不正确的输入。
#	multiple 选项变量的值是否可以为多个，布尔类型，默认值为False，如果multiple为True，那么设置选项变量时值与值之间用英文逗号分隔，而选项变量则是一个list列表（若默认值和输入均未设置，则为空列表[]）。
#	help 选项变量的帮助提示信息，在命令行启动tornado时，通过加入命令行参数 --help　可以查看所有选项变量的信息（注意，代码中需要加入tornado.options.parse_command_line()）。

tornado.options.options
# 全局的options对象，所有定义的选项变量都会作为该对象的属性。

tornado.options.parse_command_line()
# 转换命令行参数，并将转换后的值对应的设置到全局options对象相关属性上。追加命令行参数的方式是--myoption=myvalue

# opt.py
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options # 新导入的options模块

tornado.options.define("port", default=8000, type=int, help="run server on the given port.") # 定义服务器监听端口选项
tornado.options.define("itcast", default=[], type=str, multiple=True, help="itcast subjects.") # 无意义，演示多值情况

class IndexHandler(tornado.web.RequestHandler):
    """主路由处理类"""
    def get(self):
        """对应http的get请求方式"""
        self.write("Hello Itcast!")

if __name__ == "__main__":
    tornado.options.parse_command_line()
    print tornado.options.options.itcast # 输出多值选项
    app = tornado.web.Application([
        (r"/", IndexHandler),
    ])
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(tornado.options.options.port)
    tornado.ioloop.IOLoop.current().start()
    
# 开启程序： 
python opt.py --port=9000 --itcast=python,c++,java,php,ios

tornado.options.parse_config_file(path)
# 从配置文件导入option，配置文件中的选项格式如下：
myoption = "myvalue"
myotheroption = "myothervalue"

# 新建配置文件config，注意字符串和列表按照python的语法格式：
port = 8000
itcast = ["python","c++","java","php","ios"]

# 修改opt.py文件：
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options # 新导入的options模块

tornado.options.define("port", default=8000, type=int, help="run server on the given port.") # 定义服务器监听端口选项
tornado.options.define("itcast", default=[], type=str, multiple=True, help="itcast subjects.") # 无意义，演示多值情况

class IndexHandler(tornado.web.RequestHandler):
    """主路由处理类"""
    def get(self):
        """对应http的get请求方式"""
        self.write("Hello Itcast!")

if __name__ == "__main__":
    tornado.options.parse_config_file("./config") # 仅仅修改了此处
    print tornado.options.options.itcast # 输出多值选项
    app = tornado.web.Application([
        (r"/", IndexHandler),
    ])
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(tornado.options.options.port)
    tornado.ioloop.IOLoop.current().start()
    
    
# 说明:
# 1. 日志
# 在代码中调用parse_command_line()或者parse_config_file()的方法时，tornado会默认为我们配置标准logging模块，即默认开启了日志功能，并向标准输出（屏幕）打印日志信息。
# 关闭tornado默认的日志功能，可以在命令行中添加--logging=none 或者在代码中执行如下操作:
from tornado.options import options, parse_command_line
options.logging = None
parse_command_line()

# 2. 配置文件
# 在使用prase_config_file()的时候，配置文件的书写格式仍需要按照python的语法要求，其优势是可以直接将配置文件的参数转换设置到全局对象tornado.options.options中；然而，其不方便的地方在于需要在代码中调用tornado.options.define()来定义选项，而且不支持字典类型，故而在实际应用中大都不使用这种方法。

# 在使用配置文件的时候，通常会新建一个python文件（如config.py），然后在里面直接定义python类型的变量（可以是字典类型）；在需要配置文件参数的地方，将config.py作为模块导入，并使用其中的变量参数。

# config.py文件：
# conding:utf-8

# Redis配置
redis_options = {
    'redis_host':'127.0.0.1',
    'redis_port':6379,
    'redis_pass':'',
}

# Tornado app配置
settings = {
    'template_path': os.path.join(os.path.dirname(__file__), 'templates'),
    'static_path': os.path.join(os.path.dirname(__file__), 'statics'),
    'cookie_secret':'0Q1AKOKTQHqaa+N80XhYW7KCGskOUE2snCW06UIxXgI=',
    'xsrf_cookies':False,
    'login_url':'/login',
    'debug':True,
}

# 日志
log_path = os.path.join(os.path.dirname(__file__), 'logs/log')

# 使用config.py的模块中导入config，如下：
# conding:utf-8

import tornado.web
import config

if __name__ = "__main__":
    app = tornado.web.Application([], **config.settings)
...
```

### 182. Application

```python
# settings
# debug，设置tornado是否工作在调试模式，默认为False即工作在生产模式。当设置debug=True 后，tornado会工作在调试/开发模式，在此种模式下，tornado为方便我们开发而提供了几种特性：

#	自动重启，tornado应用会监控我们的源代码文件，当有改动保存后便会重启程序，这可以减少我们手动重启程序的次数。需要注意的是，一旦我们保存的更改有错误，自动重启会导致程序报错而退出，从而需要我们保存修正错误后手动启动程序。这一特性也可单独通过autoreload=True设置；
#	取消缓存编译的模板，可以单独通过compiled_template_cache=False来设置；
#	取消缓存静态文件hash值，可以单独通过static_hash_cache=False来设置；
#	提供追踪信息，当RequestHandler或者其子类抛出一个异常而未被捕获后，会生成一个包含追踪信息的页面，可以单独通过serve_traceback=True来设置。

# 使用debug参数的方法：

import tornado.web
app = tornado.web.Application([], debug=True)



# 路由映射
# 在构建路由映射列表的时候，使用的是二元元组，如：
[(r"/", IndexHandler),]
# 对于这个映射列表中的路由，实际上还可以传入多个信息，如：
[
    (r"/", Indexhandler),
    (r"/cpp", ItcastHandler, {"subject":"c++"}),
    url(r"/python", ItcastHandler, {"subject":"python"}, name="python_url")
]
# 对于路由中的字典，会传入到对应的RequestHandler的initialize()方法中：
from tornado.web import RequestHandler
class ItcastHandler(RequestHandler):
    def initialize(self, subject):
        self.subject = subject

    def get(self):
        self.write(self.subject)
        
# 对于路由中的name字段，注意此时不能再使用元组，而应使用tornado.web.url来构建。name是给该路由起一个名字，可以通过调用RequestHandler.reverse_url(name)来获取该名子对应的url。
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options
from tornado.options import options, define
from tornado.web import url, RequestHandler

define("port", default=8000, type=int, help="run server on the given port.")

class IndexHandler(RequestHandler):
    def get(self):
        python_url = self.reverse_url("python_url")
        self.write('<a href="%s">itcast</a>' %
                   python_url)

class ItcastHandler(RequestHandler):
    def initialize(self, subject):
        self.subject = subject

    def get(self):
        self.write(self.subject)

if __name__ == "__main__":
    tornado.options.parse_command_line()
    app = tornado.web.Application([
            (r"/", Indexhandler),
            (r"/cpp", ItcastHandler, {"subject":"c++"}),
            url(r"/python", ItcastHandler, {"subject":"python"}, name="python_url")
        ],
        debug = True)
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.current().start()

```

### 183. 输入

```python
# HTTP协议向服务器传参有以下几种途径:
# 查询字符串（query string)，形如key1=value1&key2=value2；
# 请求体（body）中发送的数据，比如表单数据、json、xml；
# 提取uri的特定部分，如/blogs/2016/09/0001，可以在服务器端的路由中用正则表达式截取；
# 在http报文的头（header）中增加自定义字段，如X-XSRFToken=hello。

# 1. 获取查询字符串参数
get_query_argument(name, default=_ARG_DEFAULT, strip=True)
# 从请求的查询字符串中返回指定参数name的值，如果出现多个同名参数，则返回最后一个的值。
# default为设值未传name参数时返回的默认值，如若default也未设置，则会抛出tornado.web.MissingArgumentError异常。
# strip表示是否过滤掉左右两边的空白字符，默认为过滤。
get_query_arguments(name, strip=True)
# 从请求的查询字符串中返回指定参数name的值，注意返回的是list列表（即使对应name参数只有一个值）。若未找到name参数，则返回空列表[]。
# strip同前，不再赘述。

# 2. 获取请求体参数
get_body_argument(name, default=_ARG_DEFAULT, strip=True)
# 从请求体中返回指定参数name的值，如果出现多个同名参数，则返回最后一个的值。
# default与strip同前，不再赘述。
get_body_arguments(name, strip=True)
# 从请求体中返回指定参数name的值，注意返回的是list列表（即使对应name参数只有一个值）。若未找到name参数，则返回空列表[]。
# strip同前，不再赘述。
# 说明:
# 对于请求体中的数据要求为字符串，且格式为表单编码格式（与url中的请求字符串格式相同），即key1=value1&key2=value2，HTTP报文头Header中的"Content-Type"为application/x-www-form-urlencoded 或 multipart/form-data。对于请求体数据为json或xml的，无法通过这两个方法获取。

# 3. 前两类方法的整合
get_argument(name, default=_ARG_DEFAULT, strip=True)
# 从请求体和查询字符串中返回指定参数name的值，如果出现多个同名参数，则返回最后一个的值。
# default与strip同前，不再赘述。
get_arguments(name, strip=True)
# 从请求体和查询字符串中返回指定参数name的值，注意返回的是list列表（即使对应name参数只有一个值）。若未找到name参数，则返回空列表[]。
# strip同前，不再赘述。
# 说明:
# 对于请求体中数据的要求同前。 这两个方法最常用。
# 代码示例:
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options
from tornado.options import options, define
from tornado.web import RequestHandler, MissingArgumentError

define("port", default=8000, type=int, help="run server on the given port.")

class IndexHandler(RequestHandler):
    def post(self):
        query_arg = self.get_query_argument("a")
        query_args = self.get_query_arguments("a")
        body_arg = self.get_body_argument("a")
        body_args = self.get_body_arguments("a", strip=False)
        arg = self.get_argument("a")
        args = self.get_arguments("a")

        default_arg = self.get_argument("b", "itcast")
        default_args = self.get_arguments("b")

        try:
            missing_arg = self.get_argument("c")
        except MissingArgumentError as e:
            missing_arg = "We catched the MissingArgumentError!"
            print e
        missing_args = self.get_arguments("c")

        rep = "query_arg:%s<br/>" % query_arg
        rep += "query_args:%s<br/>" % query_args 
        rep += "body_arg:%s<br/>"  % body_arg
        rep += "body_args:%s<br/>" % body_args
        rep += "arg:%s<br/>"  % arg
        rep += "args:%s<br/>" % args 
        rep += "default_arg:%s<br/>" % default_arg 
        rep += "default_args:%s<br/>" % default_args 
        rep += "missing_arg:%s<br/>" % missing_arg
        rep += "missing_args:%s<br/>" % missing_args

        self.write(rep)

if __name__ == "__main__":
    tornado.options.parse_command_line()
    app = tornado.web.Application([
        (r"/", IndexHandler),
    ])
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.current().start()

    
# 4. 关于请求的其他信息 
# RequestHandler.request 对象存储了关于请求的相关信息，具体属性有：
#	method HTTP的请求方式，如GET或POST;
#	host 被请求的主机名；
#	uri 请求的完整资源标示，包括路径和查询字符串；
#	path 请求的路径部分；
#	query 请求的查询字符串部分；
#	version 使用的HTTP版本；
#	headers 请求的协议头，是类字典型的对象，支持关键字索引的方式获取特定协议头信息，例如：request.headers["Content-Type"]
#	body 请求体数据；
#	remote_ip 客户端的IP地址；
#	files 用户上传的文件，为字典类型，型如：
{
  "form_filename1":[<tornado.httputil.HTTPFile>, <tornado.httputil.HTTPFile>],
  "form_filename2":[<tornado.httputil.HTTPFile>,],
  ... 
}

# tornado.httputil.HTTPFile是接收到的文件对象，它有三个属性：
#	filename 文件的实际名字，与form_filename1不同，字典中的键名代表的是表单对应项的名字；
#	body 文件的数据实体；
#	content_type 文件的类型。 这三个对象属性可以像字典一样支持关键字索引，如request.files["form_filename1"][0]["body"]。

# 实现一个上传文件并保存在服务器本地的小程序upload.py：
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options
from tornado.options import options, define
from tornado.web import RequestHandler

define("port", default=8000, type=int, help="run server on the given port.")

class IndexHandler(RequestHandler):
    def get(self):
        self.write("hello itcast.")

class UploadHandler(RequestHandler): 
    def post(self):
        files = self.request.files
        img_files = files.get('img')
        if img_files:
            img_file = img_files[0]["body"]
            file = open("./itcast", 'w+')
            file.write(img_file)
            file.close()
        self.write("OK")

if __name__ == "__main__":
    tornado.options.parse_command_line()
    app = tornado.web.Application([
        (r"/", IndexHandler),
        (r"/upload", UploadHandler),
    ])
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.current().start()
    
    
# 5. 正则提取uri
# tornado中对于路由映射也支持正则提取uri，提取出来的参数会作为RequestHandler中对应请求方式的成员方法参数。若在正则表达式中定义了名字，则参数按名传递；若未定义名字，则参数按顺序传递。提取出来的参数会作为对应请求方式的成员方法的参数。
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options
from tornado.options import options, define
from tornado.web import RequestHandler

define("port", default=8000, type=int, help="run server on the given port.")

class IndexHandler(RequestHandler):
    def get(self):
        self.write("hello itcast.")

class SubjectCityHandler(RequestHandler):
    def get(self, subject, city):
        self.write(("Subject: %s<br/>City: %s" % (subject, city)))

class SubjectDateHandler(RequestHandler):
    def get(self, date, subject):
        self.write(("Date: %s<br/>Subject: %s" % (date, subject)))

if __name__ == "__main__":
    tornado.options.parse_command_line()
    app = tornado.web.Application([
        (r"/", IndexHandler),
        (r"/sub-city/(.+)/([a-z]+)", SubjectCityHandler), # 无名方式
        (r"/sub-date/(?P<subject>.+)/(?P<date>\d+)", SubjectDateHandler), #　命名方式
    ])
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.current().start()
    
# 建议：提取多个值时最好用命名方式。    
```

### 184. 输出

```python
# 1. write(chunk) 将chunk数据写到输出缓冲区。
class IndexHandler(RequestHandler):
    def get(self):
        self.write("hello world!")
# 利用write方法写json数据:
import json

class IndexHandler(RequestHandler):
    def get(self):
        stu = {
            "name":"zhangsan",
            "age":24,
            "gender":1,
        }
        stu_json = json.dumps(stu)
        self.write(stu_json)
        
# 不用自己手动去做json序列化，当write方法检测到我们传入的chunk参数是字典类型后，会自动帮我们转换为json字符串。
class IndexHandler(RequestHandler):
    def get(self):
        stu = {
            "name":"zhangsan",
            "age":24,
            "gender":1,
        }
        self.write(stu)
        
# 两种方式差异？
# 对比两种方式的响应头header中Content-Type字段，自己手动序列化时为Content-Type:text/html; charset=UTF-8，而采用write方法时为Content-Type:application/json; charset=UTF-8。

# write方法除了将字典转换为json字符串之外，还帮我们将Content-Type设置为application/json; charset=UTF-8。


# 2. set_header(name, value)
# 利用set_header(name, value)方法，可以手动设置一个名为name、值为value的响应头header字段。
# 用set_header方法来完成上面write所做的工作。
import json

class IndexHandler(RequestHandler):
    def get(self):
        stu = {
            "name":"zhangsan",
            "age":24,
            "gender":1,
        }
        stu_json = json.dumps(stu)
        self.write(stu_json)
        self.set_header("Content-Type", "application/json; charset=UTF-8")
        

# 3. set_default_headers()
# 该方法会在进入HTTP处理方法前先被调用，可以重写此方法来预先设置默认的headers。注意：在HTTP处理方法中使用set_header()方法会覆盖掉在set_default_headers()方法中设置的同名header。
class IndexHandler(RequestHandler):
    def set_default_headers(self):
        print "执行了set_default_headers()"
        # 设置get与post方式的默认响应体格式为json
        self.set_header("Content-Type", "application/json; charset=UTF-8")
        # 设置一个名为itcast、值为python的header
        self.set_header("itcast", "python")

    def get(self):
        print "执行了get()"
        stu = {
            "name":"zhangsan",
            "age":24,
            "gender":1,
        }
        stu_json = json.dumps(stu)
        self.write(stu_json)
        self.set_header("itcast", "i love python") # 注意此处重写了header中的itcast字段

    def post(self):
        print "执行了post()"
        stu = {
            "name":"zhangsan",
            "age":24,
            "gender":1,
        }
        stu_json = json.dumps(stu)
        self.write(stu_json)
  


# 4. set_status(status_code, reason=None)   为响应设置状态码。
# 参数说明：
#	status_code int类型，状态码，若reason为None，则状态码必须为下表中的。
#	reason string类型，描述状态码的词组，若为None，则会被自动填充为下表中的内容。
class Err404Handler(RequestHandler):
    """对应/err/404"""
    def get(self):
        self.write("hello itcast")
        self.set_status(404) # 标准状态码，不用设置reason

class Err210Handler(RequestHandler):
    """对应/err/210"""
    def get(self):
        self.write("hello itcast")
        self.set_status(210, "itcast error") # 非标准状态码，设置了reason

class Err211Handler(RequestHandler):
    """对应/err/211"""
    def get(self):
        self.write("hello itcast")
        self.set_status(211) # 非标准状态码，未设置reason，错误
        
        
# 5. redirect(url)  告知浏览器跳转到url。
class IndexHandler(RequestHandler):
    """对应/"""
    def get(self):
        self.write("主页")

class LoginHandler(RequestHandler):
    """对应/login"""
    def get(self):
        self.write('<form method="post"><input type="submit" value="登陆"></form>')

    def post(self):
        self.redirect("/")
  

# 6. send_error(status_code=500, **kwargs)
# 抛出HTTP错误状态码status_code，默认为500，kwargs为可变命名参数。使用send_error抛出错误后tornado会调用write_error()方法进行处理，并返回给浏览器处理后的错误页面。
class IndexHandler(RequestHandler):
    def get(self):
        self.write("主页")
        self.send_error(404, content="出现404错误")
# 注意：默认的write\_error()方法不会处理send\_error抛出的kwargs参数，即上面的代码中content="出现404错误"是没有意义的。
# 示例代码:
class IndexHandler(RequestHandler):
    def get(self):
        self.write("主页")
        self.send_error(404, content="出现404错误")
        self.write("结束") # 我们在send_error再次向输出缓冲区写内容
# 注意：使用send_error()方法后就不要再向输出缓冲区写内容了！


# 7. write_error(status_code, **kwargs)
# 用来处理send_error抛出的错误信息并返回给浏览器错误信息页面。可以重写此方法来定制自己的错误显示页面。
class IndexHandler(RequestHandler):
    def get(self):
        err_code = self.get_argument("code", None) # 注意返回的是unicode字符串，下同
        err_title = self.get_argument("title", "")
        err_content = self.get_argument("content", "")
        if err_code:
            self.send_error(err_code, title=err_title, content=err_content)
        else:
            self.write("主页")

    def write_error(self, status_code, **kwargs):
        self.write(u"<h1>出错了，程序员GG正在赶过来！</h1>")
        self.write(u"<p>错误名：%s</p>" % kwargs["title"])
        self.write(u"<p>错误详情：%s</p>" % kwargs["content"])
        
        
```

### 185. 接口与调用顺序

```python
# 1. initialize()
# 对应每个请求的处理类Handler在构造一个实例后首先执行initialize()方法。在讲输入时提到，路由映射中的第三个字典型参数会作为该方法的命名参数传递，如：
class ProfileHandler(RequestHandler):
    def initialize(self, database):
        self.database = database

    def get(self):
        ...

app = Application([
    (r'/user/(.*)', ProfileHandler, dict(database=database)),
    ])
# 此方法通常用来初始化参数（对象属性），很少使用。


# 2. prepare()
# 预处理，即在执行对应请求方式的HTTP方法（如get、post等）前先执行，注意：不论以何种HTTP方式请求，都会执行prepare()方法。
# 以预处理请求体中的json数据为例：
import json

class IndexHandler(RequestHandler):
    def prepare(self):
        if self.request.headers.get("Content-Type").startswith("application/json"):
            self.json_dict = json.loads(self.request.body)
        else:
            self.json_dict = None

    def post(self):
        if self.json_dict:
            for key, value in self.json_dict.items():
                self.write("<h3>%s</h3><p>%s</p>" % (key, value))

    def put(self):
        if self.json_dict:
            for key, value in self.json_dict.items():
                self.write("<h3>%s</h3><p>%s</p>" % (key, value))

                
# 3. HTTP方法
# get:请求指定的页面信息，并返回实体主体。
# head:类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头
# post:向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。
# delete:请求服务器删除指定的内容。
# patch:请求修改局部数据。
# put:从客户端向服务器传送的数据取代指定的文档的内容。
# options:返回给定URL支持的所有HTTP方法。

# 4. on_finish()
# 在请求处理结束后调用，即在调用HTTP方法后调用。通常该方法用来进行资源清理释放或处理日志等。注意：请尽量不要在此方法中进行响应输出。

# 5. set_default_headers()
# 6. write_error()
# 7. 调用顺序
class IndexHandler(RequestHandler):

    def initialize(self):
        print "调用了initialize()"

    def prepare(self):
        print "调用了prepare()"

    def set_default_headers(self):
        print "调用了set_default_headers()"

    def write_error(self, status_code, **kwargs):
        print "调用了write_error()"

    def get(self):
        print "调用了get()"

    def post(self):
        print "调用了post()"
        self.send_error(200)  # 注意此出抛出了错误

    def on_finish(self):
        print "调用了on_finish()"
        
        
# 在正常情况未抛出错误时，调用顺序为：
# 1. set_defautl_headers()
# 2. initialize()
# 3. prepare()
# 4. HTTP方法
# 5. on_finish()

# 在有错误抛出时，调用顺序为：
# 1.  set_default_headers()
# 2.  initialize()
# 3.  prepare()
# 4.  HTTP方法
# 5.  set_default_headers()
# 6.  write_error()
# 7.  on_finish()
```

### 186. 静态文件

```python
#  static_path
# 可以通过向web.Application类的构造函数传递一个名为static_path的参数来告诉Tornado从文件系统的一个特定位置提供静态文件，如：
app = tornado.web.Application(
    [(r'/', IndexHandler)],
    static_path=os.path.join(os.path.dirname(__file__), "statics"),
)
# 在这里，设置了一个当前应用目录下名为statics的子目录作为static_path的参数。现在应用将以读取statics目录下的filename.ext来响应诸如/static/filename.ext的请求，并在响应的主体中返回。

# 对于静态文件目录的命名，为了便于部署，建议使用static
# 对于静态文件资源，可以通过http://127.0.0.1/static/html/index.html来访问。而且在index.html中引用的静态资源文件，我们给定的路径也符合/static/...的格式，故页面可以正常浏览。
<link href="/static/plugins/bootstrap/css/bootstrap.min.css" rel="stylesheet">
<link href="/static/plugins/font-awesome/css/font-awesome.min.css" rel="stylesheet">
<link href="/static/css/reset.css" rel="stylesheet">
<link href="/static/css/main.css" rel="stylesheet">
<link href="/static/css/index.css" rel="stylesheet">

<script src="/static/js/jquery.min.js"></script>
<script src="/static/plugins/bootstrap/js/bootstrap.min.js"></script>
<script src="/static/js/index.js"></script>


# StaticFileHandler
# tornado.web.StaticFileHandler来自由映射静态文件与其访问路径url。
# tornado.web.StaticFileHandler是tornado预置的用来提供静态资源文件的handler。
import os

current_path = os.path.dirname(__file__)
app = tornado.web.Application(
    [
        (r'^/()$', StaticFileHandler, {"path":os.path.join(current_path, "statics/html"), "default_filename":"index.html"}),
        (r'^/view/(.*)$', StaticFileHandler, {"path":os.path.join(current_path, "statics/html")}),
    ],
    static_path=os.path.join(current_path, "statics"),
)

# path 用来指明提供静态文件的根路径，并在此目录中寻找在路由中用正则表达式提取的文件名。
# default_filename 用来指定访问路由中未指明文件名时，默认提供的文件。


# 现在，对于静态文件statics/html/index.html，可以通过三种方式进行访问：

# 1. http://127.0.0.1/static/html/index.html
# 2. http://127.0.0.1/
# 3. http://127.0.0.1/view/index.html
```

### 187. 使用模板

```python
# 1. 路径与渲染
# 使用模板，需要仿照静态文件路径设置一样，向web.Application类的构造函数传递一个名为template_path的参数来告诉Tornado从文件系统的一个特定位置提供模板文件，如：
app = tornado.web.Application(
    [(r'/', IndexHandler)],
    static_path=os.path.join(os.path.dirname(__file__), "statics"),
    template_path=os.path.join(os.path.dirname(__file__), "templates"),
)
# 在这里，设置了一个当前应用目录下名为templates的子目录作为template_path的参数。在handler中使用的模板将在此目录中寻找。
# 现在将静态文件目录statics/html中的index.html复制一份到templates目录中，此时文件目录结构为：
.
├── statics
│   ├── css
│   │   ├── index.css
│   │   ├── main.css
│   │   └── reset.css
│   ├── html
│   │   └── index.html
│   ├── images
│   │   ├── home01.jpg
│   │   ├── home02.jpg
│   │   ├── home03.jpg
│   │   └── landlord01.jpg
│   ├── js
│   │   ├── index.js
│   │   └── jquery.min.js
│   └── plugins
│       ├── bootstrap
│       │   └─...
│       └── font-awesome
│           └─...
├── templates
│   └── index.html
└── test.py

# 在handler中使用render()方法来渲染模板并返回给客户端。
class IndexHandler(RequestHandler):
    def get(self):
        self.render("index.html") # 渲染主页模板，并返回给客户端。



current_path = os.path.dirname(__file__)
app = tornado.web.Application(
    [
        (r'^/$', IndexHandler),
        (r'^/view/(.*)$', StaticFileHandler, {"path":os.path.join(current_path, "statics/html")}),
    ],
    static_path=os.path.join(current_path, "statics"),
    template_path=os.path.join(os.path.dirname(__file__), "templates"),
)


# 2. 模板语法
# 2-1 变量与表达式
# 在tornado的模板中使用{{}}作为变量或表达式的占位符，使用render渲染后占位符{{}}会被替换为相应的结果值。
# 将index.html中的一条房源信息记录:
<li class="house-item">
    <a href=""><img src="/static/images/home01.jpg"></a>
    <div class="house-desc">
        <div class="landlord-pic"><img src="/static/images/landlord01.jpg"></div>
        <div class="house-price">￥<span>398</span>/晚</div>
        <div class="house-intro">
            <span class="house-title">宽窄巷子+160平大空间+文化保护区双地铁</span>
            <em>整套出租 - 5分/6点评 - 北京市丰台区六里桥地铁</em>
        </div>
    </div>
</li>

# 改为模板：
<li class="house-item">
    <a href=""><img src="/static/images/home01.jpg"></a>
    <div class="house-desc">
        <div class="landlord-pic"><img src="/static/images/landlord01.jpg"></div>
        <div class="house-price">￥<span>{{price}}</span>/晚</div>
        <div class="house-intro">
            <span class="house-title">{{title}}</span>
            <em>整套出租 - {{score}}分/{{comments}}点评 - {{position}}</em>
        </div>
    </div>
</li>
# 渲染方式如下：
class IndexHandler(RequestHandler):
    def get(self):
        house_info = {
            "price": 398,
            "title": "宽窄巷子+160平大空间+文化保护区双地铁",
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        }
        self.render("index.html", **house_info)
        
# {{}}不仅可以包含变量，还可以是表达式，如：
<li class="house-item">
    <a href=""><img src="/static/images/home01.jpg"></a>
    <div class="house-desc">
        <div class="landlord-pic"><img src="/static/images/landlord01.jpg"></div>
        <div class="house-price">￥<span>{{p1 + p2}}</span>/晚</div>
        <div class="house-intro">
            <span class="house-title">{{"+".join(titles)}}</span>
            <em>整套出租 - {{score}}分/{{comments}}点评 - {{position}}</em>
        </div>
    </div>
</li>

class IndexHandler(RequestHandler):
    def get(self):
        house_info = {
            "p1": 198,
            "p2": 200,
            "titles": ["宽窄巷子", "160平大空间", "文化保护区双地铁"],
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        }
        self.render("index.html", **house_info)
        
        
# 2-2 控制语句
# 可以在Tornado模板中使用Python条件和循环语句。控制语句以{\%和\%}包围，并以类似下面的形式被使用：
{% if page is None %}
# 或
{% if len(entries) == 3 %}
# 控制语句的大部分就像对应的Python语句一样工作，支持if、for、while，注意end:
{% if ... %} ... {% elif ... %} ... {% else ... %} ... {% end %}
{% for ... in ... %} ... {% end %}
{% while ... %} ... {% end %}

# 修改index.html:
<ul class="house-list">
    {% if len(houses) > 0 %}
        {% for house in houses %}
        <li class="house-item">
            <a href=""><img src="/static/images/home01.jpg"></a>
            <div class="house-desc">
                <div class="landlord-pic"><img src="/static/images/landlord01.jpg"></div>
                <div class="house-price">￥<span>{{house["price"]}}</span>/晚</div>
                <div class="house-intro">
                    <span class="house-title">{{house["title"]}}</span>
                    <em>整套出租 - {{house["score"]}}分/{{house["comments"]}}点评 - {{house["position"]}}</em>
                </div>
            </div>
        </li>
        {% end %}
    {% else %}
        对不起，暂时没有房源。
    {% end %}
</ul>
# python中渲染语句为：
class IndexHandler(RequestHandler):
    def get(self):
        houses = [
        {
            "price": 398,
            "title": "宽窄巷子+160平大空间+文化保护区双地铁",
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        },
        {
            "price": 398,
            "title": "宽窄巷子+160平大空间+文化保护区双地铁",
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        },
        {
            "price": 398,
            "title": "宽窄巷子+160平大空间+文化保护区双地铁",
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        },
        {
            "price": 398,
            "title": "宽窄巷子+160平大空间+文化保护区双地铁",
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        },
        {
            "price": 398,
            "title": "宽窄巷子+160平大空间+文化保护区双地铁",
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        }]
        self.render("index.html", houses=houses)
        

# 2-3 函数
# static_url()  生成静态文件目录下文件的URL
<link rel="stylesheet" href="{{ static_url("style.css") }}">
# 优点：
#	static_url函数创建了一个基于文件内容的hash值，并将其添加到URL末尾（查询字符串的参数v）。这个hash值确保浏览器总是加载一个文件的最新版而不是之前的缓存版本。无论是在你应用的开发阶段，还是在部署到生产环境使用时，都非常有用，因为你的用户不必再为了看到你的静态内容而清除浏览器缓存了。
#	另一个好处是你可以改变你应用URL的结构，而不需要改变模板中的代码。例如，可以通过设置static_url_prefix来更改Tornado的默认静态路径前缀/static。如果使用static_url而不是硬编码的话，代码不需要改变。

# 转义
# 新建一个表单页面new.html:
<!DOCTYPE html>
<html>
    <head>
        <title>新建房源</title>
    </head>
    <body>
        <form method="post">
            <textarea name="text"></textarea>
            <input type="submit" value="提交">
        </form>
        {{text}}
    </body>
</html>
# 对应的handler为：
class NewHandler(RequestHandler):

    def get(self):
        self.render("new.html", text="")

    def post(self):
        text = self.get_argument("text", "") 
        print text
        self.render("new.html", text=text)
# 当我们在表单中填入如下内容时,写入的js程序并没有运行，而是显示出来了：：
<script>alert("hello!");</script>
# 查看页面源代码，发现<、>、"等被转换为对应的html字符。
&lt;script&gt;alert(&quot;hello!&quot;);&lt;/script&gt;
# 是因为tornado中默认开启了模板自动转义功能，防止网站受到恶意攻击。
# 可以通过raw语句来输出不被转义的原始格式，如：
{% raw text %}
# 注意：在Firefox浏览器中会直接弹出alert窗口，而在Chrome浏览器中，需要set_header("X-XSS-Protection", 0)
# 若要关闭自动转义，一种方法是在Application构造函数中传递autoescape=None，另一种方法是在每页模板中修改自动转义行为，添加如下语句：
{% autoescape None %}

# escape()
# 关闭自动转义后，可以使用escape()函数来对特定变量进行转义，如：
{{ escape(text) }}

# 自定义函数
# 在模板中还可以使用一个自己编写的函数，只需要将函数名作为模板的参数传递即可，就像其他变量一样。
# 修改后端如下：
def house_title_join(titles):
    return "+".join(titles)

class IndexHandler(RequestHandler):
    def get(self):
        house_list = [
        {
            "price": 398,
            "titles": ["宽窄巷子", "160平大空间", "文化保护区双地铁"],
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        },
        {
            "price": 398,
            "titles": ["宽窄巷子", "160平大空间", "文化保护区双地铁"],
            "score": 5,
            "comments": 6,
            "position": "北京市丰台区六里桥地铁"
        }]
        self.render("index.html", houses=house_list, title_join = house_title_join)
        
        
# 前端模板我们修改为：  
<ul class="house-list">
    {% if len(houses) > 0 %}
        {% for house in houses %}
        <li class="house-item">
            <a href=""><img src="/static/images/home01.jpg"></a>
            <div class="house-desc">
                <div class="landlord-pic"><img src="/static/images/landlord01.jpg"></div>
                <div class="house-price">￥<span>{{house["price"]}}</span>/晚</div>
                <div class="house-intro">
                    <span class="house-title">{{title_join(house["titles"])}}</span>
                    <em>整套出租 - {{house["score"]}}分/{{house["comments"]}}点评 - {{house["position"]}}</em>
                </div>
            </div>
        </li>
        {% end %}
    {% else %}
        对不起，暂时没有房源。
    {% end %}
</ul>


# 2-4 块 可以使用块来复用模板，块语法如下：
{% block block_name %} {% end %}
# 对模板index.html进行抽象，抽离出父模板base.html如下：
<!DOCTYPE html>
<html>
<head> 
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    {% block page_title %}{% end %}
    <link href="{{static_url('plugins/bootstrap/css/bootstrap.min.css')}}" rel="stylesheet">
    <link href="{{static_url('plugins/font-awesome/css/font-awesome.min.css')}}" rel="stylesheet">
    <link href="{{static_url('css/reset.css')}}" rel="stylesheet">
    <link href="{{static_url('css/main.css')}}" rel="stylesheet">
    {% block css_files %}{% end %}
</head>
<body>
    <div class="container">
        <div class="top-bar">
            {% block header %}{% end %}
        </div>
        {% block body %}{% end %}
        <div class="footer">
            {% block footer %}{% end %}
        </div>
    </div>

    <script src="{{static_url('js/jquery.min.js')}}"></script>
    <script src="{{static_url('plugins/bootstrap/js/bootstrap.min.js')}}"></script>
    {% block js_files %}{% end %}
</body>
</html>

# 而子模板index.html使用extends来使用父模板base.html，如下：
{% extends "base.html" %}

{% block page_title %}
    <title>爱家-房源</title>
{% end %}

{% block css_files %}
    <link href="{{static_url('css/index.css')}}" rel="stylesheet">
{% end %} 

{% block js_files %}
    <script src="{{static_url('js/index.js')}}"></script>
{% end %}

{% block header %}
    <div class="nav-bar">
        <h3 class="page-title">房 源</h3>
    </div>
{% end %}

{% block body %}
    <ul class="house-list">
    {% if len(houses) > 0 %}
        {% for house in houses %}
        <li class="house-item">
            <a href=""><img src="/static/images/home01.jpg"></a>
            <div class="house-desc">
                <div class="landlord-pic"><img src="/static/images/landlord01.jpg"></div>
                <div class="house-price">￥<span>{{house["price"]}}</span>/晚</div>
                <div class="house-intro">
                    <span class="house-title">{{title_join(house["titles"])}}</span>
                    <em>整套出租 - {{house["score"]}}分/{{house["comments"]}}点评 - {{house["position"]}}</em>
                </div>
            </div>
        </li>
        {% end %}
    {% else %}
        对不起，暂时没有房源。
    {% end %}
    </ul>
{% end %}

{% block footer %}
    <p><span><i class="fa fa-copyright"></i></span>爱家租房&nbsp;&nbsp;享受家的温馨</p>
{% end %}


```

### 188.  数据库

```python
# 在Tornado3.0版本以前提供tornado.database模块用来操作MySQL数据库，而从3.0版本开始，此模块就被独立出来，作为torndb包单独提供。torndb只是对MySQLdb的简单封装，不支持Python 3。
# torndb安装:
pip install torndb

# 连接初始化:
# 需要在应用启动时创建一个数据库连接实例，供各个RequestHandler使用。我们可以在构造Application的时候创建一个数据库实例并作为其属性，而RequestHandler可以通过self.application获取其属性，进而操作数据库实例。
import torndb

class Application(tornado.web.Application):
    def __init__(self):
        handlers = [
            (r"/", IndexHandler),
        ]
        settings = dict(
            template_path=os.path.join(os.path.dirname(__file__), "templates"),
            static_path=os.path.join(os.path.dirname(__file__), "statics"),
            debug=True,
        )
        super(Application, self).__init__(handlers, **settings)
        # 创建一个全局mysql连接实例供handler使用
        self.db = torndb.Connection(
            host="127.0.0.1",
            database="pachong",
            user="root",
            password="mysql"
        )

# 使用数据库:
# 1. 执行语句
# execute(query, parameters, *kwparameters) 返回影响的最后一条自增字段值
# execute_rowcount(query, parameters, *kwparameters) 返回影响的行数
# query为要执行的sql语句，parameters与kwparameters为要绑定的参数，如：
db.execute("insert into houses(title, position, price, score, comments) values(%s, %s, %s, %s, %s)", "独立装修小别墅", "紧邻文津街", 280, 5, 128)
或
db.execute("insert into houses(title, position, price, score, comments) values(%(title)s, %(position)s, %(price)s, %(score)s, %(comments)s)", title="独立装修小别墅", position="紧邻文津街", price=280, score=5, comments=128)
# 执行语句主要用来执行非查询语句。
class InsertHandler(RequestHandler):
    def post(self):
        title = self.get_argument("title")
        position = self.get_argument("position")
        price = self.get_argument("price")
        score = self.get_argument("score")
        comments = self.get_argument("comments")
        try:
            ret = self.application.db.execute("insert into houses(title, position, price, score, comments) values(%s, %s, %s, %s, %s)", title, position, price, score, comments)
        except Exception as e:
            self.write("DB error:%s" % e)
        else:
            self.write("OK %d" % ret)

# 2. 查询语句
# get(query, parameters, *kwparameters) 返回单行结果或None，若出现多行则报错。返回值为torndb.Row类型，是一个类字典的对象，即同时支持字典的关键字索引和对象的属相访问。
# query(query, parameters, *kwparameters) 返回多行结果，torndb.Row的列表。

# 修改一下index.html模板，将
<span class="house-title">{{title_join(house["titles"])}}</span>
# 改为
<span class="house-title">{{house["title"]}}</span>
# 添加两个新的handler：
class GetHandler(RequestHandler):
    def get(self):
        """访问方式为http://127.0.0.1/get?id=111"""
        hid = self.get_argument("id")
        try:
            ret = self.application.db.get("select title,position,price,score,comments from houses where id=%s", hid)
        except Exception as e:
            self.write("DB error:%s" % e)
        else:
            print type(ret)
            print ret
            print ret.title
            print ret['title']
            self.render("index.html", houses=[ret])


class QueryHandler(RequestHandler):
    def get(self):
        """访问方式为http://127.0.0.1/query"""
        try:
            ret = self.application.db.query("select title,position,price,score,comments from houses limit 10")
        except Exception as e:
            self.write("DB error:%s" % e)
        else:
            self.render("index.html", houses=ret)
```

### 189. 安全应用

```python
# 设置
set_cookie(name, value, domain=None, expires=None, path='/', expires_days=None)
# 参数说明：
# name:cookie名
# value:cookie值
# domain:提交cookie时匹配的域名
# path:提交cookie时匹配的路径
# expires:cookie的有效期，可以是时间戳整数、时间元组或者datetime类型，为UTC时间
# expires_days:cookie的有效期，天数，优先级低于expires

# 示例:
import datetime

class IndexHandler(RequestHandler):
    def get(self):
        self.set_cookie("n1", "v1")
        self.set_cookie("n2", "v2", path="/new", expires=time.strptime("2016-11-11 23:59:59","%Y-%m-%d %H:%M:%S"))
        self.set_cookie("n3", "v3", expires_days=20)
        # 利用time.mktime将本地时间转换为UTC标准时间
        self.set_cookie("n4", "v4", expires=time.mktime(time.strptime("2016-11-11 23:59:59","%Y-%m-%d %H:%M:%S")))
        self.write("OK")

# 原理
# 设置cookie实际就是通过设置header的Set-Cookie来实现的。  
# 获取
get_cookie(name, default=None)
# 获取名为name的cookie，可以设置默认值。
class IndexHandler(RequestHandler):
    def get(self):
        n3 = self.get_cookie("n3")
        self.write(n3)
# 清除
clear_cookie(name, path='/', domain=None)

# 删除名为name，并同时匹配domain和path的cookie。

clear_all_cookies(path='/', domain=None)

# 删除同时匹配domain和path的所有cookie。
# 示例:
class ClearOneCookieHandler(RequestHandler):
    def get(self):
        self.clear_cookie("n3")
        self.write("OK")

class ClearAllCookieHandler(RequestHandler):
    def get(self):
        self.clear_all_cookies()
        self.write("OK")
#   注意：执行清除cookie操作后，并不是立即删除了浏览器中的cookie，而是给cookie值置空，并改变其有效期使其失效。真正的删除cookie是由浏览器去清理的。  


# 安全Cookie
# Cookie是存储在客户端浏览器中的，很容易被篡改。Tornado提供了一种对Cookie进行简易加密签名的方法来防止Cookie被恶意篡改。

# 使用安全Cookie需要为应用配置一个用来给Cookie进行混淆的秘钥cookie_secret，将其传递给Application的构造函数。我们可以使用如下方法来生成一个随机字符串作为cookie_secret的值。
>>> import base64, uuid
>>> base64.b64encode(uuid.uuid4().bytes + uuid.uuid4().bytes)
'2hcicVu+TqShDpfsjMWQLZ0Mkq5NPEWSk9fi0zsSt3A='

# Base64是一种基于64个可打印字符来表示二进制数据的表示方法。由于2的6次方等于64，所以每6个比特为一个单元，对应某个可打印字符。三个字节有24个比特，对应于4个Base64单元，即3个字节需要用4个可打印字符来表示。

# uuid, 通用唯一识别码（英语：Universally Unique Identifier，简称UUID），是由一组32个16进制数字所构成（两个16进制数是一个字节，总共16字节），因此UUID理论上的总数为16^32=2^128，约等于3.4 x 10^38。也就是说若每纳秒产生1兆个UUID，要花100亿年才会将所有UUID用完。

# uuid模块的uuid4()函数可以随机产生一个uuid码，bytes属性将此uuid码作为16字节字符串。

# 将生成的cookie_secret传入Application构造函数：
app = tornado.web.Application(
    [(r"/", IndexHandler),],
    cookie_secret = "2hcicVu+TqShDpfsjMWQLZ0Mkq5NPEWSk9fi0zsSt3A="
)

# 获取和设置
set_secure_cookie(name, value, expires_days=30)
# 设置一个带签名和时间戳的cookie，防止cookie被伪造。
get_secure_cookie(name, value=None, max_age_days=31)
# 如果cookie存在且验证通过，返回cookie的值，否则返回None。max_age_day不同于expires_days，expires_days是设置浏览器中cookie的有效期，而max_age_day是过滤安全cookie的时间戳。

class IndexHandler(RequestHandler):
    def get(self):
        cookie = self.get_secure_cookie("count")
        count = int(cookie) + 1 if cookie else 1
        self.set_secure_cookie("count", str(count))
        self.write(
            '<html><head><title>Cookie计数器</title></head>'
            '<body><h1>您已访问本页%d次。</h1>' % count + 
            '</body></html>'
        )
# 签名后的cookie值：
"2|1:0|10:1476412069|5:count|4:NQ==|cb5fc1d4434971de6abf87270ac33381c686e4ec8c6f7e62130a0f8cbe5b7609"


# 字段说明：
# 安全cookie的版本，默认使用版本2，不带长度说明前缀
# 默认为0
# 时间戳
# cookie名
# base64编码的cookie值
# 签名值，不带长度说明前缀

# 注意：Tornado的安全cookie只是一定程度的安全，仅仅是增加了恶意修改的难度。Tornado的安全cookies仍然容易被窃听，而cookie值是签名不是加密，攻击者能够读取已存储的cookie值，并且可以传输他们的数据到任意服务器，或者通过发送没有修改的数据给应用伪造请求。因此，避免在浏览器cookie中存储敏感的用户数据是非常重要的。
```

### 190. XSRF 跨站请求伪造

```python
# 先建立一个网站127.0.0.1:8000，使用上一节中的Cookie计数器：
class IndexHandler(RequestHandler):
    def get(self):
        cookie = self.get_secure_cookie("count")
        count = int(cookie) + 1 if cookie else 1
        self.set_secure_cookie("count", str(count))
        self.write(
            '<html><head><title>Cookie计数器</title></head>'
            '<body><h1>您已访问本页%d次。</h1>' % count +
            '</body></html>'
        )
# 再建立一个网站127.0.0.1:9000，
class IndexHandler(RequestHandler):
    def get(self):
        self.write('<html><head><title>被攻击的网站</title></head>'
        '<body><h1>此网站的图片链接被修改了</h1>'
        '<img alt="这应该是图片" src="http://127.0.0.1:8000/?f=9000/">'
        '</body></html>'
        )
# 在9000网站模拟攻击者修改了图片源地址为8000网站的Cookie计数器页面网址。当我们访问9000网站的时候，在我们不知道、未授权的情况下8000网站的Cookie被使用了，以至于让8000网址认为是我们自己调用了8000网站的逻辑。这就是CSRF（Cross-site request forgery）跨站请求伪造（跨站攻击或跨域攻击的一种），通常缩写为CSRF或者XSRF。

# 刚刚使用的是GET方式模拟的攻击，为了防范这种方式的攻击，任何会产生副作用的HTTP请求，比如点击购买按钮、编辑账户设置、改变密码或删除文档，都应该使用HTTP POST方法（或PUT、DELETE）。但是，这并不足够：一个恶意站点可能会通过其他手段来模拟发送POST请求，保护POST请求需要额外的策略。


# XSRF保护
# 浏览器有一个很重要的概念——同源策略(Same-Origin Policy)。 所谓同源是指，域名，协议，端口相同。 不同源的客户端脚本(javascript、ActionScript)在没明确授权的情况下，不能读写对方的资源。
# 由于第三方站点没有访问cookie数据的权限（同源策略），所以我们可以要求每个请求包括一个特定的参数值作为令牌来匹配存储在cookie中的对应值，如果两者匹配，我们的应用认定请求有效。而第三方站点无法在请求中包含令牌cookie值，这就有效地防止了不可信网站发送未授权的请求。

# 开启XSRF保护
# 要开启XSRF保护，需要在Application的构造函数中添加xsrf_cookies参数：
app = tornado.web.Application(
    [(r"/", IndexHandler),],
    cookie_secret = "2hcicVu+TqShDpfsjMWQLZ0Mkq5NPEWSk9fi0zsSt3A=",
    xsrf_cookies = True
)
# 当这个参数被设置时，Tornado将拒绝请求参数中不包含正确的_xsrf值的POST、PUT和DELETE请求。
class IndexHandler(RequestHandler):
    def post(self):
        self.write("hello itcast")
# 用不带_xsrf的post请求时，报出了HTTP 403: Forbidden ('_xsrf' argument missing from POST)的错误。


# 模板应用
# 在模板中使用XSRF保护，只需在模板中添加
{% module xsrf_form_html() %}
# 新建一个模板index.html
<!DOCTYPE html>
<html>
<head>
    <title>测试XSRF</title>
</head>
<body>
    <form method="post">
      {% module xsrf_form_html() %}
      <input type="text" name="message"/>
      <input type="submit" value="Post"/>
    </form>
</body>
</html>
# 后端
class IndexHandler(RequestHandler):
    def get(self):
        self.render("index.html")

    def post(self):
        self.write("hello itcast")
# 作用:
# 为浏览器设置了_xsrf的Cookie（注意此Cookie浏览器关闭时就会失效）
# 为模板的表单中添加了一个隐藏的输入名为_xsrf，其值为_xsrf的Cookie值 
# 渲染后的页面原码如下：
<!DOCTYPE html>
<html>
    <head>
        <title>测试XSRF</title>
    </head>
    <body>
        <form method="post">
            <input type="hidden" name="_xsrf" value="2|543c2206|a056ff9e49df23eaffde0a694cde2b02|1476443353"/>
            <input type="text" name="message"/>
            <input type="submit" value="Post"/>
        </form>
    </body>
</html>



# 非模板应用
# 对于不使用模板的应用来说，首先要设置_xsrf的Cookie值，可以在任意的Handler中通过获取self.xsrf_token的值来生成_xsrf并设置Cookie。
# 下面两种方式都可以起到设置_xsrf Cookie的作用。
class XSRFTokenHandler(RequestHandler):
    """专门用来设置_xsrf Cookie的接口"""
    def get(self):
        self.xsrf_token
        self.write("Ok")

class StaticFileHandler(tornado.web.StaticFileHandler):
    """重写StaticFileHandler，构造时触发设置_xsrf Cookie"""
    def __init__(self, *args, **kwargs):
        super(StaticFileHandler, self).__init__(*args, **kwargs)
        self.xsrf_token
# 对于请求携带_xsrf参数，有两种方式：
# 若请求体是表单编码格式的，可以在请求体中添加_xsrf参数
# 若请求体是其他格式的（如json或xml等），可以通过设置HTTP头X-XSRFToken来传递_xsrf值
# 1. 请求体携带_xsrf参数
# 新建一个页面xsrf.html：
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>测试XSRF</title>
</head>
<body>
    <a href="javascript:;" onclick="xsrfPost()">发送POST请求</a>
    <script src="http://cdn.bootcss.com/jquery/3.1.1/jquery.min.js"></script>
    <script type="text/javascript">
        //获取指定Cookie的函数
        function getCookie(name) {
            var r = document.cookie.match("\\b" + name + "=([^;]*)\\b");
            return r ? r[1] : undefined;
        }
        //AJAX发送post请求，表单格式数据
        function xsrfPost() {
            var xsrf = getCookie("_xsrf");
            $.post("/new", "_xsrf="+xsrf+"&key1=value1", function(data) {
                alert("OK");
            });
        }
    </script>
</body>
</html>

# 2. HTTP头X-XSRFToken
# 新建一个页面json.html：
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>测试XSRF</title>
</head>
<body>
    <a href="javascript:;" onclick="xsrfPost()">发送POST请求</a>
    <script src="http://cdn.bootcss.com/jquery/3.1.1/jquery.min.js"></script>
    <script type="text/javascript">
        //获取指定Cookie的函数
        function getCookie(name) {
            var r = document.cookie.match("\\b" + name + "=([^;]*)\\b");
            return r ? r[1] : undefined;
        }
        //AJAX发送post请求，json格式数据
        function xsrfPost() {
            var xsrf = getCookie("_xsrf");
            var data = {
                key1:1,
                key1:2
            };
            var json_data = JSON.stringify(data);
            $.ajax({
                url: "/new",
                method: "POST",
                headers: {
                    "X-XSRFToken":xsrf,
                },
                data:json_data,
                success:function(data) {
                    alert("OK");
                }
            })
        }
    </script>
</body>
</html>
```

### 191. 用户验证

```python
# 用户验证是指在收到用户请求后进行处理前先判断用户的认证状态（如登陆状态），若通过验证则正常处理，否则强制用户跳转至认证页面（如登陆页面）。

# authenticated装饰器
# 为了使用Tornado的认证功能，我们需要对登录用户标记具体的处理函数。我们可以使用@tornado.web.authenticated装饰器完成它。当我们使用这个装饰器包裹一个处理方法时，Tornado将确保这个方法的主体只有在合法的用户被发现时才会调用。
class ProfileHandler(RequestHandler):
    @tornado.web.authenticated
    def get(self):
        self.write("这是我的个人主页。")
# get_current_user()方法
# 装饰器@tornado.web.authenticated的判断执行依赖于请求处理类中的self.current_user属性，如果current_user值为假（None、False、0、""等），任何GET或HEAD请求都将把访客重定向到应用设置中login_url指定的URL，而非法用户的POST请求将返回一个带有403（Forbidden）状态的HTTP响应。
# 在获取self.current_user属性的时候，tornado会调用get_current_user()方法来返回current_user的值。也就是说，我们验证用户的逻辑应写在get_current_user()方法中，若该方法返回非假值则验证通过，否则验证失败。
class ProfileHandler(RequestHandler):
    def get_current_user(self):
        """在此完成用户的认证逻辑"""
        user_name = self.get_argument("name", None)
        return user_name 

    @tornado.web.authenticated
    def get(self):
        self.write("这是我的个人主页。")
        
# login_url设置
# 当用户验证失败时，将用户重定向到login_url上，所以我们还需要在Application中配置login_url。
class LoginHandler(RequestHandler):
    def get(self):
        """在此返回登陆页面"""
        self.write("登陆页面")

app = tornado.web.Application(
    [
        (r"/", IndexHandler),
        (r"/profile", ProfileHandler),
        (r"/login", LoginHandler),
    ],
    "login_url":"/login"
)

# 在login_url后面补充的next参数就是记录的跳转至登录页面前的所在位置，所以我们可以使用next参数来完成登陆后的跳转。
# 修改登陆逻辑：
class LoginHandler(RequestHandler):
    def get(self):
        """登陆处理，完成登陆后跳转回前一页面"""
        next = self.get_argument("next", "/")
        self.redirect(next+"?name=logined")
        
```

### 192. 认识异步

```python
# 1. 同步
# 模拟两个客户端请求，并依次进行处理：
# coding:utf-8

def req_a():
    """模拟请求a"""
    print '开始处理请求req_a'
    print '完成处理请求req_a'

def req_b():
    """模拟请求b"""
    print '开始处理请求req_b'
    print '完成处理请求req_b'

def main():
    """模拟tornado框架，处理两个请求"""
    req_a()
    req_b()

if __name__ == "__main__":
    main()
    
    
#执行结果：

# 开始处理请求req_a
# 完成处理请求req_a
# 开始处理请求req_b
# 完成处理请求req_b

# 同步是按部就班的依次执行，始终按照同一个步调执行，上一个步骤未执行完不会执行下一步。
# 如果在处理请求req_a时需要执行一个耗时的工作（如IO），其执行过程:
import time

def long_io():
    """模拟耗时IO操作"""
    print "开始执行IO操作"
    time.sleep(5)
    print "完成IO操作"
    return "io result"

def req_a():
    print "开始处理请求req_a"
    ret = long_io()
    print "ret: %s" % ret
    print "完成处理请求req_a"

def req_b():
    print "开始处理请求req_b"
    print "完成处理请求req_b"

def main():
    req_a()
    req_b()

if __name__=="__main__":
    main()

# 执行过程：
# 开始处理请求req_a
# 开始执行IO操作
# 完成IO操作
# 完成处理请求req_a
# 开始处理请求req_b
# 完成处理请求req_b    
    

# 2. 异步
# 对于耗时的过程，我们将其交给别人（如其另外一个线程）去执行，而我们继续往下处理，当别人执行完耗时操作后再将结果反馈给我们，这就是我们所说的异步。
# 使用线程机制来实现异步。

# 2.1 回调写法实现原理
import time
import thread

def long_io(callback):
    """将耗时的操作交给另一线程来处理"""
    def fun(cb): # 回调函数作为参数
        """耗时操作"""
        print "开始执行IO操作"
        time.sleep(5)
        print "完成IO操作，并执行回调函数"
        cb("io result")  # 执行回调函数
    thread.start_new_thread(fun, (callback,))  # 开启线程执行耗时操作

def on_finish(ret):
    """回调函数"""
    print "开始执行回调函数on_finish"
    print "ret: %s" % ret
    print "完成执行回调函数on_finish"

def req_a():
    print "开始处理请求req_a" 
    long_io(on_finish)
    print "离开处理请求req_a"

def req_b():
    print "开始处理请求req_b"
    time.sleep(2) # 添加此句来突出显示程序执行的过程
    print "完成处理请求req_b"

def main():
    req_a()
    req_b()
    while 1: # 添加此句防止程序退出，保证线程可以执行完
        pass

if __name__ == '__main__':
    main()
    
    
# 执行过程:
# 开始处理请求req_a
# 离开处理请求req_a
# 开始处理请求req_b
# 开始执行IO操作
# 完成处理请求req_b
# 完成IO操作，并执行回调函数
# 开始执行回调函数on_finish
# ret: io result
# 完成执行回调函数on_finish

# 异步的特点是程序存在多个步调，即本属于同一个过程的代码可能在不同的步调上同时执行。


# 2.2 协程写法实现原理
# 在使用回调函数写异步程序时，需将本属于一个执行逻辑（处理请求a）的代码拆分成两个函数req_a和on_finish，这与同步程序的写法相差很大。而同步程序更便于理解业务逻辑，所以能否用同步代码的写法来编写异步程序？
# 初始版本
# coding:utf-8

import time
import thread

gen = None # 全局生成器，供long_io使用

def long_io():
    def fun():
        print "开始执行IO操作"
        global gen
        time.sleep(5)
        try:
            print "完成IO操作，并send结果唤醒挂起程序继续执行"
            gen.send("io result")  # 使用send返回结果并唤醒程序继续执行
        except StopIteration: # 捕获生成器完成迭代，防止程序退出
            pass
    thread.start_new_thread(fun, ())

def req_a():
    print "开始处理请求req_a"
    ret = yield long_io()
    print "ret: %s" % ret
    print "完成处理请求req_a"

def req_b():
    print "开始处理请求req_b"
    time.sleep(2)
    print "完成处理请求req_b"

def main():
    global gen
    gen = req_a()
    gen.next() # 开启生成器req_a的执行
    req_b()
    while 1:
        pass

if __name__ == '__main__':
    main()
    
# 执行过程：
# 开始处理请求req_a
# 开始处理请求req_b
# 开始执行IO操作
# 完成处理请求req_b
# 完成IO操作，并send结果唤醒挂起程序继续执行
# ret: io result
# 完成处理请求req_a
    
# 升级版本
# 上面编写出的版本虽然req_a的编写方式很类似与同步代码，但是在main中调用req_a的时候却不能将其简单的视为普通函数，而是需要作为生成器对待。
# 现在，试图尝试修改，让req_a与main的编写都类似与同步代码。
# coding:utf-8

import time
import thread

gen = None # 全局生成器，供long_io使用

def gen_coroutine(f):
    def wrapper(*args, **kwargs):
        global gen
        gen = f()
        gen.next()
    return wrapper

def long_io():
    def fun():
        print "开始执行IO操作"
        global gen
        time.sleep(5)
        try:
            print "完成IO操作，并send结果唤醒挂起程序继续执行"
            gen.send("io result")  # 使用send返回结果并唤醒程序继续执行
        except StopIteration: # 捕获生成器完成迭代，防止程序退出
            pass
    thread.start_new_thread(fun, ())

@gen_coroutine
def req_a():
    print "开始处理请求req_a"
    ret = yield long_io()
    print "ret: %s" % ret
    print "完成处理请求req_a"

def req_b():
    print "开始处理请求req_b"
    time.sleep(2)
    print "完成处理请求req_b"

def main():
    req_a()
    req_b()
    while 1:
        pass

if __name__ == '__main__':
    main()
    
    
# 执行过程：
# 开始处理请求req_a
# 开始处理请求req_b
# 开始执行IO操作
# 完成处理请求req_b
# 完成IO操作，并send结果唤醒挂起程序继续执行
# ret: io result
# 完成处理请求req_a


# 最终版本
# 刚刚完成的版本依然不理想，因为存在一个全局变量gen来供long_io使用。我们现在再次改写程序，消除全局变量gen。
# coding:utf-8

import time
import thread

def gen_coroutine(f):
    def wrapper(*args, **kwargs):
        gen_f = f()  # gen_f为生成器req_a
        r = gen_f.next()  # r为生成器long_io
        def fun(g):
            ret = g.next() # 执行生成器long_io
            try:
                gen_f.send(ret) # 将结果返回给req_a并使其继续执行
            except StopIteration:
                pass
        thread.start_new_thread(fun, (r,))
    return wrapper

def long_io():
    print "开始执行IO操作"
    time.sleep(5)
    print "完成IO操作，yield回操作结果"
    yield "io result"

@gen_coroutine
def req_a():
    print "开始处理请求req_a"
    ret = yield long_io()
    print "ret: %s" % ret
    print "完成处理请求req_a"

def req_b():
    print "开始处理请求req_b"
    time.sleep(2)
    print "完成处理请求req_b"

def main():
    req_a()
    req_b()
    while 1:
        pass

if __name__ == '__main__':
    main()
    
# 执行过程：
# 开始处理请求req_a
# 开始处理请求req_b
# 开始执行IO操作
# 完成处理请求req_b
# 完成IO操作，yield回操作结果
# ret: io result
# 完成处理请求req_a

# 最终版本就是理解Tornado异步编程原理的最简易模型，但是，Tornado实现异步的机制不是线程，而是epoll，即将异步过程交给epoll执行并进行监视回调。

# 需要注意的一点是，实现的版本严格意义上来说不能算是协程，因为两个程序的挂起与唤醒是在两个线程上实现的，而Tornado利用epoll来实现异步，程序的挂起与唤醒始终在一个线程上，由Tornado自己来调度，属于真正意义上的协程。虽如此，并不妨碍理解Tornado异步编程的原理。


```

### 193. Tornado异步

```python
# 因为epoll主要是用来解决网络IO的并发问题，所以Tornado的异步编程也主要体现在网络IO的异步上，即异步Web请求。

# 1. tornado.httpclient.AsyncHTTPClient
# Tornado提供了一个异步Web请求客户端tornado.httpclient.AsyncHTTPClient用来进行异步Web请求。
# fetch(request, callback=None)
# 用于执行一个web请求request，并异步返回一个tornado.httpclient.HTTPResponse响应。

# request可以是一个url，也可以是一个tornado.httpclient.HTTPRequest对象。如果是url，fetch会自己构造一个HTTPRequest对象。

# HTTPRequest
# HTTP请求类，HTTPRequest的构造函数可以接收众多构造参数，最常用的如下：
#	url (string) – 要访问的url，此参数必传，除此之外均为可选参数
#	method (string) – HTTP访问方式，如“GET”或“POST”，默认为GET方式
#	headers (HTTPHeaders or dict) – 附加的HTTP协议头
#	body – HTTP请求的请求体


# HTTPResponse
# HTTP响应类，其常用属性如下：
#	code: HTTP状态码，如 200 或 404
#	reason: 状态码描述信息
#	body: 响应体字符串
#	error: 异常（可有可无）

# 2. 测试接口
# 新浪IP地址库
#	接口说明
#	1.请求接口（GET）：
http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json&ip=[ip地址字串]
#	2.响应信息：
#	（json格式的）国家 、省（自治区或直辖市）、市（县）、运营商
#	3.返回数据格式：
{"ret":1,"start":-1,"end":-1,"country":"\u4e2d\u56fd","province":"\u5317\u4eac","city":"\u5317\u4eac","district":"","isp":"","type":"","desc":""}


# 3. 回调异步
class IndexHandler(tornado.web.RequestHandler):
    @tornado.web.asynchronous  # 不关闭连接，也不发送响应
    def get(self):
        http = tornado.httpclient.AsyncHTTPClient()
        http.fetch("http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json&ip=14.130.112.24",
                   callback=self.on_response)

    def on_response(self, response):
        if response.error:
            self.send_error(500)
        else:
            data = json.loads(response.body)
            if 1 == data["ret"]:
                self.write(u"国家：%s 省份: %s 城市: %s" % (data["country"], data["province"], data["city"]))
            else:
                self.write("查询IP信息错误")
        self.finish() # 发送响应信息，结束请求处理


# tornado.web.asynchronous
# 此装饰器用于回调形式的异步方法，并且应该仅用于HTTP的方法上（如get、post等）。

# 此装饰器不会让被装饰的方法变为异步，而只是告诉框架被装饰的方法是异步的，当方法返回时响应尚未完成。只有在request handler调用了finish方法后，才会结束本次请求处理，发送响应。

# 不带此装饰器的请求在get、post等方法返回时自动完成结束请求处理。


# 4. 协程异步
# 上一节中封装的装饰器get_coroutine在Tornado中对应的是tornado.gen.coroutine。
class IndexHandler(tornado.web.RequestHandler):
    @tornado.gen.coroutine
    def get(self):
        http = tornado.httpclient.AsyncHTTPClient()
        response = yield http.fetch("http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json&ip=14.130.112.24")
        if response.error:
            self.send_error(500)
        else:
            data = json.loads(response.body)
            if 1 == data["ret"]:
                self.write(u"国家：%s 省份: %s 城市: %s" % (data["country"], data["province"], data["city"]))
            else:
                self.write("查询IP信息错误")
                
# 也可以将异步Web请求单独出来：
class IndexHandler(tornado.web.RequestHandler):
    @tornado.gen.coroutine
    def get(self):
        rep = yield self.get_ip_info("14.130.112.24")
        if 1 == rep["ret"]:
            self.write(u"国家：%s 省份: %s 城市: %s" % (rep["country"], rep["province"], rep["city"]))
        else:
            self.write("查询IP信息错误")

    @tornado.gen.coroutine
    def get_ip_info(self, ip):
        http = tornado.httpclient.AsyncHTTPClient()
        response = yield http.fetch("http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json&ip=" + ip)
        if response.error:
            rep = {"ret:0"}
        else:
            rep = json.loads(response.body)
        raise tornado.gen.Return(rep)  # 此处需要注意
        
# 代码中我们需要注意的地方是get_ip_info返回值的方式，在python 2中，使用了yield的生成器可以使用不返回任何值的return，但不能return value，因此Tornado为我们封装了用于在生成器中返回值的特殊异常tornado.gen.Return，并用raise来返回此返回值。


# 并行协程
# Tornado可以同时执行多个异步，并发的异步可以使用列表或字典，如下：
class IndexHandler(tornado.web.RequestHandler):
    @tornado.gen.coroutine
    def get(self):
        ips = ["14.130.112.24",
            "15.130.112.24",
            "16.130.112.24",
            "17.130.112.24"]
        rep1, rep2 = yield [self.get_ip_info(ips[0]), self.get_ip_info(ips[1])]
        rep34_dict = yield dict(rep3=self.get_ip_info(ips[2]), rep4=self.get_ip_info(ips[3]))
        self.write_response(ips[0], rep1) 
        self.write_response(ips[1], rep2) 
        self.write_response(ips[2], rep34_dict['rep3']) 
        self.write_response(ips[3], rep34_dict['rep4']) 

    def write_response(self, ip, response):
        self.write(ip) 
        self.write(":<br/>") 
        if 1 == response["ret"]:
            self.write(u"国家：%s 省份: %s 城市: %s<br/>" % (response["country"], response["province"], response["city"]))
        else:
            self.write("查询IP信息错误<br/>")

    @tornado.gen.coroutine
    def get_ip_info(self, ip):
        http = tornado.httpclient.AsyncHTTPClient()
        response = yield http.fetch("http://int.dpool.sina.com.cn/iplookup/iplookup.php?format=json&ip=" + ip)
        if response.error:
            rep = {"ret:1"}
        else:
            rep = json.loads(response.body)
        raise tornado.gen.Return(rep)
        
# 5. 关于数据库的异步说明
# 网站基本都会有数据库操作，而Tornado是单线程的，这意味着如果数据库查询返回过慢，整个服务器响应会被堵塞。

# 数据库查询，实质上也是远程的网络调用；理想情况下，是将这些操作也封装成为异步的；但Tornado对此并没有提供任何支持。

# 这是Tornado的设计，而不是缺陷。

# 一个系统，要满足高流量；是必须解决数据库查询速度问题的！

# 数据库若存在查询性能问题，整个系统无论如何优化，数据库都会是瓶颈，拖慢整个系统！

# 异步并不能从本质上提到系统的性能；它仅仅是避免多余的网络响应等待，以及切换线程的CPU耗费。

# 如果数据库查询响应太慢，需要解决的是数据库的性能问题；而不是调用数据库的前端Web应用。

# 对于实时返回的数据查询，理想情况下需要确保所有数据都在内存中，数据库硬盘IO应该为0；这样的查询才能足够快；而如果数据库查询足够快，那么前端web应用也就无将数据查询封装为异步的必要。

# 就算是使用协程，异步程序对于同步程序始终还是会提高复杂性；需要衡量的是处理这些额外复杂性是否值得。

# 如果后端有查询实在是太慢，无法绕过，Tornaod的建议是将这些查询在后端封装独立封装成为HTTP接口，然后使用Tornado内置的异步HTTP客户端进行调用。
        
```

### 194. WebSocket

```python
# WebSocket是HTML5规范中新提出的客户端-服务器通讯协议，协议本身使用新的ws://URL格式。

# WebSocket 是独立的、创建在 TCP 上的协议，和 HTTP 的唯一关联是使用 HTTP 协议的101状态码进行协议切换，使用的 TCP 端口是80，可以用于绕过大多数防火墙的限制。

# WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端直接向客户端推送数据而不需要客户端进行请求，两者之间可以创建持久性的连接，并允许数据进行双向传送。

# 目前常见的浏览器如 Chrome、IE、Firefox、Safari、Opera 等都支持 WebSocket，同时需要服务端程序支持 WebSocket。


# 1. Tornado的WebSocket模块
# ornado提供支持WebSocket的模块是tornado.websocket，其中提供了一个WebSocketHandler类用来处理通讯。
WebSocketHandler.open()  # 当一个WebSocket连接建立后被调用。
WebSocketHandler.on_message(message)  # 当客户端发送消息message过来时被调用，注意此方法必须被重写。
WebSocketHandler.on_close()  #  当WebSocket连接关闭后被调用。
WebSocketHandler.write_message(message, binary=False) # 向客户端发送消息messagea，message可以是字符串或字典（字典会被转为json字符串）。若binary为False，则message以utf8编码发送；二进制模式（binary=True）时，可发送任何字节码。
WebSocketHandler.close()  # 关闭WebSocket连接。
WebSocketHandler.check_origin(origin)  # 判断源origin，对于符合条件（返回判断结果为True）的请求源origin允许其连接，否则返回403。可以重写此方法来解决WebSocket的跨域请求（如始终return True）。


# 2. 前端JavaScript编写
# 在前端JS中使用WebSocket与服务器通讯的常用方法如下：
var ws = new WebSocket("ws://127.0.0.1:8888/websocket"); // 新建一个ws连接
ws.onopen = function() {  // 连接建立好后的回调
   ws.send("Hello, world");  // 向建立的连接发送消息
};
ws.onmessage = function (evt) {  // 收到服务器发送的消息后执行的回调
   alert(evt.data);  // 接收的消息内容在事件参数evt的data属性中
};

#  3. 在线聊天室的小Demo
# 后端代码 server.py
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options
import os
import datetime

from tornado.web import RequestHandler
from tornado.options import define, options
from tornado.websocket import WebSocketHandler

define("port", default=8000, type=int)

class IndexHandler(RequestHandler):
    def get(self):
        self.render("index.html")

class ChatHandler(WebSocketHandler):

    users = set()  # 用来存放在线用户的容器

    def open(self):
        self.users.add(self)  # 建立连接后添加用户到容器中
        for u in self.users:  # 向已在线用户发送消息
            u.write_message(u"[%s]-[%s]-进入聊天室" % (self.request.remote_ip, datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")))

    def on_message(self, message):
        for u in self.users:  # 向在线用户广播消息
            u.write_message(u"[%s]-[%s]-说：%s" % (self.request.remote_ip, datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"), message))

    def on_close(self):
        self.users.remove(self) # 用户关闭连接后从容器中移除用户
        for u in self.users:
            u.write_message(u"[%s]-[%s]-离开聊天室" % (self.request.remote_ip, datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")))

    def check_origin(self, origin):
        return True  # 允许WebSocket的跨域请求

if __name__ == '__main__':
    tornado.options.parse_command_line()
    app = tornado.web.Application([
            (r"/", IndexHandler),
            (r"/chat", ChatHandler),
        ],
        static_path = os.path.join(os.path.dirname(__file__), "static"),
        template_path = os.path.join(os.path.dirname(__file__), "template"),
        debug = True
        )
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.current().start()
    
# 前端代码index.html

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>聊天室</title>
</head>
<body>
    <div id="contents" style="height:500px;overflow:auto;"></div>
    <div>
        <textarea id="msg"></textarea>
        <a href="javascript:;" onclick="sendMsg()">发送</a>
    </div>
    <script src="{{static_url('js/jquery.min.js')}}"></script>
    <script type="text/javascript">
        var ws = new WebSocket("ws://192.168.114.177:8000/chat");
        ws.onmessage = function(e) {
            $("#contents").append("<p>" + e.data + "</p>");
        }
        function sendMsg() {
            var msg = $("#msg").val();
            ws.send(msg);
            $("#msg").val("");
        }
    </script>
</body>
</html>

```

### 195. 部署Tornado

```python
# 为了充分利用多核CPU，并且为了减少同步代码中的阻塞影响，在部署Tornado的时候需要开启多个进程（最好为每个CPU核心开启一个进程）

# 因为Tornado自带的服务器性能很高，所以我们只需开启多个Tornado进程。为了对外有统一的接口，并且可以分发用户的请求到不同的Tornado进程上，用Nginx来进行代理。

# 1. supervisor
# 为了统一管理Tornado的多个进程，可以借助supervisor工具。

# 安装
sudo pip install supervisor

# 配置
# 运行echo_supervisord_conf命令输出默认的配置项，可以如下操作将默认配置保存到文件中
echo_supervisord_conf > supervisord.conf

# 打开编辑supervisord.conf文件，修改
[include]
files = relative/directory/*.ini
# 为:
[include]
files = /etc/supervisor/*.conf

# include选项指明包含的其他配置文件。
# 将编辑后的supervisord.conf文件复制到/etc/目录下
sudo cp supervisord.conf /etc/

# 然后在/etc目录下新建子目录supervisor（与配置文件里的选项相同），并在/etc/supervisor/中新建tornado管理的配置文件tornado.conf。
[group:tornadoes]
programs=tornado-8000,tornado-8001,tornado-8002,tornado-8003

[program:tornado-8000]
command=/home/python/.virtualenvs/tornado_py2/bin/python /home/python/Documents/demo/chat/server.py --port=8000
directory=/home/python/Documents/demo/chat
user=python
autorestart=true
redirect_stderr=true
stdout_logfile=/home/python/tornado.log
loglevel=info

[program:tornado-8001]
command=/home/python/.virtualenvs/tornado_py2/bin/python /home/python/Documents/demo/chat/server.py --port=8001
directory=/home/python/Documents/demo/chat
user=python
autorestart=true
redirect_stderr=true
stdout_logfile=/home/python/tornado.log
loglevel=info

[program:tornado-8002]
command=/home/python/.virtualenvs/tornado_py2/bin/python /home/python/Documents/demo/chat/server.py --port=8002
directory=/home/python/Documents/demo/chat
user=python
autorestart=true
redirect_stderr=true
stdout_logfile=/home/python/tornado.log
loglevel=info

[program:tornado-8003]
command=/home/python/.virtualenvs/tornado_py2/bin/python /home/python/Documents/demo/chat/server.py --port=8003
directory=/home/python/Documents/demo/chat
user=python
autorestart=true
redirect_stderr=true
stdout_logfile=/home/python/tornado.log
loglevel=info


# 启动
supervisord -c /etc/supervisord.conf
# 查看 supervisord 是否在运行：
ps aux | grep supervisord


# supervisorctl
# 可以利用supervisorctl来管理supervisor。
supervisorctl

> status    # 查看程序状态
> stop tornadoes:*   # 关闭 tornadoes组 程序
> start tornadoes:*  # 启动 tornadoes组 程序
> restart tornadoes:*    # 重启 tornadoes组 程序
> update    ＃ 重启配置文件修改过的程序

# 执行status命令时，显示如下信息说明tornado程序运行正常：
supervisor> status
tornadoes:tornado-8000 RUNNING pid 32091, uptime 00:00:02
tornadoes:tornado-8001 RUNNING pid 32092, uptime 00:00:02
tornadoes:tornado-8002 RUNNING pid 32093, uptime 00:00:02
tornadoes:tornado-8003 RUNNING pid 32094, uptime 00:00:02
            

            
# 2. nginx
# 对于使用ubuntu apt-get 安装nginx，其配置文件位于/etc/nginx/sites-available中，修改default文件如下：
upstream tornadoes {
    server 127.0.0.1:8000;
    server 127.0.0.1:8001;
    server 127.0.0.1:8002;
    server 127.0.0.1:8003;
}

upstream websocket {
    server 127.0.0.1:8000;
}

server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name _;
    location /static/ {
        root /home/python/Documents/demo/chat;
        if ($query_string) {
            expires max;
        }
    }

    location /chat {
        proxy_pass http://websocket/chat;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    location / {
        proxy_pass_header Server;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Scheme $scheme;  # 协议 http https
        proxy_pass http://tornadoes;
    }
}

# 启动nginx
service nginx start   # 启动
service nginx stop    # 停止
service nginx restart # 重启


# 源码安装版本
启动：sudo sbin/nginx
停止：sudo sbin/nginx -s stop
重启：sudo sbin/nginx -s reload
```

## 十二  Shell与运维

### 196. 什么是运维

```python
# IDC--（Internet Data Center)互联网数据中心，主要服务包括整机租用、服务器托管、机柜租用、机房租用、专线接入和网络管理服务等。广义上的IDC业务，实际上就是数据中心所提供的一切服务。客户租用数据中心的服务器和带宽，并利用数据中心的技术力量，来实现自己对软、硬件的要求，搭建自己的互联网平台，享用数据中心所提供的一系列服务。
# ISP--(Internet Service Provider)互联网服务提供商，即向广大用户综合提供互联网接入业务、信息业务、和增值业务的电信运营商。
# ICP--(Internet Content Provider)互联网内容提供商，向广大用户综合提供互联网信息业务和增值业务的电信运营商。 根据中华人民共和国国务院令第292号《互联网信息服务管理办法》规定，国家对提供互联网信息服务的ICP实行许可证制度。从而，ICP证成为网站经营的许可证，经营性网站必须办理ICP证，否则就属于非法经营。因此，办理ICP证是企业网站合法经营的需要.
# CDN--(Content Delivery Network)内容分发网络,依靠部署在各地的边缘服务器，通过中心平台的负载均衡、内容分发、调度等功能模块，使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。CDN的关键技术主要有内容存储和分发技术。 CDN的基本原理是广泛采用各种缓存服务器，将这些缓存服务器分布到用户访问相对集中的地区或网络中，在用户访问网站时，利用全局负载技术将用户的访问指向距离最近的工作正常的缓存服务器上，由缓存服务器直接响应用户请求。
# LVS--(Linux Virtual Server)的简写，意即Linux虚拟服务器，是一个虚拟的服务器集群系统。LVS集群采用IP负载均衡技术和基于内容请求分发技术。调度器具有很好的吞吐率，将请求均衡地转移到不同的服务器上执行，且调度器自动屏蔽掉服务器的故障，从而将一组服务器构成一个高性能的、高可用的虚拟服务器。整个服务器集群的结构对客户是透明的，而且无需修改客户端和服务器端的程序。为此，在设计时需要考虑系统的透明性、可伸缩性、高可用性和易管理性。
# CGI--(Common Gateway Interface)通用网关接口。CGI规范允许Web服务器执行外部程序，并将它们的输出发送给Web浏览器，CGI将Web的一组简单的静态超媒体文档变成一个完整的新的交互式媒体

# GSLB--(Global Server Load Balance，全局负载均衡）作为 CDN 系统架构中最核心的部分，负责流量调度.基于DNS的GSLB 绝大部分使用负载均衡技术的应用都通过域名来访问目的主机，在用户发出任何应用连接请求时，首先必须通过DNS请求获得服务器的IP地址，基于DNS的GSLB正是在返回DNS解析结果的过程中进行智能决策，给用户返回一个最佳的服务IP。用户应用流程与没有GSLB时未发生任何变化。这也是市场上主流的GSLB技术。

# BOSS--(Business & Operation Support System，BOSS)是业务运营支撑系统。通常所说的BOSS分为四个部分：计费及结算系统、营业与账务系统、客户服务系统和决策支持系统。BOSS从业务层面来看就是一个框架，来承载业务系统、CRM系统、计费系统。实现统一框架中的纵向、横向管理。该系统最早由电信部门的计费系统发展演变而来，基本功能包括客户资料管理、产品管理、用户订购管理、计费、出帐、结算等，负责登记客户资料、管理用户订购服务的提供、实时的根据不同产品、套餐的资费标准计算业务（手机、固定电话用户通话时、点播收视、宽带流量与时间等）的消费金额，准实时及定期计算用户帐单，实时或定期结算用户各种消费费用。

# 管理:
# 配置管理
# 事件管理
# 问题管理
# 成本管理
# 容量管理
# 资源管理
# 需求管理

# 规划:
# 架构规划
# IDC规划
# 服务器规划
# ISP规划
# 预算规划

# 优化:
# 速度优化
# 成本优化
# ISP优化
# CDN优化
# 告警优化
# 故障预案
# 故障演习

# 安全:
# 漏洞扫描
# 域名劫持扫描
# 挂马扫描
# CGI扫描
# 网页篡改扫描

# 告警:
# 告警模型
# 告警故障
# 告警统计
# 告警关联
# 拨测
	#	定时curl一下某个url，有问题就告警.
	#	日志告警：5分钟Error大于xxx次告警。
	#	指标告警：cpu使用率大于xxx告警。
    
# 告警对象可以分为两种：
# 业务规则监控
# 系统可靠性监控
# 对于业务规则监控可以举一个游戏的例子。比如游戏角色在一定装备的情况下，单次打击的伤害输出应该是有一个上限，如果超过了就说明有作弊的情况。又比如斗地主游戏里一个人的连胜场次是有一定上限的，每天的胜率是有一定上限，如果超出平均值太多就可能是作弊。业务规则监控的不是硬件，也不是软件是否工作正常。而是软件是否按照业务规则实现的，是否有漏洞。也可以理解为对“正确性”的监控。

# 系统可靠性监控是最常见的监控形式，比如发现是不是服务器挂掉了，服务是不是过载了等等。对于大部分后台服务，系统可以抽象建模成这个样子：
# 监控:
    # URL监控
    # LVS监控
    # IDC监控
    # 数据库监控
    # 模块监控
    # 站点监控
    # 响应监控
# 系统/平台:
    # CDN平台
    # 静态应用平台
    # 动态应用平台
    # 点击流系统
    # 数据库平台
    # 下载平台
    # 网络健康系统
    # 经营分析系统
    # 存储平台
    # 流媒体平台
    # 质量监测系统
    # GSLB管理系统
    # BOSS系统
    # 立体监控系统
    # 自动发布系统
    # 站点分析系统
    # 统一告警系统
    # 运维工具系统
# 规范:
    # 项目立项规范
    # 运营故障分级和处罚规范
    # 重大运营故障处理流程
    # 环境一致性规范
    # 运营资源申请流程
    # IDC变更流程
    # 预算管理规范
# linux发行版本:
    # RedHat: Fedora, CentOS, Mandriva
    # SuSE: SLES, OpenSuSE
    # Debian: Ubuntu
    # Gentoo:
    # BackTrace/kali linux (黑客)
```

### 197. Shell编程

```python
# shell历史 

Shell的作用是解释执行用户的命令，用户输入一条命令，Shell就解释执行一条，这种方式称为交互式（Interactive），Shell还有一种执行命令的方式称为批处理（Batch），用户事先写一个Shell脚本（Script），其中有很多条命令，让Shell一次把这些命令执行完，而不必一条一条地敲命令。Shell脚本和编程语言很相似，也有变量和流程控制语句，但Shell脚本是解释执行的，不需要编译，Shell程序从脚本中一行一行读取并执行这些命令，相当于一个用户把脚本中的命令一行一行敲到Shell提示符下执行。

由于历史原因，UNIX系统上有很多种Shell：

1.sh（Bourne Shell）：由Steve Bourne开发，各种UNIX系统都配有sh。

2.csh（C Shell）：由Bill Joy开发，随BSD UNIX发布，它的流程控制语句很像C语言，支持很多Bourne Shell所不支持的功能：作业控制，命令历史，命令行编辑。

3.ksh（Korn Shell）：由David Korn开发，向后兼容sh的功能，并且添加了csh引入的新功能，是目前很多UNIX系统标准配置的Shell，在这些系统上/bin/sh往往是指向/bin/ksh的符号链接。

4.tcsh（TENEX C Shell）：是csh的增强版本，引入了命令补全等功能，在FreeBSD、Mac OS X等系统上替代了csh。

5.bash（Bourne Again Shell）：由GNU开发的Shell，主要目标是与POSIX标准保持一致，同时兼顾对sh的兼容，bash从csh和ksh借鉴了很多功能，是各种Linux发行版标准配置的Shell，在Linux系统上/bin/sh往往是指向/bin/bash的符号链接。虽然如此，bash和sh还是有很多不同的，一方面，bash扩展了一些命令和参数，另一方面，bash并不完全和sh兼容，有些行为并不一致，所以bash需要模拟sh的行为：当我们通过sh这个程序名启动bash时，bash可以假装自己是sh，不认扩展的命令，并且行为与sh保持一致。

6.zsh 的命令补全功能非常强大，可以补齐路径，补齐命令，补齐参数等。

vim /etc/passwd
其中最后一列显示了用户对应的shell类型

root:x:0:0:root:/root:/bin/bash
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
syslog:x:101:103::/home/syslog:/bin/false
itcast:x:1000:1000:itcast,,,:/home/itcast:/bin/bash
ftp:x:115:125:ftp daemon,,,:/srv/ftp:/bin/false
                        
用户在命令行输入命令后，一般情况下Shell会fork并exec该命令，但是Shell的内建命令例外，执行内建命令相当于调用Shell进程中的一个函数，并不创建新的进程。以前学过的cd、alias、umask、exit等命令即是内建命令，凡是用which命令查不到程序文件所在位置的命令都是内建命令，内建命令没有单独的man手册，要在man手册中查看内建命令，应该
$ man bash-builtins
如export、shift、if、eval、[、for、while等等。内建命令虽然不创建新的进程，但也会有Exit Status，通常也用0表示成功非零表示失败，虽然内建命令不创建新的进程，但执行结束后也会有一个状态码，也可以用特殊变量$?读出。
```

### 198. 执行脚本

```shell
# 编写一个简单的脚本test.sh：
#! /bin/sh
cd ..
ls
# Shell脚本中用#表示注释，相当于C语言的//注释。但如果#位于第一行开头，并且是#!（称为Shebang）则例外，它表示该脚本使用后面指定的解释器/bin/sh解释执行。如果把这个脚本文件加上可执行权限然后执行：
chmod a+x test.sh
./test.sh
# Shell会fork一个子进程并调用exec执行./test.sh这个程序，exec系统调用应该把子进程的代码段替换成./test.sh程序的代码段，并从它的_start开始执行。然而test.sh是个文本文件，根本没有代码段和_start函数，怎么办呢？其实exec还有另外一种机制，如果要执行的是一个文本文件，并且第一行用Shebang指定了解释器，则用解释器程序的代码段替换当前进程，并且从解释器的_start开始执行，而这个文本文件被当作命令行参数传给解释器。因此，执行上述脚本相当于执行程序
$ /bin/sh ./test.sh
# 以这种方式执行不需要test.sh文件具有可执行权限。

# 如果将命令行下输入的命令用()括号括起来，那么也会fork出一个子Shell执行小括号中的命令，一行中可以输入由分号;隔开的多个命令，比如：
$ (cd ..;ls -l)
# 和上面两种方法执行Shell脚本的效果是相同的，cd ..命令改变的是子Shell的PWD，而不会影响到交互式Shell。然而命令
$ cd ..;ls -l
# 则有不同的效果，cd ..命令是直接在交互式Shell下执行的，改变交互式Shell的PWD，然而这种方式相当于这样执行Shell脚本：
$ source ./test.sh
# 或者
$ . ./test.sh
# source或者.命令是Shell的内建命令，这种方式也不会创建子Shell，而是直接在交互式Shell下逐行执行脚本中的命令。
```

### 199. 基本语法--变量

```shell
按照惯例，Shell变量由全大写字母加下划线组成，有两种类型的Shell变量：

1.环境变量

环境变量可以从父进程传给子进程，因此Shell进程的环境变量可以从当前Shell进程传给fork出来的子进程。用printenv命令可以显示当前Shell进程的环境变量。

2.本地变量

# 只存在于当前Shell进程，用set命令可以显示当前Shell进程中定义的所有变量（包括本地变量和环境变量）和函数。

# 环境变量是任何进程都有的概念，而本地变量是Shell特有的概念。在Shell中，环境变量和本地变量的定义和用法相似。在Shell中定义或赋值一个变量：

itcast$ VARNAME=value
# 注意等号两边都不能有空格，否则会被Shell解释成命令和命令行参数。

# 一个变量定义后仅存在于当前Shell进程，它是本地变量，用export命令可以把本地变量导出为环境变量，定义和导出环境变量通常可以一步完成：

itcast$ export VARNAME=value
# 也可以分两步完成：

itcast$ VARNAME=value
itcast$ export VARNAME
# 用unset命令可以删除已定义的环境变量或本地变量。

itcast$ unset VARNAME
# 如果一个变量叫做VARNAME，用${VARNAME}可以表示它的值，在不引起歧义的情况下也可以用$VARNAME表示它的值。通过以下例子比较这两种表示法的不同：

itcast$ echo $SHELL
# 注意，在定义变量时不用$，取变量值时要用$。和C语言不同的是，Shell变量不需要明确定义类型，事实上Shell变量的值都是字符串，比如我们定义VAR=45，其实VAR的值是字符串45而非整数。Shell变量不需要先定义后使用，如果对一个没有定义的变量取值，则值为空字符串。
```

### 200. 基本语法--文件名代换（Globbing）：* ? []

```shell
# 这些用于匹配的字符称为通配符（Wildcard），具体如下：
# 通配符

*   # 匹配0个或多个任意字符
?   # 匹配一个任意字符
[若干字符]  # 匹配方括号中任意一个字符的一次出现

$ ls /dev/ttyS*
$ ls ch0?.doc
$ ls ch0[0-2].doc
$ ls ch[012]   [0-9].doc
# 注意，Globbing所匹配的文件名是由Shell展开的，也就是说在参数还没传给程序之前已经展开了，比如上述ls ch0[012].doc命令，如果当前目录下有ch00.doc和ch02.doc，则传给ls命令的参数实际上是这两个文件名，而不是一个匹配字符串。
```

###201. 基本语法--命令代换：`或 $()

```shell
# 由'`'反引号括起来的也是一条命令，Shell先执行该命令，然后将输出结果立刻代换到当前命令行中。例如定义一个变量存放date命令的输出：
itcast$ DATE=`date`
itcast$ echo $DATE
# 命令代换也可以用$()表示：
itcast$ DATE=$(date)
```

### 202. 基本语法--算术代换：$(())

```shell
# 用于算术计算，$(())中的Shell变量取值将转换成整数，同样含义的$[]等价例如：
itcast$ VAR=45
itcast$ echo $(($VAR+3))
# $(())中只能用+-*/和()运算符，并且只能做整数运算。

$[base#n],其中base表示进制,n按照base进制解释，后面再有运算数，按十进制解释。

echo $[2#10+11]
echo $[8#10+11]
echo $[10#10+11]
```

### 203. 基本语法--转义字符\

```shell
# 和C语言类似，\在Shell中被用作转义字符，用于去除紧跟其后的单个字符的特殊意义（回车除外），换句话说，紧跟其后的字符取字面值。例如：
itcast$ echo $SHELL
/bin/bash
itcast$ echo \$SHELL
$SHELL
itcast$ echo \\
\
# 比如创建一个文件名为“$ $”的文件可以这样：
itcast$ touch \$\ \$
# 还有一个字符虽然不具有特殊含义，但是要用它做文件名也很麻烦，就是-号。如果要创建一个文件名以-号开头的文件，这样是不行的：
itcast$ touch -hello
touch: invalid option -- h
Try `touch --help' for more information.

# 即使加上\转义也还是报错：

itcast$ touch \-hello
touch: invalid option -- h
Try `touch --help' for more information.
# 因为各种UNIX命令都把-号开头的命令行参数当作命令的选项，而不会当作文件名。如果非要处理以-号开头的文件名，可以有两种办法：
itcast$ touch ./-hello
# 或
itcast$ touch -- -hello
# \还有一种用法，在\后敲回车表示续行，Shell并不会立刻执行命令，而是把光标移到下一行，给出一个续行提示符>，等待用户继续输入，最后把所有的续行接到一起当作一个命令执行。例如：
itcast$ ls \
> -l
（ls -l命令的输出）
```

### 204. 基本语法--单引号

```shell
# 和C语言不一样，Shell脚本中的单引号和双引号一样都是字符串的界定符（双引号下一节介绍），而不是字符的界定符。单引号用于保持引号内所有字符的字面值，即使引号内的\和回车也不例外，但是字符串中不能出现单引号。如果引号没有配对就输入回车，Shell会给出续行提示符，要求用户把引号配上对。例如：
itcast$ echo '$SHELL'
$SHELL
itcast$ echo 'ABC\（回车）
> DE'（再按一次回车结束命令）
ABC\
DE
```

###205. 基本语法--双引号

```shell
# 被双引号用括住的内容，将被视为单一字串。它防止通配符扩展，但允许变量扩展。这点与单引号的处理方式不同
itcast$ DATE=$(date)
itcast$ echo "$DATE"
itcast$ echo '$DATE'
```

### 206. 脚本语法--条件测试：test [

```shell
# 命令test或[可以测试一个条件是否成立，如果测试结果为真，则该命令的Exit Status为0，如果测试结果为假，则命令的Exit Status为1（注意与C语言的逻辑表示正好相反）。例如测试两个数的大小关系：
itcast@ubuntu:~$ var=2
itcast@ubuntu:~$ test $var -gt 1
itcast@ubuntu:~$ echo $?
0
itcast@ubuntu:~$ test $var -gt 3
itcast@ubuntu:~$ echo $?
1
itcast@ubuntu:~$ [ $var -gt 3 ]
itcast@ubuntu:~$ echo $?
1
itcast@ubuntu:~$
# 虽然看起来很奇怪，但左方括号[确实是一个命令的名字，传给命令的各参数之间应该用空格隔开，比如，$VAR、-gt、3、]是[命令的四个参数，它们之间必须用空格隔开。命令test或[的参数形式是相同的，只不过test命令不需要]参数。以[命令为例，常见的测试命令如下表所示：
[ -d DIR ]              如果DIR存在并且是一个目录则为真
[ -f FILE ]             如果FILE存在且是一个普通文件则为真
[ -z STRING ]           如果STRING的长度为零则为真
[ -n STRING ]           如果STRING的长度非零则为真
[ STRING1 = STRING2 ]   如果两个字符串相同则为真
[ STRING1 != STRING2 ]  如果字符串不相同则为真
[ ARG1 OP ARG2 ]        ARG1和ARG2应该是整数或者取值为整数的变量，OP是-eq（等于）-ne（不等于）-lt（小于）-le（小于等于）-gt（大于）-ge（大于等于）之中的一个
# 和C语言类似，测试条件之间还可以做与、或、非逻辑运算：
# 带与、或、非的测试命令

[ ! EXPR ]          EXPR可以是上表中的任意一种测试条件，!表示逻辑反
[ EXPR1 -a EXPR2 ]  EXPR1和EXPR2可以是上表中的任意一种测试条件，-a表示逻辑与
[ EXPR1 -o EXPR2 ]  EXPR1和EXPR2可以是上表中的任意一种测试条件，-o表示逻辑或
# 例如:
$ VAR=abc
$ [ -d Desktop -a $VAR = 'abc' ]
$ echo $?
0
# 注意，如果上例中的$VAR变量事先没有定义，则被Shell展开为空字符串，会造成测试条件的语法错误（展开为[ -d Desktop -a = 'abc' ]），作为一种好的Shell编程习惯，应该总是把变量取值放在双引号之中（展开为[ -d Desktop -a "" = 'abc' ]）：
$ unset VAR
$ [ -d Desktop -a $VAR = 'abc' ]
bash: [: too many arguments
$ [ -d Desktop -a "$VAR" = 'abc' ]
$ echo $?
1

```

### 207. 脚本语法--if/then/elif/else/fi

```shell
# 和C语言类似，在Shell中用if、then、elif、else、fi这几条命令实现分支控制。这种流程控制语句本质上也是由若干条Shell命令组成的，例如先前讲过的
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
# 其实是三条命令，if [ -f ~/.bashrc ]是第一条，then . ~/.bashrc是第二条，fi是第三条。如果两条命令写在同一行则需要用;号隔开，一行只写一条命令就不需要写;号了，另外，then后面有换行，但这条命令没写完，Shell会自动续行，把下一行接在then后面当作一条命令处理。和[命令一样，要注意命令和各参数之间必须用空格隔开。if命令的参数组成一条子命令，如果该子命令的Exit Status为0（表示真），则执行then后面的子命令，如果Exit Status非0（表示假），则执行elif、else或者fi后面的子命令。if后面的子命令通常是测试命令，但也可以是其它命令。Shell脚本没有{}括号，所以用fi表示if语句块的结束。见下例：
#! /bin/sh

if [ -f /bin/bash ]
then echo "/bin/bash is a file"
else echo "/bin/bash is NOT a file"
fi
if :; then echo "always true"; fi
# :是一个特殊的命令，称为空命令，该命令不做任何事，但Exit Status总是真。此外，也可以执行/bin/true或/bin/false得到真或假的Exit Status。再看一个例子：
#! /bin/sh

    echo "Is it morning? Please answer yes or no."
    read YES_OR_NO
    if [ "$YES_OR_NO" = "yes" ]; then
      echo "Good morning!"
    elif [ "$YES_OR_NO" = "no" ]; then
      echo "Good afternoon!"
    else
      echo "Sorry, $YES_OR_NO not recognized. Enter yes or no."
      exit 1
    fi
    exit 0
# 上例中的read命令的作用是等待用户输入一行字符串，将该字符串存到一个Shell变量中。
# 此外，Shell还提供了&&和||语法，和C语言类似，具有Short-circuit特性，很多Shell脚本喜欢写成这样：
test "$(whoami)" != 'root' && (echo you are using a non-privileged account; exit 1)
# &&相当于“if...then...”，而||相当于“if not...then...”。&&和||用于连接两个命令，而上面讲的-a和-o仅用于在测试表达式中连接两个测试条件，要注意它们的区别，例如，
test "$VAR" -gt 1 -a "$VAR" -lt 3
# 和以下写法是等价的
test "$VAR" -gt 1 && test "$VAR" -lt 3
```

### 208. 脚本语法--case/esac

```shell
# case命令可类比C语言的switch/case语句，esac表示case语句块的结束。C语言的case只能匹配整型或字符型常量表达式，而Shell脚本的case可以匹配字符串和Wildcard，每个匹配分支可以有若干条命令，末尾必须以;;结束，执行时找到第一个匹配的分支并执行相应的命令，然后直接跳到esac之后，不需要像C语言一样用break跳出。
#! /bin/sh

    echo "Is it morning? Please answer yes or no."
    read YES_OR_NO
    case "$YES_OR_NO" in
    yes|y|Yes|YES)
      echo "Good Morning!";;
    [nN]*)
      echo "Good Afternoon!";;
    *)
      echo "Sorry, $YES_OR_NO not recognized. Enter yes or no."
      exit 1;;
    esac
    exit 0
# 使用case语句的例子可以在系统服务的脚本目录/etc/init.d中找到。这个目录下的脚本大多具有这种形式（以/etc/init.d/nfs-kernel-server为例）：
   case "$1" in
        start)
            ...
        ;;
        stop)
            ...
        ;;
        reload | force-reload)
            ...
        ;;
        restart)
        ...
        *)
            log_success_msg "Usage: nfs-kernel-server {start|stop|status|reload|force-reload|restart}"
            exit 1
        ;;
    esac
# 启动nfs-kernel-server服务的命令是
$ sudo /etc/init.d/nfs-kernel-server start
# $1是一个特殊变量，在执行脚本时自动取值为第一个命令行参数，也就是start，所以进入start)分支执行相关的命令。同理，命令行参数指定为stop、reload或restart可以进入其它分支执行停止服务、重新加载配置文件或重新启动服务的相关命令。
```

###209. 脚本语法--for/do/done

```shell
# Shell脚本的for循环结构和C语言很不一样，它类似于某些编程语言的foreach循环。例如：
#! /bin/sh

    for FRUIT in apple banana pear; do
      echo "I like $FRUIT"
    done
# FRUIT是一个循环变量，第一次循环$FRUIT的取值是apple，第二次取值是banana，第三次取值是pear。再比如，要将当前目录下的chap0、chap1、chap2等文件名改为chap0~、chap1~、chap2~等（按惯例，末尾有~字符的文件名表示临时文件），这个命令可以这样写：

$ for FILENAME in chap?; do mv $FILENAME $FILENAME~; done   
# 也可以这样写：
$ for FILENAME in `ls chap?`; do mv $FILENAME $FILENAME~; done
```

### 210. 脚本语法--while/do/done

```shell
# while的用法和C语言类似。比如一个验证密码的脚本：
#! /bin/sh

    echo "Enter password:"
    read TRY
    while [ "$TRY" != "secret" ]; do
      echo "Sorry, try again"
      read TRY
    done
# 下面的例子通过算术运算控制循环的次数：
#! /bin/sh

    COUNTER=1
    while [ "$COUNTER" -lt 10 ]; do
      echo "Here we go again"
      COUNTER=$(($COUNTER+1))
    done
    
```

### 211. 脚本语法--break和continue

```shell
# break[n]可以指定跳出几层循环，continue跳过本次循环步，没跳出整个循环。

# break跳出，continue跳过。
```

### 212. 脚本语法--位置参数和特殊变量

```shell
# 常用的位置参数和特殊变量
$0  相当于C语言main函数的argv[0]
$1、$2...    这些称为位置参数（Positional Parameter），相当于C语言main函数的argv[1]、argv[2]...
$#  相当于C语言main函数的argc - 1，注意这里的#后面不表示注释
$@  表示参数列表"$1" "$2" ...，例如可以用在for循环中的in后面。
$*  表示参数列表"$1" "$2" ...，同上
$?  上一条命令的Exit Status
$$  当前进程号
# 位置参数可以用shift命令左移。比如shift 3表示原来的$4现在变成$1，原来的$5现在变成$2等等，原来的$1、$2、$3丢弃，$0不移动。不带参数的shift命令相当于shift 1。例如：
#! /bin/sh

    echo "The program $0 is now running"
    echo "The first parameter is $1"
    echo "The second parameter is $2"
    echo "The parameter list is $@"
    shift
    echo "The first parameter is $1"
    echo "The second parameter is $2"
    echo "The parameter list is $@"
```

###213. 脚本语法--echo

```shell
# echo显示文本行或变量，或者把字符串输入到文件。
echo [option] string
-e 解析转义字符
-n 不回车换行。默认情况echo回显的内容后面跟一个回车换行。
echo "hello\n\n"
echo -e "hello\n\n"
echo  "hello"
echo -n "hello"
```

### 214. 脚本语法--管道|

```shell
# 可以通过管道把一个命令的输出传递给另一个命令做输入。管道用竖线表示。
cat myfile | more
ls -l | grep "myfile"
df -k | awk '{print $1}' | grep -v "文件系统"
df -k 查看磁盘空间，找到第一列，去除“文件系统”，并输出
```

###215. 脚本语法--tee

```shell
# tee命令把结果输出到标准输出，另一个副本输出到相应文件。
df -k | awk '{print $1}' | grep -v "文件系统" | tee a.txt

tee -a a.txt表示追加操作。
df -k | awk '{print $1}' | grep -v "文件系统" | tee -a a.txt
```

### 216. 脚本语法--文件重定向

```shell 
cmd > file             把标准输出重定向到新文件中
cmd >> file            追加
cmd > file 2>&1        标准出错也重定向到1所指向的file里
cmd >> file 2>&1
cmd < file1 > file2    输入输出都定向到文件里
cmd < &fd              把文件描述符fd作为标准输入
cmd > &fd              把文件描述符fd作为标准输出
cmd < &-               关闭标准输入
```

###217.  脚本语法--函数

```shell
# 和C语言类似，Shell中也有函数的概念，但是函数定义中没有返回值也没有参数列表。例如：
#! /bin/sh

    foo(){ echo "Function foo is called";}
    echo "-=start=-"
    foo
    echo "-=end=-"

# 注意函数体的左花括号'{'和后面的命令之间必须有空格或换行，如果将最后一条命令和右花括号'}'写在同一行，命令末尾必须有;号。

# 在定义foo()函数时并不执行函数体中的命令，就像定义变量一样，只是给foo这个名字一个定义，到后面调用foo函数的时候（注意Shell中的函数调用不写括号）才执行函数体中的命令。Shell脚本中的函数必须先定义后调用，一般把函数定义都写在脚本的前面，把函数调用和其它命令写在脚本的最后（类似C语言中的main函数，这才是整个脚本实际开始执行命令的地方）。

# Shell函数没有参数列表并不表示不能传参数，事实上，函数就像是迷你脚本，调用函数时可以传任意个参数，在函数内同样是用$0、$1、$2等变量来提取参数，函数中的位置参数相当于函数的局部变量，改变这些变量并不会影响函数外面的$0、$1、$2等变量。函数中可以用return命令返回，如果return后面跟一个数字则表示函数的Exit Status。

# 下面这个脚本可以一次创建多个目录，各目录名通过命令行参数传入，脚本逐个测试各目录是否存在，如果目录不存在，首先打印信息然后试着创建该目录。
#! /bin/sh

    is_directory()
    {
      DIR_NAME=$1
      if [ ! -d $DIR_NAME ]; then
        return 1
      else
        return 0
      fi
    }

    for DIR in "$@"; do
      if is_directory "$DIR"
      then :
      else
        echo "$DIR doesn't exist. Creating it now..."
        mkdir $DIR > /dev/null 2>&1
        if [ $? -ne 0 ]; then
          echo "Cannot create directory $DIR"
          exit 1
        fi
      fi
    done

# 注意is_directory()返回0表示真返回1表示假。
```

### 218. 调试方法

```shell 
# Shell提供了一些用于调试脚本的选项，如下所示：

-n

# 读一遍脚本中的命令但不执行，用于检查脚本中的语法错误

-v

# 一边执行脚本，一边将执行过的脚本命令打印到标准错误输出

-x

# 提供跟踪执行信息，将执行的每一条命令和结果依次打印出来

# 使用这些选项有三种方法，一是在命令行提供参数

    $ sh -x ./script.sh
# 二是在脚本开头提供参数

    #! /bin/sh -x
# 第三种方法是在脚本中用set命令启用或禁用参数

    #! /bin/sh
    if [ -z "$1" ]; then
      set -x
      echo "ERROR: Insufficient Args."
      exit 1
      set +x
    fi
# set -x和set +x分别表示启用和禁用-x参数，这样可以只对脚本中的某一段进行跟踪调试。
```

###219. 正则表达式

```shell
# 以前用grep在一个文件中找出包含某些字符串的行，比如在头文件中找出一个宏定义。其实grep还可以找出符合某个模式（Pattern）的一类字符串。例如找出所有符合xxxxx@xxxx.xxx模式的字符串（也就是email地址），要求x字符可以是字母、数字、下划线、小数点或减号，email地址的每一部分可以有一个或多个x字符，例如abc.d@ef.com、1_2@987-6.54，当然符合这个模式的不全是合法的email地址，但至少可以做一次初步筛选，筛掉a.b、c@d等肯定不是email地址的字符串。再比如，找出所有符合yyy.yyy.yyy.yyy模式的字符串（也就是IP地址），要求y是0-9的数字，IP地址的每一部分可以有1-3个y字符。

# 如果要用grep查找一个模式，如何表示这个模式，这一类字符串，而不是一个特定的字符串呢？从这两个简单的例子可以看出，要表示一个模式至少应该包含以下信息：

# 字符类（Character Class）：如上例的x和y，它们在模式中表示一个字符，但是取值范围是一类字符中的任意一个。

# 数量限定符（Quantifier）： 邮件地址的每一部分可以有一个或多个x字符，IP地址的每一部分可以有1-3个y字符

# 各种字符类以及普通字符之间的位置关系：例如邮件地址分三部分，用普通字符@和.隔开，IP地址分四部分，用.隔开，每一部分都可以用字符类和数量限定符描述。为了表示位置关系，还有位置限定符（Anchor）的概念，将在下面介绍。

# 规定一些特殊语法表示字符类、数量限定符和位置关系，然后用这些特殊语法和普通字符一起表示一个模式，这就是正则表达式（Regular Expression）。例如email地址的正则表达式可以写成[a-zA-Z0-9_.-]+@[a-zA-Z0-9_.-]+.[a-zA-Z0-9_.-]+，IP地址的正则表达式可以写成[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}。下一节介绍正则表达式的语法，我们先看看正则表达式在grep中怎么用。例如有这样一个文本文件testfile：

192.168.1.1
1234.234.04.5678
123.4234.045.678
abcde
# 查找其中包含IP地址的行：

$ egrep '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' testfile
192.168.1.1
1234.234.04.5678
# egrep相当于grep -E，表示采用Extended正则表达式语法。grep的正则表达式有Basic和Extended两种规范，它们之间的区别下一节再解释。另外还有fgrep命令，相当于grep -F，表示只搜索固定字符串而不搜索正则表达式模式，不会按正则表达式的语法解释后面的参数。

# 注意正则表达式参数用单引号括起来了，因为正则表达式中用到的很多特殊字符在Shell中也有特殊含义（例如\），只有用单引号括起来才能保证这些字符原封不动地传给grep命令，而不会被Shell解释掉。

# 192.168.1.1符合上述模式，由三个.隔开的四段组成，每段都是1到3个数字，所以这一行被找出来了，可为什么1234.234.04.5678也被找出来了呢？因为grep找的是包含某一模式的行，这一行包含一个符合模式的字符串234.234.04.567。相反，123.4234.045.678这一行不包含符合模式的字符串，所以不会被找出来。

# grep是一种查找过滤工具，正则表达式在grep中用来查找符合模式的字符串。其实正则表达式还有一个重要的应用是验证用户输入是否合法，例如用户通过网页表单提交自己的email地址，就需要用程序验证一下是不是合法的email地址，这个工作可以在网页的Javascript中做，也可以在网站后台的程序中做，例如PHP、Perl、Python、Ruby、Java或C，所有这些语言都支持正则表达式，可以说，目前不支持正则表达式的编程语言实在很少见。除了编程语言之外，很多UNIX命令和工具也都支持正则表达式，例如grep、vi、sed、awk、emacs等等。“正则表达式”就像“变量”一样，它是一个广泛的概念，而不是某一种工具或编程语言的特性。


# 基本语法
# 字符类:
字符  含义               举例
.   匹配任意一个字符          abc.可以匹配abcd、abc9等
[]  匹配括号中的任意一个字符  [abc]d可以匹配ad、bd或cd
-   在[]括号内表示字符范围    [0-9a-fA-F]可以匹配一位十六进制数字
^   位于[]括号内的开头，匹配除括号中的字符之外的任意一个字符  [^xy]匹配除xy之外的任一字符，因此[^xy]1可以匹配a1、b1但不匹配x1、y1

[[:xxx:]]   grep工具预定义的一些命名字符类   [[:alpha:]]匹配一个字母，[[:digit:]]匹配一个数字
# 数量限定符:
字符    含义                             举例
?   紧跟在它前面的单元应匹配零次或一次    [0-9]?\.[0-9]匹配0.0、2.3、.5等，由于.在正则表达式中是一个特殊字符，所以需要用\转义一下，取字面值
+   紧跟在它前面的单元应匹配一次或多次    [a-zA-Z0-9_.-]+@[a-zA-Z0-9_.-]+\.[a-zA-Z0-9_.-]+匹配email地址
*   紧跟在它前面的单元应匹配零次或多次    [0-9][0-9]*匹配至少一位数字，等价于[0-9]+，[a-zA-Z_]+[a-zA-Z_0-9]*匹配C语言的标识符
{N} 紧跟在它前面的单元应精确匹配N次       [1-9][0-9]{2}匹配从100到999的整数
{N,}  紧跟在它前面的单元应匹配至少N次     [1-9][0-9]{2,}匹配三位以上（含三位）的整数
{,M}  紧跟在它前面的单元应匹配最多M次     [0-9]{,1}相当于[0-9]?
{N,M} 紧跟在它前面的单元应匹配至少N次，最多M次   [0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}匹配IP地址
# 再次注意grep找的是包含某一模式的行，而不是完全匹配某一模式的行。再举个例子，如果文本文件的内容是
aaabc
aad
efg
# 查找a*这个模式的结果是三行都被找出来了
$ egrep 'a*' testfile 
aabc
aad
efg
# a匹配0个或多个a，而第三行包含0个a，所以也包含了这一模式。单独用a这样的正则表达式做查找没什么意义，一般是把a*作为正则表达式的一部分来用。
# 位置限定符:
字符  含义                举例
    ^   匹配行首的位置        ^Content匹配位于一行开头的Content
    $   匹配行末的位置        ;$匹配位于一行结尾的;号，^$匹配空行
    \<  匹配单词开头的位置    \<th匹配... this，但不匹配ethernet、tenth
    \>  匹配单词结尾的位置    p\>匹配leap ...，但不匹配parent、sleepy
    \b  匹配单词开头或结尾的位置     \bat\b匹配... at ...，但不匹配cat、atexit、batch
    \B  匹配非单词开头和结尾的位置   \Bat\B匹配battery，但不匹配... attend、hat ...
# 位置限定符可以帮助grep更准确地查找，例如上一节我们用[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}查找IP地址，找到这两行
192.168.1.1
1234.234.04.5678
# 如果用^[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}$查找，就可以把1234.234.04.5678这一行过滤掉了。
# 其它特殊字符
字符  含义    举例
\    转义字符，普通字符转义为特殊字符，特殊字符转义为普通字符   普通字符<写成\<表示单词开头的位置，特殊字符.写成\.以及\写成\\就当作普通字符来匹配
()   将正则表达式的一部分括起来组成一个单元，可以对整个单元使用数量限定符    ([0-9]{1,3}\.){3}[0-9]{1,3}匹配IP地址
|    连接两个子表达式，表示或的关系     n(o|either)匹配no或neither
# 以上介绍的是grep正则表达式的Extended规范，Basic规范也有这些语法，只是字符?+{}|()应解释为普通字符，要表示上述特殊含义则需要加\转义。如果用grep而不是egrep，并且不加-E参数，则应该遵照Basic规范来写正则表达式。
```

### 220. grep

```shell 
# 1.作用

# Linux系统中grep命令是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹 配的行打印出来。grep全称是Global Regular Expression Print，表示全局正则表达式版本，它的使用权限是所有用户。

# grep家族包括grep、egrep和fgrep。egrep和fgrep的命令只跟grep有很小不同。egrep是grep的扩展，支持更多的re元字符， fgrep就是fixed grep或fast grep，它们把所有的字母都看作单词，也就是说，正则表达式中的元字符表示回其自身的字面意义，不再特殊。linux使用GNU版本的grep。它功能更强，可以通过-G、-E、-F命令行选项来使用egrep和fgrep的功能。

# 2.格式

grep [options]
# 3.主要参数

grep --help

# [options]主要参数：
-c：只输出匹配行的计数。
-i：不区分大小写。
-h：查询多文件时不显示文件名。
-l：查询多文件时只输出包含匹配字符的文件名。
-n：显示匹配行及 行号。
-s：不显示不存在或无匹配文本的错误信息。
-v：显示不包含匹配文本的所有行。
--color=auto ：可以将找到的关键词部分加上颜色的显示。
# pattern正则表达式主要参数：

\： 忽略正则表达式中特殊字符的原有含义。
^：匹配正则表达式的开始行。
$: 匹配正则表达式的结束行。
\<：从匹配正则表达 式的行开始。
\>：到匹配正则表达式的行结束。
[ ]：单个字符，如[A]即A符合要求 。
[ - ]：范围，如[A-Z]，即A、B、C一直到Z都符合要求 。
.：所有的单个字符。
*：有字符，长度可以为0。
4.grep命令使用简单实例

$ grep ‘test’ d*
# 显示所有以d开头的文件中包含 test的行。

$ grep ‘test’ aa bb cc
# 显示在aa，bb，cc文件中匹配test的行。

$ grep ‘[a-z]\{5\}’ aa
# 显示所有包含每个字符串至少有5个连续小写字符的字符串的行。

$ grep ‘w\(es\)t.*\1′ aa
# 如果west被匹配，则es就被存储到内存中，并标记为1，然后搜索任意个字符(.*)，这些字符后面紧跟着 另外一个es(\1)，找到就显示该行。如果用egrep或grep -E，就不用”\”号进行转义，直接写成’w(es)t.*\1′就可以了。
# 5.grep命令使用复杂实例

# 明确要求搜索子目录：

grep -r
# 或忽略子目录：

grep -d skip
# 如果有很多输出时，您可以通过管道将其转到’less’上阅读：

$ grep magic /usr/src/Linux/Documentation/* | less
# 这样，您就可以更方便地阅读。

# 有一点要注意，您必需提供一个文件过滤方式(搜索全部文件的话用 *)。如果您忘了，’grep’会一直等着，直到该程序被中断。如果您遇到了这样的情况，按 ，然后再试。

# 下面还有一些有意思的命令行参数：

grep -i pattern files ：不区分大小写地搜索。默认情况区分大小写，
grep -l pattern files ：只列出匹配的文件名，
grep -L pattern files ：列出不匹配的文件名，
grep -w pattern files ：只匹配整个单词，而不是字符串的一部分(如匹配’magic’，而不是’magical’)，
grep -C number pattern files ：匹配的上下文分别显示[number]行，
grep pattern1 | pattern2 files ：显示匹配 pattern1 或 pattern2 的行，
例如：grep "abc\|xyz" testfile    表示过滤包含abc或xyz的行
grep pattern1 files | grep pattern2 ：显示既匹配 pattern1 又匹配 pattern2 的行。

grep -n pattern files  即可显示行号信息

grep -c pattern files  即可查找总行数
# 还有些用于搜索的特殊符号：

\< 和 \> 分别标注单词的开始与结尾。
例如：
grep man * 会匹配 ‘Batman’、’manic’、’man’等，
grep ‘\<man’ * 匹配’manic’和’man’，但不是’Batman’，
grep ‘\<man\>’ 只匹配’man’，而不是’Batman’或’manic’等其他的字符串。
‘^’：指匹配的字符串在行首，
‘$’：指匹配的字符串在行 尾，
```

###221. find

```shell
# 由于find具有强大的功能，所以它的选项也很多，其中大部分选项都值得我们花时间来了解一下。即使系统中含有网络文件系统( NFS)，find命令在该文件系统中同样有效，只要你具有相应的权限。

# 在运行一个非常消耗资源的find命令时，很多人都倾向于把它放在后台执行，因为遍历一个大的文件系统可能会花费很长的时间(这里是指30G字节以上的文件系统)。

# 一、find 命令格式

# 1、find命令的一般形式为；

find pathname -options [-print -exec -ok ...]
# 2、find命令的参数；

pathname: find命令所查找的目录路径。例如用.来表示当前目录，用/来表示系统根目录，递归查找。
-print： find命令将匹配的文件输出到标准输出。
-exec： find命令对匹配的文件执行该参数所给出的shell命令。相应命令的形式为'command' {  } \;，注意{   }和\；之间的空格。
-ok： 和-exec的作用相同，只不过以一种更为安全的模式来执行该参数所给出的shell命令，在执行每一个命令之前，都会给出提示，让用户来确定是否执行。
# 3、find命令选项

-name   按照文件名查找文件。
-perm   按照文件权限来查找文件。
-prune  使用这一选项可以使find命令不在当前指定的目录中查找，如果同时使用-depth选项，那么-prune将被find命令忽略。
-user   按照文件属主来查找文件。
-group  按照文件所属的组来查找文件。
-mtime -n +n 按照文件的更改时间来查找文件，-n表示文件更改时间距现在n天以内，+n表示文件更改时间距现在n天以前。find命令还有-atime和-ctime 选项，但它们都和-m time选项。
-nogroup 查找无有效所属组的文件，即该文件所属的组在/etc/groups中不存在。
-nouser 查找无有效属主的文件，即该文件的属主在/etc/passwd中不存在。
-newer file1 ! file2 查找更改时间比文件file1新但比文件file2旧的文件。
-type   查找某一类型的文件，诸如：
    b - 块设备文件。
    d - 目录。
    c - 字符设备文件。
    p - 管道文件。
    l - 符号链接文件。
    f - 普通文件。
-size n：[c] 查找文件长度为n块的文件，带有c时表示文件长度以字节计。
-depth   在查找文件时，首先查找当前目录中的文件，然后再在其子目录中查找。
-fstype  查找位于某一类型文件系统中的文件，这些文件系统类型通常可以在配置文件/etc/fstab中找到，该配置文件中包含了本系统中有关文件系统的信息。
-mount   在查找文件时不跨越文件系统mount点。
-follow  如果find命令遇到符号链接文件，就跟踪至链接所指向的文件。
另外,下面三个的区别:

-amin n   查找系统中最后N分钟访问的文件
-atime n  查找系统中最后n*24小时访问的文件
-cmin n   查找系统中最后N分钟被改变文件状态的文件
-ctime n  查找系统中最后n*24小时被改变文件状态的文件
-mmin n   查找系统中最后N分钟被改变文件数据的文件
-mtime n  查找系统中最后n*24小时被改变文件数据的文件
# 4、使用exec或ok来执行shell命令

# 使用find时，只要把想要的操作写在一个文件里，就可以用exec来配合find查找，很方便 在有些操作系统中只允许-exec选项执行诸如ls或ls -l这样的命令。大多数用户使用这一选项是为了查找旧文件并删除它们。建议在真正执行rm命令删除文件之前，最好先用ls命令看一下，确认它们是所要删除的文件。

# exec选项后面跟随着所要执行的命令或脚本，然后是一对儿{}，一个空格和一个\，最后是一个分号。为了使用exec选项，必须要同时使用print选项。如果验证一下find命令，会发现该命令只输出从当前路径起的相对路径及文件名。

# 例如：为了用ls -l命令列出所匹配到的文件，可以把ls -l命令放在find命令的-exec选项中

# find . -type f -exec ls -l {} \;
# 上面的例子中，find命令匹配到了当前目录下的所有普通文件，并在-exec选项中使用ls -l命令将它们列出。

# 在/logs目录中查找更改时间在5日以前的文件并删除它们：

$ find logs -type f -mtime +5 -exec rm {} \;
# 记住：在shell中用任何方式删除文件之前，应当先查看相应的文件，一定要小心！当使用诸如mv或rm命令时，可以使用-exec选项的安全模式。它将在对每个匹配到的文件进行操作之前提示你。

# 在下面的例子中， find命令在当前目录中查找所有文件名以.LOG结尾、更改时间在5日以上的文件，并删除它们，只不过在删除之前先给出提示。

$ find . -name "*.conf"  -mtime +5 -ok rm {  } \;
< rm ... ./conf/httpd.conf > ? n
# 按y键删除文件，按n键不删除。

# 任何形式的命令都可以在-exec选项中使用。

# 在下面的例子中我们使用grep命令。find命令首先匹配所有文件名为“ passwd*”的文件，例如passwd、passwd.old、passwd.bak，然后执行grep命令看看在这些文件中是否存在一个itcast用户。

# find /etc -name "passwd*" -exec grep "itcast" {  } \;

itcast:x:1000:1000::/home/itcast:/bin/bash
# 选项详解

# 1.使用name选项

# 文件名选项是find命令最常用的选项，要么单独使用该选项，要么和其他选项一起使用。

# 可以使用某种文件名模式来匹配文件，记住要用引号将文件名模式引起来。

# 不管当前路径是什么，如果想要在自己的根目录$HOME中查找文件名符合*.txt的文件，使用~作为 'pathname'参数，波浪号~代表了你的$HOME目录。

$ find ~ -name "*.txt" -print
# 想要在当前目录及子目录中查找所有的‘ *.txt’文件，可以用：

$ find . -name "*.txt" -print
# 想要的当前目录及子目录中查找文件名以一个大写字母开头的文件，可以用：

$ find . -name "[A-Z]*" -print
# 想要在/etc目录中查找文件名以host开头的文件，可以用：

$ find /etc -name "host*" -print
# 想要查找$HOME目录中的文件，可以用：

$ find ~ -name "*" -print 或find . -print
# 要想让系统高负荷运行，就从根目录开始查找所有的文件：

$ find / -name "*" -print
# 如果想在当前目录查找文件名以两个小写字母开头，跟着是两个数字，最后是.txt的文件，下面的命令就能够返回例如名为ax37.txt的文件：

$find . -name "[a-z][a-z][0-9][0-9].txt" -print
# 2、用perm选项

# 按照文件权限模式用-perm选项,按文件权限模式来查找文件的话。最好使用八进制的权限表示法。

# 如在当前目录下查找文件权限位为755的文件，即文件属主可以读、写、执行，其他用户可以读、执行的文件，可以用：

$ find . -perm 755 -print
# 还有一种表达方法：在八进制数字前面要加一个横杠-，表示都匹配，如-007就相当于777，-006相当于666

# ls -l
# find . -perm 006
# find . -perm -006

-perm mode:文件许可正好符合mode
-perm +mode:文件许可部分符合mode
-perm -mode: 文件许可完全符合mode
# 3、忽略某个目录

# 如果在查找文件时希望忽略某个目录，因为你知道那个目录中没有你所要查找的文件，那么可以使用-prune选项来指出需要忽略的目录。在使用-prune选项时要当心，因为如果你同时使用了-depth选项，那么-prune选项就会被find命令忽略。

# 如果希望在/apps目录下查找文件，但不希望在/apps/bin目录下查找，可以用：

$ find /apps -path "/apps/bin" -prune -o -print
# 4、使用find查找文件的时候怎么避开某个文件目录

# 比如要在/home/itcast目录下查找不在dir1子目录之内的所有文件

find /home/itcast -path "/home/itcast/dir1" -prune -o -print
# 避开多个文件夹

find /home \( -path /home/itcast/f1 -o -path /home/itcast/f2 \) -prune -o -print
注意(前的\,注意(后的空格。

# 5、使用user和nouser选项

# 按文件属主查找文件，如在$HOME目录中查找文件属主为itcast的文件，可以用：

$ find ~ -user itcast -print
# 在/etc目录下查找文件属主为uucp的文件：

$ find /etc -user uucp -print
# 为了查找属主帐户已经被删除的文件，可以使用-nouser选项。这样就能够找到那些属主在/etc/passwd文件中没有有效帐户的文件。在使用-nouser选项时，不必给出用户名； find命令能够为你完成相应的工作。

# 例如，希望在/home目录下查找所有的这类文件，可以用：

$ find /home -nouser -print
# 6、使用group和nogroup选项

# 就像user和nouser选项一样，针对文件所属于的用户组， find命令也具有同样的选项，为了在/apps目录下查找属于itcast用户组的文件，可以用：

$ find /apps -group itcast -print
# 要查找没有有效所属用户组的所有文件，可以使用nogroup选项。下面的find命令从文件系统的根目录处查找这样的文件

$ find / -nogroup -print
# 7、按照更改时间或访问时间等查找文件

# 如果希望按照更改时间来查找文件，可以使用mtime,atime或ctime选项。如果系统突然没有可用空间了，很有可能某一个文件的长度在此期间增长迅速，这时就可以用mtime选项来查找这样的文件。

# 用减号-来限定更改时间在距今n日以内的文件，而用加号+来限定更改时间在距今n日以前的文件。

# 希望在系统根目录下查找更改时间在5日以内的文件，可以用：

$ find / -mtime -5 -print
# 为了在/var/adm目录下查找更改时间在3日以前的文件，可以用：

$ find /var/adm -mtime +3 -print
# 8、查找比某个文件新或旧的文件

# 如果希望查找更改时间比某个文件新但比另一个文件旧的所有文件，可以使用-newer选项。它的一般形式为：

newest_file_name ! oldest_file_name

# 其中，！是逻辑非符号。
# 9、使用type选项

# 在/etc目录下查找所有的目录，可以用：

$ find /etc -type d -print
# 在当前目录下查找除目录以外的所有类型的文件，可以用：

$ find . ! -type d -print
# 在/etc目录下查找所有的符号链接文件，可以用

$ find /etc -type l -print
# 10、使用size选项

# 可以按照文件长度来查找文件，这里所指的文件长度既可以用块（block）来计量，也可以用字节来计量。以字节计量文件长度的表达形式为N c；以块计量文件长度只用数字表示即可。

# 在按照文件长度查找文件时，一般使用这种以字节表示的文件长度，在查看文件系统的大小，因为这时使用块来计量更容易转换。 在当前目录下查找文件长度大于1 M字节的文件：

$ find . -size +1000000c -print
# 在/home/apache目录下查找文件长度恰好为100字节的文件：

$ find /home/apache -size 100c -print
# 在当前目录下查找长度超过10块的文件（一块等于512字节）：

$ find . -size +10 -print
# 11、使用depth选项

# 在使用find命令时，可能希望先匹配所有的文件，再在子目录中查找。使用depth选项就可以使find命令这样做。这样做的一个原因就是，当在使用find命令向磁带上备份文件系统时，希望首先备份所有的文件，其次再备份子目录中的文件。

# 在下面的例子中， find命令从文件系统的根目录开始，查找一个名为CON.FILE的文件。

# 它将首先匹配所有的文件然后再进入子目录中查找。

$ find / -name "CON.FILE" -depth -print
# 12、使用mount选项

# 在当前的文件系统中查找文件（不进入其他文件系统），可以使用find命令的mount选项。

# 从当前目录开始查找位于本文件系统中文件名以XC结尾的文件：

$ find . -name "*.XC" -mount -print


# find命令的例子；

# 1、查找当前用户主目录下的所有文件：

# 下面两种方法都可以使用

$ find $HOME -print
$ find ~ -print
# 2、让当前目录中文件属主具有读、写权限，并且文件所属组的用户和其他用户具有读权限的文件；

$ find . -type f -perm 644 -exec ls -l {  } \;
# 3、为了查找系统中所有文件长度为0的普通文件，并列出它们的完整路径；

$ find / -type f -size 0 -exec ls -l {  } \;
# 4、查找/var/logs目录中更改时间在7日以前的普通文件，并在删除之前询问它们；

$ find /var/logs -type f -mtime +7 -ok rm {  } \;
# 5、为了查找系统中所有属于root组的文件；

$find . -group root -exec ls -l {  } \;
# 6、find命令将删除当目录中访问时间在7日以来、含有数字后缀的admin.log文件。

# 该命令只检查三位数字，所以相应文件的后缀不要超过999。先建几个admin.log*的文件 ，才能使用下面这个命令

$ find . -name "admin.log[0-9][0-9][0-9]" -atime -7  -ok rm {  } \;
# 7、为了查找当前文件系统中的所有目录并排序；

$ find . -type d | sort
# 三、xargs

# xargs - build and execute command lines from standard input

# 在使用find命令的-exec选项处理匹配到的文件时， find命令将所有匹配到的文件一起传递给exec执行。但有些系统对能够传递给exec的命令长度有限制，这样在find命令运行几分钟之后，就会出现 溢出错误。错误信息通常是“参数列太长”或“参数列溢出”。这就是xargs命令的用处所在，特别是与find命令一起使用。

# find命令把匹配到的文件传递给xargs命令，而xargs命令每次只获取一部分文件而不是全部，不像-exec选项那样。这样它可以先处理最先获取的一部分文件，然后是下一批，并如此继续下去。

# 在有些系统中，使用-exec选项会为处理每一个匹配到的文件而发起一个相应的进程，并非将匹配到的文件全部作为参数一次执行；这样在有些情况下就会出现进程过多，系统性能下降的问题，因而效率不高；

# 而使用xargs命令则只有一个进程。另外，在使用xargs命令时，究竟是一次获取所有的参数，还是分批取得参数，以及每一次获取参数的数目都会根据该命令的选项及系统内核中相应的可调参数来确定。

# 来看看xargs命令是如何同find命令一起使用的，并给出一些例子。

# 下面的例子查找系统中的每一个普通文件，然后使用xargs命令来测试它们分别属于哪类文 件

find . -type f -print | xargs file
# 在当前目录下查找所有用户具有读、写和执行权限的文件，并收回相应的写权限：

 ls -l
 find . -perm -7 -print | xargs chmod o-w
 ls -l
# 用grep命令在所有的普通文件中搜索hello这个词：

 find . -type f -print | xargs grep "hello"
# 用grep命令在当前目录下的所有普通文件中搜索hello这个词：

 find . -name \* -type f -print | xargs grep "hello"
# 注意，在上面的例子中， \用来取消find命令中的*在shell中的特殊含义。

# find命令配合使用exec和xargs可以使用户对所匹配到的文件执行几乎所有的命令。
```

### 222.  sed

```shell 
# sed意为流编辑器（Stream Editor），在Shell脚本和Makefile中作为过滤器使用非常普遍，也就是把前一个程序的输出引入sed的输入，经过一系列编辑命令转换为另一种格式输出。sed和vi都源于早期UNIX的ed工具，所以很多sed命令和vi的末行命令是相同的。

# sed命令行的基本格式为

sed option 'script' file1 file2 ...
sed option -f scriptfile file1 file2 ...
# 选项含义：

--version            显示sed版本。
--help               显示帮助文档。
-n,--quiet,--silent  静默输出，默认情况下，sed程序在所有的脚本指令执行完毕后，将自动打印模式空间中的内容，这些选项可以屏蔽自动打印。
-e script            允许多个脚本指令被执行。
-f script-file, 
--file=script-file   从文件中读取脚本指令，对编写自动脚本程序来说很棒！
-i,--in-place        直接修改源文件，经过脚本指令处理后的内容将被输出至源文件（源文件被修改）慎用！
-l N, --line-length=N 该选项指定l指令可以输出的行长度，l指令用于输出非打印字符。
--posix             禁用GNU sed扩展功能。
-r, --regexp-extended  在脚本指令中使用扩展正则表达式
-s, --separate      默认情况下，sed将把命令行指定的多个文件名作为一个长的连续的输入流。而GNU sed则允许把他们当作单独的文件，这样如正则表达式则不进行跨文件匹配。
-u, --unbuffered    最低限度的缓存输入与输出。
# 以上仅是sed程序本身的选项功能说明，至于具体的脚本指令（即对文件内容做的操作）后面我们会详细描述，这里就简单介绍几个脚本指令操作作为sed程序的例子。

a,append        追加
i,insert        插入
d,delete        删除
s,substitution  替换
# 如：$ sed "2a itcast" ./testfile 在输出testfile内容的第二行后添加"itcast"。

$ sed "2,5d" testfile
# sed处理的文件既可以由标准输入重定向得到，也可以当命令行参数传入，命令行参数可以一次传入多个文件，sed会依次处理。sed的编辑命令可以直接当命令行参数传入，也可以写成一个脚本文件然后用-f参数指定，编辑命令的格式为

/pattern/action
# 其中pattern是正则表达式，action是编辑操作。sed程序一行一行读出待处理文件，如果某一行与pattern匹配，则执行相应的action，如果一条命令没有pattern而只有action，这个action将作用于待处理文件的每一行。

# 常用的sed命令
/pattern/p  打印匹配pattern的行
/pattern/d  删除匹配pattern的行
/pattern/s/pattern1/pattern2/   查找符合pattern的行，将该行第一个匹配pattern1的字符串# 替换为pattern2
/pattern/s/pattern1/pattern2/g  查找符合pattern的行，将该行所有匹配pattern1的字符串替换为pattern2
# 使用p命令需要注意，sed是把待处理文件的内容连同处理结果一起输出到标准输出的，因此p命令表示除了把文件内容打印出来之外还额外打印一遍匹配pattern的行。比如一个文件testfile的内容是

123
abc
456
# 打印其中包含abc的行

$ sed '/abc/p' testfile
123
abc
abc
456
# 要想只输出处理结果，应加上-n选项，这种用法相当于grep命令

$ sed -n '/abc/p' testfile
abc
# 使用d命令就不需要-n参数了，比如删除含有abc的行

$ sed '/abc/d' testfile
123
456
# 注意，sed命令不会修改原文件，删除命令只表示某些行不打印输出，而不是从原文件中删去。

# 使用查找替换命令时，可以把匹配pattern1的字符串复制到pattern2中，比如：

$ sed 's/bc/-&-/' testfile
123
a-bc-
456
# pattern2中的&表示原文件的当前行中与pattern1相匹配的字符串
再比如：

$ sed 's/\([0-9]\)\([0-9]\)/-\1-~\2~/' testfile
-1-~2~3
abc
-4-~5~6
# pattern2中的\1表示与pattern1的第一个()括号相匹配的内容，\2表示与pattern1的第二个()括号相匹配的内容。sed默认使用Basic正则表达式规范，如果指定了-r选项则使用Extended规范，那么()括号就不必转义了。

$ sed  's/yes/no/;s/static/dhcp/'  ./testfile
# 注：使用分号隔开指令。

$ sed -e 's/yes/no/' -e 's/static/dhcp/' testfile
# 注：使用-e选项。
# 如果testfile的内容是

<html><head><title>Hello World</title></head>
<body>Welcome to the world of regexp!</body></html>
# 现在要去掉所有的HTML标签，使输出结果为

Hello World
Welcome to the world of regexp!
# 怎么做呢？如果用下面的命令

$ sed 's/<.*>//g' testfile
# 结果是两个空行，把所有字符都过滤掉了。这是因为，正则表达式中的数量限定符会匹配尽可能长的字符串，这称为贪心的(Greedy)。比如sed在处理第一行时，<.*>匹配的并不是或这样的标签，而是

<html><head><title>Hello World</title>
# 这样一整行，因为这一行开头是<，中间是若干个任意字符，末尾是>。

sed 's/<[^>]*>/ /g' testfile
```

### 220. awk

```shell 
# sed以行为单位处理文件，awk比sed强的地方在于不仅能以行为单位还能以列为单位处理文件。awk缺省的行分隔符是换行，缺省的列分隔符是连续的空格和Tab，但是行分隔符和列分隔符都可以自定义，比如/etc/passwd文件的每一行有若干个字段，字段之间以:分隔，就可以重新定义awk的列分隔符为:并以列为单位处理这个文件。awk实际上是一门很复杂的脚本语言，还有像C语言一样的分支和循环结构，但是基本用法和sed类似，awk命令行的基本形式为：

awk option 'script' file1 file2 ...
awk option -f scriptfile file1 file2 ...
# 和sed一样，awk处理的文件既可以由标准输入重定向得到，也可以当命令行参数传入，编辑命令可以直接当命令行参数传入，也可以用-f参数指定一个脚本文件，编辑命令的格式为：

/pattern/{actions}
condition{actions}
# 和sed类似，pattern是正则表达式，actions是一系列操作。awk程序一行一行读出待处理文件，如果某一行与pattern匹配，或者满足condition条件，则执行相应的actions，如果一条awk命令只有actions部分，则actions作用于待处理文件的每一行。比如文件testfile的内容表示某商店的库存量：

ProductA  30
ProductB  76
ProductC  55
# 打印每一行的第二列:

$ awk '{print $2;}' testfile
30
76
55
# 自动变量$1、$2分别表示第一列、第二列等，类似于Shell脚本的位置参数，而$0表示整个当前行。再比如，如果某种产品的库存量低于75则在行末标注需要订货：

$ awk '$2<75 {printf "%s\t%s\n", $0, "REORDER";} $2>=75 {print $0;}' testfile
ProductA  30    REORDER
ProductB  76
ProductC  55    REORDER
# 可见awk也有和C语言非常相似的printf函数。awk命令的condition部分还可以是两个特殊的condition－BEGIN和END，对于每个待处理文件，BEGIN后面的actions在处理整个文件之前执行一次，END后面的actions在整个文件处理完之后执行一次。

# awk命令可以像C语言一样使用变量（但不需要定义变量），比如统计一个文件中的空行数

$ awk '/^ *$/ {x=x+1;} END {print x;}' testfile
# 就像Shell的环境变量一样，有些awk变量是预定义的有特殊含义的：

# awk常用的内建变量

FILENAME  当前输入文件的文件名，该变量是只读的
NR  当前行的行号，该变量是只读的，R代表record
NF  当前行所拥有的列数，该变量是只读的，F代表field
OFS 输出格式的列分隔符，缺省是空格
FS  输入文件的列分融符，缺省是连续的空格和Tab
ORS 输出格式的行分隔符，缺省是换行符
RS  输入文件的行分隔符，缺省是换行符
# 例如打印系统中的用户帐号列表

$ awk 'BEGIN {FS=":"} {print $1;}' /etc/passwd
```

###221. linux核心命令

```shell
# 文本处理类的命令：

# wc:

wc [option] [file]...
    -l: 统计行数
    -c: 统计字节数
    -w；统计单词数
# tr

# tr: 转换字符或删除字符
    tr '集合1' '集合2'
    tr -d '字符集合'
# cut

# This is a test line.
-d字符：指定分隔符
-f#: 指定要显示字段
    单个数字：一个字段
    逗号分隔的多个数字：指定多个离散字段
    -：连续字段，如3-5；
# sort

# 按字符进行比较
sort [option] file...
    -f: 忽略字符大小写；
    -n: 比较数值大小；
    -t: 指定分隔符
    -k: 指定分隔后进行比较字段
    -u: 重复的行，只显示一次；
# uniq

# 移除重复的行
-c：显示每行重复的次数
-d：仅显示重复过的行
-u: 仅显示不曾重复的行
# 工具速查链接

http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/index.html
```

### 222.  练习

```shell 
求2个数之和
计算1-100的和
将一目录下所有的文件的扩展名改为bak
编译当前目录下的所有.c文件：
打印root可以使用可执行文件数，处理结果: root's bins: 2306
打印当前sshd的端口和进程id，处理结果: sshd Port&&pid: 22 5412
输出本机创建20000个目录所用的时间，处理结果:

real    0m3.367s
user    0m0.066s
sys     0m1.925s
打印本机的交换分区大小，处理结果: Swap:1024M

文本分析，取出/etc/password中shell出现的次数

第一种方法结果:
      4 /bin/bash
      1 /bin/sync
      1 /sbin/halt
     31 /sbin/nologin
      1 /sbin/shutdown
第二种方法结果:
        /bin/sync       1
        /bin/bash       1
        /sbin/nologin   30
        /sbin/halt      1
        /sbin/shutdown  1
文件整理，employee文件中记录了工号和姓名,（提示join）

employee.txt:
    100 Jason Smith 
    200 John Doe 
    300 Sanjay Gupta 
    400 Ashok Sharma 
    bonus文件中记录工号和工资
bonus.txt:
    100 $5,000 
    200 $500 
    300 $3,000 
    400 $1,250 
要求把两个文件合并并输出如下，处理结果:
    400 ashok sharma $1,250
    100 jason smith  $5,000
    200 john doe  $500
    300 sanjay gupta  $3,000
写一个shell脚本来得到当前的日期，时间，用户名和当前工作目录。

编写shell脚本获取本机的网络地址。
编写个shell脚本将当前目录下大于10K的文件转移到/tmp目录下
编写一个名为myfirstshell.sh的脚本，它包括以下内容。

a) 包含一段注释，列出您的姓名、脚本的名称和编写这个脚本的目的。
b) 问候用户。
c) 显示日期和时间。
d) 显示这个月的日历。
e) 显示您的机器名。
f) 显示当前这个操作系统的名称和版本。
g) 显示父目录中的所有文件的列表。
h) 显示root正在运行的所有进程。
i) 显示变量TERM、PATH和HOME的值。
j) 显示磁盘使用情况。
k) 用id命令打印出您的组ID。
m) 跟用户说“Good bye”
文件移动拷贝，有m1.txt m2.txt m3.txt m4.txt，分别创建出对应的目录，m1 m2 m3 m4 并把文件移动到对应的目录下

root用户今天登陆了多长时间
终端输入一个文件名，判断是否是设备文件
统计IP访问：要求分析apache访问日志，找出访问页面数量在前100位的IP数。日志大小在78M左右。以下是apache的访问日志节选

202.101.129.218 - - [26/Mar/2006:23:59:55 +0800] "GET /online/stat_inst.php?pid=d065 HTTP/1.1" 302 20-"-" "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)"
设计一个Shell程序，在/userdata目录下建立50个目录，即user1～user50，并设置每个目录的权限，其中其他用户的权限为：读；文件所有者的权限为：读、写、执行；文件所有者所在组的权限为：读、执行。

设计一个shell程序，添加一个新组为class1，然后添加属于这个组的30个用户，用户名的形式为stdxx，其中xx从01到30，并设置密码为对应的stdxx。
编写shell程序，实现自动删除30个账号的功能。账号名为std01至std30。
用户清理,清除本机除了当前登陆用户以外的所有用户
设计一个shell程序，在每月第一天备份并压缩/etc目录的所有内容，存放在/root/bak目录里，且文件名,为如下形式yymmdd_etc，yy为年，mm为月，dd为日。Shell程序fileback存放在/usr/bin目录下。
对于一个用户日志文件，每行记录了一个用户查询串，长度为1-255字节，共几千万行，请排出查询最多的前100条。 日志可以自己构造。 (提示：awk sort uniq head)
编写自己的ubuntu环境安装脚本
编写服务器守护进程管理脚本。
查看TCP连接状态

netstat -nat |awk ‘{print $6}’|sort|uniq -c|sort -rn

netstat -n | awk ‘/^tcp/ {++S[$NF]};END {for(a in S) print a, S[a]}’ 或
netstat -n | awk ‘/^tcp/ {++state[$NF]}; END {for(key in state) print key,"\t",state[key]}’
netstat -n | awk ‘/^tcp/ {++arr[$NF]};END {for(k in arr) print k,"t",arr[k]}’

netstat -n |awk ‘/^tcp/ {print $NF}’|sort|uniq -c|sort -rn

netstat -ant | awk ‘{print $NF}’ | grep -v ‘[a-z]‘ | sort | uniq -c
查找请求数请20个IP（常用于查找攻来源）：

netstat -anlp|grep 80|grep tcp|awk ‘{print $5}’|awk -F: ‘{print $1}’|sort|uniq -c|sort -nr|head -n20

netstat -ant |awk ‘/:80/{split($5,ip,":");++A[ip[1]]}END{for(i in A) print A[i],i}’ |sort -rn|head -n20
用tcpdump嗅探80端口的访问看看谁最高

tcpdump -i eth0 -tnn dst port 80 -c 1000 | awk -F"." ‘{print $1"."$2"."$3"."$4}’ | sort | uniq -c | sort -nr |head -20
查找较多time_wait连接

netstat -n|grep TIME_WAIT|awk ‘{print $5}’|sort|uniq -c|sort -rn|head -n20
找查较多的SYN连接

netstat -an | grep SYN | awk ‘{print $5}’ | awk -F: ‘{print $1}’ | sort | uniq -c | sort -nr | more
根据端口列进程

netstat -ntlp | grep 80 | awk ‘{print $7}’ | cut -d/ -f1
获得访问前10位的ip地址

cat access.log|awk ‘{print $1}’|sort|uniq -c|sort -nr|head -10
cat access.log|awk ‘{counts[$(11)]+=1}; END {for(url in counts) print counts[url], url}’
访问次数最多的文件或页面,取前20

cat access.log|awk ‘{print $11}’|sort|uniq -c|sort -nr|head -20
列出传输最大的几个exe文件（分析下载站的时候常用）

cat access.log |awk ‘($7~/.exe/){print $10 " " $1 " " $4 " " $7}’|sort -nr|head -20
列出输出大于200000byte(约200kb)的exe文件以及对应文件发生次数

cat access.log |awk ‘($10 > 200000 && $7~/.exe/){print $7}’|sort -n|uniq -c|sort -nr|head -100
如果日志最后一列记录的是页面文件传输时间，则有列出到客户端最耗时的页面

cat access.log |awk ‘($7~/.php/){print $NF " " $1 " " $4 " " $7}’|sort -nr|head -100
列出最最耗时的页面(超过60秒的)的以及对应页面发生次数

cat access.log |awk ‘($NF > 60 && $7~/.php/){print $7}’|sort -n|uniq -c|sort -nr|head -100
列出传输时间超过 30 秒的文件

cat access.log |awk ‘($NF > 30){print $7}’|sort -n|uniq -c|sort -nr|head -20
统计网站流量（G)

cat access.log |awk ‘{sum+=$10} END {print sum/1024/1024/1024}’
统计404的连接

awk ‘($9 ~/404/)’ access.log | awk ‘{print $9,$7}’ | sort
统计http status

cat access.log |awk ‘{counts[$(9)]+=1}; END {for(code in counts) print code, counts[code]}'
cat access.log |awk '{print $9}'|sort|uniq -c|sort -rn
蜘蛛分析，查看是哪些蜘蛛在抓取内容。

/usr/sbin/tcpdump -i eth0 -l -s 0 -w - dst port 80 | strings | grep -i user-agent | grep -i -E 'bot|crawler|slurp|spider'
创建一个用户mandriva，其ID号为2002，基本组为distro（组ID为3003），附加组为linux；

# groupadd linux
# groupadd -g 3003 distro
# useradd -u 2002 -g distro -G linux mandriva
创建一个用户fedora，其全名为Fedora Community，默认shell为tcsh； # useradd -c "Fedora Community" -s /bin/tcsh fedora

修改mandriva的ID号为4004，基本组为linux，附加组为distro和fedora；

# usermod -u 4004 -g linux -G distro,fedora mandriva
给fedora加密码，并设定其密码最短使用期限为2天，最长为50天；

# passwd fedora
# chage -m 2 -M 50 fedora
调试命令

strace -p pid
写一个脚本

1、创建一个组newgroup, id号为4000；
2、创建一个用户mageedu1, id号为3001，附加组为newgroup；
3、创建目录/tmp/hellodirxyz
4、复制/etc/fstab至上面的目录中
5、改变目录及内部文件的属主和属组为mageedu1;
6、让目录及内部文件的其它用户没有任何权限；

        #!/bin/bash
        # Description:
        # Version:
        # Datetime:
        # Author:

        myGroup="newgroup1"
        myUser="mageedu2"
        myDir="/tmp/hellodirxyz1"
        myID=3002

        groupadd -g 4001 $myGroup
        useradd -u $myID -G $myGroup $myUser
        mkdir $myDir
        cp /etc/fstab $myDir
        chown -R $myUser:$myUser $myDir
        chmod -R o= $myDir

        unset myGroup myUser myID myDir
统计/bin、/usr/bin、/sbin和/usr/sbin等各目录中的文件个数；

# ls /bin | wc -l
显示当前系统上所有用户的shell，要求，每种shell只显示一次；

# cut -d: -f7 /etc/passwd | sort -u
取出/etc/passwd文件的第7行；

# head -7 /etc/passwd | tail -1
显示第3题中取出的第7行的用户名；

# head -7 /etc/passwd | tail -1 | cut -d: -f1

# head -7 /etc/passwd | tail -1 | cut -d: -f1 | tr 'a-z' 'A-Z'
统计/etc目录下以P或p开头的文件个数；

# ls -d /etc/[Pp]* | wc -l
写一个脚本，用for循环实现显示/etc/init.d/functions、/etc/rc.d/rc.sysinit和/etc/fstab各有多少行；

for fileName in /etc/init.d/functions /etc/rc.d/rc.sysinit /etc/fstab; do
    wc -l $fileName
done

#!/bin/bash
for fileName in /etc/init.d/functions /etc/rc.d/rc.sysinit /etc/fstab; do
    lineCount=`wc -l $fileName | cut -d' ' -f1`
    echo "$fileName: $lineCount lines."
done

#!/bin/bash
for fileName in /etc/init.d/functions /etc/rc.d/rc.sysinit /etc/fstab; do
    echo "$fileName: `wc -l $fileName | cut -d' ' -f1` lines."
done
写一个脚本,将上一题中三个文件的复制到/tmp目录中；用for循环实现，分别将每个文件的最近一次的修改时间改为2016年12月15号15点43分；

for fileName in /etc/init.d/functions /etc/rc.d/rc.sysinit /etc/fstab; do
    cp $fileName /tmp
    baseName=`basename $fileName`
    touch -m -t 201109151327 /tmp/$baseName
done
写一个脚本, 显示/etc/passwd中第3、7和11个用户的用户名和ID号；

for lineNo in 3 7 11; do
    userInfo=`head -n $lineNo /etc/passwd | tail -1 | cut -d: -f1,3`
    echo -e "User: `echo $userInfo | cut -d: -f1`\nUid: `echo $userInfo |cut -d: -f2`"
done
显示/proc/meminfo文件中以大小写s开头的行；

# grep "^[sS]" /proc/meminfo
# grep -i "^s" /proc/meminfo
取出默认shell为非bash的用户；

# grep -v "bash$" /etc/passwd | cut -d: -f1
取出默认shell为bash的且其ID号最大的用户；

# grep "bash$" /etc/passwd | sort -n -t: -k3 | tail -1 | cut -d: -f1
显示/etc/rc.d/rc.sysinit文件中，以#开头，后面跟至少一个空白字符，而后又有至少一个非空白字符的行；

# grep "^#[[:space:]]\{1,\}[^[:space:]]\{1,\}" /etc/rc.d/rc.sysinit
显示/boot/grub/grub.conf中以至少一个空白字符开头的行；

# grep "^[[:space:]]\{1,\}[^[:space:]]\{1,\}" /boot/grub/grub.conf
找出/etc/passwd文件中一位数或两位数；

# grep --color=auto "\<[0-9]\{1,2\}\>" /etc/passwd
找出ifconfig命令结果中的1到255之间的整数；

# ifconfig | grep -E --color=auto "\<([1-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\>"
查看当前系统上root用户的所有信息;

# grep "^root\>" /etc/passwd
添加用户bash和testbash、basher，而后找出当前系统上其用户名和默认shell相同的用户；

# grep --color=auto "^\([[:alnum:]]\{1,\}\)\>.*\1$" /etc/passwd
找出netstat -tan命令执行的结果中以“LISTEN”或“ESTABLISHED”结尾的行；

取出当前系统上所有用户的shell，要求：每种shell只显示一次，且按升序显示；
# cut -d: -f7 /etc/passwd | sort -u
```

### 223. 开机自启动脚本

```shell
如果要添加为开机启动执行的脚本文件，可先将脚本复制或者软连接到/etc/init.d/目录下，然后用：

    update-rc.d xxx defaults NN命令(NN为启动顺序)，
将脚本添加到初始化执行的队列中去。

注意如果脚本需要用到网络，则NN需设置一个比较大的数字，如99。

1) 将你的启动脚本复制到 /etc/init.d目录下,以下假设你的脚本文件名为 test。

2) 设置脚本文件的权限

    $ sudo chmod 755 /etc/init.d/test
3) 执行如下命令将脚本放到启动脚本中去：

    $ cd /etc/init.d
    $ sudo update-rc.d test defaults 95
```

