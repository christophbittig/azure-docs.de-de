---
title: 'Terminologie: Azure Synapse Analytics'
description: Azure Synapse Analytics-Referenzhandbuch für Benutzer
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/02/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d525d3657a48e01240721e4c868fa481db58dc0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074703"
---
# <a name="azure-synapse-analytics-terminology"></a>Terminologie für Azure Synapse Analytics

Dieses Dokument enthält Informationen zu den grundlegenden Konzepten von Azure Synapse Analytics.

## <a name="basics"></a>Grundlagen

Ein **Synapse-Arbeitsbereich** bietet einen sicherungsfähigen abgegrenzten Bereich für die Zusammenarbeit an cloudbasierten Unternehmensanalysen in Azure. Ein Arbeitsbereich wird in einer bestimmten Region bereitgestellt und verfügt über ein zugeordnetes ADLS Gen2-Konto und Dateisystem (zum Speichern temporärer Daten). Ein Arbeitsbereich befindet sich unter einer Ressourcengruppe.

Ein Arbeitsbereich ermöglicht die Ausführung von Analysen mit SQL und Apache Spark. Für SQL- und Spark-Analysen verfügbare Ressourcen werden in SQL- und Spark-**Pools** organisiert.

## <a name="linked-services"></a>Verknüpfte Dienste

Ein Arbeitsbereich kann eine beliebige Anzahl von **verknüpften Diensten** enthalten. Dabei handelt es sich im Wesentlichen um Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die erforderlich sind, damit vom Arbeitsbereich eine Verbindung mit externen Ressourcen hergestellt werden kann.

## <a name="synapse-sql"></a>Synapse-SQL

**Synapse SQL** ermöglicht T-SQL-basierte Analysen in Synapse-Arbeitsbereichen. Synapse SQL hat zwei Verbrauchsmodelle: dediziert und serverlos.  Verwenden Sie für das dedizierte Modell **dedizierte SQL-Pools**. Ein Arbeitsbereich kann eine beliebige Anzahl dieser Pools enthalten. Verwenden Sie zur Nutzung des serverlosen Modells **serverlose SQL-Pools**. Jeder Arbeitsbereich verfügt über einen dieser Pools.

In Synapse Studio können Sie SQL-Pools verwenden, indem Sie **SQL-Skripts** ausführen.

## <a name="apache-spark-for-synapse"></a>Apache Spark für Synapse

Erstellen und verwenden Sie zur Nutzung von Spark-Analysen **serverlose Apache Spark-Pools** in Ihrem Synapse-Arbeitsbereich. Wenn Sie mit der Verwendung eines Spark-Pools beginnen, wird vom Arbeitsbereich eine **Spark-Sitzung** für die Behandlung der mit dieser Sitzung verknüpften Ressourcen erstellt.

Spark kann in Synapse auf zwei Arten verwendet werden:

* Von **Spark Notebooks** werden Scala, PySpark, C# und SparkSQL für Aufgaben im Zusammenhang mit Data Science und Datentechnik verwendet.
* Von **Spark-Auftragsdefinitionen** zur Ausführung von Spark-Batchaufträgen werden JAR-Dateien verwendet.

## <a name="pipelines"></a>Pipelines

Pipelines werden von Azure Synapse für die Datenintegration verwendet und ermöglichen es Ihnen, Daten zwischen Diensten zu verschieben und Aktivitäten zu orchestrieren.

* Bei **Pipelines** handelt es sich um logische Gruppierungen von Aktivitäten, die gemeinsam eine Aufgabe ausführen.
* **Aktivitäten** dienen zum Definieren von Aktionen innerhalb einer Pipeline, die für Daten ausgeführt werden sollen (beispielsweise Kopieren von Daten oder Ausführen einer Notebook-Instanz oder eines SQL-Skripts).
* Bei **Datenflüssen** handelt es sich um eine bestimmte Art von Aktivität für codefreie Datentransformationen mit Synapse Spark im Hintergrund.
* **Trigger:** Ausführen einer Pipeline. Sie kann manuell oder automatisch ausgeführt werden (zeitplanbasiert, rollierendes Fenster oder ereignisbasiert).
* **Integrationsdataset:** benannte Ansicht von Daten, die einfach auf die Daten verweist, die in einer Aktivität als Ein- und Ausgabe verwendet werden sollen. Gehört zu einem verknüpften Dienst.

## <a name="data-explorer-preview"></a>Data Explorer (Vorschau)

Azure Synapse Data Explorer bietet der Kundschaft eine interaktive Abfrage, um Erkenntnisse aus Protokoll- und Telemetriedaten zu gewinnen.

* **Data Explorer-Pools** sind dedizierte Cluster, die zwei oder mehr Serverknoten mit lokalem SSD-Speicher (Cache für heiße Daten) für optimierte Abfrageleistung und mehreren Blobspeichern (Cache für kalte Daten) für Persistenz.
* **Data Explorer-Datenbanken** werden in Data Explorer-Pools gehostet und sind logische Entitäten, die aus Sammlungen von Tabellen und anderen Datenbankobjekten bestehen. Sie können mehrere Datenbanken pro Pool verwenden.
* **Tabellen** sind Datenbankobjekte, die Daten enthalten, die in einem herkömmlichen relationalen Datenmodell organisiert sind. Die Daten werden in Datensätzen gespeichert, die dem klar definierten Tabellenschema von Data Explorer entsprechen, das eine geordnete Liste von Spalten definiert, die jeweils einen Namen und einen skalaren Datentyp haben. Skalare Datentypen können strukturiert (*int*, *real*, *datetime* oder *timespan*), teilweise strukturiert (*dynamic*) oder freier Text (*string*) sein. Der dynamische Typ ähnelt JSON, da er einen einzelnen Skalarwert, ein Array oder ein Wörterbuch mit solchen Werten enthalten kann.
* **Externe Tabellen** sind Tabellen, die auf einen Speicher oder eine SQL-Datenquelle außerhalb der Data Explorer-Datenbank verweisen. Ähnlich wie Tabellen verfügt eine externe Tabelle über ein klar definiertes Schema (eine geordnete Liste von Spaltennamen- und Datentyppaaren). Im Gegensatz zu Data Explorer-Tabellen, bei denen Daten in Data Explorer-Pools erfasst werden, werden externe Tabellen für Daten verwendet, die außerhalb der Pools gespeichert und verwaltet werden. Externe Tabellen speichern keine Daten und werden zum Abfragen oder Exportieren von Daten in einen externen Datenspeicher verwendet.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Azure Synapse Analytics](get-started.md)
* [Erstellen eines Arbeitsbereichs](quickstart-create-workspace.md)
* [Verwenden eines serverlosen SQL-Pools](quickstart-sql-on-demand.md)
* [Erstellen eines Data Explorer-Pools mit Synapse Studio](data-explorer/data-explorer-create-pool-studio.md)
