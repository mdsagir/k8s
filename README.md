# k8s

Its application orchestrator\
deployment and managing the container\
Adv\
Autohealing\
Rollback

## Componenet of kubernetes
 ### Controll plane (Master Node)
 1. **API Server** : that expose the rest API and all communication happend through API server, Its checks authentication and authorization, also command line request apply through kubectl.
 2. **etcd** : Its distributed data base store entire the cluster configuration, its also store user created object manifesto file.
 3. **Schedular** : Its create POD assigned to the node, its checkes health state, resources, port
 4. **Controller Manager** : Its deamon that manage the all controller basically its constroller of controllers, Its checks desire state of pod.
 ### Worker Node  
 1. **Kubelet** : Agent that run every Node, responsible for running the container inside the POD.
 2. **Kube Proxy**: Agent that also run every Node, that manage the local cluster networking 
 3. **Container Runtime** : Responsible for runtime container
 ## Minikube
 + Get node info\
 `kubectl get nodes`
 + Node inside\
 `minikube ssh`
 + Node logs\
  `minikube logs -f`
  
 
## POD
A smallest deployment unit is POD, have 1 or more container, its share network volume.
+ create pod\
`kubectl run <pod-name> --image=<image-name> --port <port-number>`\
`kubectl run mypod --image=nginx --port=80` 
+ Port forwarding\
 `kubectl port-forward pod/mypod 8080:80`\
 and go to on browser http://localhost:8080/
+ Describe pod\
`kubectl describe pod mypod`
+ Enter into pod container\
`kubectl exec -it mypod --bash`
+ nginx html file location\
`/usr/share/nginx/html`
+ Get pod info in yml format\
`kubectl get pod first -o yaml`
+ Create pod through yml        
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
+ command apply in yml pod\
`kubectl create -f mypod.yml`\
`kubectl delete -f mypod.yml`
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

