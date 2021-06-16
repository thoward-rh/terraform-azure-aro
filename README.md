# terraform-azure-aro
This code will first standup VNET, Bastion and firewall for the Azure Private network.

This can be used to run ARO install for internal network.

# Creating the infrastructure on Azure.
IMPORTANT: First ensure you are logged into the right Azure account with the right access.

`# az login`

Initialise your terraform directory.

`# terraform init`

Run the plan

`# terraform plan`

Apply to the infra

`# terraform apply`

After the install, create your private key and use it to access the Bastion host which was created in the terraform apply.

The result of applying the terraform will be the following:
* 1 VNET and 4 subnets (mySubnet, myControlPlaneSubnet, myWorkerSubnet, AzureFirewallSubnet)
* 1 firewall with static IP address
* Firewall rules to allow internal subnets access to the addresses required to install OpenShift (ie quay.io)
* 1 routing table which adds the worker and control plane subnets to default routing to the firewall. User Defined Routing (UDR)
* 1 CentOS VM which we will use for Bastion, this also has a dynamic public IP address (may change to use firewall later)


# Create cluster variables

```bash

LOCATION=eastus                 # the location of your cluster
RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
CLUSTER=cluster                 # the name of your cluster
```

# Create cluster

```bash
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet

```