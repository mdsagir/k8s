# k8s

Its application orchestrator\
deployment and managing the container\
Adv\
Autohealing\
Rollback
+ Master node (Controll plane)\
 etcd distributed data base in cluster\
 api server communicate through CLI or Browser\
 controll manager manage the pod desire state and actual state\
 shedular shedul pod and assigned to node
+ Worker node\
  kubelet agent per node that make sure every container runs inside the pod\
  kubeproxy per node that network proxy that assgined IP address for POD\
  container run time that pull the image
 
## POD
+ create pod\
`kubectl run <pod-name> --image=<image-name>`\
`kubectl run mypod --image=nginx`
+ describe pos\
`kubectl describe pod mypod`
+ enter into pod container\
`kubectl exec -it mypod --bash`
+ nginx html file location\
`/usr/share/nginx/html`
+ get pod info in yml format\
`kubectl get pod first -o yaml`
+ create pod through yml
```yml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx
```          
+ command apply in yml pod\
`kubectl create -f mypod.yml`\
`kubectl delete -f mypod.yml`
+ labels 
```yml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  labels:
    app: demo
spec:
  containers:
  - name: mypod
    image: nginx
    ports:
      - containerPort: 80
```
+ filter resource through labels\
`kubectl get pod -l app=demo`
+ show all labels\
`kubectl get all --show-labels`
+ assigned resource for very pod
```yml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  labels:
    app: demo
spec:
  containers:
  - name: mypod
    image: nginx
    ports:
      - containerPort: 80
    resources:
      limits:  
        memory: "128Mi"
        cpu: "500m" 
```
+ namespace
+ check default namespace\
`kubectl config view`
+ show list of namespace\
`kubectl get ns`
+ create namespace\
`kubectl create ns mynamespace`
+ create pod assigned with namespace\
`kubectl create -f mypod.yml -n=mynamespace`
+ get pod from mynamespace\
`kubectl get pod -n=mynamespace`

## Deployment
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mydeployment
  labels:
    app: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name: mydeployment
          image: nginx
          ports:
            - containerPort: 80
          resources:
            limits:
              memory: "128Mi"
              cpu: "500m"
```
## Service
### ClusterIP
```yml
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  type: ClusterIP
  selector:
    app: demo
  ports:
    - port: 80
      targetPort: 80 
```
+ port-forward\
`kubectl port-forward <svc/service-name> outerport:innerport`\
`kubectl port-forward svc/myservice 8080:80`
### NodePort
```yml
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  type: NodePort
  selector:
    app: demo
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30012  
```
+ access through port\
`kubectl get service`\
`CLUSTER-IP:nodePort`\
or\
`minikube service <service-name>`\
Its redirect to the browser

