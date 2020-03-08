---
title: Linux 下扩展磁盘分区
date: 2019-10-29 14:00:35
categories:
  - Linux
tags:
  - Linux
  - 分区
  - parted
description: 我们在使用Linux作为虚拟机进行日常开发工作，经常会遇到某个磁盘空间不足的情况，这种情况下有两种磁盘空间扩容的方法，即当前系统是否创建了逻辑卷。
---

## 扩展指定的磁盘分区

### 首先查看当前系统的磁盘和分区大小
  

```
// 查看所有的磁盘和分区大小
[root@localhost /]# lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0 29.8G  0 disk 
└─sda1   8:1    0 23.4G  0 part /
sdb      8:16   0  3.7T  0 disk 
├─sdb1   8:17   0   32G  0 part 
└─sdb2   8:18   0  3.6T  0 part 
```

```
//  查看当前文件系统的使用量
[root@localhost /]# df -h
文件系统        	 容量  已用  可用  已用% 挂载点
/dev/root        19G   18G     0  100% /
devtmpfs         16G     0   16G    0% /dev
tmpfs            16G     0   16G    0% /dev/shm
tmpfs            16G   16M   16G    1% /run
tmpfs           5.0M     0  5.0M    0% /run/lock
tmpfs            16G     0   16G    0% /sys/fs/cgroup
tmpfs           3.1G     0  3.1G    0% /run/user/0
```