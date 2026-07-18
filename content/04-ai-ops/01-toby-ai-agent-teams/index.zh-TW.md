---
title : "Toby AI 與 Agent Teams 能力演示"
weight : 41
---

## Toby AI 與 Agent Teams 能力演示

本章基於前一章產生的支付異常和監控告警，演示 Toby AI 對錯誤 Trace 的分析能力，以及 Agent Teams 接收告警、存取 EKS 並產生處置建議的能力。

Toby AI 可以直接在平台中使用；Agent Teams 的 Agent Runtime 需要由使用者部署。本 Workshop 將 Runtime 臨時安裝到一台 EKS EC2 工作節點，演示結束後再統一清理。

### 步驟一：開啟 AI 智慧分析

在建立 Agent 之前，需要先確認工作空間已開啟 AI 功能：

1. 登入 TrueWatch。
2. 進入 **Management → Workspace Settings**。
3. 找到 **Security** 設定區域。
4. 開啟 **AI 智慧分析**。
5. 確認開關已經生效。

如果看不到此開關，請聯繫工作空間管理員確認權限和功能授權。

### 步驟二：建立 Workshop Agent

點選平台頂部的 **Toby AI → Toby Agent Teams**，進入 Agent Workspace 並建立一個專用於本次 Workshop 的 Agent：

- Quick Start：`Observability Navigator`

進入該 Agent 的 **Run & Deploy** 頁面，選擇 Linux 安裝方式，並從專屬安裝命令中確認以下內容：

- Agent ID
- Agent API Key
- Beak Endpoint

![01](/static/static-28/01.png)

### 步驟三：在 EKS 節點部署 Agent Runtime

安裝腳本適用於帶 EC2 工作節點的 EKS 叢集，節點需要使用支援 `systemd` 的 Amazon Linux 或 Ubuntu。Fargate-only、Bottlerocket 以及已經安裝 `obs-agent` 的節點不使用本步驟。

本步驟使用一個短生命週期的特權 helper Pod 進入目標節點並安裝 Runtime，不修改 EC2 Node Role，也不依賴 SSM。目前的 AWS 身分需要能夠存取 EKS，並在叢集中建立 privileged、hostPath 和 hostPID Pod。該方式只應在本次 Workshop 的專用叢集中執行。

在 CloudShell 中確認所需命令可用：

```shell
aws --version
kubectl version --client
```

繼續使用第二章宣告的 EKS 參數，並填寫 Agent 安裝命令中的 Beak Endpoint：

```shell
export BEAK_ENDPOINT="https://agent-api.truewatch.com"
```

執行儲存庫中的安裝腳本：

```shell
scripts/install-obs-agent-eks-node-demo.sh
```

腳本會自動完成以下操作：

1. 選擇目標叢集中的一台 Ready Linux 工作節點。
2. 建立一個短生命週期的特權 helper Pod，並掛載目標節點根檔案系統。
3. 建立 Kubernetes 唯讀 ServiceAccount、RBAC 和預設有效期為 8 小時的 kubeconfig。
4. 在節點上安裝與 EKS 版本匹配的 `kubectl`。
5. 使用 `Standard` 權限模式安裝 Agent，並開啟加密的 Kubernetes exec 互動會話。
6. 安裝完成或腳本結束時自動刪除 helper Pod。

在互動會話中依序輸入 Agent ID 和 Agent API Key。API Key 會隱藏輸入，不會進入 Shell 歷史、Pod 定義或儲存庫。

![02](/static/static-28/02.png)

如果腳本自動選擇的節點不符合要求，可以在重新安裝前指定：

```shell
export TARGET_INSTANCE_ID="i-xxxxxxxxxxxxxxxxx"
scripts/install-obs-agent-eks-node-demo.sh
```

### 步驟四：驗證 Agent Runtime

安裝完成後回到 Agent Workspace，等待 `Observability Navigator` 狀態變為 **Online**。

建立一個唯讀驗證任務：

```text
請列出目前 EKS 叢集的節點、命名空間和 observability-demo 命名空間中的 Pod。只讀取資料，不執行任何修改。
```

確認 Agent 能夠列出節點和 Pod。

### 步驟五：準備支付異常與告警事件

在商城 Demo 中選擇 **Backend → Payment 5xx error → Inject selected fault**，連續提交多筆訂單，等待第七章建立的錯誤率監控器觸發告警。

進入 **APM → Traces**，使用以下條件查找本次異常 Trace：

- `project=mall-demo`
- `service=payment-service`
- `status=error`

