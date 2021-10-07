---
title: Optimieren von Azure Data Lake Storage Gen2 im Hinblick auf Leistung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Data Lake Storage Gen2 im Hinblick auf die Leistung optimieren können. Erfassen Sie Daten, strukturieren Sie Ihr Dataset, und führen Sie weitere Aktionen zur Leistungsoptimierung aus.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3cb875f14fe3d9b18b5249a9d2cd9e1901000610
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609306"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Optimieren von Azure Data Lake Storage Gen2 im Hinblick auf Leistung

Azure Data Lake Storage Gen2 unterstützt hohe Durchsätze für E/A-intensive Analysen und Datenverschiebungen. In diesem Artikel erfahren Sie, wie Sie Ihren Durchsatz optimieren und den Datenzugriff effizienter gestalten.

> [!NOTE]
> Die Gesamtleistung Ihrer Analysepipeline hängt auch von Faktoren ab, die für die einzelnen Analytics-Engines spezifisch sind. Eine aktuelle Anleitung zur Optimierung der Workloadleistung finden Sie in der Dokumentation des jeweiligen Systems.

## <a name="optimize-data-ingestion"></a>Optimieren der Datenerfassung

Bei der Erfassung von Daten aus einem Quellsystem können die Hardware und die Netzwerkhardware der Quelle sowie die Netzwerkkonnektivität mit Ihrem Speicherkonto einen Engpass darstellen.

![Diagramm, das die Faktoren aufzeigt, die beim Erfassen von Daten aus einem Quellsystem in Data Lake Storage Gen2 zu berücksichtigen sind.](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

### <a name="source-hardware"></a>Quellhardware

Unabhängig davon, ob Sie in Azure lokale oder virtuelle Computer (VMs) verwenden, sollten Sie die entsprechende Hardware sorgfältig auswählen. Erwägen Sie die Nutzung von SSD-Datenträgern (Solid State Drive) und von Datenträgerhardware mit schnelleren Spindeln. Verwenden Sie für die Netzwerkhardware die schnellstmöglichen Netzwerkschnittstellencontroller (NIC). Für Azure werden virtuelle Azure D14-Computer mit entsprechend leistungsstarker Datenträger- und Netzwerkhardware empfohlen.

### <a name="network-connectivity-to-the-storage-account"></a>Netzwerkkonnektivität mit dem Speicherkonto

Bei der Netzwerkkonnektivität zwischen Ihren Quelldaten und Ihrem Speicherkonto kann es gelegentlich zu einem Engpass kommen. Wenn Ihre Quelldaten lokal gespeichert sind, sollten Sie eine dedizierte Verknüpfung mit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) in Erwägung ziehen. Sind Ihre Quelldaten in Azure gespeichert, erzielen Sie dann eine optimale Leistung, wenn sich die Daten in derselben Azure-Region befinden wie das Data Lake Storage Gen2-Konto.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurieren von Datenerfassungstools für maximale Parallelisierung

Verwenden Sie für eine optimale Leistung den gesamten verfügbaren Durchsatz, indem Sie möglichst viele Lese- und Schreibvorgänge parallel ausführen.

