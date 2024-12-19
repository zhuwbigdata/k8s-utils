## CIDR

To identify the Kubernetes cluster's **CIDR ranges** for Pods and Services, you can check the configuration of the cluster and its components. Here’s how to find these ranges:

---

### **1. Pod CIDR Range**
The **Pod CIDR range** specifies the IP range for Pods in the cluster. This range is configured during cluster setup, often in the network plugin (CNI) configuration.

#### **Methods to Check Pod CIDR**

1. **Check Node Allocations**:
   - Each node in the cluster gets a subset of the Pod CIDR range.
   - You can see the CIDR assigned to a node using:
     ```bash
     kubectl get nodes -o wide
     ```
     Example output:
     ```plaintext
     NAME      STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
     node-1    Ready    worker   10d   v1.26.1   192.168.0.1   <none>        Ubuntu 20.04 LTS     5.4.0-135-generic containerd://1.6.4
     ```

   - To see the Pod CIDR block specifically:
     ```bash
     kubectl get nodes -o jsonpath='{.items[*].spec.podCIDR}'
     ```

2. **Inspect Network Add-On Configuration**:
   - If you're using a specific CNI plugin (e.g., Calico, Flannel), the Pod CIDR range is defined in the CNI configuration.
   - Look at the CNI plugin's configuration file (usually in `/etc/cni/net.d/` on the nodes).

3. **Check Controller Manager  Parameters**:
   ```
   $ cd /etc/kubernetes/manifest
   $ grep -i cidr *.yaml

   Look for 'cluster-cidr' for range and 'allocate-node-cidrs' flag
   ```    
---

### **2. Service CIDR Range**
The **Service CIDR range** specifies the range of IP addresses for ClusterIP Services.

#### **Methods to Check Service CIDR**

1. **Inspect the ClusterIP of a Service**:
   - All Services with a `ClusterIP` are assigned IPs from the Service CIDR range.
   - Get the ClusterIP of a Service:
     ```bash
     kubectl get service -o wide -n default
     ```
     Example output:
     ```plaintext
     NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
     kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP    10d
     ```

2. **Controller Manager Configuration**:
   - The Service CIDR range is set via the `--service-cluster-ip-range` flag in the **controller-manager** configuration.
   - Inspect this flag if you have access to the configuration files.

3. **Check Kubernetes ConfigMap or Cluster Documentation**:
   - Some managed Kubernetes solutions (e.g., GKE, AKS, EKS) might document the Service CIDR range.
   - You can also check if your cluster exposes this configuration in a `ConfigMap`.

---

### **3. Use Cluster Networking Tools**
You can use tools like **kubectl** or **kubectl plugins** to inspect the cluster network.

#### Example:
```bash
kubectl cluster-info dump | grep -m 1 -i cidr
```

This might reveal configuration lines containing `--cluster-cidr` or `--service-cluster-ip-range`.

---

### **4. Check Provider-Specific Configuration**
For managed Kubernetes platforms, the CIDR ranges might be configurable or automatically assigned during cluster creation:
- **GKE**: Check using the Google Cloud Console or `gcloud container clusters describe`.
- **EKS**: Check using the AWS Console or `aws eks describe-cluster`.
- **AKS**: Use the Azure Portal or `az aks show`.

---

### **Summary**
- **Pod CIDR**: Defined in the CNI plugin or controller-manager (`--cluster-cidr`).
- **Service CIDR**: Defined in the controller-manager (`--service-cluster-ip-range`).
- Use `kubectl` commands or provider-specific tools to inspect these configurations.
