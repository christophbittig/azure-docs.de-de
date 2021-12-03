---
title: Übersicht über den Azure Monitor-Agent
description: Übersicht über den Azure Monitor-Agent zum Sammeln von Überwachungsdaten aus dem Gastbetriebssystem virtueller Computer.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: references_regions
ms.openlocfilehash: c89625bb1ea7a9b2bee53468a09a48073e5516bf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308653"
---
# <a name="azure-monitor-agent-overview"></a>Übersicht über den Azure Monitor-Agent
Der Azure Monitor-Agent (AMA) sammelt Überwachungsdaten aus dem Gastbetriebssystem virtueller Azure-Computer und übermittelt sie an Azure Monitor. Dieser Artikel bietet eine Übersicht über den Azure Monitor-Agent und enthält Informationen zu dessen Installation und zur Konfiguration der Datensammlung.

## <a name="relationship-to-other-agents"></a>Beziehung zu anderen Agents
Der Azure Monitor-Agent ersetzt die folgenden Agents einer Vorgängerversion, die derzeit von Azure Monitor verwendet werden, um Gastdaten von virtuellen Computern zu sammeln ([bekannte Lücken anzeigen](../faq.yml)):

- [Log Analytics-Agent:](./log-analytics-agent.md) Sendet Daten an einen Log Analytics-Arbeitsbereich und unterstützt VM Insights und Überwachungslösungen.
- [Diagnoseerweiterung:](./diagnostics-extension-overview.md) Sendet Daten an Azure Monitor-Metriken (nur Windows), Azure Event Hubs und Azure Storage.
- [Telegraf-Agent:](../essentials/collect-custom-metrics-linux-telegraf.md) Sendet Daten an Azure Monitor-Metriken (nur Linux).

Neben der Konsolidierung dieser Funktionalität in einem einzelnen Agent bietet der Azure Monitor-Agent folgende Vorteile gegenüber den vorhandenen Agents:

- **Überwachungsumfang:** Konfigurieren Sie die Sammlung zentral für verschiedene Daten von verschiedenen virtuellen Computern.
- **Multi-Homing (Linux):** Senden Sie Daten von virtuellen Linux-Computern an mehrere Arbeitsbereiche.
- **Ereignisfilterung (Windows):** Verwenden Sie XPATH-Abfragen, um zu filtern, welche Windows-Ereignisse gesammelt werden.
- **Optimierte Erweiterungsverwaltung:** Der Azure Monitor-Agent verwendet eine neue Methode für den Umgang mit der Erweiterbarkeit. Diese Methode ist transparenter und besser steuerbar als Management Packs und Linux-Plug-Ins in den aktuellen Log Analytics-Agents.

