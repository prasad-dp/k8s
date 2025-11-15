# Kubernetes Namespaces

## Overview
Namespaces are a way to divide cluster resources between multiple users. They provide scope for resource names and enable resource quotas and access control.

## Default Namespaces
- `default`: Default namespace
- `kube-system`: System components
- `kube-public`: Publicly accessible resources
- `kube-node-lease`: Node lease objects

## Creating Namespaces
Namespaces help with:
- **Multi-tenancy**: Separate workloads for different teams/environments
- **Resource Organization**: Logically group related resources
- **Access Control**: Apply RBAC at namespace level
- **Resource Quotas**: Limit CPU, memory per namespace

## Resource Quotas
Define limits per namespace:
- CPU limits
- Memory limits
- Pod count limits
- Storage limits

## Best Practices
1. Create separate namespaces for environments (dev, staging, prod)
2. Use namespace-level RBAC for security
3. Implement resource quotas for cost control
4. Use network policies per namespace
5. Monitor resource usage per namespace
