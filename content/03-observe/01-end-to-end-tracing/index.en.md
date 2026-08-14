---
title : "End-to-End Tracing Demonstration"
weight : 32
---

## End-to-End Tracing Demonstration

After completing the application deployment, follow the steps below to trace from browser actions through to backend services, dependencies, logs, and infrastructure. The normal order flow in this demo is `gateway → order → MySQL → inventory → Redis → payment`.

### Step 1: Generate Store Traffic and Order Data

Open the store in a browser, browse products, and submit an order.

![01](/static/static-25/01.png)

### Step 2: Analyze the User Session

In Guance, navigate to **RUM → Explorers → Session**, add the filter `project=mall-demo`, and open the session that was just generated.

![02](/static/static-25/02.png)

### Step 3: View Session Replay

In the session details, open **Session Replay** to play back the store browsing and order submission process.

![03](/static/static-25/03.png)

### Step 4: View Related Traces

In the bottom-right corner of the store demo, click **Open trace details** to navigate to the Guance Trace details. In the Trace waterfall chart, verify that the Gateway, order, MySQL, inventory, Redis, and payment calls are complete.

![04](/static/static-25/04.png)

### Step 5: View Span Details

Click **Locate Current Span** to zoom in on the current call. Inspect the service entry Span, MySQL queries, Redis calls, and `/api/payments/pay` in sequence. Focus on confirming `service`, `resource`, `project`, `trace_id`, error status, and execution duration.

![05](/static/static-25/05.png)

### Step 6: Correlate with Host Metrics

Switch to the **Host** or infrastructure correlation page to associate the Trace with the corresponding EKS node, Pod, container CPU, and memory metrics. All demo workloads should carry the label `project=mall-demo`.

![06](/static/static-25/06.png)

### Step 7: View Host Logs

Switch to **Logs** to view application logs under the same `trace_id`. You can further filter logs using `biz_request_id`, `key_request`, `fault_id`, `pod_name`, and `container_name`.

![07](/static/static-25/07.png)

### Step 8: Error Investigation

Open the store in a browser. On the right side, select **Backend → Payment 5xx error → inject selected fault**, then browse products and submit an order.

![08](/static/static-25/08.png)

### Step 9: View the Error Trace

Open the error Trace that was just generated. Confirm that the Gateway returns HTTP 503, and inspect the error Span and fault fields on payment-service.

![10](/static/static-25/10.png)

### Step 10: View Profiling

Navigate to **APM → Profiling** and filter Java Profiles by `project=mall-demo`. You will see CPU Profiles periodically reported by the Gateway, order, inventory, and payment services.

![11](/static/static-25/11.png)
