---
title: "编程模型"
linkTitle: "编程模型"
weight: 30
description: >
  Spring Cloud Stream 的主要概念和应用模型
---

> 内容摘录自官方文档 [Programming Model](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_programming_model) 一节

为了理解这个编程模型，你应该熟悉以下核心概念：

- **目的地绑定器（Destination Binders）**：负责提供与外部消息系统的集成的组件。
- **绑定（Bindings）**：外部消息系统和应用程序之间的桥梁，提供消息生产者和消费者（由目的地绑定器创建）。
- **消息（Message）**：生产者和消费者使用的典型数据结构，用于与目的地绑定器通信（从而通过外部消息系统与其他应用程序通信）。

![](images/SCSt-overview.png)