---
title: 'Überwachen von virtuellen Computern mit Azure Monitor: Workloads'
description: Hier erfahren Sie, wie die Gastworkloads von virtuellen Computern in Azure Monitor überwacht werden.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 64ef920e727baf559b71cac416404fb740e47625
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788551"
---
# <a name="monitor-virtual-machines-with-azure-monitor-workloads"></a>Überwachen von virtuellen Computern mit Azure Monitor: Workloads
Dieser Artikel ist Teil des Szenarios [Überwachen von virtuellen Computern und den jeweiligen Workloads in Azure Monitor](monitor-virtual-machine.md). Hier wird beschrieben, wie Workloads überwacht werden, die auf den Gastbetriebssystemen Ihrer virtuellen Computer ausgeführt werden. Dieser Artikel enthält Informationen zum Analysieren verschiedener Datenquellen auf den virtuellen Computern sowie zum Generieren von Warnungen.

## <a name="configure-additional-data-collection"></a>Konfigurieren einer zusätzlichen Datensammlung
Mit VM Insights werden nur Leistungsdaten vom Gastbetriebssystem der jeweils aktivierten Computer gesammelt. Sie können die Sammlung zusätzlicher Leistungsdaten, Ereignisse und anderer Überwachungsdaten durch den Agent aktivieren, indem Sie den Log Analytics-Arbeitsbereich konfigurieren. Er muss nur einmal konfiguriert werden, da jeder Agent, der eine Verbindung mit dem Arbeitsbereich herstellt, die Konfiguration automatisch herunterlädt und sofort mit dem Sammeln der definierten Daten beginnt.

Eine Liste der verfügbaren Datenquellen und nähere Informationen zu ihrer Konfiguration finden Sie unter [Agent-Datenquellen in Azure Monitor](../agents/agent-data-sources.md).

> [!NOTE]
> Die Datensammlung kann nicht selektiv für verschiedene Computer konfiguriert werden. Die Konfiguration für diesen Arbeitsbereich wird von allen Computern verwendet, die mit dem Arbeitsbereich verbunden sind.

> [!IMPORTANT]
> Sammeln Sie nur Daten, die Sie benötigen. Mit allen Daten, die in Ihrem Arbeitsbereich gesammelt werden, sind Kosten verbunden. Mit den gesammelten Daten sollten nur bestimmte Analyse- und Warnungsszenarios unterstützt werden.

## <a name="convert-management-pack-logic"></a>Konvertieren der Management Pack-Logik
Eine beträchtliche Anzahl von Kunden, bei denen Azure Monitor implementiert wurde, werden derzeit die Workloads der virtuellen Computer mithilfe von Management Packs in System Center Operations Manager überwacht. Es gibt keine Migrationstools zum Konvertieren von Ressourcen von Operations Manager zu Azure Monitor, da sich die Plattformen grundlegend unterscheiden. Bei der Migration handelt es sich vielmehr um eine Azure Monitor-Standardimplementierung, wobei weiterhin Operations Manager verwendet wird. Während Sie Azure Monitor entsprechend Ihren Anforderungen für verschiedene Anwendungen und Komponenten anpassen und immer mehr Funktionen hinzukommen, können Sie damit beginnen, die Nutzung verschiedener Management Packs und Agents in Operations Manager einzustellen.

Statt die gesamte Funktionalität eines Management Packs zu replizieren, analysieren Sie die vom Management Pack bereitgestellte kritische Überwachung. Entscheiden Sie, ob Sie diese Überwachungsanforderungen mithilfe der in den vorherigen Abschnitten beschriebenen Methoden replizieren können. Häufig können in Azure Monitor Regeln zum Sammeln von Daten und zum Generieren von Warnungen konfiguriert werden, mit denen so viele Funktionen repliziert werden können, sodass ein bestimmtes Management Pack außer Betrieb genommen werden kann. Management Packs enthalten häufig Hunderte und sogar Tausende von Regeln und Monitoren.

In den meisten Fällen werden mit Operations Manager Datensammlung und Bedingungen zum Generieren von Warnungen in einer Regel oder in einem Monitor kombiniert. In Azure Monitor muss für jedes Szenario zum Generieren von Warnungen eine Regel zum Sammeln von Daten und zum Generieren einer Warnung konfiguriert werden.

