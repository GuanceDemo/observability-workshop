---
title : "部署示范应用"
weight : 24
---

## 部署示范应用

本章使用公开 GHCR 镜像将商城 Demo 部署到 AWS EKS，无需在 CloudShell 中构建镜像或配置 ECR。除 Gateway 外，订单、库存、支付、MySQL 和 Redis 都只在集群内部访问。

### 步骤一：使用 Helm 部署商城 Demo

执行以下命令安装应用：

```shell
helm upgrade --install demo charts/observability-demo \
  --namespace observability-demo \
  --create-namespace \
  -f charts/observability-demo/values-eks.yaml \
  --set rum.enabled=true \
  --set-string rum.applicationId="$RUM_APPLICATION_ID" \
  --set-string observabilityConsole.workspaceId="$TRUEWATCH_WORKSPACE_ID"

unset RUM_APPLICATION_ID TRUEWATCH_WORKSPACE_ID
```

![01](/static/static-24/01.png)

### 步骤二：等待工作负载就绪

```shell
kubectl wait --for=condition=Available deployment --all \
  --namespace observability-demo \
  --timeout=8m

kubectl -n observability-demo get pods
```

![02](/static/static-24/02.png)

正常情况下会看到 Gateway、order、inventory、payment、MySQL 和 Redis 六个工作负载。Java Pod 以非 root 用户运行，并通过节点 IP 将 Trace、JVM 指标和 Profiling 数据发送到 DataKit。

### 步骤三：获取公网访问 URL

AWS 创建 Load Balancer 通常需要几分钟。执行以下命令等待 Gateway 的公网地址：

```shell
GATEWAY_HOST=""
until [ -n "$GATEWAY_HOST" ]; do
  GATEWAY_HOST="$(kubectl -n observability-demo get service \
    -l app.kubernetes.io/component=gateway-service \
    -o jsonpath='{.items[0].status.loadBalancer.ingress[0].hostname}')"
  [ -n "$GATEWAY_HOST" ] || sleep 10
done

kubectl -n observability-demo get service \
  -l app.kubernetes.io/component=gateway-service
```

![03](/static/static-24/03.png)

这是 AWS 自动分配的公网 DNS，无需提前准备自有域名。Load Balancer 会产生 AWS 费用，Workshop 结束后请按本章末尾的命令清理。

### 步骤四：打开商城并验证部署

在浏览器中打开 `EXTERNAL-IP`。商城页面无需登录，支持中文与英文切换。首次进行故障操作时，页面会提示输入故障控制口令，该值只保存在当前浏览器的 `sessionStorage`。

在 CloudShell 中取得口令并执行自动验证：

```shell
printf '%s\n' "$(kubectl -n observability-demo get secret demo-observability-demo \
  -o jsonpath='{.data.demo-control-token}' | base64 --decode)"
```

![04](/static/static-24/04.png)
