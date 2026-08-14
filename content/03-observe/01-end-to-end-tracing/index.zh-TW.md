---
title : "端到端追蹤功能示範"
weight : 32
---

## 端到端追蹤功能示範

完成應用部署後，按照以下步驟從瀏覽器操作逐層定位到後端服務、依賴、日誌和基礎設施。本 Demo 的正常下單鏈路為 `gateway → order → MySQL → inventory → Redis → payment`。

### 步驟一：產生商城存取與下單資料

在瀏覽器開啟商城，瀏覽商品並提交一筆訂單。

![01](/static/static-25/01.png)

### 步驟二：分析使用者會話

在觀測雲中前往 **RUM → Explorers → Session**，新增 `project=mall-demo` 篩選條件，然後開啟剛剛產生的會話。

![02](/static/static-25/02.png)

### 步驟三：查看會話重播

在會話詳情中開啟 **Session Replay**，回放商城瀏覽和下單過程。

![03](/static/static-25/03.png)

### 步驟四：查看相關追蹤（Trace）

在商城 Demo 的右下角點擊 **Open trace details** 跳轉到觀測雲 Trace 詳情。在 Trace 瀑布圖中確認 Gateway、order、MySQL、inventory、Redis 與 payment 呼叫是否完整。

![04](/static/static-25/04.png)

### 步驟五：查看 Span 詳細資訊

點擊 **Locate Current Span** 放大當前呼叫，依序查看服務入口 Span、MySQL 查詢、Redis 呼叫和 `/api/payments/pay`。重點確認 `service`、`resource`、`project`、`trace_id`、錯誤狀態與執行耗時。

![05](/static/static-25/05.png)

### 步驟六：與主機指標關聯分析

切換到 **Host** 或基礎設施關聯頁，將 Trace 與對應 EKS 節點、Pod、容器 CPU 和記憶體指標關聯。所有 Demo 工作負載都應帶有 `project=mall-demo`。

![06](/static/static-25/06.png)

### 步驟七：查看主機日誌

切換到 **Logs**，查看同一 `trace_id` 下的應用日誌。日誌中還可以使用 `biz_request_id`、`key_request`、`fault_id`、`pod_name` 和 `container_name` 繼續篩選。

![07](/static/static-25/07.png)

### 步驟八：錯誤調查

在瀏覽器開啟商城，右側選擇 **Backend → Payment 5xx error → inject selected fault**，隨後瀏覽商品並提交一筆訂單。

![08](/static/static-25/08.png)

### 步驟九：查看錯誤 Trace

開啟剛剛產生的錯誤 Trace，確認 Gateway 回傳 HTTP 503，並檢查 payment-service 上的錯誤 Span 與故障欄位。

![10](/static/static-25/10.png)

### 步驟十：查看 Profiling

進入 **APM → Profiling**，按 `project=mall-demo` 篩選 Java Profile。可以看到 Gateway、order、inventory 與 payment 服務週期性上報的 CPU Profile。

![11](/static/static-25/11.png)
