# Kubernetes Lab 03 — Labels & Selectors

## Overview

This lab focuses on Kubernetes **Labels and Selectors**, which are fundamental mechanisms used to identify, organize, and dynamically select Kubernetes resources.

The lab demonstrates how label-based selection works and how selector mismatches can lead to operational issues such as workloads not being discovered by Services or controllers.

---

## Objectives

* Create Kubernetes Pods with multiple labels
* Inspect labels attached to Kubernetes resources
* Query resources using label selectors
* Use multiple label selectors
* Understand how selectors establish relationships between Kubernetes resources
* Practice troubleshooting selector mismatches

---

## Architecture

```text
Kubernetes Pod
     │
     ├── app=web
     ├── environment=dev
     └── tier=frontend
              │
              ▼
       Label Selector
              │
              ▼
       Matching Pod(s)
```

---

## Pod Manifest

The lab creates a Pod with multiple labels:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: web-pod
  labels:
    app: web
    environment: dev
    tier: frontend

spec:
  containers:
    - name: nginx
      image: nginx:1.27
      ports:
        - containerPort: 80
```

---

## Key Commands

### Create the Pod

```bash
kubectl apply -f pod.yaml
```

### Verify Pod

```bash
kubectl get pods
```

### Display Labels

```bash
kubectl get pods --show-labels
```

### Select by Application

```bash
kubectl get pods -l app=web
```

### Select by Environment

```bash
kubectl get pods -l environment=dev
```

### Select by Tier

```bash
kubectl get pods -l tier=frontend
```

### Use Multiple Selectors

```bash
kubectl get pods -l app=web,environment=dev
```

---

## Important Concept

### Label

A label is a key-value pair attached to a Kubernetes resource.

Example:

```text
app=web
environment=dev
tier=frontend
```

### Selector

A selector is used to find Kubernetes resources matching specific labels.

Example:

```bash
kubectl get pods -l app=web
```

Simple way to remember:

```text
Label     → What is this object?

Selector  → Which objects do I want?
```

---

## Production Relevance

Labels and selectors are not just metadata.

They are heavily used by Kubernetes components and resources such as:

* Deployments
* ReplicaSets
* Services
* NetworkPolicies
* Monitoring systems
* Workload organization
* Automation and operational tooling

A selector mismatch can cause real production issues.

For example:

```text
Service
   │
   │ selector: app=frontend
   ▼
Pods
   │
   ├── app=web
   └── app=backend
```

The Service will not find these Pods because:

```text
app=frontend
```

does not match:

```text
app=web
app=backend
```

This can result in a Service having **no matching endpoints**.

---

## Troubleshooting Scenario

### Problem

A Kubernetes Service is created, but traffic is not reaching the application.

### First checks

```bash
kubectl get svc
```

```bash
kubectl get endpoints
```

```bash
kubectl get pods --show-labels
```

Then compare the Service selector with the Pod labels:

```bash
kubectl describe svc <service-name>
```

The selector and Pod labels must match.

---

## Operational Takeaway

A large percentage of Kubernetes workload relationships depend on correct label/selector configuration.

When troubleshooting a Service, Deployment, ReplicaSet, or NetworkPolicy, always verify:

```text
Resource
   ↓
Selector
   ↓
Pod Labels
   ↓
Matching Objects
```

Do not assume the resource relationship is correct just because all objects show `Running`.

---

## Actual Lab Output

The `output/` directory contains command outputs captured directly from the Kubernetes lab environment.

Examples:

```text
01-get-pods.txt
02-show-labels.txt
03-selector-app-web.txt
04-multiple-selector.txt
```

This provides hands-on execution evidence for the lab.

---

## Interview Questions

### What is a Kubernetes Label?

A label is a key-value pair attached to a Kubernetes object for identification and grouping.

### What is a Selector?

A selector is used to identify Kubernetes resources based on their labels.

### What is the difference between a Label and Selector?

```text
Label     → metadata attached to an object
Selector  → query used to find matching objects
```

### Why are Labels and Selectors important?

They provide the mechanism Kubernetes uses to logically associate and select resources.

### What happens when a Service selector does not match Pod labels?

The Service cannot discover those Pods, resulting in no matching endpoints.

### How would you troubleshoot this?

```bash
kubectl get pods --show-labels
kubectl describe svc <service-name>
kubectl get endpoints
```

Then compare:

```text
Service selector
       ↓
Pod labels
```

---

## Lab Philosophy

```text
Learn
  ↓
Deploy
  ↓
Verify
  ↓
Break
  ↓
Troubleshoot
  ↓
Fix
  ↓
Document
```

This lab follows the same operational approach used throughout the Kubernetes hands-on series.

---

## Author

**Vadla Gunasekhar**

GitHub: https://github.com/gunasekharvadla

LinkedIn: https://www.linkedin.com/in/gunasekharvadla/

---

## Kubernetes Hands-on Series

| Lab    | Topic                           |
| ------ | -------------------------------- |
| Lab 01 | Pod Lifecycle & Troubleshooting |
| Lab 02 | Namespace Management            |
| Lab 03 | Labels & Selectors              |
| Next   | ReplicaSets                     |
