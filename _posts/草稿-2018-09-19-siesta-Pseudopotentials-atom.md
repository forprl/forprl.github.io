---
layout: post
title:  "编译ATOM(siesta的Pseudopotentials)"
date:   2018-09-18 21:12:00 +0800
categories: DFT siesta
tags: gnu octopus
author: cndaqiang
mathjax: true
---
* content
{:toc}







```
wget https://launchpad.net/xmlf90/trunk/1.5/+download/xmlf90-1.5.4.tar.gz
wget http://www.tddft.org/programs/octopus/down.php?file=libxc/4.2.3/libxc-4.2.3.tar.gz
wget https://launchpad.net/libgridxc/trunk/0.8/+download/libgridxc-0.8.0.tgz

```


## xmlf90-1.5.4
``` 
tar xzvf xmlf90-1.5.4.tar.gz 
cd xmlf90-1.5.4/
./configure --prefix=/home/cndaqiang/soft/gun/xmlf90-1.5.4
make -j40
make install
```
## libxc-4.2.3

## libgridxc-0.8.0
具体含义参考`libgridxc-0.8.0/00_README`
```
tar xzvf libgridxc-0.8.0.tgz
cd libgridxc-0.8.0
mkdir build-20180919
cd build-20180919
cp ../extra/fortran.mk
```
修改`fortran`中
```
LIBXC_ROOT=/home/cndaqiang/soft/gun/libxc-4.2.3
FC_SERIAL=gfortran
FC_PARALLEL=mpif90
```
安装
```
../src/config.sh
WITH_LIBXC=1 WITH_MPI=1 PREFIX=/home/cndaqiang/soft/gun/libgridxc-0.8.0 sh build.sh
```

## atom

```
XMLF90_ROOT=/home/cndaqiang/soft/gun/xmlf90-1.5.4
GRIDXC_ROOT=/home/cndaqiang/soft/gun/libgridxc-0.8.0/mpi
LIBXC_ROOT=/home/cndaqiang/soft/gun/libxc-4.2.3
```
