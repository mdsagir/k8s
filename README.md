# k8s

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
