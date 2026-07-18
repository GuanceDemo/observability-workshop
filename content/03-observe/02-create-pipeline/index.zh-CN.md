---
title : "创建日志管道"
weight : 31
---

## 创建日志管道

TrueWatch 支持在 DataKit 端或平台中央端使用 Pipeline 处理日志。本节将创建中央 Pipeline，从 Demo 的完整原始日志中提取链路、业务、故障和运行环境字段。

### 步骤一：创建新的管道

进入 **Logs → Pipelines**，点击 **Create Pipeline**。

![01](/static/static-26/01.png)

### 步骤二：获取示例日志

填写以下管道信息：

- Pipeline 类型：**Local Pipeline**
- Pipeline 名称：`mall-demo-log`
- 筛选条件：`source=java_selfheal_demo`

点击 **Get a Sample** 获取示例日志。

选择一条 `message` 中包含 `source=java_selfheal_demo` 的日志作为样本。

![02](/static/static-26/02.png)

### 步骤三：定义解析规则

选择 **Manual** 模式，将以下 Pipeline 脚本粘贴到 **Define Parsing Rules**，然后点击 **Test**：

```text
grok(_, "%{TIMESTAMP_ISO8601:time}\\s+%{LOGLEVEL:status}\\s+\\[%{DATA:thread_name}\\]\\s+%{NOTSPACE:logger_name}\\s+-\\s+%{GREEDYDATA:log_message}\\s+\\|\\s+source=%{DATA:source}\\s+service=%{DATA:service}\\s+env=%{DATA:env}\\s+version=%{DATA:version}\\s+project=%{DATA:log_project}\\s+trace_id=%{DATA:trace_id}\\s+span_id=%{DATA:span_id}\\s+key_request=%{DATA:key_request}\\s+biz_request_id=%{DATA:biz_request_id}\\s+language=%{DATA:language}\\s+fault_id=%{DATA:fault_id}\\s+fault_layer=%{DATA:fault_layer}\\s+fault_kind=%{DATA:fault_kind}\\s+fault_target=%{DATA:fault_target}\\s+process_id=%{DATA:process_id}\\s+host_process_id=%{DATA:host_process_id}\\s+container_process_id=%{DATA:container_process_id}\\s+host=%{DATA:host}\\s+host_name=%{DATA:host_name}\\s+pod_name=%{DATA:pod_name}\\s+pod_namespace=%{DATA:pod_namespace}\\s+container_name=%{DATA:container_name}\\s+container_id=%{NOTSPACE:container_id}")
set_tag(project, log_project)
drop_key(log_project)
default_time(time, "Asia/Shanghai")
```

### 步骤四：验证并保存管道

在 **Return Results** 中确认日志已提取出 `trace_id`、`service`、`key_request`、`biz_request_id`、`language`、`fault_id`、`pod_name` 等字段。业务正文保存在 `log_message`，完整原始内容继续保存在 `message`。

点击 **Save** 保存管道。

![03](/static/static-26/03.png)

### 步骤五：查看解析结果

进入 **Logs → Explorer**，使用以下条件筛选日志：

- `project=mall-demo`
- `source=java_selfheal_demo`

再次生成一笔订单，等待日志上报后打开日志详情，确认平台创建的 Pipeline 已生成链路、业务、故障和运行环境属性。
