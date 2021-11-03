---
title: PowerShell-Skripts mit Az.Search-Modul
titleSuffix: Azure Cognitive Search
description: Erstellen und konfigurieren Sie einen Azure Cognitive Search-Dienst mit PowerShell. Sie können einen Dienst zentral hoch- oder herunterskalieren, Administrator- und Abfrage-API-Schlüssel verwalten und Systeminformationen abfragen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0dea6bebd20ca5071a4c1395d5835f31c80e90c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048100"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Verwalten des Azure Cognitive Search-Diensts mit PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](search-manage-azure-cli.md)
> * [REST-API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Sie können PowerShell-Cmdlets und -Skripts unter Windows oder Linux oder in [Azure Cloud Shell](../cloud-shell/overview.md) ausführen, um Azure Cognitive Search zu erstellen und zu konfigurieren. Das **Az.Search**-Modul erweitert [Azure PowerShell](/powershell/) um vollständige Parität mit den [Search-Verwaltungs-REST-APIs](/rest/api/searchmanagement) und die Fähigkeit, die folgenden Aufgaben auszuführen:

> [!div class="checklist"]
> * [Auflisten von Suchdiensten in einem Abonnement](#list-search-services)
> * [Zurückgeben von Dienstinformationen](#get-search-service-information)
> * [Erstellen oder Löschen eines Diensts](#create-or-delete-a-service)
> * [Erstellen eines Diensts mit einem privaten Endpunkt](#create-a-service-with-a-private-endpoint)
> * [Erneutes Generieren von Administrator-API-Schlüsseln](#regenerate-admin-keys)
> * [Erstellen oder Löschen von Abfrage-API-Schlüsseln](#create-or-delete-query-keys)
> * [Zentrales Hoch- oder Herunterskalieren mit Replikaten und Partitionen](#scale-replicas-and-partitions)
> * [Erstellen einer gemeinsam genutzten Private Link-Ressource](#create-a-shared-private-link-resource)

Gelegentlich werden Fragen zu Aufgaben gestellt, die *nicht* in der oben stehenden Liste enthalten sind. Derzeit können Sie weder das **Az.Search**-Modul noch die Verwaltungs-REST-API verwenden, um einen Servernamen, eine Region oder eine Ebene zu ändern. Dedizierte Ressourcen werden beim Erstellen eines Diensts zugeordnet. Daher ist zum Ändern der zugrunde liegenden Hardware (Standort oder Knotentyp) ein neuer Dienst erforderlich. Ebenso gibt es keine Tools oder APIs für die Übertragung von Inhalten, wie z. B. einem Index, aus einem Dienst in einen anderen.

Innerhalb eines Diensts erfolgt die Erstellung und Verwaltung von Inhalten über die [Azure Search-Dienste-REST-API](/rest/api/searchservice/) oder das [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). Zwar sind keine dedizierten PowerShell-Befehle für Inhalt vorhanden, doch können Sie ein PowerShell-Skript schreiben, das REST- oder .NET-APIs zum Erstellen und Laden von Indizes aufruft.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Überprüfen von Versionen und Laden von Modulen

Die Beispiele in diesem Artikel sind interaktiv und erfordern erhöhte Berechtigungen. Azure PowerShell (das **Az**-Modul) muss installiert sein. Weitere Informationen finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>Überprüfen der PowerShell-Version (5.1 oder höher)

Auf jedem unterstützten Betriebssystem muss die lokale PowerShell-Version 5.1 oder höher sein.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Laden von Azure PowerShell

Wenn Sie nicht sicher sind, ob **Az** installiert ist, führen Sie den folgenden Befehl als Überprüfungsschritt aus. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Bei einigen Systemen werden Module nicht automatisch geladen. Wenn Sie als Reaktion auf den vorherigen Befehl eine Fehlermeldung erhalten, versuchen Sie, das Modul zu laden. Wenn das fehlschlägt, kehren Sie zu den Installationsanweisungen zurück, um festzustellen, ob Sie einen Schritt übersehen haben.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Herstellen einer Verbindung mit Azure mit einem Browser-Anmeldetoken

Mit Ihren Anmeldeinformationen für das Portal können Sie eine Verbindung mit einem Abonnement in PowerShell herstellen. Als Alternative steht die [nicht interaktive Authentifizierung mit einem Dienstprinzipal](../active-directory/develop/howto-authenticate-service-principal-powershell.md) zur Verfügung.

```azurepowershell-interactive
Connect-AzAccount
```

Wenn Sie über mehrere Azure-Abonnements verfügen, legen Sie Ihr Azure-Abonnement fest. Führen Sie den folgenden Befehl aus, um eine Liste Ihrer aktuellen Abonnements anzuzeigen.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Führen Sie den folgenden Befehl aus, um das Abonnement anzugeben. Im folgenden Beispiel ist der Name des Abonnements `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Auflisten von Diensten in einem Abonnement

Die folgenden Befehle stammen von [**Az.Resources**](/powershell/module/az.resources) und geben Informationen zu vorhandenen Ressourcen und Diensten zurück, die bereits in Ihrem Abonnement bereitgestellt sind. Wenn Sie nicht wissen, wie viele Suchdienste bereits erstellt wurden, geben die folgenden Befehle diese Information zurück und ersparen Ihnen dadurch das Aufrufen des Portals.

Der erste Befehl gibt alle Suchdienste zurück.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Geben Sie aus der Liste der Dienste Informationen zu einer bestimmten Ressource zurück.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importieren von Az.Search

Befehle von [**Az.Search**](/powershell/module/az.search) sind erst nach dem Laden des Moduls verfügbar.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Auflisten aller Az.Search-Befehle

Geben Sie als Überprüfungsschritt eine Liste der im Modul bereitgestellten Befehle aus.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

Falls Sie eine ältere Version des Pakets verwenden, sollten Sie das Modul aktualisieren, um die neuesten Funktionen nutzen zu können.

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>Abrufen von Suchdienstinformationen

Nachdem **Az.Search** importiert wurde und Ihnen die Ressourcengruppe mit Ihrem Suchdienst bekannt ist, führen Sie [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice) aus, um die Dienstdefinition zurückzugeben. Dazu gehören Name, Region und Tarif sowie die Anzahl von Replikaten und Partitionen. Geben Sie für diesen Befehl die Ressourcengruppe an, die den Suchdienst enthält.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Erstellen oder Löschen eines Diensts

Mit [**New-AzSearchService**](/powershell/module/az.search/new-azsearchservice) wird [ein neuer Suchdienst erstellt](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>Erstellen eines Diensts mit IP-Regeln

Je nach Ihren Sicherheitsanforderungen kann es ratsam sein, einen Suchdienst mit einer [konfigurierten IP-Firewall](service-configure-firewall.md) zu erstellen. Definieren Sie hierfür zunächst die IP-Regeln, und übergeben Sie sie anschließend wie unten gezeigt an den Parameter `IPRuleList`.

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Erstellen eines Diensts mit einer systemseitig zugewiesenen verwalteten Identität

In einigen Fällen, z. B. beim [Verwenden einer verwalteten Identität für die Verbindungsherstellung mit einer Datenquelle](search-howto-managed-identities-storage.md), müssen Sie [systemseitig zugewiesene verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) aktivieren. Fügen Sie dem Befehl hierfür `-IdentityType SystemAssigned` hinzu.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Erstellen eines Diensts mit einem privaten Endpunkt

[Private Endpunkte](../private-link/private-endpoint-overview.md) für Azure Cognitive Search ermöglichen, dass ein Client in einem virtuellen Netzwerk über eine [private Verbindung](../private-link/private-link-overview.md) sicher auf Daten in einem Suchindex zugreifen kann. Der private Endpunkt verwendet eine IP-Adresse aus dem [Adressraum des virtuellen Netzwerks](../virtual-network/ip-services/private-ip-addresses.md) für Ihren Suchdienst. Der Netzwerkdatenverkehr zwischen dem Client und dem Suchdienst wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt. Weitere Details finden Sie unter [Erstellen eines privaten Endpunkts für eine sichere Verbindung mit Azure Cognitive Search](service-create-private-endpoint.md).

Im folgenden Beispiel wird veranschaulicht, wie Sie einen Suchdienst mit einem privaten Endpunkt erstellen. 

Stellen Sie zunächst einen Suchdienst bereit, für den `PublicNetworkAccess` auf `Disabled` festgelegt ist.

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <search-service-resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

Erstellen Sie als Nächstes ein virtuelles Netzwerk, eine private Netzwerkverbindung und den privaten Endpunkt.

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <vm-resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <private-endpoint-resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

Erstellen Sie abschließend eine private DNS-Zone. 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <private-dns-resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <private-dns-link-resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <private-dns-zone-resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

Weitere Informationen zum Erstellen privater Endpunkte in PowerShell finden Sie in [dieser Schnellstartanleitung zu Private Link](../private-link/create-private-endpoint-powershell.md).

### <a name="manage-private-endpoint-connections"></a>Verwalten von Verbindungen mit dem privaten Endpunkt

Zusätzlich zur Erstellung einer Verbindung mit dem privaten Endpunkt können Sie auch die Vorgänge `Get`, `Set` und `Remove` für die Verbindung durchführen.

[Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) wird verwendet, um eine Verbindung mit dem privaten Endpunkt abzurufen und den Status anzuzeigen.

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name>
```

[Set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) wird verwendet, um die Verbindung zu aktualisieren. Im folgenden Beispiel wird eine Verbindung mit dem privaten Endpunkt auf „Verweigert“ (Rejected) festgelegt:

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

[Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) wird verwendet, um die Verbindung mit dem privaten Endpunkt zu löschen.

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>Erneutes Generieren von Administratorschlüsseln

Mit [**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) wird ein Rollover von Administrator-[API-Schlüsseln](search-security-api-keys.md) ausgeführt. Mit jedem Dienst werden zwei Administratorschlüssel für den authentifizierten Zugriff erstellt. Schlüssel sind für jede Anforderung erforderlich. Beide Administratorschlüssel sind funktionell gleichwertig und gewähren vollständigen Schreibzugriff auf einen Suchdienst mit der Möglichkeit, alle Informationen abrufen oder ein beliebiges Objekt zu erstellen und zu löschen. Es sind zwei Schlüssel vorhanden, sodass Sie den einen verwenden können, während der andere ersetzt wird. 

Sie können immer nur jeweils einen Schlüssel erneut generieren, der entweder als `primary`- oder `secondary`-Schlüssel angegeben ist. Um einen ununterbrochenen Dienst zu gewährleisten, denken Sie daran, den gesamten Clientcode so zu aktualisieren, dass während des Rollovers des primären Schlüssels ein sekundärer Schlüssel verwendet wird. Vermeiden Sie das Ändern der Schlüssel, während Vorgänge ausgeführt werden.

Wenn Sie Schlüssel ohne Aktualisierung des Clientcodes erneut generieren, treten bei Anforderungen, bei denen der alte Schlüssel verwendet wird, Fehler auf. Durch das erneute Generieren aller neuen Schlüssel werden Sie nicht dauerhaft vom Dienst ausgesperrt, und Sie können weiterhin über das Portal auf den Dienst zugreifen. Nachdem Sie primäre und sekundäre Schlüssel erneut generiert haben, können Sie den Clientcode zur Verwendung der neuen Schlüssel aktualisieren, und die Vorgänge werden entsprechend fortgesetzt.

Werte für die API-Schlüssel werden vom Dienst generiert. Sie können keinen benutzerdefinierten Schlüssel für die Verwendung in Azure Cognitive Search bereitstellen. Ebenso ist kein benutzerdefinierter Name für Administrator-API-Schlüssel vorhanden. Verweise auf den Schlüssel sind feste Zeichenfolgen, entweder `primary` oder `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln. Es werden beide Schlüssel zurückgegeben, obwohl Sie immer nur jeweils einen ändern.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Erstellen oder Löschen von Abfrageschlüsseln

Mit [**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) werden Abfrage-[API-Schlüssel](search-security-api-keys.md) für schreibgeschützten Zugriff von Client-Apps auf einen Azure Cognitive Search-Index erstellt. Abfrageschlüssel werden zum Authentifizieren bei einem bestimmten Index für das Abrufen von Suchergebnissen verwendet. Abfrageschlüssel gewähren keinen schreibgeschützten Zugriff auf andere Elemente im Dienst, z.B. Index, Datenquelle oder Indexer.

Sie können keinen Schlüssel für die Verwendung in Azure Cognitive Search bereitstellen. API-Schlüssel werden vom Dienst generiert.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skalieren von Replikaten und Partitionen

Mit [**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) wird die [Anzahl von Replikaten und Partitionen erhöht oder verringert](search-capacity-planning.md), um abrechenbare Ressourcen innerhalb des Diensts anzupassen. Durch eine erhöhte Anzahl von Replikaten oder Partitionen erhöht sich auch Ihre Rechnung, die sowohl feste als auch variable Gebühren umfasst. Wenn Sie vorübergehend zusätzliche Verarbeitungsleistung benötigen, können Sie die Anzahl der Replikate und Partitionen zum Bewältigen der Workload erhöhen. Der Überwachungsbereich auf der Übersichtsseite des Portals weist Kacheln für die Abfragewartezeit, Abfragen pro Sekunde und Drosselung auf, die angeben, ob die aktuelle Kapazität ausreichend ist.

Das Hinzufügen oder Entfernen von Ressourcen kann eine Weile dauern. Anpassungen der Kapazität finden im Hintergrund statt, damit bestehende Workloads fortgesetzt werden können. Zusätzliche Kapazität wird für eingehende Anforderungen verwendet, sobald sie bereitsteht, und erfordert keine zusätzliche Konfiguration. 

Das Entfernen von Kapazität kann zu einer Unterbrechung führen. Es wird empfohlen, vor dem Verringern der Kapazität alle Indizierungs- und Indexeraufträge anzuhalten, um verworfene Anforderungen zu vermeiden. Wenn das nicht möglich ist, sollten Sie die Kapazität schrittweise verringern, jeweils ein Replikat und eine Partition nach der anderen, bis das neue Zielniveau erreicht ist.

Nachdem Sie den Befehl gesendet haben, gibt es keine Möglichkeit, ihn während der Ausführung zu beenden. Sie müssen warten, bis der Befehl abgeschlossen ist, bevor Sie die jeweilige Anzahl überarbeiten können.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <search-service-resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Die Ergebnisse sollten in etwa der folgenden Ausgabe ähneln.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>Erstellen einer gemeinsam genutzten Private Link-Ressource

Private Endpunkte von gesicherten Ressourcen, die über Azure Cognitive Search-APIs erstellt wurden, werden als *freigegebene Private Link-Ressourcen* bezeichnet. Der Grund: Der Zugriff auf eine Ressource (z. B. ein Speicherkonto), die in den [Azure Private Link-Dienst](https://azure.microsoft.com/services/private-link/) integriert wurde, wird von Ihnen „freigegeben“.

Wenn Sie einen Indexer zum Indizieren von Daten in Azure Cognitive Search verwenden und sich Ihre Datenquelle in einem privaten Netzwerk befindet, können Sie eine ausgehende [private Endpunktverbindung](../private-link/private-endpoint-overview.md) erstellen, um die Daten zu erreichen.

Eine vollständige Liste mit den Azure-Ressourcen, für die Sie ausgehende private Endpunkte für Azure Cognitive Search erstellen können, und die zugehörigen Werte für die **Gruppen-ID** finden Sie [hier](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis).

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) wird verwendet, um die gemeinsam genutzte Private Link-Ressource zu erstellen. Beachten Sie hierbei, dass für die Datenquelle ggf. einige Konfigurationsschritte ausgeführt werden müssen, bevor dieser Befehl ausgeführt wird.

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-serviceresource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<storage-resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

Mit [Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) können Sie die gemeinsam genutzten Private Link-Ressourcen abrufen und ihren Status anzeigen.

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

Sie müssen die Verbindung mit dem folgenden Befehl genehmigen, bevor sie verwendet werden kann.

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <search-service-resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) wird verwendet, um die gemeinsam genutzte Private Link-Ressource zu löschen.

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <search-service-resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

Ausführliche Informationen zum Einrichten von gemeinsam genutzten Private Link-Ressourcen finden Sie unter [Erstellen von Indexerverbindungen über einen privaten Endpunkt](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie einen [Index](search-what-is-an-index.md), verwenden Sie das Portal, REST-APIs oder das .NET SDK zum [Abfragen eines Index](search-query-overview.md).

* [Erstellen eines Indexes für Azure Cognitive Search im Azure-Portal](search-get-started-portal.md)
* [Einrichten eines Indexers zum Laden von Daten anderer Dienste](search-indexer-overview.md)
* [Abfragen eines Azure Cognitive Search-Indexes per Suchexplorer im Azure-Portal](search-explorer.md)
* [Verwendung von Azure Cognitive Search in .NET](search-howto-dotnet-sdk.md)