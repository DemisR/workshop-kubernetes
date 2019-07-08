# Deployments

Edit files in `04-deployment` folder.
Change namespace
```
  namespace: <your name>
```

Deploy the app

```
kubectl apply -f 04-deployment/deployment.yaml
```

And now deploy the service ( remember, it's the _pointer_ to our app)

```
kubectl apply -f 04-deployment/service.yaml
```

We can watch the progress by looking at the deployment status:

```
kubectl get deployment podinfo
```

---

# Find the service address

Now that we have a running service that is `type: LoadBalancer` we need to find the ELB’s address.

```
kubectl get service podinfo -o wide
```
_(The `-o wide` it's only for change the output format)_


If we wanted to use the data programatically, we can also output via json. This is an example of how we might be able to make use of json output:

```
ELB=$(kubectl get service podinfo -o json | jq -r '.status.loadBalancer.ingress[].hostname')
```

---

# Load balancer access

In this example we access to the application trough the AWS load balacer directly to the service.

```
curl -m3 -v $ELB:9898
```
It will take several minutes for the ELB to become healthy and start passing traffic to the frontend pods.

Try to access to the url on port `9898` with you browser.

---

![](images/loadbalancer.png)

---

# Clean up

To delete the resources created by the applications, we should delete the application deployments:

Undeploy the applications:

```
kubectl delete -f 04-deployment/service.yaml
kubectl delete -f 04-deployment/deployment.yaml
```

---

# Ingress

Instead creating a load balancer for each service, you can use an Ingress to dispatch request on differents services.

The line `type: LoadBalancer` on `04-deployment/service.yaml` it's not necessary.

You can remove it and deploy the service.

```
kubectl apply -f 04-deployment/service.yaml
kubectl apply -f 04-deployment/deployment.yaml
```

---
## Ingress by path

Change name of path and namespace in file `04-deployment/ingress.yaml`
and create the ingress

```
kubectl apply -f 04-deployment/ingress.yaml
```

Now try to access to 

`http://demo.k8sworkshop.exxoss.academy/<your name>`

---

![](images/ingress.png)

---

## Ingress : Host based access

Edit `04-deployment/ingress.yaml` 
```
-        path: /<your name>
```
```
+   host: <your name>.k8sworkshop.exxoss.academy
```

and
```
-   traefik.frontend.rule.type: PathPrefixStrip
```
```
+   traefik.frontend.rule.type: PathPrefix
```

Apply the changes 
```
kubectl apply -f 04-deployment/ingress.yaml
```

Now you can join you app directly with 

`http://<your name>.k8sworkshop.exxoss.academy`

You can use `kubectl logs -f -l app=podinfo` for follow logs.

---

# Scale the backend service

When we launched our services, we only launched one container of each. We can confirm this by viewing the running pods:

```
kubectl get deployments
```

Now let’s scale up the backend services:

```
kubectl scale deployment podinfo --replicas=3
```

Confirm by looking at deployments again `kubectl get deployments`

_A better option to scale your service is edit the `04-deployment/ingress.yaml` and apply it._
<!-- .element: class="fragment" data-fragment-index="1"-->

