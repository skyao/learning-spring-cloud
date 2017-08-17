# 类ConsulServiceRegistry

## 类定义

ConsulServiceRegistry 是 consul 中实现服务注册的主要类。定义如下：

```java
public class ConsulServiceRegistry implements ServiceRegistry<ConsulRegistration> {}
```

先看看类ConsulRegistration的内容。

## 类ConsulRegistration

类ConsulRegistration保存有consul做注册的信息：

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

## ServiceRegistry定义的方法实现

### register()方法

```java
@Override
public void register(ConsulRegistration reg) {
    log.info("Registering service with consul: " + reg.getService());
    try {
    	// 调用ConsulClient做注册
        client.agentServiceRegister(reg.getService(), properties.getAclToken());
        // 如果开启心跳，则交给ttlScheduler
        if (heartbeatProperties.isEnabled() && ttlScheduler != null) {
            ttlScheduler.add(reg.getInstanceId());
        }
    }
    catch (ConsulException e) {
    	// 如果注册出错，检查是否设置了 failFast
        // 如果要求 failFast，则抛异常要求退出
        if (this.properties.isFailFast()) {
            log.error("Error registering service with consul: " + reg.getService(), e);
            ReflectionUtils.rethrowRuntimeException(e);
        }
        // 如果不要求 failFast，则打日志警告
        log.warn("Failfast is false. Error registering service with consul: " + reg.getService(), e);
    }
}
```

### deregister()方法

```java
@Override
public void deregister(ConsulRegistration reg) {
    if (ttlScheduler != null) {
    	// 移除心跳
        ttlScheduler.remove(reg.getInstanceId());
    }
    if (log.isInfoEnabled()) {
        log.info("Deregistering service with consul: " + reg.getInstanceId());
    }
    // 调用ConsulClient做注销
    client.agentServiceDeregister(reg.getInstanceId());
}
```

### setStatus()方法

```java
@Override
public void setStatus(ConsulRegistration registration, String status) {
    if (status.equalsIgnoreCase(OUT_OF_SERVICE.getCode())) {
    	// 如果是 OUT_OF_SERVICE，开启维护模式
        client.agentServiceSetMaintenance(registration.getInstanceId(), true);
    } else if (status.equalsIgnoreCase(UP.getCode())) {
       // 如果是 UP，取消维护模式 client.agentServiceSetMaintenance(registration.getInstanceId(), false);
    } else {
        throw new IllegalArgumentException("Unknown status: "+status);
    }

}
```