### <a name="current-limitations"></a>Aktuelle Einschränkungen
Im Vergleich zu vorhandenen Agents bietet dieser neue Agent noch keine vollständige Parität.
- **Vergleich mit Log Analytics-Agents (MMA/OMS):**
  - Derzeit werden nicht alle Log Analytics-Lösungen unterstützt. Informieren Sie sich, [welche Dienste und Features unterstützt werden](#supported-services-and-features).
  - Keine Unterstützung für Azure Private Link.
  - Keine Unterstützung für das Sammeln von dateibasierten Protokollen oder IIS-Protokollen.
- **Vergleich mit Erweiterungen für die Azure-Diagnose (WAD/LAD):**
  - Keine Unterstützung für Event Hubs und Storage-Konten als Ziele.
  - Keine Unterstützung für das Sammeln von dateibasierten Protokollen, IIS-Protokollen, ETW-Ereignissen, NET-Ereignissen und Absturzbildern.

### <a name="changes-in-data-collection"></a>Änderungen bei der Datensammlung
Diese Methoden zum Definieren einer Datensammlung für die vorhandenen Agents unterscheiden sich erheblich. Jede Methode bringt Herausforderungen mit sich, die Sie mit dem Azure Monitor-Agent angehen können.

- Der Log Analytics-Agent bezieht seine Konfiguration aus einem Log Analytics-Arbeitsbereich. Dies lässt sich zwar ganz einfach zentral konfigurieren, es ist jedoch schwierig, unabhängige Definitionen für verschiedene virtuelle Computer zu definieren. Es können nur Daten an einen Log Analytics-Arbeitsbereich gesendet werden.
- Die Diagnoseerweiterung verfügt über eine Konfiguration für jeden virtuellen Computer. Es lassen sich ganz einfach unabhängige Definitionen für verschiedene virtuelle Computer definieren, die zentrale Verwaltung gestaltet sich jedoch schwierig. Daten können nur an Azure Monitor-Metriken, an Azure Event Hubs oder an Azure Storage gesendet werden. Für Linux-Agents ist der Open-Source-Telegraf-Agent erforderlich, um Daten an Azure Monitor-Metriken zu senden.

Der Azure Monitor-Agent verwendet [Datensammlungsregeln](data-collection-rule-overview.md), um die Daten zu konfigurieren, die von den einzelnen Agents gesammelt werden sollen. Datensammlungsregeln ermöglichen die Verwaltung von Sammlungseinstellungen im großen Stil sowie die Verwendung individueller, bereichsspezifischer Konfigurationen für Untergruppen von Computern. Dank ihrer Unabhängigkeit von Arbeitsbereichen und virtuellen Computern können sie einmalig definiert und für verschiedene Computer und Umgebungen wiederverwendet werden. Weitere Informationen finden Sie unter [Konfigurieren der Datensammlung für den Azure Monitor-Agent](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-the-azure-monitor-agent"></a>Sollte ich zum Azure Monitor-Agent wechseln?
Der Azure Monitor-Agent ersetzt die [Agents einer Vorgängerversion für Azure Monitor](agents-overview.md). Beachten Sie bei der Umstellung Ihrer VMs von den aktuellen Agents zum neuen Agent die folgenden Faktoren:

- **Umgebungsanforderungen:** Der Azure Monitor-Agent unterstützt heute [diese Betriebssysteme](./agents-overview.md#supported-operating-systems). Unterstützung für zukünftige Betriebssystemversionen, Umgebungsunterstützung und Netzwerkanforderungen werden wahrscheinlich in diesem neuen Agent bereitgestellt. 
 
  Prüfen Sie, ob Ihre Umgebung vom Azure Monitor-Agent unterstützt wird. Falls dies nicht der Fall ist, müssen Sie weiterhin den aktuellen Agent verwenden. Wenn der Azure Monitor-Agent Ihre aktuelle Umgebung unterstützt, sollten Sie einen Wechsel in Erwägung ziehen.
- **Aktuelle und neue Featureanforderungen:** Der Azure Monitor-Agent führt mehrere neue Funktionen wie die Filterung, Eingrenzung und das Multi-Homing ein. Die Parität mit den aktuellen Agents für andere Funktionen wie die benutzerdefinierte Protokollsammlung und Integration mit allen Lösungen ist noch nicht gegeben. ([Siehe Lösungen in der Vorschauversion](../faq.yml)) 
 
  Die meisten neuen Funktionen in Azure Monitor werden nur mit dem Azure Monitor-Agent zur Verfügung gestellt. Im Laufe der Zeit werden weitere Funktionen nur im neuen Agent verfügbar. Prüfen Sie, ob der Azure Monitor-Agent über die von Ihnen benötigten Features verfügt und ob Sie auf einige Features vorübergehend verzichten können, um andere wichtige Features im neuen Agent zu erhalten. 
  
  Wenn der Azure Monitor-Agent über alle Kernfunktionen verfügt, die Sie benötigen, sollten Sie einen Wechsel zum neuen Agent erwägen. Wenn es wichtige Features gibt, die Sie benötigen, verwenden Sie weiterhin den aktuellen Agent, bis der Azure Monitor-Agent die Featureparität erreicht.
- **Toleranz für Überarbeitungen:** Wenn Sie eine neue Umgebung mit Ressourcen wie Bereitstellungsskripts und Onboardingvorlagen einrichten, analysieren Sie den dafür erforderlichen Aufwand. Wenn die Einrichtung erheblichen Arbeitsaufwand mit sich bringt, sollten Sie erwägen, Ihre neue Umgebung mit dem neuen und nun allgemein verfügbaren Agent einzurichten. 
 
  Der Log Analytics-Agent von Azure Monitor wird am 31. August 2024 eingestellt. Die aktuellen Agents werden nach Beginn der Veralterungsphase noch einige Jahre lang unterstützt.

## <a name="supported-resource-types"></a>Unterstützte Ressourcentypen
Derzeit werden Azure-VMs, VM-Skalierungsgruppen und Azure Arc-fähige Server unterstützt. Azure Kubernetes Service und andere Computeressourcentypen werden zurzeit nicht unterstützt.

## <a name="supported-regions"></a>Unterstützte Regionen
Der Azure Monitor Agent ist in allen öffentlichen Regionen verfügbar, die Log Analytics unterstützen, sowie in den Azure Government und China Clouds. Air-gapped Clouds werden noch nicht unterstützt.

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme
Eine Liste der Windows- und Linux-Betriebssystemversionen, die derzeit vom Azure Monitor-Agent unterstützt werden, finden Sie unter [Unterstützte Betriebssysteme](agents-overview.md#supported-operating-systems).

## <a name="supported-services-and-features"></a>Unterstützte Dienste und Features
In der folgenden Tabelle erhalten Sie Informationen zur aktuellen Unterstützung für den Azure Monitor-Agent mit anderen Azure-Diensten.

| Azure-Dienst | Aktuelle Unterstützung | Weitere Informationen |
|:---|:---|:---|
| [Microsoft Defender für Cloud](../../security-center/security-center-introduction.md) | Private Vorschau | [Link zur Registrierung](https://aka.ms/AMAgent) |
| [Microsoft Sentinel](../../sentinel/overview.md) | <ul><li>Windows Ereignisweiterleitung (WEF): Private Vorschau</li><li>Windows-Sicherheitsereignisse: [Öffentliche Vorschau](../../sentinel/connect-windows-security-events.md?tabs=AMA)</li></ul>  | <ul><li>[Link zur Registrierung](https://aka.ms/AMAgent) </li><li>Es ist keine Registrierung erforderlich!</li></ul> |

In der folgenden Tabelle erhalten Sie Informationen zur aktuellen Unterstützung für den Azure Monitor-Agent mit Azure Monitor-Features.

| Azure Monitor-Feature | Aktuelle Unterstützung | Weitere Informationen |
|:---|:---|:---|
| [VM Insights](../vm/vminsights-overview.md) | Private Vorschau  | [Link zur Registrierung](https://aka.ms/amadcr-privatepreviews) |
| [Verbinden mit privaten Links oder AMPLS](../logs/private-link-security.md) | Private Vorschau für AMA | [Link zur Registrierung](https://aka.ms/amadcr-privatepreviews) |
| [Gastintegrität von VM Insights](../vm/vminsights-health-overview.md) | Public Preview | Nur im neuen Agent verfügbar |
| [Erkenntnisse zu SQL](../insights/sql-insights-overview.md) | Public Preview | Nur im neuen Agent verfügbar |

In der folgenden Tabelle erhalten Sie Informationen zur aktuellen Unterstützung für den Azure Monitor-Agent mit Azure-Lösungen.

| Lösung | Aktuelle Unterstützung | Weitere Informationen |
|:---|:---|:---|
| [Änderungsnachverfolgung](../../automation/change-tracking/overview.md) | Wird als Überwachung der Dateiintegrität in der privaten Vorschauversion von Microsoft Defender für Cloud unterstützt.  | [Link zur Registrierung](https://aka.ms/AMAgent) |
| [Updateverwaltung](../../automation/update-management/overview.md) | Verwenden Sie die Updateverwaltung v2 (private Vorschau), für die kein Agent erforderlich ist. | [Link zur Registrierung](https://www.yammer.com/azureadvisors/threads/1064001355087872) |

## <a name="coexistence-with-other-agents"></a>Koexistenz mit anderen Agents
Der Azure Monitor-Agent kann parallel zu den bereits vorhandenen Agents vorhanden sein, sodass deren Funktionen bei der Bewertung oder Migration weiterhin zur Verfügung stehen. Diese Funktion ist aufgrund der Einschränkungen wichtig, die vorhandene Lösungen unterstützen. Beim Sammeln doppelter Daten ist Vorsicht geboten, da diese zu Verzerrungen von Abfrageergebnissen und Mehrkosten für die Datenerfassung und -aufbewahrung führen können. 

So wird von VM Insights beispielsweise der Log Analytics-Agent verwendet, um Leistungsdaten an einen Log Analytics-Arbeitsbereich zu senden. Gegebenenfalls haben Sie auch den Arbeitsbereich für die Sammlung von Windows- und Syslog-Ereignissen von Agents konfiguriert. Wenn Sie nun den Azure Monitor-Agent installieren und eine Datensammlungsregel für die gleichen Ereignisse und Leistungsdaten erstellen, führt dies zu doppelten Daten.

Stellen Sie daher sicher, dass Sie nicht die gleichen Daten von beiden Agents sammeln. Ist dies der Fall, stellen Sie sicher, dass sie an separate Ziele gesendet werden.

## <a name="costs"></a>Kosten
Für den Azure Monitor-Agent fallen keine Kosten an, möglicherweise aber für die erfassten Daten. Ausführliche Informationen zur Log Analytics-Datensammlung und -aufbewahrung sowie zu Kundenmetriken finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="data-sources-and-destinations"></a>Datenquellen und -ziele
Die folgende Tabelle gibt Aufschluss darüber, welche Datentypen Sie aktuell mit dem Azure Monitor-Agent unter Verwendung von Datensammlungsregeln sammeln und wohin diese Daten gesendet werden können. Im Artikel [Was wird von Azure Monitor überwacht?](../monitor-reference.md) finden Sie eine Liste mit Erkenntnissen und wichtigen Lösungen sowie mit anderen Lösungen, die den Azure Monitor-Agent verwenden, um andere Arten von Daten zu sammeln.

Der Azure Monitor-Agent sendet Daten an Azure Monitor-Metriken oder an einen Log Analytics-Arbeitsbereich, der Azure Monitor-Protokolle unterstützt.

| Datenquellen- | Destinations | BESCHREIBUNG |
|:---|:---|:---|
| Leistung        | Monitor – Metriken (Vorschauversion)<sup>1</sup><br>Log Analytics-Arbeitsbereich | Numerische Werte zum Messen der Leistung verschiedener Betriebssystem- und Workloadaspekte |
| Windows-Ereignisprotokolle | Log Analytics-Arbeitsbereich | An das Windows-System für die Ereignisprotokollierung gesendete Informationen |
| syslog             | Log Analytics-Arbeitsbereich | Informationen, die an das Linux-System für die Ereignisprotokollierung gesendet werden |

<sup>1</sup> [Klicken Sie hier,](../essentials/metrics-custom-overview.md#quotas-and-limits) um weitere Einschränkungen bei der Verwendung von Azure Monitor Metrics zu überprüfen. Unter Linux wird die Verwendung von Azure Monitor-Metriken als einziges Ziel in v.1.10.9.0 oder höher unterstützt. 

## <a name="security"></a>Sicherheit
Der Azure Monitor-Agent benötigt zwar keine Schlüssel, dafür aber eine [systemseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Für die Agent-Bereitstellung muss auf dem jeweiligen virtuellen Computer eine systemseitig zugewiesene verwaltete Identität aktiviert sein.

## <a name="networking"></a>Netzwerk
Der Azure Monitor-Agent unterstützt Azure-Diensttags. Die beiden Tags „AzureMonitor“ und „AzureResourceManager“ sind erforderlich. Der Azure Monitor-Agent kann noch nicht mit Private Link-Bereichen von Azure Monitor verwendet werden. Wenn der Computer für die Kommunikation über das Internet einen Proxyserver verwendet, sehen Sie sich die folgenden Anforderungen an, um sich mit der erforderlichen Netzwerkkonfiguration vertraut zu machen.

### <a name="proxy-configuration"></a>Proxykonfiguration

Die Windows- und Linux-Erweiterungen für den Azure Monitor-Agent können mithilfe des HTTPS-Protokolls entweder über einen Proxyserver oder ein Log Analytics-Gateway mit Azure Monitor kommunizieren. Sie können dieses für virtuelle Azure-Computer, Azure-VM-Skalierungsgruppen und Azure Arc für Server verwenden. Nutzen Sie die Erweiterungseinstellungen wie in den folgenden Schritten beschrieben für die Konfiguration. Sowohl die anonyme Authentifizierung als auch die Standardauthentifizierung mithilfe eines Benutzernamens und eines Kennworts wird unterstützt.

> [!IMPORTANT]
> Die Proxykonfiguration wird für [Azure Monitor Metriken (Vorschau)](../essentials/metrics-custom-overview.md) als Ziel nicht unterstützt. Wenn Sie also Metriken an dieses Ziel senden, wird das öffentliche Internet ohne Proxy verwendet.

1. Verwenden Sie dieses Flussdiagramm, um zunächst die Werte der Parameter *setting* und *protectedSetting* zu bestimmen.

    ![Flussdiagramm zum Bestimmen der Werte der Parameter „setting“ und „protectedSetting“ beim Aktivieren der Erweiterung](media/azure-monitor-agent-overview/proxy-flowchart.png)

2. Nachdem die Werte für die Parameter *setting* und *protectedSetting* festgelegt wurden, geben Sie diese zusätzlichen Parameter an, wenn Sie den Azure Monitor-Agent mithilfe von PowerShell-Befehlen bereitstellen. Die folgenden Beispiele sind für virtuelle Azure-Computer konzipiert.

    | Parameter | Wert |
    |:---|:---|
    | Einstellung | Dies ist ein in eine Zeichenfolge konvertiertes JSON-Objekt aus dem vorherigen Flussdiagramm. Überspringen Sie diesen Schritt, wenn dies nicht zutrifft. Beispiel: {"proxy":{"mode":"application","address":"http://[address]:[port]","auth": false}}. |
    | ProtectedSetting | Dies ist ein in eine Zeichenfolge konvertiertes JSON-Objekt aus dem vorherigen Flussdiagramm. Überspringen Sie diesen Schritt, wenn dies nicht zutrifft. Beispiel: {"proxy":{"username": "[username]","password": "[password]"}}. |

# <a name="windows-vm"></a>[Windows-VM](#tab/PowerShellWindows)

```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0 -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="linux-vm"></a>[Linux-VM](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5 -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="windows-arc-enabled-server"></a>[Server mit Windows Arc-Unterstützung](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location> -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="linux-arc-enabled-server"></a>[Server mit Linux Arc-Unterstützung](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location> -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Installieren des Azure Monitor-Agents](azure-monitor-agent-install.md) auf Windows- und Linux-VMs
- [Erstellen Sie eine Datensammlungsregel](data-collection-rule-azure-monitor-agent.md), um Daten des Agents zu sammeln und an Azure Monitor zu senden.
