---
title : "创建日志管道"
weight : 26
---

## 创建日志管道

TrueWatch 支持在 Datakit 端（本地端）或平台中央端使用管道脚本处理观测数据。本示例将说明如何使用 Grok 从自定义日志消息字段提取数据。

### 步骤一：创建新的管道

前往 **Logs → Pipelines**，点击 **Create Pipeline**。

![01](/static/static-26/01.png)

### 步骤二：获取示例日志

按照下图填写管道信息，并点击 **Get a Sample**。  
若未立即看到日志，可点击 **+Add** 从 `idurar-frontend` 来源获取更多日志样本。

![02](/static/static-26/02.png)

### 步骤三：使用 Grok 定义解析规则

日志的 `message` 字段包含自定义消息。要解析这些消息，请选择「Manual」模式，将以下管道脚本粘贴到 **Define Parsing Rules** 字段，然后点击 **Test**：

```
# Extract the 'message' field from the log
json(_, message)
# Replace new lines with spaces
replace(message, "\\n", " ")
# Apply the Grok patterns to the 'message' field
grok(message, "> vite --host %{IP:host_ip}")
grok(message, "VITE v%{GREEDYDATA:version} ready in %{NUMBER:ready_time} ms")
grok(message, "➜  Local:   %{URI:local_url}")
grok(message, "➜  Network: %{URI:network_url}")
```

### 步骤四：验证并保存管道配置

在 Return Results 中确认解析结果。完成后点击 Save。
未来相同格式的日志将会自动按照此配置解析。

![03](/static/static-26/03.png)
