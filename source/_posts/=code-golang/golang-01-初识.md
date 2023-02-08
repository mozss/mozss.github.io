---
title: golang初识
tags:
  - golang
categories:
  - it
  - it-golang
abbrlink: 53225
date: 2022-01-22 22:22:09
---

[![jHe2xe.jpg](https://s1.ax1x.com/2022/07/19/jHe2xe.jpg)](https://imgtu.com/i/jHe2xe)
<!--more-->

## 关于golang

- 2007年开发, 2009年开源, 2021年Go1稳定版
- 特点:
  - 简
  - 并行
  - 迅速
- 用途:
  - 游戏服务器
  - 高性能分布式系统
- 运行:
  - go run.hello.go  直接运行
  - go build hello.go 生成二进制文件, 然后 ./hello 运行文件
- 安装:
  - 需要将c:\go\bin目录添加到path变量中

## 语言结构

- 包声明
  - 第一行位置, package关键字
- 引入包
  - 包声明之后, import关键字
- 函数
  - 每个可执行程序必须包含main函数(没有init()函数情况下, 先执行main函数)
- 变量
- 语句&表达式
- 注释
  - 单行(推荐)
  - /* 这里是多行注释 */

## 基础语法

- go标记
  - 关键字
  - 标识符
  - 常量
  - 字符串
  - 符号
- 行分隔符
  - 一行一个语句结束(推荐)
  - 多行写在一行, 用";"分隔(不推荐)
- 标识符
  - 字母-数字-下划线
  - 数字不开头
- 字符串连接
  - 使用+
- 关键字
  - 25个关键字
  - 36个预定义标识符
  
    ![image.png](https://s2.loli.net/2022/07/20/xfbFydWU9TKAZg1.png)
- 空格
  - 变量的声明中必须使用空格, 比如: var photoNo int;
- 格式化字符串
  - 使用fmt.Sprintf格式化, 并赋予新的值
  




















































