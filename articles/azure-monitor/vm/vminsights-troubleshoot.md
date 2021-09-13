---
title: Problembehandlung für VM Insights
description: Beheben Sie Probleme bei der Installation von VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: a7bcce4d18f6bcfe299a31dae3eb036bef12da9b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346117"
---
# <a name="troubleshoot-vm-insights"></a>Problembehandlung für VM Insights
Dieser Artikel enthält Informationen zur Problembehandlung bei der Aktivierung und Verwendung von VM Insights.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>VM Insights kann auf einem Computer nicht aktiviert werden
Beim Onboarding einer Azure-VM über das Azure-Portal werden die folgenden Schritte ausgeführt:

- Es wird ein Log Analytics-Standardarbeitsbereich erstellt, wenn diese Option ausgewählt war.
- Der Log Analytics-Agent wird mithilfe einer VM-Erweiterung auf Azure-VMs installiert, wenn der Agent bereits installiert ist.
- Der Dependency-Agent wird mithilfe einer Erweiterung auf Azure-VMs installiert, wenn festgestellt wird, dass er erforderlich ist.
  
Während des Onboardingprozesses wird jeder dieser Schritte überprüft und ein Benachrichtigungsstatus im Portal angezeigt. Die Konfiguration des Arbeitsbereichs und die Agentinstallation nehmen normalerweise 5 bis 10 Minuten in Anspruch. Es dauert weitere 5 bis 10 Minuten, bis die Daten im Portal angezeigt werden.

Wenn Sie nach dem Onboardingprozess eine Meldung erhalten, dass für die VM ein Onboarding durchgeführt werden muss, warten Sie bis zu 30 Minuten, bis der Vorgang abgeschlossen ist. Wenn das Problem weiterhin besteht, suchen Sie in den folgenden Abschnitten nach möglichen Ursachen.

### <a name="is-the-virtual-machine-running"></a>Wird die VM ausgeführt?
 Falls die VM über längere Zeit ausgeschaltet war, derzeit ausgeschaltet ist oder erst vor Kurzem eingeschaltet wurde, kann es etwas dauern, bis Daten eingehen und angezeigt werden können.

