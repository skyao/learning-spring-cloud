# starter

## 更名

从1.4版本开始，starter文件的artifactId从原来的`spring-cloud-starter-zuul`修改为`spring-cloud-starter-netflix-zuul`。

原来的starter pom文件路径是在`spring-cloud-starter-zuul/spring-cloud-starter-zuul.pom`：

```xml
<parent>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-netflix</artifactId>
    <version>1.4.0.RELEASE</version>
    <relativePath>..</relativePath> <!-- lookup parent from repository -->
</parent>
<artifactId>spring-cloud-starter-zuul</artifactId>
<name>spring-cloud-starter-zuul</name>
<description>Spring Cloud Starter Zuul (deprecated, please use spring-cloud-starter-netflix-zuul)</description>
```

新的starter文件被转移到`spring-cloud-starter-netflix`目录下，全路径为`spring-cloud-starter-netflix/spring-cloud-starter-netflix-zuul/spring-cloud-netflix-starter-zuul.pom`

```xml
<parent>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix</artifactId>
    <version>1.4.0.RELEASE</version>
</parent>
<artifactId>spring-cloud-starter-netflix-zuul</artifactId>
<name>Spring Cloud Starter Netflix Zuul</name>
<description>Spring Cloud Starter Netflix Zuul</description>
```

注意parent也从`spring-cloud-netflix`修改为`spring-cloud-starter-netflix`。

## 依赖

spring-cloud-starter-netflix-zuul引入的依赖如下：

- org.springframework.boot

	- spring-boot-starter-web
	- spring-boot-starter-actuator

- org.springframework.cloud

	- spring-cloud-starter
	- spring-cloud-starter-netflix-hystrix
	- spring-cloud-starter-netflix-ribbon
	- spring-cloud-starter-netflix-archaius

- com.netflix.zuul

	- zuul-core

在`spring-cloud-starter-netflix-hystrix`和`spring-cloud-starter-netflix-ribbon`中都依赖了`spring-cloud-netflix-core`，而spring cloud对zuul的集成代码都在`spring-cloud-netflix-core`项目中。