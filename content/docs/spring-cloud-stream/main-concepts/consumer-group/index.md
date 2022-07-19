---
title: "consumer group"
linkTitle: "consumer group"
weight: 40
description: >
  Spring Cloud Stream 应用模型中的consumer group
---

## 官方文档的描述

> 内容摘录自官方文档 [Consumer Groups](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#consumer-groups) 一节

虽然发布-订阅模型使得通过共享主题连接应用程序变得容易，但通过创建特定应用程序的多个实例来扩大规模的能力同样重要。当这样做时，应用程序的不同实例被置于竞争的消费者关系中，其中只有一个实例被期望处理一个给定的消息。

Spring Cloud Stream 通过 consumer group （消费者组）的概念来模拟这种行为。(Spring Cloud Stream consumer group 与Kafka consumer group 相似，并受其启发。) 每个消费者 binding 可以使用 `spring.cloud.stream.bindings.<bindingName>.group` 属性来指定一个组名。对于下图所示的消费者，这个属性将被设置为 `spring.cloud.stream.bindings.<bindingName>.group=hdfsWrite` 或 `spring.cloud.stream.bindings.<bindingName>.group=average`。

![SCSt-with-binder](images/SCSt-groups.png)

所有订阅给定目的地的组都会收到一份已发布数据的副本，但每个组中只有一个成员收到来自该目的地的给定消息。默认情况下，当没有指定组时，Spring Cloud Stream 会将应用程序分配给一个匿名的、独立的单成员 consumer group，该组与所有其他 consumer group 都是发布-订阅关系。

### 消费者类型

支持两种类型的消费者:

- 消息驱动（有时称为异步）

- 轮询（有时称为同步）。

在2.0版本之前，只支持异步的消费者。只要有消息，并且有线程可以处理，消息就会被传递。

当你想控制消息的处理速度时，你可能想使用一个同步消费者。

### 持久性

与 Spring Cloud Stream 的 opinionated (翻译为 有主见的？) 应用模型一致，消费者组的订阅是持久的。也就是说，binder 的实现可以确保组的订阅是持久的，而且一旦为一个组创建了至少一个订阅，该组就会收到消息，即使这些消息是在该组的所有应用都停止时发送的。

> 匿名订阅在本质上是不可持久的。对于一些 binder 的实现（如RabbitMQ），可以有非持久性的组订阅。

一般来说，当把应用程序绑定到一个特定的目的地时，最好总是指定 consumer group。当扩展 Spring Cloud Stream 应用程序时，你必须为其每个输入 binding 指定 consumer group。这样做可以防止应用程序的实例收到重复的消息（除非需要这种行为，这是不正常的）。
