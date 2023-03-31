---
title: kafka实践
tags:
  - kafka
categories:
  - code
  - code-java
abbrlink: 62876
date: 2020-09-01 14:16:20
---

<!--more-->

# Kafka实践及代码示例

## 简介

Apache Kafka是一款实时数据流处理中心，可以快速高效地处理大规模、高吞吐量的数据流，由LinkedIn于2011年开发，后于2012年成为了Apache基金会的一个顶级开源项目。

Kafka以分布式消息队列的形式提供了高可靠（容错性高，支持数据冗余备份）、高伸缩性（支持多节点、多分区）、高吞吐率和低延迟的特性，适用于构建实时数据流的处理方案，被广泛应用于互联网、金融、电商等领域。

本文将介绍如何使用Java代码进行Kafka实践，包括Kafka配置、消息的生产者和消费者的实现。

## 环境搭建

首先，需要搭建Kafka运行环境，具体步骤请参考[Kafka官方文档](https://kafka.apache.org/quickstart)。

## Kafka配置

Kafka的配置文件位于`config/server.properties`，可以根据需要进行修改。

以下是常用的配置项及其含义：

- `broker.id`：Broker的唯一标识符，具有唯一性；
- `listeners`：Broker监听的网络地址；
- `advertised.listeners`：供客户端访问的Broker地址；
- `num.network.threads`：处理网络请求的线程数；
- `num.io.threads`：处理磁盘IO的线程数；
- `socket.send.buffer.bytes`：发送数据缓冲区大小；
- `socket.receive.buffer.bytes`：接收数据缓冲区大小；
- `socket.request.max.bytes`：最大请求大小；
- `num.partitions`：每个Topic的分区数；
- `log.dirs`：Topic数据存储路径；
- `auto.create.topics.enable`：是否允许自动创建Topic；
- `delete.topic.enable`：是否允许删除Topic；
- `group.initial.rebalance.delay.ms`：Consumer组初始负载均衡延迟时间；
- `offsets.topic.replication.factor`：偏移量Topic的副本因子。

## Kafka生产者

Kafka生产者用于向指定的Topic中发送消息，Java代码示例如下：

```java
import org.apache.kafka.clients.producer.*;
import java.util.Properties;

public class KafkaProducerDemo {
    private static final String TOPIC_NAME = "test_topic";
    private static final String BOOTSTRAP_SERVERS = "localhost:9092";

    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers", BOOTSTRAP_SERVERS);
        props.put("acks", "all");
        props.put("retries", 0);
        props.put("batch.size", 16384);
        props.put("linger.ms", 1);
        props.put("buffer.memory", 33554432);
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        Producer<String, String> producer = new KafkaProducer<>(props);
        for (int i = 0; i < 10; i++) {
            String message = "Message " + i;
            producer.send(new ProducerRecord<>(TOPIC_NAME, message));
            System.out.println("Sent:" + message);
        }
        producer.close();
    }
}
```

以上代码实现了一个简单的Kafka生产者，可以向指定的`TOPIC_NAME`中发送10条消息。具体参数配置说明如下：

- `bootstrap.servers`：Kafka集群地址；
- `acks`：消息确认模式；
    - `0`：不等待Broker的任何确认；
    - `1`：等待Broker确认消息已写入本地磁盘；
    - `all`：等待Broker确认消息已写入所有ISR副本；
- `retries`：消息发送失败后的重试次数；
- `batch.size`：消息批处理大小，单位为字节；
- `linger.ms`：延迟发送消息的时间，单位为毫秒；
- `buffer.memory`：生产者可用的内存缓存大小，单位为字节；
- `key.serializer`：序列化消息键的方式；
- `value.serializer`：序列化消息值的方式。

## Kafka消费者

Kafka消费者用于从指定的Topic中消费消息，Java代码示例如下：

```java
import org.apache.kafka.clients.consumer.*;
import java.time.Duration;
import java.util.Collections;
import java.util.Properties;

public class KafkaConsumerDemo {
    private static final String TOPIC_NAME = "test_topic";
    private static final String BOOTSTRAP_SERVERS = "localhost:9092";
    private static final String GROUP_ID = "test_group";

    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers", BOOTSTRAP_SERVERS);
        props.put("group.id", GROUP_ID);
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        Consumer<String, String> consumer = new KafkaConsumer<>(props);
        consumer.subscribe(Collections.singletonList(TOPIC_NAME));
        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
            for (ConsumerRecord<String, String> record : records) {
                System.out.printf("Received message: value = %s, topic = %s, partition = %d, offset = %d%n",
                        record.value(), record.topic(), record.partition(), record.offset());
            }
            consumer.commitAsync();
        }
    }
}
```

以上代码实现了一个简单的Kafka消费者，可以从指定的`TOPIC_NAME`中消费消息。具体参数配置说明如下：

- `bootstrap.servers`：Kafka集群地址；
- `group.id`：消费者组的唯一标识符；
- `enable.auto.commit`：是否开启自动提交偏移量；
- `auto.commit.interval.ms`：自动提交偏移量的时间间隔，单位为毫秒；
- `key.deserializer`：消息键的反序列化方式；
- `value.deserializer`：消息值的反序列化方式。

## 总结

Kafka是一款高可靠、高伸缩性、高吞吐率和低延迟的分布式消息队列，广泛应用于互联网、金融、电商等领域。本文介绍了Kafka的基本概念及其Java代码实践，希望对读者有所帮助。