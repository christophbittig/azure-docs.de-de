---
title: include file
titleSuffix: Azure
description: include file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f7e9b1ed926918496e1205f4a9e642f1cb59522d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909437"
---
Im folgenden Beispiel wird gezeigt, wie Sie ein Direct-Peering mit 10 GBit/s in Seattle erstellen.

Verwenden Sie das PowerShell-Cmdlet **New-AzPeeringDirectConnectionObject**, um DirectConnection-Objekte zu erstellen, die in der neuen Peeringanforderung verwendet werden sollen.

In diesem Beispiel wird die Erstellung eines DirectConnection-Objekts gezeigt.

```powershell
$connection1 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.31.0/31 `
    -SessionPrefixV6 fe01::3e:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

> [!NOTE]
> Der Wert für  **$peeringLocation[]** im obigen Beispiel sollte dem gewünschten Peeringstandort entsprechen.

Erstellen Sie eine weitere Verbindung, falls Redundanz an einem bestimmten Peeringstandort erforderlich ist.

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

Verwenden Sie das PowerShell-Cmdlet **New-AzPeering**, um eine neue Direct-Peeringverbindung zu erstellen. Für diesen Befehl ist eine ASN-Ressourcen-ID erforderlich, die wie hier gezeigt abgerufen werden kann.


```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeeringLocation  $peeringLocation[0].PeeringLocation `
    -PeerAsnResourceId $asn.Id `
    -DirectConnection $connection1 [, $connection2]
```
&nbsp;

Dieses Beispiel zeigt die Antwort für den Fall, dass die Anforderung erfolgreich verarbeitet wurde.

```powershell

    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : 71
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}

```
Beachten Sie, dass anstelle von **{subscriptionId}** in der Ausgabe die tatsächliche Abonnement-ID angezeigt wird.
