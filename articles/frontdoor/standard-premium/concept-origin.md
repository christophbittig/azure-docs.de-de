---
title: Ursprung und Ursprungsgruppe in Azure Front Door Standard/Premium
description: In diesem Artikel wird erläutert, worum es sich bei einem Ursprung und einer Ursprungsgruppe in einer Azure Front Door-Konfiguration handelt.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42c90d009aefb399e7635ec4a7cd09f884f3c51c
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113437646"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Ursprung und Ursprungsgruppe in Azure Front Door Standard/Premium (Vorschau)

> [!Note]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

In diesem Artikel werden die Konzepte der Webanwendungsbereitstellung mit Azure Front Door Standard/Premium beschrieben. Außerdem wird die Bedeutung der Begriffe *Ursprung* und *Ursprungsgruppe* in der Konfiguration von Azure Front Door Standard/Premium erläutert.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Origin

Ein Azure Front Door Standard/Premium-Ursprung ist der Hostname oder die öffentliche IP-Adresse der Anwendung, die Clientanforderungen verarbeitet. Von Azure Front Door Standard/Premium werden sowohl Azure-Ursprünge als Azure-fremde Ursprünge unterstützt – beispielsweise, wenn Ihre Anwendung in Ihrem lokalen Rechenzentrum oder bei einem anderen Cloudanbieter gehostet wird. Ein Ursprung sollte nicht mit einer Datenbankebene oder Speicherebene verwechselt werden. Ein Ursprung sollte als der Endpunkt für das Anwendungs-Back-End betrachtet werden. Wenn Sie einer Azure Front Door Standard/Premium-Ursprungsgruppe einen Ursprung hinzufügen, müssen Sie auch die folgenden Informationen hinzufügen:

* **Ursprungstyp**: Der Typ der Ressource, die hinzugefügt werden soll. Front Door unterstützt die automatische Erkennung der Anwendungs-Back-Ends aus dem App-Dienst, Clouddienst oder Speicher. Wenn Sie eine andere Ressource in Azure oder sogar ein Nicht-Azure-Back-End hinzufügen möchten, wählen Sie **Benutzerdefinierter Host** aus.

    >[!IMPORTANT]
    >Bei der Konfiguration überprüfen die APIs nicht, ob aus der Front Door-Umgebung nicht auf den Ursprung zugegriffen werden kann. Stellen Sie daher sicher, dass Front Door Ihren Ursprung erreichen kann.

* **Subscription and Origin host name** (Abonnement und Ursprungshostname): Wenn Sie als Back-End-Hosttyp nicht **Benutzerdefinierter Host** ausgewählt haben, müssen Sie den Ursprung auswählen. Wählen Sie dazu das entsprechende Abonnement und den entsprechenden Hostnamen des Ursprungs aus.

