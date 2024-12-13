## Static Pods

Controlled by kubelet.

### Config Path

$ ps -ef | grep kubelet
root        4136       1  0 15:12 ?        00:00:22 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock --pod-infra-container-image=registry.k8s.io/pause:3.10

Look for '--config=/var/lib/kubelet/config.yaml' parameter.

```
staticPodPath: /etc/kubernetes/manifests
```

### Imperative commands

#### Based on pod name convention 

```
$ k  get pod -n kube-system
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-77d6fd4654-55k58               1/1     Running   0          38m
coredns-77d6fd4654-vcgjw               1/1     Running   0          38m
etcd-controlplane                      1/1     Running   0          38m
kube-apiserver-controlplane            1/1     Running   0          38m
kube-controller-manager-controlplane   1/1     Running   0          38m
kube-proxy-c9wfb                       1/1     Running   0          38m
kube-proxy-xn96n                       1/1     Running   0          37m
kube-scheduler-controlplane            1/1     Running   0          38m

```

$ k get node -o name
node/controlplane
node/node01

$ k get pod -A -o name | grep -E "controlplane|node01"

$ k get pod -A -o name | grep -e "controlplane" -e "node01"

#### Based on pod definition

Static Pods are owned by Node.

```
$  k get pod static-busybox-controlplane -o yaml | grep -A10 -i ownerReferences
  ownerReferences:
  - apiVersion: v1
    controller: true
    kind: Node
    name: controlplane
    uid: a95564b8-5128-442a-9b86-c90d01745bae


$  k get pod coredns-77d6fd4654-vcgjw -n kube-system -o yaml | grep -A10 -i ownerRef
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: coredns-77d6fd4654
    uid: de99d129-f73d-4313-8974-da0c32dbd2ad
```




