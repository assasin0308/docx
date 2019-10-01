### GO语言

### 1. 变量、常量定义与声明

```go
① go语言是强类型语言（静态语言），要求变量的额类型和赋值的类型必选一致；

② 同一作用域内变量名称不能冲突；

③ 变量声明（开辟内存）后必须使用；

④正确使用变量得数据类型，存储对应类型的数据；

⑤ 简短定义方式不能定义全局变量

⑥ 变量的零值：整数默认值是0；浮点型默认值 0.0 ；字符串默认值 “ ”；[ ] 切片(slice)默认值 nil

```



```go
原始方式声明：
var name type     |  var  a  int 

name = value      |  a = 16

简写方式声明： var name type = value          |  var a int = 16

类型推断声明： var  name = value              |  var   c = 30  

省略简短声明（省略var）:   name := value       |  d   := 40 

```

```go
定义一组变量
var (
 y = 100
 z int
)
简短定义一组变量：
name，age := "王二狗"，30 （ := 左边至少有一个新变量）
```

%T（打印变量数据类型）、%d（打印整数，10进制）、%f（打印浮点数）、%t（打印bool）、%s（打印字符串）、%v（原样输出）、%c（打印对应的字符）、%p（地址）

 【常量】

显示类型定义：

```go
const name [type] = value
```

隐式类型赋值：

```go
const name = value
```

① 执行过程中数值不可以改变；

② 所有的常量名大写（公共的权限），私有（常量名前加小写字母）

```go
定义一组常量:
const c1,c2,c3 = 1,3.14,"hello-world"
const(
    MALE = 1
    FEMALE = 2
    UNKNOW = 0 
)
常量组中如不指定类型和初始值，则与上一行非空常量右值相同
const(
		a int = 100
		b
		c string = "hello-wrold"
		d
		e
	)
输出：100 100 hello-wrold hello-wrold hello-wrold
```

【iota】 特殊常量，可以被认为是一个被编译器修改的常量。

### 2. 数据类型：

2.1 基本数据类型：布尔型、数值型、字符串；

```go
数值类型:整数(int)/浮点(float)/复数(complex)
        整数值: 有符号的 正 / 负
             int8  8个01码表示的整数,最高位是符号位0表示正数,1表示负数[128,-128]
             int16  [-32768,32767] 
             int32(rune)  [-2147483648,2147483647]
             int64  [-9223372036854775808,-9223372036854775807]
                 无符号的:正数
             uint8(byte)  [0,255]
             uint16 [0,65535]
             uint32 [0,4294967295]
             uint64 [0,18446744073709551615]
        浮点型:
             float32
             float64     
        复数:
```

```go
布尔类型: true / false 
```

```go
字符串类型: 
     使用双引号" ",也可以使用 ` `(不是单引号 ' ');   
```

```go
转义字符: 
     有些字符转以后有特殊的含义;\n(换行),\r,\t(tab跳过一个水平制表符)
     有些字符有特殊含义,转以后作为普通字符;
```

2.2 复合数据类型：数组(array)、切片(slice)、map、结构体(struct)、指针(pointer)、接口(interface)、函数(function)等；

### 3. 数据类型的转换：

go语言是静态语言,定义和赋值时必须类型一致,兼容类型之间可以转换,如 int与float 

```go
语法格式: type(value) 
var a int8 = 16
   var b int16 = 20
   sum := int16(a) + b
   println(sum)
输出: 36
```

### 4. 运算符:

```go
算术运算符: + - *  /,%  ++  --  
```

```go
关系(比较)运算符 >  <  >=  <=  ==  != 结果是bool类型 
```

```go
逻辑运算符:  &&(逻辑与)  ||(逻辑或)  !(逻辑非)  操作数与运算结果均为bool类型
```

```go
赋值运算符  =  +=  -=  *=  /=  %=   将右侧数值赋值给左侧的变量
```

### 5. 标准输入/输出(fmt)

```go
打印: Print() printf() println()
输入: Scanln():读取一行数据,复制给对应的变量,通过抵制获取.数据与数据之间空格隔开,按照顺序依次赋值
      Scan():  第一个参数是读取规则,
      Scanf():
      bufio包 读取一行文本string
```

### 6. 分支语句/循环语句

