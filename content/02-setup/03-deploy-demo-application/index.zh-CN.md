---
title : "部署示范应用"
weight : 23
---

## 部署示范应用

本章使用公开 Harbor `2.3.1` 镜像将商城 Demo 部署到 AWS EKS，无需在 CloudShell 中构建镜像、配置 ECR 或登录镜像仓库。除 Gateway 外，订单、库存、支付、MySQL 和 Redis 都只在集群内部访问。

Gateway 会暴露到公网，Demo 故障接口有意不要求控制凭证。该 profile 只能用于隔离、短期的 Workshop 集群，练习结束后应及时清理。

### 步骤一：使用 Helm 部署商城 Demo

使用固定版本的观测云 EKS profile：

```shell
helm upgrade --install demo charts/observability-demo \
  --namespace observability-demo \
  --create-namespace \
  -f charts/observability-demo/values-eks.yaml \
  --set-string rum.enabled=true \
  --set-string rum.applicationId="$RUM_APPLICATION_ID" \
  --set-string observability.clusterName="$EKS_CLUSTER_NAME" \
  --set-string observabilityConsole.url="https://console.guance.com/" \
  --set-string observabilityConsole.workspaceId="$GUANCE_WORKSPACE_ID"

unset RUM_APPLICATION_ID GUANCE_WORKSPACE_ID
```

该 profile 使用 `IfNotPresent` 拉取 `pubrepo.jiagouyun.com/demo/observability-demo-{gateway,order,inventory,payment}-service:2.3.1`。镜像公开并同时支持 `linux/amd64` 和 `linux/arm64`。

### 步骤二：等待工作负载就绪

```shell
kubectl wait --for=condition=Available deployment --all \
  --namespace observability-demo \
  --timeout=8m

kubectl -n observability-demo get pods
```

正常情况下会看到 Gateway、order、inventory、payment、MySQL 和 Redis 六个工作负载。Java Pod 以非 root 用户运行，并通过节点 IP 将 Trace、JVM 指标和 Profiling 数据发送到 DataKit。应用与 DataKit 使用同一个 `EKS_CLUSTER_NAME` 完成关联。

### 步骤三：获取公网访问 URL

在十分钟内等待 LoadBalancer hostname 或 IP：

```shell
deadline=$((SECONDS + 600))
GATEWAY_ADDRESS=""
while [[ -z "$GATEWAY_ADDRESS" && "$SECONDS" -lt "$deadline" ]]; do
  GATEWAY_ADDRESS="$(kubectl -n observability-demo get service \
    -l app.kubernetes.io/component=gateway-service \
    -o jsonpath='{.items[0].status.loadBalancer.ingress[0].hostname}')"
  if [[ -z "$GATEWAY_ADDRESS" ]]; then
    GATEWAY_ADDRESS="$(kubectl -n observability-demo get service \
      -l app.kubernetes.io/component=gateway-service \
      -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')"
  fi
  [[ -n "$GATEWAY_ADDRESS" ]] || sleep 10
done

if [[ -z "$GATEWAY_ADDRESS" ]]; then
  kubectl -n observability-demo describe service \
    -l app.kubernetes.io/component=gateway-service
  exit 1
fi

export DEMO_BASE_URL="http://${GATEWAY_ADDRESS}"
echo "$DEMO_BASE_URL"
```

这是 AWS 自动分配的公网地址，无需准备自有域名，但 LoadBalancer 会产生 AWS 费用。

### 步骤四：验证部署

在浏览器中打开 `$DEMO_BASE_URL`，然后分步执行 smoke test、流量生成和故障恢复验证：

```shell
DATAKIT_PROVIDER=guance DEMO_PROJECT=mall-demo scripts/smoke-test.sh
scripts/generate-traffic.sh
scripts/inject-fault.sh payment_slow
scripts/inject-fault.sh off
```

smoke test 成功时会输出 `smoke test passed`。后续命令会生成正常订单、注入 `payment_slow` 并恢复正常状态。故障注入、恢复和预热均不需要控制口令。

### 清理

删除应用和公网 LoadBalancer，同时保留 DataKit：

```shell
scripts/workshop.sh cleanup
```

仅当集群不再需要 DataKit 时，才删除两个 Release：

```shell
scripts/workshop.sh cleanup --with-datakit
```
