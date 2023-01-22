--- 
order: 100
---

# General

[Azure Event Hubs](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-about) are a great event streaming 
and messaging system, connecting consumers with producers in a decoupled manner. 

In production-grade systems it must be monitored if consumers can keep up with producers or if they are falling behind. 
In traditional messaging systems like Azure Service Bus or Apache ActiveMQ, this is monitored via **queue sizes**. 
In event streaming systems like Apache Kafka or Azure Event Hubs, it's a bit more complex. Here, the term **lag** describes 
the difference between what has already been written and what has been read in the scope of a consumer group. 

Event Hubs provide no built-in metric for monitoring this lag.

_event-hub-lag-metrics_ bridges this gap and makes lag metrics available in Azure Monitor / Application
Insights. Based on these metrics, you can define alerting rules, such as thresholds for when the lag 
exceeds a critical value.
