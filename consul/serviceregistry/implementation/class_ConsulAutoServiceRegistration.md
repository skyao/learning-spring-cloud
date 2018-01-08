# 服务自动注册的实现

服务自动注册的实现由类 ConsulAutoServiceRegistration 完成。

## bean创建的方式

在类 ConsulAutoServiceRegistrationAutoConfiguration 中，ConsulAutoServiceRegistration 的bean是这样创建的：

```java
@Bean
@ConditionalOnMissingBean
public ConsulAutoServiceRegistration consulAutoServiceRegistration(ConsulServiceRegistry registry, ConsulDiscoveryProperties properties, ConsulAutoRegistration consulRegistration) {
    return new ConsulAutoServiceRegistration(registry, properties, consulRegistration);
}
```

## 类定义

ConsulAutoServiceRegistration 定义如下：

```java
public class ConsulAutoServiceRegistration extends AbstractAutoServiceRegistration<ConsulRegistration> {
	// 额外保存了两个属性
	private ConsulDiscoveryProperties properties;
	private ConsulAutoRegistration registration;

	public ConsulAutoServiceRegistration(ConsulServiceRegistry serviceRegistry, ConsulDiscoveryProperties properties,
										 ConsulAutoRegistration registration) {
		super(serviceRegistry);
		this.properties = properties;
		this.registration = registration;
	}
}
```

这里使用到的泛型和服务注册时一样，都是 ConsulRegistration 。

## AbstractDiscoveryLifecycle 要求的方法实现

### register()方法

```java
@Override
protected void register() {
	// 多了一个配置项检查
    // 理论上可以设置不做注册？什么情况下用？
    if (!this.properties.isRegister()) {
        log.debug("Registration disabled.");
        return;
    }

    super.register();
}
```

deregister()/registerManagement()/deregisterManagement()方法类似。

