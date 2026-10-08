# Lab 03 — ClusterRole and Binding Scope

## Key concept

A ClusterRole can be used in two different ways:

### ClusterRole + RoleBinding

The ClusterRole permissions are granted only inside the namespace where the RoleBinding exists.

### ClusterRole + ClusterRoleBinding

The ClusterRole permissions are granted cluster-wide.

## Useful verification

```bash
kubectl auth can-i get pods --as=guna -n dev
kubectl auth can-i get pods --as=guna -n prod
kubectl auth can-i --list --as=guna -n dev
```

## Interview rule

**ClusterRole + RoleBinding = namespace-scoped access**

**ClusterRole + ClusterRoleBinding = cluster-wide access**
