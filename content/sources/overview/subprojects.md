---
title: "子项目情况"
linkTitle: "子项目"
weight: 10
date: 2021-03-03
description: >
  spring cloud stream 子项目情况
---



## bom

bom目录下有两个子项目，定义 starter 和 dependencies。

### spring-cloud-starter-parent

依赖的 spring boot 版本是 2.6.8：

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.6.8</version>
  <relativePath/>
</parent>
<groupId>org.springframework.cloud</groupId>
<artifactId>spring-cloud-stream-starter-parent</artifactId>
<version>3.2.4</version>
<name>spring-cloud-stream-starter-parent</name>
```



### spring-cloud-stream-dependencies

定义 spring-cloud-stream 下各个子项目的版本：

```xml
<parent>
  <artifactId>spring-cloud-dependencies-parent</artifactId>
  <groupId>org.springframework.cloud</groupId>
  <version>3.1.3</version>
  <relativePath/>
</parent>
<artifactId>spring-cloud-stream-dependencies</artifactId>
<version>3.2.4</version>
<packaging>pom</packaging>
<name>spring-cloud-stream-dependencies</name>
<description>Spring Cloud Stream Dependencies</description>
```



## Core

### spring-cloud-stream

使用Spring integration 的消息微服务

### spring-cloud-stream-binder-test

对 binder 实现的测试支持。

### spring-cloud-stream-integaration-tests

Spring Cloud Stream 的集成测试。

### spring-cloud-stream-test-support

一组类，以方便对Spring Cloud Stream模块的测试。

### spring-cloud-stream-test-support-internal

一系列的类和实用程序代码，可以帮助测试 spring-cloud-stream 本身，以及模块。



## Binder

spring cloud stream 项目只提供两个 binder，分别支持 kafka 和 RabbitMQ



### kafka binder



### RabbitMQ binder



