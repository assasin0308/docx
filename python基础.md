# Python基础

### 1. 获取键盘输入

```python
input()  //默认获取的数据类型是字符串

打印名片:
name = input("请输入姓名:")
QQ = input("请输入QQ号码:")
print("="*10)
print("姓名:%s"%name)
print("QQ:%s"%QQ)
print("="*10)

print输出多个变量:
name = "assasin"
age = 20
address = "北京市"
print("姓名是:%s,年龄是%d,地址是:%s"%(name,age,address))
```

### 2. 查看系统关键字

```python
import keyword
keyword.kwlist //所有系统关键系 
```

### 3. 运算符

```python
1. 简单运算符
+ - * / (加减乘除)
//(取商)
%(取余)
**(幂/次方)
2. 比较运算符
>= 大于或等于
<= 小于等于
== 等于
!= 不等于
3. 逻辑运算符
or  或者
and 并且
not 取反
示例1:
a = 30
if not(a > 0 and a < 50) :
    print('0---50')
else :
    print('不在0--50')
4.循环 while 与 for
示例1:打印矩形
i = 1
while i <= 5:
    j = 1
    while j <= 5:
        print("*",end="")
        j += 1
    print("")
    i += 1
示例2:打印9*9乘法表
i = 1
while i <= 9:
    j = 1
    while j <= i:
        print("%d * %d = %d   "%(j,i,i*j),end="")
        j += 1
    print("")
    i += 1
示例3:剪刀石头布
import  random
player = int(input("请输入: (0剪刀)  (1石头)  (2布)"))
computer = random.randint(0,2)
if (player == 0 and computer == 2 ) or (player == 1 and computer == 0) or (player == 2 and computer == 1):
    print("赢了...")
elif player ==  computer:
    print("平局...")
else:
    print("输了...")
5. break 与 continue 
break 结束整个循环
continue 结束本次循环
6. for-else

```

### 4. 格式符号对照表

```python
格式符号        转换
  %c            字符
  %s         通过str()字符串换来格式化
  %i		有符号十进制整数
  %d		有符号十进制整数
  %u		无符号十进制整数
  %o		八进制整数
  %x		十六进制整数(小写字母)
  %X		十六进制整数(大写字母)
  $e		索引符号(小写'e')
  %E		索引符号(大写'E')
  %f		浮点实数
  %g		%f与%e的简写
  %G		%f与%E的简写
```

### 5. 数据类型 -- 字符串

```python
python中的数据类型: 数值 字符串 列表 元组 字典
字符串的组合: 字符串连接使用 + 进行连接
字符串的截取:下标|切片
字符串逆序: str[::-1]
常见操作: str.find(s) | str.rfind() //寻找s在字符串str中的下标
		 str.index() | str.rindex()
          str.upper() //将字符串str转为大写
          str.lower() //将字符串str转为小写
          str.capitalize() //将字符串第一个字母转为大写
          str.title() //将字符串每一个字母转为大写
          str.count(s) //s在str中出现次数 若不存在返回0
          str.replace(a,s)//将字符串中的a替换为s
          str.replace(a,s,1)//将字符串中的a替换为s 只替换1次(从左往右)
          str.split(s) //将字符串以s进行切割,返回列表
          str.startswith(s) //判断字符串是否以s开头 返回bool值
          str.endswith(s) //判断字符串是否以s结尾 返回bool值
          str.ljust(20) // 将字符串str靠左对齐
          str.rjust(20) // 将字符串str靠右对齐
          str.center(20) // 将字符串str居中对齐
          str.lstrip() //去掉字符串str左边空格
          str.rstrip() //去掉字符串str右边空格
          str.strip() //去掉字符串str两边空格
          str.partition(s) //将字符串str以s进行切割 返回元组 从左边开始
          str.partition(s) //将字符串str以s进行切割 返回元组 从右边开始
          str.splitlines() //将字符串str以换行符进行切割 返回列表
          str.isalpha() //判断是否是纯字母
          str.isdigit() //判断是否是纯数字
          str.isalnum() //判断是否是字母与数字
          str.isspace() //判断是否只包含空格
          str.join(a) //将列表a使用str连接         
```

