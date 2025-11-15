# Pod Affinity and Pod Anti-Affinity in Kubernetes

## Overview
Pod Affinity and Pod Anti-Affinity allow you to constrain which nodes your pod is eligible to be scheduled on based on the labels on other pods that are already running on the nodes.

## Pod Affinity
**Attracts** pods together - ensures pods are scheduled on the same node, rack, or zone as specific pods.

### Use Cases:
- Co-locate frontend and backend services for low latency
- Place cache pods near database pods
- Ensure related services run together

## Pod Anti-Affinity
**Repels** pods from each other - ensures pods are NOT scheduled on the same node as specific pods.

### Use Cases:
- Distribute replicas across nodes for high availability
- Prevent multiple instances of same service on one node
- Spread critical services across different failure domains

## Topology Key
Specifies the node label used to define the domain of affinity.

### Common Topology Keys:
- `kubernetes.io/hostname`: Pod runs on different nodes
- `topology.kubernetes.io/zone`: Pod runs in different zones
- `topology.kubernetes.io/region`: Pod runs in different regions

## Types:
1. **requiredDuringSchedulingIgnoredDuringExecution**: Hard constraint
2. **preferredDuringSchedulingIgnoredDuringExecution**: Soft constraint
