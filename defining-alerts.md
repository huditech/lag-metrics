--- 
order: 70
label: Defining Alerts
---

_Lag Metrics_, as the name implies, simply publishes metrics. You can 
use regular [Azure Monitor alerts](https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-overview) 
to be notified when an alert exceeds a certain threshold.

Here is an example of defining the alert using Bicep, including an action group:

```bicep
resource highLagAlert 'microsoft.insights/scheduledqueryrules@2022-06-15' = {
  name: alertRuleName
  location: 'EastUS'
  properties: {
    displayName: 'Lag Alert'
    severity: 3
    enabled: true
    evaluationFrequency: 'PT1M'
    scopes: [
      appInsights.id
    ]
    targetResourceTypes: [
      'microsoft.insights/components'
    ]
    windowSize: 'PT5M'
    criteria: {
      allOf: [
        {
          query: 'customMetrics\n| where name == \'Event Hub Partition Lag\'\n| extend eventHub=tostring(customDimensions[\'Event Hub\'])\n| extend tostring(consumerGroup=customDimensions[\'Consumer Group\'])\n| extend tostring(partitionId=customDimensions[\'Partition Id\'])\n| summarize lag=sum(value) by timestamp, eventHub, consumerGroup\n\n'
          timeAggregation: 'Maximum'
          metricMeasureColumn: 'lag'
          // By specifying dimensions in this way all combinations of eventHub and consumerGroups are compared to the threshold.
          // See: https://learn.microsoft.com/en-us/azure/azure-monitor/alerts/alerts-metric-multiple-time-series-single-rule#multiple-dimensions-multi-dimension
          dimensions: [
            {
              name: 'eventHub'
              operator: 'Include'
              values: [
                '*'
              ]
            }
            {
              name: 'consumerGroup'
              operator: 'Include'
              values: [
                '*'
              ]
            }
          ]
          operator: 'GreaterThan'
          threshold: 100
          failingPeriods: {
            numberOfEvaluationPeriods: 1
            minFailingPeriodsToAlert: 1
          }
        }
      ]
    }
    autoMitigate: true
    actions: {
      actionGroups: [
        actionGroup.id
      ]
    }
  }
}

resource actionGroup 'microsoft.insights/actionGroups@2022-06-01' = {
  name: 'lagActionGroup'
  location: 'Global'
  properties: {
    groupShortName: 'Lag'
    enabled: true
    emailReceivers: [
      {
        name: 'email'
        emailAddress: 'me@example.com'
        useCommonAlertSchema: false
      }
    ]
  }
}
```
 You can find a complete example including an alert [on GitHub](https://github.com/huditech/lag-metrics-sample).
