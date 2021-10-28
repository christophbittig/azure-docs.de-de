---
title: Behandeln von Problemen mit dem Log Analytics-Agent für Windows
description: Beschreibt die Symptome, Ursachen und Lösungen für die häufigsten Probleme mit dem Log Analytics-Agent für Windows in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2021
ms.openlocfilehash: 691bc47fbeeedfb5a2256520552ccb4f81f642cf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262563"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Behandeln von Problemen mit dem Log Analytics-Agent für Windows 

Dieser Artikel bietet Unterstützung bei der Problembehandlung von Fehlern, die beim Log Analytics-Agent für Windows in Azure Monitor auftreten können, und enthält mögliche Lösungen zum Beheben dieser Fehler.


## <a name="log-analytics-troubleshooting-tool"></a>Log Analytics-Problembehandlungstool

Das Problembehandlungstool für den Log Analytics-Agent für Windows ist eine Sammlung von PowerShell-Skripts für die Ermittlung und Diagnose von Problemen mit dem Log Analytics-Agent. Es ist bei der Installation des Agents automatisch enthalten. Das Ausführen des Tools sollte der erste Schritt bei der Diagnose eines Problems sein.

### <a name="how-to-use"></a>Verwendung
1. Öffnen Sie die PowerShell-Eingabeaufforderung als Administrator auf dem Computer, auf dem der Log Analytics-Agent installiert ist.
1. Navigieren Sie zu dem Verzeichnis, in dem sich das Tool befindet.
   * `cd "C:\Program Files\Microsoft Monitoring Agent\Agent\Troubleshooter"`
1. Führen Sie das Hauptskript mit diesem Befehl aus:
   * `.\GetAgentInfo.ps1`
1. Wählen Sie ein Problembehandlungsszenario aus.
1. Befolgen Sie die Anweisungen an der Konsole. (Hinweis: Für die Schritte mit den Ablaufverfolgungsprotokollen ist ein manueller Eingriff erforderlich, um die Protokollerfassung zu unterbrechen. Warten Sie je nach Reproduzierbarkeit des Problems die entsprechende Zeit ab, und drücken Sie „s“, um die Protokollerfassung anzuhalten und mit dem nächsten Schritt fortzufahren).

   Die Speicherorte der Ergebnisdatei werden beim Abschluss protokolliert und in einem daraufhin geöffneten neuen Explorer-Fenster hervorgehoben.

### <a name="installation"></a>Installation
Das Problembehandlungstool ist bei der Installation des Log Analytics-Agents ab Build 10.20.18053.0 automatisch enthalten.

### <a name="scenarios-covered"></a>Behandelte Szenarien
Es folgt eine Liste von Szenarien, die vom Problembehandlungstool geprüft werden:

- Agent meldet keine Daten oder fehlende Heartbeatdaten.
- Fehler bei der Agent-Erweiterungsbereitstellung
- Agent stürzt ab.
- Agent verbraucht viel CPU/Arbeitsspeicher.
- Fehler bei der Installation/Deinstallation
- Problem mit benutzerdefinierten Protokollen
- Problem mit OMS-Gateway
- Problem mit Leistungsindikatoren
- Sammeln aller Protokolle

>[!NOTE]
>Führen Sie das Problembehandlungstool aus, wenn ein Problem auftritt. Wenn Sie ein Ticket erstellen, kann unser Supportteam mithilfe der anfänglichen Protokolle das Problem schneller beheben.

## <a name="important-troubleshooting-sources"></a>Wichtige Quellen für die Problembehandlung

 Als Unterstützung bei der Behandlung von Problemen im Zusammenhang mit Log Analytics-Agent für Windows protokolliert der Agent Ereignisse im Windows-Ereignisprotokoll, insbesondere unter *Anwendung und Dienste\Operations Manager*.  

## <a name="connectivity-issues"></a>Konnektivitätsprobleme

Wenn der Agent über einen Proxyserver oder eine Firewall kommuniziert, bestehen möglicherweise Einschränkungen, die eine Kommunikation vom Quellcomputer und dem Azure Monitor-Dienst verhindern. Sollte die Kommunikation aufgrund einer Fehlkonfiguration blockiert sein, ist die Registrierung bei einem Arbeitsbereich möglicherweise nicht erfolgreich, wenn versucht wird, den Agent zu installieren oder ihn nach der Einrichtung so zu konfigurieren, dass Meldungen an einen zusätzlichen Arbeitsbereich übermittelt werden. Bei der Agent-Kommunikation können nach erfolgreicher Registrierung Fehler auftreten. In diesem Abschnitt werden die Methoden zur Behandlung dieser Art von Problemen beim Windows-Agent beschrieben.

