# Azure Infrastructure Operations Project: Deploying a scalable IaaS web server in Azure

### Introduction
For this project, we will write a Packer template and a Terraform template to deploy a customizable, scalable web server in Azure. We create a policy that ensures all indexed resources are tagged which will help us with organization and tracking, and make it easier to log when things go wrong. In order to support application deployment, we create an image that different organizations can take advantage of to deploy their own apps! To do this, we create a packer image that anyone can use, and we'll leverage in our own Terraform template. Our Terraform template will allow us to reliably create, update, and destroy our infrastructure. Using a service principal in Azure, we deploy our VM image using Packer. Once our image is successfully deployed, we use Terraform to deploy our infrastructure (making sure to run terraform plan with the -out flag, we save the plan file with the name solution.plan).

### Getting Started
1. Clone this repository

2. Install the dependencies below

3. Create your own Infrastructure, follow the instructions below

### Dependencies
1. Create an [Azure Account](https://portal.azure.com) 
2. Install the [Azure command line interface](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
3. Install [Packer](https://www.packer.io/downloads)
4. Install [Terraform](https://www.terraform.io/downloads.html)

### Prerequisites
We assume that, the code is cloned in local disk in Drive E inside Udacity-Bosch-Devops named folder
1. The full path is - E:\Udacity-Bosch-Devops\WebServerProject
2. Exection starts from E:\Udacity-Bosch-Devops\WebServerProject\Scripts
3. `cd E:\Udacity-Bosch-Devops\WebServerProject\Scripts` 
  ![setpath](https://i.imgur.com/AZIqsbD.png)
4. In terminal, use the Azure CLI tool to setup your account permissions locally. Make sure that your login is already done, if not please use the `az login`
   ![setpath](https://i.imgur.com/yzu4xLX.png)

5. you can also set the subscription name using the following command (recommended)\
   `az account set --name <subscriptionName>`

### Customization
Make sure to copu Managedimageid and change it in vars.tf file\
When the image is created, copy the ManagedimageId and change it in Configuaration\vars.tf file for the variable\
Details are in Instructions step 6. 

### Note: The execution is done in our own private subscription.

### Steps to Deploy the Infrastructure
1. Follow the prerequsite steps 
2. Create Policy\
Apply Policy -createPolicy.ps1\
Execute it using powershell ISE - `./createPolicy`\
![tagging-policy](https://i.imgur.com/LhjRxVk.png)\
The script also lists the policies existing in the subscription. Screeshot below-\
![az-list-policies](https://i.imgur.com/jldBaDw.png)

3. Create ResourceGroup\
Execute it using powershell ISE - `./createResourceGroup.ps1`\
This script checks if the ResourceGroup is already created,it does not create a new one, otherwise it creates a new\
ResourceGroup and validate it. Screeshot below-\
![resourcegroup](https://i.imgur.com/oh9mldZ.png)

4. Create ServicePrinciple\
Execute it using powershell ISE - `./createServicePrinciple.ps1`\
This script Create Service Principle and parse into Json values and then store the secrets in the environment variables. Screeshot below-\
![service-principle](https://i.imgur.com/kRnY07a.png)

5.  Create PackerImage\
Execute it using powershell ISE - `./createImage.ps1`\
This script Creates a server image, which we have created using Packer. Screeshot below-\
![packer-image](https://i.imgur.com/Mz1vbIt.png)
you can Optionally delete these images. you have to change the resource name and image name in the deletePackerImage.ps1 file.\
Execute it using powershell ISE - `./deletePackerImage.ps1`
6. Copy the ManagedImageid and change it in vars.tf file\
When the image is created, copy the ManagedimageId and change it in Configuaration\vars.tf file for the variable\
packerImage. Screenshot below-\
![packer-image](https://i.imgur.com/IGIfJtK.png)
![packer-image](https://i.imgur.com/wlBVRwt.png)
7. Deploy Resources with Terraform\
Execute it using powershell ISE - `./createResources.ps1`\
This script deploys your whole infrasturcuture using Terraform and Packer Image. Screeshot below-\
![Terraform-output](https://i.imgur.com/Y03FbOv.png)
![Terraform-resources-list](https://i.imgur.com/dvVddUa.png)
8. destroy Resources (optional)
Execute it using powershell ISE - `./DeleteReources.ps1`\
This script destroys your infrastructure. 



### Output
When you deployed infrastructure following the instructions above, your ourput should be similar to the following. 
1. createPolicy.ps1 Output

    ```{
    "description": "This policy ensures that all indexed resources in our subscription have tags and deny deployment if they do not.",
    "displayName": "Deny the creation of resources that do not have tags",
    "id": "/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/providers/Microsoft.Authorization/policyDefinitions/tagging-policy",
    "metadata": {
        "createdBy": "11cbe341-40ee-42e5-af3c-ae3a075969b4",
        "createdOn": "2022-07-26T11:34:44.1131395Z",
        "updatedBy": "11cbe341-40ee-42e5-af3c-ae3a075969b4",
        "updatedOn": "2022-08-04T20:28:22.06422Z"
    },
    "mode": "Indexed",
    "name": "tagging-policy",
    "parameters": null,
    "policyRule": {
        "if": {
        "anyOf": [
            {
            "equals": "0",
            "value": "[length(field('tags'))]"
            },
            {
            "exists": "false",
            "field": "tags"
            }
        ]
        },
        "then": {
        "effect": "deny"
        }
    },
    "policyType": "Custom",
    "systemData": {
        "createdAt": "2022-07-26T11:34:44.058921+00:00",
        "createdBy": "ahmadjee75@outlook.com",
        "createdByType": "User",
        "lastModifiedAt": "2022-08-04T20:28:22.040273+00:00",
        "lastModifiedBy": "ahmadjee75@outlook.com",
        "lastModifiedByType": "User"
    },
    "type": "Microsoft.Authorization/policyDefinitions"
    }
    tagging-policy Policy is created successfully, details are following


    description           : This policy ensures that all indexed resources in our subscription have tags and deny deployment if they do not.
    displayName           : Deny the creation of resources that do not have tags
    enforcementMode       : Default
    id                    : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/providers/Microsoft.Authorization/policyAssignments/tagging-policy
    identity              : 
    location              : 
    metadata              : @{createdBy=11cbe341-40ee-42e5-af3c-ae3a075969b4; createdOn=2022-08-03T22:05:09.6072061Z; 
                            updatedBy=11cbe341-40ee-42e5-af3c-ae3a075969b4; updatedOn=2022-08-04T20:28:25.1722128Z}
    name                  : tagging-policy
    nonComplianceMessages : 
    notScopes             : 
    parameters            : 
    policyDefinitionId    : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/providers/Microsoft.Authorization/policyDefinitions/tagging-policy
    scope                 : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433
    systemData            : @{createdAt=2022-08-03T22:05:09.574833+00:00; createdBy=ahmadjee75@outlook.com; createdByType=User; 
                            lastModifiedAt=2022-08-04T20:28:25.134342+00:00; lastModifiedBy=ahmadjee75@outlook.com; lastModifiedByType=User}
    type                  : Microsoft.Authorization/policyAssignments

    Following are the list of All policies that exists in the subscription
    description           : This is the default set of policies monitored by Azure Security Center. It was automatically assigned as part of 
                            onboarding to Security Center. The default assignment contains only audit policies. For more information please visit 
                            https://aka.ms/ascpolicies
    displayName           : ASC Default (subscription: 78edc3c6-6f4c-46b2-8fed-9503d6b80433)
    enforcementMode       : Default
    id                    : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/providers/Microsoft.Authorization/policyAssignments/SecurityCenterB
                            uiltIn
    identity              : 
    location              : 
    metadata              : @{assignedBy=Security Center; createdBy=b66bf747-2e3e-4bc1-878a-0a420c3be956; createdOn=2022-07-27T03:20:17.6950167Z; 
                            excludedOutOfTheBoxStandards=System.Object[]; updatedBy=; updatedOn=}
    name                  : SecurityCenterBuiltIn
    nonComplianceMessages : 
    notScopes             : 
    parameters            : 
    policyDefinitionId    : /providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8
    scope                 : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433
    systemData            : @{createdAt=2022-07-27T03:20:16.930864+00:00; createdBy=8edd93e1-2103-40b4-bd70-6e34e586362d; 
                            createdByType=Application; lastModifiedAt=2022-07-27T03:20:16.930864+00:00; 
                            lastModifiedBy=8edd93e1-2103-40b4-bd70-6e34e586362d; lastModifiedByType=Application}
    type                  : Microsoft.Authorization/policyAssignments

    description           : This policy ensures that all indexed resources in our subscription have tags and deny deployment if they do not.
    displayName           : Deny the creation of resources that do not have tags
    enforcementMode       : Default
    id                    : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/providers/Microsoft.Authorization/policyAssignments/tagging-policy
    identity              : 
    location              : 
    metadata              : @{createdBy=11cbe341-40ee-42e5-af3c-ae3a075969b4; createdOn=2022-08-03T22:05:09.6072061Z; 
                            updatedBy=11cbe341-40ee-42e5-af3c-ae3a075969b4; updatedOn=2022-08-04T20:28:25.1722128Z}
    name                  : tagging-policy
    nonComplianceMessages : 
    notScopes             : 
    parameters            : 
    policyDefinitionId    : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/providers/Microsoft.Authorization/policyDefinitions/tagging-policy
    scope                 : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433
    systemData            : @{createdAt=2022-08-03T22:05:09.574833+00:00; createdBy=ahmadjee75@outlook.com; createdByType=User; 
                            lastModifiedAt=2022-08-04T20:28:25.134342+00:00; lastModifiedBy=ahmadjee75@outlook.com; lastModifiedByType=User}
    type                  : Microsoft.Authorization/policyAssignments
    ```

2. createResourceGroup.ps1 Output
   ```
   ResourceGroup is created, details are following

    id         : /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/packerImageRG
    location   : westeurope
    managedBy  : 
    name       : packerImageRG
    properties : @{provisioningState=Succeeded}
    tags       : @{webServerDeployment=}
    type       : Microsoft.Resources/resourceGroups
   ```
3. createServicePrinciple.ps1 Output
   ```
    Using subscription ID 78edc3c6-6f4c-46b2-8fed-9503d6b80433
    Creating SP for RBAC with name servicePrincpleUdacity, with role contributor and in scopes /subscriptions/78edc3c6-6f4c-4
    6b2-8fed-9503d6b80433/resourceGroups/packerImageRG
   ```
   Service Principle "servicePrincpleUdacity" with Role contributer created. 

   ![service-principle](https://i.imgur.com/kRnY07a.png)
5. createPackerImage.ps1 Output
   ```
    ==> azure-arm: Running builder ...
    ==> azure-arm: Getting tokens using client secret
    ==> azure-arm: Getting tokens using client secret
        azure-arm: Creating Azure Resource Manager (ARM) client ...
    ==> azure-arm: Using existing resource group ...
    ==> azure-arm:  -> ResourceGroupName : 'packerImageRG'
    ==> azure-arm:  -> Location          : 'westeurope'
    ==> azure-arm: Validating deployment template ...
    ==> azure-arm:  -> ResourceGroupName : 'packerImageRG'
    ==> azure-arm:  -> DeploymentName    : 'pkrdp1059wn54xd'
    ==> azure-arm: Deploying deployment template ...
    ==> azure-arm:  -> ResourceGroupName : 'packerImageRG'
    ==> azure-arm:  -> DeploymentName    : 'pkrdp1059wn54xd'
    ==> azure-arm:
    ==> azure-arm: Getting the VM's IP address ...
    ==> azure-arm:  -> ResourceGroupName   : 'packerImageRG'
    ==> azure-arm:  -> PublicIPAddressName : 'pkrip1059wn54xd'
    ==> azure-arm:  -> NicName             : 'pkrni1059wn54xd'
    ==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
    ==> azure-arm:  -> IP Address          : '13.95.112.14'
    ==> azure-arm: Waiting for SSH to become available...
    ==> azure-arm: Connected to SSH!
    ==> azure-arm: Provisioning with shell script: C:\Users\SyedShah\AppData\Local\Temp\packer-shell318249523
    ==> azure-arm: + echo Hello, World!
    ==> azure-arm: + nohup busybox httpd -f -p 80
    ==> azure-arm: Querying the machine's properties ...
    ==> azure-arm:  -> ResourceGroupName : 'packerImageRG'
    ==> azure-arm:  -> ComputeName       : 'pkrvm1059wn54xd'
    ==> azure-arm:  -> Managed OS Disk   : '/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/packerImageRG/providers/Microsoft.Com
    pute/disks/pkros1059wn54xd'
    ==> azure-arm: Querying the machine's additional disks properties ...
    ==> azure-arm:  -> ResourceGroupName : 'packerImageRG'
    ==> azure-arm:  -> ComputeName       : 'pkrvm1059wn54xd'
    ==> azure-arm: Powering off machine ...
    ==> azure-arm:  -> ResourceGroupName : 'packerImageRG'
    ==> azure-arm:  -> ComputeName       : 'pkrvm1059wn54xd'
    ==> azure-arm: Capturing image ...
    ==> azure-arm:  -> Compute ResourceGroupName : 'packerImageRG'
    ==> azure-arm:  -> Compute Name              : 'pkrvm1059wn54xd'
    ==> azure-arm:  -> Compute Location          : 'westeurope'
    ==> azure-arm:  -> Image ResourceGroupName   : 'packerImageRG'
    ==> azure-arm:  -> Image Name                : 'packerImage-UdacityBosch'
    ==> azure-arm:  -> Image Location            : 'westeurope'
    ==> azure-arm: 
    ==> azure-arm: Deleting individual resources ...
    ==> azure-arm: Adding to deletion queue -> Microsoft.Compute/virtualMachines : 'pkrvm1059wn54xd'
    ==> azure-arm: Adding to deletion queue -> Microsoft.Network/networkInterfaces : 'pkrni1059wn54xd'
    ==> azure-arm: Adding to deletion queue -> Microsoft.Network/publicIPAddresses : 'pkrip1059wn54xd'
    ==> azure-arm: Adding to deletion queue -> Microsoft.Network/virtualNetworks : 'pkrvn1059wn54xd'
    ==> azure-arm: Waiting for deletion of all resources...
    ==> azure-arm: Attempting deletion -> Microsoft.Network/publicIPAddresses : 'pkrip1059wn54xd'
    ==> azure-arm: Attempting deletion -> Microsoft.Network/virtualNetworks : 'pkrvn1059wn54xd'
    ==> azure-arm: Attempting deletion -> Microsoft.Compute/virtualMachines : 'pkrvm1059wn54xd'
    ==> azure-arm: Attempting deletion -> Microsoft.Network/networkInterfaces : 'pkrni1059wn54xd'
    ==> azure-arm: Error deleting resource. Will retry.
    ==> azure-arm: Name: pkrvn1059wn54xd
    ==> azure-arm: Error: network.VirtualNetworksClient#Delete: Failure sending request: StatusCode=400 -- Original Error: Code="InUseSubnetCannotBe
    Deleted" Message="Subnet pkrsn1059wn54xd is in use by /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/packerImageRG/providers
    /Microsoft.Network/networkInterfaces/pkrni1059wn54xd/ipConfigurations/ipconfig and cannot be deleted. In order to delete the subnet, delete all 
    the resources within the subnet. See aka.ms/deletesubnet." Details=[]
    ==> azure-arm:
    ==> azure-arm: Error deleting resource. Will retry.
    ==> azure-arm: Name: pkrip1059wn54xd
    ==> azure-arm: Error: network.PublicIPAddressesClient#Delete: Failure sending request: StatusCode=400 -- Original Error: Code="PublicIPAddressCa
    nnotBeDeleted" Message="Public IP address /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/packerImageRG/providers/Microsoft.N
    etwork/publicIPAddresses/pkrip1059wn54xd can not be deleted since it is still allocated to resource /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d
    6b80433/resourceGroups/packerImageRG/providers/Microsoft.Network/networkInterfaces/pkrni1059wn54xd/ipConfigurations/ipconfig. In order to delete
    the public IP, disassociate/detach the Public IP address from the resource.  To learn how to do this, see aka.ms/deletepublicip." Details=[]
    ==> azure-arm:
    ==> azure-arm: Attempting deletion -> Microsoft.Network/virtualNetworks : 'pkrvn1059wn54xd'
    ==> azure-arm: Attempting deletion -> Microsoft.Network/publicIPAddresses : 'pkrip1059wn54xd'
    ==> azure-arm:  Deleting -> Microsoft.Compute/disks : '/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/packerImageRG/provider
    s/Microsoft.Compute/disks/pkros1059wn54xd'
    ==> azure-arm: Removing the created Deployment object: 'pkrdp1059wn54xd'
    ==> azure-arm: 
    ==> azure-arm: The resource group was not created by Packer, not deleting ...
    Build 'azure-arm' finished after 5 minutes 37 seconds.

    ==> Wait completed after 5 minutes 37 seconds

    ==> Builds finished. The artifacts of successful builds are:
    --> azure-arm: Azure.ResourceManagement.VMImage:

    OSType: Linux
    ManagedImageResourceGroupName: packerImageRG
    ManagedImageName: packerImage-UdacityBosch
    ManagedImageId: /subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/packerImageRG/providers/Microsoft.Compute/images/packerImage-
    UdacityBosch
    ManagedImageLocation: westeurope
    ```
    ![packer-Image](https://i.imgur.com/SJW201j.png)
6. Make Sure that you changed the managedimageid in vars.tf file. 
7. createResources.ps1 Output
    ```
        Starting pre-deploy steps for the TF resources...

    [0m[1mInitializing the backend...[0m

    [0m[1mInitializing provider plugins...[0m
    - Finding hashicorp/azurerm versions matching "~> 2.98"...
    - Installing hashicorp/azurerm v2.99.0...
    - Installed hashicorp/azurerm v2.99.0 (signed by HashiCorp)

    Terraform has created a lock file [1m.terraform.lock.hcl[0m to record the provider
    selections it made above. Include this file in your version control repository
    so that Terraform can guarantee to make the same selections by default when
    you run "terraform init" in the future.[0m

    [0m[1m[32mTerraform has been successfully initialized![0m[32m[0m
    [0m[32m
    You may now begin working with Terraform. Try running "terraform plan" to see
    any changes that are required for your infrastructure. All Terraform commands
    should now work.

    If you ever set or change modules or backend configuration for Terraform,
    rerun this command to reinitialize your working directory. If you forget, other
    commands will detect it and remind you to do so if necessary.[0m
    [32m[1mSuccess![0m The configuration is valid.
    [0m
    pre-deploy steps for the TF resources finished successfully !
    Starting deploy steps for the TF resources...

    Terraform used the selected providers to generate the following execution
    plan. Resource actions are indicated with the following symbols:
    [32m+[0m create
    [0m
    Terraform will perform the following actions:

    [1m  # azurerm_availability_set.webserverTfPackerAVAIL[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_availability_set" "webserverTfPackerAVAIL" {
        [32m+[0m [0m[1m[0mid[0m[0m                           = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m                     = "westeurope"
        [32m+[0m [0m[1m[0mmanaged[0m[0m                      = true
        [32m+[0m [0m[1m[0mname[0m[0m                         = "avail-dev-westeurope-001"
        [32m+[0m [0m[1m[0mplatform_fault_domain_count[0m[0m  = 2
        [32m+[0m [0m[1m[0mplatform_update_domain_count[0m[0m = 2
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m          = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0mtags[0m[0m                         = (known after apply)
        }

    [1m  # azurerm_lb.webserverTfPackerLB[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_lb" "webserverTfPackerLB" {
        [32m+[0m [0m[1m[0mid[0m[0m                   = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m             = "westeurope"
        [32m+[0m [0m[1m[0mname[0m[0m                 = "lbi-dev-westeurope-001"
        [32m+[0m [0m[1m[0mprivate_ip_address[0m[0m   = (known after apply)
        [32m+[0m [0m[1m[0mprivate_ip_addresses[0m[0m = (known after apply)
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m  = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0msku[0m[0m                  = "Basic"
        [32m+[0m [0m[1m[0msku_tier[0m[0m             = "Regional"
        [32m+[0m [0m[1m[0mtags[0m[0m                 = (known after apply)

        [32m+[0m [0mfrontend_ip_configuration {
            [32m+[0m [0m[1m[0mavailability_zone[0m[0m                                  = (known after apply)
            [32m+[0m [0m[1m[0mgateway_load_balancer_frontend_ip_configuration_id[0m[0m = (known after apply)
            [32m+[0m [0m[1m[0mid[0m[0m                                                 = (known after apply)
            [32m+[0m [0m[1m[0minbound_nat_rules[0m[0m                                  = (known after apply)
            [32m+[0m [0m[1m[0mload_balancer_rules[0m[0m                                = (known after apply)
            [32m+[0m [0m[1m[0mname[0m[0m                                               = "frontendip-dev-westeurope-001"
            [32m+[0m [0m[1m[0moutbound_rules[0m[0m                                     = (known after apply)
            [32m+[0m [0m[1m[0mprivate_ip_address[0m[0m                                 = (known after apply)
            [32m+[0m [0m[1m[0mprivate_ip_address_allocation[0m[0m                      = (known after apply)
            [32m+[0m [0m[1m[0mprivate_ip_address_version[0m[0m                         = (known after apply)
            [32m+[0m [0m[1m[0mpublic_ip_address_id[0m[0m                               = (known after apply)
            [32m+[0m [0m[1m[0mpublic_ip_prefix_id[0m[0m                                = (known after apply)
            [32m+[0m [0m[1m[0msubnet_id[0m[0m                                          = (known after apply)
            [32m+[0m [0m[1m[0mzones[0m[0m                                              = (known after apply)
            }
        }

    [1m  # azurerm_lb_backend_address_pool.webserverTfPackerBackendLB[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_lb_backend_address_pool" "webserverTfPackerBackendLB" {
        [32m+[0m [0m[1m[0mbackend_ip_configurations[0m[0m = (known after apply)
        [32m+[0m [0m[1m[0mid[0m[0m                        = (known after apply)
        [32m+[0m [0m[1m[0mload_balancing_rules[0m[0m      = (known after apply)
        [32m+[0m [0m[1m[0mloadbalancer_id[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mname[0m[0m                      = "backend-address-dev-westeurope-001"
        [32m+[0m [0m[1m[0moutbound_rules[0m[0m            = (known after apply)
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m       = (known after apply)
        }

    [1m  # azurerm_linux_virtual_machine.webserverTfPackerLinuxVM[0][0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_linux_virtual_machine" "webserverTfPackerLinuxVM" {
        [32m+[0m [0m[1m[0madmin_password[0m[0m                  = (sensitive value)
        [32m+[0m [0m[1m[0madmin_username[0m[0m                  = "syedahmad75"
        [32m+[0m [0m[1m[0mallow_extension_operations[0m[0m      = true
        [32m+[0m [0m[1m[0mavailability_set_id[0m[0m             = (known after apply)
        [32m+[0m [0m[1m[0mcomputer_name[0m[0m                   = (known after apply)
        [32m+[0m [0m[1m[0mdisable_password_authentication[0m[0m = false
        [32m+[0m [0m[1m[0mextensions_time_budget[0m[0m          = "PT1H30M"
        [32m+[0m [0m[1m[0mid[0m[0m                              = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m                        = "westeurope"
        [32m+[0m [0m[1m[0mmax_bid_price[0m[0m                   = -1
        [32m+[0m [0m[1m[0mname[0m[0m                            = "linux-vm-dev-westeurope-001-0"
        [32m+[0m [0m[1m[0mnetwork_interface_ids[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mpatch_mode[0m[0m                      = "ImageDefault"
        [32m+[0m [0m[1m[0mplatform_fault_domain[0m[0m           = -1
        [32m+[0m [0m[1m[0mpriority[0m[0m                        = "Regular"
        [32m+[0m [0m[1m[0mprivate_ip_address[0m[0m              = (known after apply)
        [32m+[0m [0m[1m[0mprivate_ip_addresses[0m[0m            = (known after apply)
        [32m+[0m [0m[1m[0mprovision_vm_agent[0m[0m              = true
        [32m+[0m [0m[1m[0mpublic_ip_address[0m[0m               = (known after apply)
        [32m+[0m [0m[1m[0mpublic_ip_addresses[0m[0m             = (known after apply)
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m             = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0msize[0m[0m                            = "Standard_D2s_v3"
        [32m+[0m [0m[1m[0msource_image_id[0m[0m                 = "/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/packe
    rImageRG/providers/Microsoft.Compute/images/packerImage-UdacityBosch"
        [32m+[0m [0m[1m[0mtags[0m[0m                            = (known after apply)
        [32m+[0m [0m[1m[0mvirtual_machine_id[0m[0m              = (known after apply)
        [32m+[0m [0m[1m[0mzone[0m[0m                            = (known after apply)

        [32m+[0m [0mos_disk {
            [32m+[0m [0m[1m[0mcaching[0m[0m                   = "ReadWrite"
            [32m+[0m [0m[1m[0mdisk_size_gb[0m[0m              = (known after apply)
            [32m+[0m [0m[1m[0mname[0m[0m                      = (known after apply)
            [32m+[0m [0m[1m[0mstorage_account_type[0m[0m      = "Standard_LRS"
            [32m+[0m [0m[1m[0mwrite_accelerator_enabled[0m[0m = false
            }
        }

    [1m  # azurerm_linux_virtual_machine.webserverTfPackerLinuxVM[1][0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_linux_virtual_machine" "webserverTfPackerLinuxVM" {
        [32m+[0m [0m[1m[0madmin_password[0m[0m                  = (sensitive value)
        [32m+[0m [0m[1m[0madmin_username[0m[0m                  = "syedahmad75"
        [32m+[0m [0m[1m[0mallow_extension_operations[0m[0m      = true
        [32m+[0m [0m[1m[0mavailability_set_id[0m[0m             = (known after apply)
        [32m+[0m [0m[1m[0mcomputer_name[0m[0m                   = (known after apply)
        [32m+[0m [0m[1m[0mdisable_password_authentication[0m[0m = false
        [32m+[0m [0m[1m[0mextensions_time_budget[0m[0m          = "PT1H30M"
        [32m+[0m [0m[1m[0mid[0m[0m                              = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m                        = "westeurope"
        [32m+[0m [0m[1m[0mmax_bid_price[0m[0m                   = -1
        [32m+[0m [0m[1m[0mname[0m[0m                            = "linux-vm-dev-westeurope-001-1"
        [32m+[0m [0m[1m[0mnetwork_interface_ids[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mpatch_mode[0m[0m                      = "ImageDefault"
        [32m+[0m [0m[1m[0mplatform_fault_domain[0m[0m           = -1
        [32m+[0m [0m[1m[0mpriority[0m[0m                        = "Regular"
        [32m+[0m [0m[1m[0mprivate_ip_address[0m[0m              = (known after apply)
        [32m+[0m [0m[1m[0mprivate_ip_addresses[0m[0m            = (known after apply)
        [32m+[0m [0m[1m[0mprovision_vm_agent[0m[0m              = true
        [32m+[0m [0m[1m[0mpublic_ip_address[0m[0m               = (known after apply)
        [32m+[0m [0m[1m[0mpublic_ip_addresses[0m[0m             = (known after apply)
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m             = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0msize[0m[0m                            = "Standard_D2s_v3"
        [32m+[0m [0m[1m[0msource_image_id[0m[0m                 = "/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/packe
    rImageRG/providers/Microsoft.Compute/images/packerImage-UdacityBosch"
        [32m+[0m [0m[1m[0mtags[0m[0m                            = (known after apply)
        [32m+[0m [0m[1m[0mvirtual_machine_id[0m[0m              = (known after apply)
        [32m+[0m [0m[1m[0mzone[0m[0m                            = (known after apply)

        [32m+[0m [0mos_disk {
            [32m+[0m [0m[1m[0mcaching[0m[0m                   = "ReadWrite"
            [32m+[0m [0m[1m[0mdisk_size_gb[0m[0m              = (known after apply)
            [32m+[0m [0m[1m[0mname[0m[0m                      = (known after apply)
            [32m+[0m [0m[1m[0mstorage_account_type[0m[0m      = "Standard_LRS"
            [32m+[0m [0m[1m[0mwrite_accelerator_enabled[0m[0m = false
            }
        }

    [1m  # azurerm_managed_disk.webserverTfPackerMD[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_managed_disk" "webserverTfPackerMD" {
        [32m+[0m [0m[1m[0mcreate_option[0m[0m                 = "Empty"
        [32m+[0m [0m[1m[0mdisk_iops_read_only[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mdisk_iops_read_write[0m[0m          = (known after apply)
        [32m+[0m [0m[1m[0mdisk_mbps_read_only[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mdisk_mbps_read_write[0m[0m          = (known after apply)
        [32m+[0m [0m[1m[0mdisk_size_gb[0m[0m                  = 1
        [32m+[0m [0m[1m[0mid[0m[0m                            = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m                      = "westeurope"
        [32m+[0m [0m[1m[0mlogical_sector_size[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mmax_shares[0m[0m                    = (known after apply)
        [32m+[0m [0m[1m[0mname[0m[0m                          = "md-dev-westeurope-001"
        [32m+[0m [0m[1m[0mpublic_network_access_enabled[0m[0m = true
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m           = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0msource_uri[0m[0m                    = (known after apply)
        [32m+[0m [0m[1m[0mstorage_account_type[0m[0m          = "Standard_LRS"
        [32m+[0m [0m[1m[0mtags[0m[0m                          = (known after apply)
        [32m+[0m [0m[1m[0mtier[0m[0m                          = (known after apply)
        }

    [1m  # azurerm_network_interface.webserverTfPackerNIC[0][0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_network_interface" "webserverTfPackerNIC" {
        [32m+[0m [0m[1m[0mapplied_dns_servers[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mdns_servers[0m[0m                   = (known after apply)
        [32m+[0m [0m[1m[0menable_accelerated_networking[0m[0m = false
        [32m+[0m [0m[1m[0menable_ip_forwarding[0m[0m          = false
        [32m+[0m [0m[1m[0mid[0m[0m                            = (known after apply)
        [32m+[0m [0m[1m[0minternal_dns_name_label[0m[0m       = (known after apply)
        [32m+[0m [0m[1m[0minternal_domain_name_suffix[0m[0m   = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m                      = "westeurope"
        [32m+[0m [0m[1m[0mmac_address[0m[0m                   = (known after apply)
        [32m+[0m [0m[1m[0mname[0m[0m                          = "nic-dev-westeurope-001-server1-udacity"
        [32m+[0m [0m[1m[0mprivate_ip_address[0m[0m            = (known after apply)
        [32m+[0m [0m[1m[0mprivate_ip_addresses[0m[0m          = (known after apply)
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m           = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0mtags[0m[0m                          = (known after apply)
        [32m+[0m [0m[1m[0mvirtual_machine_id[0m[0m            = (known after apply)

        [32m+[0m [0mip_configuration {
            [32m+[0m [0m[1m[0mgateway_load_balancer_frontend_ip_configuration_id[0m[0m = (known after apply)
            [32m+[0m [0m[1m[0mname[0m[0m                                               = "ipconfig-dev-westeurope-001-udacity"
            [32m+[0m [0m[1m[0mprimary[0m[0m                                            = (known after apply)
            [32m+[0m [0m[1m[0mprivate_ip_address[0m[0m                                 = (known after apply)
            [32m+[0m [0m[1m[0mprivate_ip_address_allocation[0m[0m                      = "Dynamic"
            [32m+[0m [0m[1m[0mprivate_ip_address_version[0m[0m                         = "IPv4"
            [32m+[0m [0m[1m[0msubnet_id[0m[0m                                          = (known after apply)
            }
        }

    [1m  # azurerm_network_interface.webserverTfPackerNIC[1][0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_network_interface" "webserverTfPackerNIC" {
        [32m+[0m [0m[1m[0mapplied_dns_servers[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mdns_servers[0m[0m                   = (known after apply)
        [32m+[0m [0m[1m[0menable_accelerated_networking[0m[0m = false
        [32m+[0m [0m[1m[0menable_ip_forwarding[0m[0m          = false
        [32m+[0m [0m[1m[0mid[0m[0m                            = (known after apply)
        [32m+[0m [0m[1m[0minternal_dns_name_label[0m[0m       = (known after apply)
        [32m+[0m [0m[1m[0minternal_domain_name_suffix[0m[0m   = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m                      = "westeurope"
        [32m+[0m [0m[1m[0mmac_address[0m[0m                   = (known after apply)
        [32m+[0m [0m[1m[0mname[0m[0m                          = "nic-dev-westeurope-001-server2-udacity"
        [32m+[0m [0m[1m[0mprivate_ip_address[0m[0m            = (known after apply)
        [32m+[0m [0m[1m[0mprivate_ip_addresses[0m[0m          = (known after apply)
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m           = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0mtags[0m[0m                          = (known after apply)
        [32m+[0m [0m[1m[0mvirtual_machine_id[0m[0m            = (known after apply)

        [32m+[0m [0mip_configuration {
            [32m+[0m [0m[1m[0mgateway_load_balancer_frontend_ip_configuration_id[0m[0m = (known after apply)
            [32m+[0m [0m[1m[0mname[0m[0m                                               = "ipconfig-dev-westeurope-001-udacity"
            [32m+[0m [0m[1m[0mprimary[0m[0m                                            = (known after apply)
            [32m+[0m [0m[1m[0mprivate_ip_address[0m[0m                                 = (known after apply)
            [32m+[0m [0m[1m[0mprivate_ip_address_allocation[0m[0m                      = "Dynamic"
            [32m+[0m [0m[1m[0mprivate_ip_address_version[0m[0m                         = "IPv4"
            [32m+[0m [0m[1m[0msubnet_id[0m[0m                                          = (known after apply)
            }
        }

    [1m  # azurerm_network_interface_backend_address_pool_association.webserverTfPackerBackendNicLB[0][0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_network_interface_backend_address_pool_association" "webserverTfPackerBackendNicLB" {
        [32m+[0m [0m[1m[0mbackend_address_pool_id[0m[0m = (known after apply)
        [32m+[0m [0m[1m[0mid[0m[0m                      = (known after apply)
        [32m+[0m [0m[1m[0mip_configuration_name[0m[0m   = "ipconfig-dev-westeurope-001-udacity"
        [32m+[0m [0m[1m[0mnetwork_interface_id[0m[0m    = (known after apply)
        }

    [1m  # azurerm_network_interface_backend_address_pool_association.webserverTfPackerBackendNicLB[1][0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_network_interface_backend_address_pool_association" "webserverTfPackerBackendNicLB" {
        [32m+[0m [0m[1m[0mbackend_address_pool_id[0m[0m = (known after apply)
        [32m+[0m [0m[1m[0mid[0m[0m                      = (known after apply)
        [32m+[0m [0m[1m[0mip_configuration_name[0m[0m   = "ipconfig-dev-westeurope-001-udacity"
        [32m+[0m [0m[1m[0mnetwork_interface_id[0m[0m    = (known after apply)
        }

    [1m  # azurerm_network_security_group.webserverTfPackerNSG[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_network_security_group" "webserverTfPackerNSG" {
        [32m+[0m [0m[1m[0mid[0m[0m                  = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m            = "westeurope"
        [32m+[0m [0m[1m[0mname[0m[0m                = "nsg-dev-westeurope-001"
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0msecurity_rule[0m[0m       = [
            [32m+[0m [0m{
                [32m+[0m [0maccess                                     = "Allow"
                [32m+[0m [0mdescription                                = "Allow access to other VMs on the subnet"
                [32m+[0m [0mdestination_address_prefix                 = "VirtualNetwork"
                [32m+[0m [0mdestination_address_prefixes               = []
                [32m+[0m [0mdestination_application_security_group_ids = []
                [32m+[0m [0mdestination_port_range                     = "*"
                [32m+[0m [0mdestination_port_ranges                    = []
                [32m+[0m [0mdirection                                  = "Inbound"
                [32m+[0m [0mname                                       = "AllowVMAccessOnSubnet"
                [32m+[0m [0mpriority                                   = 2000
                [32m+[0m [0mprotocol                                   = "*"
                [32m+[0m [0msource_address_prefix                      = "VirtualNetwork"
                [32m+[0m [0msource_address_prefixes                    = []
                [32m+[0m [0msource_application_security_group_ids      = []
                [32m+[0m [0msource_port_range                          = "*"
                [32m+[0m [0msource_port_ranges                         = []
                },
            [32m+[0m [0m{
                [32m+[0m [0maccess                                     = "Deny"
                [32m+[0m [0mdescription                                = "Denies direct access from the internet"
                [32m+[0m [0mdestination_address_prefix                 = "VirtualNetwork"
                [32m+[0m [0mdestination_address_prefixes               = []
                [32m+[0m [0mdestination_application_security_group_ids = []
                [32m+[0m [0mdestination_port_range                     = "*"
                [32m+[0m [0mdestination_port_ranges                    = []
                [32m+[0m [0mdirection                                  = "Inbound"
                [32m+[0m [0mname                                       = "DenyDirectAcessFromInternet"
                [32m+[0m [0mpriority                                   = 1000
                [32m+[0m [0mprotocol                                   = "*"
                [32m+[0m [0msource_address_prefix                      = "Internet"
                [32m+[0m [0msource_address_prefixes                    = []
                [32m+[0m [0msource_application_security_group_ids      = []
                [32m+[0m [0msource_port_range                          = "*"
                [32m+[0m [0msource_port_ranges                         = []
                },
            ]
        [32m+[0m [0m[1m[0mtags[0m[0m                = (known after apply)
        }

    [1m  # azurerm_public_ip.webserverTfPackerPIP[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_public_ip" "webserverTfPackerPIP" {
        [32m+[0m [0m[1m[0mallocation_method[0m[0m       = "Static"
        [32m+[0m [0m[1m[0mavailability_zone[0m[0m       = (known after apply)
        [32m+[0m [0m[1m[0mfqdn[0m[0m                    = (known after apply)
        [32m+[0m [0m[1m[0mid[0m[0m                      = (known after apply)
        [32m+[0m [0m[1m[0midle_timeout_in_minutes[0m[0m = 4
        [32m+[0m [0m[1m[0mip_address[0m[0m              = (known after apply)
        [32m+[0m [0m[1m[0mip_version[0m[0m              = "IPv4"
        [32m+[0m [0m[1m[0mlocation[0m[0m                = "westeurope"
        [32m+[0m [0m[1m[0mname[0m[0m                    = "pip-dev-westeurope-001"
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m     = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0msku[0m[0m                     = "Basic"
        [32m+[0m [0m[1m[0msku_tier[0m[0m                = "Regional"
        [32m+[0m [0m[1m[0mtags[0m[0m                    = (known after apply)
        [32m+[0m [0m[1m[0mzones[0m[0m                   = (known after apply)
        }

    [1m  # azurerm_resource_group.webserverTfPackerRG[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_resource_group" "webserverTfPackerRG" {
        [32m+[0m [0m[1m[0mid[0m[0m       = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m = "westeurope"
        [32m+[0m [0m[1m[0mname[0m[0m     = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0mtags[0m[0m     = (known after apply)
        }

    [1m  # azurerm_subnet.webserverTfPackerSNET[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_subnet" "webserverTfPackerSNET" {
        [32m+[0m [0m[1m[0maddress_prefix[0m[0m                                 = (known after apply)
        [32m+[0m [0m[1m[0maddress_prefixes[0m[0m                               = [
            [32m+[0m [0m"10.0.0.0/24",
            ]
        [32m+[0m [0m[1m[0menforce_private_link_endpoint_network_policies[0m[0m = false
        [32m+[0m [0m[1m[0menforce_private_link_service_network_policies[0m[0m  = false
        [32m+[0m [0m[1m[0mid[0m[0m                                             = (known after apply)
        [32m+[0m [0m[1m[0mname[0m[0m                                           = "snet-dev-westeurope-001"
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m                            = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0mvirtual_network_name[0m[0m                           = "vnet-dev-westeurope-001"
        }

    [1m  # azurerm_virtual_network.webserverTfPackerVNET[0m will be created[0m[0m
    [0m  [32m+[0m[0m resource "azurerm_virtual_network" "webserverTfPackerVNET" {
        [32m+[0m [0m[1m[0maddress_space[0m[0m         = [
            [32m+[0m [0m"10.0.0.0/22",
            ]
        [32m+[0m [0m[1m[0mdns_servers[0m[0m           = (known after apply)
        [32m+[0m [0m[1m[0mguid[0m[0m                  = (known after apply)
        [32m+[0m [0m[1m[0mid[0m[0m                    = (known after apply)
        [32m+[0m [0m[1m[0mlocation[0m[0m              = "westeurope"
        [32m+[0m [0m[1m[0mname[0m[0m                  = "vnet-dev-westeurope-001"
        [32m+[0m [0m[1m[0mresource_group_name[0m[0m   = "rg-dev-westeurope-001"
        [32m+[0m [0m[1m[0msubnet[0m[0m                = (known after apply)
        [32m+[0m [0m[1m[0mtags[0m[0m                  = (known after apply)
        [32m+[0m [0m[1m[0mvm_protection_enabled[0m[0m = false
        }

    [0m[1mPlan:[0m 15 to add, 0 to change, 0 to destroy.
    [0m[90m
    â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
    â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€[0m

    Saved the plan to: solution.plan

    To perform exactly these actions, run the following command to apply:
        terraform apply "solution.plan"
    [0m[1mazurerm_resource_group.webserverTfPackerRG: Creating...[0m[0m
    [0m[1mazurerm_resource_group.webserverTfPackerRG: Creation complete after 1s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceG
    roups/rg-dev-westeurope-001][0m
    [0m[1mazurerm_virtual_network.webserverTfPackerVNET: Creating...[0m[0m
    [0m[1mazurerm_public_ip.webserverTfPackerPIP: Creating...[0m[0m
    [0m[1mazurerm_availability_set.webserverTfPackerAVAIL: Creating...[0m[0m
    [0m[1mazurerm_managed_disk.webserverTfPackerMD: Creating...[0m[0m
    [0m[1mazurerm_network_security_group.webserverTfPackerNSG: Creating...[0m[0m
    [0m[1mazurerm_availability_set.webserverTfPackerAVAIL: Creation complete after 1s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/reso
    urceGroups/rg-dev-westeurope-001/providers/Microsoft.Compute/availabilitySets/avail-dev-westeurope-001][0m
    [0m[1mazurerm_public_ip.webserverTfPackerPIP: Creation complete after 3s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroup
    s/rg-dev-westeurope-001/providers/Microsoft.Network/publicIPAddresses/pip-dev-westeurope-001][0m
    [0m[1mazurerm_lb.webserverTfPackerLB: Creating...[0m[0m
    [0m[1mazurerm_managed_disk.webserverTfPackerMD: Creation complete after 3s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGro
    ups/rg-dev-westeurope-001/providers/Microsoft.Compute/disks/md-dev-westeurope-001][0m
    [0m[1mazurerm_network_security_group.webserverTfPackerNSG: Creation complete after 4s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/
    resourceGroups/rg-dev-westeurope-001/providers/Microsoft.Network/networkSecurityGroups/nsg-dev-westeurope-001][0m
    [0m[1mazurerm_lb.webserverTfPackerLB: Creation complete after 2s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/rg-dev
    -westeurope-001/providers/Microsoft.Network/loadBalancers/lbi-dev-westeurope-001][0m
    [0m[1mazurerm_lb_backend_address_pool.webserverTfPackerBackendLB: Creating...[0m[0m
    [0m[1mazurerm_virtual_network.webserverTfPackerVNET: Creation complete after 5s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resour
    ceGroups/rg-dev-westeurope-001/providers/Microsoft.Network/virtualNetworks/vnet-dev-westeurope-001][0m
    [0m[1mazurerm_subnet.webserverTfPackerSNET: Creating...[0m[0m
    [0m[1mazurerm_lb_backend_address_pool.webserverTfPackerBackendLB: Creation complete after 2s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6
    b80433/resourceGroups/rg-dev-westeurope-001/providers/Microsoft.Network/loadBalancers/lbi-dev-westeurope-001/backendAddressPools/backend-address
    -dev-westeurope-001][0m
    [0m[1mazurerm_subnet.webserverTfPackerSNET: Creation complete after 4s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/
    rg-dev-westeurope-001/providers/Microsoft.Network/virtualNetworks/vnet-dev-westeurope-001/subnets/snet-dev-westeurope-001][0m
    [0m[1mazurerm_network_interface.webserverTfPackerNIC[0]: Creating...[0m[0m
    [0m[1mazurerm_network_interface.webserverTfPackerNIC[1]: Creating...[0m[0m
    [0m[1mazurerm_network_interface.webserverTfPackerNIC[0]: Creation complete after 2s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/re
    sourceGroups/rg-dev-westeurope-001/providers/Microsoft.Network/networkInterfaces/nic-dev-westeurope-001-server1-udacity][0m
    [0m[1mazurerm_network_interface.webserverTfPackerNIC[1]: Creation complete after 3s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/re
    sourceGroups/rg-dev-westeurope-001/providers/Microsoft.Network/networkInterfaces/nic-dev-westeurope-001-server2-udacity][0m
    [0m[1mazurerm_network_interface_backend_address_pool_association.webserverTfPackerBackendNicLB[0]: Creating...[0m[0m
    [0m[1mazurerm_network_interface_backend_address_pool_association.webserverTfPackerBackendNicLB[1]: Creating...[0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[0]: Creating...[0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[1]: Creating...[0m[0m
    [0m[1mazurerm_network_interface_backend_address_pool_association.webserverTfPackerBackendNicLB[1]: Creation complete after 1s [id=/subscriptio
    ns/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/rg-dev-westeurope-001/providers/Microsoft.Network/networkInterfaces/nic-dev-westeurope-00
    1-server2-udacity/ipConfigurations/ipconfig-dev-westeurope-001-udacity|/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/rg-dev
    -westeurope-001/providers/Microsoft.Network/loadBalancers/lbi-dev-westeurope-001/backendAddressPools/backend-address-dev-westeurope-001][0m
    [0m[1mazurerm_network_interface_backend_address_pool_association.webserverTfPackerBackendNicLB[0]: Creation complete after 1s [id=/subscriptio
    ns/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/rg-dev-westeurope-001/providers/Microsoft.Network/networkInterfaces/nic-dev-westeurope-00
    1-server1-udacity/ipConfigurations/ipconfig-dev-westeurope-001-udacity|/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6b80433/resourceGroups/rg-dev
    -westeurope-001/providers/Microsoft.Network/loadBalancers/lbi-dev-westeurope-001/backendAddressPools/backend-address-dev-westeurope-001][0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[1]: Still creating... [10s elapsed][0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[0]: Still creating... [10s elapsed][0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[0]: Still creating... [20s elapsed][0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[1]: Still creating... [20s elapsed][0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[0]: Still creating... [30s elapsed][0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[1]: Still creating... [30s elapsed][0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[1]: Still creating... [40s elapsed][0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[0]: Still creating... [40s elapsed][0m[0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[0]: Creation complete after 48s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6
    b80433/resourceGroups/rg-dev-westeurope-001/providers/Microsoft.Compute/virtualMachines/linux-vm-dev-westeurope-001-0][0m
    [0m[1mazurerm_linux_virtual_machine.webserverTfPackerLinuxVM[1]: Creation complete after 48s [id=/subscriptions/78edc3c6-6f4c-46b2-8fed-9503d6
    b80433/resourceGroups/rg-dev-westeurope-001/providers/Microsoft.Compute/virtualMachines/linux-vm-dev-westeurope-001-1][0m
    [0m[1m[32m
    Apply complete! Resources: 15 added, 0 changed, 0 destroyed.
    [0m
    Deploy steps for the TF resources finished successfully !
    ```
    ![Terraform-Resources](https://i.imgur.com/dvVddUa.png)

8. DeleteResource.ps1 Output
    ![Terraform-Resources](https://i.imgur.com/NDtEtvh.png)


### References
1. https://docs.microsoft.com/en-us/azure/governance/policy/how-to/programmatically-create
2. https://docs.microsoft.com/en-us/azure/virtual-machines/linux/build-image-with-packer
3. https://docs.microsoft.com/en-us/azure/governance/policy/concepts/definition-structure
4. https://docs.microsoft.com/en-us/learn/modules/authenticate-apps-with-managed-identities/?WT.mc_id=udacity_learn-wwl
5. https://docs.microsoft.com/en-us/azure/developer/terraform/create-vm-cluster-with-infrastructure
6. https://medium.com/@yoursshaan2212/terraform-to-provision-multiple-azure-virtual-machines-fab0020b4a6e
7. https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-load-balancer
8. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/lb
9. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/network_security_rule
10. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/linux_virtual_machine
11. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/managed_disk
12. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/availability_set
13. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/network_interface_backend_address_pool_association
14. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/lb_backend_address_pool
15. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/public_ip
16. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/network_interface
17. https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_network
18. https://docs.microsoft.com/en-us/cli/azure/format-output-azure-cli


