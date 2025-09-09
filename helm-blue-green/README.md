# Argo Rollouts and Helm

Argo Rollouts will respond to changes in Rollout resources
regardless of the event source. If you package your manifest
with the Helm package manager you can perform Progressive Delivery deployments with Helm

1. Install the Argo Rollouts controller in your cluster: https://github.com/argoproj/argo-rollouts#installation
2. Install the `helm` executable locally: https://helm.sh/docs/intro/install/

## Deploying the initial version

To deploy the first version of your application:

```
git clone https://github.com/argoproj/argo-rollouts.git
cd argo-rollouts/examples
helm install example ./helm-blue-green/
```

Your application will be deployed and exposed via the `example-helm-nginx` service

## Perform the second deployment

To deploy the updated version using a Blue/Green strategy:

```
helm upgrade example ./helm-blue-green/  --set image.tag=1.29.1
```

Updating default index.html:

```
kubectl exec <pod name> -it -- /bin/bash 
echo "<h1>preview</h1>" > /usr/share/nginx/html/index.html
```

Now, two versions will exist in your cluster (and each one has an associated service)

```
kubectl-argo-rollouts get rollout example-helm-nginx
```

## Promoting the rollout

To advance the rollout and make the new version stable

```
kubectl-argo-rollouts promote example-helm-nginx
```

This promotes container image `nginx:1.29` to `green` status and `Rollout` deletes old replica which runs `nginx:1.29.1`.

 -------------------------------------------------------
via argocd:

kubectl config set-context --current --namespace=argocd

argocd app set example -p image.tag=1.29.1

port-forward to argocd:

kubectl port-forward svc/argocd-server -n argocd 8080:443
