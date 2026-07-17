---
title : "Configuration"
weight : 20
---

## Configuration

This workshop completes configuration and verification in the following order:

1. Understand the TrueWatch features used in this lab.
2. Declare EKS and DataWay parameters, then install DataKit using Helm.
3. Create a Web RUM application in TrueWatch and record the Application ID and Workspace ID.
4. Deploy the microservice mall to EKS using public images and obtain the public access URL.
5. Generate business traffic and verify RUM, end-to-end tracing, logs, and Profiling.
6. Create a log Pipeline and verify structured fields.
7. Create monitors and SLOs, and import the product service operational quality dashboard.
8. Deploy Agent Runtime on EKS nodes, use Toby AI and Agent Teams to analyze Traces and alerts, complete the fault-handling closed loop, and clean up Workshop resources.

The parameters that users need to fill in are AWS Region, EKS cluster name, DataWay URL, RUM Application ID, and TrueWatch Workspace ID. `project=mall-demo`, image tag `latest`, Namespace, and Demo internal passwords are fixed by the tutorial or auto-generated.
