# Kubernetes workshop

This workshop explains Kubernetes through short Reveal.js decks, staged diagrams, and a hands-on lab. The source stays in Markdown. GitHub Actions builds the static site and publishes it to GitHub Pages.

View the published workshop at [demisr.github.io/workshop-kubernetes](https://demisr.github.io/workshop-kubernetes/).

## Course outline

1. **Kubernetes concepts** covers reconciliation, cluster architecture, scheduling, and container runtimes.
2. **Kubernetes resources** connects Pods, controllers, Services, configuration, storage, and namespaces.
3. **kubectl and a local cluster** creates a kind cluster and builds a repeatable debugging workflow.
4. **Deploy an application** applies manifests, exposes podinfo, scales it, and follows a rolling update.

The practical modules use [kind](https://kind.sigs.k8s.io/) so each participant can create a disposable cluster without cloud credentials.

## Run the workshop

Install these tools:

- [Docker](https://docs.docker.com/get-docker/) or [Podman](https://podman.io/docs/installation)
- [kind](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)

On macOS with Homebrew:

```shell
brew install kubectl kind
```

Create the cluster:

```shell
kind create cluster --name workshop --wait 90s
kubectl cluster-info --context kind-workshop
```

Follow modules 3 and 4 from the published site or from the Markdown files.

## Preview the slides

Install the Node.js dependencies:

```shell
npm ci
```

Start a live preview of the first module:

```shell
npm run dev
```

Build the complete static site:

```shell
npm run build
```

The build writes the site to `dist/`.

## Publish with GitHub Pages

The workflow in `.github/workflows/pages.yml` builds and deploys the site after each push to `master`.

In the GitHub repository settings, open **Pages** and set **Source** to **GitHub Actions**. The workflow needs no repository secret.

## Authoring notes

- Separate slides with `---`.
- Add `class="fragment"` to Markdown or HTML content that should appear in stages.
- Put shared visual styles in `CSS/custom.css`.
- Keep commands executable against the local kind cluster.
- Use stable Kubernetes API versions in checked-in manifests.

## Sources and credits

The original concepts module drew from Jérôme Petazzoni's [container.training Kubernetes material](https://container.training/). The refreshed workshop also links to the relevant [Kubernetes documentation](https://kubernetes.io/docs/home/) on each version-sensitive slide.
