---
title : "工作坊介绍"
weight : 01
---

## 工作坊内容

本工作坊通过在 AWS EKS 中部署一个可注入故障的 Java 微服务商城 Demo，带您完成从数据采集到故障分析的完整可观测实践。

您将依次完成以下操作：

- 使用官方 Helm Chart 在 EKS 中安装 DataKit。
- 创建 Web RUM 应用并启用 Browser Logs、Session Replay 与 SourceMap。
- 使用公开 GHCR 镜像部署 Gateway、订单、库存、支付、MySQL 和 Redis。
- 生成商城流量并注入故障，观察 Kubernetes 指标、Trace、日志、JVM、Profiling 与 RUM 数据。
- 通过统一的 `project=mall-demo` 标签关联不同类型的观测数据。

Demo 仅用于教学演示：MySQL 和 Redis 使用临时存储，公网入口由 AWS Load Balancer 提供，不应作为生产部署模板直接使用。

## 参与贡献

如果您对贡献内容感兴趣，或者有新的内容建议及需求，可以联络 <yagrxu@amazon.com> 或 <shannatc@amazon.com>
