# test-setup-only-aks

## scenario

 * setup RG
 * setup VNET
 * setup subnet
 * setup AKS
 * setup namespaces (and other k8s resources) in AKS, 
 
## modules used

 * for RG, VNET and subnet we will use [AzBI](https://github.com/epiphany-platform/m-azure-basic-infrastructure) module
 * for AKS we will use [AzAKS](https://github.com/epiphany-platform/m-azure-kubernetes-service)
 * for namespaces we will probably prepare new module
 
## assumptions

 * using AzBI module with 0 VM's wasn't tested so far
 
