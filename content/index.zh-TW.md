---
title : "工作坊介紹"
weight : 01
---

## 工作坊內容

本工作坊透過在 AWS EKS 中部署一個可注入故障的 Java 微服務商城 Demo，帶您完成從資料採集到故障分析的完整可觀測實踐。

您將依次完成以下操作：

- 使用官方 Helm Chart 在 EKS 中安裝 DataKit。
- 建立 Web RUM 應用並啟用 Browser Logs、Session Replay 與 SourceMap。
- 使用公開 GHCR 映像部署 Gateway、訂單、庫存、支付、MySQL 和 Redis。
- 產生商城流量並注入故障，觀察 Kubernetes 指標、Trace、日誌、JVM、Profiling 與 RUM 資料。
- 透過統一的 `project=mall-demo` 標籤關聯不同類型的觀測資料。

Demo 僅用於教學演示：MySQL 和 Redis 使用臨時儲存，公網入口由 AWS Load Balancer 提供，不應作為生產部署範本直接使用。

## 參與貢獻

如果您對貢獻內容感興趣，或者有新的內容建議及需求，可以聯絡 <yagrxu@amazon.com> 或 <shannatc@amazon.com>
