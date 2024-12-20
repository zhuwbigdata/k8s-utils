##Question 11 | DaemonSet on all Nodes
Use context: kubectl config use-context k8s-c1-H
Use Namespace project-tiger for the following. Create a DaemonSet named ds-important with image httpd:2.4-alpine and labels
id=ds-important and uuid=18426a0b-5f59-4e10-923f-c0e078e82462 . The Pods it creates should request 10 millicore cpu and 10 mebibyte
memory. The Pods of that DaemonSet should run on all nodes, also controlplanes.


No kubectl create options for DaemonSet. Create a deployment and then change to daemonset.
