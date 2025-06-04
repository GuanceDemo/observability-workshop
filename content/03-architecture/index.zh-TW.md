---
title : "Architecture Overview"
weight : 30
---

## 架構概述

iDURAR 是一套開源的客戶關係管理（CRM）系統，具備現代化的前後端分離架構。本工作坊將展示如何在 AWS EKS 環境中，利用 TrueWatch 平台為此應用程式建構完整的可觀測性（Observability）解決方案。

## 部署架構圖

![01](/static/static-architecture/01.png)

## 元件說明

本系統包含以下元件：

- **AWS 應用程式負載平衡器（ALB）：** 負責將終端使用者透過瀏覽器的請求路由至應用程式。
- **iDURAR 前端應用程式：** 基於 React 開發的使用者介面，並整合 TrueWatch RUM SDK 以實現真實使用者監控。
- **iDURAR 後端應用程式：** 基於 Node.js 與 Express 開發的後端服務，處理商業邏輯。
- **MongoDB 資料庫：** 提供資料持久化儲存功能。
- **Datakit 收集代理程式：** 以 DaemonSet 模式部署於 EKS 節點上，負責收集系統的指標、日誌及追蹤資料。
- **TrueWatch 平台：** 提供雲端可觀測性服務，包含效能指標、日誌分析、追蹤、警示與分析功能。