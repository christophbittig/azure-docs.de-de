---
title: 'Problembehandlung in Azure Automation: Probleme mit Änderungsnachverfolgung und Bestand'
description: In diesem Artikel erfahren Sie, wie Sie Probleme mit dem Azure Automation-Feature für Änderungsnachverfolgung und Bestand beheben.
services: automation
ms.subservice: change-inventory-management
ms.date: 02/15/2021
ms.topic: troubleshooting
ms.openlocfilehash: 21699d306742c2a732155ac8df78608f5c3dd7ae
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346020"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Behandeln von Problemen mit Änderungsnachverfolgung und Bestand

In diesem Artikel wird beschrieben, wie Sie Probleme mit der Änderungsnachverfolgung und dem Bestand in Azure Automation beheben und lösen. Allgemeine Informationen zu Änderungsnachverfolgung und Bestand finden Sie unter [Übersicht über Änderungsnachverfolgung und Bestand](../change-tracking/overview.md).

## <a name="general-errors"></a>Allgemeine Fehler

### <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Szenario: Computer ist bereits bei einem anderen Konto registriert.

### <a name="issue"></a>Problem

Sie erhalten die folgende Fehlermeldung:

```error
Unable to Register Machine for Change Tracking, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Ursache

Der Computer wurde bereits in einem anderen Arbeitsbereich für die Änderungsnachverfolgung bereitgestellt.

### <a name="resolution"></a>Lösung

1. Vergewissern Sie sich, dass Ihr Computer Meldungen an den richtigen Arbeitsbereich zurückgibt. Anweisungen zum Überprüfen dieses Punkts finden Sie unter [Überprüfen der Agent-Konnektivität mit Azure Monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Stellen Sie außerdem sicher, dass dieser Arbeitsbereich mit Ihrem Azure Automation-Konto verknüpft ist. Rufen Sie dafür Ihr Automation-Konto auf, und wählen Sie unter **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

1. Stellen Sie sicher, dass die Computer im Log Analytics-Arbeitsbereich aufgeführt werden, der mit Ihrem Automation-Konto verknüpft ist. Führen Sie im Log Analytics-Arbeitsbereich die folgende Abfrage aus.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

   Wenn Ihr Computer nicht in den Abfrageergebnissen aufgeführt ist, wurde er in letzter Zeit nicht eingecheckt. Es liegt wahrscheinlich ein lokales Konfigurationsproblem vor. Sie sollten den Log Analytics-Agent neu installieren.

   Wenn Ihr Computer in den Abfrageergebnissen aufgeführt ist, überprüfen Sie, ob unter der Lösungen-Eigenschaft **Änderungsnachverfolgung** aufgeführt ist. Damit wird überprüft, ob er bei Änderungsnachverfolgung und Bestand registriert ist. Wenn dies nicht zutrifft, suchen Sie nach Bereichskonfigurationsproblemen. Die Bereichskonfiguration bestimmt, welche Computer für Änderungsnachverfolgung und Bestand konfiguriert werden. Informationen zum Konfigurieren der Bereichskonfiguration für den Zielcomputer finden Sie unter [Aktivieren von „Änderungsnachverfolgung und Bestand“ über ein Automation-Konto](../change-tracking/enable-from-automation-account.md).

   Führen Sie in Ihrem Arbeitsbereich diese Abfrage aus.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

1. Wenn das Ergebnis ```Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota``` angezeigt wird, wurde ein für Ihren Arbeitsbereich definiertes Kontingent erreicht, wodurch das Speichern von Daten verhindert wird. Wechseln Sie in Ihrem Arbeitsbereich zu **Verstehen von Nutzung und geschätzten Kosten**. Wählen Sie entweder einen neuen **Tarif** aus, der Ihnen ermöglicht, mehr Daten zu verwenden, oder klicken Sie auf **Tageslimit**, und entfernen Sie das Limit.

:::image type="content" source="./media/change-tracking/change-tracking-usage.png" alt-text="Nutzung und geschätzte Kosten" lightbox="./media/change-tracking/change-tracking-usage.png":::

Wird das Problem dadurch immer noch nicht behoben, führen Sie die Schritte unter [Bereitstellen eines Windows Hybrid Runbook Workers](../automation-windows-hrw-install.md) aus, um den Hybrid Worker für Windows neu zu installieren. Informationen zu Linux finden Sie unter [Bereitstellen eines Linux Hybrid Runbook Workers](../automation-linux-hrw-install.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Szenario: Datensätze von Änderungsnachverfolgung und Bestand werden für Windows-Computer nicht angezeigt

#### <a name="issue"></a>Problem

Es werden keine Ergebnisse für die Änderungsnachverfolgung oder den Bestand von Windows-Computern angezeigt, auf denen dieses Feature aktiviert wurde.

#### <a name="cause"></a>Ursache

Dieser Fehler kann folgende Ursachen haben:

* Der Azure Log Analytics-Agent für Windows wird nicht ausgeführt.
* Die Kommunikation an das Automation-Konto wird blockiert.
* Die Management Packs für Änderungsnachverfolgung und Bestand wurden nicht heruntergeladen.
* Die aktivierte VM stammt möglicherweise von einem geklonten Computer, der nicht mit der Systemvorbereitung (Sysprep) mit installiertem Log Analytics-Agent für Windows vorbereitet wurde.

#### <a name="resolution"></a>Lösung

Wechseln Sie auf dem Computer mit dem Log Analytics-Agent zu **C:\Programme\Microsoft Monitoring Agent\Agent\Tools**, und führen Sie die folgenden Befehle aus:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Wenn Sie weitere Hilfe benötigen, können Sie Diagnoseinformationen sammeln und sich an den Support wenden.

> [!NOTE]
> Der Log Analytics-Agent aktiviert standardmäßig die Fehlerablaufverfolgung. Um ausführliche Fehlermeldungen wie im vorherigen Beispiel zu aktivieren, verwenden Sie den Parameter `VER`. Für Ablaufverfolgungen zur Information verwenden Sie `INF` beim Aufrufen von `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Der Log Analytics-Agent für Windows wird nicht ausgeführt.

