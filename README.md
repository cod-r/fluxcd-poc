# Flux POC

## Install Flux CLI
```sh
curl -s https://fluxcd.io/install.sh | sudo bash
```

## Start kind cluster
```sh
kind create cluster --config kind-config.yaml
```

## Install flux on kind cluster
1. Export GitHub token
```sh
export GITHUB_TOKEN=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
```

2. Run the bootstrap command
```sh
flux bootstrap --context=kind-kind
 github \
  --owner=cod-r \
  --repository=fluxcd-poc \
  --path=clusters/kind \
  --personal
```
This will create the directory `clusters/kind` containing the Flux bootstrap manifests in https://github.com/cod-r/fluxcd-poc git repository.

3. Create kustomization to deploy everything in `apps` directory
```yaml
cat > clusters/kind/apps.yaml <<EOF
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
  path: ./apps
  prune: true
EOF
```

## Install kube-prometheus-stack helm chart

1. Create HelmRepository
```yaml
cat > apps/kube-prometheus-stack/helm-repository.yaml <<EOF
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: HelmRepository
metadata:
  name: kube-prometheus-stack
  namespace: kube-prometheus-stack
spec:
  interval: 5m
  url: https://prometheus-community.github.io/helm-charts
EOF
```

2. Create HelmRelease
```yaml
cat > apps/kube-prometheus-stack/helm-release.yaml <<EOF
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: kube-prometheus-stack
  namespace: kube-prometheus-stack
spec:
  releaseName: kube-prometheus-stack
  chart:
    spec:
      chart: kube-prometheus-stack
      version: '40.3.1'
      sourceRef:
        kind: HelmRepository
        name: kube-prometheus-stack
  interval: 50m
  install:
    remediation:
      retries: 3
  values:
    prometheus:
      prometheusSpec:
      retention: 7d
EOF
```