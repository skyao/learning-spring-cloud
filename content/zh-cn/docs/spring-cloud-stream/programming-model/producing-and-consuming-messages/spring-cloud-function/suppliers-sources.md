---
title: "Suppliers (Sources)"
linkTitle: "Suppliers (Sources)"
weight: 20
description: >
  Spring Cloud Function 之 Suppliers (Sources)
---

> 内容摘录自官方文档 [Suppliers (Sources)](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_suppliers_sources) 一节

当涉及到它们的调用如何被触发时，`Function` 和 `Consumer` 是非常直接的。它们是根据发送到它们所绑定的目的地的数据（事件）来触发的。换句话说，它们是典型的事件驱动型组件。

然而，当谈到触发时，`Supplier` 属于自己的类别。因为根据定义，它是数据的源头（the origin），它不订阅任何绑定的目的地，因此，必须由其他机制来触发。还有一个 `Supplier`  实现的问题，它可以是命令式的（imperative），也可以是反应性的（reactive），它直接关系到这些 Supplier 的触发。

请看下面的例子：

```java
@SpringBootApplication
public static class SupplierConfiguration {

	@Bean
	public Supplier<String> stringSupplier() {
		return () -> "Hello from Supplier";
	}
}
```

前面的 `Supplier` Bean 在调用其 get() 方法时产生一个字符串。然而，谁会调用这个方法，多久调用一次？框架提供了一个默认的轮询机制（回答了 "谁？"的问题），它将触发 Supplier v的调用，默认情况下，它每隔一秒就会调用一次（回答了 "多久一次？"的问题）。换句话说，上述配置每秒钟产生一条消息，每条消息都被发送到一个由 binder 暴露的 `output` 目的地。要了解如何定制轮询机制，请看轮询配置属性部分。

考虑一个不同的例子:

```java
@SpringBootApplication
public static class SupplierConfiguration {

    @Bean
    public Supplier<Flux<String>> stringSupplier() {
        return () -> Flux.fromStream(Stream.generate(new Supplier<String>() {
            @Override
            public String get() {
                try {
                    Thread.sleep(1000);
                    return "Hello from Supplier";
                } catch (Exception e) {
                    // ignore
                }
            }
        })).subscribeOn(Schedulers.elastic()).share();
    }
}
```

上面的 `Supplier` Bean采用了反应式编程风格。通常情况下，与命令式 Supplier 不同，它应该只被触发一次，因为调用它的 get() 方法会产生（供应）连续的消息流，而不是单个消息。

该框架认识到了编程风格的不同，并保证这样的 Supplier 只被触发一次。

然而，想象一下这样的用例：你想轮询一些数据源并返回代表结果集的有限数据流。反应式编程风格是这种 Supplier 的完美机制。然而，考虑到产生的数据流的有限性，这样的 Supplier 仍然需要被定期调用。

考虑一下下面的例子，它通过产生一个有限的数据流来模拟这种用例：

```java
@SpringBootApplication
public static class SupplierConfiguration {

	@PollableBean
	public Supplier<Flux<String>> stringSupplier() {
		return () -> Flux.just("hello", "bye");
	}
}
```

Bean本身被注解了 `PollableBean` 注解（@Bean的子集），从而向框架发出信号，尽管这样一个 Supplier 的实现是反应式的，但它仍然需要被轮询。

### Supplier & 线程

正如你现在所了解的，与 Function 和 Consumer 不同，Function 和 Consumer 是由事件触发的（它们有输入数据），Supplier 没有任何输入，因此由不同的机制-- poller 触发，它可能有一个不可预知的线程机制。虽然大多数时候线程机制的细节与函数的下游执行无关，但在某些情况下可能会出现问题，特别是对于那些可能对线程亲和力有一定期望的集成框架。例如，Spring Cloud Sleuth 就依赖于存储在 thread local 中的追踪数据。对于这些情况，我们有另一种通过 StreamBridge 的机制，用户可以对线程机制有更多的控制。你可以在发送任意数据到输出端（例如Foreign事件驱动源）一节中获得更多细节。
