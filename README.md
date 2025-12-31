```bash
k3d cluster create otel --servers 1 --agents 1 -p "16686:16686@loadbalancer" -p "4317:4317@loadbalancer" -p "4318:4318@loadbalancer"

helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

kubectl create namespace argocd

helm upgrade --install argo-cd argo/argo-cd -n argocd --set global.image.tag=v3.2.3

kubectl -n argocd port-forward svc/argo-cd-argocd-server 8080:80

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d


## Use Argo CD to manage itself (dev/qa/prod)

After the initial Helm install, hand control over to Argo CD by syncing the self-managing `ApplicationSet`:

```bash
kubectl apply -n argocd -f argo-otel/applicationset.yaml
```

This creates three Applications (dev/qa/prod) from the official `argo-cd` chart (tracked via the Helm repo) using values in:

- shared: `argo-otel/argocd/values.yaml`
- dev: `argo-otel/argocd/values-dev.yaml` (local k3d, insecure server)
- qa: `argo-otel/argocd/values-qa.yaml`
- prod: `argo-otel/argocd/values-prod.yaml`

Register your qa/prod clusters in Argo CD with the cluster names `qa` and `prod` so the ApplicationSet can target them (dev uses the built-in `in-cluster` target).
