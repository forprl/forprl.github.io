---
layout: post
title:  "MPI编程入门(Fortran)"
date:   2019-02-27 8:51:00 +0800
categories: Fortran
tags:  Fortran mpi
author: cndaqiang
mathjax: true
---
* content
{:toc}

MPI(openmpi)+Fortran(mpif90)<br>
不讲原理，只写代码，边用边补充




## 参考
⭐⭐[莫则尧, 袁国兴. 消息传递并行编程环境 MPI[M]. 科学出版社, 2001.](/web/file/2019/mzy_MPI.pdf)<br>
[Fortran 学习笔记])(/2019/01/30/Fortran-learn/)<br>
[USTC超算中心:MPI分布内存并行程序开发-1.ppt](https://scc.ustc.edu.cn/_upload/article/files/e0/98/a9f0c4964abdb3281233d7943f9e/W020100308601033034327.ppt)<br>
[USTC超算中心:MPI分布内存并行程序开发-2.ppt](https://scc.ustc.edu.cn/_upload/article/files/e0/98/a9f0c4964abdb3281233d7943f9e/W020100308601033282447.ppt)<br>
[Open MPI v1.10.1 documentation](https://www.open-mpi.org/doc/v1.10/)<br>
[潘建瑜:MPI 编程基础](http://math.ecnu.edu.cn/~jypan/Teaching/ParaComp/mpi_lect_jypan.pdf)

## MPI
MPI不是一门语言，而是给Fortran或C等语言提供并行通信协调各个进程的功能<br>
编译，运行
```
[cndaqiang@mu01 mpi]$ mpif90 pi.f90 
[cndaqiang@mu01 mpi]$ mpirun -np 5 a.out 
```
## 语法
语法速查[Open MPI v1.10.1 documentation](https://www.open-mpi.org/doc/v1.10/)

- MPI的所有常量、变量与函数 /过程均以MPI_ 开头
- MPI 的 C 语言接口为函数， **FORTRAN 接口为子程序 **，且对应接口的名称相同
- Fortran仅有两个函数不是子程序`MPI_WTIME() 和 MPI_WTICK()`
- 在C程序中，所有常数的定义除下划线外一律由大写字母组成，在函数和数据类型定义
中, 接MPI_ 之后的第一个字母大写，其余全部为小写字母，即MPI_Xxxx_xxx形式
-  **对于FORTRAN 程序， MPI 函数全部以过程方式调用，一般全用大写字母表示，即
MPI_XXXX_XXX形式（ FORTRAN不区分大小写）`call MPI_XXXX_XXX(一堆参数,ierr)`**
-  除 MPI_WTIME 和 MPI_WTICK 外，所有C函数调用之后都将返回一个错误信息码，
而 MPI 的**所有 FORTRAN 子程序中都有一个integer类型的输出参数（ IERR）代表调用错误码**
-  MPI 是按进程组(Process Group) 方式工作的，所有MPI 程序在开始时均被认为是在通信
器MPI_COMM_WORLD 所拥有的进程组中工作，之后用户可以根据自己的需要，建立
其它的进程组
-  所有MPI 的通信一定要在通信器(communicator) 中进行

### MPI程序流程
![](/uploads/2019/02/mpi.JPG)

- 先引用`mpif.h`头文件
- `call MPI_INIT(ierr)`进入MPI环境
- `call MPI_FINALIZE`结束MPI环境
- 在MPI环境中，各个进程均分别执行该部分代码
- 各个进程间数据独立，通过通信传递数据
  

代码示例
```
program jifen_mpi
implicit none
include 'mpif.h'
integer :: node,np,ierr,status(mpi_status_size)
logical :: Ionode
integer :: n,i
real ::a,b,x,h,jifen,pi

call MPI_INIT(ierr)
call MPI_COMM_RANK(MPI_COMM_WORLD,node,ierr)
call MPI_COMM_SIZE(MPI_COMM_WORLD,np,ierr)

Ionode=(node .eq. 0)

if (Ionode) then
    write(*,*) "Plese input n:"
    read(*,*) n
    do i=1,np-1
        call MPI_SEND(n,1,MPI_INTEGER,i,99,MPI_COMM_WORLD,ierr)
    end do
else
    call MPI_RECV(n,1,MPI_INTEGER,0,99,MPI_COMM_WORLD,status,ierr)
end if

a=0.0
b=1.0
h=(b-a)/n
jifen=0.0

i=node+1
do while(i <= n)
x=h*(i*1.0-0.5)
jifen=jifen+4.0/(1.0+x*x)
i=i+np
end do

if (Ionode) then
do i=1,np-1
call MPI_RECV(sum_n,1,MPI_REAL,i,100,MPI_COMM_WORLD,status,ierr)
jifen=jifen+sum_n
end do
jifen=jifen*h
pi=3.141592653
write(*,"(f12.10)") pi
write(*,"(f12.10)") jifen

else
call MPI_SEND(jifen,1,MPI_REAL,0,100,MPI_COMM_WORLD,ierr)
end if

call MPI_FINALIZE(ierr)

end program jifen_mpi
```

### call MPI_INIT(ierr) 
进入MPI环境，ierr为`integer`型变量，执行成功返回0
### call MPI_FINALIZE(ierr)
退出MPI环境，ierr为`integer`型变量，执行成功返回0
### 获得系统/进程信息
#### call MPI_COMM_RANK(comm,node,ierr)
- comm通信器名，`integer`型变量，系统自动创建通信器`MPI_COMM_WORLD`
- node，`integer`型变量，返回当前进程序号，取值范围0，1，2，3...进程数-1
- ierr为`integer`型变量，执行成功返回0

#### call MPI_COMM_SIZE(comm,np,ierr)
- comm通信器名，`integer`型变量，系统自动创建通信器`MPI_COMM_WORLD`
- np，`integer`型变量，返回总进程数
- ierr为`integer`型变量，执行成功返回0

#### call MPI_GET_PROCESSOR_NAME(name,namelen,ierr)进程所在机器名
在多节点运行时，返回节点的名称存储在name中
名称长度返回在namelen中
```
character(len=MPI_MAX_PROCESSOR_NAME) :: name
integer::namelen
call MPI_GET_PROCESSOR_NAME(name,namelen,ierr)
```
#### call MPI_GET_VERSION(VERSION, SUBVERSION, ierr) mpi版本 
```
MPI_GET_VERSION(VERSION, SUBVERSION, IERROR)
    INTEGER    VERSION, SUBVERSION, IERROR
```
### 点对点通信
进程与进程间的通信

#### 函数总览

阻塞型：当前发送必须完成，或者备份到缓存区后才执行下一条语句<br>
非阻塞型：MPI系统在后台执行发送语句，程序先执行后续语句，需检测到发送/备份完成后在对相应数据行进更改

通信模式
- 标准模式
<br>1)发送数据量小时，先存入缓冲区，发送操作执行完成，MPI后台将缓冲区的数据发送给接收进程
<br>2)发送数据量大是，等待接收操作启动，发送数据直至发送操作完成
<br>缓冲区大小由MPI决定
- 缓冲模式(使用缓冲区的标准模式)
<br>用户定义、使用和回收缓冲区，将数据存入缓冲区，不管接收操作是否启动，发送操作都可以执行，但是必须保证缓冲区可用
- 同步模式(不使用缓冲区的标准模式)
<br>必须等到接受开始启动发送才可以返回
- 就绪模式
<br>只有当接收操作已经启动时，才可以在发送进程启动发送操作，否则发送将出错


| 函数类型      | 通信模式     | 阻塞型           | 非阻塞型           |
| ------------- | ------------ | ---------------- | ------------------ |
| 消息发送函数  | 标准模式     | **MPI_SEND**         | MPI_ISEND          |
|               | 缓冲模式     | MPI_BSEND        | MPI_IBSEND         |
|               | 同步模式     | MPI_SSEND        | MPI_ISSEND         |
|               | 就绪模式     | MPI_RSEND        | MPI_IRSEND         |
| 消息接收函数  |              | **MPI_RECV**         | MPI_IRECV          |
| 消息检测函数  |              | MPI_PROBE        | MPI_IPROBE         |
| 等待/查询函数 |              | MPI_WAIT         | MPI_TEST           |
|               | MPI_WAITALL  | MPI_TESTALL      |                    |
|               | MPI_WAITANY  | MPI_TESTANY      |                    |
|               | MPI_WAITSOME | MPI_TESTSOME     |                    |
| 释放通信请求  |              | MPI_REQUEST_FREE |                    |
| 取消通信      |              |                  | MPI_CANCEL         |
|               |              |                  | MPI_TEST_CANCELLED |


#### 发送/接收数据在内存中的地址BUF
指应用程序定义地用于发送或接收数据的地址<br>
Fortran中就是变量名/数组元素

#### 数据个数COUNT
从BUF开始发送/接收特定类型的数据个数<br>
数据类型的长度 * 数据个数的值为用户实际传递的消息长度

#### 发送的数据类型
将Fortran定义的数据发送时，要通知MPI数据类型，Fortran与MPI的数据类型对应<br>
即使用Fortran的DATATYPE定义的变量，在MPI通信时，注明MPI DATATYPE

|    **MPI DATATYPE**    |     Fortran DATATYPE |
| -------------------- | ---------------- |
| MPI_CHARACTER        | character(1)     |
| MPI_INTEGER          | integer          |
| MPI_REAL             | real             |
| MPI_DOUBLE_PRECISION | double precision |
| MPI_COMPLEX          | complex          |
| MPI_LOGICAL          | logical          |
| MPI_BYTE             | 8 binary digits  |
| MPI_PACKED | data packed or unpacked   with MPI_Pack()/MPI_Unpack |

#### 发送目的地DEST
发送进程指定的接收该消息的目的进程，也就是**接收进程的进程号**
#### 发送进程号SOURCE
接收进程指定的发送该消息的源进程，也就是**发送进程的进程号**。如果该值为MPI_ANY_SOURCE表示接收任意源进程发来的消息。
#### 发送标识符TAG
取值非负整数值（0-32767），两进程间可能进行多次通信，**发送操作和接收操作的标识符要匹配才接收**，对于接收操作来说，如果tag指定为MPI_ANY_TAG则可与任何发送操作的tag相匹配。
#### 通信器COMM
包含源与目的进程的一组上下文相关的进程集合，除非用户自己定义（创建）了新的通信因子，否则一般使用系统预先定义的全局通信因子MPI_COMM_WORLD。
#### 状态STATUS
在FORTRAN程序中，这个参数是**包含MPI_STATUS_SIZE个整数的数组**<br>
在接收操作中使用，status（MPI_SOURCE）、status（MPI_TAG）和status（MPI_ERROR）分别表示数据的进程标识、发送数据使用tag 标识和接收操作返回的错误代码。
相当于一种在接受方对消息的监测机制，并且以其为依据对消息作出不同的处理（当用通配符接受消息时）。


#### call MPI_SEND(BUF, COUNT, DATATYPE, DEST, TAG, COMM, IERROR)发送
```
MPI_SEND(BUF, COUNT, DATATYPE, DEST, TAG, COMM, IERROR)
    <type>    BUF(*)
    INTEGER    COUNT, DATATYPE, DEST, TAG, COMM, IERROR
```
从本进程的BUF变量所在的内存开始，按照DATATYPE的类型为基本单元，提取COUNT条数据发送到DEST进程，通信标签TAG，通信器COMM，执行成功IERROR返回0

#### call  MPI_RECV(BUF, COUNT, DATATYPE, SOURCE, TAG, COMM, STATUS, IERROR)接收
```
MPI_RECV(BUF, COUNT, DATATYPE, SOURCE, TAG, COMM, STATUS, IERROR)
    <type>    BUF(*)
    INTEGER    COUNT, DATATYPE, SOURCE, TAG, COMM
    INTEGER    STATUS(MPI_STATUS_SIZE), IERROR
```
从source进程，按照DATATYPE的类型为基本单元，接收COUNT条数据存储到本进程的BUF变量，通信标签TAG，通信器COMM，执行成功IERROR返回0

其他模式用法语法速查[Open MPI v1.10.1 documentation](https://www.open-mpi.org/doc/v1.10/)

### 集合通信
集合操作的三种类型：
- 同步(barrier)：集合中所有进程都到达此处后，每个进程再接着运行；
```
call MPI_BARRIER(COMM, IERROR)
```
- 数据传递：广播(broadcast)、分散(scatter)、收集(gather)、全部到全部(alltoall)

- 全局规约(reduction)：从所有进程收集数据进行计算
<br>如：求最大值、求最小值、加、乘等

**进程只有执行了集合通信语句，才会进行同步，传递，规约**

#### call MPI_BARRIER(COMM, IERROR)
用于同步
#### 广播
##### call MPI_BCAST(BUFFER, COUNT, DATATYPE, ROOT, COMM, IERROR)
将ROOT进程的BUFFER广播给所有进程的BUFFER，**变量名一致**
<br>进程只有执行了该语句，才会接收该条广播
<br>ROOT进程即发送也接收
```
MPI_BCAST(BUFFER, COUNT, DATATYPE, ROOT, COMM, IERROR)
    <type>    BUFFER(*)
    INTEGER    COUNT, DATATYPE, ROOT, COMM, IERROR
```
##### call MPI_SCATTER(SENDBUF, SENDCOUNT, SENDTYPE, RECVBUF, RECVCOUNT,RECVTYPE, ROOT, COMM, IERROR)
ROOT进程将SENDBUF以`sendcnt*sendtype`为单元散发到不同节点<br>
**sendcnt必须要和recvcnt相同**

```
MPI_SCATTER(SENDBUF, SENDCOUNT, SENDTYPE, RECVBUF, RECVCOUNT,RECVTYPE, ROOT, COMM, IERROR)
    <type>    SENDBUF(*), RECVBUF(*)
    INTEGER    SENDCOUNT, SENDTYPE, RECVCOUNT, RECVTYPE, ROOT
    INTEGER    COMM, IERROR
```

##### call MPI_GATHER(SENDBUF, SENDCOUNT, SENDTYPE, RECVBUF, RECVCOUNT,RECVTYPE, ROOT, COMM, IERROR)
收集数据存到root进程
```
MPI_GATHER(SENDBUF, SENDCOUNT, SENDTYPE, RECVBUF, RECVCOUNT,RECVTYPE, ROOT, COMM, IERROR)
    <type>    SENDBUF(*), RECVBUF(*)
    INTEGER    SENDCOUNT, SENDTYPE, RECVCOUNT, RECVTYPE, ROOT
    INTEGER    COMM, IERROR
```

#### 全局规约：从所有进程收集数据进行操作

##### 规约方式
**表格中的相加，表示对这些数据进行OP操作，并存储到相应node的recvbuff**
![](/uploads/2019/02/reduce.JPG)

##### OP操作类型

| MPI 规约操作 |                  | C语言数据类型                   | Fortran语言数据类型             |
| ------------ | ---------------- | ------------------------------- | ------------------------------- |
| MPI_MAX      | 求最大值         | integer, float                  | integer, real,   complex        |
| MPI_MIN      | 求最小值         | integer, float                  | integer, real,   complex        |
| MPI_SUM      | 和               | integer, float                  | integer, real,   complex        |
| MPI_PROD     | 乘积             | integer, float                  | integer, real,   complex        |
| MPI_LAND     | 逻辑与           | integer                         | logical                         |
| MPI_BAND     | 按位与           | integer, MPI_BYTE               | integer, MPI_BYTE               |
| MPI_LOR      | 逻辑或           | integer                         | logical                         |
| MPI_BOR      | 按位或           | integer, MPI_BYTE               | integer, MPI_BYTE               |
| MPI_LXOR     | 逻辑异或         | integer                         | logical                         |
| MPI_BXOR     | 按位异或         | integer, MPI_BYTE               | integer, MPI_BYTE               |
| MPI_MAXLOC   | 最大值和存储单元 | float, double and   long double | real,complex,double   precision |
| MPI_MINLOC   | 最小值和存储单元 | float, double and               | real,complex,double precision   |
| long double  |                  |                                 |                                 |


##### 规约  MPI_REDUCE
```
MPI_REDUCE(SENDBUF, RECVBUF, COUNT, DATATYPE, OP, ROOT, COMM,IERROR)
    <type>    SENDBUF(*), RECVBUF(*)
    INTEGER    COUNT, DATATYPE, OP, ROOT, COMM, IERROR
```


##### 全规约 MPI_ALLREDUCE
```
MPI_ALLREDUCE(SENDBUF, RECVBUF, COUNT, DATATYPE, OP, COMM, IERROR)
    <type>    SENDBUF(*), RECVBUF(*)
    INTEGER    COUNT, DATATYPE, OP, COMM, IERROR
```
##### 规约分发 MPI_REDUCE_SCATTER
```
MPI_REDUCE_SCATTER(SENDBUF, RECVBUF, RECVCOUNTS, DATATYPE, OP,
        COMM, IERROR)
    <type>    SENDBUF(*), RECVBUF(*)
    INTEGER    RECVCOUNTS(*), DATATYPE, OP, COMM, IERROR
```

##### 并行前缀规约 MPI_SCAN
```
MPI_SCAN(SENDBUF, RECVBUF, COUNT, DATATYPE, OP, COMM, IERROR)
    <type>    SENDBUF(*), RECVBUF(*)
    INTEGER    COUNT, DATATYPE, OP, COMM, IERROR
```

