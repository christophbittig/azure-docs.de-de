---
title: Monitor Azure Virtual Desktop-Glossar – Azure
description: Ein Glossar mit Begriffen und Konzepten im Zusammenhang mit Azure Monitor für Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 46e70166fffba7c938ea6784db6eda18e69a043f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214755"
---
# <a name="azure-monitor-for-azure-virtual-desktop-glossary"></a>Azure Monitor für Azure Virtual Desktop: Glossar

In diesem Artikel werden Schlüsselbegriffe und Konzepte im Zusammenhang mit Azure Monitor für Azure Virtual Desktop (Vorschau) vorgestellt und kurz beschrieben.

## <a name="alerts"></a>Warnungen

Alle aktiven Azure Monitor-Warnungen, die Sie für das Abonnement konfiguriert und mit [Schweregrad 0](#severity-0-alerts) klassifiziert haben, werden auf der Seite „Übersicht" angezeigt. Weitere Informationen zum Einrichten von Warnungen finden Sie unter [Azure Monitor-Protokollwarnungen](../azure-monitor/alerts/alerts-log.md).

## <a name="available-sessions"></a>Verfügbare Sitzungen

„Verfügbare Sitzungen“ zeigt die Anzahl der im Hostpool verfügbaren Sitzungen. Der Dienst berechnet diese Anzahl, indem die Anzahl der virtuellen Computer (VMs) mit der maximal zulässigen Anzahl von Sitzungen pro VM multipliziert und dann die Gesamtanzahl der Sitzungen subtrahiert wird.

## <a name="connection-success"></a>Verbindung erfolgreich

Dieses Element zeigt die Verbindungsintegrität. „Verbindung erfolgreich“ bedeutet, dass die Verbindung den Host erreichen kann, was vom Stapel auf der jeweiligen VM bestätigt wird. Eine fehlgeschlagene Verbindung bedeutet, dass die Verbindung den Host nicht erreichen konnte.

## <a name="daily-active-users-dau"></a>Aktive Benutzer pro Tag

Die Gesamtanzahl der Benutzer, die in den letzten 24 Stunden eine Sitzung gestartet haben.

## <a name="daily-alerts"></a>Tägliche Warnungen

Die Gesamtanzahl der pro Tag ausgelösten Warnungen.

## <a name="daily-connections-and-reconnections"></a>Tägliche Verbindungen und erneute Verbindungen

Die Gesamtanzahl der Verbindungen und erneuten Verbindungen, die innerhalb der letzten 24 Stunden gestartet oder abgeschlossen wurden.

## <a name="daily-connected-hours"></a>Täglich verbundene Stunden

Die Gesamtanzahl der Stunden, in denen die Benutzer in den letzten 24 Stunden mit einer Sitzung verbunden waren.

## <a name="diagnostics-and-errors"></a>Diagnose und Fehler

Wenn ein Fehler oder eine Warnung in Azure Monitor für Azure Virtual Desktop auftritt, erfolgt eine Kategorisierung nach drei Kriterien:

- Aktivitätstyp: Diese Kategorie bestimmt, wie der Fehler von der Azure Virtual Desktop-Diagnose kategorisiert wird. Die Kategorien sind Verwaltungsaktivitäten, Feeds, Verbindungen, Hostregistrierungen, Fehler und Prüfpunkte. Weitere Informationen zu diesen Kategorien finden Sie unter [Verwenden von Log Analytics für die Diagnosefunktion](diagnostics-log-analytics.md).

- Variante: Diese Kategorie zeigt den Speicherort des Fehlers. 

     - Fehler, die mit „service“ oder „ServiceError = TRUE“ markiert sind, sind im Dienst Azure Virtual Desktop aufgetreten.
     - Fehler, die mit „deployment“ oder „ServiceError = FALSE“ markiert sind, sind außerhalb des Diensts Azure Virtual Desktop aufgetreten.
     - Weitere Informationen zum Tag ServiceError finden Sie unter [Gängige Fehlerszenarien](./troubleshoot-set-up-overview.md).

- Quelle: Diese Kategorie liefert eine spezifischere Beschreibung, wo der Fehler aufgetreten ist.

     - Diagnose: Die Dienstrolle, die für die Überwachung und Berichterstellung bezüglich der Dienstaktivität verantwortlich ist, damit Benutzer Bereitstellungsprobleme beobachten und diagnostizieren können.

     - RDBroker: Die Dienstrolle, die u. a. für die Orchestrierung von Bereitstellungsaktivitäten, Aufrechterhaltung des Zustands von Objekten und Überprüfung der Authentifizierung verantwortlich ist.

     - RDGateway: Die Dienstrolle, die für die Netzwerkkonnektivität zwischen Endbenutzern und VMs zuständig ist.

     - RDStack: Eine Softwarekomponente, die auf Ihren VMs installiert ist, damit diese mit dem Dienst Azure Virtual Desktop kommunizieren können.

     - Client: Software, die auf dem Computer des Endbenutzers ausgeführt wird und die Schnittstelle zum Dienst Azure Virtual Desktop bereitstellt. Sie zeigt die Liste der veröffentlichten Ressourcen an und hostet die Remotedesktopverbindung, nachdem Sie eine Wahl getroffen haben.

Alle Diagnoseprobleme oder -fehler enthalten eine Meldung, in der erläutert wird, was falsch gelaufen ist. Weitere Informationen zur Problembehandlung finden Sie unter [Identifizieren und Diagnostizieren von Problemen mit Azure Virtual Desktop](./troubleshoot-set-up-overview.md).

## <a name="input-delay"></a>Eingabeverzögerung

„Eingabeverzögerung“ in Azure Monitor für Azure Virtual Desktop bezieht sich auf den Leistungsindikator „Input Delay Per Process“ (Eingabeverzögerung pro Prozess) für jede Sitzung. Auf der Host-Leistungsseite unter [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks) ist dieser Leistungszähler so konfiguriert, dass er einmal alle 30 Sekunden einen Bericht an den Dienst sendet. Diese 30-Sekunden-Intervalle werden als „Stichproben“ bezeichnet und melden den ungünstigsten Fall in diesem Fenster. Die Werte „Median“ und „p95“ geben den Median und das 95. Perzentil aller Stichproben wieder.

Unter **Input delay by host** (Eingabeverzögerung nach Host) können Sie eine Sitzungshostzeile auswählen, um alle anderen visuellen Elemente auf der Seite zu diesem Host zu filtern. Sie können auch einen Prozessnamen auswählen, um die durchschnittliche Eingabeverzögerung im Zeitdiagramm zu filtern.

Wir ordnen Verzögerungen den folgenden Kategorien zu:

- Gut: unter 150 Millisekunden.
- Akzeptabel: 150-500 Millisekunden.
- Schwach: 500-2.000 Millisekunden (unter 2 Sekunden).
- Schlecht: über 2.000 Millisekunden (ab 2 Sekunden).

Weitere Informationen zur Funktionsweise des Leistungsindikators für die Eingabeverzögerung finden Sie unter [Verwenden von Leistungsindikatoren für die Diagnose von Leistungsproblemen von Anwendungen auf Remotedesktop-Sitzungshosts](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

##  <a name="monthly-active-users-mau"></a>Monatlich aktive Benutzer (Monthly Active Users, MAU)

Die Gesamtanzahl der Benutzer, die in den letzten 28 Tagen eine Sitzung gestartet haben. Wenn Sie Daten für maximal 30 Tage speichern, kann es sein, dass Sie in Zeiträumen mit Daten für maximal 28 Tage niedrigere Werte für „Monatlich aktive Benutzer“ und „Verbindung“ sehen als erwartet.

## <a name="performance-counters"></a>Leistungsindikatoren

Leistungsindikatoren bieten Einblick in die Leistung von Hardwarekomponenten, Betriebssystemen und Anwendungen.

In der folgenden Tabelle sind die empfohlenen Leistungsindikatoren und Zeitintervalle aufgelistet, die von Azure Monitor für Azure Virtual Desktop verwendet werden:

|Name des Leistungsindikators|Zeitintervall|
|---|---|
|Logischer Datenträger (C:)\\Mittlere Warteschlangenlänge des Datenträgers|30 Sekunden|
|Logischer Datenträger (C:)\\Mittlere Datenträger s/übertragen|60 Sekunden|
|Logischer Datenträger (C:)\\Aktuelle Warteschlangenlänge|30 Sekunden|
|Arbeitsspeicher(\*)\\Verfügbare MB|30 Sekunden|
|Arbeitsspeicher(\*)\\Seitenfehler/Sek.|30 Sekunden|
|Arbeitsspeicher (\*)\\Seiten/Sek.|30 Sekunden|
|Arbeitsspeicher(\*)\\Zugesicherte verwendete Bytes (%)|30 Sekunden|
|Physischer Datenträger(\*)\\Durchschnittl. Warteschlangenlänge des Datenträgers|30 Sekunden|
|Physischer Datenträger(\*)\\Durchschnittl. Datenträger s/gelesen|30 Sekunden|
|Physischer Datenträger(\*)\\Durchschnittl. Datenträger s/übertragen|30 Sekunden|
|Physischer Datenträger(\*)\\Durchschnittl. Datenträger s/geschrieben|30 Sekunden|
|Prozessorinformationen(_Total)\\%Prozessorzeit|30 Sekunden|
|Terminaldienste(\*)\\Aktive Sitzungen|60 Sekunden|
|Terminaldienste(\*)\\Inaktive Sitzungen|60 Sekunden|
|Terminaldienste(\*)\\Sitzungen insgesamt|60 Sekunden|
|\*Benutzereingabeverzögerung pro Prozess(\*)\\Maximale Eingabeverzögerung|30 Sekunden|
|\*Benutzereingabeverzögerung pro Sitzung(\*)\\Maximale Eingabeverzögerung|30 Sekunden|
|RemoteFX-Netzwerk (\*)\\Aktuelle TCP-RTT|30 Sekunden|
|RemoteFX-Netzwerk (\*)\\Aktuelle UDP-Bandbreite|30 Sekunden|

Informationen zum Lesen von Leistungsindikatoren finden Sie unter [Konfigurieren von Leistungsindikatoren](../azure-monitor/agents/data-sources-performance-counters.md).

Informationen zu Leistungsindikatoren zur Eingabeverzögerung finden Sie unter [Leistungsindikatoren für Eingabeverzögerung](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

## <a name="potential-connectivity-issues"></a>Mögliche Konnektivitätsprobleme

In diesem Abschnitt werden die Hosts, Benutzer, veröffentliche Ressourcen und Clients mit einer hohen Verbindungsfehlerquote gezeigt. Wenn Sie den Filter „Bericht nach“ wählen, können Sie den Schweregrad des Problems beurteilen, indem Sie die Werte in diesen Spalten überprüfen:

- Versuche (Anzahl der Verbindungsversuche)
- Ressourcen (Anzahl der veröffentlichten Apps oder Desktops)
- Hosts (Anzahl der VMs)
- Clients

Wenn Sie z. B. den Filter **Nach Benutzer** auswählen, können Sie in der Spalte **Versuche** die Verbindungsversuche jedes Benutzers überprüfen.

Wenn Sie feststellen, dass sich ein Verbindungsproblem auf mehrere Hosts, Benutzer, Ressourcen oder Clients erstreckt, ist es wahrscheinlich, dass das Problem das gesamte System betrifft. Wenn dies nicht der Fall ist, handelt es sich um ein kleineres Problem mit niedrigerer Priorität.

Sie können auch Einträge auswählen, um weitere Informationen dazu anzuzeigen. Sie können anzeigen, welche Hosts, Ressourcen und Clientversionen in das Problem involviert waren. Die Anzeige enthält auch alle während der Verbindungsversuche gemeldeten Fehler.

## <a name="round-trip-time-rtt"></a>Paketumlaufzeit (RTT)

Die Round-Trip-Time (RTT) stellt eine Schätzung der Round-Trip-Zeit der Verbindung zwischen dem Standort des Endbenutzers und der Azure-Region des Sitzungshosts dar. Um festzustellen, welche Standorte die beste Latenz aufweisen, schlagen Sie Ihren gewünschten Standort im Tool [Qualitätsschätzer für Azure Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/assessment/) nach.

## <a name="session-history"></a>Sitzungsverlauf

Das Element **Sitzungen** zeigt den Status aller Sitzungen, ob verbunden oder getrennt. In **Leerlaufsitzungen** werden nur die getrennten Sitzungen gezeigt.

## <a name="severity-0-alerts"></a>Warnungen mit Schweregrad 0

Die dringendsten Angelegenheiten, um die Sie sich sofort kümmern müssen. Wenn Sie diese Probleme nicht angehen, könnten sie dazu führen, dass Ihre Azure Virtual Desktop-Bereitstellung nicht mehr funktioniert.

## <a name="time-to-connect"></a>Zeit für Verbindungsherstellung

Dies ist die Zeit zwischen dem Start der Sitzung eines Benutzers und dem Zeitpunkt, zu dem er als beim Dienst angemeldet gezählt wird. Das Einrichten neuer Verbindungen dauert in der Regel länger als das Wiederherstellen vorhandener Verbindungen.

## <a name="user-report"></a>Benutzerbericht

Auf dieser Seite können Sie den Verbindungsverlauf und die Diagnoseinformationen eines bestimmten Benutzers einsehen. Jeder Benutzerbericht zeigt Nutzungsmuster, Benutzerfeedback und Fehler, die in den Sitzungen der Benutzer aufgetreten sind. Die meisten kleineren Probleme können mithilfe von Benutzerfeedback gelöst werden. Wenn Sie genauer nachforschen müssen, können Sie auch Informationen zu einer bestimmten Verbindungs-ID oder einem bestimmten Zeitraum filtern.

## <a name="users-per-core"></a>Benutzer pro Kern

Dies ist die Anzahl der Benutzer pro VM-Kern. Die Nachverfolgung der maximalen Anzahl von Benutzern pro Kern im Zeitverlauf kann Ihnen helfen festzustellen, ob die Umgebung konstant mit einer hohen, niedrigen oder schwankenden Anzahl von Benutzern pro Kern betrieben wird. Wenn Sie wissen, wie viele Benutzer aktiv sind, können Sie die Ressourcen effizient nutzen und die Umgebung skalieren.

## <a name="windows-event-logs"></a>Windows-Ereignisprotokolle

Bei Windows-Ereignisprotokollen handelt es sich um Datenquellen, die von Log Analytics-Agents auf Windows-VMs gesammelt werden. Sie können Ereignisse sowohl aus Standardprotokollen wie „System“ und „Anwendung“ als auch aus benutzerdefinierten Protokollen sammeln, die von zu überwachenden Anwendungen erstellt wurden.

In der folgenden Tabelle sind die erforderlichen Windows-Event-Protokolle für Azure Monitor für Azure Virtual Desktop aufgeführt:

|Ereignisname|Ereignistyp|
|---|---|
|Anwendung|Fehler und Warnungen|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin|Fehler, Warnung und Information|
|Microsoft-Windows-TerminalServices-LocalSessionManager/Operational|Fehler, Warnung und Information|
|System|Fehler und Warnungen|
| Microsoft-FSLogix-Apps/Operational|Fehler, Warnung und Information|
|Microsoft-FSLogix-Apps/Admin|Fehler, Warnung und Information|

Weitere Informationen zu Windows-Ereignissen finden Sie unter [Eigenschaften der Windows-Event-Datensätze](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie zum Einstieg [Verwenden von Azure Monitor für Azure Virtual Desktop zum Überwachen Ihrer Bereitstellung](azure-monitor.md).
- Informationen dazu, wie Sie Ihre Datenspeicherkosten schätzen, messen und verwalten können, finden Sie unter [Schätzung der Azure Monitor-Kosten](azure-monitor-costs.md).
- Wenn ein Problem auftritt, finden Sie weitere Informationen im [Leitfaden zur Problembehebung](troubleshoot-azure-monitor.md).


Sie können auch Azure Advisor einrichten, um herauszufinden, wie gängige Probleme gelöst oder verhindert werden. Mehr erfahren Sie unter [Verwenden von Azure Advisor mit Azure Virtual Desktop](azure-advisor.md).

Wenn Sie Hilfe benötigen oder Fragen haben, konsultieren Sie unsere Communityressourcen:

- Stellen Sie in der [Tech Community zu Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) Fragen, oder machen Sie der Community Vorschläge.
   
- Informationen zum Hinterlassen von Feedback finden Sie unter [Problembehandlung: Übersicht, Feedback und Support für Azure Virtual Desktop](troubleshoot-set-up-overview.md#report-issues).

- Sie können Ihr Feedback bezüglich Azure Virtual Desktop auch im [Azure Virtual Desktop-Feedback-Hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) hochladen.
