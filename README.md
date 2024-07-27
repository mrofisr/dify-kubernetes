# How to Deployment Dify on Kubernetes
## Table of Contents
## Deployment Flow
## Requirement
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [Helm](https://helm.sh/docs/intro/install/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Docker](https://docs.docker.com/get-docker/)
- [Jenkins](https://www.jenkins.io/doc/book/installing/)
- [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
## Step Installation
### Clone Repository
```bash
git clone https://github.com/mrofisr/dify
```
### Install Ingress NGINX
```bash
kubectl apply -f https://raw.githubusercontent.com/nginxinc/kubernetes-ingress/v3.6.1/deploy/crds.yaml
```
### Install Redis
```
kubectl apply -f redis
```
### Install PostgreSQL
```
kubectl apply -f postgresql
```
### Install Weaviate
```bash
helm install dify-weaviate weaviate/weaviate --values kubernetes/weaviate/values.yaml --namespace dify --create-namespace
```
### Install Dify API
```bash
kubectl apply -f dify-api
```
### Install Dify Web
```bash
kubectl apply -f dify-web
```
### Install Dify Sandbox
```bash
kubectl apply -f dify-sandbox
```
### Install Dify Worker
```bash
kubectl apply -f dify-worker
```
### Install SSRF Proxy
```bash
kubectl apply -f ssrf-proxy
```
### Setup CI/CD Pipeline Jenkins
