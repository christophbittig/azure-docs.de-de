---
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 850333ff1d0a2668dcd0553f2d03562fb2c046a8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624411"
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

