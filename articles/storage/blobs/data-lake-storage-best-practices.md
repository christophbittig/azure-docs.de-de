---
title: Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Leistung optimieren, Kosten senken und Ihr Azure Storage-Konto mit aktiviertem Data Lake Storage Gen2 schützen.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 6042acd29325ab6bb887a74e47ceff115d000a9d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360036"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen2

Dieser Artikel enthält Leitfäden mit bewährten Methoden, mit denen Sie die Leistung optimieren, Kosten senken und Ihr Azure Storage-Konto mit aktiviertem Data Lake Storage Gen2 schützen können. 

## <a name="find-documentation"></a>Zugehörige Dokumentation

Azure Data Lake Storage Gen2 ist kein dedizierter Dienst- oder Kontotyp. Es handelt sich vielmehr um eine Reihe von Funktionen zur Unterstützung von Analyseworkloads mit hohem Durchsatz. Die Dokumentation zu Data Lake Storage Gen2 enthält bewährte Methoden und einen Leitfaden für die Verwendung dieser Funktionen. Informationen zu allen anderen Aspekten der Kontoverwaltung, z. B. Einrichten der Netzwerksicherheit, Planen von Hochverfügbarkeit und Notfallwiederherstellung, finden Sie in der [Dokumentation zu Blob Storage](storage-blobs-introduction.md). 

#### <a name="evaluate-feature-support-and-known-issues"></a>Auswerten der Funktionsunterstützung und bekannter Probleme

Konfigurieren Sie Ihr Konto für die Verwendung von Blob Storage-Funktionen nach dem folgenden Muster.

1. Im Artikel [Unterstützung von Blob Storage-Funktionen in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md) erfahren Sie, ob eine Funktion in Ihrem Konto vollständig unterstützt wird. Einige Funktionen werden in Konten mit aktiviertem Data Lake Storage Gen2 noch nicht oder nur teilweise unterstützt. Die Funktionsunterstützung wird ständig erweitert, daher sollten Sie diesen Artikel regelmäßig auf Updates überprüfen.

2. Im Artikel [Bekannte Probleme mit Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md) können Sie feststellen, ob Einschränkungen oder besondere Hinweise für die Funktion vorliegen, die Sie verwenden möchten.

3. Prüfen Sie die Artikel zu Funktionen auf Leitfäden, die sich speziell auf Konten mit aktiviertem Data Lake Storage Gen2 beziehen. 

#### <a name="understand-the-terms-used-in-documentation"></a>Grundlegendes zu den in der Dokumentation verwendeten Begriffen

In den verschiedenen Inhaltsbereichen werden Sie einige kleinere begriffliche Unterschiede feststellen. In der [Dokumentation zu Blob Storage](storage-blobs-introduction.md), wird beispielsweise der Begriff *Blob* anstelle von *Datei* verwendet. Technisch gesehen werden die Dateien, die Sie in Ihrem Speicherkonto erfassen, im Konto zu Blobs. Daher ist der Begriff richtig. Dies kann jedoch zu Verwirrung führen, wenn Sie an den Begriff *Datei* gewöhnt sind. Außerdem wird der Begriff *Container* zur Bezeichnung eines *Dateisystems* verwendet. Diese beiden Begriffe sind als Synonyme zu verstehen.

## <a name="optimize-for-data-ingest"></a>Optimierung für die Datenerfassung

Bei der Erfassung von Daten aus einem Quellsystem können die Hardware und die Netzwerkhardware der Quelle sowie die Netzwerkkonnektivität mit Ihrem Speicherkonto einen Engpass darstellen.  

![Diagramm, das die Faktoren aufzeigt, die beim Erfassen von Daten aus einem Quellsystem in Data Lake Storage Gen2 zu berücksichtigen sind.](./media/data-lake-storage-best-practices/bottleneck.png)

### <a name="source-hardware"></a>Quellhardware

