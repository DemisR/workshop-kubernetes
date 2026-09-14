# Workshop requirements

Install the following tools before the workshop:

- [Docker](https://docs.docker.com/get-docker/) or [Podman](https://podman.io/docs/installation)
- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

On macOS with Homebrew:

```shell
brew install kubectl kind
```

Verify the installation:

```shell
kind version
kubectl version --client
```
