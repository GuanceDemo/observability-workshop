---
title : "建立日誌管道"
weight : 31
---

## 建立日誌管道

TrueWatch 支援在 DataKit 端或平台中央端使用 Pipeline 處理日誌。本節將建立中央 Pipeline，從 Demo 的完整原始日誌中提取鏈路、業務、故障和執行環境欄位。

### 步驟一：建立新的管道

進入 **Logs → Pipelines**，點選 **Create Pipeline**。

![01](/static/static-26/01.png)

### 步驟二：取得範例日誌

填寫以下管道資訊：

- Pipeline 類型：**Local Pipeline**
- Pipeline 名稱：`mall-demo-log`
- 篩選條件：`source=java_selfheal_demo`

點選 **Get a Sample** 取得範例日誌。

選擇一條 `message` 中包含 `source=java_selfheal_demo` 的日誌作為樣本。

![02](/static/static-26/02.png)

### 步驟三：定義解析規則

選擇 **Manual** 模式，將以下 Pipeline 指令碼貼到 **Define Parsing Rules**，然後點選 **Test**：

```text
grok(_, "%{TIMESTAMP_ISO8601:time}\\s+%{LOGLEVEL:status}\\s+\\[%{DATA:thread_name}\\]\\s+%{NOTSPACE:logger_name}\\s+-\\s+%{GREEDYDATA:log_message}\\s+\\|\\s+source=%{DATA:source}\\s+service=%{DATA:service}\\s+env=%{DATA:env}\\s+version=%{DATA:version}\\s+project=%{DATA:log_project}\\s+trace_id=%{DATA:trace_id}\\s+span_id=%{DATA:span_id}\\s+key_request=%{DATA:key_request}\\s+biz_request_id=%{DATA:biz_request_id}\\s+language=%{DATA:language}\\s+fault_id=%{DATA:fault_id}\\s+fault_layer=%{DATA:fault_layer}\\s+fault_kind=%{DATA:fault_kind}\\s+fault_target=%{DATA:fault_target}\\s+process_id=%{DATA:process_id}\\s+host_process_id=%{DATA:host_process_id}\\s+container_process_id=%{DATA:container_process_id}\\s+host=%{DATA:host}\\s+host_name=%{DATA:host_name}\\s+pod_name=%{DATA:pod_name}\\s+pod_namespace=%{DATA:pod_namespace}\\s+container_name=%{DATA:container_name}\\s+container_id=%{NOTSPACE:container_id}")
set_tag(project, log_project)
drop_key(log_project)
default_time(time, "Asia/Shanghai")
```

### 步驟四：驗證並儲存管道

在 **Return Results** 中確認日誌已提取出 `trace_id`、`service`、`key_request`、`biz_request_id`、`language`、`fault_id`、`pod_name` 等欄位。業務正文儲存在 `log_message`，完整原始內容繼續儲存在 `message`。

點選 **Save** 儲存管道。

![03](/static/static-26/03.png)

### 步驟五：檢視解析結果

進入 **Logs → Explorer**，使用以下條件篩選日誌：

- `project=mall-demo`
- `source=java_selfheal_demo`

再次產生一筆訂單，等待日誌上報後開啟日誌詳情，確認平台建立的 Pipeline 已產生鏈路、業務、故障和執行環境屬性。
