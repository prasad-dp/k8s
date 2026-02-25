# Lab 01: Mastering Kubernetes Static Pods

## 1. Problem Statement
In a standard Kubernetes cluster, the **API Server** and **Scheduler** handle the placement and lifecycle of pods. However, what happens if the Control Plane itself is down? How do the core components (like the API Server or Etcd) start themselves up if there is no API Server to schedule them?

**Objective:** To manually instruct the `kubelet` on a worker node to manage a pod's lifecycle independently of the Kubernetes Control Plane using the **Static Pod** mechanism.

---

## 2. Detailed Explanation

### The "Bottom-Up" Management
A **Static Pod** is a pod that is managed directly by the `kubelet` daemon on a specific node. While the API Server can see the pod (it creates a "Mirror Pod" so you can see it in `kubectl`), it cannot control it.

### Core Characteristics:
* **Decentralized:** The Kubelet doesn't ask the Master for permission. It simply watches a specific directory on its local disk.
* **Immutable via API:** If you run `kubectl delete pod` on a static pod, it will vanish for a second and then reappear. The Kubelet is the "source of truth," and as long as the file exists in the folder, the pod will stay alive.
* **Node-Bound:** Static pods are tied to the node they are created on. They cannot be "moved" to another node by the scheduler.



---

## 3. Execution (Step-by-Step)

### Task A: Discovering the Manifest Path
Every Kubelet has a configuration file that tells it where to look for static manifests.

1.  **SSH into your Worker Node:**
    ```bash
    ssh ubuntu@<WORKER_PRIVATE_IP>
    ```
2.  **Locate the Config File:** Usually found at `/var/lib/kubelet/config.yaml`.
3.  **Find the Path:**
    ```bash
    grep -i "staticPodPath" /var/lib/kubelet/config.yaml
    ```
    *If the output is `/etc/kubernetes/manifests`, that is your target folder.*

### Task B: Creating the Static Pod
1.  **Navigate to the manifests folder:**
    ```bash
    cd /etc/kubernetes/manifests
    ```
    *(If it doesn't exist, create it: `sudo mkdir -p /etc/kubernetes/manifests`)*

2.  **Create the YAML file:**
    ```bash
    sudo vi static-web.yaml
    ```
3.  **Add the following Pod definition:**
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: static-web
      labels:
        role: testing
    spec:
      containers:
        - name: web-container
          image: nginx:stable-alpine
          ports:
            - containerPort: 80
    ```

### Task C: Verification & Testing
1.  **Check Local Status:** On the worker, check the container runtime:
    ```bash
    sudo crictl ps | grep static-web
    ```
2.  **Check Cluster Status:** Go back to your **Master Node** and run:
    ```bash
    kubectl get pods -o wide
    ```
    *Observation: Notice the pod name suffix. It will be appended with the node name (e.g., `static-web-ip-10-0-x-x`).*

3.  **Test Immortality:**
    ```bash
    kubectl delete pod static-web-<node-name>
    ```
    *Observation: The pod status will change to 'Terminating' and then immediately 'Pending/Running' again.*

---

## 4. What to Expect
* **Success:** You should see a pod running on your worker node that was never part of a Deployment or ReplicaSet.
* **Failure Scenario:** If the pod does not appear, check the Kubelet logs on the worker node using `journalctl -u kubelet`. Common errors include YAML indentation issues or the `staticPodPath` not being correctly defined in the Kubelet configuration.

## 5. Summary Note
This lab demonstrates the fundamental way Kubernetes bootstraps itself. When you run `kubeadm init`, Kubernetes creates static pod manifests for the **API Server**, **Controller Manager**, and **Scheduler** in `/etc/kubernetes/manifests` on the Master node. Understanding this allows you to troubleshoot clusters that won't start.
