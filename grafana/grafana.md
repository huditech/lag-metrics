--- 
order: 60
label: Grafana
---

In order to visualize lag metrics on a Grafana dashboard, do the following:

In your Grafana or Grafana cloud installation, add an Azure Monitor datasource (if you do not have one already):

![List datasources](./grafana1.png)

![Add Azure Monitor datasource](./grafana2.png)

Now, configure the datasource as described in the [Grafana documentation](https://grafana.com/docs/grafana/latest/datasources/azure-monitor/).

You can now create a dashboard. Select the newly added datasource. In `Service` choose `Logs` (this is named a little confusingly).

![Select Logs](./grafana3.png)

In `Resources` select your Log Analytics Workspace.

![Select Workspace](./grafana4.png)

Enter the following KQL query:

```kql
AppMetrics
| where Name == 'EventHubLag'
| extend eventHub = tostring(Properties['EventHub']), consumerGroup = tostring(Properties['ConsumerGroup'])
| summarize Lag=sum(Sum) by TimeGenerated, eventHub, consumerGroup
| order by TimeGenerated asc 
```

You should now see the lags of the different Event Hubs and consumer groups visualized:

![Grafana dashboard](./grafana5.png)
