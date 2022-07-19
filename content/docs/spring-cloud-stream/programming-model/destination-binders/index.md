---
title: "Destination Binders"
linkTitle: "Destination Binders"
weight: 10
description: >
  Spring Cloud Stream 编程模型之 Destination Binders
---

## 官方文档的描述

> 内容摘录自官方文档 [Destination Binders](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_destination_binders) 一节

目的地绑定器（Destination Binders）是 Spring Cloud Stream 的扩展组件，负责提供必要的配置和实现，以促进与外部消息系统的集成。这种集成负责生产者和消费者之间的连接、委托和消息的路由、数据类型转换、用户代码的调用等。

Binder 处理了很多本来要落在开发者身上的责任。然而，为了达到这个目的，Binder 仍然需要一些帮助，其形式是来自用户的极简而必要的指令集，这些指令通常以某种类型的 binding 配置的形式出现。

虽然讨论所有可用的 binder 和 binding 配置选项超出了本节的范围（本手册的其他部分将广泛涉及这些选项），但 binding 作为一个概念，确实需要特别注意。下一节将详细讨论它。