---
title: "Binding"
linkTitle: "Binding"
weight: 20
description: >
  Spring Cloud Stream 编程模型之 Binding
---

## 官方文档的描述

> 内容摘录自官方文档 [Bindings](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_bindings) 一节

如前所述，binding 在外部消息系统（如队列、主题等）和应用程序提供的生产者和消费者之间提供了一座桥梁。

下面的例子显示了一个完全配置和运行的 Spring Cloud Stream 应用程序，该应用程序接收作为字符串类型的消息的有效载荷（见内容类型协商部分），将其记录到控制台，并在将其转换为大写字母后向下发送。

```java
@SpringBootApplication
public class SampleApplication {

	public static void main(String[] args) {
		SpringApplication.run(SampleApplication.class, args);
	}

	@Bean
	public Function<String, String> uppercase() {
	    return value -> {
	        System.out.println("Received: " + value);
	        return value.toUpperCase();
	    };
	}
}
```

上面的例子看起来和任何 spring-boot 应用程序没有什么不同。它定义了一个 Function 类型的 bean，这就是它。那么，它是如何成为 spring-cloud-stream 应用程序的呢？它之所以成为 spring-cloud-stream 应用程序，仅仅是因为在 classpath 上存在 spring-cloud-stream 和 binder 的依赖关系以及自动配置类，有效地将启动应用程序的上下文设置为 spring-cloud-stream 应用程序。在这种情况下，Supplier、Function 或 Consumer 类型的 bean 被视为事实上的消息处理程序，触发绑定到所提供的绑定器所暴露的目的地，并遵循一定的命名惯例和规则以避免额外的配置。

### 绑定和绑定名称

绑定 (binding) 是一个抽象概念，代表了绑定器(binder)和用户代码所暴露的源和目标之间的桥梁，这个抽象概念有一个名字，虽然我们尽力限制运行 spring-cloud-stream 应用程序所需的配置，但在需要对每个绑定 (binding) 进行额外配置的情况下，了解这些名字是必要的。

在本手册中，你会看到一些配置属性的例子，如 `spring.cloud.stream.bindings.input.destination=myQueue`。这个属性名称中的 `input` 段就是我们所说的绑定名称（binding name），它可以通过几种机制衍生出来。下面的小节将描述 spring-cloud-stream 用于控制绑定名称（binding name）的命名惯例和配置元素。

#### 函数式绑定名称

与 spring-cloud-stream 以前的版本中使用的基于注解的支持（legacy）所要求的显式命名不同，函数式编程模型在涉及到绑定名称时默认为简单的约定，从而大大简化了应用配置。让我们来看看第一个例子。

```java
@SpringBootApplication
public class SampleApplication {

	@Bean
	public Function<String, String> uppercase() {
	    return value -> value.toUpperCase();
	}
}
```

在前面的例子中，我们有一个应用程序，它有一个单一的函数作为消息处理程序。作为 `Function`，它有一个输入和输出。用来命名输入和输出绑定的命名规则如下:

- input - `<functionName> + -in- + <index>`
- output - `<functionName> + -out- + <index>`

`in` 和 `out` 对应的是 binding 的类型（如输入或输出）。index是输入或输出绑定的索引。对于典型的单一输入/输出函数，它总是0，所以它只与具有多个输入和输出参数的函数有关。

因此，如果你想把这个函数的输入映射到一个叫做 "my-topic" 的远程目标（例如，主题、队列等），你可以通过以下属性来实现:

```properties
--spring.cloud.stream.bindings.uppercase-in-0.destination=my-topic
```

请注意 `uppercase-in-0` 是如何作为属性名称中的一个段的。同样，`uppercase-out-0`也是如此。

#### 描述性的绑定名称

有些时候，为了提高可读性，你可能想给你的 binding 一个更具描述性的名字（比如 "账户"，"订单" 等）。另一种方式是你可以将隐式绑定名称映射到显式绑定名称。你可以用 `spring.cloud.stream.function.bindings.<binding-name>` 属性来做。该属性还为依赖基于自定义接口的绑定的现有应用程序提供了一个迁移路径，这些绑定需要显式名称。

例如:

```properties
--spring.cloud.stream.function.bindings.uppercase-in-0=input
```

在前面的例子中，你把 `uppercase-in-0` binding name 映射并有效地重命名为 `input`。现在，所有的配置属性都可以参考 `input`的binding name（例如， `--spring.cloud.bindings.input.destination=my-topic`）。

虽然描述性的 binding name 可能会增强配置的可读性，但它们也会通过将隐式绑定名称映射到显式绑定名称而产生另一种程度的误导。而且，由于所有后续的配置属性都将使用显式绑定名称，你必须始终参考这个 "bindings" 属性，以确定它实际上对应的是哪个功能。我们认为，对于大多数情况（函数组合除外），这可能是一种矫枉过正的做法，所以，我们建议完全避免使用它，尤其是不使用它可以在 binding 目的地和 binding name 之间提供一条清晰的路径，比如 `spring.cloud.stream.bindings.uppercase-in-0.destination=sample-topic`，在这里你可以清楚地将 `uppercase` 函数的输入与 `sample-topic` 的目的地相关联。

关于属性和其他配置选项的更多信息，请参见配置选项部分。

#### 显式绑定的创建

在上一节中，我们解释了如何通过你的应用程序提供的 Function, Supplier 或 Consumer 驱动来隐式地创建 binding。然而，有时你可能需要显式地创建绑定，而绑定并不与任何函数挂钩。这通常是为了支持与其他框架（如Spring integration 框架）的集成，在那里你可能需要直接访问底层的 MessageChannel。

Spring Cloud Stream 允许你通过 `spring.cloud.stream.input-bindings` 和 `spring.cloud.output-bindings` 属性明确定义输入和输出绑定。注意到属性名称中的复数，允许你通过简单地使用;作为分隔符来定义多个绑定。请看下面的测试案例作为一个例子:

```java
@Test
public void testExplicitBindings() {
	try (ConfigurableApplicationContext context = new SpringApplicationBuilder(
		TestChannelBinderConfiguration.getCompleteConfiguration(EmptyConfiguration.class))
				.web(WebApplicationType.NONE)
				.run("--spring.jmx.enabled=false",
					"--spring.cloud.stream.input-bindings=fooin;barin",
					"--spring.cloud.stream.output-bindings=fooout;barout")) {

	assertThat(context.getBean("fooin-in-0", MessageChannel.class)).isNotNull();
	assertThat(context.getBean("barin-in-0", MessageChannel.class)).isNotNull();
	assertThat(context.getBean("fooout-out-0", MessageChannel.class)).isNotNull();
	assertThat(context.getBean("barout-out-0", MessageChannel.class)).isNotNull();
	}
}

@EnableAutoConfiguration
@Configuration
public static class EmptyConfiguration {
}
```

正如你所看到的，我们声明了两个 input 绑定和两个 output 绑定，而我们的配置中没有定义任何函数，但我们还是能够成功地创建这些绑定并访问它们相应的通道。

其余适用于隐式绑定的绑定规则也适用于此（例如，你可以看到 `fooin` 变成了 `fooin-in-0` 绑定/通道等）。