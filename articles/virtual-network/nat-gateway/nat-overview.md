---
title: Was ist Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Enthält eine Übersicht über Features, Ressourcen, Architektur und Implementierung von Virtual Network NAT. Es wird beschrieben, wie Virtual Network NAT funktioniert und wie Sie NAT-Gatewayressourcen in der Cloud nutzen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: nat
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: allensu
ms.openlocfilehash: 5e2b9baeef03163e74ad83c97d8e8148d7f6cfaf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228316"
---
# <a name="what-is-virtual-network-nat"></a>Was ist Virtual Network NAT?

Virtual Network NAT ist ein vollständig verwalteter und äußerst resilienter NAT-Dienst (Network Address Translation, Netzwerkadressenübersetzung). Virtual Network NAT vereinfacht die ausgehende Internetkonnektivität für virtuelle Netzwerke. Bei der Konfiguration in einem Subnetz werden für die gesamte Konnektivität in ausgehender Richtung die statischen öffentlichen IP-Adressen von Virtual Network NAT verwendet. 

:::image type="content" source="./media/nat-overview/flow-map.png" alt-text="Abbildung einer NAT, die Datenverkehr aus internen Subnetzen empfängt und an eine öffentliche IP-Adresse (PIP) und ein IP-Präfix weiterleitet":::

*Abbildung: Virtual Network NAT*

## <a name="vnet-nat-benefits"></a>Vorteile von Virtual Network NAT

### <a name="security"></a>Sicherheit
Mit NAT benötigen einzelne VMs (oder andere Computeressourcen) keine öffentlichen IP-Adressen und können vollständig privat bleiben. Solche Ressourcen ohne öffentliche IP-Adresse können weiterhin externe Quellen außerhalb des VNet erreichen. Sie können auch ein Präfix für öffentliche IP-Adressen zuordnen, um sicherzustellen, dass ein zusammenhängender Satz von IP-Adressen für ausgehenden Datenverkehr verwendet wird. Sie können dann Zielfirewallregeln basierend auf dieser vorhersagbaren IP-Adressliste konfigurieren.

### <a name="resiliency"></a>Resilienz 
NAT ist ein vollständig verwalteter und verteilter Dienst. Er hängt nicht von einzelnen Compute-Instanzen ab, also z. B. von VMs oder einem einzelnen physischen Gatewaygerät. Für eine hohe Resilienz wird Software-Defined Networking genutzt. 

### <a name="scalability"></a>Skalierbarkeit
NAT kann einem Subnetz zugeordnet und von allen Computeressourcen in diesem Subnetz verwendet werden. Darüber hinaus können alle Subnetze in einem VNet dieselbe Ressource nutzen. Wenn der Dienst einem Präfix für öffentliche IP-Adressen zugeordnet ist, wird er automatisch auf die Anzahl der IP-Adressen skaliert, die für ausgehenden Datenverkehr benötigt werden.

### <a name="performance"></a>Leistung
NAT wirkt sich nicht auf die Netzwerkbandbreite Ihrer Computeressourcen aus, da es sich um einen Software-Defined Networking-Dienst handelt. Erfahren Sie mehr über die [Leistung von NAT-Gateways](nat-gateway-resource.md#performance).


## <a name="vnet-nat-basics"></a>Grundlagen von Virtual Network NAT

NAT kann in einer bestimmten Verfügbarkeitszone erstellt werden und bietet Redundanz innerhalb der angegebenen Zone. NAT ist standardmäßig nicht zonal. In Szenarien mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md) kann NAT in einer bestimmten Zone isoliert werden. Dies wird als Zonenbereitstellung bezeichnet.

NAT ist von Beginn an vollständig horizontal hochskaliert. Es ist keine Vorbereitung bzw. kein Vorgang zum horizontalen Hochskalieren erforderlich.  Der NAT-Vorgang wird von Azure für Sie verwaltet.  NAT verfügt immer über mehrere Fehlerdomänen und kann mehrere Fehler ohne Dienstausfall überstehen.

