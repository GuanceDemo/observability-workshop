---
title : "Deploy Demo Application"
weight : 23
---

## Deploy Demo Application

This chapter deploys the shopping mall demo to AWS EKS using public GHCR images — no need to build images or configure ECR in CloudShell. Except for the Gateway, the order, inventory, payment, MySQL, and Redis services are only accessible within the cluster.

### Step 1: Deploy the Shopping Mall Demo with Helm

Run the following command to install the application:

```shell
helm upgrade --install demo charts/observability-demo \
  --namespace observability-demo \
  --create-namespace \
  -f charts/observability-demo/values-eks.yaml \
  --set rum.enabled=true \
  --set-string rum.applicationId="$RUM_APPLICATION_ID" \
  --set-string observabilityConsole.workspaceId="$TRUEWATCH_WORKSPACE_ID"

unset RUM_APPLICATION_ID TRUEWATCH_WORKSPACE_ID
```

![01](/static/static-24/01.png)

### Step 2: Wait for Workloads to Become Ready

```shell
kubectl wait --for=condition=Available deployment --all \
  --namespace observability-demo \
  --timeout=8m

kubectl -n observability-demo get pods
```

![02](/static/static-24/02.png)

Under normal conditions you should see six workloads: Gateway, order, inventory, payment, MySQL, and Redis. The Java Pods run as a non-root user and send Trace, JVM metrics, and Profiling data to DataKit via the node IP.

### Step 3: Obtain the Public Access URL

AWS typically takes a few minutes to provision a Load Balancer. Run the following command to wait for the Gateway's public address:

```shell
GATEWAY_HOST=""
until [ -n "$GATEWAY_HOST" ]; do
  GATEWAY_HOST="$(kubectl -n observability-demo get service \
    -l app.kubernetes.io/component=gateway-service \
    -o jsonpath='{.items[0].status.loadBalancer.ingress[0].hostname}')"
  [ -n "$GATEWAY_HOST" ] || sleep 10
done

kubectl -n observability-demo get service \
  -l app.kubernetes.io/component=gateway-service
```

![03](/static/static-24/03.png)

This is a public DNS automatically assigned by AWS — no custom domain is required. The Load Balancer incurs AWS charges; please clean up using the commands at the end of this chapter after the workshop.

### Step 4: Open the Shopping Mall and Verify the Deployment

Open the `EXTERNAL-IP` in your browser. The shopping mall page requires no login and supports switching between Chinese and English. The first time you perform a fault operation, the page will prompt you to enter a fault-control token, which is stored only in the current browser's `sessionStorage`.

Retrieve the token in CloudShell and run the automated verification:

```shell
printf '%s\n' "$(kubectl -n observability-demo get secret demo-observability-demo \
  -o jsonpath='{.data.demo-control-token}' | base64 --decode)"
```

![04](/static/static-24/04.png)
