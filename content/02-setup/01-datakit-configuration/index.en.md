---
title : "Configure DataKit on AWS EKS"
weight : 21
---

## Configure DataKit on AWS EKS

Follow the steps below to integrate DataKit with Guance in Amazon EKS. Unless otherwise noted (e.g., Guance console operations), all commands in this chapter are executed in the same AWS CloudShell terminal.

### Step 1: Prepare Information and Declare Parameters

Before you begin, gather the following information:

- The EKS cluster name.
- The DataWay URL provided by your Guance workspace.

Log in to the [Guance console](https://console.guance.com/), navigate to **Integration → DataKit → Kubernetes(Helm)**, and copy the full value of `datakit.dataway_url`:

![01](/static/static-22/01.png)

Open AWS CloudShell and declare all parameters used in this chapter. The DataWay URL uses hidden input and will not appear in shell history:

```shell
export EKS_CLUSTER_NAME="eks-demo-cluster"
read -rsp 'DataWay URL: ' DATAWAY_URL && export DATAWAY_URL && echo
```

This demo uses fixed values `project=mall-demo`, DataKit Namespace `datakit`, and application Namespace `observability-demo`; no additional declarations are needed. The DataWay URL contains a sensitive token — do not write it directly into YAML files, documentation, or Git repositories.

### Step 2: Verify Command-Line Tools

AWS CloudShell typically provides the AWS CLI and `kubectl`. First, check for Helm:

```shell
helm version --short
```

If `helm` is not found, install Helm to CloudShell's persistent directory `$HOME/bin`:

```shell
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
mkdir -p "$HOME/bin"
HELM_INSTALL_DIR="$HOME/bin" USE_SUDO=false ./get_helm.sh
rm -f get_helm.sh
export PATH="$HOME/bin:$PATH"
grep -qxF 'export PATH="$HOME/bin:$PATH"' "$HOME/.bashrc" || \
  echo 'export PATH="$HOME/bin:$PATH"' >> "$HOME/.bashrc"
helm version --short
```

This ensures Helm remains available even after CloudShell is reopened.

### Step 3: Connect to the Target EKS Cluster

Use the cluster name declared in Step 1 to generate the kubeconfig for the current CloudShell session. AWS CloudShell supplies the current Region automatically:

```shell
aws eks update-kubeconfig \
  --name "$EKS_CLUSTER_NAME"

kubectl config current-context
kubectl get nodes
```

![02](/static/static-22/02.png)

When `kubectl get nodes` shows at least one node with `Ready` status, the connection is successful. If the current context already points to the target cluster, you can skip `aws eks update-kubeconfig`, but you should still run the two verification commands above.

### Step 4: Clone the Demo Repository

```shell
export DEMO_VERSION="2.3.5"
git clone --branch "v${DEMO_VERSION}" --depth 1 \
  https://github.com/GuanceDemo/observability-demo.git
cd observability-demo
```

All subsequent Helm and script commands are executed from the repository root directory.

### Step 5: Install DataKit Using Helm

Add the official DataKit Chart repository and install the pinned version `2.5.0`:

```shell
helm repo add datakit https://pubrepo.guance.com/chartrepo/datakit
helm repo update

helm upgrade --install datakit datakit/datakit \
  --version 2.5.0 \
  --namespace datakit \
  --create-namespace \
  -f observability/datakit-values.example.yaml \
  --set-string datakit.dataway_url="$DATAWAY_URL" \
  --set-string datakit.cluster_name_k8s="$EKS_CLUSTER_NAME"

unset DATAWAY_URL
```

The values file in the repository enables Kubernetes/container metrics, DDTrace, JVM StatsD, Profiling, and RUM. The actual DataWay URL is written into a Kubernetes Secret by the Chart and is not stored in repository files.

Check the DaemonSet and Pod status:

```shell
kubectl get pods -n datakit
kubectl logs -n datakit daemonset/datakit --tail=200 | grep 'add input'
```

![03](/static/static-22/03.png)

### Step 6: Verify DataKit Integration Status in Guance

After a few minutes, verify that data is being sent to Guance correctly:

1. Go to **Infrastructure** and view EKS cluster node information.
	![04](/static/static-22/04.png)
2. Navigate to **Containers → Pods** to view basic information and performance metrics for the DataKit Pods.
	![05](/static/static-22/05.png)
3. Click **Analysis Dashboard** to view the detailed Kubernetes analysis dashboard.
	![06](/static/static-22/06.png)
4. Check **Logs** to view Kubernetes component logs and cluster events.
	![07](/static/static-22/07.png)

At this point the mall application has not been deployed yet, so APM, application logs, JVM, Profiling, and RUM data will appear in later chapters after the application is installed and traffic is generated.
