# 类ServiceRegistryAutoConfiguration

类ServiceRegistryAutoConfiguration 用来自动配置ServiceRegistry，构建ServiceRegistryEndpoint的bean：

```java
@Configuration
public class ServiceRegistryAutoConfiguration {

	@ConditionalOnBean(ServiceRegistry.class)
	@ConditionalOnClass(Endpoint.class)
	protected class ServiceRegistryEndpointConfiguration {
		@Autowired(required = false)
		private Registration registration;

		// 构建 ServiceRegistryEndpoint 的bean
		@Bean
		public ServiceRegistryEndpoint serviceRegistryEndpoint(ServiceRegistry serviceRegistry) {
        	// new出ServiceRegistryEndpoint实例
			ServiceRegistryEndpoint endpoint = new ServiceRegistryEndpoint(serviceRegistry);
            // 设置registration（可能为null？）
			endpoint.setRegistration(registration);
			return endpoint;
		}
	}
}
```

