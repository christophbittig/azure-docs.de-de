---
title: Was ist privater Endpunkt in Azure?
description: Informationen zum privaten Endpunkt in Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: allensu
ms.openlocfilehash: 0d613d7d207da8632fe7a2767d6440ee62378866
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359407"
---
# <a name="what-is-azure-private-endpoint"></a>Was ist privater Endpunkt in Azure?

Ein privater Endpunkt ist eine Netzwerkschnittstelle, die eine private IP-Adresse aus Ihrem virtuellen Netzwerk verwendet. Diese Netzwerkschnittstelle bietet eine private und sichere Verbindung zwischen Ihnen und einem von Azure Private Link unterstützten Dienst. Indem Sie einen privaten Endpunkt aktivieren, binden Sie den Dienst in Ihr virtuelles Netzwerk ein.

Dieser Dienst könnte ein Azure-Dienst sein, wie z. B.:

* Azure Storage
* Azure Cosmos DB
* Azure SQL-Datenbank
* Ihr eigener Dienst, der einen [Private Link-Dienst](private-link-service-overview.md) verwendet.
  
## <a name="private-endpoint-properties"></a>Eigenschaften eines privaten Endpunkts 
 Für einen privaten Endpunkt werden die folgenden Eigenschaften angegeben: 

|Eigenschaft  |BESCHREIBUNG |
|---------|---------|
|Name    |    Ein eindeutiger Name innerhalb der Ressourcengruppe.      |
|Subnet    |  Das bereitzustellende Subnetz und der Ort, an dem die private IP-Adresse zugewiesen wird. Die Anforderungen an das Subnetz finden Sie im Abschnitt „Einschränkungen“ in diesem Artikel.         |
|Private Link-Ressource    |   Die Private Link-Ressource, mit der anhand der Liste verfügbarer Typen eine Verbindung über die Ressourcen-ID oder den Alias hergestellt werden soll. Für den gesamten Datenverkehr an diese Ressource wird ein eindeutiger Netzwerkbezeichner generiert.       |
|Unterressource des Ziels   |      Die Unterressource, mit der eine Verbindung hergestellt wird. Jeder Private Link-Ressourcentyp verfügt über verschiedene Optionen, die je nach Präferenz ausgewählt werden können.    |
|Methode zur Genehmigung der Verbindung    |  Automatisch oder manuell. Abhängig von den über die rollenbasierte Zugriffssteuerung in Azure erteilten Berechtigungen kann Ihr privater Endpunkt automatisch genehmigt werden. Wenn Sie versuchen, sich mit einer Private Link-Ressource ohne Azure rollenbasierte Zugriffssteuerung zu verbinden, verwenden Sie die manuelle Methode, um dem Besitzer der Ressource zu ermöglichen, die Verbindung zu genehmigen.        |
|Anforderungsnachricht     |  Sie können eine Nachricht für angeforderte Verbindungen angeben, die manuell genehmigt werden sollen. Mithilfe dieser Nachricht kann eine bestimmte Anforderung identifiziert werden.        |
|Verbindungsstatus   |   Eine schreibgeschützte Eigenschaft, die angibt, ob der private Endpunkt aktiv ist. Nur private Endpunkte in genehmigtem Zustand können zum Senden von Datenverkehr verwendet werden. Weitere verfügbare Status: <br>-**Genehmigt**: Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit.</br><br>-**Ausstehend**: Die Verbindung wurde manuell erstellt, wobei die Genehmigung des Besitzers der Private Link-Ressource aussteht.</br><br>-**Abgelehnt**: Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt.</br><br>-**Getrennt**: Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt. Der private Endpunkt dient nur noch Informationszwecken und sollte zur Bereinigung gelöscht werden. </br>|

Im Folgenden werden einige wichtige Punkte zu privaten Endpunkten erläutert: 

