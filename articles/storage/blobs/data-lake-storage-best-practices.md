---
title: Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Erfahren Sie mehr über die bewährten Methoden zur Datenerfassung, Datensicherheit und Leistung in Bezug auf die Verwendung von Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: b2d631d165b4a8cd585fbe012d9ab9afaa2e6bf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555430"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen2

Dieser Artikel enthält Empfehlungen zu den bewährten Methoden für die Sicherheit, Resilienz und Verzeichnisstruktur in einem Data Lake Storage Gen2-fähigen Speicherkonto. Empfehlungen zu den bewährten Methoden für die Leistungsoptimierung finden Sie unter [Leistungsoptimierung von Azure Data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md).

## <a name="security-considerations"></a>Sicherheitsüberlegungen

### <a name="use-security-groups-instead-of-individual-users"></a>Verwenden von Sicherheitsgruppen anstelle von einzelnen Benutzer

Verwenden Sie beim Anwenden von Zugriffssteuerungslisten (ACCESS Control Lists, ACLs) immer die Azure AD-Sicherheitsgruppen als zugewiesenen Prinzipal in einem ACL-Eintrag. Widerstehen Sie der Möglichkeit, einzelne Benutzer oder Dienstprinzipale direkt zuzuweisen. Die Verwendung dieser Struktur ermöglicht Ihnen, Benutzer oder Dienstprinzipale hinzuzufügen und zu entfernen, ohne dass Sie ACLs erneut auf eine gesamte Verzeichnisstruktur anwenden müssen. Stattdessen können Sie einfach Benutzer und Dienstprinzipale zur entsprechenden Azure AD-Sicherheitsgruppe hinzufügen oder aus dieser entfernen.

