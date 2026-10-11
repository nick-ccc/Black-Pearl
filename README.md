<h1 align="center">
    Black Pearl - GitOps tracked homelab deployment
</h1>

<p align="center">
  <a href="https://github.com/nick-ccc/Black-Pearl">
    <img src="./assets/black-pearl.gif" alt="Black Pearl" width="200" />
  </a>
</p>

***

### Bootstrap

Bootstrap Argo CD on an existing Kubernetes cluster and establish GitOps as the source of truth for application deployments. This process installs Argo CD, configures Git repository access, and applies the root Application to begin managing cluster resources from the repository.

```bash
# Create namespace and configure Git access
kubectl create namespace argocd
kubectl apply -f secrets/argocd-git-ssh.yaml

# Install Argo CD
kubectl apply --server-side -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Apply root Application to hand over to GitOps
kubectl apply -f platform/argocd/bootstrap/root.yaml

# Configure repository server plugins
kubectl patch deployment -n argocd argocd-repo-server \
  --patch-file platform/argocd/plugins/repo-server-patch.yaml

# Restart components to load updated configuration
kubectl rollout restart deployment -n argocd \
  argocd-repo-server argocd-server
kubectl rollout restart sts -n argocd
```

After bootstrap, Argo CD reconciles the root Application and its child Applications against the desired state declared in Git. Manage ongoing configuration and application changes through the repository, using direct `kubectl` operations primarily for initial setup and troubleshooting.

### Secret Encryption with SOPS


> [!NOTE] KSOPS is installed and configured during the Argo CD bootstrap process, allowing Argo CD to decrypt and deploy SOPS-encrypted secrets during GitOps reconciliation. Commit only encrypted secrets to Git; keep plaintext secrets and encryption keys out of version control.


Use SOPS to encrypt Kubernetes secrets before committing them to Git. Load the repository environment and encrypt plaintext YAML files:

```bash
source .envrc
sops --encrypt <secret>.yaml > <secret>.enc.yaml
```
