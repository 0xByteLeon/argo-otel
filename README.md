```bash
k3d cluster create otel --servers 1 --agents 1 -p "16686:16686@loadbalancer" -p "4317:4317@loadbalancer" -p "4318:4318@loadbalancer"

helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

kubectl create namespace argocd

helm upgrade --install argo-cd argo/argo-cd -n argocd --set global.image.tag=v3.2.3

kubectl -n argocd port-forward svc/argo-cd-argocd-server 8080:80

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d



```