# Node Affinity

## Affinity declaration

### Location

#### Pod 

.spec.affinity.nodeAffinity

#### Deployment

.spec.template.spec.affinity.nodeAffinity


### YAML 


```
affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: topology.kubernetes.io/zone
            operator: In
            values:
            - antarctica-east1
            - antarctica-west1
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
```

Possible operator: In, NotIn, Exists, DoesNotExist, Gt, Lt.

### Types

requiredDuringSchedulingIgnoredDuringExecution: similar as nodeSelector

preferredDuringSchedulingIgnoredDuringExecution: best effort
