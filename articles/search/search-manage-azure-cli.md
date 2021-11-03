---
title: Azure CLI-Skripts mit dem Modul „az search“
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie mit der Azure CLI einen Azure Cognitive Search-Dienst erstellen und konfigurieren. Sie können einen Dienst zentral hoch- oder herunterskalieren, Administrator- und Abfrage-API-Schlüssel verwalten und Systeminformationen abfragen.
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: e714fe12ceff3bd80cc9cf4f0df106a55a064d9f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084349"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Verwalten Ihres Azure Cognitive Search-Diensts mit der Azure CLI
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](search-manage-azure-cli.md)
> * [REST-API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Sie können Azure CLI-Cmdlets und -Skripts unter Windows, macOS oder Linux oder in [Azure Cloud Shell](../cloud-shell/overview.md) ausführen, um Azure Cognitive Search zu erstellen und zu konfigurieren. Das Modul [**az search**](/cli/azure/search) erweitert die [Azure CLI](/cli/) um vollständige Parität mit den [REST-APIs für die Azure Search-Verwaltung](/rest/api/searchmanagement) und ermöglicht Folgendes:

> [!div class="checklist"]
> * [Auflisten von Suchdiensten in einem Abonnement](#list-search-services)
> * [Zurückgeben von Dienstinformationen](#get-search-service-information)
> * [Erstellen oder Löschen eines Diensts](#create-or-delete-a-service)
> * [Erstellen eines Diensts mit einem privaten Endpunkt](#create-a-service-with-a-private-endpoint)
> * [Erneutes Generieren von Administrator-API-Schlüsseln](#regenerate-admin-keys)
> * [Erstellen oder Löschen von Abfrage-API-Schlüsseln](#create-or-delete-query-keys)
> * [Zentrales Hoch- oder Herunterskalieren mit Replikaten und Partitionen](#scale-replicas-and-partitions)
> * [Erstellen einer gemeinsam genutzten Private Link-Ressource](#create-a-shared-private-link-resource)

Gelegentlich werden Fragen zu Aufgaben gestellt, die *nicht* in der oben stehenden Liste enthalten sind. Derzeit kann weder das Modul **az search** noch die REST-API für die Verwaltung verwendet werden, um einen Servernamen, eine Region oder eine Ebene zu ändern. Dedizierte Ressourcen werden beim Erstellen eines Diensts zugeordnet. Daher ist zum Ändern der zugrunde liegenden Hardware (Standort oder Knotentyp) ein neuer Dienst erforderlich. Ebenso gibt es keine Tools oder APIs für die Übertragung von Inhalten, wie z. B. einem Index, aus einem Dienst in einen anderen.

Innerhalb eines Diensts erfolgen die Erstellung und Verwaltung von Inhalten über die [Azure Search-Dienste-REST-API](/rest/api/searchservice/) oder das [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Zwar sind keine dedizierten PowerShell-Befehle für Inhalt vorhanden, Sie können jedoch Skripts schreiben, die REST- oder .NET-APIs zum Erstellen und Laden von Indizes aufrufen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Auflisten von Diensten in einem Abonnement

Die folgenden Befehle stammen von [**az resource**](/cli/azure/resource) und geben Informationen zu vorhandenen Ressourcen und Diensten zurück, die bereits in Ihrem Abonnement bereitgestellt sind. Wenn Sie nicht wissen, wie viele Suchdienste bereits erstellt wurden, geben die folgenden Befehle diese Information zurück und ersparen Ihnen dadurch das Aufrufen des Portals.

Der erste Befehl gibt alle Suchdienste zurück.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Geben Sie aus der Liste der Dienste Informationen zu einer bestimmten Ressource zurück.

```azurecli-interactive
az resource list --name <search-service-name>
```

## <a name="list-all-az-search-commands"></a>Auflisten aller Befehle für „az search“

Sie können Informationen zu den in [**az search**](/cli/azure/search) verfügbaren Untergruppen und Befehlen innerhalb der CLI anzeigen. Alternativ können Sie sich die [Dokumentation](/cli/azure/search) ansehen.

Führen Sie den folgenden Befehl aus, um die in `az search` verfügbaren Untergruppen anzuzeigen:

```azurecli-interactive
az search --help
```

Die Antwort sollte in etwa wie die folgende Ausgabe aussehen:

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

In den einzelnen Untergruppen sind jeweils mehrere Befehle verfügbar. Die verfügbaren Befehle für die Untergruppe `service` können durch Ausführen der folgenden Zeile angezeigt werden:

```azurecli-interactive
az search service --help
```

Sie können auch die für einen bestimmten Befehl verfügbaren Argumente anzeigen:

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Abrufen von Suchdienstinformationen

Wenn Sie die Ressourcengruppe kennen, die Ihren Suchdienst enthält, können Sie [**az search service show**](/cli/azure/search/service#az_search_service_show) ausführen, um die Dienstdefinition mit Name, Region und Tarif sowie mit der Anzahl von Replikaten und Partitionen zurückzugeben. Geben Sie für diesen Befehl die Ressourcengruppe an, die den Suchdienst enthält.

```azurecli-interactive
az search service show --name <service-name> --resource-group <search-service-resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Erstellen oder Löschen eines Diensts

Verwenden Sie zum [Erstellen eines neuen Suchdiensts](search-create-service-portal.md) den Befehl [**az search service create**](/cli/azure/search/service#az_search_service_create):

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Die Ergebnisse sollten in etwa wie in der folgenden Ausgabe aussehen:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Erstellen eines Diensts mit IP-Regeln

Je nach Ihren Sicherheitsanforderungen kann es ratsam sein, einen Suchdienst mit einer [konfigurierten IP-Firewall](service-configure-firewall.md) zu erstellen. Übergeben Sie hierzu die öffentlichen IP-Adressen (v4) oder die CIDR-Bereiche wie unten gezeigt an das Argument `ip-rules`. Regeln müssen durch ein Komma (`,`) oder Semikolon (`;`) getrennt werden.

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Erstellen eines Diensts mit einer systemseitig zugewiesenen verwalteten Identität

In einigen Fällen, z. B. beim [Verwenden einer verwalteten Identität für die Verbindungsherstellung mit einer Datenquelle](search-howto-managed-identities-storage.md), müssen Sie [systemseitig zugewiesene verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) aktivieren. Fügen Sie dem Befehl hierfür `--identity-type SystemAssigned` hinzu.

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Erstellen eines Diensts mit einem privaten Endpunkt

[Private Endpunkte](../private-link/private-endpoint-overview.md) für Azure Cognitive Search ermöglichen, dass ein Client in einem virtuellen Netzwerk über eine [private Verbindung](../private-link/private-link-overview.md) sicher auf Daten in einem Suchindex zugreifen kann. Der private Endpunkt verwendet eine IP-Adresse aus dem [Adressraum des virtuellen Netzwerks](../virtual-network/ip-services/private-ip-addresses.md) für Ihren Suchdienst. Der Netzwerkdatenverkehr zwischen dem Client und dem Suchdienst wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt. Ausführlichere Informationen finden Sie in der Dokumentation zum [Erstellen eines privaten Endpunkts für Azure Cognitive Search](service-create-private-endpoint.md).

Im folgenden Beispiel wird veranschaulicht, wie Sie einen Suchdienst mit einem privaten Endpunkt erstellen.

Stellen Sie zunächst einen Suchdienst bereit, für den `PublicNetworkAccess` auf `Disabled` festgelegt ist.

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Erstellen Sie als Nächstes ein virtuelles Netzwerk und den privaten Endpunkt.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <vnet-resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <vnet-resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <search-service-resource-group-name> \
    --name <search-service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <private-endpoint-resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Erstellen Sie abschließend eine private DNS-Zone. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <private-dns-resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <private-dns-resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <private-endpoint-resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Weitere Informationen zum Erstellen privater Endpunkte in PowerShell finden Sie in [dieser Schnellstartanleitung zu Private Link](../private-link/create-private-endpoint-cli.md).

### <a name="manage-private-endpoint-connections"></a>Verwalten von Verbindungen mit dem privaten Endpunkt

Zusätzlich zur Erstellung einer Verbindung mit dem privaten Endpunkt können Sie auch die Vorgänge `show`, `update` und `delete` für die Verbindung durchführen.

Verwenden Sie [**az search private-endpoint-connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show), um eine Verbindung mit einem privaten Endpunkt abzurufen und den Status anzuzeigen.

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

Zum Aktualisieren der Verbindung können Sie [**az search private-endpoint-connection update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update) verwenden. Im folgenden Beispiel wird eine Verbindung mit dem privaten Endpunkt auf „Verweigert“ (Rejected) festgelegt:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Zum Löschen der Verbindung mit dem privaten Endpunkt können Sie [**az search private-endpoint-connection delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete) verwenden.

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Erneutes Generieren von Administratorschlüsseln

Verwenden Sie [**az search admin-key renew**](/cli/azure/search/admin-key#az_search_admin_key_renew), um ein Rollover für administratorbezogene [API-Schlüssel](search-security-api-keys.md) durchzuführen. Mit jedem Dienst werden zwei Administratorschlüssel für den authentifizierten Zugriff erstellt. Schlüssel sind für jede Anforderung erforderlich. Beide Administratorschlüssel sind funktionell gleichwertig und gewähren vollständigen Schreibzugriff auf einen Suchdienst mit der Möglichkeit, alle Informationen abrufen oder ein beliebiges Objekt zu erstellen und zu löschen. Es sind zwei Schlüssel vorhanden, sodass Sie den einen verwenden können, während der andere ersetzt wird. 

Sie können immer nur jeweils einen Schlüssel erneut generieren, der entweder als `primary`- oder `secondary`-Schlüssel angegeben ist. Um einen ununterbrochenen Dienst zu gewährleisten, denken Sie daran, den gesamten Clientcode so zu aktualisieren, dass während des Rollovers des primären Schlüssels ein sekundärer Schlüssel verwendet wird. Vermeiden Sie das Ändern der Schlüssel, während Vorgänge ausgeführt werden.

Wenn Sie Schlüssel ohne Aktualisierung des Clientcodes erneut generieren, treten bei Anforderungen, bei denen der alte Schlüssel verwendet wird, Fehler auf. Durch das erneute Generieren aller neuen Schlüssel werden Sie nicht dauerhaft vom Dienst ausgesperrt, und Sie können weiterhin über das Portal auf den Dienst zugreifen. Nachdem Sie primäre und sekundäre Schlüssel erneut generiert haben, können Sie den Clientcode zur Verwendung der neuen Schlüssel aktualisieren, und die Vorgänge werden entsprechend fortgesetzt.

Werte für die API-Schlüssel werden vom Dienst generiert. Sie können keinen benutzerdefinierten Schlüssel für die Verwendung in Azure Cognitive Search bereitstellen. Ebenso ist kein benutzerdefinierter Name für Administrator-API-Schlüssel vorhanden. Verweise auf den Schlüssel sind feste Zeichenfolgen, entweder `primary` oder `secondary`. 

```azurecli-interactive
az search admin-key renew \
    --resource-group <search-service-resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln. Es werden beide Schlüssel zurückgegeben, obwohl Sie immer nur jeweils einen ändern.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Erstellen oder Löschen von Abfrageschlüsseln

Mit [**az search query-key create**](/cli/azure/search/query-key#az_search_query_key_create) können abfragebezogene [API-Schlüssel](search-security-api-keys.md) für schreibgeschützten Zugriff von Client-Apps auf einen Azure Cognitive Search-Index erstellt werden. Abfrageschlüssel werden zum Authentifizieren bei einem bestimmten Index für das Abrufen von Suchergebnissen verwendet. Abfrageschlüssel gewähren keinen schreibgeschützten Zugriff auf andere Elemente im Dienst, z.B. Index, Datenquelle oder Indexer.

Sie können keinen Schlüssel für die Verwendung in Azure Cognitive Search bereitstellen. API-Schlüssel werden vom Dienst generiert.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <search-service-resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Skalieren von Replikaten und Partitionen

Zum [Erhöhen oder Verringern der Anzahl von Replikaten und Partitionen](search-capacity-planning.md) kann [**az search service update**](/cli/azure/search/service#az_search_service_update) verwendet werden. Durch eine erhöhte Anzahl von Replikaten oder Partitionen erhöht sich auch Ihre Rechnung, die sowohl feste als auch variable Gebühren umfasst. Wenn Sie vorübergehend zusätzliche Verarbeitungsleistung benötigen, können Sie die Anzahl der Replikate und Partitionen zum Bewältigen der Workload erhöhen. Der Überwachungsbereich auf der Übersichtsseite des Portals weist Kacheln für die Abfragewartezeit, Abfragen pro Sekunde und Drosselung auf, die angeben, ob die aktuelle Kapazität ausreichend ist.

Das Hinzufügen oder Entfernen von Ressourcen kann eine Weile dauern. Anpassungen der Kapazität finden im Hintergrund statt, damit bestehende Workloads fortgesetzt werden können. Zusätzliche Kapazität wird für eingehende Anforderungen verwendet, sobald sie bereitsteht, und erfordert keine zusätzliche Konfiguration. 

Das Entfernen von Kapazität kann zu einer Unterbrechung führen. Es wird empfohlen, vor dem Verringern der Kapazität alle Indizierungs- und Indexeraufträge anzuhalten, um verworfene Anforderungen zu vermeiden. Wenn das nicht möglich ist, sollten Sie die Kapazität schrittweise verringern, jeweils ein Replikat und eine Partition nach der anderen, bis das neue Zielniveau erreicht ist.

Nachdem Sie den Befehl gesendet haben, gibt es keine Möglichkeit, ihn während der Ausführung zu beenden. Sie müssen warten, bis der Befehl abgeschlossen ist, bevor Sie die jeweilige Anzahl überarbeiten können.

```azurecli-interactive
az search service update \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Neben der Replikat- und Partitionsanzahl können Sie auch `ip-rules`, `public-access` und `identity-type` aktualisieren.

## <a name="create-a-shared-private-link-resource"></a>Erstellen einer gemeinsam genutzten Private Link-Ressource

Private Endpunkte von gesicherten Ressourcen, die über Azure Cognitive Search-APIs erstellt wurden, werden als *freigegebene Private Link-Ressourcen* bezeichnet. Der Grund: Der Zugriff auf eine Ressource (z. B. ein Speicherkonto), die in den [Azure Private Link-Dienst](https://azure.microsoft.com/services/private-link/) integriert wurde, wird von Ihnen „freigegeben“.

Wenn Sie einen Indexer zum Indizieren von Daten in Azure Cognitive Search verwenden und sich Ihre Datenquelle in einem privaten Netzwerk befindet, können Sie eine ausgehende [private Endpunktverbindung](../private-link/private-endpoint-overview.md) erstellen, um die Daten zu erreichen.

Eine vollständige Liste mit den Azure-Ressourcen, für die Sie ausgehende private Endpunkte für Azure Cognitive Search erstellen können, und die zugehörigen Werte für die **Gruppen-ID** finden Sie [hier](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis).

Verwenden Sie [**az search shared-private-link-resource create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list), um die gemeinsam genutzte Private Link-Ressource zu erstellen. Beachten Sie hierbei, dass für die Datenquelle ggf. einige Konfigurationsschritte ausgeführt werden müssen, bevor dieser Befehl ausgeführt wird.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Verwenden Sie [**az search shared-private-link-resource list**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list), um die gemeinsam genutzten Private Link-Ressourcen abzurufen und den Status anzuzeigen.

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

Sie müssen die Verbindung mit dem folgenden Befehl genehmigen, bevor sie verwendet werden kann. Die ID der Verbindung mit dem privaten Endpunkt muss von der untergeordneten Ressource aus abgerufen werden. In diesem Fall wird die Verbindungs-ID über „az storage“ abgerufen.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Verwenden Sie [**az search shared-private-link-resource delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete), um die gemeinsam genutzte Private Link-Ressource zu löschen.

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

Ausführliche Informationen zum Einrichten von gemeinsam genutzten Private Link-Ressourcen finden Sie unter [Erstellen von Indexerverbindungen über einen privaten Endpunkt](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie einen [Index](search-what-is-an-index.md), verwenden Sie das Portal, REST-APIs oder das .NET SDK zum [Abfragen eines Index](search-query-overview.md).

* [Erstellen eines Indexes für Azure Cognitive Search im Azure-Portal](search-get-started-portal.md)
* [Einrichten eines Indexers zum Laden von Daten anderer Dienste](search-indexer-overview.md)
* [Abfragen eines Azure Cognitive Search-Indexes per Suchexplorer im Azure-Portal](search-explorer.md)
* [Verwendung von Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md)