# Virtual Machine Queries
## [Microsoft.Compute resource types](https://docs.microsoft.com/en-us/azure/templates/microsoft.compute/allversions)

##Show all virtual machines ordered by name in descending order
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## Show first five virtual machines by name and their OS type
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"

## Count virtual machines by OS type
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"

## Count Windows virtual machnines by offer(e.g. Server or Windows 10)
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and tostring(properties.storageProfile.osDisk.osType) =~ 'Windows'| summarize count() by tostring(properties.storageProfile.imageReference.offer)"

## Get VMSs capacity and size
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"

## Get VMSs by size with regex
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize matches regex 'Standard_DS[0-9]*_v2'| project name"

## Count VMs per location
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## Get count of VM sizes
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize vm_count = count() by tostring(properties.hardwareProfile.vmSize) | order by vm_count"

## Get count of VM sizes by region
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize vm_count = count() by location, vmsize = tostring(properties.hardwareProfile.vmSize) | order by vm_count" --o table

## Get all the Classic VMs
az graph query -q "where type =~ 'microsoft.classiccompute/virtualmachines'| project name, subscriptionId"

## Virtual machines matched by regex
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^cvallapimmo(.\*)[0-9]+$' | project name | order by name asc"

##Virtual machine size by resourcegroup
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines'| where location =~ 'eastus2' | summarize vm_count = count(properties.hardwareProfile.vmSize) by resourceGroup | order by vm_count"

# Display all virtual machines that starts with TEST and ends with number.
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' | where name matches regex @'^TEST(.*)[0-9]+$'|project name"

# Display all storage accounts that have the option to “Allow Access from all networks”
az graph query -q "where type =~ 'microsoft.storage/storageaccounts' | where aliases['Microsoft.Storage/storageAccounts/networkAcls.defaultAction'] =='Allow'| summarize count()"

# Display vmSize count for each resource group
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines'| summarize count() by resourceGroup, tostring(properties.hardwareProfile.vmSize) | sort by resourceGroup asc" --output table

# Get IP address of VMs
az graph query -q "resources | where type == 'microsoft.compute/virtualmachines' | project vm_name=name, nic_id=tostring(properties.networkProfile.networkInterfaces[0].id), vm_rg=resourceGroup| join (resources | where type == 'microsoft.network/networkinterfaces' | project nic_id=id, ip=tostring(properties.ipConfigurations[0].properties.privateIPAddress)) on nic_id| project vm=vm_name, ip, resourceGroup=vm_rg" --first 10
