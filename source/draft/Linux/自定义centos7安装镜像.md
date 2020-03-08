---
title: 自定义CentOS7安装镜像
date: 2020-02-09 18:30:12
categories:
  - Linux
tags:
  - anaconda
description: 基于CentOS7 minimal安装镜像,自定义安装镜像。
typora-root-url: ../
---

# 前言

`Anaconda`是`Red Hat`和`Fedora`(红帽系,包括`CentOS`等)的安装程序,在`Red Hat 7`和`Fedora 17`之后对`Anaconda`进行了很大的优化,主要是加强用户的自定义能力.目前可以通过编写插件的方式来对安装程序和图形界面进行修改和扩展.本文主要阐述如何对以下内容的修改:

* 引导菜单 
* 图形界面
* 扩展安装功能

**注意**: 本文只针对`Red Hat 7` `Fedora 17` `CentOS 7`及以上的操作系统

# 准备工作

本文的实践过程以`CentOS7.6`的`minimal`安装镜像为基础,在此基础上进行修改,对安装功能进行自定义.

准备环境: 

* `CentOS7.6` 虚拟机一台,默认已经安装完毕,下文的所有操作未特别说明,均在改虚拟机中进行.


## 下载镜像

* 镜像下载地址: [http://vault.centos.org/7.6.1810/isos/x86_64/CentOS-7-x86_64-Minimal-1810.iso](http://vault.centos.org/7.6.1810/isos/x86_64/CentOS-7-x86_64-Minimal-1810.iso),可以通过一下命令进行下载: 

```shell
wget http://vault.centos.org/7.6.1810/isos/x86_64/CentOS-7-x86_64-Minimal-1810.iso
```

