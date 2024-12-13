## Customer Schedule

Deploying a custom scheduler as a static Pod in the control plane is not recommended due to the above limitations. Instead, use a Deployment or DaemonSet for better manageability, scalability, and fault tolerance. 

### YAML Path 

#### Pod 

.spec.schedulerName

#### Daemonset or Deployment 

.spec.template.spec.schedulerName

### YAML 

```
spec:
  schedulerName: my-scheduler
```
