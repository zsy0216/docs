Spring Cloud Netflix

# 1. 微服务框架的4个核心问题

1. 服务很多，客户端怎么访问？负载均衡
2. 服务之间如何进行通信？Http、RPC
3. 如何治理？注册中心
4. 服务挂了怎么办？容灾机制

# 解决方案

Spring Cloud 生态！

## Spring Cloud NetFlix : 一站式解决方案

1. zuul 组件 ，api网关；
2. Feign：基于HttpClient，即Http通信方式，同步，阻塞；
3. Eureka：服务注册与发现；
4. Hystrix：熔断机制；

## Apache Dubbo zookeeper

半自动，需要整合别的组件

1. Api，没有，使用第三方组件，或自己实现；
2. Dubbo，RPC
3. Zookeeper：注册中心
4. 没有熔断机制，借助Hystrix

## Spring Cloud alibaba

新一代一站式解决方案



### 微服务新概念：服务网格 Server Mesh

istio

### 根本：

1. API，解决服务路由
2. HTTP、RPC，解决服务通信
3. 服务注册与发现，实现服务高可用
4. 熔断机制

# 2. Spring Cloud入门概述

## Spring Cloud 是什么？

Spring官网：https://spring.io/

![](https://gitee.com/Ep_tassel/typora-image/raw/master/typora/202003/29/160303-245633.png)

SpringCloud 基于SpringBoot提供了一套微服务解决方案，包括服务注册与发现，配置中心，全链路监控，服务网关，负载均衡，熔断器等组件，除了基于NetFilx的开源组件做高度抽象封装之外，还有一些选型中立的开源组件。

SpringCloud 利用 SpringBoot 的开发便利性，巧妙地简化了分布式系统基础设施的开发，SpringCloud为开发人员提供了快速构建分布式系统的一些工具，包括配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等，他们都可以用 SpringBoot 的开发风格做到一键启动和部署。

SpringBoot 并没有重复造轮子，它只是将目前各家公司开发的比较成熟，经得起实际考研的服务框架组合起来，通过SpringBoot风格进行再封装，屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂，易部署和易维护的分布式系统开发工具包

SpringCloud 是分布式微服务架构下的一站式解决方案，是各个微服务架构落地技术的集合体，简称微服务全家桶。

## SpringCloud 和 Springboot 的关系

- SpringBoot 专注于快速方便地开发单个个体微服务。
- SpringCloud 是关注全局的微服务协调整理治理框架，它将 SpringBoot 开发的一个个单体微服务整合并管理起来，为各个微服务之间提供：配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等集成服务。
- SpringBoot 可以离开 SpringCloud 独立使用，开发项目，但是 SpringCloud 离不开 SpringBoot，属于依赖关系。
- SpringBoot 专注于快速、方便地开发单个个体微服务，SpringCloud 专注于全局的服务治理框架。

## Dubbo 和 SpringCloud 技术选型

### 1. 分布式+服务治理 Dubbo

目前成熟的互联网架构：应用服务化拆分 + 消息中间件