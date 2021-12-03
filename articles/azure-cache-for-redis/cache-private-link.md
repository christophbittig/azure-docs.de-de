---
title: Azure Cache for Redis mit Azure Private Link
description: Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit dem von Azure Private Link betriebenen Azure Cache for Redis verbindet. In diesem Artikel erfahren Sie, wie Sie das Azure-Portal verwenden, um einen Azure-Cache, ein virtuelles Azure-Netzwerk und einen privaten Endpunkt zu erstellen.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 3/31/2021
ms.openlocfilehash: 4a98b229497aa3b11692fff044bb0f0347ada9d7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039523"
---
# <a name="azure-cache-for-redis-with-azure-private-link"></a>Azure Cache for Redis mit Azure Private Link

In diesem Artikel erfahren Sie, wie Sie im Azure-Portal ein virtuelles Netzwerk und eine Azure Cache for Redis-Instanz mit einem privaten Endpunkt erstellen. Außerdem erfahren Sie, wie Sie einer vorhandenen Azure Cache for Redis-Instanz einen privaten Endpunkt hinzufügen.

Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit dem von Azure Private Link betriebenen Azure Cache for Redis verbindet.

Sie können den öffentlichen Zugriff auf den privaten Endpunkt Ihres Caches einschränken, indem Sie das Kennzeichen `PublicNetworkAccess` deaktivieren.

>[!Important]
> Es gibt ein `publicNetworkAccess`-Flag, das standardmäßig `Disabled` ist.
> Sie können den Wert auch auf `Disabled` oder `Enabled` festlegen. Wenn dieses Flag auf aktiviert gesetzt ist, erlaubt es sowohl den öffentlichen als auch den privaten Endpunktzugriff auf den Cache. Wenn es auf `Disabled` gesetzt ist, erlaubt es nur den Zugriff privater Endpunkte. Weitere Informationen zum Ändern des Wertes finden Sie in der [FAQ](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access).
>
>

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Derzeit werden Portalkonsole und Persistenz für Firewall-Speicherkonten nicht unterstützt.
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Erstellen eines privaten Endpunkts mit einer neuen Azure Cache for Redis-Instanz

In diesem Abschnitt erstellen Sie eine neue Azure Cache for Redis-Instanz mit einem privaten Endpunkt.

### <a name="create-a-virtual-network-for-your-new-cache"></a>Erstellen Sie ein virtuelles Netzwerk für Ihren neuen Cache

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann **Ressource erstellen** aus.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Auswählen von „Ressource erstellen“.":::

2. Wählen Sie auf der Seite **Neu** zuerst **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.

3. Wählen Sie **Hinzufügen** aus, um ein virtuelles Netzwerk zu erstellen.

4. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem dieses virtuelle Netzwerk erstellt werden soll. |
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Name der Ressourcengruppe, in der Ihr virtuelles Netzwerk und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. |
   | **Name** | Geben Sie einen Namen für das virtuelle Netzwerk ein. | Der Name muss mit einem Buchstaben oder einer Ziffer beginnen, auf einen Buchstaben, eine Ziffer oder einen Unterstrich enden und darf nur Buchstaben, Ziffern, Unterstriche, Punkte und Bindestriche enthalten. |
   | **Region** | Öffnen Sie die Dropdownliste, und wählen Sie eine Region aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihr virtuelles Netzwerk verwenden. |

5. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

6. Geben Sie auf der Registerkarte **IP-Adressen** den **IPv4-Adressraum** als eine oder mehrere Adresspräfixe in CIDR-Schreibweise an (z. B. 192.168.1.0/24).

7. Wählen Sie unter **Subnetzname** die Option **Standard** aus, um die Eigenschaften des Subnetzes zu bearbeiten.

8. Geben Sie im Bereich **Subnetz bearbeiten** einen **Subnetznamen** sowie den **Subnetzadressbereich** an. Der Adressbereich des Subnetzes in CIDR-Schreibweise (z. B. 192.168.1.0/24). Er muss innerhalb des Adressraums des virtuellen Netzwerks liegen.

9. Wählen Sie **Speichern** aus.

10. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

11. Vergewissern Sie sich, dass alle Informationen richtig sind, und wählen Sie **Erstellen** aus, um das virtuelle Netzwerk bereitzustellen.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Erstellen eine einer Azure Cache for Redis-Instanz mit einem privatem Endpunkt

