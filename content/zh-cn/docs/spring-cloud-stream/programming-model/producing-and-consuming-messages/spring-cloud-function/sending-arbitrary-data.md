---
title: "将任意的数据发送到输出端"
linkTitle: "将任意的数据发送到输出端"
weight: 40
description: >
  Spring Cloud Function 之将任意的数据发送到输出端
---

> 内容摘录自官方文档 [Sending arbitrary data to an output (e.g. Foreign event-driven sources)](https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/spring-cloud-stream.html#_sending_arbitrary_data_to_an_output_e_g_foreign_event_driven_sources) 一节

将任意的数据发送到输出端（如 Foreign 的事件驱动源）。

有些情况下，实际的数据源可能来自于不是绑定器的外部（Foreign）系统。例如，数据源可能是一个经典的 REST 端点。我们如何将这样的 source 与 spring-cloud-stream 使用的函数机制连接起来？

Spring Cloud Stream 提供了两种机制，让我们来详细了解一下。

在这里，对于这两个样本，我们将使用一个标准的MVC端点方法，名为 `delegateToSupplier`，与根 Web 上下文绑定，通过StreamBridge 机制将传入的请求委托给流。

```java
@SpringBootApplication
@Controller
public class WebSourceApplication {

	public static void main(String[] args) {
		SpringApplication.run(WebSourceApplication.class, "--spring.cloud.stream.source=toStream");
	}

	@Autowired
	private StreamBridge streamBridge;

	@RequestMapping
	@ResponseStatus(HttpStatus.ACCEPTED)
	public void delegateToSupplier(@RequestBody String body) {
		System.out.println("Sending " + body);
		streamBridge.send("toStream-out-0", body);
	}
}
```

在这里，我们自动装箱(Autowire)了一个 `StreamBridge` Bean，它允许我们将数据发送到 output binding，有效地将非流应用程序与 `spring-cloud-stream` 连接起来。请注意，前面的例子没有定义任何 source 函数（例如，Supplier Bean），因此框架没有触发器来提前创建源绑定，这在配置包含函数 Bean 的情况下是很典型的。这很好，因为 StreamBridge 会在第一次调用 `send(..)` 操作时为非现有的绑定启动创建输出绑定（以及必要时的目的地自动配置），并将其缓存起来供后续重用（更多细节见 StreamBridge 和动态目的地）。

然而，如果你想在初始化（启动）时预先创建一个输出绑定，你可以从 `spring.cloud.stream.source` 属性中获益，在那里你可以声明你的源的名称。所提供的名称将被用作触发器来创建一个源绑定。所以在前面的例子中，输出绑定的名称将是 `toStream-out-0`，这与函数使用的绑定命名惯例一致（见绑定和绑定名称）。你可以使用;来表示多个源（多个输出绑定）（例如，`--spring.cloud.stream.source=foo;bar`)

另外，注意 `streamBridge.send(..)` 方法需要一个对象作为数据。这意味着你可以向它发送 POJO 或消息，它在发送输出时将经过同样的程序，就像它来自任何函数或供应商一样，提供与函数相同的一致性。这意味着输出类型的转换、分区等都会被尊重，就像它是由函数产生的输出一样。

### StreamBridge和动态目的地

`StreamBridge` 也可以用于不知道输出目的地的情况，类似于 `Routing FROM Consumer` 部分中描述的使用情况。

让我们看一下这个例子:

```java
@SpringBootApplication
@Controller
public class WebSourceApplication {

	public static void main(String[] args) {
		SpringApplication.run(WebSourceApplication.class, args);
	}

	@Autowired
	private StreamBridge streamBridge;

	@RequestMapping
	@ResponseStatus(HttpStatus.ACCEPTED)
	public void delegateToSupplier(@RequestBody String body) {
		System.out.println("Sending " + body);
		streamBridge.send("myDestination", body);
	}
}
```

正如你所看到的，上面的例子与前面的例子非常相似，除了通过 `spring.cloud.stream.source` 属性提供的显式绑定指令（没有提供）。在这里，我们正在向 `myDestination` 名称发送数据，该名称并不作为绑定存在。因此，这样的名字将被视为动态目的地，如`Routing FROM Consumer` 章节所述。

在前面的例子中，我们使用 `ApplicationRunner` 作为一个外来源来输入数据流。

一个更实际的例子，foreign 源是REST端点。

```java
@SpringBootApplication
@Controller
public class WebSourceApplication {

	public static void main(String[] args) {
		SpringApplication.run(WebSourceApplication.class);
	}

	@Autowired
	private StreamBridge streamBridge;

	@RequestMapping
	@ResponseStatus(HttpStatus.ACCEPTED)
	public void delegateToSupplier(@RequestBody String body) {
		streamBridge.send("myBinding", body);
	}
}
```

正如你所看到的，在 `delegateToSupplier` 方法中，我们使用 `StreamBridge` 来发送数据到 `myBinding` 绑定。这里你也受益于 `StreamBridge` 的动态特性，如果 `myBinding` 不存在，它将被自动创建并缓存，否则将使用现有的绑定。