開啟一條包含 payment-service 錯誤 Span 的 Trace，確認 Gateway 回傳 HTTP 503，並記錄該 Trace ID。

### 步驟六：使用 Toby AI 分析錯誤 Trace

在錯誤 Trace 詳情頁開啟 **Toby AI**，選擇分析當前 Trace，並輸入：

```text
分析當前錯誤 Trace，說明故障發生在哪個服務、上下游影響、關鍵證據、可能根因和建議的處理順序。
```

檢查分析結果是否包含以下內容：

1. 定位 payment-service 的錯誤 Span。
2. 說明錯誤如何沿 `payment → order → gateway` 傳播。
3. 使用 HTTP 狀態碼、錯誤訊息、Trace ID 和關聯日誌作為證據。
4. 區分 Demo 主動注入的故障與真實外部依賴異常。
5. 給出立即處理、後續調查和持續監控建議。

可以繼續追問：

```text
使用相同 trace_id 關聯日誌，確認 payment-service 的錯誤原因，並將分析結果整理成一份 Note。
```

![03](/static/static-28/03.png)

### 步驟七：使用 Agent Teams 處理告警任務

首先進入 **Monitoring → Alert Strategies**，新建或編輯本 Workshop 使用的告警策略：

1. 在 **關聯** 中加入第七章建立的服務錯誤率、HTTP 狀態碼錯誤率和回應時間監控器。
2. 展開 **通知規則**，將時區設定為 `(UTC+09:00) Asia/Tokyo`。
3. 選擇 **按等級（Level）** 通知，並為 `Fatal` 等級選擇通知對象 `Observability Navigator`。如果監控器使用其他事件等級，請為對應等級增加相同的 Agent 通知規則。
4. 點選 **保存**。只有匹配通知等級的事件才會投遞給 Agent。

如果通知對象列表中沒有 `Observability Navigator`，請先確認該 Agent 已在目前工作空間建立，並且 Runtime 狀態為 **Online**。

![04](/static/static-28/04.png)

進入 Agent Workspace，選擇已經部署並處於 Online 狀態的 `Observability Navigator`，隨後進入 **Task Intake**，建立或檢查 **Workspace hook**：

- Name：`Observability Navigator Event Webhook`
- Intake type：`Workspace`
- Trigger method：`Platform delivery`
- Execution period：`1 minute window`
- Permission mode：使用目前 Workshop 工作空間權限
- Status：`Active`

![05](/static/static-28/05.png)

將第七章監控器的告警策略投遞到該任務入口，再次觸發支付錯誤。確認 **Received messages** 數量增加，並在執行記錄或 **My Tasks** 中開啟新任務，檢查 Agent 是否自動完成事件、Trace、日誌和 EKS 資源的關聯分析。

### 步驟八：關閉故障並驗證閉環

返回商城 Demo，關閉當前故障並繼續提交正常訂單。等待下一個監控偵測週期後，確認：

1. 錯誤率監控器恢復正常。
2. 告警事件進入恢復狀態。
3. Agent 任務保留了本次異常的分析結論和處理建議。

可以在 Toby AI 中輸入以下內容，產生本次演示的復盤記錄：

```text
將本次 payment-service 告警、影響範圍、根因證據、處置過程和恢復驗證整理成一份復盤 Note。
```

至此完成從故障注入、監控告警、AI 分析、Agent 處理到恢復驗證的閉環演示。

### Workshop 結束後清理

先在 EKS 節點仍處於運行狀態時清理 Agent Runtime。該命令會透過臨時 helper Pod 刪除節點上的 Agent 與 Owl 憑證及臨時 Kubernetes RBAC，並在結束時刪除 helper Pod：

```shell
scripts/install-obs-agent-eks-node-demo.sh --cleanup
unset BEAK_ENDPOINT TARGET_INSTANCE_ID TARGET_NODE_NAME
```

確認清理完成後，在 Agent Workspace 中刪除本 Workshop 建立的 Agent 和 Task Intake。

然後在 CloudShell 中解除安裝應用程式：

```shell
kubectl config current-context
helm list --all-namespaces

helm uninstall demo -n observability-demo
kubectl delete namespace observability-demo --ignore-not-found
```

等待 Gateway Service 刪除後，在 AWS 主控台確認本 Workshop 建立的 Load Balancer 已被釋放，避免繼續產生費用。

僅當該 EKS 叢集不再需要 DataKit 採集時，再執行：

```shell
helm uninstall datakit -n datakit
kubectl delete namespace datakit --ignore-not-found
```
