---
title: Aktivität „Variable festlegen“
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie Sie die Aktivität „Variable festlegen“ zum Festlegen des Werts einer vorhandenen Variablen verwenden, die in einer Azure Data Factory- oder Azure Synapse Analytics-Pipeline definiert ist.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/24/2021
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.openlocfilehash: c9c489ab15f7b6a44e4aadeef8cd98b0d935e1ba
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821644"
---
# <a name="set-variable-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Aktivität „Variable festlegen“ in Azure Data Factory and Azure Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie die Aktivität „Variable festlegen“, um den Wert einer vorhandenen Variablen vom Typ „String“, „Bool“ oder „Array“ festzulegen, die in einer Data Factory- oder Synapse-Pipeline definiert ist.

## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
name | Der Name der Aktivität in der Pipeline. | ja
description | Text, der beschreibt, welche Aktion die Aktivität ausführt. | nein
type | Muss auf **SetVariable** festgelegt werden. | ja
value | Der Zeichenfolgenliteral- oder Ausdrucksobjektwert, dem die Variable zugewiesen wird. | ja
variableName | Der Name der Variablen, die durch diese Aktivität festgelegt wird. | ja

## <a name="incrementing-a-variable"></a>Schrittweises Erhöhen einer Variablen

Ein gängiges Szenario für den Einsatz von Variablen ist die Verwendung einer Variablen als Iterator innerhalb einer until- oder foreach-Aktivität. In einer Aktivität „Variable festlegen“ können Sie nicht auf die Variable verweisen, die im Feld `value` festgelegt wird. Legen Sie zur Umgehung dieser Einschränkung eine temporäre Variable fest, und erstellen Sie dann eine zweite Aktivität „Variable festlegen“. Die zweite Aktivität „Variable festlegen“ legt den Wert des Iterators auf die temporäre Variable fest. 

Nachstehend sehen Sie ein Beispiel für dieses Muster:

![Variable schrittweise erhöhen](media/control-flow-set-variable-activity/increment-variable.png "Erhöhen eines Variablenwerts")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```

Variablen gelten derzeit auf Pipelineebene. Dies bedeutet, dass sie nicht threadsicher sind und zu einem unerwarteten oder unerwünschten Verhalten führen können, wenn innerhalb einer parallelen Iterationsaktivität (z. B. einer foreach-Schleife) auf sie zugegriffen wird. Dies gilt insbesondere, wenn der Wert auch innerhalb dieser foreach-Aktivität geändert wird.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über eine verwandte Ablaufsteuerungsaktivität: 

- [Aktivität „Variable anfügen“](control-flow-append-variable-activity.md)
