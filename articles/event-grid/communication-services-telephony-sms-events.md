---
title: Azure Communication Services – Ereignisse für Telefonie und SMS
description: In diesem Artikel wird beschrieben, wie Azure Communication Services als Event Grid-Ereignisquelle für Ereignisse für Telefonie und SMS verwendet wird.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 2456df394a25df3b64f464a144224d9f9b365408
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134300"
---
# <a name="azure-communication-services---telephony-and-sms-events"></a>Azure Communication Services – Ereignisse für Telefonie und SMS

In diesem Artikel werden die Eigenschaften und das Schema für Ereignisse für Telefonie und SMS der Kommunikationsdienste beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

## <a name="events-types"></a>Ereignistypen

Von Azure Communication Services werden die folgenden Ereignistypen für Telefonie und SMS ausgegeben:

| Ereignistyp                                                  | Beschreibung                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.SMSReceived                         | Wird veröffentlicht, wenn eine SMS von einer Telefonnummer eingeht, die dem Kommunikationsdienst zugeordnet ist. |
| Microsoft.Communication.SMSDeliveryReportReceived           | Wird veröffentlicht, wenn ein Zustellbericht für eine von der Communication Services-Instanz gesendete SMS empfangen wird.     |

## <a name="event-responses"></a>Ereignisantworten

Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an die Endpunkte, die über ein entsprechendes Abonnement verfügen.

In diesem Abschnitt wird anhand eines Beispiels gezeigt, wie diese Daten für jedes Ereignis aussehen.

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Ereignis „Microsoft.Communication.SMSDeliveryReportReceived“

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Ereignis „Microsoft.Communication.SMSReceived“

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```
