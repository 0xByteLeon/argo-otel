```bash
k3d cluster create obs --image rancher/k3s:v1.31.5-k3s1 --servers 2 --agents 2 --k3s-arg '--disable=traefik@server:*' --k3s-arg '--disable=servicelb@server:*' --port '8080:80@loadbalancer' --port '8443:443@loadbalancer' --port '4317:4317@loadbalancer' --port '4318:4318@loadbalancer' --port '3000:3000@loadbalancer' --port '9090:9090@loadbalancer' --port '3100:3100@loadbalancer' --port '3200:3200@loadbalancer' --wait


helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

kubectl create namespace argocd

helm upgrade --install argocd argo/argo-cd -n argocd --set configs.params."server\.insecure"=true

kubectl -n argocd port-forward svc/argo-cd-argocd-server 8080:80

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d; echo

kubectl port-forward service/argocd-server -n argocd 8081:443

kubectl apply -f bootstrap/applicationsets/nginx-appset.yaml

kubectl -n otel-dev port-forward svc/prometheus-dev-server 19090:80

kubectl -n otel-dev port-forward svc/grafana-dev 19091:80

