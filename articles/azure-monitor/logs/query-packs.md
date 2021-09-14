---
title: Die Abfragepakete in Azure Monitor
description: Die Abfragepakete in Azure Monitor bieten eine Möglichkeit, die Erfassungen von Protokollabfragen in mehreren Log Analytics-Arbeitsbereichen zu teilen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: ef34ec8531242dfdb20e984c4c6d8f86d08b8fcf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354832"
---
# <a name="query-packs-in-azure-monitor-logs-preview"></a>Die Abfragepakete in Azure Monitor-Protokollen (Vorschau)
Ein Abfragepaket ist ein Resource Manager-Objekt, das als Container für die Protokollabfragen in Azure Monitor fungiert. Diese bieten die Möglichkeit, die Protokollabfragen zu speichern und sie für mehrere Arbeitsbereiche und andere Kontexte in Log Analytics zu teilen. 

## <a name="view-query-packs"></a>Das Anzeigen von Abfragepaketen
Sie können die Abfragepakete im Azure-Portal über das Menü **Log Analytics-Abfragepakete** anzeigen und verwalten. Wählen Sie ein Abfragepaket aus, um seine Berechtigungen anzuzeigen und zu bearbeiten. Weitere Informationen zum Erstellen eines Abfragepakets mithilfe der API finden Sie weiter unten.

