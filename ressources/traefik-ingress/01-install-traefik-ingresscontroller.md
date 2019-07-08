# Traefik ingress-controller

```
$ kubectl apply -f https://raw.githubusercontent.com/containous/traefik/v1.7/examples/k8s/traefik-rbac.yaml

$ kubectl apply -f optional/traefik-ingress/traefik-deployment.yaml
$ kubectl describe service traefik-ingress-service --namespace=kube-system
    LoadBalancer Ingress:     a5e60b411a16311e999ac0e344f0b4c0-109548033.eu-west-3.elb.amazonaws.com
```
Create CNAME DNS record
```
*.k8sworkshop.exxoss.academy. IN CNAME a5e60b411a16311e999ac0e344f0b4c0-109548033.eu-west-3.elb.amazonaws.com.
```

```
kubectl apply -f ressources/traefik-ingress/traefik-ingress-admin.yaml
```

You can access the UI on http://traefik.k8sworkshop.exxoss.academy


---


$ kubectl get all --namespace=kube-system 
NAME                                              READY   STATUS    RESTARTS   AGE
pod/aws-node-4r255                                1/1     Running   0          14d
pod/aws-node-9b6xg                                1/1     Running   0          14d
pod/aws-node-mbpnc                                1/1     Running   0          14d
pod/aws-node-ss7rc                                1/1     Running   0          14d
pod/coredns-6b7f8589d7-8mmdl                      1/1     Running   0          14d
pod/coredns-6b7f8589d7-mxblv                      1/1     Running   0          14d
pod/kube-proxy-8hbzm                              1/1     Running   0          14d
pod/kube-proxy-kw6sr                              1/1     Running   0          14d
pod/kube-proxy-mxgrd                              1/1     Running   0          14d
pod/kube-proxy-q74x6                              1/1     Running   0          14d
pod/traefik-ingress-controller-6545547764-6c8x2   1/1     Running   0          40m


NAME                              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                       AGE
service/kube-dns                  ClusterIP   10.100.0.10     <none>        53/UDP,53/TCP                 14d
service/traefik-ingress-service   NodePort    10.100.212.55   <none>        80:31959/TCP,8080:30602/TCP   40m

NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemonset.apps/aws-node     4         4         4       4            4           <none>          14d
daemonset.apps/kube-proxy   4         4         4       4            4           <none>          14d

NAME                                         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coredns                      2         2         2            2           14d
deployment.apps/traefik-ingress-controller   1         1         1            1           40m

NAME                                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/coredns-6b7f8589d7                      2         2         2       14d
replicaset.apps/traefik-ingress-controller-6545547764   1         1         1       40m




$ kubectl delete deployment.apps/traefik-ingress-controller --namespace=kube-system 
deployment.apps "traefik-ingress-controller" deleted