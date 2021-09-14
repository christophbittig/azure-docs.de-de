---
title: Migrieren von Azure Data Lake Analytics zu Azure Synapse Analytics.
description: In diesem Artikel wird beschrieben, wie Sie von Azure Data Lake Analytics zu Azure Synapse Analytics migrieren.
author: lingluo0531
ms.author: lingluo
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: migrate-azure-data-lake-analytics-to-synapse
ms.date: 08/25/2021
ms.openlocfilehash: 34a199fe8f488add4f1f2cdd9357dd9ac718d4f0
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254960"
---
# <a name="migrate-azure-data-lake-analytics-to-azure-synapse-analytics"></a>Migrieren von Azure Data Lake Analytics zu Azure Synapse Analytics

Microsoft hat Azure Synapse Analytics, das sowohl Data Lakes als auch Data Warehouse vereinen soll, am Markt eingeführt, um eine einzigartige Big Data-Analyse zu ermöglichen. Azure Synapse Analytics hilft Kunden dabei, alle unterschiedlichen Daten zu erfassen und zu analysieren, die Effizienz von Daten zu verbessern und zusammenzuarbeiten. Darüber hinaus bietet die Integration von Synapse in Azure Machine Learning und Power BI Organisationen die Möglichkeit, Erkenntnisse aus ihren Daten zu gewinnen und Machine Learning für alle intelligenten Apps auszuführen. 

In diesem Dokument erfahren Sie, wie Sie die Migration von Azure Data Lake Analytics zu Azure Synapse Analytics ausführen können. 

## <a name="recommended-approach"></a>Empfohlene Vorgehensweise
- Schritt 1: Bewerten der Bereitschaft
- Schritt 2: Vorbereiten der Migration
- Schritt 3: Migrieren von Daten und Anwendungsworkloads
- Schritt 4: Umstellung von Azure Data Lake Analytics auf Azure Synapse Analytics

### <a name="step-1-assess-readiness"></a>Schritt 1: Bewerten der Bereitschaft

1. Sehen Sie sich [Apache Spark auf Azure Synapse Analytics](../synapse-analytics/spark/apache-spark-overview.md) an, und machen Sie sich mit den wichtigsten Unterschieden zwischen Azure Data Lake Analytics und Spark auf Azure Synapse Analytics vertraut. 

    |Element | Azure Data Lake Analytics | Spark unter Synapse |
    | --- | --- |--- |
    | Preise  |Pro Analyseeinheit pro Stunde |Pro virtueller Kern-Stunde|
    |Engine     |Azure Data Lake Analytics  |Apache Spark
    |Standardprogrammiersprache    |U-SQL   |T-SQL, Python, Scala, Spark SQL und .NET
    |Projektmappen-Explorer   |Azure Data Lake Storage    |Azure Blob Storage, Azure Data Lake Storage

2. Überprüfen Sie den <a href="#questionnaire">Fragebogen für die Migrationsbewertung</a> und listen Sie alle potenziellen, zu berücksichtigenden Risiken auf. 

### <a name="step-2-prepare-to-migrate"></a>Schritt 2: Vorbereiten der Migration

1.  Identifizieren Sie Aufträge und Daten, die Sie migrieren möchten.
    -   Nutzen Sie diese Gelegenheit, um die Aufträge zu bereinigen, die Sie nicht mehr verwenden. Wenn Sie nicht alle Aufträge gleichzeitig migrieren möchten, nutzen Sie die Zeit, um logische Auftragsgruppen zu finden, die Sie in Phasen migrieren können.
    -   Werten Sie die Größe der Daten aus und machen Sie sich mit dem Datenformat von Apache Spark vertraut. Überprüfen Sie U-SQL Skripts, bewerten Sie die Neuverfassungen von Skripts und machen Sie sich mit dem Codekonzept von Apache Spark vertraut.

2.  Ermitteln Sie, welche Auswirkungen eine Migration auf Ihr Unternehmen hat. Berücksichtigen Sie beispielsweise, ob Sie sich Ausfallzeiten während der Migration leisten können.

3.  Erstellen Sie einen Migrationsplan.

### <a name="step-3-migrate-data-and-application-workload"></a>Schritt 3: Migrieren von Daten und Anwendungsworkload

