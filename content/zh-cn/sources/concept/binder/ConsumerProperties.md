---
title: "ConsumerProperties"
linkTitle: "ConsumerProperties"
weight: 30
date: 2021-03-03
description: >
  spring cloud stream的核心概念-ConsumerProperties
---

## ConsumerProperties 类

ConsumerProperties 类定义通用的 consumer 属性，这些属性对应到 `spring.cloud.stream.bindings.[destinationName].consumer.*`:

```java
public class ConsumerProperties {
	// 此消费者绑定的绑定名称
	private String bindingName;

	// 标志着这个消费者是否需要自动启动。默认值: true
	private boolean autoStartup = true;
  
  // 消费者的并发设置，默认为1
  private int concurrency = 1;
  
  // 消费者是否从分区的生产者那里接收数据。默认：'false'。
  private boolean partitioned;
	......
}
```

TBD：以下参数的意思待查明：

```java
  private String[] requiredGroups = new String[] {};

	private HeaderMode headerMode;

	private boolean useNativeEncoding = false;

	private boolean errorChannelEnabled = false;
```



## 实例相关的参数

### instance count

```java
// 当设置为大于等于零的值时，允许自定义此消费者的实例计数（如果与 spring.cloud.stream.instanceCount 不同）。
// 当设置为负值时，它将默认为 spring.cloud.stream.instanceCount。
// 更多信息请参见该属性。默认值：-1 
// 注意：该设置将覆盖 覆盖 "spring.cloud.stream.instance-count "中的设置。
private int instanceCount = -1;

```



### instance index

```java
// 当设置为大于等于零的值时，允许自定义此消费者的实例索引（如果与 spring.cloud.stream.instanceIndex 不同）。
// 当设置为负值时，它将默认为 spring.cloud.stream.instanceIndex。
// 更多信息请参见该属性。默认值：-1 注意：
// 该设置将覆盖 'spring.cloud.stream.instance-index'中的设置。
private int instanceIndex = -1;

// 当设置时，它将允许定制的消费者为列表中的每个项目产生一个消费者。
// 所有负数的索引将被丢弃。
// 默认值：null 
// 注意：该设置将禁用 instance-index
private List<Integer> instanceIndexList;
```



## 重试相关的参数



### retryableExceptions

```java
// 一个键值为Throwable类名的映射，值为一个布尔值。
// 指定那些将被或不被重试的异常（和子类）。
private Map<Class<? extends Throwable>, Boolean> retryableExceptions = new LinkedHashMap<>();
```

### defaultRetryable

```java
// 监听器抛出的异常如果没有列在 "retryableExceptions" 中，是否可以重试。
private boolean defaultRetryable = true;
```

### maxAttempts

```java
// 在处理失败的情况下，尝试处理消息的次数（包括第一次）。
// 这是一个RetryTemplate配置，由框架提供。默认值：3。
// 设置为1可以禁用重试。
// 在你想完全控制RetryTemplate的情况下，你也可以提供自定义RetryTemplate。只需在你的应用程序配置中把它配置为@Bean。
private int maxAttempts = 3;
```

### backOffInitialInterval

```java
// 重试时的回退初始时间间隔。
// 这是一个RetryTemplate配置，由框架提供。默认值：1000毫秒。
// 如果你想完全控制RetryTemplate，你也可以提供自定义RetryTemplate。只需在你的应用程序配置中把它配置为@Bean。
private int backOffInitialInterval = 1000;
```

### backOffMaxInterval

```java
// 最大的回避间隔。
// 这是一个RetryTemplate配置，由框架提供。默认值：10000毫秒。
// 如果你想完全控制RetryTemplate，你也可以提供自定义RetryTemplate。只需在你的应用程序配置中把它配置为@Bean。
private int backOffMaxInterval = 10000;
```

### backOffMultiplier

```java
// Backoff multiplier.
// 这是一个RetryTemplate配置，由框架提供。默认值：2.0。
// 如果你想完全控制RetryTemplate，你也可以提供自定义RetryTemplate。只需在你的应用程序配置中把它配置为@Bean。
private double backOffMultiplier = 2.0;
```

### retryTemplateName

```java
// 允许你进一步限定对于特定的消费者绑定应该用哪一个RetryTemplate。
private String retryTemplateName;
```



## 批量模式



```java
// 当设置为 "true" 时，如果绑定器支持，发出的消息将有一个有效载荷的列表；
// 当与函数结合使用时，函数可以接收一个带有有效载荷的对象（或 Message）列表，如果有必要的话，有效载荷将被转换。
private boolean batchMode;
```



## 多路(multiplex)



```java
// 当设置为 "true" 时，底层绑定器将在同一输入绑定上自然地复用目的地。
// 例如，在逗号分隔的多个目的地的情况下，如果设置为 "true"，核心框架将跳过单独绑定它们，而是将这一责任委托给绑定器。

// 默认情况下，这个属性被设置为 "false"，在逗号分隔的多目的地列表中，绑定器将单独绑定每个目的地。
// 需要原生支持多个输入绑定的单个绑定器实现（多路）可以启用这个属性。
private boolean multiplex;
```



## header 模式



```java
// 当设置为 "none" 时，禁止对输入的头进行解析。
// 仅对不支持消息头并需要嵌入头的信息中间件有效。
// 当从非 Spring Cloud Stream 应用程序消耗数据，而不支持原生报头时，该选项很有用。
// 当设置为 headers 时，使用中间件的原生头信息机制。
// 当设置为'embeddedHeaders'时，将头信息嵌入到消息的有效载荷中。
// 默认值：取决于绑定器的实现。
// 目前与 spring cloud stream 一起分发的 Rabbit 和 Kafka 绑定器支持原生报头。
private HeaderMode headerMode;
```



## useNativeDecoding



```java
// 当设置为 "true" 时，入站消息会被客户端库直接反序列化.
// 客户端库必须进行相应的配置（例如，设置一个合适的 Kafka 生产者值序列化器）。
// 注意：这是绑定器的特定设置，如果绑定器不支持本地序列化/反序列化，则没有影响。目前只有Kafka绑定器支持它。默认：'false'。
private boolean useNativeDecoding;
```

