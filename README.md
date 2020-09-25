# plan

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
 
# steps

 * clone [both](https://github.com/epiphany-platform/m-azure-basic-infrastructure) [modules](https://github.com/epiphany-platform/m-azure-kubernetes-service) into some repos (becouse they are not released currently)
 * in both do `make build`
 * check: 
    ```
    ~docker image ls
    epiphanyplatform/azbi                         0.0.1               b3819d5ea3b2        3 seconds ago       376MB
    epiphanyplatform/azks                         0.0.1               0dd2cd1547cc        25 seconds ago      376MB
    ```
 * create `./shared` directory
 * init AzBI module with VMS_COUNT=0
    ```
    ~docker run --rm -v /Users/mateusz/Code/github.com/mkyc/test-setup-only-aks/shared:/shared -t epiphanyplatform/azbi:0.0.1 init M_VMS_COUNT=0 M_PUBLIC_IPS=false M_NAME=aks_only_tests
    ```
 * validate that `./shared/azbi/azbi-config.yml` and `./shared/state.yml` files exists
 * get azure credentials into ENV variables as described in readme of both modules
 * run plan of prepared configuration
    ```
    ~docker run --rm -v /Users/mateusz/Code/github.com/mkyc/test-setup-only-aks/shared:/shared -t epiphanyplatform/azbi:0.0.1 plan M_ARM_CLIENT_ID=lalala M_ARM_CLIENT_SECRET=lilili M_ARM_SUBSCRIPTION_ID=lololo M_ARM_TENANT_ID=lululu
    ```
 * verify that `./shared/azbi/terraform-apply.tfplan` got created
 * run apply of created plan
    ```
    ~docker run --rm -v /Users/mateusz/Code/github.com/mkyc/test-setup-only-aks/shared:/shared -t epiphanyplatform/azbi:0.0.1 apply M_ARM_CLIENT_ID=lalala M_ARM_CLIENT_SECRET=lilili M_ARM_SUBSCRIPTION_ID=lololo M_ARM_TENANT_ID=lululu
    ```
 * validate that `./shared/state.yml` file looks similar to following one: 
    ```
    kind: state
    azbi:
      status: applied
      size: 0
      use_public_ip: false
      location: "germanywestcentral"
      name: "aks_only_tests"
      address_space:
        - "10.0.0.0/16"
      address_prefixes:
        - "10.0.1.0/24"
      rsa_pub_path: "/shared/vms_rsa.pub"
      output:
        private_ips.value: []
        public_ips.value: []
        rg_name.value: aks_only_tests-rg
        vm_names.value: []
        vnet_name.value: aks_only_tests-vnet
    ```
 * init AzAKS module with default values
    ```
    ~docker run --rm -v /Users/mateusz/Code/github.com/mkyc/test-setup-only-aks/shared:/shared -t epiphanyplatform/azks:0.0.1 init
    ```
 * verify that `./shared/azks/azks-config.yml` got created and fields `rg_name`, `vnet_name` and `address_prefix` are correct
 * verify that `./shared/state.yml` got new sections: 
    ```
    azks:
    status: initialized
    ```
 * generate ssh keys in here: `./shared/vms_rsa.pub`
    ```
    ~ssh-keygen -t rsa -b 4096 -f ./vms_rsa -N ''
    ```
 * run plan 
    ```
    ~docker run --rm -v /Users/mateusz/Code/github.com/mkyc/test-setup-only-aks/shared:/shared -t epiphanyplatform/azbi:0.0.1 plan M_ARM_CLIENT_ID=lalala M_ARM_CLIENT_SECRET=lilili M_ARM_SUBSCRIPTION_ID=lololo M_ARM_TENANT_ID=lululu
    ```
 * verify that `./shared/azks/terraform-apply.tfplan` got created
 * run apply of created plan
    ```
    ~docker run --rm -v /Users/mateusz/Code/github.com/mkyc/test-setup-only-aks/shared:/shared -t epiphanyplatform/azks:0.0.1 apply M_ARM_CLIENT_ID=lalala M_ARM_CLIENT_SECRET=lilili M_ARM_SUBSCRIPTION_ID=lololo M_ARM_TENANT_ID=lululu
    ```
 * validate that `./shared/state.yml` file looks similar to following one: 
    ```
    TODO
    ```