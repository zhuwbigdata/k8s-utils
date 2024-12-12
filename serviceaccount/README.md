# README

Wayne Zhu

## ServiceAccount

### Create sa in namespace 

$ k create sa pvviewer-sa -n default

$ k get sa pvviewer-sa -o yaml

### Create clusterrole to list all pv in the cluster

$ k create clusterrole pvviewer-cluster-role --verb=list --resource=pv

$ k get clusterrole pvviewer-cluster-role -o yaml

### Create cluster role binding 

$ k create clusterrolebinding pvviewer-role-binding --clusterrole=pvviewer-cluster-role --serviceaccount=default:pvviewer-sa

$ k get  clusterrolebinding pvviewer-role-binding -o yaml

### Verify

$ k auth can-i list pv --as=system:serviceaccount:default:pvviewer-sa

yes

$ k auth can-i get  pv --as=system:serviceaccount:default:pvviewer-sa

no
