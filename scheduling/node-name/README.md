## Node Name

Manual scheduling even when kube-scheduler is down. 

### YAML Path 

#### Pod 

.spec.nodeName

#### Deployment (multiple pods on the same node, not recommended)

.spec.template.spec.nodeName


### YAML 

```
nodeName: node01
```


