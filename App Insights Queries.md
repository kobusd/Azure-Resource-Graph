# App Insights Queries
## [microsoft.insights resource types](https://docs.microsoft.com/en-us/azure/templates/microsoft.insights/allversions)

## count all alerts
az graph query -q "where type =~ 'microsoft.insights/alertrules' | summarize count()"

## list all alerts
az graph query -q "where type =~ 'microsoft.insights/alertrules' | project name"
