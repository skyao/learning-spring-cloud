# 类ConsulServiceRegistryAutoConfiguration

## 自动装配的设置

类ConsulServiceRegistryAutoConfiguration 的定义：

```java
@Configuration
@ConditionalOnConsulEnabled
@ConditionalOnProperty(value = "spring.cloud.service-registry.enabled", matchIfMissing = true)
@AutoConfigureBefore(ServiceRegistryAutoConfiguration.class)
public class ConsulServiceRegistryAutoConfiguration {}
```

逐个看：

1. @Configuration
2. @ConditionalOnConsulEnabled
3. @ConditionalOnProperty(value = "spring.cloud.service-registry.enabled", matchIfMissing = true)

	表明需要在配置项 `spring.cloud.service-registry.enabled` 设置为true，或者missing时有效。

4. @AutoConfigureBefore(ServiceRegistryAutoConfiguration.class)

	表明在 ServiceRegistryAutoConfiguration 配置完成之前再配置。

总结，ConsulServiceRegistryAutoConfiguration 要生效，条件如下：

1. 配置项 `spring.cloud.service-registry.enabled` 设置为开启
2. 配置项 spring.cloud.consul.enabled 设置为开启
3. 要有类 ConsulClient 存在

## 自动装配的Bean

### bean consulServiceRegistry

```java
@Bean
@ConditionalOnMissingBean
public ConsulServiceRegistry consulServiceRegistry(ConsulClient consulClient, ConsulDiscoveryProperties properties, HeartbeatProperties heartbeatProperties) {
    return new ConsulServiceRegistry(consulClient, properties, ttlScheduler, heartbeatProperties);
}
```

### bean ttlScheduler

```java
@Bean
@ConditionalOnMissingBean
@ConditionalOnProperty("spring.cloud.consul.discovery.heartbeat.enabled")
public TtlScheduler ttlScheduler(ConsulClient consulClient, HeartbeatProperties heartbeatProperties) {
    return new TtlScheduler(heartbeatProperties, consulClient);
}
```

### bean heartbeatProperties

```java
@Bean
@ConditionalOnMissingBean
public HeartbeatProperties heartbeatProperties() {
    return new HeartbeatProperties();
}
```

### bean consulDiscoveryProperties

```java
@Bean
@ConditionalOnMissingBean
public ConsulDiscoveryProperties consulDiscoveryProperties(InetUtils inetUtils) {
    return new ConsulDiscoveryProperties(inetUtils);
}
```