---
title: Verwalten von Hunting- und Livestream-Abfragen in Microsoft Sentinel mithilfe der REST-API | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie mit den Hunting-Features von Microsoft Sentinel die REST-API von Log Analytics nutzen können, um Hunting- und Livestream-Abfragen zu verwalten.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 0c12c96e5a9cb8fb532cdd8ad462ff3d6a5f0bfc
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520501"
---
# <a name="manage-hunting-and-livestream-queries-in-microsoft-sentinel-using-rest-api"></a>Verwalten von Hunting- und Livestream-Abfragen in Microsoft Sentinel mithilfe der REST-API

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Mit Microsoft Sentinel, das teilweise auf Azure Monitor Log Analytics basiert, können Sie die REST-API von Log Analytics nutzen, um Hunting- und Livestream-Abfragen zu verwalten. Dieses Dokument zeigt, wie Sie Huntingabfragen über die REST-API erstellen und verwalten.  Auf diese Weise erstellte Abfragen werden auf der Microsoft Sentinel-Benutzeroberfläche angezeigt.

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

In diesem Beispiel wird gezeigt, wie Sie eine Hunting-Abfrage für einen bestimmten Microsoft Sentinel-Arbeitsbereich erstellen oder aktualisieren.  Bei einer Livestreamabfrage ersetzen Sie *“Category”: “Hunting Queries”* durch *“Category”: “Livestream Queries”* im **Anforderungstext**: 

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

In diesem Beispiel wird gezeigt, wie Sie eine Hunting- oder Livestream-Abfrage für einen bestimmten Microsoft Sentinel-Arbeitsbereich löschen:

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

In diesem Artikel haben Sie erfahren, wie Sie Hunting- und Livestream-Abfragen in Microsoft Sentinel mithilfe der Log Analytics-API verwalten. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:

- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Verwenden von Notebooks zur Ermittlung von Anomalien](notebooks.md)
