---
title: Metriken in Azure Monitor | Microsoft-Dokumentation
description: Lernen Sie die Metriken in Azure Monitor kennen, die einfach zu überwachende Daten sind, die nahezu Echtzeitszenarien unterstützen.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: bwren
ms.openlocfilehash: 3986e59965842166ce675cd778d04984896d671d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458527"
---
# <a name="azure-monitor-metrics-overview"></a>Überblick über Metriken in Azure Monitor
Metriken in Azure Monitor sind ein Feature von Azure Monitor, das numerische Daten aus [überwachten Ressourcen](../monitor-reference.md) in einer Zeitreihendatenbank erfasst. Metriken sind numerische Werte, die in regelmäßigen Abständen erfasst werden und einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. 

Metriken in Azure Monitor sind einfach und in der Lage, Szenarien zu unterstützen, die nahezu in Echtzeit ablaufen: Sie sind daher nützlich für die Alarmierung und die schnelle Erkennung von Problemen. Sie können sie interaktiv unter Verwendung des Metrik-Explorer analysieren, proaktiv mit einer Warnung benachrichtigt werden, wenn ein Wert einen Schwellenwert überschreitet, oder sie in einer Arbeitsmappe oder einem Dashboard visualisieren.

> [!NOTE]
> Azure Monitor Metriken ist die eine Hälfte der Datenplattform, die Azure Monitor unterstützt. Die andere Hälfte ist [Azure Monitor Logs](../logs/data-platform-logs.md), die Protokoll- und Leistungsdaten sammelt und organisiert und es ermöglicht, diese Daten mit einer umfangreichen Abfragesprache zu analysieren. 
>
> Die Metrik-Funktion kann nur numerische Daten in einer bestimmten Struktur speichern, während die Logs-Funktion eine Vielfalt von Datentypen speichern kann (jeder Typ mit seiner eigenen Struktur). Sie können auch komplexe Analysen von Protokolldaten durchführen, indem Sie Protokollabfragen verwenden, die Sie für die Analyse von Metrikdaten nicht verwenden können.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Welche Möglichkeiten haben Sie mit Azure Monitor-Metriken?
In der folgenden Tabelle sind die Möglichkeiten aufgeführt, wie Sie die Metrikfunktion in Azure Monitor verwenden können.

