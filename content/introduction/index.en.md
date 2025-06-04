---
title: 'Introduction'
weight: 11
---

## Introduction to the TrueWatch Observability Workshop

This workshop introduces the TrueWatch Observability Platform by demonstrating how to integrate and monitor the open-source iDURAR CRM application on AWS EKS (Elastic Kubernetes Service). The goal is to provide participants hands-on experience with key observability capabilities, including real-time metrics, logs, user experience monitoring, alerting, and tracing.

### About iDURAR CRM Application

iDURAR is an open-source CRM platform designed to simplify customer relationship management through intuitive workflows and integrations. It features:

- Modern frontend built with **React**
- Backend developed in **Node.js and Express**
- Database support using **MongoDB**
- Designed for deployment within containerized environments such as Kubernetes

In this workshop, we'll deploy and monitor the iDURAR application within an AWS EKS cluster.

### TrueWatch Observability Platform Features

TrueWatch is a comprehensive observability solution, providing insights into application performance, infrastructure health, real user monitoring (RUM), end-to-end tracing, log analysis, and alert management. Key features include:

- **Unified Monitoring:** Metrics, logs, and traces in a single platform.
- **Interactive Dashboards:** Quickly visualize observability data.
- **End-to-End Tracing:** Trace application performance from frontend to backend services.
- **Real-Time User Experience Insights:** Analyze user behavior and performance.
- **Comprehensive Log Management:** Centralized log ingestion and parsing capabilities.
- **Synthetic Testing:** Proactive monitoring of service availability.
- **Customizable Alerting:** Robust alert rules and notifications.
- **AI-Driven Error Analysis:** Automatically diagnose issues through AI-based root cause analysis.

### Workshop Environment Setup

The workshop guides participants through:

- Deploying iDURAR on AWS EKS with Datakit (TrueWatch's observability agent).
- Integrating frontend applications with RUM SDK for comprehensive user monitoring.
- Implementing pipelines for log parsing and metric collection.
- Configuring advanced alert rules to proactively detect anomalies.
- Utilizing AI-assisted tracing for rapid error analysis and troubleshooting.

### Preparation Steps

Before starting, ensure the following prerequisites:

1. **TrueWatch Cloud Account:**
   - Register at [https://id1-auth.truewatch.com/businessRegister](https://id1-auth.truewatch.com/businessRegister) and create a workspace.

**Note:**  
TrueWatch workspaces isolate observability data per environment or application. Multiple environments can share data through proper authorization setups.