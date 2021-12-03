---
title: Problembehandlung für Azure Virtual Network NAT-Konnektivität
titleSuffix: Azure Virtual Network
description: Es wird beschrieben, wie Sie Virtual Network NAT-Probleme beheben.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 610d24576386b311bf5e4ef5a98c1c22a705aff0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466407"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Problembehandlung für Azure Virtual Network NAT-Konnektivität

In diesem Artikel erhalten Administratoren Hilfe beim Diagnostizieren und Beheben von Konnektivitätsproblemen mit Virtual Network NAT.

## <a name="problems"></a>Probleme

* [SNAT-Auslastung](#snat-exhaustion)
* [Fehler bei ICMP-Ping](#icmp-ping-is-failing)
* [Konnektivitätsfehler](#connectivity-failures)
* [IPv6-Koexistenz](#ipv6-coexistence)
* [Verbindung stammt nicht von den IP-Adressen des NAT Gateways](#connection-doesnt-originate-from-nat-gateway-ips)

Führen Sie die Schritte im folgenden Abschnitt aus, um diese Probleme zu beheben.

## <a name="resolution"></a>Lösung

### <a name="snat-exhaustion"></a>SNAT-Auslastung

Für eine einzelne [NAT-Gatewayressource](nat-gateway-resource.md) werden jeweils 64.000 bis maximal 1 Million gleichzeitige Datenflüsse unterstützt.  Jede IP-Adresse stellt 64.000 SNAT-Ports für den verfügbaren Bestand bereit. Sie können bis zu 16 IP-Adressen pro NAT-Gatewayressource nutzen.  Eine ausführlichere Beschreibung des SNAT-Mechanismus finden Sie [hier](nat-gateway-resource.md#source-network-address-translation).

Die Grundursache für eine SNAT-Auslastung ist häufig ein Antimuster bei der Einrichtung und Verwaltung der ausgehenden Konnektivität oder bei der Änderung des Standardwerts von konfigurierbaren Zeitgebern.  Lesen Sie diesem Abschnitt sorgfältig.

#### <a name="steps"></a>Schritte

1. Überprüfen Sie, ob Sie das standardmäßige Leerlauftimeout auf einen Wert von mehr als vier Minuten festgelegt haben.
2. Ermitteln Sie, wie von Ihrer Anwendung ausgehende Konnektivität erstellt wird (z. B. Code Review oder Paketerfassung). 
3. Ermitteln Sie, ob es sich bei dieser Aktivität um ein erwartetes Verhalten handelt oder die Anwendung ein Fehlverhalten aufweist.  Verwenden Sie [Metriken](nat-metrics.md) in Azure Monitor, um Ihre Erkenntnisse zu untermauern. Verwenden Sie die Kategorie „Fehler“ für die Metrik „SNAT-Verbindungen“.
4. Prüfen Sie, ob die richtigen Muster eingehalten werden.
5. Prüfen Sie, ob die hohe SNAT-Portauslastung verringert werden sollte, indem der NAT-Gatewayressource weitere IP-Adressen zugewiesen werden.

#### <a name="design-patterns"></a>Entwurfsmuster

Nutzen Sie nach Möglichkeit immer die Wiederverwendung von Verbindungen und Verbindungspools.  Bei diesen Mustern werden Probleme aufgrund einer hohen Ressourcenauslastung vermieden, und es ergibt sich ein vorhersagbares Verhalten. Primitive für diese Muster finden Sie in vielen Entwicklungsbibliotheken und Frameworks.

_**Lösung:**_ Nutzen geeigneter Muster und bewährter Methoden

- Das TCP-Leerlauftimeout von NAT-Gatewayressourcen ist standardmäßig auf vier Minuten festgelegt.  Wird diese Einstellung auf einen höheren Wert festgelegt, werden Flows von der NAT länger aufrechterhalten, was eine [unnötige Belastung des SNAT-Portbestands](nat-gateway-resource.md#timers) zur Folge haben kann.
- Atomische Anforderungen (eine Anforderung pro Verbindung) sind ein schlechter Entwurfsansatz. Antimuster dieser Art führen zu einer Begrenzung der Skalierung und zu einer Verringerung der Leistung und Zuverlässigkeit. Nutzen Sie stattdessen die Wiederverwendung von HTTP/S-Verbindungen, um die Anzahl von Verbindungen und zugeordneten SNAT-Ports zu reduzieren. Die Anwendungsskalierung und die Leistung werden verbessert, weil Handshakes, Mehraufwand und Kosten für kryptografische Vorgänge reduziert werden, wenn TLS verwendet wird.
- Per DNS können viele einzelne Datenflüsse mit großen Datenmengen genutzt werden, wenn der Client das Ergebnis der DNS-Auflösung nicht zwischenspeichert. Verwenden Sie die Zwischenspeicherung.
- Bei UDP-Datenflüssen (z. B. DNS-Suchen) werden für die Dauer des Leerlauftimeouts SNAT-Ports zugeordnet. Je länger der Leerlauftimeout dauert, desto höher ist der Druck auf die SNAT-Ports. Verwenden Sie ein kurzes Leerlauftimeout (z. B. vier Minuten).
- Nutzen Sie Verbindungspools, um Ihre Verbindungsdatenmenge zu steuern.
- Vermeiden Sie den Abbruch eines TCP-Datenflusses im Hintergrund, und verlassen Sie sich nicht darauf, dass dies über TCP-Timer bereinigt wird. Wenn Sie die explizite Verbindungstrennung durch TCP nicht zulassen, bleibt der Zuordnungszustand bei Zwischensystemen und -endpunkten erhalten, und SNAT-Ports stehen nicht für andere Verbindungen zur Verfügung. Dieses Muster kann zu einer Auslösung von Anwendungsausfällen und zu einer SNAT-Überlastung führen. 
- Ändern Sie Timerwerte für die TCP-Verbindungstrennung auf der Betriebssystemebene nur, wenn Sie mit den Auswirkungen vertraut sind, die sich dadurch ergeben. Der TCP-Stapel wird zwar wiederhergestellt, Ihre Anwendungsleistung kann aber beeinträchtigt werden, wenn für die Endpunkte einer Verbindung unterschiedliche Erwartungen bestehen. Der Wunsch nach einer Änderung von Timern ist normalerweise ein Zeichen für ein zugrunde liegendes Entwurfsproblem. Sehen Sie sich die folgenden Empfehlungen an.

Die SNAT-Überlastung kann auch durch andere Antimuster in der zugrunde liegenden Anwendung verstärkt werden. Informieren Sie sich über diese zusätzlichen Muster und bewährten Methoden, um die Skalierbarkeit und Zuverlässigkeit Ihres Diensts zu verbessern.

- Untersuchen Sie die Auswirkungen einer Verringerung des [TCP-Leerlauftimeouts](nat-gateway-resource.md#timers) (einschließlich des standardmäßigen Leerlauftimeouts von vier Minuten), um SNAT-Ports früher freizugeben.
- Erwägen Sie die Nutzung von [asynchronen Abrufmustern](/azure/architecture/patterns/async-request-reply) für zeitintensive Vorgänge, um Verbindungsressourcen für andere Vorgänge freizuhalten.
- Für langlebige Datenflüsse (z. B. wiederverwendete TCP-Verbindungen) sollten TCP-Keepalives oder Anwendungsschicht-Keepalives verwendet werden, um zu verhindern, dass es für zwischengeschaltete Systeme zu Zeitüberschreitungen kommt. Die Verlängerung des Leerlauftimeouts ist das letzte Mittel, und unter Umständen wird die Grundursache hierdurch nicht beseitigt. Eine lange Timeoutdauer kann zu einer geringen Ausfallrate führen, wenn der Timeoutzeitraum abgelaufen ist, und mit Verzögerungen und unnötigen Fehlern verbunden sein.
- Es sollten ordnungsgemäße [Wiederholungsmuster](/azure/architecture/patterns/retry) verwendet werden, um bei vorübergehenden Fehlern oder der Wiederherstellung nach Fehlern aggressive Wiederholungsversuche oder Bursts zu vermeiden.
Die Erstellung einer neuen TCP-Verbindung für jeden HTTP-Vorgang (auch als „atomische Verbindungen“ bezeichnet) ist ein Antimuster.  Mit atomischen Verbindungen wird verhindert, dass für Ihre Anwendung eine gute Skalierung erzielt wird, und es werden Ressourcen verschwendet.  Fassen Sie immer mehrere Vorgänge per Pipeline in derselben Verbindung zusammen.  Ihre Anwendung profitiert von der Transaktionsgeschwindigkeit und den Ressourcenkosten.  Wenn Ihre Anwendung die Verschlüsselung auf der Transportschicht nutzt (z. B. TLS), ist die Verarbeitung neuer Verbindungen mit erheblichen Kosten verbunden.  Informationen zu weiteren bewährten Mustern finden Sie unter [Cloudentwurfsmuster](/azure/architecture/patterns/).

#### <a name="additional-possible-mitigations"></a>Weitere mögliche Minderungsmaßnahmen

_**Lösung:**_ Skalieren Sie die ausgehende Konnektivität wie folgt:

| Szenario | Beweise |Minderung |
|---|---|---|
| In Zeiten mit hoher Auslastung kommt es bei SNAT-Ports zu Konflikten und zu einer Überlastung. | In der Kategorie „Fehler“ für die [Metrik](nat-metrics.md) „SNAT-Verbindungen“ in Azure Monitor werden vorübergehende oder dauerhafte Fehler im Zeitverlauf sowie hohes Verbindungsaufkommen angezeigt.  | Ermitteln Sie, ob Sie zusätzliche öffentliche Ressourcen bzw. Präfixressourcen für öffentliche IP-Adressen hinzufügen können. Durch diese Hinzufügung können für Ihr NAT-Gateway maximal 16 IP-Adressen genutzt werden. Aufgrund dieser Maßnahme ist ein größerer Bestand für die verfügbaren SNAT-Ports vorhanden (64.000 pro IP-Adresse), und Sie können Ihr Szenario weiter skalieren.|
| Sie haben bereits 16 IP-Adressen bereitgestellt, und es kommt trotzdem weiterhin zu einer Überlastung bei den SNAT-Ports. | Beim Hinzuzufügen weiterer IP-Adressen tritt ein Fehler auf. Die Gesamtanzahl der IP-Adressen von öffentlichen IP-Adressressourcen oder öffentlichen IP-Präfixressourcen ist insgesamt höher als 16. | Verteilen Sie Ihre Anwendungsumgebung auf mehrere Subnetze, und stellen Sie für jedes Subnetz eine NAT-Gatewayressource bereit.  Optimieren Sie Ihre Entwurfsmuster anhand der obigen [Informationen](#design-patterns). |

>[!NOTE]
>Es ist wichtig, dass Sie verstehen, warum es zur SNAT-Überlastung kommt. Stellen Sie sicher, dass Sie die richtigen Muster verwenden, um skalierbare und zuverlässige Szenarien zu erhalten.  Das Hinzufügen von weiteren SNAT-Ports, ohne dass Ihnen die Ursache des Bedarfs bekannt ist, sollte nur im Notfall erfolgen. Wenn Sie nicht verstehen, warum es bei Ihnen zu einer hohen Auslastung beim SNAT-Portbestand kommt, führt die Erweiterung auf eine größere Zahl von SNAT-Ports durch das Hinzufügen von mehr IP-Adressen nur dazu, dass sich dieser Überlastungsfehler bei der weiteren Skalierung Ihrer Anwendung fortsetzt.  Es kann sein, dass hierdurch andere Ineffizienzen und Antimuster verdeckt werden.

### <a name="icmp-ping-is-failing"></a>Fehler bei ICMP-Ping

[Virtual Network NAT](nat-overview.md) unterstützt IPv4-UDP- und TCP-Protokolle. ICMP wird nicht unterstützt, sodass hierfür das Auftreten von Fehlern zu erwarten ist.  

_**Lösung:**_ Verwenden Sie stattdessen TCP-Verbindungstests (z. B. „TCP-Ping“) und UDP-spezifische Anwendungsschichttests, um die End-to-End-Konnektivität zu überprüfen.

Die folgende Tabelle kann als Ausgangspunkt dafür dienen, welche Tools für den Einstieg in die Tests verwendet werden können.

| Betriebssystem | Allgemeiner TCP-Verbindungstest | TCP-Anwendungsschichttest | UDP |
|---|---|---|---|
| Linux | nc (allgemeiner Verbindungstest) | curl (TCP-Anwendungsschichttest) | Anwendungsspezifisch |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | Anwendungsspezifisch |

### <a name="connectivity-failures"></a>Konnektivitätsfehler

Konnektivitätsprobleme mit [Virtual Network NAT](nat-overview.md) können unterschiedliche Ursachen haben:

* Permanente Fehler aufgrund von Konfigurationsfehlern
* Vorübergehende oder dauerhafte [SNAT-Auslastung](#snat-exhaustion) des NAT-Gateways
* Vorübergehende Fehler in der Azure-Infrastruktur 
* Vorübergehende Fehler im Pfad zwischen Azure und dem öffentlichen Internetziel 
* Vorübergehende oder dauerhafte Fehler am öffentlichen Internetziel

Verwenden Sie Tools wie die folgenden, um die Konnektivität zu überprüfen. [ICMP-Ping wird nicht unterstützt](#icmp-ping-is-failing).

| Betriebssystem | Allgemeiner TCP-Verbindungstest | TCP-Anwendungsschichttest | UDP |
|---|---|---|---|
| Linux | nc (allgemeiner Verbindungstest) | curl (TCP-Anwendungsschichttest) | Anwendungsspezifisch |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | Anwendungsspezifisch |

#### <a name="configuration"></a>Konfiguration

Überprüfen Sie Ihre Konfiguration:
1. Verfügt die NAT-Gatewayressource über mindestens eine öffentliche IP-Ressource oder eine Präfixressource für öffentliche IP-Adressen? Dem NAT-Gateway muss mindestens eine IP-Adresse zugeordnet sein, damit es ausgehende Verbindungen bereitstellen kann.
2. Ist das Subnetz des virtuellen Netzwerks zur Verwendung des NAT-Gateways konfiguriert?
3. Verwenden Sie die benutzerdefinierte Route (User-Defined Route, UDR), und setzen Sie das Ziel außer Kraft?  NAT-Gatewayressourcen werden zur Standardroute (0/0) in konfigurierten Subnetzen.

#### <a name="snat-exhaustion"></a>SNAT-Auslastung

Lesen Sie den Abschnitt [SNAT-Auslastung](#snat-exhaustion) in diesem Artikel.

#### <a name="azure-infrastructure"></a>Azure-Infrastruktur

Azure überwacht und betreibt seine Infrastruktur mit großer Sorgfalt. Vorübergehende Fehler können auftreten, und es gibt keine Garantie, dass die Übertragungen immer verlustfrei sind.  Verwenden Sie Entwurfsmuster, die SYN-Neuübertragungen für TCP-Anwendungen ermöglichen. Verwenden Sie ausreichen große Verbindungstimeouts, um TCP-SYN-Neuübertragungen zuzulassen und die vorübergehenden Auswirkungen von SYN-Paketverlusten zu verringern.

_**Lösung:**_

* Überprüfen Sie, ob eine [SNAT-Auslastung](#snat-exhaustion) vorliegt.
* Der Konfigurationsparameter in einem TCP-Stapel, der das Verhalten von SYN-Neuübertragungen steuert, wird [RTO](https://tools.ietf.org/html/rfc793) (Retransmission Time-Out, Timeout für Neuübertragungen) genannt. Der RTO-Wert ist anpassbar, aber in der Regel standardmäßig auf mindestens eine Sekunde mit exponentiellem Backoff festgelegt.  Sollte das Verbindungstimeout Ihrer Anwendung zu kurz sein (beispielsweise eine Sekunde), treten unter Umständen sporadische Verbindungstimeouts auf.  Erhöhen Sie das Verbindungstimeout der Anwendung.
* Erstellen Sie im Falle von längeren unerwarteten Timeouts mit Standardanwendungsverhalten eine Supportanfrage für die weitere Problembehandlung.

Wir raten davon ab, das TCP-Verbindungstimeout künstlich zu verkürzen oder den RTO-Parameter zu optimieren.

#### <a name="public-internet-transit"></a>Übertragung über das öffentliche Internet

Die Wahrscheinlichkeit vorübergehender Fehler ist direkt proportional zur Länge des Zielpfads und zur Anzahl von Zwischensystemen. Es ist mit einer Zunahme vorübergehender Fehler über die [Azure-Infrastruktur](#azure-infrastructure) zu rechnen. 

Befolgen Sie die gleichen Anweisungen wie im vorherigen [Abschnitt zur Azure-Infrastruktur](#azure-infrastructure).

#### <a name="internet-endpoint"></a>Internetendpunkt

Es gelten die Informationen in den vorherigen Abschnitten und für den Internetendpunkt, mit dem die Kommunikationsverbindung hergestellt wird. Weitere Faktoren, die sich auf den Erfolg der Konnektivität auswirken können:

* Zielseitige Datenverkehrsverwaltung, einschließlich Folgendem:
- Zielseitige API-Ratenbegrenzung
- Gegenmaßnahmen für volumetrische DDoS-Angriffe oder Traffic-Shaping auf der Transportebene
* Firewall oder andere Komponenten am Ziel 

In der Regel sind Paketerfassungen an der Quelle und am Ziel (sofern verfügbar) erforderlich, um Abläufe zu bestimmen.

_**Lösung:**_

* Überprüfen Sie, ob eine [SNAT-Auslastung](#snat-exhaustion) vorliegt. 
* Überprüfen Sie zum Vergleich die Konnektivität mit einem Endpunkt in der gleichen Region oder an einem anderen Ort.  
* Untersuchen Sie im Falle von Tests für hohes Volumen oder hohe Transaktionsraten, ob weniger Fehler auftreten, wenn Sie die Rate verringern.
* Wenn sich die Ratenänderung auf die Fehlerrate auswirkt, überprüfen Sie, ob API-Ratenbegrenzungen oder andere zielseitige Einschränkungen erreicht wurden.
* Sollte das Ergebnis Ihrer Untersuchung nicht eindeutig sein, erstellen Sie eine Supportanfrage für die weitere Problembehandlung.

#### <a name="tcp-resets-received"></a>TCP-Zurücksetzungen empfangen

Vom NAT-Gateway werden TCP-Zurücksetzungen auf dem virtuellen Quellcomputer für Datenverkehr generiert, für den nicht der Status „In Bearbeitung“ erkannt wird.

Dies kann unter anderen darauf zurückzuführen sein, dass für die TCP-Verbindung ein Leerlauftimeout aufgetreten ist.  Das Leerlauftimeout kann auf einen Wert zwischen vier und 120 Minuten festgelegt werden.

TCP-Zurücksetzungen werden nicht auf der öffentlichen Seite von NAT-Gatewayressourcen generiert. TCP-Zurücksetzungen auf der Zielseite werden vom virtuellen Quellcomputer generiert, und nicht von der NAT-Gatewayressource.

_**Lösung:**_

* Machen Sie sich mit den Empfehlungen für [Entwurfsmuster](#design-patterns) vertraut.  
* Erstellen Sie ggf. eine Supportanfrage für die weitere Problembehandlung.

### <a name="ipv6-coexistence"></a>IPv6-Koexistenz

[Virtual Network NAT](nat-overview.md) unterstützt IPv4-UDP- und TCP-Protokolle. NAT kann nicht einer öffentlichen IPv6-Adresse oder einem Präfix für öffentliche IPv6-Adressen zugeordnet werden. NAT kann jedoch in einem Dual Stack-Subnetz bereitgestellt werden.

_**Lösung:**_ Stellen Sie ein NAT-Gateway in einem Dual Stack-Subnetz bereit.

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>Verbindung stammt nicht von den IP-Adressen des NAT Gateways

Sie konfigurieren das NAT Gateway, die zu verwendende(n) IP-Adresse(n) und welches Subnetz eine NAT Gateway-Ressource verwenden soll. Bei Verbindungen von Instanzen virtueller Computer, die bereits vor der Bereitstellung des NAT Gateways bestanden, werden die IP-Adressen jedoch nicht verwendet.  Sie scheinen IP-Adressen zu verwenden, die nicht mit der NAT Gateway-Ressource verwendet werden.

_**Lösung:**_

[Virtual Network NAT](nat-overview.md) ersetzt die ausgehende Konnektivität für das Subnetz, in dem es konfiguriert ist. Beim Übergang von Standard-SNAT oder SNAT in ausgehender Richtung für den Lastenausgleich zur Verwendung von NAT Gateways beginnen neue Verbindungen sofort mit der Verwendung der IP-Adressen, die der NAT Gateway-Ressource zugeordnet sind.  Wenn jedoch ein virtueller Computer während des Wechsels zur NAT Gateway-Ressource immer noch eine bestehende Verbindung aufweist, verwendet die Verbindung weiterhin die alte SNAT-IP-Adresse, die beim Herstellen der Verbindung zugewiesen wurde.  Stellen Sie sicher, dass Sie wirklich eine neue Verbindung herstellen, anstatt eine bereits bestehende Verbindung wiederzuverwenden, da das Betriebssystem oder der Browser die Verbindungen in einem Verbindungspool zwischengespeichert hat.  Wenn Sie z. B. _curl_ in PowerShell verwenden, stellen Sie sicher, dass Sie den Parameter _-DisableKeepalive_ angeben, um eine neue Verbindung zu erzwingen.  Wenn Sie einen Browser verwenden, können die Verbindungen auch gepoolt werden.

Es ist nicht erforderlich, einen virtuellen Computer neu zu starten, der ein Subnetz für eine NAT Gateway-Ressource konfiguriert.  Wenn allerdings ein virtueller Computer neu gestartet wird, wird der Verbindungsstatus geleert.  Wenn der Verbindungsstatus geleert wurde, beginnen alle Verbindungen mit der Verwendung der IP-Adresse(n) der NAT Gateway-Ressource.  Dies ist jedoch ein Nebeneffekt des Neustarts des virtuellen Computers und kein Hinweis darauf, dass ein Neustart erforderlich ist.

Wenn Sie weiterhin Probleme haben, öffnen Sie eine Supportanfrage zur weiteren Problembehandlung.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [Virtual Network NAT](nat-overview.md).
* Informieren Sie sich über die [NAT-Gatewayressource](nat-gateway-resource.md).
* Informieren Sie sich über [Metriken und Warnungen für NAT-Gatewayressourcen](nat-metrics.md).
