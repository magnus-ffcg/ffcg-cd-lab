# ffcg-cd-lab

Test of two different deployment strategies

# Requirements 

* You will need docker running with memory set above (4192mb, see settings if it complains)

## 1. Installation of minikube & helm

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
```
```bash
sudo install minikube-darwin-amd64 /usr/local/bin/minikube
```
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

## 2. Start & verify

Check that everything is working

```bash
minikube start --kubernetes-version=v1.23.3 \
--memory 4192 --cpus 2
```
```bash
minikube kubectl -- get po -A
```

## 3. Install prometheus & grafana (monitoring)

Install tools inside the cluster with helm


```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus \
    prometheus-community/prometheus \
    --namespace=monitoring  \
    --version=15.13.0
```

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana \
    --namespace=monitoring \
    --version=6.39.0 \
    --set=adminUser=admin \
    --set=adminPassword=admin \
    --set=service.type=NodePort
```

Access grafana:

    minikube service grafana

Login with admin / admin

Add datasource under settings (bottom left):

    Name: prometheus
    Type: Prometheus
    Url: http://prometheus-server
    Access: Server

Create a new dashboard (top left) with a graph 
with this query:

    sum(rate(http_requests_total{app="my-app"}[5m])) by (version)

Save it and close the tunnel (ctrl-c in terminal)

5. Prep later deployments

Open a new terminal window, write 

    docker pull containersol/k8s-deployment-strategies

Deploy the ingress-nginx controller:

    helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
    helm repo update
    helm install ingress-nginx ingress-nginx/ingress-nginx

Expose the ingress-nginx:

    kubectl expose deployment \
    ingress-nginx-controller \
    --port 80 \
    --type LoadBalancer \
    --name ingress-nginx

Wait for nginx to be running:

     kubectl rollout status deploy ingress-nginx-controller -w

When it show "successfully rolled out" its ok.

6. Open up deployment1/README.md and go through it.

7. Open up deployment2/README.md and go through it.

8. Time to check the dashboard again:

    minikube service grafana












