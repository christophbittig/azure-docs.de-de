---
title: Übersicht zur Azure-Diagnoseerweiterung
description: Verwenden Sie die Azure-Diagnose zur Problembehandlung, zur Leistungsmessung, zur Überwachung und zur Datenverkehrsanalyse in Clouddiensten, virtuellen Maschinen und Service Fabric.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 00a3f7f43a533b66f6671b260290811667f38107
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325541"
---
# <a name="azure-diagnostics-extension-overview"></a>Übersicht zur Azure-Diagnoseerweiterung
Die Azure-Diagnoseerweiterung ist ein [Agent in Azure Monitor](../agents/agents-overview.md), der Überwachungsdaten vom Gastbetriebssystem von Azure-Computeressourcen inklusive VMs sammelt. Dieser Artikel bietet eine Übersicht zur Azure-Diagnoseerweiterung einschließlich spezifischer Funktionen, die unterstützt werden, sowie Optionen für die Installation und Konfiguration. 

> [!NOTE]
> Die Azure-Diagnoseerweiterung ist einer der Agents, die zum Erfassen von Überwachungsdaten aus dem Gastbetriebssystem von Computeressourcen verfügbar sind. Unter [Übersicht über die Azure Monitor-Agents](../agents/agents-overview.md) finden Sie eine Beschreibung der verschiedenen Agents sowie Informationen zur Wahl der geeigneten Agents für Ihre Anforderungen.

## <a name="primary-scenarios"></a>Primäre Szenarien
Die folgenden primären Szenarien werden von der Diagnoseerweiterung abgedeckt:

- Erfassen von Gastmetriken in Azure Monitor-Metriken
- Senden von Gastprotokollen und -metriken an Azure-Speicher zur Archivierung
- Senden von Gastprotokollen und -metriken an Azure Event Hubs, um sie außerhalb von Azure nutzen zu können


## <a name="comparison-to-log-analytics-agent"></a>Vergleich mit Log Analytics-Agent
Der Log Analytics-Agent in Azure Monitor kann auch zum Sammeln von Überwachungsdaten aus dem Gastbetriebssystem virtueller Computer verwendet werden. Je nach Ihren Anforderungen können Sie eine der Möglichkeiten oder beide nutzen. Einen ausführlichen Vergleich der Azure Monitor-Agents finden Sie unter [Übersicht über die Azure Monitor-Agents](../agents/agents-overview.md). 

Beachten Sie die folgenden Hauptunterschiede:

- Die Azure-Diagnoseerweiterung kann nur mit virtuellen Azure-Computern verwendet werden. Der Log Analytics-Agent kann mit virtuellen Computern in Azure, anderen Clouds und lokal verwendet werden.
- Die Azure-Diagnoseerweiterung sendet Daten an Azure Storage, [Azure Monitor-Metriken](../essentials/data-platform-metrics.md) (nur Windows) und Event Hubs. Der Log Analytics-Agent erfasst Daten für [Azure Monitor-Protokolle](../logs/data-platform-logs.md).
- Der Log Analytics-Agent ist für [Lösungen](../monitor-reference.md#insights-and-curated-visualizations), [VM Insights](../vm/vminsights-overview.md) und andere Dienste wie [Microsoft Defender für Cloud](../../security-center/index.yml) erforderlich.

## <a name="costs"></a>Kosten
Es fallen keine Kosten für die Azure-Diagnoseerweiterung an, aber möglicherweise Gebühren für die erfassten Daten. Unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/) finden Sie Informationen für das Ziel, auf dem Sie Daten sammeln.

## <a name="data-collected"></a>Gesammelte Daten
In den folgenden Tabellen sind die Daten aufgeführt, die von der Windows- und Linux-Diagnoseerweiterung erfasst werden können.

### <a name="windows-diagnostics-extension-wad"></a>Windows-Diagnoseerweiterung (WAD)

| Data source | BESCHREIBUNG |
| --- | --- |
| Windows-Ereignisprotokolle   | Ereignisse aus dem Windows-Ereignisprotokoll. |
| Leistungsindikatoren | Numerische Werte, die die Leistung verschiedener Aspekte von Betriebssystem und Workloads messen. |
| IIS-Protokolle             | Nutzungsinformationen für IIS-Websites, die unter dem Gastbetriebssystem ausgeführt werden. |
| Anwendungsprotokolle     | Von Ihrer Anwendung geschriebene Ablaufverfolgungsmeldungen. |
| .NET EventSource-Protokolle |Code zum Schreiben von Ereignissen mit der .NET-Klasse [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) |
| [Manifestbasierte ETW-Protokolle](/windows/desktop/etw/about-event-tracing) |Von einem beliebigen Prozess generierte Ereignisse der Ereignisablaufverfolgung für Windows. |
| Absturzabbilder (Protokolle)   | Informationen zum Status des Prozesses bei Absturz einer Anwendung. |
| Dateibasierte Protokolle    | Von Ihrer Anwendung oder Ihrem Dienst erstellte Protokolle |
| Agent-Diagnoseprotokolle | Informationen zur Azure-Diagnose selbst. |


### <a name="linux-diagnostics-extension-lad"></a>Linux-Diagnoseerweiterung (LAD)

| Data source | BESCHREIBUNG |
| --- | --- |
| syslog | Ereignisse, die an das Linux-Ereignisprotokollierungssystem gesendet werden.   |
| Leistungsindikatoren  | Numerische Werte, die die Leistung verschiedener Aspekte von Betriebssystem und Workloads messen. |
| Protokolldateien | Einträge, die an ein dateibasiertes Protokoll gesendet werden.  |

