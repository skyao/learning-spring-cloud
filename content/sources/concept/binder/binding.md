---
title: "binding"
linkTitle: "binding"
weight: 10
date: 2021-03-03
description: >
  spring cloud stream的核心概念-binding定义
---



## binding 定义

>  Represents a binding between an input or output and an adapter endpoint that connects via a Binder. The binding could be for a consumer or a producer. A consumer binding represents a connection from an adapter to an input. A producer binding represents a connection from an output to an adapter.

代表一个输入或输出和一个适配器端点之间的绑定，这个绑定是通过Binder连接的。该绑定可以是用于消费者或生产者。消费者绑定表示从适配器到输入的连接。生产者绑定代表从输出到适配器的连接。 

接口定义：

```java
// `Binding<T>`: binding的类型
public interface Binding<T> extends Pausable {

	default Map<String, Object> getExtendedInfo() {
		return Collections.emptyMap();
	}

  // 组件启动之后实例就已经 start，因此 stop() / start() 方法通常用于 re-bind / re-start。
	default void start() {
	}

	default void stop() {
	}

  // 当且仅当组件实现了 Pausable 时，pause() / resume() 方法可以用于实现 pause/resume 操作。
	default void pause() {
		this.stop();
	}

	default void resume() {
		this.start();
	}

  // isRunning() 方法在当前实例所表示的目标组件在运行时返回true。
	default boolean isRunning() {
		return false;
	}

  // getName() 方法返回的是 `destination name`，也就是当前binding的目的地的名字。
	default String getName() {
		return null;
	}

  // getBindingName() 返回的是 `binding name`，也就是当前绑定目标的名字，如 channel name 
	default String getBindingName() {
		return null;
	}

  // 解除这个实例所代表的目标组件的绑定，并停止任何活动组件。实现必须是idempotent的。
  // 在这个方法被调用后，目标组件不会收到任何消息；这个实例应该被丢弃，而应该创建一个新的Binding。 
	void unbind();

  // isInput() 方法表明当前 binding 的类型，true 是 input binding，false 是 output binding。
  // 由于 @input 和 @output 只能用一个，因此不会同时即是 input 又是 output。
  // （TBD：有点怪，如果真要同时支持 input 和 output 该怎么办？创建两个实例？）
	default boolean isInput() {
		throw new UnsupportedOperationException(
				"Binding implementation `" + this.getClass().getName()
						+ "` must implement this operation before it is called");
	}

}
```



## 默认实现



```java
@JsonPropertyOrder({ "bindingName", "name", "group", "pausable", "state" })
@JsonIgnoreProperties("running")
public class DefaultBinding<T> implements Binding<T> {
  
}
```

getName() 和 getBindingName() 的差异就很清楚了，getName() 返回的就是构建binding时给出的 name，而 getBindingName() 则要看 target 是不是 IntegrationObjectSupport ，如果是，则取 target 的 getComponentName。

```java
	@Override
	public String getName() {
		return this.name;
	}

	@Override
	public String getBindingName() {
		String resolvedName = (this.target instanceof IntegrationObjectSupport)
				? ((IntegrationObjectSupport) this.target).getComponentName() : getName();
		return resolvedName == null ? getName() : resolvedName;
	}
```









