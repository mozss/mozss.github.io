---
title: 初识sentinel
tags:
  - sentinel
categories:
  - it
  - it-java
abbrlink: 16e5a472
date: 2020-04-02 16:06:20
---

[![jTdpPe.jpg](https://s1.ax1x.com/2022/07/19/jTdpPe.jpg)](https://imgtu.com/i/jTdpPe)

<!--more-->

## sentinel是什么?

sentinel是面向分布式服务架构的流量控制组件, 主要以流量控制为切入点, 从流量控制, 熔断降级, 系统自适应保护等多个维度来帮助您保障微服务的稳定性.

## sentinel特性

- 丰富的应用场景: 秒杀(突发流量控制在系统容量可以承受的范围), 消峰填谷, 集群流量控制, 实时熔断下游不可用应用等
- 完备的实时监控: 可以在控制台中看到接入应用的单机器秒级别的数据
- 广泛的开源生态: sentinel提供开箱即用的与其他开源框架/库的整合模块, 例如与springcloud,apache dubbo, grpc, quarkus的整合.
- 完善的spi扩展机制: 可以通过实现扩展接口来快速定制逻辑. 例如: 定制规则管理, 适配动态数据源等

## sentinel主要特性

[![jTqh1e.png](https://s1.ax1x.com/2022/07/19/jTqh1e.png)](https://imgtu.com/i/jTqh1e)

## sentinel组成部分

- 核心库sentinel-java: 不依赖于任何框架/库, 能够运行在所有java运行时环境, 同时对dubbo/spring cloud等框架也有较好的支持.
- 控制台sentinel-dashboard: 基于springboot开发, 打包后直接运行, 不需要额外的tomcat等应用容器.




















