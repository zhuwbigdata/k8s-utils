## Taints and Tolerations

### Imperative Command taint on a node

#### To add key=value:effect (multiple taints)

kubectl taint nodes node1 key1=value1:NoSchedule

kubectl taint nodes node1 key2=value2:NoExecute

effect: NoExecute, NoSchedule, PreferNoSchedule

#### To remove

kubectl taint nodes node1 key1=value1:NoSchedule-


### YAML Path 

### Node

kubectl get nodes node01 -o yaml | grep -A10 -i taints




#### Pod 

.spec.tolerations

#### Deployment

.spec.template.spec.tolerations


### YAML 

```
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
- key: "key2"
  operator: "Equal"
  value: "value2"
  effect: "NoExecute"
```


