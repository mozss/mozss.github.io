---
title: kafka入门
tags:
  - kafka
categories:
  - code
  - code-java
abbrlink: 62876
date: 2020-10-18 07:16:20
---

<!--more-->

# Kafka 相关概念

Kafka 是一款高吞吐量、分布式、可持久化的消息系统，具有以下特点：

- 高吞吐量：Kafka 可以处理大量的消息流，每秒能够处理上百万条消息。
- 分布式系统：Kafka 可以在多台服务器上运行，实现消息的分布式处理和存储。
- 可持久化的消息存储：Kafka 通过消息存储机制，保证消息的可靠传递和持久化存储。

本文将介绍 Kafka 的相关概念。

## Topic

Topic 是 Kafka 中消息发送和接收的单位，每个 Topic 包含多个消息，每个消息由一个 Key 和一个 Value 组成，通常 Key 用于分割消息流，Value 用于存储具体的消息内容。

- 创建 Topic：可以通过 Kafka 的命令行工具或者 API 来创建 Topic。
- 分区：每个 Topic 可以分为多个分区，每个分区可以在不同节点上存储和处理，实现消息的并行处理。
- 副本：为了保证消息的可靠传递，每个分区可以有多个副本，分布在不同的节点上，当主副本故障时，备用副本可以接替主副本的功能。

## Producer

Producer 是生产者，负责向 Kafka 发送消息并将消息写入 Topic 的分区中。

- 发送消息：Producer 可以通过 Kafka 的 API 来发送消息，向指定 Topic 发送消息。
- 确认机制：Producer 发送消息后，可以通过确认机制获得发送消息的状态。

## Consumer

Consumer 是消费者，负责从 Kafka 中读取消息并处理消息。

- 订阅消息：Consumer 可以通过 Kafka 的 API 来订阅 Topic 上的消息。
- 分配分区：当 Consumer 订阅某个 Topic 后，需要为其分配分区，以实现消息的并行消费。
- 确认机制：消费者也可以通过确认机制来确认收到的消息，从而保证消息的可靠接收和处理。

## Broker

Broker 是 Kafka 的消息中间件，负责接收和处理 Producer 发送的消息、为 Consumer 提供消息服务。

- 存储和处理：Broker 负责存储和处理 Kafka 中的消息，实现消息的持久化存储和并行处理。
- 管理 Topic、Partition 和 Replica：Broker 具有管理 Topic、Partition 和 Replica 的功能，可以实现动态扩展和缩小 Kafka 集群的节点数量，以适应不同的业务需求。
- 队列存储：Broker 使用队列存储的方式，实现高性能的消息处理和传递。

## ZooKeeper

ZooKeeper 是分布式系统协同服务，Kafka 集群中需要使用 ZooKeeper 来进行分布式协调和管理。

- 为 Kafka 派发 Broker ID：ZooKeeper 可以为 Kafka Server 派发全局唯一的 Broker ID，以实现集群内部的节点通信。
- 管理元数据：ZooKeeper 管理了 Kafka 集群中的元数据，包括 Topic、Partition、Broker 等信息。
- 监控：ZooKeeper 可以对 Kafka 集群的状态进行监控和统计，发现和解决问题。

## Conclusion

Kafka 是一款高性能、分布式的消息中间件，支持可靠，高容错性的消息处理，并可轻松与各种数据系统集成。通过学习 Kafka 的相关概念，可以更好地了解和掌握 Kafka 的使用和实践。