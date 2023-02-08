---
title: golang初识----数据类型, 变量
tags:
  - golang
categories:
  - it
  - it-golang
abbrlink: 31734
date: 2022-02-07 10:16:20
---
  
[![jHeWKH.jpg](https://s1.ax1x.com/2022/07/19/jHeWKH.jpg)](https://imgtu.com/i/jHeWKH)
<!--more-->

## 数据类型

- 布尔型
- 数字类型
  - 整型
    - uint8
    - uint16
    - uint32
    - uint64
    - int8
    - int16
    - int32
    - int64
  - 浮点型
    - float32
    - float64
    - complex64: 32位实数和虚数
    - complex128: 64位实数和虚数
- 字符串类型
- 派生类型
  - 指针类型Pointer
  - 数组类型
  - 结构化类型struct
  - Channel类型
  - 函数类型
  - 切片类型
  - 接口类型interface
  - Map类型
- 其他
  - byte: 类似uint8
  - rune: 类似int32
  - uint: 32或64位
  - int: 与uint一样大小
  - uintptr: 无符号整型, 用于存放一个指针
  
## 变量

- 变量声明
- 声明(没初始化)
  - var 变量名 类型
  - var 变量名1 类型, 变量名2 类型
  - 默认为零值
    - 数值类型为0
    - 布尔类型为false
    - 字符串为""
    - 其他类型为nil
- 根据值自动判定类型
- :=
- 多变量声明
  - 类型相同, 非全局变量
      var vname1, vname2, vname3 type
      vname1, vname2, vname3 = v1, v2, v3
  - 全局变量
      var(
          vname1 v_type1
          vname2 v_type2
    )
  

## 常量

- 格式:
  - **const** 变量名 类型(可省略) = 值
  - 多个变量
    const(
        变量名1=值
        变量名2=值
        变量名3=值
    )

- iota的使用
  - 可以被编译器修改的常量


























