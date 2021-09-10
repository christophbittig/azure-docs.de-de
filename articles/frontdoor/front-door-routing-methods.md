---
title: Unterschiedliche Methoden des Datenverkehrsroutings in Azure Front Door | Microsoft-Dokumentation
description: In diesem Artikel werden die verschiedenen Methoden des Datenverkehrsroutings beschrieben, die von Azure Front Door Service verwendet werden.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/14/2021
ms.author: duau
ms.openlocfilehash: c692c814bf3ae71b34f1c31c8ab29a4cda968f1f
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203811"
---
# <a name="front-door-routing-methods"></a>Datenverkehrsrouting in Azure Front Door Service

Azure Front Door unterstützt verschiedene Methoden für das Routing von Datenverkehr, um zu ermitteln, wie HTTP-/HTTPS-Datenverkehr an verschiedene Dienstendpunkte weitergeleitet werden soll. Wenn Ihr Client eine Verbindung mit Front Door anfordert, wird die konfigurierte Datenverkehrsrouting-Methode angewendet, um sicherzustellen, dass die Anforderungen an die Back-End-Instanz weitergeleitet werden, die am ehesten geeignet ist. 

Vier Methoden für das Datenverkehrsrouting sind in Front Door verfügbar:

* **[Wartezeit](#latency):** Mit dem latenzbasierten Routing wird sichergestellt, dass Anforderungen an diejenigen Back-Ends gesendet werden, die innerhalb eines akzeptablen Empfindlichkeitsbereichs die niedrigste Latenz aufweisen. Benutzeranforderungen werden auf diese Weise unter Berücksichtigung der Netzwerklatenz an die nächstgelegenen Back-Ends gesendet.
* **[Priorität:](#priority)** Sie können Ihren Back-Ends Prioritäten zuweisen, wenn Sie ein primäres Back-End konfigurieren möchten, das den gesamten Datenverkehr verarbeitet. Beim sekundären Back-End kann es sich um eine Sicherung handeln, falls das primäre Back-End nicht verfügbar ist.
* **[Gewichtung](#weighted):** Sie können Ihren Back-Ends Gewichtungen zuweisen, wenn Sie Datenverkehr gleichmäßig oder gemäß Gewichtungskoeffizienten auf eine Gruppe von Back-Ends verteilen möchten. Der Datenverkehr wird gemäß den Gewichtungen verteilt, wenn die Latenzen der Back-Ends innerhalb des akzeptablen Latenzempfindlichkeitsbereichs im Back-End-Pool liegen.
* **[Sitzungsaffinität](#affinity):** Sie können die Sitzungsaffinität für Ihre Front-End-Hosts oder -Domänen konfigurieren, um sicherzustellen, dass Anforderungen desselben Endbenutzers an dasselbe Back-End gesendet werden.

Alle Azure Front Door Service-Konfigurationen schließen die Überwachung der Back-End-Integrität und ein automatisiertes, sofortiges und globales Failover ein. Weitere Informationen finden Sie unter [Überwachen von Azure Front Door Service-Back-Ends](front-door-health-probes.md). Front Door kann basierend auf einer einzelnen Routingmethode ausgeführt werden. Abhängig von den Anforderungen Ihrer Anwendung können Sie jedoch auch mehrere Routingmethoden kombinieren, um eine optimale Routingtopologie zu erstellen.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Datenverkehrsrouting auf Grundlage der niedrigsten Latenzen

Die Reaktionsfähigkeit Ihrer Anwendungen kann verbessert werden, indem Sie Back-Ends an mindestens zwei Standorten auf der Welt bereitstellen und Datenverkehr an das Ziel weiterleiten, das den Endbenutzern am „nächsten“ liegt. Mit der Standardmethode für Routing von Datenverkehr für Ihre Front Door-Konfiguration werden Anforderungen von Ihren Endbenutzern an das nächstgelegene Back-End der Front Door-Umgebung weitergeleitet, die die Anforderung empfangen hat. In Kombination mit der Anycast-Architektur von Azure Front Door wird durch diesen Ansatz gewährleistet, dass Ihre Endbenutzer je nach Standort von maximaler Leistung profitieren.

Das nächstgelegene Back-End ist nicht unbedingt dasjenige, das geografisch am nächsten liegt. Azure Front Door Service ermittelt das nächstgelegene Back-End durch die Messung der Netzwerklatenz. Weitere Informationen finden Sie unter [Routingarchitektur von Azure Front Door Service](front-door-routing-architecture.md). 

Im Folgenden wird der allgemeine Ablauf zur Ermittlung der Back-Ends dargestellt:

| Verfügbare Back-Ends | Priorität | Latenzsignal (basierend auf Integritätstests) | Weights |
|-------------| ----------- | ----------- | ----------- |
| Zunächst werden alle aktivierten Back-Ends ausgewählt, die bei einem Integritätstest den Statuscode „200 OK“ zurückgeben und daher als fehlerfrei beurteilt werden. Es sind z. B. sechs Back-Ends A, B, C, D, E und F vorhanden. C ist fehlerhaft und E deaktiviert. Die Liste der verfügbaren Back-Ends umfasst damit A, B, D und F.  | Als Nächstes werden von den verfügbaren Back-Ends diejenigen mit der höchsten Priorität ausgewählt. Back-End A, B und D ist Priorität 1 und Back-End F Priorität 2 zugeordnet. Als Back-Ends werden dann A, B und D ausgewählt.| Alle Back-Ends innerhalb des Latenzbereichs (niedrigste Latenz und Latenzempfindlichkeit werden in ms angegeben) werden ausgewählt. Wenn Backend A 15 ms, B 30 ms und D 60 ms von der Front-Door-Umgebung entfernt ist, in der die Anforderung eingegangen ist, und die Latenzempfindlichkeit 30 ms beträgt, dann besteht der Pool mit der geringsten Latenz aus Backend A und B, da D mehr als 30 ms vom nächstgelegenen Backend A entfernt ist. | Abschließend führt Azure Front Door Service für den Datenverkehr der zuletzt ausgewählten Back-Ends einen Roundrobin entsprechend der festgelegten Gewichtungen durch. Angenommen, Back-End A wird eine Gewichtung von 5 und Back-End B eine Gewichtung von 8 zugeordnet. Der Datenverkehr wird dann im Verhältnis 5:8 auf die Back-Ends A und B aufgeteilt. |

>[!NOTE]
> Standardmäßig ist die Eigenschaft der Latenzempfindlichkeit auf 0 ms festgelegt, d. h., die Anforderung wird immer an das schnellste verfügbare Back-End weitergeleitet, und Gewichtungen für die Back-Ends werden nur wirksam, wenn zwei Back-Ends dieselbe Netzwerklatenz aufweisen.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioritätsbasiertes Datenverkehrsrouting

Häufig möchte eine Organisation Hochverfügbarkeit für ihre Dienste gewährleisten, indem sie mehr als einen Sicherungsdienst für den Fall eines Ausfalls des primären Diensts bereitstellt. Diese Topologie wird branchenweit als Aktiv/Standby- oder Aktiv/Passiv-Topologie bezeichnet. Mithilfe der prioritätsbasierten Methode für das Datenverkehrsrouting können Azure-Kunden dieses Failovermuster problemlos implementieren.

Die Standardkonfiguration von Azure Front Door Service enthält eine Liste mit Back-Ends, denen dieselbe Priorität zugeordnet ist. Azure Front Door Service leitet dadurch Datenverkehr ausschließlich an die Back-Ends mit der höchsten Priorität (dem niedrigsten Prioritätswert) und damit an die primären Back-Ends weiter. Wenn die primären Back-Ends nicht verfügbar sind, wird der Datenverkehr an die sekundären Back-Ends (diejenigen, denen der zweitniedrigste Prioritätswert zugeordnet wurde) weitergeleitet. Wenn weder die primärem noch die sekundären Back-Ends verfügbar sind, wird der Datenverkehr an das dritte Back-End gesendet usw. Die Verfügbarkeit des Back-Ends basiert auf dem konfigurierten Status (aktiviert oder deaktiviert) und dem Back-End-Integritätsstatus, der regelmäßig durch Integritätstests ermittelt wird.

### <a name="configuring-priority-for-backends"></a>Konfigurieren der Priorität für Back-Ends

Jedes Back-End in Ihrem Back-End-Pool der Azure Front Door Service-Konfiguration verfügt über die Eigenschaft „Priority“ (Priorität), die eine Zahl zwischen 1 und 5 sein kann. Mit Azure Front Door konfigurieren Sie die Back-End-Priorität für jedes Back-End explizit mithilfe dieser Eigenschaft. mithilfe dieser Eigenschaft festlegen. Niedrigere Werte stellen eine höhere Priorität dar. Ein Prioritätswert kann für mehrere Back-Ends verwendet werden.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewichtete Methode für das Datenverkehrsrouting
Bei der gewichteten Methode für das Datenverkehrsrouting wird der Datenverkehr gleichmäßig verteilt oder eine vordefinierte Gewichtung verwendet.

Bei der Methode für Datenverkehrsrouting auf Grundlage von Gewichtungen weisen Sie in der Azure Front Door Service-Konfiguration Ihres Back-End-Pools jedem Back-End eine Gewichtung zu. Die Gewichtung ist eine Ganzzahl zwischen 1 und 1000. Als Standardgewichtung für den Parameter wird 50 verwendet.

Mit der Liste der verfügbaren Back-Ends, die eine akzeptable Latenzempfindlichkeit aufweisen, wird der Datenverkehr mit einem Roundrobin-Mechanismus unter Verwendung des angegebenen Gewichtungsverhältnisses verteilt. Wenn für die Latenzempfindlichkeit ein Wert von 0 Millisekunden festgelegt ist, wird diese Eigenschaft nur dann berücksichtigt, wenn die Netzwerklatenz für zwei Back-Ends gleich ist. 

Durch die gewichtete Methode werden einige nützliche Szenarios ermöglicht:

* **Allmähliches Anwendungsupgrade**: Legt für den Datenverkehr einen Prozentwert fest, damit nur ein Teil des Datenverkehrs an ein neues Back-End weitergeleitet wird, und erhöhen Sie den Datenverkehr dann im Lauf der Zeit allmählich, bis dessen Umfang dem anderer Back-Ends entspricht.
* **Anwendungsmigration zu Azure:** Erstellen Sie einen Back-End-Pool mit Azure-Endpunkten und externen Endpunkten. Passen Sie dabei die Gewichtung der Back-Ends so an, dass neue Back-Ends priorisiert werden. Sie können diese Änderungen schrittweise einführen, in dem Sie zuerst neue Back-Ends deaktivieren, ihnen anschließend die niedrigsten Gewichtungen zuweisen und die Werte dann langsam erhöhen, bis die Back-Ends den größten Teil des Datenverkehrs übernehmen. Abschließend deaktivieren Sie die weniger bevorzugten Back-Ends und entfernen diese aus dem Pool.  
* **Cloudbursting für zusätzliche Kapazität:** Stellen Sie in kurzer Zeit für eine lokale Bereitstellung zusätzliche Ressourcen in der Cloud zur Verfügung, indem Sie Azure Front Door Service verwenden. Wenn Sie zusätzliche Kapazität in der Cloud benötigen, können Sie weitere Back-Ends hinzufügen oder aktivieren und dabei angeben, welcher Teil des Datenverkehrs an das jeweilige Back-End gesendet wird.

## <a name="session-affinity"></a><a name = "affinity"></a>Sitzungsaffinität
Standardmäßig leitet Front Door ohne Sitzungsaffinität Anforderungen, die vom gleichen Client stammen, an verschiedene Back-Ends weiter. In einigen zustandsbehafteten Anwendungen oder in bestimmten anderen Szenarien wird jedoch bevorzugt, dass nachfolgende Anforderungen eines Benutzers immer an dasselbe Back-End gesendet werden, das die ursprüngliche Anforderung verarbeitet hat. Die cookiebasierte Sitzungsaffinität ist ein hilfreiches Feature, mit dem eine Benutzersitzung auf demselben Back-End fortgesetzt werden kann. Mit verwalteten Cookies kann Azure Front Door den nachfolgenden Datenverkehr von einer Benutzersitzung zur Verarbeitung an das gleiche Back-End weiterleiten.

Sitzungsaffinität kann auf einer Front-End-Hostebene für alle konfigurierten Domänen (oder untergeordneten Domänen) festgelegt werden. Nach der Aktivierung fügt Azure Front Door Service der Sitzung des Benutzers ein Cookie hinzu. Mithilfe der cookiebasierten Sitzungsaffinität kann Azure Front Door Service verschiedene Benutzer auch dann identifizieren, wenn für diese die gleiche IP-Adresse verwendet wird. Dadurch kann der Datenverkehr wiederum gleichmäßiger zwischen Ihren unterschiedlichen Back-Ends verteilt werden.

Die Lebensdauer des Cookies ist identisch mit derjenigen der Benutzersitzung, da Azure Front Door Service derzeit nur Sitzungscookies unterstützt. 

> [!NOTE]
> Öffentlichen Proxys können die Sitzungsaffinität beeinträchtigen. Grund dafür ist, dass Front Door zum Einrichten einer Sitzung ein Sitzungsaffinitätscookie zur Antwort hinzufügen muss. Dies ist jedoch nicht möglich, wenn die Antwort zwischengespeichert werden kann, da dies die Cookies anderer Clients, die die gleiche Ressource anfordern, beeinträchtigen würde. Um dies zu verhindern, wird die Sitzungsaffinität **nicht** hergestellt, wenn das Back-End bei einem Versuch eine zwischenspeicherbare Antwort sendet. Wenn die Sitzung bereits eingerichtet ist, spielt es keine Rolle, ob die Antwort aus dem Back-End zwischengespeichert werden kann.
> Die Sitzungsaffinität wird unter den folgenden Umständen hergestellt, **sofern** die Antwort einen HTTP 304-Statuscode enthält:
> - Die Antwort hat spezifische Werte für den ```Cache-Control```-Header, die ein Zwischenspeichern verhindern, wie z.B. „private“ oder „no-store“.
> - Die Antwort enthält einen noch nicht abgelaufenen ```Authorization```-Header.
> - Die Antwort enthält einen HTTP 302-Statuscode.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
