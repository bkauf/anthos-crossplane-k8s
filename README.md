

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

export AWS_ACCESS_KEY_ID=[...]
export AWS_SECRET_ACCESS_KEY=[...]

echo "[default]
aws_access_key_id = $AWS_ACCESS_KEY_ID
aws_secret_access_key = $AWS_SECRET_ACCESS_KEY
" | tee aws-creds.conf

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

#### Access Infrastructure
```sh
kubectl --namespace team-a \
    get secret cluster \
    --output jsonpath="{.data.kubeconfig}" \
    | base64 -d \
    | tee kubeconfig.yaml

export KUBECONFIG=$PWD/kubeconfig.yaml

kubectl get nodes

kubectl get namespaces
unset KUBECONFIG

```