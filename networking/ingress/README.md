## Ingress

### Ingress Controller

Nginx / HAProxy / Contour / Istio

Can be deployed as deployment, not part of default k8s installation.

Deployment - Service - ConfigMap - Authorization (ServiceAccount, ClusterRoles, RoleBindings)

```
$ minikube addons enable ingress
$ k get deploy -A | grep ingress
ingress-nginx   ingress-nginx-controller   1/1     1            1           3h57m

$ k get all -n ingress-nginx
NAME                                           READY   STATUS      RESTARTS   AGE
pod/ingress-nginx-admission-create-qpfqz       0/1     Completed   0          4h
pod/ingress-nginx-admission-patch-c5t2g        0/1     Completed   1          4h
pod/ingress-nginx-controller-bc57996ff-87whb   1/1     Running     0          4h

NAME                                         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             NodePort    10.100.0.114   <none>        80:31613/TCP,443:32590/TCP   4h
service/ingress-nginx-controller-admission   ClusterIP   10.99.45.210   <none>        443/TCP                      4h

NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           4h

NAME                                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-bc57996ff   1         1         1       4h

NAME                                       STATUS     COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   Complete   1/1           7s         4h
job.batch/ingress-nginx-admission-patch    Complete   1/1           7s         4h

k get cm -n ingress-nginx
NAME                       DATA   AGE
ingress-nginx-controller   1      4h17m

```

### Ingress 

Used by Ingress Controller.

Scope: name space

Alway check if the same ingress rules does not get applied in two different namespace.
There is no issues on the apply since ingress is scoped based on name space.
However, the behavior depends on the ingress controller, such as NGINX IC will pick instead of another.




#### Rules

Type - resource
```
rules:
  - http:
      paths:
        - path: /icons
          pathType: ImplementationSpecific
          backend:
            resource:
              apiGroup: k8s.example.com
              kind: StorageBucket
              name: icon-assets

```

Type - path
```
rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
```

Type - host
```
rules:
  - host: "foo.bar.com"
    http:
      paths:
      - pathType: Prefix
        path: "/bar"
        backend:
          service:
            name: service1
            port:
              number: 80
  - host: "*.foo.com"
    http:
      paths:
      - pathType: Prefix
        path: "/foo"
        backend:
          service:
            name: service2
            port:
              number: 80 

```


### Debug 

#### Check logs ingress controll pod to see the get response 

503 - not hitting backend service 

172.17.0.1 - - [16/Dec/2024:16:50:44 +0000] "GET /watch HTTP/1.1" 503 190 "-" "curl/7.81.0" 84 0.000 [ingress-nginx-video-service-8080] [] - - - - 186b1dbbb5d58d48fcaaf724f6ab9a8f

200 - hitting backend service on IP / PORT 

172.17.0.1 - - [16/Dec/2024:17:10:40 +0000] "GET /watch HTTP/1.1" 200 293 "-" "curl/7.81.0" 84 0.000 [app-space-video-service-8080] [] 172.17.0.5:8080 293 0.004 200 c35559f488b111bb80bfae4756326904


#### Check service acces with curl / wget

frontend - ingress controller service 

backend - individual services 