Führen Sie zum Erstellen einer Cache-Instanz die folgenden Schritte aus:

1. Wechseln Sie zurück zur Homepage des Azure-Portals, oder öffnen Sie das Randleistenmenü, und wählen Sie dann **Ressource erstellen** aus.

1. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Auswählen von „Azure Cache for Redis“.":::

1. Konfigurieren Sie auf der Seite **Neuer Redis Cache** die Einstellungen für den neuen Cache.

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-Name** | Geben Sie einen global eindeutigen Namen ein. | Der Cache-Name muss eine Zeichenfolge mit 1 bis 63 Zeichen sein. Die Zeichenfolge darf nur Zahlen, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net*. |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. |
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. |
   | **Location** | Öffnen Sie die Dropdownliste, und wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
   | **Preisstufe** | Öffnen Sie die Dropdownliste, und wählen Sie einen [Tarif](https://azure.microsoft.com/pricing/details/cache/) aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |

1. Wählen Sie die Registerkarte **Netzwerk** oder unten auf der Seite die Schaltfläche **Netzwerk** aus.

1. Wählen Sie auf der Registerkarte **Netzwerk** die Option **Privater Endpunkt** als Konnektivitätsmethode aus.

1. Wählen Sie die Schaltfläche **+ Hinzufügen** aus, um Ihren privaten Endpunkt zu erstellen.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Fügen Sie unter „Netzwerk“ einen privaten Endpunkt hinzu.":::

1. Konfigurieren Sie auf der Seite **Erstellen eines privaten Endpunkts** die Einstellungen für Ihren privaten Endpunkt mit dem virtuellen Netzwerk und Subnetz, die Sie im letzten Abschnitt erstellt haben, und wählen Sie **OK** aus.

1. Wählen Sie die Registerkarte **Weiter: Erweitert** oder unten auf der Seite die Schaltfläche **Weiter: Erweitert** aus.

1. Aktivieren Sie auf der Registerkarte **Erweitert** für eine Basic- oder Standard-Cache-Instanz die Aktivierungsoption, wenn Sie einen TLS-fremden Port aktivieren möchten.

1. Konfigurieren Sie auf der Registerkarte **Advanced** für eine Premium-Cache-Instanz die Einstellungen für einen TLS-fremden Port, Clustering und Datenpersistenz.

1. Wählen Sie die Registerkarte **Weiter: Tags** oder unten auf der Seite die Schaltfläche **Weiter: Tags** aus.

1. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten.

1. Klicken Sie auf **Überprüfen und erstellen**. Sie werden zur Registerkarte „Überprüfen und erstellen“ weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

1. Wenn die grüne Meldung „Validierung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus.

Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit.

> [!IMPORTANT]
> Es gibt ein `publicNetworkAccess`-Flag, das standardmäßig `Disabled` ist.
> Sie können den Wert auch auf `Disabled` oder `Enabled` festlegen. Wenn dieses Flag auf `Enabled` gesetzt ist, erlaubt es sowohl öffentlichen als auch privaten Endpunkten den Zugriff auf den Cache. Wenn es auf `Disabled` gesetzt ist, erlaubt es nur den Zugriff privater Endpunkte. Weitere Informationen zum Ändern des Wertes finden Sie in der [FAQ](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access).
>
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Erstellen eines privaten Endpunkts mit einer vorhandenen Azure Cache for Redis-Instanz

In diesem Abschnitt fügen Sie einer vorhandenen Azure Cache for Redis-Instanz einen privaten Endpunkt hinzu.

### <a name="create-a-virtual-network-for-you-existing-cache"></a>Erstellen Sie ein virtuelles Netzwerk für Ihren bestehenden Cache

Führen Sie die folgenden Schritte aus, um ein virtuelles Netzwerk zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann **Ressource erstellen** aus.

1. Wählen Sie auf der Seite **Neu** zuerst **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.

1. Wählen Sie **Hinzufügen** aus, um ein virtuelles Netzwerk zu erstellen.

1. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem dieses virtuelle Netzwerk erstellt werden soll. |
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Name der Ressourcengruppe, in der Ihr virtuelles Netzwerk und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. |
   | **Name** | Geben Sie einen Namen für das virtuelle Netzwerk ein. | Der Name muss mit einem Buchstaben oder einer Ziffer beginnen, auf einen Buchstaben, eine Ziffer oder einen Unterstrich enden und darf nur Buchstaben, Ziffern, Unterstriche, Punkte und Bindestriche enthalten. |
   | **Region** | Öffnen Sie die Dropdownliste, und wählen Sie eine Region aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihr virtuelles Netzwerk verwenden. |

1. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

1. Geben Sie auf der Registerkarte **IP-Adressen** den **IPv4-Adressraum** als eine oder mehrere Adresspräfixe in CIDR-Schreibweise an (z. B. 192.168.1.0/24).

1. Wählen Sie unter **Subnetzname** die Option **Standard** aus, um die Eigenschaften des Subnetzes zu bearbeiten.

1. Geben Sie im Bereich **Subnetz bearbeiten** einen **Subnetznamen** sowie den **Subnetzadressbereich** an. Der Adressbereich des Subnetzes in CIDR-Schreibweise (z. B. 192.168.1.0/24). Er muss innerhalb des Adressraums des virtuellen Netzwerks liegen.

1. Wählen Sie **Speichern** aus.

1. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

1. Vergewissern Sie sich, dass alle Informationen richtig sind, und wählen Sie **Erstellen** aus, um das virtuelle Netzwerk bereitzustellen.

### <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

Führen Sie die folgenden Schritte aus, um einen privaten Endpunkt zu erstellen:

1. Suchen Sie im Azure-Portal nach **Azure Cache for Redis**. Drücken Sie dann die EINGABETASTE, oder wählen Sie den Eintrag in den Suchvorschlägen aus.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Suche nach „Azure Cache for Redis“":::

1. Wählen Sie die Cache-Instanz aus, der Sie einen privaten Endpunkt hinzufügen möchten.

1. Wählen Sie im linken Bildschirmbereich **Private Endpunkte** aus.

1. Wählen Sie die Schaltfläche **Privater Endpunkt** aus, um Ihren privaten Endpunkt zu erstellen.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Privaten Endpunkt hinzufügen":::

1. Konfigurieren Sie auf der Seite **Privaten Endpunkt erstellen** die Einstellungen für Ihren privaten Endpunkt.

   | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem dieser private Endpunkt erstellt werden soll. |
   | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr privater Endpunkt und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. |
   | **Name** | Geben Sie einen Namen für den privaten Endpunkt ein. | Der Name muss mit einem Buchstaben oder einer Ziffer beginnen, auf einen Buchstaben, eine Ziffer oder einen Unterstrich enden und darf nur Buchstaben, Ziffern, Unterstriche, Punkte und Bindestriche enthalten. |
   | **Region** | Öffnen Sie die Dropdownliste, und wählen Sie eine Region aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren privaten Endpunkt verwenden. |

1. Wählen Sie unten auf der Seite die Schaltfläche **Next: Ressource** aus.

1. Wählen Sie auf der Registerkarte **Ressource** Ihr Abonnement aus, wählen Sie `Microsoft.Cache/Redis` als Ressourcentyp aus, und wählen Sie dann den Cache aus, mit dem Sie den privaten Endpunkt verbinden möchten.

1. Wählen Sie unten auf der Seite die Schaltfläche **Next: Konfiguration** aus.

1. Wählen Sie auf der Registerkarte **Konfiguration** das im vorherigen Abschnitt erstellte virtuelle Netzwerk und Subnetz aus.

1. Wählen Sie unten auf der Seite die Schaltfläche **Next: Tags** (Weiter: Tags) aus.

1. Geben Sie optional auf der Registerkarte **Tags** den Namen und den Wert ein, wenn Sie die Ressource kategorisieren möchten.

1. Klicken Sie auf **Überprüfen und erstellen**. Sie werden zur Registerkarte **Überprüfen und erstellen** weitergeleitet, auf der Azure Ihre Konfiguration überprüft.

1. Wenn die grüne Meldung **Validierung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

> [!IMPORTANT]
>
> Es gibt ein `publicNetworkAccess`-Flag, das standardmäßig `Disabled` ist.
> Sie können den Wert auch auf `Disabled` oder `Enabled` festlegen. Wenn dieses Flag auf aktiviert gesetzt ist, erlaubt es sowohl den öffentlichen als auch den privaten Endpunktzugriff auf den Cache. Wenn es auf `Disabled` gesetzt ist, erlaubt es nur den Zugriff privater Endpunkte. Weitere Informationen zum Ändern des Wertes finden Sie in der [FAQ](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access).
>
## <a name="create-a-private-endpoint-using-azure-powershell"></a>Erstellen eines privaten Endpunkts mit Azure PowerShell

Um einen privaten Endpunkt namens *MeinPrivaterEndpunkt* für eine vorhandene Azure Cache for Redis-Instanz zu erstellen, führen Sie das folgende PowerShell-Skript aus. Ersetzen Sie die Variablenwerte durch die entsprechenden Angaben für Ihre Umgebung:

```azurepowershell-interactive

$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cache for Redis instance and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cache for Redis instance
$redisCacheName = "mycacheInstance"

# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$redisCacheResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Cache/Redis/$($redisCacheName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $redisCacheResourceId -GroupId "redisCache"
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

## <a name="retrieve-a-private-endpoint-using-azure-powershell"></a>Abrufen eines privaten Endpunkts mit Azure PowerShell

Verwenden Sie den folgenden PowerShell-Befehl, um die Details eines privaten Endpunkts zu erhalten:

```azurepowershell-interactive
Get-AzPrivateEndpoint -Name $PrivateEndpointName -ResourceGroupName $ResourceGroupName
```

## <a name="remove-a-private-endpoint-using-azure-powershell"></a>Entfernen eines privaten Endpunkts mit Azure PowerShell

Verwenden Sie den folgenden PowerShell-Befehl, um einen privaten Endpunkt zu entfernen:

```azurepowershell-interactive
Remove-AzPrivateEndpoint -Name $PrivateEndpointName -ResourceGroupName $ResourceGroupName
```

## <a name="create-a-private-endpoint-using-azure-cli"></a>Erstellen eines privaten Endpunkts mit Azure CLI

Um einen privaten Endpunkt namens *MeinPrivaterEndpunkt* für eine vorhandene Azure Cache for Redis-Instanz zu erstellen, führen Sie das folgende Azure CLI-Skript aus. Ersetzen Sie die Variablenwerte durch die entsprechenden Angaben für Ihre Umgebung:

```azurecli-interactive
# Resource group where the Azure Cache for Redis and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cache for Redis and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cache for Redis instance
redisCacheName="mycacheInstance"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
    --name $VNetName \
    --resource-group $ResourceGroupName \
    --subnet-name $SubnetName

az network vnet subnet update \
    --name $SubnetName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName \
    --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Cache/Redis/$redisCacheName" \
    --group-ids "redisCache" \
    --connection-name $PrivateConnectionName
```

## <a name="retrieve-a-private-endpoint-using-azure-cli"></a>Abrufen eines privaten Endpunkts mit Azure CLI

Verwenden Sie den folgenden CLI-Befehl, um die Details eines privaten Endpunkts zu erhalten:

```azurecli-interactive
az network private-endpoint show --name MyPrivateEndpoint --resource-group MyResourceGroup
```

## <a name="remove-a-private-endpoint-using-azure-cli"></a>Entfernen eines privaten Endpunkts mit Azure CLI

Verwenden Sie den folgenden CLI-Befehl, um einen privaten Endpunkt zu entfernen:

```azurecli-interactive
az network private-endpoint delete --name MyPrivateEndpoint --resource-group MyResourceGroup
```

## <a name="faq"></a>Häufig gestellte Fragen

- [Warum kann ich keine Verbindung mit einem privaten Endpunkt herstellen?](#why-cant-i-connect-to-a-private-endpoint)
- [Welche Features werden von privaten Endpunkten nicht unterstützt?](#what-features-arent-supported-with-private-endpoints)
- [ Wie kann ich überprüfen, ob mein privater Endpunkt richtig konfiguriert ist?](#how-do-i-verify-if-my-private-endpoint-is-configured-correctly)
- [Wie kann ich meinen privaten Endpunkt so ändern, dass der öffentliche Netzwerkzugriff auf ihn deaktiviert oder aktiviert ist?](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
- [Wie kann ich meinen Cache mit VNet-Einschleusung zu einem Private Link-Cache migrieren?](#how-can-i-migrate-my-vnet-injected-cache-to-a-private-link-cache)
- [Wie kann ich mehrere Endpunkte in unterschiedlichen virtuellen Netzwerken haben?](#how-can-i-have-multiple-endpoints-in-different-virtual-networks)
- [Was passiert, wenn ich alle privaten Endpunkte in meinem Cache lösche?](#what-happens-if-i-delete-all-the-private-endpoints-on-my-cache)
- [Sind Netzwerksicherheitsgruppen (NSGs) für private Endpunkte aktiviert?](#are-network-security-groups-nsg-enabled-for-private-endpoints)
- [Meine Instanz des privaten Endpunkts befindet sich nicht in meinem VNet. Wie wird sie meinem VNet zugeordnet?](#my-private-endpoint-instance-isnt-in-my-vnet-so-how-is-it-associated-with-my-vnet)

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Warum kann ich keine Verbindung mit einem privaten Endpunkt herstellen?

- Private Endpunkte können nicht mit Ihrer Cache-Instanz verwendet werden, wenn ihr Cache bereits ein in das VNET eingefügter Cache ist.
- Für Cluster-Caches ist ein Limit für eine private Verbindung vorhanden. Für alle anderen Caches beträgt Ihr Limit 100 private Verbindungen.
- Sie versuchen, [Daten dauerhaft im Speicherkonto zu speichern](cache-how-to-premium-persistence.md), in dem Firewallregeln angewendet werden, die Sie möglicherweise daran hindern können, die private Verbindung zu erstellen.
- Möglicherweise stellen Sie keine Verbindung mit Ihrem privaten Endpunkt her, wenn Ihre Cache-Instanz ein [nicht unterstütztes Feature verwendet](#what-features-arent-supported-with-private-endpoints).

### <a name="what-features-arent-supported-with-private-endpoints"></a>Welche Features werden von privaten Endpunkten nicht unterstützt?

Der Versuch, über die Azure-Portal-Konsole eine Verbindung herzustellen, ist ein nicht unterstütztes Szenario, in dem ein Verbindungsfehler angezeigt wird.

### <a name="how-do-i-verify-if-my-private-endpoint-is-configured-correctly"></a>Wie kann ich überprüfen, ob mein privater Endpunkt richtig konfiguriert ist?

Sie können einen Befehl wie `nslookup` aus dem VNet ausführen, das mit dem privaten Endpunkt verknüpft ist, um zu überprüfen, ob der Befehl die private IP-Adresse für den Cache auflöst. Die private IP-Adresse finden Sie, indem Sie Ihren **Privaten Endpunkt** aus Ihren Ressourcen auswählen. Wählen Sie im Ressourcenmenü auf der linken Seite **DNS-Konfiguration**. Im Arbeitsbereich auf der rechten Seite sehen Sie die IP-Adresse für die **Netzwerkschnittstelle**.

:::image type="content" source="media/cache-private-link/cache-private-ip-address.png" alt-text="Im Azure-Portal, private Endpunkt D N S-Einstellungen.":::

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Wie kann ich meinen privaten Endpunkt so ändern, dass der öffentliche Netzwerkzugriff auf ihn deaktiviert oder aktiviert ist?

Es gibt ein `publicNetworkAccess`-Flag, das standardmäßig `Disabled` ist.
Wenn dieses Flag auf `Enabled` gesetzt ist, erlaubt es sowohl öffentlichen als auch privaten Endpunkten den Zugriff auf den Cache. Wenn es auf `Disabled` gesetzt ist, erlaubt es nur den Zugriff privater Endpunkte. Sie können den Wert im Azure-Portal oder mit einer RESTful-API-Patchanforderung auf `Disabled` oder `Enabled` festlegen.

Gehen Sie folgendermaßen vor, um den Wert im Azure-Portal zu ändern.

1. Suchen Sie im Azure-Portal nach **Azure Cache for Redis**.  Drücken Sie dann die EINGABETASTE, oder wählen Sie den Eintrag in den Suchvorschlägen aus.

1. Wählen Sie die Cache-Instanz aus, für die Sie den Wert für den öffentlichen Netzwerkzugang ändern möchten.

1. Wählen Sie im linken Bildschirmbereich **Private Endpunkte** aus.

1. Wählen Sie die Schaltfläche **Öffentlichen Netzwerkzugriff aktivieren** aus.

Informationen zum Ändern des Werts durch eine RESTful-API-Patchanforderung finden Sie unten, und bearbeiten Sie den Wert, um das für den Cache gewünschte Flag widerzuspiegeln.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-link-cache"></a>Wie kann ich meinen Cache mit VNet-Einschleusung zu einem Private Link-Cache migrieren?

In unserem [Migrationsleitfaden](cache-vnet-migration.md) finden Sie verschiedene Ansätze, wie Sie Ihre VNet-injizierten Caches in Private Link-Caches migrieren können.

### <a name="how-can-i-have-multiple-endpoints-in-different-virtual-networks"></a>Wie kann ich mehrere Endpunkte in unterschiedlichen virtuellen Netzwerken haben?

Um mehrere private Endpunkte in unterschiedlichen virtuellen Netzwerken zu verwenden, muss die private DNS-Zone manuell für die verschiedenen virtuellen Netzwerke konfiguriert werden, _bevor_ der private Endpunkt erstellt wird. Weitere Informationen finden Sie unter [DNS-Konfiguration für private Azure-Endpunkte](../private-link/private-endpoint-dns.md).

### <a name="what-happens-if-i-delete-all-the-private-endpoints-on-my-cache"></a>Was passiert, wenn ich alle privaten Endpunkte in meinem Cache lösche?

Nachdem Sie die privaten Endpunkte in Ihrem Cache gelöscht haben, kann Ihre Cache-Instanz unerreichbar werden, bis Sie entweder den öffentlichen Netzwerkzugriff explizit aktivieren oder einen anderen privaten Endpunkt hinzufügen. Sie können die Flag `publicNetworkAccess` entweder auf dem Azure-Portal oder mit einer RESTful-API-Patchanforderung ändern. Weitere Informationen zum Ändern des Werts finden Sie in den [häufig gestellten Fragen](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access).

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Sind Netzwerksicherheitsgruppen (NSGs) für private Endpunkte aktiviert?

Nein, sie sind für private Endpunkte deaktiviert. Während Subnetzen, die den privaten Endpunkt enthalten, eine NSG zugeordnet sein kann, gelten die Regeln nicht für den vom privaten Endpunkt verarbeiteten Datenverkehr. Sie müssen die [Durchsetzung von Netzwerkrichtlinien deaktivieren](../private-link/disable-private-endpoint-network-policy.md), um private Endpunkte in einem Subnetz bereitzustellen zu können. Die NSG wird weiterhin für andere Workloads erzwungen, die im selben Subnetz gehostet werden. Für Routen in einem beliebigen Clientsubnetz wird ein /32-Präfix verwendet. Zum Ändern des Standardverhaltens für das Routing ist eine ähnliche benutzerdefinierte Route erforderlich.

Steuern Sie den Datenverkehr, indem Sie auf Quellclients NSG-Regeln für ausgehenden Datenverkehr verwenden. Stellen Sie einzelne Routen mit /32-Präfix zum Außerkraftsetzen von Routen privater Endpunkte bereit. NSG Flow-Protokolle und Daten zur Überwachung ausgehender Verbindungen werden weiterhin unterstützt und können verwendet werden.

### <a name="my-private-endpoint-instance-isnt-in-my-vnet-so-how-is-it-associated-with-my-vnet"></a>Meine Instanz des privaten Endpunkts befindet sich nicht in meinem VNet. Wie wird sie meinem VNet zugeordnet?

Sie ist mit Ihrem VNet nur verknüpft. Da sie sich nicht in Ihrem VNet befindet, müssen NSG-Regeln für abhängige Endpunkte nicht geändert werden.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Private Link finden Sie in der [Dokumentation zu Azure Private Link](../private-link/private-link-overview.md).
- Informationen zum Vergleichen verschiedener Optionen für die Netzwerkisolation Ihrer Cache-Instanz finden Sie unter [Dokumentation der Optionen für die Azure Cache for Redis-Netzwerkisolation](cache-network-isolation.md).
