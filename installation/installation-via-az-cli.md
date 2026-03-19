--- 
order: 80
label: Installation via az cli
---

## Azure CLI script for Lag Metrics (native Event Hub API)

The following snippet installs _lag-metrics_ via [az cli](https://github.com/Azure/azure-cli).

```
az managedapp create \
  --name eh-lag-metrics \
  --location westeurope \
  --kind marketplace \
  --resource-group YOUR_RG \
  --managed-rg-id /subscriptions/YOUR_SUBSCRIPTION/resourceGroups/eh-lag-metrics-managed-resource-group \
  --plan-product lag-metrics \
  --plan-name standard \
  --plan-version 2.1.1 \
  --plan-publisher 'huditechughaftungsbeschrnkt1673457598758' \
  --parameters '{"eventHubConnectionString": {"value": "CONNECTION_STRING"}, "storageAccountConnectionString": {"value": "CONNECTION_STRING"}, "applicationInsightsConnectionString": {"value": "CONNECTION_STRING"}, "offsetContainerName": {"value": "event-hub-offsets"}}'
```

Note that you have to customize this by specifying your subscription, resource group and the
various connection strings.

## Azure CLI script for Lag Metrics (Kafka API)

The following snippet installs _lag-metrics_ via [az cli](https://github.com/Azure/azure-cli).

```
az managedapp create \
  --name eh-lag-metrics \
  --location westeurope \
  --kind marketplace \
  --resource-group YOUR_RG \
  --managed-rg-id /subscriptions/YOUR_SUBSCRIPTION/resourceGroups/eh-lag-metrics-managed-resource-group \
  --plan-product lag-metrics \
  --plan-name standard \
  --plan-version 2.1.1 \
  --plan-publisher 'huditechughaftungsbeschrnkt1673457598758' \
  --parameters '{"eventHubConnectionString": {"value": "CONNECTION_STRING"}, useKafkaMode": {"value": true}, "applicationInsightsConnectionString": {"value": "CONNECTION_STRING"}}'
```

Note that you have to customize this by specifying your subscription, resource group and the
various connection strings.
