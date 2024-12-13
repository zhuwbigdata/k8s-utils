## Static Pods

Controlled by kubelet.

Normal staticPodPath 

$ ps -ef | grep kubelet
root        4136       1  0 15:12 ?        00:00:22 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock --pod-infra-container-image=registry.k8s.io/pause:3.10

Look for '--config=/var/lib/kubelet/config.yaml' parameter.

```
staticPodPath: /etc/kubernetes/manifests
```

### Imperative commands

$ k get node -o name
node/controlplane
node/node01

$ k get pod -A -o name | grep -E "controlplane|node01"

$ k get pod -A -o name | grep -e "controlplane" -e "node01"


