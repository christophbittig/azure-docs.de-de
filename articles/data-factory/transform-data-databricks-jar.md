---
title: Transformieren von Daten mit JAR in Databricks
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Daten verarbeiten oder transformieren, indem Sie ein Databricks-Jar in einer Azure Data Factory-Pipeline ausführen.
ms.service: data-factory
ms.subservice: tutorials
ms.custom: synapse
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: d713decf0a82b5e501b7c2065a358e8ccc754d8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122639921"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformieren von Daten durch Ausführen einer JAR-Aktivität in Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die JAR-Aktivität in Azure Databricks in einer [Data Factory-Pipeline](concepts-pipelines-activities.md) führt eine Spark JAR-Datei in Ihrem Azure Databricks-Cluster aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.  Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark.

Das folgende Video enthält eine 11-minütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definition der Databricks-JAR-Aktivität

Dies ist die JSON-Beispieldefinition der JAR-Aktivität in Databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Eigenschaften der Databricks-JAR-Aktivität

Die folgende Tabelle beschreibt die JSON-Eigenschaften, die in der JSON-Definition verwendet werden:

|Eigenschaft|BESCHREIBUNG|Erforderlich|
|:--|---|:-:|
|name|Der Name der Aktivität in der Pipeline.|Ja|
|description|Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.|Nein|
|type|Bei JAR-Aktivitäten in Databricks lautet der Aktivitätstyp DatabricksSparkJar.|Ja|
|linkedServiceName|Der Name des verknüpften Databricks-Diensts, in dem die JAR-Aktivität ausgeführt wird. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md).|Ja|
|mainClassName|Der vollständige Name der Klasse, die die auszuführende Hauptmethode enthält. Diese Klasse muss in einer JAR-Datei enthalten sein, die als Bibliothek bereitgestellt wird. Eine JAR-Datei kann mehrere Klassen enthalten. Jede der Klassen kann eine Main-Methode enthalten.|Ja|
|parameters|Parameter, die an die Hauptmethode übergeben werden. Diese Eigenschaft ist ein Array von Zeichenfolgen.|Nein|
|libraries|Eine Liste der Bibliotheken, die in dem Cluster installiert werden, der den Auftrag ausführen wird. Es kann ein Array vom Typ <Zeichenfolge, Objekt> sein.|Ja (mindestens eine mit der mainClassName-Methode)|

> [!NOTE]
> **Bekanntes Problem**: Wird derselbe [interaktive Cluster](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) zum Ausführen gleichzeitiger JAR in Databricks-Aktivitäten (ohne einen Clusterneustart) verwendet, kommt es zu einem bekannten Problem in Databricks, bei dem Parametern der ersten Aktivität auch von folgenden Aktivitäten verwendet werden. Dies führt zur Übergabe falscher Parameter an die nachfolgenden Aufträge. Um dieses Problem zu beheben, verwenden Sie stattdessen einen [Auftragscluster](compute-linked-services.md#example---using-new-job-cluster-in-databricks).

## <a name="supported-libraries-for-databricks-activities"></a>Unterstützte Bibliotheken für Databricks-Aktivitäten

In der vorherigen Databricks-Aktivitätsdefinition haben Sie die folgenden Bibliothekstypen angegeben: `jar`, `egg`, `maven`, `pypi`, `cran`.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Weitere Informationen zu Bibliothekstypen finden Sie in der [Databricks-Dokumentation](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary).

## <a name="how-to-upload-a-library-in-databricks"></a>Hochladen einer Bibliothek in Databricks

### <a name="you-can-use-the-workspace-ui"></a>Sie können die Benutzeroberfläche des Arbeitsbereichs verwenden:

1. [Verwenden der Benutzeroberfläche des Databricks-Arbeitsbereichs](/azure/databricks/libraries/#create-a-library)

2. Sie können den DBFS-Pfad der hinzugefügten Bibliothek über die Benutzeroberfläche mithilfe der [Databricks-Befehlszeilenschnittstelle](/azure/databricks/dev-tools/cli/#install-the-cli) abrufen.

   JAR-Bibliotheken werden beim Verwenden der Benutzeroberfläche in der Regel unter dbfs:/FileStore/jars gespeichert. Sie können alle über die Befehlszeilenschnittstelle auflisten: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Alternativ können Sie die Databricks-Befehlszeilenschnittstelle verwenden:

1. Informationen finden Sie unter [Kopieren der Bibliotheken mit der Databricks-Befehlszeilenschnittstelle](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs).

2. Verwenden Sie die Databricks-Befehlszeilenschnittstelle [(Installationsschritte)](/azure/databricks/dev-tools/cli/#install-the-cli).

   Kopieren Sie damit beispielsweise eine JAR-Ausgabe in DBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`.

## <a name="next-steps"></a>Nächste Schritte

Das [Video](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player) enthält eine 11-minütige Einführung und Demonstration dieses Features.
