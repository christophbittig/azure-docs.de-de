---
title: Private IP-Adressen in Azure
titlesuffix: Azure Virtual Network
description: Erfahren Sie mehr über private IP-Adressen in Azure.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 748d3db418ee41164f7151dc6a62d7cc73a99604
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368254"
---
# <a name="private-ip-addresses"></a>Private IP-Adressen
Private IP-Adressen ermöglichen eine Kommunikation zwischen Ressourcen in Azure. 

Ressourcen können sein:
* Azure-Dienste, z. B.:
    * Netzwerkschnittstellen für virtuelle Computer
    * Interne Lastenausgleichsmodule (ILBs) abrufen
    * Anwendungsgateways
* Bestandteil eines [virtuellen Netzwerks](../../virtual-network/virtual-networks-overview.md).
* Ein lokales Netzwerk, das über ein VPN-Gateway oder eine ExpressRoute-Leitung verbunden ist.

Private IP-Adressen ermöglichen eine Kommunikation mit diesen Ressourcen, ohne dass eine öffentliche IP-Adresse verwendet wird.

## <a name="allocation-method"></a>Zuordnungsmethode

Azure weist Ressourcen private IP-Adressen aus dem Adressbereich des VNET-Subnetzes zu, in dem sich eine Ressource befindet.

Azure reserviert die ersten vier Adressen in jedem Subnetzadressbereich. Diese Adressen können keinen Ressourcen zugewiesen werden. Wenn der Adressbereich des Subnetzes beispielsweise 10.0.0.0/16 ist, sind die Adressen 10.0.0.0 bis 10.0.0.3 und 10.0.255.255 nicht verfügbar. IP-Adressen innerhalb des Adressbereichs des Subnetzes können jeweils nur einer Ressource zugewiesen werden. 

Es gibt zwei Methoden, um private IP-Adressen anzugeben:

- **Dynamisch**: Azure weist die nächste verfügbare nicht zugewiesene oder nicht reservierte IP-Adresse im Adressbereich des Subnetzes zu. Beispielsweise weist Azure 10.0.0.10 einer neuen Ressource zu, wenn die Adressen 10.0.0.4 bis 10.0.0.9 bereits anderen Ressourcen zugewiesen sind. 

    „Dynamisch“ ist das Standardverfahren für die Zuteilung. Dynamische Adressen werden nach der Zuweisung freigegeben, wenn eine Netzwerkschnittstelle:
    
    * Deleted
    * einem anderen Subnetz im gleichen virtuellen Netzwerk zugewiesen wird.
    * die Zuordnungsmethode in „Statisch“ geändert und eine andere IP-Adresse angegeben wird. 
    
    Standardmäßig wird in Azure die zuvor dynamisch zugewiesene Adresse als statische Adresse zugewiesen, wenn Sie die Zuteilungsmethode von „Dynamisch“ in „Statisch“ ändern.

- **Statisch**: Sie wählen eine beliebige nicht zugewiesene oder nicht reservierte IP-Adresse im Adressbereich des Subnetzes aus und weisen sie zu. 

    Wenn der Adressbereich des Subnetzes beispielsweise 10.0.0.0/16 ist und die Adressen 10.0.0.4 bis 10.0.0.9 anderen Ressourcen zugewiesen sind, können Sie eine beliebige Adresse zwischen 10.0.0.10 und 10.0.255.254 zuweisen. Statische Adressen werden nur freigegeben, wenn eine Netzwerkschnittstelle gelöscht wird. 
    
    Azure weist die statische IP-Adresse als dynamische IP-Adresse zu, wenn die Zuordnungsmethode geändert wird. Die Neuzuweisung findet auch dann statt, wenn die Adresse nicht die nächste verfügbare Adresse im Subnetz ist. Die Adresse ändert sich, wenn die Netzwerkschnittstelle einem anderen Subnetz zugewiesen wird.
    
    Um die Netzwerkschnittstelle einem anderen Subnetz zuzuweisen, ändern Sie die Zuordnungsmethode von „Statisch“ in „Dynamisch“. Weisen Sie die Netzwerkschnittstelle einem anderen Subnetz zu, und ändern Sie die Zuordnungsmethode wieder in „Statisch“. Weisen Sie eine IP-Adresse aus dem Adressbereich des neuen Subnetzes zu.
    
