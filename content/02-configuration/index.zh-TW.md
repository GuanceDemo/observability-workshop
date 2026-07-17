---
title : "配置"
weight : 20
---

## 配置

本工作坊按以下順序完成配置與驗證：

1. 了解本次實驗將使用的 TrueWatch 功能。
2. 宣告 EKS 與 DataWay 參數，並使用 Helm 安裝 DataKit。
3. 在 TrueWatch 建立 Web RUM 應用並記錄 Application ID 和 Workspace ID。
4. 使用公開映像將微服務商城部署到 EKS，並取得公網存取 URL。
5. 產生業務流量並驗證 RUM、端到端追蹤、日誌和 Profiling。
6. 建立日誌 Pipeline 並驗證結構化欄位。
7. 建立監控器和 SLO，並匯入產品服務運行品質大屏。
8. 在 EKS 節點部署 Agent Runtime，使用 Toby AI 和 Agent Teams 分析 Trace 與告警，完成故障處理閉環並清理 Workshop 資源。

需要使用者填寫的參數為 AWS Region、EKS 叢集名稱、DataWay URL、RUM Application ID 和 TrueWatch Workspace ID。`project=mall-demo`、映像標籤 `latest`、Namespace 以及 Demo 內部密碼均由教學固定或自動產生。
