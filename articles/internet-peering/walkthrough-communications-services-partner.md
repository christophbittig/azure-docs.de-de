---
title: Exemplarische Vorgehensweise zum Azure-Internetpeering für Communication Services
titleSuffix: Azure
description: Exemplarische Vorgehensweise zum Azure-Internetpeering für Communication Services
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: d17ae08067568098fffcdd443ba196c0023fbd42
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519343"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Exemplarische Vorgehensweise zum Azure-Internetpeering für Communication Services

In diesem Abschnitt werden die Schritte erläutert, die ein Communication Services-Anbieter ausführen muss, um eine direkte Verbindung mit Microsoft herzustellen.

**Communication Services-Anbieter:** Communication Services-Anbieter sind Organisationen, die Communication Services anbieten (Kommunikation, Messaging, Konferenzen usw.) und ihre Communication Services-Infrastruktur (SBC/SIP-Gateway usw.) in Azure Communication Services und Microsoft Teams integrieren möchten. 

Das Azure-Internetpeering unterstützt Communication Services-Anbieter beim Herstellen einer direkten Verbindung mit Microsoft an allen Edge-Standorten (POP-Standorten). Die Liste aller öffentlichen Edge-Standorte ist in [PeeringDB](https://www.peeringdb.com/net/694) verfügbar.

Das Azure-Internetpeering bietet hochgradig zuverlässige Verbindungen mit QoS-Unterstützung (Quality of Service) für Communication Services, um hochwertige und leistungsorientierte Dienste sicherzustellen.

## <a name="technical-requirements"></a>Technische Anforderungen
Die technischen Voraussetzungen für die Einrichtung einer direkten Verbindung für Communication Services lauten wie folgt:
-   Der Peer MUSS eine eigene autonome Systemnummer (Autonomous System Number, ASN) bereitstellen, die öffentlich sein MUSS.
-   Der Peer MUSS an jedem Verbindungsstandort über eine redundante Verbindung (PNI) verfügen, um lokale Redundanz sicherzustellen.
-   Der Peer MUSS über Georedundanz verfügen, um bei einem Standortausfall in Region/Stadt ein Failover sicherzustellen.
-   Der Peer MUSS BGP-Sitzungen im Aktiv/Aktiv-Modus durchführen, um Hochverfügbarkeit und schnellere Konvergenz sicherzustellen, und sollte nicht als „primär“ und „Sicherung“ bereitgestellt werden.
-   Der Peer MUSS ein 1:1-Verhältnis zwischen Peer-Peeringroutern und Peeringverbindungen aufrechterhalten, und es wird keine Ratenbegrenzung angewendet.
-   Der Peer MUSS seinen eigenen öffentlichen IPv4-Adressraum mit Routingunterstützung, der von den Communication Services-Endpunkten des Peers (z. B. SBC) verwendet wird, angeben und ankündigen. 
-   Der Peer MUSS detailliert angeben, welche Klasse von Datenverkehr und welche Endpunkte in jedem angekündigten Subnetz untergebracht werden. 
-   Der Peer MUSS BGP über bidirektionale Weiterleitungserkennung (Bi-directional Forwarding Detection, BFD) ausführen, um die Routenkonvergenz im Bruchteil einer Sekunde zu vereinfachen.
-   Alle Präfixe der Kommunikationsinfrastruktur werden im Azure-Portal registriert und mit der Communityzeichenfolge 8075:8007 angekündigt.
-   Der Peer DARF NICHT das Peering auf einem Gerät beenden, auf dem eine zustandsbehaftete Firewall ausgeführt wird. 
-   Microsoft konfiguriert alle Verbindungslinks standardmäßig als LAG (Linkbundles), daher MUSS der Peer LACP (Link Aggregation Control Protocol) für die Verbindungslinks unterstützen.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Einrichten einer direkten Verbindung mit Microsoft für Communication Services.

Führen Sie die folgenden Schritte aus, um eine direkte Verbindung mithilfe des Azure-Internetpeerings herzustellen:

**1.    Ordnen Sie dem Azure-Abonnement die öffentliche ASN des Peers zu:**

Wenn der Peer dem Azure-Abonnement bereits die öffentliche ASN zugeordnet hat, ignorieren Sie diesen Schritt.

[Zuordnen der Peer-ASN zum Azure-Abonnement über das Portal: Azure | Microsoft-Dokumentation](./howto-subscription-association-portal.md)

Der nächste Schritt ist das Erstellen einer Direct Peering-Verbindung für Peering Service.

> [!NOTE]
> Sobald die ASN-Zuordnung genehmigt ist, senden Sie uns über peeringservice@microsoft.com eine E-Mail mit ihrer ASN und Abonnement-ID, um Ihr Abonnement Communication Services zuzuordnen. 

**2.    Erstellen einer Direct Peering-Verbindung für Peering Service:**

Befolgen Sie die Anweisungen zum [Erstellen oder Ändern einer Instanz für Direct Peering über das Portal](./howto-direct-portal.md).

Achten Sie darauf, dass sie die Hochverfügbarkeitsanforderung erfüllt.

Wählen Sie auf der Seite „Peering erstellen" unbedingt die folgenden Optionen aus:

Peeringtyp:   **Direkt**

Microsoft-Netzwerk: **8075 (mit Voice)**

SKU:        **Premium kostenlos**


Wählen Sie unter „Verbindungsseite für Direct Peering“ die folgenden Optionen aus:

Sitzungsadressanbieter:   **Microsoft**

Für Peering Service verwenden:   **Aktiviert**

> [!NOTE] 
> Ignorieren Sie die folgende Meldung, während Sie die Auswahl zum Aktivieren für Peering Service treffen.
> *Führen Sie keine Aktivierung durch, solange Sie nicht peering@microsoft.com in der Angelegenheit kontaktiert haben, ein MAPS-Anbieter zu werden.*


  **2a. Verwenden einer vorhandenen Direct Peering-Verbindung für Peering Service**

Wenn Sie über eine Direct Peering-Verbindung verfügen, die Sie zur Unterstützung von Peering Service verwenden möchten, können Sie sie im Azure-Portal aktivieren.
1.  Befolgen Sie die Anweisungen in [Konvertieren einer älteren Instanz für Direct Peering in eine Azure-Ressource über das Portal](./howto-legacy-direct-portal.md).
Falls erforderlich, bestellen Sie zusätzliche Leitungen, um den Anforderungen an Hochverfügbarkeit zu genügen.

2.  Befolgen Sie die Schritte zum [Aktivieren von Azure Peering Service für Direct Peering über das Azure-Portal](./howto-peering-service-portal.md).




**3.    Registrieren Sie Ihre Präfixe für optimiertes Routing.**

Um ein optimiertes Routing für die Präfixe Ihrer Communication Services-Infrastruktur zu gewährleisten, sollten Sie alle Präfixe bei Ihren Peeringverbindungen registrieren.
[Registrieren von Peering Service über das Azure-Portal | Microsoft-Dokumentation](../peering-service/azure-portal.md)

Der Schlüssel „Präfix“ wird für Communication Services-Partner automatisch ausgefüllt, sodass der Partner für die Registrierung keinen Präfixschlüssel verwenden muss. 

Stellen Sie sicher, dass die zu registrierenden Präfixe über die für die Region eingerichteten direkten Verbindungen angekündigt werden.


## <a name="faqs"></a>Häufig gestellte Fragen:

**F.**  Ich besitze für meine Communication Services-Instanzen kleinere Subnetze (</24). Sind die kleineren Subnetze auch für das Routing geeignet?

**A.**  Ja, Microsoft Azure-Peering unterstützt auch das Routing mit kleineren Präfixen. Stellen Sie sicher, dass Sie die kleineren Präfixe für das Routing registrieren und sie über die Verbindungen angekündigt werden.

**F.**  Welche Microsoft-Routen empfangen wir über diese Verbindungen?

**A.** Microsoft kündigt alle öffentlichen Dienstpräfixe von Microsoft über diese Verbindungen an. Dadurch wird sichergestellt, dass über dieselbe Verbindung nicht nur auf Communication Services, sondern auch andere Clouddienste zugegriffen werden kann.

**F.**  Ich muss das Präfixlimit festlegen. Wie viele Routen würde Microsoft ankündigen?

**A.** Microsoft kündigt ungefähr 280 Präfixe im Internet an, und dies kann sich in Zukunft um 10-15 % erhöhen. Daher kann ein sicheres Limit von 400-500 als „Maximale Präfixanzahl“ festgelegt werden.

**F.** Wird Microsoft die Peerpräfixe erneut im Internet ankündigen?

**A.** Nein.

**F.** Wird für diesen Dienst eine Gebühr berechnet?

**A.** Nein, es wird jedoch erwartet, dass der Peer die Kosten für standortübergreifende Verbindungen übernimmt.

**F.** Was ist die minimale Übertragungsrate einer Verbindung?

**A.** 10 GBit/s.

**F.** Ist der Peer an eine SLA gebunden?

**A.** Ja, sobald die Auslastung 40 % erreicht, muss ein 45-60-tägiger LAG-Zuwachsprozess beginnen.

**F.** Was ist der Vorteil dieses Diensts gegenüber aktuellem Direct Peering oder ExpressRoute?

**A.** Der gesamte Pfad ist uneingeschränkt für Sprachdatenverkehr über Microsoft WAN optimiert, und die Konvergenz ist für den Bruchteil einer Sekunde mit BFD abgestimmt.

**F.** Wie lange dauert es, den Onboardingprozess abzuschließen?

**A.** Die Dauer variiert abhängig von der Anzahl und der Lage der Standorte sowie davon, ob der Peer vorhandene private Peerings migriert oder neue Verkabelung herstellt. Der Netzbetreiber sollte über 3 Wochen einplanen.

**F.** Können APIs für das Onboarding verwendet werden?

**A.** Zurzeit gibt es keine API-Unterstützung, und die Konfiguration muss über das Webportal erfolgen.
