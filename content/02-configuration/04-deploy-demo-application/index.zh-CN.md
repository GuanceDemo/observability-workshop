---
title : "部署示范应用"
weight : 24
---

## 部署示范应用

请按以下步骤将示范应用部署至 AWS EKS：

### 步骤一：启用部署脚本权限

确保部署脚本可执行：

```
chmod +x bootstrap.sh build-and-push.sh deploy.sh
```

![01](/static/static-24/01.png)

### 步骤二：确认 AWS Region 设置

确认并更新 values.env 文件第 6 行的 AWS Region（如果需要，改为您的 AWS 区域）：

```
vim values.env
```

![02](/static/static-24/02.png)

### 步骤三：初始化环境

执行以下命令设置环境：

```
./bootstrap.sh
```
![04](/static/static-24/04.png)

### 步骤四：构建并推送至 ECR

将应用构建并推送至 Amazon ECR。此过程可能需要约 5 分钟：

```
./build-and-push.sh
```

![05](/static/static-24/05.png)

### 步骤五：部署应用与 Datakit

部署应用并重新部署 Datakit，以使用最新的环境变量：

```
./deploy.sh

kubectl delete -f datakit.yaml && kubectl apply -f datakit.yaml
```

![06](/static/static-24/06.png)

### 步骤六：获取应用 URL

执行以下命令获取应用的外部 URL：

```
kubectl get all -n idurar-demo
```

![07](/static/static-24/07.png)

### 步骤七：登录示范应用

复制 service/idurar-frontend 的外部 IP 或 URL 到浏览器，并使用以下账号登录：

- 账号: admin@demo.com
- 密码: admin123

![08](/static/static-24/08.png)
