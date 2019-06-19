kubectl get nodes


Create a namespace with the following command:
kubectl create -f- <<EOF
apiVersion: v1
kind: Namespace
metadata:
  name: hello
EOF

This is equivalent to kubectl create namespace hello.

Read back our object:
kubectl get namespace hello -o yaml


What's special about watch?
The API server itself doesn't do anything: it's just a fancy object store

All the actual logic in Kubernetes is implemented with controllers

A controller watches a set of resources, and takes action when they change

Examples:

when a Pod object is created, it gets scheduled and started

when a Pod belonging to a ReplicaSet terminates, it gets replaced

when a Deployment object is updated, it can trigger a rolling update


https://kadm-2019-06.container.training/#53




# EKS (the easy way)
Install eksctl

Set the usual environment variables

(AWS_DEFAULT_REGION, AWS_ACCESS_KEY, AWS_SECRET_ACCESS_KEY)

Create the cluster:

eksctl create cluster
Wait 15-20 minutes (yes, it's sloooooooooooooooooow)

Add cluster add-ons

(by default, it doesn't come with metrics-server, logging, etc.)
Delete the cluster:

eksctl delete cluster <clustername>
If you need to find the name of the cluster:

eksctl get clusters


https://docs.aws.amazon.com/fr_fr/eks/latest/userguide/getting-started-eksctl.html

# GKE https://kadm-2019-06.container.training/#210
# AKS https://kadm-2019-06.container.training/#213


w