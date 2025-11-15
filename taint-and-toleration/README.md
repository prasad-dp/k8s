# Taints and Tolerations in Kubernetes

## Overview
Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. Taints mark nodes to repel pods, while tolerations allow pods to tolerate those taints.

## Taints
A taint is a property of a node that indicates which pods should NOT be scheduled on that node.

### Taint Format:
```
key=value:effect
```

### Taint Effects:
1. **NoSchedule**: New pods will not be scheduled on the node
2. **PreferNoSchedule**: Kubernetes tries to avoid placing new pods on the node
3. **NoExecute**: Existing pods on the node will be evicted if they don't tolerate the taint

### Example: Apply Taint to a Node
```bash
kubectl taint nodes node1 key=value:NoSchedule
```

## Tolerations
A toleration is a property of a pod that allows it to be scheduled on nodes with matching taints.

### Example: Pod with Toleration
See the accompanying YAML files for practical examples.

## Use Cases
1. **Dedicated Nodes**: Reserve nodes for specific workloads
2. **GPU Nodes**: Ensure only GPU-capable pods run on GPU nodes
3. **High-Performance Nodes**: Restrict premium nodes to important applications
4. **Node Maintenance**: Safely drain nodes before maintenance

## Key Points
- A node can have multiple taints
- A pod can have multiple tolerations
- A pod can only be scheduled if it tolerates ALL taints on a node
