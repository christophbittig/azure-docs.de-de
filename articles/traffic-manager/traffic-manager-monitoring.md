---
title: Azure-Traffic-Manager-Endpunktüberwachung
description: In diesem Artikel wird beschrieben, wie Traffic Manager die Endpunktüberwachung und das automatische Endpunktfailover verwendet, um Azure-Kunden bei der Bereitstellung von Anwendungen mit hoher Verfügbarkeit zu unterstützen.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2021
ms.author: allensu
ms.openlocfilehash: 52edf0cbae53540152a9991980499698b3292287
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467205"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager-Endpunktüberwachung

Azure Traffic Manager umfasst eine integrierte Endpunktüberwachung und ein automatisches Endpunktfailover. Dieses Feature unterstützt Sie bei der Bereitstellung von Anwendungen mit hoher Verfügbarkeit, die auch Endpunktausfälle überstehen, z.B. auch Ausfälle von Azure-Regionen.

## <a name="configure-endpoint-monitoring"></a>Konfigurieren der Endpunktüberwachung

Zum Konfigurieren der Endpunktüberwachung müssen Sie die folgenden Einstellungen in Ihrem Traffic Manager-Profil angeben:

* **Protokoll**. Wählen Sie HTTP, HTTPS oder TCP als Protokoll, das von Traffic Manager beim Testen Ihres Endpunkts verwendet wird, um seine Integrität zu überprüfen. Bei der HTTPS-Überwachung wird nicht überprüft, ob Ihr TLS/SSL-Zertifikat gültig ist. Es wird nur überprüft, ob es vorhanden ist.
* **Port**: Wählen Sie den Port aus, der für die Anforderung verwendet wird.
* **-Path:** Diese Konfigurationseinstellung gilt nur für die Protokolle HTTP und HTTPS, bei denen das Angeben der Pfadeinstellung obligatorisch ist. Das Angeben dieser Einstellung für das TCP-Überwachungsprotokoll führt zu einem Fehler. Geben Sie für das HTTP- und HTTPS-Protokoll den relativen Pfad und den Namen der Webseite oder Datei an, auf die bei der Überwachung zugegriffen wird. Ein Schrägstrich (/) ist ein gültiger Eintrag für den relativen Pfad. Dieser Wert bedeutet, dass sich die Datei im Stammverzeichnis befindet (Standard).
* **Benutzerdefinierte Headereinstellungen**. Diese Konfigurationseinstellung hilft Ihnen, bestimmte HTTP-Header den Integritätsüberprüfungen hinzuzufügen, die Traffic Manager an Endpunkte unter einem Profil sendet. Die benutzerdefinierten Header können auf Profilebene so festgelegt werden, dass sie für alle Endpunkte in diesem Profil gelten und/oder auf einer Endpunktebene, die nur für diesen Endpunkt gilt. Sie können benutzerdefinierte Header für Integritätsüberprüfungen von Endpunkten in einer mehrinstanzenfähigen Umgebung verwenden. Auf diese Weise können sie durch Angabe eines Hostheaders ordnungsgemäß an ihr Ziel weitergeleitet werden. Sie können mit dieser Einstellung auch eindeutige Header hinzufügen, mit denen Sie von Traffic Manager stammende HTTP(S)-Anforderungen identifizieren und unterschiedlich verarbeiten können. Sie können bis zu acht Header-Wert-Paare durch Trennzeichen getrennt angeben. Beispiel: „header1:wert1,header2:wert2“. 

   > HINWEIS: Die Verwendung von Sternchen (\*) in benutzerdefinierten `Host`-Headern wird nicht unterstützt.

