
---

# 🕵️ Kubernetes API-Server Debugging: Log Verbosity

## 📖 Project Context

When troubleshooting cluster issues, the default logs often lack detail. Increasing **Verbosity (`-v`)** allows you to see the raw HTTP requests, headers, and payload data coming into the API Server.

**The Problem Statement:** By default, logs only show high-level errors. To see *why* a request is failing (e.g., RBAC denials or timeout details), we need to shift the logging level from the default (usually 2) to a higher level (6–10).

---

## 📋 Prerequisites

* **Control Plane Access:** You must have SSH access to the Master Node.
* **Manifest Path:** Familiarity with `/etc/kubernetes/manifests/kube-apiserver.yaml`.
* **Sudo Permissions:** Required to edit system-level configuration files.

---

## 🛠️ Implementation Procedure

### 1. Locate the Manifest

The API Server is managed by the Kubelet through a static manifest file.

```bash
ls /etc/kubernetes/manifests/kube-apiserver.yaml

```

### 2. Modify the Verbosity Level

Edit the file using `vi` or `nano`:

```bash
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml

```

Find the `command` section and look for the `- --v=2` flag (or add it if it is missing).

**Verbosity Levels Guide:**

* `--v=2`: Standard logs (Default).
* `--v=4`: Debugging level (shows API request metadata).
* `--v=6`: Shows full HTTP headers.
* `--v=8`: Shows the full request/response body (highly detailed).

### 3. Save and Apply

Since Kubelet watches this folder, **saving the file automatically restarts the API Server.**

1. Save the file (in `vi`, type `:wq`).
2. The API Server will go down for 10-20 seconds.
3. Verify it is back: `kubectl get pods -n kube-system`.

---

## 🔍 Phase 2: Monitoring the Debug Logs

Once the server is back up, stream the logs to see the new, detailed information:

```bash
# Get the exact pod name
POD_NAME=$(kubectl get pods -n kube-system -l component=kube-apiserver -o name)

# Stream logs in real-time
kubectl logs -n kube-system $POD_NAME -f

```

---

## 🔍 Phase 3: Troubleshooting Guide

| Symptom | Cause | Solution |
| --- | --- | --- |
| **kubectl commands fail permanently** | Typo in the YAML file (e.g., wrong indentation). | Re-edit the file and ensure valid YAML syntax. Check `/var/log/syslog` if the container won't start. |
| **No change in logs** | Kubelet hasn't noticed the file change. | Restart Kubelet: `sudo systemctl restart kubelet`. |
| **Disk filling up rapidly** | Level `--v=8` or `--v=10` produces massive logs. | **Never leave high verbosity on in production.** Revert to `--v=2` after debugging. |

---

## 🧹 Phase 4: Clean Up (Post-Debugging)

Always reset the verbosity once you have captured the error to prevent performance degradation.

1. Edit `/etc/kubernetes/manifests/kube-apiserver.yaml`.
2. Set `--v=2`.
3. Save and verify the API server restarts normally.

---