### 6. 数据类型 -- 列表

```python
列表的定义: var = []
列表的赠删改: append(s) //添加s至列表结尾
             insert(位置,s) //添加s至列表指定位置
             extend(s)//将两个列表合并
             pop() //删除最后一个元素 返回删除元素
             remove(s) //删除列表中s元素 
             del //使用下标删除 (del names[2])
             对列表元素重新赋值进行修改
             s in list //判断s是否在list中
             s not in list //判断s是否不在list中
示例:名字管理系统
print("="*50)
print("  名字管理系统 v8.0")
print(" 1. 添加一个新名字")
print(" 2. 删除一个名字")
print(" 3. 修改一个名字")
print(" 4. 查询一个名字")
print(" 5. 退出系统")
print("="*50)
names = []
while True:
    num = int(input("请输入功能序号:"))
    if num == 1:
        new_name = input("请输入一个名字:")
        names.append(new_name)
        print(names)
    elif num == 2:
        del_name = input("请输入需要删除的名字:")
        names.remove(new_name)
        print(names)
    elif num == 3:
        update_name = input("请输入需要替换的名字:")
        if update_name in names:
            names.remove(update_name)
            xin_name = input("请输入需要替换后的名字:")
            names.append(xin_name)
            print(names)
        else:
            print("对不起,查无此人")
    elif num == 4:
        find = input("请输入需要查询的名字:")
        if find in names:
            print("找到了")
        else:
            print("没找到")
    elif num == 5:
        break
    else:
        print("输入有误!")

列表中字典的排序
info = [{'name': '史斌', 'qq': '3143', 'address': '西安', 'tel': '502'}, {'name': '飞跃', 'qq': '142', 'address': '河南', 'tel': '602'}]
info.sort(key=lambda,x:x['name'])
```

### 7. 数据类型 -- 元组

```python
注意:元组与列表类似,但是!  元组的元素不能修改!
元组的定义: var = ()
注意:若元组中只有一个元素,加,!

```

### 8. 数据类型 -- 字典

```python
定义一个数据字典 var = {} 
字典操作: len()  //字典中键值对的个数
         keys()  //字典中所有的键
         values() //字典中所有的值
         items() //返回键值对的  元组
示例:名片管理系统

```

### 9. 数据类型 -- 函数 

```python
1.具有独立功能的代码块
def 函数名():
    函数体
局部变量与全局变量:
global var 将变量声明为全局变量

2.缺省参数:
def 函数名(a,b = 22,,c = 33):
    pass

3.不定长参数函数1:
def 函数名(a,b,*args):
    pass
    #args 元组形式保存
    
4.不定长参数函数2:
def 函数名(a,b,**kwargs):
    pass
    #kwargs 字典形式保存
示例: 
a = (11,22,33)
b =  {'name': '飞跃', 'qq': '142', 'address': '河南', 'tel': '011'}

def test(a,*args,**kwargs):
    print(args)
    print(kwargs)

test(1,*a,**b)
结果:# (11, 22, 33)
     # {'name': '飞跃', 'qq': '142', 'address': '河南', 'tel': '011'}

5.引用传值 
id(s) //产看变量s的内存地址

6.可变类性与不可变类性
可变类性:列表,字典,集合
不可变类性:数字,字符串,元组

7. 函数的递归
def jiecheng(num):
    if num <= 1:
        return num
    num * jiecheng(num -1)
    return num
res = jiecheng(2)
print(res)

8. 匿名函数
def test(a,b,func):
    return func(a,b)

res = test(1,2,lambda x,y:x+y)
print(res)

9. 交换两个变量的值
a = 4
b = 5
a,b = b,a
或
a = a + b
b = a - b
a = a - b
或 借助第三个变量
```

### 10. 文件操作

