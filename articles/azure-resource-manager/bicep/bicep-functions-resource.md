---
title: Bicep-Funktionen – Ressourcen
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei zum Abrufen von Werten zu Ressourcen verwendet werden.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: a14019e3cdf595efe0a32a7021333aa5b4f6a6b1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087417"
---
# <a name="resource-functions-for-bicep"></a>Ressourcenfunktionen für Bicep

In diesem Artikel werden die Bicep-Funktionen zum Abrufen von Ressourcenwerten beschrieben.

Informationen zum Abrufen von Werten aus der aktuellen Bereitstellung finden Sie unter [Funktionen für Bereitstellungswerte](./bicep-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Gibt die Ressourcen-ID für eine [Erweiterungsressource](../management/extension-resource-types.md) zurück. Hierbei handelt es sich um einen Ressourcentyp, der auf eine andere Ressource angewendet wird, um deren Funktionen zu erweitern.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

Die extensionResourceId-Funktion ist zwar in Bicep-Dateien verfügbar, Sie werden sie aber in der Regel nicht benötigen. Verwenden Sie stattdessen den symbolischen Namen für die Ressource, und greifen Sie auf die `id`-Eigenschaft zu.

Das Standardformat der Ressourcen-ID, die von dieser Funktion zurückgegeben wird, ist wie folgt:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Das „scope“-Segment ist je nach erweiterter Ressource unterschiedlich.

Wenn die Erweiterungsressource auf eine **Ressource** angewendet wird, hat die zurückgegebene Ressourcen-ID das folgende Format:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wenn die Erweiterungsressource auf eine **Ressourcengruppe** angewendet wird, ist das Format wie folgt:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wenn die Erweiterungsressource auf ein **Abonnement** angewendet wird, ist das Format wie folgt:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Wenn die Erweiterungsressource auf eine **Verwaltungsgruppe** angewendet wird, ist das Format wie folgt:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Eine benutzerdefinierte, für eine Verwaltungsgruppe bereitgestellte Richtliniendefinition wird als Erweiterungsressource implementiert. Stellen Sie die folgende Bicep-Datei für eine Verwaltungsgruppe bereit, um eine Richtlinie zu erstellen und zuzuweisen.

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

---

Integrierte Richtliniendefinitionen sind Ressourcen auf Mandantenebene. Ein Beispiel für die Bereitstellung einer integrierten Richtliniendefinition finden Sie unter [tenantResourceId](#tenantresourceid).

## <a name="getsecret"></a>getSecret

`keyVaultName.getSecret(secretName)`

Gibt ein Geheimnis aus Azure Key Vault zurück. Die `getSecret`-Funktion kann nur für eine `Microsoft.KeyVault/vaults`-Ressource aufgerufen werden. Verwenden Sie diese Funktion, um ein Geheimnis an einen sicheren Zeichenfolgenparameter eines Bicep-Moduls zu übergeben. Die Funktion kann nur mit einem Parameter verwendet werden, der über den `@secure()`-Decorator verfügt.

Für den Schlüsseltresor muss `enabledForTemplateDeployment` auf `true` gesetzt sein. Der Benutzer, der die Bicep-Datei bereitstellt, muss über Zugriff auf das Geheimnis verfügen. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bicep-Bereitstellung](key-vault-parameter.md).

Ein [Namespacequalifizierer](bicep-functions.md#namespaces-for-functions) ist nicht erforderlich, da die Funktion mit einem Ressourcentyp verwendet wird.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | Beschreibung |
|:--- |:--- |:--- |:--- |
| secretName | Ja | Zeichenfolge | Der Name des Geheimnisses, das in einem Schlüsseltresor gespeichert ist. |

### <a name="return-value"></a>Rückgabewert

Der Geheimniswert für den Geheimnisnamen.

### <a name="example"></a>Beispiel

Die folgende Bicep-Datei wird als Modul verwendet.  Sie verfügt über einen *adminPassword*-Parameter, der mit dem `@secure()`-Decorator definiert ist.

```bicep
param sqlServerName string
param adminLogin string

@secure()
param adminPassword string

resource sqlServer 'Microsoft.Sql/servers@2020-11-01-preview' = {
  ...
}
```

Die folgende Bicep-Datei verwendet obige Bicep-Datei als Modul. Die Bicep-Datei verweist auf einen vorhandenen Schlüsseltresor, ruft die `getSecret`-Funktion auf, um das Schlüsseltresorgeheimnis abzurufen, und übergibt den Wert dann als Parameter an das Modul.

```bicep
param sqlServerName string
param adminLogin string

param subscriptionId string
param kvResourceGroup string
param kvName string

resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`resourceName.list([apiVersion], [functionValues])`

Sie können eine Listenfunktion für jeden Ressourcentyp mit einem Vorgang aufrufen, der mit `list` beginnt. Häufig werden `list`, `listKeys`, `listKeyValue` und `listSecrets` verwendet.

Die Syntax für diese Funktion variiert je nach dem Namen des Auflistungsvorgangs. Die zurückgegebenen Werte variieren auch je nach Vorgang. Bicep unterstützt derzeit keine Vervollständigungen und Validierungen für `list*`-Funktionen.

**Ab Bicep-Version 0.4.412** rufen Sie die Listenfunktion mit dem [Accessor-Operator](operators-access.md#function-accessor) auf. Beispiel: `stg.listKeys()`.

Ein [Namespacequalifizierer](bicep-functions.md#namespaces-for-functions) ist nicht erforderlich, da die Funktion mit einem Ressourcentyp verwendet wird.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| apiVersion |Nein |Zeichenfolge |Wenn Sie diesen Parameter nicht angeben, wird die API-Version für die Ressource verwendet. Stellen Sie nur dann eine benutzerdefinierte API-Version bereit, wenn Sie die Funktion mit einer bestimmten Version ausführen müssen. Verwenden Sie das Format **jjjj-mm-tt**. |
| functionValues |Nein |Objekt (object) | Ein Objekt, das über Werte für die Funktion verfügt. Geben Sie dieses Objekt nur für Funktionen an, die den Empfang eines Objekts mit Parameterwerten unterstützen – z.B. **listAccountSas** für ein Speicherkonto. Ein Beispiel für die Übergabe von Funktionswerten wird in diesem Artikel gezeigt. |

### <a name="valid-uses"></a>Gültige Verwendungen

Die list-Funktionen können in den Eigenschaften einer Ressourcendefinition verwendet werden. Verwenden Sie keine list-Funktionen, die im Abschnitt „outputs“ einer Bicep-Datei vertrauliche Informationen offenlegen. Ausgabewerte werden im Bereitstellungsverlauf gespeichert und könnten von einem böswilligen Benutzer abgerufen werden.

Wenn sie mit einer [iterativen Schleife](loops.md) verwendet werden, können Sie die list-Funktionen für `input` verwenden, da der Ausdruck der resource-Eigenschaft zugewiesen wird. Sie können sie nicht mit `count` verwenden, weil die Anzahl bestimmt werden muss, bevor die Listenfunktion aufgelöst wird.

Bei Verwendung einer **list**-Funktion mit einer Ressource mit bedingter Bereitstellung wird die Funktion auch dann ausgewertet, wenn die Ressource nicht bereitgestellt wird. Es wird eine Fehlermeldung angezeigt, wenn die **list**-Funktion auf eine nicht vorhandene Ressource verweist. Verwenden Sie den [Operator bedingter Ausdruck **?:**](./operators-logical.md#conditional-expression--), um sicherzustellen, dass die Funktion nur ausgewertet wird, wenn die Ressource bereitgestellt wird.

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt ist abhängig von der list-Funktion, die Sie verwenden, unterschiedlich. Die Funktion listKeys für ein Speicherkonto wird z.B. im folgenden Format zurückgegeben:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Andere list-Funktionen weisen andere Rückgabeformate auf. Um das Format einer Funktion anzuzeigen, geben Sie es wie in der Bicep-Beispieldatei dargestellt im Abschnitt „outputs“ an.

### <a name="list-example"></a>list-Beispiel

Im folgenden Beispiel wird ein Speicherkonto bereitgestellt und dann listKeys für dieses Speicherkonto aufgerufen. Der Schlüssel wird verwendet, wenn ein Wert für [Bereitstellungsskripte](../templates/deployment-script-template.md) festgelegt wird.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'dscript${uniqueString(resourceGroup().id)}'
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource dScript 'Microsoft.Resources/deploymentScripts@2019-10-01-preview' = {
  name: 'scriptWithStorage'
  location: location
  ...
  properties: {
    azCliVersion: '2.0.80'
    storageAccountSettings: {
      storageAccountName: stg.name
      storageAccountKey: stg.listKeys().keys[0].value
    }
    ...
  }
}
```

Das nächste Beispiel zeigt eine list-Funktion, die einen Parameter verwendet. In diesem Fall handelt es sich um die Funktion **listAccountSas**. Übergeben Sie ein Objekt für die Ablaufzeit. Die Ablaufzeit muss in der Zukunft liegen.

```bicep
param accountSasProperties object {
  default: {
    signedServices: 'b'
    signedPermission: 'r'
    signedExpiry: '2020-08-20T11:00:00Z'
    signedResourceTypes: 's'
  }
}
...
sasToken: stg.listAccountSas('2021-04-01', accountSasProperties).accountSasToken
```

### <a name="implementations"></a>Implementierungen

Die Verwendungsmöglichkeiten von list* werden in der folgenden Tabelle gezeigt.

| Ressourcentyp | Funktionsname |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/authorization-server/list-secrets) |
| Microsoft.ApiManagement/service/gateways | [listKeys](/rest/api/apimanagement/2021-04-01-preview/gateway/list-keys) |
| Microsoft.ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/identity-provider/list-secrets) |
| Microsoft.ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2021-04-01-preview/named-value/list-value) |
| Microsoft.ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/openid-connect-provider/list-secrets) |
| Microsoft.ApiManagement/service/subscriptions | [listSecrets](/rest/api/apimanagement/2021-04-01-preview/subscription/list-secrets) |
| Microsoft.AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/Spring | [listTestKeys](/rest/api/azurespringcloud/services/listtestkeys) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | listoutputfiles |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/get-build-source-upload-url) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/agentpools | listQueueStatus |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/2020-09-01/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/2020-09-01/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/2020-09-01/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2021-04-15/database-accounts/list-connection-strings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2021-04-15/database-accounts/list-keys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2021-04-15/notebook-workspaces/list-connection-info) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/compute/list-keys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/compute/list-nodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspaces/list-keys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy/remediations/listdeploymentsatresourcegroup) |
| Microsoft.RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/2021-04-01-preview/admin-keys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/2021-04-01-preview/query-keys/list-by-search-service) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/stable/namespaces-authorization-rules/list-keys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/preview/queues-authorization-rules/list-keys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20%E2%80%93%20authorization%20rules/list-keys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Synapse/workspaces/integrationRuntimes | [listauthkeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Um zu bestimmen, welche Ressourcentypen einen list-Vorgang aufweisen, stehen die folgenden Optionen zur Verfügung:

* Zeigen Sie die [REST-API-Vorgänge](/rest/api/) für einen Ressourcenanbieter an, und suchen Sie nach List-Vorgängen. Speicherkonten weisen z. B. den [listKeys-Vorgang](/rest/api/storagerp/storageaccounts) auf.
* Verwenden Sie das PowerShell-Cmdlet [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation). Im folgenden Beispiel werden alle List-Vorgänge für Speicherkonten abgerufen:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```

* Verwenden Sie den folgenden Azure-CLI-Befehl, um nur die Listenvorgänge zu filtern:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Bestimmt, ob ein Ressourcentyp Zonen für eine Region unterstützt.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| providerNamespace | Ja | Zeichenfolge | Der Ressourcenanbieternamespace für den Ressourcentyp, der auf Zonenunterstützung überprüft werden soll. |
| resourceType | Ja | Zeichenfolge | Der Ressourcentyp, der auf Zonenunterstützung überprüft werden soll. |
| location | Ja | Zeichenfolge | Die Region, die auf Zonenunterstützung überprüft werden soll. |
| numberOfZones | Nein | integer | Die Anzahl der zurückzugebenden logischen Zonen. Der Standardwert ist 1. Die Anzahl muss eine positive ganze Zahl zwischen 1 und 3 sein.  Verwenden Sie 1 für Ressourcen mit nur einer Zone. Für Ressourcen mit mehreren Zonen muss der Wert kleiner als oder gleich der Anzahl der unterstützten Zonen sein. |
| offset | Nein | integer | Der Offset von der beginnenden logischen Zone. Die Funktion gibt einen Fehler zurück, wenn Offset plus numberOfZones die Anzahl der unterstützten Zonen überschreitet. |

### <a name="return-value"></a>Rückgabewert

Ein Array mit den unterstützten Zonen. Wenn Sie die Standardwerte für Offset und numberOfZones verwenden, geben ein Ressourcentyp und eine Region, die Zonen unterstützen, das folgende Array zurück:

```json
[
    "1"
]
```

Wenn der `numberOfZones`-Parameter auf 3 festgelegt ist, wird Folgendes zurückgegeben:

```json
[
    "1",
    "2",
    "3"
]
```

Wenn der Ressourcentyp oder die Region keine Zonen unterstützt, wird ein leeres Array zurückgegeben.

```json
[
]
```

### <a name="pickzones-example"></a>Beispiel für pickZones

Die folgende Bicep-Datei zeigt drei Ergebnisse für die Verwendung der pickZones-Funktion.

```bicep
output supported array = pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')
output notSupportedRegion array = pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')
output notSupportedType array = pickZones('Microsoft.Cdn', 'profiles', 'westus2')
```

Die Ausgabe aus den vorherigen Beispielen gibt drei Arrays zurück.

| Name | type | Wert |
| ---- | ---- | ----- |
| Unterstützt | array | [ "1" ] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

Sie können anhand der Antwort von pickZones bestimmen, ob NULL für Zonen bereitgestellt werden soll oder virtuelle Computer verschiedenen Zonen zugewiesen werden sollen.

## <a name="providers"></a>providers

**Die providers-Funktion wurde als veraltet markiert.** Ihre Verwendung wird nicht mehr empfohlen. Wenn Sie diese Funktion verwendet haben, um eine API-Version für den Ressourcenanbieter abzurufen, empfehlen wir, dass Sie eine bestimmte API-Version in Ihrer Vorlage bereitstellen. Die Verwendung einer dynamisch zurückgegebenen API-Version kann Ihre Vorlage beschädigen, wenn sich die Eigenschaften zwischen Versionen ändern.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

## <a name="reference"></a>Referenz

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Gibt ein Objekt zurück, das den Laufzeitstatus einer Ressource darstellt.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

Die reference-Funktion ist zwar in Bicep-Dateien verfügbar, Sie werden sie aber in der Regel nicht benötigen. Verwenden Sie stattdessen den symbolischen Namen für die Ressource.

Im folgenden Beispiel wird ein Speicherkonto bereitgestellt. Dabei wird der symbolische Name `stg` für das Speicherkonto verwendet, um eine Eigenschaft zurückzugeben.

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

Zum Abrufen einer Eigenschaft einer vorhandenen Ressource, die nicht in der Vorlage bereitgestellt ist, verwenden Sie das Schlüsselwort `existing`:

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
output blobAddress string = stg.properties.primaryEndpoints.blob
```

Wenn Sie versuchen, auf eine Ressource zu verweisen, die nicht vorhanden ist, erhalten Sie den Fehler `NotFound`, und Ihre Bereitstellung schlägt fehl.

Weitere Informationen finden Sie unter [Referenzressourcen](./compare-template-syntax.md#reference-resources) und unter der [reference-Funktion für JSON-Vorlagen](../templates/template-functions-resource.md#reference).

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Gibt den eindeutigen Bezeichner einer Ressource zurück.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

Die resourceId-Funktion ist zwar in Bicep-Dateien verfügbar, Sie werden sie aber in der Regel nicht benötigen. Verwenden Sie stattdessen den symbolischen Namen für die Ressource, und greifen Sie auf die `id`-Eigenschaft zu.

Diese Funktion wird verwendet, wenn der Ressourcenname zweideutig ist oder nicht innerhalb der gleichen Bicep-Datei zur Verfügung gestellt wird. Das Format des zurückgegebenen Bezeichners variiert abhängig davon, ob die Bereitstellung im Bereich einer Ressourcengruppe, eines Abonnements, einer Verwaltungsgruppe oder eines Mandanten erfolgt.

Beispiel:

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: 'eastus'
  kind: 'Storage'
  sku: {
    name: 'Standard_LRS'
  }
}

output storageID string = stg.id
```

Verwenden Sie das Schlüsselwort „existing“, um die Ressourcen-ID für eine Ressource abzurufen, die nicht in der Bicep-Datei bereitgestellt wird.

```bicep
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
    name: storageAccountName
}

