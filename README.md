# Deploy Prometheus in LKE

### Get Prometheus Helm Chart

**Add chart repository locally (Setup Once)**
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

**Update chart**
helm repo update

**Download a chart specified version from helm repository and unpacks it in local directory**
helm pull prometheus-community/kube-prometheus-stack --version 32.2.1 --untar

## OPTION-1

### Use `helm upgrade --install` and pass the values file.

`helm upgrade --install prometheus prometheus-community/kube-prometheus-stack -n prometheus `

### Uninstall using `helm uninstal`

`helm uninstall prometheus -n prometheus`

## OPTION-2

### Update your helm values and deploy using `kubectl apply`

Reference the helm values `prometheus-test-helm-values.yaml` for all the required changes for Test.

Reference `prometheus-prod-helm-values.yaml` for Prod.

### Render chart locally and validate add `--namespace` flag to generate the chart for particular namespace

`helm template prometheus --namespace prometheus --values prometheus-test-helm-values.yaml --output-dir ./manifests/test/ prometheus --repo https://prometheus-community.github.io/helm-charts`

### Apply all the rendered yaml files using `kubectl apply`

_Test_
`k apply -Rf manifests/test/prometheus -n prometheus`

_Prod_
`k apply -Rf manifest/prod/prometheus -n prometheus`

### Uninstall \ Delete all the rendered yaml files

_Test_
`k delete -Rf manifests/test/prometheus -n prometheus`

_Prod_
`k delete -Rf manifests/prod/prometheus -n prometheus`

_POST INSTALL CONFIGURATION_

### Create Grafana Ingress

Deploy Grafana ingress

_Test_
`k apply -f ingress-test/grafana-ingress.yaml`

_Prod_
`k apply -f ingress-prod/grafana-ingress.yaml`

### Get Grafana credentials

Get the secrets from the prometheus namespace

`kubectl get secrets -n prometheus`

Decode from base64 grafana secrets identified from previous cmd

`k get secret -n prometheus prometheus-grafana -o json | jq '.data | map_values(@base64d)'`

Use decoded password to login

### Create Alert-Manager Ingess

Deploy AlertManager ingress

_Test_
`k apply -f ingress-test/alert-manager-ingress.yaml`

_Prod_
`k apply -f ingress-prod/alert-manager-ingress.yaml`
