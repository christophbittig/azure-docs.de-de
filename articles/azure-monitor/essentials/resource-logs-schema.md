---
title: Unterstützte Dienste und Schemas für Azure-Ressourcenprotokolle
description: Erfahren Sie mehr über die unterstützten Dienste und Ereignisschemas für Azure-Ressourcenprotokolle.
ms.topic: reference
ms.date: 05/10/2021
ms.openlocfilehash: 1ef9c5ecea1ff45c7e6b3501f7c71928a5ea60d6
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069461"
---
# <a name="common-and-service-specific-schemas-for-azure-resource-logs"></a>Allgemeine und dienstspezifische Schemas für Azure-Ressourcenprotokolle

> [!NOTE]
> Ressourcenprotokolle wurden zuvor als Diagnoseprotokolle bezeichnet. Der Name wurde im Oktober 2019 geändert, da die Typen der von Azure Monitor gesammelten Protokolle nicht mehr nur die Azure-Ressource umfassen. 
>
> In diesem Artikel wurden ursprünglich Ressourcenprotokollkategorien aufgeführt, die Sie sammeln können. Diese Liste befindet sich jetzt unter [Ressourcenprotokollkategorien](resource-logs-categories.md). 

[Azure Monitor-Ressourcenprotokolle](../essentials/platform-logs-overview.md) sind von Azure-Diensten ausgegebene Protokolle, die die Vorgänge der jeweiligen Dienste oder Ressourcen beschreiben. Alle Ressourcenprotokolle, die über Azure Monitor verfügbar sind, verwenden ein gemeinsames Schema der obersten Ebene. Jeder Dienst kann flexibel eindeutige Eigenschaften für seine eigenen Ereignisse ausgeben.

Ein Schema wird anhand einer Kombination aus dem Ressourcentyp (in der `resourceId`-Eigenschaft verfügbar) und der Kategorie eindeutig identifiziert. In diesem Artikel werden die Schemas der obersten Ebene für Ressourcenprotokolle beschrieben und Links zu den Schemas für die einzelnen Dienste bereitgestellt.


## <a name="top-level-common-schema"></a>Allgemeines Schema der obersten Ebene

> [!NOTE]
> Das hier beschriebene Schema ist gültig, wenn die Ressourcenprotokolle an Azure Storage oder einen Event Hub gesendet werden. Wenn die Protokolle an einen Log Analytics-Arbeitsbereich gesendet werden, können die Spaltennamen sich unterscheiden. Häufig auftretende Spalten in allen Tabellen in einem Log Analytics-Workspace finden Sie unter [Standardspalten in Azure Monitor-Protokollen](../logs/log-standard-columns.md) und eine Referenz zu verschiedenen Tabellen unter [Azure Monitor-Datenreferenz](/azure/azure-monitor/reference).

