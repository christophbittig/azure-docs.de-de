---
title: 'Tutorial: Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Leitung: Azure CLI'
description: In diesem Tutorial erfahren Sie, wie Sie virtuelle Netzwerke (VNETs) über das Resource Manager-Bereitstellungsmodell und die Azure CLI mit Azure ExpressRoute-Leitungen verknüpfen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: duau
ms.openlocfilehash: b7a6cf1bdb490683faa5285811c83d220ac80cca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662880"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>Tutorial: Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung mithilfe der CLI

In diesem Tutorial erfahren Sie, wie Sie virtuelle Netzwerke (VNETs) über die Azure CLI mit Azure ExpressRoute-Leitungen verknüpfen. Um mithilfe der Azure CLI eine Verknüpfung durchzuführen, müssen die virtuellen Netzwerke mit dem Resource Manager-Bereitstellungsmodell erstellt werden. Sie können Teil desselben Abonnements sein oder zu einem anderen Abonnement gehören. Wenn Sie eine andere Methode für die Verbindung Ihres VNET mit einer ExpressRoute-Verbindung verwenden möchten, können Sie einen Artikel aus der folgenden Liste auswählen:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-linkvnet-cli.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-linkvnet-classic.md)
> 

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in demselben Abonnement und einer Verbindung
> - Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in einem anderen Abonnement und einer Verbindung
> - Ändern einer Verbindung mit einem virtuellen Netzwerk
> - Konfigurieren von ExpressRoute FastPath

## <a name="prerequisites"></a>Voraussetzungen

