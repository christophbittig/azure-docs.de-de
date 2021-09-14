---
title: Leistung der Integration Runtime
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Leistung der Azure Integration Runtime in Azure Data Factory und Azure Synapse Analytics optimieren können.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: 6cc19a1c37071fc7a9af9c3474e0b706283cdf9d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115252"
---
# <a name="optimizing-performance-of-the-azure-integration-runtime"></a>Optimieren der Leistung der Azure Integration Runtime

Datenflüsse werden in Spark-Clustern ausgeführt, die zur Laufzeit gestartet werden. Die Konfiguration für den verwendeten Cluster wird in der Integration Runtime (IR) der Aktivität definiert. Beim Definieren Ihrer Integration Runtime müssen drei Leistungsaspekte berücksichtigt werden: Clustertyp, Clustergröße und Gültigkeitsdauer.

Weitere Informationen zum Erstellen einer Integration Runtime finden Sie unter [Integrationslaufzeit](concepts-integration-runtime.md).

## <a name="cluster-type"></a>Clustertyp

In Bezug auf den Typ des gestarteten Spark-Clusters gibt es drei verfügbare Optionen: „Universell“, „Arbeitsspeicheroptimiert“ und „Für Compute optimiert“.

Standardmäßig werden Cluster vom Typ **Universell** verwendet. Sie sind für die meisten Datenflussworkloads am besten geeignet. Bei dieser Option erhalten Sie normalerweise die beste Mischung aus Leistung und Kostenaufwand.

Falls Ihr Datenfluss über viele Joins und Suchvorgänge verfügt, sollten Sie einen Cluster vom Typ **Arbeitsspeicheroptimiert** verwenden. Bei Clustern vom Typ „Arbeitsspeicheroptimiert“ können mehr Daten im Arbeitsspeicher gespeichert werden, und die Menge an Fehlern der Art „Nicht genügend Arbeitsspeicher“ wird stark reduziert. Die Arbeitsspeicheroptimierung ist mit dem höchsten Preis pro Kern verbunden, aber sie führt auch zu erfolgreicheren Pipelines. Wenn beim Ausführen der Datenflüsse Fehler aufgrund von unzureichendem Arbeitsspeicher auftreten, sollten Sie zu einer arbeitsspeicheroptimierten Azure IR-Konfiguration wechseln. 

Der Typ **Für Compute optimiert** ist für ETL-Workflows nicht ideal und wird für die meisten Produktionsworkloads nicht empfohlen. Für einfachere Datentransformationen, die nicht arbeitsspeicherintensiv sind, z. B. Filtern von Daten oder Hinzufügen von abgeleiteten Spalten, können Cluster vom Typ „Für Compute optimiert“ zu einem günstigeren Preis pro Kern verwendet werden.

## <a name="cluster-size"></a>Clustergröße

Bei Datenflüssen wird die Datenverarbeitung auf unterschiedliche Knoten eines Spark-Clusters verteilt, damit Vorgänge parallel durchgeführt werden können. Bei Verwendung eines Spark-Clusters mit mehr Kernen erhöht sich die Anzahl von Knoten in der Compute-Umgebung. Eine größere Anzahl von Knoten führt zu einer Erhöhung der Verarbeitungsleistung des Datenflusses. Die Erhöhung der Clustergröße ist häufig eine einfache Möglichkeit, um die Verarbeitungszeit zu verkürzen.

Die Standardclustergröße beträgt vier Treiberknoten und vier Workerknoten.  Je mehr Daten Sie verarbeiten, desto größere Cluster sollten Sie verwenden. Unten sind die möglichen Optionen für die Größenanpassung angegeben:

| Workerkerne | Treiberkerne | Total cores (Kerne gesamt) | Notizen |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Nicht verfügbar für die Option „Für Compute optimiert“ |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Die Preise von Datenflüssen werden nach „Stunden der virtuellen Kerne“ berechnet. Dies bedeutet, dass sowohl die Clustergröße als auch die Ausführungszeit einfließen. Beim Hochskalieren erhöhen sich Ihre Clusterkosten pro Minute, aber die Gesamtdauer verringert sich.

> [!TIP]
> Für die Auswirkung der Größe eines Clusters auf die Leistung eines Datenflusses gibt es eine Obergrenze. Je nach Umfang Ihrer Daten gibt es einen bestimmten Punkt, an dem die Erhöhung der Größe eines Clusters nicht mehr zu einer Verbesserung der Leistung führt. Wenn Sie beispielsweise über mehr Knoten als Partitionen mit Daten verfügen, ist das Hinzufügen weiterer Knoten nicht hilfreich. Eine bewährte Methode besteht darin, klein anzufangen und dann hochzuskalieren, um Ihre Leistungsanforderungen zu erfüllen. 

## <a name="time-to-live"></a>Gültigkeitsdauer

Standardmäßig startet jede Datenflussaktivität basierend auf der Azure IR-Konfiguration einen neuen Spark-Cluster. Das Starten des kalten Clusters dauert einige Minuten, und die Datenverarbeitung kann erst gestartet werden, nachdem dieser Vorgang abgeschlossen ist. Wenn Ihre Pipelines mehrere **sequenzielle** Datenflüsse enthalten, können Sie einen Wert für die Gültigkeitsdauer aktivieren. Bei Angabe eines Werts für die Gültigkeitsdauer bleibt ein Cluster nach Abschluss der Ausführung noch eine bestimmte Zeit aktiv. Falls die IR während der Gültigkeitsdauer von einem neuen Auftrag genutzt wird, wird der vorhandene Cluster wiederverwendet und die Startzeit beträchtlich verkürzt. Nachdem der zweite Auftrag abgeschlossen ist, bleibt der Cluster erneut so lange aktiv, wie dies durch die Gültigkeitsdauer vorgegeben ist.

Sie können die Startzeit von warmen Clustern zusätzlich minimieren, indem Sie die Option „Schnelle Wiederverwendung“ in Azure Integration Runtime unter Datenflusseigenschaften festlegen. Wenn Sie diese Einstellung auf TRUE festlegen, wird der Dienst aufgefordert, den vorhandenen Cluster nicht nach jedem Auftrag zu löschen, sondern stattdessen den vorhandenen Cluster wieder zu verwenden. Dadurch bleibt die Compute-Umgebung, die Sie in Ihrer Azure IR-Instanz eingerichtet haben, für den in Ihrer TTL angegebenen Zeitraum erhalten. Diese Option sorgt für die kürzeste Startzeit Ihrer Datenflussaktivitäten, wenn sie über eine Pipeline ausgeführt werden.

Wenn die meisten Ihrer Datenflüsse jedoch parallel ausgeführt werden, wird nicht empfohlen, TTL für die Integration Runtime zu aktivieren, die Sie für diese Aktivitäten verwenden. In einem Cluster kann jeweils nur ein Auftrag ausgeführt werden. Wenn ein Cluster verfügbar ist, aber zwei Datenflüsse gestartet werden, wird er nur für einen davon verwendet. Für den zweiten Auftrag wird ein eigener isolierter Cluster gestartet.

> [!NOTE]
> Die Gültigkeitsdauer ist nicht verfügbar, wenn Sie die Integration Runtime mit automatischer Auflösung verwenden.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel zu Datenflüssen in Bezug auf die Leistung:

- [Datenflussleistung](concepts-data-flow-performance.md)
- [Datenflussaktivität](control-flow-execute-data-flow-activity.md)
- [Überwachen der Datenflussleistung](concepts-data-flow-monitoring.md)
