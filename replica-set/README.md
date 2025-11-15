# Replica Sets in Kubernetes

## Overview
A ReplicaSet ensures that a specified number of pod replicas are running at any given time. It is used to maintain a stable set of replica pods running.

## Why Replica Sets?
- **High Availability**: Maintains desired number of pod replicas
- **Self-healing**: Automatically replaces failed pods
- **Scaling**: Easy horizontal scaling of applications
- **Load Distribution**: Distributes traffic across replicas

## ReplicaSet vs Pod
- **Pod**: Single instance of application (can fail)
- **ReplicaSet**: Maintains specified replicas (self-heals)

## Comparison with Deployment
- **ReplicaSet**: Lower-level controller, direct replica management
- **Deployment**: Higher-level abstraction, provides rolling updates, rollbacks
- **Recommendation**: Use Deployments instead of ReplicaSets directly

## Key Components
1. **Selector**: Identifies pods using labels
2. **Replicas**: Number of pods to maintain
3. **Template**: Pod template specification

## Scale Operations
```bash
kubectl scale replicaset rs-name --replicas=5
kubectl get replicasets
kubectl describe rs rs-name
kubectl delete rs rs-name --cascade=false  # Delete RS but keep pods
```

## Best Practices
1. Always use Deployments for production (provides rolling updates)
2. Use appropriate selector labels
3. Set resource requests and limits
4. Use health probes (liveness/readiness)
5. Monitor ReplicaSet status and pod health
