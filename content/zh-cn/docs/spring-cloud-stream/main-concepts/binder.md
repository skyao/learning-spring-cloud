---
title: "binder抽象"
linkTitle: "binder抽象"
weight: 20
description: >
  Spring Cloud Stream 应用模型中的binder抽象
---

## 官方文档的描述

> 内容摘录自官方文档 [The Binder Abstraction](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#spring-cloud-stream-overview-binder-abstraction) 一节 

Spring Cloud Stream 为 Kafka 和 Rabbit MQ 提供了 Binder 的实现。该框架还包括一个测试 binder ，用于将你的应用程序作为 spring-cloud-stream 应用程序进行集成测试。

Binder 抽象也是该框架的扩展点之一，这意味着你可以在 Spring Cloud Stream 之上实现自己的 binder。

Spring Cloud Stream 使用 Spring Boot 进行配置，Binder 抽象使 Spring Cloud Stream 应用程序能够灵活地连接到中间件。例如，部署者可以在运行时动态地选择外部目的地（如Kafka topic 或RabbitMQ exchange）与消息处理程序的输入和输出（如函数的输入参数及其返回参数）之间的映射。这种配置可以通过外部配置属性和 Spring Boot 支持的任何形式提供（包括应用程序参数、环境变量和application.yml 或 application.properties 文件）。在介绍Spring Cloud Stream部分的 sink 示例中，将 `spring.cloud.stream.bindings.input.destination` 应用属性设置为 `raw-sensor-data` 会导致它从 `raw-sensor-data` Kafka topic 或从绑定到 `raw-sensor-data` RabbitMQ exchange 的队列中读取。

Spring Cloud Stream 会自动检测并使用在 classpath上 找到的 binder。你可以在相同的代码中使用不同类型的中间件。要做到这一点，在构建时包括一个不同的 binder。对于更复杂的用例，你也可以将多个 binder 与你的应用程序打包，让它在运行时选择 binder（甚至是是否为不同的 binding 使用不同的 binder）。
