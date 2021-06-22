# Gitops - ArgoCD + Istio

## Prerequisites

* argocd
* kubectl
* helm
* istioctl

### Create the namespaces

`kubectl apply -f namespaces/`

## Create a Chart.lock file

```bash
helm repo add argo-cd https://argoproj.github.io/argo-helm
helm dep update charts/argo-cd/
```

## Bootstrap ArgoCD

Later this will manage itself.

`helm install -n argocd argo-cd charts/argo-cd/`

Ignore the warnings, this will be fixed in a future version of helm.

## Access the UI

`kubectl port-forward -n argocd svc/argo-cd-argocd-server 3333:443`

## Logging in

Username is `admin`

`kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2`

## Getting ArgoCD to self-manage

`helm template apps/ | kubectl apply -f -`

Once argocd is running you can delete the secret created by helm

`kubectl delete secret -l owner=helm,name=argo-cd -n argocd`
