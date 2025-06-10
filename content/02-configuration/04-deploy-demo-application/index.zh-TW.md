---
title : "部署示範應用程式"
weight : 24
---

## 部署示範應用程式

請依以下步驟將示範應用程式部署至 AWS EKS：

### 步驟一：啟用部署腳本權限

確保部署腳本可執行：

```
chmod +x bootstrap.sh build-and-push.sh deploy.sh
```

![01](/static/static-24/01.png)

### 步驟二：確認 AWS Region 設定

確認並更新 values.env 檔案第 6 行的 AWS Region (如果需要，到您的 AWS 區域)：

```
vim values.env
```

![02](/static/static-24/02.png)

### 步驟三：初始化環境

執行以下指令設定環境：

```
./bootstrap.sh
```
![04](/static/static-24/04.png)

### 步驟四：建置並推送至 ECR

將應用程式建置並推送至 Amazon ECR。此過程可能需要約 5 分鐘：

```
./build-and-push.sh
```

![05](/static/static-24/05.png)

### 步驟五：部署應用程式與 Datakit

部署應用程式並重新部署 Datakit，以使用最新的環境變數：

```
./deploy.sh

kubectl delete -f datakit.yaml && kubectl apply -f datakit.yaml
```

![06](/static/static-24/06.png)

### 步驟六：取得應用程式 URL

執行以下指令取得應用程式的外部 URL：

```
kubectl get all -n idurar-demo
```

![07](/static/static-24/07.png)

### 步驟七：登入示範應用程式

複製 service/idurar-frontend 的外部 IP 或 URL 至瀏覽器，並使用以下帳號登入：

- 帳號: admin@demo.com
- 密碼: admin123

![08](/static/static-24/08.png)

