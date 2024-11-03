# k8s-lab
Deploying a microservice app (based on weather api) as part of learning k8s

## Environment
```shell
kind version 0.24.0
kubernetes v1.31.0
containerd v1.7.18
```

## App Architecture
![app-architecture](diagrams/app-architecture.png)

## Deployment

### Create namespace for the app
```shell
kubectl create namespace weather-app
```
### Create secret for mysql auth database
A user named 'auth' will be created for interacting with weather-app database.
```shell
kubectl create secret generic mysql-secret \
  --from-literal=root-password='<root-pw>' \
  --from-literal=auth-password='<password-for-auth-user>' \
  --from-literal=secret-key='<secret-for-jwt>' \
  -n weather-app
```
### Generate self-signed certificates for ingress
```shell
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=weatherapp.local/O=weatherapp"
```
### Create a tls secret
```shell
kubectl create secret tls weatherapp-ui-tls --cert=tls.crt --key=tls.key
```
### Apply Manifests
```shell
kubectl apply -f k8s/auth
kubectl apply -f k8s/weather
kubectl apply -f k8s/ui
```