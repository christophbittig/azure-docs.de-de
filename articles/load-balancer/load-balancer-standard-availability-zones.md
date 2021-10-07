---
title: Azure Load Balancer und Verfügbarkeitszonen
titleSuffix: Azure Load Balancer
description: In diesem Lernpfad beginnen Sie mit Azure Load Balancer Standard und Verfügbarkeitszonen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 87a7d10c9748a2e173d8f43dcca3611666277792
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653703"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer und Verfügbarkeitszonen

Azure Load Balancer unterstützt Szenarien mit Verfügbarkeitszonen. Sie können Load Balancer Standard verwenden, um die Verfügbarkeit im gesamten Szenario zu erhöhen, indem Sie Ressourcen an Zonen ausrichten und die Verteilung zonenübergreifend ausführen.  Lesen Sie dieses Dokument, um diese Konzepte und die Entwurfsanleitung für das grundlegende Szenario zu verstehen.

Ein Load Balancer kann **zonenredundant, zonal** oder **nicht zonal** sein. Um die oben genannten Zoneneigenschaften für den Load Balancer zu konfigurieren, wählen Sie den entsprechenden Typ des erforderlichen Front-Ends aus.

## <a name="zone-redundant"></a>Zonenredundant

In einer Region mit Verfügbarkeitszonen kann ein Load Balancer Standard zonenredundant sein. Dieser Datenverkehr wird mit einer einzelnen IP-Adresse bereitgestellt.

Eine einzelne Front-End-IP-Adresse übersteht einen Zonenfehler. Die Front-End-IP kann verwendet werden, um alle (nicht betroffenen) Back-End-Poolmitglieder unabhängig von der Zone zu erreichen. Der Datenpfad bleibt im Falle eines Ausfalls einzelner oder mehrerer Verfügbarkeitszonen verfügbar, solange noch eine einzelne fehlerfreie Zone in der Region vorhanden ist.

Die IP-Adresse des Front-Ends wird gleichzeitig durch mehrere unabhängige Infrastrukturbereitstellungen in mehreren Verfügbarkeitszonen bereitgestellt. Alle Wiederholungen oder Wiederherstellungen sind in anderen Zonen erfolgreich, die nicht von dem Zonenausfall betroffen sind.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Abbildung: Zonenredundanter Load Balancer*

## <a name="zonal"></a>Zonal

Sie können festlegen, dass ein Front-End einer einzelnen Zone zugesichert wird. Das Front-End wird dann als *zonales* Front-End bezeichnet.  Dieses Szenario bedeutet, dass alle eingehenden oder ausgehenden Datenströme von einer einzelnen Zone in einer Region bereitgestellt werden.  Ihr Front-End ist von der Integrität der Zone abhängig.  Der Datenpfad ist nicht betroffen von Fehlern in Zonen, in denen er nicht zugesichert wurde. Mit zonalen Front-Ends können Sie eine IP-Adresse pro Verfügbarkeitszone verfügbar machen.  

Darüber hinaus wird die Verwendung zonaler Front-Ends direkt für Endpunkte mit Lastenausgleich innerhalb der einzelnen Zonen unterstützt. Sie können diese Konfiguration verwenden, um pro Zone Lastenausgleichsendpunkte zur individuellen Überwachung jeder Zone verfügbar zu machen. Bei öffentlichen Endpunkten können Sie sie mit einem DNS-Lastenausgleichsprodukt wie [Traffic Manager](../traffic-manager/traffic-manager-overview.md) kombinieren und einen einzelnen DNS-Namen verwenden.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Abbildung: Zonales Lastenausgleichsmodul*

Für ein öffentliches Load Balancer-Front-End fügen Sie der öffentlichen IP-Adresse einen **zones**-Parameter hinzu. Auf diese öffentliche IP-Adresse wird von der Front-End-IP-Konfiguration verwiesen, die von der jeweiligen Regel verwendet wird.

Für ein internes Load Balancer-Front-End fügen Sie der internen Load Balancer-Front-End-IP-Konfiguration einen **zones**-Parameter hinzu. Ein zonales Front-End sichert eine IP-Adresse in einem Subnetz für eine bestimmte Zone zu.

## <a name="non-zonal"></a>Nicht zonal

