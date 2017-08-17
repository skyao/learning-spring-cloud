# 服务注册的实现

服务注册的实现由类 ConsulServiceRegistry 完成。

## 类定义

ConsulServiceRegistry 是 consul 中实现服务注册的主要类。定义如下：

```java
public class ConsulServiceRegistry implements ServiceRegistry<ConsulRegistration> {}
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

### getStatus()方法

```java
public Object getStatus(ConsulRegistration registration) {
    String serviceId = registration.getServiceId();
    // 调用ConsulClient获取健康检查的信息
    Response<List<Check>> response = client.getHealthChecksForService(serviceId, QueryParams.DEFAULT);
    List<Check> checks = response.getValue();

    for (Check check : checks) {
        if (check.getServiceId().equals(registration.getInstanceId())) {
        	//　如果有信息表明当前服务是维护状态，则返回OUT_OF_SERVICE
            if (check.getName().equalsIgnoreCase("Service Maintenance Mode")) {
                return OUT_OF_SERVICE.getCode();
            }
        }
    }

	// 否则返回UP
    return UP.getCode();
}
```