```python
1.打开文件
f = open('**.txt','w')
2. 文件关闭
f.close()

打开模式说明:
w:写模式,若不存在,则创建 |
r:读模式|
a:追加模式|
rb:二进制格式打开用于只读|
wb:二进制格式打开用于写入|
ab:二进制格式打开用于追加|
r+:打开开文件用于读写,文件指针放在文件开头|
w+:打开文件用于读写,若存在,则覆盖,若不存在,则创建|
a+:打开文件用于读写,若存在,文件指针放在文件结尾,若不存在,则创建|
rb+:二进制格式打开文件用于读写,文件指针放在文件开头|
wb+:二进制格式打开文件用于读写,文若存在,则覆盖,若不存在,则创建|
ab+:二进制格式打开文件用于追加,文件指针放在文件结尾,若不存在,则创建

3. 读文件
f.read()

4.写文件
f.write(要写的内容)

5.复制文件
file_name = input("请输入要复制的文件名")
f = open(file_name,'r')
position = new_file_name =file_name.rfind('.')
new_file_name = file_name[0:position] + '(附件)' + file_name[position:]
new_f = open(new_file_name,'w')
while True:
    file_con = f.read(1024);
    if len(file_con) == 0:
          break
    new_f.write(file_con)
f.close()
new_f.close()

6.大文件的处理方式
readlines() //以列表保存读取内容

7.文件的定位读写
f.seek()
f.tell()

8.文件夹/文件操作
import os
os.rename(原文件名,新文件名) //重命名文件
os.remove(文件名)  //删除文件
os.mkdir()//创建文件夹
os.rmdir()//删除文件夹
os.getcwd() //返回当前操作文件的路径
os.chdir() //改变当前路径
os.listdir() //获取当前目录文件列表 

9.批量重命名

```

### 11. 面向对象1 

