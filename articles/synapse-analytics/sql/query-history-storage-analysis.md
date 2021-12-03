---
title: Speicherung und Analyse von Verlaufsabfragen in Azure Synapse Analytics
description: Die Analyse von Verlaufsabfragen ist eine der wesentlichen Anforderungen von Datentechnikern. Azure Synapse Analytics unterstützt vier Hauptmethoden zur Analyse von Abfrageverlauf und Leistung. Diese umfassen den Abfragespeicher, DMVs, Azure Log Analytics und Azure Data Explorer.
author: mariyaali
ms.author: mariyaali
ms.reviewer: wiassaf
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/28/2021
ms.custom: template-concept
ms.openlocfilehash: 9f6493b2011a06a56f64e47711756b1172300885
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490267"
---
# <a name="historical-query-storage-and-analysis-in-azure-synapse-analytics"></a>Speicherung und Analyse von Verlaufsabfragen in Azure Synapse Analytics

Die Analyse von Verlaufsabfragen ist eine der wesentlichen Anforderungen von Datentechnikern. Azure Synapse Analytics unterstützt vier Hauptmethoden zur Analyse von Abfrageverlauf und Leistung. Diese umfassen den Abfragespeicher, DMVs, Azure Log Analytics und Azure Data Explorer. 

In diesem Artikel erfahren Sie, wie Sie die einzelnen Optionen für Ihre Anforderungen verwenden. Sehen Sie sich Anwendungsfälle an, wenn es um die Analyse des Abfrageverlaufs und die jeweils beste Methode geht.

| **Kundenanforderung** |  **Abfragespeicher** |  **DMVs**    | **Azure Log Analytics** | **Azure Data Explorer** |
|------------- | --- | ----- | ------------- |-------------------|
|**Vorkonfigurierte Lösung** | Muss aktiviert werden | :heavy_check_mark: | Zusätzlicher Dienst erforderlich |    Zusätzlicher Dienst erforderlich|
|**Längere Analysezeiträume** | 30 Tage |    Bis zu 10.000 Verlaufszeilen     | Anpassbar | Anpassbar|
|**Verfügbarkeit wichtiger Metriken** |    Eingeschränkt    | :heavy_check_mark: |    Eingeschränkt    | Anpassbar|
|**Verwendung von SQL für die Analyse** | :heavy_check_mark: | :heavy_check_mark:| KQL erforderlich | SQL-Unterstützung ist eingeschränkt|
|||||

## <a name="query-store"></a>Abfragespeicher

Das Abfragespeicherfeature bietet Einblicke in die Auswahl und Leistung von Abfrageplänen. Er vereinfacht das Beheben von Leistungsproblemen, indem er das schnelle Auffinden von Leistungsabweichungen durch Änderungen an Abfrageplänen ermöglicht. 

Der Abfragespeicher ist für neue Azure Synapse Analytics-Datenbanken nicht standardmäßig aktiviert. Führen Sie den folgenden T-SQL-Befehl aus, um den Abfragespeicher zu aktivieren:

```sql
ALTER DATABASE <database_name>
SET QUERY_STORE = ON;
```

Beispiel:

```sql
ALTER DATABASE [SQLPOOL1]
SET QUERY_STORE = ON;
```

