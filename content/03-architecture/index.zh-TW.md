---
title : "部署架構圖"
weight : 30
---

## 架構概述

本工作坊使用一個面向可觀測性教學的 Java 微服務商城。瀏覽器請求首先進入 Gateway，再由訂單服務呼叫 MySQL、庫存服務、Redis 和支付服務。DataKit 以 DaemonSet 運行在每個 EKS 節點上，將基礎設施、APM、日誌、JVM、Profiling 與 RUM 資料傳送到 TrueWatch。

## 部署架構圖

![01](/static/static-30/01.png)

## 元件說明

本系統包含以下元件：

- **AWS Load Balancer：** 由 `LoadBalancer` 類型的 Gateway Service 自動建立，為瀏覽器提供公網 DNS；具體類型由 EKS 叢集的負載平衡控制器決定。
- **Gateway Service：** 商城唯一的公網入口，轉發業務與 Demo API，並拒絕外部存取內部 `/admin/**` 介面。
- **Order Service：** 承載雙語商城頁面、訂單 API、RUM 代理、日誌檢視器和故障控制入口。
- **Inventory Service：** 處理庫存業務，並透過 Redis 模擬快取依賴。
- **Payment Service：** 處理支付呼叫，提供慢呼叫、錯誤和 CPU 熱點等演示場景。
- **MySQL 與 Redis：** 使用 `emptyDir` 臨時卷，僅保存 Workshop 期間的 Demo 資料，不提供生產級持久化或高可用。
- **DataKit：** 透過獨立 Helm Release 以 DaemonSet 部署，採集 Kubernetes/容器指標、應用日誌、Trace、JVM StatsD、Profiling 與 RUM。
- **TrueWatch：** 接收 DataKit 上報的資料，提供 Explorer、Dashboard、APM、日誌、RUM、告警與 AI 分析能力。

應用與 DataKit 使用兩個獨立 Helm Release：`demo` 位於 `observability-demo` Namespace，`datakit` 位於 `datakit` Namespace。所有 Demo 訊號統一設定 `project=mall-demo`，便於跨資料類型關聯分析。

## 主要資料流

1. 瀏覽器透過 AWS Load Balancer 存取 Gateway。
2. 正常下單依次經過 `gateway → order → MySQL → inventory → Redis → payment`。
3. Java Agent 將 Trace 與 Profiling 傳送到節點 `9529` 連接埠，將 JVM StatsD 指標傳送到節點 `8125` 連接埠。
4. Java Pod 透過 Annotation 將容器日誌交給 DataKit，並使用 `java-selfheal-demo.p` Pipeline 解析關聯欄位。
5. 瀏覽器透過同源 `/rum-proxy` 將 RUM 資料轉發到節點 DataKit，不在前端保存 DataWay token 或 Public DataWay client token。
6. DataKit 使用安裝時提供的 DataWay URL，將所有觀測資料傳送到 TrueWatch 工作區。
