---
title : "在 AWS EKS 配置 DataKit"
weight : 21
---

## 在 AWS EKS 配置 DataKit

请按照以下步骤在 Amazon EKS 中完成 DataKit 与观测云的集成。除观测云控制台操作外，本章命令均在同一个 AWS CloudShell 终端中执行。

### 步骤一：准备信息并声明参数

开始前准备以下信息：

- EKS 集群名称。
- 观测云工作区提供的 DataWay URL。

登录[观测云控制台](https://console.guance.com/)，进入 **集成 → DataKit → Kubernetes(Helm)**，复制 `datakit.dataway_url` 的完整值：

![01](/static/static-22/01.png)

打开 AWS CloudShell，先集中声明本章使用的参数。DataWay URL 使用隐藏输入，不会出现在 Shell 历史中：

```shell
export EKS_CLUSTER_NAME="eks-demo-cluster"
read -rsp 'DataWay URL: ' DATAWAY_URL && export DATAWAY_URL && echo
```

本 Demo 固定使用 `project=mall-demo`、DataKit Namespace `datakit` 和应用 Namespace `observability-demo`，无需额外声明。DataWay URL 包含敏感 token，请勿直接写入 YAML、文档或 Git 仓库。

### 步骤二：确认命令行工具

AWS CloudShell 通常已提供 AWS CLI 与 `kubectl`。先检查 Helm：

```shell
helm version --short
```

如果提示找不到 `helm`，将 Helm 安装到 CloudShell 的持久化目录 `$HOME/bin`：

```shell
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
mkdir -p "$HOME/bin"
HELM_INSTALL_DIR="$HOME/bin" USE_SUDO=false ./get_helm.sh
rm -f get_helm.sh
export PATH="$HOME/bin:$PATH"
grep -qxF 'export PATH="$HOME/bin:$PATH"' "$HOME/.bashrc" || \
  echo 'export PATH="$HOME/bin:$PATH"' >> "$HOME/.bashrc"
helm version --short
```

这样重新打开 CloudShell 后仍可直接使用 Helm。

### 步骤三：连接目标 EKS 集群

使用第一步声明的集群名称生成当前 CloudShell 的 kubeconfig。AWS CloudShell 会自动提供当前区域：

```shell
aws eks update-kubeconfig \
  --name "$EKS_CLUSTER_NAME"

kubectl config current-context
kubectl get nodes
```

![02](/static/static-22/02.png)

当 `kubectl get nodes` 能显示至少一个状态为 `Ready` 的节点时，说明连接成功。如果当前上下文已经指向目标集群，可以跳过 `aws eks update-kubeconfig`，但仍应执行以上两条检查命令。

### 步骤四：克隆 Demo 仓库

```shell
export DEMO_VERSION="2.3.5"
git clone --branch "v${DEMO_VERSION}" --depth 1 \
  https://github.com/GuanceDemo/observability-demo.git
cd observability-demo
```

后续所有 Helm 和脚本命令都在仓库根目录执行。

### 步骤五：使用 Helm 安装 DataKit

添加官方 DataKit Chart 仓库，并安装固定版本 `2.5.0`：

```shell
helm repo add datakit https://pubrepo.guance.com/chartrepo/datakit
helm repo update

helm upgrade --install datakit datakit/datakit \
  --version 2.5.0 \
  --namespace datakit \
  --create-namespace \
  -f observability/datakit-values.example.yaml \
  --set-string datakit.dataway_url="$DATAWAY_URL" \
  --set-string datakit.cluster_name_k8s="$EKS_CLUSTER_NAME"

unset DATAWAY_URL
```

仓库中的 values 会开启 Kubernetes/容器指标、DDTrace、JVM StatsD、Profiling 与 RUM。真实 DataWay URL 由 Chart 写入 Kubernetes Secret，不会保存在仓库文件中。

检查 DaemonSet 与 Pod 状态：

```shell
kubectl get pods -n datakit
kubectl logs -n datakit daemonset/datakit --tail=200 | grep 'add input'
```

![03](/static/static-22/03.png)

### 步骤六：在观测云验证 DataKit 集成状态

数分钟后，验证数据是否正确传送至观测云：

1. 进入 **基础设施**，查看 EKS 集群节点信息。
	![04](/static/static-22/04.png)
2. 前往 **容器 → Pods**，查看 DataKit Pod 的基本信息与性能指标。
	![05](/static/static-22/05.png)
3. 点击 **分析看板**，查看更详细的 Kubernetes 分析仪表板。
	![06](/static/static-22/06.png)
4. 在 **日志** 中查看 Kubernetes 组件日志与集群事件。
	![07](/static/static-22/07.png)

此时商城应用尚未部署，因此 APM、应用日志、JVM、Profiling 与 RUM 数据会在后续章节完成应用安装和流量生成后出现。
