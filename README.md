# kube-cloudwatch_demo
Kubernetes configuration to store logs at AWS cloudwatch logs

## Task
The purpose of this test is to solve the problem of collecting logs from Kubernetes containers. The data source is the processes running in containers that output logs to stdout. The task is to aggregate the entire output into AWS CloudWatch logs to be able to parse and analyze these data. 
## Tools
For such task we definetely will use kubernetes and fluentd as log shipper ( https://www.fluentd.org/ )
## Prerequisistes
* minikube https://github.com/kubernetes/minikube ( fluentd daemonset confguration use specific minkube mount points) or usual kuberenetes cluster with kubectl installed
* docker registry ( ive used default hub.docker.com)
## Steps to reproduce
* clone this repo
* build and push docker image(inside repo folder):
```
docker build . -t timber/fluentd-cloudwatch
docker push timber/fluentd-cloudwatch
```
* edit fluentd-aws-secret.yaml file with proper AWS key and secret:
```
...
data:
  AWS_ACCESS_KEY_ID: <key_id>
  AWS_SECRET_ACCESS_KEY: <secret_id>
  ```
* deploy secret to minikube:
```
kubectl apply -f fluentd-aws-secret.yaml
```
* edit fluentd-aws-config.yaml with proper region and AWS log group name: 
```
...
data:
  AWS_REGION: us-east-1
  CW_LOG_GROUP: kubernetes
```
* deploy config to minikube:
```
kubectl apply -f fluentd-aws-config.yaml
```
* deploy fluentd daemonset:
```
kubectl apply -f fluentd-aws-daemonset.yaml
```
* check if everything up and running:
```
kubectl get secret,configmap,daemonset,pod --selector=app=fluentd-cloudwatch --namespace=kube-system
```
