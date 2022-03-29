1. Create a new cluster configured with azure cni in the given vnet. i.e. Refer to this documentation: https://doc.microsoft.com/en-us/azure/aks/configure-azure-cns and use the 'Configure networking - CLI' part

i.e run the commands:
```sh
az network vnet subnet list \
  --resource-group teamResources \
  --vnet-name vnet \
  --query "[0].id" --output tsv
```
Which will return the subnet-id that you need for the second command

```sh
az aks create \
  --resource-group teamResources \
  --name tripViewerCluster \
  --network-plugin azure \
  --vnet-subnet-id <subnet-id> \
  --dns-service-ip 10.20.0.10 \
  --service-cidr 10.2.0.0/24 \
  --generate-ssh-keys 
```