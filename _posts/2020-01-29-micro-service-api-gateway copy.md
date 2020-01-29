---
layout: article
title: 微服务 — API Gateway
tags: 微服务

lang: zh-Hans
key: Micro_Service_API_Gateway
pageview: true
toc: true
show_subscribe: false
---

## 什么是 API Gateway？

API Gateway 是微服务架构体系中的一类型特殊服务，它是所有微服务的入口，它的职责是执行路由请求、协议转换、聚合数据、认证、限流、熔断等。

一个系统可以有一个或多个 API Gateway，例如

<div align="center"> <img src="https://s2.ax1x.com/2020/01/29/1QHXDg.png" width="100%"/> </div>

## Gateway 的指责

### 1. 请求路由和版本控制

API Gateway 是微服务的入口，可以根据不同的请求路由到不同的 microservice 上，也可以在 Gateway 上进行路由的版本控制，这样即使后端 microservice 发生了变化，Gateway 的路径依然可以不改变。

### 2. 方便单体应用到微服务的过渡

借助 Gateway 你可以很方便的在 Gateway 之后部署不同的服务，也就是可以同时存在两种不同的服务，这样可以帮助单体应用向微服务渐进式的演化。

### 3. 权限认证

不同的服务之间常常有一些共有的逻辑需要处理，比如权限认证，由于 Gateway 恰巧在各个微服务的前端，可以在 Gateway 实现权限的认证，比如可以在 Gateway 层实现 cookie 或 token 的认证。

### 4. 数据聚合

由于不同的客户端往往需要的数据完全不同，而这些数据又是不同的 service 提供的，借助 Gateway 我们可以方便完成来自不同 service 的数据聚合，以达到根据不同 client 进行数据定制的目的。

### 5. 协议转换

鉴于不同的团队采用的数据协议可能完全不同，Gateway 可以根据场景需要对不同的微服务之间进行协议转换。

### 6. 缓存和限流

Gateway 可以做限流和缓存

## 参考

[微服务 API Gateway 介绍](https://sanyuesha.com/2018/03/24/microservices-api-gateway/)

[一个简单可参考的 API 网关架构设计](https://www.infoq.cn/article/api-gateway-architecture-design)

[有赞API网关实践](https://tech.youzan.com/api-gateway-in-practice/)

[京东10亿级调用量背后的高可用网关系统架构实践](http://www.yunweipai.com/archives/23653.html)