```go
if语句(嵌套语句)与switch语句:
if 布尔表达式{
		布尔表达式满足时执行
	} else{
		布尔表达式不满足时执行
	}
if 布尔表达式{
		布尔表达式满足时执行
	} else if 布尔表达式 {
		布尔表达式满足时执行
	} ...else {
         布尔表达式满足时执行
	}
if (变量)初始化语句;条件{
     
   }
   
switch语句:
 switch 变量{
     case 数值1:分支1
     case 数值2:分支2
     ....
     default:
          最后一个分支    
     
 }
 注意点: switch的变量和case后的数值类型一致
         case是无顺序限制的
         case后的数值是唯一的
         default语句是可选的
对比:
 if只能作用bool类型,switch没有类型的限制
 if适合分值较少的情况,switch适合分支较多的情况
 
break(跳出) && fallthrough(通过)语句:
               fallthrough后边紧邻的直接匹配

```

```go
for循环(标准语法):
      for 表达式1;表达式2;表达式3{
		循环体
	  }
```

### 7. 随机数

```go
获取当前时间戳(秒):time.Now().Unix()
获取当前时间戳(纳秒):time.Now().UnixNano()
rand.Intn()
取m到n之间的随机数: rand.Intn(n-m+1)+m
```

### 8. 数组(array)

```go
1. 概念:存储一组相同数据类型的数据结构.(数组定长)
        数组一旦定义后,大小不能改变!
        特点:有序,定长,元素可以重复,属于值类型,对原始数据没有影响        
```

```go
2. 语法: var variable_name [size] variable_type
         var variable_name [size] variable_type {}
         var variable_name [size] variable_type {key:value,key:value}
         variable_name := [size] variable_type {}
         variable_name := [...] variable_type {value/key:value}
      var arr [10] int 
      arr[0] = 1
```

```go
3. 常用内置函数:
 len(array/slice/string/map) 获取数组/切片/map/字符串的长度
 cap(array) 获取数组的容量(数组中能够存储的最大数量)
```

```go
4. 数组是定长的容器,大小无法改变,可更改其存储的数据
```

```go
5. 数组的其他创建方式: 
var b = [4] int {1,2,3,4}
var d  = [5] int{1:1,3:2} //[0 1 0 2 0]
f := [...] int {1,2,3,4,5,6} // [1 2 3 4 5 6] 定义数组时使用...,表示根据{}中的数据来推断数组的长度
g :=[...] int{1:5,8:6} // [0 5 0 0 0 0 0 0 6]
```

```go
6. 遍历数组
  arr := [10] int {1,2,3,4,5,6,7,8,9,10}
  for index,value := range arr{
  	fmt.Println("下表是:",index,"数值是",value)
  }
  注意:可以使用_表示舍弃
  arr1 := [10] int {1,2,3,4,5,6,7,8,9,10}
  sum := 0
  for _,v := range arr1{
  	sum += v
  }
  fmt.Println(sum)
```

```go
7. 数组的数据类型: [size] type
   值类型:数值本身
   引用类型:数据的内存地址
```

```go
 冒泡排序算法:
  arr := [5]int{15,23,8,10,7}
   for i := 1;i< len(arr);i++{
		for j := 0;j < len(arr) - i;j++{
			if arr[j] > arr[j+1] {
				arr[j],arr[j+1] = arr[j+1],arr[j]
			}
		}
	}
   fmt.Println(arr)
 数组的搜索(查找指定的位置):如果不存在返回-1
 a:顺序搜索法(找到第一次出现的位置)
 b:二分(下标)查找法(条件是数组是有序的)
 
```

### 9. 多维数组

```go
1. 定义: arr := [几个一维][每个一维数组的长度]int
         arr := [3][4]{{1,2,3,4},{5,6,7,8,},{9,10,11,12},{13,14,15,16}}
```

### 10. 切片(slice)

```go
概念:  切片是数组的抽象,是一个引用类型的容器,指向了一个底层数组.
特点:   有序,变长,元素可以重复,引用类型
语法:  var identifier []type 注意与数组的区别
例子:  var slice1 []int    slice :=[]type{}
	  slice2 := []int{1,2,3,4}
内置函数: make() 创建slice,map,chan
      slice := make([]type,len,cap)
在数组的基础上创建切片:
    arr := [10]int{1,2,3,4,5,6,7,8,9,10}
    slice := arr[start(数组下标):end(数组下标)] (不包含end,start可以省略,表示从0开始)
    注意:从已有数组上创建切片,改切片的底层数组就是当前数组,长度是从start到end切割的数据量,容量是从start到数组的末尾.
切片的操作与数组雷同
切片与数组的不同之处:
    变长的,长度与容量不一定相同
    使用append()内置函数向切片添加元素,若超过切片容量,可自动扩容(成倍数扩容),
    append(slice,elem1,elem2,elem3) 或
    append(slice1,slice2...)
    删除切片中的元素:
    
```

