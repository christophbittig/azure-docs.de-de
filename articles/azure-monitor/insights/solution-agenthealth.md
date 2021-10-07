---
title: Lösung für die Agent-Integritätsdiagnose in Azure Monitor | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über die Verwendung der Lösung zum Überwachen der Integrität Ihrer Agents, die Berichte direkt an Log Analytics oder System Center Operations Manager senden.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 33fd14f71e0ef441fc2b33765f7275fc33448e0b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663438"
---
# <a name="agent-health-solution-in-azure-monitor"></a>Lösung für die Agent-Integritätsdiagnose in Azure Monitor
Mithilfe der Lösung für die Agent-Integritätsdiagnose in Azure erfahren Sie, welche Agents nicht reagieren und operative Daten übermitteln. Dazu gehören alle Agents, die Berichte direkt an den Log Analytics-Arbeitsbereich in Azure Monitor oder an eine mit Azure Monitor verbundene System Center Operations Manager-Verwaltungsgruppe senden.

Sie können die Lösung für die Agent-Integritätsdiagnose auch für folgende Aufgaben verwenden:

* Nachverfolgen der Anzahl der bereitgestellten Agents und ihrer geografischen Verteilung
* Ausführen weiterer Abfragen für einen Überblick über die Verteilung der Agents, die in Azure, anderen Cloudumgebungen oder lokal bereitgestellt werden    

## <a name="prerequisites"></a>Voraussetzungen
Vergewissern Sie sich vor dem Bereitstellen dieser Lösung, dass Sie über unterstützte [Windows-Agents](../agents/agent-windows.md) verfügen, die Berichte an den Log Analytics-Arbeitsbereich oder an eine mit Ihrem Arbeitsbereich integrierte [Operations Manager-Verwaltungsgruppe](../agents/om-agents.md) senden.

## <a name="management-packs"></a>Management Packs
Wenn Ihre Operations Manager-Verwaltungsgruppe mit einem Log Analytics-Arbeitsbereich verbunden ist, werden in Operations Manager die folgenden Management Packs installiert. Diese Management Packs werden nach dem Hinzufügen dieser Lösung auch auf direkt verbundenen Windows-Computern installiert.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Die Management Packs müssen weder konfiguriert noch verwaltet werden. Weitere Informationen zur Aktualisierung von Management Packs finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Log Analytics](../agents/om-agents.md).

## <a name="configuration"></a>Konfiguration
Führen Sie die unter [Hinzufügen von Lösungen](solutions.md) beschriebenen Schritte aus, um die Lösung für die Agent-Integritätsdiagnose dem Log Analytics-Arbeitsbereich hinzuzufügen. Es ist keine weitere Konfiguration erforderlich.

## <a name="supported-agents"></a>Unterstützte Agents
In der folgenden Tabelle werden die von dieser Lösung unterstützten verbundenen Quellen beschrieben:

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
| --- | --- | --- |
| Windows-Agents | Ja | Heartbeat-Ereignisse werden von direkten Windows-Agents erfasst.|
| System Center Operations Manager-Verwaltungsgruppe | Ja | Taktereignisse werden von Agents, die Berichte an die Verwaltungsgruppe senden, alle 60 Sekunden erfasst und an Azure Monitor weitergeleitet. Es ist keine direkte Verbindung zwischen Operations Manager-Agents und Azure Monitor erforderlich. Daten von Heartbeat-Ereignissen werden von der Verwaltungsgruppe an den Log Analytics-Arbeitsbereich weitergeleitet.|

## <a name="using-the-solution"></a>Verwenden der Lösung
Wenn Sie die Lösung dem Log Analytics-Arbeitsbereich hinzufügen, wird Ihrem Dashboard die Kachel **Agent-Integritätsdiagnose** hinzugefügt. Auf dieser Kachel werden die Gesamtzahl von Agents und die Anzahl von nicht mehr reagierenden Agents innerhalb der letzten 24 Stunden angezeigt.

