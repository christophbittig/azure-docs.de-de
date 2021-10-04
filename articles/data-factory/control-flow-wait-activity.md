---
title: Warteaktivität
titleSuffix: Azure Data Factory & Azure Synapse
description: Die Wait-Aktivität in Azure Data Factory und Synapse Analytics hält die Ausführung einer Pipeline für einen angegebenen Zeitraum an.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: dfc74e9ada7715f9c954123892a93611df7eca9d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798919"
---
# <a name="execute-wait-activity-in-azure-data-factory-and-synapse-analytics"></a>Ausführen der Wait-Aktivität in Azure Data Factory und Synapse Analytics
Wenn Sie eine Warteaktivität in einer Pipeline verwenden, wartet die Pipeline den angegebenen Zeitraum, bevor Sie die Ausführung nachfolgender Aktivitäten fortsetzt. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntax

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Der Name der `Wait`-Aktivität. | String | Ja
type | Muss auf **Wait** festgelegt werden. | String | Ja
waitTimeInSeconds | Die Anzahl von Sekunden, die die Pipeline wartet, bevor sie die Verarbeitung fortsetzt | Integer | Ja

## <a name="example"></a>Beispiel

> [!NOTE]
> Dieser Abschnitt enthält die JSON-Definitionen und PowerShell-Beispielbefehle zum Ausführen der Pipeline. Eine exemplarische Vorgehensweise mit einer ausführlichen Anleitung zum Erstellen einer Pipeline mithilfe von Azure PowerShell und JSON-Definitionen finden Sie im [Tutorial zum Erstellen einer Data Factory mithilfe von Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Pipeline mit Warteaktivität
In diesem Beispiel besitzt die Pipeline zwei Aktivitäten: **Until** und **Wait**. Für die Warteaktivität ist eine Wartezeit von einer Sekunde konfiguriert. Die Pipeline führt die Webaktivität in einer Schleife mit einer Sekunde Wartezeit zwischen jeder Ausführung aus. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Nächste Schritte
Informationen zu weiteren unterstützten Ablaufsteuerungsaktivitäten: 

- [Aktivität „If Condition“](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)
