# Node Affinity in Kubernetes

## Overview
Node Affinity is a more powerful alternative to Node Selector. It allows you to constrain a Pod to only be able to run on particular nodes, based on labels on the node.

## Types of Node Affinity

### 1. requiredDuringSchedulingIgnoredDuringExecution
- **Hard** constraint
- Pod WILL NOT be scheduled if the condition is not met
- If a pod is already running and the node label changes, the pod continues running

### 2. preferredDuringSchedulingIgnoredDuringExecution
- **Soft** constraint
- Scheduler TRIES to find a matching node but won't fail if none exists
- Useful for non-critical preferences

## Operators
- **In**: Value is in the provided set
- **NotIn**: Value is not in the provided set
- **Exists**: Label key exists (value not checked)
- **DoesNotExist**: Label key does not exist
- **Gt**: Value is greater than (only for numbers)
- **Lt**: Value is less than (only for numbers)

## Use Cases
1. Schedule pods on specific hardware (GPU, SSD nodes)
2. Co-locate related pods on the same node
3. Distribute pods across different zones
4. Use specific node types for workloads
