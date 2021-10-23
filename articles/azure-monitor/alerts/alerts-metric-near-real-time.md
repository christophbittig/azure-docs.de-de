---
title: Unterstützte Ressourcen für Metrikwarnungen in Azure Monitor
description: Referenz zu Supportmetriken und -protokollen für Metrikwarnungen in Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 38564ba8d607dc82a13757669b7d352359fb29e8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994744"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Unterstützte Ressourcen für Metrikwarnungen in Azure Monitor

Azure Monitor unterstützt jetzt einen [neuen Metrikwarnungstyp](./alerts-overview.md), der erhebliche Vorteile gegenüber älteren [klassischen Metrikwarnungen](./alerts-classic.overview.md) aufweist. Metriken stehen für [eine umfangreiche Liste von Azure-Diensten](../essentials/metrics-supported.md) zur Verfügung. Die neueren Warnungen unterstützen eine (stetig wachsende) Teilmenge der Ressourcentypen. In diesem Artikel wird diese Teilmenge aufgeführt.

Sie können auch neuere Metrikwarnungen für gängige Protokolldaten verwenden, die in einem Log Analytics-Arbeitsbereich gespeichert sind und als Metriken extrahiert wurden. Weitere Informationen finden Sie unter [Metrikwarnungen für Protokolle](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, Befehlszeilenschnittstelle, REST-Unterstützung
Derzeit können Sie neuere Metrikwarnungen nur im Azure-Portal, in der [REST-API](/rest/api/monitor/metricalerts/) oder in [Resource Manager-Vorlagen](./alerts-metric-create-templates.md) erstellen. Die Konfiguration neuerer Warnungen über PowerShell und die Azure-Befehlszeilenschnittstelle (Azure CLI, Version 2.0 und höher) wird in Kürze unterstützt.

## <a name="metrics-and-dimensions-supported"></a>Unterstützte Metriken und Dimensionen
Neuere Metrikwarnungen unterstützen Warnungen für Metriken mit Dimensionen. Mithilfe von Dimensionen können Sie die Metrik nach der richtigen Ebene filtern. Sie können alle unterstützten Metriken und die dazugehörigen Dimensionen unter [Azure Monitor-Metrik-Explorer](../essentials/metrics-charts.md) erkunden und visualisieren.

Im Folgenden finden Sie die vollständige Liste der Azure Monitor-Metrikquellen, die von den neueren Warnungen unterstützt werden:

|Ressourcentyp  |Unterstützte Dimensionen |Warnungen mit mehreren Ressourcen| Verfügbare Metriken|
|---------|---------|-----|----------|
|Microsoft.Aadiam/azureADMetrics | Ja | Nein | [Azure AD](../essentials/metrics-supported.md#microsoftaadiamazureadmetrics) |
|Microsoft.ApiManagement/service | Ja | Nein | [API Management](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Ja | Nein | [App Configuration](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/spring | Ja | Nein | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automationAccounts | Ja| Nein | [Automation-Konten](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft.AVS/privateClouds | Nein | Nein | [Azure VMware Solution](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Batch/batchAccounts | Ja | Nein | [Batch-Konten](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Bing/accounts | Ja | Nein | [Bing-Konten](../essentials/metrics-supported.md#microsoftbingaccounts) |
|Microsoft.BotService/botServices | Ja | Nein  | [Botdienste](../essentials/metrics-supported.md#microsoftbotservicebotservices) |
|Microsoft.Cache/redis | Ja | Ja | [Azure Cache for Redis](../essentials/metrics-supported.md#microsoftcacheredis) |
|Microsoft.Cache/redisEnterprise | Ja | Nein | [Azure Cache for Redis-Enterprise](../essentials/metrics-supported.md#microsoftcacheredisenterprise) |
|microsoft.Cdn/profiles | Ja | Nein | [CDN-Profile](../essentials/metrics-supported.md#microsoftcdnprofiles) |
|Microsoft.ClassicCompute/domainNames/slots/roles | Nein | Nein | [Cloud Services (klassisch)](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Microsoft.ClassicCompute/virtualMachines | Nein | Nein | [Virtuelle Computer (klassisch)](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Microsoft.ClassicStorage/storageAccounts | Ja | Nein | [Speicherkonten (klassisch)](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Ja | Nein | [Speicherkonten (klassisch): Blobs](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Microsoft.ClassicStorage/storageAccounts/fileServices | Ja | Nein | [Speicherkonten (klassisch): Dateien](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Ja | Nein | [Speicherkonten (klassisch): Warteschlangen](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Ja | Nein | [Speicherkonten (klassisch): Tabellen](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Microsoft.CognitiveServices/accounts | Ja | Nein | [Cognitive Services](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/cloudServices | Ja | Nein  |  [Cloud Services](../essentials/metrics-supported.md#microsoftcomputecloudservices) |
|Microsoft.Compute/cloudServices/roles | Ja | Nein  |  [Clouddienstrollen](../essentials/metrics-supported.md#microsoftcomputecloudservicesroles) |
|Microsoft.Compute/virtualMachines | Ja | Ja<sup>1</sup> | [Virtuelle Computer](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Ja | Nein |[Skalierungsgruppen für virtuelle Computer](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ConnectedVehicle/platformAccounts | Ja | Nein |[Connected Vehicle Platform-Konten](../essentials/metrics-supported.md#microsoftconnectedvehicleplatformaccounts) |
|Microsoft.ContainerInstance/containerGroups | Ja| Nein | [Containergruppen](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | Nein | Nein | [Containerregistrierungen](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Ja | Nein | [Verwaltete Cluster](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Ja| Nein | [Data Factorys V1](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/factories |Ja | Nein | [Data Factorys V2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Ja | Nein | [Data Share-Instanzen](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft.DBforMariaDB/servers | Nein | Nein | [DB for MariaDB](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Nein | Nein |[Datenbank für MySQL](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/flexibleServers | Ja | Nein | [DB for PostgreSQL (flexible Server)](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.DBforPostgreSQL/serverGroupsv2 | Ja | Nein  | DB für PostgreSQL (Hyperscale) |
|Microsoft.DBforPostgreSQL/servers | Nein | Nein | [Datenbank für PostgreSQL](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | Nein | Nein | [DB for PostgreSQL V2](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.Devices/IotHubs | Ja | Nein |[IoT Hub](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Ja | Nein | [Device Provisioning Service-Instanzen](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft.DigitalTwins/digitalTwinsInstances | Ja | Nein | [Digital Twins](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Ja | Nein | [Cosmos DB](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domains | Ja | Nein | [Event Grid-Domänen](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Microsoft.EventGrid/systemTopics | Ja | Nein | [Event Grid-Systemthemen](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/topics |Ja | Nein | [Event Grid-Themen](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Ja| Nein | [Event Hubs-Cluster](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Ja| Nein | [Event Hubs](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Ja | Nein | [HDInsight-Cluster](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Components | Ja | Nein | [Application Insights](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Ja |Ja |[Tresore](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Ja |Nein |[Data Explorer-Cluster](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Ja | Nein |[Integrationsdienstumgebungen](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Nein | Nein |[Logik-Apps](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Ja | Nein | [Machine Learning](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.MachineLearningServices/workspaces/onlineEndpoints | Ja | Nein  | Machine Learning-Endpunkte |
|Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments | Ja | Nein  | Machine Learning: Endpunktbereitstellungen |
|Microsoft.Maps/accounts | Ja | Nein | [Maps-Konten](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | Nein | Nein | [Media Services](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Ja | Nein | [Media Services-Streamingendpunkte](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Ja | Ja | [Azure NetApp-Kapazitätspools](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Ja | Ja | [Azure NetApp-Volumes](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Ja | Nein | [Anwendungsgateways](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Ja | Nein | [Firewalls](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Nein | Nein | [DNS-Zonen](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Ja | Nein |[ExpressRoute-Verbindungen](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/expressRoutePorts | Ja | Nein |[ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Microsoft.Network/loadBalancers (nur für Standard-SKUs)| Ja| Nein | [Lastenausgleichsmodule](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways| Nein | Nein | [NAT-Gateways](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Microsoft.Network/privateEndpoints| Nein | Nein | [Private Endpunkte](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Microsoft.Network/privateLinkServices| Nein | Nein | [Private Link-Dienste](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Microsoft.Network/publicipaddresses | Nein | Nein | [Öffentliche IP-Adressen](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Nein | [Traffic Manager-Profile](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ja | Nein | [Log Analytics-Arbeitsbereiche](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Peering/peerings | Ja | Nein | [Peerings](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft.Peering/peeringServices | Ja | Nein | [Peeringdienste](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | Nein | Nein | [Kapazitäten](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.RecoveryServices/vaults | Ja | Ja | [Recovery Services-Tresore](../essentials/metrics-supported.md#microsoftrecoveryservicesvaults) |
|Microsoft.Relay/namespaces | Ja | Nein | [Relays](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | Nein | Nein | [Suchdienste](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Ja | Nein | [Service Bus](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | Nein | Ja | [SQL Managed Instance](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Nein | Ja | [SQL-Datenbanken](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servers/elasticPools | Nein | Ja | [Pools für elastische SQL-Datenbanken](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Ja | Nein | [Speicherkonten](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/blobServices | Ja| Nein | [Speicherkonten: Blobs](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Microsoft.Storage/storageAccounts/fileServices | Ja| Nein | [Speicherkonten: Dateien](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Microsoft.Storage/storageAccounts/queueServices | Ja| Nein | [Speicher Konten: Warteschlangen](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Microsoft.Storage/storageAccounts/tableServices | Ja| Nein | [Speicher Konten: Tabellen](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Microsoft.StorageCache/caches | Ja | Nein | [HPC Cache-Instanzen](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Microsoft.StorageSync/storageSyncServices | Ja | Nein | [Speichersynchronisierungsdienste](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Ja | Nein | [Stream Analytics](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.Synapse/workspaces | Ja | Nein | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Microsoft.Synapse/workspaces/bigDataPools | Ja | Nein | [Apache Spark-Pools in Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Microsoft.Synapse/workspaces/sqlPools | Ja | Nein | [SQL-Pools von Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Microsoft.VMWareCloudSimple/virtualMachines | Ja | Nein | [Virtuelle CloudSimple-Computer](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Ja | Nein | [Pools mit mehreren Rollen für die App Service-Umgebung](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Ja | Nein | [Workerpools für die App Service-Umgebung](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Ja | Nein | [App Service-Pläne](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Ja | Nein | [App Services und Functions](../essentials/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Ja | Nein | [App Service-Slots](../essentials/metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> Für Netzwerkmetriken (Eingehender Netzwerkverkehr gesamt, Ausgehender Netzwerkverkehr gesamt, Eingehende Datenflüsse, Ausgehende Datenflüsse, Maximale Erstellungsrate für eingehende Datenflüsse, Maximale Erstellungsrate für ausgehende Datenflüsse) sowie benutzerdefinierte Metriken virtueller Computer nicht unterstützt.

## <a name="payload-schema"></a>Nutzlast und Schema

> [!NOTE]
> Für Ihre Webhook-Integrationen können Sie auch das [allgemeine Warnungsschema](./alerts-common-schema.md) verwenden, das den Vorteil einer einzelnen erweiterbaren und einheitlichen Warnungsnutzlast für alle Benachrichtigungsdienste in Azure Monitor bietet. [Hier finden Sie Informationen zu den Definitionen des allgemeinen Warnungsschemas](./alerts-common-schema-definitions.md).


Bei Verwendung einer ordnungsgemäß konfigurierten [Aktionsgruppe](./action-groups.md) enthält der POST-Vorgang für alle neueren Metrikwarnungen die folgende JSON-Nutzlast und das folgende Schema:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [neue Oberfläche „Warnungen“](./alerts-overview.md).
* Erfahren Sie mehr über [Protokollwarnungen in Azure](./alerts-unified-log.md).
* Erfahren Sie mehr über [Warnungen in Azure](./alerts-overview.md).
