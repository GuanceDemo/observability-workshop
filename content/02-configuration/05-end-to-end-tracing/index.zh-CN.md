---
title : "端到端追踪功能演示"
weight : 25
---

## 端到端追踪功能演示

按照以下步骤探索 TrueWatch 的端到端追踪能力：

### 步骤一：新增客户数据

登录 Idurar 示范应用，前往 **Customers**，新增任意名称的新客户。

![01](/static/static-25/01.png)

### 步骤二：分析用户会话

在 TrueWatch 中前往 **RUM → Explorers → Session**，点击最新的会话记录。

![02](/static/static-25/02.png)

### 步骤三：探索 Fetch/XHR 请求

点击第一条记录，切换到 **Fetch/XHR** 选项卡，并将延迟按降序排列。

![03](/static/static-25/03.png)

### 步骤四：查看相关追踪（Trace）

点击 `/api/payment/summary`，然后选择 **View Related Trace**。找到并点击最底部的 MongoDB 查询块。

![04](/static/static-25/04.png)

### 步骤五：查看 Span 详细信息

点击 **Locate Current Span** 图标进行放大，向下滚动可查看执行耗时与 MongoDB 查询详情。

![05](/static/static-25/05.png)

### 步骤六：与主机指标关联分析

点击 **Host** 选项卡，将追踪数据与主机层级的指标关联。

![06](/static/static-25/06.png)

### 步骤七：查看主机日志

切换到 **Logs** 选项卡，选择 **host** 查看相关主机的日志信息。

![07](/static/static-25/07.png)

### 步骤八：错误调查

点击 **Back** 返回会话视图，点击 **Error** 条目。

![08](/static/static-25/08.png)

![09](/static/static-25/09.png)

### 步骤九：AI 辅助错误分析

在 **Error Details** 页面点击 **Obsy AI Error Analysis**，由 AI 提供错误诊断与分析建议。

![10](/static/static-25/10.png)
