---
title : "建立 RUM 客戶端應用"
weight : 22
---

## 建立 RUM 客戶端應用

TrueWatch RUM 用於採集商城頁面的存取效能、Resource、Error、Browser Logs 與 Session Replay，並與後端 Trace 關聯。

### 步驟一：建立新的客戶端應用

按照以下步驟在 TrueWatch 建立 Web 類型的客戶端應用：

1. 登入 TrueWatch 控制台，點擊 **RUM → Application → Create**。
2. 選擇 **Web** 作為應用類型。
3. 在「應用名稱」中輸入 `Mall Demo`，應用 ID 輸入 `mall_demo`。
4. 選擇 **Local Deployment** **CDN Synchronous Loading**。
5. 點擊 **Create**，複製 **Application ID** 和當前工作空間的 **Workspace ID**。

![01](/static/static-23/01.png)

![02](/static/static-23/02.png)

### 步驟二：宣告 Application ID 和 Workspace ID

回到之前使用的 AWS CloudShell，宣告以下參數：

```shell
read -rp 'RUM Application ID: ' RUM_APPLICATION_ID && export RUM_APPLICATION_ID
read -rp 'TrueWatch Workspace ID: ' TRUEWATCH_WORKSPACE_ID && export TRUEWATCH_WORKSPACE_ID
```

![03](/static/static-23/03.png)

下一章會透過 Helm 將 RUM Application ID 和 TrueWatch Workspace ID 傳入應用。

### 步驟三：準備 SourceMap 檔案

Demo 已包含用於演示原始碼還原的壓縮 JavaScript 與 SourceMap。在倉庫根目錄執行：

```shell
scripts/package-rum-sourcemap.sh

SOURCE_MAP_FILE="$(realpath dist/observability-demo-rum-sourcemap.zip)"
ls -lh "$SOURCE_MAP_FILE"
echo "$SOURCE_MAP_FILE"
```

![04](/static/static-23/04.png)

隨後將 AWS CloudShell 裡面的 observability-demo-rum-sourcemap.zip 檔案下載到瀏覽器預設下載目錄：

1. 點擊 AWS CloudShell 右上角的 **Actions**。
2. 選擇 **Download file**。
3. 貼上上面輸出的絕對路徑。
4. 點擊下載，檔案會儲存到瀏覽器預設下載目錄。

![05](/static/static-23/05.png)

產生的檔案位於 `dist/observability-demo-rum-sourcemap.zip`。進入剛剛建立的 RUM 應用設定，在 **SourceMap** 頁面上傳該壓縮包，並填寫：

- Environment：`demo`
- Version：`latest`

![06](/static/static-23/06.png)

應用部署完成後，可以在 Demo 頁面啟用 `frontend_sourcemap_error`，再點擊商城中的購買按鈕。上傳 SourceMap 前，錯誤堆疊指向壓縮檔案；上傳後應還原到 `assets/src/checkout-sourcemap-fault.js` 中的原始碼位置。
