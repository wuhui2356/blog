---
title: 逻辑卷LVM相关操作
date: 2019-08-15 09:30:30
categories:
  - linux
tags:
  - LVM
  - linux
description: 逻辑卷是将几个磁盘分区或者块设备组织起来形成的一个大的扩展分区,该扩展分区不能直接用，需要将其划分成逻辑卷（lv）才能使用，lv可以格式化成不同的文件系统，挂载后直接使用。
---


## 1. 什么是逻辑卷
  * 逻辑卷（lv）是将几个磁盘分区或者块设备(pv，pv的id必须是8e的，pv可以位于不同的磁盘分区里，pv大小可以不一)组织起来形成一个大的扩展分区（vg,卷组，一个vg至少要包含一个pv。），该扩展分区不能直接用，需要将其划分成逻辑卷（lv）才能使用，lv可以格式化成不同的文件系统，挂载后直接使用。
<!-- more -->  
  * lv的扩展和缩减是不会影响原有数据的，但逻辑卷缩减的风险大于逻辑卷扩展的风险。
  * 逻辑卷可以支持快照功能。

## 2. 相关术语
  * 物理卷（PV, Physical Volume）
    * 物理卷就是指磁盘,磁盘分区或从逻辑上和磁盘分区具有同样功能的设备(如RAID)，是LVM的基本存储逻辑块，但和基本的物理存储介质（如分区、磁盘等）比较，却包含有和LVM相关的管理参数。当前LVM允许你在每个物理卷上保存这个物理卷的0至2份元数据拷贝.默认为1,保存在设备的开始处.为2时,在设备结束处保存第二份备份.
    

  * 卷组（VG, Volume Group）
    * LVM卷组类似于非LVM系统中的物理硬盘，其由物理卷组成。能在卷组上创建一个或多个“LVM分区”（逻辑卷），LVM卷组由一个或多个物理卷组成。


  * 逻辑卷（LV, Logical Volume）
    * LVM的逻辑卷类似于非LVM系统中的硬盘分区，在逻辑卷之上能建立文件系统(比如/home或/usr等)。


  * 线性逻辑卷 (Linear Volumes)
    * 一个线性逻辑卷聚合多个物理卷成为一个逻辑卷.比如,如果你有两个60GB硬盘,你能生成120GB的逻辑卷.


  * 条块化的逻辑卷(Striped Logical Volumes)
    * 当你写数据到此逻辑卷中时,文件系统能将数据放置到多个物理卷中.对于大量连接读写操作,他能改善数据I/O效率.


  * 映像的逻辑卷(Mirrored Logical Volumes)
    * 映像在不同的设备上保存一致的数据.数据同时被写入原设备及映像设备.他提供设备之间的容错。


  * 快照卷(Snapshot Volumes)
    * 快照卷提供在特定瞬间的一个设备虚拟映像，当快照开始时，他复制一份对当前数据区域的改动，由于他优先执行这些改动，所以他能重构当前设备的状态。

  * 参考: [https://www.cnblogs.com/shxdyz/articles/7834980.html](https://www.cnblogs.com/shxdyz/articles/7834980.html)

## 3. 查看逻辑卷信息

### 3.1 查看LV
  * `lvscan`
```
[root@localhost ~]# lvscan
  ACTIVE            '/dev/VG_BASTION/LV_SWAP' [2.00 GiB] inherit
  ACTIVE            '/dev/VG_BASTION/LV_DATA' [<57.98 GiB] inherit
```

### 3.2 查看VG
  * `vgscan`
```
[root@localhost ~]# vgscan
  Reading volume groups from cache.
  Found volume group "VG_BASTION" using metadata type lvm2
```

### 3.3 查看PV
  * `pvscan`
```
[root@localhost ~]# pvscan
  PV /dev/sdb1   VG VG_BASTION      lvm2 [19.99 GiB / 0    free]
  PV /dev/sdc1   VG VG_BASTION      lvm2 [19.99 GiB / 0    free]
  PV /dev/sdd1   VG VG_BASTION      lvm2 [19.99 GiB / 0    free]
  Total: 3 [<59.98 GiB] / in use: 3 [<59.98 GiB] / in no VG: 0 [0   ]
```

## 4. 创建逻辑卷

### 4.1 说明
  * 再创建逻辑卷时，应当先创建物理卷(pv)，再创建卷组(vg)，最后再创建逻辑卷(lv)

### 4.2 创建PV
  * `pvcreate`
```
pvcreate <device>

// device: 要创建物理卷的设备路径

示例:

[root@centos6 bash]# pvcreate /dev/sda1 
  Physical volume "/dev/sda1" successfully created
```
  
### 4.3 创建VG
  * `vgcreate`
```
vgcreate <vg_name> <pv list>

// vg_name: 卷组名
// device list: 要添加到卷组的物理卷pv列表

示例:

[root@centos6 bash]# vgcreate vg_bastion /dev/sda1 /dev/sdc1 
  Volume group "vg_bastion" successfully created
```

### 4.4 修改卷组属性
  * `vgchange`
    * 激活卷组: `vgchange -ay vg_bastion`

### 4.5 创建LV
  * `lvcreate`
```
lvcreate -n <lv_name> -L <size> <vg_name> -y
lvcreate -n <lv_name> -l <size> <vg_name> -y

// -L: 指定大小(--size LogicalVolumeSize[bBsSkKmMgGtTpPeE])
// -l: 可以指定百分比(--extents LogicalExtentsNumber[%{VG|FREE|ORIGIN}]),如 100%FREE

// lv_name: 逻辑卷名
// size： 创建的卷大小
// vg_name: 卷组名


示例:
    lvcreate -n "lv_swap" -L "2GiB" "vg_bastion" -y
    lvcreate -n "lv_data" -l "100%FREE" "vg_bastion" -y
```

## 5. 删除逻辑卷

### 5.1 说明
  * 在删除逻辑卷之前，应当先卸载掉已经被挂载的分区，然后再进行逻辑卷的删除。
    * 在卸载分区时，如果遇到当前分区正在被使用，无法进行卸载的情况，可以借助`lsof`命令找到正在占用挂载点的进程，然后将其`kill`掉，再进行卸载在操作。
  * 逻辑卷的删除顺序与创建顺序相反，首先删除逻辑卷(lv)，然后删除卷组(vg)，最后删除物理卷(pv)

### 5.2 删除LV
  * `lvremove`
```
[root@localhost ~]# lvremove /dev/VG_BASTION/LV_SWAP -f -y
  Logical volume "LV_SWAP" successfully removed
```

### 5.3 删除VG
  * `vgremove`
```
[root@localhost ~]# vgremove VG_BASTION -f -y
  Volume group "VG_BASTION" successfully removed
```

### 5.4 删除PV
  * `pvremove`
```
[root@localhost ~]# pvremove /dev/sdb1 -f -y
  Labels on physical volume "/dev/sdb1" successfully wiped.
```

## 6. 磁盘分区

### 6.1 创建磁盘分区
  * 创建分区表,采用 `msdos(MBR)`或者`GPT`
    * `GPT`分区表: `parted -s /dev/sdd mklabel gpt`
    * `MBR`分区表: `parted -s /dev/sdd mklabel msdos`
  * 创建主分区: 
```
parted -s <device> mkpart <part-type> [fs-type] start end

// deice: 指定磁盘设备
// part-type: 只能为 primary(主分区)、logical(逻辑分区)、extended(扩展分区)
// fs-type: 非必填参数,可以为: "btrfs",  "ext2",  "ext3",  "ext4",  "fat16",  "fat32", "hfs", "hfs+", "linux-swap", "ntfs", "reiserfs", or "xfs"
// start: 指定分区开始位置，默认为磁盘起始位置
// end: 指定分区结束位置,默认为磁盘结束位置

示例:
    parted -s /dev/sdd mkpart primary 4MiB 100%   
    // 在 /dev/sdd 上创建一个主分区，从4MiB的位置开始，到磁盘末尾结束
    // 即整个分区大小为 磁盘大小-4MiB
```
  * 修改分区表状态
```
parted -s <device> toggle <partition_number> <status>

// device: 指定磁盘设备
// partition_number: 该磁盘设备上的第几个分区
// status: 转换后的状态(raid|lvm)

示例:
    parted -s /dev/sdd toggle 1 "lvm"
    // 将 /dev/sdd 上的第一个分区标记为 lvm

```

### 6.2 删除磁盘分区
  * 可以通过`parted`删除指定的某个分区: `parted -s /dev/sdc rm 1`
```
parted -s <device> rm <partition_number>

// device: 指定磁盘设备
// partition_number: 该磁盘设备上的第几个分区

示例: 
    parted -s /dev/sdc rm 1
    // 删除 /dev/sdc 上的第一个分区
    
```
  * 一般情况下，如果要摧毁整个磁盘分区，那么只需要将磁盘首部的分区表摧毁掉就行了，所以，可以直接采用`dd`命令: `dd if=/dev/zero of=/dev/sdd bs=16MiB count=1`

### 6.3 磁盘格式化
  * 制作交换分区: `mkswap`
```
mkswap -f -L <label> <device>

// device: 指定磁盘设备
// label: 指定交换分区的标签label

示例：

  mkswap -f -L label_swap /dev/vg_bastion/lv_swap

```


  * 格式化: `mkfs`
```

mkfs.<fs> -L <label> <device>

// fs: 文件系统格式
// device: 指定磁盘设备
// label: 指定交换分区的标签label

示例:
    mkfs.xfs -L label_data /dev/vg_bastion/lv_data
    mkfs.ext4 -L label_data /dev/vg_bastion/lv_data

    mkfs -t xfs -L label_data /dev/vg_bastion/lv_data
    mkfs -t ext4 -L label_data /dev/vg_bastion/lv_data
```
  
##  7 数据无价，操作需谨慎

## 8 参考文献
  * [https://blog.csdn.net/solaraceboy/article/details/78539233](https://blog.csdn.net/solaraceboy/article/details/78539233)
  * [https://www.cnblogs.com/shxdyz/articles/7834980.html](https://www.cnblogs.com/shxdyz/articles/7834980.html)