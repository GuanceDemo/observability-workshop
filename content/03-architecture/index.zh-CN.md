---
title : "部署架构图"
weight : 30
---

## 架构概述

iDURAR 是一套开源的客户关系管理（CRM）系统，具备现代化的前后端分离架构。本工作坊将展示如何在 AWS EKS 环境中，利用 TrueWatch 平台为此应用程序构建完整的可观测性（Observability）解决方案。

## 部署架构图

![01](/static/static-30/01.png)

## 组件说明

本系统包含以下组件：

- **AWS 应用负载均衡器（ALB）：** 负责将终端用户通过浏览器的请求路由至应用程序。
- **iDURAR 前端应用：** 基于 React 开发的用户界面，并集成 TrueWatch RUM SDK 以实现真实用户监控。
- **iDURAR 后端应用：** 基于 Node.js 与 Express 开发的后端服务，处理业务逻辑。
- **MongoDB 数据库：** 提供数据持久化存储功能。
- **Datakit 采集代理：** 以 DaemonSet 模式部署于 EKS 节点上，负责采集系统的指标、日志及追踪数据。
- **TrueWatch 平台：** 提供云端可观测性服务，包含性能指标、日志分析、追踪、告警与分析功能。
