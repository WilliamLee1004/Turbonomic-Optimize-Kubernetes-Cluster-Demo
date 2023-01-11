# Turbonomic-Optimize-Kubernetes-Cluster-Demo
Two Scenarios for Turbonomic to optimize Kubernetes cluster resources.

## Background Intro
Over-provision problems are often encountered in customer’s K8s environments

In order to quickly reproduce this same scenario in our environment
So I created a Python Flask API, packaged it as an image and put it on icr (IBM Cloud Container Registry) and deployed it to IKS.
Then I tweaked it for Yaml so that I could quickly reproduce the over-provisioning and resource balance scenarios on K8s and Turbonomic environments.

1. Over-Provision scenario :
Turbonomic detects that a Pod has requested too much Resource in advance, but actually uses less than 0.1% of it, and then gives an action to suggest that the Pod Resource be downgraded.

2. Resource Balance scenario :
Turbonomic detects that one of the Nodes is running out of Resource and suggests to move some of the Pods to other Nodes to balance the Resource usage of each Node.

## Pre-Requirement
1. Install [IBM Cloud CLI](https://cloud.ibm.com/docs/containers?topic=containers-cs_cli_install)
2. Install [Docker CLI](https://docs.docker.com/engine/installation/)
3. Create a IKS Cluster
4. Create a Turbonomic platform

## Getting started
1. Clone the repository
2. Pull the latest mysql image from `Dockerhub`: `Docker pull mysql`
3. Build a kubernetes-api image with the Dockerfile in this repo: `Docker build . -t flask-api`
4. [Push image to IBM Cloud Container Registry](https://cloud.ibm.com/registry/start)
5. [Install Turbonomic Platform Operator on IKS cluster](https://github.com/turbonomic/t8c-install) 
6. Choose one of your nodes, and add a label to it:
```bash
kubectl label nodes <your-node-name> demo_scenario_2=balance-resource
```

## Secrets
`Kubernetes Secrets` can store and manage sensitive information. For this example we will define a password for the
`root` user of the `MySQL` server using the `Opaque` secret type. For more info: https://kubernetes.io/docs/concepts/configuration/secret/

## Yaml Config 
In `flaskapp-deployment.yml` find `spec:` append:
```yaml
spec:
  nodeSelector:
    demo_scenario_2: balance-resource
```

and

```yaml
resources:
  requests:
    memory: "1024Mi"
    cpu: "1"
  limits:
    memory: "2048Mi"
    cpu: "2"
```

(Optional) If you want use instana trace, you need find `spec:` in `flaskapp-deployment.yml` append:


```yaml
- name: INSTANA_AGENT_HOST
  valueFrom:
    fieldRef:
      fieldPath: status.hostIP
```


after appended the yaml `spec:` will like this:
```yaml
spec:
  nodeSelector:
    demo_scenario_2: balance-resource
  containers:
    - name: flaskapi
      image: icr.io/<namespace>/<image_name:tag>
      resources:
        requests:
          memory: "1024Mi"
          cpu: "1"
        limits:
          memory: "2048Mi"
          cpu: "2"
      imagePullPolicy: IfNotPresent
      ports:
        - containerPort: 5000
      env:
        - name: db_root_password
          valueFrom:
            secretKeyRef:
              name: flaskapi-secrets
              key: db_root_password
        - name: db_name
          value: flaskapi
        - name: INSTANA_AGENT_HOST
          valueFrom:
            fieldRef:
              fieldPath: status.hostIP
```

## Deployments
Get the secrets, persistent volume in place and apply the deployments for the MySQL database and Flask API

1. Add the secrets to your kubernetes cluster: `kubectl apply -f flaskapi-secrets.yml`
2. Create the MySQL deployment: `kubectl apply -f mysql-deployment.yml`
3. Create the Flask API deployment: `kubectl apply -f flaskapp-deployment.yml`

You can check the status of the pods, services and deployments.

## Turbonomic Optimize Kubernetes Cluster Action

### Over-Provision Optimize Action
<img width="1380" alt="截圖 2022-09-05 上午11 09 32" src="https://user-images.githubusercontent.com/58331502/211477339-4afb9b92-2630-4138-849c-a73dd5494414.png">


<img width="1329" alt="截圖 2022-09-05 上午11 10 30" src="https://user-images.githubusercontent.com/58331502/211477450-0b34b33f-9c47-4289-aa25-2c2dd6f80e3e.png">
<img width="1395" alt="截圖 2022-09-05 上午11 10 12" src="https://user-images.githubusercontent.com/58331502/211477540-ba0b5f32-8b8e-4b4b-b0e8-045fd6058e50.png">
<img width="1271" alt="截圖 2022-09-05 上午11 11 00" src="https://user-images.githubusercontent.com/58331502/211477652-07cff2a9-c819-42ec-b976-ed95bd8b86d2.png">
<img width="1402" alt="截圖 2022-09-05 上午11 10 50" src="https://user-images.githubusercontent.com/58331502/211477685-d01c00e2-b7e3-4928-bc40-4e022f6ac404.png">

### Resource Balance Optimize Action

<img width="423" alt="圖片 1" src="https://user-images.githubusercontent.com/58331502/211479139-70f97f77-7fc2-4ca3-9e2f-35abab518a94.png">

## Conclusion
From the result, we know that Turbonomic can solve Over-provision problems and automatically balance resource usage per node on K8s cluster.

In addition, we can specify how many resources a pod or cotainer needs by request and limit, and finally deploy them to one of the nodes with Node label and selector to quickly reproduce the Over-provisioning problem and the problem of insufficient resources on a node that needs to be rebalanced.
