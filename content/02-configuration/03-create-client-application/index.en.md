---
title : "Create RUM Client Application"
weight : 23
---

## Create RUM Client Application

TrueWatch RUM is used to collect page access performance, Resource, Error, Browser Logs, and Session Replay from the mall pages, and correlate them with backend Traces.

### Step 1: Create a New Client Application

Follow these steps to create a Web-type client application in TrueWatch:

1. Log in to the TrueWatch console, click **RUM → Application → Create**.
2. Select **Web** as the application type.
3. Enter `Mall Demo` for the application name, and `mall_demo` for the application ID.
4. Select **Local Deployment** **CDN Synchronous Loading**.
5. Click **Create**, then copy the **Application ID** and the current workspace's **Workspace ID**.

![01](/static/static-23/01.png)

![02](/static/static-23/02.png)

### Step 2: Declare Application ID and Workspace ID

Return to the AWS CloudShell you used earlier and declare the following parameters:

```shell
read -rp 'RUM Application ID: ' RUM_APPLICATION_ID && export RUM_APPLICATION_ID
read -rp 'TrueWatch Workspace ID: ' TRUEWATCH_WORKSPACE_ID && export TRUEWATCH_WORKSPACE_ID
```

![03](/static/static-23/03.png)

In the next chapter, the RUM Application ID and TrueWatch Workspace ID will be passed to the application via Helm.

### Step 3: Prepare SourceMap Files

The demo already includes compressed JavaScript and SourceMap files for demonstrating source code restoration. Run the following in the repository root directory:

```shell
scripts/package-rum-sourcemap.sh

SOURCE_MAP_FILE="$(realpath dist/observability-demo-rum-sourcemap.zip)"
ls -lh "$SOURCE_MAP_FILE"
echo "$SOURCE_MAP_FILE"
```

![04](/static/static-23/04.png)

Next, download the observability-demo-rum-sourcemap.zip file from AWS CloudShell to your browser's default download directory:

1. Click **Actions** in the upper-right corner of AWS CloudShell.
2. Select **Download file**.
3. Paste the absolute path from the output above.
4. Click download; the file will be saved to your browser's default download directory.

![05](/static/static-23/05.png)

The generated file is located at `dist/observability-demo-rum-sourcemap.zip`. Go to the settings of the RUM application you just created, upload the zip file on the **SourceMap** page, and fill in:

- Environment: `demo`
- Version: `latest`

![06](/static/static-23/06.png)

After the application is deployed, you can enable `frontend_sourcemap_error` on the Demo page, then click the purchase button in the mall. Before uploading the SourceMap, the error stack points to the compressed file; after uploading, it should resolve to the source location in `assets/src/checkout-sourcemap-fault.js`.
