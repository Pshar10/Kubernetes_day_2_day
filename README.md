# Kubernetes Command Reference Guide

This document is a comprehensive and practical guide to essential Kubernetes commands used in day-to-day cluster operations. Each command is explained with its syntax, flags, and real-world use cases, making it ideal for DevOps engineers, developers, and SREs working with Kubernetes environments.

---

## Namespace-Specific Resource Management

### View all resources in a namespace
```bash
kubectl get all -n webapps
````

Check the overall health and status of applications in the `webapps` namespace.

### List all pods

```bash
kubectl get pods -n webapps
```

Quickly view which pods are running, pending, or failing.

---

## Deployment Lifecycle Management

### Restart a deployment

```bash
kubectl rollout restart deployment bankapp -n webapps
```

Used when updating config maps or secrets that affect pods.

### Rollback a deployment

```bash
kubectl rollout undo deployment bankapp -n webapps
```

Reverts to a previously stable version after a bad deployment.

### Check deployment history

```bash
kubectl rollout history deployment bankapp -n webapps
```

Audit or compare previous deployment revisions.

---

## Pod Inspection & Debugging

### Describe a pod

```bash
kubectl describe pod <pod-name> -n webapps
```

Get events, status, and configuration details.

### View logs from a crashed container

```bash
kubectl logs -p <pod-name> -n webapps
```

Useful for post-crash investigations.

### Execute command inside a pod

```bash
kubectl exec <pod-name> -n webapps -- df -h
```

Check disk usage and mounted volumes.

### Simulate application crash

```bash
kubectl exec <pod-name> -n webapps -- kill 1
```

Used in resilience testing scenarios.

---

## Secrets Management

### List secrets

```bash
kubectl get secret -n webapps
```

### Decode a secret

```bash
kubectl get secret mysecret -n webapps -o jsonpath="{.data.password}" | base64 --decode
```

View the actual value of a secret (e.g., password) in human-readable format.

---

## Networking & Connectivity

### Check service endpoints

```bash
kubectl get endpoints bankapp-service -n webapps
```

### Run MySQL client to test DNS/service

```bash
kubectl run mysql-client --image=mysql --rm -it -- mysql -h mysql-service -u root -p<password>
```

### Manual readiness check

```bash
kubectl exec -it <pod-name> -n webapps -- wget -qO- localhost:80/login
```

---

## Persistent Storage

### Check PVC and StorageClasses

```bash
kubectl get pvc
kubectl get sc
```

### Enable dynamic volume expansion

```bash
kubectl patch storageclass <sc-name> -p '{"allowVolumeExpansion":true}'
```

### Expand volume for a PVC

```bash
kubectl patch pvc <pvc-name> -p '{"spec":{"resources":{"requests":{"storage":"15Gi"}}}}' -n webapps
```

---

## Cluster and Node Maintenance

### List nodes

```bash
kubectl get nodes
```

### Drain a node for maintenance

```bash
kubectl drain <node-name> --ignore-daemonsets
```

### Uncordon node after maintenance

```bash
kubectl uncordon <node-name>
```

---

## Autoscaling & Monitoring

### Create a Horizontal Pod Autoscaler

```bash
kubectl autoscale deployment bankapp --cpu-percent=50 --min=1 --max=5 -n webapps
```

### Monitor autoscaling

```bash
kubectl get hpa -n webapps
kubectl describe hpa bankapp -n webapps
```

### Install metrics server

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

### Live monitoring

```bash
watch kubectl top pods -n webapps
```

---

## Utilities & Troubleshooting

### Backup pod data

```bash
kubectl cp <pod-name>:/var/lib/mysql ./mysql-backup -n webapps
```

### View deployment images

```bash
kubectl get deployments -n webapps -o jsonpath="{range .items[*]}{.metadata.name}{': '}{.spec.template.spec.containers[*].image}{'\\n'}"
```

### Compare local manifest with live

```bash
kubectl diff -f <manifest.yaml>
```

### Force delete stuck pod

```bash
kubectl delete pod <pod-name> --force --grace-period=0 -n webapps
```

---

## About

This reference is created to support Kubernetes practitioners in real production environments. It focuses on actionable commands with context relevant to live operations—handling rollouts, backups, scaling, and diagnostics.
