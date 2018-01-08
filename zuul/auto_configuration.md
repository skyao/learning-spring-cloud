# 自动装配

## ZuulProxyAutoConfiguration

## ZuulServerAutoConfiguration

ZuulServerAutoConfiguration的类定义：

```java
@Configuration
@EnableConfigurationProperties({ ZuulProperties.class })
@ConditionalOnClass(ZuulServlet.class)
@ConditionalOnBean(ZuulServerMarkerConfiguration.Marker.class)
@Import(ServerPropertiesAutoConfiguration.class)
public class ZuulServerAutoConfiguration {}
```

其中ZuulProperties的定义如下：

```java
@ConfigurationProperties("zuul")
public class ZuulProperties {}
```

具体请见后面一节 [配置](properties.md)。
