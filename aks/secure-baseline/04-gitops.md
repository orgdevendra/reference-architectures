### Flux as the GitOps solution

GitOps allows a team to author Kubernetes manifest files, persist them in their git repo, and have them automatically apply to their cluster as changes occur.  This reference implementation is focused on the baseline cluster, so Flux is managing cluster-level concerns (distinct from workload-level concerns, which would be possible, and can be done by additional Flux operators). The namespace `cluster-baseline-settings` will be used to provide a logical division of the cluster configuration from workload configuration.  Examples of manifests that are applied:

* Cluster Role Bindings for the AKS-managed Azure AD integration
* AAD Pod Identity
* CSI driver and Azure KeyVault CSI Provider
* the App team (Application ID: 0008) namespace named a0008

1. Install kubectl 1.18 or newer (`kubctl` supports +/-1 kubernetes version)

   ```bash
   sudo az aks install-cli
   kubectl version --client
   ```

1. Get the cluster name

   ```bash
   export AKS_CLUSTER_NAME=$(az deployment group show --resource-group rg-bu0001a0008 -n cluster-stamp --query properties.outputs.aksClusterName.value -o tsv)
   ```

1. Get AKS kubectl credentials

   ```bash
   az aks get-credentials -n $AKS_CLUSTER_NAME -g rg-bu0001a0008 --admin
   ```

1. Deploy Flux

   ```bash
   kubectl create namespace cluster-baseline-settings
   kubectl apply -f https://raw.githubusercontent.com/mspnp/reference-architectures/master/aks/secure-baseline/cluster-baseline-settings/flux.yaml
   kubectl wait --namespace cluster-baseline-settings --for=condition=ready pod --selector=app.kubernetes.io/name=flux --timeout=90s
   ```

-> Nativate: [Secret Managment and Ingress Controller](./05-secret-managment-and-ingress-controller.md)