```bash
# 1. Cluster already exists
kubectl create namespace argocd

# 2. Apply secret with metatdata ArgoCD will pick up on
kubectl apply -f secrets/argocd-git-ssh.yaml

# 3. install argo CD
kubectl apply --server-side -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 3. Hand control to GitOps
kubectl apply -f platfrom/argocd/bootstrap/root.yaml
```


```
source .envrc

spos --encrypt <secret>.yaml > <secret>.enc.yaml
```