|  | BESCHREIBUNG |
|:---|:---|
| **Analysieren** | Verwenden Sie [Metrik-Explorer](metrics-charts.md), um gesammelte Metriken in einem Diagramm zu analysieren und Metriken von verschiedenen Ressourcen zu vergleichen. |
| **Warnung** | Konfigurieren einer [Warnungsregel für eine Metrik](../alerts/alerts-metric.md), die eine Benachrichtigung sendet oder eine [automatisierte Aktion](../alerts/action-groups.md) ausführt, sobald der Metrikwert einen Schwellenwert überschreitet. |
| **Visualisieren** | Heften Sie ein Diagramm aus Metrik-Explorer an ein [Azure-Dashboard](../app/tutorial-app-dashboards.md) an.<br>Erstellen einer [Arbeitsmappe](../visualize/workbooks-overview.md), um mehrere Sätze von Daten in einem interaktiven Bericht zu kombinieren. Exportieren der Ergebnisse einer Abfrage nach [Grafana](../visualize/grafana-plugin.md), um dessen Dashboards zu nutzen und die Daten mit anderen Quellen zu kombinieren. |
| **Automatisieren** |  Verwenden von [Autoskalierung](../autoscale/autoscale-overview.md), um Ressourcen basierend auf einem Schwellenwert, der von einem Metrikwert über- oder unterschritten wird, herauf- oder herabzusetzen. |
| **Abrufen** | Zugriff auf Metrikwerte über eine Befehlszeile unter Verwendung von [ PowerShell-Cmdlets](/powershell/module/az.monitor).<br>Greifen Sie auf Metrikwerte aus einer benutzerdefinierten Anwendung mit der [REST API](./rest-api-walkthrough.md) zu.<br>Greifen Sie auf Metrikwerte von einer Befehlszeile mit der [Azure CLIB](/cli/azure/monitor/metrics) zu. |
| **Export** | [Leiten Sie Metriken zu Protokollen](./resource-logs.md#send-to-azure-storage), um Daten in Azure Monitor Metriken zusammen mit Daten in Azure Monitor Logs zu analysieren und um Metrikwerte länger als 93 Tage zu speichern.<br>Streamen Sie Metriken zu einem [Event Hub](./stream-monitoring-data-event-hubs.md), um sie an externe Systeme weiterzuleiten. |
| **Archivieren** | [Archivieren](./platform-logs-overview.md) des Leistungs- oder Integritätsverlaufs Ihrer Ressourcen zu Kompatibilitäts-/Überwachungszwecken oder zur Offline-Berichterstellung. |

![Diagramm, das Quellen und Verwendung von Metriken zeigt.](media/data-platform-metrics/metrics-overview.png)

## <a name="data-collection"></a>Datensammlung
Azure Monitor sammelt Metriken aus den folgenden Quellen. Nachdem diese Metriken in der Azure Monitor Metrik-Datenbank gesammelt wurden, können sie unabhängig von ihrer Quelle gemeinsam ausgewertet werden.

- **Azure-Ressourcen:** Plattformmetriken werden von Azure-Ressourcen erstellt und geben Ihnen einen Einblick in ihre Integrität und Leistung. Jeder Ressourcentyp erstellt einen [eigenen Satz von Metriken](./metrics-supported.md); dazu ist keinerlei Konfiguration erforderlich. Plattformmetriken werden von Azure-Ressourcen minütlich erfasst, sofern in der Definition der Metrik nichts anderes angegeben ist. 

- **Anwendungen**: Application Insights erstellt Metriken für Ihre überwachten Anwendungen, die Ihnen helfen, Leistungsprobleme zu erkennen und Trends beim Gebrauch Ihrer Anwendung nachzuverfolgen. Die Werte umfassen _Server-Antwortzeit_ und _Browser-Ausnahmen_.

- **Agents für virtuelle Computer:** Metriken werden über das Gastbetriebssystem eines virtuellen Computers erfasst. Sie können Gastbetriebssystem-Metriken für virtuelle Computer mit Windows aktivieren, indem Sie die [Windows-Diagnoseerweiterung](../agents/diagnostics-extension-overview.md) verwenden, und für virtuelle Computer mit Linux, indem Sie den [InfluxData Telegraf-Agent](https://www.influxdata.com/time-series-platform/telegraf/) verwenden.

- **Benutzerdefinierte Metriken:** Sie können Metriken zusätzlich zu den automatisch verfügbaren Standardmetriken definieren. Sie können [benutzerdefinierte Metriken in Ihrer Anwendung](../app/api-custom-events-metrics.md) definieren, die von Application Insights überwacht wird, oder benutzerdefinierte Metriken für einen Azure-Dienst erstellen, indem Sie die [benutzerdefinierte Metriken-API](./metrics-store-custom-rest-api.md) verwenden.

Eine vollständige Liste der Datenquellen, die Daten an Azure Monitor Metriken senden können, finden Sie unter [Was wird von Azure Monitor überwacht? ](../monitor-reference.md).

## <a name="metrics-explorer"></a>Metrik-Explorer
Verwenden Sie den [Metrik-Explorer](metrics-charts.md), um die Daten in Ihrer Metrikdatenbank interaktiv zu analysieren und die Werte mehrerer Metriken im zeitlichen Verlauf in Diagrammen darzustellen. Sie können die Diagramme an ein Dashboard anheften, um sie mit anderen Visualisierungen anzuzeigen. Zudem haben Sie die Möglichkeit, Metriken mit der [REST-API für die Azure-Überwachung](./rest-api-walkthrough.md) abzurufen.

![Screenshot von einem Beispieldiagramm in Metrik-Explorer, das Serveranforderungen, die Serverantwortzeit und fehlgeschlagene Anforderungen zeigt.](media/data-platform-metrics/metrics-explorer.png)

Für weitere Informationen siehe [Erste Schritte mit Azure Monitor Metrik-Explorer](./metrics-getting-started.md).

## <a name="data-structure"></a>Datenstruktur
Daten, die Azure Monitor Metriken sammelt, werden in einer Zeitreihendatenbank gespeichert, die für die Analyse von Zeitstempeldaten optimiert ist. Jede Gruppe von Metrikwerten stellt eine Zeitreihe mit den folgenden Eigenschaften dar:

* Der Zeitpunkt, zu dem der Wert gesammelt wurde
* Die Ressource, der der Wert zugeordnet ist
* Ein Namespace, der wie eine Kategorie für die Metrik wirkt.
* Metrikname
* Den Wert selbst
* [Mehrere Dimensionen](#multi-dimensional-metrics), wenn sie vorhanden sind. Beachten Sie, dass benutzerdefinierte Metriken auf 10 Dimensionen beschränkt sind.

## <a name="multi-dimensional-metrics"></a>Mehrdimensionale Metriken
Eine Schwierigkeit besteht bei Metrikdaten darin, dass häufig nur eingeschränkte Informationen zur Verfügung stehen, wenn es um die Bereitstellung von Kontext für erfasste Werte geht. In Azure Monitor wird dieses Problem mit mehrdimensionalen Metriken gelöst. 

Dimensionen einer Metrik sind Name/Wert-Paare, die zusätzliche Daten zur Beschreibung des Metrikwertes enthalten. Beispielsweise kann eine Metrik mit dem Namen _Verfügbarer Speicherplatz_ eine Dimension namens _Laufwerk_ mit den Werten _C:_ und _D:_ aufweisen. Diese Dimension würde es ermöglichen, den verfügbaren Platz für alle Laufwerke oder für jedes einzelne Laufwerk anzuzeigen.

Das folgende Beispiel zeigt zwei Datensätze für eine hypothetische Metrik namens _Netzdurchsatz_. Das erste Dataset weist keine Dimensionen auf. Der zweite Datensatz zeigt die Werte mit zwei Dimensionen: _IP_ und _Richtung_.

### <a name="network-throughput"></a>Netzwerkdurchsatz

| Timestamp     | Metrikwert |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1\.331,8 KBit/s |
| 8/9/2017 8:15 | 1\.141,4 KBit/s |
| 8/9/2017 8:16 | 1\.110,2 KBit/s |

Diese dimensionslose Metrik kann nur eine grundlegende Frage beantworten wie "Wie hoch war mein Netz-Durchsatz zu einem bestimmten Zeitpunkt?"

### <a name="network-throughput-and-two-dimensions-ip-and-direction"></a>Netzdurchsatz und zwei Dimensionen ("IP" und "Richtung")

| Timestamp     | Dimension „IP“   | Dimension „Richtung“ | Metrikwert|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Send"    | 646,5 KBit/s |
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Receive" | 420,1 KBit/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Send"    | 150,0 KBit/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Receive" | 115,2 KBit/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Send"    | 515,2 KBit/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Receive" | 371,1 KBit/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Send"    | 155,0 KBit/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Receive" | 100,1 KBit/s |

Mit dieser Metrik können Fragen beantwortet werden wie "Wie hoch war der Netzdurchsatz für jede IP-Adresse?" und "Wie viele Daten wurden gesendet bzw. empfangen?" Mehrdimensionale Metriken haben im Vergleich zu dimensionslosen Metriken einen zusätzlichen analytischen und diagnostischen Wert.

### <a name="viewing-multi-dimensional-performance-counter-metrics-in-metrics-explorer"></a>Anzeigen von mehrdimensionalen Leistungsindikatoren im Metrik-Explorer 
Es ist nicht möglich, Performance-Counter-Metriken, die ein Sternchen (\*) enthalten, über die Classic Guest Metriken API an Azure Monitor zu senden. Diese API kann keine Metriken anzeigen, die ein Sternchen enthalten, da es sich um eine multidimensionale Metrik handelt, die von klassischen Metriken nicht unterstützt wird.

So konfigurieren und zeigen Sie mehrdimensionale Gastbetriebssystem-Leistungsindikator-Metriken mit der Azure Diagnose-Erweiterung an:

1. Rufen Sie die Seite **Diagnoseeinstellungen** für Ihren virtuellen Computer auf.
2. Wählen Sie die Registerkarte **Leistungszähler** aus. 
3. Wählen Sie **Custom** aus, um die Leistungszähler zu konfigurieren, die Sie sammeln möchten.

   ![Screenshot vom Abschnitt "Leistungszähler" auf der Seite mit den Diagnoseeinstellungen.](media/data-platform-metrics/azure-monitor-perf-counter.png)

4. Wählen Sie **Senke**. Wählen Sie dann **Aktiviert**, um Ihre Daten an Azure Monitor zu senden.

   ![Screenshot vom Abschnitt "Senken" auf der Seite mit den Diagnoseeinstellungen.](media/data-platform-metrics/azure-monitor-sink.png)

5. Um Ihre Metrik in Azure Monitor anzuzeigen, wählen Sie **Virtual Computer Gast** in der Dropdown-Liste **Metrik Namespace**.

   ![Screenshot vom Metrik Namespace.](media/data-platform-metrics/vm-guest-namespace.png)

6. Wählen Sie **Teilen anwenden** und geben Sie die Details ein, um die Metrik nach Instanzen aufzuteilen. Sie können dann die Metrik sehen, wie sie nach jedem der möglichen Werte aufgeteilt ist, die durch das Sternchen in der Konfiguration dargestellt werden. In diesem Beispiel steht das Sternchen für die logischen Datenträgervolumina plus die Gesamtzahl.

   ![Screenshot von der Aufteilung einer Metrik nach Instanz.](media/data-platform-metrics/split-by-instance.png)

## <a name="retention-of-metrics"></a>Aufbewahrung von Metriken
Für die meisten Ressourcen in Azure werden Plattformmetriken 93 Tage lang gespeichert. Es gibt einige Ausnahmen:

- **Klassische Gastbetriebssystem-Metriken** Dies sind Leistungszähler, die von der [Windows-Diagnoseerweiterung](../agents/diagnostics-extension-overview.md) oder der [Linux-Diagnoseerweiterung](../../virtual-machines/extensions/diagnostics-linux.md) erfasst und an ein Azure-Speicherkonto weitergeleitet werden. Die Aufbewahrung für diese Metriken ist für mindestens 14 Tage garantiert, obwohl kein Ablaufdatum in das Speicherkonto geschrieben wird. 
  
  Aus Leistungsgründen begrenzt das Portal die angezeigte Datenmenge basierend auf dem Volumen. Daher kann die tatsächliche Anzahl der Tage, die das Portal abruft, länger als 14 Tage sein, wenn das Volumen der geschriebenen Daten nicht groß ist. 

- **An Azure Monitor Metriken gesendete Gastbetriebssystemmetriken:** Dies sind Leistungsindikatoren, die von der [Windows Diagnoseerweiterung ](../agents/diagnostics-extension-overview.md) gesammelt und an die [Azure Monitor Datensenke](../agents/diagnostics-extension-overview.md#data-destinations) oder den [InfluxData Telegraf-Agent](https://www.influxdata.com/time-series-platform/telegraf/) auf Linux-Computern oder den neueren [Azure Monitor-Agent](../agents/azure-monitor-agent-overview.md) über Datensammlungsregeln gesendet werden. Die Vermerkdauer für diese Metriken beträgt 93 Tage.

- **Vom Log Analytics-Agent gesammelte Gastbetriebssystemmetriken:** Dies sind Leistungsindikatoren, die vom Log Analytics-Agent gesammelt und an einen Log Analytics-Arbeitsbereich gesendet werden. Die Aufbewahrung für diese Metriken beträgt 31 Tage. Sie kann auf bis zu 2 Jahre verlängert werden.

- **Auf Application Insights-Protokollen basierende Metriken**. Hinter den Kulissen: [Blog-basierte Metriken](../app/pre-aggregated-metrics-log-metrics.md) werden in Protokollabfragen übersetzt. Ihre Aufbewahrung ist variabel und entspricht der Aufbewahrung der Ereignisse in den zugrunde liegenden Protokollen (31 Tage bis 2 Jahre). Für die Application Insights-Ressourcen werden die Protokolle 90 Tage lang gespeichert.

> [!NOTE]
> Sie können [Plattformmetriken für Azure Monitor-Ressourcen an einen Log Analytics-Arbeitsbereich](./resource-logs.md#send-to-azure-storage) senden, um langfristige Trends zu ermitteln.

> [!NOTE]
> Wie bereits erwähnt, werden die Plattformmetriken für die meisten Ressourcen in Azure 93 Tage lang gespeichert. Sie können jedoch (in der Kachel **Metriken**) nur Daten für maximal 30 Tage in einem einzigen Diagramm abfragen. Diese Einschränkung gilt nicht für protokollbasierte Metriken. 
>
> Wenn Sie ein leeres Diagramm sehen oder Ihr Diagramm nur einen Teil der Metrikdaten anzeigt, vergewissern Sie sich, dass die Differenz zwischen Start- und Enddatum in der Zeitauswahl das 30-Tage-Intervall nicht überschreitet. Nachdem Sie ein 30-Tage-Intervall ausgewählt haben, können Sie das Diagramm [Schwenken](./metrics-charts.md#pan) verschieben, um das gesamte Aufbewahrungsfenster anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Azure Monitor-Datenplattform](../data-platform.md).
- Erfahren Sie mehr über [Protokolldaten in Azure Monitor](../logs/data-platform-logs.md).
- Erfahren Sie mehr über die für verschiedene Ressourcen in Azure [verfügbaren Überwachungsdaten](../agents/data-sources.md).