## <a name="data-destinations"></a>Datenziele
Die Azure-Diagnoseerweiterung für Windows und Linux erfasst Daten immer in einem Azure Storage-Konto. Unter [Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)](diagnostics-extension-windows-install.md) und [Verwenden der Linux-Diagnoseerweiterung zum Überwachen von Metriken und Protokollen](../../virtual-machines/extensions/diagnostics-linux.md) finden Sie eine Liste der spezifischen Tabellen und Blobs, in denen diese Daten gesammelt werden.

Konfigurieren Sie mindestens eine *Datensenke*, um Daten an andere zusätzliche Ziele zu senden. In den folgenden Abschnitten werden die verfügbaren Senken für die Windows- und Linux-Diagnoseerweiterung aufgeführt.

### <a name="windows-diagnostics-extension-wad"></a>Windows-Diagnoseerweiterung (WAD)

| Destination | BESCHREIBUNG |
|:---|:---|
| Azure Monitor-Metriken | Sammeln von Leistungsdaten für Azure Monitor-Metriken. Siehe [Senden von Gastbetriebssystemmetriken an den Metrikspeicher von Azure Monitor unter Verwendung einer Resource Manager-Vorlage für einen virtuellen Windows-Computer](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Event Hubs | Verwenden Sie Azure Event Hubs, um Daten an Empfänger außerhalb von Azure zu senden. Siehe [Streamen von Azure-Diagnosedaten an Event Hubs](diagnostics-extension-stream-event-hubs.md). |
| Azure Storage-Blobs | Schreiben von Daten außer in Tabellen in Blobs in Azure Storage. |
| Application Insights | Sammeln von Daten aus Anwendungen, die auf Ihrer VM ausgeführt werden, zur Integration in andere Anwendungsüberwachungen in Application Insights. Siehe [Senden von Cloud Services-, Virtual Machines- oder Service Fabric-Diagnosedaten an Application Insights](diagnostics-extension-to-application-insights.md). |

Sie können auch WAD-Daten aus dem Speicher in einem Log Analytics-Arbeitsbereich sammeln, um sie mit Azure Monitor-Protokollen zu analysieren, obwohl der Log Analytics-Agent in der Regel für diese Funktionalität verwendet wird. Er kann Daten direkt an einen Log Analytics-Arbeitsbereich senden und unterstützt Lösungen und Erkenntnisse, die zusätzliche Funktionalität bereitstellen.  Siehe [Sammeln von Azure-Diagnoseprotokollen aus Azure Storage](../agents/diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Linux-Diagnoseerweiterung (LAD)
LAD schreibt in Azure Storage Daten in Tabellen. LAD unterstützt die Senken in der folgenden Tabelle.

| Destination | BESCHREIBUNG |
|:---|:---|
| Event Hubs | Verwenden Sie Azure Event Hubs, um Daten an Empfänger außerhalb von Azure zu senden. |
| Azure Storage-Blobs | Schreiben von Daten außer in Tabellen in Blobs in Azure Storage. |
| Azure Monitor-Metriken | Installieren Sie zusätzlich zu LAD den Telegraf-Agent. Siehe [Erfassen von benutzerdefinierten Metriken für einen virtuellen Linux-Computer mit dem InfluxData Telegraf-Agent](../essentials/collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Installation und Konfiguration
Die Diagnoseerweiterung wird als [VM-Erweiterung](../../virtual-machines/extensions/overview.md) in Azure implementiert, sodass die gleichen Installationsoptionen mit Resource Manager-Vorlagen, PowerShell und der Befehlszeilenschnittstelle unterstützt werden. Weitere Informationen zu Installation und Wartung von VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Windows](../../virtual-machines/extensions/features-windows.md) und [Informationen zu Erweiterungen und Features für virtuelle Computer für Linux](../../virtual-machines/extensions/features-linux.md).

Sie können die Windows- und Linux-Diagnoseerweiterung auch im Azure-Portal unter **Diagnoseeinstellungen** im Abschnitt **Überwachung** des Menüs der VM installieren und konfigurieren.

Weitere Informationen zum Installieren und Konfigurieren der Diagnoseerweiterung für Windows und Linux finden Sie in den folgenden Artikeln.

- [Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)](diagnostics-extension-windows-install.md)
- [Verwenden der Linux-Diagnoseerweiterung zum Überwachen von Metriken und Protokollen](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Sonstige Dokumentation

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Azure Cloud Service (klassisch) – Web- und Workerrollen
- [Einführung in die Überwachung von Clouddiensten](../../cloud-services/cloud-services-how-to-monitor.md)
- [Aktivieren der Azure-Diagnose in Azure Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights für Azure Cloud Services](../app/cloudservices.md)<br>[Verfolgen des Ablaufs einer Cloud Services-Anwendung mit der Azure-Diagnose](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Nächste Schritte


* Hier erhalten Sie Informationen zur Verwendung von [Leistungsindikatoren in der Azure-Diagnose](../../cloud-services/diagnostics-performance-counters.md).
* Wenn bei der Diagnose Probleme mit dem Starten oder dem Finden von Daten in Azure-Speichertabellen auftreten, finden Sie weitere Informationen unter [Problembehandlung mit Azure-Diagnose](diagnostics-extension-troubleshooting.md).
