---
title: Datensammlungsregeln in Azure Monitor (Vorschau)
description: Enthält eine Übersicht über die Datensammlungsregeln in Azure Monitor, z. B. zu Inhalt und Struktur und zur Erstellung und Nutzung.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.custom: references_region
ms.openlocfilehash: 83ad2245ec010bd91907ae27e077f86b4d6b1d5e
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112122395"
---
# <a name="data-collection-rules-in-azure-monitor"></a>Datensammlungsregeln in Azure Monitor
Mit Datensammlungsregeln werden die in Azure Monitor eingehenden Daten definiert, und es wird angegeben, wohin die Daten gesendet bzw. wo sie gespeichert werden sollen. Dieser Artikel enthält eine Übersicht über die Datensammlungsregeln, z. B. zu Inhalt und Struktur und zur Erstellung und Nutzung.

## <a name="input-sources"></a>Eingabequellen
Datensammlungsregeln unterstützen derzeit die folgenden Eingabequellen:

- Azure Monitor-Agent, der auf virtuellen Computern, in VM-Skalierungsgruppen und in Azure Arc für Server ausgeführt wird. Weitere Informationen finden Sie unter [Konfigurieren der Datensammlung für den Azure Monitor-Agent (Vorschau)](../agents/data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Komponenten einer Datensammlungsregel
Eine Datensammlungsregel enthält die unten angegebenen Komponenten.

| Komponente | BESCHREIBUNG |
|:---|:---|
| Datenquellen | Eindeutige Quelle für Überwachungsdaten mit einem eigenen Format und einer eigenen Methode zum Verfügbarmachen der Daten. Beispiele für eine Datenquelle sind Windows-Ereignisprotokoll, Leistungsindikatoren und Syslog. Jede Datenquelle hat einen bestimmten Datenquellentyp (wie unten beschrieben). |
| Datenströme | Eindeutiges Handle zur Beschreibung einer Gruppe von Datenquellen, die unter Verwendung eines gemeinsamen Typs transformiert und schematisiert werden. Für jede Datenquelle wird mindestens ein Datenstrom benötigt, und ein Datenstrom kann von mehreren Datenquellen verwendet werden. Für alle Datenquellen eines Datenstroms wird ein gemeinsames Schema genutzt. Verwenden Sie beispielsweise mehrere Datenströme, wenn Sie eine bestimmte Datenquelle an mehrere Tabellen in demselben Log Analytics-Arbeitsbereich senden möchten. |
| Destinations | Eine Gruppe von Zielen, an die die Daten gesendet werden sollen. Beispiele hierfür wären ein Log Analytics-Arbeitsbereich und Azure Monitor-Metriken. | 
| Datenflüsse | Definition, welche Datenströme an welche Ziele gesendet werden sollen. | 

Datensammlungsregeln werden regional gespeichert und sind in allen öffentlichen Regionen verfügbar, in denen Log Analytics unterstützt wird. Government-Regionen und -Clouds werden momentan nicht unterstützt.

Im folgenden Diagramm sind die Komponenten einer Regel für die Datensammlung sowie deren Beziehungen dargestellt.

[![Diagramm: Datensammlungsregel](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Datenquellentypen
Jede Datenquelle hat einen Datenquellentyp. Mit jedem Typ wird eine eindeutige Gruppe von Eigenschaften definiert, die für jede Datenquelle angegeben werden müssen. Die derzeit verfügbaren Datenquellentypen sind in der folgenden Tabelle angegeben.

| Datenquellentyp | BESCHREIBUNG | 
|:---|:---|
| Erweiterung | Auf VM-Erweiterung basierende Datenquelle |
| performanceCounters | Leistungsindikatoren für Windows und Linux |
| syslog | Syslog-Ereignisse unter Linux |
| windowsEventLogs | Windows-Ereignisprotokoll |


## <a name="limits"></a>Grenzwerte
Informationen zu Grenzwerten, die für die einzelnen Datensammlungsregeln gelten, finden Sie unter [Azure Monitor-Diensteinschränkungen](../service-limits.md#data-collection-rules).

## <a name="data-residency"></a>Datenresidenz 
Datensammlungsregeln werden als Dienst regional bereitgestellt. Eine Regel wird in der von Ihnen angegebenen Region erstellt und gespeichert und in der [gekoppelten Region](../../best-practices-availability-paired-regions.md#azure-regional-pairs) innerhalb desselben geografischen Raums gesichert.  

**Datenresidenz in einer Region**: Die Vorschaufunktion zum Aktivieren der Speicherung von Kundendaten in einer einzelnen Region ist derzeit nur in der Region „Asien, Südosten“ (Singapur) des geografischen Raums „Asien-Pazifik“ und in der Region „Brasilien, Süden“ (São Paulo, Bundesstaat) des geografischen Raums „Brasilien“ verfügbar. Die Residenz in einer einzelnen Region ist in diesen Regionen standardmäßig aktiviert.

## <a name="create-a-dcr"></a>Erstellen einer Datensammlungsregel
Sie können eine Datensammlungsregel derzeit mit einer der folgenden Methoden erstellen:

- [Verwenden Sie das Azure-Portal](../agents/data-collection-rule-azure-monitor-agent.md), um eine Datensammlungsregel zu erstellen und einem oder mehreren virtuellen Computern zuzuordnen.
- Bearbeiten Sie die Datensammlungsregel direkt in JSON, und [übermitteln Sie sie mit der REST-API](/rest/api/monitor/datacollectionrules).
- Erstellen Sie die Datensammlungsregel und die Zuordnungen mit der [Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md).
- Erstellen Sie die Datensammlungsregel und die Zuordnungen mit Azure PowerShell.
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>Beispielregel für die Datensammlung
Die unten angegebene Beispielregel für die Datensammlung gilt für virtuelle Computer mit Azure Monitor-Agent und hat folgende Details:

- Leistungsdaten
  - Sammelt alle 15 Sekunden spezifische Indikatordaten für Prozessor, Arbeitsspeicher, logischen Datenträger und physischen Datenträger und führt einmal pro Minute ein Upload durch.
  - Sammelt alle 30 Sekunden spezifische Prozessindikatoren und führt alle fünf Minuten ein Upload durch.
- Windows-Ereignisse
  - Sammelt Windows-Sicherheitsereignisse und führt einmal pro Minute ein Upload durch.
  - Sammelt Windows-Anwendungs- und -Systemereignisse und führt alle fünf Minuten ein Upload durch.
- syslog
  - Sammelt Ereignisse vom Typ „Debuggen“, „Kritisch“ und „Notfall“ für die Cron-Komponente.
  - Sammelt Ereignisse vom Typ „Warnung“, „Kritisch“ und „Notfall“ für die Syslog-Komponente.
- Destinations
  - Sendet alle Daten an einen Log Analytics-Arbeitsbereich mit dem Namen „centralWorkspace“.

> [!NOTE]
> Eine Erläuterung zu XPath-Abfragen, die verwendet werden, um Angaben zu einer Ereignissammlung in Datensammlungsregeln zu machen, finden Sie unter [Einschränken der Datensammlung mithilfe von benutzerdefinierten XPath-Abfragen](data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries).


```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie eine Datensammlungsregel](data-collection-rule-azure-monitor-agent.md) und eine entsprechende Zuordnung von einem virtuellen Computer aus, indem Sie den Azure Monitor-Agent verwenden.
