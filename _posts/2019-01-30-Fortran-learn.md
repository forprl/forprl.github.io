---
layout: post
title:  "Fortran 学习笔记"
date:   2019-01-30 12:12:00 +0800
categories: Fortran
tags:  Fortran
author: cndaqiang
mathjax: true
---
* content
{:toc}

虽然整理成博客，很浪费时间，不过，每次忘记后，再捡起来，还要好久，还是继续把笔记整理成文章<br>
该文不完全，看siesta代码中遇到其他的语法再添进来<br>






## 参考
Fortran95程序设计【彭国伦】<br>
[《Fortran实用编程》系列视频教程 - Fortran Coder 研讨团队](http://v.fcode.cn/)<br>
[详论fortran格式化输出](http://blog.sciencenet.cn/blog-287062-269811.html)



## 语法规则
代码结构<br>
语句 =>**程序单元(主程序program,子例程序subroutine,函数function )** => 模块（module） => 程序
### 语句规则和特点

|        |           固定格式           |           自由格式           |
| ----- |: -------------------------- :| :-------------------------- :|
| 英文   |         Fixed-format         |         Free-format          |
| 扩展名 |      `.for      .f   ... `     |    `.f90   .f95   .f03 ... `   |
| 语法   | F66、F77、F90、F95、F03、F08 | F66、F77、F90、F95、F03、F08 |
| 格式   |       代码从第7格开始        |             任意             |
| 续行   |    在第6格键入一个非0字符    | 上一行结束下一行开头加入   & |
| 行宽   |              72              |             132              |
| 注释   |    行首打  C 或    c 或 `* `   |      注释前打感叹号   !      |
| 说明   |        不推荐，已废止        |             推荐             |

- 不区分大小写，字符串的里的大小写区分(ASCII码不同)
- 语句结束时不用添加结束标志
- 编译器可取消行宽限制`gfortran -ffree-line-length-none` 
- 编译器向下兼容Fortran语法
- 数组下标从1开始
- 函数子例程序都是传址调用
- 程序单元内声明语句必须放在执行语句前
- 变量/数组的定义和调用直接用变量名
- 编译器默认根据拓展名判断语法格式![](/upload/2019/01/fortran.png)

### 程序单元
程序单元可以写在不同的文件内进行编译，最后把分别编译的Obj链接成一个可执行文件与把他们写在一个文件里面编译等价
- 写在不同的文件内，可以提高编译速度，修改部分代码，仅需编译修改代码所在文件

主程序program，有且仅有一个，作为程序入口<br>
子例程序subroutine，没有返回值的函数

### 模块
一组程序单元及一组相关联的变量，可组成模块（module）



## 数据类型
默认ijklmn开头的变量为为整型，其他为实型，通过下面命令取消此默认规定
```
Implicit None 
```
### 变量定义
常用:**类型( 属性 ) , 形容词 , 形容词 ...  :: 变量名（数组外形）= 值 , 变量名2（数组外形）= 值**

**定义多个变量用`,`隔开，用空格不行**



### 类型

- 整型（Integer）
- 实型（Real）
- 复数Complex 类型（本质上是real）
- 字符型（Character）
- 派生（type）类型

例
```
Real(Kind=8) , parameter , private :: rVar = 20.0d0
Character(Len=32) , Intent( IN ) :: cStr(5,8)
Integer , save :: n = 30 , m = 40
Integer m
complex :: com
```

### 属性

####  数值型kind
用于解决不同编译器默认表达范围不一致问题<br>
整形最大可表示i位示例
```
! k = Selected_Int_Kind( i )  可以用这个函数来选择能满足要求的Kind
! i 表示需要最大的十进制位数
! k 表示返回的能满足范围的最小的Kind值	
! Selected_Int_Kind( i )函数可以放在声明语句里
integer , parameter :: KI=selected_int_kind(10)
integer(kind=KI) :: i1 i2 i3
```
实数精度
```
! k = Selected_Real_Kind( r , p )  可以用这个函数来选择能满足要求的Kind
! r 表示需要最大的十进制位数 , p 表示最小的有效位数 p位*E^r
! k 表示返回的能满足范围的最小的Kind值
integer , parameter :: DP=selected_real_kind(r=50,p=14)
real(kind=DP) :: r1 r2
```
在数值后面加kind数值，表明数值类型，如siesta

```
! Initialize some variables(Double precision:dp=8)
      DUext = 0.0_dp
      Eharrs = 0.0_dp
      Eharrs1 = 0.0_dp
```

complex与实数kind一样

#### 字符型len
kind默认为1(ASCII码格式)，也可通过Selected_Char_Kind( 'ASCII' )确定<br>
len表示长度
```
character(len=32) :: str
```

###  形容词
parameter 常量

| 形容词    | 功能                                                     |
| --------- | -------------------------------------------------------- |
| parameter | 常量                                                     |
| save      | 函数内定义，当函数再次被调用时，使用上次该函数被调用的值 |



### 数值型

#### 赋值

```
complex :: com
com=(实部,虚部)
```

#### 运算

直接`+ - * \( )`

乘方`**`



### 字符串
声明
```
character(len=字符串长度) :: str
```
赋值与调用，**下标从1开始**
```
character(len=7) ::str
str="hello!!"
!gfortran 对这种写法报错 str(2)="12345"
!str(n:m)即可用于赋值，也可用来掉用
!str等价str(1:len(str))
!str(n:)等价str(n:len(str))
str(2:3)="tr"
write(*,*) str(3:3)
```
字符串函数

| 函数           | 功能                                                         |
| -------------- | ------------------------------------------------------------ |
| CHAR(num)      | 返回数值num对应的ASCII码字符                                 |
| ICHAR(char)    | 字符转数字                                                   |
| LEN(str)       | 字符串长度                                                   |
| LEN_TRIM(str)  | 去除字符串尾部的空格的字符串长度(实际长度，例如后面几个没赋值) |
| INDEX(str,key) | str中第一次出现字符串key的位置<br>可用于文件读取时，由某个数据的前后索引，确定数据的下标 |
| TRIM(str)      | 清除str尾部的空格后的字符串                                  |



### 数组

#### 定义

```
类型,形容词 :: 数组名(n1,n2,n3...维度)
类型,形容词 :: 数组名(n0:n1,m0:m1,...) 也可以，n0,m0也可从负数开始
```

**动态数组**

加上`allocatable`形容词

```
类型,allocatable,其他形容词 :: 数组名(:,:,:....) !用:
!分配大小
allocate(array(N))
!释放空间
deallocate(array)
```

内存中的存储顺序为array(1,1,1)->array(2,1,1)->array(3,1,1)->array(1,2,1)<br>
所以`do r=1,n1 sum=sum+array(r,1,1) `比`do r=1,n1 sum=sum+array(1,1,r) `的语法就是最快的<br>
同理，不建议高维数组



#### 赋值

```
integer :: array(5) A(2,3)
!使用data赋值，后面的值要和前面的一一对应
array(1)=1
array(2:5)=3
data array /1,2,3,4,5/
data array /5*5/   !*表重复 /5,5,5,5,5/
data((A(i,j),i=1,2)j=1,3) /1,2,3,4,5,6/
！(f(I),i=1,5)就代表一组循环,i从1到5，输出f(i)，例如
A=(/(I*I,I=1,6)/)
```

#### 调用

```
!可直接进行调用或赋值
array(n:m)
array(n:)
array(:)
A(:,:)
```

#### 运算

`+-*/function()` 都是对应元素操作，不是数学矩阵操作

`> <`返回逻辑值，也是对应元素比较

### 结构体 TYPE

#### 定义

结构体，type内只有变量<br>type内含有方法(函数)时，就是类了->面向对象编程了<br>

定义结构体类型

```fortran
TYPE ,形容词 :: 结构体名
变量表(声明语句)
END TYPE
```

将结构体实体化，也可实体化为数组

```
TYPE(结构体名) :: 变量名
```

示例

```
      type :: student
              character :: nickname,address
              integer :: num,score
      end type

      type(student) :: xiaoming
```

#### 调用

```
结构体名%成员名 ！优先使用
结构体名.成员名 !gfortran好像不支持
xiaoming%nickname
xiaoming.num
```

#### 赋值

```
TYPE(student)::xiaoming=student (“xiaoming","china",1,90), S2, S3
xiaoming.score=95
```

### 类

类是在结构体的基础上，面向对象的拓展









## 流程控制 IF SELECT
### IF
单行版
```
IF(条件) 执行语句
```
多行版
```
IF (条件) THEN
执行语句
ELSE IF (条件) THEN
执行语句
ELSE IF (条件) THEN
执行语句
ELSE
执行语句
END IF
!ELSE IF与ELSE可不写
```

### SELECT
```
SELECT case (表达式)
case(A)
执行语句
case(B)
执行语句
END SELECT
!好像表达式结果只能为整数或字符串，A,B...也要对应
```

## 循环
###  CYCLE 进入下一循环
###  EXIT 结束循环
### DO
```
DO i=start,end [,step]
!步长step默认为1，可设置为正,负
！不写i=1,end 无穷循环
执行语句
END DO
```
### DO WHILE()
```
DO WHILE(条件)
执行语句
END DO
```



## 格式化输入输出

### READ(unit=设备号/字符串名称,fmt=格式或行号) 格式化输入

### WRITE(unit=设备号/字符串名称,fmt=格式) 格式化输出

- 默认设备是键盘，用`5`或`*`表示,还可以是外部文件（open文件时指定设备号）
- 输入输出为字符串时，等价于将字符串视为文件，称为内部文件<br>实现整数/实数<=>字符串
- 格式适合一次控制，`"格式内容"`
- 行号可以在很多次输出都采用统一格式时，减少书写量
- `READ(设备号,格式行号)`也可以
- `READ(*,*)`  `*`分别表示默认键盘和不指定格式
- `READ(*,*) a,b,c` 一次读取多个数据赋值给a,b,c

### 格式化输出控制

| 格式       | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| `Iw[.m]`   | 以w个字符的宽度来输出整数[至少输出m个数]，宽度不够补空格，以下同理 |
| `Fw.d`     | 以w个字符文本框来输出浮点数，小数部分占d个字符宽，           |
| `Ew.d[Ee]` | 用科学计数法，以w个字符宽来输出浮点数，小数部分占d个字符宽，[指数部分最少输出e个数字] |
| `Dw.d`     | 使用方法同Ew.d，差别在于输出时用来代表指数的字母由E换成D     |
| `Aw`       | 以w个字符宽来输出字符串                                      |
| `nX`       | 输出位置向右移动n位。`write(*,"(5X,I3)") 100 `; 将先填5个空格，再输出整数。 |
| `Lw`       | 以w个字符宽来输出T或F的真假值。`write(*,"(L4)") .true. `;程序会输出3个空格和一个T |
| `/` 换行输出 | `write(*,"(I3//3)") 10,10` 程序会得出4行，中间两行空格是从除号`/`得到的。 |
| `Tc` | 把输出的位置移动到本行的第c个字节 |
| `TLn` | 输出位置向左相对移动n个字节。 |
| `TRn` | 输出位置向右相对移动n个字节。 |
| `SP、SS` | 加了SP后，输出数字时如数值为正则加上`+`，SS则是用来取消SP的功能。   如 `write(*,"(SP , I5 , I5 , SS , I5)") 5 , 5 , 5` 输出：`+5   +5   5` |

示例

```
     write(*,100) 5
100  format(I5)
     write(*,"(spi5/ssf10.2)") 34 , 23.456
```
结果
```
    5
  +34
     23.46
```

## 函数

- 函数与主程序program并列
- 主程序和函数无先后次序
- function 与subroutine除了返回值，和声明方式，没有区别
- 函数都是传址调用

### function

#### 定义

```
[形容词][,返回类型] function 名称([虚参列表])
	[虚参声明]
	[局部变量定义]
	执行语句
	名称=返回值
	[return]
End [function [名称]]
```

#### 使用

用前要声明

- `external`
- `interface`
- `contains`包含在程序单元内
- 用`module`引入

```
返回变量=名称([实参列表])
```

### subroutine

#### 定义
```
[形容词] subroutine 名称([虚参列表])
	[虚参声明][局部变量定义]
	执行语句
	名称=返回值
	[return]
End [subroutine [名称]]
```
#### 使用

直接`call`

```
call 名称([实参列表])
```

### 使用前的声明

使用function前用`external`声明，或使用`interface`声明

```
program externalOrinterface
      implicit none
      real :: x,y
      real ,external :: fun !函数要声明
      x=1.0
      y=fun(x)
      write(*,*) y
      call sub(x)
end program externalOrinterface

real function fun(x)
      real :: x
      fun=x*x+1.0
end function fun

subroutine sub(x)
        real :: x,y
        y=x*x+1.0
        write(*,*) y
end subroutine sub
```

#### iterface

fun也可使用`interface` 代替`external`，有些特殊用法需要用interface声明

````
      interface 
             real function  fun(x)
                     real :: x
             end function fun
      end interface
````

使用以下用法时，必须使用 interface：

• 函数返回值是数组、指针

• 参数为假定形状数组

• 参数具有 intent、value 属性

• 参数有可选参数、改变参数顺序

以下用法时，虽然不强制要求，但也推荐使用 interface

• 函数名作为虚参和虚参

实际上，我们建议在任何函数调用时，都使用接口！ 



**每个程序单元调用其他函数时都要interface，用module可以减少书写那么多interface**

#### contains

在程序单元(主程序，function，subroutine)内使用`contains`，contains后面跟的函数，仅可以被此程序单元调用，**且不用声明**

`module`内部的函数也用`contains`，`use module_name`后，可以调用

`contains`后面的函数可以直接调用程序单元和`module`里的变量

```
real function fun(x)
      implicit none
      real ::x,y
      y=23.0
      fun=x*3.0
      fun=two(fun)
contains      !使用contains
      real function two(x)
        real ::x
        two=x*2.0+y !可以直接调用程序单元或module里的变量
    end function two
end function fun 
```

#### module 内部函数互相调用不用声明

### 参数传递：在函数内解释参数

#### 传递字符串

**声明时`len=*`**，siesta得代码中常见

```
program strsub
      implicit none
      character(len=20)::str
      str="hello,world!"
      call sub(str)
      contains
              subroutine sub(strr)
                      implicit none
                      character(len=*) :: strr
                      write(*,*) trim(strr)
              end subroutine sub
end program strsub
```





#### 传递数组-数组地址维度大小都会传过来

推荐方式

```
program pro
      implicit none
      real :: array(10),total
      integer I
      array=(/(I,I=1,10)/)
      total=sum(array(2:3))
      write(*,*) total
end program pro

real  function sum(a)
     real::a(:)   !多维时a(:,:)，传入参数的下标变为1,2,3...,size(a,dim=n)，上限自动计算，下线也可指定
     !real::a(n)取出传过来的数组的前n个元素作为一个数组
     integer :: i
     sum=0
     do i=1,size(a,dim=1)
      sum=sum+a(i)
     end do
end function sum
```

也可以把数组维度作为参数传入

**也有用`real :: a(*)`，老程序中有，虚参只能为1维,**

#### 传递结构体-用module

主程序和子程序需使用同一个结构体定义，使用module

```
module typedef
      implicit none
      type :: student
              character(len=10) :: nickname
              integer :: num,score
      end type
end module typedef

program cndaqiang
      use typedef
!use module名 要在其他语句之前
      implicit none
      type(student)::xiaoming
      xiaoming%nickname="xiaoming"
      xiaoming%num=1
      xiaoming%score=95
      call sub(xiaoming)
end program

subroutine sub(who)
        use typedef
        implicit none
        type(student)::who
        write(*,*) who%nickname(:),who%num
end subroutine

```

#### 传递函数

在函数体内用`interface`声明传入参数的类型

```
module mod_fun
    !real :: x,y
contains
real function fun(test,x)
      implicit none
      interface !函数作为参数时，要用interface声明(解释)函数类型
      real function test(x)
      real ::x
      end function
      end interface
      real :: x
      fun=test(x)+x
end function fun      

real function test(x)
      implicit none
      real :: x
      test=x*x+x
end function test

end module mod_fun

program inter
    use mod_fun   !使用module导入的函数，不用声明
    implicit none
    real :: x,y
    x=3.0
    y=fun(test,x)
    write(*,*) y
end program inter
```



### 特殊用法

#### save属性

函数执行完后，空间不释放，下次执行该函数时作为初值

```
Integer , save :: var
Integer :: var = 0  !// 虽然没有书写 save ，但定义时初始化值，有具有 save 属性
```

#### 虚参的 Intent 属性（需要 Interface）

明确指定虚参的目的：输入参数、输出参数、中性参数

```
!//输入参数，在子程序内部不允许改变  
Integer , Intent( IN ) :: input_arg   
!//输出参数，子程序返回前必须改变（对应实参不能是常数，也不能是表达式）
Integer , Intent( OUT ) :: output_arg 
!//中性参数
Integer , Intent( INOUT ) :: neuter_arg
Integer :: neuter_arg !// 未指定 Intent 则为中性
请注意：Intent 的检查是在编译时进行，而非运行时检查
建议对每一个虚参都指定 Intent 属性！
```

#### 虚参的 value 属性（**需要 Interface**）
指定该参数为传值参数，而非传址参数。

```
!//传值参数，只能作为输入参数。改变后不影响实参。  
Integer , value :: by_value_arg

请注意：除混编之外，一般不使用 value 属性
```

#### 可选参数optional（**需要 Interface**）

虚参可传可不传

```
program op
      call opop(1)
      call opop()

contains
subroutine opop(a)
      integer,optional ::a   !a为可选参数
      if (present(a)) then
              write(*,*) "a=",a
      else
              write(*,*) "none"
      end if
end subroutine opop

end program op
```

#### 更改参数顺序（需要 Interface）

更改参数顺序：即，函数的实参、虚参可以不按照顺序对应。

```
call writeresult( Data = var , file = "res.txt" , size = 1000 ) 
call writeresult( var , 1000 , "res.txt") 
```

#### result 指定返回值变量

````
real function jifen(fun,down,up,step) result(y)
````

函数体内不能再次声明y(已经在定义时生命力)



#### pure 并行有关，暂略



## 数据共享

### 函数的传址调用

### common-不推荐-略

在程序单元内，用`common 变量列表`

变量名不必一致，按顺序排列

就是划了一块公共的内存区域，不同的程序单元内按顺序读取

### module 见下

## module：数据/函数(过程)共享

- module 中可以定义若干变量、若干函数
- 子程序可以使用module内的变量，子程序内部局部变量互相隔离
- module内部程序可以互相调用，不用interface
- 所有 use 了这个 module 的程序单元，可以自由使用 public 的变量和函数、只读的使用 protected 的变量<br>private 的变量和函数，仅供 module 内部使用
- **先将module所在的文件，编译成对象文件`xxx.o`和模块描述文件`模块名.mod`，在将对象`xxx.o`和主程序连接**

### 定义

** `module`内部不能有执行语句 **，初值可在定义时赋予

```
module 名称
implicit none
变量声明
!不能写任何执行语句
contains
函数定义
end module 名称
```

### 使用

```
use module名称
!也可以使用module了内的某些，如下
use smartHome, only : tv , pc
use smartHome, only : screen => tv , b ！使用并重命名
```

**module内也可以use其他module，实现继承**



### 权限形容词，在`contains`前写

|           | 变量                    | 子程序          |
| --------- | ----------------------- | --------------- |
| public    | 外部可以读取   可以修改 | 外部可以   调用 |
| protected | 外部只能读取   不能修改 | --              |
| private   | 外部无法访问            | 外部无法调用    |

默认全是`public`，更改默认为`Private`，在`implict none`和声明语句之间加一行`Private`默认全私有

例

```
module m_init
      private
      public :: init1,init2
      contains
              subroutine init1()
                      write(*,*) "init1 begin"
              end subroutine init1
              subroutine init2()
                      write(*,*) "init2 gegin"
              end subroutine init2
end module m_init
```





## 文件读写

- Open  打开文件
- Write  写入
- Read  读取
- Close  关闭文件

### open(通道号,file="文件名")

```
Open( 子句 = 值 , 子句 = 值 , 子句 = 值 )
！它有二十多个子句，每一个都有各自的作用
!真正有必要的，只有两个：
Open( Unit = 通道号 , File = "文件名" )
Open( 通道号 , File = "文件名" )
!文件通道号，由程序员给定，一般用大于10的数字。
!10以下的数字由不同编译器预留（一些编译器用5，6表示标准输入输出）
```

### read

```
read(通道号,格式) 变量列表
read(字符串,格式) 变量列表 ！可将一行数据用字符纯的方式读入后，在处理字符串内的数据
```



|          | 文本文件   （有格式文件）                | 二进制文件   （无格式文件）                                |
| -------- | ---------------------------------------- | ---------------------------------------------------------- |
| 顺序读取 | **顺序读取有格式文件   （用得最多）   ** | 顺序读取无格式文件   (在记录前后各增加4字节，表示记录长度) |
| 直接读取 | 直接读取有格式文件   （要求每行一样长）  | **直接读取无格式文件   （用得较多）**                      |
| 直接读取 | 直接读取有格式文件   （要求每行一样长）  | 直接读取无格式文件   （用得较多）                          |



#### 顺序读写有格式文件（文本文件）

 **星号，表示表控格式（list-direct），既让变量列表自动控制格式，空格和逗号分隔数据**

也可以使用其他格式，多用`*`

如果不特殊指定格式，那么每个read 语句读取整的 N 行，即读取了3行中间，下次直接从第四行开始读

**推荐：一次只读取一行**，

不写参量列表，也会读取，可用于跳过一行

赋给无用变量，可用于跳过某一列

**可以读到字符串型中，再从字符串中读取**

如,file.txt

```
11 12 13
21 22 23
31 32 33
```

程序

```
program re
      implicit none
      real :: a,b,c,d,e,f
      integer :: i
      i=20
      open(i,file="file.txt")
      read(i,*) a,b,c,d !读到了1行多（即，第二行）
      read(i,*) e,f     !下一次从第三行读取
      write(*,*) a,b,c,d
      write(*,*) e,f
      close(i)
end program re      
```

结果

```
   11.0000000       12.0000000       13.0000000       21.0000000
   31.0000000       32.0000000
```

#### 直接读写有格式文件（文本文件）

**需固定长度**

```
open(unit=通道号,form="formatted"，access="Direct",RecL=64)	
```

- form="formatted"：指定它是有格式文件（文本文件）<br>  在顺序读取时，它是默认值，因此可以不指定

- access="Direct"：指定它是直接读取方式<br>顺序读取时，可以指定'SEQUENTIAL'，它是默认值，因此可以不指定

- RecL=64：指定记录长度（Record Length）是 64（字节）<br> 仅在直接读取时指定。



#### 顺序读写无格式文件（二进制文件）

```
      open(unit=i,file="file.bin",form="unformatted")
      write(i) a,str 
```

**因为是无格式，read和write里面只有unit，没有格式控制**

Fortran顺序写入无格式文件，以记录为基本单元，读写过程分若干笔记录：

每次一笔记录，在记录前后各多出4个字节，用来记录本次写入的长度。

顺序读取时，通过这4个字节知悉当前记录的长度，按照长度读取数据，再与最后的4个字节进行校对

示例

```
program re
      implicit none
      integer :: a,b,i
      character(len=5) :: str
      a=1
      b=2
      str="hello"
      i=20
      open(unit=i,file="file.bin",form="unformatted")
      write(i) a,str !4+5=9个数据
      write(i) b
      close(i)
      a=0
      b=0
      str="00000"
      open(unit=i,file="file.bin",form="unformatted")
      read(i) a,str  !读的时候，也要按相应数据长度
      read(i) b
      close(i)
      write(*,*) a,str,b
end program re

```

对应的二进制文件

![](/uploads/2019/01/file.jpg)

#### 直接读写无格式文件（二进制文件）

```
      open(unit=i,file="file.bin",form="unformatted",access="direct",recl=10)
      write(i,rec=1) a,str
```

- `recl`指定数据记录长度
- 随便跳转到任何整记录读写，也可以一边读一边写
- 写入记录不够时，会用0填满后面的（即使之前里面非空）
- 读取时，不一定按照写入时候的顺序，一个数据长度，爱咋读咋读


![](/uploads/2019/01/file2.jpg)

#### 流文件读写无格式文件（二进制文件）

**按照给定参数，决定读取长度**

```
open(unit=i,file="file.bin",form="unformatted",access="stream")
write(i) a,str
      
read(i,pos=5) !跳到第5个字节处开始读
inquire(i,pos=b)  !将当前位置返回到b
```

![](/uploads/2019/01/file3.jpg)

也可用于读入顺序读写的文件，无非就是在变量列表两边各添加一个n(4字节变量/整型)

#### 内部文件

就是直接读取字符串文件，把字符串当成有格式文件的一行，一次写入就写满一行，不足数据用空格补全

同样也可以读，实现字符串和数值的互转

结合字符串，格式化读写的相关操作

**可用于先将用户输入数据读入字符串，在判断字符串里的内容是否符合规则：整/实数，避免输入不符造成程序终止**

示例

```
program str
      implicit none
      integer::filenum(5)
      integer::i,uid
      character(len=10) :: filename
      data filenum /1,12,123,1234,12345/
      uid=20
      do i=1,5
      write(filename,"(i6.5a4)") filenum(i),".txt" !把str当成文件来读写
      open(uid,file=filename)
      write(uid,*) filenum(i)
      close(uid)
      end do
end program str
```

生成

```
' 00001.txt'  ' 00012.txt'  ' 00123.txt'  ' 01234.txt'  ' 12345.txt'
```

示例

```
program now1
      implicit none
      character(len=255)  :: now,r
      integer::hh,mm,ss
      call date_and_time(time=now)   !获取系统时间函数
      read(now,"(i2i2i2)") hh,mm,ss
      write(*,"(a8,i2,a1,i2,a1,i2)") "now is: ",hh,":",mm,":",ss
end program now1
```

结果

```
now is: 11:52:26
```













## 有趣的命令

### 调用系统shell命令

[9.264 `SYSTEM` — Execute a shell command](https://gcc.gnu.org/onlinedocs/gfortran/SYSTEM.html)

```
system("command"[,]状态)
```

command为字符型变量，状态为整形，执行正常，状态变量被赋予0

执行后，命令结果被输出到屏幕

例

```
call system("date",hh)
```



### 时间命令

[9.82 `DATE_AND_TIME` — Date and time subroutine](https://gcc.gnu.org/onlinedocs/gfortran/DATE_005fAND_005fTIME.html)

`DATE_AND_TIME(DATE, TIME, ZONE, VALUES)` gets the corresponding date and time information from the real-time system clock. 

- DATE is`INTENT(OUT)` and has form ccyymmdd.
-  TIME is `INTENT(OUT)` and has form hhmmss.sss. 
- ZONE is `INTENT(OUT)` and has form (+-)hhmm, representing the difference with respect to Coordinated Universal Time (UTC). 
- VALUE 参考上面给的参考链接，此处略

其他时间[CPU_TIME](https://gcc.gnu.org/onlinedocs/gfortran/CPU_005fTIME.html#CPU_005fTIME)与[SYSTEM_CLOCK](https://gcc.gnu.org/onlinedocs/gfortran/SYSTEM_005fCLOCK.html#SYSTEM_005fCLOCK)

### 暂停一段时间再执行

```
call sleep(n) !暂停n秒
```





