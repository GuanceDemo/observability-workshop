---
title : "TrueWatch 可觀測性工作坊簡介"
weight : 10
---

## TrueWatch 可觀測性工作坊簡介

本工作坊將透過在 AWS EKS（Elastic Kubernetes Service）中部署並監控一個可注入故障的微服務商城 Demo，介紹 TrueWatch 可觀測性平台的實際應用。目標是為學員提供完整的實操體驗，涵蓋 Kubernetes 與容器指標、應用效能監控、日誌分析、JVM 監控、Profiling、使用者體驗監控、告警管理及端到端鏈路追蹤等核心功能。

### 關於微服務商城 Demo

該 Demo 是一個面向可觀測性教學場景設計的 Java 微服務商城應用，透過完整的下單鏈路展示不同服務和依賴之間的呼叫關係。其主要特點包括：

- 採用 **Spring Boot** 建構 Gateway、訂單、庫存和支付四個 Java 微服務
- 使用 **MySQL** 保存訂單資料，並透過 **Redis** 模擬庫存快取
- 支援透過 **Helm** 部署到 AWS EKS 等標準 Kubernetes 環境
- 內建前端、服務、JVM 和依賴層故障場景，可用於演示指標、日誌、Trace、Profiling、RUM、Browser Logs 和 Session Replay 的關聯分析

本工作坊將帶領您在 AWS EKS 叢集中安裝 DataKit、部署微服務商城 Demo、產生業務流量、注入故障，並透過 TrueWatch 完成從使用者存取到後端服務及基礎設施的全鏈路觀測與問題分析。

### TrueWatch 可觀測性平台功能特色

TrueWatch 是全面的可觀測性解決方案，提供應用效能、基礎設施健康度、使用者行為監控（RUM）、端到端追蹤、日誌分析及告警管理等多樣化功能。主要特色包括：

- **一站式監控**：整合指標、日誌與追蹤於單一平台。
- **互動式儀表板**：快速視覺化觀測資料。
- **端到端追蹤**：完整追蹤應用程式前端至後端的效能。
- **即時使用者體驗分析**：分析使用者行為與效能表現。
- **全面的日誌管理**：集中式日誌接收與解析。
- **綜合監控**：主動式服務可用性監控。
- **自訂告警**：強大的告警規則與通知功能。
- **AI 錯誤分析**：透過 AI 自動診斷與根本原因分析。

### 工作坊環境設定

工作坊將帶領參與者完成以下實操步驟：

- 連接 AWS EKS，並透過官方 Helm Chart 安裝 DataKit。
- 建立 Web RUM 應用，並透過節點 DataKit 接收 RUM、Browser Logs 與 Session Replay 資料。
- 使用公開 GHCR 映像部署微服務商城，無需在 Workshop 環境中建構映像。
- 透過 `project=mall-demo` 關聯 Kubernetes 指標、APM、日誌、JVM、Profiling 與 RUM 資料。
- 產生業務流量、注入可恢復的故障，並設定告警與關聯分析流程。

### 準備步驟

開始前請確保已完成以下準備：

1. **TrueWatch Cloud 帳號：**
   - 註冊 [https://id1-auth.truewatch.com/businessRegister](https://id1-auth.truewatch.com/businessRegister) 並建立工作區 (Workspace)，預設為商業版。
2. **AWS EKS 叢集：**
   - 叢集至少包含一個狀態為 `Ready` 的工作節點，並允許建立 `LoadBalancer` 類型的 Service。
3. **AWS CloudShell 權限：**
   - 目前身份需要能夠讀取 EKS 叢集資訊，並在叢集中建立 Namespace、Secret、Service、Deployment、DaemonSet、Role 與 RoleBinding。

**注意事項：**  
TrueWatch 透過「工作區」將不同環境或應用的觀測資料分離，多個環境間可透過授權方式共享資料。DataWay URL 中包含敏感 token，請勿將其寫入文件、截圖或 Git 儲存庫。
