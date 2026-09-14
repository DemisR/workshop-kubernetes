<p class="eyebrow">Module 01</p>

# Kubernetes concepts

<p class="lead">How Kubernetes turns a declared application state into running workloads.</p>

---

## The problem Kubernetes solves

You describe the result that you need:

<div class="stack">
  <div class="card fragment"><strong>Availability</strong><br>Keep three instances of the API ready.</div>
  <div class="card fragment"><strong>Networking</strong><br>Give clients one stable address while Pods change.</div>
  <div class="card fragment"><strong>Change</strong><br>Replace version 6.14.0 with 6.14.1 without dropping every instance at once.</div>
</div>

<p class="fragment">Kubernetes keeps working toward that result.</p>

---

## The reconciliation loop

<div class="diagram">
  <div class="node control">Desired state<br><strong>3 replicas</strong></div>
  <div class="connector fragment">⇄</div>
  <div class="node data fragment">API state<br><strong>2 ready</strong></div>
  <div class="connector fragment">→</div>
  <div class="node workload fragment">Controller<br><strong>creates 1 Pod</strong></div>
</div>

<p class="fragment">Controllers compare desired state with observed state. Each controller makes small changes until both match.</p>

> Declarative does not mean passive. It means that control loops choose the steps.

---

## Cluster architecture

<div class="cluster">
  <div class="cluster-zone">
    <strong>Control plane</strong>
    <div class="node control fragment">API server<br><span class="small">validates requests</span></div>
    <div class="node control fragment">etcd<br><span class="small">stores API data</span></div>
    <div class="node control fragment">Scheduler and controllers<br><span class="small">make decisions</span></div>
  </div>
  <div class="cluster-zone fragment">
    <strong>Worker nodes</strong>
    <div class="node workload">kubelet<br><span class="small">maintains Pods</span></div>
    <div class="node workload">Container runtime<br><span class="small">runs containers</span></div>
    <div class="node workload">Pod network and Service proxy<br><span class="small">moves traffic</span></div>
  </div>
</div>

<p class="source">Source: <a href="https://kubernetes.io/docs/concepts/architecture/">Kubernetes cluster architecture</a></p>

---

## A request through the control plane

<div class="flow">
  <div class="node">1. kubectl</div>
  <div class="node control fragment">2. API server</div>
  <div class="node data fragment">3. etcd</div>
  <div class="node workload fragment">4. Controller</div>
</div>

<ol>
  <li class="fragment">The API server authenticates, authorizes, and validates the request.</li>
  <li class="fragment">The API object becomes durable cluster state.</li>
  <li class="fragment">Controllers notice the change and act.</li>
</ol>

---

## Scheduling a Pod

<div class="two-col">
  <div>
    <h3>Inputs</h3>
    <ul>
      <li class="fragment">CPU and memory requests</li>
      <li class="fragment">Node selectors and affinity</li>
      <li class="fragment">Taints, tolerations, and topology rules</li>
    </ul>
  </div>
  <div>
    <h3>Decision</h3>
    <div class="diagram vertical">
      <div class="node bad fragment">Nodes that cannot run the Pod</div>
      <div class="connector fragment">↓</div>
      <div class="node good fragment">Best feasible node</div>
    </div>
  </div>
</div>

<p class="fragment">The scheduler assigns the Pod to a node. The kubelet on that node starts it.</p>

---

## What runs on a worker node

<div class="diagram">
  <div class="node control">API server</div>
  <div class="connector fragment">→</div>
  <div class="node workload fragment">kubelet</div>
  <div class="connector fragment">→</div>
  <div class="node workload fragment">CRI runtime</div>
  <div class="connector fragment">→</div>
  <div class="node good fragment">Containers</div>
</div>

- `kubelet` watches Pod specifications assigned to its node.
- A CRI-compatible runtime, commonly containerd or CRI-O, runs the containers.
- The network plugin gives each Pod network connectivity.

<p class="source">Source: <a href="https://kubernetes.io/docs/setup/production-environment/container-runtimes/">Kubernetes container runtimes</a></p>

---

## Docker images still work

<div class="two-col">
  <div class="card">
    <h3>Build time</h3>
    <p>Docker, BuildKit, Podman, or another OCI tool creates the image.</p>
  </div>
  <div class="card fragment">
    <h3>Run time</h3>
    <p>The kubelet asks a CRI runtime to pull and run the image.</p>
  </div>
</div>

<p class="fragment">Kubernetes removed its built-in Docker Engine adapter, <code>dockershim</code>, in version 1.24. It did not remove support for OCI container images.</p>

<p class="source">Source: <a href="https://kubernetes.io/docs/tasks/administer-cluster/migrating-from-dockershim/check-if-dockershim-removal-affects-you/">Dockershim removal</a></p>

---

## Self-healing after a failure

<div class="flow">
  <div class="node good">3 ready Pods</div>
  <div class="node bad fragment">1 Pod fails</div>
  <div class="node data fragment">Deployment sees 2 of 3</div>
  <div class="node good fragment">Replacement Pod becomes ready</div>
</div>

<p class="fragment">Kubernetes replaces the failed Pod. Your application must still handle data consistency, retries, and graceful shutdown.</p>

---

## What Kubernetes provides

<div class="two-col">
  <div>
    <h3>Built-in mechanisms</h3>
    <ul>
      <li>Scheduling and self-healing</li>
      <li>Service discovery and load distribution</li>
      <li>Rollouts, Jobs, and horizontal scaling</li>
      <li>Configuration, Secrets, and storage attachment</li>
    </ul>
  </div>
  <div class="fragment">
    <h3>You still choose</h3>
    <ul>
      <li>Cluster platform and upgrades</li>
      <li>Observability and delivery tools</li>
      <li>Security policy and network controls</li>
      <li>How the application stores state</li>
    </ul>
  </div>
</div>

---

## The mental model

<div class="diagram vertical">
  <div class="node control">Declare resources through the API</div>
  <div class="connector fragment">↓</div>
  <div class="node data fragment">Controllers reconcile desired and observed state</div>
  <div class="connector fragment">↓</div>
  <div class="node good fragment">Pods run on worker nodes</div>
</div>

<p class="fragment lead">Next: the resource types that describe an application.</p>