![Leistung von Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

In der folgenden Tabelle finden Sie eine Zusammenfassung der wichtigsten Einstellungen für einige gängige Erfassungstools:

| Tool               | Einstellungen |
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m (Mapper) |
| [Azure Data Factory](../../data-factory/copy-activity-performance.md) | parallelCopies    |
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size, -m (Mapper)   |

Sie können Ihr Konto skalieren, um den erforderlichen Durchsatz für sämtliche Analyseszenarios bereitzustellen. Die Standardkonfiguration eines Kontos mit Data Lake Storage Gen2-Aktivierung bietet genügend Durchsatz, um die Anforderungen eines breiten Spektrums an Anwendungsfällen zu erfüllen. Bei Erreichen des Standardlimits kann das Konto für die Bereitstellung eines höheren Durchsatzes konfiguriert werden. Wenden Sie sich hierfür an den [Azure-Support](https://azure.microsoft.com/support/faq/).

## <a name="structure-your-data-set"></a>Strukturieren Ihres Datasets

Beim Speichern von Daten in einem Konto mit Data Lake Storage Gen2-Aktivierung wirken sich Dateigröße, Anzahl der Dateien und Ordnerstruktur auf die Leistung aus.  Im folgenden Abschnitt werden bewährte Methoden für diese Bereiche beschrieben.

### <a name="file-size"></a>Dateigröße

In der Regel wird bei Analytics-Engines wie HDInsight für jede Datei ein Verwaltungsaufwand verursacht, der z. B. das Auflisten, das Überprüfen des Zugriffs sowie verschiedene Metadatenvorgänge umfasst. Wenn Sie Ihre Daten in zahlreichen kleinen Dateien speichern, kann sich dies negativ auf die Leistung auswirken. Zur Verbesserung der Leistung empfiehlt es sich grundsätzlich, Daten in größeren Dateien (256 MB bis 100 GB) zu organisieren. Dateien mit einer Größe von mehr als 100 GB können von einigen Engines und Anwendungen unter Umständen nicht effizient verarbeitet werden.

Mitunter können Datenpipelines Rohdaten mit vielen kleinen Dateien nur begrenzt nutzen. Im Allgemeinen sollte Ihr System einen Prozess zum Aggregieren kleiner Dateien zu großen zur Verwendung durch nachgelagerte Anwendungen aufweisen. Für die Verarbeitung von Daten in Echtzeit können Sie mithilfe einer Echtzeit-Streaming-Engine (z. B. [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) oder [Spark Streaming](https://databricks.com/glossary/what-is-spark-streaming)) zusammen mit einem Nachrichtenbroker (z. B. [Event Hub](../../event-hubs/event-hubs-about.md) oder [Apache Kafka](https://kafka.apache.org/)) Ihre Daten als größere Dateien speichern.

Wenn Sie kleine Dateien in größere aggregieren, sollten Sie zum Speichern ein leseoptimiertes Format wie [Apache Parquet](https://parquet.apache.org/) für die Downstreamverarbeitung auswählen. Apache Parquet ist ein Open-Source-Dateiformat, das für Analysepipelines mit vielen Lesevorgängen optimiert wurde. Die spaltenbasierte Speicherstruktur von Parquet ermöglicht das Überspringen nicht relevanter Daten. Ihre Abfragen sind wesentlich effizienter, da der Umfang der Daten eingegrenzt werden kann, die vom Speicher an die Analytics-Engine gesendet werden. Da zudem ähnliche Datentypen (für eine Spalte) zusammen gespeichert werden, unterstützt Parquet effiziente Schemas zur Datenkomprimierung und -codierung. Auf diese Weise werden die Kosten für die Datenspeicherung gesenkt. Dienste wie [Azure Synapse Analytics](../../synapse-analytics/overview-what-is.md), [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) und [Azure Data Factory](../../data-factory/introduction.md) verfügen über native Funktionen, die Parquet-Dateiformate nutzen.

Weitere Informationen zu Datenformaten finden Sie im Artikel zu den [Bewährten Methoden im Abschnitt „Datenformate“](data-lake-storage-best-practices.md).

### <a name="organizing-time-series-data-in-folders"></a>Organisieren von Zeitreihendaten in Ordnern

Bei Hive-Workloads können Sie durch Bereinigen der Partition von Zeitreihendaten eine Leistungsverbesserung erreichen, da einige Abfragen nur eine Teilmenge der Daten lesen.

Diese Pipelines, die Zeitreihendaten erfassen, versehen ihre Dateien oftmals mit einer äußerst strukturierten Datei- und Ordnerbenennung. Nachfolgend wird ein sehr gängiges Beispiel für Daten vorgestellt, die nach Datum strukturiert sind:

*\DataSet\JJJJ\MM\TT\datafile_JJJJ_MM_TT.tsv*

Beachten Sie, dass Informationen zu Datum/Uhrzeit sowohl im Ordnernamen als auch im Dateinamen angegeben werden.

Im Folgenden wird ein allgemeines Muster für Datums- und Uhrzeitangaben vorgestellt:

*\DataSet\JJJJ\MM\TT\HH\mm\datafile_JJJJ_MM_TT_HH_mm.tsv*

Auch hier sollte die Wahl, die Sie bei der Ordner- und Dateiorganisation treffen, für größere Dateien und eine angemessene Anzahl von Dateien in den einzelnen Ordnern optimiert sein.

Weitere Vorschläge zur Layoutstruktur von Verzeichnissen finden Sie unter [Verzeichnisstruktur](data-lake-storage-best-practices.md#directory-layout-considerations).

### <a name="access-data-efficiently-with-query-acceleration"></a>Effizientes Zugreifen auf Daten mit der Abfragebeschleunigung

Die Abfragebeschleunigung ermöglicht es Anwendungen und Analyseframeworks, die Datenverarbeitung drastisch zu optimieren. Dabei werden nur die Daten abgerufen, die für die Durchführung eines bestimmten Vorgangs erforderlich sind. So werden weniger Zeit und Verarbeitungsleistung benötigt, um wichtige Einblicke in gespeicherte Daten zu erhalten.

Die Abfragebeschleunigung unterstützt die Filterung von Prädikaten und Spaltenprojektionen. So können Anwendungen Zeilen und Spalten zu dem Zeitpunkt filtern, zu dem die Daten vom Datenträger gelesen werden. Nur die Daten, die die Bedingungen eines Prädikats erfüllen, werden über das Netzwerk an die Anwendung übertragen. Dies reduziert die Netzwerklatenz und die Computekosten.

Weitere Informationen finden Sie unter [Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration.md).

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimieren von E/A-intensiven Aufträgen bei Hadoop- und Spark-Workloads für HDInsight

Bei E/A-intensiven Aufträgen wird der Großteil der Zeit von E/A-Vorgängen beansprucht.  Ein gängiges Beispiel ist ein Kopierauftrag, bei dem nicht nur Lesevorgänge, sondern auch Schreibvorgänge durchgeführt werden.  Ein weiteres Beispiel sind Datenvorbereitungsaufträge, die eine große Menge an Daten lesen, Datentransformationen durchführen und die Daten dann wieder in den Speicher schreiben.  Sie können zwar Aufträge verwenden, die bis zu 100 MB in einem einzelnen Vorgang lesen oder schreiben, ein Puffer dieser Größe beeinträchtigt jedoch unter Umständen die Leistung. Verwenden Sie zur Leistungsoptimierung E/A-Vorgänge mit einer Größe zwischen 4 MB und 16 MB.

### <a name="io-intensive-jobs-with-hdinsight-clusters"></a>E/A-intensive Aufträge mit HDInsight-Clustern

- **HDInsight-Versionen**: Um eine optimale Leistung zu erzielen, verwenden Sie die neueste Version von HDInsight.
- **Regionen**: Platzieren Sie das Data Lake Storage Gen2-Konto in der gleichen Region wie den HDInsight-Cluster.

Ein HDInsight-Cluster besteht aus zwei Hauptknoten und einigen Workerknoten. Jeder Workerknoten stellt eine bestimmte Anzahl von Kernen und eine bestimmte Menge an Speicher bereit, die durch den VM-Typ festgelegt wird.  Bei der Ausführung eines Auftrags ist YARN der Verhandlungspartner für Ressourcen, der den verfügbaren Speicher und die Kerne zur Erstellung von Containern zuordnet.  Jeder Container führt die für den Auftrag erforderlichen Aufgaben durch.  Zur schnellen Verarbeitung von Aufgaben werden Container parallel ausgeführt. Aus diesem Grund wird eine bessere Leistung erzielt, indem Sie so viele Container wie möglich parallel ausführen.

Es gibt drei Ebenen in einem HDInsight-Cluster, die sich optimieren lassen, um die Anzahl von Containern zu erhöhen und den gesamten verfügbaren Durchsatz zu nutzen.

- **Physische Ebene**
- **YARN-Ebene**
- **Workloadebene**

### <a name="physical-layer"></a>Physische Ebene

**Führen Sie Cluster mit einer größeren Anzahl von Knoten und/oder größeren VMs aus.**  Bei einem größeren Cluster können Sie mehr YARN-Container ausführen, wie in der folgenden Abbildung gezeigt wird.

![Diagramm, das zeigt, wie ein größerer Cluster es Ihnen ermöglicht, mehr YARN-Container auszuführen.](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Verwenden Sie VMs mit mehr Netzwerkbandbreite.**  Ist die Netzwerkbandbreite kleiner als der Data Lake Storage Gen2-Durchsatz, kann dies zu einem Engpass führen.  Die Menge der Netzwerkbandbreite variiert je nach VM.  Wählen Sie einen VM-Typ, der die größtmögliche Netzwerkbandbreite aufweist.

### <a name="yarn-layer"></a>YARN-Ebene

**Verwenden Sie kleinere YARN-Container.**  Reduzieren Sie die Größe der einzelnen YARN-Container, um mit derselben Menge an Ressourcen mehr Container zu erstellen.

![Diagramm, das das Ergebnis zeigt, wenn Sie die Größe jedes YARN-Containers verringern, um mehr Container zu erstellen.](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

Abhängig von Ihrer Workload ist stets eine Mindestgröße für YARN-Container erforderlich. Wenn Sie einen zu kleinen Container auswählen, treten bei Ihren Aufträgen Probleme aufgrund von unzureichendem Speicherplatz auf. In der Regel dürfen YARN-Container nicht kleiner als 1 GB sein. Üblich sind YARN-Container mit 3 GB. Für einige Workloads benötigen Sie möglicherweise größere YARN-Container.

**Erhöhen Sie die Anzahl der Kerne pro YARN-Container.**  Erhöhen Sie die Anzahl der Kerne pro Container, um die Anzahl der Aufgaben, die in den einzelnen Containern parallel ausgeführt werden, zu erhöhen.  Dies gilt für Anwendungen wie Spark, die mehrere Aufgaben pro Container ausführt.  Bei Anwendungen wie Hive, die einen einzelnen Thread in jedem Container ausführt, empfiehlt es sich, die Anzahl der Container statt der Anzahl der Kerne pro Container zu erhöhen.

### <a name="workload-layer"></a>Workloadebene

**Verwenden Sie alle verfügbaren Container.**  Legen Sie die Anzahl von Aufgaben auf dieselbe oder eine höhere Anzahl der verfügbaren Containern fest, damit alle Ressourcen ausgelastet sind.

![Diagramm, das die Verwendung aller Container zeigt.](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Aufgaben, die mit Fehlern beendet werden, sind kostspielig.** Sind bei jeder Aufgabe große Mengen an Daten zu verarbeiten, führen fehlerhafte Aufgaben zu einer kostenintensiven Wiederholung.  Aus diesem Grund empfiehlt es sich, mehr Aufgaben zu erstellen, bei denen jeweils eine kleine Menge von Daten verarbeitet wird.

Neben den oben genannten allgemeinen Richtlinien verfügt jedes Analysesystem oder Framework über unterschiedliche Parameter, die Sie zur Leistungsverbesserung optimieren können. Eine aktuelle Anleitung zur Optimierung der Workloadleistung finden Sie in der Dokumentation des jeweiligen Systems.

## <a name="see-also"></a>Siehe auch

- [Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen2](data-lake-storage-best-practices.md)
- [Übersicht über Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
