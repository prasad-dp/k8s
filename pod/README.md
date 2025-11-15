

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
