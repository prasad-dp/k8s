# Labels and Selectors in Kubernetes

## Overview
Labels are key-value pairs attached to Kubernetes objects. Selectors are used to query and filter objects based on labels.

## Labels
Labels are metadata that identify attributes of objects.

### Naming Rules:
- Max 63 characters
- Start/end with alphanumeric characters
- Can contain: letters, numbers, hyphens (-), underscores (_), dots (.)

### Common Labels:
- `app: frontend` - application name
- `version: v1.0` - application version
- `environment: production` - environment type
- `tier: backend` - application tier
- `team: platform` - team ownership

## Selectors
Mechanisms to filter objects based on labels.

### Types:
1. **Equality-based**: `=`, `==`, `!=`
2. **Set-based**: `in`, `notin`, `exists`

### Examples:
- `app=frontend` - objects with app=frontend
- `environment!=dev` - objects except dev
- `tier in (frontend,backend)` - multiple values

## Use Cases:
1. Pod scheduling with selectors
2. Service endpoints discovery
3. Network policies
4. Deployment/ReplicaSet management
