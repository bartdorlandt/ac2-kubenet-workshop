### Lab Guide: Setting Up a Kubernetes Cluster with `Kind` and Understanding Core Kubernetes Components

This lab will guide you through setting up a local Kubernetes cluster using **Kind** and introduce you to `kubectl` as the main tool for interacting with your Kubernetes cluster. You will learn how to check and understand the core Kubernetes pods and their functions.

---

### **Step 0: Set Up Your Kubernetes Cluster (Kind)**

#### **What is Codespaces?**
GitHub Codespaces is a cloud-based development environment that lets you code directly in the cloud using Visual Studio Code. It provides pre-configured containers with your code, dependencies, and tools, allowing you to start coding instantly without setting up a local development environment.

#### **Start your Codespaces instance**
To start your lab, use this link: https://github.com/codespaces/new/kubenet-dev/ac2-kubenet-workshop
Then Check the configuration before continue. Ensure the repo name is: `kubenet-dev/ac2-kubenet-workshop`
Make sure you are using the 4-core instance

There’s a free usage limit (120 core hours), which you can check under the Codespaces section
https://github.com/settings/billing/summary.

   **Important:** after using it, stop it and remove it at https://github.com/codespaces or it will keep using your free usage quota.

Click on **Create codespace**

Wait a few minutes for codespaces to load and go to the **Terminal** Section

#### **What is Kind?**
**Kind** is a opensource tool for running local Kubernetes clusters using Docker container "nodes." It’s great for learning, testing, or developing on Kubernetes.

#### **Installation of Kind and kubectl**
**Important:** You can skip this part if you are using GitHub codespaces. the lab image is already coming with the required tools and applications.
1. **Install Kind**  
   Run the following commands to download and set up Kind:
   ```bash
   curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64
   chmod +x ./kind
   sudo mv ./kind /usr/local/bin/kind
   ```
2. **Create a Kind-based Kubernetes cluster**  
   ```bash
   kind create cluster
   ```

3. **Set Up Network Communication (iptables rule)**
   ```bash
   sudo iptables -I DOCKER-USER -o br-$(docker network inspect -f '{{ printf "%.12s" .ID }}' kind) -j ACCEPT
   ```
   - **Explanation**: This command allows the Kind cluster to communicate properly with other network elements.

4. **Install `kubectl`**
`kubectl` is the command-line tool for managing Kubernetes clusters.
   ```bash
   curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
   chmod +x ./kubectl
   sudo mv ./kubectl /usr/local/bin/kubectl
   ```
5.  **Verify the Installation**
Check the `kubectl` version
   ```bash
   kubectl version --client
   ```
---

### **Step 1: Create your kubernetes cluster**
1. **Create Kind Cluster and docker Network for GitHub CodeSpaces**
   If you are using codespaces, you need to create a docker network before to create the cluster. Otherwise, **go to Step 2**.
   Create docker network:
   ```bash
   docker network create -d=bridge -o com.docker.network.bridge.enable_ip_masquerade=true -o com.docker.network.driver.mtu=1500 --subnet fc00:f853:ccd:e793::/64 kind
   ```
   Create one-node kubernetes cluster
   ```bash
   kind create cluster
   ```

---

### **Step 2: Check Core Kubernetes Components**
After setting up the cluster, you’ll see several pods running in the `kube-system` namespace. These are the core components of your Kubernetes cluster.

### **Verify your cluster access**  
   Check if `kubectl` is configured to connect to your cluster:
   ```bash
   kubectl cluster-info
   ```

#### **List the Core Pods**
1. **View pods in the `kube-system` namespace**
   ```bash
   kubectl get pods -n kube-system
   ```

#### **Core Components Overview**
The following are the core Kubernetes components you will see:

1. **kube-apiserver**
   - **What it does**: The API server is the front-end of the Kubernetes control plane, handling all REST requests for the cluster and processing changes.
   - **Check the pod**:
     ```bash
     kubectl describe pod <pod-name> -n kube-system
     ```

2. **etcd**
   - **What it does**: A distributed key-value store that holds all the cluster's state data.
   - **Why it's important**: etcd is critical as it stores information on all Kubernetes objects, ensuring the cluster's state is consistent.

3. **kube-scheduler**
   - **What it does**: Watches for newly created pods with no assigned nodes and assigns them to available nodes based on resource requirements and other constraints.
   - **Check its status**:
     ```bash
     kubectl get pod -l component=kube-scheduler -n kube-system
     ```

4. **kube-controller-manager**
   - **What it does**: Manages the different controllers that regulate the cluster’s state, such as handling replication, updating status information, and maintaining a healthy cluster state.

5. **kube-proxy**
   - **What it does**: Maintains network rules on each node, allowing communication to your services within or outside of the cluster.
   - **Check the pods**:
     ```bash
     kubectl get pod -l k8s-app=kube-proxy -n kube-system
     ```

6. **CoreDNS**
   - **What it does**: Provides DNS services to the cluster, allowing pods to discover each other by name.
   - **Check CoreDNS**:
     ```bash
     kubectl get pods -l k8s-app=kube-dns -n kube-system
     ```

**Explanation**:  
Each of these components works together to form the control plane, managing the entire Kubernetes cluster.


---

### **Conclusion:**
In this lab, you set up a Kubernetes cluster using Kind and explored core Kubernetes components with `kubectl`. You learned about the key elements like `kube-apiserver`, `etcd`, `kube-scheduler`, `kube-controller-manager`, `kube-proxy`, and CoreDNS, which form the backbone of any Kubernetes cluster. This hands-on experience will help you understand how Kubernetes manages workloads and communicates internally.