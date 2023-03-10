

#### Troubleshooting
```sh
kubectl get events -n default --sort-by={'lastTimestamp'}
```

### Install Crossplane
```sh
kubectl create namespace a-team

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
#### Apply the Providers 
```sh
kubectl apply -f providers/gcp.yaml
```
Apply the provider Configs 
```sh
kubectl apply -f providers/gcp-providerconfig.yaml
```

####Apply the Compositions

GKE on GCP
```sh
kubectl apply -f compositions/gcp-gke.yaml
kubectl apply -f compositions/gcp--gke-nodepool.yaml
```

```sh
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
#### Install Azure

```sh

az ad sp create-for-rbac \
    --sdk-auth \
    --role Owner \
    | tee azure-creds.json

export AZURE_CLIENT_ID=$(\
    cat azure-creds.json \
    | grep clientId \
    | cut -c 16-51)

export RW_ALL_APPS=1cda74f2-2616-4834-b122-5cb1b07f8a59

export RW_DIR_DATA=78c8a3c8-a07e-4b9e-af1b-b5ccab50a175

export AAD_GRAPH_API=00000002-0000-0000-c000-000000000000

az ad app permission add \
    --id "${AZURE_CLIENT_ID}" \
    --api ${AAD_GRAPH_API} \
    --api-permissions \
    ${RW_ALL_APPS}=Role \
    ${RW_DIR_DATA}=Role

az ad app permission grant \
    --id "${AZURE_CLIENT_ID}" \
    --api ${AAD_GRAPH_API} \
    --expires never

az ad app permission admin-consent \
    --id "${AZURE_CLIENT_ID}"
kubectl crossplane install provider \
    crossplane/provider-azure:v0.16.1


kubectl --namespace crossplane-system \
    create secret generic azure-creds \
    --from-file creds=./azure-creds.json

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