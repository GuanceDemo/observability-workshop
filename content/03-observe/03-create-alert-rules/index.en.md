---
title : "Create Monitors, Set Up SLO, and Import Dashboard"
weight : 33
---

## Create Monitors, Set Up SLO, and Import Dashboard

In this chapter, you will import APM monitors at the product and service level, create a `Project SLO` based on those monitors, and finally import a product service operational quality analysis dashboard.

### Step 1: Download Configuration Attachments

::alert[Right-click the following links and select "Save As" to download the two JSON files.]{type="info"}

- [product-service-monitor-template.json](https://ws-assets-prod-iad-r-nrt-2cb4b4649d0e0f94.s3.ap-northeast-1.amazonaws.com/24932227-1f86-44e3-acc9-3df44d13d2fc/product-service-monitor-template.json)
- [product-service-operational-quality-dashboard.json](https://ws-assets-prod-iad-r-nrt-2cb4b4649d0e0f94.s3.ap-northeast-1.amazonaws.com/24932227-1f86-44e3-acc9-3df44d13d2fc/product-service-operational-quality-dashboard.json)

### Step 2: Import and Create Monitors

Navigate to **Monitoring → Monitors**, click the settings menu in the upper-right corner, select **Import**, and upload `product-service-monitor-template.json`.

![01](/static/static-27/01.png)

After the import completes, confirm that 12 monitors have been created, covering the following detection scopes:

- Product, service, and interface-level error rates
- HTTP status code error rates
- Product, service, and interface-level P90/P99 response times

Open `Product Error Rate` and `Product P90 Latency`, and verify that the detection interval is set to `5 minutes` and that the detection data shows `project=mall-demo`.

Return to the monitor list and confirm that the monitor status is **Enable**.

### Step 3: Set Up Product SLO

Navigate to **Monitoring → SLO**, click **Create SLO**, and configure the following parameters (Service SLO is similar):

- SLO Name: `Product SLO`
- SLI Monitors: `Product Error Rate`, `Product P90 Latency`
- Target: `95%`
- Minimum Target: `90%`
- Group Dimension: `project`
- Group Value: `mall-demo`

![02](/static/static-27/02.png)
![03](/static/static-27/03.png)

After saving, confirm that the SLO page displays the compliance rate, error budget, and associated monitors.

### Step 4: Import the Product Service Operational Quality Dashboard

Navigate to **Dashboards**, click **New Dashboard → Import Dashboard**, and upload `product-service-operational-quality-dashboard.json`.

Confirm the following information before completing the import:

- Dashboard Name: `Product Service Operational Quality Analysis Dashboard`
- Identifier: `Product Service Operational Quality Analysis Dashboard`

Open the dashboard and set the **Product** variable at the top to `mall-demo`.

### Step 5: Generate Anomalies and Verify Monitoring Results

Inject a payment error in the mall demo and generate business traffic. After one detection interval has elapsed, verify the following in sequence:

1. The relevant error rate monitors in **Monitoring → Monitors** enter an alert state.
2. The `Product SLO` in **Monitoring → SLO** begins consuming error budget.
3. The product service operational quality dashboard displays error requests, alert events, and SLO changes.

![04](/static/static-27/04.png)

After verification is complete, disable the fault injection and generate normal traffic. Wait for the next detection interval and confirm that the monitors recover.
