---
title : "Introduction to the TrueWatch Observability Workshop"
weight : 10
---

## Introduction to the TrueWatch Observability Workshop

This workshop introduces the TrueWatch observability platform through deploying and monitoring a fault-injectable microservice mall demo on AWS EKS (Elastic Kubernetes Service). The goal is to provide participants with a complete hands-on experience covering Kubernetes and container metrics, application performance monitoring, log analysis, JVM monitoring, Profiling, user experience monitoring, alert management, and end-to-end tracing.

### About the Microservice Mall Demo

This demo is a Java microservice mall application designed for observability education. It demonstrates the call relationships between different services and dependencies through a complete order flow. Key features include:

- Built with **Spring Boot** — four Java microservices: Gateway, Order, Inventory, and Payment
- Uses **MySQL** for order data persistence and **Redis** for simulated inventory caching
- Supports deployment to standard Kubernetes environments like AWS EKS via **Helm**
- Built-in fault scenarios at the frontend, service, JVM, and dependency layers for demonstrating correlated analysis of metrics, logs, Traces, Profiling, RUM, Browser Logs, and Session Replay

This workshop guides you through installing DataKit on an AWS EKS cluster, deploying the microservice mall demo, generating business traffic, injecting faults, and using TrueWatch to complete full-stack observability and problem analysis from user access to backend services and infrastructure.

### TrueWatch Observability Platform Features

TrueWatch is a comprehensive observability solution providing application performance, infrastructure health, real user monitoring (RUM), end-to-end tracing, log analysis, and alert management. Key features include:

- **Unified Monitoring:** Metrics, logs, and traces in a single platform.
- **Interactive Dashboards:** Quickly visualize observability data.
- **End-to-End Tracing:** Trace application performance from frontend to backend.
- **Real-Time User Experience Analysis:** Analyze user behavior and performance.
- **Comprehensive Log Management:** Centralized log ingestion and parsing.
- **Synthetic Monitoring:** Proactive service availability monitoring.
- **Custom Alerting:** Powerful alert rules and notification capabilities.
- **AI Error Analysis:** Automatic diagnosis and root cause analysis through AI.

### Workshop Environment Setup

The workshop guides participants through the following hands-on steps:

- Connect to AWS EKS and install DataKit via the official Helm Chart.
- Create a Web RUM application and receive RUM, Browser Logs, and Session Replay data through node DataKit.
- Deploy the microservice mall using public GHCR images — no need to build images in the workshop environment.
- Correlate Kubernetes metrics, APM, logs, JVM, Profiling, and RUM data through `project=mall-demo`.
- Generate business traffic, inject recoverable faults, and configure alerting and correlated analysis workflows.

### Preparation Steps

Before starting, ensure the following prerequisites are met:

1. **TrueWatch Cloud Account:**
   - Register at [https://ap1-auth.truewatch.com/businessRegister](https://ap1-auth.truewatch.com/businessRegister) and create a Workspace (defaults to Commercial version).
2. **AWS EKS Cluster:**
   - The cluster must have at least one worker node in `Ready` state and allow creating `LoadBalancer` type Services.
3. **AWS CloudShell Permissions:**
   - The current identity must be able to read EKS cluster information and create Namespaces, Secrets, Services, Deployments, DaemonSets, Roles, and RoleBindings in the cluster.

**Note:**  
TrueWatch uses "Workspaces" to isolate observability data across different environments or applications. Multiple environments can share data through authorization. The DataWay URL contains a sensitive token — do not include it in documents, screenshots, or Git repositories.
