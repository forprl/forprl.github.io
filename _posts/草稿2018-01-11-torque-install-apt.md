---
layout: post
title:  "单机ubuntu安装PBS torque"
date:   2018-01-11 16:15:00 +0800
categories: Linux
tags: pbs 集群
author: cndaqiang
mathjax: true
---
* content
{:toc}

尝试编译安装[[失败]单机ubuntu编译安装PBS torque](/2018/01/11/torque-install/)，最后在一步失败了，找到篇ubuntu可以直接apt安装的方法.





# 相关概念
参考[[失败]单机ubuntu编译安装PBS torque](/2018/01/11/torque-install/)

## 节点
对于torque PBS有以下节点
- 管理节点(master)
<br>集群系统的管理节点
<br>编译安装管理Torque PBS
<br>安装pbs_server
- 计算节点
<br>安装pbs_client
<br>安装pbs_mom
- 交作业节点
<br>安装pbs_client


# 安装
此次进行单机安装pbs，即管理节点，计算节点，作业节点都在一个服务器上










