---
title: "生产和消费消息"
linkTitle: "生产和消费消息"
weight: 50
description: >
  Spring Cloud Stream 生产和消费消息
---

> 内容摘录自官方文档 [Producing and Consuming Messages](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_producing_and_consuming_messages) 一节

你可以通过简单地编写函数并将其作为 "@Bean " 公开，来编写 Spring Cloud Stream 应用程序。你也可以使用基于 Spring Integration 注解的配置或基于 Spring Cloud Stream 注解的配置，不过从 spring-cloud-stream 3.x 开始，我们建议使用函数实现。

