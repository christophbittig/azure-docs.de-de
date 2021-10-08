---
title: Premium-Tarif für Azure Data Lake Storage
description: Informationen zur Leistungsstufe Premium für Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.openlocfilehash: 5e16a5c6f158b9223c3982b00daba258025f4d03
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596980"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Premium-Tarif für Azure Data Lake Storage

Azure Data Lake Storage Gen2 unterstützt jetzt die [Leistungsstufe Premium](storage-blob-performance-tiers.md#premium-performance). Die Leistungsstufe Premium eignet sich ideal für Anwendungen und Workloads zur Analyse von Big Data, die eine geringe konsistente Latenz erfordern und eine große Anzahl von Transaktionen unterstützen.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Workloads, die von der Leistungsstufe Premium profitieren können

Beispiele für Workloads sind interaktive Workloads, IoT, Streaminganalysen, künstliche Intelligenz und maschinelles Lernen.

**Interaktive Workloads**

Für diese Workloads sind sofortige Aktualisierungen und Benutzerfeedback erforderlich, z. B. bei E-Commerce- und Kartierungsanwendungen, Anwendungen mit interaktivem Video usw. Beispielsweise werden in einer E-Commerce-Anwendung weniger häufig angezeigte Elemente wahrscheinlich nicht zwischengespeichert. Sie müssen dem Kunden jedoch sofort bei Bedarf angezeigt werden. Ein weiteres Beispiel: Datenwissenschaftler, Analysten und Entwickler können zeitkritische Erkenntnisse noch schneller gewinnen, indem sie Abfragen auf Daten anwenden, die in einem Konto der Leistungsstufe Premium gespeichert sind.

**IoT-/Streaminganalysen**

In einem IoT-Szenario können jede Sekunde viele kleinere Schreibvorgänge in die Cloud übermittelt werden. Große Datenmengen können erfasst, zu Analysezwecken aggregiert und anschließend fast sofort gelöscht werden. Dank ihrer hohen Erfassungskapazität kann die Leistungsstufe Premium für diese Art von Workloads effizient zum Einsatz kommen.

**Künstliche Intelligenz/Maschinelles Lernen (KI/ML)**

Bei AI/ML geht es um den Nutzung und Verarbeitung verschiedener Datentypen wie visuelle Elemente, Sprache und Text. Dieser High Performance Compting-Workloadtyp verarbeitet große Datenmengen, die eine schnelle Reaktion und effiziente Erfassungszeiten für die Datenanalyse erfordern.

## <a name="cost-effectiveness"></a>Kosteneffizienz

Die Leistungsstufe Premium weist im Vergleich zur Leistungsstufe Standard höhere Speicherkosten, aber geringere Transaktionskosten auf. Wenn Ihre Anwendungen und Workloads eine große Anzahl von Transaktionen ausführen, kann die Leistungsstufe Premium wirtschaftlich sein.

In der folgenden Tabelle wird die Wirtschaftlichkeit des Tarifs Premium für Azure Data Lake Storage veranschaulicht. Die einzelnen Spalten stellen die Anzahl der Transaktionen in einem Monat dar. Die einzelnen Zeilen stellen den Prozentsatz von Transaktionen dar, die Lesetransaktionen sind. Die einzelnen Zellen in der Tabelle zeigen den Prozentsatz der Kosteneinsparung in Verbindung mit einem Prozentsatz der Lesetransaktionen und der Anzahl ausgeführter Transaktionen.

Angenommen, Ihr Konto befindet sich in der Region USA, Osten 2, die Anzahl der Transaktionen mit Ihrem Konto übersteigt 90 Mio. und 70 % dieser Transaktionen sind Lesetransaktionen. Dann ist die Leistungsstufe Premium wirtschaftlicher.

> [!div class="mx-imgBorder"]
> ![Abbildung hier einfügen](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE]
> Wenn Sie es vorziehen, die Wirtschaftlichkeit auf Grundlage der Anzahl der Transaktionen pro Sekunde für jedes TB Daten auszuwerten, können Sie die Spaltenüberschriften unten in der Tabelle nutzen.

Weitere Informationen zu Preisen finden Sie auf der Seite [Azure Data Lake Storage Gen2: Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="feature-availability"></a>Verfügbarkeit von Funktionen

Einige Blob Storage-Features sind möglicherweise nicht verfügbar oder werden auf der Leistungsstufe Premium nur teilweise unterstützt. Eine vollständige Liste finden Sie unter [Verfügbare Blob Storage-Features in Azure Data Lake Storage Gen2](./storage-feature-support-in-storage-accounts.md). Überprüfen Sie dann die Liste [bekannter Probleme](data-lake-storage-known-issues.md), um etwaige Lücken in der Funktionalität zu bewerten.

## <a name="enabling-the-premium-performance-tier"></a>Aktivieren der Leistungsstufe Premium

Sie können den Tarif Premium für Azure Data Lake Storage verwenden, indem Sie ein BlockBlobStorage-Konto mit auf **Aktiviert** festgelegter Einstellung **Hierarchischer Namespace** erstellen. Eine ausführliche Anleitung finden Sie unter [Erstellen eines BlockBlobStorage-Kontos](../common/storage-account-create.md).

Wenn Sie das Konto erstellen, stellen Sie sicher, dass Sie die Leistungsoption **Premium** und die Kontoart **BlockBlobStorage** wählen.

> [!div class="mx-imgBorder"]
> ![BlockBlobStorage-Konto erstellen](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Aktivieren Sie auf der Seite **Speicherkonto erstellen** auf der Registerkarte **Erweitert** die Einstellung **Hierarchischer Namespace**. Diese Einstellung müssen Sie beim Erstellen des Kontos aktivieren. Später können Sie sie nicht mehr aktivieren.

In der folgenden Abbildung wird diese Einstellung auf der Seite **Speicherkonto erstellen** gezeigt.

> [!div class="mx-imgBorder"]
> ![Einstellung „Hierarchischer Namespace“](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="next-steps"></a>Nächste Schritte

Nutzen Sie den Tarif Premium für Azure Data Lake Storage mit Ihrem bevorzugten Analysedienst wie etwa Azure Databricks, Azure HDInsight und Azure Synapse Analytics.

- [Tutorial: Azure Data Lake Storage Gen2, Azure Databricks und Spark](data-lake-storage-use-databricks-spark.md)
- [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). HDInsight unterstützt derzeit ein Konto, das die Premium-Leistungsstufe sowie einen HBase-Cluster verwendet, für den beschleunigte Schreibvorgänge aktiviert sind.
- [Schnellstart: Erstellen eines Synapse-Arbeitsbereichs](../../synapse-analytics/quickstart-create-workspace.md)
