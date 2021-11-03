---
title: Erstellen von Aktivitätsgruppen mit Resource Manager-Vorlagen
description: Erfahren Sie, wie Sie eine Aktionsgruppe mit einer Azure Resource Manager-Vorlage erstellen.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: dukek
ms.openlocfilehash: 1680ff8d209fc2680b19d3d3afe8c2b6aded9678
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044227"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Erstellen einer Aktionsgruppe mithilfe einer Resource Manager-Vorlage
In diesem Artikel erfahren Sie, wie Sie mit [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/syntax.md) Aktionsgruppen konfigurieren können. Mithilfe von Vorlagen können Sie automatisch Aktionsgruppen festlegen, die in bestimmten Warnungstypen wiederverwendet werden können. Diese Aktionsgruppen stellen sicher, dass alle relevanten Parteien benachrichtigt werden, wenn eine Warnung ausgelöst wird.

Die grundlegenden Schritte lauten wie folgt:

1. Erstellen Sie eine Vorlage als JSON-Datei, die die Erstellung der Aktionsgruppe beschreibt.

2. Stellen Sie die Vorlage mithilfe einer [beliebigen Bereitstellungsmethode](../../azure-resource-manager/templates/deploy-powershell.md) bereit.

## <a name="resource-manager-templates-for-an-action-group"></a>Resource Manager-Vorlagen für eine Aktionsgruppe

Um eine Aktionsgruppe mithilfe einer Resource Manager-Vorlage zu erstellen, müssen Sie eine Ressource des Typs `Microsoft.Insights/actionGroups` erstellen. Anschließend tragen Sie alle zugehörigen Eigenschaften ein. Im Folgenden finden Sie zwei Beispielvorlagen, die eine Aktionsgruppe erstellen.

Erste Vorlage. Hier erfahren Sie, wie Sie eine Resource Manager-Vorlage für eine Aktionsgruppe erstellen, bei der die Aktionsdefinitionen in der Vorlage hartcodiert sind. Zweite Vorlage. Hier erfahren Sie, wie Sie eine Vorlage erstellen, die die Daten der Webhookkonfiguration als Eingabeparameter akzeptiert, wenn die Vorlage bereitgestellt wird.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2021-09-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com",
            "useCommonAlertSchema": true

          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com",
            "useCommonAlertSchema": true
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1",
            "useCommonAlertSchema": true
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2",
            "useCommonAlertSchema": true
          }
        ],
        "eventHubReceivers": [
          {
            "name": "contosoeventhub1",
            "subscriptionId": "replace with subscription id GUID",
            "eventHubNameSpace": "contosoeventHubNameSpace",
            "eventHubName": "contosoeventHub",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2021-09-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu [Aktionsgruppen](./action-groups.md).
* Weitere Informationen zu [Warnungen](./alerts-overview.md).
* Weitere Informationen zum [Erstellen einer Aktivitätsprotokollwarnung mithilfe einer Resource Manager-Vorlage](./alerts-activity-log.md).