# How to Deployment Dify on Kubernetes
## Table of Contents
- [Deployment Flow](#deployment-flow)
- [Requirement](#requirement)
- [Step Installation](#step-installation)
  - [Clone Repository](#clone-repository)
  - [Install Redis](#install-redis)
  - [Install PostgreSQL](#install-postgresql)
  - [Install Weaviate](#install-weaviate)
  - [Install Dify API](#install-dify-api)
  - [Install Dify Web](#install-dify-web)
  - [Install Dify Sandbox](#install-dify-sandbox)
  - [Install Dify Worker](#install-dify-worker)
  - [Install SSRF Proxy](#install-ssrf-proxy)
  - [Setup CI/CD Pipeline Jenkins](#setup-cicd-pipeline-jenkins)
## Deployment Flow
![Deployment Flow](images/flow.gif)
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
git clone https://github.com/mrofisr/dify-kubernetes.git
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
