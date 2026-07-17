---
title : "Deployment Architecture"
weight : 12
---

## Architecture Overview

This workshop uses a Java microservice mall designed for observability education. Browser requests first enter the Gateway, then the Order service calls MySQL, Inventory service, Redis, and Payment service. DataKit runs as a DaemonSet on each EKS node, sending infrastructure, APM, logs, JVM, Profiling, and RUM data to TrueWatch.

## Deployment Architecture

![01](/static/static-30/01.png)

## Component Description

The system consists of the following components:

- **AWS Load Balancer:** Automatically created by the Gateway Service of type `LoadBalancer`, providing public DNS for browsers. The specific type is determined by the EKS cluster's load balancer controller.
- **Gateway Service:** The mall's only public entry point, forwarding business and Demo APIs while rejecting external access to internal `/admin/**` endpoints.
- **Order Service:** Hosts the bilingual mall pages, order APIs, RUM proxy, log viewer, and fault control entry points.
- **Inventory Service:** Handles inventory business logic and simulates cache dependency through Redis.
- **Payment Service:** Processes payment calls and provides demonstration scenarios for slow calls, errors, and CPU hotspots.
- **MySQL and Redis:** Use `emptyDir` ephemeral volumes, storing only demo data during the workshop without production-grade persistence or high availability.
- **DataKit:** Deployed as a DaemonSet via a separate Helm release, collecting Kubernetes/container metrics, application logs, Traces, JVM StatsD, Profiling, and RUM.
- **TrueWatch:** Receives data reported by DataKit, providing Explorer, Dashboard, APM, Logs, RUM, Alerting, and AI analysis capabilities.

The application and DataKit use two separate Helm releases: `demo` in the `observability-demo` namespace and `datakit` in the `datakit` namespace. All demo signals are uniformly tagged with `project=mall-demo` for cross-data-type correlation analysis.

## Main Data Flows

1. Browsers access the Gateway through the AWS Load Balancer.
2. A normal order flows through `gateway → order → MySQL → inventory → Redis → payment`.
3. The Java Agent sends Traces and Profiling to the node's port `9529`, and JVM StatsD metrics to port `8125`.
4. Java Pods deliver container logs to DataKit via Annotations, using the `java-selfheal-demo.p` Pipeline to parse correlation fields.
5. The browser forwards RUM data to the node DataKit through the same-origin `/rum-proxy`, without storing the DataWay token or Public DataWay client token in the frontend.
6. DataKit uses the DataWay URL provided during installation to send all observability data to the TrueWatch workspace.
