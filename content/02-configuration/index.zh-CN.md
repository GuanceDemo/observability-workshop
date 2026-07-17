---
title : "配置"
weight : 20
---

## 配置

本工作坊按以下顺序完成配置与验证：

1. 了解本次实验将使用的 TrueWatch 功能。
2. 声明 EKS 与 DataWay 参数，并使用 Helm 安装 DataKit。
3. 在 TrueWatch 创建 Web RUM 应用并记录 Application ID 和 Workspace ID。
4. 使用公开镜像将微服务商城部署到 EKS，并取得公网访问 URL。
5. 生成业务流量并验证 RUM、端到端追踪、日志和 Profiling。
6. 创建日志 Pipeline 并验证结构化字段。
7. 创建监控器和 SLO，并导入产品服务运行质量大屏。
8. 在 EKS 节点部署 Agent Runtime，使用 Toby AI 和 Agent Teams 分析 Trace 与告警，完成故障处理闭环并清理 Workshop 资源。

需要用户填写的参数为 AWS Region、EKS 集群名、DataWay URL、RUM Application ID 和 TrueWatch Workspace ID。`project=mall-demo`、镜像标签 `latest`、Namespace 以及 Demo 内部密码均由教程固定或自动生成。
