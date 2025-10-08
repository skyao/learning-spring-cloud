---
title: "reactive 函数支持"
linkTitle: "reactive 函数支持"
weight: 50
description: >
  Spring Cloud Function 之 reactive 函数支持
---

> 内容摘录自官方文档 [Reactive Functions support](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_reactive_functions_support) 一节

由于 Spring Cloud Function 是建立在 Project Reactor 之上的，所以在实现 `Supplier`、`Function` 或 `Consumer` 时，你不需要做太多的事情就能从反应式编程模型中获益。

比如说:

```java
@SpringBootApplication
public static class SinkFromConsumer {

	@Bean
	public Function<Flux<String>, Flux<String>> reactiveUpperCase() {
		return flux -> flux.map(val -> val.toUpperCase());
	}
}
```