Vergewissern Sie sich, dass die Firewall oder der Proxy so konfiguriert ist, dass die in der folgenden Tabelle beschriebenen Ports und URLs zulässig sind. Stellen Sie außerdem sicher, dass die HTTP-Überprüfung für den Webdatenverkehr nicht aktiviert ist, da sie einen sicheren TLS-Kanal zwischen dem Agent und Azure Monitor verhindern kann.  

|Agent-Ressource|Ports |Direction |Umgehung der HTTPS-Überprüfung|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Port 443 |Ausgehend|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Ausgehend|Ja |  
|*.blob.core.windows.net |Port 443 |Ausgehend|Ja |  
|*.agentsvc.azure-automation.net |Port 443 |Ausgehend|Ja |  

Informationen zur Firewall, die für Azure Government erforderlich sind, finden Sie unter [Azure Government-Verwaltung](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). Wenn Sie den Azure Automation Hybrid Runbook Worker zum Herstellen einer Verbindung mit dem Automatisierungsdienst bzw. die Registrierung bei diesem nutzen möchten, um Runbooks oder Verwaltungslösungen in Ihrer Umgebung zu verwenden, muss dieser Zugriff auf die Portnummer und die unter [Konfigurieren Ihres Netzwerks für den Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning) beschriebenen URLs besitzen. 

Es gibt mehrere Möglichkeiten zur Überprüfung, ob der Agent erfolgreich mit Azure Monitor kommuniziert.

- Aktivieren Sie die [Integritätsbewertung des Azure Log Analytics-Agents](../insights/solution-agenthealth.md) im Arbeitsbereich. Sehen Sie sich im Dashboard für die Agent-Integritätsdiagnose die Spalte **Anzahl der nicht reagierenden Agents** an, um schnell festzustellen, ob der Agent aufgeführt ist.  

- Führen Sie die folgende Abfrage aus, um sicherzustellen, dass der Agent einen Heartbeat an den Arbeitsbereich sendet, an den er gemäß der Konfiguration berichten soll. Ersetzen Sie `<ComputerName>` durch den tatsächlichen Namen des Computers.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Wenn der Computer erfolgreich mit dem Dienst kommuniziert, sollte die Abfrage ein Ergebnis zurückgeben. Gibt die Abfrage kein Ergebnis zurück, stellen Sie zunächst sicher, dass der Agent für das Berichten an den richtigen Arbeitsbereich konfiguriert ist. Ist er richtig konfiguriert, fahren Sie mit Schritt 3 fort, und durchsuchen Sie das Windows-Ereignisprotokoll, um festzustellen, ob der Agent protokolliert, durch welches Problem die Kommunikation mit Azure Monitor möglicherweise verhindert wird.

