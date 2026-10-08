# Lab 02 — ServiceAccount RBAC

## Scenario

Give an application ServiceAccount read-only access to Pods and Services in the `monitoring` namespace.

## Objects

- Namespace: `monitoring`
- ServiceAccount: `monitor-sa`
- Role: `monitoring-reader`
- RoleBinding: `monitoring-reader-binding`

## Test identity

```bash
kubectl auth can-i get pods --as=system:serviceaccount:monitoring:monitor-sa -n monitoring
kubectl auth can-i get services --as=system:serviceaccount:monitoring:monitor-sa -n monitoring
kubectl auth can-i delete pods --as=system:serviceaccount:monitoring:monitor-sa -n monitoring
kubectl auth can-i get secrets --as=system:serviceaccount:monitoring:monitor-sa -n monitoring
```

Expected: Pod/Service reads are allowed; delete and Secret access are denied.

## Production lesson

Applications should use dedicated ServiceAccounts with only the permissions they require.
