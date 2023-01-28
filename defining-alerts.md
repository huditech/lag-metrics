--- 
order: 70
label: Defining Alerts
---

_Lag Metrics_, as the name implies, simply publishes metrics. You can 
use regular [Azure Monitor alerts](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-overview) 
to be notified when an alert exceeds a certain threshold.

Here is an example of defining the alert using Bicep:

```bicep
resource highLagAlert 'Microsoft.Insights/scheduledQueryRules@2021-08-01' = {
  name: 'eefbe064-99d8-11ed-a8fc-0242ac120002'
  location: location
  kind: 'LogAlert'
  properties: {
    displayName: 'High lag'
    description: 'Consumer are falling behind'
    autoMitigate: true
    severity: 1
    enabled: true
    evaluationFrequency: 'PT5M'
    windowSize: 'PT5M'
    scopes: [
      // Log Analytics scope example
      '/subscriptions/YOUR_SUBSCRIPTION/resourcegroups/YOUR_RG/providers/microsoft.operationalinsights/workspaces/YOUR_WORKSPACE'
     ]
     skipQueryValidation: false
     actions: {
       actionGroups: [
         '/subscriptions/YOUR_SUBSCRIPTION/resourceGroups/YOUR_RG/providers/Microsoft.Insights/actiongroups/YOUR_ACTION_GROU'
     ]
  }
  criteria: {
    allOf: [
      {
        query: 'customMetrics | where ...'
        timeAggregation: 'Average'
        metricMeasureColumn: 'value'
        resourceIdColumn: null
        operator: 'GreaterThan'
        threshold: 500
        dimensions: [
          {
            name: 'xxx'
            operator: 'Include'
            values: [
              '*'
            ]
          }
        ]
        failingPeriods: {
          minFailingPeriodsToAlert: 1
          numberOfEvaluationPeriods: 1
        }
      }
    ]
  }
}
```
 You can find a complete example including an alert [on GitHub](https://github.com/huditech/event-hub-lag-metrics-sample)
