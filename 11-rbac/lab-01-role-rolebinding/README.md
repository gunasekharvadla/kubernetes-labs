# Lab 01 — Role + RoleBinding

## Scenario

Give user `guna` read-only access to Pods in the `dev` namespace.

## Setup

```bash
kubectl create namespace dev
kubectl apply -f role.yaml
kubectl apply -f rolebinding.yaml
```

## Permission tests

```bash
kubectl auth can-i get pods --as=guna -n dev
kubectl auth can-i list pods --as=guna -n dev
kubectl auth can-i delete pods --as=guna -n dev
kubectl auth can-i create pods --as=guna -n dev
kubectl auth can-i --list --as=guna -n dev
```

Expected: get/list are allowed; create/delete are denied.

> `--as=guna` is used for authorization testing/impersonation. It does not create a real Kubernetes user.
