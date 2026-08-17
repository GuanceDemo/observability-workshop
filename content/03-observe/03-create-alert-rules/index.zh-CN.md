---
title : "创建监控器、设置 SLO 并导入监控大屏"
weight : 33
---

## 创建监控器、设置 SLO 并导入监控大屏

本章将导入产品与服务维度的 APM 监控器，基于监控器创建 `Project SLO`，最后导入产品服务运行质量分析大屏。

### 步骤一：下载配置附件

::alert[右键点击以下链接并选择「另存为」，将两个 JSON 文件下载到本地。]{type="info"}

- [product-service-monitor-template.json](https://ws-assets-prod-iad-r-nrt-2cb4b4649d0e0f94.s3.ap-northeast-1.amazonaws.com/24932227-1f86-44e3-acc9-3df44d13d2fc/product-service-monitor-template.json)
- [product-service-operational-quality-dashboard.json](https://ws-assets-prod-iad-r-nrt-2cb4b4649d0e0f94.s3.ap-northeast-1.amazonaws.com/24932227-1f86-44e3-acc9-3df44d13d2fc/product-service-operational-quality-dashboard.json)

### 步骤二：导入并创建监控器

进入 **监控 → 监控器**，点击右上角设置菜单并选择 **导入**，上传 `product-service-monitor-template.json`。

![01](/static/static-27/01.png)

确认导入后创建了 12 个监控器，覆盖以下检测范围：

- 产品、服务和接口级错误率
- HTTP 状态码错误率
- 产品、服务和接口级 P90/P99 响应时间

打开 `产品异常错误率` 和 `产品 P90 响应时间`，确认检测周期为 `5 minutes`，检测数据中可以看到 `project=mall-demo`。

返回监控器列表，确认监控器状态为 **启用**。

### 步骤三：设置产品 SLO

进入 **监控 → SLO**，点击 **新建SLO**，按以下参数创建（服务 SLO 类似）：

- SLO 名称：`产品 SLO`
- SLI 监控器：`产品异常错误率`、`产品 P90 响应时间`
- 目标：`95%`
- 最低目标：`90%`
- 分组维度：`project`
- 分组值：`mall-demo`

![02](/static/static-27/02.png)
![03](/static/static-27/03.png)

保存后确认 SLO 页面可以显示达标率、错误预算和关联监控器。

### 步骤四：导入产品服务运行质量大屏

进入 **场景**，点击 **导入仪表板**，上传 `product-service-operational-quality-dashboard.json`。

确认以下信息后完成导入：

- 仪表板名称：`产品服务运行质量分析大盘`
- 标识 ID：`产品服务运行质量分析大盘`

打开大屏，将顶部 **产品** 变量设置为 `mall-demo`。

### 步骤五：产生异常并验证监控结果

在商城 Demo 中注入支付错误并生成业务流量,等待一个检测周期后，依次检查：

1. **监控 → 监控器** 中相关错误率监控器进入异常状态。
2. **监控 → SLO** 中 `产品 SLO` 开始消耗错误预算。
3. 产品服务运行质量大屏显示错误请求、告警事件和 SLO 变化。

![04](/static/static-27/04.png)

验证完成后关闭故障，并生成正常流量，等待下一个检测周期，确认监控器恢复。
