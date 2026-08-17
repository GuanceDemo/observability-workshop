---
title : "部署示範應用"
weight : 23
---

## 部署示範應用

本章使用公開 Harbor `2.3.1` 映像將商城 Demo 部署到 AWS EKS，無需在 CloudShell 中建置映像、設定 ECR 或登入映像倉庫。除 Gateway 外，訂單、庫存、支付、MySQL 和 Redis 都只在叢集內部存取。

Gateway 會公開到網際網路，Demo 故障介面有意不要求控制憑證。此 profile 只能用於隔離、短期的 Workshop 叢集，練習結束後應及時清理。

### 步驟一：使用 Helm 部署商城 Demo

使用固定版本的觀測雲 EKS profile：

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

![01](/static/static-24/01.png)

此 profile 使用 `IfNotPresent` 拉取 `pubrepo.jiagouyun.com/demo/observability-demo-{gateway,order,inventory,payment}-service:2.3.1`。映像公開並同時支援 `linux/amd64` 和 `linux/arm64`。

### 步驟二：等待工作負載就緒

```shell
kubectl wait --for=condition=Available deployment --all \
  --namespace observability-demo \
  --timeout=8m

kubectl -n observability-demo get pods
```

![02](/static/static-24/02.png)

正常情況下會看到 Gateway、order、inventory、payment、MySQL 和 Redis 六個工作負載。Java Pod 以非 root 使用者執行，並透過節點 IP 將 Trace、JVM 指標和 Profiling 資料傳送到 DataKit。應用與 DataKit 使用相同的 `EKS_CLUSTER_NAME` 完成關聯。

### 步驟三：取得公網存取 URL

在十分鐘內等待 LoadBalancer hostname 或 IP：

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

![03](/static/static-24/03.png)

這是 AWS 自動分配的公網地址，無需準備自有網域，但 LoadBalancer 會產生 AWS 費用。

### 步驟四：驗證部署

在瀏覽器中開啟 `$DEMO_BASE_URL`：

![04](/static/static-24/04.png)