Weitere Informationen zum Anwenden dieser bewährten Methode finden Sie unter [Sicherheitsgruppen](data-lake-storage-access-control-model.md#security-groups).

Allgemeine Informationen zum Data Lake Storage Gen2-Zugriffssteuerungsmodell finden Sie unter [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md).

### <a name="configure-the-azure-storage-firewall-with-azure-service-access"></a>Konfigurieren der Azure Storage-Firewall mit Azure-Dienstzugriff

Aktivieren Sie die Azure Storage-Firewall, um den Vektor von externen Angriffen zu begrenzen. Stellen Sie für den Zugriff auf Ihr Speicherkonto über einen Dienst, wie z. B. [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) eine Instanz dieses Diensts in Ihrem virtuellen Netzwerk zur bereit. Anschließend können Sie die Firewall so konfigurieren, dass sie den Zugriff auf das Speicherkonto für diesen Dienst gewährt.

Weitere Informationen zum Anwenden dieser bewährten Methode finden Sie unter [Gewähren des Zugriffs auf vertrauenswürdige Azure-Dienste](../common/storage-network-security.md).

Allgemeine Sicherheitsempfehlungen finden Sie unter [Sicherheitsempfehlungen für Blob-Speicher](security-recommendations.md)

## <a name="resiliency-considerations"></a>Überlegungen zur Resilienz

Beim Erstellen eines Systems mit Data Lake Storage Gen2 oder einem beliebigen Clouddienst müssen Sie Ihre Verfügbarkeitsanforderungen und die Reaktion auf mögliche Unterbrechungen des Diensts berücksichtigen. Ein Problem kann ggf. einer bestimmten Instanz oder einer gesamten Region zugeordnet sein, sodass es wichtig ist, für beide Fälle über einen Plan zu verfügen. Je nach Vereinbarung zum Servicelevel für Recovery Time Objective und Recovery Point Objective Ihrer Workload, können Sie für die Hochverfügbarkeit und Notfallwiederherstellung eine aggressivere oder weniger aggressive Strategie wählen.

### <a name="high-availability-and-disaster-recovery"></a>Hochverfügbarkeit und Notfallwiederherstellung

Data Lake Storage Gen2 bietet bereits eine Dreifachreplikation als Schutz vor lokalen Hardwarefehlern. Andere Replikationsoptionen, wie z. B. der zonenredundanter Speicher (ZRS) oder geozonenredundanter Speicher (GZRS), verbessern die Verfügbarkeit. Mit diesen Funktionen können Workloads auf eine Dienstunterbrechung reagieren, indem sie zu einer separat replizierten lokalen, oder in einer neuen Region vorhandenen Instanz, wechseln.

Um sich auf einen schwerwiegenden Ausfall einer Region vorzubereiten, stellen Sie sicher, dass Sie Daten in eine andere Region replizieren. Verwenden Sie dazu die Replikationsoptionen, wie georedundanten Speicher (GRS) und georedundanten Speicher mit Lesezugriff (RA-GRS). Berücksichtigen Sie außerdem Ihre Anforderungen in Bezug auf Sonderfälle, wie z. B. Datenbeschädigungen, in denen es ratsam sein kann, regelmäßige Momentaufnahmen als Fallbacklösung zu erstellen. Je nach Wichtigkeit und Größe der Daten können Sie erwägen, rollierende Deltamomentaufnahmen für Zeiträume von 1 Stunde, 6 Stunden und 24 Stunden zu erstellen. Dies richtet sich nach Risikotoleranzen.

Erwägen Sie nicht nur die Auswahl eines geeigneten Replikationsmodells, sondern auch Möglichkeiten, Clients beim automatischen Durchführen eines Failovers zur sekundären Region zu unterstützen. Überwachen Sie dazu die Trigger und die Länge der fehlgeschlagenen Versuche oder senden Sie vielleicht eine Benachrichtigung an die Administratoren, um ihnen einen manuellen Eingriff zu ermöglichen. Beachten Sie, dass abgewogen werden sollte, ob ein Failover oder das Warten auf die Wiedererlangung des Onlinezustands für einen Dienst mit weniger Nachteilen verbunden ist.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Verwenden von Distcp für Datenverschiebungen zwischen zwei Standorten

DistCp ist die Abkürzung von „distributed copy“. Es handelt sich um ein Linux-Befehlszeilentool, das zum Funktionsumfang von Hadoop gehört und verteilte Datenverschiebungen zwischen zwei Speicherorten ermöglicht. Die beiden Standorte können Data Lake Storage Gen2, Hadoop Distributed File System (HDFS) oder S3 sein. Bei diesem Tool werden MapReduce-Aufträge in einem Hadoop-Cluster (z.B. HDInsight) verwendet, um das Aufskalieren auf allen Knoten durchzuführen. Distcp ist die schnellste Möglichkeit zum Verschieben von Big Data-Datenmengen ohne besondere Einrichtungen für die Netzwerkkomprimierung. Distcp verfügt auch über eine Option zum alleinigen Aktualisieren von Deltadaten zwischen zwei Standorten und ermöglicht die Durchführung von automatischen Wiederholungsversuchen und die dynamische Computeskalierung. Dieser Ansatz ist äußerst effizient, was das Replizieren von Elementen wie Hive/Spark-Tabellen betrifft, die über viele große Dateien in einem einzelnen Verzeichnis verfügen, wenn Sie lediglich die geänderten Daten kopieren möchten. Aus diesen Gründen ist Distcp das empfohlene Tool zum Kopieren von Daten zwischen Big Data-Speichern.

Kopieraufträge können von Apache Oozie-Workflows ausgelöst werden, indem Häufigkeits- oder Datentrigger verwendet werden, und über Linux-Cron-Aufträge. Für intensive Replikationsaufträge wird empfohlen, dass Sie einen separaten HDInsight Hadoop-Cluster erstellen, der speziell für die Kopieraufträge optimiert und skaliert werden kann. So wird sichergestellt, dass es für Kopieraufträge nicht zu Konflikten mit kritischen Aufträgen kommt. Falls die Replikation mit einer ausreichend geringen Häufigkeit erfolgt, kann der Cluster zwischen den einzelnen Aufträgen ggf. sogar heruntergefahren werden. Stellen Sie beim Ausführen eines Failovers in eine sekundäre Region sicher, dass in der sekundären Region ein weiterer Cluster erstellt wird, damit neue Daten wieder unter dem primären Data Lake Storage Gen2-Konto repliziert werden können, sobald es wieder verfügbar ist. Beispiele für die Verwendung von DistCp finden Sie unter [Kopieren von Daten zwischen Azure Storage-Blobs und Data Lake Storage Gen2 mithilfe von DistCp](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Verwenden von Azure Data Factory zum Planen von Kopieraufträgen

[Azure Data Factory](../../data-factory/introduction.md) kann auch verwendet werden, um Kopieraufträge mit einer Kopieraktivität zu planen. Es kann sogar ein Intervall über den Kopierassistenten dafür eingerichtet werden. Beachten Sie hierbei, dass für Azure Data Factory eine Begrenzung in Bezug auf die Einheiten der Clouddatenverschiebung (Data Movement Units, DMUs) gilt und für große Datenworkloads irgendwann eine Durchsatz-/Computebeschränkung greift. Außerdem sind mit Azure Data Factory derzeit keine Deltaupdates zwischen Data Lake Storage Gen2-Konten möglich, sodass für Verzeichnisse wie Hive-Tabellen für die Replikation eine vollständige Kopie erforderlich ist. Weitere Informationen zum Kopieren mit Data Factory finden Sie im [Artikel zu Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md).

## <a name="monitoring-considerations"></a>Aspekte der Überwachung

Für Data Lake Storage Gen2 werden im Azure-Portal unter dem Data Lake Storage Gen2-Konto und in Azure Monitor Metriken bereitgestellt. Die Verfügbarkeit von Data Lake Storage Gen2 wird im Azure-Portal angezeigt. Sie müssen Ihre eigenen synthetischen Tests durchführen, um die Verfügbarkeit zu überprüfen, wenn Sie die aktuellste Verfügbarkeit eines Data Lake Storage Gen2-Kontos ermitteln möchten. Andere Metriken wie „Speichernutzung gesamt“, „Lese-/Schreibanforderungen“ und „Eingehend/Ausgehend“ stehen zur Verfügung, um von Überwachungsanwendungen genutzt zu werden, und können auch Warnungen auslösen, wenn Schwellenwerte (z. B. durchschnittliche Wartezeit oder Anzahl der Fehler pro Minute) überschritten werden.

## <a name="directory-layout-considerations"></a>Aspekte des Verzeichnislayouts

Beim Erfassen von Daten ist es wichtig, die Struktur der Daten im Voraus zu planen, damit Sicherheit, Partitionierung und Verarbeitung effektiv genutzt werden können. Viele der folgenden Empfehlungen gelten für alle Big Data-Workloads. Für jede Workload gelten unterschiedliche Anforderungen in Bezug auf die Nutzung der Daten. Unten sind einige häufig verwendete Layouts angegeben, die beim Internet der Dinge (IoT) und bei Batch-Szenarien berücksichtigt werden sollten.

### <a name="iot-structure"></a>IoT-Struktur

Bei den IoT-Workloads können große Datenmengen erfasst werden, die sich über zahlreiche Produkte, Geräte, Organisationen und Kunden erstrecken. Es ist wichtig, das Layout des Verzeichnisses im Voraus zu planen, um die Organisation, die Sicherheit und die effiziente Verarbeitung der Daten für die nachgeschalteten Verbraucher zu gewährleisten. Das folgende Layout kann hierbei als allgemeine Vorlage dienen:

*{Region}/{SubjectMatter(s)}/jjjj/{mm}/{tt}/{hh}/*

Die Zieltelemetrie für ein Flugzeugtriebwerk im Vereinigten Königreich kann beispielsweise wie die folgende Struktur aussehen:

*UK/Planes/BA1293/Engine1/2017/08/11/12/*

In diesem Beispiel können Sie Zugriffssteuerungslisten verwenden, um Regionen und Themen für bestimmte Benutzer und Gruppen einfacher zu schützen, indem Sie das Datum an das Ende der Verzeichnisstruktur setzen. Wenn Sie die Datenstruktur an den Anfang setzen, wäre es viel schwieriger, diese Regionen und Themen zu schützen. Wenn Sie beispielsweise nur Zugriff auf UK-Daten oder bestimmte Ebenen bereitstellen möchten, müssten Sie eine separate Berechtigung für zahlreiche Verzeichnisse in jedem Stundenverzeichnis anwenden. Diese Struktur würde auch die Anzahl der Verzeichnisse im Laufe der Zeit exponentiell erhöhen.

### <a name="batch-jobs-structure"></a>Struktur von Batchaufträgen

Ein häufig verwendeter Ansatz bei der Batchverarbeitung besteht im Platzieren von Daten in einem Verzeichnis vom Type „Eingang“ (In). Nachdem die Daten verarbeitet wurden, werden die neuen Daten dann in einem Verzeichnis des Typs „Ausgang“ (Out) angeordnet, damit sie von nachgelagerten Prozessen genutzt werden können. Diese Verzeichnisstruktur wird manchmal bei Aufträgen verwendet, bei denen einzelne Dateien verarbeitet werden müssen und ggf. kein Massively Parallel Processing für große Datasets erforderlich ist. Wie bei der oben empfohlenen IoT-Struktur auch, verfügt eine gute Verzeichnisstruktur über übergeordnete Verzeichnisse für Dinge wie Region und Themen (z. B. Organisation, Produkt oder Hersteller). Berücksichtigen Sie in der Struktur das Datum und die Uhrzeit, um eine bessere Organisation, gefilterte Suchen, Sicherheit und Automatisierung der Verarbeitung zu ermöglichen. Der Granularitätsgrad für die Datumsstruktur wird durch das Intervall bestimmt, nach dem die Daten hochgeladen oder verarbeitet werden, z.B. stündlich, täglich oder auch monatlich.

Es kann vorkommen, dass die Dateiverarbeitung nicht erfolgreich ist, weil Daten beschädigt sind oder ein unerwartetes Format haben. In diesen Fällen kann für die Verzeichnisstruktur die Nutzung des Ordners **/bad** vorteilhaft sein, in den die Dateien zur weiteren Untersuchung verschoben werden können. Über den Batchauftrag können ggf. auch die Berichterstellung oder die Benachrichtigungsvorgänge für diese fehlerhaften Dateien (*bad* files) abgewickelt werden, um einen manuellen Eingriff zu ermöglichen. Erwägen Sie die Verwendung der folgenden Vorlagenstruktur:

*{Region}/{SubjectMatter(s)}/In/jjjj/{mm}/{tt}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Out/jjjjj/{mm}/{tt}/{hh}/* \
*{Region}/{SubjectMatter (e)}/Bad/jjjj/{mm}/{tt}/{hh}/*

Es kann beispielsweise sein, dass ein Marketingunternehmen tägliche Datenextrakte aus Kundenupdates von seinen Kunden in Nordamerika erhält. Vor und nach der Verarbeitung kann dies ggf. wie im folgenden Codeausschnitt aussehen:

*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*

Bei der normalen Verarbeitung von Batchdaten direkt in Datenbanken, z. B. Hive oder herkömmlichen SQL-Datenbanken, sind die Verzeichnisse **/in** oder **/out** nicht erforderlich, da die Ausgabe bereits in einem separaten Ordner für die Hive-Tabelle oder externe Datenbank angeordnet wird. Beispielsweise würden tägliche Extraktionen von Kunden in ihren jeweiligen Verzeichnissen landen. Anschließend würde ein Dienst wie [Azure Data Factory](../../data-factory/introduction.md), [Apache Oozie](https://oozie.apache.org/) oder [Apache Airflow](https://airflow.apache.org/) einen täglichen Hive- oder Spark-Auftrag auslösen, um die Daten zu verarbeiten und in eine Hive-Tabelle zu schreiben.

## <a name="see-also"></a>Weitere Informationen

- [Zugriffssteuerungsmodell in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
- [Optimieren von Azure Data Lake Storage Gen2 im Hinblick auf Leistung](data-lake-storage-performance-tuning-guidance.md)
- [Leitfaden für den Data Lake](https://github.com/rukmani-msft/adlsguidancedoc/blob/master/Hitchhikers_Guide_to_the_Datalake.md)
- [Übersicht über Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
