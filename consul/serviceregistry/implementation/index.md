# 服务注册的实现

在自动装配清晰之后，我们在来继续看服务注册的具体实现方式。

从　AutoConfiguration　的代码实现可以找到对应的服务注册实现类：

| 　 |　实现类(bean)| 注册信息　|
|--------|--------|--------|
|    服务注册    |　ConsulServiceRegistry | ConsulRegistration |
|    服务自动注册    | ConsulAutoServiceRegistration| ConsulAutoRegistration |



