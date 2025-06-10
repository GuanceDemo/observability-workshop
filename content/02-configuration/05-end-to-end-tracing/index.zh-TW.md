---
title : "端到端追蹤功能示範"
weight : 25
---

## 端到端追蹤功能示範

依照以下步驟探索 TrueWatch 的端到端追蹤能力：

### 步驟一：新增客戶資料

登入 Idurar 示範應用程式，前往 **Customers**，新增任意名稱的新客戶。

![01](/static/static-25/01.png)

### 步驟二：分析使用者會話

在 TrueWatch 中前往 **RUM → Explorers → Session**，點擊最新的會話紀錄。

![02](/static/static-25/02.png)

### 步驟三：探索 Fetch/XHR 請求

點選第一筆項目，切換到 **Fetch/XHR** 分頁，並將延遲依降序排列。

![03](/static/static-25/03.png)

### 步驟四：檢視相關追蹤（Trace）

點擊 `/api/payment/summary`，然後選擇 **View Related Trace**。找到並點選最底部的 MongoDB 查詢區塊。

![04](/static/static-25/04.png)

### 步驟五：查看 Span 詳細資訊

點擊 **Locate Current Span** 圖示進行放大，往下滑動可查看執行耗時與 MongoDB 查詢細節。

![05](/static/static-25/05.png)

### 步驟六：與主機指標關聯分析

點擊 **Host** 分頁，將追蹤資料與主機層級的指標關聯。

![06](/static/static-25/06.png)

### 步驟七：檢視主機日誌

切換到 **Logs** 分頁，選擇 **host** 檢視相關主機的日誌資訊。

![07](/static/static-25/07.png)

### 步驟八：錯誤調查

點擊 **Back** 返回會話檢視，點擊 **Error** 項目。

![08](/static/static-25/08.png)

![09](/static/static-25/09.png)

### 步驟九：AI 輔助錯誤分析

於 **Error Details** 頁面點擊 **Obsy AI Error Analysis**，由 AI 提供錯誤診斷與分析建議。

![10](/static/static-25/10.png)