- Eine weitere Methode zur Erkennung eines Konnektivitätsproblems ist das Ausführen des **TestCloudConnectivity**-Tools. Das Tool wird standardmäßig mit dem Agent im Ordner *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent* installiert. Navigieren Sie an einer Eingabeaufforderung mit erhöhten Rechten zum Ordner, und führen Sie das Tool aus. Das Tool gibt die Ergebnisse zurück und hebt hervor, wo der Test fehlschlug (z.B. in Bezug auf einen bestimmten Port oder eine URL, der bzw. die blockiert war). 

    ![Ausführungsergebnisse des TestCloudConnection-Tools](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtern Sie das *Operations Manager*-Ereignisprotokoll nach **Ereignisquellen** - *Integritätsdienstmodule*, *Integritätsdienst* und *Dienstconnector*, und filtern Sie nach der **Ereignisebene** *Warnung* und *Fehler*, um zu überprüfen, ob Ereignisse geschrieben wurden, die in der folgenden Tabelle aufgeführt sind. Wenn dies der Fall ist, sehen Sie sich die Lösungsschritte für jedes mögliche Ereignis an.

    |Ereignis-ID |`Source` |Beschreibung |Lösung |
    |---------|-------|------------|-----------|
    |2133 und 2129 |Integritätsdienst |Fehler bei der Verbindung des Agents mit dem Dienst |Dieser Fehler kann auftreten, wenn der Agent nicht direkt oder über eine Firewall/einen Proxyserver mit dem Azure Monitor-Dienst kommunizieren kann. Überprüfen Sie die Proxyeinstellungen des Agents, und stellen Sie sicher, dass die Netzwerkfirewall/der Netzwerkproxy den TCP-Datenverkehr vom Computer zum Dienst zulässt.|
    |2138 |Integritätsdienstmodule |Proxy erfordert Authentifizierung |Konfigurieren Sie die Proxyeinstellungen des Agents, und geben Sie den erforderlichen Benutzernamen und das Kennwort für die Authentifizierung beim Proxyserver an. |
    |2129 |Integritätsdienstmodule |Fehler bei der Verbindung/Fehler bei der TLS-Aushandlung |Überprüfen Sie die TCP/IP-Einstellungen des Netzwerkadapters und die Proxyeinstellungen des Agents.|
    |2127 |Integritätsdienstmodule |Fehler beim Senden von Daten mit Empfang eines Fehlercodes |Wenn dies im Laufe des Tages nur zeitweilig auftritt, kann es sich einfach um eine zufällige Anomalie handeln, die ignoriert werden kann. Überwachen Sie dieses Verhalten, um festzustellen, wie häufig dieser Fehler auftritt. Tritt er im Laufe des Tages häufig auf, überprüfen Sie zunächst Ihre Netzwerkkonfiguration und die Proxyeinstellungen. Wenn die Beschreibung HTTP-Fehlercode 404 enthält und der Agent zum ersten Mal versucht, Daten an den Dienst zu senden, ist ein Fehlercode 500 mit einem internen Fehlercode 404 enthalten. 404 bedeutet „nicht gefunden“ und gibt an, dass der Speicherbereich für den neuen Arbeitsbereich weiterhin bereitgestellt wird. Beim nächsten Versuch werden Daten wie erwartet erfolgreich in den Arbeitsbereich geschrieben. Ein HTTP-Fehler 403 weist möglicherweise auf ein Problem mit Berechtigungen oder Anmeldeinformationen hin. Der Fehler 403 enthält weitere Informationen als Hilfe zur Problembehandlung.|
    |4000 |Dienstconnector |Fehler beim Auflösen des DNS-Namens |Der Computer konnte die Internetadresse, die beim Senden von Daten an den Dienst verwendet wurde, nicht auflösen. Dies kann an den Einstellungen der DNS-Auflösung auf Ihrem Computer, falschen Proxyeinstellungen oder an einem vorübergehenden DNS-Problem bei Ihrem Anbieter liegen. Wenn dieses Problem zeitweilig auftritt, kann es durch ein vorübergehendes Netzwerkproblem verursacht werden.|
    |4001 |Dienstconnector |Fehler bei der Verbindung mit dem Dienst |Dieser Fehler kann auftreten, wenn der Agent nicht direkt oder über eine Firewall/einen Proxyserver mit dem Azure Monitor-Dienst kommunizieren kann. Überprüfen Sie die Proxyeinstellungen des Agents, und stellen Sie sicher, dass die Netzwerkfirewall/der Netzwerkproxy den TCP-Datenverkehr vom Computer zum Dienst zulässt.|
    |4002 |Dienstconnector |Der Dienst hat HTTP-Statuscode 403 als Antwort auf eine Abfrage zurückgegeben. Wenden Sie sich an den Dienstadministrator, um die Integrität des Diensts zu prüfen. Die Abfrage wird später wiederholt. |Dieser Fehler wird während der anfänglichen Registrierungsphase des Agents geschrieben, und Sie sehen eine URL, die der folgenden ähnelt: *https://\<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Ein Fehlercode 403 bedeutet „verboten“ und kann durch eine falsch geschriebene Arbeitsbereichs-ID oder einen falsch geschriebenen Arbeitsbereichsschlüssel verursacht werden oder wenn Datum und Uhrzeit auf dem Computer falsch sind. Wenn die Zeit um +/-15 Minuten von der aktuellen Uhrzeit abweicht ist, tritt bei der Integration ein Fehler auf. Aktualisieren Sie zur Behebung dieses Problems das Datum und/oder die Zeitzone des Windows-Computers.|

## <a name="data-collection-issues"></a>Datensammlungsprobleme

Nachdem der Agent installiert wurde und Berichte an den konfigurierten Arbeitsbereich bzw. die Arbeitsbereiche sendet, kann möglicherweise das Empfangen der Konfiguration sowie das Sammeln oder Weiterleiten von Leistung, Protokollen oder anderen Daten an den Dienst (je nachdem, was aktiviert ist und den Computer als Ziel hat) beendet werden. Sie müssen Folgendes ermitteln:

- Handelt es sich um einen bestimmten Datentyp oder sind keinerlei Daten im Arbeitsbereich verfügbar?
- Wird der Datentyp von einer Lösung oder als Teil der Datensammlungskonfiguration des Arbeitsbereichs angegeben?
- Wie viele Computer sind betroffen? Senden ein oder mehrere Computer Berichte an den Arbeitsbereich?
- War er aktiv und hat er zu einem bestimmten Zeitpunkt des Tages aufgehört, oder wurden noch nie Daten gesammelt? 
- Weist die verwendete Protokollsuchabfrage die korrekte Syntax auf? 
- Hat der Agent jemals seine Konfiguration von Azure Monitor empfangen?

Als erster Schritt bei der Problembehandlung muss festgestellt werden, ob der Computer ein Heartbeat-Ereignis sendet.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Wenn die Abfrage Ergebnisse zurückgibt, müssen Sie feststellen, ob ein bestimmter Datentyp nicht gesammelt und an den Dienst weitergeleitet wird. Der Grund dafür kann sein, dass der Agent keine aktualisierte Konfiguration vom Dienst empfängt, oder es kann ein anderes Symptom vorliegen, das einen normalen Betrieb des Agents verhindert. Führen Sie zur weiteren Problembehandlung die folgenden Schritte aus.

1. Öffnen Sie auf dem Computer eine Eingabeaufforderung mit erhöhten Rechten, und starten Sie den Agent-Dienst durch Eingabe von `net stop healthservice && net start healthservice` neu.
2. Öffnen Sie das *Operations Manager*-Ereignisprotokoll, und suchen Sie nach den **Ereignis-IDs** *7023, 7024, 7025, 7028* und *1210* aus der **Ereignisquelle** *Integritätsdienst*.  Diese Ereignisse geben an, dass der Agent die Konfiguration von Azure Monitor erfolgreich empfängt und der Computer aktiv überwacht wird. In der Beschreibung für Ereignis-ID 1210 werden außerdem in der letzten Zeile alle Lösungen und Insights angegeben, die im Umfang der Überwachung auf dem Agent enthalten sind.  

    ![Beschreibung für Ereignis-ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Wenn nach einigen Minuten nicht die erwarteten Daten in den Abfrageergebnissen oder der Visualisierung angezeigt werden (je nachdem, ob Sie die Daten aus einer Lösung oder Insight anzeigen), suchen Sie im *Operations Manager*-Ereignisprotokoll nach den **Ereignisquellen** *Integritätsdienst* und *Integritätsdienstmodule*, und filtern Sie nach der **Ereignisebene** *Warnung* und *Fehler*, um zu überprüfen, ob Ereignisse geschrieben wurden, die in der folgenden Tabelle aufgeführt sind.

    |Ereignis-ID |`Source` |Beschreibung |Lösung |
    |---------|-------|------------|
    |8.000 |Integritätsdienst |Dieses Ereignis gibt an, ob ein Workflow im Zusammenhang mit der Leistung, ein Ereignis oder ein anderer gesammelter Datentyp nicht zur Erfassung im Arbeitsbereich an den Dienst weitergeleitet werden kann. | Ereignis-ID 2136 aus der Integritätsdienstquelle wird zusammen mit diesem Ereignis geschrieben und kann angeben, dass der Agent nicht mit dem Dienst kommunizieren kann. Der Grund dafür ist möglicherweise eine falsche Konfiguration der Proxy- und Authentifizierungseinstellungen, ein Netzwerkausfall oder dass die Netzwerkfirewall/der Netzwerkproxy keinen TCP-Datenverkehr vom Computer zum Dienst zulässt.| 
    |10102 und 10103 |Integritätsdienstmodule |Workflow konnte Datenquelle nicht auflösen. |Dies kann auftreten, wenn der angegebene Leistungsindikator oder die Instanz auf dem Computer nicht vorhanden oder in den Einstellungen des Arbeitsbereichs falsch definiert ist. Wenn es sich um einen vom Benutzer angegebenen [Leistungsindikator](data-sources-performance-counters.md#configuring-performance-counters) handelt, überprüfen Sie, ob die angegebenen Informationen das richtige Format aufweisen und auf den Zielcomputern vorhanden sind. |
    |26002 |Integritätsdienstmodule |Workflow konnte Datenquelle nicht auflösen. |Dies kann auftreten, wenn das angegebene Windows-Ereignisprotokoll auf dem Computer nicht vorhanden ist. Dieser Fehler kann ignoriert werden, wenn nicht erwartet wird, dass auf dem Computer dieses Ereignisprotokoll registriert ist. Andernfalls überprüfen Sie bei einem vom Benutzer angegebenen [Ereignisprotokoll](data-sources-windows-events.md#configuring-windows-event-logs), ob die angegebenen Informationen korrekt sind. |
