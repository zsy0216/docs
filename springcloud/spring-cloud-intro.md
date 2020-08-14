# SpringCloud 介绍

SpringCloud 是解决分布式架构的一站式解决方案，是一系列技术的综合体现（比如配置管理、服务发现、服务调用、断路器、路由、微代理，控制总线，一次性令牌，全局锁，领导者选举，分布式会话，集群状态等）

![image-20200813160631653](https://gitee.com/zsy0216/typora-image/raw/master/typora/image-20200813160631653.png)

## 服务注册与发现(Service registration and discovery)

- Eureka

- Zookeeper

- Consul

- Nacos

## 负载均衡/服务调用

- 负载均衡：Ribbon (服务调用-RestTemplate)
- 服务调用：Feign/OpenFeign (负载均衡-Ribbon)