### 11. 切片的数据类型(引用类型)

```go
值类型:(数组,int,float,string,bool,struct)传递的是数据副本;
引用类型:传递的是数据地址.

arr1 := [4]int{1,2,3,4}
	arr2 := arr1
	fmt.Println(arr1,arr2)
     arr2[0] = 100
	fmt.Println(arr1,arr2)
fmt.Println("--------------------------------------")
     slice1 := []int{1,2,3,4}
     slice2 := slice1
     fmt.Println(slice1,slice2)
     slice2[0] = 100
	fmt.Println(slice1,slice2)
输出:
      [1 2 3 4] [1 2 3 4]
      [1 2 3 4] [100 2 3 4]
      --------------------------------------
      [1 2 3 4] [1 2 3 4]
      [100 2 3 4] [100 2 3 4]
```

### 12. 映射(map)

```go
概念:map是一种无序键值对的集合,通过key来快速检索数据
特点:存储键值对(key-value),
     无序,引用类型数据
     键值key不能重复,并且与value值--对应,
     长度不固定,
     len()返回拥有key的数量
     如果不初始化map,那么就会创建一个nil map,nil map 不能用来存储键值对
 创建:
     var map_variable map[key_data_type]value_data_type 或
     var map_variable := map[key_data_type]value_data_type{无序的键值对}make创建
     map_variable := make(map[key_data_type]value_data_type)
 ok-idiom检验key/value是否存在:
     value,ok := map(key) key不存在 ok返回 false
 删除值:
     delete(map,键值)
```

### 13. 字符串(string)

```go
概念:是一些字节的集合,一个字节的切片
语法:使用 "" 或 ``
strings包的使用: https://studygolang.com/pkgdoc
     Index()/IndexAny(),IndexRune()/LastIndex()/LastIndexAny()/
     Contains()/ContainsAny()/Count()/Split()/SplitN()
     Trim()/TrimLeft()/TrimRight()
     Replace()/Repeat()/ToLower()/ToUpper()
     HasPrefix()/HasSuffix()
     strsub := str[start:end]
```

### 14. 函数(function)

```go
概念:执行特定任务的代码块.
语法: func functionname(parametername typ1,parameter2 type2)(output type1,output2 type2){
        return value1,value2
     }
调用: 形参与实参类型,个数,顺序一一对应;
返回值: return将函数的执行结果返回给调用处,支持一个函数有多个返回值,可以使用_舍弃不需要的返回值
return: 若函数定义了返回值,则必须使用return返回,return后的数据表现与声明的一致;
        若存在分支,循环等语句,须保证总有一个return执行 ;
        若没有定义返回值,使用return 终止函数执行;
        若有多个返回值,可使用 ' _ '进行舍弃;
作用域:全局变量不能使用简短语法定义.
可变参数(类型确定,但个数不确定): 参数名 ... 参数的类型 | 可传入该种类型的切边 | 必须放在形参末尾 | 最多                               只能有1个可变参数
参数的传递(值传递/引用传递):
    值类型额数据默认都是值传递(int,float,string,bool,array)
    引用传递传递的是数据的地址(slice,map)
```

```go
递归函数:一个函数自己调用自己,一定要有出口,逐渐向出口靠近
   func sum(n int )(int){
	if n == 1 {
		return 1
	}
	return sum(n-1) + n
}
```

### 15. 匿名函数  

```go
创建在main()主函数内,只能调用一次.
ret := func (a,b int)int{
		return a+b
	}(2,10)
fmt.Println(ret)
```

### 16. 回调函数(callback)

```go
函数作为另一个函数的参数,该函数则是回调函数
```

### 17. 闭包函数(closure)

```go
将函数作为另一个函数的返回值.
一个函数有内层函数,还内层函数中,还会操作外层阿函数的局部变量(外层函数中定义,外层函数的参数),
并且该外层函数的返回值就是内层函数.该内层函数和内层函数操作的局部变量同城为闭包结构.
func increment() func()int{ //外层函数
	i := 0
	fun := func() int{  //内层函数
		i++
		return i
	}
	return fun //将内层函数返回 本质返回该内层函数的地址
}
```

