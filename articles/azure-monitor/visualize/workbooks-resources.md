---
title: Ressourcenparameter in Azure Monitor-Arbeitsmappen
description: Erfahren Sie, wie Sie Ressourcen in Arbeitsmappen mithilfe von Ressourcenparametern auswählen können. Verwenden Sie die Ressourcenparameter, um den Bereich festzulegen, aus dem die Daten abgerufen werden sollen.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: dba84320bb753fa162d1c4eced5de5ae79de6f48
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436156"
---
# <a name="workbook-resource-parameters"></a>Ressourcenparameter für Arbeitsmappen

Ressourcenparameter ermöglichen das Auswählen von Ressourcen in Arbeitsmappen. Dies ist nützlich, um den Bereich festzulegen, aus dem die Daten abgerufen werden sollen. Ein Beispiel dafür ist die Möglichkeit, dass Benutzer die Gruppe virtueller Computer auswählen können, die später in den Diagrammen bei der Darstellung der Daten verwendet werden.

Die Werte der Ressourcenauswahl können aus dem Arbeitsmappenkontext, einer statischen Liste oder aus Azure Resource Graph-Abfragen stammen.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Erstellen eines Ressourcenparameters (Arbeitsmappenressourcen)
1. Beginnen Sie mit einer leeren Arbeitsmappe im Bearbeitungsmodus.
2. Wählen Sie unter den Links in der Arbeitsmappe die Option _Parameter hinzufügen_ aus.
3. Klicken Sie auf die blaue Schaltfläche _Parameter hinzufügen_.
4. Geben Sie im daraufhin angezeigten Bereich für den neuen Parameter Folgendes ein:
    1. Parametername: `Applications`
    2. Parametertyp: `Resource picker`
    3. Erforderlich: `checked`
    4. Mehrfachauswahl zulassen: `checked`
5. Daten abrufen aus: `Workbook Resources`
6. Nur Ressourcentypen einschließen: `Application Insights`
7. Wählen Sie auf der Symbolleiste die Option „Speichern“ aus, um den Parameter zu erstellen.

![Abbildung: Erstellen eines Ressourcenparameters mithilfe von Arbeitsmappenressourcen](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Erstellen eines Ressourcenparameters (Azure Resource Graph)
1. Beginnen Sie mit einer leeren Arbeitsmappe im Bearbeitungsmodus.
2. Wählen Sie unter den Links in der Arbeitsmappe die Option _Parameter hinzufügen_ aus.
3. Klicken Sie auf die blaue Schaltfläche _Parameter hinzufügen_.
4. Geben Sie im daraufhin angezeigten Bereich für den neuen Parameter Folgendes ein:
    1. Parametername: `Applications`
    2. Parametertyp: `Resource picker`
    3. Erforderlich: `checked`
    4. Mehrfachauswahl zulassen: `checked`
5. Daten abrufen aus: `Query`
    1. Abfragetyp: `Azure Resource Graph`
    2. Abonnements: `Use default subscriptions`
    3. Fügen Sie im Abfragesteuerelement den folgenden Codeausschnitt hinzu.
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Wählen Sie auf der Symbolleiste die Option „Speichern“ aus, um den Parameter zu erstellen.

![Abbildung: Erstellen eines Ressourcenparameters mithilfe von Azure Resource Graph](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph ist noch nicht in allen Clouds verfügbar. Stellen Sie sicher, dass es in Ihrer Zielcloud unterstützt wird, wenn Sie diese Methode wählen.

[Azure Resource Graph – Dokumentation](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>Erstellen eines Ressourcenparameters (JSON-Liste)
1. Beginnen Sie mit einer leeren Arbeitsmappe im Bearbeitungsmodus.
2. Wählen Sie unter den Links in der Arbeitsmappe die Option _Parameter hinzufügen_ aus.
3. Klicken Sie auf die blaue Schaltfläche _Parameter hinzufügen_.
4. Geben Sie im daraufhin angezeigten Bereich für den neuen Parameter Folgendes ein:
    1. Parametername: `Applications`
    2. Parametertyp: `Resource picker`
    3. Erforderlich: `checked`
    4. Mehrfachauswahl zulassen: `checked`
5. Daten abrufen aus: `JSON`
    1. Fügen Sie im Inhaltssteuerelement den folgenden JSON-Codeausschnitt hinzu.
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Klicken Sie auf die blaue Schaltfläche _Aktualisieren_.
6. Legen Sie optional `Include only resource types` auf _Application Insights_ fest.
7. Wählen Sie auf der Symbolleiste die Option „Speichern“ aus, um den Parameter zu erstellen.

## <a name="referencing-a-resource-parameter"></a>Verweisen auf einen Ressourcenparameter
1. Fügen Sie der Arbeitsmappe ein Abfragesteuerelement hinzu, und wählen Sie eine Application Insights-Ressource aus.
2. Verwenden Sie das Dropdownmenü unter _Application Insights_, um den Parameter an das Steuerelement zu binden. Dadurch wird der Bereich der Abfrage auf die Ressourcen festgelegt, die zur Laufzeit vom Parameter zurückgegeben werden.
4. Fügen Sie im KQL-Steuerelement den folgenden Codeausschnitt hinzu.
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Führen Sie die Abfrage aus, um die Ergebnisse anzuzeigen. 

![Abbildung: Ressourcenparameter, auf den in einem Abfragesteuerelement verwiesen wird](./media/workbooks-resources/resource-reference.png)

> Diese Methode kann verwendet werden, um Ressourcen an andere Steuerelemente (z.B. Metriken) zu binden.

## <a name="resource-parameter-options"></a>Optionen für Ressourcenparameter
| Parameter | Erklärung | Beispiel |
| ------------- |:-------------|:-------------|
| `{Applications}` | Die ausgewählte Ressourcen-ID | _/subscriptions/\<sub-id\>/resourceGroups/\<resource-group\>/providers/\<resource-type\>/acmeauthentication_ |
| `{Applications:label}` | Die Bezeichnung der ausgewählten Ressource | `acmefrontend` |
| `{Applications:value}` | Der Wert der ausgewählten Ressource | _'/subscriptions/\<sub-id\>/resourceGroups/\<resource-group\>/providers/\<resource-type\>/acmeauthentication'_ |
| `{Applications:name}` | Der Name der ausgewählten Ressource | `acmefrontend` |
| `{Applications:resourceGroup}` | Die Ressourcengruppe der ausgewählten Ressource | `acmegroup` |
| `{Applications:resourceType}` | Der Typ der ausgewählten Ressource | _microsoft.insights/components_ |
| `{Applications:subscription}` | Das Abonnement der ausgewählten Ressource |  |
| `{Applications:grid}` | Ein Raster mit den Ressourceneigenschaften; nützlich zum Rendern in einem Textblock beim Debuggen  |  |

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr](./workbooks-overview.md#visualizations) über die vielen umfassenden Visualisierungsoptionen für Arbeitsmappen.
* [Steuern](./workbooks-access-control.md) Sie den Zugriff auf Ihre Arbeitsmappenressourcen, und geben Sie diese frei.