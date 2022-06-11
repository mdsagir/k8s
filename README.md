# k8s

## POD
+ create pod\
`kubectl run <pod-name> --image=<image-name>`\
`kubectl run firstpod --image=nginx`
+ describe pos\
`kubectl describe pod firstpod`
+ enter into pod container\
`kubectl exec -it firstpod --bash`
+ nginx html file location\
`/usr/share/nginx/html`
+ get pod info in yml format\
`kubectl get pod first -o yaml`
+ create pod through yml
```yml
apiVersion: v1
kind: Pod
metadata: 
    name: firstpod
spec:
    container:
        - name: server
          image: nginx
