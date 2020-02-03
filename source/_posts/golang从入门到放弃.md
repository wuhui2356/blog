---
title: golang从入门到放弃
date: 2020-02-03 22:36:07
categories:
  - 编程基础
tags:
  - golang
description: 简单记录一下golang学习过程中踩过的一些坑。
---

# 前言

**Go**（又称 **Golang**）是 Google 的 Robert Griesemer，Rob Pike 及 Ken Thompson 开发的一种静态强类型、编译型语言。Go 语言语法与 C相近，但功能上有：内存安全，GC(垃圾回收），结构形态及 CSP-style 并发计算。

# 包管理 Go Mod

`go modules` 是 golang 1.11 添加的新特性(默认关闭)，在`1.11`之前的版本中，针对于第三方库的引用，直接讲对应的库文件放在`GOPATH`环境变量下即可，故通常会设置一个系统全局的`GOPATH`以及一个或者多个工程的`GOPATH`。但是在引入了`go modules`以后，就直接通过`go.mod`文件(该文件不能存在与`GOPATH`环境变量中)来进行第三方库的管理，同时需要设置环境变量`GO111MODULE=on`来开启`go modules`， 通过`go get`命令下载的所有库都会自动托管到`go.mod`文件中。