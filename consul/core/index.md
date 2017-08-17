# Consul Core模块

## 自动装配的设置

`META-INF/spring.factories` 的内容：

```bash
# Auto Configuration
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.cloud.consul.ConsulAutoConfiguration
```

类ConsulAutoConfiguration的内容：

```java
@Configuration
@EnableConfigurationProperties
@ConditionalOnConsulEnabled
public class ConsulAutoConfiguration {......}
```

## 自动装配的生效条件

由 @ConditionalOnConsulEnabled 控制，类ConditionalOnConsulEnabled的内容：

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
@Conditional(ConditionalOnConsulEnabled.OnConsulEnabledCondition.class)
public @interface ConditionalOnConsulEnabled {

	class OnConsulEnabledCondition extends AllNestedConditions {

		public OnConsulEnabledCondition() {
			super(ConfigurationPhase.REGISTER_BEAN);
		}

		@ConditionalOnProperty(value = "spring.cloud.consul.enabled", matchIfMissing = true)
		static class FoundProperty {}

		@ConditionalOnClass(ConsulClient.class)
		static class FoundClass {}
	}
}
```

可以看到 @ConditionalOnConsulEnabled 生效的前提是：

1. 配置项 "spring.cloud.consul.enabled" 生: true或者missing
2. 能找到类 ConsulClient

## 自动装配的bean

### bean consulProperties

```java
@Bean
@ConditionalOnMissingBean
public ConsulProperties consulProperties() {
    return new ConsulProperties();
}
```

具体配置看类 ConsulProperties：

```java
@ConfigurationProperties("spring.cloud.consul")
public class ConsulProperties {
	/** Consul agent hostname. 默认 'localhost'. */
	@NotNull
	private String host = "localhost";

	/** Consul agent 端口. 默认 '8500'. */
	@NotNull
	private int port = 8500;

	/** 是否开启 spring cloud consul */
	private boolean enabled = true;
}
```

默认时连接 localhost 的 consul agent，如果需要直接连接到consul server，可以将 host/port 设置为目标地址即可。

### bean consulClient

```java
@Bean
@ConditionalOnMissingBean
public ConsulClient consulClient(ConsulProperties consulProperties) {
    return new ConsulClient(consulProperties.getHost(), consulProperties.getPort());
}
```

从配置信息中得到 host, port，然后创建 ConsulClient 对象。

### 其他bean

Health 和 retry 的暂时跳过，以后再细看。




