---
title : "创建 RUM 客户端应用"
weight : 23
---

## 创建 RUM 客户端应用

TrueWatch 通过在前端应用程序中进行简单配置，即可支持用户行为监控（RUM），并实现从前端到后端的端到端追踪。本指南说明如何在 TrueWatch 创建客户端应用并获取所需的 `clientToken`。

### 步骤一：创建新的客户端应用

实验用的应用程序已完成基本配置，请按照以下步骤在 TrueWatch 创建新的客户端应用并获取您的 `clientToken`：

1. 登录 TrueWatch 控制台，点击 **RUM → Application → Create**。
2. 选择 **Web** 作为应用类型。
3. 在「应用名称」与「应用 ID」输入 `idurar`。
4. 复制显示的 `clientToken` 以供后续使用。
5. 点击 **Create** 创建。

![01](/static/static-23/01.png)

### 步骤二：更新 RUM 的 Client Token

编辑前端配置文件中的 RUM client token：

```
vim ../frontend/src/main.jsx
```

将第 10 行的 clientToken 替换为从 TrueWatch → RUM → Application Settings 获取的 token：

![02](/static/static-23/02.png)
