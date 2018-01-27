---
layout: post
title:  "python(补充1)输入输出"
date:   2017-10-01 12:00:00 +0800
categories: Python
tags: python 
author: cndaqiang
mathjax: true
---
* content
{:toc}




# 一些函数
### print
#### 输出字符串，列表等
```
print('hello')
print(str1,str2,str3)  #连接输出，中间以空格隔开
print(list)
```
print每执行一次，输出后最后默认加一个回车，可以print(str1,edn='结束内容')，指定输出后的内容
```
>>> print('a');print('b')
a
b 
>>> print('a',end='');print('b')
ab
>>> print('a',end=' ');print('b')
a b
```

#### 格式化输出
- %s --- 字符串
- %d --- dec十进制
- %x --- hex 十六进制
- %d --- dec 十进制
- %o --- oct 八进制
- %f --- 浮点数
- %m.nf --- 整数部分m个，不够补空格，小数部分n个

**更多格式先略**
```
>>> print('%x' %23)
17
>>> str='hello,%d,%x,%o' %(45,45,45)
>>> print(str)
hello,45,2d,55
```
### format