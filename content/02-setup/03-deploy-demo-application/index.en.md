---
title : "Deploy Demo Application"
weight : 23
---

## Deploy Demo Application

This chapter deploys the shopping mall demo to AWS EKS using the public Harbor `2.3.5` images. No image build, ECR configuration, or registry login is required in CloudShell. Except for the Gateway, the order, inventory, payment, MySQL, and Redis services remain cluster-internal.

The Gateway is public and the demo fault endpoints intentionally require no credential. Use this profile only in an isolated, short-lived workshop cluster and clean it up after the exercise.

### Step 1: Deploy the Shopping Mall Demo with Helm

Install the application with the pinned Guance EKS profile:

```shell
helm upgrade --install demo charts/observability-demo \
  --namespace observability-demo \
  --create-namespace \
  -f charts/observability-demo/values-eks.yaml \
  --set-string rum.enabled=true \
  --set-string rum.applicationId="$RUM_APPLICATION_ID" \
  --set-string observability.clusterName="$EKS_CLUSTER_NAME" \
  --set-string observabilityConsole.url="https://console.guance.com/" \
  --set-string observabilityConsole.workspaceId="$GUANCE_WORKSPACE_ID"

unset RUM_APPLICATION_ID GUANCE_WORKSPACE_ID
```

![01](/static/static-24/01.png)

The profile pulls `pubrepo.jiagouyun.com/demo/observability-demo-{gateway,order,inventory,payment}-service:2.3.5` with `IfNotPresent`. The images are public and support `linux/amd64` and `linux/arm64`.

### Step 2: Wait for Workloads to Become Ready

```shell
kubectl wait --for=condition=Available deployment --all \
  --namespace observability-demo \
  --timeout=8m

kubectl -n observability-demo get pods
```

![02](/static/static-24/02.png)

Under normal conditions you should see six workloads: Gateway, order, inventory, payment, MySQL, and Redis. The Java Pods run as a non-root user and send Trace, JVM metrics, and Profiling data to DataKit through the node IP. The application and DataKit use the same `EKS_CLUSTER_NAME` for correlation.

### Step 3: Obtain the Public Access URL

Wait up to ten minutes for either a LoadBalancer hostname or IP:

```shell
deadline=$((SECONDS + 600))
GATEWAY_ADDRESS=""
while [[ -z "$GATEWAY_ADDRESS" && "$SECONDS" -lt "$deadline" ]]; do
  GATEWAY_ADDRESS="$(kubectl -n observability-demo get service \
    -l app.kubernetes.io/component=gateway-service \
    -o jsonpath='{.items[0].status.loadBalancer.ingress[0].hostname}')"
  if [[ -z "$GATEWAY_ADDRESS" ]]; then
    GATEWAY_ADDRESS="$(kubectl -n observability-demo get service \
      -l app.kubernetes.io/component=gateway-service \
      -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')"
  fi
  [[ -n "$GATEWAY_ADDRESS" ]] || sleep 10
done

if [[ -z "$GATEWAY_ADDRESS" ]]; then
  kubectl -n observability-demo describe service \
    -l app.kubernetes.io/component=gateway-service
  exit 1
fi

export DEMO_BASE_URL="http://${GATEWAY_ADDRESS}"
echo "$DEMO_BASE_URL"
```

![03](/static/static-24/03.png)

AWS assigns this public address automatically. No custom domain is required, but the LoadBalancer incurs AWS charges.

### Step 4: Verify the Deployment

Open `$DEMO_BASE_URL` in a browser:

![04](/static/static-24/04.png)