```python
1. 类由三部分构成:
类的名称;
类的属性;
类的方法.
2. 类的定义
class 类名:
    方法体 方法必须写self
    属性
示例:
class Cat:
    def eat(self):
        print("猫咪在吃鱼")

    def drink(self):
        print("猫咪在喝水")
        
3. 对象的创建
tom = Cat()
tom.eat()

4.创建对象的属性
tom.name = '汤姆'
tom.age = 25

5.示例:
class Cat:
    def eat(self):
        print("猫咪在吃鱼")

    def drink(self):
        print("猫咪在喝水")

    def introduce(self):
        print("%s的年龄是:%d" %(self.name,self.age))

#创建对象并调用方法
tom = Cat()
tom.eat()
#创建属性
tom.name = '汤姆'
tom.age = 25
tom.introduce()

#创建其他对象
lanmao = Cat()
lanmao.name = '蓝猫'
lanmao.age = 15
lanmao.introduce()

6.__init__方法
作用:初始化对象
示例:
class Cat:
    def __init__(self,new_name,new_age):
        self.name = new_name
        self.age = new_age

    def eat(self):
        print("猫咪在吃鱼")

    def drink(self):
        print("猫咪在喝水")

    def introduce(self):
        print("%s的年龄是:%d" %(self.name,self.age))

#创建对象并调用方法
tom = Cat('汤姆',25) #将对象的引用传递给self
#创建属性
tom.introduce()

#创建其他对象
lanmao = Cat('蓝猫',15)
lanmao.introduce()

7. __str__方法
作用:获取对象的描述信息
示例:
class Cat:

    def __init__(self,new_name,new_age):
        self.name = new_name
        self.age = new_age

    def __str__(self):
        return "%s的年龄是%d"%(self.name,self.age)


    def eat(self):
        print("猫咪在吃鱼")

    def drink(self):
        print("猫咪在喝水")

    def introduce(self):
        print("%s的年龄是:%d" %(self.name,self.age))

#创建对象并调用方法
tom = Cat('汤姆',25)
print(tom)
#创建属性

#创建其他对象
lanmao = Cat('蓝猫',15)
print(lanmao)

8. 案例(烤地瓜):
class SweetPotato:

    def __init__(self):
        self.cookString = '生的'
        self.cookLevel = 0
        self.condiments = []

    def __str__(self):
        return "地瓜的状态%s(%d),添加的作料有:%s"%(self.cookString,self.cookLevel,str(self.condiments))

    def cook(self,cook_time):
        self.cookLevel += cook_time
        if self.cookLevel >= 0 and self.cookLevel < 3:
            self.cookString = "生的"
        elif self.cookLevel >= 3 and self.cookLevel < 5:
            self.cookString = "半生不熟"
        elif self.cookLevel >= 5 and self.cookLevel < 8:
            self.cookString = "好了"
        elif self.cookLevel > 8:
            self.cookString = '烤糊了'

    def addCondiments(self,element):
        self.condiments.append(element)

digua = SweetPotato()
print(digua)
digua.cook(1)
print(digua)
digua.cook(1)
digua.addCondiments('番茄酱')
print(digua)
digua.cook(1)
digua.addCondiments('大蒜')
print(digua)
digua.cook(1)
digua.addCondiments('肉末')
print(digua)

9. 案例2(存放家具):
class Home:

    def __init__(self,area,type,address):
        self.area = area
        self.type = type
        self.address = address
        self.leftArea = area
        self.content = []

    def __str__(self):
        return '房子的总面积是:%d,当前房子里有:%s,可用面积是:%d,户型是:%s,地址是:%s'%(self.area,str(self.content),self.leftArea,self.type,self.address)

    def addItem(self,item):
        self.leftArea  -=  item.get_area()
        self.content.append(item.get_name())

class  Bed:
    def __init__(self,name,area):
        self.name = name
        self.area = area

    def __str__(self):
        return '创的品牌是:%s,面积是:%d'%(self.name,self.area)

    def get_area(self):
        return self.area

    def get_name(self):
        return self.name

house = Home(150,'三室一厅','北京市朝阳区')
print(house)

bed1 = Bed('水晶家纺',4)
print(bed1)

house.addItem(bed1)
print(house)

bed2 = Bed('席梦思',3)
house.addItem(bed2)
print(house)

bed3 = Bed('婴儿床',1)
house.addItem(bed3)
print(house)

10. 隐藏属性
使用方法代替直接设置属性
示例:
class Dog:
    def set_age(self,new_age):
        if new_age > 0 and new_age <= 10:
            self.age = new_age
        else:
            self.age = 0

    def get_age(self):
         return self.age

dog = Dog()
dog.set_age(25)
age = dog.get_age()
print(age)

11 私有方法
类外不可访问的方法 __def(self)
示例:
class Dog:
    def set_age(self,new_name):
        self.name = new_name
        self.__age = 0 #私有属性

    def __send(self): #私有方法 类外不可访问
        print("发送短信"*20)

    def send_msg(self,money):
        if money > 100:
            print("="*20)
            self.__send()
        else:
            print("余额不足")
dog = Dog()
dog.send_msg(200)

12. __del__方法
对象被删除前自动调用

13.测量对象的引用个数
import sys
sys.getrefcount(var) 比实际多 1

14. 继承    子类(父类)
示例:
class Animal:
    def eat(self):
        print("...吃饭...")

    def drink(self):
        print('...喝水...')

    def sleep(self):
        print("...睡觉...")

    def run(self):
        print("...跑跑...")

class Dog(Animal): #继承Animal基类
     def bark(self):
         print("...旺旺叫...")
         
class Xiaotian(Dog): #继承父类Dog类
    def fly(self):
        print("..飞啊飞....")
        
a = Animal()
wangcai = Dog()
wangcai.bark()
wangcai.run()
xiaotian = Xiaotian()
xiaotian.fly()
xiaotian.bark()
xiaotian.eat()

15. 重写
class Xiaotian(Dog): #继承父类Dog类
    def fly(self):
        print("..飞啊飞....")

    def bark(self): #重写父类方法
        print("...狂叫....")
        
16. 调用被重写的方法  

class Xiaotian(Dog): #继承父类Dog类
    def fly(self):
        print("..飞啊飞....")

    def bark(self): #重写父类方法
        print("...狂叫....")
        #调用被重写的父类方法1
        Dog.bark(self)  #必须写self
        #调用被重写的父类方法2
        super().bark()

17. 私有属性,私有方法在继承中的表现

   私有方法不能被继承 | 私有属性不能被继承.
   但是!如果调用的是继承的父类中的共有方法,可以再这个共有方法中访问父类中的私有属性与私有方法;
   但是!如果在子类中实现了一个共有方法,那么这个方法是不能调用继承的父类中的私有属性与私有方法.
   
18. 多继承
子类有多个父类,并且具有它们的特征
object 是python3的最终类
class Base(): #默认继承  经典类
class Base(object): #新式类
class C(A,B): #多继承类 同时拥有A,B类的特征  ',':逗号隔开即可

多继承注意点: 类名.__mro__ 获取调取顺序

19.类的多态 
示例:
class Dog(object):

    def print_self(self):
        print("大家好,希望多多关照")

class Xiaotian(Dog):
    def print_self(self):
        print("hello everybody...")

def introduce(temp):
    temp.print_self()

dog1 = Dog()
dog2 = Xiaotian()
introduce(dog1)
introduce(dog2)

20. 类属性 实例属性
实例属性与具体的某个实例对象有关;并且一个实例对象与另一个实例对象是不共享属性的;
类属性所属于类对象,并且多个示例对象之间共享同一个类属性
示例:
class Tool(object):
    #定义属性
    num = 0 #类属性
    def __init__(self,new_name):
        #实例属性
        self.name = new_name
        Tool.num += 1 #获取类属性

tool1 = Tool('铁锹')
tool2 = Tool('铲子')
tool3 = Tool('水桶')
print(Tool.num)

21. 实例方法 类方法 静态方法
示例:
class Game(object):
    #类属性
    num = 0
    #实例方法
    def __init__(self):
        self.name = '老王' #实例属性
     #类方法
    @classmethod
    def add_num(cls):
        cls.num = 100

    #静态方法
    @staticmethod
    def print_menu():
        print("------------------")
        print("穿越火线")
        print("开始游戏")
        print("结束游戏")
        print("------------------")


game = Game()

#调类方法
Game.add_num() #可以通过类的名字调用类方法
game.add_num() #还可以通过这个类创建出的对象调用这个类方法
print(Game.num)

#调用静态方法
Game.print_menu() #1. 通过类名
game.print_menu() #2. 通过实例对象调用

22 调用父类方法:
 父类名称.父类方法()
 super().父类方法()
 super(当前类名称,self).父类方法()
```

