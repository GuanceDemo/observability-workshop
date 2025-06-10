---
title : "Deployment Architecture"
weight : 30
---

## Overview

The iDURAR application is an open-source Customer Relationship Management (CRM) system, built with a modern frontend and backend architecture. This workshop demonstrates observability for the application deployed within AWS EKS using TrueWatch.

## Deployment Architecture

![01](/static/static-30/01.png)

## Component Description

The architecture consists of the following components:

- **AWS Application Load Balancer (ALB):** Routes incoming requests from end-user browsers.
- **iDURAR Frontend:** React-based user interface integrated with TrueWatch RUM SDK for real user monitoring.
- **iDURAR Backend:** Node.js and Express application processing business logic.
- **MongoDB:** Database providing persistent data storage.
- **Datakit:** DaemonSet deployed across the EKS nodes, collecting metrics, logs, and traces.
- **TrueWatch Platform:** Cloud observability solution for metrics, logs, tracing, alerting, and analytics.