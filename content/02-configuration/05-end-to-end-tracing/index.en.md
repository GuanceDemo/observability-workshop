---
title : "End-to-End Tracing Demonstration"
weight : 25
---

## End-to-End Tracing Demonstration

Follow these steps to explore TrueWatch's end-to-end tracing capabilities:

### Step 1: Create a New Customer

Log into the idurar demo application, navigate to **Customers**, and add a new customer with any name.

![01](/static/static-25/01.png)

### Step 2: Analyze User Session

In TrueWatch, navigate to **RUM → Explorers → Session** and click on the latest session.

![02](/static/static-25/02.png)

### Step 3: Explore Fetch/XHR Requests

Select the first item, navigate to the **Fetch/XHR** tab, and sort the requests by descending latency.

![03](/static/static-25/03.png)

### Step 4: View Related Trace

Click on `/api/payment/summary`, then select **View Related Trace**. Find and click the MongoDB query block at the bottom.

![04](/static/static-25/04.png)

### Step 5: Inspect Span Details

Click the **Locate Current Span** icon to zoom in. Scroll down to see detailed execution duration and MongoDB query information.

![05](/static/static-25/05.png)

### Step 6: Correlate with Host Metrics

Click the **Host** tab to correlate the trace with host-level metrics.

![06](/static/static-25/06.png)

### Step 7: Review Host Logs

Click on the **Logs** tab and select **host** to review related host logs.

![07](/static/static-25/07.png)

### Step 8: Investigate Errors

Click **Back** to return to the session view, then click on the **Error** entry.

![08](/static/static-25/08.png)

![09](/static/static-25/09.png)

### Step 9: AI-Assisted Error Analysis

On the **Error Details** page, click **Obsy AI Error Analysis** for AI-generated insights and diagnosis.

![10](/static/static-25/10.png)