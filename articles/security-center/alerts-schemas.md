---
title: Schemas für Microsoft Defender für Cloud-Warnungen
description: In diesem Artikel werden die verschiedenen Schemas beschrieben, die von Microsoft Defender für Cloud für Sicherheitswarnungen verwendet werden.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: memildin
ms.openlocfilehash: 6554e451c1622e10ab7d06abfb4d36e309be0395
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433344"
---
# <a name="security-alerts-schemas"></a>Schemas für Sicherheitswarnungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Wenn in Ihrem Abonnement erweiterte Sicherheitsfeatures aktiviert sind, erhalten Sie Sicherheitswarnungen, wenn Defender für Cloud Bedrohungen für zugehörige Ressourcen erkennt.

Sie können diese Sicherheitswarnungen auf den Seiten von Microsoft Defender für Cloud anzeigen – [Dashboard „Übersicht“](overview-page.md), [Warnungen](tutorial-security-incident.md), [Seiten zur Ressourcenintegrität](investigate-resource-health.md) oder [Dashboard zum Workloadschutz](workload-protections-dashboard.md) – und mithilfe externer Tools wie z. B.:

- [Microsoft Sentinel](../sentinel/index.yml): die cloudnative SIEM-Lösung von Microsoft. Der Sentinel-Connector ruft Warnungen von Microsoft Defender für Cloud ab und sendet sie an den [Log Analytics-Arbeitsbereich](../azure-monitor/logs/quick-create-workspace.md) für Microsoft Sentinel.
- SIEMs von Drittanbietern: Senden von Daten an [Azure Event Hubs](../event-hubs/index.yml). Integrieren Sie anschließend Ihre Event Hub-Daten in eine SIEM-Lösung eines Drittanbieters. Weitere Informationen finden Sie unter [Streamen von Warnungen in eine SIEM-, SOAR- oder IT-Service-Management-Lösung](export-to-siem.md).
- [Die REST-API](/rest/api/securitycenter/): Wenn Sie die REST-API für den Zugriff auf Warnungen verwenden, finden Sie weitere Informationen in der [Online-API-Dokumentation zu Warnungen](/rest/api/securitycenter/alerts).

Wenn Sie programmgesteuerte Methoden verwenden, um die Warnungen zu verarbeiten, benötigen Sie das richtige Schema, um die für Sie relevanten Felder zu finden. Wenn Sie in einen Event Hub exportieren oder die Workflowautomatisierung mit generischen HTTP-Connectors auslösen möchten, verwenden Sie die Schemas, um die JSON-Objekte ordnungsgemäß zu analysieren.

>[!IMPORTANT]
> Das Schema ist für jedes dieser Szenarien etwas anders. Stellen Sie daher sicher, dass Sie die entsprechende Registerkarte auswählen.


## <a name="the-schemas"></a>Die Schemas 


### <a name="microsoft-sentinel"></a>[Microsoft Sentinel](#tab/schema-sentinel)

Der Sentinel-Connector ruft Warnungen von Microsoft Defender für Cloud ab und sendet sie an den Log Analytics-Arbeitsbereich für Microsoft Sentinel.

Zum Erstellen eines Sentinel-Falls oder -Vorfalls mithilfe von Defender für Cloud-Warnungen benötigen Sie das Schema für die unten angezeigten Warnungen. 

Weitere Informationen finden Sie in der [Dokumentation zu Microsoft Sentinel](../sentinel/index.yml).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]


### <a name="azure-activity-log"></a>[Azure-Aktivitätsprotokoll](#tab/schema-activitylog)

Microsoft Defender für Cloud überwacht generierte Sicherheitswarnungen im Azure-Aktivitätsprotokoll als Ereignisse.

Sie können die Sicherheitswarnungen im Aktivitätsprotokoll anzeigen, indem Sie folgendermaßen nach dem Ereignis „Activate Alert“ suchen:

