---
title : "建立監控器、設定 SLO 並匯入監控大屏"
weight : 33
---

## 建立監控器、設定 SLO 並匯入監控大屏

本章將匯入產品與服務維度的 APM 監控器，基於監控器建立 `Project SLO`，最後匯入產品服務運行品質分析大屏。

### 步驟一：下載設定附件

::alert[右鍵點選以下連結並選擇「另存新檔」，將兩個 JSON 檔案下載到本機。]{type="info"}

- [product-service-monitor-template.json](https://ws-assets-prod-iad-r-nrt-2cb4b4649d0e0f94.s3.ap-northeast-1.amazonaws.com/24932227-1f86-44e3-acc9-3df44d13d2fc/product-service-monitor-template.json)
- [product-service-operational-quality-dashboard.json](https://ws-assets-prod-iad-r-nrt-2cb4b4649d0e0f94.s3.ap-northeast-1.amazonaws.com/24932227-1f86-44e3-acc9-3df44d13d2fc/product-service-operational-quality-dashboard.json)

### 步驟二：匯入並建立監控器

進入 **Monitoring → Monitors**，點選右上角設定選單並選擇 **Import**，上傳 `product-service-monitor-template.json`。

![01](/static/static-27/01.png)

確認匯入後建立了 12 個監控器，涵蓋以下檢測範圍：

- 產品、服務和介面級錯誤率
- HTTP 狀態碼錯誤率
- 產品、服務和介面級 P90/P99 回應時間

開啟 `Product Error Rate` 和 `Product P90 Latency`，確認檢測週期為 `5 minutes`，檢測資料中可以看到 `project=mall-demo`。

返回監控器列表，確認監控器狀態為 **Enable**。

### 步驟三：設定 Project SLO

進入 **Monitoring → SLO**，點選 **Create SLO**，按以下參數建立（Service SLO 類似）：

- SLO 名稱：`Project SLO`
- SLI 監控器：`Product Error Rate`、`Product P90 Latency`
- 目標：`95%`
- 最低目標：`90%`
- 分組維度：`project`
- 分組值：`mall-demo`

![02](/static/static-27/02.png)
![03](/static/static-27/03.png)

儲存後確認 SLO 頁面可以顯示達標率、錯誤預算和關聯監控器。

### 步驟四：匯入產品服務運行品質大屏

進入 **Dashboards**，點選 **New Dashboard → Import Dashboard**，選擇 **Official Dashboard**，上傳 `product-service-operational-quality-dashboard.json`。

確認以下資訊後完成匯入：

- Dashboard 名稱：`Product Service Operational Quality Analysis Dashboard`
- Identifier：`product-service-operational-quality`

開啟大屏，將頂部 **Product** 變數設定為 `mall-demo`。編輯 `Product SLO` 圖表，選擇當前工作空間中剛建立的 `Project SLO`，儲存大屏。

### 步驟五：產生異常並驗證監控結果

在商城 Demo 中注入支付錯誤並產生業務流量，等待一個檢測週期後，依序檢查：

1. **Monitoring → Monitors** 中相關錯誤率監控器進入異常狀態。
2. **Monitoring → SLO** 中 `Project SLO` 開始消耗錯誤預算。
3. 產品服務運行品質大屏顯示錯誤請求、告警事件和 SLO 變化。

![04](/static/static-27/04.png)

驗證完成後關閉故障，並產生正常流量，等待下一個檢測週期，確認監控器恢復。
