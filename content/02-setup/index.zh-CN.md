---
title : "环境搭建"
weight : 20
---

## 环境搭建

本章完成从零到可观测的基础环境配置：

1. 声明 EKS 与 DataWay 参数，并使用 Helm 安装 DataKit。
2. 在观测云创建 Web RUM 应用并记录 Application ID 和 Workspace ID。
3. 使用公开镜像将微服务商城部署到 EKS，并取得公网访问 URL。

需要用户填写的参数为 DataWay URL、RUM Application ID 和观测云 Workspace ID。`project=mall-demo`、镜像标签 `2.3.5`、Namespace 以及 Demo 内部密码均由教程固定或自动生成。

### 快速搭建（可选）

如果已经有包含 Ready EC2 工作节点的 EKS 集群，可以使用快速路径完成 DataKit 和 Demo 安装。现有分步教程继续保留，用于了解配置细节和排查问题。

#### 1. 准备平台参数

开始前请在[观测云控制台](https://console.guance.com/)完成以下准备：

1. 进入 **集成 → DataKit → Kubernetes(Helm)**，复制 `datakit.dataway_url` 的完整值。
2. 进入 **用户访问监测 → 应用列表 → 新建应用**，创建 Web RUM 应用，复制 **Application ID** 和当前工作空间的 **Workspace ID**。

打开 AWS CloudShell，集中声明快速安装所需参数。CloudShell 会自动提供当前区域。DataWay URL 包含敏感 token，使用隐藏输入可避免它进入 Shell 历史：

```shell
export EKS_CLUSTER_NAME="eks-demo-cluster"
read -rsp 'DataWay URL: ' DATAWAY_URL && export DATAWAY_URL && echo
read -rp 'RUM Application ID: ' RUM_APPLICATION_ID && export RUM_APPLICATION_ID
read -rp '观测云 Workspace ID: ' GUANCE_WORKSPACE_ID && export GUANCE_WORKSPACE_ID
```

#### 2. 执行快速安装

克隆 Guance 官方固定版本，并在仓库根目录运行安装命令：

```shell
export DEMO_VERSION="2.3.5"
git clone --branch "v${DEMO_VERSION}" --depth 1 \
  https://github.com/GuanceDemo/observability-demo.git
cd observability-demo

scripts/workshop.sh install
```

脚本会自动安装并校验 Helm `v3.17.3`（仅在 Helm 缺失时），显示 AWS 身份、目标 Kubernetes context 和节点，确认后安装 DataKit `2.5.0` 与六个 Demo 工作负载，并等待 Gateway 获得公网 hostname 或 IP。自动化环境可使用 `scripts/workshop.sh install --yes` 跳过确认。

如果已经存在 `datakit/datakit` Helm Release，脚本会默认停止，避免覆盖共享配置。请改用本章后续的分步教程；只有确认允许升级现有 DataKit 时，才显式执行 `scripts/workshop.sh install --upgrade-existing-datakit`。

安装成功后，脚本会输出 `DEMO_BASE_URL` 和 SourceMap 的版本提示。它不会运行 smoke test、生成流量或注入故障。请清理当前 Shell 中的参数：

```shell
unset DATAWAY_URL RUM_APPLICATION_ID GUANCE_WORKSPACE_ID
```

接下来完成 **创建 RUM 客户端应用 → 步骤三：准备 SourceMap 文件**，然后进入第三章继续观测和故障演示。需要了解完整部署过程或排查安装问题时，继续阅读本章后续的分步教程。