* **Hostheader für Ursprung**: Der Hostheaderwert, der für jede Anforderung an den Ursprung gesendet wird. Weitere Informationen finden Sie unter [Header des Ursprungshosts](#hostheader).

* **Priorität**: Weisen Sie den verschiedenen Ursprüngen Prioritäten zu, wenn Sie einen primären Ursprung für den gesamten Datenverkehr verwenden möchten. Stellen Sie außerdem Sicherungen bereit, wenn der primäre oder Sicherungsursprung nicht verfügbar ist. Weitere Informationen finden Sie unter [Priorität](#priority).

* **Gewichtung**: Weisen Sie den verschiedenen Ursprüngen Gewichtungen zu, um den Datenverkehr entweder gleichmäßig oder anhand von Gewichtungskoeffizienten auf eine Gruppe von Ursprüngen zu verteilen. Weitere Informationen finden Sie unter [Gewichtung](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>Hostheader für Ursprung

Anforderungen, die von Azure Front Door Standard/Premium an einen Ursprung weitergeleitet werden, enthalten ein Hostheaderfeld, das der Ursprung zum Abrufen der gewünschten Ressource verwendet. Der Wert für dieses Feld stammt in der Regel aus dem Ursprungs-URI, der den Hostheader und Port angibt.

Eine für `www.contoso.com` gesendete Anforderung weist beispielsweise den Hostheader `www.contoso.com` auf. Wenn Sie den Ursprung im Azure-Portal konfigurieren, wird standardmäßig der Back-End-Hostname als Wert für dieses Feld verwendet. Wenn der Ursprung im Azure-Portal `contoso-westus.azurewebsites.net` lautet, lautet der automatisch aufgefüllte Wert für den Hostheader des Ursprungs `contoso-westus.azurewebsites.net`. Wenn Sie jedoch Azure Resource Manager-Vorlagen oder eine andere Methode verwenden und dieses Feld nicht explizit festlegen, sendet Front Door den Eingangshostnamen als Wert für den Hostheader. Wenn die Anforderung für `www.contoso.com` erfolgt ist und das Headerfeld des Ursprungs `contoso-westus.azurewebsites.net` leer ist, wird als Hostheader `www.contoso.com` festgelegt.

Bei den meisten App-Back-Ends (Azure Web-Apps, Blob Storage und Cloud Services) muss der Hostheader der Domäne des Back-Ends entsprechen. Der Front-End-Host, der die Weiterleitung an das Back-End vornimmt, hat jedoch einen anderen Hostnamen, z. B. `www.contoso.net`.

Wenn der Hostheader für den Ursprung dem Back-End-Hostnamen entsprechen muss, müssen Sie sicherstellen, dass der Back-End-Hostheader den Back-End-Hostnamen enthält.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Konfigurieren des Ursprungshostheaders für den Ursprung

So konfigurieren Sie das Feld **Hostheader für Ursprung** für einen Ursprung im Abschnitt „Ursprungsgruppe“:

1. Öffnen Sie die Front Door-Ressource, und wählen Sie die Ursprungsgruppe mit dem zu konfigurierenden Ursprung aus.

2. Fügen Sie einen Ursprung hinzu, sofern noch keiner hinzugefügt wurde, oder bearbeiten Sie einen vorhandenen Ursprung.

3. Legen Sie das Feld „Hostheader für Ursprung“ auf einen benutzerdefinierten Wert fest, oder lassen Sie es leer. Der Hostname für die eingehende Anforderung wird als Hostheaderwert verwendet werden.

## <a name="origin-group"></a>Ursprungsgruppe

Eine Ursprungsgruppe in Azure Front Door Standard/Premium ist eine Gruppe von Ursprüngen, die ähnlichen Datenverkehr für die zugehörige Anwendung empfangen. Mit anderen Worten: Es handelt sich um eine logische Gruppierung Ihrer weltweiten Anwendungsinstanzen, die den gleichen Datenverkehr empfangen und mit einem erwarteten Verhalten reagieren. Diese Ursprünge können regionsübergreifend oder innerhalb derselben Region bereitgestellt werden. Alle Ursprünge können im Aktiv/Aktiv-Bereitstellungsmodus oder in einer sogenannten Aktiv/Passiv-Konfiguration bereitgestellt werden.

Eine Ursprungsgruppe definiert, wie Ursprünge über Integritätstests ausgewertet werden sollen. Er definiert ebenso, wie der Lastenausgleich zwischen den Back-Ends erfolgen soll.

### <a name="health-probes"></a>Integritätstests

Azure Front Door Standard/Premium sendet regelmäßige HTTP/HTTPS-Testanforderungen an jeden konfigurierten Ursprung. Anhand der Testanforderungen werden Näherung und Integrität jedes Ursprungs für den Lastenausgleich der Endbenutzeranforderungen bestimmt. Mit den Integritätstesteinstellungen für eine Ursprungsgruppe wird festgelegt, wie der Integritätsstatus der Anwendungs-Back-Ends abgefragt wird. Die folgenden Einstellungen sind für die Konfiguration des Lastenausgleichs verfügbar:

* **Pfad**: Die URL, die für Testanforderungen für alle Ursprünge in der Ursprungsgruppe verwendet wird. Beispiel: Wenn ein Ursprung `http://contoso-westus.azurewebsites.net/probe/test.aspx` lautet und der Pfad auf „/probe/test.aspx“ festgelegt wird, senden Front Door-Umgebungen Integritätstestanforderungen an `contoso-westus.azurewebsites.net` (sofern das Protokoll HTTP verwendet wird).

* **Protokoll**: Legt fest, ob die Integritätstestanforderungen von Front Door an die Ursprünge über das HTTP-Protokoll oder das HTTPS-Protokoll gesendet werden.

* **Methode**: Die HTTP-Methode, die zum Senden von Integritätstests verwendet werden soll. Mögliche Optionen sind GET oder HEAD (Standardeinstellung).
    > [!NOTE]
    > Um die Belastung und die Kosten Ihrer Back-Ends zu senken, empfiehlt Front Door die Verwendung von HEAD-Anforderungen für Integritätstests.

* **Intervall (Sekunden)** : Definiert die Häufigkeit der Integritätstests für Ihre Ursprünge bzw. die Intervalle, in denen die einzelnen Front Door-Umgebungen einen Test senden.

    >[!NOTE]
    >Für schnellere Failover sollten Sie das Intervall auf einen niedrigeren Wert festlegen. Je niedriger der Wert, desto größer ist jedoch das Integritätstestvolumen, das Ihre Back-Ends empfangen. Wenn beispielsweise bei 100 globalen Front Door-POPs das Intervall auf 30 Sekunden festgelegt ist, empfängt jedes Back-End etwa 200 Testanforderungen pro Minute.

Weitere Informationen finden Sie unter [Integritätstests](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Einstellungen für den Lastenausgleich

Anhand der Einstellungen für den Lastenausgleich für die Ursprungsgruppe wird definiert, wie Integritätstests ausgewertet werden. Diese Einstellungen bestimmen, ob der Ursprung fehlerfrei oder fehlerhaft ist. Dabei wird auch überprüft, wie der Lastenausgleich für den Datenverkehr zwischen den verschiedenen Ursprüngen in der Ursprungsgruppe erfolgen soll. Die folgenden Einstellungen sind für die Konfiguration des Lastenausgleichs verfügbar:

* **Stichprobengröße**: Gibt an, wie viele Stichproben von Integritätstests bei der Auswertung der Integrität der Ursprünge zu berücksichtigen sind.

* **Erfolgreiche Stichprobengröße**: Definiert die zuvor erwähnte Stichprobengröße, d. h. die Anzahl der erfolgreichen Stichproben, die erforderlich sind, um den Ursprung als fehlerfrei zu bezeichnen. Angenommen, das Intervall für Front Door-Integritätstests ist auf 30 Sekunden, die Stichprobengröße auf 5 und die erfolgreiche Stichprobengröße auf 3 festgelegt. Jedes Mal, wenn Integritätstests für den Ursprung ausgewertet werden, werden die letzten fünf Stichproben innerhalb von 150 Sekunden (5 x 30) betrachtet. Mindestens drei erfolgreiche Stichproben sind erforderlich, um den Ursprung als fehlerfrei zu deklarieren.

* **Wartezeitenaktivität (zusätzliche Wartezeit)** : Definiert, ob Azure Front Door Standard/Premium die Anforderung innerhalb der festgelegten Wartezeit an den Ursprung senden oder sie an das nächstgelegene Back-End weiterleiten soll.

Weitere Informationen finden Sie unter [Datenverkehrsrouting auf Grundlage der niedrigsten Latenzen](#latency).

## <a name="routing-methods"></a>Routingmethoden

Azure Front Door Standard/Premium unterstützt verschiedene Methoden für das Routing von Datenverkehr, um zu ermitteln, wie HTTP-/HTTPS-Datenverkehr an verschiedene Dienstendpunkte weitergeleitet werden soll. Wenn Ihr Client eine Verbindung mit Front Door anfordert, wird die konfigurierte Datenverkehrsrouting-Methode angewendet, um sicherzustellen, dass die Anforderungen an die Back-End-Instanz weitergeleitet werden, die am ehesten geeignet ist. 

In Azure Front Door Standard/Premium sind vier Methoden für das Datenverkehrsrouting verfügbar:

* **[Wartezeit](#latency):** Mit dem latenzbasierten Routing wird sichergestellt, dass Anforderungen an diejenigen Back-Ends gesendet werden, die innerhalb eines akzeptablen Empfindlichkeitsbereichs die niedrigste Latenz aufweisen. Benutzeranforderungen werden auf diese Weise unter Berücksichtigung der Netzwerklatenz an die nächstgelegenen Back-Ends gesendet.
* **[Priorität:](#priority)** Sie können Ihren Back-Ends Prioritäten zuweisen, wenn Sie ein primäres Back-End konfigurieren möchten, das den gesamten Datenverkehr verarbeitet. Beim sekundären Back-End kann es sich um eine Sicherung handeln, falls das primäre Back-End nicht verfügbar ist.
* **[Gewichtet:](#weighted)** Sie können Ihren Back-Ends Gewichtungen zuweisen, wenn Sie Datenverkehr über eine Sammlung von Back-Ends verteilen möchten. Diese Option gibt an, ob Sie Datenverkehr gleichmäßig oder gemäß den Gewichtungskoeffizienten verteilen möchten.

Alle Konfigurationen von Azure Front Door Standard/Premium schließen die Überwachung der Back-End-Integrität und ein automatisiertes, sofortiges und globales Failover ein. Weitere Informationen finden Sie unter [Integritätstestüberwachung in Azure Front Door Standard/Premium (Vorschau)](concept-health-probes.md). Front Door kann basierend auf einer einzelnen Routingmethode ausgeführt werden. Abhängig von den Anforderungen Ihrer Anwendung können Sie jedoch auch mehrere Routingmethoden kombinieren, um eine optimale Routingtopologie zu erstellen.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Datenverkehrsrouting auf Grundlage der niedrigsten Latenzen

Die Reaktionsfähigkeit Ihrer Anwendungen kann verbessert werden, indem Sie Back-Ends an mindestens zwei Standorten auf der Welt bereitstellen und Datenverkehr an das Ziel weiterleiten, das den Endbenutzern am „nächsten“ liegt. Mit der Standardmethode für Routing von Datenverkehr für Ihre Front Door-Konfiguration werden Anforderungen von Ihren Endbenutzern an das nächstgelegene Back-End der Front Door-Umgebung weitergeleitet, die die Anforderung empfangen hat. In Kombination mit der Anycast-Architektur von Azure Front Door wird durch diesen Ansatz gewährleistet, dass Ihre Endbenutzer je nach Standort von maximaler Leistung profitieren.

Das nächstgelegene Back-End ist nicht unbedingt dasjenige, das geografisch am nächsten liegt. Azure Front Door Service ermittelt das nächstgelegene Back-End durch die Messung der Netzwerklatenz.

Im Folgenden wird der allgemeine Ablauf zur Ermittlung der Back-Ends dargestellt:

| Verfügbare Back-Ends | Priorität | Latenzsignal (basierend auf Integritätstests) | Weights |
|-------------| ----------- | ----------- | ----------- |
| Zunächst werden alle aktivierten Back-Ends ausgewählt, die bei einem Integritätstest den Statuscode „200 OK“ zurückgeben und daher als fehlerfrei beurteilt werden. Es sind z. B. sechs Back-Ends A, B, C, D, E und F vorhanden. C ist fehlerhaft und E deaktiviert. Die Liste der verfügbaren Back-Ends umfasst damit A, B, D und F.  | Als Nächstes werden von den verfügbaren Back-Ends diejenigen mit der höchsten Priorität ausgewählt. Back-End A, B und D ist Priorität 1 und Back-End F Priorität 2 zugeordnet. Als Back-Ends werden dann A, B und D ausgewählt.| Alle Back-Ends innerhalb des Latenzbereichs (niedrigste Latenz und Latenzempfindlichkeit werden in ms angegeben) werden ausgewählt. Wenn Backend A 15 ms, B 30 ms und D 60 ms von der Front-Door-Umgebung entfernt ist, in der die Anforderung eingegangen ist, und die Latenzempfindlichkeit 30 ms beträgt, dann besteht der Pool mit der geringsten Latenz aus Backend A und B, da D mehr als 30 ms vom nächstgelegenen Backend A entfernt ist. | Abschließend führt Azure Front Door Service für den Datenverkehr der zuletzt ausgewählten Back-Ends einen Roundrobin entsprechend der festgelegten Gewichtungen durch. Angenommen, Back-End A wird eine Gewichtung von 5 und Back-End B eine Gewichtung von 8 zugeordnet. Der Datenverkehr wird dann im Verhältnis 5:8 auf die Back-Ends A und B aufgeteilt. |

>[!NOTE]
> Standardmäßig wird für die Eigenschaft der Latenzempfindlichkeit 0 ms festgelegt. Dadurch wird die Anforderung immer an das am schnellsten verfügbare Back-End weitergeleitet.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Prioritätsbasiertes Datenverkehrsrouting

Häufig möchte eine Organisation Hochverfügbarkeit für ihre Dienste gewährleisten, indem sie mehr als einen Sicherungsdienst für den Fall eines Ausfalls des primären Diensts bereitstellt. Diese Topologie wird branchenweit als Aktiv/Standby- oder Aktiv/Passiv-Topologie bezeichnet. Mithilfe der prioritätsbasierten Methode für das Datenverkehrsrouting können Azure-Kunden dieses Failovermuster problemlos implementieren.

Die Standardkonfiguration von Azure Front Door Service enthält eine Liste mit Back-Ends, denen dieselbe Priorität zugeordnet ist. Azure Front Door Service leitet dadurch Datenverkehr ausschließlich an die Back-Ends mit der höchsten Priorität (dem niedrigsten Prioritätswert) und damit an die primären Back-Ends weiter. Wenn die primären Back-Ends nicht verfügbar sind, wird der Datenverkehr an die sekundären Back-Ends (diejenigen, denen der zweitniedrigste Prioritätswert zugeordnet wurde) weitergeleitet. Wenn weder die primärem noch die sekundären Back-Ends verfügbar sind, wird der Datenverkehr an das dritte Back-End gesendet usw. Die Verfügbarkeit des Back-Ends basiert auf dem konfigurierten Status (aktiviert oder deaktiviert) und dem Back-End-Integritätsstatus, der regelmäßig durch Integritätstests ermittelt wird.

#### <a name="configuring-priority-for-backends"></a>Konfigurieren der Priorität für Back-Ends

Jedes Back-End in Ihrem Back-End-Pool der Azure Front Door Service-Konfiguration verfügt über die Eigenschaft „Priority“ (Priorität), die eine Zahl zwischen 1 und 5 sein kann. Mit Azure Front Door konfigurieren Sie die Back-End-Priorität für jedes Back-End explizit mithilfe dieser Eigenschaft. mithilfe dieser Eigenschaft festlegen. Niedrigere Werte stellen eine höhere Priorität dar. Ein Prioritätswert kann für mehrere Back-Ends verwendet werden.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewichtete Methode für das Datenverkehrsrouting
Bei der gewichteten Methode für das Datenverkehrsrouting wird der Datenverkehr gleichmäßig verteilt oder eine vordefinierte Gewichtung verwendet.

Bei der Methode für Datenverkehrsrouting auf Grundlage von Gewichtungen weisen Sie in der Azure Front Door Service-Konfiguration Ihres Back-End-Pools jedem Back-End eine Gewichtung zu. Die Gewichtung ist eine Ganzzahl zwischen 1 und 1000. Als Standardgewichtung für den Parameter wird 50 verwendet.

Mit der Liste der verfügbaren Back-Ends, die eine akzeptable Latenzempfindlichkeit aufweisen, wird der Datenverkehr mit einem Roundrobin-Mechanismus unter Verwendung des angegebenen Gewichtungsverhältnisses verteilt. Wenn für die Latenzempfindlichkeit ein Wert von 0 Millisekunden festgelegt ist, wird diese Eigenschaft nur dann berücksichtigt, wenn die Netzwerklatenz für zwei Back-Ends gleich ist. 

Durch die gewichtete Methode werden einige nützliche Szenarios ermöglicht:

* **Allmähliches Anwendungsupgrade**: Legt für den Datenverkehr einen Prozentwert fest, damit nur ein Teil des Datenverkehrs an ein neues Back-End weitergeleitet wird, und erhöhen Sie den Datenverkehr dann im Lauf der Zeit allmählich, bis dessen Umfang dem anderer Back-Ends entspricht.
* **Anwendungsmigration zu Azure:** Erstellen Sie einen Back-End-Pool mit Azure-Endpunkten und externen Endpunkten. Passen Sie dabei die Gewichtung der Back-Ends so an, dass neue Back-Ends priorisiert werden. Sie können diese Änderungen schrittweise einführen, in dem Sie zuerst neue Back-Ends deaktivieren, ihnen anschließend die niedrigsten Gewichtungen zuweisen und die Werte dann langsam erhöhen, bis die Back-Ends den größten Teil des Datenverkehrs übernehmen. Abschließend deaktivieren Sie die weniger bevorzugten Back-Ends und entfernen diese aus dem Pool.  
* **Cloudbursting für zusätzliche Kapazität:** Stellen Sie in kurzer Zeit für eine lokale Bereitstellung zusätzliche Ressourcen in der Cloud zur Verfügung, indem Sie Azure Front Door Service verwenden. Wenn Sie zusätzliche Kapazität in der Cloud benötigen, können Sie weitere Back-Ends hinzufügen oder aktivieren und dabei angeben, welcher Teil des Datenverkehrs an das jeweilige Back-End gesendet wird.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Erstellen einer Instanz von Front Door Standard/Premium](create-front-door-portal.md).
