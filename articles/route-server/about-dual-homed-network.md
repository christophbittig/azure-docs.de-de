---
title: 'Informationen zu einem Dual-Homed-Netzwerk mit Azure Route Server '
description: Hier erfahren Sie mehr über die Funktionsweise von Azure Route Server in einem Dual-Homed-Netzwerk.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: duau
ms.openlocfilehash: 76c1a1e424e4f8ae372f53ceab5da43ca9d40629
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434572"
---
# <a name="about-dual-homed-network-with-azure-route-server"></a>Informationen zu einem Dual-Homed-Netzwerk mit Azure Route Server

Azure Route Server unterstützt die typische Hub-and-Spoke-Netzwerktopologie. Bei dieser Konfiguration befinden sich sowohl der Routenserver als auch das virtuelle Netzwerkgerät (Network Virtual Appliance, NVA) im virtuellen Hubnetzwerk. Azure Route Server ermöglicht Ihnen auch, eine andere Topologie zu konfigurieren, die als Dual-Homed-Netzwerk bezeichnet wird. Bei dieser Konfiguration verwenden Sie ein virtuelles Spokenetzwerk, das per Peering mit zwei oder mehr virtuellen Hubnetzwerken gekoppelt ist. VMs im virtuellen Spokenetzwerk können über ein virtuelles Hubnetzwerk entweder mit Ihrer lokalen Umgebung oder mit dem Internet kommunizieren.

## <a name="how-to-set-it-up"></a>So funktioniert die Einrichtung

Wie im folgenden Diagramm zu sehen ist, müssen Sie folgende Schritte ausführen:

* Stellen Sie in jedem virtuellen Hubnetzwerk und auf dem Routenserver im virtuellen Spokenetzwerk ein virtuelles Netzwerkgerät (NVA) bereit.
* Aktivieren Sie das VNet-Peering zwischen den virtuellen Hub- und Spokenetzwerken.
* Konfigurieren Sie das BGP-Peering zwischen Route Server und jedem bereitgestellten NVA.

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology.png" alt-text="Diagramm von Route Server in einer Dual-Homed-Topologie":::

### <a name="how-does-it-work"></a>Wie funktioniert dies?

Auf der Steuerungsebene tauschen NVA und Route Server Routen so aus wie bei einer Bereitstellung im gleichen virtuellen Netzwerk. Das virtuelle Netzwerkgerät (NVA) erhält von Route Server Informationen zu den Adressen im virtuellen Spokenetzwerk. Route Server erhält die Routen von den einzelnen NVAs. Route Server programmiert dann alle VMs im virtuellen Spokenetzwerk mit den empfangenen Routen. 

Auf der Datenebene sehen die VMs im virtuellen Spokenetzwerk das Sicherheits-NVA oder das VPN-NVA im Hub als nächsten Hop. Internet- oder standortübergreifender Hybriddatenverkehr wird jetzt über die NVAs im virtuellen Hubnetzwerk geroutet. Sie können beide Hubs so konfigurieren, dass sie entweder aktiv/aktiv oder aktiv/passiv sind. Wenn der aktive Hub ausfällt, wird für den Datenverkehr zu und von den VMs ein Failover auf den anderen Hub ausgeführt. Zu diesen Fehlern zählen u. a. NVA-Ausfälle oder Fehler bei der Dienstkonnektivität. Durch dieses Setup wird sichergestellt, dass Ihr Netzwerk für Hochverfügbarkeit konfiguriert ist.

## <a name="integration-with-expressroute"></a>Integration mit ExpressRoute

Sie können ein Dual-Homed-Netzwerk erstellen, das zwei oder mehr ExpressRoute-Leitungen umfasst. Dazu müssen Sie neben den oben beschriebenen Schritten noch folgende Schritte ausführen:

* Erstellen Sie eine weitere Route Server-Instanz in jedem virtuellen Hubnetzwerk, das über ein ExpressRoute-Gateway verfügt.
* Konfigurieren Sie das BGP-Peering zwischen dem virtuellen Netzwerkgerät und der Route Server-Instanz im virtuellen Hubnetzwerk.
* [Aktivieren Sie den Routenaustausch](quickstart-configure-route-server-portal.md#configure-route-exchange) zwischen dem ExpressRoute-Gateway und der Route Server-Instanz im virtuellen Hubnetzwerk.
* Stellen Sie sicher, dass „Remotegateway verwenden“ oder „Remoteroutenserver verwenden“ in der Konfiguration für das VNet-Peering des virtuellen Spokenetzwerks **deaktiviert** ist.

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology-expressroute.png" alt-text="Diagramm von Route Server in einer Dual-Homed-Topologie mit ExpressRoute":::

### <a name="how-does-it-work"></a>Wie funktioniert dies?

Auf der Steuerungsebene erhält das NVA im virtuellen Hubnetzwerk lokale Routen vom ExpressRoute-Gateway über den [Routenaustausch](quickstart-configure-route-server-portal.md#configure-route-exchange) mit der Route Server-Instanz im Hub. Im Gegenzug sendet das virtuelle Netzwerkgerät die Adressen im virtuellen Spokenetzwerk mithilfe derselben Route Server-Instanz an das ExpressRoute-Gateway. Die Route Server-Instanzen im virtuellen Spoke- und Hubnetzwerk programmieren dann die lokalen Netzwerkadressen auf den VMs in ihrem jeweiligen virtuellen Netzwerk.

> [!IMPORTANT]
> BGP verhindert dabei eine Schleife, indem die AS-Nummer im AS-Pfad überprüft wird. Wenn der empfangende Router seine eigene AS-Nummer im AS-Pfad eines empfangenen BGP-Pakets erkennt, wird das Paket verworfen. In diesem Beispiel haben beide Route Server-Instanzen die gleiche AS-Nummer: 65515. Um zu verhindern, dass eine Route Server-Instanz die Routen von der anderen Instanz verwirft, muss das NVA beim Peering mit jeder Route Server-Instanz die BGP-Richtlinie mit **override** kennzeichnen. 
>

Auf der Datenebene senden die VMs im virtuellen Spokenetzwerk den gesamten Datenverkehr, der für das lokale Netzwerk bestimmt ist, zuerst an die NVA im virtuellen Hubnetzwerk. Anschließend wird der Datenverkehr vom NVA über ExpressRoute an das lokale Netzwerk weitergeleitet. Der Datenverkehr aus der lokalen Umgebung durchläuft denselben Datenpfad in umgekehrter Richtung. Sie werden feststellen, dass sich keine der Route Server-Instanzen im Datenpfad befindet.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Azure Route Server mit Azure ExpressRoute verwendet wird.](expressroute-vpn-support.md)
* [Erfahren Sie, wie Azure Route Server mit einem virtuellen Netzwerkgerät funktioniert.](resource-manager-template-samples.md)

