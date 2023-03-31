---
title: rocketmq架构
tags:
  - rocketmq
categories:
  - code
  - code-java
abbrlink: 62876
date: 2020-07-11 21:36:30
---

<!--more-->


## 架构图

![RocketMQ架构图](https://rocketmq.apache.org/assets/images/architecture.png)

## 组件说明

### NameServer

NameServer是RocketMQ Broker的路由信息注册中心。它将各个Broker的Cluster信息、Topic信息、路由信息等存储在内存中，提供查询服务给Producer和Consumer。

### Broker

Broker是RocketMQ系统的核心组件，它是负责消息的存储、传输和处理的服务。每个Broker都包含了Message Store、消息队列、消费队列和消息处理线程。

### Producer

Producer是消息的发送端，向某个Topic发送消息，消息将由NameServer查询到对应的Broker，然后通过网络传输到Broker。Producer将数据源封装成消息后发送给Broker。

### Consumer

Consumer是消息的接收端，向Broker订阅某个Topic，消费消费队列中的消息。消息处理完成后，Consumer同步向Broker发送消息确认信息。

## Java代码示例

### Producer

```java
public class RocketMQProducer {
    public static void main(String[] args) throws MQClientException {
        // 创建生产者实例
        DefaultMQProducer producer = new DefaultMQProducer("group");
        // 指定NameServer地址
        producer.setNamesrvAddr("127.0.0.1:9876");
        // 启动生产者
        producer.start();

        try {
            // 创建消息实例
            Message message = new Message("topic", "tag", "Hello RocketMQ".getBytes());
            // 发送消息
            SendResult result = producer.send(message);
            System.out.println(result);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 关闭生产者
            producer.shutdown();
        }
    }
}
```

### Consumer

```java
public class RocketMQConsumer {
    public static void main(String[] args) throws MQClientException {
        // 创建消费者实例
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group");
        // 指定NameServer地址
        consumer.setNamesrvAddr("127.0.0.1:9876");
        // 订阅Topic和Tag
        consumer.subscribe("topic", "tag");
        // 注册消息监听器
        consumer.registerMessageListener((List<MessageExt> msgs, ConsumeConcurrentlyContext context) -> {
            // 处理消息
            for (MessageExt msg : msgs) {
                System.out.println(new String(msg.getBody()));
            }
            return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
        });
        // 启动消费者
        consumer.start();
    }
}
```
