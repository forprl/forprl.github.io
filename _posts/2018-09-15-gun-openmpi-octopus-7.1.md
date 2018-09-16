---
layout: post
title:  "[编译通过，未计算测试]centos6.5 gcc Openmpi 编译octopus-7.1 "
date:   2018-09-15 21:23:00 +0800
categories: DFT
tags: gnu octopus
author: cndaqiang
mathjax: true
---
* content
{:toc}

软件版本主要参考，算盘上的Makefile<br>
环境centos6.5，软件版本gcc-4.4.7,libXC-3.0.0,gsl-2.0,openmpi-1.10.3,fftw-3.3.3 scalapack-2,octopus-7.1<br>
gcc使用系统默认的gcc-4.4.7,其他软件分别编译







~~octopus简直了，不同版本需要特定版本的编译器、库，~~直接参考组里使用的软件版本，组里使用gcc-4.4.7,libXC-3.0.0,gsl-2.0,openmpi-1.10.3(这个是我之前编译过，没看组里使用的那个版本),openfftw-3.3.3(因为我之前编译过3.3.3，组里使用3.3.7，不编译3.3.7了，应该都可以) scalapack-2,octopus-7.1

## 下载

```
wget ftp://ftp.gnu.org/gnu/gsl/gsl-2.0.tar.gz
wget http://www.tddft.org/programs/octopus/down.php?file=libxc/3.0.0/libxc-3.0.0.tar.gz
wget https://download.open-mpi.org/release/open-mpi/v1.10/openmpi-1.10.3.tar.gz
wget http://www.netlib.org/scalapack/scalapack_installer.tgz
wget ftp://ftp.fftw.org/pub/fftw/fftw-3.3.3.tar.gz
wget http://www.tddft.org/programs/octopus/down.php?file=4.1.2/octopus-4.1.2.tar.gz
```
## libXC-3.0.0
同[centos6.5 gcc Openmpi 编译octopus-4.1.2](/2018/09/15/gun-openmpi-octopus-4.1.2/)
## gsl-2.0
同[centos6.5 gcc Openmpi 编译octopus-4.1.2](/2018/09/15/gun-openmpi-octopus-4.1.2/)
## openmpi-1.10.3
同[centos6.5 gcc Openmpi 编译octopus-4.1.2](/2018/09/15/gun-openmpi-octopus-4.1.2/)
## fftw-3.3.3
同[centos6.5 gcc Openmpi 编译octopus-4.1.2](/2018/09/15/gun-openmpi-octopus-4.1.2/)
## scalapack
同[centos6.5 gcc Openmpi 编译octopus-4.1.2](/2018/09/15/gun-openmpi-octopus-4.1.2/)


<br>安装完上述软件后记得分别输入下列命令
```
export LD_LIBRARY_PATH=/home/cndaqiang/soft/libxc-3.0.0/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/gsl-2.0/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/gsl-2.0/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/openmpi-1.10.3/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/openmpi-1.10.3/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/fftw-3.3.3/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/fftw-3.3.3/bin:$PATH
```

## octopus-7.1

octopus-7.1和4.1.2的configure的fftw参数有些不同
```
./configure --prefix=/home/cndaqiang/soft/octopus-7.1 --with-blas='-L/home/cndaqiang/soft/scalapack/lib -lrefblas' --with-lapack='-L/home/cndaqiang/soft/scalapack/lib -ltmg -lreflapack' --with-scalapack='-L/home/cndaqiang/soft/scalapack/lib -lscalapack' --with-libxc-prefix=/home/cndaqiang/soft/libxc-3.0.0 --with-gsl-prefix=/home/cndaqiang/soft/gsl-2.0  --with-fftw-prefix=/home/cndaqiang/soft/fftw-3.3.3 --enable-mpi
```
## 测试
这里和octopus-4.1.2有点不同，4.1.2版本是`octopus-4.1.2/bin/octopus_mpi`
```
EXEC=/home/cndaqiang/soft/octopus-7.1/bin/octopus 
cd ~/soft/octopus-test/
mpirun -np 8 $EXEC
```

## 备注
每次运行octopus需执行下列命令，即在交作业脚本中加入以下内容，或者添加到.bashrc
```
export LD_LIBRARY_PATH=/home/cndaqiang/soft/libxc-3.0.0/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/gsl-2.0/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/gsl-2.0/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/openmpi-1.10.3/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/openmpi-1.10.3/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/fftw-3.3.3/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/fftw-3.3.3/bin:$PATH
```
