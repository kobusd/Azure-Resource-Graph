# App Insights Queries

## count all alerts
az graph query -q "where type =~ 'microsoft.insights/alertrules' | summarize count()"

## list all alerts
az graph query -q "where type =~ 'microsoft.insights/alertrules' | project name"
