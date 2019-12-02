# Virtual Network Queries
# [Microsoft.Network resource types](https://docs.microsoft.com/en-us/azure/templates/microsoft.network/allversions)

# vnets/subnets without NSG 
az graph query -q "where type =~ 'Microsoft.Network/virtualNetworks' | extend nsg = tostring(properties.subnets[0].properties.networkSecurityGroup)| where isempty(nsg) | project name" 

## Count vnets 
az graph query -q "where type =~ 'Microsoft.Network/virtualNetworks' | summarize count()"

## vnet address space
az graph query -q "where type =~ 'Microsoft.Network/virtualNetworks' | project name, properties.addressSpace.addressPrefixes[0]"

## vnet address spaces to json file
az graph query -q "where type =~ 'Microsoft.Network/virtualNetworks'| extend addressSpace =properties.addressSpace.addressPrefixes[0] | project name, subscriptionId, resourceGroup, addressSpace " >"vnet address ranges.json"

## List all public IP addresses
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## get all public ip addresses and write to file
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress" --first 5000 >> c:\output\AzureIPaddresses.txt

## Count resources that have IP addresses configured by subscription
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"

## determine SKU allowances for VM with a specific name pattern
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | where name contains '<pattern>' | summarize vm_count = count() by tostring(properties.hardwareProfile.vmSize) |order by vm_count"

## get peerings with disconnected status
for /L %a in (0,1,10) Do az graph query -q "where type =~ 'Microsoft.Network/virtualNetworks' and properties.virtualNetworkPeerings[%a].properties.peeringState == 'Disconnected'| project name" 

##ARG EXPLORER: VNETs with connected devices
resources
|where type == "microsoft.network/virtualnetworks"
|where isnotnull(properties.subnets)
|mvexpand subnet = properties.subnets
|where tostring(subnet.properties.ipConfigurations) != ""
|distinct name

##ARG EXPLORER: VNET prefix for subscriptions
Resources
| where type == 'microsoft.network/virtualnetworks'
| project vnet_name = name, vnet_id = id, vnet_prefix = properties.addressSpace.addressPrefixes, subscriptionId
| join kind = inner (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project SubName, vnet_name, vnet_prefix 
| order by SubName asc

##ARG EXPLORER: ASGs connected to NICs
resources
|where type == 'microsoft.network/networkinterfaces' and properties.ipConfigurations[0].properties.applicationSecurityGroups != '' 
| project name, asg = tostring(properties.ipConfigurations[0].properties.applicationSecurityGroups[0].id) 
| order by asg
