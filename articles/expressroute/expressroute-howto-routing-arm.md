---
title: 'Tutorial: Konfigurieren des Peerings für eine ExpressRoute-Leitung: Azure PowerShell'
description: In diesem Tutorial erfahren Sie, wie Sie die Routingkonfiguration für eine ExpressRoute-Leitung mithilfe von PowerShell im Resource Manager-Bereitstellungsmodell erstellen und verwalten.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 79d49a7a9acd30a9af03921c774e6b2430dc848b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228937"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Tutorial: Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung mithilfe von PowerShell

In diesem Tutorial erfahren Sie, wie Sie die Routingkonfiguration für eine ExpressRoute-Leitung mithilfe von PowerShell im Resource Manager-Bereitstellungsmodell erstellen und verwalten. Sie können außerdem den Status prüfen, ein Update durchführen oder Peerings für eine ExpressRoute-Verbindung löschen bzw. deren Bereitstellung aufheben. Wenn Sie eine andere Methode für die Arbeit mit Ihrer Verbindung verwenden möchten, wählen Sie einen Artikel aus der folgenden Liste aus:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-routing-cli.md)
> * [Öffentliches Peering](about-public-peering.md)
> * [Video – Privates Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-routing-classic.md)
> 

Diese Anweisungen gelten nur für Verbindungen, die über Service Provider erstellt wurden, von denen Layer 2-Konnektivitätsdienste angeboten werden. Wenn Sie einen Dienstanbieter nutzen, der verwaltete Layer 3-Dienste anbietet (meist ein IP-VPN, z. B. MPLS), übernimmt Ihr Konnektivitätsanbieter die Konfiguration und Verwaltung des Routings für Sie.

> [!IMPORTANT]
> Derzeit kündigen wir keine von Service Providern konfigurierten Peerings über das Service Management Portal an. Wir arbeiten daran, dass diese Funktion schnell bereitgestellt wird. Informieren Sie sich bei Ihrem Dienstanbieter, bevor Sie BGP-Peerings konfigurieren.
> 

Sie können privates Peering und Microsoft-Peering für eine ExpressRoute-Leitung konfigurieren (öffentliches Azure-Peering ist für neue Leitungen veraltet). Sie können Peerings in beliebiger Reihenfolge konfigurieren. Sie müssen jedoch sicherstellen, dass Sie die Konfiguration jedes Peerings einzeln nacheinander durchführen. Weitere Informationen zu Routingdomänen und Peerings finden Sie unter [ExpressRoute-Routingdomänen](expressroute-circuit-peerings.md). Weitere Informationen zum öffentlichen Peering finden Sie unter [Öffentliches ExpressRoute-Peering](about-public-peering.md).

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Konfigurieren, Aktualisieren und Löschen des Microsoft-Peerings für eine Leitung
> - Konfigurieren, Aktualisieren und Löschen des privaten Azure-Peerings für eine Leitung

## <a name="prerequisites"></a>Voraussetzungen

* Lesen Sie unbedingt die folgenden Seiten, bevor Sie mit der Konfiguration beginnen:
    * [Voraussetzungen](expressroute-prerequisites.md) 
    * [Routinganforderungen](expressroute-routing.md)
    * [Workflows](expressroute-workflows.md)
* Sie benötigen eine aktive ExpressRoute-Verbindung. Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Leitung](expressroute-howto-circuit-arm.md) aus, und lassen Sie die Leitung vom Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Die ExpressRoute-Leitung muss bereitgestellt und aktiviert sein, damit Sie die Cmdlets in diesem Artikel ausführen können.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft-Peering

Dieser Abschnitt unterstützt Sie beim Erstellen, Abrufen, Aktualisieren und Löschen der Microsoft-Peeringkonfiguration für eine ExpressRoute-Verbindung.

> [!IMPORTANT]
> Beim Microsoft-Peering von ExpressRoute-Verbindungen, die vor dem 1. August 2017 konfiguriert wurden, werden alle Dienstpräfixe über das Microsoft-Peering angekündigt, auch wenn keine Routenfilter definiert sind. Beim Microsoft-Peering von ExpressRoute-Verbindungen, die am oder nach dem 1. August 2017 konfiguriert wurden, werden Präfixe erst angekündigt, wenn der Verbindung ein Routenfilter hinzugefügt wurde. Weitere Informationen finden Sie unter [Konfigurieren eines Routenfilters für das Microsoft-Peering](how-to-routefilter-powershell.md).
> 

