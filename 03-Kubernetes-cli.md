<p class="eyebrow">Module 03</p>

# kubectl and a local cluster

<p class="lead">Create a disposable kind cluster, query the API, and debug a workload.</p>

---

## Workshop tools

<div class="two-col">
  <div>
    <h3>Required</h3>
    <ul>
      <li><code>kubectl</code></li>
      <li><code>kind</code></li>
      <li>Docker or Podman</li>
    </ul>
  </div>
  <div class="fragment">
    <h3>macOS with Homebrew</h3>

```shell
brew install kubectl kind
```

  </div>
</div>

<p class="small">Other platforms: <a href="https://kubernetes.io/docs/tasks/tools/">install kubectl</a> and <a href="https://kind.sigs.k8s.io/docs/user/quick-start/#installation">install kind</a>.</p>

---

## Create the cluster

```shell
kind create cluster --name workshop --wait 90s
```

<p class="fragment">kind runs Kubernetes nodes as containers on your machine. The cluster is disposable, but the Kubernetes API is real.</p>

```shell
kubectl cluster-info --context kind-workshop
kubectl get nodes
```
<!-- .element: class="fragment" -->

<p class="fragment small">Expected: one control-plane node with status <code>Ready</code>.</p>

---

## kubeconfig and contexts

<div class="diagram">
  <div class="node data">kubeconfig</div>
  <div class="connector fragment">→</div>
  <div class="node control fragment">Context<br><span class="small">cluster + user + namespace</span></div>
  <div class="connector fragment">→</div>
  <div class="node workload fragment">API server</div>
</div>

```shell
kubectl config get-contexts
kubectl config current-context
kubectl config use-context kind-workshop
```

<p class="fragment">Read the current context before any command that changes a cluster.</p>

---

## Discover the API

```shell
kubectl api-resources
kubectl explain deployment
kubectl explain deployment.spec.template.spec.containers
```

<div class="two-col fragment">
  <div class="card">
    <h3>Resource discovery</h3>
    <p>Shows names, short names, API groups, scope, and kinds supported by this cluster.</p>
  </div>
  <div class="card">
    <h3>Schema help</h3>
    <p>Explains fields from the API schema that your current cluster serves.</p>
  </div>
</div>

---

## Create a namespace

```shell
kubectl create namespace workshop
kubectl config set-context --current --namespace=workshop
kubectl get namespace workshop
```

<p class="fragment">The context now supplies <code>--namespace=workshop</code> for namespaced commands.</p>

<span class="terminal-line fragment">kubectl config view --minify | grep namespace</span>

---

## Create the first Deployment

```shell
kubectl create deployment podinfo \
  --image=ghcr.io/stefanprodan/podinfo:6.14.1 \
  --replicas=2

kubectl rollout status deployment/podinfo
```

<div class="diagram fragment">
  <div class="node control">Deployment</div>
  <div class="connector">→</div>
  <div class="node data">ReplicaSet</div>
  <div class="connector">→</div>
  <div class="node workload">2 Pods</div>
</div>

---

## Inspect the resources

```shell
kubectl get deployment,replicaset,pod
kubectl get pods -o wide
kubectl describe deployment podinfo
```

<p class="fragment">Start broad, then inspect the resource whose status looks wrong.</p>

```shell
kubectl get pods \
  -l app=podinfo \
  -o custom-columns=NAME:.metadata.name,NODE:.spec.nodeName,READY:.status.containerStatuses[0].ready
```
<!-- .element: class="fragment" -->

---

## Use output for the next question

| Need | Command |
| --- | --- |
| Readable list | `kubectl get pods -o wide` |
| Complete object | `kubectl get pod NAME -o yaml` |
| Selected fields | `kubectl get pods -o custom-columns=...` |
| Script input | `kubectl get pods -o json` |

<p class="fragment">Prefer structured output for scripts. Human-readable columns can change.</p>

---

## A debugging path

<div class="flow">
  <div class="node">1. get<br><span class="small">find the symptom</span></div>
  <div class="node data fragment">2. describe<br><span class="small">read events and state</span></div>
  <div class="node workload fragment">3. logs<br><span class="small">inspect the process</span></div>
  <div class="node good fragment">4. exec or debug<br><span class="small">test inside the network</span></div>
</div>

```shell
kubectl get pods
kubectl describe pod POD_NAME
kubectl logs POD_NAME
kubectl logs POD_NAME --previous
```

---

## Reach and inspect the application

```shell
kubectl port-forward deployment/podinfo 9898:9898
```

<p class="fragment">Open <a href="http://localhost:9898">http://localhost:9898</a> in another terminal or browser.</p>

```shell
curl http://localhost:9898/healthz
kubectl exec deploy/podinfo -- cat /etc/os-release
```
<!-- .element: class="fragment" -->

<p class="small fragment">Press Ctrl+C to stop the port forward.</p>

---

## Scale and restart

```shell
kubectl scale deployment/podinfo --replicas=3
kubectl get pods --watch
```

<p class="fragment">Stop the watch with Ctrl+C, then restart every Pod through the Deployment:</p>

```shell
kubectl rollout restart deployment/podinfo
kubectl rollout status deployment/podinfo
kubectl rollout history deployment/podinfo
```
<!-- .element: class="fragment" -->

---

## Clean up the practice workload

```shell
kubectl delete deployment podinfo
```

<p class="fragment">Keep the <code>workshop</code> namespace and kind cluster. The next module applies reviewed YAML manifests to both.</p>

<p class="source">Command reference: <a href="https://kubernetes.io/docs/reference/kubectl/quick-reference/">kubectl quick reference</a></p>
