---
title: "分区"
linkTitle: "分区"
weight: 50
description: >
  Spring Cloud Stream 应用模型中的分区
---

## 官方文档的描述

> 内容摘录自官方文档 [Partitioning Support](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#partitioning) 一节

Spring Cloud Stream 提供了对特定应用程序的多个实例之间的数据分区的支持。在分区方案中，物理通信介质（如 broker topic）被视为被结构化为多个分区。一个或多个生产者应用实例向多个消费者应用实例发送数据，并确保由共同特征识别的数据由同一个消费者实例处理。

Spring Cloud Stream 为以统一方式实现分区处理用例提供了一个通用抽象。因此，无论 broker 本身是自然分区（例如Kafka）还是不分区（例如RabbitMQ），都可以使用分区。!

[SCSt-partitioning](images/SCSt-partitioning.png)

分区是有状态处理中的一个关键概念，在有状态处理中，确保所有相关数据被一起处理是非常关键的（出于性能或一致性的原因）。例如，在时间窗口平均计算的例子中，重要的是来自任何给定传感器的所有测量都由同一个应用实例处理。

> ​	要搭建分区处理方案，必须配置数据生产端和数据消费端。

