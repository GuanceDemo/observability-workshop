---
title : "Configure Datakit on AWS EKS"
weight : 22
---

## Configure Datakit on AWS EKS

Follow these steps to set up Datakit integration with TrueWatch on Amazon EKS. All shell commands are executed in the AWS CloudShell terminal.

### Step 1: Clone the Workshop Repository

Open AWS CloudShell and run the following command in the terminal:

```shell
git clone https://github.com/TrueWatchTech/idurar-demo-workshop
```

### Step 2: Obtain Dataway Endpoint

Log in to TrueWatch Cloud, navigate to Integration → Datakit → Kubernetes, and copy the ENV_DATAWAY value:

![01](/static/static-22/01.png)

### Step 3: Update Datakit Configuration

Replace YOUR-ENV-DATAWAY in the datakit.yaml file (line 166) with the value copied above:

```
cd idurar-demo-workshop/workshop
vim datakit.yaml  # Use ":set nu" to show line numbers
```

![02](/static/static-22/02.png)

### Step 4: Update Kubernetes Configuration

Run the following command to configure your Kubernetes context for the EKS cluster:

```
aws eks --region ap-southeast-1 update-kubeconfig --name eks-demo-cluster
```

![03](/static/static-22/03.png)

### Step 5: Deploy Datakit to Kubernetes

Execute these commands to deploy and verify Datakit:

```
# Deploy Datakit
kubectl apply -f datakit.yaml

# Check deployment status
kubectl get pods -n datakit
```

### Step 6: Verify Datakit Integration on TrueWatch

After a few minutes, verify that data is sent correctly to TrueWatch:

1. Click Infrastructure to view the EKS cluster nodes.
	![04](/static/static-22/04.png)
2. Navigate to Containers → Pods to view Pod metrics and details.
	![05](/static/static-22/05.png)
3. Access the Analysis Dashboard for detailed analysis.
	![06](/static/static-22/06.png)
4. Check Network → Map for network topology traffic flows. The following screenshot illustrates data flow from EKS nodes to TrueWatch:
	![07](/static/static-22/07.png)
5. View logs for the kube-proxy component and cluster events under Logs.
	![08](/static/static-22/08.png)