---
title: "概述"
linkTitle: "概述"
weight: 1
date: 2022-06-27
description: >
  Spring Cloud Stream 项目概述
---



## 介绍

### 资料

- https://spring.io/projects/spring-cloud-stream
- https://github.com/spring-cloud/spring-cloud-stream
- [Spring Cloud Stream Reference Documentation](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/)
- [Spring Cloud Stream (Greenwich) 中文文档](https://www.springcloud.cc/spring-cloud-greenwich.html#_spring_cloud_stream)

Spring Cloud Stream是一个框架，用于构建高度可扩展的事件驱动的微服务的，这些服务与共享消息系统相连。

该框架提供了一个灵活的编程模型，该模型建立在已经建立和熟悉的Spring习语和最佳实践之上，包括对持久化 pub/sub 语义、消费者组和有状态分区的支持。

### 项目背景

内容来自官方文档：

> Spring的数据集成之旅始于[Spring Integration](https://projects.spring.io/spring-integration/)。通过其编程模型，它为开发人员提供了一致的开发经验，以构建可以包含[企业集成模式](http://www.enterpriseintegrationpatterns.com/)以与外部系统（例如数据库，消息代理等）连接的应用程序。
>
> 快进到云时代，微服务已在企业环境中变得突出。[Spring Boot](https://projects.spring.io/spring-boot/)改变了开发人员构建应用程序的方式。借助Spring的编程模型和Spring Boot处理的运行时职责，无缝开发了基于生产，生产级Spring的独立微服务。
>
> 为了将其扩展到数据集成工作负载，Spring Integration和Spring Boot被放到一个新项目中。Spring Cloud Stream出生了。

## 架构

### binder 的实现

Spring Cloud Stream 支持多种 binder 实现，下表包括 GitHub 项目的链接：

- [RabbitMQ](https://github.com/spring-cloud/spring-cloud-stream-binder-rabbit)
- [Apache Kafka](https://github.com/spring-cloud/spring-cloud-stream-binder-kafka)
- [Kafka Streams](https://github.com/spring-cloud/spring-cloud-stream-binder-kafka/tree/master/spring-cloud-stream-binder-kafka-streams)
- [Amazon Kinesis](https://github.com/spring-cloud/spring-cloud-stream-binder-aws-kinesis)
- [Google PubSub *(partner maintained)*](https://github.com/spring-cloud/spring-cloud-gcp/tree/master/spring-cloud-gcp-pubsub-stream-binder)
- [Solace PubSub+ *(partner maintained)*](https://github.com/SolaceProducts/spring-cloud-stream-binder-solace)
- [Azure Event Hubs *(partner maintained)*](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/spring/spring-cloud-azure-stream-binder-eventhubs)
- [Azure Service Bus *(partner maintained)*](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/spring/spring-cloud-azure-stream-binder-servicebus)
- [AWS SQS *(partner maintained)*](https://github.com/idealo/spring-cloud-stream-binder-sqs)
- [AWS SNS *(partner maintained)*](https://github.com/idealo/spring-cloud-stream-binder-sns)
- [Apache RocketMQ *(partner maintained)*](https://github.com/alibaba/spring-cloud-alibaba/wiki/RocketMQ-en)



Spring Cloud Stream的核心构件是：

- **Destination Binders**（目的地绑定器）：负责提供与外部消息系统集成的组件。

- **Destination Bindings**（目的地绑定）：在外部消息系统和最终用户提供的应用程序代码（生产者/消费者）之间建立桥梁。

- **消息**。生产者和消费者用来与Destination Binders（从而通过外部消息系统与其他应用程序）进行通信的典型数据结构。
