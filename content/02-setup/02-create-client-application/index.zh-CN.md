---
title : "创建 RUM 客户端应用"
weight : 22
---

## 创建 RUM 客户端应用

观测云 RUM 用于采集商城页面的访问性能、Resource、Error、Browser Logs 与 Session Replay，并与后端 Trace 关联。

### 步骤一：创建新的客户端应用

按照以下步骤在观测云创建 Web 类型的客户端应用：

1. 登录[观测云控制台](https://console.guance.com/)，点击 **用户访问监测 → 应用列表 → 新建应用**。
2. 选择 **Web** 作为应用类型。
3. 在「应用名称」中输入 `Mall Demo`，应用 ID 输入 `mall_demo`。
4. 点击 **创建**，复制 **Application ID** 和当前工作空间的 **Workspace ID**。

![01](/static/static-23/01.png)

![02](/static/static-23/02.png)

### 步骤二：声明 Application ID 和 Workspace ID

回到之前使用的 AWS CloudShell，声明以下参数：

```shell
read -rp 'RUM Application ID: ' RUM_APPLICATION_ID && export RUM_APPLICATION_ID
read -rp '观测云 Workspace ID: ' GUANCE_WORKSPACE_ID && export GUANCE_WORKSPACE_ID
```

![03](/static/static-23/03.png)

下一章会通过 Helm 将 RUM Application ID 和观测云 Workspace ID 传入应用。

### 步骤三：准备 SourceMap 文件

Demo 已包含用于演示源码还原的压缩 JavaScript 与 SourceMap。在仓库根目录执行：

```shell
scripts/package-rum-sourcemap.sh --version 2.3.1

SOURCE_MAP_FILE="$(realpath dist/observability-demo-rum-sourcemap-2.3.1.zip)"
ls -lh "$SOURCE_MAP_FILE"
echo "$SOURCE_MAP_FILE"
```

![04](/static/static-23/04.png)

随后将 AWS CloudShell 里面的 observability-demo-rum-sourcemap-2.3.1.zip 文件下载到浏览器默认下载目录：

1. 点击 AWS CloudShell 右上角的 操作（Actions）。
2. 选择 下载文件（Download file）。
3. 粘贴上面输出的绝对路径。
4. 点击下载，文件会保存到浏览器默认下载目录。

![05](/static/static-23/05.png)

生成的文件位于 `dist/observability-demo-rum-sourcemap-2.3.1.zip`。进入刚刚创建的 RUM 应用设置，在 **SourceMap** 页面上传该压缩包，并填写：

- Environment：`demo`
- Version：`2.3.1`

![06](/static/static-23/06.png)

应用部署完成后，可以在 Demo 页面启用 `SourceMap 源码定位错误`，再点击商城中的购买按钮。上传 SourceMap 前，错误栈指向压缩文件；上传后应还原到 `assets/src/checkout-sourcemap-fault.js` 中的源码位置。
