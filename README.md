# Minimal GCP Template Stack

You can use this stack to spin up a private network as well as
resource classes that will let you provision resources in that
network.

# Installation

Requirements:
* Crossplane should be installed.
* [GCP Stack](https://github.com/crossplaneio/stack-gcp) should be installed and its version should be at least 0.6.0

If you have crossplane-cli installed, you can use the following command to install:

```bash
# Do not forget to change <version> with the correct version.
kubectl crossplane stack install --cluster -n crossplane-system 'crossplane/stack-minimal-gcp:<version>' minimal-gcp
```

If you don't have crossplane-cli installed, you need to create the following YAML to install:

```yaml
apiVersion: stacks.crossplane.io/v1alpha1
kind: ClusterStackInstall
metadata:
  name: "minimal-gcp"
  namespace: crossplane-system
spec:
  package: "crossplane/stack-minimal-gcp:<version>"
```

# Usage Instructions

You can create the following YAML to trigger creation of [`Network`][network], [`Subnetwork`][subnetwork], [`GlobalAddress`][global-address], [`Connection`][connection] resources as well as [`GKEClusterClass`][gkecluster-class], [`CloudSQLInstanceClass`][cloudsqlinstance-class], [`CloudMemorystoreInstanceClass`][cloudmemorystoreinstance-class] with minimal hardware requirements that can connect to those networking resources.

```yaml
apiVersion: gcp.resourcepacks.crossplane.io/v1alpha1
kind: MinimalGCP
metadata:
  name: test
spec:
  region: us-west2
  projectID: crossplane-playground
  credentialsSecretRef:
    name: gcp-account-creds
    namespace: crossplane-system
    key: credentials
```

In Crossplane, the resource classes that are annotated with `resourceclass.crossplane.io/is-default-class: "true"` are used as default if the claim doesn't specify a resource class selector. The resource classes you create via the `MinimalGCP` instance above will deploy all of its resource classes as default. If you'd like those defaulting annotations to be removed, you need to add the following to `MinimalGCP` instance above:

```yaml
templatestacks.crossplane.io/remove-defaulting-annotations: true
```

## Build

Run `make`

## Test Locally

### Minikube

Run `make` and then run the following command to copy the image into your minikube node's image registry:

```bash
# Do not forget to specify <version>
docker save "crossplane/stack-minimal-gcp:<version>" | (eval "$(minikube docker-env --shell bash)" && docker load)
```

After running this, you can use the [installation](#installation) command and the image loaded into minikube node will be picked up. 

[network]: https://github.com/crossplaneio/crossplane/blob/master/docs/api/crossplaneio/stack-gcp/compute-gcp-crossplane-io-v1beta1.md#network
[subnetwork]: https://github.com/crossplaneio/crossplane/blob/master/docs/api/crossplaneio/stack-gcp/compute-gcp-crossplane-io-v1beta1.md#subnetwork
[global-address]: https://github.com/crossplaneio/crossplane/blob/master/docs/api/crossplaneio/stack-gcp/compute-gcp-crossplane-io-v1beta1.md#globaladdress
[connection]: https://cloud.google.com/service-infrastructure/docs/service-networking/reference/rest/v1/services.connections
[gkecluster-class]: https://github.com/crossplaneio/crossplane/blob/master/docs/api/crossplaneio/stack-gcp/compute-gcp-crossplane-io-v1alpha3.md#gkeclusterclass
[cloudmemorystoreinstance-class]: https://github.com/crossplaneio/crossplane/blob/master/docs/api/crossplaneio/stack-gcp/cache-gcp-crossplane-io-v1beta1.md#cloudmemorystoreinstanceclass
[cloudsqlinstance-class]: https://github.com/crossplaneio/crossplane/blob/master/docs/api/crossplaneio/stack-gcp/database-gcp-crossplane-io-v1beta1.md#cloudsqlinstanceclass