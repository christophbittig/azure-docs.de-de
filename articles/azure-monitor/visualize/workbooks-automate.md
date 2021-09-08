---
title: Azure Monitor-Arbeitsmappen und Azure Resource Manager-Vorlagen
description: Vereinfachen Sie die komplexe Berichterstellung mit vordefinierten und benutzerdefinierten parametrisierten Azure Monitor-Arbeitsmappen, die über Azure Resource Manager-Vorlagen bereitgestellt werden.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: d48cefcae16829bc0a58cdb4f4a43f52d78f8460
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356035"
---
# <a name="programmatically-manage-workbooks"></a>Programmgesteuertes Verwalten von Arbeitsmappen

Ressourcenbesitzer können Ihre Arbeitsmappen über Resource Manager-Vorlagen programmgesteuert erstellen und verwalten.

Dies kann in den folgenden Szenarien hilfreich sein:
* Bereitstellen von organisations- oder domänenspezifischen Analyseberichten zusammen mit Ressourcenbereitstellungen. Sie können beispielsweise organisationsspezifische Leistungs-und Fehlerarbeitsmappen für Ihre neuen Apps oder VMs bereitstellen.
* Bereitstellen von Standardberichten oder Standarddashboards mithilfe von Arbeitsmappen für vorhandene Ressourcen.

Die Arbeitsmappe wird in der gewünschten Untergruppe/Ressourcengruppe und mit dem Inhalt erstellt, der in den Resource Manager-Vorlagen angegeben ist.

