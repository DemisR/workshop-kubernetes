<p class="eyebrow">Module 04</p>

# Deploy an application

<p class="lead">Apply the podinfo manifests, follow the resource relationships, and roll out a new version.</p>

---

## Lab result

<div class="diagram">
  <div class="node control">Browser</div>
  <div class="connector fragment">→</div>
  <div class="node data fragment">port-forward</div>
  <div class="connector fragment">→</div>
  <div class="node good fragment">Service<br><code>podinfo:9898</code></div>
  <div class="connector fragment">→</div>
  <div class="diagram vertical fragment">
    <div class="node workload">Pod</div>
    <div class="node workload">Pod</div>
  </div>
</div>

<p class="fragment">A Deployment maintains the Pods. A Service keeps the route stable while Pods change.</p>

---

## The manifests

```text
04-deployment/
├── namespace.yaml
├── deployment.yaml
├── service.yaml
├── ingress.yaml
└── kustomization.yaml
```

<div class="two-col fragment">
  <div class="card">
    <h3>Base lab</h3>
    <p><code>kustomization.yaml</code> applies the Namespace, Deployment, and Service.</p>
  </div>
  <div class="card">
    <h3>Optional routing</h3>
    <p><code>ingress.yaml</code> needs an Ingress controller and is not part of the base lab.</p>
  </div>
</div>

---

## Apply the desired state

```shell
kubectl apply -k 04-deployment
```

<p class="fragment">Kustomize submits the resources in the directory. <code>apply</code> creates missing objects and updates existing objects.</p>

```shell
kubectl get all -n workshop
kubectl rollout status deployment/podinfo -n workshop
```
<!-- .element: class="fragment" -->

---

## Read the Deployment status

```shell
kubectl get deployment podinfo -n workshop
```

```text
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
podinfo   2/2     2            2           40s
```
<!-- .element: class="fragment" -->

<ul>
  <li class="fragment"><strong>READY</strong> compares ready replicas with desired replicas.</li>
  <li class="fragment"><strong>UP-TO-DATE</strong> counts replicas that use the current Pod template.</li>
  <li class="fragment"><strong>AVAILABLE</strong> counts replicas ready to serve traffic.</li>
</ul>

---

## Reach the Service

```shell
kubectl port-forward -n workshop service/podinfo 9898:9898
```

<p class="fragment">Open <a href="http://localhost:9898">http://localhost:9898</a>.</p>

<div class="diagram fragment">
  <div class="node control">localhost:9898</div>
  <div class="connector">→</div>
  <div class="node good">Service port 9898</div>
  <div class="connector">→</div>
  <div class="node workload">One ready Pod</div>
</div>

<p class="small fragment">Keep the port-forward command running during the next steps.</p>

---

## Labels drive Service routing

```yaml
# service.yaml
selector:
  app.kubernetes.io/name: podinfo
```

```yaml
# deployment.yaml Pod template
labels:
  app.kubernetes.io/name: podinfo
```
<!-- .element: class="fragment" -->

```shell
kubectl get endpointslice -n workshop \
  -l kubernetes.io/service-name=podinfo
```
<!-- .element: class="fragment" -->

<p class="fragment">If the labels do not match, the Service exists but has no backends.</p>

---

## Scale the Deployment

```shell
kubectl scale deployment/podinfo \
  --namespace workshop \
  --replicas 4
```

<div class="diagram fragment">
  <div class="node data">Desired<br><strong>4</strong></div>
  <div class="connector">→</div>
  <div class="node control">Deployment controller</div>
  <div class="connector">→</div>
  <div class="node good">4 ready Pods</div>
</div>

```shell
kubectl get pods -n workshop --watch
```
<!-- .element: class="fragment" -->

---

## Declarative state wins on the next apply

<p>The file still declares two replicas.</p>

```yaml
spec:
  replicas: 2
```

<p class="fragment">Apply the directory again:</p>

```shell
kubectl apply -k 04-deployment
kubectl get deployment podinfo -n workshop
```
<!-- .element: class="fragment" -->

<p class="fragment">The Deployment returns to two replicas because the manifest is the source of desired state.</p>

---

## Roll out a new image

```shell
kubectl set image deployment/podinfo \
  --namespace workshop \
  podinfo=ghcr.io/stefanprodan/podinfo:6.14.0
```

<div class="flow fragment">
  <div class="node workload">Old ReplicaSet</div>
  <div class="node data">New ReplicaSet starts</div>
  <div class="node good">New Pods become ready</div>
  <div class="node control">Old Pods stop</div>
</div>

```shell
kubectl rollout status deployment/podinfo -n workshop
kubectl rollout history deployment/podinfo -n workshop
```
<!-- .element: class="fragment" -->

---

## Roll back, then restore the manifest

```shell
kubectl rollout undo deployment/podinfo -n workshop
kubectl rollout status deployment/podinfo -n workshop
```

<p class="fragment">The rollback changes live state. The manifest still declares version 6.14.1.</p>

```shell
kubectl apply -k 04-deployment
```
<!-- .element: class="fragment" -->

> Pick one delivery source for production. A GitOps controller can keep live state aligned with the repository.

---

## Probes control traffic and restarts

<div class="two-col">
  <div class="card">
    <h3>Readiness probe</h3>
    <p>A failed check removes the Pod from Service backends.</p>
  </div>
  <div class="card fragment">
    <h3>Liveness probe</h3>
    <p>A repeated failed check makes the kubelet restart the container.</p>
  </div>
</div>

```shell
kubectl describe pod -n workshop -l app.kubernetes.io/name=podinfo
```
<!-- .element: class="fragment" -->

<p class="fragment">Resource requests also help the scheduler choose a node. Limits cap resource use.</p>

---

## External HTTP traffic

<div class="flow">
  <div class="node control">Client</div>
  <div class="node data fragment">Gateway or Ingress</div>
  <div class="node good fragment">Service</div>
  <div class="node workload fragment">Ready Pods</div>
</div>

<div class="compact">

- `LoadBalancer` exposes one Service through supported infrastructure.
- Ingress routes HTTP traffic through an installed controller.
- Gateway API supports more routing options and separates infrastructure from application routes.

</div>

<p class="fragment compact">Kubernetes has frozen the Ingress API and recommends Gateway API for new designs. Many existing clusters still use Ingress.</p>

<p class="source">Sources: <a href="https://kubernetes.io/docs/concepts/services-networking/ingress/">Ingress</a> and <a href="https://kubernetes.io/docs/concepts/services-networking/gateway/">Gateway API</a></p>

---

## Optional Ingress exercise

<p>Use this only if your cluster has an Ingress controller named <code>nginx</code>.</p>

```shell
kubectl apply -f 04-deployment/ingress.yaml
kubectl get ingress -n workshop
```

<p class="fragment">Map <code>podinfo.local</code> to the controller address, then open:</p>

<span class="terminal-line fragment">http://podinfo.local/</span>

<p class="fragment small">The resource uses <code>networking.k8s.io/v1</code>, <code>ingressClassName</code>, <code>pathType</code>, and the current Service backend shape.</p>

---

## Remove the lab

```shell
kubectl delete -k 04-deployment
kind delete cluster --name workshop
```

<p class="fragment lead">You have followed one application from YAML to controllers, Pods, a stable Service, scaling, and a rolling update.</p>
