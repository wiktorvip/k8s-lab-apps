# argocd-k8s-lab

A demo repository showing how application workloads can be managed by ArgoCD on a Kubernetes cluster

# Software

Install [arkade](https://arkade.dev) and then use it to setup dependent tools

```
ark get kubectl kubectx kubens helm k3d argocd devspace yq jq
```

# Quick start

Create a k8s cluster

```
k3d cluster create cluster-01 --agents=2
```

Core install of ArgoCD

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
```

Login to ArgoCD

```
kubectl config set-context --current --namespace=argocd
argocd login --core
```

Start a local UI proxy

```
argocd admin dashboard
```

## Deploy workloads

Deploy "dev" workloads

```
argocd app create bootstrap-dev \
   --repo https://github.com/wiktorvip/k8s-lab-apps.git \
   --path argocd/dev \
   --dest-namespace argocd \
   --dest-server https://kubernetes.default.svc \
   --sync-policy automated
```

Deploy "test" workloads

```
argocd app create bootstrap-test \
   --repo https://github.com/wiktorvip/k8s-lab-apps.git \
   --path argocd/test \
   --dest-namespace argocd \
   --dest-server https://kubernetes.default.svc \
   --sync-policy automated
```

Deploy "prod" workloads

```
argocd app create bootstrap-prod \
   --repo https://github.com/wiktorvip/k8s-lab-apps.git \
   --path argocd/prod \
   --dest-namespace argocd \
   --dest-server https://kubernetes.default.svc \
   --sync-policy automated
```

# Configuration

## Application configuration

Applications are deployed using Helm. Each application has a "dev", "test" and "prod" sub-directory corresponding to the environments used in the application release promotion process.

```
apps
├── app-webcolor
    ├── dev
    │   ├── Chart.yaml
    │   └── values.yaml
    ├── prod
    │   ├── Chart.yaml
    │   └── values.yaml
    └── test
        ├── Chart.yaml
        └── values.yaml
```
