---
title: Azure Communication Services – Ereignisse für Sprach- und Videoanrufe
description: In diesem Artikel wird beschrieben, wie Azure Communication Services als Event Grid-Ereignisquelle für Ereignisse für Sprach- und Videoanrufe verwendet wird.
ms.topic: conceptual
ms.date: 10/15/2021
ms.author: vikramdh
ms.openlocfilehash: 9259a5ce7151197f1155bfb91f23f3c98afca9b4
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134307"
---
# <a name="azure-communication-services---voice-and-video-calling-events"></a>Azure Communication Services – Ereignisse für Sprach- und Videoanrufe

In diesem Artikel werden die Eigenschaften und das Schema für Ereignisse für Sprach- und Videoanrufe der Kommunikationsdienste beschrieben.  Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

## <a name="events-types"></a>Ereignistypen

Von Azure Communication Services werden die folgenden Ereignistypen für Sprach- und Videoanrufe ausgegeben:

| Ereignistyp                                                  | BESCHREIBUNG                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.RecordingFileStatusUpdated | Veröffentlicht, wenn die Aufzeichnungsdatei verfügbar ist |

## <a name="event-responses"></a>Ereignisantworten

Wenn ein Ereignis ausgelöst wird, sendet der Event Grid-Dienst Daten zum Ereignis an die Endpunkte, die über ein entsprechendes Abonnement verfügen.

In diesem Abschnitt wird anhand eines Beispiels gezeigt, wie diese Daten für jedes Ereignis aussehen.

> [!IMPORTANT]
> Das Anrufaufzeichnungsfeature befindet sich noch in der öffentlichen Vorschau

### <a name="microsoftcommunicationrecordingfilestatusupdated"></a>Microsoft.Communication.RecordingFileStatusUpdated

```json
[
 {
  "id": "7283825e-f8f1-4c61-a9ea-752c56890500",
  "topic": "/subscriptions/{subscription-id}/resourcegroups/}{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/recording/call/{call-id}/recordingId/{recording-id}",
  "data": {
    "recordingStorageInfo": {
      "recordingChunks": [
        {
          "documentId": "0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8",
          "index": 0,
          "endReason": "SessionEnded",
          "contentLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/video",
          "metadataLocation": "https://storage.asm.skype.com/v1/objects/0-eus-d12-801b3f3fc462fe8a01e6810cbff729b8/content/acsmetadata"
        }
      ]
    },
    "recordingStartTime": "2021-07-27T15:20:23.6089755Z",
    "recordingDurationMs": 6620,
    "sessionEndReason": "CallEnded"
  },
  "eventType": "Microsoft.Communication.RecordingFileStatusUpdated",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-07-27T15:20:34.2199328Z"
 }
]
```
