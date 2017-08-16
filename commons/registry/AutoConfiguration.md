# AutoConfiguration

## 类AutoServiceRegistrationAutoConfiguration

类 AutoServiceRegistrationAutoConfiguration 是标准的 @Configuration：

```java
@Configuration
@ConditionalOnBean(AutoServiceRegistrationProperties.class)
public class AutoServiceRegistrationAutoConfiguration {

	@Autowired(required = false)
	private AutoServiceRegistration autoServiceRegistration;

	@Autowired
	private AutoServiceRegistrationProperties properties;

	@PostConstruct
	protected void init() {
    	// failfast 检查
        // 如果 autoServiceRegistration 为空，并且开启了 failFast，则报错
		if (autoServiceRegistration == null && this.properties.isFailFast()) {
			throw new IllegalStateException("Auto Service Registration has been requested, but there is no AutoServiceRegistration bean");
		}
	}
}
```

## 类AutoServiceRegistrationProperties

配置项信息如下：

```java
@ConfigurationProperties("spring.cloud.service-registry.auto-registration")
public class AutoServiceRegistrationProperties {

	/** 自动服务注册是否开启，默认为ture */
	private boolean enabled = true;

	/** 是否注册管理为服务，默认为true */
	private boolean registerManagement = true;

	/** 如果没有 AutoServiceRegistration ，是否应该在启动时失败，默认false */
	private boolean failFast = false;
    ......
}
```

备注：failFast 默认为 false，也就是说如果初始化时不做 failFast，即使 autoServiceRegistration 为空也不让启动失败。

TBD： 不确认上述情况发生时会是什么状况？是否有必要在配置文件中设置为 true？

## 类AutoServiceRegistrationConfiguration

类AutoServiceRegistrationConfiguration 基本上啥都不干，只是初始化 AutoServiceRegistrationProperties：

```java
@Configuration
@EnableConfigurationProperties(AutoServiceRegistrationProperties.class)
public class AutoServiceRegistrationConfiguration {
}
```