### <a name="is-the-operating-system-supported"></a>Wird das Betriebssystem unterstützt?
Wenn das Betriebssystem nicht in der [Liste mit den unterstützten Betriebssystemen](vminsights-enable-overview.md#supported-operating-systems) aufgeführt ist, kann die Erweiterung nicht installiert werden, und es wird eine Meldung angezeigt, dass auf den Empfang der Daten gewartet wird.

### <a name="did-the-extension-install-properly"></a>Wurde die Erweiterung ordnungsgemäß installiert?
Wenn weiterhin eine Meldung angezeigt wird, dass ein Onboarding der VM durchgeführt werden muss, kann dies bedeuten, dass eine oder beide Erweiterungen nicht ordnungsgemäß installiert wurden. Überprüfen Sie die Seite **Erweiterungen** der VM im Azure-Portal, um sicherzustellen, dass die folgenden Erweiterungen aufgeführt sind:

| Betriebssystem | Agents | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft.Azure.Monitoring.DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentLinux |

Wenn beide Erweiterungen für Ihr Betriebssystem in der Liste mit den installierten Erweiterungen nicht angezeigt werden, müssen sie installiert werden. Wenn die Erweiterungen aufgeführt werden, als Status jedoch nicht *Bereitstellung erfolgreich* angezeigt wird, sollte die Erweiterung entfernt und neu installiert werden.

### <a name="do-you-have-connectivity-issues"></a>Liegen Verbindungsprobleme vor?
Bei Windows-Computern können Sie das Tool *TestCloudConnectivity* verwenden, um Verbindungsprobleme zu identifizieren. Dieses Tool wird standardmäßig mit dem Agent im Ordner *%SystemDrive%\Programme\Microsoft Monitoring Agent\Agent* installiert. Führen Sie das Tool an einer Eingabeaufforderung mit erhöhten Rechten aus. Es werden Ergebnisse zurückgegeben, in denen hervorgehoben ist, wo der Test fehlschlägt. 

![TestCloudConnectivity-Tool](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>Weitere Informationen zur Problembehandlung für den Agent

In den folgenden Artikeln finden Sie Informationen zur Behebung von Problemen mit dem Log Analytics-Agent:

- [Behandeln von Problemen mit dem Log Analytics-Agent für Windows](../agents/agent-windows-troubleshoot.md)
- [Behandeln von Problemen mit dem Log Analytics-Agent für Linux](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>Leistungsansicht enthält keine Daten
Wenn die Agents anscheinend ordnungsgemäß installiert wurden, in der Leistungsansicht jedoch keine Daten angezeigt werden, suchen Sie in den folgenden Abschnitten nach möglichen Ursachen.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Hat der Log Analytics-Arbeitsbereich das Datenlimit erreicht?
Überprüfen Sie die [Kapazitätsreservierungen und die Preise für die Datenerfassung](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="is-your-virtual-machine-agent-connected-to-azure-monitor-logs"></a>Ist Ihr VM-Agent mit Azure Monitor Logs verbunden?

Öffnen Sie Log Analytics im Azure-Portal über **Protokolle** im Menü „Azure Monitor“. Führen Sie die folgende Abfrage für Ihren Computer aus:

```kuso
Heartbeat
| where Computer == "my-computer"
| sort by TimeGenerated desc 
```

Wenn keine Daten angezeigt werden oder der Computer kürzlich keinen Heartbeat gesendet hat, liegt möglicherweise ein Problem mit Ihrem Agent vor. Weitere Informationen finden Sie im Abschnitt zur Problembehandlung für die Agents weiter oben.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>VM wird nicht in der Kartenansicht angezeigt

### <a name="is-the-dependency-agent-installed"></a>Ist der Dependency-Agent installiert?
 Verwenden Sie die Informationen in den Abschnitten oben, um zu bestimmen, ob der Dependency-Agent installiert ist und ordnungsgemäß funktioniert.

### <a name="are-you-on-the-log-analytics-free-tier"></a>Haben Sie den Log Analytics-Tarif „Free“?
Der [Log Analytics-Tarif „Free“](https://azure.microsoft.com/pricing/details/monitor/) ist ein älterer Tarif, der bis zu fünf einzelne Dienstzuordnungscomputer erlaubt. Alle weiteren Computer werden in der Dienstzuordnung nicht angezeigt, selbst wenn die vorherigen fünf keine Daten mehr senden.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>Sendet die VM Protokoll- und Leistungsdaten an Azure Monitor-Protokolle?
Verwenden Sie die Protokollabfrage im Abschnitt [Leistungsansicht enthält keine Daten](#performance-view-has-no-data), um zu ermitteln, ob Daten für die VM gesammelt werden. Wenn keine Daten gesammelt werden, verwenden Sie das oben beschriebene TestCloudConnectivity-Tool, um zu ermitteln, ob Verbindungsprobleme vorliegen.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>VM wird in der Kartenansicht angezeigt, es fehlen jedoch Daten
Wenn die VM in der Kartenansicht vorhanden ist, ist der Dependency-Agent installiert und aktiv, der Kerneltreiber wurde jedoch nicht geladen. Überprüfen Sie die Protokolldatei an den folgenden Speicherorten:

| Betriebssystem | Log | 
|:---|:---|
| Windows | C:\Programme\Microsoft Dependency Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

Die letzten Zeilen der Datei sollten den Grund angeben, warum der Kernel nicht geladen wurde. Beispielsweise, weil der Kernel nicht unterstützt wird, was unter Linux nach der Aktualisierung des Kernels auftreten kann.
## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Onboarding von VM Insights-Agents finden Sie unter [Übersicht zum Aktivieren von VM Insights](vminsights-enable-overview.md).
