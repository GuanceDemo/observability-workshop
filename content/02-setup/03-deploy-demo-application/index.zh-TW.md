---
title : "部署示範應用"
weight : 23
---

## 部署示範應用

本章使用公開 GHCR 映像檔將商城 Demo 部署到 AWS EKS，無需在 CloudShell 中建置映像檔或設定 ECR。除 Gateway 外，訂單、庫存、支付、MySQL 和 Redis 都僅在叢集內部存取。

### 步驟一：使用 Helm 部署商城 Demo

執行以下命令安裝應用程式：

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

### 步驟二：等待工作負載就緒

```shell
kubectl wait --for=condition=Available deployment --all \
  --namespace observability-demo \
  --timeout=8m

kubectl -n observability-demo get pods
```

![02](/static/static-24/02.png)

正常情況下會看到 Gateway、order、inventory、payment、MySQL 和 Redis 六個工作負載。Java Pod 以非 root 使用者執行，並透過節點 IP 將 Trace、JVM 指標和 Profiling 資料傳送到 DataKit。

### 步驟三：取得公網存取 URL

AWS 建立 Load Balancer 通常需要幾分鐘。執行以下命令等待 Gateway 的公網位址：

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

這是 AWS 自動分配的公網 DNS，無需事先準備自有網域。Load Balancer 會產生 AWS 費用，Workshop 結束後請按本章末尾的命令進行清理。

### 步驟四：開啟商城並驗證部署

在瀏覽器中開啟 `EXTERNAL-IP`。商城頁面無需登入，支援中文與英文切換。首次進行故障操作時，頁面會提示輸入故障控制口令，該值僅保存在當前瀏覽器的 `sessionStorage`。

在 CloudShell 中取得口令並執行自動驗證：

```shell
printf '%s\n' "$(kubectl -n observability-demo get secret demo-observability-demo \
  -o jsonpath='{.data.demo-control-token}' | base64 --decode)"
```

![04](/static/static-24/04.png)
