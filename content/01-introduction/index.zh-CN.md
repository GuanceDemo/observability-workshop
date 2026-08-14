---
title : "观测云可观测性工作坊简介"
weight : 10
---

## 观测云可观测性工作坊简介

本工作坊将通过在 AWS EKS（Elastic Kubernetes Service）中部署并监控一个可注入故障的微服务商城 Demo，介绍观测云可观测性平台的实际应用。目标是为学员提供完整的实操体验，涵盖 Kubernetes 与容器指标、应用性能监控、日志分析、JVM 监控、Profiling、用户体验监控、告警管理及端到端链路追踪等核心功能。

### 关于微服务商城 Demo

该 Demo 是一个面向可观测性教学场景设计的 Java 微服务商城应用，通过完整的下单链路展示不同服务和依赖之间的调用关系。其主要特点包括：

- 采用 **Spring Boot** 构建 Gateway、订单、库存和支付四个 Java 微服务
- 使用 **MySQL** 保存订单数据，并通过 **Redis** 模拟库存缓存
- 支持通过 **Helm** 部署到 AWS EKS 等标准 Kubernetes 环境
- 内置前端、服务、JVM 和依赖层故障场景，可用于演示指标、日志、Trace、Profiling、RUM、Browser Logs 和 Session Replay 的关联分析

本工作坊将带领您在 AWS EKS 集群中安装 DataKit、部署微服务商城 Demo、生成业务流量、注入故障，并通过观测云完成从用户访问到后端服务及基础设施的全链路观测与问题分析。

### 观测云可观测性平台功能特色

观测云是全面的可观测性解决方案，提供应用性能、基础设施健康度、用户行为监控（RUM）、端到端追踪、日志分析及告警管理等多样化功能。主要特色包括：

- **一站式监控**：整合指标、日志与追踪于单一平台。
- **交互式仪表板**：快速可视化观测数据。
- **端到端追踪**：完整追踪应用程序前端至后端的性能。
- **实时用户体验分析**：分析用户行为与性能表现。
- **全面的日志管理**：集中式日志接收与解析。
- **综合监控**：主动式服务可用性监控。
- **自定义告警**：强大的告警规则与通知功能。
- **AI 错误分析**：通过 AI 自动诊断与根本原因分析。

### 工作坊环境设置

工作坊将带领参与者完成以下实操步骤：

- 连接 AWS EKS，并通过官方 Helm Chart 安装 DataKit。
- 创建 Web RUM 应用，并通过节点 DataKit 接收 RUM、Browser Logs 与 Session Replay 数据。
- 使用公开 Harbor 固定版本镜像部署微服务商城，无需在 Workshop 环境中构建镜像。
- 通过 `project=mall-demo` 关联 Kubernetes 指标、APM、日志、JVM、Profiling 与 RUM 数据。
- 生成业务流量、注入可恢复的故障，并配置告警与关联分析流程。

### 准备步骤

开始前请确保已完成以下准备：

1. **观测云 Cloud 账号：**
   - 注册 [https://auth.guance.com/businessRegister](https://auth.guance.com/businessRegister) 并创建工作区 (Workspace)，默认为商业版。
2. **AWS EKS 集群：**
   - 集群至少包含一个状态为 `Ready` 的工作节点，并允许创建 `LoadBalancer` 类型的 Service。
3. **AWS CloudShell 权限：**
   - 当前身份需要能够读取 EKS 集群信息，并在集群中创建 Namespace、Secret、Service、Deployment、DaemonSet、Role 与 RoleBinding。

**注意事项：**  
观测云通过「工作区」将不同环境或应用的观测数据分离，多个环境间可通过授权方式共享数据。DataWay URL 中包含敏感 token，请勿将其写入文档、截图或 Git 仓库。
