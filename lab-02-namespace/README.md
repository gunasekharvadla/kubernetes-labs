# Lab 02 — Kubernetes Namespace

## Objective

Demonstrate how Kubernetes Namespaces provide logical organization and isolation of resources within a Kubernetes cluster.

## Scenario

In a production Kubernetes environment, workloads are commonly separated by environment, team, or application.

Example:

```text
Kubernetes Cluster
│
├── dev
├── staging
├── production
├── monitoring
└── ingress


What I Practiced
Creating a Namespace using declarative YAML
Applying Namespace configuration
Deploying a Pod into a specific Namespace
Querying resources within a Namespace
Querying resources across all Namespaces
Inspecting Namespace configuration and events
Understanding default Namespace behavior
Understanding logical workload isolation


Files
lab-02-namespace/
├── README.md
├── namespace.yaml
├── nginx.yaml
└── output/


| Command                                 | Purpose                         |
| --------------------------------------- | ------------------------------- |
| `kubectl apply -f namespace.yaml`       | Create/update Namespace         |
| `kubectl get namespaces`                | List Namespaces                 |
| `kubectl describe namespace devops-dev` | Inspect Namespace               |
| `kubectl get pods -n devops-dev`        | List Pods in Namespace          |
| `kubectl get pods -A`                   | List Pods across all Namespaces |
| `kubectl get all -n devops-dev`         | View resources in Namespace     |



kubectl get pods
        ↓
default namespace

kubectl get pods -n devops-dev
        ↓
devops-dev namespace

kubectl get pods -A
        ↓
all namespaces
