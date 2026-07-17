---
title : "在 AWS EKS 配置 DataKit"
weight : 21
---

## 在 AWS EKS 配置 DataKit

請按照以下步驟在 Amazon EKS 中完成 DataKit 與 TrueWatch 的整合。除 TrueWatch 控制台操作外，本章命令均在同一個 AWS CloudShell 終端中執行。

### 步驟一：準備資訊並宣告參數

開始前準備以下資訊：

- EKS 所在的 AWS Region。
- EKS 叢集名稱。
- TrueWatch 工作區提供的 DataWay URL。

登入 TrueWatch Cloud，進入 **Integration → DataKit → Kubernetes(Helm)**，複製 `datakit.dataway_url` 的完整值：

![01](/static/static-22/01.png)

開啟 AWS CloudShell，先集中宣告本章使用的參數。請替換前兩個範例值；DataWay URL 使用隱藏輸入，不會出現在 Shell 歷史記錄中：

```shell
export AWS_REGION="ap-northeast-2"
export EKS_CLUSTER_NAME="observability-demo"
read -rsp 'DataWay URL: ' DATAWAY_URL && export DATAWAY_URL && echo
```

本 Demo 固定使用 `project=mall-demo`、DataKit Namespace `datakit` 和應用 Namespace `observability-demo`，無需額外宣告。DataWay URL 包含敏感 token，請勿直接寫入 YAML、文件或 Git 儲存庫。

### 步驟二：確認命令列工具

AWS CloudShell 通常已提供 AWS CLI 與 `kubectl`。先檢查 Helm：

```shell
helm version --short
```

如果提示找不到 `helm`，將 Helm 安裝到 CloudShell 的持久化目錄 `$HOME/bin`：

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

這樣重新開啟 CloudShell 後仍可直接使用 Helm。

### 步驟三：連線目標 EKS 叢集

使用步驟一宣告的參數產生當前 CloudShell 的 kubeconfig：

```shell
aws eks update-kubeconfig \
  --region "$AWS_REGION" \
  --name "$EKS_CLUSTER_NAME"

kubectl config current-context
kubectl get nodes
```

![02](/static/static-22/02.png)

當 `kubectl get nodes` 能顯示至少一個狀態為 `Ready` 的節點時，表示連線成功。如果當前上下文已經指向目標叢集，可以跳過 `aws eks update-kubeconfig`，但仍應執行以上兩條檢查命令。

### 步驟四：複製 Demo 儲存庫

```shell
git clone https://github.com/TrueWatchTech/observability-demo.git
cd observability-demo
```

後續所有 Helm 和腳本命令都在儲存庫根目錄執行。

### 步驟五：使用 Helm 安裝 DataKit

新增官方 DataKit Chart 儲存庫，並安裝固定版本 `2.5.0`：

```shell
helm repo add datakit https://pubrepo.truewatch.com/chartrepo/datakit
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

儲存庫中的 values 會開啟 Kubernetes/容器指標、DDTrace、JVM StatsD、Profiling、RUM、日誌採集和日誌 Pipeline。真實 DataWay URL 由 Chart 寫入 Kubernetes Secret，不會保存在儲存庫檔案中。

檢查 DaemonSet 與 Pod 狀態：

```shell
kubectl get pods -n datakit
kubectl logs -n datakit daemonset/datakit --tail=200 | grep 'add input'
```

![03](/static/static-22/03.png)

### 步驟六：在 TrueWatch 驗證 DataKit 整合狀態

數分鐘後，驗證資料是否正確傳送至 TrueWatch：

1. 進入 **Infrastructure**，按 `project=mall-demo` 篩選並查看 EKS 叢集節點資訊。
	![04](/static/static-22/04.png)
2. 前往 **Containers → Pods**，查看 DataKit Pod 的基本資訊與效能指標。
	![05](/static/static-22/05.png)
3. 點擊 **Analysis Dashboard**，查看更詳細的 Kubernetes 分析儀表板。
	![06](/static/static-22/06.png)
4. 在 **Logs** 中查看 Kubernetes 元件日誌與叢集事件。
	![08](/static/static-22/08.png)

此時商城應用尚未部署，因此 APM、應用日誌、JVM、Profiling 與 RUM 資料會在後續章節完成應用安裝和流量產生後出現。
