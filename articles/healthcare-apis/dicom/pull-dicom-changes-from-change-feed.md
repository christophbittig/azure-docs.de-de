---
title: Pullen von DICOM-Änderungen mithilfe des Änderungsfeeds
description: In dieser Anleitung wird erläutert, wie Sie DICOM-Änderungen mithilfe des DICOM-Änderungsfeeds für Azure Healthcare-APIs abrufen.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fa0237a57f5ebb8df0a69fa715a36d963ea0748f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345977"
---
# <a name="pull-dicom-changes-using-the-change-feed"></a>Pullen von DICOM-Änderungen mithilfe des Änderungsfeeds

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der DICOM-Änderungsfeed bietet Kunden die Möglichkeit, den Verlauf des DICOM-Diensts zu durchlaufen und auf die Erstellungs- und Löschereignisse im Dienst zu reagieren. In dieser Anleitung wird beschrieben, wie Sie den Änderungsfeed nutzen.

Der Zugriff auf den Änderungsfeed erfolgt über REST-APIs. Diese APIs sowie die Beispielverwendung des Änderungsfeeds sind in der [Übersicht über den DICOM-Änderungsfeed](dicom-change-feed-overview.md)dokumentiert. Die Version der REST-API sollte explizit in der Anforderungs-URL angegeben werden, wie in der Dokumentation zur [API-Versionierung für den DICOM-Dienst](api-versioning-dicom-service.md)angegeben.

## <a name="consume-change-feed"></a>Nutzen des Änderungsfeeds

Das folgende C#-Codebeispiel zeigt, wie Sie den Änderungsfeed mithilfe des DICOM-Clientpakets nutzen.

```csharp
const int limit = 10;
 
using HttpClient httpClient = new HttpClient { BaseAddress = new Uri("<URL>") };
using CancellationTokenSource tokenSource = new CancellationTokenSource();
 
int read;
List<ChangeFeedEntry> entries = new List<ChangeFeedEntry>();
DicomWebClient client = new DicomWebClient(httpClient);
do
{
    read = 0;
    DicomWebAsyncEnumerableResponse<ChangeFeedEntry> result = await client.GetChangeFeed(
        $"?offset={entries.Count}&limit={limit}&includeMetadata={true}",
        tokenSource.Token);
 
    await foreach (ChangeFeedEntry entry in result)
    {
        read++;
        entries.Add(entry);
    }
} while (read > 0);
```

Informationen zum Anzeigen und Zugreifen auf das **Codebeispiel ChangeFeedRetrieveService.cs** finden Sie unter [Nutzen des Änderungsfeeds.](https://github.com/microsoft/dicom-server/blob/main/converter/dicom-cast/src/Microsoft.Health.DicomCast.Core/Features/DicomWeb/Service/ChangeFeedRetrieveService.cs)

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung wird beschrieben, wie Sie den Änderungsfeed nutzen. Mit dem Änderungsfeed können Sie den Verlauf des DICOM-Diensts überwachen. Informationen zum DICOM-Dienst finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
