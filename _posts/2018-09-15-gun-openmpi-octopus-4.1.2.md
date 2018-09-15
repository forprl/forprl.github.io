---
layout: post
title:  "gcc Openmpi 编译octopus-4.1.2 "
date:   2018-09-15 12:05:00 +0800
categories: DFT
tags: siesta Intel
author: cndaqiang
mathjax: true
---
* content
{:toc}

编译方法主要参考[@sculxb](https://www.zybuluo.com/sculxb/note/987446#octopus%E5%AE%89%E8%A3%85-%E7%AE%97%E7%9B%98)<br>
环境centos6.5，软件版本gcc-4.4.7,libXC-2.0.0,gsl-1.14,openmpi-1.10.3,fftw-3.3.3 scalapack-2,octopus-4.1.2<br>
gcc使用系统默认的gcc-4.4.7,其他软件分别编译






根据[Basic Configuration of Octopus 4.1.2 with OpenMPI on CentOS 6](https://linuxcluster.wordpress.com/2015/03/25/basic-configuration-of-octopus-4-1-2-with-openmpi-on-centos-6/)的建议:<br>
octopus-4.1.2只能使用libXC-2.0.x或2.1.x,gsl只能用1.14或更早<br>
使用gcc编译libXC-2.0.0,gsl-1.14,openmpi-1.10.3<br>
编译openmpi-1.10.3得到的并行编译器(mpicc,mpif90等)和库用于编译fftw-3.3.3 scalapack-2,octopus-4.1.2<br>

<br>**待解决问题:**<br>
centos6.5默认的gcc-4.4.7编译openmpi-1.6.4报错，编译1.10.3没问题<br>
目前在centos7上先编译gcc-4.8.4再编译其他程序有一些问题，使用系统的gcc-4.8.5编译也会有问题，libXC-2.0.0编译报错，2.0.3就可以编译通过，但最后编译octopus时configure没问题，make时报错，等待进一步解决


此文直接将在我计算机上的编译过程输入的命令复制了过来，请适当更改
## 下载

```
wget http://ftp.gnu.org/gnu/gsl/gsl-1.14.tar.gz
wget http://www.tddft.org/programs/octopus/down.php?file=libxc/2.0.0/libxc-2.0.0.tar.gz
wget https://download.open-mpi.org/release/open-mpi/v1.10/openmpi-1.10.3.tar.gz
wget http://www.netlib.org/scalapack/scalapack_installer.tgz
wget ftp://ftp.fftw.org/pub/fftw/fftw-3.3.3.tar.gz
wget http://www.tddft.org/programs/octopus/down.php?file=4.1.2/octopus-4.1.2.tar.gz
```
## libXC-2.0.0
```
cd ..
tar xzvf libxc-2.0.0.tar.gz 
cd libxc-2.0.0
./configure --prefix=/home/cndaqiang/soft/libxc-2.0.0/ CC=gcc CXX=g++ FC=gfortran
make -j8
make install
export LD_LIBRARY_PATH=/home/cndaqiang/soft/libxc-2.0.0/lib:$LD_LIBRARY_PATH
```

## openmpi-1.10.3
```
tar xzvf openmpi-1.10.3.tar.gz 
cd openmpi-1.10.3
./configure --prefix=/home/cndaqiang/soft/openmpi-1.10.3 CC=gcc FC=gfortran CXX=g++
make -j8
make install
export LD_LIBRARY_PATH=/home/cndaqiang/soft/openmpi-1.10.3/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/openmpi-1.10.3/bin:$PATH
```



## gsl-1.14

```
cd ..
tar xzvf gsl-1.14.tar.gz 
cd gsl-1.14
mkdir build-gsl
cd build-gsl/
../configure --prefix=/home/cndaqiang/soft/gsl-1.14
make -j8
make install
export PATH=/home/cndaqiang/soft/gsl-1.14/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/gsl-1.14/lib:$LD_LIBRARY_PATH
```

## fftw-3.3.3

```
cd ..
tar xzvf fftw-3.3.3.tar.gz 
cd fftw-3.3.3
./configure --prefix=/home/cndaqiang/soft/fftw-3.3.3 --enable-mpi
make -j8
make install
export LD_LIBRARY_PATH=/home/cndaqiang/soft/fftw-3.3.3/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/fftw-3.3.3/bin:$PATH
```

## scalapack

```
cd ..
tar xzvf scalapack_installer.tgz 
cd scalapack_installer
./setup.py --prefix=/home/cndaqiang/soft/scalapack --downall
```

## octopus-4.1.2
```
cd ..
tar xzvf octopus-4.1.2.tar.gz 
cd octopus-4.1.2
 ./configure --prefix=/home/cndaqiang/soft/octopus-4.1.2 --with-blas='-L/home/cndaqiang/soft/scalapack/lib -lrefblas' --with-lapack='-L/home/cndaqiang/soft/scalapack/lib -ltmg -lreflapack' --with-scalapack='-L/home/cndaqiang/soft/scalapack/lib -lscalapack' --with-libxc-prefix=/home/cndaqiang/soft/libxc-2.0.0 --with-gsl-prefix=/home/cndaqiang/soft/gsl-1.14 -with-fftw=fftw3 --with-fft-lib=/home/cndaqiang/soft/fftw-3.3.3/lib/libfftw3.a --enable-mpi
make -j8
make install
```

## 测试
```
EXEC=/home/cndaqiang/soft/octopus-4.1.2/bin/octopus_mpi 
cd ~/soft/octopus-test/
mpirun -np 8 $EXEC
```

## 备注
每次运行octopus需执行下列命令，即在交作业脚本中加入以下内容，或者添加到.bashrc
```
export LD_LIBRARY_PATH=/home/cndaqiang/soft/openmpi-1.6.4/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/openmpi-1.6.4/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/libxc-2.0.0/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/gsl-1.14/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/gsl-1.14/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/openmpi-1.10.3/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/openmpi-1.10.3/bin:$PATH
export LD_LIBRARY_PATH=/home/cndaqiang/soft/fftw-3.3.3/lib:$LD_LIBRARY_PATH
export PATH=/home/cndaqiang/soft/fftw-3.3.3/bin:$PATH
```

