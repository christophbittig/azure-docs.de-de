---
title: Erstellen und Verwalten von öffentlichem Azure ExpressRoute-Peering
description: Weitere Informationen zu öffentlichem Azure-Peering und dessen Verwaltung
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5c2da05dd68951cada82fc888d54a0f423ce18c7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242277"
---
# <a name="create-and-manage-expressroute-public-peering"></a>Erstellen und Verwalten von öffentlichem ExpressRoute-Peering

> [!div class="op_single_selector"]
> * [Artikel – öffentliches Peering](about-public-peering.md)
> * [Video – Öffentliches Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Artikel – Microsoft-Peering](expressroute-circuit-peerings.md#microsoftpeering)
>

In diesem Artikel erfahren Sie, wie Sie die Routingkonfiguration für das öffentliche Peering einer ExpressRoute-Verbindung erstellen und verwalten. Sie können außerdem den Status von Peerings überprüfen, oder diese aktualisieren, löschen bzw. ihre Bereitstellung aufheben. Dieser Artikel bezieht sich auf Resource Manager-Verbindungen, die erstellt wurden, bevor öffentliches Peering als veraltet eingestuft wurde. Wenn Sie bereits über eine Verbindung verfügen (vor der Einstufung des öffentlichen Peerings als veraltet erstellt), können Sie das öffentliche Peering mithilfe von [Azure PowerShell](#powershell), der [Azure-Befehlszeilenschnittstelle](#cli) und des [Azure-Portals](#portal) verwalten/konfigurieren.

>[!NOTE]
>Öffentliches Peering gilt als veraltet. Sie können kein öffentliches Peering für neue ExpressRoute-Verbindungen erstellen. Wenn Sie über eine neue ExpressRoute-Verbindung verfügen, verwenden Sie stattdessen [Microsoft-Peering](expressroute-circuit-peerings.md#microsoftpeering) für Ihre Azure-Dienste.
>

## <a name="connectivity"></a>Verbindung

Die Verbindung wird immer von Ihrem WAN aus mit den Microsoft Azure-Diensten initiiert. Microsoft Azure-Dienste können über diese Routingdomäne keine Verbindungen in Ihr Netzwerk initiieren. Wenn Ihre ExpressRoute-Verbindung für öffentliches Azure-Peering aktiviert ist, können Sie über die Verbindung auf die [in Azure verwendeten öffentlichen IP-Adressbereiche](../virtual-network/ip-services/public-ip-addresses.md#public-ip-addresses) zugreifen.

Sobald öffentliches Peering aktiviert ist, können Sie eine Verbindung mit den meisten Azure-Diensten herstellen. Es ist Ihnen nicht gestattet, selektiv Dienste auszuwählen, zu denen wir Routen ankündigen.

* Dienste wie Azure Storage, SQL-Datenbank-Instanzen und Websites werden über öffentliche IP-Adressen angeboten.
* Über die öffentliche Peeringroutingdomäne können Sie eine private Verbindung mit über öffentliche IP-Adressen gehosteten Diensten herstellen, einschließlich VIPs Ihrer Clouddienste.
* Sie können die öffentliche Peeringdomäne mit der DMZ verbinden und aus Ihrem WAN heraus eine Verbindung mit allen Azure-Diensten unter ihren öffentlichen IP-Adressen herstellen, ohne eine Verbindung über das Internet herstellen zu müssen.

## <a name="services"></a><a name="services"></a>Dienste

In diesem Abschnitt werden die Dienste angezeigt, die über öffentliches Peering verfügbar sind. Da öffentliches Peering veraltet ist, ist nicht geplant, dem öffentlichen Peering neue oder zusätzliche Dienste hinzuzufügen. Wenn Sie öffentliches Peering verwenden und der Dienst, den Sie verwenden möchten, nur über Microsoft-Peering unterstützt wird, müssen Sie auf das Microsoft-Peering umstellen. Eine Liste der unterstützten Dienste finden Sie unter [Microsoft-Peering](expressroute-faqs.md#microsoft-peering).

**Unterstützt:**

* Power BI
* Die meisten Azure-Dienste werden unterstützt. Überprüfen Sie dies direkt für den Dienst, den Sie verwenden möchten.

**Nicht unterstützt:**
  * CDN
  * Azure Front Door
  * Multi-Factor Authentication-Server (Vorversion)
  * Traffic Manager

Um die Verfügbarkeit für einen bestimmten Dienst zu überprüfen, können Sie die Dokumentation für diesen Dienst lesen, um festzustellen, ob ein reservierter Bereich für ihn veröffentlicht wurde. Anschließend können Sie die IP-Adressbereiche des Zieldiensts überprüfen und mit den Bereichen vergleichen, die in der XML-Datei [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Azure-IP-Bereiche und -Diensttags – öffentliche Cloud) aufgeführt sind. Alternativ können Sie ein Supportticket für den betreffenden Dienst erstellen.

## <a name="peering-comparison"></a><a name="compare"></a>Peeringvergleich

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Im öffentlichen Azure-Peering ist jeder BGP-Sitzung eine NAT-IP-Adresse zugeordnet. Wechseln Sie bei mehr als 2 NAT-IP-Adressen zu Microsoft-Peering. Microsoft-Peering ermöglicht Ihnen, Ihre eigenen NAT-Zuordnungen zu konfigurieren und Routenfilter für selektive Präfixankündigungen zu verwenden. Weitere Informationen finden Sie unter [Wechseln zu Microsoft-Peering](./how-to-move-peering.md).
>

## <a name="custom-route-filters"></a>Benutzerdefinierte Routenfilter

Innerhalb des Netzwerks können Sie benutzerdefinierte Routingfilter definieren, um nur die benötigten Routen zu nutzen. Ausführliche Informationen zur Routingkonfiguration finden Sie unter [Routing](expressroute-routing.md) .

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Schritte mit Azure PowerShell


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Da öffentliches Peering veraltet ist, können Sie öffentliches Peering nicht für eine neue ExpressRoute-Verbindung konfigurieren.

1. Vergewissern Sie sich, dass die ExpressRoute-Verbindung bereitgestellt und auch aktiviert wurde. Nehmen Sie das folgende Beispiel:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Die Antwort ähnelt dem folgenden Beispiel:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                      "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Konfigurieren Sie das öffentliche Azure-Peering für die Verbindung. Stellen Sie sicher, dass die folgenden Informationen vorliegen, bevor Sie fortfahren:

   * Ein /30-Subnetz für die primäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden.
   * Optional:
   * Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.

   Führen Sie das folgende Beispiel aus, um das öffentliche Azure-Peering für Ihre Verbindung zu konfigurieren:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Wenn Sie sich für den Einsatz eines MD5-Hash entscheiden, verwenden Sie das folgende Beispiel:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>So rufen Sie Details zum öffentlichen Azure-Peering ab

Sie können die Konfigurationsdetails mit dem folgenden Cmdlet abrufen:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Aktualisieren der Konfiguration für öffentliches Azure-Peering

Sie können einen beliebigen Teil der Konfiguration anhand des folgenden Beispiels aktualisieren. In diesem Beispiel wird die VLAN-ID der Verbindung von 200 in 600 geändert.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>So löschen Sie ein öffentliches Azure-Peering

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Beispiel ausführen:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Schritte mit der Azure-Befehlszeilenschnittstelle


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Überprüfen Sie, ob die ExpressRoute-Verbindung bereitgestellt und auch aktiviert wurde. Nehmen Sie das folgende Beispiel:

   ```azurecli-interactive
   az network express-route list
   ```

   Die Antwort ähnelt dem folgenden Beispiel:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Konfigurieren Sie das öffentliche Azure-Peering für die Verbindung. Stellen Sie sicher, dass die folgenden Informationen vorliegen, bevor Sie fortfahren:

   * Ein /30-Subnetz für die primäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden.
   * **Optional** – Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.

   Führen Sie das folgende Beispiel aus, um das öffentliche Azure-Peering für Ihre Verbindung zu konfigurieren:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Wenn Sie sich für den Einsatz eines MD5-Hash entscheiden, verwenden Sie das folgende Beispiel:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>So zeigen Sie Details zum öffentlichen Azure-Peering an

Sie können Konfigurationsdetails anhand des folgenden Beispiels abrufen:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Aktualisieren der Konfiguration für öffentliches Azure-Peering

Sie können einen beliebigen Teil der Konfiguration anhand des folgenden Beispiels aktualisieren. In diesem Beispiel wird die VLAN-ID der Verbindung von 200 in 600 geändert.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>So löschen Sie ein öffentliches Azure-Peering

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Beispiel ausführen:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Schritte im Azure-Portal

Verwenden Sie die in diesem Artikel enthaltenen PowerShell- oder CLI-Schritte, um das Peering zu konfigurieren. Zum Verwalten eines Peerings können Sie die folgenden Abschnitte verwenden. Diese Schritte ähneln der Verwaltung des [Microsoft-Peerings im Portal](expressroute-howto-routing-portal-resource-manager.md#msft).

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>So zeigen Sie Details zum öffentlichen Azure-Peering an

Zeigen Sie die Eigenschaften des öffentlichen Azure-Peerings an, indem Sie das Peering auswählen.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Aktualisieren der Konfiguration für öffentliches Azure-Peering

Wählen Sie die Zeile für das Peering aus, und ändern Sie dann die Peeringeigenschaften.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>So löschen Sie ein öffentliches Azure-Peering

Entfernen Sie Ihre Peeringkonfiguration, indem Sie das Löschsymbol auswählen.

## <a name="next-steps"></a>Nächste Schritte

Nächster Schritte: [Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md).

* Weitere Informationen zu ExpressRoute-Workflows finden Sie unter [ExpressRoute-Workflows](expressroute-workflows.md).
* Weitere Informationen zum Verbindungspeering finden Sie unter [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md).
* Weitere Informationen zur Arbeit mit virtuellen Netzwerken finden Sie unter [Virtuelle Netzwerke im Überblick](../virtual-network/virtual-networks-overview.md).