Sie können Aufgaben zur Leistungsüberwachung und Problembehandlung ausführen, indem Sie die zuletzt ausgeführten Abfragen, die Anzahl der Ausführungen, die Abfragen mit der längsten Ausführungsdauer und die Abfragen mit den höchsten physischen E/A-Anteilen ermitteln. Beispielabfragen finden Sie unter [Überwachen der Leistung mit dem Abfragespeicher](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#performance).

Vorteile:
* Schwellenwert von 30 Tagen für Speicherabfragedaten.
* Daten können in demselben Tool verwendet werden, in dem Sie auch die Abfrage ausführen würden.

Bekannte Einschränkung:
* Szenarien für die Analyse sind beim Abfragespeicher für Azure Synapse im Vergleich zur Verwendung von DMVs begrenzt.

## <a name="dmvs"></a>DMVs

Dynamische Verwaltungssichten (Dynamic Management Views, DMVs) sind äußerst nützlich, wenn es um das Sammeln von Informationen zu Abfragewartezeiten, Ausführungsplänen, Arbeitsspeicher usw. geht. Es wird dringend empfohlen, die Abfrage, an der Sie interessiert sind, zu kennzeichnen, um sie später nachverfolgen zu können. Beispiel:

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query');
```

Weitere Informationen zum Kennzeichnen Ihrer Abfragen in Azure Synapse SQL finden Sie unter [Verwenden von Abfragebezeichnungen in Synapse SQL](develop-label.md).

Weitere Informationen zur Verwendung von DMVs zum Überwachen Ihrer Azure Synapse Analytics-Workload finden Sie unter [Überwachen der Workload Ihres dedizierten SQL-Pools mit DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context). Eine Dokumentation zu Katalogsichten, die für Azure Synapse Analytics spezifisch sind, finden Sie unter [Azure Synapse Analytics-Katalogsichten](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views).

Vorteile:
* Daten können im selben Abfragetool verwendet werden.
* DMVs bieten umfangreiche Analyseoptionen.

Bekannte Einschränkungen:
* DMVs sind auf 10.000 Zeilen mit Verlaufseinträgen beschränkt. 
* Sichten werden zurückgesetzt, wenn der Pool angehalten/fortgesetzt wird.

## <a name="log-analytics"></a>Log Analytics
Log Analytics-Arbeitsbereiche können auf einfache Weise im Azure-Portal erstellt werden. Weitere Anweisungen zum Verbinden von Synapse mit Log Analytics finden Sie unter [Überwachen von Workloads – Azure-Portal](../sql-data-warehouse/sql-data-warehouse-monitor-workload-portal.md).

Wie Azure Data Explorer verwendet auch Log Analytics die Kusto-Abfragesprache (KQL). Weitere Informationen zur Kusto-Syntax finden Sie unter [Übersicht über Kusto-Abfragen](/azure/data-explorer/kusto/query/). 

Neben dem konfigurierbaren Aufbewahrungszeitraum wählen Sie den Arbeitsbereich aus, den Sie speziell für die Abfrage in Log Analytics verwenden möchten. Log Analytics bietet Ihnen die Flexibilität, Daten zu speichern sowie Abfragen auszuführen und zu speichern.

Vorteile:
* Azure Log Analytics verfügt über eine anpassbare Protokollaufbewahrungsrichtlinie.

Bekannte Einschränkungen:
* Die Verwendung von KQL erhöht die Lernkurve.
* Eingeschränkte Sichten können sofort protokolliert werden.

## <a name="azure-data-explorer-adx"></a>Azure Data Explorer (ADX)

Azure Data Explorer (ADX) ist ein führender Dienst zur Untersuchung von Daten. Mit diesem Dienst können Verlaufsabfragen aus Azure Synapse Analytics analysiert werden. Informationen zum Einrichten einer ADF-Pipeline (Azure Data Factory) zum Kopieren und Speichern von Protokollen in ADX finden Sie unter [Kopieren von Daten in oder aus Azure Data Explorer](/azure/data-factory/connector-azure-data-explorer). In ADX können Sie leistungsfähige Kusto-Abfragen zum Analysieren Ihrer Protokolle ausführen. Sie können hier auch andere Strategien kombinieren, z. B. zum Abfragen und Laden der DMV-Ausgabe in ADX über ADF.
  
Vorteile:
* ADX bietet eine anpassbare Protokollaufbewahrungsrichtlinie.
* Ausführung leistungsfähiger Abfragen für große Datenmengen, insbesondere Abfragen mit Zeichenfolgensuche.

Bekannte Einschränkung:
* Die Verwendung von KQL erhöht die Lernkurve.

## <a name="next-steps"></a>Nächste Schritte

 - [Azure Data Explorer](/azure/data-explorer/)
 - [Azure Data Factory](/azure/data-factory/)
 - [Log Analytics in Azure Monitor](/azure/azure-monitor/logs/log-analytics-overview)
 - [Azure Synapse SQL-Architektur](overview-architecture.md)
 - [Erste Schritte mit Azure Synapse Analytics](../get-started.md)