![Screenshot der Kachel „Agent-Integritätsdiagnose“ auf dem Dashboard](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Klicken Sie zum Öffnen des Dashboards für die **Agent-Integrität** auf die Kachel **Agent-Integritätsdiagnose**.  Das Dashboard enthält die Spalten, die in der folgenden Tabelle angegeben sind. In jeder Spalte werden die zehn häufigsten Ereignisse entsprechend den Kriterien der Spalte für den angegebenen Zeitbereich aufgeführt. Sie können eine Protokollsuche durchführen, bei der die gesamte Liste ausgegeben wird. Klicken Sie hierzu unter der jeweiligen Spalte auf die Option **Alle anzeigen** oder auf die Spaltenüberschrift.

| Column | BESCHREIBUNG |
|--------|-------------|
| Agent-Anzahl über die Zeit | Ein Trend der Agent-Anzahl für einen Zeitraum von sieben Tagen für Linux- und Windows-Agents|
| Anzahl der nicht reagierenden Agents | Eine Liste der Agents, die während der letzten 24 Stunden keinen Takt gesendet haben|
| Verteilung der Agents nach Betriebssystemtyp | Eine Partition über die Anzahl der Windows- und Linux-Agents in Ihrer Umgebung|
| Verteilung der Agents nach Version | Eine Partition der in Ihrer Umgebung installierten Agent-Versionen und der jeweiligen Anzahl|
| Verteilung der Agents nach Kategorie | Eine Partition der Agent-Kategorien, die Taktereignisse senden: direkte Agents, Operations Manager-Agents oder der Operations Manager-Verwaltungsserver|
| Verteilung der Agents nach Verwaltungsgruppe | Eine Partition der Operations Manager-Verwaltungsgruppen in Ihrer Umgebung|
| Geografischer Standort der Agents | Eine Partition der Länder/Regionen, in denen Sie über Agents verfügen, sowie die Gesamtzahl der jeweils in einem Land bzw. einer Region installierten Agents|
| Anzahl installierter Gateways | Die Anzahl von Servern, auf denen das Log Analytics-Gateway installiert ist, und eine Liste dieser Server|

![Screenshot eines Beispieldashboards der Lösung für die Agent-Integritätsdiagnose](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Protokolldatensätze in Azure Monitor
Die Lösung erstellt im Log Analytics-Arbeitsbereich nur Datensätze vom Typ „Takt“. Die Eigenschaften der Takt-Datensätze sind in der folgenden Tabelle aufgeführt:  

| Eigenschaft | Beschreibung |
| --- | --- |
| `Type` | `Heartbeat`|
| `Category` | `Direct Agent`, `SCOM Agent`oder `SCOM Management Server`|
| `Computer` | Computername|
| `OSType` | Windows- oder Linux-Betriebssystem|
| `OSMajorVersion` | Hauptversion des Betriebssystems|
| `OSMinorVersion` | Nebenversion des Betriebssystems|
| `Version` | Version des Log Analytics- oder Operations Manager-Agents|
| `SCAgentChannel` | `Direct` und/oder `SCManagementServer`|
| `IsGatewayInstalled` | `true`, wenn das Log Analytics-Gateway installiert ist; andernfalls `false`|
| `ComputerIP` | Öffentliche IP-Adresse für einen virtuellen Azure-Computer, sofern verfügbar; Azure-SNAT-Adresse (nicht die private IP-Adresse) für einen virtuellen Computer, der eine private IP-Adresse verwendet |
| `ComputerPrivateIPs` | Liste der privaten IP-Adressen des Computers |
| `RemoteIPCountry` | Geografischer Standort, an dem der Computer bereitgestellt wird|
| `ManagementGroupName` | Name der Operations Manager-Verwaltungsgruppe|
| `SourceComputerId` | Eindeutige ID des Computers|
| `RemoteIPLongitude` | Längengrad des geografischen Standorts des Computers|
| `RemoteIPLatitude` | Breitengrad des geografischen Standorts des Computers|

Jeder Agent, der Berichte an einen Operations Manager-Verwaltungsserver sendet, übermittelt zwei Taktereignisse. Der Wert `SCAgentChannel` der Eigenschaft enthält basierend auf den in Ihrem Abonnement aktivierten Datenquellen und Überwachungslösungen sowohl `Direct` als auch `SCManagementServer`. 

Wie Sie bereits wissen, können Daten aus Lösungen auf zwei unterschiedlichen Wegen gesendet werden:

* Direkt von einem Operations Manager-Verwaltungsserver zu Azure Monitor
* Direkt vom Agent zu Azure Monitor (aufgrund der vom Agent erfassten Datenmenge)

Bei Taktereignissen mit dem Wert `SCManagementServer` stellt der Wert `ComputerIP` die IP-Adresse des Verwaltungsservers dar, da von diesem die Daten hochgeladen werden. Bei Taktereignissen, für die `SCAgentChannel` auf `Direct` festgelegt ist, handelt es sich um die öffentliche IP-Adresse des Agents.  

## <a name="sample-log-searches"></a>Beispiele für Protokollsuchen
Die folgende Tabelle enthält Beispiele für eine Protokollsuche nach Datensätzen, die mit der Lösung erfasst werden.

| Abfrage | BESCHREIBUNG |
|:---|:---|
| Heartbeat &#124; distinct Computer |Gesamtanzahl von Agents |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Anzahl der nicht reagierenden Agents innerhalb der letzten 24 Stunden |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Anzahl der nicht reagierenden Agents innerhalb der letzten 15 Minuten |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Anzahl der Computer im Onlinezustand während der letzten 24 Stunden |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Gesamtzahl der Agents im Offlinezustand während der letzten 30 Minuten (innerhalb der letzten 24 Stunden) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Trend zur Anzahl der Agents für einen Zeitraum nach Betriebssystemtyp|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Verteilung der Agents nach Betriebssystemtyp |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Verteilung der Agents nach Version |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Verteilung der Agents nach Kategorie |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Verteilung der Agents nach Verwaltungsgruppe |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geografischer Standort der Agents |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Anzahl installierter Log Analytics-Gateways |

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Generieren von Warnungen aus Protokollabfragen in Azure Monitor finden Sie unter [Überblick über Warnungen in Microsoft Azure](../alerts/alerts-overview.md). 

