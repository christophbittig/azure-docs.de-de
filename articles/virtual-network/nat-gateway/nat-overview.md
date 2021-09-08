---
title: Was ist Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Enthält eine Übersicht über Features, Ressourcen, Architektur und Implementierung von Virtual Network NAT. Es wird beschrieben, wie Virtual Network NAT funktioniert und wie Sie NAT-Gatewayressourcen in der Cloud nutzen.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: nat
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: allensu
ms.openlocfilehash: afaee074fcfda1713031468a18039aac8aa398ca
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297062"
---
# <a name="what-is-virtual-network-nat"></a>Was ist Virtual Network NAT?

Virtual Network NAT (Network Address Translation, Netzwerkadressenübersetzung) vereinfacht für virtuelle Netzwerke die Einrichtung von ausschließlich ausgehenden Internetverbindungen. Bei der Konfiguration in einem Subnetz werden für die gesamte Konnektivität in ausgehender Richtung die von Ihnen angegebenen statischen öffentlichen IP-Adressen verwendet.  Die ausgehende Konnektivität ist möglich, ohne dass ein Lastenausgleich oder öffentliche IP-Adressen direkt virtuellen Computern zugeordnet werden. NAT ist vollständig verwaltet und äußerst resilient.

> [!VIDEO https://www.youtube.com/embed/2Ng_uM0ZaB4]

:::image type="content" source="./media/nat-overview/flow-map.png" alt-text="Abbildung einer NAT, die Datenverkehr aus internen Subnetzen empfängt und an eine öffentliche IP-Adresse (PIP) und ein IP-Präfix weiterleitet":::

*Abbildung: Virtual Network NAT*
## <a name="static-ip-addresses-for-outbound-only"></a>Statische IP-Adressen ausschließlich für den Ausgang

Die ausgehende Konnektivität kann für jedes Subnetz mit NAT definiert werden.  Mehrere Subnetze desselben virtuellen Netzwerks können über unterschiedliche NATs verfügen. Ein Subnetz wird konfiguriert, indem angegeben wird, welche NAT-Gatewayressource verwendet werden soll. Für alle ausgehenden UDP- und TCP-Datenflüsse einer VM-Instanz wird NAT genutzt. 

NAT ist mit Ressourcen für öffentliche IP-Adressen und Präfixressourcen für öffentliche IP-Adressen der Standard-SKU kompatibel (bzw. einer Kombination dieser Ressourcen).  Sie können das Präfix einer öffentlichen IP-Adresse direkt nutzen, oder die öffentlichen IP-Adressen des Präfixes auf mehrere NAT-Gatewayressourcen verteilen. Mit NAT wird der gesamte Datenverkehr auf den IP-Adressbereich des Präfixes bereinigt.  Das Filtern von IP-Adressen Ihrer Bereitstellungen ist jetzt sehr einfach.

Der gesamte ausgehende Datenverkehr für das Subnetz wird per NAT ohne Konfiguration durch den Kunden automatisch verarbeitet.  Benutzerdefinierte Routen sind nicht erforderlich. NAT hat Vorrang vor anderen Ausgangsszenarien und ersetzt das Standard-Internetziel eines Subnetzes.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>Bedarfsgesteuerte SNAT mit mehreren IP-Adressen zur Skalierung

Für NAT wird die „Port-Netzwerkadressenübersetzung“ (Port Network Address Translation, PNAT/PAT) verwendet. Diese Vorgehensweise wird für die meisten Workloads empfohlen. Dynamische oder divergente Workloads können über die bedarfsgesteuerte Datenflusszuteilung in ausgehender Richtung problemlos abgedeckt werden. Eine umfassende Vorabplanung, Vorabzuteilung und damit verbundene Überbereitstellung von Ausgangsressourcen wird vermieden. SNAT-Portressourcen werden gemeinsam genutzt und sind in allen Subnetzen verfügbar, indem eine spezifische NAT-Gatewayressource verwendet und bei Bedarf bereitgestellt wird.

Eine mit NAT verknüpfte öffentliche IP-Adresse stellt jeweils bis zu 64.000 gleichzeitige Flows für UDP und TCP bereit. 

Eine NAT-Gatewayressource kann Folgendes nutzen:

* Öffentliche IP-Adresse
* Präfix für öffentliche IP-Adressen

Beide Typen können einem NAT-Gateway zugeordnet werden.

Verwenden Sie eine einzelne IP-Adresse, und skalieren Sie bis auf 16 öffentliche IP-Adressen hoch.

Subnetze in einem virtuellen Netzwerk sind einem NAT-Gateway zugeordnet, um ausgehende Verbindungen zu ermöglichen.  Ein NAT-Gateway verwendet für ausgehende Verbindungen alle der Ressource zugeordneten IP-Adressen.

Dabei erlaubt das NAT-Gateway das Erstellen von Flows aus dem virtuellen Netzwerk in das Internet. Aus dem Internet zurückfließender Datenverkehr ist nur als Antwort auf einen aktiven Datenfluss zulässig.

Im Gegensatz zu SNAT in ausgehender Richtung für Load Balancer gelten bei einem NAT-Gateway keine Einschränkungen in Bezug darauf, welche private IP-Adresse einer VM-Instanz ausgehende Verbindungen herstellen kann.  Es können sowohl für primäre als auch für sekundäre IP-Adressen ausgehende Verbindungen mit NAT hergestellt werden.

## <a name="coexistence-of-inbound-and-outbound"></a>Koexistenz von eingehenden und ausgehenden Verbindungen

NAT ist mit den folgenden Ressourcen der Standard-SKU kompatibel:

- Load Balancer
- Öffentliche IP-Adresse
- Präfix für öffentliche IP-Adressen

Bei der gemeinsamen Verwendung mit NAT stellen diese Ressourcen für Ihre Subnetze die Internetkonnektivität in eingehender Richtung bereit. Per NAT wird die gesamte Internetkonnektivität in ausgehender Richtung aus Ihren Subnetzen bereitgestellt.

NAT und kompatible Features der Standard-SKU sind über die Richtung informiert, in der der Datenfluss gestartet wurde. Eine Koexistenz von Eingangs- und Ausgangsszenarien ist möglich. Bei diesen Szenarien werden die richtigen Netzwerkadressenübersetzungen empfangen, weil diese Features die Datenflussrichtung kennen. 

:::image type="content" source="./media/nat-overview/flow-direction4.png" alt-text="Abbildung eines NAT-Gateways, das ausgehenden Datenverkehr aus einem virtuellen Netzwerk zum Internet unterstützt":::

*Abbildung: Richtung von Virtual Network NAT-Datenflüssen*
## <a name="fully-managed-highly-resilient"></a>Vollständig verwaltet, äußerst resilient

NAT ist von Beginn an vollständig horizontal hochskaliert. Es ist keine Vorbereitung bzw. kein Vorgang zum horizontalen Hochskalieren erforderlich.  Der NAT-Vorgang wird von Azure für Sie verwaltet.  NAT verfügt immer über mehrere Fehlerdomänen und kann mehrere Fehler ohne Dienstausfall überstehen.
## <a name="tcp-reset-for-unrecognized-flows"></a>TCP-Zurücksetzung für nicht erkannte Datenflüsse

Die private Seite von NAT sendet TCP-Zurücksetzungspakete, wenn versucht wird, über eine nicht vorhandene TCP-Verbindung zu kommunizieren. Ein Beispiel hierfür sind Verbindungen, für die das Leerlauftimeout erreicht wurde. Für das nächste empfangene Paket wird eine TCP-Zurücksetzung an die private IP-Adresse zurückgegeben, um das Schließen der Verbindung zu signalisieren und zu erzwingen.

Die öffentliche Seite von NAT generiert keine TCP-Zurücksetzungspakete oder anderen Datenverkehr.  Es wird nur Datenverkehr ausgegeben, der aus dem virtuellen Netzwerk des Kunden stammt.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurierbares TCP-Leerlauftimeout

Standardmäßig wird ein TCP-Leerlauftimeout von vier Minuten verwendet, das auf bis zu 120 Minuten erhöht werden kann. Bei allen Aktivitäten eines Datenflusses kann auch der Leerlaufzeitgeber, einschließlich TCP-Keep-Alives, zurückgesetzt werden.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regionale oder Zonenisolation mit Verfügbarkeitszonen

NAT ist standardmäßig regional. Bei der Erstellung von Szenarien mit [Verfügbarkeitszonen](../../availability-zones/az-overview.md) kann NAT in einer bestimmten Zone isoliert werden (Zonenbereitstellung).

:::image type="content" source="./media/nat-overview/az-directions.png" alt-text="Abbildung mit drei Zonenstapeln, von denen jeder ein NAT-Gateway und ein Subnetz enthält":::

*Abbildung: Virtual Network NAT mit Verfügbarkeitszonen*
## <a name="multi-dimensional-metrics-for-observability"></a>Einblick dank mehrdimensionaler Metriken

Sie können den Betrieb von NAT mit mehrdimensionalen Metriken überwachen, die in Azure Monitor verfügbar gemacht werden. Diese Metriken können verwendet werden, um die Nutzung zu verfolgen und die Problembehandlung durchzuführen.  Von NAT-Gatewayressourcen werden die folgenden Metriken verfügbar gemacht:

- Byte
- Pakete
- Verworfene Pakete
- Gesamte SNAT-Verbindungen
- Der SNAT-Verbindungsstatus ändert sich pro Intervall.

Erfahren Sie mehr über [Metriken zu NAT-Gateways](./nat-metrics.md).
## <a name="sla"></a>SLA

Bei allgemeiner Verfügbarkeit werden für den NAT-Datenpfad mindestens 99,9 % erreicht.

## <a name="pricing"></a>Preise

Preisinformationen finden Sie unter [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="availability"></a>Verfügbarkeit

Virtual Network NAT und die NAT-Gatewayressource sind in allen [Regionen](https://azure.microsoft.com/global-infrastructure/regions/) aller Azure-Clouds verfügbar.

## <a name="suggestions"></a>Vorschläge

Wir möchten wissen, wie wir den Dienst verbessern können. Unter [UserVoice für NAT](https://aka.ms/natuservoice) können Sie Vorschläge zu den nächsten Entwicklungsschritten machen und darüber abstimmen.
## <a name="limitations"></a>Einschränkungen

* NAT ist mit öffentlichen IP-Adressen, Präfixen für öffentliche IP-Adressen und Lastenausgleichsressourcen der Standard-SKU kompatibel. Basic-Ressourcen, z. B. Load Balancer im Tarif „Basic“, und alle davon abgeleiteten Produkte sind nicht mit NAT kompatibel.  Basic-Ressourcen müssen in einem Subnetz angeordnet sein, für das NAT nicht konfiguriert ist.
* Die Familie der IPv4-Adressen wird unterstützt.  NAT interagiert nicht mit der Familie der IPv6-Adressen.  Die NAT kann nicht in einem Subnetz mit IPv6-Präfix bereitgestellt werden.
* NAT kann übergreifend für mehrere virtuelle Netzwerke genutzt werden.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie [wie Sie mit einem Azure NAT Gateway](https://www.youtube.com/watch?v=2Ng_uM0ZaB4) bessere ausgehende Konnektivität erhalten.
* Informieren Sie sich über [NAT-Gatewayressourcen](./nat-gateway-resource.md).
* [Teilen Sie uns bei UserVoice mit, welche Funktionen wir als Nächstes für Virtual Network NAT entwickeln sollen.](https://aka.ms/natuservoice)