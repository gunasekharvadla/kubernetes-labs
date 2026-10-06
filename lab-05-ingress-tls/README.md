# Lab 05 — Kubernetes Ingress + TLS

## Overview

This lab demonstrates production-oriented Kubernetes HTTP/HTTPS traffic routing using **NGINX Ingress Controller, Ingress, Services, TLS, and Kubernetes Secrets**.

The objective is to understand the complete request path:

```text
Client
  ↓
Ingress Controller
  ↓
Ingress Rules
  ↓
Service
  ↓
Service Selector
  ↓
Pods
```

For HTTPS:

```text
Client
  ↓
HTTPS
  ↓
Ingress Controller
  ↓
TLS Secret
  ↓
Ingress Rules
  ↓
Service
  ↓
Pods
```

---

## Architecture

```text
                         Client
                           |
                         HTTPS
                           |
                           ↓
                NGINX Ingress Controller
                           |
                      TLS Secret
                           |
                    Ingress Rules
                     /           \
                    /             \
                   ↓               ↓
            web-service       api-service
                |                  |
          selector=app:web    selector=app:api
                |                  |
             ┌──┴──┐            ┌──┴──┐
             ↓     ↓            ↓     ↓
           WEB   WEB          API    API
           Pod   Pod          Pod    Pod
```

## Routing

| Host                | Service       | Application     |
| ------------------- | ------------- | --------------- |
| `web.example.local` | `web-service` | Web Application |
| `api.example.local` | `api-service` | API Application |

---

## Kubernetes Components Used

* Deployment
* Pods
* ClusterIP Service
* Labels and Selectors
* Ingress
* NGINX Ingress Controller
* IngressClass
* TLS Secret
* Self-signed TLS certificate
* Endpoint/EndpointSlice validation
* HTTP/HTTPS testing

---

## Step 1 — Web Application

Created a Deployment with two replicas.

```yaml
replicas: 2

labels:
  app: web
```

The Pods were verified using:

```bash
kubectl get pods -l app=web -o wide
```

---

## Step 2 — Web Service

Created a ClusterIP Service:

```text
web-service
```

The Service selects:

```yaml
selector:
  app: web
```

Verified backend endpoints:

```bash
kubectl get endpoints web-service
```

The Service successfully discovered both Web Pods.

---

## Step 3 — API Application

Created a second Deployment:

```text
api-app
```

with:

```text
app=api
```

Two replicas were created and verified.

---

## Step 4 — API Service

Created:

```text
api-service
```

with:

```yaml
selector:
  app: api
```

Verified the backend endpoints.

---

## Step 5 — NGINX Ingress Controller

Verified whether an Ingress Controller existed:

```bash
kubectl get pods -A | grep -i ingress
kubectl get ingressclass
```

The cluster initially had no Ingress Controller.

Installed the NGINX Ingress Controller and verified:

```bash
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

---

## Step 6 — Host-Based Ingress Routing

Configured:

```text
web.example.local → web-service
api.example.local → api-service
```

Example:

```yaml
spec:
  ingressClassName: nginx

  rules:
    - host: web.example.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web-service
                port:
                  number: 80

    - host: api.example.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: api-service
                port:
                  number: 80
```

---

## Step 7 — TLS Configuration

Generated a lab certificate:

```bash
openssl req -x509 -nodes -days 365 \
  -newkey rsa:2048 \
  -keyout ingress.key \
  -out ingress.crt \
  -subj "/CN=example.local"
```

Created a Kubernetes TLS Secret:

```bash
kubectl create secret tls ingress-tls \
  --cert=ingress.crt \
  --key=ingress.key
```

Verified:

```bash
kubectl get secret ingress-tls
```

Expected type:

```text
kubernetes.io/tls
```

The Ingress was then configured to use:

```yaml
tls:
  - hosts:
      - web.example.local
      - api.example.local
    secretName: ingress-tls
```

---

## HTTPS Testing

For the local kind cluster, the Ingress Controller was accessed using port-forwarding:

```bash
kubectl -n ingress-nginx port-forward \
  svc/ingress-nginx-controller 8443:443
