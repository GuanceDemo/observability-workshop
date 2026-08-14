---
title : "環境搭建"
weight : 20
---

## 環境搭建

本章完成從零到可觀測的基礎環境設定：

1. 宣告 EKS 與 DataWay 參數，並使用 Helm 安裝 DataKit。
2. 在 TrueWatch 建立 Web RUM 應用並記錄 Application ID 和 Workspace ID。
3. 使用公開映像將微服務商城部署到 EKS，並取得公網存取 URL。

需要使用者填寫的參數為 DataWay URL、RUM Application ID 和 TrueWatch Workspace ID。`project=mall-demo`、映像標籤 `2.3.0`、Namespace 以及 Demo 內部密碼均由教程固定或自動產生。

各章節保留分步命令用於教學。準備好 EKS 叢集和 TrueWatch RUM 應用後，也可以使用行為等價的快速路徑：

```shell
export DEMO_VERSION="2.3.0"
git clone --branch "v${DEMO_VERSION}" --depth 1 \
  https://github.com/GuanceDemo/observability-demo.git
cd observability-demo
scripts/workshop.sh install
```
