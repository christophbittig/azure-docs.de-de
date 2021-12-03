---
title: Beispiele für Azure Resource Manager-Vorlagen für Chaos-Experimente
description: Beispiel für Azure Resource Manager-Vorlagen zum Erstellen von Azure Chaos Studio-Experimenten
services: chaos-studio
author: johnkemnetz
ms.topic: sample
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: dc0777380cf34465fc89a9e2e61d63aec0ca9e8c
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404441"
---
# <a name="resource-manager-template-samples-for-experiments-in-azure-chaos-studio"></a>Beispiele für Resource Manager-Vorlagen für Experimente in Azure Chaos Studio
Dieser Artikel enthält Beispiele für [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/syntax.md) zum Erstellen eines [Chaos-Experiments](chaos-studio-chaos-experiments.md) in Azure Chaos Studio. Jedes Beispiel umfasst eine Vorlagendatei und eine Parameterdatei mit Beispielwerten für die Vorlage.

## <a name="create-experiment-sample"></a>Erstellen eines Experiments (Beispiel)

In diesem Beispiel erstellen Sie ein Chaos-Experiment mit einer einzelnen Zielressource und einem einzelnen CPU-Druckfehler. Das Experiment kann durch Verweisen auf die [REST-API](/rest/api/chaosstudio/experiments/create-or-update) und die [Fehlerbibliothek](chaos-studio-fault-library.md) geändert werden.

### <a name="template-file"></a>Vorlagendatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "experimentName": {
      "type": "string",
      "defaultValue": "simple-experiment",
      "metadata": {
        "description": "A name for the experiment."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "A region where the experiment will be deployed."
      }
    },
    "chaosTargetResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the chaos target. This is the resource ID of the resource being targeted plus the target proxy resource."
      }
    }
  },
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Chaos/experiments",
      "apiVersion": "2021-09-15-preview",
      "name": "parameters('experimentName')",
      "location": "parameters('location')",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "identity": {
          "properties": {
            "type": "SystemAssigned"
          }
        },
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "parameters('chaosTargetResourceId')"
              }
            ]
          }
        ],
        "startOnCreation": "false",
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ]
              }
            ]
          }
        ]
      }
    }
  ],
  "outputs": {}
}
```

### <a name="parameter-file"></a>Parameterdatei

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "experimentName": {
        "value": "my-first-experiment"
      },
      "location": {
        "value": "eastus"
      },
      "chaosTargetResourceId": {
        "value": "eastus"
      }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Chaos Studio](chaos-studio-overview.md)
* [Weitere Informationen zu Chaos-Experimenten](chaos-studio-chaos-experiments.md)
