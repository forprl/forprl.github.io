---
layout: post
title:  "ubuntu 16.04 编译 可跳过散列检测的 transmission-2.92"
date:   2018-11-17 12:53:00 +0800
categories: OPENWRT
tags: openwrt ubuntu nas
author: cndaqiang
mathjax: true
---
* content
{:toc}

路由器的usb2.0挂pt太慢了，为充分利用千兆网，换了千兆小主机挂pt了










## 参考
[superlukia/transmission-2.92_skiphashcheck](https://github.com/superlukia/transmission-2.92_skiphashcheck)<br>
[源码编译安装Transmission 2.93（debian 7)](https://www.jianshu.com/p/551ed5464e81)<br>
[Ubuntu 16.04 and transmission 2.92](https://forum.odroid.com/viewtopic.php?t=23992)<br>
[ubuntu开放指定端口](https://www.jianshu.com/p/2ec5d16db02b)<br>

得益于大佬[superlukia](https://github.com/superlukia)对transmission的修改，可以跳过transmission的散列检测

## 环境
Ubuntu 16.04.5 LTS<br>
小主机:
- Intel(R) Atom(TM) CPU N270   @ 1.60GHz
- 内存： 2G ddr2
## 编译
### 依赖
```
sudo apt update
sudo apt-get install libcurl4-openssl-dev
apt-get install libevent-dev
sudo apt-get install zlib1g
sudo apt-get install zlib1g.dev
sudo apt install libssh-dev
sudo apt-get install intltool
```
### 编译
下载
```
wget https://github.com/cndaqiang/transmission-2.92_skiphashcheck/archive/master.zip
unzip master.zip
cd cd transmission-2.92_skiphashcheck-master/
```
检查
```
./configure
```
编译，安装
```
make
sudo su
make install
```

### 配置
#### 使用`systemctl`管理程序
```
vi /etc/systemd/system/transmission.service
```
填入
```
[Unit]
Description=Transmission BitTorrent Daemon
After=network.target

[Service]
User=root
LimitNOFILE=100000
ExecStart=/usr/local/bin/transmission-daemon -f --log-error -g /usr/local/share/transmission

[Install]
WantedBy=multi-user.target
```
控制命令
```
#查看状态
systemctl status ssh.service
#重新载入配置信息
systemctl daemon-reload
#启动
systemctl start transmission.service
#关闭
systemctl stop transmission.service
#添加到开机启动
systemctl enable transmission.service
#关闭开开机启动
systemctl disable transmission.service
```

#### 配置文件
关闭`transmission`后在修改配置文件<br>
生成初始配置
```
systemctl daemon-reload
systemctl start transmission.service
systemctl stop transmission.service
```
配置相关目录
```
/usr/local/share/transmission
```
修改
```
vi /usr/local/share/transmission/settings.json
```
其中,设置rpc网页访问
```
    "rpc-authentication-required": false,
    "rpc-bind-address": "0.0.0.0",
    "rpc-enabled": true,
    "rpc-password": "123456", #密码，填入明文，启动后会自动转成密文
    "rpc-port": 9091,         #端口
    "rpc-url": "/transmission/",
    "rpc-username": "root",   #用户名，rpc用户名和密码与系统用户名密码不一样
    "rpc-whitelist": "192.168.1.*,127.0.0.1",  #允许访问的ip
    "rpc-whitelist-enabled": true,
```
之后
```
systemctl start transmission.service
```
就可以通过`http://ip:9091`来访问transmission,然后通过网页配置了

#### 添加防火墙端口
临时生效，其中51413是transmission的Peer listen port
```
iptables -I INPUT -p tcp --dport 51413 -j ACCEPT
iptables-save
```
永久生效
```
sudo apt-get install iptables-persistent
sudo netfilter-persistent save
sudo netfilter-persistent reload
```

## 跳过散列检测
在网页访问时，在任何一个种子上，右键`Ask tracker for more peers`<br>
就会跳过当前的检测
