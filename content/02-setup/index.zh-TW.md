---
title : "環境搭建"
weight : 20
---

## 環境搭建

本章完成從零到可觀測的基礎環境設定：

1. 宣告 EKS 與 DataWay 參數，並使用 Helm 安裝 DataKit。
2. 在 TrueWatch 建立 Web RUM 應用並記錄 Application ID 和 Workspace ID。
3. 使用公開映像將微服務商城部署到 EKS，並取得公網存取 URL。

需要使用者填寫的參數為 AWS Region、EKS 叢集名、DataWay URL、RUM Application ID 和 TrueWatch Workspace ID。`project=mall-demo`、映像標籤 `latest`、Namespace 以及 Demo 內部密碼均由教程固定或自動產生。
