# Argo and Helm

via argocd:

kubectl config set-context --current --namespace=argocd

kubectl apply -f example-cronjob-manifest.yaml 

port-forward to argocd:

kubectl port-forward svc/argocd-server -n argocd 8080:443
