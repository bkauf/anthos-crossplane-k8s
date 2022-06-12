

#### Build Image
```sh
kubectl crossplane build configuration
```

#### Push to Docker Registry
```sh
REG=gcr.io/
kubectl crossplane push configuration ${REG}/anthos-crossplane:v0.1
```