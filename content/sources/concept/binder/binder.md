---
title: "binder"
linkTitle: "binder"
weight: 10
date: 2021-03-03
description: >
  spring cloud stream的核心概念-binder定义
---



## Binder 定义

>  A strategy interface used to bind an app interface to a logical name. The name is  intended to identify a logical consumer or producer of messages. This may be a queue, a channel adapter, another message channel, a Spring bean, etc.

一个策略接口，用于将应用接口与逻辑名称绑定。该名称旨在识别消息的逻辑消费者或生产者。这可能是队列、通道适配器、另一个消息通道、Spring Bean，等等。

接口定义：

```java
public interface Binder<T, C extends ConsumerProperties, P extends ProducerProperties> {
  	Binding<T> bindConsumer(String name, String group, T inboundBindTarget,
			C consumerProperties);
  	Binding<T> bindProducer(String name, T outboundBindTarget, P producerProperties);
}
```



name：消息目标的逻辑身份

group：该消费者所属的消费者组（consumer group） - 订阅在同一组的消费者之间共享（如果为 <code>null</code>或空的String，必须被视为一个匿名组，不与任何其他消费者共享订阅）。

inboundBindTarget：绑定为消费者的应用程序接口

outboundBindTarget：绑定为生产者的应用程序接口

consumerProperties：消费者属性

producerProperties： 生产者属性

`**Binding**<**T**>`: 返回设置好的binding

