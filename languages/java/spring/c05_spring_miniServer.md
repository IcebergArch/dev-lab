# 微服务

将单一应用拆分为多个小型、独立部署的服务，每个服务围绕业务能力设计、独立运行、独立扩展。

## 核心要点

1️⃣ 服务注册与发现

Eureka / Consul / Zookeeper

原理：服务启动注册 → 客户端调用发现 → 健康检查

面试高频考点：客户端负载均衡 vs 服务端负载均衡

2️⃣ 配置中心

Spring Cloud Config / Nacos

配置统一管理，支持热更新、版本管理

面试点：配置优先级、分环境 profile、动态刷新

3️⃣ 服务网关

Spring Cloud Gateway / Zuul

功能：路由、限流、鉴权、负载均衡

面试点：全局过滤器 vs 局部过滤器、请求转发流程

4️⃣ 消息驱动 / 异步通信

RabbitMQ / Kafka / RocketMQ

目的：解耦服务、保证异步可靠性

面试点：事务消息、幂等设计、消息顺序保证

5️⃣ 负载均衡

Ribbon / Spring Cloud LoadBalancer

客户端负载均衡 vs 服务端负载均衡

面试点：轮询、随机、权重策略实现

6️⃣ 熔断与限流

Resilience4j / Sentinel

保护服务稳定，避免雪崩效应

面试点：熔断触发条件、熔断器状态机、重试策略

7️⃣ 分布式事务

2PC、TCC、Saga、最终一致性

面试点：何时使用本地事务？如何保证最终一致性？

## 设计模式

API Gateway：统一入口，安全与流量控制

Circuit Breaker：熔断保护

Sidecar（服务代理）：监控、日志、服务注册

Event-Driven Architecture：事件驱动解耦

CQRS + Saga：复杂业务分布式事务解决方案

## ServerLess

1. 云原生微服务
    
    Kubernetes 部署 + 弹性伸缩
    
    Service Mesh（Istio / Linkerd）
    
    Observability：Prometheus + Grafana + OpenTelemetry

2. Serverless 微服务
    
    函数化部署，按调用计费
    
    Spring Cloud Function 集成

3. Reactive / 高吞吐微服务

    WebFlux + Reactor

    异步非阻塞处理，提升响应能力

4. AI 与微服务结合

    后端智能推理服务

    LLM API 集成模式