# AutoServiceRegistration

## 接口AutoServiceRegistration

这是一个空接口。

```java
public interface AutoServiceRegistration {
}
```

## 类AbstractAutoServiceRegistration

类 AbstractAutoServiceRegistration 提供有用而通用的生命周期方法给 ServiceRegistry 的实现。

```java
public abstract class AbstractAutoServiceRegistration<R extends Registration> extends AbstractDiscoveryLifecycle implements AutoServiceRegistration {}
```

### 属性和构造函数

```java
private ServiceRegistry<R> serviceRegistry;

protected AbstractAutoServiceRegistration(ServiceRegistry<R> serviceRegistry) {
    this.serviceRegistry = serviceRegistry;
}

protected ServiceRegistry<R> getServiceRegistry() {
    return this.serviceRegistry;
}

protected abstract R getRegistration();

protected abstract R getManagementRegistration();
```

1. 属性 serviceRegistry 居然不是final?从代码上看没有修改的可能。

	https://github.com/spring-cloud/spring-cloud-commons/pull/241

    提交了一个PR给spring cloud，已经被merge到master了。

2. 看后面的代码实现分析，getRegistration()不能返回null，而getManagementRegistration()可以返回null

### AbstractDiscoveryLifecycle的实现方法

```java
/**
 * 用ServiceRegistry注册本地服务
 */
@Override
protected void register() {
    this.serviceRegistry.register(getRegistration());
}

/**
 * 用ServiceRegistry注册本地管理服务
 */
@Override
protected void registerManagement() {
    R registration = getManagementRegistration();
    if (registration != null) {
        this.serviceRegistry.register(registration);
    }
}

/**
 * 用ServiceRegistry注销本地服务
 */
@Override
protected void deregister() {
    this.serviceRegistry.deregister(getRegistration());
}

/**
 * 用ServiceRegistry注销本地管理服务
 */
@Override
protected void deregisterManagement() {
    R registration = getManagementRegistration();
    if (registration != null) {
        this.serviceRegistry.deregister(registration);
    }
}
```

### DiscoveryLifecycle的实现方法

AbstractAutoServiceRegistration 重写了close()方法

```java
@Override
public void stop() {
    if (this.getRunning().compareAndSet(true, false) && isEnabled()) {
        deregister();
        if (shouldRegisterManagement()) {
            deregisterManagement();
        }
        // 上面的内容是基类 AbstractDiscoveryLifecycle 的实现内容
        // AbstractAutoServiceRegistration 增加了下面这行代码
        // 来关闭serviceRegistry
        this.serviceRegistry.close();
    }
}
```
