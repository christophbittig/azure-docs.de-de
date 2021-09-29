---
title: Transformieren von Daten
titleSuffix: Azure Data Factory & Azure Synapse
description: Transformieren oder Verarbeiten von Daten in Azure Data Factory oder Azure Synapse Analytics mithilfe von Hadoop, ML Studio (klassisch) oder Azure Data Lake Analytics.
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 6a5500bdcf551fbbb8c3fc612606ec63bce00557
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124805947"
---
# <a name="transform-data-in-azure-data-factory-and-azure-synapse-analytics"></a>Transformieren oder Verarbeiten von Daten in Azure Data Factory and Azure Synapse Analytics

> [!div class="op_single_selector"]
> * [Mapping Data Flow](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight-Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [ML Studio (klassisch)](transform-data-using-machine-learning.md) 
> * [Gespeicherte Prozedur](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Azure Synapse-Notebook](../synapse-analytics/synapse-notebook-activity.md)
> * [Databricks-Notebook](transform-data-databricks-notebook.md)
> * [JAR in Databricks](transform-data-databricks-jar.md)
> * [Python in Databricks](transform-data-databricks-python.md)
> * [Benutzerdefinierte .NET-Aktivität](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel enthält Informationen zu Datentransformationsaktivitäten in Azure Data Factory- und Synapse-Pipelines, mit denen Sie Ihre Rohdaten in Vorhersagen und Einblicke im gewünschten Umfang transformieren und verarbeiten können. Eine Transformationsaktivität erfolgt in einer Computing-Umgebung wie Azure Databricks oder Azure HDInsight. Sie finden hier Links zu Artikeln mit detaillierten Informationen zu jeder Transformationsaktivität.

Der Dienst unterstützt die folgenden Transformationsaktivitäten, die [Pipelines](concepts-pipelines-activities.md) entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt werden können.

## <a name="transform-natively-in-azure-data-factory-and-azure-synapse-analytics-with-data-flows"></a>Natives Transformieren in Azure Data Factory und Azure Synapse Analytics mit Datenflüssen

### <a name="mapping-data-flows"></a>Zuordnen von Datenflüssen

Mapping Data Flows (Zuordnungsdatenflüsse) sind visuell entworfene Datentransformationen in Azure Data Factory und Azure Synapse. Mit Data Flows können Data Engineers grafische Datentransformationslogik entwickeln, ohne Code schreiben zu müssen. Die daraus resultierenden Datenflüsse werden als Aktivitäten in Pipelines ausgeführt, für die horizontal hochskalierte Spark-Cluster verwendet werden. Datenflussaktivitäten können über die im Dienst vorhandenen Planungs-, Steuerungs-, Fluss- und Überwachungsfunktionen operationalisiert werden. Weitere Informationen finden Sie unter [Mapping Data Flows (Zuordnungsdatenflüsse)](concepts-data-flow-overview.md).

### <a name="data-wrangling"></a>Data Wrangling

Power Query in Azure Data Factory ermöglicht Data Wrangling auf Cloudebene, das Ihnen die Möglichkeit zum iterativen Vorbereiten von Daten ohne Code für die Cloud bietet. Data Wrangling kann in [Power Query Online](/power-query/) integriert werden und stellt Power Query M-Funktionen für Data Wrangling in der Cloud über Spark-Ausführung bereit. Weitere Informationen finden Sie unter [Data Wrangling in Azure Data Factory](wrangling-overview.md).

> [!NOTE]
> Power Query wird derzeit nur in Azure Data Factory und nicht in Azure Synapse unterstützt.  Eine Liste der spezifischen Features, die in den einzelnen Diensten unterstützt werden, finden Sie unter [Verfügbare Features in Azure Data Factory- & Azure Synapse Analytics-Pipelines](../synapse-analytics/data-integration/concepts-data-factory-differences.md).

## <a name="external-transformations"></a>Externe Transformationen

Optional können Sie Transformationen manuell kodieren und die externe Compute-Umgebung selbst verwalten.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive-Aktivität
Die HDInsight Hive-Aktivität in einer Pipeline wendet Hive-Abfragen auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [Hive-Aktivität](transform-data-using-hadoop-hive.md) finden Sie Details zu dieser Aktivität. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig-Aktivität
Die HDInsight Pig-Aktivität in einer Pipeline wendet Pig-Abfragen auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [Pig-Aktivität](transform-data-using-hadoop-pig.md) finden Sie Details zu dieser Aktivität. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-Aktivität
Die HDInsight MapReduce-Aktivität in einer Pipeline wendet MapReduce-Programme auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [MapReduce-Aktivität](transform-data-using-hadoop-map-reduce.md) finden Sie Details zu dieser Aktivität.

### <a name="hdinsight-streaming-activity"></a>HDInsight-Streamingaktivität
Die HDInsight-Streamingaktivität in einer Pipeline wendet Hadoop-Streamingprogramme auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Unter [HDInsight-Streamingaktivität](transform-data-using-hadoop-streaming.md) finden Sie ausführliche Informationen zu dieser Aktivität.

### <a name="hdinsight-spark-activity"></a>HDInsight Spark-Aktivität
Die HDInsight Spark-Aktivität in einer Pipeline führt Spark-Programme in Ihrem eigenen HDInsight-Cluster aus. Weitere Informationen finden Sie unter [Aufrufen von Spark-Programmen mit Azure Data Factory oder Azure Synapse Analytics](transform-data-using-spark.md). 

### <a name="ml-studio-classic-activities"></a>Aktivitäten in ML Studio (klassisch)
Der Dienst ermöglicht Ihnen die einfache Erstellung von Pipelines, die einen veröffentlichten Webdienst von ML Studio (klassisch) für Predictive Analytics nutzen. Mithilfe der [Batchausführungsaktivität](transform-data-using-machine-learning.md) in einer Pipeline können Sie einen Studio (Classic)-Webdienst aufrufen, um Vorhersagen für die Daten im Batch zu treffen.

Im Laufe der Zeit müssen die Vorhersagemodelle in den Bewertungsexperimenten von Azure Machine Learning Studio (Classic) mit neuen Eingabedatasets neu trainiert werden. Wenn Sie mit dem erneuten Trainieren fertig sind, sollten Sie den Bewertungswebdienst mit dem neu trainierten Machine Learning-Modell aktualisieren. Verwenden Sie die [Ressourcenaktualisierungsaktivität](update-machine-learning-models.md), um den Webdienst mit dem neu trainierten Modell zu aktualisieren.  

Ausführliche Informationen zu diesen Aktivitäten in Studio (klassisch) finden Sie unter [Use ML Studio (classic) activities](transform-data-using-machine-learning.md) (Verwenden der Aktivitäten von ML Studio (klassisch)). 

### <a name="stored-procedure-activity"></a>Aktivität „Gespeicherte Prozedur“
Sie können die SQL Server-Aktivität „Gespeicherte Prozedur“ in einer Data Factory-Pipeline verwenden, um eine gespeicherte Prozedur in einem der folgenden Datenspeicher aufzurufen: Azure SQL-Datenbank, Azure Synapse Analytics, SQL Server-Datenbank in Ihrem Unternehmen oder auf einem virtuellen Azure-Computer. Unter [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md) finden Sie Details.  

### <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-Aktivität für Data Lake Analytics
Die U-SQL-Aktivität für Data Lake Analytics wendet ein U-SQL-Skript auf einen Azure Data Lake Analytics-Cluster an. Unter [U-SQL-Aktivität für Data Lake Analytics](transform-data-using-data-lake-analytics.md) finden Sie Details. 

### <a name="azure-synapse-notebook-activity"></a>Azure Synapse-Notebook-Aktivität 

Die Azure Synapse-Notebook-Aktivität in einer Synapse-Pipeline führt ein Synapse-Notebook in Ihrem Azure Synapse-Arbeitsbereich aus. Informationen finden Sie unter [Transformieren von Daten durch Ausführen eines Azure Synapse-Notebooks](../synapse-analytics/synapse-notebook-activity.md).

### <a name="databricks-notebook-activity"></a>Databricks-Notebook-Aktivität

Die Azure Databricks-Notebook-Aktivität in einer Pipeline führt ein Databricks-Notebook in Ihrem Azure Databricks-Arbeitsbereich aus. Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark. Informationen finden Sie unter [Transformieren von Daten durch Ausführen eines Databricks-Notebooks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks-JAR-Aktivität

Die JAR-Aktivität in Azure Databricks in einer Pipeline führt eine Spark JAR-Datei in Ihrem Azure Databricks-Cluster aus. Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark. Informationen finden Sie unter [Transformieren von Daten durch Ausführen einer JAR-Aktivität in Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks-Python-Aktivität

Die Python-Aktivität in Azure Databricks in einer Pipeline führt eine Python-Datei in Ihrem Azure Databricks-Cluster aus. Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark. Informationen finden Sie unter [Transformieren von Daten durch Ausführen einer Python-Aktivität in Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Benutzerdefinierte Aktivität
Wenn Sie Daten auf eine Weise transformieren/verarbeiten müssen, die von Data Factory nicht unterstützt wird, können Sie eine benutzerdefinierte Aktivität mit Ihrer eigenen Datenverarbeitungslogik erstellen und in der Pipeline verwenden. Sie können die benutzerdefinierte .NET-Aktivität so konfigurieren, dass sie entweder mithilfe eines Azure Batch-Diensts oder eines Azure HDInsight-Clusters ausgeführt wird. Unter [Verwenden benutzerdefinierter Aktivitäten](transform-data-using-dotnet-custom-activity.md) finden Sie Einzelheiten. 

Sie können eine benutzerdefinierte Aktivität erstellen, um R-Skripts in Ihrem HDInsight-Cluster mit installiertem R auszuführen. Informationen hierzu finden Sie unter [R-Skript mithilfe von Azure Data Factory- and Synapse-Pipelines ausführen](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Compute-Umgebungen
Sie erstellen einen verknüpften Dienst für die Computeumgebung und verwenden dann den verknüpften Dienst, wenn Sie eine Transformationsaktivität definieren. Es gibt zwei Arten von Computeumgebungen, die unterstützt werden. 

- **Bei Bedarf:** In diesem Fall wird die Compute-Umgebung vollständig vom Dienst verwaltet. Der Dienst erstellt diese Umgebung automatisch, bevor ein Auftrag zur Verarbeitung von Daten übermittelt wird. Sobald der Auftrag abgeschlossen wurde, wird die Umgebung entfernt. Sie können differenzierte Einstellungen für die bedarfsgesteuerte Computeumgebung zur Auftragsausführung, Clusterverwaltung sowie für Bootstrappingaktionen konfigurieren und steuern. 
- **Bring Your Own:** In diesem Fall können Sie Ihre eigene Compute-Umgebung (z. B. HDInsight-Cluster) als verknüpften Dienst registrieren. Die Compute-Umgebung wird von Ihnen verwaltet und vom Dienst zum Ausführen von Aktivitäten verwendet. 

Unter dem Artikel [Verknüpfte Computedienste](compute-linked-services.md) finden Sie Informationen zu unterstützten Computediensten. 

## <a name="next-steps"></a>Nächste Schritte
Ein Beispiel für die Verwendung einer Transformationsaktivität finden Sie im folgenden Tutorial: [Transformieren von Daten mit Spark](tutorial-transform-data-spark-powershell.md)