---
title: Wire Data-Lösung in Azure Monitor | Microsoft-Dokumentation
description: Daten zur Kommunikation sind konsolidierte Netzwerk- und Leistungsdaten von Computern mit Log Analytics-Agents. Netzwerkdaten werden mit Ihren Protokolldaten kombiniert, um Ihnen das Korrelieren von Daten zu ermöglichen.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 74d3544fd7e858a685b669ad826bc17e0eb6e64f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312918"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Wire Data 2.0-Lösung (Vorschau) in Azure Monitor (eingestellt)

![Wire Data-Symbol](media/wire-data/wire-data2-symbol.png)

> [!NOTE]
> Die Wire Data-Lösung wurde durch [VM Insights](../vm/vminsights-overview.md) und die [Dienstzuordnungslösung](../vm/service-map.md) ersetzt.  Beide Lösungen nutzen den Log Analytics-Agent und den Dependency-Agent, um Netzwerkverbindungsdaten in Azure Monitor zu erfassen.
>
> Die Unterstützung für die Wire Data-Lösung endet am **31. März 2022**.  Bis zum Einstellungsdatum können bestehende Kunden, die die Wire Data 2.0-Lösung (Vorschau) verwenden, diese weiterhin nutzen.
>
> Neue und bestehende Kunden sollten [VM Insights](../vm/vminsights-enable-overview.md) oder die [Dienstzuordnungslösung](../vm/service-map.md) installieren.  Der von diesen Lösungen gesammelte Satz an Zuordnungsdaten ist mit dem Dataset von Wire Data 2.0 (Vorschau) vergleichbar.  VM Insights umfasst das Dataset der Dienstzuordnung sowie zusätzliche Leistungsdaten und Analysefeatures. Beide Angebote verfügen über [Verbindungen mit Microsoft Sentinel](../../sentinel/connect-data-sources.md#map-data-types-with-microsoft-sentinel-connection-options).

Daten zur Kommunikation sind konsolidierte Netzwerk- und Leistungsdaten, die von mit Windows verbundenen und mit Linux verbundenen Computern mit dem Log Analytics-Agent gesammelt werden, einschließlich der in Ihrer Umgebung von Operations Manager überwachten Computer. Netzwerkdaten werden mit Ihren sonstigen Protokolldaten kombiniert, um Ihnen das Korrelieren von Daten zu ermöglichen.

Zusätzlich zum Log Analytics-Agent verwendet die Wire Data-Lösung Microsoft Dependency-Agents, die Sie auf Computern in Ihrer IT-Infrastruktur installieren. Dependency-Agents überwachen Netzwerkdaten, die für die Netzwerkschichten 2 und 3 des [OSI-Modells](https://en.wikipedia.org/wiki/OSI_model) an ihre Computer und von ihnen gesendet werden, z.B. die verschiedenen verwendeten Protokolle und Ports. Die Daten werden dann mit Agents an Azure Monitor gesendet.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Migrieren zu Azure Monitor VM Insights oder der Dienstzuordnung

Wir stellen häufig fest, dass bei Kunden sowohl Wire Data 2.0 (Vorschau) als auch [VM Insights](../vm/vminsights-overview.md) oder die [Dienstzuordnungslösung](../vm/service-map.md) bereits auf denselben VMs aktiviert ist.  Dies bedeutet, dass das Ersatzangebot auf Ihrer VM aktiviert ist.  Sie können einfach die [Wire Data 2.0-Lösung (Vorschau) aus Ihrem Log Analytics-Arbeitsbereich entfernen](./solutions.md?tabs=portal#remove-a-monitoring-solution).

Wenn Sie über VMs verfügen, auf denen nur Wire Data 2.0 (Vorschau) aktiviert ist, können Sie die VMs in [VM Insights](../vm/vminsights-enable-overview.md) oder die [Dienstzuordnungslösung](../vm/service-map.md) integrieren und dann die [Wire Data 2.0-Lösung (Vorschau) aus Ihrem Log Analytics-Arbeitsbereich entfernen](./solutions.md?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Migrieren Ihrer Abfragen in die Tabelle „VMConnection“ von Azure Monitor VM Insights

### <a name="agents-providing-data"></a>Agents, die Daten bereitstellen

#### <a name="wire-data-20-query"></a>Abfrage in Wire Data 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>Abfrage in VM Insights und der Dienstzuordnung

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>IP-Adressen der Agents, die Daten bereitstellen

#### <a name="wire-data-20-query"></a>Abfrage in Wire Data 2.0

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>Abfrage in VM Insights und der Dienstzuordnung

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Gesamte ausgehende Kommunikation nach Remote-IP-Adresse

#### <a name="wire-data-20-query"></a>Abfrage in Wire Data 2.0

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>Abfrage in VM Insights und der Dienstzuordnung

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Empfangene Bytes nach Protokollname

#### <a name="wire-data-20-query"></a>Abfrage in Wire Data 2.0

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>Abfrage in VM Insights und der Dienstzuordnung

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Menge des Netzwerkdatenverkehrs (in Byte) nach Prozess

#### <a name="wire-data-20-query"></a>Abfrage in Wire Data 2.0

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>Abfrage in VM Insights und der Dienstzuordnung

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Weitere Beispielabfragen

Weitere Beispielabfragen finden Sie in der [Dokumentation zur VM Insights-Protokollsuche](../vm/vminsights-log-search.md) und in der [Dokumentation zu VM Insights-Warnungen](../vm/monitor-virtual-machine-alerts.md).

## <a name="uninstall-wire-data-20-solution"></a>Deinstallieren der Wire Data 2.0-Lösung

Um Wire Data 2.0 zu deinstallieren, müssen Sie einfach nur die Lösung aus Ihrem Log Analytics-Arbeitsbereich entfernen.  Das hat folgende Auswirkungen:

- Das Wire Data Management-Paket wird von den virtuellen Computern entfernt, die mit dem Arbeitsbereich verbunden sind.
- Der Datentyp Wire Data wird nicht mehr in Ihrem Arbeitsbereich angezeigt

Folgen Sie [diesen Anweisungen](./solutions.md?tabs=portal#remove-a-monitoring-solution), um die Wire Data-Lösung zu entfernen.

>[!NOTE]
>Wenn entweder die Dienstzuordnungslösung oder die VM Insights-Lösung in Ihrem Arbeitsbereich vorhanden ist, wird das Management Pack nicht entfernt, da diese Lösungen ebenfalls dieses Management Pack verwenden.

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 Management Packs

Wenn Wire Data in einem Log Analytics-Arbeitsbereich aktiviert ist, wird an alle Windows-Server in diesem Arbeitsbereich ein Management Pack von 300 KB gesendet. Wenn Sie System Center Operations Manager-Agents in einer [verbundenen Verwaltungsgruppe](../agents/om-agents.md) verwenden, wird das Dependency Monitor-Management Pack von System Center Operations Manager bereitgestellt. Wenn die Agents direkt verbunden sind, wird das Management Pack von Azure Monitor bereitgestellt.

Der Name des Management Packs lautet „Microsoft.IntelligencePacks.ApplicationDependencyMonitor“. Es wird in das Verzeichnis „%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\“ geschrieben. Die vom Management Pack verwendete Datenquelle lautet „%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll“.

## <a name="uninstall-the-dependency-agent"></a>Deinstallieren des Dependency-Agents

>[!NOTE]
>Wenn Sie beabsichtigen, Wire Data entweder durch die Dienstzuordnung oder VM Insights zu ersetzen, sollten Sie den Dependency-Agent nicht entfernen.

In den folgenden Abschnitten erfahren Sie, wie Sie den Dependency-Agent entfernen können.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Deinstallieren des Dependency-Agents unter Windows

Ein Administrator kann den Dependency-Agent für Windows über die Systemsteuerung deinstallieren.

Ein Administrator kann auch „%Programfiles%\Microsoft Dependency Agent\Uninstall.exe“ ausführen, um den Dependency-Agent zu deinstallieren.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Deinstallieren des Dependency-Agents unter Linux

Wenn Sie den Dependency-Agent unter Linux vollständig deinstallieren möchten, müssen Sie den Agent selbst und den Connector entfernen, der automatisch mit dem Agent installiert wird. Mit dem folgenden einzelnen Befehl können Sie beide Komponenten deinstallieren:

```bash
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Verwenden der Wire Data 2.0-Lösung

Klicken Sie im Azure-Portal auf der Seite **Übersicht** für Ihren Log Analytics-Arbeitsbereich auf die Kachel **Wire Data 2.0**, um das Wire Data-Dashboard zu öffnen. Das Dashboard enthält die Blätter, die in der folgenden Tabelle angegeben sind. Auf jedem Blatt sind bis zu 10 Einträge aufgeführt, die die Kriterien des Blatts für den angegebenen Bereich und Zeitraum erfüllen. Sie können eine Protokollsuche durchführen, mit der alle Einträge zurückgegeben werden, indem Sie unten auf dem Blatt auf **Alle anzeigen** oder auf die Blattüberschrift klicken.

| **Blatt** | **Beschreibung** |
| --- | --- |
| Agents, die Netzwerkdatenverkehr erfassen | Zeigt die Anzahl der Agents an, die Netzwerkdatenverkehr erfassen, und listet die 10 Computer auf, die am meisten Datenverkehr erfassen. Klicken Sie auf die Zahl, um eine Protokollsuche für <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code> durchzuführen. Klicken Sie auf einen Computer in der Liste, um eine Protokollsuche durchzuführen, die die Gesamtanzahl erfasster Bytes zurückgibt. |
| Lokale Subnetze | Zeigt die Anzahl von lokalen Subnetzen an, die Agents ermittelt haben.  Klicken Sie auf die Anzahl, um eine Protokollsuche für <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> durchzuführen, die alle Subnetze mit der Anzahl von Bytes auflistet, die über jedes einzelne gesendet werden. Klicken Sie auf ein Subnetz in der Liste, um eine Protokollsuche durchzuführen, die die Gesamtanzahl über das Subnetz gesendeter Bytes zurückgibt. |
| Protokolle auf Anwendungsebene | Zeigt die von Agents ermittelte Anzahl der auf Anwendungsebene verwendeten Protokolle. Klicken Sie auf die Zahl, um eine Protokollsuche für <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code> durchzuführen. Klicken Sie auf ein Protokoll, um eine Protokollsuche durchzuführen, die die Gesamtanzahl mithilfe des Protokolls gesendeter Bytes zurückgibt. |

![Wire Data-Dashboard](./media/wire-data/wire-data-dash.png)

Mit dem Blatt **Agents, die Netzwerkdatenverkehr erfassen** können Sie bestimmen, wie viel Netzwerkbandbreite von Computern in Anspruch genommen wird. Mit diesem Blatt können Sie leicht den _„geschwätzigsten“_ Computer in Ihrer Umgebung ermitteln. Solche Computer könnten überlastet sein, nicht normal arbeiten oder mehr Netzwerkressourcen als normal verwenden.

![Screenshot des Blatts „Agents, die Netzwerkdatenverkehr erfassen“ im Wire Data 2.0-Dashboard mit der von jedem Computer genutzten Netzwerkbandbreite](./media/wire-data/log-search-example01.png)

Auf ähnliche Weise können Sie das Blatt **Lokale Subnetze** verwenden, um zu bestimmen, wie viel Netzwerkdatenverkehr über die Subnetze verschoben wird. Benutzer definieren Subnetze oft um kritische Bereiche für ihre Anwendungen herum. Dieses Blatt bietet einen Einblick in die Bereiche.

![Screenshot des Blatts „Lokale Subnetze“ im Wire Data 2.0-Dashboard mit der von jedem lokalen Subnetz genutzten Netzwerkbandbreite.](./media/wire-data/log-search-example02.png)

Das Blatt **Protokolle auf Anwendungsebene** ist nützlich, da es hilfreich ist, zu wissen, welche Protokolle verwendet werden. Sie könnten z.B. erwarten, dass SSH nicht in Ihrer Netzwerkumgebung verwendet wird. Die im Blatt angezeigten Informationen können Ihre Erwartungen schnell bestätigen oder widerlegen.

![Screenshot des Blatts „Protokolle auf Anwendungsebene“ im Wire Data 2.0-Dashboard mit der von jedem Protokoll genutzten Netzwerkbandbreite](./media/wire-data/log-search-example03.png)

Es ist auch hilfreich zu wissen, ob der Protokolldatenverkehr mit der Zeit steigt oder sinkt. Wenn z.B. die Menge der von einer Anwendung übertragenen Daten steigt, dann könnte dies ein Aspekt sein, über den Sie informiert sein müssen, oder den Sie erwähnenswert finden.

## <a name="input-data"></a>Eingabedaten

Wire Data sammelt Metadaten über den Netzwerkverkehr mit den Agents, die Sie aktiviert haben. Jeder Agent sendet alle 15 Sekunden Daten.

## <a name="output-data"></a>Ausgabedaten

Ein Datensatz mit dem Typ _WireData_ wird für jeden Eingabedatentyp erstellt. Die Eigenschaften der WireData-Datensätze sind in der folgenden Tabelle aufgeführt:

| Eigenschaft | BESCHREIBUNG |
|---|---|
| Computer | Name des Computers, auf dem Daten gesammelt wurden |
| TimeGenerated | Uhrzeit des Datensatzes |
| LocalIP | IP-Adresse des lokalen Computers |
| SessionState | Verbunden oder getrennt |
| ReceivedBytes | Summe empfangener Bytes |
| ProtocolName | Name des verwendeten Netzwerkprotokolls |
| IPVersion | IP-Version |
| Direction | Eingehend oder ausgehend |
| MaliciousIP | IP-Adresse einer bekannten schädlichen Quelle |
| severity | Vermuteter Malwareschweregrad |
| RemoteIPCountry | Land/Region der Remote-IP-Adresse |
| ManagementGroupName | Name der Operations Manager-Verwaltungsgruppe |
| SourceSystem | Quelle, auf der Daten gesammelt wurden |
| SessionStartTime | Startzeit der Sitzung |
| SessionEndTime | Endzeit der Sitzung |
| LocalSubnet | Subnetz, in dem Daten gesammelt wurden |
| LocalPortNumber | Lokale Portnummer |
| RemoteIP | Vom Remotecomputer verwendete Remote-IP-Adresse |
| RemotePortNumber | Von der Remote-IP-Adresse verwendete Portnummer |
| SessionID | Ein eindeutiger Wert, der die Kommunikationssitzung zwischen zwei IP-Adressen identifiziert |
| SentBytes | Anzahl der gesendeten Bytes |
| TotalBytes | Gesamtanzahl der während der Sitzung gesendeten Bytes |
| ApplicationProtocol | Typ des verwendeten Netzwerkprotokolls   |
| ProcessID | Windows-Prozess-ID |
| ProcessName | Pfad und Dateiname des Prozesses |
| RemoteIPLongitude | IP-Längengradwert |
| RemoteIPLatitude | IP-Breitengradwert |

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den Anforderungen und Methoden für die Aktivierung der Überwachung für Ihre virtuellen Computer finden Sie unter [Bereitstellen von VM Insights](../vm/vminsights-enable-overview.md).
- [Durchsuchen von Protokollen](../logs/log-query-overview.md) und darüber, wie Sie ausführliche Wire Data-Suchdatensätze anzeigen.