output storageID string = stg.id
```

Weitere Informationen finden Sie unter der [resourceId-Funktion für JSON-Vorlagen](../templates/template-functions-resource.md#resourceid).

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Gibt den eindeutigen Bezeichner für eine Ressource zurück, die auf Abonnementebene bereitgestellt wird.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

Die subscriptionResourceId-Funktion ist zwar in Bicep-Dateien verfügbar, Sie werden sie aber in der Regel nicht benötigen. Verwenden Sie stattdessen den symbolischen Namen für die Ressource, und greifen Sie auf die `id`-Eigenschaft zu.

Der Bezeichner wird im folgenden Format zurückgeben:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Bemerkungen

Mit dieser Funktion können Sie die Ressourcen-ID für Ressourcen abrufen, die [im Abonnement bereitgestellt werden](deploy-to-subscription.md) und nicht in einer Ressourcengruppe. Die zurückgegebene ID unterscheidet sich dadurch von dem Wert, der von der Funktion [resourceId](#resourceid) zurückgegeben wird, dass kein Ressourcengruppenwert enthalten ist.

### <a name="subscriptionresourceid-example"></a>Beispiel für „subscriptionResourceId“

Mit der folgenden Bicep-Datei wird eine integrierte Rolle zugewiesen. Sie können diese entweder in einer Ressourcengruppe oder einem Abonnement bereitstellen. Hierbei wird die Funktion „subscriptionResourceId“ verwendet, um die Ressourcen-ID für integrierte Rollen abzurufen.

```bicep
param principalId string {
  metadata: {
    'description': 'principalId'
  }
}
param builtInRoleType string {
  'allowed': [
    'Owner'
    'Contributor'
    'Reader'
  ]
  'metadata': {
      'description': 'Built-in role to assign'
  }
}

