---
title: "Spring Cloud 子项目"
linkTitle: "Spring Cloud 子项目"
weight: 10
date: 2021-01-29
description: >
  Spring Cloud 子项目介绍
---



## 特别关注的项目

### Spring Cloud Config

由 git 仓库支持的集中式外部配置管理。配置资源直接映射到 Spring  `Environment`，但如果需要的话，也可以被非 Spring 应用使用。

### Spring cloud Cluster

领导选举和常见的有状态模式，对Zookeeper、Redis、Hazelcast、Consul进行抽象和实现。

### Spring Cloud Data Flow

为现代运行时上的可组合微服务应用提供云原生协调服务。易于使用的DSL、拖放式GUI和REST-APIs共同简化了基于数据管道的微服务的整体协调工作。

### Spring Cloud Data Stream

一个轻量级的事件驱动的微服务框架，可以快速构建可以连接到外部系统的应用程序。使用Apache Kafka或RabbitMQ在Spring Boot应用程序之间发送和接收消息的简单声明性模型。

### Spring Cloud Data Stream Application

Spring Cloud Stream应用程序是开箱即用的Spring Boot应用程序，使用Spring Cloud Stream中的绑定器抽象，提供与外部中间件系统的集成，如Apache Kafka、RabbitMQ等。

### Spring Cloud Function

Spring Cloud Function促进了通过函数实现业务逻辑。它支持跨无服务器提供商的统一编程模型，以及独立运行的能力（本地或PaaS）。 

## 比较关注的项目

### Spring Cloud Bus

一个通过分布式消息传递将服务和服务实例联系起来的事件总线。适用于在集群中传播状态变化（例如，配置变更事件）。

### Spring Cloud Open Service Broker

为构建一个实现 Open Service Broker API 的服务代理提供一个起点。

### Spring Cloud Consul

使用Hashicorp Consul的服务发现和配置管理。

### Spring Cloud Sleuth

为Spring Cloud应用程序提供分布式跟踪，与Zipkin、HTrace和基于日志（如ELK）的跟踪兼容。

### Spring Cloud Connectors

使得各种平台中的PaaS应用能够轻松连接到数据库和消息代理等后端服务（该项目以前称为 "Spring Cloud"）。

## 暂时先不关注的项目

### Spring Cloud Gateway

Spring Cloud Gateway是一个基于Project Reactor的智能和可编程的路由器。

### Spring Cloud OpenFeign

Spring Cloud OpenFeign通过自动配置和与Spring环境及其他Spring编程模型成语的绑定，为Spring Boot应用提供集成。



## 不关注的项目

### Spring Cloud Cloudfoundry

将您的应用程序与Pivotal Cloud Foundry集成。提供了一个服务发现的实现，也使得实现SSO和OAuth2保护的资源变得容易。

### Spring Cloud Netflix

与各种Netflix OSS组件（Eureka、Hystrix、Zuul、Archaius等）集成。

### Spring Cloud Security

在Zuul代理中提供对负载平衡的OAuth2休息客户端和认证头转发的支持。

### Spring Cloud Starters

Spring Boot风格的启动项目，便于Spring Cloud的消费者进行依赖性管理。(在Angel.SR2之后停止作为项目，并与其他项目合并。)

### Spring Cloud CLI

Spring Boot CLI插件，用于在Groovy中快速创建Spring Cloud组件应用程序。

### Spring Cloud Task

一个短暂的微服务框架，用于快速构建执行有限数量数据处理的应用程序。简单的声明性，用于向Spring Boot应用添加功能和非功能特性。

### Spring Cloud Task App Starters

Spring Cloud Task App Starters是Spring Boot应用程序，可以是任何进程，包括Spring Batch作业，不会永远运行，它们在有限的数据处理期后结束/停止。

### Spring Cloud Zookeeper
使用Apache Zookeeper进行服务发现和配置管理。

### Spring Cloud Pipelines

Spring Cloud Pipelines提供了一个有意见的部署管道，其步骤可以确保你的应用程序可以以零停机时间的方式进行部署，并且在出现问题时可以轻松回滚。

### Spring Cloud Contract

Spring Cloud Contract是一个总括性项目，包含了帮助用户成功实施消费者驱动合同方法的解决方案。











