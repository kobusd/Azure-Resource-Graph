# Storage Account Queries
## [Microsoft.Storage resource types](https://docs.microsoft.com/en-us/azure/templates/microsoft.storage/allversions)

## Show resources that contain storage
az graph query -q "where type contains 'storage' | distinct type"

## number of storage accounts
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | summarize count()"

## count storage accounts blob encryption enabled
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where properties.encryption.services.blob.enabled==true | summarize count()"

## list storage account blob not encrypted
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where properties.encryption.services.blob.enabled==false"

## list strorage account file not encrypted
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where properties.encryption.services.file.enabled==false"

## list Unattached managed disks
az graph query -q "where type =~ 'Microsoft.Compute/disks' and properties.diskState =~'Unattached' | project name, subscriptionId, resourceGroup, properties.diskState"

## List all storage accounts with specific tag value
#*** don't know how this works

az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
