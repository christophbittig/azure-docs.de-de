---
title: Transformieren von Daten mit Python in Databricks
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie Daten verarbeiten oder umwandeln können, indem Sie eine Databricks Python-Aktivität in einer Azure Data Factory- oder Synapse Analytics-Pipeline ausführen.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 09/09/2021
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-python, synapse
ms.openlocfilehash: b178a54d71d7f004f66e15f455c0b618842d765e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124806356"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformieren von Daten durch Ausführen einer Python-Aktivität in Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die Azure Databricks Python Activity in einer [Pipeline](concepts-pipelines-activities.md) führt eine Python-Datei in Ihrem Azure Databricks-Cluster aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.  Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark.

Das folgende Video enthält eine 11-minütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definition der Databricks-Python-Aktivität

Dies ist die JSON-Beispieldefinition der Python-Aktivität in Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Eigenschaften der Databricks-Python-Aktivität

Die folgende Tabelle beschreibt die JSON-Eigenschaften, die in der JSON-Definition verwendet werden:

|Eigenschaft|Beschreibung|Erforderlich|
|---|---|---|
|name|Der Name der Aktivität in der Pipeline.|Ja|
|description|Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.|Nein|
|type|Bei Python-Aktivitäten in Databricks lautet der Aktivitätstyp DatabricksSparkPython.|Ja|
|linkedServiceName|Der Name des verknüpften Databricks-Diensts, in dem die Python-Aktivität ausgeführt wird. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md).|Ja|
|pythonFile|Der URI der auszuführenden Python-Datei. Es werden nur DBFS-Pfade unterstützt.|Ja|
|parameters|Befehlszeilenparameter, die an die Python-Datei übergeben werden. Es handelt sich um einen Array von Zeichenfolgen.|Nein|
|libraries|Eine Liste der Bibliotheken, die in dem Cluster installiert werden, der den Auftrag ausführen wird. Es kann ein Array vom Typ <Zeichenfolge, Objekt> sein.|Nein|

## <a name="supported-libraries-for-databricks-activities"></a>Unterstützte Bibliotheken für Databricks-Aktivitäten

In der oben genannten Definition der Databricks-Aktivität geben Sie diese Bibliothekstypen an: *JAR*, *EGG*, *Maven*, *PyPI*,  *CRAN*.

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
