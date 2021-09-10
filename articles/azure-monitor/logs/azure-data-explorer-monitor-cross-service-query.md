---
title: Dienstübergreifende Abfragen zwischen Azure Monitor und Azure Data Explorer
description: Fragen Sie Daten in Azure Data Explorer über Azure Log Analytics-Tools und umgekehrt ab, um alle Daten an einem Ort zusammenzuführen und zu analysieren.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 7556469049e072c6915baef60083c306753f8717
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339125"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer"></a>Dienstübergreifende Abfragen – Azure Monitor und Azure Data Explorer
Erstellen Sie dienstübergreifende Abfragen zwischen [Azure Data Explorer](/azure/data-explorer/), [Application Insights](../app/app-insights-overview.md) und [Log Analytics](../logs/data-platform-logs.md).
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Dienstübergreifende Abfragen zwischen Azure Monitor und Azure Data Explorer
Mit dieser Funktion können Sie [dienstübergreifende Abfragen zwischen Azure Data Explorer und Azure Monitor](/azure/data-explorer/query-monitor-data) sowie [dienstübergreifende Abfragen zwischen Azure Monitor und Azure Data Explorer](./azure-monitor-data-explorer-proxy.md) erstellen.

Beispiel (Abfrage von Azure Data Explorer aus Log Analytics):
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
Hierbei wird mit der äußeren Abfrage eine Tabelle im Arbeitsbereich abgefragt und anschließend mit einer anderen Tabelle in einem Azure Data Explorer-Cluster (in diesem Fall „clustername=help“, „databasename=samples“) mithilfe einer neuen „adx()“-Funktion verknüpft. Auf ähnliche Weise können Sie einen anderen Arbeitsbereich aus dem Abfragetext abfragen.

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>Abfragen exportierter Log Analytics-Daten aus dem Azure Blob Storage-Konto

Das Exportieren von Daten aus Azure Monitor in ein Azure Storage-Konto ermöglicht eine kostengünstige Aufbewahrung und bietet die Möglichkeit, Protokolle verschiedenen Regionen neu zuzuordnen.

Verwenden Sie Azure Data Explorer, um Daten abzufragen, die aus Ihrem Log Analytics-Arbeitsbereich exportiert wurden. Nach der Konfiguration stehen unterstützte Tabellen, die von Ihren Arbeitsbereichen an ein Azure Storage-Konto gesendet werden, als Datenquelle für Azure Data Explorer bereit. [Fragen Sie exportierte Daten aus Azure Monitor mit Azure Data Explorer ab](../logs/azure-data-explorer-query-storage.md).

[Ablauf einer Azure Data Explorer-Abfrage aus dem Speicher](media\azure-data-explorer-query-storage\exported-data-query.png)

>[!tip] 
> * Wenn Sie alle Daten aus dem Log Analytics-Arbeitsbereich in ein Azure Storage-Konto oder einen Event Hub exportieren möchten, verwenden Sie das Datenexportfeature von Azure Monitor-Protokollen im Log Analytics-Arbeitsbereich. Informationen hierzu finden Sie unter [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor](/azure/data-explorer/query-monitor-data).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen:
* [Erstellen Sie dienstübergreifende Abfragen zwischen Azure Data Explorer und Azure Monitor](/azure/data-explorer/query-monitor-data). Abfragen von Azure Monitor-Daten aus Azure Data Explorer
* [Erstellen Sie dienstübergreifende Abfragen zwischen Azure Monitor und Azure Data Explorer](./azure-monitor-data-explorer-proxy.md). Abfragen von Azure Data Explorer-Daten aus Azure Monitor
* [Datenexport im Log Analytics-Arbeitsbereich in Azure Monitor](/azure/data-explorer/query-monitor-data). Verknüpfen Sie Azure Blob Storage-Konten mit exportierten Log Analytics-Daten, und fragen Sie Daten ab.