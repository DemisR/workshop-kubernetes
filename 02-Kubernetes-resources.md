<p class="eyebrow">Module 02</p>

# Kubernetes resources

<p class="lead">The API objects used to run, connect, configure, and store an application.</p>

---

## Every resource has the same envelope

```yaml
apiVersion: apps/v1       # API group and version
kind: Deployment          # Resource type
metadata:
  name: podinfo
  namespace: workshop
spec:                     # Desired state
  replicas: 2
```

<p class="fragment"><code>status</code> reports observed state. Controllers update it. You normally write <code>spec</code>.</p>

<span class="terminal-line fragment">kubectl explain deployment.spec</span>

---

## Pod

<div class="two-col wide-right">
  <div>
    <p>A Pod is the smallest deployable unit.</p>
    <ul>
      <li class="fragment">One or more containers</li>
      <li class="fragment">One IP address</li>
      <li class="fragment">Shared network and attached volumes</li>
      <li class="fragment">Scheduled together on one node</li>
    </ul>
  </div>
  <div>

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: podinfo
spec:
  containers:
    - name: podinfo
      image: ghcr.io/stefanprodan/podinfo:6.14.1
      ports:
        - containerPort: 9898
```

  </div>
</div>

---

## Labels connect resources

<div class="diagram">
  <div class="node control">Deployment<br><code>app=podinfo</code></div>
  <div class="connector fragment">→</div>
  <div class="node workload fragment">Pod<br><code>app=podinfo</code></div>
  <div class="node workload fragment">Pod<br><code>app=podinfo</code></div>
  <div class="node workload fragment">Pod<br><code>app=podinfo</code></div>
</div>

<p class="fragment">A selector finds resources by label. Names identify one object. Labels create a changing set.</p>

```shell
kubectl get pods -l app.kubernetes.io/name=podinfo
```

---

## Deployment, ReplicaSet, and Pods

<div class="diagram vertical">
  <div class="node control">Deployment<br><span class="small">rollout policy and Pod template</span></div>
  <div class="connector fragment">↓</div>
  <div class="node data fragment">ReplicaSet<br><span class="small">replica count for one template</span></div>
  <div class="connector fragment">↓</div>
  <div class="diagram fragment">
    <div class="node workload">Pod</div>
    <div class="node workload">Pod</div>
    <div class="node workload">Pod</div>
  </div>
</div>

<p class="fragment">Create Deployments. Let the Deployment manage ReplicaSets and Pods.</p>

---

## Service

<p>A Service gives a changing set of Pods a stable network name and virtual IP.</p>

<div class="diagram">
  <div class="node control">Client</div>
  <div class="connector fragment">→</div>
  <div class="node data fragment">Service<br><code>podinfo:9898</code></div>
  <div class="connector fragment">→</div>
  <div class="diagram vertical fragment">
    <div class="node workload">Ready Pod</div>
    <div class="node workload">Ready Pod</div>
  </div>
</div>

<p class="fragment">The Service selects Pods by label. It does not care which node hosts them.</p>

---

## Service types

| Type | Reachable from | Typical use |
| --- | --- | --- |
| `ClusterIP` | Inside the cluster | Service-to-Service traffic |
| `NodePort` | A port on every node | Labs and infrastructure integrations |
| `LoadBalancer` | An external address | Cloud or load-balancer integration |
| `ExternalName` | Cluster DNS | Alias for an external DNS name |

<p class="fragment"><code>ClusterIP</code> is the default. HTTP routing across several Services usually belongs in Gateway API or Ingress.</p>

---

## EndpointSlices follow the Pods

<div class="flow">
  <div class="node control">Service selector</div>
  <div class="node data fragment">EndpointSlice<br><code>10.244.1.5:9898</code></div>
  <div class="node data fragment">EndpointSlice<br><code>10.244.2.8:9898</code></div>
  <div class="node workload fragment">Service proxy or network plugin</div>
</div>

<p class="fragment">Kubernetes updates EndpointSlices as matching Pods appear, disappear, or become unready.</p>

<p class="source">Source: <a href="https://kubernetes.io/docs/concepts/services-networking/">Services, load balancing, and networking</a></p>

---

## ConfigMap and Secret

<div class="two-col">
  <div class="card">
    <h3>ConfigMap</h3>
    <p>Non-confidential configuration such as a feature flag or log level.</p>
  </div>
  <div class="card fragment">
    <h3>Secret</h3>
    <p>Sensitive values such as a token or password. Base64 encoding does not encrypt the value.</p>
  </div>
</div>

<p class="fragment">A Pod can read both as environment variables or mounted files. Your cluster still needs access controls and encryption appropriate to the data.</p>

---

## Storage lifetime

| Storage | Survives a container restart | Survives Pod replacement |
| --- | :---: | :---: |
| Container writable layer | No | No |
| Pod volume such as `emptyDir` | Yes | No |
| PersistentVolumeClaim | Yes | Yes |

<div class="diagram fragment">
  <div class="node workload">Pod</div>
  <div class="connector">→</div>
  <div class="node data">PersistentVolumeClaim</div>
  <div class="connector">→</div>
  <div class="node good">Storage provided by a StorageClass</div>
</div>

---

## Workload controllers

| Resource | Use it for |
| --- | --- |
| Deployment | Interchangeable, usually stateless replicas |
| StatefulSet | Pods that need stable identity or storage |
| DaemonSet | One Pod on every selected node |
| Job | Work that runs to completion |
| CronJob | Jobs on a schedule |

<p class="fragment">Choose the controller from the workload's lifecycle. The container image does not decide.</p>

---

## Namespaces and boundaries

<div class="two-col">
  <div>
    <h3>Namespaces group resources</h3>
    <p>Names only need to be unique inside a namespace.</p>
  </div>
  <div class="fragment">
    <h3>Policies create boundaries</h3>
    <p>RBAC, ResourceQuota, LimitRange, and NetworkPolicy control access and consumption.</p>
  </div>
</div>

> A namespace alone does not isolate network traffic or make a hostile workload safe.

---

## One application, several resources

<div class="diagram vertical">
  <div class="node control">Deployment<br><span class="small">keeps Pods running</span></div>
  <div class="diagram fragment">
    <div class="node data">ConfigMap and Secret</div>
    <div class="node workload">Pods</div>
    <div class="node data">PersistentVolumeClaim</div>
  </div>
  <div class="node good fragment">Service<br><span class="small">gives the Pods one endpoint</span></div>
</div>

<p class="fragment lead">Next: use <code>kubectl</code> to see these relationships in a live cluster.</p>
