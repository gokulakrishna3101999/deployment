# Deployment

General EKS command

1. To create a eks cluster - eksctl create cluster --name aws-eks-cluster --region us-east-1 --nodegroup-name eks-cluster-node --node-type t3.small --nodes 2
2. To delete all deployment in a default namespace - kubectl delete deployment --all -n default
3. To delete all statefulset in a default namespace - kubectl delete statefulSet --all -n default
4. To delete all pods in a default namespace - kubectl delete pods --all -n default
5. To get details of all the pods in the default namespace - kubectl get pods -n default
6. To create resource from the yaml files - kubectl apply -f .
7. To get logs of a pods - kubect logs <podname> 
8. To delete a specific pod in a namespace - kubectl delete pod <podname> -n default



Forwarding for pods in eks to local

1. kubectl port-forward service/eureka-service 8761:8761
2. kubectl port-forward service/restaurant-service 8080:8080
3. kubectl port-forward service/foodcatalogue-service 8082:8082
4. kubectl port-forward service/user-service 8081:8081
5. kubectl port-forward service/order-service 8084:8084



Steps for creating and configuring ALB in EKS (refer aws documentation - https://docs.aws.amazon.com/eks/latest/userguide/lbc-manifest.html) 

1. Download the permission policy - curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.14.1/docs/install/iam_policy.json

2. Create the IAM policy in AWS - aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json

3. Associate the IAM OIDC provider with your EKS cluster in your AWS region - eksctl utils associate-iam-oidc-provider --region=us-east-1 --cluster=aws-eks-cluster --approve

4. Attach this policy to your EKS cluster (IRSA) - eksctl create iamserviceaccount --cluster=aws-eks-cluster --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::748255946024:policy/AWSLoadBalancerControllerIAMPolicy --approve

5. Install cert-manager (required helper) - kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.13.5/cert-manager.yaml

6. Verify it’s running - kubectl get pods -n cert-manager

7. Install AWS Load Balancer Controller (Download the controller manifest) - curl -Lo v2_14_1_full.yaml https://github.com/kubernetes-sigs/aws-load-balancer-controller/releases/download/v2.14.1/v2_14_1_full.yaml

8. remove the ServiceAccount section in the v2_14_1_full.yaml manifest file

apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    app.kubernetes.io/component: controller
    app.kubernetes.io/name: aws-load-balancer-controller
  name: aws-load-balancer-controller
  namespace: kube-system
---

9. Replace your-cluster-name in the Deployment spec section of the file.
    spec:
      containers:
      - args:
        - --cluster-name=aws-eks-cluster
        - --ingress-class=alb
        - --aws-vpc-id=vpc-0a5706ca5ae1f406a
        - --aws-region=us-east-1
        image: public.ecr.aws/eks/aws-load-balancer-controller:v2.14.1

10. To deploy alb in eks - kubectl apply -f v2_14_1_full.yaml