- Ein privater Endpunkt ermöglicht Konnektivität zwischen Consumern, wenn Folgendes gleich ist:
    
    - Virtual Network
    - Virtuelle Netzwerke mit regionalem Peering
    - Virtuelle Netzwerke mit globalem Peering
    - Lokales Netzwerk über ein [VPN](https://azure.microsoft.com/services/vpn-gateway/) oder [ExpressRoute](https://azure.microsoft.com/services/expressroute/)
    - Von Private Link unterstützte Dienste
 
- Netzwerkverbindungen können nur von Clients initiiert werden, die eine Verbindung mit dem privaten Endpunkt herstellen. Dienstanbieter verfügen nicht über eine Routingkonfiguration, um Verbindungen mit Dienstverbrauchern herzustellen. Verbindungen können nur in einer Richtung eingerichtet werden.

- Beim Erstellen eines privaten Endpunkts wird für die Lebensdauer der Ressource eine schreibgeschützte Netzwerkschnittstelle eingerichtet. Der Schnittstelle wird eine private dynamische IP-Adresse aus dem Subnetz zugewiesen, dass der privaten Link-Ressource zugeordnet ist. Der Wert der privaten IP-Adresse bleibt über den gesamten Lebenszyklus des privaten Endpunkts unverändert.
 
- Der private Endpunkt muss in derselben Region und im selben Abonnement wie das virtuelle Netzwerk bereitgestellt werden. 
 
- Die Private Link-Ressource kann in einer anderen Region als das virtuelle Netzwerk und der private Endpunkt bereitgestellt werden.
 
- Mehrere private Endpunkte können mithilfe derselben Private Link-Ressource erstellt werden. Für ein einzelnes Netzwerk mit einer herkömmlichen DNS-Serverkonfiguration wird empfohlen, nur einen privaten Endpunkt pro Private Link-Ressource zu verwenden. Verwenden Sie diese Vorgehensweise, um doppelte Einträge oder Konflikte bei der DNS-Auflösung zu vermeiden. 
 
- Mehrere private Endpunkte können im gleichen oder in verschiedenen Subnetzen innerhalb desselben virtuellen Netzwerks erstellt werden. Die Anzahl der privaten Endpunkte, die Sie in einem Abonnement anlegen können, ist begrenzt. Ausführliche Informationen finden Sie im Artikel zu  [Azure-Grenzwerten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- Das Abonnement der Private Link-Ressource muss ebenfalls bei Microsoft registriert sein. Netzwerkressourcenanbieter. Weitere Informationen finden Sie unter  [Azure-Ressourcenanbieter](../azure-resource-manager/management/resource-providers-and-types.md).
 
## <a name="private-link-resource"></a>Private Link-Ressource 
Eine Private Link-Ressource ist das Ziel eines bestimmten privaten Endpunkts. 

In der folgenden Tabelle sind die verfügbaren Ressourcen aufgeführt, die private Endpunkte unterstützen: 
 
| Name der Private Link-Ressource | Ressourcentyp | Unterressourcen |
| ---------------------------| ------------- | ------------- |
| **Azure App Configuration** | Microsoft.Appconfiguration/configurationStores | configurationStores |
| **Azure Automation** | Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
| **Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Table |
| **Azure Batch** | Microsoft.Batch/batchAccounts | Batch-Konto |
| **Azure Cache for Redis** | Microsoft.Cache/Redis | RedisCache |
| **Azure Cache for Redis-Enterprise** | Microsoft.Cache/redisEnterprise | redisEnterprise |
| **Cognitive Services** | Microsoft.CognitiveServices/accounts | account |
| **Azure Managed Disks** | Microsoft.Compute/diskAccesses | Verwalteter Datenträger |
| **Azure Container Registry** | Microsoft.ContainerRegistry/registries | Registrierung |
| **Azure Kubernetes Service: Kubernetes-API** | Microsoft.ContainerService/managedClusters | management |
| **Azure Data Factory** | Microsoft.DataFactory/factories | Data Factory |
| **Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers | mariadbServer |
| **Azure Database for MySQL** | Microsoft.DBforMySQL/servers | mysqlServer |
| **Azure Database for PostgreSQL (Einzelserver)** | Microsoft.DBforPostgreSQL/servers | postgresqlServer |
| **Azure IoT Hub** | Microsoft.Devices/IotHubs | iotHub |
| **Microsoft Digital Twins** | Microsoft.DigitalTwins/digitalTwinsInstances | digitaltwinsinstance |
| **Azure Event Grid** | Microsoft.EventGrid/domains | Domäne |
| **Azure Event Grid** | Microsoft.EventGrid/topics  | Event Grid-Thema |
| **Azure Event Hub** | Microsoft.EventHub/namespaces | Namespace |
| **Azure API for FHIR** | Microsoft.HealthcareApis/services | service |
| **Azure Keyvault HSM** | Microsoft.Keyvault/managedHSMs | HSM |
| **Azure Key Vault** | Microsoft.KeyVault/vaults | Tresor |
| **Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces | amlworkspace |
| **Azure Migrate** | Microsoft.Migrate/assessmentProjects | project |
| **Application Gateway** | Microsoft.Network/applicationgateways | Application Gateway |
| **Private Link-Dienst** (Ihr eigener Dienst) |  Microsoft.Network/privateLinkServices | empty |
| **Power BI** | Microsoft.PowerBI/privateLinkServicesForPowerBI | Power BI |
| **Azure Purview** | Microsoft.Purview/accounts | account |
| **Azure Backup** | Microsoft.RecoveryServices/vaults | Tresor |
| **Azure Relay** | Microsoft.Relay/namespaces | Namespace |
| **Microsoft Search** | Microsoft.Search/searchServices | Suchdienst |
| **Azure Service Bus** | Microsoft.ServiceBus/namespaces | Namespace |
| **SignalR** | Microsoft.SignalRService/SignalR | signalr |
| **SignalR** | Microsoft.SignalRService/webPubSub | webpubsub |
| **Azure SQL-Datenbank** | Microsoft.Sql/servers | SQL Server (sqlServer) |
| **Azure Storage (in englischer Sprache)** | Microsoft.Storage/storageAccounts | Blob (blob, blob_secondary)<BR> Tabelle (table, table_secondary)<BR> Warteschlange (queue, queue_secondary)<BR> Datei (file, file_secondary)<BR> Web (web, web_secondary) |
| **Azure-Dateisynchronisierung** | Microsoft.StorageSync/storageSyncServices | Azure-Dateisynchronisierungsdienst |
| **Azure Synapse** | Microsoft.Synapse/privateLinkHubs | synapse |
| **Azure Synapse Analytics** | Microsoft.Synapse/workspaces | Sql, SqlOnDemand, Dev | 
| **Azure App Service** | Microsoft.Web/hostingEnvironments | Hostumgebung |
| **Azure App Service** | Microsoft.Web/sites | sites |
| **Azure App Service** | Microsoft.Web/staticSites | staticSite |

## <a name="network-security-of-private-endpoints"></a>Netzwerksicherheit privater Endpunkte 

Bei der Verwendung privater Endpunkte ist der Datenverkehr zu Private Link-Ressourcen geschützt. Die Plattform verwendet Zugriffssteuerung, um sicherzustellen, dass Netzwerkverbindungen nur die angegebene Private Link-Ressource erreichen. Für den Zugriff auf mehr Ressourcen innerhalb desselben Azure-Diensts sind zusätzliche private Endpunkte erforderlich. 
 
Sie können Ihre Workloads vollständig vom Zugriff auf öffentliche Endpunkte ausschließen, um sich mit einem unterstützten Azure-Dienst zu verbinden. Diese Steuerung bietet eine zusätzliche Netzwerksicherheitsebene für Ihre Ressourcen. Diese Sicherheitsmaßnahme bietet einen Schutz, der den Zugriff auf andere Ressourcen verhindert, die im selben Azure-Dienst gehostet werden. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Zugriff auf eine Private Link-Ressource mithilfe des Genehmigungsworkflows 
Sie können sich mit einer Private Link-Ressource mit den folgenden Methoden zur Genehmigung von Verbindungen verbinden:
- **Automatische** Genehmigung, wenn Sie die spezifische private Link-Ressource besitzen oder über eine Berechtigung verfügen. Die erforderliche Berechtigung basiert auf dem Ressourcentyp des privaten Links in folgendem Format: : Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionsApproval/action
- **Manuelle** Anforderung, wenn Sie nicht über die erforderliche Berechtigung verfügen und den Zugriff anfordern möchten. Ein Genehmigungsworkflow wird ausgelöst. Der private Endpunkt und die anschließende Verbindung mit dem privaten Endpunkt werden im Zustand „Ausstehend“ erstellt. Der Besitzer der Private Link-Ressource ist für die Genehmigung der Verbindung verantwortlich. Nach der Genehmigung ist der private Endpunkt in der Lage, Datenverkehr normal zu senden (siehe das folgende Diagramm des Genehmigungsworkflows).  

![Genehmigungsworkflow](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Der Besitzer der privaten Link-Ressource kann über eine private Endpunktverbindung die folgenden Aktionen vornehmen: 

- Überprüfen aller Details von Verbindungen mit privaten Endpunkten. 
- Genehmigen einer Verbindung mit einem privaten Endpunkt. Der entsprechende private Endpunkt wird für das Senden von Datenverkehr an die Private Link-Ressource aktiviert. 
- Ablehnen einer Verbindung mit einem privaten Endpunkt. Der entsprechende private Endpunkt wird aktualisiert, um den Status widerzuspiegeln.
- Löschen einer Verbindung mit einem privaten Endpunkt in beliebigem Zustand. Der entsprechende private Endpunkt wird mit getrenntem Zustand aktualisiert, um die Aktion widerzuspiegeln. Nur der Besitzer des privaten Endpunkts kann die Ressource an dieser Stelle löschen. 
 
> [!NOTE]
> Nur ein privater Endpunkt im genehmigten Zustand kann Datenverkehr an eine angegebene Private Link-Ressource senden. 

### <a name="connect-with-alias"></a>Herstellen einer Verbindung mit einem Alias

Der Alias ist ein eindeutiger Moniker, der generiert wird, wenn der Dienstbesitzer den Private Link-Dienst hinter einem Standardlastenausgleich erstellt. Dienstbesitzer*innen können ihren Endbenutzer*innen diesen Alias offline mitteilen. 

Endbenutzer*innen können entweder den Ressourcen-URI oder den Alias verwenden, um eine Verbindung mit dem Private Link-Dienst anzufordern. Wenn Sie eine Verbindung über einen Alias herstellen möchten, müssen Sie einen privaten Endpunkt mithilfe der manuellen Verbindungsgenehmigungsmethode erstellen. Um die manuelle Verbindungsgenehmigungsmethode zu verwenden, legen Sie den Parameter für die manuelle Anforderung während des Flows zum Erstellen eines privaten Endpunkts auf TRUE fest. Weitere Informationen finden Sie unter [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) und [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

## <a name="dns-configuration"></a>DNS-Konfiguration

Die für Verbindungen mit einer Private Link-Ressource verwendeten DNS-Einstellungen sind wichtig. Stellen Sie sicher, dass Ihre DNS-Einstellungen korrekt sind, wenn Sie den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für die Verbindung verwenden. Die Einstellungen müssen so festgelegt sein, dass die private IP-Adresse des privaten Endpunkts aufgelöst wird. Bestehende Azure-Dienste verfügen möglicherweise bereits über eine DNS-Konfiguration, die beim Herstellen einer Verbindung über einen öffentlichen Endpunkt verwendet werden kann. Diese Konfiguration muss überschrieben werden, um eine Verbindung mithilfe Ihres privaten Endpunkts herzustellen. 
 
Die Netzwerkschnittstelle, die dem privaten Endpunkt zugeordnet ist, enthält die Informationen, die zum Konfigurieren des DNS benötigt werden. Hierzu gehören der FQDN und die private IP-Adresse für eine Private Link-Ressource. 

Ausführliche Informationen zu Empfehlungen für das Konfigurieren des DNS für private Endpunkte finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](private-endpoint-dns.md).
 
## <a name="limitations"></a>Einschränkungen
 
Die folgende Tabelle enthält eine Liste der bekannten Einschränkungen bei Verwendung privater Endpunkte: 

| Einschränkung | BESCHREIBUNG |Minderung |
| --------- | --------- | --------- |
| Der Datenverkehr, der über eine benutzerdefinierte Route an einen privaten Endpunkt geleitet wird, ist möglicherweise asymmetrisch. | Rückgabedatenverkehr von einem privaten Endpunkt umgeht ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) und versucht, zurück zur Quell-VM zu gelangen. | Die Quellnetzwerkadressenübersetzung (Source Network Address Translation, SNAT) wird verwendet, um symmetrisches Routing sicherzustellen. Für den gesamten Datenverkehr, der über eine benutzerdefinierte Route an einen privaten Endpunkt gesendet wird, wird empfohlen, die SNAT für den Datenverkehr am NVA zu verwenden. |

> [!IMPORTANT]
> Die NSG- und UDR-Unterstützung für private Endpunkte befindet sich in ausgewählten Regionen in der öffentlichen Vorschau. Weitere Informationen finden Sie unter [Öffentliche Vorschauversion von Private Link UDR-Unterstützung](https://azure.microsoft.com/updates/public-preview-of-private-link-udr-support/) und [Öffentliche Vorschauversion der Unterstützung von Netzwerksicherheitsgruppen](https://azure.microsoft.com/updates/public-preview-of-private-link-network-security-group-support/).
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="public-preview-limitations"></a>Einschränkungen der öffentlichen Vorschauversion

### <a name="nsg"></a>NSG

| Einschränkung | BESCHREIBUNG | Minderung |
| ---------- | ----------- | ---------- |
| Das Abrufen der effektiven Routen und Sicherheitsregeln ist an einer Netzwerkschnittstelle für einen privaten Endpunkt nicht verfügbar. | Sie können nicht zur Netzwerkschnittstelle navigieren, um relevante Informationen zu den effektiven Routen und Sicherheitsregeln anzuzeigen. | Q4CY21 |
| NSG-Datenflussprotokolle werden nicht unterstützt. | NSG-Datenflussprotokolle funktionieren nicht für eingehenden Datenverkehr zu einem privaten Endpunkt. | Zurzeit sind keine Informationen hierzu verfügbar. |
| Zeitweilige Trennungen bei Speicherkonten mit zonenredundantem Speicher (ZRS) | Bei Kund*innen, die ein Speicherkonto mit ZRS verwenden, treten möglicherweise wiederkehrende zeitweilige Trennungen auf, selbst wenn die NSG zugelassen wird, die auf das Subnetz des privaten Speicherendpunkts angewendet wird. | September |
| Zeitweilige Trennungen bei Azure Key Vault | Bei Kund*innen, die Azure Key Vault verwenden, treten möglicherweise wiederkehrende zeitweilige Trennungen auf, selbst wenn die NSG zugelassen wird, die auf das Subnetz des privaten Endpunkts für Azure Key Vault angewendet wird. | September |
| Begrenzung der Anzahl der Adresspräfixe pro NSG | Das Verwenden von mehr als 500 Adresspräfixen in einer NSG in einer einzelnen Regel wird nicht unterstützt. | September |
| Flag AllowVirtualNetworkAccess | Kund*innen, die das VNet-Peering für ihr VNet (VNet A) so einstellen, dass das Flag **AllowVirtualNetworkAccess** für die Peeringverbindung mit einem anderen VNet (VNet B) auf „false“ festgelegt ist, können das Tag **VirtualNetwork** nicht verwenden, um Datenverkehr von VNet B abzulehnen, der auf Ressourcen für private Endpunkte zugreift. Sie müssen explizit einen Block für das Adresspräfix von VNet B einfügen, um Datenverkehr an den privaten Endpunkt abzulehnen. | September |
| Dualport-NSG-Regeln werden nicht unterstützt. | Wenn bei NSG-Regeln mehrere Portbereiche verwendet werden, wird bei Zulassungs- und Ablehnungsregeln nur der erste Portbereich berücksichtigt. Regeln mit mehreren Portbereichen sind standardmäßig so konfiguriert, dass alle Ports abgelehnt werden statt nur bestimmte Ports. </br> **Weitere Informationen finden Sie im folgenden Regelbeispiel.** | September |

| Priorität | Quellport | Zielport | Aktion | Effektive Aktion |
| -------- | ----------- | ---------------- | ------ | ---------------- |
| 10 | 10–12 | 10–12 | Allow/Deny | Ein einzelner Portbereich für Quell- und Zielports funktioniert erwartungsgemäß. |
| 10 | 10–12, 13–14 | 14–15, 16–17 | Zulassen | Nur die Quellports 10–12 und die Zielports 14–15 sind zulässig. |
| 10 | 10–12, 13–14 | 120–130, 140–150 | Verweigern | Datenverkehr von allen Quellports an alle Zielports wird abgelehnt, da mehrere Quell- und Zielportbereiche angegeben sind. |
| 10 | 10–12, 13–14 | 120–130 | Verweigern | Datenverkehr von allen Quellports wird nur für die Zielports 120–130 abgelehnt. Es gibt mehrere Quellportbereiche und einen einzelnen Zielportbereich. |

**Tabelle: Beispiele für Dualportregeln**

### <a name="udr"></a>UDR

| Einschränkung | BESCHREIBUNG | Minderung |
| ---------- | ----------- | ---------- |
| Die Quellnetzwerkadressenübersetzung (SNAT) wird immer empfohlen. | Da die Datenebene des privaten Endpunkts variabel ist, wird empfohlen, für Datenverkehr an einen privaten Endpunkt die SNAT zu verwenden, um sicherzustellen, dass der Rückgabedatenverkehr berücksichtigt wird. | Zurzeit sind keine Informationen hierzu verfügbar. |
 
## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu privaten Endpunkten und Private Link finden Sie unter [Was ist Azure Private Link?](private-link-overview.md).
- Informationen zum Einstieg in die Erstellung eines privaten Endpunkts für eine Web-App finden Sie unter [Schnellstart: Erstellen eines privaten Endpunkts mit dem Azure-Portal](create-private-endpoint-portal.md).
