---
title : "Toby AI 与 Agent Teams 能力演示"
weight : 41
---

## Toby AI 与 Agent Teams 能力演示

本章基于前一章产生的支付异常和监控告警，演示 Toby AI 对错误 Trace 的分析能力，以及 Agent Teams 接收告警、访问 EKS 并生成处置建议的能力。

Toby AI 可以直接在平台中使用；Agent Teams 的 Agent Runtime 需要由用户部署。本 Workshop 将 Runtime 临时安装到一台 EKS EC2 工作节点，演示结束后再统一清理。

### 步骤一：创建 Workshop Agent

点击平台顶部的 **Toby AI → Toby Agent Teams**，进入 Agent Workspace 并创建一个专用于本次 Workshop 的 Agent：

- Quick Start：`Observability Navigator`

进入该 Agent 的 **Run & Deploy** 页面，选择 Linux 安装方式，并从专属安装命令中确认以下内容：

- Agent ID
- Agent API Key
- Beak Endpoint

![01](/static/static-28/01.png)

### 步骤二：在 EKS 节点部署 Agent Runtime

安装脚本适用于带 EC2 工作节点的 EKS 集群，节点需要使用支持 `systemd` 的 Amazon Linux 或 Ubuntu。Fargate-only、Bottlerocket 以及已经安装 `obs-agent` 的节点不使用本步骤。

本步骤使用一个短生命周期的特权 helper Pod 进入目标节点并安装 Runtime，不修改 EC2 Node Role，也不依赖 SSM。当前 AWS 身份需要能够访问 EKS，并在集群中创建 privileged、hostPath 和 hostPID Pod。该方式只应在本次 Workshop 的专用集群中执行。

在 CloudShell 中确认所需命令可用：

```shell
aws --version
kubectl version --client
```

继续使用第二章声明的 EKS 参数，并填写 Agent 安装命令中的 Beak Endpoint：

```shell
export BEAK_ENDPOINT="https://agent-api.truewatch.com"
```

执行仓库中的安装脚本：

```shell
scripts/install-obs-agent-eks-node-demo.sh
```

脚本会自动完成以下操作：

1. 选择目标集群中的一台 Ready Linux 工作节点。
2. 创建一个短生命周期的特权 helper Pod，并挂载目标节点根文件系统。
3. 创建 Kubernetes 只读 ServiceAccount、RBAC 和默认有效期为 8 小时的 kubeconfig。
4. 在节点上安装与 EKS 版本匹配的 `kubectl`。
5. 使用 `Standard` 权限模式安装 Agent，并打开加密的 Kubernetes exec 交互会话。
6. 安装完成或脚本退出时自动删除 helper Pod。

在交互会话中依次输入 Agent ID 和 Agent API Key。API Key 会隐藏输入，不会进入 Shell 历史、Pod 定义或仓库。

![02](/static/static-28/02.png)

如果脚本自动选择的节点不符合要求，可以在重新安装前指定：

```shell
export TARGET_INSTANCE_ID="i-xxxxxxxxxxxxxxxxx"
scripts/install-obs-agent-eks-node-demo.sh
```

### 步骤三：验证 Agent Runtime

安装完成后回到 Agent Workspace，等待 `Observability Navigator` 状态变为 **Online**。

创建一个只读验证任务：

```text
请列出当前 EKS 集群的节点、命名空间和 observability-demo 命名空间中的 Pod。只读取数据，不执行任何修改。
```

确认 Agent 能够列出节点和 Pod。

### 步骤四：准备支付异常与告警事件

在商城 Demo 中选择 **Backend → Payment 5xx error → Inject selected fault**，连续提交多笔订单，等待第七章创建的错误率监控器触发告警。

进入 **APM → Traces**，使用以下条件查找本次异常 Trace：

- `project=mall-demo`
- `service=payment-service`
- `status=error`

打开一条包含 payment-service 错误 Span 的 Trace，确认 Gateway 返回 HTTP 503，并记录该 Trace ID。

### 步骤五：使用 Toby AI 分析错误 Trace

