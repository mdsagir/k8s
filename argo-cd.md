### Install argo cd

website to create and install\
https://argo-cd.readthedocs.io/en/stable/getting_started/ 

`kubectl create namespace argocd`\
`kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml` \

YML file link :\
https://github.com/mdsagir/argo-cd

Run the argocd browser\
`kubectl get svc -n argocd`\
`kubectl port-forward -n argocd svc/argocd-server 8080:443`

Get the password\
`kubectl get secrete argocd-initial-admin-secret -n argocd -o yaml`\
Convert base64 to text\
`echo <base64Password> | base64`
