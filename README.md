# Flux POC

## Create dev and prod clusters
1. Create kind config file
```yaml
cat > kind-config.yaml <<EOF
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    image: kindest/node:v1.25.3
EOF
```
2. Create dev cluster
```sh
kind create cluster --config kind-config.yaml --name dev
```
3. Create prod cluster
```sh
kind create cluster --config kind-config.yaml --name prod
```

## Install Flux CLI
```sh
curl -s https://fluxcd.io/install.sh | sudo bash
```

## Install flux on dev and prod clusters
1. Export GitHub token
```sh
export GITHUB_TOKEN=<token>
```

2. Run the bootstrap command for dev cluster
```sh
flux bootstrap --context=kind-dev \
 github \
  --owner=cod-r \
  --repository=fluxcd-poc \
  --path=clusters/dev \
  --personal
```

3. Run the bootstrap command for prod cluster
```sh
flux bootstrap --context=kind-prod \
 github \
  --owner=cod-r \
  --repository=fluxcd-poc \
  --path=clusters/prod \
  --personal
```
This will create directories `clusters/dev` and `clusters/prod` in https://github.com/cod-r/fluxcd-poc git repository containing the Flux bootstrap manifests.

4. Create kustomization to deploy everything in `apps` directory
```yaml
cat > clusters/dev/infrastructure.yaml <<EOF
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: apps
  namespace: flux-system
spec:
  interval: 1m0s
  sourceRef:
    kind: GitRepository
    name: flux-system
  path: ./infrastructure/overlays/dev
  prune: true
EOF
```
5. Repeat for prod cluster

## Access Grafana
```sh
kubectl port-forward -n kube-prometheus-stack svc/kube-prometheus-stack-grafana 3000:80
```
Open http://localhost:3000

Username: admin  
Password: prom-operator