Dabei besteht eine Strategie darin, sich auf die Monitoren und Regeln zu konzentrieren, die in der Umgebung Warnungen ausgelöst hat. Nutzen Sie die [vorhandenen Berichte in Operations Manager](/system-center/scom/manage-reports-installed-during-setup), z. B. **Warnungen** und **Häufigste Warnungen**, mit deren Hilfe Sie Warnungen im Laufe der Zeit identifizieren können. Sie können auch die folgende Abfrage für die Operations-Datenbank ausführen, um die häufigsten aktuellen Warnungen auszuwerten.

```sql
select AlertName, COUNT(AlertName) as 'Total Alerts' from
Alert.vAlertResolutionState ars
inner join Alert.vAlertDetail adt on ars.AlertGuid = adt.AlertGuid
inner join Alert.vAlert alt on ars.AlertGuid = alt.AlertGuid
group by AlertName
order by 'Total Alerts' DESC
```

Werten Sie die Ausgabe aus, um bestimmte Warnungen für die Migration zu identifizieren. Ignorieren Sie alle Warnungen, die optimiert wurden oder bekanntermaßen problematisch sind. Überprüfen Sie die Management Packs, um kritische Warnungen zu identifizieren, die nie ausgelöst wurden.

## <a name="windows-or-syslog-event"></a>Windows-oder Syslog-Ereignis
In diesem gängigen Überwachungsszenario werden die Windows-Ereignisse bzw. Syslog vom Betriebssystem und von Anwendungen beschrieben. Sobald ein Ereignis erkannt wird, soll eine Warnung erstellt werden. Es kann auch gewartet werden, bis mehrere ähnliche Ereignisse innerhalb eines bestimmten Zeitfensters auftreten.

Wenn Sie diese Ereignisse sammeln möchten, konfigurieren Sie einen Log Analytics-Arbeitsbereich so, dass [Windows-Ereignisse](../agents/data-sources-windows-events.md) oder [Syslog-Ereignisse](../agents/data-sources-windows-events.md) gesammelt werden. Die Erfassung und Aufbewahrung dieser Daten im Arbeitsbereich sind mit Kosten verbunden.

Windows-Ereignisse werden in der [Ereignis](/azure/azure-monitor/reference/tables/event)tabelle und Syslog-Ereignisse in der [Syslog](/azure/azure-monitor/reference/tables/syslog)-Tabelle im Log Analytics-Arbeitsbereich gespeichert.

### <a name="sample-log-queries"></a>Beispielprotokollabfragen

- **Zählen Sie die Anzahl der Ereignisse nach Ereignisprotokoll und Ereignistyp auf dem Computer.**

    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

- **Zählen Sie die Anzahl der Ereignisse nach Ereignisprotokoll und Ereignis-ID auf dem Computer.**
    
    ```kusto
    Event
    | summarize count() by Computer, EventLog, EventLevelName
    | sort by Computer, EventLog, EventLevelName
    ```

### <a name="sample-alert-rules"></a>Beispiele für Warnungsregeln
Im folgenden Beispiel wird eine Warnung erstellt, wenn ein bestimmtes Windows-Ereignis auftritt. Dabei wird eine Warnungsregel für metrische Messungen verwendet, damit für jeden Computer eine eigene Warnung zu erstellt wird.

- **Erstellen Sie eine Warnungsregel bei einem bestimmten Windows-Ereignis.**

   In diesem Beispiel ist ein Ereignis im Anwendungsprotokoll dargestellt. Geben Sie für den Schwellenwert den Wert 0 an, und legen Sie für aufeinanderfolgende Sicherheitsverletzungen einen Wert größer 0 fest.

    ```kusto
    Event 
    | where EventLog == "Application"
    | where EventID == 123 
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **Erstellen Sie eine Warnungsregel für Syslog-Ereignisse mit einem bestimmten Schweregrad.**

   Im folgenden Beispiel sind Ereignisse im Zusammenhang mit Autorisierungsfehlern dargestellt. Geben Sie für den Schwellenwert den Wert 0 an, und legen Sie für aufeinanderfolgende Sicherheitsverletzungen einen Wert größer 0 fest.

    ```kusto
    Syslog
    | where Facility == "auth"
    | where SeverityLevel == "err"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="custom-performance-counters"></a>Benutzerdefinierte Leistungsindikatoren
