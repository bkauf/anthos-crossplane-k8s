#### Troubleshooting
```sh
kubectl get events -n <namespace> --sort-by={'lastTimestamp'}
```

### Install Crossplane
```sh
kubectl create namespace team-a

helm repo add crossplane-stable \
    https://charts.crossplane.io/stable

helm repo update

helm upgrade --install \
    crossplane crossplane-stable/crossplane \
    --namespace crossplane-system \
    --create-namespace \
    --wait
```

#### Create Secrets
AWS
```sh
kubectl --namespace crossplane-system \
    create secret generic aws-creds \
    --from-file creds=./aws-creds.conf
```
### After Package has been built
#### Apply configuration
```sh
kubectl apply -f crossplane.yaml
kubectl apply -f provider-default-aws.yaml
```



#### Install configuration
```sh
kubectl create ns a-team
kubectl apply -f cluster.yaml
```