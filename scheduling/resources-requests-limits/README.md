## Resources - requests and limits 

### YAML Path 

#### Pod 

.spec.container[].resources

#### Deployment 

.spec.template.spec.container[].resources


### YAML 

```
resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"

```


