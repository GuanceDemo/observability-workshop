---
title : "環境搭建"
weight : 20
---

## 環境搭建

本章完成從零到可觀測的基礎環境設定：

1. 宣告 EKS 與 DataWay 參數，並使用 Helm 安裝 DataKit。
2. 在觀測雲建立 Web RUM 應用並記錄 Application ID 和 Workspace ID。
3. 使用公開映像將微服務商城部署到 EKS，並取得公網存取 URL。

需要使用者填寫的參數為 DataWay URL、RUM Application ID 和觀測雲 Workspace ID。`project=mall-demo`、映像標籤 `2.3.5`、Namespace 以及 Demo 內部密碼均由教學固定或自動產生。

### 快速搭建（可選）

如果已有包含 Ready EC2 工作節點的 EKS 叢集，可以使用快速路徑完成 DataKit 和 Demo 安裝。現有分步教學繼續保留，用於瞭解設定細節和疑難排解。

#### 1. 準備平台參數

開始前請在[觀測雲控制台](https://console.guance.com/)完成以下準備：

1. 進入 **整合 → DataKit → Kubernetes(Helm)**，複製 `datakit.dataway_url` 的完整值。
2. 進入 **使用者訪問監測 → 應用列表 → 新建應用**，建立 Web RUM 應用，複製 **Application ID** 和目前工作空間的 **Workspace ID**。

開啟 AWS CloudShell，集中宣告快速安裝所需參數。CloudShell 會自動提供目前區域。DataWay URL 包含敏感 token，使用隱藏輸入可避免它進入 Shell 歷史：

```shell
export EKS_CLUSTER_NAME="eks-demo-cluster"
read -rsp 'DataWay URL: ' DATAWAY_URL && export DATAWAY_URL && echo
read -rp 'RUM Application ID: ' RUM_APPLICATION_ID && export RUM_APPLICATION_ID
read -rp '觀測雲 Workspace ID: ' GUANCE_WORKSPACE_ID && export GUANCE_WORKSPACE_ID
```

#### 2. 執行快速安裝

複製 Guance 官方固定版本，並在儲存庫根目錄執行安裝命令：

```shell
export DEMO_VERSION="2.3.5"
git clone --branch "v${DEMO_VERSION}" --depth 1 \
  https://github.com/GuanceDemo/observability-demo.git
cd observability-demo

scripts/workshop.sh install
```

腳本會自動安裝並驗證 Helm `v3.17.3`（僅在 Helm 缺失時），顯示 AWS 身分、目標 Kubernetes context 和節點，確認後安裝 DataKit `2.5.0` 與六個 Demo 工作負載，並等待 Gateway 取得公網 hostname 或 IP。自動化環境可使用 `scripts/workshop.sh install --yes` 跳過確認。

如果已經存在 `datakit/datakit` Helm Release，腳本會預設停止，避免覆蓋共用設定。請改用本章後續的分步教學；只有確認允許升級現有 DataKit 時，才明確執行 `scripts/workshop.sh install --upgrade-existing-datakit`。

安裝成功後，腳本會輸出 `DEMO_BASE_URL` 和 SourceMap 的版本提示。它不會執行 smoke test、產生流量或注入故障。請清理目前 Shell 中的參數：

```shell
unset DATAWAY_URL RUM_APPLICATION_ID GUANCE_WORKSPACE_ID
```

接下來完成 **建立 RUM 客戶端應用 → 步驟三：準備 SourceMap 檔案**，然後進入第三章繼續觀測與故障示範。需要瞭解完整部署過程或疑難排解時，繼續閱讀本章後續的分步教學。