| Name | Erforderlich oder optional | BESCHREIBUNG |
|---|---|---|
| `time` | Erforderlich | Der Zeitstempel (UTC) des Ereignisses. |
| `resourceId` | Erforderlich | Die Ressourcen-ID der Ressource, die das Ereignis ausgegeben hat. Für Mandantendienste weist sie das Format */tenants/tenant-id/providers/provider-name* auf. |
| `tenantId` | Erforderlich für Mandantenprotokolle | Die Mandanten-ID des Active Directory-Mandanten, mit dem dieses Ereignis verknüpft ist. Diese Eigenschaft wird nur für Protokolle auf Mandantenebene verwendet. In Protokollen auf Ressourcenebene kommt sie nicht vor. |
| `operationName` | Erforderlich | Der Name des Vorgangs, den dieses Ereignis darstellt. Wenn das Ereignis einen Azure-RBAC-Vorgang (Role-Based Access Control, rollenbasierte Zugriffssteuerung) darstellt, ist dies der Name des Azure-RBAC-Vorgangs (Beispiel: `Microsoft.Storage/storageAccounts/blobServices/blobs/Read`). Dieser Name wird normalerweise in Form eines Azure Resource Manager-Vorgangs modelliert, auch wenn es sich nicht um einen dokumentierten Resource Manager-Vorgang handelt: (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`). |
| `operationVersion` | Optional | Die dem Vorgang zugeordnete API-Version, wenn `operationName` über eine API durchgeführt wurde (Beispiel: `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Wenn keine API für diesen Vorgang vorhanden ist, entspricht die Version der Version dieses Vorgangs für den Fall, dass sich die dem Vorgang zugeordneten Eigenschaften in Zukunft ändern. |
| `category` | Erforderlich | Die Protokollkategorie des Ereignisses. „category“ ist die Granularität, mit der Sie Protokolle für eine bestimmte Ressource aktivieren oder deaktivieren können. Die Eigenschaften, die im Eigenschaftenblob eines Ereignisses angezeigt werden, sind für eine bestimmte Protokollkategorie und einen Ressourcentyp gleich. Typische Protokollkategorien sind `Audit`, `Operational`, `Execution` und `Request`. |
| `resultType` | Optional | Der Status des Ereignisses. Typische Werte sind `Started`, `In Progress`, `Succeeded`, `Failed`, `Active` und `Resolved`. |
| `resultSignature` | Optional | Der Unterstatus des Ereignisses. Wenn dieser Vorgang einem REST-API-Aufruf entspricht, ist dieses Feld der HTTP-Statuscode des entsprechenden REST-Aufrufs. |
| `resultDescription `| Optional | Die statische Textbeschreibung dieses Vorgangs, z. B. `Get storage file`. |
| `durationMs` | Optional | Die Dauer des Vorgangs in Millisekunden. |
| `callerIpAddress` | Optional | Die IP-Adresse des Aufrufers, wenn der Vorgang einem API-Aufruf entspricht, der von einer Entität mit einer öffentlich verfügbaren IP-Adresse stammt. |
| `correlationId` | Optional | Eine GUID, die zum Gruppieren einer Reihe verwandter Ereignisse verwendet wird. Wenn zwei Ereignisse über denselben `operationName`-Wert, aber zwei verschiedene Status (z. B. `Started` und `Succeeded`) verfügen, verwenden sie in der Regel denselben `correlationID`-Wert. Hiermit können auch andere Beziehungen zwischen Ereignissen dargestellt werden. |
| `identity` | Optional | Ein JSON-Blob zum Beschreiben der Identität des Benutzers oder der Anwendung, der bzw. die den Vorgang durchgeführt hat. Normalerweise sind in diesem Feld auch die Autorisierung und die Ansprüche bzw. das JWT-Token aus Active Directory enthalten. |
| `Level` | Optional | Der Schweregrad des Ereignisses. Muss `Informational`, `Warning`, `Error` oder `Critical` sein. |
| `location` | Optional | Die Region der Ressource, die das Ereignis ausgibt, z. B. `East US` oder `France South`. |
| `properties` | Optional | Erweiterte Eigenschaften, die sich auf diese Kategorie von Ereignissen beziehen. Alle benutzerdefinierten bzw. eindeutigen Eigenschaften müssen in diesem „Teil B“ des Schemas angeordnet werden. |

## <a name="service-specific-schemas"></a>Dienstspezifische Schemas

Das Schema für Ressourcenprotokolle variiert abhängig von der Ressource und der Protokollkategorie. In der folgenden Liste sind Azure-Dienste aufgeführt, über die Ressourcenprotokolle und Links zum Dienst und zum kategoriespezifischen Schema (falls zutreffend) verfügbar gemacht werden. Die Liste ändert sich, wenn neue Dienste hinzugefügt werden. Wenn die von Ihnen benötigten Dienste nicht aufgeführt sind, können Sie ein GitHub-Issue zu diesem Artikel erstellen, damit wir ihn aktualisieren können.

| Dienst oder Feature | Schema und Dokumentation |
| --- | --- |
| Azure Active Directory | [Übersicht](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [Überwachungsprotokollschema](../../active-directory/reports-monitoring/overview-reports.md), [Anmeldeschema](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Azure Analysis Services | [Azure Analysis Services: Einrichten der Diagnoseprotokollierung](../../analysis-services/analysis-services-logging.md) |
| Azure API Management | [API Management-Ressourcenprotokolle](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| Azure App Service | [App Service-Protokolle](../../app-service/troubleshoot-diagnostic-logs.md)
| Azure Application Gateway |[Protokollierung für Application Gateway](../../application-gateway/application-gateway-diagnostics.md) |
| Azure-Automatisierung |[Log Analytics für Azure Automation](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch-Protokollierung](../../batch/batch-diagnostics.md) |
| Azure Cognitive Services | [Protokollierung für Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Azure Container Instances | [Protokollierung für Azure Container Instances](../../container-instances/container-instances-log-analytics.md#log-schema) |
| Azure Container Registry | [Protokollierung für Azure Container Registry](../../container-registry/monitor-service.md) |
| Azure Content Delivery Network | [Diagnoseprotokolle für Azure Content Delivery Network](../../cdn/cdn-azure-diagnostic-logs.md) |
| Azure Cosmos DB | [Azure Cosmos DB-Protokollierung](../../cosmos-db/monitor-cosmos-db.md) |
| Azure-Daten-Explorer | [Azure Data Explorer-Protokolle](/azure/data-explorer/using-diagnostic-logs) |
| Azure Data Factory | [Überwachen von Data Factorys mit Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Azure Data Lake Analytics |[Zugreifen auf Protokolle für Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Azure Data Lake Storage |[Zugreifen auf Protokolle für Azure Data Lake Storage](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Azure Database for MySQL | [Azure Database for MySQL-Diagnoseprotokolle](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL-Protokolle](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure Databricks | [Diagnoseprotokollierung in Azure Databricks](/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs) |
| Azure DDoS Protection | [Protokollierung für Azure DDoS Protection Standard](../../ddos-protection/diagnostic-logging.md#log-schemas) |
| Azure Digital Twins | [Einrichten der Azure Digital Twins-Diagnose](../../digital-twins/troubleshoot-diagnostics.md#log-schemas)
| Azure Event Hubs |[Azure Event Hubs-Protokolle](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Azure ExpressRoute | Schema nicht verfügbar |
| Azure Firewall | [Protokollierung für Azure Firewall](../../firewall/logs-and-metrics.md#diagnostic-logs) |
| Azure Front Door | [Protokollierung für Azure Front Door](../../frontdoor/front-door-diagnostics.md) |
| Azure IoT Hub | [IoT Hub-Vorgänge](../../iot-hub/monitor-iot-hub-reference.md#resource-logs) |
| Azure Key Vault |[Azure Key Vault-Protokollierung](../../key-vault/general/logging.md) |
| Azure Kubernetes Service |[Azure Kubernetes Service-Protokollierung](../../aks/monitor-aks-reference.md#resource-logs) |
| Azure Load Balancer |[Log Analytics für Azure Load Balancer](../../load-balancer/monitor-load-balancer.md) |
| Azure Logic Apps |[Benutzerdefiniertes Logic Apps-B2B-Nachverfolgungsschema](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Azure Machine Learning | [Diagnoseprotokollierung in Azure Machine Learning](../../machine-learning/monitor-resource-reference.md) |
| Azure Media Services | [Media Services-Überwachungsschemas](../../media-services/latest/monitoring/monitor-media-services-data-reference.md#schemas) |
| Netzwerksicherheitsgruppen |[Log Analytics für Netzwerksicherheitsgruppen (NSGs)](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Azure Power BI Embedded | [Protokollierung für Power BI Embedded in Azure](/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Datenmodell für Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| Durchsuchen der Datenverkehrsanalyse |[Aktivieren und Verwenden von „Datenverkehrsanalyse durchsuchen“](../../search/search-traffic-analytics.md) |
| Azure-Servicebus |[Azure Service Bus-Protokolle](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| Azure SQL-Datenbank | [Azure SQL-Datenbank-Protokollierung](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| Azure Storage | [Blobs](../../storage/blobs/monitor-blob-storage-reference.md#resource-logs-preview), [Dateien](../../storage/files/storage-files-monitoring-reference.md#resource-logs-preview), [Warteschlangen](../../storage/queues/monitor-queue-storage-reference.md#resource-logs-preview), [Tabellen](../../storage/tables/monitor-table-storage-reference.md#resource-logs-preview) |
| Azure Stream Analytics |[Auftragsprotokolle](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Azure Traffic Manager | [Traffic Manager-Protokollschema](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| Azure Virtual Network | Schema nicht verfügbar |
| Gateways des virtuellen Netzwerks | Schema nicht verfügbar |



## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen der Ressourcenprotokollkategorien, die Sie sammeln können](resource-logs-categories.md)
* [Weitere Informationen zu Ressourcenprotokollen](../essentials/platform-logs-overview.md)
* [Streamen von Ressourcenprotokollen an Event Hubs](./resource-logs.md#send-to-azure-event-hubs)
* [Ändern der Diagnoseeinstellungen für Ressourcenprotokolle mithilfe der Azure Monitor-REST-API](/rest/api/monitor/diagnosticsettings)
* [Analysieren von Protokollen aus Azure Storage mit Log Analytics](./resource-logs.md#send-to-log-analytics-workspace)
