# Kubernetes (K8s) - Comprehensive Guide

## Table of Contents
1. [What is Kubernetes?](#what-is-kubernetes)
2. [Why We Need Kubernetes](#why-we-need-kubernetes)
3. [Docker vs Kubernetes](#docker-vs-kubernetes)
4. [Docker Swarm vs Kubernetes](#docker-swarm-vs-kubernetes)
5. [Kubernetes Architecture](#kubernetes-architecture)
6. [Control Plane](#control-plane)
7. [Data Plane](#data-plane)
8. [Workloads](#workloads)
9. [ConfigMaps and Secrets](#configmaps-and-secrets)
10. [Services](#services)
11. [Ingress and Ingress Controller](#ingress-and-ingress-controller)
12. [Taints and Tolerations](#taints-and-tolerations)
13. [Node Selector and Node Affinity](#node-selector-and-node-affinity)
14. [Resource Requests and Limits](#resource-requests-and-limits)
15. [Kubernetes Auto-Scaling](#kubernetes-auto-scaling)
16. [Health Probes](#health-probes)
17. [RBAC (Role-Based Access Control)](#rbac)

---

## What is Kubernetes?

Kubernetes (K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It is designed to manage containers at scale across clusters of machines.

**Key Points:**
- Kubernetes automatically manages and schedules containers across a cluster of nodes
- It ensures high availability and reliability of applications
- It provides automatic scaling, self-healing, and load balancing capabilities
- Originally developed by Google and now maintained by the Cloud Native Computing Foundation (CNCF)
- Kubernetes abstracts the underlying infrastructure and provides a unified platform for running containers


---

## Why We Need Kubernetes

In modern application development, managing containers manually becomes increasingly complex. Here are the main reasons why Kubernetes is essential:

### 1. **Scalability**
- Automatically scales applications based on demand
- Handles horizontal scaling across multiple machines
- Manages workload distribution efficiently

### 2. **High Availability**
- Automatically restarts failed containers
- Replaces unhealthy pods with new ones
- Distributes traffic across healthy instances
- Ensures minimal downtime

### 3. **Resource Optimization**
- Efficiently packs containers across available resources
- Reduces infrastructure costs
- Maximizes hardware utilization

### 4. **Self-Healing**
- Automatically restarts crashed containers
- Replaces failed pods
- Performs health checks and takes corrective actions

### 5. **Declarative Configuration**
- Define desired state through YAML manifests
- Kubernetes ensures the system reaches and maintains that state
- Easy version control and reproducibility

### 6. **Load Balancing & Service Discovery**
- Automatically distributes traffic
- Service discovery out of the box
- Internal DNS for service communication

### 7. **Rolling Updates & Rollbacks**
- Zero-downtime deployments
- Gradual rollout of new versions
- Quick rollback if issues occur

---

## Docker vs Kubernetes

| Aspect | Docker | Kubernetes |
|--------|--------|------------|
| **Purpose** | Containerization - Packages applications into containers | Orchestration - Manages and orchestrates containers |
| **Scope** | Runs on a single machine | Manages containers across a cluster of machines |
| **Complexity** | Simpler, easier to learn | More complex, requires understanding of distributed systems |
| **Scaling** | Manual horizontal scaling needed | Automatic scaling built-in |
| **High Availability** | No built-in HA features | Multiple replica management and failover built-in |
| **Load Balancing** | No native load balancing | Built-in service discovery and load balancing |
| **Self-Healing** | Requires external monitoring | Automatic self-healing capabilities |
| **Networking** | Basic networking | Advanced networking and service mesh support |
| **Use Case** | Development, single-host deployments | Production-grade, multi-host deployments |

**Simple Analogy:**
- Docker is like having a shipping container that can hold your application
- Kubernetes is like having a shipping company that manages thousands of containers, ensuring they're transported, organized, and maintained properly

---

## Docker Swarm vs Kubernetes

| Aspect | Docker Swarm | Kubernetes |
|--------|--------------|------------|
| **Learning Curve** | Easier to learn and set up | Steeper learning curve |
| **Installation** | Built-in with Docker | Separate installation and setup |
| **Cluster Management** | Simpler, built-in to Docker | More complex but more powerful |
| **Scaling** | Manual scaling | Automatic and intelligent scaling |
| **Load Balancing** | Basic load balancing | Advanced load balancing |
| **Rolling Updates** | Limited support | Full rolling update support |
| **Networking** | Basic overlay networking | Advanced networking and service mesh |
| **Community Support** | Smaller community | Large and active community |
| **Production Readiness** | Suitable for smaller deployments | Enterprise-grade, suitable for large-scale deployments |
| **Monitoring & Logging** | Limited built-in tools | Extensive ecosystem of tools |
| **Market Adoption** | Declining popularity | Industry standard for orchestration |

---

## Kubernetes Architecture

Kubernetes follows a master-worker (control plane - data plane) architecture:

**Control Plane Components:**
- API Server
- Scheduler
- Controller Manager
- etcd (Data Store)

**Data Plane Components:**
- kubelet
- Container Runtime
- kube-proxy
- Pods

---

## Control Plane

The Control Plane (Master Node) is the brain of Kubernetes.

### Control Plane Components:

#### 1. **API Server**
- Exposes the Kubernetes API
- Entry point for all administrative tasks
- Validates and processes requests
- Stores cluster state in etcd

#### 2. **Scheduler**
- Assigns newly created pods to nodes
- Considers resource requirements and constraints
- Makes intelligent scheduling decisions

#### 3. **Controller Manager**
- Runs controller processes that regulate cluster state
- Node Controller: Monitors node health
- Replication Controller: Ensures correct number of replicas
- Deployment Controller: Manages deployments
- StatefulSet Controller: Manages stateful applications

#### 4. **etcd**
- Distributed key-value store for cluster data
- Source of truth for cluster state
- Critical component requiring backup strategy

---

## Data Plane

The Data Plane (Worker Nodes) run containerized applications.

### Data Plane Components:

#### 1. **kubelet**
- Agent running on each worker node
- Creates, manages, and monitors pods
- Performs health checks on containers
- Reports status to API server

#### 2. **Container Runtime**
- Runs containers (Docker, containerd, CRI-O)
- Manages container lifecycle

#### 3. **kube-proxy**
- Network proxy on each node
- Enables service discovery and load balancing
- Forwards traffic to correct pods

#### 4. **Pods**
- Smallest deployable unit
- Contains one or more containers
- Ephemeral by nature

---

## Workloads

Workloads define deployment rules for pods:

### 1. **Pods**
- Smallest deployable unit
- Typically contains a single container
- Containers share network and storage

### 2. **Deployments**
- Most common for stateless applications
- Manages replica sets
- Provides rolling updates and rollbacks

### 3. **StatefulSets**
- For stateful applications
- Provides stable network identities
- Persistent storage per pod
- Used for databases and caches

### 4. **DaemonSets**
- Ensures pod runs on every node
- Used for logging and monitoring

### 5. **Jobs**
- For batch processing
- Creates pods and waits for completion
- Retries failed pods

### 6. **CronJobs**
- Schedules jobs at specific times
- Like cron jobs in Linux

### 7. **Replica Sets**
- Ensures specified number of pod replicas
- Usually managed by Deployments

---

## ConfigMaps and Secrets

### ConfigMaps
- Store non-sensitive configuration data
- Key-value pairs in text format
- Decouple configuration from application code
- Max size: 1MB

**Use Cases:**
- Application configuration files
- Feature flags
- Application settings

### Secrets
- Store sensitive data securely
- Base64 encoded (not encrypted by default)
- Should use encryption at rest in production
- Max size: 1MB

**Use Cases:**
- Database passwords
- API keys
- SSH keys
- TLS/SSL certificates

---

## Services

Services expose pods to the network and provide stable endpoints.

### Service Types:

#### 1. **ClusterIP** (Default)
- Exposes service only within cluster
- Used for internal pod-to-pod communication

#### 2. **NodePort**
- Exposes service on each node's IP
- Accessible from outside cluster
- Port range: 30000-32767

#### 3. **LoadBalancer**
- Uses cloud provider's load balancer
- Provides external IP address
- Used in production for external access

#### 4. **ExternalName**
- Maps service to external DNS name
- Returns CNAME record

---

## Ingress and Ingress Controller

### Ingress
- Kubernetes API object for managing external access
- Provides HTTP/HTTPS routing
- Enables host-based and path-based routing
- Single entry point for multiple services

### Ingress Controller
- Implements Ingress functionality
- Watches Ingress objects and configures routing
- Popular: NGINX, Traefik, HAProxy, AWS ALB

---

## Taints and Tolerations

### Taints
- Applied to nodes to repel pods
- Prevents specific pods from being scheduled
- Used to reserve nodes for specific workloads

**Taint Effects:**
- **NoSchedule**: New pods won't be scheduled
- **NoExecute**: Pod evicted if already running
- **PreferNoSchedule**: Scheduler avoids but doesn't prevent

### Tolerations
- Applied to pods to allow scheduling on tainted nodes
- "Tolerates" the taint
- Enables scheduling on reserved nodes

**Use Cases:**
- Reserve nodes for specific applications
- GPU nodes for specific workloads
- Dedicated nodes for databases

---

## Node Selector and Node Affinity

### Node Selector
- Simple way to constrain pods to specific nodes
- Uses label matching
- Limited flexibility

### Node Affinity
- Advanced pod scheduling constraints
- More powerful than Node Selector
- Two types:

#### 1. **requiredDuringSchedulingIgnoredDuringExecution**
- Pod must be scheduled on matching node
- Strict requirements

#### 2. **preferredDuringSchedulingIgnoredDuringExecution**
- Scheduler prefers matching nodes
- Soft preference, not required

**Use Cases:**
- Schedule pods on nodes with specific hardware
- Distribute pods across zones
- Ensure pod runs on node with high memory

---

## Resource Requests and Limits

### Resource Requests
- Minimum resources guaranteed to a pod
- Scheduler uses this for pod placement
- Pod won't be scheduled if resources unavailable

### Resource Limits
- Maximum resources a pod can use
- Prevents pod from consuming excessive resources
- Pod gets throttled or killed if exceeded

**Resource Types:**
- **CPU**: Measured in millicores (m) or cores
- **Memory**: Measured in bytes (Mi, Gi)

**Impact:**
- Better resource utilization
- Prevents resource contention
- Enables efficient bin-packing

---

## Kubernetes Auto-Scaling

Kubernetes provides multiple scaling mechanisms:

### 1. **Horizontal Pod Autoscaler (HPA)**
- Automatically scales number of pod replicas
- Based on CPU/memory usage or custom metrics
- Scales deployment or replica set
- Default scale range: 1-10 pods

**How it works:**
- Monitors pod metrics
- Calculates desired replica count
- Adjusts replicas based on metrics

### 2. **Vertical Pod Autoscaler (VPA)**
- Automatically adjusts CPU/memory requests and limits
- Analyzes pod usage patterns
- Recommends resource adjustments
- Optimizes resource allocation

**Modes:**
- **Off**: Only recommendations
- **Initial**: Recommendations for new pods
- **Recreate**: Updates existing pods
- **Auto**: Chooses appropriate mode

### 3. **Cluster Autoscaler**
- Automatically scales number of nodes in cluster
- Adds nodes when pods can't be scheduled
- Removes underutilized nodes
- Works with cloud providers

---

## Health Probes

Health probes ensure pod/application health and readiness.

### 1. **Liveness Probe**
- Determines if pod should be restarted
- If fails, pod is restarted
- Detects stuck or deadlocked applications

**Use Case:**
- Restart unhealthy applications automatically

### 2. **Readiness Probe**
- Determines if pod is ready to receive traffic
- If fails, pod removed from service endpoints
- Doesn't restart pod, just removes from load balancing

**Use Case:**
- Prevent traffic to initializing pods

### 3. **Startup Probe**
- Indicates if application has started
- For slow-starting applications
- Disables liveness and readiness probes until successful

**Probe Types:**
- **HTTP**: Makes HTTP request
- **TCP**: Makes TCP connection
- **Exec**: Executes command in container

---

## RBAC (Role-Based Access Control)

RBAC manages who can do what in the cluster.

### Key Concepts:

#### 1. **Service Accounts**
- Identity for processes running in pods
- Each namespace has default service account
- Used for pod authentication

#### 2. **Roles**
- Collection of permissions
- Namespace-scoped
- Defines what actions are allowed

#### 3. **ClusterRoles**
- Like Roles but cluster-scoped
- For cluster-wide permissions

#### 4. **RoleBindings**
- Grants permissions from Role to user/service account
- Namespace-scoped

#### 5. **ClusterRoleBindings**
- Grants permissions from ClusterRole
- Cluster-scoped

### Common Use Cases:
- Grant developers access to specific namespaces
- Restrict pod permissions
- Separate admin and developer access
- Enable multi-tenancy

### Best Practices:
- Follow principle of least privilege
- Create specific roles for specific tasks
- Don't use admin role for all operations
- Audit RBAC policies regularly

---

## Summary

Kubernetes is a powerful platform for container orchestration. Understanding these core concepts is essential for:
- **Development**: Building cloud-native applications
- **Operations**: Managing and scaling applications
- **Architecture**: Designing resilient systems

Key takeaways:
- Kubernetes automates container management at scale
- Control Plane manages cluster state
- Data Plane runs applications
- Workloads define how applications run
- Advanced features enable production-grade deployments

---

## Additional Resources

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [Kubernetes API Reference](https://kubernetes.io/docs/reference/)
- [CNCF Kubernetes Training](https://www.cncf.io/certification/ckad/)
- [Community Forums](https://discuss.kubernetes.io/)

---

*Last Updated: November 2025*
