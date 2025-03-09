# K8S

### **Scheduling in Kubernetes**

Kubernetes scheduling is the process of assigning **Pods** to **Nodes** within a cluster. The **Kube-Scheduler** is the default scheduler in Kubernetes responsible for selecting the most suitable node for a pod based on resource availability, constraints, and policies.

---

### **Key Concepts in Kubernetes Scheduling**

1. **Kube-Scheduler**

   - The default scheduler that watches for unscheduled pods and assigns them to nodes.
   - Evaluates nodes based on resource availability, constraints, and scheduling policies.

2. **Node Selection Process**

   - **Filtering:** Removes nodes that don’t meet the pod’s requirements (e.g., insufficient CPU/memory, taints).
   - **Scoring:** Assigns a score to each remaining node based on factors like resource efficiency and affinity.
   - **Binding:** The highest-scoring node is selected, and the pod is bound to it.

---

### **Scheduling Constraints and Rules**

1. **Resource Requests & Limits**

   - Requests define the minimum CPU and memory required for a pod.
   - Limits set the maximum resources a pod can consume.

2. **Node Selectors & Affinity**

   - **Node Selector:** Assigns a pod to a node with specific labels.
   - **Node Affinity:** Offers more flexible placement rules using soft (preferred) and hard (required) constraints.

3. **Taints & Tolerations**

   - Taints prevent specific pods from running on a node unless they have a matching toleration.
   - Used to keep certain workloads separate (e.g., critical system pods on dedicated nodes).

4. **Pod Affinity & Anti-Affinity**

   - **Pod Affinity:** Encourages pods to be scheduled together.
   - **Pod Anti-Affinity:** Prevents pods from running on the same node for better availability.

5. **Priority & Preemption**

   - Higher-priority pods can preempt lower-priority ones if resources are insufficient.
   - Ensures critical workloads get scheduled first.

---

### **Examples of Kubernetes Scheduling**

#### **1. Assign a Pod to a Specific Node Using `nodeSelector`**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  nodeSelector:
    disktype: ssd
  containers:
  - name: nginx
    image: nginx
```
✅ **Use Case**: Assign workloads to specific nodes based on labels.

#### **2. Assign a Pod Using Node Affinity**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
  containers:
  - name: app
    image: myapp:latest
```
✅ **Use Case**: Prefer running on nodes with `disktype=ssd`.

#### **3. Prevent a Pod from Running on Certain Nodes (Taints & Tolerations)**
```sh
kubectl taint nodes node1 key=value:NoSchedule
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toleration-pod
spec:
  tolerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
  containers:
  - name: app
    image: myapp:latest
```
✅ **Use Case**: Reserve certain nodes for critical workloads.

#### **4. Pod Affinity & Anti-Affinity**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: affinity-pod
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        labelSelector:
          matchLabels:
            app: myapp
        topologyKey: "kubernetes.io/hostname"
  containers:
  - name: app
    image: myapp:latest
```
✅ **Use Case**: Run related pods on the same node.

#### **5. Set Pod Priority & Preemption**
```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
globalDefault: false
description: "High priority for critical workloads"
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: priority-pod
spec:
  priorityClassName: high-priority
  containers:
  - name: app
    image: myapp:latest
```
✅ **Use Case**: Ensure critical services get scheduled first.

---

### **Conclusion**

Kubernetes scheduling ensures efficient resource utilization and workload placement across nodes. By leveraging scheduling constraints like **affinity rules, resource requests, and priorities**, administrators can optimize pod distribution and maintain high availability.
### **Manually schedule the pod on node01.**
#### **4. Pod Affinity & Anti-Affinity**
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  -  image: nginx
     name: nginx
```
### controlplane ~ ➜  kubectl get pods --namespace kube-system 
#### NAME                                   READY   STATUS    RESTARTS   AGE
#### coredns-7484cd47db-9x26h               1/1     Running   0          21m
#### coredns-7484cd47db-d7mfv               1/1     Running   0          21m
#### etcd-controlplane                      1/1     Running   0          21m
#### kube-apiserver-controlplane            1/1     Running   0          21m
#### kube-controller-manager-controlplane   1/1     Running   0          21m
#### kube-proxy-n879n                       1/1     Running   0          21m
#### kube-proxy-wkjc4     
