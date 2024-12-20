## Question 4 | Pod Ready if Service is reachable
Use context: kubectl config use-context k8s-c1-H
Do the following in Namespace default . Create a single Pod named ready-if-service-ready of image nginx:1.16.1-alpine . Configure a
LivenessProbe which simply executes command true . Also configure a ReadinessProbe which does check if the url http://service-am-iready:80 is reachable, you can use wget -T2 -O- http://service-am-i-ready:80 for this. Start the Pod and confirm it isn't ready because
of the ReadinessProbe.
Create a second Pod named am-i-ready of image nginx:1.16.1-alpine with label id: cross-server-ready . The already existing Service
service-am-i-ready should now have that second Pod as endpoint.
Now the first Pod should be in ready state, confirm that.


### Create service service-am-i-ready without a pod - no endpoint defined
```
k apply -f 4.svc.yaml
service/service-am-i-ready created

k get svc
NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes           ClusterIP   10.96.0.1        <none>        443/TCP   7h23m
service-am-i-ready   ClusterIP   10.105.174.116   <none>        80/TCP    3s

k describe svc service-am-i-ready
Name:                     service-am-i-ready
Namespace:                default
Labels:                   id=cross-server-ready
Annotations:              <none>
Selector:                 id=cross-server-ready
Type:                     ClusterIP
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.105.174.116
IPs:                      10.105.174.116
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
Endpoints:
Session Affinity:         None
Internal Traffic Policy:  Cluster
Events:                   <none>

```

### Create liveness / readyness probe pod on the service
```
k apply -f 4.pod.yaml

k get pod -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP           NODE          NOMINATED NODE   READINESS GATES
ready-if-service-ready   0/1     Running   0          22m   10.244.1.6   macbeth-m02   <none>           <none>
test                     1/1     Running   0          12m   10.244.1.8   macbeth-m02   <none>           <none>

$ k describe pod ready-if-service-ready
Name:             ready-if-service-ready
Namespace:        default
Priority:         0
Service Account:  default
Node:             macbeth-m02/192.168.58.3
Start Time:       Thu, 19 Dec 2024 19:04:56 -0500
Labels:           run=ready-if-service-ready
Annotations:      <none>
Status:           Running
IP:               10.244.1.6
IPs:
  IP:  10.244.1.6
Containers:
  ready-if-service-ready:
    Container ID:   docker://ffb2072ce9e2755476e4f10ff18ae09082950c768edb80cbf262af160edd4fae
    Image:          nginx:1.16.1-alpine
    Image ID:       docker-pullable://nginx@sha256:5057451e461dda671da5e951019ddbff9d96a751fc7d548053523ca1f848c1ad
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 19 Dec 2024 19:04:58 -0500
    Ready:          False
    Restart Count:  0
    Liveness:       exec [true] delay=0s timeout=1s period=10s #success=1 #failure=3
    Readiness:      exec [sh -c wget -T2 -O- http://service-am-i-ready:80] delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-zgd6x (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True
  Initialized                 True
  Ready                       False
  ContainersReady             False
  PodScheduled                True
Volumes:
  kube-api-access-zgd6x:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age                   From               Message
  ----     ------     ----                  ----               -------
  Normal   Scheduled  22m                   default-scheduler  Successfully assigned default/ready-if-service-ready to macbeth-m02
  Normal   Pulling    22m                   kubelet            Pulling image "nginx:1.16.1-alpine"
  Normal   Pulled     22m                   kubelet            Successfully pulled image "nginx:1.16.1-alpine" in 1.799s (1.799s including waiting). Image size: 20852366 bytes.
  Normal   Created    22m                   kubelet            Created container ready-if-service-ready
  Normal   Started    22m                   kubelet            Started container ready-if-service-ready
  Warning  Unhealthy  17m (x37 over 22m)    kubelet            Readiness probe failed: wget: bad address 'service-am-i-ready:80'
  Warning  Unhealthy  2m32s (x91 over 16m)  kubelet            Readiness probe failed: command timed out: "sh -c wget -T2 -O- http://service-am-i-ready:80" timed out after 1s
```

### Create pod am-i-ready for the service - endpoint, then the probe pod will be ready  
```
k run am-i-ready --image=nginx:1.16.1-alpine --labels="id=cross-server-ready"

k get svc
NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes           ClusterIP   10.96.0.1        <none>        443/TCP   7h29m
service-am-i-ready   ClusterIP   10.105.174.116   <none>        80/TCP    6m5s

k describe svc service-am-i-ready
Name:                     service-am-i-ready
Namespace:                default
Labels:                   id=cross-server-ready
Annotations:              <none>
Selector:                 id=cross-server-ready
Type:                     ClusterIP
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.105.174.116
IPs:                      10.105.174.116
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
Endpoints:                10.244.1.9:80
Session Affinity:         None
Internal Traffic Policy:  Cluster
Events:                   <none>

k get pod -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP           NODE          NOMINATED NODE   READINESS GATES
am-i-ready               1/1     Running   0          27s   10.244.1.9   macbeth-m02   <none>           <none>
ready-if-service-ready   1/1     Running   0          24m   10.244.1.6   macbeth-m02   <none>           <none>
```
