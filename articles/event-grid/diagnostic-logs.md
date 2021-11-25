---
title: 'Azure Event Grid: Diagnoseprotokolle für Azure Event Grid-Themen und Event Grid-Domänen'
description: In diesem Artikel erhalten Sie konzeptionelle Informationen über Diagnoseprotokolle für ein Azure Event Grid-Thema oder eine -Domäne.
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: df3fe9eaab544e3e52ff3a2da24fe7b624292367
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546821"
---
# <a name="diagnostic-logs-for-event-grid-topics-and-event-grid-domains"></a>Diagnoseprotokolle für Event Grid-Themen und -Domänen

Diagnoseeinstellungen ermöglichen es Event Grid-Benutzern, Protokolle zu **Veröffentlichungs- und Zustellungsfehlern** entweder in einem Speicherkonto, einer Event Hub-Instanz oder einem Log Analytics-Arbeitsbereich zu erfassen und anzuzeigen. In diesem Artikel werden das Schema für die Protokolle und ein Beispielprotokolleintrag bereitgestellt.

## <a name="schema-for-publishdelivery-failure-logs"></a>Schema für Veröffentlichungs-/Zustellungsfehlerprotokolle

| Eigenschaftenname | Datentyp | BESCHREIBUNG |
| ------------- | --------- | ----------- |
| Time | Datetime | Der Zeitpunkt, zu dem der Protokolleintrag generiert wurde. <p>**Beispielwert:**  01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | Der Name des Ereignisabonnements. <p>**Beispielwert:** „EVENTSUB1“</p> <p>Diese Eigenschaft ist nur für Zustellungsfehlerprotokolle vorhanden.</p>  |
| Category | String | Der Name der Protokollkategorie. <p>**Beispielwerte:** „DeliveryFailures“ oder „PublishFailures“ | 
| Vorgangsname | String | Der Name des Vorgangs hat den Fehler verursacht.<p>**Beispielwerte:** „Deliver“ für Zustellungsfehler. |
| `Message` | String | Die Protokollnachricht für den Benutzer, in der der Grund für den Fehler und andere zusätzliche Details erläutert werden. |
| resourceId | String | Die Ressourcen-ID für die Themen-/Domänenressource.<p>**Beispielwerte:** `/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example---schema-for-publishdelivery-failure-logs"></a>Beispiel: Schema für Veröffentlichungs-/Zustellungsfehlerprotokolle

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

Die möglichen Werte von `Outcome` sind `NotFound`, `Aborted`, `TimedOut`, `GenericError` und `Busy`. Event Grid protokolliert alle Informationen, die der Dienst vom Ereignishandler in `message` empfängt. Für `GenericError` werden beispielsweise der HTTP-Statuscode, der Fehlercode und die Fehlermeldung protokolliert.

## <a name="schema-for-data-plane-requests"></a>Schema für Datenebenenanforderungen

| Eigenschaftenname | Datentyp | Beschreibung |
| ------------- | --------- | ----------- |
| NetworkAccess | String | **PublicAccess** beim Herstellen einer Verbindung über eine öffentliche IP-Adresse <br /> **PrivateAccess** beim Herstellen einer Verbindung über eine private Verbindung |
| ClientIpAddress | String | Quell-IP von eingehenden Anforderungen |
| TlsVersion | String | Die von der Clientverbindung verwendete TLS-Version. Mögliche Werte sind **1.0**, **1.1** und **1.2**. |
| Authentication/Type | String | Der Typ des Geheimnisses, der beim Veröffentlichen von Nachrichten für die Authentifizierung verwendet wird. <br /> **Unknown**: Keiner der anderen Authentifizierungstypen. OPTIONS-Anforderungen weisen diesen Authentifizierungstyp auf. <br /> **Key**: Anforderung verwendet den SAS-Schlüssel. <br /> **SASToken**: Anforderung verwendet ein vom SAS-Schlüssel generiertes SAS-Token. <br /> **AADAccessToken**: Ein von AAD ausgestelltes JWT-Token. |
| Authentication/ObjectId | String | Objekt-ID des Dienstprinzipals, die den AADAccessToken-Authentifizierungstyp verwendet. |
| OperationResult | String | Ergebnis der Veröffentlichung. **Success**, **Unauthorized**, **Forbidden**, **RequestEntityTooLarge**, **BadRequest** & **InternalServerError** |
| TotalOperations | String | Diese Ablaufverfolgungen werden nicht für jede Veröffentlichungsanforderung ausgegeben. Ein Aggregat für jede eindeutige Kombination der oben genannten Werte wird jede Minute ausgegeben. |

## <a name="example---schema-for-data-plane-requests"></a>Beispiel: Schema für Datenebenenanforderungen

```json
{
    "time": "2021-10-26T21:44:16.8117322Z",
    "resourceId": "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/RESOURCEGROUPS/BMT-TEST/PROVIDERS/MICROSOFT.EVENTGRID/DOMAINS/BMTAUDITLOGDOMAIN",
    "operationName": "Microsoft.EventGrid/events/send",
    "category": "DataPlaneRequests",
    "level": "Information",
    "region": "CENTRALUSEUAP",
    "properties": {
        "aggregatedRequests": [
            {
                "networkAccess": "PublicAccess",
                "clientIpAddress": "xx.xx.xx.xxx",
                "tlsVersion": "1.2",
                "authentication": {
                            "type": "AADAccessToken",
                            "objectId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
                },
                "operationResult": "Success",
                "totalOperations": 1
            }
        ]
    }
}
```

Sobald die Diagnoseeinstellung `DataPlaneRequests` ausgewählt ist, beginnen Event Grid-Ressourcen mit der Veröffentlichung der Überwachungsablaufverfolgungen für Datenebenenvorgänge, einschließlich des öffentlichen und privaten Zugriffs. Diese Ablaufverfolgung kann bei Bedarf eine oder mehrere Anforderungen protokollieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Aktivieren von Diagnoseprotokollen für Themen oder Domänen finden Sie unter [Aktivieren von Diagnoseprotokollen](enable-diagnostic-logs-topic.md).