### 18. defer函数

```go
一个函数的执行被延迟. 
当外围函数的语句正常执行时,只有其中所有的延迟函数都执行完毕,外围函数才会真正的结束执行.
当执行外围函数中的return语句时,只有其中所有的延迟函数都执行完毕,外围函数才会返回.
当外围函数中的代码引发恐慌时,只有其中所有的延迟函数都执行完毕,该运行时恐慌才会真正被扩展至调用函数.
```

### 19. 指针(pointer)

```go
指针:是指存储另一个变量的内存地址的变量. *T是指指针变量的类型,它指向T类型的系
语法: var var_name *var_type  例: var ip *int
& 对于变量,取地址
* 对于指针,获取指针中存储的地址对应的数据
类型: *Type
存储的数据类型: 
存储的数据地址: &
指针本身的地址: 
```

### 20. 深浅拷贝

```go
深拷贝:拷贝数据的副本,最原始数据没有影响,值类型的数据默认都是深拷贝(int,string,bool,array,struct)
浅拷贝:拷贝的是数据的地址,引用类型的数据,默认都是浅拷贝(slice, map,function) 
```

### 21.指针函数与函数指针

```go
函数指针:一个指针,指向一个函数的指针
指针函数:一个函数,该函数的返回值是一个指针  
```

### 22.面向对象

```go
三大特征:封装,继承,多态
```

### 23. 结构体(struct)

```go
含义:由一系列具有相同类型或不同类型的数据构成的数据集合.
定义: type struct_veriable_name struct{
       member definition;
       member definition;
       ......
}
匿名结构体:在创建结构体时,同时创建对象:
变量名 := struct{
   定义字段
}{
  对字段进行赋值
}
匿名字段:一个结构体的字段没有名字.默认使用类型作为字段名,类型不能重复.
```

### 24. 方法(method)

```go
含义:一个方法就是一个包含了接受者的函数,接受者可以是命名类型或结构体类型的一个值或一个指针.所有的给定类型的方法属于该类型的方法集.
语法: func (t type) methodName(parameter list)(return list){
    
    }
```

### 25.接口(interface)

```go
含义: 功能描述的集合,指定对象应该做什么.
语法: type interface_name interface{
  method_name1 (return list)
  method_name2 (return list)
  method_name3 (return list)
}
```

### 26.error处理

```go
type error interface{
    Error() string
}
```

### 27.panic与recover

```go
处理程序的最后手段!
panic:恐慌.当函数F调用panic,函数的执行被中断,但F中的延迟函数会正常执行,然后F返回到调用它的地方.在调用的地方,F的行为就像调用了panic.这一过程持续向上,直到发生panic的goroutine中所有调用的函数返回,此时程序退出.恐慌可以只要接调用panic产生,也可以由运行时的错误产生,例如方位越界的数组
recover:恢复.在正常的执行过程中,调用recover会返回NULL,并且没有其他任何效果.如果当前的goroutine陷入恐慌,调用recover可以捕获到panic的输入值,并且恢复正常的执行.
```

### 28. time

```go
1. 获取当前时间: time.Now() | t1.Format("2006年1月2日 15:04:05")
2. 获取指定时间: time.Date(2019,5,7,23,16,25,0,time.Local)
3. time与string转换: t3,err := time.Parse("200年1月2日",s3)
4. 根据当前时间获取指定内容
5.时间间隔: t1.Add(24*time.Hour)
```

### 29. 文件操作

1. 文件信息 

   ```go
   fileinfo,err := os.Stat("E:/phpStudy/WWW/GO/11/test.text")
     if err != nil{
     	fmt.Printf(err.Error())
     }
     fmt.Println(fileinfo.Name()) //文件名 test.text
     fmt.Println(fileinfo.Size()) //文件大小 5
     fmt.Println(fileinfo.Mode()) //文件权限 -rw-rw-rw-
     fmt.Println(fileinfo.ModTime()) //最后一次修改时间  2019-05-08 23:50:02.2154172 +0800 CST
     fmt.Println(fileinfo.IsDir()) //是否为目录
     fmt.Println(fileinfo.Sys())
   ```

   ​