Möglicherweise benötigen Sie von Anwendungen oder vom Gastbetriebssystem erstellte Leistungsindikatoren, die von VM Insights nicht erfasst werden. Konfigurieren Sie den Log Analytics-Arbeitsbereich so, dass diese [Leistungsdaten](../agents/data-sources-windows-events.md) erfasst werden. Die Erfassung und Aufbewahrung dieser Daten im Arbeitsbereich sind mit Kosten verbunden. Achten Sie darauf, dass keine Leistungsdaten erfasst werden, die bereits von VM Insights erfasst werden.

Über den Arbeitsbereich konfigurierte Leistungsdaten werden in der Tabelle [Perf](/azure/azure-monitor/reference/tables/perf) gespeichert. Diese Tabelle hat eine anderes Struktur als die von VM Insights verwendete Tabelle [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics).

### <a name="sample-log-queries"></a>Beispielprotokollabfragen

Beispiele für Protokollabfragen, bei denen benutzerdefinierte Leistungsindikatoren verwendet werden, finden Sie unter [Protokollabfragen mit Leistungsdatensätzen](../agents/data-sources-performance-counters.md#log-queries-with-performance-records).

### <a name="sample-alerts"></a>Beispielwarnungen

- **Erstellen Sie eine Warnung für den maximal zulässigen Wert eines Zählers.**
    
    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = max(CounterValue) by Computer
    ```

- **Erstellen Sie eine Warnung für den Durchschnittswert eines Zählers.**

    ```kusto
    Perf 
    | where CounterName == "My Counter" 
    | summarize AggregatedValue = avg(CounterValue) by Computer
    ```

## <a name="text-logs"></a>Textprotokolle
Von einigen Anwendungen werden Ereignisse in ein Textprotokoll geschrieben, das auf dem virtuellen Computer gespeichert wird. Geben Sie ein [benutzerdefiniertes Protokoll](../agents/data-sources-custom-logs.md) im Log Analytics-Arbeitsbereich an, in dem diese Ereignisse erfasst werden sollen. Sie definieren den Speicherort des Textprotokolls und dessen detaillierte Konfiguration. Die Erfassung und Aufbewahrung dieser Daten im Arbeitsbereich sind mit Kosten verbunden.

Ereignisse aus dem Textprotokoll werden in einer Tabelle mit einem Namen wie **MyTable_CL** gespeichert. Sie definieren den Namen und die Struktur des Protokolls, wenn Sie es konfigurieren.

### <a name="sample-log-queries"></a>Beispielprotokollabfragen
Bei den hier verwendeten Spaltennamen handelt es sich lediglich um Beispiele. Sie definieren die Spaltennamen für Ihr Protokoll, wenn Sie es definieren. Die Spaltennamen für Ihr Protokoll lauten höchstwahrscheinlich anders.

- **Zählen Sie die Anzahl der Ereignisse nach Code.**
    
    ```kusto
    MyApp_CL
    | summarize count() by code
    ```

### <a name="sample-alert-rule"></a>Beispiel für eine Warnungsregel

- **Erstellen Sie für jedes Fehlerereignis eine Warnungsregel.**
    
    ```kusto
    MyApp_CL
    | where status == "Error"
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```
## <a name="iis-logs"></a>IIS-Protokolle
Wenn IIS auf Windows-Computern ausgeführt wird, werden Protokolle in eine Textdatei geschrieben. Konfigurieren Sie einen Log Analytics-Arbeitsbereich so, dass [IIS-Protokolle](../agents/data-sources-iis-logs.md) erfasst werden. Die Erfassung und Aufbewahrung dieser Daten im Arbeitsbereich sind mit Kosten verbunden.

Datensätze aus dem IIS-Protokoll werden in der Tabelle [W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) im Log Analytics-Arbeitsbereich gespeichert.

### <a name="sample-log-queries"></a>Beispielprotokollabfragen

- **Zählen Sie die IIS-Protokolleinträge nach URL für den Host www.contoso.com.**
    
    ```kusto
    W3CIISLog 
    | where csHost=="www.contoso.com" 
    | summarize count() by csUriStem
    ```

- **Überprüfen Sie die Gesamtanzahl der von jedem IIS-Computer empfangenen Bytes.**

    ```kusto
    W3CIISLog 
    | summarize sum(csBytes) by Computer
    ```

### <a name="sample-alert-rule"></a>Beispiel für eine Warnungsregel

- **Erstellen Sie eine Warnungsregel für jeden Datensatz mit dem Rückgabestatus 500.**
    
    ```kusto
    W3CIISLog 
    | where scStatus==500
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

## <a name="service-or-daemon"></a>Dienst oder Daemon
Wenn Sie den Status eines Windows-Diensts oder eines Linux-Daemons überwachen möchten, aktivieren Sie die Lösung [Änderungsnachverfolgung und Bestand](../../automation/change-tracking/overview.md) in [Azure Automation](../../automation/automation-intro.md). Bei Azure Monitor gibt es keine Möglichkeit den Status eines Diensts oder Daemons zu überwachen. Es gibt zwar einige mögliche Methoden, z. B. die Suche nach Ereignissen im Windows-Ereignisprotokoll, aber diese Methode ist unzuverlässig. Sie können auch in der Tabelle [VMProcess](/azure/azure-monitor/reference/tables/vmprocess) nach dem Prozess suchen, der dem Dienst zugeordnet ist, der auf dem Computer ausgeführt wird. Diese Tabelle wird jedoch nur einmal pro Stunde aktiviert. Das ist in der Regel für Warnungen nicht ausreichend.

> [!NOTE]
> Die Lösung „Änderungsnachverfolgung und Bestand“ unterscheidet sich vom Feature [Änderungsanalyse](vminsights-change-analysis.md) in VM Insights. Dieses Feature befindet sich in der Phase der öffentlichen Vorschau und ist in diesem Szenario noch nicht enthalten.

Weitere Optionen zum Aktivieren der Lösung „Änderungsnachverfolgung“ auf virtuellen Computern finden Sie unter [Aktivieren der Lösung für Änderungsnachverfolgung und Bestand](../../automation/change-tracking/overview.md#enable-change-tracking-and-inventory). Diese Lösung enthält Methoden zum Konfigurieren von virtuellen Computern im großen Stil. Zur Unterstützung der Lösung müssen Sie [ein Azure Automation-Konto erstellen](../../automation/quickstarts/create-account-portal.md).

Wenn Sie „Änderungsnachverfolgung und Bestand“ aktivieren, werden im Log Analytics-Arbeitsbereich zwei neue Tabellen erstellt. Verwenden Sie diese Tabellen für Warnungsregeln für die Protokollabfrage.

| Tabelle | BESCHREIBUNG |
|:---|:---|
| [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationdata) | Änderungen an Gastkonfigurationsdaten |
| [ConfigurationData](/azure/azure-monitor/reference/tables/configurationdata) | Zuletzt gemeldeter Status für Gastkonfigurationsdaten |


### <a name="sample-log-queries"></a>Beispielprotokollabfragen

- **Listen Sie alle Dienste und Daemons auf, die vor Kurzem gestartet wurden.**
    
    ```kusto
    ConfigurationChange
    | where ConfigChangeType == "Daemons" or ConfigChangeType == "WindowsServices"
    | where SvcState == "Running"
    | sort by Computer, SvcName
    ```

### <a name="alert-rule-samples"></a>Beispiel für Warnungsregel

- **Erstellen Sie eine Warnungsregel für das Beenden eines Diensts.**

    
    ```kusto
    ConfigurationData
    | where SvcName == "W3SVC" 
    | where SvcState == "Stopped"
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

- **Erstellen Sie eine Warnungsregel für das Beenden eines Diensts in einer Gruppe von Diensten.**
    
    ```kusto
    let services = dynamic(["omskd","cshost","schedule","wuauserv","heathservice","efs","wsusservice","SrmSvc","CertSvc","wmsvc","vpxd","winmgmt","netman","smsexec","w3svc","sms_site_vss_writer","ccmexe","spooler","eventsystem","netlogon","kdc","ntds","lsmserv","gpsvc","dns","dfsr","dfs","dhcp","DNSCache","dmserver","messenger","w32time","plugplay","rpcss","lanmanserver","lmhosts","eventlog","lanmanworkstation","wnirm","mpssvc","dhcpserver","VSS","ClusSvc","MSExchangeTransport","MSExchangeIS"]);
    ConfigurationData
    | where ConfigDataType == "WindowsServices"
    | where SvcStartupType == "Auto"
    | where SvcName in (services)
    | where SvcState == "Stopped"
    | project TimeGenerated, Computer, SvcName, SvcDisplayName, SvcState
    | summarize AggregatedValue = count() by Computer, SvcName, SvcDisplayName, SvcState, bin(TimeGenerated, 15m)
    ```

## <a name="port-monitoring"></a>Portüberwachung
Mit der Portüberwachung wird überprüft, ob ein Computer an einem bestimmten Port lauscht. Im Folgenden werden zwei mögliche Strategien für die Portüberwachung beschrieben.

### <a name="dependency-agent-tables"></a>Dependency-Agent-Tabellen
Verwenden Sie [VMConnection](/azure/azure-monitor/reference/tables/vmconnection) und [VMBoundPort](/azure/azure-monitor/reference/tables/vmboundport) zum Analysieren von Verbindungen und Ports auf dem Computer. Die Tabelle „VMBoundPort“ wird einmal pro Minute für alle auf dem Computer ausgeführten Prozesse und den Port aktualisiert, an dem der Computer lauscht. Erstellen Sie ähnlich wie für die Warnung bei einem fehlenden Heartbeat eine Warnungsregel für eine Protokollabfrage, um nach Prozessen zu suchen, die beendet wurden, oder um zu warnen, wenn der Computer nicht an einem bestimmten Port lauscht.

### <a name="sample-log-queries"></a>Beispielprotokollabfragen

- **Überprüfen Sie die Anzahl der auf Ihren virtuellen Computern geöffneten Ports. Das ist nützlich, um zu ermitteln, welche virtuellen Computer Schwachstellen in der Konfiguration oder Sicherheitsrisiken aufweisen.**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize by Computer, Machine, Port, Protocol
    | summarize OpenPorts=count() by Computer, Machine
    | order by OpenPorts desc
    ```

- **Listen Sie die gebundenen Ports auf Ihren virtuellen Computern auf. Das ist nützlich, um zu ermitteln, welche virtuellen Computer Schwachstellen in der Konfiguration oder Sicherheitsrisiken aufweisen.**

    ```kusto
    VMBoundPort
    | distinct Computer, Port, ProcessName
    ```


- **Analysieren Sie die Netzwerkaktivität nach Port, um zu ermitteln, wie die jeweilige Anwendung oder der jeweilige Dienst konfiguriert ist.**

    ```kusto
    VMBoundPort
    | where Ip != "127.0.0.1"
    | summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
    | project-away TimeGenerated
    | order by Machine, Computer, Port, Ip, ProcessName
    ```

- **Überprüfen Sie die für Ihre virtuellen Computer gesendeten Bytes und empfangenen Trends.**

    ```kusto
    VMConnection
    | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer
    | order by Computer desc
    | render timechart
    ```

- **Verwenden Sie „Verbindungsfehler im Zeitverlauf“, um festzustellen, ob die Fehlerrate stabil ist oder sich ändert.**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | extend bythehour = datetime_part("hour", TimeGenerated)
    | project bythehour, LinksFailed
    | summarize failCount = count() by bythehour
    | sort by bythehour asc
    | render timechart
    ```

- **Verknüpfen Sie Statustrends, um das Verhalten und den Verbindungsstatus eines Computers zu analysieren.**

    ```kusto
    VMConnection
    | where Computer == <replace this with a computer name, e.g. ‘acme-demo’>
    | summarize  dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h)
    | render timechart
    ```

### <a name="connection-manager"></a>Verbindungs-Manager
Das Feature [Verbindungsmonitor](../../network-watcher/connection-monitor-overview.md) von [Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) wird zum Testen von Verbindungen mit einem Port an einem virtuellen Computer verwendet. Mit einem Test wird überprüft, ob der Computer am Port lauscht und im Netzwerk darauf zugegriffen werden kann.
Für den Verbindungs-Manager muss die Erweiterung Network Watcher auf dem Clientcomputer vorhanden sein, mit der der Test initiiert wird. Auf dem Computer, der getestet wird, muss sie nicht installiert werden. Ausführlichere Informationen finden Sie unter [Tutorial: Überwachen der Netzwerkkommunikation über das Azure-Portal](../../network-watcher/connection-monitor.md).

Für den Verbindungs-Manager fallen zusätzliche Kosten an. Weitere Informationen hierzu finden Sie unter [Network Watcher – Preise](https://azure.microsoft.com/pricing/details/network-watcher/).

## <a name="run-a-process-on-a-local-machine"></a>Ausführen eines Prozesses auf einem lokalen Computer
Für die Überwachung einiger Workloads ist ein lokaler Prozess erforderlich. Ein Beispiel hierfür ist ein PowerShell-Skript, das auf dem lokalen Computer ausgeführt wird, um eine Verbindung mit einer Anwendung herzustellen und Daten zu erfassen oder zu verarbeiten. Sie können [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) verwenden, das Teil von [Azure Automation](../../automation/automation-intro.md) ist, um ein lokales PowerShell-Skript auszuführen. Für Hybrid Runbook Worker fallen keine direkten Gebühren an. Lediglich für die jeweils verwendeten Runbooks entstehen Kosten.

Mit dem Runbook kann auf alle Ressourcen auf dem lokalen Computer zugegriffen werden, um die erforderlichen Daten zu erfassen. Dabei können Daten nicht direkt an Azure Monitor gesendet oder Warnungen direkt erstellt werden. Damit eine Warnung erstellt werden kann, muss mit dem Runbook ein Eintrag in ein benutzerdefiniertes Protokoll geschrieben und das Protokoll anschließend so konfiguriert werden, dass es von Azure Monitor erfasst wird. Erstellen Sie eine Warnungsregel für Protokollabfragen, die bei diesem Protokolleintrag ausgelöst wird.

## <a name="synthetic-transactions"></a>Synthetische Transaktionen
Bei einer synthetischen Transaktion wird eine Verbindung mit einer Anwendung oder einem Dienst auf einem Computer hergestellt, um eine Benutzerverbindung oder den tatsächlichen Benutzerdatenverkehr zu simulieren. Wenn die Anwendung verfügbar ist, können Sie davon ausgehen, dass der Computer ordnungsgemäß ausgeführt wird. Von [Application Insights](../app/app-insights-overview.md) in Azure Monitor wird diese Funktion bereitgestellt. Sie kann nur für Anwendungen verwendet werden, auf die über das Internet zugegriffen werden kann. Für interne Anwendungen müssen Sie eine Firewall öffnen, um für den Test den Zugriff über bestimmte Microsoft-URLs zu ermöglichen. Sie können aber auch eine andere Überwachungslösung wie etwa System Center Operations Manager verwenden.

|Methode | BESCHREIBUNG |
|:---|:---|
| [URL-Test](../app/monitor-web-app-availability.md) | Damit wird sichergestellt, dass HTTP verfügbar ist und eine Webseite zurückgibt. |
| [Mehrstufiger Test](../app/availability-multistep.md) | Damit wird eine Benutzersitzung simuliert. |

## <a name="sql-server"></a>SQL Server

Verwenden Sie [SQL Insights](../insights/sql-insights-overview.md), um SQL Server auf Ihren virtuellen Computern zu überwachen.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Daten in Azure Monitor-Protokollen mithilfe von Protokollabfragen analysieren.](../logs/get-started-queries.md)
* [Erfahren Sie mehr über Warnungen mithilfe von Metriken und Protokollen in Azure Monitor.](../alerts/alerts-overview.md)