README

User
Create private and CSR with subject for the user and his/her group
$ openssl genrsa -out wayne.key 2048
$ openssl req -new -key wayne.key -out wayne.csr -subj "/O=developers/CN=wayne"
$ openssl req -text -noout -verify -in wayne.csr | grep Subject

Encode CSR with base64 and create wayne-csr.yaml 
usages has to be 'client auth'
expirationSeconds could be made longer (i.e. 864000 for ten days) or shorter (i.e. 3600 for one hour)
request is the base64 encoded value of the CSR file content. You can get the content
$ cat wayne.csr | base64 | tr -d "\n"

Create CSR with kubectl
$ k apply -f wayne-csr.yaml

Once approved, get the signed certificate
$ kubectl get csr
NAME    AGE   SIGNERNAME                            REQUESTOR       REQUESTEDDURATION   CONDITION
wayne   51s   kubernetes.io/kube-apiserver-client   minikube-user   30d                 Pending
$ kubectl get csr wayne -o jsonpath='{.status.certificate}'| base64 -d > wayne.crt
$ openssl x509 -text -noout -in wayne.crt

Admin
Approve CSR
$ k certificate approve wayne
certificatesigningrequest.certificates.k8s.io/wayne approved
$ kubectl get csr
NAME    AGE     SIGNERNAME                            REQUESTOR       REQUESTEDDURATION   CONDITION
wayne   2m23s   kubernetes.io/kube-apiserver-client   minikube-user   30d                 Approved,Issued

Create Role and RoleBinding
$ k api-resources 
$ k create role developer --verb=create,get,list,update,delete --resource=pods,deployments
te role developer --verb=create,get,list,update,delete --resource=pods,deployments
role.rbac.authorization.k8s.io/developer created
$ k get role developer -o yaml
$ k create rolebinding developer-rb-wayne --role=developer --user=wayne --group=developers
$ k get rolebindings.rbac.authorization.k8s.io developer-rb-wayne  -o wide
NAME                 ROLE             AGE   USERS   GROUPS       SERVICEACCOUNTS
developer-rb-wayne   Role/developer   21s   wayne   developers

Add credentials
$ k config set-credentials wayne --client-key=wayne.key --client-certificate=wayne.crt --embed-certs=true
$ k config view -o jsonpath='{.clusters[*].name}'
$ k  config set-context wayne --cluster=minikube  --user=wayne

Switch context
$ kubectl config use-context wayne
$ k config current-context
