# Resource Requests and Limits in Kubernetes

## Overview
Resource Requests and Limits are used to define how much CPU and memory a container needs and how much it's allowed to use.

## Resource Requests
Specifies the **minimum** resources a container needs. The scheduler uses this to decide which node to place the pod on.

### CPU Units:
- `1 = 1000m (milliCPU)`
- `0.5 = 500m`
- `100m = 0.1 CPU`

### Memory Units:
- `1Gi = 1 Gigabyte`
- `500Mi = 500 Megabytes`
- `1G = 1 Gigabyte`

## Resource Limits
Specifies the **maximum** resources a container is allowed to use.

### Important Notes:
- Container can burst up to the limit
- If CPU limit is exceeded, container is throttled
- If memory limit is exceeded, container is OOMKilled (Out of Memory)

## Best Practices:
1. Always set both requests and limits
2. Set requests based on typical usage
3. Set limits 1.5-2x higher than requests
4. Use HPA with requests for auto-scaling
5. Monitor actual usage and adjust accordingly
