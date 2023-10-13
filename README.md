# aks_DES_issue
## common issue when implementing AKS with Disk encryption set

When deploying the pod we see this error:

Warning  FailedAttachVolume  80s (x3 over 5m25s)  attachdetach-controller  AttachVolume.Attach failed for volume 'pvc-5a88ad26-9d6e-44e9-bf8c-9494e5c6a8c2' : rpc error: code = Internal desc = Attach volume /subscriptions/xxxxxxxxx/resourceGroups/mc_xxxxxxx/providers/Microsoft.Compute/disks/pvc-xxxxxxxxx to instance aks-agentpool-vmss00000 failed with could not find disk(/subscriptions/xxxxxxxx/resourcegroups/mc_xxxxxxxx/providers/microsoft.compute/disks/pvc-xxxxxxxx) *in current disk list(len: 0) nor in diskMap(map[])*

This can mean that the disk is using Disk Encryption Set and that Disk Encryption Set could be in another resource group.

According to [Microsoft Documentation][https://learn.microsoft.com/en-us/azure/aks/azure-disk-customer-managed-keys#encrypt-your-aks-cluster-data-disk] we need to give permission to the AKS cluster to access the DES resource group.

> [!IMPORTANT]  
> Ensure you have the proper AKS credentials. The managed identity needs to have contributor access to the resource group where the diskencryptionset is deployed. Otherwise, you'll get an error suggesting that the managed identity does not have permissions.

One way to mitigate this you can follow this example or you can apply other layers of granularity if you need

```bash
az role assignment create --assignee AKS_MSI_ID --scope DES_RESOURCE_ID --role Reader
```