Vergewissern Sie sich, dass der Log Analytics-Agent (**HealthService.exe**) auf dem Computer ausgeführt wird.

##### <a name="communication-to-automation-account-blocked"></a>Kommunikation mit dem Automation-Konto blockiert

Überprüfen Sie die Ereignisanzeige auf dem Computer, und suchen Sie nach allen Ereignisse, die das Wort `changetracking` enthalten.

Welche Adressen und Ports zugelassen werden müssen, damit Änderungsnachverfolgung und Bestand funktionieren, erfahren Sie im Abschnitt [Netzwerkplanung](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Management Packs nicht heruntergeladen

Überprüfen Sie, ob die Management Packs für die Änderungsnachverfolgung und den Bestand lokal installiert wurden:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM von geklontem Computer, der nicht mit Sysprep vorbereitet wurde

Wenn Sie ein geklontes Image verwenden, bereiten Sie das Image zunächst mit Sysprep vor, und installieren Sie dann den Log Analytics-Agent für Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Szenario: Keine Ergebnisse für Änderungsnachverfolgung und Bestand auf Linux-Computern

#### <a name="issue"></a>Problem

Es werden keine Ergebnisse für Änderungsnachverfolgung und Bestand für Linux-Computer angezeigt, die für das Feature aktiviert sind. 

#### <a name="cause"></a>Ursache
Im Folgenden sind mögliche Ursachen für dieses Problem aufgeführt:
* Der Log Analytics-Agent für Linux wird nicht ausgeführt.
* Der Log Analytics-Agent für Linux wurde nicht ordnungsgemäß konfiguriert.
* Es bestehen Konflikte mit der Überwachung der Dateiintegrität (File Integrity Monitoring, FIM).

#### <a name="resolution"></a>Lösung 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics-Agent für Linux nicht ausgeführt

Überprüfen Sie, ob der Daemon für den Log Analytics-Agent für Linux (**omsagent**) auf dem Computer ausgeführt wird. Führen Sie in dem Log Analytics-Arbeitsbereich, der mit Ihrem Automation-Konto verknüpft ist, die folgende Abfrage aus.

```loganalytics
Copy
Heartbeat
| summarize by Computer, Solutions
```

Wenn Ihr Computer nicht in den Abfrageergebnissen aufgeführt ist, wurde er in letzter Zeit nicht eingecheckt. Es liegt wahrscheinlich ein Problem mit der lokalen Konfiguration vor, und Sie sollten den Agent neu installieren. Weitere Informationen zur Installation und Konfiguration finden Sie unter [Sammeln von Protokolldaten mit dem Log Analytics-Agent](../../azure-monitor/agents/log-analytics-agent.md).

Wenn Ihr Computer in den Abfrageergebnissen angezeigt wird, überprüfen Sie die Bereichskonfiguration. Weitere Informationen finden Sie unter [Zielgruppenadressierung für Überwachungslösungen in Azure Monitor](../../azure-monitor/insights/solution-targeting.md).

Weitere Informationen zum Troubleshooting für dieses Problem finden Sie unter [Problem: Es werden keine Linux-Daten angezeigt](../../azure-monitor/agents/agent-linux-troubleshoot.md#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics-Agent für Linux nicht ordnungsgemäß konfiguriert

Der Log Analytics-Agent für Linux wurde möglicherweise nicht ordnungsgemäß für die Sammlung der Protokolle und Befehlszeilenausgaben mit dem Tool OMS Log Collector konfiguriert. Siehe [Übersicht über Änderungsnachverfolgung und Bestand](../change-tracking/overview.md).

##### <a name="fim-conflicts"></a>FIM-Konflikte

Die FIM-Funktion von Microsoft Defender für Cloud könnte dann die Integrität Ihrer Linux-Dateien nicht ordnungsgemäß validieren. Vergewissern Sie sich, dass FIM betriebsbereit und ordnungsgemäß für die Linux-Dateiüberwachung konfiguriert ist. Siehe [Übersicht über Änderungsnachverfolgung und Bestand](../change-tracking/overview.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem hier nicht aufgeführt wird, oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Stellen Sie eine Verbindung mit [@AzureSupport](https://twitter.com/azuresupport) her, dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Der Azure-Support verbindet die Azure-Community mit Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.
