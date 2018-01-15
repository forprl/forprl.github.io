---
layout: post
title:  "Intel Parallel Studio XE 编译VASP "
date:   2018-01-15 11:57:00 +0800
categories: DFT
tags: vasp centos Intel
author: cndaqiang
mathjax: true
---
* content
{:toc}

之前尝试编译VASP[Ubuntu VASP安装和运行](/2018/01/09/ubuntu-install-vasp/)，但是在centos上进行重复时，各种报错，现在尝试了安装几次感觉自己的理解更多了，总结如下。这篇文章省略了很多命令,有看不懂的地方参考[Ubuntu VASP安装和运行](/2018/01/09/ubuntu-install-vasp/)。





# 编译注意
- 硬盘空间足够
<br>编译时会产生很多临时文件，占据空间大，Intel编译器Intel Parallel Studio XE 2018 for linux安装后占11G，安装包3.5G,硬盘空间不足编译失败
- 内存足够
<br>使用fortran编译vasp时,内存1G编译过程中,进程被杀死，添加2G的虚拟内存，编译通过
- 编译器最好一致
<br>vasp需要数学库,mpi,fft，使用gfortran和ifort编译产生的库文件不同，最后使用gfortran和ifort编译vasp时容易冲突，所以只使用ifort或gfortran其中的一种进行编译
- configure的一些参数
<br> `--prefix 安装目录`,是最后`make install`的安装地址
- 安装地址
<br>可以编译在`/home/username`即家目录下,这样只能自己使用
<br>也可以安装到根目录下的某目录(需要root权限)，每个用户都可以使用
- PATH
<br>安装软件后,软件执行文件所在目录被添加到系统PATH路径后，才能在shell里直接输入命令如`icc`,不添加则需要使用`/opt/intel/bin/icc`运行
<br>添加PATH的方法，参考[添加PATH](/2017/09/10/linux-command/#%E6%B7%BB%E5%8A%A0path)
<br><br>
# vasp编译说明
建议认真读一下vasp4.6的makefile文件,里面说的很详细[makefile.linux_ifc_P4](/web/file/2018/makefile.linux_ifc_P4)，还有VASP.5.4.1里面的README
## vasp安装需要
- fortran等编译器
<br> intel: icc ifort
<br> gfortran等
- 数学库 BLAS BLACS LAPACK SCALAPACK
<br> intel:mkl含有
<br> 分别从[NETLIB](http://www.netlib.org/liblist.html)编译安装
<br> [SCALAPACK安装包](http://www.netlib.org/scalapack/#_scalapack_installer_for_linux)可帮忙下载所有数学库
- fft
<br> vasp自带
<br> intel:mkl含有
<br> 编译[fftw](http://www.fftw.org/)
- **注:**
<br>若编译数学库,fftw,需和最后编译vasp使用同一fortran编译器
<br>编译数学库,需支持mpi并行，这样才能编译支持mpi的vasp

通过上面的分析，我们可以发现，intel的编译器[Intel Parallel Studio XE](https://software.intel.com/en-us/parallel-studio-xe/choose-download)包含了我们编译vasp所有的工具

## 编译准备
从[Intel Parallel Studio XE](https://software.intel.com/en-us/parallel-studio-xe/choose-download)注册账号，获取安装序列号，建议使用edu邮箱注册，获取序列号时间短,我申请的开源贡献者账号好几天都没通过。当然也有使用license激活的,百度相关资源。<br>
此次我使用的版本Intel® Parallel Studio XE 2018 for Linux<br><br>
vasp来源[VASP](https://www.vasp.at/),组里购买的正版,网上也可搜索的相关的资源,计算请使用正版<br>
此次我使用的文件vasp.5.4.1.24Jun15.tar.gz

## 编译环境
此次编译在vmware上运行的Centos7,3.10.0-514.el7.x86_64,2G内存,i7-7500U<br>
除了intel需要的库安装方法不一样外，vasp编译运行方式应该适用于所有Linux
<br><br>
# 编译过程
## intel
### 依赖
```
yum install glibc-devel.i686 
yum install libstdc++.so.6  
ldconfig
yum install gcc-c++
```
安装过程提示OS unsupport ,忽视<br>
有其他依赖缺少时,yum安装后再Re-check
<br>另外在云服务器上尝试时
- KVM安装时提示 CPU unsupport，暂未继续安装
- OVZ安装提示 内核有问题，暂未继续安装

### 安装
```
./install.sh
```
同意协议,保持默认选项即可,默认安装到`/opt/intel`,也可以自定义
### 添加PATH
下面的路径与实际路径与intel编译器的版本有关,版本变更后适当修改<br>
执行
```
source /opt/intel/compilers_and_libraries_2018.0.128/linux/bin/compilervars.sh intel64
source /opt/intel/compilers_and_libraries_2018.0.128/linux/bin/iccvars.sh intel64 
source /opt/intel/compilers_and_libraries_2018.0.128/linux/bin/ifortvars.sh intel64 
source /opt/intel/compilers_and_libraries/linux/mkl/bin/mklvars.sh intel64
source  /opt/intel/impi/2018.0.128/bin64/mpivars.sh
```
或者讲上述命令添加到`/etc/profile`或`~/.bashrc`,具体含义[添加PATH](/2017/09/10/linux-command/#%E6%B7%BB%E5%8A%A0path)<br>
可用`which icc ifort icpc mpiifort`检查是否添加成功<br>
之后编译,若提示`xxx:command not found`，则在source一遍上述命令<br>
在编译后运行vasp时,若上述文件不在PATH内,也无法运行,需要先执行一遍<br>
修改`/etc/profile`或`~/.bashrc`中就无需上述操作,登陆时source一下或着添加到文件永久修改都可以,看个人喜好
### 编译并行fftw
下面的路径与实际路径与intel编译器的版本有关,版本变更后适当修改<br>
`make -h`可确定使用intel编译器编译并行版本的fftw命令为`make libmic`
```
cd /opt/intel/compilers_and_libraries_2018.0.128/linux/mkl/interfaces/fftw3xf
make libmic
```
编译后在当前文件夹内生成`libfftw3xf_intel.a`
## vasp
好像不需要vasp.5.lib也编译通过<br>
解压vasp.5.4.1.24Jun15.tar.gz后
```
cd vasp.5.4.1
cp arch/makefile.include.linux_intel makefile.include
```
修改makefile.include中内容
- 10行开始编译器配置
```
FC         = mpiifort
FCL        = mpiifort -mkl
```
- 19行开始,数学库配置如下
```
MKLROOT=/opt/intel/compilers_and_libraries_2018.0.128/linux/mkl
MKL_PATH   = $(MKLROOT)/lib/intel64
BLAS       = -L$(MKL_PATH) -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread
LAPACK     = -L$(MKL_PATH) -lmkl_intel_lp64 -lmkl_sequential -lmkl_core -lpthread
BLACS      = -L$(MKL_PATH) -lmkl_blacs_intelmpi_lp64
SCALAPACK  = -L$(MKL_PATH) -lmkl_scalapack_ilp64 -lmkl_scalapack_lp64 -lmkl_blacs_intelmpi_lp64
```
- 26行fft配置
```
OBJECTS    = fftmpiw.o fftmpi_map.o fftw3d.o fft3dlib.o \
             $(MKLROOT)/interfaces/fftw3xf/libfftw3xf_intel.a
INCS       =-I$(MKLROOT)/include/fftw
```

最后我的[makefile.inclued](/web/file/2018/makefile.include_intelfftw/makefiel.include)<br>
编译
```
make
```
就在`./build`中生成了gamma版本的vasp,非线性版本的vasp,标准版本的vasp
```
gam  ncl  std
```
每个文件夹中都有一个vasp的可执行文件,添加PATH即可

# 运行vasp
若没有给数学库添加PATH,运行前需要source一下,具体内容，前面都有<br>
vasp运行方式1)添加PATH直接输入vasp运行，或类似这样`~/vasp/vasp.5.4.1/build/std/vasp`运行<br>
是否添加PATH，看组里习惯吧<br>
从[Materials Project](https://www.materialsproject.org)下载POSCAR, INCAR,KPOINTS从vasp网站下载POTCAR，放在一个文件夹中，在改文件夹内运行vasp,将结果与[Materials Project](https://www.materialsproject.org)结果比较
# 常见问题
这里放一些，安装过程中的问题和解决方案

## intel64
在intel的路径中`ia32`代表32位,`intel64`代表64位
## 数学库MKL
### 数学库的调用
```
/opt/intel/compilers_and_libraries_2018.0.128/linux/mkl/lib/intel64
```
里面有很多库文件，如`libmkl_blacs_intelmpi_ilp64.a`,在编译时使用 
```
-L/opt/intel/compilers_and_libraries_2018.0.128/linux/mkl/lib/intel64 -lmkl_blacs_intelmpi_ilp64
```
或者
```
/opt/intel/compilers_and_libraries_2018.0.128/linux/mkl/lib/intel64/libmkl_blacs_intelmpi_ilp64.a
```
都可以调用
## mpif90
编译vasp时若FC设置为mpif90,报错
```
gfortran: command not found
```
这是因为,intel MPI命令中mpif90调用gfortran进行编译,gfortran没安装报错
<br>若装上gfortran又会因为,数学库等是使用intel的ifort编译的,和gfortran又有冲突报错
<br>最好的解决方式,是FC=mpiifort
<br>此内容参考[mpif90 from cluster toolkit pointing to gfortran](https://software.intel.com/en-us/forums/intel-clusters-and-hpc-technology/topic/288354)和[科大李会民老师-MPI编译环境的使用](http://scc.ustc.edu.cn/zlsc/pxjz/201408/W020140804352832344867.pdf)
![](/uploads/2018/01/intel_mpi.png)
## 编译时报错哪个数学库文件
检查数学库文件名是否正确,数学库是否选对,如<br>
`BLACS      = -lmkl_blacs_intelmpi_lp64`因为我们使用的是intel的mpi所以blacs使用`intelmpi`，若使用openmpi,则设置为`libmkl_blacs_openmpi_lp64`
## 使用fftw
若不适用intel的fftw,下载fftw<br>
解压进入相关文件夹后,生成使用intel编译支持并行的makefile文件
```
./configure --prefix=/opt/fftw/ CC=icc F77=ifort MPICC=mpiicc --enable-mpi
make
make install
```
则`makefile.include`中设置为
```
OBJECTS    = fftmpiw.o fftmpi_map.o fftw3d.o fft3dlib.o \
             /opt/fftw/lib/libfftw3_mpi.a
INCS       =-I/opt/fftw/include
```
此处参考[fftw 编译安装说明](http://blog.csdn.net/sowhatgavin/article/details/71036878)

## fftw不支持mpi报错
编译fftw时，若使用`make libintel64`，则编译的fftw不支持mpi,编译时会对`libfftw3xf_intel.a`报错

