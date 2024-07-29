# COWORKING
The Coworking Space Service is a set of APIs that enables users to request one-time tokens and administrators to authorize access to a coworking space.

This service follows a microservice pattern and the APIs are split into distinct services that can be deployed and managed independently of one another.

## Getting Started

Detail on how to the application deployment process works and how to deploy changes.

### Prerequisites

Requirements for the software and other tools to build, test and push 
- [Git](https://git-scm.com/downloads)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [eksctl](https://eksctl.io/installation/)
- [AWS CLI](https://aws.amazon.com/cli/)

### Deployment

Configure aws credentials

    aws configure

Create an EKS Cluster using eksctl

    eksctl create cluster --name <cluster_name> --region <aws_region_code> --nodegroup-name <nodegroup_name> --node-type t3.small --nodes 1 --nodes-min 1 --nodes-max 2

Update the Kubeconfig 

    aws eks --region <aws_region_code> update-kubeconfig --name <cluster_name>

Apply environment variables configmap and secret, remember to replace actual secret value

    kubectl apply -f deployment/env-configmap.yaml
    kubectl apply -f deployment/env-secret.yaml

Deploy the database

    kubectl apply -f pvc.yaml
    kubectl apply -f pv.yaml
    kubectl apply -f postgresql-deployment.yaml

Configure Continous Integraion using AWS CodeBuild
 - Create an Amazon ECR repository using AWS console.

 - Create an Amazon CodeBuild project that is connected to the project's GitHub repository.

 - Build script will be in "buildspec.yml", which CodeBuild will automatically grab when triggering new build.

 - Triggers can be customize in CodeBuild project.

 - CodeBuild will build the project docker image and push it to AWS ECR repository.

Deploy the application, the deployment will try to pull the latest image from AWS ECR repository to run.

    kubectl apply -f deployment/coworking-deployment.yaml

Verify the Deployment

    kubectl get svc

Set up AWS CloudWatch using [this guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html).