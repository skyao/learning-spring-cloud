# serviceregistry的自动装配

serviceregistry模块需要自动装配的内容有：

- serviceregistry.ConsulServiceRegistryAutoConfiguration
- serviceregistry.ConsulAutoServiceRegistrationAutoConfiguration

这两个类名有点长，按语意应该是这样：

- Consul **ServiceRegistry** AutoConfiguration
- Consul **AutoServiceRegistration** AutoConfiguration

两个类都是 consul 的自动装配，一个是　ServiceRegistry/服务注册，一个是　AutoServiceRegistration　自动服务注册。



