

#### Build Package
```sh
kubectl crossplane build configuration
```

#### Push to Docker Registry
```sh
VERSION=v0.1
kubectl crossplane push configuration us-east1-docker.pkg.dev/anthos-multi-cloud-335819/anthos-crossplane-k8s/package:c
```


#### Install configuration
```sh
kubectl crossplane install configuration us-east1-docker.pkg.dev/anthos-multi-cloud-335819/anthos-crossplane-k8s/package:${VERSION}
```