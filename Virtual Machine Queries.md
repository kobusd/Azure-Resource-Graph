# Virtual Machine Queries
##Show all virtual machines ordered by name in descending order
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## Show first five virtual machines by name and their OS type
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"

## Count virtual machines by OS type
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"

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
