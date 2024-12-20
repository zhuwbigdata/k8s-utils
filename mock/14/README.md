##Question 14 | Find out Cluster Information

Use context: kubectl config use-context k8s-c1-H
You're ask to find out following information about the cluster k8s-c1-H :
1. How many controlplane nodes are available?
2. How many worker nodes are available?
3. What is the Service CIDR?
4. Which Networking (or CNI Plugin) is configured and where is its config file?
5. Which suffix will static pods have that run on cluster1-node1 ?

###Service CIDR

```
ssh -n macbeth -p macbeth
cd /etc/kubernetes/manifests/

sudo grep range kube-apiserver.yaml
    - --service-cluster-ip-range=10.96.0.0/12
```

###Node CIDR and CNI provider

```
cd /etc/cni/net.d

cat 10-kindnet.conflist
{
	"cniVersion": "0.3.1",
	"name": "kindnet",
	"plugins": [
	{
		"type": "ptp",
		"ipMasq": false,
		"ipam": {
			"type": "host-local",
			"dataDir": "/run/cni-ipam-state",
			"routes": [


				{ "dst": "0.0.0.0/0" }
			],
			"ranges": [


				[ { "subnet": "10.244.0.0/24" } ]
			]
		}
		,
		"mtu": 65535

	},
	{
		"type": "portmap",
		"capabilities": {
			"portMappings": true
		}
	}
	]
}


cd /etc/kubernetes/manifests/
sudo grep cidr kube-controller-manager.yaml
    - --allocate-node-cidrs=true
    - --cluster-cidr=10.244.0.0/16

```

###ConfigMap in kube-system

```
kubectl get cm -n kube-system
NAME                                                   DATA   AGE
coredns                                                1      22h
extension-apiserver-authentication                     6      22h
kube-apiserver-legacy-service-account-token-tracking   1      22h
kube-proxy                                             2      22h
kube-root-ca.crt                                       1      22h
kubeadm-config                                         1      22h
kubelet-config                                         1      22h



kubectl describe cm kubeadm-config -n kube-system
Name:         kubeadm-config
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Data
ClusterConfiguration:
apiServer:
  certSANs:
  - 127.0.0.1
  - localhost
  - 192.168.58.2
  extraArgs:
  - name: enable-admission-plugins
    value: NamespaceLifecycle,LimitRanger,ServiceAccount,DefaultStorageClass,DefaultTolerationSeconds,NodeRestriction,MutatingAdmissionWebhook,ValidatingAdmissionWebhook,ResourceQuota
apiVersion: kubeadm.k8s.io/v1beta4
caCertificateValidityPeriod: 87600h0m0s
certificateValidityPeriod: 8760h0m0s
certificatesDir: /var/lib/minikube/certs
clusterName: mk
controlPlaneEndpoint: control-plane.minikube.internal:8443
controllerManager:
  extraArgs:
  - name: allocate-node-cidrs
    value: "true"
  - name: leader-elect
    value: "false"
dns: {}
encryptionAlgorithm: RSA-2048
etcd:
  local:
    dataDir: /var/lib/minikube/etcd
    extraArgs:
    - name: proxy-refresh-interval
      value: "70000"
imageRepository: registry.k8s.io
kind: ClusterConfiguration
kubernetesVersion: v1.31.0
networking:
  dnsDomain: cluster.local
  podSubnet: 10.244.0.0/16
  serviceSubnet: 10.96.0.0/12
proxy: {}
scheduler:
  extraArgs:
  - name: leader-elect
    value: "false"
BinaryData
Events:  <none>
```

