---
title: kafka架构
tags:
  - kafka
categories:
  - code
  - code-java
abbrlink: 62876
date: 2020-09-10 21:36:10
---

<!--more-->

# Kafka 架构

## Kafka 架构

Kafka 架构主要分为以下几个部分：

1. Topic：消息的录入点，每个 Topic 可以被分成多个 Partition，每个 Partition 可以存储多个消息。
2. Producer：消息的生产者，负责往 Kafka 集群中的 Topic 发送消息，将消息放到指定的 Partition 中。
3. Broker：消息经过 Producer 生产后，会发送到多个 Broker 上，Broker 维护了消息的持久化存储和 Snapshot 文件，使得 Kafka 集群可以在 Broker 崩溃或网络故障的情况下依然可以保证消息的持久性。
4. Consumer：消息的消费者，从指定的 Topic 的 Partition 中消费消息，消费后的消息将不能被再次消费，一旦消息被消费，该消费者就读取该消息的 Offset 位置，以便下一次从该位置开始消费。
5. ZooKeeper：用于管理 Broker 集群，负责维护集群中每个 Broker 的元数据，并且在 Broker 发生变化时通知 Consumer。

## Kafka 的工作流程

1. Producer 将消息发送到 Broker。
2. Broker 将消息持久化到磁盘，并将消息存储到对应的 Partition 中。
3. Consumer 从指定的 Partition 中消费消息，并将 Offset 位置保存到 ZooKeeper 中。
4. 当有新消息到达时，Consumer 将自动从上一次消费的 Offset 位置继续消费。

## Java 代码示例

以下是一个简单的 Java 代码示例，展示了如何使用 Kafka 的 Producer 模块发送消息到指定的 Topic 中：

```java
// 配置 Kafka 生产者
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

// 创建 Kafka 生产者
Producer<String, String> producer = new KafkaProducer<>(props);

// 生产并发送消息
for (int i = 0; i < 100; i++) {
    producer.send(new ProducerRecord<>("my-topic", Integer.toString(i), Integer.toString(i)));
}        

// 关闭 Kafka 生产者
producer.close();
```

## 结语

Kafka 是一个强大的消息引擎，它可以使得大规模数据处理变得更加高效、灵活，适合于各种大数据处理场景。希望本文能为大家对 Kafka 的理解和应用提供帮助。