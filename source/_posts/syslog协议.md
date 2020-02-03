---
title: syslog协议
date: 2019-08-15 17:38:00
categories:
  - Linux
tags:
  - syslog
  - linux
  - 日志
  - 协议
description: syslog协议属于一种主从式协议，通常被用于信息系统管理及信息安全审核。虽然它有不少缺陷，但仍获得相当多的设备及各种平台的接收端支持。因此syslog能被用来将来自许多不同类型系统的日志记录集成到集中的存储库中。
---

## 简介
  * `syslog`协议属于一种主从式协议，通常被用于信息系统管理及信息安全审核。虽然它有不少缺陷，但仍获得相当多的设备及各种平台的接收端支持。因此`syslog`能被用来将来自许多不同类型系统的日志记录集成到集中的存储库中。
<!-- more -->  

## 协议内容

  * `syslog`格式为一个长字符串，整个数据报分为三个部分：`PRI`、`HEADER`、`MSG`
    * `PRI`:  只能为3、4、5个字符，并且以小于符号开始，以大于符号结束，中间为一个1到3位的数字
    * `HEADER`: 由`TimeStamp`与`HostName`组成。`HEADER`部分紧跟`PRI`，中间没有空格。`TimeStamp`与`HostName`之间间隔一个空格。`HostName`后面紧跟一个空格。
      * `TimeStamp`: 字段使用`local time`，格式为：`Mmm dd hh:mm:ss`
      * `Mmm`: 是英语月份的单词缩写，例如：Jan, Feb, Mar, Apr, May, Jun, Jul, Aug, Sep, Oct, Nov, Dec
      * `dd`: 某个月的多少号，固定宽度2个字符，不足两个字符在数字前面用空格填充。
      * `hh:mm:ss`: 本地时间
        - `hh`: 24时制的小时，在00-23之间
        - `mm`和`ss`： 分和秒，在00-59之间
      * `HostName`: 该字段为主机的`hostname`。该字段后面紧跟一个空格
    * `MSG`:  该字段又两部分组成： `TAG`和`CONTENT`
      * `TAG`: 该字段的值为产生日志的程序名，即推送日志到外部系统的程序名（不超过32个字符）。`TAG`后面紧跟一个用中括号包含着的`pid`,即`[pid]`,并且再追加一个`:`,`pid`为该推送程序的进程号
      * `CONTENT`: 该字段为具体的日志内容
  * 综上，整个数据报为：
```
<xxx>Mmm dd hh:mm:ss hostname tag[pid]: xxxxxxxxx
```

## rsyslog服务器配置

### 安装`syslog`服务

```
sudo yum install rsyslog -y
```

###  修改配置文件

```
$ModLoad imudp
$UDPServerRun 514

$ModLoad imtcp
$InputTCPServerRun 514

$template RemoteLogs,"/var/log/%HOSTNAME%/%PROGRAMNAME%.log" *
*.*  ?RemoteLogs
& ~
```

###  启动`rsyslog`服务生效

```
systemctl restart rsyslog
```

## 参考文献