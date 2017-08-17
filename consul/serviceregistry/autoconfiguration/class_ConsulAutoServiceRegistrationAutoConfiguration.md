# 服务自动注册的自动装配

服务自动注册的自动装配由类 ConsulAutoServiceRegistrationAutoConfiguration 完成。

类 ConsulAutoServiceRegistrationAutoConfiguration 上有一堆注解：

```java
@Configuration
@ConditionalOnBean(AutoServiceRegistrationProperties.class)
@ConditionalOnMissingBean(type = "org.springframework.cloud.consul.discovery.ConsulLifecycle")
@ConditionalOnConsulEnabled
@ConditionalOnProperty(value = "spring.cloud.service-registry.auto-registration.enabled", matchIfMissing = true)
@AutoConfigureAfter(ConsulServiceRegistryAutoConfiguration.class)
public class ConsulAutoServiceRegistrationAutoConfiguration {}
```

逐个看：

1. @Configuration

	表明是Configuration，后面会有对应的方法和 @bean 注解来提供bean.

2. @ConditionalOnBean(AutoServiceRegistrationProperties.class)

	表明需要在类 AutoServiceRegistrationProperties 的bean存在时才生效。

3. @ConditionalOnMissingBean(type = "org.springframework.cloud.consul.discovery.ConsulLifecycle")

	表明只有有类 ConsulLifecycle 的bean不存在时才生效。

4. @ConditionalOnConsulEnabled

	Consul的开关配置，见下面。

5. @ConditionalOnProperty(value = "spring.cloud.service-registry.auto-registration.enabled", matchIfMissing = true)

	表明只有配置项 `spring.cloud.service-registry.auto-registration.enabled` 设置为开启才有效，当然如果没有设置则视为true。同样注意这个配置项是 spring cloud 的统一配置，不是 consul 特有的。

6. @AutoConfigureAfter(ConsulServiceRegistryAutoConfiguration.class)

	表明在 ConsulServiceRegistryAutoConfiguration 配置完成之后再配置。

细看一下 @ConditionalOnConsulEnabled的内容：

```bash
@Conditional(ConditionalOnConsulEnabled.OnConsulEnabledCondition.class)
public @interface ConditionalOnConsulEnabled {

    class OnConsulEnabledCondition extends AllNestedConditions {

        public OnConsulEnabledCondition() {
            super(ConfigurationPhase.REGISTER_BEAN);
        }

		// 可以通过 spring.cloud.consul.enabled 做设置
        @ConditionalOnProperty(value = "spring.cloud.consul.enabled", matchIfMissing = true)
        static class FoundProperty {}

		// 要有类 ConsulClient
        @ConditionalOnClass(ConsulClient.class)
        static class FoundClass {}
    }
}
```

总结，ConsulAutoServiceRegistrationAutoConfiguration 要生效，条件如下：

1. 类 AutoServiceRegistrationProperties 的bean存在
2. 类 ConsulLifecycle 的bean不存在
3. 配置项 `spring.cloud.service-registry.auto-registration.enabled` 设置为开启
4. 配置项 spring.cloud.consul.enabled 设置为开启
5. 要有类 ConsulClient 存在

## 自动装配的Bean

### bean ConsulAutoServiceRegistration

```java
@Bean
@ConditionalOnMissingBean
public ConsulAutoServiceRegistration consulAutoServiceRegistration(ConsulServiceRegistry registry, ConsulDiscoveryProperties properties, ConsulAutoRegistration consulRegistration) {
    return new ConsulAutoServiceRegistration(registry, properties, consulRegistration);
}
```

这个方法在参数中传进来的 ConsulServiceRegistry 和 ConsulDiscoveryProperties 这两个bean都是在 ConsulServiceRegistryAutoConfiguration 里面创建的。所以要通过 `@AutoConfigureAfter(ConsulServiceRegistryAutoConfiguration.class)` 来设置初始化顺序。

### bean ConsulAutoRegistration

```java
@Bean
@ConditionalOnMissingBean
public ConsulAutoRegistration consulRegistration(ConsulDiscoveryProperties properties, ApplicationContext applicationContext, ServletContext servletContext, HeartbeatProperties heartbeatProperties) {
    return ConsulAutoRegistration.registration(properties, applicationContext, servletContext, heartbeatProperties);
}
```

HeartbeatProperties 同样来自 ConsulServiceRegistryAutoConfiguration。

这里比较闹心的时引入了 ServletContext，总不至于用consul做客户端服务发现也需要servlet 环境吧？

检查了一下代码，还好，只是在 ConsulAutoRegistration 的 createTags()方法中使用，取了一下ContextPath。重要的是，有 `servletContext != null` 的判断。

```java
public static List<String> createTags(ConsulDiscoveryProperties properties, ServletContext servletContext) {
    List<String> tags = new LinkedList<>(properties.getTags());
    if(servletContext != null
            && StringUtils.hasText(servletContext.getContextPath())
            && StringUtils.hasText(servletContext.getContextPath().replaceAll("/", ""))) {
        tags.add("contextPath=" + servletContext.getContextPath());
    }
	......
    return tags;
}
```