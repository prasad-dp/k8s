# Kubernetes Pod Deployment - Nginx

This folder contains Kubernetes pod deployment configurations with practical examples.

## Files

- **nginx-pod.yaml** - Pod deployment configuration for Nginx web server with health probes and resource limits

## What is a Pod?

A Pod is the smallest deployable unit in Kubernetes. It typically contains a single container, though it can contain multiple containers that share network and storage resources.

## Nginx Pod Configuration

The nginx-pod.yaml file includes:

- **Container Image:** nginx:latest
- **Port:** 80 (HTTP)
- **Resource Requests:** CPU 100m, Memory 128Mi
- **Resource Limits:** CPU 500m, Memory 512Mi
- **Liveness Probe:** Checks pod health every 10 seconds after 30-second delay
- **Readiness Probe:** Checks pod readiness every 5 seconds after 5-second delay

## Running the Pod

### Single Command to Deploy the Nginx Pod:

kubectl apply -f nginx-pod.yaml

### Other Useful Commands:

**Check pod status:**
kubectl get pods
kubectl get pods -o wide
kubectl describe pod nginx-pod

**View logs:**
kubectl logs nginx-pod
kubectl logs nginx-pod -f

**Port forward to access Nginx:**
kubectl port-forward nginx-pod 8080:80

**Delete the pod:**
kubectl delete pod nginx-pod
kubectl delete -f nginx-pod.yaml

**Get pod details in YAML format:**
kubectl get pod nginx-pod -o yaml

## Key Features

1. **Labels:** app: nginx - For identifying and selecting pods
2. **Health Checks:** Liveness and Readiness probes for pod health monitoring
3. **Resource Management:** CPU and Memory limits for efficient resource utilization
4. **Named Port:** HTTP port is named for better visibility

## Prerequisites

- Kubernetes cluster running (minikube, Docker Desktop, EKS, etc.)
- kubectl CLI tool installed and configured

## Summary

This is a production-ready pod configuration with best practices including health checks, resource constraints, and proper labeling.
