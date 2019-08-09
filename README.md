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
## Add a new tool
First create a new deployment editing [tools_deployments.yaml]() :
```bash
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: <name> # Tool name
spec:
  replicas: 2
  template:
    metadata:
      labels:
        app: <app> # App name
    spec:
      containers:
      - name: <name>
        image: <image> # Tool image available on docker hub
        env:
        ports:
        - containerPort: <port> # port exposed in Dockerfile
```
Then create a new service editing [tools_services.yaml]() :
```bash
apiVersion: v1
kind: Service
metadata:
  name: <name> # Service name
spec:
  ports:
  - port: <port> # port number which makes a service visible to other services running within the same cluster
    protocol: TCP
    targetPort: <targetPort> # port on the POD where the service is running.
  selector:
    app: contamehistorias
 ```
Now just add a new ingress rule editing [app_ingress.yaml]() :
```bash
      - path: /<name>(/|$)(.*) # Your tool name. Here Nginx is using regex to rewrite sub-path after /<name>
        backend:
          serviceName: <service_name>
          servicePort: <port> # similar as port in tools_services.yaml
 ```
## Test
To access to swagger documentation request [http://<external_ip>/<tools_name>/apidocs/]().

Request examples :
```
curl -X POST -L -v "http://<external_ip>/contamehistorias/conta" -H  
"accept: application/json" -H  "Content-Type: 
application/json" -d "{  \"domains\": [    
\"http://publico.pt/\",    \"http://www.rtp.pt/\",    
\"http://www.dn.pt/\",    \"http://news.google.pt/\"  ],  \"end_date\": 
\"2018-07-21 17:32:28\",  \"query\": \"Dilma 
Roussef\",  \"start_date\": \"2016-07-21 
17:32:28\"}"

curl -X POST "http://<external_ip>/pampo/pampo" 
-H  "accept: application/json" 
-H  "Content-Type: application/json" 
-d "{  \"text\": \"A aldeia piscatória de Alvor está situada no estuário do Rio Alvor e apesar da evolução 
constante do turismo no Algarve, mantém a sua arquitetura baixa e encanto da cidade velha, com 
ruas estreitas de paralelepípedos que nos levam até à Ria de Alvor, uma das belezas naturais mais 
impressionantes de Portugal. Há muitos hotéis em Alvor por onde escolher e adequar às exigências das suas férias, 
quanto a gosto e orçamento, bem como uma série de alojamento autossuficiente para aqueles que preferem ter um pouco 
mais de liberdade durante a sua estadia na Região de Portimão. Há muito para fazer e descobrir em Alvor, 
quer seja passar os seus dias descobrindo a rede de ruas desta encantadora vila de pescadores, explorar as lojas, 
ir para a praia para se divertir entre brincadeiras na areia e mergulhos no mar, ou descobrir 
a flora e fauna da área classificada da Ria de Alvor. O charme de Alvor não se esgota na Vila. 
Ficar hospedado em Alvor vai proporcionar-lhe momento mágicos entre paisagens de colinas, 
lagoas rasas e vistas panorâmicas sobre o Oceano Atlântico. Terá oportunidade de praticar o seu swing num dos campos de 
golfe de classe mundial e explorar as principais atrações históricas e alguns dos segredos mais bem escondidos 
do Algarve, nas proximidades, em Portimão e Mexilhoeira Grande. Consulte a lista dos nossos
 parceiros e escolha o hotel em Alvor, onde ficar durante as suas férias no Algarve.\"}"

```
