# Lab 01 — Kubernetes Pod

## Objective

Learn how to create, inspect, troubleshoot, access, and delete a Kubernetes Pod.

## What I Practiced

- Creating a Pod using YAML
- Understanding `apiVersion`, `kind`, `metadata`, and `spec`
- Checking Pod status
- Viewing Pod details
- Viewing container logs
- Executing commands inside a container
- Port forwarding
- Understanding Pod lifecycle
- Deleting a Pod

## Pod Architecture

```text
Kubernetes Cluster
       |
       v
    Pod
       |
       v
   Nginx Container
       |
       v
      Port 80


| Command                                      | Purpose                  |
| -------------------------------------------- | ------------------------ |
| `kubectl apply -f pod1.yaml`                 | Create the Pod           |
| `kubectl get pods`                           | List Pods                |
| `kubectl get pods -o wide`                   | Show Pod IP and Node     |
| `kubectl describe pod nginx-pod`             | Detailed Pod information |
| `kubectl logs nginx-pod`                     | View container logs      |
| `kubectl exec -it nginx-pod -- /bin/bash`    | Enter the container      |
| `kubectl port-forward pod/nginx-pod 8080:80` | Access Nginx locally     |
| `kubectl get pod --show-labels`              | Display Pod labels       |
| `kubectl delete pod nginx-pod`               | Delete the Pod           |

