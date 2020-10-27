## Create Kubernetes Stack

### Build Docker image
```
go to ./app/rsvpapp
docker build -t rsvp-image .
```

### create DB
```
kubectl create -f rsvp-db.yaml
kubectl create -f rsvp-db-service.yaml
kubectl get deployments
kubectl get services
```

### Create FrontEnd
```
eval $(minikube docker-env)
minikube addons enable registry
minikube addons enable metrics-server
minikube addons enable dashboard

kubectl create -f rsvp-web.yaml
kubectl create -f rsvp-web-service.yaml
kubectl get deployments
kubectl get services

kubectl expose deployment rsvp --type=LoadBalancer --name=rsvp-nlb

minikube service rsvp-nlb 
```

### Scale
```
kubectl scale deployment rsvp --replicas=3
kubectl get deployments
```


## Update Image and use deployment

### Create NGNIX deployment
```
kubectl create -f  https://k8s.io/examples/controllers/nginx-deployment.yaml
```

### Update Docker image
```
kubectl set image deployment/nginx-deployment nginx=nginx:1.9.1
kubectl describe deployments
```

### Roll Back
```
kubectl rollout status deployments nginx-deployment
kubectl rollout history deployment/nginx-deployment
```

## Install Prometheus/Grafana
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install my-prometheus prometheus-community/prometheus --version 11.16.5
export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093

helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-grafana bitnami/grafana --version 3.4.5 
kubectl port-forward svc/my-grafana 8080:3000

```