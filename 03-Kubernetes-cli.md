# The Kubernetes CLI : kubectl
## Prepare your cli

Install `kubectl`
```
brew install kubernetes-cli
```
Check version `kubectl version`

---

## Enable kubectl autocompletion
**Bash on MacOS**
You can test if you have bash-completion v2 already installed with `type _init_completion`. If not, you can install it with Homebrew:

```
brew install bash-completion@2
```

As stated in the output of this command, add the following to your `~/.bashrc` file:

```
export BASH_COMPLETION_COMPAT_DIR="/usr/local/etc/bash_completion.d"
[[ -r "/usr/local/etc/profile.d/bash_completion.sh" ]] && . "/usr/local/etc/profile.d/bash_completion.sh"
```

Enable kubectl autocompletion

```
echo 'source <(kubectl completion bash)' >>~/.bashrc
```

---

## Enable kubectl autocompletion
**Zsh on MacOS**

```
echo 'source <(kubectl completion zsh)' >>~/.zshrc 
```

---

## Workshop cluster

The cluster for the workshop is a manager k8s on AWS ( EKS ).
Created with the tool `eksctl`.

---

## Requirement for easy access to EKS cluster
Install `awscli`
```
brew install aws-cli
```

Configure with your aws key
```
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: eu-west-3
Default output format [None]: json
```

---

## Configure your kubectl 

We can update our kubectl config file to use the aws-cli for pulling IAM tokens with this command:

```
aws eks update-kubeconfig --name workshop-1 --region eu-west-3 # this updates kubeconfig to pull iam tokens using aws-cli
```

Check the accessibility to the cluster
```
kubectl get nodes
kubectl get service 
```

---

## Create your namespace

Create a namespace with the following command:

```
kubectl create -f- <<EOF
apiVersion: v1
kind: Namespace
metadata:
  name: demis
EOF
```

This is equivalent to `kubectl create namespace demis`.

Read back our object:

```
kubectl get namespace demis -o yaml
```

Set you context namespace
```
kubectl config set-context --current --namespace=demis
```

---

## Base commannds
```
kubectl get namespaces 
kubectl get pods  --all-namespaces 
kubectl get service 

kubectl describe pod <name>  

kubectl cluster-info
kubectl describe node <node>
```

---

## Run a pod

```
kubectl run web --image=nginx --replicas=3 --namespace demis 
kubectl get pods --namespace demis
```
---

![](images/kubectl-run-slideshow/01.svg)

---

![](images/kubectl-run-slideshow/02.svg)

---

![](images/kubectl-run-slideshow/03.svg)

---

![](images/kubectl-run-slideshow/04.svg)

---

![](images/kubectl-run-slideshow/05.svg)

---

![](images/kubectl-run-slideshow/06.svg)

---

![](images/kubectl-run-slideshow/07.svg)

---

![](images/kubectl-run-slideshow/08.svg)

---

![](images/kubectl-run-slideshow/09.svg)

---

![](images/kubectl-run-slideshow/10.svg)

---

![](images/kubectl-run-slideshow/11.svg)

---

![](images/kubectl-run-slideshow/12.svg)

---

![](images/kubectl-run-slideshow/13.svg)

---

![](images/kubectl-run-slideshow/14.svg)

---

![](images/kubectl-run-slideshow/15.svg)

---

![](images/kubectl-run-slideshow/16.svg)

---

![](images/kubectl-run-slideshow/17.svg)

---

![](images/kubectl-run-slideshow/18.svg)

---

![](images/kubectl-run-slideshow/19.svg)

---

```
kubectl get deployments  
kubectl delete deployments web
```

---