Unabhängig davon, ob Sie in Azure lokale oder virtuelle Computer (VMs) verwenden, sollten Sie die entsprechende Hardware sorgfältig auswählen. Erwägen Sie die Nutzung von SSD-Datenträgern (Solid State Drive) und von Datenträgerhardware mit schnelleren Spindeln. Verwenden Sie für die Netzwerkhardware die schnellstmöglichen Netzwerkschnittstellencontroller (NIC). Für Azure werden virtuelle Azure D14-Computer mit entsprechend leistungsstarker Datenträger- und Netzwerkhardware empfohlen.

### <a name="network-connectivity-to-the-storage-account"></a>Netzwerkkonnektivität mit dem Speicherkonto

Bei der Netzwerkkonnektivität zwischen Ihren Quelldaten und Ihrem Speicherkonto kann es gelegentlich zu einem Engpass kommen. Wenn Ihre Quelldaten lokal gespeichert sind, sollten Sie eine dedizierte Verknüpfung mit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) in Erwägung ziehen. Sind Ihre Quelldaten in Azure gespeichert, erzielen Sie dann eine optimale Leistung, wenn sich die Daten in derselben Azure-Region befinden wie das Data Lake Storage Gen2-Konto.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurieren von Datenerfassungstools für maximale Parallelisierung

Verwenden Sie für eine optimale Leistung den gesamten verfügbaren Durchsatz, indem Sie möglichst viele Lese- und Schreibvorgänge parallel ausführen.

![Leistung von Data Lake Storage Gen2](./media/data-lake-storage-best-practices/throughput.png)

In der folgenden Tabelle finden Sie eine Zusammenfassung der wichtigsten Einstellungen für einige gängige Erfassungstools:  

| Tool               | Einstellungen | 
|--------------------|------------------------------------------------------|
| [DistCp](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)             | -m (Mapper) |
| [Azure Data Factory](../../data-factory/copy-activity-performance.md) | parallelCopies    | 
| [Sqoop](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)          | fs.azure.block.size, -m (Mapper)   |   

> [!NOTE]
> Die Gesamtleistung der Erfassungsvorgänge hängt von anderen Faktoren ab, die speziell für das Tool gelten, das Sie zum Erfassen von Daten verwenden. Den jeweils neuesten Leitfaden finden Sie in der Dokumentation zu den einzelnen Tools, die Sie verwenden möchten.

