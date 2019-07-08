# EKS (the easy way)
Install `eksctl`

Set the usual environment variables

(AWS_DEFAULT_REGION, AWS_ACCESS_KEY, AWS_SECRET_ACCESS_KEY)

Create the cluster:

`eksctl create cluster`
Wait 15-20 minutes (yes, it's sloooooooooooooooooow)

```
eksctl create cluster --name workshop-1 --nodes 4 --region eu-west-3 --tags environment=sandbox
```


Add cluster add-ons

(by default, it doesn't come with metrics-server, logging, etc.)
Delete the cluster:

```
eksctl delete cluster --name=<name>
```
If you need to find the name of the cluster:

```
eksctl get clusters
```

https://docs.aws.amazon.com/fr_fr/eks/latest/userguide/getting-started-eksctl.html


https://aws.amazon.com/fr/premiumsupport/knowledge-center/amazon-eks-cluster-access/

kubectl get -n kube-system configmap/aws-auth -o yaml > /tmp/aws-auth-patch.yml
code /tmp/aws-auth-patch.yml
kubectl patch configmap/aws-auth -n kube-system --patch "$(cat /tmp/aws-auth-patch.yml)"

```
  mapRoles: |
    - groups:
      - system:bootstrappers
      - system:nodes
      rolearn: arn:aws:iam::269592875733:role/eksctl-workshop-1-nodegroup-ng-c8-NodeInstanceRole-K2WG6VX59DUS
      username: system:node:{{EC2PrivateDNSName}}
  mapUsers: |
    - userarn: arn:aws:iam::269592875733:user/john.doe
      username: john.doe
      groups:
        - system:masters
    - userarn: arn:aws:iam::269592875733:user/marco.rossi
      username: marco.rossi
      groups:
        - system:masters
```

Create an access key for a user 
`aws iam create-access-key --user-name <username>`

In AWS accounts that have never created a load balancer before, it’s possible that the service role for ELB might not exist yet.

We can check for the role, and create it if it’s missing.

Copy/Paste the following commands into your Cloud9 workspace:
```
aws iam get-role --role-name "AWSServiceRoleForElasticLoadBalancing" || aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"
```

# GKE https://kadm-2019-06.container.training/#210
# AKS https://kadm-2019-06.container.training/#213

