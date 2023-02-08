--- 
order: 90
label: Installation via Bicep
---

## Writing the Bicep file(s)

The following snippet installs _Lag Metrics_ via [Bicep](https://github.com/Azure/bicep). You can find
a minimal, but complete example in a [sample repo on GitHub](https://github.com/huditech/lag-metrics-sample).

```bicep
resource managedApp 'Microsoft.Solutions/applications@2021-07-01' = {
  name: 'lag-metrics'
  kind: 'marketplace'
  plan: {
    name: 'standard'
    product: 'lag-metrics'
    publisher: 'tbd'
    version: '1.0.0'
  }
  location: location
  properties: {
    managedResourceGroupId: '${resourceGroup().id}-eh-lag-metrics'
    parameters: {
      eventHubConnectionString: {
        value: eventHubConnectionString
      }
      storageAccountConnectionString: {
        value: storageConnectionString
      }
      applicationInsightsConnectionString: {
        value: appInsights.properties.ConnectionString
      }
      offsetContainerName: {
        value: storageAccount::blobService::eventHubCheckpointsContainer.name
      }
    }
  }
}
```

This requires the Event Hub connection string, the storage account connection string, 
the application insights connection string and the container name that holds checkpoints/offsets.
See the following sections on how these might be defined via Bicep and how to extract
the connection strings.


### Event Hubs

```bicep
// This connection string is used as input for lag-metrics
var eventHubConnectionString = listKeys(eventHubs::authRule.id, eventHubs::authRule.apiVersion).primaryConnectionString

resource eventHubs 'Microsoft.EventHub/namespaces@2021-06-01-preview' = {
  name: 'my-event-hub'
  location: location
  sku: {
    name: 'Basic'
    tier: 'Basic'
    capacity: 1
  }
  properties: {
  }

  // This is the Event Hub that you wish to monitor. There can be several of course.
  resource eventHub 'eventhubs@2021-06-01-preview' = {
    name: 'example-event-hub'
    properties: {
      messageRetentionInDays: 1
      partitionCount: 4
      status: 'Active'
    }
  }

  // The authorization rule must have Manage, Listen and Send rights.
  resource authRule 'authorizationRules@2021-11-01' = {
    name: 'metricsAuthRule'
    properties: {
      rights: [
        'Manage'
        'Listen'
        'Send'
      ]
    }
  }
}
```

### Storage Account

```bicep
// This connection string is used as input for lag-metrics
var storageConnectionString = 'DefaultEndpointsProtocol=https;AccountName=${storageName};AccountKey=${storageKey};EndpointSuffix=${environment().suffixes.storage}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: storageName
  kind: 'StorageV2'
  location: location
  sku: {
    name: 'Standard_LRS'
  }

  resource blobService 'blobServices@2021-04-01' = {
    name: 'default'
    
    // This is the container that you use in your Event Hub Consumers to store checkpoints/offsets
    resource eventHubCheckpointsContainer 'containers@2021-04-01' = {
      name: 'event-hub-checkpoints'
    }
  }
}
```

### Application Insights

```bicep
resource appInsights 'Microsoft.Insights/components@2020-02-02' = {
  name: applicationInsightsName
  kind: 'web'
  location: location
  properties: {
    Application_Type: 'web'
  }
}
```

## Performing the Deployment

You can start the deployment using:

```
az deployment group create \
  --resource-group lag-monitor-test \
  --subscription xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx \
  -f template.bicep 
```

More information on this can be found [here](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/deploy-cli).
