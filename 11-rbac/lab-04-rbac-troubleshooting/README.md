# Lab 04 — RBAC Troubleshooting

## Scenario

An application receives an authorization failure because the RoleBinding points to the wrong ServiceAccount.

## Troubleshooting flow

```text
403 Forbidden
   ↓
Identify WHO
   ↓
Check namespace
   ↓
Check resource + verb
   ↓
Inspect Role / ClusterRole
   ↓
Inspect RoleBinding / ClusterRoleBinding
   ↓
Verify subject name
   ↓
kubectl auth can-i
```

## Verification

```bash
kubectl auth can-i get pods --as=system:serviceaccount:rbac-test:app-sa -n rbac-test
kubectl get rolebinding -n rbac-test
kubectl describe rolebinding <binding-name> -n rbac-test
```

## Production lesson

A correct Role is not enough. The Binding must reference the correct identity.
