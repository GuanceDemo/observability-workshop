---
title : "Workshop Introduction"
weight : 01
---

## Workshop Content

This workshop guides you through deploying a fault-injectable Java microservice mall demo on AWS EKS, providing a complete observability hands-on experience from data collection to fault analysis.

You will complete the following tasks in order:

- Install DataKit in EKS using the official Helm Chart.
- Create a Web RUM application and enable Browser Logs, Session Replay, and SourceMap.
- Deploy Gateway, Order, Inventory, Payment, MySQL, and Redis using public GHCR images.
- Generate mall traffic and inject faults to observe Kubernetes metrics, Traces, logs, JVM, Profiling, and RUM data.
- Correlate different types of observability data through a unified `project=mall-demo` tag.

This demo is for educational purposes only: MySQL and Redis use ephemeral storage, and public access is provided by an AWS Load Balancer. It should not be used as a production deployment template.

## Contribute

If you are interested in contributing content or have suggestions and new content requirements, you can contact <yagrxu@amazon.com> or <shannatc@amazon.com>
