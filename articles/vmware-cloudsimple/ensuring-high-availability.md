---
title: Sicherstellen der Hochverfügbarkeit von Anwendungen bei Ausführung in VMware in Azure
description: In diesem Artikel werden CloudSimple-Hochverfügbarkeitsfunktionen für die Behandlung häufiger Anwendungsfehlerszenarien von Anwendungen beschrieben, die in einer privaten CloudSimple-Cloud ausgeführt werden.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 741a314665f6678d70dc003eb96bc5e8087bf5bb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322334"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Sicherstellen der Hochverfügbarkeit von Anwendungen bei Ausführung in VMware in Azure

Die CloudSimple-Lösung bietet Hochverfügbarkeit für Ihre Anwendungen, die in VMware in der Azure-Umgebung ausgeführt werden. In der folgenden Tabelle werden Fehlerszenarien und die entsprechenden Hochverfügbarkeitsfunktionen aufgeführt.

|  Fehlerszenario  |  Anwendung geschützt?  |  Hochverfügbarkeitsfunktion für Plattform  |  Hochverfügbarkeitsfunktion für VMware  |  Hochverfügbarkeitsfunktion für Azure  |
|----------------------------------------|------------------------|-------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------|
|  Datenträgerfehler  |  YES  |  Schnelles Ersetzen des fehlerhaften Knotens  |  [Informationen zur vSAN-Standardspeicher-Richtlinie](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html)  |  |
|  Lüfterausfall  |  YES  |  Redundante Lüfter, schnelles Ersetzen des fehlerhaften Knotens  |  |  |
|  NIC-Fehler  |  YES  |  Redundante NIC, schnelles Ersetzen des fehlerhaften Knotens  |  |  |
|  Stromausfall bei Host  |  YES  |  Redundante Stromversorgung  |  |  |
|  ESXi-Hostfehler  |  YES  |  Schnelles Ersetzen des fehlerhaften Knotens  |  [VMware vSphere-Hochverfügbarkeit](https://www.vmware.com/products/vsphere/high-availability.html)  |  |
|  VM-Fehler  |  YES  |  [Lastenausgleichsmodule](load-balancers.md)  |  [VMware vSphere-Hochverfügbarkeit](https://www.vmware.com/products/vsphere/high-availability.html)  |  Azure Load Balancer für zustandslose VMware-VMs  |
|  Fehler an Blattknoten-Switchport  |  YES  |  Redundante NIC  |  |  |
|  Fehler an Blattknoten-Switch  |  YES  |  Redundante Blattknoten-Switches  |  |  |
|  Rack-Fehler  |  YES  |  Platzierungsgruppen  |  |  |
|  Netzwerkkonnektivität zum lokalen Rechenzentrum  |  YES  |  Redundante Netzwerkdienste  |  |  Redundante ExpressRoute-Leitungen  |
|  Netzwerkkonnektivität mit Azure  |  YES  |  |  |  Redundante ExpressRoute-Leitungen  |
|  Rechenzentrumsfehler  |  YES  |  |  |  Verfügbarkeitszonen  |
|  Regionaler Fehler  |  YES  |  |  |  Azure-Regionen  |

Azure VMware Solution by CloudSimple bietet die folgenden Hochverfügbarkeitsfunktionen.

## <a name="fast-replacement-of-failed-node"></a>Schnelles Ersetzen des fehlerhaften Knotens

Die CloudSimple-Software für die Steuerungsebene überwacht kontinuierlich die Integrität der VMware-Cluster und erkennt Ausfälle von ESXi-Knoten. In diesem Fall fügt sie dem betreffenden VMware-Cluster aus seinem Pool verfügbarer Knoten automatisch einen neuen ESXi-Host hinzu und entfernt den ausgefallenen Knoten aus dem Cluster. Durch diese Funktion wird sichergestellt, dass die Reservekapazität im VMware-Cluster schnell wiederhergestellt wird, sodass die von vSAN und VMware-Hochverfügbarkeit bereitgestellte Resilienz des Clusters wiederhergestellt wird.

## <a name="placement-groups"></a>Platzierungsgruppen

Ein Benutzer, der eine private Cloud erstellt, kann eine Azure-Region und eine Platzierungsgruppe innerhalb der ausgewählten Region auswählen. Eine Platzierungsgruppe ist eine Gruppe von Knoten, die auf verschiedene Racks verteilt sind, jedoch innerhalb desselben Spine-Netzwerksegments liegen. Knoten innerhalb derselben Platzierungsgruppe können einander mit maximal zwei zusätzlichen Switchhops erreichen. Eine Platzierungsgruppe befindet sich immer innerhalb einer einzigen Azure-Verfügbarkeitszone und umfasst mehrere Racks. Die CloudSimple-Steuerungsebene verteilt Knoten einer privaten Cloud bestmöglich auf mehrere Racks. Knoten in unterschiedlichen Platzierungsgruppen werden garantiert in unterschiedlichen Racks platziert.

## <a name="availability-zones"></a>Verfügbarkeitszonen

Verfügbarkeitszonen sind ein Hochverfügbarkeitsangebot, das Anwendungen und Daten vor Ausfällen von Rechenzentren schützt. Bei Verfügbarkeitszonen handelt es sich um spezielle physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Jede Region verfügt über eine Verfügbarkeitszone. Weitere Informationen finden Sie unter [Was sind Verfügbarkeitszonen in Azure?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Redundante Azure ExpressRoute-Leitungen

Für die Rechenzentren-Konnektivität mit Azure vNet mit ExpressRoute sind redundante Leitungen vorhanden, die Links für Hochverfügbarkeits-Netzwerkkonnektivität bereitstellen.

## <a name="redundant-networking-services"></a>Redundante Netzwerkdienste

Alle CloudSimple-Netzwerkdienste für die Private Cloud (einschließlich VLAN, Firewall, öffentlichen IP-Adressen, Internet und VPN) sind auf Hochverfügbarkeit ausgelegt und unterstützen die Vereinbarung zum Servicelevel für Dienste.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Azure Load Balancer (Schicht 7) für zustandslose VMware-VMs

Benutzer können einen Azure Load Balancer (Schicht 7) vor den zustandslosen VMs der Webschicht in der VMware-Umgebung platzieren, um Hochverfügbarkeit für die Webschicht zu erzielen.

## <a name="azure-regions"></a>Azure-Regionen

Eine Azure-Region ist eine Reihe von Rechenzentren, die innerhalb eines durch Wartezeit definierten Umkreises bereitgestellt und über ein dediziertes regionales Netzwerk mit geringer Wartezeit verbunden sind. Weitere Informationen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions).
