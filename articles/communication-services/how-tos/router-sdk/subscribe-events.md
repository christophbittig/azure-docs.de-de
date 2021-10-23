---
title: Abonnieren von Ereignissen im Auftragsrouter
titleSuffix: An Azure Communication Services how-to guide
description: Verwenden von Azure Communication Services-SDKs zum Abonnieren von Auftragsrouterereignissen aus Event Grid
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 8e2cff056a6fb96f5cbcdbaea6be4c4257c3752b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129033"
---
# <a name="subscribe-to-job-router-events"></a>Abonnieren von Auftragsrouterereignissen

In diesem Leitfaden werden die Schritte zum Abonnieren von Auftragsrouterereignissen aus Ihrem Azure Communication Services Event Grid-Abonnement beschrieben. Das Empfangen von Ereignissen ist eine kritische Funktion, die Ihre benutzerdefinierten Anwendungen ausführen müssen. Die Aktionen, die der Auftragsrouter für von Ihnen übermittelte Aufträge ausführt, erfolgen asynchron, und während das SDK zwar Endpunkte zum Abfragen des Status und Zustands von Objekten im System bereitstellt, hat das Erstellen einer reaktiven, ereignisgesteuerten, benutzerdefinierten Anwendung erhebliche Vorteile.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine bereitgestellte Communication Services-Ressource. [Erstellen Sie eine Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Optional: Absolvieren Sie die Schnellstartanleitung für [die ersten Schritte mit dem Auftragsrouter](../../quickstarts/router/get-started-router.md).
- Installieren Sie den [Azure Resource Manager-Client (ARM)](https://github.com/projectkudu/ARMClient).
- Sehen Sie sich das [GitHub Beispielprojekt mit dem Event Grid Viewer](https://github.com/Azure/communication-preview/tree/master/samples/Job-Router/Event-Grid-Viewer) an.

## <a name="create-an-event-grid-subscription"></a>Erstellen eines Event Grid-Abonnements

> [!NOTE]
> Die folgenden Skripts werden mithilfe von PowerShell ausgeführt.

**Melden Sie sich bei Ihrem Azure-Konto an.**

```powershell
armclient azlogin
```

**Festlegen eines Abonnement- und Ressourcengruppennamens**
```powershell
$env:SUB = 'subscriptions/<insert_subscription_id>'
$env:RG = 'resourcegroups/<insert_resource_group_name>'
```

**(Optional) Auflisten aller ACS-Ressourcen im Abonnement**
```powershell
armclient get "/$env:SUB/$env:RG/providers/Microsoft.Communication/communicationservices?api-version=2020-08-20"
```
**Ausgabe**

:::image type="content" source="media/create-subscription-output.png" alt-text="Ausgabe des PowerShell-Befehls":::

Wie Sie sehen können, gibt es derzeit nur eine Azure Communication Services-Ressource unter dem angegebenen Abonnement und der Ressourcengruppe.

**Festlegen des ACS-Ressourcennamens**

Legen Sie den Namen Ihrer Azure Communication Services-Ressource fest. Wenn der Endpunkt für Ihre Ressource beispielsweise `https://contoso.communication.azure.net` ist, legen Sie `ACS_RESOURCE_NAME` auf das Präfix des DNS-Namens fest: `contoso`.

```powershell
$env:ACS_RESOURCE_NAME = "<insert_acs_resource_name>"
```

**(Optional) Auflisten vorhandener Ereignisabonnements**

Überprüfen Sie, ob bereits Ereignisabonnements vorhanden sind.

```powershell
$env:ACS_RESOURCE_ARM_ID = "/$env:SUB/$env:RG/providers/Microsoft.Communication/CommunicationServices/$env:ACS_RESOURCE_NAME"
$env:API_VERSION = "?api-version=2020-06-01"
$env:EVENT_SUBSCRIPTIONS_PATH = "providers/Microsoft.EventGrid/eventSubscriptions"

armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```
:::image type="content" source="media/check-subscriptions.png" alt-text="Überprüfen auf bestehende Abonnements":::

Wie Sie sehen können, wurden für diese Ressource keine Ereignisabonnements erstellt.

**Erstellen eines neuen Ereignisabonnements für Routerereignisse**

Kopieren Sie die folgende JSON-Nutzdaten, und fügen Sie sie in eine Textdatei namens `test.json` ein.

*Beispielnutzlast*
```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>"
      }
    },
    "filter": {
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobReceived",
        "Microsoft.Communication.RouterJobClassified",
        "Microsoft.Communication.RouterJobLabelsUpdated",
        "Microsoft.Communication.RouterJobClassificationFailed",
        "Microsoft.Communication.RouterJobCompleted",
        "Microsoft.Communication.RouterJobClosed",
        "Microsoft.Communication.RouterJobCancelled",
        "Microsoft.Communication.RouterJobExceptionTriggered",
        "Microsoft.Communication.RouterJobExceptionCleared",
        "Microsoft.Communication.RouterWorkerOfferIssued",
        "Microsoft.Communication.RouterWorkerOfferAccepted",
        "Microsoft.Communication.RouterWorkerOfferDeclined",
        "Microsoft.Communication.RouterWorkerOfferRevoked",
        "Microsoft.Communication.RouterWorkerOfferExpired",
        "Microsoft.Communication.RouterWorkerRegistered",
        "Microsoft.Communication.RouterWorkerDeregistered"
      ],
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    }
  }
}
```

**Festlegen des Ereignisabonnementnamens**
```powershell
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_All"
```

**Erstellen des Ereignisabonnements**
```powershell
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test.json
```
**Ausgabe**

:::image type="content" source="media/create-subscription.png" alt-text="Erstellen eines Ereignisabonnements":::

Wie Sie sehen können, wird das Ereignisabonnement erstellt und befindet sich derzeit im Zustand `Creating`. Der Erstellungsvorgang dauert im Allgemeinen ein paar Sekunden.

**Überprüfen, ob das Ereignisabonnement erfolgreich erstellt wurde**
```powershell
armclient get "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:API_VERSION"
```

**Ausgabe**

:::image type="content" source="media/verify-subscription-created.png" alt-text="Überprüfen, ob das Abonnement erstellt wurde":::

Wie Sie sehen können, wurde das Ereignisabonnement jetzt für alle Routerereignisse erfolgreich erstellt.

## <a name="creating-a-subscription-with-filters"></a>Erstellen eines Abonnements mit Filtern

Beim Einrichten von Ereignisabonnements können Sie auch erweiterte Filter verwenden, die die genauen Ereignisse steuern, die an ein bestimmtes Abonnement gesendet werden müssen. Im folgenden Beispiel werden beispielsweise nur `RouterJobCancelled`-Ereignisse abonniert und unter den folgenden Bedingungen an den Webhook gesendet:

- Die **Priorität** des Auftrags ist höher als `5`.
- Der Auftrag wurde einer Eskalationswarteschlange zugewiesen.
- Der Auftrag wurde wegen Inaktivität abgebrochen.
- Der Dispositionscode für abgebrochene Aufträge endet auf `_JobCancelledDueToInactivity`.
- Die Warteschlangen-ID endet auf den Namen `EscalationQueue`.

```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<insert_webhook_path_here>",
        "maxEventsPerBatch": 1,
        "preferredBatchSizeInKilobytes": 64
      }
    },
    "filter": {
      "subjectEndsWith": "_JobCancelledDueToInactivity",
      "isSubjectCaseSensitive": true,
      "includedEventTypes": [
        "Microsoft.Communication.RouterJobCancelled"
      ],
      "advancedFilters": [
        {
          "operatorType": "NumberGreaterThan",
          "key": "data.priority",
          "value": 5
        },
        {
          "operatorType": "StringEndsWith",
          "key": "data.queueId",
          "values": [
            "EscalationQueue"
          ]
        }
      ],
      "enableAdvancedFilteringOnArrays": true
    }
  }
}
```

Kopieren Sie die oben genannten JSON-Nutzdaten, fügen Sie sie in eine Textdatei ein, nennen Sie diese `test-with-advanced-filters.json`, und führen Sie dann den folgenden PowerShell-Code aus:

```powershell
$env:API_VERSION = "?api-version=2020-10-15-preview"
$env:EVENT_SUBSCRIPTION_NAME = "RouterEventsSubScription_WithFilters"
armclient put "$env:ACS_RESOURCE_ARM_ID/$env:EVENT_SUBSCRIPTIONS_PATH/$env:EVENT_SUBSCRIPTION_NAME/$env:API_VERSION" .\test-with-advanced-filters.json
```

**Ausgabe**

:::image type="content" source="media/advanced-filters.png" alt-text="Ausgabe erweiterter Filter":::

> [!NOTE]
> Eine vollständige Liste der Operatoren, die beim Erstellen von Abonnements verwendet werden können, finden Sie unter [Event Grid | Ereignisfilterung : Operatoren](../../../event-grid/event-filtering.md).

## <a name="events-catalog"></a>Ereignissekatalog

| Ereignisse | Unterdomäne | BESCHREIBUNG |
|------|:------------:| ---------- |
| [`RouterJobReceived`](#microsoftcommunicationrouterjobreceived) | `Job` | Für das Routing wurde ein neuer Auftrag erstellt. |
| [`RouterJobClassified`](#microsoftcommunicationrouterjobclassified)| `Job` | Die Klassifizierungsrichtlinie wurde auf einen Auftrag angewendet. |
| [`RouterJobLabelsUpdated`](#microsoftcommunicationrouterjoblabelsupdated) | `Job` | Die Bezeichnungen des Auftrags wurden geändert. |
| [`RouterJobClassificationFailed`](#microsoftcommunicationrouterjobclassificationfailed) | `Job` | Der Router konnte den Auftrag nicht mit einer Klassifizierungsrichtlinie klassifizieren. |
| [`RouterJobCompleted`](#microsoftcommunicationrouterjobcompleted) | `Job` | Ein Auftrag wurde abgeschlossen und tritt in die Abschlussphase ein. |
| [`RouterJobClosed`](#microsoftcommunicationrouterjobclosed) | `Job` | Ein Auftrag wurde geschlossen, und die Abschlussphase wurde abgeschlossen. |
| [`RouterJobCancelled`](#microsoftcommunicationrouterjobcancelled) | `Job` | Ein Auftrag wurde abgebrochen. |
| [`RouterJobExceptionTriggered`](#microsoftcommunicationrouterjobexceptiontriggered) | `Job` | Eine Auftragsausnahme wurde ausgelöst. |
| [`RouterJobExceptionCleared`](#microsoftcommunicationrouterjobexceptioncleared) | `Job` | Eine Auftragsausnahme wurde gelöscht. |
| [`RouterWorkerOfferIssued`](#microsoftcommunicationrouterworkerofferissued) | `Worker` | Ein Auftrag wurde einem Worker angeboten. |
| [`RouterWorkerOfferAccepted`](#microsoftcommunicationrouterworkerofferaccepted) | `Worker` | Ein Angebot an einen Worker wurde akzeptiert. |
| [`RouterWorkerOfferDeclined`](#microsoftcommunicationrouterworkerofferdeclined) | `Worker` | Ein Angebot an einen Worker wurde abgelehnt. |
| [`RouterWorkerOfferRevoked`](#microsoftcommunicationrouterworkerofferrevoked)  | `Worker` | Ein Angebot an einen Worker wurde widerrufen. |
| [`RouterWorkerOfferExpired`](#microsoftcommunicationrouterworkerofferexpired)  | `Worker` | Ein Angebot an einen Worker ist abgelaufen. |
| [`RouterWorkerRegistered`](#microsoftcommunicationrouterworkerregistered)  | `Worker` | Ein Worker wurde registriert. |
| [`RouterWorkerDeregistered`](#microsoftcommunicationrouterworkerderegistered)  | `Worker` | Die Registrierung eines Workers wurde aufgehoben. |

## <a name="router-events"></a>Routerereignisse

### <a name="microsoftcommunicationrouterjobreceived"></a>Microsoft.Communication.RouterJobReceived

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "acdf8fa5-8ab4-4a65-874a-c1d2a4a97f2e",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "PendingClassification",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "queue-id",
    "priority": 0,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | Mögliche Werte <ul> <li>PendingClassification</li> </ul> | Wenn dieses Ereignis gesendet wird, muss der Klassifizierungsprozess noch ausgeführt werden. |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null`, wenn `queueId` für einen Auftrag angegeben ist. |
| queueId | `string` | ✔️ | | `null`, wenn `classificationPolicyId` für einen Auftrag angegeben ist. |
| priority | `int` | ✔️ | | NULL, wenn `classificationPolicyId` angegeben ist. Ungleich NULL, wenn eine direkte Warteschlangenzuweisung vorliegt. |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |

### <a name="microsoftcommunicationrouterjobclassified"></a>Microsoft.Communication.RouterJobClassified

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/queue/{queue-id}",
  "data": {
    "queueInfo": {
      "id": "625fec06-ab81-4e60-b780-f364ed96ade1",
      "name": "Queue 1",
      "labels": {
        "Language": "en",
        "Product": "Office",
        "Geo": "NA"
      }
    },
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassified",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| queueInfo | `QueueInfo` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null`, wenn `queueId` für einen Auftrag angegeben wird (direkte Warteschlangenzuweisung) |
| queueId | `string` | ✔️ | | `null`, wenn `classificationPolicyId` für einen Auftrag angegeben ist. |
| priority | `int` | ❌ |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |

### <a name="microsoftcommunicationrouterjoblabelsupdated"></a>Microsoft.Communication.RouterJobLabelsUpdated

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "jobStatus": "Queued",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "priority": 5,
    "labelsAddedOrChanged": {
      "English": "5",
      "Office": "7"
    },
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken",
      "English": "5",
      "Office": "7"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobLabelsUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| jobStatus| `enum` | ❌ | Mögliche Werte <ul> <li>PendingClassification</li> <li>In Warteschlange</li> <li>Zugewiesen</li> <li>Abgeschlossen</li> <li>Geschlossen</li> <li>Canceled</li> <li>ClassificationFailed</li> </ul> |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ✔️ | | `null`, wenn `queueId` für einen Auftrag angegeben ist. |
| queueId | `string` | ✔️ | | `null`, wenn `classificationPolicyId` für einen Auftrag angegeben ist. |
| priority | `int` | ❌ |
| labelsAddedOrChanged | `Dictionary<string, object>` | ✔️ | | Bezeichnungen, die basierend auf Benutzereingaben hinzugefügt oder geändert wurden. |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Vollständige Liste der dem Auftrag zugeordneten Bezeichnungen. |

### <a name="microsoftcommunicationrouterjobclassificationfailed"></a>Microsoft.Communication.RouterJobClassificationFailed

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/classificationpolicy/{classificationpolicy-id}",
  "data": {
    "errors": [
      {
        "code": null,
        "message": "Classification failed due to <reason>",
        "target": null,
        "innerError": null,
        "details": null
      }
    ],
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "classificationPolicyId": "test-policy",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClassificationFailed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| errors| `List<CommunicationError>` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| classificationPolicyId | `string` | ❌ | |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |

### <a name="microsoftcommunicationrouterjobcompleted"></a>Microsoft.Communication.RouterJobCompleted

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "queue-id",
    "assignmentId": "6f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
    "workerId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCompleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| assignmentId| `string` | ❌ | |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |
| workerId | `string` | ❌ | |

### <a name="microsoftcommunicationrouterjobclosed"></a>Microsoft.Communication.RouterJobClosed

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/assignment/{assignment-id}",
  "data": {
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "",
    "dispositionCode": "",
    "workerId": "",
    "assignmentId": "",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobClosed",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ | |
| dispositionCode| `string` | ✔️ | | Basierend auf Benutzereingaben |
| workerId | `string` | ❌ | |
| assignmentId | `string` | ❌ | |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |

### <a name="microsoftcommunicationrouterjobcancelled"></a>Microsoft.Communication.RouterJobCancelled

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "b6d8687a-5a1a-42ae-b8b5-ff7ec338c872",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/disposition/{disposition-code}",
  "data": {
    "note": "Cancelled due to <reason>",
    "dispositionCode": "100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "queueId": ""
  },
  "eventType": "Microsoft.Communication.RouterJobCancelled",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:30Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| Hinweis| `string` | ✔️ | | Basierend auf Benutzereingaben |
| dispositionCode| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ✔️ | | Nicht NULL, wenn der Auftrag nach erfolgreicher Klassifizierung abgebrochen wird. |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |

### <a name="microsoftcommunicationrouterjobexceptiontriggered"></a>Microsoft.Communication.RouterJobExceptionTriggered

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "exceptionRuleId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionTriggered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| exceptionRuleId| `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |

### <a name="microsoftcommunicationrouterjobexceptioncleared"></a>Microsoft.Communication.RouterJobExceptionCleared

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "job/{job-id}/channel/{channel-id}/exceptionrule/{rulekey}",
  "data": {
    "ruleKey": "r100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "labels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterJobExceptionCleared",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| ruleKey | `string` | ❌ | |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
| channelId | `string` | ❌ |
| Bezeichnungen | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |

## <a name="worker-events"></a>Workerereignisse

### <a name="microsoftcommunicationrouterworkerofferissued"></a>Microsoft.Communication.RouterWorkerOfferIssued

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "525fec06-ab81-4e60-b780-f364ed96ade1",
    "offerTimeUtc": "2021-06-23T02:43:30.3847144Z",
    "expiryTimeUtc": "2021-06-23T02:44:30.3847674Z",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    }
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferIssued",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId| `string` | ❌ |
| offerTimeUtc | `DateTimeOffset` | ❌ |
| expiryTimeUtc| `DateTimeOffset` | ❌ |
| jobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |

### <a name="microsoftcommunicationrouterworkerofferaccepted"></a>Microsoft.Communication.RouterWorkerOfferAccepted

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "jobPriority": 5,
    "jobLabels": {
      "Locale": "en-us",
      "Segment": "Enterprise",
      "Token": "FooToken"
    },
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "assignmentId": "765fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferAccepted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| jobPriority| `int` | ❌ |
| jobLabels | `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |
| assignmentId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferdeclined"></a>Microsoft.Communication.RouterWorkerOfferDeclined

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1",
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferDeclined",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |
| offerId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferrevoked"></a>Microsoft.Communication.RouterWorkerOfferRevoked

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferRevoked",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerofferexpired"></a>Microsoft.Communication.RouterWorkerOfferExpired

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}/job/{job-id}",
  "data": {
    "offerId": "565fec06-ab81-4e60-b780-f364ed96ade1",
    "workerId": "w100",
    "jobId": "7f1df17b-570b-4ae5-9cf5-fe6ff64cc712",
    "channelReference": "test-abc",
    "channelId": "FooVoiceChannelId",
    "queueId": "625fec06-ab81-4e60-b780-f364ed96ade1"
  },
  "eventType": "Microsoft.Communication.RouterWorkerOfferExpired",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| offerId | `string` | ❌ |
| workerId | `string` | ❌ |
| jobId| `string` | ❌ |
| channelReference | `string` | ❌ |
|channelId | `string` | ❌ |
| queueId | `string` | ❌ |

### <a name="microsoftcommunicationrouterworkerregistered"></a>Microsoft.Communication.RouterWorkerRegistered

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
    "totalCapacity": 100,
    "queueAssignments": [
      {
        "id": "MyQueueId2",
        "name": "Queue 3",
        "labels": {
          "Language": "en",
          "Product": "Office",
          "Geo": "NA"
        }
      }
    ],
    "labels": {
      "x": "111",
      "y": "111"
    },
    "channelConfigurations": [
      {
        "channelId": "FooVoiceChannelId",
        "capacityCostPerJob": 10
      }
    ]
  },
  "eventType": "Microsoft.Communication.RouterWorkerRegistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```

**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
| totalCapacity | `int` | ❌ |
| queueAssignments| `List<QueueInfo>` | ❌ |
| Bezeichnungen| `Dictionary<string, object>` | ✔️ | | Basierend auf Benutzereingaben |
| channelConfigurations| `List<ChannelConfiguration>` | ❌ |

### <a name="microsoftcommunicationrouterworkerderegistered"></a>Microsoft.Communication.RouterWorkerDeregistered

[Zurück zum Ereigniskatalog](#events-catalog)

```json
{
  "id": "1027db4a-17fe-4a7f-ae67-276c3120a29f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "worker/{worker-id}",
  "data": {
    "workerId": "worker3",
  },
  "eventType": "Microsoft.Communication.RouterWorkerDeregistered",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-06-23T02:43:31Z"
}
```
**Attributeliste**

| Attribut | Typ | Nullwerte zulässig |BESCHREIBUNG | Hinweise |
|:--------- |:-----:|:-------:|-------------|-------|
| workerId | `string` | ❌ |
