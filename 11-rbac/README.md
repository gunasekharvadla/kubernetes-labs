# Kubernetes RBAC Labs

Production-oriented hands-on labs for Kubernetes Role-Based Access Control (RBAC).

## Objective

Practice how Kubernetes controls **who can perform which actions on which resources and in which scope**.

## Labs

| Lab | Topic | Key Skill |
|---|---|---|
| 01 | Role + RoleBinding | Namespace-scoped user permissions |
| 02 | ServiceAccount RBAC | Application identity and least privilege |
| 03 | ClusterRole + Bindings | Namespace vs cluster-wide access |
| 04 | RBAC Troubleshooting | Diagnosing incorrect bindings |
| 05 | Secret Access | Fine-grained access with `resourceNames` |

## Core Concepts

- Role
- RoleBinding
- ClusterRole
- ClusterRoleBinding
- ServiceAccount
- RBAC verbs
- API groups
- Namespace-scoped vs cluster-scoped access
- Least privilege
- `kubectl auth can-i`
- `resourceNames`

## RBAC Mental Model

```
WHO
 ↓
User / ServiceAccount
 ↓
Binding
 ↓
Role / ClusterRole
 ↓
Verb + Resource
 ↓
Namespace / Cluster scope
 ↓
ALLOW
```

## Production Takeaways

- RBAC permissions are additive.
- Kubernetes RBAC has no explicit deny rule.
- Prefer least privilege.
- Avoid wildcard permissions unless there is a justified administrative requirement.
- ServiceAccounts should receive only the permissions required by the workload.
- `kubectl auth can-i` is one of the first tools to use when investigating RBAC authorization failures.

## Environment

- Kind Kubernetes cluster
- kubectl
- Linux VM

These labs were executed and tested in a local Kubernetes practice environment.