[![Durchsuchen des Aktivitätsprotokolls nach dem Ereignis „Warnung aktivieren“.](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>JSON-Beispiel für Warnungen, die an das Azure-Aktivitätsprotokoll gesendet werden

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Das Datenmodell des Schemas

|Feld|BESCHREIBUNG|
|----|----|
|**channels**|Konstant („Operation“)|
|**correlationId**|Die Warnungs-ID von Microsoft Defender für Cloud|
|**description**|Beschreibung der Warnung|
|**eventDataId**|Siehe correlationId|
|**eventName**|Die Unterfelder „value“ und „localizedValue“ enthalten den Anzeigenamen der Warnung.|
|**category**|Die Unterfelder „value“ und „localizedValue“ sind konstant („Security“).|
|**eventTimestamp**|UTC-Zeitstempel, wann die Warnung generiert wurde|
|**id**|Die vollqualifizierte Warnungs-ID|
|**level**|Konstant („Informational“)|
|**operationId**|Siehe „correlationId“|
|**operationName**|Das Feld „value“ ist konstant („Microsoft.Security/locations/alerts/activate/action“), und „localized value“ ist „Activate Alert“ (wird möglicherweise dem Gebietsschema des Benutzers entsprechend lokalisiert).|
|**resourceGroupName**|Enthält den Namen der Ressourcengruppe.|
|**resourceProviderName**|Die Unterfelder „value“ und „localizedValue“ sind konstant („Microsoft.Security“).|
|**resourceType**|Die Unterfelder „value“ und „localizedValue“ sind konstant („Microsoft.Security/locations/alerts“).|
|**Ressourcen-ID**|Die vollqualifizierte Azure-Ressourcen-ID|
|**status**|Die Unterfelder „value“ und „localizedValue“ sind konstant („Active“).|
|**subStatus**|Die Unterfelder „value“ und „localizedValue“ sind leer.|
|**submissionTimestamp**|Der UTC-Zeitstempel der Ereignisübermittlung an das Aktivitätsprotokoll|
|**subscriptionId**|Die Abonnement-ID der kompromittierten Ressource|
|**properties**|Ein JSON-Behälter mit zusätzlichen Eigenschaften, die sich auf die Warnung beziehen. Diese können sich je nach Warnung ändern. Die folgenden Felder werden jedoch in allen Warnungen angezeigt:<br>- severity: Der Schweregrad des Angriffs<br>- compromisedEntity: Der Name der kompromittierten Ressource<br>- remediationSteps: Array von Problembehandlungsschritten, die durchgeführt werden müssen<br>- intent: Absicht des Angriffs. Mögliche Absichten finden Sie in der Tabelle mit den [Absichten](alerts-reference.md#intentions).|
|**relatedEvents**|Konstant, leeres Array|
|||


### <a name="workflow-automation"></a>[Workflowautomatisierung (Vorschauversion)](#tab/schema-workflow-automation)

Informationen zum Warnungsschema bei Verwendung der Workflowautomatisierung finden Sie in der [Connectors-Dokumentation](/connectors/ascalert/).


### <a name="continuous-export"></a>[Fortlaufendem Export](#tab/schema-continuousexport)

Das Defender für Cloud-Feature für den fortlaufenden Export übergibt Warnungsdaten an:

- Azure Event Hub verwendet das gleiche Schema wie [die Warnungs-API.](/rest/api/securitycenter/alerts)
- Log Analytics-Arbeitsbereiche gemäß dem [SecurityAlert-Schema](/azure/azure-monitor/reference/tables/SecurityAlert) in der Azure Monitor-Datenreferenz-Dokumentation.




### <a name="ms-graph-api"></a>[MS Graph-API](#tab/schema-graphapi)

Microsoft Graph ist das Gateway zu Daten und Intelligence in Microsoft 365. Microsoft Graph bietet ein einheitliches Programmierbarkeitsmodell, mit dem Sie auf die enormen Datenmengen in Microsoft 365, Windows 10 und Enterprise Mobility + Security zugreifen können. Verwenden Sie die Vielzahl von Daten in Microsoft Graph, um Apps für Organisationen und Consumer zu erstellen, die mit Millionen von Benutzern interagieren.

Das Schema und eine JSON-Darstellung für Sicherheitswarnungen, die an MS Graph gesendet werden, sind in der [Microsoft Graph-Dokumentation](/graph/api/resources/alert) verfügbar.

---


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die Schemas beschrieben, die von den Tools zum Bedrohungsschutz von Microsoft Defender für Cloud beim Senden von Informationen zu Sicherheitswarnungen verwendet werden.

Weitere Informationen zu den Möglichkeiten, wie Sie auf Sicherheitswarnungen außerhalb von Defender für Cloud zugreifen können, finden Sie auf den folgenden Seiten:

- [Microsoft Sentinel](../sentinel/index.yml): die cloudnative SIEM-Lösung von Microsoft
- [Azure Event Hubs](../event-hubs/index.yml): der vollständig verwaltete Microsoft-Dienst für die Datenerfassung in Echtzeit
- [Fortlaufender Export von Defender für Cloud-Daten](continuous-export.md)
- [Log Analytics-Arbeitsbereiche](../azure-monitor/logs/quick-create-workspace.md): Azure Monitor speichert die Protokolldaten in einem Log Analytics-Arbeitsbereich, einem Container, der Daten und Konfigurationsinformationen enthält.