---
title: Übersicht zum Aktivieren von VM Insights
description: In diesem Artikel erfahren Sie, wie Sie VM Insights bereitstellen und konfigurieren. Informieren Sie sich über die Systemanforderungen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: 1a4c872e5df50e2874053ad460cb50ecfabfeed9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713378"
---
# <a name="enable-vm-insights-overview"></a>Übersicht zum Aktivieren von VM Insights

Dieser Artikel bietet eine Übersicht über die verfügbaren Optionen zum Aktivieren von VM Insights, um die Integrität und Leistung folgender Elemente zu überwachen:

- Virtuelle Azure-Computer 
- Azure-VM-Skalierungsgruppen
- Hybrid-VMs, die mit Azure Arc verbunden sind
- Lokale virtuelle Computer
- In einer anderen Cloudumgebung gehostete virtuelle Computer  

So richten Sie VM Insights ein:

* Aktivieren Sie einen einzelnen virtuellen Azure-Computer, eine einzelne Azure-VM-Skalierungsgruppe oder einen einzelnen Azure Arc-Computer, indem Sie direkt im entsprechenden Menü des Azure-Portals die Option **Insights** auswählen.
* Aktivieren Sie mehrere virtuelle Azure-Computer, Azure-VM-Skalierungsgruppen oder Azure Arc-Computer mithilfe von Azure Policy. Diese Methode stellt sicher, dass bei bestehenden und neuen virtuellen Computern und Skalierungsgruppen die erforderlichen Abhängigkeiten installiert und ordnungsgemäß konfiguriert werden. Nicht konforme virtuelle Computer und Skalierungsgruppen werden gemeldet, sodass Sie entscheiden können, ob Sie sie aktivieren und den Zustand korrigieren möchten.
* Aktivieren Sie mehrere virtuelle Azure-Computer, virtuelle Azure Arc-Computer, Azure-VM-Skalierungsgruppen oder Azure Arc-Computer innerhalb eines angegebenen Abonnements oder einer angegebenen Ressourcengruppe mithilfe von PowerShell.
* Aktivieren Sie VM Insights für die Überwachung von VMs oder physischen Computern, die in Ihrem Unternehmensnetzwerk oder einer anderen Cloudumgebung gehostet werden.

## <a name="supported-machines"></a>Unterstützte Computer
VM Insights unterstützt die folgenden Computer:

- Virtueller Azure-Computer
- Azure-VM-Skalierungsgruppe
- Hybrid-VM, die mit Azure Arc verbunden ist

> [!IMPORTANT]
> Wenn der Name des Ethernet-Geräts für Ihre VM mehr als neun Zeichen enthält, wird es von VM Insights nicht erkannt, und es werden keine Daten an die InsightsMetrics-Tabelle gesendet. Der Agent sammelt Daten aus [anderen Quellen](../agents/agent-data-sources.md).

