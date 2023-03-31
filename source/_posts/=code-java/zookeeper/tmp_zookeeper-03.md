---
title: zookeeper落地实践
tags:
  - zookeeper
categories:
  - code
  - code-java
abbrlink: 62876
date: 2018-01-01 01:16:20
---
[![ppnXIvn.png](https://s1.ax1x.com/2023/03/10/ppnXIvn.png)](https://imgse.com/i/ppnXIvn)

<!--more-->

## Kafka落地实践

### 前言

Kafka是一个分布式的消息队列系统，常用于解决大规模数据处理、实时数据传输等问题。在本文中，我们将介绍在Java语言中如何使用Kafka实现消息的发布和订阅。

### 环境准备

在使用Kafka前，我们需要准备以下环境：

1. JDK 1.8及以上版本
2. Kafka 2.4.1及以上版本
3. Kafka相关依赖包

### 快速入门

#### 创建Topic

在Kafka中，我们需要先创建一个Topic（主题）来存储消息。可以使用Kafka提供的脚本工具创建Topic，命令如下：

```shell
$ kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test
```

以上命令将会在Kafka中创建一个名为test的Topic，副本因子为1，分区数为1。

#### Producer

消息的发送者称为Producer。在Java语言中，我们可以使用Kafka提供的KafkaProducer类来创建一个Producer实例。示例代码如下：

```java
import java.util.Properties;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
 
public class ProducerExample {
    public static void main(String[] args) {
        // Producer配置信息
        Properties props = new Properties();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("acks", "all");
        props.put("retries", 0);
        props.put("batch.size", 16384);
        props.put("linger.ms", 1);
        props.put("buffer.memory", 33554432);
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
 
        // 创建Producer实例
        KafkaProducer<String, String> kafkaProducer = new KafkaProducer<>(props);
 
        // 发送消息
        for (int i = 0; i < 10; i++) {
            String msg = "消息 " + i;
            ProducerRecord<String, String> producerRecord = new ProducerRecord<>("test", msg);
            kafkaProducer.send(producerRecord);
        }
 
        // 关闭Producer实例
        kafkaProducer.close();
    }
}
```

以上代码创建了一个Producer实例，并发送了10条字符串消息到名为test的Topic中。其中，配置信息中的属性可根据实际需求进行调整。

#### Consumer

消息的消费者称为Consumer。在Java语言中，我们可以使用Kafka提供的KafkaConsumer类来创建一个Consumer实例。示例代码如下：

```java
import java.util.Collections;
import java.util.Properties;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.consumer.KafkaConsumer;
 
public class ConsumerExample {
    public static void main(String[] args) {
        // Consumer配置信息
        Properties props = new Properties();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("group.id", "test-group");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
 
        // 创建Consumer实例
        KafkaConsumer<String, String> kafkaConsumer = new KafkaConsumer<>(props);
 
        // 订阅Topic
        kafkaConsumer.subscribe(Collections.singletonList("test"));
 
        // 消费消息
        while (true) {
            ConsumerRecords<String, String> records = kafkaConsumer.poll(100);
            records.forEach(record -> {
                System.out.printf("消费者：%s, 分区：%d, 偏移：%d, 消息：%s%n", 
                                record.key(), record.partition(), record.offset(), record.value());
            });
        }
    }
}
```

以上代码创建了一个Consumer实例，并订阅了名为test的Topic。在消费消息时，我们使用了一个无限循环，在每次循环中调用了KafkaConsumer的poll方法从Topic中拉取最新的消息。

### 总结

以上便是使用Java语言实现Kafka落地实践的简单示例，通过这些代码，我们可以了解到Kafka的基本使用方法。当然，在实际项目中，Kafka的应用远比我们在这篇文章中介绍的要复杂，因此需要根据实际业务需求进行详细的配置和应用。