### 12. 面向对象2 

```
1. 设计一个类(4S店) 简单工厂模式
2. 工厂方法模式
3. __new__方法
示例:
class Dog(object):

    def __init__(self):
        print("----------init----------")

    def __del__(self):
        print("----------del----------")

    def __str__(self):
        print("----------str----------")

    def __new__(cls): #cls此时是Dog指向的类对象
        # print(id(cls))
        print("----------new----------")
        return object.__new__(cls)

#print(id(Dog))
xiaotian = Dog()

4. 单例对象
示例:
class Dog(object):

   __instance = None

   def __new__(cls):
        if cls.__instance == None:
            cls.__instance = object.__new__(cls)
            return cls.__instance
        else:
            return cls.__instance
a = Dog()
print(id(a))
b = Dog()
print(id(b))

5.只初始化一次
示例:
class Dog(object):

    __instance = None
    __init_flag = False

    def __new__(cls,name):
        if cls.__instance == None:
            cls.__instance = object.__new__(cls)
            return cls.__instance
        else:
            return cls.__instance

    def __init__(self,name):
        if Dog.__init_flag == False:
            self.name = name
            Dog.__init_flag = True

a = Dog('旺财')
print(id(a))
print(a.name)

b = Dog('哮天犬')
print(id(b))
print(b.name)

6. 异常及其处理
异常定义:
try: #可能发生的异常
    print(a)
    print("---------")
except NameError: #异常名称
    print("如果捕获到异常后做的处理....")
    
print("--------------")


异常处理:
try: #可能发生的异常
    11/0
    open('xxx.txt','r')
    print(a)
    print("---------")
except (NameError,FileNotFoundError):
    print("如果捕获到异常后做的处理....")
except Exception as ret: #捕获所有异常 可以起一个别名
    print("如果用了Exception,只要以上没有捕获到异常,此except一定会捕获到")
    print(ret)
else:
    print("没有异常就会执行")
finally:
    print("不管有无异常最终都会执行.....")

print("--------------")

抛出自定义异常:
class ShortInputException(Exception):
    """自定义的异常类"""
    def __init__(self,length,atleast):
        self.length = length
        self.atleast = atleast


def main():
    try:
        s = input("请输入--->")
        if len(s) < 3:
            raise ShortInputException(len(s),3)
    except ShortInputException  as result:
        print("ShortInputException:输入的长度是:%d,长度至少是:%d"%(result.length,result.atleast))
    else:
        print("没有异常发生")

main()

异常处理中抛出异常:
class Test(object):
     def __init__(self,switch):
         self.switch = switch #开关

     def calculate(self,a,b):
         try:
             return a / b
         except Exception as result:
             if self.switch :
                 print("捕获开始,应捕获到异常,信息如下:")
                 print(result)
             else:
                 raise

a = Test(True)
a.calculate(11,0)

print("----------------分割线---------------------")

a.switch = False
a.calculate(11,0)

7 if的真假判断
条件为真: 1|-1|"a"|
条件为假: ""|None|0|[]|{} 等价于 False

8. 导入使用自定义模块方法
1.
# import new_mode #无需写文件后缀
#
# new_mode.test()
#或者
2.
from new_mode import test
test()
#或者
3.
#from new_mode import test,test1
from new_mode import * #尽量少用 若方法名相同后导入的会覆盖前导入
4. import time as tt #给模块起别名


```

