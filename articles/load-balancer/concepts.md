---
title: 'Azure Load Balancer: Konzepte'
description: Übersicht über die Azure Load Balancer-Konzepte
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 946741c8aa70040dd0186deceab0fb090cf38c11
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271743"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer-Algorithmus

Load Balancer verfügt über mehrere Funktionen für UDP- und TCP-Anwendungen.

## <a name="load-balancing-algorithm"></a>Lastenausgleichsalgorithmus

Indem Sie eine Lastenausgleichsregel erstellen, können Sie eingehende Datenverkehrsflüsse vom Front-End eines Lastenausgleichs an die Back-End-Pools verteilen. Azure Load Balancer nutzt einen Fünf-Tupel-Hashalgorithmus für die Verteilung eingehender Datenflüsse (keine Bytes).  Beim Lastenausgleich werden die Header von TCP-/UDP-Headern umgeschrieben, wenn Datenverkehr an die Back-End-Poolinstanzen geleitet wird. (HTTP-/HTTPS-Header werden nicht umgeschrieben). Wenn der Integritätstest des Lastenausgleichs auf einen fehlerfreien Back-End-Endpunkt hinweist, stehen Back-End-Instanzen zum Empfangen neuer Datenverkehrsflüsse zur Verfügung.

Standardmäßig wird für den Lastenausgleich ein Fünf-Tupel-Hash verwendet.

Der Hash umfasst Folgendes:

- **Quell-IP-Adresse**
- **Quellport**
- **Ziel-IP-Adresse**
- **Zielport**
- **IP-Protokollnummer zum Zuordnen von Flows zu verfügbaren Servern**

## <a name="session-persistence"></a>Sitzungspersistenz

Pakete desselben Datenflusses gehen bei derselben Back-End-Poolinstanz ein. Wenn ein Client jedoch einen neuen Datenfluss über dieselbe IP-Quelladresse initiiert, ändert sich der Quellport. Dies kann dazu führen, dass der Datenverkehr aufgrund des Fünf-Tupel-Hashs an einen anderen Back-End-Endpunkt gesendet wird. 

Sitzungspersistenz zu einer IP-Quelladresse wird anhand eines Zwei- oder Drei-Tupel-Hashs hergestellt. Wenn Sitzungspersistenz aktiviert ist, werden aufeinanderfolgende Anforderungen von derselben Client-IP-Adresse von derselben VM verarbeitet. Weitere Informationen zu Load Balancer-Verteilungsmodi finden Sie unter [Konfigurieren des Verteilungsmodus für Azure Load Balancer](./load-balancer-distribution-mode.md).

In der folgenden Abbildung wird die hashbasierte Verteilung angezeigt:

![Hash-basierte Verteilung](./media/load-balancer-overview/load-balancer-distribution.png)

*Abbildung: Hashbasierte Verteilung*

## <a name="application-independence-and-transparency"></a>Anwendungsunabhängigkeit und -transparenz

Load Balancer unterstützt jedes TCP-/UDP-Anwendungsszenario, aber schließt oder startet keine Datenflüsse. Außerdem interagiert Load Balancer nicht mit den Nutzdaten eines Datenflusses. 

- Anwendungsnutzlasten sind für das Lastenausgleichsmodul transparent. Alle UDP- oder TCP-Anwendungen können unterstützt werden.

Load Balancer wird auf Schicht 4 betrieben und bietet keine Gatewayfunktionalität auf Anwendungsebene. Protokollhandshakes werden immer direkt zwischen dem Client und der Back-End-Poolinstanz durchgeführt. 

- Da Load Balancer nicht mit den TCP-Nutzdaten interagiert und keine TLS-Auslagerung bereitstellt, können Sie umfassende verschlüsselte Szenarien erstellen. Die Verwendung eines Lastenausgleichsmoduls ermöglicht ein hohes Maß an Aufskalierung für TLS-Anwendungen, indem die TLS-Verbindung auf dem virtuellen Computer selbst beendet wird. Beispielsweise ist die TLS-Funktion zum erstellen von Sitzungsschlüsseln vom Typ und der Nummer der VMs beschränkt, die Sie zum Back-End-Pool hinzufügen.

Bei einer Antwort auf einen eingehenden Flow handelt es sich immer um die Antwort eines virtuellen Computers. Wenn der Flow auf dem virtuellen Computer eingeht, wird auch die IP-Adresse der ursprünglichen Quelle gespeichert.

- Jeder Endpunkt erhält über eine VM eine Antwort. Zum Beispiel wird ein TCP-Handshake zwischen dem Client und der ausgewählten Back-End-VM durchgeführt. Eine Antwort auf eine Anforderung, die an ein Front-End gesendet wird, wird von einer Back-End-VM generiert. Wenn Sie eine erfolgreiche Überprüfung der Konnektivität für ein Front-End durchführen, bedeutet dies, dass Sie den Konnektivitätsdurchsatz für mindestens eine Back-End-VM überprüfen.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Komponenten](components.md), aus denen Azure Load Balancer besteht.
- Informationen zu den ersten Schritten mit einer Load Balancer-Instanz finden Sie unter [Schnellstart: Erstellen eines Load Balancers im Tarif „Standard“ für den Lastenausgleich virtueller Computer über das Azure-Portal](quickstart-load-balancer-standard-public-portal.md). Dort erfahren Sie, wie Sie eine Load Balancer-Instanz und virtuelle Computer mit einer installierten benutzerdefinierten IIS-Erweiterung erstellen und den Lastenausgleich für die Web-App zwischen den virtuellen Computern vornehmen.
- Informieren Sie sich über [Azure Load Balancer – Ausgehende Verbindungen](load-balancer-outbound-connections.md).
- Weitere Informationen zu [Azure Load Balancer](load-balancer-overview.md).
- Informationen zu [Integritätstests](load-balancer-custom-probe-overview.md)
- Weitere Informationen zu [Diagnosen für Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).
