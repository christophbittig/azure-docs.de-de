---
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: include
ms.date: 10/22/2021
ms.author: jingwang
ms.openlocfilehash: 8317f05587a63bd8f8a81b0949101ea51d39b28a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "131454053"
---
Azure Data Factory unterstützt die folgenden Transformationsaktivitäten, die Pipelines entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt werden können.

| Datentransformationsaktivität | Compute-Umgebung |
|:--- |:--- |
| [Hive](../data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop-Datenströme](../data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../data-factory-spark.md) | HDInsight [Hadoop] |
| [Aktivitäten in ML Studio (klassisch): Batchausführung und Ressourcenaktualisierung](../data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Gespeicherte Prozedur](../data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics oder SQL Server |
| [Data Lake Analytics U-SQL](../data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../data-factory-use-custom-activities.md) |HDInsight [Hadoop] oder Azure Batch |

> [!NOTE]
> Sie können die MapReduce-Aktivität verwenden, um Spark-Programme in Ihrem HDInsight Spark-Cluster auszuführen. Weitere Informationen finden Sie unter [Invoke Spark programs from Azure Data Factory](../data-factory-spark.md) (Aufrufen von Spark-Programmen aus Azure Data Factory).
> Sie können eine benutzerdefinierte Aktivität erstellen, um R-Skripts in Ihrem HDInsight-Cluster mit installiertem R auszuführen. Informationen hierzu finden Sie unter [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample).
> 
> 

