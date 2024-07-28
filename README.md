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
![Deployment Flow](https://raw.githubusercontent.com/mrofisr/dify-kubernetes/main/image/flow.gif)
## Requirement
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [Helm](https://helm.sh/docs/intro/install/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
- [Docker](https://docs.docker.com/get-docker/)
- [Jenkins](https://www.jenkins.io/doc/book/installing/)
- [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
## Step Installation
### Install Docker on Ubuntu
```bash
#!/bin/bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin zip net-tools unzip -y

sudo usermod -aG docker $USER
```
### Install Minikube on Ubuntu
```bash
#!/bin/bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
minikube start --driver=docker
```
### Setup Remote MiniKube Cluster
1. Create directory /etc/nginx
2. Create file minikube.conf
```bash
server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;
    auth_basic "Administrator’s Area";
    auth_basic_user_file /etc/nginx/.htpasswd;

    location / {
        proxy_pass https://`minikube ip`:8443;
        proxy_ssl_certificate /etc/nginx/certs/minikube-client.crt;
        proxy_ssl_certificate_key /etc/nginx/certs/minikube-client.key;
    }
}
```
3. Install Apache-Utils for basic auth
```bash
apt-get install apache2-utils -y
```
4. Create basic auth
```bash
htpasswd -c /etc/nginx/.htpasswd minikube
```
5. Run Docker Container NGINX
```bash
docker run -d \
--restart=always \
--name nginx \
-p 8080:80 \
-v $HOME/.minikube/profiles/minikube/client.key:/etc/nginx/certs/minikube-client.key \
-v $HOME/.minikube/profiles/minikube/client.crt:/etc/nginx/certs/minikube-client.crt \
-v /etc/nginx/conf.d/:/etc/nginx/conf.d \
-v /etc/nginx/.htpasswd:/etc/nginx/.htpasswd \
nginx
```
6. Create file .kube/config in local machine
```bash
apiVersion: v1
clusters:
- cluster:
    extensions:
    - extension:
        last-update: Sat, 27 Jul 2024 00:55:49 UTC
        provider: minikube.sigs.k8s.io
        version: v1.33.1
      name: cluster_info
    server: http://minikube:12345@public-ip:8080
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Sat, 27 Jul 2024 00:55:49 UTC
        provider: minikube.sigs.k8s.io
        version: v1.33.1
      name: context_info
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
```
7. Test Connection
```bash
kubectl get nodes

Result:
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   26h   v1.30.0
```
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
kubectl apply -f weaviate
```
### Install Dify API
```bash
kubectl apply -f dify-api
```
### Install Dify Worker
```bash
kubectl apply -f dify-worker
```
### Install Dify Web
```bash
kubectl apply -f dify-web
```
### Install Dify Sandbox
```bash
kubectl apply -f dify-sandbox
```
### Install NGINX
```bash
kubectl apply -f nginx
```
### Install SSRF Proxy
```bash
kubectl apply -f ssrf-proxy
```
### Setup CI/CD Pipeline Jenkins
1. Add Secret for push image to registry and deploy to kubernetes, for the credentials id use `container_registry` and `minikube`, and the value is your username and password for id `container_registry` and for id `minikube` is upload file config `.kube/config` to jenkins.
2. Create Jenkins Pipeline for Dify App. Select Multibranch Pipeline and add your repository. For the Jenkinsfile you can use this code on this [repository](https://raw.githubusercontent.com/mrofisr/dify/main/Jenkinsfile)
3. Create Jenkins Pipeline for Dify Sandbox. Select Multibranch Pipeline and add your repository. For the Jenkinsfile you can use this code on this [repository](https://raw.githubusercontent.com/mrofisr/dify-sandbox/main/Jenkinsfile)
4. Run the both pipeline
