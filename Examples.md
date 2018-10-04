# Azure Resource Graph Examples
**Command**

    az graph query : Query the resources managed by Azure Resource Manager.
        See https://aka.ms/AzureResourceGraph-QueryLanguage to learn more about query language and
        browse examples.

**Arguments**

    --graph-query --q -q [Required] : Resource Graph query to execute.
    --first                         : The maximum number of objects to return. Accepted range:
                                      1-5000.  Default: 100.
    --skip                          : Ignores the first N objects and then gets the remaining
                                      objects.
    --subscriptions -s              : List of subscriptions to run query against. By default all
                                      accessible subscriptions are queried.

**Global Arguments**

    --debug                         : Increase logging verbosity to show all debug logs.
    --help -h                       : Show this help message and exit.
    --output -o                     : Output format.  Allowed values: json, jsonc, table, tsv, yaml.
                                      Default: json.
    --query                         : JMESPath query string. See http://jmespath.org/ for more
                                      information and examples.
    --subscription                  : Name or ID of subscription. You can configure the default
                                      subscription using `az account set -s NAME_OR_ID`.
    --verbose                       : Increase logging verbosity. Use --debug for full debug logs.

**Examples**

    Query resources requesting a subset of resource fields.
        az graph query -q "project id, name, type, location, tags"


    Query resources with field selection, filtering and summarizing.
        az graph query -q "project id, type, location | where type =~
        'Microsoft.Compute/virtualMachines' | summarize count() by location | top 3 by count_"


    Request a subset of results, skipping 20 items and getting the next 10.
        az graph query -q "where type =~ "Microsoft.Compute" | project name, tags" --first 10 --skip
        20


    Choose subscriptions to query.
        az graph query -q "where type =~ "Microsoft.Compute" | project name, tags" --subscriptions
        11111111-1111-1111-1111-111111111111, 22222222-2222-2222-2222-222222222222

## Count Azure resources

    az graph query -q "summarize count()"

## List resources sorted by name

    az graph query -q "project name, type, location | order by name asc" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## Show all virtual machines ordered by name in descending order
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## Show first five virtual machines by name and their OS type
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"

## Count virtual machines by OS type
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"

## Show resources that contain storage
az graph query -q "where type contains 'storage' | distinct type"

## List all public IP addresses
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | project properties.ipAddress | limit 100" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## Count resources that have IP addresses configured by subscription
az graph query -q "where type contains 'publicIPAddresses' and properties.ipAddress != '' | summarize count () by subscriptionId"

## List resources with a specific tag value
az graph query -q "where tags.owner=~'CloudOps & SecurityEng' | project name, subscriptionId, tags"

## List all storage accounts with specific tag value
#*** don't know how this works
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"

## Get VMSS capacity and size
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"

## Get VMSS capacity and size
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"

## List all tag names
az graph query -q "project tags | summarize buildschema(tags)" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## Virtual machines matched by regex

az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^cvallapimmo(.\*)[0-9]+$' | project name | order by name asc"

## Count VMs per location
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location" --subscriptions 40c4df1f-a6bb-44ba-8329-92e057f247c6

## Get count of VM sizes
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize vm_count = count() by tostring(properties.hardwareProfile.vmSize) | order by vm_count"