### <a name="to-create-microsoft-peering"></a>So erstellen Sie Microsoft-Peering

1. Melden Sie sich an, und wählen Sie Ihr Abonnement aus.

   Wenn Sie PowerShell lokal installiert haben, melden Sie sich an. Wenn Sie Azure Cloud Shell verwenden, können Sie diesen Schritt überspringen.

   ```azurepowershell
   Connect-AzAccount
   ```

   Wählen Sie das Abonnement aus, mit dem die ExpressRoute-Verbindung erstellt werden soll.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Erstellen Sie eine ExpressRoute-Verbindung.

   Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md) aus, und lassen Sie sie vom Konnektivitätsanbieter bereitstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie bei ihm die Aktivierung des Microsoft-Peerings anfordern. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing jedoch nicht für Sie verwaltet, setzen Sie die Konfiguration nach dem Erstellen der Leitung mit den folgenden Schritten fort. 

3. Überprüfen Sie, ob die ExpressRoute-Leitung bereitgestellt und auch aktiviert wurde. Nehmen Sie das folgende Beispiel:

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
4. Konfigurieren Sie das Microsoft-Peering für die Verbindung. Bevor Sie fortfahren, stellen Sie sicher, dass Ihnen die folgenden Informationen vorliegen.

   * Ein /30- oder /126-Subnetz für die primäre Verknüpfung. Der Adressblock muss ein gültiges öffentliches IPv4- oder IPv6-Präfix sein, das sich in Ihrem Besitz befindet und in einer RIR/IRR registriert ist.
   * Ein /30- oder /126-Subnetz für die sekundäre Verknüpfung. Der Adressblock muss ein gültiges öffentliches IPv4- oder IPv6-Präfix sein, das sich in Ihrem Besitz befindet und in einer RIR/IRR registriert ist.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden.
   * Angekündigte Präfixe: Sie geben eine Liste mit allen Präfixen an, die Sie über die BGP-Sitzung ankündigen möchten. Nur öffentliche IP-Adresspräfixe werden akzeptiert. Wenn Sie planen, einen Satz mit Präfixen zu senden, können Sie eine durch Komma getrennte Liste senden. Diese Präfixe müssen über eine RIR/IRR-Registrierung für Sie verfügen. IPv4-BGP-Sitzungen erfordern von IPv4 angekündigte Präfixe und IPv6-BGP-Sitzungen erfordern von IPv6 angekündigte Präfixe. 
   * Routing-Registrierungsname: Sie können den RIR/IRR-Wert angeben, unter dem die AS-Nummer und die Präfixe registriert sind.
   * Optional:
     * Kunden-ASN: Wenn Sie Präfixe ankündigen, die nicht für die Peering-AS-Nummer registriert sind, können Sie die AS-Nummer angeben, für die sie registriert sind.
     * Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.

> [!IMPORTANT]
> Microsoft überprüft, ob Ihnen die angegebenen „Angekündigten öffentlichen Präfixe“ und „Peer-ASN" (oder „Kunden-ASN“) in der Internet Routing Registry zugewiesen werden. Wenn Sie die öffentlichen Präfixe aus einer anderen Entität erhalten und die Zuweisung nicht mit der Routing Registry aufgezeichnet wird, wird die automatische Überprüfung nicht durchgeführt, sodass eine manuelle Überprüfung erforderlich ist. Wenn bei der automatischen Überprüfung ein Fehler auftritt, wird für „AdvertisedPublicPrefixesState“ in der Ausgabe von „Get-AzExpressRouteCircuitPeeringConfig“ die Meldung „Überprüfung erforderlich“ angezeigt (siehe „So rufen Sie Microsoft-Peering-Details ab“ im folgenden Abschnitt). 
> 
> Wenn die Meldung „Überprüfung erforderlich“ angezeigt wird, sammeln Sie die Dokumente mit den öffentlichen Präfixen, die Ihrer Organisation von der in der Routing Registry als Besitzer der Präfixe aufgeführten Entität zugewiesen wurden. Eröffnen Sie ein Supportticket, um diese Dokumente zur manuellen Überprüfung zu senden, wie unten gezeigt. 
> 

   Verwenden Sie das folgende Beispiel, um das Microsoft-Peering für Ihre Verbindung zu konfigurieren:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>So rufen Sie Details zum Microsoft-Peering ab

