# Keycloak Setup

## Prerequisites
#### Start minikube / add helm package
```bash
minikube start \
minikube addons enable ingress \
helm repo add bitnami https://charts.bitnami.com/bitnami
```
```bash
# when using minikube ingress addon ingress-nginx is already installed
kubectl create namespace ingress-nginx \
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx \
helm install ingress-nginx -n ingress-nginx 
```
else
```bash
export KUBECONFIG=~/.kube/config
```
## Deploy the Keycloak cluster:

#### Create dedicated namespace for our deployments
```bash
kubectl create ns deployment
```

#### Create TLS cert
```bash
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout auth-tls.key -out auth-tls.crt -subj "/CN=auth.localtest.me/O=deployment"
```
```bash
kubectl create secret -n deployment tls auth-tls-secret --key auth-tls.key --cert auth-tls.crt
```
#### Deploy PostgreSQL cluster 
```bash
helm install -n deployment keycloak-db bitnami/postgresql-ha --set postgresql.replicaCount=1
```
#### Deploy Keycloak cluster
```bash
kubectl apply -n deployment -f keycloak.yaml
```
#### Create HTTPS ingress for Keycloak
```bash
kubectl apply -n deployment -f keycloak-ingress.yaml
```

## Start the minikube tunnel.

```bash
minikube tunnel
```