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