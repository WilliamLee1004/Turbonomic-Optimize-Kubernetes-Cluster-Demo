# Turbonomic-Optimize-Kubernetes-Cluster-Demo
Two Scneario to optimize Kubernetes cluster resource 

# Background Intro
Over-provision problems and too many action that are difficult to manage are often encountered in customer’s K8s environments

In order to quickly reproduce this same scenario in our environment
So I created a Python Flask API, packaged it as an image and put it on icr (IBM Cloud Container Registry) and deployed it to IKS.
Then I tweaked it for Yaml so that I could quickly reproduce the over-provisioning and resource balance scenarios in K8s and Turbonomic environments.

Over-Provision scenario :
Turbonomic detects that a Pod has requested too much Resource in advance, but actually uses less than 0.1% of it, and then gives an action to suggest that the Pod Resource be downgraded.

Resource Balance scenario :
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
