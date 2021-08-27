---
title: Übergeben von Triggerinformationen an eine Pipeline
description: Hier erfahren Sie, wie Sie in einer Pipeline auf Triggermetadaten verweisen.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 4d51377daeeb16d7e3e74a77a4379b6fc13cb94b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122640769"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>Verweisen auf Triggermetadaten in Pipelineausführungen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel erfahren Sie, wie Sie Triggermetadaten (beispielsweise die Startzeit eines Triggers) in einer Pipelineausführung verwenden.

Eine Pipeline muss manchmal Metadaten des Triggers, durch den sie aufgerufen wird, interpretieren und lesen können. Bei einer Triggerausführung mit rollierendem Fenster verarbeitet die Pipeline beispielsweise auf der Grundlage der Start- und Endzeit des Fensters verschiedene Datenslices oder Ordner. In Azure Data Factory werden Parametrisierung und die [Systemvariable](control-flow-system-variables.md) verwendet, um Metadaten des Triggers an die Pipeline zu übergeben.

Dieses Muster ist besonders nützlich für einen [Trigger mit rollierendem Fenster](how-to-create-tumbling-window-trigger.md), bei dem der Trigger die Start- und Endzeit des Fensters angibt, und für einen [benutzerdefinierten Ereignisauslöser](how-to-create-custom-event-trigger.md), bei dem der Trigger die Werte in einem [benutzerdefinierten _Daten_-Feld](../event-grid/event-schema.md) analysiert und verarbeitet.

> [!NOTE]
> Von anderen Triggerarten werden andere Metadateninformationen bereitgestellt. Weitere Informationen finden Sie unter [Von Azure Data Factory unterstützte Systemvariablen](control-flow-system-variables.md).

## <a name="data-factory-ui"></a>Data Factory-Benutzeroberfläche

In diesem Abschnitt erfahren Sie, wie Sie Metadateninformationen eines Triggers innerhalb der Azure Data Factory-Benutzeroberfläche an eine Pipeline übergeben.

1. Navigieren Sie zum **Erstellungsbereich**, und bearbeiten Sie eine Pipeline.

1. Klicken Sie die leere Canvas, um Pipelineeinstellungen anzuzeigen. Wählen Sie keine Aktivität aus. Möglicherweise müssen Sie den Einstellungsbereich am unteren Rand der Canvas einblenden, da er ggf. reduziert wurde.

1. Wählen Sie **Parameter** und anschließend **+ Neu** aus, um Parameter hinzuzufügen.

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Screenshot: Pipelineeinstellung, die zeigt, wie Parameter in einer Pipeline definiert werden":::

1. Fügen Sie der Pipeline Trigger hinzu, indem Sie auf **+ Trigger** klicken.

1. Erstellen Sie einen Trigger, oder fügen Sie einen Trigger an die Pipeline an, und klicken Sie anschließend auf **OK**.

1. Geben Sie auf der nächsten Seite die Triggermetadaten für die einzelnen Parameter ein. Verwenden Sie das in der [Systemvariablen](control-flow-system-variables.md) definierte Format, um Triggerinformationen abzurufen. Sie müssen nur Informationen für Parameter angeben, von denen Triggermetadatenwerte angenommen werden. Hier weisen wir beispielsweise *parameter_1* die Startzeit für die Triggerausführung zu.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Screenshot: Seite mit der Triggerdefinition, die zeigt, wie Triggerinformationen an Pipelineparameter übergeben werden":::

1. Verwenden Sie in Pipelinedefinitionen __nicht__ die Systemvariable, sondern Parameter vom Typ _@pipeline().parameters.parameterName_, um die Werte in der Pipeline zu verwenden. In unserem Fall verweisen wir beispielsweise auf „@pipeline().parameters.parameter_1“, um die Startzeit des Triggers zu lesen.

## <a name="json-schema"></a>JSON-Schema

Um Triggerinformationen an Pipelineausführungen zu übergeben, müssen sowohl der JSON-Code des Triggers als auch der JSON-Code der Pipeline mit einem Abschnitt vom Typ _parameters_ aktualisiert werden.

### <a name="pipeline-definition"></a>Definition der Pipeline

Fügen Sie unter dem Abschnitt **properties** Parameterdefinitionen zum Abschnitt **parameters** hinzu.

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
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
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Triggerdefinition

Weisen Sie unter dem Abschnitt **pipelines** im Abschnitt **parameters** Parameterwerte zu. Sie müssen nur Informationen für Parameter angeben, von denen Triggermetadatenwerte angenommen werden.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>Verwenden von Triggerinformationen in einer Pipeline

Verwenden Sie in Pipelinedefinitionen __nicht__ die Systemvariable, sondern Parameter vom Typ _@pipeline().parameters.parameterName_, um die Werte in der Pipeline zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

Detaillierte Informationen zu Triggern finden Sie unter [Pipelineausführung und -trigger](concepts-pipeline-execution-triggers.md#trigger-execution).
