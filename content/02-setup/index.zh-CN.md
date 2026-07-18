---
title : "环境搭建"
weight : 20
---

## 环境搭建

本章完成从零到可观测的基础环境配置：

1. 声明 EKS 与 DataWay 参数，并使用 Helm 安装 DataKit。
2. 在 TrueWatch 创建 Web RUM 应用并记录 Application ID 和 Workspace ID。
3. 使用公开镜像将微服务商城部署到 EKS，并取得公网访问 URL。

需要用户填写的参数为 DataWay URL、RUM Application ID 和 TrueWatch Workspace ID。`project=mall-demo`、镜像标签 `latest`、Namespace 以及 Demo 内部密码均由教程固定或自动生成。
