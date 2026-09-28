```bash
# 1. Cluster already exists
# 2. Install Argo CD
kubectl create namespace argocd
kubectl apply --server-side -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 3. Hand control to GitOps
kubectl apply -f bootstrap/argocd/root.yaml
```
