---
title : "Toby AI & Agent Teams Demo"
weight : 28
---

## Toby AI & Agent Teams Demo

This chapter builds on the payment anomalies and monitoring alerts generated in the previous chapter to demonstrate Toby AI's ability to analyze error Traces, as well as Agent Teams' ability to receive alerts, access EKS, and generate remediation recommendations.

Toby AI can be used directly within the platform; the Agent Runtime for Agent Teams must be deployed by the user. This Workshop temporarily installs the Runtime on an EKS EC2 worker node and performs a unified cleanup after the demo.

### Step 1: Create the Workshop Agent

Click **Toby AI → Toby Agent Teams** at the top of the platform to enter the Agent Workspace and create an Agent dedicated to this Workshop:

- Quick Start: `Observability Navigator`

Navigate to the Agent's **Run & Deploy** page, select the Linux installation method, and confirm the following from the installation command:

- Agent ID
- Agent API Key
- Beak Endpoint

![01](/static/static-28/01.png)

### Step 2: Deploy Agent Runtime on an EKS Node

The installation script is designed for EKS clusters with EC2 worker nodes running Amazon Linux or Ubuntu with `systemd` support. This step does not apply to Fargate-only clusters, Bottlerocket nodes, or nodes that already have `obs-agent` installed.

This step uses a short-lived privileged helper Pod to access the target node and install the Runtime. It does not modify the EC2 Node Role and does not depend on SSM. The current AWS identity must be able to access EKS and create privileged, hostPath, and hostPID Pods in the cluster. This method should only be executed on the dedicated cluster used for this Workshop.

Verify that the required commands are available in CloudShell:

```shell
aws --version
kubectl version --client
```

Continue using the EKS parameters declared in Chapter 2, and set the Beak Endpoint from the Agent installation command:

```shell
export BEAK_ENDPOINT="https://agent-api.truewatch.com"
```

Run the installation script from the repository:

```shell
scripts/install-obs-agent-eks-node-demo.sh
```

The script automatically performs the following operations:

1. Selects a Ready Linux worker node in the target cluster.
2. Creates a short-lived privileged helper Pod and mounts the target node's root filesystem.
3. Creates a read-only Kubernetes ServiceAccount, RBAC, and a kubeconfig with a default validity of 8 hours.
4. Installs `kubectl` matching the EKS version on the node.
5. Installs the Agent using `Standard` permission mode and opens an encrypted Kubernetes exec interactive session.
6. Automatically deletes the helper Pod when installation completes or the script exits.

In the interactive session, enter the Agent ID and Agent API Key in sequence. The API Key is entered with hidden input and will not appear in Shell history, Pod definitions, or the repository.

![02](/static/static-28/02.png)

If the automatically selected node does not meet your requirements, you can specify a target before re-running:

```shell
export TARGET_INSTANCE_ID="i-xxxxxxxxxxxxxxxxx"
scripts/install-obs-agent-eks-node-demo.sh
```

### Step 3: Verify Agent Runtime

After installation, return to the Agent Workspace and wait for the `Observability Navigator` status to change to **Online**.

Create a read-only verification task:

```text
List the nodes, namespaces, and Pods in the observability-demo namespace of the current EKS cluster. Only read data; do not perform any modifications.
```

Confirm that the Agent can list nodes and Pods.

### Step 4: Prepare Payment Anomaly and Alert Events

In the mall Demo, select **Backend → Payment 5xx error → Inject selected fault**, submit multiple orders consecutively, and wait for the error rate monitor created in Chapter 7 to trigger an alert.

Navigate to **APM → Traces** and search for the anomalous Trace using the following criteria:

- `project=mall-demo`
- `service=payment-service`
- `status=error`

Open a Trace containing a payment-service error Span, confirm that the Gateway returned HTTP 503, and record the Trace ID.

### Step 5: Analyze the Error Trace with Toby AI

On the error Trace detail page, open **Toby AI**, select the current Trace for analysis, and enter:

```text
Analyze the current error Trace. Identify which service the fault originated in, upstream and downstream impact, key evidence, possible root causes, and the recommended remediation order.
```

Verify that the analysis results include the following:

1. Identifies the error Span in payment-service.
2. Explains how the error propagates along `payment → order → gateway`.
3. Uses HTTP status codes, error messages, Trace ID, and correlated logs as evidence.
4. Distinguishes between the deliberately injected Demo fault and real external dependency failures.
5. Provides recommendations for immediate action, follow-up investigation, and ongoing monitoring.

You can ask follow-up questions:

```text
Correlate logs using the same trace_id, confirm the error cause in payment-service, and compile the analysis into a Note.
```

![03](/static/static-28/03.png)

### Step 6: Handle Alert Tasks with Agent Teams

First, navigate to **Monitoring → Alert Strategies** and create or edit the alert strategy used in this Workshop:

1. Under **关联**, add the service error rate, HTTP status code error rate, and response time monitors created in Chapter 7.
2. Expand **通知规则** and set the timezone to `(UTC+09:00) Asia/Tokyo`.
3. Select notification **按等级（Level）** and choose `Observability Navigator` as the notification target for the `Fatal` level. If the monitors use other event levels, add the same Agent notification rule for those levels.
4. Click **保存**. Only events matching the notification level will be delivered to the Agent.

If `Observability Navigator` does not appear in the notification target list, first confirm that the Agent has been created in the current workspace and that the Runtime status is **Online**.

![04](/static/static-28/04.png)

Go to the Agent Workspace, select the deployed `Observability Navigator` that is in Online status, then navigate to **Task Intake** and create or verify the **Workspace hook**:

- Name: `Observability Navigator Event Webhook`
- Intake type: `Workspace`
- Trigger method: `Platform delivery`
- Execution period: `1 minute window`
- Permission mode: Use the current Workshop workspace permissions
- Status: `Active`

![05](/static/static-28/05.png)

Deliver the alert strategy from the Chapter 7 monitors to this task intake, then trigger the payment error again. Confirm that the **Received messages** count increases, and open the new task in the execution history or **My Tasks** to check whether the Agent automatically performs correlated analysis of events, Traces, logs, and EKS resources.

### Step 7: Close the Fault and Verify End-to-End

Return to the mall Demo, disable the current fault, and continue submitting normal orders. After the next monitoring detection cycle, confirm:

1. The error rate monitor returns to normal.
2. The alert event enters recovery status.
3. The Agent task retains the analysis conclusions and remediation recommendations from this anomaly.

You can enter the following in Toby AI to generate a post-incident review:

```text
Compile the payment-service alert, impact scope, root cause evidence, remediation process, and recovery verification from this incident into a retrospective Note.
```

This completes the end-to-end demo covering fault injection, monitoring alerts, AI analysis, Agent processing, and recovery verification.

### Post-Workshop Cleanup

First, clean up the Agent Runtime while the EKS node is still running. This command uses a temporary helper Pod to remove the Agent, Owl credentials, and temporary Kubernetes RBAC from the node, and deletes the helper Pod upon completion:

```shell
scripts/install-obs-agent-eks-node-demo.sh --cleanup
unset BEAK_ENDPOINT TARGET_INSTANCE_ID TARGET_NODE_NAME
```

After confirming the cleanup is complete, delete the Agent and Task Intake created for this Workshop in the Agent Workspace.

Then uninstall the application in CloudShell:

```shell
kubectl config current-context
helm list --all-namespaces

helm uninstall demo -n observability-demo
kubectl delete namespace observability-demo --ignore-not-found
```

After the Gateway Service is deleted, verify in the AWS Console that the Load Balancer created for this Workshop has been released to avoid ongoing charges.

Only when DataKit collection is no longer needed for the EKS cluster, run:

```shell
helm uninstall datakit -n datakit
kubectl delete namespace datakit --ignore-not-found
```