var roleDefinitionId = {
  Owner: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')
  }
  Contributor: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')
  }
  Reader: {
    id: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')
  }
}

resource myRoleAssignment 'Microsoft.Authorization/roleAssignments@2018-09-01-preview' = {
  name: guid(resourceGroup().id, principalId, roleDefinitionId[builtInRoleType].id)
  properties: {
    roleDefinitionId: roleDefinitionId[builtInRoleType].id
    principalId: principalId
  }
}
```

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Gibt den eindeutigen Bezeichner für eine Ressource zurück, die auf Mandantenebene bereitgestellt wird.

Namespace: [az](bicep-functions.md#namespaces-for-functions).

Die tenantResourceId-Funktion ist zwar in Bicep-Dateien verfügbar, Sie werden sie aber in der Regel nicht benötigen. Verwenden Sie stattdessen den symbolischen Namen für die Ressource, und greifen Sie auf die `id`-Eigenschaft zu.

Der Bezeichner wird im folgenden Format zurückgeben:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Integrierte Richtliniendefinitionen sind Ressourcen auf Mandantenebene. Verwenden Sie die tenantResourceId-Funktion, um eine Richtlinienzuweisung bereitzustellen, die auf eine integrierte Richtliniendefinition verweist.

```bicep
param policyDefinitionID string{
  default: '0a914e76-4921-4c19-b460-a2d36003525a'
  metadata: {
    'description': 'Specifies the ID of the policy definition or policy set definition being assigned.'
  }
}

param policyAssignmentName string {
  default: guid(policyDefinitionID, resourceGroup().name)
  metadata: {
    'description': 'Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides.'
  }
}

resource myPolicyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: policyAssignmentName
  properties: {
    scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
    policyDefinitionId: tenantResourceId('Microsoft.Authorization/policyDefinitions', policyDefinitionID)
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Abrufen von Werten aus der aktuellen Bereitstellung finden Sie unter [Funktionen für Bereitstellungswerte](./bicep-functions-deployment.md).
* Wenn Sie beim Erstellen eines Ressourcentyps eine angegebene Anzahl von Wiederholungen durchlaufen möchten, finden Sie weitere Informationen unter [Iterative Schleifen in Bicep](loops.md).