### 13. 其他

```python
1. __all__
模块中__all__ = ['模块中功能1','模块中功能2',...]
防止 from *** import * 导入不需要的功能
2.__init__.py 
将多个模块文件放入文件夹中(在文件夹中创建__init__.py)
那么这个文件夹整体就是一个包.

__init__.py中内容:
__all__ = ['功能1','功能2',...]
或者
还可以写一些独立的功能(方法) 只针对python2
或者 
from . inport 模块  从当前路径导入

3. 模块的发布与下载
1.在包的同级目录下新建setup.py
内容:
from distutils.core import setup
setup(name="名称",version="1.0",description="描述信息",author="assasin",py_modules=["包名.模块名","包名.模块名"])
2. python setup.py build
3. python setup.py sdist
4. 安装 python setup.py install

4. 给程序传参数
python xxx.py 参数1 参数2 参数3 ... 空格隔开 

xxx.py
import sys
sys.argv 接收参数 列表形式

5. 列表生成式(推导式)
range(1,5)  从1到5-1  是列表
range的风险: 占用内存空间 python2中!
如获取1--17的列表:
示例:
 a  = [i for i in range(1,17)]

c = [i for i in range(10) if i%2==0]
#  [0, 2, 4, 6, 8]
d = [i for i in range(3) for j in range(2)]
#  [0, 0, 1, 1, 2, 2]
d = [(i,j) for i in range(3) for j in range(2)]
#  [(0, 0), (0, 1), (1, 0), (1, 1), (2, 0), (2, 1)]

6.集合,元组,列表
集合定义: c = {11,22,33} 
集合中的元素不能重复!
a = []
set(a) #将列表a转为集合
```

### 14. 飞机大战游戏

