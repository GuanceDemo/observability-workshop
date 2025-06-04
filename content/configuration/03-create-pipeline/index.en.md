---
title : "Creating a Log Pipeline"
weight : 22
---

## Creating a Log Pipeline

TrueWatch supports using pipeline scripts either locally (on Datakit) or centrally on the platform for processing observability data. This example demonstrates how to extract data from custom log message fields using Grok.

### Step 1: Create a New Pipeline

Navigate to **Logs → Pipelines** and click **Create Pipeline**.

![01](/static/static-3/01.png)

### Step 2: Retrieve Sample Logs

Fill out the pipeline information as shown below, then click **Get a Sample**.  
If you don't see sample logs immediately, click **+Add** to retrieve more logs from the `idurar-frontend` source.

![02](/static/static-3/02.png)

### Step 3: Define Parsing Rules with Grok

The `message` field contains custom log entries. To parse these logs, paste the following pipeline script into the **Define Parsing Rules** section and click **Test**:

```
# Extract the 'message' field from the log
json(_, message)
# Replace new lines with spaces
replace(message, "\\n", " ")
# Apply the Grok patterns to the 'message' field
grok(message, "> vite --host %{IP:host_ip}")
grok(message, "VITE v%{GREEDYDATA:version} ready in %{NUMBER:ready_time} ms")
grok(message, "➜  Local:   %{URI:local_url}")
grok(message, "➜  Network: %{URI:network_url}")
```

### Step 4: Verify and Save Pipeline

Confirm extracted values in the Return Results section. Once verified, click Save.
Future logs matching this format will automatically be parsed accordingly.

![03](/static/static-3/03.png)