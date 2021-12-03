---
title: Verwaltetes virtuelles Netzwerk und verwaltete private Endpunkte
description: Erfahren Sie mehr über das verwaltete virtuelle Netzwerk und verwaltete private Endpunkte in Azure Data Factory.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions, devx-track-azurepowershell
ms.date: 09/28/2021
ms.openlocfilehash: c4baf3ee8fdb26bd361dfafbaa29953f6d1d13f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469546"
---
# <a name="azure-data-factory-managed-virtual-network"></a>Verwaltetes virtuelles Netzwerk in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel werden das verwaltete virtuelle Netzwerk und verwaltete private Endpunkte in Azure Data Factory erläutert.


## <a name="managed-virtual-network"></a>Verwaltetes virtuelles Netzwerk

Wenn Sie eine Azure Integration Runtime in einem verwalteten virtuellen Azure Data Factory-Netzwerk (VNET) erstellen, wird die Integration Runtime mit dem verwalteten virtuellen Netzwerk bereitgestellt und nutzt private Endpunkte, um sichere Verbindungen mit unterstützten Datenspeichern herzustellen. 

Durch das Erstellen einer Azure IR in einem verwaltetem virtuellen Netzwerk wird sichergestellt, dass der Datenintegrationsprozess isoliert und sicher ist. 

Vorteile der Verwendung eines verwalteten virtuellen Netzwerks:

- Mit einem verwalteten virtuellen Netzwerk können Sie die Verwaltung des virtuellen Netzwerks an Azure Data Factory auslagern. Es ist nicht erforderlich, ein Subnetz für die Azure Integration Runtime zu erstellen, das letztendlich viele private IP-Adressen Ihres virtuellen Netzwerks verwenden könnte, sodass eine vorherige Planung der Netzwerkinfrastruktur erforderlich wäre. 
- Für sichere Datenintegrationen sind keine tief greifenden Azure-Netzwerkkenntnisse erforderlich. Die ersten Schritte mit sicherem ETL sind für Datentechniker im Vergleich viel einfacher. 
- Das verwaltete virtuelle Netzwerk schützt zusammen mit verwalteten privaten Endpunkten vor Datenexfiltration. 

> [!IMPORTANT]
>Derzeit wird das verwaltete virtuelle Netzwerk nur in derselben Region wie Azure Data Factory unterstützt.

> [!Note]
>Für eine vorhandene globale Azure Integration Runtime-Instanz kann keine Umstellung auf eine Azure Integration Runtime-Instanz in einem verwalteten virtuellen Azure Data Factory-Netzwerk durchgeführt werden (und umgekehrt).
 

:::image type="content" source="./media/managed-vnet/managed-vnet-architecture-diagram.png" alt-text="Architektur des verwalteten virtuellen ADF-Netzwerks":::

## <a name="managed-private-endpoints"></a>Verwaltete private Endpunkte

Verwaltete private Endpunkte sind private Endpunkte, die im verwalteten virtuellen Netzwerk von Azure Data Factory erstellt werden und einen privaten Link zu Azure-Ressourcen herstellen. Azure Data Factory verwaltet diese privaten Endpunkte für Sie. 

:::image type="content" source="./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png" alt-text="Neuer verwalteter privater Endpunkt":::

Azure Data Factory unterstützt private Links. Der private Link ermöglicht Ihnen den Zugriff auf Azure-Dienste (PaaS, z. B. Azure Storage, Azure Cosmos DB, Azure Synapse Analytics).

Bei der Verwendung eines privaten Links wird der Datenverkehr zwischen Ihrem Datenspeicher und dem verwalteten virtuellen Netzwerk vollständig über das Microsoft-Backbone-Netzwerk übertragen. Private Link schützt vor einer möglichen Datenexfiltration. Ein privater Link zu einer Ressource wird durch die Erstellung eines privaten Endpunkts eingerichtet.

Der private Endpunkt nutzt eine private IP-Adresse im verwalteten virtuellen Netzwerk, um den Dienst effektiv darin einzubinden. Private Endpunkte werden einer bestimmten Ressource in Azure und nicht dem gesamten Dienst zugeordnet. Kunden können die Konnektivität auf eine bestimmte Ressource beschränken, die von Ihrer Organisation genehmigt wurde. Weitere Informationen finden Sie in der [Dokumentation zu Private Link](../private-link/index.yml).

> [!NOTE]
> Es wird empfohlen, verwaltete private Endpunkte zu erstellen, um eine Verbindung mit all Ihren Azure-Datenquellen herzustellen. 
 
> [!WARNING]
> Wenn für einen PaaS-Datenspeicher (Blob, ADLS Gen2, Azure Synapse Analytics) bereits ein privater Endpunkt erstellt wurde, kann ADF nur mithilfe eines verwalteten privaten Endpunkts darauf zugreifen, selbst wenn er den Zugriff aus allen Netzwerken zulässt. Wenn noch kein privater Endpunkt vorhanden ist, müssen Sie in solchen Szenarien zunächst einen erstellen. 