Load Balancer können auch in einer nicht zonalen Konfiguration mithilfe eines Front-Ends ohne Zone (öffentliche IP-Adresse oder öffentliches IP-Adressenpräfix) erstellt werden.  Diese Option bietet keine Garantie für Redundanz. Beachten Sie, dass alle öffentlichen IP-Adressen, die von der Basic-SKU auf die Standard-SKU [geupgradet](https://docs.microsoft.com/azure/virtual-network/public-ip-upgrade-portal) werden, den Typ „no-zone“ haben.

## <a name="design-considerations"></a><a name="design"></a> Überlegungen zum Entwurf

Nachdem Sie nun die Zoneneigenschaften für Load Balancer Standard verstanden haben, sind die folgenden Entwurfsüberlegungen beim Entwerfen von Hochverfügbarkeit möglicherweise hilfreich.

### <a name="tolerance-to-zone-failure"></a>Toleranz für Zonenfehler

- Ein **zonenredundanter** Load Balancer kann für eine zonale Ressource in jeder Zone mit einer IP-Adresse verwendet werden.  Die IP-Adresse kann mindestens einen Zonenfehler überstehen, solange mindestens eine Zone in der Region fehlerfrei bleibt.
- Ein **zonales** Front-End ist eine Reduzierung des Diensts auf eine einzelne Zone und von der jeweiligen Zone abhängig. Wenn die Zone, in der sich Ihre Bereitstellung befindet, ausfällt, wird dies von der Bereitstellung nicht überstanden.

Es wird empfohlen, für Produktionsworkloads einen zonenredundanten Load Balancer zu verwenden.

### <a name="multiple-frontends"></a>Mehrere Front-Ends

Mithilfe mehrerer Front-Ends können Sie einen Lastenausgleich für Datenverkehr auf mehrere Ports und/oder IP-Adressen ermöglichen.  Beim Entwerfen Ihrer Architektur ist es wichtig, die Art und Weise zu berücksichtigen, wie Zonenredundanz und mehrere Front-Ends interagieren können.  Beachten Sie, dass alle IP-Adressen, die als Front-Ends zugewiesen sind, zonenredundant sein müssen, wenn das Ziel ist, dass jedes Front-End immer ausfallsicher ist.   Wenn eine Gruppe von Front-Ends einer einzelnen Zone zugeordnet werden soll, muss jede IP-Adresse für diese Gruppe dieser spezifischen Zone zugeordnet werden.  Es ist nicht erforderlich, für jede Zone einen Lastenausgleich zu haben. Stattdessen könnte jedes zonale Front-End (oder die Gruppe zonaler Front-Ends) virtuellen Computern im Back-End-Pool zugeordnet werden, die Teil dieser spezifischen Verfügbarkeitszone sind.

### <a name="transition-between-regional-zonal-models"></a>Übergang zwischen regionalen zonalen Modellen

Wenn eine Region um [Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview) erweitert wird, bleiben alle vorhandenen Front-End-IP-Adressen nicht zonal. Um sicherzustellen, dass Ihre Architektur die neuen Zonen nutzen kann, wird empfohlen, neue Front-End-IP-Adressen zu erstellen und die entsprechenden Regeln und Konfigurationen zu replizieren, um diese neuen öffentlichen IP-Adressen zu nutzen.

### <a name="control-vs-data-plane-implications"></a>Auswirkungen auf Steuerungsebene und Datenebene

Zonenredundanz impliziert weder eine störungsfreie Datenebene noch eine störungsfreie Steuerebene. Zonenredundante Datenströme können alle Zonen verwenden, und Ihre Datenströme verwenden alle fehlerfreien Zonen in einer Region. Bei einem Zonenfehler sind Datenströme, die fehlerfreie Zonen verwenden, nicht betroffen.

Datenströme, die zum Zeitpunkt des Zonenausfalls eine Zone verwenden, können beeinträchtigt werden, aber Anwendungen können wiederhergestellt werden. Der Datenverkehr wird in den fehlerfreien Zonen innerhalb der Region nach nochmaliger Übertragung fortgesetzt, wenn Azure den Zonenausfall in den Griff bekommen hat.

Informationen zur Verbesserung der Resilienz Ihrer Anwendung in Ausfallszenarien finden Sie unter [Cloudentwurfsmuster](/azure/architecture/patterns/).

## <a name="limitations"></a>Einschränkungen

* Zonen können nach der Erstellung nicht geändert, aktualisiert oder für die Ressource erstellt werden.
* Nach der Erstellung kann für Ressourcen kein Update von der zonalen auf die zonenredundante Einstellung oder umgekehrt ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Verfügbarkeitszonen](../availability-zones/az-overview.md)
- Weitere Informationen zum [Load Balancer Standard](./load-balancer-overview.md).
- Informationen über den [Lastenausgleich für VMs innerhalb einer Zone mit einem zonalen Load Balancer Standard](./quickstart-load-balancer-standard-public-cli.md)
- Informationen über den [zonenübergreifenden Lastenausgleich für VMs mit einem zonenredundanten Load Balancer Standard](./quickstart-load-balancer-standard-public-cli.md)
- Machen Sie sich mit [Cloudentwurfsmustern](/azure/architecture/patterns/) vertraut, um die Resilienz Ihrer Anwendung in Ausfallszenarien zu verbessern.