* Die ausgehende Konnektivität kann für jedes Subnetz mit NAT definiert werden.  Mehrere Subnetze desselben virtuellen Netzwerks können über unterschiedliche NATs verfügen. Ein Subnetz wird konfiguriert, indem angegeben wird, welche NAT-Gatewayressource verwendet werden soll.  Der gesamte ausgehende Datenverkehr für das Subnetz wird per NAT ohne Konfiguration durch den Kunden automatisch verarbeitet.  Benutzerdefinierte Routen sind nicht erforderlich. NAT hat Vorrang vor anderen Ausgangsszenarien und ersetzt das Standard-Internetziel eines Subnetzes.
* NAT unterstützt nur die Protokolle TCP und UDP. ICMP wird nicht unterstützt.
* Eine NAT-Gatewayressource kann Folgendes nutzen:

  * Öffentliche IP-Adresse
  * Präfix für öffentliche IP-Adressen
* NAT ist mit Ressourcen für öffentliche IP-Adressen und Präfixressourcen für öffentliche IP-Adressen der Standard-SKU kompatibel (bzw. einer Kombination dieser Ressourcen). Sie können das Präfix einer öffentlichen IP-Adresse direkt nutzen, oder die öffentlichen IP-Adressen des Präfixes auf mehrere NAT-Gatewayressourcen verteilen. Mit NAT wird der gesamte Datenverkehr auf den IP-Adressbereich des Präfixes bereinigt. Basic-Ressourcen wie Load Balancer Basic oder öffentliche IP-Adressen der Basic-SKU sind nicht mit NAT kompatibel.  Basic-Ressourcen müssen in einem Subnetz platziert werden, das keinem NAT-Gateway zugeordnet ist.
* NAT kann nicht einer öffentlichen IPv6-Adresse oder einem Präfix für öffentliche IPv6-Adressen zugeordnet werden. Der Dienst kann jedoch einem Dual Stack-Subnetz zugeordnet werden.
* Bei NAT können Datenflüsse aus dem virtuellen Netzwerk an den Dienst außerhalb Ihres VNet erstellt werden. Aus dem Internet zurückfließender Datenverkehr ist nur als Antwort auf einen aktiven Datenfluss zulässig. Dienste außerhalb Ihres VNet können keine Verbindung mit Instanzen initiieren.
* NAT kann übergreifend für mehrere virtuelle Netzwerke genutzt werden.
* NAT kann nicht in einem [Gatewaysubnetz](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub) bereitgestellt werden.
* Die private Seite des NAT-Diensts (VM-Instanzen oder andere Computeressourcen) sendet TCP-Zurücksetzungspakete für Versuche, über eine TCP-Verbindung zu kommunizieren, die es nicht gibt. Ein Beispiel hierfür sind Verbindungen, für die das Leerlauftimeout erreicht wurde. Für das nächste empfangene Paket wird eine TCP-Zurücksetzung an die private IP-Adresse zurückgegeben, um das Schließen der Verbindung zu signalisieren und zu erzwingen. Die öffentliche Seite von NAT generiert keine TCP-Zurücksetzungspakete oder anderen Datenverkehr.  Es wird nur Datenverkehr ausgegeben, der aus dem virtuellen Netzwerk des Kunden stammt.
* Standardmäßig wird ein TCP-Leerlauftimeout von vier Minuten verwendet, das auf bis zu 120 Minuten erhöht werden kann. Bei allen Aktivitäten eines Datenflusses kann auch der Leerlaufzeitgeber, einschließlich TCP-Keep-Alives, zurückgesetzt werden.

## <a name="pricing-and-sla"></a>Preise und SLA

Preisinformationen finden Sie unter [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network). Der NAT-Datenpfad bietet eine Verfügbarkeit von mindestens 99,9 %.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie [wie Sie mit einem Azure NAT Gateway](https://www.youtube.com/watch?v=2Ng_uM0ZaB4) bessere ausgehende Konnektivität erhalten.
* Informieren Sie sich über [NAT-Gatewayressourcen](./nat-gateway-resource.md).
* Erfahren Sie mehr über [Metriken zu NAT-Gateways](./nat-metrics.md).
