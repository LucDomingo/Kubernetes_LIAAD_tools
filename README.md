# Kubernetes deployment for LIAAD tools
It's a simple  example of kubernetes deployment using Kubernetes Engine from Google Cloud Platform.
## Installation
First create tools deployments and tools services. In this example we are using two tools :
```bash
kubectl create -f tools_deployments.yaml
kubectl create -f tools_services.yaml
```
Then follow instructions from this [link](https://github.com/kubernetes/ingress-nginx/blob/master/docs/deploy/index.md) and create a Nginx Ingress Controller :
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/cloud-generic.yaml
```
Now just create ingress rules for Nginx Controller :
```bash
kubectl apply -f app-ingress.yaml
```
## Usage
Test the tools requesting load-balancer external ip:
```
curl -X POST -L -v "http://<external_ip>/contamehistorias/conta" -H  
"accept: application/json" -H  "Content-Type: 
application/json" -d "{  \"domains\": [    
\"http://publico.pt/\",    \"http://www.rtp.pt/\",    
\"http://www.dn.pt/\",    \"http://news.google.pt/\"  ],  \"end_date\": 
\"2018-07-21 17:32:28\",  \"query\": \"Dilma 
Roussef\",  \"start_date\": \"2016-07-21 
17:32:28\"}"
```
