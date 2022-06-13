

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