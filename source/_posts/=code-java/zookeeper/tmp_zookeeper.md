---
title: 初识zookeeper
tags:
  - zookeeper
categories:
  - code
  - code-java
abbrlink: 41723
date: 2020-09-02 16:06:20
---
[![jTaXb6.jpg](https://s1.ax1x.com/2022/07/19/jTaXb6.jpg)](https://imgtu.com/i/jTaXb6)

<!--more-->

## zookeeper被用在了哪里?

- hbase: 在hbase中, zookeeper用于选举一个集群内的主节点, 以便跟踪可以的服务器, 并保存集群的元数据.
- kafka: 在kafka中, 用于检测崩溃, 实现topic的发现, 并保持主题的生产和消费状态
- solr: 使用zookeeper来存储集群的元数据, 并协作更新这些元数据.


## 分布式系统需要注意的问题

- 消息延时: 可能因为网络拥堵, 这种任意延迟可能会导致不可预期的后果. 比如: 根据基准时钟, 进程p先发送一个消息后, 之后另一进程q发送了消息, 但是q也许会先完成传送.

- 处理器性能: 操作系统的调度和超载也可能会导致消息处理的任意延迟. 当一个进程想另一个进程发送消息的时候, 整个消息的延时时间约等于发送端消耗的时间, 传输时间, 接收端的处理时间的总和. 如果发送或接收过程需要调度时间进行处理, 消息延时会更高.

- 时钟偏移: 使用时间概念的系统并不少见, 比如, 确定某一时间系统中发生了哪些事件. 处理器时钟并不可靠, 它们之间也会发生任意的偏移. 因此, 依赖处理器时钟也许会导致错误的决策.


## 主-从模式的系统, 我们必须要解决以下3个关键问题:

- 主节点崩溃: 如果主节点发送错误并失效, 系统就无法分配新的任务或重新分配已失败的任务

- 从节点崩溃: 如果从节点崩溃, 已分配的任务将无法完成

- 通信故障: 如果主节点和从节点之间无法进行信息交换, 从节点将无法得知新任务分配给它.


## 我们可以得出主从架构的主要需求:

- 主节点选举
- 崩溃检测
- 组成员关系管理
- 元数据管理






























