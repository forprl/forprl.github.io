---
layout: post
title:  "VASP输入文件总结"
date:   2018-01-21 17:29:00 +0800
categories: DFT
tags: vasp
author: cndaqiang
mathjax: true
---
* content
{:toc}
整理备查




## 说明
输入文件POSCAR，KPOINT，POTCAR，INCAR等,尝试用`!`注释，还没发现问题,还是先不要注释了吧
## POSCAT 赝势
### 产生
来源VASP官网提供<br>
直接拼接赝库,顺序同POSCAR
```
cat POTCAR_AL POTCAR_N > POTCAR
```
### 说明
VASP赝势文件夹中包含两个压缩文件：potpaw_LDA和potpaw_PBE<br>
potpaw_LDA ==> PAW, LDA<br>
potpaw_PBE ==> PAW, GGA, PBE<br>

- 赝势的种类要一致
- 赝势使用的泛函要与INCAR中选择的泛函一致（PBE的泛函要选择 PBE的赝势）
<br>即:POTCAR中的LEXCH与INCAR中GGA的设置对应
<br>LEXCH=CA(LDA赝势) GGA不设置 VOSKOWN不设置
<br>LEXCH=91(PW91赝势) GGA=91 VOSKOWN=1
<br>LEXCH=PE GGA=PE VOSKOWN不设置

## KPOINT K点
布里渊区K点采样方式
### 产生
#### 自动
```
Automaticmesh  首行注释
0              0表示自动产生K点
M/G            M表示采用Monkhorst-Pack方法生成K点坐标，G表示一定会取到Γ点
K1 K2 K3       K-mesh取K1xK2xK3网格，
0 0 0            原点平移大小
```
- K1 K2 K3的取值,使得K1*a=K2*b=K3*c，a,b,c为POSCAR中基失长度
![](/uploads/2018/01/k1k2k3mesh.jpg)
![](/uploads/2018/01/k1k2k3mesh2.jpg)

#### Line-mode(一般仅在计算能带结构时使用)：
```
k-pointsforMgO(100)(title) 注释
21             K点数目
Line-mode      L表示Line-mode
Rec           字母R打头表示为倒易空间坐标，否则为实空间的坐标)
0.0 0.0 0.0 !Γ   各K点的以及权重
0.5 0.0 0.0 !Z
0.5 0.0 0.0 !Z
0.5 -0.5 0.0 !K
0.5 -0.5 0.0 !K
0.0 -0.5 0.0 !L
0.0 -0.5 0.0 !L
0.0 0.0 0.0 !Γ
```
(对称点Γ等,与布里渊区形状有关)
Ref：10.1016/j.commatsci.2010.05.010

#### 手动定义各K点的坐标(一般仅在计算HSE能带结构时使用)：

### 说明
(个人感觉，在不计算能带时Auto就行)

## POSCAR 晶格参数原子位置
### 产生
- 文献
- MS建模-cif-VESTA-POSCAR
- 数据网站

### 格式
```
AlN bulk (Title) 首行注释
1.0   缩放系数
3.11  0.00  0.00    第一个平移矢量a方向
-1.56   2.69  0.00  第二个平移矢量b方向
0.00    0.00  4.98  第三个平移矢量c方向
Al    N 原子种类
2   2   单胞内原子数目
Selective dynamics [可选]有对构型(原子坐标)进行部分优化，没有,则全优化
Direct  Direct分数坐标,Car实际坐标单位为埃
0.667   0.333     0.000    T   T   T  T表示对此方向优化，F表示对此方向不优化
0.333   0.667     0.500    T   T   T
0.667   0.333     0.382    T   T   T
0.333   0.667     0.882    F   F   F
```
## INCAR
计算的方式和内容<br>
可以内容为空保持默认,但不能没有该文件
### ENCUT 平面波截断能
例,单位eV
```
ENCUT=250
```
侯:推荐手动输入
### PREC计算精度
决定ENCUT,FFT的网格大小和ROPT的默认值
```
PREC=Low|Medium|High|Normal|Accurate
```
### ISATRT ICHARG INIWAV
- ISATART初始波函数产生方法
<br>默认存在WAVECAR时取1,否则0
<br>ISATAR=0,根据INIWAV决定初始波函数的产生方法
<br>ISATAR=1,波函数从WAVECAR文件读入
- INIWAV初始波函数产生方法
<br>尽在ISATART=0生效
<br>0 凝胶波函数
<br>1[默认]随机数
- ICHAGE初始电荷密度产生方法
<br>默认ISATRT=0取2否则取0
<br> 0从初始波函数计算
<br> 1从CHGCAR(上次输出的电荷密度)读入
<br> +10非自洽运算,电荷密度在计算过程中保持不变
<br> 如1+10=11时，电荷密度保持CHGCAR中的值不变,适用于给定电荷密度求能级本征值(输出EIGENVAL文件)和态密度(输出DOSCAR文件),用于能带计算

初次计算
```
ISATRT=0 ICHARG=2
```
程序终止,恢复计算
```
ISATRT=1 ICHARG=1
```
计算能带最后进行自洽时
```
ISATRT=1 ICHARG=11
```
### GGA VOSKOWN
POTCAR中的LEXCH与INCAR中GGA的设置对应
<br>LEXCH=CA(LDA赝势) GGA不设置 VOSKOWN不设置
<br>LEXCH=91(PW91赝势) GGA=91 VOSKOWN=1
<br>LEXCH=PE GGA=PE VOSKOWN不设置
### ISMEAR SIGMA
ISMEAR用来确定如何或用何种方法来设置每个波函数的部分占有数fnk。在采用有限
温度方法设置fnk时，smearing方法中的smearing宽度σ
- ISMEAR = -5，表示采用Blochl修正的四面体方法
<br>进行任何的静态计算或态密度计算，且k点数目大于4时，取ISMEAR = -5
<br>半导体或绝缘体的计算（不论是静态还是结构优化），取ISMEAR = -5
- ISMEAR = -4，表示采用四面体方法，但是没有Blochl修正。
- ISMEAR = -1，表示采用Fermi-Dirac smearing方法。
- ISMEAR = 0，表示采用Gaussian smearing方法。
<br>于原胞较大而k点数目较少（小于4个）时，取ISMEAR = 0，并设置一个合适的SIGMA值
<br>一般说来，无论是对何种体系，进行何种性质的计算，采用ISMEAR =0，并选择一个合适的SIGMA值都能得到合理的结果
- ISMEAR = N，表示采用Methfessel-Paxton smearing方法，N为正整数,表示此方法中的阶数
<br>体系呈现金属性时，取ISMEAR=1和2，以及设置一个合适的SIGMA值

- 在进行能带结构计算时，ISMEAR和SIGMA用默认值就好。
<br>