```python
import pygame
import time
from pygame.locals import *
import random

class Base(object):
    def __init__(self,screen_temp,x,y,image_name):
        self.x = x
        self.y = y
        self.screen = screen_temp
        self.image = pygame.image.load(image_name)


class BasePlane(Base):

    def __init__(self,screen_temp,x,y,image_name):
        Base.__init__(self,screen_temp,x,y,image_name)
        self.bullet_list = [] #存储发射出去的子弹对象引用

    def display(self):
        self.screen.blit(self.image, (self.x,self.y))
        for bullet in self.bullet_list:
            bullet.display()
            bullet.move()
            if bullet.judge():#判断子弹是否越界
                self.bullet_list.remove(bullet)


class HeroPlane(BasePlane):

    def __init__(self,screen_temp):
        BasePlane.__init__(self,screen_temp,210,720,"./images/hero1.png")

    def move_left(self):
        self.x -= 5

    def move_right(self):
        self.x += 5

    def fire(self):
        self.bullet_list.append(Bullet(self.screen,self.x,self.y))

class EnemyPlane(BasePlane):
    """敌机"""
    def __init__(self,screen_temp):
        BasePlane.__init__(self, screen_temp, 0, 0, "./images/enemy0.png")
        self.direction = 'right'

    def move(self):
        if self.direction == 'right':
            self.x += 5
        elif self.direction == 'left':
            self.x -= 5

        if self.x > 480 - 50: # 边界 - 飞机宽度
            self.direction = 'left'
        elif self.x < 0:
            self.direction = 'right'


    def fire(self):
        random_num = random.randint(1,100)
        if  random_num == 8 or random_num == 20:
             self.bullet_list.append(EnemyBullet(self.screen,self.x,self.y))


class BaseBullet(Base):
    def __init__(self,screen_temp,x,y,image_name):
        Base.__init__(self, screen_temp, x, y, image_name)

    def display(self):
       self.screen.blit(self.image,(self.x,self.y))

class Bullet(BaseBullet):

    def __init__(self,screen_temp,x,y):
        BaseBullet.__init__(self,screen_temp,x + 40,y -20,"./images/bullet.png")

    def move(self):
        self.y -= 5

    def judge(self):
        if self.y < 0:
            return True
        else:
            return False

#敌机发射子弹
class EnemyBullet(BaseBullet):

    def __init__(self,screen_temp,x,y):
        BaseBullet.__init__(self, screen_temp, x + 25, y + 40, "./images/bullet1.png")

    def move(self):
        self.y += 5

    def judge(self):
        if self.y > 852:
            return True
        else:
            return False


def key_control(hero_temp):
    # 获取键盘输入事件
    for event in pygame.event.get():
        # 判断是都点击了退出按钮
        if event.type == QUIT:
            print("exit")
            exit()

        # 判断是否按下了键
        if event.type == KEYDOWN:
            # 检测是否按下a或left
            if event.key == K_a or event.key == K_LEFT:
                print("left")
                hero_temp.move_left()
            # 检测是否按下d或right
            elif event.key == K_d or event.key == K_RIGHT:
                print("right")
                hero_temp.move_right()
            # 检测按键是否是空格
            elif event.key == K_SPACE:
                print("space")
                hero_temp.fire()

def main():
    #1.创建一个窗口,用来显示内容
    screen =  pygame.display.set_mode((480,852),0,32)
    #2.创建一个和矿口大小一样的图片,用来充当背景
    background = pygame.image.load("./images/background.png")
    #创建飞机对象
    hero = HeroPlane(screen)
    #创建一个敌机
    enemy = EnemyPlane(screen)

    #3.把背景图片显示到窗口中
    while True:
        #设定需要显示的背景图
        screen.blit(background,(0,0))
        hero.display() #玩家飞机显示
        enemy.display() #敌机显示
        enemy.move() #敌机移动
        enemy.fire()#敌机开火
        #显示玩家飞机
        #更新需要显示的内容
        pygame.display.update()

        #键盘事件
        key_control(hero)
        #延时显示 降低CPU
        time.sleep(0.01)


if __name__ == '__main__':
    main()
```

