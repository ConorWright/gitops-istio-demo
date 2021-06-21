# Gitops - ArgoCD + Istio

## Prerequisites

* argocd
* kubectl
* helm
* istioctl

## Create a Chart.lock file

```bash
helm repo add argo-cd https://argoproj.github.io/argo-helm
helm dep update infra/charts/argo-cd/
```

## Bootstrap ArgoCD

Later this will manage itself

`helm install argo-cd infra/charts/argo-cd/`

Ignore the warnings, this will be fixed in a future version of helm.

## Access the UI

`kubectl port-forward svc/argo-cd-argocd-server 3333:443`

## Logging in

Username is `admin`

`kubectl get pods -l app.kubernetes.io/name=argocd-server -o name | cut -d'/' -f 2`

## Getting ArgoCD to self-manage

`helm template apps/argo-cd | kubectl apply -f -`
