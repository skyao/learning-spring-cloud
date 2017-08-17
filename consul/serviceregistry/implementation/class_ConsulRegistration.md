# ConsulRegistration

类ConsulRegistration保存有 consul 做服务注册的信息：

```java
public class ConsulRegistration implements Registration {
	// 其实就一个consul定义的 NewService 的实例
	private final NewService service;

	public ConsulRegistration(NewService service) {
		this.service = service;
	}

	public String getInstanceId() {
		return getService().getId();
	}

	public String getServiceId() {
    	// serviceId取 NewService 的 name 属性
        // 对照上面 getInstanceId() 方法，那里返回的是 id 属性
		return getService().getName();
	}
}
```