## <a name="supported-azure-arc-machines"></a>Unterstützte Azure Arc-Computer
VM Insights steht für Azure Arc-fähige Server in Regionen zur Verfügung, in denen der Arc-Erweiterungsdienst verfügbar ist. Sie müssen Version 0.9 oder höher des Arc-Agents ausführen.

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
|:--|:--|:--|
| Windows-Agents | Ja | Zusätzlich zum [Log Analytics-Agent für Windows](../agents/log-analytics-agent.md) benötigen Windows-Agents den Dependency-Agent. Weitere Informationen finden Sie unter [Unterstützte Betriebssysteme](../agents/agents-overview.md#supported-operating-systems). |
| Linux-Agents | Ja | Zusätzlich zum [Log Analytics-Agent für Linux](../agents/log-analytics-agent.md) benötigen Linux-Agents den Dependency-Agent. Weitere Informationen finden Sie unter [Unterstützte Betriebssysteme](#supported-operating-systems). |
| System Center Operations Manager-Verwaltungsgruppe | Nein | |

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

VM Insights unterstützt alle Betriebssysteme, die den Log Analytics-Agent und den Dependency-Agent unterstützen. Eine vollständige Liste finden Sie unter [Übersicht über Azure Monitor-Agents](../agents/agents-overview.md#supported-operating-systems).

> [!IMPORTANT]
> Das Feature „Gastintegrität“ von VM Insights weist eine eingeschränktere Betriebssystemunterstützung auf, während es sich in der öffentlichen Vorschau befindet. Unter [Aktivieren des Features „Gastintegrität“ von Azure Monitor für VMs (Vorschau)](../vm/vminsights-health-enable.md) finden Sie eine detaillierte Liste.

### <a name="linux-considerations"></a>Überlegungen zu Linux
In der folgenden Liste finden Sie Überlegungen zur Linux-Unterstützung des Dependency-Agents, der VM Insights unterstützt:

- Es werden nur die Standardversion und SMP-Version des Linux-Kernels unterstützt.
- Nicht-Standardversionen des Kernels, z. B. PAE (Physical Address Extension) und Xen, werden für keine Linux-Distribution unterstützt. Beispielsweise wird ein System mit der Versionszeichenfolge *2.6.16.21-0.8-xen* nicht unterstützt.
- Benutzerdefinierte Kernels, einschließlich Neukompilierungen von Standardkernels, werden nicht unterstützt.
- Für andere Debian-Distributionen als Version 9.4 wird das Zuordnungsfeature nicht unterstützt, und das Leistungsfeature ist nur über das Azure Monitor-Menü verfügbar. Es ist nicht direkt über den linken Bereich des virtuellen Azure-Computers verfügbar.
- Der CentOSPlus-Kernel wird unterstützt.

Der Linux-Kernel muss für die Spectre- und Meltdown-Sicherheitsrisiken gepatcht werden. Weitere Informationen erhalten Sie beim Anbieter der Linux-Distribution. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Schutz vor Spectre/Meltdown eingerichtet wurde:

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

Die Ausgabe für diesen Befehl ähnelt der folgenden und gibt an, ob ein Computer für eines der Probleme anfällig ist. Wenn diese Dateien fehlen, wurde der Computer nicht gepatcht.

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Log Analytics-Arbeitsbereich
Für VM Insights ist ein Log Analytics-Arbeitsbereich erforderlich. Ausführliche Informationen sowie Anforderungen dieses Arbeitsbereichs finden Sie unter [Konfigurieren eines Log Analytics-Arbeitsbereichs für VM Insights](vminsights-configure-workspace.md).
## <a name="agents"></a>Agents
Für VM Insights müssen die folgenden beiden Agents auf jeder zu überwachenden VM oder VM-Skalierungsgruppe installiert sein. Zum Onboarding der Ressource installieren Sie diese Agents, und verbinden Sie sie mit dem Arbeitsbereich.  Informationen zu den Netzwerkanforderungen für diese Agents finden Sie unter [Netzwerkanforderungen](../agents/log-analytics-agent.md#network-requirements).

- [Log Analytics-Agent:](../agents/log-analytics-agent.md) Dieser Agent erfasst Ereignisse und Leistungsdaten für die VM oder VM-Skalierungsgruppe und übergibt sie an den Log Analytics-Arbeitsbereich. Bei Bereitstellungsmethoden für den Log Analytics-Agent in Azure-Ressourcen wird die VM-Erweiterung für [Windows](../../virtual-machines/extensions/oms-windows.md) und [Linux](../../virtual-machines/extensions/oms-linux.md) verwendet.
- Dependency-Agent: Dieser Agent erfasst ermittelte Daten zu Prozessen, die auf der VM ausgeführt werden, sowie zu Abhängigkeiten von externen Prozessen. Diese Daten werden vom [Zuordnungsfeature in VM Insights](../vm/vminsights-maps.md) genutzt. Der Dependency-Agent verwendet den Log Analytics-Agent, um die Daten an Azure Monitor zu senden. Bei Bereitstellungsmethoden für den Dependency-Agent in Azure-Ressourcen wird die VM-Erweiterung für [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) und [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) verwendet.

> [!NOTE]
> Beim Log Analytics-Agent handelt es sich um den Agent, der auch in System Center Operations Manager verwendet wird. VM Insights kann Agents überwachen, die auch von Operations Manager überwacht werden, wenn eine direkte Verbindung vorhanden ist und Sie den Dependency-Agent auf ihnen installieren. Agents, die über eine [Verwaltungsgruppenverbindung](../tform/../agents/om-agents.md) mit Azure Monitor verbunden sind, können nicht von VM Insights überwacht werden.

Im Folgenden sind verschiedene Methoden für die Bereitstellung dieser Agents aufgeführt. 

| Methode | BESCHREIBUNG |
|:---|:---|
| [Azure portal](../vm/vminsights-enable-portal.md) | Installieren Sie beide Agents auf einem einzelnen virtuellen Computer, einer VM-Skalierungsgruppe oder virtuellen Hybridcomputern, die mit Azure Arc verbunden sind. |
| [Resource Manager-Vorlagen](../vm/vminsights-enable-resource-manager.md) | Installieren Sie beide Agents mithilfe einer der unterstützten Methoden zum Bereitstellen einer Resource Manager-Vorlage, z. B. über die CLI oder PowerShell. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | Weisen Sie eine Azure Policy-Initiative zu, um die Agents bei der Erstellung eines virtuellen Computers oder einer VM-Skalierungsgruppe automatisch zu installieren. |
| [Manuelle Installation](../vm/vminsights-enable-hybrid.md) | Installieren Sie die Agents auf dem Gastbetriebssystem auf Computern, die außerhalb von Azure gehostet werden, z. B. in Ihrem Rechenzentrum oder anderen Cloudumgebungen. |


## <a name="network-requirements"></a>Netzwerkanforderungen

- Informationen zu den Netzwerkanforderungen für den Log Analytics-Agent finden Sie unter [Netzwerkanforderungen](../agents/log-analytics-agent.md#network-requirements).
- Für den Dependency-Agent ist eine Verbindung zwischen dem virtuellen Computer und der Adresse 169.254.169.254 erforderlich. Dies ist der Endpunkt des Azure-Metadatendiensts. Stellen Sie sicher, dass die Firewalleinstellungen Verbindungen mit diesem Endpunkt zulassen.


## <a name="management-packs"></a>Management Packs
Wenn ein Log Analytics-Arbeitsbereich für VM Insights konfiguriert wird, werden zwei Management Packs an alle Windows-Computer weitergeleitet, die mit diesem Arbeitsbereich verbunden sind. Die Management Packs heißen *Microsoft.IntelligencePacks.ApplicationDependencyMonitor* und *Microsoft.IntelligencePacks.VMInsights* und werden in *%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs* geschrieben. 

Die vom Management Pack *ApplicationDependencyMonitor* verwendete Datenquelle ist * *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Die vom Management Pack *VMInsights* verwendete Datenquelle ist *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnose- und Nutzungsdaten

Wenn Sie den Azure Monitor-Dienst verwenden, sammelt Microsoft automatisch Nutzungs- und Leistungsdaten. Microsoft verwendet diese Daten, um die Qualität, Sicherheit und Integrität des Diensts zu verbessern. 

Das Zuordnungsfeature enthält Daten zur Konfiguration Ihrer Software, um genaue und effiziente Funktionen zur Problembehandlung bereitzustellen. Die Daten enthalten Informationen wie Betriebssystem und Version, IP-Adresse, DNS-Name und Arbeitsstationsname. Microsoft erfasst weder Namen noch Adressen oder andere Kontaktinformationen.

Weitere Informationen zur Sammlung und Nutzung von Daten finden Sie in den [Datenschutzbestimmungen für Onlinedienste von Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwenden der Leistungsüberwachung finden Sie unter [Darstellen der Leistung in Diagrammen mit VM Insights](../vm/vminsights-performance.md). Informationen zu ermittelten Anwendungsabhängigkeiten finden Sie unter [Verwenden des Zuordnungsfeatures in Azure Monitor für VMs zum Analysieren von Anwendungskomponenten](../vm/vminsights-maps.md).
