# Keycloak Setup

## Prerequisites
#### Start minikube / add helm package
```bash
minikube start
```
```bash
minikube addons enable ingress
```
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```
```bash
# when using minikube ingress addon ingress-nginx is already installed
kubectl create namespace ingress-nginx 
```
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```
```bash
helm install my-ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx
```
Erroring out? try 'kubectl delete ingressclass nginx' Before applying the above command again.
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

### Issues? Start again.
```bash
minikube stop
```
```bash
minikube delete
```

# Minimum Configuration complete !

## Checkout Keycloak is now available at [https://auth.localhost.](https://auth.localhost)