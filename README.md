# Stord SRE Challenge

This repository contains the solution andHelm chart for the Stord SRE Technical Challenge.

Requirements are based off of @parkerd's [gist](https://gist.github.com/parkerd/57c0b11d8683474ac1cd168950211354)

## Installation Instructions
### Setup - set secret env vars

```bash
# Create the secret with your actual values
kubectl create secret generic sre-app-secrets \
  --from-literal=SECRET_KEY_BASE="GWks+00R99rp4iCigXEDAP7WRQXFiTImOP0vIUmFyaNj1DGDXsMvEfSXtb7ITMKC"

kubectl get secret sre-app-secrets
kubectl describe secret sre-app-secrets
kubectl get secret sre-app-secrets -o json | jq -r '.data | map_values(@base64d)'
```

### Installing the Helm Chart
```bash
NAMESPACE=stord

helm install sre-db oci://registry-1.docker.io/bitnamicharts/postgresql \
  --create-namespace \
  --namespace $NAMESPACE \
  --set auth.database=sre-technical-challenge \
  --set auth.postgresPassword=password

helm install sre-app ./sre-app \
  --debug \
  --namespace $NAMESPACE \
  --values ./sre-app/values.yaml
```

## Validation
### Database
```bash
kubectl port-forward svc/sre-db-postgresql 55432:5432
psql postgresql://postgres:password@127.0.0.1:55432/sre-technical-challenge
```
### App
```bash
kubectl port-forward svc/sre-app 8080:80 -n stord
http http://localhost:8080
```
## Resources:
- Kubernetes
  - [Managing secrets using kubectl](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-kubectl/)
  - [Pod security context](https://kubernetes.io/docs/concepts/security/pod-security-context/)