## <a name="virtual-machines"></a>Virtuelle Computer

Private IP-Adressen sind **Netzwerkschnittstellen** zugewiesen. Die Netzwerkschnittstellen sind einem virtuellen [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer zugewiesen. Für jede private IP-Adresse können Sie als Zuteilungsmethode „Dynamisch“ oder „Statisch“ angeben.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Interne DNS-Hostnamensauflösung (für virtuelle Computer)

Virtuelle Azure-Computer werden standardmäßig mit [von Azure verwalteten DNS-Servern](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) konfiguriert. Sie können explizit benutzerdefinierte DNS-Server konfigurieren. Diese DNS-Server ermöglichen die interne Namensauflösung für virtuelle Computer, die sich im selben virtuellen Netzwerk befinden.

Den von Azure verwalteten DNS-Servern wird eine Hostnamenzuordnung für die private IP-Adresse des virtuellen Computers hinzugefügt. 

Ein Hostname wird der primären IP-Adresse der Hauptnetzwerkschnittstelle zugeordnet, wenn ein virtueller Computer wie folgt konfiguriert ist:

* Mehrere Netzwerkschnittstellen
* Mehrere IP-Adressen
* Beide

Virtuelle Computer, die mit von Azure verwaltetem DNS konfiguriert sind, können die Hostnamen innerhalb desselben virtuellen Netzwerks auflösen. Verwenden Sie einen benutzerdefinierten DNS-Server, um Hostnamen von virtuellen Computern in verbundenen virtuellen Netzwerken aufzulösen.

## <a name="internal-load-balancers-ilb--application-gateways"></a>Interne Load Balancer (ILB) und Anwendungsgateways

Der **Front-End**-Konfiguration folgender Komponenten kann eine private IP-Adresse zugewiesen werden:

* [Interner Azure-Lastenausgleich](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Azure Application Gateway](../../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Diese private IP-Adresse dient als interner Endpunkt. Der interne Endpunkt ist nur für Ressourcen im jeweiligen virtuellen Netzwerk und den damit verbundenen Remotenetzwerken zugänglich. Es kann eine dynamische oder eine statische IP-Adresse zugewiesen werden.

## <a name="at-a-glance"></a>Auf einen Blick
Die folgende Tabelle zeigt die Eigenschaft, über die eine private IP-Adresse einer Ressource zugeordnet werden kann. 

Darüber hinaus werden die möglichen Zuordnungsmethoden, die verwendet werden können, angezeigt:

* Dynamisch
* statischen

| Ressource der obersten Ebene | Zuordnung der IP-Adresse | Dynamisch | statischen |
| --- | --- | --- | --- |
| Virtueller Computer |Netzwerkschnittstelle |Ja |Ja |
| Load Balancer |Front-End-Konfiguration |Ja |Ja |
| Anwendungsgateway |Front-End-Konfiguration |Ja |Ja |

## <a name="limits"></a>Einschränkungen
Die Grenzwerte für die IP-Adressierung finden Sie in den vollständigen Informationen zu [Grenzwerten für Netzwerke](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. Die Grenzwerte gelten pro Region und pro Abonnement. [Wenden Sie sich an den Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um die Standardgrenzwerte je nach Ihren Unternehmensanforderungen bis auf die maximalen Grenzwerte zu erhöhen.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über [öffentliche IP-Adressen in Azure](public-ip-addresses.md).
* [Bereitstellen eines virtuellen Computers mit einer statischen privaten IP-Adresse mithilfe des Azure-Portals](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)