在错误 Trace 详情页打开 **Toby AI**，选择分析当前 Trace，并输入：

```text
分析当前错误 Trace，说明故障发生在哪个服务、上下游影响、关键证据、可能根因和建议的处理顺序。
```

检查分析结果是否包含以下内容：

1. 定位 payment-service 的错误 Span。
2. 说明错误如何沿 `payment → order → gateway` 传播。
3. 使用 HTTP 状态码、错误消息、Trace ID 和关联日志作为证据。
4. 区分 Demo 主动注入的故障与真实外部依赖异常。
5. 给出立即处理、后续调查和持续监控建议。

可以继续追问：

```text
使用相同 trace_id 关联日志，确认 payment-service 的错误原因，并将分析结果整理成一份 Note。
```

![03](/static/static-28/03.png)

### 步骤六：使用 Agent Teams 处理告警任务

首先进入 **Monitoring → Alert Strategies**，新建或编辑本 Workshop 使用的告警策略：

1. 在 **关联** 中加入第七章创建的服务错误率、HTTP 状态码错误率和响应时间监控器。
2. 展开 **通知规则**，将时区设置为 `(UTC+09:00) Asia/Tokyo`。
3. 选择 **按等级（Level）** 通知，并为 `Fatal` 等级选择通知对象 `Observability Navigator`。如果监控器使用其他事件等级，请为对应等级增加相同的 Agent 通知规则。
4. 点击 **保存**。只有匹配通知等级的事件才会投递给 Agent。

如果通知对象列表中没有 `Observability Navigator`，请先确认该 Agent 已在当前工作空间创建，并且 Runtime 状态为 **Online**。

![04](/static/static-28/04.png)

进入 Agent Workspace，选择已经部署并处于 Online 状态的 `Observability Navigator`，随后进入 **Task Intake**，创建或检查**Workspace hook**：

- Name：`Observability Navigator Event Webhook`
- Intake type：`Workspace`
- Trigger method：`Platform delivery`
- Execution period：`1 minute window`
- Permission mode：使用当前 Workshop 工作空间权限
- Status：`Active`

![05](/static/static-28/05.png)

将第七章监控器的告警策略投递到该任务入口，再次触发支付错误。确认 **Received messages** 数量增加，并在运行记录或 **My Tasks** 中打开新任务，检查 Agent 是否自动完成事件、Trace、日志和 EKS 资源的关联分析。

### 步骤七：关闭故障并验证闭环

返回商城 Demo，关闭当前故障并继续提交正常订单。等待下一个监控检测周期后，确认：

1. 错误率监控器恢复正常。
2. 告警事件进入恢复状态。
3. Agent 任务保留了本次异常的分析结论和处理建议。

可以在 Toby AI 中输入以下内容，生成本次演示的复盘记录：

```text
将本次 payment-service 告警、影响范围、根因证据、处置过程和恢复验证整理成一份复盘 Note。
```

至此完成从故障注入、监控告警、AI 分析、Agent 处理到恢复验证的闭环演示。

### Workshop 结束后清理

先在 EKS 节点仍处于运行状态时清理 Agent Runtime。该命令会通过临时 helper Pod 删除节点上的 Agent 与 Owl 凭证及临时 Kubernetes RBAC，并在结束时删除 helper Pod：

```shell
scripts/install-obs-agent-eks-node-demo.sh --cleanup
unset BEAK_ENDPOINT TARGET_INSTANCE_ID TARGET_NODE_NAME
```

确认清理完成后，在 Agent Workspace 中删除本 Workshop 创建的 Agent 和 Task Intake。

然后在 CloudShell 中卸载应用：

```shell
kubectl config current-context
helm list --all-namespaces

helm uninstall demo -n observability-demo
kubectl delete namespace observability-demo --ignore-not-found
```

等待 Gateway Service 删除后，在 AWS 控制台确认本 Workshop 创建的 Load Balancer 已被释放，避免继续产生费用。

仅当该 EKS 集群不再需要 DataKit 采集时，再执行：

```shell
helm uninstall datakit -n datakit
kubectl delete namespace datakit --ignore-not-found
```
