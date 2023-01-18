--- 
order: 100
---

# General

Event Hubs are a great event streaming and messaging system, connecting consumers with producers
in a decoupled manner. What is needed for production-grade systems is a way to monitor if
consumers can keep up with consumers or if they are falling behind. The term 'lag' describes the 
difference between what has already been written and what has been read in the scope of
a consumer group. Event Hubs provide no built-in metric for monitoring this lag.

`event-hub-lag-metrics` bridges this gap and makes lag metrics available in Azure Monitor / Application
Insights. Based on these metrics, you can define alerting rules, such as thresholds for when the lag 
exceeds a critical value.
