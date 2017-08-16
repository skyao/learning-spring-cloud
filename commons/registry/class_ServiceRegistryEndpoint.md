# 类ServiceRegistryEndpoint

## 类定义

```java
package org.springframework.cloud.client.serviceregistry.endpoint;

@ManagedResource(description = "Can be used to display and set the service instance status using the service registry")
public class ServiceRegistryEndpoint implements MvcEndpoint {}
```

## 属性和构造函数

```java
private final ServiceRegistry serviceRegistry;
private Registration registration;

public ServiceRegistryEndpoint(ServiceRegistry<?> serviceRegistry) {
    this.serviceRegistry = serviceRegistry;
}

public void setRegistration(Registration registration) {
    this.registration = registration;
}
```

保持有一个 serviceRegistry 实例，通过构造函数传入，final不可变。registration 通过setter传入。

## 类方法

### MvcEndpoint 接口实现方法

- getEndpointType()

	这里直接返回null了，也就是说 ServiceRegistryEndpoint 这个MvcEndpoint 暴露的 Endpoint 不是一个传统的 MvcEndpoint。

	```java
	public Class<? extends Endpoint<?>> getEndpointType() {
		return null;
	}
    ```

- isSensitive()

	isSensitive()返回true，也就是说这些暴露的信息对普通用户是敏感的。

	```java
	public boolean isSensitive() {
		return true;
	}
    ```

- getPath()

	mvc路径为 "/service-registry"。

	```java
	public String getPath() {
		return "/service-registry";
	}
    ```

### 自定义的方法

- setStatus()

	```java
	@RequestMapping(path = "instance-status", method = RequestMethod.POST)
	@ResponseBody
	@ManagedOperation
	public ResponseEntity<?> setStatus(@RequestBody String status) {
		Assert.notNull(status, "status may not by null");

		// 如果registration没有设置则报错
		if (this.registration == null) {
			return ResponseEntity.status(HttpStatus.NOT_FOUND).body("no registration found");
		}

		// 调用serviceRegistry.setStatus()方法设置状态
        // 这也就体现了 ServiceRegistryEndpoint 作为 Endpoint 的功能：暴露信息
		this.serviceRegistry.setStatus(this.registration, status);
		return ResponseEntity.ok().build();
	}
    ```


- getStatus()

	```java
	@RequestMapping(path = "instance-status", method = RequestMethod.GET)
	@ResponseBody
	@ManagedAttribute
	public ResponseEntity getStatus() {
    	// 如果registration没有设置则报错
		if (this.registration == null) {
			return ResponseEntity.status(HttpStatus.NOT_FOUND).body("no registration found");
		}

		// 调用serviceRegistry.getStatus()方法，以Endpoint的方式暴露状态信息
		return ResponseEntity.ok().body(this.serviceRegistry.getStatus(this.registration));
	}
    ```

## 分析

ServiceRegistryEndpoint 是一个标准的 MvcEndpoint，通过 getStatus()/setStatus()方法，暴露了 serviceRegistry 对应的 getStatus()/setStatus()方法。
