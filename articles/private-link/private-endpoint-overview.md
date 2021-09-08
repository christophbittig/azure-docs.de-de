---
title: Was ist privater Endpunkt in Azure?
description: Informationen zum privaten Endpunkt in Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: allensu
ms.openlocfilehash: f816ae15ddba9f56f1b504b2e4ccc52efdc09249
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099974"
---
# <a name="what-is-azure-private-endpoint"></a>Was ist privater Endpunkt in Azure?

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse in Ihrem VNet und bindet den Dienst effektiv in Ihr VNet ein. 

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
|Subnet    |  Das bereitzustellende Subnetz und der Ort, an dem die private IP-Adresse zugewiesen wird. Anforderungen an das Subnetz finden Sie im Abschnitt „Einschränkungen“ in diesem Artikel.         |
|Private Link-Ressource    |   Die Private Link-Ressource, mit der anhand der Liste verfügbarer Typen eine Verbindung über die Ressourcen-ID oder den Alias hergestellt werden soll. Für den gesamten Datenverkehr an diese Ressource wird ein eindeutiger Netzwerkbezeichner generiert.       |
|Unterressource des Ziels   |      Die Unterressource, mit der eine Verbindung hergestellt wird. Jeder Private Link-Ressourcentyp verfügt über verschiedene Optionen, die je nach Präferenz ausgewählt werden können.    |
|Methode zur Genehmigung der Verbindung    |  Automatisch oder manuell. Abhängig von den Berechtigungen gemäß rollenbasierter Zugriffssteuerung kann Ihr privater Endpunkt automatisch genehmigt werden. Wenn Sie versuchen, sich mit einer Private Link-Ressource ohne Azure rollenbasierte Zugriffssteuerung zu verbinden, verwenden Sie die manuelle Methode, um dem Besitzer der Ressource zu ermöglichen, die Verbindung zu genehmigen.        |
|Anforderungsnachricht     |  Sie können eine Nachricht für angeforderte Verbindungen angeben, die manuell genehmigt werden sollen. Mithilfe dieser Nachricht kann eine bestimmte Anforderung identifiziert werden.        |
|Verbindungsstatus   |   Eine schreibgeschützte Eigenschaft, die angibt, ob der private Endpunkt aktiv ist. Nur private Endpunkte in genehmigtem Zustand können zum Senden von Datenverkehr verwendet werden. Weitere verfügbare Status: <br>-**Genehmigt**: Die Verbindung wurde automatisch oder manuell genehmigt und ist zur Verwendung bereit.</br><br>-**Ausstehend**: Die Verbindung wurde manuell erstellt, wobei die Genehmigung des Besitzers der Private Link-Ressource aussteht.</br><br>-**Abgelehnt**: Die Verbindung wurde vom Besitzer der Private Link-Ressource abgelehnt.</br><br>-**Getrennt**: Die Verbindung wurde vom Besitzer der Private Link-Ressource entfernt. Der private Endpunkt dient nur noch Informationszwecken und sollte zur Bereinigung gelöscht werden. </br>|

