---
title: ForEach-Aktivität
titleSuffix: Azure Data Factory & Azure Synapse
description: Die ForEach-Aktivität definiert eine sich wiederholende Ablaufsteuerung in einer Azure Data Factory- oder Azure Synapse Analytics-Pipeline. Die ForEach-Aktivität wird zum Durchlaufen einer Sammlung verwendet, um Aktionen für jedes Element in der Sammlung einzeln auszuführen.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: b0caba4963420de555b9dbfdb269924ba8c9a942
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831593"
---
# <a name="foreach-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>ForEach-Aktivität in Azure Data Factory und Azure Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Die ForEach-Aktivität definiert eine sich wiederholende Ablaufsteuerung in einer Azure Data Factory- oder Synapse-Pipeline. Diese Aktivität wird verwendet, um eine Sammlung zu durchlaufen. Sie führt die angegebenen Aktivitäten in einer Schleife aus. Die Schleifenimplementierung dieser Aktivität ähnelt der Foreach-Schleifenstruktur in Programmiersprachen.

## <a name="syntax"></a>Syntax
Die Eigenschaften werden weiter unten in diesem Artikel beschrieben. Die Eigenschaft „items“ ist die Sammlung, und auf die einzelnen Elemente in der Sammlung wird mit `@item()` verwiesen, wie im folgenden Syntaxbeispiel gezeigt:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Name der ForEach-Aktivität. | String | Ja
type | Muss auf **ForEach** festgelegt sein. | String | Ja
isSequential | Gibt an, ob die Schleife sequenziell oder parallel ausgeführt werden soll.  Es können maximal 50 Schleifeniterationen parallel ausgeführt werden. Beispiel: Bei einer ForEach-Aktivität, die eine Kopieraktivität mit 10 unterschiedlichen Quell- und Senkendatasets durchläuft, während **isSequential** auf „false“ festgelegt ist, werden alle Kopien gleichzeitig ausgeführt. Der Standardwert lautet False. <br/><br/> Wenn „isSequential“ auf „false“ festgelegt ist, stellen Sie sicher, dass die Konfiguration die Ausführung mehrerer ausführbarer Dateien ermöglicht. Andernfalls sollte diese Eigenschaft vorsichtig verwendet werden, um Schreibkonflikte zu vermeiden. Weitere Informationen finden Sie im Abschnitt [Parallele Ausführung](#parallel-execution). | Boolean | Nein. Der Standardwert lautet False.
batchCount | Batchanzahl, die zum Steuern der Anzahl der parallelen Ausführungen verwendet werden soll (wenn „isSequential“ auf „false“ festgelegt ist). Dies ist das obere Parallelitätslimit, aber die ForEach-Aktivität wird nicht immer mit dieser Zahl ausgeführt. | Ganze Zahl (maximal 50) | Nein. Der Standardwert ist 20.
Items | Ein Ausdruck, der ein JSON-Array zurückgibt, das durchlaufen werden soll. | Ausdruck (der ein JSON-Array zurückgibt) | Ja
activities | Die Aktivitäten, die ausgeführt werden sollen. | Liste der Aktivitäten | Ja

## <a name="parallel-execution"></a>Parallele Ausführung
Wenn **isSequential** auf FALSE festgelegt ist, werden maximal 50 Iterationen der Aktivität gleichzeitig ausgeführt. Diese Einstellung sollte vorsichtig verwendet werden. Wenn die gleichzeitigen Iterationen in den gleichen Ordner, aber in andere Dateien schreiben, ist dieser Ansatz gut. Wenn die gleichzeitigen Iterationen gleichzeitig in genau dieselbe Datei schreiben, verursacht dieser Ansatz wahrscheinlich einen Fehler. 

## <a name="iteration-expression-language"></a>Sprache für Iterationsausdrücke
Geben Sie in der ForEach-Aktivität für die Eigenschaft **items** ein Array an, das durchlaufen werden soll. Verwenden Sie `@item()` zum Durchlaufen einer einzelnen Enumeration in der ForEach-Aktivität. Beispiel: Wenn **items** ein Array wie [1, 2, 3] ist, gibt `@item()` in der ersten Iteration 1 zurück, in der zweiten Iteration 2 und in der dritten Iteration 3. Sie können `@range(0,10)` den auch „Gefällt mir“ Ausdruck verwenden, um zehnmal zu iterieren, beginnend mit 0 bis 9.

## <a name="iterating-over-a-single-activity"></a>Durchlaufen einer einzelnen Aktivität
**Szenario:** Kopieren aus einer Quelldatei im Azure-Blob in mehrere Zieldateien im Azure-Blob.

### <a name="pipeline-definition"></a>Definition der Pipeline

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Definition des Blobdatasets

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Verwendete Parameterwerte

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Durchlaufen mehrerer Aktivitäten
Es ist möglich, mehrere Aktivitäten in einer ForEach-Aktivität zu durchlaufen (zum Beispiel: Kopier- und Webaktivitäten). In diesem Szenario wird empfohlen, dass Sie mehrere Aktivitäten in eine separate Pipeline abstrahieren. Anschließend können Sie die [ExecutePipeline-Aktivität](control-flow-execute-pipeline-activity.md) in der Pipeline mit der ForEach-Aktivität verwenden, um die separate Pipeline mit mehreren Aktivitäten aufzurufen. 


### <a name="syntax"></a>Syntax

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Beispiel
**Szenario:** Durchlaufen einer inneren Pipeline in einer ForEach-Aktivität mit der Aktivität „Pipeline ausführen“. Die innere Pipeline wird mit parametrisierten Schemadefinitionen kopiert.

#### <a name="master-pipeline-definition"></a>Definition der Masterpipeline

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Definition der inneren Pipeline

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Definition des Quelldatasets

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definition des Senkendatasets

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Parameter der Masterpipeline
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Aggregieren von Ausgaben

Um Ausgaben der __foreach__-Aktivität zu aggregieren, verwenden Sie _Variables_ und die Aktivität _Variable anfügen_.

Deklarieren Sie zunächst eine `array`-_Variable_ in der Pipeline. Rufen Sie anschließend die Aktivität _Variable anfügen_ innerhalb jeder __foreach__-Schleife auf. Anschließend können Sie die Aggregation aus Ihrem Array abrufen.

## <a name="limitations-and-workarounds"></a>Einschränkungen und Problemumgehungen

Hier finden Sie einige Einschränkungen der ForEach-Aktivität sowie empfohlene Problemumgehungen.

| Einschränkung | Problemumgehung |
|---|---|
| Eine ForEach-Schleife kann nicht in einer anderen ForEach-Schleife (oder in einer Until-Schleife) geschachtelt werden. | Entwerfen Sie eine Pipeline mit zwei Ebenen, bei der die äußere Pipeline mit der äußeren ForEach-Schleife eine innere Pipeline mit der geschachtelten Schleife durchläuft. |
| Bei der ForEach-Aktivität gilt eine `batchCount`-Obergrenze von 50 für die parallele Verarbeitung sowie eine Obergrenze von 100.000 Elementen. | Entwerfen Sie eine Pipeline mit zwei Ebenen, bei der die äußere Pipeline mit der ForEach-Aktivität eine innere Pipeline durchläuft. |
| „SetVariable“ kann nicht innerhalb einer parallel ausgeführten ForEach-Aktivität verwendet werden, da die Variablen nicht für eine ForEach-Aktivität oder für eine andere Aktivität spezifisch, sondern für die gesamte Pipeline global sind. | Verwenden Sie ggf. ein sequenzielles ForEach-Element, oder verwenden Sie „Pipeline ausführen“ innerhalb von „ForEach“ (mit Behandlung der Variable/des Parameters in einer untergeordneten Pipeline).|
| | |

## <a name="next-steps"></a>Nächste Schritte
Informationen zu weiteren unterstützten Ablaufsteuerungsaktivitäten: 

- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
