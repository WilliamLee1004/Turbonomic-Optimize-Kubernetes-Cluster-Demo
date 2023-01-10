# Turbonomic-Optimize-Kubernetes-Cluster-Demo
Two Scneario to optimize Kubernetes cluster resource 

# Background Intro
Over-provision problems and too many action that are difficult to manage are often encountered in customer’s K8s environments

In order to quickly reproduce this same scenario in our environment
So I created a Python Flask API, packaged it as an image and put it on icr (IBM Cloud Container Registry) and deployed it to IKS.
Then I tweaked it for Yaml so that I could quickly reproduce the over-provisioning and resource balance scenarios in K8s and Turbonomic environments.

1. Over-Provision scenario :
Turbonomic detects that a Pod has requested too much Resource in advance, but actually uses less than 0.1% of it, and then gives an action to suggest that the Pod Resource be downgraded.

2. Resource Balance scenario :
Turbonomic detects that one of the Nodes is running out of Resource and suggests to move some of the Pods to other Nodes to balance the Resource usage of each Node.

## Pre-Requirement
1. Install [IBM Cloud CLI](https://cloud.ibm.com/docs/containers?topic=containers-cs_cli_install)

2. Install [Docker CLI](https://docs.docker.com/engine/installation/)

3. Build image

4. [Push image to IBM Cloud Container Registry](https://cloud.ibm.com/registry/start)

5. Create a IKS Cluster

6. Create a Turbonomic platform

7. [Install Turbonomic Platform Operator on IKS cluster](https://github.com/turbonomic/t8c-install) 


## Deployments
Get the secrets, persistent volume in place and apply the deployments for the MySQL database and Flask API

1. Add the secrets to your kubernetes cluster: `kubectl apply -f flaskapi-secrets.yml`
2. Create the MySQL deployment: `kubectl apply -f mysql-deployment.yml`
3. Create the Flask API deployment: `kubectl apply -f flaskapp-deployment.yml`

You can check the status of the pods, services and deployments.

## Turbonomic Optimize Kubernetes Action

### Over-Provision Optimize Action
<img width="1380" alt="截圖 2022-09-05 上午11 09 32" src="https://user-images.githubusercontent.com/58331502/211477339-4afb9b92-2630-4138-849c-a73dd5494414.png">


<img width="1329" alt="截圖 2022-09-05 上午11 10 30" src="https://user-images.githubusercontent.com/58331502/211477450-0b34b33f-9c47-4289-aa25-2c2dd6f80e3e.png">
<img width="1395" alt="截圖 2022-09-05 上午11 10 12" src="https://user-images.githubusercontent.com/58331502/211477540-ba0b5f32-8b8e-4b4b-b0e8-045fd6058e50.png">
<img width="1271" alt="截圖 2022-09-05 上午11 11 00" src="https://user-images.githubusercontent.com/58331502/211477652-07cff2a9-c819-42ec-b976-ed95bd8b86d2.png">
<img width="1402" alt="截圖 2022-09-05 上午11 10 50" src="https://user-images.githubusercontent.com/58331502/211477685-d01c00e2-b7e3-4928-bc40-4e022f6ac404.png">

### Resource Balance Optimize Action

<img width="423" alt="圖片 1" src="https://user-images.githubusercontent.com/58331502/211479139-70f97f77-7fc2-4ca3-9e2f-35abab518a94.png">

