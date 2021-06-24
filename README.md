# Gitops - ArgoCD + Istio

## Prerequisites

* argocd
* kubectl
* helm
* istioctl
* jq
* awscli (optional)
* eksctl (optional)

### Setting up minikube

`minikube start --memory=8192mb --cpus=4`

### Setting up EKS

Set up a cluster in AWS using the console or IaaC.

`eksctl create cluster -f cluster/cluster.yaml`

Get your kubeconfig with:

`aws eks --region <region-code> update-kubeconfig --name <cluster_name>`

**NOTE:** If you use `eksctl` to make your cluster it will do it for you.

### Installing istio

```bash
istioctl install --set profile=demo -y

# In a seperate folder
git clone git@github.com:istio/istio.git

# This will install kiali, grafana prometheus etc
kubectl apply -f samples/addons
```

**NOTE:** If you get errors with kiali, run the kubectl apply again. This will be fixed in a later version of istio.

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

**NOTE:** Ignore the warnings, this will be fixed in a future version of helm.

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

## Getting the cluster endpoint

```bash
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')

export INGRESS_HOST=$(minikube ip)

# You can access the endpoint for this in the browzer
export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

# In a separate window
minikube tunnel
```

## Accessing the dashboards

```bash
# K8s dashboard
minikube dashboard

# Kiali
istioctl dashboard kiali
```

## Adding more clusters

```bash
argocd cluster add
argocd cluster list
```

## Cleaning up

```bash
# If using minikube
minikube delete

# If using EKS
eksctl delete cluster --name gitops-istio --region us-west-2
```
