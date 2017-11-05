---
layout: post
title:  "Linux常用命令学习"
date:   2017-09-10 12:00:00 +0800
categories: Linux
tags: Linux ubuntu 
author: cndaqiang
mathjax: true
---
* content
{:toc}

我也不清楚哪些命令是常用的，我需要哪些命令后就回来总结，先记录自己常用的，这不是本字典，命令现用现查




#常用命令学习(一)简略
我也不清楚哪些命令是常用的，我需要哪些命令后就回来总结，先记录自己常用的，这不是本字典，命令现用现查


## 环境
Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-53-generic x86_64)

## 命令
如无特殊说明
[目录]支持绝对目录和相对目录
[文件]支持[目录]/文件
### 目录操作
#### ls [选项] [目录]
```
ls
```
查看当前目录下的文件
```
ls -a 
```
列出所有文件，包括隐藏文件和.
```
ls -l
```
显示权限，所有者信息，文件类型



![](http://upload-images.jianshu.io/upload_images/4575564-e202a67a455af2e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


>**权限**
8进制 读r4 写w2 执行x1

>**文件类型**
-d 目录
-l 符号链接
-c 字符设备，如鼠标键盘
-d 区块设备，如硬盘
-s 数据接口文件(sockets)

>**ls彩色目录**
>1. 蓝色-->目录
>  2. 绿色-->可执行文件
>  3. 红色-->压缩文件
>4. 浅蓝色-->链接文件
>5. 灰色-->其他文件
>开启或关闭可搜索终端颜色设置

```
ls -R
```
显示当前文件和文件夹下面的所有文件(夹)
#### cd [目录]
```
根目录 \
用户目录 ~或/home/username
上级目录 ..
当前目录 .
上一目录 -
上一条命令中的目录 !$
```
例
```
root@VM-10-194-ubuntu:/home/ftp/ftptest# cd ..
root@VM-10-194-ubuntu:/home/ftp# cd !$
cd ..
root@VM-10-194-ubuntu:/home# cd -
/home/ftp
root@VM-10-194-ubuntu:/home/ftp# 
```
#### pwd
```
pwd
```
显示当前路径
```
pwd -L 链接路径默认
pwd -P 实际路径
```
#### mkdir [选项] [目录]
```
mkdir test
```
创建test目录
```
mkdir -p /tmp/test1/test2/test3
```
递归创建目录，不存在父目录则创建
```
mkdir -m 777 test
```
创建权限为777的test目录


```
root@VM-10-194-ubuntu:/tmp/test2# mkdir -p test/{1.1/,1.2/}2/{3.1,3.2}
root@VM-10-194-ubuntu:/tmp/test2# tree test
test
├── 1.1
│   └── 2
│       ├── 3.1
│       └── 3.2
└── 1.2
    └── 2
        ├── 3.1
        └── 3.2
```
创建目录树示例，中括号{}内是并列的，其他是包含关系
```
mkdir -v test
```
创建时显示信息
#### rm [选项][文件]

```
rm 文件
```
删除文件
```
rm test*
```
删除test开头的文件，*通配符，例如*test表示test结尾的文件
```
rm -r,-R 文件夹
```
递归删除文件夹及内部的文件
>-f, --force    忽略不存在的文件，从不给出提示。
    -i, --interactive 进行交互式删除
    -v, --verbose    详细显示进行的步骤
	
#### rmdir [选项] [空目录]
rmdir**只能删除空目录**，需要对父目录有写权限

```
rmdir test
```
删除test目录
```
rm -rf *
```
删除当前目录所有文件，不要提示
```
rmdir -p test
```
删除test目录后，父目录为空则一并删除
> -v 显示信息的删除

#### mv [选项] 原文件 目标文件
可用于重命名和移动
```
mv test /tmp/te
```
移动文件test到/tmp/下并命名为te
```
mv * ../
```
移动当前目录所有文件至上级目录
详见[每天一个linux命令（7）：mv命令](http://www.cnblogs.com/peida/archive/2012/10/27/2743022.html)
#### cp [选项] 原文件 目标文件
复制文件
```
cp test1 test2
```
当test2不存在时，复制test1命名为test2
当test2存在时，复制test1到test2目录中
>在命令行下复制文件时，如果目标文件已经存在，就会询问是否覆盖，不管你是否使用-i参数。但是如果是在shell脚本中执行cp时，没有-i参数时不会询问是否覆盖。这说明命令行和shell脚本的执行方式有些不同。 

#### touch [选项] [文件]
```
touch 文件
```
新建文件
### 查看文件内容
#### cat [选项] [文件]
```
cat 文件
```
显示文件
```
cat -n 文件
```
同时显示行号
还可与重定向>配合使用[20170805bash学习](http://www.jianshu.com/p/2438d563de06)
#### nl [选项] [文件]
```
nl 文件
```
列出文件内容和行号
#### more [选项] [文件]
```
more 文件
```
按行翻阅文件内容
#### 略
less，head，tail，
### 查找
#### which在PATH中查找命令
```
which ls
```
查找ls所在路径
#### whereis [-bmsu] [BMS 目录名 -f ] 文件名 
>-b   定位可执行文件。
-m   定位帮助文件。
-s   定位源代码文件。
-u   搜索默认路径下除可执行文件、源代码文件、帮助文件以外的其它文件。
-B   指定搜索可执行文件的路径。
-M   指定搜索帮助文件的路径。
-S   指定搜索源代码文件的路径。

whereis命令只能用于程序名的搜索，基于数据库查询，而且只搜索二进制文件（参数-b）、man说明文件（参数-m）和源代码文件（参数-s）。如果省略参数，则返回所有信息。
 
例 
```
ubuntu@VM-10-194-ubuntu:~$ whereis ls
ls: /bin/ls /usr/share/man/man1/ls.1.gz
ubuntu@VM-10-194-ubuntu:~$ whereis -b ls
ls: /bin/ls
```
#### find pathname -options [-print -exec -ok ...]
```
find /tmp -name test
```
再/tmp目录中查找文件名为test的文件
更多用法[每天一个linux命令（19）：find 命令概览](http://www.cnblogs.com/peida/archive/2012/11/13/2767374.html)

### 打包压缩
#### tar[必要参数][选择参数][文件] 
##### 使用tar进行解包打包，并不压缩
```
解包：tar xvf FileName.tar
打包：tar cvf FileName.tar DirName
```
##### tar可调用压缩解压命令
更多参数和压缩解压命令[每天一个linux命令（28）：tar命令](http://www.cnblogs.com/peida/archive/2012/11/30/2795656.html)
### 空间占用
#### df [选项] [文件]
查看磁盘使用和剩余
```
df
```
查看磁盘使用和剩余
```
df -h
```
以K,M,G等易于识别的单位显示磁盘使用和剩余
#### du [选项] [文件]
查看文件(夹)大小
```
du
```
查看文件夹大小,文件夹会一直显示文件夹内的文件(夹)，查看文件du 文件名
```
du -h
```
以K,M,G等易于识别的单位显示文件夹大小，查看文件 du -h 文件名
### 改权限，所有者
![](http://upload-images.jianshu.io/upload_images/4575564-e202a67a455af2e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### chown [选项]... [所有者][:[组]] 文件...
```
chown 用户名文件
```
更改文件所属用户
```
chown 用户名:用户组 文件
```
更改文件所属用户和用户组
#### chgrp [选项] [组] [文件]
```
chgrp 用户组 文件
```
更改文件的用户组
#### chmod
8进制 读r4 写w2 执行x1
拥有读r和写w权限则权限设置为4+2=6
```
chmod 762 文件
```
设置文件的所有者，所有者所在用户组其他成员，其他成员权限分别为7，6，2
### 网络
#### ifconfig [网络设备] [参数]
>用ifconfig命令配置的网卡信息，在网卡重启后机器重启后，配置就不存在。要想将上述的配置信息永远的存的电脑里，那就要修改网卡的配置文件了。
```
ifconfig
```
查看激活的网卡连接情况

![](http://upload-images.jianshu.io/upload_images/4575564-882141bc64f71d3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

属性|值
-|-
HWaddr|mac地址
inet|ipv4地址
inet6|ipv6地址
Bcast|网关
Mask|子网掩码
UP|代表网卡开启状态
RUNNING|代表网卡的网线被接上
MULTICAST|支持组播
MTU:1500|最大传输单元:1500字节
RX|收到的数据包，可根据后面的丢包等情况判断网络
TX|发送的数据包

```
ifconfig lo down
```
关闭lo网卡，ifconfig后不再显示lo网卡
```
ifconfig lo up
```
开启lo网卡
```
ifconfig eth0 add 192.168.1.2 
```
给eth0添加ip 192.168.1.2,发现增加了一个网卡eth0:0
ifconfig eth0 del 192.168.1.2 删除ip命令在ubuntu上无效？？

```
ifconfig eth0 hw ether 52:54:00:5c:f4:9a
```
**修改**mac地址
```
ifconfig eth0 10.105.10.195 netmask 255.255.192.0 broadcast 10.105.63.255
```
**修改** ip地址 掩码 广播地址
#### 永久更改ip/dhcp
```
vi /etc/network/interfaces
```
dhcp
```
auto eth0
iface eth0 inet dhcp
```
固定ip
```
auto eth0
iface eth0 inet static
address 10.105.10.194
netmask 255.255.192.0
gateway 10.105.0.1
```
添加ip地址
在该文件中添加如下的行
```
auto eth0:1
iface eth0:1 inet static
address 192.168.1.60
netmask 255.255.255.0
network x.x.x.x
broadcast x.x.x.x
gateway x.x.x.x
```
重启生效
`/etc/init.d/networking restart`
#### 更改主机名
```
/bin/hostname 
```
显示主机名
```
/bin/hostname newname
```
更改主机名
#### 更改dns
```
vi /etc/resolv.conf
```
添加`nameserver DNS的ip地址`
其他参数教程中的参数表达意思我还不理解，展示不记录
### netstat
查看与IP、TCP、UDP和ICMP协议相关的统计数据
```
netstat
```
查看建立的连接
![](http://upload-images.jianshu.io/upload_images/4575564-907c67110b62fbdd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中local Address 中有10.105.10.194:ssh，ssh就代表了ssh默认端口号
```
netstat -a
```
列出所有端口包括监听端口，如图listen

![](http://upload-images.jianshu.io/upload_images/4575564-d4975672965a6cae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### ln [参数][源文件或目录][目标文件或目录]

**目录为绝对路径**

>**软连接和硬链接**
软链接：
1.软链接，以路径的形式存在。类似于Windows操作系统中的**快捷方式**
2.软链接可以跨文件系统 ，硬链接不可以
3.软链接可以对一个不存在的文件名进行链接
4.软链接可以对**目录**进行链接
硬链接:
1.硬链接，以**文件副本**的形式存在。但不占用实际空间。
2.**不允许给目录创建硬链接**
3.硬链接只有在同一个文件系统中才能创建

```
root@VM-10-194-ubuntu:/tmp# ln -s /var/www/html/index.html ruan
root@VM-10-194-ubuntu:/tmp# ln  /var/www/html/index.html ying
root@VM-10-194-ubuntu:/tmp# du -h *
0	ruan
12K	ying
root@VM-10-194-ubuntu:/tmp# ls -l
total 12
lrwxrwxrwx 1 root root    24 Sep 11 20:14 ruan -> /var/www/html/index.html
-rw-r--r-- 2 root root 11321 Sep  3 21:18 ying
```
对/var/www/html/index.html文件分别创建软连接ruan，硬链接ying，并查看大小，属性,对于软连接/var/www/html/index.html换为**绝对目录**也可以
可看到:
软连接是快捷方式，不占大小，就是一个可以修改的快捷方式，指向谁都可以，即使对方不存在，所以搭建网页时，若使用软连接 `http://domain../软连接` 后面不需要再加/**??**
硬链接是副本，有大小，显示的也是源文件的权限信息（副本必然一样），既然是副本了，要求同一文件系统没毛病

对连接文件进行的修改和原文件是同步的
### wget [参数] [URL地址]
>wget可以在用户退出系统的之后在后台执行
当网络的原因下载失败，wget会不断的尝试，直到整个文件下载完毕。如果是服务器打断下载过程，它会再次联到服务器上从停止的地方继续下载
```
wget ftp://f.test.cn:17828/download/kaying%20tools.exe
```
下载文件
```
wget --ftp-user=USERNAME --ftp-password=PASSWORD url
```
ftp账户密码
```
wget -b http://test.com/index.html
```
在后台下载http://test.com/index.html文件
```
wget -c ftp://f.test.cn:17828/download/kaying%20tools.exe
```
下载中断后，续传
```
wget -O test  ftp://f.test.cn:17828/download/kaying%20tools.exe
```
下载并重命名为test
```
wget --user-agent="Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16" ftp://f.test.cn:17828/download/kaying%20tools.exe
```
伪装代理user-agent下载
>使用wget –mirror镜像网站
命令：`wget --mirror -p --convert-links -P ./LOCAL URL`
说明：
下载整个网站到本地。
--miror:开户镜像下载
-p:下载所有为了html页面显示正常的文件
–convert-links:下载后，转换成本地的链接
-P ./LOCAL：保存所有文件和目录到本地指定目录

忽视robots加上`-e robots=off`参数
```
wget -r -e robots=off http://www.xxx.com/test/
```
-r 也可下载整站，-r表示递归
### 安装卸载软件
#### dpkg
```
dpkg -L 安装包名 | more
```
可以查看，安装后添加了哪些目录
这个more用的很好，
```
命令 --help | more
```
也可以少看命令
#### apt-get
彻底删除软件及配置
```
apt-get remove --purge 软件名称  
```
适合修改vsftpd软件配置文件后，用apt-get remove vsftpd卸载不干净，重装配置文件不变

## 参考
[peida-博客-每天一个linux命令目录](http://www.cnblogs.com/peida/)

[Ubuntu命令行修改网络配置方法](http://forum.ubuntu.org.cn/viewtopic.php?f=73&t=190174&sid=6019f19515906b2b0219f8cee52bbcdb)

[wget命令下载整站，并忽略robots.txt文件](https://www.leocode.net/article/index/17.html)

[Ubuntu终端彻底删除软件](http://www.linuxidc.com/Linux/2012-07/65455.htm)