### 15. 老王开枪

```python
class  Person(object):
    def __init__(self,name):
        super(Person, self).__init__()
        self.name = name
        self.gun = None #保存枪对象的引用
        self.hp = 100

    #安装子弹到弹夹
    def load(self,danjia_temp,bullet_temp):
        danjia_temp.save(bullet_temp)

    #安装弹夹到枪
    def anzhuang(self,gun_temp,danjia_temp):
        gun_temp.save(danjia_temp)

    #老王拿枪
    def handle(self,gun_temp):
        self.gun = gun_temp

    #老王开枪打敌人
    def fire(self,diren_temp):
        self.gun.kaipao(diren_temp)

    def diaoxue(self,shashang):
        self.hp -= shashang

    def __str__(self):
        if self.gun:
            return "%s的血量是:%d,有枪,%s"%(self.name,self.hp,self.gun)
        else:
            if self.hp > 0 :
                return "%s的血量是:%d,没有枪"%(self.name,self.hp)
            else:
                return "%s已经挂了"%(self.name)

class Gun(object):
    def __init__(self,name):
        super(Gun, self).__init__()
        self.name = name #记录枪的类型
        self.danjia = None #用来记录弹夹对象的引用

    #保存弹夹对象的引用
    def save(self,danjia_temp):
        self.danjia = danjia_temp

    def kaipao(self,diren_temp):
        bullet  = self.danjia.tan_zidan()
        if bullet :
            #子弹射杀敌人
            bullet.kill(diren_temp)
        else:
            print("弹夹中没有子弹了...")


    def __str__(self):
        if self.danjia:
            return "枪的信息:%s,%s"%(self.name,self.danjia)
        else:
            return "枪的信息:%s,枪中无子弹"%(self.name)

class Danjia(object):

    def __init__(self,max_num):
        super(Danjia, self).__init__()
        self.max_num = max_num #记录弹夹的容量
        self.bullet_list = [] #记录所有子弹的引用

    #保存子弹到弹夹
    def save(self,bullet_temp):
        self .bullet_list.append(bullet_temp)

    def tan_zidan(self):
        if self.bullet_list :
            return self.bullet_list.pop()
        else:
            return None

    def __str__(self):
        return "弹夹的信息:%d/%d"%(len(self.bullet_list),self.max_num)

class Bullet(object):
    def __init__(self,shashang):
        super(Bullet, self).__init__()
        self.shashang = shashang #记录子弹的威力

    def kill(self,diren_temp):
        #敌人掉血 掉一颗子弹的威力
        diren_temp.diaoxue(self.shashang)


def main():
    '''控制整个程序'''
    # 1.老王对象
    laowang = Person('老王')
    #2.枪对象
    ak = Gun('Ak47')
    #3.弹夹对象
    danjia = Danjia(20)
    #4.一些子弹对象
    for i in range(15):
        bullet = Bullet(10)
    #5.老王-->子弹-->弹夹
        laowang.load(danjia,bullet)
    #测试弹夹信息
    #print(danjia)
    #测试赛枪的信息
    #print(ak)
    #6. 老王弹夹-->枪
    laowang.anzhuang(ak,danjia)
    #7.老王-->枪
    laowang.handle(ak)
    #测试老王对象
    print(laowang)
    #8.敌人对象
    diren = Person('敌人')
    print(diren)
    #9. 老王开枪-->敌人
    laowang.fire(diren)
    print(diren)
    print(laowang)
    laowang.fire(diren)
    print(diren)
    print(laowang)
    laowang.fire(diren)
    print(diren)
    print(laowang)
    laowang.fire(diren)
    print(diren)
    print(laowang)
    laowang.fire(diren)
    print(diren)
    print(laowang)
    laowang.fire(diren)
    print(diren)
    print(laowang)
    laowang.fire(diren)
    print(diren)
    print(laowang)
    laowang.fire(diren)
    print(diren)
    print(laowang)



if __name__ == '__main__':
    main()

```

