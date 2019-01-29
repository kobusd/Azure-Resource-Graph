# Azure SQL Queries

## [Microsoft.Sql resource types](https://docs.microsoft.com/en-us/azure/templates/microsoft.sql/allversions)

## count Azure SQL servers
az graph query -q "where type =~ 'Microsoft.Sql/servers' | summarize count()" 

## count Azure SQL databases
az graph query -q "where type =~ 'Microsoft.Sql/servers/databases' | summarize count()"
