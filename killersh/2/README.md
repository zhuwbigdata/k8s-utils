## Question 2 | Schedule Pod on Controlplane Nodes
```
Use context: kubectl config use-context k8s-c1-H
Create a single Pod of image httpd:2.4.41-alpine in Namespace default . The Pod should be named pod1 and the container should be
named pod1-container . This Pod should only be scheduled on controlplane nodes. Do not add new labels to any nodes.
```

Control plane node labels are shown:
```
$ k get node macbeth --show-labels
NAME      STATUS   ROLES           AGE    VERSION   LABELS
macbeth   Ready    control-plane   5h1m   v1.31.0   beta.kubernetes.io/arch=arm64,beta.kubernetes.io/os=linux,kubernetes.io/arch=arm64,kubernetes.io/hostname=macbeth,kubernetes.io/os=linux,minikube.k8s.io/commit=210b148df93a80eb872ecbeb7e35281b3c582c61,minikube.k8s.io/name=macbeth,minikube.k8s.io/primary=true,minikube.k8s.io/updated_at=2024_12_19T11_59_33_0700,minikube.k8s.io/version=v1.34.0,node-role.kubernetes.io/control-plane=,node.kubernetes.io/exclude-from-external-load-balancers=
```

### Control plane node is tainted

Assume node control plane tainted with the following:
```
k taint node macbeth node-role.kubernetes.io/control-plane=:NoSchedule
or
k taint node macbeth node-role.kubernetes.io/control-plane="":NoSchedule

$ k get node macbeth -o yaml | grep -A5 taints
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/control-plane
```

Tolerations or Node Selector alone does not work.

With node selector only, control plan node taint will reject the pod.

With tolerations only,   work node might be selected.

Solution: node tolerations +  node selector

```
spec:
  nodeSelector:
    node-role.kubernetes.io/control-plane: ""

spec:
 tolerations:
 - key: "node-role.kubernetes.io/control-plane"
    operator: "Exists"
    effect: "NoSchedule"
```


### Control plane node is NOT tainted
```
k taint node macbeth node-role.kubernetes.io/control-plane=:NoSchedule-
node/macbeth untainted

k get node macbeth -o yaml | grep taints -A5

```

#### Node selector should work alone.
```
k apply -f 2.1-node-selector.yaml

k get pod -o wide
NAME   READY   STATUS    RESTARTS   AGE     IP           NODE      NOMINATED NODE   READINESS GATES
pod1   1/1     Running   0          3m36s   10.244.0.5   macbeth   <none>           <none>

```

#### Manual schedule
```
k apply -f 2.2.yaml
k get pod pod2 -o wide
NAME   READY   STATUS    RESTARTS   AGE   IP           NODE      NOMINATED NODE   READINESS GATES
pod2   1/1     Running   0          17s   10.244.0.6   macbeth   <none>           <none>

```




