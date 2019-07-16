# Azure Resource Graph Examples

This is a collection of helpful queries. Generic queries are contained in this document while specialized categories are shown below. 

[Virtual Machine Queries](Virtual%20Machine%20Queries.md)

[Virtual Network Queries](Virtual%20Network%20Queries.md)

[Storage Account Queries](Storage%20Account%20Queries.md)

[App Insights Queries](App%20Insights%20Queries.md)

[Azure SQL Queries](Azure%20SQL%20Queries.md)


# az graph query --help
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

## List resources with a specific tag value
az graph query -q "where tags.owner=~'CloudOps & SecurityEng' | project name, subscriptionId, tags"

## List all tag names
az graph query -q "project tags | summarize buildschema(tags)" --subscriptions xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## List owner tag with user name, return project name (resource)
az graph query -q "where tags.owner =~'<exact Value>' | project name"
