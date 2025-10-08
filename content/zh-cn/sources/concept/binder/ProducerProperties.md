---
title: "ProducerProperties"
linkTitle: "ProducerProperties"
weight: 40
date: 2021-03-03
description: >
  spring cloud stream的核心概念-ProducerProperties
---

## ProducerProperties 类

ProducerProperties 类定义通用的 producer 属性:

```java
public class ProducerProperties {
	// 此生产者绑定的绑定名称
	private String bindingName;

	// 标志着这个生产者是否需要自动启动。默认值: true
	private boolean autoStartup = true;
  
  // 同 ComsumerProperties
	private HeaderMode headerMode;

  // 同 ComsumerProperties
	private boolean useNativeEncoding = false;
	......
}
```

TBD：以下参数的意思待查明：

```java
  private String[] requiredGroups = new String[] {};
	private boolean errorChannelEnabled = false;
```



## 分区相关的参数

### partiton key

获取 partiton key 的方式有两种：通过 partitionKeyExpression 或者 通过 partitionKeyExtractorName 指定的 bean。两种方式只能选择其中一个：

```java
@JsonSerialize(using = ExpressionSerializer.class)
private Expression partitionKeyExpression;

// 实现 PartitionKeyExtractorStrategy 的 bean 的名字
// 用于提取 key，而这个 key 将用于计算 partition id （参见 partitionSelector)
private String partitionKeyExtractorName;


```

根据 partitionKeyExpression 和 partitionKeyExtractorName 的设置可以判断是否有做分区：

```java
	public boolean isPartitioned() {
		return this.partitionKeyExpression != null
				|| this.partitionKeyExtractorName != null;
	}
```

### partiton id

从 partiton key 计算出 partiton id 的方式也有两种：通过 partitionSelectorExpression 或者通过 partitionSelectorName 指定的 bean。同样，两种方式只能选择其中一个：

```java
// 实现 PartitionSelectorStrategy 的 bean 的名字
// 用于根据 partition key 来决定 partition id 
private String partitionSelectorName;

@JsonSerialize(using = ExpressionSerializer.class)
private Expression partitionSelectorExpression;
```

partiton key 和 partiton id 共用同一个 ExpressionSerializer 的实现：

```java
static class ExpressionSerializer extends JsonSerializer<Expression> {

  @Override
  public void serialize(Expression expression, JsonGenerator jsonGenerator,
                        SerializerProvider serializerProvider) throws IOException {
    if (expression != null) {
      jsonGenerator.writeString(expression.getExpressionString());
    }
  }
}
```

### partition count

partitionCount 参数指定分区数量：

```java
private int partitionCount = 1;
```



## PollerProperties

PollerProperties 的参数：

```java
public static class PollerProperties {

  private Duration fixedDelay = Duration.ofMillis(1000);

  private long maxMessagesPerPoll = 1L;

  private String cron;

  private Duration initialDelay = Duration.ofMillis(0);
}
```



## requiredGroups



## errorChannelEnabled



