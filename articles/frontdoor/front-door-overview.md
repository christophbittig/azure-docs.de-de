---
title: Azure Front Door | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht zu Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 03/09/2021
ms.author: duau
ms.openlocfilehash: 708112634c6c6d25c506e5866f7f3588298991c9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738016"
---
# <a name="what-is-azure-front-door"></a>Was ist Azure Front Door?

> [!IMPORTANT]
> Diese Dokumentation ist für Azure Front Door gedacht. Sie suchen nach Informationen zu Azure Front Door Standard/Premium (Vorschau)? Informationen finden Sie [hier](standard-premium/overview.md).

Azure Front Door ist ein globaler, skalierbarer Einstiegspunkt, der das globale Microsoft-Edge-Netzwerk nutzt, um schnelle, sichere und weit skalierbare Webanwendungen zu erstellen. Mit Front Door können Sie Ihre globalen Consumer- und Unternehmensanwendungen in robuste, hochleistungsfähige personalisierte moderne Anwendungen mit Inhalten transformieren, die über Azure eine globale Zielgruppe erreichen.

<p align="center">
  <img src="./media/front-door-overview/front-door-visual-diagram.png" alt="Front Door architecture" width="600" title="Azure Front Door">
</p>

Front Door arbeitet in Schicht 7 (HTTP/HTTPS-Schicht) unter Verwendung des Anycast-Protokolls mit Split TCP und dem globalen Netzwerk von Microsoft zur Verbesserung der globalen Konnektivität. Basierend auf Ihrer Routingmethode können Sie sicherstellen, dass Front Door Ihre Clientanforderungen an das schnellste und verfügbarste Anwendungs-Back-End weiterleitet. Ein Anwendungs-Back-End ist ein Dienst mit Internetzugriff, der innerhalb oder außerhalb von Azure gehostet wird. Front Door bietet eine Reihe von [Datenverkehrsrouting-Methoden](front-door-routing-methods.md) und [Back-End-Systemüberwachungsoptionen](front-door-health-probes.md), die verschiedene Anwendungsanforderungen und automatische Failoverszenarien erfüllen. Ähnlich wie [Traffic Manager](../traffic-manager/traffic-manager-overview.md) zeichnet sich Front Door durch eine geringe Fehleranfälligkeit aus, selbst wenn es zum Ausfall einer ganzen Azure-Region kommt.

>[!NOTE]
> Azure stellt eine Sammlung aus vollständig verwalteten Lastenausgleichslösungen für Ihre Szenarios bereit. 
> * Wenn Sie DNS-basiertes globales Routing durchführen möchten und **keine** Anforderungen zur Beendigung der TLS-Protokollierung (Transport Layer Security, „SSL Offloading“) oder zur Verarbeitung der Anwendungsschicht pro HTTP/HTTPS-Anforderung vorliegen, lesen Sie den Artikel zu [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Wenn Sie Lastenausgleich zwischen den Servern in einer Region auf Anwendungsebene durchführen möchten, lesen Sie den Artikel zu [Application Gateway](../application-gateway/overview.md).
> * Um Lastenausgleich auf Netzwerkebene auszuführen, lesen Sie den Artikel zu [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Für Ihre End-to-End-Szenarien kann es vorteilhaft sein, diese Lösungen nach Bedarf zu kombinieren.
> Einen Vergleich der Azure-Lastenausgleichsoptionen finden Sie unter [Übersicht über Lastenausgleichsoptionen in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="why-use-azure-front-door"></a>Gründe für die Verwendung von Azure-Front-Door

Mit Azure Front Door Service können Sie Ihre dynamischen Webanwendungen und statischen Inhalte erstellen, ausführen und aufskalieren. Front Door ermöglicht es Ihnen, das globale Routing für Ihren Webdatenverkehr zu definieren, zu verwalten und zu überwachen, indem durch schnelles globales Failover die Leistung und Zuverlässigkeit für Endbenutzer auf höchstem Niveau optimiert wird.

Wichtige Features von Front-Door:

* Beschleunigte Anwendungsleistung durch Verwenden des auf **[Split TCP](front-door-routing-architecture.md#splittcp)** basierenden **[Anycast-Protokolls](front-door-routing-architecture.md#anycast)** .

* Intelligente **[Integritätstestüberwachung](front-door-health-probes.md)** von Back-End-Ressourcen.

*  Routing auf **[URL-Pfadbasis](front-door-route-matching.md)** für Anforderungen.

* Ermöglicht das Hosten mehrerer Websites für eine effiziente Anwendungsinfrastruktur. 

* Cookiebasierte **[Sitzungsaffinität](front-door-routing-methods.md#affinity)** .

* **[SSL-Offloading](front-door-custom-domain-https.md)** und Zertifikatverwaltung.

* Definieren Ihrer eigenen **[benutzerdefinierten Domäne](front-door-custom-domain.md)** . 

* Anwendungssicherheit mit integrierter **[Web Application Firewall (WAF)](../web-application-firewall/overview.md)** .

* Umleiten von HTTP-Datenverkehr an HTTPS mit **[URL-Redirect](front-door-url-redirect.md)** .

* Benutzerdefinierter Weiterleitungspfad mit **[URL-Rewrite](front-door-url-rewrite.md)** .

* Native Unterstützung von End-to-End-IPv6-Konnektivität und **[HTTP/2-Protokoll](front-door-http2.md)** .

## <a name="pricing"></a>Preise

Preisinformationen finden Sie unter [Front Door – Preise](https://azure.microsoft.com/pricing/details/frontdoor/). Weitere Informationen finden Sie unter [SLA für Azure Front Door](https://azure.microsoft.com/support/legal/sla/frontdoor/v1_0/).

## <a name="whats-new"></a>Neuigkeiten

Abonnieren Sie den RSS-Feed, und zeigen Sie die neuesten Azure Front Door-Featureupdates auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) an.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart zum Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- [Lern-Modul: Einführung in Azure Front Door](/learn/modules/intro-to-azure-front-door/).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
