---
title: "Binder SPI"
linkTitle: "Binder SPI"
weight: 30
description: >
  
---

> 内容摘录自官方文档 [Binder SPI](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#spring-cloud-stream-overview-binder-api) 一节

Binder SPI由一些接口、开箱即用的实用类和发现策略组成，提供了一个连接到外部中间件的可插拔机制。

SPI的关键点是 Binder 接口，它是一种连接输入和输出到外部中间件的策略。下面的列表显示了Binder接口的定义:

```java
public interface Binder<T, C extends ConsumerProperties, P extends ProducerProperties> {
    Binding<T> bindConsumer(String bindingName, String group, T inboundBindTarget, C consumerProperties);

    Binding<T> bindProducer(String bindingName, T outboundBindTarget, P producerProperties);
}
```

该接口是参数化的，提供一些扩展点:

- 输入和输出的绑定目标。

- 可扩展的消费者和生产者属性，允许特定的 Binder 实现添加补充属性，可以以类型安全的方式支持。

典型的 binder 实现由以下部分组成。

- 一个实现了 `Binder` 接口的类。

- 一个 Spring `@Configuration` 类，它与中间件连接基础设施一起创建了一个 Binder 类型的bean。

- 一个在 classpath 上找到的 `META-INF/spring.binders` 文件，包含一个或多个 `Binder` 定义，如下例所示。

```properties
kafka:\
org.springframework.cloud.stream.binder.kafka.config.KafkaBinderConfiguration
```

> 正如前面提到的，binder 的抽象性也是框架的扩展点之一。因此，如果你在前面的列表中找不到合适的 binder，你可以在 Spring Cloud Stream 的基础上实现你自己的 binder。在《如何从头开始创建 Spring Cloud Stream binder》一文中，一位社区成员通过一个例子详细记录了实现自定义 binder 的一系列必要步骤。这些步骤在实现自定义binder部分也有强调。