```

WEB application:

```bash
curl -k \
  -H "Host: web.example.local" \
  https://127.0.0.1:8443
```

API application:

```bash
curl -k \
  -H "Host: api.example.local" \
  https://127.0.0.1:8443
```

Expected application responses:

```text
WEB APPLICATION
Traffic reached WEB service
```

and:

```text
API APPLICATION
Traffic reached API service
```

`-k` was used only because the lab certificate is self-signed.

---

## Troubleshooting Flow

A production troubleshooting approach:

```text
Client
  ↓
DNS
  ↓
Load Balancer / Entry Point
  ↓
Ingress Controller
  ↓
Ingress
  ↓
Service
  ↓
EndpointSlice
  ↓
Pods
  ↓
Application
```

Useful commands:

```bash
kubectl get ingress
```

```bash
kubectl describe ingress devops-ingress
```

```bash
kubectl get ingressclass
```

```bash
kubectl get svc
```

```bash
kubectl get endpoints
```

```bash
kubectl get endpointslice
```

```bash
kubectl get pods -l app=web
```

```bash
kubectl get pods -l app=api
```

```bash
kubectl get secret ingress-tls
```

```bash
kubectl logs -n ingress-nginx \
  deployment/ingress-nginx-controller
```

### Common Failure Scenarios

**404**

Check:

```text
Ingress host
Ingress path
IngressClass
```

**503**

Check:

```text
Ingress
  ↓
Service
  ↓
EndpointSlice
  ↓
Pod
```

Especially verify Service selectors.

**TLS/certificate error**

Check:

```text
TLS Secret
Secret type
Certificate hostname/SAN
Ingress TLS configuration
Ingress Controller logs
```

**Service has no endpoints**

Check:

```text
Service selector
        ↓
Pod labels
        ↓
Pod readiness
```

---

## Production Relevance

In production environments, the same architecture is commonly implemented as:

```text
Internet
   ↓
DNS
   ↓
Cloud Load Balancer
   ↓
Ingress Controller
   ↓
Ingress
   ↓
Services
   ↓
Pods
```

Typical production responsibilities include:

* HTTP/HTTPS routing
* TLS termination
* Certificate management
* Host-based routing
* Path-based routing
* Load balancing
* Ingress Controller monitoring
* 4xx/5xx troubleshooting
* Service/Endpoint validation
* DNS troubleshooting
* Application availability troubleshooting

---

## Key Takeaways

### Pod

Runs the application.

### Service

Provides a stable network endpoint and selects Pods.

### Ingress

Defines HTTP/HTTPS routing rules.

### Ingress Controller

Actually implements those routing rules.

### TLS Secret

Stores the certificate and private key used by the Ingress Controller.

### Complete flow

```text
HTTPS Request
     ↓
Ingress Controller
     ↓
TLS termination
     ↓
Ingress rule
     ↓
Service
     ↓
Selector
     ↓
Pod
```

---

## Interview Questions

1. What is Kubernetes Ingress?
2. What is the difference between Ingress and Ingress Controller?
3. Can an Ingress route traffic directly to a Pod?
4. How does Ingress find the backend Service?
5. What is host-based routing?
6. What is path-based routing?
7. How does TLS work with Kubernetes Ingress?
8. Where is the TLS certificate normally stored?
9. What would you check if Ingress returns 503?
10. Why would an Ingress return 404?
11. How would you troubleshoot an Ingress with no backend endpoints?
12. What is the difference between Service and Ingress?
13. Why is an Ingress Controller required?
14. How would you troubleshoot an HTTPS certificate problem?
15. How would this architecture change in AWS/EKS?

---

## Lab Status

**Completed**

* [x] Web Deployment
* [x] Web Service
* [x] API Deployment
* [x] API Service
* [x] NGINX Ingress Controller
* [x] Host-based routing
* [x] TLS certificate
* [x] Kubernetes TLS Secret
* [x] HTTPS testing
* [x] Ingress troubleshooting concepts

**Environment:** kind Kubernetes cluster
**Focus:** Kubernetes Networking / Ingress / TLS / Troubleshooting
