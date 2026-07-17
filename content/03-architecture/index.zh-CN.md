---
title : "部署架构图"
weight : 30
---

## 架构概述

本工作坊使用一个面向可观测性教学的 Java 微服务商城。浏览器请求首先进入 Gateway，再由订单服务调用 MySQL、库存服务、Redis 和支付服务。DataKit 以 DaemonSet 运行在每个 EKS 节点上，将基础设施、APM、日志、JVM、Profiling 与 RUM 数据发送到 TrueWatch。

## 部署架构图

![01](/static/static-30/01.png)

## 组件说明

本系统包含以下组件：

- **AWS Load Balancer：** 由 `LoadBalancer` 类型的 Gateway Service 自动创建，为浏览器提供公网 DNS；具体类型由 EKS 集群的负载均衡控制器决定。
- **Gateway Service：** 商城唯一的公网入口，转发业务与 Demo API，并拒绝外部访问内部 `/admin/**` 接口。
- **Order Service：** 承载双语商城页面、订单 API、RUM 代理、日志查看器和故障控制入口。
- **Inventory Service：** 处理库存业务，并通过 Redis 模拟缓存依赖。
- **Payment Service：** 处理支付调用，提供慢调用、错误和 CPU 热点等演示场景。
- **MySQL 与 Redis：** 使用 `emptyDir` 临时卷，仅保存 Workshop 期间的 Demo 数据，不提供生产级持久化或高可用。
- **DataKit：** 通过独立 Helm Release 以 DaemonSet 部署，采集 Kubernetes/容器指标、应用日志、Trace、JVM StatsD、Profiling 与 RUM。
- **TrueWatch：** 接收 DataKit 上报的数据，提供 Explorer、Dashboard、APM、日志、RUM、告警与 AI 分析能力。

应用与 DataKit 使用两个独立 Helm Release：`demo` 位于 `observability-demo` Namespace，`datakit` 位于 `datakit` Namespace。所有 Demo 信号统一设置 `project=mall-demo`，便于跨数据类型关联分析。

## 主要数据流

1. 浏览器通过 AWS Load Balancer 访问 Gateway。
2. 正常下单依次经过 `gateway → order → MySQL → inventory → Redis → payment`。
3. Java Agent 将 Trace 与 Profiling 发送到节点 `9529` 端口，将 JVM StatsD 指标发送到节点 `8125` 端口。
4. Java Pod 通过 Annotation 将容器日志交给 DataKit，并使用 `java-selfheal-demo.p` Pipeline 解析关联字段。
5. 浏览器通过同源 `/rum-proxy` 将 RUM 数据转发到节点 DataKit，不在前端保存 DataWay token 或 Public DataWay client token。
6. DataKit 使用安装时提供的 DataWay URL，将所有观测数据发送到 TrueWatch 工作区。
