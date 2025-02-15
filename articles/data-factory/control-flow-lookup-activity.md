---
title: Lookup-Aktivität
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie die Lookupaktivität in Azure Data Factory und Azure Synapse Analytics verwenden, um einen Wert in einer externen Quelle zu suchen. Auf die Ausgabe kann durch nachfolgende Aktivitäten verwiesen werden.
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 5a605177c741f3da23c5cce2b3ecf3e757a8af32
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758083"
---
# <a name="lookup-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Lookupaktivität in Azure Data Factory und Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die Lookupaktivität kann ein Dataset aus allen Datenquellen abrufen, die von Data Factory- und Synapse-Pipelines unterstützt werden. Sie können damit dynamisch ermitteln, welche Objekte in einer nachfolgenden Aktivität verarbeitet werden sollen, statt den Objektnamen hart zu codieren. Beispiele für Objekte sind Dateien und Tabellen.

Die Lookup-Aktivität liest die Daten und gibt den Inhalt einer Konfigurationsdatei oder Tabelle zurück. Zudem gibt sie auch das Ergebnis einer ausgeführten Abfrage oder gespeicherten Prozedur zurück. Die Ausgabe kann ein Singleton-Wert oder ein Array von Attributen sein, das in einer nachfolgenden Kopier-, Transformations-oder Ablaufsteuerungsaktivität wie einer ForEach-Aktivität verwendet werden kann.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Beachten Sie Folgendes:

- Die Lookup-Aktivität kann bis zu **5000 Zeilen** zurückgeben. Wenn das Resultset mehr Datensätze enthält, werden die ersten 5000 Zeilen zurückgegeben.
- Die Ausgabe der Lookup-Aktivität unterstützt eine Größe von bis zu **4 MB**. Wenn die Größe diesen Grenzwert überschreitet, schlägt die Aktivität fehl. 
- Die Höchstdauer für die Lookup-Aktivität vor dem Timeout beträgt **24 Stunden**.
- Wenn Sie zum Suchen von Daten eine Abfrage oder gespeicherte Prozedur verwenden, müssen Sie sicherstellen, dass genau ein Resultset zurückgegeben wird. Andernfalls schlägt die Lookup-Aktivität fehl.

Die folgenden Datenquellen werden für die Lookup-Aktivität unterstützt. 