Im Folgenden finden Sie einige wichtige Details zu privaten Endpunkten: 
- Ein privater Endpunkt ermöglicht die Verbindung zwischen den Consumern im selben VNet, in regionalen VNets mit Peering, globalen VNets mit Peering und lokalen Standorten mit [VPN](https://azure.microsoft.com/services/vpn-gateway/) oder [Express Route](https://azure.microsoft.com/services/expressroute/) und Diensten auf Basis von Private Link.
 
- Netzwerkverbindungen können nur von Clients initiiert werden, die eine Verbindung mit dem privaten Endpunkt herstellen. Dienstanbieter verfügen nicht über eine Routingkonfiguration, um Verbindungen mit Dienstverbrauchern herzustellen. Verbindungen können nur in einer Richtung eingerichtet werden.

- Beim Erstellen eines privaten Endpunkts wird für die Lebensdauer der Ressource eine schreibgeschützte Netzwerkschnittstelle eingerichtet. Der Schnittstelle wird eine private dynamische IP-Adresse aus dem Subnetz zugewiesen, dass der privaten Link-Ressource zugeordnet ist. Der Wert der privaten IP-Adresse bleibt über den gesamten Lebenszyklus des privaten Endpunkts unverändert.
 
- Der private Endpunkt muss in derselben Region und im selben Abonnement wie das virtuelle Netzwerk bereitgestellt werden. 
 
- Die Private Link-Ressource kann in einer anderen Region als das virtuelle Netzwerk und der private Endpunkt bereitgestellt werden.
 
- Mehrere private Endpunkte können mithilfe derselben Private Link-Ressource erstellt werden. Für ein einzelnes Netzwerk mit einer herkömmlichen DNS-Serverkonfiguration wird empfohlen, einen einzigen privaten Endpunkt für eine bestimmte Private Link-Ressource zu verwenden, um doppelte Einträge oder Konflikte bei der DNS-Auflösung zu vermeiden. 
 
- Mehrere private Endpunkte können im gleichen oder in verschiedenen Subnetzen innerhalb desselben virtuellen Netzwerks erstellt werden. Die Anzahl der privaten Endpunkte, die Sie in einem Abonnement anlegen können, ist begrenzt. Ausführliche Informationen finden Sie im Artikel zu  [Azure-Grenzwerten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- Das Abonnement der Private Link-Ressource muss ebenfalls bei Microsoft registriert sein. Netzwerkressourcenanbieter. Weitere Informationen finden Sie unter  [Azure-Ressourcenanbieter](../azure-resource-manager/management/resource-providers-and-types.md).

 
## <a name="private-link-resource"></a>Private Link-Ressource 
Eine Private Link-Ressource ist das Ziel eines bestimmten privaten Endpunkts. In der folgenden Tabelle sind die verfügbaren Ressourcen für private Endpunkte aufgeführt: 
 
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
Bei Verwenden privater Endpunkte für Azure-Dienste wird der Datenverkehr zu einer bestimmten Private Link-Ressource abgesichert. Die Plattform führt eine Zugriffssteuerung durch, um zu bestätigen, dass Netzwerkverbindungen nur die angegebene Private Link-Ressource erreichen. Für den Zugriff auf mehr Ressourcen innerhalb desselben Azure-Diensts sind zusätzliche private Endpunkte erforderlich. 
 
Sie können Ihre Workloads vollständig vom Zugriff auf öffentliche Endpunkte ausschließen, um sich mit einem unterstützten Azure-Dienst zu verbinden. Dieses Steuerungsinstrument bietet Ihren Ressourcen eine zusätzliche Netzwerksicherheitsebene, indem es einen integrierten Exfiltrationsschutz bereitstellt. Dieser Schutz verhindert den Zugriff auf andere Ressourcen, die im gleichen Azure-Dienst gehostet werden. 
 
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

### <a name="connecting-using-alias"></a>Verbindungsherstellung per Alias
Der Alias ist ein eindeutiger Moniker, der generiert wird, wenn der Dienstbesitzer den Private Link-Dienst hinter einem Standardlastenausgleich erstellt. Der Dienstbesitzer kann diesen Alias mit seinen Consumern offline gemeinsam nutzen. Verbraucher können eine Verbindung mit dem Private Link-Dienst anfordern, indem sie entweder den Ressourcen-URI oder Alias verwenden. Wenn Sie eine Verbindung per Alias herstellen möchten, müssen Sie einen privaten Endpunkt mithilfe der manuellen Verbindungsgenehmigungsmethode erstellen. Um die manuelle Verbindungsgenehmigungsmethode zu verwenden, legen Sie den Parameter für die manuelle Anforderung während des Flows zum Erstellen eines privaten Endpunkts auf TRUE fest. Ausführliche Informationen finden Sie unter [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) und [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create). 

## <a name="dns-configuration"></a>DNS-Konfiguration 
Wenn Sie eine Verbindung mit einer privaten Link-Ressource über einen voll qualifizierten Domänennamen (FQDN) als Teil der Verbindungszeichenfolge herstellen, ist es wichtig, Ihre DNS-Einstellungen richtig zu konfigurieren, damit sie in der angegebenen privaten IP-Adresse aufgelöst werden. Bestehende Azure-Dienste verfügen möglicherweise bereits über eine DNS-Konfiguration, die beim Herstellen einer Verbindung über einen öffentlichen Endpunkt verwendet werden kann. Diese Konfiguration muss überschrieben werden, um eine Verbindung mithilfe Ihres privaten Endpunkts herzustellen. 
 
Die Netzwerkschnittstelle, die dem privaten Endpunkt zugeordnet ist, enthält alle Informationen, die zur Konfiguration Ihres DNS erforderlich sind, einschließlich FQDN und privater IP-Adressen, die in einer bestimmten privaten Link-Ressource angegeben wurden. 

Ausführliche Informationen zu den Empfehlungen um DNS für private Endpunkte zu konfigurieren, finden Sie in dem Artikel [DNS-Konfiguration für private Endpunkte](private-endpoint-dns.md).
 
## <a name="limitations"></a>Einschränkungen
 
Die folgende Tabelle enthält eine Liste der bekannten Einschränkungen bei Verwendung privater Endpunkte: 

| Einschränkung | BESCHREIBUNG |Minderung |
| --------- | --------- | --------- |
| Der Datenverkehr, der über eine benutzerdefinierte Route an einen privaten Endpunkt geleitet wird, kann asymmetrisch sein. | Bei der Rückgabe von Datenverkehr von einem privaten Endpunkt wird ein virtuelles NVA umgangen, und es wird versucht, zur Quell-VM zurückzukehren. | Für den gesamten Datenverkehr, der über eine UDR an einen privaten Endpunkt bestimmt ist, wird empfohlen, SNAT-Datenverkehr am NVA zu verwenden, um symmetrisches Routing sicherzustellen.  |

> [!IMPORTANT]
> Die NSG- und UDR-Unterstützung für private Endpunkte befindet sich in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines privaten Endpunkts für Azure Web-Apps im Portal](create-private-endpoint-portal.md)
- [Erstellen eines privaten Endpunkts für Azure Web-Apps mit PowerShell](create-private-endpoint-powershell.md)
- [Erstellen eines privaten Endpunkts für Azure Web-Apps mit CLI](create-private-endpoint-cli.md)
- [Erstellen eines privaten Endpunkts für das Speicherkonto im Portal](./tutorial-private-endpoint-storage-portal.md)
- [Erstellen eines privaten Endpunkts für das Azure Cosmos-Konto im Portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Erstellen eines eigenen Private Link-Diensts mit Azure PowerShell](create-private-link-service-powershell.md)
- [Erstellen eines eigenen Private Link für Azure Database for PostgreSQL – Einzelserver im Portal](../postgresql/howto-configure-privatelink-portal.md)
- [Erstellen eines eigenen Private Link für Azure Database for PostgreSQL – Einzelserver über die Befehlszeilenschnittstelle](../postgresql/howto-configure-privatelink-cli.md)
- [Erstellen eines eigenen Private Link für Azure Database for MySQL im Portal](../mysql/howto-configure-privatelink-portal.md)
- [Erstellen eines eigenen Private Link für Azure Database for MySQL über die Befehlszeilenschnittstelle](../mysql/howto-configure-privatelink-cli.md)
- [Erstellen eines eigenen Private Link für Azure Database for MariaDB im Portal](../mariadb/howto-configure-privatelink-portal.md)
- [Erstellen eines eigenen Private Link für Azure Database for MariaDB über die Befehlszeilenschnittstelle](../mariadb/howto-configure-privatelink-cli.md)
- [Erstellen eines eigenen Private Link für Azure Key Vault im Portal und mit der CLI](../key-vault/general/private-link-service.md)