* **Erwartete Statuscodebereiche**. Mit dieser Einstellung können Sie mehrere Erfolgscodebereiche im Format 200-299, 301-301 angeben. Wenn diese Statuscodes als Antwort von einem Endpunkt empfangen werden, wenn eine Integritätsprüfung erfolgt, markiert Traffic Manager diese Endpunkte als fehlerfrei. Sie können maximal acht Statuscodebereiche angeben. Diese Einstellung gilt nur für das HTTP- und HTTPS-Protokoll sowie für alle Endpunkte. Diese Einstellung bezieht sich auf die Traffic Manager-Profilebene und standardmäßig ist der Wert 200 als Erfolgsstatuscode definiert.
* **Testintervall**: Dieser Wert gibt an, wie oft die Integrität eines Endpunkts mit einem Test-Agent von Traffic Manager überprüft wird. Sie können hier zwei Werte angeben: 30 Sekunden (Standardtest) und 10 Sekunden (schneller Test). Wenn keine Werte angegeben sind, wird für das Profil ein Standardwert von 30 Sekunden festgelegt. Weitere Informationen zu den Preisen für schnelle Tests finden Sie auf der Seite [Traffic Manager – Preise](https://azure.microsoft.com/pricing/details/traffic-manager).
* **Tolerierte Anzahl von Fehlern**: Dieser Wert gibt an, wie viele Fehler von einem Traffic Manager-Test-Agent toleriert werden, bevor der Endpunkt als fehlerhaft markiert wird. Der Wert kann zwischen 0 und 9 liegen. Der Wert 0 bedeutet, dass der Endpunkt schon bei nur einem Überwachungsfehler als fehlerhaft markiert werden kann. Wenn kein Wert angegeben ist, wird der Standardwert 3 verwendet.
* **Test-Timeout**: Mit dieser Eigenschaft wird angegeben, wie lange der Traffic Manager-Test-Agent warten sollte, bevor der Integritätstest eines Endpunkts als nicht bestanden gilt. Bei einem Testintervall von 30 Sekunden können Sie für den Timeout einen Wert zwischen 5 und 10 Sekunden festlegen. Wenn kein Wert angegeben ist, wird der Standardwert von 10 Sekunden verwendet. Bei einem Testintervall von 10 Sekunden können Sie für den Timeout einen Wert zwischen 5 und 9 Sekunden festlegen. Wenn kein Timeoutwert angegeben ist, wird der Standardwert von 9 Sekunden verwendet.

    ![Traffic Manager-Endpunktüberwachung](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Abbildung:  Traffic Manager-Endpunktüberwachung**

## <a name="how-endpoint-monitoring-works"></a>Funktionsweise der Endpunktüberwachung

Wenn das Überwachungsprotokoll auf HTTP oder HTTPS festgelegt ist, sendet der Traffic Manager-Test-Agent eine GET-Anforderung an den Endpunkt, indem er die Angaben zum Protokoll, Port und relativen Pfad verwendet. Ein Endpunkt wird als fehlerfrei angesehen, wenn der Test-Agent die Antwort „200-OK“ oder eine der in den **Erwarteten \*Statuscodebereichen** konfigurierten Antworten empfängt. Bei einer Antwort mit einem anderen Wert oder bei Nichterhalt einer Antwort innerhalb des Timeout-Zeitraums startet der Test-Agent von Traffic Manager gemäß der Einstellung „Tolerierte Anzahl von Fehlern“ erneute Versuche. Wenn diese Einstellung auf 0 festgelegt ist, werden keine erneuten Versuche durchgeführt. Falls die Anzahl von aufeinanderfolgenden Fehlern höher als der Wert der Einstellung „Tolerierte Anzahl von Fehlern“ ist, wird der Endpunkt als fehlerhaft markiert.

Bei Verwendung von TCP als Überwachungsprotokoll erstellt der Traffic Manager-Test-Agent eine TCP-Verbindungsanforderung über den angegebenen Port. Wenn der Endpunkt auf die Anforderung mit einer Antwort zur Herstellung einer Verbindung reagiert, wird die Integritätsprüfung als erfolgreich markiert. Der Traffic Manager-Test-Agent setzt die TCP-Verbindung zurück. Bei einer Antwort mit einem anderen Wert oder bei Nichterhalt einer Antwort innerhalb des Timeout-Zeitraums startet der Test-Agent von Traffic Manager gemäß der Einstellung „Tolerierte Anzahl von Fehlern“ erneute Versuche. Wenn diese Einstellung auf 0 festgelegt ist, werden keine erneuten Versuche durchgeführt. Falls die Anzahl von aufeinanderfolgenden Fehlern höher als der Wert der Einstellung „Tolerierte Anzahl von Fehlern“ ist, wird der Endpunkt als fehlerhaft markiert.

In allen Fällen testet der Traffic Manager von mehreren Standorten aus. Die aufeinanderfolgenden Fehler bestimmen, was innerhalb der einzelnen Regionen geschieht. Dies bedeutet auch, dass Integritätstests von Traffic Manager für Endpunkte häufiger erfolgen, als mit der Einstellung unter „Testintervall“ angegeben ist.

>[!NOTE]
>Für das HTTP- bzw. HTTPS-Überwachungsprotokoll wird endpunktseitig in Ihrer Anwendung häufig eine benutzerdefinierte Seite implementiert, z.B. „/health.aspx“. Wenn Sie diesen Pfad für die Überwachung verwenden, können Sie anwendungsspezifische Überprüfungen durchführen, wie z.B. die Überprüfung von Leistungsindikatoren oder der Datenbankverfügbarkeit. Die Seite gibt basierend auf diesen benutzerdefinierten Überprüfungen einen entsprechenden HTTP-Statuscode zurück.

Alle Endpunkte in einem Traffic Manager-Profil teilen sich Überwachungseinstellungen. Falls Sie für verschiedene Endpunkte unterschiedliche Überwachungseinstellungen verwenden müssen, können Sie [geschachtelte Traffic Manager-Profile](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings) erstellen.

## <a name="endpoint-and-profile-status"></a>Endpunkt- und Profilstatus

Sie können Traffic Manager-Profile und -Endpunkte aktivieren und deaktivieren. Der Endpunktstatus kann sich auch aufgrund der automatisierten Traffic Manager-Einstellungen und -Prozessen ändern.

### <a name="endpoint-status"></a>Endpunktstatus

Sie können einen spezifischen Endpunkt aktivieren oder deaktivieren. Der zugrunde liegende Dienst, der weiterhin fehlerfrei sein kann, ist nicht betroffen. Das Ändern des Endpunktstatus steuert die Verfügbarkeit des Endpunkts im Traffic Manager-Profil. Wenn der Status eines Endpunkts deaktiviert ist, überprüft Traffic Manager die Integrität des Endpunkts nicht, und der Endpunkt wird nicht in eine DNS-Antwort einbezogen.

### <a name="profile-status"></a>Profilstatus

Indem Sie die Profilstatuseinstellung verwenden, können Sie ein spezifisches Profil aktivieren oder deaktivieren. Während der Endpunktstatus einen einzelnen Endpunkt betrifft, wirkt sich der Profilstatus auf das gesamte Profil aus, einschließlich aller Endpunkte. Wenn Sie ein Profil deaktivieren, werden die Endpunkte nicht auf Integrität überprüft, und keine Endpunkte werden in eine DNS-Antwort eingeschlossen. Ein [NXDOMAIN](https://tools.ietf.org/html/rfc2308)-Antwortcode wird für die DNS-Abfrage zurückgegeben.

### <a name="endpoint-monitor-status"></a>Überwachungsstatus von Endpunkten

Der Überwachungsstatus von Endpunkten ist ein Wert, der von Traffic Manager generiert wird und den Status eines Endpunkts anzeigt. Sie können diese Einstellung nicht manuell ändern. Der Überwachungsstatus eines Endpunkts ist eine Kombination aus den Ergebnissen der Endpunktüberwachung und dem konfigurierten Endpunktstatus. In der folgenden Tabelle sind die möglichen Werte des Überwachungsstatus von Endpunkten aufgeführt:

| Profilstatus | Endpunktstatus | Überwachungsstatus von Endpunkten | Notizen |
| --- | --- | --- | --- |
| Disabled |Aktiviert |Inaktiv |Das Profil wurde deaktiviert. Obwohl der Endpunktstatus immer noch „Aktiviert“ lautet, hat der Profilstatus („Deaktiviert“) Vorrang. Endpunkte in deaktivierten Profilen werden nicht überwacht. Ein „NXDOMAIN“-Antwortcode wird für die DNS-Abfrage zurückgegeben. |
| &lt;beliebig&gt; |Disabled |Disabled |Der Endpunkt wurde deaktiviert. Deaktivierte Endpunkte werden nicht überwacht. Der Endpunkt wird nicht in DNS-Antworten einbezogen und empfängt als solcher keinen Datenverkehr. |
| Aktiviert |Aktiviert |Online |Der Endpunkt wird überwacht und ist fehlerfrei. Er wird in DNS-Antworten einbezogen und kann Datenverkehr empfangen. |
| Aktiviert |Aktiviert |Heruntergestuft |Bei Integritätsprüfungen im Rahmen der Endpunktüberwachung werden Fehler erkannt. Der Endpunkt wird nicht in DNS-Antworten einbezogen und empfängt keinen Datenverkehr. <br>Eine Ausnahme ist, wenn alle Endpunkte heruntergestuft werden. In diesem Fall wird davon ausgegangen, dass alle in der Abfrageantwort zurückgegeben werden.</br>|
| Aktiviert |Aktiviert |CheckingEndpoint |Der Endpunkt wird überwacht, die Ergebnisse des ersten Tests wurden jedoch noch nicht empfangen. CheckingEndpoint ist ein temporärer Status, der in der Regel unmittelbar nach dem Hinzufügen oder Aktivieren eines Endpunkts im Profil auftritt. Ein Endpunkt in diesem Status kann in DNS-Antworten einbezogen werden und Datenverkehr empfangen. |
| Aktiviert |Aktiviert |Beendet |Die Web-App, auf die der Endpunkt zeigt, wird nicht ausgeführt. Überprüfen Sie die Web-App-Einstellungen. Dieser Status kann auch vorkommen, wenn der Endpunkt ein geschachtelter Endpunkt ist und das untergeordnete Profil deaktiviert oder inaktiv ist. <br>Ein Endpunkt mit dem Status „Beendet“ wird nicht überwacht. Er wird nicht in DNS-Antworten einbezogen und empfängt keinen Datenverkehr. Eine Ausnahme ist, wenn alle Endpunkte heruntergestuft werden. In diesem Fall wird davon ausgegangen, dass alle in der Abfrageantwort zurückgegeben werden.</br>|

Ausführlichere Informationen zur Berechnung des Überwachungsstatus bei geschachtelten Endpunkten finden Sie unter [Geschachtelte Traffic Manager-Profile](traffic-manager-nested-profiles.md).

>[!NOTE]
> Der Überwachungsstatus „Endpunkt wurde beendet“ kann in App Service auftreten, wenn Ihre Webanwendung nicht mindestens im Tarif „Standard“ ausgeführt wird. Weitere Informationen finden Sie unter [Steuern des Azure App Service-Datenverkehrs mit Azure Traffic Manager](../app-service/web-sites-traffic-manager.md).

### <a name="profile-monitor-status"></a>Überwachungsstatus von Profilen

Der Überwachungsstatus von Profilen ist das Ergebnis einer Kombination aus dem konfigurierten Profilstatus und den Überwachungsstatuswerten von Endpunkten für alle Endpunkte. Eine Beschreibung der möglichen Werte finden Sie in der folgenden Tabelle:

| Profilstatus (wie konfiguriert) | Überwachungsstatus von Endpunkten | Überwachungsstatus von Profilen | Notizen |
| --- | --- | --- | --- |
| Disabled |&lt;beliebig&gt; oder ein Profil ohne definierte Endpunkte. |Disabled |Das Profil wurde deaktiviert. |
| Aktiviert |Der Status mindestens eines Endpunkts lautet „Heruntergestuft“. |Heruntergestuft |Überprüfen Sie die einzelnen Endpunkt-Statuswerte, um zu ermitteln, für welche Endpunkte weitere Aufmerksamkeit erforderlich ist. |
| Aktiviert |Der Status mindestens eines Endpunkts lautet „Online“. Kein Endpunkt weist den Status „Heruntergestuft“ auf. |Online |Der Dienst akzeptiert Datenverkehr. Es ist keine weitere Aktion erforderlich. |
| Aktiviert |Der Status mindestens eines Endpunkts lautet „CheckingEndpoint“. Es befinden sich keine Endpunkte in den Status „Online“ oder „Heruntergestuft“. |CheckingEndpoints |Dieser Übergangsstatus tritt auf, wenn ein Profil erstellt oder aktiviert wird. Die Endpunktintegrität wird zum ersten Mal überprüft. |
| Aktiviert |Der Status aller Endpunkte im Profil ist „Deaktiviert“ oder „Beendet“, oder im Profil wurden keine Endpunkte definiert. |Inaktiv |Es sind keine Endpunkte aktiv, das Profil ist jedoch weiterhin aktiviert. |

## <a name="endpoint-failover-and-recovery"></a>Endpunktfailover und -wiederherstellung

Traffic Manager überprüft regelmäßig die Integrität jedes Endpunkts, einschließlich fehlerhafter Endpunkte. Traffic Manager erkennt, wenn ein Endpunkt fehlerfrei wird, und nimmt ihn wieder in den Kreislauf auf.

Ein Endpunkt ist fehlerhaft, wenn eines der folgenden Ereignisse eintritt:

- Bei Verwendung des Überwachungsprotokolls HTTP oder HTTPS:
    - Eine „Nicht-200“-Antwort oder eine Antwort, die nicht den in der Einstellung **Erwartete Statuscodebereiche** angegebenen Statusbereich enthält, wird empfangen. (Einschließlich eines anderen 2xx-Codes oder einer 301/302-Umleitung).
- Bei Verwendung des Überwachungsprotokolls TCP: 
    - Eine andere Antwort als ACK oder SYN-ACK wird als Reaktion auf die von Traffic Manager gesendete SYN-Anforderung empfangen, um zu versuchen, eine Verbindung herzustellen.
- Timeout 
- Alle anderen Verbindungsprobleme, die dazu führen, dass der Endpunkt nicht erreichbar ist.

Weitere Informationen zur Behandlung von Fehlern bei Integritätsprüfungen finden Sie unter [Problembehandlung beim Status „Heruntergestuft“ in Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Die Zeitachse in der folgenden Abbildung enthält eine ausführliche Beschreibung des Prozesses der Überwachung des Traffic Manager-Endpunkts mit folgenden Einstellungen:

* Das Überwachungsprotokoll ist HTTP.
* Das Standardintervall beträgt 30 Sekunden.
* Die Anzahl der tolerierten Fehler ist 3.
* Der Timeoutwert beträgt 10 Sekunden.
* Die DNS-Gültigkeitsdauer beträgt 30 Sekunden.

![Sequenz für Traffic Manager-Endpunktfailover und -failback](./media/traffic-manager-monitoring/timeline.png)

**Abbildung:  Sequenz für Traffic Manager-Endpunktfailover und -wiederherstellung**

1. **GET**. Für jeden Endpunkt führt das Traffic Manager-Überwachungssystem eine GET-Anforderung für den Pfad aus, der in den Überwachungseinstellungen angegeben wurde.
2. **200 OK oder im benutzerdefinierten Codebereich angegebene Traffic Manager-Profilüberwachungseinstellungen**. Das Überwachungssystem erwartet, dass innerhalb von zehn Sekunden die Meldung „HTTP 200 OK“ oder ein Statuscode in dem in den Überwachungseinstellungen angegebenem Bereich zurückgegeben wird. Wenn diese Nachricht empfangen wird, wird der Dienst als verfügbar betrachtet.
3. **30 Sekunden zwischen Überprüfungen**. Die Endpunktintegritätsprüfung wird alle 30 Sekunden wiederholt.
4. **Dienst nicht verfügbar**. Der Dienst ist nicht verfügbar. Traffic Manager kann den Status erst bei der nächsten Integritätsprüfung ermitteln.
5. **Versuch, auf den Überwachungspfad zuzugreifen**. Das Überwachungssystem führt eine GET-Anforderung aus, empfängt jedoch innerhalb des Timeout-Zeitraums von 10 Sekunden keine Antwort. Es werden dann in Abständen von jeweils 30 Sekunden drei weitere Versuche durchgeführt. Wenn einer der Versuche erfolgreich ist, wird die Anzahl der Versuche zurückgesetzt.
6. **Status auf „Heruntergestuft“ festgelegt**. Nach dem vierten erfolglosen Versuch in Folge markiert das Überwachungssystem den Status des nicht verfügbaren Endpunkts als „Heruntergestuft“.
7. **Datenverkehr wird an andere Endpunkte umgeleitet**. Die DNS-Namenserver in Traffic Manager werden aktualisiert, und Traffic Manager gibt den Endpunkt in Antworten auf DNS-Abfragen nicht mehr zurück. Neue Verbindungen werden an andere, verfügbare Endpunkte umgeleitet. Jedoch werden vorherige DNS-Antworten, die diesen Endpunkt enthalten, von rekursiven DNS-Servern und DNS-Clients möglicherweise weiterhin zwischengespeichert. Clients verwenden den Endpunkt weiterhin, bis der DNS-Cache abläuft. Wenn dieser Cache abläuft, führen Clients neue DNS-Abfragen durch und werden an andere Endpunkte umgeleitet. Die Cachedauer wird über die TTL-Einstellung im Traffic Manager-Profil gesteuert, z.B. 30 Sekunden.
8. **Integritätsprüfungen werden fortgesetzt**. Traffic Manager fährt mit der Integritätsprüfung für den Endpunkt fort, während dieser sich im Status „Heruntergestuft“ befindet. Traffic Manager erkennt, wenn der Endpunkt wieder fehlerfrei wird.
9. **Service wird wieder online geschaltet**. Der Dienst wird verfügbar. Der Endpunkt behält in Traffic Manager den Status „Heruntergestuft“, bis das Überwachungssystem die nächste Integritätsprüfung durchführt.
10. **Datenverkehr an den Dienst wird wieder aufgenommen**. Traffic Manager sendet eine GET-Anforderung und empfängt eine 200 OK-Statusantwort. Der Dienst befindet sich wieder in einem fehlerfreien Zustand. Die Traffic Manager-Namensserver werden aktualisiert und beginnen damit, den DNS-Namen des Diensts in DNS-Antworten wieder anzugeben. Der Datenverkehr wird wieder an den Endpunkt geleitet, wenn zwischengespeicherte DNS-Antworten mit Rückgabe anderer Endpunkte ablaufen und vorhandene Verbindungen mit anderen Endpunkten beendet werden.

    > [!IMPORTANT]
    > Der Traffic Manager stellt für jeden Endpunkt mehrere Tests von mehreren Standorten aus zur Verfügung. Mehrere Tests erhöhen die Resilienz für die Endpunktüberwachung. Der Traffic Manager fasst die durchschnittliche Integrität der Tests zusammen, anstatt sich auf eine einzelne Testinstanz zu verlassen. Die Redundanz des Testsystems ist standardmäßig. Endpunktwerte sollten ganzheitlich und nicht pro Test betrachtet werden. Die für die Testzustandsanzeige angezeigte Zahl ist ein Durchschnittswert. Der Status sollte nur dann ein Problem sein, wenn weniger als 50 % (0,5) der Tests einen **Aktiv**-Status veröffentlichen.

    > [!NOTE]
    > Da Traffic Manager auf DNS-Ebene arbeitet, kann er vorhandene Verbindungen mit Endpunkten nicht beeinflussen. Beim Weiterleiten von Datenverkehr zwischen Endpunkten (entweder durch geänderte Profileinstellungen oder beim Failover oder Failback) leitet Traffic Manager neue Verbindungen an verfügbare Endpunkte weiter. Andere Endpunkte erhalten jedoch möglicherweise über bestehende Verbindungen weiterhin Datenverkehr, bis diese Sitzungen beendet werden. Um diesen Datenverkehr aus den vorhandenen Verbindungen zu beseitigen, sollte die Sitzungsdauer für Anwendungen verringert werden, die für einen Endpunkt jeweils verwendet wird.

## <a name="traffic-routing-methods"></a>Methoden für das Datenverkehrrouting

Wenn ein Endpunkt den Status „Heruntergestuft“ aufweist, wird er nicht mehr als Antwort auf DNS-Abfragen zurückgegeben. Stattdessen wird ein alternativer Endpunkt ausgewählt und zurückgegeben. Die im Profil konfigurierte Methode für das Datenverkehrrouting bestimmt, wie der alternative Endpunkt ausgewählt wird.

* **Priorität**. Endpunkte bilden eine mit Prioritäten versehene Liste. Der erste verfügbare Endpunkt der Liste wird immer zurückgegeben. Wenn ein Endpunkt sich im Status „Heruntergestuft“ befindet, wird der nächste verfügbare Endpunkt zurückgegeben.
* **Gewichtet**. Alle verfügbaren Endpunkte werden basierend auf der ihnen zugewiesenen Gewichtung und den Gewichtungen der anderen verfügbaren Endpunkte zufällig ausgewählt.
* **Leistung**: Der Endpunkt, der dem Endbenutzer am nächsten liegt, wird zurückgegeben. Wenn dieser Endpunkt nicht verfügbar ist, verschiebt Traffic Manager den Datenverkehr an die Endpunkte in der nächstgelegenen Azure-Region. Sie können alternative Failoverpläne für das leistungsorientierte Datenverkehrsrouting mithilfe von [geschachtelten Traffic Manager-Profilen](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region) konfigurieren.
* **Geografisch**. Der zugeordnete Endpunkt zum Bereitstellen des geografischen Standorts basierend auf den IPs der Abfrageanforderung wird zurückgegeben. Falls dieser Endpunkt nicht verfügbar ist, wird kein anderer Endpunkt für das Failover ausgewählt, da ein geografischer Standort nur einem Endpunkt eines Profils zugeordnet werden kann. (Weitere Details unter [Häufig gestellte Fragen (FAQ) zu Traffic Manager](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Als bewährte Methode bei der Verwendung des geografischen Routings empfehlen wir Kunden, geschachtelte Traffic Manager-Profile mit mehr als einem Endpunkt zu verwenden.
* **MultiValue**: Mehrere Endpunkte, die IPv4/IPv6-Adressen zugeordnet sind, werden zurückgegeben. Wenn eine Abfrage für dieses Profil empfangen wird, werden fehlerfreie Endpunkte basierend auf dem von Ihnen angegebenen Wert für die **maximale Datensatzanzahl in einer Antwort** zurückgegeben. Die Standardanzahl der Antworten sind zwei Endpunkte.
* **Subnetz**: Der Endpunkt, der einem Satz von IP-Adressbereichen zugeordnet ist, wird zurückgegeben. Wenn eine Anforderung von dieser IP-Adresse empfangen wird, ist der zurückgegebene Endpunkt derjenige, der für diese IP-Adresse zugeordnet ist. 

Weitere Informationen finden Sie unter [Traffic Manager-Methoden für das Datenverkehrsrouting](traffic-manager-routing-methods.md).

> [!NOTE]
> Eine Ausnahme des normalen Datenverkehrsrouting-Verhaltens tritt auf, wenn alle auswählbaren Endpunkte den Status „Heruntergestuft“ aufweisen. Traffic Manager wählt die beste Lösung und *antwortet so, als befänden sich alle heruntergestuften Endpunkte im Status „Online“* . Dieses Verhalten ist besser als die Alternative, bei der für die DNS-Antwort keinerlei Endpunkte zurückgegeben würden. Deaktivierte oder beendete Endpunkte werden nicht überwacht und daher für den Datenverkehr als nicht auswählbar betrachtet.
>
> Diese Bedingung wird häufig durch eine falsche Konfiguration des Dienst verursacht, wie z.B.:
>
> * Blockierung der Traffic Manager-Integritätsprüfungen durch eine Zugriffssteuerungsliste (Access Control List, ACL)
> * Eine falsche Konfiguration des Überwachungsports oder -protokolls im Traffic Manager-Profil
>
> Wenn die Traffic Manager-Integritätsprüfungen nicht richtig konfiguriert sind, führt dieses Verhalten dazu, dass es aufgrund des Datenverkehrsroutings so aussieht, *als ob* Traffic Manager richtig funktioniert. Allerdings kann in diesem Fall kein Endpunktfailover durchgeführt werden. Dies wirkt sich auf die Gesamtverfügbarkeit der Anwendung aus. Es ist wichtig, sicherzustellen, dass das Profil den Status „Online“ aufweist, und nicht den Status „Heruntergestuft“. Der Status „Online“ weist darauf hin, dass die Traffic Manager-Integritätsprüfungen wie gewünscht durchgeführt werden.

Weitere Informationen zur Behandlung von Fehlern bei Integritätsprüfungen finden Sie unter [Problembehandlung beim Status „Heruntergestuft“ in Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Häufig gestellte Fragen

* [Ist Traffic Manager gegenüber Ausfällen von Azure-Regionen resistent?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [Wie wirkt sich die Wahl des Ressourcengruppenstandorts auf Traffic Manager aus?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Wie kann ich den aktuellen Integritätsstatus jedes Endpunkts bestimmen?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [Kann ich HTTPS-Endpunkte überwachen?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [Verwende ich beim Hinzufügen eines Endpunkts eine IP-Adresse oder einen DNS-Namen?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Welche Arten von IP-Adressen kann ich beim Hinzufügen eines Endpunkts verwenden?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Kann ich verschiedene Endpunktadressierungsarten innerhalb eines einzigen Profils verwenden?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Was passiert, wenn sich der Eintragstyp einer eingehenden Abfrage vom Eintragstyp unterscheidet, der dem Adressierungstyp der Endpunkte zugeordnet ist?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Kann ich ein Profil mit über IPv4/IPv6 adressierten Endpunkten in einem geschachtelten Profil verwenden?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Ich habe einen Webanwendungs-Endpunkt in meinem Traffic Manager-Profil angehalten, aber ich empfange auch nach einem Neustart keinen Datenverkehr. Wie kann ich dieses Problem beheben?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Kann ich Traffic Manager auch dann verwenden, wenn meine Anwendung HTTP oder HTTPS nicht unterstützt?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Welche bestimmten Antworten vom Endpunkt sind bei Verwendung der TCP-Überwachung erforderlich?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Wie schnell verschiebt Traffic Manager meine Benutzer von einem fehlerhaften Endpunkt weg?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Wie kann ich verschiedene Überwachungseinstellungen für verschiedene Endpunkte in einem Profil angeben?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Wie kann ich meinen Endpunkten HTTP-Header für die Traffic Manager-Integritätsprüfungen zuweisen?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Welcher Hostheader wird für die Integritätsprüfungen für Endpunkte verwendet?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [Wie lauten die IP-Adressen, von denen die Integritätsüberprüfungen stammen?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Wie viele Integritätsprüfungen meines Endpunkts kann ich von Traffic Manager erwarten?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Wie kann ich benachrichtigt werden, wenn einer meiner Endpunkte ausfällt?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Funktionsweise von Traffic Manager](traffic-manager-how-it-works.md)

Informieren Sie sich über die von Traffic Manager unterstützten [Methoden für das Datenverkehrsrouting](traffic-manager-routing-methods.md) .

Informieren Sie sich über das [Erstellen eines Traffic Manager-Profils](traffic-manager-manage-profiles.md)

[Problembehandlung beim Status „Heruntergestuft“](traffic-manager-troubleshooting-degraded.md) auf einem Traffic Manager-Endpunkt.
