# README

Wayne Zhu
## User
Create private and CSR with subject for the user and his/her group

$ openssl genrsa -out wayne.key 2048

$ openssl req -new -key wayne.key -out wayne.csr -subj "/O=developers/CN=wayne"

$ openssl req -text -noout -verify -in wayne.csr | grep Subject

### Encode CSR with base64 and create wayne-csr.yaml 

```
cat <<EOF | kubectl apply -f -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: csr-name
spec:
  request:  base64-encoded-csr
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - client auth
EOF

```



usages has to be 'client auth'

expirationSeconds could be made longer (i.e. 864000 for ten days) or shorter (i.e. 3600 for one hour)

request is the base64 encoded value of the CSR file content. You can get the content

$ cat wayne.csr | base64 | tr -d "\n"

### Create CSR with kubectl

$ k apply -f wayne-csr.yaml

### Once approved, get the signed certificate

$ kubectl get csr

NAME    AGE   SIGNERNAME                            REQUESTOR       REQUESTEDDURATION   CONDITION

wayne   51s   kubernetes.io/kube-apiserver-client   minikube-user   30d                 Pending

$ kubectl get csr wayne -o jsonpath='{.status.certificate}'| base64 -d > wayne.crt

$ openssl x509 -text -noout -in wayne.crt

## Admin

### Approve CSR

$ k certificate approve wayne

certificatesigningrequest.certificates.k8s.io/wayne approved

$ kubectl get csr

NAME    AGE     SIGNERNAME                            REQUESTOR       REQUESTEDDURATION   CONDITION

wayne   2m23s   kubernetes.io/kube-apiserver-client   minikube-user   30d                 Approved,Issued

### Create Role and RoleBinding - namespaced 

$ k api-resources --namespaced=true | grep role
rolebindings                             rbac.authorization.k8s.io/v1   true         RoleBinding
roles                                    rbac.authorization.k8s.io/v1   true         Role

$ k create role developer --verb=create,get,list,update,delete --resource=pods,deployments --namespace=developement

$ k get role developer  --namespace=developement -o yaml 

$ k create rolebinding developer-rb-developers --role=developer  --group=developers  --namespace=developement
$ k create rolebinding developer-rb-wayne --role=developer --user=wayne  --namespace=developement

$ k get rolebindings.rbac.authorization.k8s.io  -n developement -o wide
NAME                      ROLE             AGE   USERS   GROUPS       SERVICEACCOUNTS
developer-rb-developers   Role/developer   26s           developers
developer-rb-wayne        Role/developer   20s   wayne

### Verify

$ k auth can-i list pods --as=wayne -n developement

$ k auth can-i list pods --as=wayne --as-group=developers -n developement



### Add credentials per cluster 

Note: client certificate is signed for the cluster specific CA, so the client private key is the same, but
the client certificate is different.

Notes: there is no k8s CLI commands to list all the users defined in a cluster, but we can infer with:
$ k get csr
$ k get rolebindings --all-namespaces -o yaml
$ k get clusterrolebindings -o yaml 

$ k config set-credentials wayne --client-key=wayne.key --client-certificate=wayne.crt --embed-certs=true

$ k config view -o jsonpath='{.clusters[*].name}'

$ k  config set-context wayne --cluster=macbeth  --user=wayne

### Switch context

$ kubectl config use-context wayne

$ k config current-context

### View current context only  

kubectl config view --minify


### Verify Role Binding 

$ k auth whoami

ATTRIBUTE   VALUE

Username    minikube-user

Groups      [system:masters system:authenticated]

$ k auth  can-i delete pod --as=wayne

yes

$ k auth  can-i delete rs --as=wayne

no