* Sie benötigen die neueste Version der Befehlszeilenschnittstelle (CLI). Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).
* Lesen Sie, bevor Sie mit der Konfiguration beginnen, die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md).
* Sie benötigen eine aktive ExpressRoute-Verbindung. 
  * Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](howto-circuit-cli.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren. 
  * Stellen Sie sicher, dass privates Azure-Peering für die Verbindung konfiguriert ist. Informationen zum Routing finden Sie unter [Konfigurieren des Routings](howto-routing-cli.md) . 
  * Stellen Sie sicher, dass das private Azure-Peering konfiguriert ist. Das BGP-Peering zwischen Ihrem Netzwerk und Microsoft muss eingerichtet sein, damit End-to-End-Konnektivität bereitgestellt werden kann.
  * Vergewissern Sie sich, dass ein virtuelles Netzwerk und ein virtuelles Netzwerkgateway erstellt und vollständig bereitgestellt wurden. Befolgen Sie die Anweisungen zum [Konfigurieren eines virtuellen Netzwerkgateways für ExpressRoute](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md). Achten Sie darauf, `--gateway-type ExpressRoute` zu verwenden.
* Sie können bis zu zehn virtuelle Netzwerke mit einer ExpressRoute-Standardverbindung verknüpfen. Alle virtuellen Netzwerke müssen sich in der gleichen geopolitischen Region befinden, wenn Sie eine ExpressRoute-Standardverbindung verwenden. 
* Ein einzelnes VNet kann mit bis zu 16 ExpressRoute-Leitungen verknüpft werden. Erstellen Sie anhand der folgenden Vorgehensweise ein neues Verbindungsobjekt für jede ExpressRoute-Leitung, mit der Sie eine Verbindung herstellen. Die ExpressRoute-Leitungen können sich im gleichen Abonnement, in verschiedenen Abonnements oder in einer Kombination aus beidem befinden.
* Wenn Sie das ExpressRoute-Premium-Add-On aktivieren, können Sie virtuelle Netzwerke außerhalb der geopolitischen Region der ExpressRoute-Leitung verknüpfen. Über das Premium-Add-On können Sie – je nach ausgewählter Bandbreite – auch mehr als 10 virtuelle Netzwerke mit Ihrer ExpressRoute-Leitung verbinden. Weitere Informationen zum Premium-Add-On finden Sie in den [häufig gestellten Fragen](expressroute-faqs.md) .

* Um die Verbindung von der ExpressRoute-Verbindung zum Zielgateway des virtuellen ExpressRoute-Netzwerks herzustellen, muss die Anzahl der von den lokalen oder virtuellen Netzwerken mit Peering angekündigten Adressräume gleich oder kleiner als **200** sein. Nachdem die Verbindung erfolgreich erstellt wurde, können Sie den lokalen oder virtuellen Netzwerken mit Peering zusätzliche Adressräume hinzufügen (bis zu 1.000).

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in demselben Abonnement und einer Verbindung

Sie können anhand des folgenden Beispiels ein virtuelles Netzwerkgateway mit einer ExpressRoute-Verbindung verbinden. Stellen Sie sicher, dass das Gateway für das virtuelle Netzwerk erstellt wurde und für das Erstellen von Verknüpfungen bereit ist, bevor Sie den Befehl ausführen.

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Herstellen einer Verbindung zwischen einem virtuellen Netzwerk in einem anderen Abonnement und einer Verbindung

Sie können eine ExpressRoute-Verbindung für mehrere Abonnements freigeben. Die folgende Abbildung zeigt eine einfache schematische Darstellung der Freigabe von Lasten für ExpressRoute-Verbindungen für mehrere Abonnements.

> [!NOTE]
> Das Verbinden virtueller Netzwerke zwischen Azure Sovereign Clouds und der öffentlichen Azure-Cloud wird nicht unterstützt. Sie können nur virtuelle Netzwerke aus verschiedenen Abonnements in derselben Cloud verknüpfen.

Jede der kleineren Clouds innerhalb der großen Cloud stellt Abonnements dar, die zu verschiedenen Abteilungen innerhalb einer Organisation gehören. Jede Abteilung in der Organisation verwendet ihr eigenes Abonnement zum Bereitstellen von Diensten. Für die Verbindung mit dem lokalen Netzwerk kann jedoch eine einzelne ExpressRoute-Leitung gemeinsam genutzt werden. Eine einzelne Abteilung (in diesem Beispiel: IT) kann die ExpressRoute-Verbindung besitzen. Andere Abonnements innerhalb der Organisation können die ExpressRoute-Leitung ebenfalls nutzen.

> [!NOTE]
> Konnektivitäts- und Bandbreitengebühren für die dedizierte Verbindung werden dem Besitzer der ExpressRoute-Verbindung in Rechnung gestellt. Alle virtuellen Netzwerke verwenden gemeinsam dieselbe Bandbreite.
> 
> 

![Abonnementübergreifende Konnektivität](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>Verwaltung – Verbindungsbesitzer und Verbindungsbenutzer

Der „Verbindungsbesitzer“ ist ein autorisierter Poweruser der ExpressRoute-Verbindungsressource. Der Verbindungsbesitzer kann Autorisierungen erstellen, die durch „Verbindungsbenutzer“ eingelöst werden können. Leitungsbenutzer sind Besitzer virtueller Netzwerkgateways, die sich nicht im selben Abonnement wie die ExpressRoute-Leitung befinden. Verbindungsbenutzer können Autorisierungen einlösen (eine Autorisierung für jedes virtuelle Netzwerk).

Der Besitzer der Verbindung hat die Möglichkeit, Autorisierungen jederzeit zu ändern und zu widerrufen. Wenn eine Autorisierung widerrufen wird, führt dies dazu, dass alle Verbindungen aus dem Abonnement gelöscht werden, dessen Zugriff aufgehoben wurde.

### <a name="circuit-owner-operations"></a>Aktionen als Verbindungsbesitzer

**So erstellen Sie eine Autorisierung**

Der Leitungsbesitzer erstellt eine Autorisierung, die wiederum einen Autorisierungsschlüssel erstellt. Mit diesem kann ein Leitungsbenutzer seine virtuellen Netzwerkgateways mit der ExpressRoute-Leitung verbinden. Eine Autorisierung gilt nur für jeweils eine Verbindung.

Im folgenden Beispiel wird veranschaulicht, wie Sie eine Autorisierung erstellen:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

Die Antwort enthält Schlüssel und Status für die Autorisierung:

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**So überprüfen Sie Autorisierungen**

Mit dem folgenden Beispiel kann der Besitzer einer Verbindung alle für eine bestimmte Verbindung ausgestellten Autorisierungen überprüfen:

```azurecli-interactive
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**So fügen Sie Autorisierung hinzu**

Mit dem folgenden Beispiel kann der Verbindungsbesitzer Autorisierungen hinzufügen:

```azurecli-interactive
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**So löschen Sie Autorisierungen**

Mit dem folgenden Beispiel kann der Besitzer einer Verbindung Autorisierungen für einen Benutzer widerrufen oder löschen:

```azurecli-interactive
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>Aktionen als Verbindungsbenutzer

Der Verbindungsbenutzer benötigt die Peer-ID und einen Autorisierungsschlüssel vom Verbindungsbesitzer. Der Autorisierungsschlüssel ist eine GUID.

```azurecli-interactive
az network express-route show -n MyCircuit -g ExpressRouteResourceGroup
```

**So lösen Sie eine Verbindungsautorisierung ein**

Mit dem folgenden Beispiel können Benutzer einer Verbindung eine Verknüpfungsautorisierung abrufen:

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**So geben Sie eine Verbindungsautorisierung frei**

Sie können eine Autorisierung durch das Löschen der Verbindung freigeben, die die ExpressRoute-Verbindung mit dem virtuellen Netzwerk verknüpft.

## <a name="modify-a-virtual-network-connection"></a>Ändern einer Verbindung mit einem virtuellen Netzwerk
Sie können bestimmte Eigenschaften einer Verbindung mit einem virtuellen Netzwerk aktualisieren. 

**So aktualisieren Sie die Verbindungsgewichtung**

Das virtuelle Netzwerk kann mit mehreren ExpressRoute-Verbindungen verbunden werden. Möglicherweise erhalten Sie von mehreren ExpressRoute-Verbindungen dasselbe Präfix. Um auszuwählen, an welche Verbindung Datenverkehr für dieses Präfix gesendet werden soll, ändern Sie die Eigenschaft *RoutingWeight* einer Verbindung. Datenverkehr wird an die Verbindung mit dem höchsten *RoutingWeight* gesendet.

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

Der Bereich von *RoutingWeight* liegt zwischen 0 und 32.000. Der Standardwert ist 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurieren von ExpressRoute FastPath 
Sie können [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) aktivieren, wenn Ihr virtuelles Netzwerkgateway den Typ „Höchstleistung“ oder „ErGw3AZ“ hat. FastPath verbessert die Leistung von Datenpfaden (Pakete pro Sekunde und Verbindungen pro Sekunde) zwischen Ihrem lokalen und Ihrem virtuellen Netzwerk. 

**Konfigurieren von FastPath für eine neue Verbindung**

```azurecli-interactive
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**Aktualisieren einer vorhandenen Verbindung zum Aktivieren von FastPath**

```azurecli-interactive
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

> [!NOTE]
> Sie können den [Verbindungsmonitor](how-to-configure-connection-monitor.md) verwenden, um mithilfe von FastPath zu überprüfen, ob Ihr Datenverkehr das Ziel erreicht.
>

## <a name="enroll-in-expressroute-fastpath-features-preview"></a>Registrieren für FastPath-Features in ExpressRoute (Vorschau)

FastPath-Unterstützung für das Peering virtueller Netzwerke befindet sich jetzt in der öffentlichen Vorschauphase. Die Registrierung ist nur über Azure PowerShell verfügbar. Anweisungen zur Registrierung finden Sie unter [FastPath-Previewfunktionen](expressroute-howto-linkvnet-arm.md#enroll-in-expressroute-fastpath-features-preview).

> [!NOTE] 
> Alle Verbindungen, die für FastPath im Zielabonnement konfiguriert sind, werden in dieser Vorschauversion registriert. Wir raten davon ab, diese Vorschauversion in Produktionsabonnements zu aktivieren.
> Wenn Sie FastPath bereits konfiguriert haben und sich für die Previewfunktionen registrieren möchten, müssen Sie folgende Schritte ausführen:
> 1. Registrieren Sie sich mit dem obigen Azure PowerShell-Befehl für die FastPath-Previewfunktion.
> 1. Deaktivieren Sie FastPath für die Zielverbindung, und aktivieren Sie es dann erneut.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die ExpressRoute-Verbindung nicht mehr benötigen, entfernen Sie die Verknüpfung zwischen dem Gateway und der Leitung, indem Sie in dem Abonnement, in dem sich das Gateway befindet, den Befehl `az network vpn-connection delete` verwenden.

```azurecli-interactive
az network vpn-connection delete --name ERConnection --resource-group ExpressRouteResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie ein virtuelles Netzwerk mit einer Leitung im selben und in einem anderen Abonnement verbinden. Weitere Informationen zum ExpressRoute-Gateway finden Sie hier: 

> [!div class="nextstepaction"]
> [Informationen zu ExpressRoute-Gateways für virtuelle Netzwerke](expressroute-about-virtual-network-gateways.md)
