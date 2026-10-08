# Lab 05 — Fine-Grained Secret Access

## Scenario

Allow an application ServiceAccount to read one specific Secret while denying access to other Secrets.

## Key technique

Use `resourceNames`:

```yaml
resources: ["secrets"]
resourceNames: ["app-secret"]
verbs: ["get"]
```

## Tests

```bash
kubectl auth can-i get secret/app-secret --as=system:serviceaccount:secret-lab:app-sa -n secret-lab
kubectl auth can-i get secret/db-secret --as=system:serviceaccount:secret-lab:app-sa -n secret-lab
kubectl auth can-i delete secret/app-secret --as=system:serviceaccount:secret-lab:app-sa -n secret-lab
kubectl auth can-i list secrets --as=system:serviceaccount:secret-lab:app-sa -n secret-lab
```

Expected: only `get` on `app-secret` is allowed.

## Security note

Kubernetes Secret data is commonly represented as Base64 in manifests/API output. Base64 is encoding, not encryption. Access control still needs to be enforced with RBAC and appropriate cluster Secret encryption controls.
