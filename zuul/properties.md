# 配置


zuul相关的配置项列表：

| 配置项 | 说明 | 默认值 | 示例 |
|--------|--------|--------|--------|
|    prefix    |    所有路由的通用前缀    |    ""    | zuul.prefix=/api |
|    stripPrefix    |    标记，表明在发送之前是否从path中去除前缀    |    true    | zuul.stripPrefix=false |
|    retryable    |    标记，默认是否支持重发（假定route是支持重发的）    |    false    | zuul.retryable=false |
|    routes    |    route名称的map，详细配置见ZuulRoute   |        | `routes.users.path=/myusers/**` |
|    addProxyHeaders    |    标记，用来判断proxy是否应该添加X-Forwarded-* headers   |    true    | routes.addProxyHeaders=true |
|    addHostHeader    |    标记，用来判断proxy是否应该发送Host header   |    false    | routes.addHostHeader=true |
|    ignoredServices    |    不自动代理的服务名集合。默认所有在discovery client中找到的服务都将被代理   |        | routes.ignoredServices=service1,service2 |
|    ignoredPatterns    |    不自动代理的服务名正则表达式。   |        | `routes.ignoredPatterns=/**/admin/**` |
|    ignoredHeaders    |    彻底忽略的HTTP header名称（如在下游请求中去除并在下游响应中删除）。   |        | routes.ignoredHeaders=header1,header2|
|    ignoreSecurityHeaders    |    当classpath中存在spring securiy时，标记添加SECURITY_HEADERS到被忽略header中   |    true    | routes.ignoreSecurityHeaders=true|
|    forceOriginalQueryStringEncoding    |    TBD   |    false    | routes.forceOriginalQueryStringEncoding=true|
|    servletPath    |    用于安装zuul作为servlet的路径，不是spring mvc的一部分。   |    "/zuul"    | routes.servletPath="/zuul"|
|    ignoreLocalService    |    TBD   |    true    | routes.ignoreLocalService=false|
|    host    |    控制默认连接池属性，具体见Host   |        | routes.host=false|
|    traceRequestBody    |    标记，标明request body是否可以被trace   |    true    | routes.traceRequestBody=false|
|    removeSemicolonContent    |    标记，在第一个分号之后的path元素是否应该被删除   |    true    | routes.removeSemicolonContent=false|
|    removeSemicolonContent    |    标记，在第一个分号之后的path元素是否应该被删除   |    true    | routes.removeSemicolonContent=false|



## ZuulRoute

## Host