Das [![Anzeigen von Abfragepaketen](media/query-packs/view-query-pack.png)](media/query-packs/view-query-pack.png#lightbox)

## <a name="permissions"></a>Berechtigungen
Sie können die Berechtigungen für ein Abfragepaket festlegen, wenn Sie es im Azure-Portal anzeigen. Die Benutzer benötigen die folgenden Berechtigungen, um Abfragepakete zu verwenden:

- Ein **Leser**: Der Benutzer kann alle Abfragen im Abfragepaket sehen und ausführen.
- Ein **Mitwirker**: Der Benutzer kann vorhandene Abfragen ändern und dem Abfragepaket neue Abfragen hinzufügen.

## <a name="default-query-pack"></a>Das Standardabfragepaket
Ein Abfragepaket genannt **DefaultQueryPack** wird automatisch in jedem Abonnement in einer Ressourcengruppe namens **LogAnalyticsDefaultResources** erstellt, wenn die erste Abfrage gespeichert wird. Sie können Abfragen in diesem Abfragepaket erstellen oder je nach Ihren Anforderungen zusätzliche Abfragepakete erstellen.

## <a name="using-multiple-query-packs"></a>Das Verwenden mehrerer Abfragepakete
Das einzelne Standardabfragepaket reicht für die meisten Benutzer aus, um Abfragen zu speichern und wiederzuverwenden. Es gibt jedoch Fälle, in denen Sie mehrere Abfragepakete für Benutzer in Ihrem Unternehmen erstellen möchten. Dazu gehören das Laden verschiedener Abfragesätze in verschiedenen Log Analytics-Sitzungen und das Bereitstellen unterschiedlicher Berechtigungen für verschiedene Sammlungen von Abfragen. 

Wenn Sie ein neues Abfragepaket mithilfe der API erstellen, können Sie Tags hinzufügen, die Abfragen gemäß Ihren Geschäftsanforderungen klassifizieren. Beispielsweise können Sie ein Abfragepaket markieren, um es einer bestimmten Abteilung in Ihrem Unternehmen zuzuordnen oder um den Schweregrad der Probleme zu bestimmen, auf die die enthaltenen Abfragen abzielen. Dadurch können Sie verschiedene Abfragesätze erstellen, die für verschiedene Benutzergruppen und unterschiedliche Situationen bestimmt sind.

## <a name="query-pack-definition"></a>Die Abfragepaketdefinition
Jedes Abfragepaket wird in einer JSON-Datei definiert, die die Definition für eine oder mehrere Abfragen enthält. Jede Abfrage wird durch einen Block wie folgt dargestellt:

```json
{
    "properties":
       {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        },
   }
}
```


## <a name="query-properties"></a>Die Abfrageeigenschaften
Jede Abfrage im Abfragepaket verfügt über die folgenden Eigenschaften.


| Eigenschaft | BESCHREIBUNG |
|:---|:---|
| displayName | Der Anzeigename, der in Log Analytics für jede Abfrage aufgeführt ist. | 
| description | Die Beschreibung der Abfrage, die in Log Analytics für jede Abfrage angezeigt wird. |
| body        | Die in KQL geschriebene Abfrage. |
| bezogen     | Die verwandten Kategorien, Ressourcentypen und Lösungen für die Abfrage. Diese wird für die Gruppierung und Filterung in Log Analytics durch den Benutzer verwendet, um das Auffinden seiner Anfrage zu erleichtern. Jede Abfrage kann bis zu zehn von jedem Typ aufweisen. Das Abrufen zulässiger Werte aus https://api.loganalytics.io/v1/metadata?select=resourceTypes, Lösungen, Kategorien. |
| tags        | Die zusätzlichen Tags, die vom Benutzer zum Sortieren und Filtern in Log Analytics verwendet werden. Jedes Tag wird beim [Gruppieren und Filtern von den Abfragen](queries.md#finding-and-filtering-queries) zu Kategorie, dem Ressourcentyp und der Lösung hinzugefügt. |




## <a name="create-a-query-pack"></a>Erstellen eines Abfragepakets
Die einzige Möglichkeit zum Installieren eines Abfragepakets ist derzeit die REST-API. 

### <a name="create-token"></a>Erstellen eines Tokens
Sie benötigen ein Token für die Authentifizierung der API-Anforderung. Es gibt mehrere Methoden, ein Token zu erhalten, einschließlich der Verwendung von **armclient**.

Melden Sie sich zunächst mit dem folgenden Befehl bei Azure an:

```
armclient login
```

Erstellen Sie dann das Token mit dem folgenden Befehl. Das Token wird automatisch in die Zwischenablage kopiert, damit Sie ihn in ein anderes Tool einfügen können.

```
armclient token
```

### <a name="create-payload"></a>Erstellen einer Nutzlast
Die Nutzlast der Anforderung ist der JSON-Code, der eine oder mehrere Abfragen definiert, und der Speicherort, an dem das Abfragepaket gespeichert werden soll. Der Name des Abfragepakets wird in der API-Anforderung angegeben, die im nächsten Abschnitt beschrieben wird.

```json
{
    "location": "eastus",
    "properties":
    {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        }
    }
}
```

### <a name="create-request"></a>Anforderung erstellen
Verwenden Sie die folgende Anforderung, um mithilfe der REST-API ein neues Abfragepaket zu erstellen. Die Anforderung sollte die Bearertokenautorisierung verwenden. Der Inhaltstyp sollte eine Anwendung/json sein.

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack?api-version=2019-09-01-preview
```

Verwenden Sie ein Tool, das eine REST-API-Anforderung wie z. B. Fiddler oder Postman übermitteln kann, um die Anforderung mithilfe der im vorherigen Abschnitt beschriebenen Nutzlast zu übermitteln. Die Abfrage-ID wird generiert und in der Nutzlast zurückgegeben. 

## <a name="update-a-query-pack"></a>Das Aktualisieren eines Abfragepakets
Um ein Abfragepaket zu aktualisieren, senden Sie die folgende Anforderung mit einer aktualisierten Nutzlast. Für diesen Befehl ist die Abfragepaket-ID erforderlich.

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack/queries/query-id/?api-version=2019-09-01-preview
```

## <a name="next-steps"></a>Nächste Schritte

- Unter [Verwenden von Abfragen in Azure Monitor Log Analytics](queries.md) erfahren Sie, wie die Benutzer mit Abfragepaketen in Log Analytics interagieren.
