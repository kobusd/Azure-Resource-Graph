# Storage Account Queries

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
