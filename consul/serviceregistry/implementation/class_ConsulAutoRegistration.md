# 类ConsulAutoRegistration

## bean的构造方式

在类 ConsulAutoServiceRegistrationAutoConfiguration 中，通过下面的方式构造 ConsulAutoRegistration 的 bean:

```java
@Bean
@ConditionalOnMissingBean
public ConsulAutoRegistration consulRegistration(ConsulDiscoveryProperties properties, ApplicationContext applicationContext,
                                             ServletContext servletContext, HeartbeatProperties heartbeatProperties) {
    return ConsulAutoRegistration.registration(properties, applicationContext, servletContext, heartbeatProperties);
```

这里和 ConsulServiceRegistry 与 ConsulRegistration 的方式很不相同. ConsulAutoRegistration 作为一个标准的spring bean 给 ConsulAutoServiceRegistration 使用,单例.而ConsulRegistration 是通过方法传递给 ConsulServiceRegistry.如register()方法:

```java
public void register(ConsulRegistration reg) {......}
```

### registration()方法的实现

```java
public static ConsulAutoRegistration registration(ConsulDiscoveryProperties properties, ApplicationContext context,　ServletContext servletContext, HeartbeatProperties heartbeatProperties) {
    RelaxedPropertyResolver propertyResolver = new RelaxedPropertyResolver(context.getEnvironment());

    NewService service = new NewService();
    // 取应用名
    String appName = getAppName(properties, propertyResolver);
    // 取ID
    service.setId(getInstanceId(properties, context));
    if(!properties.isPreferAgentAddress()) {
        service.setAddress(properties.getHostname());
    }
    service.setName(normalizeForDns(appName));
    // 取tag
    service.setTags(createTags(properties, servletContext));

    if (properties.getPort() != null) {
        service.setPort(properties.getPort());
        // we know the port and can set the check
        setCheck(service, properties, context, heartbeatProperties);
    }
	// 最后返回ConsulAutoRegistration对象
    return new ConsulAutoRegistration(service, properties, context, heartbeatProperties);
}
```

## 类定义

ConsulAutoRegistration 定义如下：

```java
// 继承自ConsulRegistration
public class ConsulAutoRegistration extends ConsulRegistration {
	// 增加了三个属性
	private final ConsulDiscoveryProperties properties;
	private final ApplicationContext context;
	private final HeartbeatProperties heartbeatProperties;

	public ConsulAutoRegistration(NewService service, ConsulDiscoveryProperties properties, ApplicationContext context, HeartbeatProperties heartbeatProperties) {
		super(service);
		this.properties = properties;
		this.context = context;
		this.heartbeatProperties = heartbeatProperties;
	}
}
```



