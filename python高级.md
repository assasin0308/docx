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

```python
# 创建虚拟环境
mkvirtualenv [虚拟环境名称]
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
# 从远程库获取到本地
git pull
# 将本地提交远程库
git push origin master
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

### 131.

```python

```

### 132.

```python

```

### 133.

```python

```

### 134.

```python

```

### 135.

```python

```

### 136.

```python

```

### 137.

```python

```

### 138.

```python

```

### 139.

```python

```

### 140.

```python

```

### 141.

```python

```

### 142.

```python

```

### 143.

```python

```

### 144.

```python

```

### 145.

```python

```

### 146.

```python

```

### 147.

```python

```

### 148.

```python

```

### 149.

```python

```

### 150.

```python

```

###151.

```python

```

### 152.

```python

```

### 153.

```python

```

### 154.

```python

```

### 155.

```python

```

### 156.

```python

```

### 157.

```python

```

### 158.

```python

```

### 159.

```python

```

### 160.

```python

```

### 161.

```python

```

### 162.

```python

```

### 163.

```python

```

### 164.

```python

```

### 165.

```python

```

### 166.

```python

```

### 167.

```python

```

### 168.

```python

```

### 169.

```python

```

### 170.

```python

```

### 171.

```python

```

### 172.

```python

```

### 173.

```python

```

### 174.

```python

```

### 175.

```python

```

### 176.

```python

```

### 177.

```python

```

### 178.

```python

```

### 179.

```python

```

### 180.

```python

```

### 181.

```python

```

### 182.

```python

```

### 183.

```python

```

### 184.

```python

```

### 185.

```python

```

### 186.

```python

```

### 187.

```python

```

### 188.

```python

```

### 189.

```python

```

### 190.

```python

```

### 191.

```python

```

### 192.

```python

```

### 193.

```python

```

### 194.

```python

```

### 195.

```python

```

### 196.

```python

```

### 197.

```python

```

### 198.

```python

```

### 199.

```python

```

### 200.

```python

```

### 