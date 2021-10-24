---
title: 'Azure Communication Services: Präsenzereignisse'
description: In diesem Artikel wird beschrieben, wie Azure Communication Services als Event Grid-Ereignisquelle für Benutzerpräsenzereignisse verwendet wird.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 75e23d5de55ab205cc35bf6e7496413f4dd4be9a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134309"
---
# <a name="azure-communication-services---presence-events"></a>Azure Communication Services: Präsenzereignisse

In diesem Artikel werden die Eigenschaften und das Schema für Präsenzereignisse der Kommunikationsdienste beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

## <a name="events-types"></a>Ereignistypen

Von Azure Communication Services werden folgende Benutzerpräsenz-Ereignistypen ausgegeben:

| Ereignistyp                                                  | BESCHREIBUNG                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.UserDisconnected | Wird veröffentlicht, nachdem ein Benutzer der Communication Services als von den Communication Services getrennt festgelegt wurde |

## <a name="event-responses"></a>Ereignisantworten

Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an die Endpunkte, die über ein entsprechendes Abonnement verfügen.

In diesem Abschnitt wird anhand eines Beispiels gezeigt, wie diese Daten für jedes Ereignis aussehen.

> [!IMPORTANT]
> Das Ereignis Microsoft.Communication.UserDisconnected befindet sich in Public Preview. Während dieser Vorschauzeit können die Protokolle, die dem Zustand „Vom Benutzer getrennt“ zugeordnet sind, global repliziert werden. Sie können den getrennten Zustand abrufen, indem Sie dieses Ereignis über Event Grid abonnieren.

> [!NOTE]
> Das Ereignis Microsoft.Communication.UserDisconnected gilt nur im Chatkontext.
 
### <a name="microsoftcommunicationuserdisconnected"></a>Microsoft.Communication.UserDisconnected

```json
[
 {
  "id": "8f60490d-0719-4d9d-a1a6-835362fb752e",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "user/{rawId}",
  "data": {
    "userCommunicationIdentifier": {
      "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40",
      "communicationUser": {
        "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_0000000b-d198-0d50-84f5-084822008d40"
      }
    }
  },
  "eventType": "Microsoft.Communication.UserDisconnected",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-08-10T20:25:38Z"
 }
]
```
