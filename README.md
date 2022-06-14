

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
kubectl apply -f provider-default-aws.yaml

```

GCP 

```sh
kubectl --namespace crossplane-system \
    create secret generic gcp-creds \
    --from-file creds=./gcp-creds.json
```

or if you created a package
```sh
kubectl crossplane install configuration [link to OCI image]

```


```
### After Package has been built
Either

```sh
kubectl crossplane install provider \
    crossplane/provider-gcp:v0.21.0
kubectl crossplane install provider \
    crossplane/provider-aws:v0.19.0
kubectl apply -f package/composition-aws.yaml
kubectl apply -f package/composition-gcp.yaml
kubectl apply -f package/xrd.yaml 

```
#### Install Provider configurations
```sh
kubectl apply -f provider-default-aws.yaml
kubectl create ns a-team
kubectl apply -f cluster-aws.yaml
```

#### Install GCP Config
```sh
kubectl crossplane install provider \
    crossplane/provider-gcp:v0.21.0
kubectl apply -f provider-default-gcp.yaml
kubectl create ns b-team
kubectl apply -f cluster-gcp.yaml
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