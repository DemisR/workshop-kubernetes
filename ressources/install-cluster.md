# Create the workshop cluster

This workshop uses kind instead of a shared cloud cluster. Each participant gets an isolated cluster and needs no cloud credentials.

Create the cluster:

```shell
kind create cluster --name workshop --wait 90s
```

Check the context and node:

```shell
kubectl config current-context
kubectl get nodes
```

The current context must be `kind-workshop`, and the node must reach the `Ready` state.

Delete the cluster after the workshop:

```shell
kind delete cluster --name workshop
```
