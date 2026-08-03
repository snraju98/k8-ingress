create OIDC provider
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster roboshop \
    --approve

Download IAM Policy

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v3.4.2/docs/install/iam_policy.json

Create IAM Policy
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json

create SA

eksctl create iamserviceaccount \
--cluster=roboshop \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::786020472029:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve


Install AWS LoadBalancer Controller Drivers

helm repo add eks https://aws.github.io/eks-charts
helm repo update

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=roboshop --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
