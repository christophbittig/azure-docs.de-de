---
title: Erstellen einer Event Hub-Datenverbindung für Azure Synapse Data Explorer mithilfe einer Azure Resource Manager-Vorlage (Vorschau)
description: In diesem Artikel erfahren Sie, wie Sie eine Event Hub-Datenverbindung für Azure Synapse Data Explorer mithilfe einer Azure Resource Manager-Vorlage erstellen.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 7b948a25a9a7b259abb0931927d9c2a821be427d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478864"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-azure-resource-manager-template-preview"></a>Erstellen einer Event Hub-Datenverbindung für Azure Synapse Data Explorer mithilfe einer Azure Resource Manager-Vorlage (Vorschau)

> [!div class="op_single_selector"]
> * [Portal](data-explorer-ingest-event-hub-portal.md)
> * [1-Klick](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Azure Resource Manager-Vorlage](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)] 

In diesem Artikel erstellen Sie eine Event Hub-Datenverbindung für Azure Synapse Data Explorer mithilfe einer Azure Resource Manager-Vorlage.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Event Hub mit Daten für die Erfassung](data-explorer-ingest-event-hub-portal.md#create-an-event-hub)

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

## <a name="azure-resource-manager-template-for-adding-an-event-hub-data-connection"></a>Azure Resource Manager-Vorlage zum Hinzufügen einer Event Hub-Datenverbindung

Das folgende Beispiel zeigt eine Azure Resource Manager-Vorlage für das Hinzufügen einer Event Hub-Datenverbindung.  Sie können [die Vorlage im Azure-Portal mithilfe des Formulars bearbeiten und bereitstellen](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namespaces_eventhubns_name": {
            "type": "string",
            "defaultValue": "eventhubns",
            "metadata": {
                "description": "Specifies the Event Hub Namespace name."
            }
        },
        "EventHubs_eventhubdemo_name": {
            "type": "string",
            "defaultValue": "eventhubdemo",
            "metadata": {
                "description": "Specifies the Event Hub name."
            }
        },
        "consumergroup_default_name": {
            "type": "string",
            "defaultValue": "$Default",
            "metadata": {
                "description": "Specifies the consumer group of the Event Hub."
            }
        },
        "Clusters_kustocluster_name": {
            "type": "string",
            "defaultValue": "kustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databases_kustodb_name": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "tables_kustotable_name": {
            "type": "string",
            "defaultValue": "kustotable",
            "metadata": {
                "description": "Specifies the name of the table"
            }
        },
        "mapping_kustomapping_name": {
            "type": "string",
            "defaultValue": "kustomapping",
            "metadata": {
                "description": "Specifies the name of the mapping rule"
            }
        },
        "dataformat_type": {
            "type": "string",
            "defaultValue": "csv",
            "metadata": {
                "description": "Specifies the data format"
            }
        },
        "dataconnections_kustodc_name": {
            "type": "string",
            "defaultValue": "kustodc",
            "metadata": {
                "description": "Name of the data connection to create"
            }
        },
        "subscriptionId": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "Specifies the subscriptionId of the Event Hub"
            }
        },
        "resourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "metadata": {
                "description": "Specifies the resourceGroup of the Event Hub"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/DataConnections",
            "apiVersion": "2019-09-07",
            "name": "[concat(parameters('Clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'), '/', parameters('dataconnections_kustodc_name'))]",
            "location": "[parameters('location')]",
            "kind": "EventHub",
            "properties": {
                "eventHubResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.EventHub/namespaces/eventhubs', parameters('namespaces_eventhubns_name'), parameters('EventHubs_eventhubdemo_name'))]",
                "consumerGroup": "[parameters('consumergroup_default_name')]",
                "tableName": "[parameters('tables_kustotable_name')]",
                "mappingRuleName": "[parameters('mapping_kustomapping_name')]",
                "dataFormat": "[parameters('dataformat_type')]"
            }
        }
    ]
}
```

[!INCLUDE [data-explorer-clean-resources](../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Analysieren mit Data Explorer](../../get-started-analyze-data-explorer.md)
- [Überwachen von Data Explorer-Pools](../data-explorer-monitor-pools.md)
