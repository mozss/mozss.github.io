---
title: 初识dubbo
tags:
  - dubbo
categories:
  - it
  - it-java
abbrlink: 16e5a472
date: 2020-04-02 16:06:20
---

[![jTd98H.jpg](https://s1.ax1x.com/2022/07/19/jTd98H.jpg)](https://imgtu.com/i/jTd98H)

<!--more-->

- 当服务越来越多, URL配置管理困难, F5硬件负载均衡器的单点压力也越来越大
- 进一步发展, 服务间依赖关系变得错综复杂, 甚至分不清那个应用在哪个应用之前启动
- 服务调用量也越来越大, 服务的容量问题就暴露出来, 这个服务需要多少台机器?


[![jT8gw4.png](https://s1.ax1x.com/2022/07/19/jT8gw4.png)](https://imgtu.com/i/jT8gw4)

节点角色说明:

* Provider: 暴露服务的服务提供方
* Consumer: 调用远程服务的服务消费方
* Registry: 服务注册与发现的注册中心
* Monitor: 统计服务的调用次调和调用时间的监控中心
* Container: 服务运行容器

调用关系说明:
0. 服务容器负责启动, 加载, 运行服务提供者
1. 服务提供者在启动时, 向注册中心注册自己提供的服务
2. 服务消费者在启动时, 向注册中心订阅自己所需的服务
3. 注册中心返回服务提供者地址列表给消费者, 如果有变更, 注册中心将基于长连接推送变更数据给消费者
4. 服务消费者, 从提供者地址列表中, 基于软负载均衡算法, 选一台提供者进行调用, 如果调用失败, 再选另一台调用
5. 服务消费者和提供者, 在内存中累计调用次数和调用时间, 定时每分钟发送一次统计数据到监控中心


Dubbo框架设计一个划分10个层:
1. 服务接口层(Service): 该层是与实际业务逻辑相关的, 根据服务提供方和服务消费方的业务设计对应的接口和实现

2. 配置层(Config):对外配置接口, 以ServiceConfig和ReferenceConfig为中心, 可以直接new配置类, 也可用通过Spring解析配置生成配置类

3. 服务代理层(Proxy): 服务接口透明代理,生成服务的客户端Stub和服务端Skeleton, 以ServiceProxy为中心, 扩展接口为ProxyFactory

4. 服务注册层(Registry): 封装服务地址的注册与发现, 以服务URL为中心, 扩展接口为RegistryFactory, Registry和RegistryService. 可能没有服务注册中心, 此时服务提供方直接暴露服务

5. 集群层(Cluster): 封装多个提供者的路及负载均衡, 并桥接注册中心, 以Invoker为中心, 扩展接口为Cluster, Directory, Router和LoadBalance. 将多个服务提供方组合为一个服务提供方, 实现对象服务消费方法来透明, 只需要与一个服务提供方进行交互.

6. 监控层(Monitor): PRC调用次数和调用时间监控, 以Statistics为中心,扩展接口为MonitorFactory,Monitor和MonitorService.

7. 远程调用层(Protocol): 封装RPC调用, 以Invocation和Result为中心, 扩展接口为Protocol, Invoker和Exporter. Protocol是服务域, 它是Invoker暴露和引用的主功能入口, 它负责Invoker的生命周期管理. Invoker是实体域, 它是Dubbo的核心模型, 其他模型都是向它靠拢, 或转换成它, 它代表一个可执行体, 可向它发起invoke调用, 它有可能是一个本地的实现, 也可能是一个远程的实现, 也可能一个集群实现.

8. 信息交换层(Exchange): 封装请求响应模式, 同步转异步, 以Request和Response为中心, 扩展接口为Exchanger, ExchangeChannel, ExchangeClient和ExchangeServer.

9. 网络传输层(Transport): 抽象mina和netty为统一的接口, 以Message为中心, 扩展接口为Channel, Transporter, Client, Server和Codec.

10. 数据序列化层(Serialize): 可复用的一些工具, 扩展接口为Serialization, ObjectInput,ObjectOutput和ThreadPool.

Dubbo对于服务提供方和服务消费方, 从框架的10层中分别提供了各自需要关心和扩展的接口, 构建整个服务生态(以服务提供方和服务消费方本身就是一个以服务为中心的)






























