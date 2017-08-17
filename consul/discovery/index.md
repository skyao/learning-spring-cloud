# discovery模块

spring-cloud-consul-discovery模块下实际有 serviceregistry 和 discovery 两大块。

## 自动装配

`META-INF/spring.factories` 的内容:

```bash
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.cloud.consul.discovery.RibbonConsulAutoConfiguration,\
org.springframework.cloud.consul.discovery.configclient.ConsulConfigServerAutoConfiguration,\
org.springframework.cloud.consul.serviceregistry.ConsulAutoServiceRegistrationAutoConfiguration,\
org.springframework.cloud.consul.serviceregistry.ConsulServiceRegistryAutoConfiguration

# Discovery Client Configuration
org.springframework.cloud.client.discovery.EnableDiscoveryClient=\
org.springframework.cloud.consul.discovery.ConsulDiscoveryClientConfiguration

org.springframework.cloud.bootstrap.BootstrapConfiguration=\
org.springframework.cloud.consul.discovery.configclient.ConsulDiscoveryClientConfigServiceBootstrapConfiguration
```

需要自动装配的内容有：

- discovery.RibbonConsulAutoConfiguration： 给ribbon用的
- discovery.ConsulConfigServerAutoConfiguration
- serviceregistry.ConsulAutoServiceRegistrationAutoConfiguration
- serviceregistry.ConsulServiceRegistryAutoConfiguration

然后还有定义的 Discovery Client Configuration：

- ConsulDiscoveryClientConfiguration
- ConsulDiscoveryClientConfigServiceBootstrapConfiguration