Wenn Sie einen verwalteten privaten Endpunkt in Azure Data Factory erstellen, wird eine Verbindung mit einem privaten Endpunkt mit dem Status „Ausstehend“ erstellt. Ein Genehmigungsworkflow wird ausgelöst. Der Besitzer der Ressource für private Links ist für die Genehmigung oder Ablehnung der Verbindung verantwortlich.

:::image type="content" source="./media/tutorial-copy-data-portal-private/manage-private-endpoint.png" alt-text="Verwalten eines privaten Endpunkts":::

Wenn der Besitzer die Verbindung genehmigt, wird der private Link eingerichtet. Andernfalls wird der private Link nicht eingerichtet. In beiden Fällen wird der verwaltete private Endpunkt mit dem Status der Verbindung aktualisiert.

:::image type="content" source="./media/tutorial-copy-data-portal-private/approve-private-endpoint.png" alt-text="Genehmigter verwalteter privater Endpunkt":::

Nur ein verwalteter privater Endpunkt im genehmigten Zustand kann Datenverkehr an eine angegebene Ressource für private Links senden.

## <a name="interactive-authoring"></a>Interaktive Erstellung
Interaktive Erstellungsfunktionen werden beispielsweise für Testverbindungen, das Durchsuchen von Ordner- und Tabellenlisten, das Abrufen von Schemas und die Vorschau von Daten verwendet. Sie können die interaktive Erstellung aktivieren, wenn Sie eine Azure Integration Runtime erstellen, die sich in einem per ADF verwalteten virtuellen Netzwerk befindet. Der Back-End-Dienst weist vorab Computeressourcen für die interaktive Erstellung zu. Andernfalls werden Computeressourcen jedes Mal zugewiesen, wenn eine interaktive ausgeführt wird. Dies nimmt mehr Zeit in Anspruch. Die Gültigkeitsdauer für die interaktive Erstellung beträgt 60 Minuten. Das bedeutet, dass das Feature 60 Minuten nach dem letzten interaktiven Erstellungsvorgang deaktiviert wird.

:::image type="content" source="./media/managed-vnet/interactive-authoring.png" alt-text="Interaktive Erstellung":::

## <a name="activity-execution-time-using-managed-virtual-network"></a>Aktivitätsausführungszeit mit verwaltetem virtuellen Netzwerk
Die Warteschlangenzeit der Azure Integration Runtime ist im verwalteten VNet länger als in der globalen Azure Integration Runtime. Der Grund dafür ist, dass nicht ein Serverknoten pro Data Factory reserviert wird und somit vor dem Starten jeder Aktivität eine Aufwärmphase erfolgt. Dies geschieht hauptsächlich beim Beitritt zu einem VNet und nicht in der Azure IR. Für Aktivitäten, die keine Copy-Aktivitäten sind, einschließlich Pipelineaktivitäten und externen Aktivitäten, beträgt die Gültigkeitsdauer (Time To Live, TTL) beim ersten Auslösen 60 Minuten. Innerhalb der TTL ist die Warteschlangenzeit kürzer, da der Knoten bereits aufgewärmt ist. 
> [!NOTE]
> Die Copy-Aktivität bietet noch keine TTL-Unterstützung.

> [!NOTE]
> 2 DIU für die Copy-Aktivität werden in einem verwalteten virtuellen Netzwerk nicht unterstützt.

## <a name="create-managed-virtual-network-via-azure-powershell"></a>Erstellen eines verwalteten virtuellen Netzwerks über Azure PowerShell
```powershell
$subscriptionId = ""
$resourceGroupName = ""
$factoryName = ""
$managedPrivateEndpointName = ""
$integrationRuntimeName = ""
$apiVersion = "2018-06-01"
$privateLinkResourceId = ""

$vnetResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default"
$privateEndpointResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/managedVirtualNetworks/default/managedprivateendpoints/${managedPrivateEndpointName}"
$integrationRuntimeResourceId = "subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/integrationRuntimes/${integrationRuntimeName}"

# Create managed Virtual Network resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${vnetResourceId}" -Properties @{}

# Create managed private endpoint resource
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${privateEndpointResourceId}" -Properties @{
        privateLinkResourceId = "${privateLinkResourceId}"
        groupId = "blob"
    }

# Create integration runtime resource enabled with VNET
New-AzResource -ApiVersion "${apiVersion}" -ResourceId "${integrationRuntimeResourceId}" -Properties @{
        type = "Managed"
        typeProperties = @{
            computeProperties = @{
                location = "AutoResolve"
                dataFlowProperties = @{
                    computeType = "General"
                    coreCount = 8
                    timeToLive = 0
                }
            }
        }
        managedVirtualNetwork = @{
            type = "ManagedVirtualNetworkReference"
            referenceName = "default"
        }
    }

```

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

