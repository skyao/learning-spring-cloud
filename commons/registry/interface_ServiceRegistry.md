# 接口ServiceRegistry

接口 ServiceRegistry 在服务注册中用于注册和注销实例的契约。

## 接口定义

```java
package org.springframework.cloud.client.serviceregistry;

public interface ServiceRegistry<R extends Registration> {}
```

Registration 是一个标记接口，被ServiceRegistry使用，用来获取和当前注册对象关联的 serviceId。

```java
public interface Registration {
	String getServiceId();
}
```

## 接口方法

- register()

    注册。registration 通常拥有关于实例的信息，如 hostname 和 端口.

    ```java
    void register(R registration);
    ```

- deregister()

    注销。

    ```java
    void deregister(R registration);
    ```

- close()

    关闭服务注册。这是一个生命周期方法。

    ```java
    void close();
    ```

- setStatus()

    设置注册对象的状态。状态的值由单独的实现来检测。

    ```java
    void setStatus(R registration, String status);
    ```

- getStatus()

    获取特定注册对象的状态。

    ```java
    <T> T getStatus(R registration);
    ```

备注: setStatus()和getStatus() 具体见 ServiceRegistryEndpoint。