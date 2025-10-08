---
title: "Spring Cloud Function支持概述"
linkTitle: "概述"
weight: 10
description: >
  Spring Cloud Stream 的 Spring Cloud Function 支持概述
---

> 内容摘录自官方文档 [Overview](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_overview) 一节

自 Spring Cloud Stream v2.1以来，定义流处理程序和源的另一个选择是使用 Spring Cloud Function 的内置支持，在那里它们可以被表达为 `java.util.function.[Supplier/Function/Consumer]` 类型的 bean。

要指定哪个函数式 bean 绑定到绑定所暴露的外部目标，你必须提供 `spring.cloud.function.define` 属性。

如果你只有 `java.util.function.[Supplier/Function/Consumer]` 类型的单个Bean，你可以跳过 `spring.cloud.function.define` 属性，因为这种函数 Bean 会被自动发现。然而，使用这种属性被认为是最好的做法，以避免任何混淆。有些时候，这种自动发现可能会妨碍工作，因为 `java.util.function.[Supplier/Function/Consumer]` 类型的单体 Bean 可能有处理消息以外的目的，但由于是单体，它被自动发现并自动绑定。对于这些罕见的情况，你可以通过提供 `spring.cloud.stream.function.autodetect` 属性来禁用自动发现，其值设置为 false。

下面是应用程序将消息处理程序暴露为 `java.util.function.Function` 的例子，通过充当数据的消费者和生产者，有效地支持直通语义。

```java
@SpringBootApplication
public class MyFunctionBootApp {

	public static void main(String[] args) {
		SpringApplication.run(MyFunctionBootApp.class);
	}

	@Bean
	public Function<String, String> toUpperCase() {
		return s -> s.toUpperCase();
	}
}
```

在前面的例子中，我们定义了一个名为 toUpperCase 的 `java.util.function.Function` 类型的 bean，作为消息处理程序，其 "input" 和 "output" 必须绑定到所提供的目标绑定器所暴露的外部目的地。默认情况下，'inpu' 和 'output' binding name 将是 `toUpperCase-in-0` 和 `toUpperCase-0`。请参阅函数绑定名称部分，了解用于建立绑定名称的命名规则的细节。

下面是支持其他语义的简单功能应用的例子。

下面是一个以 `java.util.function.Supplier` 形式暴露的 source 语义的例子:

```java
@SpringBootApplication
public static class SourceFromSupplier {

	@Bean
	public Supplier<Date> date() {
		return () -> new Date(12345L);
	}
}
```

下面是一个以 `java.util.function.Consumer` 形式暴露的 sink 语义的例子 

```java
@SpringBootApplication
public static class SinkFromConsumer {

	@Bean
	public Consumer<String> sink() {
		return System.out::println;
	}
}
```


```mindmap
root
  topic1
    subtopic
  topic2
    subtopic
```


