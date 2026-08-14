---
title : "端到端追踪功能演示"
weight : 32
---

## 端到端追踪功能演示

完成应用部署后，按照以下步骤从浏览器操作逐层定位到后端服务、依赖、日志和基础设施。本 Demo 的正常下单链路为 `gateway → order → MySQL → inventory → Redis → payment`。

### 步骤一：生成商城访问与下单数据

在浏览器打开商城，浏览商品并提交一笔订单。

![01](/static/static-25/01.png)

### 步骤二：分析用户会话

在观测云中前往 **RUM → Explorers → Session**，添加 `project=mall-demo` 过滤条件，然后打开刚刚产生的会话。

![02](/static/static-25/02.png)

### 步骤三：查看会话重放

在会话详情中打开 **Session Replay**，回放商城浏览和下单过程。

![03](/static/static-25/03.png)

### 步骤四：查看相关追踪（Trace）

在商城 Demo 的右下角点击 **Open trace details** 跳转到观测云 Trace 详情。在 Trace 瀑布图中确认 Gateway、order、MySQL、inventory、Redis 与 payment 调用是否完整。

![04](/static/static-25/04.png)

### 步骤五：查看 Span 详细信息

点击 **Locate Current Span** 放大当前调用，依次查看服务入口 Span、MySQL 查询、Redis 调用和 `/api/payments/pay`。重点确认 `service`、`resource`、`project`、`trace_id`、错误状态与执行耗时。

![05](/static/static-25/05.png)

### 步骤六：与主机指标关联分析

切换到 **Host** 或基础设施关联页，将 Trace 与对应 EKS 节点、Pod、容器 CPU 和内存指标关联。所有 Demo 工作负载都应带有 `project=mall-demo`。

![06](/static/static-25/06.png)

### 步骤七：查看主机日志

切换到 **Logs**，查看同一 `trace_id` 下的应用日志。日志中还可以使用 `biz_request_id`、`key_request`、`fault_id`、`pod_name` 和 `container_name` 继续筛选。

![07](/static/static-25/07.png)

### 步骤八：错误调查

在浏览器打开商城，右侧选择 **Backend → Payment 5xx error → inject selected fault** ，随后浏览商品并提交一笔订单。

![08](/static/static-25/08.png)


### 步骤九：查看错误 Trace

打开刚刚产生的错误 Trace，确认 Gateway 返回 HTTP 503，并检查 payment-service 上的错误 Span 与故障字段。

![10](/static/static-25/10.png)

### 步骤十：查看 Profiling

进入 **APM → Profiling**，按 `project=mall-demo` 筛选 Java Profile。可以看到 Gateway、order、inventory 与 payment 服务周期性上报的 CPU Profile。

![11](/static/static-25/11.png)