2. 文件操作

   ```go
   gfilename1 := "E:/phpStudy/WWW/GO/11/test.text"
   	filename2 := "test.text"
   	fmt.Println(filepath.IsAbs(filename1))
   	fmt.Println(filepath.IsAbs(filename2))
   	fmt.Println(filepath.Abs(filename1))
   	fmt.Println(filepath.Abs(filename2))
   	//获取父级目录
   	fmt.Println(path.Join(filename1,".."))
   ```

3. 创建文件夹

   ```go
   err := os.Mkdir("E:/phpStudy/WWW/GO/11/newdir",os.ModePerm)
   	if err != nil{
   		fmt.Println(err.Error())
   	}
   ```

4.  创建文件

   ```go
   file1, err2 := os.Create("E:/phpStudy/WWW/GO/11/test.doc")
   	if err2 != nil{
   		fmt.Println(err2.Error())
   	}
   	fmt.Println(file1)
   ```

5. 打开文件

   ```go
   file4,err4 := os.Open(filename1)
   	 if err4 !=nil{
   		 fmt.Println(err4.Error())
   	 }
   	 fmt.Println(file4)
   	 
   	 
   // 第一个参数:文件名
   	 // 第二个参数:文件的打开方式
   	 //os.O_RDONLY
   	 //os.O_WRONLY
   	 //os.O_RDWR
   	 //os.O_APPEND
   	 //os.O_CREATE
   	 //第三个参数:文件的权限,文件不存在创建文件,需要指定权限
   	file5,err5 := os.OpenFile(filename1,os.O_WRONLY|os.O_CREATE,os.ModePerm)
   	if err5 !=nil{
   		fmt.Println(err5.Error())
   	}
   	fmt.Println(file5)
   ```

6. 关闭文件

   ```go
   //关闭文件
   	file5.Close()
   ```

7. 删除文件/目录

   ```go
   os.Remove("E:/phpStudy/WWW/GO/11/test.doc") //要求文件为空
   os.RemoveAll("E:/phpStudy/WWW/GO/11/newdir")
   ```

8. 读取文件

   ```go
   filename := "E:/phpStudy/WWW/GO/11/test.txt"
   bs := make([]byte,4,4)
   	n := -1
       for {
         n,err = file.Read(bs)
         if n==0 || err == io.EOF{
         	fmt.Println("读到末尾了")
         	break
   	  }
         fmt.Println(string(bs[:n]))
   	}
       file.Close()
   ```

9. 写入数据

   ```go
   // 向文件写入数据
   	filename := "E:/phpStudy/WWW/GO/11/txt.txt"
   	file,err := os.OpenFile(filename,os.O_CREATE|os.O_WRONLY,os.ModePerm)
   	if err != nil{
   		fmt.Println("打开文件有无误",err.Error())
   	}

   	fmt.Println(file)
   	bs :=  []byte{65,66,67,68,69,70}
   	n,err := file.Write(bs)
   	fmt.Println(err)
   	fmt.Println(n)
   	n,err = file.WriteString("面朝大海,春暖花开")
   	fmt.Println(n)
        file.Close()
   ```

10.   复制文件

  ```go
  //实现文件的copy 返回的copy的总数量及异常
  func copyfile(srcfile,destfile string)(int,error){
      file1,err := os.Open(srcfile)
      if err != nil{
      	return 0,err
  	}
      file2,err := os.OpenFile(destfile,os.O_CREATE|os.O_WRONLY,os.ModePerm)
      if err != nil{
      	return 0,err
  	}
      defer file1.Close()
      defer file2.Close()
      //copy
      bs := make([]byte,1024,1024)
      n := -1
      total := 0
      for{
      	n,err = file1.Read(bs)
      	if err == io.EOF || n == 0{
      		fmt.Println("拷贝完毕")
      		break
  		}else if err != nil{
  			fmt.Println("err")
  			return total,err
  		}
      	total += n
      	file2.Write(bs[:n])
  	}
      return total,nil

  }
  ```

