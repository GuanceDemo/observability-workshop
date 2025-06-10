---
title : "建立 RUM 客戶端應用程式"
weight : 23
---

## 建立 RUM 客戶端應用程式

TrueWatch 透過在前端應用程式中進行簡單配置，即可支援真實用戶監控（RUM），並實現從前端到後端的端到端追蹤。本指南說明如何在 TrueWatch 建立客戶端應用程式並取得所需的 `clientToken`。

### 步驟一：建立新的客戶端應用程式

實驗用的應用程式已完成基本設定，請依照以下步驟在 TrueWatch 建立新的客戶端應用程式並取得您的 `clientToken`：

1. 登入 TrueWatch 控制台，點選 **RUM → Application → Create**。
2. 選擇 **Web** 作為應用程式類型。
3. 在「應用程式名稱」與「應用程式 ID」輸入 `idurar`。
4. 複製顯示的 `clientToken` 以供後續使用。
5. 點擊 **Create** 建立。

![01](/static/static-23/01.png)

### 步驟二：更新 RUM 的 Client Token

編輯前端設定檔中的 RUM client token：

```
vim ../frontend/src/main.jsx
```

將第 10 行的 clientToken 替換為從 TrueWatch → RUM → Application Settings 取得的 token：

![02](/static/static-23/02.png)