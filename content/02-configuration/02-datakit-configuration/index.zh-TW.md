---
title : "在 AWS EKS 設定 Datakit"
weight : 22
---

## 在 AWS EKS 設定 Datakit

請依照以下步驟在 Amazon EKS 中完成 Datakit 與 TrueWatch 的整合。所有的指令都在 AWS CloudShell 終端機中執行。

### 步驟一：複製 Workshop 儲存庫

開啟 AWS CloudShell，並在終端機中執行以下指令：

```shell
git clone https://github.com/TrueWatchTech/idurar-demo-workshop
```

### 步驟二：取得 Dataway 端點資訊

登入 TrueWatch Cloud，點擊 Integration → Datakit → Kubernetes，複製 ENV_DATAWAY 的值：

![01](/static/static-22/01.png)

### 步驟三：更新 Datakit 設定檔

將剛才複製的 ENV_DATAWAY 值取代 datakit.yaml（第 166 行）中的 YOUR-ENV-DATAWAY：

```
cd idurar-demo-workshop/workshop
vim datakit.yaml  # Use ":set nu" to show line numbers
```

![02](/static/static-22/02.png)

### 步驟四：更新 Kubernetes 設定

執行以下指令，更新 Kubernetes 設定至指定的 EKS 叢集：

```
aws eks --region ap-southeast-1 update-kubeconfig --name eks-demo-cluster
```

![03](/static/static-22/03.png)

### 步驟五：將 Datakit 部署至 Kubernetes

執行以下指令部署並驗證 Datakit 狀態：

```
# Deploy Datakit
kubectl apply -f datakit.yaml

# Check deployment status
kubectl get pods -n datakit
```

### 步驟六：在 TrueWatch 驗證 Datakit 整合狀態

數分鐘後，驗證資料是否正確傳送至 TrueWatch：

1. 點擊 Infrastructure 查看 EKS 叢集節點資訊。
	![04](/static/static-22/04.png)
2. 前往 Containers → Pods 檢視 Pod 基本資訊與效能指標。
	![05](/static/static-22/05.png)
3. 點擊 Analysis Dashboard 查看更詳細的分析儀表板。
	![06](/static/static-22/06.png)
4. 檢查 Network → Map 查看網路拓撲和流量，下圖展示 EKS 節點與 TrueWatch 之間的資料傳送狀況：
	![07](/static/static-22/07.png)
5. 在 Logs 下檢視與 kube-proxy 元件和叢集事件相關的日誌。
	![08](/static/static-22/08.png)