---
title: "Consumer (Reactive)"
linkTitle: "Consumer (Reactive)"
weight: 30
description: >
  Spring Cloud Function 之 Consumer (Reactive)
---

> 内容摘录自官方文档 [Consumer (Reactive)](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_consumer_reactive) 一节

Reactive `Consumer` 有点特别，因为它的返回类型是空的，没有给框架留下可以订阅的引用。你很可能不需要写 `Consumer<Flux<?>`，而是把它写成 `Function<Flux<?>, Mono<Void>>`，调用 `then` operator 作为你流中的最后一个 operator。

比如说。

```java
public Function<Flux<?>, Mono<Void>>consumer() {
	return flux -> flux.map(..).filter(..).then();
}
```

但如果你确实需要写一个显式的 `Consumer<Flux<?>`，记得要订阅传入的 `Flux`。

另外，请记住，当混合反应式和命令式函数时，同样的规则也适用于函数组合。Spring Cloud Function 确实支持将反应式函数与命令式函数进行组合，但是你必须注意到某些限制。例如，假设你将反应式函数与命令式消费者进行组合。这种组合的结果是一个反应式 `Consumer`。然而，正如本节前面所讨论的那样，没有办法订阅这样的消费者，所以这个限制只能通过使你的消费者成为反应式并手动订阅（如前所述），或者将你的函数改为命令式来解决。



### 轮询配置属性
以下属性由 Spring Cloud Stream 公开（尽管自3.2版本起已被废弃），并以 `spring.cloud.stream.poller` 为前缀。

- fixedDelay

  默认轮询器的固定延迟，单位是毫秒。

  默认值：1000L。

- maxMessagesPerPoll
  
  默认轮询器的每个轮询事件的最大信息。

  默认值：1L。

- cron

  Cron触发器的Cron表达式值。

  默认值：无。

- initialDelay

  定期触发器的初始延迟。

  默认值：0。

- timeUnit

  应用于延迟值的时间单位。

  默认值。MILLISECONDS。

  例如 `--spring.cloud.stream.poller.fixed-delay=2000` 设置轮询器的间隔为每两秒轮询一次。



{{% alert title="Warning" color="warning" %}}
从3.2版本开始，这些 poller 属性被弃用，转而使用 Spring Boot 自动配置 Spring Integration 的类似配置属性。参见`org.springframework.boot.autoconfigure.integration.IntegrationProperties.Poller` 以了解更多信息。
{{% /alert %}}



### 每个绑定的轮询配置

上一节展示了如何配置一个将应用于所有绑定的默认轮询器。虽然它很适合 spring-cloud-stream 设计的微服务模型，即每个微服务代表一个组件（例如，供应商），因此默认轮询器配置就足够了，但在一些边缘情况下，你可能有几个组件需要不同的轮询配置。

在这种情况下，请使用按绑定方式配置轮询器。在这种情况下，你可以使用 `spring.cloud.stream.bindings.supply-out-0.producer.poller...`前缀为这种绑定配置轮询器（例如，`spring.cloud.bindings.supply-out-0.producer.poller.fixed-delay=2000`）。
