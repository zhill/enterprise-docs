---
title: "Overview"
linkTitle: "Overview"
weight: 3
---

### What is Anchore Enterprise?

**_Anchore Enterprise_** is a Docker container static analysis and policy-based compliance system that automates the inspection, analysis, and evaluation of images against user-defined checks to allow high confidence in container deployments by ensuring workload content meets the required criteria. Anchore ultimately provides a policy evaluation result for each image: pass/fail against policies defined by the user. Additionally, the way that policies are defined and evaluated allows the policy evaluation itself to double as an audit mechanism that allows point-in-time evaluations of specific image properties and content attributes.

## Software Components

- On-Premises Anchore Enterprise 
  - Web UI 
  - API
  - Notifications
  - RBAC
  - Reporting
  - Worker
  - Queue
  - Catalog
  - CLI
- On-Premises Feed Service

![Enterprise Overview](EnterpriseOverview.png)

### Next Steps

Now, let's get familiar with the architecture of Anchore Enterprise.

To begin, go to [Architecture]({{< ref "architecture" >}})
