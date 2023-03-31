---
title: rocketmq避坑
tags:
  - rocketmq
categories:
  - code
  - code-java
abbrlink: 62876
date: 2020-08-01 21:26:23
---
[![ppnXqET.png](https://s1.ax1x.com/2023/03/10/ppnXqET.png)](https://imgse.com/i/ppnXqET)

<!--more-->
# RocketMQ消息中间件的避坑指南

> RocketMQ是一个高性能、高可靠、可扩展的分布式消息中间件。在使用过程中，我们可能会遇到一些坑，本篇文章将为大家指出其中一些，并提供相应的解决方案。

## 1. 错误的Producer Group

在发送消息时，我们需要使用`Producer`将消息发送到RocketMQ中。在创建`Producer`时，我们需要指定一个`Producer Group`，用于标识一组具有相同功能的`Producer`。如果我们使用了相同的`Producer Group`发送不同类型的消息，就会造成消息丢失或混淆的问题。因此，我们需要为每种类型的消息创建不同的`Producer Group`。

```java
// 正确的示例
DefaultMQProducer productA = new DefaultMQProducer("productA");
DefaultMQProducer productB = new DefaultMQProducer("productB");

// 错误的示例
DefaultMQProducer product = new DefaultMQProducer("product");
```

## 2. 误用同步消息发送

`RocketMQ`支持两种方式发送消息：同步和异步。在使用同步方式发送消息时，我们需要等待消息被成功写入`CommitLog`后再返回，否则将会抛出异常。如果我们在生产环境中误用同步方式发送大量消息，可能会导致生产者线程被阻塞，从而降低了整个系统的性能。

```java
// 正确的示例
producer.send(message, new SendCallback(){
    @Override
    public void onSuccess(SendResult sendResult) {}

    @Override
    public void onException(Throwable throwable) {}
});

// 错误的示例
producer.send(message);
```

## 3. 消息体过大

`RocketMQ`默认设置了消息大小限制，最大限制为4MB。如果我们发送的消息超过了限制，将会导致消息发送失败。因此，在发送消息前，我们需要确保消息体大小不会超过限制。

```java
// 正确的示例，限制消息体大小为1MB
message.setBody(new byte[1024 * 1024]);

// 错误的示例，消息体大小超过了限制
message.setBody(new byte[1024 * 1024 * 5]);
```

## 4. 错误的Consumer Group

在`RocketMQ`中，一个消费者组可以包含多个消费者实例。当我们创建一个消费者组时，我们需要确保该组名称的唯一性，否则将会出现消费者实例互相抢占消息的问题。

```java
// 正确的示例
DefaultMQPushConsumer consumerA = new DefaultMQPushConsumer("consumerA", false);
DefaultMQPushConsumer consumerB = new DefaultMQPushConsumer("consumerB", false);

// 错误的示例
DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("consumerA", false);
```

## 5. 误用顺序消息

`RocketMQ`支持顺序消息，可以确保相同`MessageQueue`上的消息顺序消费。但是，在使用顺序消息时，我们需要保证消息发送与消费的时序完全一致。否则，将会导致消费者收到的消息顺序与生产者发送的顺序不一致，从而出现数据错误。

```java
// 正确的示例
messageQueueSelector.select(queueList, message, orderKey);

// 错误的示例
messageQueueSelector.select(queueList, message);
```

## 结语

以上是本文介绍的RocketMQ消息中间件的避坑指南。希望能对大家在使用`RocketMQ`时避免一些坑有所帮助。记住，正确使用`RocketMQ`，才能发挥其优异的性能和可靠性！