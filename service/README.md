# Kubernetes Services

## Overview
A Service is an abstraction that defines a logical set of Pods and a policy for accessing them. Services provide stable IP addresses and DNS names for Pods that may be ephemeral.

## Why Services?
- Pods are ephemeral (created and destroyed dynamically)
- Services provide a stable endpoint for accessing Pods
- Load balancing across multiple Pod replicas
- Service discovery within the cluster
- External access to applications

## Service Types

### 1. ClusterIP (Default)
- Exposes service on a cluster-internal IP
- Only accessible from within the cluster
- Used for internal communication

### 2. NodePort
- Exposes service on each Node's IP at a static port
- Accessible from outside the cluster (Node IP:NodePort)
- Range: 30000-32767

### 3. LoadBalancer
- Provisions an external load balancer
- Accessible from outside via load balancer IP
- Available on cloud providers

### 4. ExternalName
- Maps service to an external DNS name
- Returns CNAME record
- No proxying involved

## Service Discovery
- **DNS**: `service-name.namespace.svc.cluster.local`
- **Environment Variables**: Injected into Pod environment

## Best Practices
1. Use ClusterIP for internal services
2. Use NodePort for development/testing
3. Use LoadBalancer for production external access
4. Use appropriate selectors for Pod discovery
5. Define resource requests and limits
6. Use health checks (liveness/readiness probes)
