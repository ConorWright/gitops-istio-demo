# Gitops - ArgoCD + Istio

## Prerequisites

* argocd
* kubectl
* helm
* istioctl

### Setting up minikube

`minikube start --memory=8192mb --cpus=4`

### Installing istio

```bash
minikube addons enable istio-provisioner
minikube addons enable istio
```

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

`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

Password can be changed with: `argocd account update-password`

## Getting ArgoCD to self-manage

`helm template apps/ | kubectl apply -f -`

Once argocd is running you can delete the secret created by helm

`kubectl delete secret -l owner=helm,name=argo-cd -n argocd`

## Get the istio endpoint

`kubectl get svc -n istio-system istio-ingressgateway`

## Adding more clusters

```bash
argocd cluster add
argocd cluster list
```
