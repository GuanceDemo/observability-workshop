---
title : "在 AWS EKS 配置 Datakit"
weight : 22
---

## 在 AWS EKS 配置 Datakit

请按照以下步骤在 Amazon EKS 中完成 Datakit 与 TrueWatch 的集成。所有命令都在 AWS CloudShell 终端中执行。

### 步骤一：克隆 Workshop 仓库

打开 AWS CloudShell，并在终端中执行以下命令：

```shell
git clone https://github.com/TrueWatchTech/idurar-demo-workshop
```

### 步骤二：获取 Dataway 端点信息

登录 TrueWatch Cloud，点击 Integration → Datakit → Kubernetes，复制 ENV_DATAWAY 的值：

![01](/static/static-22/01.png)

### 步骤三：更新 Datakit 配置文件

将刚才复制的 ENV_DATAWAY 值替换 datakit.yaml（第 166 行）中的 YOUR-ENV-DATAWAY：

```
cd idurar-demo-workshop/workshop
vim datakit.yaml  # Use ":set nu" to show line numbers
```

![02](/static/static-22/02.png)

### 步骤四：更新 Kubernetes 配置

执行以下命令，更新 Kubernetes 配置至指定的 EKS 集群：

```
aws eks --region ap-southeast-1 update-kubeconfig --name eks-demo-cluster
```

![03](/static/static-22/03.png)

### 步骤五：将 Datakit 部署至 Kubernetes

执行以下命令部署并验证 Datakit 状态：

```
# Deploy Datakit
kubectl apply -f datakit.yaml

# Check deployment status
kubectl get pods -n datakit
```

### 步骤六：在 TrueWatch 验证 Datakit 集成状态

数分钟后，验证数据是否正确传送至 TrueWatch：

1. 点击 Infrastructure 查看 EKS 集群节点信息。
	![04](/static/static-22/04.png)
2. 前往 Containers → Pods 查看 Pod 基本信息与性能指标。
	![05](/static/static-22/05.png)
3. 点击 Analysis Dashboard 查看更详细的分析仪表板。
	![06](/static/static-22/06.png)
4. 检查 Network → Map 查看网络拓扑和流量，下图展示 EKS 节点与 TrueWatch 之间的数据传送状况：
	![07](/static/static-22/07.png)
5. 在 Logs 下查看与 kube-proxy 组件和集群事件相关的日志。
	![08](/static/static-22/08.png)