[!INCLUDE [data-factory-v2-supported-data-stores](includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Typeigenschaften

Name | Beschreibung | type | Erforderlich?
---- | ----------- | ---- | --------
dataset | Enthält die Datasetreferenz für die Lookupaktivität. Details finden Sie in den entsprechenden Connectorartikeln im Abschnitt **Dataset-Eigenschaften**. | Schlüssel-Wert-Paar | Ja
source | Enthält spezifische Quelleneigenschaften für das Dataset, identisch mit der Quelle der Kopieraktivität. Details finden Sie in jedem entsprechenden Connectorartikel im Abschnitt **Eigenschaften der Kopieraktivität**. | Schlüssel-Wert-Paar | Ja
firstRowOnly | Gibt an, ob nur die erste Zeile oder alle Zeilen zurückgegeben werden sollen. | Boolean | Nein. Der Standardwert lautet `true`.

> [!NOTE]
> 
> * Quellspalten mit dem Typ **ByteArray** werden nicht unterstützt.
> * In der Datasetdefinition wird keine **Struktur** unterstützt. Bei Textformatdateien geben Sie den Spaltennamen in der Kopfzeile an.
> * Wenn es sich bei Ihrer Lookup-Quelle um eine JSON-Datei handelt, wird die `jsonPathDefinition`-Einstellung zur Neugestaltung des JSON-Objekts nicht unterstützt. Die gesamten Objekte werden abgerufen.

## <a name="use-the-lookup-activity-result"></a>Verwenden des Ergebnisses der Lookup-Aktivität

Das Lookupergebnis wird im Abschnitt `output` des Ergebnisses der Aktivitätsausführung zurückgegeben.

* **Wenn `firstRowOnly` auf `true` (Standardwert) gesetzt ist**, wird das Ausgabeformat wie im folgenden Code gezeigt. Das Lookupergebnis befindet sich unter dem festen Schlüssel `firstRow`. Wenn Sie das Ergebnis in nachfolgenden Aktivitäten nutzen möchten, verwenden Sie das Muster `@{activity('LookupActivity').output.firstRow.table}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **Wenn `firstRowOnly` auf `false` festgelegt ist**, wird das Ausgabeformat wie im folgenden Code gezeigt. Ein Feld `count` gibt an, wie viele Datensätze zurückgegeben werden. Detaillierte Werte werden unter einem festen `value`-Array angezeigt. In diesem Fall folgt auf die Lookup-Aktivität eine [ForEach-Aktivität](control-flow-for-each-activity.md). Um das Array `value` an das Feld `items` der ForEach-Aktivität zu übergeben, verwenden Sie das Muster `@activity('MyLookupActivity').output.value`. Für den Zugriff auf Elemente in Array `value` verwenden Sie die folgende Syntax: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. z. B. `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Beispiel

In diesem Beispiel enthält die Pipeline zwei Aktivitäten: **Lookup** und **Kopieren**. Mit der Kopieraktivität kopieren Sie Daten aus einer SQL-Tabelle in Ihrer Azure SQL-Datenbank-Instanz nach Azure Blob Storage. Der Name der SQL-Tabelle wird in einer JSON-Datei in Blob Storage gespeichert. Die Lookup-Aktivität sucht den Tabellennamen zur Runtime. Durch diesen Ansatz wird die JSON-Datei dynamisch geändert. Pipelines oder Datasets müssen nicht erneut bereitgestellt werden. 

Dieses Beispiel zeigt nur das Lookup für die erste Zeile. Wenn Sie weitere Informationen zum Lookup für alle Zeilen und dem Verketten von Ergebnissen mit der ForEach-Aktivität benötigen, sehen Sie sich die Beispiele unter [Massenkopieren mehrerer Tabellen mithilfe von Azure Data Factory und PowerShell](tutorial-bulk-copy.md) an.


### <a name="pipeline"></a>Pipeline

- Die Lookup-Aktivität ist für die Verwendung von **LookupDataset** konfiguriert, das auf einen Speicherort in Azure Blob Storage verweist. Der Name der SQL-Tabelle wird von der Lookup-Aktivität aus einer JSON-Datei an diesem Speicherort gelesen. 
- Die Kopieraktivität verwendet die Ausgabe der Lookup-Aktivität, also den Namen der SQL-Tabelle. Die **tableName**-Eigenschaft in **SourceDataset** ist für die Verwendung der Ausgabe der Lookup-Aktivität konfiguriert. Die Kopieraktivität kopiert Daten aus der SQL-Tabelle an einen Speicherort in Azure Blob Storage. Der Speicherort wird durch die **SinkDataset**-Eigenschaft festgelegt. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Suchdataset

Das **lookup**-Dataset verweist auf die Datei **sourcetable.json** im Lookup-Ordner von Azure Storage, der durch den Typ **AzureBlobStorageLinkedService** angegeben wird. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**source**-Dataset für die Kopieraktivität

Das **source**-Dataset verwendet die Ausgabe der Lookup-Aktivität, also den Namen der SQL-Tabelle. Die Kopieraktivität kopiert Daten aus dieser SQL-Tabelle an einen Speicherort in Azure Blob Storage. Der Speicherort wird durch das **sink**-Dataset festgelegt. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**sink**-Dataset für die Kopieraktivität

Die Kopieraktivität kopiert Daten aus der SQL-Tabelle in die Datei **filebylookup.csv** im Ordner **csv** in Azure Storage. Die Datei wird durch die Eigenschaft **AzureBlobStorageLinkedService** festgelegt. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

Sie können die folgenden zwei Arten von Formaten für die Datei **sourcetable.json** verwenden.

#### <a name="set-of-objects"></a>Satz von Objekten

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Array von Objekten

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Einschränkungen und Problemumgehungen

Hier werden einige Einschränkungen der Lookup-Aktivität und empfohlene Problemumgehungen aufgeführt.

| Einschränkung | Problemumgehung |
|---|---|
| Die Grenze der Lookup-Aktivität liegt bei maximal 5.000 Zeilen und einer maximalen Größe von 4 MB. | Entwerfen Sie eine Pipeline mit zwei Ebenen, wobei die äußere Pipeline eine innere Pipeline durchläuft, die Daten abruft, die die maximale Zeilenanzahl oder Größe nicht überschreiten. |
| | |

## <a name="next-steps"></a>Nächste Schritte
Dies sind weitere Ablaufsteuerungsaktivitäten, die von Azure Data Factory- und Synapse-Pipelines unterstützt werden: 

- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Webaktivität](control-flow-web-activity.md)
