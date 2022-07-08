

#### Build Package
```sh
kubectl crossplane build configuration
```

#### Push to Docker Registry
```sh
VERSION=v0.1
kubectl crossplane push configuration us-east1-docker.pkg.dev/anthos-multi-cloud-335819/anthos-crossplane-k8s/package:${VERSION}
```


#### Install configuration
```sh
kubectl crossplane install configuration us-east1-docker.pkg.dev/anthos-multi-cloud-335819/anthos-crossplane-k8s/package:${VERSION}
```

kubectl crossplane install provider \
    crossplane/provider-gcp:v0.21.0

  ### Upgrade provider
  ```sh
  kubectl get provider.pkg
  kubectl patch provider.pkg [crossplane-provider] -p '{"spec":{"package": "crossplane/provider-gcp:v0.21.0"}}' --type=merge
  ```
  

or upgrade configuration
```sh
kubectl crossplane update configuration anthos-aws-azure-gcp v0.9
```


# Find the name of your Provider object.
```sh
kubectl get provider.pkg
```
# Patch it with the new version. Make sure you use the latest patch release of
# v0.22.x
```
export PROVIDER_NAME=[provider name]
kubectl patch provider.pkg $PROVIDER_NAME -p '{"spec":{"package": "crossplane/provider-aws:v0.19.0"}}' --type=merge
```