Sie können Ihr Konto skalieren, um den erforderlichen Durchsatz für sämtliche Analyseszenarios bereitzustellen. Die Standardkonfiguration eines Kontos mit Data Lake Storage Gen2-Aktivierung bietet genügend Durchsatz, um die Anforderungen eines breiten Spektrums an Anwendungsfällen zu erfüllen. Bei Erreichen des Standardlimits kann das Konto für die Bereitstellung eines höheren Durchsatzes konfiguriert werden. Wenden Sie sich hierfür an den [Azure-Support](https://azure.microsoft.com/support/faq/).

## <a name="structure-data-sets"></a>Strukturieren von Datasets

Sie können die Struktur der Daten vorab planen. Dateiformat, Dateigröße und Verzeichnisstruktur können sich auf Leistung und Kosten auswirken. 

### <a name="file-formats"></a>Dateiformate

Daten können in verschiedenen Formaten erfasst werden. Daten können in lesbaren Formaten wie JSON, CSV oder XML oder in komprimierten Binärformaten wie `.tar.gz` vorliegen. Außerdem können Daten in verschiedenen Größen gespeichert werden. Daten können aus umfangreichen Dateien (einige Terabyte) bestehen, z. B. Daten aus einem Export einer SQL-Tabelle aus lokalen Systemen. Daten können auch in Form einer großen Anzahl kleiner Dateien (wenige Kilobyte) vorliegen, z. B. Daten aus Echtzeitereignissen aus einer IoT-Lösung (Internet der Dinge). Durch Auswahl eines entsprechenden Dateiformats und einer geeigneten Dateigröße können Sie die Effizienz und die Kosten optimieren. 

Hadoop unterstützt eine Reihe von Dateiformaten, die für die Speicherung und Verarbeitung strukturierter Daten optimiert sind. Einige gängige Formate sind Avro, Parquet und ORC (Optimized Row Columnar). Bei all diesen Formaten handelt es sich um maschinenlesbare Binärdateiformate. Zur Verringerung der Dateigröße sind sie komprimiert. Sie verfügen über ein in jede Datei eingebettetes Schema, sodass sie selbstbeschreibend sind. Die Formate unterscheiden sich in der Speicherung der Daten. In Avro werden Daten in einem zeilenbasierten Format gespeichert, in Parquet und ORC dagegen in einem spaltenbasierten Format.

Das Avro-Dateiformat können Sie für schreibintensive Ein-/Ausgabemuster oder für Abfragemuster verwenden, bei denen der Abruf mehrerer Datensatzzeilen in ihrer Gesamtheit erforderlich ist. Das Avro-Format eignet sich beispielsweise gut für einen Nachrichtenbus wie Event Hub oder Kafka für das sukzessive Schreiben mehrerer Ereignisse oder Nachrichten.

Die Dateiformate Parquet und ORC eignen sich für leseintensive Ein-/Ausgabemuster oder für Abfragemuster, die sich auf eine Teilmenge von Spalten in den Datensätzen beschränken. Lesetransaktionen können so optimiert werden, dass bestimmte Spalten abgerufen werden, ohne dass der gesamte Datensatz gelesen wird.

Apache Parquet ist ein Open-Source-Dateiformat, das für Analysepipelines mit vielen Lesevorgängen optimiert wurde. Die spaltenbasierte Speicherstruktur von Parquet ermöglicht das Überspringen nicht relevanter Daten. Die Abfragen sind wesentlich effizienter, da der Umfang der Daten eingegrenzt werden kann, die vom Speicher an die Analyse-Engine gesendet werden. Da zudem ähnliche Datentypen (für eine Spalte) zusammen gespeichert werden, unterstützt Parquet effiziente Schemas zur Datenkomprimierung und -codierung. Auf diese Weise werden die Kosten für die Datenspeicherung gesenkt. Dienste wie [Azure Synapse Analytics](../../synapse-analytics/overview-what-is.md), [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) und [Azure Data Factory](../../data-factory/introduction.md) verfügen über native Funktionen, die Parquet-Dateiformate nutzen.

### <a name="file-size"></a>Dateigröße

Größere Dateien ermöglichen bessere Leistung und geringere Kosten. 

In der Regel wird bei Analyse-Engines wie HDInsight für jede Datei ein Verwaltungsaufwand verursacht, der z. B. das Auflisten, das Überprüfen des Zugriffs sowie verschiedene Metadatenvorgänge umfasst. Wenn Sie Ihre Daten in zahlreichen kleinen Dateien speichern, kann sich dies negativ auf die Leistung auswirken. Im Allgemeinen sollten Sie Ihre Daten in größeren Dateien organisieren, um eine bessere Leistung zu erzielen (256 MB bis 100 GB). Dateien mit einer Größe von mehr als 100 GB können von einigen Engines und Anwendungen unter Umständen nicht effizient verarbeitet werden. 

Durch Erhöhung der Dateigröße lassen sich auch die Transaktionskosten reduzieren. Lese- und Schreibvorgänge werden in Schritten von 4 MB abgerechnet, d. h., Sie zahlen für einen Vorgang unabhängig davon, ob die Datei 4 MB oder nur wenige Kilobyte umfasst. Preisinformationen finden Sie unter [Azure Data Lake Storage – Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/).

Mitunter können Datenpipelines Rohdaten mit vielen kleinen Dateien nur begrenzt nutzen. Im Allgemeinen sollte Ihr System einen Prozess zum Aggregieren kleiner Dateien zu großen zur Verwendung durch nachgelagerte Anwendungen aufweisen. Für die Verarbeitung von Daten in Echtzeit können Sie mithilfe einer Echtzeit-Streaming-Engine (z. B. [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md) oder [Spark Streaming](https://databricks.com/glossary/what-is-spark-streaming)) zusammen mit einem Nachrichtenbroker (z. B. [Event Hub](../../event-hubs/event-hubs-about.md) oder [Apache Kafka](https://kafka.apache.org/)) Ihre Daten als größere Dateien speichern. Wenn Sie kleine Dateien in größere aggregieren, sollten Sie zum Speichern ein leseoptimiertes Format wie [Apache Parquet](https://parquet.apache.org/) für die Downstreamverarbeitung auswählen. 

### <a name="directory-structure"></a>Verzeichnisstruktur

Für jede Workload gelten unterschiedliche Anforderungen in Bezug auf die Nutzung der Daten. Nachfolgend sind einige häufig verwendete Layouts angegeben, die beim Internet der Dinge (IoT), bei Batch-Szenarien oder bei der Optimierung für Zeitreihendaten zu berücksichtigen sind.

#### <a name="iot-structure"></a>IoT-Struktur

Bei den IoT-Workloads können große Datenmengen erfasst werden, die sich über zahlreiche Produkte, Geräte, Organisationen und Kunden erstrecken. Es ist wichtig, das Layout des Verzeichnisses im Voraus zu planen, um die Organisation, die Sicherheit und die effiziente Verarbeitung der Daten für die nachgeschalteten Verbraucher zu gewährleisten. Das folgende Layout kann hierbei als allgemeine Vorlage dienen:

`*{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/*`

Die Zieltelemetrie für ein Flugzeugtriebwerk im Vereinigten Königreich kann beispielsweise wie die folgende Struktur aussehen:

`*UK/Planes/BA1293/Engine1/2017/08/11/12/*`

In diesem Beispiel können Sie Zugriffssteuerungslisten verwenden, um Regionen und Themen für bestimmte Benutzer und Gruppen einfacher zu schützen, indem Sie das Datum an das Ende der Verzeichnisstruktur setzen. Wenn Sie die Datenstruktur an den Anfang setzen, wäre es viel schwieriger, diese Regionen und Themen zu schützen. Wenn Sie beispielsweise nur Zugriff auf UK-Daten oder bestimmte Ebenen bereitstellen möchten, müssten Sie eine separate Berechtigung für zahlreiche Verzeichnisse in jedem Stundenverzeichnis anwenden. Diese Struktur würde auch die Anzahl der Verzeichnisse im Laufe der Zeit exponentiell erhöhen.

#### <a name="batch-jobs-structure"></a>Struktur von Batchaufträgen

Ein häufig verwendeter Ansatz bei der Batchverarbeitung besteht im Platzieren von Daten in einem Verzeichnis vom Type „Eingang“ (In). Nachdem die Daten verarbeitet wurden, werden die neuen Daten dann in einem Verzeichnis des Typs „Ausgang“ (Out) angeordnet, damit sie von nachgelagerten Prozessen genutzt werden können. Diese Verzeichnisstruktur wird manchmal bei Aufträgen verwendet, bei denen einzelne Dateien verarbeitet werden müssen und ggf. kein Massively Parallel Processing für große Datasets erforderlich ist. Wie bei der oben empfohlenen IoT-Struktur auch, verfügt eine gute Verzeichnisstruktur über übergeordnete Verzeichnisse für Dinge wie Region und Themen (z. B. Organisation, Produkt oder Hersteller). Berücksichtigen Sie in der Struktur das Datum und die Uhrzeit, um eine bessere Organisation, gefilterte Suchen, Sicherheit und Automatisierung der Verarbeitung zu ermöglichen. Der Granularitätsgrad für die Datumsstruktur wird durch das Intervall bestimmt, nach dem die Daten hochgeladen oder verarbeitet werden, z.B. stündlich, täglich oder auch monatlich.

Es kann vorkommen, dass die Dateiverarbeitung nicht erfolgreich ist, weil Daten beschädigt sind oder ein unerwartetes Format haben. In diesen Fällen kann für die Verzeichnisstruktur die Nutzung des Ordners **/bad** vorteilhaft sein, in den die Dateien zur weiteren Untersuchung verschoben werden können. Über den Batchauftrag können ggf. auch die Berichterstellung oder die Benachrichtigungsvorgänge für diese fehlerhaften Dateien (*bad* files) abgewickelt werden, um einen manuellen Eingriff zu ermöglichen. Erwägen Sie die Verwendung der folgenden Vorlagenstruktur:

`*{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/*\`
`*{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/*\`
`*{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/*`

Es kann beispielsweise sein, dass ein Marketingunternehmen tägliche Datenextrakte aus Kundenupdates von seinen Kunden in Nordamerika erhält. Vor und nach der Verarbeitung kann dies ggf. wie im folgenden Codeausschnitt aussehen:

`*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\`
`*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*`

Bei der normalen Verarbeitung von Batchdaten direkt in Datenbanken, z. B. Hive oder herkömmlichen SQL-Datenbanken, sind die Verzeichnisse **/in** oder **/out** nicht erforderlich, da die Ausgabe bereits in einem separaten Ordner für die Hive-Tabelle oder externe Datenbank angeordnet wird. Beispielsweise würden tägliche Extraktionen von Kunden in ihren jeweiligen Verzeichnissen landen. Anschließend würde ein Dienst wie [Azure Data Factory](../../data-factory/introduction.md), [Apache Oozie](https://oozie.apache.org/) oder [Apache Airflow](https://airflow.apache.org/) einen täglichen Hive- oder Spark-Auftrag auslösen, um die Daten zu verarbeiten und in eine Hive-Tabelle zu schreiben.

#### <a name="time-series-data-structure"></a>Struktur von Zeitreihendaten

Bei Hive-Workloads können Sie durch Bereinigen der Partition von Zeitreihendaten eine Leistungsverbesserung erreichen, da einige Abfragen nur eine Teilmenge der Daten lesen.    

Diese Pipelines, die Zeitreihendaten erfassen, versehen ihre Dateien oftmals mit einer strukturierten Benennung für Dateien und Ordner. Nachfolgend wird ein gängiges Beispiel für Daten vorgestellt, die nach Datum strukturiert sind:

*\DataSet\JJJJ\MM\TT\datafile_JJJJ_MM_TT.tsv*

Beachten Sie, dass Informationen zu Datum/Uhrzeit sowohl im Ordnernamen als auch im Dateinamen angegeben werden.

Im Folgenden wird ein allgemeines Muster für Datums- und Uhrzeitangaben vorgestellt:

*\DataSet\JJJJ\MM\TT\HH\mm\datafile_JJJJ_MM_TT_HH_mm.tsv*

Auch hier sollte die Wahl, die Sie bei der Ordner- und Dateiorganisation treffen, für größere Dateien und eine angemessene Anzahl von Dateien in den einzelnen Ordnern optimiert sein.

## <a name="set-up-security"></a>Einrichten der Sicherheit

Lesen Sie zunächst die Empfehlungen im Artikel [Sicherheitsempfehlungen für Blob Storage](security-recommendations.md). Dort finden Sie bewährte Methoden zum Schutz Ihrer Daten vor versehentlichem oder böswilligem Löschen, zum Schutz von Daten hinter einer Firewall und zur Verwendung von Azure Active Directory (Azure AD) als Grundlage der Identitätsverwaltung. 

Lesen Sie dann den Artikel [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md), um spezifische Leitfäden für Konten mit aktiviertem Data Lake Storage Gen2 zu erhalten. In diesem Artikel erfahren Sie, wie Sie Rollen der rollenbasierten Azure-Zugriffssteuerung (Azure RBAC) zusammen mit Zugriffssteuerungslisten (ACLs) verwenden, um Sicherheitsberechtigungen für Verzeichnisse und Dateien in Ihrem hierarchischen Dateisystem zu erzwingen. 

## <a name="ingest-data"></a>Erfassen von Daten

In diesem Abschnitt werden die unterschiedlichen Quellen von Daten sowie die verschiedenen Methoden hervorgehoben, mit denen Daten in einem Data Lake Storage Gen2-Konto erfasst werden können.

#### <a name="ad-hoc-data"></a>Ad-hoc-Daten:

Kleinere Datasets, die zum Erstellen von Prototypen einer Big Data-Anwendung verwendet werden. Verwenden Sie eines der folgenden Tools zum Erfassen von Daten: 

- Azure-Portal
- [Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [Azure-Befehlszeilenschnittstelle](data-lake-storage-directory-file-acl-cli.md)
- [REST](/rest/api/storageservices/data-lake-storage-gen2)
- [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [AzCopy](../common/storage-use-azcopy-v10.md)

#### <a name="streamed-data"></a>Streamingdaten

Werden von verschiedenen Quellen wie Anwendungen, Geräten und Sensoren generiert. Tools, die zum Erfassen dieser Art von Daten verwendet werden, erfassen und verarbeiten die Daten in der Regel ereignisbezogen in Echtzeit und schreiben die Ereignisse dann in Batches in Ihr Konto. Verwenden Sie eines der folgenden Tools zum Erfassen von Daten:

- [HDInsight Storm](../../hdinsight/storm/apache-storm-write-data-lake-store.md)
- [Azure Stream Analytics](../../stream-analytics/stream-analytics-quick-create-portal.md)

#### <a name="relational-data"></a>Relationale Daten

Relationale Datenbanken erfassen eine große Anzahl von Datensätzen, die bei Verarbeitung über eine Big Data-Pipeline wichtige Erkenntnisse liefern können. Es wird empfohlen, [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) zum Erfassen relationaler Daten zu verwenden.

#### <a name="web-server-log-data"></a>Webserverprotokolldaten

Protokolldateien, die Informationen wie den Verlauf von Seitenanforderungen enthalten. Sie können benutzerdefinierte Skripts oder Anwendungen zum Hochladen dieser Daten schreiben, damit Sie Ihre Datenuploadkomponente flexibel als Teil der größeren Big Data-Anwendung einbinden können. Sie können folgende Tools und SDKs verwenden:

- [Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [Azure-Befehlszeilenschnittstelle](data-lake-storage-directory-file-acl-cli.md)
- [REST](/rest/api/storageservices/data-lake-storage-gen2)
- Azure-SDKs ([.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md) und [Node.js](data-lake-storage-directory-file-acl-javascript.md))
- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)

#### <a name="hdinsight-clusters"></a>HDInsight-Cluster

Die meisten HDInsight-Clustertypen (Hadoop, HBase, Storm) unterstützen Data Lake Storage Gen2 als Datenspeicherrepository. Verwenden Sie eines der folgenden Tools zum Erfassen von Daten:

- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [AzCopy](../common/storage-use-azcopy-v10.md)

#### <a name="hadoop-clusters"></a>Hadoop-Cluster

Diese Cluster können lokal oder in der Cloud ausgeführt werden. Verwenden Sie eines der folgenden Tools zum Erfassen von Daten:

- [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)
- [Apache DistCp](data-lake-storage-use-distcp.md)
- [WANdisco LiveData Migrator for Azure](migrate-gen2-wandisco-live-data-platform.md)
- [Azure Data Box](data-lake-storage-migrate-on-premises-hdfs-cluster.md)

#### <a name="large-data-sets"></a>Umfangreiche Datasets

Das Hochladen von Datasets im Bereich mehrerer Terabyte kann mithilfe der oben beschriebenen Methoden manchmal langsam und kostspielig sein. In solchen Fällen können Sie Azure ExpressRoute verwenden.  

Azure ExpressRoute ermöglicht Ihnen, private Verbindungen zwischen Azure-Rechenzentren und Ihrer lokalen Infrastruktur zu erstellen. Dies ist eine zuverlässige Option zur Übertragung großer Datenmengen. Weitere Informationen finden Sie in der [Dokumentation zu Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-and-analyze-data"></a>Verarbeiten und Analysieren von Daten

Wenn die Daten in Data Lake Storage Gen2 verfügbar sind, können Sie Analysen für diese Daten ausführen, Visualisierungen erstellen und sogar Daten auf den lokalen Computer oder in andere Repositorys, z. B. eine Azure SQL-Datenbank- oder SQL Server-Instanz, herunterladen. In den folgenden Abschnitten werden Tools empfohlen, mit denen Sie Daten analysieren, visualisieren und herunterladen können.

#### <a name="tools-for-analyzing-data"></a>Tools zum Analysieren von Daten

- [Azure Synapse Analytics](../../synapse-analytics/get-started-analyze-storage.md)
- [Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse)

#### <a name="tools-for-visualizing-data"></a>Tools zum Visualisieren von Daten

- [Power BI](/power-query/connectors/datalakestorage)
- [Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration.md)

#### <a name="tools-for-downloading-data"></a>Tools zum Herunterladen von Daten

- Azure-Portal
- [PowerShell](data-lake-storage-directory-file-acl-powershell.md)
- [Azure-Befehlszeilenschnittstelle](data-lake-storage-directory-file-acl-cli.md)
- [REST](/rest/api/storageservices/data-lake-storage-gen2)
- Azure-SDKs ([.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md) und [Node.js](data-lake-storage-directory-file-acl-javascript.md))
- [Azure Storage-Explorer](data-lake-storage-explorer.md)
- [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)
- [Azure Data Factory](../../data-factory/copy-activity-overview.md)
- [Apache DistCp](./data-lake-storage-use-distcp.md)

## <a name="monitor-telemetry"></a>Überwachen von Telemetriedaten

Die Überwachung von Verbrauch und Leistung ist ein wichtiger Bestandteil der Operationalisierung Ihres Diensts. Beispiele hierfür sind häufig ausgeführte Vorgänge, Vorgänge mit hoher Latenz oder Vorgänge, die eine dienstseitige Drosselung zur Folge haben. 

Alle Telemetriedaten für Ihr Speicherkonto sind über [Azure Storage-Protokolle in Azure Monitor](monitor-blob-storage.md) verfügbar. Mit dieser Funktion wird das Speicherkonto in Log Analytics und Event Hubs integriert. Gleichzeitig können Sie Protokolle in einem anderen Speicherkonto archivieren. Die vollständige Liste der Metriken und Ressourcenprotokolle und das zugehörige Schema finden Sie unter [Überwachen von Daten in Azure Storage – Referenz](monitor-blob-storage-reference.md).

Wo Sie die Protokolle speichern, hängt davon ab, wie Sie darauf zugreifen möchten. Wenn Sie beispielsweise nahezu in Echtzeit auf die Protokolle zugreifen und Ereignisse in Protokollen mit anderen Metriken aus Azure Monitor korrelieren möchten, können Sie die Protokolle in einem Log Analytics-Arbeitsbereich speichern. Dadurch können Sie die Protokolle mithilfe von KQL abfragen und Abfragen erstellen, über die die Tabelle `StorageBlobLogs` in Ihrem Arbeitsbereich aufgelistet wird.

Wenn Sie die Protokolle für Abfragen nahezu in Echtzeit und für die Langzeitaufbewahrung speichern möchten, können Sie die Diagnoseeinstellungen so konfigurieren, dass Protokolle an einen Log Analytics-Arbeitsbereich sowie an ein Speicherkonto gesendet werden.

Wenn Sie über eine andere Abfrage-Engine wie Splunk auf die Protokolle zugreifen möchten, können Sie die Diagnoseeinstellungen so konfigurieren, dass Protokolle an einen Event Hub gesendet sowie vom Event Hub im ausgewählten Ziel erfasst werden.

Azure Storage-Protokolle in Azure Monitor können über das Azure-Portal, PowerShell, die Azure-Befehlszeilenschnittstelle und Azure Resource Manager-Vorlagen aktiviert werden. Für bedarfsgerechte Bereitstellungen kann Azure Policy mit vollständiger Unterstützung für Wartungstasks verwendet werden. Weitere Informationen finden Sie unter [Azure/Community-Policy](https://github.com/Azure/Community-Policy/tree/master/Policies/Storage/deploy-storage-monitoring-log-analytics) und [ciphertxt/AzureStoragePolicy](https://github.com/ciphertxt/AzureStoragePolicy).


## <a name="see-also"></a>Weitere Informationen

- [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
- [Leitfaden für den Data Lake](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Übersicht über Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
