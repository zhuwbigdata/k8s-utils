## Metrics Server
 

### Minikube

minikube addons enable metrics-server

### Deploy by command

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/high-availability.yaml
```

### Top command
```
k top nodes
k top pods
```

## Logs

```
kubectl logs -f my-pod   
kubectl logs -l name=myLabel -c my-container
kubectl logs my-pod --previous  

```
