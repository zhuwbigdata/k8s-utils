##  Deployment 


### Strategy

#### YAML path

.spec.strategy 

.spec.strategy.type can be "Recreate" or "RollingUpdate" 

```
strategy:
   type: RollingUpdate
   rollingUpdate:
     maxUnavailable: 1
     maxSurge: 1

strategy:
   type: Recreate

```

### Set image 
```
k get deployment frontend -o yaml  

k set image deployment/frontend simple-webapp=kodekloud/webapp-color:v2

k get deployments.apps frontend -o json | jq -c 'paths|join(".")' | grep image

k get deployments.apps frontend -o jsonpath='{.spec.template.spec.containers[*].image}'

```

## Rollout status

### Rolling Upgrade
```
k rollout status  deployment/frontend
Waiting for deployment "frontend" rollout to finish: 2 out of 4 new replicas have been updated...
Waiting for deployment "frontend" rollout to finish: 2 out of 4 new replicas have been updated...
Waiting for deployment "frontend" rollout to finish: 2 out of 4 new replicas have been updated...
Waiting for deployment "frontend" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "frontend" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "frontend" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "frontend" rollout to finish: 1 old replicas are pending termination...
deployment "frontend" successfully rolled out
```

### Recreate
```
k rollout status deployment/frontend
Waiting for deployment "frontend" rollout to finish: 0 out of 4 new replicas have been updated...
Waiting for deployment "frontend" rollout to finish: 0 out of 4 new replicas have been updated...
Waiting for deployment "frontend" rollout to finish: 0 out of 4 new replicas have been updated...
Waiting for deployment "frontend" rollout to finish: 0 of 4 updated replicas are available...
Waiting for deployment "frontend" rollout to finish: 0 of 4 updated replicas are available...
Waiting for deployment "frontend" rollout to finish: 0 of 4 updated replicas are available...
Waiting for deployment "frontend" rollout to finish: 0 of 4 updated replicas are available...
Waiting for deployment "frontend" rollout to finish: 0 of 4 updated replicas are available...
deployment "frontend" successfully rolled out
```

## Rollout history

```
$ k rollout history deployment/frontend
deployment.apps/frontend 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

$ k rollout history deployment/frontend --revision=2
deployment.apps/frontend with revision #2
Pod Template:
  Labels:       name=webapp
        pod-template-hash=7fdd5c7d64
  Containers:
   simple-webapp:
    Image:      kodekloud/webapp-color:v3
    Port:       8080/TCP
    Host Port:  0/TCP
    Environment:        <none>
    Mounts:     <none>
  Volumes:      <none>
  Node-Selectors:       <none>
  Tolerations:  <none>


$ kubectl rollout undo deployment/frontend --to-revision=2

```

### Curl Test on Service

```
cat curl-test.sh 
for i in {1..35}; do
   kubectl exec --namespace=kube-public curl -- sh -c 'test=`wget -qO- -T 2  http://webapp-service.default.svc.cluster.local:8080/info 2>&1` && echo "$test OK" || echo "Failed"';
   echo ""
done
```
###