### <a name="supported-data-sources"></a>Unterstützte Datenquellen
Die folgenden Datenquellen verfügen über native Unterstützung für private Endpunkte und können über Private Link eine Verbindung vom verwalteten virtuellen ADF-Netzwerk aus herstellen.
- Azure Blob Storage (ohne Speicherkonto V1)
- Azure Cognitive Search
- SQL-API von Azure Cosmos DB
- Azure Data Lake Storage Gen2
- Azure Database for MariaDB
- Azure Database for MySQL
- Azure Database for PostgreSQL
- Azure Files (ohne Speicherkonto V1)
- Azure-Schlüsseltresor
- Azure Machine Learning
- Azure Private Link-Dienst
- Azure Purview
- Azure SQL-Datenbank (ohne Azure SQL Managed Instance)
- Azure Synapse Analytics
- Azure Table Storage (ohne Speicherkonto V1)

> [!Note]
> Sie können weiterhin über das öffentliche Netzwerk auf alle Datenquellen zugreifen, die von Data Factory unterstützt werden.

> [!NOTE]
> Da Azure SQL Managed Instance derzeit keinen nativen privaten Endpunkt unterstützt, können Sie darauf vom verwalteten virtuellen Netzwerk aus über den Private Link-Dienst und Load Balancer zugreifen. Weitere Informationen finden Sie unter [Zugreifen auf SQL Managed Instance über ein verwaltetes Data Factory-VNet mithilfe eines privaten Endpunkts](tutorial-managed-virtual-network-sql-managed-instance.md).

### <a name="on-premises-data-sources"></a>Lokale Datenquellen
Informationen zum Zugriff auf lokale Datenquellen aus dem verwalteten VNet mithilfe eines privaten Endpunkts finden Sie im [Tutorial: Zugreifen auf eine lokale SQL Server-Instanz über ein verwaltetes Data Factory-VNet unter Verwendung eines privaten Endpunkts](tutorial-managed-virtual-network-on-premise-sql-server.md).

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>Verwaltetes virtuelles Netzwerk in Azure Data Factory ist in den folgenden Azure-Regionen verfügbar:
Im Allgemeinen ist verwaltetes virtuelles Netzwerk für alle Azure Data Factory-Regionen verfügbar, mit Ausnahme von:
- Indien (Süden)


### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Ausgehende Kommunikation über einen öffentlichen Endpunkt von einem verwaltetem virtuellen ADF-Netzwerk
- Alle Ports sind für die ausgehende Kommunikation geöffnet.
- Azure Storage und Azure Data Lake Gen2 werden für eine Verbindung über einen öffentlichen Endpunkt von einem verwalteten virtuellen ADF-Netzwerk nicht unterstützt.

### <a name="linked-service-creation-of-azure-key-vault"></a>Erstellung eines verknüpften Diensts von Azure Key Vault 
- Wenn Sie einen verknüpften Dienst für Azure Key Vault erstellen, gibt es keinen Verweis auf Azure Integration Runtime. Daher können Sie während der Erstellung eines verknüpften Diensts von Azure Key Vault keinen privaten Endpunkt erstellen. Wenn Sie jedoch einen verknüpften Dienst für Datenspeicher erstellen, der auf den verknüpften Dienst von Azure Key Vault verweist, und dieser verknüpfte Dienst auf Azure Integration Runtime mit aktiviertem verwalteten virtuellen Netzwerk verweist, können Sie während der Erstellung einen privaten Endpunkt für den verknüpften Dienst von Azure Key Vault erstellen. 
- Bei dem Vorgang **Verbindung testen** für den verknüpften Dienst von Azure Key Vault wird nur das URL-Format überprüft. Es werden dabei jedoch keine Netzwerkvorgänge ausgeführt.
- Die Spalte **Privater Endpunkt wird verwendet** ist immer leer, selbst wenn Sie einen privaten Endpunkt für Azure Key Vault erstellen.

### <a name="linked-service-creation-of-azure-hdi"></a>Erstellung eines verknüpften Diensts von Azure HDI
- Die Spalte **Privaten Endpunkt verwenden** wird immer als leer angezeigt, auch wenn Sie einen privaten Endpunkt für HDI mithilfe des Private Link-Diensts und des Lastenausgleichs mit Portweiterleitung erstellen.

:::image type="content" source="./media/managed-vnet/akv-pe.png" alt-text="Privater Endpunkt für Azure Key Vault":::

## <a name="next-steps"></a>Nächste Schritte

- Tutorial: [Erstellen einer Kopierpipeline mithilfe eines verwalteten virtuellen Netzwerks und privater Endpunkte](tutorial-copy-data-portal-private.md) 
- Tutorial: [Erstellen einer Pipeline für den Zuordnungsdatenfluss mithilfe eines verwalteten virtuellen Netzwerks und privater Endpunkte](tutorial-data-flow-private.md)
