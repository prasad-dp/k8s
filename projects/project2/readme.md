

---

# 🛡️ Kubernetes Disaster Recovery: etcd Snapshot & Restore

## 📖 Project Context

In a Kubernetes cluster, **etcd** is the distributed key-value store that acts as the cluster's "Source of Truth."

**The Problem Statement:** If a critical Namespace or resource is accidentally deleted, Kubernetes (being a declarative system) will immediately move to that new "empty" state by terminating all running containers. To recover, we must "rewind" the cluster's brain (etcd) to a point in time before the deletion occurred.

---

## 📋 Prerequisites

Before attempting a backup or restore, ensure the following conditions are met on the **Control Plane** node:

1. **Tooling:** The `etcd-client` must be installed on the host OS.
* *Command:* `sudo apt-get install -y etcd-client`


2. **Certificate Access:** You must have read access to the Kubernetes PKI directory (typically `/etc/kubernetes/pki/etcd/`).
3. **KUBECONFIG:** Ensure your administrative credentials are accessible.
* *Command:* `export KUBECONFIG=/etc/kubernetes/admin.conf`


4. **Storage:** Ensure `/tmp` or your backup destination has enough space (at least 2x the size of your `/var/lib/etcd` directory).
5. **Runtime:** Know your container runtime (Docker, containerd, or CRI-O) to verify pod status during the restart.

---

## 🛠️ Phase 1: The Backup (Creating a Save Point)

We use `etcdctl` to capture the database state. We must provide the TLS certificates because the data is encrypted.

```bash
sudo ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  snapshot save /tmp/cluster-backup.db

```

---

## ⚠️ Phase 2: The Restore (The "Surgery")

### 1. Stop the Control Plane

We must "freeze" the cluster by moving the Static Pod manifests. This tells the Kubelet to shut down the API Server and etcd containers.

```bash
sudo mkdir /tmp/k8s-manifests
sudo mv /etc/kubernetes/manifests/*.yaml /tmp/k8s-manifests/

```

### 2. Restore Data from Snapshot

This command initializes a new data directory structure based on the backup file.

```bash
sudo ETCDCTL_API=3 etcdctl snapshot restore /tmp/cluster-backup.db \
  --data-dir=/var/lib/etcd-restored

```

### 3. The Database Swap

We replace the current (deleted state) database with our restored version.

```bash
sudo mv /var/lib/etcd /var/lib/etcd-old
sudo mv /var/lib/etcd-restored /var/lib/etcd
sudo chown -R root:root /var/lib/etcd

```

### 4. Restart the Cluster

Moving the manifests back triggers the Kubelet to restart the core cluster components.

```bash
sudo mv /tmp/k8s-manifests/*.yaml /etc/kubernetes/manifests/

```

---

## 🔍 Phase 3: Troubleshooting Guide

| Symptom | Cause | Solution |
| --- | --- | --- |
| **"connection refused" (port 8080)** | `kubectl` is defaulting to localhost:8080 because it can't find your config. | Run `export KUBECONFIG=/etc/kubernetes/admin.conf` or use sudo with the path. |
| **"sh: executable file not found"** | The `etcd` container is **Distroless** (no shell inside for security). | **Do not use `kubectl exec`.** Run the `etcdctl` command from the host OS. |
| **Namespace is back, but Pod is missing** | The worker node already killed the container; it hasn't checked in with the master yet. | Restart the Kubelet on the worker node: `sudo systemctl restart kubelet`. |
| **Snapshot file is 0 bytes** | Insufficient permissions to read the certs or write to `/tmp`. | Always ensure you are using `sudo` for the snapshot command. |

---

## 🧹 Phase 4: Clean Up

Once you have verified that your pods and namespaces are back, clean up the temporary files.

```bash
# Remove the old (broken) data directory
sudo rm -rf /var/lib/etcd-old

# Remove the temporary manifest storage
sudo rm -rf /tmp/k8s-manifests

# Delete the backup file
sudo rm /tmp/cluster-backup.db

```

---
