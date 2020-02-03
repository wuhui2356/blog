---
title: GRUB与GRUB2的区别
date: 2019-08-14 15:48:43
categories:
  - Linux
tags:
  - GRUB
  - Linux
  - GRUB2
description: 简单来说GRUB就是电脑开机过后运行的第一个程序，它负责加载操作系统内核并且将当前系统的控制权移交给操作系统内核。
---

## 什么是GRUB
  * 简单来说`GRUB`就是电脑开机过后运行的第一个程序，它负责加载操作系统内核并且将当前系统的控制权移交给操作系统内核。
<!-- more -->
    详细信息参见`GRUB`的文件。

##  关于版本

  * `GRUB2`是将`GRUB`完全重构后的版本，所以又将之前版本的`GRUB`称为`GRUB Legacy`
  * `GRUB Legacy`的最后一个版本为: `0.97`，即`0.97`之后的版本均为`Grub2`,目前`GRUB Legacy`已经不再做开发了，只进行相关`bug`的修复，以保证一些旧的系统任然能够使用`GRUB Legacy`
  * `GRUB2`的版本是从`1.90`开始，目前已经到了`2.04`,它与`GRUB Legacy`有许多相同的特性，具体的差异见下一小节。

## GRUB2与GRUB Legacy的差异


## 未完待续

## 参考文档
  * [项目地址](https://www.gnu.org/software/grub/)
  * [grub legacy文档](https://www.gnu.org/software/grub/manual/legacy/index.html#Top)
  * [grub2文档](https://www.gnu.org/software/grub/manual/grub/grub.html)
  * [镜像地址](https://alpha.gnu.org/gnu/grub/)