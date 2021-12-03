---
title: Einrichten von Diagnoseprotokollen über die Azure Monitor-REST-API
description: Erfahren Sie, wie Sie Diagnoseprotokolle für Azure Data Factory mithilfe der Azure Monitor-REST-API einrichten.
author: joshuha-msft
ms.author: joowen
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 41b7a817b35b15ad18d962f5fb073fac76118444
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842832"
---
# <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Einrichten von Diagnoseprotokollen über die Azure Monitor-REST-API

In diesem Artikel wird beschrieben, wie Sie Diagnoseprotokolle für Azure Data Factory mithilfe der Azure Monitor-REST-API einrichten.

## <a name="diagnostic-settings"></a>Diagnoseeinstellungen

Verwenden Sie Diagnoseeinstellungen, um Diagnoseprotokolle für Nicht-Computeressourcen zu konfigurieren. Die Einstellungen für eine Ressourcensteuerung verfügen über die folgenden Features:

* Sie geben an, wohin Diagnoseprotokolle gesendet werden, z. B. an ein Azure Storage-Konto, an einen Azure Event Hub oder an Monitor-Protokolle.
* Sie geben an, welche Protokollkategorien gesendet werden.
* Sie geben an, wie lange die einzelnen Protokollkategorien in einem Speicherkonto aufbewahrt werden.
* Wenn für die Beibehaltungsdauer 0 Tage festgelegt sind, bedeutet dies, dass Protokolle unbegrenzt beibehalten werden. Andernfalls kann als Wert die Anzahl von Tagen (1 bis 2.147.483.647) festgelegt werden.
* Wenn Aufbewahrungsrichtlinien festgelegt werden, aber das Speichern von Protokollen in einem Speicherkonto deaktiviert ist, werden die Aufbewahrungsrichtlinien ignoriert. Diese Bedingung kann z. B. auftreten, wenn nur die Optionen „Event Hubs“ oder „Überwachungsprotokolle“ ausgewählt sind.
* Aufbewahrungsrichtlinien werden pro Tag angewendet. Die Grenze zwischen Tagen liegt um Mitternacht (Koordinierte Weltzeit, UTC). Jeweils am Ende eines Tages werden die Protokolle der Tage gelöscht, deren Aufbewahrungsdauer abgelaufen ist. Beispiel: Wenn Sie eine Aufbewahrungsrichtlinie für einen Tag verwenden, werden heute am Anfang des Tages die Protokolle von vorgestern gelöscht.

## <a name="enable-diagnostic-logs-via-the-monitor-rest-api"></a>Aktivieren von Diagnoseprotokollen über die Monitor-REST-API

Verwenden Sie die Monitor-REST-API, um Diagnoseprotokolle zu aktivieren.

### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Erstellen oder Aktualisieren einer Diagnoseeinstellung in der REST-API von Azure Monitor

#### <a name="request"></a>Anforderung

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>Header

* Ersetzen Sie `{api-version}` durch `2016-09-01`.
* Ersetzen Sie `{resource-id}` durch die ID der Ressource, deren Diagnoseeinstellungen Sie bearbeiten möchten. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Legen Sie den Header `Content-Type` auf `application/json` fest.
* Legen Sie den Autorisierungsheader auf ein JSON-Webtoken fest, das Sie aus Azure Active Directory (Azure AD) abgerufen haben. Weitere Informationen finden Sie unter [Authentifizieren von Anforderungen](../active-directory/develop/authentication-vs-authorization.md).

#### <a name="body"></a>Body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Eigenschaft | type | BESCHREIBUNG |
| --- | --- | --- |
| **storageAccountId** |String | Die Ressourcen-ID des Speicherkontos, an das Diagnoseprotokolle gesendet werden sollen. |
| **serviceBusRuleId** |String | Die Service Bus-Regel-ID des Service Bus-Namespace, in dem Event Hubs für das Streaming von Diagnoseprotokollen erstellt werden sollen. Die Regel-ID weist das Format `{service bus resource ID}/authorizationrules/{key name}` auf.|
| **workspaceId** | String | Die ID des Arbeitsbereichs, in dem die Protokolle gespeichert werden. |
|**metrics**| Parameterwerte der Pipelineausführung, die an die aufgerufene Pipeline übergeben werden sollen| Ein JSON-Objekt, das Parameternamen Argumentwerten zuordnet. |
| **logs**| Komplexer Typ| Der Name einer Diagnoseprotokollkategorie für einen Ressourcentyp. Um die Liste der Diagnoseprotokollkategorien für eine Ressource zu erhalten, führen Sie einen GET-Vorgang zum Abrufen von Diagnoseeinstellungen aus. |
| **category**| String| Ein Array von Protokollkategorien und ihrer Aufbewahrungsrichtlinien. |
| **timeGrain** | String | Die Granularität von Metriken, die im Zeitformat ISO 8601 erfasst werden. Der Eigenschaftswert muss `PT1M` sein, was eine Minute angibt. |
| **enabled**| Boolean | Gibt an, ob die Sammlung der Metrik- oder Protokollkategorie für diese Ressource aktiviert ist. |
| **retentionPolicy**| Komplexer Typ| Beschreibt die Aufbewahrungsrichtlinie für eine Metrik- oder Protokollkategorie. Diese Eigenschaft wird nur für Speicherkonten verwendet. |
|**days**| Int| Die Anzahl der Tage, die Metriken oder Protokolle aufbewahrt werden sollen. Wenn der Eigenschaftswert „0“ ist, werden die Protokolle dauerhaft aufbewahrt. Diese Eigenschaft wird nur für Speicherkonten verwendet. |

#### <a name="response"></a>Antwort

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Abrufen von Informationen zu Diagnoseeinstellungen in der Monitor-REST-API

#### <a name="request"></a>Anforderung

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>Header

* Ersetzen Sie `{api-version}` durch `2016-09-01`.
* Ersetzen Sie `{resource-id}` durch die ID der Ressource, deren Diagnoseeinstellungen Sie bearbeiten möchten. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Legen Sie den Header `Content-Type` auf `application/json` fest.
* Legen Sie den Autorisierungsheader auf ein JSON-Webtoken fest, das Sie aus Azure AD abgerufen haben. Weitere Informationen finden Sie unter [Authentifizieren von Anforderungen](../active-directory/develop/authentication-vs-authorization.md).

#### <a name="response"></a>Antwort

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Weitere Informationen finden Sie unter [Diagnoseeinstellungen](/rest/api/monitor/diagnosticsettings).

## <a name="next-steps"></a>Nächste Schritte

[Überwachen von SSIS-Vorgängen mit Azure Monitor](monitor-ssis.md)
