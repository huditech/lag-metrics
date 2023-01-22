---
order: 80
---

The following metrics are published to Application Insights:

## Lag by Topic, Consumer Group and Partition

The name of the metrics is `Event Hub Partition Lag`. The value of the metric is the number
of messages that have not yet been processed in the context of a certain event hub, partition and consumer
group.

Custom dimensions of this metric are:

* `Event Hub Namespace`: Name of the Event Hub Namespace.
* `Event Hub`: Name of the Event Hub.
* `Consumer Group`: Name of the consumer group.
* `Partition ID`: Numerical identifier of the partition. If you have 4 partitions, values are 0, 1, 2 and 3.

This metric provides maximum flexibility as it includes information about partitions.

The lag for a particular Event Hub and Consumer Group can be extracted using the following Log Analytics / KQL query:

```kusto
customMetrics
| where name == 'Event Hub Consumer Lag'
| extend eventHub=tostring(customDimensions['Event Hub'])
| extend tostring(consumerGroup=customDimensions['Consumer Group'])
| extend tostring(partitionId=customDimensions['Partition Id'])
| where consumerGroup == '$Default' and eventHub == 'example-event-hub'
| summarize lag=sum(value) by timestamp
```

## Lag by Topic and Consumer Group

The name of the metrics is `Event Hub Lag`. The value of the metric is the number
of messages that have not yet been processed in the context of a certain event hub and consumer
group.  The values are already summed up over all partitions of the Event Hub. This level of
detail is usually sufficient, as the overall lag across all partitions is most relevant.

Custom dimensions of this metric are:

* `Event Hub Namespace`: Name of the Event Hub Namespace.
* `Event Hub`: Name of the Event Hub.
* `Consumer Group`: Name of the consumer group.

## Viewing 
