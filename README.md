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
A smallest deployment unit is POD, have 1 or more container, its share network volume, its not autohealing
+ create pod\
`kubectl run <pod-name> --image=<image-name> --port <port-number>`\
`kubectl run mypod --image=nginx --port=80` 
+ Port forwarding\
 `kubectl port-forward pod/mypod 8080:80`\
 and go to on browser http://localhost:8080/
+ Get the pod\
`kubectl get pod mypod`
+ Get all the pod including cluster\
 `kubectl get pod -A`
+ Get all the running object including cluster\
 `kubectl get all -A` 
+ Describe pod\
`kubectl describe pod mypod`
+ Get log info\
 `kubectl logs mypod`
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
    resources:
      limits:  
        memory: "128Mi"
        cpu: "500m"     
    ports:
      - containerPort: 80 
```
+ Command apply in yml pod\
`kubectl apply -f mypod.yml`\
`kubectl delete -f mypod.yml`
+ Filter resource through labels\
`kubectl get pod -l app=demo`
+ Show all labels\
`kubectl get all --show-labels`
+ Assigned resource for very pod

+ Namespace
+ check default namespace\
`kubectl config view`
+ show list of namespace\
`kubectl get ns`
+ create namespace\
`kubectl create ns mynamespace`
+ create pod assigned with namespace\
`kubectl apply -f mypod.yml -n=mynamespace`
+ get pod from mynamespace\
`kubectl get pod -n=mynamespace`

## Deployment
Mange the pod, zerodowntime deployment and create replicaset, autohealing
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
+ Deploy\
`kubectl apply -f mydeployment`
+ Delete the 1 of the pod and see replicaset will create a pod instead\
+ To get deployment history\
 `kubectl rollout history deployment mydeployment`
+ To get rollout previous deployment\
 `kubectl rollout undo deployment mydeployment`
+ To get deployment history details by rivision\
 `kubectl rollout history deployment mydeployment --revision=3`
## Deployment strategy
### Recrete 
Deleting all the pod then create latest deployment, Its hells downtime of application
### Rolling Update 
Create new deployment through pod if get suucess then oldest pod will delete, Incase newly deployment not gets
 sucess then oldest deployment not goes to deleted
### Revision History Limit 
`revisionHistoryLimit` The number of old ReplicaSets to retain to allow rollback. This is a pointer to distinguish between explicit zero and not specified. Defaults to 10.
### Maximimum Unavailable
`maxUnavailable` The maximum number of pods that can be unavailable during the update
### Maximum Surge
`maxSurge` The maximum number of pods that can be scheduled above the desired number of pods
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mydeployment
  labels:
    app: demo
  annotations:
    kubernetes.io/changes-cause: "nginx"  
spec:
  replicas: 3
  revisionHistoryLimit: 20
  strategy:
    type: RolllingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
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

