---
title : "Create Log Pipeline"
weight : 26
---

## Create Log Pipeline

TrueWatch supports processing logs using Pipelines either on the DataKit side or on the platform's central side. In this section, you will create a central Pipeline to extract trace, business, fault, and runtime environment fields from the complete raw logs of the Demo application.

### Step 1: Create a New Pipeline

Navigate to **Logs → Pipelines** and click **Create Pipeline**.

![01](/static/static-26/01.png)

### Step 2: Obtain a Sample Log

Fill in the following pipeline information:

- Pipeline type: **Local Pipeline**
- Pipeline name: `mall-demo-log`
- Filter condition: `source=java_selfheal_demo`

Click **Get a Sample** to retrieve a sample log.

Select a log entry whose `message` contains `source=java_selfheal_demo` as the sample.

![02](/static/static-26/02.png)

### Step 3: Define Parsing Rules

Select **Manual** mode, paste the following Pipeline script into **Define Parsing Rules**, then click **Test**:

```text
grok(_, "%{TIMESTAMP_ISO8601:time}\\s+%{LOGLEVEL:status}\\s+\\[%{DATA:thread_name}\\]\\s+%{NOTSPACE:logger_name}\\s+-\\s+%{GREEDYDATA:log_message}\\s+\\|\\s+source=%{DATA:source}\\s+service=%{DATA:service}\\s+env=%{DATA:env}\\s+version=%{DATA:version}\\s+project=%{DATA:log_project}\\s+trace_id=%{DATA:trace_id}\\s+span_id=%{DATA:span_id}\\s+key_request=%{DATA:key_request}\\s+biz_request_id=%{DATA:biz_request_id}\\s+language=%{DATA:language}\\s+fault_id=%{DATA:fault_id}\\s+fault_layer=%{DATA:fault_layer}\\s+fault_kind=%{DATA:fault_kind}\\s+fault_target=%{DATA:fault_target}\\s+process_id=%{DATA:process_id}\\s+host_process_id=%{DATA:host_process_id}\\s+container_process_id=%{DATA:container_process_id}\\s+host=%{DATA:host}\\s+host_name=%{DATA:host_name}\\s+pod_name=%{DATA:pod_name}\\s+pod_namespace=%{DATA:pod_namespace}\\s+container_name=%{DATA:container_name}\\s+container_id=%{NOTSPACE:container_id}")
set_tag(project, log_project)
drop_key(log_project)
default_time(time, "Asia/Shanghai")
```

### Step 4: Verify and Save the Pipeline

In **Return Results**, confirm that the log has been parsed into fields such as `trace_id`, `service`, `key_request`, `biz_request_id`, `language`, `fault_id`, and `pod_name`. The business message body is stored in `log_message`, while the complete raw content remains in `message`.

Click **Save** to save the pipeline.

![03](/static/static-26/03.png)

### Step 5: View Parsing Results

Navigate to **Logs → Explorer** and filter logs using the following conditions:

- `project=mall-demo`
- `source=java_selfheal_demo`

Generate another order, wait for the logs to be reported, then open the log details to confirm that the Pipeline created on the platform has successfully generated trace, business, fault, and runtime environment attributes.
