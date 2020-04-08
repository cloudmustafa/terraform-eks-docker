# AWS IAM Authenticator
a client makes q requests to an API server passing an access-token with the user’s identificator
API servers pass this token to another Kubernetes Control Plane’s service — aws-iam-authenticator
aws-iam-authenticator asks AWS IAM service and passes this identificator to check if this is valid user and do he has permissions to access the EKS cluster
AWS IAM makes internal authentification check by using a secret key tied with the ACCESS_KEY passed in a token as a user’s identificator
AWS IAM makes internal authorization check by checking IAM policies tied to this user — a user without API calls to eks::* resources must be declined
aws-iam-authenticator goes back to the Kubernetes cluster to check via aws-auth ConfigMap (will see it soon in the AWS EKS aws-auth ConfigMap) - do this user has permissions to access this cluster
aws-iam-authenticator returns approve/decline response to the API server
API server in its turn will or perform actions requested by the user — or will return the “You must be logged in to the server (Unauthorized)” message

# Setting up AWS EKS (AWS Hosted Kubernetes)


## Prerequisites. Installing terraform, Kubernetes CLI, and AWS-IAM-Authenticator
choco install terraform
choco install aws-iam-authenticator
choco install kubernetes-cli


## Modify providers.tf

## Delete the local config file from users/.Kube folder

## Get the ECR Login

$(aws ecr get-login --no-include-email --region us-east-1) //Replace with your own region information

## Terraform apply

terraform init
terraform plan
terraform apply
terraform graph | dot -Tsvg > graph.svg
## Configure kubectl
```
terraform output kubeconfig # save output in ~/.kube/config
aws eks --region us-east-1 update-kubeconfig --name terraform-eks-demo
```

## Configure config-map-auth-aws
```
terraform output config-map-aws-auth # save output in config-map-aws-auth.yaml
kubectl apply -f config-map-aws-auth.yaml
```

## See nodes coming up
```
kubectl get nodes --watch
```
## Deploy the application from ECR
kubectl apply -f Deployment.yaml 


## Review the services to make sure they are running and to get the NLB address
kubectl get services
kubectl describe pods <POD NAME>
kubectl logs <POD NAME>

## Deploy the load balancer

kubectl expose deployment hello-world-deployment --type="LoadBalancer"

## Scaling pods
kubectl create -f replica.yaml

## Tear down the deployment
kubectl delete service/hello-world-deployment
kubectl delete deployment/hello-world-deployment
OR
kubectl delete -f Deployment.yaml
## Destroy
```
terraform destroy
```
## Other useful commands


## Rolling updates
$ kubectl set image deployment.<VERSION>.apps/DEPLOYMENT-NAME NEW-IMAGE-VERSION  --record

## ROLL BACK
$ kubectl rollout undo deployment.v1.apps/DEPLOYMENT-NAME
deployment.apps/DEPLOYMENT-NAME rolled back

## SCALE DEPLOYMENT
$ kubectl scale deployment.v1.apps/DEPLOYMENT-NAME --replicas=9
deployment.apps/DEPLOYMENT-NAME scaled


