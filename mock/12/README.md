##Question 12 | Deployment on all Nodes
Use context: kubectl config use-context k8s-c1-H
Implement the following in Namespace project-tiger :
Create a Deployment named deploy-important with 3 replicas
The Deployment and its Pods should have label id=very-important
It should have two containers:
First named container1 with image nginx:1.17.6-alpine
Second named container2 with image google/pause
There should only ever be one Pod of that Deployment running on one worker node, use topologyKey: kubernetes.io/hostname for
this
 Because there are two worker nodes and the Deployment has three replicas the result should be that the third Pod won't be
scheduled. In a way it simulates the behaviour of a DaemonSet, but using a Deployment and a fixed number of replicas.

###Node affinity (not applied)

###Pod affinity / anti-affinity / topology spread constraints
