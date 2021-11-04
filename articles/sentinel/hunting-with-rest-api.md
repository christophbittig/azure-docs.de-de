---
title: Verwalten von Hunting- und Livestreamabfragen in Azure Sentinel über die REST-API | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mit den Huntingfeatures von Azure Sentinel die REST-API von Log Analytics nutzen können, um Hunting- und Livestreamabfragen zu verwalten.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2020
ms.author: yelevin
ms.openlocfilehash: 264e99b15676807c54f400cb4431f53c06cae8bd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037110"
---
# <a name="manage-hunting-and-livestream-queries-in-azure-sentinel-using-rest-api"></a>Verwalten von Hunting- und Livestreamabfragen in Azure Sentinel über die REST-API

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Mit Azure Sentinel, das teilweise auf Azure Monitor Log Analytics basiert, können Sie die REST-API von Log Analytics nutzen, um Hunting- und Livestreamabfragen zu verwalten. Dieses Dokument zeigt, wie Sie Huntingabfragen über die REST-API erstellen und verwalten.  Abfragen, die auf diese Weise erstellt werden, werden auf der Azure Sentinel-Benutzeroberfläche angezeigt.

Weitere Informationen zur [API für gespeicherte Suchvorgänge](/rest/api/loganalytics/savedsearches) finden Sie in der endgültigen REST-API-Referenz.

## <a name="api-examples"></a>API-Beispiele

Ersetzen Sie in den folgenden Beispielen die jeweiligen Platzhalter durch die in der nachstehenden Tabelle angegebenen Werte:

| Platzhalter | Ersetzen durch |
|-|-|
| **{subscriptionId}** | Name des Abonnements, auf die Sie die Hunting- oder Livestreamabfrage anwenden |
| **{resourceGroupName}** | Name der Ressourcengruppe, auf die Sie die Hunting- oder Livestreamabfrage anwenden |
| **{savedSearchId}** | Eindeutiger Bezeichner (GUID) für jede Huntingabfrage |
| **{WorkspaceName}** | Name des Log Analytics-Arbeitsbereichs, der Ziel der Abfrage ist |
| **{DisplayName}** | Anzeigename Ihrer Wahl für die Abfrage |
| **{Description}** | Beschreibung der Hunting- oder Livestreamabfrage |
| **{Tactics}** | Relevante MITRE ATT&CK-Taktiken, die für die Abfrage gelten |
| **{Query}** | Abfrageausdruck für Ihre Abfrage |
|  

### <a name="example-1"></a>Beispiel 1

Dieses Beispiel zeigt, wie Sie eine Huntingabfrage für einen bestimmten Azure Sentinel-Arbeitsbereich erstellen oder aktualisieren.  Bei einer Livestreamabfrage ersetzen Sie *“Category”: “Hunting Queries”* durch *“Category”: “Livestream Queries”* im **Anforderungstext**: 

#### <a name="request-header"></a>Anforderungsheader

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>Anforderungstext

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>Beispiel 2

Dieses Beispiel zeigt, wie Sie eine Hunting- oder Livestreamabfrage für einen bestimmten Azure Sentinel-Arbeitsbereich löschen:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>Beispiel 3

Dieses Beispiel zeigt, wie Sie eine Hunting- oder Livestreamabfrage für einen bestimmten Arbeitsbereich abrufen:

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Hunting- und Livestreamabfragen in Azure Sentinel mithilfe der Log Analytics-API verwalten. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Verwenden von Notebooks zur Ermittlung von Anomalien](notebooks.md)
