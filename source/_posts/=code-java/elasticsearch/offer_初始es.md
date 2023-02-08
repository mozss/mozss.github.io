---
title: 初识es
tags:
  - elasticsearch
categories:
  - it
  - it-java
abbrlink: 16e5a472
date: 2020-08-12 17:16:20
---

[![jTaz5D.jpg](https://s1.ax1x.com/2022/07/19/jTaz5D.jpg)](https://imgtu.com/i/jTaz5D)

<!--more-->

## elasticsearch术语

- 索引index
- 类型type
- 文档document
- 字段fields

## elasticsearch概念

- 映射mapping: 可以想象成是数据库表字段结构的定义
- 近实时NRT: 近实时,也就是接近真实的时间; 一个文档存储在es中, 到用户可以搜索出结果, 这中间有一定的延时, 控制在1s以内
- 节点node: 每一个服务, 用于构建es集群
- shard replica: 数据分片与备份

## es集群架构原理

[![j7SnzV.md.png](https://s1.ax1x.com/2022/07/19/j7SnzV.md.png)](https://imgtu.com/i/j7SnzV)

shard和replica本质都是一个shard, 但职责是不同的
- shard: 主分片, 用来存储数据和提供查询
- replica: 对数据进行冗余存储, 提供高可用性, 类似mysql主从的思想; 同时也提供搜索功能, 以提高吞吐量
但是创建索引时, 主分片指定好之后, 就无法再做更改了, 而replica是可用动态规划的

假设一个shard的吞吐量为1000/s, 那么3个shard就能提供3000/s的吞吐量, 存储量也就会平均分配到每个shard上, 所以就能很方便的进行横向拓展.

## 倒排索引
[![j79PEQ.md.png](https://s1.ax1x.com/2022/07/19/j79PEQ.md.png)](https://imgtu.com/i/j79PEQ)


倒排索引概念: 倒排索引源于实际应用中需要根据属性的值来查找记录. 这种索引表中的每一项都包括一个属性值和包含属性值的各个记录地址. 由于不是根据记录来确定属性, 而是根据属性来确定记录的位置, 所以称之为倒排索引.

