11.  seek( )设置指针光标的位置

    ```go
    seek(offset,whence)
    第一个参数:偏移量;
    第二个参数: 0: 距离文件开头offset个子节数;
               1: 距离光标当前的位置;
               2: 距离文件末尾的位置;

    ```
    #### 12. ioutil/bufio

    ```go
    1. 读取文件
        filename := "E:/phpStudy/WWW/GO/11/txt.txt"
    	data,_ := ioutil.ReadFile(filename)
    	fmt.Println(string(data))
    2. 写书数据
         file := "E:/phpStudy/WWW/GO/11/ccc.txt"
    	string := "面朝尘土,  大雪纷飞"
    	ioutil.WriteFile(file,[]byte(string),0777)
    	
    ```

    ### 30. 并发(Concurrency)

    ```go
    Concurrency同时处理许多事情的能力.多个程序"同时"执行
    并发的实现方式: 
        1.进程 (process): 一个正在执行的程序;
        2.线程 (thread): 一个进程中的一条执行路径;
        3.协程 (coroutine): 也叫轻量级线程,可轻松创建上百万个而系统不会衰竭
    同步,串性: 一个接一个的执行;
    Go语言实现高并发:
    A:goroutine,轻量级的协程;
    B:语法简单轻便,go关键字 函数
    Go代码的执行过程:
    1.启动主goroutine,执行main主函数,当main结束时,goroutine结束,程序结束;
    2.用于启动的goroutine,均为子goroutine.

    init()函数:  由系统自动调用执行,用于初始化

    ```

    ### 31. Channel 

    ```go
    channel:主要用于多个goroutine之间传递数据.
    go通过数据传递实现共享内存,而非通过共享内存实现消息传递.
    语法格式: make(chan type) -->对象,属于引用类型数据
    读数据 <-chan,写数据 chan <- data | 阻塞式读写,直到另一个goroutine写出或读取数据解除阻塞
    只能允许一个goroutine操作chan.   关闭通道: close(chan),一般是发送方调用,关闭通道后,可继续读取数据,但不能再写入数据了.
    示例:
    func main(){
    	ch1 := make(chan int)
    	go send(ch1)
    	for {
    		time.Sleep(1*time.Second)
    		data, ok := <-ch1
    		if !ok {
    			fmt.Println("读取完毕,通道关闭了",ok)
    			break
    		}
    		fmt.Println("main读取到:",data,ok)
    	}
    }
    func send(ch1 chan int){
    	for i:=1;i<=10;i++{
    		ch1 <- i
    	}
    	fmt.Println("send over")
    	close(ch1) //关闭通道
    }
    ```

    ### 32. for...range

    ```go
    for...range : 读取容器中的数据
    示例:
    func main(){
      ch1 :=make(chan string)
      go senddata(ch1)

      for value:= range ch1{ //停止条件:通道关闭
      	fmt.Println("从通道当中读取到的数据: ",value)
      }
    }
    func senddata(ch1 chan string){
    	for i:=1;i<=10;i++{
    		ch1 <- fmt.Sprint("数据 ",i)
    	}
    	fmt.Println("写入数据完毕")
    	close(ch1)
    }
    ```

    ### 33. 缓冲通道 

    ```go
    缓冲通道:默认创建的即为非缓冲通道.缓冲通道是指自带一块缓冲区,可以暂时存储数据,
    ```

    ### 34. 定向通道 

    ```go
    定向通道:也称为单项通道,只读或只写.一般在传递参数时使用.
    只读: make(<- chan Type),只读不写;
    只写: make(chan <- Type),只写不读;
    ```

    ### 35. timer包

    ```go
    time对于chan的操作
    timer:计时器:
         timer1 := time.NewTimer(3*time.Second)
    	fmt.Printf("%T\n",timer1) // *time.Timer
    	fmt.Println(time.Now()) //  2019-05-18 00:10:00.3091944 +0800 CST m=+0.002976801
    	time1 := <- timer1.C
    	//fmt.Printf("%T\n",time1) // <-chan time.Time
    	fmt.Println(time1) // 2019-05-18 00:10:03.3096616 +0800 CST m=+3.003444001
    after(): 返回 <- chan Time,同Timer.C
        ch1 := time.After(5*time.Second)
    	fmt.Println(time.Now()) //2019-05-18 00:14:19.0792395 +0800 CST m=+3.002512801
    	time2 := <-ch1
    	fmt.Println(time2) //  2019-05-18 00:14:24.0792811 +0800 CST m=+8.002554401
    ```

    ### 36. select语句

    ```go
    select:类似于switch语句,但是select会随机执行一个可运行的case.如果没有case可运行,如果有default,就执行default,否则它将阻塞,直到有case可运行.专门用于通道操作.
    select {
    	case chan读/写:
    	case chan读/写:
    		.....
    	default:
    	}
    示例:
    ch1 := make(chan int)
    	ch2 := make(chan int)
    	go func(){
    		time.Sleep(3*time.Second)
    		ch1 <- 100
    	}()
    	go func(){
    		time.Sleep(3*time.Second)
    		ch2 <- 200
    	}()
    	select {
    	case data:=<-ch1:
    	 	fmt.Println("ch1中读取数据",data)
    	case data:= <-ch2:
    	 	fmt.Println("ch2中读取数据",data)
    	default:
    		fmt.Println("执行了default......")
    	}
    ```

    ### 37. sync同步

    ```go
    同步:sync  一个接一个
    异步:async 同时执行
    waitgroup:同步等待组:
    内置计数器:要执行的goroutine的数量;
    add(-3),设置计数器的数量
    Done(),将计数器的 数值减1,同add(-1)
    wait(),等待,导致执行wait的goroutine进入阻塞状态.同步等待组中的计数器的数值为0解除阻塞.
    ```

    ```go
    func main(){
     //同步等待组
     var wg sync.WaitGroup
     wg.Add(2)

      go num1(&wg)
      go num2(&wg)
      //time.Sleep(2*time.Second)
      wg.Wait()
     fmt.Println("main...解除阻塞..结束")
    }

    func num1(wg *sync.WaitGroup){
       rand.Seed(time.Now().UnixNano())
       for i:=1;i<=100;i++{
          fmt.Println("子goroutine1#中i",i)
          time.Sleep(time.Duration(rand.Intn(1000)))
       }
       wg.Done()
    }
    func num2(wg *sync.WaitGroup){
       for j:=1;j<=100;j++{
          fmt.Println("\t子goroutine2#中j",j)
          time.Sleep(time.Duration(rand.Intn(1000)))
       }
       wg.Done()
    }
    ```

    ### 38.互斥锁

    ```go
    MUTEX互斥锁:
    lock:上锁
    unlock:解锁
    ```

    ```go
    func main()  {
    	 var mutex sync.Mutex
    	 fmt.Println("main即将锁定")
    	 mutex.Lock()
    	 fmt.Println("已经锁定")
    	 for i:=1;i<=3;i++{
    	 	go func(i int){
    	 		fmt.Println("goroutine",i,"即将锁定mutex")
    	 		mutex.Lock() //导致阻塞
    	 		fmt.Println("goroutine",i,"已经锁定")
    		}(i)
    	 }
    	 time.Sleep(5*time.Second)
    	 fmt.Println("main..即将解锁")
    	 mutex.Unlock()
    	 fmt.Println("main已经解锁")
    	time.Sleep(3*time.Second)
    }
    ```

    ### 39. 读写锁

    ```go
    sync.RWMutex 将读与写动作分开
    Lock():写锁定
    Unlock():写解锁
    RLock():读锁定
    RUnlock():读解锁
    规则:读写锁的使用中,写操作都是互斥的;读与写互斥,读与读不互斥;
    ```

    ### 40. 条件变量

    ```go
    sync.Cond:多个goroutine等待或接收的集合地.
    Wait():等待接收通知
    Single():发送一个通知
    Broadcast():广播,发送给所有人,解除阻塞
    ```

    ```go
    示例:
    func main(){
       //条件变量
       var mutex sync.Mutex
       cond := sync.Cond{L:&mutex}
    	condition := false
       go func(){
       	time.Sleep(1*time.Second)
       	cond.L.Lock()
       	fmt.Println("字goroutine已经锁定")
       	fmt.Println("字goroutine更改条件数值,并发送通知")
       	condition = true
       	cond.Signal() //发送通知 一个
       	time.Sleep(5*time.Second)
       	fmt.Println("字goroutine继续")
       	fmt.Println("字goroutine解锁")
       	cond.L.Unlock()
       }()
    	
       cond.L.Lock()
       fmt.Println("main....已经锁定")
       if !condition{
       	fmt.Println("main...即将等待")
       	//尝试解锁...等待
       	cond.Wait()
       	fmt.Println("main...被唤醒")
       }
    	fmt.Println("main...继续工作")
    	fmt.Println("main...解锁")
    	cond.L.Unlock()
    }

    ```

    ### 41. hyperledger环境搭建

    ```

    ```

    ### 42.