Sie können Konfigurationsdetails anhand des folgenden Beispiels abrufen:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>So aktualisieren Sie die Konfiguration des Microsoft-Peerings:

Sie können einen beliebigen Teil der Konfiguration anhand des folgenden Beispiels aktualisieren:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Privates Azure-Peering:

Dieser Abschnitt unterstützt Sie beim Erstellen, Abrufen, Aktualisieren und Löschen der privaten Azure-Peeringkonfiguration für eine ExpressRoute-Verbindung.

### <a name="to-create-azure-private-peering"></a>So erstellen Sie ein privates Azure-Peering

1. Importieren Sie das PowerShell-Modul für ExpressRoute.

   Installieren Sie das neueste PowerShell-Installationsprogramm aus dem [PowerShell-Katalog](https://www.powershellgallery.com/). Importieren Sie dann die Azure Resource Manager-Module in die PowerShell-Sitzung, damit Sie die ExpressRoute-Cmdlets verwenden können. Sie müssen PowerShell als Administrator ausführen.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importieren Sie alle Az.\*-Module im bekannten semantischen Versionsbereich.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Sie können auch lediglich ein ausgewähltes Modul im bekannten semantischen Versionsbereich importieren.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Melden Sie sich bei Ihrem Konto an.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Wählen Sie das Abonnement aus, mit dem die ExpressRoute-Verbindung erstellt werden soll.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Erstellen Sie eine ExpressRoute-Verbindung.

   Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md) aus, und lassen Sie sie vom Konnektivitätsanbieter bereitstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie bei ihm die Aktivierung des privaten Azure-Peerings anfordern. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing jedoch nicht für Sie verwaltet, setzen Sie die Konfiguration nach dem Erstellen der Leitung mit den folgenden Schritten fort.

3. Überprüfen Sie, ob die ExpressRoute-Leitung bereitgestellt und auch aktiviert wurde. Nehmen Sie das folgende Beispiel:

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
4. Konfigurieren Sie das private Azure-Peering für die Verbindung. Bevor Sie mit den nächsten Schritten fortfahren, stellen Sie sicher, dass Sie über Folgendes verfügen:

   * Sie benötigen zwei Subnetze, die nicht zu einem für virtuelle Netzwerke reservierten Adressraum gehören. Ein Subnetz wird für den primären Link verwendet, während das andere Subnetz für den sekundären Link verwendet wird. Über jedes dieser Subnetze weisen Sie die erste verwendbare IP-Adresse für Ihren Router zu, da die zweite verwendbare IP-Adresse von Microsoft für den eigenen Router genutzt wird. Sie verfügen über drei Optionen für dieses Paar von Subnetzen:
       * IPv4: zwei /30-Subnetze
       * IPv6: zwei /126-Subnetze
       * Beides: zwei /30-Subnetze und zwei /126-Subnetze
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden. Sie können eine private AS-Nummer für dieses Peering verwenden. Stellen Sie sicher, dass Sie 65515 verwenden.
   * Optional:
     * Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.

   Verwenden Sie das folgende Beispiel, um das private Azure-Peering für Ihre Verbindung zu konfigurieren:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Wenn Sie sich für den Einsatz eines MD5-Hash entscheiden, verwenden Sie das folgende Beispiel:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>So rufen Sie Details zum privaten Azure-Peering ab

Sie können Konfigurationsdetails anhand des folgenden Beispiels abrufen:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>So aktualisieren Sie die Konfiguration für privates Azure-Peering:

Sie können einen beliebigen Teil der Konfiguration anhand des folgenden Beispiels aktualisieren. In diesem Beispiel wird die VLAN-ID der Verbindung von 200 auf 500 aktualisiert.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>So löschen Sie das Microsoft-Peering:

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Cmdlet ausführen:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>So löschen Sie ein privates Azure-Peering:

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Beispiel ausführen:

> [!WARNING]
> Sie müssen sicherstellen, dass alle virtuellen Netzwerke und ExpressRoute Global Reach-Verbindungen entfernt wurden, bevor Sie dieses Beispiel ausführen. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie das private Azure-Peering konfiguriert haben, können Sie ein ExpressRoute-Gateway erstellen, um ein virtuelles Netzwerk mit der Leitung zu verknüpfen. 

> [!div class="nextstepaction"]
> [Konfigurieren eines Gateways für ein virtuelles Netzwerk für ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
