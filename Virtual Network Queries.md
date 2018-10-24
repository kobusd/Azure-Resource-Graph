# Virtual Network Queries

## vnets/subnets without NSG 
az graph query -q "where type =~ 'Microsoft.Network/virtualNetworks' | extend nsg = tostring(properties.subnets[0].properties.networkSecurityGroup)| where isempty(nsg) | project name" 

## Count vnets 
az graph query -q "where type =~ 'Microsoft.Network/virtualNetworks' | summarize count()"

## vnet address space
az graph query -q "where type =~ 'Microsoft.Network/virtualNetworks' | project name, properties.addressSpace.addressPrefixes[0]"