Es gibt zwei Arten von Arbeitsmappenressourcen, die programmgesteuert verwaltet werden können:
* [Arbeitsmappenvorlagen](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Arbeitsmappeninstanzen](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Azure Resource Manager-Vorlage für das Bereitstellen einer Arbeitsmappenvorlage

1. Öffnen Sie eine Arbeitsmappe, die Sie programmgesteuert bereitstellen möchten.
2. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
3. Öffnen Sie _Erweiterter Editor_, indem Sie auf der Symbolleiste auf die Schaltfläche _</>_ klicken.
4. Stellen Sie sicher, dass Sie sich auf der Registerkarte _Katalogvorlage_ befinden.

    ![Registerkarte „Katalogvorlage“](./media/workbooks-automate/gallery-template.png)
1. Kopieren Sie den JSON-Code in der Katalogvorlage in die Zwischenablage.
2. Im Folgenden ein Beispiel für eine Azure Resource Manager-Vorlage, mit der eine Arbeitsmappenvorlage im Azure Monitor-Arbeitsmappenkatalog bereitgestellt wird. Fügen Sie den kopierten JSON-Code anstelle von `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` ein. Eine Azure Resource Manager-Referenzvorlage, mit der eine Arbeitsmappenvorlage erstellt wird, finden Sie [hier](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. Geben Sie im `galleries`-Objekt für die Schlüssel `name` und `category` Ihre Werte ein. Im nächsten Abschnitt erfahren Sie mehr über [Parameter](#parameters).
2. Stellen Sie diese Azure Resource Manager-Vorlage über das [Azure-Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), die [Befehlszeilenschnittstelle](../../azure-resource-manager/templates/deploy-cli.md) oder [PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) usw. bereit.
3. Öffnen Sie das Azure-Portal, und navigieren Sie zum Arbeitsmappenkatalog, der in der Azure Resource Manager-Vorlage ausgewählt wurde. Navigieren Sie in der Beispielvorlage zum Azure Monitor-Arbeitsmappenkatalog:
    1. Öffnen Sie das Azure-Portal, und navigieren Sie zu Azure Monitor.
    2. Öffnen Sie `Workbooks` aus dem Inhaltsverzeichnis.
    3. Suchen Sie Ihre Vorlage im Katalog unter der Kategorie `Deployed Templates` (einer der lilafarbenen Einträge).

### <a name="parameters"></a>Parameter

|Parameter                |Erklärung                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Der Name der Arbeitsmappenvorlagen-Ressource in Azure Resource Manager.                                  |
|`type`                    | Immer „microsoft.insights/workbooktemplates“                                                            |
| `location`               | Der Azure-Speicherort, an dem die Arbeitsmappe erstellt wird.                                               |
| `apiVersion`             | 17.10.2019 Vorschau                                                                                     |
| `type`                   | Immer „microsoft.insights/workbooktemplates“                                                            |
| `galleries`              | Die Gruppe von Galerien, in der diese Arbeitsmappenvorlage angezeigt wird.                                                |
| `gallery.name`           | Der Anzeigename der Arbeitsmappenvorlage im Katalog.                                             |
| `gallery.category`       | Die Gruppe im Katalog, in die die Vorlage eingefügt werden soll.                                                     |
| `gallery.order`          | Eine Zahl, die die Rangordnung bestimmt, in der die Vorlage in einer Kategorie im Katalog angezeigt werden soll. Ein niedrigerer Rang impliziert eine höhere Priorität. |
| `gallery.resourceType`   | Der Ressourcentyp, der dem Katalog entspricht. Dies ist normalerweise die Ressourcentyp-Zeichenfolge, die der Ressource entspricht (z. B. microsoft.operationalinsights/workspaces). |
|`gallery.type`            | Sie wird als Arbeitsmappentyp bezeichnet und ist ein eindeutiger Schlüssel, der zur Unterscheidung des Katalogs innerhalb eines Ressourcentyps dient. Application Insights verfügen beispielsweise über die Typen `workbook` und `tsg`, die verschiedenen Arbeitsmappenkatalogen entsprechen. |

### <a name="galleries"></a>Kataloge

| Galerie                                        | Ressourcentyp                                       | Arbeitsmappentyp |
|:-----------------------------------------------|:----------------------------------------------------|:--------------|
| Arbeitsmappen in Azure Monitor                     | `Azure Monitor`                                     | `workbook`    |
| VM Insights in Azure Monitor                   | `Azure Monitor`                                     | `vm-insights` |
| Arbeitsmappen im Log Analytics-Arbeitsbereich           | `microsoft.operationalinsights/workspaces`          | `workbook`    |
| Arbeitsmappen in Application Insights              | `microsoft.insights/components`                     | `workbook`    |
| Leitfäden zur Problembehandlung in Application Insights | `microsoft.insights/components`                     | `tsg`         |
| Nutzung in Application Insights                  | `microsoft.insights/components`                     | `usage`       |
| Arbeitsmappen in Kubernetes Service                | `Microsoft.ContainerService/managedClusters`        | `workbook`    |
| Arbeitsmappen in Ressourcengruppen                   | `microsoft.resources/subscriptions/resourcegroups`  | `workbook`    |
| Arbeitsmappen in Azure Active Directory            | `microsoft.aadiam/tenant`                           | `workbook`    |
| VM Insights in virtuellen Computern                | `microsoft.compute/virtualmachines`                 | `insights`    |
| VM Insights in VM-Skalierungsgruppen      | `microsoft.compute/virtualmachinescalesets`         | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Azure Resource Manager-Vorlage für das Bereitstellen einer Arbeitsmappeninstanz

1. Öffnen Sie eine Arbeitsmappe, die Sie programmgesteuert bereitstellen möchten.
2. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
3. Öffnen Sie _Erweiterter Editor_, indem Sie auf der Symbolleiste auf die Schaltfläche _</>_ klicken.
4. Ändern Sie im Editor _Vorlagentyp_ in _Resource Manager-Vorlage_.
5. Die Resource Manager-Vorlage zum Erstellen wird im Editor angezeigt. Kopieren Sie den Inhalt, und übernehmen Sie ihn unverändert, oder führen Sie ihn mit einer größeren Vorlage zusammen, mit der ebenfalls die Zielressource bereitgestellt wird.

    ![Abbildung, die veranschaulicht, wie die Resource Manager-Vorlage aus der Arbeitsmappen-Benutzeroberfläche abgerufen wird](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Beispiel für eine Azure Resource Manager-Vorlage
Diese Vorlage zeigt, wie Sie eine einfache Arbeitsmappe bereitstellen, in der „Hallo Welt“ angezeigt wird.
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Vorlagenparameter

| Parameter | Erklärung |
| :------------- |:-------------|
| `workbookDisplayName` | Der Anzeigename für die Arbeitsmappe, der im Katalog oder in der Liste gespeicherter Elemente verwendet wird. Muss im Bereich der Ressourcengruppe und der Quelle eindeutig sein |
| `workbookType` | Der Katalog, in dem die Arbeitsmappe angezeigt wird. Zu den unterstützten Werten zählen Arbeitsmappe, `tsg`, Azure Monitor usw. |
| `workbookSourceId` | Die ID der Ressourceninstanz, der die Arbeitsmappe zugeordnet wird. Die neue Arbeitsmappe wird im Zusammenhang mit dieser Ressourceninstanz angezeigt, z. B. im Inhaltsverzeichnis der Ressource unter _Arbeitsmappe_. Wenn die Arbeitsmappe im Arbeitsmappenkatalog in Azure Monitor angezeigt werden soll, verwenden Sie anstelle einer Ressourcen-ID die Zeichenfolge _Azure Monitor_. |
| `workbookId` | Die eindeutige GUID für diese Arbeitsmappeninstanz. Verwenden Sie _[newGuid()]_ , um automatisch eine neue GUID zu erstellen. |
| `kind` | Hiermit wird angegeben, ob die erstellte Arbeitsmappe freigegeben ist. Alle neuen Arbeitsmappen verwenden den Wert _shared_. |
| `location` | Der Azure-Speicherort, an dem die Arbeitsmappe erstellt wird. Verwenden Sie _[resourceGroup().location]_ , um sie an demselben Speicherort wie die Ressourcengruppe zu erstellen |
| `serializedData` | Enthält den Inhalt oder die Nutzlast, der bzw. die in der Arbeitsmappe verwendet werden soll. Rufen Sie den Wert mithilfe der Resource Manager-Vorlage aus der Arbeitsmappen-Benutzeroberfläche ab |

### <a name="workbook-types"></a>Arbeitsmappentypen
Arbeitsmappentypen geben an, unter welchem Arbeitsmappenkatalogtyp die neue Arbeitsmappeninstanz angezeigt wird. Beispiele für Optionen:

| type | Position im Katalog |
| :------------- |:-------------|
| `workbook` | Der in den meisten Berichten verwendete Standardwert, einschließlich des Arbeitsmappenkatalogs von Application Insights, Azure Monitor usw.  |
| `tsg` | Der Katalog „Leitfäden zur Problembehandlung“ in Application Insights |
| `usage` | Der Katalog _Mehr_ unter _Nutzung_ in Application Insights |

### <a name="working-with-json-formatted-workbook-data-in-the-serializeddata-template-parameter"></a>Verwenden von Arbeitsmappendaten im JSON-Format im Vorlagenparameter „serializedData“

Beim Exportieren einer Azure Resource Manager-Vorlage für eine Azure-Arbeitsmappe sind häufig feste Ressourcenlinks in den exportierten Vorlagenparameter `serializedData` eingebettet. Dazu gehören potenziell vertrauliche Werte wie Abonnement-ID und Ressourcengruppenname sowie andere Arten von Ressourcen-IDs.

Das folgende Beispiel veranschaulicht die Anpassung einer exportierten Azure Resource Manager-Vorlage für Arbeitsmappen ohne Zeichenfolgenbearbeitung. Das in diesem Beispiel gezeigte Muster ist für die Verwendung mit den unveränderten Daten vorgesehen, die aus dem Azure-Portal exportiert wurden. Es empfiehlt sich außerdem, eingebettete vertrauliche Werte beim programmgesteuerten Verwalten von Arbeitsmappen zu maskieren. Daher wurden die Abonnement-ID und die Ressourcengruppe hier ebenfalls maskiert. Es wurden keine weiteren Änderungen am eingehenden `serializedData`-Rohwert vorgenommen.

```json
{
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workbookDisplayName": {
      "type": "string"
    },
    "workbookSourceId": {
      "type": "string",
      "defaultValue": "[resourceGroup().id]"
    },
    "workbookId": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    // serializedData from original exported Azure Resource Manager template
    "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":{\"json\":\"Replace with Title\"},\"name\":\"text - 0\"},{\"type\":3,\"content\":{\"version\":\"KqlItem/1.0\",\"query\":\"{\\\"version\\\":\\\"ARMEndpoint/1.0\\\",\\\"data\\\":null,\\\"headers\\\":[],\\\"method\\\":\\\"GET\\\",\\\"path\\\":\\\"/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups\\\",\\\"urlParams\\\":[{\\\"key\\\":\\\"api-version\\\",\\\"value\\\":\\\"2019-06-01\\\"}],\\\"batchDisabled\\\":false,\\\"transformers\\\":[{\\\"type\\\":\\\"jsonpath\\\",\\\"settings\\\":{\\\"tablePath\\\":\\\"$..*\\\",\\\"columns\\\":[]}}]}\",\"size\":0,\"queryType\":12,\"visualization\":\"map\",\"tileSettings\":{\"showBorder\":false},\"graphSettings\":{\"type\":0},\"mapSettings\":{\"locInfo\":\"AzureLoc\",\"locInfoColumn\":\"location\",\"sizeSettings\":\"location\",\"sizeAggregation\":\"Count\",\"opacity\":0.5,\"legendAggregation\":\"Count\",\"itemColorSettings\":null}},\"name\":\"query - 1\"}],\"isLocked\":false,\"fallbackResourceIds\":[\"/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/XXXXXXX\"]}",

    // parse the original into a JSON object, so that it can be manipulated
    "parsedData": "[json(variables('serializedData'))]",

    // create new JSON objects that represent only the items/properties to be modified
    "updatedTitle": {
      "content":{
        "json": "[concat('Resource Group Regions in subscription \"', subscription().displayName, '\"')]"
      }
    },
    "updatedMap": {
      "content": {
        "path": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups')]"
      }
    },

    // the union function applies the updates to the original data
    "updatedItems": [
      "[union(variables('parsedData')['items'][0], variables('updatedTitle'))]",
      "[union(variables('parsedData')['items'][1], variables('updatedMap'))]"
    ],

    // copy to a new workbook object, with the updated items
    "updatedWorkbookData": {
      "version": "[variables('parsedData')['version']]",
      "items": "[variables('updatedItems')]",
      "isLocked": "[variables('parsedData')['isLocked']]",
      "fallbackResourceIds": ["[parameters('workbookSourceId')]"]
    },

    // convert back to an encoded string
    "reserializedData": "[string(variables('updatedWorkbookData'))]"
  },
  "resources": [
    {
      "name": "[parameters('workbookId')]",
      "type": "microsoft.insights/workbooks",
      "location": "[resourceGroup().location]",
      "apiVersion": "2018-06-17-preview",
      "dependsOn": [],
      "kind": "shared",
      "properties": {
        "displayName": "[parameters('workbookDisplayName')]",
        "serializedData": "[variables('reserializedData')]",
        "version": "1.0",
        "sourceId": "[parameters('workbookSourceId')]",
        "category": "workbook"
      }
    }
  ],
  "outputs": {
    "workbookId": {
      "type": "string",
      "value": "[resourceId( 'microsoft.insights/workbooks', parameters('workbookId'))]"
    }
  },
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#"
}
```

In diesem Beispiel erleichterten die folgenden Schritte die Anpassung einer exportierten Azure Resource Manager-Vorlage:
1. Exportieren Sie die Arbeitsmappe als Azure Resource Manager-Vorlage, wie im obigen Abschnitt erläutert.
2. Gehen Sie im Abschnitt `variables` der Vorlage wie folgt vor:
    1. Analysieren Sie den Wert `serializedData` in einer JSON-Objektvariablen, die eine JSON-Struktur mit einem Array von Elementen erstellt, die den Inhalt der Arbeitsmappe darstellen.
    2. Erstellen Sie neue JSON-Objekte, die nur die Elemente/Eigenschaften darstellen, die geändert werden sollen.
    3. Projektieren Sie mithilfe der Funktion `union()` einen neuen Satz von JSON-Inhaltselementen (`updatedItems`), um die Änderungen auf die ursprünglichen JSON-Elemente anzuwenden.
    4. Erstellen Sie ein neues Arbeitsmappenobjekt (`updatedWorkbookData`), das `updatedItems` und die Daten vom Typ `version`/`isLocked` aus den ursprünglichen analysierten Daten sowie einen korrigierten Satz von `fallbackResourceIds` enthält.
    5. Serialisieren Sie den neuen JSON-Inhalt wieder in eine neue Zeichenfolgenvariable (`reserializedData`).
3. Verwenden Sie die neue Variable `reserializedData` anstelle der ursprünglichen Eigenschaft `serializedData`.
4. Stellen Sie die neue Arbeitsmappenressource mithilfe der aktualisierten Azure Resource Manager-Vorlage bereit.

### <a name="limitations"></a>Einschränkungen
Aus technischen Gründen können mit diesem Mechanismus keine Arbeitsmappeninstanzen im Katalog _Arbeitsmappen_ von Application Insights erstellt werden. Wir arbeiten derzeit an einer Lösung für diese Einschränkung. In der Zwischenzeit empfehlen wir, den Katalog „Leitfaden zur Problembehandlung“ (workbookType: `tsg`) zum Bereitstellen von Application Insights-bezogenen Arbeitsmappen zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Arbeitsmappen die neue [Storage Insights-Funktion](../../storage/common/storage-insights-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json) fördern.