1.  Migrieren Sie Ihre Daten aus Azure Data Lake Storage Gen1 zu Azure Data Lake Storage Gen2. <br></br>
    Azure Data Lake Storage Gen1 wird im Februar 2024 auslaufen. Weitere Informationen finden Sie in der [ offiziellen Ankündigung](https://azure.microsoft.com/updates/action-required-switch-to-azure-data-lake-storage-gen2-by-29-february-2024/). Wir empfehlen ihnen, die Daten zunächst zu Gen2 zu migrieren. Weitere Informationen finden Sie unter [Grundlegendes zu Apache Spark-Datenformaten für Azure Data Lake Analytics U-SQL-Entwickler](understand-spark-data-formats.md). Verschieben Sie die in U-SQL-Tabellen gespeicherten Daten und Datei, um sie für Azure Synapse Analytics zugänglich zu machen.  Mehr Informationen über die Migrations-Führungslinie finden Sie [hier](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). 

2.  Transformieren Ihrer U-SQL-Skripts in Spark. 
    Navigieren Sie zu [Grundlegendes zu Apache Spark-Codekonzepten für Azure Data Lake Analytics-U-SQL-Entwickler](understand-spark-code-concepts.md), um Ihre U-SQL-Skripts in Spark zu transformieren. 

3.  Transformieren oder erstellen Sie Ihre Auftragsorchestrierung-Pipeline in dem neuen Spark-Programm erneut.

### <a name="step-4-cut-over-from-azure-data-lake-analytics-to-azure-synapse-analytics"></a>Schritt 4: Umstellung von Azure Data Lake Analytics auf Azure Synapse Analytics

Nachdem Sie sicher sind, dass Ihre Anwendungen und Workloads stabil ausgeführt werden, können Sie mit der Verwendung von Azure Synapse Analytics entsprechend Ihren geschäftlichen Szenarien beginnen. Deaktivieren Sie alle verbleibenden Pipelines, die auf Azure Data Lake Analytics ausgeführt werden sowie Ihre Azure Data Lake Analytics-Konten.

<a name="questionnaire"></a>
## <a name="questionnaire-for-migration-assessment"></a>Fragebogen zur Migrationsbewertung 

|Category   |Fragen  |Referenz|
| --- | --- |--- |
|Auswertung der Größe der Migration |Über wie viele Azure Data Lake Analytics-Konten verfügen Sie? Wie viele Pipelines werden verwendet? Wie viele U-SQL-Skripts werden verwendet?| Je mehr Daten und Skripts migriert werden müssen, desto mehr UDO/UDF werden in Skripts verwendet, wodurch die Migration erschwert wird. Die für die Migration erforderliche Zeit und Ressourcen müssen entsprechend der Größe des Projekts gut eingeplant werden.|
|Datenquelle |Wie groß ist die Datenquelle? Welche Arten von Datenformaten werden verarbeitet? |[Grundlegendes zu Apache Spark-Datenformaten für Azure Data Lake Analytics-U-SQL-Entwickler](understand-spark-data-formats.md)|
|Datenausgabe |Behalten Sie die Ausgabedaten zur späteren Verwendung bei? Wie werden die Ausgabedaten in U-SQL-Tabellen gespeichert? | Wenn die Ausgabedaten häufig verwendet und in U-SQL gespeichert werden, müssen Sie die Skripts ändern und die Ausgabedaten auf das von Spark unterstützte Datenformat anpassen.|
|Datenmigration |Haben Sie den Speichermigrationsplan ausgeführt? |[Migrieren von Azure Data Lake Storage von Gen1 zu Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) |
|Transformieren von U-SQL-Skripts|Verwenden Sie UDO/UDF (.NET, Python usw.)? Falls ja, welche Sprache verwenden Sie in Ihrer UDO/UDF und für Probleme, die während der Transformation auftreten? Wird die Sammelabfrage in U-SQL verwendet?|[Grundlegendes zu Apache Spark-Codekonzepten für Azure Data Lake Analytics-U-SQL-Entwickler](understand-spark-code-concepts.md)|

## <a name="next-steps"></a>Nächste Schritte

- [Azure Synapse Analytics](../synapse-analytics/get-started.md)
