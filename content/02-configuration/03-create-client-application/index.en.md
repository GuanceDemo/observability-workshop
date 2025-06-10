---
title : "Creating a Client Application for RUM"
weight : 23
---

## Creating a Client Application for RUM

TrueWatch supports integrating Real User Monitoring (RUM) through minimal configuration in front-end applications, enabling end-to-end tracing from frontend to backend. This guide demonstrates creating a client application in TrueWatch to obtain the necessary `clientToken`.

### Step 1: Create New Client Application

The workshop application already includes basic setup. Follow the steps below to create a new client application in TrueWatch and obtain your `clientToken`:

1. In the TrueWatch console, navigate to **RUM → Application → Create**.
2. Select **Web** as the application type.
3. Enter `idurar` for both **Application Name** and **Application ID**.
4. Copy the displayed `clientToken` for later use.
5. Click **Create**

![01](/static/static-23/01.png)

### Step 2: Update RUM Client Token

In your AWS CloudShell terminal, edit the RUM client token in the frontend configuration file:

```
vim ../frontend/src/main.jsx
```

Replace the clientToken on line 10 with the one obtained from TrueWatch → RUM → Application Settings.

![02](/static/static-23/02.png)